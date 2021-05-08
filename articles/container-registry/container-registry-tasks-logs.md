---
title: 작업 실행 로그 보기 - 작업
description: ACR 작업에서 생성된 실행 로그를 보고 관리하는 방법입니다.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781058"
---
# <a name="view-and-manage-task-run-logs"></a>작업 실행 로그 보기 및 관리

[Azure Container Registry 작업](container-registry-tasks-overview.md)에서 실행되는 각 작업은 작업 단계가 성공적으로 실행되었는지를 확인하기 위해 검사할 수 있는 로그 출력을 생성합니다. 

이 문서에서는 작업 실행 로그를 보고 관리하는 방법을 설명합니다.

## <a name="view-streamed-logs"></a>스트리밍된 로그 보기

수동으로 작업을 트리거하는 경우 로그 출력이 콘솔로 직접 스트리밍됩니다. 예를 들어 [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run) 또는 [az acr task run](/cli/azure/acr/task#az_acr_task_run) 명령을 사용하여 수동으로 작업을 트리거하면 로그 출력이 콘솔에 스트리밍됩니다. 

다음 샘플 [az acr run](/cli/azure/acr#az_acr_run) 명령은 동일한 레지스트리에서 끌어온 컨테이너를 실행하는 작업을 수동으로 트리거합니다.

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

스트리밍된 로그:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>저장된 로그 보기 

Azure Container Registry는 모든 작업에 대해 실행 로그를 저장합니다. Azure Portal에서 저장된 실행 로그를 볼 수 있습니다. 또는 [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) 명령을 사용하여 선택한 로그를 봅니다. 기본적으로 로그는 30일 동안 보존됩니다.

예를 들어 소스 코드 업데이트에 의해 작업이 자동으로 트리거되는 경우 저장된 로그에 액세스하는 것이 실행 로그를 볼 수 있는 *유일한* 방법입니다. 자동 작업 트리거는 소스 코드 커밋 또는 끌어오기 요청, 기본 이미지 업데이트 및 타이머 트리거를 포함합니다.

포털에서 실행 로그를 보려면 다음을 수행합니다.

1. 컨테이너 레지스트리로 이동합니다.
1. **서비스** 에서 **작업** > **실행** 을 선택합니다.
1. 실행 상태와 실행 로그를 보려면 **실행 ID** 를 선택합니다. 로그에는 스트리밍 로그와 동일한 정보(생성된 경우)가 포함됩니다.

![작업 실행 로그인 포털 보기](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Azure CLI를 사용하여 로그를 보려면 [az acr task logs](/cli/azure/acr/task#az_acr_task_logs)를 실행하고 실행 ID, 작업 이름 또는 빌드 작업으로 만든 특정 이미지를 지정합니다. 작업 이름이 지정된 경우 명령은 마지막으로 만든 실행에 대한 로그를 표시합니다.

다음 예는 ID가 *cf4* 인 실행에 대한 로그를 출력합니다.

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>대체 로그 스토리지

작업 실행 로그를 로컬 파일 시스템에 저장하거나 다른 보관 솔루션(예: Azure Storage)을 사용할 수 있습니다.

예를 들어 로컬 *tasklogs* 디렉터리를 만들고 [az acr task logs](/cli/azure/acr/task#az_acr_task_logs)의 출력을 로컬 파일로 리디렉션합니다.

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

로컬 로그 파일을 Azure Storage에 저장할 수도 있습니다. 예를 들어 [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) 또는 기타 방법을 사용하여 스토리지 계정에 파일을 업로드합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Container Registry 작업](container-registry-tasks-overview.md)에 대해 자세히 알아보기


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
