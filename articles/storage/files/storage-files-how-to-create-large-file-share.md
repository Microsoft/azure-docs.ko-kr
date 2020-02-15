---
title: 대량 파일 공유 사용 및 만들기-Azure Files
description: 이 문서에서는 많은 파일 공유를 사용 하도록 설정 하 고 만드는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d94237d2cfeb814b2e15d43c9f8863a76c0bcd11
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190667"
---
# <a name="enable-and-create-large-file-shares"></a>대량 파일 공유 사용 및 만들기

저장소 계정에서 대용량 파일 공유를 사용 하도록 설정 하면 파일 공유가 최대 100 TiB 확장 될 수 있습니다. 기존 파일 공유에 대 한 기존 저장소 계정에 이러한 크기 조정을 사용 하도록 설정할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure CLI를 사용 하려는 경우 [최신 버전을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)합니다.
- Azure PowerShell를 사용 하려는 경우 [최신 버전을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)합니다.

## <a name="restrictions"></a>제한

지금은 LRS (로컬 중복 저장소) 또는 ZRS (영역 중복 저장소)를 사용 하 여 파일 공유를 사용할 수 있습니다. GZRS (지역 중복 저장소), GRS (지역 중복 저장소) 또는 읽기 액세스 지역 중복 저장소 (RA-GRS)를 사용할 수 없습니다.
계정에서 대량 파일 공유를 사용 하도록 설정 하는 작업은 되돌릴 수 없습니다. 사용 하도록 설정한 후에는 계정을 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다.

## <a name="create-a-new-storage-account"></a>새 스토리지 계정 만들기

### <a name="portal"></a>포털

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **모든 서비스**를 선택합니다. 
1. 리소스 목록에서 **저장소 계정**을 입력 합니다. 입력하는 대로 입력 내용에 따라 목록이 필터링됩니다. **Storage 계정**을 선택합니다.
1. 표시 되는 **저장소 계정** 창에서 **추가**를 선택 합니다.
1. 저장소 계정을 만드는 데 사용할 구독을 선택 합니다.
1. **리소스 그룹** 필드 아래에서 **새로 만들기**를 선택합니다. 새 리소스 그룹의 이름을 입력 합니다.

    ![포털에서 리소스 그룹을 만드는 방법을 보여주는 스크린샷](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 이름은 Azure에서 고유해야 합니다. 이름은 3 자에서 24 자 사이 여야 하며 숫자와 소문자만 사용할 수 있습니다.
1. 저장소 계정에 대 한 위치를 선택 하 고, [용량이 대량 파일 공유에 대해 지원 되는 지역 중 하나](storage-files-planning.md#regional-availability)인지 확인 합니다.
1. 복제를 **로컬 중복 저장소** 또는 **영역 중복 저장소**로 설정 합니다.
1. 다음 필드를 기본값으로 둡니다.

   |필드  |값  |
   |---------|---------|
   |배포 모델     |리소스 관리자         |
   |성능     |Standard         |
   |계정 종류     |StorageV2(범용 v2)         |
   |액세스 계층     |핫         |

1. **고급**을 선택한 다음, **Large file 공유**의 오른쪽에 있는 **사용** 옵션 단추를 선택 합니다.
1. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.

    ![Azure Portal의 새 저장소 계정에서 "사용" 옵션 단추가 있는 스크린샷](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **만들기**를 선택합니다.

### <a name="cli"></a>CLI

먼저, 대량 파일 공유를 사용 하도록 설정할 수 있도록 [최신 버전의 Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 합니다.

대량 파일 공유를 사용 하는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`및 `<yourDesiredRegion>`을 사용자의 정보로 바꿉니다.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

먼저, [최신 버전의 PowerShell을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) 하 여 대량 파일 공유를 사용 하도록 설정 합니다.

대량 파일 공유를 사용 하는 저장소 계정을 만들려면 다음 명령을 사용 합니다. `<yourStorageAccountName>`, `<yourResourceGroup>`및 `<yourDesiredRegion>`을 사용자의 정보로 바꿉니다.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>기존 계정에서 대량 파일 공유 사용

기존 계정에서 대량 파일 공유를 사용 하도록 설정할 수도 있습니다. 대량 파일 공유를 사용 하도록 설정 하는 경우 GZRS, GRS 또는 RA-GRS로 변환할 수 없습니다. 이 저장소 계정에서 대량 파일 공유를 사용 하도록 설정 하는 것은 취소할 수 없습니다.

### <a name="portal"></a>포털

1. [Azure Portal](https://portal.azure.com)를 열고 대량 파일 공유를 사용 하도록 설정할 저장소 계정으로 이동 합니다.
1. 저장소 계정을 열고 **구성**을 선택 합니다.
1. **Large file 공유**에서 **사용** 을 선택한 다음 **저장**을 선택 합니다.
1. **개요** 를 선택 하 고 **새로 고침**을 선택 합니다.

![Azure Portal에서 기존 저장소 계정에 대해 사용 옵션 단추를 선택 합니다.](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

이제 저장소 계정에서 대량 파일 공유를 사용 하도록 설정 했습니다. 다음으로, 용량 및 규모 증가를 활용 하기 위해 기존 공유의 할당량을 업데이트 해야 합니다.

"계정에 대 한 대량 파일 공유를 아직 사용할 수 없습니다." 라는 오류 메시지가 표시 되 면 해당 지역은 롤아웃 완료 중 일 수 있습니다. 대량 파일 공유에 대 한 긴급 한 요구 사항이 있는 경우 지원 담당자에 게 문의 하세요.

### <a name="cli"></a>CLI

기존 계정에서 대량 파일 공유를 사용 하도록 설정 하려면 다음 명령을 사용 합니다. `<yourStorageAccountName>` 및 `<yourResourceGroup>`을 사용자의 정보로 바꿉니다.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

기존 계정에서 대량 파일 공유를 사용 하도록 설정 하려면 다음 명령을 사용 합니다. `<yourStorageAccountName>` 및 `<yourResourceGroup>`을 사용자의 정보로 바꿉니다.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>대량 파일 공유 만들기

저장소 계정에서 큰 파일 공유를 사용 하도록 설정한 후 할당량을 높게 설정 하 여 해당 계정에서 파일 공유를 만들 수 있습니다. 

### <a name="portal"></a>포털

대량 파일 공유를 만드는 것은 표준 파일 공유 만들기와 거의 동일 합니다. 주요 차이점은 최대 100 TiB 할당량을 설정할 수 있다는 것입니다.

1. 저장소 계정에서 **파일 공유**를 선택 합니다.
1. **+ 파일 공유**를 선택합니다.
1. 파일 공유의 이름을 입력 합니다. 원하는 할당량 크기를 최대 100 TiB 설정할 수도 있습니다. 그런 다음 **만들기**를 선택합니다. 

![이름 및 할당량 상자를 표시 하는 Azure Portal UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

대량 파일 공유를 만들려면 다음 명령을 사용 합니다. `<yourStorageAccountName>`, `<yourStorageAccountKey>`및 `<yourFileShareName>`을 사용자의 정보로 바꿉니다.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

대량 파일 공유를 만들려면 다음 명령을 사용 합니다. `<YourStorageAccountName>`, `<YourStorageAccountKey>`및 `<YourStorageAccountFileShareName>`을 사용자의 정보로 바꿉니다.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>기존 파일 공유 확장

저장소 계정에서 큰 파일 공유를 사용 하도록 설정한 후에는 해당 계정의 기존 파일 공유를 더 높은 할당량으로 확장할 수도 있습니다. 

### <a name="portal"></a>포털

1. 저장소 계정에서 **파일 공유**를 선택 합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭 한 다음 **할당량**을 선택 합니다.
1. 원하는 새 크기를 입력 하 고 **확인**을 선택 합니다.

![기존 파일 공유의 할당량이 있는 Azure Portal UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

할당량을 최대 크기로 설정 하려면 다음 명령을 사용 합니다. `<yourStorageAccountName>`, `<yourStorageAccountKey>`및 `<yourFileShareName>`을 사용자의 정보로 바꿉니다.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

할당량을 최대 크기로 설정 하려면 다음 명령을 사용 합니다. `<YourStorageAccountName>`, `<YourStorageAccountKey>`및 `<YourStorageAccountFileShareName>`을 사용자의 정보로 바꿉니다.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>다음 단계

* [Windows에서 파일 공유 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에서 파일 공유 연결 및 탑재](storage-how-to-use-files-linux.md)
* [MacOS에서 파일 공유 연결 및 탑재](storage-how-to-use-files-mac.md)
