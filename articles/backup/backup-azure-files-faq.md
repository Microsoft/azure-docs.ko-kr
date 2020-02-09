---
title: Azure Files 백업 FAQ
description: 이 문서에서는 Azure Backup 서비스를 사용하여 Azure 파일 공유를 보호하는 방법에 대한 일반적인 질문과 답변을 검색합니다.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 053fe38b07fff5d289c72024ef185fbf5d647f70
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294000"
---
# <a name="questions-about-backing-up-azure-files"></a>Azure Files 백업에 대한 질문

이 문서에서는 Azure Files 백업에 대한 일반적인 질문에 대답합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

이 문서의 섹션을 신속하게 검색하려면 **이 문서** 아래의 오른쪽에서 링크를 사용합니다.

## <a name="configuring-the-backup-job-for-azure-files"></a>Azure Files에 대한 백업 작업 구성

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>유효한 Azure 파일 공유가 포함되어 있고 보호하려는 일부 스토리지 계정이 표시되지 않는 이유는 무엇인가요?

미리 보기로 있는 동안에는 Azure 파일 공유에 대한 Backup에서 일부 유형의 스토리지 계정이 지원되지 않습니다. 지원되는 스토리지 계정 목록을 보려면 [여기](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) 목록을 참조하세요. 원하는 Storage 계정이 이미 보호되거나 다른 자격 증명 모음으로 등록되었을 수도 있습니다. 자격 증명 모음에서 [등록을 취소](troubleshoot-azure-files.md#configuring-backup)하여 보호를 위해 다른 자격 증명 모음에서 Storage 계정을 검색합니다.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>백업을 구성하려고 할 때 스토리지 계정에서 일부 Azure 파일 공유가 표시되지 않는 이유는 무엇인가요?

Azure 파일 공유가 동일한 Recovery Services 자격 증명 모음에서 이미 보호되었거나 최근에 삭제되었는지 확인합니다.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Azure File Sync에서 동기화 그룹에 연결된 파일 공유를 보호할 수 있나요?

예. 동기화 그룹에 연결된 Azure 파일 공유를 보호하도록 설정되어 있으며 이 기능은 공개 미리 보기의 일부입니다.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>파일 공유를 백업하려고 시도할 때 스토리지 계정의 파일 공유를 검색하기 위해 스토리지 계정을 클릭했습니다. 그런데 파일 공유를 보호하지 않았습니다. 다른 자격 증명 모음으로 이러한 파일 공유를 보호하려면 어떻게 해야 하나요?

백업하려고 시도할 때 파일 공유를 검색할 스토리지 계정을 선택하면 파일 공유가 검색되는 자격 증명 모음에 스토리지 계정이 등록됩니다. 다른 자격 증명 모음으로 파일 공유를 보호하려면 이 자격 증명 모음에서 선택한 스토리지 계정을 [등록 취소](troubleshoot-azure-files.md#configuring-backup)합니다.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>파일 공유를 백업하는 자격 증명 모음을 변경할 수 있나요?

예. 그러나 연결 된 자격 증명 모음에서 [파일 공유에 대한 보호를 중지](manage-afs-backup.md#stop-protection-on-a-file-share) 하 고이 저장소 계정의 등록을 [취소](troubleshoot-azure-files.md#configuring-backup) 한 다음 다른 자격 증명 모음에서 보호 해야 합니다.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>Azure 파일 공유를 백업할 수 있는 지역은 어디인가요?

Azure 파일 공유에 대한 Backup은 현재 미리 보기로 제공되며 다음 지역에서만 사용할 수 있습니다.

- 오스트레일리아 동부(AE)
- 오스트레일리아 동남부(ASE)
- 브라질 남부(BRS)
- 캐나다 중부(CNC)
- 캐나다 동부(CE)
- 미국 중부(CUS)
- 동아시아(EA)
- 미국 동부(EUS)
- 미국 동부 2(EUS2)
- 일본 동부(JPE)
- 일본 서부(JPW)
- 인도 중부(INC)
- 인도 남부(INS)
- 한국 중부(KRC)
- 한국 남부(KRS)
- 미국 중북부(NCUS)
- 북유럽(NE)
- 미국 중남부(SCUS)
- 동남 아시아(SEA)
- 영국 남부(UKS)
- 영국 서부(UKW)
- 서유럽(WE)
- 미국 서부(WUS)
- 미국 중서부(WCUS)
- 미국 서부 2(WUS 2)
- US Gov 애리조나(UGA)
- US Gov 텍사스(UGT)
- US Gov 버지니아(UGV)
- 오스트레일리아 중부 (ACL)
- 인도 서 부 (INW)
- 남아프리카 공화국 북부 (SAN)
- 아랍에미리트 북부 (UAN)
- 프랑스 중부 (FRC)
- 독일 북부 (GN)                       
- 독일 중서부 (GWC)
- 남아프리카 공화국 서 (본)
- 아랍에미리트 Central (UAC)
- NWE (노르웨이 동부)     
- NWW (노르웨이 서 부)
- SZN (스위스 북부)

위에 나열되지 않은 특정 지역에서 사용해야 하는 경우 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com)으로 문의하세요.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>자격 증명 모음에서 보호할 수 있는 Azure 파일 공유 수는 몇 개인가요?

미리 보기 동안에는 자격 증명 모음당 최대 50개의 스토리지 계정에서 Azure 파일 공유를 보호할 수 있습니다. 또한 자격 증명 모음당 최대 200개의 Azure 파일 공유를 보호할 수 있습니다.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>동일한 스토리지 계정의 두 파일 공유를 서로 다른 자격 증명 모음에서 보호할 수 있나요?

아닙니다. 한 스토리지 계정의 모든 파일 공유는 동일한 자격 증명 모음에서만 보호할 수 있습니다.

## <a name="backup"></a>Backup

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>파일 공유당 구성할 수 있는 예약 백업 수는 몇 개인가요?

현재 Azure Backup은 Azure 파일 공유의 예약 백업을 하루에 한 번 구성할 수 있습니다.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>파일 공유당 만들 수 있는 주문형 백업은 몇 개입니까?

언제든지 파일 공유에 대해 최대 200개의 스냅샷을 가질 수 있습니다. 이 한도에는 정책에 정의된 대로 Azure Backup에서 생성한 스냅샷이 포함됩니다. 한도에 도달한 후 백업이 실패하기 시작하면 향후 백업에 성공하기 위해 주문형 복원 지점을 삭제합니다.

## <a name="restore"></a>복원

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>삭제된 Azure 파일 공유에서 복구할 수 있나요?

Azure 파일 공유가 삭제되면 삭제될 백업 목록이 표시되고 확인이 요청됩니다. 삭제된 Azure 파일 공유는 복원할 수 없습니다.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Azure 파일 공유에 대한 보호를 중지한 경우 백업에서 복원할 수 있나요?

예. 보호를 중지할 때 **백업 데이터 보관**을 선택한 경우 모든 기존 복원 지점에서 복원할 수 있습니다.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>진행 중인 복원 작업을 취소하면 어떻게 되나요?

진행 중인 복원 작업이 취소되면 복원 프로세스가 중지되고 취소 전에 모든 파일이 복원되면 롤백 없이 구성된 대상(원래 또는 대체 위치)에 남아 있습니다.

## <a name="manage-backup"></a>백업 관리

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>PowerShell을 사용하여 Azure 파일 공유에 대한 백업을 구성/관리/복원할 수 있나요?

예. [여기](backup-azure-afs-automation.md)에 있는 자세한 설명서를 참조하세요.

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Azure Backup에서 만든 스냅샷에 액세스하고 탑재할 수 있나요?

Azure Backup에서 만든 모든 스냅샷은 포털의 스냅샷 보기, PowerShell 또는 CLI로 액세스할 수 있습니다. Azure 파일 공유 스냅샷에 대한 자세한 내용은 [Azure Files(미리 보기)의 공유 스냅샷 개요](../storage/files/storage-snapshots-files.md)를 참조하세요.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>백업에 대해 구성할 수 있는 최대 보존 기간은 얼마인가요?

Azure 파일 공유 백업은 최대 180일의 보존 기간이 적용된 정책을 구성할 수 있는 기능을 제공합니다. 그러나 [PowerShell의 "요청 시 백업"](backup-azure-afs-automation.md#trigger-an-on-demand-backup) 옵션을 사용하면 복구 지점을 10년 동안 유지할 수도 있습니다.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Azure 파일 공유에 대한 Backup 정책을 변경하면 어떻게 되나요?

파일 공유에 대한 새 정책이 적용되면 새 정책의 일정 및 보존 기간을 따릅니다. 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 유지되도록 표시됩니다. 보존 기간을 줄이면 다음 정리 작업에서 정리(prune) 표시되고 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Azure Backup의 다른 영역에 대한 자세한 내용은 다음 백업 FAQ를 참조하세요.

- [Recovery Services 자격 증명 모음 FAQ](backup-azure-backup-faq.md)
- [Azure VM 백업 FAQ](backup-azure-vm-backup-faq.md)
- [Azure Backup 에이전트 FAQ](backup-azure-file-folder-backup-faq.md)
