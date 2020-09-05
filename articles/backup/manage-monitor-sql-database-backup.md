---
title: Azure VM에서 SQL Server Db 관리 및 모니터링
description: 이 문서에서는 Azure VM에서 실행 되는 SQL Server 데이터베이스를 관리 하 고 모니터링 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: b0df22002521c8148cac1200e79aeb0ae5a60546
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146524"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>백업한 SQL Server 데이터베이스 관리 및 모니터링

이 문서에서는 Azure VM (가상 컴퓨터)에서 실행 되 고 [Azure Backup](backup-overview.md) 서비스에서 Azure Backup Recovery Services 자격 증명 모음에 백업 되는 SQL Server 데이터베이스를 관리 하 고 모니터링 하는 일반적인 작업에 대해 설명 합니다. 작업 및 경고를 모니터링 하 고, 데이터베이스 보호를 중지 한 후 다시 시작 하 고, 백업 작업을 실행 하 고, 백업에서 VM의 등록을 취소 하는 방법을 알아봅니다.

SQL Server 데이터베이스에 대 한 백업을 아직 구성 하지 않은 경우 [Azure vm에서 SQL Server 데이터베이스 백업](backup-azure-sql-database.md) 을 참조 하세요.

## <a name="monitor-backup-jobs-in-the-portal"></a>포털에서 백업 작업 모니터링

Azure Backup는 포털의 **백업 작업** 에서 예약 된 작업 및 주문형 작업을 모두 표시 합니다. 단, 예약 된 로그 백업은 매우 자주 수행 될 수 있기 때문입니다. 이 포털에 표시 되는 작업에는 데이터베이스 검색 및 등록, 백업 구성, 백업 및 복원 작업이 포함 됩니다.

![백업 작업 포털](./media/backup-azure-sql-database/jobs-list.png)

모니터링 시나리오에 대 한 자세한 내용은 [Azure Portal의 모니터링](backup-azure-monitoring-built-in-monitor.md) 및 Azure Monitor를 [사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)을 참조 하세요.  

## <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에 백업 작업 모니터링이 번거로울 수 있습니다. Azure Backup 전자 메일 경고를 전송 하 여 모니터링을 용이 하 게 합니다. 전자 메일 경고는 다음과 같습니다.

- 모든 백업 실패에 대해 트리거됩니다.
- 오류 코드별로 데이터베이스 수준에서 통합됩니다.
- 데이터베이스의 첫 번째 백업 실패에 대해서만 전송 됩니다.

데이터베이스 백업 경고를 모니터링 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 자격 증명 모음 대시보드에서 **경고 및 이벤트**를 선택합니다.

   ![경고 및 이벤트 선택](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. **경고 및 이벤트**에서 **Backup 경고**를 선택합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

다음과 같은 여러 가지 방법으로 SQL Server 데이터베이스 백업을 중지할 수 있습니다.

- 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
- 이후의 모든 백업 작업을 중지 하 고 복구 지점은 그대로 둡니다.

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

- 모든 복구 지점은 영구적으로 그대로 유지 되 고 모든 정리는 데이터 보존을 사용 하 여 보호 중지에서 중지 됩니다.
- 보호된 인스턴스와 사용한 스토리지 요금이 청구됩니다. 자세한 내용은 [Microsoft Azure Backup 가격 책정](https://azure.microsoft.com/pricing/details/backup/)을 참조하세요.
- 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다. 이전 복구 지점은 정책에 따라 만료 되지만 가장 최근 복구 지점은 백업을 중지 하 고 데이터를 삭제할 때까지 항상 유지 됩니다.

데이터베이스에 대한 보호를 중지하려면:

1. 자격 증명 모음 대시보드에서 **백업 항목**을 선택합니다.

2. **Backup 관리 유형**에서 **Azure VM의 SQL**을 선택 합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 보호를 중지하려는 데이터베이스를 선택합니다.

    ![보호를 중지할 데이터베이스 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 중지 선택](./media/backup-azure-sql-database/stop-db-button.png)

5. **백업 중지** 메뉴에서 데이터를 보존할지 아니면 삭제할지 선택합니다. 원하는 경우 이유나 설명을 입력합니다.

    ![백업 중지 메뉴에서 데이터 보존 또는 삭제](./media/backup-azure-sql-database/stop-backup-button.png)

6. **백업 중지**를 선택합니다.

> [!NOTE]
>
>데이터 삭제 옵션에 대 한 자세한 내용은 아래 FAQ를 참조 하세요.
>
>- [Autoprotected 인스턴스에서 데이터베이스를 삭제 하는 경우 백업에 어떤 일이 발생 하나요?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Autoprotected 데이터베이스의 백업 작업을 중지 하는 경우 해당 동작이 어떻게 되나요?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

SQL database에 대 한 보호를 중지 하는 경우 **백업 데이터 보존** 옵션을 선택 하면 나중에 보호를 다시 시작할 수 있습니다. 백업 데이터를 보존 하지 않는 경우 보호를 다시 시작할 수 없습니다.

SQL database에 대 한 보호를 다시 시작 하려면:

1. 백업 항목을 열고 **백업 다시 시작**을 선택합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

다음과 같은 다양한 유형의 주문형 백업을 실행할 수 있습니다.

- 전체 백업
- 복사 전용 전체 백업
- 차등 백업
- 로그 백업

복사 전용 전체 백업에 대 한 보존 기간을 지정 해야 하지만, 주문형 전체 백업에 대 한 보존 범위는 현재 시간부터 45 일로 자동 설정 됩니다.

자세한 내용은 [SQL Server 백업 유형](backup-architecture.md#sql-server-backup-types)을 참조 하세요.

## <a name="modify-policy"></a>정책 수정

정책을 수정 하 여 백업 빈도 또는 보존 범위를 변경 합니다.

> [!NOTE]
> 보존 기간의 변경 내용은 새 복구 지점이 아닌 모든 이전 복구 지점에 소급 적용됩니다.

자격 증명 모음 대시보드에서 백업 정책 **관리**로 이동 하 여  >  **Backup Policies** 편집 하려는 정책을 선택 합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![백업 정책 수정](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

정책 수정은 관련된 모든 백업 항목에 영향을 주며 해당하는 **보호 구성** 작업을 트리거합니다.

### <a name="inconsistent-policy"></a>일관성 없는 정책

경우에 따라 정책 수정 작업을 수행 하면 일부 백업 항목에 대해 **일관** 되지 않은 정책 버전이 발생할 수 있습니다. 정책 수정 작업이 트리거된 후 백업 항목에 대한 **보호 구성** 작업이 실패하는 경우 이러한 상황이 발생합니다. 백업 항목 보기에 다음과 같이 나타납니다.

  ![일관성 없는 정책](./media/backup-azure-sql-database/inconsistent-policy.png)

한 번의 클릭으로 영향을 받는 모든 항목에 대한 정책 버전을 수정할 수 있습니다.

  ![일관 되지 않은 정책 수정](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

보호를 사용 하지 않도록 설정 하 고 자격 증명 모음을 삭제 하기 전에 SQL Server 인스턴스를 등록 취소 합니다.

1. 자격 증명 모음 대시보드의 **관리** 아래에서 **백업 인프라**를 선택합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **관리 서버**에서 **보호된 서버**를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)

3. **보호된 서버**에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

4. 보호 된 서버를 마우스 오른쪽 단추로 클릭 하 고 **등록 취소**를 선택 합니다.

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM 확장을 다시 등록 합니다.

경우에 따라 VM에 대 한 워크 로드 확장이 영향을 받을 수 있습니다. 이러한 경우 VM에서 트리거되는 모든 작업이 실패하기 시작합니다. 그런 다음, VM에서 확장을 다시 등록해야 할 수 있습니다. **다시 등록** 작업은 작업을 계속 진행 하기 위해 VM에서 워크 로드 백업 확장을 다시 설치 합니다. Recovery Services 자격 증명 모음의 **Backup 인프라** 에서이 옵션을 찾을 수 있습니다.

![백업 인프라의 보호 된 서버](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

이 옵션은 주의해서 사용해야 합니다. 이미 정상 상태의 VM에서 트리거되면이 작업을 수행 하면 확장이 다시 시작 됩니다. 이로 인해 진행 중인 모든 작업이 실패할 수 있습니다. 다시 등록 작업을 트리거하기 전에 하나 이상의 [증상](backup-sql-server-azure-troubleshoot.md#re-registration-failures)을 확인하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SQL Server 데이터베이스에서 백업 문제 해결](backup-sql-server-azure-troubleshoot.md)을 참조 하세요.
