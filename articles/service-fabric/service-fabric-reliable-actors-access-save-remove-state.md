---
title: Azure Service Fabric 상태 관리 | Microsoft Docs
description: Service Fabric Reliable Actors 상태에 액세스, 저장 및 제거하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 7c10d00916ef65767c98616c7337bfa444c339a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725400"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Reliable Actors 상태에 액세스, 저장 및 제거
[Reliable Actors](service-fabric-reliable-actors-introduction.md)는 논리와 상태를 모두 캡슐화하고 상태를 안정적으로 유지할 수 있는 단일 스레드 개체입니다. 모든 행위자 인스턴스에는 안정적으로 키/값 쌍을 저장하는 사전 형식의 데이터 구조인 고유한 [상태 관리자](service-fabric-reliable-actors-state-management.md)가 있습니다. 상태 관리자는 상태 제공자에 대한 래퍼입니다. 사용되는 [지속성 설정](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication)에 상관없이 데이터를 저장하는 데 사용할 수 있습니다.

상태 관리자 키는 문자열이어야 합니다. 값은 제네릭 및 사용자 지정 형식을 포함한 모든 형식일 수 있습니다. 복제하는 동안 네트워크를 통해 다른 노드로 전송될 수 있기 때문에 상태 관리자에 저장된 값은 데이터 계약을 직렬화 가능해야 하며 행위자의 상태 지속성 설정에 따라 디스크에 기록할 수 있습니다.

상태 관리자는 신뢰할 수 있는 사전에 있는 것과 비슷한 상태를 관리하기 위한 일반적인 사전 메서드를 제공합니다.

자세한 내용은 [행위자 상태 관리의 모범 사례](service-fabric-reliable-actors-state-management.md#best-practices)를 참조하세요.

## <a name="access-state"></a>액세스 상태
상태 관리자를 통해 키로 상태에 액세스합니다. 상태 관리자 메서드는 행위자가 지속된 상태인 경우 디스크 I/O가 필요할 수 있기에 모두 비동기적입니다. 첫 번째 액세스에서 상태 개체는 메모리에 캐시됩니다. 반복 액세스 작업은 메모리에서 직접 개체에 액세스하고 오버헤드를 전환하는 디스크 I/O 또는 비동기 컨텍스트를 발생시키지 않고 동기적으로 반환합니다. 상태 개체는 다음과 같은 경우에 캐시에서 제거됩니다.

* 행위자 메서드는 상태 관리자에서 개체를 검색한 후에 처리되지 않은 예외를 throw합니다.
* 비활성화되거나 오류 발생 후에 행위자가 다시 활성화됩니다.
* 상태 제공자가 디스크에 상태를 페이징합니다. 이 동작은 상태 제공자 구현에 따라 다릅니다. `Persisted` 설정에 대한 기본 상태 제공자는 이 동작을 포함합니다.

키에 대한 항목이 없는 경우 `KeyNotFoundException`(C#) 또는 `NoSuchElementException`(Java)를 throw하는 표준 *Get* 작업을 사용하여 상태를 검색할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

또한 키에 대한 항목이 없는 경우 throw하지 않는 *TryGet* 메서드를 사용하여 상태를 검색할 수 있습니다.

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>상태 저장
상태 관리자 검색 메서드는 로컬 메모리에 개체에 대한 참조를 반환합니다. 로컬 메모리에서 이 개체를 수정하는 것만으로는 지속적으로 저장하지 못합니다. 개체를 상태 관리자에서 검색하고 수정하는 경우 지속적으로 저장되도록 상태 관리자에 다시 삽입해야 합니다.

`dictionary["key"] = value` 구문과 동등한 무조건 *설정*을 사용하여 상태를 삽입할 수 있습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

*Add* 메서드를 사용하여 상태를 추가할 수 있습니다. 이미 존재하는 키를 추가하려고 하면 이 메서드가 `InvalidOperationException`(C#) 또는 `IllegalStateException`(Java)을 throw합니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

*TryAdd* 메서드를 사용하여 상태를 추가할 수도 있습니다. 이미 존재하는 키를 추가하려고 하면 이 메서드가 throw하지 않습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

행위자 메서드가 끝날 때 상태 관리자는 삽입 또는 업데이트 작업에 의해 추가되거나 수정된 모든 값을 자동으로 저장합니다. "저장"은 사용된 설정에 따라 디스크와 복제를 지속하도록 포함할 수 있습니다. 수정되지 않은 값은 지속되거나 복제되지 않습니다. 수정된 값이 없는 경우 저장 작업은 아무 것도 수행하지 않습니다. 실패를 저장하는 경우 수정된 상태는 삭제되고 원래 상태를 다시 로드합니다.

또한 행위자 기반의 `SaveStateAsync` 메서드를 호출하여 상태를 수동으로 저장할 수 있습니다.

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>상태 제거
*제거* 메서드 호출하여 행위자의 상태 관리자에서 상태를 영구적으로 상태를 제거할 수 있습니다. 존재하지 않는 키를 제거하려고 하면 이 메서드가 `KeyNotFoundException`(C#) 또는 `NoSuchElementException`(Java)을 throw합니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

*TryRemove* 메서드를 사용하여 상태를 영구적으로 제거할 수도 있습니다. 존재하지 않는 키를 제거하려고 하면 이 메서드가 throw하지 않습니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>다음 단계

Reliable Actors에 저장된 상태를 디스크에 기록하고 고가용성을 위해 복제하기 전에 직렬화해야 합니다. [행위자 형식 직렬화](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)에 대해 자세히 알아봅니다.

다음으로 [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)에 대해 자세히 알아봅니다.
