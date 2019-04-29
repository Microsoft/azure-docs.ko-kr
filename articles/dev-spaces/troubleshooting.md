---
title: 문제 해결
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 044e997703f5b274215fb05c7152186948b331b4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761410"
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

Azure 개발 공간을 사용할 때 문제가 있으면 만들는 [Azure 개발 공간 GitHub 리포지토리에 문제](https://github.com/Azure/dev-spaces/issues)합니다.

## <a name="enabling-detailed-logging"></a>자세한 로깅 사용

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 TEMP 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 TEMP 디렉터리는 일반적으로 `/tmp`입니다.

## <a name="debugging-services-with-multiple-instances"></a>여러 인스턴스를 사용하여 서비스 디버깅

현재 Azure Dev Spaces는 단일 인스턴스 또는 Pod를 디버그할 때 가장 원활하게 작동합니다. azds.yaml 파일에는 Kubernetes가 서비스용으로 실행하는 Pod 수를 나타내는 설정인 *replicaCount*가 포함되어 있습니다. 지정된 서비스용으로 여러 개의 Pod를 실행하도록 앱을 구성하기 위해 replicaCount를 변경하면 디버거는 첫 번째 Pod(사전순으로 나열된 경우)에 연결됩니다. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>'Azure Dev Spaces 컨트롤러를 만들지 못했음' 오류

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

### <a name="try"></a>다음을 시도해 보세요.

컨트롤러를 삭제하려면 Azure Dev Spaces CLI를 사용합니다. Visual Studio 또는 Cloud Shell에서는 작업할 수 없습니다. AZDS CLI를 설치하려면 먼저 Azure CLI를 설치한 다음 이 명령을 실행합니다.

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

그런 다음 이 명령을 실행하여 컨트롤러를 삭제합니다.

```cmd
azds remove -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. 처음 만드는 것처럼 이 자습서의 지침에 따릅니다.


## <a name="error-service-cannot-be-started"></a>'서비스를 시작할 수 없습니다.' 오류

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 자세한 진단 정보를 가져오려면 명령 및 설정을 다음과 같이 변경합니다.

### <a name="try"></a>다음을 시도해 보세요.

명령줄에서:

_azds.exe_를 사용하는 경우 --verbose 명령줄 옵션을 사용하고 --output 명령줄 옵션을 사용하여 출력 형식을 지정합니다.
 
```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>다단계 Dockerfile:
다단계 Dockerfile을 사용하는 경우 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

이 오류는 AKS 노드가 다단계 빌드를 지원하지 않는 이전 버전의 Docker를 실행하기 때문에 발생합니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행
이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

이 오류는 Dev Spaces 컨트롤러를 제거해도 해당 컨트롤러에서 이전에 설치한 서비스는 제거되지 않기 때문에 발생합니다. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--public` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다.

### <a name="try"></a>다음을 시도해 보세요.

다음 명령을 사용하여 Dev Spaces 서비스에 연결된 모든 URL을 나열할 수 있습니다.

```cmd
azds list-uris
```

URL이 *보류 중* 상태이면 Dev Spaces가 DNS 등록 완료를 대기 중임을 의미합니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.

URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. 다음 명령을 사용하여 이러한 Pod를 삭제할 수 있습니다. AKS가 삭제된 포드를 자동으로 다시 만듭니다.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>'필수 도구 및 구성이 누락되었습니다.' 오류

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

### <a name="try"></a>다음을 시도해 보세요.

PATH 환경 변수가 제대로 설정되어 있는 명령 프롬프트에서 VS Code를 시작합니다.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

### <a name="try"></a>시도해 보기

최신 버전의 Azure Dev Spaces CLI를 다운로드한 후 설치합니다.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>오류 'azds'는 내부 또는 외부 명령, 실행 가능한 프로그램 또는 일괄 처리 파일로 인식되지 않습니다.
 
azds.exe가 설치되지 않았거나 올바르게 구성되지 않은 경우 이 오류가 발생할 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.

1. azds.exe에 대한 위치인 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI(미리 보기)를 확인합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. azds.exe가 설치되어 있지 않으면 다음 명령을 실행합니다.

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 ‘지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다’
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 중 하나로 작성된 코드를 포함하는 디렉터리에서 *azds prep*를 실행할 때 Azure Dev Spaces는 자동으로 적절한 Dockerfile을 만듭니다.

다른 언어로 작성된 코드를 통해 Azure Dev Spaces를 사용할 수도 있지만, *azds up*을 처음 실행하기 전에 Dockerfile을 수동으로 만들어야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure Dev Spaces에서 기본적으로 지원하지 않는 언어로 애플리케이션이 작성된 경우 코드를 실행하는 컨테이너 이미지를 빌드하기 위해 적절한 Dockerfile을 제공해야 합니다. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절한 Dockerfile을 만들고 나면 *azds up*을 계속 실행하여 Azure Dev Spaces에서 애플리케이션을 실행할 수 있습니다.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>오류 '업스트림 연결 오류 또는 헤더 전에 연결 끊김/다시 설정'
서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 

### <a name="reason"></a>이유 
컨테이너 포트를 사용할 수 없습니다. 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다. 
* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 다음과 같은 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)


## <a name="config-file-not-found"></a>구성 파일을 찾을 수 없음
`azds up`을 실행하고 다음 오류가 표시됩니다. `Config file not found: .../azds.yaml`

### <a name="reason"></a>이유
실행하려는 코드의 루트 디렉터리에서 `azds up`을 실행하고, Azure Dev Spaces로 실행하도록 코드 폴더를 초기화해야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. 코드 폴더에 _azds.yaml_ 파일이 없는 경우 `azds prep`를 실행하여 Docker, Kubernetes 및 Azure Dev Spaces 자산을 생성합니다.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>오류: '파이프 프로그램 'azds'가 코드 126으로 예기치 않게 종료되었습니다.'
VS Code 디버거를 시작하면 때때로 이 오류가 발생할 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. VS Code를 닫았다가 다시 엽니다.
2. F5 키를 다시 누릅니다.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>디버깅 오류 '다음 유형에 대한 디버거 확장을 찾지 못함: coreclr'
VS Code 디버거를 실행하면 오류를 보고합니다. `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>이유
개발 컴퓨터에 C#용 VS Code 확장이 설치되어 있지 않습니다. C# 확장 지원.NET Core (CoreCLR)에 대 한 디버깅을 포함 합니다.

### <a name="try"></a>다음을 시도해 보세요.
[C#용 VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)을 설치합니다.

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>디버깅 오류 '구성된 디버그 형식 'coreclr'은 지원되지 않습니다.'
VS Code 디버거를 실행하면 오류를 보고합니다. `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>이유
개발 컴퓨터에 설치된 Azure Dev Spaces용 VS Code 확장이 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
[Azure Dev Spaces용 VS Code 확장](get-started-netcore.md)을 설치합니다.

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>디버깅 오류 "'cwd' 값 '/src'가 잘못되었습니다. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.
VS Code 디버거를 실행하면 오류 `Invalid 'cwd' value '/src'. The system cannot find the file specified.` 및/또는 `launch: program '/src/[path to project executable]' does not exist`를 보고합니다.

### <a name="reason"></a>이유
기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
프로젝트 폴더의 `.vscode` 하위 디렉터리에서 `launch.json` 파일을 업데이트합니다. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>형식 또는 네임스페이스 이름 'MyLibrary'를 찾을 수 없음

### <a name="reason"></a>이유 
빌드 컨텍스트는 기본적으로 프로젝트/서비스 수준에 있으므로 사용 중인 라이브러리 프로젝트를 찾을 수 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
수행해야 하는 작업:
1. 빌드 컨텍스트를 솔루션 수준으로 설정하도록 _azds.yaml_ 파일을 수정합니다.
2. 새 빌드 컨텍스트를 기준으로 올바르게 프로젝트 파일(_.csproj_)을 참조하도록 _Dockerfile_ 및 _Dockerfile.develop_ 파일을 수정합니다.
3. .sln 파일 옆에 _.dockerignore_ 파일을 배치하고 필요에 따라 수정합니다.

https://github.com/sgreenmsft/buildcontextsample에서 예제를 찾을 수 있습니다.

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 권한 부여 오류
Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다. Dev Spaces를 관리하려고 하는데 관련 Azure 구독에 대한 ‘owner’ 또는 ‘contributor’ 액세스 권한이 없는 경우 이 오류가 표시될 수 있습니다.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>이유
선택한 Azure 구독에서 `Microsoft.DevSpaces` 네임스페이스를 등록하지 않았습니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있는 사용자는 다음 Azure CLI 명령을 실행하여 `Microsoft.DevSpaces` 네임스페이스를 수동으로 등록할 수 있습니다.

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 가상 노드에 대한 ‘컨테이너 이미지 빌드 대기 중...’ 단계에서 Dev Spaces가 시간 초과됩니다.

### <a name="reason"></a>이유
실행 하도록 구성 된 서비스를 실행 하려면 개발자 공간을 사용 하려고 할 때이 시간 초과 발생을 [AKS 가상 노드](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)합니다. Dev Spaces는 현재 가상 노드에서 빌드 또는 디버깅 서비스를 지원하지 않습니다.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

위의 명령은 서비스의 pod를 할당할 수 있음을 보여 줍니다 *가상-노드-aci-linux*, 가상 노드는 합니다.

### <a name="try"></a>다음을 시도해 보세요.
서비스에 대한 Helm 차트를 업데이트하여 가상 노드에서 서비스를 실행할 수 있도록 하는 *nodeSelector* 및/또는 *tolerations* 값을 제거합니다. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

Dev Spaces를 통해 빌드/디버그하려는 서비스가 VM 노드에서 실행되는 경우에는 가상 노드 기능이 사용되는 AKS 클러스터를 계속 사용할 수 있습니다. 이 값이 기본 구성입니다.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 시작 시 “오류: could not find a ready tiller pod(준비된 Tiller Pod를 찾을 수 없음)”

### <a name="reason"></a>이유
이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

### <a name="try"></a>다음을 시도해 보세요.
클러스터에서 에이전트 노드를 다시 시작하면 대체로 이 문제가 해결됩니다.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces 프록시는 개발 공간에서 실행되는 다른 Pod를 방해할 수 있습니다.

### <a name="reason"></a>이유
AKS 클러스터의 네임스페이스에서 Dev Spaces를 활성화하면 _mindaro-proxy_라는 추가 컨테이너가 해당 네임스페이스 내에서 실행되는 각 Pod에 설치됩니다. 이 컨테이너는 Pod의 서비스에 대한 호출을 가로채며, Dev Spaces의 팀 개발 기능에 필수적입니다. 그러나 이러한 Pod에서 실행 중인 특정 서비스를 방해할 수 있습니다. Redis Azure Cache 실행 되 고, 주/보조 통신에서 연결 오류 및 실패를 일으키는 pod를 방해 하는 것이 이라고 합니다.

### <a name="try"></a>다음을 시도해 보세요.
영향을 받는 Pod를 Dev Spaces가 ‘사용되지 않는’ 클러스터 내의 네임스페이스로 이동할 수 있습니다. 애플리케이션의 나머지 부분은 Dev Spaces 사용 네임스페이스 내에서 계속 실행할 수 있습니다. Dev Spaces는 비 Dev Spaces 사용 네임스페이스 내에 _mindaro-proxy_ 컨테이너를 설치하지 않습니다.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces에서 컨테이너를 빌드하는 데 내 기존 Dockerfile을 사용하지 않는 것처럼 보임

### <a name="reason"></a>이유
프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

### <a name="try"></a>다음을 시도해 보세요.
Azure Dev Spaces가 사용자 프로젝트에 생성한 _azds.yaml_ 파일을 엽니다. *configurations->develop->build->dockerfile* 지시문을 사용하여 사용할 Dockerfile을 가리킵니다.

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>오류 "내부 보기 실패: ENOSPC 보기" Node.js 응용 프로그램에 디버깅을 연결 하는 경우

### <a name="reason"></a>이유

디버거를 사용 하 여 연결 하려고 하는 Node.js 응용 프로그램을 사용 하 여 pod를 실행 하는 노드를 초과 합니다 *fs.inotify.max_user_watches* 값입니다. 경우에 따라 [기본값인 *fs.inotify.max_user_watches* 디버거를 pod에 직접 연결을 처리 하기 너무 작거나](https://github.com/Azure/AKS/issues/772)합니다.

### <a name="try"></a>시도해 보기
임시 해결 방법이이 문제에 대 한 값을 늘려야 하는 것 *fs.inotify.max_user_watches* 클러스터의 각 노드에 변경 내용을 적용 하려면 해당 노드를 다시 시작 합니다.

## <a name="new-pods-are-not-starting"></a>새 pod를 시작 하지 않기

### <a name="reason"></a>이유

Kubernetes 이니셜라이저 RBAC 권한이 변경 내용으로 인해 새 pod에 대 한 PodSpec를 적용할 수 없습니다는 *클러스터 관리자* 클러스터의 역할입니다. 새 pod에는 잘못 된 PodSpec 있을, 예를 들어 pod와 사용 하 여 연결 된 서비스 계정은 더 이상 없습니다. 에 있는 pod를 참조 하는 *보류 중* 문제로 이니셜라이저를 사용 하 여 상태를 `kubectl get pods` 명령:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

이 문제는 영향을 줄의 pod 수 *모든 네임 스페이스* Azure 개발 공간 해제 되어 없는 네임 스페이스를 포함 하 여 클러스터에 있습니다.

### <a name="try"></a>시도해 보기

[개발 공간 CLI 최신 버전으로 업데이트](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) 한 후 삭제 하는 *azds InitializerConfiguration* Azure 개발 공간 컨트롤러에서:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

제거한 후는 *azds InitializerConfiguration* Azure 개발 공간 컨트롤러에서 사용 하 여 `kubectl delete` 의 모든 pod를 제거 하는 *보류 중인* 상태입니다. 결국 pod 보류 중인 제거한, pod를 다시 배포 합니다.

새 pod에 여전히 닫힌 경우를 *보류 중인* 재배포를 사용 하 여 이후 상태 `kubectl delete` 의 모든 pod를 제거 하는 *보류 중인* 상태입니다. 결국 pod 보류 중인 제거, 클러스터에서 컨트롤러를 삭제 하 고 다시 설치:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

컨트롤러를 다시 설치, pod를 다시 배포 합니다.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>개발 공간 컨트롤러 및 Api 호출에 대 한 잘못 된 RBAC 권한

### <a name="reason"></a>이유
Azure 개발 공간 컨트롤러를 액세스 하는 사용자 관리자를 읽을 권한이 있어야 합니다 *kubeconfig* AKS 클러스터에 있습니다. 이 권한에에서 사용할 수 있는 예를 들어 합니다 [기본 제공 Azure Kubernetes 서비스 클러스터 관리자 역할](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)입니다. Azure 개발 공간 컨트롤러를 액세스 하는 사용자도 있어야 합니다 *참여자* 또는 *소유자* 컨트롤러에 대 한 RBAC 역할입니다.

### <a name="try"></a>시도해 보기
AKS 클러스터에 대 한 사용자의 권한을 업데이트에 대 한 자세한 내용은 [여기](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user)합니다.

컨트롤러에 대 한 사용자의 RBAC 역할을 업데이트.

1.  [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
1. AKS 클러스터와 동일한는 일반적으로 컨트롤러를 포함 하는 리소스 그룹으로 이동 합니다.
1. 사용 하도록 설정 합니다 *숨겨진된 형식 표시* 확인란을 선택 합니다.
1. 컨트롤러에서를 클릭 합니다.
1. 엽니다는 *액세스 제어 (IAM)* 창입니다.
1. 클릭 합니다 *역할 할당* 탭 합니다.
1. 클릭 *추가* 한 다음 *역할 할당 추가*합니다.
    * 에 대 한 *역할* 중 하나를 선택 *참가자* 하거나 *소유자*합니다.
    * 에 대 한 *에 대 한 액세스 할당* 선택 *Azure AD 사용자, 그룹 또는 서비스 주체*합니다.
    * 에 대 한 *선택* 권한을 부여 하려는 사용자를 검색 합니다.
1. *저장*을 클릭합니다.
