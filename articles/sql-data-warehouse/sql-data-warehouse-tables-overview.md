---
title: 테이블 디자인 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse의 테이블 디자인을 소개합니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1073e1b4ad38c4b05c9195cf4ea16ade7416fbce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474976"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse의 테이블 디자인

Azure SQL Data Warehouse의 테이블 디자인에 대한 주요 개념을 알아봅니다. 

## <a name="determine-table-category"></a>테이블 범주 확인 

[스타 스키마](https://en.wikipedia.org/wiki/Star_schema)는 데이터를 팩트 및 차원 테이블로 구성합니다. 일부 테이블은 팩트 또는 차원 테이블로 이동하기 전의 통합 또는 준비 데이터에 사용됩니다. 테이블을 디자인할 때 테이블 데이터가 팩트, 차원 또는 통합 테이블에 속하는지를 결정합니다. 이 결정은 적절한 테이블 구조 및 배포를 알려줍니다. 

- **팩트 테이블**에는 일반적으로 트랜잭션 시스템에서 생성된 다음, 데이터 웨어하우스에 로드된 정량적 데이터가 있습니다. 예를 들어 소매점에서 판매 트랜잭션을 매일 생성한 다음 분석을 위해 데이터 웨어하우스의 팩트 테이블에 데이터를 로드합니다.

- **차원 테이블**에는 변경될 수 있지만 일반적으로 드물게 변경되는 특성 데이터가 있습니다. 예를 들어 고객의 이름과 주소는 차원 테이블에 저장되고, 고객 프로필이 변경될 때만 업데이트됩니다. 대형 팩트 테이블의 크기를 최소화하기 위해 고객의 이름과 주소는 팩트 테이블의 모든 행에 있지 않아도 됩니다. 대신, 팩트 테이블과 차원 테이블에서 고객 ID를 공유할 수 있습니다. 쿼리는 두 테이블을 조인하여 고객 프로필과 트랜잭션을 연결할 수 있습니다. 

- **통합 테이블**에서는 데이터를 통합하거나 준비할 수 있습니다. 통합 테이블을 일반 테이블, 외부 테이블 또는 임시 테이블로 만들 수 있습니다. 예를 들어 준비 테이블에 데이터를 로드하고 준비 중인 데이터에 대한 변환을 수행한 다음 프로덕션 테이블에 데이터를 삽입할 수 있습니다.

## <a name="schema-and-table-names"></a>스키마 및 테이블 이름
스키마는 테이블 그룹을 비슷한 방식으로 함께 사용 하는 좋은 방법입니다.  마이그레이션하려는 경우 여러 데이터베이스 온-프레미스 솔루션에서 SQL Data warehouse, SQL Data Warehouse의 한 스키마로 모든 팩트, 차원 및 통합 테이블을 마이그레이션하도록 적합 합니다. 예를 들어 [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) 샘플 데이터 웨어하우스의 모든 테이블을 wwi라는 하나의 스키마 내에 저장할 수 있습니다. 다음 코드에서는 wwi라는 [사용자 정의 스키마](/sql/t-sql/statements/create-schema-transact-sql)를 만듭니다.

```sql
CREATE SCHEMA wwi;
```

SQL Data Warehouse의 테이블 구성을 표시하려면 fact, dim 및 int를 테이블 이름의 접두사로 사용할 수 있습니다. 다음 표는 WideWorldImportersDW의 일부 스키마 및 테이블 이름을 보여 줍니다.  

| WideWorldImportersDW 테이블  | 테이블 형식 | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| City | 차원 | wwi.DimCity |
| 순서 | 팩트 | wwi.FactOrder |


## <a name="table-persistence"></a>테이블 지속성 

테이블의 데이터는 Azure Storage에 영구적으로 또는 일시적으로 저장되거나 데이터 웨어하우스 외부의 데이터 저장소에 저장됩니다.

### <a name="regular-table"></a>일반 테이블

일반 테이블의 데이터는 데이터 웨어하우스의 일부로 Azure Storage에 저장됩니다. 세션이 열려 있는지 여부에 관계없이 테이블과 데이터가 지속됩니다.  다음 예제에서는 두 개의 열이 있는 일반 테이블을 만듭니다. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>임시 테이블
임시 테이블은 세션 기간 동안만 유지됩니다. 다른 사용자가 임시 결과 표시 하지 못하도록 설정 및 정리에 대 한 필요성을 줄이기 위해 임시 테이블을 사용할 수 있습니다.  임시 테이블 빠른 성능을 제공 하기 위해 로컬 저장소를 활용 합니다.  자세한 내용은 [임시 테이블](sql-data-warehouse-tables-temporary.md)을 참조하세요.

### <a name="external-table"></a>외부 테이블
외부 테이블은 Azure Storage Blob 또는 Azure Data Lake Store에 있는 데이터를 가리킵니다. CREATE TABLE AS SELECT 문과 함께 사용하는 경우 외부 테이블에서 선택하면 데이터를 SQL Data Warehouse로 가져옵니다. 따라서 외부 테이블은 데이터 로드에 유용합니다. 로드 자습서는 [PolyBase를 사용하여 Azure Blob Storage에서 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)를 참조하세요.

## <a name="data-types"></a>데이터 형식
SQL Data Warehouse는 가장 일반적으로 사용되는 데이터 형식을 지원합니다. 지원되는 데이터 형식의 목록은 CREATE TABLE 문의 [CREATE TABLE 참조의 데이터 형식](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)을 참조하세요. 데이터 형식 사용에 대한 지침은 [데이터 형식](sql-data-warehouse-tables-data-types.md)을 참조하세요.

## <a name="distributed-tables"></a>분산 테이블
SQL Data Warehouse의 기본 기능은 방법이 저장 하 고에서 테이블을 작업할 수 있습니다 [배포판](massively-parallel-processing-mpp-architecture.md#distributions)합니다.  SQL Data Warehouse는 데이터, 라운드 로빈 (기본값), 해시 배포에 대 한 세 가지 메서드를 지원 하 고 복제 합니다.

### <a name="hash-distributed-tables"></a>해시 분산 테이블
해시 분산된 테이블은 행 분산 열의 값을 기반으로 합니다. 해시 분산된 테이블은 대형 테이블에서 쿼리에 대 한 높은 성능을 달성 하도록 설계 되었습니다. 배포 열을 선택할 때 고려해 야 할 여러 요인이 있습니다. 

자세한 내용은 [분산 테이블에 대한 디자인 지침](sql-data-warehouse-tables-distribute.md)을 참조하세요.

### <a name="replicated-tables"></a>복제된 테이블
복제된 테이블에는 모든 계산 노드에서 사용할 수 있는 테이블의 전체 복사본이 있습니다. 복제된 테이블을 조인할 때는 데이터를 이동할 필요가 없으므로 복제된 테이블에 대한 쿼리는 빠르게 실행됩니다. 하지만 복제 시 추가 저장소가 필요하며 대형 테이블에는 비효율적입니다. 

자세한 내용은 [복제된 테이블에 대한 디자인 지침](design-guidance-for-replicated-tables.md)을 참조하세요.

### <a name="round-robin-tables"></a>라운드 로빈 테이블
라운드 로빈 테이블은 테이블 행을 모든 분산에서 균일하게 배포합니다. 행은 무작위로 분산됩니다. 라운드 로빈 테이블에 대한 데이터 로드는 빠릅니다.  그러나 쿼리에는 다른 배포 방법보다 더 많은 데이터 이동이 필요할 수 있습니다. 

자세한 내용은 [분산 테이블에 대한 디자인 지침](sql-data-warehouse-tables-distribute.md)을 참조하세요.

### <a name="common-distribution-methods-for-tables"></a>테이블에 대한 일반적인 분산 방법
테이블 범주에 따라 선택할 테이블 배포 옵션이 결정되는 경우가 많습니다. 

| 테이블 범주 | 권장 배포 옵션 |
|:---------------|:--------------------|
| 팩트           | 클러스터형 columnstore 인덱스와 함께 해시 배포를 사용합니다. 동일한 배포 열에서 두 해시 테이블을 조인하면 성능이 향상됩니다. |
| 차원      | 작은 테이블에는 복제를 사용합니다. 테이블이 너무 커서 각 계산 노드에 저장할 수 없는 경우 해시 분산을 사용합니다. |
| 스테이징        | 준비 테이블에는 라운드 로빈을 사용합니다. CTAS를 사용하면 빠르게 로드됩니다. 준비 테이블에 데이터가 있으면 INSERT를 사용 하는 중... 프로덕션 테이블에 데이터를 이동 하려면 선택 합니다. |

## <a name="table-partitions"></a>테이블 파티션
분할된 테이블은 데이터 범위에 따라 테이블 행에 저장하고 작업을 수행합니다. 예를 들어 테이블을 일, 월 또는 연도별로 분할할 수 있습니다. 쿼리 검색을 파티션 내의 데이터로 제한하는 파티션 제거를 통해 쿼리 성능을 향상시킬 수 있습니다. 파티션 전환을 통해 데이터를 유지 관리할 수도 있습니다. SQL Data Warehouse의 데이터는 이미 분산되어 있으므로 파티션이 너무 많으면 쿼리 성능이 느려질 수 있습니다. 자세한 내용은 [분할 지침](sql-data-warehouse-tables-partition.md)을 참조하세요.  비어 있지 않은 파티션 테이블로 전환는 파티션의 경우, TRUNCATE_TARGET 옵션을 사용 하는 것이 좋습니다에 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) 문 경우 기존 데이터가 잘릴 수 있습니다. 모든 기존 데이터를 덮어쓰지 SalesFact에 변환 된 일일 데이터에서 코드 스위치 아래. 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Columnstore 인덱스
기본적으로 SQL Data Warehouse는 테이블을 클러스터형 columnstore 인덱스로 저장합니다. 이러한 형태의 데이터 저장소는 대형 테이블에서 데이터 압축률과 쿼리 성능이 높습니다.  일반적으로 클러스터형 columnstore 인덱스가 가장 좋은 옵션이지만 클러스터형 인덱스 또는 힙이 적절한 저장소 구조인 경우도 있습니다.  힙 테이블은 최종 테이블을 변환 되는 준비 테이블 같은 임시 데이터를 로드 하는 것에 대 한 특히 유용할 수 있습니다.

columnstore 기능 목록은 [columnstore 인덱스의 새로운 기능](/sql/relational-databases/indexes/columnstore-indexes-what-s-new)을 참조하세요. columnstore 인덱스 성능을 향상하려면 [columnstore 인덱스의 행 그룹 품질 최대화](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)를 참조하세요.

## <a name="statistics"></a>통계
쿼리 최적화 프로그램은 쿼리 실행 계획을 만들 때 열 수준 통계를 사용합니다. 가 쿼리 성능 향상을 위해 쿼리 조인에 사용 된 열에 특히 개별 열에 대 한 통계가 해야 합니다. [통계를 만드는](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics) 자동으로 수행 합니다.  그러나 통계를 업데이트 하는 중 자동으로 이루어지지는지 않습니다. 많은 행을 추가하거나 변경한 후에는 통계를 업데이트합니다. 예를 들어 로드 후 통계를 업데이트합니다. 자세한 내용은 [통계 가이드](sql-data-warehouse-tables-statistics.md)를 참조하세요.

## <a name="commands-for-creating-tables"></a>테이블을 만드는 명령
테이블을 새로운 빈 테이블로 만들 수 있습니다. 테이블을 만들고 select 문의 결과로 채울 수도 있습니다. 다음은 테이블을 만드는 T-SQL 명령입니다.

| T-SQL 문 | 설명 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | 모든 테이블 열과 옵션을 정의하여 빈 테이블을 만듭니다. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | 외부 테이블을 만듭니다. 테이블 정의는 SQL Data Warehouse에 저장됩니다. 테이블 데이터는 Azure Blob Storage 또는 Azure Data Lake Store에 저장됩니다. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | select 문의 결과로 새 테이블을 채웁니다. 테이블 열과 데이터 형식은 select 문의 결과를 기반으로 합니다. 데이터를 가져오기 위해 이 문은 외부 테이블에서 선택할 수 있습니다. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | select 문의 결과를 외부 위치로 내보내 새 외부 테이블을 만듭니다.  위치는 Azure Blob Storage 또는 Azure Data Lake Store입니다. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>원본 데이터를 데이터 웨어하우스에 맞춤

데이터 웨어하우스 테이블은 다른 데이터 원본의 데이터를 로드하여 채워집니다. 로드를 성공적으로 수행하려면 원본 데이터에 있는 열의 개수와 데이터 형식이 데이터 웨어하우스의 테이블 정의와 일치해야 합니다. 맞출 데이터를 가져오는 것이 테이블 디자인의 가장 어려운 부분일 수 있습니다. 

여러 데이터 저장소에서 데이터를 가져오는 경우 데이터 웨어하우스로 데이터를 가져와 통합 테이블에 저장할 수 있습니다. 데이터가 통합 테이블에 저장되면 SQL Data Warehouse의 기능을 사용하여 변환 작업을 수행할 수 있습니다. 데이터가 준비되면 프로덕션 테이블에 삽입할 수 있습니다.

## <a name="unsupported-table-features"></a>지원되지 않는 테이블 기능
SQL Data Warehouse는 다른 데이터베이스에서 제공하는 테이블 기능을 모두는 아니지만 대부분 지원합니다.  다음 목록은 SQL Data Warehouse에서 지원되지 않는 일부 테이블 기능을 보여 줍니다.

- PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK [테이블 제약 조건](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [계산된 열](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [인덱싱된 뷰](/sql/relational-databases/views/create-indexed-views)
- [시퀀스](/sql/t-sql/statements/create-sequence-transact-sql)
- [스파스 열](/sql/relational-databases/tables/use-sparse-columns)
- 서로게이트 키. [ID](sql-data-warehouse-tables-identity.md)로 구현합니다.
- [동의어](/sql/t-sql/statements/create-synonym-transact-sql)
- [트리거](/sql/t-sql/statements/create-trigger-transact-sql)
- [고유 인덱스](/sql/t-sql/statements/create-index-transact-sql)
- [사용자 정의 형식](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>테이블 크기 쿼리
60개의 각 배포에서 한 테이블에 사용되는 공간 및 행을 식별하는 한 가지 간단한 방법은 [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql)를 사용하는 것입니다.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

그러나 DBCC 명령을 사용하면 작업이 상당히 제한될 수 있습니다.  DMV(동적 관리 뷰)는 DBCC 명령보다 자세한 내용을 표시합니다. 먼저 이 뷰를 만듭니다.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>테이블 공간 요약

이 쿼리는 테이블에 의해 행 및 공간을 반환합니다.  가장 큰 테이블이 어느 테이블인지, 해당 테이블이 라운드 로빈, 복제 또는 해시 분산인지 확인할 수 있습니다.  해시 분산 테이블의 경우 쿼리에서 배포 열을 보여 줍니다.  

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>배포 유형별 테이블 공간

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>인덱스 유형별 테이블 공간

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>배포 공간 요약

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>다음 단계
데이터 웨어하우스에 대한 테이블을 만든 후 다음 단계는 테이블에 데이터를 로드하는 것입니다.  로드에 대한 자습서는 [SQL Data Warehouse에 데이터 로드](load-data-wideworldimportersdw.md)를 참조하세요.
