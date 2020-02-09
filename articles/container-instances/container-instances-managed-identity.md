---
title: 컨테이너 그룹에서 관리 되는 id 사용
description: 다른 Azure 서비스를 사용 하 여 인증할 수 있는 Azure Container Instances에서 관리 되는 id를 사용 하도록 설정 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901937"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Azure Container Instances에서 관리 ID를 사용하는 방법

[Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 비밀 또는 자격 증명을 코드에 유지하지 않고, 다른 Azure 서비스와 상호 작용하는 Azure Container Instances에서 코드를 실행합니다. 이 기능은 Azure Active Directory에서 자동으로 관리되는 ID를 Azure Container Instances 배포에 제공합니다.

이 문서에서는 Azure Container Instances의 관리 ID에 대해 알아보고 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 컨테이너 그룹에서 사용자 할당 또는 시스템 할당 ID를 사용하도록 설정
> * Azure key vault에 대한 id 액세스 권한 부여
> * 관리 id를 사용 하 여 실행 중인 컨테이너에서 키 자격 증명 모음에 액세스

다른 Azure 서비스에 액세스하려면 예제를 조정하여 Azure Container Instances에서 ID를 사용하도록 설정하고 사용합니다. 이러한 예제는 대화형입니다. 그러나 실제로 컨테이너 이미지는 Azure 서비스에 액세스하는 코드를 실행합니다.

> [!NOTE]
> 현재 가상 네트워크에 배포된 컨테이너 그룹의 관리 ID를 사용할 수 없습니다.

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

실행 중인 컨테이너에서 관리 ID를 사용하면 컨테이너 코드에서 자격 증명을 관리하지 않고, [Azure AD 인증을 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에서 인증을 받을 수 있습니다. AD 인증을 지원 하지 않는 서비스의 경우 Azure key vault에 암호를 저장 하 고 관리 되는 id를 사용 하 여 자격 증명을 검색 하는 키 자격 증명 모음에 액세스할 수 있습니다. 관리 ID 사용에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다. 현재 Azure Container Instances에서 관리 되는 id는 Linux 컨테이너 에서만 지원 되며 아직 Windows 컨테이너에는 지원 되지 않습니다.
>  

### <a name="enable-a-managed-identity"></a>관리 ID 사용

 Azure Container Instances에서 Azure 리소스의 관리 ID는 REST API 버전 2018-10-01, 해당 SDK 및 도구에서 지원됩니다. 컨테이너 그룹을 만들 때 [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) 속성을 설정하여 하나 이상의 관리 ID를 사용하도록 설정합니다. 컨테이너 그룹을 실행 한 후에 관리 되는 id를 사용 하도록 설정 하거나 업데이트할 수도 있습니다. 이렇게 하면 컨테이너 그룹이 다시 시작 됩니다. 새 또는 기존 컨테이너 그룹에 대해 ID를 설정하려면 Azure CLI, Resource Manager 템플릿 또는 YAML 파일을 사용합니다. 

Azure Container Instances는 두 가지 유형의 Azure 관리 ID인 사용자 할당 및 시스템 할당 ID를 지원합니다. 컨테이너 그룹에서 시스템 할당 ID 또는 하나 이상의 사용자 할당 ID를 사용하도록 설정하거나 두 가지 유형의 ID를 모두 사용할 수 있습니다. 

* **사용자 할당** 관리 ID는 사용 중인 구독에서 신뢰할 수 있는 Azure AD 테넌트의 독립 실행형 Azure 리소스로 만들어집니다. 생성된 ID는 하나 이상의 Azure 리소스(Azure Container Instances 또는 다른 Azure 서비스)에 할당할 수 있습니다. 사용자 할당 ID의 수명 주기는 할당된 컨테이너 그룹 또는 기타 서비스 리소스의 수명 주기와는 별도로 관리됩니다. 이 동작은 Azure Container Instances에서 특히 유용합니다. ID가 컨테이너 그룹의 수명을 넘어 연장되므로 다른 표준 설정과 함께 다시 사용하여 컨테이너 그룹 배포의 반복성을 높일 수 있습니다.

* **시스템 할당** 관리 ID는 Azure Container Instances의 컨테이너 그룹에 대해 직접 사용하도록 설정됩니다. 이 ID를 사용하도록 설정하면 Azure는 인스턴스의 구독에서 신뢰하는 Azure AD 테넌트에 그룹의 ID를 만듭니다. ID가 만들어지면 자격 증명이 컨테이너 그룹의 각 컨테이너에 프로비전됩니다. 시스템 할당 ID의 수명 주기는 사용하도록 설정된 컨테이너 그룹과 직접적으로 연관됩니다. 이 그룹이 삭제되면 Azure는 Azure AD에서 자격 증명과 ID를 자동으로 정리합니다.

### <a name="use-a-managed-identity"></a>관리 ID 사용

관리 id를 사용 하려면 id에 초기에 구독에서 하나 이상의 Azure 서비스 리소스 (예: 웹 앱, 주요 자격 증명 모음 또는 저장소 계정)에 대한 액세스 권한이 부여 되어야 합니다. 실행 중인 컨테이너에서 Azure 리소스에 액세스하려면 코드는 Azure AD 엔드포인트에서 *액세스 토큰*을 획득해야 합니다. 그런 후에 코드는 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다. 

실행 중인 컨테이너에서 관리 ID를 사용하는 것은 Azure VM에서 ID를 사용하는 것과 기본적으로 동일합니다. [토큰](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell 또는 Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) 또는 [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)를 사용하기 위한 VM 지침을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.49 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-azure-key-vault"></a>Azure key vault 만들기

이 문서의 예제에서는 Azure Container Instances에서 관리 되는 id를 사용 하 여 Azure key vault 비밀에 액세스 합니다. 

먼저, 다음 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용 하 여 주요 자격 증명 모음을 만듭니다. 고유 키 자격 증명 모음 이름을 지정 해야 합니다. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

[Az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 명령을 사용 하 여 주요 자격 증명 모음에 샘플 암호를 저장 합니다.

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Azure Container Instances에서 사용자 할당 또는 시스템 할당 관리 id를 사용 하 여 키 자격 증명 모음에 액세스 하려면 다음 예제를 계속 진행 합니다.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>예제 1: 사용자 할당 id를 사용 하 여 Azure key vault 액세스

### <a name="create-an-identity"></a>ID 만들기

먼저 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 명령을 사용하여 구독에서 ID를 만듭니다. 키 자격 증명 모음을 만드는 데 사용 되는 것과 동일한 리소스 그룹을 사용 하거나 다른 자격 증명 모음을 사용할 수 있습니다.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

다음 단계에서 ID를 사용하려면 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 명령을 사용하여 ID의 서비스 주체 ID 및 리소스 ID를 변수에 저장합니다.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>컨테이너 그룹에서 사용자 할당 ID를 사용하도록 설정

다음 [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) 명령을 실행 하 여 Microsoft의 `azure-cli` 이미지를 기반으로 컨테이너 인스턴스를 만듭니다. 이 예제에서는 Azure CLI를 실행 하 여 다른 Azure 서비스에 액세스 하는 데 대화형으로 사용할 수 있는 단일 컨테이너 그룹을 제공 합니다. 이 섹션에서는 기본 Ubuntu 운영 체제만 사용 됩니다. 

`--assign-identity` 매개 변수는 그룹에 사용자 할당 관리 ID를 전달합니다. 장기 실행 명령이 컨테이너가 계속 실행되도록 합니다. 이 예제에서는 키 자격 증명 모음을 만드는 데 사용 된 것과 동일한 리소스 그룹을 사용 하지만 다른 자격 증명 모음을 지정할 수 있습니다.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

몇 초 내에 Azure CLI로부터 배포가 완료되었음을 알려주는 응답을 받습니다. [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 명령을 사용하여 상태를 확인합니다.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

출력의 `identity` 섹션은 다음과 유사하며 ID가 컨테이너 그룹에 설정되어 있음을 나타냅니다. `userAssignedIdentities` 아래의 `principalID`는 Azure Active Directory에서 만든 ID의 서비스 주체입니다.

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>사용자 할당 id에 키 자격 증명 모음에 대한 액세스 권한 부여

다음 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) 명령을 실행 하 여 key vault에 대한 액세스 정책을 설정 합니다. 다음 예에서는 사용자 할당 id를 사용 하 여 키 자격 증명 모음에서 암호를 가져올 수 있습니다.

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>사용자 할당 id를 사용 하 여 key vault에서 비밀 가져오기

이제 실행 중인 컨테이너 인스턴스 내에서 관리 되는 id를 사용 하 여 키 자격 증명 모음에 액세스할 수 있습니다. 먼저 컨테이너에서 bash 셸을 시작 합니다.

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

컨테이너의 bash 셸에서 다음 명령을 실행합니다. Azure Active Directory 사용 하 여 key vault에 인증 하는 액세스 토큰을 가져오려면 다음 명령을 실행 합니다.

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

출력:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

후속 명령에서 인증을 받는 데 사용할 액세스 토큰을 변수에 저장하려면 다음 명령을 실행합니다.

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

이제 액세스 토큰을 사용 하 여 key vault에 인증 하 고 비밀을 읽습니다. URL의 Key Vault 이름을 대체해야 합니다( *https://mykeyvault.vault.azure.net/...* ).

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

응답은 다음 예제와 유사하며 비밀을 표시합니다. 코드에서 이 출력을 구문 분석하여 비밀을 가져올 수 있습니다. 그런 다음, 후속 작업에서 이 비밀을 사용하여 다른 Azure 리소스에 액세스합니다.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>예제 2: 시스템 할당 id를 사용 하 여 Azure key vault 액세스

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>컨테이너 그룹에서 시스템 할당 ID 사용

다음 [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) 명령을 실행 하 여 Microsoft의 `azure-cli` 이미지를 기반으로 컨테이너 인스턴스를 만듭니다. 이 예제에서는 Azure CLI를 실행 하 여 다른 Azure 서비스에 액세스 하는 데 대화형으로 사용할 수 있는 단일 컨테이너 그룹을 제공 합니다. 

추가 값 없는 `--assign-identity` 매개 변수를 사용하여 그룹에 대해 시스템 할당 관리 ID를 사용하도록 설정할 수 있습니다. Id의 범위는 컨테이너 그룹의 리소스 그룹입니다. 장기 실행 명령이 컨테이너가 계속 실행되도록 합니다. 이 예제에서는 키 자격 증명 모음을 만드는 데 사용 된 것과 동일한 리소스 그룹을 사용 하지만 다른 자격 증명 모음을 지정할 수 있습니다.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

몇 초 내에 Azure CLI로부터 배포가 완료되었음을 알려주는 응답을 받습니다. [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 명령을 사용하여 상태를 확인합니다.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

출력의 `identity` 섹션은 다음과 유사하며, 시스템 할당 ID가 Azure Active Directory에서 만들어졌음을 나타냅니다.

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

후속 단계에서 사용할 수 있도록 변수를 ID의 `principalId` 값(서비스 주체 ID)으로 설정합니다.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>컨테이너 그룹에 키 자격 증명 모음에 대한 액세스 권한 부여

다음 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) 명령을 실행 하 여 key vault에 대한 액세스 정책을 설정 합니다. 다음 예에서는 시스템 관리 id를 사용 하 여 키 자격 증명 모음에서 암호를 가져올 수 있습니다.

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>컨테이너 그룹 id를 사용 하 여 key vault에서 비밀 가져오기

이제 관리 되는 id를 사용 하 여 실행 중인 컨테이너 인스턴스 내에서 키 자격 증명 모음에 액세스할 수 있습니다. 먼저 컨테이너에서 bash 셸을 시작 합니다.

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

컨테이너의 bash 셸에서 다음 명령을 실행합니다. 먼저 관리 id를 사용 하 여 Azure CLI에 로그인 합니다.

```bash
az login --identity
```

실행 중인 컨테이너에서 키 자격 증명 모음에서 암호를 검색 합니다.

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

암호의 값이 검색 됩니다.

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 관리 ID 사용

[Resource Manager 템플릿](container-instances-multi-container-group.md)을 사용하여 컨테이너 그룹에서 관리 ID를 사용하도록 설정하려면 `ContainerGroupIdentity` 개체를 사용하여 `Microsoft.ContainerInstance/containerGroups` 개체의 `identity` 속성을 설정합니다. 다음 코드 조각은 여러 다른 시나리오에 맞게 구성된 `identity` 속성을 보여 줍니다. [Resource Manager 템플릿 참조](/azure/templates/microsoft.containerinstance/containergroups)를 참조하세요. `2018-10-01`의 최소 `apiVersion` 지정 합니다.

### <a name="user-assigned-identity"></a>사용자 할당 ID

사용자 할당 ID는 다음 형식의 리소스 ID입니다.

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

하나 이상의 사용자 할당 ID를 사용하도록 설정할 수 있습니다.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>시스템 할당 ID

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>시스템 및 사용자 할당 ID

컨테이너 그룹에서 시스템 할당 ID 또는 하나 이상의 사용자 할당 ID를 사용하도록 설정할 수 있습니다.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>YAML 파일을 사용하여 관리 ID 사용

[YAML 파일](container-instances-multi-container-yaml.md)을 사용하여 배포된 컨테이너 그룹에서 관리 ID를 사용하도록 설정하려면 다음 YAML을 포함합니다.
`2018-10-01`의 최소 `apiVersion` 지정 합니다.

### <a name="user-assigned-identity"></a>사용자 할당 ID

사용자 할당 ID는 다음 형식의 리소스 ID입니다. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

하나 이상의 사용자 할당 ID를 사용하도록 설정할 수 있습니다.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>시스템 할당 ID

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>시스템 및 사용자 할당 ID

컨테이너 그룹에서 시스템 할당 ID 또는 하나 이상의 사용자 할당 ID를 사용하도록 설정할 수 있습니다.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Container Instances의 관리 ID와 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 컨테이너 그룹에서 사용자 할당 또는 시스템 할당 ID를 사용하도록 설정
> * Azure key vault에 대한 id 액세스 권한 부여
> * 관리 id를 사용 하 여 실행 중인 컨테이너에서 키 자격 증명 모음에 액세스

* [Azure 리소스에 대한 관리 ID](/azure/active-directory/managed-identities-azure-resources/)에 대해 자세히 알아보세요.

* 관리 id를 사용 하 여 Azure Container Instances에서 키 자격 증명 모음에 액세스 하는 [Azure GO SDK 예](https://medium.com/@samkreter/c98911206328) 를 참조 하세요.
