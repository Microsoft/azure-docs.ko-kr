---
title: Azure Files 백업 FAQ
description: 이 문서에서는 Azure Backup 서비스를 사용하여 Azure 파일 공유를 보호하는 방법에 대한 일반적인 질문과 답변을 검색합니다.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0db30de655bfc0b98baa81a4ef20532e697fc1f8
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824732"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files 백업에 대한 질문

이 문서에서는 Azure Files 백업에 대한 일반적인 질문에 대답합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. 또한 [Microsoft 질문과 답변의 토론 관련 질문 페이지](/answers/topics/azure-backup.html)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

이 문서의 섹션을 신속하게 검색하려면 **이 문서** 아래의 오른쪽에서 링크를 사용합니다.

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files에 대한 백업 작업 구성

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>유효한 Azure 파일 공유가 포함되어 있고 보호하려는 일부 스토리지 계정이 표시되지 않는 이유는 무엇인가요?

스토리지 계정이 지원되는 스토리지 계정 유형 중 하나에 속하도록 하려면 [Azure 파일 공유 백업에 대한 지원 매트릭스](azure-file-share-support-matrix.md)를 참조하세요. 원하는 Storage 계정이 이미 보호되거나 다른 자격 증명 모음으로 등록되었을 수도 있습니다. 자격 증명 모음에서 [스토리지 계정의 등록을 취소](manage-afs-backup.md#unregister-a-storage-account)하여 보호를 위해 다른 자격 증명 모음에서 Storage 계정을 검색합니다.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>백업을 구성하려고 할 때 스토리지 계정에서 일부 Azure 파일 공유가 표시되지 않는 이유는 무엇인가요?

Azure 파일 공유가 동일한 Recovery Services 자격 증명 모음에서 이미 보호되었거나 최근에 삭제되었는지 확인합니다.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Azure File Sync에서 동기화 그룹에 연결된 파일 공유를 보호할 수 있나요?

예. 동기화 그룹에 연결된 Azure 파일 공유를 보호할 수 있습니다.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>파일 공유를 백업하려고 시도할 때 스토리지 계정의 파일 공유를 검색하기 위해 스토리지 계정을 클릭했습니다. 그런데 파일 공유를 보호하지 않았습니다. 다른 자격 증명 모음으로 이러한 파일 공유를 보호하려면 어떻게 해야 하나요?

백업하려고 시도할 때 파일 공유를 검색할 스토리지 계정을 선택하면 파일 공유가 검색되는 자격 증명 모음에 스토리지 계정이 등록됩니다. 다른 자격 증명 모음으로 파일 공유를 보호하려면 이 자격 증명 모음에서 선택한 스토리지 계정을 [등록 취소](manage-afs-backup.md#unregister-a-storage-account)합니다.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>파일 공유에 대 한 백업을 구성 하기 위해 자격 증명 모음을 변경할 수 없는 이유는 무엇입니까?

저장소 계정이 자격 증명 모음에 이미 등록 되어 있거나 저장소 계정의 다른 파일 공유가 자격 증명 모음을 사용 하 여 보호 되는 경우이를 변경할 수 있는 옵션이 제공 되지 않습니다. 저장소 계정의 모든 파일 공유는 동일한 자격 증명 모음 에서만 보호할 수 있습니다. 자격 증명 모음을 변경 하려면 연결 된 자격 증명 모음에서 [저장소 계정의 모든 파일 공유에 대 한 보호를 중지](manage-afs-backup.md#stop-protection-on-a-file-share) 하 고, 저장소 계정의 [등록을 취소](manage-afs-backup.md#unregister-a-storage-account) 한 후 보호를 위해 다른 자격 증명 모음을 선택 해야 합니다.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>파일 공유를 백업하는 자격 증명 모음을 변경할 수 있나요?

예. 그러나 연결된 자격 증명 모음에서 [파일 공유에 대한 보호를 중지](manage-afs-backup.md#stop-protection-on-a-file-share)하고, 이 스토리지 계정을 [등록 취소](manage-afs-backup.md#unregister-a-storage-account)한 다음, 다른 자격 증명 모음에서 파일 공유를 보호해야 합니다.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>동일한 스토리지 계정의 두 파일 공유를 서로 다른 자격 증명 모음에서 보호할 수 있나요?

아니요. 한 스토리지 계정의 모든 파일 공유는 동일한 자격 증명 모음에서만 보호할 수 있습니다.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>최대 제한 도달 오류로 인해 백업에 실패하는 경우 어떻게 해야 하나요?

언제든지 파일 공유에 대해 최대 200개의 스냅샷을 가질 수 있습니다. 이 한도에는 정책에 정의된 대로 Azure Backup에서 생성한 스냅샷이 포함됩니다. 한도에 도달한 후 백업이 실패하기 시작하면 향후 백업에 성공하기 위해 주문형 스냅샷을 삭제합니다.

## <a name="restore"></a>복원

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>삭제된 Azure 파일 공유에서 복구할 수 있나요?

파일 공유가 일시 삭제 된 상태 이면 먼저 파일 공유의 삭제를 취소 하 여 복원 작업을 수행 해야 합니다. 삭제 취소 작업은 파일 공유를 특정 시점으로 복원할 수 있는 활성 상태로 전환 합니다. 파일 공유의 삭제를 취소 하는 방법을 알아보려면 [이 링크](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 를 방문 하거나 [삭제 취소 파일 공유 스크립트](./scripts/backup-powershell-script-undelete-file-share.md)를 참조 하세요. 파일 공유가 영구적으로 삭제 되 면 콘텐츠와 스냅숏을 복원할 수 없습니다.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Azure 파일 공유에 대한 보호를 중지한 경우 백업에서 복원할 수 있나요?

예. 보호를 중지할 때 **백업 데이터 보관**을 선택한 경우 모든 기존 복원 지점에서 복원할 수 있습니다.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업이 취소되면 복원 프로세스가 중지되고 취소 전에 모든 파일이 복원되면 롤백 없이 구성된 대상(원래 또는 대체 위치)에 남아 있습니다.

## <a name="manage-backup"></a>백업 관리

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>PowerShell을 사용하여 Azure 파일 공유에 대한 백업을 구성/관리/복원할 수 있나요?

예. [여기](backup-azure-afs-automation.md)에 있는 자세한 설명서를 참조하세요.

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Azure Backup에서 만든 스냅샷에 액세스하고 탑재할 수 있나요?

Azure Backup에서 만든 모든 스냅샷은 포털의 스냅샷 보기, PowerShell 또는 CLI로 액세스할 수 있습니다. Azure 파일 공유 스냅샷에 대해 자세히 알아보려면 [Azure Files의 공유 스냅샷 개요](../storage/files/storage-snapshots-files.md)를 참조하세요.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>백업에 대해 구성할 수 있는 최대 보존 기간은 얼마인가요?

최대 보존 기간에 대한 자세한 내용은 [지원 매트릭스](azure-file-share-support-matrix.md)를 참조하세요. Azure Backup은 백업 정책을 구성하는 동안 보존 기간 값을 입력할 때 스냅샷 수를 실시간으로 계산합니다. 정의된 보존 기간 값에 해당하는 스냅샷 수가 200을 초과하는 즉시 포털에 보존 기간 값을 조정하도록 요청하는 경고가 표시됩니다. 즉, 언제든지 파일 공유에 대한 Azure Files에서 지원하는 최대 스냅샷 수 제한을 초과하지 않습니다.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Azure 파일 공유에 대한 백업 정책을 수정하여 "매일 정책"에서 "GFS 정책"으로 전환하는 경우 기존 복구 지점과 스냅샷에 어떤 영향이 있나요?

일일 백업 정책을 GFS 정책(매주/매월/매년 보존 기간 추가)으로 수정하는 경우의 동작은 다음과 같습니다.

- **보존 기간**: 정책을 수정하는 과정에서 매주/매월/매년 보존 기간을 추가하는 경우 예약된 백업의 일부로 생성되는 모든 이후의 복구 지점은 새 정책에 따라 태그가 지정됩니다. 모든 기존 복구 지점은 여전히 매일 복구 지점으로 간주되므로 매주/매월/매년으로 태그를 지정하지 않습니다.

- **스냅샷 및 복구 지점 정리**:

  - 매일 보존 기간이 연장되면 기존 복구 지점의 만료 날짜는 새 정책에 구성된 매일 보존 기간 값에 따라 업데이트됩니다.
  - 매일 보존 기간이 축소되면 기존 복구 지점과 스냅샷이 새 정책에 구성된 매일 보존 기간 값에 따라 다음 정리 실행 작업에서 삭제 대상으로 표시된 후 삭제됩니다.

작동 방식을 보여 주는 예는 다음과 같습니다.

#### <a name="existing-policy-p1"></a>기존 정책 [P1]

|보존 유형 |일정 |보존  |
|---------|---------|---------|
|매일    |    매일 오후 8시    |  100일       |

#### <a name="new-policy-modified-p1"></a>새 정책 [수정된 P1]

| 보존 유형 | 일정                       | 보존 |
| -------------- | ------------------------------ | --------- |
| 매일          | 매일 오후 9시              | 50일   |
| 매주         | 일요일 오후 9시              | 3주   |
| 매월        | 마지막 주 월요일 오후 9시         | 1개월   |
| 매년         | 1월 셋째 주 일요일 오후 9시 | 4년   |

#### <a name="impact"></a>영향

1. 기존 복구 지점의 만료 날짜는 새 정책의 매일 보존 기간 값(50일)에 따라 조정됩니다. 따라서 50일보다 오래된 복구 지점은 삭제 대상으로 표시됩니다.

2. 기존 복구 지점은 새 정책에 따라 매주/매월/매년 태그가 지정되지 않습니다.

3. 이후 모든 백업은 새 일정에 따라 트리거됩니다. 즉, 오후 9시에 트리거됩니다.

4. 이후 모든 복구 지점의 만료 날짜는 새 정책에 맞게 조정됩니다.

>[!NOTE]
>정책 변경 내용은 예약된 백업 작업 실행의 일부로 생성된 복구 지점에만 영향을 줍니다. 주문형 백업의 보존 기간은 백업을 수행하는 시점에 지정된 **보존 기한** 값에 의해 결정됩니다.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>기존 GFS 정책을 수정하면 기존 복구 지점에 어떤 영향이 있나요?

파일 공유에 새 정책이 적용되면 이후에 예약되는 모든 백업이 수정된 정책에 구성된 일정에 따라 수행됩니다.  모든 기존 복구 지점의 보존 기간은 구성된 새 보존 기간 값에 따라 조정됩니다. 보존 기간이 연장되면 기존 복구 지점이 새 정책에 따라 보존 대상으로 표시됩니다. 보존 기간이 축소되면 다음 정리 작업에서 정리 대상으로 표시된 후 삭제됩니다.

작동 방식을 보여 주는 예는 다음과 같습니다.

#### <a name="existing-policy-p2"></a>기존 정책 [P2]

| 보존 유형 | 일정           | 보존 |
| -------------- | ------------------ | --------- |
| 매일          | 매일 오후 8시 | 50일   |
| 매주         | 월요일 오후 8시  | 3주   |

#### <a name="new-policy-modified-p2"></a>새 정책 [수정된 P2]

| 보존 유형 | 일정               | 보존 |
| -------------- | ---------------------- | --------- |
| 매일          | 매일 오후 9시     | 10일   |
| 매주         | 월요일 오후 9시      | 2주   |
| 매월        | 마지막 주 월요일 오후 9시 | 2개월  |

#### <a name="impact-of-change"></a>변경의 영향

1. 기존의 매일 복구 지점의 만료 날짜는 새 매일 보존 기간 값(10일)에 따라 조정됩니다. 따라서 10일보다 오래된 매일 복구 지점은 삭제됩니다.

2. 기존의 매주 복구 지점의 만료 날짜는 새 매주 보존 기간 값(2주)에 따라 조정됩니다. 따라서 2주보다 오래된 매주 복구 지점은 삭제됩니다.

3. 매월 복구 지점은 새 정책 구성에 따라 이후 백업의 일부로만 생성됩니다.

4. 이후 모든 복구 지점의 만료 날짜는 새 정책에 맞게 조정됩니다.

>[!NOTE]
>정책 변경 내용은 예약된 백업의 일부로 생성된 복구 지점에만 영향을 줍니다. 주문형 백업의 보존 기간은 백업을 수행하는 시점에 지정된 **보존 기한** 값에 의해 결정됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 파일 공유를 백업 하는 동안 발생 하는 문제 해결](troubleshoot-azure-files.md)
