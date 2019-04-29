---
title: 경제적이며 우선 순위가 낮은 VM에서 워크로드 실행 - Azure Batch | Microsoft Docs
description: 우선 순위가 낮은 VM을 프로비전하여 Azure Batch 워크로드의 비용을 줄이는 방법을 알아봅니다.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 17668470be3e997c215aacc4cc2c32c80de2dd81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776143"
---
# <a name="use-low-priority-vms-with-batch"></a>Batch에서 낮은 우선 순위 VM 사용

Azure Batch는 낮은 우선 순위 VM(가상 머신)을 사용하여 Batch 워크로드의 비용을 줄입니다. 우선 순위가 낮은 VM은 비용 절감을 위해 대량의 계산 능력을 사용할 수 있도록 하여 새로운 유형의 Batch 워크로드를 가능하게 합니다.
 
우선 순위가 낮은 VM은 Azure에서 남는 용량을 활용합니다. 풀에서 우선 순위가 낮은 VM을 지정하면 Azure Batch는 가능한 경우 이러한 남는 용량을 사용할 수 있습니다.
 
우선 순위가 낮은 VM은 할당이 가능하지 않거나, 사용 가능한 용량에 따라, 언제든지 선점될 수 있다는 단점이 있습니다. 이러한 이유로 우선 순위가 낮은 VM이 특정 유형의 워크로드에 가장 적절합니다. 작업 완료 시간이 유연하고 작업이 여러 VM 간에 분산되는 Batch 및 비동기 처리 워크로드에 우선 순위가 낮은 BM을 사용합니다.
 
우선 순위가 낮은 VM은 전용 VM에 비해 상당히 저렴한 가격으로 제공됩니다. 가격 책정 세부 정보에 대해서는 [Batch 가격 책정](https://azure.microsoft.com/pricing/details/batch/)을 참조하세요.

## <a name="use-cases-for-low-priority-vms"></a>우선 순위가 낮은 VM에 대한 사용 사례

우선 순위가 낮은 VM의 특성을 고려할 때 어떤 워크로드에 해당 VM을 사용할 수 있나요? 작업이 여러 병렬 태스크로 분할되거나, 많은 VM에서 확장 및 분산되는 많은 작업이 있으므로 일반적으로는 Batch 처리 워크로드가 적합합니다.

-   Azure에서 남는 용량을 최대한 활용하기 위해 적절한 작업을 확장할 수 있습니다.

-   경우에 따라 VM을 사용할 수 없거나 선점되면 작업에 대한 용량이 줄어들어 태스크가 중단되고 다시 실행될 수 있습니다. 따라서 작업을 실행하는 데 걸리는 시간을 유연하게 설정해야 합니다.

-   좀 더 긴 태스크를 포함하는 작업은 중단될 경우 더 많은 영향을 받을 수 있습니다. 장기 실행 작업이 검사점 설정을 구현하여 실행될 때 진행 상황을 저장하는 경우 중단에 따른 영향이 훨씬 줄어듭니다. 실행 시간이 더 짧은 태스크는 중단에 따른 영향이 훨씬 작아지므로 우선 순위가 낮은 VM에서 가장 잘 작동합니다.

-   여러 VM을 활용하는 장기 실행 MPI 작업은 하나의 선점된 VM 때문에 전체 작업이 다시 실행되어야 하므로 우선 순위가 낮은 VM을 사용하는 것이 별로 적합하지 않습니다.

Batch 처리 사용 사례의 일부 예제에는 우선 순위가 낮은 VM을 사용하는 것이 적합합니다.

-   **개발 및 테스트**: 특히 대규모 솔루션을 개발 중인 경우 상당한 비용 절감을 실현할 수 있습니다. 모든 유형의 테스트가 혜택을 볼 수 있지만 대규모 부하 테스트 및 회귀 테스트에 사용하면 아주 좋습니다.

-   **요청 시 용량 보완**: 우선 순위가 낮은 VM은 일반적인 전용 VM을 보완하는 데 사용될 수 있습니다. 사용 가능한 경우 작업을 확장하여 더 낮은 비용으로 더 빠르게 완료할 수 있고, 사용 가능하지 않은 경우 전용 VM의 기준만 사용할 수 있게 유지됩니다.

-   **유연한 작업 실행 시간**: 작업이 완료되어야 하는 시간이 유연한 경우 잠재적인 용량 감소가 허용될 수 있지만 우선 순위가 낮은 VM을 추가하면 작업이 더 낮은 비용으로 더 빠르게 실행됩니다.

Batch 풀은 작업 실행 시간의 유연성에 따라 몇 가지 방법으로 우선 순위가 낮은 VM을 사용하도록 구성될 수 있습니다.

-   우선 순위가 낮은 VM은 풀에서만 사용할 수 있습니다. 이 경우 Batch는 사용 가능한 경우 선점된 용량을 모두 복구합니다. 이 구성은 우선 순위가 낮은 VM만 사용될 때 작업을 실행하는 가장 경제적인 방법입니다.

-   우선 순위가 낮은 VM을 고정된 전용 VM과 함께 사용할 수 있습니다. 고정된 전용 VM의 수가 있으면 항상 일정한 용량을 작업 처리에 사용할 수 있도록 보장됩니다.

-   전용 VM과 우선 순위가 낮은 VM이 동적으로 혼합될 수 있으므로 사용 가능한 경우 좀 더 저렴한 우선 순위가 낮은 VM만 사용되고, 필요할 때는 높은 가격의 전용 VM이 확장됩니다. 이 구성은 최소한의 용량으로 작업을 계속 진행할 수 있도록 합니다.

## <a name="batch-support-for-low-priority-vms"></a>우선 순위가 낮은 VM에 대한 Batch 지원

Azure Batch는 우선 순위가 낮은 VM을 쉽게 활용하고 혜택을 얻을 수 있도록 하는 몇 가지 기능을 제공합니다.

-   Batch 풀은 전용 VM 및 우선 순위가 낮은 VM을 모두 포함할 수 있습니다. 각 유형의 VM 수는 풀이 만들어질 때 지정될 수 있으며, 기준 풀에 대해서는 명시적 크기 조정 작업이나 자동 크기 조정 기능을 사용해서 언제든지 변경될 수 있습니다. 작업 및 태스크 전송은 풀의 VM 유형과 관계없이 변경되지 않은 상태로 유지될 수 있습니다. 작업을 가능한 한 경제적으로 실행할 수 있게 우선 순위가 낮은 VM을 완전히 사용하도록 풀을 구성할 수도 있지만 용량이 최소 임계값 아래로 떨어질 경우 작업이 계속 실행되도록 하기 위해 전용 VM을 스핀업할 수 있습니다.

-   Batch 풀은 목표 개수의 우선 순위가 낮은 VM을 자동으로 검색합니다. VM이 선점되면 Batch는 손실된 용량을 대체하고 목표 수준을 복구하려고 합니다.

-   작업이 중단되면 Batch는 작업을 검색하고 자동으로 다시 큐에 넣어 다시 실행합니다.

-   우선 순위가 낮은 VM은 전용 VM의 vCPU 할당량과는 다른 별도의 코어 할당량을 갖습니다. 
    우선 순위가 낮은 VM은 비용이 저렴하므로 할당량이 전용 VM보다 높습니다. 자세한 내용은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#resource-quotas)을 참조하세요.    

> [!NOTE]
> 우선 순위가 낮은 VM은 [사용자 구독 모드](batch-api-basics.md#account)에서 만든 배치 계정에서 지원되지 않습니다.
>

## <a name="create-and-update-pools"></a>풀 만들기 및 업데이트

Batch 풀은 전용 VM 및 우선 순위가 낮은 VM(Compute 노드라고도 함)을 둘 다 포함할 수 있습니다. 전용 및 우선 순위가 낮은 VM 둘 다에 대해 Compute 노드의 목표 수를 설정할 수 있습니다. 목표 노드 수는 풀에 유지하려는 VM의 수를 지정합니다.

예를 들어 전용 VM과 우선 순위가 낮은 VM의 목표 수가 각각 5개와 20개인 Azure Cloud Service VM을 사용하여 풀을 만들려면 다음을 사용합니다.

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

전용 VM과 우선 순위가 낮은 VM의 목표 수가 각각 5개와 20개인 Azure Virtual Machines(이 경우 Linux VM)을 사용하여 풀을 만들려면 다음을 사용합니다.

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

전용 및 우선 순위가 낮은 VM 둘 다의 현재 노드 수를 가져올 수 있습니다.

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

풀 노드에는 해당 노드가 전용 VM인지 또는 우선 순위가 낮은 VM인지를 나타내는 속성이 있습니다.

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

풀에서 하나 이상의 노드가 선점되어도 풀의 목록 노드 작업은 여전히 해당 노드를 반환합니다. 우선 순위가 낮은 노드의 현재 수는 변경되지 않지만 해당 노드의 상태는 **선점됨** 상태로 설정됩니다. Batch는 대체 VM을 찾으려고 하고, 성공하면 노드는 새 노드처럼 **만드는 중**, **시작 중** 상태를 차례로 거쳐 태스크 실행에 사용할 수 있게 됩니다.

## <a name="scale-a-pool-containing-low-priority-vms"></a>우선 순위가 낮은 VM을 포함하는 풀 크기 조정

전용 VM으로만 구성된 풀의 경우처럼, Resize 메서드를 호출하거나 자동 크기 조정을 사용하여 우선 순위가 낮은 VM을 포함하는 풀의 크기를 조정할 수 있습니다.

풀 크기 조정 작업은 **targetLowPriorityNodes** 값을 업데이트하는 두 번째 선택적 매개 변수를 사용합니다.

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

풀 자동 크기 조정 수식은 우선 순위가 낮은 VM을 다음과 같이 지원합니다.

-   서비스 정의 변수 **$TargetLowPriorityNodes**의 값을 가져오거나 설정할 수 있습니다.

-   서비스 정의 변수 **$CurrentLowPriorityNodes**의 값을 가져올 수 있습니다.

-   서비스 정의 변수 **$PreemptedNodeCount**의 값을 가져올 수 있습니다. 
    이 변수는 선점 상태의 노드 수를 반환하고 사용할 수 없는 선점된 노드 수에 따라 전용 노드의 수를 늘리거나 줄일 수 있도록 합니다.

## <a name="jobs-and-tasks"></a>작업 및 태스크

작업 및 태스크는 우선 순위가 낮은 노드에 대한 추가 지원을 거의 요구하지 않으며 다음만 지원됩니다.

-   작업의 JobManagerTask 속성은 새 속성 **AllowLowPriorityNode**를 갖습니다. 
    이 속성이 true이면 작업 관리자 태스크는 전용 또는 우선 순위가 낮은 노드에서 예약될 수 있습니다. 이 속성이 false이면 작업 관리자 태스크는 전용 노드에서만 예약됩니다.

-   태스크 애플리케이션에서는 [환경 변수](batch-compute-node-environment-variables.md)를 사용하여 해당 애플리케이션이 우선 순위가 낮은 노드에서 실행되는지 또는 전용 노드에서 실행되는지를 확인할 수 있습니다. 환경 변수는 AZ_BATCH_NODE_IS_DEDICATED입니다.

## <a name="handling-preemption"></a>선점 처리

VM은 경우에 따라 선점될 수 있습니다. 선점될 경우 Batch는 다음을 수행합니다.

-   선점된 VM의 상태는 **선점됨**으로 업데이트됩니다.
-   작업이 선점된 노드 VM에서 실행되면 해당 작업이 요청되고 다시 실행됩니다.
-   VM은 효과적으로 삭제되므로 VM에 로컬로 저장된 모든 데이터는 손실됩니다.
-   풀은 계속해서 우선 순위가 낮은 노드의 목표 개수가 사용 가능해지도록 하려고 합니다. 대체 용량이 발견되면 노드는 해당 ID를 유지하지만 다시 초기화되며, 작업 예약에 사용되기 전에 먼저 **만드는 중** 및 **시작 중** 상태를 거치게 됩니다.
-   선점 수는 Azure Portal에서 메트릭으로 사용할 수 있습니다.

## <a name="metrics"></a>메트릭

우선 순위가 낮은 노드의 경우 [Azure Portal](https://portal.azure.com)에서 새 메트릭을 사용할 수 있습니다. 이러한 메트릭은 다음과 같습니다.

- 우선 순위가 낮은 노드 수
- 우선 순위가 낮은 코어 수 
- 선점된 노드 수

Azure Portal에서 메트릭을 확인하려면 다음을 수행합니다.

1. 포털에서 Batch 계정으로 이동하여 Batch 계정의 설정을 확인합니다.
2. **모니터링** 섹션에서 **메트릭**을 선택합니다.
3. **사용 가능한 메트릭** 목록에서 원하는 메트릭을 선택합니다.

![우선 순위가 낮은 노드의 메트릭](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>다음 단계

* 배치를 사용하려는 사용자를 위한 중요한 정보는 [개발자를 Batch 기능 개요](batch-api-basics.md)를 참고합니다. 문서에는 Batch 애플리케이션을 빌드하는 동안 사용할 수 있는 풀, 노드, 작업 및 태스크와 같은 Batch 서비스 리소스 및 여러 API 기능에 대한 자세한 내용이 포함됩니다.
* Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
