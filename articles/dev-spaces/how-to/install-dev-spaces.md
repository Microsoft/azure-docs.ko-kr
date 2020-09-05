---
title: AKS에서 Azure Dev Spaces 사용 및 클라이언트 쪽 도구 설치
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정하고 클라이언트 쪽 도구를 설치하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: eb7c9b4d3d03b6f4f1f21e6fb2b2a60aa303b181
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212437"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>AKS 클러스터에서 Azure Dev Spaces 사용 및 클라이언트 쪽 도구 설치

이 문서에서는 AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정하고 클라이언트 쪽 도구를 설치하는 여러 가지 방법을 보여 줍니다.

## <a name="enable-azure-dev-spaces-using-the-cli"></a>CLI를 사용 하 여 Azure Dev Spaces 사용

CLI를 사용하여 Dev Spaces를 사용하도록 설정하려면 다음이 필요합니다.
* Azure 구독 Azure 구독이 없는 경우 [체험 계정][az-portal-create-account]을 만들 수 있습니다.
* [Azure CLI가 설치되어 있습니다][install-cli].
* [지원되는 지역][supported-regions]에 [AKS 클러스터][create-aks-cli]가 있습니다.

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

위 명령은 *myResourceGroup* 그룹의 *myAKSCluster* 클러스터에서 Dev Spaces를 사용하도록 설정하고 *기본* 개발 공간을 만듭니다.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

또한 `use-dev-spaces` 명령은 Azure Dev Spaces CLI도 설치합니다.

## <a name="install-the-client-side-tools"></a>클라이언트 쪽 도구 설치

Azure Dev Spaces 클라이언트 쪽 도구를 사용하여 로컬 머신에서 AKS 클러스터의 Dev Spaces와 상호 작용할 수 있습니다. 다음과 같은 몇 가지 방법으로 클라이언트 쪽 도구를 설치할 수 있습니다.

* [Visual Studio Code][vscode]에서 [Azure Dev Spaces 확장][vscode-extension]을 설치합니다.
* [Visual Studio 2019][visual-studio]에서 Azure Development 워크로드를 설치합니다.
* [Windows][cli-win], [Mac][cli-mac]또는 [Linux][cli-linux] CLI를 다운로드하여 설치합니다.

## <a name="remove-azure-dev-spaces-using-the-cli"></a>CLI를 사용 하 여 Azure Dev Spaces 제거

AKS 클러스터에서 Azure Dev Spaces를 제거하려면 `azds remove` 명령을 사용합니다.

```azurecli
azds remove -g MyResourceGroup -n MyAKS
```

아래 예제 출력에서는 *MyAKS* 클러스터에서 Azure Dev Spaces를 제거 하는 방법을 보여 줍니다.

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Azure Dev Spaces를 사용하여 만든 네임스페이스는 워크로드와 함께 유지되지만 해당 네임스페이스의 새 워크로드는 Azure Dev Spaces를 사용하여 계측되지 않습니다. 또한 Azure Dev Spaces를 사용하여 계측된 기존 pod를 다시 시작하면 오류가 표시될 수 있습니다. 이러한 pod는 Azure Dev Spaces 도구를 사용하지 말고 다시 배포해야 합니다. 클러스터에서 Azure Dev Spaces를 완전히 제거하려면 Azure Dev Spaces가 사용하도록 설정된 모든 네임스페이스의 모든 pod를 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](../how-dev-spaces-works.md)

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
