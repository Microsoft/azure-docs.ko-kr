---
title: Cosmos DB에 대해 독립적인 Azure Functions 트리거를 여러 개 만듭니다.
description: 여러 개의 독립적인 Cosmos DB용 Azure Functions 트리거를 구성하여 이벤트 기반 아키텍처를 만드는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: dce10fb85ac181bb06aef0058768bef659462a5a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019983"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>여러 개의 Cosmos DB용 Azure Functions 트리거 만들기

이 문서에서는 병렬로 작동하고 변경 내용에 독립적으로 반응하는 여러 개의 Cosmos DB용 Azure Functions 트리거를 구성하는 방법에 대해 설명합니다.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Cosmos DB용 Azure Functions 트리거와 함께 작동하고 임대 컨테이너를 공유하는 서버리스 이벤트 기반 함수" border="false":::

## <a name="event-based-architecture-requirements"></a>이벤트 기반 아키텍처 요구 사항

[Azure Functions](../azure-functions/functions-overview.md)를 사용하여 서버리스 아키텍처를 구축하는 경우 장기 실행 함수 대신 함께 작동하는 작은 함수 세트를 만드는 것이 [좋습니다](../azure-functions/functions-best-practices.md#avoid-long-running-functions).

[Cosmos DB용 Azure Functions 트리거](./change-feed-functions.md)를 사용하여 이벤트 기반 서버리스 흐름을 작성하면 특정 [Azure Cosmos 컨테이너](./databases-containers-items.md#azure-cosmos-containers)에 새 이벤트가 있을 때마다 여러 작업을 수행하려는 시나리오와 충돌합니다. 트리거하려는 작업이 서로 독립적인 경우 가장 적절한 해결 방법은 동일한 Azure Cosmos 컨테이너에서 변경 내용을 수행하려는 **작업당 하나의 Cosmos DB용 Azure Functions 트리거를 만들어** 동일한 Azure Cosmos 컨테이너에서 모든 변경 내용을 수신 대기하는 것입니다.

## <a name="optimizing-containers-for-multiple-triggers"></a>여러 트리거에 대한 컨테이너 최적화

Cosmos DB용 Azure Functions 트리거의 *요구 사항*을 고려할 때, 상태를 저장하는 두 번째 컨테이너(*임대 컨테이너*라고도 함)가 필요합니다. 이는 각 Azure Function마다 별도의 임대 컨테이너가 필요하다는 것을 의미할까요?

여기에는 다음 두 가지 옵션이 있습니다.

* **함수 당 하나의 임대 컨테이너**만들기:이 접근 방식은 [공유 처리량 데이터베이스](./set-throughput.md#set-throughput-on-a-database)를 사용 하지 않는 한 추가 비용으로 변환 될 수 있습니다. 컨테이너 수준의 최소 처리량은 400개 [요청 단위](./request-units.md)이며, 임대 컨테이너의 경우 진행률 검사점을 확인하고 상태를 유지하는 데만 사용됩니다.
* **하나의 임대 컨테이너를 포함 하 고** 모든 함수를 공유 합니다 .이 두 번째 옵션을 사용 하면 여러 Azure Functions가 프로 비전 된 처리량을 공유 하 고 사용할 수 있으므로 컨테이너에서 프로 비전 된 요청 단위를 더 효율적으로 사용할 수 있습니다.

이 문서의 목표는 두 번째 옵션을 수행하도록 안내하는 것입니다.

## <a name="configuring-a-shared-leases-container"></a>공유 임대 컨테이너 구성

공유 임대 컨테이너를 구성하기 위해 트리거에 필요한 유일한 추가 구성은 C#을 사용하는 경우 `LeaseCollectionPrefix` [특성](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations)을 추가하거나, JavaScript를 사용하는 경우 `leaseCollectionPrefix` [특성](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)을 추가하는 것입니다. 특성 값은 해당 특정 트리거의 논리적 설명자여야 합니다.

예를 들어 세 개의 트리거, 즉 이메일을 보내는 트리거, 구체화된 보기를 만들기 위해 집계를 수행하는 트리거 및 변경 내용을 다른 스토리지에 보내는 트리거가 있는 경우, 나중에 분석하기 위해 첫 번째 트리거에는 "emails"인 `LeaseCollectionPrefix`를, 두 번째 트리거에는 "materialized"를, 세 번째 트리거에는 "analytics"를 할당할 수 있습니다.

중요한 부분은 세 개의 트리거에서 모두 **동일한 임대 컨테이너 구성(계정, 데이터베이스 및 컨테이너 이름)을 사용할 수 있다**는 것입니다.

C#에서 `LeaseCollectionPrefix` 특성을 사용하는 매우 간단한 코드 샘플은 다음과 같습니다.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

그리고 JavaScript의 경우 `leaseCollectionPrefix` 특성을 사용하여 `function.json` 파일에 구성을 적용할 수 있습니다.

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> 공유 임대 컨테이너에 프로비저닝된 요청 단위를 항상 모니터링하세요. 이를 공유하는 각 트리거에서 처리량 평균 사용량을 늘리므로 이 트리거를 사용하는 Azure Function의 수를 늘릴 때 프로비저닝된 처리량을 늘려야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Cosmos DB용 Azure Functions 트리거](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)에 대한 전체 구성을 확인합니다.
* 모든 언어에 대한 [샘플의 확장 목록](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)을 확인합니다.
* 자세한 샘플은 Azure Cosmos DB 및 Azure Functions [GitHub 리포지토리](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios)를 사용하여 서버리스 레시피를 방문합니다.
