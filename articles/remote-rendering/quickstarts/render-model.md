---
title: Unity를 사용하여 모델 렌더링
description: 사용자에게 모델 렌더링 단계를 안내하는 빠른 시작
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b5865f2fd76c1159f7f72633362a96335af8a059
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509368"
---
# <a name="quickstart-render-a-model-with-unity"></a>빠른 시작: Unity를 사용하여 모델 렌더링

이 빠른 시작에서는 ARR(Azure Remote Rendering) 서비스를 사용하여 기본 제공 모델을 원격으로 렌더링하는 Unity 샘플을 실행하는 방법을 설명합니다.

ARR API 자체 또는 새 Unity 프로젝트를 설정하는 방법은 자세히 다루지 않습니다. 이에 대한 토픽은 [자습서: 원격으로 렌더링된 모델 보기](../tutorials/unity/view-remote-models/view-remote-models.md)에서 다룹니다.

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
>
>* 로컬 개발 환경 설정
>* Unity용 ARR 빠른 시작 샘플 앱을 가져와서 빌드
>* ARR 빠른 시작 샘플 앱에서 모델 렌더링

## <a name="prerequisites"></a>필수 구성 요소

Azure Remote Rendering 서비스에 액세스하려면 먼저 [계정을 만들어야 합니다](../how-tos/create-an-account.md).

다음 소프트웨어가 설치되어 있어야 합니다.

* Windows SDK 10.0.18362.0[(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 최신 버전의 Visual Studio 2019[(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [혼합 현실용 Visual Studio 도구](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). 특히, 다음 *워크로드* 설치는 필수입니다.
  * **C++를 사용한 데스크톱 개발**
  * **UWP(유니버설 Windows 플랫폼) 개발**
* GIT([다운로드](https://git-scm.com/downloads))
* Unity 2019.3.1[(다운로드)](https://unity3d.com/get-unity/download)
  * 다음 모듈을 Unity에 설치합니다.
    * **UWP** - 유니버설 Windows 플랫폼 빌드 지원
    * **IL2CPP** - Windows 빌드 지원(IL2CPP)

## <a name="clone-the-sample-app"></a>샘플 앱 복제

명령 프롬프트를 열고(Windows 시작 메뉴에서 `cmd` 입력) ARR 샘플 프로젝트를 저장할 디렉터리로 변경합니다.

다음 명령을 실행합니다.

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

마지막 명령은 Azure Remote Rendering에 사용할 다양한 샘플 프로젝트가 들어 있는 하위 디렉터리를 ARR 디렉터리에 만듭니다.

Unity용 빠른 시작 샘플 앱은 하위 디렉터리 *Unity/Quickstart*에 있습니다.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Unity 샘플 프로젝트를 사용하여 모델 렌더링

Unity Hub를 열고 *ARR\azure-remote-rendering\Unity\Quickstart* 폴더인 샘플 프로젝트를 추가합니다.
프로젝트를 엽니다. 필요한 경우 Unity에서 프로젝트를 설치된 버전으로 업그레이드할 수 있도록 허용합니다.

여기서 렌더링하는 기본 모델은 [기본 제공 샘플 모델](../samples/sample-model.md)입니다. [다음 빠른 시작](convert-model.md)의 ARR 변환 서비스를 사용하여 사용자 지정 모델을 변환하는 방법을 보여드리겠습니다.

### <a name="enter-your-account-info"></a>계정 정보 입력

1. Unity 자산 브라우저에서 *Scenes* 폴더로 이동하여 **빠른 시작** 장면을 엽니다.
1. *계층*에서 **RemoteRendering** 게임 개체를 선택합니다.
1. *검사기*에 [계정 자격 증명](../how-tos/create-an-account.md)을 입력합니다. 아직 계정이 없는 경우 [계정을 만듭니다](../how-tos/create-an-account.md).

![ARR 계정 정보](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal은 계정의 도메인을 *mixedreality.azure.com*으로만 표시합니다. 이것만으로는 성공적으로 연결하기에 부족합니다.
> **AccountDomain**을 `<region>.mixedreality.azure.com`으로 설정합니다. 여기서 `<region>`은 [사용 가능한 가까운 지역 중 하나](../reference/regions.md)입니다.

나중에 이 프로젝트를 HoloLens에 배포하고 해당 디바이스에서 Remote Rendering 서비스에 연결할 것입니다. 디바이스에서 자격 증명을 입력할 수 있는 쉬운 방법이 없기 때문에 이 빠른 시작 샘플에서는 **Unity 장면에 자격 증명을 저장**할 것입니다.

> [!WARNING]
> 저장된 자격 증명이 일부 리포지토리에 전달되는 프로젝트를 선택하면 비밀 로그인 정보가 유출될 수 있으므로 선택하면 안 됩니다.

### <a name="create-a-session-and-view-the-default-model"></a>세션을 만들고 기본 모델 보기

Unity의 **재생** 단추를 눌러 세션을 시작합니다. *게임* 패널의 뷰포트 맨 아래에 상태 텍스트가 있는 오버레이가 표시됩니다. 이 세션은 일련의 상태 전환 과정을 거치게 됩니다. **시작** 상태에서는 서버가 스핀업되며, 몇 분 정도 걸립니다. 성공하면 상태가 **준비**로 전환됩니다. 이제 세션이 **연결 중** 상태로 전환되고, 해당 서버의 렌더링 런타임에 도달하려고 시도합니다. 성공하면 샘플이 **연결됨** 상태로 전환됩니다. 이제 렌더링에 사용할 모델 다운로드가 시작됩니다. 모델의 크기 때문에 다운로드에 몇 분 정도 걸릴 수 있습니다. 다운로드가 완료되면 원격으로 렌더링되는 모델이 표시됩니다.

![샘플의 출력](media/arr-sample-output.png)

축하합니다! 이제 원격으로 렌더링되는 모델을 볼 수 있습니다.

## <a name="inspecting-the-scene"></a>장면 검사

원격 렌더링 연결이 실행되면 검사기 패널이 추가 상태 정보로 업데이트됩니다.

![Unity 샘플 재생](./media/arr-sample-configure-session-running.png)

이제 검사기에서 새 노드를 선택하고 **자식 표시**를 클릭하여 장면 그래프를 살펴볼 수 있습니다.

![Unity 계층 구조](./media/unity-hierarchy.png)

장면에 [절단면](../overview/features/cut-planes.md) 개체가 있습니다. 해당 속성에서 이 개체를 사용하도록 설정하고 움직여 보세요.

![절단면 변경](media/arr-sample-unity-cutplane.png)

변환을 동기화하려면 **지금 동기화**를 클릭하거나 **모든 프레임 동기화** 옵션을 선택합니다. 구성 요소 속성은 변경하는 것만으로 충분합니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작에서는 이 샘플을 HoloLens에 배포하고 원격으로 렌더링되는 모델을 원래 크기로 살펴보겠습니다.

> [!div class="nextstepaction"]
> [빠른 시작: HoloLens에 Unity 샘플 배포](deploy-to-hololens.md)

또는 샘플을 데스크톱 PC에 배포할 수도 있습니다.

> [!div class="nextstepaction"]
> [빠른 시작: 데스크톱에 Unity 샘플 배포](deploy-to-desktop.md)
