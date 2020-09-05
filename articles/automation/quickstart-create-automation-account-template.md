---
title: '빠른 시작: Automation 계정 만들기 - Azure 템플릿'
titleSuffix: Azure Automation
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Automation 계정을 만드는 방법을 보여 줍니다.
services: automation
documentationcenter: na
author: mgoedtel
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.service: automation
ms.devlang: na
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 07/23/2020
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 441e53123512a9611f9568edccfd40a3e17c981f
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055433"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Automation 계정 만들기

Azure Automation은 Azure 및 Azure 이외의 환경에서 일관된 관리를 지원하는 클라우드 기반 자동화 및 구성 서비스를 제공하며, 이 빠른 시작에서는 Automation 계정을 만드는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하는 방법을 보여 줍니다. ARM 템플릿을 사용하면 다른 배포 방법과 비교하여 단계가 줄어듭니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 샘플 템플릿에서는 다음을 수행합니다.

* Azure Monitor Log Analytics 작업 영역 만들기를 자동화합니다.
* Azure Automation 계정 만들기를 자동화합니다.
* Automation 계정을 Log Analytics 작업 영역에 연결합니다.
* 계정에 샘플 Automation Runbook을 추가합니다.

>[!NOTE]
>ARM 템플릿을 사용하는 경우 Automation 실행 계정 만들기가 지원되지 않습니다. 포털에서 또는 PowerShell을 사용하여 실행 계정을 수동으로 만들려면 [실행 계정 관리](manage-runas-account.md)를 참조하세요.

이러한 단계를 완료한 후에는 Automation 계정에 대한 [진단 설정을 구성](automation-manage-send-joblogs-log-analytics.md)하여 Runbook 작업 상태 및 작업 스트림을 연결된 Log Analytics 작업 영역으로 보내도록 해야 합니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-automation/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API 버전

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| [작업 영역](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation 계정](/azure/templates/microsoft.automation/automationaccounts) | Automation | 2020-01-13-preview |
| [작업 영역 연결된 서비스](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>템플릿을 사용하기 전에 확인해야 할 사항

JSON 매개 변수 템플릿은 다음을 지정할 수 있도록 구성됩니다.

* 작업 영역의 이름
* 작업 영역을 만들 지역
* Automation 계정의 이름
* Automation 계정을 만들 지역

템플릿의 다음 매개 변수는 Log Analytics 작업 영역의 기본값으로 설정됩니다.

* *sku*는 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값입니다.
* *dataRetention*은 기본값이 30일입니다.

>[!WARNING]
>2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성하려면 유효한 유일한 Log Analytics 가격 책정 계층은 *PerGB2018*입니다.
>

JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대해서는 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [ARM 템플릿과 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

Azure Automation 및 Azure Monitor를 처음 접하는 경우 다음 구성 정보를 이해하는 것이 중요합니다. 새 자동화 계정에 연결된 Log Analytics 작업 영역을 만들고 구성하고 사용하려고 할 때 오류를 방지하는 데 도움이 될 수 있습니다.

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)를 검토하세요.

* [작업 영역 매핑](how-to/region-mappings.md)을 검토하여 지원되는 지역 인라인 또는 매개 변수 파일에서 지정합니다. 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.

* Azure Monitor 로그를 처음 접하고 아직 작업 영역을 배포하지 않은 경우 [작업 영역 디자인 지침](../azure-monitor/platform/design-logs-deployment.md)을 검토해야 합니다. 이 지침은 액세스 제어에 대해 알아보고 조직에 권장되는 디자인 구현 전략을 이해하는 데 도움이 됩니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 Azure Automation 계정, Log Analytics 작업 영역을 만들고 Automation 계정을 작업 영역에 연결합니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. 값을 입력합니다.

3. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 출력됩니다.

    ![배포가 완료되었을 때 결과 예](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 방금 만든 Automation 계정을 엽니다. 

3. 왼쪽 창에서 **Runbook**을 선택합니다. **Runbook** 페이지에서 Automation 계정을 사용하여 만든 세 가지 자습서 Runbook이 나열됩니다.

    ![Automation 계정을 사용하여 만든 자습서 Runbook](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. 왼쪽 창에서 **연결된 작업 영역**을 선택합니다. **연결된 작업 영역** 페이지에서 이전에 Automation 계정에 연결된 Log Analytics 작업 영역을 표시합니다.

    ![Log Analytics 작업 영역에 연결된 Automation 계정](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제한 다음, Automation 계정 및 작업 영역을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Automation 계정, Log Analytics 작업 영역을 만들고 함께 연결했습니다.

자세히 알아보려면 Azure Automation에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Automation 자습서](learn/automation-tutorial-runbook-graphical.md)
