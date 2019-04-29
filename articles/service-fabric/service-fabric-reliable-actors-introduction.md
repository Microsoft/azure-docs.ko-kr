---
title: Service Fabric Reliable Actors 개요 | Microsoft Docs
description: Service Fabric Reliable Actors 프로그래밍 모델에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 5a237e23dffed76e6122e17b59c85d20ca7e1baf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727208"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors 알아보기
Reliable Actors는 [가상의 행위자](https://research.microsoft.com/en-us/projects/orleans/) 패턴을 기반으로 한 Service Fabric 애플리케이션 프레임워크입니다. Reliable Actors API는 Service Fabric에서 보장하는 확장성과 안정성을 바탕으로 단일 스레드 프로그래밍 모델을 제공합니다.

## <a name="what-are-actors"></a>행위자(Actor)란?
하나의 행위자(actor)는 독립적이며 격리된 컴퓨팅 단위이며 단일 스레드로 실행되는 상태를 가리킵니다. 많은 수의 행위자가 모여 동시에 상호독립적으로 실행할 수 있게 되는 동시 또는 분산 시스템을 위한 컴퓨팅 모델을 [행위자 패턴](https://en.wikipedia.org/wiki/Actor_model)이라고 합니다. 행위자 간에는 통신이 가능할 뿐 아니라 더 많은 행위자를 만들어 낼 수도 있습니다.

### <a name="when-to-use-reliable-actors"></a>Reliable Actors 활용 시나리오
Service Fabric Reliable Actors는 행위자 디자인 패턴을 구현한 것입니다. 소프트웨어 디자인 패턴처럼 특정 패턴을 사용하도록 결정하는 것은 소프트웨어 디자인 문제가 패턴에 맞는지 여부에 따라 이루어집니다.

행위자 디자인 패턴은 많은 분산된 시스템 문제 및 시나리오에 적합하나, 구현하는 패턴 및 프레임워크의 제약 조건을 신중하게 고려해야 합니다. 일반적인 지침으로써, 다음과 같은 문제 또는 시나리오를 모델링하기 위해 행위자 패턴을 고려해볼 수 있습니다.

* 문제가 되는 공간이 1,000개 이상의 많은 수의 작고 독립적이며 격리된 상태 및 논리 단위를 포함하는 경우
* 외부 구성 요소와의 중요한 상호 작용을 필요로 하지 않는 단일 스레드 개체로 작업을 원하는 경우(행위자 세트를 아우르는 상태 쿼리 포함)
* I/O 작업 실행으로 인한 예측할 수 없는 지연을 이유로 행위자 인스턴스가 호출자를 차단하지 않는 경우

## <a name="actors-in-service-fabric"></a>Service Fabric 내에서의 행위자
Service Fabric의 행위자는 Reliable Actors 프레임 워크에서 구현 됩니다. 맨 위에 행위자 패턴 기반 응용 프로그램 프레임 워크 [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)합니다. 작성하는 각 Reliable Actor 서비스는 실제로 분할되고, 상태가 유지되는 Reliable Service입니다.

.NET 개체가 .NET 형식의 인스턴스인 것과 동일하게 모든 행위자는 행위자 형식의 인스턴스로 정의됩니다. 예를 들어, 계산기의 기능을 구현하는 행위자 형식이 있을 수 있으며 클러스터 전체에 걸쳐 다양한 노드에 배포되는 해당 형식의 여러 행위자가 있을 수 있습니다. 이러한 각 행위자는 행위자 ID에 의해 고유하게 식별됩니다.

## <a name="actor-lifetime"></a>행위자 수명
Service Fabric의 행위자는 가상 행위자로 수명이 해당 인메모리(in-memory)의 수명에 제약을 받지 않습니다. 따라서, 구체적으로 생성하거나 제거할 필요가 없습니다. Reliable Actors 런타임은 해당 행위자 ID 요청을 처음으로 받은 시점에 행위자를 자동으로 활성화합니다. 행위자를 특정 기간 동안 사용하지 않으면 Reliable Actors 런타임은 메모리 내 개체를 가비지 수집합니다. 또한 행위자를 나중에 다시 활성화해야 할 경우를 대비해 행위자의 유무에 대한 정보를 유지합니다. 자세한 내용은 [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)을 참조하세요.

이 가상 행위자 수명 추상화는 가상 행위자 모델의 결과로서 몇 가지 주의 사항을 발생시키고 실제로 Reliable Actors 구현은 때때로 이 모델과 달라집니다.

* 해당 행위자 ID에 메시지가 처음으로 전송된 시점에 행위자가 자동으로 활성화(행위자 개체가 생성되게 함)됩니다. 일정 기간 동안 후에 행위자 개체는 가비지를 수집합니다. 나중에 다시 행위자 ID를 사용하면 새로운 행위자 개체가 생성됩니다. 행위자의 상태는 상태 관리자에 저장된 경우 개체의 수명보다 오래 지속됩니다.
* 행위자 ID에 대한 행위자 메서드를 호출하면 해당 행위자를 활성화합니다. 이러한 이유로 행위자 형식에는 런타임에 의해 암시적으로 호출된 해당 생성자가 있습니다. 따라서 매개 변수가 서비스 자체에서 행위자의 생성자로 전달될 수 있지만 클라이언트 코드는 행위자 형식의 생성자에 매개 변수를 전달할 수 없습니다. 결과적으로 행위자는 클라이언트에서 초기화 매개 변수를 필요로 하는 경우 다른 메서드를 호출하는 시점에 부분적으로 초기화된 상태로 생성될 수 있습니다. 클라이언트에서 행위자의 활성화에 대한 단일 진입점이 없습니다.
* Reliable Actors가 생성하는 행위자 개체는 암시적인 형태이지만 행위자와 행위자의 상태를 삭제할 때에는 이를 구체적으로 지정할 수 있습니다.

## <a name="distribution-and-failover"></a>배포 및 장애 조치
Service Fabric은 확장성과 안정성을 제공하기 위해 클러스터 전체에 행위자를 배포합니다. 또한, 필요에 따라 실패한 노드를 정상적인 노드로 자동 마이그레이션합니다. [분할된 상태 저장 Reliable Service](service-fabric-concepts-partitioning.md)에 대한 추상화합니다. 행위자가 *Actor Service*라는 상태 저장 Reliable Service 내에서 실행되기 때문에 배포, 확장성, 안정성 및 자동 장애 조치가 모두 제공됩니다.

행위자는 Actor Service의 파티션에 분산되고 이러한 파티션은 Service Fabric 클러스터의 노드에 분산됩니다. 각 서비스 파티션은 일련의 행위자를 포함합니다. Service Fabric은 서비스 파티션의 배포 및 장애 조치를 관리합니다.

예를 들어 기본 행위자 파티션 배치를 사용하여 세 개의 노드에 배포된 9개의 파티션이 포함된 행위자 서비스는 다음과 같이 분산됩니다.

![Reliable Actors 배포][2]

Actor Framework가 파티션 구성표 및 키 범위 설정을 관리합니다. 이는 일부 선택을 간소화하지만 다음과 같은 몇 가지를 고려해야 합니다.

* Reliable Services를 사용하면 파티션 구성표, 키 범위(범위 파티션 구성표를 사용하는 경우) 및 파티션 수를 선택할 수 있습니다. Reliable Actors는 범위 파티션 구성표(균일한 Int64 구성표)로 제한되고 전체 Int64 키 범위를 사용해야 합니다.
* 기본적으로 행위자는 균일하게 분포되는 파티션에 임의로 배치됩니다.
* 행위자가 임의로 배치되기 때문에 행위자 작업에는 항상 네트워크 통신이 필요하게 됩니다. 네트워크 통신에는 대기 시간 및 오버헤드를 발생시키는 메서드 호출 데이터의 직렬화 및 역직렬화가 포함됩니다.
* 고급 시나리오에서는 특정 파티션에 매핑되는 Int64 행위자 ID를 사용하여 행위자 파티션 배치를 제어할 수 있습니다. 그러나 이렇게 하면 파티션에 행위자를 불균형하게 배치하게 될 수 있습니다.

행위자 서비스를 분할하는 방법에 대한 자세한 내용은 [행위자에 대한 분할 개념](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)을 참조하세요.

## <a name="actor-communication"></a>행위자 통신
행위자의 상호 작용은 인터페이스를 구현하는 행위자가 공유한 인터페이스와 동일한 인터페이스를 통해 행위자에 프록시를 전달하는 클라이언트에 의해 정의됩니다. 이 인터페이스가 행위자 메서드를 비동기적으로 호출하는 데 사용되므로 인터페이스의 모든 메서드는 태스크를 반환해야 합니다.

메서드 호출 및 해당 응답의 결과는 궁극적으로 클러스터에 대한 네트워크 요청이므로 반환한 작업의 결과 형식 및 인수는 플랫폼에서 직렬화가 가능해야 합니다. 특히, [데이터 계약 직렬화가 가능](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)해야 합니다.

### <a name="the-actor-proxy"></a>행위자 프록시
Reliable Actors 클라이언트 API는 행위자 인스턴스 및 행위자 클라이언트 간의 통신을 제공합니다. 행위자와 통신하기 위해 클라이언트는 행위자 인터페이스를 구현하는 행위자 프록시 개체를 만듭니다. 클라이언트는 프록시 개체에서 메서드를 호출하여 행위자와 상호 작용합니다. 행위자 프록시는 클라이언트-행위자 통신 및 행위자 간 통신에 사용할 수 있습니다.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


행위자 프록시 개체를 만드는 데 사용된 두 가지 정보는 행위자 ID 및 애플리케이션 이름입니다. 애플리케이션 이름은 행위자가 배포된 [Service Fabric 애플리케이션](service-fabric-reliable-actors-platform.md#application-model)을 식별하고 해당 행위자 ID는 행위자를 고유하게 식별합니다.

클라이언트 쪽 `ActorProxy`(C#)/`ActorProxyBase`(Java) 클래스는 ID를 기준으로 행위자를 찾는 데 필요한 해결 방법을 수행하고 통신 채널을 엽니다. 또한 통신 오류 및 장애 조치(failover) 시에 행위자를 찾도록 다시 시도합니다. 결과적으로 메시지 전달에는 다음과 같은 특징이 있습니다.

* Best Effort 원칙에 따른 전송이 이루어집니다.
* 행위자는 동일한 클라이언트에서 중복 메시지를 받을 수 있습니다.

## <a name="concurrency"></a>동시성
Reliable Actors 런타임은 행위자 메서드에 액세스하기 위한 간단한 턴 기반 액세스 모델을 제공합니다. 따라서 행위자 개체의 코드 내에서 절대로 둘 이상의 스레드가 함께 활성화될 수 없습니다. 데이터 액세스에 대한 동기화 메커니즘이 필요하지 않기에 턴 기반 액세스는 동시 시스템을 매우 간소화합니다. 또한 각 행위자 인스턴스의 단일 스레드 액세스 특성에 대해 특별히 고려하여 시스템을 디자인해야 합니다.

* 단일 행위자 인스턴스는 한 번에 둘 이상의 요청을 처리할 수 없습니다. 행위자 인스턴스가 동시 요청을 처리해야 하는 경우 처리량 병목 지점이 발생할 수 있습니다.
* 행위자는 행위자 중 하나에 외부 요청을 동시에 수행하는 동안 두 행위자 간에 순환 요청이 있는 경우 행위자는 서로 교착 상태가 될 수 있습니다. 행위자 런타임은 자동으로 행위자 호출에 대해 시간이 초과되고 교착 상태가 발생한 호출자에게 예외를 throw합니다.

![Reliable Actors 통신][3]

### <a name="turn-based-access"></a>턴 기반 액세스
하나의 턴은 다른 행위자 또는 클라이언트의 요청에 대한 응답 시 행위자 메서드의 완전한 실행 또는 [타이머/미리 알림](service-fabric-reliable-actors-timers-reminders.md) 콜백의 완전한 실행으로 이루어집니다. 메서드와 콜백이 비동기인 경우에도 행위자 런타임은 이러한 메서드와 콜백을 인터리빙하지 않습니다. 하나의 턴을 완전히 완료해야 새로운 턴이 허용됩니다. 즉, 메서드 또는 콜백에 대한 새로운 호출을 허용하기 전에 현재 실행 중인 행위자 메서드 또는 타이머/미리 알림 콜백을 완전히 완료해야 합니다. 메서드 또는 콜백에서 실행이 반환된 경우 및 메서드 또는 콜백에서 반환된 작업이 완료된 경우, 메서드 또는 콜백이 완료된 것으로 간주됩니다. 서로 다른 메서드, 타이머 및 콜백 전체에 걸쳐 턴 기반 동시성이 준수된다는 것을 강조할 가치가 있습니다.

행위자 런타임은 턴 시작 시 행위자별 잠금을 획득하고 턴 종료 시 해당 잠금을 해제하여 턴 기반 동시성을 적용합니다. 따라서 전체 행위자가 아닌 개별적인 행위자 단위를 기준으로 턴 기반 동시성이 적용됩니다. 행위자 메서드와 타이머/미리 알림 콜백은 여러 행위자를 대신하여 동시에 실행할 수 있습니다.

다음 예제에서는 위의 개념을 설명합니다. 두 비동기 메서드(즉, *Method1* 및 *Method2*), 타이머 및 미리 알림을 구현하는 행위자 형식을 고려합니다. 아래 다이어그램에서는 이 행위자 형식에 속하는 행위자(*ActorId1* 및 *ActorId2*)를 대신하여 이러한 메서드와 콜백이 실행되는 타임라인의 예를 보여줍니다.

![Reliable Actors 런타임 턴 기반 동시성 및 액세스][1]

이 다이어그램은 다음 규칙을 따릅니다.

* 각 세로줄은 특정 행위자를 대신한 메서드 또는 콜백의 논리적 실행 흐름을 보여줍니다.
* 각 세로줄에 표시된 이벤트는 이전 이벤트 아래에 최신 이벤트가 발생하는 시간적 순서에 따라 발생합니다.
* 각 행위자에 따라 다른 색상이 타임라인에 적용됩니다.
* 강조 표시는 행위자별 잠금이 메서드 또는 콜백을 대신하여 유지되는 기간을 나타내는 데 사용됩니다.

고려할 몇 가지 중요한 사항은 다음과 같습니다.

* *Method1*이 클라이언트 요청 *xyz789*에 대한 응답으로 *ActorId2*를 대신하여 실행되는 동안 *ActorId2*에서 *Method1*을 실행해야 하는 다른 클라이언트 요청(*abc123*)이 도착합니다. 그러나 *Method1* 의 두 번째 실행은 이전 실행이 완료될 때까지 시작하지 않습니다. 비슷하게, *ActorId2*에서 등록한 미리 알림은 클라이언트 요청 *xyz789*에 대한 응답으로 *Method1*이 실행되는 동안 실행됩니다. 미리 알림 콜백은 두 *Method1* 의 실행이 완료된 후에만 실행됩니다. 모두가 *ActorId2*에 적용되는 턴 기반 동시성으로 인한 것입니다.
* 마찬가지로, 턴 기반 동시성은 순차적으로 발생하는 *ActorId1* 대신 *Method1*, *Method2* 및 타이머 콜백을 실행하여 증명된 것처럼 *ActorId1*에 대해서도 적용됩니다.
* *ActorId1*을 대신한 *Method1*의 실행은 *ActorId2*를 대신한 실행과 겹칩니다. 이는 턴 기반 동시성이 전체 행위자들이 아닌 하나의 행위자 내에만 적용되기 때문입니다.
* 일부 메서드/콜백 실행에서 메서드/콜백에 의해 반환된 `Task`(C#)/`CompletableFuture`(Java)의 경우, 해당 메서드가 반환된 후에 완료됩니다. 다른 경우에 비동기 작업은 메서드/콜백이 반환되는 시점에 이미 완료되었습니다. 두 경우 모두 메서드/콜백이 반환되고 비동기 작업이 완료된 후에 행위자별 잠금이 해제됩니다.

### <a name="reentrancy"></a>다시 표시
기본적으로 행위자 런타임은 다시 표시를 허용합니다. 따라서 *행위자 A*의 행위자 메서드가 *행위자 A*의 다른 메서드를 차례로 호출하는 *행위자 B*의 메서드를 호출하는 경우 해당 메서드를 실행할 수 있습니다. 즉, 동일한 논리 호출 체인 컨텍스트의 일부이기 때문입니다. 모든 타이머 및 미리 알림 호출은 새로운 논리 호출 컨텍스트에서 시작됩니다. 자세한 내용은 [Reliable Actors 다시 표시](service-fabric-reliable-actors-reentrancy.md)를 참조하세요.

### <a name="scope-of-concurrency-guarantees"></a>동시성 보증의 범위
행위자 런타임은 이러한 메서드의 호출을 제어하는 상황에서 이러한 동시성을 보증합니다. 예를 들어 타이머와 미리 알림 콜백 뿐만 아니라 클라이언트 요청에 대한 응답으로 수행되는 메서드 호출에 대해 이러한 보증을 제공합니다. 하지만 행위자 코드가 행위자 런타임에서 제공하는 메커니즘 외부에서 이러한 메서드를 호출하는 경우 런타임은 어떠한 동시성도 보장할 수 없습니다. 예를 들어, 행위자 메서드에서 반환된 작업과 연결되지 않은 일부 작업의 컨텍스트에서 메서드를 호출하는 경우 런타임은 동시성을 보장할 수 없습니다. 행위자가 자체적으로 만드는 스레드에서 메서드가 호출되는 경우 런타임은 동시성을 보장할 수 없습니다. 따라서 백그라운드 작업을 수행하려면 행위자가 턴 기반 동시성을 따르는 [행위자 타이머 및 행위자 미리 알림](service-fabric-reliable-actors-timers-reminders.md) 을 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
첫 번째 Reliable Actors 서비스를 빌드하여 시작합니다.
   * [.NET에서 Reliable Actors 시작](service-fabric-reliable-actors-get-started.md)
   * [Java에서 Reliable Actors 시작](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
