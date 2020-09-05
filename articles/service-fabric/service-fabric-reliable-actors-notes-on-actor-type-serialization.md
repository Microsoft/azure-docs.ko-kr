---
title: 행위자 형식 직렬화에 대 한 Reliable Actors 참고
description: 서비스 패브릭 Reliable Actors 상태 및 인터페이스를 정의하는 데 사용될 수 있는 직렬화가 가능 클래스를 정의하기 위한 기본 요구 사항을 설명합니다.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 33a15b3c6f570d3b4d36a205f91bce7b042f3bb4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016583"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>서비스 패브릭 신뢰할 수 있는 행위자 형식 직렬화에 대한 참고 사항
모든 메서드의 인수, 행위자 인터페이스의 각 메서드에서 반환 되는 작업의 결과 형식 및 행위자의 상태 관리자에 저장 된 개체는 [데이터 계약을 serialize](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)할 수 있어야 합니다. 또한 [행위자 이벤트 인터페이스](service-fabric-reliable-actors-events.md)에 정의된 메서드의 인수에도 적용됩니다. (행위자 이벤트 인터페이스 메서드는 항상 void를 반환합니다.)

## <a name="custom-data-types"></a>사용자 지정 데이터 형식
이 예제에서 다음과 같은 행위자 인터페이스는 `VoicemailBox`이라는 사용자 지정 데이터 형식을 반환하는 메서드를 정의합니다.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

이 인터페이스는 행위자에서 구현되며 이는 상태 관리자를 사용하여 `VoicemailBox` 개체를 저장합니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

이 예제에서는 다음의 경우 `VoicemailBox` 개체를 직렬화합니다.

* 개체는 행위자 인스턴스와 호출자 간에 전송됩니다.
* 개체는 디스크에 유지되고 다른 노드에 복제되는 상태 관리자에 저장됩니다.

Reliable Actor 프레임워크는 DataContract 직렬화를 사용합니다. 따라서 사용자 지정 데이터 개체와 해당 멤버는 각각 **DataContract** 및 **DataMember** 특성을 사용 하 여 주석으로 처리 되어야 합니다.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>다음 단계
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 타이머 및 미리 알림](service-fabric-reliable-actors-timers-reminders.md)
* [행위자 이벤트](service-fabric-reliable-actors-events.md)
* [행위자 재입력](service-fabric-reliable-actors-reentrancy.md)
* [행위자 다형성 및 개체 지향 디자인 패턴](service-fabric-reliable-actors-polymorphism.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
