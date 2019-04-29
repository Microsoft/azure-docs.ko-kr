---
title: Azure Service Fabric 행위자 삭제 | Microsoft Docs
description: Service Fabric Reliable Actors 및 해당 상태를 수동으로 삭제하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: e297a6f42774f29e2eca4a410b695d5bbb636300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726607"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors 및 해당 상태 삭제
비활성화된 행위자의 가비지 수집에서는 행위자 개체를 정리하기만 하고 행위자의 상태 관리자에 저장된 데이터를 제거하지는 않습니다. 행위자가 다시 활성화되면 상태 관리자를 통해 해당 데이터를 다시 사용할 수 있게 됩니다. 행위자가 상태 관리자에 데이터를 저장하고 비활성화되었지만 다시 활성화되지 않는 경우에는 해당 데이터를 정리해야 할 수 있습니다.

[행위자 서비스](service-fabric-reliable-actors-platform.md) 에서는 원격 호출자에서 행위자를 삭제하는 기능을 제공합니다.

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

행위자를 삭제하면 행위자가 현재 활성 상태인지 여부에 따라 다음과 같은 결과가 발생합니다.

* **활성 행위자**
  * 행위자가 활성 행위자 목록에서 제거되고 비활성화됩니다.
  * 해당 상태가 영구적으로 삭제됩니다.
* **비활성 행위자**
  * 해당 상태가 영구적으로 삭제됩니다.

행위자는 행위자 메서드 중 하나에서 자체를 삭제하도록 호출할 수 없습니다. 행위자 호출 컨텍스트 내에서 실행되는 동안에는 런타임에서 단일 스레드 액세스를 적용하기 위해 행위자 호출에 대한 잠금을 획득하기 때문에 행위자를 삭제할 수 없습니다.

Reliable Actors에 대한 자세한 내용은 다음을 참조하세요.
* [행위자 타이머 및 미리 알림](service-fabric-reliable-actors-timers-reminders.md)
* [행위자 이벤트](service-fabric-reliable-actors-events.md)
* [행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
