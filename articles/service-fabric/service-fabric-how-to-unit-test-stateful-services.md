---
title: Azure Service Fabric에서 상태 저장 서비스에 대한 단위 테스트 개발 | Microsoft Docs
description: Service Fabric 상태 저장 서비스에 대한 단위 테스트 개발 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: b066296ca52d3067f8985245161eb4fa7b484a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720130"
---
# <a name="create-unit-tests-for-stateful-services"></a>상태 저장 서비스에 대한 단위 테스트 만들기
Service Fabric 상태 저장 서비스의 단위 테스트는 기본 애플리케이션 또는 도메인별 단위 테스트를 통해 찾지 못할 수 있는 몇 가지 일반적인 실수를 알아냅니다. 상태 저장 서비스에 대한 단위 테스트를 개발하는 경우 유의해야 할 몇 가지 특별한 고려 사항이 있습니다.

1. 모든 복제본이 애플리케이션 코드를 실행하지만 컨텍스트는 다릅니다. 서비스가 3개의 복제본을 사용하는 경우 다양한 컨텍스트/역할에 따라 3개의 노드에서 동시에 서비스 코드가 실행됩니다.
2. 상태 저장 서비스 내에 저장된 상태는 모든 복제본에서 일관되어야 합니다. 상태 관리자 및 신뢰할 수 있는 컬렉션은 기본적으로 이러한 일관성을 제공합니다. 그러나 메모리 내 상태는 애플리케이션 코드에서 관리해야 합니다.
3. 각 복제본은 클러스터에서 실행되는 특정 시점에 역할을 변경합니다. 주 복제본을 호스트하는 노드가 사용 불가능해지거나 오버로드될 경우 보조 복제본은 주 복제본이 됩니다. 이것은 Service Fabric의 자연스러운 동작이므로, 서비스는 다른 역할에서 실행되는 경우를 계획해야 합니다.

이 문서에서는 [Service Fabric의 상태 저장 서비스 단위 테스트](service-fabric-concepts-unit-testing.md)를 읽었다고 가정합니다.

## <a name="the-servicefabricmocks-library"></a>ServiceFabric.Mocks 라이브러리
버전 3.3.0부터, [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/)는 복제본 및 상태 관리의 오케스트레이션을 둘 다 모의하기 위한 API를 제공합니다. 이것은 예제에서 사용됩니다.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mockss는 Microsoft에서 소유하거나 관리하지 않습니다. 그렇지만 현재 상태 저장 서비스를 단위 테스트하기 위해 Microsoft에서 권장되는 라이브러리입니다.*

## <a name="set-up-the-mock-orchestration-and-state"></a>모의 오케스트레이션 및 상태 설정
테스트 정렬 부분의 일환으로, 모의 복제본 세트 및 상태 관리자가 만들어집니다. 그런 다음, 해당 복제본 세트는 각 복제본에 대한 테스트된 서비스의 인스턴스를 자체적으로 만듭니다. 또한 `OnChangeRole` 및 `RunAsync`와 같은 수명 주기 이벤트를 자체적으로 실행합니다. 모의 상태 관리자는 상태 관리자에 대해 모든 작업이 수행되었는지 확인하며, 실제 상태 관리자와 마찬가지로 실행 및 유지됩니다.

1. 테스트할 서비스를 인스턴스화하는 서비스 팩터리 대리자를 만드세요. 이 대리자는 Service Fabric 서비스 또는 작업자에 대해 `Program.cs`에서 일반적으로 확인되는 서비스 팩터리 콜백과 비슷하거나 동일해야 합니다. 또한 다음 서명을 따라야 합니다.
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. `MockReliableStateManager` 클래스의 인스턴스를 만듭니다. 이 인스턴스는 상태 관리자와의 모든 상호 작용을 모의합니다.
3. `MockStatefulServiceReplicaSet<TStatefulService>`의 인스턴스를 만듭니다. 여기서 `TStatefulService`는 테스트할 서비스의 유형입니다. 이를 위해 1단계에서 생성된 대리자와 2단계에서 인스턴스화된 상태 관리자가 필요합니다.
4. 복제본을 복제본 세트에 추가합니다. 복제본의 역할(예: Primary, ActiveSecondary, IdleSecondary) 및 ID를 지정합니다.
   > 복제본 ID를 적어둡니다. 이 ID는 단위 테스트의 동작 및 어설션 부분 중에 사용될 수 있습니다.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>서비스 요청 실행
서비스 요청은 편의 속성 및 조회를 사용하여 특정 복제본에 대해 실행될 수 있습니다.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>서비스 이동 실행
모의 복제본 세트는 다양한 유형의 서비스 이동을 트리거하는 몇 가지 편의 메서드를 노출합니다.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>모든 항목 요약
다음 테스트는 3개 노드의 복제본 세트를 설정하고 역할 변경 후에도 보조 복제본에서 해당 데이터를 사용할 수 있는지 확인하는 방법을 보여 줍니다. 여기서 확인될 수 있는 일반적인 문제는 `InsertAsync` 동안 추가되는 데이터는 `CommitAsync`를 실행하지 않고 메모리 내 또는 신뢰할 수 있는 컬렉션에 저장된 경우입니다. 두 경우 모두 보조 복제본이 주 복제본과 동기화되지 않습니다. 이로 인해 서비스 이동 후에 응답이 불일치하게 됩니다.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>다음 단계
[서비스 간 통신](service-fabric-testability-scenarios-service-communication.md)을 테스트하고 [제어된 비정상 상황을 사용하여 실패를 시뮬레이트](service-fabric-controlled-chaos.md)하는 방법을 알아봅니다.
