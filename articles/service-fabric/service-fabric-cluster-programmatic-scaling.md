---
title: 프로그래밍 방식으로 Azure Service Fabric 크기 조정 | Microsoft Docs
description: 사용자 지정 트리거에 따라 Azure Service Fabric 클러스터를 프로그래밍 방식으로 규모 확장 또는 규모 감축
services: service-fabric
documentationcenter: .net
author: mjrousos
manager: jonjung
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: 552c9820cca4380c00e1bf435fdb3d068c0690fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111305"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>프로그래밍 방식으로 Service Fabric 클러스터의 크기 조정 

Azure에서 실행되는 Service Fabric 클러스터는 가상 머신 확장 집합 위에 구축됩니다.  [클러스터 크기 조정](./service-fabric-cluster-scale-up-down.md)에서는 Service Fabric 클러스터 크기를 수동으로 또는 자동 크기 조정 규칙을 사용하여 조정하는 방법을 설명합니다. 이 문서에서는 좀 더 고급 시나리오에 해당하는 유연한 Azure Compute SDK를 사용하여 자격 증명을 관리하고 클러스터의 크기를 조정하는 방법을 설명합니다. 개요를 보려면 [Azure 크기 조정 작업을 조정하는 프로그래밍 방법](service-fabric-cluster-scaling.md#programmatic-scaling)을 읽어보세요. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>자격 증명 관리
크기 조정을 처리하는 서비스를 작성할 때 마주치는 한 가지 어려움은 대화형 로그인 없이 서비스가 가상 머신 확장 집합에 액세스할 수 있어야 한다는 점입니다. 크기 조정 서비스가 자체 Service Fabric 애플리케이션을 수정하는 경우 Service Fabric 클러스터에 손쉽게 액세스할 수 있지만 자격 증명이 확장 세트에 액세스해야 합니다. 로그인하려면 [Azure CLI](https://github.com/azure/azure-cli)로 만든 [서비스 사용자](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)를 사용하면 됩니다.

서비스 주체는 다음 단계에 따라 만들 수 있습니다.

1. 가상 머신 확장 집합에 액세스할 수 있는 사용자로 Azure CLI(`az login`)에 로그인합니다.
2. `az ad sp create-for-rbac`을 사용하여 서비스 주체를 만듭니다.
    1. 나중에 사용할 수 있도록 appId(다른 곳에서는 'client ID'라고도 함), 이름, 암호, 테넌트를 기록해 둡니다.
    2. 구독 ID도 필요하며 `az account list`를 사용하여 확인할 수 있습니다.

다음과 같이 자격 증명을 사용하여 Fluent 계산 라이브러리에 로그인할 수 있습니다(`IAzure`와 같은 핵심 Fluent Azure 형식은 [Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) 패키지에 있음).

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

로그인되면 `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`를 통해 확장 집합 인스턴스 수를 쿼리할 수 있습니다.

## <a name="scaling-out"></a>확장
fluent Azure compute SDK를 사용하면 호출 몇 번으로 가상 머신 확장 집합에 인스턴스를 추가할 수 있습니다.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

또는 PowerShell cmdlet을 통해 가상 머신 확장 집합 크기를 관리할 수도 있습니다. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)는 가상 컴퓨터 확장 집합 개체를 검색할 수 있습니다. 현재 용량은 `.sku.capacity` 속성을 통해 사용할 수 있습니다. 용량을 원하는 값으로 변경한 후 Azure에서 [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) 명령을 통해 가상 머신 확장 집합을 업데이트할 수 있습니다.

노드를 수동으로 추가하는 경우 확장 집합 인스턴스만 추가하면 새로운 Service Fabric 노드를 시작할 수 있습니다. 확장 집합 템플릿에는 새 인스턴스를 자동으로 Service Fabric 클러스터에 조인하는 확장 기능이 포함되어 있기 때문입니다. 

## <a name="scaling-in"></a>규모 감축

규모 감축은 규모 확장과 비슷합니다. 실제 가상 머신 확장 집합 변경은 실질적으로 동일합니다. 하지만 앞서 살펴본 것처럼 Service Fabric은 제거된 노드 중에서 내구성 수준이 Gold 또는 Silver인 노드만 자동으로 정리합니다. 따라서 내구성 수준이 Bronze인 규모 감축에서는 제거할 노드를 종료한 후 상태를 제거하도록 Service Fabric 클러스터와 상호 작용이 필요합니다.

종료할 노드를 준비하는 과정에는 제거할 노드(가장 최근에 추가된 가상 머신 확장 집합 인스턴스)를 찾아서 비활성화하는 작업이 포함됩니다. 가상 머신 확장 집합 인스턴스는 추가된 순서대로 번호가 매겨지므로 노드 이름(기본 가상 머신 확장 집합 인스턴스 이름에 해당)의 숫자 접미사를 비교하여 최신 노드를 찾을 수 있습니다. 

```csharp
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

제거할 노드를 찾았으면 이전과 동일한 `FabricClient` 인스턴스 및 `IAzure` 인스턴스를 사용하여 노드를 비활성화하고 제거할 수 있습니다.

```csharp
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

스크립팅 방식을 선호하는 경우 여기에서 확장과 마찬가지로 가상 머신 확장 집합 용량을 수정하기 위한 PowerShell cmdlet을 사용할 수도 있습니다. 가상 머신 인스턴스가 제거되면 Service Fabric 노드 상태를 제거할 수 있습니다.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>다음 단계

사용자 고유의 자동 크기 조정 논리를 구현하려면 먼저 다음과 같은 개념과 유용한 API부터 숙지해야 합니다.

- [수동으로 또는 자동 크기 조정 규칙을 사용하여 크기 조정](./service-fabric-cluster-scale-up-down.md)
- [.NET용 Fluent Azure Management 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/Fluent)(Service Fabric 클러스터의 기본 가상 가상 머신 확장 집합과 상호 작용하는 데 유용함)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient)(Service Fabric 클러스터 및 그 노드와 상호 작용하는 데 유용함)
