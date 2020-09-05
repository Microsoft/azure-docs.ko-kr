---
title: 순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정
description: 정렬 된 클러스터형 columnstore 인덱스를 사용 하 여 쿼리 성능을 향상 시킬 때 알아야 할 권장 사항 및 고려 사항입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 454e205904b3623bdb5adc906465f01abd77092a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795612"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정  

사용자가 Synapse SQL 풀에서 columnstore 테이블을 쿼리하면 최적화 프로그램은 각 세그먼트에 저장 된 최소값과 최 댓 값을 확인 합니다.  쿼리 조건자의 범위 밖에 있는 세그먼트는 디스크에서 메모리로 읽지 않습니다.  읽을 세그먼트 수와 총 크기가 작은 경우 쿼리는 더 빠른 성능을 얻을 수 있습니다.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>정렬 된 클러스터형 columnstore 인덱스 및 순서가 지정 되지 않은 클러스터형 columnstore 인덱스

기본적으로 인덱스 옵션 없이 생성 된 각 테이블에 대해 내부 구성 요소 (인덱스 작성기)는 순서가 지정 되지 않은 클러스터형 columnstore 인덱스 (CCI)를 만듭니다.  각 열의 데이터는 별도의 CCI 행 그룹 세그먼트로 압축 됩니다.  각 세그먼트의 값 범위에 대 한 메타 데이터가 있으므로 쿼리 조건자의 범위 밖에 있는 세그먼트는 쿼리 실행 중에 디스크에서 읽지 않습니다.  CCI는 가장 높은 수준의 데이터 압축을 제공 하 고 읽을 세그먼트 크기를 줄여 쿼리가 더 빠르게 실행 될 수 있도록 합니다. 그러나 인덱스 작성기가 데이터를 세그먼트로 압축 하기 전에 정렬 하지 않으므로 값 범위가 겹치는 세그먼트가 발생 하 여 디스크에서 더 많은 세그먼트를 읽고 쿼리가 완료 되는 데 시간이 오래 걸릴 수 있습니다.  

정렬 된 CCI를 만들 때 Synapse SQL 엔진은 인덱스 작성기가 인덱스 세그먼트로 압축 하기 전에 순서 키를 기준으로 메모리의 기존 데이터를 정렬 합니다.  정렬 된 데이터를 사용 하는 경우 쿼리를 더 효율적으로 분할 하 여 디스크에서 읽을 세그먼트 수가 줄어들기 때문에 더 효율적으로 세그먼트를 제거 하 여 성능을 향상 시킬 수 있습니다.  모든 데이터를 한 번에 메모리에서 정렬할 수 있는 경우 세그먼트 겹치기를 방지할 수 있습니다.  데이터 웨어하우스의 테이블이 많기 때문에이 시나리오는 자주 발생 하지 않습니다.  

열에 대 한 세그먼트 범위를 확인 하려면 테이블 이름 및 열 이름으로 다음 명령을 실행 합니다.

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

>[!TIP]
> Synapse SQL의 성능 향상을 위해 영구 사용자 테이블에서 **pdw_table_mappings** 대신 **pdw_permanent_table_mappings** 를 사용 하는 것이 좋습니다. 자세한 내용은 **[pdw_permanent_table_mappings &#40;transact-sql&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=azure-sqldw-latest)** 을 참조 하십시오.

> [!NOTE] 
> 순서가 지정 된 CCI 테이블에서 동일한 DML 또는 데이터 로드 작업 일괄 처리로 생성 되는 새 데이터는 해당 일괄 처리 내에서 정렬 되며 테이블의 모든 데이터에 대해 전역 정렬이 수행 되지 않습니다.  사용자는 정렬 된 CCI를 다시 작성 하 여 테이블의 모든 데이터를 정렬할 수 있습니다.  Synapse SQL에서 columnstore 인덱스 다시 작성은 오프 라인 작업입니다.  분할 된 테이블의 경우 다시 작성은 한 번에 하나의 파티션으로 수행 됩니다.  다시 작성 되는 파티션의 데이터는 "오프 라인" 이며 해당 파티션에 대해 다시 작성이 완료 될 때까지 사용할 수 없습니다. 

## <a name="query-performance"></a>쿼리 성능

순서가 지정 된 CCI의 쿼리 성능 향상은 쿼리 패턴, 데이터 크기, 데이터 정렬 방법, 세그먼트의 물리적 구조 및 쿼리 실행을 위해 선택한 DWU 및 리소스 클래스에 따라 다릅니다.  사용자는 정렬 된 CCI 테이블을 디자인할 때 순서 열을 선택 하기 전에 이러한 모든 요인을 검토 해야 합니다.

이러한 모든 패턴의 쿼리는 일반적으로 순서가 지정 된 CCI를 사용 하 여 더 빠르게 실행 됩니다.  
1. 쿼리에 같음, 같지 않음 또는 범위 조건자가 있습니다.
1. 조건자 열과 정렬 된 CCI 열은 동일 합니다.  
1. 조건자 열은 정렬 된 CCI 열의 열 서 수와 동일한 순서로 사용 됩니다.  
 
이 예에서 테이블 t 1에는 Col_C, Col_B 및 Col_A 순서로 정렬 된 클러스터형 columnstore 인덱스가 있습니다.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

쿼리 1의 성능은 다른 세 개의 쿼리 보다 순서가 지정 된 CCI 보다 더 유용할 수 있습니다. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>데이터 로드 성능

정렬 된 CCI 테이블로 데이터를 로드 하는 성능은 분할 된 테이블과 유사 합니다.  데이터 정렬 작업으로 인해 순서가 지정 된 CCI 테이블에 데이터를 로드 하는 데 시간이 오래 걸릴 수 있지만 나중에 순서가 지정 된 CCI를 사용 하 여 쿼리를 더 빠르게 실행할 수 있습니다.  

다음은 서로 다른 스키마를 사용 하는 테이블로 데이터를 로드 하는 경우의 성능 비교 예제입니다.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


다음은 CCI와 정렬 된 CCI 간의 쿼리 성능 비교 예제입니다.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>겹치는 세그먼트 줄이기

겹치는 세그먼트 수는 정렬 된 CCI를 만드는 동안 정렬할 데이터의 크기, 사용 가능한 메모리 및 최대 병렬 처리 수준 (MAXDOP) 설정에 따라 달라 집니다. 다음은 순서가 지정 된 CCI를 만들 때 겹치는 세그먼트를 줄이기 위한 옵션입니다.

- 더 높은 DWU에서 xlargerc 리소스 클래스를 사용 하 여 인덱스 작성기가 데이터를 세그먼트로 압축 하기 전에 데이터 정렬에 더 많은 메모리를 사용할 수 있습니다.  인덱스 세그먼트에서 데이터의 물리적 위치를 변경할 수 없습니다.  세그먼트 또는 세그먼트 사이에는 데이터 정렬이 없습니다.  

- MAXDOP = 1을 사용 하 여 정렬 된 CCI를 만듭니다.  순서가 지정 된 CCI 생성에 사용 되는 각 스레드는 데이터의 하위 집합에서 작동 하며 로컬로 정렬 됩니다.  여러 스레드에 의해 정렬 된 데이터에는 전역 정렬이 없습니다.  병렬 스레드를 사용 하면 정렬 된 CCI를 만드는 시간을 줄일 수 있지만 단일 스레드를 사용 하는 것 보다 더 겹치는 세그먼트를 생성 합니다.  현재 MAXDOP 옵션은 CREATE TABLE SELECT 명령으로 사용 하 여 정렬 된 CCI 테이블을 만드는 경우에만 지원 됩니다.  CREATE INDEX 또는 CREATE TABLE 명령을 통해 정렬 된 CCI를 만드는 것은 MAXDOP 옵션을 지원 하지 않습니다. 예를 들면 다음과 같습니다.

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- 테이블에 로드 하기 전에 정렬 키를 기준으로 데이터를 미리 정렬 합니다.

다음은 위의 권장 사항과 겹치지 않는 세그먼트가 0 인 순서가 지정 된 CCI 테이블 배포의 예입니다. 정렬 된 CCI 테이블은 MAXDOP 1 및 xlargerc를 사용 하 여 20gb 힙 테이블에서 CTAS를 통해 DWU1000c 데이터베이스에 만들어집니다.  CCI는 중복 없이 BIGINT 열에 대해 정렬 됩니다.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>많은 테이블에 순서가 지정 된 CCI 만들기

정렬 된 CCI를 만드는 작업은 오프 라인 작업입니다.  파티션이 없는 테이블의 경우에는 순서가 지정 된 CCI 생성 프로세스가 완료 될 때까지 사용자가 데이터에 액세스할 수 없습니다.   분할 된 테이블의 경우 엔진은 파티션에 의해 순서가 지정 된 CCI 파티션을 만들기 때문에, 사용자는 정렬 된 CCI 생성이 아직 진행 되지 않는 파티션에서 데이터에 계속 액세스할 수 있습니다.   이 옵션을 사용 하 여 많은 테이블에서 순서가 지정 된 CCI를 만드는 동안 가동 중지 시간을 최소화할 수 있습니다. 

1.    대상 대량 테이블 (Table_A 이라고 함)에 파티션을 만듭니다.
2.    테이블 A와 동일한 테이블 및 파티션 스키마를 사용 하 여 빈 정렬 된 CCI 테이블 (Table_B 이라고 함)을 만듭니다.
3.    테이블 A에서 테이블 B로 파티션 하나를 전환 합니다.
4.    ALTER INDEX <Ordered_CCI_Index> Table_B <를 실행 하 여 테이블 B에서 ALTER INDEX를 실행 하 여 전환 된 파티션을 다시 작성 합니다.  
5.    Table_A의 각 파티션에 대해 3 단계와 4 단계를 반복 합니다.
6.    모든 파티션이 Table_A에서 Table_B로 전환 되 고 다시 작성 된 후 Table_A를 삭제 하 고 Table_A Table_B 이름을 바꿉니다. 

## <a name="examples"></a>예제

**입니다. 순서가 지정 된 열 및 주문 서 수를 확인 하려면 다음을 수행 합니다.**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. 열 서 수를 변경 하려면 order 목록에서 열을 추가 또는 제거 하거나 CCI에서 정렬 된 CCI로 변경 합니다.**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
