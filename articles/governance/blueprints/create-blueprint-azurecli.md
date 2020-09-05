---
title: '빠른 시작: Azure CLI를 사용하여 청사진 만들기'
description: 이 빠른 시작에서는 Azure Blueprints를 사용하여 Azure CLI를 통해 아티팩트를 만들고 정의하고 배포합니다.
ms.date: 06/02/2020
ms.topic: quickstart
ms.openlocfilehash: c64723befbe3cb8d811179e7062df18606749783
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051545"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>빠른 시작: Azure CLI로 Azure Blueprint 정의 및 할당

청사진을 만들고 할당하는 방법을 학습하면 공통 패턴 정의를 통해 ARM 템플릿(Azure Resource Manager 템플릿), 정책, 보안 등을 기반으로 재사용이 가능하고 신속하게 배포할 수 있는 구성을 개발할 수 있습니다. 이 자습서에서는 Azure Blueprint를 사용하여 조직 내에서 청사진을 작성, 게시 및 할당하는 것과 관련된 다음과 같은 일반적인 작업을 수행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>청사진 확장 추가

Azure CLI를 사용하여 청사진 정의 및 할당을 관리하려면 확장을 추가해야 합니다.
이 확장은 [Windows 10의 bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com)(독립형 및 포털 내부형), [Azure CLI Docker 이미지](https://hub.docker.com/r/microsoft/azure-cli/)를 포함하여 Azure CLI가 사용되거나 로컬로 설치될 수 있는 모든 위치에서 작동됩니다.

1. 최신 Azure CLI(최소 **2.0.76**)가 설치되어 있는지 확인하세요. 아직 설치되지 않은 경우 [다음 지침](/cli/azure/install-azure-cli-windows)을 따릅니다.

1. 선택한 Azure CLI 환경에서 다음 명령을 사용하여 가져옵니다.

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. 확장이 설치되었고 필요한 버전인지(최소 **0.1.0**) 유효성을 검사합니다:

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>청사진 만들기

규정 준수를 위한 표준 패턴을 정의하는 첫 단계는 사용 가능한 리소스로 청사진을 작성하는 것입니다. 'MyBlueprint'라는 청사진을 만들어 구독의 역할 및 정책 할당을 구성하겠습니다. 그런 다음, 리소스 그룹, ARM 템플릿 및 리소스 그룹에 대한 역할 할당을 추가합니다.

> [!NOTE]
> Azure CLI을 사용하는 경우 _청사진_ 개체가 먼저 생성됩니다. 매개 변수가 있는 추가할 _아티팩트_ 각각에 대해, 초기 _청사진_에 매개 변수를 미리 정의해야 합니다.

1. 초기 _청사진_ 개체를 만듭니다. **매개 변수** 매개 변수는 모든 청사진 수준 매개 변수를 포함하는 JSON 파일을 사용합니다. 매개 변수는 할당 중에 설정되고 이후 단계에서 추가되는 아티팩트에서 사용합니다.

   - JSON 파일- blueprintparms.json

     ```json
     {
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
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Azure CLI 명령

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > 청사진 정의를 가져올 때 _blueprint.json_이라는 파일 이름을 사용합니다.
     > 이 파일 이름은 [az 청사진 가져오기](/cli/azure/ext/blueprint/blueprint#ext-blueprint-az-blueprint-import)를 호출할 때 사용됩니다.

     청사진 개체는 기본적으로 기본 구독에 생성됩니다. 관리 그룹을 지정하려면 매개 변수 **managementgroup**를 사용합니다. 구독을 지정하려면 매개 변수 **subscription**을 사용합니다.

1. 스토리지 아티팩트에 대한 리소스 그룹을 정의에 추가합니다.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. 구독 시 역할 할당을 추가합니다. 아래 예에서 지정된 역할에 부여된 주체 ID는 청사진 할당 중에 설정되는 매개 변수로 구성됩니다. 이 예제에서는 `b24988ac-6180-42a0-ab88-20f7382dd24c`의 GUID와 함께 _기여자_ 기본 제공 역할을 사용합니다.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. 구독 시 정책 할당을 추가합니다. 이 예제에서는 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`의 GUID와 함께 _리소스 그룹에 태그 및 기본값 적용_ 기본 제공 정책을 사용합니다.

   - JSON 파일 - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI 명령

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. 구독 시 Storage 태그(_storageAccountType_ 매개 변수 재사용)에 대해 또 다른 정책 할당을 추가합니다. 이렇게 추가된 정책 할당 아티팩트는 청사진에 정의된 매개 변수가 둘 이상의 아티팩트에서 사용될 수 있다는 것을 보여줍니다. 이 예에서 **storageAccountType**은 리소스 그룹에 태그를 설정하는 데 사용됩니다. 이 값은 다음 단계에서 생성되는 스토리지 계정에 대한 정보를 제공합니다. 이 예제에서는 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`의 GUID와 함께 _리소스 그룹에 태그 및 기본값 적용_ 기본 제공 정책을 사용합니다.

   - JSON 파일 - artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI 명령

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. 리소스 그룹에서 템플릿을 추가합니다. ARM 템플릿의 **템플릿** 매개 변수에는 템플릿의 일반 JSON 구성 요소가 포함됩니다. 이 템플릿은 또한 **storageAccountType**, **tagName** 및 **tagValue** 청사진 매개 변수를 각각 템플릿에 전달하는 방식으로 재사용합니다. 청사진 매개 변수는 매개 변수 **매개 변수**를 사용하여 템플릿에서 사용할 수 있으며, 템플릿 JSON 내에 포함된 키-값 쌍은 값을 삽입하는 데 사용됩니다. 청사진 및 템플릿 매개 변수 이름은 같을 수 있습니다.

   - JSON ARM 템플릿 파일 - artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON ARM 템플릿 매개 변수 파일 - artifacts\templateStorageParams.json

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI 명령

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. 리소스 그룹에서 역할 할당을 추가합니다. 이전 역할 할당과 유사하게, 아래 예제에서는 **소유자** 역할에 대한 정의 식별자를 사용하고 청사진과 다른 매개 변수를 제공합니다. 이 예제에서는 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`의 GUID와 함께 _소유자_ 기본 제공 역할을 사용합니다.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>청사진 게시

아티팩트가 청사진에 추가되었으면 이제 게시할 차례입니다. 게시한 후에는 구독에 할당할 수 있습니다.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

`{BlueprintVersion}`의 값은 최대 길이가 20자인 문자, 숫자 및 하이픈(공백이나 다른 특수 문자 제외)으로 이루어진 문자열입니다. **v20200605-135541**처럼 고유하고 정보가 될만한 값을 사용합니다.

## <a name="assign-a-blueprint"></a>청사진 할당

Azure CLI를 사용하여 청사진을 게시하고 나면 구독에 할당할 수 있습니다. 작성한 청사진을 관리 그룹 계층 구조에 속하는 구독 중 하나에 할당합니다. 구독에 청사진이 저장되면 해당 구독에만 할당될 수 있습니다. **Blueprint-이름** 매개 변수는 할당할 청사진을 지정합니다. 이름, 위치, ID, 잠금 및 청사진 매개 변수를 제공하려면 `az blueprint assignment create` 명령에서 일치하는 Azure CLI 매개 변수를 사용하거나 **매개 변수** JSON 파일에 제공합니다.

1. 청사진 배포를 구독에 할당하여 실행합니다. **contributor** 및 **소유자** 매개 변수에는 역할 할당에 부여될 주체의 objectId 배열이 필요하기 때문에, [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md)를 사용하여 사용자, 그룹 또는 서비스 주체에 대한 **매개 변수**에 사용될 objectId를 수집합니다.

   - JSON 파일 - blueprintAssignment.json

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Azure CLI 명령

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - 사용자 할당 관리 ID

     청사진 할당은 [사용자가 할당한 관리형 ID](../../active-directory/managed-identities-azure-resources/overview.md)로 사용할 수 있습니다.
     이 경우 **ID 형식** 매개 변수는 _UserAssigned_로 설정되고 **사용자 할당 ID** 매개 변수는 ID를 지정합니다. `{userIdentity}`을 사용자 할당 관리 ID의 이름으로 바꿉니다.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **사용자가 할당한 관리형 ID**는 청사진을 할당한 사용자에게 권한이 있는 구독 및 리소스 그룹일 수 있습니다.

     > [!IMPORTANT]
     > Azure Blueprints는 사용자 할당 관리 ID를 관리하지 않습니다. 충분한 역할과 권한을 할당할 책임이 사용자에게 있으며, 그렇지 못하면 청사진 할당이 실패합니다.

## <a name="clean-up-resources"></a>리소스 정리

### <a name="unassign-a-blueprint"></a>청사진 할당 취소

구독에서 청사진을 제거할 수 있습니다. 아티팩트 리소스가 더 이상 필요 없는 경우에 청사진을 제거하는 경우가 많습니다. 청사진을 제거해도 해당 청사진의 일부분으로 할당된 아티팩트는 남아 있습니다. 청사진 할당을 제거하려면 `az blueprint assignment delete`명령을 사용합니다:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI을 사용하여 청사진을 생성, 할당 및 제거했습니다. Azure Blueprints에 대해 자세히 알아보려면 청사진 수명 주기 문서를 참조하세요.

> [!div class="nextstepaction"]
> [청사진 수명 주기에 대해 알아보기](./concepts/lifecycle.md)