---
title: Azure Express 경로 개인 피어 링에 대 한 백업으로 S2S VPN 사용 | Microsoft Docs
description: 이 페이지에서는 S2S VPN을 사용 하 여 Azure Express 경로 개인 피어 링을 백업 하기 위한 아키텍처 권장 사항을 제공 합니다.
services: networking
author: rambk
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: 68596b881ef1b62187bdb7194b364c9477b4e04d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244774"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Express 경로 개인 피어 링에 대 한 백업으로 S2S VPN 사용

[Express 경로 개인 피어 링을 사용 하 여 재해 복구를 위한 디자인][DR-PP]이라는 문서에서는 express 경로 개인 피어 링 연결에 대 한 백업 연결 솔루션에 대 한 필요성 및 용도에 대해 지역 중복 express 경로 회로를 사용 하는 방법에 대해 설명 했습니다. 이 문서에서는 사이트 간 (S2S) VPN을 Express 경로 개인 피어 링에 대 한 백업으로 활용 하 고 유지 관리 하는 방법을 살펴보겠습니다. 

지역 중복 Express 경로 회로와는 달리, 활성-수동 모드 에서만 Express 경로-VPN 재해 복구 조합을 사용할 수 있습니다. 수동 모드에서의 백업 네트워크 연결을 사용 하는 경우의 주요 문제는 수동 연결이 주 연결과 함께 실패 하는 경우가 많습니다. 수동 연결 오류가 발생 하는 일반적인 이유는 활성 유지 관리가 부족 하기 때문입니다. 따라서이 문서에서는 Express 경로 개인 피어 링을 백업 하는 S2S VPN 연결을 확인 하 고 적극적으로 유지 관리 하는 방법을 집중적으로 살펴보겠습니다.

>[!NOTE] 
>지정 된 경로가 Express 경로와 VPN을 통해 보급 되 면 Azure는 Express 경로를 통한 라우팅을 선호 합니다.  
>

이 문서에서는 Azure 관점과 고객 측 네트워크에 지 관점의 연결을 확인 하는 방법을 알아보겠습니다. 한쪽 끝에서 유효성을 검사 하는 기능은 Microsoft 네트워크 엔터티를 사용 하는 고객 측 네트워크 장치를 관리 하는지 여부에 관계 없이 도움이 됩니다. 

## <a name="example-topology"></a>예제 토폴로지

설치 프로그램에는 Express 경로 회로와 S2S VPN 연결을 통해 Azure 허브 VNet에 연결 된 온-프레미스 네트워크가 있습니다. Azure 허브 VNet은 아래 다이어그램에 표시 된 것 처럼 스포크 VNet에 피어 링 됩니다.

![1][1]

설정에서 Express 경로 회로는 온-프레미스의 "고객에 지" (CE) 라우터 쌍에서 종료 됩니다. 온-프레미스 LAN은 지도자-종동체 모드로 작동 하는 한 쌍의 방화벽을 통해 CE 라우터에 연결 됩니다. S2S VPN은 방화벽에서 직접 종료 됩니다.

다음 표에는 토폴로지의 키 IP 접두사가 나와 있습니다.

| **엔터티** | **Prefix** |
| --- | --- |
| 온-프레미스 LAN | 10.1.11.0/25 |
| Azure 허브 VNet | 10.17.11.0/25 |
| Azure 스포크 VNet | 10.17.11.128/26 |
| 온-프레미스 테스트 서버 | 10.1.11.10 |
| 스포크 VNet 테스트 VM | 10.17.11.132 |
| Express 경로 기본 연결 p2p 서브넷 | 192.168.11.16/30 |
| Express 경로 보조 연결 p2p 서브넷 | 192.168.11.20/30 |
| VPN gateway 기본 BGP 피어 IP | 10.17.11.76 |
| VPN gateway 보조 BGP 피어 IP | 10.17.11.77 |
| 온-프레미스 방화벽 VPN BGP 피어 IP | 192.168.11.88 |
| 방화벽 IP를 향하는 기본 CE 라우터 i/f | 192.168.11.0/31 |
| 기본 CE 라우터 IP에 대 한 방화벽 i/f | 192.168.11.1/31 |
| 방화벽 IP에 대 한 보조 CE 라우터 i/f | 192.168.11.2/31 |
| 보조 CE 라우터 IP에 대 한 방화벽 i/f | 192.168.11.3/31 |


다음 표에는 토폴로지의 ASNs가 나와 있습니다.

| **자치 시스템** | **ASN** |
| --- | --- |
| 온-프레미스 | 65020 |
| Microsoft Enterprise Edge | 12076 |
| ExR (Virtual Network GW) | 65515 |
| Virtual Network GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Asymmetricity 없이 고가용성

### <a name="configuring-for-high-availability"></a>고가용성을 위한 구성

[Express 경로 및 사이트 간 공존 연결 구성][Conf-CoExist] 에서 공존 하는 express 경로 회로 및 S2S VPN 연결을 구성 하는 방법을 설명 합니다. Express 경로를 사용 하 여 고가용성을 [위한 설계][HA]에서 설명한 대로 express 경로 고가용성을 개선 하기 위해 설치 프로그램에서 끝점까지 네트워크 중복성 (단일 실패 지점 방지)을 유지 합니다. 또한 Express 경로 회로의 기본 및 보조 연결은 모두 두 연결을 통해 동일한 방식으로 온-프레미스 접두사를 보급 하 여 활성-활성 모드에서 작동 하도록 구성 됩니다. 

Express 경로 회로의 기본 연결을 통해 기본 CE 라우터에 대 한 온-프레미스 경로 보급 알림은 아래 (Junos commands)에 표시 됩니다.

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Express 경로 회로의 보조 연결을 통해 보조 CE 라우터의 온-프레미스 경로 보급 알림은 아래 (Junos commands)에 표시 됩니다.

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

백업 연결의 고가용성을 향상 시키기 위해 S2S VPN도 활성-활성 모드에서 구성 됩니다. Azure VPN gateway 구성은 다음과 같습니다. 참고 VPN 구성의 일부로 게이트웨이의 BGP 피어 IP 주소 (10.17.11.76 및 10.17.11.77)도 나열 됩니다.

![2][2]

온-프레미스 경로는 방화벽에서 VPN 게이트웨이의 기본 및 보조 BGP 피어로 보급 됩니다. 경로 알림은 아래에 표시 됩니다 (Junos).

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>활성-활성 모드에서 S2S VPN을 구성 하는 것은 재해 복구 백업 네트워크 연결에 대 한 고가용성을 제공할 뿐만 아니라 백업 연결에 대 한 높은 처리량도 제공 합니다. 즉, 활성-활성 모드에서 S2S VPN을 구성 하는 것은 여러 기본 터널을 강제로 만들도록 하는 것이 좋습니다.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>대칭 트래픽 흐름에 대 한 구성

Express 경로 및 S2S VPN을 통해 지정 된 온-프레미스 경로를 보급 하는 경우 Azure는 Express 경로 경로를 선호 합니다. 공존 하는 Express 경로를 통해 Azure에서 S2S VPN 경로를 선호 하도록 하려면 VPN 연결을 통해 더 구체적인 경로 (더 큰 서브넷 마스크를 사용 하는 더 긴 접두사)를 보급 해야 합니다. 여기서의 목표는 VPN 연결을 백업만 사용 하는 것입니다. 따라서 Azure의 기본 경로 선택 동작은 현재 목적과 함께 사용 됩니다. 

온-프레미스에서 Azure로 향하는 트래픽이 S2S VPN을 통해 Express 경로 경로를 선호 하는지 확인 하는 것은 책임이 있습니다. 온-프레미스 설치에서 CE 라우터 및 방화벽의 기본 로컬 기본 설정은 100입니다. 따라서 Express 경로 개인 100 피어 링을 통해 수신 되는 경로에 대 한 로컬 기본 설정 (예 150)을 구성 하 여 Azure로 향하는 트래픽이 안정 된 상태에서 Express 경로 회로를 선호 하도록 만들 수 있습니다.

Express 경로 회로의 기본 연결을 종료 하는 기본 CE 라우터의 BGP 구성은 아래와 같습니다. IBGP 세션을 통해 보급 된 경로의 로컬 기본 설정 값은 150로 구성 됩니다. 마찬가지로 Express 경로 회로의 보조 연결을 종료 하는 보조 CE 라우터의 로컬 기본 설정도 150으로 구성 되어 있는지 확인 해야 합니다.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

온-프레미스 방화벽의 라우팅 테이블은 Azure를 대상으로 하는 온-프레미스 트래픽에 대해 기본 경로가 안정 된 상태에서 Express 경로를 초과 하는 것을 확인 합니다 (아래 참조).

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

위의 경로 테이블에서 허브 및 스포크 VNet 경로 (-10.17.11.0/25 and 10.17.11.128/26--)에 대해 VPN 연결 보다 Express 경로 회로가 선호 됨을 확인할 수 있습니다. 192.168.11.0 및 192.168.11.2는 CE 라우터를 향하는 방화벽 인터페이스의 Ip입니다.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>S2S VPN을 통한 경로 교환의 유효성 검사

이 문서의 앞부분에서 VPN 게이트웨이의 기본 및 보조 BGP 피어로 방화벽의 온-프레미스 경로 보급 알림을 확인 했습니다. 또한 VPN 게이트웨이의 기본 및 보조 BGP 피어에서 방화벽을 통해 수신 되는 Azure 경로를 확인 하겠습니다.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

마찬가지로 Azure VPN gateway에서 받은 온-프레미스 네트워크 경로 접두사를 확인 합니다. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

위에서 설명한 것 처럼 VPN gateway에는 VPN 게이트웨이의 기본 BGP 피어와 보조 BGP 피어를 모두 수신 하는 경로가 있습니다. 기본 및 보조 Express 경로 연결을 통해 수신 된 경로에 대 한 가시성도 있습니다 (AS-path 앞에 12076 앞에 있음). VPN 연결을 통해 수신 된 경로를 확인 하려면 연결의 온-프레미스 BGP 피어 IP를 알고 있어야 합니다. 고려 중인 설치의 경우 192.168.11.88이 고 여기에서 받은 경로를 볼 수 있습니다.

다음으로, Azure VPN 게이트웨이에서 온-프레미스 방화벽 BGP 피어 (192.168.11.88)에 알린 경로를 확인 합니다.

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

경로 교환에 대 한 실패는 연결 실패를 표시 합니다. [문제 해결: Azure 사이트 간 vpn 연결을 연결 하 고 작동을 중지][VPN Troubleshoot] 하 여 vpn 연결 문제 해결에 도움을 줍니다.

## <a name="testing-failover"></a>장애 조치 테스트

이제 VPN 연결 (제어 평면)을 통해 성공적으로 경로 교환을 확인 했으므로 Express 경로 연결에서 VPN 연결로 트래픽 (데이터 평면)을 전환 하도록 설정 되었습니다. 

>[!NOTE] 
>프로덕션 환경에서 장애 조치 (failover) 테스트는 서비스 중단 일 수 있으므로 예약 된 네트워크 유지 관리 작업을 수행 해야 합니다.
>

트래픽 스위치를 수행 하기 전에 설정에서 현재 경로를 온-프레미스 테스트 서버에서 스포크 VNet의 테스트 VM으로 라우팅합니다.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

설치 프로그램의 기본 및 보조 Express 경로 지점 간 연결 서브넷은 각각 192.168.11.16/30 및 192.168.11.20/30입니다. 위의 추적 경로에서 3 단계에서는 기본 MSEE의 인터페이스 IP 인 192.168.11.18가 적중 되는 것을 확인할 수 있습니다. MSEE 인터페이스가 있으면 현재 경로가 Express 경로를 초과 하 고 있음을 확인 합니다.

[Express 경로 회로 피어][RST]링에서 보고 된 것 처럼 다음 powershell 명령을 사용 하 여 express 경로 회로의 기본 피어 링과 보조 피어 링을 모두 사용 하지 않도록 설정 합니다.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

장애 조치 (failover) 전환 시간은 BGP 수렴 시간에 따라 달라 집니다. 이 설정에서 장애 조치 (failover) 스위치는 몇 초 (10 개 미만)를 사용 합니다. 스위치 뒤에 경로 추적를 반복 하면 다음 경로가 표시 됩니다.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

경로 추적 결과는 S2S VPN을 통한 백업 연결이 활성 상태 인지 확인 하 고, 기본 및 보조 Express 경로 연결이 모두 실패 하면 서비스 연속성을 제공할 수 있습니다. 장애 조치 (failover) 테스트를 완료 하려면 다음 명령 집합을 사용 하 여 Express 경로 연결을 다시 사용 하도록 설정 하 고 트래픽 흐름을 정규화 하겠습니다.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

트래픽이 Express 경로에 다시 전환 되었는지 확인 하려면 경로 추적를 반복 하 여 Express 경로 개인 피어 링을 통과 하는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

Express 경로는 Microsoft 네트워크 내에서 단일 실패 지점이 없는 고가용성을 위해 설계 되었습니다. Express 경로 회로는 단일 지리적 지역 및 서비스 공급자에만 국한 됩니다. S2S VPN은 Express 경로 회로에 대 한 좋은 재해 복구 수동 백업 솔루션 일 수 있습니다. 신뢰할 수 있는 수동 백업 연결 솔루션의 경우 수동 구성 및 정기 정기 유지 관리에서 연결이 중요 합니다. VPN 구성이 유효 하지 않게 되 고 주기적으로 (분기 마다) 유지 관리 기간 동안이 문서에 설명 된 유효성 검사 및 장애 조치 (failover) 테스트 단계를 반복 하는 것이 중요 합니다.

VPN gateway 메트릭에 따라 모니터링 및 경고를 사용 하도록 설정 하려면 [VPN Gateway 메트릭에 대 한 경고 설정][VPN-alerts]을 참조 하세요.

Express 경로 오류 다음에 BGP 수렴을 신속 하 게 수행 하려면 Express 경로를 [통해 BFD를 구성][BFD]합니다.

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "고려 중인 토폴로지"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 구성"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



