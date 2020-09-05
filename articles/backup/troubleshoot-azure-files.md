---
title: Azure 파일 공유 백업 문제 해결
description: 이 문서에서는 Azure 파일 공유를 보호할 때 발생하는 문제를 해결하는 방법에 대한 내용을 설명합니다.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 9e8869ed75350fd4063832932f076fb6fd52d4e5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001742"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Azure 파일 공유를 백업 하는 동안 발생 하는 문제 해결

이 문서에서는 Azure Backup 서비스를 사용 하 여 백업을 구성 하거나 Azure 파일 공유를 복원 하는 동안 발생 하는 문제를 해결 하기 위한 문제 해결 정보를 제공 합니다.

## <a name="common-configuration-issues"></a>일반적인 구성 문제

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Azure 파일 공유에 대 한 백업을 구성할 저장소 계정을 찾을 수 없습니다.

- 검색이 완료될 때까지 기다립니다.
- 저장소 계정의 파일 공유가 이미 다른 Recovery Services 자격 증명 모음으로 보호 되 고 있는지 확인 합니다.

  >[!NOTE]
  >스토리지 계정의 모든 파일 공유를 Recovery Services 자격 증명 모음 하나로 보호할 수 있습니다. [이 스크립트](scripts/backup-powershell-script-find-recovery-services-vault.md) 를 사용 하 여 저장소 계정이 등록 된 Recovery Services 자격 증명 모음을 찾을 수 있습니다.

- 지원 되지 않는 저장소 계정에 파일 공유가 없는지 확인 하세요. [Azure 파일 공유 백업에 대 한 지원 매트릭스](azure-file-share-support-matrix.md) 를 참조 하 여 지원 되는 저장소 계정을 찾을 수 있습니다.
- 저장소 계정 이름 및 리소스 그룹 이름의 결합 된 길이가 새 저장소 계정 및 77 문자 (클래식 저장소 계정)의 경우 84 자를 초과 하지 않는지 확인 합니다.
- 저장소 계정의 방화벽 설정을 확인 하 여 신뢰할 수 있는 Microsoft 서비스에서 저장소 계정에 액세스 하도록 허용 하는 옵션이 설정 되어 있는지 확인 합니다.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>스토리지 계정의 포털 상태 검색이 실패했다는 오류가 발생합니다.

파트너 구독 (CSP 사용)이 있으면 오류를 무시 합니다. 구독이 CSP를 사용 하지 않고 저장소 계정을 검색할 수 없는 경우 지원 담당자에 게 문의 하세요.

### <a name="selected-storage-account-validation-or-registration-failed"></a>선택한 저장소 계정 유효성 검사 또는 등록에 실패 했습니다.

등록을 다시 시도 합니다. 문제가 계속되면 지원에 문의하세요.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>선택한 저장소 계정에서 파일 공유를 나열 하거나 찾을 수 없습니다.

- 저장소 계정이 리소스 그룹에 존재 하 고 자격 증명 모음에서 마지막 유효성 검사 또는 등록 후에 삭제 또는 이동 되지 않았는지 확인 합니다.
- 보호 하려는 파일 공유가 삭제 되지 않았는지 확인 합니다.
- 저장소 계정이 파일 공유 백업에 지원 되는 저장소 계정 인지 확인 하세요. [Azure 파일 공유 백업에 대 한 지원 매트릭스](azure-file-share-support-matrix.md) 를 참조 하 여 지원 되는 저장소 계정을 찾을 수 있습니다.
- 파일 공유가 동일한 Recovery Services 자격 증명 모음에서 이미 보호 되 고 있는지 확인 합니다.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>백업 파일 공유 구성 또는 보호 정책 구성에 실패 했습니다.

- 구성을 다시 시도 하 여 문제가 지속 되는지 확인 합니다.
- 보호 하려는 파일 공유가 삭제 되지 않았는지 확인 합니다.
- 한 번에 여러 파일 공유를 보호 하려는 경우 일부 파일 공유에 오류가 발생 하는 경우 실패 한 파일 공유에 대 한 백업을 다시 구성 하십시오.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>보호 해제 파일 공유 후에 Recovery Services 자격 증명 모음을 삭제할 수 없습니다.

Azure Portal에서 **자격 증명 모음**  >  **백업 인프라**  >  **저장소 계정을** 열고 **등록 취소** 를 클릭 하 여 Recovery Services 자격 증명 모음에서 저장소 계정을 제거 합니다.

>[!NOTE]
>자격 증명 모음에 등록 된 모든 저장소 계정의 등록을 취소 한 후에만 Recovery Services 자격 증명 모음을 삭제할 수 있습니다.

## <a name="common-backup-or-restore-errors"></a>일반적인 백업 또는 복원 오류

>[!NOTE]
>백업 또는 복원 작업을 수행할 수 있는 충분 한 권한이 있는지 확인 하려면 [이 문서](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) 를 참조 하세요.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound-파일 공유를 찾을 수 없어서 작업에 실패 했습니다.

오류 코드: FileShareNotFound

오류 메시지: 파일 공유를 찾을 수 없어서 작업에 실패 했습니다.

보호 하려는 파일 공유가 삭제 되지 않았는지 확인 합니다.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable-저장소 계정을 찾을 수 없거나 지원 되지 않습니다.

오류 코드: UserErrorFileShareEndpointUnreachable

오류 메시지: 저장소 계정이 없거나 지원 되지 않습니다.

- 저장소 계정이 리소스 그룹에 존재 하 고 마지막 유효성 검사 후 리소스 그룹에서 삭제 또는 제거 되지 않았는지 확인 합니다.

- 저장소 계정이 파일 공유 백업에 지원 되는 저장소 계정 인지 확인 하세요.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached-이 파일 공유에 대 한 최대 스냅숏 제한에 도달 했습니다. 이전 기간이 만료 되 면 더 많은 시간이 소요 될 수 있습니다.

오류 코드: AFSMaxSnapshotReached

오류 메시지:이 파일 공유에 대 한 최대 스냅숏 제한에 도달 했습니다. 이전 기간이 만료 되 면 더 많은 시간이 소요 될 수 있습니다.

- 이 오류는 파일 공유에 대 한 여러 주문형 백업을 만들 때 발생할 수 있습니다.
- Azure Backup에서 수행 하는 것을 포함 하 여 파일 공유 당 스냅숏 수가 200 개로 제한 됩니다. 오래된 이전 백업(또는 스냅샷)은 자동으로 정리됩니다. 최대 한도에 도달할 경우 주문형 백업(또는 스냅샷)을 삭제해야 합니다.

Azure Files 포털에서 요청 시 백업(Azure 파일 공유 스냅샷)을 삭제합니다.

>[!NOTE]
> Azure Backup에서 만든 스냅샷을 삭제하면 복구 지점이 손실됩니다.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound-지정 된 저장소 계정이 더 이상 존재 하지 않으므로 작업이 실패 했습니다.

오류 코드: UserErrorStorageAccountNotFound

오류 메시지: 지정한 저장소 계정이 더 이상 존재 하지 않으므로 작업이 실패 했습니다.

저장소 계정이 여전히 존재 하며 삭제 되지 않았는지 확인 합니다.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound-제공 된 저장소 계정 세부 정보가 잘못 되었습니다.

오류 코드: UserErrorDTSStorageAccountNotFound

오류 메시지: 제공 된 저장소 계정 세부 정보가 잘못 되었습니다.

저장소 계정이 여전히 존재 하며 삭제 되지 않았는지 확인 합니다.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceGroupNotFound-리소스 그룹이 없습니다.

오류 코드: UserErrorResourceGroupNotFound

오류 메시지: 리소스 그룹이 없습니다.

기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest-이 파일 공유에 대 한 백업 작업이 이미 진행 중입니다.

오류 코드: ParallelSnapshotRequest

오류 메시지:이 파일 공유에 대 한 백업 작업이 이미 진행 중입니다.

- 파일 공유 백업은 동일한 파일 공유에 대 한 병렬 스냅숏 요청을 지원 하지 않습니다.

- 기존 백업 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오. Recovery Services 자격 증명 모음에서 백업 작업을 찾을 수 없는 경우 동일한 구독에서 다른 Recovery Services 자격 증명 모음을 선택 합니다.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling-저장소 서비스 제한으로 인해 파일 공유 백업 또는 복원에 실패 했습니다. 저장소 서비스가 지정 된 저장소 계정에 대 한 다른 요청을 처리 하는 중이기 때문일 수 있습니다.

오류 코드: FileshareBackupFailedWithAzureRpRequestThrottling/FileshareRestoreFailedWithAzureRpRequestThrottling

오류 메시지: 저장소 서비스 제한으로 인해 파일 공유 백업 또는 복원에 실패 했습니다. 스토리지 서비스가 특정 스토리지 계정에 대한 다른 요청을 처리 중이기 때문에 이 오류가 발생할 수 있습니다.

나중에 백업/복원 작업을 시도 합니다.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound-대상 파일 공유를 찾을 수 없습니다.

오류 코드: TargetFileShareNotFound

오류 메시지: 대상 파일 공유를 찾을 수 없습니다.

- 선택한 저장소 계정이 존재 하 고 대상 파일 공유가 삭제 되지 않았는지 확인 합니다.

- 저장소 계정이 파일 공유 백업에 지원 되는 저장소 계정 인지 확인 하세요.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked-저장소 계정이 잠긴 상태 이므로 백업 또는 복원 작업이 실패 했습니다.

오류 코드: UserErrorStorageAccountIsLocked

오류 메시지: 저장소 계정이 잠긴 상태 여 서 백업 또는 복원 작업이 실패 했습니다.

저장소 계정에 대 한 잠금을 제거 하거나 **읽기 잠금** 대신 **삭제 잠금을** 사용 하 고 백업 또는 복원 작업을 다시 시도 하세요.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>실패 한 파일 수가 임계값을 초과 하 여 DataTransferServiceCoFLimitReached 복구에 실패 했습니다.

오류 코드: DataTransferServiceCoFLimitReached

오류 메시지: 실패 한 파일 수가 임계값을 초과 하 여 복구에 실패 했습니다.

- 복구 실패 이유는 파일 (작업 세부 정보에 제공 된 경로)에 나열 되어 있습니다. 오류를 해결 하 고 실패 한 파일에 대해서만 복원 작업을 다시 시도 합니다.

- 파일 복원에 실패 하는 일반적인 이유:

  - 실패 한 파일이 현재 사용 중입니다.
  - 실패 한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover-복구할 수 있는 파일이 없어서 복구에 실패 했습니다.

오류 코드: DataTransferServiceAllFilesFailedToRecover

오류 메시지: 복구할 수 있는 파일이 없어서 복구에 실패 했습니다.

- 복구 실패 이유는 파일 (작업 세부 정보에 제공 된 경로)에 나열 되어 있습니다. 오류를 해결하고 실패한 파일에 대해서만 복원 작업을 다시 시도합니다.

- 파일 복원에 실패 하는 일반적인 이유:

  - 실패 한 파일이 현재 사용 중입니다.
  - 실패 한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>원본에 있는 파일 중 하나가 없으므로 UserErrorDTSSourceUriNotValid이 실패 합니다.

오류 코드: DataTransferServiceSourceUriNotValid

오류 메시지: 원본의 파일 중 하나가 없으므로 복원에 실패 합니다.

- 선택한 항목이 복구 지점 데이터에 없습니다. 파일을 복구하려면 올바른 파일 목록을 제공하세요.
- 복구 지점에 해당하는 파일 공유 스냅샷은 수동으로 삭제됩니다. 다른 복구 지점을 선택하고 복원 작업을 다시 시도합니다.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked-동일한 대상에 복구 작업이 진행 중입니다.

오류 코드: UserErrorDTSDestLocked

오류 메시지: 복구 작업이 동일한 대상에 대해 처리 중입니다.

- 파일 공유 백업은 동일한 대상 파일 공유에 대 한 병렬 복구를 지원 하지 않습니다.

- 기존 복구가 완료될 때까지 기다린 후 다시 시도합니다. Recovery Services 자격 증명 모음에서 복구 작업을 찾을 수 없는 경우 동일한 구독에서 다른 Recovery Services 자격 증명 모음을 선택 합니다.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull-대상 파일 공유가 가득 차서 복원 작업에 실패 했습니다.

오류 코드: UserErrorTargetFileShareFull

오류 메시지: 대상 파일 공유가 가득 차서 복원 작업에 실패 했습니다.

복원 데이터를 수용 하기 위해 대상 파일 공유 크기 할당량을 늘리고 복원 작업을 다시 시도 합니다.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetFileShareQuotaNotSufficient-대상 파일 공유에 복원에 필요한 저장소 크기 할당량이 부족 합니다.

오류 코드: UserErrorTargetFileShareQuotaNotSufficient

오류 메시지: 대상 파일 공유에 복원에 필요한 저장소 크기 할당량이 부족 합니다.

복원 데이터를 수용 하기 위해 대상 파일 공유 크기 할당량을 늘리고 작업을 다시 시도 하세요.

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>파일 동기화 PreRestoreFailed-대상 파일 공유와 연결 된 파일 동기화 서비스 리소스에 대해 사전 복원 작업을 수행 하는 동안 오류가 발생 하 여 복원 작업이 실패 했습니다.

오류 코드: 파일 동기화 PreRestoreFailed

오류 메시지: 대상 파일 공유와 연결 된 파일 동기화 Service 리소스에 대 한 복원 전 작업을 수행 하는 동안 오류가 발생 하 여 복원 작업이 실패 했습니다.

나중에 데이터를 복원 해 보세요. 문제가 지속되면 Microsoft 지원에 문의하세요.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>AzureFileSyncChangeDetectionInProgress-대상 파일 공유에 대 한 Azure File Sync 서비스 변경 검색이 진행 중입니다. 대상 파일 공유에 대 한 이전 복원에서 변경 내용 검색을 트리거 했습니다.

오류 코드: AzureFileSyncChangeDetectionInProgress

오류 메시지: 대상 파일 공유에 대 한 Azure File Sync 서비스 변경 검색이 진행 중입니다. 대상 파일 공유에 대 한 이전 복원에서 변경 내용 검색을 트리거 했습니다.

다른 대상 파일 공유를 사용 하십시오. 또는 복원을 다시 시도 하기 전에 대상 파일 공유에 대 한 Azure File Sync 서비스 변경 검색이 완료 될 때까지 기다릴 수 있습니다.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored-하나 이상의 파일을 성공적으로 복구할 수 없습니다. 자세한 내용은 위에 지정 된 경로에서 실패 한 파일 목록을 확인 하세요.

오류 코드: UserErrorAFSRecoverySomeFilesNotRestored

오류 메시지: 하나 이상의 파일을 성공적으로 복구할 수 없습니다. 자세한 내용은 위에 제공된 경로에서 실패한 파일 목록을 확인하세요.

- 복구 실패 이유는 파일 (작업 세부 정보에 제공 된 경로)에 나열 되어 있습니다. 이유를 해결 하 고 실패 한 파일에 대해서만 복원 작업을 다시 시도 합니다.
- 파일 복원에 실패 하는 일반적인 이유:

  - 실패 한 파일이 현재 사용 중입니다.
  - 실패 한 파일과 같은 이름의 디렉터리가 부모 디렉터리에 있습니다.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound-복구 지점에 해당 하는 Azure 파일 공유 스냅숏을 찾을 수 없습니다.

오류 코드: UserErrorAFSSourceSnapshotNotFound

오류 메시지: 복구 지점에 해당 하는 Azure 파일 공유 스냅숏을 찾을 수 없습니다.

- 복구에 사용 하려고 하는 복구 지점에 해당 하는 파일 공유 스냅숏이 여전히 존재 하는지 확인 합니다.

  >[!NOTE]
  >Azure Backup 여 만든 파일 공유 스냅숏을 삭제 하면 해당 복구 지점은 사용할 수 없게 됩니다. 복구를 보장 하려면 스냅숏을 삭제 하지 않는 것이 좋습니다.

- 다른 복원 지점을 선택 하 여 데이터를 복구 해 보세요.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget-동일한 대상 파일 공유에서 다른 복원 작업이 진행 중입니다.

오류 코드: UserErrorAnotherRestoreInProgressOnSameTarget

오류 메시지: 동일한 대상 파일 공유에서 다른 복원 작업이 진행 중입니다.

다른 대상 파일 공유를 사용 하십시오. 또는 취소 하거나 다른 복원이 완료 될 때까지 기다릴 수 있습니다.

## <a name="common-modify-policy-errors"></a>일반적인 정책 수정 오류

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation-이 항목에 대 한 다른 보호 구성 작업이 진행 중입니다.

오류 코드: BMSUserErrorConflictingProtectionOperation

오류 메시지:이 항목에 대 한 다른 보호 구성 작업이 진행 중입니다.

이전 정책 수정 작업이 완료 될 때까지 기다렸다가 나중에 다시 시도 합니다.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked-선택한 항목에서 다른 작업이 진행 중입니다.

오류 코드: BMSUserErrorObjectLocked

오류 메시지: 선택한 항목에서 다른 작업이 진행 중입니다.

진행 중인 다른 작업이 완료 될 때까지 기다렸다가 나중에 다시 시도 하세요.

## <a name="common-soft-delete-related-errors"></a>일반적인 일시 삭제 관련 오류

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState-이 지점과 연결 된 스냅숏이 일시 삭제 된 상태에 있는 파일 공유에 있으므로이 복원 지점을 사용할 수 없습니다.

오류 코드: UserErrorRestoreAFSInSoftDeleteState

오류 메시지:이 지점과 연결 된 스냅숏이 일시 삭제 된 상태에 있는 파일 공유에 있으므로이 복원 지점을 사용할 수 없습니다.

파일 공유가 일시 삭제 된 상태 이면 복원 작업을 수행할 수 없습니다. 파일 포털에서 또는 [삭제 취소 스크립트](scripts/backup-powershell-script-undelete-file-share.md) 를 사용 하 여 파일 공유의 삭제를 취소 한 다음 복원을 시도 합니다.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>복원 지점 스냅숏이 포함 된 연결 된 파일 공유가 영구적으로 삭제 되었으므로 UserErrorRestoreAFSInDeleteState에 나열 된 복원 지점을 사용할 수 없습니다.

오류 코드: UserErrorRestoreAFSInDeleteState

오류 메시지: 복원 지점 스냅숏이 포함 된 연결 된 파일 공유가 영구적으로 삭제 되었으므로 나열 된 복원 지점을 사용할 수 없습니다.

백업 된 파일 공유가 삭제 되었는지 확인 합니다. 일시 삭제 된 상태 였던 경우 일시 삭제 보존 기간이 초과 되었고 복구 되지 않았는지 확인 합니다. 이러한 경우 모든 스냅숏이 영구적으로 손실 되며 데이터를 복구할 수 없습니다.

>[!NOTE]
> 백업 된 파일 공유를 삭제 하지 않거나 일시 삭제 된 상태에 있는 경우에는 모든 복원 지점이 손실 되지 않도록 일시 삭제 보존 기간이 끝나기 전에 삭제 취소를 수행 하는 것이 좋습니다.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>Azure 파일 공유가 일시 삭제 된 상태 이므로 Usererrorbackupafsin소프트 Deletestate-백업 실패

오류 코드: usererrorbackupafsin소프트 delet

오류 메시지: Azure 파일 공유가 일시 삭제 된 상태 여 서 백업이 실패 했습니다.

파일 **포털** 에서 파일 공유의 삭제를 취소 하거나 [삭제 취소 스크립트](scripts/backup-powershell-script-undelete-file-share.md) 를 사용 하 여 백업을 계속 하 고 데이터 영구 삭제를 방지 합니다.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>연결 된 Azure 파일 공유가 영구적으로 삭제 되어 UserErrorBackupAFSInDeleteState-백업 실패

오류 코드: UserErrorBackupAFSInDeleteState

오류 메시지: 연결 된 Azure 파일 공유가 영구적으로 삭제 되어 백업에 실패 했습니다.

백업 된 파일 공유가 영구적으로 삭제 되었는지 확인 합니다. 그렇다면 백업 실패를 방지 하기 위해 파일 공유에 대 한 백업을 중지 합니다. 보호를 중지 하는 방법에 대해 알아보려면 [Azure 파일 공유에 대 한 보호 중지](./manage-afs-backup.md#stop-protection-on-a-file-share) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 백업에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 파일 공유 백업](backup-afs.md)
- [Azure 파일 공유 백업 FAQ](backup-azure-files-faq.md)
