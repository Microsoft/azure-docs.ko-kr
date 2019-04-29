---
title: Azure Service Fabric에서 메트릭의 조각 모음 | Microsoft Docs
description: 서비스 패브릭에서 메트릭에 대한 전략으로써 조각 모음 또는 압축 사용의 개요
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844414"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>서비스 패브릭에서 부하 및 메트릭의 조각 모음
클러스터의 부하 메트릭을 관리하기 위한 Service Fabric Cluster Resource Manager의 기본 전략은 부하를 분산하는 것입니다. 노드가 균등하게 사용되도록 하면 핫스폿 및 콜드스폿이 방지되어 경합 및 리소스 낭비를 야기하지 않습니다. 클러스터에서 워크로드를 분산하는 것은 오류가 지정된 워크로드의 많은 부분을 사용하지 않도록 하기 때문에 오류를 극복하는 측면에서 가장 안전한 방법입니다. 

Service Fabric Cluster Resource Manager는 조각 모음이라는 다른 로드 관리 전략을 지원합니다. 조각 모음은 클러스터 전체에 걸쳐 메트릭의 사용률을 분산하려고 하지 않고 통합하려고 하는 것을 의미합니다. 통합은 기본 부하 분산 전략을 뒤집는 것입니다. Cluster Resource Manager는 메트릭 부하의 평균 표준 편차를 최소화하는 대신 늘리려고 합니다.

## <a name="when-to-use-defragmentation"></a>조각 모음을 사용하는 경우
클러스터에서 부하를 분산하면 각 노드의 리소스 일부가 사용됩니다. 일부 워크로드는 매우 커서 노드의 대부분 또는 전체를 사용하는 서비스를 만듭니다. 이러한 경우 대량의 워크로드가 시작될 때 노드에 해당 워크로드를 실행할 충분한 공간이 없을 수 있습니다. 대량의 워크로드는 Service Fabric에서 문제가 되지 않습니다. 이러한 경우 Cluster Resource Manager는 이러한 대량의 워크로드를 위한 공간을 만들기 위해 클러스터를 다시 구성해야 한다고 결정합니다. 그러나 동시에 해당 워크로드는 클러스터에서 예약되기를 대기해야 합니다.

실행될 서비스와 상태가 많은 경우 큰 워크로드는 오랫동안 클러스터에 배치될 수 있습니다. 클러스터의 다른 워크로드도 커서 구성하는 데 시간이 더 오래 걸리는 경우 이러한 상황이 발생하기 더 쉽습니다. Service Fabric 팀은 이 시나리오의 시뮬레이션에서 생성 시간을 측정했습니다. 클러스터 사용률이 30%~50%보다 높아지자마자, 대규모 서비스를 만드는 데 훨씬 더 오래 걸린다는 사실이 확인되었습니다. 이 시나리오를 처리하기 위해 분산 전략으로 조각 모음을 도입했습니다. 특히 생성 시간이 중요한 큰 워크로드의 경우 조각 모음이 해당 새 워크로드를 클러스터에서 예약하는 데 도움이 될 수 있습니다.

조각 모음 메트릭을 구성하여 Cluster Resource Manage에서 서비스의 부하를 더 적은 노드로 축소하려고 사전에 시도할 수 있습니다. 이렇게 하면 클러스터를 다시 구성하지 않고도 대규모 서비스를 위한 공간이 항상 존재하게 됩니다. 클러스터를 다시 구성할 필요가 없으므로 대량의 워크로드를 빠르게 만들 수 있습니다.

대부분의 경우에는 디스크 조각 모음이 필요하지 않습니다. 서비스는 일반적으로 작으므로 클러스터에서 서비스의 공간을 쉽게 찾을 수 있습니다. 다시 구성할 수 있으면 대부분의 서비스가 작으며 동시에 빠르게 이동될 수 있으므로 다시 구성 작업도 빠르게 진행됩니다. 그러나 서비스가 크고 신속하게 만들어야 할 경우 조각 모음 전략을 사용합니다. 다음에서는 조각 모음을 사용할 때의 장단점을 살펴보겠습니다. 

## <a name="defragmentation-tradeoffs"></a>조각 모음의 장단점
실패한 노드에서 실행되는 서비스가 더 많기 때문에 조각 모음에 미치는 오류의 영향이 커질 수 있습니다. 클러스터의 리소스는 예약된 상태로 보유해야 하고 대량의 워크로드가 만들어질 때까지 기다려야 하므로 조각 모음의 비용도 증가할 수 있습니다.

다음 다이어그램에서는 조각 모음된 클러스터 및 조각 모음되지 않은 클러스터와 같이 두 클러스터의 시각적 표시를 제공합니다. 

<center>

![분산 및 조각 모음 클러스터 비교][Image1]
</center>

분산된 경우에 가장 큰 서비스 개체 중 하나를 배치하는 데 필요한 이동 수를 고려합니다. 조각 모음된 클러스터에서는 다른 서비스가 이동할 때까지 기다릴 필요 없이 대량의 워크로드를 4개 또는 5개의 노드에 배치할 수 있습니다.

## <a name="defragmentation-pros-and-cons"></a>조각 모음 장점 및 단점
그러면 다른 개념적 절충은 무엇입니까? 다음은 생각해 볼 사항을 간단한 테이블로 보여줍니다.

| 조각 모음 장점 | 조각 모음 단점 |
| --- | --- |
| 큰 서비스를 빠르게 만들 수 있습니다 |더 적은 노드에 부하가 집중되어 경합을 늘립니다 |
| 생성하는 동안 데이터 이동을 줄입니다 |실패한 경우 더 많은 서비스에 영향을 주고 더 많은 이탈이 발생할 수 있습니다 |
| 풍부한 요구 사항에 대한 설명 및 공간의 확보가 가능합니다. |전체 리소스 관리 구성이 더 복잡합니다 |

동일한 클러스터에서 조각 모음된 일반 메트릭을 혼합할 수 있습니다. Cluster Resource Manager는 다른 항목을 분산하는 동안 가능한 한 많은 디스크 조각 모음 메트릭을 통합하려고 합니다. 조각 모음 및 분산 전략을 함께 사용할 때의 결과는 다음을 비롯한 여러 가지 요인에 따라 달라집니다.
  - 부하 분산 메트릭의 수 및 조각 모음 메트릭의 수
  - 서비스가 두 가지 유형의 메트릭을 모두 사용하는지 여부 
  - 메트릭 가중치
  - 현재 메트릭 부하
  
실험은 필요한 정확한 구성을 결정해야 합니다. 프로덕션에서 조각 모음 메트릭을 사용하도록 설정하기 전에 워크로드를 철저하게 측정하는 것이 좋습니다. 동일한 서비스 내에서 조각 모음과 분산된 메트릭을 함께 사용하는 경우에 특히 그렇습니다. 

## <a name="configuring-defragmentation-metrics"></a>조각 모음 메트릭 구성
조각 모음 메트릭을 구성하는 작업은 클러스터에서 전역적인 의사 결정이며 개별 메트릭은 조각 모음에서 선택될 수 있습니다. 다음 구성 조각은 조각 모음에 대한 메트릭을 구성하는 방법을 보여 줍니다. 이 경우 "Metric2"는 정상적으로 계속 부하가 분산되지만 "Metric1"은 조각 모음 메트릭으로 구성됩니다. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>다음 단계
- Cluster Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.
- 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
