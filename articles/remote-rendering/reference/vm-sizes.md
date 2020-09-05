---
title: 서버 크기
description: 할당할 수 있는 고유 서버 크기를 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 8843f24f27f8973ad99989f743d1b3fae568679e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997152"
---
# <a name="server-sizes"></a>서버 크기

Azure 원격 렌더링은 및의 두 서버 구성에서 사용할 수 있습니다. `Standard` `Premium`

## <a name="polygon-limits"></a>다각형 제한

크기 서버를 사용 하는 원격 렌더링 `Standard` 의 최대 장면 크기는 2000만입니다. 크기를 사용 하는 원격 렌더링 `Premium` 은 최대값을 적용 하지 않지만 콘텐츠가 서비스의 렌더링 기능을 초과 하는 경우 성능이 저하 될 수 있습니다.

' 표준 ' 서버 크기의 렌더러에서는이 제한에 도달 하면 렌더링을 바둑판 배경에 전환 합니다.

![체크 무늬](media/checkerboard.png)

## <a name="specify-the-server-size"></a>서버 크기 지정

필요한 서버 구성 유형은 렌더링 세션 초기화 시간에 지정 해야 합니다. 실행 중인 세션 내에서 변경할 수 없습니다. 다음 코드 예에서는 서버 크기를 지정 해야 하는 위치를 보여 줍니다.

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

[예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)의 경우 원하는 서버 크기를 파일 내에 지정 해야 합니다 `arrconfig.json` .

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>렌더러에서 다각형 수를 평가 하는 방법

제한 테스트에 대해 고려 되는 다각형 수는 실제로 렌더러에 전달 되는 다각형 수입니다. 이 기 하 도형은 일반적으로 인스턴스화된 모든 모델의 합계 이지만 예외도 있습니다. 다음 기 하 도형은 **포함 되지 않습니다**.
* 뷰가 완전히 외부에 있는 로드 된 모델 인스턴스입니다.
* [계층 구조 상태 재정의 구성 요소](../overview/features/override-hierarchical-state.md)를 사용 하 여 표시 되지 않도록 전환 된 모델 또는 모델 파트입니다.

따라서 `standard` 모든 단일 모델에 대 한 한도에 가까운 다각형 수를 사용 하 여 여러 모델을 로드 하는 크기를 대상으로 하는 응용 프로그램을 작성할 수 있습니다. 응용 프로그램에서 한 번에 하나의 모델만 표시 하는 경우에는 바둑판이 트리거되지 않습니다.

### <a name="how-to-determine-the-number-of-polygons"></a>다각형 수를 확인 하는 방법

구성 크기의 예산 제한에 영향을 주는 모델 또는 장면의 다각형 수를 결정 하는 방법에는 다음 두 가지가 있습니다 `standard` .
* 모델 변환 쪽에서 [변환 출력 json 파일](../how-tos/conversion/get-information.md)을 검색 하 고 `numFaces` [ *inputstatistics* 섹션](../how-tos/conversion/get-information.md#the-inputstatistics-section) 의 항목을 확인 합니다.
* 응용 프로그램에서 동적 콘텐츠를 처리 하는 경우 렌더링 된 다각형의 수를 런타임에 동적으로 쿼리할 수 있습니다. [성능 평가 쿼리](../overview/features/performance-queries.md#performance-assessment-queries) 를 사용 하 여 `polygonsRendered` 구조체의 멤버를 확인 `FrameStatistics` 합니다. `polygonsRendered` `bad` 렌더러가 다각형 제한에 도달 하면 필드가로 설정 됩니다. 이 비동기 쿼리 후 사용자 작업을 수행할 수 있도록 바둑판 배경은 항상 약간의 지연 시간으로 페이드 인 됩니다. 사용자 작업은 인스턴스에서 모델 인스턴스를 숨기 거 나 삭제할 수 있습니다.

## <a name="pricing"></a>가격 책정

각 구성 유형에 대 한 가격 책정에 대 한 자세한 내용은 [원격 렌더링 가격 책정](https://azure.microsoft.com/pricing/details/remote-rendering) 페이지를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
* [모델 변환](../how-tos/conversion/model-conversion.md)

