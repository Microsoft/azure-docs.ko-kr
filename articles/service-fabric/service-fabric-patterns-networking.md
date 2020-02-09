---
title: Azure Service Fabric에 대 한 네트워킹 패턴
description: Service Fabric에 대한 일반적인 네트워킹 패턴과 Azure 네트워킹 기능을 사용하여 클러스터를 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 065c311fffe409b20e02a3fddf1e9e7e6a82a2a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466278"
---
# <a name="service-fabric-networking-patterns"></a>Service Fabric 네트워킹 패턴
다른 Azure 네트워킹 기능으로 Azure Service Fabric 클러스터를 통합할 수 있습니다. 이 문서에서는 다음과 같은 기능을 사용하여 클러스터를 만드는 방법을 보여 줍니다.

- [기존 가상 네트워크 또는 서브넷](#existingvnet)
- [고정 공용 IP 주소](#staticpublicip)
- [내부 전용 부하 분산 장치](#internallb)
- [내부 및 외부 부하 분산 장치](#internalexternallb)

Service Fabric은 표준 가상 머신 확장 집합에서 실행됩니다. 가상 머신 확장 집합에서 사용할 수 있는 모든 기능을 Service Fabric 클러스터에서 사용할 수 있습니다. 가상 머신 확장 집합 및 Service Fabric에 대한 Azure Resource Manager 템플릿의 네트워킹 섹션은 동일합니다. 기존 가상 네트워크에 배포한 후 Azure ExpressRoute, Azure VPN Gateway, 네트워크 보안 그룹 및 가상 네트워크의 피어링 등의 다른 네트워킹 기능을 쉽게 통합할 수 있습니다.

### <a name="allowing-the-service-fabric-resource-provider-to-query-your-cluster"></a>Service Fabric 리소스 공급자가 클러스터를 쿼리할 수 있도록 허용

Service Fabric은 한 가지 측면에서 다른 네트워킹 기능과 다릅니다. [Azure Portal](https://portal.azure.com)이 내부적으로 Service Fabric 리소스 공급자를 사용하여 노드 및 애플리케이션에 대한 정보를 얻기 위해 클러스터를 호출한다는 것이 바로 그것입니다. Service Fabric 리소스 공급자는 관리 엔드포인트에서 HTTP 게이트웨이 포트(기본적으로 19080)에 대해 공개적으로 액세스 가능한 인바운드 액세스 권한이 필요합니다. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 관리 엔드포인트를 사용하여 클러스터를 관리합니다. 또한 Service Fabric 리소스 공급자는 Azure Portal에 표시하기 위해 클러스터에 대한 정보를 쿼리하는 데도 이 포트를 사용합니다. 

Service Fabric 리소스 공급자에서 포트 19080에 액세스할 수 없으면 포털에 *노드를 찾을 수 없음* 메시지가 표시되고 노드 및 애플리케이션 목록이 빈 상태로 나타납니다. Azure Portal에서 클러스터를 보려는 경우 부하 분산 장치가 공용 IP 주소를 노출해야 하고 네트워크 보안 그룹은 들어오는 포트 19080 트래픽을 허용해야 합니다. 설정이 이러한 요구 사항을 충족하지 않는 경우 Azure Portal에 클러스터의 상태가 표시되지 않습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="templates"></a>템플릿

Service Fabric 템플릿은 모두 [GitHub](https://github.com/Azure/service-fabric-scripts-and-templates/tree/master/templates/networking)에 있습니다. 다음 PowerShell 명령을 사용하여 템플릿을 있는 그대로 배포할 수 있습니다. 기존 Azure Virtual Network 템플릿 또는 고정 공용 IP 템플릿을 배포하는 경우 먼저 이 문서의 [초기 설치](#initialsetup) 섹션을 읽으세요.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>초기 설치

### <a name="existing-virtual-network"></a>기존 가상 네트워크

다음 예제에서는 **ExistingRG** 리소스 그룹에 ExistingRG-vnet이라는 기존 가상 네트워크로 시작합니다. 서브넷의 이름은 default입니다. 이러한 기본 리소스는 Azure Portal을 사용하여 표준 VM(가상 컴퓨터)을 만들 때 만들어집니다. VM을 만들지 않고 가상 네트워크 및 서브넷을 만들 수 있지만 기존 가상 네트워크에 클러스터를 추가하는 주요 목표는 다른 VM에 대한 네트워크 연결을 제공하는 것입니다. VM을 만드는 작업은 기존 가상 네트워크가 일반적으로 사용되는 방식의 좋은 예제가 됩니다. Service Fabric 클러스터가 공용 IP 주소 없이 내부 부하 분산 장치만 사용하는 경우 VM 및 해당 공용 IP를 보안 *점프 서버*로 사용할 수 있습니다.

### <a name="static-public-ip-address"></a>고정 공용 IP 주소

고정 공용 IP 주소는 일반적으로 할당된 VM과는 별도로 관리되는 전용 리소스입니다. Service Fabric 클러스터 리소스 그룹 자체와는 달리 전용 네트워킹 리소스 그룹에 프로비전됩니다. Azure Portal 또는 Powershell을 통해 동일한 ExistingRG 리소스 그룹에 이름이 staticIP1인 고정 공용 IP 주소를 만듭니다.

```powershell
PS C:\Users\user> New-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric 템플릿

이 문서의 예제에서는 Service Fabric template.json을 사용합니다. 클러스터를 만들기 전에 표준 포털 마법사를 사용하여 포털에서 템플릿을 다운로드할 수 있습니다. 또한 [보안 5 노드 Service Fabric 클러스터](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)와 같은 [샘플 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates) 중 하나를 사용할 수도 있습니다.

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>기존 가상 네트워크 또는 서브넷

1. 서브넷 매개 변수를 기존 서브넷의 이름으로 변경하고 기존 가상 네트워크를 참조하는 두 개의 새 매개 변수를 추가합니다.

    ```json
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```

2. 기존 서브넷을 사용하고 1단계에서 이 변수를 사용하지 않도록 설정했으므로 `Microsoft.Compute/virtualMachineScaleSets`의 `nicPrefixOverride` 특성을 주석으로 처리합니다.

    ```json
            /*"nicPrefixOverride": "[parameters('subnet0Prefix')]",*/
    ```

3. `vnetID` 변수를 기존 가상 네트워크를 가리키도록 변경합니다.

    ```json
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

4. Azure에서 새 가상 네트워크를 만들지 않도록 리소스에서 `Microsoft.Network/virtualNetworks`를 제거합니다.

    ```json
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

5. 새 가상 네트워크를 만드는 데만 의존하지 않도록 `Microsoft.Compute/virtualMachineScaleSets`의 `dependsOn` 특성에서 가상 네트워크를 주석 처리합니다.

    ```json
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

6. 템플릿 배포

    ```powershell
    New-AzResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    배포 후 가상 네트워크에는 새 확장 집합 VM이 포함됩니다. 가상 머신 확장 집합 노드 형식은 기존 가상 네트워크 및 서브넷을 표시합니다. 또한 RDP(원격 데스크톱 프로토콜 )를 사용하여 가상 네트워크에 이미 있던 VM에 액세스하고 새 확장 집합 VM을 Ping할 수도 있습니다.

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

또 다른 예제를 보려면 [Service Fabric에 국한되지 않는 항목](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)을 참조하세요.


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>고정 공용 IP 주소

1. 기존 고정 IP 리소스 그룹의 이름 및 FQDN(정규화된 도메인 이름)에 대한 매개 변수를 추가합니다.

    ```json
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. `dnsName` 매개 변수를 제거합니다. (고정 IP 주소에 매개 변수가 이미 하나 있습니다.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. 기존 고정 IP를 참조하는 변수를 추가합니다.

    ```json
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Azure에서 새 IP 주소를 만들지 않도록 리소스에서 `Microsoft.Network/publicIPAddresses`를 제거합니다.

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 새 IP 주소를 만드는 데만 의존하지 않도록 `Microsoft.Network/loadBalancers`의 `dependsOn` 특성에서 IP 주소를 주석 처리합니다.

    ```json
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. `Microsoft.Network/loadBalancers` 리소스에서 새로 만든 IP 주소가 아닌 기존의 고정 IP 주소를 참조하도록 `frontendIPConfigurations`의 `publicIPAddress` 요소를 변경합니다.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. `Microsoft.ServiceFabric/clusters` 리소스에서 `managementEndpoint`를 고정 IP 주소의 DNS FQDN으로 변경합니다. 보안 클러스터를 사용하는 경우 *http://* 를 *https://* 로 변경해야 합니다. (이 단계는 Service Fabric 클러스터에만 적용됩니다. 가상 머신 확장 집합을 사용하는 경우에는 이 단계를 건너뜁니다.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. 템플릿 배포

    ```powershell
    New-AzResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

배포 후에는 부하 분산 장치가 다른 리소스 그룹의 공용 고정 IP 주소에 바인딩된 것을 볼 수 있습니다. Service Fabric 클라이언트 연결 엔드포인트 및 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 엔드포인트는 고정 IP 주소의 DNS FQDN을 가리킵니다.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>내부 전용 부하 분산 장치

이 시나리오에서는 기본 Service Fabric 템플릿의 외부 부하 분산 장치를 내부 전용 부하 분산 장치로 바꿉니다. Azure Portal 및 Service Fabric 리소스 공급자에 대 한 의미는이 [문서의 앞부분에 있는](#allowing-the-service-fabric-resource-provider-to-query-your-cluster) 을 참조 하세요.

1. `dnsName` 매개 변수를 제거합니다. (필수는 아닙니다.)

    ```json
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. 경우에 따라 고정 할당 방법을 사용할 때 고정 IP 주소 매개 변수를 추가할 수 있습니다. 동적 할당 방법을 사용하는 경우 이 단계를 수행할 필요가 없습니다.

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Azure에서 새 IP 주소를 만들지 않도록 리소스에서 `Microsoft.Network/publicIPAddresses`를 제거합니다.

    ```json
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 새 IP 주소를 만드는 데만 의존하지 않도록 `Microsoft.Network/loadBalancers`의 IP 주소 `dependsOn` 특성을 제거합니다. 이제 부하 분산 장치는 가상 네트워크의 서브넷에 의존하므로 가상 네트워크 `dependsOn` 특성을 추가합니다.

    ```json
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. 부하 분산 장치의 `frontendIPConfigurations` 설정을 `publicIPAddress`를 사용하는 방식에서 서브넷 및 `privateIPAddress`를 사용하는 방식으로 변경합니다. `privateIPAddress`에서는 미리 정의된 고정 내부 IP 주소를 사용합니다. 동적 IP 주소를 사용하려면 `privateIPAddress` 요소를 제거한 다음 `privateIPAllocationMethod`를 **Dynamic**으로 변경합니다.

    ```json
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. `Microsoft.ServiceFabric/clusters` 리소스에서 내부 부하 분산 장치 주소를 가리키도록 `managementEndpoint`를 변경합니다. 보안 클러스터를 사용하는 경우 *http://* 를 *https://* 로 변경해야 합니다. (이 단계는 Service Fabric 클러스터에만 적용됩니다. 가상 머신 확장 집합을 사용하는 경우에는 이 단계를 건너뜁니다.)

    ```json
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. 템플릿 배포

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

배포 후에 부하 분산 장치는 프라이빗 정적 10.0.0.250 IP 주소를 사용합니다. 동일한 가상 네트워크에 다른 컴퓨터가 있는 경우 내부 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 엔드포인트로 이동할 수 있습니다. 부하 분산 장치 뒤의 노드 중 하나로 연결됩니다.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>내부 및 외부 부하 분산 장치

이 시나리오는 기존 단일 노드 형식 외부 부하 분산 장치로 시작하고 동일한 노드 형식에 대한 내부 부하 분산 장치를 추가합니다. 백 엔드 주소 풀에 연결된 백 엔드 포트는 단일 부하 분산 장치에만 할당할 수 있습니다. 애플리케이션 포트를 포함할 부하 분산 장치 및 관리 엔드포인트를 포함할 부하 분산 장치를 선택합니다(포트 19000 및 19080). 내부 부하 분산 장치에 관리 엔드포인트을 배치 하는 경우이 [문서의 앞부분에서](#allowing-the-service-fabric-resource-provider-to-query-your-cluster)설명한 Service Fabric 리소스 공급자 제한 사항에 유의 하세요. 사용하는 예제에서 관리 엔드포인트는 외부 부하 분산 장치에 유지됩니다. 또한 포트 80을 애플리케이션 포트에 추가한 후 내부 부하 분산 장치에 배치합니다.

두 노드 형식 클러스터에서 한 노드 형식은 외부 부하 분산 장치에 있습니다. 다른 노드 형식은 내부 부하 분산 장치에 있습니다. 두 노드 형식 클러스터를 사용하려면 포털에서 만들어진 두 노드 형식 템플릿(두 가지 부하 분산 장치와 함께 제공)에서 두 번째 부하 분산 장치를 내부 부하 분산 장치로 전환합니다. 자세한 내용은 [내부 전용 부하 분산 장치](#internallb) 섹션을 참조하세요.

1. 고정 내부 부하 분산 장치 IP 주소 매개 변수를 추가합니다. (동적 IP 주소 사용과 관련된 내용은 이 문서 이전 섹션을 참조하세요.)

    ```json
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. 애플리케이션 포트 80 매개 변수를 추가합니다.

3. 기존 네트워킹 변수의 내부 버전을 추가하려면 복사한 후 붙여 넣고 이름에 "-Int"를 추가합니다.

    ```json
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. 애플리케이션 포트 80을 사용하는 포털에서 생성된 템플릿으로 시작하는 경우 기본 포털 템플릿은 외부 부하 분산 장치에 AppPort1(포트 80)을 추가합니다. 이 경우 외부 부하 분산 장치 `loadBalancingRules` 및 프로브에서 AppPort1을 제거하여 내부 부하 분산 장치에 추가할 수 있도록 합니다.

    ```json
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. 두 번째 `Microsoft.Network/loadBalancers` 리소스를 추가합니다. [내부 전용 부하 분산 장치](#internallb) 섹션에서 만든 내부 부하 분산 장치와 비슷해 보이지만 "-Int" 부하 분산 장치 변수를 사용하고 애플리케이션 포트 80만 구현합니다. 또한 `inboundNatPools`를 제거하여 RDP 엔드포인트를 공용 부하 분산 장치에 유지합니다. 내부 부하 분산 장치의 RDP를 원할 경우 외부 부하 분산 장치의 `inboundNatPools`를 이 내부 부하 분산 장치로 이동합니다.

    ```json
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. `Microsoft.Compute/virtualMachineScaleSets` 리소스에 대한 `networkProfile`에서 내부 백 엔드 주소 풀을 추가합니다.

    ```json
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. 템플릿 배포

    ```powershell
    New-AzResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

배포 후 리소스 그룹에서 두 개의 부하 분산 장치를 볼 수 있습니다. 부하 분산 장치를 찾아보면 공용 IP 주소와 공용 IP 주소에 할당된 관리 엔드포인트(포트 19000 및 19080)를 볼 수 있습니다. 또한 고정 내부 IP 주소와 내부 부하 분산 장치에 할당된 애플리케이션 엔드포인트(포트 80)도 볼 수 있습니다. 두 부하 분산 장치 모두 동일한 가상 머신 확장 집합 백 엔드 풀을 사용합니다.

## <a name="notes-for-production-workloads"></a>프로덕션 워크 로드에 대 한 참고 사항

위의 GitHub 템플릿은 기본 SKU 인 SLB (Azure 표준 Load Balancer)에 대 한 기본 SKU를 사용 하도록 설계 되었습니다. 이 SLB는 SLA를 갖지 않으므로 프로덕션 워크 로드의 경우 표준 SKU를 사용 해야 합니다. 이에 대 한 자세한 내용은 [Azure 표준 Load Balancer 개요](/azure/load-balancer/load-balancer-standard-overview)를 참조 하세요. SLB에 대 한 표준 SKU를 사용 하는 모든 Service Fabric 클러스터는 각 노드 형식에 443 포트에서 아웃 바운드 트래픽을 허용 하는 규칙이 있는지 확인 해야 합니다. 이는 클러스터 설정을 완료 하는 데 필요 하며, 이러한 규칙이 없는 모든 배포는 실패 합니다. 위의 "내부 전용" 부하 분산 장치 예제에서는 포트 443에 대 한 아웃 바운드 트래픽을 허용 하는 규칙을 사용 하 여 추가 외부 부하 분산 장치를 템플릿에 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계
[클러스터 만들기](service-fabric-cluster-creation-via-arm.md)

배포 후 리소스 그룹에서 두 개의 부하 분산 장치를 볼 수 있습니다. 부하 분산 장치를 찾아보면 공용 IP 주소와 공용 IP 주소에 할당된 관리 엔드포인트(포트 19000 및 19080)를 볼 수 있습니다. 또한 고정 내부 IP 주소와 내부 부하 분산 장치에 할당된 애플리케이션 엔드포인트(포트 80)도 볼 수 있습니다. 두 부하 분산 장치 모두 동일한 가상 머신 확장 집합 백 엔드 풀을 사용합니다.

