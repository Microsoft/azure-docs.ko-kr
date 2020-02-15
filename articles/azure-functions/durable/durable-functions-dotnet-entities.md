---
title: .NET의 내구성이 있는 엔터티에 대 한 개발자 가이드-Azure Functions
description: Azure Functions Durable Functions 확장을 사용 하 여 .NET에서 지속형 엔터티로 작업 하는 방법입니다.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198499"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>.NET의 내구성이 있는 엔터티에 대 한 개발자 가이드

이 문서에서는 예제 및 일반적인 조언을 포함 하 여 .NET을 사용 하 여 내구성이 있는 엔터티를 개발 하는 데 사용할 수 있는 인터페이스에 대해 설명 합니다. 

엔터티 함수는 서버를 사용 하지 않는 응용 프로그램 개발자에 게 응용 프로그램 상태를 세분화 된 엔터티의 컬렉션으로 구성 하는 편리한 방법을 제공 합니다. 기본 개념에 대 한 자세한 내용은 지 [속성 엔터티: 개념](durable-functions-entities.md) 문서를 참조 하세요.

현재 엔터티를 정의 하는 두 가지 Api를 제공 합니다.

- **클래스 기반 구문은** 엔터티와 작업을 클래스 및 메서드로 나타냅니다. 이 구문은 쉽게 읽을 수 있는 코드를 생성 하 고 인터페이스를 통해 형식이 선택 된 방식으로 작업을 호출할 수 있도록 합니다. 

- **함수 기반 구문은** 엔터티를 함수로 나타내는 하위 수준 인터페이스입니다. 엔터티 작업을 디스패치 하는 방법 및 엔터티 상태를 관리 하는 방법을 정확 하 게 제어할 수 있습니다.  

이 문서에서는 대부분의 응용 프로그램에 더 적합 하기 때문에 주로 클래스 기반 구문에 중점을 둔 것으로 간주 합니다. 그러나 [함수 기반 구문은](#function-based-syntax) 엔터티 상태 및 작업에 대 한 자체 추상화를 정의 하거나 관리 하려는 응용 프로그램에 적합할 수 있습니다. 또한 클래스 기반 구문에서 현재 지원 되지 않는 genericity가 필요한 라이브러리를 구현 하는 데 적합할 수도 있습니다. 

> [!NOTE]
> 클래스 기반 구문은 단지 함수 기반 구문 위에 있는 계층 이므로 동일한 응용 프로그램에서 두 변형을 모두 교대로 사용할 수 있습니다. 
 
## <a name="defining-entity-classes"></a>엔터티 클래스 정의

다음 예제는 정수 형식의 단일 값을 저장 하 고 `Add`, `Reset`, `Get`및 `Delete`의 네 가지 작업을 제공 하는 `Counter` 엔터티의 구현입니다.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run` 함수는 클래스 기반 구문을 사용 하는 데 필요한 상용구를 포함 합니다. *정적* Azure 함수 여야 합니다. 엔터티에 의해 처리 되는 각 작업 메시지에 대해 한 번 실행 됩니다. `DispatchAsync<T>`를 호출 하 고 엔터티가 메모리에 없는 경우 `T` 형식의 개체를 생성 하 고 저장소에 있는 마지막으로 유지 된 JSON (있는 경우)에서 해당 필드를 채웁니다. 그런 다음 일치 하는 이름을 사용 하 여 메서드를 호출 합니다.

> [!NOTE]
> 클래스 기반 엔터티의 상태는 엔터티가 작업을 처리 하기 전에 **암시적으로 생성** 되며 `Entity.Current.DeleteState()`를 호출 하 여 작업에서 **명시적으로 삭제할** 수 있습니다.

### <a name="class-requirements"></a>클래스 요구 사항
 
엔터티 클래스는 특별 한 슈퍼 클래스, 인터페이스 또는 특성이 필요 하지 않은 POCOs (일반 이전 CLR 개체)입니다. 그러나

- 클래스는 생성 가능 이어야 합니다 ( [엔터티 생성](#entity-construction)참조).
- 클래스는 JSON serializable 이어야 합니다 ( [엔터티 serialization](#entity-serialization)참조).

또한 작업으로 호출 되는 모든 메서드는 추가 요구 사항을 충족 해야 합니다.

- 작업에는 최대 하나의 인수만 사용할 수 있으며 오버 로드 또는 제네릭 형식 인수를 포함 하지 않아야 합니다.
- 인터페이스를 사용 하 여 오케스트레이션에 서 호출 해야 하는 작업은 `Task` 또는 `Task<T>`를 반환 해야 합니다.
- 인수 및 반환 값은 serialize 할 수 있는 값 또는 개체 여야 합니다.

### <a name="what-can-operations-do"></a>작업은 무엇을 수행할 수 있나요?

모든 엔터티 작업에서 엔터티 상태를 읽고 업데이트할 수 있으며, 상태에 대 한 변경 내용이 저장소에 자동으로 유지 됩니다. 또한 작업은 모든 Azure Functions 공통 된 일반 제한 내에서 외부 i/o 또는 기타 계산을 수행할 수 있습니다.

작업은 `Entity.Current` 컨텍스트에서 제공 하는 기능에도 액세스할 수 있습니다.

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티 (이름 및 키 포함)의 ID입니다.
* `SignalEntity`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration`: 새 오케스트레이션을 시작 합니다.
* `DeleteState`:이 엔터티의 상태를 삭제 합니다.

예를 들어 카운터가 100에 도달 하 여 엔터티 ID를 입력 인수로 전달할 때 오케스트레이션을 시작 하도록 counter 엔터티를 수정할 수 있습니다.

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>엔터티에 직접 액세스

클래스 기반 엔터티는 엔터티의 명시적 문자열 이름 및 해당 작업을 사용 하 여 직접 액세스할 수 있습니다. 몇 가지 예제를 제공 합니다. 기본 개념 (예: 신호와 호출)에 대 한 자세한 설명은 [액세스 엔터티에](durable-functions-entities.md#access-entities)대 한 설명을 참조 하세요. 

> [!NOTE]
> 가능 하면 더 많은 형식 검사를 제공 하므로 [인터페이스를 통해 엔터티에 액세스](#accessing-entities-through-interfaces)하는 것이 좋습니다.

### <a name="example-client-signals-entity"></a>예: 클라이언트 신호 엔터티

다음 Azure Http 함수는 REST 규칙을 사용 하 여 삭제 작업을 구현 합니다. URL 경로에서 키가 전달 된 카운터 엔터티에 삭제 신호를 보냅니다.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>예: 클라이언트가 엔터티 상태를 읽습니다.

다음 Azure Http 함수는 REST 규칙을 사용 하 여 GET 작업을 구현 합니다. URL 경로에서 키가 전달 된 카운터 엔터티의 현재 상태를 읽습니다.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> `ReadEntityStateAsync`에서 반환 되는 개체는 단지 로컬 복사본, 즉 이전 특정 시점에서 엔터티 상태의 스냅숏입니다. 특히 오래 된 것일 수 있으며,이 개체를 수정 해도 실제 엔터티에는 영향을 주지 않습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>예: 오케스트레이션 먼저 신호를 전달 하 고 엔터티를 호출 합니다.

다음 오케스트레이션은 카운터 엔터티를 증가 시키는 신호를 주고, 동일한 엔터티를 호출 하 여 최신 값을 읽습니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>인터페이스를 통해 엔터티 액세스

인터페이스는 생성 된 프록시 개체를 통해 엔터티에 액세스 하는 데 사용할 수 있습니다. 이 방법을 사용 하면 작업의 이름과 인수 형식이 구현 된 항목과 일치 합니다. 가능 하면 항상 엔터티에 액세스 하기 위해 인터페이스를 사용 하는 것이 좋습니다.

예를 들어 다음과 같이 카운터 예를 수정할 수 있습니다.

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

엔터티 클래스 및 엔터티 인터페이스는 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)에서 mvvm 하는 조직 및 그레인 인터페이스와 비슷합니다. 영 속 엔터티와 Orleans 간의 유사성 및 차이점에 대 한 자세한 내용은 [가상 행위자와 비교](durable-functions-entities.md#comparison-with-virtual-actors)를 참조 하세요.

인터페이스는 형식 검사를 제공 하는 것 외에도 응용 프로그램 내에서 문제를 더 잘 분리 하는 데 유용 합니다. 예를 들어 엔터티가 여러 인터페이스를 구현할 수 있으므로 단일 엔터티가 여러 역할을 수행할 수 있습니다. 또한 인터페이스는 여러 엔터티에 의해 구현 될 수 있으므로 일반 통신 패턴을 재사용 가능한 라이브러리로 구현할 수 있습니다.

### <a name="example-client-signals-entity-through-interface"></a>예: 클라이언트에서 인터페이스를 통해 신호를 보냅니다.

클라이언트 코드는 `SignalEntityAsync<TEntityInterface>`를 사용 하 여 `TEntityInterface`를 구현 하는 엔터티로 신호를 보낼 수 있습니다. 다음은 그 예입니다.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

이 예제에서 `proxy` 매개 변수는 내부적으로 생성 되는 `ICounter`인스턴스이고, 내부적으로 `Delete`에 대 한 호출을 신호로 변환 합니다.

> [!NOTE]
> `SignalEntityAsync` Api는 단방향 작업에만 사용할 수 있습니다. 작업이 `Task<T>`반환 하는 경우에도 `T` 매개 변수의 값은 항상 null 또는 `default`이며 실제 결과가 아닙니다.
예를 들어 값이 반환 되지 않으므로 `Get` 작업에 신호를 보내는 것은 적절 하지 않습니다. 대신, 클라이언트는 `ReadStateAsync` 사용 하 여 카운터 상태에 직접 액세스 하거나 `Get` 작업을 호출 하는 orchestrator 함수를 시작할 수 있습니다. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>예: 오케스트레이션은 먼저 신호를 전달 하 고 프록시를 통해 엔터티를 호출 합니다.

오케스트레이션 내에서 엔터티를 호출 하거나 신호를 보내기 위해 인터페이스 형식과 함께 `CreateEntityProxy`를 사용 하 여 엔터티에 대 한 프록시를 생성할 수 있습니다. 그런 다음이 프록시를 사용 하 여 작업을 호출 하거나 신호를 보낼 수 있습니다.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

암시적으로 `void`을 반환 하는 모든 작업에 신호를 보내고 `Task` 또는 `Task<T>`를 반환 하는 모든 작업을 호출 합니다. `SignalEntity<IInterfaceType>` 메서드를 명시적으로 사용 하 여이 기본 동작을 변경 하 고 작업을 반환 하는 경우에도 신호를 보낼 수 있습니다.

### <a name="shorter-option-for-specifying-the-target"></a>대상을 지정 하는 짧은 옵션

인터페이스를 사용 하 여 엔터티를 호출 하거나 신호를 보내는 경우 첫 번째 인수는 대상 엔터티를 지정 해야 합니다. 엔터티 ID를 지정 하 여 대상을 지정 하거나, 엔터티를 구현 하는 클래스가 한 개만 있는 경우 엔터티 키만 지정할 수 있습니다.

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

엔터티 키만 지정 하 고 런타임에 고유한 구현을 찾을 수 없으면 `InvalidOperationException`이 throw 됩니다. 

### <a name="restrictions-on-entity-interfaces"></a>엔터티 인터페이스에 대 한 제한 사항

일반적으로 모든 매개 변수 및 반환 형식은 JSON serializable 이어야 합니다. 그렇지 않으면 런타임에 serialization 예외가 throw 됩니다.

또한 다음과 같은 몇 가지 추가 규칙을 적용 합니다.
* 엔터티 인터페이스는 메서드만 정의 해야 합니다.
* 엔터티 인터페이스에 제네릭 매개 변수를 포함 하면 안 됩니다.
* 엔터티 인터페이스 메서드에는 매개 변수를 둘 이상 사용할 수 없습니다.
* 엔터티 인터페이스 메서드는 `void`, `Task`또는을 반환 해야 `Task<T>` 

이러한 규칙을 위반 하는 경우 인터페이스를 `SignalEntity` 또는 `CreateProxy`하는 형식 인수로 사용 하는 경우 런타임에 `InvalidOperationException` 발생 합니다. 예외 메시지는 중단 된 규칙을 설명 합니다.

> [!NOTE]
> `void`를 반환 하는 인터페이스 메서드는 호출 되지 않는 단방향 (단방향) 으로만 신호를 받을 수 있습니다 (양방향). `Task` 또는 `Task<T>`를 반환 하는 인터페이스 메서드를 호출 하거나 신호를 받을 수 있습니다. 호출 된 경우 작업의 결과를 반환 하거나 작업에서 throw 된 예외를 다시 throw 합니다. 그러나 신호를 보낸 경우 작업에서 실제 결과 또는 예외를 반환 하지 않고 기본값만 반환 합니다.

## <a name="entity-serialization"></a>엔터티 serialization

엔터티 상태는 지속적으로 지속형 이므로 엔터티 클래스를 serialize 할 수 있어야 합니다. Durable Functions 런타임은 serialization 및 deserialization 프로세스를 제어 하는 다양 한 정책 및 특성을 지 원하는이 용도의 [Json.NET](https://www.newtonsoft.com/json) 라이브러리를 사용 합니다. 가장 일반적으로 C# 사용 되는 데이터 형식 (배열 및 컬렉션 형식 포함)은 이미 serialize 가능 하며 내구성이 있는 엔터티의 상태를 정의 하는 데 쉽게 사용할 수 있습니다.

예를 들어, Json.NET는 다음 클래스를 쉽게 직렬화 및 deserialize 할 수 있습니다.

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>직렬화 특성

위의 예제에서는 기본 serialization이 더 표시 되도록 여러 특성을 포함 하도록 선택 했습니다.
- 클래스를 serialize 할 수 있어야 함을 알려 주는 `[JsonObject(MemberSerialization.OptIn)]` 클래스에 주석을 달고 JSON 속성으로 명시적으로 표시 된 멤버만 유지 합니다.
-  필드가 지속형 엔터티 상태의 일부임을 알리기 위해 `[JsonProperty("name")]`와 함께 유지 될 필드에 주석을 달고 JSON 표현에 사용할 속성 이름을 지정 합니다.

그러나 이러한 특성은 필요 하지 않습니다. 다른 규칙 또는 특성은 Json.NET에서 작동 하는 한 허용 됩니다. 예를 들어 `[DataContract]` 특성을 사용 하거나 특성을 전혀 사용 하지 않을 수 있습니다.

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

기본적으로 클래스의 이름은 JSON 표현의 일부로 저장 *되지* 않습니다. 즉, `TypeNameHandling.None`를 기본 설정으로 사용 합니다. `JsonObject` 또는 `JsonProperty` 특성을 사용 하 여이 기본 동작을 재정의할 수 있습니다.

### <a name="making-changes-to-class-definitions"></a>클래스 정의 변경

저장 된 JSON 개체가 새 클래스 정의와 더 이상 일치 하지 않을 수 있기 때문에 응용 프로그램을 실행 한 후에 클래스 정의를 변경 하는 경우에는 주의 해야 합니다. 그러나 `JsonConvert.PopulateObject`에서 사용 하는 deserialization 프로세스를 이해 하는 한 데이터 형식을 변경 하는 것이 적절 한 경우도 있습니다.

예를 들어 다음과 같은 몇 가지 변경 내용과 그 결과를 확인할 수 있습니다.

1. 저장 된 JSON에 없는 새 속성을 추가 하는 경우 기본값을 가정 합니다.
1. 저장 된 JSON에 있는 속성이 제거 되 면 이전 내용이 손실 됩니다.
1. 속성의 이름을 바꾸면 기존 항목을 제거 하 고 새 항목을 추가 하는 것 처럼 효과가 있습니다.
1. 속성 형식이 변경 되어 저장 된 JSON에서 더 이상 deserialize 할 수 없는 경우 예외가 throw 됩니다.
1. 속성의 형식이 변경 되었지만 저장 된 JSON에서 deserialize 할 수 있는 경우에는이 작업을 수행 합니다.

Json.NET의 동작을 사용자 지정 하는 데 사용할 수 있는 여러 가지 옵션이 있습니다. 예를 들어 저장 된 JSON에 클래스에 없는 필드가 포함 된 경우 예외를 강제로 적용 하려면 `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`특성을 지정 합니다. 임의의 형식으로 저장 된 JSON을 읽을 수 있는 deserialization에 대 한 사용자 지정 코드를 작성할 수도 있습니다.

## <a name="entity-construction"></a>엔터티 생성

경우에 따라 엔터티 개체가 생성 되는 방식을 보다 효과적으로 제어할 수 있습니다. 이제 엔터티 개체를 생성할 때 기본 동작을 변경 하기 위한 몇 가지 옵션을 설명 합니다. 

### <a name="custom-initialization-on-first-access"></a>처음 액세스할 때 사용자 지정 초기화

액세스 하지 않았거나 삭제 된 엔터티를 작업에 디스패치하기 전에 몇 가지 특수 한 초기화를 수행 해야 하는 경우도 있습니다. 이 동작을 지정 하기 위해 `DispatchAsync`전에 조건부를 추가할 수 있습니다.

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>엔터티 클래스의 바인딩

일반 함수와 달리, 엔터티 클래스 메서드는 입력 및 출력 바인딩에 직접 액세스할 수 없습니다. 대신, 진입점 함수 선언에서 바인딩 데이터를 캡처한 다음, `DispatchAsync<T>` 메서드에 전달해야 합니다. `DispatchAsync<T>`에 전달된 모든 개체는 자동으로 엔터티 클래스 생성자에 인수로 전달됩니다.

다음 예제에서는 `CloudBlobContainer`Blob 입력 바인딩[의 ](../functions-bindings-storage-blob-input.md) 참조를 클래스 기반 엔터티에 사용할 수 있게 하는 방법을 보여 줍니다.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Azure Functions의 바인딩에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩](../functions-triggers-bindings.md) 설명서를 참조하세요.

### <a name="dependency-injection-in-entity-classes"></a>엔터티 클래스의 종속성 주입

엔터티 클래스는 [Azure Functions 종속성 주입](../functions-dotnet-dependency-injection.md)을 지원합니다. 다음 예제에서는 `IHttpClientFactory` 서비스를 클래스 기반 엔터티에 등록하는 방법을 보여 줍니다.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

다음 코드 조각에서는 주입된 서비스를 엔터티 클래스에 통합하는 방법을 보여 줍니다.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Serialization 관련 문제를 방지 하려면 직렬화에서 삽입 된 값을 저장 하는 필드를 제외 해야 합니다.

> [!NOTE]
> 일반 .NET Azure Functions에서 생성자 주입을 사용하는 경우와 달리 클래스 기반 엔터티에 대한 함수 진입점 메서드를 *으로* 선언해야`static` 합니다. 비정적 함수 진입점을 선언하면 일반 Azure Functions 개체 이니셜라이저와 지속성 엔터티 개체 이니셜라이저 간에 충돌이 발생할 수 있습니다.

## <a name="function-based-syntax"></a>함수 기반 구문

지금까지 대부분의 응용 프로그램에 더 적합 하다 고 생각 하는 클래스 기반 구문을 집중적으로 다루었습니다. 그러나 함수 기반 구문은 엔터티 상태 및 작업에 대 한 자체 추상화를 정의 하거나 관리 하려는 응용 프로그램에 적합할 수 있습니다. 또한 클래스 기반 구문에서 현재 지원 되지 않는 genericity를 필요로 하는 라이브러리를 구현할 때 적합할 수도 있습니다. 

함수 기반 구문을 사용 하 여 Entity 함수는 작업 디스패치를 명시적으로 처리 하 고 엔터티의 상태를 명시적으로 관리 합니다. 예를 들어 다음 코드에서는 함수 기반 구문을 사용 하 여 구현 된 *카운터* 엔터티를 보여 줍니다.  

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>엔터티 컨텍스트 개체입니다.

`IDurableEntityContext`형식의 컨텍스트 개체를 통해 엔터티 관련 기능에 액세스할 수 있습니다. 이 컨텍스트 개체는 엔터티 함수에 대 한 매개 변수로 사용할 수 있으며 비동기-로컬 속성 `Entity.Current`를 통해 사용할 수 있습니다.

다음 멤버는 현재 작업에 대 한 정보를 제공 하 고 반환 값을 지정할 수 있도록 합니다. 

* `EntityName`: 현재 실행 중인 엔터티의 이름입니다.
* `EntityKey`: 현재 실행 중인 엔터티의 키입니다.
* `EntityId`: 현재 실행 중인 엔터티 (이름 및 키 포함)의 ID입니다.
* `OperationName`: 현재 작업의 이름입니다.
* `GetInput<TInput>()`: 현재 작업에 대 한 입력을 가져옵니다.
* `Return(arg)`: 작업을 호출한 오케스트레이션에 값을 반환 합니다.

다음 멤버는 엔터티의 상태를 관리 합니다 (만들기, 읽기, 업데이트, 삭제). 

* `HasState`: 엔터티가 존재 하는지 여부에 관계 없이 몇 가지 상태가 있습니다. 
* `GetState<TState>()`: 엔터티의 현재 상태를 가져옵니다. 아직 존재 하지 않는 경우 생성 됩니다.
* `SetState(arg)`: 엔터티의 상태를 만들거나 업데이트 합니다.
* `DeleteState()`: 엔터티의 상태 (있는 경우)를 삭제 합니다. 

`GetState`에서 반환 된 상태가 개체 이면 응용 프로그램 코드에서 직접 수정할 수 있습니다. 종료 시에는 `SetState`을 다시 호출할 필요가 없습니다 (하지만 피해도 않음). `GetState<TState>` 여러 번 호출 되는 경우 동일한 형식을 사용 해야 합니다.

마지막으로, 다음 멤버를 사용 하 여 다른 엔터티에 신호를 보내거나 새 오케스트레이션을 시작 합니다.

* `SignalEntity(EntityId, operation, input)`: 엔터티에 단방향 메시지를 보냅니다.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: 새 오케스트레이션을 시작 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [엔터티 개념에 대 한 자세한 정보](durable-functions-entities.md)
