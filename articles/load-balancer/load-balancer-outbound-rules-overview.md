---
title: Azure Load Balancer의 아웃바운드 규칙
titlesuffix: Azure Load Balancer
description: 아웃바운드 규칙을 사용하여 아웃바운드 네트워크 주소 변환을 정의합니다.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 52fafa7e9dd46b6c78af3776797bae48b22ea8df
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736667"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer 아웃바운드 규칙

Azure Load Balancer는 가상 네트워크에서 인바운드 연결 외에도 아웃바운드 연결을 제공합니다.  아웃바운드 규칙을 사용하면 공용 [표준 Load Balancer](load-balancer-standard-overview.md)의 아웃바운드 네트워크 주소 변환을 쉽게 구성할 수 있습니다.  아웃바운드 연결을 완전하게 선언적으로 제어하여 이 기능을 특정 요구에 맞게 확장하고 튜닝할 수 있습니다.

![Load Balancer 아웃바운드 규칙](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

아웃바운드 규칙을 사용하면 Load Balancer를 통해 다음을 수행할 수 있습니다. 
- 아웃바운드 NAT를 처음부터 정의합니다.
- 기존 아웃바운드 NAT의 동작을 확장하고 튜닝합니다. 

아웃바운드 규칙을 통해 제어할 수 있는 항목은 다음과 같습니다.
- 특정 공용 IP 주소로 변환되어야 하는 가상 머신 
- [아웃바운드 SNAT 포트](load-balancer-outbound-connections.md#snat)를 할당해야 하는 방법
- 아웃바운드 변환을 제공하는 프로토콜
- 아웃바운드 연결 유휴 시간 제한에 사용할 기간(4-120분)
- 유휴 시간 제한 시 TCP 재설정을 보낼지 여부(공개 미리 보기에서) 

아웃바운드 규칙은 [아웃바운드 연결](load-balancer-outbound-connections.md) 문서에서 설명한 [시나리오 2](load-balancer-outbound-connections.md#lb)를 확장하며 시나리오 우선 순위는 그대로 유지됩니다.

## <a name="outbound-rule"></a>아웃바운드 규칙

모든 Load Balancer 규칙과 마찬가지로, 아웃바운드 규칙은 부하 분산 및 인바운드 NAT 규칙과 동일한 친숙한 구문을 따릅니다.

**프런트 엔드** + **매개 변수** + **백 엔드 풀**

아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_에 대한 아웃바운드 NAT를 _프런트 엔드_로 변환하도록 구성합니다.  그리고 _매개 변수_는 아웃바운드 NAT 알고리즘에 대해 세분화된 제어를 추가로 제공합니다.

"2018-07-01" API 버전은 다음과 같이 구조화된 아웃바운드 규칙 정의를 허용합니다.

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>효과적인 아웃바운드 NAT 구성은 모든 아웃바운드 규칙과 부하 분산 규칙을 합성한 것입니다. 아웃바운드 규칙은 부하 분산 규칙에 따라 증분합니다. [부하 분산 규칙에 대해 아웃바운드 NAT 사용 안 함](#disablesnat)을 검토하여 VM에 여러 규칙이 적용될 때 효과적인 아웃바운드 NAT 변환을 관리합니다. 부하 분산 규칙과 동일한 공용 IP 주소를 사용하는 아웃바운드 규칙을 정의하는 경우 [아웃바운드 SNAT를 사용하지 않도록 설정](#disablesnat)해야 합니다.

### <a name="scale"></a> 여러 IP 주소를 사용하여 아웃바운드 NAT 확장

하나의 아웃바운드 규칙은 하나의 공용 IP 주소에만 사용할 수 있지만, 아웃바운드 규칙은 아웃바운드 NAT를 확장하기 위한 구성에 대한 부담을 덜어줍니다. 여러 IP 주소를 사용하여 대규모 시나리오를 계획하고, 아웃바운드 규칙을 사용하여 [SNAT 고갈](load-balancer-outbound-connections.md#snatexhaust) 가능성이 높은 경향의 패턴을 완화할 수 있습니다.  

프런트 엔드에서 제공하는 추가 IP 주소 각각은 SNAT 포트로 사용할 Load Balancer에 대해 51,200개의 사용 후 삭제 포트를 제공합니다. 부하 분산 또는 인바운드 NAT 규칙에는 단일 프런트 엔드가 있지만, 아웃바운드 규칙에는 프런트 엔드 개념이 확장되고 규칙마다 여러 개의 프런트 엔드가 허용됩니다.  규칙당 여러 개의 프런트 엔드를 사용하면 사용 가능한 SNAT 포트의 수에 각 공용 IP 주소를 곱하여 큰 시나리오를 지원할 수 있습니다.

또한 아웃바운드 규칙에 [공용 IP 접두사](https://aka.ms/lbpublicipprefix)를 직접 사용할 수 있습니다.  공용 IP 접두사를 사용하면 Azure 배포에서 시작되는 흐름을 더 쉽게 크기 조정하고 허용 목록에 추가를 간소화할 수 있습니다. 공용 IP 주소 접두사를 직접 참조하도록 Load Balancer 리소스 내에 프런트 엔드 IP 구성을 구성할 수 있습니다.  이렇게 하면 Load Balancer에서 공용 IP 접두사를 독점적으로 제어하고, 아웃바운드 연결에 대한 공용 IP 접두사에 포함된 모든 공용 IP 주소가 아웃바운드 규칙에 자동으로 사용될 수 있습니다.  공용 IP 접두사 범위 내의 각 IP 주소는 Load Balancer에서 SNAT 포트로 사용할 IP 주소당 51,200개의 사용 후 삭제 포트를 제공합니다.   

아웃바운드 규칙에서 공용 IP 접두사를 완전히 제어해야 하므로, 이 옵션을 사용하는 경우 공용 IP 접두사에서 만든 개별 공용 IP 주소 리소스는 사용할 수 없습니다.  더 세분화된 제어가 필요한 경우, 공용 IP 접두사에서 개별 공용 IP 주소 리소스를 만들고 여러 공용 IP 주소를 개별적으로 아웃바운드 규칙의 프런트 엔드에 할당할 수 있습니다.

### <a name="snatports"></a> SNAT 포트 할당 튜닝

아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝하고 자동 SNAT 포트 할당보다 더 많거나 적게 할당할 수 있습니다.

VM당 10,000개의 SNAT 포트를 할당하려면 다음 매개 변수를 사용합니다(NIC IP 구성).
 

          "allocatedOutboundPorts": 10000

아웃바운드 규칙의 모든 프런트 엔드에 있는 각 공용 IP 주소는 SNAT 포트로 사용할 최대 51,200개의 사용 후 삭제 포트를 제공합니다.  Load Balancer는 SNAT 포트를 8의 배수로 할당합니다. 8로 나눌 수 없는 값을 제공하면 구성 작업이 거부됩니다.  공용 IP 주소의 수를 기준으로 사용 가능한 것보다 더 많은 SNAT 포트를 할당하려고 하면 구성 작업이 거부됩니다.  예를 들어, VM 및 7 Vm 당 10,000 포트를 할당 하는 경우 풀에서 백 엔드를 단일 공용 IP 주소를 공유, 구성은 거부 (7 x 10, 000 SNAT 포트 > 51,200 SNAT 포트).  아웃바운드 규칙의 프런트 엔드에 더 많은 공용 IP 주소를 추가하여 시나리오를 사용하도록 설정할 수 있습니다.

포트 수로 0을 지정하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)으로 되돌릴 수 있습니다.

### <a name="idletimeout"></a> 아웃바운드 흐름 유휴 시간 제한 제어

아웃바운드 규칙은 아웃바운드 흐름 유휴 시간 제한을 제어하고 이를 애플리케이션의 요구와 일치시키는 구성 매개 변수를 제공합니다.  아웃바운드 유휴 시간 제한은 기본적으로 4분입니다.  매개 변수는 이 특정 규칙과 일치하는 흐름에 대한 유휴 시간 제한을 분 단위로 지정하기 위해 4-120의 값을 허용합니다.

아웃바운드 유휴 시간 제한을 1시간으로 설정하려면 다음 매개 변수를 사용합니다.

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> 유휴 시간 제한 시 TCP 재설정 사용(미리 보기)

Load Balancer의 기본 동작은 아웃바운드 유휴 시간 제한에 도달하는 경우 흐름을 자동으로 삭제하는 것입니다.  enableTCPReset 매개 변수를 사용하면 예측 가능한 애플리케이션 동작을 사용하도록 설정하고 아웃바운드 유휴 시간 제한 시간을 초과할 때 양방향 TCP 재설정(TCP RST)을 보낼지 여부를 제어할 수 있습니다. 

아웃바운드 규칙에서 TCP 재설정을 사용하도록 설정하려면 다음 매개 변수를 사용합니다.

           "enableTcpReset": true

지역 가용성을 포함한 자세한 내용은 [유휴 시간 제한 시 TCP 재설정(미리 보기)](https://aka.ms/lbtcpreset)을 검토하세요.

### <a name="proto"></a> 단일 규칙으로 TCP 및 UDP 전송 프로토콜을 모두 지원

아웃바운드 규칙의 전송 프로토콜에 "모두"를 사용할 수 있지만, 필요한 경우 아웃바운드 규칙을 특정 전송 프로토콜에도 적용할 수 있습니다.

다음 매개 변수를 사용하여 프로토콜을 TCP 및 UDP로 설정합니다.

          "protocol": "All"

### <a name="disablesnat"></a> 부하 분산 규칙에 대해 아웃바운드 NAT 사용 안 함

앞에서 설명한 대로 부하 분산 규칙은 자동 아웃바운드 NAT 프로그래밍을 제공합니다. 그러나 일부 시나리오에서는 동작을 제어하거나 구체화할 수 있도록 부하 분산 규칙을 통해 자동 아웃바운드 NAT 프로그래밍을 사용하지 않도록 설정하거나 이렇게 설정해야 합니다.  아웃바운드 규칙에는 자동 아웃바운드 NAT 프로그래밍을 중지해야 하는 시나리오가 있습니다.

이 매개 변수는 다음 두 가지 방법으로 사용할 수 있습니다.
- 아웃바운드 NAT에 인바운드 IP 주소를 사용하지 않도록 선택적으로 억제합니다.  아웃바운드 규칙은 부하 분산 규칙에 따라 증분하며, 이 매개 변수 집합을 사용하여 제어됩니다.
  
- 인바운드 및 아웃바운드에 동시에 사용되는 IP 주소의 아웃바운드 NAT 매개 변수를 튜닝합니다.  아웃바운드 규칙을 제어하려면 자동 아웃바운드 NAT 프로그래밍을 사용하지 않도록 설정해야 합니다.  예를 들어 인바운드에도 사용되는 주소의 SNAT 포트 할당을 변경하려면 이 매개 변수를 true로 설정해야 합니다.  아웃바운드 규칙을 사용하여 인바운드에도 사용되는 IP 주소의 매개 변수를 재정의하려고 하지만 부하 분산 규칙의 아웃바운드 NAT 프로그래밍을 해제하지 않은 경우 아웃바운드 규칙을 구성하는 작업이 실패합니다.

>[!IMPORTANT]
> 이 매개 변수를 true로 설정하고 아웃바운드 연결을 정의하는 아웃바운드 규칙(또는 [인스턴스 수준 공용 IP 시나리오](load-balancer-outbound-connections.md#ilpip))이 없는 경우 가상 머신에는 아웃바운드 연결이 없습니다.  VM 또는 애플리케이션의 일부 작업은 아웃바운드 연결을 사용할 수 있는지에 따라 달라질 수 있습니다. 시나리오의 종속성을 이해하고 이러한 변경의 영향을 고려해야 합니다.

다음 구성 매개 변수를 사용하여 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정할 수 있습니다.

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

disableOutboundSNAT 매개 변수의 기본값은 false입니다. 즉 부하 분산 규칙에서 자동 아웃바운드 NAT를 부하 분산 규칙 구성의 미러 이미지로 **제공합니다**.  

부하 분산 규칙에서 disableOutboundSnat를 true로 설정하는 경우 부하 분산 규칙에서 그렇지 않으면 자동 아웃바운드 NAT 프로그래밍에 대한 제어를 해제합니다.  부하 분산 규칙의 결과로 아웃바운드 SNAT가 비활성화됩니다.

### <a name="reuse-existing-or-define-new-backend-pools"></a>기존 백 엔드 풀 재사용 또는 새로 정의

아웃바운드 규칙은 규칙을 적용해야 하는 VM 그룹을 정의하기 위한 새로운 개념을 도입하지 않습니다.  대신, 부하 분산 규칙에도 사용되는 백 엔드 풀의 개념을 다시 사용합니다. 이 개념을 사용하여 기존 백 엔드 풀 정의를 다시 사용하거나 아웃바운드 규칙에 맞는 개념을 특별히 만들어 구성을 간소화할 수 있습니다.

## <a name="scenarios"></a>시나리오

### <a name="groom"></a> 특정 공용 IP 주소 집합에 대한 아웃바운드 연결 영구 제거

아웃바운드 규칙을 사용하여 특정 공용 IP 주소 집합에서 시작된 것으로 보이는 아웃바운드 연결을 영구 제거하여 허용 목록 시나리오를 쉽게 만들 수 있습니다.  이 원본 공용 IP 주소는 부하 분산 규칙에서 사용하는 것과 같거나 부하 분산 규칙에서 사용하는 것과 다른 공용 IP 주소 집합일 수 있습니다.  

1. [공용 IP 접두사](https://aka.ms/lbpublicipprefix)(또는 공용 IP 접두사의 공용 IP 주소)를 만듭니다.
2. 공용 Standard Load Balancer 만들기
3. 사용하려는 공용 IP 접두사(또는 공용 IP 주소)를 참조하는 프런트 엔드를 만듭니다.
4. 백 엔드 풀을 다시 사용하거나 새로 만들고, 공용 Load Balancer의 백 엔드 풀에 VM을 배치합니다.
5. 프런트 엔드를 사용하여 이러한 VM의 아웃바운드 NAT를 프로그래밍하도록 공용 Load Balancer에 아웃바운드 규칙을 구성합니다
   
아웃바운드에 부하 분산 규칙을 사용하지 않으려면 부하 분산 규칙에서 [아웃바운드 SNAT를 사용하지 않도록 설정](#disablesnat)해야 합니다.

### <a name="modifysnat"></a> SNAT 포트 할당 수정

아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝할 수 있습니다.

예를 들어 아웃바운드 NAT에 대해 하나의 공용 IP 주소를 공유하는 두 개의 가상 머신이 있는 경우 SNAT가 고갈되었으면 기본 1024 포트에서 할당되는 SNAT 포트 수를 늘리는 것이 좋습니다. 각 공용 IP 주소는 최대 51,200개의 사용 후 삭제 포트를 제공할 수 있습니다.  단일 공용 IP 주소 프런트 엔드를 사용하여 아웃바운드 규칙을 구성하면 총 51,200개의 SNAT 포트를 백 엔드 풀의 VM에 배포할 수 있습니다.  두 VM의 경우 아웃바운드 규칙을 사용하여 최대 25,600개의 SNAT 포트를 할당할 수 있습니다(2 x 25,600 = 51,200).

[아웃바운드 연결](load-balancer-outbound-connections.md) 및 [SNAT](load-balancer-outbound-connections.md#snat) 포트가 할당되고 사용되는 방법에 대한 자세한 내용을 검토하세요.

### <a name="outboundonly"></a> 아웃바운드만 사용

공용 표준 Load Balancer를 사용하여 VM 그룹에 아웃바운드 NAT를 제공할 수 있습니다. 이 시나리오에서는 추가 규칙 없이 아웃바운드 규칙 자체만 사용할 수 있습니다.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>VM 전용 아웃바운드 NAT(인바운드 없음)

공용 표준 Load Balancer를 정의하고, 백 엔드 풀에 VM을 배치하고, 아웃바운드 NAT를 프로그래밍하고 특정 공용 IP 주소에서 시작되는 아웃바운드 연결을 영구 제거하도록 아웃바운드 규칙을 구성합니다. 또한 공용 IP 접두사를 사용하여 아웃바운드 연결의 원본을 허용 목록에 추가하도록 간소화할 수 있습니다.

1. 공용 표준 Load Balancer를 만듭니다.
2. 백 엔드 풀을 만들고 공용 Load Balancer의 백 엔드 풀에 VM을 배치합니다.
3. 공용 Load Balancer에서 이러한 VM에 대해 아웃바운드 NAT를 프로그래밍하도록 아웃바운드 규칙을 구성합니다.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>내부 표준 Load Balancer 시나리오에 대한 아웃바운드 NAT

내부 표준 Load Balancer를 사용하는 경우 아웃바운드 연결이 명시적으로 선언될 때까지 아웃바운드 NAT를 사용할 수 없습니다. 다음 단계에 따라 내부 표준 Load Balancer 뒤에 있는 VM에 대한 아웃바운드 연결을 만드는 아웃바운드 규칙을 통해 아웃바운드 연결을 정의할 수 있습니다.

1. 공용 표준 Load Balancer를 만듭니다.
2. 백 엔드 풀을 만들고 내부 Load Balancer 외에, 공용 Load Balancer의 백 엔드 풀에 VM을 배치합니다.
3. 공용 Load Balancer에서 이러한 VM에 대해 아웃바운드 NAT를 프로그래밍하도록 아웃바운드 규칙을 구성합니다.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>공용 표준 Load Balancer를 통해 아웃바운드 NAT에 TCP 및 UDP 프로토콜을 모두 사용

- 공용 표준 Load Balancer를 사용하는 경우 제공되는 자동 아웃바운드 NAT 프로그래밍은 부하 분산 규칙의 전송 프로토콜과 일치합니다.  

   1. 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정합니다.
   2. 동일한 Load Balancer에서 아웃바운드 규칙을 구성합니다.
   3. VM에서 이미 사용하고 있는 백 엔드 풀을 다시 사용합니다.
   4. 다음과 같이 "프로토콜"을 지정합니다. 아웃바운드 규칙의 일부로 "모두"를 지정합니다.

- 인바운드 NAT 규칙만 사용하는 경우 아웃바운드 NAT는 제공되지 않습니다.

   1. 백 엔드 풀에 VM을 배치합니다.
   2. 공용 IP 주소 또는 공용 IP 접두사를 사용하여 하나 이상의 프런트 엔드 IP 구성을 정의합니다.
   3. 동일한 Load Balancer에서 아웃바운드 규칙을 구성합니다.
   4. 다음과 같이 "프로토콜"을 지정합니다. 아웃바운드 규칙의 일부로 "모두"를 지정합니다.

## <a name="limitations"></a>제한 사항

- 프런트 엔드 IP 주소당 사용 가능한 사용 후 삭제 포트의 최대 수는 51,200개입니다.
- 구성 가능한 아웃바운드 유휴 시간 제한 범위는 4-120분(240-7200초)입니다.
- Load Balancer는 아웃바운드 NAT에 대해 ICMP를 지원하지 않습니다.
- 아웃바운드 규칙은 포털을 통해 구성하거나 볼 수 없습니다.  대신 템플릿, REST API, Az CLI 2.0 또는 PowerShell을 사용하세요.
- 아웃바운드 규칙은 기본 NIC 및 기본 IP 구성에만 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [아웃바운드 연결에 Load Balancer](load-balancer-outbound-connections.md) 사용에 대해 자세히 알아보세요.
- [표준 Load Balancer](load-balancer-standard-overview.md)에 대해 알아봅니다.
- [유휴 시간 제한 시 양방향 TCP 재설정](load-balancer-tcp-reset.md)에 대해 알아봅니다.
- [Azure CLI 2.0을 사용하여 아웃바운드 규칙을 구성합니다](configure-load-balancer-outbound-cli.md).
