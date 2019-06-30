---
title: Azure Cosmos DB의 다양한 API에 대한 질문과 대답
description: 전역적으로 분산된 다중 모델 데이터베이스 서비스인 Azure Cosmos DB에 대해 자주 묻는 질문에 대한 대답을 살펴봅니다. 용량, 성능 수준 및 크기 조정에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 89c04a55138d57fd2ff37a96f2bc92b12d1780d9
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341226"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Azure Cosmos DB의 다양한 API에 대한 질문과 대답

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB의 대표적인 사용 사례는 무엇인가요?

Azure Cosmos DB는 자동 크기 조정, 예측 가능한 성능, 밀리초 응답 시간의 빠른 주문, 스키마 제약 없는 데이터에 대한 쿼리 기능 등이 중요한 경우 새로운 웹, 모바일, 게임 및 IoT 애플리케이션에 적합한 솔루션입니다. Azure Cosmos DB는 애플리케이션 데이터 모델의 신속한 개발 및 연속적인 반복 지원에 적합합니다. 사용자 생성 콘텐츠 및 데이터를 관리하는 애플리케이션은 [Azure Cosmos DB의 일반적인 사용 사례](use-cases.md)입니다.

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB는 어떻게 예측 가능한 성능을 제공하나요?

RU([요청 단위](request-units.md))는 Azure Cosmos DB의 처리량 측정 단위입니다. 1RU 처리량은 1KB 문서의 GET 처리량에 해당합니다. 읽기, 쓰기, SQL 쿼리, 저장 프로시저 실행 등 Azure Cosmos DB에서의 모든 작업에는 작업을 완료하는 데 필요한 처리량을 기반으로 하는 결정적 RU 값이 있습니다. CPU, IO 및 메모리와 이 각각이 애플리케이션 처리량에 미치는 영향을 고려하는 대신 단일 RU 측정값 측면에서 고려할 수 있습니다.

초당 Ru 기준으로 프로 비전 된 처리량으로 각 Azure Cosmos 컨테이너를 구성할 수 있습니다. 모든 규모의 애플리케이션에 대해 개별 요청을 벤치마킹하여 해당 RU 값을 측정하고 모든 요청에서 총 요청 단위 합계를 처리하도록 컨테이너를 프로비전할 수 있습니다. 또한 애플리케이션 요구 사항이 진화함에 따라 컨테이너의 처리량을 확장하거나 축소할 수 있습니다. 요청 단위에 대 한 자세한 내용은 컨테이너 요구 사항을 결정에 대 한 도움말을 시도 합니다 [처리량 계산기](https://www.documentdb.com/capacityplanner)합니다.

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB에서는 키/값, 열 형식, 문서 및 그래프와 같은 다양한 데이터 모델을 어떻게 지원하나요?

Azure Cosmos DB는 ARS(아톰, 레코드 및 시퀀스) 디자인을 기반으로 하므로 키/값(테이블), 열 형식, 문서 및 그래프 데이터 모델을 기본적으로 모두 지원합니다. 아톰, 레코드 및 시퀀스는 다양한 데이터 모델에 쉽게 매핑 및 프로젝션할 수 있습니다. 모델의 하위 집합에 대한 API는 현재 사용 가능하며(SQL, MongoDB, 테이블 및 Gremlin) 추가 데이터 모델과 관련된 기타 사항은 향후 제공될 예정입니다.

Azure Cosmos DB에는 개발자로부터 스키마 또는 보조 인덱스를 요구하지 않고도 수집되는 모든 데이터를 자동으로 인덱싱할 수 있는 스키마 독립적인 인덱싱 엔진을 포함합니다. 이 엔진은 인덱스 및 쿼리 처리 하위 시스템에서 저장소 레이아웃을 분리하는 논리적 인덱스 레이아웃 집합(반전됨, 열 형식, 트리)을 기반으로 합니다. Cosmos DB는 유선 프로토콜과 API 세트를 확장 가능한 방식으로 지원하고 핵심 데이터 모델(1)과 논리적 인덱스 레이아웃(2)으로 효율적으로 변환하여 기본적으로 둘 이상의 데이터 모델을 고유하게 지원할 수 있도록 합니다.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB는 HIPAA 규격인가요?

예, Azure Cosmos DB는 HIPAA 규격입니다. HIPAA는 개별적으로 식별할 수 있는 상태 정보의 사용, 공개 및 보호에 대한 요구 사항을 설정합니다. 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)를 참조하세요.

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB의 저장소 한도는 어느 정도인가요?

하나의 컨테이너가 Azure Cosmos DB에 저장할 수 있는 총 데이터 양에는 제한이 없습니다.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB의 처리량 한도는 어느 정도인가요?

하나의 컨테이너가 Azure Cosmos DB에서 지원할 수 있는 총 처리량에는 제한이 없습니다. 이는 대다수 파티션 키 간에 워크로드를 거의 균등하게 분산시키기 위한 것입니다.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>직접 연결 및 게이트웨이 연결 모드를 암호화하나요?

예, 두 모드는 항상 완벽하게 암호화됩니다.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 비용은 얼마인가요?

자세한 내용은 [Azure Cosmos DB 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cosmos-db/) 페이지를 참조하세요. Azure Cosmos DB 사용 요금은 프로비전된 컨테이너 수, 컨테이너가 온라인이었던 시간 및 각 컨테이너의 프로비전된 처리량에 따라 결정됩니다.

### <a name="is-a-free-account-available"></a>무료 계정을 사용할 수 있나요?

예, 약정이나 비용 없이 시간이 제한된 계정에 등록할 수 있습니다. 등록하려면 [무료로 Azure Cosmos DB 사용](https://azure.microsoft.com/try/cosmosdb/)을 방문하거나 [Azure Cosmos DB FAQ 사용](#try-cosmos-db)을 자세히 참조하세요.

Azure를 처음 사용하는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)에 가입하면 30일간 모든 Azure 서비스를 사용해 볼 수 있습니다. Visual Studio 구독이 있다면 모든 Azure 서비스에서 사용할 수 있는 [평가판 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)에 대한 자격이 주어집니다.

[Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용하여 Azure 구독을 구입하지 않고도 무료로 로컬에서 애플리케이션을 개발하고 테스트할 수 있습니다. Azure Cosmos DB 에뮬레이터에서 애플리케이션이 작동하는 방식에 만족하는 경우 Azure Cosmos DB 계정을 클라우드에서 사용하도록 전환할 수 있습니다.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB 추가 도움말은 어떻게 구할 수 있나요?

기술 관련 질문을 하려면 다음 두 가지의 질문 및 답변 포럼 중 하나에 게시합니다.

* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow는 프로그래밍 질문에 가장 적합합니다. 질문이 [주제와 관련된](https://stackoverflow.com/help/on-topic) 것인지 확인하고 [질문에 대해 분명하고 답변할 수 있도록 가능한 한 자세하게 제공하세요](https://stackoverflow.com/help/how-to-ask).

새로운 기능을 요청하려면 [사용자 의견](https://feedback.azure.com/forums/263030-azure-cosmos-db)에서 새 요청을 만듭니다.

계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.

기타 질문은 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)으로 팀에 제출할 수 있습니다. 그러나 이것은 기술 지원 별칭이 아닙니다.

## <a id="try-cosmos-db"></a>Azure Cosmos DB 구독 사용해 보기

이제 구독이나 요금, 약정 없이 시간이 제한된 Cosmos DB를 경험할 수 있습니다. Azure Cosmos DB 구독에 등록하려면 [무료로 Azure Cosmos DB 사용](https://azure.microsoft.com/try/cosmosdb/)로 이동합니다. 이 구독은 [Azure 평가판](https://azure.microsoft.com/free/)과 별도이며 Azure 평가판 또는 Azure 유료 구독과 함께 사용할 수 있습니다.

Azure Cosmos DB 구독 사용이 사용자 ID와 연결된 다른 구독의 옆에 있는 Azure Portal에 표시됩니다.

다음 조건은 Azure Cosmos DB 구독 사용에 적용됩니다.

* 하나의 [처리량 프로 비전 된 컨테이너](./set-throughput.md#set-throughput-on-a-container) SQL, Gremlin API 및 테이블 계정의 구독 당 합니다.
* 최대 3 개의 [컬렉션을 프로 비전 된 처리량](./set-throughput.md#set-throughput-on-a-container) MongoDB 계정의 구독 당 합니다.
* 하나의 [처리량 프로 비전 된 데이터베이스](./set-throughput.md#set-throughput-on-a-database) 구독 당 합니다. 프로 비전 된 처리량 데이터베이스 내에서 컨테이너를 개수에 관계 없이 포함할 수 있습니다.
* 10GB 저장소 용량
* 전역 복제는 [Azure 지역](https://azure.microsoft.com/regions/) 미국 중부, 북유럽 및 동남 아시아에서 사용할 수 있습니다.
* 5 5k RU/s 컨테이너 수준에서 프로 비전 하는 경우의 최대 처리량입니다.
* 20 5k RU/s 데이터베이스 수준에서 프로 비전 하는 경우의 최대 처리량입니다.
* 구독에는 30 일 후 만료 및 최대 31 일 동안 총 확장할 수 있습니다.
* Azure Cosmos DB 체험 계정용으로 Azure 지원 티켓을 만들 수 없습니다. 단, 기존 지원 플랜을 사용하는 구독자에게는 지원이 제공됩니다.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB 설정

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB에는 어떻게 등록하나요?

Azure Cosmos DB는 Azure Portal에서 사용할 수 있습니다. 먼저 Azure 구독에 등록합니다. 등록 한 후 Azure 구독에 Azure Cosmos DB 계정을 추가할 수 있습니다.

### <a name="what-is-a-master-key"></a>마스터 키란 무엇인가요?

마스터 키는 계정의 모든 리소스에 액세스할 수 있는 보안 토큰입니다. 이 키가 있는 개인은 데이터베이스 계정의 모든 리소스에 대해 읽기 및 쓰기 액세스 권한을 가집니다. 마스터 키를 배포할 때 주의하세요. 주 마스터 키 및 보조 마스터 키는에서 사용할 수는 **키** 의 블레이드를 [Azure portal][azure-portal]합니다. 키에 대한 자세한 내용은 [액세스 키 보기, 복사 및 다시 생성](manage-with-cli.md#list-account-keys)을 참조하세요.

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations를 설정할 수 있는 지역은 어디인가요?

Cosmos DB를 사용할 수 있는 모든 Azure 지역에 PreferredLocations 값을 설정할 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Azure의 데이터 센터를 통해 전 세계에서 데이터를 배포하는 경우 알아야 할 점이 있나요?

Azure Cosmos DB는 [Azure 지역](https://azure.microsoft.com/regions/) 페이지에 지정된 대로 모든 Azure 지역에 존재합니다. 핵심 서비스이기 때문에 새로운 데이터 센터에는 모두 Azure Cosmos DB가 갖춰집니다.

지역을 설정할 때 Azure Cosmos DB는 국가 및 정부 클라우드를 따른다는 점을 염두에 두어야 합니다. 즉, [국가 지역](https://azure.microsoft.com/global-infrastructure/)에 계정을 만든 경우 해당 [국가 지역](https://azure.microsoft.com/global-infrastructure/) 외부로 복제할 수 없습니다. 마찬가지로 외부 계정에서 다른 국가 위치로 복제할 수 없습니다.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>컨테이너 수준 처리량 프로비전에서 데이터베이스 수준 처리량 프로비전으로 전환할 수 있나요? 또는 그 반대로 가능한가요?

컨테이너 및 데이터베이스 수준 처리량 프로비전은 별개의 제안이며 이를 전환하려면 원본에서 대상으로 데이터를 마이그레이션해야 합니다. 즉, 새 데이터베이스 또는 새 컬렉션을 만든 다음, [bulk executor library](bulk-executor-overview.md) 또는 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용하여 데이터를 마이그레이션해야 합니다.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB는 시계열 분석을 지원하나요?

예, Azure CosmosDB는 시계열 분석을 지원합니다. 여기 [시계열 패턴](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns) 샘플이 있습니다. 이 샘플에서는 시계열 데이터에 따라 집계된 보기를 빌드하기 위해 변경 피드를 사용하는 방법을 보여 줍니다. Spark 스트리밍 또는 다른 스트림 데이터 프로세서를 사용 하여 이 접근 방식을 확장할 수 있습니다.

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>SQL API에 대해 어떻게 개발을 시작하나요?

먼저 Azure 구독에 등록해야 합니다. Azure 구독에 등록하면 사용자의 Azure 구독에 SQL API 컨테이너를 추가할 수 있습니다. Azure Cosmos DB 계정 추가에 대한 지침은 [Azure Cosmos DB 데이터베이스 계정 만들기](create-sql-api-dotnet.md#create-account)를 참조하세요.

[SDK](sql-api-sdk-dotnet.md) 는 .NET, Python, Node.js, JavaScript 및 Java용으로 제공됩니다. 또한 개발자는 [RESTful HTTP API](/rest/api/cosmos-db/)를 사용하여 다양한 플랫폼 및 언어의 Azure Cosmos DB 리소스를 조작할 수 있습니다.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>유리한 출발을 위해 사전에 준비된 몇 가지 샘플에 액세스할 수 있나요?

GitHub에서 SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)및 [Python](sql-api-python-samples.md) SDK의 샘플을 사용할 수 있습니다.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API 데이터베이스에서 스키마 제약이 없는 데이터를 지원하나요?

예, SQL API를 통해 애플리케이션은 스키마 정의 또는 힌트 없이 임의의 JSON 문서를 저장할 수 있습니다. Azure Cosmos DB SQL 쿼리 인터페이스를 통해 쿼리에 즉시 데이터를 사용할 수 있습니다.

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API에서 ACID 트랜잭션을 지원하나요?

예, SQL API는 JavaScript 저장 프로시저 및 트리거로 표현되는 문서 간 트랜잭션을 지원합니다. 동시에 실행되는 다른 코드 및 사용자 요청과 "모두 분리되거나 전혀 분리되지 않으므로" 트랜잭션은 범위가 각 컨테이너 내의 단일 파티션으로 지정되고 ACID 시맨틱으로 실행됩니다. JavaScript 애플리케이션 코드의 서버 쪽 실행을 통해 예외가 발생하는 경우 전체 트랜잭션이 롤백됩니다. 

### <a name="what-is-a-container"></a>컨테이너란?

컨테이너는 문서 및 관련 JavaScript 애플리케이션 논리의 그룹입니다. 컨테이너는 처리량 및 사용한 저장소에 따라 [비용](performance-levels.md)이 결정되는 청구 가능 엔터티입니다. 컨테이너는 하나 이상의 파티션 또는 서버에 걸쳐 있을 수 있으며 크기가 거의 무제한인 저장소 또는 처리량을 처리하도록 확장할 수 있습니다.

* SQL API 및 Cosmos DB의 API for MongoDB 계정인 경우 컨테이너가 컬렉션에 매핑됩니다.
* Cassandra 및 Table API 계정의 경우 컨테이너는 테이블에 매핑됩니다.
* Gremlin API 계정의 경우 컨테이너는 Graph에 매핑됩니다.

컨테이너는 Azure Cosmos DB의 청구 엔터티이기도 합니다. 각 컨테이너는 프로비전된 처리량 및 사용된 저장소 공간에 따라 시간 단위로 요금이 청구됩니다. 자세한 내용은 [Azure Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/)을 참조하세요.

### <a name="how-do-i-create-a-database"></a>데이터베이스를 어떻게 만드나요?

[컬렉션 추가](create-sql-api-dotnet.md#create-collection-database)의 설명대로 [Azure Portal](https://portal.azure.com)을 사용하거나 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 중 하나를 사용하거나 [REST API](/rest/api/cosmos-db/)를 사용하여 데이터베이스를 만들 수 있습니다.

### <a name="how-do-i-set-up-users-and-permissions"></a>사용자 및 사용 권한을 어떻게 설정하나요?

[Cosmos DB API SDK](sql-api-sdk-dotnet.md) 또는 [REST API](/rest/api/cosmos-db/) 중 하나를 사용하여 사용자 및 사용 권한을 만들 수 있습니다.

### <a name="does-the-sql-api-support-sql"></a>SQL API는 SQL을 지원하나요?

SQL API 계정에서 지원하는 SQL 쿼리 언어는 SQL Server에서 지원되는 쿼리 기능의 향상된 하위 집합입니다. Azure Cosmos DB SQL 쿼리 언어는 JavaScript 기반 UDF(사용자 정의 함수)를 통해 다양한 계층 구조 및 관계형 연산자를 제공합니다. JSON 문법은 레이블이 지정된 노드가 있는 트리로 JSON 문서 모델링을 허용합니다. 이 내용은 Azure Cosmos DB 자동 인덱싱 기술 및 Azure Cosmos DB의 SQL 쿼리 언어 모두에 사용됩니다. SQL 문법을 사용 하 여에 대 한 내용은 참조는 [SQL 쿼리][query] 문서.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API는 SQL 집계 함수를 지원하나요?

SQL API는 `COUNT`, `MIN`, `MAX`, `AVG` 집계 함수를 통해 모든 규모의 짧은 대기 시간 집계를 지원하고 SQL 문법을 통해 `SUM`을 지원합니다. 자세한 내용은 [집계 함수](sql-query-aggregates.md)를 참조하세요.

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API는 어떻게 동시성을 제공하나요?

SQL API는 HTTP 엔터티 태그 또는 ETag를 통해 OCC(낙관적 동시성 제어)를 지원합니다. 모든 SQL API 리소스에는 ETag가 있고 ETag는 문서가 업데이트될 때마다 서버에서 설정됩니다. ETag 헤더 및 현재 값은 모든 응답 메시지에 포함됩니다. ETag는 If-Match 헤더와 함께 사용하여 서버가 리소스를 업데이트해야 하는지를 결정하도록 할 수 있습니다. If-Match 값은 검사해야 할 ETag 값입니다. ETag 값이 서버 ETag 값과 일치하는 경우 리소스가 업데이트됩니다. ETag가 최신 상태가 아닌 경우 서버에서는 "HTTP 412 사전 조건 실패" 응답 코드와 함께 작업을 거부합니다. 그러면 클라이언트는 리소스를 다시 페치하여 리소스에 대한 현재 ETag 값을 가져옵니다. 또한 If-None-Match 헤더와 함께 ETag를 사용하여 리소스를 다시 페치해야 하는지를 확인합니다.

.NET에서 낙관적 동시성을 사용하려면 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 클래스를 사용합니다. .NET 샘플은 GitHub에 있는 DocumentManagement 샘플에서 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) 를 참조하세요.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>SQL API에서 어떻게 트랜잭션을 수행하나요?

SQL API는 JavaScript 저장 프로시저 및 트리거를 통해 언어 통합 트랜잭션을 지원합니다. 스크립트 내의 모든 데이터베이스 작업은 스냅샷이 격리된 상태에서 실행됩니다. 단일 파티션 컬렉션인 경우 실행 범위는 해당 컬렉션으로 지정됩니다. 컬렉션이 분할된 경우 실행 범위는 컬렉션 내에서 파티션 키 값이 동일한 문서로 지정됩니다. 문서 버전의 스냅샷(ETags)은 트랜잭션의 시작 부분에서 찍고 스크립트가 성공한 경우에만 커밋됩니다. JavaScript에서 오류가 발생한 경우 트랜잭션이 롤백됩니다. 자세한 내용은 [Azure Cosmos DB에 대한 서버 쪽 JavaScript 프로그래밍](stored-procedures-triggers-udfs.md)을 참조하세요.

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Cosmos DB에 문서를 어떻게 일괄 삽입할 수 있나요?

다음 방법 중 하나로 Azure Cosmos DB에 문서를 대량 삽입할 수 있습니다.

* [대량 실행기 .NET 라이브러리 사용](bulk-executor-dot-net.md) 및 [대량 실행자 Java 라이브러리 사용](bulk-executor-java.md)에 설명된 대량 실행기 도구
* [Azure Cosmos DB용 데이터베이스 마이그레이션 도구](import-data.md)에 설명된 대로 데이터 마그레이션 도구를 사용합니다.
* [Azure Cosmos DB에 대한 서버 쪽 JavaScript 프로그래밍](stored-procedures-triggers-udfs.md)에 설명된 대로 저장 프로시저를 사용합니다.

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API에서 리소스 링크 캐싱을 지원하나요?

예, Azure Cosmos DB는 RESTful 서비스이므로 리소스 링크가 제한되며 캐시될 수 있습니다. SQL API 클라이언트는 문서 또는 컬렉션과 같은 모든 리소스 읽기에 대해 “If-None-Match” 헤더를 지정하고, 서버 버전이 변경된 후 로컬 복사본을 업데이트할 수 있습니다.

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API의 로컬 인스턴스를 사용할 수 있나요?

예. [Azure Cosmos DB 에뮬레이터](local-emulator.md)는 신뢰도 있는 Cosmos DB 서비스의 에뮬레이션을 제공합니다. JSON 문서 만들기 및 쿼리, 컬렉션 프로비전 및 확장, 저장 프로시저 및 트리거 실행을 비롯하여 Azure Cosmos DB와 동일한 기능을 지원합니다. Azure Cosmos DB 에뮬레이터를 사용하여 애플리케이션을 개발 및 테스트하고 Azure Cosmos DB에 대한 연결 엔드포인트에 대한 단일 구성을 변경하여 글로벌 규모로 Azure에 배포할 수 있습니다.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>이유는 포털의 데이터 탐색기에서 볼 때 반올림되는 문서에서 긴 부동 소수점 값이기 때문입니다.

이는 JavaScript의 제한 사항입니다. JavaScript는 IEEE 754에 지정 된 배정밀도 부동 소수점 형식 숫자를 사용 하 고 안전 하 게-사이의 숫자를 포함할 수 있습니다 (2<sup>53</sup> -1) 및 2<sup>53</sup>-1 (즉, 9007199254740991)만 합니다.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>개체 계층 구조에서 사용 권한이 허용되나요?

ResourceTokens를 사용한 사용 권한 만들기는 컨테이너 수준 및 해당 하위 항목에서 허용됩니다(예: 문서, 첨부 파일). 이는 데이터베이스에서 권한을 생성하려고 하거나 계정 수준이 현재 허용되지 않음을 의미합니다.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB란 무엇인가요?

Azure Cosmos DB의 MongoDB API는 MongoDB에 대 한 기존, 커뮤니티 지원 Sdk 및 드라이버를 사용 하 여 네이티브 Azure Cosmos DB 데이터베이스 엔진을 사용 하 여 쉽고 투명 하 게 통신 하 여 응용 프로그램을 유선 프로토콜 호환성 계층입니다. 개발자는 이제 Azure Cosmos DB를 활용 하는 응용 프로그램을 빌드할 기존 MongoDB 도구 체인 및 기술을 사용할 됩니다. 다중 마스터 복제, 자동 인덱싱, 백업 유지 관리를 사용 하 여 전역 배포를 포함 하는 Azure Cosmos DB의 고유한 기능을 개발자가 활용할 재정적으로 지원 서비스 수준 계약 (Sla) 등입니다.

### <a name="how-do-i-connect-to-my-database"></a>내 데이터베이스 연결하려면 어떻게 할까요?

Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결하는 가장 빠른 방법은 [Azure Portal](https://portal.azure.com)로 이동하는 것입니다. 계정으로 이동하여 왼쪽 탐색 메뉴에서 **빠른 시작**을 클릭합니다. 빠른 시작은 데이터베이스에 연결 하는 코드 조각을 얻는 가장 좋은 방법은.

Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 적용됩니다. Azure Cosmos DB 계정에는 SSL을 통한 인증 및 보안 통신이 필요하므로 TLSv1.2를 사용해야 합니다.

자세한 내용은 [Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결](connect-mongodb-account.md)을 참조하세요.

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하는 동안 처리해야 하는 추가 오류 코드가 있나요?

Azure Cosmos DB의 API for MongoDB는 일반적인 MongoDB 오류 코드 외에도 자체적인 특정 오류 코드를 갖고 있습니다.

| 오류               | 코드  | 설명  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용된 총 요청 단위 수가 컬렉션에 프로비전된 요청 단위 비율을 초과하여 제한되었습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 크기 조정하거나 다시 시도하는 것이 좋습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. <br><br>예: <em> &nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}} <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB에 사용할 수 있도록 MongoDB용 Simba 드라이버가 지원됩니까?

예, Azure Cosmos DB의 API for MongoDB를 통해 Simba의 Mongo ODBC 드라이버를 사용할 수 있습니다.

## <a id="table"></a>Table API

### <a name="how-can-i-use-the-table-api-offering"></a>Table API 제품을 사용하려면 어떻게 해야 하나요?

Azure Cosmos DB Table API의 수를 [Azure portal][azure-portal]합니다. 먼저 Azure 구독에 등록해야 합니다. 등록한 후에는 Azure 구독에 Azure Cosmos DB Table API 계정을 추가한 다음 계정에 테이블을 추가할 수 있습니다.

[Azure Cosmos DB Table API 소개](table-introduction.md)에서 지원되는 언어와의 연결된 빠른 시작을 찾을 수 있습니다.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Table API를 사용하려면 새 SDK가 필요하나요?

아니요, 기존 저장소 SDK가 여전히 작동합니다. 그러나 대부분의 경우 더 뛰어난 성능을 가진 지원을 받기 위해 최신 SDK를 가져오는 것이 좋습니다. [Azure Cosmos DB Table API 소개](table-introduction.md)에서 사용 가능한 언어 목록을 참조하세요.

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Table API가 Azure Table Storage 동작과 동일하지 않은 경우는 언제인가요?

Azure Cosmos DB Table API를 사용하여 테이블을 만들려는 Azure Table Storage에서 들어오는 사용자가 알고 있어야 하는 몇 가지 동작 차이점이 있습니다.

* Azure Cosmos DB Table API는 보장된 성능을 보장하기 위해 예약된 용량 모델을 사용합니다. 하지만 용량을 사용하지 않더라도 테이블이 생성되는 즉시 해당 용량에 대한 요금을 지불해야 합니다. Azure Table Storage에서는 사용하는 용량에 대해서만 요금을 지불합니다. Azure Table Storage가 10초 SLA를 제공하는 반면 Table API가 99번째 백분위수로 10ms 및 15ms 쓰기 SLA를 제공할 수 있는 이유는 바로 이 때문입니다. 하지만 이로 인해 요청이 없는 빈 테이블이라도 Table API 테이블을 사용하면 용량을 보장하기 위한 비용은 Azure Cosmos DB에서 제공한 SLA에서 모든 요청을 처리하는 데 사용할 수 있습니다.
* Table API에서 반환한 쿼리 결과 Azure Table storage에 있는 것 처럼 파티션 키/행 키 순서로 정렬 되지 않습니다.
* 행 키는 최대 255바이트일 수 있습니다.
* 일괄 처리에 최대 2MB를 포함할 수 있습니다.
* CORS는 현재 지원되지 않습니다.
* Azure Table storage의 테이블 이름은 대/소문자를 구분 하지 않지만 Azure Cosmos DB Table API
* 이진 필드 같은 정보 인코딩을 위한 일부 Azure Cosmos DB의 내부 형식은 현재 생각만큼 효율이 좋지는 않습니다. 따라서 데이터 크기에 예기치 않은 제한이 발생할 수 있습니다. 예를 들어, 데이터를 인코드하면 데이터 크기가 커지기 때문에 현재는 테이블 엔터티 1Meg 전체를 이진 데이터 저장에 사용할 수 없습니다.
* 엔터티 속성 이름 'D'가 현재 지원 되지 않습니다
* TableQuery TakeCount는 1000으로 제한되지 않습니다.

REST API를 기준으로 Azure Cosmos DB Table API에서 지원하지 않는 많은 엔드포인트/쿼리 옵션이 있습니다.

| Rest 메서드 | Rest 엔드포인트/쿼리 옵션 | 문서 URL | 설명 |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [테이블 서비스 속성 설정](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) 및 [테이블 서비스 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | 이 엔드포인트는 CORS 규칙, 저장소 분석 구성 및 로깅 설정을 설정하는 데 사용됩니다. CORS는 현재 지원되지 않습니다. 또한 분석 및 로깅은 Azure Storage 테이블과 다르게 Azure Cosmos DB에서 처리됩니다. |
| OPTIONS | /\<table-resource-name> | [사전 CORS 테이블 요청](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Azure Cosmos DB에서 현재 지원하지 않는 CORS의 일부입니다. |
| GET | /?restype=service@comp=stats | [테이블 서비스 통계 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | 주 데이터베이스와 보조 데이터베이스 간에 데이터를 신속하게 복제하는 방법을 제공합니다. 복제가 쓰기의 일부이기 때문에 Cosmos DB에서 필요하지 않습니다. |
| GET, PUT | /mytable?comp=acl | [테이블 ACL 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) 및 [테이블 ACL 설정](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | SAS(공유 액세스 서명)을 관리하는 데 사용되는 저장 액세스 정책을 가져오고 설정합니다. SAS가 지원되지만 다른 방법으로 설정되고 관리됩니다. |

또한 Azure Cosmos DB Table API는 ATOM이 아닌 JSON 형식만을 지원합니다.

Azure Cosmos DB가 SAS(공유 액세스 서명)을 지원하지만 새 테이블을 만드는 권한 등 관리 작업에 관련된 정책을 비롯한 특정 정책을 지원하지 않습니다.

특히 .NET SDK의 경우 Azure Cosmos DB가 현재 지원하지 않는 일부의 클래스 및 메서드가 있습니다.

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

이러한 차이점으로 인해 프로젝트에서 문제가 발생하는 경우 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)에 알려주세요.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK 또는 버그에 대한 피드백은 어떻게 제공할 수 있나요?

다음 방법 중 하나로 사용자 의견을 공유할 수 있습니다.

* [사용자 의견](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow는 프로그래밍 질문에 가장 적합합니다. 질문이 [주제와 관련된](https://stackoverflow.com/help/on-topic) 것인지 확인하고 [질문에 대해 분명하고 답변할 수 있도록 가능한 한 자세하게 제공하세요](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Table API에 연결하는 데 사용해야 하는 연결 문자열은 무엇인가요?

연결 문자열은 다음과 같습니다.

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Azure Portal의 연결 문자열 페이지에서 연결 문자열을 가져올 수 있습니다.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Table API의 .NET SDK에서 요청 옵션에 대한 구성 설정을 재정의하려면 어떻게 할까요?

일부 설정은 CreateCloudTableClient 메서드에서 처리되고 다른 설정은 클라이언트 애플리케이션의 appSettings 섹션에서 app.config를 통해 처리됩니다. 구성 설정에 대한 자세한 내용은 [Azure Cosmos DB 기능](tutorial-develop-table-dotnet.md)을 참조하세요.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>기존 Azure Table Storage SDK를 사용하는 고객에 대한 변동 사항이 있나요?

없음. 기존 Azure Table Storage SDK를 사용하는 기존 고객 또는 신규 고객에 대한 변동 사항은 없습니다.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Table API와 함께 사용하기 위해 Azure Cosmos DB에 저장된 테이블 데이터를 보려면 어떻게 해야 하나요?

Azure Portal을 사용하여 데이터를 찾을 수 있습니다. 또한 Table API 코드 또는 다음 답변에 언급된 도구를 사용할 수 있습니다.

### <a name="which-tools-work-with-the-table-api"></a>Table API에는 어떤 도구를 사용할 수 있나요?

[Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)를 사용할 수 있습니다.

이전에 지정된 형식으로 연결 문자열을 가져오는 유연성이 있는 도구에서 새 Table API를 지원할 수 있습니다. 테이블 도구 목록은 [Azure Storage Client 도구](../storage/common/storage-explorers.md) 페이지에 나와 있습니다.

### <a name="is-the-concurrency-on-operations-controlled"></a>제어되는 작업에 대한 동시성이 있나요?

예, 낙관적 동시성은 ETag 메커니즘 사용을 통해 제공됩니다.

### <a name="is-the-odata-query-model-supported-for-entities"></a>엔터티에 대해 OData 쿼리 모델이 지원되나요?

예, Table API는 OData 쿼리 및 LINQ 쿼리를 지원합니다.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>동일한 애플리케이션에서 Azure Table Storage와 Azure Cosmos DB Table API에 나란히 연결할 수 있나요?

예, 각각 연결 문자열을 통해 자체 URI를 가리키는 CloudTableClient의 두 가지 개별 인스턴스를 만들어 연결할 수 있습니다.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>기존 Azure Table Storage 애플리케이션을 이 제품으로 마이그레이션하려면 어떻게 할까요?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 및 [Azure Cosmos DB 데이터 마이그레이션 도구](import-data.md)는 모두 지원됩니다.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>예를 들어 *n*GB에서 시작한 데이터가 시간이 흘러 1TB까지 증가하게 되면 이 서비스의 경우 저장소 크기를 어떻게 확장할 수 있나요?

Azure Cosmos DB는 수평적 확장을 통해 무제한 저장소를 제공하도록 설계되어 있습니다. 이 서비스는 저장소를 모니터링하여 효율적으로 증가시킬 수 있습니다.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Table API 서비스를 모니터링하려면 어떻게 할까요?

Table API **메트릭** 창에서 요청 및 저장소 사용량을 모니터링할 수 있습니다.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>필요한 처리량은 어떻게 계산하나요?

용량 추정기를 사용하여 작업에 필요한 TableThroughput을 계산할 수 있습니다. 자세한 내용은 [요청 단위 및 데이터 저장소 예측](https://www.documentdb.com/capacityplanner)을 참조하세요. 일반적으로 엔터티를 JSON으로 나타내고 작업 수를 제공할 수 있습니다.

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
* 백 엔드의 컨테이너 또는 컨테이너 집합에서 추가 처리량이 필요합니다. 예를 들어 사용되는 처리량이 프로비전된 처리량보다 많은 경우 제한됩니다. 자세한 내용은 [Azure Cosmos DB 컨테이너에 대한 처리량 설정](set-throughput.md)을 참조하세요.

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>내 Table API 테이블의 처리량을 늘리거나 줄일 수 있나요?

예, Azure Cosmos DB 포털의 배율 창을 사용하여 처리량의 크기를 조정할 수 있습니다. 자세한 내용은 [처리량 설정](set-throughput.md)을 참조하세요.

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>새로 프로비전된 테이블에 대해 기본 TableThroughput이 설정되나요?

예, app.config를 통해 TableThroughput을 재정의하지 않고 Azure Cosmos DB에서 미리 만든 컨테이너를 사용하지 않으면 서비스에서 처리량이 400인 테이블을 만듭니다.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Table Storage 서비스의 기존 고객에 대한 가격 책정에 변동 사항이 있나요?

없음. 기존 Azure Table Storage 고객을 위한 가격에는 변동 사항이 없습니다.

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

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장할 뿐만 아니라 무제한 확장을 제공합니다. 보장된 최고의 성능을 얻기 위해서는 데이터 크기 및 인덱스가 관리 및 확장 가능해야 합니다. 파티션 키당 엔터티 또는 항목 수에 대한 10GB 한도는 뛰어난 조회 및 쿼리 성능을 제공하도록 보장합니다. Azure Storage에 대해서도 애플리케이션이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>그렇다면 Table API에도 여전히 PartitionKey 및 RowKey가 필요한가요?

예. Table API의 노출 영역이 Azure Table Storage SDK와 유사하기 때문에 파티션 키를 사용하면 데이터를 효율적으로 배포할 수 있습니다. 행 키는 파티션 내에서 고유합니다. 행 키는 반드시 존재해야 하며 표준 SDK에서와 같이 null일 수 없습니다. RowKey의 길이는 255바이트이고 PartitionKey의 길이는 1KB입니다.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Table API의 오류 메시지로는 어떤 것이 있나요?

Azure Table Storage 및 Azure Cosmos DB Table API는 동일한 SDK를 사용합니다. 따라서 대부분의 오류가 같습니다.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Tables API에서 많은 수의 테이블을 차례로 만들려고 하면 제한되는 이유는 무엇인가요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장하며, 프로비전된 시스템이기 때문에 이러한 요구 사항을 보장하기 위해 리소스를 예약해 둡니다. 따라서 빠른 테이블 만들기 속도가 감지되고 제한됩니다. 테이블 만들기 속도를 살펴보고 분당 5 미만으로 낮추는 것이 좋습니다. Table API는 프로비전된 시스템입니다. 프로비전하는 순간 비용이 발생하기 시작합니다.

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>C#.NET 개발의 경우 Microsoft.Azure.Graphs 패키지 또는 Gremlin.NET를 사용해야 하나요?

Azure Cosmos DB Gremlin API는 서비스의 기본 커넥터로 오픈 소스 드라이버를 활용합니다. 따라서 권장되는 옵션은 [Apache Tinkerpop에서 지원하는 드라이버](https://tinkerpop.apache.org/)를 사용하는 것입니다.

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>그래프 데이터베이스에서 쿼리 실행 시 RU/s는 어떻게 청구되나요?

모든 그래프 개체, 꼭짓점 및 모서리는 백 엔드에서 JSON 문서로 표시됩니다. 하나의 Gremlin 쿼리가 한 번에 하나 이상의 그래프 개체를 수정할 수 있으므로 쿼리와 연결된 비용은 쿼리에서 처리하는 개체인 모서리와 직접 관련이 있습니다. 이것은 Azure Cosmos DB가 모든 다른 API에 사용하는 것과 동일한 프로세스입니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

RU 청구는 결과 집합이 아닌 횡단의 유효 데이터 집합에 기반합니다. 예를 들어 쿼리가 결과로 단일 꼭짓점을 획득하려고 하지만 도중에 둘 이상의 다른 개체를 트래버스해야 한다면 비용은 한 개의 결과 꼭짓점을 컴퓨팅하기 위해 사용되는 모든 그래프 개체에 기반합니다.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>그래프 데이터베이스가 Azure Cosmos DB Gremlin API에서 가질 수 있는 최대 축적은 얼마입니까?

Azure Cosmos DB는 [수평 분할](partition-data.md)을 활용하여 저장소 및 처리량 요구 사항 증가를 자동으로 해결합니다. 워크로드의 최대 처리량 및 저장소 용량은 지정된 컬렉션과 연결된 파티션 수에 따라 결정됩니다. 그러나 Gremlin API 컬렉션에는 척도에서 적절한 성능 경험을 보장할 수 있도록 특정 집합의 가이드라인이 있습니다. 분할에 대한 자세한 내용 및 모범 사례는 [Azure Cosmos DB에서 분할](partition-data.md) 문서를 참조하세요.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin 드라이버를 사용하여 삽입 공격을 방지하려면 어떻게 하나요?

대부분의 네이티브 Apache Tinkerpop Gremlin 드라이버는 쿼리 실행에 대 한 매개 변수 사전을 제공 하는 옵션을 허용 합니다. 다음은 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 및 [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)에서 수행하는 방법의 예제입니다.

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>“Gremlin 쿼리 컴파일 오류: 메서드를 찾을 수 없음” 오류가 표시되는 이유는 무엇인가요?

Azure Cosmos DB Gremlin API는 Gremlin 노출 영역에 정의된 기능의 하위 집합을 구현합니다. 지원되는 단계 및 자세한 내용은 [Gremlin 지원](gremlin-support.md) 문서를 참조하세요.

가장 좋은 해결 방법은 모든 필수 Gremlin 단계가 Azure Cosmos DB에서 지원되므로 지원되는 기능을 사용하여 필요한 Gremlin 단계를 다시 작성하는 것입니다.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>“WebSocketException: 상태 코드 ‘101’을 예상했을 때 서버가 상태 코드 ‘200’을 반환함” 오류가 표시되는 이유가 무엇인가요?

이 오류는 잘못된 엔드포인트를 사용하는 경우 발생할 가능성이 높습니다. 이 오류를 생성하는 엔드포인트에는 다음과 같은 패턴이 있습니다.

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

그래프 데이터베이스에 대한 문서 엔드포인트입니다.  사용할 올바른 엔드포인트는 다음 형식의 Gremlin 엔드포인트입니다.

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" 오류가 표시되는 이유가 무엇인가요?

이 오류는 초당 할당된 요청 단위가 쿼리를 처리할 정도로 충분하지 않다는 것을 의미합니다. 이 오류는 일반적으로 모든 꼭짓점을 가져오는 쿼리를 실행할 때 표시됩니다.

```
// Query example:
g.V()
```

이 쿼리는 그래프에서 모든 꼭짓점을 가져오려고 합니다. 따라서 이 쿼리의 비용은 RU 측면에서 적어도 꼭짓점 개수와 동일하게 됩니다. 초당 RU는 이 쿼리를 해결할 수 있도록 조정되어야 합니다.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>내 Gremlin 드라이버 연결이 갑자가 끊어진 이유는 무엇인가요?

Gremlin 연결은 WebSocket 연결을 통해 이루어집니다. WebSocket 연결에 특정 TTL(Time to Live)이 없을 경우 Azure Cosmos DB Gremlin API는 30분의 비활성 후 유휴 연결을 종료합니다.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>네이티브 Gremlin 드라이버에서 흐름 API 호출을 왜 사용할 수 없나요?

흐름 API 호출은 Azure Cosmos DB Gremlin API에서 아직 지원되지 않습니다. Fluent API 호출은 Azure Cosmos DB Gremlin API에서 현재 지원되지 않는, 바이트코드 지원이라는 내부 서식 지정 기능이 필요합니다. 같은 이유로 인해 최신 Gremlin-JavaScript 드라이버도 현재 지원되지 않습니다.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>내 Gremlin 쿼리의 효율성은 어떻게 평가할 수 있나요?

**executionProfile()** 미리 보기 단계를 사용하여 쿼리 실행 계획에 대한 분석을 제공할 수 있습니다. 이 단계는 다음 예와 같이 Gremlin 쿼리 끝에 추가해야 합니다.

**쿼리 예제**

```
g.V('mary').out('knows').executionProfile()
```

**예제 출력**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

위의 프로필의 출력에서는 꼭 짓 점 개체, edge 개체 및 작업 데이터 집합의 크기를 가져오는 얼마나 많은 시간이 소요 되는지 보여 줍니다. 이는 Azure Cosmos DB 쿼리에 대한 표준 비용 측정과 관련이 있습니다.

## <a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure Cosmso DB Cassandra API에서 지 원하는 프로토콜 버전은 무엇입니까? 다른 프로토콜을 지원할 계획이 있나요?

Azure Cosmos DB용 Apache Cassandra API는 현재 CQL 버전 4를 지원합니다. 다른 프로토콜 지원에 대한 피드백이 있는 경우 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db)을 통해 알려주시거나 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 메일을 보내주세요.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>테이블에 대한 처리량을 요구 사항으로 선택하는 이유는 무엇인가요?

Azure Cosmos DB는 테이블을 만드는 위치, 포털 또는 CQL에 따라 컨테이너에 대해 기본 처리량을 설정합니다.
Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.
처리량을 탄력적으로 변경하여 애플리케이션의 계절성을 이용하고 비용을 절감할 수 있습니다.

처리량 개념은 [Azure Cosmos DB의 요청 단위](request-units.md)에 설명되어 있습니다. 테이블에 대한 처리량은 기본 실제 파티션에 동일하게 분산됩니다.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>CQL을 통해 만들 때 테이블의 기본 RU/s는 얼마인가요? 변경해야 하는 경우 어떻게 해야 하나요?

Azure Cosmos DB는 처리량을 제공하는 기준으로 초당 요청 단위(RU/s)를 사용합니다. CQL을 통해 생성된 테이블의 경우 400RU입니다. 포털에서 이 RU를 변경할 수 있습니다.

CQL

```
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

Azure Cosmos DB는 작업에 대한 상한을 사용하여 성능 및 대기 시간을 보장합니다. 이 보장은 엔진이 테넌트의 작업에 대한 거버넌스를 적용할 수 있는 경우 가능합니다. 처리량을 설정하면 플랫폼에서 이 용량을 예약하고 작업 성공을 보장하므로 보장된 처리량 및 대기 시간을 가져올 수 있습니다.
이 용량을 초과하면 용량을 초과했다는 오버로드 오류 메시지가 표시됩니다.
0x1001 오버로드됨: “요청 빈도가 높아서” 요청을 처리할 수 없습니다. 이 시점에서 어떤 작업 및 해당 볼륨으로 인해 이 문제가 발생한 것인지 확인해야 합니다. 포털의 메트릭을 통해 프로비전된 용량을 초과하는 사용 용량을 파악할 수 있습니다. 그런 다음 모든 기본 파티션에서 용량이 거의 균등하게 사용되는지 확인해야 합니다. 한 파티션에서 처리량의 대부분을 사용할 경우 워크로드가 한쪽으로 기웁니다.

개별 파티션 또는 전체 파티션이 1시간, 하루, 일주일 동안 처리량을 얼마나 사용하는지 보여주는 메트릭을 사용할 수 있습니다. 자세한 내용은 [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버깅](use-metrics.md)을 참조하세요.

진단 로그에 대한 내용은 [Azure Cosmos DB 진단 로깅](logging.md) 문서에 설명되어 있습니다.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>기본 키는 Azure Cosmos DB의 파티션 키 개념에 대응하나요?

예, 파티션 키는 엔터티를 올바른 위치에 배치하는 데 사용됩니다. Azure Cosmos DB에서는 물리적 파티션에 저장된 올바른 논리 파티션을 찾는 데 사용됩니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다. 여기서 핵심은 논리 파티션이 현재의 10GB 한도를 초과해서는 안 된다는 것입니다.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>파티션이 가득 찼음을 나타내는 "할당량 가득 참" 알림이 표시되면 어떻게 되나요?

Azure Cosmos DB는 SLA 기반 시스템으로, 대기 시간, 처리량, 가용성 및 일관성을 보장할 뿐만 아니라 무제한 확장을 제공합니다. 이 무제한 저장소는 분할을 주요 개념으로 사용하는 데이터 수평 확장을 기반으로 합니다. 분할 개념은 [Azure Cosmos DB의 파티션 및 확장](partition-data.md) 문서에 잘 설명되어 있습니다.

논리 파티션당 엔터티 또는 항목 수에 대한 10GB 제한을 준수해야 합니다. 애플리케이션이 원활하게 확장할 수 있도록 하려면 단일 파티션에 대한 모든 정보를 저장 및 쿼리하여 핫 파티션을 만들지 *않는* 것이 좋습니다. 이 오류는 하나의 파티션 키에 너무 많은 데이터가 있어서(10&nbsp;GB 초과) 데이터가 한쪽으로 기울어진 경우에만 발생합니다. 저장소 포털을 사용하여 데이터 분포를 확인할 수 있습니다. 이 오류를 해결하는 방법은 테이블을 다시 만들고, 보다 균등한 데이터 분포를 허용하는 세분화된 기본 파티션 키를 선택하는 것입니다.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Cassandra API를 수백만 또는 수십 억 개의 개별 파티션 키가 포함된 키 값 저장소로 사용할 수 있나요?

Azure Cosmos DB는 저장소를 수평으로 확장하여 무제한 데이터를 저장할 수 있습니다. 이는 처리량과는 별개입니다. 예, 올바른 기본/파티션 키를 지정하여 항상 Cassandra API로 키/값을 저장하고 검색할 수 있습니다. 이러한 개별 키는 자체 논리 파티션을 가져와서 아무런 문제 없이 실제 파티션 위에 상주합니다.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB의 Apache Cassandra API를 사용하여 테이블을 둘 이상 만들 수 있나요?

예, Apache Cassandra API를 사용하여 테이블을 둘 이상 만들 수 있습니다. 이러한 각 테이블은 처리량 및 저장소에 대한 단위로 처리됩니다.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>둘 이상의 테이블을 연속으로 만들 수 있나요?

Azure Cosmos DB는 데이터와 제어 평면 작업 모두에 대한 리소스 관리 시스템입니다. 컬렉션, 테이블 등의 컨테이너는 지정된 처리량 용량에 맞게 프로비전되는 런타임 엔터티입니다. 연속적으로 이러한 컨테이너를 만드는 작업은 올바른 활동이 아니며 제한됩니다. 테이블을 즉시 삭제/생성하는 테스트가 있다면 테이블 사이에 공간을 충분히 확보하세요.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>만들 수 있는 테이블의 최대 수는 얼마인가요?

테이블 수에 대한 물리적 한도는 없습니다. 보통 10초 또는 100초 안에 많은 테이블을 만들어야 하는 경우(총 크기가 일정하게 10TB 데이터를 초과하는 경우) [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 이메일을 보내주세요.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>키스페이스를 최대 몇 개까지 만들 수 있나요?

키스페이스는 메타데이터 컨테이너이므로 개수에 대한 물리적 한도는 없습니다. 어떤 이유로든 키스페이스가 많은 경우 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 이메일을 보내주세요.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>시작한 후에 일반 테이블에서 많은 데이터를 가져올 수 있나요?

저장소 용량은 자동으로 관리되고 더 많은 데이터를 푸시하면 증가됩니다. 따라서 노드 관리 및 프로비전 등의 작업을 수행하지 않고도 필요한 만큼의 데이터를 안심하고 가져올 수 있습니다.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Azure Cosmos DB 동작의 Apache Casssandra API를 구성하도록 yaml 파일 설정을 제공할 수 있나요?

Azure Cosmos DB의 Apache Cassandra API는 플랫폼 서비스입니다. 작업을 실행하기 위한 프로토콜 수준 호환성을 제공합니다. 관리, 모니터링 및 구성의 복잡성을 숨깁니다. 개발자/사용자인 경우 가용성, 삭제 표식, 키 캐시, 행 캐시, bloom 필터 및 다양한 기타 설정에 대해 걱정할 필요가 없습니다. Azure Cosmos DB Apache Cassandra API는 구성 및 관리 오버헤드 없이 요구하는 읽기 및 쓰기 성능을 제공하는 데 중점을 둡니다.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB의 Apache Cassandra API가 노드 추가/클러스터 상태/노드 상태 명령을 지원하나요?

Apache Cassandra API는 처리량 및 저장소에 대한 탄력성 요구에 맞는 용량 계획을 쉽게 만들 수 있는 플랫폼 서비스입니다. Azure Cosmos DB를 사용하여 필요한 처리량을 프로비전합니다. 그런 다음 노드를 추가/삭제 또는 관리하는 방법에 대한 걱정 없이 하루 종일 여러 번 확장하고 축소할 수 있습니다. 따라서 노드, 클러스터 관리 도구도 사용할 필요가 없습니다.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>키 공간을 생성하기 위한 단순/네트워크와 같은 다양한 구성 설정과 관련하여 어떤 상황이 발생하나요?

Azure Cosmos DB는 가용성 및 짧은 대기 시간을 이유로 기본적인 글로벌 배포를 제공합니다. 복제본 또는 다른 작업을 설정할 필요가 없습니다. 모든 쓰기는 항상 성능 보장을 제공 하는 동안 작성 하는 모든 지역에서 커밋된 지속적인 쿼럼입니다.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>bloom 필터, 캐싱, 읽기 복구 변경 내용, gc_grace, 압축 memtable_flush_period 등과 같은 테이블 메타데이터에 대한 다양한 설정은 어떻게 되나요?

Azure Cosmos DB는 구성 설정을 다루거나 실수로 조작하지 않고도 읽기/쓰기 성능과 처리량을 제공합니다.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>TTL(Time to Live)이 Cassandra 테이블에 지원되나요?

예, TTL이 지원됩니다.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>다양한 도구를 사용하여 노드 상태, 복제본 상태, GC 및 OS 매개 변수를 사전에 모니터링할 수 있나요? 이제 무엇을 모니터링해야 하나요?

Azure Cosmos DB는 인프라를 관리하고 모니터링할 걱정 없이 생산성을 높이는 데 도움이 되는 플랫폼 서비스입니다. 포털 메트릭에서 사용 가능한 처리량을 통해 제한에 근접했는지 확인하고 처리량을 늘리거나 낮추면 됩니다.
[SLA](monitor-accounts.md)를 모니터링하세요.
[메트릭](use-metrics.md)을 사용하고 [진단 로그](logging.md)를 사용하세요.

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB의 Apache Cassandra API와 함께 사용할 수 있는 클라이언트 SDK는 무엇인가요?

에서는 CQLv3을 사용 하는 Apache Cassandra SDK의 클라이언트 드라이버는 클라이언트 프로그램에 대 한 사용 되었습니다. 사용하는 다른 드라이버가 있거나 문제가 발생한 경우 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 이메일을 보내주세요.

### <a name="is-composite-partition-key-supported"></a>복합 파티션 키가 지원되나요?

예, 일반 구문을 사용하여 복합 파티션 키를 만들 수 있습니다.

### <a name="can-i-use-stable-loader-for-data-loading"></a>데이터 로드에 안정적인 로더를 사용할 수 있나요?

아니요, 안정적인 로더는 지원 되지 않습니다.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Azure Cosmos DB의 Cassandra API를 사용 하 여 온-프레미스 Apache Cassandra 클러스터를 연결할 수 있나요?

현재 Azure Cosmos DB에는 작업의 오버헤드 없이 클라우드 환경에 최적화된 환경이 있습니다. 페어링이 필요할 경우 해당 시나리오에 대한 설명이 포함된 이메일을 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 보내주세요. 제품 도움말 쌍 Cosomos DB의 Cassandra API에 온-프레미스/다양 한 클라우드 Cassandra 클러스터에 노력 합니다.

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API에서 전체 백업을 제공하나요?

Azure Cosmos DB는 모든 API에서 당일에 4시간 간격으로 수행된 두 개의 무료 전체 백업을 제공합니다. 따라서 백업 일정 및 기타 항목을 설정할 필요가 없습니다.
보존 기간 및 빈도를 수정하려면 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)으로 이메일을 보내거나 지원 사례를 제출해 주세요. 백업 기능에 대한 정보는 [Azure Cosmos DB로 자동 온라인 백업 및 복원](online-backup-and-restore.md) 문서에 나와 있습니다.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>지역에서 작동이 중지된 경우 Cassandra API 계정은 장애 조치(Failover)를 어떻게 처리하나요?

Azure Cosmos DB Cassandra API는 Azure Cosmos DB의 전역적으로 분산된 플랫폼을 활용합니다. 애플리케이션이 데이터 센터 가동 중지 시간을 견뎌낼 수 있도록 하려면 Azure Cosmos DB 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)에서 계정에 대해 하위 지역을 하나 이상 사용하도록 설정합니다. 포털 [다중 하위 지역 Azure Cosmos DB 계정을 사용하여 개발](high-availability.md)을 사용하여 하위 지역의 우선 순위를 설정할 수 있습니다.

계정에 대해 원하는 개수의 지역을 추가하고 장애 조치(Failover) 우선 순위를 지정하여 장애 조치할 수 있는 위치를 제어할 수 있습니다. 데이터베이스를 사용하려면 애플리케이션도 제공해야 합니다. 이렇게 하면 고객에게 가동 중지 시간이 발생하지 않습니다.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API는 기본적으로 엔터티의 모든 특성을 인덱싱하나요?

Cassandra API는 특정 특성에 선택적 인덱스를 만들기 위해 보조 인덱싱을 지원 하려고 합니다. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>새로운 Cassandra API SDK를 에뮬레이터에서 로컬로 사용할 수 있나요?

예이 지원 됩니다.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>플랫폼인 azure Cosmos DB 변경 피드 및 기타 기능 같은 기능을 많은 것 같습니다. Cassandra API에 이러한 기능이 추가될 예정인가요?

Apache Cassandra API는 Apache Cassandra와 동일한 CQL 기능을 제공합니다. 나중에 다양한 기능을 지원할 가능성을 조사할 계획입니다.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>일반 Cassandra API의 x 기능이 현재 작동하지 않는데 어디로 피드백을 보낼 수 있나요?

피드백은 [사용자 의견 피드백](https://feedback.azure.com/forums/263030-azure-cosmos-db)을 통해 보내주세요.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
