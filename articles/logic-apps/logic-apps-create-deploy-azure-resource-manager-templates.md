---
title: Azure Resource Manager 템플릿-Azure Logic Apps를 사용 하 여 논리 앱 배포
description: Azure Resource Manager 템플릿을 사용 하 여 논리 앱 배포
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681758"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용 하 여 논리 앱 배포

논리 앱을 배포 하기 위한 Azure Resource Manager 템플릿을 만들면 다음과 같은이 방법으로 템플릿을 배포할 수 있습니다.

* [Azure Portal](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure 리소스 관리자 REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure 파이프라인](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Azure portal을 통해 배포

논리 앱 템플릿을 Azure에 자동으로 배포 하려면 다음을 선택할 수 있습니다 **Azure에 배포** 단추를 Azure portal에 로그인 하 고 논리 앱에 대 한 정보에 대 한 라는 메시지가 표시 됩니다. 그런 다음 논리 앱 템플릿 또는 매개 변수에 필요한 내용을 가능 합니다.

[![Azure에 배포](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

예를 들어 묻는이 정보에 대 한 Azure portal에 로그인 한 후:

* Azure 구독 이름
* 사용하려는 리소스 그룹
* 논리 앱 위치
* 논리 앱의 이름
* 테스트 URI
* 지정된 사용 약관 동의 여부

자세한 내용은 [Azure Resource Manager 템플릿 및 Azure portal을 사용 하 여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-portal.md)합니다.

## <a name="authorize-oauth-connections"></a>OAuth 연결 권한 부여

배포 후 논리 앱은 유효한 매개 변수와 함께 종단 간에 적용됩니다. 그러나 OAuth 연결이 유효한 액세스 토큰을 생성하도록 권한을 부여해야 합니다. 자동화 된 배포의 경우 이와 같은 각 OAuth 연결에 동의 하는 스크립트를 사용할 수 있습니다 [예제 프로젝트의에서 스크립트는 GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth)합니다. Logic Apps 디자이너에서 논리 앱을 열어도 Visual Studio 또는 Azure portal을 통해 OAuth 연결 권한을 부여할 수 있습니다.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Azure PowerShell을 사용하여 배포

특정 배포 *Azure 리소스 그룹*,이 명령을 사용 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

특정 Azure 구독에 배포 하려면이 명령을 사용 합니다.

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

특정 배포 *Azure 리소스 그룹*,이 명령을 사용 합니다.

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

특정 Azure 구독에 배포 하려면이 명령을 사용 합니다.

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

자세한 내용은 다음 항목을 참조하세요. 

* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Azure DevOps를 사용 하 여 배포

논리 앱 템플릿을 배포 하 고 환경 관리, 팀은 일반적으로 도구를 사용과 같은 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 에 [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)합니다. Azure 파이프라인 제공을 [Azure 리소스 그룹 배포 작업](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) 모든 빌드에 추가 하거나 릴리스 파이프라인 수 있습니다.
배포 및 릴리스 파이프라인을 생성 하는 권한 부여를 위해 또한 Azure Active Directory (AD)가 필요한 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md)합니다. 에 대해 자세히 알아보세요 [서비스 주체를 사용 하 여 Azure 파이프라인과](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)합니다. 

Azure 파이프라인을 사용 하 여 일반 대략적인 단계는 다음과 같습니다.

1. Azure 파이프라인에서 빈 파이프라인을 만듭니다.

1. 논리 앱 템플릿 및 수동으로 또는 빌드 프로세스의 일부로 생성 하는 템플릿 매개 변수 파일을 같은 파이프라인에 필요한 리소스를 선택 합니다.

1. 에이전트 작업에 대해 찾기 및 추가 합니다 **Azure 리소스 그룹 배포** 작업 합니다.

   !["Azure 리소스 그룹 배포" 작업 추가](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. 사용 하 여 구성 된 [서비스 주체](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)합니다. 

1. 논리 앱 템플릿 및 템플릿 매개 변수 파일에 대 한 참조를 추가 합니다.

1. 릴리스 프로세스에서 다른 환경, 자동화된 테스트 또는 승인자에 대한 단계를 필요에 따라 계속 작성합니다.

## <a name="get-support"></a>지원 받기

질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)
