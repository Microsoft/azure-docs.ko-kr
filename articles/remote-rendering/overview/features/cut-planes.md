---
title: 평면 잘라내기
description: 절단면의 정의와 사용 방법에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b92bfad99c854f75c945121d352a7122d8c6db89
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011602"
---
# <a name="cut-planes"></a>평면 잘라내기

*절단면*은 가상 평면 한쪽에 픽셀을 자르는 시각적 기능으로, [메시](../../concepts/meshes.md) 안쪽에 표시합니다.
아래 이미지는 효과를 보여줍니다. 왼쪽은 원본 메쉬를 표시하고 오른쪽은 메시 내부를 볼 수 있습니다.

![평면 잘라내기](./media/cutplane-1.png)

## <a name="limitations"></a>제한 사항

* 당분간 Azure Remote Rendering은 **최대 8개의 활성 절단면**을 지원합니다. 더 많은 절단면 구성 요소를 만들 수 있지만 동시에 사용하도록 설정하려고 하면 활성화가 무시됩니다. 장면에 영향을 주는 구성 요소를 전환하려면 먼저 다른 평면을 비활성화합니다.
* 각 절단면은 원격으로 렌더링되는 모든 개체에 영향을 줍니다. 현재 특정 개체 또는 메시 파트를 제외할 방법이 없습니다.
* 절단면은 순전히 시각적 기능으로, [공간 쿼리](spatial-queries.md) 결과에 영향을 주지 않습니다. 잘린 메시로 캐스트하려는 경우 광선의 시작점을 절단면에 맞게 조정할 수 있습니다. 이러한 방식으로 광선은 표시되는 부분만 적중될 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

각 활성 절단면은 렌더링하는 동안 약간의 비용이 발생합니다. 필요하지 않은 경우에는 절단면을 비활성화하거나 삭제합니다.

## <a name="cutplanecomponent"></a>CutPlaneComponent

*CutPlaneComponent*를 만들어 장면에 절단면을 추가합니다. 평면의 위치와 방향은 구성 요소의 소유자 [엔터티](../../concepts/entities.md)에 의해 결정됩니다.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>CutPlaneComponent 속성

다음 속성은 절단면 구성 요소에서 노출됩니다.

* `Enabled`: 구성 요소를 사용하지 않도록 설정하여 절단면을 일시적으로 해제할 수 있습니다. 비활성화된 절단면은 렌더링 오버헤드를 초래하지 않으며 글로벌 평면 잘라내기 제한에도 해당되지 않습니다.

* `Normal`: 보통 평면으로 사용되는 방향(+X,-X,+Y,-Y,+Z,-Z)을 지정합니다. 이 방향은 소유자 엔터티의 방향을 기준으로 합니다. 정확한 배치를 위해 소유자 엔터티를 이동하고 회전합니다.

* `FadeColor` 및 `FadeLength`:

  *FadeColor*의 알파 값이 0이 아닌 경우 절단면에 가까운 픽셀은 FadeColor의 RGB 부분으로 페이드됩니다. 알파 채널의 강도에 따라 페이드 색을 완전히 페이드할지 아니면 부분적으로만 페이드할지 여부를 결정합니다. *FadeLength*는 이 페이드가 발생하는 거리를 정의합니다.

## <a name="next-steps"></a>다음 단계

* [단면 렌더링](single-sided-rendering.md)
* [공간 쿼리](spatial-queries.md)
