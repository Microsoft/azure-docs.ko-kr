---
title: Azure 네트워크 보안 그룹 개요
titlesuffix: Azure Virtual Network
description: 네트워크 보안 그룹에 대해 알아봅니다. 네트워크 보안 그룹을 통해 Azure 리소스 간의 네트워크 트래픽을 필터링 할 수 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperfq1
ms.openlocfilehash: 940fb9046a69c28e4f43abb03a4469a2f73d9eb2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074902"
---
# <a name="network-security-groups"></a>네트워크 보안 그룹
<a name="network-security-groups"></a>

Azure 네트워크 보안 그룹을 사용 하 여 azure 가상 네트워크에서 azure 리소스에 대 한 네트워크 트래픽을 필터링 할 수 있습니다. 네트워크 보안 그룹에는 여러 유형의 Azure 리소스에 대 한 인바운드 네트워크 트래픽 또는 아웃 바운드 네트워크 트래픽을 허용 하거나 거부 하는 [보안 규칙이](#security-rules) 포함 되어 있습니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다.

이 문서에서는 네트워크 보안 그룹 규칙의 속성, 적용 되는 [기본 보안 규칙](#default-security-rules) 및 [강화 된 보안 규칙](#augmented-security-rules)을 만들기 위해 수정할 수 있는 규칙 속성을 설명 합니다.

## <a name="security-rules"></a><a name="security-rules"></a> 보안 규칙

네트워크 보안 그룹은 Azure 구독 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 내에서 필요한 만큼 0개 또는 많은 규칙을 포함합니다. 각 규칙은 다음 속성을 지정합니다.

|속성  |설명  |
|---------|---------|
|이름|네트워크 보안 그룹 내에서 고유한 이름입니다.|
|우선 순위 | 100~4096 사이의 숫자입니다. 낮은 번호의 우선 순위가 더 높기 때문에 규칙은 낮은 번호가 높은 번호보다 먼저 처리되는 우선 순위 순서로 처리됩니다. 트래픽이 규칙과 일치하면 처리가 중지됩니다. 따라서 우선 순위가 높은 규칙과 동일한 특성을 가진 우선 순위가 낮은 규칙(높은 번호)은 처리되지 않습니다.|
|원본 또는 대상| 임의 또는 개별 IP 주소, CIDR(Classless Inter-Domain Routing) 블록(예: 10.0.0.0/24), 서비스 태그 또는 애플리케이션 보안 그룹입니다. Azure 리소스의 주소를 지정하는 경우 리소스에 할당된 개인 IP 주소를 지정하세요. 네트워크 보안 그룹은 Azure가 공용 IP 주소를 인바운드 트래픽용 개인 IP 주소로 변환한 후에, 그리고 Azure가 개인 IP 주소를 아웃바운드 트래픽용 공용 IP 주소로 변환하기 전에 처리됩니다. . 범위, 서비스 태그 또는 애플리케이션 보안 그룹을 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 규칙에서 여러 개별 IP 주소와 범위를 지정 하는 기능 (여러 서비스 태그 또는 응용 프로그램 그룹을 지정할 수 없음)은 [보강 된 보안 규칙](#augmented-security-rules)이라고 합니다. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 여러 개의 IP 주소 및 IP 주소 범위를 지정할 수 없습니다.|
|프로토콜     | TCP, UDP, ICMP 또는 Any입니다.|
|방향| 규칙이 인바운드 또는 아웃바운드 트래픽에 적용되는지 여부입니다.|
|포트 범위     |개별 포트나 포트의 범위를 지정할 수 있습니다. 예를 들어 80 또는 10000-10005과 같이 지정할 수 있습니다. 범위를 지정하면 더 적은 보안 규칙을 만들어도 됩니다. 보강된 보안 규칙은 Resource Manager 배포 모델을 통해 만들어진 네트워크 보안 그룹에서만 만들 수 있습니다. 클래식 배포 모델을 통해 만든 네트워크 보안 그룹에서는 동일한 보안 규칙에 여러 개의 포트 또는 포트 범위를 지정할 수 없습니다.   |
|작업     | 허용 또는 거부        |

네트워크 보안 그룹 보안 규칙은 5 튜플 정보(원본, 원본 포트, 대상, 대상 포트 및 프로토콜)를 사용하는 우선 순위를 통해 평가되어 트래픽을 허용하거나 거부합니다. 기존 연결에 대한 흐름 레코드가 만들어집니다. 통신은 흐름 레코드의 연결 상태에 따라 허용 또는 거부됩니다. 흐름 레코드는 네트워크 보안 그룹의 상태 저장을 허용합니다. 예를 들어 포트 80을 통해 모든 주소에 대한 아웃바운드 보안 규칙을 지정하는 경우 아웃바운드 트래픽에 대한 응답에 인바운드 보안 규칙을 지정하지 않아도 됩니다. 통신이 외부에서 시작된 경우 인바운드 보안 규칙을 지정하기만 하면 됩니다. 반대의 경우도 마찬가지입니다. 포트를 통해 인바운드 트래픽이 허용되는 경우 포트를 통해 트래픽에 응답하도록 아웃바운드 보안 규칙을 지정하지 않아도 됩니다.

흐름을 사용하는 보안 규칙을 제거해도 기존 연결이 중단되지 않을 수 있습니다. 연결이 중단되고 몇 분 이상 어느 방향으로도 트래픽이 흐르지 않으면 트래픽 흐름이 중단됩니다.

한 네트워크 보안 그룹에 만들 수 있는 보안 규칙 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

### <a name="default-security-rules"></a><a name="default-security-rules"></a> 기본 보안 규칙

Azure는 사용자가 만드는 각 네트워크 보안 그룹에 다음과 같은 기본 규칙을 만듭니다.

#### <a name="inbound"></a>인바운드

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|모두|Allow|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|모두|Allow|

##### <a name="denyallinbound"></a>DenyAllInbound

|우선 순위|원본|원본 포트|대상|대상 포트|프로토콜|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|모두|거부|

#### <a name="outbound"></a>아웃바운드

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 모두 | Allow |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | 인터넷 | 0-65535 | 모두 | Allow |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|우선 순위|원본|원본 포트| 대상 | 대상 포트 | 프로토콜 | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 모두 | 거부 |

**원본** 및 **대상** 열에서 *VirtualNetwork*, *AzureLoadBalancer* 및 *인터넷*은 IP 주소가 아닌 [서비스 태그](service-tags-overview.md)입니다. 프로토콜 열에는 TCP, UDP 및 ICMP **가 포함 됩니다** . 규칙을 만들 때 TCP, UDP, ICMP 또는 Any를 지정할 수 있습니다. **소스** 및 **대상** 열에서 *0.0.0.0/0*은 모든 주소를 나타냅니다. Azure Portal, Azure CLI 또는 PowerShell과 같은 클라이언트는이 식에 * 또는 any를 사용할 수 있습니다.
 
기본 규칙을 제거할 수 없지만 더 높은 우선 순위의 규칙을 만들어서 재정의할 수 있습니다.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> 보강 보안 규칙

보강된 보안 규칙은 가상 네트워크에 대한 보안 정의를 간소화하여 더 적은 규칙으로 크고 복잡한 네트워크 보안 정책을 정의할 수 있도록 합니다. 여러 포트, 여러 명시적 IP 주소 및 범위를 이해하기 쉬운 단일 보안 규칙으로 결합할 수 있습니다. 규칙의 원본, 대상 및 포트 필드에서 보강된 규칙을 사용합니다. 보안 규칙 정의의 유지 관리를 간소화 하려면 보강 된 보안 규칙을 [서비스 태그](service-tags-overview.md) 또는 [응용 프로그램 보안 그룹과](#application-security-groups)결합 합니다. 한 규칙에서 지정할 수 있는 주소, 범위 및 포트 수가 제한됩니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

#### <a name="service-tags"></a>서비스 태그

서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. 네트워크 보안 규칙에 대 한 빈번한 업데이트의 복잡성을 최소화 하는 데 도움이 됩니다.

자세한 내용은 [Azure 서비스 태그](service-tags-overview.md)를 참조 하세요. 저장소 서비스 태그를 사용 하 여 네트워크 액세스를 제한 하는 방법에 대 한 예제는 [PaaS 리소스에 대 한 네트워크 액세스 제한](tutorial-restrict-network-access-to-resources.md)을 참조 하세요.

#### <a name="application-security-groups"></a>애플리케이션 보안 그룹

애플리케이션 보안 그룹을 사용하면 네트워크 보안을 애플리케이션 구조의 자연 확장으로 구성하여 가상 머신을 그룹화하고 해당 그룹에 따라 네트워크 보안 정책을 정의할 수 있습니다. 명시적 IP 주소를 수동으로 유지 관리하지 않고 대규모 보안 정책을 재사용할 수 있습니다. 자세히 알아보려면 [응용 프로그램 보안 그룹](application-security-groups.md)을 참조 하세요.

## <a name="azure-platform-considerations"></a>Azure 플랫폼 고려 사항

- **호스트 노드의 가상 IP**: DHCP, DNS, imds 및 상태 모니터링과 같은 기본 인프라 서비스는 가상화 된 호스트 IP 주소 168.63.129.16 및 169.254.169.254를 통해 제공 됩니다. 이러한 IP 주소는 Microsoft에 속하며, 이 용도로 모든 지역에서 유일하게 사용되는 가상화된 IP 주소입니다. 효과적인 보안 규칙 및 유효 경로에는 이러한 플랫폼 규칙이 포함 되지 않습니다. 이 기본 인프라 통신을 재정의 하기 위해 네트워크 보안 그룹 규칙에서 AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM의 [서비스 태그](service-tags-overview.md) 를 사용 하 여 트래픽을 거부 하는 보안 규칙을 만들 수 있습니다. 네트워크 [트래픽 필터링을 진단](diagnose-network-traffic-filter-problem.md) 하 고 [네트워크 라우팅을 진단](diagnose-network-routing-problem.md)하는 방법을 알아봅니다.
- **라이선싱(키 관리 서비스):** 가상 머신에서 실행되는 Windows 이미지는 사용이 허가되어 있어야 합니다. 사용 허가를 위해 라이선스 요청이 이러한 쿼리를 처리하는 키 관리 서비스 호스트 서버로 전송됩니다. 요청은 1688 포트를 통해 아웃바운드로 수행됩니다. [기본 경로 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) 구성을 사용한 배포에 대해 이 플랫폼 규칙은 사용하지 않도록 설정됩니다.
- **부하가 분산된 풀의 가상 머신**: 적용되는 원본 포트와 주소 범위는 부하 분산 장치가 아닌 원래 컴퓨터에서 가져옵니다. 대상 포트와 주소 범위는 부하 분산 장치가 아닌 대상 컴퓨터에서 가져옵니다.
- **Azure 서비스 인스턴스**: HDInsight, 애플리케이션 서비스 환경 및 Virtual Machine Scale Sets와 같은 몇 가지 Azure 서비스의 인스턴스는 가상 네트워크 서브넷에 배포됩니다. 가상 네트워크에 배포할 수 있는 서비스의 전체 목록은 [Azure 서비스에 대한 가상 네트워크](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)를 참조하세요. 리소스를 배포한 서브넷에 네트워크 보안 그룹을 적용하기 전에 각 서비스에 대한 포트 요구 사항을 잘 이해하도록 합니다. 서비스에 필요한 포트를 거부하는 경우 서비스가 제대로 작동하지 않습니다.
- **아웃바운드 전자 메일 보내기**: 인증된 SMTP 릴레이 서비스(일반적으로 587 TCP 포트를 통해 연결되지만 종종 다른 방법도 사용)를 활용하여 Azure Virtual Machines에서 전자 메일을 보내는 것이 좋습니다. SMTP 릴레이 서비스는 타사 전자 메일 공급자에서 메시지를 거부할 가능성을 최소화하기 위해 보낸 사람 신뢰도를 특수화합니다. 이러한 SMTP 릴레이 서비스는 Exchange Online Protection 및 SendGrid를 포함하지만 여기에 제한되지 않습니다. SMTP 릴레이 서비스는 구독 유형에 관계 없이 Azure에서 제한되지 않고 사용할 수 있습니다. 

  2017년 11월 15일까지 Azure 구독을 만든 경우 SMTP 릴레이 서비스를 사용할 수 있을 뿐만 아니라 TCP 포트 25를 통해 직접 전자 메일을 보낼 수 있습니다. 2017년 11월 15일 이후에 구독을 만든 경우 포트 25를 통해 직접 전자 메일을 보낼 수 없습니다. 포트 25를 통한 아웃바운드 통신 동작은 다음과 같이 구독 형식에 따라 다릅니다.

     - **기업 계약**: 아웃바운드 포트 25 통신이 허용됩니다. Azure 플랫폼에서 제한 없이 가상 머신에서 외부 전자 메일 공급자로 직접 아웃 바운드 전자 메일을 보낼 수 있습니다. 
     - **종량제:** 모든 리소스에서 아웃바운드 포트 25 통신이 차단되었습니다. 가상 머신에서 전자 메일을 외부 전자 메일 공급자로 직접 보내야 하는 경우(인증된 SMTP 릴레이를 사용하지 않음) 제한을 제거하도록 요청할 수 있습니다. 요청은 Microsoft의 재량에 따라 검토되고 승인되어 부패 방지 검사를 수행한 후에 허가됩니다. 요청하려면 *기술*, *Virtual Network 연결*, *전자 메일을 보낼 수 없습니다(SMTP/포트 25).* 라는 문제 형식의 지원 사례를 엽니다. 지원 사례에는 인증된 SMTP 릴레이를 통하지 않고 구독에서 메일 공급자로 직접 전자 메일을 보내야 하는 이유에 대한 세부 정보가 포함됩니다. 구독이 제외되는 경우 예외 날짜 이후에 만든 가상 머신만이 포트 25를 통해 아웃바운드로 통신할 수 있습니다.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark 및 평가판**: 모든 리소스에서 아웃바운드 포트 25 통신이 차단되었습니다. 요청이 허가되지 않기 때문에 제한을 제거하도록 요청할 수 없습니다. 가상 머신에서 이메일을 보내야 하는 경우 SMTP 릴레이 서비스를 사용해야 합니다.
     - **클라우드 서비스 공급자**: 클라우드 서비스 공급자를 통해 Azure 리소스를 사용하는 고객은 해당 클라우드 서비스 공급자와 관련된 지원 사례를 만들고, 보안 SMTP 릴레이를 사용할 수 없는 경우 대신 공급자가 차단 해제 사례를 만들도록 요청할 수 있습니다.

  Azure에서 25 포트를 통해 전자 메일을 보낼 수 있도록 허용하는 경우 Microsoft는 전자 메일 공급자에서 가상 머신의 인바운드 전자 메일을 수락하도록 보장할 수 없습니다. 특정 공급자가 가상 머신의 메일을 거부하는 경우 메시지 배달 또는 스팸 필터링 문제를 해결하기 위해 공급자와 직접 작업하거나 인증된 SMTP 릴레이 서비스를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크에 배포할 수 있는 Azure 리소스와 여기에 연결 된 네트워크 보안 그룹에 대 한 자세한 내용은 [azure 서비스에 대 한 가상 네트워크 통합](virtual-network-for-azure-services.md) 을 참조 하세요.
* 네트워크 보안 그룹을 사용 하 여 트래픽을 평가 하는 방법에 대 한 자세한 내용은 [네트워크 보안 그룹의 작동 방식](network-security-group-how-it-works.md)을 참조 하세요.
* 네트워크 보안 그룹을 만들어 본 경험이 전혀 없는 경우 빠른 [자습서](tutorial-filter-network-traffic.md)를 완료하여 직접 경험해 볼 수 있습니다.
* 네트워크 보안 그룹에 익숙하고 네트워크 보안 그룹을 관리해야 하는 경우 [네트워크 보안 그룹 관리](manage-network-security-group.md)를 참조하세요. 
* 통신에 문제가 있고 네트워크 보안 그룹 문제를 해결해야 하는 경우 [가상 머신 네트워크 트래픽 필터 문제 진단](diagnose-network-traffic-filter-problem.md)을 참조하세요. 
* 네트워크 보안 [그룹 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 를 사용 하 여 연결 된 네트워크 보안 그룹이 있는 리소스와의 네트워크 트래픽을 분석 하는 방법에 대해 알아봅니다.
