---
title: 테이블 공동 배치-hyperscale (Citus)-Azure Database for PostgreSQL
description: 더 빠른 쿼리를 위해 관련 정보를 함께 저장 하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967340"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL의 테이블 공동 위치 – Hyperscale (Citus)

공동 배치는 동일한 노드에 관련 정보를 함께 저장 하는 것을 의미 합니다. 모든 필요한 데이터를 네트워크 트래픽 없이 사용할 수 있게 되 면 쿼리가 신속 하 게 진행 될 수 있습니다. 서로 다른 노드에 있는 관련 데이터를 공동 배치 하면 쿼리가 각 노드에서 병렬로 효율적으로 실행 될 수 있습니다.

## <a name="data-colocation-for-hash-distributed-tables"></a>해시 분산 테이블에 대 한 데이터 공동 위치

Azure Database for PostgreSQL – Hyperscale (Citus)에서 배포 열의 값 해시가 분할 된 해시 범위 내에 있는 경우 행은 분할 된에 저장 됩니다. 동일한 해시 범위가 있는 분할는 항상 같은 노드에 배치 됩니다. 분산 열 값이 같은 행은 항상 테이블의 동일한 노드에 있습니다.

![분할](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>공동 위치의 실제 예

다중 테넌트 웹 분석 SaaS의 일부가 될 수 있는 다음 표를 참조 하세요.

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

이제 고객 지향 대시보드에서 발급 될 수 있는 쿼리에 응답 하려고 합니다. 예제 쿼리는 "테넌트 6에서 '/블로그 '로 시작 하는 모든 페이지에 대 한 지난 주의 방문 횟수를 반환 합니다."

데이터가 단일 서버 배포 옵션에 있는 경우 SQL에서 제공 하는 다양 한 관계형 작업 집합을 사용 하 여 쿼리를 쉽게 표현할 수 있습니다.

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

이 쿼리에 대 한 [작업 집합이](https://en.wikipedia.org/wiki/Working_set) 메모리에 있으면 단일 서버 테이블이 적절 한 솔루션입니다. Citus (Hyperscale) 배포 옵션을 사용 하 여 데이터 모델의 크기를 조정 하는 기회를 살펴보겠습니다.

### <a name="distribute-tables-by-id"></a>ID 별로 테이블 배포

단일 서버 쿼리는 테넌트의 수와 각 테넌트에 대해 저장 된 데이터가 증가 함에 따라 속도가 느려집니다. 작업 집합이 메모리의 맞춤을 중지 하 고 CPU가 병목 상태가 됩니다.

이 경우 Citus (Hyperscale)를 사용 하 여 여러 노드 간에 데이터를 분할할 수 있습니다. 분할을 결정할 때 가장 중요 한 첫 번째 선택은 배포 열입니다. 이벤트 테이블에 대해 `event_id`를 사용 하 고 `page` 테이블에 대해 `page_id` 하는 naive을 선택 합니다.

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

데이터가 여러 작업자에 게 분산 되는 경우 단일 PostgreSQL 노드에서와 같이 조인을 수행할 수 없습니다. 대신 두 개의 쿼리를 실행 해야 합니다.

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

그런 다음 두 단계의 결과를 응용 프로그램에서 결합 해야 합니다.

쿼리를 실행 하려면 여러 노드에 분산 된 분할의 데이터를 참조 해야 합니다.

![비효율적인 쿼리](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

이 경우 데이터 배포는 상당한 단점을 만듭니다.

-   각 분할을 쿼리하고 여러 쿼리를 실행 하는 오버 헤드
-   클라이언트에 많은 행을 반환 하는 Q1의 오버 헤드입니다.
-   Q2가 커집니다.
-   여러 단계로 쿼리를 작성 해야 하는 경우에는 응용 프로그램을 변경 해야 합니다.

데이터가 분산 되므로 쿼리를 병렬화 할 수 있습니다. 쿼리가 수행 하는 작업 양이 많은 분할를 쿼리 하는 오버 헤드 보다 훨씬 큰 경우에만 유용 합니다.

### <a name="distribute-tables-by-tenant"></a>테넌트 별로 테이블 배포

Citus (Hyperscale)에서 동일한 분포 열 값을 가진 행은 동일한 노드에 있도록 보장 됩니다. 부터 배포 열로 `tenant_id`를 사용 하 여 테이블을 만들 수 있습니다.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

이제 Citus (Hyperscale)에서 수정 없이 원래 단일 서버 쿼리를 답변할 수 있습니다 (Q1).

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Tenant_id에 대 한 필터 및 조인 때문에 Citus (Hyperscale)는 특정 테넌트에 대 한 데이터를 포함 하는 공동 배치 분할 집합을 사용 하 여 전체 쿼리에 대 한 답변을 받을 수 있음을 알고 있습니다. 단일 PostgreSQL 노드는 단일 단계에서 쿼리에 응답할 수 있습니다.

![향상 쿼리](media/concepts-hyperscale-colocation/colocation-better-query.png)

경우에 따라 테넌트 ID를 고유한 제약 조건 및 조인 조건에 포함 하도록 쿼리와 테이블 스키마를 변경 해야 합니다. 이 변경은 일반적으로 간단 합니다.

## <a name="next-steps"></a>Következő lépések

- [다중 테넌트 자습서](tutorial-design-database-hyperscale-multi-tenant.md)에서 테넌트 데이터를 공동 배치 하는 방법을 참조 하세요.
