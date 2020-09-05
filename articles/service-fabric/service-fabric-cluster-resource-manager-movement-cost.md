---
title: 'Service Fabric 클러스터 리소스 관리자: 이동 비용'
description: Service Fabric 서비스의 이동 비용 및 동적 구성을 비롯 하 여 아키텍처 요구에 맞게 지정 하는 방법에 대해 알아봅니다.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005346"
---
# <a name="service-movement-cost"></a>서비스 이동 비용
Service Fabric 클러스터 리소스 관리자에서 클러스터에 적용할 변경 내용을 결정할 때 고려할 요소는 이러한 변경에 소요되는 비용입니다. "비용"이라는 개념은 클러스터를 향상시킬 수 있는 정도와 절충됩니다. 분산, 조각 모음 및 기타 요구 사항을 위해 서비스를 이동할 때 비용이 고려됩니다. 목표는 최소 중단 또는 비용으로 요구 사항을 충족하는 것입니다.

서비스 이동에는 최소한의 CPU 시간과 네트워크 대역폭이 필요합니다. 상태 저장 서비스의 경우 해당 서비스의 상태를 복사하여 추가 메모리와 디스크를 사용해야 합니다. Service Fabric 클러스터 리소스 관리자에서 제공하는 솔루션 비용을 최소화하면 클러스터의 리소스가 불필요하게 낭비되지 않도록 할 수 있습니다. 그러나 클러스터에서 리소스 할당을 크게 향상시키는 솔루션도 고려해야 합니다.

클러스터 리소스 관리자에는 클러스터를 관리하려고 하는 동안 비용을 계산하고 제한하는 두 가지 방법이 있습니다. 첫 번째 메커니즘은 단순히 모든 이동 횟수를 계산하는 것입니다. 두 솔루션이 동일한 분산(점수)으로 생성되는 경우 클러스터 리소스 관리자에서는 비용(총 이동 횟수)이 가장 낮은 솔루션을 선호합니다.

이 전략은 효과가 좋습니다. 하지만 복잡한 시스템에서도 기본 또는 정적 부하와 마찬가지로 모든 이동이 동일할 가능성은 거의 없습니다. 즉, 비용이 훨씬 높은 이동도 있습니다.

## <a name="setting-move-costs"></a>이동 비용 설정 
서비스를 만들 때 해당 서비스의 기본 이동 비용을 지정할 수 있습니다.

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

또한 서비스를 만든 후 해당 서비스에 대해 MoveCost를 동적으로 지정하거나 업데이트할 수도 있습니다. 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>복제본별 이동 비용을 동적으로 지정

위의 코드 조각은 모두 서비스 자체의 외부에서 전체 서비스에 대한 MoveCost를 한 번에 지정하기 위한 것입니다. 그러나 특정 서비스 개체의 이동 비용이 수명에 따라 변할 때 이동 비용이 가장 유용합니다. 서비스 자체에는 지정된 시간 동안 이동하는 데 소요되는 비용에 대한 최상의 아이디어가 있을 것이므로 서비스에서 런타임 동안 자체의 개별 이동 비용을 보고하는 API가 있습니다. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>이동 비용의 영향
MoveCost에는 5 개의 수준 (0, 낮음, 중간, 높음 및 VeryHigh)이 있습니다. 다음 규칙이 적용됩니다.

* Movecost는 0과 VeryHigh를 제외 하 고 서로 상대적입니다. 
* 이동 비용 0은 이동 비용이 무료임을 의미하며 솔루션 점수 계산에 포함되지 않습니다.
* 이동 비용을 High 또는 VeryHigh로 설정 해도 *복제본이 이동* 되지 않는다는 보장은 제공 *되지* 않습니다.
* VeryHigh 이동 비용이 있는 복제본은 다른 방법으로는 해결할 수 없는 제약 조건 위반이 발생 하는 경우에만 이동 됩니다 (위반을 해결 하기 위해 다른 많은 복제본을 이동 해야 하는 경우에도).



<center>

![이동할 복제본 선택 시의 요인으로 사용되는 이동 비용][Image1]
</center>

MoveCost를 사용하면 전체적으로 중단을 최소화며 가장 쉽게 적용할 수 있는 동시에 동등한 균형을 제공하는 솔루션을 찾을 수 있습니다. 서비스의 비용 개념은 다양한 요인을 기준으로 할 수 있습니다. 이동 비용 계산에서 가장 일반적으로 사용되는 요인은 다음과 같습니다.

- 서비스에서 이동해야 하는 상태 또는 데이터의 양
- 클라이언트 연결 끊기 비용 주 복제본 이동은 일반적으로 보조 복제본 이동보다 비용이 많이 듭니다.
- 처리 중인 작업을 중단하는 비용 데이터 저장소 수준의 일부 작업 또는 클라이언트 호출에 대한 응답으로 수행되는 작업의 경우 비용이 많이 소요됩니다. 이러한 작업은 특정 시점이 지나면 필요한 경우를 제외하고는 중지되지 않아야 합니다. 따라서 작업을 진행하는 동안에는 이 서비스 개체의 이동 비용을 늘려서 하는 이동 가능성을 줄입니다. 작업이 완료되면 비용을 다시 정상 수준으로 설정합니다.

> [!IMPORTANT]
> VeryHigh 이동 비용을 사용 하는 것은 클러스터에서 전역적으로 최적 배치 솔루션을 찾기 위해 클러스터 리소스 관리자의 기능을 크게 제한 하므로 신중 하 게 고려해 야 합니다. VeryHigh 이동 비용이 있는 복제본은 다른 방법으로는 해결할 수 없는 제약 조건 위반이 발생 하는 경우에만 이동 됩니다 (위반을 해결 하기 위해 다른 많은 복제본을 이동 해야 하는 경우에도).

## <a name="enabling-move-cost-in-your-cluster"></a>클러스터에서 이동 비용 사용
보다 세부적인 MoveCost를 고려하려면 클러스터에서 MoveCost를 사용하도록 설정해야 합니다. 이 설정이 없으면 이동 횟수를 계산하는 기본 모드를 사용하여 MoveCost가 계산되고 MoveCost 보고서는 무시됩니다.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>다음 단계
- Service Fabric 클러스터 리소스 관리자는 메트릭을 사용하여 클러스터의 리소스 사용량 및 용량을 관리합니다. 메트릭 및 메트릭 구성 방법에 대한 자세한 내용은 [Service Fabric에서 메트릭을 사용하여 리소스 사용량 및 부하 관리](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.
- 클러스터 리소스 관리자가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [Service Fabric 클러스터 부하 분산](service-fabric-cluster-resource-manager-balancing.md)을 참조하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
