---
title: 분산 테이블 수정-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Portal를 사용 하 여 분산 테이블을 만들고 수정 하는 SQL 명령-Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 628944f9763dc79148e0b64c97158064208412bf
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136939"
---
# <a name="distribute-and-modify-tables"></a>테이블 배포 및 수정

## <a name="distributing-tables"></a>테이블 배포

분산 테이블을 만들려면 먼저 테이블 스키마를 정의 해야 합니다. 이렇게 하려면 일반 PostgreSQL 테이블을 사용 하는 것과 같은 방법으로 [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) 문을 사용 하 여 테이블을 정의할 수 있습니다.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

그런 다음 create \_ distributed \_ table () 함수를 사용 하 여 테이블 배포 열을 지정 하 고 작업자 분할을 만들 수 있습니다.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

함수 호출은 github \_ 이벤트 테이블이 리포지토리 \_ id 열 (열 값을 해시 하 여)에 배포 되어야 한다는 Citus (hyperscale)를 알려 줍니다. 또한 함수는 citus \_ 수 및 citus \_ 복제 \_ 요소 구성 값을 사용 하 여 작업자 노드에 분할을 만듭니다.

분할의 총 citus 수를 만듭니다. \_ 여기서 각 분할 영역은 해시 공간의 일부를 소유 하 고 기본 citus \_ 복제 \_ 요소 구성 값을 기반으로 복제 됩니다. 작업자에 생성 된 분할 된 복제본의 테이블 스키마, 인덱스 및 제약 조건 정의가 코디네이터의 테이블과 동일 합니다. 복제본이 만들어지면 함수는 코디네이터에 모든 분산 메타 데이터를 저장 합니다.

만든 각 분할 된 데이터베이스에는 고유한 분할 된 데이터베이스 ID가 할당 되 고 모든 복제본은 동일한 분할 된 데이터베이스 ID를 가집니다. 분할은 worker 노드에 이름이 tablename shardid로 표시 됩니다 \' \_ \' . 여기서 tablename은 분산 테이블의 이름이 고 분할 된 id는 할당 된 고유 id입니다. 작업자 postgres 인스턴스에 연결 하 여 개별 분할에서 명령을 보거나 실행할 수 있습니다.

이제 분산 테이블에 데이터를 삽입 하 고이에 대 한 쿼리를 실행할 준비가 되었습니다. [테이블 및](reference-hyperscale-functions.md#table-and-shard-ddl) 분할 된 DDL 참조의이 섹션에서 사용 되는 UDF에 대해 자세히 알아볼 수도 있습니다.

### <a name="reference-tables"></a>참조 테이블

위의 메서드는 여러 개의 수평 분할 테이블을 배포 합니다.  또 다른 가능성은 단일 분할 된 데이터베이스로 테이블을 배포 하 고 분할 된 데이터베이스로 모든 작업자 노드를 복제 하는 것입니다. 이러한 방식으로 분산 된 테이블을 *참조 테이블 이라고 합니다.* 이러한 데이터는 클러스터의 여러 노드에서 자주 액세스 해야 하는 데이터를 저장 하는 데 사용 됩니다.

참조 테이블에 대 한 일반적인 후보는 다음과 같습니다.

-   더 큰 분산 테이블과 조인 해야 하는 작은 테이블
-   테 넌 트 ID 열이 없거나 \' 테 넌 트와 연결 되지 않은 다중 테 넌 트 앱의 테이블 (또는 마이그레이션 중에 테 넌 트와 연결 된 일부 테이블의 경우에도)
-   여러 열에 대해 unique 제약 조건을 필요로 하 고 크기가 작은 테이블

예를 들어 다중 테 넌 트 전자 상거래 사이트에서 해당 상점에 있는 트랜잭션의 판매 세금을 계산 해야 한다고 가정 합니다. 세금 정보 \' 는 어떤 테 넌 트에만 적용 됩니다. 공유 테이블에 배치 하는 것이 좋습니다. 미국 중심의 참조 테이블은 다음과 같습니다.

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

이제 장바구니에 대해 계산 세금 하나를 계산 하는 것과 같은 쿼리는 `states` 네트워크 오버 헤드 없이 테이블에 가입할 수 있으며, 상태 코드에 외래 키를 추가 하 여 유효성 검사를 더 쉽게 수행할 수 있습니다.

UDF는 단일 복제 된 분할 된 데이터베이스로 테이블을 배포 하는 것 외에도 `create_reference_table` Citus (Hyperscale) 메타 데이터 테이블에 참조 테이블로 표시 합니다. Citus (hyperscale)는이 방식으로 표시 된 테이블을 수정 하기 위해 2 단계 커밋 ([2pc](https://en.wikipedia.org/wiki/Two-phase_commit_protocol))을 자동으로 수행 하므로 강력한 일관성을 보장 합니다.

분할 된 테이블 수가 있는 분산 테이블이 있는 경우 다음과 같이 인식 된 참조 테이블로 업그레이드할 수 있습니다.

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

참조 테이블을 사용 하는 또 다른 예는 [다중 테 넌 트 데이터베이스 자습서](tutorial-design-database-hyperscale-multi-tenant.md)를 참조 하십시오.

### <a name="distributing-coordinator-data"></a>코디네이터 데이터 배포

기존 PostgreSQL 데이터베이스를 Citus (Hyperscale) 클러스터의 코디네이터 노드로 변환 하면 해당 테이블의 데이터를 효율적으로 배포 하 고 응용 프로그램의 중단을 최소화할 수 있습니다.

`create_distributed_table`앞에서 설명한 함수는 비어 있거나 비어 있지 않은 테이블 모두에서 작동 하며 후자의 경우 클러스터 전체에서 테이블 행을 자동으로 배포 합니다. 메시지의 유무에 따라 데이터를 복사 하는지 알 수 있습니다. \" 참고: 로컬 테이블에서 데이터 복사 \. . \" 예를 들어:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

데이터를 마이그레이션하는 동안 테이블에 대 한 쓰기를 차단 하 고, 함수가 커밋되면 보류 중인 쓰기가 분산 쿼리로 처리 됩니다. 함수가 실패 하면 쿼리는 다시 로컬 상태가 됩니다. 읽기는 정상적으로 계속 되 고 함수가 커밋되면 배포 쿼리가 됩니다.

B에 대 한 외래 키가 있는 테이블 A와 B를 배포할 때 먼저 키 대상 테이블 B를 배포 합니다. 잘못 된 순서로 수행 하면 오류가 발생 합니다.

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

올바른 순서로 배포할 수 없는 경우에는 외래 키를 삭제 하 고 테이블을 배포 하 고 외래 키를 다시 만듭니다.

Amazon RDS에서 Citus (Hyperscale) 클라우드와 같은 외부 데이터베이스에서 데이터를 마이그레이션하는 경우 먼저를 통해 Citus (Hyperscale) 분산 테이블을 만든 `create_distributed_table` 다음 데이터를 테이블에 복사 합니다.
분산 테이블에 복사 하면 코디네이터 노드의 공간이 부족 하지 않습니다.

## <a name="colocating-tables"></a>공동 배치 테이블

공동 배치는 동일한 컴퓨터에 관련 정보를 보관 하는 것을 의미 합니다. 이를 통해 전체 데이터 집합에 대 한 가로 확장성을 활용 하면서 효율적인 쿼리를 사용할 수 있습니다. 자세한 내용은 [공동](concepts-hyperscale-colocation.md)배치를 참조 하세요.

테이블은 그룹으로 공동 배치 됩니다. 테이블의 공동 위치 그룹 할당을 수동으로 제어 하려면 `colocate_with` 의 선택적 매개 변수를 사용 `create_distributed_table` 합니다. \'테이블의 공동 배치를 고려 하지 않는 경우 \' 이 매개 변수를 생략 합니다. 이 값은 기본적으로 값으로 설정 되며,이 값은 `'default'` 동일한 배포 열 유형, 분할 개수 및 복제 요인이 있는 다른 기본 공동 배치 테이블을 사용 하 여 테이블을 그룹화 합니다. 이 암시적 공동 배치를 중단 하거나 업데이트 하려는 경우에는를 사용할 수 있습니다 `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

새 테이블이 해당 하는 암시적 공동 위치 그룹의 다른 테이블과 관련 되지 않은 경우를 지정 `colocated_with => 'none'` 합니다.

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

관련 되지 않은 테이블을 자체 공동 배치 그룹으로 분할 하면 동일한 그룹의 분할 함께 이동 해야 하기 때문에 분할 된 테이블 [재조정](howto-hyperscale-scaling.md#rebalance-shards) 성능이 향상 됩니다.

테이블이 조인 될 때와 같이 테이블이 실제로 관련 된 경우 명시적으로 배치 하는 것이 적합할 수 있습니다. 적절 한 공동 배치의 향상은 균형 재조정 오버 헤드 보다 더 중요 합니다.

여러 테이블을 명시적으로 배치 하려면 하나를 배포한 다음 다른 테이블을 공동 위치 그룹에 배치 합니다. 예를 들면 다음과 같습니다.

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

공동 배치 그룹에 대 한 정보는 [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) 테이블에 저장 되는 반면 [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) 는 어떤 테이블이 어떤 그룹에 할당 되는지 보여 줍니다.

## <a name="dropping-tables"></a>테이블 삭제

표준 PostgreSQL DROP TABLE 명령을 사용 하 여 분산 테이블을 제거할 수 있습니다. 일반 테이블과 마찬가지로 DROP TABLE은 대상 테이블에 대해 존재 하는 인덱스, 규칙, 트리거 및 제약 조건을 제거 합니다. 또한 작업자 노드에 대 한 분할을 삭제 하 고 해당 메타 데이터를 정리 합니다.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>테이블 수정

Citus (hyperscale)는 여러 종류의 DDL 문을 자동으로 전파 합니다.
코디네이터 노드의 분산 테이블을 수정 하면 작업자의 분할 업데이트 됩니다. 다른 DDL 문에는 수동 전파가 필요 하며, 다른 DDL 문에는 배포 열을 수정 하는 것과 같은 일부 다른 DDL이 허용 되지 않습니다.
자동 전파에 적합 하지 않은 DDL을 실행 하려고 하면 오류가 발생 하 고 코디네이터 노드의 테이블이 변경 되지 않습니다.

다음은 전파 되는 DDL 문의 범주에 대 한 참조입니다.
[구성 매개 변수](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean) 를 사용 하 여 자동 전파를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

### <a name="addingmodifying-columns"></a>열 추가/수정

Citus (hyperscale)는 대부분의 [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) 명령을 자동으로 전파 합니다. 단일 컴퓨터 PostgreSQL 데이터베이스에서와 같이 열을 추가 하거나 기본값을 변경 하는 작업은 다음과 같습니다.

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

기존 열을 변경 하거나 데이터 형식을 변경 하는 등의 기존 열에 대 한 중요 한 변경도 마찬가지입니다. 그러나 [배포 열의](concepts-hyperscale-nodes.md#distribution-column) 데이터 형식은 변경할 수 없습니다.
이 열은 테이블 데이터가 Hyperscale (Citus) 클러스터를 통해 배포 되는 방식을 결정 하 고 데이터 형식을 수정 하려면 데이터를 이동 해야 합니다.

이렇게 하려고 하면 오류가 발생 합니다.

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>제약 조건 추가/제거

Citus (Hyperscale)를 사용 하면 데이터베이스 제약 조건을 포함 하 여 관계형 데이터베이스의 보안을 계속 사용할 수 있습니다 (PostgreSQL [문서](https://www.postgresql.org/docs/current/static/ddl-constraints.html)참조).
분산 된 시스템의 특성으로 인해 Citus (Hyperscale)는 작업자 노드 간의 상호 참조 고유성 제약 조건 또는 참조 무결성을 따르지 않습니다.

공동 배치 분산 테이블 간에 외래 키를 설정 하려면 항상 키에 분포 열을 포함 합니다. 배포 열을 포함 하 여 키를 복합 상태로 만들 수 있습니다.

다음과 같은 상황에서 외래 키를 만들 수 있습니다.

-   두 로컬 (분산 되지 않은) 테이블 사이에
-   두 참조 테이블 사이에
-   키에 배포 열이 포함 된 경우 두 [공동 배치](concepts-hyperscale-colocation.md) 분산 테이블 사이
-   [참조 테이블](concepts-hyperscale-nodes.md#type-2-reference-tables) 을 참조 하는 분산 테이블

참조 테이블에서 분산 테이블로의 외래 키는 지원 되지 않습니다.

> [!NOTE]
>
> 기본 키와 고유성 제약 조건에는 배포 열이 포함 되어야 합니다. 비 배포 열에 추가 하면 오류가 생성 됩니다.

이 예에서는 분산 테이블에 기본 키 및 외래 키를 만드는 방법을 보여 줍니다.

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

마찬가지로 고유성 제약 조건에 분포 열을 포함 합니다.

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Not-null 제약 조건은 작업자 간에 조회가 필요 하지 않으므로 모든 열에 적용할 수 있습니다 (분포 또는 not).

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>NOT VALID 제약 조건 사용

경우에 따라 새 행에 대 한 제약 조건을 적용 하는 것이 유용할 수 있지만 기존 비준수 행은 변경 되지 않은 상태로 유지할 수 있습니다. Citus (hyperscale)는 CHECK 제약 조건 및 외래 키에 대해이 기능을 지원 하며, PostgreSQL \' s \" NOT VALID 제약 조건 지정을 사용 \" 합니다.

예를 들어 사용자 프로필을 [참조 테이블](concepts-hyperscale-nodes.md#type-2-reference-tables)에 저장 하는 응용 프로그램이 있다고 가정 합니다.

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

이 과정에서는 몇 가지 비 주소를 테이블에 가져올 것으로 가정 합니다.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

주소 유효성을 검사 하려고 하지만, PostgreSQL는 일반적으로 기존 행에 대해 실패 하는 CHECK 제약 조건을 추가 하는 것을 허용 하지 않습니다. 그러나 잘못 된 것으로 표시 된 제약 조건은 허용 되지 *않습니다* .

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

이제 새 행이 보호 됩니다.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

나중에는 사용량이 적은 시간에 데이터베이스 관리자가 잘못 된 행을 수정 하 고 제약 조건의 유효성을 다시 검사할 수 있습니다.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

PostgreSQL 설명서에는 [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) 섹션에서 not VALID 및 VALIDATE 제약 조건에 대 한 자세한 정보가 있습니다.

### <a name="addingremoving-indices"></a>인덱스 추가/제거

Citus (hyperscale)는 [인덱스](https://www.postgresql.org/docs/current/static/sql-createindex.html)추가 및 제거를 지원 합니다.

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

인덱스를 추가 하면 쓰기 잠금이 사용 되며이는 다중 테 넌 트 시스템 레코드에 바람직하지 않을 수 있습니다 \" . \" 응용 프로그램 가동 중지 시간을 최소화 하려면 대신 인덱스를 [동시](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) 에 만듭니다. 이 방법을 사용 하려면 표준 인덱스 빌드 보다 많은 작업을 수행 해야 하며 완료 하는 데 더 많은 시간이 걸립니다. 그러나 인덱스를 작성 하는 동안에는 정상적인 작업을 계속할 수 있으므로이 방법은 프로덕션 환경에서 새 인덱스를 추가 하는 데 유용 합니다.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
