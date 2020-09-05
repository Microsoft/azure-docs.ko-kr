---
title: HoloLens에 Unity 샘플 배포
description: Unity 샘플을 HoloLens로 가져오는 방법을 보여주는 빠른 시작
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 3eec935d0a25f9510cd9a2f6e00b7ac22756e697
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796802"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>빠른 시작: HoloLens에 Unity 샘플 배포

이 빠른 시작에서는 Unity용 빠른 시작 샘플 앱을 HoloLens 2에 배포하고 실행하는 방법을 다룹니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
>* HoloLens용 빠른 시작 샘플 앱 빌드
>* 디바이스에 샘플 배포
>* 디바이스에서 샘플 실행

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에서는 [빠른 시작: Unity를 사용하여 모델 렌더링](render-model.md)의 샘플 프로젝트를 배포합니다.

자격 증명이 장면과 함께 제대로 저장되어 있고 Unity 편집기 내에서 세션에 연결할 수 있는지 확인합니다.

## <a name="build-the-sample-project"></a>샘플 프로젝트 빌드

1. *파일 > 빌드 설정*을 엽니다.
1. *플랫폼*을 **유니버설 Windows 플랫폼**으로 변경합니다.
1. *대상 디바이스*를 **HoloLens**로 설정합니다.
1. *아키텍처*를 **ARM64**로 설정합니다.
1. *빌드 형식*을 **D3D 프로젝트**\로 설정합니다.
    ![빌드 설정](./media/unity-build-settings.png)
1. **플랫폼으로 전환**을 선택합니다.
1. **빌드**(또는 '빌드 및 실행')를 누르면 솔루션을 저장할 폴더를 선택하라는 메시지가 표시됩니다.
1. Visual Studio에서 생성된 **Quickstart.sln**을 엽니다.
1. 구성을 **Release** 및 **ARM64**로 변경합니다.
1. 디버거 모드를 **원격 머신**\으로 전환합니다.
    ![솔루션 구성](media/unity-deploy-config.png)
1. 솔루션 빌드
1. '빠른 시작' 프로젝트의 경우 *속성 > 디버깅*으로 이동합니다.
    1. 구성 *릴리스*가 활성 상태인지 확인합니다.
    1. *실행할 디버거*를 **원격 머신**으로 설정합니다.
    1. *머신 이름*을 **HoleLens의 IP**로 변경합니다.

## <a name="launch-the-sample-project"></a>샘플 프로젝트 시작

1. USB 케이블을 사용하여 HoloLens를 PC에 연결합니다.
1. Visual Studio에서 디버거를 시작합니다(F5). 그러면 앱이 자동으로 디바이스에 배포됩니다.

샘플 앱이 시작된 다음, 새 세션을 시작해야 합니다. 잠시 후 세션이 준비되고 원격으로 렌더링되는 모델이 앞에 표시됩니다.
나중에 샘플을 다시 시작하려면 HoloLens 지금 시작 메뉴에서도 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작에서는 사용자 지정 모델을 변환하는 방법을 살펴보겠습니다.

> [!div class="nextstepaction"]
> [빠른 시작: 렌더링을 위해 모델 변환](convert-model.md)
