---
title: Azure 배포용 템플릿 연결 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 연결된 템플릿을 사용하여 모듈식 템플릿 솔루션을 만드는 방법을 설명합니다. 매개 변수 값을 전달하고 매개 변수 파일 및 동적으로 생성된 URL을 지정하는 방법을 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2019
ms.author: tomfitz
ms.openlocfilehash: d4ecccf8787e369b9a3270eab2d01a01ce7ae0c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363460"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Azure 리소스를 배포할 때 연결 및 중첩된 템플릿 사용

솔루션을 배포하려면 단일 템플릿 또는 많은 관련 템플릿이 있는 주 템플릿 중 하나를 사용할 수 있습니다. 관련 템플릿은 주 템플릿에 연결된 별도 파일이거나 주 템플릿 내에 중첩된 템플릿일 수 있습니다.

중소기업에게는 단일 템플릿이 더 간편하게 이해하고 유지 관리할 수 있습니다. 모든 리소스 및 값을 단일 파일에서 볼 수 있습니다. 고급 시나리오의 경우 연결된 템플릿을 통해 솔루션을 대상 구상 요소로 분리하고 템플릿을 재사용할 수 있습니다.

연결된 템플릿을 사용할 때는 배포 중에 매개 변수 값을 받는 기본 템플릿을 만듭니다. 기본 템플릿은 연결된 모든 템플릿을 포함하여 필요에 따라 해당 템플릿에 값을 전달합니다.

자습서의 경우 [자습서: 연결된 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-linked-templates.md)를 참조하세요.

> [!NOTE]
> 연결된 템플릿 또는 중첩된 템플릿의 경우 [증분](deployment-modes.md) 배포 모드만 사용할 수 있습니다.
>

## <a name="link-or-nest-a-template"></a>템플릿 연결 또는 중첩

다른 템플릿에 연결하려면 **배포** 리소스를 기본 템플릿에 추가합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

배포 리소스에 제공하는 속성은 외부 템플릿에 연결하는지 또는 기본 템플릿에 인라인 템플릿을 중첩하는지 여부에 따라 달라집니다.

### <a name="nested-template"></a>중첩된 템플릿

템플릿을 주 템플릿 내에 중첩하려면 **template** 속성을 사용하고 템플릿 구문을 지정합니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-07-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> 중첩된 템플릿의 경우, 중첩된 템플릿 내에 정의된 매개 변수 또는 변수를 사용할 수 없습니다. 주 템플릿의 매개 변수 및 변수를 사용할 수 있습니다. 앞의 예제에서 `[variables('storageName')]`은 중첩된 템플릿이 아닌 주 템플릿에서 값을 검색합니다. 이러한 제한은 외부 템플릿에 적용되지 않습니다.
>
> 내에 정의 된 두 개의 리소스에 대 한 다른에 따라 달라 집니다 중첩 템플릿 및 리소스, 종속성의 값이 단순히 이러한 종속된 리소스의 이름:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> `reference` 함수는 중첩된 템플릿의 출력 섹션에 사용할 수 없습니다. 중첩된 템플릿에서 배포된 리소스의 값을 반환하려면 중첩된 템플릿을 연결된 템플릿으로 변환합니다.

중첩된 템플릿에는 표준 템플릿과 [동일한 속성](resource-group-authoring-templates.md)이 필요합니다.

### <a name="external-template-and-external-parameters"></a>외부 템플릿 및 외부 매개 변수

외부 템플릿과 매개 변수 파일에 연결하려면 **templateLink** 및 **parametersLink**를 사용합니다. 템플릿에 연결할 때 Resource Manager 서비스가 해당 템플릿에 액세스할 수 있어야 합니다. 로컬 네트워크에서만 사용 가능한 파일이나 로컬 파일은 지정할 수 없습니다. **http** 또는 **https** 중 하나를 포함하는 URI 값만 제공할 수 있습니다. 한 가지 옵션은 연결된 템플릿을 저장소 계정에 배치하고 해당 항목의 URI를 사용하는 것입니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
    }
    }
  }
]
```

템플릿 또는 매개 변수에 대한 `contentVersion` 속성을 제공하지 않아도 됩니다. 콘텐츠 버전 값을 제공하지 않으면 현재 버전의 템플릿이 배포됩니다. 콘텐츠 버전 값을 제공하는 경우에는 연결된 템플릿의 버전과 일치해야 합니다. 그렇지 않으면 오류와 함께 배포에 실패합니다.

### <a name="external-template-and-inline-parameters"></a>외부 템플릿 및 인라인 매개 변수

또는 매개 변수를 인라인으로 제공할 수 있습니다. 인라인 매개 변수와 매개 변수 파일에 대한 링크를 둘 다 사용할 수는 없습니다. `parametersLink` 및 `parameters`를 둘 다 지정하면 오류를 발생하며 배포가 실패합니다.

기본 템플릿의 값을 연결된 템플릿에 전달하려면 **매개 변수**를 사용합니다.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-variables-to-link-templates"></a>변수를 사용하여 템플릿 연결

앞의 예제에서는 템플릿 링크에 대한 하드 코딩된 URL 값을 보여 주었습니다. 이 방법은 간단한 템플릿에는 적용될 수 있지만 대규모 모듈식 템플릿 집합으로 작업하는 경우에는 가능하지 않습니다. 대신, 주 템플릿에 대한 기본 URL을 보관하는 정적 변수를 만든 다음 해당 기본 URL에서 연결된 템플릿에 대한 URL을 동적으로 만들 수 있습니다. 이 방식의 경우 주 템플릿에서만 정적 변수를 변경하면 되므로 템플릿을 쉽게 이동하거나 분기할 수 있다는 장점이 있습니다. 주 템플릿은 분해된 템플릿 전체에서 올바른 URI를 전달합니다.

다음 예제에서는 기본 URL을 사용하여 연결된 템플릿에 대한 두 개의 URL을 만드는 방법을 보여 줍니다(**sharedTemplateUrl** 및 **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

또한 [deployment()](resource-group-template-functions-deployment.md#deployment) 를 사용하여 현재 템플릿에 대한 기본 URL을 가져올 수 있으며 동일한 위치에 있는 다른 템플릿에 대한 URL를 가져올 수 있습니다. 이 방법은 템플릿 위치가 변경되거나 템플릿 파일에서 URL 하드 코딩을 방지하려는 경우 유용합니다. templateLink 속성은 URL을 사용하여 원격 템플릿을 연결할 때만 반환됩니다. 로컬 템플릿을 사용하는 경우 이 속성을 사용할 수 없습니다.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>연결된 템플릿에서 값 가져오기

연결된 템플릿에서 출력 값을 가져오려면 `"[reference('deploymentName').outputs.propertyName.value]"` 같은 구문으로 속성 값을 검색합니다.

연결된 템플릿에서 출력 속성을 가져올 때 속성 이름에 대시를 포함할 수 없습니다.

다음 예에서는 연결된 템플릿을 참조하고 출력 값을 가져오는 방법을 보여 줍니다. 연결된 템플릿이 간단한 메시지를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

주 템플릿은 연결된 템플릿을 배포하고 반환된 값을 가져옵니다. 배포 리소스를 이름으로 참조하고 연결된 템플릿에서 반환한 속성의 이름을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

다른 리소스 형식과 마찬가지로 연결된 템플릿과 기타 리소스 간에 종속성을 설정할 수 있습니다. 따라서 다른 리소스에 연결된 템플릿의 출력 값이 필요한 경우 연결된 템플릿이 먼지 배포되었는지 확인해야 합니다. 또는 연결된 템플릿이 다른 리소스에 종속될 경우 연결된 템플릿 이전에 다른 리소스가 배포되었는지 확인해야 합니다.

다음 예제에서는 공개 IP 주소를 배포하고 리소스 ID를 반환하는 템플릿을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Load Balancer를 배포할 때 이전 템플릿의 공개 IP 주소를 사용하려면 템플릿에 연결하고 배포 리소스에서 종속성을 추가합니다. Load Balancer의 공개 IP 주소는 연결된 템플릿에서 값을 출력하도록 설정됩니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>배포 기록의 연결 및 중첩된 템플릿

Resource Manager는 각 템플릿을 배포 기록에서 별도 배포로 처리합니다. 따라서 3개의 연결 또는 중첩된 템플릿이 있는 주 템플릿은 배포 기록에 다음과 같이 나타납니다.

![배포 기록](./media/resource-group-linked-templates/deployment-history.png)

기록에서 이러한 개별 항목을 사용하여 배포 후 출력 값을 가져올 수 있습니다. 다음 템플릿은 공개 IP 주소를 만들고 해당 IP 주소를 출력합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

다음 템플릿은 위에 템플릿에 연결됩니다. 세 개의 공개 IP 주소를 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

배포 후 다음 PowerShell 스크립트를 통해 출력 값을 검색할 수 있습니다.

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

또는 Bash 셸의 Azure CLI 스크립트:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>외부 템플릿 보호

연결된 템플릿은 외부에서 사용할 수 있어야 하지만 일반에 공개할 필요는 없습니다. 저장소 계정 소유자만 액세스할 수 있는 개인 저장소 계정에 템플릿을 추가할 수 있습니다. 그런 다음 배포하는 동안 액세스할 수 있도록 공유 액세스 서명(SAS) 토큰을 만듭니다. 링크된 템플릿 URI에 SAS 토큰을 추가합니다. 토큰이 보안 문자열로 전달되었지만 SAS 토큰을 포함한 링크된 템플릿 URI가 배포 작업에 로그됩니다. 노출을 최소화하려면 토큰에 만료 날짜를 설정합니다.

매개 변수 파일은 SAS 토큰으로 액세스를 제한할 수 있습니다.

다음 예제에서는 템플릿에 연결할 때 SAS 토큰을 전달하는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell에서는 다음 명령을 사용하여 컨테이너용 토큰을 얻고 템플릿을 배포합니다. **containerSasToken** 매개 변수는 템플릿에 정의됩니다. **New-AzResourceGroupDeployment** 명령의 매개 변수가 아닙니다.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Bash 셸의 Azure CLI에서는 컨테이너용 토큰을 얻고 다음 코드를 사용하여 템플릿을 배포합니다.

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>예제 템플릿

다음 예제에서는 연결된 템플릿의 일반적인 사용 방법을 보여 줍니다.

|기본 템플릿  |연결된 템플릿 |설명  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | 연결된 템플릿에서 문자열을 반환합니다. |
|[공용 IP 주소가 있는 Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |연결된 템플릿에서 공용 IP 주소를 반환하고 부하 분산 장치에서 해당 값을 설정합니다. |
|[여러 IP 주소](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [연결된 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |연결된 템플릿에서 여러 공용 IP 주소를 만듭니다.  |

## <a name="next-steps"></a>다음 단계

* 자습서를 진행하려면 [자습서: 연결된 Azure Resource Manager 템플릿 만들기](./resource-manager-tutorial-create-linked-templates.md)를 참조하세요.
* 리소스 배포 순서를 정의하는 방법을 알아보려면 [Azure Resource Manager 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요.
* 하나의 리소스를 정의하되 해당 리소스의 여러 인스턴스를 만드는 방법을 알아보려면 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)를 참조하세요.
* 저장소 계정에서 템플릿을 설정하고 SAS 토큰을 생성하는 절차는 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](resource-group-template-deploy.md) 또는 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](resource-group-template-deploy-cli.md)를 참조하세요.
