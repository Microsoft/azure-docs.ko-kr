---
title: 'PowerShell: 예약된 백업 만들기'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 앱에 대해 예약된 백업을 만드는 방법을 보여줍니다.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: c53872973dad3d438d0d948c4eaebaf0cb7ba4de
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89075896"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>PowerShell을 사용하여 웹앱에 대한 예약된 백업 만들기

이 샘플 스크립트는 App Service에 관련 리소스가 있는 웹앱을 만든 다음 이에 대한 예약된 백업을 만듭니다. 

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Storage 계정을 만듭니다. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Azure Storage 컨테이너를 만듭니다. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Azure Storage 컨테이너에 대한 SAS 토큰을 생성합니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 웹앱을 만듭니다. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | 웹앱에 대한 백업 구성을 편집합니다. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 웹앱의 백업 목록을 가져옵니다. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | 웹앱에 대한 구성을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
