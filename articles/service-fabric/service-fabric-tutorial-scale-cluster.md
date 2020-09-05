---
title: Azure에서 Service Fabric 클러스터 크기 조정
description: 이 자습서에서는 Azure에서 Service Fabric 클러스터의 크기를 확장 및 축소하는 방법과 남은 리소스를 정리하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: d9699103f5e13301cce408d2e54f0e15780e0a35
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716897"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>자습서: Azure에서 Service Fabric 클러스터 크기 조정

이 자습서는 이 시리즈의 세 번째 파트로, 기존 클러스터를 확장 및 축소하는 방법을 보여 줍니다. 이 내용을 완료하면 클러스터를 크기 조정하는 방법과 남은 리소스를 정리하는 방법을 알게 됩니다.  Azure에서 실행되는 클러스터의 크기를 조정하는 방법에 대한 자세한 내용은 [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 노드 추가 및 제거(규모 확장 및 규모 감축)
> * 노드 유형 추가 및 제거(규모 확장 및 규모 감축)
> * 노드 리소스 증가(강화)

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 만들기
> * [클러스터 모니터링](service-fabric-tutorial-monitor-cluster.md)
> * 클러스터 규모 확장 또는 규모 감축
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [클러스터 삭제](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 만들기

## <a name="important-considerations-and-guidelines"></a>중요 고려 사항 및 지침

애플리케이션 워크로드는 시간이 지남에 따라 변합니다. 기존 서비스에 더 많거나 더 적은 리소스가 필요한가요?  그러면 노드 유형에서 [노드를 추가하거나 제거](#add-nodes-to-or-remove-nodes-from-a-node-type)하여 클러스터 리소스를 늘리거나 줄입니다.

클러스터에 노드를 100개 이상 추가해야 하나요?  단일 Service Fabric 노드 유형/확장 집합에는 100개 이상의 노드/VM이 포함될 수 없습니다.  클러스터를 100개가 넘는 노드로 확장하려면 [추가 노드 유형을 추가](#add-nodes-to-or-remove-nodes-from-a-node-type)합니다.

애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요?  일반적인 애플리케이션은 클라이언트에서 입력을 수신하는 프런트 엔드 게이트웨이 서비스 및 프런트 엔드 서비스와 통신하는 하나 이상의 백 엔드 서비스를 포함합니다. 이 경우 클러스터에 [둘 이상의 노드 유형을 추가](#add-nodes-to-or-remove-nodes-from-a-node-type)하는 것이 좋습니다.  

서비스에 더 많은 RAM 또는 더 높은 CPU 사이클과 같은 다양한 인프라 요구 사항이 있나요? 예를 들어 애플리케이션에 프런트 엔드 서비스와 백 엔드 서비스가 포함되어 있습니다. 프런트 엔드 서비스는 인터넷에 열린 포트가 있는 작은 VM(D2와 같은 VM 크기)에서 실행할 수 있습니다. 하지만 백 엔드 서비스는 계산 집약적이며 인터넷에 연결되지 않은 비교적 큰 VM(D4, D6, D15과 같은 VM 크기로)에서 실행됩니다. 이 경우 클러스터에 [둘 이상의 노드 유형을 추가](#add-nodes-to-or-remove-nodes-from-a-node-type)하는 것이 좋습니다. 그러면 각 노드 유형이 인터넷 연결 또는 VM 크기와 같은 고유 속성을 가질 수 있습니다. VM의 수를 독립적으로 확장할 수도 있습니다.

Azure 클러스터 크기를 조정할 때는 다음 지침에 유의하세요.

* 단일 Service Fabric 노드 유형/확장 집합에는 100개 이상의 노드/VM이 포함될 수 없습니다.  클러스터를 100개가 넘는 노드로 확장하려면 추가 노드 유형을 추가합니다.
* 프로덕션 워크로드를 실행하는 주 노드 유형에는 Gold 또는 Silver의 [내구성 수준][durability]이 있어야 하며 항상 5개 이상의 노드가 있어야 합니다.
* 상태 저장 프로덕션 워크로드를 실행하는 주 이외의 노드 유형에는 항상 5개 이상의 노드가 있어야 합니다.
* 상태 비저장 프로덕션 워크로드를 실행하는 주 이외의 노드 유형에는 항상 둘 이상의 노드가 있어야 합니다.
* [내구성 수준][durability]이 골드 또는 실버인 모든 노드 형식에는 항상 5개 이상의 노드가 있어야 합니다.
* 주 노드 유형의 크기를 축소(노드 제거)하는 경우 인스턴스 수를 [안정성 수준][reliability]에 필요한 것보다 적게 줄이면 안 됩니다.

자세한 내용은 [클러스터 용량 지침](service-fabric-cluster-capacity.md)을 참조하세요.

## <a name="export-the-template-for-the-resource-group"></a>리소스 그룹에 대한 템플릿 내보내기

보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md)가 만들어지고 리소스 그룹이 설정되면 리소스 그룹에 대한 Resource Manager 템플릿을 내보냅니다. 템플릿을 내보내면 템플릿에 전체 인프라가 모두 포함되어 있으므로 클러스터와 해당 리소스의 향후 배포를 자동화할 수 있습니다.  템플릿 내보내기에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Resource Manager 리소스 그룹 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 클러스터가 포함된 리소스 그룹(이 자습서를 따르는 경우 **sfclustertutorialgroup**)으로 이동합니다. 

2. 왼쪽 창에서 **배포**를 선택하거나 **배포** 아래에 있는 링크를 선택합니다. 

3. 목록에서 가장 최근에 성공한 배포를 선택합니다.

4. 왼쪽 창에서 **템플릿**을 선택한 다음, **다운로드**를 선택하여 템플릿을 ZIP 파일로 내보냅니다.  템플릿과 매개 변수를 로컬 컴퓨터에 저장합니다.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>노드 유형에서 노드 추가 또는 제거

규모 확장 또는 축소하거나 수평 확장하면 클러스터의 노드 수가 변경됩니다. 규모를 확장하거나 축소하는 경우 더 많은 가상 머신 인스턴스를 확장 집합에 추가합니다. 이러한 인스턴스는 Service Fabric 에서 사용하는 노드가 됩니다. Service Fabric은 확장 집합에 더 많은 인스턴스가 추가되고(규모 확장) 자동으로 반응할 때를 압니다. 클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.

### <a name="update-the-template"></a>템플릿 업데이트

가장 최근 배포에 대한 리소스 그룹에서 [템플릿 및 매개 변수 파일을 내보냅니다](#export-the-template-for-the-resource-group).  *parameters.json* 파일을 엽니다.  이 자습서에서 [샘플 템플릿][template]을 사용하여 클러스터를 배포한 경우 클러스터에는 3개의 노드 유형과 각 노드 유형의 노드 수가 설정되는 3개의 매개 변수(*nt0InstanceCount*, *nt1InstanceCount* 및 *nt2InstanceCount*)가 있습니다.  예를 들어 *nt1InstanceCount* 매개 변수는 두 번째 노드 유형에 대한 인스턴스 수 및 연결되는 가상 머신 확장 집합의 VM 수를 설정합니다.

따라서 *nt1InstanceCount*의 값을 업데이트하여 두 번째 노드 유형의 노드 수를 변경합니다.  한 노드 유형의 노드 수는 100개를 초과할 수 없습니다.  상태 저장 프로덕션 워크로드를 실행하는 주 이외의 노드 유형에는 항상 5개 이상의 노드가 있어야 합니다. 상태 비저장 프로덕션 워크로드를 실행하는 주 이외의 노드 유형에는 항상 둘 이상의 노드가 있어야 합니다.

Bronze [내구성 수준][durability]의 노드 유형에서 노드를 확장하거나 제거하려는 경우 [해당 노드의 상태를 수동으로 제거](service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)해야 합니다.  Silver 및 Gold 내구성 계층의 경우 이러한 단계는 플랫폼에서 자동으로 수행됩니다.

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
모든 변경 내용은 *template.json* 및 *parameters.json* 파일에 저장됩니다.  업데이트된 템플릿을 배포하려면 다음 명령을 실행합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
또는 다음 Azure CLI 명령을 실행합니다.
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>클러스터에 노드 유형 추가

Azure에서 실행되는 Service Fabric 클러스터에 정의된 모든 노드 유형은 [별도의 가상 머신 확장 집합](service-fabric-cluster-nodetypes.md)으로 설정됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. 각 노드 형식을 독립적으로 확장 또는 축소하고, 다른 포트의 집합을 열며 다른 용량 메트릭을 사용할 수 있습니다. 각 클러스터 노드에서 실행되는 OS SKU를 독립적으로 변경할 수도 있지만, 샘플 클러스터에서 실행되는 Windows와 Linux를 함께 사용할 수는 없습니다. 단일 노드 유형/확장 집합의 노드 수는 100개를 초과할 수 없습니다.  클러스터의 노드 수가 100개를 초과하도록 조정하려면 노드 유형/확장 집합을 추가하여 수평적으로 확장하면 됩니다. 클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.

### <a name="update-the-template"></a>템플릿 업데이트

가장 최근 배포에 대한 리소스 그룹에서 [템플릿 및 매개 변수 파일을 내보냅니다](#export-the-template-for-the-resource-group).  *parameters.json* 파일을 엽니다.  이 자습서에서 [샘플 템플릿][template]을 사용하여 클러스터를 배포한 경우 클러스터에는 세 개의 노드 유형이 있습니다.  이 섹션에서는 Resource Manager 템플릿을 업데이트하고 배포하여 네 번째 노드 유형을 추가합니다. 

또한 새 노드 유형 외에도, 연결된 가상 머신 확장 집합(가상 네트워크의 별도 서브넷에서 실행됨)과 네트워크 보안 그룹을 추가합니다.  새 확장 집합에 대해 새 또는 기존 공용 IP 주소와 Azure 부하 분산 장치 리소스를 추가하도록 선택할 수 있습니다.  새 노드 유형의 [내구성 수준][durability]은 Silver이며 크기는 "Standard_D2_V2"입니다.

*template.json* 파일에서 다음 새 매개 변수를 추가합니다.
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

*template.json* 파일에서 다음 새 변수를 추가합니다.
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

*template.json* 파일에서 새 서브넷을 가상 네트워크 리소스에 추가합니다.
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

*template.json* 파일에서 새 공용 IP 주소와 부하 분산 장치 리소스를 추가합니다.
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

*template.json* 파일에서 새 네트워크 보안 그룹과 가상 머신 확장 집합 리소스를 추가합니다.  가상 머신 확장 집합의 Service Fabric 확장 속성 내의 NodeTypeRef 속성은 지정된 노드 유형을 확장 집합에 매핑합니다.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

*template.json* 파일에서 클러스터 리소스를 업데이트하고 새 노드 유형을 추가합니다.
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

*parameters.json* 파일에서 다음 새 매개 변수와 값을 추가합니다.
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
모든 변경 내용은 *template.json* 및 *parameters.json* 파일에 저장됩니다.  업데이트된 템플릿을 배포하려면 다음 명령을 실행합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
또는 다음 Azure CLI 명령을 실행합니다.
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>클러스터에서 노드 유형 제거
Service Fabric 클러스터를 만든 후에 노드 유형(가상 머신 확장 집합) 및 모든 노드를 제거하여 클러스터를 수평 확장할 수 있습니다. 클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다. 클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

> [!WARNING]
> Remove-AzServiceFabricNodeType을 사용하여 프로덕션 클러스터에서 노드 유형을 제거하는 것은 자주 사용하지 않는 것이 좋습니다. 노드 유형 뒤의 가상 머신 확장 집합 리소스가 삭제되므로 위험한 명령입니다. 

노드 유형을 제거하려면 [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet을 실행합니다.  노드 유형은 Silver 또는 Gold [내구성 수준][durability]이어야 합니다. 이 cmdlet은 노드 유형과 연결된 확장 집합을 삭제하고 완료하는 데 약간의 시간이 걸립니다.  그런 다음, 제거할 각 노드에서 [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) cmdlet을 실행하여 노드 상태를 삭제하고 클러스터에서 노드를 제거합니다. 해당 노드에 서비스가 있으면 먼저 해당 서비스가 다른 노드로 이동됩니다. 클러스터 관리자가 복제본/서비스에 대한 노드를 찾을 수 없으면 작업이 지연/차단됩니다.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>노드 리소스 증가 
Service Fabric 클러스터를 생성한 후 원래 노드 유형을 새 노드 유형(업데이트된 VM SKU 또는 OS 이미지)으로 바꾸어 클러스터 노드 유형을 수직으로 확장(노드의 리소스 변경)하거나 노드 유형 VM의 운영 체제를 업그레이드할 수 있습니다. 자세한 내용은 [Azure Service Fabric 노드 형식 스케일 업](service-fabric-scale-up-primary-node-type.md)을 참조하세요.

> [!IMPORTANT]
> 위험한 작업이며 지원되지 않는 VM SKU 또는 OS 이미지의 내부 변경을 시도하지 마세요.

이것이 불가능한 경우 새 클러스터를 만들고 구 클러스터에서 [애플리케이션 상태를 복원](service-fabric-reliable-services-backup-restore.md)할 수 있습니다(해당하는 경우). 시스템 서비스 상태를 복원할 필요는 없습니다. 애플리케이션을 새 클러스터에 배포하면 다시 만들어집니다. 클러스터에서 상태 비저장 애플리케이션을 실행한 경우에만 애플리케이션을 새 클러스터에 배포하기만 하면 되며 복원은 필요하지 않습니다.

### <a name="update-the-template"></a>템플릿 업데이트

가장 최근 배포에 대한 리소스 그룹에서 [템플릿 및 매개 변수 파일을 내보냅니다](#export-the-template-for-the-resource-group).  *parameters.json* 파일을 엽니다.  이 자습서에서 [샘플 템플릿][template]을 사용하여 클러스터를 배포한 경우 클러스터에는 세 개의 노드 유형이 있습니다.  

두 번째 노드 유형의 VM 크기는 *vmNodeType1Size* 매개 변수에서 설정됩니다.  *vmNodeType1Size* 매개 변수 값을 Standard_D2_V2에서 각 VM 인스턴스의 리소스가 두 배 크기인 [Standard_D3_V2](../virtual-machines/dv2-dsv2-series.md)로 변경합니다.

이러한 세 노드 유형 모두에 대한 VM SKU는 *vmImageSku* 매개 변수에서 설정됩니다.  다시 말하지만, 노드 유형의 VM SKU를 변경하는 경우 주의해야 하며 주 노드 유형에는 추천되지 않습니다.

### <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
모든 변경 내용은 *template.json* 및 *parameters.json* 파일에 저장됩니다.  업데이트된 템플릿을 배포하려면 다음 명령을 실행합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
또는 다음 Azure CLI 명령을 실행합니다.
```azurecli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 노드 추가 및 제거(규모 확장 및 규모 감축)
> * 노드 유형 추가 및 제거(규모 확장 및 규모 감축)
> * 노드 리소스 증가(강화)

이제 다음 자습서로 넘어가서 클러스터 런타임을 업그레이드하는 방법을 알아보겠습니다.
> [!div class="nextstepaction"]
> [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
