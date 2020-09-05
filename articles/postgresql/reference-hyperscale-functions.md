---
title: SQL 함수-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Citus (Hyperscale) SQL API의 함수
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: eaada1981929cec890ce3c8ca89fe47393730b05
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136904"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Citus (Hyperscale) SQL API의 함수

이 섹션에서는 Citus (Hyperscale)에서 제공 하는 사용자 정의 함수에 대 한 참조 정보를 제공 합니다. 이러한 함수는 표준 SQL 명령 이외의 Citus (Hyperscale)에 추가 분산 기능을 제공 하는 데 도움이 됩니다.

> [!NOTE]
>
> Citus 엔진의 이전 버전을 실행 하는 하이퍼 확장 서버 그룹은 아래 나열 된 모든 기능을 제공 하지 않을 수 있습니다.

## <a name="table-and-shard-ddl"></a>테이블 및 분할 DDL

### <a name="create_distributed_table"></a>\_분산 \_ 테이블 만들기

Create \_ distributed \_ table () 함수는 분산 테이블을 정의 하 고 해시 분산 테이블인 경우 분할을 만드는 데 사용 됩니다. 이 함수는 테이블 이름, 배포 열 및 선택적 분포 메서드를 사용 하 고 테이블을 분산 된 것으로 표시 하기 위한 적절 한 메타 데이터를 삽입 합니다. 배포 방법이 지정 되지 않은 경우이 함수는 기본적으로 ' hash ' 배포로 설정 됩니다. 테이블이 해시 분산 인 경우 함수는 분할 된 항목 수 및 분할 된 복제 요소 구성 값을 기반으로 작업자 분할 만듭니다. 테이블에 행이 포함 되어 있으면 자동으로 작업자 노드에 배포 됩니다.

이 함수는 master create \_ \_ distributed \_ table () 다음에 master \_ create \_ worker 분할 ()를 사용 하 여 대체 \_ 합니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 배포 해야 하는 테이블의 이름입니다.

**배포 \_ 열:** 테이블이 배포 될 열입니다.

**배포 \_ 유형:** (선택 사항) 테이블을 배포 하는 기준이 되는 방법입니다. 허용 되는 값은 append 또는 hash 이며 기본값은 ' hash '입니다.

** \_ with:** (선택 사항) 다른 테이블의 배치 그룹에 현재 테이블을 포함 합니다. 기본적으로 테이블은 같은 형식의 열에 의해 분산 되 고 분할 개수가 동일 하며 동일한 복제 요인이 공동 배치 됩니다. 에 사용할 수 있는 값 `colocate_with` 은 `default` `none` 새 공동 배치 그룹을 시작 하는 경우이 고 해당 테이블에서 공동 배치할 다른 테이블의 이름입니다.  [표 공동 위치](concepts-hyperscale-colocation.md)를 참조 하세요.

의 기본값은 암시적 공동 배치를 수행 한다는 점에 유의 하세요 `colocate_with` . 테이블이 관련 되거나 조인 될 때 [공동](concepts-hyperscale-colocation.md) 배치를 사용할 수 있습니다.  그러나 두 테이블이 관련이 없지만 배포 열에 동일한 데이터 형식을 사용 하기 위해 발생 하는 경우에는 실수로 공동 배치 하 여 분할 된 데이터를 분산 하 [는 동안 성능을](howto-hyperscale-scaling.md#rebalance-shards)저하 시킬 수 있습니다.  분할 테이블은 cascade에서 불필요 하 게 함께 이동 됩니다 \" .\"

새 분산 테이블이 다른 테이블과 관련 되지 않은 경우를 지정 하는 것이 가장 좋습니다 `colocate_with => 'none'` .

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

이 예에서는 데이터베이스에 github \_ 이벤트 테이블이 리포지토리 id 열에서 해시로 배포 되어야 함을 알립니다 \_ .

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>\_참조 \_ 테이블 만들기

Create \_ reference \_ table () 함수는 작은 참조 또는 차원 테이블을 정의 하는 데 사용 됩니다. 이 함수는 테이블 이름을 사용 하 고, 하나의 분할 된 테이블을 만들어 모든 작업자 노드에 복제 합니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 배포 해야 하는 작은 차원 또는 참조 테이블의 이름입니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

이 예에서는 국가 테이블이 참조 테이블로 정의 되어야 함을 데이터베이스에 알립니다.

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>\_ \_ 참조 \_ 테이블로 업그레이드

\_ \_ 참조 테이블에 대 \_ 한 업그레이드 () 함수는 분할 개수가 1 인 기존 분산 테이블을 사용 하 여 해당 테이블을 인식 된 참조 테이블로 업그레이드 합니다. 이 함수를 호출한 후 테이블은 [create_reference_table](#create_reference_table)를 사용 하 여 생성 된 것 처럼 됩니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 참조 테이블로 배포 될 분산 테이블의 이름 (분할 된 테이블 수 = 1)입니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

이 예에서는 국가 테이블이 참조 테이블로 정의 되어야 함을 데이터베이스에 알립니다.

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>\_테이블 표시 \_ 공동 배치

Mark \_ tables \_ 공동 배치 () 함수는 분산 된 테이블 (원본)과 기타 항목 (대상)의 목록을 사용 하 여 원본과 동일한 공동 배치 그룹에 대상을 배치 합니다. 소스가 아직 그룹에 없는 경우이 함수는 하나를 만들고 소스와 대상을 할당 합니다.

공동 배치 테이블은 create_distributed_table 매개 변수를 통해 테이블 배포 시 수행 되어야 `colocate_with` 하지만 [create_distributed_table](#create_distributed_table), `mark_tables_colocated` 필요한 경우 나중에 처리할 수 있습니다.

#### <a name="arguments"></a>인수

**원본 \_ 테이블 \_ 이름:** 대상의 공동 배치 그룹이 일치 하도록 할당 되는 분산 테이블의 이름입니다.

**대상 \_ 테이블 \_ 이름:** 분산 된 대상 테이블의 이름 배열은 비어 있지 않아야 합니다. 이러한 분산 테이블은의 원본 테이블과 일치 해야 합니다.

> -   distribution 메서드
> -   분포 열 유형
> -   복제 유형
> -   분할 개수

위의 적용 되는 내용이 없으면 Citus (Hyperscale)에서 오류가 발생 합니다. 예를 들어 테이블을 공동 배치 하려고 시도 하 `apples` 고 `oranges` 해당 배포 열 유형이 다른 결과를 생성 합니다.

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

이 예에서는 `products` 와를 `line_items` 같은 공동 배치 그룹에 배치 `stores` 합니다. 이 예에서는 이러한 테이블이 모두 일치 하는 형식의 열에 분산 되어 있다고 가정 합니다 .이는 대부분 \" 저장소 id입니다.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>create \_ distributed \_ 함수

코디네이터 노드에서 작업자로 함수를 전파 하 고 분산 실행을 위해 표시 합니다. 코디네이터에서 분산 함수를 호출 하는 경우 Citus (Hyperscale)는 분포 인수 값을 사용 하 여 \" \" 함수를 실행할 작업자 노드를 선택 합니다. 작업자에서 함수를 실행 하면 병렬 처리가 증가 하 고 대기 시간이 분할 코드를 데이터에 더 가깝게 가져올 수 있습니다.

Distributed 함수를 실행 하는 동안에는 Postgres 검색 경로가 코디네이터에서 작업자로 전파 되지 않으므로 분산 함수 코드는 데이터베이스 개체의 이름을 정규화 해야 합니다. 또한 함수에 의해 내보내지는 알림은 사용자에 게 표시 되지 않습니다.

#### <a name="arguments"></a>인수

**함수 \_ 이름:** 배포할 함수의 이름입니다. 여러 함수가 PostgreSQL에서 동일한 이름을 가질 수 있으므로 이름은 함수의 매개 변수 형식을 괄호 안에 포함 해야 합니다. 예를 들어, `'foo(int)'` 는와 다릅니다 `'foo(int, text)'` .

**배포 \_ 인수 \_ 이름:** (선택 사항) 배포할 인수 이름입니다. 편의를 위해 (또는 함수 인수에 이름이 없는 경우)와 같은 위치 자리 표시 자가 허용 됩니다 `'$1'` . 이 매개 변수를 지정 하지 않으면 이라는 함수는 `function_name` 작업자에만 생성 됩니다. 작업자 노드를 나중에 추가 하는 경우에도 함수가 자동으로 생성 됩니다.

**공동 찾기 \_ :** (선택 사항) 분산 함수에서 분산 된 테이블 (또는 더 일반적으로 배치 그룹)을 읽거나 쓸 때 매개 변수를 사용 하 여 해당 테이블의 이름을 지정 해야 `colocate_with` 합니다. 그러면 함수를 호출할 때마다 관련 분할을 포함 하는 작업자 노드에서 실행 됩니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>메타 데이터/구성 정보

### <a name="master_get_table_metadata"></a>master \_ \_ 테이블 가져오기 \_ 메타 데이터

Master \_ get \_ table \_ metadata () 함수를 사용 하 여 분산 테이블에 대 한 배포 관련 메타 데이터를 반환할 수 있습니다. 이 메타 데이터에는 테이블에 대 한 관계 ID, 저장소 유형, 배포 방법, 배포 열, 복제 수, 최대 분할 크기 및 분할 된 분할 배치 정책이 포함 됩니다. 내부적으로이 함수는 Citus (Hyperscale) 메타 데이터 테이블을 쿼리하여 필요한 정보를 가져오고이를 사용자에 게 반환 하기 전에 튜플로 연결 합니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 메타 데이터를 가져올 분산 테이블의 이름입니다.

#### <a name="return-value"></a>Return Value

다음 정보를 포함 하는 튜플입니다.

**논리적 \_ relid:** 분산 테이블의 Oid입니다. Pg 클래스 시스템 카탈로그 테이블의 relfilenode 열을 참조 합니다 \_ .

**파트 \_ 저장소 \_ 유형:** 테이블에 사용 되는 저장소 유형입니다. ' (표준 테이블) ', ' f ' (외래 테이블) 또는 ' c ' (열 형식 테이블) 일 수 있습니다.

**part \_ 메서드:** 테이블에 사용 되는 배포 메서드입니다. ' A ' (추가) 또는 ' h ' (해시) 일 수 있습니다.

**파트 \_ 키:** 테이블의 배포 열입니다.

**부분 \_ 복제본 \_ 수:** 현재 분할 복제 수입니다.

** \_ 최대 \_ 크기 부분:** 현재 최대 분할 크기 (바이트)입니다.

**파트 \_ 배치 \_ 정책:** 테이블의 분할를 배치 하는 데 사용 되는 분할 된 배치 정책입니다. 1 (로컬 노드 우선) 또는 2 (라운드 로빈) 일 수 있습니다.

#### <a name="example"></a>예제

아래 예제에서는 github events 테이블의 테이블 메타 데이터를 인출 하 고 표시 합니다 \_ .

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>\_ \_ \_ \_ 배포 \_ 열의 분할 id 가져오기

Citus (hyperscale)는 행의 배포 열 값 및 테이블의 분포 메서드를 기반으로 분할 된 테이블의 모든 행을 분할 된 테이블에 할당 합니다. 대부분의 경우 정확한 매핑은 데이터베이스 관리자가 무시할 수 있는 하위 수준 세부 정보입니다. 그러나 수동 데이터베이스 유지 관리 작업 또는 궁금한 충족 하기 위해 행의 분할 된 데이터베이스를 결정 하는 것이 유용할 수 있습니다. `get_shard_id_for_distribution_column`함수는 해시 및 범위 분산 테이블 및 참조 테이블에 대해이 정보를 제공 합니다. 이는 추가 배포에 대해 작동 하지 않습니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 분산 테이블입니다.

**분포 \_ 값:** 분포 열의 값입니다.

#### <a name="return-value"></a>Return Value

분할 된 ID Citus (ID)는 지정 된 테이블에 대 한 분포 열 값과 연결 됩니다.

#### <a name="example"></a>예제

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>열 \_ 에서 \_ 열 \_ 이름으로

`partkey`의 열을 `pg_dist_partition` 텍스트 열 이름으로 변환 합니다. 변환은 분산 테이블의 배포 열을 결정 하는 데 유용 합니다.

자세한 내용은 [배포 열 선택](concepts-hyperscale-choose-distribution-column.md)을 참조 하세요.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 분산 테이블입니다.

**열 \_ var \_ text:** 테이블의 값입니다 `partkey` `pg_dist_partition` .

#### <a name="return-value"></a>Return Value

`table_name`배포 열의 이름입니다.

#### <a name="example"></a>예제

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

출력:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus \_ 관계 \_ 크기

지정 된 분산 테이블의 모든 분할에서 사용 하는 디스크 공간을 가져옵니다.
디스크 공간에는 주 포크의 크기가 포함 \" \" 되지만 분할에 대 한 표시 유형 맵과 사용 가능한 공간 맵은 제외 됩니다.

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 된 테이블의 이름입니다.

#### <a name="return-value"></a>Return Value

Bigint 인 크기 (바이트)입니다.

#### <a name="example"></a>예제

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ 테이블 \_ 크기

인덱스를 제외 하 고 지정 된 분산 테이블의 모든 분할 사용 하는 디스크 공간을 가져옵니다 (알림, 사용 가능한 공간 맵 및 표시 유형 맵 포함).

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 된 테이블의 이름입니다.

#### <a name="return-value"></a>Return Value

Bigint 인 크기 (바이트)입니다.

#### <a name="example"></a>예제

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ 총 \_ 관계 \_ 크기

모든 인덱스 및 알림 데이터를 포함 하 여 지정 된 분산 테이블의 모든 분할에서 사용 하는 총 디스크 공간을 가져옵니다.

#### <a name="arguments"></a>인수

**logicalrelid:** 분산 된 테이블의 이름입니다.

#### <a name="return-value"></a>Return Value

Bigint 인 크기 (바이트)입니다.

#### <a name="example"></a>예제

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus \_ stat \_ 문 \_ 다시 설정

[Citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)에서 모든 행을 제거 합니다.
이 함수는와 독립적으로 작동 `pg_stat_statements_reset()` 합니다. 모든 통계를 다시 설정 하려면 두 함수를 호출 합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="return-value"></a>Return Value

없음

## <a name="server-group-management-and-repair"></a>서버 그룹 관리 및 복구

### <a name="master_copy_shard_placement"></a>마스터 \_ 복사 분할 영역 \_ \_ 배치

분할 된 배치를 수정 명령 또는 DDL 작업 중에 업데이트 하지 못한 경우 비활성으로 표시 됩니다. 그런 다음 마스터 복사 분할 된 배치 \_ \_ 함수를 \_ 호출 하 여 정상 배치의 데이터를 사용 하 여 비활성 분할 된 분할 배치를 복구할 수 있습니다.

분할 된 개체를 복구 하기 위해 함수는 먼저 비정상 분할 된 분할 된 위치를 삭제 하 고 코디네이터의 스키마를 사용 하 여 다시 만듭니다. 분할 된 데이터 배치가 만들어지면 함수는 정상적인 배치에서 데이터를 복사 하 고 메타 데이터를 업데이트 하 여 새 분할 된 데이터 배치를 정상으로 표시 합니다. 이 함수를 사용 하면 복구 중에 동시 수정 으로부터 분할 된 데이터베이스가 보호 됩니다.

#### <a name="arguments"></a>인수

분할 된 ** \_ id:** 복구할 분할 된의 id입니다.

**원본 \_ 노드 \_ 이름:** 정상 분할 된 분할 배치가 있는 노드의 DNS 이름 \" 입니다 (원본 \" 노드).

**원본 \_ 노드 \_ 포트:** 데이터베이스 서버가 수신 대기 중인 원본 작업자 노드의 포트입니다.

**대상 \_ 노드 \_ 이름:** 잘못 된 분할 배치가 있는 노드의 DNS 이름 ( \" 대상 \" 노드)입니다.

**대상 \_ 노드 \_ 포트:** 데이터베이스 서버가 수신 대기 중인 대상 작업자 노드의 포트입니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

아래 예제에서는 포트 5432의 ' 잘못 된 호스트 '에서 실행 중인 데이터베이스 서버에 있는 분할 된 데이터베이스 12345의 비활성 분할 된 데이터베이스 배치를 복구 합니다 \_ . 이를 복구 하려면 \_ 포트의 ' 양호 호스트 '에서 실행 중인 서버에 있는 정상적인 분할 된 분할 배치의 데이터를 사용 합니다.
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>마스터 \_ 이동 분할 영역 \_ \_ 배치

이 함수는 지정 된 분할 된 파일 (및 분할 공동 배치)을 한 노드에서 다른 노드로 이동 합니다. 일반적으로 데이터베이스 관리자가 직접 호출 하는 대신 분할 된 데이터베이스를 다시 분산 하는 동안 간접적으로 사용 됩니다.

데이터를 이동 하는 두 가지 방법이 있습니다. 차단 또는 차단 되지 않습니다. 차단 방식은 이동 중에 분할 된 모든 수정 내용이 일시 중지 되었음을 의미 합니다.
분할 된 데이터베이스가 차단 되는 것을 방지 하는 두 번째 방법은 Postgres 10 논리적 복제를 사용 하는 것입니다.

이동 작업이 성공 하면 원본 노드의 분할이 삭제 됩니다. 어떤 지점에서 든 이동이 실패 하면이 함수는 오류를 throw 하 고 원본 및 대상 노드를 변경 되지 않은 상태로 유지 합니다.

#### <a name="arguments"></a>인수

분할 된 ** \_ id:** 이동할 분할 된 데이터베이스가 있는 id입니다.

**원본 \_ 노드 \_ 이름:** 정상 분할 된 분할 배치가 있는 노드의 DNS 이름 \" 입니다 (원본 \" 노드).

**원본 \_ 노드 \_ 포트:** 데이터베이스 서버가 수신 대기 중인 원본 작업자 노드의 포트입니다.

**대상 \_ 노드 \_ 이름:** 잘못 된 분할 배치가 있는 노드의 DNS 이름 ( \" 대상 \" 노드)입니다.

**대상 \_ 노드 \_ 포트:** 데이터베이스 서버가 수신 대기 중인 대상 작업자 노드의 포트입니다.

분할 된 ** \_ 전송 \_ 모드:** (선택 사항) PostgreSQL 논리 복제를 사용할지 아니면 작업자 간 복사 명령을 사용할지를 지정 합니다. 가능한 값은 다음과 같습니다.

> -   `auto`: 논리적 복제가 가능 하면 복제본 id가 필요 합니다. 그렇지 않으면 레거시 동작을 사용 합니다 (예: 분할 된 데이터베이스 복구의 경우 PostgreSQL 9.6). 이것은 기본값입니다.
> -   `force_logical`: 테이블에 복제본 id가 없는 경우에도 논리적 복제를 사용 합니다. 복제 하는 동안 테이블에 대 한 모든 동시 update/delete 문이 실패 합니다.
> -   `block_writes`: 기본 키 또는 복제본 id가 없는 테이블에 대해 COPY (차단 쓰기)를 사용 합니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>\_테이블 \_ 분할 리 밸런스

\_ \_ 분할 () 함수는 지정 된 테이블의 분할 이동 하 여 작업자 간에 균등 하 게 분산 되도록 합니다. 함수는 지정 된 임계값 내에서 서버 그룹의 균형을 유지 하기 위해 만들어야 하는 이동 목록을 계산 합니다. 그런 다음 원본 노드에서 대상 노드로 분할 영역 배치를 하나씩 이동 하 고 해당 분할 된 메타 데이터를 업데이트 하 여 이동을 반영 합니다.

분할 균등 하 게 분산 되는지 여부를 결정할 때 모든 분할 영역에 비용이 할당 됩니다 \" . \" 기본적으로 각 분할 영역에는 동일한 비용 (값 1)이 있으므로 작업자 간에 비용을 균일화 하기 위해 배포 하는 것은 각각의 분할 수를 equalizing 하는 것과 같습니다. 상수 비용 전략은 분할 된 \" 횟수에 의해 호출 되며 \_ \_ \" 기본 균형 재조정 전략입니다.

기본 전략은 다음과 같은 경우에 적합 합니다.

*  분할는 거의 동일한 크기입니다.
*  분할는 거의 동일한 양의 트래픽을 받습니다.
*  작업자 노드의 크기/형식이 모두 동일 합니다.
*  분할이 특정 작업자에 게 고정 되지 않았습니다.

이러한 가정 중 하나라도 유지 되지 않으면 기본 균형 재조정으로 인해 잘못 된 계획이 생성 될 수 있습니다. 이 경우 매개 변수를 사용 하 여 전략을 사용자 지정할 수 있습니다 `rebalance_strategy` .

분할 테이블을 실행 하기 전에 [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) 를 호출 하 여 수행할 작업을 확인 하 \_ 고 확인 하는 것이 좋습니다 \_ .

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** (선택 사항) 분할가 균형 해야 하는 테이블의 이름입니다. NULL 인 경우 모든 기존 공동 위치 그룹의 균형을 다시 조정 합니다.

**threshold:** (선택 사항) 평균 사용률에서 노드 사용률의 최대 비율을 나타내는 0.0에서 1.0 사이의 float 숫자입니다. 예를 들어 0.1를 지정 하면 분할 된 데이터베이스가 동일한 수의 분할 ± 10%를 포함 하도록 모든 노드의 균형을 조정 하려고 합니다.
특히 분할 된 데이터베이스가 다시 분산 되 면 모든 작업자 노드의 사용률을 (1-임계값) 평균 사용률으로 수렴 \* 합니다 \_ \. . (1
+ 임계값) \* 평균 \_ 사용률 범위입니다.

**최대 \_ 분할 \_ 이동 수:** (선택 사항) 이동할 최대 분할 수입니다.

**제외 된 분할 \_ \_ 목록:** (선택 사항) 리 밸런스 작업 중에는 이동 하지 않아야 하는 분할의 식별자입니다.

분할 된 ** \_ 전송 \_ 모드:** (선택 사항) PostgreSQL 논리 복제를 사용할지 아니면 작업자 간 복사 명령을 사용할지를 지정 합니다. 가능한 값은 다음과 같습니다.

> -   `auto`: 논리적 복제가 가능 하면 복제본 id가 필요 합니다. 그렇지 않으면 레거시 동작을 사용 합니다 (예: 분할 된 데이터베이스 복구의 경우 PostgreSQL 9.6). 이것은 기본값입니다.
> -   `force_logical`: 테이블에 복제본 id가 없는 경우에도 논리적 복제를 사용 합니다. 복제 하는 동안 테이블에 대 한 모든 동시 update/delete 문이 실패 합니다.
> -   `block_writes`: 기본 키 또는 복제본 id가 없는 테이블에 대해 COPY (차단 쓰기)를 사용 합니다.

**드레이닝 \_ 만:** (선택 사항) true 인 경우 `shouldhaveshards` [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)에서를 false로 설정한 작업자 노드를 이동 하 여 다른 분할 이동 하지 분할.

**균형 재조정 \_ 전략:** (선택 사항) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)의 전략 이름입니다.
이 인수를 생략 하면 함수는 테이블에 표시 된 대로 기본 전략을 선택 합니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

아래 예제에서는 \_ 기본 임계값 내에서 github events 테이블의 분할의 균형을 다시 조정 하려고 합니다.

```postgresql
SELECT rebalance_table_shards('github_events');
```

이 예에서는 \_ ID가 1과 2 인 분할를 이동 하지 않고 github 이벤트 테이블의 균형을 다시 조정 하려고 합니다.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>\_리 밸런스 \_ 테이블 \_ 분할 \_ 계획 가져오기

작업을 수행 하지 않고 계획 된 분할 된 분할 이동 [rebalance_table_shards](#rebalance_table_shards) 출력 합니다.
거의 발생 하지 않는 경우에 \_ 는 \_ \_ 분할 \_ plan 테이블이 \_ \_ 동일한 인수를 사용 하 여 분할 호출에서 수행 하는 것과 약간 다른 계획을 출력할 수 있습니다. 동시에 실행 되지 않으므로 서버 그룹에 대 한 팩트 ( \- 예: 디스크 공간 \- )는 호출 사이에 다를 수 있습니다.

#### <a name="arguments"></a>인수

균형 조정 테이블 분할와 동일한 인수 \_ \_ : 관계, 임계값, 최대 분할 된 영역 \_ \_ 이동, 제외 된 분할 \_ \_ 목록 및 드레이닝 \_ 만 해당 합니다. 인수의 의미는 해당 함수의 설명서를 참조 하세요.

#### <a name="return-value"></a>Return Value

다음 열을 포함 하는 튜플:

-   **테이블 \_ 이름**: 분할이 이동 하는 테이블입니다.
-   **shardid**: 의심 스러운 분할 된
-   **분할 \_ 크기**: 크기 (바이트)
-   **sourcename**: 원본 노드의 호스트 이름
-   **sourceport**: 원본 노드의 포트
-   **targetname**: 대상 노드의 호스트 이름
-   **targetport**: 대상 노드의 포트

### <a name="get_rebalance_progress"></a>\_리 밸런스 \_ 진행률 가져오기

분할 된 데이터베이스가 시작 되 면이 `get_rebalance_progress()` 함수는 관련 된 모든 분할의 진행률을 나열 합니다. 에서 계획 하 고 실행 한 이동을 모니터링 `rebalance_table_shards()` 합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="return-value"></a>Return Value

다음 열을 포함 하는 튜플:

-   **sessionid**: 리 밸런스 모니터의 postgres PID
-   **테이블 \_ 이름**: 분할이 이동 중인 테이블입니다.
-   **shardid**: 의심 스러운 분할 된
-   **분할 \_ 크기**: 크기 (바이트)
-   **sourcename**: 원본 노드의 호스트 이름
-   **sourceport**: 원본 노드의 포트
-   **targetname**: 대상 노드의 호스트 이름
-   **targetport**: 대상 노드의 포트
-   **진행률**: 0 = 이동할 때까지 기다리는 중입니다. 1 = 이동 2 = 완료

#### <a name="example"></a>예제

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ \_ 리 밸런스 \_ 전략 추가

[Pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 에 행을 추가 합니다.

#### <a name="arguments"></a>인수

이러한 인수에 대 한 자세한 내용은의 해당 열 값을 참조 하십시오 `pg_dist_rebalance_strategy` .

**이름:** 새 전략의 식별자

분할 된 ** \_ 비용 \_ 함수:** 각 분할 된 공간을 확인 하는 데 사용 되는 함수를 식별 합니다. \" \"

**노드 \_ 용량 \_ 함수:** 노드 용량을 측정 하는 함수를 식별 합니다.

** \_ \_ \_ 노드 \_ 함수에서 허용** 된 분할: 노드에 배치할 수 있는 분할를 결정 하는 함수를 식별 합니다.

**기본 \_ 임계값:** 노드 간에 누적 분할 된 분할 영역의 균형을 조정 해야 하는 정확한 시간을 조정 하는 부동 소수점 임계값입니다.

**최소 \_ 임계값:** (선택 사항) 균형 조정 \_ 테이블 \_ 분할 ()의 임계값 인수에 허용 되는 최소값을 보유 하는 보호 열입니다. 기본값은 0입니다.

#### <a name="return-value"></a>Return Value

해당 없음

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ 설정 \_ 기본 \_ 리 밸런스 \_ 전략

[Pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 테이블을 업데이트 하 고 인수에 의해 이름이 지정 된 전략을 분할을 재 분산 하는 경우 선택 된 기본값으로 변경 합니다.

#### <a name="arguments"></a>인수

**이름:** pg 분산 \_ \_ 리 밸런스 \_ 전략의 전략 이름

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ 원격 \_ 연결 \_ 통계

Citus \_ remote \_ connection \_ stats () 함수는 각 원격 노드에 대 한 활성 연결 수를 표시 합니다.

#### <a name="arguments"></a>인수

해당 없음

#### <a name="example"></a>예제

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>마스터 \_ 드레이닝 \_ 노드

Master \_ 드레이닝 \_ node () 함수는 `shouldhaveshards` [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)에서을 true로 설정한 다른 노드로 분할를 이동 합니다. 서버 그룹에서 노드를 제거 하 고 노드의 실제 서버를 해제 하기 전에 함수를 호출 합니다.

#### <a name="arguments"></a>인수

**nodename:** 제거할 노드의 호스트 이름 이름입니다.

**nodeport:** 방전 시킬 노드의 포트 번호입니다.

분할 된 ** \_ 전송 \_ 모드:** (선택 사항) PostgreSQL 논리 복제를 사용할지 아니면 작업자 간 복사 명령을 사용할지를 지정 합니다. 가능한 값은 다음과 같습니다.

> -   `auto`: 논리적 복제가 가능 하면 복제본 id가 필요 합니다. 그렇지 않으면 레거시 동작을 사용 합니다 (예: 분할 된 데이터베이스 복구의 경우 PostgreSQL 9.6). 이것은 기본값입니다.
> -   `force_logical`: 테이블에 복제본 id가 없는 경우에도 논리적 복제를 사용 합니다. 복제 하는 동안 테이블에 대 한 모든 동시 update/delete 문이 실패 합니다.
> -   `block_writes`: 기본 키 또는 복제본 id가 없는 테이블에 대해 COPY (차단 쓰기)를 사용 합니다.

**균형 재조정 \_ 전략:** (선택 사항) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)의 전략 이름입니다.
이 인수를 생략 하면 함수는 테이블에 표시 된 대로 기본 전략을 선택 합니다.

#### <a name="return-value"></a>Return Value

해당 없음

#### <a name="example"></a>예제

단일 노드를 제거 하는 일반적인 단계는 다음과 같습니다 (예: 표준 PostgreSQL 포트에서 ' 10.0.0.1 ').

1.  노드를 드레이닝 합니다.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  명령이 완료 될 때까지 대기

3.  노드 제거

여러 노드를 드레이닝 하는 경우 대신 [rebalance_table_shards](#rebalance_table_shards) 를 사용 하는 것이 좋습니다. 이렇게 하면 Citus (Hyperscale)를 미리 계획 하 고 분할를 최소 횟수로 이동할 수 있습니다.

1.  제거할 각 노드에 대해를 실행 합니다.

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  [Rebalance_table_shards](#rebalance_table_shards) 를 사용 하 여 한 번에 모두 드레이닝

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  드레이닝 리 밸런스가 완료 될 때까지 대기

4.  노드 제거

### <a name="replicate_table_shards"></a>\_테이블 복제 \_ 분할

복제 \_ 테이블 \_ 분할 () 함수는 지정 된 테이블의 복제 된 분할을 복제 합니다. 함수는 먼저 복제 된 분할 및 복제를 위해 페치할 수 있는 위치 목록을 계산 합니다. 그러면 함수는 해당 분할를 복사 하 고 해당 하는 분할 된 메타 데이터를 업데이트 하 여 복사본을 반영 합니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 분할를 복제 해야 하는 테이블의 이름입니다.

분할 된 ** \_ 복제 \_ 비율:** (선택 사항) 각 분할에 대해 달성할 수 있는 원하는 복제 비율입니다.

최대 분할 된 복제본 ** \_ \_ :** (선택 사항) 원하는 복제 비율에 도달 하기 위해 복사할 최대 분할 수입니다.

**제외 된 분할 \_ \_ 목록:** (선택 사항) 복제 작업 중에 복사할 수 없는 분할의 식별자입니다.

#### <a name="return-value"></a>Return Value

N/A

#### <a name="examples"></a>예

아래 예제에서는 github 이벤트 테이블의 분할를 분할 된 \_ 복제 요소에 복제 하려고 \_ 합니다 \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

이 예에서는 최대 10 개의 분할 된 복사본을 사용 하 여 github 이벤트 테이블의 분할를 \_ 원하는 복제 요소로 가져오려고 합니다. Rebalancer 장치는 원하는 복제 비율에 도달 하기 위해 최대 10 개의 분할를 복사 합니다.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>새 분할 된 데이터베이스가 있는 \_ 테 넌 트 격리 \_ \_ \_

이 함수는 배포 열에 특정 단일 값을 가진 행을 저장할 새 분할 된 데이터베이스가 만들어집니다. 다중 테 넌 트 Citus (Hyperscale) 사용 사례에 특히 유용 합니다. 대규모 테 넌 트는 자체 분할 된 자체 실제 노드에만 배치 될 수 있습니다.

#### <a name="arguments"></a>인수

**테이블 \_ 이름:** 새 분할 된 테이블을 가져올 테이블의 이름입니다.

**테 넌 트 \_ id:** 새 분할 된 데이터베이스가 할당 될 배포 열의 값입니다.

**cascade \_ 옵션:** (선택 사항)로 설정 하 \" 는 경우 CASCADE \" 는 현재 테이블의 [공동 배치 그룹](concepts-hyperscale-colocation.md)에 있는 모든 테이블에서 분할 된 항목을 격리 합니다.

#### <a name="return-value"></a>Return Value

분할 된 ** \_ id:** 함수는 새로 만든 분할 된 데이터베이스가 할당 된 고유 id를 반환 합니다.

#### <a name="examples"></a>예

테 넌 트 135에 대 한 lineitems를 보유할 새 분할 된 데이터베이스가 만들어집니다.

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>다음 단계

* 이 문서의 많은 함수는 시스템 [메타 데이터 테이블](reference-hyperscale-metadata.md) 을 수정 합니다.
* [서버 매개 변수](reference-hyperscale-parameters.md) 는 일부 함수의 동작을 사용자 지정 합니다.
