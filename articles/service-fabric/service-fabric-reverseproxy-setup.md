---
title: Azure Service Fabric 역방향 프록시 설정
description: Azure Service Fabric 응용 프로그램에 대 한 역방향 프록시 서비스를 설정 하 고 구성 하는 방법을 이해 합니다.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 131440036896d323cbf821d7a220328456e1db36
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645449"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric에서 역방향 프록시 설정 및 구성
역방향 프록시는 Service Fabric 클러스터에서 실행되는 마이크로 서비스를 통해 http 엔드포인트가 있는 다른 서비스를 검색하고 통신할 수 있도록 하는 선택적 Azure Service Fabric 서비스입니다. 자세한 내용은 [Azure Service Fabric의 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요. 이 문서에서는 클러스터에서 역방향 프록시를 설정하고 구성하는 방법에 대해 설명합니다. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Azure Portal을 통해 역방향 프록시 사용

Azure Portal에서는 새 Service Fabric 클러스터를 만들 때 역방향 프록시를 사용하도록 설정하는 옵션이 제공됩니다. 기존 클러스터는 포털을 통해 역방향 프록시를 사용하도록 업그레이드할 수 없습니다. 

[Azure Portal을 사용하여 클러스터를 만들](./service-fabric-cluster-creation-via-portal.md) 때 역방향 프록시를 구성하려면 다음을 수행해야 합니다.

1. **2단계: 클러스터 구성**의 **노드 유형 구성** 아래에서 **역방향 프록시 사용**을 선택합니다.

   ![포털에서 역방향 프록시 사용](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (선택 사항) 보안 역방향 프록시를 구성하려면 SSL 인증서를 구성해야 합니다. **3단계: 보안**에 있는 **클러스터 보안 설정 구성**의 **구성 형식** 아래에서 **사용자 지정**을 선택합니다. 그런 다음, **역방향 프록시 SSL 인증서** 아래에서 **역방향 프록시의 SSL 인증서 포함**을 선택하고 인증서 세부 정보를 입력합니다.

   ![포털에서 보안 역방향 프록시 구성](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   클러스터를 만들 때 인증서를 사용하여 역방향 프록시를 구성하지 않으려는 경우 나중에 클러스터의 리소스 그룹에 대한 Resource Manager 템플릿을 통해 이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿을 통해 역방향 프록시 사용](#enable-reverse-proxy-via-azure-resource-manager-templates)을 참조하세요.

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 통해 역방향 프록시 사용

Azure에 있는 클러스터의 경우 Azure Resource Manager 템플릿을 사용하여 Service Fabric에서 역방향 프록시를 사용하도록 설정할 수 있습니다. 역방향 프록시는 클러스터를 만들 때 또는 나중에 클러스터를 업데이트하여 사용하도록 설정할 수 있습니다. 

새 클러스터의 경우 [사용자 지정 Resource Manager 템플릿을 만들거나](service-fabric-cluster-creation-via-arm.md) 템플릿 샘플을 사용할 수 있습니다. 

GitHub의 [보안 역방향 프록시 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample)에서 Azure 클러스터에 보안 역방향 프록시를 구성하는 데 도움이 되는 Resource Manager 템플릿 샘플을 찾을 수 있습니다. 인증서를 사용하여 보안 역방향 프록시를 구성하고 인증서 롤오버를 처리하는 데 사용할 지침과 템플릿은 추가 정보 파일의 [보안 클러스터에서 HTTPS 역방향 프록시 구성](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster)을 참조하세요.

기존 클러스터의 경우 [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) 또는 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli)를 사용하여 클러스터의 리소스 그룹에 대한 Resource Manager 템플릿을 내보낼 수 있습니다.

Resource Manager 템플릿이 준비되면 다음 단계를 사용하여 역방향 프록시를 사용하도록 설정할 수 있습니다.

1. 템플릿의 [매개 변수 섹션](../azure-resource-manager/templates/template-syntax.md) 에서 역방향 프록시에 대한 포트를 정의합니다.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. [**ServiceFabric/클러스터**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [리소스 종류 섹션](../azure-resource-manager/templates/template-syntax.md)에서 각 nodetype 개체의 포트를 지정 합니다.

    포트는 reverseProxyEndpointPort라는 매개 변수 이름으로 식별됩니다.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. 역방향 프록시에 대 한 포트에서 SSL 인증서를 구성 하려면 **ServiceFabric/클러스터** [리소스 종류 섹션](../resource-group-authoring-templates.md)의 ***reverseProxyCertificate*** 속성에 인증서를 추가 합니다.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>클러스터 인증서와 다른 역방향 프록시 인증서 지원
 역방향 프록시 인증서가 클러스터를 보호하는 인증서와 다른 경우 이전에 지정한 인증서를 가상 머신에 설치하고 ACL(액세스 제어 목록)에 추가하여 Service Fabric에서 액세스할 수 있게 합니다. 이 작업은 [**virtualMachineScaleSets/** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [리소스 형식 섹션](../resource-group-authoring-templates.md)에서 수행할 수 있습니다. 설치하려면 해당 인증서를 osProfile에 추가합니다. 템플릿의 확장 섹션은 ACL의 인증서를 업데이트할 수 있습니다.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> 클러스터 인증서와 다른 인증서를 사용하여 기존 클러스터에서 역방향 프록시를 사용하도록 설정하는 경우 먼저 역방향 프록시를 사용하도록 설정하기 전에 클러스터에 역방향 프록시 인증서를 설치하고 ACL을 업데이트합니다. 1-3 단계에서 역방향 프록시를 사용하도록 설정하기 위해 배포를 시작하기 전에 앞에서 언급한 설정을 사용하여 [Azure Resource Manager 템플릿](service-fabric-cluster-creation-via-arm.md) 배포를 완료합니다.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>독립 실행형 클러스터에서 역방향 프록시 사용

독립 실행형 클러스터의 경우 ClusterConfig.json 파일에서 역방향 프록시를 사용하도록 설정합니다. 클러스터를 만들 때 또는 기존 클러스터의 구성을 업그레이드하여 역방향 프록시를 사용하도록 설정할 수 있습니다. ClusterConfig.json 파일에서 사용할 수 있는 설정에 대한 자세한 내용은 [독립 실행형 클러스터 설정](./service-fabric-cluster-manifest.md)을 참조하세요.

다음 단계에서는 역방향 프록시를 사용하도록 설정하고, 필요에 따라 X.509 인증서를 통해 역방향 프록시를 보호하는 데 사용할 설정을 보여 줍니다. 

1. 역방향 프록시를 사용 하도록 설정 하려면 클러스터 구성의 **속성** 아래에 있는 노드 형식에 대해 **reverseProxyEndpointPort** 값을 설정 합니다. 다음 JSON은 "NodeType0" 유형의 노드에 대해 역방향 프록시 엔드포인트 포트를 19081로 설정 하는 작업을 보여 줍니다.

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (선택 사항) 보안 역방향 프록시의 경우 **properties** 아래의 **security** 섹션에서 인증서를 구성합니다. 
   - 개발 또는 테스트 환경의 경우 **ReverseProxyCertificate** 설정을 사용할 수 있습니다.

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - 프로덕션 환경의 경우 **ReverseProxyCertificateCommonNames** 설정을 사용하는 것이 좋습니다.

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   독립 실행형 클러스터에 대한 인증서를 구성하고 관리하는 방법 및 역방향 프록시를 보호하는 데 사용되는 인증서를 구성하는 방법에 대한 자세한 내용은 [X509 인증서 기반 보안](./service-fabric-windows-cluster-x509-security.md)을 참조하세요.

ClusterConfig.json 파일이 역방향 프록시를 사용하도록 수정되면 [클러스터 구성 업그레이드](service-fabric-cluster-config-upgrade-windows-server.md)의 지침에 따라 클러스터에 변경 내용을 푸시합니다.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Azure Load Balancer를 통해 공용 포트에서 역방향 프록시 노출

Azure 클러스터 외부에서 역방향 프록시를 처리하려면 역방향 프록시 포트에 대한 Azure Load Balancer 규칙과 Azure Health Probe를 설정합니다. 이러한 단계는 클러스터를 만든 후에 언제든지 Azure Portal 또는 Resource Manager 템플릿을 사용하여 수행할 수 있습니다. 

> [!WARNING]
> 부하 분산 장치에서 역방향 프록시의 포트를 구성하면 HTTP 엔드포인트를 표시하는 클러스터의 모든 마이크로 서비스의 주소를 클러스터 외부에서 지정할 수 있습니다. 즉, 내부용으로 의도된 마이크로 서비스를 악의적인 사용자가 검색할 수 있습니다. 이는 잠재적으로 악용될 수 있는 심각한 취약성을 나타냅니다. 예를 들어 다음과 같습니다.
>
> * 악의적인 사용자는 충분히 강화된 공격 노출 영역이 없는 내부 서비스를 반복적으로 호출하여 서비스 거부 공격을 시작할 수 있습니다.
> * 악의적인 사용자가 잘못된 패킷을 내부 서비스로 전송하여 의도하지 않은 동작이 발생할 수 있습니다.
> * 내부용으로 의도된 서비스는 클러스터 외부의 서비스에 노출되지 않아야 하는 프라이빗 정보나 중요한 정보를 반환할 수 있으므로 이 민감한 정보가 악의적인 사용자에게 노출될 수 있습니다. 
>
> 역방향 프록시 포트를 공개하기 전에 클러스터 및 클러스터에서 실행 중인 앱의 잠재적인 보안 결과를 완전히 파악하고 완화해야 합니다. 
>

독립 실행형 클러스터에 대한 역방향 프록시를 공개적으로 노출하려는 경우 이렇게 수행하는 방식은 클러스터를 호스팅하는 시스템에 따라 다르며 이 문서의 범위를 벗어납니다. 그러나 공개적인 역방향 프록시 노출에 대한 이전 경고는 여전히 적용됩니다.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Azure Portal을 사용하여 역방향 프록시 노출 

1. Azure Portal에서 클러스터에 대한 리소스 그룹, 해당 클러스터에 대한 부하 분산 장치를 차례로 클릭합니다.
2. 역방향 프록시 포트에 대한 상태 프로브를 추가하려면 부하 분산 장치 창에 있는 왼쪽 분할 창의 **설정** 아래에서 **상태 프로브**를 클릭합니다. 그런 다음, [상태 프로브] 창의 위쪽에서 **추가**를 클릭하고, 역방향 프록시 포트에 대한 세부 정보를 입력한 다음, **확인**을 클릭합니다. 클러스터를 만들 때 변경하지 않는 한 역방향 프록시 포트는 기본적으로 19081입니다.

   ![역방향 프록시 상태 프로브 구성](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. 역방향 프록시 포트를 노출하는 Load Balancer 규칙을 추가하려면 부하 분산 장치 창에 있는 왼쪽 분할 창의 **설정** 아래에서 **부하 분산 규칙**을 클릭합니다. 그런 다음, [부하 분산 규칙] 창의 위쪽에서 **추가**를 클릭하고, 역방향 프록시 포트에 대한 세부 정보를 입력합니다. **포트** 값을 역방향 프록시가 노출되도록 하려는 포트로 설정하고, **백 엔드 포트** 값을 역방향 프록시를 사용하도록 설정할 때 설정한 포트로 설정하고, **상태 프로브** 값을 이전 단계에서 구성한 상태 프로브로 설정해야 합니다. 다른 필드를 적절하게 설정한 다음, **확인**을 클릭합니다.

   ![역방향 프록시에 대한 부하 분산 장치 규칙 구성](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Resource Manager 템플릿을 통해 역방향 프록시 노출

다음 JSON에서는 [Azure Resource Manager 템플릿을 통해 역방향 프록시 사용](#enable-reverse-proxy-via-azure-resource-manager-templates)에서 사용한 것과 동일한 템플릿을 참조합니다. Resource Manager 템플릿을 만들거나 기존 클러스터에 대한 템플릿을 내보내는 방법에 대한 자세한 내용은 이 문서의 해당 섹션을 참조하세요.  [**Microsoft. 네트워크/loadBalancers 조정기**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [리소스 유형 섹션](../resource-group-authoring-templates.md)에 변경 내용이 적용 됩니다.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>패브릭 설정을 사용하여 역방향 프록시 동작 사용자 지정

Azure에서 호스팅되는 클러스터에 대한 Resource Manager 템플릿 또는 독립 실행형 클러스터에 대한 ClusterConfig.json 파일의 패브릭 설정을 통해 역방향 프록시의 동작을 사용자 지정할 수 있습니다. 역방향 프록시 동작을 제어하는 설정은 클러스터 **properties** 섹션 아래에 있는 **fabricSettings** 섹션의 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 섹션에 있습니다. 

예를 들어 다음 JSON과 같이 **DefaultHttpRequestTimeout** 값을 설정하여 역방향 프록시 요청에 대한 시간 제한을 180초로 설정할 수 있습니다.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Azure 클러스터에 대한 패브릭 설정을 업데이트하는 방법에 대한 자세한 내용은 [Resource Manager 템플릿을 사용하여 클러스터 설정 사용자 지정](service-fabric-cluster-config-upgrade-azure.md)을 참조하세요. 독립 실행형 클러스터의 경우 [독립 실행형 클러스터에 대한 클러스터 설정 사용자 지정](service-fabric-cluster-config-upgrade-windows-server.md)을 참조하세요. 

몇 가지 패브릭 설정은 역방향 프록시와 서비스 간의 보안 통신을 설정하는 데 사용됩니다. 이러한 설정에 대 한 자세한 내용은 [역방향 프록시를 사용 하 여 보안 서비스에 연결](service-fabric-reverseproxy-configure-secure-communication.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [역방향 프록시를 사용하여 보안 HTTP 서비스에 전달 설정](service-fabric-reverseproxy-configure-secure-communication.md)
* 역방향 프록시 구성 옵션은 [Service Fabric 클러스터 설정 사용자 지정의 ApplicationGateway/Http 섹션](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)을 참조하세요.
