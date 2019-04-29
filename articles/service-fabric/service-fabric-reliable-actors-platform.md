---
title: Service Fabric의 Reliable Actors| Microsoft Docs
description: Reliable Actors를 Reliable Services에 계층화하고 서비스 패브릭 플랫폼의 기능을 사용하는 방법을 설명합니다.
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
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: bc7569c9f230abb7677a8df9fc0cc0268e57296f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725933"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>신뢰할 수 있는 행위자가 서비스 패브릭 플랫폼을 사용하는 방법
이 문서에서는 Azure Service Fabric 플랫폼에서 Reliable Actors가 작동하는 방법을 설명합니다. Reliable Actors는 *행위자 서비스*라는 상태 저장 신뢰할 수 있는 서비스의 구현에서 호스트되는 프레임워크에서 실행됩니다. 행위자 서비스는 행위자에게 발송되는 수명 주기 및 메시지를 관리하는 데 필요한 모든 구성 요소를 포함합니다.

* 행위자 런타임은 수명 주기, 가비지 수집을 관리하고 단일 스레드 액세스를 적용합니다.
* 행위자 서비스 원격 수신기는 행위자에 대한 원격 액세스 호출을 허용하고 적절한 행위자 인스턴스를 라우팅하는 디스패처에게 보냅니다.
* 행위자 상태 제공자는 상태 공급자(예: 신뢰할 수 있는 컬렉션 상태 공급자)를 래핑하고 행위자 상태 관리에 대한 어댑터를 제공합니다.

이러한 구성 요소는 Reliable Actor 프레임워크를 함께 구성합니다.

## <a name="service-layering"></a>서비스 계층
행위자 서비스 자체가 Reliable Service이므로 Reliable Services의 [애플리케이션 모델](service-fabric-application-model.md), 수명 주기, [패키징](service-fabric-package-apps.md), [배포](service-fabric-deploy-remove-applications.md), 업그레이드 및 개념 확장은 모두 행위자 서비스에 동일한 방식으로 적용됩니다.

![행위자 서비스 계층][1]

이전 다이어그램은 Service Fabric 애플리케이션 프레임워크와 사용자 코드 간의 관계를 보여 줍니다. 블루 요소는 Reliable Services 애플리케이션 프레임워크를 나타내고 오렌지는 Reliable Actor 프레임워크를 나타내며 그린은 사용자 코드를 나타냅니다.

Reliable Services에서 서비스는 `StatefulService` 클래스를 상속합니다. 이 클래스는 `StatefulServiceBase`(또는 상태 비저장 서비스의 경우 `StatelessService`)에서 파생됩니다. Reliable Actors에서 행위자 서비스를 사용합니다. 행위자 서비스는 행위자가 실행되는 행위자 패턴을 구현하는 `StatefulServiceBase` 클래스의 다른 구현입니다. 행위자 서비스 자체는 `StatefulServiceBase`의 구현이므로 `ActorService`에서 파생된 고유한 서비스를 작성할 수 있고 다음과 같이 `StatefulService`을 상속하는 경우와 동일한 방식으로 서비스 수준 기능을 구현할 수 있습니다.

* 서비스 백업 및 복원
* 모든 행위자에 대한 공유 기능(예: 회로 차단기)
* 행위자 서비스 자체 및 각 개별 행위자에서의 원격 프로시저 호출

자세한 내용은 [행위자 서비스에서 서비스 수준 기능 구현](service-fabric-reliable-actors-using.md)을 참조하세요.

## <a name="application-model"></a>애플리케이션 모델
행위자 서비스는 Reliable Services에 속하므로 애플리케이션 모델이 동일합니다. 그러나 행위자 프레임워크 빌드 도구는 일부 애플리케이션 모델 파일을 생성합니다.

### <a name="service-manifest"></a>서비스 매니페스트
행위자 프레임워크 빌드 도구에서 행위자 서비스의 ServiceManifest.xml 파일의 콘텐츠를 자동으로 생성합니다. 이 파일에는 다음이 포함됩니다.

* 행위자 서비스 유형. 유형 이름은 행위자 프로젝트 이름에 따라 생성됩니다. 행위자의 지속성 특성에 따라 HasPersistedState 플래그도 적절하게 설정됩니다.
* 코드 패키지.
* 구성 패키지.
* 장치 및 엔드포인트.

### <a name="application-manifest"></a>애플리케이션 매니페스트
행위자 프레임워크 빌드 도구는 행위자 서비스에 대한 기본 서비스 정의를 자동으로 만듭니다. 빌드 도구가 기본 서비스 속성을 채웁니다.

* 복제본 세트 수는 행위자의 지속성 특성에 의해 결정됩니다. 행위자의 지속성 특성이 변경될 때마다 기본 서비스 정의에 있는 복제본 세트 수는 적절하게 다시 설정됩니다.
* 파티션 구성표와 범위는 전체 Int64 키 범위인 균일한 Int64로 설정됩니다.

## <a name="service-fabric-partition-concepts-for-actors"></a>행위자에 대한 서비스 패브릭 파티션 개념
행위자 서비스는 분할된 상태 저장 서비스입니다. 행위자 서비스의 각 파티션은 일련의 행위자를 포함합니다. 서비스 파티션은 서비스 패브릭에 있는 여러 노드에 자동으로 배포됩니다. 결과적으로 행위자 인스턴스가 배포됩니다.

![행위자 분할 및 배포][5]

Reliable Services는 다른 파티션 구성표와 파티션 키 범위로 만들어질 수 있습니다. 행위자 서비스는 전체 Int64 키 범위를 가진 Int64 파티션 구성표를 사용하여 파티션에 행위자를 매핑합니다.

### <a name="actor-id"></a>행위자 ID
서비스에서 만들어진 각 행위자에는 그와 관련된 고유한 ID가 있고 `ActorId` 클래스에서 나타납니다. `ActorId`는 임의의 ID를 생성하여 서비스 파티션에 행위자를 균일하게 배포하기 위해 사용될 수 있는 불투명 ID 값입니다.

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


모든 `ActorId`는 Int64로 해시됩니다. 그래서 행위자 서비스가 전체 Int64 키 범위가 있는 Int64 파티션 구성표를 사용해야 합니다. 그러나 사용자 지정 ID 값은 GUID/UUID, 문자열 및 Int64를 비롯한 `ActorID`에 사용할 수 있습니다.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID/UUID 및 문자열을 사용하는 경우 값은 Int64로 해시됩니다. 그러나 명시적으로 `ActorId`에 대한 Int64를 제공하는 경우 Int64는 해시를 추가하지 않고 파티션에 직접 매핑됩니다. 이 기술을 사용하여 행위자가 배치되는 파티션을 제어할 수 있습니다.


## <a name="next-steps"></a>다음 단계
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [.NET 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
