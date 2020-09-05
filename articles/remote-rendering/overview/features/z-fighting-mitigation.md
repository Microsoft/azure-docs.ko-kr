---
title: Z-fighting 완화
description: z-fighting 아티팩트를 완화하는 기술을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: dbe3af30963791891f0ceee4e18c34e078c0ac7a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013183"
---
# <a name="z-fighting-mitigation"></a>Z-fighting 완화

두 표면이 겹치면 어느 표면을 다른 표면 위에 렌더링해야 하는지 명확하지 않습니다. 결과는 픽셀 마다 다르므로 카메라 뷰 종속 아티팩트가 발생 합니다. 따라서 카메라나 메시가 움직이면 이러한 패턴이 눈에 띄게 깜박입니다. 이 아티팩트를 *z-fighting*이라고 합니다. AR 및 VR 응용 프로그램의 경우 머리에 탑재된 장치가 자연스럽게 항상 움직이기 때문에 문제가 심화됩니다. 시청자의 불편을 방지하기 위해 z-fighting 완화 기능이 Azure Remote Rendering에서 제공됩니다.

## <a name="z-fighting-mitigation-modes"></a>z-fighting 완화 모드

|상황                        | 결과                               |
|---------------------------------|:-------------------------------------|
|일반 z-fighting               |![z-fighting](./media/zfighting-0.png)|
|z-fighting 완화 사용    |![z-fighting](./media/zfighting-1.png)|
|바둑판 무늬 강조 표시 사용|![z-fighting](./media/zfighting-2.png)|

다음 코드는 z-fighting 완화를 사용합니다.

```cs
void EnableZFightingMitigation(AzureSession session, bool highlight)
{
    ZFightingMitigationSettings settings = session.Actions.ZFightingMitigationSettings;

    // enabling z-fighting mitigation
    settings.Enabled = true;

    // enabling checkerboard highlighting of z-fighting potential
    settings.Highlighting = highlight;
}
```

```cpp
void EnableZFightingMitigation(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<ZFightingMitigationSettings> settings = session->Actions()->GetZFightingMitigationSettings();

    // enabling z-fighting mitigation
    settings->SetEnabled(true);

    // enabling checkerboard highlighting of z-fighting potential
    settings->SetHighlighting(highlight);
}
```


> [!NOTE]
> z-fighting 완화는 렌더링된 모든 메시에 영향을 주는 전역 설정입니다.

## <a name="reasons-for-z-fighting"></a>z-fighting 발생 이유

z-fighting은 주로 다음과 같은 두 가지 이유로 발생합니다.

1. 표면이 카메라에서 매우 멀리 떨어진 경우. 깊이 값의 정밀도가 떨어지고 값을 구분할 수 없음
1. 메시의 표면이 물리적으로 겹치는 경우

첫 번째 문제는 항상 발생할 수 있으며 해결하기 어렵습니다. 응용 프로그램에 이 문제가 발생하는 경우에는 *가까운 평면* 거리와 *먼 평면* 거리의 비율이 실제만큼 낮아야 합니다. 예를 들어 거리가 0.01인 가까운 평면과 거리가 1000인 먼 평면에는 가까운 평면이 0.1에 있고 먼 평면이 20에 있는 것보다 훨씬 일찍 이 문제가 발생합니다.

두 번째 문제는 잘못 작성된 콘텐츠에 대한 지표입니다. 실제 환경에서는 2개의 개체가 동시에 같은 위치에 있을 수 없습니다. 응용 프로그램에 따라 사용자는 겹치는 표면이 있는지 여부와 해당 위치를 알고자 할 수 있습니다. 예를 들어 실제 건축의 기초가 되는 건물의 CAD 장면에는 물리적으로 불가능한 표면 교차가 포함되지 않아야 합니다. 시각적 검사를 허용하기 위해 강조 표시 모드를 사용할 수 있습니다. 이 모드는 잠재적 z-fighting을 애니메이션 바둑판 표시 패턴으로 표시합니다.

## <a name="limitations"></a>제한 사항

제공된 z-fighting 완화는 최적의 방법입니다. 모든 z-fighting아 제거된다는 보장은 없습니다. 또한 한 표면을 다른 표면 위에 자동으로 사용합니다. 따라서 서로 너무 가까운 여러 표면이 있는 경우 "잘못된" 표면이 맨 위에 있는 것일 수 있습니다. 일반적인 문제는 텍스트 및 기타 효과가 표면에 적용되는 경우입니다. z-fighting 완화를 사용하도록 설정하면 이러한 세부 정보가 쉽게 사라질 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

* z-fighting을 사용하면 성능 오버헤드가 거의 발생하지 않습니다.
* 추가로 z-fighting 오버레이를 사용하면 사소한 성능 오버헤드가 발생하지만 장면에 따라 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)
