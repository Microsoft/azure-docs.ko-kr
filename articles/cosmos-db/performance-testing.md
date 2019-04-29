---
title: Azure Cosmos DB를 사용한 성능 및 규모 테스트
description: Azure Cosmos DB를 사용하여 규모 및 성능 테스트를 수행하는 방법을 알아봅니다. 그런 다음, 고성능 애플리케이션 시나리오에 대한 Azure Cosmos DB의 기능을 평가할 수 있습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926569"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용한 성능 및 규모 테스트

성능 및 규모 테스트는 애플리케이션 개발의 핵심 단계입니다. 많은 애플리케이션에서 데이터베이스 계층은 전체 성능과 확장성에 큰 영향을 미칩니다. 따라서 성능 테스트의 주요 구성 요소가 됩니다. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)는 탄력적 확장 및 예측 가능한 성능을 목적으로 구축되었습니다. 이러한 기능은 고성능 데이터베이스 계층이 필요한 애플리케이션에 잘 맞습니다. 

이 문서는 Azure Cosmos DB 워크로드에 대해 성능 테스트 세트를 구현하는 개발자가 참조할 수 있습니다. 또한 고성능 애플리케이션 시나리오에 대해 Azure Cosmos DB를 평가하는 데도 사용할 수 있습니다. 또한 이 문서는 데이터베이스의 격리된 성능 테스트에 중점을 두지만 프로덕션 애플리케이션에 대한 모범 사례도 제공합니다.

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다. 

* Azure Cosmos DB의 성능 테스트를 위한 샘플 .NET 클라이언트 애플리케이션은 어디에서 찾을 수 있나요? 
* 클라이언트 애플리케이션에서 Azure Cosmos DB를 사용하여 높은 처리량 수준을 달성하려면 어떻게 하나요?

코드를 시작하려면 [Azure Cosmos DB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)에서 프로젝트를 다운로드하세요. 

> [!NOTE]
> 이 애플리케이션의 목표는 적은 수의 클라이언트 머신을 사용하여 Azure Cosmos DB에서 최상의 성능을 얻는 방법을 제시하는 것입니다. 이 샘플의 목표는 Azure Cosmos DB의 최대 처리 용량을 달성하는 것이 아닙니다(제한 없이 확장 가능).
> 
> 

Azure Cosmos DB의 성능 향상을 위한 클라이언트 쪽 구성 옵션에 대한 자세한 내용은 [Azure Cosmos DB 성능 팁](performance-tips.md)을 참조하세요.

## <a name="run-the-performance-testing-application"></a>성능 테스트 애플리케이션 실행
가장 빠른 시작 방법은 다음 단계에 설명된 대로 아래의 .NET 샘플을 컴파일하고 실행하는 것입니다. 소스 코드를 검토하고 자체 클라이언트 애플리케이션에 대해 비슷한 구성을 구현할 수도 있습니다.

**1단계:** [Azure Cosmos DB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)에서 프로젝트를 다운로드하거나 GitHub 리포지토리를 분기합니다.

**2단계:** App.config에서 EndpointUrl, AuthorizationKey, CollectionThroughput 및 DocumentTemplate(옵션)에 대한 설정을 수정합니다.

> [!NOTE]
> 높은 처리량의 컬렉션을 프로비전하기 전에 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하여 컬렉션당 비용을 추정합니다. Azure Cosmos DB는 시간 단위로 저장소 및 처리량을 독립적으로 청구합니다. 테스트 후 Azure Cosmos DB 컬렉션을 삭제하거나 처리량을 낮추어 비용을 절감할 수 있습니다.
> 
> 

**3단계:** 명령줄에서 콘솔 앱을 컴파일하고 실행합니다. 다음과 유사한 출력이 표시됩니다.

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**4단계(필요한 경우):** 도구에서 보고된 처리량(RU/s)은 컬렉션 또는 컬렉션 세트의 프로비전된 처리량과 같거나 많아야 합니다. 그렇지 않은 경우 DegreeOfParallelism을 조금씩 늘리면 제한에 도달하는 데 도움이 될 수 있습니다. 클라이언트 앱의 처리량이 안정화될 경우 추가 클라이언트 컴퓨터에서 앱의 여러 인스턴스를 시작합니다. 이 단계에 대해 도움이 필요한 경우 askcosmosdb@microsoft.com에 전자 메일을 보내거나 [Azure Portal](https://portal.azure.com)에서 지원 티켓을 작성하세요.

실행 중인 앱이 있는 경우 다양한 [인덱싱 정책](index-policy.md) 및 [일관성 수준](consistency-levels.md)을 시도하면서 처리량 및 대기 시간에 미치는 영향을 이해할 수 있습니다. 소스 코드를 검토하고 자체 테스트 제품군 또는 프로덕션 애플리케이션에 대해 비슷한 구성을 구현할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 .NET 콘솔 앱을 사용하여 Azure Cosmos DB로 성능 및 규모 테스트를 수행하는 방법을 살펴보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB 성능 테스트 샘플](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Azure Cosmos DB 성능 향상을 위한 클라이언트 구성 옵션](performance-tips.md)
* [Azure Cosmos DB의 서버 쪽 분할](partition-data.md)


