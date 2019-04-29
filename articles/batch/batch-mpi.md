---
title: 다중 인스턴스 작업을 사용하여 MPI 애플리케이션 실행 - Azure Batch | Microsoft Docs
description: Azure Batch에서 다중 인스턴스 작업 유형을 사용하여 MPI(메시지 전달 인터페이스) 애플리케이션을 실행하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 83e34bd7-a027-4b1b-8314-759384719327
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.date: 03/13/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fe75dabe098cf98f0c3c04d592a32d6a44cebf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775329"
---
# <a name="use-multi-instance-tasks-to-run-message-passing-interface-mpi-applications-in-batch"></a>다중 인스턴스 작업을 사용하여 Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행

다중 인스턴스 작업을 통해 여러 계산 노드에서 동시에 Azure Batch 작업을 실행할 수 있습니다. 이러한 작업을 통해 MPI(메시지 전달 인터페이스) 애플리케이션과 같은 고성능 컴퓨팅 시나리오를 Batch로 수행할 수 있습니다. 이 문서에서 [Batch .NET][api_net] 라이브러리를 사용하여 다중 인스턴스 작업을 실행하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서의 예제에서는 Batch .NET, MS-MPI 및 Windows 계산 노드에 집중하는 반면 여기에서 설명한 다중 인스턴스 작업 개념은 다른 플랫폼 및 기술(예를 들어 Linux 노드의 Python 및 Intel MPI)에 적용됩니다.
>
>

## <a name="multi-instance-task-overview"></a>다중 인스턴스 작업 개요
Batch에서 각 태스크는 일반적으로 단일 계산 노드에서 실행됩니다. 작업에 여러 태스크를 제출하고 Batch 서비스는 노드에서 실행을 위해 각 태스크를 예약합니다. 그러나 태스크의 **다중 인스턴스 설정**을 구성하여 Batch에 하나의 기본 태스크를 만들고 대신 여러 노드에서 여러 하위 태스크를 실행하도록 지시합니다.

![다중 인스턴스 작업 개요][1]

작업에 다중 인스턴스 설정을 사용하여 태스크를 제출할 때 Batch는 다중 인스턴스 태스크에 고유한 몇 가지 단계를 수행합니다.

1. Batch 서비스는 다중 인스턴스 설정에 따라 하나의 **기본** 태스크 및 여러 개의 **하위 태스크**를 만듭니다. 작업의 총 수(주 및 모든 하위 작업)는 다중 인스턴스 설정에서 지정하는 **인스턴스**(계산 노드)의 총 수와 일치합니다.
2. 배치는 계산 노드 중 하나를 **마스터**로 지정하고 주 작업이 마스터에서 실행되도록 예약합니다. 하위 작업이 다중 인스턴스 작업, 노드당 하나의 하위 작업에 할당된 계산 노드의 나머지 부분에서 실행되도록 예약합니다.
3. 주 및 하위 작업은 다중 인스턴스 설정에서 지정하는 모든 **공용 리소스 파일**을 다운로드합니다.
4. 공용 리소스 파일을 다운로드한 후 주 및 하위 작업에서 다중 인스턴스 설정에 지정하는 **조정 명령**을 실행합니다. 조정 명령은 작업을 실행하기 위한 노드를 준비하는 데 일반적으로 사용됩니다. 이는 백그라운드 서비스 시작(예: [Microsoft MPI][msmpi_msdn]의 `smpd.exe`) 및 노드가 노드 간 메시지를 처리할 준비가 되었음을 확인하는 것을 포함할 수 있습니다.
5. 주 및 모든 하위 작업에서 조정 명령이 성공적으로 완료된 *후* 주 작업은 마스터 노드에서 **애플리케이션 명령**을 실행합니다. 애플리케이션 명령은 다중 인스턴스 작업 자체의 명령줄이며 주 작업에서만 실행됩니다. [MS-MPI][msmpi_msdn] 기반 솔루션에서 `mpiexec.exe`를 사용하여 MPI 사용 애플리케이션을 실행하는 위치입니다.

> [!NOTE]
> 기능적으로 고유하지만 "다중 인스턴스 작업"은 [StartTask][net_starttask] 또는 [JobPreparationTask][net_jobprep]와 같은 고유 작업 유형이 아닙니다. 다중 인스턴스 작업은 단순히 다중 인스턴스 설정이 구성된 표준 Batch 작업(Batch .NET에서 [CloudTask][net_task])입니다. 이 문서에서는 이를 **다중 인스턴스 작업**이라고 합니다.
>
>

## <a name="requirements-for-multi-instance-tasks"></a>다중 인스턴스 작업에 대한 요구 사항
다중 인스턴스 작업은 **노드 간 통신이 활성화**되고 **동시 작업 실행이 비활성화**된 풀이 필요합니다. 동시 작업 실행을 사용하지 않도록 설정하려면 [CloudPool.MaxTasksPerComputeNode](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool) 속성을 1로 설정합니다.

> [!NOTE]
> 일괄 처리는 노드 간 통신을 사용하도록 설정한 풀의 크기를 [제한](batch-quota-limit.md#pool-size-limits)합니다.


이 코드 조각에서는 일괄 처리 .NET 라이브러리를 사용하여 다중 인스턴스 작업용 풀을 만드는 방법을 보여 줍니다.

```csharp
CloudPool myCloudPool =
    myBatchClient.PoolOperations.CreatePool(
        poolId: "MultiInstanceSamplePool",
        targetDedicatedComputeNodes: 3
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Multi-instance tasks require inter-node communication, and those nodes
// must run only one task at a time.
myCloudPool.InterComputeNodeCommunicationEnabled = true;
myCloudPool.MaxTasksPerComputeNode = 1;
```

> [!NOTE]
> 노드 간 통신이 비활성화되었거나 *maxTasksPerNode* 값이 1보다 큰 풀에서 다중 인스턴스 작업을 실행하려는 경우 작업은 예약되지 않습니다. 무기한으로 "활성" 상태로 유지됩니다. 


### <a name="use-a-starttask-to-install-mpi"></a>StartTask를 사용하여 MPI 설치
다중 인스턴스 작업으로 MPI 애플리케이션을 실행하려면 먼저 풀의 계산 노드에 MPI 구현(예: MS-MPI 또는 Intel MPI)을 설치해야 합니다. 노드가 풀에 연결되거나 다시 시작될 때마다 실행하는 [StartTask][net_starttask]를 사용하는 것이 좋습니다. 이 코드 조각은 MS-MPI 설치 패키지를 [리소스 파일][net_resourcefile]로 지정하는 StartTask를 만듭니다. 리소스 파일이 노드로 다운로드된 후에 시작 태스크의 명령줄이 실행됩니다. 이 경우 명령줄은 MS-MPI의 무인 설치를 수행합니다.

```csharp
// Create a StartTask for the pool which we use for installing MS-MPI on
// the nodes as they join the pool (or when they are restarted).
StartTask startTask = new StartTask
{
    CommandLine = "cmd /c MSMpiSetup.exe -unattend -force",
    ResourceFiles = new List<ResourceFile> { new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MSMpiSetup.exe", "MSMpiSetup.exe") },
    UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin)),
    WaitForSuccess = true
};
myCloudPool.StartTask = startTask;

// Commit the fully configured pool to the Batch service to actually create
// the pool and its compute nodes.
await myCloudPool.CommitAsync();
```

### <a name="remote-direct-memory-access-rdma"></a>RDMA(원격 직접 메모리 액세스)
Batch 풀에서 계산 노드에 대해 A9 등, [RDMA 지원 크기](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하는 경우 MPI 애플리케이션은 Azure의 고성능, 지연율이 낮은 RDMA(원격 직접 메모리 액세스) 네트워크를 활용할 수 있습니다.

다음 문서에서 "RDMA 지원"으로 지정된 크기를 찾습니다.

* **CloudServiceConfiguration** 풀

  * [Cloud Services 크기](../cloud-services/cloud-services-sizes-specs.md)(Windows만 해당)
* **VirtualMachineConfiguration** 풀

  * [Azure에서 가상 머신 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(Linux)
  * [Azure에서 가상 머신 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Windows)

> [!NOTE]
> [Linux 계산 노드](batch-linux-nodes.md)에서 RDMA를 활용하려면 노드에서 **Intel MPI**를 사용해야 합니다. 
>

## <a name="create-a-multi-instance-task-with-batch-net"></a>Batch .NET을 사용하여 다중 인스턴스 작업 만들기
이제 풀 요구 사항 및 MPI 패키지 설치를 다루었으며 다중 인스턴스 작업을 만들어 보겠습니다. 이 코드 조각에서 표준 [CloudTask][net_task]를 만든 다음 해당 [MultiInstanceSettings][net_multiinstance_prop] 속성을 구성합니다. 이전에 설명했듯이 다중 인스턴스 작업은 고유한 작업 유형이 아니지만 다중 인스턴스 설정으로 구성된 표준 Batch 작업입니다.

```csharp
// Create the multi-instance task. Its command line is the "application command"
// and will be executed *only* by the primary, and only after the primary and
// subtasks execute the CoordinationCommandLine.
CloudTask myMultiInstanceTask = new CloudTask(id: "mymultiinstancetask",
    commandline: "cmd /c mpiexec.exe -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe");

// Configure the task's MultiInstanceSettings. The CoordinationCommandLine will be executed by
// the primary and all subtasks.
myMultiInstanceTask.MultiInstanceSettings =
    new MultiInstanceSettings(numberOfNodes) {
    CoordinationCommandLine = @"cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d",
    CommonResourceFiles = new List<ResourceFile> {
    new ResourceFile("https://mystorageaccount.blob.core.windows.net/mycontainer/MyMPIApplication.exe",
                     "MyMPIApplication.exe")
    }
};

// Submit the task to the job. Batch will take care of splitting it into subtasks and
// scheduling them for execution on the nodes.
await myBatchClient.JobOperations.AddTaskAsync("mybatchjob", myMultiInstanceTask);
```

## <a name="primary-task-and-subtasks"></a>주 작업 및 하위 작업
작업에 대한 다중 인스턴스 설정을 만드는 경우 작업을 실행하는 계산 노드 수를 지정합니다. 작업에 태스크를 제출하는 경우 Batch 서비스는 지정한 노드 수와 일치하는 하나의 **주** 작업과 충분한 **하위 작업**을 만듭니다.

이러한 작업은 0에서 *numberOfInstances* - 1의 범위로 정수 ID가 할당됩니다. ID가 0인 작업은 주 작업이며 모든 다른 ID는 하위 작업입니다. 예를 들어 작업에 대한 다음과 같은 다중 인스턴스 설정을 만드는 경우 주 작업은 0의 ID를 가지며 하위 작업은 1~9의 ID를 가집니다.

```csharp
int numberOfNodes = 10;
myMultiInstanceTask.MultiInstanceSettings = new MultiInstanceSettings(numberOfNodes);
```

### <a name="master-node"></a>마스터 노드
다중 인스턴스 작업을 제출할 때 Batch 서비스는 계산 노드 중 하나를 "마스터" 노드로 지정하고 주 작업이 마스터 노드에서 실행되도록 예약합니다. 하위 작업은 다중 인스턴스 작업에 할당된 노드의 나머지 부분에서 실행되도록 예약됩니다.

## <a name="coordination-command"></a>조정 명령
**조정 명령**은 주 및 하위 작업에서 실행됩니다.

조정 명령의 호출을 차단합니다. Batch는 조정 명령이 모든 하위 작업에 대해 성공적으로 반환될 때까지 애플리케이션 명령을 실행하지 않습니다. 따라서 조정 명령은 모든 필요한 백그라운드 서비스를 시작하고 사용할 준비가 되었는지 확인한 다음 종료해야 합니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 이 조정 명령은 노드에서 SMPD 서비스를 시작한 다음 종료합니다.

```
cmd /c start cmd /c ""%MSMPI_BIN%\smpd.exe"" -d
```

이 조정 명령에서 `start`를 사용합니다. `smpd.exe` 애플리케이션은 실행 후 즉시 반환하지 않으므로 필요합니다. [start][cmd_start] 명령을 사용하지 않고 이 조정 명령에서 반환하지 않으므로 애플리케이션 명령의 실행이 차단됩니다.

## <a name="application-command"></a>애플리케이션 명령
주 작업 및 모든 하위 작업이 조정 명령 실행을 마치면 다중 인스턴스 작업의 명령줄이 주 작업에서*만* 실행됩니다. 조정 명령과 구분하도록 **애플리케이션 명령**이라고 합니다.

MS-MPI 애플리케이션의 경우 `mpiexec.exe`로 MPI 사용 애플리케이션을 실행하는 데 애플리케이션 명령을 사용합니다. 예를 들어 MS-MPI 버전 7을 사용하는 솔루션에 대한 애플리케이션 명령은 다음과 같습니다.

```
cmd /c ""%MSMPI_BIN%\mpiexec.exe"" -c 1 -wdir %AZ_BATCH_TASK_SHARED_DIR% MyMPIApplication.exe
```

> [!NOTE]
> 기본적으로 MS-MPI의 `mpiexec.exe`는 `CCP_NODES` 변수를 사용하므로([환경 변수](#environment-variables) 참조) 위의 애플리케이션 명령줄 예에서는 이를 제외합니다.
>
>

## <a name="environment-variables"></a>환경 변수
배치는 다중 인스턴스 작업에 할당된 계산 노드의 다중 인스턴스 작업에 관련된 여러 [환경 변수][msdn_env_var]를 만듭니다. 조정 및 애플리케이션 명령줄은 실행하는 스크립트 및 프로그램을 참조할 수 있는 것처럼 이러한 환경 변수를 참조할 수 있습니다.

다음 환경 변수는 다중 인스턴스 작업에서 사용하기 위해 Batch 서비스에 의해 생성됩니다.

* `CCP_NODES`
* `AZ_BATCH_NODE_LIST`
* `AZ_BATCH_HOST_LIST`
* `AZ_BATCH_MASTER_NODE`
* `AZ_BATCH_TASK_SHARED_DIR`
* `AZ_BATCH_IS_CURRENT_NODE_MASTER`

해당 내용 및 가시성을 포함한 해당 환경 변수 및 다른 Batch 계산 노드 환경 변수에 대한 자세한 내용은 [Compute 노드 환경 변수][msdn_env_var]를 참조하세요.

> [!TIP]
> Batch Linux MPI 코드 샘플은 이러한 다양한 환경 변수 사용 방법의 예를 포함합니다. [coordination-cmd][coord_cmd_example] Bash 스크립트는 Azure Storage에서 일반적인 애플리케이션 및 입력 파일을 다운로드하고 마스터 노드에서 NFS(네트워크 파일 시스템) 공유를 활성화하고 NFS 클라이언트로 다중 인스턴스 작업에 할당된 다른 노드를 구성합니다.
>
>

## <a name="resource-files"></a>리소스 파일
다중 인스턴스 작업에 대해 고려해야 할 리소스 파일의 두 집합: *모든* 작업(주 및 하위 작업)이 다운로드하는 **공용 리소스 파일** 및 *주 작업에서만* 다운로드하는 다중 인스턴스 작업 자체에 지정된 **리소스 파일**입니다.

작업에 대해 다중 인스턴스 설정에서 하나 이상의 **공용 리소스 파일**을 지정할 수 있습니다. 이러한 공용 리소스 파일은 주 및 모든 하위 작업에 의해 [Azure Storage](../storage/common/storage-introduction.md)에서 각 노드의 **작업 공유 디렉터리**로 다운로드됩니다. `AZ_BATCH_TASK_SHARED_DIR` 환경 변수를 사용하여 애플리케이션 및 조정 명령줄에서 작업 공유 디렉터리에 액세스할 수 있습니다. `AZ_BATCH_TASK_SHARED_DIR` 경로는 다중 인스턴스 작업에 할당된 모든 노드에서 동일하므로 주 데이터베이스와 모든 하위 작업 간의 단일 조정 명령을 공유할 수 있습니다. 배치는 원격 액세스 측면에서 디렉터리를 "공유"하지 않지만 환경 변수에 대한 팁에서 이전에 설명한 것처럼 탑재 또는 공유 지점으로 사용할 수 있습니다.

다중 인스턴스 작업 자체에 대해 지정한 리소스 파일은 작업의 작업 디렉터리 `AZ_BATCH_TASK_WORKING_DIR`에 기본적으로 다운로드됩니다. 언급한 대로 공용 리소스 파일과 달리 주 작업만이 다중 인스턴스 작업 자체에 대해 지정된 리소스 파일을 다운로드합니다.

> [!IMPORTANT]
> 명령줄에서 이러한 디렉터리를 나타내는 환경 변수 `AZ_BATCH_TASK_SHARED_DIR` 및 `AZ_BATCH_TASK_WORKING_DIR`을 항상 사용합니다. 경로를 수동으로 구성하지 마세요.
>
>

## <a name="task-lifetime"></a>작업 수명
주 작업의 수명은 전체 다중 인스턴스 작업의 수명을 제어합니다. 주 작업이 종료될 때 모든 하위 작업이 종료됩니다. 주 작업의 종료 코드는 작업의 종료 코드이며 따라서 재시도 목적에 대한 작업의 성공 여부를 결정하는 데 사용됩니다.

하위 작업 중 하나라도 실패하는 경우(예: 0이 아닌 반환 코드와 함께 종료) 전체 다중 인스턴스 작업이 실패합니다. 다중 인스턴스 작업은 종료되고 해당 재시도 한계까지 재시도됩니다.

다중 인스턴스 작업을 삭제하는 경우 주 및 모든 하위 작업도 Batch 서비스에서 삭제됩니다. 모든 하위 작업 디렉터리 및 해당 파일은 표준 작업의 경우처럼 계산 노드에서 삭제됩니다.

[MaxTaskRetryCount][net_taskconstraint_maxretry], [MaxWallClockTime][net_taskconstraint_maxwallclock] 및 [RetentionTime][net_taskconstraint_retention] 속성과 같은 다중 인스턴스 작업에 대한 [TaskConstraints][net_taskconstraints]는 표준 작업에 대한 것이므로 적용되고 주 및 모든 하위 작업에 적용됩니다. 그러나 다중 인스턴스 태스크를 작업에 추가한 후 [RetentionTime][net_taskconstraint_retention] 속성을 변경하는 경우 이 변경은 주 작업에만 적용됩니다. 모든 하위 작업은 원래 [RetentionTime][net_taskconstraint_retention]을 계속해서 사용합니다.

계산 노드의 최근 작업 목록은 최근 작업이 다중 인스턴스 작업의 일부일 경우 하위 작업의 ID를 반영합니다.

## <a name="obtain-information-about-subtasks"></a>하위 작업에 대한 정보 가져오기
Batch .NET 라이브러리를 사용하여 하위 작업에 대한 정보를 가져오려면 [CloudTask.ListSubtasks][net_task_listsubtasks] 메서드를 호출합니다. 이 메서드는 모든 하위 작업에 대한 정보 및 작업을 실행하는 계산 노드에 대한 정보를 반환합니다. 이 정보로부터 각 하위 작업의 루트 디렉터리, 풀 ID, 현재 상태, 종료 코드 등을 확인할 수 있습니다. [PoolOperations.GetNodeFile][poolops_getnodefile] 메서드와 함께 이 정보를 사용하여 하위 작업의 파일을 가져올 수 있습니다. 이 메서드는 주 작업(ID 0)에 대한 정보를 반환하지 않습니다.

> [!NOTE]
> 별도로 언급하지 않는 한 다중 인스턴스 [CloudTask][net_task] 자체에서 작동하는 Batch .NET 메서드는 주 작업에*만* 적용됩니다. 예를 들어 다중 인스턴스 작업에서 [CloudTask.ListNodeFiles][net_task_listnodefiles] 메서드를 호출하는 경우 주 작업의 파일만 반환됩니다.
>
>

다음 코드 조각은 하위 작업 정보를 얻는 방법 뿐만 아니라 실행되는 노드에서 파일 콘텐츠를 요청하는 방법을 보여 줍니다.

```csharp
// Obtain the job and the multi-instance task from the Batch service
CloudJob boundJob = batchClient.JobOperations.GetJob("mybatchjob");
CloudTask myMultiInstanceTask = boundJob.GetTask("mymultiinstancetask");

// Now obtain the list of subtasks for the task
IPagedEnumerable<SubtaskInformation> subtasks = myMultiInstanceTask.ListSubtasks();

// Asynchronously iterate over the subtasks and print their stdout and stderr
// output if the subtask has completed
await subtasks.ForEachAsync(async (subtask) =>
{
    Console.WriteLine("subtask: {0}", subtask.Id);
    Console.WriteLine("exit code: {0}", subtask.ExitCode);

    if (subtask.State == SubtaskState.Completed)
    {
        ComputeNode node =
            await batchClient.PoolOperations.GetComputeNodeAsync(subtask.ComputeNodeInformation.PoolId,
                                                                 subtask.ComputeNodeInformation.ComputeNodeId);

        NodeFile stdOutFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardOutFileName);
        NodeFile stdErrFile = await node.GetNodeFileAsync(subtask.ComputeNodeInformation.TaskRootDirectory + "\\" + Constants.StandardErrorFileName);
        stdOut = await stdOutFile.ReadAsStringAsync();
        stdErr = await stdErrFile.ReadAsStringAsync();

        Console.WriteLine("node: {0}:", node.Id);
        Console.WriteLine("stdout.txt: {0}", stdOut);
        Console.WriteLine("stderr.txt: {0}", stdErr);
    }
    else
    {
        Console.WriteLine("\tSubtask {0} is in state {1}", subtask.Id, subtask.State);
    }
});
```

## <a name="code-sample"></a>코드 샘플
GitHub의 [MultiInstanceTasks][github_mpi] 코드 샘플에서는 다중 인스턴스 태스크를 사용하여 Batch 계산 노드에서 [MS-MPI][msmpi_msdn] 애플리케이션을 실행하는 방법을 보여 줍니다. [준비](#preparation) 및 [실행](#execution)의 단계에 따라 샘플을 실행합니다.

### <a name="preparation"></a>준비
1. [How to compile and run a simple MS-MPI program][msmpi_howto](간단한 MS-MPI 프로그램을 컴파일하고 실행하는 방법)의 처음 두 단계를 수행합니다. 이렇게 하면 다음 단계의 필수 조건이 충족됩니다.
2. [MPIHelloWorld][helloworld_proj] 샘플 MPI 프로그램의 *릴리스* 버전을 빌드합니다. 이는 다중 인스턴스 태스크를 통해 계산 노드에서 실행할 프로그램입니다.
3. `MPIHelloWorld.exe`(2단계에서 빌드) 및 `MSMpiSetup.exe`(1단계에서 다운로드)를 포함하는 Zip 파일을 만듭니다. 다음 단계의 애플리케이션 패키지로 이 Zip 파일을 업로드합니다.
4. [Azure Portal][portal]을 사용하여 "MPIHelloWorld"라는 Batch [애플리케이션](batch-application-packages.md)을 만들고, 이전 단계에서 만든 Zip 파일을 버전 "1.0"의 애플리케이션 패키지로 지정합니다. 자세한 내용은 [애플리케이션 업로드 및 관리](batch-application-packages.md#upload-and-manage-applications)를 참조하세요.

> [!TIP]
> 애플리케이션 패키지에 추가 종속성(예: `msvcp140d.dll` 또는 `vcruntime140d.dll`)을 포함할 필요가 없도록 *릴리스* 버전의 `MPIHelloWorld.exe`을 빌드합니다.
>
>

### <a name="execution"></a>실행
1. GitHub에서 [azure-batch-samples][github_samples_zip]를 다운로드합니다.
2. Visual Studio 2017에서 MultiInstanceTasks **솔루션**을 엽니다. `MultiInstanceTasks.sln` 솔루션 파일은 다음 위치에 있습니다.

    `azure-batch-samples\CSharp\ArticleProjects\MultiInstanceTasks\`
3. **Microsoft.Azure.Batch.Samples.Common** 프로젝트의 `AccountSettings.settings`에 Batch 계정 및 Storage 계정의 자격 증명을 입력합니다.
4. MultiInstanceTasks 솔루션을 **빌드 및 실행**하여 Batch 풀의 계산 노드에서 MPI 샘플 애플리케이션을 실행합니다.
5. *선택 사항*: 리소스를 삭제하기 전에 [Azure Portal][portal] 또는 [Batch Explorer][batch_labs]를 사용하여 샘플 풀, 작업 및 태스크("MultiInstanceSamplePool", "MultiInstanceSampleJob", "MultiInstanceSampleTask")를 검사합니다.

> [!TIP]
> Visual Studio가 없는 경우 [Visual Studio 커뮤니티][visual_studio]를 무료로 다운로드할 수 있습니다.
>
>

`MultiInstanceTasks.exe`는 다음과 유사하게 출력됩니다.

```
Creating pool [MultiInstanceSamplePool]...
Creating job [MultiInstanceSampleJob]...
Adding task [MultiInstanceSampleTask] to job [MultiInstanceSampleJob]...
Awaiting task completion, timeout in 00:30:00...

Main task [MultiInstanceSampleTask] is in state [Completed] and ran on compute node [tvm-1219235766_1-20161017t162002z]:
---- stdout.txt ----
Rank 2 received string "Hello world" from Rank 0
Rank 1 received string "Hello world" from Rank 0

---- stderr.txt ----

Main task completed, waiting 00:00:10 for subtasks to complete...

---- Subtask information ----
subtask: 1
        exit code: 0
        node: tvm-1219235766_3-20161017t162002z
        stdout.txt:
        stderr.txt:
subtask: 2
        exit code: 0
        node: tvm-1219235766_2-20161017t162002z
        stdout.txt:
        stderr.txt:

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>다음 단계
* Microsoft HPC 및 Azure Batch 팀 블로그는 [Azure Batch의 Linux에 대한 MPI 지원][blog_mpi_linux]을 설명하고 Batch와 함께 [OpenFOAM][openfoam] 사용에 대한 정보를 포함합니다. [GitHub에서 OpenFOAM 예제][github_mpi]에 대한 Python 코드 샘플을 찾을 수 있습니다.
* Azure Batch MPI 솔루션에서 사용하기 위해 [Linux 계산 노드 풀을 만드는 방법](batch-linux-nodes.md)을 알아봅니다.

[helloworld_proj]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks/MPIHelloWorld

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[blog_mpi_linux]: https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/
[cmd_start]: https://technet.microsoft.com/library/cc770297.aspx
[coord_cmd_example]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/mpi/data/linux/openfoam/coordination-cmd
[github_mpi]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/MultiInstanceTasks
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[msdn_env_var]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[msmpi_msdn]: https://msdn.microsoft.com/library/bb524831.aspx
[msmpi_sdk]: https://go.microsoft.com/FWLink/p/?LinkID=389556
[msmpi_howto]: https://blogs.technet.com/b/windowshpc/archive/2015/02/02/how-to-compile-and-run-a-simple-ms-mpi-program.aspx
[openfoam]: http://www.openfoam.com/
[visual_studio]: https://www.visualstudio.com/vs/community/

[net_jobprep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_multiinstance_class]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_multiinstance_prop]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.multiinstancesettings.aspx
[net_multiinsance_commonresfiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.commonresourcefiles.aspx
[net_multiinstance_coordcmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.coordinationcommandline.aspx
[net_multiinstance_numinstances]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.numberofinstances.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_cmdline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.commandline.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_taskconstraints]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.aspx
[net_taskconstraint_maxretry]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxtaskretrycount.aspx
[net_taskconstraint_maxwallclock]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.maxwallclocktime.aspx
[net_taskconstraint_retention]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskconstraints.retentiontime.aspx
[net_task_listsubtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listsubtasks.aspx
[net_task_listnodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[poolops_getnodefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getnodefile.aspx

[portal]: https://portal.azure.com
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx

[1]: ./media/batch-mpi/batch_mpi_01.png "다중 인스턴스 개요"
