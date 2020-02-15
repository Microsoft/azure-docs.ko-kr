---
title: Azure의 Kubernertes 자습서 - 클러스터 배포
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 AKS 클러스터를 만들고 kubectl을 사용하여 Kubernetes 마스터 노드에 연결합니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 130cb973f2de1de0d4c4636a4752a06e22edf65b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932555"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>자습서: AKS(Azure Kubernetes Service) 클러스터 배포

Kubernetes는 컨테이너화된 애플리케이션용 분산 플랫폼을 제공합니다. AKS를 사용하면 프로덕션 준비 Kubernetes 클러스터를 신속하게 만들 수 있습니다. 총 7부 중 3부인 이 자습서에서는 Kubernetes 클러스터가 AKS에 배포됩니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure container registry에 인증할 수 있는 Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * AKS 클러스터에 연결하도록 kubectl 구성

추가 자습서에서 Azure Vote 애플리케이션은 클러스터에 배포되고, 크기가 조정되며, 업데이트됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 컨테이너 이미지를 만들어 Azure Container Registry 인스턴스에 업로드했습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]에서 시작합니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.75 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-a-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

AKS 클러스터는 Kubernetes RBAC(역할 기반 액세스 제어)를 사용할 수 있습니다. 이러한 컨트롤을 통해 사용자에게 할당된 역할에 따라 리소스에 대한 액세스를 정의할 수 있습니다. 사용자에게 여러 역할이 할당된 경우 권한을 결합할 수 있으며, 권한의 범위를 단일 네임스페이스 또는 전체 클러스터로 지정할 수 있습니다. 기본적으로 Azure CLI는 사용자가 AKS 클러스터를 만들면 자동으로 RBAC를 사용하도록 설정합니다.

[az aks create][] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster*라는 클러스터를 만듭니다. 이 리소스 그룹은 [이전 자습서][aks-tutorial-prepare-acr]에서 만들었습니다. AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 Azure Active Directory 서비스 주체가 지정되어 있지 않기 때문에 자동으로 생성됩니다. 여기에서 이 서비스 주체는 이전 자습서에서 만든 ACR(Azure Container Registry) 인스턴스에서 [이미지를 끌어올 수 있는 권한을 부여][container-registry-integration]했습니다.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

몇 분 후에 배포가 완료되고 JSON 형식의 AKS 배포 관련 정보가 반환됩니다.

> [!NOTE]
> 클러스터를 안정적으로 작동하도록 하려면 2개 이상의 노드를 실행해야 합니다.

## <a name="install-the-kubernetes-cli"></a>Kubernetes CLI 설치

로컬 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다.

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [az aks install-cli][] 명령을 사용하여 kubectl을 로컬로 설치할 수도 있습니다.

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>kubectl을 사용하여 클러스터에 연결

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup*에서 AKS 클러스터 이름 *myAKSCluster*에 대한 자격 증명을 가져옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get nodes][kubectl-get] 명령을 실행하여 클러스터 노드 목록을 반환합니다.

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.13.10
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS에 Kubernetes 클러스터를 배포하고, 이 클러스터에 연결하도록 `kubectl`을 구성했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure container registry에 인증할 수 있는 Kubernetes AKS 클러스터 배포
> * Kubernetes CLI(kubectl) 설치
> * AKS 클러스터에 연결하도록 kubectl 구성

그 다음 자습서를 계속 진행하여 클러스터에 애플리케이션을 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Kubernetes에서 애플리케이션 배포][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
