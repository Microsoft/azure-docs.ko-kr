---
title: Microsoft Azure Recovery Services 자격 증명 모음 삭제
description: 이 문서에서는 종속성을 제거 하 고 MARS (Microsoft Azure Backup Recovery Services) 자격 증명 모음을 삭제 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 23e0d2874229616037c44800639411f66bc4d1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173183"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services 자격 증명 모음 삭제

이 문서에서는 MARS (Microsoft [Azure Backup](backup-overview.md) Recovery Services) 자격 증명 모음을 삭제 하는 방법을 설명 합니다. 종속성을 제거한 후 자격 증명 모음을 삭제 하는 방법에 대한 지침이 포함 되어 있습니다.

## <a name="before-you-start"></a>시작하기 전에

보호 된 서버 또는 백업 관리 서버와 연결 된 종속성이 있는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다.

- 백업 데이터를 포함 하는 자격 증명 모음은 삭제할 수 없습니다 (보호를 중지 했지만 백업 데이터를 유지 한 경우에도).

- 종속성을 포함 하는 자격 증명 모음을 삭제 하면 다음과 같은 메시지가 나타납니다.

  ![자격 증명 모음 오류를 삭제 합니다.](./media/backup-azure-delete-vault/error.png)

- 종속성이 포함 된 포털에서 온-프레미스로 보호 된 항목을 삭제 하면 다음과 같은 경고 메시지가 나타납니다.

  ![보호 된 서버 오류를 삭제 합니다.](./media/backup-azure-delete-vault/error-message.jpg)

- 백업 항목이 일시 삭제 됨 상태에 있는 경우 경고 메시지가 표시 되 고 영구적으로 삭제 될 때까지 기다려야 합니다. 자세한 내용은 이 [문서](https://aka.ms/SoftDeleteCloudWorkloads)를 참조하세요.

   ![자격 증명 모음 오류를 삭제 합니다.](./media/backup-azure-delete-vault/error-message-soft-delete.png)
  
자격 증명 모음을 삭제 하려면 설치와 일치 하는 시나리오를 선택 하 고 권장 단계를 따릅니다.

시나리오 | 자격 증명 모음을 삭제 하기 위한 종속성을 제거 하는 단계 |
-- | --
Azure Backup 에이전트를 사용 하 여 보호 된 온-프레미스 파일 및 폴더를 사용 하 여 Azure에 백업 | [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console) 의 단계를 수행 합니다.
MABS (Microsoft Azure Backup 서버) 또는 DPM (System Center Data Protection Manager)을 사용 하 여 보호 되는 온-프레미스 컴퓨터가 Azure에 있습니다. | [MABS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-management-console) 의 단계를 수행 합니다.
클라우드에서 항목을 보호 합니다 (예: laaS 가상 머신 또는 Azure Files 공유).  | [클라우드에서 보호 된 항목 삭제](#delete-protected-items-in-the-cloud) 의 단계를 수행 합니다.
온-프레미스와 클라우드에서 항목을 보호 했습니다. | 다음 섹션의 모든 단계를 다음 순서 대로 수행 합니다. <br> 1. [클라우드에서 보호 된 항목을 삭제](#delete-protected-items-in-the-cloud) 합니다.<br> 2. [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console) <br> 3. [MABS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-management-console)
온-프레미스 또는 클라우드에 보호 된 항목이 없습니다. 그러나 여전히 자격 증명 모음 삭제 오류가 발생 합니다. | 을 [사용 하 여 Recovery Services 자격 증명 모음 삭제](#delete-the-recovery-services-vault-by-using-azure-resource-manager) 의 단계를 수행 Azure Resource Manager

## <a name="delete-protected-items-in-the-cloud"></a>클라우드에서 보호 된 항목 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해 하려면 **[시작 하기 전에](#before-you-start)** 섹션을 참조 하세요.

보호를 중지 하 고 백업 데이터를 삭제 하려면 다음 단계를 수행 합니다.

1. 포털에서 **Recovery Services 자격 증명 모음**으로 이동한 다음 **백업 항목**으로 이동 합니다. 그런 다음 클라우드에서 보호 된 항목을 선택 합니다 (예: Azure Virtual Machines, Azure Storage [Azure Files service] 또는 Azure Virtual Machines에서 SQL Server).

    ![백업 유형을 선택 합니다.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 마우스 오른쪽 단추를 클릭 하 여 백업 항목을 선택 합니다. 백업 항목이 보호 되었는지 여부에 따라 메뉴에 **백업 중지** 창이 나 **백업 데이터 삭제** 창이 표시 됩니다.

    - **백업 중지** 창이 표시 되 면 드롭다운 메뉴에서 **백업 데이터 삭제** 를 선택 합니다. 백업 항목의 이름 (이 필드는 대/소문자 구분)을 입력 한 다음 드롭다운 메뉴에서 이유를 선택 합니다. 설명 (있는 경우)을 입력 합니다. 그런 다음 **백업 중지**를 선택 합니다.

        ![백업 중지 창](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **백업 데이터 삭제** 창이 나타나면 백업 항목의 이름을 입력 하 고 (이 필드는 대/소문자를 구분 함) 드롭다운 메뉴에서 이유를 선택 합니다. 설명 (있는 경우)을 입력 합니다. 그런 다음, **삭제**를 선택합니다.

         ![백업 데이터 삭제 창](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 알림 아이콘 **![** 알림 아이콘을 선택 합니다.](./media/backup-azure-delete-vault/messages.png) 프로세스가 완료 되 면 서비스에서 "백업 항목 *"* 의 백업 *및 백업 데이터 삭제를 중지 하*는 다음 메시지를 표시 합니다. *작업을 완료 했습니다*.
4. **백업 항목 메뉴에서** **새로 고침** 을 선택 하 여 백업 항목이 삭제 되었는지 확인 합니다.

      ![백업 항목 삭제 페이지.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>온-프레미스에서 보호 된 항목 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해 하려면 **[시작 하기 전에](#before-you-start)** 섹션을 참조 하세요.

1. 자격 증명 모음 대시보드 메뉴에서 **백업 인프라**를 선택 합니다.
2. 온-프레미스 시나리오에 따라 다음 옵션 중 하나를 선택 합니다.

      - MARS의 경우 **보호 된 서버** 를 선택한 다음 **에이전트를 Azure Backup**합니다. 그런 다음 삭제 하려는 서버를 선택 합니다.

        ![MARS의 경우 자격 증명 모음을 선택 하 여 해당 대시보드를 엽니다.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS 또는 DPM의 경우 **백업 관리 서버**를 선택 합니다. 그런 다음 삭제 하려는 서버를 선택 합니다.

          ![MABS의 경우 자격 증명 모음을 선택 하 여 해당 대시보드를 엽니다.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **삭제** 창에 경고 메시지가 표시 됩니다.

     ![삭제 창입니다.](./media/backup-azure-delete-vault/delete-protected-server.png)

     경고 메시지와 동의 확인란의 지침을 검토 합니다.
    > [!NOTE]
    >- 보호 된 서버가 Azure 서비스와 동기화 되 고 백업 항목이 있는 경우 동의 확인란은 종속 된 백업 항목 수와 백업 항목을 볼 수 있는 링크를 표시 합니다.
    >- 보호 된 서버가 Azure 서비스와 동기화 되지 않고 백업 항목이 있는 경우 동의 확인란은 백업 항목만 표시 합니다.
    >- 백업 항목이 없으면 동의 확인란은 삭제를 요청 합니다.

4. 동의 확인란을 선택 하 고 **삭제**를 선택 합니다.

5. **알림** 아이콘을 확인 하 ![백업 데이터](./media/backup-azure-delete-vault/messages.png)삭제 합니다. 작업이 완료 되 면 서비스는 *백업 중지 및 "백업 항목에 대한 백업 데이터 삭제"* 메시지를 표시 합니다. *작업을 완료 했습니다*.
6. **백업 항목 메뉴에서** **새로 고침** 을 선택 하 여 백업 항목이 삭제 되었는지 확인 합니다.

이 프로세스가 완료 되 면 관리 콘솔에서 백업 항목을 삭제할 수 있습니다.

- [MARS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mars-management-console)
- [MABS 관리 콘솔에서 백업 항목 삭제](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS 관리 콘솔에서 백업 항목 삭제

1. MARS 관리 콘솔을 열고 **작업** 창으로 이동 하 여 **Backup 예약**을 선택 합니다.
2. 예약 된 **백업 수정 또는 중지** 페이지에서 **이 백업 일정 사용 중지와 저장 된 백업 모두 삭제**를 선택 합니다. 그다음에 **다음**을 선택합니다.

    ![예약 된 백업 수정 또는 중지](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 예약 된 **백업 중지** 페이지에서 **마침**을 선택 합니다.

    ![예약 된 백업을 중지 합니다.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 수동으로 생성 해야 하는 보안 PIN (개인 식별 번호)을 입력 하 라는 메시지가 표시 됩니다. 이렇게 하려면 먼저 Azure Portal에 로그인 합니다.
5. **Recovery Services 자격 증명 모음** > **설정** > **속성**으로 이동 합니다.
6. **보안 PIN**아래에서 **생성**을 선택 합니다. 이 PIN을 복사 합니다. PIN은 5 분 동안만 유효 합니다.
7. 관리 콘솔에서 PIN을 붙여넣은 다음 **확인**을 선택 합니다.

    ![보안 PIN을 생성 합니다.](./media/backup-azure-delete-vault/security-pin.png)

8. **백업 변경 진행률** 페이지에 다음 메시지가 표시 됩니다. 삭제 된 *백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다.*  

    ![백업 인프라를 삭제 합니다.](./media/backup-azure-delete-vault/deleted-backup-data.png)

온-프레미스 백업 항목을 삭제 한 후 포털에서 다음 단계를 수행 합니다.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>MABS 관리 콘솔에서 백업 항목 삭제

MABS 관리 콘솔에서 백업 항목을 삭제 하는 데 사용할 수 있는 방법에는 두 가지가 있습니다.

#### <a name="method-1"></a>방법 1

보호를 중지 하 고 백업 데이터를 삭제 하려면 다음 단계를 수행 합니다.

1. DPM 관리자 콘솔 열고 탐색 모음에서 **보호** 를 선택 합니다.
2. 디스플레이 창에서 제거할 보호 그룹 구성원을 선택 합니다. **그룹 구성원의 보호 중지** 옵션을 마우스 오른쪽 단추로 클릭 하 여 선택 합니다.
3. **보호 중지** 대화 상자에서 **보호 된 데이터 삭제**를 선택 하 고 **저장소를 온라인으로 삭제** 확인란을 선택 합니다. 그런 다음 **보호 중지**를 선택 합니다.

    ![보호 중지 창에서 보호 된 데이터 삭제를 선택 합니다.](./media/backup-azure-delete-vault/delete-storage-online.png)

    보호 된 구성원 상태가 *비활성 복제본 사용 가능*으로 변경 됩니다.

4. 비활성 보호 그룹을 마우스 오른쪽 단추로 클릭 하 고 **비활성 보호 제거**를 선택 합니다.

    ![비활성 보호를 제거 합니다.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **비활성 보호 삭제** 창에서 **온라인 저장소 삭제** 확인란을 선택 하 고 **확인**을 선택 합니다.

    ![온라인 저장소를 삭제 합니다.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>방법 2

**Mabs 관리** 콘솔을 엽니다. **데이터 보호 방법 선택**아래에서 **온라인 보호를 사용할** 확인란의 선택을 취소 합니다.

  ![데이터 보호 방법을 선택합니다.](./media/backup-azure-delete-vault/data-protection-method.png)

온-프레미스 백업 항목을 삭제 한 후 포털에서 다음 단계를 수행 합니다.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음 삭제

1. 모든 종속성이 제거 되 면 자격 증명 모음 메뉴의 **Essentials** 창으로 스크롤합니다.
2. 백업 항목, 백업 관리 서버 또는 복제 된 항목이 나열 되어 있지 않은지 확인 합니다. 항목이 자격 증명 모음에 계속 표시 되는 경우 [시작 하기 전에](#before-you-start) 섹션을 참조 하세요.

3. 자격 증명 모음에 더 이상 항목이 없으면 자격 증명 모음 대시보드에서 **삭제** 를 선택 합니다.

    ![자격 증명 모음 대시보드에서 삭제를 선택 합니다.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. **예** 를 선택 하 여 자격 증명 모음을 삭제할 것인지 확인 합니다. 자격 증명 모음이 삭제 됩니다. 포털이 **새** 서비스 메뉴로 돌아갑니다.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>PowerShell을 사용 하 여 Recovery Services 자격 증명 모음 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해 하려면 **[시작 하기 전에](#before-you-start)** 섹션을 참조 하세요.

보호를 중지 하 고 백업 데이터를 삭제 하려면:

- Azure Vm 백업에서 SQL을 사용 하 고 SQL 인스턴스에 대해 자동 보호를 사용 하도록 설정한 경우 먼저 자동 보호를 사용 하지 않도록 설정 합니다.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Azure Backup 보호 된 항목에 대해 보호를 사용 하지 않도록 설정 하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) .

- 클라우드의 모든 백업 보호 항목에 대한 보호를 중지 하 고 데이터를 삭제 합니다 (예: laaS VM, Azure 파일 공유 등):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    에 대한 [자세한](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) 내용은 백업 보호 된 항목에 대한 보호 사용 안 함을 .

- Azure에 백업 하는 MARS (Azure Backup 에이전트)를 사용 하 여 보호 되는 온-프레미스 파일 및 폴더의 경우 다음 PowerShell 명령을 사용 하 여 각 MARS PowerShell 모듈에서 백업 된 데이터를 삭제 합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    다음 프롬프트가 표시 되는 게시:

    *이 백업 정책을 제거 하 시겠습니까 Microsoft Azure Backup? 삭제 된 백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다. <br/> [Y] 예 [A] 예: 모두 [N] 아니요 [L] no to All [S] 일시 중단 [?] 도움말 (기본값: "Y"):*

- MABS (Microsoft Azure Backup Server) 또는 DPM에서 Azure로 보호 되는 온-프레미스 컴퓨터 (System Center Data Protection Manager)에서 다음 명령을 사용 하 여 Azure에서 백업 된 데이터를 삭제 합니다.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    다음 프롬프트가 표시 되는 게시:

   *Microsoft Azure Backup* 이 백업 정책을 제거 하 시겠습니까? 삭제 된 백업 데이터는 14 일 동안 보존 됩니다. 이 시간 후에는 백업 데이터가 영구적으로 삭제 됩니다. <br/>
   [Y] 예 [A] 예 [N] 아니요 [S] 아니요 모두 [S] 일시 중지 [?] 도움말 (기본값은 "Y"):*

백업 된 데이터를 삭제 한 후 온-프레미스 컨테이너와 관리 서버의 등록을 취소 합니다.

- MARS (Azure Backup 에이전트)를 사용 하 여 보호 되는 온-프레미스 파일 및 폴더는 Azure에 백업 합니다.

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    자격 증명 모음에서 Windows Server 또는 기타 컨테이너를 등록 취소 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) .

- MABS (Microsoft Azure Backup 서버) 또는 DPM을 사용 하 여 보호 되는 온-프레미스 컴퓨터 (System Center Data Protection 관리:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    자격 증명 모음에서 백업 관리 컨테이너를 등록 취소 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) .

백업 된 데이터를 영구적으로 삭제 하 고 모든 컨테이너의 등록을 취소 한 후 자격 증명 모음 삭제를 계속 합니다.

Recovery Services 자격 증명 모음 삭제하려면

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Recovery services 자격 증명 모음 삭제에 [대해 자세히 알아보세요](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) .

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLI를 사용 하 여 Recovery Services 자격 증명 모음 삭제

먼저 종속성 및 자격 증명 모음 삭제 프로세스를 이해 하려면 **[시작 하기 전에](#before-you-start)** 섹션을 참조 하세요.

> [!NOTE]
> 현재 Azure Backup CLI는 Azure VM 백업만 관리 하도록 지원 하므로 자격 증명 모음을 삭제 하는 다음 명령은 자격 증명 모음에 Azure VM 백업이 포함 된 경우에만 작동 합니다. 자격 증명 모음에 Azure Vm 이외의 형식의 백업 항목이 포함 된 경우 Azure Backup CLI를 사용 하 여 자격 증명 모음을 삭제할 수 없습니다.

기존 Recovery services 자격 증명 모음을 삭제 하려면 다음을 수행 합니다.

- 보호를 중지 하 고 백업 데이터를 삭제 하려면

    ```CLI
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    자세한 내용은이 [문서](/cli/azure/backup/protection#az-backup-protection-disable)를 참조 하세요.

- 기존 Recovery services 자격 증명 모음을 삭제 합니다.

    ```CLI
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    자세한 내용은이 [문서](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) 를 참조 하세요.

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Azure Resource Manager을 사용 하 여 Recovery Services 자격 증명 모음을 삭제 합니다.

Recovery Services 자격 증명 모음을 삭제 하는이 옵션은 모든 종속성이 제거 되 고 *자격 증명 모음 삭제 오류가 계속 발생*하는 경우에만 권장 됩니다. 다음 팁 중 하나 또는 모두를 시도 합니다.

- 자격 증명 모음 메뉴의 **Essentials** 창에서 백업 항목, 백업 관리 서버 또는 복제 된 항목이 나열 되지 않는지 확인 합니다. 백업 항목이 있는 경우 [시작 하기 전에](#before-you-start) 섹션을 참조 하세요.
- [포털에서 자격 증명 모음을 다시 삭제](#delete-the-recovery-services-vault) 해 보세요.
- 모든 종속성이 제거 되 고 *자격 증명 모음 삭제 오류가 계속 발생*하는 경우 ARMClient 도구를 사용 하 여 다음 단계 (참고 후)를 수행 합니다.

1. [Chocolatey.org](https://chocolatey.org/) 으로 이동 하 여 chocolatey를 다운로드 하 고 설치 합니다. 그런 다음, 다음 명령을 실행 하 여 ARMClient를 설치 합니다.

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure 계정에 로그인 하 고 다음 명령을 실행 합니다.

    `ARMClient.exe login [environment name]`

3. Azure Portal에서 삭제 하려는 자격 증명 모음에 대한 구독 ID 및 리소스 그룹 이름을 수집 합니다.

ARMClient 명령에 대한 자세한 내용은 [ARMCLIENT 추가](https://github.com/projectkudu/ARMClient/blob/master/README.md)정보를 참조 하세요.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 삭제

1. 구독 ID, 리소스 그룹 이름 및 자격 증명 모음 이름을 사용 하 여 다음 명령을 실행 합니다. 종속성이 없는 경우 다음 명령을 실행 하면 자격 증명 모음이 삭제 됩니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. 자격 증명 모음이 비어 있지 않으면 다음 오류 메시지가 표시 됩니다. 자격 증명 모음 *에 기존 리소스가 있으므로 자격 증명 모음을 삭제할 수 없습니다* . 자격 증명 모음 내에서 보호 된 항목 또는 컨테이너를 제거 하려면 다음 명령을 실행 합니다.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure Portal에서 자격 증명 모음이 삭제 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[Recovery Services 자격 증명 모음에 대한 자세한 정보](backup-azure-recovery-services-vault-overview.md)<br/>
[Recovery Services 자격 증명 모음 모니터링 및 관리에 대한 자세한 정보](backup-azure-manage-windows-server.md)
