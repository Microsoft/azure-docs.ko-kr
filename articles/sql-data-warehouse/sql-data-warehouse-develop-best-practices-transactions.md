---
title: Azure SQL Data Warehouse에 대해 트랜잭션 최적화 | Microsoft Docs
description: 긴 롤백에 대한 위험을 최소화하면서 Azure SQL Data Warehouse의 트랜잭션 코드 성능을 최적화하는 방법을 알아봅니다.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: f5e0b2b75ac111f3221108936f84e5883aebfc1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478830"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 트랜잭션 최적화
긴 롤백에 대한 위험을 최소화하면서 Azure SQL Data Warehouse의 트랜잭션 코드 성능을 최적화하는 방법을 알아봅니다.

## <a name="transactions-and-logging"></a>트랜잭션 및 로깅
트랜잭션은 관계형 데이터베이스 엔진의 중요한 구성 요소입니다. SQL Data Warehouse는 데이터를 수정하는 동안 트랜잭션을 사용합니다. 이러한 트랜잭션은 명시적일 수도 있고 암시적일 수도 있습니다. 단일 INSERT, UPDATE 및 DELETE 문은 모두 암시적 트랜잭션의 예입니다. 명시적 트랜잭션은 BEGIN TRAN, COMMIT TRAN 또는 ROLLBACK TRAN을 사용합니다. 명시적 트랜잭션은 일반적으로 여러 수정 문을 단일 원자 단위에 서로 연결되도록 해야 하는 경우에 사용됩니다. 

Azure SQL Data Warehouse는 트랜잭션 로그를 사용하여 데이터베이스에 변경 내용을 커밋합니다. 각 분산에는 고유한 트랜잭션 로그가 있습니다. 트랜잭션 로그 쓰기는 자동입니다. 구성이 필요 없습니다. 그러나 이 프로세스는 쓰기를 보장하지만 시스템에 오버헤드가 발생합니다. 트랜잭션 측면에서 효율적인 코드를 작성하면 이 영향을 최소화할 수 있습니다. 트랜잭션 측면에서 효율적인 코드는 크게 두 범주로 나눌 수 있습니다.

* 가능하면 항상 최소한의 로깅 구문 사용
* 한 트랜잭션이 오래 실행되지 않도록 범위가 지정된 배치 사용
* 지정된 파티션에 대규모 수정을 위한 파티션 전환 패턴 사용

## <a name="minimal-vs-full-logging"></a>최소 로깅 및 전체 로깅 비교
트랜잭션 로그를 사용하여 모든 행 변경을 추적하는 전체 로깅 작업과는 달리, 최소 로깅 작업은 규모 할당 및 메타 데이터 변경 내용만 추적합니다. 따라서 최소 로깅은 장애 발생 후 또는 명시적 요청을 위해 트랜잭션을 롤백(ROLLBACK TRAN)하는 데 필요한 정보만 로깅합니다. 최소 로깅 작업은 트랜잭션 로그에서 추적하는 정보의 양이 훨씬 적기 때문에 비슷한 크기의 전체 로깅 작업보다 성능이 우수합니다. 뿐만 아니라 트랜잭션 로그에 전달되는 쓰기 작업이 적기 때문에 훨씬 적은 양의 로그 데이터가 생성되고 따라서 I/O가 훨씬 효율적입니다.

트랜잭션 안전성 제한은 전체 로깅 작업에만 적용됩니다.

> [!NOTE]
> 최소 로깅 작업은 명시적 트랜잭션에 참여할 수 있습니다. 할당 구조의 모든 변경 내용이 추적되므로 최소 로깅 작업을 롤백할 수 있습니다. 
> 
> 

## <a name="minimally-logged-operations"></a>최소 로깅 작업
다음은 최소한으로 로깅 가능한 작업입니다.

* CREATE TABLE AS SELECT([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> 내부 데이터 이동 작업(예: BROADCAST 및 SHUFFLE)은 트랜잭션 안전성 제한의 영향을 받지 않습니다.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>대량 로드를 사용하여 최소 로깅
CTAS 및 INSERT...SELECT는 둘 다 대량 로드 작업입니다. 그러나 둘 다 대상 테이블 정의의 영향을 받으며 부하 시나리오에 따라 달라집니다. 다음 표에서는 대량 작업이 완전히 기록되거나 최소로 기록되는 시기를 설명합니다.  

| 기본 인덱스 | 부하 시나리오 | 로깅 모드 |
| --- | --- | --- |
| 힙 |모두 |**최소** |
| 클러스터형 인덱스 |빈 대상 테이블 |**최소** |
| 클러스터형 인덱스 |로드된 행이 대상의 기존 페이지와 겹치지 않음 |**최소** |
| 클러스터형 인덱스 |로드된 행이 대상의 기존 페이지와 겹침 |전체 |
| 클러스터형 Clustered 인덱스 |Batch 크기는 파티션 정렬 분산당 102,400 이상 |**최소** |
| 클러스터형 Clustered 인덱스 |Batch 크기는 파티션 정렬 분산당 102,400 미만 |전체 |

보조 또는 비클러스터형 인덱스를 업데이트하는 모든 쓰기 작업은 항상 전체 로깅됩니다.

> [!IMPORTANT]
> SQL Data Warehouse에는 60개의 분산이 있습니다. 따라서 모든 행이 균등하게 분산되고 단일 파티션에 도착한다고 가정하면, 클러스터형 Columnstore 인덱스에 쓸 때 최소한으로 로깅하려면 배치에 6,144,000개 이상의 행이 포함되어야 합니다. 테이블이 분할되어 있고, 삽입되는 행이 파티션 경계에 걸쳐 있는 경우에는 데이터가 균등하게 분산된다는 가정하에 파티션 경계당 6,144,000개의 행이 필요합니다. 삽입 작업을 분산에 최소한으로 로깅하려면 각 분산의 각 파티션이 행 임계값 102,400을 독립적으로 초과해야 합니다.
> 
> 

클러스터형 인덱스가 포함된 비어 있지 않은 테이블로 데이터를 로드하면 전체 로깅 행과 최소 로깅 행이 모두 포함되는 경우가 종종 있습니다. 클러스터형 인덱스는 균형 잡힌 페이지 트리(b-트리)입니다. 쓰여지고 있는 페이지가 이미 다른 트랜잭션의 행을 포함하고 있으면 쓰기 작업이 전체 로깅됩니다. 그러나 페이지가 비어 있으면 해당 페이지에 대한 쓰기 작업이 최소한으로 로깅됩니다.

## <a name="optimizing-deletes"></a>삭제 최적화
DELETE는 전체 로깅 작업입니다.  테이블 또는 파티션에서 대량의 데이터를 삭제해야 하는 경우 보관하려는 데이터에 대해 최소 로깅 작업으로 실행할 수 있는 `SELECT` 를 사용하는 것이 적절한 경우가 많습니다.  데이터를 선택하려면 [CTAS](sql-data-warehouse-develop-ctas.md)를 사용하여 새 테이블을 만듭니다.  새 테이블을 만든 후에는 [RENAME](/sql/t-sql/statements/rename-transact-sql)을 사용하여 이전 테이블을 새로 만든 테이블로 교체합니다.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>업데이트 최적화
UPDATE는 전체 로깅 작업입니다.  테이블 또는 파티션에서 행을 대량으로 업데이트해야 하는 경우 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)처럼 최소 로깅 작업을 사용하는 것이 훨씬 효율적일 때가 종종 있습니다.

아래 예에서는 최소 로깅이 가능하도록 전체 테이블 업데이트가 CTAS로 변환되었습니다.

이 예에서는 테이블의 판매 금액에 할인 금액을 소급하여 추가하고 있습니다.

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> 대규모 테이블을 다시 만들면 SQL Data Warehouse 워크로드 관리 기능의 이점을 활용할 수 있습니다. 자세한 내용은 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
> 
> 

## <a name="optimizing-with-partition-switching"></a>파티션 전환을 사용하여 최적화
[테이블 파티션](sql-data-warehouse-tables-partition.md) 내부에서 대규모 수정 작업에 직면하는 경우 파티션 전환 패턴을 사용하는 것이 효율적입니다. 데이터 수정 작업이 대규모이고 여러 파티션에 걸쳐 있는 경우 파티션을 반복해도 동일한 결과를 얻습니다.

파티션 전환을 수행하는 단계는 다음과 같습니다.

1. 빈 외부 파티션 만들기
2. CTAS로 '업데이트' 수행
3. 기존 데이터를 외부 테이블로 전환
4. 새 데이터를 내부로 전환
5. 데이터 정리

그러나 전환할 파티션을 식별하는 데 도움이 되도록 다음 도우미 프로시저를 만듭니다.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

이 프로시저는 코드 재사용률을 최대화하고 파티션 전환 예를 보다 작게 유지합니다.

아래 코드는 앞서 언급한 전체 파티션 전환 루틴을 달성하는 단계를 보여줍니다.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>작은 배치를 사용하여 로깅 최소화
대규모 데이터 수정 작업의 경우 작업을 청크 또는 배치로 나누어서 작업 단위의 범위를 지정하는 것이 더 편할 수 있습니다.

다음 코드는 작업 예제입니다. 방법을 강조하기 위해 배치 크기가 간단한 숫자로 설정되었습니다. 현실에서는 배치 크기가 엄청나게 거대합니다. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>일시 중지 및 크기 조정 지침
Azure SQL Data Warehouse를 사용하여 필요에 따라 데이터 웨어하우스를 [일시 중지하고, 다시 시작하고, 규모를 조정](sql-data-warehouse-manage-compute-overview.md)할 수 있습니다. SQL Data Warehouse를 일시 중지하거나 규모를 조정하면 진행 중인 모든 트랜잭션이 즉시 종료되고 열려 있는 모든 트랜잭션이 롤백됩니다. 일시 중지 또는 규모 조정 작업을 수행하기 전에 워크로드에서 실행 시간이 길고 불완전한 데이터 수정 작업을 실행하면 이 작업을 실행 취소해야 합니다. 실행 취소로 인해 Azure SQL Data Warehouse 데이터베이스를 일시 중지하거나 크기를 조정하는 데 걸리는 시간이 달라질 수 있습니다. 

> [!IMPORTANT]
> `UPDATE` 및 `DELETE`는 전체 로깅 작업이므로 이러한 실행 취소/다시 실행 작업이 최소 로깅에 비해 훨씬 오래 걸릴 수 있습니다. 
> 
> 

가장 좋은 시나리오는 진행 중인 데이터 수정 트랜잭션이 완료된 후 SQL Data Warehouse를 일시 중지하거나 규모를 조정하는 것입니다. 그러나 이 시나리오가 항상 실용적이지는 않습니다. 긴 롤백의 위험을 완화하기 위해 다음 옵션 중 하나를 고려해 볼 수 있습니다.

* [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)를 사용하여 실행 시간이 긴 작업을 다시 작성
* 작업을 청크로 나누어서 행의 하위 집합에서 작동

## <a name="next-steps"></a>다음 단계
격리 수준 및 트랜잭션 제한에 대해 자세히 알아보려면 [SQL Data Warehouse의 트랜잭션](sql-data-warehouse-develop-transactions.md) 을 참조하세요.  기타 모범 사례의 개요에 대해서는 [SQL Data Warehouse 모범 사례](sql-data-warehouse-best-practices.md)를 참조하세요.

