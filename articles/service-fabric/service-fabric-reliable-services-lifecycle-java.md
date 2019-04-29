---
title: Azure Service Fabric Reliable Services 수명 주기 | Microsoft Docs
description: Service Fabric Reliable Services의 수명 주기 이벤트에 대해 알아봅니다.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 36c1ff2ace944d84120bf456060c7504170a814c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772944"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services 수명 주기
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux에서 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services는 Azure Service Fabric에서 제공되는 프로그래밍 모델 중 하나입니다. Reliable Services의 수명 주기에 대해 알아볼 때는 기본 수명 주기 이벤트를 이해하는 것이 가장 중요합니다. 이벤트의 정확한 순서는 구성 세부 정보에 따라 달라집니다. 

일반적으로 Reliable Services 수명 주기에는 다음 이벤트가 포함됩니다.

* 시작 중:
  * 서비스가 생성됩니다.
  * 서비스는 0개 이상의 수신기를 생성하고 반환할 수 있습니다.
  * 반환된 모든 수신기는 서비스와의 통신을 위해 열려 있습니다.
  * 서비스의 `runAsync` 메서드가 호출되어, 서비스가 장기 실행 중인 작업 또는 백그라운드 작업을 수행할 수 있습니다.
* 종료 중:
  * `runAsync`에 전달된 취소 토큰이 취소되고 수신기가 닫힙니다.
  * 서비스 개체 자체는 소멸됩니다.

Reliable Services에서 이벤트 순서는 신뢰할 수 있는 서비스가 상태 비저장 또는 상태 저장인지 여부에 따라 약간 달라질 수 있습니다. 

또한 상태 저장 서비스의 경우, 기본 스왑 시나리오를 해결해야 합니다. 이 과정 중에 서비스를 종료하지 않고 주 복제본의 역할을 다른 복제본으로 전송하거나 복구합니다. 

마지막으로 오류 또는 실패 조건에 대해 고려해야 합니다.

## <a name="stateless-service-startup"></a>상태 비저장 서비스 시작
상태 비저장 서비스의 수명 주기는 매우 간단합니다. 이벤트의 순서는 다음과 같습니다.

1. 서비스가 생성됩니다.
2. 이러한 이벤트는 병렬로 수행됩니다.
    - `StatelessService.createServiceInstanceListeners()`가 호출되고 반환된 모든 수신기가 열립니다. `CommunicationListener.openAsync()`가 각 수신기에서 호출됩니다.
    - 서비스의 `runAsync` 메서드(`StatelessService.runAsync()`)가 호출됩니다.
3. 서비스의 고유 `onOpenAsync` 메서드(있는 경우)가 호출됩니다. 특히 `StatelessService.onOpenAsync()`가 호출됩니다. 이는 일반적이지 않은 재정의이지만 사용 가능합니다.

수신기를 만들고 여는 호출과 `runAsync`에 대한 호출 사이에는 순서가 없다는 것을 알아야 합니다. `runAsync`가 시작되기 전에 수신기가 열릴 수 있습니다. 마찬가지로, 통신 수신기가 열리기 전에 또는 생성되기도 전에 `runAsync`가 호출될 수 있습니다. 동기화가 필요한 경우 구현자가 수행해야 합니다. 다음은 몇 가지 일반적인 해결 방법입니다.

* 경우에 따라 다른 정보가 생성되거나 다른 작업이 수행될 때까지 수신기가 작동할 수 없습니다. 상태 비저장 서비스의 경우, 이러한 작업은 일반적으로 서비스의 생성자에서 수행될 수 있습니다. `createServiceInstanceListeners()` 호출 중에 또는 수신기 자체의 생성 중에 수행될 수 있습니다.
* 경우에 따라 `runAsync`의 코드는 수신기가 열릴 때까지 시작되지 않습니다. 이 경우 추가 조정이 필요합니다. 일반적인 솔루션은 수신기에 플래그를 추가하는 것입니다. 플래그는 수신기가 완료된 시간을 나타냅니다. `runAsync` 메서드는 실제 작업을 계속하기 전에 이를 확인합니다.

## <a name="stateless-service-shutdown"></a>상태 비저장 서비스 종료
상태 비저장 서비스를 종료할 때에도 동일한 패턴이 사용되지만, 방향이 반대로 수행됩니다.

1. 이러한 이벤트는 병렬로 수행됩니다.
    - 열려 있는 수신기가 모두 닫힙니다. `CommunicationListener.closeAsync()`가 각 수신기에서 호출됩니다.
    - `runAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `isCancelled` 속성을 확인하면 `true`를 반환하고, 호출된 경우 토큰의 `throwIfCancellationRequested` 메서드에서 `CancellationException`이 발생(throw)됩니다.
2. `closeAsync()`가 각 수신기에 완료되고 `runAsync()`도 완료되면 서비스의 `StatelessService.onCloseAsync()` 메서드가 호출됩니다(있는 경우). 다시 말해 이것이 일반적인 재정의는 아니지만 리소스를 안전하게 닫거나 백그라운드 프로세스를 중지하거나 외부 상태 저장을 완료하거나 기존 연결을 종료하는 데 사용할 수 있습니다.
3. `StatelessService.onCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-startup"></a>상태 저장 서비스 시작
상태 저장 서비스에는 상태 비저장 서비스와 비슷한 패턴이 포함되지만, 몇 가지 다른 점이 있습니다.  상태 저장 서비스를 시작할 때의 이벤트 순서는 다음과 같습니다.

1. 서비스가 생성됩니다.
2. `StatefulServiceBase.onOpenAsync()`을 호출합니다. 이 호출은 서비스에서 드물게 재정의됩니다.
3. 이러한 이벤트는 병렬로 수행됩니다.
    - `StatefulServiceBase.createServiceReplicaListeners()`가 호출됩니다. 
      - 서비스가 주 서비스인 경우, 반환된 모든 수신기가 열립니다. `CommunicationListener.openAsync()`가 각 수신기에서 호출됩니다.
      - 서비스가 보조 서비스인 경우, `listenOnSecondary = true`로 표시된 수신기만 열립니다. 보조 복제본에서 수신기가 열려 있는 경우는 일반적이지 않습니다.
    - 서비스가 현재 주 서비스인 경우 서비스의 `StatefulServiceBase.runAsync()` 메서드가 호출됩니다.
4. 모든 복제본 수신기의 `openAsync()` 호출이 완료되고 `runAsync()`가 호출되면 `StatefulServiceBase.onChangeRoleAsync()`가 호출됩니다. 이 호출은 서비스에서 드물게 재정의됩니다.

상태 비저장 서비스와 마찬가지로, 상태 저장 서비스에서는 수신기가 만들어지고 열리는 순서와 `runAsync`가 호출되는 순서가 조정되지 않습니다. 조정이 필요한 경우 해결 방법은 거의 동일합니다. 하지만 상태 저장 서비스에 대한 한 가지 추가 사례가 있습니다. 즉 통신 수신기에 도달한 호출에는 일부 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md) 내에 보관된 정보가 필요합니다. 통신 수신기는 Reliable Collections가 읽기 또는 쓰기 가능할 때까지 그리고 `runAsync`가 시작되기 전에 열릴 수 있기 때문에, 일부 추가 조정이 필요합니다. 가장 간단하고 일반적인 솔루션은 통신 수신기가 오류 코드를 반환하는 것입니다. 클라이언트는 오류 코드를 사용해서 요청을 재시도해야 하는지 확인합니다.

## <a name="stateful-service-shutdown"></a>상태 저장 서비스 종료
상태 비저장 서비스와 마찬가지로 종료 중 수명 주기 이벤트는 시작 시와 동일하지만 역방향으로 이뤄집니다. 상태 저장 서비스가 종료되면 다음과 같은 이벤트가 발생합니다.

1. 이러한 이벤트는 병렬로 수행됩니다.
    - 열려 있는 수신기가 모두 닫힙니다. `CommunicationListener.closeAsync()`가 각 수신기에서 호출됩니다.
    - `runAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `isCancelled()` 메서드에 대한 호출은 `true`를 반환하고, 호출되는 경우 토큰의 `throwIfCancellationRequested()` 메서드에서 `OperationCanceledException`이 발생(throw)합니다.
2. `closeAsync()`가 각 수신기에서 완료되고 `runAsync()`도 완료되면 서비스의 `StatefulServiceBase.onChangeRoleAsync()`가 호출됩니다(있는 경우). 이 호출은 서비스에서 드물게 재정의됩니다.

   > [!NOTE]  
   > `runAsync`가 완료될 때까지 기다리는 것은 이 복제본이 주 복제본인 경우에만 필요합니다.

3. `StatefulServiceBase.onChangeRoleAsync()` 메서드가 완료되면 `StatefulServiceBase.onCloseAsync()` 메서드가 호출됩니다. 이 호출은 일반적이지 않은 재정의이지만 사용 가능합니다.
3. `StatefulServiceBase.onCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-primary-swaps"></a>상태 저장 서비스 주 교환
상태 저장 서비스가 실행 중일 때는 통신 수신기가 열리고, 해당 상태 저장 서비스의 주 복제본에 대해서만 `runAsync` 메서드가 호출됩니다. 보조 복제본을 생성하지만 더 이상 호출하지 않습니다. 상태 저장 서비스가 실행되는 동안 현재 주 복제본인 복제본을 변경할 수 있습니다. 상태 저장 복제본이 확인할 수 있는 수명 주기 이벤트는 스왑 중 복제본이 강등 또는 승격 중인지 여부에 따라 달라집니다.

### <a name="for-the-demoted-primary"></a>강등된 주 복제본의 경우
Service Fabric에서 강등되는 주 복제본은 메시지 처리를 중지하고, 모든 백그라운드 작업을 중지해야 합니다. 이 단계는 서비스가 종료될 때와 비슷합니다. 한 가지 차이점은 서비스가 보조 복제본으로 그대로 남아 있으므로 소멸되거나 종료되지 않는다는 점입니다. 다음 이벤트가 발생합니다.

1. 이러한 이벤트는 병렬로 수행됩니다.
    - 열려 있는 수신기가 모두 닫힙니다. `CommunicationListener.closeAsync()`가 각 수신기에서 호출됩니다.
    - `runAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `isCancelled()` 메서드를 확인하면 `true`가 반환됩니다. 호출된 경우 토큰의 `throwIfCancellationRequested()` 메서드에서 `OperationCanceledException`이 발생(throw)됩니다.
2. `closeAsync()`가 각 수신기에서 완료되고 `runAsync()`도 완료되면 서비스의 `StatefulServiceBase.onChangeRoleAsync()`가 호출됩니다(있는 경우). 이 호출은 서비스에서 드물게 재정의됩니다.

### <a name="for-the-promoted-secondary"></a>승격된 보조 복제본의 경우
마찬가지로 Service Fabric에서는 승격되는 보조 복제본이 통신 중에 메시지에 대한 수신 대기를 시작하고, 완료해야 하는 모든 백그라운드 작업을 시작해야 합니다. 이 프로세스는 서비스가 생성될 때와 비슷합니다. 복제본 자체가 이미 존재한다는 것만 다릅니다. 다음 이벤트가 발생합니다.

1. 이러한 이벤트는 병렬로 수행됩니다.
    - `StatefulServiceBase.createServiceReplicaListeners()`가 호출되고 반환된 모든 수신기가 열립니다. `CommunicationListener.openAsync()`가 각 수신기에서 호출됩니다.
    - 서비스의 `StatefulServiceBase.runAsync()` 메서드가 호출됩니다.
2. 모든 복제본 수신기의 `openAsync()` 호출이 완료되고 `runAsync()`가 호출되면 `StatefulServiceBase.onChangeRoleAsync()`가 호출됩니다. 이 호출은 서비스에서 드물게 재정의됩니다.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>상태 저장 서비스 종료 및 기본 수준 내리기 동안의 일반적인 문제
Service Fabric은 다양한 이유로 상태 저장 서비스의 주 복제본을 변경합니다. 가장 일반적인 이유는 [클러스터 재분산](service-fabric-cluster-resource-manager-balancing.md) 및 [애플리케이션 업그레이드](service-fabric-application-upgrade.md)입니다. 이러한 작업 중에는 서비스가 `cancellationToken`을 고려하는 것이 중요합니다. 이것은 서비스가 삭제된 경우와 같은 일반 서비스 종료 중에도 적용됩니다.

취소를 완전히 처리하지 않는 서비스에는 몇 가지 문제가 발생할 수 있습니다. Service Fabric에서 서비스가 정상적으로 중지되기를 기다리기 때문에 이러한 작업이 느려질 수 있습니다. 이로 인해 궁극적으로 시간이 초과되고 롤백하게 되는 실패한 업그레이드로 이어질 수 있습니다. 취소 토큰을 사용하지 않으면 클러스터 불균형이 발생할 수 있습니다. 클러스터 불균형은 노드가 과부하되기 때문에 발생합니다. 하지만 이를 다른 곳으로 이동하는 데 시간이 너무 오래 걸리기 때문에 서비스 균형을 다시 조정할 수 없습니다. 

서비스가 상태 저장 서비스이기 때문에 또한 서비스에 [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)가 사용될 가능성이 높습니다. Service Fabric에서 주 복제본이 강등될 때 처음 발생하는 상황 중 하나는 기본 상태에 대한 쓰기 액세스가 철회되는 것입니다. 그 결과 서비스 수명 주기에 영향을 줄 수 있는 두 번째 문제들이 발생합니다. 컬렉션에서는 타이밍 및 복제본이 이동되거나 종료되는지 여부에 따라 예외를 반환합니다. 이러한 예외를 올바르게 처리하는 것이 중요합니다. 

Service Fabric으로 throw된 예외는 영구적[(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception)이거나 일시적[(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception)입니다. 영구적 예외는 로깅 및 throw해야 합니다. 일시적인 예외는 재시도 논리에 따라 재시도할 수 있습니다.

Reliable Services 테스트 및 유효성 검사 중 중요한 부분은 서비스 수명 주기 이벤트와 함께 `ReliableCollections`를 사용할 때 발생하는 예외를 처리하는 것입니다. 서비스는 항상 부하 상태에서 실행하는 것이 좋습니다. 또한 프로덕션에 배포하기 전에 업그레이드 및 [Chaos 테스트](service-fabric-controlled-chaos.md)를 수행해야 합니다. 이러한 기본 단계는 서비스가 올바르게 구현되고, 수명 주기 이벤트가 올바르게 처리될 수 있도록 보장합니다.

## <a name="notes-on-service-lifecycle"></a>서비스 수명 주기에 대한 참고 사항
* `runAsync()` 메서드 및 `createServiceInstanceListeners/createServiceReplicaListeners` 호출은 모두 선택 사항입니다. 서비스에는 이 중 하나 또는 두 개가 모두 포함되거나 아무 것도 포함되지 않을 수 있습니다. 예를 들어 서비스가 사용자 호출에 대한 응답으로 모든 작업을 수행할 경우, `runAsync()`를 구현할 필요가 없습니다. 통신 수신기 및 해당 관련 코드만이 필요합니다.  마찬가지로 통신 수신기 만들기 및 반환은 선택 사항입니다. 서비스에 수행할 백그라운드 작업만 포함될 수 있기 때문에 `runAsync()`만 구현하면 됩니다.
* 서비스가 `runAsync()`를 성공적으로 완료하고 여기에서 반환하는 것은 유효합니다. 이것은 오류 조건으로 간주되지 않습니다. 이것은 서비스의 백그라운드 작업의 완료를 나타냅니다. 상태 저장 Reliable Services의 경우에는 서비스가 주 복제본에서 강등되고 다시 주 복제본으로 승격된 경우 `runAsync()`를 다시 호출해야 합니다.
* 일부 예기치 않은 예외가 throw되어 서비스가 `runAsync()`에서 종료된 경우는 실패입니다. 서비스 개체가 종료되고 상태 오류가 보고됩니다.
* 이러한 메서드에서의 반환에는 시간 제한이 없지만, 쓰기 기능이 즉시 손실됩니다. 따라서 모든 실제 작업을 완료할 수 없습니다. 취소 요청을 받는 즉시 최대한 신속하게 반환하는 것이 좋습니다. 서비스가 적절한 시간 내에 이러한 API 호출에 응답하지 않으면 Service Fabric은 서비스를 강제로 종료할 수 있습니다. 이러한 상황은 일반적으로 애플리케이션을 업그레이드하거나 서비스를 삭제할 때만 발생합니다. 이 시간 제한은 기본적으로 15분입니다.
* `onCloseAsync()` 경로에서 실패가 발생하면 `onAbort()`가 호출됩니다. 이 호출은 서비스가 이전에 요구한 리소스를 정리하고 릴리스할 수 있는 마지막 최선의 기회입니다. 이는 일반적으로 노드에서 영구 오류가 감지되거나, 내부 오류로 인해 서비스 패브릭에서 서비스 인스턴스 수명 주기를 안정적으로 관리할 수 없을 때 호출됩니다.
* 상태 저장 서비스 복제본이 역할을 변경할 경우(예: 주 또는 보조) `OnChangeRoleAsync()`가 호출됩니다. 주 복제본에는 쓰기 상태가 지정됩니다(신뢰할 수 있는 컬렉션을 만들고 쓰도록 허용). 보조 복제본에는 읽기 상태가 지정됩니다(신뢰할 수 있는 기존 컬렉션에서 읽기만 가능). 상태 저장 서비스에서 대부분의 작업은 주 복제본에서 수행됩니다. 보조 복제본은 읽기 전용 유효성 검사, 보고서 생성, 데이터 마이닝 또는 다른 읽기 전용 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Reliable Services 소개](service-fabric-reliable-services-introduction.md)
* [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start-java.md)

