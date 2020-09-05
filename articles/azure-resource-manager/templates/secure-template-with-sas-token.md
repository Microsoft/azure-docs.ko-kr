---
title: SAS 토큰을 사용 하 여 템플릿을 안전 하 게 배포
description: SAS 토큰으로 보호 되는 Azure Resource Manager 템플릿을 사용 하 여 Azure에 리소스를 배포 합니다. Azure PowerShell 및 Azure CLI를 표시 합니다.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855668"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>SAS 토큰을 사용 하 여 개인 ARM 템플릿 배포

Azure Resource Manager 템플릿 (ARM 템플릿)이 저장소 계정에 있는 경우 공개적으로 노출 되지 않도록 템플릿에 대 한 액세스를 제한할 수 있습니다. 템플릿에 대 한 SAS (공유 액세스 서명) 토큰을 만들고 배포 하는 동안 해당 토큰을 제공 하 여 보안 템플릿에 액세스 합니다. 이 문서에서는 Azure PowerShell 또는 Azure CLI를 사용 하 여 SAS 토큰을 사용 하 여 템플릿을 배포 하는 방법을 설명 합니다.

> [!IMPORTANT]
> SAS 토큰을 사용 하 여 템플릿을 보호 하는 대신 [템플릿 사양을](template-specs.md)사용 하는 것이 좋습니다. 템플릿 사양을 사용 하 여 조직의 다른 사용자와 템플릿을 공유 하 고 Azure RBAC를 통해 템플릿에 대 한 액세스를 관리할 수 있습니다.

## <a name="create-storage-account-with-secured-container"></a>보안 컨테이너를 사용 하 여 저장소 계정 만들기

다음 스크립트는 공용 액세스를 해제 한 상태에서 저장소 계정 및 컨테이너를 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>저장소 계정에 템플릿 업로드

이제 저장소 계정에 템플릿을 업로드할 준비가 되었습니다. 사용 하려는 템플릿에 대 한 경로를 제공 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>배포하는 동안 SAS 토큰 제공

스토리지 계정에 프라이빗 템플릿을 배포하려면 SAS 토큰을 생성하고 해당 템플릿의 URI에 포함합니다. 배포를 완료할 만큼 충분한 여유를 두고 만료 기간을 설정합니다.

> [!IMPORTANT]
> 템플릿이 포함된 Blob은 계정 소유자만 액세스할 수 있습니다. 그러나 blob용 SAS 토큰을 생성하면 해당 blob은 해당 URI를 가진 사람이면 누구나 액세스할 수 있습니다. 다른 사용자가 URI를 가로채는 경우, 그 사용자가 템플릿에 액세스할 수 있습니다. SAS 토큰은 템플릿에 대한 액세스를 제한하는 좋은 방법이지만, 암호와 같은 중요한 데이터를 템플릿에 직접 포함하지 않아야 합니다.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 예에서는 Cloud Shell의 Bash 환경에서 작동 합니다. 다른 환경에서는 SAS 토큰에 대 한 만료 시간을 만들기 위해 다른 구문이 필요할 수 있습니다.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

연결된 템플릿에 SAS 토큰을 사용하는 예제는 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계
* 템플릿 배포에 대 한 소개는 [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure PowerShell](deploy-powershell.md)을 참조 하세요.
* 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](template-syntax.md#parameters)을 참조하세요.
