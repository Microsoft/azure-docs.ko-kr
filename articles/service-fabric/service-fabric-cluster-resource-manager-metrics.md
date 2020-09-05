---
title: 메트릭을 사용 하 여 Azure Service Fabric 앱 로드 관리
description: 서비스 리소스 소비를 관리하기 위해 Service Fabric에서 메트릭을 구성하고 사용하는 방법에 대해 알아봅니다.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 3cb22bc2cd032e51dcdb7429e2c0684c578b0870
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005652"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리
*메트릭*은 서비스에서 관심을 갖고 클러스터의 노드에서 제공하는 리소스입니다. 메트릭은 서비스 성능을 향상시키거나 모니터링하기 위해 관리하려는 모든 항목입니다. 예를 들어 메모리 사용량을 조사하여 서비스가 오버로드 상태인지 여부를 확인할 수 있습니다. 또 다른 용도는 더 나은 성능을 얻기 위해 메모리 제약이 심하지 않은 위치로 서비스를 이동할 수 있는지 여부를 파악하는 것입니다.

메모리, 디스크, CPU 사용량 등이 모두 메트릭의 예입니다. 이러한 메트릭은 노드에서 관리해야 하는 물리적 리소스에 해당하는 물리적 메트릭입니다. 메트릭은 논리 메트릭이 될 수도 있으며 이것이 일반적인 경우이기도 합니다. “MyWorkQueueDepth”, "MessagesToProcess" 또는 "TotalRecords" 등이 논리 메트릭입니다. 논리 메트릭은 애플리케이션에서 정의하며 간접적으로 일부 물리적 리소스 사용량에 해당합니다. 서비스별로 물리적 리소스 사용량을 측정하고 보고하는 것이 어려울 수 있기 때문에 논리 메트릭이 일반적입니다. 사용자 고유의 메트릭을 측정하고 보고하는 복잡성은 Service Fabric에서 몇 가지 기본 메트릭을 제공하는 이유이기도 합니다.

## <a name="default-metrics"></a>기본 메트릭
서비스를 작성하고 배포하기 시작한다고 가정해 보겠습니다. 이 시점에서는 어떤 물리적 또는 논리적 리소스를 사용하는지 알 수 없습니다. 이것으로 끝입니다. 다른 메트릭이 지정되지 않은 경우 Service Fabric 클러스터 리소스 관리자에서는 일부 기본 메트릭을 사용합니다. 해당 항목은 다음과 같습니다.

  - PrimaryCount - 노드의 주 복제본 수 
  - ReplicaCount - 노드의 총 상태 저장 복제본 수
  - Count - 노드의 모든 서비스 개체(상태 비저장 및 상태 저장) 수

| 메트릭 | 상태 비저장 인스턴스 부하 | 상태 저장 보조 부하 | 상태 저장 기본 부하 | 무게 |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |높음 |
| ReplicaCount |0 |1 |1 |중간 |
| 개수 |1 |1 |1 |낮음 |


기본 워크로드의 경우 기본 메트릭은 클러스터에서 적절한 작업 분산을 제공합니다. 다음 예에서는 두 서비스를 만들고 분산에 대해 기본 메트릭을 사용할 때 어떤 상황이 발생하는지 확인해 보겠습니다. 첫 번째 서비스는 3개의 파티션과 3개의 대상 복제본 세트 크기가 있는 상태 저장 서비스입니다. 두 번째는 하나의 파티션과 3개의 인스턴스 수가 있는 상태 비저장 서비스입니다.

결과는 다음과 같습니다.

<center>

![기본 메트릭으로 클러스터 레이아웃][Image1]
</center>

유의 사항:
  - 상태 저장 서비스에 대한 주 복제본은 여러 노드에 분산됩니다.
  - 동일한 파티션에 대한 복제본은 다른 노드에 있습니다
  - 기본 복제본 및 보조 복제본의 총 수는 클러스터에 배포됩니다
  - 서비스 개체의 총 수는 각 노드에 균등하게 할당됩니다.

좋습니다.

기본 메트릭은 처음부터 잘 작동하지만 지금까지만 수행됩니다. 예를 들어 선택한 파티션 구성표가 모든 파티션에서 완벽하게 활용되는 결과는 어떻게 될까요? 시간이 지남에 따라 지정된 서비스에 대한 로드가 일정하거나 심지어 여러 파티션에서 지금 동일하게 나타날 가능성은 얼마나 될까요?

기본 메트릭만으로 실행할 수 있습니다. 그러나 이렇게 하면 일반적으로 클러스터 사용률이 원하는 것보다 낮고 균등하지 않습니다. 이는 기본 메트릭이 적응되지 않으며 모든 것이 동등하다고 가정하기 때문입니다. 예를 들어 사용 중인 주 복제본과 사용되지 않는 복제본이 있으며, 둘 다 PrimaryCount 메트릭에 "1"을 제공하지 않습니다. 최악의 경우 기본 메트릭만 사용하면 노드를 과도하게 예약하여 성능 문제가 발생할 수 있습니다. 클러스터를 최대한 활용하고 성능 문제를 방지하는 데 관심이 있는 경우 사용자 지정 메트릭 및 동적 로드 보고를 사용해야 합니다.

## <a name="custom-metrics"></a>사용자 지정 메트릭
메트릭은 서비스를 만들 때 지정된 서비스 인스턴스별로 구성됩니다.

모든 메트릭에는 자체에 대해 설명하는 이름,가중치 및 기본 로드와 같은 몇 가지 속성이 있습니다.

* 메트릭 이름: 메트릭의 이름입니다. 메트릭 이름은 Resource Manager의 관점에서 클러스터 내의 메트릭에 대한 고유한 식별자입니다.
* Weight: 메트릭 가중치는 이 서비스에 대한 다른 메트릭에 상대적으로 이 메트릭이 갖는 중요도를 정의합니다.
* 기본 부하: 기본 부하는 서비스가 상태 비저장 서비스인지 또는 상태 저장 서비스인지에 따라 다르게 표현됩니다.
  * 상태 비저장 서비스의 경우 각 메트릭에는 DefaultLoad라는 단일 속성만 있습니다.
  * 상태 저장 서비스의 경우 다음과 같이 정의합니다.
    * PrimaryDefaultLoad: 이 서비스가 기본일 때 소비하는 이 메트릭의 기본 크기입니다.
    * SecondaryDefaultLoad: 이 서비스가 보조일 때 소비하는 이 메트릭의 기본 크기입니다.

> [!NOTE]
> 사용자 지정 메트릭을 정의하고 _기본 메트릭도_ 사용하려면 해당 기본 메트릭을 _명시적으로_ 다시 추가하고 이에 대한 가중치와 값을 정의해야 합니다. 이는 기본 메트릭과 사용자 지정 메트릭 간의 관계를 정의해야 하기 때문입니다. 예를 들어 ConnectionCount 또는 WorkQueueDepth에 대한 관심이 기본 배포 이상일 수 있습니다. 기본적으로 PrimaryCount 메트릭의 가중치는 높음이므로 다른 메트릭을 추가할 때 이를 우선적으로 사용하기 위해 중간으로 낮추려고 합니다.
>

### <a name="defining-metrics-for-your-service---an-example"></a>서비스에 대한 메트릭 정의 - 예
다음 구성을 원한다고 가정해 보겠습니다.

  - 서비스에서 "ConnectionCount"라는 메트릭을 보고합니다.
  - 기본 메트릭도 사용하려고 합니다. 
  - 몇 가지 측정을 수행했으며 일반적으로 해당 서비스의 주 복제본에서 20단위의 "ConnectionCount"를 차지한다는 것을 알고 있습니다.
  - 보조 복제본에서 5단위의 "ConnectionCount"를 사용합니다.
  - "ConnectionCount"가 이러한 특정 서비스의 성능 관리 측면에서 가장 중요한 메트릭입니다.
  - 여전히 분산된 주 복제본을 원합니다. 주 복제본의 분산이 무엇이든 간에 일반적으로 좋은 아이디어입니다. 이렇게 하면 일부 노드 또는 장애 도메인의 손실로 인해 대다수의 주 복제본이 영향을 받지 않도록 방지할 수 있습니다. 
  - 그렇지 않으면 기본 메트릭이 좋습니다.

이러한 메트릭 구성이 있는 서비스를 만들기 위한 코드는 다음과 같습니다.

코드:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> 위의 예제와 이 문서의 나머지 부분에서는 명명된 서비스별로 메트릭을 관리하는 방법에 대해 설명합니다. 서비스 _유형_ 수준에서 서비스에 대한 메트릭을 정의할 수도 있습니다. 이는 서비스 매니페스트에 지정하여 수행됩니다. 유형 수준 메트릭을 정의하는 것은 여러 가지 이유로 권장되지 않습니다. 첫 번째 이유는 메트릭 이름이 자주 환경별로 지정된다는 것입니다. 확실한 계약이 체결되지 않는 한, 한 환경의 "Cores" 메트릭이 다른 환경의 "MiliCores" 또는 "CoReS"가 아님을 확신할 수 없습니다. 메트릭이 매니페스트에 정의된 경우 환경별로 새 매니페스트를 만들어야 합니다. 이렇게 하면 일반적으로 관리상의 어려움을 초래할 수 있는 사소한 차이만 있는 다양한 매니페스트의 확산으로 이어집니다.  
>
> 메트릭 로드는 일반적으로 명명된 서비스 인스턴스별로 할당됩니다. 예를 들어 가끔씩 사용하려고 하는 CustomerA에 대해 하나의 서비스 인스턴스를 만든다고 가정해 보겠습니다. 더 큰 워크로드를 가지고 있는 CustomerB에 대해서도 다른 하나의 서비스 인스턴스를 만든다고 가정해 보겠습니다. 이 경우 이러한 서비스에 대한 기본 로드를 조정할 수도 있습니다. 매니페스트를 통해 정의된 메트릭과 로드가 있고 이 시나리오를 지원하려는 경우 각 고객마다 다른 애플리케이션 및 서비스 유형이 필요합니다. 서비스를 만들 때 정의된 값은 매니페스트에 정의된 값을 재정의하므로 이러한 값을 사용하여 특정 기본값을 설정할 수 있습니다. 그러나 이렇게 하면 매니페스트에 선언된 값이 실제로 서비스가 실행되는 값과 일치하지 않게 됩니다. 이 경우 혼란이 발생할 수 있습니다. 
>

참고로 기본 메트릭만 사용하려는 경우 서비스를 만들 때 메트릭 컬렉션을 사용하거나 특별한 작업을 수행할 필요가 전혀 없습니다. 다른 메트릭이 정의되지 않은 경우 기본 메트릭이 자동으로 사용됩니다. 

이제 각각의 설정을 자세히 살펴보고 영향을 받는 동작에 대해 설명하겠습니다.

## <a name="load"></a>로드
메트릭 정의의 핵심은 일부 부하에 관한 것입니다. *로드* 는 지정 된 노드의 일부 서비스 인스턴스 또는 복제본에서 사용 되는 지정 된 메트릭의 양입니다. 로드는 거의 모든 지점에서 구성될 수 있습니다. 예를 들어:

  - 서비스를 만들 때 로드를 정의할 수 있습니다. 이를 _기본 로드_라고 합니다.
  - 기본 로드를 포함하여 서비스에 대한 메트릭 정보는 서비스를 만든 후에 업데이트될 수 있습니다. 이를 _서비스 업데이트_라고 합니다. 
  - 지정된 파티션에 대한 로드는 해당 서비스의 기본값으로 다시 설정될 수 있습니다. 이를 _파티션 로드 다시 설정_이라고 합니다.
  - 로드는 런타임에 동적으로 서비스 개체별로 보고될 수 있습니다. 이를 _로드 보고_라고 합니다. 
  
이러한 모든 전략은 해당 수명 동안 동일한 서비스 내에서 사용할 수 있습니다. 

## <a name="default-load"></a>기본 부하
*기본 로드*는 이 서비스의 각 서비스 개체(상태 비저장 인스턴스 또는 상태 저장 복제본)에서 사용하는 메트릭의 양입니다. 클러스터 리소스 관리자는 동적 로드 보고서와 같은 다른 정보를 받을 때까지 서비스 개체의 로드에 이 숫자를 사용합니다. 간단한 서비스의 경우 기본 로드는 정적 정의입니다. 기본 로드는 전혀 업데이트되지 않으며 서비스 수명 동안 사용됩니다. 기본 로드는 여러 워크로드에서 특정 양의 리소스를 전적으로 사용하고 변경하지 않는 간단한 용량 계획 시나리오에 적합합니다.

> [!NOTE]
> 클러스터의 노드에 대한 용량 관리 및 정의에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-cluster-description.md#capacity)를 참조하세요.
> 

클러스터 리소스 관리자를 사용하면 상태 저장 서비스에서 주 복제본 및 보조 복제본 모두에 서로 다른 기본 로드를 지정할 수 있지만, 상태 비저장 서비스에서는 모든 인스턴스에 적용되는 하나의 값만 지정할 수 있습니다. 상태 저장 서비스의 경우 복제본이 각 역할에서 다른 종류의 작업을 수행하기 때문에 일반적으로 주 복제본 및 보조 복제본에 대한 기본 로드는 서로 다릅니다. 예를 들어 주 복제본은 대개 읽기와 쓰기를 모두 수행하며 대부분의 계산 작업을 처리하지만, 보조 복제본은 그렇지 않습니다. 일반적으로 주 복제본의 기본 로드는 보조 복제본의 기본 로드보다 높습니다. 실제의 크기(수)는 사용자 고유의 측정값에 따라 달라집니다.

## <a name="dynamic-load"></a>동적 부하
한동안 서비스를 실행했다고 가정해 보겠습니다. 일부 모니터링에서 다음을 확인했습니다.

1. 일부 파티션 또는 특정 서비스의 인스턴스가 다른 항목보다 더 많은 리소스를 소비합니다.
2. 일부 서비스에 시간 경과에 따라 달라지는 부하가 있습니다.

많은 요소가 이러한 유형의 부하 변동을 야기할 수 있습니다. 예를 들어 서비스 또는 파티션마다 별도의 요구 사항을 가진 서로 다른 고객과 연관됩니다. 하루 종일 서비스에서 수행하는 작업량이 달라지므로 로드가 변경될 수도 있습니다. 대개의 경우 어떤 이유로든 기본 로드에 사용할 수 있는 하나의 수치는 없습니다. 이는 클러스터에서 최대한의 사용률을 얻으려는 경우에 특히 유용합니다. 기본 부하에 선택한 값은 어느 시점에는 맞지 않게 됩니다. 잘못된 기본 로드로 인해 클러스터 리소스 관리자에서 리소스를 너무 많거나 적게 할당하게 됩니다. 결과적으로 클러스터 리소스 관리자에서 클러스터의 로드가 분산된다고 인식하더라도 노드가 너무 많거나 적게 사용됩니다. 기본 로드는 초기 배치에 대한 일부 정보를 제공하기 때문에 여전히 유용하지만 실제 워크로드에 대해 완전하게 알려주지는 않습니다. 변화하는 리소스 요구 사항을 정확하게 캡처하기 위해 런타임에 각 서비스 개체에서 클러스터 리소스 관리자를 통해 자체의 로드를 업데이트할 수 있습니다. 이를 동적 부하 보고라 합니다.

동적 부하 보고서를 사용하면 복제본 또는 인스턴스가 자신의 수명 동안 메트릭의 할당/보고된 부하를 조정할 수 있습니다. 콜드 상태이며 아무 작업도 수행하지 않는 서비스 복제본 또는 인스턴스는 보통 특정 메트릭을 적게 사용했다고 보고합니다. 사용 중인 복제본 또는 인스턴스는 더 많은 양을 사용 중이라 보고할 것입니다.

복제본 또는 인스턴스별로 로드를 보고하면 클러스터 리소스 관리자에서 클러스터의 개별 서비스 개체를 다시 구성할 수 있습니다. 서비스를 다시 구성하면 필요한 리소스를 얻을 수 있습니다. 사용 중인 서비스는 현재 콜드 상태이거나 작업이 적은 다른 복제본 또는 인스턴스로부터 리소스를 효과적으로 "회수"할 수 있습니다.

Reliable Services 내에서 로드를 동적으로 보고하는 코드는 다음과 같습니다.

코드:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

서비스는 생성 시 정의된 메트릭에 대해 보고할 수 있습니다. 서비스에서 사용하도록 구성되지 않은 메트릭에 대한 로드를 보고하는 경우 Service Fabric에서는 해당 보고서를 무시합니다. 유효한 다른 메트릭이 동시에 보고되는 경우 해당 보고서가 수락됩니다. 서비스 코드는 사용 방법을 인식하고 있는 모든 메트릭을 측정하여 보고할 수 있으며, 작업자는 서비스 코드를 변경하지 않고도 사용할 메트릭 구성을 지정할 수 있습니다. 

### <a name="updating-a-services-metric-configuration"></a>서비스의 메트릭 구성 업데이트
서비스와 관련된 메트릭 목록과 해당 메트릭의 속성은 서비스가 라이브 상태로 있는 동안 동적으로 업데이트될 수 있습니다. 이렇게 하면 실험과 유연성을 허용합니다. 유용한 경우의 몇 가지 예는 다음과 같습니다.

  - 특정 서비스에 대한 버그가 포함된 보고서로 메트릭 비활성화
  - 원하는 동작을 기반으로 하여 메트릭의 가중치 재구성
  - 코드가 이미 배포되고 다른 메커니즘을 통해 유효성이 검사된 후에만 새 메트릭 활성화
  - 관찰된 동작 및 사용량을 기반으로 하여 서비스에 대한 기본 로드 변경

메트릭 구성을 변경하기 위한 주요 API는 C#의 경우 `FabricClient.ServiceManagementClient.UpdateServiceAsync`이고, PowerShell의 경우 `Update-ServiceFabricService`입니다. 이러한 API로 지정한 모든 정보는 서비스의 기존 메트릭 정보를 즉시 바꿉니다. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>기본 부하 값 및 동적 부하 보고서 혼합
기본 로드 및 동적 로드는 동일한 서비스에 사용할 수 있습니다. 서비스에서 기본 로드 및 동적 로드 보고서를 모두 사용하는 경우 동적 보고서가 표시될 때까지 기본 로드가 추정값으로 사용됩니다. 기본 로드는 클러스터 리소스 관리자에 사용할 항목을 제공하므로 유용합니다. 기본 로드를 사용하면 클러스터 리소스 관리자에서 서비스 개체를 만들 때 적절한 위치에 배치할 수 있습니다. 기본 로드 정보를 제공하지 않으면 서비스 배치가 사실상 무작위로 수행됩니다. 나중에 로드 보고서가 도착하면 종종 초기 무작위 배치가 잘못되어 클러스터 리소스 관리자에서 서비스를 이동해야 합니다.

이전 예제에서 몇 가지 사용자 지정 메트릭과 동적 부하 보고를 추가하면 어떻게 되는지 살펴보겠습니다. 이 예제에서는 "MemoryInMb"를 예제 메트릭으로 사용합니다.

> [!NOTE]
> Memory는 Service Fabric에서 [리소스 관리](service-fabric-resource-governance.md)를 수행할 수 있는 시스템 메트릭 중 하나이며 일반적으로 자신에 대해 보고하는 것이 어렵습니다. 실제로 사용자가 메모리 사용량에 대해 보고할 것이라고 기대하지 않으며, 여기서 Memory는 클러스터 리소스 관리자의 기능을 학습하기 위한 보조 수단으로 사용됩니다.
>

다음 명령을 사용하여 상태 저장 서비스를 처음 만들었다고 가정하겠습니다.

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

참고로, 이 구문은 ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad")입니다.

가능한 클러스터 레이아웃의 모양을 살펴보겠습니다.

<center>

![기본 및 사용자 지정 메트릭으로 클러스터 부하 분산][Image2]
</center>

주목할 만한 몇 가지가 있습니다.

* 파티션 내의 보조 복제본은 각각 고유한 로드를 가질 수 있습니다.
* 전반적으로 메트릭은 분산된 것처럼 보입니다. Memory의 경우 최대 로드와 최소 로드 사이의 비율은 1.75입니다(로드가 가장 많은 노드는 N3이고, 가장 적은 노드는 N2이므로 28/16 = 1.75).

다음과 같은 몇 가지에 대한 설명이 필요합니다.

* 1.75의 비율이 합리적인지를 무엇이 결정했나요? Cluster Resource Manager는 충분한지 또는 추가 작업이 필요한지를 어떻게 확인하나요?
* 언제 부하가 분산되나요?
* 메모리의 가중치가 "높음"은 무슨 의미인가요?

## <a name="metric-weights"></a>메트릭 가중치
여러 서비스에서 동일한 메트릭을 추적하는 것이 중요합니다. 이 전역 보기를 통해 클러스터 리소스 관리자에서 클러스터의 사용량을 추적하고, 노드 간에 사용량을 분산하며, 노드에서 용량을 초과하지 않도록 보장할 수 있습니다. 그러나 서비스에서 동일한 메트릭의 중요도에 따라 서로 다른 보기가 있을 수 있습니다. 또한 메트릭과 서비스가 많은 클러스터에서는 모든 메트릭에 대해 완벽하게 분산된 솔루션이 없을 수도 있습니다. Cluster Resource Manager는 이러한 상황을 어떻게 처리해야 할까요?

메트릭 가중치를 통해 Cluster Resource Manager는 완벽한 답이 없을 때 클러스터를 분산하는 방법을 결정할 수 있습니다. 메트릭 가중치를 통해 Cluster Resource Manager가 특정 서비스를 다르게 분산할 수도 있습니다. 메트릭은 0, 낮음, 보통, 높음 등 네 가지 가중치 수준을 사용할 수 있습니다. 작업이 분산되는지 여부를 고려할 때 가중치가 0인 메트릭은 작업에 아무런 영향을 주지 않습니다. 그러나 로드는 용량 관리에 여전히 영향을 줍니다. 가중치가 0인 메트릭은 여전히 유용하며, 서비스 동작 및 성능 모니터링의 일부로 자주 사용됩니다. [이 문서](service-fabric-diagnostics-event-generation-infra.md)에서는 서비스 모니터링 및 진단을 위해 메트릭을 사용하는 방법에 대해 자세히 설명합니다. 

클러스터에서 서로 다른 메트릭 가중치의 실제 영향은 Cluster Resource Manager가 서로 다른 솔루션을 생성한다는 것입니다. 메트릭 가중치는 Cluster Resource Manager에게 특정 메트릭이 다른 메트릭보다 더 중요함을 알려 줍니다. 완벽한 솔루션이 없으면 Cluster Resource Manager는 가중치가 더 높은 메트릭을 더 잘 분산하는 솔루션을 선호합니다. 서비스에서 특정 메트릭이 중요하지 않다고 인식하면 해당 메트릭의 사용이 분산되지 않았다고 확인할 수 있습니다. 이렇게 하면 다른 서비스에서 중요한 메트릭을 균등하게 분산할 수 있습니다.

일부 로드 보고서의 예와 다른 메트릭 가중치에 따라 클러스터에서 다른 할당을 수행하게 되는 방식을 살펴보겠습니다. 이 예에서 메트릭의 상대 가중치를 전환하면 클러스터 리소스 관리자에서 서비스의 다른 정렬을 만들게 됩니다.

<center>

![메트릭 가중치 예제 및 부하 분산 솔루션에 미치는 영향][Image3]
</center>

이 예에서는 별도의 두 메트릭인 메트릭 A 및 메트릭 B에 대해 모두 서로 다른 값을 보고하는 별도의 4가지 서비스가 있습니다. 한 경우에는 서비스에서 메트릭 A가 가장 중요한 것(가중치 = 높음)이고, 메트릭 B는 중요하지 않은 것(가중치 = 낮음)으로 정의합니다. 결과적으로 클러스터 리소스 관리자에서 메트릭 A가 메트릭 B보다 더 잘 분산되도록 서비스를 배치합니다. "더 잘 분산됨"은 메트릭 B보다 낮은 표준 편차가 메트릭 A에 있음을 의미합니다. 두 번째 경우는 메트릭 가중치를 반대로 합니다. 결과적으로 클러스터 리소스 관리자에서 서비스 A와 서비스 B를 교환하여 메트릭 B가 메트릭 A보다 더 잘 분산되는 할당을 만들게 됩니다.

> [!NOTE]
> 메트릭 가중치는 클러스터 리소스 관리자에서 분산이 발생하는 경우가 아니라 분산하는 방법을 결정합니다. 분산에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-balancing.md)를 확인하세요.
>

### <a name="global-metric-weights"></a>전역 메트릭 가중치
서비스 A에서 메트릭 A를 가중치 높음으로 정의하고, 서비스 B에서 메트릭 A의 가중치를 낮음 또는 영(0)으로 설정한다고 가정해 보겠습니다. 사용하게 되는 실제 가중치는 무엇일까요?

모든 메트릭에 대해 추적되는 여러 가중치가 있습니다. 첫 번째 가중치는 서비스를 만들 때 메트릭에 대해 정의된 가중치입니다. 다른 가중치는 자동으로 계산되는 전역 가중치입니다. 클러스터 리소스 관리자는 솔루션의 점수를 매길 때 이러한 두 가중치를 모두 사용합니다. 두 가중치를 모두 고려해야 합니다. 이렇게 하면 클러스터 리소스 관리자에서 자체의 우선 순위에 따라 각 서비스를 분산하고 클러스터 전체가 올바르게 할당될 수 있습니다.

Cluster Resource Manager가 전역 및 로컬 분산에 대해 고려하지 않는다면 어떤 상황이 발생하나요? 전역적으로 분산된 솔루션을 구축하는 것은 쉽지만 개별 서비스에 대한 리소스 분산이 제대로 수행되지 않을 수 있습니다. 다음 예에서는 기본 메트릭만으로 구성된 서비스를 살펴보고, 전역 분산만 고려할 때 어떤 상황이 발생하는지 알아보겠습니다.

<center>

![전역에만 해당하는 솔루션의 영향][Image4]
</center>

전역 분산에만 기반한 위 예제에서는 실제로 클러스터 전체가 분산되었습니다. 모든 노드에서 기본 복제본의 수와 총 복제본의 수가 동일합니다. 그러나 이 할당의 실제 영향은 그렇게 좋지 않습니다. 모든 주 복제본을 제거하기 때문에 어떤 노드의 손실이 특정 워크로드에 불균형적으로 영향을 미칩니다. 예를 들어, 첫 번째 노드가 실패할 경우 원형 서비스의 3가지 파티션에 대한 3가지 기본 복제본이 모두 손실될 수 있습니다. 반대로 삼각형 서비스와 육각형 서비스의 파티션에서 복제본을 잃게 됩니다. 가동 중지된 복제본을 복구해야 하는 것 외에는 이로 인해 중단이 발생하지 않습니다.

아래쪽 예제에서는 Cluster Resource Manager가 전역 및 서비스별 부하 분산에 따라 복제본을 배포했습니다. 솔루션의 점수를 계산할 때 대부분의 가중치를 전역 솔루션에 부여했고 (구성 가능한) 일부를 개별 서비스에 할당했습니다. 메트릭에 대한 전역 분산은 각 서비스의 메트릭 가중치의 평균에 따라 계산됩니다. 각 서비스는 자체 정의된 메트릭 가중치에 따라 분산됩니다. 이렇게 하면 서비스가 자체의 필요에 따라 자체 내에서 분산됩니다. 결과적으로 동일한 첫 번째 노드가 실패하면 오류가 모든 서비스의 모든 파티션에 분산됩니다. 각각에 대한 영향이 같습니다.

## <a name="next-steps"></a>다음 단계
- 서비스 구성에 대한 자세한 내용은 [서비스 구성](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)에서 알아봅니다.
- 조각 모음 메트릭을 정의 하는 것은 노드를 분산 하는 대신 노드에 로드를 통합 하는 한 가지 방법입니다. 조각 모음을 구성 하는 방법에 대 한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md) 를 참조 하세요.
- 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
- 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
- 이동 비용은 특정 서비스가 다른 서비스에 비해 이동하는 데 비용이 더 많이 드는 것을 클러스터 리소스 관리자에게 알리는 한 가지 방법입니다. 이동 비용에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
