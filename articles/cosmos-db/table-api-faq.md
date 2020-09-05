---
title: Azure Cosmos DB의 Table API에 대한 질문과 대답
description: Azure Cosmos DB의 Table API에 대한 질문과 답변 가져오기
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: 65f276662ac4837003c7a7078b6197ba155eadc9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167592"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Azure Cosmos DB의 Table API에 대한 질문과 대답

Azure Cosmos DB Table API는 [Azure Portal](https://portal.azure.com)에서 사용할 수 있습니다. 먼저 Azure 구독에 등록해야 합니다. 등록한 후에는 Azure 구독에 Azure Cosmos DB Table API 계정을 추가한 다음 계정에 테이블을 추가할 수 있습니다. [Azure Cosmos DB Table API 소개](table-introduction.md)에서 지원되는 언어와의 연결된 빠른 시작을 찾을 수 있습니다.

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Azure Cosmos DB 및 Azure Table 스토리지의 Table API

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Table API가 Azure Table Storage 동작과 동일하지 않은 경우는 언제인가요?

Azure Cosmos DB Table API를 사용하여 테이블을 만들려는 Azure Table Storage에서 들어오는 사용자가 알고 있어야 하는 몇 가지 동작 차이점이 있습니다.

* Azure Cosmos DB Table API는 보장된 성능을 보장하기 위해 예약된 용량 모델을 사용합니다. 하지만 용량을 사용하지 않더라도 테이블이 생성되는 즉시 해당 용량에 대한 요금을 지불해야 합니다. Azure Table Storage에서는 사용하는 용량에 대해서만 요금을 지불합니다. Azure Table Storage가 10초 SLA를 제공하는 반면 Table API가 99번째 백분위수로 10ms 및 15ms 쓰기 SLA를 제공할 수 있는 이유는 바로 이 때문입니다. 하지만 이로 인해 요청이 없는 빈 테이블이라도 Table API 테이블을 사용하면 용량을 보장하기 위한 비용은 Azure Cosmos DB에서 제공한 SLA에서 모든 요청을 처리하는 데 사용할 수 있습니다.

* Table API에서 반환한 쿼리 결과는 Azure Table 스토리지에 있는 대로 파티션 키/행 키 순서로 정렬되지 않습니다.

* 행 키는 최대 255바이트일 수 있습니다.

* 일괄 처리에 최대 2MB를 포함할 수 있습니다.

* CORS는 현재 지원되지 않습니다.

* Azure Table Storage의 테이블 이름은 대/소문자를 구분하지 않지만 Azure Cosmos DB Table API에 위치합니다.

* 이진 필드 같은 정보 인코딩을 위한 일부 Azure Cosmos DB의 내부 형식은 현재 생각만큼 효율이 좋지는 않습니다. 따라서 데이터 크기에 예기치 않은 제한이 발생할 수 있습니다. 예를 들어, 데이터를 인코드하면 데이터 크기가 커지기 때문에 현재는 테이블 엔터티 1Meg 전체를 이진 데이터 저장에 사용할 수 없습니다.

* 엔터티 속성 이름 'ID'는 현재 지원되지 않습니다.

* TableQuery TakeCount는 1000으로 제한되지 않습니다.

* REST API를 기준으로 Azure Cosmos DB Table API에서 지원하지 않는 많은 엔드포인트/쿼리 옵션이 있습니다.

  | Rest 메서드 | Rest 엔드포인트/쿼리 옵션 | 문서 URL | 설명 |
  | ------------| ------------- | ---------- | ----------- |
  | GET, PUT | `/?restype=service@comp=properties`| [테이블 서비스 속성 설정](/rest/api/storageservices/set-table-service-properties) 및 [테이블 서비스 속성 가져오기](/rest/api/storageservices/get-table-service-properties) | 이 엔드포인트는 CORS 규칙, 스토리지 분석 구성 및 로깅 설정을 설정하는 데 사용됩니다. CORS는 현재 지원되지 않습니다. 또한 분석 및 로깅은 Azure Storage 테이블과 다르게 Azure Cosmos DB에서 처리됩니다. |
  | OPTIONS | `/<table-resource-name>` | [사전 CORS 테이블 요청](/rest/api/storageservices/preflight-table-request) | Azure Cosmos DB에서 현재 지원하지 않는 CORS의 일부입니다. |
  | GET | `/?restype=service@comp=stats` | [테이블 서비스 통계 가져오기](/rest/api/storageservices/get-table-service-stats) | 주 데이터베이스와 보조 데이터베이스 간에 데이터를 신속하게 복제하는 방법을 제공합니다. 복제가 쓰기의 일부이기 때문에 Cosmos DB에서 필요하지 않습니다. |
  | GET, PUT | `/mytable?comp=acl` | [테이블 ACL 가져오기](/rest/api/storageservices/get-table-acl) 및 [테이블 ACL 설정](/rest/api/storageservices/set-table-acl) | SAS(공유 액세스 서명)을 관리하는 데 사용되는 저장 액세스 정책을 가져오고 설정합니다. SAS가 지원되지만 다른 방법으로 설정되고 관리됩니다. |

* Azure Cosmos DB Table API는 ATOM이 아닌 JSON 형식만을 지원합니다.

* Azure Cosmos DB가 SAS(공유 액세스 서명)을 지원하지만 새 테이블을 만드는 권한 등 관리 작업에 관련된 정책을 비롯한 특정 정책을 지원하지 않습니다.

* 특히 .NET SDK의 경우 Azure Cosmos DB가 현재 지원하지 않는 일부의 클래스 및 메서드가 있습니다.

  | 클래스 | 지원되지 않는 메서드 |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (이 클래스는 사용되지 않음) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>기타 질문과 대답

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Table API를 사용하려면 새 SDK가 필요하나요?

아니요, 기존 스토리지 SDK가 여전히 작동합니다. 그러나 대부분의 경우 더 뛰어난 성능을 가진 지원을 받기 위해 최신 SDK를 가져오는 것이 좋습니다. [Azure Cosmos DB Table API 소개](table-introduction.md)에서 사용 가능한 언어 목록을 참조하세요.

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Table API에 연결하는 데 사용해야 하는 연결 문자열은 무엇인가요?

연결 문자열은 다음과 같습니다.

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Azure Portal의 연결 문자열 페이지에서 연결 문자열을 가져올 수 있습니다.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Table API의 .NET SDK에서 요청 옵션에 대한 구성 설정을 재정의하려면 어떻게 할까요?

일부 설정은 CreateCloudTableClient 메서드에서 처리되고 다른 설정은 클라이언트 애플리케이션의 appSettings 섹션에서 app.config를 통해 처리됩니다. 구성 설정에 대한 자세한 내용은 [Azure Cosmos DB 기능](tutorial-develop-table-dotnet.md)을 참조하세요.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>기존 Azure Table Storage SDK를 사용하는 고객에 대한 변동 사항이 있나요?

없음 기존 Azure Table Storage SDK를 사용하는 기존 고객 또는 신규 고객에 대한 변동 사항은 없습니다.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Table API와 함께 사용하기 위해 Azure Cosmos DB에 저장된 테이블 데이터를 보려면 어떻게 해야 하나요?

Azure Portal을 사용하여 데이터를 찾을 수 있습니다. 또한 Table API 코드 또는 다음 답변에 언급된 도구를 사용할 수 있습니다.

### <a name="which-tools-work-with-the-table-api"></a>Table API에는 어떤 도구를 사용할 수 있나요?

[Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)를 사용할 수 있습니다.

이전에 지정된 형식으로 연결 문자열을 가져오는 유연성이 있는 도구에서 새 Table API를 지원할 수 있습니다. 테이블 도구 목록은 [Azure Storage Client 도구](../storage/common/storage-explorers.md) 페이지에 나와 있습니다.

### <a name="is-the-concurrency-on-operations-controlled"></a>제어되는 작업에 대한 동시성이 있나요?

예, 낙관적 동시성은 ETag 메커니즘 사용을 통해 제공됩니다.

### <a name="is-the-odata-query-model-supported-for-entities"></a>엔터티에 대해 OData 쿼리 모델이 지원되나요?

예, Table API는 OData 쿼리 및 LINQ 쿼리를 지원합니다.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>동일한 애플리케이션에서 Azure Table Storage와 Azure Cosmos DB Table API에 나란히 연결할 수 있나요?

예, 각각 연결 문자열을 통해 자체 URI를 가리키는 CloudTableClient의 두 가지 개별 인스턴스를 만들어 연결할 수 있습니다.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>기존 Azure Table Storage 애플리케이션을 이 제품으로 마이그레이션하려면 어떻게 할까요?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 및 [Azure Cosmos DB 데이터 마이그레이션 도구](import-data.md)는 모두 지원됩니다.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>예를 들어 *n*GB에서 시작한 데이터가 시간이 흘러 1TB까지 증가하게 되면 이 서비스의 경우 스토리지 크기를 어떻게 확장할 수 있나요?

Azure Cosmos DB는 수평적 확장을 통해 무제한 스토리지를 제공하도록 설계되어 있습니다. 이 서비스는 스토리지를 모니터링하여 효율적으로 증가시킬 수 있습니다.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Table API 서비스를 모니터링하려면 어떻게 할까요?

Table API **메트릭** 창에서 요청 및 스토리지 사용량을 모니터링할 수 있습니다.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>필요한 처리량은 어떻게 계산하나요?

용량 추정기를 사용하여 작업에 필요한 TableThroughput을 계산할 수 있습니다. 자세한 내용은 [요청 단위 및 데이터 스토리지 예측](https://www.documentdb.com/capacityplanner)을 참조하세요. 일반적으로 엔터티를 JSON으로 나타내고 작업 수를 제공할 수 있습니다.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Table API SDK를 에뮬레이터에서 로컬로 사용할 수 있나요?

지금은 없습니다.

### <a name="can-my-existing-application-work-with-the-table-api"></a>기존 애플리케이션을 Table API에서 사용할 수 있나요?

예, 동일한 API가 지원됩니다.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Table API 기능을 사용할 생각이 없는데 기존의 Azure Table Storage 애플리케이션을 SDK로 마이그레이션해야 하나요?

아니요, 어떤 종류의 중단도 없이 기존 Azure Table Storage 자산을 만들고 사용할 수 있습니다. 그러나 Table API를 사용하지 않으면 자동 인덱스, 추가 일관성 옵션 또는 글로벌 배포의 이점을 누릴 수 없습니다.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>둘 이상의 Azure 지역에 걸쳐 Table API의 데이터 복제를 추가하려면 어떻게 해야 하나요?

Azure Cosmos DB 포털의 [전역 복제 설정](tutorial-global-distribution-sql-api.md#portal)을 사용하여 애플리케이션에 적합한 지역을 추가할 수 있습니다. 전역적으로 분산된 애플리케이션을 개발하려면 읽기 대기 시간을 단축하기 위해 로컬 지역에 PreferredLocation 정보가 설정된 애플리케이션을 추가해야 합니다.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Table API의 계정에 대한 기본 쓰기 지역을 변경하려면 어떻게 할까요?

Azure Cosmos DB의 전역 복제 포털 창에서 지역을 추가한 다음 필요한 지역에 장애 조치(Failover)를 추가할 수 있습니다. 지침은 [다중 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)을 참조하세요.

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>데이터 분산 시 대기 시간을 단축하려면 기본 읽기 하위 지역을 어떻게 구성해야 하나요?

로컬 위치에서 쉽게 읽으려면 app.config 파일에서 PreferredLocation 키를 사용합니다. 기존 애플리케이션에 대해 LocationMode가 설정된 경우 Table API는 오류를 throw합니다. Table API가 app.config 파일에서 이 정보를 선택하므로 해당 코드를 제거합니다. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Table API의 일관성 수준에 대해 어떻게 생각해야 하나요?

Azure Cosmos DB는 일관성, 가용성 및 대기 시간 간을 매우 논리적으로 절충합니다. Azure Cosmos DB는 Table API 개발자에게 5가지 일관성 수준을 제공하므로 테이블 수준에서 올바른 일관성 모델을 선택하고 데이터를 쿼리하는 동안 개별적으로 요청할 수 있습니다. 클라이언트가 연결되면 일관성 수준을 지정할 수 있습니다. CreateCloudTableClient의 consistencyLevel 인수를 통해 수준을 변경할 수 있습니다.

Table API는 읽기 및 쓰기에 짧은 대기 시간 읽기와 제한된 부실 일관성을 기본적으로 제공합니다. 자세한 내용은 [ 일관성 수준](consistency-levels.md)을 참조하세요.

기본적으로 Azure Table Storage는 지역 내에서 강력한 일관성을 제공하고 보조 위치에서 최종 일관성을 제공합니다.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Table API는 Azure Table Storage보다 더 많은 일관성 수준을 제공하나요?

예, Azure Cosmos DB의 분산된 특성에서 활용하는 방법에 대한 자세한 내용은 [일관성 수준](consistency-levels.md)을 참조하세요. 일관성 수준에 대한 보장을 제공하기 때문에 확신을 가지고 사용할 수 있습니다.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>전역 분산이 가능한 경우 데이터 복제에 얼마나 걸리나요?

Azure Cosmos DB는 로컬 지역에서 지속적으로 데이터를 적용하고 몇 밀리초 이내에 즉시 다른 지역에 데이터를 밀어넣습니다. 이 복제는 데이터 센터의 RTT(왕복 시간)에만 종속됩니다. Azure Cosmos DB의 전역 분산 기능에 대한 자세한 내용은 [Azure Cosmos DB: Azure의 전역 분산 데이터베이스 서비스](distribute-data-globally.md)를 참조하세요.

### <a name="can-the-read-request-consistency-level-be-changed"></a>읽기 요청 일관성 수준을 변경할 수 있나요?

Azure Cosmos DB를 사용하면 컨테이너 수준에서 일관성 수준을 설정할 수 있습니다. .NET SDK를 사용하면 app.config 파일에서 TableConsistencyLevel 키에 대한 값을 제공하여 컨테이너 수준을 변경할 수 있습니다. 사용 가능한 값은 강력, 제한된 부실, 세션, 일관된 접두사, 최종입니다. 자세한 내용은 [Azure Cosmos DB의 조정 가능한 데이터 일관성 수준](consistency-levels.md)을 참조하세요. 주요 개념은 테이블 설정보다 높게 요청 일관성 수준을 설정할 수 없다는 점입니다. 예를 들어 테이블에 대한 일관성 수준을 최종으로 설정하고 요청 일관성 수준을 강력으로 설정할 수 없습니다.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>지역에서 작동이 중지된 경우 Table API는 장애 조치(Failover)를 어떻게 처리하나요?

Table API는 Azure Cosmos DB라는 세계적으로 분산된 플랫폼을 활용합니다. 애플리케이션이 데이터 센터 가동 중지 시간을 견뎌낼 수 있도록 하려면 Azure Cosmos DB 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)에서 계정에 대해 하위 지역을 하나 이상 사용하도록 설정합니다. 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)을 사용하여 하위 지역의 우선 순위를 설정할 수 있습니다.

계정에 대해 원하는 개수의 지역을 추가하고 장애 조치(Failover) 우선 순위를 지정하여 장애 조치할 수 있는 위치를 제어할 수 있습니다. 데이터베이스를 사용하려면 애플리케이션도 제공해야 합니다. 이렇게 하면 고객에게 가동 중지 시간이 발생하지 않습니다. [최신 .NET 클라이언트 SDK](table-sdk-dotnet.md)는 자동으로 호밍되지만 다른 SDK는 그렇지 않습니다. 즉, 중단된 지역을 검색하여 새 지역으로 자동으로 장애 조치할 수 있습니다.

### <a name="is-the-table-api-enabled-for-backups"></a>백업에 Table API를 사용할 수 있나요?

예, Table API는 백업을 위해 Azure Cosmos DB라는 플랫폼을 활용합니다. Backup은 자동으로 수행됩니다. 자세한 내용은 [Azure Cosmos DB로 자동 온라인 백업 및 복원](online-backup-and-restore.md)을 참조하세요.

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Table API는 기본적으로 엔터티의 모든 특성을 인덱싱하나요?

예, 기본적으로 엔터티의 모든 특성이 인덱싱됩니다. 자세한 내용은 [Azure Cosmos DB: 인덱싱 정책](index-policy.md)을 참조하세요.

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>그렇다면 쿼리를 충족하기 위해 인덱스를 둘 이상 만들 필요가 없다는 의미인가요?

예, Azure Cosmos DB Table API에서는 스키마 정의 없이 모든 특성에 대한 자동 인덱싱을 제공합니다. 이 자동화 덕분에 개발자는 인덱스 만들기 및 관리보다 애플리케이션에 역량을 집중할 수 있습니다. 자세한 내용은 [Azure Cosmos DB: 인덱싱 정책](index-policy.md)을 참조하세요.

### <a name="can-i-change-the-indexing-policy"></a>인덱싱 정책을 변경할 수 있나요?

예, 인덱스 정의를 제공하여 인덱싱 정책을 변경할 수 있습니다. 설정을 적절하게 인코딩 및 이스케이프해야 합니다.

**데이터 탐색기**의 포털에서만 인덱싱 정책을 설정할 수 있는 비 .NET SDK의 경우 변경하려는 특정 테이블로 이동한 다음, **규모 및 설정**->인덱싱 정책으로 이동하여 필요한 내용을 변경한 다음, **저장**합니다.

.NET SDK에서 app.config 파일에 제출할 수 있습니다.

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>플랫폼인 Azure Cosmos DB에는 정렬, 집계, 계층 구조 등 많은 기능이 있는데, 이러한 기능이 Table API에 추가될 계획이 있나요 ?

Table API는 Azure Table Storage와 동일한 쿼리 기능을 제공합니다. 또한 Azure Cosmos DB는 정렬, 집계, 지리 공간적 쿼리, 계층 구조 및 다양한 기본 제공 함수도 지원합니다. 자세한 내용은 [SQL 쿼리](how-to-sql-query.md)를 참조하세요.

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Table API에 대한 TableThroughput은 언제 변경해야 하나요?

다음 조건 중 하나가 적용된 경우 TableThroughput을 변경해야 합니다.

* 데이터의 ETL(추출, 변환 및 로드)을 수행하거나, 짧은 시간 내에 많은 양의 데이터를 업로드할 때
* 백 엔드의 컨테이너 또는 컨테이너 집합에서 추가 처리량이 필요합니다. 예를 들어 사용되는 처리량이 프로비전된 처리량보다 많은 경우 제한됩니다. 자세한 내용은 [Azure Cosmos 컨테이너에 대한 처리량 설정](set-throughput.md)을 참조하세요.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>내 Table API 테이블의 처리량을 늘리거나 줄일 수 있나요?

예, Azure Cosmos DB 포털의 배율 창을 사용하여 처리량의 크기를 조정할 수 있습니다. 자세한 내용은 [처리량 설정](set-throughput.md)을 참조하세요.

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>새로 프로비전된 테이블에 대해 기본 TableThroughput이 설정되나요?

예, app.config를 통해 TableThroughput을 재정의하지 않고 Azure Cosmos DB에서 미리 만든 컨테이너를 사용하지 않으면 서비스에서 처리량이 400인 테이블을 만듭니다.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Table Storage 서비스의 기존 고객에 대한 가격 책정에 변동 사항이 있나요?

없음 기존 Azure Table Storage 고객을 위한 가격에는 변동 사항이 없습니다.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Table API의 가격은 어떻게 계산하나요?

가격은 할당된 TableThroughput에 따라 달라집니다.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Table API 제안에서 테이블에 대한 속도 제한을 처리하려면 어떻게 할까요?

요청 속도가 기본 컨테이너 또는 컨테이너 세트에 대해 프로비전된 처리량의 용량을 초과하면 오류가 발생하고, SDK에서 다시 시도 정책을 적용하여 호출을 다시 시도합니다.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Azure Cosmos DB의 Table API 제품을 활용하기 위해 PartitionKey 및 RowKey와 별도로 처리량을 선택해야 하는 이유는 무엇인가요?

사용자가 app.config 파일에서 또는 포털을 통해 기본 처리량을 제공하지 않으면 Azure Cosmos DB가 사용자의 컨테이너에 대해 기본 처리량을 설정합니다.

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. TableThroughput을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.

처리량 지정을 사용하면 처리량을 탄력적으로 변경하여 애플리케이션의 계절성을 활용하고, 처리량 요구 사항을 충족하고, 비용을 절감할 수 있습니다.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>저는 데이터 저장 비용만 부담하면 되고 거의 쿼리하지 않기 때문에 Azure Table Storage가 저렴했습니다. 그런데 단일 트랜잭션을 수행하지도 않았고, 어떠한 것도 저장하지 않았는데도 Azure Cosmos DB Table API 제품에 비용이 청구되는 것 같습니다. 설명해 주시기 바랍니다.

Azure Cosmos DB는 전역적으로 분산된 SLA 기반 시스템으로, 가용성, 대기 시간 및 처리량을 보장하도록 설계되었습니다. Azure Cosmos DB에서 처리량을 예약하는 경우 다른 시스템과 달리 처리량이 보장됩니다. Azure Cosmos DB는 보조 인덱스 및 글로벌 배포와 같이 고객이 요청할 수 있는 추가 기능을 제공합니다.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Azure Table Storage에 데이터를 수집할 때 파티션이 가득 찼음을 나타내는 &quot;할당량 가득 참&quot; 알림을 받은 적이 없는데, Table API를 사용하면 이 메시지가 나타냅니다. 이 제품이 현재 사용자를 제한하거나 기존 애플리케이션을 변경하도록 강제하나요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장할 뿐만 아니라 무제한 확장을 제공합니다. 보장된 최고의 성능을 얻기 위해서는 데이터 크기 및 인덱스가 관리 및 확장 가능해야 합니다. 파티션 키 당 엔터티 또는 항목 수에 대 한 20gb 제한은 뛰어난 조회 및 쿼리 성능을 제공 하도록 보장 하기 위한 것입니다. Azure Storage에 대해서도 애플리케이션이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>그렇다면 Table API에도 여전히 PartitionKey 및 RowKey가 필요한가요?

예. Table API의 노출 영역이 Azure Table Storage SDK와 유사하기 때문에 파티션 키를 사용하면 데이터를 효율적으로 배포할 수 있습니다. 행 키는 파티션 내에서 고유합니다. 행 키는 반드시 존재해야 하며 표준 SDK에서와 같이 null일 수 없습니다. RowKey의 길이는 255바이트이고 PartitionKey의 길이는 1KB입니다.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Table API의 오류 메시지로는 어떤 것이 있나요?

Azure Table Storage 및 Azure Cosmos DB Table API는 동일한 SDK를 사용합니다. 따라서 대부분의 오류가 같습니다.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Tables API에서 많은 수의 테이블을 차례로 만들려고 하면 제한되는 이유는 무엇인가요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장하며, 프로비전된 시스템이기 때문에 이러한 요구 사항을 보장하기 위해 리소스를 예약해 둡니다. 따라서 빠른 테이블 만들기 속도가 감지되고 제한됩니다. 테이블 만들기 속도를 살펴보고 분당 5 미만으로 낮추는 것이 좋습니다. Table API는 프로비전된 시스템입니다. 프로비전하는 순간 비용이 발생하기 시작합니다.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK 또는 버그에 대한 피드백은 어떻게 제공할 수 있나요?

다음 방법 중 하나로 사용자 의견을 공유할 수 있습니다.

* [사용자 의견](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow는 프로그래밍 질문에 가장 적합합니다. 질문이 [주제와 관련된](https://stackoverflow.com/help/on-topic) 것인지 확인하고 [질문에 대해 분명하고 답변할 수 있도록 가능한 한 자세하게 제공하세요](https://stackoverflow.com/help/how-to-ask).

## <a name="next-steps"></a>다음 단계

* [.NET SDK 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드](create-table-dotnet.md)
* [Azure Cosmos DB Table API 데이터를 관리하는 Java 앱 빌드](create-table-java.md)