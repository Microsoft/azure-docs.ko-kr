---
title: Azure Cosmos DB에서 그래프 데이터를 쿼리하는 방법
description: Gremlin 쿼리를 사용하여 Azure Cosmos DB에서 그래프 데이터를 쿼리하는 방법 알아보기
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 12/03/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: f699f6ace6989cec7c3050e7c0bd050f3b84c3c6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019490"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>자습서: Gremlin을 사용하여 Azure Cosmos DB Gremlin API 쿼리

Azure Cosmos DB [Gremlin API](graph-introduction.md)는 [Gremlin](https://github.com/tinkerpop/gremlin/wiki) 쿼리를 지원합니다. 이 문서에서는 샘플 문서와 쿼리를 제공하여 시작합니다. Gremlin에 대해서는 [Gremlin 지원](gremlin-support.md) 문서에서 자세히 참조할 수 있습니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Gremlin을 사용한 데이터 쿼리

## <a name="prerequisites"></a>사전 요구 사항

이러한 쿼리가 작동하려면 Azure Cosmos DB 계정이 있어야 하며 컨테이너에 그래프 데이터가 있어야 합니다. 이들 중 하나라도 없는가요? 그러면 [5분 퀵 스타트](create-graph-dotnet.md) 또는 [개발자 자습서](tutorial-query-graph.md)를 수행하여 계정을 만들고 데이터베이스를 채워 놓으세요. [Gremlin 콘솔](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) 또는 즐겨찾는 Gremlin 드라이버를 사용하여 다음 쿼리를 실행할 수 있습니다.

## <a name="count-vertices-in-the-graph"></a>그래프의 꼭짓점 계산

다음 코드 조각에서는 그래프의 꼭짓점 수를 계산하는 방법을 보여 줍니다.

```
g.V().count()
```

## <a name="filters"></a>필터

Gremlin의 `has` 및 `hasLabel` 단계를 사용하여 필터링을 수행하고, `and`, `or` 및 `not`으로 필터를 결합하여 더 복잡한 필터를 만들 수 있습니다. Azure Cosmos DB는 빠른 쿼리를 위해 꼭짓점과 각도 내의 모든 속성에 대해 스키마 독립적 인덱싱을 제공합니다.

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>도법

`values` 단계를 사용하여 쿼리 결과에 특정 속성을 프로젝션할 수 있습니다.

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>관련 가장자리 및 꼭짓점 찾기

지금까지 모든 데이터베이스에서 작동하는 쿼리 연산자만 보았습니다. 관련 가장자리와 꼭짓점으로 이동해야 할 때 그래프는 이 순회 작업에 빠르고 효율적입니다. Thomas의 친구들을 모두 찾아 보겠습니다. 이렇게 하려면 Gremlin의 `outE` 단계를 사용하여 Thomas의 모든 가장자리를 찾은(out-edge) 다음, Gremlin의 `inV` 단계를 사용하여 이러한 가장자리에서 꼭짓점으로 이동합니다(in-vertex).

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

다음 쿼리에서는 `outE`과 `inV`를 두 번 호출함으로써 홉을 두 번 수행하여 Thomas의 "친구의 친구"를 모두 찾습니다. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Gremlin을 사용하여 필터 식을 혼합하고, `loop` 단계를 사용하여 루핑을 수행하고, `choose` 단계를 사용하여 조건부 탐색을 구현하는 등 더욱 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. [Gremlin 지원](gremlin-support.md)에서 수행할 수 있는 작업에 대해 자세히 알아보세요!

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 그래프를 사용하여 쿼리하는 방법 

이제 개념 섹션으로 진행하여 Cosmos DB에 대한 자세한 정보를 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [글로벌 분포](distribute-data-globally.md) 

