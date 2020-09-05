---
title: Azure Cosmos DB Table API 소개
description: Azure Table API를 사용하여 대기 시간이 짧은 엄청난 양의 키-값 데이터를 저장하고 쿼리하는 데 Azure Cosmos DB를 사용하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 6cf9dddbfa6eb6f754c529981307f8ee80cfe7d1
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376998"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 소개: 테이블 API

[Azure Cosmos DB](introduction.md)는 Azure Table Storage용으로 작성되었으며 다음과 같은 프리미엄 기능이 필요한 애플리케이션에 대한 테이블 API를 제공합니다.

* [턴키 전역 배포](distribute-data-globally.md)
* 전 세계적인 [전용 처리량](partition-data.md)
* 99번째 백분위수에서 1자리 밀리초 대기 시간
* 보장된 고가용성
* 자동 보조 인덱싱.

Azure Table Storage에 대해 작성된 애플리케이션은 코드를 변경하지 않고 테이블 API를 사용하여 Azure Cosmos DB로 마이그레이션할 수 있으며 프리미엄 기능을 활용할 수 있습니다. 테이블 API에는 .NET, Java, Python 및 Node.js에 사용할 수 있는 클라이언트 SDK가 있습니다.

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)이 유지 관리 모드에 있으며 곧 사용 중지될 예정됩니다. 새 .NET Standard 라이브러리 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)로 업그레이드하여 Table API에서 지원하는 최신 기능을 계속 유지합니다.

## <a name="table-offerings"></a>Table 제품
현재 Azure Table Storage를 사용하는 경우 Azure Cosmos DB 테이블 API로 이동하면 다음과 같은 이점이 있습니다.

| 기능 | Azure Table Storage | Azure Cosmos DB 테이블 API |
| --- | --- | --- |
| 대기 시간 | 빠르지만 대기 시간에 대한 상한이 없습니다. | 읽기, 쓰기 지연 시간(ms) 한 자릿수 보장(규모와 장소에 관계없이 모든 요청의 99%에 대해 읽기, 쓰기 지연 시간이 10ms를 넘지 않도록 지원). |
| 처리량 | 가변 처리량 모델입니다. 테이블의 확장 제한은 20,000개 작업/초입니다. | SLA를 통해 지원하는 [테이블당 예약된 전용 처리량](request-units.md)으로 확장성이 뛰어납니다. 계정에는 처리량에 대한 상한이 없으며, 테이블당 1,000만 개 이상 작업/초를 지원합니다. |
| 글로벌 분포 | 고가용성을 위해 선택적인 읽기 가능한 보조 읽기 하위 지역 하나가 있는 단일 지역입니다. | 하나에서 임의의 여러 지역까지 [턴키 글로벌 배포](distribute-data-globally.md)를 수행할 수 있습니다. 전 세계 어디에서나 [자동 및 수동 장애 조치](high-availability.md)를 지원합니다. 모든 지역에서 쓰기 작업을 허용하는 다중 마스터 기능. |
| 인덱싱 | PartitionKey 및 RowKey에 대한 기본 인덱스만 제공. 보조 인덱스가 없습니다. | 인덱스 관리 없이 기본적으로 모든 속성에 대해 자동 및 전체 인덱싱을 수행할 수 있습니다. |
| 쿼리 | 쿼리 실행 시 기본 키에 대한 인덱스를 사용하고 그렇지 않은 경우 검색합니다. | 쿼리는 빠른 쿼리 시간을 위해 속성에 대해 자동 인덱싱을 활용할 수 있습니다. |
| 일관성 | 주 지역 내에서 강력하게 유지되며, 최종적으로는 보조 지역 내에서 유지됩니다. | 애플리케이션 요구 사항에 따라 가용성, 대기 시간, 처리량 및 일관성을 절충할 수 있는 [잘 정의된 5가지 일관성 수준](consistency-levels.md)이 적용됩니다. |
| 가격 책정 | 스토리지 최적화 | 처리량 최적화 |
| SLA | 복제 전략에 따라 99.9% ~ 99.99%의 가용성을 제공합니다. | 99.999% 읽기 가용성, 단일 지역 계정에 대한 99.99% 쓰기 가용성 및 다중 지역 계정에 대한 99.999% 쓰기 가용성을 제공합니다. 가용성, 대기 시간, 처리량 및 일관성을 포괄하는 [종합적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). |

## <a name="get-started"></a>시작하기

[Azure Portal](https://portal.azure.com)에서 새 Azure Cosmos DB 계정을 만듭니다. 그런 다음 [.NET을 사용하여 테이블 API에 대한 빠른 시작](create-table-dotnet.md)을 시작합니다. 

> [!IMPORTANT]
> 미리 보기 도중에 Table API 계정을 만든 경우 일반 공급되는 Table API SDK와 작동하는 [새 Table API 계정](create-table-dotnet.md#create-a-database-account)을 만드세요.
>

## <a name="next-steps"></a>다음 단계

다음은 시작하기 위한 몇 가지 지침입니다.
* [테이블 API를 사용하여 .NET 애플리케이션 빌드](create-table-dotnet.md)
* [.NET의 테이블 API(미리 보기)를 사용하여 개발](tutorial-develop-table-dotnet.md)
* [테이블 API를 사용하여 테이블 데이터 쿼리](tutorial-query-table.md)
* [테이블 API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 테이블 .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB 테이블 .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB 테이블 Java SDK](table-sdk-java.md)
* [Azure Cosmos DB 테이블 Node.js SDK](table-sdk-nodejs.md)
* [Python용 Azure Cosmos DB 테이블 SDK](table-sdk-python.md)
