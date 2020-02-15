---
title: 빠른 시작 - 컨테이너 이미지 빌드 및 실행
description: Azure Container Registry를 사용하여 클라우드에 주문형 Docker 컨테이너 이미지를 빌드하고 실행하는 작업을 신속하게 실행합니다.
ms.topic: quickstart
ms.date: 01/31/2020
ms.openlocfilehash: f08f10dd170acaa8594ad5a47f5ef58e27288b10
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986277"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>빠른 시작: Azure Container Registry 작업을 사용하여 컨테이너 이미지 빌드 및 실행

이 빠른 시작에서는 Azure Container Registry 작업 명령을 사용하여 신속하게 Docker 컨테이너 이미지를 Azure 내에서 빌드, 푸시 및 실행하고, "내부 루프" 개발 주기를 클라우드로 오프로드하는 방법을 보여줍니다. [ACR 작업][container-registry-tasks-overview]은 컨테이너 수명 주기에 걸쳐 컨테이너 이미지를 관리하고 수정할 수 있는 Azure Container Registry 내부 기능 모음입니다. 

이 빠른 시작을 마친 후 ACR 작업의 고급 기능을 더 살펴보세요. 다양한 시나리오를 지원하는 ACR 작업은 코드 커밋 또는 기본 이미지 업데이트를 기반으로 이미지 빌드를 자동화하거나 여러 컨테이너를 병렬로 테스트할 수 있습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 빠른 시작을 완료할 수 있습니다. 로컬로 사용하려는 경우 2.0.58 이상 버전을 권장합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

아직 컨테이너 레지스트리가 없는 경우 [az group create][az-group-create] 명령을 사용하여 리소스 그룹부터 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

[az acr create][az-acr-create] 명령을 사용하여 컨테이너 레지스트리를 만듭니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음 예제에서는 *myContainerRegistry008*을 사용합니다. 이를 고유한 값으로 업데이트합니다.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

이 예제에서는 Azure Container Registry에 대해 학습하고 있는 개발자를 위해 비용 최적화된 옵션인 *기본* 레지스트리를 만듭니다. 사용 가능한 서비스 계층에 대한 자세한 내용은 [컨테이너 레지스트리 SKU][container-registry-skus]를 참조하세요.

## <a name="build-an-image-from-a-dockerfile"></a>Dockerfile에서 이미지 빌드

이제 Azure Container Registry를 사용하여 이미지를 빌드합니다. 먼저 작업 디렉터리를 만든 후, 다음 콘텐츠를 사용하여 *Dockerfile*이라는 Dockerfile을 만듭니다. 이 예제는 Linux 컨테이너 이미지를 빌드하는 간단한 예제이지만, 여러분만의 표준 Dockerfile을 만들고 다른 플랫폼에 대한 이미지를 빌드할 수도 있습니다. 이 문서의 예제 명령은 bash 셸용으로 서식이 지정되어 있습니다.

```bash
echo FROM hello-world > Dockerfile
```

[az acr build][az-acr-build] 명령을 실행하여 이미지를 빌드합니다. 성공적으로 빌드된 이미지는 레지스트리로 푸시됩니다. 다음 예제는 `sample/hello-world:v1` 이미지를 푸시합니다. 명령 끝부분의 `.`는 Dockerfile의 위치(이 예에서는 현재 디렉터리)를 설정합니다.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

빌드 및 푸시가 성공하면 다음과 비슷한 출력이 표시됩니다.

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>이미지 실행

이제 빌드하여 레지스트리로 푸시한 이미지를 신속하게 실행합니다. 여기에서 [az acr run][az-acr-run]을 사용하여 컨테이너 명령을 실행합니다. 컨테이너 개발 워크플로에서 이 작업은 이미지를 배포하기 전에 실행하는 유효성 검사 단계이거나 [다단계 YAML 파일][container-registry-tasks-multi-step]에 명령을 포함할 수 있습니다. 

다음 예제에서는 `$Registry`를 사용하여 명령을 실행하는 레지스트리를 지정합니다.

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

이 예제의 `cmd` 매개 변수는 컨테이너를 기본 구성으로 실행하지만, `cmd`는 추가 `docker run` 매개 변수 또는 기타 `docker` 명령도 지원합니다.

다음과 유사하게 출력됩니다.

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 레지스트리 및 저장된 컨테이너 이미지를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ACR 작업의 기능을 사용하여 로컬 Docker를 설치하지 않고 Azure 내에서 기본적으로 Docker 컨테이너 이미지를 신속하게 빌드, 푸시 및 실행했습니다. Azure Container Registry 작업 자습서를 계속 진행하여 ACR 작업으로 이미지 빌드 및 업데이트를 자동화하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 작업 자습서][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
