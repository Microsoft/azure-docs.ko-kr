---
title: 문제 해결
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Azure Dev Spaces를 사용하도록 설정하고 사용할 때 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: e26f066294cb0a6a48c5a3299213206fe4226ad0
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210825"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces 문제 해결

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

Azure Dev Spaces를 사용하는 동안 문제가 발생하는 경우 [Azure Dev Spaces GitHub 리포지토리에서 이슈](https://github.com/Azure/dev-spaces/issues)를 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 *‘TEMP’* 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 *‘TEMP’* 디렉터리는 일반적으로 `/tmp`입니다. 또한 [Azure CLI 구성 파일](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)에서 로깅을 사용하도록 설정했는지 확인합니다.

또한 Azure Dev Spaces는 단일 인스턴스 또는 Pod를 디버그할 때 가장 원활하게 작동합니다. `azds.yaml` 파일에는 Kubernetes가 서비스용으로 실행하는 Pod 수를 나타내는 설정인 *replicaCount*가 포함되어 있습니다. 지정된 서비스용으로 여러 개의 Pod를 실행하도록 애플리케이션을 구성하기 위해 *replicaCount*를 변경하면 디버거는 첫 번째 Pod(사전순으로 나열된 경우)에 연결됩니다. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="common-issues-when-using-local-process-with-kubernetes"></a>Kubernetes에서 로컬 프로세스를 사용할 때 발생 하는 일반적인 문제

### <a name="fail-to-restore-original-configuration-of-deployment-on-cluster"></a>클러스터의 원래 배포 구성을 복원 하지 못함

Kubernetes를 사용 하 여 로컬 프로세스를 사용 하는 경우 Kubernetes client를 사용 하는 로컬 프로세스가 갑자기 충돌 하거나 종료 되 면 Kubernetes를 사용 하 여 로컬 프로세스가 리디렉션하는 서비스는 로컬 프로세스와 Kubernetes가 연결 되기 전에 원래 상태로 복원 되지 않을 수 있습니다.

이 문제를 해결 하려면 클러스터에 서비스를 다시 배포 합니다.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Azure Dev Spaces를 사용하는 경우 일반적인 이슈

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>"Azure Dev Spaces 컨트롤러를 만들지 못했음" 오류

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

다음과 같이 컨트롤러를 삭제해 볼 수도 있습니다.

```bash
azds remove -g <resource group name> -n <cluster name>
```

Azure Dev Spaces CLI를 사용하여 컨트롤러를 삭제합니다. Visual Studio에서는 컨트롤러를 삭제할 수 없습니다. 또한 Azure Cloud Shell에 Azure Dev Spaces CLI를 설치할 수 없으므로 Azure Cloud Shell에서 컨트롤러를 삭제할 수 없습니다.

Azure Dev Spaces CLI를 설치하지 않은 경우 먼저 다음 명령을 사용하여 설치한 후 삭제할 수 있습니다.

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>컨트롤러 이름 길이 때문에 컨트롤러를 만들지 못함

Azure Dev Spaces 컨트롤러 이름은 31자보다 길 수 없습니다. AKS 클러스터에서 Dev Spaces를 사용하도록 설정하거나 컨트롤러를 만들 때 컨트롤러의 이름이 31자를 초과하는 경우 오류가 표시됩니다. 다음은 그 예입니다.

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

이 이슈를 해결하려면 다른 이름을 지정해서 컨트롤러를 만듭니다. 다음은 그 예입니다.

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>AKS 클러스터에 Windows 노드 풀이 추가될 때 Dev Spaces를 사용하도록 설정하지 못함

현재 Azure Dev Spaces는 Linux Pod 및 노드에서만 실행하도록 디자인되었습니다. Windows 노드 풀을 포함하는 AKS 클러스터가 있는 경우 Azure Dev Spaces Pod가 Linux 노드에서만 예약되어 있는지 확인해야 합니다. Azure Dev Spaces Pod가 Windows 노드에서 실행되도록 예약된 경우 해당 Pod가 시작되지 않고 Dev Spaces를 사용하도록 설정할 수 없습니다.

이 이슈를 해결하려면 AKS 클러스터에 [taint를 추가](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)하여 Linux Pod가 Windows 노드에서 실행되도록 예약하지 않게 합니다.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>오류 "클러스터에서 준비 상태의 taint되지 않은 Linux 노드를 찾을 수 없습니다. 'azds' 네임스페이스에 pod을 배포하려면 준비 상태의 taint되지 않은 Linux 노드가 하나 이상 있어야 합니다."

AKS 클러스터에서 *‘준비’* 상태의 taint되지 않은 노드를 찾을 수 없으므로 AKS 클러스터에서 컨트롤러를 만들 수 없습니다. Azure Dev Spaces에는 toleration을 지정하지 않고 pod 예약을 허용하는 *‘준비’* 상태의 Linux 노드가 하나 이상 필요합니다.

이 이슈를 해결하려면 AKS 클러스터에서 [taint 구성을 업데이트](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)하여 toleration을 지정하지 않고 하나 이상의 Linux 노드에서 pod 일정을 예약할 수 있도록 합니다. 또한 toleration을 지정하지 않고 pod 예약을 허용하는 하나 이상의 Linux 노드가 *준비* 상태인지 확인합니다. 노드가 *준비* 상태에 도달하는 데 시간이 오래 걸리는 경우 노드를 다시 시작해 볼 수 있습니다.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>az aks use-dev-spaces를 실행하는 동안 "Azure Dev Spaces CLI가 제대로 설치되지 않음" 오류가 발생함

Azure Dev Spaces CLI 업데이트가 설치 경로를 변경했습니다. 2\.0.63 이전 버전의 Azure CLI를 사용하는 경우 이 오류가 표시될 수 있습니다. Azure CLI 버전을 표시하려면 `az --version`을 사용합니다.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

2\.0.63 이전 버전의 Azure CLI를 사용하여 `az aks use-dev-spaces`를 실행하는 경우 오류 메시지가 표시되더라도 설치는 성공적으로 수행됩니다. 아무런 문제 없이 `azds`를 계속 사용할 수 있습니다.

이 이슈를 해결하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)의 설치를 2.0.63 이상으로 업데이트합니다. 이 업데이트는 `az aks use-dev-spaces`를 실행할 때 나타나는 오류 메시지를 해결합니다. 또는 현재 버전의 Azure CLI 및 Azure Dev Spaces CLI를 계속 사용할 수 있습니다.

### <a name="error-unable-to-reach-kube-apiserver"></a>"kube-apiserver에 연결할 수 없습니다." 오류 발생

Azure Dev Spaces에서 AKS 클러스터의 API 서버에 연결할 수 없는 경우 이 오류가 표시될 수 있습니다.

AKS cluster API 서버에 대 한 액세스가 잠겨 있거나 AKS 클러스터에 대 한 [API 서버 권한이 부여 된 IP 주소 범위](../aks/api-server-authorized-ip-ranges.md) 를 사용 하도록 설정한 경우에는 [해당 지역을 기반으로 하는 추가 범위를 허용](configure-networking.md#aks-cluster-network-requirements) 하도록 클러스터도 [만들거나](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) [업데이트](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) 해야 합니다.

kubectl 명령을 실행하여 API 서버를 사용할 수 있는지 확인합니다. API 서버를 사용할 수 없는 경우 AKS 지원에 문의하고 API 서버가 작동하고 있는 경우 다시 시도하세요.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Azure Dev Spaces에 대해 프로젝트를 준비할 때 발생하는 일반적인 이슈

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 "지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다."
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 중 하나로 작성된 코드를 포함하는 디렉터리에서 `azds prep`를 실행할 때 Azure Dev Spaces는 자동으로 적절한 Dockerfile을 만듭니다.

다른 언어로 작성된 코드를 통해 Azure Dev Spaces를 사용할 수도 있지만, `azds up`을 처음 실행하기 전에 Dockerfile을 수동으로 만들어야 합니다.

Azure Dev Spaces에서 기본적으로 지원하지 않는 언어로 애플리케이션이 작성된 경우 코드를 실행하는 컨테이너 이미지를 빌드하기 위해 적절한 Dockerfile을 제공해야 합니다. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절한 Dockerfile을 만들고 나면 `azds up`을 실행하여 Azure Dev Spaces에서 애플리케이션을 실행합니다.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Azure Dev Spaces를 사용하여 서비스를 시작하거나 중지할 때 발생하는 일반적인 이슈

### <a name="error-config-file-not-found"></a>오류 "구성 파일을 찾을 수 없음:"

`azds up`을 실행하는 경우 이 오류가 표시될 수 있습니다. `azds up`과 `azds prep`은 모두 개발 공간에서 실행하려는 프로젝트의 루트 디렉터리에서 실행해야 합니다.

이 문제를 해결하려면
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. 코드 폴더에 _azds.yaml_ 파일이 없는 경우 `azds prep`를 실행하여 Docker, Kubernetes 및 Azure Dev Spaces 자산을 생성합니다.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 가상 노드의 "컨테이너 이미지 빌드 대기 중..." 단계에서 시간 초과 발생

이 시간 초과 오류는 Dev Spaces를 사용하여 [AKS 가상 노드](../aks/virtual-nodes-portal.md)에서 실행되도록 구성된 서비스를 실행하려고 할 때 발생합니다. Dev Spaces는 현재 가상 노드에서 빌드 또는 디버깅 서비스를 지원하지 않습니다.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

위 명령은 가상 노드인 *virtual-node-aci-linux*에 서비스의 Pod가 할당되었음을 보여 줍니다.

이 이슈를 해결하려면 서비스에 대한 Helm 차트를 업데이트하여 가상 노드에서 서비스를 실행할 수 있도록 하는 *nodeSelector* 또는 *tolerations* 값을 제거합니다. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

Dev Spaces를 통해 빌드 또는 디버그하려는 서비스가 VM 노드에서 실행되는 경우에는 가상 노드 기능이 사용되는 AKS 클러스터를 계속 사용할 수 있습니다. VM 노드에서 Dev Spaces를 사용하여 서비스를 실행하는 것은 기본 구성입니다.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 시작 시 "준비된 Tiller Pod를 찾을 수 없음" 오류 발생

이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

이 이슈를 해결하려면 클러스터의 에이전트 노드를 다시 시작합니다.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>오류 "release azds- \<identifier\> - \<spacename\> - \<servicename\> 실패: 서비스 ' \<servicename\> '이 (가) 이미 있습니다." 또는 "끌어오기 액세스가 거부 되었습니다. \<servicename\> 리포지토리가 없거나 ' docker 로그인 ' '이 필요할 수 있습니다.

이러한 오류는 동일한 개발 공간 내에서 Dev Spaces 명령(예: `azds up` 및 `azds down`)과 함께 직접 Helm 명령(예: `helm install`, `helm upgrade` 또는 `helm delete`)을 함께 실행하는 경우에 발생할 수 있습니다. Dev Spaces에는 동일한 개발 공간에서 실행되는 고유한 Tiller 인스턴스와 충돌하는 자체 Tiller 인스턴스가 있기 때문에 이러한 오류가 발생합니다.

동일한 AKS 클러스터에 대해 Helm 명령과 Dev Spaces 명령을 모두 사용하는 것이 좋지만 각 Dev Spaces 지원 네임스페이스는 이중 하나만 사용해야 합니다.

예를 들어, Helm 명령을 사용하여 부모 개발 공간에서 전체 애플리케이션을 실행한다고 가정합니다. 해당 부모 개발 공간에서 자식 개발 공간을 만들고, Dev Spaces를 사용하여 자식 개발 공간 내에서 개별 서비스를 실행하고 서비스를 함께 테스트할 수 있습니다. 변경 내용을 체크 인할 준비가 되면 Helm 명령을 사용하여 업데이트된 코드를 부모 개발 공간에 배포합니다. 부모 개발 공간에서 업데이트된 서비스를 실행하는 데 `azds up`을 사용하지 마세요. 이 명령을 사용하면 Helm을 사용하여 처음에 실행한 서비스와 충돌하기 때문입니다.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>기존 Dockerfile이 컨테이너 빌드에 사용되지 않음

프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

이 이슈를 해결하려면 Azure Dev Spaces가 사용자 프로젝트에 생성한 _azds.yaml_ 파일을 엽니다. 사용할 Dockerfile을 가리키도록 *‘configurations: develop: build: dockerfile’* 을 업데이트합니다. 다음은 그 예입니다.

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>개인 레지스트리에서 Docker 이미지를 사용하려고 할 때 "권한 없음: 인증 필요" 오류 발생

인증을 요구하는 개인 레지스트리에서 Docker 이미지를 사용하고 있습니다.

이 이슈를 해결하려면 [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)를 사용하여 Dev Spaces가 이 프라이빗 레지스트리에서 이미지를 인증하고 끌어오도록 허용할 수 있습니다. ImagePullSecrets를 사용하려면 이미지를 사용하려는 네임스페이스에서 [Kubernetes 비밀을 만듭니다](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod). 그런 다음, 이 비밀을 `azds.yaml`에서 imagePullSecret로 제공합니다.

다음은 `azds.yaml`에서 imagePullSecrets를 지정하는 예입니다.

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
> `azds.yaml`에서 imagePullSecrets를 설정하면 `values.yaml`에 지정된 imagePullSecrets가 재정의됩니다.

### <a name="error-service-cannot-be-started"></a>"서비스를 시작할 수 없습니다." 오류 발생

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 더 많은 진단 정보를 얻으려면 서비스를 시작할 때 자세한 로깅을 사용하도록 설정합니다.

명령줄에서 `--verbose`를 사용하여 자세한 로깅을 사용하도록 설정합니다. `--output`을 사용하여 출력 형식을 지정할 수도 있습니다. 다음은 그 예입니다.

```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행

이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

이 오류는 Dev Spaces 컨트롤러를 제거해도 해당 컨트롤러에서 이전에 설치한 서비스는 제거되지 않기 때문에 발생합니다. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>다단계 Dockerfiles를 사용할 때 "서비스를 시작할 수 없습니다." 오류 발생

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

이 오류는 현재 Azure Dev Spaces에서 다단계 빌드를 지원하지 않기 때문에 발생합니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>개발 머신에 연결할 때 네트워크 트래픽이 AKS 클러스터로 전달되지 않습니다.

[Azure Dev Spaces를 사용하여 AKS 클러스터를 개발 머신에 연결](https://code.visualstudio.com/docs/containers/local-process-kubernetes)하는 경우 개발 머신과 AKS 클러스터 간에 네트워크 트래픽이 전달되지 않는 이슈가 발생할 수 있습니다.

개발 머신을 AKS 클러스터에 연결하는 경우 Azure Dev Spaces는 개발 머신의 `hosts` 파일을 수정하여 AKS 클러스터와 개발 머신 간에 네트워크 트래픽을 전달합니다. Azure Dev Spaces는 호스트 이름으로 대체하려는 Kubernetes 서비스의 주소를 사용하여 `hosts`에 항목을 만듭니다. 이 항목은 개발 머신과 AKS 클러스터 간에 네트워크 트래픽을 보내기 위해 포트 전달과 함께 사용됩니다. 개발 머신의 서비스가 바꿀 Kubernetes 서비스의 포트와 충돌하는 경우 Azure Dev Spaces는 Kubernetes 서비스의 네트워크 트래픽을 전달할 수 없습니다. 예를 들어, *Windows BranchCache* 서비스는 일반적으로 *0.0.0.0:80*에 바인딩되므로 모든 로컬 IP의 포트 80에 충돌을 유발합니다.

이 이슈를 해결하려면 바꾸려는 Kubernetes 서비스의 포트와 충돌하는 서비스나 프로세스를 중지해야 합니다. *netstat*와 같은 도구를 사용하여 개발 머신에서 충돌하는 서비스 또는 프로세스를 검사할 수 있습니다.

예를 들어 *Windows BranchCache* 서비스를 중지하고 사용하지 않도록 설정하려면 다음을 수행합니다.
* 명령 프롬프트에서 `services.msc`를 실행합니다.
* *BranchCache*를 마우스 오른쪽 단추로 클릭하고 *‘속성’* 을 선택합니다.
* *‘중지’* 를 클릭합니다.
* 선택적으로 *‘시작 유형’* 을 *‘사용 안 함’* 으로 설정하여 사용하지 않도록 설정할 수 있습니다.
* *확인*을 클릭합니다.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>"Pod: azds/azds에 대 한 AzureAssignedIdentity를 찾을 수 없음: \<id\> " 할당 된 상태 "오류

[관리 id](../aks/use-managed-identity.md) 및 [pod 관리 id](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) 가 설치 된 AKS 클러스터에서 Azure Dev Spaces로 서비스를 실행 하는 경우 *차트 설치* 단계 후 프로세스가 응답 하지 않을 수 있습니다. *azds* 이름 공간에서 *azds-injector-webhook*를 검사하면 이 오류가 표시될 수 있습니다.

Azure Dev Spaces가 클러스터에서 실행하는 서비스는 클러스터의 관리 ID를 활용하여 클러스터 외부의 Azure Dev Spaces 백 엔드 서비스와 통신합니다. Pod 관리 ID가 설치되면 클러스터의 노드에서 관리 ID 자격 증명에 대한 모든 호출을 [클러스터에 설치된 NMI(Node Managed Identity) DaemonSet](https://github.com/Azure/aad-pod-identity#node-managed-identity)로 리디렉션하는 네트워킹 규칙이 구성됩니다. 이 NMI DaemonSet은 호출 pod를 식별하고 pod에 요청된 관리 ID에 액세스하도록 적절히 레이블이 지정되었는지 확인합니다. Azure Dev Spaces는 클러스터에 pod 관리 ID가 설치되어 있는지와 Azure Dev Spaces 서비스에서 클러스터의 관리 ID에 액세스할 수 있도록 하는 데 필요한 구성을 수행할 수 없는지를 확인할 수 없습니다. Azure Dev Spaces 서비스는 클러스터의 관리 id에 액세스 하도록 구성 되지 않았기 때문에 NMI DaemonSet는 관리 되는 id에 대 한 Azure AD 토큰을 가져오는 것을 허용 하지 않으며 Azure Dev Spaces 백엔드 서비스와 통신 하지 못합니다.

이 이슈를 해결하려면 *azds-injector-webhook*에 [AzurePodIdentityException](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md)을 적용하고 Azure Dev Spaces에서 관리 ID에 액세스하기 위해 계측하는 pod를 업데이트합니다.

*webhookException.yaml*이라는 파일을 만들고 다음 YAML 정의를 복사합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

위의 파일은 *azds-injector-webhook*에 대해 *AzurePodIdentityException* 개체를 만듭니다. 이 개체를 배포하려면 `kubectl`을 사용합니다.

```cmd
kubectl apply -f webhookException.yaml
```

Azure Dev Spaces에서 관리 ID에 액세스하기 위해 계측하는 pod를 업데이트하려면 아래 YAML 정의에서 *namespace*를 업데이트하고 `kubectl`을 사용하여 각 개발 공간에 적용합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

또는 *AzureIdentity* 및 *AzureIdentityBinding* 개체를 만들고 Azure Dev Spaces에서 AKS 클러스터가 만든 관리 ID에 액세스하기 위해 계측하는 공간에서 실행 중인 워크로드에 대한 pod 레이블을 업데이트할 수 있습니다.

관리 ID에 대한 세부 정보를 나열하려면 AKS 클러스터에 대해 다음 명령을 실행합니다.

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

위 명령은 관리 ID에 대한 *clientId*와 *resourceId*를 출력합니다. 다음은 그 예입니다.

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

*AzureIdentity* 개체를 만들려면 *clusteridentity.yaml*이라는 파일을 만들고 이전 명령에서 관리 ID의 세부 정보로 업데이트한 다음, YAML 정의를 사용합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

*AzureIdentityBinding* 개체를 만들려면 *clusteridentitybinding.yaml*이라는 파일을 만들고 다음 YAML 정의를 사용합니다.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

*AzureIdentity* 및 *AzureIdentityBinding* 개체를 배포하려면 `kubectl`을 사용합니다.

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

*AzureIdentity* 및 *AzureIdentityBinding* 개체를 배포하면 *aadpodidbinding: my-label-value* 레이블이 있는 모든 워크로드에서 클러스터의 관리 ID에 액세스할 수 있습니다. 이 레이블을 추가하고 개발 공간에서 실행 중인 모든 워크로드를 다시 배포합니다. 다음은 그 예입니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Azure Dev Spaces에서 Visual Studio 및 Visual Studio Code를 사용할 때 발생하는 일반적인 이슈

### <a name="error-required-tools-and-configurations-are-missing"></a>"필수 도구 및 구성이 누락되었습니다." 오류 발생

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

PATH 환경 변수가 제대로 설정되어 있는 명령 프롬프트에서 VS Code를 시작합니다.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

최신 버전의 Azure Dev Spaces CLI를 다운로드한 후 설치합니다.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>오류: "다음 유형에 대한 디버거 확장을 찾지 못함: coreclr"

Visual Studio Code 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 개발 머신에 C#용 VS Code 확장이 설치되어 있지 않습니다. C# 확장에는 .NET Core(CoreCLR)용 디버깅 지원이 포함되어 있습니다.

이 이슈를 해결하려면 [C#용 VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>"구성된 디버그 형식 'coreclr'은 지원되지 않습니다." 오류 발생

Visual Studio Code 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 개발 머신에 설치된 Azure Dev Spaces용 VS Code 확장이 없습니다.

이 이슈를 해결하려면 Azure Dev Spaces용 VS Code 확장을 설치합니다.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>오류 "'cwd' 값 '/src'가 잘못되었습니다. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.

Visual Studio Code 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

이 이슈를 해결하려면 프로젝트 폴더의 `.vscode` 하위 디렉터리에서 `launch.json` 파일을 업데이트합니다. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>"파이프 프로그램 'azds'가 코드 126으로 예기치 않게 종료되었습니다." 오류 발생

Visual Studio Code 디버거를 실행할 때 이 오류가 표시될 수 있습니다.

이 이슈를 해결하려면 Visual Studio Code를 닫았다가 다시 엽니다. 디버거를 다시 시작합니다.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js 애플리케이션에 디버깅을 연결할 때 "내부 조사식 실패: ENOSPC 조사" 오류 발생

이 오류는 디버거를 사용하여 연결하려고 하는 Node.js 애플리케이션에서 pod를 실행하는 노드가 *inotify max_user_watches* 값을 초과하는 경우에 발생합니다. 경우에 따라 fs.inotify.max_user_watches의 [기본값*이* 너무 작아서 pod에 대한 디버거 직접 연결을 처리할 수 없습니다](https://github.com/Azure/AKS/issues/772).

이 이슈에 대한 일시적인 해결 방법은 클러스터의 각 노드에서 *fs.inotify.max_user_watches*의 값을 늘리고 해당 노드를 다시 시작하여 변경 내용을 적용하는 것입니다.

## <a name="other-common-issues"></a>기타 일반적인 이슈

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>"azds"가 내부 또는 외부 명령, 실행 가능한 프로그램 또는 일괄 처리 파일로 인식되지 않음 오류 발생

`azds.exe`가 설치되지 않았거나 올바르게 구성되지 않은 경우 이 오류가 발생할 수 있습니다.

이 문제를 해결하려면

1. `azds.exe`에 대한 위치인 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI를 확인합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. `azds.exe`를 설치하지 않은 경우 다음 명령을 실행합니다.

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>"Microsoft.DevSpaces/register/action" 권한 부여 오류 발생

Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다.  Dev Spaces를 관리하려고 하는데 관련 Azure 구독에 대한 *소유자* 또는 *기여자* 액세스 권한이 없는 경우 이 오류가 표시될 수 있습니다. 다음은 그 예입니다.

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

이 이슈를 해결하려면 Azure 구독에 대한 *소유자* 또는 *기여자* 액세스 권한이 있는 계정을 사용하여 `Microsoft.DevSpaces` 네임스페이스를 수동으로 등록합니다.

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>새 pod가 시작되지 않음

Kubernetes 이니셜라이저는 클러스터의 *cluster-admin* 역할에 대한 RBAC 권한 변경으로 인해 새 pod에 PodSpec을 적용할 수 없습니다. 또한 새 pod에 잘못된 PodSpec이 있을 수 있습니다. 예를 들어 pod와 연결된 서비스 계정이 더 이상 존재하지 않을 수 있습니다. 이니셜라이저 문제로 인해 *‘보류 중’* 상태에 있는 pod을 보려면 `kubectl get pods` 명령을 사용합니다.

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

이 이슈는 Azure Dev Spaces를 사용하도록 설정하지 않은 네임스페이스를 포함하여 클러스터의 *‘모든 네임스페이스’* 에 있는 pod에 영향을 줄 수 있습니다.

이 이슈를 해결하려면 [Dev Spaces CLI를 최신 버전으로 업데이트](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)한 후 Azure Dev Spaces 컨트롤러에서 *azds InitializerConfiguration*을 삭제합니다.

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Azure Dev Spaces 컨트롤러에서 *azds InitializerConfiguration*를 제거한 후 `kubectl delete`를 사용하여 *보류 중* 상태의 모든 pod를 제거합니다. 보류 중인 모든 pod를 제거한 후 pod를 다시 배포합니다.

다시 배포한 후에도 새 pod가 *‘보류 중’* 상태로 중지되는 경우 `kubectl delete`를 사용하여 *‘보류 중’* 상태의 모든 pod를 제거합니다. 보류 중인 모든 pod가 제거된 후 클러스터에서 컨트롤러를 삭제했다가 다시 설치합니다.

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

컨트롤러를 다시 설치한 후 pod를 다시 배포합니다.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Dev Spaces 컨트롤러 및 API를 호출하기 위한 RBAC 권한이 올바르지 않음

Azure Dev Spaces 컨트롤러에 액세스하는 사용자는 AKS 클러스터에서 관리자 *kubeconfig*를 읽을 수 있는 권한이 있어야 합니다. 예를 들어 이 권한은 [기본 제공 Azure Kubernetes Service 클러스터 관리자 역할](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)에서 사용할 수 있습니다. 또한 Azure Dev Spaces 컨트롤러에 액세스 하는 사용자에 게는 컨트롤러에 대 한 *참가자* 또는 *소유자* Azure 역할이 있어야 합니다. AKS 클러스터에 대한 사용자 권한을 업데이트하는 방법에 대한 자세한 내용은 [여기](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)를 참조하세요.

컨트롤러에 대 한 사용자의 Azure 역할을 업데이트 하려면:

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. 일반적으로 AKS 클러스터와 동일한 컨트롤러를 포함 하는 리소스 그룹으로 이동합니다.
1. *숨겨진 형식 표시* 확인란을 사용하도록 설정합니다.
1. 컨트롤러를 클릭합니다.
1. *‘액세스 제어(IAM)’* 창을 엽니다.
1. *‘역할 할당’* 탭을 클릭합니다.
1. *‘추가’* 를 클릭한 후 *‘역할 할당 추가’* 를 클릭합니다.
    * *‘역할’* 에 대해 *‘기여자’* 또는 *‘소유자’* 를 선택합니다.
    * *액세스 할당*에서 *Azure AD 사용자, 그룹 또는 서비스 보안 주체*를 선택합니다.
    * *‘선택’* 에서 사용 권한을 부여하려는 사용자를 검색합니다.
1. *저장*을 클릭합니다.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--enable-ingress` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다. 

이 문제를 해결하려면

* Dev Spaces 서비스와 연결된 모든 URL의 상태를 확인합니다.

  ```console
  azds list-uris
  ```

* URL이 *‘보류 중’* 상태이면 Dev Spaces가 DNS 등록 완료를 대기 중임을 의미합니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.
* URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. 다음 명령을 사용하여 이러한 pod를 삭제하고 AKS가 자동으로 다시 만들 수 있도록 허용합니다.
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>"업스트림 연결 오류 또는 헤더 전에 연결 끊김/다시 설정" 오류 발생

서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 이 오류는 컨테이너 포트를 사용할 수 없음을 의미합니다. 이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

이 문제를 해결하려면

1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 다음 자산에서 포트 구성을 확인합니다.
    * **[Helm 차트](https://docs.helm.sh):** `azds prep` 명령에 의해 스캐폴드된 values.yaml에서 `service.port` 및 `deployment.containerPort`가 지정합니다.
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>형식 또는 네임스페이스 이름 "MyLibrary"를 찾을 수 없음

사용 중인 라이브러리 프로젝트를 찾을 수 없습니다. Dev Spaces를 사용하면 기본적으로 빌드 컨텍스트가 프로젝트/서비스 수준에 있습니다.  

이 문제를 해결하려면

1. 빌드 컨텍스트를 솔루션 수준으로 설정하도록 `azds.yaml` 파일을 수정합니다.
2. 새 빌드 컨텍스트와 관련된 프로젝트 파일(예: `.csproj`)을 올바르게 참조하도록 `Dockerfile` 및 `Dockerfile.develop` 파일을 수정합니다.
3. `.sln` 파일과 동일한 디렉터리에 `.dockerignore`를 추가합니다.
4. 필요에 따라 추가 항목으로 `.dockerignore`를 업데이트합니다.

[여기](https://github.com/sgreenmsft/buildcontextsample)에서 예제를 찾을 수 있습니다.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>개발 공간에서 작동하지 않는 수평 pod 자동 크기 조정

개발 공간에서 서비스를 실행하는 경우 해당 서비스의 pod는 [계측을 위한 추가 컨테이너와 함께 삽입되고](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) pod의 모든 컨테이너는 수평 Pod 자동 크기 조정을 위해 리소스 제한 및 요청이 설정되어야 합니다.

이 이슈를 해결하려면 리소스 요청을 적용하고 삽입된 Dev Spaces 컨테이너로 제한합니다. Pod 사양에 `azds.io/proxy-resources` 주석을 추가하여 삽입된 컨테이너(devspaces-proxy)에 리소스 요청 및 제한을 적용할 수 있습니다. 해당 값을 프록시에 대한 컨테이너 사양의 리소스 섹션을 나타내는 JSON 개체로 설정해야 합니다.

다음은 pod 사양에 적용해야 하는 proxy-resources 주석의 예제입니다.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Pod를 실행하여 기존 네임스페이스에서 Azure Dev Spaces 사용

Azure Dev Spaces를 사용하도록 설정하려는 실행 중인 pod에 기존 AKS 클러스터 및 네임스페이스가 있을 수 있습니다.

AKS 클러스터에서 기존 네임스페이스에 대해 Azure Dev Spaces를 사용하도록 설정하려면 `use-dev-spaces`를 실행하고 `kubectl`을 사용하여 해당 네임스페이스의 모든 pod를 다시 시작합니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

pod를 다시 시작하면 Azure Dev Spaces에서 기존 네임스페이스를 사용할 수 있습니다.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 송신 트래픽을 제한하여 AKS 클러스터에서 Azure Dev Spaces 사용

클러스터 노드의 송신 트래픽이 제한되는 AKS 클러스터에 대해 Azure Dev Spaces를 사용하도록 설정하려면 다음 FQDN을 허용해야 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Linux alpine 및 기타 Azure Dev Spaces 이미지를 끌어옵니다. |
| gcr.io | HTTP:443 | helm/tiller 이미지를 끌어옵니다.|
| storage.googleapis.com | HTTP:443 | helm/tiller 이미지를 끌어옵니다.|

위의 Fqdn 및 [Azure Dev Spaces 인프라 서비스](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)모두와의 네트워크 트래픽을 허용 하도록 방화벽 또는 보안 구성을 업데이트 합니다.

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>"구독에서 클러스터를 찾을 수 없습니다. \<cluster\> \<subscriptionId\> " 오류

kubeconfig 파일이 Azure Dev Spaces 클라이언트 쪽 도구에서 사용하려는 것과는 다른 클러스터 또는 구독을 대상으로 하는 경우 이 오류가 표시될 수 있습니다. Azure Dev Spaces 클라이언트 쪽 도구는 [하나 이상의 kubeconfig 파일](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)을 사용하여 클러스터를 선택하고 통신하는 *kubectl*의 동작을 복제합니다.

이 문제를 해결하려면

* `az aks use-dev-spaces -g <resource group name> -n <cluster name>`을 사용하여 현재 컨텍스트를 업데이트합니다. 이 명령은 Azure Dev Spaces를 사용하도록 설정하지 않은 경우에도 AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정합니다. 또는 `kubectl config use-context <cluster name>`을 사용하여 현재 컨텍스트를 업데이트합니다.
* `az account show`를 사용하여 대상으로 지정하는 현재 Azure 구독을 표시하고 올바른지 확인합니다. `az account set`을 사용하여 대상으로 지정하는 구독을 변경할 수 있습니다.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>AKS 인증서를 순환한 후 Dev Spaces를 사용하는 동안 오류 발생

[AKS 클러스터에서 인증서를 순환](../aks/certificate-rotation.md)한 후에 `azds space list` 및 `azds up`과 같은 특정 작업이 실패합니다. 또한 클러스터에서 인증서를 순환한 후 Azure Dev Spaces 컨트롤러에서 인증서를 새로 고쳐야 합니다.

이 이슈를 해결하려면 *kubeconfig* `az aks get-credentials`를 사용하여 업데이트된 인증서가 있는지 확인한 다음, `azds controller refresh-credentials` 명령을 실행합니다. 다음은 그 예입니다.

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
