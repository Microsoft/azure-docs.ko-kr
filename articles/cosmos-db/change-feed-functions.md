---
title: Azure Functions에서 Azure Cosmos DB 변경 피드를 사용하는 방법
description: Azure Functions를 사용 하 여 Azure Cosmos DB 변경 피드에 연결 합니다. 나중에 모든 새 이벤트에서 트리거되는 사후 Azure 함수를 만들 수 있습니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 215ecc1e392f8e7051173fb6f589fb940c26f17d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872250"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB 및 Azure Functions를 사용 하는 서버 리스 이벤트 기반 아키텍처

Azure Functions는 [변경 피드에](change-feed.md)연결 하는 가장 간단한 방법을 제공 합니다. Azure Cosmos 컨테이너의 변경 피드에서 새 이벤트 마다 자동으로 트리거되는 작은 사후 Azure Functions 만들 수 있습니다.

![Cosmos DB에 대한 Azure Functions 트리거를 사용 하는 서버 리스 이벤트 기반 함수](./media/change-feed-functions/functions.png)

[Cosmos DB에 대한 Azure Functions 트리거](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)를 사용 하면 [작업자 인프라](./change-feed-processor.md)를 유지 관리할 필요 없이 [변경 피드 프로세서](./change-feed-processor.md)의 크기 조정 및 안정적인 이벤트 감지 기능을 활용할 수 있습니다. 이벤트 소싱 파이프라인의 나머지 부분에 대해 걱정 하지 않고 Azure 함수의 논리에만 집중 하세요. 트리거를 다른 [Azure Functions 바인딩과](../azure-functions/functions-triggers-bindings.md#supported-bindings)혼합할 수도 있습니다.

> [!NOTE]
> 현재 Cosmos DB에 대한 Azure Functions 트리거는 코어 (SQL) API에 대해서만 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

서버를 사용 하지 않는 이벤트 기반 흐름을 구현 하려면 다음이 필요 합니다.

* 모니터링 되는 **컨테이너**: 모니터링 되는 컨테이너는 모니터링 되는 Azure Cosmos 컨테이너 이며, 변경 피드가 생성 되는 데이터를 저장 합니다. 모든 삽입, 모니터링 되는 컨테이너에 대한 업데이트는 컨테이너의 변경 피드에 반영 됩니다.
* **임대 컨테이너**: 임대 컨테이너는 여러 서버 및 동적 서버를 사용 하지 않는 Azure 함수 인스턴스에 걸쳐 상태를 유지 관리 하 고 동적 크기 조정을 지원 합니다. 이 임대 컨테이너는 Cosmos DB에 대한 Azure Functions 트리거에서 수동으로 또는 자동으로 만들 수 있습니다. 임대 컨테이너를 자동으로 만들려면 [구성](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)에서 *CreateLeaseCollectionIfNotExists* 플래그를 설정 합니다. 분할 된 임대 컨테이너는 `/id` 파티션 키 정의를 포함 해야 합니다.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB에 대한 Azure Functions 트리거 만들기

Cosmos DB에 대한 Azure Functions 트리거를 사용 하 여 Azure 함수를 만드는 작업은 이제 모든 Azure Functions IDE 및 CLI 통합에서 지원 됩니다.

* Visual studio 사용자를 위한 [Visual Studio 확장](../azure-functions/functions-develop-vs.md) 입니다.
* Visual Studio Code 사용자를 위한 [Visual Studio Core 확장](/azure/javascript/tutorial-vscode-serverless-node-01) 입니다.
* 그리고 마지막으로 [핵심 CLI 도구](../azure-functions/functions-run-local.md#create-func) 를 통해 플랫폼 간 IDE를 독립적으로 사용할 환경을 제공 합니다.

## <a name="run-your-trigger-locally"></a>로컬에서 트리거 실행

[Azure Cosmos DB 에뮬레이터](./local-emulator.md) 를 사용 하 여 [azure 함수를 로컬로](../azure-functions/functions-develop-local.md) 실행 하 여 azure 구독 없이 서버 리스 이벤트 기반 흐름을 만들고 개발 하거나 비용을 발생 시킬 수 있습니다.

클라우드에서 라이브 시나리오를 테스트 하려는 경우에는 신용 카드나 Azure 구독이 필요 없이 [무료로 Cosmos DB을 사용해](https://azure.microsoft.com/try/cosmosdb/) 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드를 읽는 방법](read-change-feed.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
* [변경 피드 프로세서 라이브러리를 사용하는 방법](change-feed-processor.md)
* [Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md)
