---
title: 태스크 및 노드의 상태 계산
description: Batch 솔루션을 관리하고 모니터링할 수 있으려면 컴퓨팅 노드 및 Azure Batch 태스크의 상태를 계산합니다.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85960591"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>상태별로 노드 및 작업을 계산하여 Batch 솔루션 모니터링

대규모 Azure Batch 솔루션을 모니터링 및 관리하려면 다양한 상태의 리소스 수 확인이 필요할 수 있습니다. Azure Batch는 Batch 작업 및 컴퓨팅 노드 수를 가져오는 데 효율적인 작업을 제공합니다. 시간이 오래 걸릴 수 있는 목록 쿼리 대신 해당 작업을 사용하여 노드 또는 작업의 대형 컬렉션에 대한 자세한 정보를 반환할 수 있습니다.

- [Get Task Counts](/rest/api/batchservice/job/gettaskcounts)는 성공 또는 실패한 태스크 및 작업에서 활성, 실행 및 완료된 태스크의 집계 수를 가져옵니다. 

  각 상태에 있는 태스크를 계산하면 사용자에게 작업 진행 상태를 쉽게 표시하거나, 작업에 영향을 미칠 수 있는 예상치 못한 지연 또는 오류를 감지할 수 있습니다. Get Task Counts는 Batch 서비스 API 버전 2017-06-01.5.1 및 관련 SDK 및 도구부터 사용할 수 있습니다.

- [List Pool Node Counts](/rest/api/batchservice/account/listpoolnodecounts)는 만들기, 유휴 상태, 오프라인, 선점, 재부팅, 이미지로 다시 설치, 시작 및 기타의 다양한 상태에 있는 각 풀에서 전용 및 우선 순위가 낮은 컴퓨팅 노드 수를 가져옵니다.

  각 상태에서 노드를 계산하면 작업을 실행하기에 충분한 컴퓨팅 리소스가 있고 풀을 사용하여 잠재적인 문제를 식별하는 경우를 확인할 수 있습니다. List Pool Node Counts는 Batch 서비스 API 버전 2018-03-01.6.1 및 관련 SDK 및 도구부터 사용할 수 있습니다.

이 작업에서 반환되는 숫자가 최신 상태가 아닌 경우도 있습니다. 수가 정확한지 확인해야 하는 경우 목록 쿼리를 사용하여 해당 리소스를 계산합니다. 목록 쿼리를 사용하면 애플리케이션과 같은 다른 Batch 리소스에 대한 정보도 얻을 수 있습니다. 목록 쿼리에 필터링을 적용하는 방법에 대한 자세한 내용은 [Batch 리소스를 효율적으로 나열하는 쿼리 만들기](batch-efficient-list-queries.md)를 참조하세요.

## <a name="task-state-counts"></a>태스크 상태 수

Get Task Counts 연산은 다음과 같 상태별로 태스크를 계산합니다.

- **활성** - 대기열에 있으며 실행할 수 있지만, 현재는 컴퓨팅 노드에 할당되지 않는 태스크입니다. 아직 완료되지 않은 [상위 태스크에 종속된](batch-task-dependencies.md) 태스크도 `active`입니다. 
- **실행 중** - 컴퓨팅 노드에 할당되었지만, 아직 완료되지 않은 태스크입니다. [태스크에 대한 정보 가져오기](/rest/api/batchservice/task/get) 연산으로 표시된 대로 상태가 `preparing` 또는 `running`인 태스크는 `running`으로 계산됩니다.
- **완료** - 태스크가 성공적으로 또는 실패로 완료되고 다시 시도 제한 횟수를 모두 사용했기 때문에 더 이상 실행하기에 적합하지 않은 태스크입니다. 
- **성공** - 태스크 실행의 결과가 `success`인 태스크입니다. Batch는 [executionInfo](/rest/api/batchservice/task/get)의 `TaskExecutionResult` 속성을 확인하여 태스크의 성공 또는 실패 여부를 확인합니다.
- **실패** - 태스크 실행의 결과가 `failure`인 태스크입니다.

다음 .NET 코드 예제는 상태별로 태스크 수를 가져오는 방법을 보여 줍니다.

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

REST 및 기타 지원되는 언어에 유사한 패턴을 사용하여 작업에 대한 태스크 수를 가져올 수 있습니다. 

> [!NOTE]
> 2018-08-01.7.0 이전의 Batch Service API 또한 태스크 수 가져오기 응답에서 `validationStatus` 속성을 반환합니다. 이 속성은 Batch가 상태 수와 List Tasks API에 보고된 상태의 일관성 여부를 확인했는지 여부를 나타냅니다. `validated` 값은 Batch가 작업에 대해 1번 이상 일관성을 확인했는지만 나타냅니다. `validationStatus` 속성 값은 작업 수 가져오기에서 반환하는 수가 현재 최신 상태인지는 나타내지 않습니다.

## <a name="node-state-counts"></a>노드 상태 수

List Pool Node Counts 연산은 각 풀의 다음 상태에 따라 컴퓨팅 노드를 계산합니다. 전용 노드 및 각 풀에서 우선 순위가 낮은 노드에 대해 별도 집계 수가 제공됩니다.

- **만들기** - 풀에 조인이 아직 시작되지 않은 Azure 할당 VM입니다.
- **유휴 상태** - 현재 태스크를 실행하지 않는 사용 가능한 컴퓨팅 노드입니다.
- **LeavingPool** - 사용자가 명시적으로 풀을 제거하거나, 풀이 크기가 조정되거나 자동 축소되기 때문에 풀을 나가는 노드입니다.
- **오프라인** - Batch가 새 태스크를 예약하는 데 사용할 수 없는 노드입니다.
- **선점** - Azure가 VM을 회수했기 때문에 풀에서 제거된 우선 순위가 낮은 노드입니다. `preempted` 노드는 대체 우선 순위가 낮은 VM 용량을 사용할 수 있는 경우 다시 초기화될 수 있습니다.
- **다시 부팅** - 다시 시작하는 노드입니다.
- **이미지로 다시 설치** - 다시 설치되는 운영 체제의 노드입니다.
- **실행** - 하나 이상의 태스크(시작 태스크 이외)를 실행하는 노드입니다.
- **시작** - 시작 중인 Batch 서비스의 노드입니다. 
- **StartTaskFailed** - 실패하고 모든 재시도를 다 써버린 [시작 태스크](/rest/api/batchservice/pool/add#starttask) 및 시작 태스크에 설정된 `waitForSuccess`의 노드입니다. 노드는 태스크를 실행하는 데 사용할 수 없습니다.
- **알 수 없음** - Batch 서비스와 연락을 상실하여 해당 상태가 알려지지 않은 노드입니다.
- **사용할 수 없음** - 오류로 인해 태스크 실행에 사용할 수 없는 노드입니다.
- **WaitingForStartTask** - 시작 태스크는 실행을 시작했지만 `waitForSuccess`가 설정되어 있어 시작 태스크가 완료되지 않은 상태의 노드입니다.

다음 C# 코드 조각은 현재 계정에서 모든 풀에 대한 노드 수를 나열하는 방법을 보여줍니다.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

다음 C# 코드 조각은 현재 계정에서 지정된 풀에 대한 노드 수를 나열하는 방법을 보여줍니다.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

REST 및 기타 지원되는 언어에 유사한 패턴을 사용하여 풀에 대한 노드 수를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- Batch 리소스를 나열하는 쿼리에 필터링을 적용하는 방법에 대한 자세한 내용은 [쿼리를 만들어서 효율적으로 Batch 리소스 나열](batch-efficient-list-queries.md)을 참조하세요.
