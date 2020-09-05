---
title: 변경 데이터 캡처에 대 한 Debezium를 사용 하 여 Azure Event Hubs (미리 보기)에 Apache Kafka 연결 통합
description: 이 문서에서는 Kafka에 대한 Azure Event Hubs에서 Apache Spark를 사용하는 방법을 설명합니다.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: a11ec882a50d051a34758562ac84dcef5b799f5f
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136891"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>변경 데이터 캡처에 대 한 Debezium를 사용 하 여 Azure Event Hubs (미리 보기)에 Apache Kafka 연결 지원 통합

**CDC (변경 데이터 캡처)** 는 만들기, 업데이트 및 삭제 작업에 대 한 응답으로 데이터베이스 테이블의 행 수준 변경 내용을 추적 하는 데 사용 되는 기술입니다. [Debezium](https://debezium.io/) 는 다양 한 데이터베이스 (예: [PostgreSQL의 논리적 디코딩](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html))에서 사용할 수 있는 변경 데이터 캡처 기능을 기반으로 구축 되는 분산 플랫폼입니다. 데이터베이스 테이블에서 행 수준 변경 내용을 탭 하 고이를 이벤트 스트림으로 변환한 후 [Apache Kafka](https://kafka.apache.org/)으로 전송 하는 [Kafka Connect 커넥터](https://debezium.io/documentation/reference/1.2/connectors/index.html) 집합을 제공 합니다.

이 자습서에서는 azure [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about?WT.mc_id=devto-blog-abhishgu) (Kafka 용), [Azure DB (PostgreSQL](../postgresql/overview.md) 및 Debezium)를 사용 하 여 azure에서 변경 데이터 캡처 기반 시스템을 설정 하는 방법을 안내 합니다. [Debezium PostgreSQL 커넥터](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) 를 사용 하 여 PostgreSQL의 데이터베이스 수정 내용을 Event Hubs Azure의 Kafka 토픽으로 스트리밍합니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * Azure Database for PostgreSQL 설정 및 구성
> * Debezium PostgreSQL connector를 사용 하 여 Kafka Connect 구성 및 실행
> * 변경 데이터 캡처 테스트
> * 필드 커넥터를 사용 하 여 변경 데이터 이벤트 사용 `FileStreamSink`

## <a name="pre-requisites"></a>필수 구성 요소
이 연습을 완료 하려면 다음이 필요 합니다.

- 동작합니다. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- Linux/MacOS
- Kafka 릴리스(버전 1.1.1, Scala 버전 2.11), [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)에서 제공
- [Apache Kafka용 Event Hubs](./event-hubs-for-kafka-ecosystem-overview.md) 지침 문서를 참조하세요.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. 네임 스페이스 및 이벤트 허브를 만드는 방법에 대 한 지침은 [이벤트 허브 만들기](event-hubs-create.md) 를 참조 하세요. 나중에 사용할 수 있도록 Event Hubs 연결 문자열 및 FQDN(정규화된 도메인 이름)을 가져옵니다. 자세한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 설정 및 구성
[Azure Database for PostgreSQL](../postgresql/overview.md) 는 커뮤니티 버전의 오픈 소스 PostgreSQL 데이터베이스 엔진을 기반으로 하는 관계형 데이터베이스 서비스 이며, 단일 서버 및 hyperscale (Citus)의 두 가지 배포 옵션으로 사용할 수 있습니다. [다음 지침에 따라](../postgresql/quickstart-create-server-database-portal.md) Azure Portal를 사용 하 여 Azure Database for PostgreSQL 서버를 만듭니다. 

## <a name="setup-and-run-kafka-connect"></a>Kafka Connect 설정 및 실행
이 섹션에서는 다음 항목을 다룹니다.

- Debezium 커넥터 설치
- Event Hubs에 대 한 Kafka Connect 구성
- Debezium 커넥터를 사용 하 여 Kafka Connect 클러스터 시작

### <a name="download-and-setup-debezium-connector"></a>Debezium 커넥터 다운로드 및 설정
[Debezium 설명서](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 의 최신 지침에 따라 커넥터를 다운로드 하 고 설정 하세요.

- 커넥터의 플러그 인 아카이브를 다운로드 합니다. 예를 들어 커넥터의 버전을 다운로드 하려면 `1.2.0` 다음 링크를 사용 합니다.https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- JAR 파일을 추출 하 여 [Kafka Connect 플러그인. 경로](https://kafka.apache.org/documentation/#connectconfigs)에 복사 합니다.


### <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs에 대해 Kafka Connect 구성
Kafka에서 Event Hubs로의 Kafka Connect 처리량을 리디렉션할 때 최소한의 재구성이 필요합니다.  다음 `connect-distributed.properties` 샘플은 Event Hubs에서 Kafka 엔드포인트를 인증하고 통신하도록 Connect를 구성하는 방법을 보여줍니다.

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

### <a name="run-kafka-connect"></a>Kafka Connect 실행
이 단계에서 Kafka Connect 작업자는 로컬에서 분산 모드로 시작되고, Event Hubs를 사용하여 클러스터 상태를 유지합니다.

1. 위의 `connect-distributed.properties` 파일을 로컬에 저장합니다.  중괄호 안에 있는 모든 값을 바꿔야 합니다.
2. 사용하는 머신에서 Kafka 릴리스가 있는 위치로 이동합니다.
3. 를 실행 하 `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` 고 클러스터를 시작할 때까지 기다립니다.

> [!NOTE]
> Kafka Connect는 Kafka AdminClient API를 사용하여 압축을 비롯한 권장 구성이 포함된 토픽을 자동으로 만듭니다. Azure Portal에서 네임스페이스를 신속하게 살펴보면 Connect 작업자의 내부 토픽이 자동으로 만들어진 것을 알 수 있습니다.
>
> Kafka Connect 내부 토픽은 **압축을 사용해야 합니다**.  Event Hubs 팀은 내부 연결 토픽이 잘못 구성된 경우 잘못된 구성을 수정하는 일을 담당하지 않습니다.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Debezium PostgreSQL source 커넥터 구성 및 시작

`pg-source-connector.json`PostgreSQL 원본 커넥터용 구성 파일 () 만들기-Azure PostgreSQL 인스턴스 별로 값을 바꿉니다.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name`특성은 모니터링 되는 특정 PostgreSQL 데이터베이스 서버/클러스터에 대 한 네임 스페이스를 식별 하 고 제공 하는 논리적 이름입니다. 자세한 정보는 [Debezium 설명서](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name) 를 참조 하세요.

커넥터의 인스턴스를 만들려면 Kafka Connect REST API 끝점을 사용 합니다.

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

커넥터의 상태를 확인 하려면:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>변경 데이터 캡처 테스트
작동 중인 변경 데이터 캡처를 보려면 Azure PostgreSQL 데이터베이스에서 레코드를 생성/업데이트/삭제 해야 합니다.

Azure PostgreSQL 데이터베이스에 연결 하 여 시작 합니다 (아래 예제에서는 [psql](https://www.postgresql.org/docs/12/app-psql.html)을 사용).

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**테이블 만들기 및 레코드 삽입**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

이제 커넥터는 작업을 수행 하 고 다음 na (e)가 있는 Event Hubs 항목으로 변경 데이터 이벤트를 전송 해야 합니다 .이 항목은 `my-server.public.todos` `my-server` 에 대 한 값으로 가정 하 고 `database.server.name` `public.todos` 변경 내용이 추적 중인 테이블 ( `table.whitelist` 구성 기준)입니다.

**Event Hubs 항목 확인**

항목의 내용을 introspect 모든 것이 예상 대로 작동 하는지 확인 합니다. 아래 예제에서는 [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) 를 사용 하지만 [여기에 나열 된 옵션 중 하나를 사용 하 여 소비자를 만들](apache-kafka-developer-guide.md) 수도 있습니다.

`kafkacat.conf`다음 내용을 사용 하 여 라는 파일을 만듭니다.

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> `metadata.broker.list` `sasl.password` Event Hubs 정보를 기준으로에서 및 특성을 업데이트 `kafkacat.conf` 합니다. 

다른 터미널에서 소비자를 시작 합니다.

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

방금 테이블에 추가한 행에 대 한 응답으로 PostgreSQL에서 생성 된 변경 데이터 이벤트를 나타내는 JSON 페이로드가 표시 되어야 합니다 `todos` . 다음은 페이로드의 코드 조각입니다.


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

이벤트는와 함께로 구성 됩니다 `payload` (간단 하 게 나타내기 `schema` 위해 생략 됨). `payload`섹션에서는 만들기 작업 ( `"op": "c"` )이 표시 되는 방식을 확인 합니다 `"before": null` . 즉, 새로 표시 된 `INSERT` 행 이며, `after` 행의 열에 대 한 값을 제공 하 고, `source` 이 이벤트가 선택 된 위치에서 PostgreSQL 인스턴스 메타 데이터를 제공 합니다.

업데이트 또는 삭제 작업 에서도 동일한 작업을 수행 하 고 변경 데이터 이벤트를 introspect 수 있습니다. 예를 들어 `configure and install connector` (가 인 것으로 가정)의 작업 상태를 업데이트 하려면 `id` `3` 다음을 수행 합니다.

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>필드 FileStreamSink 커넥터 설치
모든 `todos` 테이블 변경 내용이 Event Hubs 토픽에서 캡처 되었으므로 이러한 이벤트를 사용 하기 위해 FileStreamSink 커넥터 (기본적으로 Kafka Connect에서 사용 가능)를 사용 합니다.

`file-sink-connector.json`커넥터에 대 한 구성 파일 () 만들기- `file` 파일 시스템에 따라 특성을 바꿉니다.

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

커넥터를 만들고 해당 상태를 확인 하려면 다음을 수행 합니다.

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

데이터베이스 레코드를 삽입/업데이트/삭제 하 고 구성 된 출력 싱크 파일의 레코드를 모니터링 합니다.

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>정리
Kafka Connect는 Connect 클러스터가 중단된 후에도 유지되는 구성, 오프셋 및 상태를 저장하는 이벤트 허브 토픽을 만듭니다. 이 지속성을 원하지 않는다면 이러한 토픽을 삭제하는 것이 좋습니다. 이 연습을 진행 하는 동안 생성 된 이벤트 허브를 삭제할 수도 있습니다 `my-server.public.todos` .

## <a name="next-steps"></a>다음 단계

Kafka에 대 한 Event Hubs에 대해 자세히 알아보려면 다음 문서를 참조 하세요.  

- [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md)
- [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
- [이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)
