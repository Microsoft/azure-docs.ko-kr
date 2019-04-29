---
title: Azure Service Fabric 컨테이너 서비스에 대한 네트워킹 모드 구성 | Microsoft Docs
description: Azure Service Fabric에서 지원하는 다양한 네트워킹 모드를 설정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 6f14b3184cabd1dfd84f04260f6b8c831037cbcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718158"
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric 컨테이너 네트워킹 모드

컨테이너 서비스용 Azure Service Fabric 클러스터는 기본적으로 **nat** 네트워킹 모드를 사용합니다. 둘 이상의 컨테이너 서비스가 동일한 포트에서 수신 대기 중이며 nat 모드가 사용되고 있으면 배포 오류가 발생할 수 있습니다. 동일한 포트에서 수신 대기하는 여러 컨테이너 서비스를 지원하기 위해 Service Fabric은 **오픈** 네트워킹 모드(버전 5.7 이상)를 제공합니다. 오픈 모드에서는 각 컨테이너 서비스에 동일한 포트에서 수신 대기하는 여러 서비스를 지원하는 동적으로 할당 된 내부 IP 주소가 있습니다.  

서비스 매니페스트에 고정 엔드포인트가 있는 컨테이너 서비스가 하나 있으면 오픈 모드를 사용하여 배포 오류 없이 새 서비스를 만들고 삭제할 수 있습니다. 동일한 docker-compose.yml 파일을 정적 포트 매핑과 함께 사용하여 여러 서비스를 만들 수도 있습니다.

컨테이너 서비스가 다시 시작되거나 클러스터의 다른 노드로 이동하면 IP 주소가 변경됩니다. 따라서 동적으로 할당된 IP 주소를 사용하여 컨테이너 서비스를 검색하는 것을 좋지 않습니다. 서비스 검색에는 Service Fabric 명명 서비스 또는 DNS 서비스 만 사용해야 합니다. 

>[!WARNING]
>Azure 가상 네트워크 당 총 65, 356 Ip의 허용 됩니다. 노드 수 및 컨테이너 서비스 인스턴스 (오픈 모드를 사용 하는) 숫자의 합계는 가상 네트워크 내에서 65, 356 Ip를 초과할 수 없습니다. 고밀도 시나리오의 경우 nat 네트워킹 모드를 사용하는 것이 좋습니다. 또한 부하 분산 장치와 같은 다른 종속성을 갖습니다 다른 [제한 사항](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits) 고려해 야 합니다. 노드당 50 Ip은 현재 테스트 되었으며 안정적인 입증 합니다. 
>

## <a name="set-up-open-networking-mode"></a>오픈 네트워킹 모드 설정

1. Azure Resource Manager 템플릿을 설정합니다. 클러스터 리소스의 **fabricSettings** 섹션에서 DNS 서비스와 IP 공급자를 사용하도록 설정합니다. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. 가상 머신 확장 집합 리소스의 네트워크 프로필 섹션을 설정합니다. 그러면 클러스터의 각 노드에 여러 IP 주소를 구성할 수 있습니다. 다음 예제에서는 Windows/Linux Service Fabric 클러스터에 노드당 다섯 개의 IP 주소를 설정합니다. 각 노드의 포트에서 수신 대기하는 서비스 인스턴스를 5개 포함할 수 있습니다. Azure Load Balancer에서 다섯 개의 IP에 액세스할 수 있도록 하려면 아래 그림과 같이 Azure Load Balancer 백 엔드 주소 풀에 다섯 개의 IP를 등록합니다.  또한 템플릿 위쪽의 variables 섹션에 변수를 추가해야 합니다.

    변수에 이 섹션 추가:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    가상 머신 확장 집합 리소스에 이 섹션 추가:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 
3. Windows 클러스터에만 다음 값을 사용하여 가상 네트워크에 UDP/53 포트를 여는 Azure NSG(네트워크 보안 그룹) 규칙을 설정합니다.

   |설정 |값 | |
   | --- | --- | --- |
   |우선 순위 |2000 | |
   |이름 |Custom_Dns  | |
   |원본 |VirtualNetwork | |
   |대상 | VirtualNetwork | |
   |서비스 | DNS(UDP/53) | |
   |액션(Action) | 허용  | |
   | | |

4. 각 서비스에 대해 애플리케이션 매니페스트에서 네트워킹 모드를 지정합니다. `<NetworkConfig NetworkType="Open">` **오픈** 네트워킹 모드에서는 서비스가 전용 IP 주소를 갖게 됩니다. 모드를 지정하지 않으면 서비스는 기본적으로 **nat** 모드가 됩니다. 다음 매니페스트 예제에서 `NodeContainerServicePackage1` 및 `NodeContainerServicePackage2` 서비스는 동일한 포트에서 각각 수신 대기할 수 있습니다(두 서비스는 모두 `Endpoint1`에서 수신 대기 중). 오픈 네트워킹 모드를 지정하면 `PortBinding` 구성을 지정할 수 없습니다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    Windows 클러스터용 애플리케이션 내의 서비스에서 다른 네트워킹 모드를 혼합하고 일치시킬 수 있습니다. 일부 서비스는 오픈 모드를 사용하고 다른 서비스는 nat 모드를 사용할 수 있습니다. 서비스가 nat 모드를 사용하도록 구성된 경우 서비스가 수신 대기하는 포트는 고유해야 합니다.

    >[!NOTE]
    >Linux 클러스터에서는 다른 서비스에 네트워킹 모드를 혼합하는 것이 지원되지 않습니다. 
    >

5. **열기** 모드를 선택하면 서비스 패키지에 코드 패키지가 하나만 있어도, 서비스 매니페스트의 **엔드포인트** 정의가 엔드포인트에 해당하는 코드 패키지를 명시적으로 가리켜야 합니다. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Windows의 경우 VM을 다시 부팅하면 개방형 네트워크가 다시 생성됩니다. 이는 네트워킹 스택의 기본 문제를 완화하기 위한 조치입니다. 기본 동작은 네트워크를 다시 만드는 것입니다. 이 동작을 해제해야 하는 경우 다음 구성을 사용한 후 구성 업그레이드를 수행하면 됩니다.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>다음 단계
* [Service Fabric 애플리케이션 모델 이해](service-fabric-application-model.md)
* [Service Fabric 서비스 매니페스트 리소스에 대해 자세히 알아보기](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
