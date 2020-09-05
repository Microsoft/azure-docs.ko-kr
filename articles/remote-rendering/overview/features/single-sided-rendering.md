---
title: 단일 면 렌더링
description: 단일 면 렌더링 설정 및 사용 사례를 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f1778d1f37217a5f65624be2fcc6c58c68002931
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013285"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: 렌더링

대부분 렌더러는 [뒷면 선별](https://en.wikipedia.org/wiki/Back-face_culling)을 사용하여 성능을 개선합니다. 그러나 [평면 잘라내기](cut-planes.md)를 통해 메시가 잘려 있으면 종종 사용자에게 삼각형의 뒷면이 보입니다. 해당 삼각형이 선별되지 않으면 결과가 설득력 있는 것으로 보이지 않습니다.

이 문제를 안정적으로 방지하는 방법은 ‘양면’ 삼각형을 렌더링하는 것입니다. 뒷면 선별을 사용하지 않으면 성능에 영향을 미치므로 Azure Remote Rendering은 기본적으로 잘린 평면과 교차하는 메시의 양면 렌더링으로만 전환합니다.

* :::no-loc text="single-sided"::: 렌더링* 설정을 사용 하 여이 동작을 사용자 지정할 수 있습니다.

> [!CAUTION]
> :::no-loc text="single-sided":::렌더링 설정은 실험적 기능입니다. 이후에 다시 제거될 수 있습니다. 실제로 애플리케이션에서 중요한 문제를 해결하는 경우가 아니면 기본 설정을 변경하지 마세요.

## <a name="prerequisites"></a>사전 요구 사항

:::no-loc text="single-sided":::렌더링 설정은 옵션을로 설정 하 여 [변환](../../how-tos/conversion/configure-model-conversion.md) 된 메시에만 적용 `opaqueMaterialDefaultSidedness` `SingleSided` 됩니다. 기본적으로 이 옵션은 `DoubleSided`로 설정됩니다.

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: 렌더링 설정

다음과 같은 세 가지 모드가 있습니다.

**보통:** 이 모드에서 메시는 변환될 때 항상 렌더링됩니다. 즉, `opaqueMaterialDefaultSidedness`를 `SingleSided`로 설정하여 변환된 메시는 잘린 평면과 교차하는 경우에도 항상 뒷면 선별을 사용하여 렌더링됩니다.

**DynamicDoubleSiding:** 이 모드에서 잘린 평면은 메시와 교차하는 경우 자동으로 양면 렌더링으로 전환됩니다. 이 모드가 기본 모드입니다.

**AlwaysDoubleSided:** 항상 모든 단면 기하 도형을 양면으로 렌더링합니다. 이 모드는 대부분 표시 되므로 및 렌더링 간의 성능 영향을 쉽게 비교할 수 있습니다 :::no-loc text="single-sided"::: :::no-loc text="double-sided"::: .

:::no-loc text="single-sided":::렌더링 설정을 변경 하는 작업은 다음과 같이 수행할 수 있습니다.

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Actions()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>다음 단계

* [평면 잘라내기](cut-planes.md)
* [모델 변환 구성](../../how-tos/conversion/configure-model-conversion.md)
