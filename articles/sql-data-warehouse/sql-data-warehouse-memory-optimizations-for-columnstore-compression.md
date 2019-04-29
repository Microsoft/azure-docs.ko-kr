---
title: Columnstore 인덱스 성능 향상 - Azure SQL Data Warehouse | Microsoft Docs
description: 메모리 요구 사항을 줄이거나 사용 가능한 메모리를 늘려 columnstore 인덱스가 각 행 그룹으로 압축되는 행 수를 최대화합니다.
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
ms.openlocfilehash: e7ab09522184f5c2d1c5168b24b2948f58e5189e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748795"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>columnstore의 행 그룹 품질 최대화

행 그룹 품질은 행 그룹의 행 수에 따라 결정됩니다. 사용 가능한 메모리를 늘려 각 rowgroup을 columnstore 인덱스로 압축 된 행의 수를 최대화할 수 있습니다.  이 방법을 사용하여 columnstore 인덱스에 대한 압축 비율 및 쿼리 성능을 개선시킬 수 있습니다.

## <a name="why-the-rowgroup-size-matters"></a>행 그룹 크기가 중요한 이유
columnstore 인덱스는 개별 행 그룹의 열 세그먼트를 검색하여 테이블을 검색하므로 각 행 그룹에서 행 수를 최대화하면 쿼리 성능이 향상됩니다. 행 그룹에 행 수가 많은 경우 데이터 압축이 향상되며 따라서 디스크에서 읽어올 데이터가 줄어듭니다.

행 그룹 에 대한 자세한 내용은 [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.

## <a name="target-size-for-rowgroups"></a>행 그룹의 대상 크기
최적의 쿼리 성능을 위해 columnstore 인덱스에서 행 그룹당 행 수를 최대화하는 것을 목표로 합니다. 행 그룹은 최대 1,048,576개의 행을 포함할 수 있습니다. 행 그룹당 최대 행 수를 포함하지 않는 것이 좋습니다. Columnstore 인덱스는 행 그룹에 100,000개 이상의 행이 있을 때 적절한 성능을 달성합니다.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>압축 중에 행 그룹을 잘라낼 수 있음

대량 로드 또는 columnstore 인덱스 다시 작성 중에는 각 행 그룹에 대해 지정된 모든 행을 압축하기에 메모리가 부족할 수 있습니다. 메모리가 부족할 때 columnstore 인덱스는 columnstore로 압축이 성공할 수 있도록 행 그룹 크기를 자릅니다. 

10,000개 이상의 행을 각 행 그룹으로 압축하기에 메모리가 부족한 경우 SQL Data Warehouse에서 오류를 생성합니다.

대량 로드에 대한 자세한 내용은 [클러스터형 columnstore 인덱스로 대량 로드](https://msdn.microsoft.com/library/dn935008.aspx#Bulk )를 참조하세요.

## <a name="how-to-monitor-rowgroup-quality"></a>행 그룹 품질을 모니터링 하는 방법

DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) SQL Data Warehouse에 SQL DB를 일치 하는 뷰 정의 포함) 유용한 정보를 노출 하는 같은 행 그룹 및 해당 이유 수 있습니다 하는 경우에 행 수입니다. 다음 보기를 만들면 이 DMV를 간편하게 쿼리하여 행 그룹 잘라내기에 대한 정보를 가져올 수 있습니다.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

trim_reason_desc는 행 그룹이 잘렸는지 여부를 나타냅니다. trim_reason_desc = NO_TRIM은 행 그룹이 잘리지 않았으며 최적의 품질임을 나타냅니다. 아래의 잘림 이유는 행 그룹이 중간에 잘렸음을 나타냅니다.
- BULKLOAD: 로드에 대해 들어오는 행 배치의 행 수가 1백만 개 미만이면 이 자르기 이유가 사용됩니다. 삽입되는 행 수가 10만 개보다 많으면 엔진은 델타 저장소에 행을 삽입하지 않고 압축된 행 그룹을 만들지만 자르기 이유를 BULKLOAD로 설정합니다. 이 시나리오에서는 더 많은 행을 포함 하 여 일괄 처리 부하가 증가 하는 것이 좋습니다. 또한 행 그룹은 파티션 경계를 벗어나도록 배치될 수 없으므로, 파티션 구성표를 다시 평가하여 파티션이 너무 세밀하지 않은지 확인합니다.
- MEMORY_LIMITATION: 행이 1백만 개인 행 그룹을 만들려는 경우 엔진에는 일정량의 작업 메모리가 필요합니다. 로딩 세션의 사용 가능한 메모리가 필요한 작업 메모리보다 적으면 행 그룹이 중간에 잘립니다. 필요한 메모리를 예측하고 메모리를 추가로 할당하는 방법은 다음 섹션에서 설명합니다.
- DICTIONARY_SIZE: 이 자르기 이유는 너비 및/또는 높이 값이 큰 카디널리티 문자열이 포함된 문자열 열이 하나 이상 있어서 행 그룹이 잘렸음을 나타냅니다. 메모리에서 사전 크기는 16MB로 제한되며, 이 제한에 도달하면 행 그룹은 압축됩니다. 이러한 상황이 발생하는 경우 문제가 되는 열을 별도의 테이블에 포함해 보세요.

## <a name="how-to-estimate-memory-requirements"></a>메모리 요구 사항을 예측하는 방법

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

한 개의 행 그룹을 압축하는 데 필요한 최대 메모리는 대략적으로 다음과 같습니다.

- 72MB +
- \#행 \* \#열 \* 8바이트 +
- \#행 \* \#short-string-columns \* 32바이트 +
- 압축 사전인 경우 \#long-string-columns \* 16MB

여기서 short-string-columns는 32바이트 이하의 문자열 데이터 형식을 사용하고 long-string-columns는 32바이트를 초과하는 문자열 데이터 형식을 사용합니다.

긴 문자열은 텍스트 압축용으로 고안된 압축 방법으로 압축됩니다. 이 압축 방법은 *사전*을 사용하여 텍스트 패턴을 저장합니다. 사전의 최대 크기는 16MB입니다. 행 그룹에는 긴 문자열 각각에 대해 사전이 한 개만 있습니다.

columnstore 메모리 요구 사항에 대한 자세한 내용은 [Azure SQL Data Warehouse 확장: 구성 및 지침](https://channel9.msdn.com/Events/Ignite/2016/BRK3291) 비디오를 참조하세요.

## <a name="ways-to-reduce-memory-requirements"></a>메모리 요구 사항을 줄이는 방법

다음 기술을 사용하여 행 그룹을 columnstore 인덱스로 압축하기 위한 메모리 요구 사항을 줄일 수 있습니다.

### <a name="use-fewer-columns"></a>적은 수의 열 사용
가능한 경우 열 수를 줄여서 테이블을 설계하세요. 행 그룹이 columnstore로 압축된 경우 columnstore 인덱스는 각 열 세그먼트를 별도로 압축합니다. 따라서 열 수가 늘어나면 행 그룹을 압축하기 위한 메모리 요구 사항이 증가합니다.


### <a name="use-fewer-string-columns"></a>적은 수의 문자열 열 사용
문자열 데이터 형식의 열에는 숫자 및 날짜 데이터 형식보다 더 많은 메모리가 필요합니다. 메모리 요구 사항을 줄이려면 팩트 테이블에서 문자열 열을 제거하고 더 작은 차원 테이블에 배치하는 것이 좋습니다.

문자열 압축을 위한 추가 메모리 요구 사항:

- 32자 이하의 문자열 데이터 형식에는 값당 32바이트가 더 필요할 수 있습니다.
- 32자를 초과하는 문자열 데이터 형식은 사전 방법을 사용하여 압축됩니다.  행 그룹의 각 열에는 사전을 작성하기 위해 최대 16MB가 더 필요할 수 있습니다.

### <a name="avoid-over-partitioning"></a>오버 분할 방지

Columnstore 인덱스는 파티션당 행 그룹을 하나 이상 만듭니다. SQL Data Warehouse에서는 데이터가 배포되고 각 배포가 분할되므로 파티션 수가 금방 증가합니다. 테이블에 너무 많은 파티션이 있으면 행 그룹을 채우기에 충분하지 않을 수 있습니다. 행이 부족하다고 해서 압축 중에 메모리 부족이 발생하지는 않지만 행 그룹에서 최적의 columnstore 쿼리 성능을 달성하지 못하게 됩니다.

오버 분할을 방지하는 다른 이유는 행을 분할된 테이블의 columnstore 인덱스에 로드하는 데 메모리 오버헤드가 있다는 점입니다. 로드하는 동안 많은 파티션이 들어오는 행을 수신할 수 있으며 각 파티션이 압축할 행을 충분히 포함할 때까지 행은 메모리에 보관됩니다. 너무 많은 파티션이 있으면 메모리 부족이 발생합니다.

### <a name="simplify-the-load-query"></a>로드 쿼리 간소화

데이터베이스는 쿼리에서 모든 연산자 간 쿼리에 대한 메모리 부여를 공유합니다. 로드 쿼리에 복잡한 정렬 및 조인이 있는 경우 압축에 사용 가능한 메모리가 줄어듭니다.

쿼리를 로드하는 데만 집중할 로드 쿼리를 디자인합니다. 데이터 변환을 실행해야 할 경우 로드 쿼리와 별도로 실행합니다. 예를 들어, 힙 테이블의 데이터를 준비하고 변환을 실행한 후 준비 테이블을 columnstore 인덱스에 로드합니다. 또한 데이터를 먼저 로드한 후 MPP 시스템을 사용하여 데이터를 변환할 수도 있습니다.

### <a name="adjust-maxdop"></a>MAXDOP 조정

각 배포에서는 배포당 사용 가능한 CPU 코어가 두 개 이상 있는 경우 행 그룹을 columnstore로 병렬로 압축합니다. 병렬 처리는 메모리 부족 및 행 그룹 트리밍을 야기할 수 있는 추가 메모리 리소스를 필요로 합니다.

메모리 부족을 줄이기 위해 MAXDOP 쿼리 힌트를 사용하여 로드 작업이 각 배포 내에서 직렬 모드로 강제로 실행되도록 할 수 있습니다.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>더 많은 메모리를 할당하는 방법

DWU 크기와 사용자 리소스 클래스를 함께 사용하여 사용자 쿼리에 사용 가능한 메모리 양을 결정합니다. 로드 쿼리에 대한 메모리 부여를 늘리려면 DWU 수를 늘리거나 리소스 클래스 수를 늘리면 됩니다.

- DWU 수를 늘리려면 [성능을 조정하려면 어떻게 해야 합니까?](quickstart-scale-compute-portal.md)를 참조하세요.
- 쿼리에 대한 리소스 클래스를 변경하려면 [사용자 리소스 클래스 변경 예제](resource-classes-for-workload-management.md#change-a-users-resource-class)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SQL Data Warehouse의 성능을 향상시키기 위해 여러 가지 방법을 찾으려면 [성능 개요](sql-data-warehouse-overview-manage-user-queries.md)를 참조하세요.

