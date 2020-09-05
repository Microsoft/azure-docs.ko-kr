---
title: 디버그 렌더링
description: 서버 쪽 디버깅 렌더링 효과 개요
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ee6945cec4c2441334e665947568743aaf29226
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013591"
---
# <a name="debug-rendering"></a>디버그 렌더링

디버그 렌더링 API는 다양한 디버깅 효과를 사용하여 서버 쪽 렌더링을 변경하기 위한 다양한 글로벌 옵션을 제공합니다.

## <a name="available-debug-rendering-effects"></a>사용 가능한 디버그 렌더링 효과

|설정                          | 영향                               |
|---------------------------------|:-------------------------------------|
|프레임 카운터                    | 텍스트 오버레이를 프레임의 왼쪽 위 모서리에 렌더링합니다. 텍스트는 렌더링이 진행됨에 따라 지속적으로 증가되는 현재 서버 쪽 프레임 ID를 표시합니다. |
|다각형 수                    | 텍스트 오버레이를 프레임의 왼쪽 위 모서리에 렌더링합니다. 텍스트는 현재 렌더링된 다각형의 크기를 보여줍니다. 이는 [서버 쪽 성능 쿼리](performance-queries.md)를 통해 쿼리된 것과 동일한 값입니다.| 
|와이어프레임                        | 활성화되면 서버에 로드된 모든 개체 형상이 와이어프레임 모드로 렌더링됩니다. 이 모드에서는 다각형의 가장자리만 래스터화됩니다. |

다음 코드에서는 이러한 디버깅 효과를 활성화합니다.

```cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Actions()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![디버그 렌더링](./media/debug-rendering.png)

> [!NOTE]
> 모든 디버그 렌더링 효과는 전체 프레임에 영향을 주는 전역 설정입니다.

## <a name="use-cases"></a>사용 사례

디버그 렌더링 API는 서비스 연결이 실제로 작동하고 올바르게 실행되는지 확인하는 것과 같은 간단한 디버깅 작업을 위한 것입니다. 텍스트 렌더링 옵션은 다운 스트리밍 비디오 프레임에 직접 영향을 줍니다. 활성화하면 새 프레임이 수신되고 비디오가 올바르게 디코딩되었는지 확인합니다.

그러나 제공된 효과는 서비스 상태에 대한 자세한 검사를 제공하지 않습니다. 이 사용 사례에는 [서버 쪽 성능 쿼리](performance-queries.md)가 권장됩니다.

## <a name="performance-considerations"></a>성능 고려 사항

* 텍스트 오버레이를 활성화하면 성능 오버헤드가 거의 발생하지 않습니다.
* 와이어프레임 모드를 사용하도록 설정하면 사소한 성능 오버헤드가 발생하지만 장면에 따라 달라질 수 있습니다. 복잡한 장면의 경우 이 모드는 프레임 속도가 60-Hz 대상 아래로 떨어질 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [서버 쪽 성능 쿼리](performance-queries.md)
