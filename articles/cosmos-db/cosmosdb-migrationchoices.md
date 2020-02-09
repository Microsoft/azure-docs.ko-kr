---
title: Cosmos DB 마이그레이션 옵션
description: 이 문서에서는 온-프레미스 또는 클라우드 데이터를로 마이그레이션하는 다양 한 옵션에 대해 설명 Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882398"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB으로 마이그레이션하는 옵션

다양 한 데이터 원본에서 Azure Cosmos DB으로 데이터를 로드할 수 있습니다. 또한 Azure Cosmos DB는 여러 Api를 지원 하기 때문에 대상은 기존 Api 중 하나일 수 있습니다. 다양 한 원본에서 다른 Azure Cosmos DB Api로의 마이그레이션 경로를 지원 하기 위해 각 마이그레이션 경로에 대한 특수 한 처리 기능을 제공 하는 여러 솔루션이 있습니다. 이 문서에서는 사용 가능한 솔루션을 나열 하 고 이점 및 제한 사항에 대해 설명 합니다.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>마이그레이션 도구 선택에 영향을 주는 요소

다음 요소는 마이그레이션 도구를 선택 하는 방법을 결정 합니다.
* **온라인 및 오프 라인 마이그레이션**: 대부분의 마이그레이션 도구는 일회성 마이그레이션을 수행 하는 경로를 제공 합니다. 이는 데이터베이스에 액세스 하는 응용 프로그램에 가동 중지 시간이 발생할 수 있음을 의미 합니다. 일부 마이그레이션 솔루션은 원본 및 대상 간에 설정 된 복제 파이프라인이 있는 실시간 마이그레이션을 수행 하는 방법을 제공 합니다.

* **데이터 원본**: 기존 데이터는 Oracle DB2, Datastax Cassanda, Azure SQL Server, PostgreSQL 등과 같은 다양 한 데이터 원본에 있을 수 있습니다. 데이터는 기존 Azure Cosmos DB 계정에 있을 수 있으며 마이그레이션의 의도는 데이터 모델을 변경 하거나 다른 파티션 키를 사용 하 여 컨테이너의 데이터를 다시 분할할 수 있습니다.

* **AZURE COSMOS DB API**: AZURE COSMOS DB의 SQL api에는 여러 마이그레이션 시나리오를 지 원하는 Azure Cosmos DB 팀에서 개발한 여러 도구가 있습니다. 다른 모든 Api에는 커뮤니티에서 개발 하 고 유지 관리 하는 고유의 특수 도구 집합이 있습니다. Azure Cosmos DB는 유선 프로토콜 수준에서 이러한 Api를 지원 하기 때문에 데이터를 Azure Cosmos DB으로 마이그레이션하는 동안 이러한 도구는 그대로 작동 해야 합니다. 그러나이 개념은 Azure Cosmos DB에만 해당 되므로 제한에 대한 사용자 지정 처리가 필요할 수 있습니다.

* **데이터 크기**: 대부분의 마이그레이션 도구는 작은 데이터 집합에 대해 매우 잘 작동 합니다. 데이터 집합이 수백 기가바이트를 초과 하는 경우 마이그레이션 도구의 선택이 제한 됩니다. 

* **예상 마이그레이션 기간**: 더 낮은 처리량을 소비 하거나 대상 Azure Cosmos DB 컨테이너에서 프로 비전 된 전체 처리량을 사용 하 고 더 낮은 마이그레이션 작업을 완료 하도록 마이그레이션을 구성할 수 있습니다. 런타임.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>대량 데이터 집합에 적합 하지 &bull;|
|오프라인|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>&bull;에서 Azure Cosmos DB bulk executor 라이브러리를 사용 합니다. <br/>대량 데이터 집합에 적합 한 &bull; <br/>검사점 부족 &bull;-마이그레이션 과정에서 문제가 발생 하는 경우 전체 마이그레이션 프로세스를 다시 시작 해야 함을 의미 합니다.<br/>배달 못 한 편지 큐가 없는 경우에는 일부 잘못 된 파일이 전체 마이그레이션 프로세스를 중지할 수 있음을 의미 하는 &bull;.|
|오프라인|[Azure Cosmos DB Spark 커넥터](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;에서 Azure Cosmos DB bulk executor 라이브러리를 사용 합니다. <br/>대량 데이터 집합에 적합 한 &bull; <br/>사용자 지정 Spark 설정이 필요한 &bull; <br/>&bull; Spark는 스키마 불일치로 인식 되며 마이그레이션 중에 문제가 발생할 수 있습니다. |
|오프라인|[Cosmos DB 대량 실행자 라이브러리를 사용 하는 사용자 지정 도구](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;는 검사점, 배달 못 한 편지 처리 기능을 제공 하 여 마이그레이션 복원 력을 높입니다. <br/>매우 큰 데이터 집합에 적합 한 &bull; (10TB +)  <br/>&bull; 하려면이 도구의 사용자 지정 설치를 App Service으로 실행 해야 합니다. |
|온라인|[Cosmos DB 함수 + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; 쉽게 설정 <br/>&bull; 원본이 Azure Cosmos DB 컨테이너인 경우에만 작동 합니다. <br/>대량 데이터 집합에 적합 하지 &bull; <br/>&bull; 원본 컨테이너에서 삭제를 캡처하지 않습니다. |
|온라인|[ChangeFeed를 사용 하는 사용자 지정 마이그레이션 서비스](https://aka.ms/CosmosDBMigrationSample)|진행률 추적을 제공 &bull; <br/>&bull; 원본이 Azure Cosmos DB 컨테이너인 경우에만 작동 합니다. <br/>&bull; 큰 데이터 집합에 대해서도 작동 합니다. <br/>&bull;에서 사용자가 변경 피드 프로세서를 호스팅하도록 App Service를 설정 해야 합니다. <br/>&bull; 원본 컨테이너에서 삭제를 캡처하지 않습니다.|
|온라인|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;은 Oracle, DB2, SQL Server 등 다양 한 소스에서 작동 합니다. <br/>ETL 파이프라인을 쉽게 빌드하고 모니터링을 위한 대시보드를 제공 하는 &bull; <br/>더 큰 데이터 집합을 지 원하는 &bull; <br/>&bull; 타사 도구 이므로 marketplace에서 구매 하 고 사용자 환경에 설치 해야 합니다.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>대량 데이터 집합에 적합 하지 &bull;|
|오프라인|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>&bull;에서 Azure Cosmos DB bulk executor 라이브러리를 사용 합니다. <br/>대량 데이터 집합에 적합 한 &bull; <br/>검사점 부족 &bull; 마이그레이션 과정 중에 발생 하는 모든 문제에 대해 전체 마이그레이션 프로세스를 다시 시작 해야 함을 의미 합니다.<br/>배달 못 한 편지 큐가 없으면 몇 개의 잘못 된 파일이 전체 마이그레이션 프로세스를 중지할 수 있습니다. &bull; <br/>특정 데이터 원본에 대한 읽기 처리량을 늘리려면 사용자 지정 코드가 필요 &bull;|
|오프라인|[기존 Mongo 도구 (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 쉽게 설정 하 고 통합할 수 있습니다. <br/>제한에 대한 사용자 지정 처리를 요구 하 &bull;|
|온라인|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;에서 Azure Cosmos DB bulk executor 라이브러리를 사용 합니다. <br/>대량 데이터 집합에 적합 하며 라이브 변경 내용 복제를 처리 하는 &bull; <br/>&bull;는 다른 MongoDB 원본 에서만 작동 합니다.|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[cqlsh COPY 명령](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; 쉽게 설정 <br/>대량 데이터 집합에 적합 하지 &bull; <br/>&bull; 원본이 Cassandra 테이블인 경우에만 작동 합니다.|
|오프라인|[Spark를 사용 하 여 테이블 복사](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;는 Spark 기능을 사용 하 여 변환 및 수집을 병렬화 할 수 있습니다. <br/>제한를 처리 하려면 사용자 지정 다시 시도 정책을 사용 하는 구성이 필요 &bull;|
|온라인|[Striim (Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;은 Oracle, DB2, SQL Server 등 다양 한 소스에서 작동 합니다. <br/>ETL 파이프라인을 쉽게 빌드하고 모니터링을 위한 대시보드를 제공 하는 &bull; <br/>더 큰 데이터 집합을 지 원하는 &bull; <br/>&bull; 타사 도구 이므로 marketplace에서 구매 하 고 사용자 환경에 설치 해야 합니다.|
|온라인|[Blitzz (Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>더 큰 데이터 집합을 지 원하는 &bull; <br/>&bull; 타사 도구 이므로 marketplace에서 구매 하 고 사용자 환경에 설치 해야 합니다.|

## <a name="other-apis"></a>기타 Api
SQL API, Mongo API 및 Cassandra API 이외의 Api의 경우 API의 기존 에코 시스템에서 지원 되는 다양 한 도구가 있습니다. 

**Table API** 
* [데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AZCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [그래프 대량 실행자 라이브러리](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>다음 단계

* [.Net](bulk-executor-dot-net.md) 및 [Java](bulk-executor-java.md)에서 대량 실행자 라이브러리를 사용 하는 샘플 응용 프로그램을 사용해 보세요. 
* 대량 실행자 라이브러리는 Cosmos DB Spark 커넥터에 통합 되어 있습니다. 자세한 내용은 [Azure Cosmos DB spark 커넥터](spark-connector.md) 문서를 참조 하세요.  
* 대규모 마이그레이션에 대한 추가 도움말을 보려면 "일반 권고" 문제 유형 및 "대규모 (TB +) 마이그레이션" 문제 하위 유형에 서 지원 티켓을 열어 Azure Cosmos DB 제품 팀에 문의 하세요.
