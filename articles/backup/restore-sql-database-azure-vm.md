---
title: Azure Backup을 사용 하 여 Azure VM에서 SQL Server 데이터베이스 백업 복원 | Microsoft Docs
description: 이 문서에서는 Azure VM에서 실행 되는 Azure Backup으로 백업 하는 SQL Server 데이터베이스를 복원 하는 방법을 설명 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: raynew
ms.openlocfilehash: 1712e46494796e563c26316b4f45d968872c304f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781806"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM에서 SQL Server 데이터베이스 복원

이 문서에서는 Azure 가상 머신 (VM)에서 실행 되는 SQL Server 데이터베이스를 복원 하는 방법을 설명 하는 합니다 [Azure Backup](backup-overview.md) 서비스에서 Azure Backup Recovery Services 자격 증명 모음을 백업 했습니다.

이 문서에서는 SQL Server 데이터베이스를 복원하는 방법을 설명합니다. 자세한 내용은 [Azure Vm에서 SQL Server 데이터베이스 백업](backup-azure-sql-database.md)합니다.

## <a name="restore-to-a-time-or-a-recovery-point"></a>한 번 또는 복구 지점으로 복원

Azure Backup 다음과 같이 Azure Vm에서 실행 되는 SQL Server 데이터베이스를 복원할 수 있습니다.

- 트랜잭션 로그 백업을 사용 하 여 특정 날짜 또는 시간 (초)을 복원 합니다. 선택한 시간을 기준으로 복원 하는 데 필요한 로그 백업 체인 및 azure Backup은 자동으로 적절 한 전체 차등 백업 결정 합니다.
- 특정 복구 지점으로 복원 하려면 특정을 전체 또는 차등 백업을 복원 합니다.


## <a name="prerequisites"></a>필수 조건

데이터베이스를 복원 하기 전에 다음 note:

- 동일한 Azure 지역의 SQL Server 인스턴스에 데이터베이스를 복원할 수 있습니다.
- 대상 서버를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
- 다른 SQL Server로 TDE로 암호화 된 데이터베이스를 복원 하려면 먼저 [대상 서버에 인증서를 복원](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)합니다.
- "Master" 데이터베이스를 복원 하기 전에 시작 SQL Server 인스턴스를 단일 사용자 모드로 시작 옵션을 사용 하 여 **-m AzureWorkloadBackup**합니다.
    - 에 대 한 값 **-m** 클라이언트의 이름입니다.
    - 지정 된 클라이언트 이름만 연결을 열 수 있습니다.
- 모든 시스템 데이터베이스 (모델, master, msdb)에 대 한 복원을 트리거하기 전에 SQL Server 에이전트 서비스를 중지 합니다.
- 이러한 데이터베이스 중 하나에 대 한 연결을 수행 하려고 할 수 있습니다 하는 모든 응용 프로그램을 닫습니다.

## <a name="restore-a-database"></a>데이터베이스 복원

를 복원 하려면 다음 권한이 필요 합니다.

* **Backup 운영자** 복원 하면 자격 증명 모음에 대 한 사용 권한.
* 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
* 대상 VM에 대한 **참가자(쓰기)** 액세스 권한
    - 동일한 VM에서 복원 하는 경우 원본 VM입니다.
    - 에서 대체 위치로 복원 하는 경우 새 대상 VM입니다.

다음과 같이 복원합니다.
1. SQL Server VM이 등록된 자격 증명 모음을 엽니다.
2. 자격 증명 모음 대시보드의 **사용 현황** 아래에서 **백업 항목**을 선택합니다.
3. **백업 항목**의 **백업 관리 유형**에서 **Azure VM의 SQL**을 선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 복원할 데이터베이스를 선택합니다.

    ![복원할 데이터베이스를 선택합니다.](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 데이터베이스 메뉴를 검토합니다. 다음과 같은 데이터베이스 백업에 대한 정보를 제공합니다.

    * 가장 오래된 복원 지점 및 최신 복원 지점
    * 트랜잭션 로그 백업에 대 한 전체 및 대량 로그 복구 모드에 있는 데이터베이스에 대 한 지난 24 시간 동안의 로그 백업 상태를 구성 됩니다.

6. 선택 **Restore DB**합니다.

    ![복원 DB 선택](./media/backup-azure-sql-database/restore-db-button.png)

7. **복원 구성**, 데이터를 복원할 위치를 지정 합니다.
   - **대체 위치**: 대체 위치에 데이터베이스를 복원 하 고 원본 데이터베이스를 유지 합니다.
   - **DB 덮어쓰기**: 원래 원본과 동일한 SQL Server 인스턴스에 데이터를 복원합니다. 이 옵션은 원본 데이터베이스를 덮어씁니다.

     > [!Important]
     > 선택한 데이터베이스가 Always On 가용성 그룹에 속하면 SQL Server에서 데이터베이스를 덮어쓸 수 없습니다. **대체 위치**만 사용할 수 있습니다.
     >

     ![복원 구성 메뉴](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>대체 위치에 복원

1. 에 **복원 구성** 메뉴 아래에 있는 **복원할 위치**를 선택 **대체 위치**합니다.
2. 데이터베이스를 복원하려는 SQL Server 이름 및 인스턴스를 선택합니다.
3. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.
4. 해당되는 경우 **선택한 SQL 인스턴스에 이름이 같은 DB가 있으면 덮어쓰기**를 선택합니다.
5. **확인**을 선택합니다.

    ![복원 구성 메뉴에 대한 값 제공](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **복원 지점 선택**, 선택 것인지 [특정 시점으로 복원](#restore-to-a-specific-point-in-time) 또는 [특정 복구 지점으로 복원](#restore-to-a-specific-restore-point)합니다.

    > [!NOTE]
    > 지정 시간 복원은 전체 및 대량 로그 복구 모드에 있는 데이터베이스에 대 한 로그 백업에만 사용할 수 있습니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. 에 **복원 구성** 메뉴 아래에 있는 **복원할 위치**를 선택 **DB 덮어쓰기** > **확인**합니다.

    ![DB 덮어쓰기 선택](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **복원 지점 선택**를 선택 **로그 (시점)** 하 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 선택 하거나 **전체 & 차등** 를 복원 하는 [특정 복구 지점](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > 지정 시간 복원은 전체 및 대량 로그 복구 모드에 있는 데이터베이스에 대 한 로그 백업에만 사용할 수 있습니다.

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1.  아래 **복원 날짜/시간**, 달력을 엽니다. 달력에서 굵게 표시 되는 복구 지점에 있는 날짜 및 현재 날짜가 강조 표시 됩니다.
1. 복구 지점에 있는 날짜를 선택 합니다. 복구 지점이 있는 날짜를 선택할 수 없습니다.

    ![달력을 열으십시오](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 날짜를 선택한 후에는 타임라인 그래프에 사용 가능한 복구 지점이 연속적인 범위로 표시됩니다.
1. 시간 표시 막대 그래프에서 복구 시간을 지정 하거나 시간을 선택 합니다. 그런 다음 **확인**을 선택합니다.

    ![복원 시간을 선택](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. 에 **고급 구성** 메뉴에서 복원 후 데이터베이스를 사용할 수 없지만 유지 하려는 경우 사용 하도록 설정 **Restore with NORECOVERY**합니다.
1. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.
1. **확인**을 선택합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
1. 복원 진행률을 추적 합니다 **알림을** 영역에서 선택 하 여 추적 또는 **복원 작업** 데이터베이스 메뉴에서.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>특정 복원 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택하고 **확인**을 선택하여 복원 지점 절차를 완료합니다.

    ![전체 복구 지점 선택](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. 에 **고급 구성** 메뉴에서 복원 후 데이터베이스를 사용할 수 없지만 유지 하려는 경우 사용 하도록 설정 **Restore with NORECOVERY**합니다.
1. 대상 서버에서 복원 위치를 변경하려면 새 대상 경로를 입력합니다.
1. **확인**을 선택합니다.

    ![고급 구성 메뉴](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.
1. 복원 진행률을 추적 합니다 **알림을** 영역에서 선택 하 여 추적 또는 **복원 작업** 데이터베이스 메뉴에서.

    ![복원 작업 진행률](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>다음 단계

[관리 및 모니터링](manage-monitor-sql-database-backup.md) Azure Backup에서 백업 되는 SQL Server 데이터베이스입니다.
