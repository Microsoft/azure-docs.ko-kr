---
title: 개체 경계
description: 공간 개체 경계를 쿼리하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021853"
---
# <a name="object-bounds"></a>개체 경계

개체 경계는 [엔터티](entities.md) 및 해당 자식이 차지하는 볼륨을 나타냅니다. Azure Remote Rendering에서 개체 경계는 항상 *AABB(축 정렬 경계 상자)* 로 제공됩니다. 개체 경계는 *로컬 공간* 또는 *월드 공간* 중 하나일 수 있습니다. 어느 쪽이든 항상 축에 정렬됩니다. 즉, 범위와 볼륨이 로컬 및 월드 공간 표현 사이에 다를 수 있습니다.

## <a name="querying-object-bounds"></a>개체 경계 쿼리

[메시](meshes.md)의 로컬 AABB는 메시 리소스에서 직접 쿼리할 수 있습니다. 이러한 경계는 엔터티의 변형을 사용하여 엔터티의 로컬 공간 또는 월드 공간으로 변환될 수 있습니다.

이 방식으로 전체 개체 계층의 경계를 컴퓨팅할 수 있지만 계층 구조를 트래버스하고 각 메시의 경계를 쿼리한 다음, 수동으로 결합해야 합니다. 이 작업은 번거롭고 비효율적입니다.

더 나은 방법은 엔터티에서 `QueryLocalBoundsAsync` 또는 `QueryWorldBoundsAsync`를 호출하는 것입니다. 그런 다음, 계산이 서버로 오프로드되고 최소 지연 시간으로 반환됩니다.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>다음 단계

* [공간 쿼리](../overview/features/spatial-queries.md)
