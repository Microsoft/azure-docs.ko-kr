---
title: 노드-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL에서 서버 그룹의 노드 및 테이블 형식에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: a02583825f4a1ef15992aa2307e7f666d5abeaba
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136459"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL의 노드 및 테이블-Hyperscale (Citus)

## <a name="nodes"></a>노드

Citus (Hyperscale) 호스팅 형식을 사용 하면 Azure Database for PostgreSQL 서버 (노드)가 "비공유" 아키텍처에서 서로 조정 될 수 있습니다. 서버 그룹의 노드는 단일 서버에서 사용할 수 있는 것 보다 더 많은 데이터를 포함 하 고 더 많은 CPU 코어를 사용 합니다. 아키텍처를 사용 하면 서버 그룹에 노드를 더 추가 하 여 데이터베이스의 크기를 조정할 수도 있습니다.

### <a name="coordinator-and-workers"></a>코디네이터 및 작업자

모든 서버 그룹에는 코디네이터 노드와 여러 작업자가 있습니다. 응용 프로그램은 해당 쿼리를 코디네이터 노드에 전송 하 여 관련 작업자에 게 릴레이 하 고 결과를 누적 합니다. 응용 프로그램은 작업자에 직접 연결할 수 없습니다.

Citus (hyperscale)를 사용 하면 데이터베이스 관리자가 테이블을 *배포* 하 여 다른 작업자 노드에 다른 행을 저장할 수 있습니다. 분산 테이블은 성능의 규모를 조정 하는 핵심입니다. 테이블 배포에 실패 하면 해당 테이블은 완전히 코디네이터 노드에 그대로 남아 있으므로 크로스 시스템 병렬 처리를 사용할 수 없습니다.

코디네이터는 분산 된 테이블에 대 한 각 쿼리에 대해이를 단일 작업자 노드로 라우트 하거나, 필요한 데이터가 단일 노드에 있는지 또는 여러 데이터가 아니라에 따라 몇 가지에 걸쳐 있습니다. 코디네이터는 컨설팅 메타 데이터 테이블에서 수행할 작업을 결정 합니다. 이러한 테이블은 작업자 노드의 DNS 이름 및 상태와 노드 간 데이터 배포를 추적 합니다.

## <a name="table-types"></a>테이블 유형

Citus (Hyperscale) 서버 그룹에는 세 가지 유형의 테이블이 있으며 각각 노드에 다르게 저장 되며 다른 용도로 사용 됩니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산 테이블

첫 번째 유형과 가장 일반적으로 분산 된 테이블이 있습니다. 이러한 테이블은 SQL 문에 대 한 일반 테이블 처럼 보이지만 작업자 노드 간에 수평 분할 됩니다. 즉, 테이블의 행이 분할 라는 조각 테이블의 다른 노드에 저장 됩니다.

Citus (hyperscale)는 클러스터 전체에서 SQL은 아니지만 SQL DDL 문이 실행 됩니다.
분산 테이블의 스키마를 변경 하면 작업자 간에 테이블의 모든 분할 업데이트 됩니다.

#### <a name="distribution-column"></a>분포 열

Citus (hyperscale)는 알고리즘 분할를 사용 하 여 분할에 행을 할당 합니다. 할당은 배포 열 이라고 하는 테이블 열의 값에 따라 명확 하 게 결정 됩니다. 클러스터 관리자는 테이블을 배포할 때이 열을 지정 해야 합니다.
적절 한 선택은 성능 및 기능에 중요 합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 전체 내용이 단일 분할 된 데이터베이스로 집중 되는 분산 테이블의 유형입니다. 분할 된 데이터베이스가 모든 작업자에 복제 됩니다. 모든 작업자의 쿼리는 다른 노드의 행을 요청 하는 네트워크 오버 헤드 없이 로컬에서 참조 정보에 액세스할 수 있습니다. 행 마다 별도의 분할를 구분할 필요가 없기 때문에 참조 테이블에는 배포 열이 없습니다.

참조 테이블은 일반적으로 작지만 모든 작업자 노드에서 실행 되는 쿼리와 관련 된 데이터를 저장 하는 데 사용 됩니다. 예는 주문 상태 또는 제품 범주와 같은 열거 된 값입니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

Citus (Hyperscale)를 사용 하는 경우 연결 하는 코디네이터 노드는 일반 PostgreSQL 데이터베이스입니다. 코디네이터에서 일반 테이블을 만들고 분할 하지 않도록 선택할 수 있습니다.

로컬 테이블에 적합 한 후보는 조인 쿼리에 참여 하지 않는 작은 관리 테이블입니다. 예를 들어 응용 프로그램 로그인 및 인증을 위한 사용자 테이블이 있습니다.

## <a name="shards"></a>분할

이전 섹션에서는 분산 테이블이 작업자 노드에 분할로 저장 되는 방법에 대해 설명 했습니다. 이 섹션에서는 기술에 대해 자세히 설명 합니다.

`pg_dist_shard`코디네이터의 메타 데이터 테이블에는 시스템에 있는 각 분산 된 테이블의 각 분할 된 행이 포함 됩니다. 이 행은 해시 공간의 정수 범위 (shardminvalue, shardmaxvalue)를 사용 하 여 분할 된 ID와 일치 합니다.

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

코디네이터 노드가 행을 보유 하 고 있는 분할 영역을 확인 하려는 경우 `github_events` 행의 분포 열 값을 해시 합니다. 그런 다음 노드는 \' 해시 된 값을 포함 하는 분할 된 범위를 확인 합니다. 범위는 해시 함수의 이미지가 비연속 합집합이 되도록 정의 됩니다.

### <a name="shard-placements"></a>분할 영역 배치

분할 된 102027이 해당 하는 행과 연결 되어 있다고 가정 합니다. 작업자 중 하나에서 호출 된 테이블에서 행을 읽거나 씁니다 `github_events_102027` . 작업자는 무엇입니까? 이는 메타 데이터 테이블에 의해 완전히 결정 됩니다. 분할 된 작업자에 대 한 분할을 분할 된 배치 라고 합니다.

코디네이터 노드는와 같은 특정 테이블을 참조 하는 조각으로 쿼리를 다시 작성 하 고 해당 하 `github_events_102027` 는 작업자의 조각을 실행 합니다. 다음은 분할 된 ID 102027을 보유 하는 노드를 찾기 위해 내부적으로 실행 되는 쿼리의 예입니다.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>다음 단계

- 데이터 모델링을 준비 하기 위한 [응용 프로그램 형식 결정](concepts-hyperscale-app-type.md)
