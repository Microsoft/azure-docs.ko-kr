---
title: Azure Cosmos DB 서비스 할당량
description: 다른 리소스 유형에 대 한 기본 제한 및 서비스 할당량을 Azure Cosmos DB 합니다.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: d250cc3c74f27079d633fa24c49eb11b94135ead
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200047"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 서비스 할당량

이 문서에서는 Azure Cosmos DB의 다른 리소스에 제공되는 기본 할당량에 대한 개요를 제공합니다.

## <a name="storage-and-throughput"></a>저장소 및 처리량

구독에서 Azure Cosmos 계정을 만든 후 [데이터베이스, 컨테이너 및 항목을 만들어](databases-containers-items.md)계정에서 데이터를 관리할 수 있습니다. [요청 단위 (o s/s 또는 RUs)](request-units.md)측면에서 컨테이너 수준 또는 데이터베이스 수준으로 처리량을 프로 비전 할 수 있습니다. 다음 표에서는 컨테이너/데이터베이스당 저장소 및 처리량에 대 한 제한을 보여 줍니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너 당 최대 RUs ([프로 비전 된 전용 처리량 모드](databases-containers-items.md#azure-cosmos-containers)) | 기본적으로 100만입니다. [Azure 지원 티켓](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 을 작성 하 여이를 늘릴 수 있습니다. |
| 데이터베이스당 최대 RUs ([공유 처리량 프로 비전 된 모드](databases-containers-items.md#azure-cosmos-containers)) | 기본적으로 100만입니다. [Azure 지원 티켓](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 을 작성 하 여이를 늘릴 수 있습니다. |
| (논리적) 파티션 키 당 최대 RUs | 10000 |
| (논리적) 파티션 키 당 모든 항목에 대 한 최대 저장소| 10 GB |
| 고유 (논리적) 파티션 키의 최대 수 | 제한 없음 |
| 컨테이너 당 최대 저장소 | 제한 없음 |
| 데이터베이스당 최대 저장소 | 제한 없음 |
| 계정 당 최대 첨부 파일 크기 (첨부 기능을 사용) | 2GB |
| 1gb 당 최소 RUs가 필요 합니다. | 10 r u/초 |

> [!NOTE]
> 저장소 또는 처리량을 더 많이 필요로 하는 파티션 키가 있는 작업을 관리 하기 위한 모범 사례에 대해 알아보려면 [가상 파티션 키 만들기](synthetic-partition-keys.md)를 참조 하세요.
>

Cosmos 컨테이너 (또는 공유 처리량 데이터베이스)의 최소 처리량은 400 RUs 여야 합니다. 컨테이너가 확장 될 때 지원 되는 최소 처리량은 다음 요소에 따라 달라 집니다.

* 컨테이너에 대해 설정할 수 있는 최소 처리량은 컨테이너에서 프로 비전 된 최대 처리량에 따라 달라 집니다. 예를 들어 처리량이 1만 RUs로 증가 한 경우 프로 비전 된 가장 낮은 처리량은 1000 RUs가 될 수 있습니다.
* 공유 처리량 데이터베이스의 최소 처리량은 공유 처리량 데이터베이스에서 만든 컨테이너의 총 수에 따라 달라 지 며,이는 컨테이너 당 100 RUs로 측정 됩니다. 예를 들어 공유 처리량 데이터베이스 내에 5 개의 컨테이너를 만든 경우 처리량은 500 RUs 이상 이어야 합니다.

컨테이너 또는 데이터베이스의 현재 및 최소 처리량은 Azure Portal 또는 Sdk에서 검색할 수 있습니다. 자세한 내용은 [컨테이너 및 데이터베이스에 대 한 처리량 프로 비전](set-throughput.md)을 참조 하세요. 

> [!NOTE]
> 경우에 따라 처리량을 10% 미만으로 낮출 수 있습니다. API를 사용 하 여 컨테이너 당 정확한 최소 RUs를 가져옵니다.
>

요약 하자면, 프로 비전 된 최소 제한 사항은 다음과 같습니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 컨테이너 당 최소 RUs ([프로 비전 된 전용 처리량 모드](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 데이터베이스당 최소 RUs ([공유 처리량 프로 비전 된 모드](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 공유 처리량 데이터베이스 내 컨테이너 당 최소 RUs | 100 |

Cosmos DB는 Sdk 또는 포털을 통해 컨테이너 또는 데이터베이스당 탄력적인 탄력적 크기 조정 (RUs)을 지원 합니다. 각 컨테이너는 최소 및 최대 값 사이의 크기 조정 범위 (10 ~ 100 회) 내에서 동기적으로 크기를 조정할 수 있습니다. 요청 된 처리량 값이 범위를 벗어나면 확장이 비동기식으로 수행 됩니다. 비동기 크기 조정은 컨테이너의 요청 된 처리량 및 데이터 저장소 크기에 따라 완료 하는 데 몇 분 정도 걸릴 수 있습니다.  

## <a name="control-plane-operations"></a>제어 평면 작업

Azure Portal, Azure PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 사용 하 여 [Azure Cosmos 계정을 프로 비전 하 고 관리할](how-to-manage-database-account.md) 수 있습니다. 다음 표에서는 구독 당 제한, 계정 및 작업 수를 나열 합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 구독 당 최대 데이터베이스 계정 | 기본적으로 50입니다. [Azure 지원 티켓](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 을 작성 하 여이를 늘릴 수 있습니다.|
| 최대 지역 장애 조치 (failover) 수 | 기본적으로 1/시간입니다. [Azure 지원 티켓](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 을 작성 하 여이를 늘릴 수 있습니다.|

> [!NOTE]
> 지역 장애 조치 (failover)는 단일 지역 쓰기 계정에만 적용 됩니다. 다중 지역 쓰기 계정에는 쓰기 영역을 변경할 필요가 없거나 제한이 없습니다.

Cosmos DB는 일정 한 간격으로 데이터의 백업을 자동으로 수행 합니다. 백업 보존 간격 및 windows에 대 한 자세한 내용은 [Azure Cosmos DB의 온라인 백업 및 주문형 데이터 복원](online-backup-and-restore.md)을 참조 하세요.

## <a name="per-account-limits"></a>계정 당 제한

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 데이터베이스 수 | 제한 없음 |
| 공유 처리량이 있는 데이터베이스에서 프로 비전 할 수 있는 최대 컨테이너 수 |25 |
| 최대 지역 수 | 제한 없음 (모든 Azure 지역) |

## <a name="per-container-limits"></a>컨테이너 당 제한

사용 하는 API에 따라 Azure Cosmos 컨테이너는 컬렉션, 테이블 또는 그래프를 나타낼 수 있습니다. 컨테이너는 [고유 키 제약 조건](unique-keys.md), [저장 프로시저, 트리거, udf](stored-procedures-triggers-udfs.md)및 [인덱싱 정책](how-to-manage-indexing-policy.md)에 대 한 구성을 지원 합니다. 다음 표에서는 컨테이너 내의 구성과 관련 된 제한을 나열 합니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 데이터베이스 또는 컨테이너 이름의 최대 길이 | 255 |
| 컨테이너 당 최대 저장 프로시저 수 | 100 <sup>*</sup>|
| 컨테이너 당 최대 Udf | 25 <sup>*</sup>|
| 인덱싱 정책의 최대 경로 수| 100 <sup>*</sup>|
| 컨테이너 당 고유 키의 최대 수|10 <sup>*</sup>|
| Unique key 제약 조건 당 최대 경로 수|16 <sup>*</sup>|

<sup>*</sup> Azure 지원에 문의 하 여 이러한 컨테이너 별 제한을 늘릴 수 있습니다.

## <a name="per-item-limits"></a>항목당 제한

사용 하는 API에 따라 Azure Cosmos 항목은 컬렉션의 문서, 테이블의 행 또는 그래프의 노드 또는 가장자리를 나타낼 수 있습니다. 다음 표에서는 Cosmos DB 항목당 한도를 보여 줍니다. 

| 리소스 | 기본 제한 |
| --- | --- |
| 항목의 최대 크기 | 2mb (JSON 표현의 UTF-8 길이) |
| 파티션 키 값의 최대 길이입니다. | 2048 바이트 |
| Id 값의 최대 길이입니다. | 1024 바이트 |
| 항목당 최대 속성 수 | 실제적인 제한 없음 |
| 최대 중첩 깊이 | 실제적인 제한 없음 |
| 속성 이름의 최대 길이 | 실제적인 제한 없음 |
| 속성 값의 최대 길이입니다. | 실제적인 제한 없음 |
| 문자열 속성 값의 최대 길이입니다. | 실제적인 제한 없음 |
| 숫자 속성 값의 최대 길이입니다. | IEEE754 이중 정밀도 64 비트 |

파티션 키와 id 값의 길이 제한과 2mb의 전체 크기 제한은 제외 하 고 속성 및 중첩 깊이와 같은 항목 페이로드에는 제한이 없습니다. 매우 복잡 한 항목 구조를 포함 하는 컨테이너에 대 한 인덱싱 정책을 구성 하 여 많은 소비를 줄일 수 있습니다. 실제 예제는 [Cosmos DB의 항목 모델링](how-to-model-partition-example.md) 및 큰 항목을 관리 하는 패턴을 참조 하세요.

## <a name="per-request-limits"></a>요청당 제한

Azure Cosmos DB는 컨테이너, 항목 및 데이터베이스와 같은 리소스에 대해 [CRUD 및 쿼리 작업](https://docs.microsoft.com/rest/api/cosmos-db/) 을 지원 합니다. 또한 컨테이너에서 동일한 파티션 키를 가진 여러 항목에 대 한 [트랜잭션 일괄 처리 요청](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) 을 지원 합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 단일 작업의 최대 실행 시간 (예: 저장 프로시저 실행 또는 단일 쿼리 페이지 검색)| 5초 |
| 최대 요청 크기 (예: 저장 프로시저, CRUD)| 2MB |
| 최대 응답 크기 (예: 페이지를 매긴 쿼리) | 4MB |
| 트랜잭션 일괄 처리의 최대 작업 수 | 100 |

쿼리와 같은 작업이 실행 제한 시간 또는 응답 크기 제한에 도달 하면 실행을 다시 시작 하기 위해 결과 페이지와 클라이언트에 연속 토큰이 반환 됩니다. 단일 쿼리가 페이지/연속에서 실행 될 수 있는 시간에 대 한 실질적인 제한은 없습니다.

Cosmos DB은 권한 부여에 HMAC를 사용 합니다. 컨테이너, 파티션 키 또는 항목과 같은 리소스에 대 한 세분화 된 액세스 제어를 위해 마스터 키 또는 [리소스 토큰](secure-access-to-data.md) 을 사용할 수 있습니다. 다음 표에서는 Cosmos DB 권한 부여 토큰에 대 한 제한을 나열 합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 마스터 토큰 만료 시간 | 15분  |
| 최소 리소스 토큰 만료 시간 | 10 분  |
| 최대 리소스 토큰 만료 시간 | 기본적으로 24 h입니다. [Azure 지원 티켓](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) 을 작성 하 여이를 늘릴 수 있습니다.|
| 토큰 권한 부여에 대 한 최대 클럭 오차| 15분 |

Cosmos DB는 쓰기 중 트리거의 실행을 지원 합니다. 서비스는 쓰기 작업당 최대 하나의 사전 트리거와 하나의 사후 트리거를 지원 합니다. 

## <a name="autopilot-mode-limits"></a>Autopilot 모드 제한

Autopilot 모드의 처리량 및 저장소 제한에 대해서는 [Autopilot](provision-throughput-autopilot.md#autopilot-limits) 문서를 참조 하세요.

## <a name="sql-query-limits"></a>SQL 쿼리 제한

Cosmos DB는 [SQL](how-to-sql-query.md)을 사용한 항목 쿼리를 지원 합니다. 다음 표에서는 절 수 또는 쿼리 길이와 같이 쿼리 문의 제한 사항을 설명 합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| SQL 쿼리의 최대 길이| 256 <sup>*</sup>|
| 쿼리당 최대 조인 수| 5 <sup>*</sup>|
| 쿼리당 최대 ANDs| 2000 <sup>*</sup>|
| 쿼리당 최대 ORs| 2000 <sup>*</sup>|
| 쿼리당 최대 Udf| 10 <sup>*</sup>|
| 식 당 최대 인수 수| 6000 <sup>*</sup>|
| 다각형 당 최대 점수| 4096 <sup>*</sup>|

<sup>*</sup> Azure 지원에 문의 하 여 이러한 SQL 쿼리 제한을 늘릴 수 있습니다.

## <a name="mongodb-api-specific-limits"></a>MongoDB API 특정 제한

Cosmos DB은 MongoDB에 대해 작성 된 응용 프로그램에 대 한 MongoDB 유선 프로토콜을 지원 합니다. 지원 되는 명령 및 프로토콜 버전은 [지원 되는 MongoDB 기능 및 구문](mongodb-feature-support.md)에서 찾을 수 있습니다.

다음 표에서는 MongoDB 기능 지원과 관련 된 제한을 나열 합니다. SQL (코어) API에 대해 언급 된 다른 서비스 제한은 MongoDB API에도 적용 됩니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 최대 MongoDB 쿼리 메모리 크기 | 40 M B |
| MongoDB 작업에 대 한 최대 실행 시간| 30초 |

## <a name="try-cosmos-db-free-limits"></a>무료 제한을 Cosmos DB 사용해 보세요.

다음 표에서는 평가판 [에 대 한 Azure Cosmos DB 시도](https://azure.microsoft.com/try/cosmosdb/) 의 제한을 나열 합니다.

| 리소스 | 기본 제한 |
| --- | --- |
| 평가판 기간 | 30 일 (여러 번 갱신할 수 있음) |
| 구독 당 최대 컨테이너 (SQL, Gremlin, Table API) | 1 |
| 구독 당 최대 컨테이너 (MongoDB API) | 3 |
| 컨테이너 당 최대 처리량 | 5,000 |
| 공유 처리량 데이터베이스당 최대 처리량 | 20000 |
| 계정 당 최대 총 저장소 | 10 GB |

Cosmos DB는 미국 중부, 서유럽, 동남 아시아 지역 에서만 글로벌 배포를 지원 합니다. 시도 Azure Cosmos DB 계정에 대해 Azure 지원 티켓을 만들 수 없습니다. 그러나 기존 지원 계획이 있는 구독자에 대 한 지원이 제공 됩니다.

## <a name="next-steps"></a>다음 단계

Cosmos DB의 핵심 개념에 대 한 자세한 내용은 [전역 배포](distribute-data-globally.md) 및 [분할](partitioning-overview.md) 및 [프로 비전 된 처리량](request-units.md)을 참조 하세요.

다음 요약 설명서를 통해 Azure Cosmos DB를 시작해 보세요.

* [Azure Cosmos DB SQL API 시작](create-sql-api-dotnet.md)
* [Azure Cosmos DB의 MongoDB API 시작](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API 시작](create-cassandra-dotnet.md)
* [Azure Cosmos DB Gremlin API 시작](create-graph-dotnet.md)
* [Azure Cosmos DB Table API 시작](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [무료로 Azure Cosmos DB 사용해 보기](https://azure.microsoft.com/try/cosmosdb/)
