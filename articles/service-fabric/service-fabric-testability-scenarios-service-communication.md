---
title: '테스트 용이성: 서비스 통신 | Microsoft Docs'
description: 서비스 간 통신은 Service Fabric 애플리케이션의 중요 한 통합점입니다. 이 문서에서는 설계 고려 사항 및 테스트 기술에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543226"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>서비스 패브릭 테스트 용이성 시나리오: 서비스 통신
Azure 서비스 패브릭에서 마이크로 서비스 및 서비스 지향 아키텍처 스타일이 자연스럽게 드러납니다. 이러한 유형의 분산 아키텍처에서 구성 요소화된 마이크로 서비스 애플리케이션은 서로 통신이 필요한 여러 서비스로 구성됩니다. 가장 간단한 경우에도 일반적으로 상태 비저장 웹 서비스 그리고 통신이 필요한 상태 저장 데이터 저장소 서비스가 있습니다.

각 서비스에서 다른 서비스에 원격 API를 노출하기 때문에 서비스 간 통신은 애플리케이션의 중요한 통합점입니다. I/O와 관련된 API 경계 집합을 사용하여 작업하려면 일반적으로 적절한 테스트 및 유효성 검사가 필요합니다.

분산 시스템에서 이러한 서비스 경계를 연결할 때 여러 가지 사항을 고려해야 합니다.

* *전송 프로토콜*. 상호 운용성을 높이기 위해 HTTP를 사용할까요 아니면 처리량을 극대화하기 위해 사용자 지정 이진 프로토콜을 사용할까요?
* *오류 처리*. 영구 및 임시 오류는 어떻게 처리되나요? 서비스가 다른 노드로 이동하는 경우 어떻게 되나요?
* *제한 시간 및 대기 시간*. 다중 계층 애플리케이션에서 각 서비스 계층이 스택을 거쳐 사용자에 도달하는 대기 시간을 어떻게 처리합니까?

Service Fabric에서 제공하는 기본 제공 서비스 통신 구성 요소 중 하나를 사용하든지 사용자가 고유의 구성 요소를 만들든지 서비스 간의 상호 작용을 테스트하는 것이 애플리케이션의 복구 기능을 보장하는 데 중요합니다.

## <a name="prepare-for-services-to-move"></a>서비스 이동에 대한 준비
서비스 인스턴스는 시간이 지남에 따라 이동할 수 있습니다. 사용자 지정된 최적의 리소스 균형에 대한 부하 메트릭을 사용하여 구성된 경우에 특히 그렇습니다. 분산 시스템의 전체 수명 주기에서 발생하는 업그레이드, 장애 조치(failover), 수평 확장 및 기타 상황 동안 가용성을 극대화하기 위해 서비스 패브릭이 서비스 인스턴스를 이동시킵니다.

클러스터에서 서비스가 이동하는 경우에 서비스와 통신할 때 클라이언트 및 다른 서비스가 두 가지 시나리오를 처리할 수 있도록 준비해야 합니다.

* 마지막으로 통신한 이후에 서비스 인스턴스 또는 파티션 복제본이 이동했습니다. 이는 서비스 수명 주기에서 일반적인 부분이므로 애플리케이션의 수명 주기 동안 발생할 것으로 예상할 수 있습니다.
* 서비스 인스턴스 또는 파티션 복제본이 이동하는 중입니다. 서비스 패브릭에서는 한 노드에서 다른 노드로의 서비스 장애 조치(failover)가 매우 빠르게 발생하지만 서비스 통신 구성 요소가 느리게 시작되면 가용성이 지연될 수 있습니다.

시스템이 원활하게 실행되도록 이러한 시나리오를 정상적으로 처리하는 것이 중요합니다. 이렇게 하려면 다음에 주의합니다.

* 연결 가능한 모든 서비스에는 수신하는 *주소*가 있습니다(예: HTTP 또는 WebSockets). 서비스 인스턴스 또는 파티션이 이동하면 주소 엔드포인트가 변경됩니다. (다른 IP 주소로 다른 노드에 이동합니다.) 기본 제공 통신 구성 요소를 사용하는 경우 사용자를 대신하여 구성 요소에서 다시 해결 서비스 주소를 처리합니다.
* 서비스 인스턴스에서 수신기를 다시 시작하기 때문에 서비스 대기 시간이 일시적으로 늘어날 수 있습니다. 서비스 인스턴스를 이동한 후에 서비스가 수신기를 여는 속도에 따라 다릅니다.
* 새 노드에서 서비스가 열린 후에 기존 연결을 닫았다가 다시 열어야 합니다. 정상적인 노드 종료 또는 재시작의 경우 기존 연결이 정상적으로 종료될 때까지 시간이 허용됩니다.

### <a name="test-it-move-service-instances"></a>테스트: 서비스 인스턴스 이동
서비스 패브릭의 테스트 용이성 도구를 사용하여 이러한 상황을 다양한 방법으로 테스트하는 테스트 시나리오를 작성할 수 있습니다.

1. 상태 저장 서비스의 주 복제본을 이동합니다.
   
    여러 가지 이유로 상태 저장 서비스 파티션의 주 복제본을 이동할 수 있습니다. 특정 파티션의 주 복제본을 대상으로 이 방법을 사용하여 철저하게 제어된 방식으로 서비스가 이동에 어떻게 대응하는지 살펴볼 수 있습니다.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. 노드를 중지합니다.
   
    노드가 중지되면 서비스 패브릭에서는 해당 노드에 있는 모든 서비스 인스턴스 또는 파티션을 클러스터의 사용 가능한 다른 노드 중 하나로 이동합니다. 클러스터의 노드가 손실되어 해당 노드의 모든 서비스 인스턴스 및 복제본을 이동해야 하는 상황을 테스트할 수 있습니다.
   
    PowerShell **Stop-ServiceFabricNode** cmdlet을 사용하여 노드를 중지할 수 있습니다.
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>서비스 가용성 유지 관리
플랫폼인 서비스 패브릭은 서비스 고가용성을 제공하도록 설계됩니다. 그러나 극단적인 상황에서 기본 인프라 문제로 인해 서비스 제공이 중단될 수 있습니다. 이러한 시나리오를 테스트하는 것도 중요합니다.

상태 저장 서비스에서는 쿼럼 기반 시스템을 사용하여 고가용성에 대한 상태를 복제합니다. 즉, 쓰기 작업을 수행하려면 복제본의 쿼럼을 사용할 수 있어야 합니다. 매우 드물기는 해도 광범위한 하드웨어 오류와 같이 복제본의 쿼럼을 사용할 수 없는 경우가 아주 가끔 있습니다. 이러한 경우에 쓰기 작업을 수행할 수 없지만 읽기 작업은 계속 수행할 수 있습니다.

### <a name="test-it-write-operation-unavailability"></a>테스트: 쓰기 작업 사용 불가
서비스 패브릭의 테스트 용이성 도구를 사용하여 쿼럼 손실을 테스트로 유도하는 오류를 삽입할 수 있습니다. 이러한 시나리오가 드물지만 상태 저장 서비스를 사용하는 클라이언트 및 서비스에서 이에 대한 쓰기 요청을 만들 수 없는 상황을 처리할 수 있도록 준비하는 것이 중요합니다. 또한 상태 저장 서비스 자체에서 이 가능성을 인식하고 호출자에게 정상적으로 전달하는 것도 중요합니다.

PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** cmdlet을 사용하여 쿼럼 손실을 유도할 수 있습니다.

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

이 예제에서는 모든 복제본을 중지하지 않고 쿼럼 손실을 포함하도록 `QuorumLossMode`를 `QuorumReplicas`로 설정합니다. 이러한 방식으로 읽기 작업은 여전히 가능합니다. 전체 파티션을 사용할 수 없는 시나리오를 테스트하려면 이 스위치를 `AllReplicas`로 설정합니다.

## <a name="next-steps"></a>다음 단계
[테스트 용이성 작업에 대한 자세한 정보](service-fabric-testability-actions.md)

[테스트 용이성 시나리오에 대한 자세한 정보](service-fabric-testability-scenarios.md)

