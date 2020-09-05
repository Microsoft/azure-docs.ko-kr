---
title: '빠른 시작: 서버 그룹 만들기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL'
description: Azure Database for PostgreSQL 하이퍼스케일(Citus)에서 분산된 테이블을 만들고 쿼리하는 빠른 시작입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/17/2020
ms.openlocfilehash: 977082b7f9055b90ee5c93913154934741d93772
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547701"
---
# <a name="quickstart-create-a-hyperscale-citus-server-group-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 하이퍼스케일(Citus) 서버 그룹 만들기

PostgreSQL용 Azure Database는 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL - 하이퍼스케일(Citus) 서버 그룹을 만드는 방법을 보여줍니다. 분산된 데이터를 살펴보겠습니다. 노드 간에 테이블을 분할하고, 샘플 데이터를 수집하고, 여러 노드에서 실행되는 쿼리를 실행할 것입니다.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>테이블 만들기 및 테이블 배포

psql을 사용하여 하이퍼스케일 코디네이터 노드에 연결되면 기본 작업을 완료할 수 있습니다.

하이퍼스케일 서버 내에는 다음과 같은 세 가지 유형의 테이블이 있습니다.

- 분산 또는 분할된 테이블(성능 및 병렬 처리를 향상하기 위해 분산)
- 참조 테이블(여러 복사본 유지)
- 로컬 테이블(종종 내부 관리 테이블에 사용)

이 빠른 시작에서는 분산 테이블을 집중적으로 살펴보고 익히도록 하겠습니다.

우리가 작업하려는 데이터 모델은 간단합니다. GitHub의 사용자 및 이벤트 데이터입니다. 이벤트로는 분기 만들기, 조직과 관련된 git 커밋 등이 있습니다.

psql을 통해 연결했으면 테이블을 만들어 보겠습니다. psql 콘솔에서 다음을 실행합니다.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

`github_events`의 `payload` 필드는 JSONB 데이터 형식을 갖습니다. JSONB는 Postgres의 이진 형태인 JSON 데이터 형식입니다. 이 데이터 형식을 사용하면 유연한 스키마를 단일 열에 쉽게 저장할 수 있습니다.

Postgres는 모든 키와 키 내부의 값을 인덱싱하는 이 유형의 `GIN` 인덱스를 만들 수 있습니다. 인덱스가 있으면 다양한 조건을 사용하여 쉽고 빠르게 페이로드를 쿼리할 수 있습니다. 데이터를 로드하기 전에 몇 가지 인덱스를 만들어 보겠습니다. psql에서 다음을 수행합니다.

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

다음으로, 코디네이터 노드의 이 Postgres 테이블을 가져오고, 이 테이블을 여러 작업 노드에 분할하라고 하이퍼스케일에 지시하겠습니다. 이렇게 하기 위해 각 테이블에 대해 테이블을 분할할 키를 지정하는 쿼리를 실행하겠습니다. 현재 예제에서는 이벤트 및 사용자 테이블을 `user_id`에 분할합니다.

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

[!INCLUDE [azure-postgresql-hyperscale-dist-alert](../../includes/azure-postgresql-hyperscale-dist-alert.md)]

데이터를 로드할 준비가 되었습니다. psql에서 계속해서 파일을 다운로드합니다.

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

다음으로, 파일의 데이터를 분산 테이블에 로드합니다.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>쿼리 실행

이제부터 재미있는 부분이 시작됩니다. 몇 가지 쿼리를 실제로 실행할 것입니다. 간단한 `count (*)`부터 시작하여 우리가 얼마나 많은 데이터를 로드했는지 알아봅시다.

```sql
SELECT count(*) from github_events;
```

문제 없이 작동합니다. 집계에 관련된 내용은 나중에 살펴보기로 하고, 지금은 몇 가지 다른 쿼리를 살펴보겠습니다. JSONB `payload` 열 내에는 좋은 데이터가 있지만, 이벤트 유형에 따라 데이터가 달라집니다. `PushEvent` 이벤트에는 푸시에 대한 고유의 커밋 수를 포함하는 크기가 포함되어 있습니다. 다음과 같이 이 크기를 사용하여 시간당 총 커밋 수를 알아낼 수 있습니다.

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

지금까지 살펴본 쿼리는 오직 github\_events만 사용했지만, 이 정보를 github\_users와 결합할 수 있습니다. 사용자와 이벤트를 동일한 식별자(`user_id`)에 분할했기 때문에 사용자 ID가 일치하는 두 테이블의 행은 동일한 데이터베이스 노드에 [공동 배치](concepts-hyperscale-colocation.md)되며, 따라서 쉽게 조인할 수 있습니다.

`user_id`에 조인하는 경우 하이퍼스케일은 조인 실행을 분할된 데이터베이스로 보내 여러 작업자 노드에서 병렬로 실행할 수 있습니다. 예를 들어 리포지토리를 가장 많이 만든 사용자를 찾아보겠습니다.

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 Azure 리소스를 만들었습니다. 이러한 리소스가 더 이상 필요 없으면 서버 그룹을 삭제합니다. 서버 그룹의 **개요** 페이지에서 **삭제** 단추를 누릅니다. 팝업 페이지에 메시지가 표시되면 서버 그룹의 이름을 확인하고 최종 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. 그 후 psql을 사용하여 이 서버 그룹에 연결하고, 스키마를 만들고, 데이터를 분산했습니다.

다음으로, 자습서에 따라 확장성 있는 다중 테넌트 애플리케이션을 빌드합니다.
> [!div class="nextstepaction"]
> [다중 테넌트 데이터베이스 설계](https://aka.ms/hyperscale-tutorial-multi-tenant)
