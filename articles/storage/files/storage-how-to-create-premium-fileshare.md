---
title: 파일 Azure premium storage 계정 만들기
description: 이 문서에서는 프리미엄 Azure 파일 저장소 계정 및 premium 파일 공유를 만드는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844549"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>프리미엄 Azure 파일 공유를 만드는 방법

FileStorage (미리 보기) 저장소 계정 유형을 premium 성능 특성을 사용 하 여 파일 공유를 만들 수 있도록 Azure Files에 대 한 새 계층을 나타냅니다. 이러한 파일 공유 고성능 및 엔터프라이즈 규모 응용 프로그램을 일관 된 짧은 대기 시간, 높은 IOPS 및 처리량이 높은 공유를 제공 하도록 디자인 되었습니다.

이 문서에서는이 새 계정 유형을 사용 하 여 만드는 방법을 보여 줍니다. [Azure portal](https://portal.azure.com/), Azure PowerShell 및 Azure CLI.

## <a name="prerequisites"></a>필수 조건

Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Azure portal을 사용 하 여 premium 파일 공유 만들기

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="create-a-filestorage-preview-storage-account"></a>FileStorage (미리 보기) 저장소 계정 만들기

이제 저장소 계정을 만들 준비가 되었습니다.

모든 저장소 계정은 Azure 리소스 그룹에 속해야 합니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 저장소 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다. 이 아티클에서 새 리소스 그룹을 만드는 방법에 설명 합니다.

1. Azure portal에서 선택 **저장소 계정** 왼쪽된 메뉴에서.

    ![저장소 계정을 선택 하는 azure 포털 기본 페이지](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
1. 저장소 계정을 만들 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 다음 이미지에 표시된 대로 새 리소스 그룹의 이름을 입력합니다.

1. 그런 다음, 저장소 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 저장소 계정의 위치를 선택하거나 기본 위치를 사용합니다.
1. 에 대 한 **성능** 선택 **Premium**합니다.
1. 선택 **계정 종류** 선택한 **FileStorage (미리 보기)** 합니다.
1. 둡니다 **복제** 의 기본값으로 설정 **로컬 중복 저장소 (LRS)** 합니다.

    ![프리미엄 파일 저장소 계정을 만드는 방법](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. **검토 + 만들기**를 선택하여 저장소 계정 설정을 검토하고 계정을 만듭니다.
1. **만들기**를 선택합니다.

저장소 계정 리소스를 만든 후에 이동 합니다.

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

1. 저장소 계정의 왼쪽된 메뉴에서 스크롤하여 합니다 **파일 서비스** 섹션을 선택한 다음 선택 **파일 (미리 보기)** 합니다.
1. 선택 **+ 파일 공유** premium 파일 공유를 만듭니다.
1. 이름과 원하는 할당량을 파일 공유를 입력 하 고 선택 **만들기**합니다.

> [!NOTE]
> 프로 비전 된 공유 크기는 지정 된 공유 할당량에 의해 파일 공유 프로 비전된 된 크기에는 요금이 청구 됩니다, 참조를 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/) 대 한 자세한 내용은 합니다.

   ![프리미엄 파일 공유 만들기](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스를 정리 하기를 원하는 경우 리소스 그룹을 간단히 삭제할 수 있습니다. 리소스 그룹과 다른 리소스와 연결된 된 저장소 계정 삭제도 리소스 그룹을 삭제 합니다.

## <a name="create-a-premium-file-share-using-powershell"></a>PowerShell을 사용 하 여 premium 파일 공유 만들기

### <a name="create-an-account-using-powershell"></a>PowerShell을 사용하여 계정 만들기

먼저 [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) 모듈의 최신 버전을 설치합니다.

그런 다음, powershell 모듈을 업그레이드하고, Azure 구독에 로그인하고, 리소스 그룹을 만든 다음, 스토리지 계정을 만듭니다.

### <a name="upgrade-your-powershell-module"></a>PowerShell 모듈 업그레이드

와 PowerShell 사용 하 여 premium 파일 상호 작용 하 여 최신 Az.Storage 모듈을 설치 해야 합니다.

관리자 권한으로 PowerShell 세션을 열어 시작합니다.

Az.Storage 모듈을 설치 합니다.

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure 구독에 로그인합니다.

`Login-AzAccount` 명령을 사용하고 화면의 지시에 따라 인증합니다.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

PowerShell에서 새 리소스 그룹을 만들려면 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용합니다.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>Filestorage (미리 보기) 저장소 계정 만들기

PowerShell에서 FileStorage (미리 보기) 저장소 계정을 만들려면 사용 합니다 [새 AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 명령:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

이제 FileStorage 계정을 만들었으므로 premium 파일 공유를 만들 수 있습니다. 사용 하 여는 [새로 만들기-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet을 하나 만듭니다.

> [!NOTE]
> 프로 비전 된 공유 크기는 지정 된 공유 할당량에 의해 파일 공유 프로 비전된 된 크기에는 요금이 청구 됩니다, 참조를 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/) 대 한 자세한 내용은 합니다.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>리소스 정리

새 스토리지 계정을 포함하여 리소스 그룹 및 관련 리소스를 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용합니다. 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Azure CLI를 사용 하 여 premium 파일 공유 만들기

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 로그인 명령을 실행합니다.

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Azure premium 파일에 대 한 CLI 확장 추가

CLI를 사용 하 여 premium 파일 상호 작용을 하 여 shell에 확장을 추가 해야 합니다.

이렇게 하려면 Cloud Shell이나 로컬 셸을 사용하여 다음 명령을 입력합니다. `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure CLI로 새 리소스 그룹을 만들려면 [az group create](/cli/azure/group) 명령을 사용합니다.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>FileStorage (미리 보기) 저장소 계정 만들기

FileStorage (미리 보기) 저장소 계정을 만들려면 Azure CLI에서 사용 합니다 [az storage 계정 만들기](/cli/azure/storage/account) 명령입니다.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>저장소 계정 키 가져오기

이 문서의 저장소 계정에서 리소스에 대 한 액세스를 제어 하는 저장소 계정 키, 키 premium 파일 공유를 만들려면 사용 합니다. 키는 저장소 계정을 만들 때 자동으로 만들어집니다. [az storage account keys list](/cli/azure/storage/account/keys) 명령을 사용하여 스토리지 계정에 대한 스토리지 계정 키를 가져올 수 있습니다.

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>프리미엄 파일 공유 만들기

이제 FileStorage 계정을 만들었으므로 premium 파일 공유를 만들 수 있습니다. 사용 하 여 합니다 [az storage 공유 만들기](/cli/azure/storage/share) 명령을 만들어야 합니다.

> [!NOTE]
> 프로 비전 된 공유 크기는 지정 된 공유 할당량에 의해 파일 공유 프로 비전된 된 크기에는 요금이 청구 됩니다, 참조를 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/) 대 한 자세한 내용은 합니다.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>리소스 정리

새 저장소 계정을 포함하여 리소스 그룹과 관련 리소스를 제거하려면 [az group delete](/cli/azure/group) 명령을 사용합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 프리미엄 파일 저장소 계정을 만들었습니다. 이 계정에서 제공 하는 성능에 대 한 자세한 내용은 계획 가이드의 성능 계층 섹션을 계속 합니다.

> [!div class="nextstepaction"]
> [파일 공유 성능 계층](storage-files-planning.md#file-share-performance-tiers)