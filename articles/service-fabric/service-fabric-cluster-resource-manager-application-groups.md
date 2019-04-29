---
title: Service Fabric Cluster Resource Manager - 애플리케이션 그룹 | Microsoft Docs
description: Service Fabric 클러스터 리소스 관리자에서 애플리케이션 그룹 기능 개요
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7e90dc00a8e042e48d8016e25dda04c15ce9f619
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114076"
---
# <a name="introduction-to-application-groups"></a>애플리케이션 그룹 소개
Service Fabric의 Cluster Resource Manager는 일반적으로 부하([메트릭](service-fabric-cluster-resource-manager-metrics.md)을 통해 표시됨)를 클러스터 전체에 균등하게 분산하여 클러스터 리소스를 관리합니다. Service Fabric은 클러스터에서 노드의 용량과 [용량](service-fabric-cluster-resource-manager-cluster-description.md)을 통해 전체적으로 클러스터를 관리합니다. 메트릭과 용량은 다양한 워크로드에 잘 적용되지만 서로 다른 Service Fabric 애플리케이션 인스턴스를 과도하게 사용하는 패턴은 때때로 추가 요구 사항을 가져옵니다. 예를 들어 다음을 원할 수 있습니다.

- 일부 명명된 애플리케이션 인스턴스 내에서 클러스터에 있는 노드에 대한 일부 용량을 서비스용으로 예약
- 명명된 애플리케이션 인스턴스 내의 서비스가 실행되는 노드의 총 수 제한(전체 클러스터에 분산하지 않음)
- 내부 서비스의 수 또는 서비스의 전체 리소스 소비량을 제한하기 위해 명명된 애플리케이션 인스턴스 자체에서 용량 정의

이러한 요구 사항을 충족하기 위해 Service Fabric Cluster Resource Manager는 애플리케이션 그룹이라는 기능을 지원합니다.

## <a name="limiting-the-maximum-number-of-nodes"></a>노드의 최대 수 제한
애플리케이션 용량에 대한 가장 간단한 사용 사례는 애플리케이션 인스턴스를 특정 최대 노드 수로 제한해야 하는 경우입니다. 이를 통해 해당 애플리케이션 인스턴스 내의 모든 서비스가 설정된 여러 머신에 통합됩니다. 명명된 해당 애플리케이션 인스턴스 내에서 서비스의 실제 리소스 사용을 예측하거나 방지하려는 경우 통합 방식이 유용합니다. 

다음 이미지에서는 최대 노드 수가 정의되거나 정의되지 않은 애플리케이션 인스턴스를 보여줍니다.

<center>

![최대 노드 수를 정의 하는 응용 프로그램 인스턴스][Image1]
</center>

왼쪽 예제에서 애플리케이션에는 정의된 최대 노드 수가 없으며 세 가지 서비스가 있습니다. Cluster Resource Manager는 클러스터에서 최상의 균형을 달성하기 위해 6개의 사용 가능한 노드에 모든 복제본을 분산했습니다(기본 동작). 오른쪽 예제에서는 동일한 애플리케이션이 3개의 노드로 제한된 것을 확인할 수 있습니다.

이 동작을 제어하는 매개 변수를 MaximumNodes라고 합니다. 이 매개 변수는 애플리케이션을 만드는 동안 설정되거나 이미 실행 중인 애플리케이션 인스턴스에 대해 업데이트될 수 있습니다.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

노드 집합 내에서 Cluster Resource Manager는 함께 배치되는 서비스 개체 또는 사용되는 노드를 보장하지 않습니다.

## <a name="application-metrics-load-and-capacity"></a>애플리케이션 메트릭, 부하 및 용량
애플리케이션 그룹을 사용하면 지정된 명명된 애플리케이션 인스턴스와 연결된 메트릭 및 이러한 메트릭에 대한 해당 애플리케이션 인스턴스의 용량을 정의할 수 있습니다. 애플리케이션 메트릭을 사용하여 해당 애플리케이션 인스턴스 내에서 서비스의 리소스 사용을 추적하고 예약하며 제한할 수 있습니다.

각 애플리케이션 메트릭에서 두 개의 값을 다음과 같이 설정할 수 있습니다.

- **총 애플리케이션 용량** - 이 설정은 특정 메트릭에 대한 애플리케이션의 총 용량을 나타냅니다. Cluster Resource Manager를 사용하면 이 값을 초과하는 총 부하를 발생시키는 이 애플리케이션 인스턴스 내에서 새 서비스를 만들 수 없습니다. 예를 들어, 애플리케이션 인스턴스에 10이라는 용량이 있었고 이미 5라는 부하가 있다고 가정해보겠습니다. 10이라는 기본 총 부하를 가진 서비스를 만들도록 허용하지 않습니다.
- **노드 최대 용량** – 이 설정은 단일 노드의 애플리케이션에 대한 최대 총 부하를 지정합니다. 부하가 이 용량을 초과하면 Cluster Resource Manager는 부하가 감소되도록 복제본을 다른 노드로 이동합니다.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>용량 예약
애플리케이션 그룹의 또 다른 일반적인 용도는 클러스터 내의 리소스가 지정된 애플리케이션 인스턴스에 대해 예약되었는지 확인하는 것입니다. 애플리케이션 인스턴스를 만들 때 해당 공간이 항상 예약됩니다.

클러스터의 애플리케이션 공간 예약은 다음과 같은 경우에도 즉시 수행됩니다.
- 애플리케이션 인스턴스가 만들어지지만 그 안에 서비스가 아직 없는 경우
- 애플리케이션 인스턴스 내의 서비스 수가 매번 변경되는 경우 
- 서비스가 존재하지만 리소스를 사용하지 않고 있는 경우 

응용 프로그램 인스턴스에 대 한 리소스를 예약 필요 두 개의 추가 매개 변수를 지정 합니다. *MinimumNodes* 고 *NodeReservationCapacity*

- **MinimumNodes** - 애플리케이션 인스턴스를 실행해야 하는 노드의 최소 수를 정의합니다.  
- **NodeReservationCapacity** - 이 설정은 애플리케이션에 대한 메트릭을 기준으로 합니다. 해당 값은 해당 애플리케이션의 서비스가 실행되는 모든 노드에서 애플리케이션용으로 예약된 메트릭 크기입니다.

**MinimumNodes** 및 **NodeReservationCapacity**를 함께 사용하면 클러스터 내에서 애플리케이션에 대한 최소 부하 예약이 보장됩니다. 필요한 총 예약 크기보다 더 적은 양의 용량이 클러스터에 남아 있는 경우 애플리케이션을 만들지 못합니다. 

용량 예약의 예를 살펴보겠습니다.

<center>

![예약 된 용량을 정의 하는 응용 프로그램 인스턴스][Image2]
</center>

왼쪽 예에서 애플리케이션은 정의된 애플리케이션 용량이 없습니다. Cluster Resource Manager는 기본 규칙에 따라 모든 항목의 균형을 유지합니다.

오른쪽의 예제에서는 Application1을 다음 설정으로 만들었다고 가정하겠습니다.

- MinimumNodes 2로 설정
- 다음으로 정의된 애플리케이션 메트릭
  - 20인 NodeReservationCapacity

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric은 2개의 노드에 Application1용으로 용량을 예약하고, 해당 시점에 Application1 내의 서비스에서 소비하는 부하가 없더라도 Application2의 서비스가 해당 용량을 소비하도록 허용하지 않습니다. 이 예약된 애플리케이션 용량은 해당 노드 및 클러스터 내에서 남은 용량으로 소비되고 계산될 것으로 간주됩니다.  예약된 용량은 나머지 클러스터 용량에서 즉시 차감되지만, 하나 이상의 서비스 개체가 배치된 경우에만 특정 노드의 용량에서 예약된 소비량이 차감됩니다. 이후 예약을 사용하면 리소스가 노드에서 필요할 때만 예약되므로 유연성과 리소스 사용률을 개선할 수 있습니다.

## <a name="obtaining-the-application-load-information"></a>애플리케이션 부하 정보 얻기
하나 이상의 메트릭에 대해 정의된 애플리케이션 용량이 정의된 각 애플리케이션의 경우 해당 서비스의 복제본에서 보고된 집계 부하에 대한 정보를 얻을 수 있습니다.

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

ApplicationLoad 쿼리는 애플리케이션에 대해 지정된 애플리케이션 성능에 대한 기본 정보를 반환합니다. 이 정보에는 최소 노드와 최대 노드 정보 및 애플리케이션이 현재 사용하고 있는 노드 수가 포함됩니다. 다음을 비롯한 각 애플리케이션 부하 메트릭에 대한 정보도 포함합니다.

* 메트릭 이름: 메트릭의 이름입니다.
* 예약 용량: 이 응용 프로그램에 대 한 클러스터에 예약 된 클러스터 용량입니다.
* 응용 프로그램 부하: 이 응용 프로그램 자식 복제본의 총 부하입니다.
* 응용 프로그램 용량: 최대 응용 프로그램 부하의 값을 허용 합니다.

## <a name="removing-application-capacity"></a>애플리케이션 용량 삭제
애플리케이션 용량 매개 변수가 애플리케이션에 대해 설정되면 업데이트 애플리케이션 API 또는 PowerShell cmdlet을 사용하여 제거할 수 있습니다. 예를 들면 다음과 같습니다.

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

이 명령은 애플리케이션 인스턴스에서 모든 애플리케이션 용량 관리 매개 변수를 제거합니다. 여기에는 MinimumNodes, MaximumNodes 및 애플리케이션의 메트릭(있는 경우)이 포함됩니다. 명령은 즉시 적용됩니다. 이 명령이 완료되면 Cluster Resource Manager는 애플리케이션을 관리하는 기본 동작을 사용합니다. `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`을 통해 애플리케이션 용량 매개 변수를 다시 지정할 수 있습니다.

### <a name="restrictions-on-application-capacity"></a>애플리케이션 용량에 대한 제한 사항
고려해야 할 애플리케이션 용량 매개 변수에 대한 몇 가지 제한 사항이 있습니다. 유효성 검사 오류가 있는 경우 아무 것도 변경되지 않습니다.

- 모든 정수 매개 변수는 음수가 아닌 숫자여야 합니다.
- MinimumNodes는 MaximumNodes보다 클 수 없습니다.
- 부하 메트릭의 용량을 정의한 경우 다음 규칙을 따라야 합니다.
  - 노드 예약 용량은 최대 노드 용량보다 크지 않아야 합니다. 예를 들어, 노드의 메트릭 "CPU"의 용량을 2단위로 제한하고 각 노드에 3단위를 예약하려고 시도할 수 없습니다.
  - MaximumNodes를 지정하는 경우 MaximumNodes의 제품 및 최대 노드 용량은 총 애플리케이션 용량보다 크지 않아야 합니다. 예를 들어, 부하 메트릭 "CPU"의 최대 노드 용량을 8로 설정했다고 가정하겠습니다. 또한 최대 노드를 10으로 설정했다고 가정하겠습니다. 이 경우 이 부하 메트릭에 대해 애플리케이션 총 용량은 80보다 커야 합니다.

애플리케이션 생성 및 업데이트 동안 이러한 제한이 둘 다 적용됩니다.

## <a name="how-not-to-use-application-capacity"></a>애플리케이션 용량을 사용하지 않는 방법
- 애플리케이션 그룹 기능을 사용하여 애플리케이션을 노드의 _특정_ 하위 집합으로 제한하지 않습니다. 즉, 애플리케이션을 최대 다섯 개의 노드에서 실행하도록 지정할 수 있지만 클러스터에서 어떤 특정 다섯 개의 노드인지 지정할 수 없습니다. 서비스에 대한 배치 제약 조건을 사용하여 애플리케이션 특정 노드로 제한할 수 있습니다.
- 동일한 애플리케이션의 두 서비스가 같은 노드에 배치될 수 있도록 애플리케이션 용량을 사용하려고 하지 않습니다. 대신 [선호도](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) 또는 [배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 사용합니다.

## <a name="next-steps"></a>다음 단계
- 서비스 구성에 대한 자세한 내용은 [서비스 구성에 대한 자세한 정보](service-fabric-cluster-resource-manager-configure-services.md)에서 알아봅니다.
- 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
- 처음부터 시작 및 [서비스 패브릭 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)
- 메트릭이 일반적으로 작동하는 방식에 대한 자세한 내용은 [서비스 패브릭 부하 메트릭](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
