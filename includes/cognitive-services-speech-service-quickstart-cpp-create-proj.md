---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 2f9e02af0652108fe10e4b73e7bb43c48451ca77
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123231"
---
C++ 데스크톱 개발에 대한 Visual Studio 프로젝트를 만들려면 Visual Studio 개발 옵션을 설정하고, 프로젝트를 만들고, 대상 아키텍처를 선택하고, Speech SDK를 설치해야 합니다.

### <a name="set-up-visual-studio-development-options"></a>Visual Studio 개발 옵션 설정

시작하려면 먼저 Visual Studio에서 C++ 데스크톱 개발용으로 올바르게 설정했는지 확인합니다.

1. Visual Studio 2019를 열어 **시작** 창을 표시합니다.

   ![시작 창 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. **코드를 사용하지 않고 계속**을 선택하여 Visual Studio IDE로 이동합니다.

1. Visual Studio 메뉴 모음에서 **도구** > **도구 및 기능 가져오기**를 선택하여 Visual Studio 설치 관리자를 열고 **수정** 대화 상자를 봅니다.

   ![워크로드 탭, 수정 대화 상자, Visual Studio 설치 관리자](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. **워크로드** 탭의 **Windows** 아래에서 **C++를 사용한 데스크톱 개발** 워크로드를 찾습니다. 해당 워크로드 옆의 확인란이 아직 선택되어 있지 않은 경우 선택합니다.

1. **개별 구성 요소** 탭에서 **Nuget 패키지 관리자** 확인란을 찾습니다. 확인란이 아직 선택되어 있지 않으면 선택합니다.

1. **닫기** 또는 **수정**이라는 레이블이 지정된 모서리의 단추를 선택합니다. (단추 이름은 설치할 기능을 선택했는지 여부에 따라 달라집니다.) **수정**을 선택하는 경우 설치가 시작되며, 이는 다소 시간이 걸릴 수 있습니다.

1. Visual Studio 설치 관리자를 닫습니다.

### <a name="create-the-project-and-select-the-target-architecture"></a>프로젝트를 만들고 대상 아키텍처를 선택합니다.

다음으로 프로젝트를 만듭니다.

1. Visual Studio 메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 **새 프로젝트 만들기** 창을 표시합니다.

   ![새 프로젝트 만들기 C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. **콘솔 앱**을 찾아 선택합니다. 이 프로젝트 형식의 C++ 버전을 선택했는지 확인합니다(C#이나 Visual Basic이 아님).

1. **다음**을 선택하여 **새 프로젝트 구성** 화면을 표시합니다.

   ![새 프로젝트 구성 C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. **프로젝트 이름**에서 `helloworld`를 입력합니다.

1. **위치**에서 프로젝트를 저장할 폴더로 이동하여 선택하거나 새로 만듭니다.

이제 대상 플랫폼 아키텍처를 선택합니다. Visual Studio 도구 모음에서 **솔루션 플랫폼** 드롭다운 상자를 찾습니다. (표시되지 않는 경우 **보기** > **도구 모음** > **표준**을 선택하여 **솔루션 플랫폼**을 포함하는 도구 모음을 표시합니다.) 64비트 Windows를 실행하는 경우 드롭다운 상자에서 **x64**를 선택합니다. 64비트 Windows는 32비트 애플리케이션을 실행할 수도 있으므로 원한다면 **x86**을 선택할 수 있습니다.

### <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

마지막으로 [Speech SDK NuGet 패키지](https://aka.ms/csspeech/nuget)를 설치하고, 프로젝트에서 Speech SDK를 참조합니다.

1. **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리**를 선택하여 **NuGet - 솔루션** 창으로 이동합니다.

1. **찾아보기**를 선택합니다.

   ![NuGet - 솔루션 탭, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. **패키지 원본**에서 **nuget.org**를 선택합니다.

1. **검색** 상자에 `Microsoft.CognitiveServices.Speech`를 입력한 다음, 검색 결과에 표시된 후에 해당 패키지를 선택합니다.

   ![Microsoft.CognitiveServices.Speech C++ 패키지 설치 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. 검색 결과 옆의 패키지 상태 창에서 **helloworld** 프로젝트를 선택합니다.

1. **설치**를 선택합니다.

1. **변경 내용 미리 보기** 대화 상자에서 **확인**을 선택합니다.

1. **라이선스 승인** 대화 상자에서 라이선스를 확인한 다음, **동의함**을 선택합니다. 패키지 설치가 시작되고 설치가 완료되면 **출력** 창에 `Successfully installed 'Microsoft.CognitiveServices.Speech 1.9.0' to helloworld`와 유사한 메시지가 표시됩니다.
