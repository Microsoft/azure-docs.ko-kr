---
title: Azure Cosmos DB Azure Cosmos DB에서 변경 피드 액세스
description: 이 문서에서는 Azure Cosmos DB Azure Cosmos DB에서 변경 피드를 읽고 액세스할 수 있는 다양한 옵션에 대해 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 459ed4b6f16d3cfe5bb792be7f063a1253a3006e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60927803"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Cosmos DB 변경 피드 읽기

다음 옵션 중 하나를 사용하여 Azure Cosmos DB 변경 피드로 작업할 수 있습니다.

* Azure Functions 사용
* 변경 피드 프로세서 라이브러리 사용
* Azure Cosmos DB SQL API SDK 사용

## <a name="using-azure-functions"></a>Azure Functions 사용

Azure Functions는 가장 간단하고 권장되는 옵션입니다. Azure Functions 애플리케이션에서 Azure Cosmos DB 트리거를 만들 때 연결할 컨테이너를 선택하면 컨테이너가 변경될 때마다 Azure Function가 트리거됩니다. 트리거는 Azure Functions 포털, Azure Cosmos DB 포털을 사용하거나 SDK를 사용하여 프로그래밍 방식으로 만들 수 있습니다. Visual Studio 및 VS Code는 Azure Functions를 작성하는 지원을 제공하며 플랫폼 간 개발을 위해 Azure Functions CLI를 사용할 수도 있습니다. 데스크톱에서 코드를 작성하고 디버그한 다음, 클릭 한 번으로 함수를 배포할 수 있습니다. 자세한 내용은 [Azure Functions를 사용한 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md) 및 [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)) 문서를 참조하세요.

## <a name="using-the-change-feed-processor-library"></a>변경 피드 프로세서 라이브러리 사용

변경 피드 프로세서 라이브러리는 복잡성을 숨기고 변경 피드의 전체 컨트롤을 제공합니다. 라이브러리는 관찰자 패턴을 따릅니다. 여기서 처리 함수는 라이브러리를 통해 호출됩니다. 높은 처리량 변경 피드가 있는 경우 변경 피드를 읽을 여러 클라이언트를 인스턴스화할 수 있습니다. 변경 피드 프로세서 라이브러리를 사용하므로 이 논리를 구현할 필요 없이 다른 클라이언트 간에 로드가 자동으로 분할됩니다. 모든 복잡성은 라이브러리가 처리합니다. 고유의 부하 분산 장치를 원하는 경우 변경 피드를 처리하는 사용자 지정 파티션 전략에 대한 `IPartitionLoadBalancingStrategy`를 구현하면 됩니다. 자세한 내용은 [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)을 참조하세요.

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK 사용

SDK를 사용하면 변경 피드의 하위 수준 컨트롤을 가져올 수 있습니다. 검사점을 관리하고 특정 논리 파티션 키에 액세스할 수 있습니다. 복수의 reader가 있는 경우 `ChangeFeedOptions`를 사용하여 읽기 로드를 다른 스레드 또는 다른 클라이언트로 분산할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
