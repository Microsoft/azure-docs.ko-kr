---
title: 작업 의존 관계를 사용하여 다른 작업의 완료에 따라 작업 실행 - Azure Batch | Microsoft Docs
description: Azure Batch에서 MapReduce 스타일과 비슷한 빅 데이터 워크로드를 처리하기 위해 다른 작업의 완료에 종속된 작업을 만듭니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca6918b809a9b4ede3fffb151c7fa5183ae03b47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550380"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>작업 의존 관계를 만들어 다른 작업에 종속된 작업 실행

상위 태스크가 완료된 후에 (일련의) 태스크를 실행하는 태스크 종속성을 정의할 수 있습니다. 태스크 종속성이 유용한 몇 가지 시나리오는 다음과 같습니다.

* 클라우드에서 MapReduce 스타일의 컴퓨팅 워크로드
* DAG(방향성 비순환 그래프)으로 표현할 수 있는 태스크의 데이터 처리 작업
* 프로세스를 미리 렌더링하거나 사후 렌더링하는 경우 다음 태스크를 시작하기 전에 각 태스크를 완료해야 합니다.
* 다운스트림 태스크가 업스트림 태스크의 출력에 따라 달라지는 다른 작업

Batch 태스크 종속성을 통해 하나 이상의 상위 태스크를 완료한 후에 계산 노드에서 실행하기 위해 예약된 태스크를 만들 수 있습니다. 예를 들어, 별도의 병렬 태스크를 포함한 3D 동영상의 각 프레임을 렌더링하는 작업을 만들 수 있습니다. 해당 최종 작업인 "병합 태스크"는 전체 프레임이 성공적으로 렌더링된 경우에만 렌더링된 프레임을 완전한 영화로 함께 병합합니다.

기본적으로 종속 태스크는 상위 태스크가 완료된 후에만 실행하기 위해 예약됩니다. 상위 태스크가 실패한 경우 기본 동작을 재정의하고 태스크를 실행하는 종속성 동작을 지정할 수 있습니다. 자세한 내용은 [종속성 작업](#dependency-actions) 섹션을 참조하세요.  

일대일 또는 일대다 관계에서 다른 태스크에 따라 달라지는 태스크를 만들 수 있으며, 지정된 범위의 태스크 ID 내에서 태스크 그룹이 완료되는 데 따라 태스크가 달라지는 범위 종속성을 만들 수도 있습니다. 다대다 관계를 만들기 위해 다음 세 가지 기본 시나리오를 결합할 수 있습니다.

## <a name="task-dependencies-with-batch-net"></a>Batch .NET을 사용한 태스크 종속성
이 문서에서는 [Batch .NET][net_msdn] 라이브러리를 사용하여 태스크 종속성을 구성하는 방법을 설명합니다. 먼저는 작업에서 [태스크 종속성을 사용](#enable-task-dependencies)하는 방법을 보여 주고 [종속성을 사용하여 태스크를 구성](#create-dependent-tasks)하는 방법을 설명합니다. 또한 상위 태스크가 실패하는 경우 종속 태스크를 실행하는 종속성 작동을 지정하는 방법을 설명합니다. 마지막으로 Batch에서 지원되는 [종속성 시나리오](#dependency-scenarios)를 설명합니다.

## <a name="enable-task-dependencies"></a>태스크 종속성 사용
Batch 애플리케이션에서 태스크 종속성을 사용하려면 먼저 작업이 태스크 종속성을 사용하도록 구성해야 합니다. Batch .NET에서 해당 [UsesTaskDependencies][net_usestaskdependencies] 속성을 `true`으로 설정하여 [CloudJob][net_cloudjob]에서 이를 사용합니다.

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

앞의 코드 조각에서 "batchClient"는 [BatchClient][net_batchclient] 클래스의 인스턴스입니다.

## <a name="create-dependent-tasks"></a>종속성 태스크 만들기
하나 이상의 상위 태스크를 완료하는 데 따라 달라지는 태스크를 만들려면 태스크가 다른 태스크"에 종속"되도록 지정할 수 있습니다. Batch .NET에서 [TaskDependencies][net_taskdependencies] 클래스의 인스턴스를 사용하여 [CloudTask][net_cloudtask].[DependsOn][net_dependson] 속성을 구성합니다.

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

이 코드 조각은 태스크 ID가 "Flowers"인 종속 태스크를 만듭니다. "Flowers" 태스크는 "Rain" 및 "Sun" 태스크에 따라 달라집니다. "Rain" 및 "Sun" 태스크를 성공적으로 완료한 후에 "Flowers" 태스크를 계산 노드에서 실행되도록 예약합니다.

> [!NOTE]
> 기본적으로 태스크가 **완료** 상태이고 해당 **종료 코드**가 `0`인 경우 성공적으로 완료되었다고 간주됩니다. 즉, Batch .NET에서 `Completed`의 [CloudTask][net_cloudtask].[State][net_taskstate] 속성 값 및 CloudTask의 [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] 속성 값은 `0`입니다. 변경 방법에 대해서는 [종속성 작업](#dependency-actions) 섹션을 참조하세요.
> 
> 

## <a name="dependency-scenarios"></a>종속성 시나리오
Azure Batch에서 사용할 수 있는 세 가지 기본 태스크 종속성 시나리오는 일대일, 일대다 및 태스크 ID 범위 종속성입니다. 네 번째 시나리오인 다대다를 제공하도록 결합될 수 있습니다.

| 시나리오&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 예 |  |
|:---:| --- | --- |
|  [일대일](#one-to-one) |*taskB*가 *taskA*에 종속됨 <p/> *taskB*는 *taskA*가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |![다이어그램: 일대일 태스크 종속성][1] |
|  [일대다](#one-to-many) |*taskC*는 *taskA* 및 *taskB*에 종속됨 <p/> *taskC*는 *taskA* 및 *taskB*가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |![다이어그램: 일대다 태스크 종속성][2] |
|  [태스크 ID 범위](#task-id-range) |*taskD*가 태스크의 범위에 종속됨 <p/> *taskD*는 ID *1*-*10*을 가진 태스크가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |![다이어그램: 작업 ID 범위 종속성][3] |

> [!TIP]
> 태스크 C, D, E 및 F는 각각 태스크 A 및 B에 종속되는 경우 **다대다** 관계를 만들 수 있습니다. 예를 들어, 다운스트림 태스크가 여러 업스트림 태스크의 출력에 따라 달라지는 병렬화된 전처리 시나리오에서 유용합니다.
> 
> 이 섹션의 예제에서는 부모 태스크가 성공적으로 완료된 후에 종속 태스크가 실행됩니다. 이 동작은 종속 태스크에 대한 기본 동작입니다. 기본 동작을 재정의하는 종속성 작업을 지정하여 상위 태스크에 실패한 후에 종속 태스크를 실행할 수 있습니다. 자세한 내용은 [종속성 작업](#dependency-actions) 섹션을 참조하세요.

### <a name="one-to-one"></a>일대일
일대일 관계의 경우 태스크는 상위 태스크를 성공적으로 완료했는지에 따라 달라집니다. 종속성을 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 단일 태스크 ID를 [TaskDependencies][net_taskdependencies].[OnId][net_onid] 고정 메서드에 제공합니다.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>일대다
일대다 관계의 경우 태스크는 여러 상위 태스크를 완료했는지에 따라 달라집니다. 종속성을 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 태스크 ID 컬렉션을 [TaskDependencies][net_taskdependencies].[OnId][net_onids] 고정 메서드에 제공합니다.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>태스크 ID 범위
상위 태스크의 범위에 대한 종속성에서 태스크는 범위 내에 있는 ID를 가진 태스크를 완료했는지에 따라 달라집니다.
종속성을 만들려면 [CloudTask][net_cloudtask]의 [DependsOn][net_dependson] 속성을 채우는 경우 범위에서 처음 및 최신 태스크 ID를 [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] 고정 메서드에 제공합니다.

> [!IMPORTANT]
> 종속성에 대해 태스크 ID 범위를 사용할 경우 ID가 정수 값을 나타내는 태스크만 범위에 따라 선택됩니다. 따라서 범위 `1..10`에서 태스크 `3` 및 `7`이 선택되지만 `5flamingoes`는 선택되지 않습니다. 
> 
> 범위 종속성을 평가할 때 앞에 오는 0은 의미가 없으므로 문자열 식별자가 `4`, `04` 및 `004`인 작업은 모두 범위 *내*에 포함되며 `4` 작업으로 처리됩니다. 따라서 처음 완료되는 작업에서 종속성이 충족됩니다.
> 
> **충족**으로 설정된 종속성 작업에 매핑되는 오류를 성공적으로 완료하거나 해결하여 범위에 있는 모든 태스크가 종속성을 충족해야 합니다. 자세한 내용은 [종속성 작업](#dependency-actions) 섹션을 참조하세요.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>종속성 작업

기본적으로 상위 태스크가 성공적으로 완료된 후에만 (일련의) 종속 태스크가 실행됩니다. 일부 시나리오에서는 상위 태스크가 실패하더라도 종속 태스크를 실행하려고 할 수 있습니다. 종속성 작업을 지정하여 기본 동작을 재정의할 수 있습니다. 종속성 작업은 상위 태스크의 성공 또는 실패에 따라 종속 태스크를 실행할 수 있는지 여부를 지정합니다. 

예를 들어, 종속 태스크는 데이터가 업스트림 태스크를 완료하도록 대기 중입니다. 업스트림 태스크가 실패할 경우 종속 태스크는 오래된 데이터를 사용하여 실행될 수 있습니다. 이 경우에 종속성 작업은 상위 태스크가 실패하더라도 종속 태스크를 실행할 수 있는지 여부를 지정할 수 있습니다.

종속성 작업은 상위 태스크에 대한 종료 조건을 기반으로 합니다. 다음과 같은 종료 조건에 대한 종속성 작업을 지정할 수 있습니다. .NET에 대한 세부 정보는 [ExitConditions][net_exitconditions]를 참조하세요.

- 전처리 오류가 발생할 때
- 파일을 업로드 오류가 발생할 때 태스크가 **exitCodes** 또는 **exitCodeRanges**를 통해 지정된 종료 코드로 인해 종료된 다음 파일 업로드 오류가 발생하면 해당 종료 코드로 지정된 작업이 우선적으로 적용됩니다.
- 태스크가 **ExitCodes** 속성으로 정의된 종료 코드로 인해 종료되는 경우
- 태스크가 **ExitCodeRanges** 속성에서 지정한 범위 내에서 실패한 종료 코드로 인해 종료되는 경우
- 기본적인 경우(태스크가 **ExitCodes** 또는 **ExitCodeRanges**로 정의되지 않은 종료 코드로 인해 종료되거나 태스크가 전처리 오류와 함께 종료되고 **PreProcessingError** 속성이 설정되지 않았거나, 태스크가 파일 업로드 오류로 인해 실패하고 **FileUploadError** 속성이 설정되지 않은 경우) 

.NET의 종속성 작업을 지정하려면 종료 조건에 대한 [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] 속성을 설정합니다. **DependencyAction** 속성은 다음 두 값 중 하나를 사용합니다.

- **DependencyAction** 속성을 **충족**으로 설정하면 지정된 오류로 인해 상위 태스크를 종료하는 경우 종속 태스크를 실행할 수 있음을 나타냅니다.
- **DependencyAction** 속성을 **차단**으로 설정하면 종속 태스크를 실행할 수 없음을 나타냅니다.

**DependencyAction** 속성에 대한 기본 설정은 종료 코드 0의 경우 **충족**이고 다른 모든 종료 조건의 경우 **차단**입니다.

다음 코드 조각에서는 상위 태스크에 대한 **DependencyAction** 속성을 설정합니다. 전처리 오류 또는 지정된 오류 코드로 인해 상위 태스크가 종료되면 종속 태스크가 차단됩니다. 상위 태스크가 다른 0이 아닌 오류로 인해 종료되면 종속 태스크를 실행할 수 있습니다.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>코드 샘플
[TaskDependencies][github_taskdependencies] 샘플 프로젝트는 GitHub의 [Azure Batch 코드 샘플][github_samples] 중 하나입니다. 이 Visual Studio 솔루션은 다음 사항을 보여 줍니다.

- 작업에 대한 태스크 종속성을 사용하는 방법
- 다른 태스크에 종속된 태스크를 만드는 방법
- 계산 노드의 풀에서 해당 태스크를 실행하는 방법.

## <a name="next-steps"></a>다음 단계
### <a name="application-deployment"></a>애플리케이션 배포
Batch의 [애플리케이션 패키지](batch-application-packages.md) 기능은 계산 노드에서 태스크를 실행하는 애플리케이션을 배포하고 버전을 관리하는 쉬운 방법을 제공합니다.

### <a name="installing-applications-and-staging-data"></a>애플리케이션 설치 및 데이터 준비
태스크를 실행하기 위해 노드를 준비하는 방법의 개요는 Azure 배치 포럼에서 [Batch 계산 노드에서 애플리케이션 설치 및 데이터 스테이징][forum_post]을 참조하세요. Azure Batch 팀 구성원 중 한 사람이 작성한 이 게시물은 계산 노드에 애플리케이션, 태스크 입력 데이터 및 다른 파일을 복사하는 다른 방법에 대한 좋은 기초입니다.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "다이어그램: 일대일 종속성"
[2]: ./media/batch-task-dependency/02_one_to_many.png "다이어그램: 일대다 종속성"
[3]: ./media/batch-task-dependency/03_task_id_range.png "다이어그램: 태스크 ID 범위 종속성"
