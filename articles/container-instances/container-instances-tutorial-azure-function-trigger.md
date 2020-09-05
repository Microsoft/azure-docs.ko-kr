---
title: 자습서 - Azure 함수별 컨테이너 그룹 트리거
description: Azure 컨테이너 인스턴스 만들기를 자동화하는 HTTP 트리거 서버리스 PowerShell 함수 만들기
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ec4b2273f6be6ea4aabed2b660e0b7553f861d0d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072046"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>자습서: HTTP 트리거 Azure 함수를 사용하여 컨테이너 그룹 만들기

[Azure Functions](../azure-functions/functions-overview.md)는 HTTP 요청, 타이머, Azure Storage 큐의 메시지 같은 다양한 이벤트에 응답하여 스크립트나 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다.

이 자습서에서는 HTTP 요청을 받아서 [컨테이너 그룹](container-instances-container-groups.md)의 배포를 트리거하는 Azure 함수를 만듭니다. 이 예제에서는 Azure Functions를 사용하여 Azure Container Instances에서 자동으로 리소스를 만드는 기본적인 방법을 보여줍니다. 좀 더 복잡한 시나리오 또는 기타 이벤트 트리거를 원하는 경우 예제를 수정하거나 확장하세요. 

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)과 함께 Visual Studio Code를 사용하여 기본 HTTP 트리거 PowerShell 함수를 만듭니다.
> * 함수 앱에서 ID를 사용하도록 설정하고 Azure 리소스를 만들 수 있는 권한을 ID에 부여합니다.
> * 단일 컨테이너 컨테이너 그룹의 배포를 자동화하도록 PowerShell 함수를 수정하고 다시 게시합니다.
> * 컨테이너의 HTTP 트리거 배포를 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

OS에서 Azure Functions 확장을 사용하여 Visual Studio Code를 설치하고 사용하기 위한 필수 구성 요소는 [ Visual Studio Code를 사용하여 Azure에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell#configure-your-environment)를 참조하세요.

이 문서의 추가 단계에서는 Azure PowerShell을 사용합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치][azure-powershell-install] 및 [Azure에 로그인](/powershell/azure/get-started-azureps#sign-in-to-azure)을 참조하세요.

## <a name="create-a-basic-powershell-function"></a>기본 PowerShell 함수 만들기

[Azure에서 첫 번째 PowerShell 함수 만들기](../azure-functions/functions-create-first-function-vs-code.md?pivots=programming-language-powershell)의 단계에 따라 HTTP 트리거 템플릿을 사용하여 PowerShell 함수를 만듭니다. 기본 Azure 함수 이름 **HttpTrigger**를 사용합니다. 빠른 시작에서 볼 수 있듯이, 함수를 로컬로 테스트하고 Azure의 함수 앱에 프로젝트를 게시합니다. 이 예제는 텍스트 문자열을 반환하는 기본 HTTP 트리거 함수입니다. 이 문서의 뒷부분에서는 컨테이너 그룹을 만들도록 함수를 수정합니다.

이 문서에서는 함수 앱 이름(*myfunctionapp*)에 따라 자동으로 이름이 지정된 Azure 리소스 그룹에서 *myfunctionapp*이라는 이름을 사용하여 프로젝트를 게시한다고 가정합니다. 이후 단계에서 각자 함수 앱 이름 및 리소스 그룹 이름을 바꾸세요.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>함수 앱에서 Azure 관리 ID 사용

다음 명령은 함수 앱에서 시스템 할당 [관리 ID](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity)를 사용하도록 설정합니다. 앱을 실행하는 PowerShell 호스트는 이 ID를 사용하여 Azure에 자동으로 인증할 수 있으며, 함수는 ID에 액세스 권한이 부여된 Azure 서비스에 대한 작업을 수행할 수 있습니다. 이 자습서에서는 함수 앱의 리소스 그룹에 리소스를 만들 수 있는 권한을 관리 ID에 부여합니다. 

함수 앱에 [ID를 추가](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1)합니다.

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

리소스 그룹에 범위가 지정된 기여자 역할의 ID를 할당합니다.

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>HttpTrigger 함수 수정

컨테이너 그룹을 만들도록 **HttpTrigger** 함수에 대한 PowerShell 코드를 수정합니다. 함수의 `run.ps1` 파일에서 다음 코드 블록을 찾습니다. 다음 코드는 이름 값이 함수 URL에서 쿼리 문자열로 전달되는 경우 이름 값을 표시합니다.

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

이 코드를 다음 예제 블록으로 바꿉니다. 여기서, 이름 값이 쿼리 문자열에 전달되는 경우 이름 값은 [New-AzContainerGroup][new-azcontainergroup] cmdlet을 사용하여 이름을 지정하고 컨테이너 그룹을 만드는 데 사용됩니다. 리소스 그룹 이름 *myfunctionapp*을 함수 앱의 리소스 그룹 이름으로 바꿔야 합니다.

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

이 예제에서는 `alpine` 이미지를 실행하는 단일 컨테이너 인스턴스로 구성된 컨테이너 그룹을 만듭니다. 컨테이너는 단일 `echo` 명령을 실행한 후 종료됩니다. 실제 예제에서는 배치 작업을 실행하기 위해 하나 이상의 컨테이너 그룹 만들기를 트리거할 수 있습니다.
 
## <a name="test-function-app-locally"></a>로컬로 함수 앱 테스트

함수 앱 프로젝트를 Azure에 다시 게시하기 전에 함수가 로컬로 실행되는지 확인합니다. 로컬로 실행하는 경우 함수는 Azure 리소스를 만들지 않습니다. 그러나 쿼리 문자열에 이름 값을 전달하거나 전달하지 않고 함수 흐름을 테스트할 수 있습니다. 함수를 디버깅하려면 [로컬로 PowerShell Azure Functions 디버그](../azure-functions/functions-debug-powershell-local.md)를 참조하세요.

## <a name="republish-azure-function-app"></a>Azure 함수 앱 다시 게시

함수가 로컬로 실행되는지 확인한 후 Azure의 기존 함수 앱에 프로젝트를 다시 게시합니다.

1. Visual Studio Code에서 명령 팔레트를 엽니다. `Azure Functions: Deploy to Function App...`을 검색하고 선택합니다.
1. 압축하여 배포할 현재 작업 폴더를 선택합니다.
1. 구독을 선택한 다음, 기존 함수 앱의 이름(*myfunctionapp*)을 선택합니다. 이전 배포를 덮어쓸 것인지 확인합니다.

함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 이 알림에서 **출력 보기**를 선택하여 여러분이 업데이트한 Azure 리소스를 비롯한 만들기 및 배포 결과를 살펴봅니다.

## <a name="run-the-function-in-azure"></a>Azure에서 함수 실행

배포가 성공적으로 완료되면 함수 URL을 가져옵니다. 예를 들어 Visual Studio Code의 **Azure: Functions** 영역을 사용하여 **HttpTrigger** 함수 URL을 복사하거나 [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function)에서 함수 URL을 가져옵니다.

함수 URL의 형식은 다음과 같습니다.

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>이름을 전달하지 않고 함수 실행

첫 번째 테스트로, `curl` 명령을 실행하고 `name` 쿼리 문자열을 추가하지 않고 함수 URL을 전달합니다. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

함수가 다음과 같이 상태 코드 200 및 `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` 텍스트를 반환합니다.

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>함수를 실행하고 컨테이너 그룹의 이름 전달

이제 `curl` 명령을 실행하고 컨테이너 그룹의 이름(*mycontainergroup*)을 쿼리 문자열 `?name=mycontainergroup`으로 추가합니다.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

함수가 다음과 같이 상태 코드 200을 반환하고 컨테이너 그룹 만들기를 트리거합니다.

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

다음과 같이 [Get-AzContainerInstanceLog][get-azcontainerinstancelog] 명령을 사용하여 컨테이너가 실행되었는지 확인합니다.

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

샘플 출력:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스가 더 이상 필요 없는 경우 [az group delete][az-group-delete] 명령을 실행하여 리소스 그룹과 그 안에 들어 있는 모든 리소스를 제거할 수 있습니다. 이 명령은 실행 중인 컨테이너뿐만 아니라 생성한 함수 앱 및 모든 관련 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 HTTP 요청을 받아서 컨테이너 그룹의 배포를 트리거하는 Azure 함수를 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Functions 확장과 함께 Visual Studio Code를 사용하여 기본 HTTP 트리거 PowerShell 함수를 만듭니다.
> * 함수 앱에서 ID를 사용하도록 설정하고 Azure 리소스를 만들 수 있는 권한을 ID에 부여합니다.
> * 단일 컨테이너 컨테이너 그룹의 배포를 자동화하도록 PowerShell 함수 코드를 수정합니다.
> * 컨테이너의 HTTP 트리거 배포를 확인합니다.

컨테이너화된 작업을 시작하고 모니터링하는 방법에 대한 자세한 예제는 블로그 게시물 [Event-Driven Serverless Containers with PowerShell Azure Functions and Azure Container Instances(PowerShell Azure Functions 및 Azure Container Instances를 사용하는 이벤트 구동 서버리스 컨테이너)](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) 및 함께 제공되는 [코드 샘플](https://github.com/anthonychu/functions-powershell-run-aci)을 참조하세요.

Azure 함수를 만들고 함수 프로젝트를 게시하는 방법에 대한 자세한 지침은 [Azure Functions 설명서](../azure-functions/index.yml)를 참조하세요. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
