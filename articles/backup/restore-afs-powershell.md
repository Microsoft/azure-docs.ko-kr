---
title: PowerShell을 사용 하 여 Azure Files 복원
description: 이 문서에서는 Azure Backup 서비스와 PowerShell을 사용 하 여 Azure Files를 복원 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826500"
---
# <a name="restore-azure-files-with-powershell"></a>PowerShell을 사용 하 여 Azure Files 복원

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure Backup](backup-overview.md) 서비스에서 만든 복원 지점에서 전체 파일 공유 또는 특정 파일을 복원 하는 방법을 설명 합니다.

공유에서 전체 파일 공유 또는 특정 파일을 복원할 수 있습니다. 원본 위치 또는 대체 위치에 복원할 수 있습니다.

> [!WARNING]
> AFS 백업의 경우 PowerShell 버전이 ' Az. RecoveryServices 2.6.0 '의 최소 버전으로 업그레이드 되었는지 확인 합니다. 자세한 내용은이 변경에 대 한 요구 사항 개요 [섹션](backup-azure-afs-automation.md#important-notice-backup-item-identification) 을 참조 하세요.

>[!NOTE]
>이제 Azure Backup에서는 PowerShell을 사용 하 여 원본 또는 대체 위치로 여러 파일 또는 폴더를 복원할 수 있습니다. 자세한 내용은 문서에서 [이 섹션](#restore-multiple-files-or-folders-to-original-or-alternate-location) 을 참조 하세요.

## <a name="fetch-recovery-points"></a>복구 지점 가져오기

[AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) 를 사용 하 여 백업 된 항목에 대 한 모든 복구 지점의 목록을 표시 합니다.

다음 스크립트에서:

* **$Rp** 변수는 지난 7 일간 선택한 백업 항목에 대 한 복구 지점의 배열입니다.
* 배열은 인덱스 **0**의 가장 최근 복구 지점부터 시간이 역순으로 정렬됩니다.
* 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다.
* 예에서, **$rp[0]** 은 최신 복구 지점을 선택합니다.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

출력은 다음과 유사 합니다.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

관련 복구 지점을 선택한 후에는 파일 공유 또는 파일을 원본 위치 또는 대체 위치에 복원 합니다.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure 파일 공유를 대체 위치로 복원

[AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 를 사용 하 여 선택한 복구 지점으로 복원 합니다. 이러한 매개 변수를 지정 하 여 대체 위치를 식별 합니다.

* **Targetstorageaccountname**: 백업 된 콘텐츠를 복원할 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName**: 백업 된 콘텐츠가 복원 되는 대상 저장소 계정 내의 파일 공유입니다.
* **Targetfolder**: 데이터가 복원 되는 파일 공유의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **Resolveconflict가**: 복원 된 데이터와 충돌 하는 경우 명령입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

다음과 같이 매개 변수를 사용 하 여 cmdlet을 실행 합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>대체 위치로 Azure 파일 복원

[AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 를 사용 하 여 선택한 복구 지점으로 복원 합니다. 이러한 매개 변수를 지정 하 여 대체 위치를 식별 하 고 복원 하려는 파일을 고유 하 게 식별 합니다.

* **Targetstorageaccountname**: 백업 된 콘텐츠를 복원할 저장소 계정입니다. 대상 스토리지 계정은 자격 증명 모음과 동일한 위치에 있어야 합니다.
* **TargetFileShareName**: 백업 된 콘텐츠가 복원 되는 대상 저장소 계정 내의 파일 공유입니다.
* **Targetfolder**: 데이터가 복원 되는 파일 공유의 폴더입니다. 백업된 콘텐츠를 루트 폴더로 복원해야 하는 경우 대상 폴더 값을 빈 문자열로 제공합니다.
* **Sourcefilepath**: 파일 공유 내에서 복원할 파일의 절대 경로입니다. 이 경로는 **Get-AzStorageFile** PowerShell cmdlet에 사용되는 경로와 동일합니다.
* **Sourcefiletype**: 디렉터리 또는 파일을 선택 했는지 여부입니다. **디렉터리** 또는 **파일**을 허용 합니다.
* **Resolveconflict가**: 복원 된 데이터와 충돌 하는 경우 명령입니다. **덮어쓰기** 또는 **건너뛰기**를 수락합니다.

추가 매개 변수 (SourceFilePath 및 Sourcefilepath)는 복원 하려는 개별 파일에만 관련 됩니다.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

이 명령은 이전 섹션과 같이 추적할 ID가 있는 작업을 반환 합니다.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure 파일 공유 및 파일을 원래 위치로 복원

원래 위치로 복원 하는 경우 대상 및 대상 관련 매개 변수를 지정할 필요가 없습니다. **ResolveConflict**만 제공해야 합니다.

### <a name="overwrite-an-azure-file-share"></a>Azure 파일 공유 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Azure 파일 덮어쓰기

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>원본 또는 대체 위치로 여러 파일 또는 폴더 복원

복원 하려는 모든 파일 또는 폴더의 경로를 **MultipleSourceFilePath** 매개 변수에 대 한 값으로 전달 하 여 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 명령을 사용 합니다.

### <a name="restore-multiple-files"></a>여러 파일 복원

다음 스크립트에서 *FileSharePage.png* 및 *MyTestFile.txt* 파일을 복원 하려고 합니다.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>여러 디렉터리 복원

다음 스크립트에서 *zrs1_restore* 복원 하 *고 디렉터리를 복원 하려고* 합니다.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

다음과 유사하게 출력됩니다.

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

여러 파일 또는 폴더를 대체 위치로 복원 하려면 위의 스크립트를 사용 하 여 [Azure 파일을 대체 위치로 복원](#restore-an-azure-file-to-an-alternate-location)에서 설명한 대로 대상 위치 관련 매개 변수 값을 지정 합니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure Files을 복원 하는 [방법에 대해 알아봅니다](restore-afs.md) .
