---
title: Azure Service Fabric 자동 크기 조정 서비스 및 컨테이너 | Microsoft Docs
description: Azure Service Fabric을 사용하면 서비스 및 컨테이너에 대해 자동 크기 조정 정책을 설정할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: radicmilos
manager: ''
editor: nipuzovi
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2018
ms.author: miradic
ms.openlocfilehash: 8e57c071c9fd93a8581d574aeec2b23b38b3ab95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844026"
---
# <a name="introduction-to-auto-scaling"></a>자동 크기 조정 소개
자동 크기 조정은 서비스가 보고하는 로드 또는 리소스의 사용량에 따라 서비스를 동적으로 조정하는 Service Fabric의 추가 기능입니다. 자동 크기 조정 기능은 뛰어난 탄력성을 제공하며 필요에 따라 서비스의 인스턴스 또는 파티션을 추가로 프로비전할 수 있습니다. 전체 자동 크기 조정 프로세스는 자동화되고 투명합니다. 서비스에 정책을 설정하면 서비스 수준에서 수동으로 크기 조정 작업을 수행할 필요가 없습니다. 자동 크기 조정은 서비스 생성 시 또는 서비스를 업데이트하여 언제든지 설정할 수 있습니다.

자동 크기 조정이 유용한 일반적인 시나리오는 특정 서비스의 로드가 시간에 따라 변하는 경우입니다. 예를 들어, 게이트웨이와 같은 서비스는 들어오는 요청을 처리하는 데 필요한 리소스의 양에 따라 크기 조정할 수 있습니다. 이러한 크기 조정 규칙이 어떻게 생겼는지에 대한 예를 살펴보겠습니다.
* 내 게이트웨이의 모든 인스턴스가 평균적으로 두 개를 초과하는 코어를 사용하는 경우 한 개를 초과하는 인스턴스를 추가하여 게이트웨이 서비스를 규모 확장합니다. 매시간 이 작업을 수행하지만 총 인스턴스 수가 7개를 초과하지 않아야 합니다.
* 게이트웨이의 모든 인스턴스가 평균적으로 0.5개 미만의 코어를 사용하는 경우 하나의 인스턴스를 제거하여 서비스 크기를 조정합니다. 매시간 이 작업을 수행하지만 총 인스턴스 수가 3개 미만이어서는 안됩니다.

자동 크기 조정은 컨테이너와 일반 Service Fabric 서비스 모두에서 지원됩니다. 자동 크기 조정을 사용하기 위해 Service Fabric 런타임 버전 6.2 이상에서 실행해야 합니다. 

이 문서의 나머지 부분에서는 크기 조정 정책, 자동 크기 조정을 사용하거나 사용하지 않도록 설정하는 방법 및 이 기능을 사용하는 방법에 대한 예제를 제공합니다.

## <a name="describing-auto-scaling"></a>자동 크기 조정 설명
자동 크기 조정 정책은 Service Fabric 클러스터의 각 서비스에 대해 정의할 수 있습니다. 각 크기 조정 정책은 두 부분으로 구성됩니다.
* **Scaling 트리거**는 서비스 크기 조정이 수행되는 시기에 대해 설명합니다. 트리거에 정의된 조건은 서비스의 크기를 조정해야 하는지 여부를 결정하기 위해 주기적으로 확인됩니다.

* **크기 조정 메커니즘**은 크기 조정이 트리거될 때 수행되는 방법을 설명합니다. 메커니즘은 트리거의 조건이 충족될 때만 적용됩니다.

현재 지원되는 모든 트리거는 [논리적 로드 메트릭](service-fabric-cluster-resource-manager-metrics.md), CPU 또는 메모리 사용량과 같은 물리적 메트릭으로 작동합니다. 어느 쪽이든 Service Fabric은 메트릭에 대해 보고된 로드를 모니터링하고 트리거를 정기적으로 평가하여 스케일링이 필요한지 여부를 결정합니다.

현재 자동 크기 조정에 대해 지원되는 메커니즘에는 두 가지가 있습니다. 첫 번째 메커니즘은 [인스턴스](service-fabric-concepts-replica-lifecycle.md)를 추가하거나 제거하여 자동 크기 조정을 수행하는 상태 비저장 서비스 또는 컨테이너에 사용됩니다. 상태 저장 서비스와 상태 비저장 서비스는 모두 서비스의 명명된 [파티션](service-fabric-concepts-partitioning.md)을 추가하거나 제거하여 자동 크기 조정을 수행할 수도 있습니다.

> [!NOTE]
> 현재 서비스당 하나의 크기 조정 정책 및 크기 조정 정책당 하나의 크기 조정 트리거만 지원됩니다.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>인스턴스 기반 크기 조정을 사용하는 평균 파티션 로드 트리거
첫 번째 트리거 형식은 상태 비저장 서비스 파티션에서 인스턴스의 로드를 기반으로 합니다. 메트릭 로드는 먼저 파티션의 모든 인스턴스에 대한 로드를 얻기 위해 다듬어진 다음, 파티션의 모든 인스턴스에서 이러한 값의 평균이 구해집니다. 서비스의 크기 조정 시기를 결정하는 세 가지 요소가 있습니다.

* _하위 로드 임계값_은 서비스가 **규모 감축**되는 시기를 결정하는 값입니다. 파티션에 있는 모든 인스턴스의 평균 로드가 이 값 미만이면 서비스가 규모 감축됩니다.
* _상위 로드 임계값_은 서비스가 **규모 확장**되는 시기를 결정하는 값입니다. 파티션에 있는 모든 인스턴스의 평균 로드가 이 값보다 높으면 서비스가 규모 확장됩니다.
* _크기 조정 간격_은 트리거를 검사할 빈도를 결정합니다. 트리거를 확인하여 크기 조정이 필요한 경우 메커니즘이 적용됩니다. 크기 조정이 필요하지 않으면 아무 작업도 수행되지 않습니다. 두 경우 모두, 크기 조정 간격이 다시 만료되기 전에는 트리거가 다시 확인되지 않습니다.

이 트리거는 상태 비저장 서비스(상태 비저장 컨테이너 또는 Service Fabric 서비스)에서만 사용할 수 있습니다. 서비스에 여러 파티션이 있는 경우 트리거는 각 파티션에 대해 개별적으로 평가되고 각 파티션에는 독립적으로 적용되는 지정된 메커니즘이 포함됩니다. 따라서 이 경우 서비스의 일부 파티션은 규모 확장되고 일부는 규모 감축되며 일부는 로드에 따라 동시에 크기 조정되지 않을 수 있습니다.

이 트리거와 함께 사용할 수 있는 유일한 메커니즘은 PartitionInstanceCountScaleMechanism입니다. 이 메커니즘이 적용되는 방식을 결정하는 세 가지 요소가 있습니다.
* _눈금 증가_는 메커니즘이 트리거될 때 추가 또는 제거할 인스턴스 수를 결정합니다.
* _최대 인스턴스 수_는 크기 조정의 상한을 정의합니다. 파티션의 인스턴스 수가 이 한계에 도달하면 로드에 관계없이 서비스가 규모 확장되지 않습니다. 값 -1을 지정하여 이 제한을 생략할 수 있으며, 이 경우 서비스는 가능한 최대로 규모 확장됩니다(한계는 클러스터에서 사용 가능한 노드 수).
* _최소 인스턴스 수_는 크기 조정의 하한을 정의합니다. 파티션의 인스턴스 수가 이 한계에 도달하면 로드에 관계없이 서비스가 규모 감축되지 않습니다.

## <a name="setting-auto-scaling-policy"></a>자동 크기 조정 정책 설정

### <a name="using-application-manifest"></a>애플리케이션 매니페스트 사용
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API 사용
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>Powershell 사용
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>파티션 기반 크기 조정을 사용하는 평균 서비스 로드 트리거
두 번째 트리거는 한 서비스에 있는 모든 파티션의 로드를 기반으로 합니다. 메트릭 로드는 먼저 파티션의 모든 복제본 또는 인스턴스에 대한 로드를 얻기 위해 다듬어집니다. 상태 저장 서비스의 경우, 파티션의 로드는 주 복제본의 로드로 간주되는 반면, 상태 비저장 서비스의 경우, 파티션의 로드는 파티션에 있는 모든 인스턴스의 평균 로드입니다. 서비스의 모든 파티션에서 이러한 값의 평균이 구해지며 이 값은 자동 크기 조정을 트리거하는 데 사용됩니다. 이전 메커니즘과 마찬가지로 서비스의 크기 조정 시기를 결정하는 세 가지 요소가 있습니다.

* _하위 로드 임계값_은 서비스가 **규모 감축**되는 시기를 결정하는 값입니다. 서비스에 있는 모든 파티션의 평균 로드가 이 값 미만이면 서비스가 규모 감축됩니다.
* _상위 로드 임계값_은 서비스가 **규모 확장**되는 시기를 결정하는 값입니다. 서비스에 있는 모든 파티션의 평균 로드가 이 값을 초과하면 서비스가 규모 확장됩니다.
* _크기 조정 간격_은 트리거를 검사할 빈도를 결정합니다. 트리거를 확인하여 크기 조정이 필요한 경우 메커니즘이 적용됩니다. 크기 조정이 필요하지 않으면 아무 작업도 수행되지 않습니다. 두 경우 모두, 크기 조정 간격이 다시 만료되기 전에는 트리거가 다시 확인되지 않습니다.

이 트리거는 상태 저장 및 상태 비저장 서비스와 함께 사용할 수 있습니다. 이 트리거와 함께 사용할 수 있는 유일한 메커니즘은 AddRemoveIncrementalNamedPartitionScalingMechanism입니다. 서비스가 규모 확장되면 새 파티션이 추가되고 서비스가 규모 감축되면 기존 파티션 중 하나가 제거됩니다. 서비스를 만들거나 업데이트할 때 확인되는 제한 사항이 있으며 이러한 조건이 충족되지 않으면 서비스 만들기/업데이트가 실패합니다.
* 명명된 파티션 구성표가 해당 서비스에 사용되어야 합니다.
* 파티션 이름은 “0”, “1” 등과 같이 연속된 정수여야 합니다.
* 첫 번째 파티션 이름은 “0”이어야 합니다.

예를 들어, 처음에 세 개의 파티션이 있는 서비스를 만든 경우 파티션 이름으로 가능한 것은 “0”, “1” 및 “2”뿐입니다.

수행되는 실제 자동 크기 조정 작업은 다음 이름 지정 체계를 따릅니다.
* 서비스의 현재 파티션 이름이 “0”, “1” 및 “2”이면 규모 확장을 위해 추가될 파티션 이름은 “3”입니다.
* 서비스의 현재 파티션 이름이 “0”, “1” 및 “2”이면 규모 감축을 위해 제거될 파티션은 이름이 “2”인 파티션입니다.

인스턴스를 추가하거나 제거하여 크기 조정을 사용하는 메커니즘과 마찬가지로 이 메커니즘이 적용되는 방식을 결정하는 세 가지 요소가 있습니다.
* _눈금 증가_는 메커니즘이 트리거될 때 추가 또는 제거할 파티션 수를 결정합니다.
* _최대 파티션 수_는 크기 조정의 상한을 정의합니다. 서비스의 파티션 수가 이 한계에 도달하면 로드에 관계없이 서비스가 규모 확장되지 않습니다. 값 -1을 지정하여 이 제한을 생략할 수 있으며, 이 경우 서비스는 가능한 최대로 규모 확장됩니다(한계는 클러스터의 실제 용량).
* _최소 인스턴스 수_는 크기 조정의 하한을 정의합니다. 서비스의 파티션 수가 이 한계에 도달하면 로드에 관계없이 서비스가 규모 감축되지 않습니다.

> [!WARNING] 
> AddRemoveIncrementalNamedPartitionScalingMechanism을 상태 저장 서비스와 함께 사용하면 Service Fabric이 **알림 또는 경고 없이** 파티션을 추가 또는 제거합니다. 크기 조정 메커니즘이 트리거되면 데이터의 다시 분할이 수행되지 않습니다. 강화 작업의 경우 새 파티션은 비어 있고 규모 축소 작업의 경우 **파티션은**포함된 모든 데이터와 함께 삭제됩니다.

## <a name="setting-auto-scaling-policy"></a>자동 크기 조정 정책 설정

### <a name="using-application-manifest"></a>애플리케이션 매니페스트 사용
``` xml
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API 사용
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>Powershell 사용
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>리소스에 따라 자동 크기 조정

실제 리소스에 따라 크기 조정하도록 리소스 모니터 서비스를 설정하기 위해

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
실제 물리적 리소스를 나타내는 두 가지 메트릭이 있습니다. 그 중 하나인 servicefabric:/_CpuCores는 실제 CPU 사용량을 나타내며(따라서 0.5는 코어의 절반을 나타냄) 다른 하나는 servicefabric:/_MemoryInMB로 메모리 사용량(MB)을 나타냅니다.
ResourceMonitorService는 사용자 서비스의 cpu 및 메모리 사용량을 추적하는 일을 담당합니다. 이 서비스는 잠재적인 일시적 급증을 설명하기 위해 가중 이동 평균을 적용합니다. 리소스 모니터링은 Windows의 컨테이너화 및 컨테이너화되지 않은 애플리케이션과 Linux의 컨테이너화된 애플리케이션 모두에서 지원됩니다. 리소스에 대한 자동 크기 조정은 [단독 프로세스 모델](service-fabric-hosting-model.md#exclusive-process-model)에서 활성화된 서비스에만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 자세히 알아봅니다.
