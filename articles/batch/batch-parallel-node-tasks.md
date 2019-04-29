---
title: 병렬로 태스크를 실행하여 효율적으로 계산 리소스 사용 - Azure Batch | Microsoft Docs
description: Azure Batch 풀의 각 노드에서 동시 작업을 실행하고 더 적은 수의 계산 노드를 사용하여 효율성은 높이고 비용은 낮춥니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79b45bd423ed6715cdb7cc7c0e079c150eefede5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763706"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>동시에 태스크를 실행하여 Batch 계산 노드의 사용량 극대화 

Azure Batch 풀의 각 계산 노드에서 동시에 둘 이상의 작업을 실행하여 풀의 더 작은 수의 노드에서 리소스 사용을 최대화할 수 있습니다. 일부 워크로드의 경우, 작업 시간이 짧아지고 비용이 낮아질 수 있습니다.

일부 시나리오에서는 노드의 모든 리소스를 단일 태스크에 전적으로 사용할 수 있지만 몇 가지 상황에서는 여러 태스크가 이러한 리소스를 공유할 수 있는 이점이 있습니다.

* **데이터 전송을 최소화** . 이 시나리오에서는 적은 수의 노드에 공유 데이터를 복사하고 각 노드에 병렬로 작업을 실행하여 데이터 전송 요금을 크게 줄일 수 있습니다. 특히 각 노드로 복사할 데이터를 지역 간에 전송해야 하는 경우 적용됩니다.
* **메모리 사용 최대화** - 태스크에 많은 양의 메모리가 필요하지만 단기간에만 그리고 실행 중에는 가변 시간에 메모리 사용량을 최대화합니다. 이러한 급증을 효율적으로 처리하기 위해 많은 메모리를 가진 적지만 크기가 큰 계산 노드를 사용할 수 있습니다. 이러한 노드에는 각 노드에서 병렬로 실행되는 여러 작업이 있지만 각 작업은 시간을 두고 노드의 풍부한 메모리를 활용합니다.
* **노드 숫자 제한 완화** . 현재 노드 간 통신에 대해 구성된 풀은 계산 노드가 50개로 제한됩니다. 이러한 풀의 각 노드가 병렬로 태스크를 실행할 수 있으면 더 많은 수의 태스크를 동시에 실행할 수 있습니다.
* 최초로 컴퓨팅 환경을 Azure로 이동하는 경우 등 **온-프레미스 계산 클러스터 복제**. 현재 온-프레미스 솔루션이 계산 노드당 여러 태스크를 실행하는 경우 최대 노드 태스크 수를 늘려 해당 구성을 보다 자세히 미러링할 수 있습니다.

## <a name="example-scenario"></a>예제 시나리오 
병렬 태스크 실행의 이점을 보여 주는 한 예로서, 태스크 애플리케이션에 [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) 노드로 충분한 CPU 및 메모리 요구 사항이 있다고 가정해 보겠습니다. 하지만 주어진 시간에 작업을 완료하기 위해 이러한 노드가 1,000개 필요합니다.

1개 CPU 코어가 있는 Standard\_D1 노드를 사용하는 대신 각각 16개 코어가 있는 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) 노드를 사용하여 병렬 태스크 실행을 구현할 수 있습니다. 따라서 사용되는 *노드 수가 1/16로 줄기 때문에* 필요한 노드 수는 1000개가 아니라 63개입니다. 또한 대규모 애플리케이션 파일 또는 참조 데이터가 각 노드에 대해 필요한 경우 데이터가 63개의 노드로 복사되기 때문에 작업 기간 및 효율성은 다시 개선됩니다.

## <a name="enable-parallel-task-execution"></a>병렬 작업 실행 사용
풀 수준에서 병렬 작업 실행을 위해 계산 노드를 구성합니다. Batch .NET 라이브러리를 사용하여 풀을 만들 때 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 속성을 설정합니다. Batch REST API를 사용하는 경우 풀을 만들 때 요청 본문에 [maxTasksPerNode][rest_addpool] 요소를 설정합니다.

Azure Batch를 사용 하면 최대 (4 배) 노드 당 작업을 설정할 수 있습니다 core 노드의 수입니다. 예를 들어, 풀이 노드 크기 “Large”로 구성되었다면(4코어) `maxTasksPerNode` 는 16으로 설정될 수 있습니다. 그러나 노드에 코어 개수에 관계 없이 노드당 256 개 이상의 태스크 수는 없습니다. 각 노드 크기에 대한 코어 수에 대한 자세한 내용은 [Cloud Services에 적합한 크기](../cloud-services/cloud-services-sizes-specs.md)를 참조하세요. 서비스 제한에 대한 자세한 내용은 [Azure Batch 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

> [!TIP]
> 풀에 [자동 크기 조정 수식][enable_autoscaling]을 구성할 때는 `maxTasksPerNode` 값을 고려해야 합니다. 예를 들어, `$RunningTasks` 를 평가하는 수식은 노드당 작업 수 증가에 크게 영향을 받을 수 있습니다. 자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.
>
>

## <a name="distribution-of-tasks"></a>작업 배분
풀 내의 계산 노드가 작업을 동시에 실행할 수 있는 경우 풀 내 노드에서 작업을 배분하려는 방법을 지정하는 것이 중요합니다.

[CloudPool.TaskSchedulingPolicy][task_schedule] 속성을 사용하여 풀의 모든 노드에서 작업을 균등하게 할당하도록 지정할 수 있습니다("확산"). 또는 작업이 풀의 다른 노드로 할당되기 전에 최대한 많은 작업이 각 노드에 할당되도록 지정할 수 있습니다.("압축")

이 기능이 얼마나 중요한지 확인하기 위해 위의 예에서 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 값이 16으로 구성된 [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) 노드 풀을 고려해 보겠습니다. [CloudPool.TaskSchedulingPolicy][task_schedule]가 *Pack*인 [ComputeNodeFillType][fill_type]으로 구성된 경우, 각 노드의 모든 16개 코어의 사용량을 최대화하며 [풀 자동 크기 조정](batch-automatic-scaling.md)을 통해 풀에서 사용되지 않는 노드(작업이 할당되지 않은 노드)를 솎아냅니다. 리소스 사용량을 최소화하고 비용을 절감합니다.

## <a name="batch-net-example"></a>Batch .NET 예
이 [Batch .NET][api_net] API 코드 조각은 노드 당 최대 4개의 작업이 있는 네 개의 노드를 포함하는 풀을 만드는 요청을 나타냅니다. 풀의 다른 노드로 작업을 할당하기 전에 각 노드를 채울 정책을 예약하는 작업을 지정합니다. Batch .NET API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [BatchClient.PoolOperations.CreatePool][poolcreate_net]을 참조하세요.

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Batch REST 예
이 [Batch REST][api_rest] API 코드 조각은 노드당 최대 4개의 작업이 있는 두 대규모 노드를 포함하는 풀을 만드는 요청을 나타냅니다. REST API를 사용하여 풀을 추가하는 방법에 대한 자세한 내용은 [계정에 풀 추가][rest_addpool]를 참조하세요.

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> 풀을 만들 때만 `maxTasksPerNode` 요소 및 [MaxTasksPerComputeNode][maxtasks_net] 속성을 설정할 수 있습니다. 풀이 이미 만들어진 후에는 수정될 수 없습니다.
>
>

## <a name="code-sample"></a>코드 샘플
GitHub의 [ParallelNodeTasks][parallel_tasks_sample] 프로젝트는 [CloudPool.MaxTasksPerComputeNode][maxtasks_net] 속성의 사용을 보여 줍니다.

이 C# 콘솔 애플리케이션은 [Batch .NET][api_net] 라이브러리를 사용하여 하나 이상의 계산 노드가 있는 풀을 만듭니다. 해당 노드에서 구성 가능한 다양한 작업을 실행하여 변수 부하를 시뮬레이션합니다. 애플리케이션에서 출력은 각 작업을 실행하는 노드를 지정합니다. 또한 애플리케이션은 작업 매개 변수 및 기간의 요약을 제공합니다. 아래는 예제 애플리케이션의 두 가지 다른 실행에서의 출력을 요약하는 부분입니다.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

샘플 애플리케이션의 최초 실행에서는 풀 내 단일 노드 및 노드 당 작업의 기본값 설정을 보여주며 작업 길이는 30분이 넘습니다.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

샘플의 두 번째 실행은 작업 기간에서 크게 감소합니다. 풀이 노드 당 4개의 작업으로 구성되었기 때문에 이를 사용하면 병렬 작업 실행이 거의 1/4 시간에서 작업을 완료할 수 있습니다.

> [!NOTE]
> 위 요약의 작업 기간에는 풀 생성 시간이 포함되지 않았습니다. 위의 각 작업은 이전에 만든 풀에 제출되며, 제출 시점에 계산 노드는 *Idle* 상태입니다.
>
>

## <a name="next-steps"></a>다음 단계
### <a name="batch-explorer-heat-map"></a>Batch 탐색기 열 지도
[Batch Explorer][batch_labs]는 Azure Batch 응용 프로그램을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Batch Explorer에는 작업 실행의 시각화를 제공하는 *열 지도* 기능이 포함되어 있습니다. [ParallelTasks][parallel_tasks_sample] 샘플 애플리케이션을 실행할 때 열 지도 기능을 사용하면 각 노드에서 병렬 작업의 실행을 쉽게 시각화할 수 있습니다.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

