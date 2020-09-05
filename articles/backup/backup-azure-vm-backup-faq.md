---
title: FAQ-Azure Vm 백업
description: 이 문서에서는 Azure Backup 서비스를 사용 하 여 Azure Vm을 백업 하는 방법에 대 한 일반적인 질문에 대 한 답변을 검색 합니다.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: ec79fc7c09f2bc1aeb2c07016365e831932fa1ff
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018113"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>질문과 대답-Azure Vm 백업

이 문서에서는 [Azure Backup](./backup-overview.md) 서비스를 사용 하 여 Azure vm을 백업 하는 일반적인 질문에 답변 합니다.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>백업할 VM 이미지를 만들 때 어떤 VM 이미지를 사용 하도록 설정할 수 있나요?

VM을 만들 때 [지원 되는 운영 체제](backup-support-matrix-iaas.md#supported-backup-actions)를 실행 하는 vm에 대 한 백업을 사용 하도록 설정할 수 있습니다.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>백업 비용이 VM 비용에 포함 되나요?

아니요. 백업 비용은 VM의 비용과 별개입니다. [Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)에 대해 자세히 알아보세요.

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>VM에 대 한 백업을 사용 하도록 설정 하는 데 필요한 권한은 무엇입니까?

VM 참가자 인 경우 VM에서 백업을 사용 하도록 설정할 수 있습니다. 사용자 지정 역할을 사용 하는 경우 VM에서 백업을 사용 하도록 설정 하려면 다음 권한이 필요 합니다.

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Recovery Services 자격 증명 모음 및 VM에 다른 리소스 그룹이 있는 경우 Recovery Services 자격 증명 모음에 대 한 리소스 그룹에서 쓰기 권한이 있는지 확인 합니다.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>주문형 백업 작업은 예약된 백업과 동일한 보존 일정을 사용하나요?

아니요. 주문형 백업 작업의 보존 범위를 지정 합니다. 기본적으로 포털에서 트리거된 이후 30일 동안 유지됩니다.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>최근에 일부 VM에서 Azure Disk Encryption을 사용할 수 있습니다. 내 백업이 계속 작동하나요?

Azure Backup에서 Key Vault에 액세스할 수 있는 권한을 제공 합니다. [Azure Backup PowerShell](backup-azure-vms-automation.md) 설명서의 **백업 사용** 섹션에 설명된 대로 PowerShell에서 권한을 지정합니다.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM 디스크를 관리 디스크로 마이그레이션했습니다. 내 백업이 계속 작동하나요?

예. 백업은 원활하게 작동합니다. 아무 것도 다시 구성할 필요가 없습니다.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>백업 구성 마법사에서 내 VM을 볼 수 없는 이유는 무엇인가요?

이 마법사는 자격 증명 모음과 동일한 지역에 있고 아직 백업되지 않은 VM만 나열합니다.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>내 VM이 종료되었습니다. 요청 시 또는 예약된 백업 작업도 종료되나요?

예. 머신이 종료되면 백업이 실행됩니다. 복구 지점은 충돌 일치로 표시됩니다.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>진행 중인 백업 작업을 취소할 수 있나요?

예. **스냅숏 만들기** 상태에서 백업 작업을 취소할 수 있습니다. 스냅샷에서 데이터 전송이 진행 중인 경우 작업을 취소할 수 없습니다.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Azure Backup 서비스에서 만든 리소스 그룹 (예:)에 대 한 잠금을 사용 하도록 설정 했습니다 `AzureBackupRG_<geo>_<number>` . 내 백업이 계속 작동하나요?

Azure Backup 서비스에서 만든 리소스 그룹을 잠그는 경우 최대 18 개의 복원 지점이 있으므로 백업이 실패 하기 시작 합니다.

잠금을 제거 하 고 나중에 백업을 성공적으로 수행할 수 있도록 해당 리소스 그룹에서 복원 지점 컬렉션의 선택을 취소 합니다. 복원 지점 컬렉션을 제거 하려면 [다음 단계를 수행](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) 합니다.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>표준 SSD 관리 디스크를 지원할 Azure Backup 있나요?

예, Azure Backup [는 표준 SSD 관리 디스크](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)를 지원 합니다.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>WA(쓰기 가속기) 지원 디스크를 사용하여 VM을 백업할 수 있나요?

WA 지원 디스크에는 스냅샷을 만들 수 없습니다. 그러나 Azure Backup 서비스는 백업에서 WA 지원 디스크를 제외할 수 있습니다.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>WA(쓰기 가속기) 디스크를 사용하고 SAP HANA가 설치된 VM을 갖고 있습니다. 어떻게 백업해야 하나요?

Azure Backup은 WA 지원 디스크를 백업할 수 없지만 백업에서 제외할 수는 있습니다. 그러나 WA 지원 디스크의 정보가 백업되지 않으므로 백업하더라도 데이터베이스 일관성이 제공되지 않습니다. 운영 체제 디스크 백업 및 WA 미사용 디스크 백업을 원하는 경우 이 구성으로 디스크를 백업하면 됩니다.

Azure Backup는 RPO가 15 분인 SAP HANA 데이터베이스에 대 한 스트리밍 백업 솔루션을 제공 합니다. SAP에서 제공 하는 Backint는 SAP HANA의 기본 Api를 활용 하는 네이티브 백업 지원을 제공 합니다. [Azure vm에서 SAP HANA 데이터베이스를 백업](./sap-hana-db-about.md)하는 방법에 대해 자세히 알아보세요.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>내 VM 백업 정책에 설정 된 예약 된 백업 시간부터 백업 시작 시간에 예상 되는 최대 지연 수는 얼마 인가요?

예약 된 백업은 예약 된 백업 시간의 2 시간 이내에 트리거됩니다. 예를 들어 100 Vm의 백업 시작 시간이 2:00 오전 4:00 10 시에 실행 되는 경우 모든 100 Vm은 백업 작업을 진행 하 게 됩니다. 가동 중단 및 다시 시작 또는 다시 시도로 인해 예약 된 백업이 일시 중지 된 경우이 예약 된 2 시간 기간을 벗어나 백업을 시작할 수 있습니다.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>매일 백업 지점에 대해 허용 되는 최소 보존 범위는 무엇 인가요?

Azure 가상 머신 백업 정책은 7 일에서 최대 9999 일 까지의 최소 보존 범위를 지원 합니다. 7 일 이내에 기존 VM 백업 정책을 수정 하려면 7 일의 최소 보존 범위를 만족 하는 업데이트가 필요 합니다.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>내 VM 또는 내 VM 리소스 그룹의 이름에 대 한 대/소문자를 변경 하면 어떻게 되나요?

VM 또는 VM 리소스 그룹의 대/소문자를 변경 하는 경우 백업 항목 이름의 대/소문자가 변경 되지 않습니다. 그러나이 동작은 Azure Backup 동작으로 예상 됩니다. 사례 변경은 백업 항목에 표시 되지 않지만 백 엔드에서 업데이트 됩니다.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>VM에 연결 된 선택적 디스크를 백업 하거나 복원할 수 있나요?

이제 Azure Backup은 Azure Virtual Machine 백업 솔루션을 사용한 선택적 디스크 백업 및 복원을 지원합니다. 자세한 내용은 [Azure vm의 선택적 디스크 백업 및 복원](selective-disk-backup-restore.md)을 참조 하세요.

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>백업 하는 동안 테 넌 트 변경이 발생 하는 경우 관리 되는 id가 유지 되나요?

[테 넌 트가 변경](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection) 되 면 백업이 다시 작동 하도록 [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하지 않도록 설정 했다가 다시 사용 하도록 설정 해야 합니다.

## <a name="restore"></a>복원

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>디스크만 복원할지 아니면 전체 VM을 복원할지 결정하는 방법은 무엇인가요?

VM 복원은 Azure VM의 빠른 만들기 옵션이라고 생각하시면 됩니다. 이 옵션은 디스크 이름, 디스크에 사용 되는 컨테이너, 공용 IP 주소 및 네트워크 인터페이스 이름을 변경 합니다. VM이 만들어지면 변경 내용이 고유한 리소스를 유지합니다. VM은 가용성 집합에 추가되지 않습니다.

원할 경우 다음과 같이 디스크 복원 옵션을 사용할 수 있습니다.

- 생성되는 VM을 사용자 지정합니다. 예를 들어 크기를 변경 합니다.
- 백업 시에 없는 구성 설정을 추가 합니다.
- 생성되는 리소스의 명명 규칙을 제어합니다.
- 가용성 집합에 VM을 추가합니다.
- PowerShell 또는 템플릿을 사용하여 구성해야 하는 다른 설정을 추가합니다.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>관리 디스크로 업그레이드한 후 비관리형 VM 디스크의 백업을 복원할 수 있나요?

예, 디스크를 비관리형에서 관리형으로 마이그레이션하기 전에 만든 백업을 사용할 수 있습니다.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM이 관리 디스크로 마이그레이션되기 전에 VM을 복원 지점으로 복원하려면 어떻게 할까요?

복원 프로세스는 동일 하 게 유지 됩니다. VM이 관리 되지 않는 디스크를가지고 있는 경우 복구 지점은 관리 되지 않는 디스크로 [복원할](tutorial-restore-disk.md#unmanaged-disks-restore)수 있습니다. VM에 managed disks가 있는 경우 [디스크를 managed disks로 복원할](tutorial-restore-disk.md#managed-disk-restore)수 있습니다. 그런 다음 [해당 디스크에서 VM을 만들](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)수 있습니다.

PowerShell에서 이 작업을 수행하는 방법을 [자세히 알아보세요](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>복원 시 VM을 만들지 못할 경우 복원에 포함 된 디스크는 어떻게 되나요?

관리 VM 복원 시 VM을 만들 수 없는 경우에도 디스크는 계속 복원 됩니다.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>삭제 된 VM을 복원할 수 있나요?

예. VM을 삭제 하더라도 자격 증명 모음의 해당 백업 항목으로 이동 하 여 복구 지점에서 복원할 수 있습니다.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>VM을 동일한 가용성 집합으로 복원할 어떻게 할까요? 있나요?

관리 디스크 Azure Vm의 경우, 관리 디스크로 복원 하는 동안 템플릿에 옵션을 제공 하 여 가용성 집합에 대 한 복원을 사용 하도록 설정 합니다. 이 템플릿에 **가용성 집합**이라는 입력 매개 변수가 있습니다.

### <a name="how-do-we-get-faster-restore-performances"></a>복원 성능을 높이려면 어떻게 해야 하나요?

[즉시 복원](backup-instant-restore-capability.md) 기능을 사용 하면 스냅숏에 대 한 빠른 백업 및 즉시 복원을 수행할 수 있습니다.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>암호화 된 VM에 대 한 주요 자격 증명 모음 설정을 변경 하면 어떻게 되나요?

암호화 된 VM에 대 한 주요 자격 증명 모음 설정을 변경한 후에는 백업이 새로운 세부 정보 집합을 사용 하 여 계속 작동 합니다. 그러나 변경 전에 복구 지점에서 복원한 후에는 키 자격 증명 모음에서 암호를 복원 해야 VM을 만들 수 있습니다. 자세한 내용은 [이 문서](./backup-azure-restore-key-secret.md)를 참조하세요.

비밀/키 롤아웃 등의 작업에는이 단계가 필요 하지 않으며 복원 후 동일한 키 자격 증명 모음을 사용할 수 있습니다.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>도메인 컨트롤러와의 관계가 손상 된 VM으로 인해 복원 된 VM에 액세스할 수 있나요?

예, 도메인 컨트롤러와의 관계가 손상 된 VM으로 인해 복원 된 VM에 액세스 합니다. 자세한 내용은 관련 [문서](./backup-azure-arm-restore-vms.md#post-restore-steps)를 참조하세요.

## <a name="manage-vm-backups"></a>VM 백업 관리

### <a name="what-happens-if-i-modify-a-backup-policy"></a>백업 정책을 수정하면 어떻게 되나요?

VM은 수정된 정책 또는 새 정책의 일정 및 보존 설정을 사용하여 백업됩니다.

- 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 표시되고 유지됩니다.
- 보존 기간을 줄이면 복구 지점이 다음 정리 작업에서 정리하도록 표시되고, 결국 삭제됩니다.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup에서 백업한 VM을 다른 리소스 그룹으로 이동하려면 어떻게 할까요?

1. 백업을 일시적으로 중지 하 고 백업 데이터를 보존 합니다.
2. Azure Backup로 구성 된 가상 컴퓨터를 이동 하려면 다음 단계를 수행 합니다.

   1. 가상 컴퓨터의 위치를 찾습니다.
   2. 다음 명명 패턴을 사용 하 여 리소스 그룹을 `AzureBackupRG_<location of your VM>_1` 찾습니다. 예를 들어 *AzureBackupRG_westus2_1*
   3. Azure Portal에서 **숨겨진 형식 표시**를 선택 합니다.
   4. 이름 지정 패턴이 있는 **restorePointCollections/** 형식의 리소스를 찾습니다 `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
   6. 삭제 작업을 완료 한 후 가상 컴퓨터를 이동할 수 있습니다.

3. VM을 대상 리소스 그룹으로 이동합니다.
4. 백업을 다시 시작 합니다.

이동 작업 전에 만든 사용 가능한 복원 지점에서 VM을 복원할 수 있습니다.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>VM을 다른 리소스 그룹으로 이동한 후에는 어떻게 되나요?

VM이 다른 리소스 그룹으로 이동 되 면 Azure Backup 관련 된 새 VM이 됩니다.

VM을 새 리소스 그룹으로 이동한 후 동일한 자격 증명 모음이 나 다른 자격 증명 모음에서 VM을 다시 보호할 수 있습니다. Azure Backup에 대 한 새 VM 이므로 별도로 청구 됩니다.

필요한 경우 이전 VM의 복원 지점이 복원에 사용 될 수 있습니다. 이 백업 데이터가 필요 하지 않은 경우 데이터 삭제로 이전 VM 보호를 중지할 수 있습니다.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>동일한 백업 정책에 연결할 수 있는 Vm 수에 제한이 있나요?

예, 포털에서 동일한 백업 정책에 연결할 수 있는 Vm은 100 개로 제한 됩니다. Vm이 100 개 이상인 경우 동일한 일정 또는 다른 일정으로 여러 백업 정책을 만듭니다.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>내 백업에 대 한 보존 설정은 어떻게 볼 수 있나요?

현재 VM에 할당 된 백업 정책에 따라 VM (백업 항목) 수준에서 보존 설정을 볼 수 있습니다.

백업에 대 한 보존 설정을 확인 하는 한 가지 방법은 Azure Portal에서 VM에 대 한 백업 항목 [대시보드로](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) 이동 하는 것입니다. 해당 백업 정책에 대 한 링크를 클릭 하면 VM에 연결 된 매일, 매주, 매월 및 매년 보존 지점의 보존 기간을 볼 수 있습니다.

또한 [Backup 탐색기](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) 를 사용 하 여 단일 창에 있는 모든 vm의 보존 설정을 볼 수 있습니다. 모든 Recovery Services 자격 증명 모음에서 Backup 탐색기로 이동 하 고, **백업 항목** 탭으로 이동 하 고, 고급 보기를 선택 하 여 각 VM에 대 한 자세한 보존 정보를 확인 합니다.
