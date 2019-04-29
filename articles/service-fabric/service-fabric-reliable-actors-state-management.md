---
title: Reliable Actors 상태 관리 | Microsoft Docs
description: 고가용성을 위해 Reliable Actors 상태를 관리, 유지 및 복제하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 65dd47ab21ca4b1c50e0f17b73e7bc4eae8a96e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725740"
---
# <a name="reliable-actors-state-management"></a>Reliable Actors 상태 관리
Reliable Actors는 논리와 상태를 모두 캡슐화할 수 있는 단일 스레드 개체입니다. 행위자가 Reliable Services에서 실행되므로 동일한 지속성 및 복제 메커니즘을 사용하여 안전하게 상태를 유지할 수 있습니다. 이러한 방식으로 행위자는 실패한 후에 해당 상태를 손실하지 않고 가비지 수집 후 다시 활성화합니다. 또한 리소스 균형 조정 또는 업그레이드로 인해 클러스터의 노드 간에 이동하는 경우에도 그렇습니다.

## <a name="state-persistence-and-replication"></a>상태 지속성 및 복제
각 행위자 인스턴스가 고유 ID에 매핑되기 때문에 모든 Reliable Actors는 *상태 저장* 이라고 여겨집니다. 즉, 동일한 행위자 ID에 대한 반복된 호출이 동일한 행위자 인스턴스에 라우트됩니다. 상태 비저장 시스템에서 대조적으로 클라이언트 호출이 매번 동일한 서버에 라우트되도록 보장되지는 않습니다. 따라서 행위자 서비스는 항상 상태 저장 서비스입니다.

행위자가 상태 저장을 고려하더라도 상태를 안정적으로 저장해야 하는 것은 아닙니다. 행위자는 해당 데이터 저장소 요구 사항에 따라 상태 지속성 및 복제의 수준을 선택할 수 있습니다.

* **지속 된 상태**: 상태가 유지 되 디스크 및 3 개 이상의 복제본에 복제 됩니다. 지속된 상태는 완전한 클러스터 가동 중단 내내 상태가 유지될 수 있는 가장 지속적인 상태 저장 옵션입니다.
* **일시적 상태**: 상태 3 개 이상의 복제본에 복제 되 고 메모리에만 보관 됩니다. 일시적 상태는 업그레이드 및 리소스 균형을 조정하는 동안 노드 실패 및 행위자 실패에 대한 복원력을 제공합니다. 그러나 상태는 디스크에 유지되지 않으므로 한 번에 모든 복제본이 손실되면 상태도 손실됩니다.
* **지속 된 상태 없음**: 상태 복제 되거나 디스크에 사용 하 여 상태를 안정적으로 유지할 필요가 없는 행위자에 대 한 기록 되지 않습니다.

지속성의 수준은 각각 단순히 *상태 제공자* 및 서비스의 *복제* 구성에 따라 다릅니다. 상태가 디스크에 기록되는지 여부는 상태를 저장하는 Reliable Service의 구성 요소인 상태 제공자에 따라 달라지며 복제는 서비스를 배포한 복제본 수에 따라 달라집니다. Reliable Services와 마찬가지로 상태 제공자와 복제본 수는 모두 쉽게 수동으로 설정될 수 있습니다. 행위자 프레임워크는 특성을 제공합니다. 이 특성은 행위자에 사용될 경우 자동으로 기본 상태 제공자를 선택하고 이러한 세 가지 지속성 설정 중 하나를 달성하기 위해 복제본 수에 대한 설정을 자동으로 생성합니다. StatePersistence 특성은 파생 클래스에서 상속되지 않으며 각 행위자 형식은 해당 StatePersistence 수준을 제공해야 합니다.

### <a name="persisted-state"></a>지속된 상태
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
이 설정은 디스크에 데이터를 저장하는 상태 제공자를 사용하고 자동으로 서비스 복제본 수를 3으로 설정합니다.

### <a name="volatile-state"></a>일시적 상태
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
이 설정은 메모리내 전용 상태 제공자를 사용하고 복제본 수를 3으로 설정합니다.

### <a name="no-persisted-state"></a>지속된 상태 없음
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
이 설정은 메모리내 전용 상태 제공자를 사용하고 복제본 수를 1로 설정합니다.

### <a name="defaults-and-generated-settings"></a>기본값 및 생성된 설정
`StatePersistence` 특성을 사용하는 경우 행위자 서비스를 시작할 때 런타임 시 상태 제공자가 자동으로 선택됩니다. 그러나 복제본 수는 Visual Studio 행위자 빌드 도구에 의해 컴파일 시점에 설정됩니다. 빌드 도구는 ApplicationManifest.xml의 행위자 서비스에 대한 *기본 서비스*를 자동으로 생성합니다. **최소 복제본 세트 크기** 및 **대상 복제본 세트 크기**에 대한 매개 변수를 만듭니다.

이러한 매개 변수를 수동으로 변경할 수 있지만 `StatePersistence` 특성을 변경할 때마다 매개 변수는 이전 값을 재정의하여 선택된 `StatePersistence` 특성에 대한 기본 복제본 세트 크기 값으로 설정됩니다. 즉, ServiceManifest.xml에서 설정한 값은 `StatePersistence` 특성 값을 변경하는 경우 빌드 시에*만* 재정의됩니다.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>상태 관리자
모든 행위자 인스턴스에는 안정적으로 키/값 쌍을 저장하는 사전 형식의 데이터 구조인 고유한 상태 관리자가 있습니다. 상태 관리자는 상태 제공자에 대한 래퍼입니다. 사용되는 지속성 설정에 상관없이 데이터를 저장하는 데 사용할 수 있습니다. 실행 중인 행위자 서비스가 데이터를 유지하는 동안 롤링 업그레이드를 통해 지속된 상태로 설정된 일시적인(메모리 내 전용) 상태에서 변경될 수 있음을 보증하지 않습니다. 그러나 실행 중인 서비스에 대한 복제본 수를 변경할 수 있습니다.

상태 관리자 키는 문자열이어야 합니다. 값은 제네릭 및 사용자 지정 형식을 포함한 모든 형식일 수 있습니다. 복제하는 동안 네트워크를 통해 다른 노드로 전송될 수 있기 때문에 상태 관리자에 저장된 값은 데이터 계약을 직렬화 가능해야 하며 행위자의 상태 지속성 설정에 따라 디스크에 기록할 수 있습니다.

상태 관리자는 신뢰할 수 있는 사전에 있는 것과 비슷한 상태를 관리하기 위한 일반적인 사전 메서드를 제공합니다.

행위자 상태 관리의 예제는 [Reliable Actors 상태 액세스, 저장 및 제거](service-fabric-reliable-actors-access-save-remove-state.md)를 참조하세요.

## <a name="best-practices"></a>모범 사례
다음은 행위자 상태를 관리하는 권장 사례 및 문제 해결 팁입니다.

### <a name="make-the-actor-state-as-granular-as-possible"></a>행위자 상태를 최대한 세분화합니다.
이것은 애플리케이션의 성능 및 리소스 사용량에 중요합니다. 행위자의 “명명된 상태”에 대한 쓰기/업데이트가 있을 때마다, “명명된 상태”에 해당하는 전체 값이 직렬화되어 네트워크를 통해 보조 복제본으로 전송됩니다.  보조 복제본은 이것을 로컬 디스크에 쓰고 주 복제본에 회신합니다. 주 복제본이 보조 복제본의 쿼럼으로부터 승인을 수신하면 로컬 디스크에 상태를 씁니다. 예를 들어 값이 멤버가 20개 있고 크기가 1MB인 클래스라고 가정하겠습니다. 크기가 1KB인 클래스 멤버 중 하나만 수정하더라도 1MB 전체에 대한 직렬화와 네트워크 및 디스크 쓰기에 대한 비용을 지불하게 됩니다. 마찬가지로, 컬렉션(예: 목록, 배열 또는 사전) 값을 사용하는 경우에 멤버 중 하나를 수정하더라도 컬렉션 전체에 대한 비용을 지불합니다. 행위자 클래스의 StateManager 인터페이스는 사전과 유사합니다. 이 사전 위에 행위자 상태를 나타내는 데이터 구조를 항상 모델링해야 합니다.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>행위자의 수명 주기를 올바르게 관리
행위자 서비스의 파티션마다 상태의 크기를 관리하는 명확한 정책이 있어야 합니다. 행위자 서비스에는 고정된 수의 행위자를 두고 최대한 많이 다시 사용해야 합니다. 새 행위자를 지속적으로 만드는 경우 작업이 완료되고 나면 행위자를 삭제해야 합니다. 행위자 프레임워크는 존재하는 각 행위자에 대한 메타데이터를 저장합니다. 행위자의 상태를 모두 삭제해도 해당 행위자에 대한 메타데이터는 제거되지 않습니다. 시스템에 저장된 행위자에 대한 정보를 모두 제거하려면 행위자를 삭제해야 합니다([행위자와 해당 상태 삭제](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state) 참조). 추가 검사의 일환으로, 가끔 행위자 서비스를 쿼리하여 숫자 행위자가 예상 범위 내에 있는지 확인해야 합니다([행위자 열거](service-fabric-reliable-actors-enumerate.md) 참조).
 
행위자 서비스의 데이터베이스 파일 크기가 예상 크기를 초과하여 증가하는 경우가 발생하면 이전 지침을 따르고 있는지 확인해야 합니다. 지침을 따르고 있는데 데이터베이스 파일 크기에 문제가 있으면 제품 팀과 함께 [지원 티켓을 열어서](service-fabric-support.md) 지원을 받는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Reliable Actors에 저장된 상태를 디스크에 기록하고 고가용성을 위해 복제하기 전에 직렬화해야 합니다. [행위자 형식 직렬화](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)에 대해 자세히 알아봅니다.

다음으로 [행위자 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)에 대해 자세히 알아봅니다.
