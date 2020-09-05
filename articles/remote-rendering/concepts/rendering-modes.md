---
title: 렌더링 모드
description: 다른 서버 쪽 렌더링 모드에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d7a895f3b565fdd4ec4659045034d0200355a60
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021870"
---
# <a name="rendering-modes"></a>렌더링 모드

원격 렌더링은 **TileBasedComposition** 모드와 **DepthBasedComposition** 모드의 두 가지 주요 작업 모드를 제공 합니다. 이러한 모드는 서버의 여러 Gpu에서 작업 부하를 분산 하는 방법을 결정 합니다. 모드는 연결 시에 지정 해야 하며 런타임 중에는 변경할 수 없습니다.

두 모드 모두 장점과 기본적인 기능 제한이 있으므로 가장 적합 한 모드를 선택 하는 경우에만 사용 합니다.

## <a name="modes"></a>모드

이제 두 가지 모드에 대해 자세히 설명 합니다.

### <a name="tilebasedcomposition-mode"></a>' TileBasedComposition ' 모드

**TileBasedComposition** 모드에서 관련 된 모든 GPU는 화면에 특정 하위 사각형 (타일)을 렌더링 합니다. 주 GPU는 이미지를 클라이언트에 비디오 프레임으로 보내기 전에 타일에서 최종 이미지를 작성 합니다. 따라서 모든 Gpu는 렌더링을 위해 동일한 리소스 집합을 필요로 하므로 로드 된 자산은 단일 GPU 메모리에 맞아야 합니다.

이 모드의 렌더링 품질은 모든 GPU의 전체 geometry 집합에서 MSAA를 사용할 수 있기 때문에 **DepthBasedComposition** 모드 보다 약간 낫습니다. 다음 스크린샷에서는 두 가장자리 모두에 대해 앤티 앨리어싱을 제대로 작동 하는 것을 보여 줍니다.

![TileBasedComposition의 MSAA](./media/service-render-mode-quality.png)

또한이 모드에서 각 부분은 투명 재질로 전환 하거나 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) 를 통해 **표시** 모드로 전환할 수 있습니다.

### <a name="depthbasedcomposition-mode"></a>' DepthBasedComposition ' 모드

**DepthBasedComposition** 모드에서 관련 된 모든 GPU는 전체 화면 해상도에서 렌더링 되지만 메시의 하위 집합에만 렌더링 됩니다. 주 GPU의 최종 이미지 컴퍼지션은 해당 깊이 정보에 따라 파트가 적절히 병합 됨을 고려 합니다. 기본적으로 메모리 페이로드는 Gpu를 통해 분산 되므로 단일 GPU의 메모리에 맞지 않는 모델을 렌더링할 수 있습니다.

모든 단일 GPU는 MSAA를 사용 하 여 로컬 콘텐츠를 앤티앨리어싱 합니다. 그러나 고유 Gpu의 가장자리 사이에는 고유 하 게 별칭을 지정할 수 있습니다. 최종 이미지를 후 처리이 효과는 크지 않지만 MSAA 품질은 **TileBasedComposition** 모드 보다는 여전히 좋지 않습니다.

MSAA 아티팩트는 다음 이미지에서 설명 합니다. ![ DepthBasedComposition에서 msaa](./media/service-render-mode-balanced.png)

앤티 앨리어싱은 sculpture와 방식을 사이에서 제대로 작동 합니다. 두 파트가 동일한 GPU에서 렌더링 되기 때문입니다. 반면 방식을와 벽 사이의 가장자리는 이러한 두 부분이 고유한 Gpu에서 구성 되기 때문에 일부 별칭을 보여 줍니다.

이 모드의 가장 큰 제한은이 기 하 도형 부분은 투명 한 자료로 전환 하거나 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)에 대 한 **참조** 모드를 사용 하지 않도록 하는 것입니다. 그러나 다른 상태 재정의 기능 (윤곽선, 색 색조, ...)은 작동 합니다. 또한 변환 시간에 투명 하 게 표시 된 자료는이 모드에서 제대로 작동 합니다.

### <a name="performance"></a>성능

두 모드에 대 한 성능 특징은 사용 사례에 따라 달라 지 며, 이유 또는 일반적인 권장 사항을 제공 하기 어렵습니다. 위에서 언급 한 제한 사항 (메모리 또는 투명성/참조)에 제한이 없는 경우 두 모드를 모두 사용 하 고 다양 한 카메라 위치를 사용 하 여 성능을 모니터링 하는 것이 좋습니다.

## <a name="setting-the-render-mode"></a>렌더링 모드 설정

원격 렌더링 서버에서 사용 되는 렌더링 모드는 `AzureSession.ConnectToRuntime` 를 통해에서 지정 됩니다 `ConnectToRuntimeParams` .

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>다음 단계

* [세션](../concepts/sessions.md)
* [계층 상태 재정의 구성 요소](../overview/features/override-hierarchical-state.md)
