---
title: Azure Firewall FAQ
description: Azure 방화벽에 대한 FAQ입니다. Azure Virtual Network 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/29/2020
ms.author: victorh
ms.openlocfilehash: 8b55f31f12ab1057ac2e0f625a0285b6518cc44a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845779"
---
# <a name="azure-firewall-faq"></a>Azure Firewall FAQ

## <a name="what-is-azure-firewall"></a>Azure Firewall이란?

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure Firewall에서 어떤 기능이 지원되나요?

* 서비스로서의 상태 저장 방화벽
* 무제한 클라우드 확장성이 포함된 기본 제공 고가용성
* FQDN 필터링
* FQDN 태그
* 네트워크 트래픽 필터링 규칙
* 아웃바운드 SNAT 지원
* 인바운드 DNAT 지원
* Azure 구독 및 VNET 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고 적용하고 기록
* 로깅 및 분석을 위한 Azure Monitor와 완전히 통합됨

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall의 일반적인 배포 모델은 무엇입니까?

어떤 가상 네트워크에도 Azure Firewall을 배포할 수 있지만 고객은 일반적으로 중앙 가상 네트워크에 배포한 후, 허브 및 스포크 모델에서 다른 가상 네트워크를 중앙 가상 네트워크에 피어링합니다. 그 후 피어링된 가상 네트워크에서 이 중앙의 방화벽 가상 네트워크를 가리키도록 기본 경로를 설정할 수 있습니다. 글로벌 VNet 피어 링은 지원 되지만 지역 전반의 잠재적 성능 및 대기 시간 문제로 인해 권장 되지 않습니다. 최상의 성능을 위해 지역 당 하나의 방화벽을 배포 합니다.

이 모델의 장점은 다양한 구독에 걸쳐 여러 스포크 VNET을 중앙집중적으로 제어하는 기능입니다. 또한 각 VNet에 별도로 방화벽을 배포할 필요가 없으므로 비용도 절감됩니다. 비용 절감은 고객 트래픽 패턴을 기반으로 연결 피어링 비용에 대해 측정해야 합니다.

## <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall을 설치하려면 어떻게 해야 하나요?

Azure Portal, PowerShell, REST API 또는 템플릿을 사용하여 Azure Firewall을 설정할 수 있습니다. 단계별 지침은 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.

## <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall의 몇 가지 개념을 알려주세요.

Azure Firewall은 규칙 및 규칙 컬렉션을 지원합니다. 규칙 컬렉션은 동일한 작업 및 우선 순위를 공유하는 규칙 집합입니다. 규칙 컬렉션은 우선 순위에 따라 실행됩니다. 네트워크 규칙 컬렉션이 애플리케이션 규칙 컬렉션보다 우선 순위가 높으며, 모든 규칙은 종료됩니다.

규칙 컬렉션에는 다음 세 가지 유형이 있습니다.

* *응용 프로그램 규칙*: 서브넷에서 액세스할 수 있는 fqdn (정규화 된 도메인 이름)을 구성 합니다.
* *네트워크 규칙*: 원본 주소, 프로토콜, 대상 포트 및 대상 주소를 포함 하는 규칙을 구성 합니다.
* *NAT 규칙*: 들어오는 연결을 허용 하도록 dnat 규칙을 구성 합니다.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall은 인바운드 트래픽 필터링을 지원하나요?

Azure Firewall은 인바운드 및 아웃바운드 필터링을 지원합니다. 인바운드 보호는 일반적으로 비 HTTP/S 프로토콜에 사용 됩니다. 예를 들어 RDP, SSH 및 FTP 프로토콜이 이에 해당합니다. 최적의 인바운드 HTTP/S 보호를 사용 하려면 [Azure 애플리케이션 게이트웨이에서 Azure 웹 응용 프로그램 방화벽과](../web-application-firewall/ag/ag-overview.md)같은 웹 응용 프로그램 방화벽을 사용 합니다.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall에서는 어떤 로깅 및 분석 서비스를 지원하나요?

Azure Firewall은 방화벽 로그를 살펴보고 분석할 수 있도록 Azure Monitor와 통합됩니다. 로그를 Log Analytics, Azure Storage 또는 Event Hubs로 전송할 수 있습니다. 전송된 로그를 Log Analytics 또는 Excel이나 Power BI 같은 다른 도구에서 분석할 수 있습니다. 자세한 내용은 [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)을 참조하세요.

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall이 Marketplace의 NVA와 같은 기존 서비스와 어떻게 다르게 작동하나요?

Azure Firewall은 특정 고객 시나리오를 해결할 수 있는 기본 방화벽 서비스입니다. 타사 Nva 및 Azure 방화벽을 혼합 하 여 사용할 것으로 예상 됩니다. 함께 잘 작동하는 것이 가장 중요합니다.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF와 Azure Firewall의 차이점은 무엇인가요?

WAF(웹 애플리케이션 방화벽)는 일반적인 악용 및 취약점으로부터 웹 애플리케이션에 대해 중앙 집중식 인바운드 보호를 제공하는 Application Gateway의 기능입니다. Azure Firewall은 비HTTP/S 프로토콜(예: RDP, SSH, FTP)에 대해 인바운드 보호를 제공하고, 모든 포트 및 프로토콜에 아웃바운드 네트워크 수준 보호를 제공하고 아웃바운드 HTTP/S에 애플리케이션 수준 보호를 제공합니다.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>NSG(네트워크 보안 그룹)와 Azure Firewall의 차이점은 무엇입니까?

Azure Firewall 서비스는 네트워크 보안 그룹 기능을 보완합니다. 그뿐 아니라 더 나은 "심층 방어" 네트워크 보안을 제공합니다. 네트워크 보안 그룹은 각 구독의 가상 네트워크 내 리소스에 대한 트래픽을 제한하는 분산 네트워크 레이어 트래픽 필터링을 제공합니다. Azure Firewall은 상태를 저장하는 서비스 형태의 완전한 중앙 집중식 네트워크 방화벽으로, 다양한 구독 및 가상 네트워크에 네트워크 및 애플리케이션 수준의 보호를 제공합니다.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Azure 방화벽 서브넷에서 NSGs (네트워크 보안 그룹)가 지원 되나요?

Azure 방화벽은 NIC 수준 NSGs (볼 수 없음)를 사용 하는 플랫폼 보호를 비롯 한 여러 보호 계층을 포함 하는 관리 되는 서비스입니다.  서브넷 수준 NSGs는 Azure 방화벽 서브넷에서 필요 하지 않으며 서비스 중단을 방지 하기 위해 사용 하지 않도록 설정 됩니다.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>서비스 엔드포인트를 사용하여 Azure Firewall을 설정하려면 어떻게 해야 하나요?

PaaS 서비스에 안전하게 액세스하려면 서비스 엔드포인트를 사용하는 것이 좋습니다. Azure Firewall 서브넷에서 서비스 엔드포인트를 사용하도록 설정하고, 연결된 스포크 가상 네트워크에서는 사용하지 않도록 설정하도록 선택할 수 있습니다. 이러한 방식으로 서비스 엔드포인트 보안과 모든 트래픽에 대한 중앙 로깅 기능의 이점을 모두 얻을 수 있습니다.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall의 가격 책정은 어떻게 되나요?

[Azure 방화벽 가격](https://azure.microsoft.com/pricing/details/azure-firewall/)을 참조 하세요.

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall을 중지하려면 어떻게 하나요?

Azure PowerShell *할 당 취소* 및 *할당* 메서드를 사용할 수 있습니다.

예:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 원래 리소스 그룹 및 구독에 방화벽과 공용 IP를 다시 할당해야 합니다.

## <a name="what-are-the-known-service-limits"></a>알려진 서비스 제한 사항은 무엇입니까?

Azure 방화벽 서비스 제한의 경우 [azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)을 참조 하세요.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>허브 가상 네트워크의 Azure Firewall이 두 스포크 가상 네트워크 간의 네트워크 트래픽을 전달하고 필터링할 수 있나요?

예. 허브 가상 네트워크의 Azure Firewall을 사용하여 두 스포크 가상 네트워크 간의 트래픽을 라우팅하고 필터링할 수 있습니다. 이 시나리오가 적절하게 작동하려면 각각의 스포크 가상 네트워크 서브넷에 Azure Firewall을 기본 게이트웨이로 가리키는 UDR이 포함되어 있어야 합니다.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall이 동일한 가상 네트워크 또는 피어링된 가상 네트워크에 있는 서브넷 간에 네트워크 트래픽을 전달하고 필터링할 수 있나요?

예. 그러나 UDRs를 구성 하 여 동일한 VNET의 서브넷 간에 트래픽을 리디렉션하도록 하려면 추가 주의가 필요 합니다. VNET 주소 범위를 UDR의 대상 접두사로 사용하면 충분하지만 이렇게 하면 Azure Firewall 인스턴스를 통해 한 머신의 모든 트래픽이 동일한 서브넷으로 경로 설정되는 문제도 있습니다. 이 문제를 방지하려면 **VNET**의 다음 홉을 사용하여 UDR에 해당 서브넷의 경로를 포함시킵니다. 이러한 경로 관리는 번거롭고 오류가 발생하기 쉬울 수 있습니다. 내부 네트워크 조각화에 대해 UDR이 필요 없는 네트워크 보안 그룹을 사용하는 방법이 권장됩니다.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>개인 네트워크 간에 Azure 방화벽 아웃 바운드 SNAT

Azure 방화벽은 대상 IP 주소가 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)당 개인 ip 범위인 경우 SNAT 하지 않습니다. 조직에서 개인 네트워크에 대한 공용 IP 주소 범위를 사용 하는 경우 Azure 방화벽은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나로 트래픽을 SNATs 합니다.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>강제 터널링/네트워크 가상 어플라이언스에 대한 연결이 지원 되나요?

강제 터널링은 현재 지원 되지 않습니다. Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet이 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습하는 경우 이 경로를 직접 인터넷 연결을 유지하기 위해 **Internet**으로 설정된 **NextHopType** 값을 통해 0.0.0.0/0 UDR로 재정의해야 합니다.

구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요하고 인터넷 대상의 대상 IP 접두사를 확인할 수 있는 경우, AzureFirewallSubnet의 사용자 정의 경로를 통해 온-프레미스 네트워크를 사용하여 이러한 범위를 다음 홉으로 구성할 수 있습니다. 또는 BGP를 사용하여 이러한 경로를 정의할 수 있습니다.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>방화벽 리소스 그룹 제한 사항이 있나요?

예. 방화벽, 서브넷, VNet 및 공용 IP 주소는 모두 동일한 리소스 그룹에 있어야 합니다.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>인바운드 네트워크 트래픽에 대해 DNAT를 구성할 때 해당 트래픽을 허용하도록 해당 네트워크 규칙을 구성해야 하나요?

아닙니다. NAT 규칙은 해당 네트워크 규칙을 암시적으로 추가하여 변환된 트래픽을 허용합니다. 변환된 트래픽을 일치시키는 거부 규칙을 사용하여 네트워크 규칙 컬렉션을 명시적으로 추가함으로써 이 동작을 재정의할 수 있습니다. Azure Firewall 규칙 처리 논리에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>응용 프로그램 규칙 대상 FQDN에서 와일드 카드를 사용 하는 방법

* **. Contoso.com**을 구성 하는 경우 contoso.com (도메인 apex)가 아닌 *anyvalue*. contoso.com가 허용 됩니다. 도메인 apex을 허용 하려면 대상 FQDN으로 명시적으로 구성 해야 합니다.

## <a name="what-does-provisioning-state-failed-mean"></a>*프로 비전 상태: 실패* 평균 이란?

구성 변경이 적용 될 때마다 Azure 방화벽은 모든 기본 백 엔드 인스턴스를 업데이트 하려고 합니다. 드문 경우 지만 이러한 백 엔드 인스턴스 중 하나가 새 구성으로 업데이트 되지 않고 업데이트 프로세스가 실패 한 프로 비전 상태로 중지 될 수 있습니다. Azure 방화벽은 여전히 작동 하지만 적용 된 구성은 일관 되지 않은 상태일 수 있습니다 .이 경우 일부 인스턴스에는 업데이트 된 규칙 집합이 있는 이전 구성이 있습니다. 이 문제가 발생 하는 경우 작업이 성공 하 고 방화벽이 *성공한* 프로 비전 상태에 도달할 때까지 구성을 한 번 더 업데이트 해 보세요.

### <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure 방화벽은 계획 된 유지 관리 및 계획 되지 않은 오류를 어떻게 처리 하나요?
Azure 방화벽은 활성-활성 구성의 여러 백 엔드 노드로 구성 됩니다.  계획 된 유지 관리를 위해 노드를 정상적으로 업데이트 하는 연결 드레이닝 논리가 있습니다.  업무 외 시간에는 각 Azure 지역에 대한 업무 외 시간에 업데이트를 계획 하 여 중단 위험을 추가로 제한할 수 있습니다.  계획 되지 않은 문제의 경우에는 새 노드를 인스턴스화하여 오류가 발생 한 노드를 대체 합니다.  새 노드에 대한 연결은 일반적으로 오류가 발생 한 시간부터 10 초 이내에 다시 설정 됩니다.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>방화벽 이름에 대한 문자 제한이 있나요?

예. 방화벽 이름에는 50 자 제한이 있습니다.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Azure 방화벽에/26 서브넷 크기가 필요한 이유는 무엇 인가요?

Azure 방화벽은 규모에 따라 더 많은 가상 머신 인스턴스를 프로 비전 해야 합니다. /26 주소 공간을 통해 방화벽에 크기 조정을 수용할 수 있는 충분 한 IP 주소가 있습니다.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>서비스가 확장 됨에 따라 방화벽 서브넷 크기를 변경 해야 하나요?

아닙니다. Azure 방화벽에는/26 보다 큰 서브넷이 필요 하지 않습니다.

## <a name="how-can-i-increase-my-firewall-throughput"></a>방화벽 처리량을 늘리려면 어떻게 해야 하나요?

Azure 방화벽의 초기 처리량 용량은 2.5-3gbps입니다. 현재 scale out은 CPU 사용량만을 기준으로 합니다. 네트워크 규칙을 사용 하는 방화벽은 CPU 사용에 심각한 영향을 주지 않으므로 처리량을 높이도록 확장할 수 없는 경우도 있습니다. 방화벽에 더 높은 처리량이 필요한 경우 지원에 문의 하 여 방화벽의 초기 처리량 용량을 늘리세요.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure 방화벽을 확장 하는 데 얼마나 걸립니까?

현재 Azure 방화벽을 확장 하는 데 5 ~ 7 분이 걸립니다. 더 빠른 자동 크기 조정을 필요로 하는 버스트가 있는 경우 지원에 문의 하 여 방화벽의 초기 처리량 용량을 늘리세요.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Azure 방화벽에서 기본적으로 Active Directory에 대한 액세스를 허용 하나요?

아닙니다. Azure 방화벽은 기본적으로 Active Directory 액세스를 차단 합니다. 액세스를 허용 하려면 AzureActiveDirectory service 태그를 구성 합니다. 자세한 내용은 [Azure 방화벽 서비스 태그](service-tags.md)를 참조 하세요.
