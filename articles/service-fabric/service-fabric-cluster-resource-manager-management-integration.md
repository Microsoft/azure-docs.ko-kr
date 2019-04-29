---
title: Service Fabric 클러스터 리소스 관리자 - 관리 통합 | Microsoft Docs
description: 클러스터 리소스 관리자과 서비스 패브릭 관리 간에 통합 지점의 개요입니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c201945e94474d54b8a19918f3b55a0b40995a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743516"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>클러스터 리소스 관리자와 서비스 패브릭 클러스터 관리 통합
Service Fabric 클러스터 리소스 관리자는 Service Fabric에서 업그레이드를 수행하지는 않지만 관련되어 있습니다. Cluster Resource Manager에서 관리에 유용하게 사용할 수 있는 첫 번째 방법은 내부에 있는 서비스와 클러스터의 필요한 상태를 추적하는 것입니다. Cluster Resource Manager는 원하는 구성에 클러스터를 배치할 수 없는 경우 상태 보고서를 보냅니다. 예를 들어 용량이 부족하면 클러스터 리소스 관리자에서 문제를 나타내는 상태 경고 및 오류를 보냅니다. 통합의 다른 부분은 업그레이드 작동 방법와 관련이 있습니다. 클러스터 리소스 관리자는 업그레이드 중에 해당 동작을 약간 변경합니다.  

## <a name="health-integration"></a>상태 통합
클러스터 리소스 관리자는 서비스를 배치하기 위해 정의한 규칙을 지속적으로 추적합니다. 또한 노드와 클러스터 및 클러스터 전체에서 각 메트릭에 남은 용량을 추적합니다. 해당 규칙을 충족할 수 없거나 용량이 부족한 경우 상태 경고 및 오류를 내보냅니다. 예를 들어, 노드가 용량을 초과하면 Cluster Resource Manager는 서비스를 이동하여 상황을 수정하려고 합니다. 상황을 수정할 수 없는 경우 용량을 초과한 노드와 해당 메트릭을 나타내는 상태 경고를 내보냅니다.

Resource Manager 상태 경고의 또 다른 예는 배치 제약 조건 위반에 대한 것입니다. 예를 들어 배치 제약 조건(예: `“NodeColor == Blue”`)을 정의하고 리소스 관리자에서 해당 제약 조건 위반을 검색한 경우 상태 경고를 내보냅니다. 이는 사용자 지정 제약 조건 및 기본 제약 조건(예: 장애 도메인 및 업그레이드 도메인 제약 조건)에 적용됩니다.

이러한 상태 보고서의 예를 들면 다음과 같습니다. 이 경우에 상태 보고서는 시스템 서비스의 파티션 중 하나에 대한 내용입니다. 상태 메시지는 해당 파티션의 복제본이 너무 적은 업그레이드 도메인에 일시적으로 압축되었음을 나타냅니다.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

상태 메시지는 다음과 같은 사항을 알려줍니다.

1. 모든 복제본은 자체로 정상: 각에 AggregatedHealthState 있습니다. 확인
2. 현재 업그레이드 도메인 분산 제약 조건이 위반되었습니다. 이는 특정 업그레이드 도메인에 이 파티션보다 더 많은 복제본이 있음을 의미합니다.
3. 위반이 발생하는 복제본을 포함한 노드 - 이 경우 이름이 "Node.8"인 노드입니다.
4. 이 파티션에서 현재 업그레이드가 진행 중인지 여부("현재 업그레이드 중 - false")
5. 이 서비스에 대 한 배포 정책: "배포 정책-압축"입니다. `RequireDomainDistribution` [배치 정책](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)으로 관리됩니다. "압축 중"은 이 경우 DomainDistribution이 필요하지 _않음_을 나타냅니다. 따라서 이 서비스에 대해 배치 정책이 지정되지 않았습니다. 
6. 보고서가 발생한 시기 - 2015년 8월 10일 오후 7:13:02

이와 같은 정보는 무언가 문제가 발생했음을 알려주기 위해 프로덕션에서 실행되는 경고를 제공하며, 잘못된 업그레이드를 검색하고 중단하는 데에도 사용됩니다. 이 경우에 Resource Manager가 업그레이드 도메인에 복제본을 압축해야 했던 이유를 알아낼 수 있는지 확인하려고 합니다. 예를 들어 다른 업그레이드 도메인의 노드가 중단되었기 때문에 압축이 일반적으로 일시적일 수 있습니다.

클러스터 리소스 관리자에서 일부 서비스를 배치하려고 시도하지만 작동하는 솔루션이 없다고 가정해 보겠습니다. 서비스를 배치할 수 없는 경우 일반적으로 다음과 같은 이유 중 하나에 해당합니다.

1. 일부 일시적 조건 때문에 서비스 인스턴스 또는 복제본을 올바르게 배치할 수 없습니다.
2. 서비스의 배치 요구 사항이 만족스럽지 않습니다.

이러한 경우 클러스터 리소스 관리자의 상태 보고서를 통해 서비스를 배치할 수 없는 이유를 확인할 수 있습니다. 이 프로세스를 제약 조건 제거 시퀀스라고 합니다. 이 프로세스를 실행하는 동안 서비스에 영향을 주는 구성된 제약 조건이 설명되고 제거한 항목이 기록됩니다. 이와 같이 서비스를 배치할 수 없는 경우 제거된 노드 및 원인을 확인할 수 있습니다.

## <a name="constraint-types"></a>제약 조건 형식
이러한 상태 보고서에서 서로 다른 제약 조건 각각에 대해 살펴보겠습니다. 복제본을 배치할 수 없는 경우 이러한 제약 조건과 관련된 상태 메시지가 표시됩니다.

* **ReplicaExclusionStatic** 하 고 **ReplicaExclusionDynamic**: 이러한 제약 조건은 동일한 파티션의 두 서비스 개체가 동일한 노드에 배치 되어야 하므로 솔루션이 거부 되었음을 나타냅니다. 이는 해당 노드의 실패가 해당 파티션에 지나치게 영향을 줄 수 있으므로 허용되지 않습니다. ReplicaExclusionStatic 및 ReplicaExclusionDynamic은 거의 동일한 규칙이며, 차이점은 실제로 문제가 되지 않습니다. ReplicaExclusionStatic 또는 ReplicaExclusionDynamic 제약 조건이 포함된 제약 조건 제거 시퀀스가 표시되는 경우 클러스터 리소스 관리자에서는 노드가 충분하지 않다고 인식합니다. 이 경우 나머지 솔루션에서 허용되지 않는 이러한 잘못된 배치를 사용해야 합니다. 시퀀스의 다른 제약 조건은 일반적으로 노드가 처음부터 제거되는 이유를 알려줍니다.
* **PlacementConstraint**: 이 메시지를 표시 하는 경우 서비스의 배치 제약 조건에 일치 하지 않는 일부 노드를 제거 했음을 의미 합니다. 현재 구성된 배치 제약 조건을 이 메시지의 일부분으로 추적합니다. 배치 제약 조건이 정의되어 있는 경우 이는 정상입니다. 그러나 배치 제약 조건이 잘못되어 너무 많은 노드를 제거하면 이를 인식하게 됩니다.
* **NodeCapacity**: 이 제약 조건은 배치 하 용량을 초과 하기 때문에 Cluster Resource Manager 없습니다 표시 된 노드에 복제본을 배치할는 것을 의미 합니다.
* **선호도**: 이 제약 조건은 배치할 수는 복제본에 영향을 받는 노드 선호도 제약 조건의 위반을 발생 시키기 때문 나타냅니다. 선호도에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)에 있습니다.
* **FaultDomain** 하 고 **UpgradeDomain**: 이 제약 조건은 특정 장애 또는 업그레이드 도메인에서 발생 표시 된 노드에 복제본을 배치 하는 경우 노드를 제거 합니다. 이 제약 조건을 설명하는 몇 가지 예는 [장애 및 업그레이드 도메인 제약 조건 및 결과 동작](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: 일반적으로이 제약 조건은 기본적으로 최적화로 실행 되므로 솔루션에서 노드 제거를 참조 해서는 안 됩니다. 또한 기본 설정 위치 제약 조건은 업그레이드 중에도 존재합니다. 이 제약 조건은 업그레이드하는 동안 업그레이드를 시작했을 때의 위치로 서비스를 다시 이동하는 데 사용합니다.

## <a name="blocklisting-nodes"></a>노드 차단 목록 작성
클러스터 리소스 관리자에서 보고하는 또 다른 상태 메시지는 노드가 차단 목록에 있는 경우입니다. 차단 목록은 자동으로 적용되는 임시 제약 조건으로 간주할 수 있습니다. 해당 서비스 유형의 인스턴스를 시작할 때 반복적인 오류가 발생하면 노드가 차단 목록에 포함됩니다. 노드는 서비스 유형별로 차단 목록에 포함됩니다. 하나의 서비스 유형에 대한 노드가 차단 목록에 포함되지만 다른 유형에 대해서는 차단 목록에 포함될 수 없습니다. 

개발하는 중에 차단 목록이 자주 표시됩니다. 일부 버그로 인해 서비스 호스트가 시작될 때 크래시가 발생합니다. Service Fabric에서 서비스 호스트를 만들려고 몇 번을 시도하지만 오류가 계속 발생합니다. 몇 번 시도한 후에 해당 노드가 차단 목록에 포함되고 클러스터 리소스 관리자에서는 다른 위치에 해당 서비스를 만들려고 시도합니다. 이 오류가 여러 노드에서 계속 발생하면 클러스터에서 유효한 모든 노드가 차단될 수 있습니다. 또한 차단 목록 작성은 원하는 규모에 맞게 서비스를 성공적으로 시작할 수 없을 만큼 너무 많은 노드를 제거할 수도 있습니다. 일반적으로 서비스가 원하는 복제본 또는 인스턴스 수 이하에 있음을 나타내는 추가 오류 또는 경고뿐만 아니라 처음에 차단 목록으로 연결되는 오류가 무엇인지 알려주는 상태 메시지도 클러스터 리소스 관리자에서 표시합니다.

차단 목록은 영구적인 조건이 아닙니다. 몇 분 후에 노드가 차단 목록에서 제거되고 Service Fabric에서 해당 노드의 서비스를 다시 활성화할 수 있습니다. 서비스가 계속 실패하면 해당 서비스 유형에 대한 노드가 차단 목록에 다시 포함됩니다. 

### <a name="constraint-priorities"></a>제약 조건 우선 순위

> [!WARNING]
> 제약 조건 우선 순위를 변경하는 것은 권장되지 않으며 클러스터에 심각하게 부정적인 영향을 미칠 수 있습니다. 아래의 정보는 기본 제약 조건 우선 순위 및 그 동작을 참조하기 위해 제공됩니다. 
>

이러한 모든 제약 조건을 고려할 때 "내 시스템에서 장애 도메인 제약 조건이 가장 중요하다고 생각합니다. 장애 도메인 제약 조건을 위반하지 않도록 보장하기 위해 다른 제약 조건을 위반하려고 합니다."라고 생각했을 수도 있습니다.

제약 조건은 서로 다른 우선 순위 수준으로 구성할 수 있습니다. 다음과 같습니다.

   - "하드" (0)
   - "소프트" (1)
   - "최적화" (2)
   - "해제" (-1) 
   
대부분의 제약 조건은 기본적으로 하드 제약 조건으로 구성됩니다.

제약 조건의 우선 순위 변경은 흔하지 않습니다. 대개 환경에 영향을 미친 다른 버그 또는 동작을 해결하기 위해 제약 조건 우선 순위를 변경해야 하는 경우가 있었습니다. 일반적으로 제약 조건 우선 순위 인프라의 유연성은 훌륭하게 작동하지만 자주 필요하지는 않습니다. 모든 항목은 대부분 해당 기본 우선 순위에 위치합니다. 

우선 순위 수준은 _지정된 제약 조건이 위반되거나 항상 충족된다_는 것을 의미하지 않습니다. 제약 조건 우선 순위는 제약 조건이 적용되는 순서를 정의합니다. 우선 순위는 모든 제약 조건을 충족할 수 없는 경우의 절충 작업을 정의합니다. 일반적으로 환경에 다른 작업이 없다면 모든 제약 조건이 충족될 수 있습니다. 제약 조건 위반으로 이어질 시나리오의 몇 가지 예로 충돌하는 제약 조건 또는 많은 수의 동시 실패가 있습니다.

고급 시나리오에서는 제약 조건 우선 순위를 변경할 수 있습니다. 예를 들어 노드 용량 문제를 해결하는 데 필요한 경우 선호도를 항상 위반할 수 있다고 가정해 보겠습니다. 이를 위해 선호도 제약 조건의 우선순위를 "soft"(1)로 설정하고 용량 제약 조건을 "hard"(0)로 설정해 둘 수 있습니다.

다양한 제약 조건에 대한 기본 우선 순위 값이 지정된 구성은 다음과 같습니다.

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>장애 도메인 및 업그레이드 도메인 제약 조건
클러스터 리소스 관리자에서 장애 도메인과 업그레이드 도메인 간에 서비스가 분산되도록 유지하려고 합니다. 이를 위해 클러스터 리소스 관리자 엔진 내부의 제약 조건으로 모델링합니다. 이들의 사용 방법과 특정 동작에 대한 자세한 내용은 [클러스터 구성](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior) 문서를 확인하세요.

클러스터 리소스 관리자는 업그레이드, 오류 또는 다른 제약 조건 위반을 처리하기 위해 업그레이드 도메인에 몇 개의 복제본을 압축해야 할 수 있습니다. 장애 도메인 또는 업그레이드 도메인에 압축하는 것은 일반적으로 시스템에 여러 번의 실패 또는 다른 변동이 있어 올바르게 배치할 수 없는 경우에만 발생합니다. 이러한 상황에서도 압축을 방지하려면 `RequireDomainDistribution` [배치 정책](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)을 활용할 수 있습니다. 이 경우 서비스 가용성 및 안정성에 부작용을 미칠 수 있으므로 신중하게 고려해야 합니다.

환경이 올바르게 구성되면 업그레이드 중에도 모든 제약 조건이 완전히 준수됩니다. 클러스터 리소스 관리자에서 제약 조건을 감시하고 있다는 것이 중요합니다. 위반을 검색하는 즉시 문제를 보고하고 해결하려고 시도합니다.

## <a name="the-preferred-location-constraint"></a>기본 설정 위치 제약 조건
PreferredLocation 제약 조건은 두 가지 용도로 사용되므로 약간 다릅니다. 이 제약 조건의 한 가지 용도는 애플리케이션 업그레이드 중에 있습니다. 클러스터 리소스 관리자는 업그레이드하는 동안 이 제약 조건을 자동으로 관리합니다. 업그레이드가 완료되면 해당 복제본이 초기 위치로 반환되는지 확인하는 데 사용됩니다. PreferredLocation 제약 조건의 다른 용도는 [ `PreferredPrimaryDomain`배치 정책](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)에 대한 것입니다. 이러한 두 가지 용도가 모두 최적화이므로 PreferredLocation 제약 조건은 기본적으로 "Optimization(최적화)"으로 설정되는 유일한 제약 조건입니다.

## <a name="upgrades"></a>업그레이드
애플리케이션 및 클러스터를 업그레이드하는 동안 Cluster Resource Manager가 유용합니다. 이 때 다음과 같은 두 개의 작업이 있습니다.

* 클러스터 규칙이 손상되지 않았는지 확인하는 작업
* 원활한 업그레이드를 돕는 작업

### <a name="keep-enforcing-the-rules"></a>규칙 적용 유지
알고 있어야 하는 중요한 점은 배치 제약 조건 및 용량과 같은 엄격한 제약 조건인 규칙이 업그레이드 중에도 적용된다는 것입니다. 배치 제약 조건을 통해 업그레이드 중에도 허용된 작업을 실행할 수 있습니다. 서비스에 대한 제약이 심하면 업그레이드가 오래 걸릴 수 있습니다. 실행 중인 서비스 또는 노드가 업데이트를 위해 중단되면 이동할 수 있는 위치에 대한 옵션이 거의 없을 수 있습니다.

### <a name="smart-replacements"></a>스마트 대체
업그레이드가 시작되면 Resource Manager는 클러스터 현재 정렬의 스냅숏을 생성합니다. 각각의 업그레이드 도메인이 완료되면 해당 업그레이드 도메인에 있던 서비스를 원래 정렬로 반환하려고 시도합니다. 이렇게 하면 업그레이드하는 동안 서비스에 대해 최대 2개의 전환이 수행됩니다. 영향을 받는 노드에서 하나의 이동이 있고, 하나의 다시 이동이 있습니다. 클러스터 또는 서비스를 업그레이드 이전의 상태로 돌려놓으면 업그레이드에서도 클러스터의 레이아웃에 영향을 주지 않습니다. 

### <a name="reduced-churn"></a>이탈 감소
업그레이드 중에 발생하는 또 다른 작업은 클러스터 리소스 관리자에서 분산을 해제하는 것입니다. 부하 분산은 업그레이드를 위해 서비스를 비운 노드로 이동하는 것과 같이 업그레이드 자체에 대한 불필요한 반응을 방지합니다. 문제가 발생한 업그레이드가 클러스터 업그레이드인 경우 전체 클러스터는 업그레이드하는 동안 부하가 분산되지 않습니다. 제약 조건 검사는 활성 상태를 유지하며 메트릭의 사전 분산을 기반으로 하는 이동만 비활성화됩니다.

### <a name="buffered-capacity--upgrade"></a>버퍼링된 용량 및 업그레이드
일반적으로 클러스터가 제한되거나 거의 가득 찬 경우에도 업그레이드를 완료할 수 있습니다. 업그레이드하는 동안 클러스터의 용량을 관리하는 것이 평소보다 훨씬 더 중요합니다. 업그레이드 도메인의 수에 따라 클러스터를 통해 업그레이드가 롤링함에 따라 용량의 5 ~ 20%가 마이그레이션되어야 합니다. 해당 작업은 어딘가로 이동해야 합니다. 이는 [버퍼링된 용량](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)이라는 개념이 유용한 경우입니다. 버퍼링된 용량은 정상 작업 중에 적용됩니다. 클러스터 리소스 관리자에서 필요한 경우 업그레이드 중에 노드를 총 용량(버퍼 사용)까지 채울 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
