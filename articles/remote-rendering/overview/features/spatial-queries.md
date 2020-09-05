---
title: 공간 쿼리
description: 장면에서 공간 쿼리를 수행하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b378fe3f01e6ef1d54a89341bbac3a26b9d6b33
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013149"
---
# <a name="spatial-queries"></a>공간 쿼리

공간 쿼리는 영역에 있는 개체가 무엇인지 원격 렌더링 서비스에 질문할 수 있는 작업입니다. 공간 쿼리는 사용자가 가리키는 개체를 파악하는 등의 상호 작용을 구현하는 데 자주 사용됩니다.

모든 공간 쿼리는 서버에서 평가됩니다. 결과적으로 해당 쿼리는 비동기 작업이며 결과는 네트워크 대기 시간에 따라 지연되어 도착합니다. 모든 공간 쿼리는 네트워크 트래픽을 생성하므로 한 번에 너무 많은 작업을 수행하지 않도록 주의하세요.

## <a name="collision-meshes"></a>충돌 메시

공간 쿼리는 [Havok Physics](https://www.havok.com/products/havok-physics) 엔진을 기반으로 하며 전용 충돌 메시가 필요합니다. 기본적으로 [모델 변환](../../how-tos/conversion/model-conversion.md)은 충돌 메시를 생성합니다. 복잡한 모델에서 공간 쿼리가 필요하지 않은 경우 여러 가지 방법으로 영향을 주므로 [변환 옵션](../../how-tos/conversion/configure-model-conversion.md)에서 충돌 메시 생성을 사용하지 않도록 설정하는 것이 좋습니다.

* [모델 변환](../../how-tos/conversion/model-conversion.md)은 시간이 훨씬 더 오래 걸립니다.
* 변환된 모델 파일 크기가 훨씬 더 크므로 다운로드 속도에 영향을 줍니다.
* 런타임 로드 시간이 더 깁니다.
* 런타임 CPU 메모리 사용량이 더 높습니다.
* 모든 모델 인스턴스에 대해 약간의 런타임 성능 오버헤드가 있습니다.

## <a name="ray-casts"></a>광선 캐스트

‘광선 캐스트’는 런타임이 지정된 위치에서 시작하여 특정 방향을 가리키는 광선과 교차하는 개체를 확인하는 공간 쿼리입니다. 최적화를 위해 너무 멀리 떨어져 있는 개체를 검색하지 않도록 최대 광선 거리도 제공됩니다.

```cs
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<AzureSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = { 0, 0, 0 };
    rayCast.EndPos = { 0, 0, 1 };
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    ApiHandle<RaycastQueryAsync> castQuery = *session->Actions()->RayCastQueryAsync(rayCast);

    castQuery->Completed([](const ApiHandle<RaycastQueryAsync>& async)
        {
            std::vector<RayCastHit> hits;
            async->GetResult(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        });
}
```


다음과 같은 세 가지 적중 수집 모드가 있습니다.

* **`Closest`:** 이 모드에서는 가장 가까운 적중만 보고됩니다.
* **`Any`:** 광선이 어떤 것을 적중하지 ‘여부’만 알려고 하지만 정확히 무엇을 적중하는지 중요하지 않는 경우 이 모드를 사용하는 것이 좋습니다. 이 쿼리는 평가 비용이 매우 저렴하지만 몇 개의 애플리케이션만 포함할 수 있습니다.
* **`All`:** 이 모드에서는 광선에 따른 적중이 보고되고 거리별로 정렬됩니다. 실제로 첫 번째 적중보다 많이 필요한 경우가 아니면 이 모드를 사용하지 마세요. `MaxHits` 옵션을 사용하여 보고된 적중 횟수를 제한합니다.

필요한 경우 광선 캐스트에 고려되지 않도록 개체를 제외하기 위해 [HierarchicalStateOverrideComponent](override-hierarchical-state.md) 구성 요소를 사용할 수 있습니다.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>적중 결과

광선 캐스트 쿼리의 결과는 적중의 배열입니다. 개체가 적중되지 않은 경우 배열이 비어 있습니다.

적중의 속성은 다음과 같습니다.

* **`HitEntity`:** 적중된 [엔터티](../../concepts/entities.md)입니다.
* **`SubPartId`:** [MeshComponent](../../concepts/meshes.md)에서 적중된 ‘하위 메시’입니다 *.* `MeshComponent.UsedMaterials`로 인덱싱하고 해당 시점에 [재질](../../concepts/materials.md)을 조회하는 데 사용될 수 있습니다.
* **`HitPosition`:** 광선이 개체와 교차한 월드 공간 위치입니다.
* **`HitNormal`:** 교차 위치에 있는 메시에 일반적인 월드 공간 표면입니다.
* **`DistanceToHit`:** 광선 시작 위치에서 적중까지 거리입니다.

## <a name="next-steps"></a>다음 단계

* [개체 경계](../../concepts/object-bounds.md)
* [계층 상태 재정의](override-hierarchical-state.md)
