---
title: Azure Cosmos DB Cassandra API에 대 한 질문과 대답
description: Azure Cosmos DB Cassandra API 대 한 질문과 대답을 확인 하세요.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: b327c0786fb07488fd8863272598dbffe19bfe07
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167609"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>의 Cassandra API에 대 한 질문과 대답 Azure Cosmos DB

이 문서에서는 Azure Cosmos DB에서 Apache Cassandra와 Cassandra API의 기능 차이점에 대해 설명 합니다. 또한 Azure Cosmos DB의 Cassandra API에 대 한 질문과 대답을 제공 합니다.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra와 Cassandra API 간의 주요 차이점

- Apache Cassandra는 파티션 키 크기에 대해 100의 제한을 권장 합니다. Azure Cosmos DB에 대 한 Cassandra API에는 파티션당 최대 20gb가 허용 됩니다.
- Apache Cassandra를 사용 하면 지 속성 커밋을 비활성화할 수 있습니다. 커밋 로그에 대 한 쓰기를 건너뛰고 memtables로 바로 이동할 수 있습니다. 이로 인해 memtables가 디스크의 SSTables에 플러시되기 전에 노드가 중단 되 면 데이터가 손실 될 수 있습니다. Azure Cosmos DB는 데이터 손실을 방지 하기 위해 항상 내구성이 있는 커밋을 수행 합니다.
- 작업에 많은 교체 또는 삭제가 포함 된 경우 Apache Cassandra는 성능이 저하 된 것을 볼 수 있습니다. 이유는 읽기 작업에서 최신 데이터를 가져오기 위해 건너뛰어야 하는 삭제 표식입니다. 워크 로드에 많은 교체 또는 삭제가 있는 경우 Cassandra API에는 감소 된 읽기 성능이 표시 되지 않습니다.
- 높은 대체 작업의 시나리오에서 압축을 실행 하 여 디스크에 SSTables를 병합 해야 합니다. (Apache Cassandra의 쓰기는 추가 전용 이므로 병합이 필요 합니다. 여러 업데이트는 주기적으로 병합 해야 하는 개별 SSTable 된 항목으로 저장 됩니다. 이러한 상황에서는 압축 하는 동안 읽기 성능이 저하 될 수도 있습니다. API에서 압축을 구현 하지 않기 때문에 이러한 성능 영향은 Cassandra API에서 발생 하지 않습니다.
- Apache Cassandra를 사용 하 여 복제 인수를 1로 설정할 수 있습니다. 그러나 데이터가 있는 유일한 노드가 중단 되 면 가용성이 낮아집니다. 이는 항상 복제 요인이 4 (3의 쿼럼) 이기 때문에 Azure Cosmos DB에 대 한 Cassandra API에는 문제가 되지 않습니다.
- Apache Cassandra에서 노드를 추가 하거나 제거 하려면 기존 노드가 현재 토큰 범위 중 일부를 새 노드로 이동 하는 동안 새 노드의 높은 CPU 사용량과 함께 수동 작업이 필요 합니다. 이 상황은 기존 노드를 서비스 해제 하는 경우에도 마찬가지입니다. 그러나 Cassandra API 서비스 또는 응용 프로그램에서 관찰 된 문제 없이 확장 됩니다.
- Apache Cassandra 같이 클러스터의 각 노드에 **num_tokens** 를 설정할 필요가 없습니다. Azure Cosmos DB는 노드와 토큰 범위를 완전히 관리 합니다.
- Cassandra API는 완전히 관리 됩니다. Apache Cassandra에 사용 되는 복구 및 서비스 해제와 같은 **nodetool** 명령이 필요 하지 않습니다.

## <a name="other-frequently-asked-questions"></a>기타 질문과 대답

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API 지원 되는 프로토콜 버전은 무엇 인가요?

Azure Cosmos DB에 대 한 Cassandra API는 CQL 버전 3(sp3)을 지원 합니다. CQL 호환성은 공용 [Apache Cassandra GitHub 리포지토리](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)를 기반으로 합니다. 다른 프로토콜 지원에 대 한 의견이 있는 경우 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db) 을 통해 알려 주시기 바랍니다 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>테이블 요구 사항에 대 한 처리량을 선택 해야 하는 이유는 무엇 인가요?

Azure Cosmos DB Azure Portal 또는 CQL에서 테이블을 만든 위치에 따라 컨테이너에 대 한 기본 처리량을 설정 합니다.

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이러한 보장은 엔진이 테 넌 트의 작업에 대 한 거 버 넌 스를 적용할 수 있을 때 가능 합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.
응용 프로그램의 계절성를 활용 하 고 비용을 절감 하기 위해 [변경 처리량을 탄력적으로](manage-scale-cassandra.md) 수 있습니다.

처리량 개념은 [Azure Cosmos DB의 요청 단위](request-units.md)에 설명되어 있습니다. 테이블에 대 한 처리량은 기본 실제 파티션에 균등 하 게 분산 됩니다.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>CQL를 통해 만들어진 테이블의 처리량은 무엇 인가요?

Azure Cosmos DB는 처리량을 제공 하기 위한 통화로 초당 요청 단위 (/s)를 사용 합니다. CQL를 통해 만들어진 테이블의 기본값은 400입니다. Azure Portal에서 사용자를 변경할 수 있습니다.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>처리량을 다 사용하면 어떻게 되나요?

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이러한 보장은 엔진이 테 넌 트의 작업에 대 한 거 버 넌 스를 적용할 수 있을 때 가능 합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.

이 용량을 초과 하면 용량이 사용 되었음을 나타내는 다음과 같은 오류 메시지가 표시 됩니다.

**0x1001 오버 로드 됨: "요청 빈도가 크므로" 요청을 처리할 수 없습니다.** 

이 문제가 발생 하는 작업 (및 볼륨)을 확인 하는 것이 중요 합니다. Azure Portal에 대 한 메트릭을 사용 하 여 프로 비전 된 용량을 초과 하는 소비 된 용량에 대 한 아이디어를 얻을 수 있습니다. 그런 다음 모든 기본 파티션에서 용량이 거의 동일 하 게 사용 되는지 확인 해야 합니다. 한 파티션에서 대부분의 처리량을 사용 하는 것이 확인 되 면 워크 로드의 불균형을 갖게 됩니다.

사용 가능한 메트릭은 전체 파티션 또는 집계에서 시간, 일 단위 및 7 일간의 처리량을 사용 하는 방법을 보여 주는 메트릭을 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버깅](use-metrics.md)을 참조하세요.

진단 로그에 대한 내용은 [Azure Cosmos DB 진단 로깅](logging.md) 문서에 설명되어 있습니다.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>기본 키는 Azure Cosmos DB의 파티션 키 개념에 대응하나요?

예, 파티션 키를 사용 하 여 엔터티를 올바른 위치에 배치 합니다. Azure Cosmos DB에서 실제 파티션에 저장 된 올바른 논리적 파티션을 찾는 데 사용 됩니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다. 여기서 중요 한 요점은 논리 파티션은 20gb 제한을 초과 하지 않아야 한다는 것입니다.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>파티션이 가득 찬 알림이 표시 되 면 어떻게 되나요?

Azure Cosmos DB는 SLA (서비스 수준 계약)를 기반으로 하는 시스템입니다. 대기 시간, 처리량, 가용성 및 일관성을 보장 하는 무제한 규모를 제공 합니다. 무제한 저장소는 분할을 주요 개념으로 사용 하는 수평 확장 데이터를 기반으로 합니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다.

논리적 파티션 당 엔터티 또는 항목 수에 대 한 20gb 제한을 준수 해야 합니다. 애플리케이션이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다. 이 오류는 데이터가 왜곡 된 경우에만 발생할 수 있습니다. 즉, 하나의 파티션 키 (20gb 이상)에 대해 많은 데이터가 있습니다. 저장소 포털을 사용 하 여 데이터 배포를 찾을 수 있습니다. 이 오류를 해결 하는 방법은 테이블을 다시 만들고 세부적인 기본 (파티션 키)을 선택 하 여 데이터를 보다 효율적으로 배포할 수 있다는 것입니다.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Cassandra API를 수백만 또는 수십억 개의 파티션 키로 키 값 저장소로 사용할 수 있나요?

Azure Cosmos DB는 스토리지를 수평으로 확장하여 무제한 데이터를 저장할 수 있습니다. 이 저장소는 처리량에 독립적입니다. 예, 올바른 기본/파티션 키를 지정 하 여 키 및 값을 저장 하 고 검색 하는 데 Cassandra API 항상 사용할 수 있습니다. 이러한 개별 키를 사용 하는 경우에는 문제가 발생 하지 않고 실제 파티션 위에 배치 됩니다.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Cassandra API를 사용 하 여 둘 이상의 테이블을 만들 수 있나요?

예, Cassandra API를 사용 하 여 둘 이상의 테이블을 만들 수 있습니다. 이러한 각 테이블은 처리량 및 스토리지에 대한 단위로 처리됩니다.

### <a name="can-i-create-more-than-one-table-in-succession"></a>연속 해 서 두 개 이상의 테이블을 만들 수 있나요?

Azure Cosmos DB은 데이터 및 제어 평면 작업 모두에 대 한 리소스 관리 시스템입니다. 컨테이너 (예: 컬렉션 및 테이블)는 지정 된 처리량 용량에 대해 프로 비전 되는 런타임 엔터티입니다. 연속적으로 이러한 컨테이너를 만드는 작업은 예상과 다를 수 있으며 제한 될 수 있습니다. 테이블을 즉시 삭제 하거나 생성 하는 테스트를 수행 하는 경우 공간을 확보 해 보세요.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>만들 수 있는 테이블의 최대 수는 얼마 인가요?

테이블 수에 대 한 물리적 제한은 없습니다. 일반적으로 수십 또는 수백이 아닌 많은 테이블이 생성 되어야 하는 많은 테이블이 있는 경우에는에 전자 메일을 보냅니다 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>만들 수 있는 최대 보내주세요의 수는 얼마 인가요?

키 공간 수는 메타 데이터 컨테이너 이기 때문에 실제 제한이 없습니다. 키 공간이 많은 경우로 전자 메일을 보내세요 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>일반 테이블에서 시작한 후 많은 데이터를 가져올 수 있나요?

예. 균일 하 게 분산 된 파티션이 있다고 가정 하면 저장소 용량은 자동으로 관리 되 고 더 많은 데이터를 푸시할 때 늘어납니다. 따라서 노드를 관리 하 고 프로 비전 하지 않고도 필요한 만큼 데이터를 안전 하 게 가져올 수 있습니다. 그러나 즉각적인 데이터 증가를 예상 하는 경우에는 시작 하 여 즉시 증가 하는 대신 [예상 처리량을 직접 프로 비전](set-throughput.md) 하는 것이 더 좋습니다.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>YAML 파일 설정을 사용 하 여 API 동작을 구성할 수 있나요?

Azure Cosmos DB에 대 한 Cassandra API 작업을 실행 하기 위한 프로토콜 수준 호환성을 제공 합니다. 관리, 모니터링 및 구성의 복잡성을 숨깁니다. 개발자/사용자는 가용성, 삭제 표식, 키 캐시, 행 캐시, 블 룸 필터 및 다양 한 기타 설정에 대해 걱정할 필요가 없습니다. Cassandra API는 구성 및 관리의 오버 헤드 없이 필요한 읽기 및 쓰기 성능을 제공 하는 데 중점을 두어야 합니다.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API은 노드 추가, 클러스터 상태 및 노드 상태 명령을 지원 하나요?

Cassandra API는 용량 계획을 간소화 하 고 처리량 및 저장소에 대 한 탄력성 요청에 응답 합니다. Azure Cosmos DB를 사용 하 여 필요한 처리량을 프로 비전 합니다. 그런 다음 노드를 추가, 삭제 또는 관리 하는 것에 대해 걱정 하지 않고 하루에 원하는 횟수 만큼 규모를 확장 하 고 축소할 수 있습니다. 노드 및 클러스터 관리에 대 한 도구를 사용할 필요가 없습니다.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>단순/네트워크와 같이 keyspace 만들기에 대 한 다양 한 구성 설정을 사용 하면 어떻게 되나요?

Azure Cosmos DB는 가용성 및 짧은 대기 시간을 위해 기본 제공 되는 글로벌 배포를 제공 합니다. 복제본 이나 기타 항목을 설정 하지 않아도 됩니다. 쓰기는 항상 지속적으로 쿼럼이 커밋된 모든 지역에서 커밋된 것으로, 성능 보장을 제공 합니다.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>테이블 메타 데이터에 대 한 다양 한 설정에서 무슨 일이 발생 하나요?

Azure Cosmos DB는 읽기, 쓰기 및 처리량에 대 한 성능 보장을 제공 합니다. 따라서 구성 설정에 대 한 터치와 실수로 조작 하는 것에 대해 걱정할 필요가 없습니다. 이러한 설정에는 블 룸 필터, 캐싱, 읽기 복구 가능성, gc_grace 및 압축 memtable_flush_period 포함 됩니다.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra 테이블에 대해 ttl (time to live)을 지원 하나요?

예, TTL이 지원됩니다.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>처리량과 함께 인프라를 모니터링 하려면 어떻게 해야 하나요?

Azure Cosmos DB는 인프라를 관리하고 모니터링할 걱정 없이 생산성을 높이는 데 도움이 되는 플랫폼 서비스입니다. 예를 들어 다양 한 도구를 사용 하 여 이전에 노드 상태, 복제본 상태, gc 및 OS 매개 변수를 모니터링할 필요가 없습니다. 제한 되는지 확인 하기 위해 포털 메트릭에 제공 되는 처리량을 처리 하 고 처리량을 늘리거나 줄일 수 있습니다. 다음을 할 수 있습니다.

- [Sla](monitor-accounts.md) 모니터링
- [메트릭](use-metrics.md) 사용
- [진단 로그](logging.md) 사용

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Cassandra API에서 사용할 수 있는 클라이언트 Sdk는 무엇입니까?

CQLv3를 사용 하는 Apache Cassandra SDK의 클라이언트 드라이버는 클라이언트 프로그램에 사용 되었습니다. 다른 드라이버를 사용 하는 경우 또는 문제가 발생 하는 경우에 메일을 보냅니다 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>복합 파티션 키가 지원 되나요?

예, 일반 구문을 사용 하 여 복합 파티션 키를 만들 수 있습니다.

### <a name="can-i-use-sstableloader-for-data-loading"></a>데이터 로드에 sstableloader를 사용할 수 있나요?

아니요, sstableloader는 지원 되지 않습니다.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>온-프레미스 Apache Cassandra 클러스터를 Cassandra API와 쌍으로 연결할 수 있나요?

현재 Azure Cosmos DB에는 작업의 오버 헤드 없이 클라우드 환경에 최적화 된 환경이 있습니다. 페어링이 필요한 경우 시나리오에 대 한 설명과 함께에 메일을 보냅니다 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) . 온-프레미스 또는 클라우드 Cassandra 클러스터를 Azure Cosmos DB Cassandra API와 연결 하는 데 도움이 되는 제품에 대해 노력 하 고 있습니다.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API에서 전체 백업을 제공 하나요?

Azure Cosmos DB은 모든 Api에서 4 시간 간격으로 수행 되는 두 개의 무료 전체 백업을 제공 합니다. 따라서 백업 일정을 설정할 필요가 없습니다. 

보존 및 빈도를 수정 하려면 전자 메일을 보내거나 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 지원 사례를 발생 시킵니다. 백업 기능에 대한 정보는 [Azure Cosmos DB로 자동 온라인 백업 및 복원](online-backup-and-restore.md) 문서에 나와 있습니다.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>지역에서 작동이 중지된 경우 Cassandra API 계정은 장애 조치(Failover)를 어떻게 처리하나요?

Cassandra API은 Azure Cosmos DB의 전역적으로 분산 된 플랫폼에서 활용 합니다. 응용 프로그램이 데이터 센터 가동 중지 시간을 허용할 수 있도록 하려면 Azure Portal 계정에 하나 이상의 지역을 하나 이상 사용 하도록 설정 합니다. 자세한 내용은 [Azure Cosmos DB를 사용 하 여 고가용성](high-availability.md)을 참조 하세요.

계정에 대해 원하는 개수의 지역을 추가하고 장애 조치(Failover) 우선 순위를 지정하여 장애 조치할 수 있는 위치를 제어할 수 있습니다. 데이터베이스를 사용하려면 애플리케이션도 제공해야 합니다. 이렇게 하면 고객에게 가동 중지 시간이 발생하지 않습니다.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API는 기본적으로 엔터티의 모든 특성을 인덱싱 하나요?

아니요. Cassandra API는 Apache Cassandra와 비슷한 방식으로 작동 하는 [보조 인덱스](cassandra-secondary-index.md)를 지원 합니다. API는 기본적으로 모든 특성을 인덱싱하지 않습니다.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>새로운 Cassandra API SDK를 에뮬레이터에서 로컬로 사용할 수 있나요?

예, 지원됩니다. [로컬 개발 및 테스트에 Azure Cosmos Emulator 사용](local-emulator.md#cassandra-api) 문서에서이 기능을 사용 하도록 설정 하는 방법에 대 한 세부 정보를 찾을 수 있습니다.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Apache Cassandra 클러스터에서 Azure Cosmos DB로 데이터를 마이그레이션하려면 어떻게 해야 하나요?

마이그레이션 옵션에 대 한 자세한 내용은 [Azure Cosmos DB의 데이터를 Cassandra API 계정으로 마이그레이션](cassandra-import-data.md) 자습서를 참조 하세요.


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Cassandra API 기능에 대 한 피드백을 어디에서 제공할 수 있나요?

피드백은 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db)을 통해 보내주세요.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB Cassandra API 계정으로 탄력적으로 크기 조정을](manage-scale-cassandra.md)시작 하세요.
