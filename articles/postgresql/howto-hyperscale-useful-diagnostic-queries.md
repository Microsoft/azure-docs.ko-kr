---
title: 유용한 진단 쿼리-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 분산 데이터 등에 대 한 자세한 내용 쿼리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 4858f650aca1b704ac79482e0158fd83fc0264b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98165244"
---
# <a name="useful-diagnostic-queries"></a>유용한 진단 쿼리

## <a name="finding-which-node-contains-data-for-a-specific-tenant"></a>특정 테 넌 트에 대 한 데이터를 포함 하는 노드 찾기

다중 테 넌 트 사용 사례에서 특정 테 넌 트에 대 한 행을 포함 하는 작업자 노드를 확인할 수 있습니다.  Citus (hyperscale)는 분산 테이블의 행을 분할로 그룹화 하 고 각 분할 된 서버를 서버 그룹의 작업자 노드에 배치 합니다. 

응용 프로그램의 테 넌 트가 저장소이 고 상점 ID = 4의 데이터를 보유 하는 작업자 노드를 찾으려고 한다고 가정 합니다.  즉, 배포 열의 값이 4 인 행을 포함 하는 분할 된 데이터베이스가 포함 된 위치를 찾으려고 합니다.

``` postgresql
SELECT shardid, shardstate, shardlength, nodename, nodeport, placementid
  FROM pg_dist_placement AS placement,
       pg_dist_node AS node
 WHERE placement.groupid = node.groupid
   AND node.noderole = 'primary'
   AND shardid = (
     SELECT get_shard_id_for_distribution_column('stores', 4)
   );
```

출력에는 작업자 데이터베이스의 호스트와 포트가 포함 됩니다.

```
┌─────────┬────────────┬─────────────┬───────────┬──────────┬─────────────┐
│ shardid │ shardstate │ shardlength │ nodename  │ nodeport │ placementid │
├─────────┼────────────┼─────────────┼───────────┼──────────┼─────────────┤
│  102009 │          1 │           0 │ 10.0.0.16 │     5432 │           2 │
└─────────┴────────────┴─────────────┴───────────┴──────────┴─────────────┘
```

## <a name="finding-the-distribution-column-for-a-table"></a>테이블에 대 한 배포 열 찾기

Citus (Hyperscale)의 각 분산 테이블에는 "분포 열"이 있습니다. (자세한 내용은 [분산 데이터 모델링](concepts-hyperscale-choose-distribution-column.md)을 참조 하세요.) 열을 알아야 할 수 있습니다. 예를 들어 테이블을 조인 하거나 필터링 할 때 "배포 열에 필터 추가"와 같은 힌트가 포함 된 오류 메시지가 표시 될 수 있습니다.

`pg_dist_*`코디네이터 노드의 테이블에는 분산 데이터베이스에 대 한 다양 한 메타 데이터가 포함 됩니다. 특히 `pg_dist_partition` 각 테이블의 배포 열에 대 한 정보를 저장 합니다. 편리한 유틸리티 함수를 사용 하 여 메타 데이터의 하위 수준 세부 정보에서 배포 열 이름을 조회할 수 있습니다. 예제 및 해당 출력은 다음과 같습니다.

``` postgresql
-- create example table

CREATE TABLE products (
  store_id bigint,
  product_id bigint,
  name text,
  price money,

  CONSTRAINT products_pkey PRIMARY KEY (store_id, product_id)
);

-- pick store_id as distribution column

SELECT create_distributed_table('products', 'store_id');

-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

예제 출력:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ store_id      │
└───────────────┘
```

## <a name="detecting-locks"></a>잠금 검색

이 쿼리는 모든 작업자 노드에서 실행 되며 잠금, 열린 시간 및 잘못 된 쿼리를 식별 합니다.

``` postgresql
SELECT run_command_on_workers($cmd$
  SELECT array_agg(
    blocked_statement || ' $ ' || cur_stmt_blocking_proc
    || ' $ ' || cnt::text || ' $ ' || age
  )
  FROM (
    SELECT blocked_activity.query    AS blocked_statement,
           blocking_activity.query   AS cur_stmt_blocking_proc,
           count(*)                  AS cnt,
           age(now(), min(blocked_activity.query_start)) AS "age"
    FROM pg_catalog.pg_locks         blocked_locks
    JOIN pg_catalog.pg_stat_activity blocked_activity
      ON blocked_activity.pid = blocked_locks.pid
    JOIN pg_catalog.pg_locks         blocking_locks
      ON blocking_locks.locktype = blocked_locks.locktype
     AND blocking_locks.DATABASE IS NOT DISTINCT FROM blocked_locks.DATABASE
     AND blocking_locks.relation IS NOT DISTINCT FROM blocked_locks.relation
     AND blocking_locks.page IS NOT DISTINCT FROM blocked_locks.page
     AND blocking_locks.tuple IS NOT DISTINCT FROM blocked_locks.tuple
     AND blocking_locks.virtualxid IS NOT DISTINCT FROM blocked_locks.virtualxid
     AND blocking_locks.transactionid IS NOT DISTINCT FROM blocked_locks.transactionid
     AND blocking_locks.classid IS NOT DISTINCT FROM blocked_locks.classid
     AND blocking_locks.objid IS NOT DISTINCT FROM blocked_locks.objid
     AND blocking_locks.objsubid IS NOT DISTINCT FROM blocked_locks.objsubid
     AND blocking_locks.pid != blocked_locks.pid
    JOIN pg_catalog.pg_stat_activity blocking_activity ON blocking_activity.pid = blocking_locks.pid
    WHERE NOT blocked_locks.GRANTED
     AND blocking_locks.GRANTED
    GROUP BY blocked_activity.query,
             blocking_activity.query
    ORDER BY 4
  ) a
$cmd$);
```

예제 출력:

```
┌───────────────────────────────────────────────────────────────────────────────────┐
│                               run_command_on_workers                              │
├───────────────────────────────────────────────────────────────────────────────────┤
│ (10.0.0.16,5432,t,"")                                                             │
│ (10.0.0.20,5432,t,"{""update ads_102277 set name = 'new name' where id = 1; $ sel…│
│…ect * from ads_102277 where id = 1 for update; $ 1 $ 00:00:03.729519""}")         │
└───────────────────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-your-shards"></a>분할 크기 쿼리

이 쿼리는 다음과 같은 지정 된 분산 테이블의 모든 분할 된 테이블 크기를 제공 합니다 `my_distributed_table` .

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT json_build_object(
    'shard_name', '%1$s',
    'size',       pg_size_pretty(pg_table_size('%1$s'))
  );
$cmd$);
```

예제 출력:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                                result                                 │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │ {"shard_name" : "my_distributed_table_102008", "size" : "2416 kB"}    │
│  102009 │ t       │ {"shard_name" : "my_distributed_table_102009", "size" : "3960 kB"}    │
│  102010 │ t       │ {"shard_name" : "my_distributed_table_102010", "size" : "1624 kB"}    │
│  102011 │ t       │ {"shard_name" : "my_distributed_table_102011", "size" : "4792 kB"}    │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="querying-the-size-of-all-distributed-tables"></a>모든 분산 테이블의 크기 쿼리

이 쿼리는 각 분산 테이블의 크기와 해당 인덱스의 크기에 대 한 목록을 가져옵니다.

``` postgresql
SELECT
  tablename,
  pg_size_pretty(
    citus_total_relation_size(tablename::text)
  ) AS total_size
FROM pg_tables pt
JOIN pg_dist_partition pp
  ON pt.tablename = pp.logicalrelid::text
WHERE schemaname = 'public';
```

예제 출력:

```
┌───────────────┬────────────┐
│   tablename   │ total_size │
├───────────────┼────────────┤
│ github_users  │ 39 MB      │
│ github_events │ 98 MB      │
└───────────────┴────────────┘
```

분산 테이블 크기를 쿼리 하기 위한 다른 Citus (Hyperscale) 함수는 [테이블 크기 확인](howto-hyperscale-table-size.md)을 참조 하세요.

## <a name="identifying-unused-indices"></a>사용 하지 않는 인덱스 식별

다음 쿼리는 지정 된 분산 테이블 ()에 대 한 작업자 노드의 사용 하지 않는 인덱스를 식별 합니다. `my_distributed_table`

``` postgresql
SELECT *
FROM run_command_on_shards('my_distributed_table', $cmd$
  SELECT array_agg(a) as infos
  FROM (
    SELECT (
      schemaname || '.' || relname || '##' || indexrelname || '##'
                 || pg_size_pretty(pg_relation_size(i.indexrelid))::text
                 || '##' || idx_scan::text
    ) AS a
    FROM  pg_stat_user_indexes ui
    JOIN  pg_index i
    ON    ui.indexrelid = i.indexrelid
    WHERE NOT indisunique
    AND   idx_scan < 50
    AND   pg_relation_size(relid) > 5 * 8192
    AND   (schemaname || '.' || relname)::regclass = '%s'::regclass
    ORDER BY
      pg_relation_size(i.indexrelid) / NULLIF(idx_scan, 0) DESC nulls first,
      pg_relation_size(i.indexrelid) DESC
  ) sub
$cmd$);
```

예제 출력:

```
┌─────────┬─────────┬───────────────────────────────────────────────────────────────────────┐
│ shardid │ success │                            result                                     │
├─────────┼─────────┼───────────────────────────────────────────────────────────────────────┤
│  102008 │ t       │                                                                       │
│  102009 │ t       │ {"public.my_distributed_table_102009##some_index_102009##28 MB##0"}   │
│  102010 │ t       │                                                                       │
│  102011 │ t       │                                                                       │
└─────────┴─────────┴───────────────────────────────────────────────────────────────────────┘
```

## <a name="monitoring-client-connection-count"></a>클라이언트 연결 수 모니터링

다음 쿼리는 코디네이터에서 열린 연결 수를 계산 하 고 유형별로 그룹화 합니다.

``` sql
SELECT state, count(*)
FROM pg_stat_activity
GROUP BY state;
```

예제 출력:

```
┌────────┬───────┐
│ state  │ count │
├────────┼───────┤
│ active │     3 │
│ idle   │     3 │
│ ∅      │     6 │
└────────┴───────┘
```

## <a name="index-hit-rate"></a>인덱스 적중률

이 쿼리는 모든 노드에 걸쳐 인덱스 적중률을 제공 합니다. 인덱스 적중률은 쿼리할 때 인덱스를 사용 하는 빈도를 결정 하는 데 유용 합니다.

``` postgresql
SELECT nodename, result as index_hit_rate
FROM run_command_on_workers($cmd$
  SELECT CASE sum(idx_blks_hit)
    WHEN 0 THEN 'NaN'::numeric
    ELSE to_char((sum(idx_blks_hit) - sum(idx_blks_read)) / sum(idx_blks_hit + idx_blks_read), '99.99')::numeric
    END AS ratio
  FROM pg_statio_user_indexes
$cmd$);
```

예제 출력:

```
┌───────────┬────────────────┐
│ nodename  │ index_hit_rate │
├───────────┼────────────────┤
│ 10.0.0.16 │ 0.88           │
│ 10.0.0.20 │ 0.89           │
└───────────┴────────────────┘
```

## <a name="cache-hit-rate"></a>캐시 적중률

대부분의 응용 프로그램은 일반적으로 한 번에 작은 비율의 전체 데이터에 액세스 합니다. PostgreSQL는 디스크에서 읽기 속도가 느려지는 것을 방지 하기 위해 자주 액세스 하는 데이터를 메모리에 유지 합니다. [Pg_statio_user_tables](https://www.postgresql.org/docs/current/monitoring-stats.html#MONITORING-PG-STATIO-ALL-TABLES-VIEW) 보기에서 통계를 볼 수 있습니다.

중요 한 측정은 메모리 캐시와 워크 로드의 디스크에서 제공 되는 데이터의 비율입니다.

``` postgresql
SELECT
  sum(heap_blks_read) AS heap_read,
  sum(heap_blks_hit)  AS heap_hit,
  sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read)) AS ratio
FROM
  pg_statio_user_tables;
```

예제 출력:

```
 heap_read | heap_hit |         ratio
-----------+----------+------------------------
         1 |      132 | 0.99248120300751879699
```

99% 보다 훨씬 낮은 비율을 사용 하는 경우에는 데이터베이스에 사용할 수 있는 캐시의 증가를 고려해 야 할 가능성이 높습니다.

## <a name="next-steps"></a>다음 단계

* 진단에 유용한 다른 [시스템 테이블](reference-hyperscale-metadata.md) 에 대해 알아보기
