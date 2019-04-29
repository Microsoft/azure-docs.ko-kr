---
title: Azure Service Fabric에서 상태 저장 서비스 단위 테스트 | Microsoft Docs
description: Service Fabric 상태 저장 서비스의 단위 테스트 개념 및 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881456"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Service Fabric의 상태 저장 서비스 단위 테스트

이 문서에서는 Service Fabric 상태 저장 서비스의 단위 테스트 개념 및 방법에 대해 알아봅니다. Service Fabric 내의 단위 테스트는 애플리케이션 코드가 여러 다른 컨텍스트에서 능동적으로 실행된다는 사실만으로도 고려할 가치가 있습니다. 이 문서에서는 애플리케이션 코드가 실행될 수 있는 각기 다른 컨텍스트에 포함되도록 하는 데 사용되는 방법을 설명합니다.

## <a name="unit-testing-and-mocking"></a>단위 테스트 및 모의
이 문서 컨텍스트의 단위 테스트는 MSTest 또는 NUnit와 같은 Test Runner 컨텍스트 내에서 실행될 수 있는 자동 테스트입니다. 이 문서 내의 단위 테스트는 데이터베이스 또는 RESTFul API와 같은 원격 리소스에 대해 작업을 수행하지 않습니다. 따라서 이러한 원격 리소스는 모의되어야 합니다. 이 문서 컨텍스트의 모의는 원격 리소스에 대한 반환 값을 가짜로 만들고, 기록하고, 제어합니다.

### <a name="service-fabric-considerations"></a>Service Fabric 고려 사항
Service Fabric 상태 저장 서비스의 단위 테스트를 위해서는 몇 가지 사항이 고려됩니다. 첫째, 서비스 코드는 여러 노드에서 다른 역할로 실행됩니다. 단위 테스트는 각 역할에서 코드를 평가하여 전체 적용 범위를 테스트해야 합니다. 다양한 역할로는 주 복제본, 활성 보조 보제본, 유휴 보조 복제본 및 알 수 없음이 있습니다. 없음 역할의 경우 Service Fabric에서 void 또는 null 서비스로 간주하므로 일반적으로 특별한 적용 범위가 필요하지 않습니다. 둘째, 각 노드는 특정 시점에 해당 역할을 변경합니다. 전체 적용 범위를 테스트하기 위해서는 역할 변경이 발생하고 있는 코드 실행 경로를 테스트해야 합니다.

## <a name="why-unit-test-stateful-services"></a>왜 상태 저장 서비스를 단위 테스트해야 할까요? 
상태 저장 서비스의 단위 테스트는 기본 애플리케이션 또는 도메인별 단위 테스트를 통해 찾지 못할 수 있는 몇 가지 일반적인 실수를 알아내는 데 도움이 될 수 있습니다. 예를 들어, 상태 저장 서비스가 메모리 내 상태인 경우 이러한 유형의 테스트를 사용해서 이러한 메모리 내 상태가 각 복제본에서 동기화된 상태를 유지하는지 확인할 수 있습니다. 또한 이러한 유형의 테스트는 상태 저장 서비스가 Service Fabric 오케스트레이션에 의해 전달된 취소 토큰에 적절히 응답하는지도 확인할 수 있습니다. 취소가 트리거되면 서비스는 장기 실행 및/또는 비동기 작업을 중단해야 합니다.  

## <a name="common-practices"></a>일반적인 방법

다음 섹션에서는 상태 저장 서비스를 단위 테스트하기 위한 가장 일반적인 방법을 설명합니다. 또한 Service Fabric 오케스트레이션 및 상태 관리에 가깝게 조정해야 하는 모의 계층을 추천합니다. 이 기능을 제공하는 모의 라이브러리가 있습니다. 3.3.0 이상부터 [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/)는 권장되는 모의 기능을 제공하고 아래 설명된 방법을 따르는 이러한 단일 라이브러리입니다.

### <a name="arrangement"></a>정렬

#### <a name="use-multiple-service-instances"></a>여러 서비스 인스턴스 사용
단위 테스트는 상태 저장 서비스의 여러 인스턴스를 실행해야 합니다. 이를 통해 Service Fabric이 여러 다른 노드 간에 서비스를 실행하는 여러 복제본을 프로비전하는 경우 클러스터에서 실제로 수행되는 작업이 시뮬레이트됩니다. 그렇지만 이러한 각 인스턴스는 다른 컨텍스트에서 실행됩니다. 테스트를 실행할 때 각 인스턴스는 클러스터에서 예상되는 역할 구성을 사용 하여 준비되어야 합니다. 예를 들어, 이 서비스가 크기가 3인 대상 복제본을 유지해야 할 경우 Service Fabric은 다른 노드에 3개의 복제본을 프로비전합니다. 하나는 주 복제본이고, 다른 2개는 활성 보조 복제본이 됩니다.

대부분의 경우 서비스 실행 경로는 이러한 각 역할에 대해 약간 다릅니다. 예를 들어, 서비스는 활성 보조 복제본의 요청을 수락하지 않아야 할 경우 이 사례를 확인하여 요청이 보조 복제본에서 시도되었음을 나타내는 정보 예외를 다시 throw할 수 있습니다. 여러 인스턴스가 있으면 이러한 상황을 테스트할 수 있습니다.

또한 여러 인스턴스가 있으면 이러한 각 인스턴스의 역할을 전환하면서 역할 변경 시에도 응답이 일관되는지 확인할 수 있습니다.

#### <a name="mock-the-state-manager"></a>상태 관리자 모의
상태 관리자는 원격 리소스로 취급되므로 모의되어야 합니다. 상태 관리자를 모의하는 경우 상태 관리자에 저장되는 내용을 추적하여 읽고 확인할 수 있도록 기본 메모리 내 저장소가 필요합니다. 이 작업을 수행하는 간단한 방법은 각 신뢰할 수 있는 컬렉션 유형의 모의 인스턴스를 만드는 것입니다. 해당 모의 개체 내에서 해당 컬렉션에 대해 수행되는 작업에 가깝게 조정되는 데이터 형식을 사용합니다. 다음은 신뢰할 수 있는 각 컬렉션에 대해 제안되는 몇 가지 데이터 형식입니다.

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>여러 상태 관리자 인스턴스, 단일 저장소
앞서 언급한 것처럼 상태 관리자 및 신뢰할 수 있는 컬렉션은 원격 리소스로 처리되어야 합니다. 따라서 이러한 리소스는 단위 테스트 내에서 모의되어야 합니다. 그러나 상태 저장 서비스의 여러 인스턴스를 실행하는 경우 여러 다른 상태 저장 서비스 인스턴스에서 모의된 각 상태 관리자를 동기화 상태로 유지하는 일은 어려울 수 있습니다. 상태 저장 서비스가 클러스터에서 실행되는 경우 Service Fabric은 각 보조 복제본의 상태 관리자를 주 복제본과 일관되게 유지하려고 합니다. 따라서 역할 변경을 시뮬레이트할 수 있도록 테스트도 동일하게 진행되어야 합니다.

이 동기화를 달성하는 간단한 방법은 신뢰할 수 있는 각 컬렉션에 기록된 데이터를 저장하는 기본 개체에 대해 단일 항목 패턴을 사용하는 것입니다. 예를 들어, 상태 저장 서비스가 `IReliableDictionary<string, string>`를 사용할 경우 모의 상태 관리자는 `IReliableDictionary<string, string>`의 모의 항목을 사용해야 합니다. 해당 모의 항목은 `ConcurrentDictionary<string, string>`를 사용하여 기록된 키/값 쌍을 추적할 수 있습니다. `ConcurrentDictionary<string, string>`는 서비스에 전달되는 상태 관리자의 모든 인스턴스에서 사용되는 단일 항목이어야 합니다.

#### <a name="keep-track-of-cancellation-tokens"></a>취소 토큰 추적
취소 토큰은 상태 관리 서비스에서 중요하면서도 일반적으로 간과되는 측면입니다. Service Fabric이 상태 저장 서비스의 주 복제본을 시작할 때 취소 토큰이 제공됩니다. 이 취소 토큰은 서비스가 제거되거나 다른 역할로 강등될 때 신호로 알리도록 디자인되었습니다. 상태 저장 서비스는 Service Fabric이 역할 변경 워크플로를 완료할 수 있도록 모든 장기 실행 또는 비동기 작업을 중지해야 합니다.

단위 테스트를 실행할 때, RunAsync, ChangeRoleAsync, OpenAsync 및 CloseAsync에 제공되는 모든 취소 토큰은 테스트 실행 동안 보류되어야 합니다. 이러한 토큰을 보류하면 테스트 중에 서비스 종료 또는 강등을 시뮬레이트할 수 있고 서비스가 적절히 응답하는지 확인할 수 있습니다.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>모의 원격 리소스로 포괄적인 테스트 진행
단위 테스트는 상태 저장 서비스의 상태를 수정할 수 있는 애플리케이션 코드 양만큼 실행해야 합니다. 테스트는 좀 더 포괄적으로 진행하는 것이 좋습니다. 존재하는 유일한 모의 항목은 원격 리소스 상호 작용을 기록, 시뮬레이트 및/또는 확인하는 것입니다. 여기에는 상태 관리자 및 신뢰할 수 있는 컬렉션과의 상호 작용이 포함됩니다. 다음 코드 조각은 포괄적인 테스트를 보여주는 오이 피클 예제입니다.

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

이 테스트는 한 복제본에서 캡처한 데이터를 주 복제본으로 승격된 보조 복제본에서 사용할 수 있도록 어설션합니다. 신뢰할 수 있는 컬렉션이 직원 데이터에 대한 백업 저장소라고 가정합니다. 또한 이 테스트로 찾아낼 수 있는 잠재적인 오류는 프로그램 코드가 새 직원을 저장하기 위해 트랜잭션에 대해 `CommitAsync`를 실행하지 않는지 여부입니다. 이러한 경우 직원을 가져오기 위한 두 번째 요청은 첫 번째 요청에 추가해서 직원을 반환하지 않습니다.

### <a name="acting"></a>작업
#### <a name="mimic-service-fabric-replica-orchestration"></a>Service Fabric 복제본 오케스트레이션 모방
여러 서비스 인스턴스를 관리하는 경우 테스트는 Service Fabric 오케스트레이션과 동일한 방식으로 이러한 서비스를 초기화하고 분해합니다. 예를 들어, 새 주 복제본에서 서비스가 생성되면 Service Fabric은 CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync 및 RunAsync를 호출합니다. 수명 주기 이벤트는 다음 문서에 설명되어 있습니다.

- [상태 저장 서비스 시작](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [상태 저장 서비스 종료](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [상태 저장 서비스 주 교환](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>복제본 역할 변경 실행
단위 테스트는 Service Fabric 오케스트레이션과 동일한 방식으로 서비스 인스턴스의 역할을 변경해야 합니다. 역할 상태 컴퓨터는 다음 문서에 설명되어 있습니다.

[복제본 역할 상태 컴퓨터](service-fabric-concepts-replica-lifecycle.md#replica-role)

역할 변경을 시뮬레이트하는 작업은 테스트에서 좀 더 중요한 측면 중 하나로, 복제본의 상태가 서로 일관되지 않는 문제를 찾아낼 수 있습니다. 일관성 없는 복제본 상태는 메모리 내 상태를 정적 또는 클래스 수준 인스턴스 변수에 저장하기 때문에 발생할 수 있습니다. 이에 대한 예제로는 취소 토큰, 열거형 및 구성 개체/값이 있습니다. 또한 이러한 시뮬레이트를 통해 서비스는 RunAsync 동안 제공되는 취소 토큰을 유지하여 역할이 변경되도록 할 수 있습니다. 역할 변경을 시뮬레이트하면 RunAsync가 여러 번 호출되도록 코드가 작성되지 않은 경우에 발생할 수 있는 문제도 확인될 수 있습니다.

#### <a name="cancel-cancellation-tokens"></a>취소 토큰 취소
RunAsync에 제공된 취소 토큰이 취소되는 경우 단위 테스트가 존재해야 합니다. 이 경우 해당 테스트를 통해 서비스가 정상적으로 종료되는지 확인할 수 있습니다. 이러한 종료 동안 모든 장기 실행 또는 비동기 작업이 중지되어야 합니다. 서비스에 존재할 수 있는 장기 실행 프로세스의 예로 신뢰할 수 있는 큐에서 메시지를 수신하는 경우를 들 수 있습니다. 이러한 프로세스는 RunAsync 또는 백그라운드 스레드 바로 안에 존재할 수 있습니다. 구현에는 이 취소 토큰이 취소될 경우에 작업을 종료하기 위한 논리가 포함됩니다.

상태 저장 서비스가 주 복제본에만 존재하는 캐시 또는 메모리 내 상태를 사용하는 경우 지금 처리해야 합니다. 그러면 노드가 나중에 다시 주 복제본이 될 경우 이 상태가 일관되게 유지될 수 있습니다. 취소 테스트를 사용하면 테스트 중에 이 상태가 적절히 처리되었는지 확인할 수 있습니다.

#### <a name="execute-requests-against-multiple-replicas"></a>여러 복제본에 대해 요청 실행
어설션 테스트는 다른 복제본에 대해 동일한 요청을 실행해야 합니다. 역할 변경과 함께 사용할 경우 일관성 문제가 확인될 수 있습니다. 예제 테스트는 다음 단계를 수행할 수 있습니다.
1. 현재 주 복제본에 대해 쓰기 요청을 실행합니다.
2. 현재 주 복제본에 대해 1단계에서 쓴 데이터를 반환하는 읽기 요청을 실행합니다.
3. 보조 복제본을 주 복제본으로 승격합니다. 이를 통해 현재 주 복제본이 보조 복제본으로 강등됩니다.
4. 새 보조 복제본에 대해 2단계의 동일한 읽기 요청을 실행합니다.

마지막 단계에서 이 테스트는 반환되는 데이터가 일관되는지 어설션할 수 있습니다. 이를 통해 확인될 수 있는 잠재적인 문제는 서비스에서 반환하는 데이터가 메모리 내에 있지만 궁극적으로 신뢰할 수 있는 컬렉션을 통해 지원될 수 있다는 것입니다. 이러한 메모리 내 데이터는 신뢰할 수 있는 컬렉션에 존재하는 데이터와 제대로 동기화되지 않을 수 있습니다.

메모리 내 데이터는 일반적으로 신뢰할 수 있는 컬렉션에 있는 데이터의 보조 인덱스 또는 집계를 만드는 데 사용됩니다.

### <a name="asserting"></a>어설션
#### <a name="ensure-responses-match-across-replicas"></a>복제본 간에 응답이 일치하는지 확인
단위 테스트는 주 복제본으로 전환된 후에 여러 복제본에서 지정된 요청에 대한 응답이 일관되는지 어설션해야 합니다. 이렇게 하면 응답에 제공된 데이터가 신뢰할 수 있는 컬렉션에서 지원되지 않거나, 해당 데이터를 복제본 간에 동기화하는 메커니즘 없이 메모리 내에 유지되는 경우 잠재적인 문제가 표면화될 수 있습니다. 이를 통해 서비스는 Service Fabric이 균형을 다시 맞추거나 새로운 주 복제본으로 장애 조치(Failover)된 후에 일관된 응답을 다시 보낼 수 있게 됩니다.

#### <a name="verify-service-respects-cancellation"></a>서비스가 취소를 유지하는지 확인
취소 토큰이 취소될 때 종료되어야 하는 장기 실행 또는 비동기 프로세스는 취소 후에 실제로 종료되었는지 확인되어야 합니다. 이렇게 하면 복제본 역할이 변경되더라도, 주 복제본이 아닌 복제본에서 계속 실행되도록 의도하지 않은 프로세스는 전환이 완료되기 전에 중지되게 됩니다. 또한 이를 통해 이러한 프로세스가 Service Fabric의 역할 변경 또는 종료 요청이 완료되지 못하도록 하는 문제도 확인될 수 있습니다.

#### <a name="verify-which-replicas-should-serve-requests"></a>요청을 처리해야 하는 복제본 확인
테스트는 요청이 주 복제본이 아닌 복제본으로 라우트될 경우 예상되는 동작을 어설션해야 합니다. Service Fabric은 보조 복제본이 요청을 처리하도록 하는 기능을 제공합니다. 그러나 신뢰할 수 있는 컬렉션에 대한 쓰기는 주 복제본에서만 발생할 수 있습니다. 애플리케이션에서 주 복제본만 요청을 처리하도록 지정되어 있거나 보조 복제본을 통해서는 요청 일부만 처리될 수 있는 경우, 테스트는 긍정 및 부정 사례 모두에 대해 예상되는 동작을 어설션해야 합니다. 부정적인 경우는 요청을 처리하면 안 되는 복제본으로 요청이 라우트되는 경우이고, 긍정적인 경우는 그 반대입니다.

## <a name="next-steps"></a>다음 단계
[상태 저장 서비스의 단위 테스트를 수행하는](service-fabric-how-to-unit-test-stateful-services.md) 방법을 알아봅니다.