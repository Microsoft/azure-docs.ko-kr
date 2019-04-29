---
title: Azure Service Fabric 진단 및 모니터링 | Microsoft Docs
description: 이 문서에서는 Service Fabric Reliable ServiceRemoting 런타임에서 내보낸 성능 카운터를 포함하여 성능 모니터링 기능을 설명합니다.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: e4c698dc5aa8a07d09835adeac39db6ee35f720c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123181"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Reliable Service Remoting에 대한 진단 및 성능 모니터링
Reliable ServiceRemoting 런타임에서는 [성능 카운터](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)를 내보냅니다. 이러한 정보는 ServiceRemoting이 어떻게 작동하고 있는지 파악하여 문제를 해결하고 성능을 모니터링하는 데 도움이 됩니다.


## <a name="performance-counters"></a>성능 카운터
Reliable ServiceRemoting 런타임은 다음과 같은 성능 카운터 범주를 정의합니다.

| Category | 설명 |
| --- | --- |
| Service Fabric 서비스 |Azure Service Fabric Remoting 서비스 전용 카운터. 예: 요청을 처리하는 데 걸린 평균 시간 |
| Service Fabric 서비스 메서드 |Service Fabric Remoting 서비스에 의해 구현되는 메서드 전용 카운터. 예: 서비스 메서드가 호출되는 빈도 |

앞의 각 범주마다 하나 이상의 카운터가 있습니다.

Windows 운영 체제에서 기본적으로 사용할 수 있는 [Windows 성능 모니터](https://technet.microsoft.com/library/cc749249.aspx) 애플리케이션을 사용하면 성능 카운터 데이터를 수집하고 볼 수 있습니다. [Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 은 성능 카운터 데이터를 수집하여 Azure 테이블에 업로드하기 위한 또 다른 옵션입니다.

### <a name="performance-counter-instance-names"></a>성능 카운터 인스턴스 이름
많은 수의 ServiceRemoting 서비스 또는 파티션이 있는 클러스터에는 성능 카운터 인스턴스 수가 많습니다. 성능 카운터 인스턴스 이름은 성능 카운터 인스턴스가 연결된 특정 파티션 및 서비스 메서드(있는 경우)를 식별하는 데 도움이 될 수 있습니다.

#### <a name="service-fabric-service-category"></a>Service Fabric 서비스 범주
`Service Fabric Service`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* - 성능 카운터 인스턴스와 연결된 서비스 패브릭 파티션 ID의 문자열 표현. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 메서드를 통해 생성됩니다.

*ServiceReplicaOrInstanceId*는 성능 카운터 인스턴스와 연결된 Service Fabric 복제본/인스턴스 ID의 문자열 표현입니다.

*ServiceRuntimeInternalID*는 내부 용도에 맞게 패브릭 서비스 런타임에 의해 생성되는 64비트 정수의 문자열 표현입니다. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

다음은 `Service Fabric Service` 범주에 속하는 카운터에 대한 카운터 인스턴스 이름의 예입니다.

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

앞의 예에서 `2740af29-78aa-44bc-a20b-7e60fb783264`는 Service Fabric 파티션 ID의 문자열 표현이고 `635650083799324046`는 복제본/InstanceId의 문자열 표현이며 `5008379932`은 런타임의 내부 용도에 맞게 생성되는 64비트 ID입니다.

#### <a name="service-fabric-service-method-category"></a>Service Fabric 서비스 메서드 범주
`Service Fabric Service Method`범주의 경우 카운터 인스턴스 이름이 다음과 같은 형식으로 표시됩니다.

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName*은 성능 카운터 인스턴스가 연결되어 있는 서비스 메서드의 이름입니다. 메서드 이름의 형식은 Windows에서 성능 카운터 인스턴스 이름의 최대 길이에 대한 제약 조건을 사용하여 이름의 가독성을 조정하는 패브릭 서비스 런타임의 일부 논리에 따라 결정됩니다.

*ServiceRuntimeMethodId*는 내부 용도에 맞게 패브릭 서비스 런타임에 의해 생성되는 32비트 정수의 문자열 표현입니다. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

*ServiceFabricPartitionID* - 성능 카운터 인스턴스와 연결된 서비스 패브릭 파티션 ID의 문자열 표현. 파티션 ID는 GUID이며 해당 문자열 표현은 형식 지정자 "D"와 함께 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 메서드를 통해 생성됩니다.

*ServiceReplicaOrInstanceId*는 성능 카운터 인스턴스와 연결된 Service Fabric 복제본/인스턴스 ID의 문자열 표현입니다.

*ServiceRuntimeInternalID*는 내부 용도에 맞게 패브릭 서비스 런타임에 의해 생성되는 64비트 정수의 문자열 표현입니다. 고유성을 보장하고 다른 성능 카운터 인스턴스 이름과의 충돌을 방지하기 위해 성능 카운터 인스턴스 이름에 포함시킵니다. 사용자는 성능 카운터 인스턴스 이름에서 이 부분을 해석하지 않도록 해야 합니다.

다음은 `Service Fabric Service Method` 범주에 속하는 카운터에 대한 카운터 인스턴스 이름의 예입니다.

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

앞의 예에서 `ivoicemailboxservice.leavemessageasync`는 메서드 이름, `2`는 런타임의 내부 용도에 맞게 생성된 32비트 ID, `89383d32-e57e-4a9b-a6ad-57c6792aa521`은 Service Fabric 파티션 ID의 문자열 표현, `635650083804480486`는 Service Fabric 복제본/인스턴스 ID의 문자열 표현, `5008380`은 런타임의 내부 용도에 맞게 생성된 64비트 ID입니다.

## <a name="list-of-performance-counters"></a>성능 카운터 목록
### <a name="service-method-performance-counters"></a>서비스 메서드 성능 카운터

Reliable Service 런타임은 서비스 메서드의 실행과 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| Service Fabric 서비스 메서드 |초당 호출 수 |초당 서비스 메서드가 호출되는 횟수 |
| Service Fabric 서비스 메서드 |호출당 평균 시간(밀리초) |서비스 메서드를 실행하는 데 걸린 시간(밀리초) |
| Service Fabric 서비스 메서드 |초당 발생한 예외 수 |초당 서비스 메서드에서 예외가 발생한 횟수 |

### <a name="service-request-processing-performance-counters"></a>서비스 요청 처리 성능 카운터
클라이언트가 서비스 프록시 개체를 통해 메서드를 호출하면 요청 메시지가 네트워크를 통해 서비스 서비스에 전송됩니다. 서비스는 요청 메시지를 처리하고 응답을 다시 클라이언트로 보냅니다. Reliable ServiceRemoting 런타임은 서비스 요청 처리와 관련하여 다음과 같은 성능 카운터를 게시합니다.

| 범주 이름 | 카운터 이름 | 설명 |
| --- | --- | --- |
| Service Fabric 서비스 |미해결 요청 수 |서비스에서 처리 중인 요청 수 |
| Service Fabric 서비스 |요청당 평균 시간(밀리초) |서비스에서 요청을 처리하는 데 걸린 시간(밀리초) |
| Service Fabric 서비스 |요청 deserialization에 걸린 평균 시간(밀리초) |서비스에서 수신될 때 서비스 요청 메시지를 deserialize하는 데 걸린 시간(밀리초) |
| Service Fabric 서비스 |요청 serialization에 걸린 평균 시간(밀리초) |응답이 클라이언트로 전송되기 전에 서비스에서 서비스 응답 메시지를 직렬화하는 데 걸린 시간(밀리초) |

## <a name="next-steps"></a>다음 단계
* [샘플 코드](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [PerfView의 EventSource 공급자](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
