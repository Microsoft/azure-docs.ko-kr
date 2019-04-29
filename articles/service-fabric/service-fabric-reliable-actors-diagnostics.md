---
title: 행위자 진단 및 모니터링 | Microsoft Docs
description: 이 문서에서는 서비스 패브릭 Reliable Actors 런타임에서 내보낸 이벤트 및 성능 카운터를 포함하여 패브릭 행위자 런타임에 있는 진단 및 성능 모니터링 기능을 설명합니다.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: chackdan
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5f573db887b3acc2c4a668a8c19c7f8e3cb25019
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726573"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Reliable Actors에 대한 진단 및 성능 모니터링
Reliable Actors 런타임에서는 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트 및 [성능 카운터](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)를 내보냅니다. 이러한 정보는 런타임이 어떻게 작동하고 있는지 파악하여 문제를 해결하고 성능을 모니터링하는 데 도움이 됩니다.

## <a name="eventsource-events"></a>EventSource 이벤트
Reliable Actors 런타임의 EventSource 공급자 이름은 "Microsoft-ServiceFabric-Actors"입니다. 이 이벤트 원본의 이벤트는 행위자 애플리케이션이 [Visual Studio에서 디버깅](service-fabric-debugging-your-application.md)될 때 [진단 이벤트](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) 창에 표시됩니다.

EventSource 이벤트를 수집하거나 보는 데 도움이 되는 도구 및 기술의 예제에는 [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [의미 중심 로깅](https://msdn.microsoft.com/library/dn774980.aspx) 및 [Microsoft TraceEvent 라이브러리](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)가 있습니다.

### <a name="keywords"></a>키워드
신뢰할 수 있는 행위자 EventSource에 속하는 모든 이벤트는 하나 이상의 키워드와 연결되어 있습니다. 이를 통해 수집된 이벤트를 필터링할 수 있습니다. 다음과 같은 키워드 비트가 정의되어 있습니다.

| Bit | 설명 |
| --- | --- |
| 0x1 |패브릭 행위자 런타임의 작업을 요약하는 중요한 이벤트의 집합. |
| 0x2 |행위자 메서드 호출을 설명하는 이벤트의 집합. 자세한 내용은 [행위자에 대한 소개 항목](service-fabric-reliable-actors-introduction.md)을 참조하세요. |
| 0x4 |행위자 상태와 관련된 이벤트의 집합. 자세한 내용은 [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)에 대한 항목을 참조하세요. |
| 0x8 |행위자의 턴 기반 동시성과 관련된 이벤트의 집합. 자세한 내용은 [동시성](service-fabric-reliable-actors-introduction.md#concurrency)에 대한 항목을 참조하세요. |

## <a name="performance-counters"></a>성능 카운터
신뢰할 수 있는 행위자 런타임은 다음과 같은 성능 카운터 범주를 정의합니다.

| Category | 설명 |
| --- | --- |
| 서비스 패브릭 행위자 |Azure 서비스 패브릭 행위자 전용 카운터. 예: 행위자 상태를 저장하는 데 걸리는 시간 |
| 서비스 패브릭 행위자 메서드 |서비스 패브릭 행위자에 의해 구현되는 메서드 전용 카운터. 예: 행위자 메서드가 호출되는 빈도 |

위의 각 범주마다 하나 이상의 카운터가 있습니다.

Windows 운영 체제에서 기본적으로 사용할 수 있는 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 애플리케이션을 사용하면 성능 카운터 데이터를 수집하고 볼 수 있습니다. [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 은 성능 카운터 데이터를 수집하여 Azure 테이블에 업로드하기 위한 또 다른 옵션입니다.

### <a name="performance-counter-instance-names"></a>성능 카운터 인스턴스 이름
많은 수의 행위자 서비스 또는 행위자 서비스 파티션이 있는 클러스터에는 행위자 성능 카운터 인스턴스 수가 많습니다. 성능 카운터 인스턴스 이름은 성능 카운터 인스턴스가 연결된 특정 [파티션](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) 및 행위자 메서드(있는 경우)를 식별하는 데 도움이 될 수 있습니다.

#### <a name="service-fabric-actor-category"></a>서비스 패브릭 행위자 범주
`Service Fabric Actor`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* - 성능 카운터 인스턴스와 연결된 서비스 패브릭 파티션 ID의 문자열 표현. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 메서드를 통해 생성됩니다.

*ActorRuntimeInternalID* - 내부 용도에 맞게 패브릭 행위자 런타임에 의해 생성되는 64비트 정수의 문자열 표현. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

다음은 `Service Fabric Actor` 범주에 속하는 카운터에 대한 카운터 인스턴스 이름의 예입니다.

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

위의 예에서 `2740af29-78aa-44bc-a20b-7e60fb783264`는 서비스 패브릭 파티션 ID의 문자열 표현이고 `635650083799324046`은 런타임의 내부 용도에 맞게 생성되는 64비트 ID입니다.

#### <a name="service-fabric-actor-method-category"></a>서비스 패브릭 행위자 메서드 범주
`Service Fabric Actor Method`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* - 성능 카운터 인스턴스가 연결되어 있는 행위자 메서드의 이름. 메서드 이름의 형식은 Windows에서 성능 카운터 인스턴스 이름의 최대 길이에 대한 제약 조건을 사용하여 이름의 가독성을 조정하는 패브릭 행위자 런타임의 일부 논리에 따라 결정됩니다.

*ActorsRuntimeMethodId* - 내부 용도에 맞게 패브릭 행위자 런타임에 의해 생성되는 32비트 정수의 문자열 표현. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

*ServiceFabricPartitionID* - 성능 카운터 인스턴스와 연결된 서비스 패브릭 파티션 ID의 문자열 표현. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 메서드를 통해 생성됩니다.

*ActorRuntimeInternalID* - 내부 용도에 맞게 패브릭 행위자 런타임에 의해 생성되는 64비트 정수의 문자열 표현. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

다음은 `Service Fabric Actor Method` 범주에 속하는 카운터에 대한 카운터 인스턴스 이름의 예입니다.

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

위의 예에서 `ivoicemailboxactor.leavemessageasync`는 메서드 이름, `2`는 런타임의 내부 용도에 맞게 생성된 32비트 ID, `89383d32-e57e-4a9b-a6ad-57c6792aa521`은 서비스 패브릭 파티션 ID의 문자열 표현, `635650083804480486`은 런타임의 내부 용도에 맞게 생성된 64비트 ID입니다.

## <a name="list-of-events-and-performance-counters"></a>이벤트 및 성능 카운터 목록
### <a name="actor-method-events-and-performance-counters"></a>행위자 메서드 이벤트 및 성능 카운터
Reliable Actors 런타임에서는 다음과 같은 [행위자 메서드](service-fabric-reliable-actors-introduction.md)관련 이벤트를 내보냅니다.

| 이벤트 이름 | 이벤트 ID | Level | 키워드 | 설명 |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |자세한 정보 표시 |0x2 |행위자 런타임이 행위자 메서드를 호출하려고 합니다. |
| ActorMethodStop |8 |자세한 정보 표시 |0x2 |행위자 메서드가 실행을 완료했습니다. 즉, 해당 행위자 메서드에 대한 런타임의 비동기 호출이 반환되었고 해당 행위자 메서드에서 반환한 작업이 완료되었습니다. |
| ActorMethodThrewException |9 |Warning |0x3 |행위자 메서드를 실행하는 동안 또는 행위자 메서드에 대한 런타임의 비동기 호출이나 행위자 메서드에서 반환한 작업의 실행 중에 예외가 발생했습니다. 이 이벤트는 행위자 코드에 일종의 오류가 있는 것으로, 조사가 필요합니다. |

신뢰할 수 있는 행위자 런타임은 행위자 메서드의 실행과 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| 서비스 패브릭 행위자 메서드 |초당 호출 수 |초당 행위자 서비스 메서드가 호출되는 횟수 |
| 서비스 패브릭 행위자 메서드 |호출당 평균 시간(밀리초) |행위자 서비스 메서드를 실행하는 데 걸린 시간(밀리초) |
| 서비스 패브릭 행위자 메서드 |초당 발생한 예외 수 |초당 행위자 서비스 메서드에서 예외가 발생한 횟수 |

### <a name="concurrency-events-and-performance-counters"></a>동시 이벤트 및 성능 카운터
Reliable Actors 런타임에서는 다음과 같은 [동시성](service-fabric-reliable-actors-introduction.md#concurrency)관련 이벤트를 내보냅니다.

| 이벤트 이름 | 이벤트 ID | Level | 키워드 | 설명 |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |자세한 정보 표시 |0x8 |이 이벤트는 행위자에서 각 턴의 시작 부분에 기록됩니다. 턴 기반 동시성을 적용하는 행위자별 잠금을 획득하기 위해 대기 중인 보류된 행위자 호출 수가 포함되어 있습니다. |

신뢰할 수 있는 행위자 런타임은 동시성과 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| 서비스 패브릭 행위자 |행위자 잠금을 기다리는 행위자 호출 수 |턴 기반 동시성을 적용하는 행위자별 잠금을 획득하기 위해 대기 중인 보류된 행위자 호출 수. |
| 서비스 패브릭 행위자 |잠금 대기당 평균 시간(밀리초) |턴 기반 동시성을 적용하는 행위자별 잠금을 획득하기 위해 소요된 시간(밀리초). |
| 서비스 패브릭 행위자 |평균 밀리초 행위자 잠금 보유됨 |행위자별 잠금이 보유되는 시간(밀리초) |

### <a name="actor-state-management-events-and-performance-counters"></a>행위자 상태 관리 이벤트 및 성능 카운터
Reliable Actors 런타임에서는 다음과 같은 [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)관련 이벤트를 내보냅니다.

| 이벤트 이름 | 이벤트 ID | Level | 키워드 | 설명 |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |자세한 정보 표시 |0x4 |행위자 런타임에서 행위자 상태를 저장하려고 합니다. |
| ActorSaveStateStop |11 |자세한 정보 표시 |0x4 |행위자 런타임에서 행위자 상태 저장을 끝마쳤습니다. |

신뢰할 수 있는 행위자 런타임은 행위자 상태 관리와 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| 서비스 패브릭 행위자 |상태 저장 작업당 평균 시간(밀리초) |행위자 상태를 저장하는 데 걸린 시간(밀리초) |
| 서비스 패브릭 행위자 |로드 상태 작업당 평균 시간(밀리초) |행위자 상태를 로드하는 데 걸린 시간(밀리초) |

### <a name="events-related-to-actor-replicas"></a>행위자 복제본 관련 이벤트
Reliable Actors 런타임에서는 다음과 같은 [행위자 복제본](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)관련 이벤트를 내보냅니다.

| 이벤트 이름 | 이벤트 ID | Level | 키워드 | 설명 |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |정보 제공 |0x1 |행위자 복제본이 주 복제본 역할로 변경되었습니다. 따라서 이 파티션에 대한 행위자가 이 복제본 내부에 만들어집니다. |
| ReplicaChangeRoleFromPrimary |2 |정보 제공 |0x1 |행위자 복제본이 주 복제본 이외의 역할로 변경되었습니다. 따라서 이 파티션에 대한 행위자를 이 복제본 내부에서 더 이상 만들 수 없습니다. 이 복제본 내에 이미 만들어진 행위자로 새 요청이 전달되지 않습니다. 모든 진행 중인 요청이 완료된 후 해당 행위자는 소멸됩니다. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>행위자 활성화 및 비활성화 이벤트와 성능 카운터
Reliable Actors 런타임에서는 다음과 같은 [행위자 활성화 및 비활성화](service-fabric-reliable-actors-lifecycle.md)관련 이벤트를 내보냅니다.

| 이벤트 이름 | 이벤트 ID | Level | 키워드 | 설명 |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |정보 제공 |0x1 |행위자가 활성화되었습니다. |
| ActorDeactivated |6 |정보 제공 |0x1 |행위자가 비활성화되었습니다. |

Reliable Actors 런타임은 행위자 활성화 및 비활성화와 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| 서비스 패브릭 행위자 |평균 OnActivateAsync(밀리초) |OnActivateAsync 메서드를 실행하는 데 걸린 시간(밀리초) |

### <a name="actor-request-processing-performance-counters"></a>행위자 요청 처리 성능 카운터
클라이언트가 행위자 프록시 개체를 통해 메서드를 호출하면 요청 메시지가 네트워크를 통해 행위자 서비스에 전송됩니다. 서비스는 요청 메시지를 처리하고 응답을 다시 클라이언트로 보냅니다. Reliable Actors 런타임은 행위자 요청 처리와 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| 서비스 패브릭 행위자 |미해결 요청 수 |서비스에서 처리 중인 요청 수 |
| 서비스 패브릭 행위자 |요청당 평균 시간(밀리초) |서비스에서 요청을 처리하는 데 걸린 시간(밀리초) |
| 서비스 패브릭 행위자 |요청 deserialization에 걸린 평균 시간(밀리초) |서비스에서 수신될 때 행위자 요청 메시지를 deserialize하는 데 걸린 시간(밀리초) |
| 서비스 패브릭 행위자 |요청 serialization에 걸린 평균 시간(밀리초) |응답이 클라이언트로 전송되기 전에 서비스에서 행위자 응답 메시지를 serialize하는 데 걸린 시간(밀리초) |

## <a name="next-steps"></a>다음 단계
* [신뢰할 수 있는 행위자가 서비스 패브릭 플랫폼을 사용하는 방법](service-fabric-reliable-actors-platform.md)
* [행위자 API 참조 설명서](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [PerfView의 EventSource 공급자](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
