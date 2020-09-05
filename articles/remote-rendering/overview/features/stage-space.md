---
title: 스테이지 공간
description: 단계 공간 설정 및 사용 사례에 대해 설명 합니다.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 5809494fb8b619569316a24816a2e5d943dee6b4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013132"
---
# <a name="stage-space"></a>스테이지 공간

HoloLens 2와 같은 헤드 추적 데이터를 제공 하는 장치에서 ARR을 실행 하는 경우 head 포즈는 사용자 응용 프로그램과 서버 모두에 전송 됩니다. 에서 헤드 변환을 정의 하는 공간을 *스테이지 공간*이라고 합니다.

로컬 및 원격 콘텐츠를 맞추려면 스테이지 공간과 세계 공간이 클라이언트와 서버 모두에서 동일 하다 고 가정 합니다. 사용자가 카메라 위에 추가 변환을 추가 하기로 결정 한 경우에는 로컬 및 원격 콘텐츠를 올바르게 정렬 하기 위해 서버에도 전송 해야 합니다.

스테이지 공간을 이동 하는 일반적인 이유는 [전 세계의 잠금 도구나](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) 다른 실제 지역 고정 기술 이며, VR에서 가상 문자를 이동 하는 것입니다.

## <a name="stage-space-settings"></a>스테이지 공간 설정

> [!CAUTION]
> 실험적:이 기능은 실험적 이며 시간이 지남에 따라 변경 됩니다. 따라서 최신 버전의 클라이언트 SDK 버전으로 업데이트 하려면 코드를 업그레이드 하는 데 추가 작업이 필요할 수 있습니다. 현재 구현은 스테이지 공간 원본을 변경할 때 잠시 동안 로컬/원격 콘텐츠 정렬을 중단 합니다.
따라서 현재는 시간에 따른 약간만 약간만 변경 되는 앵커와 같이 세계 잠금의 목적 으로만 사용 하기 위한 것입니다.

추가 변환이 스테이지 공간에 적용 됨을 서버에 알리려면 위치에 의해 정의 된 원본과 세계 공간에서의 회전을 전송 해야 합니다. 이 설정은 *스테이지 공간 설정을*통해 액세스할 수 있습니다.

> [!IMPORTANT]
> [데스크톱 시뮬레이션](../../concepts/graphics-bindings.md) 에서 카메라의 전 세계 공간 위치는 사용자 응용 프로그램에서 제공 합니다. 이 경우 스테이지 공간 원본을 설정 하는 것은 카메라 변환에 이미 곱하여 건너뛰어야 합니다.

```cs
void ChangeStageSpace(AzureSession session)
{
    StageSpaceSettings settings = session.Actions.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<AzureSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Actions()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity 단계 공간 스크립트

Unity 통합에서는 카메라의 부모 GameObject에 추가할 수 있는 **StageSpace** 라는 스크립트를 제공 합니다. 이 스크립트는 일단 있으면 스테이지 공간 원본 설정을 처리 합니다.

## <a name="next-steps"></a>다음 단계

* [그래픽 바인딩](../../concepts/graphics-bindings.md)
* [후기 단계 다시 프로젝션](late-stage-reprojection.md)
