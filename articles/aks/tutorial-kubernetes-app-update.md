---
title: Azure의 Kubernetes 자습서 - 애플리케이션 업데이트
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 새 버전의 애플리케이션 코드를 사용하여 기존 애플리케이션 배포를 AKS로 업데이트하는 방법을 알아봅니다.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 05eac7e673ad01e9d3e0fb25f261444fd7bc4e6d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475505"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 업데이트

Kubernetes에서 애플리케이션을 배포한 후 새 컨테이너 이미지 또는 이미지 버전을 지정하여 해당 애플리케이션을 업데이트할 수 있습니다. 배포의 일부분만 동시에 업데이트되도록 업데이트가 스테이징됩니다. 이처럼 스테이징 업데이트가 수행되므로 업데이트 중에도 애플리케이션을 계속 실행할 수 있습니다. 또한 배포 오류가 발생하는 경우에는 롤백 메커니즘도 제공됩니다.

이 자습서(전체 7부 중 6부)에서는 샘플 Azure 투표 앱을 업데이트합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 푸시
> * 업데이트된 컨테이너 이미지 배포

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서 애플리케이션은 컨테이너 이미지로 패키징되었습니다. 이 이미지는 Azure Container Registry로 업로드되었고, AKS 클러스터를 만들었습니다. 그런 다음, 애플리케이션은 AKS 클러스터에 배포되었습니다.

이 자습서에서 사용한 미리 작성된 Docker Compose 파일과 애플리케이션 소스 코드를 포함하는 애플리케이션 리포지토리도 복제했습니다. 리포지토리 복제본을 만들었으며, 디렉터리를 복제된 디렉터리로 변경했는지 확인합니다. 이러한 단계를 완료하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 시작합니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="update-an-application"></a>응용 프로그램 업데이트

샘플 애플리케이션을 변경한 다음, AKS 클러스터에 배포된 버전을 업데이트하겠습니다. 복제된 *azure-voting-app-redis* 디렉터리에 있는지 확인합니다. 그런 다음, *azure-vote* 디렉터리 내에서 애플리케이션 예제 소스 코드를 찾을 수 있습니다. `vi` 같은 편집기를 사용하여 *config_file.cfg* 파일을 엽니다.

```console
vi azure-vote/azure-vote/config_file.cfg
```

*VOTE1VALUE* 및 *VOTE2VALUE* 값을 색과 같은 다른 값으로 변경합니다. 다음 예제에서는 업데이트된 값을 보여줍니다.

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

파일을 저장하고 닫습니다. `vi`에서 `:wq`를 사용합니다.

## <a name="update-the-container-image"></a>컨테이너 이미지 업데이트

프런트 엔드 이미지를 다시 만들고 업데이트된 애플리케이션을 테스트하려면 [docker-compose][docker-compose] 명령을 사용합니다. `--build` 인수를 사용하여 응용 프로그램 이미지를 다시 만들도록 Docker Compose에 명령합니다.

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>로컬에서 애플리케이션 테스트

업데이트된 컨테이너 이미지에 변경 내용이 적용되었는지 확인하고, 로컬 웹 브라우저를 `http://localhost:8080`으로 엽니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated.png)

*config_file.cfg* 파일에 제공된 업데이트된 값은 실행 중인 애플리케이션에 표시됩니다.

## <a name="tag-and-push-the-image"></a>이미지 태그 지정 및 밀어넣기

업데이트된 이미지를 올바르게 사용하려면 *azure-vote-front* 이미지에 ACR 레지스트리의 로그인 서버 이름을 태그로 지정해야 합니다. [az acr list](/cli/azure/acr) 명령을 사용하여 로그인 서버 이름을 가져옵니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag][docker-tag]를 사용하여 이미지에 태그를 지정합니다. 다음과 같이 `<acrLoginServer>`를 ACR 로그인 서버 이름 또는 공용 레지스트리 호스트 이름으로 바꾸고, 이미지 버전을 *:v2*로 업데이트합니다.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

이제 [docker push][docker-push]를 사용하여 레지스트리에 이미지를 업로드합니다. `<acrLoginServer>`를 ACR 로그인 서버 이름으로 바꿉니다.

> [!NOTE]
> ACR 레지스트리로 푸시하는 데 문제가 있는 경우 여전히 로그인되어 있는지 확인합니다. [Azure Container Registry 만들기](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) 단계에서 생성한 Azure Container Registry의 이름을 사용하여 [az acr login][az-acr-login] 명령을 실행합니다. 예: `az acr login --name <azure container registry name>`

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>업데이트된 애플리케이션 배포

최대 작동 시간을 제공하려면 애플리케이션 Pod의 여러 인스턴스가 실행되고 있어야 합니다. [kubectl get pods][kubectl-get] 명령을 사용하여 실행 중인 프런트 엔드 인스턴스 수를 확인합니다.

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

프런트 엔드 Pod가 하나만 있는 경우 다음과 같이 *azure-vote-front* 배포를 확장합니다.

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

애플리케이션을 업데이트하려면 [kubectl set][kubectl-set] 명령을 사용합니다. `<acrLoginServer>`를 컨테이너 레지스트리의 로그인 서버 또는 호스트 이름으로 업데이트하고, *v2* 응용 프로그램 버전을 지정합니다.

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

배포를 모니터링하려면 [kubectl get pod][kubectl-get] 명령을 사용합니다. 업데이트된 애플리케이션이 배포되면 Pod가 종료되고 새 컨테이너 이미지로 다시 만들어집니다.

```console
kubectl get pods
```

다음 예제 출력은 배포가 진행됨에 따라 Pod가 종료되고 새 인스턴스가 실행되는 것을 보여줍니다.

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>업데이트된 애플리케이션 테스트

업데이트된 애플리케이션을 보려면 먼저 `azure-vote-front` 서비스의 외부 IP 주소를 가져옵니다.

```console
kubectl get service azure-vote-front
```

로컬 웹 브라우저를 서비스의 IP 주소로 엽니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 애플리케이션을 업데이트하고 AKS 클러스터에 배포했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 프런트 엔드 응용 프로그램 코드 업데이트
> * 업데이트된 컨테이너 이미지 만들기
> * Azure Container Registry에 컨테이너 이미지 푸시
> * 업데이트된 컨테이너 이미지 배포

다음 자습서를 진행하여 AKS 클러스터를 새 버전의 Kubernetes로 업그레이드하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Kubernetes 업그레이드][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
