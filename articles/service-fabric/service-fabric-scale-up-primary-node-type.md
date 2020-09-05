---
title: Azure Service Fabric 주 노드 유형 강화
description: 노드 유형을 추가 하 여 Service Fabric 클러스터의 크기를 조정 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228718"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>노드 유형을 추가 하 여 Service Fabric 클러스터 주 노드 유형 강화
이 문서에서는 클러스터에 노드 유형을 추가 하 여 Service Fabric 클러스터 주 노드 유형을 확장 하는 방법을 설명 합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다.

다음 자습서의 샘플 템플릿은 [Service Fabric 주 노드 형식 크기 조정 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample) 에서 찾을 수 있습니다.

> [!WARNING]
> 클러스터 상태가 비정상 인 경우 주 노드 유형 확장 프로시저를 시도 하지 마십시오. 이렇게 하면 클러스터를 추가로 불안정 하 게 됩니다.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>주 노드 형식의 크기 및 운영 체제를 업그레이드 하는 프로세스
다음은 VM 크기 및 주 노드 유형 Vm의 운영 체제를 업데이트 하는 프로세스입니다.  업그레이드 후 주 노드 유형 Vm은 크기 표준 D4_V2 하 고 컨테이너를 포함 하는 Windows Server 2019 Datacenter를 실행 합니다.

> [!WARNING]
> 프로덕션 클러스터에서 이 절차를 시도하기 전에 먼저 샘플 템플릿을 알아보고 테스트 클러스터에 대한 프로세스를 확인하는 것이 좋습니다. 클러스터를 짧은 시간 동안 사용할 수 없는 경우도 있습니다. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>초기 Service Fabric 클러스터 배포 
샘플과 함께 수행 하려면 단일 주 노드 유형 및 단일 확장 집합 [Service Fabric 초기](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json)클러스터로 초기 클러스터를 배포 합니다. 기존 Service Fabric 클러스터가 이미 배포 되어 있는 경우이 단계를 건너뛸 수 있습니다. 

1. Azure 계정에 로그인 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. 새 리소스 그룹 만들기 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. 템플릿 파일의 매개 변수 값을 입력 합니다. 
4. 2 단계에서 만든 리소스 그룹에 클러스터를 배포 합니다. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>클러스터에 새 주 노드 형식 추가
> [!Note]
> 다음 단계에서 만든 리소스는 크기 조정 작업이 완료 되 면 클러스터의 새 주 노드 유형이 됩니다. 초기 서브넷, 공용 IP, Load Balancer, 가상 컴퓨터 확장 집합 및 노드 형식에서 고유한 이름을 사용 해야 합니다. 

[Service Fabric-새 노드 형식 클러스터](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json)에서 완료 한 다음 단계를 모두 사용 하 여 템플릿을 찾을 수 있습니다. 다음 단계에는 새 리소스의 변경 내용을 강조 표시 하는 부분 리소스 조각이 포함 되어 있습니다.  

1. 기존 Virtual Network에 새 서브넷을 만듭니다.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. 고유한 domainNameLabel를 사용 하 여 새 공용 IP 리소스를 만듭니다. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. 위에서 만든 공용 IP에 따라 새 Load Balancer 리소스를 만듭니다. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. 확장할 새 VM SKU 및 OS SKU를 사용 하는 새 가상 머신 확장 집합을 만듭니다. 

노드 유형 참조 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

다음 코드 조각은 Service Fabric 클러스터에 대해 새 노드 유형을 만드는 데 사용 되는 새 가상 머신 확장 집합 리소스의 예입니다. 워크 로드에 필요한 추가 확장을 포함 해야 합니다. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
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
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
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
            "computernamePrefix": "[variables('vmNodeType1Name')]",
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
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
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
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. 클러스터에 새 노드 유형을 추가 하 여 위에서 만든 가상 머신 확장 집합을 참조 합니다. 이 노드 형식의 **isPrimary** 속성은 true로 설정 해야 합니다. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. 업데이트 된 ARM 템플릿을 배포 합니다. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

배포가 완료 되 면 이제 Service Fabric 클러스터에 두 개의 노드 유형이 있습니다. 

### <a name="remove-the-existing-node-type"></a>기존 노드 형식 제거 
리소스 배포가 완료 되 면 원래 주 노드 유형의 노드를 사용 하지 않도록 설정할 수 있습니다. 노드가 사용 하지 않도록 설정 되 면 시스템 서비스는 위 단계에서 배포 된 새 주 노드 유형으로 마이그레이션됩니다.

1. Service Fabric 클러스터 리소스의 주 노드 유형 속성을 false로 설정 합니다. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. 원래 노드 형식에 업데이트 된 isPrimary 속성을 사용 하 여 템플릿을 배포 합니다. 원본 노드 형식에 대 한 기본 플래그가 false로 설정 된 템플릿을 찾을 수 있습니다. [Service Fabric-주 노드 형식 false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. 노드 유형 0에서 노드를 사용 하지 않도록 설정 합니다. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* 청동 내구성의 경우 모든 노드가 비활성 상태가 될 때까지 기다립니다.
* 실버 및 골드 내구성의 경우 일부 노드가 사용 하지 않도록 설정 되 고 나머지는 비활성화 상태로 전환 됩니다. 상태를 사용 하지 않도록 설정 하는 노드의 세부 정보 탭을 확인 합니다. 모두 인프라 서비스 파티션에 대 한 쿼럼을 보장 하는 데 문제가 있으면 계속 하는 것이 안전 합니다.

> [!Note]
> 이 단계를 완료 하는 데 다소 시간이 걸릴 수 있습니다. 

4. 노드 유형 0에서 데이터를 중지 합니다. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. 원본 가상 머신 확장 집합에서 노드 할당 취소 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> 표준 SKU 공용 IP 및 표준 SKU 부하 분산 장치를 이미 사용 중인 경우에는 6 단계와 7 단계를 선택할 수 있습니다. 이 경우 동일한 부하 분산 장치에서 여러 가상 머신 확장 집합/노드 유형을 가질 수 있습니다. 

6. 이제 원래 IP 및 Load Balancer 리소스를 삭제할 수 있습니다. 이 단계에서는 DNS 이름도 업데이트 합니다. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. 새 IP를 참조 하도록 클러스터의 관리 끝점을 업데이트 합니다. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. 노드 유형 0에서 노드 상태를 제거 합니다.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. ARM 템플릿의 Service Fabric 리소스에서 원래 노드 형식 참조를 제거 합니다. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
실버 이상 내구성이 있는 클러스터의 경우에만 템플릿에서 클러스터 리소스를 업데이트 하 고 아래 지정 된 대로 클러스터 리소스 속성 아래에 applicationDeltaHealthPolicies를 추가 하 여 패브릭:/시스템 응용 프로그램 상태를 무시 하도록 상태 정책을 구성 합니다. 아래 정책은 기존 오류는 무시 하지만 새 상태 오류는 허용 하지 않습니다.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. ARM 템플릿에서 원래 노드 유형과 관련 된 다른 모든 리소스를 제거 합니다. 이러한 모든 원본 리소스가 제거 된 템플릿에 대 한 [Service Fabric-새 노드 형식 클러스터](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) 를 참조 하세요.

11. 수정 된 Azure Resource Manager 템플릿을 배포 합니다. * *이 단계는 시간이 오래 걸립니다 (일반적으로 최대 2 시간). 이 업그레이드는 설정을 InfrastructureService로 변경 합니다. 따라서 노드를 다시 시작 해야 합니다. 이 경우 forceRestart은 무시 됩니다. UpgradeReplicaSetCheckTimeout 매개 변수는 아직 안전 상태가 아닌 경우 파티션이 안전한 상태가 될 때까지 Service Fabric 대기 하는 최대 시간을 지정 합니다. 안전 검사가 노드의 모든 파티션에 대해 통과 하면 Service Fabric는 해당 노드에서의 업그레이드를 진행 합니다. UpgradeTimeout 매개 변수에 대 한 값을 6 시간으로 줄일 수 있지만 최대 보안을 12 시간으로 사용 해야 합니다.
그런 다음 포털의 Service Fabric 리소스가 준비 된 것으로 표시 되는지 확인 합니다. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

이제 클러스터의 기본 노드 형식이 업그레이드되었습니다. 배포된 애플리케이션이 제대로 작동하고 클러스터 상태가 정상인지 확인합니다.

## <a name="next-steps"></a>다음 단계
* [클러스터에 노드 형식을 추가](virtual-machine-scale-set-scale-node-type-scale-out.md)하는 방법을 알아봅니다.
* [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
* [Azure 클러스터를 스케일 인 또는 스케일 아웃](service-fabric-tutorial-scale-cluster.md)합니다.
* 유용한 Azure Compute SDK를 사용하여 [Azure 클러스터의 크기를 프로그래밍 방식으로 조정](service-fabric-cluster-programmatic-scaling.md)합니다.
* [독립 실행형 클러스터 스케일 인 또는 스케일 아웃](service-fabric-cluster-windows-server-add-remove-nodes.md).
