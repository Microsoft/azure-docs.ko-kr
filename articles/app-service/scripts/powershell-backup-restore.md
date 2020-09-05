---
title: 'PowerShell: 앱 백업 복원'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 백업에서 앱을 복원하는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: f5e36122ef1362672e5cdee4f742b98354f0bc75
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075907"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Azure PowerShell을 사용하여 백업에서 웹앱 복원

이 샘플 스크립트는 기존 웹앱에서 이전에 완료된 백업을 검색하고 해당 콘텐츠를 덮어쓰는 방법으로 복원합니다. 

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>배포 정리 

더 이상 웹앱이 필요하지 않은 경우 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 웹앱의 백업 목록을 가져옵니다. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | 이전에 완료된 백업에서 웹앱을 복원합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
