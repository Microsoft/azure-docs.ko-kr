---
title: Azure Service Fabric 행위자 수명 개요 | Microsoft Docs
description: 서비스 패브릭 Reliable Actor 수명 주기, 가비지 수집 및 행위자와 해당 상태 수동 삭제에 대해 설명합니다.
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
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: f81fde441a2f0dc2504601f82e5b890eb6e216de
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105295"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>행위자 수명 주기, 자동 가비지 수집 및 수동 삭제
행위자는 해당 메서드 중 하나가 처음 호출되면 활성화됩니다. 구성 가능한 기간 동안 사용되지 않으면 비활성화됩니다(행위자 런타임에 의한 가비지 수집). 행위자와 그 상태를 언제든지 수동으로 삭제할 수도 있습니다.

## <a name="actor-activation"></a>행위자 활성화
행위자가 활성화되면 다음 상황이 발생합니다.

* 행위자를 호출하는 경우 아직 활성화되지 않았으면 새 행위자를 만듭니다.
* 행위자의 상태가 로드됩니다(상태가 유지되는 경우).
* `OnActivateAsync`(C#) 또는 `onActivateAsync`(Java)메서드(행위자 구현 시 재정의될 수 있음)를 호출합니다.
* 이제 행위자가 활성 상태인 것으로 간주됩니다.

## <a name="actor-deactivation"></a>행위자 비활성화
행위자 비활성화되면 다음과 같은 상황이 발생합니다.

* 행위자를 일정 기간 동안 사용하지 않으면 활성 행위자 테이블에서 제거됩니다.
* `OnDeactivateAsync`(C#) 또는 `onDeactivateAsync`(Java)메서드(행위자 구현 시 재정의될 수 있음)를 호출합니다. 그러면 행위자에 대한 모든 타이머가 지워집니다. 상태 변경과 같은 행위자 작업은 이 메서드에서 호출되지 않습니다.

> [!TIP]
> 패브릭 행위자 런타임에서는 일부 [행위자 활성화 및 비활성화 관련 이벤트](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.
>
>

### <a name="actor-garbage-collection"></a>행위자 가비지 수집
행위자 비활성화되면 행위자 개체에 대한 참조가 해제되고 가비지가 CLR(공용 언어 런타임) 또는 JVM(Java 가상 컴퓨터) 가비지 수집기에 의해 정상적으로 수집될 수 있습니다. 가비지 수집에서는 행위자 개체를 정리하기만 하고, 행위자의 상태 관리자에 저장된 상태를 제거하지 **않습니다** . 다음에 행위자가 활성화되면 새 행위자 개체가 만들어지고 해당 상태가 복원됩니다.

비활성화 및 가비지 수집 목적에서 "사용 중"으로 계산되는 항목

* 호출 수신
* `IRemindable.ReceiveReminderAsync` 메서드(행위자가 미리 알림을 사용하는 경우에만 해당).

> [!NOTE]
> 행위자가 타이머를 사용하고 해당 타이머 콜백이 호출된 경우 "사용 중"으로 계산하지 **않습니다** .
>
>

비활성화에 대한 세부 정보를 살펴보기 전에 다음과 같은 용어를 정의하는 것이 중요합니다.

* *스캔 간격*. 행위자 런타임이 비활성화 및 가비지 수집될 수 있는 행위자에 대해 해당 활성 행위자 테이블을 스캔하는 간격입니다. 기본값은 1분입니다.
* *유휴 시간 제한*. 행위자가 비활성화 및 가비지 수집되기 전에 미사용(유휴) 상태를 유지해야 하는 시간입니다. 기본값은 60분입니다.

일반적으로 이 기본값은 변경할 필요가 없습니다. 그러나 [행위자 서비스](service-fabric-reliable-actors-platform.md)를 등록할 때 필요한 경우 `ActorServiceSettings`를 통해 이러한 간격을 변경할 수 있습니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
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
    }
}
```
각 활성 행위자에 대해 행위자 런타임은 유휴 상태였던 시간(사용되지 않은 시간)을 기록합니다. 행위자 런타임은 `ScanIntervalInSeconds`마다 각 행위자를 검사하여 가비지 수집 가능한지 확인하고 `IdleTimeoutInSeconds` 동안 유휴 상태였는지 수집합니다.

행위자를 사용할 때마다 유휴 시간이 0으로 다시 설정됩니다. 이후부터는 `IdleTimeoutInSeconds`동안 다시 유휴 상태인 경우에만 행위자가 가비지 수집됩니다. 행위자 인터페이스 메서드 또는 행위자 미리 알림 콜백이 실행되는 경우, 행위자가 사용된 것으로 간주됩니다. 타이머 콜백이 실행되는 경우 해당 행위자는 사용된 것으로 간주되지 **않습니다** .

다음 다이어그램에서는 이러한 개념을 설명하는 단일 행위자의 수명 주기를 보여 줍니다.

![유휴 시간의 예][1]

이 예제에서는 행위자 메서드 호출에 대한 영향 및 이 행위자의 수명에 대한 타이머를 보여 줍니다. 이 예제에 대해 다음과 같은 사항에 주의해야 합니다.

* ScanInterval 및 IdleTimeout은 각각 5와 10으로 설정합니다. 이 예제에서는 개념만 보여 주므로 단위는 중요하지 않습니다.
* 스캔 간격이 5로 정의되어 있으므로 가비지 수집할 행위자의 스캔은 T=0,5,10,15,20,25에서 수행됩니다.
* 주기적 타이머는 T=4,8,12,16,20,24에서 발생하며 해당 콜백이 실행됩니다. 행위자의 유휴 시간에는 영향을 주지 않습니다.
* 행위자는 T=7에서 호출되고 유휴 시간을 0으로 재설정하고 행위자의 가비지 수집을 지연시킵니다.
* 행위자 미리 알림 콜백은 T=14에서 실행되며 추가로 행위자의 가비지 수집이 지연됩니다.
* T=25에서 가비지 수집 스캔이 수행되는 동안, 결국 행위자의 유휴 시간이 10으로 설정된 유휴 시간 제한을 초과하고 행위자는 가비지 수집됩니다.

메서드 중 하나를 실행하는 동안에는 해당 메서드를 실행하는 데 소요되는 시간에 상관 없이 행위자는 절대 가비지 수집되지 않습니다. 앞서 설명한 대로 행위자 인터페이스 메서드 및 미리 알림 콜백을 실행하면 행위자의 유휴 시간을 0으로 다시 설정하여 가비지 수집을 방지합니다. 타이머 콜백 실행의 경우는 유휴 시간이 0으로 설정되지 않습니다. 하지만 행위자의 가비지 수집은 타이머 콜백의 실행이 완료될 때까지는 지연됩니다.

## <a name="manually-deleting-actors-and-their-state"></a>수동으로 행위자와 해당 상태 삭제
비활성화된 행위자의 가비지 수집에서는 행위자 개체를 정리하기만 하고 행위자의 상태 관리자에 저장된 데이터를 제거하지는 않습니다. 행위자가 다시 활성화되면 상태 관리자를 통해 해당 데이터를 다시 사용할 수 있게 됩니다. 행위자가 상태 관리자에 데이터를 저장하고 비활성화되었지만 다시 활성화되지 않는 경우에는 해당 데이터를 정리해야 할 수 있습니다.  행위자를 삭제하는 방법의 예제는 [행위자 및 해당 상태 삭제](service-fabric-reliable-actors-delete-actors.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [행위자 타이머 및 미리 알림](service-fabric-reliable-actors-timers-reminders.md)
* [행위자 이벤트](service-fabric-reliable-actors-events.md)
* [행위자 다시 표시](service-fabric-reliable-actors-reentrancy.md)
* [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
