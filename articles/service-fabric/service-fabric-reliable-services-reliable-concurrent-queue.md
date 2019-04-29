---
title: Azure Service Fabric의 ReliableConcurrentQueue
description: ReliableConcurrentQueue는 병렬 큐에 추가하고 큐에서 제거할 수 있는 처리량이 높은 큐입니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: dbdfa4686c047fa7cf5d74cd9aca768447f9db93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774015"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Azure Service Fabric의 ReliableConcurrentQueue 소개
신뢰할 수 있는 동시 큐는 비동기, 트랜잭션 및 복제된 큐로서 큐에 넣기 및 큐에서 제거 작업에 대한 높은 동시성을 제공합니다. [신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx)에서 제공한 엄격한 FIFO 순서를 완화하여 처리량이 높고 대기 시간이 짧게 설계되었으며 대신 최상의 순서를 제공합니다.

## <a name="apis"></a>API

|동시 큐                |신뢰할 수 있는 동시 큐                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>[신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx)와 비교

신뢰할 수 있는 동시 큐는 [신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx)에 대한 대안을 제공합니다. FIFO 보장은 동시성을 상쇄해야 하기 때문에 엄격한 FIFO 순서가 필요하지 않은 경우에 사용해야 합니다.  [신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx)는 잠금을 사용하여 큐에 넣을 수 있는 최대 트랜잭션 및 한 번에 큐에서 제거하도록 허용되는 최대 트랜잭션 순으로 FIFO 순서를 강제합니다. 비교에서 신뢰할 수 있는 동시 큐는 정렬 제약 조건을 완화하고 어떤 수의 동시 트랜잭션이 해당 큐에 넣기 및 큐에서 제거 작업을 인터리브하도록 할 수 있습니다. 가장 효율적인 순서를 제공하지만 신뢰할 수 있는 동시 큐에 있는 두 값의 상대적 순서를 보장할 수 없습니다.

여러 동시 트랜잭션이 큐에 넣기 및/또는 큐에서 제거를 수행할 때 신뢰할 수 있는 동시 큐는 [신뢰할 수 있는 큐](https://msdn.microsoft.com/library/azure/dn971527.aspx)보다 처리량이 높고 대기 시간이 낮습니다.

ReliableConcurrentQueue의 샘플 사용 사례는 [메시지 큐](https://en.wikipedia.org/wiki/Message_queue) 시나리오입니다. 이 시나리오에서 한 명 이상의 메시지 생산자는 항목을 만들고 큐에 추가하며 한 명 이상의 메시지 소비자는 큐에서 메시지를 끌어오고 처리합니다. 여러 생산자 및 소비자는 큐를 처리하기 위해 동시 트랜잭션을 사용하여 독립적으로 작동할 수 있습니다.

## <a name="usage-guidelines"></a>사용 지침
* 큐에 있는 항목의 보존 기간이 짧습니다. 즉, 항목은 오랜 시간 동안 큐에 유지되지 않습니다.
* 큐는 엄격한 FIFO 순서를 보장하지 않습니다.
* 큐는 고유한 쓰기를 읽을 수 없습니다. 항목이 트랜잭션 내에서 큐에 삽입된 경우 동일한 트랜잭션 내에서 큐에서 제거하는 사용자에게 표시되지 않습니다.
* 큐에서 제거는 서로 분리되지 않습니다. 항목 *A*가 트랜잭션 *txnA*라는 큐에서 제거된 경우 *txnA*가 커밋되지 않더라도 항목 *A*는 동시 트랜잭션 *txnB*에 표시되지 않습니다.  *txnA*가 중단되면 *txnB*에서 즉시 *A*를 볼 수 있게 됩니다.
* *TryPeekAsync* 동작은 *TryDequeueAsync*를 사용한 다음 트랜잭션을 중단하여 구현할 수 있습니다. 이러한 예는 프로그래밍 패턴 섹션에 있습니다.
* 개수는 비트랜잭션입니다. 큐에서 요소의 수를 추측하는 데 사용할 수 있지만 특정 시점을 나타내며 의존할 수 없습니다.
* 큐에서 제거된 항목에서 비용이 많이 드는 처리는 시스템의 성능에 영향을 줄 수 있는 장기 실행 트랜잭션을 방지하기 위해 트랜잭션이 활성화된 동안 수행하면 안됩니다.

## <a name="code-snippets"></a>코드 조각
몇 가지 코드 조각 및 예상된 출력에 대해 살펴보겠습니다. 이 섹션에서 예외 처리는 무시됩니다.

### <a name="enqueueasync"></a>EnqueueAsync
다음은 예상된 출력 뒤에 EnqueueAsync를 사용하기 위한 몇 가지 코드 조각입니다.

- *사례 1: 단일 큐에 넣기 작업*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

작업이 성공적으로 완료되고 큐를 수정하는 동시 트랜잭션이 없다고 가정합니다. 사용자는 다음 순서 중 하나로 항목을 포함하는 큐를 예상할 수 있습니다.

> 10, 20
> 
> 20, 10


- *사례 2: 병렬 큐에 넣기 작업*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

작업이 성공적으로 완료되고, 작업이 병렬로 실행되고, 큐를 수정하는 다른 동시 트랜잭션이 없다고 가정합니다. 큐에 있는 항목의 순서를 방해할 수 없습니다. 이 코드 조각에서 항목은 4가지 가능한 순서 중 하나로 나타날 수 있습니다.  큐는 원래 (큐에 넣은) 순서로 항목을 유지하려고 하지만 오류 또는 동시 작업으로 인해 순서를 변경해야 할 수도 있습니다.


### <a name="dequeueasync"></a>DequeueAsync
다음은 예상된 출력 뒤에 TryDequeueAsync를 사용하기 위한 몇 가지 코드 조각입니다. 큐가 이미 큐의 다음 항목으로 채워졌다고 가정합니다.
> 10, 20, 30, 40, 50, 60

- *사례 1: 단일 큐에서 제거 작업*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

작업이 성공적으로 완료되고 큐를 수정하는 동시 트랜잭션이 없다고 가정합니다. 큐에 있는 항목의 순서를 방해할 수 없으므로 3개의 모든 항목은 순서에 관계없이 제거될 수 있습니다. 큐는 원래 (큐에 넣은) 순서로 항목을 유지하려고 하지만 오류 또는 동시 작업으로 인해 순서를 변경해야 할 수도 있습니다.  

- *사례 2: 병렬 큐에서 제거 작업*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

작업이 성공적으로 완료되고, 작업이 병렬로 실행되고, 큐를 수정하는 다른 동시 트랜잭션이 없다고 가정합니다. 큐에 있는 항목의 순서를 방해할 수 없으므로 *dequeue1* 및 *dequeue2* 목록은 순서에 관계없이 각각 두 개의 항목을 포함합니다.

동일한 항목은 두 목록에 모두 표시되지 *않습니다*. 따라서 dequeue1에 *10*, *30*이 있으면 dequeue2에는 *20*, *40*이 포함됩니다.

- *사례 3: 트랜잭션이 중단된 큐에서 제거 순서*

진행 중인 큐에서 제거를 포함한 트랜잭션을 중단하면 항목을 큐의 시작 부분에 다시 배치합니다. 항목을 큐의 시작 부분에 다시 배치하는 순서는 보장되지 않습니다. 다음 코드를 살펴보겠습니다.

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
다음과 같은 순서로 항목을 큐에서 제거한다고 가정합니다.
> 10, 20

트랜잭션을 중단하는 경우 항목은 다음 순서로 큐의 시작 부분에 다시 추가됩니다.
> 10, 20
> 
> 20, 10

트랜잭션이 성공적으로 *커밋되지* 않은 경우에도 마찬가지입니다.

## <a name="programming-patterns"></a>프로그래밍 패턴
이 섹션에서는 ReliableConcurrentQueue를 사용하는 데 도움이 될 수 있는 몇 가지 프로그래밍 패턴을 살펴보겠습니다.

### <a name="batch-dequeues"></a>큐에서 제거 일괄 처리
한 번에 큐에서 제거를 수행하는 대신 큐에서 제거를 일괄 처리하는 소비자 작업의 경우 프로그래밍 패턴을 사용하는 것이 좋습니다. 사용자는 모든 일괄 처리 또는 일괄 처리 크기 간에 지연 시간을 제한하도록 선택할 수 있습니다. 다음 코드 조각에서는 이 프로그래밍 모델을 보여줍니다.  이 예제에서 트랜잭션이 커밋된 후에 처리를 수행합니다. 따라서 처리하는 동안 오류가 발생하는 경우 처리되지 않은 항목은 처리되지 않고 손실됩니다.  또는 트랜잭션 범위 내에서 처리를 수행할 수 있지만 그러면 성능에 부정적인 영향을 주고 이미 처리된 항목을 처리해야 할 수 있습니다.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>최상의 알림 기반 처리
개수 API를 사용하는 또 다른 프로그래밍 패턴입니다. 여기서는 큐에 대해 최상의 알림 기반 처리를 구현할 수 있습니다. 큐 개수는 큐에 넣기 또는 큐에서 제거 작업을 제한하는 데 사용할 수 있습니다.  이전 예제와 같이 처리가 트랜잭션 외부에서 발생하므로 처리하는 동안 오류가 발생하는 경우 처리되지 않은 항목은 손실될 수 있습니다.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>최상의 드레이닝
데이터 구조의 동시 특성으로 인해 큐의 드레이닝을 보장할 수 없습니다.  큐에서 사용자 작업이 진행되지 않는 경우더라도 TryDequeueAsync에 대한 특정 호출은 이전에 큐에 넣고 커밋된 항목을 반환하지 않을 수 있습니다.  큐에 넣은 항목은 *결국* 큐에서 제거된다고 표시되지만 모든 생산자가 중지되고 새 큐에 넣기 작업이 허용되는 경우에도 독립 소비자는 대역외 통신 메커니즘 없이 큐가 안정적인 상태에 도달했음을 알 수 없습니다. 따라서 드레이닝 작업은 아래와 같이 구현될 때 가장 효율적입니다.

사용자는 큐를 비우기 전에 모든 추가 생산자와 소비자 작업을 중지하고 실행 중인 모든 트랜잭션을 커밋하거나 중단할 때까지 기다려야 합니다.  사용자가 예상된 큐의 항목 수를 아는 경우 모든 항목이 큐에서 제거되었음을 알리는 알림을 설정할 수 있습니다.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>보기
ReliableConcurrentQueue는 *TryPeekAsync* API를 제공하지 않습니다. 사용자는 *TryDequeueAsync*를 사용한 다음 트랜잭션을 중단하여 보기 의미 체계를 가져올 수 있습니다. 이 예제에서는 큐에서 제거는 항목의 값이 *10*보다 큰 경우에만 처리됩니다.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>필수 참고 목록
* [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
* [신뢰할 수 있는 컬렉션 작업](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 알림](service-fabric-reliable-services-notifications.md)
* [Reliable Services Backup 및 복원(재해 복구)](service-fabric-reliable-services-backup-restore.md)
* [신뢰할 수 있는 상태 관리자 구성](service-fabric-reliable-services-configuration.md)
* [Service Fabric Web API 서비스 시작](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services 프로그래밍 모델 고급 사용법](service-fabric-reliable-services-advanced-usage.md)
* [신뢰할 수 있는 컬렉션에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
