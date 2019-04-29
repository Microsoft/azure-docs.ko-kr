---
title: Azure SQL Data Warehouse 복원(Azure Portal) | Microsoft Docs
description: Azure SQL Data Warehouse 복원을 위한 Azure Portal 작업
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2d59235b067d9571bc8b64c33799431be6489502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421392"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Azure SQL Data Warehouse 복원(포털)
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [포털][Portal]
> * [PowerShell][PowerShell]
> * [REST (영문)][REST]
> 
> 
> 이 문서에서는 Azure Portal을 사용하여 Azure SQL Data Warehouse를 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.** SQL Data Warehouse의 각 인스턴스는 기본 DTU(데이터 처리량 단위) 할당량이 있는 SQL Server(예 : myserver.database.windows.net)에 의해 호스트됩니다. SQL Data Warehouse를 복원하기 전에 SQL Server에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. DTU 할당량을 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][Request a DTU quota change]을 참조하세요.

## <a name="restore-an-active-or-paused-database"></a>활성 또는 일시 중지된 데이터베이스 복원
데이터베이스를 복원하려면

1. [Azure Portal][Azure portal]에 로그인합니다.
2. 왼쪽 창에서 **찾아보기**를 선택한 다음 **SQL Servers**를 선택합니다.

    ![찾아보기 > SQL Servers 선택](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 서버를 찾아 선택합니다.

    ![서버 선택](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. 복원하려는 SQL Data Warehouse 인스터스를 찾아서 선택합니다.

    ![복원할 SQL Data Warehouse의 인스턴스 선택](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Data Warehouse 블레이드의 위쪽에서 **복원**을 선택합니다.

    ![복원 선택](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. 새 **데이터베이스 이름**을 지정합니다.
7. 최신 **복원 지점**을 선택합니다.

   최신 복원 지점을 선택했는지 확인합니다. 복원 지점은 UTC(협정 세계시)로 표시되기 때문에 기본 옵션이 최신 복원 지점이 아닐 수도 있습니다.

      ![복원 지점 선택](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. **확인**을 선택합니다.
9. 데이터베이스 복원 프로세스가 시작되고 **알림**을 사용하여 프로세스를 모니터링할 수 있습니다.

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][Configure your database after recovery]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
>
>

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원
삭제된 데이터베이스를 복원하려면:

1. [Azure Portal][Azure portal]에 로그인합니다.
2. 왼쪽 창에서 **찾아보기**를 선택한 다음 **SQL Servers**를 선택합니다.

    ![찾아보기 > SQL Servers 선택](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 서버를 찾아 선택합니다.

    ![서버 선택](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. 서버 블레이드의 **작업** 섹션까지 아래로 스크롤합니다.
5. **삭제된 데이터베이스** 타일을 선택합니다.

    ![삭제된 데이터베이스 타일 선택](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. 복원할 삭제된 데이터베이스를 선택합니다.

    ![복원할 데이터베이스 선택](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. 새 **데이터베이스 이름**을 지정합니다.

    ![데이터베이스에 대한 이름 추가](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. **확인**을 선택합니다.
9. 데이터베이스 복원 프로세스가 시작되고 **알림**을 사용하여 프로세스를 모니터링할 수 있습니다.

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성][Configure your database after recovery]을 참조하세요.
>
>

## <a name="next-steps"></a>다음 단계
Azure SQL Database 버전의 비즈니스 연속성 기능에 대해 알아보려면 [Azure SQL Database 비즈니스 연속성 개요][Azure SQL Database business continuity overview]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
