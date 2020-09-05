---
title: Azure Service Fabric 행위자에서 재진입
description: 호출 컨텍스트에 따라 차단을 논리적으로 방지 하는 방법인 Service Fabric Reliable Actors에 대 한 재입력을 소개 합니다.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 75512633dae8d9a9f61c37bbc795d8e8d20c4ff1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007811"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors 다시 표시
기본적으로 Reliable Actors 런타임을 사용하면 논리적 호출 컨텍스트를 기반으로 다시 표시할 수 있습니다. 따라서 동일한 호출 컨텍스트 체인에 있는 경우 행위자가 다시 표시되도록 할 수 있습니다. 예를 들어 행위자 A가 행위자 C에 메시지를 보내는 행위자 B에 메시지를 보내는 경우 메시지 처리 과정의 일부로 행위자 C가 행위자 A를 호출하면 해당 메시지가 다시 표시됩니다. 다른 호출 컨텍스트의 일부인 다른 모든 메시지는 처리를 완료할 때까지 행위자 A에서 차단됩니다.

`ActorReentrancyMode` 열거에 정의된 행위자 다시 표시에 두 개의 옵션을 사용할 수 있습니다.

* `LogicalCallContext` (기본 동작)
* `Disallowed` - 다시 표시 비활성화

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
다시 표시는 등록하는 동안 `ActorService`의 설정에서 구성될 수 있습니다. 행위자 서비스에서 만든 모든 행위자 인스턴스에 설정이 적용됩니다.

다음 예제에서는 다시 표시 모드를 `ActorReentrancyMode.Disallowed`로 설정하는 행위자 서비스를 보여 줍니다. 이 경우, 행위자가 다시 표시 메시지를 다른 행위자에게 보내면 `FabricException` 형식의 예외가 발생합니다.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>다음 단계
* [행위자 API 참조 설명서](/previous-versions/azure/dn971626(v=azure.100))에서 재진입에 대해 자세히 알아봅니다.
