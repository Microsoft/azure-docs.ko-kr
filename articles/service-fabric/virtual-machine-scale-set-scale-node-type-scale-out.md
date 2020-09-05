---
title: Azure Service Fabric 클러스터에 노드 형식 추가
description: Virtual Machine Scale Set을 추가하여 Service Fabric 클러스터를 확장하는 방법을 알아봅니다.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719328"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 추가하여 Service Fabric 클러스터 확장
이 문서에서는 기존 클러스터에 새 노드 유형을 추가하여 Azure Service Fabric 클러스터를 확장하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터를 만든 후에 새 노드 유형(가상 머신 확장 집합)을 기존 클러스터에 추가하여 클러스터를 수평 확장할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>기존 클러스터에 추가 확장 집합 추가
기존 클러스터에 새 노드 형식 (가상 머신 확장 집합에 의해 지원 됨)을 추가 하는 것은 동일한 해당 노드의 nodetyperef를 사용 하지 않는 경우를 제외 하 고는 [주 노드 유형을 업그레이드](service-fabric-scale-up-primary-node-type.md)하는 것과 비슷합니다. 실제로 사용 되는 가상 머신 확장 집합을 사용 하지 않도록 설정 하는 것은 아니며, 주 노드 유형을 업데이트 하지 않으면 클러스터 가용성이 손실 되지 않습니다. 

NodeTypeRef 속성은 가상 머신 확장 집합 Service Fabric 확장 속성 내에서 선언됩니다.
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

또한 이 새 노드 형식에 Service Fabric 클러스터 리소스를 추가해야 합니다.

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>다음 단계
* [주 노드 유형을 확장](service-fabric-scale-up-primary-node-type.md)하는 방법을 알아봅니다.
* [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).

