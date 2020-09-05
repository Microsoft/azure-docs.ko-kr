---
title: Azure VM에서 SQL Server 데이터베이스 복원
description: 이 문서에서는 Azure VM에서 실행 되 고 Azure Backup을 사용 하 여 백업 되는 SQL Server 데이터베이스를 복원 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 682540e498c7531777032b5375f0105c03ce4ec6
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826559"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM에서 SQL Server 데이터베이스 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스가 Azure Backup Recovery Services 자격 증명 모음에 백업 된 Azure VM (가상 컴퓨터)에서 실행 되는 SQL Server 데이터베이스를 복원 하는 방법을 설명 합니다.

이 문서에서는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다. 자세한 내용은 [Azure vm에서 SQL Server 데이터베이스 백업](backup-azure-sql-database.md)을 참조 하세요.

## <a name="restore-to-a-time-or-a-recovery-point"></a>시간 또는 복구 지점으로 복원

다음과 같이 Azure Vm에서 실행 되는 SQL Server 데이터베이스를 복원할 수 Azure Backup.

- 트랜잭션 로그 백업을 사용 하 여 특정 날짜 또는 시간 (초)으로 복원 합니다. Azure Backup은 선택 된 시간에 따라 복원 하는 데 필요한 적절 한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정 합니다.
- 특정 복구 지점으로 복원 하기 위해 특정 전체 또는 차등 백업을 복원 합니다.

## <a name="prerequisites"></a>사전 준비 사항

데이터베이스를 복원 하기 전에 다음 사항에 유의 하십시오.

- 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다.
- 대상 서버를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
- TDE로 암호화 된 데이터베이스를 다른 SQL Server 복원 하려면 먼저 [대상 서버에 인증서를 복원](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)해야 합니다.
- [CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-ver15) 사용 데이터베이스는 [파일로 복원](#restore-as-files) 옵션을 사용 하 여 복원 해야 합니다.
- "Master" 데이터베이스를 복원 하기 전에 시작 옵션인 **-m AzureWorkloadBackup**을 사용 하 여 SQL Server 인스턴스를 단일 사용자 모드로 시작 합니다.
  - **-M** 에 대 한 값은 클라이언트 이름입니다.
  - 지정 된 클라이언트 이름만 연결을 열 수 있습니다.
- 모든 시스템 데이터베이스 (모델, master, msdb)에 대해 복원을 트리거하기 전에 SQL Server 에이전트 서비스를 중지 합니다.
- 이러한 데이터베이스에 대 한 연결을 시도할 수 있는 응용 프로그램을 모두 닫습니다.
- 서버에서 여러 인스턴스를 실행 하는 경우 모든 인스턴스를 실행 해야 합니다. 그렇지 않으면 데이터베이스를 복원할 대상 서버 목록에 서버가 나타나지 않습니다.

## <a name="restore-a-database"></a>데이터베이스 복원

복원 하려면 다음 권한이 필요 합니다.

- 복원을 수행 하는 자격 증명 모음의 **Backup Operator** 권한입니다.
- 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
- 대상 VM에 대한 **참가자(쓰기)** 액세스 권한
  - 동일한 VM으로 복원 하는 경우이 VM은 원본 VM입니다.
  - 대체 위치로 복원 하는 경우 새 대상 VM이 됩니다.

다음과 같이 복원합니다.

1. SQL Server VM이 등록된 자격 증명 모음을 엽니다.
2. 자격 증명 모음 대시보드의 **사용 현황** 아래에서 **백업 항목**을 선택합니다.
3. **백업 항목**의 **백업 관리 유형**에서 **Azure VM의 SQL**을 선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 복원할 데이터베이스를 선택합니다.

    ![복원할 데이터베이스를 선택합니다.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다.

    - 가장 오래된 복원 지점 및 최신 복원 지점
    - 전체 및 대량 로그 복구 모드 이며 트랜잭션 로그 백업용으로 구성 된 데이터베이스의 경우 지난 24 시간 동안의 로그 백업 상태입니다.

6. **복원**을 선택합니다.

    ![복원 선택](./media/backup-azure-sql-database/restore-db.png)

7. **복원 구성**에서 데이터를 복원할 위치 (또는 방법)를 지정 합니다.
   - **대체 위치**: 데이터베이스를 대체 위치로 복원 하 고 원래 원본 데이터베이스를 유지 합니다.
   - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

        > [!IMPORTANT]
        > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. **대체 위치**만 사용할 수 있습니다.
        >
   - **파일로 복원**: 데이터베이스로 복원 하는 대신 SQL Server Management Studio를 사용 하 여 파일이 있는 컴퓨터에서 나중에 데이터베이스로 복구할 수 있는 백업 파일을 복원 합니다.
     ![복원 구성 메뉴](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원 위치**에서 **대체 위치**를 선택 합니다.
1. 데이터베이스를 복원하려는 SQL Server 이름 및 인스턴스를 선택합니다.
1. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
1. 해당 하는 경우 **선택한 SQL 인스턴스에 동일한 이름의 DB가 이미 있는 경우 덮어쓰기**를 선택 합니다.
1. **복원 지점**을 선택 하 고 [특정 시점으로 복원할지](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원할지](#restore-to-a-specific-restore-point)를 선택 합니다.

    ![복원 지점 선택](./media/backup-azure-sql-database/select-restore-point.png)

    ![특정 시점으로 복원](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. **고급 구성** 메뉴에서:

    - 복원 후 데이터베이스 nonoperational을 유지 하려면 **restore WITH NORECOVERY**를 사용 하도록 설정 합니다.
    - 대상 서버에서 복원 위치를 변경 하려면 새 대상 경로를 입력 합니다.

        ![대상 경로 입력](./media/backup-azure-sql-database/target-paths.png)

1. **확인** 을 클릭 하 여 복원을 트리거합니다. **알림** 영역에서 복원 진행률을 추적 하거나, 자격 증명 모음의 **백업 작업** 보기에서 추적 합니다.

    > [!NOTE]
    > 지정 시간 복원은 전체 및 대량 로그 복구 모드에 있는 데이터베이스의 로그 백업에 대해서만 사용할 수 있습니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원 위치**에서 **DB로 덮어쓰기**  >  **확인**을 선택 합니다.

    ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **복원 지점 선택**에서 **로그 (지정 시간)** 를 선택 하 여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 & 차등** 을 선택 하 여 [특정 복구 지점](#restore-to-a-specific-restore-point)으로 복원 합니다.

    > [!NOTE]
    > 지정 시간 복원은 전체 및 대량 로그 복구 모드에 있는 데이터베이스의 로그 백업에 대해서만 사용할 수 있습니다.

### <a name="restore-as-files"></a>파일로 복원

데이터베이스 대신 .bak 파일로 백업 데이터를 복원 하려면 **파일로 복원**을 선택 합니다. 지정 된 경로에 파일을 덤프 한 후에는 이러한 파일을 데이터베이스로 복원 하려는 모든 컴퓨터로 가져올 수 있습니다. 이러한 파일을 컴퓨터로 이동할 수 있으므로 이제 구독과 지역 간에 데이터를 복원할 수 있습니다.

1. **복원 위치 및 방법**에서 **파일로 복원**을 선택 합니다.
1. 백업 파일을 복원 하려는 SQL Server 이름을 선택 합니다.
1. **서버의 대상 경로** 에서 2 단계에서 선택한 서버의 폴더 경로를 입력 합니다. 서비스에서 필요한 모든 백업 파일을 덤프 하는 위치입니다. 일반적으로 네트워크 공유 경로 또는 탑재된 Azure 파일 공유 경로가 대상 경로로 지정되면 이를 통해 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재된 다른 컴퓨터에서 이러한 파일에 더 쉽게 액세스할 수 있습니다.<BR>

    >대상으로 등록 된 VM에 탑재 된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원 하려면 NT 권한 없음이 파일 공유에 액세스할 수 있는지 확인 합니다. 아래 지정 된 단계를 수행 하 여 VM에 탑재 된 AFS에 대 한 읽기/쓰기 권한을 부여할 수 있습니다.
    >
    >- `PsExec -s cmd`을 실행 하 여 NT 권한 없는 셸에 입력
    >   - `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` 실행
    >   - 액세스 권한 확인 `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- 백업 자격 증명 모음에서 경로로 복원 파일을 시작 합니다. `\\<storageacct>.file.core.windows.net\<filesharename>`<BR>
    [Sysinternals](/sysinternals/downloads/psexec) 페이지에서 PsExec를 다운로드할 수 있습니다.

1. **확인**을 선택합니다.

    ![파일로 복원 선택](./media/backup-azure-sql-database/restore-as-files.png)

1. **복원 지점**을 선택 하 고 [특정 시점으로 복원할지](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원할지](#restore-to-a-specific-restore-point)를 선택 합니다.

1. 선택한 복구 지점과 연결 된 모든 백업 파일은 대상 경로로 덤프 됩니다. SQL Server Management Studio 사용 하 여 있는 모든 컴퓨터의 데이터베이스로 파일을 복원할 수 있습니다.

    ![대상 경로에서 백업 파일 복원 됨](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1. **복원 날짜/시간**에서 달력을 엽니다. 달력에서 복구 지점이 있는 날짜는 굵은 형식으로 표시 되 고 현재 날짜는 강조 표시 됩니다.
1. 복구 지점이 있는 날짜를 선택 합니다. 복구 지점이 없는 날짜는 선택할 수 없습니다.

    ![일정 열기](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.
1. 시간 표시 막대 그래프에서 복구 시간을 지정 하거나 시간을 선택 합니다. 그런 다음, **확인**을 선택합니다.

### <a name="restore-to-a-specific-restore-point"></a>특정 복원 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택하고 **확인**을 선택하여 복원 지점 절차를 완료합니다.

    ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > 기본적으로 최근 30 일 동안의 복구 지점이 표시 됩니다. **필터** 를 클릭 하 고 사용자 지정 범위를 선택 하 여 30 일 보다 오래 된 복구 지점은 표시할 수 있습니다.

### <a name="restore-databases-with-large-number-of-files"></a>많은 수의 파일을 포함 하는 데이터베이스 복원

데이터베이스에 있는 파일의 전체 문자열 크기가 [특정 한도](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)보다 큰 경우 Azure Backup는 데이터베이스 파일 목록을 다른 pit 구성 요소에 저장 하므로 복원 작업 중에 대상 복원 경로를 설정할 수 없습니다. 대신 파일이 SQL 기본 경로로 복원 됩니다.

  ![대량 파일을 사용 하 여 데이터베이스 복원](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>다음 단계

[관리 및 모니터링](manage-monitor-sql-database-backup.md) Azure Backup에서 백업한 데이터베이스를 SQL Server 합니다.
