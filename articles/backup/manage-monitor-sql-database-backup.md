---
title: Azure Backup에서 백업 되는 Azure VM에서 SQL Server 데이터베이스 관리 및 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure VM에서 실행 되는 SQL Server 데이터베이스 관리 및 모니터링 하는 방법을 설명 합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: ea5495867d5f453db014e000e01d533d049dc628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849365"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>백업한 SQL Server 데이터베이스 관리 및 모니터링

이 문서에서 자격 증명 모음 관리 및 SQL Server 데이터베이스를 Azure 가상 머신 (VM)에서 실행 되는 및 Azure Backup Recovery services 백업 하는 모니터링에 대 한 일반적인 작업에 설명 합니다 [Azure Backup](backup-overview.md) 서비스입니다. 작업 및 경고 모니터링, 중지 데이터베이스 보호를 다시 시작, 백업 작업을 실행 하 고 백업에서 VM의 등록을 취소 하는 방법을 알아봅니다.

SQL Server 데이터베이스에 대 한 백업을 아직 구성 하지 않은 경우 [Azure Vm에서 SQL Server 데이터베이스 백업](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>포털에서 수동 백업 작업 모니터링

Azure Backup에서 수동으로 트리거된 모든 작업을 표시 합니다 **백업 작업** 포털입니다. 이 포털 포함 데이터베이스 검색 및 등록, 그리고 백업 및 복원 작업 작업입니다.

![백업 작업 포털](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 합니다 **백업 작업** 포털 예약 된 백업 작업을 표시 하지 않습니다. SQL Server Management Studio를 사용하면 다음 섹션에서 설명하는 대로 예약된 백업 작업을 모니터링할 수 있습니다.
>

모니터링 시나리오에 대 한 내용은로 이동 [Azure Portal에서 모니터링](backup-azure-monitoring-built-in-monitor.md) 하 고 [Azure Monitor를 사용 하 여 모니터링](backup-azure-monitoring-use-azuremonitor.md)합니다.  


## <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에 백업 작업 모니터링이 번거로울 수 있습니다. Azure Backup 전자 메일 경고를 전송 하 여 모니터링을 간소화 합니다. 전자 메일 알림은 다음과 같습니다.

- 모든 백업 실패에 대해 트리거됩니다.
- 오류 코드에서 데이터베이스 수준에서 통합 합니다.
- 데이터베이스의 첫 번째 백업 실패에 대해서만 전송 합니다.

데이터베이스 백업 경고를 모니터링 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 자격 증명 모음 대시보드에서 **경고 및 이벤트**를 선택합니다.

   ![경고 및 이벤트 선택](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. **경고 및 이벤트**에서 **Backup 경고**를 선택합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

다음과 같은 여러 가지 방법으로 SQL Server 데이터베이스 백업을 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 모든 미래의 백업 작업을 중지 하 고 복구 지점을 그대로 둡니다.

복구 지점을 유지 하려는 경우 이러한 세부 사항에 유의 해야 합니다.

* 모든 복구 지점을 계속 그대로 유지 됩니다, 모든 정리 중지 중지는 보호 된 데이터를 보존 합니다.
* 보호 된 인스턴스 및 사용 된 저장소에 대 한 청구 됩니다. 자세한 내용은 [Azure Backup 가격](https://azure.microsoft.com/pricing/details/backup/)합니다.
* 백업 중지 하지 않고 데이터 소스를 삭제 하면 새 백업이 실패 합니다.

데이터베이스에 대한 보호를 중지하려면:

1. 자격 증명 모음 대시보드에서 **Backup 항목**합니다.

2. 아래 **백업 관리 유형**를 선택 **Azure VM에서 SQL**합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 보호를 중지하려는 데이터베이스를 선택합니다.

    ![보호를 중지할 데이터베이스 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 중지 선택](./media/backup-azure-sql-database/stop-db-button.png)


5. 에 **백업 중지** 메뉴를 유지 하거나 데이터를 삭제할지 여부를 선택 합니다. 원한다 면 이유 설명을 제공 합니다.

    ![유지 하거나 백업 중지 메뉴의 데이터를 삭제 합니다.](./media/backup-azure-sql-database/stop-backup-button.png)

6. 선택 **백업 중지**합니다.


## <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

선택 하는 경우 SQL database에 대 한 보호를 중지 하는 **Backup 데이터 보관** 옵션, 보호를 나중에 다시 시작할 수 있습니다. 백업 데이터를 유지 하지 않습니다, 경우에 보호를 다시 시작할 수 없습니다.

SQL database에 대 한 보호를 다시 시작 합니다.

1. 선택한 백업 항목을 엽니다 **백업 다시 시작**합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

다음과 같은 다양한 유형의 주문형 백업을 실행할 수 있습니다.

* 전체 백업
* 복사 전용 전체 백업
* 차등 백업
* 로그 백업

복사 전용 전체 백업에 대 한 보존 기간을 지정 해야 하는 동안 다른 백업 형식에 대 한 보존 범위는 자동으로 30 일로 설정 현재 시간부터 합니다. <br/>
자세한 내용은 [SQL Server 백업 유형](backup-architecture.md#sql-server-backup-types)합니다.

## <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

보호를 사용 하지 않도록 설정 하지만 자격 증명 모음을 삭제 하기 전에 SQL Server 인스턴스를 등록 취소 합니다.

1. 자격 증명 모음 대시보드에서 아래 **관리**를 선택 **Backup 인프라**합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **관리 서버**에서 **보호된 서버**를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)

3. **보호된 서버**에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

4. 보호 된 서버를 마우스 오른쪽 단추로 누르고 **삭제**합니다.

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM에서 확장 다시 등록

경우에 따라 이유 중 하나 또는 다른 VM에서 워크 로드 확장 영향 수 있습니다. 이러한 경우 VM에서 트리거된 모든 작업이 실패 하기 시작 합니다. 그런 다음 VM에서 확장을 다시 등록 해야 합니다. **Re-register** 작업을 계속 하려면 작업에 대 한 VM에서 워크 로드 백업 확장을 다시 설치 합니다.  <br>

주의 사용 하 여이 옵션을 사용 하는 것이 좋습니다. 이미 정상 확장을 사용 하 여 VM에서 트리거되면이 작업에 확장을 다시 시작 가져오기 발생 합니다. 따라서 모든 진행 중인 작업이 실패할 수 있습니다. 하나 이상의 참조 확인 합니다 [증상](backup-sql-server-azure-troubleshoot.md#symptoms) 다시 등록 작업을 트리거하기 전에 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SQL Server 데이터베이스의 백업 문제 해결](backup-sql-server-azure-troubleshoot.md)합니다.
