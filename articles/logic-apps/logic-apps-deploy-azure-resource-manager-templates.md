---
title: 논리 앱 템플릿 배포
description: Azure Logic Apps에 대해 만든 Azure Resource Manager 템플릿을 배포 하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 81fad94dc02bd57f839d7ab8653bec7074e41800
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076343"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps에 대한 Azure Resource Manager 템플릿 배포

논리 앱에 대 한 Azure Resource Manager 템플릿을 만든 후에 다음과 같은 방법으로 템플릿을 배포할 수 있습니다.

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure Portal를 통해 배포

논리 앱 템플릿을 Azure에 자동으로 배포 하려면 다음 **azure에 배포** 단추를 선택 하 여 Azure Portal에 로그인 하 고 논리 앱에 대 한 정보를 묻는 메시지를 표시 합니다. 그런 다음 논리 앱 템플릿이나 매개 변수를 필요한 대로 변경할 수 있습니다.

[![Azure에 배포](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

예를 들어 Azure Portal에 로그인 한 후 다음 정보를 입력 하 라는 메시지가 표시 됩니다.

* Azure 구독 이름
* 사용하려는 리소스 그룹
* 논리 앱 위치
* 논리 앱의 이름
* 테스트 URI
* 지정된 사용 약관 동의 여부

자세한 내용은 다음 항목을 참조하세요.

* [개요: Azure Resource Manager 템플릿을 사용 하 여 논리 앱에 대 한 배포 자동화](logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager 템플릿 및 Azure Portal를 사용 하 여 리소스 배포](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Visual Studio를 사용하여 배포

Visual Studio를 사용 하 여 만든 Azure 리소스 그룹 프로젝트에서 논리 앱 템플릿을 배포 하려면 다음 단계에 따라 Azure에 [논리 앱을 수동으로 배포](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) 합니다.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

특정 *Azure 리소스 그룹*에 배포 하려면 다음 명령을 사용 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

특정 *Azure 리소스 그룹*에 배포 하려면 다음 명령을 사용 합니다.

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요.

* [리소스 관리자 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps를 사용 하 여 배포

논리 앱 템플릿을 배포 하 고 환경을 관리 하기 위해 일반적으로 팀은 [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services)의 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 와 같은 도구를 사용 합니다. Azure Pipelines는 빌드 또는 릴리스 파이프라인에 추가할 수 있는 [Azure 리소스 그룹 배포 작업](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) 을 제공 합니다. 릴리스 파이프라인을 배포 하 고 생성 하기 위한 권한 부여를 위해 AD (Azure Active Directory) [서비스 사용자](../active-directory/develop/app-objects-and-service-principals.md)도 필요 합니다. [Azure Pipelines에서 서비스 사용자를 사용 하는](/azure/devops/pipelines/library/connect-to-azure)방법에 대해 자세히 알아보세요.

Azure Pipelines를 사용 하는 Azure Resource Manager 템플릿의 CI/CD에 대 한 지속적인 통합 및 지속적인 배포에 대 한 자세한 내용은 다음 항목 및 샘플을 참조 하세요.

* [Azure Pipelines와 리소스 관리자 템플릿 통합](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [자습서: Azure Pipelines를 사용한 Azure Resource Manager 템플릿의 지속적인 통합](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [샘플: Azure Logic Apps에서 Azure Service Bus 큐에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 Azure Storage 계정에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure DevOps에서 Azure Logic Apps에 대 한 함수 앱 작업 설정 및 Azure Pipelines를 사용 하 여 배포](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [샘플: Azure Logic Apps에서 통합 계정에 연결 하 고 Azure DevOps에서 Azure Pipelines를 사용 하 여 배포](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [샘플: Azure Logic Apps를 사용 하 여 Azure Pipelines 오케스트레이션](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Azure Pipelines 사용에 대 한 일반적인 개략적인 단계는 다음과 같습니다.

1. Azure Pipelines에서 빈 파이프라인을 만듭니다.

1. 수동으로 생성 하거나 빌드 프로세스의 일부로 생성 하는 논리 앱 템플릿 및 템플릿 매개 변수 파일과 같이 파이프라인에 필요한 리소스를 선택 합니다.

1. 에이전트 작업에 대해 **Azure 리소스 그룹 배포** 작업을 찾아 추가 합니다.

   !["Azure 리소스 그룹 배포" 작업 추가](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. [서비스 주체로](/azure/devops/pipelines/library/connect-to-azure)구성 합니다.

1. 논리 앱 템플릿 및 템플릿 매개 변수 파일에 대 한 참조를 추가 합니다.

1. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후에는 논리 앱이 유효한 매개 변수를 사용 하 여 종단 간에 작동 하지만 [자격 증명을 인증](../active-directory/develop/authentication-vs-authorization.md)하는 데 유효한 액세스 토큰을 생성 하려면 여전히 미리 인증 된 OAuth 연결에 권한을 부여 하거나 사용 해야 합니다. 그러나 API 연결 리소스를 한 번만 배포 및 인증 하면 됩니다. 즉, 연결 정보를 업데이트 해야 하는 경우가 아니면 후속 배포에 이러한 연결 리소스를 포함할 필요가 없습니다. 연속 통합 및 연속 배포 파이프라인을 사용 하는 경우 업데이트 된 Logic Apps 리소스만 배포 하 고 매번 연결을 다시 인증 필요가 없습니다.

다음은 권한 부여 연결을 처리 하기 위한 몇 가지 제안 사항입니다.

* 동일한 지역에 있는 논리 앱에서 API 연결 리소스를 사전에 부여 하 고 공유 합니다. API 연결은 논리 앱과는 별도로 Azure 리소스로 존재 합니다. 논리 앱은 API 연결 리소스에 종속 되어 있지만 API 연결 리소스는 논리 앱에 대 한 종속성이 없으며 종속 논리 앱을 삭제 한 후에도 유지 됩니다. 또한 논리 앱은 다른 리소스 그룹에 존재 하는 API 연결을 사용할 수 있습니다. 그러나 논리 앱 디자이너는 논리 앱과 동일한 리소스 그룹에만 API 연결을 만들 수 있도록 지원 합니다.

  > [!NOTE]
  > API 연결 공유를 고려 하는 경우 솔루션이 [잠재적 제한 문제를 처리할](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)수 있는지 확인 합니다. 제한은 연결 수준에서 발생 하므로 여러 논리 앱에서 동일한 연결을 다시 사용 하면 제한 문제가 발생할 가능성이 높아질 수 있습니다.

* Multi-factor authentication을 요구 하는 서비스 및 시스템이 시나리오에 포함 되지 않은 경우 PowerShell 스크립트를 사용 하 여, 이미 제공 된 권한 부여 및 동의가 있는 활성 브라우저 세션이 있는 가상 머신에서 일반 사용자 계정으로 연속 통합 작업자를 실행 하 여 각 OAuth 연결에 대 한 동의를 제공할 수 있습니다. 예를 들어 [Logic Apps GitHub 리포지토리에서 LogicAppConnectionAuth 프로젝트](https://github.com/logicappsio/LogicAppConnectionAuth)에서 제공 하는 샘플 스크립트의 용도를 선택할 수 있습니다.

* Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 열어 OAuth 연결에 대 한 권한을 수동으로 부여 합니다.

* 연결 권한을 부여 하는 대신 Azure Active Directory (Azure AD) [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md) 를 사용 하는 경우 [논리 앱 템플릿에서 서비스 주체 매개 변수를 지정](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)하는 방법을 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 모니터링](../logic-apps/monitor-logic-apps.md)
