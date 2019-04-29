---
title: Azure 표준 Load Balancer란?
titlesuffix: Azure Load Balancer
description: Azure 표준 Load Balancer 기능 개요
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: kumud
ms.openlocfilehash: ee0dc1b9879c8a26c7f3e48cc8daf6ae3511b27a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734521"
---
# <a name="azure-standard-load-balancer-overview"></a>Azure 표준 Load Balancer 개요

Azure Load Balancer를 사용하여 애플리케이션 크기를 조정하고 서비스에 대한 고가용성을 구현할 수 있습니다. Load Balancer는 인바운드 및 아웃바운드 시나리오에 사용할 수 있으며, 짧은 대기 시간과 높은 처리량을 제공하고, 모든 TCP 및 UDP 애플리케이션에 대해 수백만 개의 흐름으로 확장됩니다. 

이 문서에서는 표준 Load Balancer에 대해 설명합니다.  Azure Load Balancer에 대한 보다 일반적인 개요를 보려면 [Load Balancer 개요](load-balancer-overview.md)도 검토하세요.

## <a name="what-is-standard-load-balancer"></a>표준 Load Balancer란?

표준 Load Balancer는 기본 Load Balancer보다 확장되고 좀 더 세분화된 기능 집합을 포함하는 모든 TCP 및 UDP 애플리케이션을 위한 새로운 Load Balancer 제품입니다.  유사성이 많지만 이 문서에 설명된 차이점을 이해하는 것이 중요합니다.

표준 Load Balancer는 공용 또는 내부 Load Balancer로 사용할 수 있습니다. 또한 가상 머신을 하나의 공용 Load Balancer 및 하나의 내부 Load Balancer 리소스에 연결할 수 있습니다.

Load Balancer 리소스의 기능은 항상 프런트 엔드, 규칙, 상태 프로브 및 백 엔드 풀 정의로 표현됩니다.  하나의 리소스가 여러 규칙을 포함할 수 있습니다. 가상 머신의 NIC 리소스에 있는 백 엔드 풀을 지정하여 가상 머신을 백 엔드 풀에 배치할 수 있습니다.  가상 머신 확장 집합을 사용하는 경우 이 매개 변수가 네트워크 프로필을 통해 전달되고 확장됩니다.

한 가지 중요한 측면은 리소스에 대한 가상 네트워크 범위입니다.  기본 Load Balancer는 가용성 집합 범위 내에 존재하지만, 표준 Load Balancer는 가상 네트워크의 범위에 완전히 통합되며, 모든 가상 네트워크 개념이 적용됩니다.

Load Balancer 리소스는 만들려는 시나리오를 달성하기 위해 Azure에서 다중 테넌트 인프라를 프로그래밍해야 하는 방법을 표현할 수 있는 개체입니다.  Load Balancer 리소스와 실제 인프라 사이에는 직접적인 관계가 없습니다. 따라서 Load Balancer를 만들어도 인스턴스가 만들어지지 않으며 용량은 항상 사용 가능하고, 고려해야 할 시작 또는 확장 지연도 없습니다. 

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다.  TLS 종료("SSL 오프로드") 또는 HTTP/HTTPS 요청별 애플리케이션 계층 처리를 확인하려는 경우 [Application Gateway](../application-gateway/application-gateway-introduction.md)를 검토하세요.  전역 DNS 부하 분산을 확인하려는 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 검토하세요.  필요에 따라 종단 간 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.

## <a name="why-use-standard-load-balancer"></a>표준 Load Balancer를 사용해야 하는 이유

표준 Load Balancer를 사용하면 크고 복잡한 다중 영역 아키텍처에 대한 소규모 배포에 맞게 애플리케이션의 크기를 조정하고 고가용성을 만들 수 있습니다.

표준 Load Balancer 및 기본 Load Balancer 간의 차이점에 대한 개요는 아래 표를 검토하세요.

>[!NOTE]
> 새 디자인에서는 표준 Load Balancer를 채택하는 것이 좋습니다. 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

[Load Balancer의 서비스 제한](https://aka.ms/lblimits)과 [가격 책정](https://aka.ms/lbpricing) 및 [SLA](https://aka.ms/lbsla)를 검토하세요.


### <a name="backend"></a>백 엔드 풀

표준 Load Balancer 백 엔드 풀은 가상 네트워크의 가상 머신 리소스로 확장됩니다.  최대 1,000개의 백 엔드 인스턴스를 포함할 수 있습니다.  백 엔드 인스턴스는 NIC 리소스의 속성인 IP 구성입니다.

백 엔드 풀은 독립 실행형 가상 머신, 가용성 집합 또는 가상 머신 확장 집합을 포함할 수 있습니다.  또한 백 엔드 풀에서 리소스를 혼합할 수 있습니다. Load Balancer 리소스당 백 엔드 풀에서 최대 150개의 리소스를 결합할 수 있습니다.

백 엔드 풀을 디자인하는 방법을 고려할 때는 가장 적은 수의 개별 백 엔드 풀 리소스에 맞게 디자인하고, 관리 작업 기간을 추가로 최적화할 수 있습니다.  데이터 평면 성능 또는 크기 조정에는 차이가 없습니다.

### <a name="probes"></a>상태 프로브
  
표준 Load Balancer는 HTTPS 애플리케이션을 정확하게 모니터링하려면 [HTTPS 상태 프로브](load-balancer-custom-probe-overview.md#httpprobe)(TLS(전송 계층 보안) 래퍼를 사용한 HTTP 프로브)에 대한 지원을 추가합니다.  

또한, 전체 백 엔드 풀에서 [조사](load-balancer-custom-probe-overview.md#probedown)하는 경우 표준 Load Balancer는 설정된 모든 TCP 연결이 지속되도록 허용합니다. (기본 Load Balancer는 모든 인스턴스에 대한 모든 TCP 연결을 종료합니다).

자세한 내용은 [Load Balancer 상태 프로브](load-balancer-custom-probe-overview.md)를 검토합니다.

### <a name="az"></a> 가용성 영역

>[!IMPORTANT]
>검토 [가용성 영역](../availability-zones/az-overview.md) 관련된 항목에 대 한 지역 특정 정보를 포함 합니다.

표준 Load Balancer는 가용성 영역을 사용할 수 있는 지역에서 추가 기능을 지원합니다.  이러한 기능은 모든 표준 Load Balancer 제공 기능에 추가됩니다.  가용성 영역 구성은 공용 및 내부 표준 Load Balancer에 사용할 수 있습니다.

비영역 프런트 엔드는 가용성 영역이 구성된 지역에 배포될 경우 기본적으로 영역 중복 상태가 됩니다.   영역 중복 프런트 엔드는 영역 실패 후에도 유지되며, 모든 영역의 전용 인프라를 통해 제공됩니다. 

또한 특정 영역에 대한 프런트 엔드도 보장할 수 있습니다. 영역 프론트 엔드는 해당 영역과 결과를 공유하며, 단일 영역의 전용 인프라를 통해서만 제공됩니다.

영역 간 부하 분산은 백 엔드 풀에 사용될 수 있으며, vnet의 모든 가상 머신은 백 엔드 풀의 일부가 될 수 있습니다.

[가용성 영역 관련 기능에 대한 자세한 논의](load-balancer-standard-availability-zones.md)를 검토하세요.

### <a name="diagnostics"></a> 진단

표준 Load Balancer는 Azure Monitor를 통해 다차원 메트릭을 제공합니다.  이러한 메트릭은 지정된 차원에 대해 필터링, 그룹화 및 나눌 수 있습니다.  이러한 메트릭은 서비스의 성능과 상태에 대한 현재 및 이전 정보를 제공합니다.  리소스 상태도 지원됩니다.  다음은 지원되는 진단에 대한 간략한 개요입니다.

| 메트릭 | 설명 |
| --- | --- |
| VIP 가용성 | 표준 Load Balancer는 지역 내에서 Load Balancer 프런트 엔드를 거쳐 VM을 지원하는 SDN 스택으로 이어지는 데이터 경로를 지속적으로 사용합니다. 정상 인스턴스가 남아 있는 한 측정은 애플리케이션 부하가 분산된 트래픽과 동일한 경로를 따릅니다. 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 애플리케이션에 표시되지 않으며 다른 작업을 방해하지 않습니다.|
| DIP 가용성 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 Load Balancer 풀에서 각 개별 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다.  상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다.
| SYN 패킷 | 표준 Load Balancer는 TCP 연결을 종료하거나 TCP 또는 UDP 패킷 흐름을 조작하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 사이에 있습니다. TCP 프로토콜 시나리오의 문제를 잘 해결하기 위해 SYN 패킷 카운터를 사용하여 TCP 연결 시도 횟수를 파악할 수 있습니다. 메트릭은 수신된 TCP SYN 패킷 수를 보고합니다.|
| SNAT 연결 | 표준 Load Balancer는 공용 IP 주소 프런트 엔드로 위장한 아웃바운드 흐름 수를 보고합니다. SNAT 포트는 소모성 리소스입니다. 이 메트릭은 애플리케이션이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다.  성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다.|
| 바이트 카운터 | 표준 Load Balancer는 프런트 엔드당 처리되는 데이터를 보고합니다.|
| 패킷 카운터 | 표준 Load Balancer는 프런트 엔드당 처리되는 패킷을 보고합니다.|

[표준 Load Balancer 진단에 대한 자세한 논의](load-balancer-standard-diagnostics.md)를 검토하세요.

### <a name="haports"></a>HA 포트

표준 Load Balancer는 새로운 유형의 규칙을 지원합니다.  

부하 분산 규칙을 구성하여 애플리케이션 규모를 매우 안정적으로 만들 수 있습니다. HA 포트 부하 분산 규칙을 사용할 경우, 표준 Load Balancer는 내부 표준 Load Balancer 프런트 엔드 IP 주소의 모든 삭제 포트에서 흐름별 부하 분산을 제공합니다.  이 기능은 개별 포트 지정이 불가능하거나 바람직하지 않은 다른 시나리오에 유용합니다.

HA 포트 부하 분산 규칙을 사용하여 네트워크 가상 어플라이언스 및 광범위한 인바운드 포트가 모든 애플리케이션을 위한 활성-수동 또는 활성-활성 n+1 시나리오를 만들 수 있습니다.  상태 프로브를 사용하여 새 흐름을 수신할 백 엔드를 확인할 수 있습니다.  네트워크 보안 그룹을 사용하여 포트 범위 시나리오를 에뮬레이트할 수 있습니다.

>[!IMPORTANT]
> 네트워크 가상 어플라이언스를 사용하려는 경우, 제품을 HA 포트로 테스트했는지에 따른 지침을 공급업체에 문의하고 구체적인 지침에 따라 구현합니다. 

[HA 포트에 대한 자세한 논의](load-balancer-ha-ports-overview.md)를 검토하세요.

### <a name="securebydefault"></a>기본적으로 보안 적용

표준 Load Balancer는 가상 네트워크에 완벽하게 온보딩됩니다.  가상 네트워크는 닫혀 있는 개인 네트워크입니다.  표준 Load Balancer 및 표준 공용 IP 주소는 이 가상 네트워크를 가상 네트워크 외부에서 액세스할 수 있도록 디자인되어 있으므로, 이제 이러한 리소스는 열지 않으면 기본적으로 닫혀 있습니다. 즉, 이제 NSG(네트워크 보안 그룹)를 사용하여 트래픽을 명시적으로 허용합니다.  전체 가상 데이터 센터를 만들고, NSG를 통해 사용 가능한 항목 및 시기를 결정할 수 있습니다.  서브넷에 NSG가 없거나 가상 머신 리소스의 NIC가 없으면 트래픽이 이 리소스에 도달하도록 허용되지 않습니다.

NSG에 대한 개요와 NSG를 시나리오에 적용하는 방법을 자세히 알아보려면 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 참조하세요.

### <a name="outbound"></a> 아웃바운드 연결

Load Balancer는 인바운드 및 아웃바운드 시나리오를 지원합니다.  표준 Load Balancer는 아웃바운드 연결 측면에서 기본 Load Balancer와 크게 다릅니다.

가상 네트워크의 내부, 개인 IP 주소를 Load Balancer 프런트 엔드의 공용 IPO 주소에 매핑하는 데 SNAT(Systems Network Architecture)가 사용됩니다.

표준 Load Balancer는 [좀 더 강력하고 확장 가능하고 예측 가능한 SNAT 알고리즘](load-balancer-outbound-connections.md#snat)을 위한 새로운 알고리즘을 도입했으며, 새로운 기능을 지원하고, 모호함을 없애고, 파생 작업을 기대하기 보다는 명시적인 구성을 강제로 적용합니다. 이러한 변경은 새 기능이 구현되기 위해 반드시 필요합니다. 

다음은 표준 Load Balancer로 작업할 때 기억해야 할 주요 개념입니다.

- 규칙이 완성되면 Load Balancer 리소스가 구동됩니다.  Azure의 모든 프로그래밍은 구성에서 파생됩니다.
- 여러 프론트 엔드를 사용할 수 있는 경우, 모든 프런트 엔드가 사용되므로 사용 가능한 SNAT 포트 수가 크게 증가합니다.
- 특정 프런트 엔드를 아웃바운드 연결에 사용할지를 선택하고 제어할 수 있습니다.
- 아웃바운드 시나리오는 명시적이며 아웃바운드 연결은 지정될 때까지 존재하지 않습니다.
- 부하 분산 규칙은 SNAT가 프로그래밍되는 방식을 유추합니다. 부하 분산 규칙은 프로토콜에 따라 다릅니다. SNAT는 프로토콜에 따라 다르므로, 파생 결과가 나타나지 않도록 구성에 프로토콜이 반영되어야 합니다.

#### <a name="multiple-frontends"></a>여러 프런트 엔드
아웃바운드 연결에 대한 수요가 높아질 것으로 예상되거나 이미 높아져서 더 많은 SNAT 포트를 원할 경우, 동일한 가상 머신 리소스에 대해 추가 프런트 엔드, 규칙 및 백 엔드 풀을 구성하여 증분 SNAT 포트 인벤토리를 추가할 수도 있습니다.

#### <a name="control-which-frontend-is-used-for-outbound"></a>아웃바운드에 사용되는 프런트 엔드 제어
특정 프런트 엔드 IP 주소에서만 시작되도록 아웃바운드 연결을 제한하려는 경우, 필요에 따라 아웃바운드 매핑을 나타내는 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정할 수 있습니다.

#### <a name="control-outbound-connectivity"></a>아웃바운드 연결 제어
표준 Load Balancer는 가상 네트워크의 컨텍스트 내에 존재합니다.  가상 네트워크는 격리된 개인 네트워크입니다.  공용 IP 주소와의 연결이 없으면 공용 연결이 허용되지 않습니다.  [VNet 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 가상 네트워크의 내부 및 로컬에 있으므로 연결 가능합니다.  가상 네트워크 외부의 대상에 대해 아웃바운드 연결을 설정하려는 경우 다음 두 가지 옵션을 사용할 수 있습니다.
- 표준 SKU 공용 IP 주소를 가상 머신 리소스에 대한 인스턴스 수준 공용 IP 주소로 할당 또는
- 가상 머신 리소스를 공용 표준 Load Balancer의 백 엔드 풀에 배치

두 방법 모두 가상 네트워크에서 가상 네트워크 외부로의 아웃바운드 연결을 허용합니다. 

가상 머신 리소스가 있는 백 엔드 풀에 내부 표준 Load Balancer_만_ 연결되어 있는 경우 가상 머신은 가상 네트워크 리소스 및 [VNet 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에만 연결될 수 있습니다.  아웃바운드 연결을 만들려면 이전 단락에 설명된 단계를 따르면 됩니다.

표준 SKU와 연결되지 않은 가상 머신 리소스의 아웃바운드 연결은 이전과 동일하게 유지됩니다.

[아웃바운드 연결에 대한 자세한 논의](load-balancer-outbound-connections.md)를 검토하세요.

### <a name="multife"></a>여러 프런트 엔드
Load Balancer는 여러 프런트 엔드가 있는 여러 규칙을 지원합니다.  표준 Load Balancer는 이 규칙을 아웃바운드 시나리오로 확장합니다.  아웃바운드 시나리오는 기본적으로 인바운드 부하 분산 규칙을 뒤집은 것입니다.  인바운드 부하 분산 규칙에서도 아웃바운드 연결을 위한 연결을 만듭니다. 표준 Load Balancer는 부하 분산 규칙을 통해 가상 머신 리소스와 연결된 모든 프런트 엔드를 사용합니다.  또한 부하 분산 규칙에 대한 매개 변수를 사용하여 아웃바운드 연결을 위한 부하 분산 규칙을 표시하지 않을 수 있으며, 없음을 비롯한 특정 프런트 엔드 선택 옵션도 사용할 수 있습니다.

비교하자면, 기본 Load Balancer는 임의로 단일 프런트 엔드를 선택하며, 선택되는 프런트 엔드를 제어하는 기능도 없습니다.

[아웃바운드 연결에 대한 자세한 논의](load-balancer-outbound-connections.md)를 검토하세요.

### <a name="operations"></a> 관리 작업

표준 Load Balancer 리소스는 완전히 새로운 인프라 플랫폼에 존재합니다.  따라서 표준 SKU에 대한 관리 작업이 더 빨라지며, 완료 시간도 표준 SKU 리소스별로 30초 이내로 감소됩니다.  백 엔드 풀 크기가 커질수록 백 엔드 풀 변경에 필요한 기간도 늘어납니다.

표준 Load Balancer 리소스를 수정하고 표준 공용 IP 주소를 가상 머신 간에 훨씬 더 빠르게 이동할 수 있습니다.

## <a name="migration-between-skus"></a>SKU 간의 마이그레이션

SKU는 변경할 수 없습니다. 이 섹션의 단계에 따라 리소스 SKU 간에 이동합니다.

>[!IMPORTANT]
>SKU 간의 차이점을 이해하고 시나리오를 신중하게 검사하기 위해 전체에서 이 문서를 검토합니다.  시나리오에 맞게 추가로 변경해야 할 수 있습니다.

### <a name="migrate-from-basic-to-standard-sku"></a>기본에서 표준 SKU로 마이그레이션

1. 새 표준 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만듭니다. 규칙 및 프로브 정의를 다시 작성합니다.  전에 443/tcp에 대해 TCP 프로브를 사용한 경우 이 프로브 프로토콜을 HTTPS 프로브로 변경하고 경로를 추가하는 것이 좋습니다.

2. NIC 또는 서브넷에 새 NSG를 만들거나 기존 NSG를 업데이트하여 허용하려는 다른 트래픽 외에도 부하 분산된 트래픽과 프로브를 허용 목록에 추가합니다.

3. 모든 VM 인스턴스에서 기본 SKU 리소스(Load Balancer 및 해당되는 경우 공용 IP)를 제거합니다. 가용성 집합의 모든 VM 인스턴스도 제거해야 합니다.

4. 새 표준 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

### <a name="migrate-from-standard-to-basic-sku"></a>표준에서 기본 SKU로 마이그레이션

1. 새 기본 리소스(Load Balancer 및 필요에 따라 공용 IP)를 만듭니다. 규칙 및 프로브 정의를 다시 작성합니다.  HTTPS 프로브를 443/tcp에 대한 TCP 프로브로 변경합니다. 

2. 모든 VM 인스턴스에서 표준 SKU 리소스(Load Balancer 및 해당되는 경우 공용 IP)를 제거합니다. 가용성 집합의 모든 VM 인스턴스도 제거해야 합니다.

3. 새 기본 SKU 리소스에 모든 VM 인스턴스를 연결합니다.

>[!IMPORTANT]
>
>기본 및 표준 SKU를 사용할 때 다음과 같은 제한 사항이 따릅니다.
>
>HA 포트 및 표준 SKU 진단은 표준 SKU에서만 사용할 수 있습니다. 표준 SKU에서 기본 SKU로 마이그레이션할 수 없으며 이러한 기능을 유지할 수도 없습니다.
>
>기본 및 표준 SKU는 이 문서에 설명된 것과 같은 많은 차이점을 갖습니다.  이러한 차이점을 이해하고 대비해야 합니다.
>
>SKU 일치는 Load Balancer 및 공용 IP 리소스에 대해 사용되어야 합니다. 기본 SKU 리소스와 표준 SKU 리소스를 함께 사용할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 리소스의 가상 머신 또는 가상 머신 확장 집합 리소스를 두 SKU에 동시에 연결할 수 없습니다.

## <a name="region-availability"></a>지역 가용성

표준 Load Balancer는 현재 모든 공용 클라우드 지역에서 사용 가능합니다.

## <a name="sla"></a>SLA

표준 Load Balancer는 99.99% SLA으로 사용할 수 있습니다.  자세한 내용은 [표준 Load Balancer SLA](https://aka.ms/lbsla)를 검토하세요.

## <a name="pricing"></a>가격

표준 Load Balancer 사용량에 해당하는 요금이 청구됩니다.

- 구성된 부하 분산 및 아웃바운드 규칙의 수(인바운드 NAT 규칙은 총 규칙 수에 포함되지 않음)
- 규칙에 관계없이 인바운드 및 아웃바운드에서 처리된 데이터의 양 

표준 Load Balancer 가격 정보에 대해서는 [Load Balancer 가격](https://azure.microsoft.com/pricing/details/load-balancer/) 페이지를 참조하세요.

## <a name="limitations"></a>제한 사항

- SKU는 변경할 수 없습니다. 기존 리소스의 SKU는 변경할 수 없습니다.
- 독립 실행형 가상 머신 리소스, 가용성 집합 리소스 또는 가상 머신 확장 집합 리소스는 하나의 SKU만 참조할 수 있습니다.
- Load Balancer 규칙은 두 가상 네트워크에 걸쳐 있을 수 없습니다.  프런트엔드 및 해당 관련 백 엔드 인스턴스는 동일한 가상 네트워크에 있어야 합니다.  
- Load Balancer 프런트 엔드는 전역 가상 네트워크 피어링에 액세스할 수 없습니다.
- [구독 작업 이동](../azure-resource-manager/resource-group-move-resources.md)은 표준 SKU LB 및 PIP 리소스에 대해 지원되지 않습니다.
- VNet 및 기타 Microsoft 플랫폼 서비스가 없는 웹 작업자 역할은 사전 VNet 서비스 및 다른 플랫폼 서비스 작동 방식의 부작용으로 인해 내부 표준 Load Balancer만 사용할 때 액세스할 수 있습니다. 해당 서비스 자체 또는 기본 플랫폼이 예고 업이 변경될 수 있기 때문에 여기에 의존하지 말아야 합니다. 내부 표준 Load Balancer만 사용하는 경우 원하면 명시적으로 [아웃 바운드 연결](load-balancer-outbound-connections.md)을 만들어야 한다고 항상 가정해야 합니다.
- Load Balancer는 이러한 특정 IP 프로토콜에 대한 부하 분산 및 포트 전달용 TCP 또는 UDP 제품입니다.  부하 분산 규칙 및 인바운드 NAT 규칙은 TCP 및 UDP에 대해 지원되며 ICMP를 포함한 다른 IP 프로토콜에 대해서는 지원되지 않습니다. Load Balancer가 종료 및 응답하지 않거나 UDP 또는 TCP 흐름의 페이로드와 상호 작용하지 않습니다. 프록시가 아닙니다. 프런트 엔드 연결에 대한 성공적인 유효성 검사는 부하 분산 또는 인바운드 NAT 규칙(TCP 또는 UDP)에 사용된 동일한 프로토콜을 사용하여 대역 내에서 이뤄져야 하며, _그리고_ 하나 이상의 가상 머신은 프런트 엔드에서 응답을 확인하려면 클라이언트에 대한 응답을 생성해야 합니다.  Load Balancer 프런트 엔드에서 대역 내 응답을 수신하지 못하면 어떤 가상 머신도 응답할 수 없음을 나타냅니다.  응답할 수 있는 가상 머신이 없으면 Load Balancer 프런트 엔드와 상호 작용이 불가능합니다.  이는 [SNAT 모조 포트](load-balancer-outbound-connections.md#snat)가 TCP 및 UDP에 대해서만 지원되고 ICMP를 포함한 다른 모든 IP 프로토콜이 실패하는 경우에 아웃 바운드 연결에도 적용됩니다.  완화할 인스턴스 수준 공용 IP 주소를 할당합니다.
- 가상 네트워크 내의 개인 IP 주소에서 공용 IP 주소로 전환할 때 [아웃 바운드 연결](load-balancer-outbound-connections.md)을 제공하는 공용 Load Balancer와 달리 공용 및 내부 Load Balancer가 모두 개인 IP 주소 공간에 있으므로 내부 Load Balancer는 내부 Load Balancer의 프런트 엔드에 아웃바운드 연결을 변환하지 않습니다.  이렇게 하면 변환이 필요하지 않은 경우 고유한 내부 IP 주소 공간 내에서 SNAT 소모 가능성이 없습니다.  부작용은 백 엔드 풀의 VM에서 아웃바운드 흐름이 상주하는 풀에 있는 내부 Load Balancer의 프런트 엔드로 흐름을 유도하고 _또_ 자체로 매핑되는 경우, 흐름의 구간 둘 다 일치하지 않으며 흐름이 실패하게 된다는 것입니다.  흐름이 프런트 엔드로 흐름을 생성한 백 엔드 풀에서 동일한 VM에 다시 매핑하지 않은 경우 흐름은 성공하게 됩니다.   흐름이 스스로에게 다시 매핑되는 경우 아웃바운드 흐름은 VM에서 시작돼 프런트 엔드에 나타나며 해당 인바운드 흐름은 VM에서 시작돼 자신에게 나타납니다. 게스트 OS의 관점에서 동일한 흐름의 인바운드 및 아웃 바운드 부분은 가상 머신 내에서 일치하지 않습니다. TCP 스택은 원본과 대상이 일치하지 않으므로 동일한 흐름의 이러한 절반을 동일한 흐름의 일부로서 인식하지 않습니다.  흐름이 백 엔드 풀에서 다른 모든 VM에 매핑되는 경우 흐름의 절반은 일치하며 VM은 성공적으로 흐름에 응답할 수 있습니다.  이 시나리오의 증상은 일시적인 연결 시간 제한입니다. 이 시나리오를 안전하게 실현하기 위한 몇 가지 일반적인 해결 방법(백 엔드 풀에서 백 엔드 풀 해당 내부 Load Balancer 프런트 엔드로 흐름이 시작)에는 내부 Load Balancer 뒤에 타사 프록시의 삽입이나 [DSR 스타일 규칙 사용](load-balancer-multivip-overview.md) 중 하나가 포함됩니다.  완화할 공용 Load Balancer를 사용할 수도 있지만 결과적인 시나리오는 [SNAT 소모](load-balancer-outbound-connections.md#snat)가 발생하기 쉬우며 주의해 관리하지 않는 한 사용하지 말아야 합니다.

## <a name="next-steps"></a>다음 단계

- [표준 Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md) 사용에 대해 자세히 알아보세요.
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [가용성 영역](../availability-zones/az-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)의 진단과 관련된 [지원되는 다차원 메트릭](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers)에 대해 자세히 알아보세요.
- [아웃바운드 연결에 Load Balancer](load-balancer-outbound-connections.md) 사용에 대해 자세히 알아보세요.
- [아웃바운드 규칙](load-balancer-outbound-rules-overview.md)에 대해 알아보세요.
- [유휴 상태의 TCP 재설정](load-balancer-tcp-reset.md)에 대해 알아보세요.
- [HA 포트 부하 분산 규칙을 사용하는 표준 Load Balancer](load-balancer-ha-ports-overview.md)에 대해 자세히 알아보세요.
- [다중 프런트 엔드를 사용하는 Load Balancer](load-balancer-multivip-overview.md)에 대해 자세히 알아보세요.
- [Virtual Networks](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
- [VNet 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에 대해 자세히 알아보세요.
- Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보세요.
- [Load Balancer](load-balancer-overview.md)에 대해 자세히 알아보세요.
