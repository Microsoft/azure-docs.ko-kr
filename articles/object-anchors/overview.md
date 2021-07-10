---
title: Azure Object Anchors 개요
description: Azure Object Anchors를 사용하여 실제 세계에서 개체를 검색하는 방법에 대해 알아봅니다.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 84a69c46e9d7587ed87d8288fca5e7d8ef25f546
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111983589"
---
# <a name="azure-object-anchors-overview"></a>Azure Object Anchors 개요

Azure Object Anchors를 사용하면 애플리케이션이 3D 모델을 사용하여 실제 세계의 개체를 검색하고 해당 6DoF 포즈를 예측할 수 있습니다. 6DoF(6자유도) 포즈는 3D 모델과 실제 개체 간의 회전 및 좌표 이동으로 정의됩니다.

Azure Object Anchors는 모델 변환을 위한 서비스와 HoloLens용 런타임 클라이언트 SDK로 구성됩니다. 이 서비스는 3D 개체 모델을 받아 Azure Object Anchors 모델을 출력합니다. Azure Object Anchors 모델은 런타임 SDK와 함께 사용되어 HoloLens 애플리케이션이 개체 모델을 로드하고 실제 세계에서 해당 모델의 인스턴스를 검색하고 추적하는 기능을 지원합니다.

:::image type="content" source="./media/object-anchors-overview.jpg" alt-text="작동 중인 Azure Object Anchors":::

## <a name="examples"></a>예제

Azure Object Anchors가 설정된 몇 가지 예제 사용 사례는 다음과 같습니다.

- **학습**. 표식을 배치하거나 홀로그램 맞춤을 수동으로 조정하지 않고도 작업자를 위한 Mixed Reality 학습 환경을 만듭니다. 자동화된 검색 및 추적을 사용하여 Mixed Reality 학습 환경을 보강하려는 경우 모델을 Azure Object Anchors 서비스로 수집하면 표식 없는 환경에 한 걸음 다가설 수 있습니다.

- **작업 지침**. Mixed Reality를 사용하는 경우 작업자가 일련의 작업을 수행할 지침 제공이 크게 간소화될 수 있습니다. 공장에 설치된 기계든 팀 탕비실에 있는 커피메이커든 물리적 개체에 디지털 지침 및 모범 사례를 결합하여 작업 세트를 완료하는 난도를 크게 낮출 수 있습니다. 이러한 환경을 트리거하는 데는 일반적으로 특정 형태의 표식 또는 수동 맞춤이 필요하지만 Azure Object Anchors를 사용하면 현재 작업에 관련된 개체를 자동으로 검색하는 환경을 만들 수 있습니다. 그런 다음 표식 또는 수동 맞춤 없이 Mixed Reality 지침을 원활하게 진행합니다.

- **자산 찾기**. 실제 공간에 일부 개체의 3D 모델이 이미 있는 경우 Azure Object Anchors를 사용하여 실제 환경에서 해당 개체의 인스턴스를 찾고 추적할 수 있습니다.

## <a name="usage-flow"></a>사용 흐름

먼저 Azure Object Anchors 변환 서비스에 3D 자산을 업로드합니다. 빠른 시작 중 하나의 단계를 수행할 수 있습니다.

  - [Unity HoloLens](quickstarts/get-started-unity-hololens.md)
  - [MRTK를 사용한 Unity HoloLens](quickstarts/get-started-unity-hololens-mrtk.md)
  - [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)

그러면 서비스에서 자산을 Azure Object Anchors 모델로 변환합니다. 원할 경우 [메시를 시각화](visualize-converted-model.md)할 수 있도록 변환된 모델을 다운로드합니다. 마지막으로 [Unity](/dotnet/api/Microsoft.Azure.ObjectAnchors) 또는 [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt)용 런타임 SDK가 있는 HoloLens 디바이스에 모델을 복사합니다. 이제 원본 모델과 일치하는 물리적 개체를 검색할 수 있습니다.

:::image type="content" source="./media/object-anchors-flow.png" alt-text="사용 흐름":::

## <a name="asset-requirements"></a>자산 요구 사항

자산의 각 크기는 1m~10m 범위어야 하며 파일 크기는 150MB 미만이어야 합니다.

현재 지원되는 자산 형식은 `fbx`, `ply`, `obj`, `glb` 및 `gltf`입니다.

## <a name="next-steps"></a>다음 단계

다음 섹션에서는 Azure Object Anchors를 사용하여 앱 빌드 및 사용을 시작하는 방법에 대한 정보를 제공합니다.

> [!div class="nextstepaction"]
> [모델 수집](quickstarts/get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Unity HoloLens](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [MRTK를 사용한 Unity HoloLens](quickstarts/get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](quickstarts/get-started-hololens-directx.md)
