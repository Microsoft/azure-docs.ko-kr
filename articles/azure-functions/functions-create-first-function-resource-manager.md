---
title: Azure Resource Manager 템플릿을 사용하여 첫 번째 함수 만들기
description: Azure Resource Manager 템플릿(ARM 템플릿)을 사용하여 간단한 HTTP 트리거된 서버리스 함수를 만들고 Azure에 배포합니다.
ms.date: 3/5/2020
ms.topic: quickstart
ms.service: azure-functions
ms.custom: subject-armqs
ms.openlocfilehash: 362ff5cd59982c1d848ed59af8381090344f5c5e
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642268"
---
# <a name="quickstart-create-and-deploy-azure-functions-resources-from-an-arm-template"></a>빠른 시작: ARM 템플릿에서 Azure Functions 리소스 만들기 및 배포

이 문서에서는 Azure Resource Manager 템플릿(ARM 템플릿)을 사용하여 HTTP 요청에 응답하는 함수를 만듭니다. 

이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-function-app-create-dynamic%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

### <a name="azure-account"></a>Azure 계정 

시작하기 전에 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

### <a name="create-a-local-functions-project"></a>로컬 함수 프로젝트 만들기

이 문서에서는 사용자가 만드는 Azure 리소스에서 실행할 로컬 함수 코드 프로젝트가 필요합니다. 게시할 프로젝트를 먼저 만들지 않은 경우 이 문서의 배포 섹션을 완료할 수 없습니다. 

다음 탭 중 하나를 선택하고 링크를 따라 섹션을 완료하여 선택한 언어로 함수 앱을 만듭니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[Visual Studio Code에서 로컬 함수 프로젝트 만들기](functions-create-first-function-vs-code.md#create-an-azure-functions-project)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[Visual Studio에서 로컬 함수 프로젝트 만들기](functions-create-your-first-function-visual-studio.md#create-a-function-app-project)

# <a name="command-line"></a>[명령줄](#tab/command-line)

[명령줄에서 로컬 함수 프로젝트 만들기](functions-create-first-azure-function-azure-cli.md#create-a-local-function-project)

---

프로젝트를 로컬로 만든 후에 Azure에서 새 함수를 실행하는 데 필요한 리소스를 만듭니다. 

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-function-app-create-dynamic/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-function-app-create-dynamic/azuredeploy.json":::

이 템플릿에서 생성되는 네 가지 Azure 리소스는 다음과 같습니다.

+ [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): Functions에 필요한 Azure Storage 계정을 만듭니다.
+ [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): 함수 앱에 대한 서버리스 소비 호스팅 계획을 만듭니다.
+ [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): 함수 앱을 만듭니다.
+ [**microsoft.insights/components**](/azure/templates/microsoft.insights/components): 모니터링을 위한 Application Insights 인스턴스를 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
read -p "Enter a resource group name that is used for generating resource names:" resourceGroupName &&
read -p "Enter the location (like 'eastus' or 'northeurope'):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter a resource group name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (like 'eastus' or 'northeurope')"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="validate-the-deployment"></a>배포 유효성 검사

다음으로 프로젝트를 Azure에 게시하고 함수의 HTTP 엔드포인트를 호출하여 만든 함수 앱 호스팅 리소스의 유효성을 검사합니다.

### <a name="publish-the-function-project-to-azure"></a>Azure에 함수 프로젝트 게시

다음 단계를 사용하여 새 Azure 리소스에 프로젝트를 게시합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

출력에서 HTTP 트리거의 URL을 복사합니다. 이를 사용하여 Azure에서 실행되는 함수를 테스트합니다. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시 대상 선택**에서**기존 항목 선택**으로 **Azure Functions 사용량 과금 플랜**을 선택하고 **프로필 만들기**를 선택합니다.

    :::image type="content" source="media/functions-create-first-function-arm/choose-publish-target-visual-studio.png" alt-text="기존 게시 대상 선택":::

1. **구독**을 선택하고, 리소스 그룹을 확장하고, 함수 앱을 선택하고, **확인**을 선택합니다.

1. 게시가 완료된 후 **사이트 URL**을 복사합니다.

    :::image type="content" source="media/functions-create-first-function-arm/publish-summary-site-url.png" alt-text="게시 요약에서 사이트 URL 복사":::

1. 경로 `/api/<FUNCTION_NAME>?name=Functions`를 추가합니다. 여기서 `<FUNCTION_NAME>`은 함수의 이름입니다. HTTP 트리거 함수를 호출하는 URL은 다음과 같은 형식입니다.

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=Functions`

이 URL을 사용하여 Azure에서 실행되는 HTTP 트리거 함수를 테스트합니다.

# <a name="command-line"></a>[명령줄](#tab/command-line)

Azure의 함수 앱에 로컬 코드를 게시하려면 `publish` 명령을 사용합니다.

```cmd
func azure functionapp publish <FUNCTION_APP_NAME>
```

이 예제에서는 `<FUNCTION_APP_NAME>`을 함수 앱 이름으로 바꿉니다. `az login`을 사용하여 다시 로그인해야 할 수도 있습니다. 

출력에서 HTTP 트리거의 URL을 복사합니다. 이를 사용하여 Azure에서 실행되는 함수를 테스트합니다.

---

### <a name="invoke-the-function-on-azure"></a>Azure에서 함수 호출

HTTP 요청에 대해 복사한 URL을 브라우저의 주소 표시줄에 붙여넣고, `?name=Functions`로 `name` 쿼리 문자열이 이 URL의 끝에 추가되었는지 확인한 다음, 요청을 실행합니다. 

다음과 같은 응답이 표시됩니다.

<pre>Hello Functions!</pre>

## <a name="clean-up-resources"></a>리소스 정리

다음 단계를 계속 진행하고 Azure Storage 큐 출력 바인딩을 추가하는 경우 이미 수행한 작업을 기반으로 하여 빌드되는 모든 리소스를 그대로 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --name <RESOURCE_GROUP_NAME>
```

`<RESOURCE_GROUP_NAME>`을 사용자의 리소스 그룹 이름으로 바꿉니다.

## <a name="next-steps"></a>다음 단계

첫 번째 함수를 게시했으므로 함수에 출력 바인딩을 추가하여 자세히 알아보세요.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-vs-code.md)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-vs.md)

# <a name="command-line"></a>[명령줄](#tab/command-line)

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md)

---
