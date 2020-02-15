---
title: 통합 서비스 환경에 아티팩트 추가
description: ISE (통합 서비스 환경)에 논리 앱, 통합 계정, 사용자 지정 커넥터 및 관리 되는 커넥터를 추가 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e2505d8ee8b8539f158c0a549bedfcd69a954e24
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191756"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps에서 ISE (통합 서비스 환경)에 아티팩트를 추가 합니다.

[ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만든 후에는 Azure 가상 네트워크의 리소스에 액세스할 수 있도록 논리 앱, 통합 계정 및 커넥터와 같은 아티팩트를 추가 합니다. 예를 들어 ISE를 만든 후에 사용할 수 있는 관리 되는 ISE 커넥터는 논리 앱 디자이너에 자동으로 표시 되지 않습니다. 이러한 ISE 커넥터를 사용 하려면 먼저 [해당 커넥터를 사용자의 ise에 추가 하 고 배포](#add-ise-connectors-environment) 하 여 논리 앱 디자이너에 표시 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱을 실행 하기 위해 만든 ISE입니다. ISE가 없으면 [먼저 ise를 만듭니다](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>논리 앱 만들기

ISE (통합 서비스 환경)에서 실행 되는 논리 앱을 빌드하려면 다음 단계를 수행 합니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **논리 앱** > **추가**를 선택 합니다.

   ![ISE에 새 논리 앱 추가](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 만들려는 논리 앱에 대 한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | yes | 만들 논리 앱의 이름 |
   | **구독** | yes | 사용할 Azure 구독의 이름입니다. |
   | **리소스 그룹** | yes | 사용할 Azure 리소스 그룹의 이름 (신규 또는 기존) |
   | **위치** | yes | **Integration service environment**에서 사용할 ISE를 선택 합니다 (아직 선택 하지 않은 경우). <p><p> **중요**: 논리 앱을 통합 계정으로 사용 하려면 둘 다 동일한 ISE를 사용 해야 합니다. |
   ||||

1. 완료되면 **만들기**를 선택합니다.

1. [일반적인 방법으로 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 계속 만듭니다.

   트리거 및 작업이 작동 하는 방식과 ISE를 사용 하 여 글로벌 Logic Apps 서비스에 표시 되는 방식에 대 한 차이점 [은 ise 개요에서 격리 된 vs global](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)을 참조 하세요.

1. ISE에서 논리 앱 및 API 연결을 관리 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>통합 계정 만들기

생성 시 선택 된 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) 에 따라 ise에 추가 비용 없이 특정 통합 계정 사용이 포함 됩니다. ISE (integration service environment)에 있는 논리 앱은 동일한 ISE에 있는 통합 계정만 참조할 수 있습니다. 따라서 통합 계정이 ISE에서 논리 앱과 작동 하려면 통합 계정 및 논리 앱 모두 해당 위치와 *동일한 환경을* 사용 해야 합니다. 통합 계정 및 ISEs에 대 한 자세한 내용은 [ISE와 통합 계정](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)을 참조 하세요.

ISE를 사용 하는 통합 계정을 만들려면 다음 단계를 수행 합니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **통합 계정** > **추가**를 선택 합니다.

   ![ISE에 새 통합 계정 추가](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 만들려는 논리 앱에 대 한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | yes | 만들려는 통합 계정의 이름입니다. |
   | **구독** | yes | 사용 하려는 Azure 구독의 이름입니다. |
   | **리소스 그룹** | yes | 사용할 Azure 리소스 그룹의 이름 (신규 또는 기존) |
   | **가격 책정 계층** | yes | 통합 계정에 사용할 가격 책정 계층입니다. |
   | **위치** | yes | **Integration service environment**에서 아직 선택 하지 않은 경우 논리 앱에서 사용 하는 것과 동일한 ISE를 선택 합니다. <p><p> **중요**: 논리 앱에서 통합 계정을 사용 하려면 둘 다 동일한 ISE를 사용 해야 합니다. |
   ||||

1. 완료되면 **만들기**를 선택합니다.

1. [일반적인 방법으로 통합 계정에 논리 앱을 연결](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)합니다.

1. [거래 파트너](../logic-apps/logic-apps-enterprise-integration-partners.md) 및 [계약과](../logic-apps/logic-apps-enterprise-integration-agreements.md)같은 통합 계정에 아티팩트를 추가 하 여 계속 합니다.

1. ISE에서 통합 계정을 관리 하려면 [integration service Environment 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE 커넥터 추가

ISE를 만든 후 사용할 수 있게 되는 Microsoft 관리 커넥터는 논리 앱 디자이너의 커넥터 선택에 자동으로 표시 되지 않습니다. 이러한 ISE 커넥터를 사용 하려면 먼저 이러한 커넥터를 수동으로 추가 하 고 해당 커넥터를 해당 ISE에 배포 하 여 논리 앱 디자이너에 표시 해야 합니다.

1. ISE 메뉴의 **설정**에서 **관리 되는 커넥터**를 선택 합니다. 도구 모음에서 **추가**를 선택합니다.

   ![관리 되는 커넥터 보기](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. **새 관리 커넥터 추가** 창에서 **커넥터 찾기** 목록을 엽니다. 사용할 ISE 커넥터를 선택 하지만 ISE에서 아직 배포 되지 않았습니다. **만들기**를 선택합니다.

   ![ISE에 배포할 ISE 커넥터를 선택 합니다.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   ISE에 아직 배포 되지 않은 ISE 커넥터도 사용자가 선택할 수 있는 것으로 나타납니다. ISE에 이미 배포 된 커넥터는 선택할 수 없는 것으로 나타납니다.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>사용자 지정 커넥터 만들기

ISE에서 사용자 지정 커넥터를 사용 하려면 ISE 내에서 직접 사용자 지정 커넥터를 만듭니다.

1. ISE를 찾아 엽니다 (아직 열지 않은 경우). ISE 메뉴의 **설정**에서 **사용자 지정 커넥터** > **추가**를 선택 합니다.

   ![사용자 지정 커넥터 만들기](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 사용자 지정 커넥터에 사용할 이름, Azure 구독 및 Azure 리소스 그룹 (신규 또는 기존)을 제공 합니다.

1. **위치** 목록의 **Integration service environment** 섹션에서 논리 앱이 사용 하는 동일한 ISE를 선택 하 고 **만들기**를 선택 합니다. 예를 들면 다음과 같습니다.

   ![통합 서비스 환경 선택](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 새 사용자 지정 커넥터를 선택 하 고 **편집**을 선택 합니다. 예를 들면 다음과 같습니다.

   ![사용자 지정 커넥터 선택 및 편집](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. [Openapi 정의](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) 또는 [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)에서 일반적인 방법으로 커넥터를 만들어 계속 합니다.

1. ISE에서 사용자 지정 커넥터를 관리 하려면 [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [통합 서비스 환경 관리](../logic-apps/ise-manage-integration-service-environment.md)
