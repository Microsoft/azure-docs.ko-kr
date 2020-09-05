---
title: 빠른 시작 - Azure CLI를 사용하여 첫 번째 Batch 작업 실행
description: Azure CLI를 사용하여 Batch 계정을 만들고 Batch 작업을 실행하는 방법을 빠르게 알아봅니다.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8824d4485167955dd1b928bc57381b2e6b672c5d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213101"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 첫 번째 Batch 작업 실행

Azure CLI를 사용하여 Batch 계정, 컴퓨팅 노드 풀(가상 머신) 및 풀에서 태스크를 실행하는 작업을 만들어 Azure Batch를 시작합니다. 각 샘플 태스크는 풀 노드 중 하나에서 기본 명령을 실행합니다.

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 빠른 시작을 완료하면, Batch 서비스의 주요 개념을 이해하고 더 큰 규모의 더 실제적인 작업으로 Batch를 시도할 준비가 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- CLI를 로컬로 설치하여 사용하도록 선택하려면 이 빠른 시작에서 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus2* 위치에 *QuickstartBatch-rg*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure Storage 계정과 배치 계정을 연결할 수 있습니다. 스토리지 계정은 이 빠른 시작에서 필요하지 않지만, 애플리케이션을 배포하고 대부분의 실제 작업에 대한 입력 및 출력 데이터를 저장하는 데 유용합니다. [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 사용하여 리소스 그룹에 스토리지 계정을 만듭니다.

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch 계정 만들기

[az batch account create](/cli/azure/batch/account#az-batch-account-create) 명령을 사용하여 배치 계정을 만듭니다. 컴퓨팅 리소스(컴퓨팅 노드의 풀) 및 Batch 작업을 만들려면 계정이 필요합니다.

다음 예제에서는 *mybatchaccount*라는 배치 계정을 *QuickstartBatch-rg*에 만들고, 사용자가 만든 스토리지 계정을 연결합니다.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

컴퓨팅 풀 및 작업을 만들고 관리하려면 Batch를 통해 인증해야 합니다. [az batch account login](/cli/azure/batch/account#az-batch-account-login) 명령으로 계정에 로그인합니다. 로그인되면 이 계정 컨텍스트가 `az batch` 명령에 사용됩니다.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>컴퓨팅 노드 풀 만들기

이제 배치 계정이 있으므로 [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) 명령을 사용하여 Linux 컴퓨팅 노드의 샘플 풀을 만듭니다. 다음 예제에서는 Ubuntu 16.04 LTS를 실행하는 *Standard_A1_v2* 크기의 2개 노드로 구성되는 *mypool*이라는 풀을 만듭니다. 제안된 노드 크기는 이 빠른 예제의 성능과 비용에 대한 적절한 균형을 제공합니다.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

Batch는 풀을 즉시 만들지만, 컴퓨팅 노드를 할당하고 시작하는 데 몇 분이 걸립니다. 이 시간 동안 풀의 상태는 `resizing`입니다. 풀의 상태를 보려면 [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) 명령을 실행합니다. 이 명령은 풀의 모든 속성을 표시하고 특정 속성을 쿼리할 수 있습니다. 다음 명령은 풀의 할당 상태를 가져옵니다.

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

풀 상태가 변경되는 동안 작업 및 태스크를 만들려면 다음 단계를 계속합니다. 할당 상태가 `steady`이고 모든 노드가 실행되고 있으면 풀에서 태스크를 실행할 준비가 됩니다.

## <a name="create-a-job"></a>작업 만들기

이제 풀이 있으므로 여기에서 실행할 작업을 만들 수 있습니다. Batch 작업은 하나 이상의 태스크에 대한 논리적 그룹입니다. 작업에는 우선 순위 및 태스크를 실행할 풀과 같은 태스크에 공통적으로 적용되는 설정이 포함됩니다. [az batch job create](/cli/azure/batch/job#az-batch-job-create) 명령을 사용하여 Batch 작업을 만듭니다. 다음 예제에서는 *mypool* 풀에 *myjob* 작업을 만듭니다. 처음에는 작업에 태스크가 없습니다.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>태스크 만들기

이제 [az batch task create](/cli/azure/batch/task#az-batch-task-create) 명령을 사용하여 작업에서 실행할 일부 태스크를 만듭니다. 이 예제에서는 네 개의 동일한 태스크를 만듭니다. 각 태스크는 `command-line`을 실행하여 컴퓨팅 노드에 Batch 환경 변수를 표시한 다음 90초 동안 기다립니다. Batch를 사용하면 이 명령줄에서 앱 또는 스크립트를 지정합니다. Batch는 컴퓨팅 노드에 앱과 스크립트를 배포하는 여러 가지 방법을 제공합니다.

다음 Bash 스크립트에서는 4개의 병렬 태스크(*mytask1*-*mytask4*)를 만듭니다.

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

명령 출력에는 각 태스크에 대한 설정이 표시됩니다. Batch에서 태스크를 컴퓨팅 노드에 배포합니다.

## <a name="view-task-status"></a>태스크 상태 보기

태스크가 만들어지면 Batch는 풀에서 실행되도록 해당 태스크를 큐에 넣습니다. 노드에서 실행할 수 있게 되면 태스크가 실행됩니다.

[az batch task show](/cli/azure/batch/task#az-batch-task-show) 명령을 사용하여 Batch 태스크의 상태를 봅니다. 다음 예제에서는 풀 노드 중 하나에서 실행되는 *mytask1*에 대한 세부 정보가 표시됩니다.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

명령 출력에는 많은 세부 정보가 포함되어 있지만, 태스크 명령줄의 `exitCode` 및 `nodeId`를 적어둡니다. `exitCode`가 0이면 태스크 명령줄이 성공적으로 완료되었음을 나타냅니다. `nodeId`는 태스크가 실행된 풀 노드의 ID를 나타냅니다.

## <a name="view-task-output"></a>태스크 출력 보기

컴퓨팅 노드에서 태스크로 만들어진 파일을 나열하려면 [az batch task file list](/cli/azure/batch/task) 명령을 사용합니다. 다음 명령은 *mytask1*로 만들어진 파일을 나열합니다.

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

다음과 유사하게 출력됩니다.

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

출력 파일 중 하나를 로컬 디렉터리에 다운로드하려면 [az batch task file download](/cli/azure/batch/task) 명령을 사용합니다. 이 예제에서 태스크 출력은 `stdout.txt`에 있습니다.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

텍스트 편집기에서 `stdout.txt`의 내용을 볼 수 있습니다. 내용에는 노드에 설정된 Azure Batch 환경 변수가 표시됩니다. 사용자 고유의 Batch 작업을 만들 때, 태스크 명령줄과 이 명령줄에서 실행되는 앱 및 스크립트에서 이러한 환경 변수를 참조할 수 있습니다. 예를 들면 다음과 같습니다.

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>리소스 정리

Batch 자습서 및 샘플을 계속 사용하려면 이 빠른 시작에서 만든 배치 계정 및 연결된 스토리지 계정을 사용합니다. 배치 계정 자체에는 요금이 부과되지 않습니다.

작업이 예약되지 않은 경우에도 노드가 실행되는 동안은 풀에 대한 요금이 부과됩니다. 더 이상 풀이 필요하지 않으면 [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete) 명령을 사용하여 삭제합니다. 풀을 삭제하면 노드의 모든 태스크 출력이 삭제됩니다.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹, 배치 계정, 풀 및 관련된 모든 리소스를 제거할 수 있습니다. 다음과 같이 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 배치 계정, Batch 풀 및 Batch 작업을 만들었습니다. 작업에서 샘플 태스크를 실행했고, 노드 중 하나에서 만들어진 출력을 보았습니다. 이제 Batch 서비스의 핵심 개념을 이해 했으므로 더 큰 규모의 더 실제적인 작업 부하로 Batch를 시도할 준비가 되었습니다. Azure Batch에 대한 자세한 내용은 Azure Batch 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Batch 자습서](./tutorial-parallel-dotnet.md)
