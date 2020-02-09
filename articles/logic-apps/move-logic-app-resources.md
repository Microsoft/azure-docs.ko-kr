---
title: 구독, 리소스 그룹 또는 지역에서 논리 앱 이동
description: 논리 앱 또는 통합 계정을 다른 Azure 구독, 리소스 그룹 또는 위치 (지역)로 마이그레이션
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: d6250238edd15126e7a56bd821fbd1c736ebda07
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965882"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>논리 앱 리소스를 다른 Azure 구독, 리소스 그룹 또는 지역으로 이동

논리 앱 또는 관련 리소스를 다른 Azure 구독, 리소스 그룹 또는 지역으로 이동 하기 위해 Azure Portal, Azure PowerShell, Azure CLI, REST API 등의 여러 가지 방법으로 이러한 작업을 완료할 수 있습니다. 리소스를 이동 하기 전에 다음 사항을 검토 하십시오. 

* [특정 논리 앱 리소스 유형만](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) Azure 리소스 그룹 또는 구독 간에 이동할 수 있습니다.

* Azure 구독 및 각 Azure 지역에서 사용할 수 있는 논리 앱 리소스의 수에 대 한 [제한을](../logic-apps/logic-apps-limits-and-config.md) 확인 합니다. 이러한 제한은 지역이 구독 또는 리소스 그룹에서 동일 하 게 유지 될 때 특정 리소스 종류를 이동할 수 있는지 여부에 영향을 줍니다. 예를 들어 각 Azure 구독에서 각 Azure 지역에 대해 무료 계층 통합 계정을 하나만 사용할 수 있습니다.

* 리소스를 이동 하면 Azure에서 새 리소스 Id를 만듭니다. 따라서 새 Id를 대신 사용 하 여 이동 된 리소스와 관련 된 스크립트나 도구를 업데이트 해야 합니다. 구독, 리소스 그룹 또는 지역 간에 논리 앱을 이동한 후에는 모든 OAuth 기반 연결을 다시 만들거나 다시 인증 해야 합니다.

## <a name="prerequisites"></a>필수 조건

* 이동 하려는 논리 앱 또는 통합 계정을 만드는 데 사용 된 것과 동일한 Azure 구독

* 리소스 소유자 권한을 사용 하 여 원하는 리소스를 이동 하 고 설정할 수 있습니다. [RBAC (역할 기반 액세스 제어)](../role-based-access-control/built-in-roles.md#owner)에 대해 자세히 알아보세요.

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>구독 간에 리소스 이동

논리 앱 또는 통합 계정과 같은 리소스를 다른 Azure 구독으로 이동 하려면 Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용할 수 있습니다. 이러한 단계는 리소스의 지역이 동일 하 게 유지 될 때 사용할 수 있는 Azure Portal를 포함 합니다. 다른 단계 및 일반 준비는 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 이동 하려는 논리 앱 리소스를 찾아 선택 합니다.

1. 리소스의 **개요** 페이지에서 **구독**옆에 있는 **변경** 링크를 선택 합니다.

1. **리소스 이동** 페이지에서 이동 하려는 논리 앱 리소스 및 관련 리소스를 선택 합니다.

1. **구독** 목록에서 대상 구독을 선택 합니다.

1. **리소스 그룹** 목록에서 대상 리소스 그룹을 선택 합니다. 또는 다른 리소스 그룹을 만들려면 **새 그룹 만들기**를 선택 합니다.

1. 이동 된 리소스와 관련 된 스크립트나 도구가 새 리소스 Id로 업데이트 될 때까지 작동 하지 않는다는 점을 확인 하려면 확인 상자를 선택한 다음 **확인**을 선택 합니다.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>리소스 그룹 간 리소스 이동

논리 앱 또는 통합 계정과 같은 리소스를 다른 Azure 리소스 그룹으로 이동 하려면 Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용할 수 있습니다. 이러한 단계는 리소스의 지역이 동일 하 게 유지 될 때 사용할 수 있는 Azure Portal를 포함 합니다. 다른 단계 및 일반 준비는 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조 하세요.

실제로 그룹 간에 리소스를 이동 하기 전에 리소스를 다른 그룹으로 이동할 수 있는지 여부를 테스트할 수 있습니다. 자세한 내용은 [이동 유효성 검사](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 이동 하려는 논리 앱 리소스를 찾아 선택 합니다.

1. 리소스의 **개요** 페이지에서 **리소스 그룹**옆에 있는 **변경** 링크를 선택 합니다.

1. **리소스 이동** 페이지에서 이동 하려는 논리 앱 리소스 및 관련 리소스를 선택 합니다.

1. **리소스 그룹** 목록에서 대상 리소스 그룹을 선택 합니다. 또는 다른 리소스 그룹을 만들려면 **새 그룹 만들기**를 선택 합니다.

1. 이동 된 리소스와 관련 된 스크립트나 도구가 새 리소스 Id로 업데이트 될 때까지 작동 하지 않는다는 점을 확인 하려면 확인 상자를 선택한 다음 **확인**을 선택 합니다.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>영역 간 리소스 이동

논리 앱을 다른 지역으로 이동 하려는 경우 논리 앱을 만든 방법에 따라 옵션이 달라 집니다. 선택한 옵션에 따라 논리 앱에서 연결을 다시 만들거나 다시 인증 해야 합니다.

* Azure Portal에서 새 지역에 논리 앱을 다시 만들고 워크플로 설정을 다시 구성 합니다. 시간을 절약 하기 위해 원본 앱에서 대상 앱으로 기본 워크플로 정의 및 연결을 복사할 수 있습니다. 논리 앱 뒤의 "코드"를 보려면 논리 앱 디자이너 도구 모음에서 **코드 보기**를 선택 합니다.

* Visual Studio 및 Visual Studio 용 Azure Logic Apps 도구를 사용 하 여 Azure Portal에서 [논리 앱을 열고](../logic-apps/manage-logic-apps-with-visual-studio.md) [Azure Resource Manager 템플릿으로](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)다운로드할 수 있습니다. 이 템플릿은 대부분 배포할 준비가 되었으며 워크플로 자체와 연결을 포함 하 여 논리 앱에 대 한 리소스 정의를 포함 합니다. 또한 템플릿은 배포에 사용할 값에 대 한 매개 변수를 선언 합니다. 이렇게 하면 요구에 따라 논리 앱을 배포 하는 위치와 방법을 보다 쉽게 변경할 수 있습니다. 배포에 필요한 위치 및 기타 필요한 정보를 지정 하려면 별도의 매개 변수 파일을 사용할 수 있습니다.

* Azure DevOps의 Azure Pipelines와 같은 CI (지속적인 통합) 및 CD (지속적인 업데이트) 도구를 사용 하 여 논리 앱을 만들고 배포한 경우 해당 도구를 사용 하 여 다른 지역에 앱을 배포할 수 있습니다.

논리 앱의 배포 템플릿에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [개요: Azure Resource Manager 템플릿을 사용 하 여 Azure Logic Apps에 대 한 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Portal에서 Visual Studio로 논리 앱을 찾고 열고 다운로드 합니다.](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Logic Apps에 대 한 Azure Resource Manager 템플릿 만들기](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps에 대 한 Azure Resource Manager 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>관련 리소스

Azure의 온-프레미스 데이터 게이트웨이 리소스와 같은 일부 Azure 리소스는 해당 리소스를 사용 하는 논리 앱과 다른 지역에 있을 수 있습니다. 그러나 연결 된 통합 계정과 같은 다른 Azure 리소스는 논리 앱과 동일한 지역에 있어야 합니다. 시나리오에 따라 논리 앱이 동일한 지역에 있는 것으로 간주 되는 리소스에 액세스할 수 있는지 확인 합니다.

예를 들어 통합 계정에 논리 앱을 연결 하려면 두 리소스가 동일한 지역에 있어야 합니다. 재해 복구와 같은 시나리오에서는 일반적으로 동일한 구성 및 아티팩트가 있는 통합 계정을 원합니다. 다른 시나리오에서는 구성 및 아티팩트가 서로 다른 통합 계정이 필요할 수 있습니다.

Azure Logic Apps의 사용자 지정 커넥터는 커넥터 작성자와 동일한 Azure 구독 및 동일한 Azure Active Directory 테넌트를 가진 사용자에 게 표시 됩니다. 이러한 커넥터는 논리 앱을 배포 하는 동일한 지역에서 사용할 수 있습니다. 자세한 내용은 [조직에서 사용자 지정 커넥터 공유](https://docs.microsoft.com/connectors/custom-connectors/share)를 참조하세요.

Visual Studio에서 가져오는 템플릿에는 논리 앱 및 해당 연결에 대 한 리소스 정의만 포함 됩니다. 따라서 논리 앱에서 다른 리소스를 사용 하는 경우, 예를 들어 파트너, 규약, 스키마 등의 B2B 아티팩트와 통합 계정을 사용 하는 경우 Azure Portal를 사용 하 여 해당 통합 계정의 템플릿을 내보내야 합니다. 이 템플릿에는 통합 계정 및 아티팩트에 대 한 리소스 정의가 포함 되어 있습니다. 그러나 템플릿은 완전히 매개 변수화 되지 않습니다. 따라서 배포에 사용 하려는 값을 수동으로 매개 변수화 해야 합니다.

### <a name="export-templates-for-integration-accounts"></a>통합 계정에 대 한 템플릿 내보내기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾아 엽니다.

1. 통합 계정의 메뉴에 있는 **설정**에서 **템플릿 내보내기**를 선택 합니다.

1. 도구 모음에서 **다운로드**를 선택 하 고 템플릿을 저장 합니다.

1. 템플릿을 열고 편집 하 여 배포에 필요한 값을 매개 변수화 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 리소스를 새 리소스 그룹 또는 구독으로 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)