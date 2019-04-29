---
title: Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법
description: Azure Functions에서 Azure Cosmos DB 변경 피드 사용
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112024"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB 및 Azure Functions를 사용 하 여 서버 리스 이벤트 기반 아키텍처

Azure Functions에 연결 하는 가장 간단한 방법은 제공 된 [변경 피드](change-feed.md)합니다. 각 새 Azure Cosmos 컨테이너의 변경 피드 이벤트에 자동으로 트리거될 수 있는 작은 사후 Azure Functions를 만들 수 있습니다.

![Azure Cosmos DB 트리거를 사용 하 여 작업 하는 서버 리스 이벤트 기반 함수](./media/change-feed-functions/functions.png)

사용 하 여는 [Azure Cosmos DB 트리거](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)를 활용할 수 있습니다 합니다 [변경 피드 프로세서](./change-feed-processor.md)의 크기 조정 및 신뢰할 수 있는 이벤트 검색 기능은 모든 유지 관리 하지 않아도 [작업자 인프라](./change-feed-processor.md#implementing-the-change-feed-processor-library)합니다. 이벤트 소싱 파이프라인의 나머지 부분에 대 한 걱정 없이 Azure Function의 논리에만 집중 합니다. 모든 다른 트리거를 혼합할 수도 있습니다 [Azure Functions 바인딩](../azure-functions/functions-triggers-bindings.md#supported-bindings)합니다.

> [!NOTE]
> 현재, Azure Cosmos DB 트리거를 사용 하 여 코어 (SQL) API만 사용 하기 위해 지원 됩니다.

## <a name="requirements"></a>요구 사항

서버 리스 이벤트 기반 흐름을 구현 하려면 다음을 수행 해야 합니다.

* **모니터링 되는 컨테이너**: 모니터링 되는 컨테이너를 모니터링 하는 Azure Cosmos 컨테이너 이며 변경 피드 생성 되는 데이터를 저장 합니다. 모든 삽입 및 변경 내용 (예: CRUD) 모니터링 되는 컨테이너는 컨테이너의 변경 피드에 반영 됩니다.
* **컨테이너 임대**: 컨테이너 임대 여러 상태를 유지 관리 및 동적 서버 리스 Azure Function 인스턴스 및 동적 크기 조정을 사용 하도록 설정 합니다. 이 컨테이너의 임대 수동 또는 자동으로 Azure Cosmos DB Trigger.To에서 자동으로 만들어집니다 컨테이너 임대를 만들고, 설정 합니다 *CreateLeaseCollectionIfNotExists* 플래그는 [구성](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). 분할 된 임대 컨테이너는 필요 하기는 `/id` 파티션 키 정의 합니다.

## <a name="create-your-azure-cosmos-db-trigger"></a>Azure Cosmos DB 트리거 만들기

Azure Cosmos DB 트리거를 사용 하 여 Azure Function 만들기는 이제 모든 Azure Functions IDE 및 CLI 통합에서 지원 됩니다.

* [Visual Studio 확장](../azure-functions/functions-develop-vs.md) Visual Studio 사용자에 대 한 합니다.
* [Visual Studio 핵심 확장](https://code.visualstudio.com/tutorials/functions-extension/create-function) Visual Studio Code 사용자에 대 한 합니다.
* 마지막 [Core CLI 도구](../azure-functions/functions-run-local.md#create-func) 플랫폼 간 IDE 알 수 없는 환경에 대 한 합니다.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Azure Cosmos DB 트리거를 로컬로 실행

실행할 수 있습니다 하 [Azure 함수를 로컬로](../azure-functions/functions-develop-local.md) 사용 하 여는 [Azure Cosmos DB 에뮬레이터](./local-emulator.md) 을 만들고 Azure 구독이 나 모든 비용 없이 서버 리스 이벤트 기반 흐름을 개발 합니다.

클라우드에서 실시간 시나리오를 테스트 하려는 경우 [Cosmos DB를 무료로 사용해 보기](https://azure.microsoft.com/try/cosmosdb/) 없이 모든 신용 카드나 Azure 구독이 필요 합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에 피드 변경에 자세히 알아보려면 계속 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드를 읽는 방법](read-change-feed.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
* [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)
* [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)
