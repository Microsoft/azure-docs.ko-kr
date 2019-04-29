---
title: Azure Service Fabric Reliable Services의 수명 주기 개요 | Microsoft Docs
description: Service Fabric Reliable Services의 다른 수명 주기 이벤트에 대해 알아보기
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723569"
---
# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 수명 주기 개요
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux에서 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Azure Service Fabric Reliable Services의 수명 주기를 고려할 경우 수명 주기에 대한 기본 사항이 가장 중요합니다. 일반적으로 수명 주기는 다음과 같습니다.

- 시작 중:
  - 서비스가 생성됩니다.
  - 서비스는 0개 이상의 수신기를 생성하고 반환할 수 있습니다.
  - 반환된 모든 수신기가 열리면 서비스와의 통신이 가능합니다.
  - 서비스의 **RunAsync** 메서드를 호출하면 서비스가 장기 실행 중인 작업 또는 백그라운드 작업을 수행할 수 있습니다.
- 종료 중:
  - **RunAsync**에 전달된 취소 토큰이 취소되고 수신기 닫힙니다.
  - 수신기가 닫히면 서비스 개체 자체는 소멸됩니다.

이러한 이벤트의 정확한 순서에 대한 세부 정보가 있습니다. 이벤트의 순서는 Reliable Service가 상태 비저장인지 또는 상태 저장인지에 따라 약간 변경될 수 있습니다. 또한 상태 저장 서비스의 경우 주 복제본 교환 시나리오를 처리해야 합니다. 이 과정 중에 서비스를 종료하지 않고 주 복제본의 역할을 다른 복제본으로 전송하거나 복구합니다. 마지막으로 오류 또는 실패 조건에 대해 고려해야 합니다.

## <a name="stateless-service-startup"></a>상태 비저장 서비스 시작
상태 비저장 서비스의 수명 주기는 간단합니다. 이벤트의 순서는 다음과 같습니다.

1. 서비스가 생성됩니다.
2. 그런 후 다음 두 가지 작업이 동시에 수행됩니다.
    - `StatelessService.CreateServiceInstanceListeners()`가 호출되고 반환된 모든 수신기가 열립니다. `ICommunicationListener.OpenAsync()`가 각 수신기에서 호출됩니다.
    - 서비스의 `StatelessService.RunAsync()` 메서드가 호출됩니다.
3. 있는 경우 서비스의 `StatelessService.OnOpenAsync()` 메서드가 호출됩니다. 이 호출은 일반적이지 않은 재정의이지만 사용 가능합니다. 이때 확장된 서비스 초기화 작업을 시작할 수 있습니다.

수신기 및 **RunAsync**를 만들고 여는 호출 간에 순서가 없다는 점에 유의합니다. **RunAsync**가 시작되기 전에 수신기가 열릴 수 있습니다. 마찬가지로 통신 수신기를 열고 생성하기 전에 **RunAsync**를 호출할 수 있습니다. 동기화가 필요한 경우 구현 작업에 연습으로 남겨 둡니다. 다음은 몇 가지 일반적인 해결 방법입니다.

  - 경우에 따라 다른 정보를 만들거나 수행할 때까지 수신기는 작동할 수 없습니다. 상태 비저장 서비스의 경우 해당 작업은 일반적으로 다음과 같은 다른 위치에서 수행할 수 있습니다. 
    - 서비스의 생성자에서
    - `CreateServiceInstanceListeners()` 호출 중에
    - 수신기 자체 생성의 일부로서
  - 경우에 따라 **RunAsync**의 코드는 수신기 열릴 때까지 시작하지 않습니다. 이 경우 추가 조정이 필요합니다. 한 가지 일반적인 해결책은 완료되었음을 나타내는 플래그가 수신기 내에 표시된다는 것입니다. 이 플래그는 실제 작업을 계속하기 전에 **RunAsync**에서 확인됩니다.

## <a name="stateless-service-shutdown"></a>상태 비저장 서비스 종료
상태 비저장 서비스를 종료할 경우 동일한 패턴이 역방향으로 다음과 같이 따라옵니다.

1. 병렬로
    - 열려 있는 수신기가 모두 닫힙니다. `ICommunicationListener.CloseAsync()`가 각 수신기에서 호출됩니다.
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고, 호출되는 경우 토큰의 `ThrowIfCancellationRequested` 메서드에서 `OperationCanceledException`이 발생됩니다.
2. `CloseAsync()`가 각 수신기에서 완료되고 `RunAsync()`도 완료되면 서비스의 `StatelessService.OnCloseAsync()` 메서드가 호출됩니다(있는 경우).  상태 비저장 서비스 인스턴스가 정상적으로 종료되려고 할 때 OnCloseAsync가 호출됩니다. 이는 서비스의 코드를 업그레이드할 때, 로드 균형 조정으로 인해 서비스 인스턴스가 이동할 때 또는 일시적인 오류가 감지되었을 때 발생할 수 있습니다. `StatelessService.OnCloseAsync()` 재정의는 일반적이지 않지만 리소스를 안전하게 닫거나 백그라운드 프로세스를 중지하거나 외부 상태 저장을 완료하거나 기존 연결을 종료하는 데 사용할 수 있습니다.
3. `StatelessService.OnCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-startup"></a>상태 저장 서비스 시작
상태 저장 서비스는 몇 가지를 변경한 상태 비저장 서비스 패턴과 비슷합니다. 상태 저장 서비스를 시작하면 이벤트의 순서는 다음과 같습니다.

1. 서비스가 생성됩니다.
2. `StatefulServiceBase.OnOpenAsync()`을 호출합니다. 이 호출은 서비스에서 드물게 재정의됩니다.
3. 다음과 같은 작업이 동시에 수행됩니다.
    - `StatefulServiceBase.CreateServiceReplicaListeners()`가 호출됩니다. 
      - 서비스가 주 서비스인 경우 반환된 모든 수신기가 열립니다. `ICommunicationListener.OpenAsync()`가 각 수신기에서 호출됩니다.
      - 서비스가 보조 서비스인 경우 `ListenOnSecondary = true`로 표시된 수신기만 열립니다. 보조 복제본에서 수신기가 열려 있는 경우는 일반적이지 않습니다.
    - 서비스가 현재 주 서비스인 경우 서비스의 `StatefulServiceBase.RunAsync()` 메서드가 호출됩니다.
4. 모든 복제본 수신기의 `OpenAsync()` 호출이 완료되고 `RunAsync()`가 호출되면 `StatefulServiceBase.OnChangeRoleAsync()`가 호출됩니다. 이 호출은 서비스에서 드물게 재정의됩니다.

상태 비저장 서비스와 마찬가지로 수신기가 만들어지고 열리는 순서와 **RunAsync**가 호출되는 순서 사이에는 조정되지 않습니다. 조정이 필요한 경우 해결 방법은 거의 동일합니다. 상태 저장 서비스에 대한 한 가지 추가 사례가 있습니다. 즉 통신 수신기에 도달한 호출에는 일부 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md) 내에 보관된 정보가 필요합니다.

   > [!NOTE]  
   > 신뢰할 수 있는 컬렉션을 읽기 가능하거나 쓰기 가능하게 되고 **RunAsync**가 시작하기 전에 통신 수신기를 열 수 있기 때문에 일부 추가 조정이 필요합니다. 가장 간단하고 일반적인 솔루션은 통신 수신기에서 클라이언트가 사용하는 오류 코드를 반환하여 요청을 다시 시도하는 것입니다.

## <a name="stateful-service-shutdown"></a>상태 저장 서비스 종료
상태 비저장 서비스와 마찬가지로 종료 중 수명 주기 이벤트는 시작 시와 동일하지만 역방향으로 이뤄집니다. 상태 저장 서비스가 종료되면 다음과 같은 이벤트가 발생합니다.

1. 병렬로
    - 열려 있는 수신기가 모두 닫힙니다. `ICommunicationListener.CloseAsync()`가 각 수신기에서 호출됩니다.
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고, 호출되는 경우 토큰의 `ThrowIfCancellationRequested` 메서드에서 `OperationCanceledException`이 발생됩니다.
2. `CloseAsync()`가 각 수신기에서 완료되고 `RunAsync()`도 완료되면 서비스의 `StatefulServiceBase.OnChangeRoleAsync()`가 호출됩니다(있는 경우). 이 호출은 서비스에서 드물게 재정의됩니다.

   > [!NOTE]  
   > **RunAsync**가 완료될 때까지 기다려야 하는 경우는 이 복제본이 주 복제본인 경우 뿐입니다.

3. `StatefulServiceBase.OnChangeRoleAsync()` 메서드가 완료되면 `StatefulServiceBase.OnCloseAsync()` 메서드가 호출됩니다. 이 호출은 일반적이지 않은 재정의이지만 사용 가능합니다.
3. `StatefulServiceBase.OnCloseAsync()`이 완료되면 서비스 개체는 소멸됩니다.

## <a name="stateful-service-primary-swaps"></a>상태 저장 서비스 주 교환
상태 저장 서비스가 실행되는 동안 해당 상태 저장 서비스의 주 복제본은 해당 통신 수신기를 열고 해당 **RunAsync** 메서드를 호출합니다. 보조 복제본을 생성하지만 더 이상 호출하지 않습니다. 장애 또는 클러스터 균형 조정 최적화의 결과로 상태 저장 서비스가 실행되는 동안 현재 주 복제본인 복제본을 변경할 수 있습니다. 복제본이 확인할 수 있는 수명 주기 이벤트의 측면에서 무슨 의미인가요? 상태 저장 복제본이 확인하는 동작은 강등되는 복제본인지 승격되는 복제본인지에 따라 달라 집니다.

### <a name="for-the-primary-thats-demoted"></a>강등되는 주 복제본의 경우
강등되는 주 복제본의 경우 Service Fabric은 이 복제본에서 메시지 처리를 중지하고 수행 중인 모든 백그라운드 작업을 종료하도록 합니다. 결과적으로 이 단계는 서비스가 종료될 경우와 유사합니다. 한 가지 차이점은 서비스가 보조 복제본으로 그대로 남아 있으므로 소멸되거나 종료되지 않는다는 점입니다. 다음 API가 호출됩니다.

1. 병렬로
    - 열려 있는 수신기가 모두 닫힙니다. `ICommunicationListener.CloseAsync()`가 각 수신기에서 호출됩니다.
    - `RunAsync()`에 전달된 취소 토큰이 취소됩니다. 취소 토큰의 `IsCancellationRequested` 속성을 확인하면 true를 반환하고, 호출되는 경우 토큰의 `ThrowIfCancellationRequested` 메서드에서 `OperationCanceledException`이 발생됩니다.
2. `CloseAsync()`가 각 수신기에서 완료되고 `RunAsync()`도 완료되면 서비스의 `StatefulServiceBase.OnChangeRoleAsync()`가 호출됩니다(있는 경우). 이 호출은 서비스에서 드물게 재정의됩니다.

### <a name="for-the-secondary-thats-promoted"></a>승격되는 보조 복제본의 경우
마찬가지로 Service Fabric에서 통신 중에 메시지에 대한 수신 대기를 시작하고, 관심 있는 모든 백그라운드 작업을 시작하기 위해 승격되는 보조 복제본이 필요합니다. 결과적으로 이 프로세스는 복제본 자체가 이미 존재한다는 점을 제외하면 서비스를 만들 때와 유사합니다. 다음 API가 호출됩니다.

1. 병렬로
    - `StatefulServiceBase.CreateServiceReplicaListeners()`가 호출되고 반환된 모든 수신기가 열립니다. `ICommunicationListener.OpenAsync()`가 각 수신기에서 호출됩니다.
    - 서비스의 `StatefulServiceBase.RunAsync()` 메서드가 호출됩니다.
2. 모든 복제본 수신기의 `OpenAsync()` 호출이 완료되고 `RunAsync()`가 호출되면 `StatefulServiceBase.OnChangeRoleAsync()`가 호출됩니다. 이 호출은 서비스에서 드물게 재정의됩니다.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>상태 저장 서비스 종료 및 기본 수준 내리기 동안의 일반적인 문제
Service Fabric에서는 다양한 이유로 상태 저장 서비스에 대한 주 복제본을 변경합니다. 가장 일반적인 것은 [클러스터 재분산](service-fabric-cluster-resource-manager-balancing.md) 및 [애플리케이션 업그레이드](service-fabric-application-upgrade.md)입니다. 이러한 작업 동안(서비스가 삭제되었는지 확인하는 경우처럼 정상적인 서비스 종료 동안에도) 서비스에서 `CancellationToken`을 준수하는 것이 중요합니다. 

취소를 완전히 처리하지 않는 서비스에는 몇 가지 문제가 발생합니다. Service Fabric에서 서비스가 정상적으로 중지되기를 기다리기 때문에 이러한 작업이 느려질 수 있습니다. 이로 인해 궁극적으로 시간이 초과되고 롤백하게 되는 실패한 업그레이드로 이어질 수 있습니다. 취소 토큰을 사용하지 않으면 클러스터가 분산되지 않을 수도 있습니다. 클러스터가 핫 노드가 되어 분산되지 않게 되지만 다른 곳으로 이동하는 데 시간이 너무 오래 걸리므로 서비스를 다시 분산할 수 없습니다. 

서비스가 상태 저장 서비스이므로 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)을 사용하고 있을 가능성이 큽니다. Service Fabric에서 주 복제본이 강등될 때 가장 먼저 발생하는 상황 중 하나는 기본 상태에 대한 쓰기 액세스가 철회된다는 것입니다. 이로 인해 서비스 수명 주기에 영향을 줄 수 있는 두 번째 문제 집합이 발생합니다. 컬렉션에서는 타이밍 및 복제본이 이동되거나 종료되는지 여부에 따라 예외를 반환합니다. 이러한 예외는 올바르게 처리되어야 합니다. Service Fabric에서 발생한(throw) 예외는 영구[(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) 및 일시적[(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) 범주로 분류됩니다. 영구 예외는 기록되고 발생되어야 하지만, 일시적 예외는 몇 가지 다시 시도 논리에 따라 다시 시도될 수 있습니다.

서비스 수명 주기 이벤트와 함께 `ReliableCollections` 사용으로 인한 예외 처리는 신뢰할 수 있는 서비스의 테스트 및 유효성 검사에 대한 중요한 부분입니다. 권장 사항은 항상 프로덕션에 배포하기 전에 업그레이드 및 [비정상 상황 테스트(chaos testing)](service-fabric-controlled-chaos.md)를 수행하는 동안 로드 상태에서 서비스를 실행하는 것입니다. 이러한 기본 단계를 통해 서비스가 올바르게 구현되고 수명 주기 이벤트가 올바르게 처리되도록 할 수 있습니다.


## <a name="notes-on-the-service-lifecycle"></a>서비스 수명 주기에 대한 참고 사항
  - `RunAsync()` 메서드 및 `CreateServiceReplicaListeners/CreateServiceInstanceListeners` 호출은 모두 선택 사항입니다. 서비스에는 이러한 항목이 있거나 없을 수도 있습니다. 예를 들어, 서비스가 모든 작업을 사용자 호출에 대한 응답으로 수행할 경우 `RunAsync()`을(를) 구현할 필요가 없습니다. 통신 수신기 및 해당 관련 코드만이 필요합니다. 마찬가지로 통신 수신기를 만들고 반환하는 작업은 선택적이며 서비스가 백그라운드 작업을 수행해야 하므로 `RunAsync()`을 구현해야 합니다.
  - 서비스가 `RunAsync()`을 성공적으로 완료하고 거기에서 반환하는 작업이 유효합니다. 완료는 실패 조건이 아닙니다. `RunAsync()`를 완료하면 서비스의 백그라운드 작업이 완료되었음을 나타냅니다. 상태 저장 신뢰할 수 있는 서비스의 경우 복제본이 주에서 보조로 강등된 다음 다시 주로 승격되면 `RunAsync()`가 다시 호출됩니다.
  - 예기치 않은 예외가 발생(throw)되어 서비스가 `RunAsync()`에서 종료되는 경우 실패로 간주됩니다. 서비스 개체가 종료되고 상태 오류가 보고됩니다.
  - 이러한 메서드에서 반환에는 시간 제한이 없으며 신뢰할 수 있는 컬렉션에 작성할 수 있는 기능이 즉시 손실되고 모든 실제 작업을 완료할 수 없습니다. 취소 요청을 받는 즉시 최대한 신속하게 반환하는 것이 좋습니다. 서비스가 적절한 시간 내에 이러한 API 호출에 응답하지 않으면 Service Fabric은 서비스를 강제로 종료할 수 있습니다. 이러한 상황은 일반적으로 애플리케이션을 업그레이드하거나 서비스를 삭제할 때 발생합니다. 이 시간 제한은 기본적으로 15분입니다.
  - `OnCloseAsync()` 경로의 오류로 인해 `OnAbort()`가 호출되고 이것이 서비스에서 요구하는 리소스를 정리하고 릴리스할 수 있는 마지막 방법입니다. 이는 일반적으로 노드에서 영구 오류가 감지되거나, 내부 오류로 인해 서비스 패브릭에서 서비스 인스턴스 수명 주기를 안정적으로 관리할 수 없을 때 호출됩니다.
  - 상태 저장 서비스 복제본이 역할을 변경할 경우(예: 주 또는 보조) `OnChangeRoleAsync()`가 호출됩니다. 주 복제본에는 쓰기 상태가 지정됩니다(신뢰할 수 있는 컬렉션을 만들고 쓰도록 허용). 보조 복제본에는 읽기 상태가 지정됩니다(신뢰할 수 있는 기존 컬렉션에서 읽기만 가능). 상태 저장 서비스에서 대부분의 작업은 주 복제본에서 수행됩니다. 보조 복제본은 읽기 전용 유효성 검사, 보고서 생성, 데이터 마이닝 또는 다른 읽기 전용 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Reliable Services 소개](service-fabric-reliable-services-introduction.md)
- [Reliable Services 빠른 시작](service-fabric-reliable-services-quick-start.md)
- [복제본 및 인스턴스](service-fabric-concepts-replica-lifecycle.md)
