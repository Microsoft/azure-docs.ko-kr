---
title: Azure Service Fabric 클러스터 템플릿 만들기
description: Service Fabric 클러스터의 Resource Manager 템플릿을 만드는 방법을 알아봅니다. 클라이언트 인증의 보안, Azure Key Vault 및 Azure AD(Azure Active Directory)를 구성합니다.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: a00f2bc505acd89d9fb9488565b6235bf7d146ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463259"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>서비스 패브릭 클러스터 Resource Manager 템플릿 만들기

[Azure Service Fabric 클러스터](service-fabric-deploy-anywhere.md)는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 머신 집합입니다. Azure에서 실행 중인 Service Fabric 클러스터는 Azure 리소스이며, Resource Manager를 사용하여 배포, 관리 및 모니터링됩니다.  이 문서에서는 Azure에서 실행 중인 Service Fabric 클러스터의 Resource Manager 템플릿을 만드는 방법을 설명합니다.  템플릿이 완성되면 [Azure에 클러스터를 배포](service-fabric-cluster-creation-via-arm.md)할 수 있습니다.

클러스터 보안은 클러스터가 먼저 설정된 후에 구성되며 나중에 변경할 수 없습니다. 클러스터를 설정 하기 전에 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조 하세요. Azure에서 Service Fabric은 x509 인증서를 사용하여 클러스터 및 해당 엔드포인트를 보호하고, 클라이언트를 인증하고, 데이터를 암호화합니다. 관리 엔드포인트에 대한 액세스를 보호하려면 Azure Active Directory를 사용하는 것도 좋습니다. Azure AD 테넌트 및 사용자는 클러스터를 만들기 전에 생성되어야 합니다.  자세한 내용은 [클라이언트를 인증하도록 Azure AD 설정](service-fabric-cluster-creation-setup-aad.md)을 읽어보세요.

프로덕션 워크로드를 실행할 프로덕션 클러스터를 배포하기 전에 먼저 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)을 읽어 보세요.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기
샘플 Resource Manager 템플릿은 [GitHub의 Azure 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 사용할 수 있습니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다.

이 문서에서는 [5 노드 보안 클러스터][service-fabric-secure-cluster-5-node-1-nodetype] 예제 템플릿과 템플릿 매개 변수를 사용 합니다. *azuredeploy.json* 및 *azuredeploy.parameters.json*을 컴퓨터에 다운로드하고 주로 사용하는 텍스트 편집기에서 두 파일을 여세요.

> [!NOTE]
> 국가별 클라우드(Azure Government, Azure 중국, Azure 독일)의 경우 다음 `fabricSettings`(`AADLoginEndpoint`, `AADTokenEndpointFormat` 및 `AADCertEndpointFormat`)를 템플릿에 추가해야 합니다.

## <a name="add-certificates"></a>인증서 추가
인증서 키를 포함하는 Key Vault 참조하여 Cluster Resource Manager 템플릿에 인증서를 추가합니다. Resource Manager 템플릿 매개 변수 파일(*azuredeploy.parameters.json*)에 해당 키 자격 증명 모음 매개 변수 및 값을 추가합니다.

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>가상 머신 확장 집합 osProfile에 모든 인증서 추가
클러스터에 설치된 모든 인증서는 확장 집합 리소스(Microsoft.Compute/virtualMachineScaleSets)의 **osProfile** 섹션에 구성되어야 합니다. 이 작업은 리소스 공급자에게 인증서를 VM에 설치하도록 지시합니다. 이 설치에는 클러스터 인증서와, 애플리케이션에 사용하려는 모든 애플리케이션 보안 인증서가 포함됩니다.

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서 구성

또한 클러스터 인증 인증서는 서비스 패브릭 클러스터 리소스(Microsoft.ServiceFabric/clusters)와, 가상 머신 확장 집합 리소스의 가상 머신 확장 집합에 대해 Service Fabric 확장에서 모두 구성되어야 합니다. 이렇게 해야 Service Fabric 리소스 공급자는 클러스터 인증에 대한 사용 및 관리 엔드포인트에 대한 서버 인증을 위해 그것을 구성할 수 있습니다.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>인증서 정보를 가상 머신 확장 집합 리소스에 추가

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>인증서 정보를 Service Fabric 클러스터 리소스에 추가

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD 구성을 추가하여 클라이언트 액세스를 위해 Azure AD 사용

인증서 키를 포함하는 Key Vault를 참조하여 Cluster Resource Manager 템플릿에 Azure AD 구성을 추가합니다. Resource Manager 템플릿 매개 변수 파일(*azuredeploy.parameters.json*)에 해당 Azure AD 매개 변수 및 값을 추가합니다. 

> [!NOTE]
> Linux에서 클러스터를 만들기 전에 Azure AD 테넌트 및 사용자를 만들어야 합니다.  자세한 내용은 [클라이언트를 인증하도록 Azure AD 설정](service-fabric-cluster-creation-setup-aad.md)을 읽어보세요.

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>이 값으로 매개 변수 파일을 채웁니다.

마지막으로, Key Vault 및 Azure AD PowerShell 명령의 출력 값을 매개 변수 파일을 채우는 데 사용합니다.

Azure Service Fabric RM PowerShell 모듈을 사용하려는 경우 클러스터 인증서 정보를 채울 필요가 없습니다. 시스템에서 클러스터 보안을 위해 자체 서명 인증서를 생성하려는 경우 Null로만 유지합니다. 

> [!NOTE]
> 이러한 빈 매개 변수 값을 선택하고 채우는 RM 모듈의 경우 매개 변수 이름은 아래 이름과 일치해야 합니다.

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

애플리케이션 인증서를 사용하거나 키 자격 증명 모음에 업로드한 기존 클러스터를 사용하는 경우 이 정보를 가져와서 채워야 합니다.

RM 모듈에는 Azure AD 구성을 생성하는 기능이 없으므로 클라이언트 액세스에 Azure AD를 사용하려는 경우 채워야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>템플릿 테스트
매개 변수 파일로 Resource Manager 템플릿을 테스트하려면 다음 PowerShell 명령을 사용합니다.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

문제가 발생하고 복잡한 메시지가 표시된 경우에 "-Debug"를 옵션으로 사용합니다.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

다음 다이어그램은 Key Vault 및 Azure AD 구성이 Resource Manager 템플릿 어디에 적합한지 보여 줍니다.

![리소스 관리자 종속성 맵][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>다음 단계
이제 클러스터의 템플릿이 있으니 [클러스터를 Azure에 배포](service-fabric-cluster-creation-via-arm.md)하는 방법을 알아보세요.  아직 읽어 보지 않았다면 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)을 읽은 후 프로덕션 클러스터를 배포하세요.

이 문서에서 배포된 리소스의 JSON 구문과 속성에 대해 알아보려면 다음을 참조하세요.

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
