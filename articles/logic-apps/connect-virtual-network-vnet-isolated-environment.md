---
title: ISE를 사용 하 여 Azure 가상 네트워크에 연결
description: Azure Logic Apps에서 Azure Vnet (가상 네트워크)에 액세스할 수 있는 ISE (통합 서비스 환경)를 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2fa43cb9ec526cfab2367431712e09406556a529
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191997"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)를 사용하여 Azure Logic Apps에서 Azure 가상 네트워크에 연결

논리 앱 및 통합 계정이 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)에 액세스해야 하는 시나리오의 경우 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만듭니다. ISE는 전용 저장소 및 공용, "전역" 다중 테 넌 트 Logic Apps 서비스와 별도로 유지 되는 기타 리소스를 사용 하는 격리 된 환경입니다. 이러한 격리로 인해 다른 Azure 테넌트가 앱 성능에 줄 수 있는 영향이 감소됩니다. 또한 ISE는 사용자 고유의 고정 IP 주소를 제공 합니다. 이러한 IP 주소는 공용 다중 테 넌 트 서비스의 논리 앱에서 공유 하는 고정 IP 주소와는 별개입니다.

ISE를 만들 때 Azure는이 ISE를 Azure 가상 네트워크에 *삽입* 하 여 가상 네트워크에 Logic Apps 서비스를 배포 합니다. 논리 앱 또는 통합 계정을 만들 때 ISE를 해당 위치로 선택 합니다. 그러면 논리 앱 및 통합 계정은 가상 네트워크에서 VM(가상 머신), 서버, 시스템 및 서비스와 같은 리소스에 직접 액세스할 수 있습니다.

![통합 서비스 환경 선택](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 논리 앱과 통합 계정이 ISE에서 함께 작동 하려면 둘 다 해당 위치와 *동일한 ise* 를 사용 해야 합니다.

ISE는 실행 지속 시간, 저장소 보존, 처리량, HTTP 요청 및 응답 시간 제한, 메시지 크기 및 사용자 지정 커넥터 요청에 대 한 제한을 증가 시켰습니다. 자세한 내용은 [Azure Logic Apps에 대 한 제한 및 구성](logic-apps-limits-and-config.md)을 참조 하세요. ISEs에 대해 자세히 알아보려면 [Azure Logic Apps에서 Azure Virtual Network 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 참조 하세요.

이 문서에서는 다음 작업을 완료 하는 방법을 보여 줍니다.

* ISE에 대 한 액세스를 사용 하도록 설정 합니다.
* ISE를 만듭니다.
* ISE에 추가 용량을 추가 합니다.

> [!IMPORTANT]
> ISE에서 실행 되는 논리 앱, 기본 제공 트리거, 기본 제공 작업 및 커넥터는 소비 기반 요금제와 다른 가격 책정 계획을 사용 합니다. ISEs에 대 한 가격 책정 및 청구의 작동 방식에 대 한 자세한 [Logic Apps 내용은 가격 책정 모델](../logic-apps/logic-apps-pricing.md#fixed-pricing)을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](../logic-apps/logic-apps-pricing.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)입니다. 가상 네트워크가 없는 경우 [Azure 가상 네트워크를 만드는](../virtual-network/quick-create-portal.md) 방법을 알아봅니다.

  * 가상 네트워크에는 ISE에서 리소스를 만들고 배포 하기 위한 4 개의 *빈* 서브넷이 있어야 합니다. 각 서브넷은 ISE에 대해 다른 Logic Apps 구성 요소를 지원 합니다. 이러한 서브넷을 미리 만들 수도 있고, 한 번에 서브넷을 만들 수 있는 ISE를 만들 때까지 기다릴 수도 있습니다. [서브넷 요구 사항](#create-subnet)에 대해 자세히 알아보세요.

  * 서브넷 이름은 알파벳 문자 또는 밑줄로 시작 해야 하 고, `<`, `>`, `%`, `&`, `\\`, `?`, `/`문자를 사용할 수 없습니다. 
  
  * Azure Resource Manager 템플릿을 통해 ISE를 배포 하려는 경우 먼저 빈 서브넷 하나를 Microsoft. integrationServiceEnvironment/에 위임 해야 합니다. Azure Portal를 통해 배포할 때이 위임을 수행할 필요가 없습니다.

  * ISE가 제대로 작동 하 고 액세스할 수 있도록 가상 네트워크가 [ise에 대 한 액세스를 허용](#enable-access) 하는지 확인 합니다.

  * 연결 공급자가 촉진 하는 Microsoft 클라우드 서비스에 대 한 개인 연결을 제공 하는 [express](../expressroute/expressroute-introduction.md)경로를 사용 하는 경우 다음 경로를 포함 하는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) 해당 테이블을 ISE에서 사용 하는 각 서브넷에 연결 해야 합니다.

    **이름**: <*경로 이름*><br>
    **주소 접두사**: 0.0.0.0/0<br>
    **다음 홉**: 인터넷

* Azure 가상 네트워크에 사용자 지정 DNS 서버를 사용 하려면 가상 네트워크에 ISE를 배포 하기 전에 다음 단계를 수행 하 [여 해당 서버를 설정](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 합니다. 그렇지 않으면 DNS 서버를 변경할 때마다 ISE를 다시 시작 해야 합니다.

  > [!IMPORTANT]
  > ISE를 만든 후 DNS 서버 설정을 변경 하는 경우 ISE를 다시 시작 해야 합니다. DNS 서버 설정을 관리 하는 방법에 대 한 자세한 내용은 [가상 네트워크 만들기, 변경 또는 삭제](../virtual-network/manage-virtual-network.md#change-dns-servers)를 참조 하세요.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>ISE에 대 한 액세스 사용

Azure 가상 네트워크에서 ISE를 사용 하는 경우 일반적인 설치 문제는 하나 이상의 차단 된 포트를 포함 하는 것입니다. ISE와 대상 시스템 간에 연결을 만드는 데 사용 하는 커넥터에도 자체 포트 요구 사항이 있을 수 있습니다. 예를 들어 ftp 커넥터를 사용 하 여 FTP 시스템과 통신 하는 경우 FTP 시스템에서 사용 하는 포트 (예: 송신 명령을 위한 포트 21)를 사용할 수 있어야 합니다.

ISE에 액세스할 수 있고 ISE의 논리 앱이 가상 네트워크의 각 서브넷에서 통신할 수 있는지 확인 하려면 [각 서브넷에 대해이 표에 설명 된 포트를 엽니다](#network-ports-for-ise). 필요한 포트를 사용할 수 없는 경우 ISE가 제대로 작동 하지 않습니다.

* IP 제한이 있는 다른 끝점에 액세스 해야 하는 ISE 인스턴스가 여러 개 있는 경우 [Azure 방화벽](../firewall/overview.md) 또는 [네트워크 가상 어플라이언스](../virtual-network/virtual-networks-overview.md#filter-network-traffic) 를 가상 네트워크에 배포 하 고 해당 방화벽 또는 네트워크 가상 어플라이언스를 통해 아웃 바운드 트래픽을 라우팅합니다. 그런 다음 가상 네트워크의 모든 ISE 인스턴스가 대상 시스템과 통신 하는 데 사용할 수 있는 [단일 아웃 바운드, 공용, 정적 및 예측 가능한 IP 주소를 설정할](connect-virtual-network-vnet-set-up-single-ip-address.md) 수 있습니다. 이런 방식으로 각 ISE에 대 한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

   > [!NOTE]
   > 시나리오에서 액세스 해야 하는 IP 주소의 수를 제한 해야 하는 경우 단일 ISE에이 방법을 사용할 수 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대 한 추가 비용이 시나리오에 적합 한지 여부를 고려 합니다. [Azure 방화벽 가격 책정](https://azure.microsoft.com/pricing/details/azure-firewall/)에 대해 자세히 알아보세요.

* 제약 조건 없이 새 Azure 가상 네트워크 및 서브넷을 만든 경우 서브넷 간 트래픽을 제어 하기 위해 가상 네트워크에서 [NSGs (네트워크 보안 그룹)](../virtual-network/security-overview.md#network-security-groups) 를 설정할 필요가 없습니다.

* 기존 가상 네트워크에서 *필요* 에 따라 [서브넷 간 네트워크 트래픽을 필터링](../virtual-network/tutorial-filter-network-traffic.md)하 여 nsgs를 설정할 수 있습니다. 이 경로로 이동 하거나 이미 NSGs를 사용 하 고 있는 경우 NSGs가 있거나 NSGs를 설정 하려는 가상 네트워크에서 [이 테이블의 포트를 열어야](#network-ports-for-ise) 합니다.

  > [!NOTE]
  > [Nsg 보안 규칙](../virtual-network/security-overview.md#security-rules)을 사용 하는 경우 TCP 및 UDP 프로토콜을 *모두* 사용 해야 합니다. NSG 보안 규칙은 이러한 포트에 액세스 해야 하는 IP 주소에 대해 열어야 하는 포트를 설명 합니다. 이러한 끝점 사이에 존재 하는 방화벽, 라우터 또는 기타 항목 에서도 해당 IP 주소에 액세스할 수 있는 포트를 유지 해야 합니다.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE에서 사용 하는 네트워크 포트

이 표에서는 ISE에서 사용 하는 Azure virtual network의 포트와 이러한 포트가 사용 되는 위치에 대해 설명 합니다. [리소스 관리자 서비스 태그](../virtual-network/security-overview.md#service-tags) 는 보안 규칙을 만들 때 복잡성을 최소화 하는 데 도움이 되는 IP 주소 접두사 그룹을 나타냅니다.

> [!IMPORTANT]
> 원본 포트는 사용 후 삭제 되므로 모든 규칙에 대 한 `*`으로 설정 해야 합니다. 언급 된 위치에서 내부 ISE 및 외부 ISE는 [ise 생성 시 선택 된 끝점](connect-virtual-network-vnet-isolated-environment.md#create-environment)을 참조 합니다. 자세한 내용은 [끝점 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조 하세요. 

| 목적 | Direction | 대상 포트 | 원본 서비스 태그 | 대상 서비스 태그 | 메모 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intrasubnet 통신 | 인바운드 및 아웃바운드 | * | ISE 서브넷을 사용 하는 가상 네트워크의 주소 공간 | ISE 서브넷을 사용 하는 가상 네트워크의 주소 공간 | 각 서브넷 내에서 트래픽이 흐를 수 있도록 해야 합니다. <p><p>**중요**: 서브넷 내의 구성 요소 간 통신의 경우 해당 서브넷 내의 모든 포트를 열어야 합니다. |
| 상호 서브넷 통신 | 인바운드 및 아웃바운드 | 80, 443 | VirtualNetwork | VirtualNetwork | 서브넷 간 통신 |
| Azure Logic Apps에서 받는 통신 | 아웃바운드 | 80, 443 | VirtualNetwork | 인터넷 | 포트는 Logic Apps 서비스가 통신 하는 외부 서비스에 의존 합니다. |
| Azure Active Directory | 아웃바운드 | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage 종속성 | 아웃바운드 | 80, 443, 445 | VirtualNetwork | 스토리지 | |
| Azure Logic Apps로 보내는 통신 | 인바운드 | 443 | 내부 ISE: <br>VirtualNetwork <p><p>외부 ISE: <br>인터넷 | VirtualNetwork | 논리 앱에서 요청 트리거 또는 웹 후크를 호출 하는 컴퓨터 또는 서비스에 대 한 IP 주소입니다. 이 포트를 닫거나 차단 하면 요청 트리거를 사용 하 여 논리 앱에 대 한 HTTP 호출을 수행할 수 없습니다. |
| 논리 앱 실행 기록 | 인바운드 | 443 | 내부 ISE: <br>VirtualNetwork <p><p>외부 ISE: <br>인터넷 | VirtualNetwork | 논리 앱의 실행 기록을 보려는 컴퓨터의 IP 주소입니다. 이 포트를 닫거나 차단 해도 실행 기록이 표시 되지 않지만 해당 실행 기록의 각 단계에 대 한 입력 및 출력은 볼 수 없습니다. |
| 연결 관리 | 아웃바운드 | 443 | VirtualNetwork  | AppService | |
| 진단 로그 및 메트릭 게시 | 아웃바운드 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Traffic Manager에서 통신 | 인바운드 | 내부 ISE: 454 <p><p>외부 ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps 디자이너 - 동적 속성 | 인바운드 | 454 | 허용 되는 IP 주소는 **참고** 열을 참조 하세요. | VirtualNetwork | 요청은 해당 지역에 대 한 끝점 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) IP 주소 Logic Apps 액세스 합니다. |
| 네트워크 상태 검사 | 인바운드 | 454 | 허용 되는 IP 주소는 **참고** 열을 참조 하세요. | VirtualNetwork | 요청은 해당 지역에 대 한 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) 및 [아웃 바운드](../logic-apps/logic-apps-limits-and-config.md#outbound) IP 주소에 대 한 Logic Apps 액세스 끝점에서 제공 됩니다. |
| App Service 관리 종속성 | 인바운드 | 454, 455 | AppServiceManagement | VirtualNetwork | |
| 커넥터 배포 | 인바운드 | 454 | AzureConnectors | VirtualNetwork | 커넥터를 배포 및 업데이트 하는 데 필요 합니다. 이 포트를 닫거나 차단 하면 ISE 배포가 실패 하 고 커넥터 업데이트 또는 수정이 방지 됩니다. |
| 커넥터 정책 배포 | 인바운드 | 3443 | APIManagement | VirtualNetwork | 커넥터를 배포 및 업데이트 하는 데 필요 합니다. 이 포트를 닫거나 차단 하면 ISE 배포가 실패 하 고 커넥터 업데이트 또는 수정이 방지 됩니다. |
| Azure SQL 종속성 | 아웃바운드 | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | 아웃바운드 | 1886 | VirtualNetwork | AzureMonitor | Resource Health에 상태를 게시 하는 경우 |
| API Management - 관리 엔드포인트 | 인바운드 | 3443 | APIManagement | VirtualNetwork | |
| 이벤트 허브에 로그 정책 및 모니터링 에이전트의 종속성 | 아웃바운드 | 5672 | VirtualNetwork | EventHub | |
| 역할 인스턴스 간의 Azure Cache for Redis 인스턴스 액세스 | 인바운드 <br>아웃바운드 | 6379-6383 | VirtualNetwork | VirtualNetwork | 또한 ISE가 Redis 용 Azure Cache를 사용 하도록 하려면 [Azure cache For REDIS FAQ에 설명 된 이러한 아웃 바운드 및 인바운드 포트](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)를 열어야 합니다. |
| Azure Load Balancer | 인바운드 | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE 만들기

1. [Azure Portal](https://portal.azure.com)의 기본 Azure search 상자에서 필터로 `integration service environments`를 입력 하 고 **Integration Service environment**를 선택 합니다.

   !["Integration Service Environment"를 찾아 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. **Integration Service environment** 창에서 **추가**를 선택 합니다.

   !["Integration Service Environment"를 찾아 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. 사용자 환경에 이러한 세부 정보를 제공 하 고 **검토 + 만들기**를 선택 합니다. 예를 들면 다음과 같습니다.

   ![환경 세부 정보 제공](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **구독** | yes | <*Azure-subscription-name*> | 환경에 사용할 Azure 구독 |
   | **리소스 그룹** | yes | <*Azure-resource-group-name*> | 환경을 만들려는 새 또는 기존 Azure 리소스 그룹 |
   | **Integration service environment 이름** | yes | <*environment-name*> | 문자, 숫자, 하이픈 (`-`), 밑줄 (`_`) 및 마침표 (`.`)만 포함할 수 있는 ISE 이름입니다. |
   | **위치** | yes | <*Azure-datacenter-region*> | 환경을 배포할 Azure 데이터 센터 지역 |
   | **SKU** | yes | **프리미엄** 또는 **개발자 (SLA 없음)** | 만들고 사용할 ISE SKU입니다. 이러한 Sku 간의 차이점은 [ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)를 참조 하세요. <p><p>**중요**:이 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **추가 용량** | Premium: <br>yes <p><p>개발자: <br>해당 없음 | Premium: <br>0 ~ 10 <p><p>개발자: <br>해당 없음 | 이 ISE 리소스에 사용할 추가 처리 단위의 수입니다. 만든 후 용량을 추가 하려면 [ISE 용량 추가](#add-capacity)를 참조 하세요. |
   | **액세스 끝점** | yes | **내부** 또는 **외부** | ISE에 사용할 액세스 끝점의 유형입니다. 이러한 끝점은 ISE의 논리 앱에 대 한 요청 또는 webhook 트리거가 가상 네트워크 외부에서 호출을 받을 수 있는지 여부를 결정 합니다. <p><p>선택 항목은 논리 앱 실행 기록에서 입력 및 출력을 보고 액세스할 수 있는 방법에도 영향을 줍니다. 자세한 내용은 [ISE 끝점 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)를 참조 하세요. <p><p>**중요**:이 옵션은 ISE를 만들 때만 사용할 수 있으며 나중에 변경할 수 없습니다. |
   | **가상 네트워크** | yes | <*Azure-virtual-network-name*> | 해당 환경의 논리 앱이 가상 네트워크에 액세스할 수 있도록 환경을 삽입하려는 Azure 가상 네트워크입니다. 네트워크가 없는 경우 [먼저 Azure virtual network를 만듭니다](../virtual-network/quick-create-portal.md). <p><p>**중요**: ISE를 만들 때 *만* 이 삽입을 수행할 수 있습니다. |
   | **서브넷** | yes | <*subnet-resource-list*> | ISE에는 사용자 환경에서 리소스를 만들고 배포 하기 위해 네 개의 *빈* 서브넷이 필요 합니다. 각 서브넷을 만들려면 [이 테이블 아래의 단계를 따릅니다](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **서브넷 만들기**

   사용자 환경에서 리소스를 만들고 배포 하기 위해 ISE에는 어떤 서비스에도 위임 되지 않은 *빈* 서브넷 4 개가 필요 합니다. 환경을 만든 후에는 이러한 서브넷 주소를 변경할 *수 없습니다* .

   > [!IMPORTANT]
   > 
   > 서브넷 이름은 알파벳 문자 또는 밑줄 (숫자 없음)로 시작 해야 하며, `<`, `>`, `%`, `&`, `\\`, `?`, `/`문자를 사용 하지 않습니다.

   또한 각 서브넷은 다음 요구 사항을 충족 해야 합니다.

   * 는 클래스 없는 [CIDR (도메인 간 라우팅) 형식](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 및 클래스 B 주소 공간을 사용 합니다.

   * 각 서브넷 *에 최소한 32* *이상의* 주소가 필요 하기 때문에 주소 공간에 적어도 `/27`를 사용 합니다. 다음은 그 예입니다.

     * 2<sup>(32-27)</sup> 가 2<sup>5</sup> 또는 32 이기 때문에 `10.0.0.0/27`에 32 주소가 있습니다.

     * 2<sup>(32-24)</sup> 가 2<sup>8</sup> 또는 256 256 주소를 `10.0.0.0/24` 합니다.

     * `10.0.0.0/28` 4 개의 주소만 포함 하 고 2<sup>(32-28)</sup> 가 2<sup>4</sup> 또는 16 이므로 너무 작습니다.

     주소 계산에 대해 자세히 알아보려면 [IPV4 CIDR 블록](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)을 참조 하세요.

   * [Express](../expressroute/expressroute-introduction.md)경로를 사용 하는 경우 다음 경로를 포함 하는 [경로 테이블을 만들고](../virtual-network/manage-route-table.md) 해당 테이블을 ISE에서 사용 하는 각 서브넷과 연결 해야 합니다.

     **이름**: <*경로 이름*><br>
     **주소 접두사**: 0.0.0.0/0<br>
     **다음 홉**: 인터넷

   1. **서브넷 목록에서** **서브넷 구성 관리**를 선택 합니다.

      ![서브넷 구성 관리](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. **서브넷** 창에서 **서브넷**을 선택 합니다.

      ![빈 서브넷 4 개 추가](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. **서브넷 추가** 창에서 이 정보를 제공합니다.

      * **이름**: 서브넷의 이름입니다.
      * **주소 범위 (cidr 블록)** : 가상 네트워크와 CIDR 형식의 서브넷 범위

      ![서브넷 세부 정보 추가](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. 완료되면 **확인**을 선택합니다.

   1. 세 개 이상의 서브넷에 대해 이 단계를 반복합니다.

      > [!NOTE]
      > 만들려고 하는 서브넷이 유효 하지 않은 경우 Azure Portal 메시지를 표시 하지만 진행 상황을 차단 하지는 않습니다.

   서브넷을 만드는 방법에 대 한 자세한 내용은 [가상 네트워크 서브넷 추가](../virtual-network/virtual-network-manage-subnet.md)를 참조 하세요.

1. Azure에서 ISE 정보를 성공적으로 확인 한 후 **만들기**를 선택 합니다. 예를 들면 다음과 같습니다.

   ![유효성 검사가 완료 되 면 "만들기"를 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure는 일반적으로 2 시간 이내에 완료 되는 환경 배포를 시작 합니다. 경우에 따라 배포에 최대 4 시간이 걸릴 수 있습니다. 배포 상태를 확인 하려면 Azure 도구 모음에서 알림 아이콘을 선택 합니다. 그러면 알림 창이 열립니다.

   ![배포 상태 확인](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   배포가 성공적으로 완료되면 Azure에 이 알림이 표시됩니다.

   ![배포 성공](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   그렇지 않으면 배포 문제 해결에 대 한 Azure Portal 지침을 따르세요.

   > [!NOTE]
   > 배포가 실패 하거나 ISE를 삭제 하는 경우 Azure는 서브넷을 해제 하기 전까지 최대 한 시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용 하기 전에 기다려야 한다는 것을 의미 합니다.
   >
   > 가상 네트워크를 삭제 하는 경우 Azure는 일반적으로 서브넷을 해제 하기 전까지 최대 2 시간이 걸리지만이 작업은 더 오래 걸릴 수 있습니다. 
   > 가상 네트워크를 삭제 하는 경우 아직 연결 된 리소스가 없는지 확인 합니다. 
   > [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조 하세요.

1. 환경을 보려면 배포가 완료 된 후 Azure가 환경으로 자동으로 이동 하지 않는 경우 **리소스로 이동** 을 선택 합니다.

1. ISE에 대 한 네트워크 상태를 확인 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)를 참조 하세요.

1. ISE에서 논리 앱 및 기타 아티팩트 만들기를 시작 하려면 [integration service environment에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)를 참조 하세요.

   > [!IMPORTANT]
   > ISE를 만든 후 사용할 수 있게 되는 관리형 ISE 커넥터는 논리 앱 디자이너의 커넥터 선택에 자동으로 표시 되지 않습니다. 이러한 ISE 커넥터를 사용 하려면 먼저 [해당 커넥터를 ise에 수동으로 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) 하 여 논리 앱 디자이너에 표시 되도록 해야 합니다.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE 용량 추가

프리미엄 ISE 기본 단위는 고정 용량을 가지 므로 더 많은 처리량이 필요한 경우 생성 중 또는 나중에 확장 단위를 추가할 수 있습니다. 개발자 SKU에는 배율 단위를 추가 하는 기능이 포함 되어 있지 않습니다.

1. Azure Portal에서 ISE를 찾습니다.

1. Ise에 대 한 사용 현황 및 성능 메트릭을 검토 하려면 ISE 메뉴에서 **개요**를 선택 합니다.

   ![ISE에 대 한 사용 현황 보기](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. **설정**아래에서 **Scale out**을 선택 합니다. **구성** 창에서 다음 옵션을 선택 합니다.

   * [**수동 크기 조정**](#manual-scale): 사용 하려는 처리 단위 수를 기준으로 크기를 조정 합니다.
   * [**사용자 지정 자동 크기 조정**](#custom-autoscale): 다양 한 조건에서 선택 하 고 해당 조건에 맞는 임계값 조건을 지정 하 여 성능 메트릭에 따라 크기를 조정 합니다.

   ![원하는 크기 조정 유형을 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>수동 크기 조정

1. **수동 크기 조정**을 선택한 후 **용량을 추가**하려면 사용할 배율 단위 수를 선택 합니다.

   ![원하는 크기 조정 유형을 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/select-manual-scale-out-units.png)

1. 완료되면 **저장**을 선택합니다.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>사용자 지정 자동 크기 조정

1. **사용자 지정 자동 크기 조정**을 선택한 후 **자동 크기 조정 설정 이름**에 설정에 대 한 이름을 제공 하 고 필요에 따라 설정이 속한 Azure 리소스 그룹을 선택 합니다.

   ![자동 크기 조정 설정에 대 한 이름을 제공 하 고 리소스 그룹을 선택 합니다.](./media/connect-virtual-network-vnet-isolated-environment/select-custom-autoscale.png)

1. **기본** 조건의 경우 **메트릭 기반 크기 조정** 또는 **특정 인스턴스 수에**대 한 크기 조정 중 하나를 선택 합니다.

   * 인스턴스 기반을 선택 하는 경우 처리 단위의 수를 0에서 10 사이의 값으로 입력 합니다.

   * 메트릭 기반을 선택 하는 경우 다음 단계를 수행 합니다.

     1. **규칙** 섹션에서 **규칙 추가**를 선택 합니다.

     1. 규칙 **크기 조정** 창에서 규칙을 트리거할 때 사용할 조건 및 작업을 설정 합니다.

     1. **인스턴스 제한**의 경우 다음 값을 지정 합니다.

        * **최소**: 사용할 최소 처리 단위 수
        * **최대값**: 사용할 최대 처리 단위 수
        * **기본값**: 리소스 메트릭을 읽는 동안 문제가 발생 하 고 현재 용량이 기본 용량 보다 낮은 경우 자동 크기 조정은 기본 처리 단위 수로 확장 됩니다. 그러나 현재 용량이 기본 용량을 초과 하는 경우 자동 크기 조정이 확장 되지 않습니다.

1. 다른 조건을 추가 하려면 **크기 조정 조건 추가**를 선택 합니다.

1. 자동 크기 조정 설정에 대 한 작업이 완료 되 면 변경 내용을 저장 합니다.

## <a name="delete-ise"></a>ISE 삭제

더 이상 필요 하지 않은 ISE를 삭제 하거나 ISE를 포함 하는 Azure 리소스 그룹을 삭제 하기 전에 이러한 항목이 삭제를 차단할 수 있으므로 azure 가상 네트워크 또는 이러한 리소스를 포함 하는 Azure 리소스 그룹에 대 한 정책 또는 잠금이 없는지 확인 하세요.

ISE를 삭제 한 후 Azure 가상 네트워크 또는 서브넷을 삭제 하기 전에 최대 9 시간까지 기다려야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Integration service environment에 아티팩트 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [통합 서비스 환경의 네트워크 상태 확인](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)에 대해 자세히 알아보기
* [Azure 서비스에 대한 가상 네트워크 통합](../virtual-network/virtual-network-for-azure-services.md)에 대해 알아보기
