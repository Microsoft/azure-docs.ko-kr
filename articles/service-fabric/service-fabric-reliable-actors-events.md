---
title: 행위자 기반 Azure Service Fabric 행위자의 이벤트
description: 행위자와 클라이언트 간에 통신 하는 효과적인 방법 인 Service Fabric Reliable Actors에 대 한 이벤트에 대해 알아봅니다.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: f5634a33dccb06437f2e5f095e7880221dba9d6e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007913"
---
# <a name="actor-events"></a>행위자 이벤트
행위자 이벤트는 행위자에서 클라이언트로 최상의 알림을 보낼 수 있는 방법을 제공합니다. 행위자 이벤트는 행위자-클라이언트 간 통신을 위해 디자인되었으며 행위자-행위자 간 통신에는 사용하지 않아야 합니다.

다음 코드 조각에서는 애플리케이션에서 행위자 이벤트를 사용하는 방법을 보여 줍니다.

행위자가 게시한 이벤트를 설명하는 인터페이스를 정의합니다. 이 인터페이스는 `IActorEvents` 인터페이스에서 파생되어야 합니다. 메서드의 인수는 [데이터 계약 직렬화 가능](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)해야 합니다. 이벤트 알림은 단방향으로 최대한 제공되므로 메서드는 void를 반환해야 합니다.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
행위자 인터페이스에서 행위자에 의해 게시된 이벤트를 선언합니다.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
클라이언트쪽에서는 이벤트 처리기를 구현합니다.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

클라이언트에서 이벤트를 게시하고 이벤트를 구독하는 행위자에 대한 프록시를 만듭니다.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

장애 조치 발생 시 행위자는 서로 다른 프로세스 또는 노드로 장애 조치(failover)가 될 수 있습니다. 행위자 프록시는 활성 구독을 관리하고 자동으로 재구독합니다. `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API를 통해 재구독 간격을 제어할 수 있습니다. 구독을 취소하려면 `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API를 사용합니다.

행위자에서 이벤트 발생 시 해당 이벤트를 게시합니다. 이벤트에 구독자가 여럿 있는 경우는 행위자 런타임에서 구독자들에게 알림을 보냅니다.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>다음 단계
* [행위자 재입력](service-fabric-reliable-actors-reentrancy.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
* [행위자 API 참조 설명서](/previous-versions/azure/dn971626(v=azure.100))
* [C # 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)
