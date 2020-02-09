---
title: Azure Cosmos DB Azure Cosmos DB에서 변경 피드 액세스
description: 이 문서에서는 Azure Cosmos DB Azure Cosmos DB에서 변경 피드를 읽고 액세스할 수 있는 다양한 옵션에 대해 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688126"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Cosmos DB 변경 피드 읽기

다음 옵션 중 하나를 사용하여 Azure Cosmos DB 변경 피드로 작업할 수 있습니다.

* Azure Functions 사용
* 변경 피드 프로세서 라이브러리 사용
* Azure Cosmos DB SQL API SDK 사용

## <a name="using-azure-functions"></a>Azure Functions 사용

Azure Functions는 가장 간단하고 권장되는 옵션입니다. Cosmos DB에 대한 Azure Functions 트리거를 만들 때 연결할 컨테이너를 선택할 수 있으며, 컨테이너가 변경 될 때마다 Azure Function이 트리거됩니다. 트리거는 Azure Functions 포털, Azure Cosmos DB 포털을 사용하거나 SDK를 사용하여 프로그래밍 방식으로 만들 수 있습니다. Visual Studio 및 VS Code는 Azure Functions를 작성하는 지원을 제공하며 플랫폼 간 개발을 위해 Azure Functions CLI를 사용할 수도 있습니다. 데스크톱에서 코드를 작성하고 디버그한 다음, 클릭 한 번으로 함수를 배포할 수 있습니다. 자세한 내용은 [Azure Functions를 사용한 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md) 및 [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)) 문서를 참조하세요.

## <a name="using-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 사용

변경 피드 프로세서 라이브러리는 복잡성을 숨기고 변경 피드의 전체 컨트롤을 제공합니다. 라이브러리는 관찰자 패턴을 따릅니다. 여기서 처리 함수는 라이브러리를 통해 호출됩니다. 높은 처리량 변경 피드가 있는 경우 변경 피드를 읽을 여러 클라이언트를 인스턴스화할 수 있습니다. 변경 피드 프로세서 라이브러리를 사용하므로 이 논리를 구현할 필요 없이 다른 클라이언트 간에 로드가 자동으로 분할됩니다. 모든 복잡성은 라이브러리가 처리합니다. 고유의 부하 분산 장치를 원하는 경우 변경 피드를 처리하는 사용자 지정 파티션 전략에 대한 `IPartitionLoadBalancingStrategy`를 구현하면 됩니다. 자세한 내용은 [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)을 참조하세요.

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK 사용

SDK를 사용하면 변경 피드의 하위 수준 컨트롤을 가져올 수 있습니다. 검사점을 관리 하 고 특정 논리적 파티션 키 등에 액세스할 수 있습니다. 여러 판독기가 있는 경우 `ChangeFeedOptions`를 사용 하 여 읽기 부하를 다른 스레드나 다른 클라이언트에 배포할 수 있습니다.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra 및 MongoDB에 대한 Api의 변경 피드

변경 피드 기능은 MongoDB API에서 변경 스트림으로 표시 되 고 Cassandra API 조건자를 사용 하 여 쿼리 됩니다. MongoDB API에 대한 구현 세부 정보에 대한 자세한 내용은 [MongoDB에 대한 AZURE COSMOS DB api의 변경 스트림](mongodb-change-streams.md)을 참조 하세요.

기본 Apache Cassandra는 cdc (변경 데이터 캡처)를 제공 합니다 .이 메커니즘은 보관을 위해 특정 테이블에 플래그를 지정 하 고 CDC 로그의 구성 가능한 크기에 도달 하면 해당 테이블에 대한 쓰기를 거부 하는 메커니즘입니다. Cassandra에 대한 Azure Cosmos DB API의 변경 피드 기능은 CQL을 통해 조건자를 사용 하 여 변경 내용을 쿼리 하는 기능을 향상 시킵니다. 구현 세부 정보에 대한 자세한 내용은 [Cassandra에 대한 AZURE COSMOS DB API에서 변경 피드](cassandra-change-feed.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
