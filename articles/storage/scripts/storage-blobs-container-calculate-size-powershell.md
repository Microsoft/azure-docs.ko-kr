---
title: PowerShell을 사용하여 Blob 컨테이너 크기 계산
titleSuffix: Azure Storage
description: 컨테이너에 있는 각 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de275bcca1644750532809b35ae85d954d3cac6d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076908"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>PowerShell을 사용하여 Blob 컨테이너 크기 계산

이 스크립트는 컨테이너에 있는 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 이 PowerShell 스크립트는 컨테이너의 예상 크기를 제공하므로 청구 요금 계산에 사용하면 안 됩니다. 청구 목적으로 컨테이너 크기를 계산하는 스크립트는 [청구 목적으로 Blob Storage 컨테이너 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)을 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, 컨테이너 및 모든 관련된 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 Blob Storage 컨테이너의 크기를 계산합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 리소스 그룹 또는 구독의 지정된 스토리지 계정 또는 모든 스토리지 계정을 가져옵니다. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | 컨테이너의 Blob을 나열합니다. |

## <a name="next-steps"></a>다음 단계

청구 목적으로 컨테이너 크기를 계산하는 스크립트는 [청구 목적으로 Blob Storage 컨테이너 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)을 참조하세요.

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 스토리지 PowerShell 스크립트 샘플은 [Azure Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
