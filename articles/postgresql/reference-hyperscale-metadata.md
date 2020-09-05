---
title: 시스템 테이블-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 분산 쿼리 실행을 위한 메타 데이터
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: c11fd7a9cb6fdd3eb976d0b9e6a91fdc69bf9fba
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136846"
---
# <a name="system-tables-and-views"></a>시스템 테이블 및 뷰

Citus (hyperscale)는 서버 그룹의 분산 데이터에 대 한 정보를 포함 하는 특수 테이블을 만들고 유지 관리 합니다. 코디네이터 노드는 작업자 노드에서 쿼리를 실행 하는 방법을 계획할 때 이러한 테이블을 조회 합니다.

## <a name="coordinator-metadata"></a>코디네이터 메타 데이터

Citus (hyperscale)는 배포 열을 기반으로 각 분산 테이블을 여러 개의 논리적 분할 나눕니다. 그런 다음 코디네이터는 이러한 분할의 상태 및 위치에 대 한 통계 및 정보를 추적 하는 메타 데이터 테이블을 유지 관리 합니다.

이 섹션에서는 이러한 각 메타 데이터 테이블 및 스키마에 대해 설명 합니다.
코디네이터 노드에 로그인 한 후 SQL을 사용 하 여 이러한 테이블을 보고 쿼리할 수 있습니다.

> [!NOTE]
>
> Citus 엔진의 이전 버전을 실행 하는 하이퍼 확장 서버 그룹은 아래 나열 된 모든 테이블을 제공 하지 않을 수 있습니다.

### <a name="partition-table"></a>파티션 테이블

Pg \_ \_ 데이터 파티션 테이블은 배포 되는 데이터베이스 테이블에 대 한 메타 데이터를 저장 합니다. 또한 배포 된 각 테이블에 대해 배포 방법에 대 한 정보와 배포 열에 대 한 자세한 정보를 저장 합니다.

| 속성         | Type     | 설명                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | 이 행이 해당 하는 분산 테이블입니다. 이 값은 pg_class 시스템 카탈로그 테이블의 relfilenode 열을 참조 합니다.                                                                                                                   |
| partmethod   | char     | 분할/배포에 사용 되는 메서드입니다. 다른 배포 메서드에 해당 하는이 열의 값은 append: ' a ', hash: ' h ', 참조 테이블: ' n '입니다.                                                                          |
| partkey      | text     | 열 번호, 유형 및 기타 관련 정보를 포함 하 여 배포 열에 대 한 자세한 정보입니다.                                                                                                                                      |
| colocationid | integer  | 이 테이블이 속한 colocation 그룹입니다. 동일한 그룹의 테이블은 공동 배치 조인과 분산 롤업이 다른 최적화를 허용 합니다. 이 값은 pg_dist_colocation 테이블의 colocationid 열을 참조 합니다.                      |
| repmodel     | char     | 데이터 복제에 사용 되는 방법입니다. 다른 복제 메서드에 해당 하는이 열의 값은 Citus 문 기반 복제: ' c ', postgresql streaming replication: s ', 2 단계 커밋 (참조 테이블의 경우)입니다. |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>분할 테이블

Pg \_ 데이터 분할 \_ 테이블은 테이블의 개별 분할에 대 한 메타 데이터를 저장 합니다. Pg_dist_shard는에 속한 분산 테이블 분할에 대 한 정보 및 분할의 배포 열에 대 한 통계를 포함 합니다.
추가 분산 테이블의 경우 이러한 통계는 분포 열의 min/max 값에 해당 합니다. 해시 분산 테이블의 경우 해시 토큰 범위는 해당 분할 된 테이블에 할당 됩니다. 이러한 통계는 선택 쿼리 중 관련 없는 분할를 정리 하는 데 사용 됩니다.

| 속성          | Type     | 설명                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | 이 행이 해당 하는 분산 테이블입니다. 이 값은 pg_class 시스템 카탈로그 테이블의 relfilenode 열을 참조 합니다.                                                          |
| shardid       | bigint   | 이 분할 영역에 할당 된 guid (Globally unique identifier)입니다.                                                                                                                                           |
| shardstorage  | char     | 이 분할 된 저장소에 사용 되는 저장소 유형입니다. 다음 표에서는 다양 한 저장소 유형을 설명 합니다.                                                                                               |
| shardminvalue | text     | 추가 분산 테이블의 경우이 분할 된 데이터베이스가 포함 된 배포 열의 최소값입니다. 해시 분산 테이블의 경우 해당 분할 된 데이터베이스가 포함 된 최소 해시 토큰 값입니다. |
| shardmaxvalue | text     | 추가 분산 테이블의 경우이 분할 된 데이터베이스가 포함 된 배포 열의 최대값입니다. 해시 분산 테이블의 경우 해당 분할 된 데이터베이스가 포함 된 최대 해시 토큰 값입니다. |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>분할 저장소 유형

Pg의 shardstorage 열은 분할 된 데이터베이스가 사용 하는 \_ \_ 저장소 유형을 나타냅니다. 다른 분할 된 저장소 유형 및 해당 표현에 대 한 간략 한 개요는 아래와 같습니다.

| 스토리지 유형 | Shardstorage 값 | 설명                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | 없습니다                | 분할 된 데이터베이스가 정규 분산 테이블에 속하는 데이터를 저장 함을 나타냅니다.         |
| 컬럼     | c                | 분할 된 데이터를 저장 하는 분할 된 데이터를 나타냅니다. (분산 cstore_fdw 테이블에서 사용 됨) |
| FOREIGN      | 350                | 분할 된 데이터베이스가 외부 데이터를 저장 함을 나타냅니다. (분산 file_fdw 테이블에서 사용 됨)    |

### <a name="shard-placement-table"></a>분할 배치 테이블

Pg 배포 \_ \_ 배치 테이블은 작업자 노드에서 분할 된 복제본의 위치를 추적 합니다. 특정 노드에 할당 된 분할 된 데이터베이스의 각 복제본을 분할 된 데이터베이스 배치 라고 합니다. 이 테이블은 각 분할 된 배치의 상태 및 위치에 대 한 정보를 저장 합니다.

| 속성        | Type   | 설명                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | 이 배치와 연결 된 분할 된 식별자입니다. 이 값은 pg_dist_shard 카탈로그 테이블의 shardid 열을 참조 합니다.             |
| shardstate  | int    | 이 배치의 상태를 설명 합니다. 서로 다른 분할 된 상태에 대해서는 아래 섹션에서 설명 합니다.                                         |
| shardlength | bigint | 추가 분산 테이블의 경우 작업자 노드의 분할 된 배치 크기 (바이트)입니다. 해시 분산 테이블의 경우 0입니다.            |
| placementid | bigint | 개별 배치 마다 자동 생성 되는 고유 식별자입니다.                                                                           |
| groupid     | int    | 스트리밍 복제 모델을 사용할 때 하나의 주 서버와 0 개 이상의 보조 서버 그룹을 나타냅니다. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>분할 배치 상태

Citus (hyperscale)는 배치 단위로 분할 된 상태를 관리 합니다. 배치가 시스템을 일관 되지 않은 상태로 전환 하는 경우 Citus는이를 자동으로 사용할 수 없음으로 표시 합니다. 배치 상태는 shardstate 열 내의 pg_dist_shard_placement 테이블에 기록 됩니다. 다른 분할 된 배치 상태의 간략 한 개요는 다음과 같습니다.

| 상태 이름 | Shardstate 값 | 설명                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 종료  | 1                | 새 분할 상태는에 생성 됩니다. 이 상태의 분할 된 데이터베이스가 최신으로 간주 되어 쿼리 계획 및 실행에 사용 됩니다.                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | 이 상태의 분할 된 데이터베이스 위치는 동일한 분할 된 데이터베이스의 다른 복제본과 동기화 되지 않아서 비활성 상태인 것으로 간주 됩니다. 상태는이 배치에 대해 추가, 수정 (삽입, 업데이트, 삭제) 또는 DDL 작업이 실패 하는 경우 발생할 수 있습니다. 쿼리 계획자는 계획 및 실행 중이 상태의 배치를 무시 합니다. 사용자는 이러한 분할의 데이터를 최종 복제본과 백그라운드 작업으로 동기화 할 수 있습니다. |
| TO_DELETE  | 4                | Citus 호출에 master_apply_delete_command 대 한 응답으로 분할 된 배치를 삭제 하려고 시도 하 고 실패 하면 배치가이 상태로 이동 합니다. 그런 다음 사용자는 이러한 분할를 후속 백그라운드 작업으로 삭제할 수 있습니다.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>작업자 노드 테이블

Pg 배포 \_ \_ 노드 테이블에는 클러스터의 작업자 노드에 대 한 정보가 포함 되어 있습니다.

| 속성             | Type    | 설명                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | int     | 개별 노드에 대 한 식별자를 자동으로 생성 합니다.                                                                                                                                          |
| groupid          | int     | 스트리밍 복제 모델을 사용할 때 하나의 주 서버와 0 개 이상의 보조 서버 그룹을 나타내는 데 사용 되는 식별자입니다. 기본적으로 nodeid와 동일 합니다.         |
| nodename         | text    | PostgreSQL worker 노드의 호스트 이름 또는 IP 주소입니다.                                                                                                                                     |
| nodeport         | int     | PostgreSQL worker 노드가 수신 대기 하는 포트 번호입니다.                                                                                                                              |
| noderack         | text    | 필드 작업자 노드에 대 한 랙 배치 정보입니다.                                                                                                                                 |
| hasmetadata      | boolean | 내부용으로 예약된 속성입니다.                                                                                                                                                                 |
| isactive         | boolean | 노드가 분할 된 위치를 수락 하는지 여부입니다.                                                                                                                                     |
| noderole         | text    | 노드가 주 노드인지 아니면 보조 노드인지                                                                                                                                                 |
| nodecluster      | text    | 이 노드를 포함 하는 클러스터의 이름                                                                                                                                               |
| shouldhaveshards | boolean | False 인 경우 분할은 분할가 이미 있는 공동 배치 경우를 제외 하 고, 균형을 재조정 하거나 새 분산 테이블에서 노드를 배치할 수 없습니다. |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>분산 개체 테이블

Citus.pg 배포 \_ \_ 개체 테이블에는 코디네이터 노드에 생성 되어 작업자 노드에 전파 된 유형 및 함수와 같은 개체의 목록이 포함 됩니다. 관리자가 새 작업자 노드를 클러스터에 추가 하면 Citus (분산 개체의 복사본을 개체 종속성을 충족 하기 위해 올바른 순서로) 새 노드에 자동으로 만듭니다.

| 속성                        | Type    | 설명                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | 분산 개체의 클래스                      |
| objid                       | oid     | 분산 개체의 개체 ID입니다.                  |
| objsubid                    | integer | 분산 개체의 개체 하위 ID (예: attnum) |
| type                        | text    | Pg 업그레이드 중에 사용 되는 안정적인 주소의 일부   |
| object_names                | text []  | Pg 업그레이드 중에 사용 되는 안정적인 주소의 일부   |
| object_args                 | text []  | Pg 업그레이드 중에 사용 되는 안정적인 주소의 일부   |
| distribution_argument_index | integer | 분산 함수/프로시저에만 유효 합니다.      |
| colocationid                | integer | 분산 함수/프로시저에만 유효 합니다.      |

\"안정적인 주소 \" 는 특정 서버와 독립적으로 개체를 고유 하 게 식별 합니다. Citus (hyperscale)는 [pg \_ id \_ 개체를 \_ \_ address ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) 함수로 만든 안정 된 주소를 사용 하 여 PostgreSQL를 업그레이드 하는 동안 개체를 추적 합니다.

다음은 \' `create_distributed_function()` 테이블에 항목을 추가 하는 방법의 예입니다 `citus.pg_dist_object` .

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Colocation 그룹 테이블

Pg \_ \_ 분할 공동 배치 테이블에는 \' 함께 배치 해야 하는 테이블 또는 [공동 배치](concepts-hyperscale-colocation.md)에 대 한 정보가 포함 되어 있습니다.
두 테이블이 동일한 공동 배치 그룹에 있는 경우 Citus (hyperscale)는 동일한 파티션 값이 있는 분할 동일한 작업자 노드에 배치 되도록 합니다.
공동 배치는 조인 최적화, 특정 분산 롤업 및 외래 키 지원을 가능 하 게 합니다. 분할 된 데이터베이스가 두 테이블 간에 모두 일치 하는 경우 분할 된 데이터베이스가 두 테이블 간에 일치 하는 것으로 간주 됩니다. 그러나 원하는 경우에는 분산 테이블을 만들 때 사용자 지정 공동 배치 그룹을 지정할 수 있습니다.

| 속성                   | Type | 설명                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | 이 행에 해당 하는 공동 위치 그룹의 고유 식별자입니다.          |
| shardcount             | int  | 이 공동 배치 그룹의 모든 테이블에 대 한 분할 수                          |
| replicationfactor      | int  | 이 공동 배치 그룹의 모든 테이블에 대 한 복제 비율입니다.                  |
| distributioncolumntype | oid  | 이 공동 배치 그룹의 모든 테이블에 대 한 배포 열의 유형입니다. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Rebalancer 장치 전략 표

이 표에서는 분할를 이동할 위치를 결정 하는 데 사용할 수 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 전략을 정의 합니다.

| 속성                           | Type    | 설명                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Rebalance_table_shards에서 기본적으로이 전략을 선택 해야 하는지 여부입니다. Citus_set_default_rebalance_strategy를 사용 하 여이 열 업데이트             |
| shard_cost_function            | regproc | Shardid을 bigint로 사용 하 고 비용의 개념을 real 형식으로 반환 해야 하는 cost 함수의 식별자입니다.                                |
| node_capacity_function         | regproc | Nodeid를 int로 사용 하 고 노드 용량의 개념을 real 형식으로 반환 해야 하는 용량 함수의 식별자입니다.                          |
| shard_allowed_on_node_function | regproc | Shardid bigint 및 nodeidarg int가 지정 된 함수의 식별자입니다. Citus가 노드에 분할 된 데이터베이스가 저장 될 수 있는지 여부에 대 한 부울 값을 반환 합니다. |
| default_threshold              | float4  | 노드가 너무 꽉 찼거나 너무 비어 있기 때문에 rebalance_table_shards 분할 이동 해야 하는 시간을 결정 하는 임계값입니다.                    |
| minimum_threshold              | float4  | Rebalance_table_shards ()의 임계값 인수가 너무 낮게 설정 되지 않도록 방지 하는 보호                                                  |

Citus (Hyperscale) 설치는 테이블에 다음과 같은 전략을 제공 합니다.

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

기본 전략는 모든 분할 영역 `by_shard_count` 에 동일한 비용을 할당 합니다. 이는 여러 노드 간에 분할 된 수를 균일화 하는 것입니다. 미리 정의 된 다른 전략 인은 해당 `by_disk_size` 디스크 크기와 공동 배치 된 분할의 디스크 크기에 해당 하는 각 분할 된 파일에 대 한 비용을 할당 합니다. 디스크 크기는를 사용 하 여 계산 `pg_total_relation_size` 되므로 인덱스를 포함 합니다. 이 전략은 모든 노드에서 동일한 디스크 공간을 확보 하려고 시도 합니다. 임계값 0.1은 디스크 공간의 차이를 초래 하지 않는 불필요 한 분할 이동을 방지 합니다.

#### <a name="creating-custom-rebalancer-strategies"></a>사용자 지정 rebalancer 장치 전략 만들기

다음은 새 분할 된 다시 분산 장치 전략 내에서 사용할 수 있고 [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) 함수를 사용 하 여 [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 에 등록 되는 함수의 예입니다.

-   호스트 이름 패턴에 따라 노드 용량 예외 설정:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   [Citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)측정 된 대로 분할 된 서버로 이동 하는 쿼리 수를 기준으로 균형을 재조정 합니다.

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   노드의 특정 분할 (1만) 격리 (주소 \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>쿼리 통계 테이블

Citus (hyperscale)는 `citus_stat_statements` 쿼리를 실행 하는 방법 및 그에 대 한 통계를 제공 합니다. 이 \' 는 쿼리 속도에 대 한 통계를 추적 하는 PostgreSQL의 [pg \_ stat \_ 문](https://www.postgresql.org/docs/current/static/pgstatstatements.html) 보기와 유사 하 게 연결 될 수 있습니다.

이 뷰는 다중 테 넌 트 응용 프로그램에서 원래 테 넌 트에 대 한 쿼리를 추적할 수 있습니다 .이를 통해 테 넌 트 격리를 수행할 시기를 결정할 수

| 속성          | Type   | 설명                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | 식별자 (pg_stat_statements 조인에 적합)                                   |
| userId        | oid    | 쿼리를 실행 한 사용자                                                           |
| dbid          | oid    | 코디네이터의 데이터베이스 인스턴스                                                 |
| Query         | text   | 익명화 쿼리 문자열                                                          |
| 실행자      | text   | Citus executor 사용: 적응, 실시간, 작업-추적기, 라우터 또는 삽입-선택 |
| partition_key | text   | 라우터 실행 쿼리의 배포 열 값, NULL               |
| calls         | bigint | 쿼리가 실행 된 횟수                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

결과:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

주의 사항:

-   통계 데이터가 복제 되지 않고 \' 데이터베이스 충돌 또는 장애 조치 (failover)가 계속 됩니다.
-   `pg_stat_statements.max`Guc (기본값 5000)로 설정 된 제한 된 수의 쿼리를 추적 합니다.
-   테이블을 잘라내려면 함수를 사용 합니다. `citus_stat_statements_reset()`

### <a name="distributed-query-activity"></a>분산 쿼리 작업

Citus (hyperscale)는 분산 쿼리를 위한 결과를 빌드하기 위해 내부적으로 사용 되는 분할 된 쿼리를 포함 하 여 클러스터 전체의 쿼리 및 잠금을 감시 하는 특별 한 뷰를 제공 합니다.

-   **citus \_ 분산 \_ stat \_ 활동**: 모든 노드에서 실행 중인 분산 쿼리를 표시 합니다. `pg_stat_activity`후자가 있는 모든 위치에서 사용할 수 있는의 상위 집합입니다.
-   **citus \_ worker \_ stat \_ 작업**: 개별 분할에 대 한 조각 쿼리를 포함 하 여 작업자에 대 한 쿼리를 표시 합니다.
-   **citus \_ lock \_ wait**: 클러스터 전체에서 차단 된 쿼리입니다.

처음 두 뷰에는 [pg \_ stat \_ 활동](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) 의 모든 열과 쿼리를 시작한 작업자의 호스트 호스트/포트 및 클러스터의 코디네이터 노드에 대 한 호스트/포트가 포함 됩니다.

예를 들어 분산 테이블의 행을 계산 하는 것을 고려해 보십시오.

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

다음과 같이 쿼리를 볼 수 있습니다 `citus_dist_stat_activity` .

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

이 쿼리에는 모든 분할의 정보가 필요 합니다. 일부 정보는에 저장 되는 분할 된 부분에 `users_table_102038` `localhost:9700` 있습니다. 뷰를 살펴보면 분할 된 데이터베이스가 액세스 하는 쿼리를 볼 수 있습니다 `citus_worker_stat_activity` .

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

필드는 분할 `query` 된 데이터를 계산 하기 위해 복사 중인 데이터를 표시 합니다.

> [!NOTE]
> 라우터 쿼리 (예: 다중 테 넌 트 응용 프로그램의 단일 테 넌 트) 인 경우 선택
> * FROM table WHERE tenant_id = X ')가 트랜잭션 블록 없이 실행 된 다음 \_ \_ \_ \_ \_ \_ citus \_ worker \_ stat \_ 활동에서 마스터 쿼리 호스트 이름 및 마스터 쿼리 호스트 포트 열이 NULL이 됩니다.

가 어떻게 작동 하는지 확인 하기 위해 `citus_lock_waits` 수동으로 잠금 상황을 생성할 수 있습니다. 먼저 \' 코디네이터에서 테스트 테이블을 설정 합니다.

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

그런 다음 코디네이터에서 두 세션을 사용 하 여 다음 문 시퀀스를 실행할 수 있습니다.

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

이 `citus_lock_waits` 보기에는 상황이 표시 됩니다.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

이 예제에서 쿼리는 코디네이터에서 발생 했지만 뷰는 작업자에 대해 발생 하는 쿼리 (예: Citus)에 대해 실행 되는 쿼리 간의 잠금을 나열할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* 일부 [Hyperscale 함수](reference-hyperscale-functions.md) 에서 시스템 테이블을 변경 하는 방법 알아보기
* [노드 및 테이블](concepts-hyperscale-nodes.md) 의 개념 검토
