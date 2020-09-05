---
title: '빠른 시작: 마이크에서 음성 인식, C#(Unity) - Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 8307cfee90fdf65d119bbc1e55eabb0533a4a6ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926444"
---
> [!NOTE]
> Unity용 Speech SDK는 Windows 데스크톱(x86 및 x64) 또는 유니버설 Windows 플랫폼(x86, x64, ARM/ARM64), Android(x86, ARM32/64) 및 iOS(x64 시뮬레이터, ARM32 및 ARM64)를 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에:

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

이미 이 작업을 수행한 경우 매우 유용합니다. 계속 진행하겠습니다.

## <a name="create-a-unity-project"></a>Unity 프로젝트 만들기

1. Unity를 엽니다. Unity를 처음 사용하는 경우 **Unity Hub** *<version number>* 창이 표시됩니다. (Unity Hub를 직접 열어 이 창으로 이동할 수도 있습니다.)

   [![Unity Hub 창](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. **새로 만들기**를 선택합니다. **Unity를 사용하여 새 프로젝트 만들기** *<version number>* 창이 표시됩니다.

   [![Unity Hub에서 새 프로젝트 만들기](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. **프로젝트 이름**에서 **csharp-unity**를 입력합니다.
1. **템플릿**에서 **3D**가 아직 선택되지 않은 경우 이를 선택합니다.
1. **위치**에서 프로젝트를 저장할 폴더를 선택하거나 만듭니다.
1. **만들기**를 선택합니다.

잠시 후에 Unity 편집기 창이 표시됩니다.



## <a name="add-ui"></a>UI 추가

이제 최소한의 UI를 장면에 추가해 보겠습니다. 이 UI는 음성 인식을 트리거하는 단추와 결과를 표시하는 텍스트 필드로 구성됩니다. [**계층 구조** 창](https://docs.unity3d.com/Manual/Hierarchy.html)에서 Unity에서 새 프로젝트로 만든 샘플 장면이 표시됩니다.

1. **계층 구조** 창의 위쪽에서 **만들기** > **UI** > **Button**을 차례로 선택합니다.

   이 작업은 **계층 구조** 창에서 볼 수 있는 세 개의 게임 개체, 즉 **Button** 개체, 단추가 포함된 **Canvas** 개체 및 **EventSystem** 개체를 만듭니다.

   [![Unity 편집기 환경](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [**장면** 보기](https://docs.unity3d.com/Manual/UsingTheSceneView.html)에서 캔버스와 단추를 잘 볼 수 있도록 [**장면** 보기를 탐색](https://docs.unity3d.com/Manual/SceneViewNavigation.html)합니다.

1. [**검사기** 창](https://docs.unity3d.com/Manual/UsingTheInspector.html)(기본적으로 오른쪽에 있음)에서 단추가 캔버스의 중앙에 배치되도록 **Pos X** 및 **Pos Y** 속성을 **0**으로 설정합니다.

1. **계층 구조** 창에서 **만들기** > **UI** > **Text**를 차례로 선택하여 **Text** 개체를 만듭니다.

1. **검사기** 창에서 **Pos X** 및 **Pos Y** 속성을 **0** 및 **120**로 설정하고, **Width** 및 **Height** 속성을 **240** 및 **120**으로 설정합니다. 이러한 값을 사용하면 텍스트 필드와 단추가 겹치지 않습니다.

완료되면 **장면** 보기가 다음 스크린샷과 같이 표시됩니다.

[![Unity 편집기의 장면 보기](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>샘플 코드 추가

Unity 프로젝트에 대한 샘플 스크립트 코드를 추가하려면 다음 단계를 수행합니다.

1. [프로젝트 창](https://docs.unity3d.com/Manual/ProjectView.html)에서 **만들기** > **C# 스크립트**를 차례로 선택하여 새 C# 스크립트를 추가합니다.

   [![Unity 편집기의 프로젝트 창](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. 스크립트 이름을 `HelloWorld`로 지정합니다.

1. `HelloWorld`를 두 번 클릭하여 새로 만든 스크립트를 편집합니다.

   > [!NOTE]
   > Unity에서 편집하는 데 사용할 코드 편집기를 구성하려면 **편집** > **기본 설정**을 차례로 선택한 다음, **외부 도구** 기본 설정으로 이동합니다. 자세한 내용은 [Unity 사용자 설명서](https://docs.unity3d.com/Manual/Preferences.html)를 참조하세요.

1. 기존 스크립트를 다음 코드로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` 문자열을 찾아서 Speech Service 구독 키로 바꿉니다.

1. `YourServiceRegion` 문자열을 찾아서 구독과 연결된 [지역](https://aka.ms/speech/sdkregion)의 **영역 식별자**로 바꿉니다.

1. 변경 내용을 스크립트에 저장합니다.

이제 Unity 편집기로 돌아가서 스크립트를 구성 요소로 게임 개체 중 하나에 추가합니다.

1. **계층 구조** 창에서 **Canvas** 개체를 선택합니다.

1. **검사기** 창에서 **구성 요소 추가** 단추를 선택합니다.

   [![Unity 편집기의 검사기 창](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. 드롭다운 목록에서 위에서 만든 `HelloWorld` 스크립트를 검색하여 추가합니다. **Hello World(스크립트)** 섹션이 **검사기** 창에 표시되며, 초기화되지 않은 **출력 텍스트** 및 **기록 시작 단추**의 두 속성을 나열합니다. 이러한 Unity 구성 요소 속성은 `HelloWorld` 클래스의 public 속성과 일치합니다.

1. **기록 시작 단추** 속성의 개체 선택기(속성의 오른쪽에 있는 작은 원형 아이콘)를 선택하고, 이전에 만든 **Button** 개체를 선택합니다.

1. **출력 텍스트** 속성의 개체 선택기를 선택하고 앞에서 만든 **Text** 개체를 선택합니다.

   > [!NOTE]
   > 단추에는 중첩된 텍스트 개체도 있습니다. 텍스트 출력을 위해 실수로 선택하지 마세요(또는 혼동을 방지하기 위해 **검사기** 창의 **Name** 필드를 사용하여 텍스트 개체 중 하나의 이름을 바꾸세요).

## <a name="run-the-application-in-the-unity-editor"></a>Unity 편집기에서 애플리케이션 실행

이제 Unity 편집기 내에서 애플리케이션을 실행할 준비가 되었습니다.

1. Unity 편집기 도구 모음(메뉴 모음 아래)에서 **재생** 단추(오른쪽을 가리키는 삼각형)를 선택합니다.

1. [**게임** 보기](https://docs.unity3d.com/Manual/GameView.html)로 이동하여 **Text** 개체에서 **음성을 인식하려면 단추를 클릭하세요**를 표시할 때까지 기다립니다. (애플리케이션이 시작되지 않았거나 응답할 준비가 되지 않은 경우 **새 텍스트**가 표시됩니다.)

1. 단추를 선택하고 컴퓨터의 마이크에 영어 구 또는 문장을 말합니다. 음성은 Speech Service로 전송되고 **게임** 보기에 표시되는 텍스트로 전사됩니다.

   [![Unity 편집기의 게임 보기](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. [**콘솔** 창](https://docs.unity3d.com/Manual/Console.html)에서 디버그 메시지를 확인합니다. **콘솔** 창이 표시되지 않으면 메뉴 모음으로 이동하여 **창** > **일반** > **콘솔**을 차례로 선택하여 표시합니다.

1. 음성 인식이 완료되면 Unity 편집기 도구 모음에서 **재생** 단추를 선택하여 애플리케이션을 중지합니다.

## <a name="additional-options-to-run-this-application"></a>이 애플리케이션을 실행하기 위한 추가 옵션

이 애플리케이션은 Android 앱, Windows 독립 실행형 앱 또는 UWP 애플리케이션으로 배포할 수도 있습니다.
자세한 내용은 [샘플 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요. `quickstart/csharp-unity` 폴더에서는 이러한 추가 대상에 대한 구성을 설명합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

