---
title: Azure 서비스 패브릭 행위자에서 기능 구현 | Microsoft Docs
description: StatefulService를 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현하는 고유의 행위자 서비스를 작성하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 57894770ad9d27430d5803c9a93ce6973355878a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123249"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>행위자 서비스에서 서비스 수준 기능 구현

[서비스 계층](service-fabric-reliable-actors-platform.md#service-layering)에 설명된 대로 행위자 서비스 자체는 신뢰할 수 있는 서비스입니다. `ActorService`에서 파생되는 고유한 서비스를 작성할 수 있습니다. 다음과 같이 상태 저장 서비스를 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현할 수도 있습니다.

- 서비스 백업 및 복원
- 모든 행위자에 대한 공유 기능(예: 회로 차단기)
- 행위자 서비스 자체 및 각 개별 행위자에서의 원격 프로시저 호출

## <a name="use-the-actor-service"></a>행위자 서비스 사용

행위자 인스턴스는 실행 중인 행위자 서비스에 대한 액세스 권한이 있습니다. 행위자 인스턴스는 행위자 서비스를 통해 프로그래밍 방식으로 서비스 컨텍스트를 얻을 수 있습니다. 서비스 컨텍스트에는 파티션 ID, 서비스 이름, 애플리케이션 이름 및 기타 Azure Service 플랫폼 관련 정보가 있습니다.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Reliable Services처럼 행위자 서비스는 Service Fabric 런타임에서 서비스 유형으로 등록되어야 합니다. 행위자 인스턴스를 실행하는 행위자 서비스의 경우에도 행위자 서비스에 행위자 유형이 등록되어야 합니다. `ActorRuntime` 등록 메서드가 행위자에 대한 이 작업을 수행합니다. 가장 간단한 경우 행위자 형식을 등록할 수 있고 행위자 서비스는 기본 설정을 사용합니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

또는 행위자 서비스를 직접 생성하는 등록 메서드가 제공하는 람다를 사용할 수 있습니다. 그런 다음, 행위자 서비스를 구성하고 행위자 인스턴스를 명시적으로 구성할 수 있습니다. 생성자를 통해 행위자에 종속성을 넣을 수 있습니다.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>행위자 서비스 메서드

행위자 서비스는 `IActorService`(C#) 또는 `ActorService`(Java)를 구현하며 이는 `IService`(C#) 또는 `Service`(Java)를 구현합니다. 이 인터페이스는 Reliable Services 원격에서 사용되며 서비스 메서드에서 원격 프로시저 호출을 허용합니다. 원격 서비스를 통해 원격으로 호출할 수 있는 서비스 수준 메서드가 포함되어 있습니다. 이 메서드를 사용하여 행위자를 [열거](service-fabric-reliable-actors-enumerate.md) 및 [삭제](service-fabric-reliable-actors-delete-actors.md)할 수 있습니다.


## <a name="custom-actor-service"></a>사용자 지정 행위자 서비스

행위자 등록 람다를 사용하여 `ActorService`(C#) 및 `FabricActorService`(Java)에서 파생된 사용자 지정 행위자 서비스를 등록할 수 있습니다. 그런 다음, `ActorService`(C#) 또는 `FabricActorService`(Java)를 상속하는 서비스 클래스를 작성하여 고유한 서비스 수준 기능을 구현할 수 있습니다. 사용자 지정 행위자 서비스는 `ActorService`(C#) 또는 `FabricActorService`(Java)로부터 행위자 런타임 기능을 모두 상속합니다. 또한 고유의 서비스 메서드를 구현하는 데 사용할 수 있습니다.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>행위자 백업 및 복원 구현

사용자 지정 행위자 서비스가 `ActorService`에 이미 나타난 원격 수신기를 활용하여 행위자 데이터를 백업하는 메서드를 노출할 수 있습니다. 예를 들어 [행위자 Backup 및 복원](service-fabric-reliable-actors-backup-and-restore.md)을 참조하세요.

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Remoting V2(인터페이스 호환 가능) 스택을 사용하는 행위자

Remoting V2(인터페이스 호환 가능, V2_1이라고 함) 스택에는 V2 Remoting 스택의 모든 기능이 포함되어 있습니다. 해당 인터페이스는 Remoting V1 스택과 호환되지만 이전 버전의 V2 및 V1과는 호환되지 않습니다. 서비스 가용성에 영향을 주지 않고 V1에서 V2_1로 업그레이드하려면 다음 섹션의 단계를 따르세요.

Remoting V2_1 스택을 사용하려면 다음과 같은 변경이 필요합니다.

1. 다음과 같은 어셈블리 특성을 행위자 인터페이스에 추가합니다.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. V2 스택을 사용하여 시작하려면 행위자 서비스 및 행위자 클라이언트 프로젝트를 빌드하고 업그레이드합니다.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>서비스 가용성에 영향을 주지 않고 Remoting V2(인터페이스 호환 가능) 스택으로 행위자 서비스 업그레이드

이 변경은 2단계 업그레이드입니다. 이 순서대로 단계를 따르세요.

1. 다음과 같은 어셈블리 특성을 행위자 인터페이스에 추가합니다. 이 특성은 행위자 서비스, V1(기존) 및 V2_1 수신기에 대한 2개의 수신기를 시작합니다. 이와 같이 변경하여 행위자 서비스를 업그레이드합니다.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. 이전 업그레이드를 완료한 후에 행위자 클라이언트를 업그레이드합니다.
   이 단계에서는 행위자 프록시가 Remoting V2_1 스택을 사용하는지 확인합니다.

3. 이 단계는 선택 사항입니다. V1 수신기를 제거하려면 이전 특성을 변경합니다.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Remoting V2 스택을 사용하는 행위자

이제 사용자는 버전 2.8 NuGet 패키지와 함께 Remoting V2 스택을 사용할 수 있습니다. 이 스택은 성능이 우수하며 사용자 지정 serialization과 같은 기능을 제공합니다. Remoting V2는 이전 버전의 기존 Remoting 스택(이제 V1 Remoting 스택이라고 함)과 호환되지 않습니다.

Remoting V2 스택을 사용하려면 다음과 같은 변경이 필요합니다.

1. 다음과 같은 어셈블리 특성을 행위자 인터페이스에 추가합니다.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. V2 스택을 사용하여 시작하려면 행위자 서비스 및 행위자 클라이언트 프로젝트를 빌드하고 업그레이드합니다.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>서비스 가용성에 영향을 주지 않고 Remoting V2 스택으로 행위자 서비스 업그레이드

이 변경은 2단계 업그레이드입니다. 이 순서대로 단계를 따르세요.

1. 다음과 같은 어셈블리 특성을 행위자 인터페이스에 추가합니다. 이 특성은 행위자 서비스, V1(기존) 및 V2 수신기에 대한 2개의 수신기를 시작합니다. 이와 같이 변경하여 행위자 서비스를 업그레이드합니다.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. 이전 업그레이드를 완료한 후에 행위자 클라이언트를 업그레이드합니다.
   이 단계에서는 행위자 프록시가 Remoting V2 스택을 사용하는지 확인합니다.

3. 이 단계는 선택 사항입니다. V1 수신기를 제거하려면 이전 특성을 변경합니다.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>다음 단계

* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
