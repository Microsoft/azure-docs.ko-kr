---
title: Azure Batch 풀에 대한 Azure 파일 공유 | Microsoft Docs
description: Azure Batch의 Linux 또는 Windows 풀에서 계산 노드의 Azure Files 공유를 탑재하는 방법입니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 914bc11736b08dab6b334307dc188b5d153c7331
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341314"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>배치 풀에서 Azure 파일 공유 사용

[Azure Files](../storage/files/storage-files-introduction.md)는 SMB(서버 메시지 블록) 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. 이 문서에서는 풀 계산 노드에서 Azure 파일 공유를 탑재 및 사용하는 데 필요한 정보 및 코드 예제를 제공합니다. 코드 예제에는 배치 .NET 및 Python SDK가 사용되지만, 다른 배치 SDK와 도구를 사용하여 유사한 작업을 수행할 수 있습니다.

배치는 Azure Storage Blob을 사용하여 데이터를 읽고 쓸 수 있는 기본 API 지원을 제공합니다. 그러나 경우에 따라 풀 계산 노드에서 Azure 파일 공유에 액세스하려 수 있습니다. 예를 들어, SMB 파일 공유를 사용하는 레거시 워크로드가 있거나 작업 시 공유 데이터에 액세스하거나 공유 출력을 생성해야 합니다. 

## <a name="considerations-for-use-with-batch"></a>배치와 함께 사용 시 고려 사항

* 비교적 적은 병렬 작업을 실행하는 풀이 있는 경우 Azure 파일 공유를 사용하는 것이 좋습니다. [성능 및 크기 조정 대상](../storage/files/storage-files-scale-targets.md)을 검토하여 예상 풀 크기 및 자산 파일 수를 고려할 경우 Azure Files(Azure Storage 계정 사용)를 사용해야 하는지 결정합니다. 

* Azure 파일 공유는 [비용 효율적](https://azure.microsoft.com/pricing/details/storage/files/)이며 다른 지역에 대한 데이터 복제를 사용하여 구성할 수 있으므로 전역으로 중복됩니다. 

* 온-프레미스 컴퓨터에서 Azure 파일 공유를 동시에 탑재할 수 있습니다.

* Azure 파일 공유에 대한 일반적인 [플랜 고려 사항](../storage/files/storage-files-planning.md)도 참조하세요.


## <a name="create-a-file-share"></a>파일 공유 만들기

배치 계정에 연결된 저장소 계정 또는 별도의 저장소 계정으로 [파일 공유를 만듭니다](../storage/files/storage-how-to-create-file-share.md).

## <a name="mount-a-share-on-a-windows-pool"></a>Windows 풀에서 공유 탑재

이 섹션에서는 Windows 노드 풀에서 Azure 파일 공유를 탑재 및 사용하기 위한 단계와 코드 예제를 제공합니다. 추가적인 배경은 Windows에서 Azure 파일 공유를 탑재하는 방법에 대한 [설명서](../storage/files/storage-how-to-use-files-windows.md)를 참조하세요. 

배치에서는 Windows 노드에서 작업을 실행할 때마다 공유를 탑재해야 합니다. 현재는 Windows 노드의 작업 간에 네트워크 연결을 유지할 수 없습니다.

예를 들어, 각 작업 명령줄의 일부로 파일 공유를 탑재하려면 `net use` 명령을 포함합니다. 파일 공유를 탑재하려면 다음 자격 증명이 필요합니다.

* **사용자 이름**: AZURE\\\<storageaccountname\>, 예: AZURE\\*mystorageaccountname*
* **암호**: <StorageAccountKeyWhichEnds in==>, 예: *XXXXXXXXXXXXXXXXXXXXX==*

다음 명령은 스토리지 계정 *mystorageaccountname*에서 파일 공유 *myfileshare*를 *S:* 드라이브로 탑재합니다.

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

간단히 말해서, 여기에 있는 예제에서는 자격 증명을 직접 텍스트로 전달합니다. 실제로 환경 변수, 인증서 또는 솔루션(예: Azure Key Vault)을 사용하여 자격 증명을 관리하는 것이 좋습니다.

탑재 작업을 간소화하려면 선택적으로 노드에서 자격 증명을 유지합니다. 그런 다음, 자격 증명 없이 공유를 탑재할 수 있습니다. 다음 두 단계를 수행합니다.

1. 풀 구성에서 시작 작업을 사용하여 `cmdkey` 명령줄 유틸리티를 실행합니다. 이렇게 하면 각 Windows 노드에서 자격 증명이 유지됩니다. 시작 작업 명령줄은 다음과 유사합니다.

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. `net use`를 사용하여 각 작업의 일부로 각 노드에 공유를 탑재합니다. 예를 들어, 다음 작업 명령줄은 파일 공유를 *S:* 드라이브로 탑재합니다. 이다음에 공유를 참조하는 명령 또는 스크립트가 옵니다. 캐시된 자격 증명은 `net use` 호출에서 사용됩니다. 이 단계에서는 풀의 시작 작업에서 사용한 작업에 대해 동일한 사용자 ID를 사용하고 있다고 가정합니다. 이 가정은 일부 시나리오에 적합하지 않습니다.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# 예제
다음 C# 예제는 시작 작업을 사용하여 Windows 풀에서 자격 증명을 유지하는 방법을 보여 줍니다. 저장소 파일 서비스 이름 및 저장소 자격 증명은 정의된 상수로 전달됩니다. 여기서 시작 작업은 풀 범위가 있는 표준(비관리자) 자동 사용자 계정으로 실행됩니다.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

자격 증명을 저장한 후 작업 명령줄을 사용하여 공유를 탑재하고 읽기 또는 쓰기 작업에서 공유를 참조합니다. 기본적인 예제로, 다음 코드 조각의 작업 명령줄은 `dir` 명령을 사용하여 파일 공유의 파일을 나열합니다. 풀에서 시작 작업을 실행하는 데 사용한 동일한 [사용자 ID](batch-user-accounts.md)를 사용하여 각 작업을 실행해야 합니다. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Linux 풀에서 공유 탑재

Azure 파일 공유는 [CIFS 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 다음 예제는 Ubuntu 16.04 LTS 계산 노드의 풀에서 파일 공유를 탑재하는 방법을 보여 줍니다. 다른 Linux 배포를 사용하는 경우, 일반적인 단계는 유사하지만 배포에 적합한 패키지 관리자를 사용합니다. 자세한 내용과 추가적인 예제는 [Linux에서 Azure Files 사용](../storage/files/storage-how-to-use-files-linux.md)을 참조하세요.

먼저 관리자 ID로 `cifs-utils` 패키지를 설치하고 로컬 파일 시스템에서 탑재 지점(예: */mnt/MyAzureFileShare*)을 만듭니다. 탑재 지점의 폴더는 파일 시스템의 어디에나 만들 수 있지만, 일반적으로 `/mnt` 폴더 아래에 만듭니다. `/mnt`(Ubuntu) 또는 `/mnt/resource`(다른 배포)에 직접 탑재 지점을 만들지는 마세요.

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

그런 다음, `mount` 명령을 실행하여 파일 공유를 탑재하여 다음 자격 증명을 제공합니다.

* **사용자 이름**: \<storageaccountname\>, 예: *mystorageaccountname*
* **암호**: <StorageAccountKeyWhichEnds in==>, 예: *XXXXXXXXXXXXXXXXXXXXX==*

다음 명령은 스토리지 계정 *mystorageaccountname*에서 파일 공유 *myfileshare*를 */mnt/MyAzureFileShare*에 탑재합니다. 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

간단히 말해서, 여기에 있는 예제에서는 자격 증명을 직접 텍스트로 전달합니다. 실제로 환경 변수, 인증서 또는 솔루션(예: Azure Key Vault)을 사용하여 자격 증명을 관리하는 것이 좋습니다.

Linux 풀에서는 이러한 모든 단계를 단일 시작 작업으로 결합하거나 스크립트로 실행할 수 있습니다. 풀에서 관리자로 시작 작업을 실행합니다. 공유를 참조하는 풀에서 추가 작업을 실행하기 전에 성공적으로 완료될 때까지 대기하도록 시작 작업을 설정합니다.

### <a name="python-example"></a>Python 예제

다음 Python 예제는 시작 작업에서 공유를 탑재하도록 Ubuntu 풀을 구성하는 방법을 보여 줍니다. 탑재 지점, 파일 공유 엔드포인트 및 저장소 자격 증명은 정의된 상수로 전달됩니다. 시작 작업은 풀 범위가 있는 관리자 자동 사용자 계정으로 실행됩니다.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

공유를 탑재하고 작업을 정의한 후 작업 명령줄에서 공유를 사용합니다. 예를 들어, 다음 기본 명령은 `ls`를 사용하여 파일 공유의 파일을 나열합니다.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>다음 단계

* 배치에서 데이터를 읽고 쓰는 기타 옵션은 [배치 기능 개요](batch-api-basics.md) 및 [작업 및 작업 출력 유지](batch-task-output.md)를 참조하세요.

* 배치 컨테이너 워크로드에 대한 파일 시스템을 배포하는 데 필요한 [Shipyard 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes)를 포함하는 [배치 Shipyard](https://github.com/Azure/batch-shipyard) 도구 키트도 참조하세요.