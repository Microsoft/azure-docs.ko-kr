---
title: Azure Service Fabric 클러스터 분산 | Microsoft Docs
description: 서비스 패브릭 클러스터 리소스 관리자를 사용한 클러스터 분산에 대한 소개
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74fe4f7c4c231f80c7555f39f840a85baae310e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809381"
---
# <a name="balancing-your-service-fabric-cluster"></a>서비스 패브릭 클러스터 분산
Service Fabric 클러스터 리소스 관리자는 노드나 서비스의 추가 또는 제거에 대응하는 동적 로드 변경을 지원합니다. 또한 제약 조건 위반을 자동으로 수정하고 사전에 로드를 분산하도록 클러스터를 조정합니다. 그러나 이러한 작업은 얼마나 자주 수행될까요? 그리고 이러한 작업을 트리거하는 것은 무엇일까요?

클러스터 리소스 관리자에서 수행하는 세 가지 작업 범주가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

1. 배치 – 이 단계는 상태 저장 복제본 또는 누락된 상태 비저장 인스턴스 배치를 처리합니다. 배치에는 새로운 서비스와 실패한 상태 저장 복제본 또는 상태 비저장 인스턴스 모두가 포함됩니다. 복제본 또는 인스턴스 삭제와 제거도 여기에서 다룹니다.
2. 제약 조건 검사 - 이 단계에서는 시스템 내에서 여러 배치 제약 조건(규칙)을 검사하고 위반을 수정합니다. 규칙의 예로는 노드 용량을 초과하지 않고 서비스 배치 제약 조건을 충족하도록 하는 것을 들 수 있습니다.
3. 분산 - 이 단계에서는 다양한 메트릭에 대해 구성된 원하는 수준의 분산에 따라 다시 조정이 필요한지 확인합니다. 필요한 경우 더 균형이 맞는 클러스터에서 배치를 찾습니다.

## <a name="configuring-cluster-resource-manager-timers"></a>클러스터 리소스 관리자 타이머 구성
균형 조정과 관련된 첫 번째 컨트롤 집합은 타이머 집합입니다. 이러한 타이머는 클러스터 리소스 관리자에서 클러스터를 검사하고 수정 작업을 수행하는 빈도를 제어합니다.

Cluster Resource Manager에서 수행할 수 있는 이러한 각 형식의 수정 작업은 해당 빈도를 제어하는 다른 타이머에 의해 제어됩니다. 각 타이머가 실행되면 작업이 예약됩니다. 기본적으로 Resource Manager는

* 1/10초마다 상태를 검색하고 업데이트를 적용합니다(예: 노드가 다운된 기록).
* 배치 검사 플래그를 설정합니다. 
* 제약 조건 검사 플래그를 매 초마다 설정합니다.
* 분산 플래그를 5초마다 설정합니다.

이러한 타이머를 관리하는 구성의 예제는 다음과 같습니다.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

현재 클러스터 리소스 관리자에서는 이러한 작업 중 하나를 한 번에 하나씩 순차적으로 수행합니다. 이는 이러한 타이머를 "최소 간격"이라고 하며, 타이머가 "설정 플래그"가 되면 수행되도록 하는 동작을 참조하는 이유입니다. 예를 들어 클러스터 분산에 앞서 서비스를 만들기 위해 Cluster Resource Manager가 보류 중인 요청을 처리합니다. 지정된 기본 시간 간격에서 알 수 있듯이 클러스터 리소스 관리자에서는 자주 수행해야 하는 작업을 검색합니다. 일반적으로 이는 각 단계에서 수행된 변경 집합이 작음을 의미합니다. 작은 변경을 자주 수행하면 클러스터에서 상황이 발생할 때 클러스터 리소스 관리자에서 즉각적으로 반응할 수 있습니다. 여러 동일한 형식의 이벤트는 동시에 발생하는 경향이 있으므로 기본 타이머가 일부 일괄 처리를 제공합니다. 

예를 들어 노드가 실패할 경우 전체 장애 도메인 작업을 한 번에 수행할 수 있습니다. 이러한 모든 실패는 *PLBRefreshGap* 이후의 다음 상태 업데이트에서 캡처됩니다. 수정은 다음 배치, 제약 조건 검사 및 분산 실행 중에 결정됩니다. 기본적으로 Cluster Resource Manager는 클러스터에서 변경 시간 내내 검색하는 것이 아니라 모든 변경을 한꺼번에 처리합니다. 이렇게 하면 갑작스러운 이탈이 발생하게 됩니다.

Cluster Resource Manager는 클러스터의 분산 여부를 판단하기 위해 추가적인 정보도 필요로 합니다. 에 대 한 다른 두 가지 구성 했습니다. *BalancingThresholds* 하 고 *ActivityThresholds*합니다.

## <a name="balancing-thresholds"></a>분산 임계값
분산 임계값은 로드 다시 분산을 트리거하기 위한 주요 컨트롤입니다. 메트릭에 대한 분산 임계값은 _비율_입니다. 가장 로드가 많은 노드의 메트릭에 대한 로드를 가장 로드가 적은 노드의 로드 양으로 나눈 값이 해당 메트릭의 *BalancingThreshold*를 초과하는 경우 클러스터의 불균형이 발생합니다. 결과적으로 Cluster Resource Manager가 다음 번에 확인할 때 분산이 트리거됩니다. *MinLoadBalancingInterval* 타이머는 클러스터 리소스 관리자에서 로드 다시 분산이 필요한지 확인해야 하는 빈도를 정의합니다. 확인은 아무 것도 발생하지 않는다는 의미입니다. 

분산 임계값은 클러스터 정의의 일부로서 메트릭 단위로 정의됩니다. 메트릭에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![분산 임계값 예][Image1]
</center>

이 예제에서는 각 서비스에서 한 단위의 메트릭을 사용합니다. 위 예제에서 노드의 최대 부하는 5이고 최소 부하는 2입니다. 이 메트릭에 대한 분산 임계값이 3이라고 가정해 봅니다. 클러스터의 비율이 5/2 = 2.5이며 지정된 분산 임계값인 3보다 낮으므로 클러스터는 균형이 맞습니다. Cluster Resource Manager가 확인할 때 분산이 트리거되지 않습니다.

아래 예에서 노드의 최대 로드는 10이지만 최소 로드는 2이므로 비율은 5입니다. 5는 해당 메트릭에 지정된 분산 임계값인 3보다 큽니다. 결과적으로 다음번에 분산 타이머가 실행될 때 분산 변경 실행이 예약됩니다. 이와 같은 상황에서 일부 로드는 일반적으로 노드 3에 분산됩니다. Service Fabric 클러스터 리소스 관리자에서 greedy 방식을 사용하지 않으므로 일부 로드가 노드 2에도 분산됩니다. 

<center>

![분산 임계값 예제 작업][Image2]
</center>

> [!NOTE]
> "분산"은 클러스터에서 로드를 관리하기 위한 두 가지 전략을 처리합니다. 클러스터 리소스 관리자에서 사용하는 기본 전략은 클러스터의 노드 간에 로드를 분산하는 것입니다. 다른 전략은 [조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)입니다. 조각 모음은 동일한 분산을 실행하는 동안에 수행됩니다. 분산 및 조각 모음 전략은 동일한 클러스터 내의 여러 메트릭에 사용할 수 있습니다. 서비스에서는 분산 및 조각 모음 메트릭을 모두 사용할 수 있습니다. 조각 모음 메트릭의 경우 분산 임계값보다 _작은_ 경우 클러스터의 로드 비율에서 다시 분산을 트리거합니다. 
>

분산 임계값 이하를 달성하는 것이 명시적 목표는 아닙니다. 분산 임계값은 *트리거*입니다. 분산이 실행될 때 클러스터 리소스 관리자는 자체적으로 수행할 수 있는 향상된 기능(있는 경우)을 결정합니다. 분산 검색이 시작되었기 때문에 어떤 항목도 이동하지 않는다는 의미는 아닙니다. 때로는 클러스터에서 분산되지 않지만 너무 제한되어 수정하지 못할 수 있습니다. 또는 향상된 기능이 이동해야 하지만 [비용이 많이 들 수도](service-fabric-cluster-resource-manager-movement-cost.md) 있습니다.

## <a name="activity-thresholds"></a>활동 임계값
경우에 따라 노드가 상대적으로 불균형하기도 하지만 클러스터의 *총* 부하량은 낮습니다. 이러한 현상은 일시적인 감소이거나, 새 클러스터가 막 부트스트랩되었기 때문에 발생합니다. 어느 경우든 얻을 수 있는 이득이 거의 없기 때문에 클러스터 분산에 시간을 투자하지 않으려 할 수 있습니다. 클러스터가 분산을 거쳤다면 *절대적인* 큰 차이를 만들지 않고 네트워크와 계산 리소스를 사용하여 항목을 이동한 것입니다. 이처럼 불필요한 이동을 방지하기 위해 활동 임계값이라고 하는 다른 컨트롤이 있습니다. 활동 임계값을 사용하면 활동에 대해 일부 절대 하한값을 지정할 수 있습니다. 이 임계값을 초과하는 노드가 없으면 분산 임계값에 도달해도 분산이 트리거되지 않습니다.

이 메트릭에 대해 세 개의 분산 임계값을 유지한다고 가정해 보겠습니다. 활동 임계값도 1,536이라고 가정해 보겠습니다. 첫 번째 경우에서 분산 임계값에 따르면 클러스터는 불균형 상태이지만 활동 임계값을 충족하는 노드가 없으므로 아무 일도 발생하지 않습니다. 아래 예에서 노드 1은 활동 임계값을 초과했습니다. 메트릭에 대한 분산 임계값과 활동 임계값이 모두 초과되었으므로 분산이 예약됩니다. 예를 들어 다음 다이어그램을 살펴보겠습니다. 

<center>

![활동 임계값 예][Image3]
</center>

분산 임계값과 마찬가지로 활동 임계값은 클러스터 정의를 통한 메트릭을 기준으로 정의됩니다.

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

분산 및 활동 임계값은 모두 특정 메트릭에 연결됩니다. 분산은 동일한 메트릭에 대해 분산 임계값과 활동 임계값을 모두 초과한 경우에만 트리거됩니다.

> [!NOTE]
> 지정되지 않은 경우 메트릭에 대한 균형 조정 임계값은 1이고 활동 임계값은 0입니다. 즉, 클러스터 Resource Manager는 지정된 부하에 해당 메트릭이 완벽하게 분산되도록 유지하려고 합니다. 사용자 지정 메트릭을 사용하는 경우 메트릭에 대한 고유한 균형 조정 및 활동 임계값을 명시적으로 정의하는 것이 좋습니다. 
>

## <a name="balancing-services-together"></a>분산 서비스 함께 사용
클러스터가 분산되지 않는지 여부는 클러스터 전체의 결정입니다. 그러나 해결하는 방법은 개별 서비스 복제본과 인스턴스를 움직이는 것입니다. 참 합리적이지 않은가요? 메모리가 한 노드에 쌓여 있다면 여러 복제본이나 인스턴스가 여기에 참여할 수 있습니다. 불균형을 해결하려면 불균형 상태인 메트릭을 사용하는 상태 저장 복제본이나 상태 비저장 인스턴스를 움직여야 합니다.

그러나 때때로 자체적으로 분산되지 않는 서비스가 이동됩니다(이전의 로컬 및 전역 가중치에 대한 논의를 상기함). 모든 서비스의 메트릭이 분산되었을 때 서비스가 이동하는 이유는 무엇인가요? 예를 들어 살펴보겠습니다.

- 4개 서비스, 즉 서비스 1, 서비스 2, 서비스 3 및 서비스 4를 가정해 보겠습니다. 
- 서비스 1에서 메트릭 1 및 메트릭 2를 보고합니다. 
- 서비스 2에서 메트릭 2 및 메트릭 3을 보고합니다. 
- 서비스 3에서 메트릭 3 및 메트릭 4를 보고합니다.
- 서비스 4에서 메트릭 99를 보고합니다. 

물론 여기서 우리가 려는 확인할 수 있습니다. 즉 체인이 있습니다! 실제로 4개의 독립된 서비스가 있는 것이 아니라, 3개의 서비스가 관련되어 있고 하나는 자체적으로 꺼져 있습니다.

<center>

![분산 서비스 함께 사용][Image4]
</center>

이 체인으로 인해 메트릭 1-4가 분산되지 않으므로 서비스 1-3에 속한 복제본 또는 인스턴스가 이동할 수 있습니다. 메트릭 1, 2 또는 3이 분산되지 않으므로 서비스 4에서 이동이 발생하지 않음을 알 수 있습니다. 서비스 4에 속한 복제본 또는 인스턴스를 이동해도 메트릭 1-3의 분산에 아무런 영향을 주지 않기 때문에 전혀 의미가 없습니다.

클러스터 리소스 관리자는 관련된 서비스를 자동으로 파악합니다. 서비스에 대한 메트릭을 추가, 제거 또는 변경하면 이러한 관계에 영향을 미칠 수 있습니다. 예를 들어 분산이 두 번 실행되는 동안 서비스 2에서 메트릭 2를 제거하도록 업데이트되었을 수 있습니다. 이를 통해 Service1와 Service2 사이의 체인이 끊깁니다. 이제는 관련된 두 개의 서비스 그룹 대신 세 개 그룹이 있습니다.

<center>

![분산 서비스 함께 사용][Image5]
</center>

## <a name="next-steps"></a>다음 단계
* 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.
* 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)를 참조하세요.
* 클러스터 리소스 관리자에는 클러스터에서 이탈을 늦추도록 구성할 수 있는 몇 가지 제한이 있습니다. 일반적으로 필요하지는 않지만 필요할 경우 [여기](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
