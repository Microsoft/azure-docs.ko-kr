---
title: Blitzz를 사용 하 여 Cassandra에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션
description: Blitzz를 사용 하 여 Apache Cassandra 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 746232a85e326f08d44cf2dbe0a4ae718c7cc312
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984369"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Blitzz를 사용 하 여 Cassandra에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션

Azure Cosmos DB Cassandra API는 다음과 같은 다양 한 이유로 Apache Cassandra에서 실행 되는 엔터프라이즈 워크 로드에 적합 한 선택이 되었습니다. 

* **관리 및 모니터링의 오버 헤드 없음:** OS, JVM, yaml 파일 및 상호 작용에서 많은 설정을 관리 하 고 모니터링 하는 오버 헤드를 제거 합니다.

* **상당한 비용 절감:** Azure Cosmos DB를 사용 하 여 비용을 절감할 수 있습니다. 여기에는 VM의 비용, 대역폭 및 모든 해당 라이선스를 포함 합니다. 또한 데이터 센터, 서버, SSD 저장소, 네트워킹 및 전기 비용을 관리할 필요가 없습니다. 

* **기존 코드 및 도구를 사용 하는 기능:** Azure Cosmos DB는 기존 Cassandra SDK 및 도구와 유선 프로토콜 수준의 호환성을 제공합니다. 이 호환성을 통해 간단한 변경 사항이 있는 Azure Cosmos DB Cassandra API와 함께 기존 코드베이스를 사용할 수 있습니다.

한 플랫폼에서 다른 플랫폼으로 데이터베이스 워크 로드를 마이그레이션하는 다양 한 방법이 있습니다. [Blitzz](https://www.blitzz.io) 는 다양 한 데이터베이스에서 Azure Cosmos DB 가동 중지 시간을 0으로 마이그레이션하는 안전 하 고 안정적인 방법을 제공 하는 도구입니다. 이 문서에서는 Blitzz를 사용 하 여 Apache Cassandra 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 데 필요한 단계를 설명 합니다.

## <a name="benefits-using-blitzz-for-migration"></a>마이그레이션에 Blitzz를 사용 하는 이점

Blitzz의 마이그레이션 솔루션은 복잡 한 운영 워크 로드를 마이그레이션하는 단계별 접근 방식을 따릅니다. Blitzz의 가동 중지 시간이 0 인 마이그레이션 계획의 몇 가지 주요 측면은 다음과 같습니다.

* Apache Cassandra 데이터베이스에서 Azure Cosmos DB로 비즈니스 논리 (테이블, 인덱스, 뷰)를 자동으로 마이그레이션하는 것을 제공 합니다. 수동으로 스키마를 만들 필요가 없습니다.

* Blitzz는 대용량 및 병렬 데이터베이스 복제를 제공 합니다. 이를 통해 CDC (변경 데이터 캡처) 라는 기술을 사용 하 여 마이그레이션하는 동안 원본 및 대상 플랫폼을 동기화 할 수 있습니다. Blitzz는 CDC를 사용 하 여 원본 데이터베이스 (Apache Cassandra)에서 변경 내용의 스트림을 지속적으로 가져와 대상 데이터베이스 (Azure Cosmos DB)에 적용 합니다.

* 내결함성이 있으며 시스템에서 하드웨어 또는 소프트웨어 오류가 발생 하는 경우에도 정확 하 게 데이터를 배달 하는 것을 보장 합니다.

* SSL, 암호화 등의 다양 한 보안 방법을 사용 하 여 전송 하는 동안 데이터를 보호 합니다.

## <a name="steps-to-migrate-data"></a>데이터를 마이그레이션하는 단계

이 섹션에서는 Blitzz를 설정 하 고 Apache Cassandra 데이터베이스에서 Azure Cosmos DB로 데이터를 마이그레이션하는 데 필요한 단계에 대해 설명 합니다.

1. Blitzz replicant를 설치 하려는 컴퓨터에서 보안 인증서를 추가 합니다. 이 인증서는 Blitzz replicant에서 지정 된 Azure Cosmos DB 계정으로 SSL 연결을 설정 하는 데 필요 합니다. 다음 단계를 사용 하 여 인증서를 추가할 수 있습니다.

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. [Blitzz 웹 사이트](https://www.blitzz.io)에서 데모를 요청 하 여 Blitzz 설치 및 이진 파일을 가져올 수 있습니다. 또는 팀에 [전자 메일](mailto:success@blitzz.io) 을 보낼 수도 있습니다.

   ![Blitzz replicant tool 다운로드](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replicant 파일](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. CLI 터미널에서 원본 데이터베이스 구성을 설정 합니다. 명령을 사용 하 여 **`vi conf/conn/cassandra.yml`** 구성 파일을 열고 Cassandra 노드의 IP 주소, 포트 번호, 사용자 이름, 암호 및 기타 필요한 세부 정보를 쉼표로 구분한 목록을 추가 합니다. 다음은 구성 파일의 내용에 대한 예입니다.

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![Cassandra 연결 편집기 열기](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Cassandra 연결 구성](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   구성 세부 정보를 입력 한 후 파일을 저장 한 후 닫습니다.

1. 필요에 따라 원본 데이터베이스 필터 파일을 설정할 수 있습니다. 필터 파일은 마이그레이션할 스키마 또는 테이블을 지정 합니다. 명령을 사용 하 여 **`vi filter/cassandra_filter.yml`** 구성 파일을 열고 다음 구성 세부 정보를 입력 합니다.

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   데이터베이스 필터 정보를 입력 한 후 파일을 저장 한 후 닫습니다.

1. 다음에는 대상 데이터베이스 구성을 설정 합니다. 구성을 정의 하기 전에 [Azure Cosmos DB Cassandra API 계정을 만든](create-cassandra-dotnet.md#create-a-database-account) 다음 Keyspace 및 마이그레이션된 데이터를 저장할 테이블을 만듭니다. Apache Cassandra에서 Azure Cosmos DB Cassandra API으로 마이그레이션하기 때문에 Apache Cassandra에서 사용한 것과 동일한 파티션 키를 사용할 수 있습니다.

1. 데이터를 마이그레이션하기 전에 컨테이너 처리량을 응용 프로그램을 신속 하 게 마이그레이션하는 데 필요한 양만큼 늘립니다. 예를 들어 처리량을 10만 RUs로 늘릴 수 있습니다. 마이그레이션을 시작 하기 전에 처리량을 확장 하면 데이터를 짧은 시간 내에 마이그레이션하는 데 도움이 됩니다.

   ![Azure Cosmos 컨테이너 전체 확장](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   마이그레이션이 완료 된 후 처리량을 줄입니다. 각 작업에 대해 저장 된 데이터와 RUs의 양에 따라 데이터 마이그레이션 후 필요한 처리량을 예상할 수 있습니다. 필요한 RUs를 추정 하는 방법에 대한 자세한 내용은 [컨테이너 및 데이터베이스에 대한 처리량 프로 비전](set-throughput.md) 및 [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md) 을 참조 하세요.

1. **연결 문자열** 창에서 Azure Cosmos 계정의 연결 **지점, 포트, 사용자 이름**및 **기본 암호** 를 가져옵니다. 구성 파일에서 이러한 값을 사용 합니다.

1. CLI 터미널에서 대상 데이터베이스 구성을 설정 합니다. 명령을 사용 하 여 **`vi conf/conn/cosmosdb.yml`** 구성 파일을 열고 호스트 URI, 포트 번호, 사용자 이름, 암호 및 기타 필수 매개 변수의 쉼표로 구분 된 목록을 추가 합니다. 다음 예에서는 구성 파일의 내용을 보여 줍니다.

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. 그런 다음 Blitzz를 사용 하 여 데이터를 마이그레이션합니다. Blizz replicant를 **전체** 또는 **스냅숏** 모드에서 실행할 수 있습니다.

   * **전체 모드** –이 모드에서 replicant는 마이그레이션 후에도 계속 실행 되며 원본 Apache Cassandra 시스템의 변경 내용을 수신 대기 합니다. 변경 내용이 검색 되 면 대상 Azure Cosmos 계정에 실시간으로 복제 됩니다.

   * **스냅숏 모드** –이 모드에서는 스키마 마이그레이션과 일회성 데이터 복제를 수행할 수 있습니다. 이 옵션에서는 실시간 복제가 지원 되지 않습니다.

   위의 두 모드를 사용 하 여 가동 중지 시간 없이 마이그레이션을 수행할 수 있습니다. 

1. 데이터를 마이그레이션하려면 Blitzz replicant CLI 터미널에서 다음 명령을 실행 합니다.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Replicant UI는 복제 진행률을 보여 줍니다. 스키마 마이그레이션 및 스냅숏 작업이 완료 되 면 진행률에 100%가 표시 됩니다. 마이그레이션이 완료 된 후 대상 Azure Cosmos 데이터베이스에서 데이터의 유효성을 검사할 수 있습니다.

   ![Cassandra 데이터 마이그레이션 출력](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. 마이그레이션을 위해 전체 모드를 사용 했으므로 원본 Apache Cassandra 데이터베이스에서 데이터 삽입, 업데이트 또는 삭제와 같은 작업을 수행할 수 있습니다. 나중에 대상 Azure Cosmos 데이터베이스에서 실시간으로 복제 되는지 확인 합니다. 마이그레이션 후 Azure Cosmos 컨테이너에 대해 구성 된 처리량을 줄여야 합니다.

1. Replicant any point를 중지 하 고 **--resume** 스위치를 사용 하 여 다시 시작할 수 있습니다. 복제는 데이터 일관성을 손상 시 키 지 않고 중지 된 지점부터 다시 시작 됩니다. 다음 명령은 resume 스위치를 사용 하는 방법을 보여 줍니다.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

대상에 대한 데이터 마이그레이션에 대한 자세한 내용은 실시간 마이그레이션 [Blitzz replicant 데모](https://www.youtube.com/watch?v=fsUhF9LUZmM)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md)