---
title: ISE를 사용하여 Azure 가상 네트워크에 연결
description: Azure Logic Apps에서 Azure VNET(가상 네트워크)에 액세스할 수 있는 ISE(통합 서비스 환경) 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 624668ad80d72933d6dd1e67fcac799fd210d659
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816663"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. ISE는 "글로벌" 다중 테넌트 Logic Apps 서비스와 별도로 유지되는 전용 스토리지 및 기타 리소스를 사용하는 전용 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. 또한 ISE는 자체 고정 IP 주소를 제공합니다. 이러한 IP 주소는 공용 다중 테넌트 서비스에서 논리 앱이 공유하는 고정 IP 주소와는 별개입니다.

ISE를 만들면 Azure는 ISE를 Azure 가상 네트워크에 삽입한 다음, Logic Apps 서비스를 가상 네트워크에 배포합니다. 논리 앱 또는 통합 계정을 만들 때 ISE를 해당 위치로 선택합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동하려면 둘 다 각자 위치와 동일한 ISE를 사용해야 합니다.

ISE는 실행 지속 시간, 스토리지 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대한 제한이 증가합니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. ISE에 대해 자세히 알아보려면 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조하세요.

이 문서에서는 Azure Portal을 사용하여 이러한 작업을 완료하는 방법을 보여줍니다.

* ISE에 대한 액세스가 가능하도록 설정합니다.
* ISE를 만듭니다.
* ISE에 용량을 더 추가합니다.

ISE는 [샘플 Azure Resource Manager 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)을 사용하거나 고객 관리형 키 설정을 포함하여 Logic Apps REST API를 사용하여 만들 수도 있습니다.

* [Logic Apps REST API를 사용하여 ISE(통합 서비스 환경) 만들기](../logic-apps/create-integration-service-environment-rest-api.md)
* [고객 관리형 키를 설정하여 ISE의 미사용 데이터 암호화](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

  > [!IMPORTANT]
  > ISE에서 실행되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 사용량 기반 가격 책정 플랜과 다른 가격 책정 플랜을 사용합니다. ISE의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조하세요. 가격 책정 요금은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조하세요.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다. 가상 네트워크에는 ISE에서 리소스를 만들고 배포 하는 데 필요한 4 개의 *빈* 서브넷이 있어야 하 고, 커넥터 및 성능 캐싱과 같은 내부 Logic Apps 구성 요소에서 사용 됩니다. 서브넷을 미리 만들 수도 있고, 나중에 서브넷을 만들 수 있도록 ISE를 만들 때까지 기다릴 수도 있습니다. 그러나 서브넷을 만들기 전에 [서브넷 요구 사항을](#create-subnet)검토 합니다.

  > [!IMPORTANT]
  >
  > Azure Logic Apps에서 확인할 수 없기 때문에 가상 네트워크 또는 서브넷에 대해 다음 IP 주소 공간을 사용 하지 마세요.<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * ISE가 제대로 작동하고 액세스 가능한 상태를 유지할 수 있도록 가상 네트워크가 [ISE에 액세스할 수 있는지](#enable-access) 확인해야 합니다.

  * 를 사용 하거나 [강제 터널링](../firewall/forced-tunneling.md)과 함께 [express](../expressroute/expressroute-introduction.md) 경로를 사용 하려면 다음과 같은 특정 경로를 사용 하 여 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) ISE에서 사용 하는 각 서브넷에 경로 테이블을 연결 해야 합니다.

    **이름**: <*경로-이름*><br>
    **주소 접두사**: 0.0.0.0/0<br>
    **다음 홉**: 인터넷
    
    Logic Apps 구성 요소가 Azure Storage 및 Azure SQL DB와 같은 다른 종속 Azure 서비스와 통신할 수 있도록이 특정 경로 테이블이 필요 합니다. 이 경로에 대 한 자세한 내용은 [0.0.0.0/0 주소 접두사](../virtual-network/virtual-networks-udr-overview.md#default-route)를 참조 하세요. Express 경로를 사용 하 여 강제 터널링을 사용 하지 않는 경우이 특정 경로 테이블이 필요 하지 않습니다.
    
    Express 경로를 사용 하면 온-프레미스 네트워크를 Microsoft 클라우드로 확장 하 고 연결 공급자가 촉진 하는 개인 연결을 통해 Microsoft 클라우드 서비스에 연결할 수 있습니다. 특히 Express 경로는 공용 인터넷을 통하지 않고 개인 네트워크를 통해 트래픽을 라우팅하는 가상 개인 네트워크입니다. 논리 앱은 Express 경로 또는 가상 개인 네트워크를 통해 연결할 때 동일한 가상 네트워크에 있는 온-프레미스 리소스에 연결할 수 있습니다.
   
  * [NVA (네트워크 가상 어플라이언스)](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 사용 하는 경우 TLS/ssl 종료를 사용 하도록 설정 하거나 아웃 바운드 TLS/ssl 트래픽을 변경 하지 않도록 해야 합니다. 또한 ISE의 서브넷에서 발생 하는 트래픽에 대 한 검사를 사용 하지 않도록 설정 해야 합니다. 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조 하세요.

  * Azure 가상 네트워크에 사용자 지정 DNS 서버를 사용하려면 ISE를 가상 네트워크에 배포하기 전에 [다음 단계에 따라 해당 서버를 설정](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)합니다. DNS 서버 설정 관리에 대한 자세한 내용은 [가상 네트워크 만들기, 변경 또는 삭제](../virtual-network/manage-virtual-network.md#change-dns-servers)를 참조하세요.

    > [!NOTE]
    > DNS 서버 또는 DNS 서버 설정을 변경 하는 경우 ISE에서 해당 변경 내용을 선택할 수 있도록 ISE를 다시 시작 해야 합니다. 자세한 내용은 [ISE 다시 시작](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)을 참조하세요.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE에 대한 액세스가 가능하도록 설정

Azure 가상 네트워크에서 ISE를 사용할 때 일반적인 설정 문제는 차단된 포트가 하나 이상 있는 경우입니다. ISE와 대상 시스템 간의 연결을 만드는 데 사용하는 커넥터에 자체 포트 요구 사항이 있을 수도 있습니다. 예를 들어, FTP 커넥터를 사용하여 FTP 시스템과 통신하는 경우에는 FTP 시스템에서 사용하는 포트(예: 명령 전송을 위한 포트 21)를 사용할 수 있어야 합니다.

ISE가 액세스할 수 있고 ISE의 논리 앱이 가상 네트워크의 각 서브넷에서 통신할 수 있도록 하려면 [각 서브넷에 대해 이 테이블에 설명된 포트를 엽니다](#network-ports-for-ise). 필요한 포트를 사용할 수 없으면 ISE가 제대로 작동하지 않습니다.

* IP 제한이 있는 다른 엔드포인트에 액세스해야 하는 ISE 인스턴스가 여러 개 있으면, [Azure Firewall](../firewall/overview.md) 또는 [네트워크 가상 어플라이언스](../virtual-network/virtual-networks-overview.md#filter-network-traffic)를 가상 네트워크에 배포하고 이 방화벽 또는 네트워크 가상 어플라이언스를 통해 아웃바운드 트래픽을 라우팅합니다. 그런 다음, 가상 네트워크의 모든 ISE 인스턴스가 대상 시스템과 통신하는 데 사용할 수 있는 [단일, 아웃바운드, 공용, 고정 및 예측 가능한 IP 주소를 설정](connect-virtual-network-vnet-set-up-single-ip-address.md)할 수 있습니다. 이런 방식으로 각 ISE에 대한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

   > [!NOTE]
   > 시나리오에서 액세스가 필요한 IP 주소 수를 제한해야 하는 경우 단일 ISE에 이 방법을 사용할 수 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대한 추가 비용이 시나리오에 적합한지 고려하십시오. [Azure Firewall 가격 책정](https://azure.microsoft.com/pricing/details/azure-firewall/)에 대해 자세히 알아보세요.

* 제약 조건 없이 새 Azure 가상 네트워크 및 서브넷을 만든 경우, 서브넷 전체의 트래픽을 제어하기 위해 가상 네트워크에 [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md#network-security-groups)를 설정할 필요가 없습니다.

* 기존 가상 네트워크의 경우 선택적으로 [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md#network-security-groups)를 설정하여 [서브넷 전체에서 네트워크 트래픽을 필터링](../virtual-network/tutorial-filter-network-traffic.md)할 수 있습니다. 이 경로로 이동하거나 이미 NSG를 사용하는 경우에는 해당 NSG에 대해 [이 표에 설명되어 있는 포트를 열어야](#network-ports-for-ise)합니다.

  [NSG 보안 규칙](../virtual-network/security-overview.md#security-rules)을 설정하는 경우 **TCP**와 **UDP** 프로토콜을 모두 사용해야 합니다. 그렇지 않으면 각 프로토콜에 대해 별도의 규칙을 만들 필요가 없도록 **임의**를 선택합니다. NSG 보안 규칙은 포트에 액세스해야 하는 IP 주소에 대해 열어야 하는 해당 포트를 설명합니다. 이러한 엔드포인트 사이에 존재하는 방화벽, 라우터 또는 기타 항목도 해당 포트가 IP 주소에 액세스할 수 있도록 유지해야 합니다.

* 방화벽을 통해 강제 터널링을 설정 하 여 인터넷 바인딩된 트래픽을 리디렉션하는 경우 [추가 강제 터널링 요구 사항을](#forced-tunneling)검토 합니다.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE에 사용되는 네트워크 포트

이 표는 ISE가 액세스할 수 있어야 하는 포트와 해당 포트의 용도를 설명합니다. 보안 규칙을 설정할 때 복잡성을 줄이기 위해 이 표에는 특정 Azure 서비스에 대한 IP 주소 접두사 그룹을 나타내는 [서비스 태그](../virtual-network/service-tags-overview.md)가 사용되었습니다. 언급된 곳에서, 내부 ISE 및 외부 ISE는 [ISE 생성 중에 선택된 액세스 엔드포인트](connect-virtual-network-vnet-isolated-environment.md#create-environment)를 나타냅니다.  자세한 내용은 [엔드포인트 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조하세요.

> [!IMPORTANT]
> 모든 규칙에 대해 원본 포트를 `*`로 설정해야 합니다. 원본 포트는 사용 후 삭제되기 때문입니다.

#### <a name="inbound-security-rules"></a>인바운드 보안 규칙

| 목적 | 원본 서비스 태그 또는 IP 주소 | 원본 포트 | 대상 서비스 태그 또는 IP 주소 | 대상 포트 | 메모 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 가상 네트워크 내에서 서브넷 간 통신 | ISE 서브넷이 있는 가상 네트워크의 주소 공간 | * | ISE 서브넷이 있는 가상 네트워크의 주소 공간 | * | 가상 네트워크의 서브넷 간에 트래픽이 흐르는 데 필요합니다. <p><p>**중요**: 각 서브넷의 구성 요소 간에 트래픽이 흐르려면 각 서브넷 내에서 모든 포트를 열어야 합니다. |
| 둘 다: <p>논리 앱으로 향하는 통신 <p><p>논리 앱의 실행 기록| 내부 ISE: <br>**VirtualNetwork** <p><p>외부 ISE: **Internet** 또는 **메모** 참조 | * | **VirtualNetwork** | 443 | **Internet** 서비스 태그를 사용하는 대신 다음 항목에 대한 소스 IP 주소를 지정할 수 있습니다. <p><p>- 논리 앱에서 요청 트리거 또는 웹후크를 호출하는 컴퓨터 또는 서비스 <p>- 로직 앱 실행 기록에 액세스하려는 컴퓨터 또는 서비스 <p><p>**중요**: 이 포트를 닫거나 차단하면 요청 트리거 또는 웹후크가 있는 논리 앱을 호출할 수 없습니다. 실행 기록의 각 단계에 대한 입력 및 출력에도 액세스 할 수 없습니다. 단, 논리 앱 실행 기록에는 액세스할 수 있습니다.|
| Logic Apps 디자이너 - 동적 속성 | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Logic Apps에서 오는 요청은 해당 지역에 대한 엔드포인트의 [인바운드 IP 주소](../logic-apps/logic-apps-limits-and-config.md#inbound)에 액세스합니다. |
| 커넥터 배포 | **AzureConnectors** | * | **VirtualNetwork** | 454 | 커넥터를 배포하고 업데이트하는 데 필요합니다. 이 포트를 닫거나 차단하면 ISE 배포가 실패하고 커넥터를 업데이트 및 수정할 수 없습니다. |
| 네트워크 상태 검사 | **LogicApps** | * | **VirtualNetwork** | 454 | Logic Apps에서 오는 요청은 해당 지역에 대한 엔드포인트의 [인바운드 IP 주소](../logic-apps/logic-apps-limits-and-config.md#inbound)와 [아웃바운드 IP 주소](../logic-apps/logic-apps-limits-and-config.md#outbound)에 액세스합니다. |
| App Service 관리 종속성 | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Azure Traffic Manager에서 오는 통신 | **AzureTrafficManager** | * | **VirtualNetwork** | 내부 ISE: 454 <p><p>외부 ISE: 443 ||
| 둘 다: <p>커넥터 정책 배포 <p>API Management - 관리 엔드포인트 | **APIManagement** | * | **VirtualNetwork** | 3443 | 커넥터 정책 배포의 경우 커넥터를 배포하고 업데이트하려면 포트 액세스가 필요합니다. 이 포트를 닫거나 차단하면 ISE 배포가 실패하고 커넥터를 업데이트 및 수정할 수 없습니다. |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383 및 **메모** 참조| ISE가 Azure Cache for Redis와 작동하려면 [Azure Cache for Redis FAQ 설명에 있는 아웃바운드 및 인바운드 포트](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)를 열어야 합니다. |
|||||||

#### <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| 목적 | 원본 서비스 태그 또는 IP 주소 | 원본 포트 | 대상 서비스 태그 또는 IP 주소 | 대상 포트 | 메모 |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| 가상 네트워크 내에서 서브넷 간 통신 | ISE 서브넷이 있는 가상 네트워크의 주소 공간 | * | ISE 서브넷이 있는 가상 네트워크의 주소 공간 | * | 가상 네트워크의 서브넷 간에 트래픽이 흐르는 데 필요합니다. <p><p>**중요**: 각 서브넷의 구성 요소 간에 트래픽이 흐르려면 각 서브넷 내에서 모든 포트를 열어야 합니다. |
| 논리 앱에서 오는 통신 | **VirtualNetwork** | * | 대상에 따라 다름 | 80, 443 | 대상은 논리 앱이 통신해야 하는 외부 서비스의 엔드포인트에 따라 다릅니다. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage 종속성 | **VirtualNetwork** | * | **스토리지** | 80, 443, 445 ||
| 연결 관리 | **VirtualNetwork** | * | **AppService** | 443 ||
| 진단 로그 및 메트릭 게시 | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL 종속성 | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | 상태를 Resource Health에 게시하는 데 필요합니다. |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | **VirtualNetwork** | * | **EventHub** | 5672 ||
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383 및 **메모** 참조| ISE가 Azure Cache for Redis와 작동하려면 [Azure Cache for Redis FAQ 설명에 있는 아웃바운드 및 인바운드 포트](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)를 열어야 합니다. |
| DNS 이름 확인 | **VirtualNetwork** | * | 가상 네트워크의 모든 사용자 지정 DNS (Domain Name System) 서버에 대 한 IP 주소 | 53 | 가상 네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우에만 필요 합니다. |
|||||||

또한 [ASE (App Service Environment)](../app-service/environment/intro.md)에 대 한 아웃 바운드 규칙을 추가 해야 합니다.

* Azure 방화벽을 사용 하는 경우 ASE 플랫폼 트래픽에 대 한 아웃 바운드 액세스를 허용 하는 ASE (App Service Environment) [FQDN (정규화 된 도메인 이름) 태그](../firewall/fqdn-tags.md#current-fqdn-tags)를 사용 하 여 방화벽을 설정 해야 합니다.

* Azure 방화벽 이외의 방화벽 어플라이언스를 사용 하는 경우 App Service Environment에 필요한 [방화벽 통합 종속성](../app-service/environment/firewall-integration.md#dependencies) 에 나열 된 *모든* 규칙을 사용 하 여 방화벽을 설정 해야 합니다.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>강제 터널링 요구 사항

방화벽을 통해 [강제 터널링](../firewall/forced-tunneling.md) 을 설정 하거나 사용 하는 경우 ISE에 대 한 추가 외부 종속성을 허용 해야 합니다. 강제 터널링을 사용 하면 인터넷에 바인딩된 트래픽을 VPN (가상 사설망) 또는 가상 어플라이언스와 같은 지정 된 다음 홉으로 리디렉션하여 아웃 바운드 네트워크 트래픽을 검사 하 고 감사할 수 있습니다.

일반적으로 모든 ISE 아웃 바운드 종속성 트래픽은 ISE로 프로 비전 된 VIP (가상 IP 주소)를 통해 이동 합니다. 그러나 ISE 간에 트래픽 라우팅을 변경 하는 경우 다음 홉을로 설정 하 여 방화벽에 대해 다음과 같은 아웃 바운드 종속성을 허용 해야 `Internet` 합니다. Azure 방화벽을 사용 하는 경우 지침에 따라 [App Service Environment를 사용 하 여 방화벽을 설정](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)합니다.

이러한 종속성에 대 한 액세스를 허용 하지 않으면 ISE 배포가 실패 하 고 배포 된 ISE 작동이 중지 됩니다.

* [App Service Environment 관리 주소](../app-service/environment/management-addresses.md)

* [Azure API Management 주소](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Azure Traffic Manager 관리 주소](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [ISE 영역에 대 한 인바운드 및 아웃 바운드 주소를 Logic Apps 합니다.](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* 방화벽을 통해 이러한 서비스로 트래픽을 보낼 수 없기 때문에 Azure SQL, 저장소, Service Bus 및 이벤트 허브에 대 한 서비스 끝점을 사용 하도록 설정 해야 합니다.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 검색 상자에서 `integration service environments`를 필터로 입력하고 **통합 서비스 환경**을 선택합니다.

   !["통합 서비스 환경" 찾기 및 선택](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. **통합 서비스 환경** 창에서 **추가**를 선택합니다.

   !["추가"를 선택 하 여 통합 서비스 환경 만들기](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 사용자 환경에 대한 세부 정보를 제공한 다음, **검토 + 만들기**를 선택합니다. 예:

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 환경을 만들려는 신규 또는 기존 Azure 리소스 그룹 |
   | **통합 서비스 환경 이름** | 예 | <*environment-name*> | ISE 이름이며 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 마침표(`.`)만 포함할 수 있습니다. |
   | **위치** | 예 | <*Azure-datacenter-region*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **SKU** | 예 | **프리미엄** 또는 **개발자(SLA 없음)** | 만들고 사용할 ISE SKU입니다. 이러한 SKU 간의 차이점은 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)를 참조하세요. <p><p>**중요**: 이 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에는 변경할 수 없습니다. |
   | **추가 용량** | 프리미엄: <br>예 <p><p>개발자: <br>해당 없음 | 프리미엄: <br>0~10 <p><p>개발자: <br>해당 없음 | 이 ISE 리소스에 사용할 추가 처리 단위 수입니다. 만든 후 용량을 추가하려면 [ISE 용량 추가](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)를 참조하세요. |
   | **액세스 엔드포인트** | 예 | **내부** 또는 **외부** | ISE에 사용할 액세스 엔드포인트의 유형입니다. 이러한 엔드포인트에 따라 ISE의 논리 앱에서 요청 또는 웹후크 트리거가 가상 네트워크 외부의 호출을 받을 수 있을지 여부가 결정됩니다. <p><p>선택한 사항은 논리 앱 실행 기록에서 입력 및 출력을 보고 액세스할 수 있는 방식에도 영향을 줍니다. 자세한 내용은 [ISE 엔드포인트 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조하세요. <p><p>**중요**: 액세스 엔드포인트는 ISE를 만드는 동안에만 선택할 수 있고 나중에 이 옵션을 변경할 수 없습니다. |
   | **가상 네트워크** | 예 | <*Azure-virtual-network-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크가 없으면 [먼저 Azure 가상 네트워크를 만듭니다](../virtual-network/quick-create-portal.md). <p><p>**중요**: ISE를 만들 때*만* 이 삽입을 수행할 수 있습니다. |
   | **서브넷** | 예 | <*subnet-resource-list*> | ISE에는 ISE에서 리소스를 만들고 배포 하는 데 필요한 4 개의 *빈* 서브넷이 필요 하며 커넥터 및 성능 캐싱과 같은 내부 Logic Apps 구성 요소에서 사용 됩니다. <p>**중요**: 서브넷을 [만들기 위해 이러한 단계를 계속 하기 전에 서브넷 요구 사항을 검토](#create-subnet)해야 합니다. |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   Ise에는 ISE에서 리소스를 만들고 배포 하는 데 필요한 4 개의 *빈* 서브넷이 필요 하며 커넥터 및 성능 캐싱과 같은 내부 Logic Apps 구성 요소에서 사용 됩니다. 환경을 만든 후 이러한 서브넷 주소를 변경할 수 없습니다. Azure Portal를 통해 ISE를 만들어 배포 하는 경우 Azure 서비스에 이러한 서브넷을 위임 하지 않아야 합니다. 그러나 REST API, Azure PowerShell 또는 Azure Resource Manager 템플릿을 통해 ISE를 만들어 배포 하는 경우에는 빈 서브넷 하나를에 [위임](../virtual-network/manage-subnet-delegation.md) 해야 `Microsoft.integrationServiceEnvironment` 합니다. 자세한 내용은 [서브넷 위임 추가](../virtual-network/manage-subnet-delegation.md)를 참조 하세요.

   각 서브넷은 다음 요구 사항을 충족해야 합니다.

   * 는 알파벳 문자 또는 밑줄 (숫자 없음)로 시작 하는 이름을 사용 하 고,,,,, `<` `>` `%` `&` `\\` `?` , `/` 등의 문자는 사용 하지 않습니다.

   * [CIDR(Classless Inter-Domain Routing) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 및 클래스 B 주소 공간을 사용합니다.
   
     > [!IMPORTANT]
     >
     > Azure Logic Apps에서 확인할 수 없기 때문에 가상 네트워크 또는 서브넷에 대해 다음 IP 주소 공간을 사용 하지 마세요.<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * 각 서브넷에 주소 32개가 필요하기 때문에 주소 공간에 `/27`을 사용합니다. 예를 들어, `10.0.0.0/27`에는 주소가 32개 있습니다.2<sup>(32-27)</sup>은 2<sup>5</sup> 또는 32이기 때문입니다. 주소가 더 많아도 이점은 없습니다. 주소 계산에 대해 자세히 알아보려면 [IPv4 CIDR 블록](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)을 참조하세요.

   * [ExpressRoute](../expressroute/expressroute-introduction.md)를 사용하는 경우 다음 경로를 포함하는 [경로 테이블을 만든](../virtual-network/manage-route-table.md) 다음, 이 테이블을 ISE에서 사용하는 각 서브넷과 연결해야 합니다.

     **이름**: <*경로-이름*><br>
     **주소 접두사**: 0.0.0.0/0<br>
     **다음 홉**: 인터넷

   1. **서브넷** 목록에서 **서브넷 구성 관리**를 선택합니다.

      ![서브넷 구성 관리](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. **서브넷** 창에서 **서브넷**을 선택합니다.

      ![빈 서브넷 4개 추가](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. **서브넷 추가** 창에서 이 정보를 제공합니다.

      * **Name**: 서브넷의 이름
      * **주소 범위(CIDR 블록)** : 가상 네트워크 및 CIDR 형식의 서브넷 범위

      ![서브넷 세부 정보 추가](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 완료되면 **확인**을 선택합니다.

   1. 세 개 이상의 서브넷에 대해 이 단계를 반복합니다.

      > [!NOTE]
      > 만들려는 서브넷이 유효하지 않으면 Azure Portal에 메시지가 표시되지만 진행이 차단되지는 않습니다.

   서브넷 만들기에 대한 자세한 내용은 [가상 네트워크 서브넷 추가](../virtual-network/virtual-network-manage-subnet.md)를 참조하세요.

1. Azure에서 성공적으로 ISE 정보의 유효성 검사를 완료하면 **만들기**를 선택합니다. 예:

   ![유효성 검사에 성공하면 "만들기" 선택](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure가 환경 배포를 시작하며, 보통 2시간 이내에 완료됩니다. 배포에 최대 4시간이 걸리는 경우가 간혹 있습니다. 배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   그렇지 않으면, 배포 문제 해결에 대한 Azure Portal 지침을 따릅니다.

   > [!NOTE]
   > 배포가 실패 하거나 ISE를 삭제 하는 경우에는 서브넷을 해제 하기 전에 Azure에서 최대 한 시간이 걸릴 수 있습니다. 따라서 다른 ISE에서 해당 서브넷을 다시 사용 하려면 먼저 기다려야 할 수 있습니다.
   >
   > 가상 네트워크를 삭제하면 Azure에서 서브넷이 해제되기까지 일반적으로 최대 2시간이 걸리지만 더 오래 걸릴 수도 있습니다. 
   > 가상 네트워크를 삭제할 때는 그 때까지 연결된 리소스가 없는지 확인해야 합니다. 
   > [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

1. 배포가 완료된 후에 Azure가 환경으로 자동으로 이동하지 않는 경우 환경을 보려면 **리소스로 이동**을 선택합니다.

1. ISE의 네트워크 상태를 확인하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)를 참조하세요.

1. ISE에서 논리 앱 및 기타 아티팩트 만들기를 시작하려면 [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)를 참조하세요.

   > [!IMPORTANT]
   > ISE를 만든 후 사용 가능한 관리형 ISE 커넥터는 Logic App 디자이너의 커넥터 선택기에 자동으로 나타나지 않습니다. ISE 커넥터를 사용하려면 먼저 [해당 커넥터를 ISE에 수동으로 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)하여 Logic App 디자이너에 나타나도록 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경에 리소스 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
