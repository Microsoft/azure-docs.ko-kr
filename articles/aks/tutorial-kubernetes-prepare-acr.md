---
title: Azure의 Kubernetes 자습서 - 컨테이너 레지스트리 만들기
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 Azure Container Registry 인스턴스를 만들고 애플리케이션 예제 컨테이너 이미지를 업로드합니다.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 197e5c7bed569e67376f9c28fe0d2e050016cce8
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922407"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>자습서: Azure Container Registry 배포 및 사용

ACR(Azure Container Registry)은 컨테이너 이미지를 위한 프라이빗 레지스트리입니다. 프라이빗 컨테이너 레지스트리를 사용하면 애플리케이션 및 사용자 지정 코드를 안전하게 빌드하고 배포할 수 있습니다. 7부 중 2부에 해당하는 이 자습서에서는 ACR 인스턴스를 배포하고 컨테이너 이미지를 푸시합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * ACR(Azure Container Registry) 인스턴스 만들기
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드
> * 레지스트리의 이미지 보기

추가 자습서에서 이 ACR 인스턴스는 AKS의 Kubernetes 클러스터와 통합되고, 이미지로 애플리케이션을 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

[이전 자습서][aks-tutorial-prepare-app]에서는 간단한 Azure Voting 애플리케이션에 컨테이너 이미지를 만들었습니다. Azure Voting 앱 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 돌아갑니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

Azure Container Registry를 만들려면 먼저 리소스 그룹이 필요합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create][az-acr-create] 명령을 사용하여 Azure Container Registry 인스턴스를 만들고 고유한 레지스트리 이름을 입력합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 이 자습서의 나머지 부분에서는 컨테이너 레지스트리 이름의 자리 표시자로 `<acrName>`을 사용합니다. 사용자 고유의 레지스트리 이름을 제공합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 스토리지와 처리량의 균형을 적절하게 맞추었습니다.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>컨테이너 레지스트리에 로그인

ACR 인스턴스를 사용하려면 먼저 로그인해야 합니다. [az acr login][az-acr-login] 명령을 사용하고 이전 단계에서 컨테이너 레지스트리에 지정한 고유의 이름을 입력합니다.

```azurecli
az acr login --name <acrName>
```

이 명령이 완료되면 *로그인했습니다.* 라는 메시지를 반환합니다.

## <a name="tag-a-container-image"></a>컨테이너 이미지 태그 지정

현재 로컬 이미지 목록을 보려면 [docker images][docker-images] 명령을 사용합니다.

```azurecli
$ docker images
```
위의 명령 출력에는 현재 로컬 이미지 목록이 표시됩니다.

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

ACR에 *azure-vote-front* 컨테이너 이미지를 사용하려면 레지스트리의 로그인 서버 주소를 사용하여 이미지에 태그를 지정해야 합니다. 이 태그는 컨테이너 이미지를 이미지 레지스트리에 밀어넣을 때 라우팅에 사용됩니다.

로그인 서버 주소를 가져오려면 다음과 같이 [az acr list][az-acr-list] 명령을 사용하여 *loginServer*를 쿼리합니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

이제 컨테이너 레지스트리의 *acrLoginServer* 주소를 사용하여 로컬 *azure-vote-front* 이미지에 태그를 지정합니다. 이미지 버전을 표시하려면 이미지 이름 끝에 *:v1*을 추가합니다.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

태그가 적용되었는지 확인하려면 [docker images][docker-images] 명령을 다시 사용합니다. 

```azurecli
$ docker images
```

ACR 인스턴스 주소와 버전 번호가 이미지의 태그로 지정되었습니다.

```
REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

빌드 및 태그가 지정된 이미지를 사용하여 *azure-vote-front* 이미지를 ACR 인스턴스로 푸시합니다. 다음과 같이 [docker push][docker-push] 명령을 사용하여 고유의 *acrLoginServer* 주소를 이미지 이름으로 입력합니다.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

이미지를 ACR로 푸시하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="list-images-in-registry"></a>레지스트리에서 이미지 나열

ACR 인스턴스로 푸시된 이미지 목록을 반환하려면 [az acr repository list][az-acr-repository-list] 명령을 사용합니다. 다음과 같이 고유의 `<acrName>`를 입력합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

다음 예제 출력은 레지스트리에서 사용 가능한 *azure-vote-front* 이미지를 나열합니다.

```
Result
----------------
azure-vote-front
```

특정 이미지의 태그를 보려면 다음과 같이 [az acr repository show-tags][az-acr-repository-show-tags] 명령을 사용합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

다음 예제 출력은 이전 단계에서 태그를 지정한 *v1* 이미지를 보여줍니다.

```
Result
--------
v1
```

이제 프라이빗 Azure Container Registry 인스턴스에 컨테이너 이미지가 저장되었습니다. 그 다음 자습서에서 이 이미지는 ACR에서 Kubernetes 클러스터로 배포됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS 클러스터에서 사용하기 위해 Azure Container Registry를 만들고 이미지를 푸시했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * ACR(Azure Container Registry) 인스턴스 만들기
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드
> * 레지스트리의 이미지 보기

그 다음 자습서로 이동하여 Azure에서 Kubernetes 클러스터를 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Kubernetes 클러스터 배포][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
