---
title: Azure SQL Database 메모리 내 샘플 | Microsoft Docs
description: OLTP 및 columnstore 샘플에서 Azure SQL Database 메모리 내 기술을 사용해보세요.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 2aa98c3958f1dffeb8adbad5e91a11f397d4a9fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035740"
---
# <a name="in-memory-sample"></a>메모리 내 샘플

Azure SQL Database에서 메모리 내 기술을 사용하면 애플리케이션의 성능을 향상시키고, 데이터베이스의 비용을 크게 줄일 수 있습니다. Azure SQL Database에서 메모리 내 기술을 사용하여 다양한 워크로드를 통해 성능 향상을 얻을 수 있습니다.

이 문서에는 Azure SQL Database의 columnstore 인덱스뿐만 아니라 메모리 내 OLTP의 사용을 보여 주는 두 개의 샘플이 표시됩니다.

참조 항목:
- [메모리 내 OLTP 개요 및 사용 시나리오](https://msdn.microsoft.com/library/mt774593.aspx)(고객 사례 연구 및 시작 정보에 대한 참조 포함)
- [메모리 내 OLTP에 대한 설명서](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP(하이브리드 트랜잭션/분석 처리) 즉, [실시간 운영 분석](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. 메모리 내 OLTP 샘플 설치

[Azure Portal](https://portal.azure.com/)에서 몇 번만 클릭하면 AdventureWorksLT 샘플 데이터베이스를 만들 수 있습니다. 그런 다음 이 섹션의 단계에서는 메모리 내 OLTP 개체를 사용하여 AdventureWorksLT 데이터베이스를 보강할 수 있는 방법 및 성능 이점을 설명합니다.

메모리 내 OLTP의 경우 더 간단하지만 시각적으로 뛰어난 성능 데모는 다음을 참조하세요.

- 릴리스: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- 소스 코드: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>설치 단계

1. [Azure Portal](https://portal.azure.com/)에서 서버에 프리미엄 또는 중요 비즈니스용 데이터베이스를 만듭니다. AdventureWorksLT 샘플 데이터베이스에 **소스**를 설정합니다. 자세한 지침은 [첫 번째 Azure SQL Database 만들기](sql-database-single-database-get-started.md)를 참조하세요.

2. SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx)를 사용하여 데이터베이스에 연결합니다.

3. [메모리 내 OLTP Transact-SQL 스크립트](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) 를 클립보드에 복사합니다. T-SQL 스크립트는 1단계에서 만든 AdventureWorksLT 샘플 데이터베이스에서 필요한 메모리 내 개체를 만듭니다.

4. SSMS에 T-SQL 스크립트를 붙여 넣고 스크립트를 실행합니다. `MEMORY_OPTIMIZED = ON` 절 CREATE TABLE 문이 중요합니다. 예를 들면 다음과 같습니다.


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>오류 40536


T-SQL 스크립트를 실행할 때 오류 40536을 받게 되면 다음 T-SQL 스크립트를 실행하여 데이터베이스에서 메모리를 지원하는지 여부를 확인합니다.


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


**0**이라는 결과는 메모리 내가 지원되지 않음을 나타내고 **1**은 지원됨을 나타냅니다. 문제를 진단하려면 데이터베이스가 프리미엄 서비스 계층에 있어야 합니다.


#### <a name="about-the-created-memory-optimized-items"></a>생성된 메모리 최적화된 항목에 대한 정보

**테이블**: 샘플은 다음과 같은 메모리 최적화 테이블을 포함합니다.

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


SSMS의 **개체 탐색기**를 통해 메모리 최적화 테이블을 검사할 수 있습니다. **테이블** > **필터** > **필터 설정** > **메모리 액세스에 최적화됨**을 마우스 오른쪽 단추로 클릭합니다. 값은 1과 같습니다.


또는 다음과 같은 카탈로그 뷰를 쿼리할 수 있습니다.


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**고유하게 컴파일된 저장 프로시저.**: 카탈로그 뷰 쿼리를 통해 SalesLT.usp_InsertSalesOrder_inmem을 검사할 수 있습니다.


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>샘플 OLTP 워크로드 실행

다음 두 *저장 프로시저* 간의 유일한 차이점은 첫 번째 절차는 메모리 최적화 테이블 버전을 사용하는 반면 두 번째 절차에서는 일반 디스크상의 테이블을 사용한다는 점입니다.

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


이 섹션에는 편리한 **ostress.exe** 유틸리티를 사용하여 스트레스 수준에서 두 저장된 프로시저를 실행하는 방법을 살펴봅니다. 두 스트레스가 실행을 마치는 데 걸리는 시간을 비교할 수 있습니다.


ostress.exe를 실행하는 경우 다음 모두에 대해 설계된 매개 변수 값을 전달하는 것이 좋습니다.

- -n100을 사용하여 많은 수의 동시 연결을 실행합니다.
- -r500을 사용하여 각 연결 루프를 수백 번 가집니다.


그러나 모든 것이 작동하도록 -n10 및 -r50과 같은 훨씬 작은 값으로 시작할 수도 있습니다.


### <a name="script-for-ostressexe"></a>ostress.exe에 대한 스크립트


이 섹션에서는 ostress.exe 명령줄에 포함된 T-SQL 스크립트를 표시합니다. 스크립트는 이전에 설치한 T-SQL 스크립트에 의해 생성된 항목을 사용합니다.


다음 스크립트는 다음과 같은 메모리 최적화 *테이블*에 다섯 줄 항목의 샘플 판매 주문을 삽입합니다.

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


ostress.exe용 이전 T-SQL 스크립트의 *_ondisk* 버전을 만들려면 *_inmem* 하위 문자열의 두 항목을 *_ondisk*로 바꿉니다. 이렇게 대체하면 테이블의 이름 및 저장된 프로시저에 영향을 줍니다.


### <a name="install-rml-utilities-and-ostress"></a>RML 유틸리티 및 `ostress` 설치


이상적으로 Azure VM(가상 머신)에 ostress.exe를 실행합니다. AdventureWorksLT 데이터베이스가 있는 곳과 동일한 Azure 지리적 지역에 [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/)을 만듭니다. 하지만 대신 노트북에서 ostress.exe를 실행할 수 있습니다.


VM 또는 선택한 호스트에서 RML(Replay Markup Language) 유틸리티를 설치합니다. 유틸리티는 ostress.exe를 포함합니다.

참조 항목:
- [메모리 내 OLTP에 대한 샘플 데이터베이스](https://msdn.microsoft.com/library/mt465764.aspx)의 ostress.exe 설명
- [메모리 내 OLTP에 대한 샘플 데이터베이스](https://msdn.microsoft.com/library/mt465764.aspx)
- [ostress.exe 설치에 대한 블로그](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>먼저 *_inmem* 스트레스 워크로드를 실행합니다.


*RML Cmd Prompt* 창을 사용하여 ostress.exe 명령줄을 실행할 수 있습니다. 명령줄 매개 변수는 `ostress`에게 다음을 명령합니다.

- 동시에 100개의 연결(-n100)을 실행하도록 합니다.
- 각 연결이 T-SQL 스크립트를 50회(-r50) 실행하도록 설정합니다.


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


위의 ostress.exe 명령줄을 실행하려면:


1. 이전 실행으로 삽입된 모든 데이터를 삭제하도록 SSMS에서 다음 명령을 실행하여 데이터베이스 데이터 콘텐츠를 다시 설정합니다.

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. 이전 ostress.exe 명령줄의 텍스트를 클립보드에 복사합니다.

3. 매개 변수-S -U -P -d에 대한 `<placeholders>` 을(를) 올바른 실제 값으로 변경합니다.

4. 편집한 명령줄을 RML Cmd 창에서 실행합니다.


#### <a name="result-is-a-duration"></a>결과는 기간입니다


`ostress.exe`가 완료되면 출력의 마지막 줄로 실행 기간을 RML Cmd 창에 작성합니다. 예를 들어 짧은 테스트 실행은 약 1.5분 동안 지속됩니다.

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>*_ondisk*에 대해 다시 설정하고 편집한 다음 다시 실행합니다.


*_inmem* 실행에서 결과를 얻은 후 *_ondisk* 실행에 대해 다음 단계를 수행합니다.


1. 이전 실행으로 삽입된 모든 데이터를 삭제하도록 SSMS에서 다음 명령을 실행하여 데이터베이스를 다시 설정합니다.
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. ostress.exe 명령줄을 편집하여 모든 *_inmem*을 *_ondisk*로 대체합니다.

3. ostress.exe를 두 번째로 실행하고 기간 결과를 캡처합니다.

4. 많은 양의 테스트 데이터를 확실히 제거하기 위해 데이터베이스를 다시 설정합니다.


#### <a name="expected-comparison-results"></a>예상된 비교 결과

데이터베이스와 동일한 Azure 지역의 Azure VM에서 `ostress`를 실행할 때 이렇게 간단한 워크로드에서 메모리 내 테스트의 성능이 **9배**까지 향상되었습니다.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. 메모리 내 분석 샘플 설치


이 섹션에서는 columnstore 인덱스 및 전형적인 b-트리 인덱스를 사용하는 경우의 IO 및 통계 결과를 비교합니다.


OLTP 워크로드의 실시간 분석의 경우 비클러스터형 columnstore 인덱스를 사용하는 것이 좋습니다. 자세한 내용은 [설명한 Columnstore 인덱스](https://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.



### <a name="prepare-the-columnstore-analytics-test"></a>columnstore 분석 테스트 준비


1. Azure Portal을 사용하여 샘플에서 새로운 AdventureWorksLT 데이터베이스를 만듭니다.
   - 정확한 이름을 사용합니다.
   - Premium 서비스 계층을 선택합니다.

2. [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql)을 클립보드에 복사합니다.
   - T-SQL 스크립트는 1단계에서 만든 AdventureWorksLT 샘플 데이터베이스에서 필요한 메모리 내 개체를 만듭니다.
   - 스크립트는 차원 테이블과 두 개의 팩트 테이블을 만듭니다. 팩트 테이블은 각각 350만 개의 행으로 채워집니다.
   - 스크립트는 완료하는 데 15분 정도 걸릴 수 있습니다.

3. SSMS에 T-SQL 스크립트를 붙여 넣고 스크립트를 실행합니다. 다음과 같이 **CREATE INDEX** 문의 **COLUMNSTORE** 키워드가 중요합니다.<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT를 호환성 수준 130으로 설정합니다.<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    수준 130은 메모리 내 기능과 직접적으로 연관되지 않습니다. 하지만 수준 130은 일반적으로 120이 제공하는 것보다 빠른 쿼리 성능을 제공합니다.


#### <a name="key-tables-and-columnstore-indexes"></a>핵심 테이블 및 columnstore 인덱스


- dbo.FactResellerSalesXL_CCI는 클러스터형 columnstore 인덱스를 포함하는 테이블이고, 여기에는 *데이터* 수준의 고급 압축이 포함됩니다.

- dbo.FactResellerSalesXL_PageCompressed는 동등한 일반 클러스터형 인덱스를 가지며 *페이지* 수준에서만 압축된 테이블입니다.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>columnstore 인덱스를 비교하는 핵심 쿼리


성능 향상을 확인하도록 [실행할 수 있는 여러 유형의 T-SQL 쿼리](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql)가 있습니다. T-SQL 스크립트의 2단계에서 이 쿼리 쌍에 유의하세요. 두 쿼리는 한 줄만 다릅니다.


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


클러스터형 columnstore 인덱스는 FactResellerSalesXL\_CCI 테이블에 있습니다.

다음 T-SQL 스크립트 발췌는 각 테이블의 쿼리에 대한 IO 및 TIME에 대한 통계를 출력합니다.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

P2 가격 책정 계층의 데이터베이스에서 클러스터형 columnstore 인덱스를 사용하면 기존 인덱스와 비교하여 이 쿼리에 대해 약 9배의 성능 향상을 기대할 수 있습니다. P15에서는 columnstore 인덱스를 사용하여 약 57배의 성능 향상을 기대할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- [빠른 시작 1: 더 빠른 T-SQL 성능을 위한 메모리 내 OLTP 기술](https://msdn.microsoft.com/library/mt694156.aspx)

- [기존 Azure SQL 애플리케이션에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)

- 메모리 내 OLTP에 대한 [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md).


## <a name="additional-resources"></a>추가 리소스

#### <a name="deeper-information"></a>자세한 정보

- [쿼럼이 SQL Database의 메모리 내 OLTP을 사용하여 DTU를 70% 줄이는 동시에 키 데이터베이스의 워크로드를 두 배로 증가시키는 방법에 대해 알아보기](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Azure SQL Database의 메모리 내 OLTP 블로그 게시물](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [메모리 내 OLTP에 대해 알아보기](https://msdn.microsoft.com/library/dn133186.aspx)

- [columnstore 인덱스에 대해 알아보기](https://msdn.microsoft.com/library/gg492088.aspx)

- [실시간 운영 성과 분석에 대해 알아보기](https://msdn.microsoft.com/library/dn817827.aspx)

- [일반적인 워크로드 패턴 및 마이그레이션 고려 사항에 대한 백서](https://msdn.microsoft.com/library/dn673538.aspx)를 참조하세요. 여기에서 메모리 내 OLTP가 일반적으로 상당한 성능 향상을 제공하는 워크로드 패턴을 설명합니다.

#### <a name="application-design"></a>애플리케이션 설계

- [메모리 내 OLTP(메모리 내 최적화)](https://msdn.microsoft.com/library/dn133186.aspx)

- [기존 Azure SQL 애플리케이션에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>도구

- [Azure Portal](https://portal.azure.com/)

- [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)
