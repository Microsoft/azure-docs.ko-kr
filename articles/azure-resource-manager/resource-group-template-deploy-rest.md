---
title: REST API 및 템플릿으로 리소스 배포 | Microsoft Docs
description: Azure Resource Manager 및 Resource Manager REST API를 사용 하 여 Azure에 리소스를 배포 합니다. 리소스는 Resource Manager 템플릿에 정의됩니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 15e4a7058dc1e74c726644e86c58381003eee937
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782976"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>리소스 관리자 템플릿과 리소스 관리자 REST API로 리소스 배포

이 문서에서는 리소스 관리자 템플릿으로 리소스 관리자 REST API를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다.  

요청 본문 또는 파일 링크에는 템플릿을 포함할 수 있습니다. 템플릿을 사용할 경우 템플릿은 로컬 파일이거나 URI를 통해 사용 가능한 외부 파일일 수 있습니다. 템플릿이 저장소 계정에 있는 경우, 템플릿에 대한 액세스를 제한하고 배포 중에 SAS(공유 액세스 서명) 토큰을 제공할 수 있습니다.

## <a name="deployment-scope"></a>배포 범위

Azure 구독 또는 구독 내에서 리소스 그룹 배포를 대상으로 지정할 수 있습니다. 대부분의 경우에서 리소스 그룹 배포를 대상으로 수 있습니다. 구독 배포를 사용 하 여 정책 및 역할 할당은 구독에서 적용 하 합니다. 또한 리소스 그룹을 만들고 리소스를 배포할 구독 배포를 사용 합니다. 배포의 범위에 따라 다른 명령을 사용할 수 있습니다.

배포 하는 **리소스 그룹**를 사용 하 여 [배포 만들기](/rest/api/resources/deployments/createorupdate)합니다.

배포 하는 **구독**를 사용 하 여 [Deployments-구독 범위에서 만들기](/rest/api/resources/deployments/createorupdateatsubscriptionscope)합니다.

이 문서의 예제에서는 리소스 그룹 배포를 사용합니다. 구독 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](deploy-to-subscription.md)합니다.

## <a name="deploy-with-the-rest-api"></a>REST API를 사용하여 배포

1. 인증 토큰을 포함하여 [공통 매개 변수 및 헤더](/rest/api/azure/)를 설정합니다.

1. 기본 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다. 솔루션에 필요한 구독 ID, 새 리소스 그룹 이름 및 위치를 제공합니다. 자세한 내용은 [리소스 그룹 만들기](/rest/api/resources/resourcegroups/createorupdate)를 참조하세요.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
   ```

   다음과 같은 요청 본문을 사용합니다.

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. 배포를 실행하기 전에 [템플릿 배포 유효성 검사](/rest/api/resources/deployments/validate) 작업을 실행하여 배포 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다(다음 단계에 표시됨).

1. 배포를 만듭니다. 템플릿의 구독 ID, 리소스 그룹 이름, 배포 이름 및 템플릿 링크를 제공합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](#parameter-file)을 참조하세요. 리소스 그룹을 만드는 REST API에 대한 정보는 [템플릿 배포 만들기](/rest/api/resources/deployments/createorupdate)를 참조하세요. **mode**가 **Incremental**로 설정되어 있습니다. 전체 배포를 실행하려면 **mode**를 **Complete**로 설정합니다. 이 완전한 모드를 사용할 때는 템플릿에 없는 리소스를 실수로 삭제할 수 있으므로 주의해야 합니다.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   다음과 같은 요청 본문을 사용합니다.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
   }
   ```

    요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다를 기록하려면 요청에 **debugSetting** 을 포함합니다.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    공유 액세스 서명(SAS) 토큰을 사용하여 저장소 계정을 설정할 수 있습니다. 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)을 참조하세요.

1. 템플릿 및 매개 변수의 파일에 연결하는 대신, 요청 본문에 포함할 수 있습니다.

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. 템플릿 배포의 상태를 가져옵니다. 자세한 내용은 [템플릿 배포에 대한 정보 가져오기](/rest/api/resources/deployments/get)를 참조하세요.

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능은 라고도 *오류 발생 시 롤백*합니다. 배포가 실패하면 배포 기록에서 이전에 성공한 배포를 자동으로 다시 배포할 수 있습니다. 재배포를 지정하려면 요청 본문에서 `onErrorDeployment` 속성을 사용합니다. 이 기능은 인프라 배포에 대 한 알려진된 좋은 상태로 가져왔습니다이 되돌려야 할 경우에 유용 합니다. 주의 및 제한 사항이 있습니다.

- 재배포는 동일한 매개 변수를 사용 하 여 이전에 실행 했던 대로 실행 됩니다. 매개 변수를 변경할 수 없습니다.
- 이전 배포를 사용 하 여 실행 되는 [전체 모드](./deployment-modes.md#complete-mode)합니다. 이전 배포에 포함 되지 모든 리소스가 삭제 되 고 모든 리소스 구성을 이전 상태로 설정 됩니다. 완벽 하 게 이해 해야 합니다 [배포 모드](./deployment-modes.md)합니다.
- 리소스에만 영향을 줍니다 재배포, 데이터 변경에 영향을 받지 않습니다.
- 이 기능은 리소스 그룹 배포의 경우 구독 수준 배포에만 지원 됩니다. 구독 수준 배포에 대 한 자세한 내용은 참조 하세요. [구독 수준에서 리소스 그룹 및 리소스를 만드는](./deploy-to-subscription.md)합니다.

이 옵션을 사용하려면 배포가 배포 기록에서 식별될 수 있도록 고유한 이름을 지정해야 합니다. 고유한 이름이 없으면 현재 실패한 배포가 기록에서 이전에 성공한 배포를 덮어쓸 수 있습니다. 루트 수준 배포에만 이 옵션을 사용할 수 있습니다. 중첩된 템플릿의 배포는 다시 배포할 수 없습니다.

현재 배포가 실패할 경우 마지막으로 성공적으로 배포를 다시 배포하려면 다음을 사용합니다.

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

현재 배포가 실패할 경우 특정 배포를 다시 배포하려면 다음을 사용합니다.

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

지정된 배포는 분명히 성공했을 것입니다.

## <a name="parameter-file"></a>매개 변수 파일 

배포 중에 매개 변수 값을 전달하는 데 매개 변수 파일을 사용하려면 다음 예와 유사한 형식의 JSON 파일을 만들어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

매개 변수 파일 크기는 64KB보다 클 수 없습니다.

매개 변수에 대해 중요한 값(예: 암호)을 제공해야 할 경우 해당 값을 주요 자격 증명 모음에 추가합니다. 앞의 예제에 표시된 대로 배포하는 동안 주요 자격 증명 모음을 검색합니다. 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- 리소스 그룹에 있지만 템플릿에 정의되지 않은 리소스를 처리하는 방법을 지정하려면 [Azure Resource Manager 배포 모드](deployment-modes.md)를 참조하세요.
- 비동기 REST 작업 처리에 대해 알아보려면 [Azure 비동기 작업 추적](resource-manager-async-operations.md)을 참조하세요.
- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
- 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
- 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.
