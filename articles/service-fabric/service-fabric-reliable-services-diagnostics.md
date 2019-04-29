---
title: Azure Service Fabric 상태 저장 Reliable Services 진단 | Microsoft Docs
description: Azure Service Fabric 상태 저장 Reliable Services의 진단 기능
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: f49176f944aa2abfa1d355ce0bd207d1b544c275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772961"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>상태 저장 Reliable Services의 진단 기능
Azure Service Fabric 상태 저장 Reliable Services StatefulServiceBase 클래스는 서비스를 디버그하는 데 사용할 수 있는 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 내보내고, 런타임이 작동하는 방법에 대한 고급 정보를 제공하고 문제 해결에 도움을 줍니다.

## <a name="eventsource-events"></a>EventSource 이벤트
상태 저장 Reliable Services StatefulServiceBase 클래스의 EventSource 이름은 “Microsoft-ServiceFabric-Services”입니다. 이 이벤트 원본의 이벤트는 서비스가 [Visual Studio에서 디버깅](service-fabric-debugging-your-application.md)될 때 [진단 이벤트](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) 창에 표시됩니다.

EventSource 이벤트를 수집하거나 보는 데 도움이 되는 도구 및 기술의 예로 [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) 및 [Microsoft TraceEvent 라이브러리](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)가 있습니다.

## <a name="events"></a>이벤트
| 이벤트 이름 | 이벤트 ID | Level | 이벤트 설명 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |정보 제공 |서비스 RunAsync 작업이 시작되면 내보내집니다. |
| StatefulRunAsyncCancellation |2 |정보 제공 |서비스 RunAsync 작업이 취소되면 내보내집니다. |
| StatefulRunAsyncCompletion |3 |정보 제공 |서비스 RunAsync 작업이 완료되면 내보내집니다. |
| StatefulRunAsyncSlowCancellation |4 |Warning |서비스 RunAsync 작업이 취소를 완료하는 데 너무 오래 걸리는 경우 내보내집니다. |
| StatefulRunAsyncFailure |5 |오류 |서비스 RunAsync 작업이 예외를 throw하면 내보내집니다. |

## <a name="interpret-events"></a>이벤트 해석
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion 및 StatefulRunAsyncCancellation 이벤트는 서비스 작성자가 서비스 시작, 취소 또는 완료 타이밍과 서비스의 수명 주기를 이해하는 데 유용합니다. 이 정보는 서비스 문제를 디버깅하거나 서비스 수명 주기를 이해할 때 유용할 수 있습니다.

StatefulRunAsyncSlowCancellation 및 StatefulRunAsyncFailure 이벤트는 서비스와 관련된 문제를 나타내므로 서비스 작성자는 이 이벤트에 세심한 주의를 기울여야 합니다.

서비스 RunAsync() 작업이 예외를 throw할 때마다 StatefulRunAsyncFailure가 내보내집니다. 일반적으로 throw된 예외는 서비스의 오류 또는 버그를 나타냅니다. 또한 예외로 인해 서비스가 실패하므로 다른 노드로 이동시켜야 합니다. 이 작업은 비용이 많이 소요될 수 있으며 서비스가 이동하는 동안 들어오는 요청을 지연시킬 수 있습니다. 서비스 작성자는 예외의 원인을 확인하고 가능한 경우 이를 완화해야 합니다.

RunAsync 작업에 대한 취소 요청이 4초보다 오래 걸릴 경우 StatefulRunAsyncSlowCancellation이 내보내집니다. 서비스 취소 완료 시간이 너무 오래 걸리는 경우 서비스가 다른 노드에서 신속하게 다시 시작하는 기능에 영향을 줄 수 있습니다. 이 시나리오는 서비스의 전반적인 가용성에 영향을 줄 수 있습니다.

## <a name="performance-counters"></a>성능 카운터
Reliable Services 런타임은 다음과 같은 성능 카운터 범주를 정의합니다.

| Category | 설명 |
| --- | --- |
| Service Fabric 트랜잭션 복제기 |Azure Service Fabric 트랜잭션 복제기 관련 카운터 |
| Service Fabric TStore |Azure Service Fabric TStore 관련 카운터 |

Service Fabric 트랜잭션 복제기는 [신뢰할 수 있는 상태 관리자](service-fabric-reliable-services-reliable-collections-internals.md)에서 지정된 [복제본](service-fabric-concepts-replica-lifecycle.md) 집합 내의 트랜잭션을 복제하는 데 사용합니다.

Service Fabric TStore는 키-값 쌍을 저장 및 검색하기 위한 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections-internals.md)에서 사용되는 구성 요소입니다.

Windows 운영 체제에서 기본적으로 사용할 수 있는 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 애플리케이션을 사용하면 성능 카운터 데이터를 수집하고 볼 수 있습니다. [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 은 성능 카운터 데이터를 수집하여 Azure 테이블에 업로드하기 위한 또 다른 옵션입니다.

### <a name="performance-counter-instance-names"></a>성능 카운터 인스턴스 이름
많은 수의 Reliable Services 또는 Reliable Services 파티션이 있는 클러스터에는 트랜잭션 복제기 성능 카운터 인스턴스가 많이 있습니다. 이것은 TStore 성능 카운터에 대한 사례이기도 하지만 사용된 신뢰할 수 있는 사전 및 신뢰할 수 있는 큐 수로 곱합니다. 성능 카운터 인스턴스 이름은 성능 카운터 인스턴스가 연결된 특정 [파티션](service-fabric-concepts-partitioning.md), 서비스 복제본 및 상태 공급자(TStore의 경우)를 식별하는 데 도움이 될 수 있습니다.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric 트랜잭션 복제기 범주
`Service Fabric Transactional Replicator`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId*는 성능 카운터 인스턴스와 연결된 Service Fabric 파티션 ID의 문자열 표현입니다. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)을 통해 생성됩니다.

*ServiceFabricReplicaId*는 지정된 Reliable Services 복제본와 연결된 ID입니다. 복제본 ID는 고유성을 보장하고 동일한 파티션에서 생성된 다른 성능 카운터 인스턴스와의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함됩니다. Reliable Services의 복제본 및 해당 역할에 대한 자세한 내용은 [여기](service-fabric-concepts-replica-lifecycle.md)에서 찾을 수 있습니다.

다음 카운터 인스턴스 이름은 `Service Fabric Transactional Replicator` 범주 아래의 카운터에 일반적인 이름입니다.

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

앞의 예제에서 `00d0126d-3e36-4d68-98da-cc4f7195d85e`은 Service Fabric 파티션 ID의 문자열 표현이고 `131652217797162571`은 복제본 ID입니다.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore 범주
`Service Fabric TStore`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId*는 성능 카운터 인스턴스와 연결된 Service Fabric 파티션 ID의 문자열 표현입니다. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)을 통해 생성됩니다.

*ServiceFabricReplicaId*는 지정된 Reliable Services 복제본와 연결된 ID입니다. 복제본 ID는 고유성을 보장하고 동일한 파티션에서 생성된 다른 성능 카운터 인스턴스와의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함됩니다. Reliable Services의 복제본 및 해당 역할에 대한 자세한 내용은 [여기](service-fabric-concepts-replica-lifecycle.md)에서 찾을 수 있습니다.

*ServiceFabricStateProviderId*는 신뢰할 수 있는 서비스 내의 상태 공급자와 연결된 ID입니다. 상태 공급자 ID는 TStore 간을 구분하기 위해 성능 카운터 인스턴스 이름에 포함됩니다.

*PerformanceCounterInstanceDifferentiator*는 상태 공급자 내에서 성능 카운터 인스턴스와 연결된 차별화 ID입니다. 이 구별자는 고유성을 보장하고 동일한 상태 공급자가 생성한 다른 성능 카운터 인스턴스와 충돌하지 않도록 하기 위해 성능 카운터 인스턴스에 포함됩니다.

다음 카운터 인스턴스 이름은 `Service Fabric TStore` 범주 아래의 카운터에 일반적인 이름입니다.

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

앞의 예제에서 `00d0126d-3e36-4d68-98da-cc4f7195d85e`는 Service Fabric 파티션 ID의 문자열 표현이고, `131652217797162571`은 복제본 ID이고, `142652217797162571`은 상태 공급자 ID이고, `1337`은 성능 카운터 인스턴스 구별자입니다.

### <a name="transactional-replicator-performance-counters"></a>트랜잭션 복제기 성능 카운터

Reliable Services 런타임은 `Service Fabric Transactional Replicator` 범주 아래에 다음 이벤트를 내보냅니다.

 카운터 이름 | 설명 |
| --- | --- |
| Begin Txn Operations/sec | 초당 만들어진 새 쓰기 트랜잭션의 수입니다.|
| Txn Operations/sec | 초당 신뢰할 수 있는 컬렉션에 대해 수행된 추가/업데이트/삭제 작업의 수입니다.|
| 로그 플러시 바이트/초 | 트랜잭션 복제기가 초당 디스크에 플러시하는 바이트 수입니다. |
| Throttled Operations/sec | 제한 때문에 트랜잭션 복제기가 초당 거부한 작업 수입니다. |
| 평균 Transaction ms/Commit | 트랜잭션당 평균 커밋 대기 시간(밀리초)입니다. |
| 평균 Flush Latency (ms) | 트랜잭션 복제기가 시작한 디스크 플러시 작업의 평균 지속 시간(밀리초)입니다. |

### <a name="tstore-performance-counters"></a>TStore 성능 카운터

Reliable Services 런타임은 `Service Fabric TStore` 범주 아래에 다음 이벤트를 내보냅니다.

 카운터 이름 | 설명 |
| --- | --- |
| 항목 개수 | 스토어에 있는 항목의 수|
| 디스크 크기 | 저장소에 대한 검사점 파일의 전체 디스크 크기(바이트)|
| 검사점 파일 쓰기(바이트/초) | 가장 최근 검사점 파일에 쓴 초당 바이트 수|
| 복사본 디스크 전송(바이트/초) | 저장소 복사 중 초당 읽거나(주 복제본에서) 쓴(보조 복제본에서) 디스크 바이트 수|

## <a name="next-steps"></a>다음 단계
[PerfView의 EventSource 공급자](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
