---
title: Azure Database for PostgreSQL의 쿼리 저장소
description: 이 문서에서는 Azure Database for PostgreSQL의 쿼리 저장소 기능을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2019
ms.openlocfilehash: c904b6e6cd7a4dc0f9d5a442e20738e43595b369
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564007"
---
# <a name="monitor-performance-with-the-query-store"></a>쿼리 저장소를 사용하여 성능 모니터링

**적용 대상:** Azure Database for PostgreSQL 9.6 및 10

Azure Database for PostgreSQL의 쿼리 저장소 기능은 시간 경과에 따라 쿼리 성능을 추적하는 방법을 제공합니다. 쿼리 저장소는 가장 오래 실행되고 리소스를 가장 많이 사용하는 쿼리를 신속하게 찾도록 지원하여 성능 문제 해결을 단순화합니다. 쿼리 저장소는 쿼리 및 런타임 통계의 기록을 자동으로 캡처하고 검토를 위해 보존합니다. 데이터베이스 사용량 패턴을 볼 수 있도록 데이터를 기간별로 구분합니다. 모든 사용자, 데이터베이스 및 쿼리에 대한 데이터는 Azure Database for PostgreSQL 인스턴스의 **azure_sys**라는 데이터베이스에 저장됩니다.

> [!IMPORTANT]
> **azure_sys** 데이터베이스 또는 해당 스키마를 수정하지 마세요. 이렇게 하면 쿼리 저장소 및 관련 성능 기능이 제대로 작동하지 않습니다.

## <a name="enabling-query-store"></a>쿼리 저장소 사용
쿼리 저장소는 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 저장소는 지정된 서버의 모든 데이터베이스에 대해 전역으로 사용하거나 사용하지 않도록 설정되며 데이터베이스별로 켜거나 끌 수 없습니다.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure Portal을 통해 쿼리 저장소 사용
1. Azure Portal에 로그인하고 Azure Database for PostgreSQL 서버를 선택합니다.
2. 메뉴의 **설정** 섹션에서 **서버 매개 변수**를 선택합니다.
3. `pg_qs.query_capture_mode` 매개 변수를 검색합니다.
4. 값을 설정 합니다 `TOP` 하 고 **저장**합니다.

쿼리 저장소에서 대기 통계 수 있도록 합니다. 
1. `pgms_wait_sampling.query_capture_mode` 매개 변수를 검색합니다.
1. 값을 설정 합니다 `ALL` 하 고 **저장**합니다.


또는 Azure CLI를 사용 하 여 이러한 매개 변수를 설정할 수 있습니다.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

데이터의 첫 번째 배치가 azure_sys 데이터베이스에서 지속되는 데 최대 20분이 걸립니다.

## <a name="information-in-query-store"></a>쿼리 저장소의 정보
쿼리 저장소에는 다음과 같은 두 개의 저장소가 있습니다.
- 쿼리 실행 통계 정보를 지속하기 위한 런타임 통계 저장소.
- 대기 통계 정보를 지속하기 위한 대기 통계 저장소.

쿼리 저장소 사용에 대한 일반적인 시나리오는 다음과 같습니다.
- 지정된 기간에 쿼리가 실행된 횟수를 확인하는 경우
- 전체 기간에 대한 쿼리의 평균 실행 시간을 비교하여 큰 델타를 확인하는 경우
- 지난 X시간 동안 가장 오래 실행되는 쿼리를 식별하는 경우
- 리소스에서 대기 중인 최상위 N개 쿼리를 식별하는 경우
- 특정 쿼리의 대기 특성을 이해하는 경우

공간 사용량을 최소화하기 위해 런타임 통계 저장소의 런타임 실행 통계가 고정된 구성 가능 기간을 통해 집계됩니다. 이러한 저장소의 정보는 쿼리 저장소 보기를 쿼리하여 표시됩니다.

다음 쿼리는 쿼리 저장소의 쿼리에 대한 정보를 반환합니다.
```sql
SELECT * FROM query_store.qs_view; 
``` 

또는 대기 통계에 대한 이 쿼리는 다음을 수행합니다.
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>대기 쿼리 찾기
대기 이벤트 유형은 유사성을 기준으로 서로 다른 대기 이벤트를 버킷으로 결합합니다. 쿼리 저장소는 대기 이벤트 유형, 특정 대기 이벤트 이름 및 해당하는 쿼리를 제공합니다. 이 대기 정보를 쿼리 런타임 통계와 상호 연관시킬 수 있다는 것은 쿼리 성능 특성에 영향을 미치는 내용을 더 잘 이해할 수 있음을 의미합니다.

다음은 쿼리 저장소의 대기 통계를 사용하여 워크로드에 대한 더 많은 통찰력을 얻을 수 있는 방법의 몇 가지 예입니다.

| **관찰** | **작업** |
|---|---|
|최고 잠금 대기 | 영향을 받는 쿼리에 대한 쿼리 텍스트를 확인하고 대상 엔터티를 식별합니다. 쿼리 저장소에서 자주 실행되거나 오래 실행되는 동일한 엔터티를 수정하는 다른 쿼리를 확인합니다. 이러한 쿼리를 식별한 후 동시성 향상을 위해 애플리케이션 논리를 변경해 보거나 덜 제한적인 격리 수준을 사용합니다.|
| 높은 버퍼 IO 대기 | 쿼리 저장소에서 물리적 읽기 횟수가 많은 쿼리를 찾습니다. 해당 쿼리가 IO 대기가 많은 쿼리와 일치하는 경우 검사 대신 검색을 수행하기 위해 기본 엔터티에 인덱스를 도입하는 것이 좋습니다. 이렇게 하면 쿼리의 IO 오버헤드가 최소화됩니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 쿼리를 최적화하는 이 서버에 대한 인덱스 권장 사항이 있는지 확인합니다.|
| 높은 메모리 대기 | 쿼리 저장소에서 메모리 사용량이 많은 상위 쿼리를 찾습니다. 이러한 쿼리는 영향을 받는 쿼리의 추가 진행을 지연시킬 수 있습니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 이러한 쿼리를 최적화하는 인덱스 권장 사항이 있는지 확인합니다.|

## <a name="configuration-options"></a>구성 옵션
쿼리 저장소를 사용하도록 설정하면 데이터가 15분 집계 창에 저장되고 각 창에는 최대 500개의 고유 쿼리가 포함됩니다. 

다음 옵션은 쿼리 저장소 매개 변수를 구성하는 데 사용할 수 있습니다.

| **매개 변수** | **설명** | **기본값** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | 추적되는 문을 설정합니다. | 없음 | none, top, all |
| pg_qs.max_query_text_length | 저장할 수 있는 최대 쿼리 길이를 설정합니다. 더 긴 쿼리는 잘립니다. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | 보존 기간을 설정합니다. | 7 | 1 - 30 |
| pg_qs.track_utility | 유틸리티 명령을 추적할지 여부를 설정합니다. | on | on, off |

다음 옵션은 특히 대기 통계에 적용됩니다.

| **매개 변수** | **설명** | **기본값** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | 대기 통계가 추적되는 문을 설정합니다. | 없음 | none, all|
| Pgms_wait_sampling.history_period | 대기 이벤트가 샘플링되는 빈도(밀리초)를 설정합니다. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode**는 **pgms_wait_sampling.query_capture_mode**를 대체합니다. pg_qs.query_capture_mode가 NONE인 경우 pgms_wait_sampling.query_capture_mode 설정은 영향을 미치지 않습니다.


[Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용하여 매개 변수에 대한 다른 값을 가져오거나 설정합니다.

## <a name="views-and-functions"></a>보기 및 함수
다음 보기 및 함수를 사용하여 쿼리 저장소를 보고 관리합니다. PostgreSQL 공용 역할의 사용자는 이러한 보기를 사용하여 쿼리 저장소의 데이터를 볼 수 있습니다. 이러한 보기는 **azure_sys** 데이터베이스에서만 사용 가능합니다.

쿼리는 리터럴 및 상수를 제거한 후 구조를 확인하여 정규화됩니다. 리터럴 값을 제외하고 두 쿼리가 동일한 경우에는 동일한 해시를 포함합니다.

### <a name="querystoreqsview"></a>query_store.qs_view
이 보기는 쿼리 저장소의 모든 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID 및 쿼리 ID에 대한 하나의 행이 있습니다. 

|**Name**   |**형식** | **참조**  | **설명**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | runtime_stats_entries 테이블의 ID|
|user_id    |oid    |pg_authid.oid  |문을 실행한 사용자의 OID|
|db_id  |oid    |pg_database.oid    |문이 실행된 데이터베이스의 OID|
|query_id   |bigint  || 문의 구문 분석 트리에서 계산된 내부 해시 코드|
|query_sql_text |Varchar(10000)  || 대표 문의 텍스트. 동일한 구조의 서로 다른 쿼리가 함께 클러스터되고, 이 텍스트는 클러스터에 있는 첫 번째 쿼리의 텍스트입니다.|
|plan_id    |bigint |   |이 쿼리에 해당하는 계획의 ID로, 아직 사용할 수 없음|
|start_time |timestamp  ||  쿼리는 시간 버킷별로 집계되며 버킷의 시간 범위는 기본적으로 15분입니다. 이는 이 항목의 시간 버킷에 해당하는 시작 시간.|
|end_time   |timestamp  ||  이 항목의 시간 버킷에 해당하는 종료 시간.|
|calls  |bigint  || 쿼리 실행 횟수|
|total_time |double precision   ||  총 쿼리 실행 시간(밀리초)|
|min_time   |double precision   ||  최소 쿼리 실행 시간(밀리초)|
|max_time   |double precision   ||  최대 쿼리 실행 시간(밀리초)|
|mean_time  |double precision   ||  평균 쿼리 실행 시간(밀리초)|
|stddev_time|   double precision    ||  쿼리 실행 시간(밀리초)의 표준 편차 |
|rows   |bigint ||  문이 영향을 미치거나 검색하는 총 행 수|
|shared_blks_hit|   bigint  ||  문을 통해 공유되는 총 블록 캐시 적중 수|
|shared_blks_read|  bigint  ||  문이 읽은 총 공유 블록 수|
|shared_blks_dirtied|   bigint   || 문에 의해 변경된 총 공유 블록 수 |
|shared_blks_written|   bigint  ||  문이 쓴 총 공유 블록 수|
|local_blks_hit|    bigint ||   문에 의한 총 로컬 블록 캐시 적중 수|
|local_blks_read|   bigint   || 문이 읽은 총 로컬 블록 수|
|local_blks_dirtied|    bigint  ||  문에 의해 변경된 총 로컬 블록 수|
|local_blks_written|    bigint  ||  문이 쓴 총 로컬 블록 수|
|temp_blks_read |bigint  || 문이 읽은 총 임시 블록 수|
|temp_blks_written| bigint   || 문이 쓴 총 임시 블록 수|
|blk_read_time  |double precision    || 문이 블록을 읽는 데 사용한 총 시간(밀리초)(track_io_timing이 사용하도록 설정된 경우, 이외의 경우에는 0)|
|blk_write_time |double precision    || 문이 블록을 쓰는 데 사용한 총 시간(밀리초)(track_io_timing이 사용하도록 설정된 경우, 이외의 경우에는 0)|
    
### <a name="querystorequerytextsview"></a>query_store.query_texts_view
이 보기는 쿼리 저장소의 쿼리 텍스트 데이터를 반환합니다. 각 고유 query_text에 대한 하나의 행이 있습니다.

|**Name**|  **형식**|   **설명**|
|---|---|---|
|query_text_id  |bigint     |query_texts 테이블의 ID|
|query_sql_text |Varchar(10000)     |대표 문의 텍스트. 동일한 구조의 서로 다른 쿼리가 함께 클러스터되고, 이 텍스트는 클러스터에 있는 첫 번째 쿼리의 텍스트입니다.|

### <a name="querystorepgmswaitsamplingview"></a>query_store.pgms_wait_sampling_view
이 보기는 쿼리 저장소의 대기 이벤트 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID, 쿼리 ID 및 이벤트에 대한 하나의 행이 있습니다.

|**Name**|  **형식**|   **참조**| **설명**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |문을 실행한 사용자의 OID|
|db_id  |oid    |pg_database.oid    |문이 실행된 데이터베이스의 OID|
|query_id   |bigint     ||문의 구문 분석 트리에서 계산된 내부 해시 코드|
|event_type |text       ||백 엔드가 대기 중인 이벤트 유형|
|event  |text       ||백 엔드가 현재 대기 중인 경우 대기 이벤트 이름|
|calls  |정수         ||캡처된 동일한 이벤트 수|


### <a name="functions"></a>Functions
Query_store.qs_reset() returns void

`qs_reset`은 쿼리 저장소가 지금까지 수집한 모든 통계를 무시합니다.  이 함수는 서버 관리자 역할만 실행할 수 있습니다.

Query_store.staging_data_reset() returns void

`staging_data_reset`은 쿼리 저장소가 메모리에서 수집한 모든 통계(즉, 아직 데이터베이스로 플러시되지 않은 메모리의 데이터)를 무시합니다.  이 함수는 서버 관리자 역할만 실행할 수 있습니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제
- PostgreSQL 서버에서 default_transaction_read_only 매개 변수가 설정되어 있으면 Query Store가 데이터를 캡처할 수 없습니다.
- 6000바이트 이상의 긴 유니코드 쿼리가 발견되면 Query Store 기능이 중단될 수 있습니다.


## <a name="next-steps"></a>다음 단계
- [쿼리 저장소가 특히 유용할 수 있는 시나리오](concepts-query-store-scenarios.md)에 대해 자세히 알아보세요.
- [쿼리 저장소 사용 모범 사례](concepts-query-store-best-practices.md)에 대해 자세히 알아보세요.
