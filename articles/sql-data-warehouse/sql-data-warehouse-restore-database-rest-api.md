---
title: Azure SQL Data Warehouse 복원 - REST API | Microsoft Docs
description: REST API를 사용하여 Azure SQL Data Warehouse를 복원합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935690"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>REST API로 Azure SQL Data Warehouse 복원
REST API를 사용하여 Azure SQL Data Warehouse를 복원합니다.

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.** 각 SQL Data Warehouse는 기본 [DTU 할당량](../sql-database/sql-database-what-is-a-dtu.md)이 있는 논리적 SQL 서버(예: myserver.database.windows.net)에 의해 호스팅됩니다.  SQL Data Warehouse를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. DTU를 더 요청하려면 [지원 티켓 만들기](sql-data-warehouse-get-started-create-support-ticket.md)를 수행하세요.

## <a name="restore-an-active-or-paused-data-warehouse"></a>활성 또는 일시 중지된 데이터 웨어하우스 복원
데이터 웨어하우스를 복원하려면:

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기](https://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태](https://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)에 따라 복구된 데이터 웨어하우스를 구성할 수 있습니다.
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>삭제된 데이터 웨어하우스 복원
삭제된 데이터 웨어하우스를 복원하려면:

1. [복원 가능한 삭제된 데이터베이스 나열](https://msdn.microsoft.com/library/azure/dn509562.aspx) 작업을 사용하여 복원 가능한 삭제된 데이터 웨어하우스를 모두 나열합니다.
2. [복원 가능한 삭제된 데이터베이스 가져오기][복원 가능한 삭제된 데이터베이스 가져오기] 작업을 사용하여 복원하려는 삭제된 데이터 웨어하우스에 대한 세부 정보를 가져옵니다.
3. [데이터베이스 복원 요청 만들기](https://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 사용하여 복원을 시작합니다.
4. [데이터베이스 작업 상태](https://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 사용하여 복원 상태를 추적합니다.

> [!NOTE]
> 복원이 완료된 후에 데이터 웨어하우스를 구성하려면 [복구 후 데이터베이스 구성](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
Azure SQL Database 버전의 비즈니스 연속성 기능에 대해 알아보려면 [Azure SQL Database 비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 읽으세요.
