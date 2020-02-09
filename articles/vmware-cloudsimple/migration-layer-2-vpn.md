---
title: Azure VMware 솔루션 (AVS)-계층 2 네트워크 온-프레미스를 AVS 사설 클라우드로 스트레치
description: AVS 사설 클라우드의 NSX과 온-프레미스 독립 실행형 NSX Edge 클라이언트 간의 계층 2 VPN을 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d4e25074203ddcc016f54842f25f52017c6137f0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083219"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Layer 2 확장 네트워크로 워크로드 마이그레이션

이 가이드에서는 계층 2 VPN (L2VPN)을 사용 하 여 온-프레미스 환경에서 계층 2 네트워크를 AVS 사설 클라우드로 스트레치 하는 방법에 대해 설명 합니다. 이 솔루션을 사용 하면 워크 로드를 다시 IP 하지 않고도 온-프레미스 VMware 환경에서 실행 되는 워크 로드를 동일한 서브넷 주소 공간 내의 Azure에서 AVS 사설 클라우드로 마이그레이션할 수 있습니다.

계층 2 네트워크의 L2VPN 기반 확장은 온-프레미스 VMware 환경에서 NSX 기반 네트워크를 사용 하거나 사용 하지 않고 작동할 수 있습니다. 온-프레미스 워크 로드에 대 한 NSX 기반 네트워크가 없는 경우 독립 실행형 NSX Edge 서비스 게이트웨이를 사용할 수 있습니다.

> [!NOTE]
> 이 가이드에서는 온-프레미스 및 AVS 사설 클라우드 데이터 센터가 사이트 간 VPN을 통해 연결 되는 시나리오에 대해 설명 합니다.

## <a name="deployment-scenario"></a>배포 시나리오

L2VPN를 사용 하 여 온-프레미스 네트워크를 확장 하려면 L2VPN 서버 (대상 NSX-T Tier0 라우터)와 L2VPN client (원본 독립 실행형 클라이언트)를 구성 해야 합니다. 

이 배포 시나리오에서 AVS 사설 클라우드는 온-프레미스 관리 및 vMotion 서브넷이 AVS 사설 클라우드 관리 및 vMotion 서브넷과 통신할 수 있도록 하는 사이트 간 VPN 터널을 통해 온-프레미스 환경에 연결 됩니다. 이 정렬은 Cross vCenter vMotion (xVC-vMotion)에 필요 합니다. NSX-T Tier0 라우터는 AVS 사설 클라우드에 L2VPN 서버로 배포 됩니다.

독립 실행형 NSX Edge는 온-프레미스 환경에서 L2VPN 클라이언트로 배포 된 후 L2VPN 서버와 쌍으로 연결 됩니다. GRE 터널 엔드포인트은 각 쪽에 생성 되며, 온-프레미스 계층 2 네트워크를 AVS 사설 클라우드에 ' 스트레치 ' 하도록 구성 됩니다. 이 구성은 다음 그림에 나와 있습니다.

![배포 시나리오](media/l2vpn-deployment-scenario.png)

L2 VPN을 사용 하 여 마이그레이션하는 방법에 대 한 자세한 내용은 VMware 설명서의 [가상 개인 네트워크](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) 를 참조 하세요.

## <a name="prerequisites-for-deploying-the-solution"></a>솔루션 배포를 위한 필수 구성 요소

솔루션을 배포 하 고 구성 하기 전에 다음이 준비 되었는지 확인 합니다.

* 온-프레미스 vSphere 버전은 6.7 U1 + 또는 6.5 P03 +입니다.
* 온-프레미스 vSphere 라이선스는 엔터프라이즈급 (vSphere 분산 된 스위치의 경우) 수준에 있습니다.
* AVS 사설 클라우드에 확장 될 워크 로드 계층 2 네트워크를 확인 합니다.
* L2VPN 클라이언트 어플라이언스를 배포 하기 위해 온-프레미스 환경에서 계층 2 네트워크를 식별 합니다.
* [AVS 사설 클라우드가 이미 만들어졌습니다](create-private-cloud.md).
* 독립 실행형 NSX-Edge 어플라이언스의 버전은 AVS 사설 클라우드 환경에서 사용 되는 NSX Manager 버전 (NSX-T 2.3.0)과 호환 됩니다.
* 위조 된 전송 기능을 사용 하 여 온-프레미스 vCenter에서 트렁크 포트 그룹을 만들었습니다.
* 공용 IP 주소는 NSX-T 독립 실행형 클라이언트 업링크 IP 주소에 사용 하도록 예약 되었으며, 1:1 NAT는 두 주소 간 변환을 위해 준비 되었습니다.
* DNS 전달은 az.cloudsimple.io 도메인에 대 한 온-프레미스 DNS 서버에서 AVS 사설 클라우드 DNS 서버를 가리키도록 설정 됩니다.
* VMotion가 두 사이트에서 작동 하는 데 필요한 경우 RTT 대기 시간은 150 밀리초 보다 작거나 같습니다.

## <a name="limitations-and-considerations"></a>제한 사항 및 고려 사항

다음 표에는 지원 되는 vSphere 버전 및 네트워크 어댑터 유형이 나와 있습니다. 

| vSphere 버전 | 원본 vSwitch 유형 | 가상 NIC 드라이버 | 대상 vSwitch 유형 | 지원 여부 |
------------ | ------------- | ------------ | ------------- | ------------- 
| 모두 | DVS | 모두 | DVS | yes |
| vSphere 6.7 UI 이상, 6.5 P03 이상 | DVS | VMXNET3 | N-VDS | yes |
| vSphere 6.7 UI 이상, 6.5 P03 이상 | DVS | E1000 | N-VDS | [VWware 당 지원 되지 않음](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI 또는 6.5 P03, NSX 또는 NSX 아래에 있는 버전-T 2.2, 6.5 P03 이상 | 모두 | 모두 | N-VDS | [VWware 당 지원 되지 않음](https://kb.vmware.com/s/article/56991) |

VMware NSX-T 2.3 릴리스를 기반으로 합니다.

* L2VPN를 통해 온-프레미스로 확장 된 AVS 사설 클라우드의 논리 스위치를 동시에 라우팅할 수 없습니다. 스트레치 된 논리 스위치는 논리 라우터에 연결할 수 없습니다.
* L2VPN 및 경로 기반 IPSEC Vpn은 API 호출만 사용 하 여 구성할 수 있습니다.

자세한 내용은 VMware 설명서의 [가상 개인 네트워크](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) 를 참조 하세요.

### <a name="sample-l2-vpn-deployment-addressing"></a>샘플 L2 VPN 배포 주소 지정

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>독립 실행형 ESG (L2 VPN 클라이언트)가 배포 된 온-프레미스 네트워크

| **항목** | **값** |
|------------|-----------------|
| 네트워크 이름 | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| 독립 실행형 Edge 어플라이언스 IP 주소 | 10.250.0.111 |
| 독립 실행형 Edge 어플라이언스 NAT IP 주소 | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>스트레치 될 온-프레미스 네트워크

| **항목** | **값** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="avs-private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>NSX-T Tier0 Router 용 AVS 사설 클라우드 IP 스키마 (L2 VPN 서비스)

| **항목** | **값** |
|------------|-----------------|
| 루프백 인터페이스 | 192.168.254.254/32 |
| 터널 인터페이스 | 5.5.5.1/29 |
| 논리 스위치 (스트레치) | Stretch_LS |
| 루프백 인터페이스 (NAT IP 주소) | 104.40.21.81 |

### <a name="avs-private-cloud-network-to-be-mapped-to-the-stretched-network"></a>스트레치 된 네트워크에 매핑할 AVS 사설 클라우드 네트워크

| **항목** | **값** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>L2VPN에 필요한 논리 라우터 ID를 가져옵니다.

다음 단계는 IPsec 및 L2VPN 서비스에 대 한 Tier0 DR 논리 라우터 인스턴스의 논리 라우터 ID를 인출 하는 방법을 보여 줍니다. 논리 라우터 ID는 나중에 L2VPN을 구현할 때 필요 합니다.

1. NSX-T 관리자 https://*NSX* 에 로그인 하 고 **네트워킹** > **라우터** > **공급자-LR** > **개요**를 선택 합니다. 고가용성 **모드**의 경우 **활성-대기**를 선택 합니다. 이 작업을 수행 하면 Tier0 라우터가 현재 활성 상태인에 지 VM을 보여 주는 팝업 창이 열립니다.

    ![활성-대기 선택](media/l2vpn-fetch01.png)

2. **패브릭** > **노드** > **가장자리**를 선택 합니다. 이전 단계에서 확인 한 활성에 지 VM (Edge VM1)의 관리 IP 주소를 기록해 둡니다.

    ![참고 관리 IP](media/l2vpn-fetch02.png)

3. 에 지 VM의 관리 IP 주소에 대 한 SSH 세션을 엽니다. 사용자 이름 **관리자** 및 암호 **AVS 123!** 를 사용 하 여 ```get logical-router``` 명령을 실행 합니다.

    ![논리 라우터 출력 가져오기](media/l2vpn-fetch03.png)

4. ' DR-s p a n s i o n ' 항목이 표시 되지 않으면 다음 단계를 완료 합니다.

5. 오버레이 지원 논리 스위치 두 개를 만듭니다. 하나의 논리 스위치가 마이그레이션된 워크 로드가 있는 온-프레미스로 확장 됩니다. 다른 논리 스위치는 더미 스위치입니다. 자세한 지침은 VMware 설명서에서 [논리 스위치 만들기](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) 를 참조 하세요.

    ![논리 스위치 만들기](media/l2vpn-fetch04.png)

6. 온-프레미스 또는 AVS 사설 클라우드의 링크 로컬 IP 주소 또는 겹치지 않는 서브넷을 사용 하 여 Tier1 라우터에 더미 스위치를 연결 합니다. VMware 설명서의 [계층 1 논리 라우터에서 다운 링크 포트 추가](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) 를 참조 하세요.

    ![더미 스위치 연결](media/l2vpn-fetch05.png)

7. Edge VM의 SSH 세션에서 `get logical-router` 명령을 다시 실행 합니다. ' DR-s s l-l i n ' 논리 라우터의 UUID가 표시 됩니다. L2VPN를 구성할 때 필요한 UUID를 적어 둡니다.

    ![논리 라우터 출력 가져오기](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>L2VPN에 필요한 논리 스위치 ID를 가져옵니다.

1. [NSX](https://nsx-t-manager-ip-address)에 로그인 합니다.
2. **네트워킹** > **전환** > **스위치** >  **< \logical 스위치\>**  > **개요**를 선택 합니다.
3. L2VPN를 구성할 때 필요한 스트레치 논리 스위치의 UUID를 적어 둡니다.

    ![논리 라우터 출력 가져오기](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN에 대 한 라우팅 및 보안 고려 사항

NSX-T Tier0 라우터와 독립 실행형 NSX Edge 클라이언트 간에 IPsec 경로 기반 VPN을 설정 하려면 NSX-T Tier0 라우터의 루프백 인터페이스가 UDP 500/4500을 통해 온-프레미스 NSX 독립 실행형 클라이언트의 공용 IP 주소와 통신할 수 있어야 합니다.

### <a name="allow-udp-5004500-for-ipsec"></a>IPsec에 UDP 500/4500 허용

1. AVS 포털에서 NSX-T Tier0 루프백 인터페이스에 대 한 [공용 IP 주소를 만듭니다](public-ips.md) .

2. UDP 500/4500 인바운드 트래픽을 허용 하 고 NSX-T HostTransport 서브넷에 방화벽 테이블을 연결 하는 상태 저장 규칙을 사용 하 여 [방화벽 테이블을 만듭니다](firewall.md) .

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>루프백 인터페이스 IP를 언더레이 네트워크에 보급

1. 루프백 인터페이스 네트워크에 대 한 null 경로를 만듭니다. NSX-T Manager에 로그인 하 고 **네트워킹** > **라우팅** > **라우터** > **공급자-LR** > **라우팅** > **고정 경로**를 선택 합니다. **추가**를 클릭합니다. **네트워크**에 루프백 인터페이스 IP 주소를 입력 합니다. **다음 홉**의 경우 **추가**를 클릭 하 고, 다음 홉에 대해 ' n u l l '을 지정 하 고, 관리자 거리에 대해 기본값인 1을 유지 합니다.

    ![고정 경로 추가](media/l2vpn-routing-security01.png)

2. IP 접두사 목록을 만듭니다. NSX-T Manager에 로그인 하 고 **네트워킹** > **라우팅** > **라우터** > **공급자-LR** > **라우팅** > **IP 접두사 목록**을 선택 합니다. **추가**를 클릭합니다. 목록을 식별 하는 이름을 입력 합니다. **접두사**의 경우 **추가** 를 두 번 클릭 합니다. 첫 번째 줄에서 **네트워크** 에 대해 ' 0.0.0.0/0 '을 입력 하 고 **동작**에 대해 ' 거부 '를 입력 합니다. 두 번째 줄에서 **네트워크** 에 대해 **를 선택 하** 고 **작업**을 **허용** 합니다.
3. 두 BGP 환경 ()에 IP 접두사 목록을 연결 합니다. IP 접두사 목록을 BGP 환경에 연결 하면 기본 경로가 BGP에서 대상 스위치로 보급 되지 않습니다. 그러나 null 경로를 포함 하는 다른 모든 경로는 루프백 인터페이스 IP 주소를 대상 스위치에 보급 합니다.

    ![IP 접두사 목록 만들기](media/l2vpn-routing-security02.png)

4. NSX-T Manager에 로그인 하 고 **네트워킹** > **라우팅** > **라우터** > **공급자-LR** > **라우팅** > **BGP** > **인접**을 선택 합니다. 첫 번째 인접 항목을 선택 합니다.  > **주소 패밀리** **편집** 을 클릭 합니다. IPv4 패밀리의 경우 **아웃 필터** 열을 편집 하 고 만든 IP 접두사 목록을 선택 합니다. **저장**을 클릭합니다. 두 번째 인접 항목에 대해이 단계를 반복 합니다.

    IP 접두사 목록 1](media/l2vpn-routing-security03.png) 연결 ![IP 접두사 목록 2 ![연결 합니다](media/l2vpn-routing-security04.png)

5. BGP에 null 고정 경로를 다시 배포 합니다. 루프백 인터페이스 경로를 언더레이에 알리려면 null 정적 경로를 BGP에 재배포 해야 합니다. NSX-T Manager에 로그인 하 고 **네트워킹** > **라우팅** > **라우터** > **공급자-LR** > **라우팅** > **재배포** > **인접**라우터를 선택 합니다. **공급자-LR-Route_Redistribution** 을 선택 하 고 **편집**을 클릭 합니다. **정적** 확인란을 선택 하 고 **저장**을 클릭 합니다.

    ![BGP에 null 고정 경로 재배포](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>NSX-T Tier0 라우터에서 경로 기반 VPN 구성

다음 템플릿을 사용 하 여 NSX-T Tier0 라우터에서 경로 기반 VPN을 구성 하는 데 필요한 모든 세부 정보를 입력 합니다. 이후 POST 호출에는 각 POST 호출의 Uuid가 필요 합니다. L2VPN에 대 한 루프백 및 터널 인터페이스의 IP 주소는 고유 해야 하며 온-프레미스 또는 AVS 사설 클라우드 네트워크와 겹치지 않아야 합니다.

L2VPN에 사용 되는 루프백 및 터널 인터페이스에 대해 선택 된 IP 주소는 고유 해야 하며 온-프레미스 또는 AVS 사설 클라우드 네트워크와 겹치지 않아야 합니다. 루프백 인터페이스 네트워크는 항상/32 이어야 합니다.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

다음 모든 API 호출에 대해 IP 주소를 NSX-T 관리자 IP 주소로 바꿉니다. POSTMAN 클라이언트에서 또는 `curl` 명령을 사용 하 여 이러한 모든 API 호출을 실행할 수 있습니다.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>논리 라우터에서 IPSec VPN 서비스를 사용 하도록 설정

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>프로필 만들기: IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>프로필 만들기: LPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>프로필 만들기: 터널

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>로컬 엔드포인트 만들기

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>피어 엔드포인트 만들기

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>경로 기반 VPN 세션 만들기

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>NSX Tier0 router에서 L2VPN 구성

모든 사후 호출 후 다음 정보를 입력 합니다. 후속 사후 호출에는 Id가 필요 합니다.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>L2VPN 서비스 만들기

구성이 아직 완료 되지 않았기 때문에 다음 GET 명령의 출력은 비어 있습니다.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

다음 POST 명령의 경우 논리 라우터 ID는 앞에서 구한 Tier0 DR 논리 라우터의 UUID입니다.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>L2VPN 세션 만들기

다음 POST 명령의 경우 L2VPN 서비스 ID는 방금 가져온 ID 이며 IPsec VPN 세션 ID는 이전 섹션에서 얻은 ID입니다.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

이러한 호출은 GRE 터널 엔드포인트을 만듭니다. 상태를 확인 하려면 다음 명령을 실행 합니다.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>지정 된 터널 ID를 사용 하 여 논리 포트 만들기

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>NSX 쪽에서 L2VPN에 대 한 피어 코드 가져오기

NSX-T 엔드포인트의 피어 코드를 가져옵니다. 원격 엔드포인트을 구성 하는 경우 피어 코드가 필요 합니다. 이전 섹션에서 L2VPN < 세션 id >를 가져올 수 있습니다. 자세한 내용은 [NSX-T 2.3 API 가이드](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)를 참조 하세요.

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>NSX-T 독립 실행형 클라이언트 (온-프레미스) 배포

배포 하기 전에 온-프레미스 방화벽 규칙이 NSX-T T0 라우터 루프백 인터페이스에 대해 이전에 예약 된 AVS 공용 IP 주소에서 인바운드 및 아웃 바운드 UDP 500/4500 트래픽을 허용 하는지 확인 합니다. 

1. [독립 실행형 NSX Edge 클라이언트를 다운로드 합니다](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) . 을 (를) 다운로드 하 고 다운로드 한 번들에서 폴더로 파일을 추출 합니다.

    ![독립 실행형 NSX Edge 클라이언트 다운로드](media/l2vpn-deploy-client01.png)

2. 압축을 푼 모든 파일이 포함 된 폴더로 이동 합니다. 모든 vmdk (큰 어플라이언스 크기의 경우 NSX 및 NSX-l2t-client-large를 선택 하 고, 매우 큰 크기 어플라이언스 크기의 경우 NSX-l2t-NSX-l2t-client-Xlarge-)를 선택 합니다. **다음**을 클릭합니다.

    템플릿](media/l2vpn-deploy-client02.png) ![선택 하 ![템플릿을 선택](media/l2vpn-deploy-client03.png)

3. NSX-T 독립 실행형 클라이언트의 이름을 입력 하 고 **다음**을 클릭 합니다.

    ![템플릿 이름 입력](media/l2vpn-deploy-client04.png)

4. 필요에 따라 **다음** 을 클릭 하 여 데이터 저장소 설정에 도달 합니다. NSX-T 독립 실행형 클라이언트에 적합 한 데이터 저장소를 선택 하 고 **다음**을 클릭 합니다.

    ![데이터 저장소 선택](media/l2vpn-deploy-client06.png)

5. NSX-T 독립 실행형 클라이언트의 트렁크 (트렁크 PG), 공용 (업링크 PG) 및 HA 인터페이스 (업링크 PG)에 대 한 올바른 포트 그룹을 선택 합니다. **다음**을 클릭합니다.

    ![포트 그룹 선택](media/l2vpn-deploy-client07.png)

6. **템플릿 사용자 지정** 화면에서 다음 정보를 입력 하 고 **다음**을 클릭 합니다.

    L2T를 확장 합니다.

    * **피어 주소**입니다. NSX-T Tier0 루프백 인터페이스에 대해 Azure AVS 포털에서 예약 된 IP 주소를 입력 합니다.
    * **피어 코드**. L2VPN 서버 배포의 마지막 단계에서 얻은 피어 코드를 붙여넣습니다.
    * **하위 인터페이스 VLAN (터널 ID)** . 확장할 VLAN ID를 입력 합니다. 괄호 ()에 이전에 구성 된 터널 ID를 입력 합니다.

    업링크 인터페이스 확장:

    * **DNS IP 주소**입니다. 온-프레미스 DNS IP 주소를 입력 합니다.
    * **기본 게이트웨이입니다**. 이 클라이언트에 대 한 기본 게이트웨이의 역할을 하는 VLAN의 기본 게이트웨이를 입력 합니다.
    * **IP 주소**입니다. 독립 실행형 클라이언트의 업링크 IP 주소를 입력 합니다.
    * **접두사 길이**입니다. 업링크 VLAN/서브넷의 접두사 길이를 입력 합니다.
    * **CLI admin/enable/Root 사용자 암호**입니다. 관리자/enable/root 계정에 대 한 암호를 설정 합니다.

      템플릿 ![](media/l2vpn-deploy-client08.png)
      사용자 지정 ![템플릿 사용자 지정-더](media/l2vpn-deploy-client09.png)

7. 설정을 검토 하 고 **마침**을 클릭 합니다.

    ![구성 완료](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>온-프레미스 싱크 포트 구성

VPN 사이트 중 하나에 NSX가 배포 되지 않은 경우 해당 사이트에 독립 실행형 NSX Edge를 배포 하 여 L2 VPN을 구성할 수 있습니다. 독립 실행형 NSX Edge는 NSX에서 관리 하지 않는 호스트의 파일을 사용 하 여 배포 됩니다. 그러면 L2 VPN 클라이언트 역할을 하는 NSX Edge 서비스 게이트웨이 어플라이언스를 배포 합니다.

독립 실행형에 지 트렁크 vNIC가 vSphere 분산 된 스위치에 연결 된 경우 L2 VPN 기능에 대해 무차별 모드 또는 싱크 포트가 필요 합니다. 무차별 모드를 사용 하면 중복 ping 및 중복 응답이 발생할 수 있습니다. 이러한 이유로 L2 VPN 독립 실행형 NSX Edge 구성에서 싱크 포트 모드를 사용 합니다. VMware 설명서의 [싱크 포트 구성](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) 을 참조 하세요.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN 및 L2VPN 확인

다음 명령을 사용 하 여 독립 실행형 NSX-T Edge에서 IPsec 및 L2VPN 세션을 확인 합니다.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

다음 명령을 사용 하 여 NSX Tier0 라우터로부터 IPsec 및 L2VPN 세션을 확인 합니다.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

다음 명령을 사용 하 여 NSX 독립 실행형 클라이언트 VM이 온-프레미스 환경에 있는 ESXi 호스트의 싱크 포트를 확인 합니다.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
