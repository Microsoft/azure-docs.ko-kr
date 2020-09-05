---
title: Azure에 VHD 업로드 또는 지역 간에 디스크 복사-Azure PowerShell
description: Azure 관리 디스크에 VHD를 업로드 하 고 직접 업로드를 통해 Azure PowerShell를 사용 하 여 여러 지역에 관리 되는 디스크를 복사 하는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: ec765e2eb635449140d60ee98762e35aea12fe44
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689612"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Azure에 VHD를 업로드 하거나 관리 디스크를 다른 지역에 복사-Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [AzCopy v10의 최신 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드 합니다.
- [Azure PowerShell 모듈을 설치](/powershell/azure/install-Az-ps)합니다.
- 온-프레미스에서 VHD를 업로드 하려는 경우: [Azure에 대해 준비](prepare-for-upload-vhd-image.md)된 고정 크기 VHD는 로컬에 저장 됩니다.
- 또는 복사 작업을 수행 하려는 경우 Azure에서 관리 되는 디스크입니다.

## <a name="getting-started"></a>시작

GUI를 통해 디스크를 업로드 하는 것을 선호 하는 경우 Azure Storage 탐색기를 사용 하 여이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Storage 탐색기를 사용 하 여 Azure managed disks 관리](../disks-use-storage-explorer-managed-disks.md) 를 참조 하세요.

Azure에 VHD를 업로드 하려면이 업로드 프로세스에 대해 구성 된 빈 관리 디스크를 만들어야 합니다. 계정을 만들기 전에 이러한 디스크에 대해 알아야 하는 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadToUpload는 디스크가 업로드를 받을 준비가 되었지만 SAS ( [보안 액세스 서명](../../storage/common/storage-sas-overview.md) )가 생성 되지 않았음을 의미 합니다.
- ActiveUpload-디스크가 업로드를 받을 준비가 되었으며 SAS가 생성 되었음을 의미 합니다.

> [!NOTE]
> 이러한 상태 중 하나에서 관리 디스크는 실제 디스크 유형에 상관 없이 [표준 HDD 가격](https://azure.microsoft.com/pricing/details/managed-disks/)으로 청구 됩니다. 예를 들어 P10는 S10로 청구 됩니다. 이는 `revoke-access` 디스크를 VM에 연결 하는 데 필요한 관리 디스크에서가 호출 될 때까지 적용 됩니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

업로드할 빈 표준 HDD를 만들려면 먼저 업로드할 VHD의 파일 크기 (바이트)가 필요 합니다. 예제 코드는 사용자를 위한 것 이지만를 직접 수행 하려면를 사용 하면 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` 됩니다. 이 값은 **-UploadSizeInBytes** 매개 변수를 지정할 때 사용 됩니다.

이제 로컬 셸에서 **-createoption** 매개 변수의 **업로드** 설정과 [AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet의 **-UploadSizeInBytes** 매개 변수를 지정 하 여 업로드할 빈 표준 HDD를 만듭니다. 그런 다음 [AzDisk](/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) 를 호출 하 여 디스크를 만듭니다.

,을 (를) 바꾼 `<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` 후 다음 명령을 실행 합니다.

> [!TIP]
> OS 디스크를 만드는 경우에는-HyperVGeneration ' '를 추가 <yourGeneration> `New-AzDiskConfig` 합니다.

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

프리미엄 SSD 또는 표준 SSD를 업로드 하려면 **Standard_LRS** **Premium_LRS** 또는 **StandardSSD_LRS**으로 바꿉니다. Ultra 디스크는 아직 지원 되지 않습니다.

업로드 프로세스를 위해 구성 된 빈 관리 디스크를 만들었으므로 이제 VHD를 업로드할 수 있습니다. 디스크에 VHD를 업로드 하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 필요 합니다.

빈 관리 디스크의 쓰기 가능한 SAS를 생성 하려면 `<yourdiskname>` 및를 바꾼 `<yourresourcegroupname>` 후 다음 명령을 사용 합니다.

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>VHD 업로드

이제 빈 관리 디스크에 대 한 SAS가 있으므로이를 사용 하 여 업로드 명령의 대상으로 관리 디스크를 설정할 수 있습니다.

AzCopy v10를 사용 하 여 생성 한 SAS URI를 지정 하 여 로컬 VHD 파일을 관리 되는 디스크에 업로드 합니다.

이 업로드는 동일한 [표준 HDD](disks-types.md#standard-hdd)와 동일한 처리량을 갖습니다. 예를 들어 S4와 동일한 크기의 경우 최대 60 s p s/s의 처리량이 있습니다. 그러나 S70에 해당 하는 크기의 경우 최대 500 m b/s의 처리량이 있습니다.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

업로드가 완료 되 고 더 이상 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 해지 합니다. SAS를 해지 하면 관리 디스크의 상태가 변경 되 고 해당 디스크를 VM에 연결할 수 있습니다.

`<yourdiskname>`및 `<yourresourcegroupname>` 를 바꾼 후 다음 명령을 실행 합니다.

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

또한 직접 업로드는 관리 디스크를 복사 하는 프로세스를 간소화 합니다. 동일한 지역 내에 복사 하거나 관리 디스크를 다른 지역에 복사할 수 있습니다.

다음 스크립트는이 작업을 수행 합니다 .이 프로세스는 기존 디스크로 작업 하므로 앞에서 설명한 단계와 비슷하지만 몇 가지 차이점이 있습니다.

> [!IMPORTANT]
> Azure에서 관리 디스크의 디스크 크기 (바이트)를 제공 하는 경우 512의 오프셋을 추가 해야 합니다. 이는 Azure에서 디스크 크기를 반환할 때 바닥글이 생략 되기 때문입니다. 이렇게 하지 않으면 복사가 실패 합니다. 다음 스크립트는 이미이를 위해이를 수행 합니다.

,, `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` , `<targetResourceGroupHere>` `<yourOSTypeHere>` 및 `<yourTargetLocationHere>` (위치 값의 예: uswest2)를 값으로 바꾼 후 관리 디스크를 복사 하기 위해 다음 스크립트를 실행 합니다.

> [!TIP]
> OS 디스크를 만드는 경우에는-HyperVGeneration ' '를 추가 <yourGeneration> `New-AzDiskConfig` 합니다.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 VHD를 성공적으로 업로드 했으므로 VM에 디스크를 연결 하 고 사용을 시작할 수 있습니다.

VM에 데이터 디스크를 연결 하는 방법에 대 한 자세한 내용은 [Azure PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](attach-disk-ps.md)주제에서 문서를 참조 하세요. 디스크를 OS 디스크로 사용 하려면 [특수 디스크에서 WINDOWS VM 만들기](create-vm-specialized.md#create-the-new-vm)를 참조 하세요.
