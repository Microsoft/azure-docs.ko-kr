---
title: 'PowerShell: 앱 백업 삭제'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 앱 백업을 삭제하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 29eda8fcb24dce6c54fb56c94d242513b319caad
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073967"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Azure PowerShell을 사용하여 웹에 대한 백업 삭제

이 샘플 스크립트는 App Service에 관련 리소스가 있는 웹앱을 만든 다음 이에 대한 일회성 백업을 만듭니다. 

이 스크립트를 실행하려면 웹앱의 기존 백업이 필요합니다. 만들려면 [웹앱 백업](powershell-backup-onetime.md) 또는 [웹앱에 대한 예약된 백업 만들기](powershell-backup-scheduled.md)를 참조하세요.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 웹앱의 백업 목록을 가져옵니다. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | 웹앱의 지정된 백업을 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
