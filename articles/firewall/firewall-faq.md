---
title: Azure Firewall FAQ
description: Azure Firewall에 대한 FAQ입니다. Azure Virtual Network 리소스를 보호하는 관리형 클라우드 기반 네트워크 보안 서비스입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 75435155ba1dad798d301006a30a5d5b6e96226a
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611180"
---
# <a name="azure-firewall-faq"></a>Azure Firewall FAQ

## <a name="what-is-azure-firewall"></a>Azure Firewall이란?

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure Firewall에서 어떤 기능이 지원되나요?

Azure Firewall 기능에 대해 알아보려면 [Azure Firewall 기능](features.md)을 참조하세요.

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall의 일반적인 배포 모델은 무엇입니까?

어떤 가상 네트워크에도 Azure Firewall을 배포할 수 있지만 고객은 일반적으로 중앙 가상 네트워크에 배포한 후, 허브 및 스포크 모델에서 다른 가상 네트워크를 중앙 가상 네트워크에 피어링합니다. 그 후 피어링된 가상 네트워크에서 이 중앙의 방화벽 가상 네트워크를 가리키도록 기본 경로를 설정할 수 있습니다. 글로벌 VNet 피어링은 지원되지만 지역 전반의 잠재적 성능 및 대기 시간 문제로 인해 권장되지 않습니다. 최상의 성능을 위해 지역당 하나의 방화벽을 배포합니다.

이 모델의 장점은 다양한 구독에 걸쳐 여러 스포크 VNET을 중앙집중적으로 제어하는 기능입니다. 또한 각 VNet에 별도로 방화벽을 배포할 필요가 없으므로 비용도 절감됩니다. 비용 절감은 고객 트래픽 패턴을 기반으로 연결 피어링 비용에 대해 측정해야 합니다.

## <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall을 설치하려면 어떻게 해야 하나요?

Azure Portal, PowerShell, REST API 또는 템플릿을 사용하여 Azure Firewall을 설정할 수 있습니다. [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)을 참조하세요.

## <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall의 몇 가지 개념을 알려주세요.

Azure Firewall은 규칙 및 규칙 컬렉션을 지원합니다. 규칙 컬렉션은 동일한 작업 및 우선 순위를 공유하는 규칙 집합입니다. 규칙 컬렉션은 우선 순위에 따라 실행됩니다. 네트워크 규칙 컬렉션이 애플리케이션 규칙 컬렉션보다 우선 순위가 높으며, 모든 규칙은 종료됩니다.

다음과 같은 세 가지 유형의 규칙 컬렉션이 있습니다.

* *애플리케이션 규칙*: 서브넷에서 액세스할 수 있는 FQDN(정규화된 도메인 이름)을 구성합니다.
* *네트워크 규칙*: 원본 주소, 프로토콜, 대상 포트 및 대상 주소를 포함하는 규칙을 구성합니다.
* *NAT 규칙*: 들어오는 인터넷 연결을 허용하도록 DNAT 규칙을 구성합니다.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall은 인바운드 트래픽 필터링을 지원하나요?

Azure Firewall은 인바운드 및 아웃바운드 필터링을 지원합니다. 인바운드 보호는 일반적으로 HTTP/S 이외의 프로토콜에 사용됩니다. 예를 들어 RDP, SSH 및 FTP 프로토콜이 이에 해당합니다. 최상의 인바운드 HTTP/S 보호를 위해 [Azure WAF(웹 애플리케이션 방화벽)](../web-application-firewall/overview.md)와 같은 웹 애플리케이션 방화벽을 사용합니다.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall에서는 어떤 로깅 및 분석 서비스를 지원하나요?

Azure Firewall은 방화벽 로그를 살펴보고 분석할 수 있도록 Azure Monitor와 통합됩니다. 로그를 Log Analytics, Azure Storage 또는 Event Hubs로 전송할 수 있습니다. 전송된 로그를 Log Analytics 또는 Excel이나 Power BI 같은 다른 도구에서 분석할 수 있습니다. 자세한 내용은 [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)을 참조하세요.

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall이 Marketplace의 NVA와 같은 기존 서비스와 어떻게 다르게 작동하나요?

Azure Firewall은 특정 고객 시나리오를 해결할 수 있는 기본 방화벽 서비스입니다. 타사 NVA와 Azure Firewall이 혼합되어 있을 것으로 예상됩니다. 함께 잘 작동하는 것이 가장 중요합니다.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF와 Azure Firewall의 차이점은 무엇인가요?

WAF(웹 애플리케이션 방화벽)는 일반적인 악용 및 취약점으로부터 웹 애플리케이션에 대해 중앙 집중식 인바운드 보호를 제공하는 Application Gateway의 기능입니다. Azure Firewall은 비HTTP/S 프로토콜(예: RDP, SSH, FTP)에 대해 인바운드 보호를 제공하고, 모든 포트 및 프로토콜에 아웃바운드 네트워크 수준 보호를 제공하고 아웃바운드 HTTP/S에 애플리케이션 수준 보호를 제공합니다.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>NSG(네트워크 보안 그룹)와 Azure Firewall의 차이점은 무엇입니까?

Azure Firewall 서비스는 네트워크 보안 그룹 기능을 보완합니다. 그뿐 아니라 더 나은 "심층 방어" 네트워크 보안을 제공합니다. 네트워크 보안 그룹은 각 구독의 가상 네트워크 내 리소스에 대한 트래픽을 제한하는 분산 네트워크 레이어 트래픽 필터링을 제공합니다. Azure Firewall은 상태를 저장하는 서비스 형태의 완전한 중앙 집중식 네트워크 방화벽으로, 다양한 구독 및 가상 네트워크에 네트워크 및 애플리케이션 수준의 보호를 제공합니다.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>AzureFirewallSubnet에서 NSG(네트워크 보안 그룹)가 지원되나요?

Azure Firewall은 NIC 수준 NSG(볼 수 없음)를 통한 플랫폼 보호를 포함하여 여러 보호 계층이 있는 관리되는 서비스입니다.  서브넷 수준 NSG는 AzureFirewallSubnet에 필요하지 않으며 서비스 중단 방지를 위해 사용하지 않도록 설정됩니다.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>서비스 엔드포인트를 사용하여 Azure Firewall을 설정하려면 어떻게 해야 하나요?

PaaS 서비스에 안전하게 액세스하려면 서비스 엔드포인트를 사용하는 것이 좋습니다. Azure Firewall 서브넷에서 서비스 엔드포인트를 사용하도록 설정하고, 연결된 스포크 가상 네트워크에서는 사용하지 않도록 설정하도록 선택할 수 있습니다. 이러한 방식으로 서비스 엔드포인트 보안과 모든 트래픽에 대한 중앙 로깅 기능의 이점을 모두 얻을 수 있습니다.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall의 가격 책정은 어떻게 되나요?

[Azure Firewall 가격 책정](https://azure.microsoft.com/pricing/details/azure-firewall/)을 참조하세요.

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall을 중지하려면 어떻게 하나요?

Azure PowerShell *할 당 취소* 및 *할당* 메서드를 사용할 수 있습니다.

다음은 그 예입니다.

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
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 원래 리소스 그룹 및 구독에 방화벽과 공용 IP를 다시 할당해야 합니다.

## <a name="what-are-the-known-service-limits"></a>알려진 서비스 제한 사항은 무엇입니까?

Azure Firewall 서비스 제한 사항은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)을 참조하세요.

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>허브 가상 네트워크의 Azure Firewall이 두 스포크 가상 네트워크 간의 네트워크 트래픽을 전달하고 필터링할 수 있나요?

예. 허브 가상 네트워크의 Azure Firewall을 사용하여 두 스포크 가상 네트워크 간의 트래픽을 라우팅하고 필터링할 수 있습니다. 이 시나리오가 적절하게 작동하려면 각각의 스포크 가상 네트워크 서브넷에 Azure Firewall을 기본 게이트웨이로 가리키는 UDR이 포함되어 있어야 합니다.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall이 동일한 가상 네트워크 또는 피어링된 가상 네트워크에 있는 서브넷 간에 네트워크 트래픽을 전달하고 필터링할 수 있나요?

예. 그러나 UDR을 동일한 VNET의 서브넷 간에 트래픽을 리디렉션하도록 구성하려면 좀 더 주의해야 합니다. VNET 주소 범위를 UDR의 대상 접두사로 사용하면 충분하지만 이렇게 하면 Azure Firewall 인스턴스를 통해 한 머신의 모든 트래픽이 동일한 서브넷으로 경로 설정되는 문제도 있습니다. 이 문제를 방지하려면 **VNET**의 다음 홉을 사용하여 UDR에 해당 서브넷의 경로를 포함시킵니다. 이러한 경로 관리는 번거롭고 오류가 발생하기 쉬울 수 있습니다. 내부 네트워크 조각화에 대해 UDR이 필요 없는 네트워크 보안 그룹을 사용하는 방법이 권장됩니다.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>Azure Firewall이 개인 네트워크 간에 SNAT를 아웃바운드하나요?

[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)에 따라 대상 IP 주소가 개인 IP 범위인 경우 Azure Firewall은 SNAT를 지원하지 않습니다. 조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 공용 IP 주소 범위를 SNAT하지 **않도록** Azure 방화벽을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>네트워크 가상 어플라이언스에 대한 강제 터널링/체이닝이 지원되나요?

강제 터널링은 새 방화벽을 만들 때 지원됩니다. 강제 터널링을 위해 기존 방화벽을 구성할 수는 없습니다. 자세한 내용은 [Azure Firewall 강제 터널링](forced-tunneling.md)을 참조하세요.

Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet이 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습하는 경우 이 경로를 직접 인터넷 연결을 유지하기 위해 **Internet**으로 설정된 **NextHopType** 값을 통해 0.0.0.0/0 UDR로 재정의해야 합니다.

구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요하고 인터넷 대상의 대상 IP 접두사를 확인할 수 있는 경우, AzureFirewallSubnet의 사용자 정의 경로를 통해 온-프레미스 네트워크를 사용하여 이러한 범위를 다음 홉으로 구성할 수 있습니다. 또는 BGP를 사용하여 이러한 경로를 정의할 수 있습니다.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>방화벽 리소스 그룹 제한 사항이 있나요?

예. 방화벽, VNet 및 공용 IP 주소는 모두 동일한 리소스 그룹에 있어야 합니다.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>인바운드 인터넷 네트워크 트래픽에 대해 DNAT를 구성할 때 해당 트래픽을 허용하도록 해당 네트워크 규칙을 구성해야 하나요?

아니요. NAT 규칙은 해당 네트워크 규칙을 암시적으로 추가하여 변환된 트래픽을 허용합니다. 변환된 트래픽을 일치시키는 거부 규칙을 사용하여 네트워크 규칙 컬렉션을 명시적으로 추가함으로써 이 동작을 재정의할 수 있습니다. Azure Firewall 규칙 처리 논리에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>와일드카드는 애플리케이션 규칙 대상 FQDN에서 어떻게 작동하나요?

와일드 카드는 현재 FQDN의 왼쪽에만 사용할 수 있습니다. 예: ***. contoso.com** 및 ***contoso.com**.

* **.contoso.com**을 구성하면 *anyvalue*.contoso.com은 허용되지만 contoso.com(도메인 정점)은 허용되지 않습니다. 도메인 정점을 허용하려면 대상 FQDN으로 명시적으로 구성해야 합니다.

## <a name="what-does-provisioning-state-failed-mean"></a>*프로비저닝 상태: 실패*는 무엇을 의미하나요?

구성 변경이 적용될 때마다 Azure Firewall은 모든 기본 백 엔드 인스턴스를 업데이트하려고 합니다. 드물지만, 이러한 백 엔드 인스턴스 중 하나가 새 구성으로 업데이트되지 않고 업데이트 프로세스가 프로비저닝 실패 상태로 중지되는 경우가 있습니다. Azure Firewall은 여전히 작동하지만 적용된 구성이 “일부 인스턴스는 이전 구성을 갖고 있고, 다른 인스턴스를 업데이트된 규칙 집합을 갖고 있는”일관성이 없는 상태일 수 있습니다. 이 경우 작업이 성공하고 방화벽이 *성공* 프로비저닝 상태가 될 때까지 구성을 한 번 더 업데이트해 봅니다.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure Firewall은 계획된 유지 관리와 계획되지 않은 오류를 어떻게 처리하나요?
Azure Firewall은 활성-활성 구성의 여러 백 엔드 노드로 구성됩니다.  계획된 유지 관리를 위해 노드를 정상적으로 업데이트하는 연결 드레이닝 논리가 있습니다.  각 Azure 지역의 업무 외 시간 동안 업데이트가 계획되어 중단 위험을 더 줄입니다.  계획되지 않은 문제의 경우 새 노드를 인스턴스화하여 오류가 발생한 노드를 대체합니다.  새 노드에 대한 연결은 대개 오류 발생 시점부터 10초 이내에 다시 설정됩니다.

## <a name="how-does-connection-draining-work"></a>연결 드레이닝은 어떻게 작동하나요?

계획된 유지 관리를 위해 연결 드레이닝 논리가 백 엔드 노드를 정상적으로 업데이트합니다. Azure Firewall은 기존 연결이 종료될 때까지 90초 동안 대기 합니다. 필요한 경우 클라이언트는 자동으로 다른 백 엔드 노드에 대한 연결을 다시 설정할 수 있습니다.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>방화벽 이름에 대한 문자 제한이 있나요?

예. 방화벽 이름에는 50자 제한이 있습니다.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Azure Firewall에 /26 서브넷 크기가 필요한 이유는 무엇인가요?

Azure Firewall은 규모에 따라 더 많은 가상 머신 인스턴스를 프로비저닝해야 합니다. /26 주소 공간을 통해 방화벽이 크기 조정을 수용할 수 있는 충분한 IP 주소를 갖게 됩니다.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>서비스 확장에 따라 방화벽 서브넷 크기 변경이 필요한가요?

아니요. Azure Firewall에는 /26보다 큰 서브넷이 필요하지 않습니다.

## <a name="how-can-i-increase-my-firewall-throughput"></a>방화벽 처리량을 늘리려면 어떻게 해야 하나요?

Azure Firewall의 초기 처리량 용량은 2.5 ~ 3Gbps이며 30Gbps까지 스케일 아웃됩니다. CPU 사용량과 처리량에 따라 자동으로 스케일 아웃됩니다.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure Firewall을 스케일 아웃하는 데 얼마나 걸리나요?

평균 처리량 또는 CPU 사용량이 60%일 때 Azure Firewall이 점차적으로 스케일링됩니다. 기본 배포 최대 처리량은 약 2.5-3gbps 이며 해당 숫자의 60%에 도달 하면 규모 확장을 시작 합니다. 스케일 아웃에는 5 ~ 7분 정도 걸립니다. 

성능 테스트를 수행 하는 경우 10 ~ 15 분 이상 테스트 하 고 새로 만든 방화벽 노드를 활용 하려면 새 연결을 시작 해야 합니다.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>Azure Firewall에서 기본적으로 Active Directory에 대한 액세스를 허용하나요?

아니요. Azure Firewall은 기본적으로 Active Directory 액세스를 차단합니다. 액세스를 허용하려면 AzureActiveDirectory 서비스 태그를 구성합니다. 자세한 내용은 [Azure Firewall 서비스 태그](service-tags.md)를 참조하세요.

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall 위협 인텔리전스 기반 필터링에서 FQDN 또는 IP 주소를 제외할 수 있나요?

예, Microsoft Azure PowerShell을 통해 가능합니다.

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Azure Firewall의 규칙에서 해당 트래픽을 허용하지 않는 경우에도 TCP ping 및 유사한 도구가 대상 FQDN에 성공적으로 연결할 수 있는 이유는 무엇인가요?

TCP ping은 실제로는 대상 FQDN에 연결하지 않습니다. 이러한 상황이 발생하는 이유는 Azure Firewall의 투명 프록시가 아웃바운드 트래픽에 대해 포트 80/443에서 수신 대기하기 때문입니다. TCP ping은 방화벽과의 연결을 설정한 다음, 패킷을 삭제하고 연결을 기록합니다. 이 동작은 보안에 영향을 주지 않습니다. 그러나 혼동을 피하기 위해 이 동작에 대한 잠재적인 변경 내용을 조사하고 있습니다.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>IP 그룹에서 지원되는 IP 주소 수에 제한이 있나요?

예. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)을 참조하세요.

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>IP 그룹을 다른 리소스 그룹으로 이동할 수 있나요?

아니요, IP 그룹을 다른 리소스 그룹으로 이동 하는 것은 현재 지원 되지 않습니다.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Azure 방화벽에 대 한 TCP 유휴 시간 제한은 무엇 인가요?

네트워크 방화벽의 표준 동작은 TCP 연결을 활성 상태로 유지 하 고 작업이 없는 경우 즉시 종료 하는 것입니다. Azure 방화벽 TCP 유휴 시간 제한은 4 분입니다. 이 설정은 구성할 수 없습니다. 비활성 기간이 시간 제한 값 보다 길면 TCP 또는 HTTP 세션이 유지 되지 않을 수 있습니다. 일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. 이 방법은 더 오랜 기간 동안 연결을 활성 상태로 유지합니다. 자세한 내용은 [.net 예제](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_)를 참조 하십시오.

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>공용 IP 주소 없이 Azure 방화벽을 배포할 수 있나요?

아니요, 현재 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포 해야 합니다.

## <a name="where-does-azure-firewall-store-customer-data"></a>Azure 방화벽은 고객 데이터를 저장 하는 위치

Azure 방화벽은 고객 데이터를 배포 된 지역 외부로 이동 하거나 저장 하지 않습니다.
