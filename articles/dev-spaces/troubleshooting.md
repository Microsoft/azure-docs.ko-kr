---
title: 문제 해결
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Azure Dev Spaces를 사용 하도록 설정 하 고 사용 하는 경우 일반적인 문제를 해결 하는 방법을 알아봅니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 3a2eb98af2c73b5a920f3e3bcedb7ab18e9f0430
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548852"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 문제 해결

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

Azure Dev Spaces를 사용 하는 동안 문제가 발생 하는 경우 [Azure Dev Spaces GitHub 리포지토리에서 문제](https://github.com/Azure/dev-spaces/issues)를 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 TEMP 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 TEMP 디렉터리는 일반적으로 `/tmp`입니다.

또한 Azure Dev Spaces는 단일 인스턴스 또는 pod를 디버그할 때 가장 잘 작동 합니다. `azds.yaml` 파일에는 Kubernetes에서 서비스에 대해 실행 하는 pod의 수를 나타내는 *replicaCount*설정이 포함 되어 있습니다. 지정 된 서비스에 대해 여러 pod를 실행 하도록 응용 프로그램을 구성 하도록 *replicaCount* 를 변경 하는 경우 디버거는 사전순으로 나열 될 때 첫 번째 pod에 연결 됩니다. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Azure Dev Spaces를 사용 하는 경우 일반적인 문제

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"Azure Dev Spaces 컨트롤러를 만들지 못했습니다." 오류

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

컨트롤러를 삭제 해 볼 수도 있습니다.

```bash
azds remove -g <resource group name> -n <cluster name>
```

Azure Dev Spaces CLI를 사용 하 여 컨트롤러를 삭제 합니다. Visual Studio에서 컨트롤러를 삭제할 수 없습니다. 또한 Azure Cloud Shell에 Azure Dev Spaces CLI를 설치할 수 없으므로 Azure Cloud Shell에서 컨트롤러를 삭제할 수 없습니다.

Azure Dev Spaces CLI를 설치 하지 않은 경우 먼저 다음 명령을 사용 하 여 설치할 수 있습니다. 그런 다음 컨트롤러를 삭제 합니다.

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. 예는 [팀 개발](quickstart-team-development.md) 또는 [.net Core 빠른 시작을 사용 하 여 개발](quickstart-netcore-visualstudio.md) 을 참조 하세요.

### <a name="controller-create-failing-because-of-controller-name-length"></a>컨트롤러 이름 길이 때문에 컨트롤러를 만들지 못했습니다.

Azure Dev Spaces 컨트롤러 이름은 31 자 보다 길 수 없습니다. AKS 클러스터에서 개발 공간을 사용 하도록 설정 하거나 컨트롤러를 만들 때 컨트롤러의 이름이 31 자를 초과 하는 경우 오류가 표시 됩니다. 예:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

이 문제를 해결 하려면 대체 이름으로 컨트롤러를 만듭니다. 예:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>AKS 클러스터에 Windows 노드 풀이 추가 될 때 Dev 공간 실패

현재 Azure Dev Spaces는 Linux pod 및 노드에서만 실행 하기 위한 것입니다. Windows 노드 풀을 포함 하는 AKS 클러스터가 있는 경우 Azure Dev Spaces pod이 Linux 노드에서만 예약 되어 있는지 확인 해야 합니다. Azure Dev Spaces pod가 Windows 노드에서 실행 되도록 예약 된 경우 해당 pod가 시작 되지 않고 개발 공간을 사용할 수 없습니다.

이 문제를 해결 하려면 AKS 클러스터에 [taint를 추가](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) 하 여 Linux pod Windows 노드에서 실행 되도록 예약 되지 않도록 합니다.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>오류 "클러스터의 준비 상태에서 untainted Linux 노드를 찾을 수 없습니다. ' Azds ' 네임 스페이스에 pod을 배포 하려면 준비 상태에 untainted Linux 노드가 하나 이상 있어야 합니다. "

AKS 클러스터에서 untainted 노드를 찾을 수 없습니다. *준비* 상태에서 pod를 예약 하려면이 노드를 찾을 수 없습니다. Azure Dev Spaces Azure Dev Spaces에는 tolerations를 지정 하지 않고 pod 예약을 허용 하는 하나 이상의 Linux 노드가 *준비* 상태 여야 합니다.

이 문제를 해결 하려면 AKS 클러스터에서 [taint 구성을 업데이트](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) 하 여 하나 이상의 Linux 노드에서 tolerations를 지정 하지 않고 pod 일정을 예약할 수 있도록 합니다. 또한 tolerations를 지정 하지 않고 pod 예약을 허용 하는 하나 이상의 Linux 노드가 *준비* 상태 인지 확인 합니다. 노드가 *준비* 상태에 도달 하는 데 오랜 시간이 걸리는 경우 노드를 다시 시작 해 볼 수 있습니다.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>`az aks use-dev-spaces` 실행 시 "Azure Dev Spaces CLI가 제대로 설치 되지 않았습니다." 오류가 발생 함

Azure Dev Spaces CLI 업데이트가 설치 경로를 변경 했습니다. 2\.0.63 이전 버전의 Azure CLI 사용 하는 경우이 오류가 표시 될 수 있습니다. Azure CLI 버전을 표시 하려면 `az --version`를 사용 합니다.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

2\.0.63 이전 버전의 Azure CLI를 사용 하 여 `az aks use-dev-spaces`를 실행 하는 경우 오류 메시지에도 불구 하 고 설치는 성공 합니다. 아무런 문제 없이 `azds`를 계속 사용할 수 있습니다.

이 문제를 해결 하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 설치를 2.0.63 이상으로 업데이트 합니다. 이 업데이트는 `az aks use-dev-spaces`을 실행할 때 나타나는 오류 메시지를 해결 합니다. 또는 현재 버전의 Azure CLI 및 Azure Dev Spaces CLI를 계속 사용할 수 있습니다.

### <a name="error-unable-to-reach-kube-apiserver"></a>"Kube-apiserver에 연결할 수 없습니다." 오류

Azure Dev Spaces에서 AKS 클러스터의 API 서버에 연결할 수 없는 경우이 오류가 표시 될 수 있습니다. 

AKS cluster API 서버에 대한 액세스가 잠겨 있거나 AKS 클러스터에 대해 [API server 권한 있는 IP 주소 범위](../aks/api-server-authorized-ip-ranges.md) 를 사용 하도록 설정한 경우에는 [해당 지역에 따라 추가 범위를 허용](https://github.com/Azure/dev-spaces/tree/master/public-ips)하도록 클러스터도 [만들거나](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) [업데이트](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) 해야 합니다.

Kubectl 명령을 실행 하 여 API 서버를 사용할 수 있는지 확인 합니다. API 서버를 사용할 수 없는 경우 AKS 지원에 문의 하 고 API 서버가 작동 하 고 있는 경우 다시 시도 하세요.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Azure Dev Spaces에 대한 프로젝트를 준비할 때 발생 하는 일반적인 문제

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 "Dockerfile은 지원 되지 않는 언어 때문에 생성할 수 없습니다."
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 중 하나로 작성 된 코드를 사용 하 여 디렉터리에서 `azds prep`를 실행 하면 Azure Dev Spaces 자동으로 적절 한 Dockerfile을 만듭니다.

다른 언어로 작성 된 코드를 사용 하 여 Azure Dev Spaces을 계속 사용할 수 있지만, `azds up`를 처음 실행 하기 전에 Dockerfile을 수동으로 만들어야 합니다.

응용 프로그램이 기본적으로 지원 되지 Azure Dev Spaces 않는 언어로 작성 된 경우 코드를 실행 하는 컨테이너 이미지를 빌드하기 위한 적절 한 Dockerfile을 제공 해야 합니다. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절 한 Dockerfile이 준비 되 면 `azds up`를 실행 하 여 Azure Dev Spaces에서 응용 프로그램을 실행 합니다.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Azure Dev Spaces를 사용 하 여 서비스를 시작 하거나 중지할 때 발생 하는 일반적인 문제

### <a name="error-config-file-not-found"></a>오류 "구성 파일을 찾을 수 없음:"

`azds up`를 실행 하는 경우이 오류가 표시 될 수 있습니다. `azds up`와 `azds prep`는 모두 개발 공간에서 실행 하려는 프로젝트의 루트 디렉터리에서 실행 해야 합니다.

이 문제를 해결하려면
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. _Azds_ 파일이 코드 폴더에 없는 경우 `azds prep`를 실행 하 여 Docker, Kubernetes 및 Azure Dev Spaces 자산을 생성 합니다.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>"컨테이너 이미지 빌드를 기다리는 중 ..."에서 시간이 초과 되었습니다. AKS 가상 노드를 사용 하는 단계

이 시간 제한은 [AKS 가상 노드에서](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)실행 하도록 구성 된 서비스를 실행 하기 위해 Dev Spaces를 사용 하려고 할 때 발생 합니다. 개발 공간은 현재 가상 노드에서 서비스 빌드 또는 디버깅을 지원 하지 않습니다.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

위의 명령은 서비스의 pod가 가상 노드인 *가상 노드 aci-linux*에 할당 되었음을 보여 줍니다.

이 문제를 해결 하려면 서비스에 대한 투구 차트를 업데이트 하 여 가상 노드에서 서비스를 실행할 수 있도록 하는 *Nodeselector* 또는 *tolerations* 값을 제거 합니다. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

개발 공간을 통해 빌드 또는 디버그 하려는 서비스가 VM 노드에서 실행 되는 경우 가상 노드 기능이 활성화 된 AKS 클러스터를 계속 사용할 수 있습니다. VM 노드에서 Dev Spaces를 사용 하 여 서비스를 실행 하는 것은 기본 구성입니다.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces를 시작할 때 "ready tiller pod를 찾을 수 없습니다" 오류

이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

이 문제를 해결 하려면 클러스터의 에이전트 노드를 다시 시작 합니다.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>오류 "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: 서비스 '\<servicename\>'이 (가) 이미 존재 합니다." 또는 "\<servicename\>에 대한 끌어오기 액세스가 거부 되었습니다. 리포지토리가 없거나 ' docker 로그인 ' '이 필요할 수 있습니다.

이러한 오류는 동일한 개발 공간 내에서 개발 공간 명령 (예: `azds up` 및 `azds down`)과 함께 직접 투구 명령 (예: `helm install`, `helm upgrade`또는 `helm delete`)을 함께 사용 하는 경우 발생할 수 있습니다. 개발자 공간에는 동일한 개발 공간에서 실행 되는 고유한 Tiller 인스턴스와 충돌 하는 자체 Tiller 인스턴스가 있기 때문에 발생 합니다.

동일한 AKS 클러스터에 대해 투구 명령과 Dev Spaces 명령을 모두 사용 하는 것이 좋지만 각 개발 공간 사용 네임 스페이스는 하나 또는 다른를 사용 해야 합니다.

예를 들어, 투구 명령을 사용 하 여 부모 개발 공간에서 전체 응용 프로그램을 실행 한다고 가정 합니다. 해당 부모에서 자식 dev 공간을 만들고 Dev 공간을 사용 하 여 자식 dev 공간 내에서 개별 서비스를 실행 하 고 서비스를 함께 테스트할 수 있습니다. 변경 내용을 체크 인할 준비가 되 면 투구 명령을 사용 하 여 업데이트 된 코드를 부모 개발 공간에 배포 합니다. 부모 dev 공간에서 업데이트 된 서비스를 실행 하는 데 `azds up`를 사용 하지 마세요 .이는 처음에 투구를 사용 하 여 실행 하는 서비스와 충돌 하기 때문입니다.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>기존 Dockerfile은 컨테이너를 빌드하는 데 사용 되지 않습니다.

프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

이 문제를 해결 하려면 프로젝트에 생성 Azure Dev Spaces _azds_ 파일을 엽니다. 업데이트 *구성: 개발: 빌드: dockerfile* 을 사용 하려는 dockerfile을 가리킵니다. 예:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>개인 레지스트리에서 Docker 이미지를 사용 하려고 할 때 "권한이 없음: 인증 필요" 오류

인증을 요구 하는 개인 레지스트리에서 Docker 이미지를 사용 하 고 있습니다.

이 문제를 해결 하기 위해 이미지를 사용 [하 여이](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)개인 레지스트리에서 이미지를 인증 하 고 끌어올 수 있습니다. ImagePullSecrets을 사용 하려면 이미지를 사용 하는 네임 스페이스에 [Kubernetes secret을 만듭니다](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) . 그런 다음 `azds.yaml`의 imagePullSecret로 암호를 제공 합니다.

다음은 `azds.yaml`에서 imagePullSecrets 지정 하는 예입니다.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> `azds.yaml`에서 imagePullSecrets 설정 하면 `values.yaml`에 지정 된 imagePullSecrets 재정의 됩니다.

### <a name="error-service-cannot-be-started"></a>"서비스를 시작할 수 없습니다." 오류가 발생 했습니다.

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 더 많은 진단 정보를 얻으려면 서비스를 시작할 때 자세한 로깅을 사용 하도록 설정 합니다.

명령줄에서 `--verbose` 사용 하 여 자세한 로깅을 사용 하도록 설정 합니다. `--output`를 사용 하 여 출력 형식을 지정할 수도 있습니다. 예:

```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행

이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

이 오류는 Dev 공간 컨트롤러를 제거 해도 이전에 해당 컨트롤러에서 설치한 서비스가 제거 되지 않기 때문에 발생 합니다. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>"서비스를 시작할 수 없습니다." 오류가 발생 했습니다. 다중 단계 Dockerfiles를 사용 하는 경우

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

이 오류는 Azure Dev Spaces에서 현재 다단계 빌드를 지원 하지 않기 때문에 발생 합니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>개발 컴퓨터에 연결할 때 네트워크 트래픽이 AKS 클러스터로 전달 되지 않습니다.

Azure Dev Spaces를 사용 하 여 [AKS 클러스터를 개발 컴퓨터에 연결 하](how-to/connect.md)는 경우 개발 컴퓨터와 AKS 클러스터 간에 네트워크 트래픽이 전달 되지 않는 문제가 발생할 수 있습니다.

개발 컴퓨터를 AKS 클러스터에 연결 하는 경우 개발 컴퓨터의 `hosts` 파일을 수정 하 여 AKS 클러스터와 개발 컴퓨터 간에 네트워크 트래픽을 전달 Azure Dev Spaces. Azure Dev Spaces 호스트 이름으로 대체 하는 Kubernetes 서비스의 주소를 사용 하 여 `hosts`에 항목을 만듭니다. 이 항목은 개발 컴퓨터와 AKS 클러스터 간에 네트워크 트래픽을 전송 하기 위해 포트 전달과 함께 사용 됩니다. 개발 컴퓨터의 서비스가 교체할 Kubernetes 서비스의 포트와 충돌 하는 경우에는 Azure Dev Spaces Kubernetes 서비스에 대한 네트워크 트래픽을 전달할 수 없습니다. 예를 들어 *Windows BranchCache* 서비스는 일반적으로 *0.0.0.0:80*에 바인딩되고,이로 인해 모든 로컬 ip의 포트 80에 대한 충돌이 발생 합니다.

이 문제를 해결 하려면 바꾸려는 Kubernetes 서비스의 포트와 충돌 하는 서비스나 프로세스를 중지 해야 합니다. *Netstat*와 같은 도구를 사용 하 여 개발 컴퓨터에서 충돌 하는 서비스 또는 프로세스를 검사할 수 있습니다.

예를 들어 *Windows BranchCache* 서비스를 중지 하 고 사용 하지 않도록 설정 하려면 다음을 수행 합니다.
* 명령 프롬프트에서 `services.msc`를 실행 합니다.
* *BranchCache* 를 마우스 오른쪽 단추로 클릭 하 고 *속성*을 선택 합니다.
* *중지*를 클릭합니다.
* 필요에 따라 *시작 유형* 을 *사용*안 함으로 설정 하 여 비활성화할 수 있습니다.
* *확인*을 클릭합니다.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Azure Dev Spaces에서 Visual Studio 및 Visual Studio Code를 사용 하는 일반적인 문제

### <a name="error-required-tools-and-configurations-are-missing"></a>"필요한 도구 및 구성이 없습니다." 오류

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

PATH 환경 변수가 올바르게 설정 된 명령 프롬프트에서 VS Code을 시작 해 보세요.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

최신 버전의 Azure Dev Spaces CLI를 다운로드 하 여 설치 해 보세요.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>오류: "형식에 대한 디버거 확장을 찾지 못했습니다: coreclr"

Visual Studio Code 디버거를 실행할 때이 오류가 표시 될 수 있습니다. 개발 컴퓨터에 VS Code 확장 프로그램이 C# 설치 되어 있지 않을 수 있습니다. 확장 C# 에는 CoreCLR (.net Core)에 대한 디버깅 지원이 포함 됩니다.

이 문제를 해결 하려면 [ C#용 VS Code 확장 ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)을 설치 합니다.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>"구성 된 디버그 형식 ' coreclr '은 지원 되지 않습니다." 오류

Visual Studio Code 디버거를 실행할 때이 오류가 표시 될 수 있습니다. 개발 컴퓨터에 설치 된 Azure Dev Spaces에 대한 VS Code 확장이 없을 수 있습니다.

이 문제를 해결 하려면 [Azure Dev Spaces에 대한 VS Code 확장](get-started-netcore.md)을 설치 합니다.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>"Cwd ' 값 '/src ' 값이 잘못 되었습니다. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.

Visual Studio Code 디버거를 실행할 때이 오류가 표시 될 수 있습니다. 기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

이 문제를 해결 하려면 프로젝트 폴더의 `.vscode` 하위 디렉터리에 있는 `launch.json` 파일을 업데이트 합니다. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>"파이프 프로그램 ' azds '은 코드 126로 인해 예기치 않게 종료 되었습니다." 오류가 발생 합니다.

Visual Studio Code 디버거를 실행할 때이 오류가 표시 될 수 있습니다.

이 문제를 해결 하려면 Visual Studio Code를 닫은 후 다시 여십시오. 디버거를 다시 시작 합니다.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js 응용 프로그램에 디버깅을 연결 하는 경우 "내부 조사식 실패: watch ENOSPC" 오류

이 오류는 디버거를 사용 하 여 연결 하려고 하는 node.js 응용 프로그램과 함께 pod를 실행 하는 노드가 *max_user_watches inotify* 값을 초과 하는 경우에 발생 합니다. 경우에 따라 [ *max_user_watches inotify* 의 기본값이 너무 작아 pod에 직접 디버거를 연결 하](https://github.com/Azure/AKS/issues/772)는 것을 처리할 수 없는 경우도 있습니다.

이 문제에 대한 임시 해결 방법은 클러스터의 각 노드에서 *max_user_watches inotify* 의 값을 늘리고 해당 노드를 다시 시작 하 여 변경 내용을 적용 하는 것입니다.

## <a name="other-common-issues"></a>그 외 일반적인 문제

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>"Azds" 오류는 내부 또는 외부 명령, 실행할 수 있는 프로그램 또는 배치 파일로 인식 되지 않습니다.

이 오류는 `azds.exe` 설치 되지 않았거나 올바르게 구성 되지 않은 경우에 발생할 수 있습니다.

이 문제를 해결하려면

1. `azds.exe`에 대한% ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI 위치를 확인 합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. `azds.exe` 설치 되지 않은 경우 다음 명령을 실행 합니다.

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>"Microsoft DevSpaces/register/action" 권한 부여 오류

Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다. Dev 공간을 관리 하려는 경우 연결 된 Azure 구독에 대한 *소유자* 또는 *참가자* 액세스 권한이 없으면 권한 부여 오류가 표시 될 수 있습니다. 예:

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

이 문제를 해결 하려면 Azure 구독에 대한 *소유자* 또는 *참가자* 액세스 권한이 있는 계정을 사용 하 여 `Microsoft.DevSpaces` 네임 스페이스를 수동으로 등록 합니다.

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>새 pod 시작 되지 않음

Kubernetes 이니셜라이저는 클러스터의 *클러스터 관리자* 역할에 대한 RBAC 권한 변경으로 인해 new Pod에 PodSpec을 적용할 수 없습니다. 또한 새 pod에 잘못 된 PodSpec 있을 수 있습니다. 예를 들어 pod와 연결 된 서비스 계정이 더 이상 존재 하지 않습니다. 이니셜라이저 문제로 인해 *보류* 상태에 있는 pod을 보려면 `kubectl get pods` 명령을 사용 합니다.

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

이 문제는 Azure Dev Spaces 사용 하도록 설정 되지 않은 네임 스페이스를 포함 하 여 클러스터의 *모든 네임 스페이스* 에 pod 영향을 줄 수 있습니다.

이 문제를 해결 하려면 [Dev SPACES CLI를 최신 버전으로 업데이트](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) 한 다음 Azure Dev Spaces 컨트롤러에서 *Azds initializerconfiguration* 을 삭제 합니다.

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Azure Dev Spaces 컨트롤러에서 *Azds InitializerConfiguration* 을 제거한 후에는 `kubectl delete`를 사용 하 여 *보류 중* 상태의 모든 pod를 제거 합니다. 보류 중인 모든 pod을 제거한 후 pod를 다시 배포 합니다.

다시 배포 후 새 pod가 *보류 중* 상태로 유지 되는 경우 `kubectl delete`를 사용 하 여 *보류 중* 상태에서 pod를 제거 합니다. 보류 중인 모든 pod 제거 된 후 클러스터에서 컨트롤러를 삭제 하 고 다시 설치 합니다.

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

컨트롤러를 다시 설치한 후 pod를 다시 배포 합니다.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Dev Spaces 컨트롤러 및 Api 호출에 대한 RBAC 권한이 잘못 되었습니다.

Azure Dev Spaces 컨트롤러에 액세스 하는 사용자는 AKS 클러스터에서 admin *kubeconfig* 를 읽을 수 있는 권한이 있어야 합니다. 예를 들어이 권한은 [기본 제공 Azure Kubernetes Service 클러스터 관리자 역할](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)에서 사용할 수 있습니다. 또한 Azure Dev Spaces 컨트롤러에 액세스 하는 사용자에 게는 컨트롤러에 대한 *참가자* 또는 *소유자* RBAC 역할이 있어야 합니다. AKS 클러스터에 대한 사용자 권한 업데이트에 대한 자세한 내용은 [여기](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)에서 제공 됩니다.

컨트롤러에 대한 사용자의 RBAC 역할을 업데이트 하려면:

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. 일반적으로 AKS 클러스터와 동일한 컨트롤러를 포함 하는 리소스 그룹으로 이동 합니다.
1. *숨겨진 형식 표시* 확인란을 사용 하도록 설정 합니다.
1. 컨트롤러를 클릭 합니다.
1. *Access Control (IAM)* 창을 엽니다.
1. *역할 할당* 탭을 클릭 합니다.
1. *추가* 를 클릭 하 고 *역할 할당 추가*를 클릭 합니다.
    * *역할*에 대해 *참가자* 또는 *소유자*를 선택 합니다.
    * *액세스 할당*에서 *Azure AD 사용자, 그룹 또는 서비스 보안 주체*를 선택합니다.
    * *Select*에서 사용 권한을 부여 하려는 사용자를 검색 합니다.
1. *저장*을 클릭합니다.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--public` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다.

이 문제를 해결하려면

* Dev Spaces 서비스와 연결 된 모든 Url의 상태를 확인 합니다.

  ```console
  azds list-uris
  ```

* URL이 *보류 중* 상태인 경우에도 DEV 공간은 DNS 등록이 완료 될 때까지 대기 하 고 있습니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.
* URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. 다음 명령을 사용 하 여 이러한 pod를 삭제 하 고 AKS가 자동으로 다시 만들 수 있도록 허용 합니다.
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>"업스트림 연결 오류 또는 헤더 앞에 있는 연결 끊기/다시 설정" 오류 발생

서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 이 오류는 컨테이너 포트를 사용할 수 없음을 의미 합니다. 이는 다음과 같은 이유로 발생할 수 있습니다.

* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

이 문제를 해결하려면

1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 다음과 같은 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>"MyLibrary" 형식 또는 네임 스페이스 이름을 찾을 수 없습니다.

사용 중인 라이브러리 프로젝트를 찾을 수 없습니다. 개발 공간을 사용 하면 기본적으로 빌드 컨텍스트가 프로젝트/서비스 수준에 있습니다.  

이 문제를 해결하려면

1. `azds.yaml` 파일을 수정 하 여 빌드 컨텍스트를 솔루션 수준으로 설정 합니다.
2. `Dockerfile` 및 `Dockerfile.develop` 파일을 수정 하 여 새 빌드 컨텍스트와 관련 하 여 `.csproj`와 같은 프로젝트 파일을 참조 합니다.
3. `.sln` 파일과 동일한 디렉터리에 `.dockerignore`를 추가 합니다.
4. 필요에 따라 추가 항목으로 `.dockerignore`를 업데이트 합니다.

[여기](https://github.com/sgreenmsft/buildcontextsample)에서 예제를 찾을 수 있습니다.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>개발 공간에서 작업 하지 않는 수평 pod 자동 크기 조정

개발 공간에서 서비스를 실행 하는 경우 해당 서비스의 pod가 [계측을 위한 추가 컨테이너와 함께 삽입](how-dev-spaces-works.md#prepare-your-aks-cluster) 되며 pod의 모든 컨테이너에는 수평 pod 자동 크기 조정에 대해 설정 된 리소스 제한 및 요청이 있어야 합니다.

이 문제를 해결 하려면 리소스 요청을 적용 하 고 삽입 된 Dev Spaces 컨테이너에 제한을 적용 합니다. Pod 사양에 `azds.io/proxy-resources` 주석을 추가 하 여 삽입 된 컨테이너에 대한 리소스 요청 및 제한을 적용할 수 있습니다. 프록시에 대한 컨테이너 사양의 리소스 섹션을 나타내는 JSON 개체로 값을 설정 해야 합니다.

다음은 pod 사양에 적용 되는 프록시 리소스 주석의 예입니다.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Pod를 실행 하 여 기존 네임 스페이스에서 Azure Dev Spaces 사용

Azure Dev Spaces를 사용 하도록 설정 하려는 pod를 실행 하는 기존 AKS 클러스터 및 네임 스페이스가 있을 수 있습니다.

AKS 클러스터에서 기존 네임 스페이스에 대한 Azure Dev Spaces를 사용 하도록 설정 하려면 `use-dev-spaces`를 실행 하 고 `kubectl`를 사용 하 여 해당 네임 스페이스의 모든 pod를 다시 시작 합니다.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

Pod가 다시 시작 되 면 Azure Dev Spaces에서 기존 네임 스페이스 사용을 시작할 수 있습니다.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 제한 된 송신 트래픽으로 AKS 클러스터에서 Azure Dev Spaces 사용

클러스터 노드의 송신 트래픽이 제한 되는 AKS 클러스터에 대한 Azure Dev Spaces를 사용 하도록 설정 하려면 다음 Fqdn을 허용 해야 합니다.

| FQDN                                    | Port      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Linux 알파인 및 기타 Azure Dev Spaces 이미지를 꺼내려면 |
| gcr.io | HTTP: 443 | 투구/tiller 이미지를 꺼내려면|
| storage.googleapis.com | HTTP: 443 | 투구/tiller 이미지를 꺼내려면|
| azds-<guid>.<location>azds.io | HTTPS:443 | 컨트롤러에 대한 Azure Dev Spaces 백 엔드 서비스와 통신 하는 데 사용 됩니다. 정확한 FQDN 은% USERPROFILE%\.azds\settings.json의 "dataplaneFqdn"에서 찾을 수 있습니다.|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>"구독 \<subscriptionId\>에서 클러스터\> 클러스터 \<를 찾을 수 없습니다." 오류가 발생 했습니다.

Kubeconfig 파일이 Azure Dev Spaces 클라이언트 쪽 도구에서 사용 하려는 것과 다른 클러스터 또는 구독을 대상으로 하는 경우이 오류가 표시 될 수 있습니다. Azure Dev Spaces 클라이언트 쪽 도구는 [하나 이상의 kubeconfig 파일](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) 을 사용 하 여 클러스터를 선택 하 고 통신 하는 *kubectl*의 동작을 복제 합니다.

이 문제를 해결하려면

* `az aks use-dev-spaces -g <resource group name> -n <cluster name>`를 사용 하 여 현재 컨텍스트를 업데이트 합니다. 이 명령을 사용 하도록 설정 하지 않은 경우에도 AKS 클러스터에 대한 Azure Dev Spaces를 사용할 수 있습니다. 또는 `kubectl config use-context <cluster name>`를 사용 하 여 현재 컨텍스트를 업데이트할 수 있습니다.
* `az account show`를 사용 하 여 대상으로 지정 하는 현재 Azure 구독을 표시 하 고 올바른지 확인 합니다. `az account set`를 사용 하 여 대상으로 지정 하는 구독을 변경할 수 있습니다.