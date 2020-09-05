---
title: 테이블 인덱싱
description: Synapse SQL 풀의 테이블 인덱싱에 대 한 권장 사항 및 예제입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5ac32c41bd6b30c3edce68d67adc376e066d0bf5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797601"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Synapse SQL 풀의 테이블 인덱싱

Synapse SQL 풀의 테이블 인덱싱에 대 한 권장 사항 및 예제입니다.

## <a name="index-types"></a>인덱스 형식

Synapse SQL 풀은 [클러스터형 columnstore 인덱스](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [클러스터형 인덱스 및 비클러스터형 인덱스](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 비롯 한 여러 인덱싱 옵션과 [힙](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)이 라고도 하는 비 인덱스 옵션을 제공 합니다.  

인덱스가 있는 테이블을 만들려면 [CREATE TABLE (SYNAPSE SQL 풀)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 설명서를 참조 하세요.

## <a name="clustered-columnstore-indexes"></a>클러스터형 columnstore 인덱스

기본적으로 Synapse SQL 풀은 테이블에 인덱스 옵션이 지정 되지 않은 경우 클러스터형 columnstore 인덱스를 만듭니다. 클러스터형 columnstore 테이블은 가장 높은 수준의 데이터 압축 뿐만 아니라 전반적으로 최적의 쿼리 성능을 제공합니다.  클러스터형 columnstore 테이블은 일반적으로 클러스터형 인덱스 또는 힙 테이블보다 나은 성능을 제공하며 대형 테이블에 적합합니다.  이러한 이유로, 클러스터형 columnstore는 테이블 인덱싱 방법을 잘 모를 경우에 시작하기 가장 좋습니다.  

클러스터형 columnstore 테이블을 만들려면 WITH 절에 CLUSTERED COLUMNSTORE INDEX를 지정하거나 WITH 절을 제외합니다.

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

다음과 같은 일부 시나리오에서는 클러스터형 columnstore를 사용하는 것이 적합하지 않을 수 있습니다.

- Columnstore 테이블이 varchar(max), nvarchar(max) 및 varbinary(max)를 지원하지 않습니다. 대신 힙 또는 클러스터형 인덱스를 고려합니다.
- Columnstore 테이블이 임시 데이터에 대해 덜 효율적일 수 있습니다. 힙 및 임시 테이블을 고려합니다.
- 6000만 개 미만의 행이 포함 된 작은 테이블 힙 테이블을 고려합니다.

## <a name="heap-tables"></a>힙 테이블

Synapse SQL 풀에서 일시적으로 데이터를 방문 하는 경우 힙 테이블을 사용 하면 전체 프로세스를 더 빠르게 수행할 수 있습니다. 즉, 힙에 로드하는 것이 인덱스 테이블에 로드하는 것보다 더 빠르며 경우에 따라 캐시에서 후속 읽기가 수행될 수도 있습니다.  더 많은 변환을 실행하기 전에 준비만을 위해 데이터를 로드하는 경우 테이블을 힙 테이블에 로드하면 데이터를 클러스터형 columnstore 테이블에 로드할 때보다 훨씬 빠릅니다. 또한 데이터를 [임시 테이블](sql-data-warehouse-tables-temporary.md)에 로드하면 테이블을 영구 스토리지에 로드하는 것보다 빠릅니다.  데이터를 로드 한 후 쿼리 성능을 향상 시키기 위해 테이블에 인덱스를 만들 수 있습니다.  

6000만 개 이상의 행이 있는 경우 클러스터 columnstore 테이블은 최적의 압축을 얻기 시작 합니다.  6000만 행 미만의 작은 조회 테이블의 경우 더 빠른 쿼리 성능을 위해 힙 또는 클러스터형 인덱스를 사용 하는 것이 좋습니다. 

힙 테이블을 만들려면 WITH 절에서 HEAP을 지정하면 됩니다.

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>클러스터형 및 비클러스터형 인덱스

클러스터형 인덱스는 단일 행을 빠르게 검색해야 하는 경우 클러스터형 columnstore 테이블보다 더 나은 성능을 제공할 수 있습니다. 단일 또는 매우 적은 수의 행 조회가 매우 빠른 속도로 수행 되어야 하는 쿼리의 경우 클러스터 인덱스 또는 비클러스터형 보조 인덱스를 고려 합니다. 클러스터형 인덱스를 사용할 때의 단점은 클러스터형 인덱스 열에 고도의 선택 필터를 사용하는 쿼리에만 도움이 된다는 것입니다. 다른 열에 대한 필터를 향상시키기 위해 비클러스터형 인덱스를 다른 열에 추가할 수 있습니다. 그러나 테이블에 각 인덱스가 추가될 때마다 차지하는 공간이 늘어나고 로드 처리 시간도 늘어납니다.

클러스터형 인덱스 테이블을 만들려는 경우 WITH 절에서 CLUSTERED INDEX를 지정하면 됩니다.

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

테이블에 비클러스터형 인덱스를 추가 하려면 다음 구문을 사용 합니다.

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>클러스터형 columnstore 인덱스 최적화

클러스터형 columnstore 테이블은 데이터가 세그먼트로 구성됩니다.  columnstore 테이블에 대해 최적의 쿼리 성능을 구현하려면 높은 세그먼트 품질을 유지하는 것이 중요합니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  세그먼트 품질은 압축된 행 그룹마다 100,000개 이상의 행이 있을 때 가장 최적 상태가 되며, 행 그룹당 행 수가 행 그룹이 포함할 수 있는 최대 수인 1,048,576개일 때 성능이 향상됩니다.

아래 뷰를 만들어 시스템에서 행 그룹당 평균 행 수를 계산하고 차선에 해당하는 클러스터 columnstore 인덱스를 식별하는 데 사용할 수 있습니다.  이 뷰의 마지막 열은 인덱스를 다시 구성하는 데 사용할 수 있는 SQL 문으로 생성됩니다.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

>[!TIP]
> Synapse SQL의 성능 향상을 위해 영구 사용자 테이블에서 **pdw_table_mappings** 대신 **pdw_permanent_table_mappings** 를 사용 하는 것이 좋습니다. 자세한 내용은 **[pdw_permanent_table_mappings &#40;transact-sql&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=azure-sqldw-latest)** 을 참조 하십시오.

뷰를 만들었으므로 이제 다음 쿼리를 실행하여 행 수가 100,000개 미만인 행 그룹이 있는 테이블을 식별합니다. 물론 좀 더 최적 상태의 세그먼트 품질을 원할 경우 임계값인 100,000개를 늘릴 수도 있습니다.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

쿼리를 실행했으면 데이터를 확인하고 결과를 분석할 수 있습니다. 다음 표는 행 그룹 분석에서 확인할 내용에 대해 설명합니다.

| 열 | 이 데이터를 사용하는 방법 |
| --- | --- |
| [table_partition_count] |테이블이 분할되어 있으면 열려 있는 행 그룹의 숫자가 더 많을 것입니다. 이론상으로는 분산의 각 파티션에 열려 있는 행 그룹이 연결될 수 있습니다. 분석할 때 이 점을 고려해야 합니다. 분할된 작은 테이블의 경우 압축 성능이 향상되도록 파티션을 모두 제거하여 최적화할 수 있습니다. |
| [row_count_total] |테이블에 대한 전체 행 개수입니다. 예를 들어 압축된 상태에서 행의 비율을 계산하는 데 이 값을 사용할 수 있습니다. |
| [row_count_per_distribution_MAX] |모든 행이 균등하게 분산되는 경우 이 값은 분산당 대상 행 수가 됩니다. 이 값을 compressed_rowgroup_count와 비교해 보세요. |
| [COMPRESSED_rowgroup_rows] |테이블에서 columnstore 형식의 총 행 수 |
| [COMPRESSED_rowgroup_rows_AVG] |평균 행 수가 행 그룹의 최대 행 수보다 훨씬 적은 경우에는 CTAS 또는 ALTER INDEX REBUILD를 사용하여 데이터를 다시 압축하는 옵션을 고려해야 합니다. |
| [COMPRESSED_rowgroup_count] |columnstore 형식인 행 그룹의 수. 테이블에 비해 이 숫자가 매우 높으면 columnstore 밀도가 낮다는 뜻입니다. |
| [COMPRESSED_rowgroup_rows_DELETED] |행이 columnstore 형식으로 논리적으로 삭제됩니다. 테이블 크기에 비해 이 숫자가 더 높으면 행이 실제로 제거되도록 파티션을 다시 만들거나 인덱스를 다시 작성하는 옵션을 고려해 보아야 합니다. |
| [COMPRESSED_rowgroup_rows_MIN] |이 열을 AVG 및 MAX 열과 함께 사용하여 columnstore의 행 그룹에 대한 값 범위를 이해할 수 있습니다. 이 숫자가 로드 임계값(파티션 정렬 분산당 102,400)보다 낮으면 데이터 로드에서 최적화를 사용할 수 있습니다. |
| [COMPRESSED_rowgroup_rows_MAX] |위와 동일합니다. |
| [OPEN_rowgroup_count] |열려 있는 행 그룹은 정상입니다. 테이블 분산당(60) 행 그룹이 하나 열려 있다고 예상하는 것이 합리적일 것입니다. 이 숫자가 과도하게 높으면 여러 파티션에 데이터를 로드한다는 뜻입니다. 이 숫자가 적절하게 유지되도록 분할 전략을 확인하세요. |
| [OPEN_rowgroup_rows] |각 행 그룹은 최대 1,048,576개 행을 포함할 수 있습니다. 이 값을 사용하여 현재 열려 있는 행 그룹이 얼마나 찼는지 확인할 수 있습니다. |
| [OPEN_rowgroup_rows_MIN] |열려 있는 그룹은 데이터가 테이블로 서서히 로드되고 있거나 이전 부하가 나머지 행을 다 채우고 이 행 그룹까지 넘어 왔음을 뜻합니다. MIN, MAX, AVG 열을 사용하여 열려 있는 행 그룹에 데이터가 얼마나 있는지 확인할 수 있습니다. 작은 테이블의 경우 모든 데이터가 100%일 수 있습니다! 이 경우에는 ALTER INDEX REBUILD를 사용하여 데이터를 강제로 columnstore로 만들어야 합니다. |
| [OPEN_rowgroup_rows_MAX] |위와 동일합니다. |
| [OPEN_rowgroup_rows_AVG] |위와 동일합니다. |
| [CLOSED_rowgroup_rows] |닫힌 행 그룹이 있는 경우 |
| [CLOSED_rowgroup_count] |닫힌 행 그룹이 있더라도 그 수가 적어야 합니다. ALTER INDEX ...를 사용 하 여 닫힌 행 그룹을 압축 된 행 그룹으로 변환할 수 있습니다. 다시 구성 명령입니다. 그러나 일반적으로는 이 작업이 필요하지 않습니다. 닫힌 그룹은 백그라운드 "튜플 이동기" 프로세스를 통해 자동으로 columnstore 행 그룹으로 변환됩니다. |
| [CLOSED_rowgroup_rows_MIN] |닫힌 행 그룹의 채우기 속도가 매우 높아야 합니다. 닫힌 행 그룹의 채우기 속도가 낮으면 columnstore의 추가 분석이 필요합니다. |
| [CLOSED_rowgroup_rows_MAX] |위와 동일합니다. |
| [CLOSED_rowgroup_rows_AVG] |위와 동일합니다. |
| [Rebuild_Index_SQL] |테이블에 대해 columnstore 인덱스를 다시 작성하기 위한 SQL |

## <a name="causes-of-poor-columnstore-index-quality"></a>columnstore 인덱스 품질 저하의 원인

세그먼트 품질이 저하된 테이블을 식별한 경우 근본 원인을 확인할 수 있습니다.  다음은 세그먼트 품질 저하의 일반적인 몇 가지 원인입니다.

1. 인덱스를 작성했을 때 메모리 부족
2. 많은 양의 DML 작업
3. 작거나 지속적인 로드 작업
4. 너무 많은 파티션

다음 요소로 인해 columnstore 인덱스가 행 그룹당 1백만 개보다 훨씬 적은 행을 가질 수 있으며 행이 압축된 행 그룹 대신 델타 행 그룹으로 이동할 수도 있습니다.

### <a name="memory-pressure-when-index-was-built"></a>인덱스를 작성했을 때 메모리 부족

압축된 행 그룹당 행 수는 행의 너비와 행 그룹을 처리하는 데 사용할 수 있는 메모리 양에 직접적으로 관련되어 있습니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  따라서 columnstore 인덱스가 테이블에 쓰는 세션에 가능한 한 많은 메모리에 대한 액세스 권한을 부여하는 것이 가장 좋습니다.  메모리 및 동시성은 서로 상쇄되므로 적합한 메모리 할당 지침은 테이블의 각 행에 포함된 데이터, 시스템에 할당한 데이터 웨어하우스 단위 및 테이블에 데이터를 쓰는 세션에 제공할 수 있는 동시 슬롯 수에 따라 좌우됩니다.

### <a name="high-volume-of-dml-operations"></a>많은 양의 DML 작업

행을 업데이트 또는 삭제하는 많은 고용량 DML 작업은 columnstore에 비효율적일 수 있습니다. 특히 행 그룹 내 대부분의 행이 수정될 때 더욱 그렇습니다.

- 압축 행 그룹에서 하나의 행을 삭제하는 것은 논리적으로 행을 삭제된 것으로 표시하는 것입니다. 이 행은 파티션 또는 테이블이 다시 빌드될 때까지 압축 행 그룹에 남아 있습니다.
- 행 삽입은 행을 델타 행 그룹이라 불리는 내부 rowstore 테이블에 추가합니다. 삽입된 행은 델타 행 그룹이 꽉 차서 닫힌 것으로 표시될 때까지 columnstore로 변환되지 않습니다. 행 그룹은 최대 용량인 1,048,576개 행에 도달하면 닫힙니다.
- Columnstore 형식에서 행을 업데이트하면 논리적 삭제 후 삽입으로 처리됩니다. 삽입된 행은 델타 저장소에 저장될 수 있습니다.

파티션 정렬 배포당 102,400개 행의 대량 임계값을 초과하는 일괄 처리된 업데이트 및 삽입 작업은 columnstore 형식으로 직접 기록됩니다. 그러나 균일한 배포를 가정하여 한 작업에서 614만 4천 개 이상의 행을 수정해야 할 수 있습니다. 지정 된 파티션 정렬 분포의 행 수가 102400 보다 작은 경우 행이 델타 저장소로 이동 하 고 행 그룹을 닫기 위해 충분 한 행이 삽입 또는 수정 되거나 인덱스가 다시 작성 될 때까지 해당 위치에 유지 됩니다.

### <a name="small-or-trickle-load-operations"></a>작거나 지속적인 로드 작업

Synapse SQL 풀로 이동 하는 작은 로드를 trickle 로드 라고도 합니다. 이들은 일반적으로 시스템에 의해 수집되는 거의 일정한 데이터 스트림을 나타냅니다. 그러나 이 스트림은 거의 지속적이므로 행의 볼륨은 특별히 크지 않습니다. 종종 데이터가 columnstore 형식으로 직접 로드하는 데 필요한 임계값보다 훨씬 큽니다.

이러한 상황에서는 데이터를 로드하기 전에 Azure Blob Storage에 먼저 데이터를 배치하고 누적되도록 합니다. 이 기술을 *마이크로 일괄 처리*라고 합니다.

### <a name="too-many-partitions"></a>너무 많은 파티션

클러스터형 columnstore 테이블에 분할이 미치는 영향도 고려해야 합니다.  분할 하기 전에 Synapse SQL pool은 이미 데이터를 60 데이터베이스로 나눕니다.  분할을 수행하면 데이터가 좀 더 세분화됩니다.  데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 **각** 파티션에는 1백만 개 이상의 행을 포함하는 것이 좋습니다.  테이블을 100 파티션으로 분할 하는 경우 클러스터형 columnstore 인덱스 (60 배포판 *100 파티션* 100만 행)를 활용 하려면 테이블에 60억 행 이상이 필요 합니다. 100개 파티션 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.

테이블에 일부 데이터가 로드된 경우 아래 단계에 따라 테이블을 식별한 후 차선에 해당하는 클러스터형 columnstore 인덱스로 테이블을 다시 작성합니다.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>인덱스를 다시 작성하여 세그먼트 품질 개선

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1단계: 적합한 리소스 클래스를 사용하는 사용자 식별 또는 만들기

세그먼트 품질을 즉시 개선하는 한 가지 빠른 방법은 인덱스를 다시 작성하는 것입니다.  위의 보기에서 반환된 SQL은 인덱스를 다시 작성하는 데 사용할 수 있는 ALTER INDEX REBUILD 문을 반환합니다. 인덱스를 다시 작성하는 경우 인덱스를 다시 작성할 세션에 충분한 메모리를 할당해야 합니다.  이렇게 하려면 이 테이블의 인덱스를 다시 작성하기 위한 권한이 있는 사용자의 리소스 클래스를 권장되는 최소 수로 늘립니다.

다음은 사용자의 리소스 클래스를 늘려 사용자에게 더 많은 메모리를 할당하는 방법의 예입니다. 리소스 클래스를 사용하려면 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2단계: 더 높은 리소스 클래스 사용자를 사용하여 클러스터형 columnstore 인덱스 다시 작성

1 단계의 사용자 (예: LoadUser)로 로그인 합니다 .이 사용자는 이제 더 높은 리소스 클래스를 사용 하 고 ALTER INDEX 문을 실행 합니다. 이 사용자가 인덱스를 다시 작성하려는 테이블에 대한 ALTER 권한이 있는지 확인합니다. 이 예제에서는 전체 columnstore 인덱스 또는 단일 파티션을 다시 빌드하는 방법을 보여 줍니다. 대형 테이블에서는 한 번에 파티션 하나에 대해 인덱스를 다시 빌드하는 것이 실용적입니다.

또는 인덱스를 다시 작성 하는 대신 [CTAS를 사용 하 여](sql-data-warehouse-develop-ctas.md)테이블을 새 테이블에 복사할 수 있습니다. 어떤 방식이 적합할까요? 데이터 양이 많은 경우 일반적으로 CTAS가 [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)보다 빠릅니다. 더 작은 볼륨의 데이터에서는 ALTER INDEX를 사용하기가 더 쉬우며 테이블도 스왑할 필요가 없습니다.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Synapse SQL 풀에서 인덱스를 다시 작성 하는 작업은 오프 라인 작업입니다.  인덱스를 다시 빌드하는 방법에 대한 자세한 내용은 [Columnstore 인덱스 조각 모음](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)의 ALTER INDEX REBUILD 섹션을 참조하세요.

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3단계: 클러스터형 columnstore 세그먼트 품질이 향상되었는지 확인

세그먼트 품질이 저하된 테이블을 식별하는 쿼리를 다시 실행하고 세그먼트 품질이 향상되었는지 확인합니다.  세그먼트 품질이 개선되지 않은 경우 테이블의 행이 아주 넓은 것일 수 있습니다.  인덱스를 다시 작성할 때 더 높은 리소스 클래스 또는 DWU를 사용하는 것이 좋습니다.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>CTAS 및 파티션 전환을 사용하여 인덱스 다시 빌드

이 예제에서는 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 문 및 파티션 전환을 사용하여 테이블 파티션을 다시 작성합니다.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

CTAS를 사용 하 여 파티션을 다시 만드는 방법에 대 한 자세한 내용은 [SYNAPSE SQL 풀에서 파티션 사용](sql-data-warehouse-tables-partition.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

테이블 개발에 대한 자세한 내용은 [테이블 개발](sql-data-warehouse-tables-overview.md)을 참조하세요.
