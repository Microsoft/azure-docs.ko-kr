---
title: 서버 매개 변수 – Hyperscale (Citus)-Azure Database for PostgreSQL
description: Citus (Hyperscale) SQL API의 매개 변수
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136838"
---
# <a name="server-parameters"></a>서버 매개 변수

Citus (Hyperscale)의 동작에 영향을 주는 다양 한 서버 매개 변수, 표준 PostgreSQL의 매개 변수 및 Citus (Hyperscale) 관련 매개 변수가 있습니다. PostgreSQL 구성 매개 변수에 대 한 자세한 내용은 PostgreSQL 설명서의 [런타임 구성](http://www.postgresql.org/docs/current/static/runtime-config.html) 섹션을 참조 하세요.

이 참조의 나머지 부분에서는 Citus (Hyperscale) 특정 구성 매개 변수를 논의 합니다. 이러한 매개 변수는 Citus (Hyperscale) 서버 그룹에 대 한 **설정** 아래의 **작업자 노드 매개 변수** 아래 Azure Portal에서 설정할 수 있습니다.

> [!NOTE]
>
> Citus 엔진의 이전 버전을 실행 하는 하이퍼 확장 서버 그룹은 아래 나열 된 모든 매개 변수를 제공 하지 않을 수 있습니다.

## <a name="general-configuration"></a>일반 구성

### <a name="citususe_secondary_nodes-enum"></a>citus \_ 보조 \_ 노드 (enum) 사용

SELECT 쿼리에 대 한 노드를 선택할 때 사용할 정책을 설정 합니다. ' Always '로 설정 된 경우 planner는 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)에서 ' 보조 ' noderole로 표시 된 노드만 쿼리 합니다.

이 열거형에 대해 지원 되는 값은 다음과 같습니다.

-   **never:** (기본값) 모든 읽기는 주 노드에서 수행 됩니다.
-   **항상:** 대신 보조 노드에 대해 읽기를 실행 하 고 insert/update 문을 사용할 수 없습니다.

### <a name="cituscluster_name-text"></a>citus \_ 이름 (텍스트)

코디네이터 노드 계획자에 조정 된 클러스터를 알립니다. 클러스터 \_ 이름이 설정 되 면 planner는 해당 클러스터의 작업자 노드만 쿼리 합니다.

### <a name="citusenable_version_checks-boolean"></a>citus \_ 버전 \_ 검사를 사용 합니다 (부울).

Citus (Hyperscale) 버전을 업그레이드 하려면 서버를 다시 시작 하 여 새 공유 라이브러리를 선택한 다음 ALTER EXTENSION UPDATE 명령을 사용 해야 합니다.  두 단계를 모두 실행 하는 데 실패 하면 오류 또는 작동이 중단 될 수 있습니다.
따라서 Citus (hyperscale)는 코드 버전과 확장 일치 항목의 유효성을 검사 하 고, 그렇지 않은 경우 오류를 발생 시킵니다 \' .

이 값의 기본값은 true 이며 코디네이터에 적용 됩니다. 드문 경우 지만 복잡 한 업그레이드 프로세스에서이 매개 변수를 false로 설정 하 여 검사를 사용 하지 않도록 설정 해야 할 수 있습니다.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus \_ 분산 \_ 교착 상태 \_ 검색 (부울)

서버 로그에서 distributed 교착 상태 검색 관련 처리를 기록할지 여부를 지정 합니다. 기본값은 false입니다.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus 분산 \_ 교착 상태 \_ 검색 \_ 계수 (부동 소수점)

분산 교착 상태를 확인 하기 전에 대기할 시간을 설정 합니다. 특히 대기 시간에는이 값에 PostgreSQL \' s [교착 상태 \_ 시간 제한이](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 설정 됩니다. 기본값은 `2`입니다. 값이 `-1` 이면 분산 된 교착 상태 검색을 사용 하지 않도록 설정 합니다.

### <a name="citusnode_connection_timeout-integer"></a>citus \_ 연결 \_ 제한 시간 (정수)

`citus.node_connection_timeout`Guc는 연결 설정을 대기 하는 최대 기간 (밀리초)을 설정 합니다. 하나 이상의 작업자 연결이 설정 되기 전에 제한 시간이 초과 되 면 hyperscale (Citus)에서 오류를 발생 시킵니다. 이 GUC는 코디네이터와 작업자 간의 연결 및 작업자 간의 연결에 영향을 줍니다.

-   기본값: 5 초
-   최소: 10 밀리초
-   최대: 1 시간

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>쿼리 통계

### <a name="citusstat_statements_purge_interval-integer"></a>citus \_ 문 \_ 제거 \_ 간격 (정수)

유지 관리 디먼이에서 일치 하지 않는 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 의 레코드를 제거 하는 빈도를 설정 합니다 `pg_stat_statements` . 이 구성 값은 초 단위로 제거 사이의 시간 간격을 설정 합니다. 기본값은 10입니다. 0 값은 제거를 사용 하지 않도록 설정 합니다.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

이 매개 변수는 코디네이터에 적용 되며 런타임에 변경할 수 있습니다.

## <a name="data-loading"></a>데이터 로드

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus \_ \_ 커밋 \_ 프로토콜 (enum)

해시 분산 테이블에서 복사를 수행할 때 사용할 커밋 프로토콜을 설정 합니다. 개별 분할 된 데이터를 배치 하면 복사본을 실행 하는 동안 오류가 발생 하는 경우 데이터가 수집 되지 않도록 트랜잭션 블록에서 복사본이 수행 됩니다. 그러나 모든 위치에서 복사가 성공 하지만 모든 트랜잭션을 커밋하기 전에 (하드웨어) 오류가 발생 하는 특정 실패 사례가 있습니다. 이 매개 변수를 사용 하 여 다음 커밋 프로토콜 중에서 선택 하 여 데이터 손실을 방지할 수 있습니다.

-   **2pc:** (기본값) 분할 된 위치에서 복사가 수행 되는 트랜잭션은 먼저 PostgreSQL \' s [2 단계 커밋을](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) 사용 하 여 준비 된 다음 커밋됩니다. 커밋 준비 또는 롤백 준비를 사용 하 여 실패 한 커밋을 수동으로 복구 하거나 중단할 수 있습니다.
    2pc를 사용 하는 경우 모든 작업자의 [최대 \_ 준비 된 \_ 트랜잭션을](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) 일반적으로 max connections와 동일한 값으로 늘려야 합니다 \_ .
-   **1pc:** 분할 된 위치에서 복사가 수행 되는 트랜잭션은 단일 라운드로 커밋됩니다. 모든 배치에서 복사가 성공한 후 커밋이 실패할 경우 데이터가 손실 될 수 있습니다 (드물게 발생 함).

### <a name="citusshard_replication_factor-integer"></a>citus \_ 복제 \_ 비율 (정수)

분할에 대 한 복제 비율, 즉 분할가 배치 되는 노드 수를 설정 하 고 기본값을 1로 설정 합니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다. 이 매개 변수에 대 한 이상적인 값은 클러스터의 크기와 노드 실패의 빈도에 따라 달라 집니다.  예를 들어 큰 클러스터를 실행 하 고 더 자주 노드 오류를 관찰 하는 경우이 복제 요인을 늘릴 수 있습니다.

### <a name="citusshard_count-integer"></a>citus \_ 수 (integer)

해시 분할 테이블의 분할 된 수를 설정 하 고 기본값을 32로 설정 합니다.  이 값은 해시 분할 된 테이블을 만들 때 [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF에서 사용 됩니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

### <a name="citusshard_max_size-integer"></a>citus \_ 최대 \_ 크기 (정수)

분할이 분할 되기 전에 분할 되는 최대 크기를 설정 하 고 기본값은 1gb로 설정 합니다. 한 분할 된 데이터베이스의 원본 파일 \' 크기 (준비에 사용 됨)가이 구성 값을 초과 하는 경우 데이터베이스는 새 분할 된 데이터베이스를 만들도록 합니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

## <a name="planner-configuration"></a>Planner 구성

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus \_ 절 \_ 행 \_ 인출 \_ 수 (integer)

Limit 절 최적화에 대해 작업당 반입할 행 수를 설정 합니다.
경우에 따라 limit 절이 있는 쿼리를 선택 하 여 결과를 생성 하기 위해 각 작업의 모든 행을 인출 해야 할 수도 있습니다. 이러한 경우와 근사값으로 의미 있는 결과를 생성 하는 경우이 구성 값은 각 분할 된 데이터베이스가 인출 되는 행 수를 설정 합니다. Limit 근사치는 기본적으로 사용 하지 않도록 설정 되며이 매개 변수는-1로 설정 됩니다. 이 값은 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus \_ distinct \_ error \_ rate (부동 소수점)

Citus (hyperscale)는 postgresql-hll 확장을 사용 하 여 개수 (고유) 근사치를 계산할 수 있습니다. 이 구성 항목은 개수 (고유)를 계산할 때 원하는 오류 발생률을 설정 합니다. 기본값 인 0.0는 count (distinct)에 대해 근사치를 사용 하지 않도록 설정 합니다. 및 1.0는 결과의 정확도를 보장 하지 않습니다. 최상의 결과를 위해이 매개 변수를 0.005로 설정 하는 것이 좋습니다. 이 값은 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

### <a name="citustask_assignment_policy-enum"></a>citus \_ 할당 \_ 정책 (enum)

> [!NOTE]
> 이 GUC는 [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) 이 1 보다 크거나 [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)에 대 한 쿼리의 경우에만 적용할 수 있습니다.

작업을 작업자에 게 할당할 때 사용할 정책을 설정 합니다. 코디네이터는 분할 된 위치에 따라 작업자에 게 작업을 할당 합니다. 이 구성 값은 이러한 할당을 만들 때 사용할 정책을 지정 합니다.
현재 사용할 수 있는 세 가지 작업 할당 정책이 있습니다.

-   **greedy:** Greedy 정책은 기본값 이며 작업자 간에 작업을 균등 하 게 분산 하는 것을 목표로 합니다.
-   **라운드 로빈:** 라운드 로빈 정책은 다른 복제본 간에 반복 되는 라운드 로빈 방식으로 작업자에 게 작업을 할당 합니다. 이 정책을 사용 하면 테이블에 대 한 분할 된 데이터베이스가 작업자 수와 비교할 때 클러스터 사용률이 향상 됩니다.
-   **첫 번째-복제본:** 첫 번째 복제본 정책은 분할에 대 한 배치 (복제본)의 삽입 순서를 기준으로 작업을 할당 합니다. 즉, 분할 된 데이터베이스에 대 한 조각 쿼리는 해당 분할 된 데이터베이스의 첫 번째 복제본이 있는 작업자에 게 할당 됩니다.
    이 메서드를 사용 하면 어떤 노드에 사용 되는 분할 (즉, 더 강력한 메모리 상주 보장)를 강력 하 게 보장할 수 있습니다.

이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

## <a name="intermediate-data-transfer"></a>중간 데이터 전송

### <a name="citusbinary_worker_copy_format-boolean"></a>citus \_ worker \_ 복사 \_ 형식 (부울)

이진 복사본 형식을 사용 하 여 작업자 간에 중간 데이터를 전송 합니다.
대규모 테이블 조인 중에 Citus (Hyperscale)는 서로 다른 작업자 간에 데이터를 동적으로 다시 분할 하 고 순서를 다시 조정 해야 할 수 있습니다. 기본적으로이 데이터는 텍스트 형식으로 전송 됩니다. 이 매개 변수를 사용 하도록 설정 하면 PostgreSQL의 이진 serialization 형식을 사용 하 여이 데이터를 전송 하도록 데이터베이스에 지시 합니다. 이 매개 변수는 작업자에 게 적용 되며 postgresql 파일에서 변경 해야 합니다. 구성 파일을 편집한 후 사용자는 SI의 신호 신호를 보내거나 서버를 다시 시작 하 여이 변경 내용을 적용할 수 있습니다.

### <a name="citusbinary_master_copy_format-boolean"></a>citus \_ 마스터 \_ 복사본 \_ 형식 (부울)

이진 복사본 형식을 사용 하 여 코디네이터와 작업자 간에 데이터를 전송 합니다. 분산 쿼리를 실행 하는 경우 작업자는 최종 집계를 위해 코디네이터에 중간 결과를 전송 합니다. 기본적으로이 데이터는 텍스트 형식으로 전송 됩니다. 이 매개 변수를 사용 하도록 설정 하면 PostgreSQL의 이진 serialization 형식을 사용 하 여이 데이터를 전송 하도록 데이터베이스에 지시 합니다.
이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

### <a name="citusmax_intermediate_result_size-integer"></a>citus \_ 중간 \_ 결과 \_ 크기 (정수)

실행을 위해 작업자 노드로 푸시할 수 없는 Cte에 대 한 중간 결과의 최대 크기 (KB)입니다. 기본값은 1gb이 고 값이-1 이면 제한이 없음을 의미 합니다.
제한을 초과 하는 쿼리는 취소 되 고 오류 메시지가 생성 됩니다.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus \_ ddl \_ 전파를 사용 합니다 (부울).

DDL 변경 내용을 코디네이터에서 모든 작업자에 게 자동으로 전파할 것인지 여부를 지정 합니다. 기본값은 true입니다. 일부 스키마 변경에는 테이블에 대 한 액세스 배타 잠금이 필요 하며, 자동 전파가 모든 작업자에 게 순차적으로 적용 되기 때문에 Citus (Hyperscale) 클러스터가 일시적으로 응답 하지 않도록 할 수 있습니다. 이 설정을 사용 하지 않도록 설정 하 고 변경 내용을 수동으로 전파 하도록 선택할 수 있습니다.

## <a name="executor-configuration"></a>Executor 구성

### <a name="general"></a>일반

#### <a name="citusall_modifications_commutative"></a>citus \_ 수정 \_

Citus (hyperscale)는 동작의 정확성을 보장 하기 위해 연산의 교환 규칙을 적용 하 고 수정 작업에 적절 한 잠금을 획득 합니다. 예를 들어 INSERT 문이 다른 INSERT 문과 commutes UPDATE 또는 DELETE 문이 아닌 것으로 가정 합니다. 마찬가지로 UPDATE 또는 DELETE 문이 다른 UPDATE 또는 DELETE 문에서 commute 않는 것으로 가정 합니다. 이러한 예방 조치는 업데이트 및 삭제에서 더 강력한 잠금을 획득 하기 위해 Hyperscale (Citus)이 필요 함을 의미 합니다.

Insert 또는 다른 업데이트를 사용 하 여 교환 하는 UPDATE 문이 있는 경우이 매개 변수를 true로 설정 하 여 이러한 연산의 교환 가정을 완화할 수 있습니다. 이 매개 변수를 true로 설정 하면 모든 명령이 교환 되는 것으로 간주 되어 전체 처리량을 향상 시킬 수 있는 공유 잠금을 요청 합니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

#### <a name="citusremote_task_check_interval-integer"></a>citus. 원격 \_ 작업 \_ 확인 \_ 간격 (정수)

Hyperscale (Citus)이 작업 추적기 실행자에서 관리 하는 작업의 상태를 확인 하는 빈도를 설정 합니다. 기본값은 10 밀리초입니다. 코디네이터는 작업자에 게 작업을 할당 한 다음 각 작업의 진행 상황에 대해 정기적으로 확인 \' 합니다. 이 구성 값은 두 그 검사 간의 시간 간격을 설정 합니다. 이 매개 변수는 코디네이터에 적용 되며 런타임에 설정할 수 있습니다.

#### <a name="citustask_executor_type-enum"></a>citus 실행 기 \_ \_ 형식 (열거형)

Citus (hyperscale)에는 분산 된 SELECT 쿼리를 실행 하기 위한 세 가지 실행자 형식이 있습니다.  이 구성 매개 변수를 설정 하 여 원하는 실행자를 선택할 수 있습니다. 이 매개 변수에 허용 되는 값은 다음과 같습니다.

-   **적응:** 기본값입니다. 여러 분할에 걸쳐 있는 집계 및 공동 배치 조인이 포함 된 쿼리에 대 한 빠른 응답을 하는 것이 가장 좋습니다.
-   **작업-추적기:** 작업 추적기 실행 기는 작업자 노드 전체에서 데이터를 순서 섞기 하 고 효율적인 리소스 관리를 필요로 하는 장기 실행 및 복잡 한 쿼리에 적합 합니다.
-   **실시간:** (사용 되지 않음)은 적응 실행자와 비슷한 용도로 사용 되지만 유연성이 낮지만 작업자 노드에서 더 많은 연결 압력을 일으킬 수 있습니다.

이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus \_ 작업 \_ 쿼리 \_ 로그 \_ 수준 (enum) {#multi_task_logging}

둘 이상의 태스크를 생성 하는 쿼리 (즉, 둘 이상의 분할 된 데이터베이스가 있는 경우)에 대 한 로그 수준을 설정 합니다. 로깅은 다중 테 넌 트 응용 프로그램을 마이그레이션하는 동안 유용 합니다. 이러한 쿼리에 대 한 오류 또는 경고를 선택 하 여 이러한 쿼리에 대 한 오류를 확인 하 고 테 넌 트 id 필터를 추가할 수 있습니다 \_ . 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다. 이 매개 변수의 기본값은 \' off \' 입니다.

이 열거형에 대해 지원 되는 값은 다음과 같습니다.

-   **끄기:** 여러 작업을 생성 하는 쿼리 (즉, 여러 분할에 걸쳐 있는 쿼리) 로깅 해제
-   **디버그:** 디버그 심각도 수준에서 문을 기록 합니다.
-   **로그:** 로그 심각도 수준에서 문을 기록 합니다. 로그 줄에는 실행 된 SQL 쿼리가 포함 됩니다.
-   **알림:** 알림 심각도 수준에서 문을 기록 합니다.
-   **경고:** 경고 심각도 수준에서 문을 기록 합니다.
-   **오류:** 오류 심각도 수준에서 문을 기록 합니다.

개발 테스트 중에를 사용 하는 것이 유용할 수 `error` 있으며 실제 프로덕션 배포 중에는 더 낮은 로그 수준 `log` 입니다.
를 선택 하면 `log` 쿼리 자체를 사용 하 여 데이터베이스 로그에 다중 태스크 쿼리가 표시 됩니다 \" .\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus. 다시 \_ 분할 \_ 조인 사용 (부울)

일반적으로 적응 실행자와의 다시 분할 조인을 수행 하려는 시도는 오류 메시지와 함께 실패 합니다.  그러나 `citus.enable_repartition_joins` 를 true로 설정 하면 Citus (Hyperscale)를 사용 하 여 조인을 수행할 수 있습니다.  기본값은 false입니다.

### <a name="task-tracker-executor-configuration"></a>작업 추적기 실행자 구성

#### <a name="citustask_tracker_delay-integer"></a>citus \_ tracker \_ 지연 (정수)

이 매개 변수는 작업 관리를 반올림 하 고 기본값을 200 밀리초로 설정 합니다. 작업 추적기 프로세스는 정기적으로 절전 모드를 해제 하 고, 할당 된 모든 작업을 실행 하 고, 이러한 작업을 예약 하 고 실행 합니다.  그런 다음 작업 추적기는 이러한 작업을 다시 실행 하기 전에 시간 동안 대기 합니다.
이 구성 값은 해당 대기 기간의 길이를 결정 합니다. 이 매개 변수는 작업자에 게 적용 되며 postgresql 파일에서 변경 해야 합니다. 구성 파일을 편집한 후 사용자는 SI의 신호 신호를 보내거나 서버를 다시 시작 하 여 변경 내용을 적용할 수 있습니다.

이 매개 변수를 감소 시켜 관리 라운드 간의 시간 간격을 줄여 작업 추적기 실행자 때문에 발생 하는 지연 시간을 줄일 수 있습니다.
지연 시간을 줄이면 분할 된 쿼리가 짧고 정기적으로 상태를 업데이트 하는 경우에 유용 합니다.

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus \_ 할당 \_ 태스크 \_ 일괄 처리 \_ 크기 (정수)

코디네이터의 작업 추적기 실행자는 작업자의 데몬에 작업을 일괄 처리 합니다. 이 매개 변수는 단일 일괄 처리에서 할당할 최대 태스크 수를 설정 합니다. 더 큰 일괄 처리 크기를 선택 하면 더 빠르게 작업을 할당할 수 있습니다. 그러나 작업자 수가 많은 경우 모든 작업 자가 작업을 가져오는 데 시간이 오래 걸릴 수 있습니다.  이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용 됩니다.

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus \_ 노드당 실행 중인 \_ 최대 \_ 태스크 \_ (정수)

작업 추적기 프로세스는 적절 하 게 할당 된 작업을 예약 하 고 실행 합니다. 이 구성 값은 지정 된 시간에 한 노드에서 동시에 실행할 최대 태스크 수를 설정 하 고 기본값은 8입니다.

한도를 사용 하면 디스크를 동시에 적중 하는 \' 작업이 많지 않으며 디스크 i/o 경합을 방지할 수 있습니다. 쿼리가 메모리 또는 Ssd에서 제공 되는 경우에는 \_ \_ \_ \_ 많은 걱정 없이 노드당 최대 실행 태스크를 늘릴 수 있습니다.

#### <a name="cituspartition_buffer_size-integer"></a>citus \_ 버퍼 \_ 크기 (정수)

파티션 작업에 사용할 버퍼 크기를 설정 하 고 기본값은 8mb로 설정 합니다.
Citus (hyperscale)를 사용 하면 두 개의 대규모 테이블이 조인 될 때 테이블 데이터를 여러 파일에 다시 분할 수 있습니다. 이 파티션 버퍼가 채워지면 다시 분할 데이터가 디스크의 파일로 플러시됩니다.  이 구성 항목은 런타임에 설정할 수 있으며 작업자에 게 적용 됩니다.

### <a name="explain-output"></a>설명 출력

#### <a name="citusexplain_all_tasks-boolean"></a>citus \_ 모든 \_ 작업을 설명 합니다 (부울).

기본적으로 Citus (Hyperscale)는 분산 쿼리에 [대해 설명](http://www.postgresql.org/docs/current/static/sql-explain.html) 하는 작업을 실행할 때 단일 작업의 출력을 표시 합니다. 대부분의 경우 설명 출력은 작업 간에 유사 합니다. 경우에 따라 일부 작업은 다르게 계획 되거나 실행 시간이 훨씬 더 높을 수 있습니다. 이러한 경우에는이 매개 변수를 사용 하도록 설정 하는 것이 유용할 수 있으며, 그 후에는 설명 출력에 모든 태스크가 포함 됩니다. 모든 작업에 대해 설명 하는 데 시간이 더 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 서버 매개 변수 외의 또 다른 형태의 구성은 Citus (Hyperscale) 서버 그룹의 리소스 [구성 옵션](concepts-hyperscale-configuration-options.md) 입니다.
* 기본 PostgreSQL 데이터 베이스에도 [구성 매개 변수가](http://www.postgresql.org/docs/current/static/runtime-config.html)있습니다.
