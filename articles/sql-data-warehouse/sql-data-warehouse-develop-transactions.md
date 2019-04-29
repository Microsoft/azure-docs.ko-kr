---
title: Azure SQL Data Warehouse의 트랜잭션 사용 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 트랜잭션 구현을 위한 팁
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 0b4ce6f4479552f42d32124149f64614b7e3cb70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439182"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>SQL Data Warehouse의 트랜잭션 사용
솔루션 개발을 위한 Azure SQL Data Warehouse의 트랜잭션 구현을 위한 팁

## <a name="what-to-expect"></a>예상 프로그램
예상한 것처럼 SQL Data Warehouse는 데이터 웨어하우스 워크로드의 일부로 트랜잭션을 지원합니다. 그러나 SQL Data Warehouse의 성능은 SQL Server와 비교할 때 일부 기능이 제한되는 수준으로 유지됩니다. 이 문서는 차이점을 강조 표시하고 다른 부분에 대해 설명합니다. 

## <a name="transaction-isolation-levels"></a>트랜잭션 격리 수준
SQL Data Warehouse는 ACID 트랜잭션을 구현합니다. 그러나, 트랜잭션 지원의 격리 수준은 READ UNCOMMITTED로 제한되며 이 수준은 변경할 수 없습니다. READ UNCOMMITTED가 염려되는 경우 다양한 코딩 메서드를 구현하여 데이터의 더티 읽기를 방지할 수 있습니다. 가장 인기 있는 메서드는 CTAS 및 테이블 파티션 전환(슬라이딩 창 패턴이라고 하는)을 모두 사용하여 사용자 준비 중인 데이터 쿼리를 방지합니다. 데이터를 사전 필터링하는 뷰가 일반적인 접근 방법이기도 합니다.  

## <a name="transaction-size"></a>트랜잭션 크기
단일 데이터 수정 트랜잭션은 크기가 제한됩니다. 이러한 제한은 배포 기준으로 적용됩니다. 따라서 제한을 배포 수와 곱하여 전체 할당을 계산할 수 있습니다. 트랜잭션에 포함된 대략적인 최대 행 수를 구하려면 배포 용량을 각 행의 전체 크기로 나눕니다. 가변 길이 열의 경우에는 최대 크기를 사용하는 대신, 평균 열 길이를 사용하는 것을 고려합니다.

아래 표에서는 다음과 같이 가정되었습니다.

* 균일한 데이터 분포가 발생했습니다. 
* 평균 행 길이는 250바이트입니다.

## <a name="gen2"></a>2세대

| [DWU](sql-data-warehouse-overview-what-is.md) | (GB) 배포당 용량 | 배포 수 | 최대 트랜잭션 크기 (GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>1세대

| [DWU](sql-data-warehouse-overview-what-is.md) | (GB) 배포당 용량 | 배포 수 | 최대 트랜잭션 크기 (GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5. |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

트랜잭션 또는 작업 기준으로 트랜잭션 크기 제한이 적용되며 모든 동시 트랜잭션에서 적용되지는 않습니다. 따라서 각 트랜잭션은 이 크기의 데이터를 로그에 쓰도록 허용됩니다. 

로그에 기록된 데이터의 양을 최적화하고 최소화하려면 [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md) 문서를 참조하세요.

> [!WARNING]
> 최대 트랜잭션 크기는 데이터가 균일하게 분산되는 HASH 또는 ROUND_ROBIN 분산 테이블에 대해서만 도달할 수 있습니다. 트랜잭션이 균일하지 않은 분산 방식으로 데이터를 쓰는 경우 최대 트랜잭션 크기에 도달하기 전에 제한에 도달할 가능성이 높습니다.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>트랜잭션 상태
SQL Data Warehouse는 XACT_STATE() 함수를 사용하여 값 -2를 사용하는 실패한 트랜잭션을 보고합니다. 이 값은 트랜잭션이 실패하고 롤백만 표시함을 의미합니다.

> [!NOTE]
> XACT_STATE 함수에서-2 사용은 실패한 트랜잭션이 SQL Server와 다른 동작을 표시함을 나타냅니다. SQL Server는 값 -1를 사용하여 커밋할 수 없는 트랜잭션을 나타냅니다. SQL Server는 커밋할 수 없음으로 표시하지 않고 트랜잭션 내 일부 오류를 허용할 수 있습니다. 예를 들어 `SELECT 1/0`은 오류를 발생시키지만 커밋할 수 없는 상태로 트랜잭션을 강제 적용하지 않습니다. 또한 SQL Server는 커밋할 수 없는 트랜잭션에서 읽기를 허용합니다. 그러나 SQL Data Warehouse는 이를 허용하지 않습니다. SQL Data Warehouse의 트랜잭션 내부에서 오류가 발생하는 경우 자동으로 -2 상태가 되며, 해당 문이 롤백될 때까지 추가 select 문을 실행할 수 없습니다. 따라서 코드를 수정해야 할 수 있으므로 XACT_STATE()가 사용되는지 알기 위해 해당 애플리케이션 코드를 확인하는 것이 중요합니다.
> 
> 

예를 들어 SQL Server에서 다음과 같은 트랜잭션이 나타날 수 있습니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

위의 코드는 다음과 같은 오류 메시지를 제공합니다.

Msg 111233, Level 16, State 1, Line 1 111233; 현재 트랜잭션이 중단되었으며 보류 중인 변경 내용은 롤백되었습니다. 원인: 롤백 전용 상태의 트랜잭션이 DDL, DML 또는 SELECT 문 앞에서 명시적으로 롤백되지 않았습니다.

또한 ERROR_* 함수의 출력도 제공되지 않습니다.

SQL Data Warehouse에서는 이 코드를 약간 변경해야 합니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

이제 예상되는 동작이 진행됩니다. 트랜잭션의 오류가 관리되고 ERROR_* 함수는 예상대로 값을 제공합니다.

변경된 부분은 트랜잭션의 ROLLBACK이 CATCH 블록의 오류 정보를 읽기 전에 발생해야 한다는 것입니다.

## <a name="errorline-function"></a>Error_Line() 함수
SQL Data Warehouse가 ERROR_LINE() 함수를 구현하거나 지원하는 것 또한 주목할 가치가 있습니다. 이 코드에 있는 경우 SQL Data Warehouse와 호환되도록 제거해야 합니다. 동등한 기능을 구현하는 대신 코드에서 쿼리 레이블을 사용합니다. 자세한 내용은 [레이블](sql-data-warehouse-develop-label.md) 문서를 참조하세요.

## <a name="using-throw-and-raiserror"></a>THROW 및 RAISERROR 사용
THROW는 SQL Data Warehouse에서 예외를 발생시키기 위한 가장 최신 구현이지만 RAISERROR도 지원됩니다. 그러나 다음 몇 가지 사항에 주의해야 합니다.

* 사용자 정의 오류 메시지 번호는 THROW에 대해 100,000 - 150,000 범위에 있을 수 없습니다.
* RAISERROR 오류 메시지는 50,000으로 고정됩니다.
* sys.messages 사용은 지원되지 않습니다.

## <a name="limitations"></a>제한 사항
SQL Data Warehouse에는 트랜잭션과 관련된 몇 가지 기타 제한 사항이 있습니다.

다음과 같습니다.

* 분산된 트랜잭션이 없습니다
* 중첩된 트랜잭션이 허용되지 않습니다.
* 저장 점수를 사용할 수 없습니다.
* 명명된 트랜잭션이 없습니다.
* 표시된 트랜잭션이 없습니다.
* 사용자 정의 트랜잭션 내에 CREATE TABLE과 같은 DDL 지원은 없습니다.

## <a name="next-steps"></a>다음 단계
트랜잭션을 최적화하는 방법에 대한 자세한 내용은 [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md)를 참조하세요. 기타 SQL Data Warehouse 모범 사례에 대해 자세히 알아보려면 [SQL Data Warehouse 모범 사례](sql-data-warehouse-best-practices.md)를 참조하세요.

