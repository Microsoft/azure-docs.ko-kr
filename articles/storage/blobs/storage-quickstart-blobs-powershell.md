---
title: 빠른 시작 - PowerShell을 사용하여 Blob 만들기
titleSuffix: Azure Storage
description: 이 빠른 시작에서는 개체(Blob) 스토리지에서 Azure PowerShell을 사용합니다. 그런 다음, PowerShell을 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b051a4ffe4d24e1ef0e69ab7c18a8ed3388b57e5
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078285"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Blob 업로드, 다운로드 및 나열

Azure PowerShell 모듈을 사용하여 Azure 리소스를 만들고 관리합니다. PowerShell 명령줄 또는 스크립트에서 Azure 리소스 만들기 또는 관리를 수행할 수 있습니다. 이 가이드에서는 PowerShell을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 설명합니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Storage에 액세스하려면 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 빠른 시작에서는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. `Get-InstalledModule -Name Az -AllVersions | select Name,Version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 폴더에서 컴퓨터의 파일을 구성하는 방식과 같이 Blob 그룹을 구성할 수 있습니다.

컨테이너 이름을 설정한 다음, [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)를 사용하여 컨테이너를 만듭니다. 파일의 공용 액세스를 허용하도록 권한을 `blob`으로 설정합니다. 이 예제의 컨테이너 이름은 *quickstartblobs*입니다.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. IaaS VM을 백업하는 VHD 파일은 페이지 Blob입니다. 파일에 쓴 다음, 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 추가 Blob을 사용합니다. Blob Storage에 저장된 대부분의 파일은 블록 Blob입니다. 

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 해당 컨테이너의 블록 Blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다.

다음 예제에서는 *Image001.jpg* 및 *Image002.png*를 로컬 디스크의 *D:\\_TestImages* 폴더에서 방금 만든 컨테이너로 업로드합니다.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx 
  -StandardBlobTier Archive
```

원하는 만큼 파일을 업로드한 후 계속합니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)을 사용하여 컨테이너의 Blob 목록을 가져옵니다. 이 예제에서는 업로드된 Blob의 이름만 보여 줍니다.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blob 다운로드

로컬 디스크로 Blob을 다운로드합니다. 다운로드하려는 각 Blob의 경우 이름을 설정하고 [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent)를 호출하여 Blob을 다운로드합니다.

이 예제에서는 로컬 디스크의 *D:\\_TestImages\Downloads*에 Blob을 다운로드합니다. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>AzCopy를 사용한 데이터 전송

AzCopy 명령줄 유틸리티는 Azure Storage에 대한 스크립트 가능한 고성능 데이터 전송을 제공합니다. AzCopy를 사용하여 Blob 스토리지와 Azure Files 간에 데이터를 전송할 수 있습니다. 최신 버전의 AzCopy인 AzCopy v10에 대한 자세한 내용은 [AzCopy 시작](../common/storage-use-azcopy-v10.md)을 참조하세요. Blob 스토리지에서 AzCopy v10을 사용하는 방법에 대한 자세한 내용은 [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](../common/storage-use-azcopy-blobs.md)을 참조하세요.

다음 예제에서는 AzCopy를 사용하여 로컬 파일을 Blob에 업로드합니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>리소스 정리

만든 자산을 모두 제거합니다. 자산을 제거하는 가장 쉬운 방법은 리소스 그룹을 삭제하는 것입니다. 리소스 그룹을 제거하면 그룹 안에 포함된 모든 리소스가 삭제됩니다. 다음 예제에서 리소스 그룹을 제거하면 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬 파일 시스템과 Azure Blob 스토리지 간에 파일을 전송했습니다. PowerShell을 통해 Blob 스토리지를 사용하는 방법에 대해 자세히 알아보려면 Blob 스토리지용 Azure PowerShell 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Blob 스토리지용 Azure PowerShell 샘플](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Microsoft Azure PowerShell Storage cmdlet 참조

* [스토리지 PowerShell cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
