---
title: ISEs에 대 한 공용 아웃 바운드 IP 주소 설정
description: Azure Logic Apps에서 integration service environment (ISEs)에 대 한 단일 공용 아웃 바운드 IP 주소를 설정 하는 방법에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191470"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Azure Logic Apps에서 하나 이상의 통합 서비스 환경에 대해 단일 IP 주소를 설정 합니다.

Azure Logic Apps를 사용 하는 경우 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)의 리소스에 액세스 해야 하는 논리 앱을 호스팅하기 위한 [ISE ( *통합 서비스 환경* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 를 설정할 수 있습니다. IP 제한이 있는 다른 끝점에 액세스 해야 하는 ISE 인스턴스가 여러 개 있는 경우 [Azure 방화벽](../firewall/overview.md) 또는 [네트워크 가상 어플라이언스](../virtual-network/virtual-networks-overview.md#filter-network-traffic) 를 가상 네트워크에 배포 하 고 해당 방화벽 또는 네트워크 가상 어플라이언스를 통해 아웃 바운드 트래픽을 라우팅합니다. 그런 다음 가상 네트워크의 모든 ISE 인스턴스가 대상 시스템과 통신 하는 데 사용할 수 있는 단일 공용, 정적 및 예측 가능한 IP 주소를 사용할 수 있습니다. 이런 방식으로 각 ISE에 대 한 해당 대상 시스템에서 추가 방화벽을 설정할 필요가 없습니다.

이 항목에서는 Azure 방화벽을 통해 아웃 바운드 트래픽을 라우팅하는 방법을 보여 주지만, Azure Marketplace에서 타사 방화벽과 같은 네트워크 가상 어플라이언스에 비슷한 개념을 적용할 수 있습니다. 이 항목에서는 여러 ISE 인스턴스의 설치에 중점을 둔 반면, 시나리오에서 액세스 해야 하는 IP 주소의 수를 제한 해야 하는 경우 단일 ISE에 대해이 방법을 사용할 수도 있습니다. 방화벽 또는 가상 네트워크 어플라이언스에 대 한 추가 비용이 시나리오에 적합 한지 여부를 고려 합니다. [Azure 방화벽 가격 책정](https://azure.microsoft.com/pricing/details/azure-firewall/)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

* ISE와 동일한 가상 네트워크에서 실행 되는 Azure 방화벽. 방화벽이 없으면 먼저 `AzureFirewallSubnet` 이라는 [서브넷을](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) 가상 네트워크에 추가 합니다. 그런 다음 가상 네트워크에서 [방화벽을 만들고 배포할](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) 수 있습니다.

* Azure [경로 테이블](../virtual-network/manage-route-table.md). 없으면 먼저 [경로 테이블을 만듭니다](../virtual-network/manage-route-table.md#create-a-route-table). 라우팅에 대 한 자세한 내용은 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조 하세요.

## <a name="set-up-route-table"></a>경로 테이블 설정

1. [Azure Portal](https://portal.azure.com)에서 경로 테이블을 선택 합니다. 예를 들면 다음과 같습니다.

   ![아웃 바운드 트래픽 전송 규칙을 사용 하 여 경로 테이블을 선택 합니다.](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. [새 경로를 추가](../virtual-network/manage-route-table.md#create-a-route)하려면 경로 테이블 메뉴에서 **추가** > **경로** 를 선택 합니다.

   ![아웃 바운드 트래픽에 대 한 경로 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. **경로 추가** 창에서 대상 시스템에 대 한 모든 나가는 트래픽이이 동작을 수행 하도록 지정 하는 규칙을 사용 하 여 [새 경로를 설정](../virtual-network/manage-route-table.md#create-a-route) 합니다.

   * [**가상 어플라이언스**](../virtual-network/virtual-networks-udr-overview.md#user-defined) 를 다음 홉 유형으로 사용 합니다.

   * 방화벽 인스턴스의 개인 IP 주소를 다음 홉 주소로 이동 합니다.

     이 IP 주소를 찾으려면 방화벽 메뉴에서 **개요**를 선택 하 고 **개인 IP 주소**아래의 주소를 찾습니다. 예를 들면 다음과 같습니다.

     ![방화벽 개인 IP 주소 찾기](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   이러한 규칙이 표시 되는 방법을 보여 주는 예제는 다음과 같습니다.

   ![아웃 바운드 트래픽 전송 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **경로 이름** | <*고유 경로 이름*> | 경로 테이블의 경로에 대 한 고유 이름입니다. |
   | **주소 접두사** | <*대상-주소*> | 트래픽을 이동할 대상 시스템 주소입니다. 이 주소에 대해 [클래스 없는 CIDR (도메인 간 라우팅) 표기법](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 을 사용 해야 합니다. |
   | **다음 홉 유형** | **가상 어플라이언스** | 아웃 바운드 트래픽에 사용 되는 [홉 유형](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **다음 홉 주소** | <*방화벽-개인-IP 주소*> | 방화벽에 대 한 개인 IP 주소 |
   |||

## <a name="set-up-network-rule"></a>네트워크 규칙 설정

1. Azure Portal에서 방화벽을 찾아 선택 합니다. 방화벽 메뉴의 **설정**에서 **규칙**을 선택 합니다. 규칙 창에서 네트워크 규칙 **컬렉션** > **네트워크 규칙 컬렉션 추가**를 선택 합니다.

   ![방화벽에 네트워크 규칙 컬렉션 추가](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 컬렉션에서 대상 시스템에 대 한 트래픽을 허용 하는 규칙을 추가 합니다.

   예를 들어 ISE에서 실행 되며 SFTP 시스템과 통신 해야 하는 논리 앱이 있다고 가정 합니다. 이름이 `LogicApp_ISE_SFTP_Outbound`인 네트워크 규칙 컬렉션을 만듭니다 .이 컬렉션에는 `ISE_SFTP_Outbound`라는 네트워크 규칙이 포함 되어 있습니다. 이 규칙은 사용자의 방화벽 개인 IP 주소를 사용 하 여 가상 네트워크에서 ISE가 실행 되는 서브넷의 IP 주소에서 대상 SFTP 시스템으로의 트래픽을 허용 합니다.

   ![방화벽에 대 한 네트워크 규칙 설정](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **네트워크 규칙 컬렉션 속성**

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | <*네트워크-컬렉션-이름*> | 네트워크 규칙 컬렉션의 이름입니다. |
   | **우선 순위** | <*우선 순위 수준*> | 규칙 컬렉션을 실행 하는 데 사용할 우선 순위의 순서입니다. 자세한 내용은 [몇 가지 Azure 방화벽 개념은 무엇](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)인가요?를 참조 하세요. |
   | **동작** | **허용** | 이 규칙에 대해 수행할 동작 유형입니다. |
   |||

   **네트워크 규칙 속성**

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | <*네트워크-이름*> | 네트워크 규칙의 이름 |
   | **프로토콜** | <*연결-프로토콜*> | 사용할 연결 프로토콜입니다. 예를 들어 NSG 규칙을 사용 하는 경우 **tcp 뿐만 아니라** **tcp** 와 **UDP**를 모두 선택 합니다. |
   | **원본 주소** | <*ISE-서브넷 주소*> | ISE를 실행 하는 서브넷 IP 주소와 논리 앱의 트래픽이 발생 하는 위치 |
   | **대상 주소** | <*대상-IP 주소*> | 트래픽을 이동 하려는 대상 시스템의 IP 주소 |
   | **대상 포트** | <*대상-포트*> | 대상 시스템에서 인바운드 통신에 사용 하는 모든 포트 |
   |||

   네트워크 규칙에 대 한 자세한 내용은 다음 문서를 참조 하세요.

   * [네트워크 규칙 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 방화벽 규칙 처리 논리](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 방화벽 FAQ](../firewall/firewall-faq.md)
   * [Azure PowerShell: AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az network firewall network-rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 Azure virtual networks에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)