---
title: Docker 이미지를 개인 Azure 컨테이너 레지스트리에 푸시
description: Docker CLI를 사용하여 Azure의 개인 컨테이너 레지스트리로 Docker 이미지 밀어넣기 및 끌어오기
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 2cb401dfd68075ff0867ae3f89eee3474000b5de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828774"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Docker CLI를 사용하여 개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기 및 끌어오기

Azure Container Registry는 [Docker Hub](https://hub.docker.com/)에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 [Docker](https://hub.docker.com) 컨테이너 이미지를 저장하고 관리합니다. [Docker 명령줄 인터페이스](https://docs.docker.com/engine/reference/commandline/cli/)(Docker CLI)를 사용하여 컨테이너 레지스트리에서 [로그인](https://docs.docker.com/engine/reference/commandline/login/), [푸시](https://docs.docker.com/engine/reference/commandline/push/), [풀](https://docs.docker.com/engine/reference/commandline/pull/) 및 기타 작업을 수행합니다.

다음 단계에서는 공개 Docker Hub 레지스트리에서 공식 [Nginx 이미지](https://store.docker.com/images/nginx)를 다운로드하고 개인 Azure 컨테이너 레지스트리용으로 태그를 지정하고, 레지스트리에 푸시한 다음 레지스트리에서 풀합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* **Docker CLI** - 또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.

## <a name="log-in-to-a-registry"></a>레지스트리에 로그인

개인 컨테이너 레지스트리에 대해 [몇 가지 방법으로 인증](container-registry-authentication.md)할 수 있습니다. 명령줄에서 작업할 때 권장되는 방법은 Azure CLI 명령 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)입니다. 예를 들어 이름이 *myregistry*인 레지스트리에 로그인하려면

```azurecli
az acr login --name myregistry
```

[docker login](https://docs.docker.com/engine/reference/commandline/login/)을 사용하여 로그인할 수도 있습니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 [서비스 주체를 할당](container-registry-authentication.md#service-principal)할 수 있습니다. 다음 명령을 실행할 때 메시지가 표시되면 서비스 주체 appID(사용자 이름) 및 암호를 대화식으로 입력하세요. 로그인 자격 증명 관리 모범 사례는 [docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하세요.

```
docker login myregistry.azurecr.io
```

두 명령 모두 완료되면 `Login Succeeded`를 반환합니다.

> [!TIP]
> `docker login`을 사용하고 레지스트리에 푸시하기 위해 이미지에 태그할 때는 항상 정규화된 레지스트리 이름(전체 소문자)를 지정합니다.  이 문서의 예제에서 정규화된 이름은 *myregistry.azurecr.io*입니다.

## <a name="pull-the-official-nginx-image"></a>공식 Nginx 이미지 풀하기

먼저 공개 Nginx 이미지를 로컬 컴퓨터로 풀합니다.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

다음 [docker run](https://docs.docker.com/engine/reference/run/) 명령을 실행하여 포트 8080에서 Nginx 컨테이너의 로컬 인스턴스를 대화형으로 시작합니다(`-it`). `--rm` 인수는 사용자가 해당 컨테이너를 중지하면 제거되어야 함을 지정합니다.

```
docker run -it --rm -p 8080:80 nginx
```

이동할 `http://localhost:8080` 실행 중인 컨테이너에서 Nginx가 서비스 기본 웹 페이지를 표시 합니다. 다음과 유사한 결과가 표시됩니다.

![로컬 컴퓨터의 Nginx](./media/container-registry-get-started-docker-cli/nginx.png)

`-it`를 통해 대화형으로 컨테이너를 시작했으므로 브라우저로 이동한 후 명령줄에서 Nginx 서버의 출력을 볼 수 있습니다.

컨테이너를 중지하고 제거하려면 `Control`+`C`를 누릅니다.

## <a name="create-an-alias-of-the-image"></a>이미지의 별칭 만들기 

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/)를 사용하여 레지스트리에 대한 정규화된 경로를 통한 이미지의 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 `samples` 네임스페이스를 지정합니다.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

네임스페이스를 통한 태그 지정에 대한 자세한 내용은 [Azure Container Registry 모범 사례](container-registry-best-practices.md#repository-namespaces)의 [리포지토리 네임스페이스](container-registry-best-practices.md) 섹션을 참조하세요.

## <a name="push-the-image-to-your-registry"></a>레지스트리에 이미지 푸시하기

이제 개인 레지스트리에 대한 정규화된 경로로 이미지에 태그를 지정했으므로 [docker push](https://docs.docker.com/engine/reference/commandline/push/)로 레지스트리에 푸시할 수 있습니다.

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>레지스트리에서 이미지 풀하기

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 레지스트리에서 이미지를 풀합니다.

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Nginx 컨테이너 시작

[docker run](https://docs.docker.com/engine/reference/run/) 명령을 사용하여 레지스트리에서 풀한 이미지를 실행합니다.

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

이동할 `http://localhost:8080` 실행 중인 컨테이너를 봅니다.

컨테이너를 중지하고 제거하려면 `Control`+`C`를 누릅니다.

## <a name="remove-the-image-optional"></a>이미지 제거(선택 사항)

Nginx 이미지가 더 이상 필요하지 않으면 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) 명령을 사용하여 로컬로 삭제할 수 있습니다.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Azure 컨테이너 레지스트리에서 이미지를 제거하려면 Azure CLI 명령 [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete)를 사용합니다. 예를 들어, 다음 명령은 `samples/nginx:latest` 태그에서 참조하는 매니페스트, 모든 고유한 계층 데이터, 기타 해당 매니페스트를 참조하는 모든 태그를 삭제합니다.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>다음 단계

이제 기본 사항을 배웠으니 레지스트리 사용을 시작할 준비가 되었습니다. 예를 들어 레지스트리에서 다음에 컨테이너 이미지를 배포합니다.

* [AKS(Azure Kubernetes Service)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

선택적으로 Azure 컨테이너 레지스트리와 작동할 [Visual Studio Code용 Docker 확장](https://code.visualstudio.com/docs/azure/docker)과 [Azure 계정](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 확장을 설정합니다. Visual Studio Code 내에서 Azure 컨테이너 레지스트리에 이미지를 밀어넣고 끌어오거나, ACR Tasks를 실행합니다.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
