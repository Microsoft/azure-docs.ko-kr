---
title: Azure Service Fabric 용량 계획 및 크기 조정 모범 사례 | Microsoft Docs
description: Service Fabric 클러스터 및 애플리케이션 계획 및 크기 조정을 위한 모범 사례를 소개합니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 425154958e4c60902b56f320f714a011b9095830
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471539"
---
# <a name="capacity-planning-and-scaling"></a>용량 계획 및 크기 조정

Azure Service Fabric 클러스터를 만들거나 클러스터를 호스트하는 컴퓨팅 리소스 크기를 조정하기 전에 용량을 계획해야 합니다. 용량 계획에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요. 용량을 계획할 때는 Nodetype 및 Cluster 특성을 고려해야 할 뿐 아니라, 추가하는 VM 수에 관계없이 프로덕션 환경에서 완료하는 데 1시간 넘게 걸리는 크기 조정 작업도 계획해야 합니다.

## <a name="auto-scaling"></a>자동 크기 조정
크기 조정 작업은 Azure Resource 템플릿 배포를 통해 수행해야 합니다. [리소스 구성은 코드로 처리]( https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)하는 것이 모범 사례이며, Virtual Machine Scale Set 자동 크기 조정을 사용하면 버전이 지정된 Resource Manager 템플릿이 사용자의 가상 머신 확장 집합 인스턴스 수를 잘못 정의하기 때문입니다. 그러면 이후 배포에서 의도하지 않은 크기 조정 작업이 실행될 위험이 높아집니다. 일반적으로는 다음과 같은 경우에 자동 크기 조정을 사용해야 합니다.

* 적절한 용량을 선언하여 Resource Manager 템플릿을 배포하는 과정에서 사용 사례가 지원되지 않는 경우
  * 수동 크기 조정을 수행함과 동시에 [Azure Resource Group 배포 프로젝트를 사용하여 Azure DevOps Services에서 연속 통합 및 배포 파이프라인]( https://docs.microsoft.com/azure/vs-azure-tools-resource-groups-ci-in-vsts)을 구성할 수 있습니다. 연속 통합과 배포 파이프라인은 일반적으로 Virtual Machine 성능 메트릭([Azure Monitor REST API](https://docs.microsoft.com/azure/azure-monitor/platform/rest-api-walkthrough)에서 쿼리함)을 활용하는 논리 앱을 통해 트리거됩니다. 이렇게 하면 원하는 모든 메트릭을 기준으로 크기를 효율적으로 자동 조정할 수 있으며, Azure Resource Manager에서 값을 추가할 수 있도록 배포가 최적화됩니다.
* Virtual Machine Scale Set 노드를 한 번에 하나씩만 수평으로 크기를 조정하면 되는 경우
  * 3개 이상의 노드 크기를 한 번에 확장하려는 경우에는 [Virtual Machine Scale Set를 추가하여 Service Fabric 클러스터를 확장](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)해야 합니다. 하지만 Virtual Machine Scale Sets는 한 번에 한 노드씩 수평으로 확장하고 규모를 감축하는 것이 가장 안전합니다.
* Service Fabric 클러스터의 안정성이 Silver 이상이고 자동 크기 조정 규칙을 구성하는 Scale Set의 내구성이 Silver 이상인 경우
  * 자동 크기 조정 규칙 용량[최솟값]은 5개 이상의 가상 머신 인스턴스이어야 하며 주 노드 형식의 안정성 계층 최솟값 이상이어야 합니다.

> [!NOTE]
> Azure Service Fabric 상태 저장 서비스 패브릭:/System/InfastructureService/<NODE_TYPE_NAME>은(는) 내구성이 Silver 이상인 모든 노드 형식에서 실행됩니다. 클러스터 노드 형식에서 Azure를 실행할 수 있는 시스템 서비스는 이 노드 형식뿐입니다. 

## <a name="vertical-scaling-considerations"></a>수직 크기 조정 관련 고려 사항

Azure Service Fabric에서 노드 형식의 [크기를 수직으로 조정](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)하려면 여러 단계를 수행하고 다양한 사항을 고려해야 합니다. 예를 들면 다음과 같습니다.
* 크기 조정 전에 클러스터가 정상 상태여야 합니다. 그렇지 않은 경우에는 크기를 조정하면 클러스터가 더 불안정해집니다.
* 상태 저장 서비스를 호스트하는 모든 Service Fabric 클러스터 NodeType의 경우에는 **내구성 수준이 Silver 이상**이어야 합니다.

> [!NOTE]
> 상태 저장 Service Fabric System Services를 호스트하는 주 NodeType은 내구성 수준이 Silver 이상이어야 합니다. Silver 내구성을 사용하도록 설정하고 나면 노드 업그레이드/추가/제거 등의 클러스터 작업 속도가 느려집니다. 시스템이 작업 속도 향상이 아닌 데이터 안전 유지를 위해 최적화되기 때문입니다.

Virtual Machine Scale Set 수직 크기 조정은 안전하지 않은 작업입니다. 그러므로 원하는 SKU가 포함된 새 Scale Set를 추가하여 클러스터 크기를 수평으로 조정하고 원하는 SKU로 서비스를 마이그레이션하면 수직 크기 조정 작업을 안전하게 완료할 수 있습니다. Virtual Machine Scale Set 리소스 SKU 변경은 안전하지 않은 작업입니다. 이 작업에서는 호스트가 이미지로 다시 설치되어 로컬에 영구 저장된 상태가 모두 제거되기 때문입니다.

클러스터는 Service Fabric [노드 속성 및 배치 제약 조건](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-cluster-description#node-properties-and-placement-constraints)을 사용하여 애플리케이션 서비스를 호스트할 위치를 결정합니다. 주 노드 형식의 크기를 수직으로 조정할 때는 Virtual Machine Scale Set Service Fabric 확장에 있는 `"nodeTypeRef"`에 대해 동일 속성 값을 선언합니다. 다음의 Resource Manager 템플릿 코드 조각에는 확장 대상인 새로 프로비저닝한 Scale Sets에 대해 같은 값을 사용하여 선언해야 하는 속성이 나와 있습니다. 이 코드 조각은 클러스터의 임시 상태 저장 명령으로만 지원됩니다. 다음을 참고하세요.

```json
"settings": {
   "nodeTypeRef": ["[parameters('primaryNodetypeName')]"]
}
```

> [!NOTE]
> 같은 `nodeTypeRef` 속성 값을 사용하는 여러 Scale Sets가 포함된 클러스터를 정상적인 수직 크기 조정 작업을 완료하는 데 필요한 시간보다 오랫동안 실행 상태로 유지하면 안 됩니다.
> 프로덕션 환경 변경을 시도하기 전에 항상 테스트 환경에서 작업 유효성을 검사하세요. 기본적으로 Service Fabric Cluster System Services에는 주 nodetype만 대상으로 지정하는 배치 제약 조건이 있습니다.

노드 속성 및 배치 제약 조건을 선언한 후 다음 단계를 한 번에 한 VM 인스턴스씩 수행합니다. 이렇게 하면 다른 위치에서 새 복제본을 만들 때 제거하는 VM 인스턴스에서 시스템 서비스와 상태 저장 서비스가 정상 종료됩니다.
1. PowerShell에서 'RemoveNode' 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 노드 클러스터가 6개이면 "MyNodeType_5" 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드별로 몇 시간씩 기다려야 할 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

## <a name="horizontal-scaling"></a>수평적 크기 조정

Service Fabric에서는 [수동](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down) 또는 [프로그래밍 방식](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-programmatic-scaling)으로 Service Fabric 수평 크기 조정을 수행할 수 있습니다.

> [!NOTE]
> 내구성이 Silver 또는 Gold인 노드 형식의 크기를 조정하는 경우에는 크기 조정 속도가 느려집니다.

### <a name="scaling-out"></a>확장

특정 Virtual Machine Scale Set의 인스턴스 수를 늘리는 방식으로 Service Fabric 클러스터를 확장합니다. 원하는 Scale Set의 ID와 AcureClient를 사용해 용량을 늘려서 프로그래밍 방식으로 클러스터를 확장할 수 있습니다.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

수동으로 클러스터를 확장하려는 경우에는 원하는 [Virtual Machine Scale Set](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.
```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

### <a name="scaling-in"></a>규모 감축

규모를 감축하려면 확장할 때보다 더 많은 사항을 고려해야 합니다. 예를 들면 다음과 같습니다.
* Service Fabric System Services는 클러스터의 주 노드 형식에서 실행됩니다. 인스턴스 수가 안정성 계층에서 보증하는 수보다 적어지도록 해당 노드 형식의 인스턴스를 종료하거나 인스턴스 수를 축소하지 마세요. 
* 상태 저장 서비스의 경우에는 가용성을 유지하고 서비스 상태를 보존하려면 특정 수의 노드가 항상 작동 상태여야 합니다. 최소한 파티션/서비스의 대상 복제본 세트 수와 같은 수의 노드가 필요합니다.

수동으로 규모를 감축하려면 다음 단계를 수행합니다.

1. PowerShell에서 'RemoveNode' 의도를 포함하여 `Disable-ServiceFabricNode`를 실행해 제거할 노드를 사용하지 않도록 설정합니다. 번호가 가장 큰 노드 형식을 제거합니다. 예를 들어 노드 클러스터가 6개이면 "MyNodeType_5" 가상 머신 인스턴스를 제거합니다.
2. `Get-ServiceFabricNode`를 실행하여 노드가 사용하지 않도록 전환되었는지 확인합니다. 그렇지 않은 경우 노드가 사용되지 않도록 설정될 때까지 기다립니다. 각 노드별로 몇 시간씩 기다려야 할 수 있습니다. 노드가 사용 안 함 상태로 전환될 때까지는 다음 단계를 진행하지 마세요.
3. 해당 노드 형식의 VM 수를 1개 줄입니다. 그러면 번호가 가장 큰 VM 인스턴스가 제거됩니다.
4. 필요에 따라 1~3단계를 반복하되, 주 노드 형식의 인스턴스 수를 안정성 계층이 경고하는 크기보다 작게 줄이지 않아야 합니다. 권장 인스턴스 목록은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

수동으로 클러스터 규모를 감축하려는 경우에는 원하는 [Virtual Machine Scale Set](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) 리소스의 SKU 속성에서 용량을 업데이트합니다.

```json
"sku": {
    "name": "[parameters('vmNodeType0Size')]",
    "capacity": "[parameters('nt0InstanceCount')]",
    "tier": "Standard"
}
```

1. 1~3단계를 반복하여 원하는 용량을 프로비저닝합니다. 단, 주 노드 형식의 인스턴스 수가 안정성 계층에서 보증되는 수보다 적어지도록 클러스터를 축소해서는 안 됩니다. 안정성 계층 및 각 계층에 필요한 인스턴스 수에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)을 참조하세요.

프로그래밍 방식으로 규모를 감축하려면 종료할 노드를 준비해야 합니다. 이 과정에서는 제거할 노드(번호가 가장 큰 인스턴스 노드)를 찾아서 비활성화합니다. 예를 들면 다음과 같습니다.

```c#
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

제거할 노드를 확인한 후에는 위의 코드에서 사용한 것과 같은 `FabricClient` 인스턴스(여기서는 `client`) 및 노드 인스턴스 이름(여기서는 `instanceIdString`)를 사용하여 해당 노드를 비활성화하고 제거합니다.

```c#
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply();
```

> [!NOTE]
> 크기를 조정할 때 Service Fabric Explorer에서 비정상 상태로 표시 제거 된 노드/v M 인스턴스가 중단 클러스터에 표시 됩니다. 이 동작의 설명에 대 한 참조 [Service Fabric Explorer에서 볼 수 있는 동작](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#behaviors-you-may-observe-in-service-fabric-explorer)합니다.
> 
> 다음을 수행할 수 있습니다.
> * 호출 [Remove-servicefabricnodestate cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) 적절 한 노드 이름과 함께 합니다.
> * 배포할 [service fabric 자동 크기 조정 도우미 응용 프로그램](https://github.com/Azure/service-fabric-autoscale-helper/) 노드 아래로 확장을 보장 하는 클러스터에서 Service Fabric Explorer에서 제거 됩니다.

## <a name="reliability-levels"></a>안정성 수준

Service Fabric 클러스터 리소스의 속성인 [안정성 수준](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)은 각 nodeType별로 다르게 구성할 수 없습니다. 클러스터에 대한 시스템 서비스의 복제 인자를 제어하는 이 속성은 클러스터 리소스 수준의 설정입니다. 안정성 수준에 따라 주 노드 형식이 포함해야 하는 최소 노드 수가 결정됩니다. 안정성 계층은 다음 값을 사용할 수 있습니다.
* Platinum - 대상 복제본 세트 7개와 시드 노드 9개를 사용하여 시스템 서비스를 실행합니다.
* Gold - 대상 복제본 세트 7개와 시드 노드 7개를 사용하여 시스템 서비스를 실행합니다.
* Silver - 대상 복제본 세트 5개와 시드 노드 5개를 사용하여 시스템 서비스를 실행합니다.
* Bronze - 대상 복제본 세트 3개와 시드 노드 3개를 사용하여 시스템 서비스를 실행합니다.

최소 권장 안정성 수준은 Silver입니다.

안정성 수준은 다음과 같이 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 속성 섹션에서 설정됩니다.

```json
"properties":{
    "reliabilityLevel": "Silver"
}
```

## <a name="durability-levels"></a>내구성 수준

> [!WARNING]
> Bronze 내구성으로 실행되는 노드 형식은 _권한 없음_이 됩니다. 즉, 상태 비저장 워크로드에 영향을 주는 인프라 작업은 중지되거나 지연되지 않으며, 이는 워크로드에 영향을 줄 수 있습니다. Bronze 내구성은 상태 비저장 워크로드를 실행하는 노드 형식에만 사용해야 합니다. 프로덕션 워크로드의 경우 상태 일관성을 유지할 수 있도록 내구성이 Silver 이상인 노드 형식을 실행하세요. [용량 계획 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)의 지침에 따라 올바른 안정성을 선택합니다.

내구성 수준은 두 리소스에서 설정해야 합니다. 먼저 다음과 같이 [Virtual Machine Scale Set 리소스](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)의 확장 프로필에서 내구성 수준을 설정합니다.

```json
"extensionProfile": {
    "extensions":          {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
        "properties": {
            "settings": {
                "durabilityLevel": "Bronze"
            }
        }
    }
}
```

그리고 [Microsoft.ServiceFabric/clusters resource](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2018-02-01/clusters)의 `nodeTypes` 아래에서도 설정합니다. 

```json
"nodeTypes": [
    {
        "name": "[variables('vmNodeType0Name')]",
        "durabilityLevel": "Bronze"
    }
]
```

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
