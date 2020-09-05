---
title: '자습서: 음성 SDK를 사용 하 여 봇 활성화-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Microsoft 봇 Framework를 사용 하 여 에코 봇을 만들고, Azure에 배포 하 고, 봇 Framework Direct Line Speech channel을 사용 하 여 등록 합니다. 그런 다음 봇에 얘기할 수 있는 Windows 용 샘플 클라이언트 앱을 구성 하 고 응답 하는 것을 환영 합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 2806ce18cc9febfdf15d48052d301da48b3c226f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934466"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>자습서: 음성 SDK를 사용 하 여 봇 음성 사용

음성 서비스를 사용 하 여 채팅 봇을 음성으로 사용 하도록 설정할 수 있습니다.

이 자습서에서는이를 반복 하는 봇을 만듭니다.
Microsoft Bot Framework를 사용 하 여 봇을 만들고, Azure에 배포 하 고, 봇 Framework Direct Line Speech channel에 등록 합니다.
그런 다음 봇에 대해 얘기할 수 있는 Windows 용 샘플 클라이언트 앱을 구성 하 고 다시 사용자에 게 알려줍니다.

이 자습서는 Azure, 봇 프레임 워크 봇, 직접 라인 음성 또는 음성 SDK를 처음 사용 하는 개발자를 위해 작성 되었으며 코딩을 제한 하면서 작업 중인 시스템을 신속 하 게 구축 하려고 합니다. 이러한 서비스에 대 한 경험이 나 지식이 필요 하지 않습니다.

이 자습서에서 음성 지원 채팅 봇은 다음 단계를 따릅니다.

1. 샘플 클라이언트 응용 프로그램은 직접 선 음성 채널과 Echo Bot에 연결 하도록 구성 되어 있습니다.
1. 사용자가 단추를 누르면 마이크에서 음성 오디오 스트림이 사용 됩니다. 사용자 지정 키워드를 사용 하는 경우 오디오는 지속적으로 기록 됩니다.
1. 사용자 지정 키워드를 사용 하는 경우 키워드 검색은 로컬 장치에서 수행 되며 오디오 스트리밍을 클라우드로 제어 합니다.
1. 샘플 클라이언트 응용 프로그램은 Speech SDK를 사용 하 여 직접 선 음성 채널 및 스트림 오디오에 연결 합니다.
1. 필요에 따라 서비스에서 더 높은 정확도 키워드 확인이 수행 됩니다.
1. 오디오는 음성 인식 서비스에 전달 되 고 transcribed 텍스트로 전달 됩니다.
1. 인식 된 텍스트는 Bot Framework 활동으로 Echo 봇에 전달 됩니다.
1. 응답 텍스트는 TTS (텍스트 음성 변환) 서비스에서 오디오로 전환 되 고 재생을 위해 클라이언트 응용 프로그램으로 스트리밍됩니다.

<!-- svg src in User Story 1754106 -->
![다이어그램-태그](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "음성 채널 흐름")

> [!NOTE]
> 이 자습서의 단계에는 유료 서비스가 필요 하지 않습니다. 새 Azure 사용자는 무료 Azure 평가판 구독의 크레딧을 사용 하 고 음성 서비스의 무료 계층을 사용 하 여이 자습서를 완료할 수 있습니다.

이 자습서에서는 다음 내용을 다룹니다.
> [!div class="checklist"]
> * 새 Azure 리소스 만들기
> * Echo Bot 샘플을 빌드, 테스트 및 배포 하는 Azure App Service
> * 직접 선 음성 채널을 사용 하 여 봇 등록
> * Windows 음성 도우미 클라이언트를 빌드하고 실행 하 여 Echo 봇과 상호 작용
> * 사용자 지정 키워드 활성화 추가
> * 인식 된 음성 및 음성 음성의 언어를 변경 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료 하는 데 필요한 사항은 다음과 같습니다.

- 마이크 및 스피커가 작동 하는 Windows 10 PC
- **ASP.NET 및 웹 개발** 워크 로드가 설치 된 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 이상
- [.NET Framework Runtime 4.6.1](https://dotnet.microsoft.com/download) 이상
- Azure 계정. [무료 등록](https://azure.microsoft.com/free/cognitive-services/)
- [GitHub](https://github.com/) 계정
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 자습서에서 만들 클라이언트 앱은 몇 가지 Azure 서비스를 사용 합니다. Bot의 응답에 대 한 왕복 시간을 줄이려면 이러한 서비스가 동일한 Azure 지역에 있는지 확인 하는 것이 좋습니다. 이 섹션에서는 **미국 서 부** 지역에 리소스 그룹을 만듭니다. 이 리소스 그룹은 봇 프레임 워크, 직접 선 음성 채널 및 음성 서비스에 대 한 개별 리소스를 만들 때 사용 됩니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">리소스 그룹 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 몇 가지 정보를 제공 하 라는 메시지가 표시 됩니다.
   * **구독** 을 **무료 평가판** 으로 설정 합니다 (기존 구독을 사용할 수도 있음).
   * **리소스 그룹**의 이름을 입력 합니다. **SpeechEchoBotTutorial-ResourceGroup**을 권장 합니다.
   * **지역** 드롭다운에서 **미국 서 부**를 선택 합니다.
1. **검토 + 만들기**를 클릭합니다. **유효성 검사를 통과**하는 배너가 표시 됩니다.
1. **만들기**를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.
1. 이 자습서의 뒷부분에서 만들 리소스와 마찬가지로 쉽게 액세스 하기 위해이 리소스 그룹을 대시보드에 고정 하는 것이 좋습니다. 이 리소스 그룹을 고정 하려면 리소스 그룹 이름 오른쪽에 있는 고정 아이콘을 클릭 합니다.

### <a name="choosing-an-azure-region"></a>Azure 지역 선택

이 자습서에 다른 지역을 사용 하려는 경우 이러한 요소를 선택 하는 것이 제한 될 수 있습니다.

* [지원 되는 Azure 지역을](regions.md#voice-assistants)사용 하는지 확인 합니다.
* 직접 선 음성 채널은 표준 및 신경망을 사용 하는 텍스트 음성 변환 서비스를 사용 합니다. 신경망은 [특정 Azure 지역으로 제한](regions.md#standard-and-neural-voices)됩니다.

지역에 대 한 자세한 내용은 [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)를 참조 하세요.

## <a name="create-resources"></a>리소스 만들기

이제 지원 되는 지역에 리소스 그룹이 있으므로 다음 단계는이 자습서에서 사용할 각 서비스에 대 한 개별 리소스를 만드는 것입니다.

### <a name="create-a-speech-service-resource"></a>음성 서비스 리소스 만들기

음성 리소스를 만들려면 다음 지침을 따르세요.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">음성 서비스 리소스 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 몇 가지 정보를 제공 하 라는 메시지가 표시 됩니다.
   * 리소스에 **이름을**지정 합니다. **SpeechEchoBotTutorial을** 권장 합니다.
   * **구독**의 경우 **무료 평가판** 이 선택 되어 있는지 확인 합니다.
   * **위치**에서 **미국 서 부**를 선택 합니다.
   * **가격 책정 계층**에 대해 **F0**를 선택 합니다. 무료 계층입니다.
   * **리소스 그룹**에 대해 **SpeechEchoBotTutorial-ResourceGroup**을 선택 합니다.
5. 모든 필수 정보를 입력 한 후 **만들기**를 클릭 합니다. 리소스를 만드는 데 몇 분 정도 걸릴 수 있습니다.
6. 이 자습서의 뒷부분에서는이 서비스에 대 한 구독 키가 필요 합니다. 리소스의 **개요** (키 관리) 또는 **키**에서 언제 든 지 이러한 키에 액세스할 수 있습니다.

이 시점에서 리소스 그룹 (**SpeechEchoBotTutorial**)에 음성 리소스가 있는지 확인 합니다.

| 속성 | Type  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

다음 단계는 App Service 계획을 만드는 것입니다. App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service 계획 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 몇 가지 정보를 제공 하 라는 메시지가 표시 됩니다.
   * **구독** 을 **무료 평가판** 으로 설정 합니다 (기존 구독을 사용할 수도 있음).
   * **리소스 그룹**에 대해 **SpeechEchoBotTutorial-ResourceGroup**을 선택 합니다.
   * 리소스에 **이름을**지정 합니다. **SpeechEchoBotTutorial-AppServicePlan을** 권장 합니다.
   * **운영 체제**의 경우 **Windows**를 선택 합니다.
   * **지역**에서 **미국 서 부**를 선택 합니다.
   * **가격 책정 계층**의 경우 **Standard S1** 이 선택 되어 있는지 확인 합니다. 이 값은 기본값 이어야 합니다. 그렇지 않은 경우 위에 설명 된 대로 **운영 체제** 를 **Windows** 로 설정 해야 합니다.
5. **검토 + 만들기**를 클릭합니다. **유효성 검사를 통과**하는 배너가 표시 됩니다.
6. **만들기**를 클릭합니다. 리소스 그룹을 만드는 데 몇 분 정도 걸릴 수 있습니다.

이 시점에서 리소스 그룹 (**SpeechEchoBotTutorial-ResourceGroup**)에 두 개의 리소스가 있는지 확인 합니다.

| 속성 | Type  | 위치 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service 플랜 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="build-an-echo-bot"></a>에코 봇 빌드

이제 몇 가지 리소스를 만들었으므로 봇을 빌드 하겠습니다. 여기서는 이름에서 알 수 있듯이 Echo Bot 샘플로 시작 하 여 응답으로 입력 한 텍스트를 에코 합니다. 걱정 하지 마세요. 샘플 코드는 변경 하지 않고 사용할 수 있습니다. Azure에 bot을 배포한 후 연결 하는 직접 선 음성 채널을 사용 하도록 구성 됩니다.

> [!NOTE]
> 다음 지침과 Echo 봇에 대 한 추가 정보는 [GitHub의 샘플](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)추가 정보에서 확인할 수 있습니다.

### <a name="run-the-bot-sample-on-your-machine"></a>컴퓨터에서 봇 샘플 실행

1. 샘플 리포지토리를 복제 합니다.

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Visual Studio를 실행합니다.
3. 도구 모음에서 **파일**  >  **열기**  >  **프로젝트/솔루션**을 선택 하 고 Echo Bot project 솔루션을 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 프로젝트가 로드 되 면 <kbd>F5</kbd> 키를 눌러 프로젝트를 빌드하고 실행 합니다.
5. 브라우저가 시작 되 고 다음과 비슷한 화면이 표시 됩니다.
    > [!div class="mx-imgBorder"]
    > [![echobot-localhost](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "Localhost에서 실행 되는 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Bot Framework 에뮬레이터를 사용 하 여 봇 샘플 테스트

[Bot Framework 에뮬레이터](https://github.com/microsoft/botframework-emulator) 는 개발자가 봇을 로컬로 테스트 하 고 디버그할 수 있도록 하는 데스크톱 앱입니다 (또는 원격으로 터널을 통해). 에뮬레이터가 입력 된 텍스트 (음성 아님)를 허용 합니다. 또한 봇은 텍스트를 사용 하 여 응답 합니다. 다음 단계에 따라 Bot Framework 에뮬레이터를 사용 하 여 텍스트 입력 및 텍스트 출력으로 실행 되는 Echo Bot을 로컬로 테스트 합니다. Azure에 봇을 배포한 후 음성 입력 및 음성 출력으로 테스트 합니다.

1. [Bot Framework 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 버전 4.3.0 이상을 설치 합니다.
2. Bot Framework 에뮬레이터를 시작 하 고 bot를 엽니다.
   * **파일**  ->  **Bot를 엽니다**.
3. 봇의 URL을 입력 합니다. 예를 들면

   ```
   http://localhost:3978/api/messages
   ```
   "연결"을 누릅니다.
4. 봇에 "Hello 및 환영!"을 사용 해야 합니다. 반환됩니다. 모든 문자 메시지를 입력 하 고 봇에서 응답이 수신 되는지 확인 합니다.
5. Echo Bot 인스턴스와 통신 교환이 다음과 같이 표시 될 수 있습니다. [ ![봇-에뮬레이터](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework 에뮬레이터")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>Azure App Service에 봇 배포

다음 단계는 Echo Bot를 Azure에 배포 하는 것입니다. 몇 가지 방법으로 봇을 배포할 수 있지만이 자습서에서는 Visual Studio에서 직접 게시 하는 방법을 집중적으로 설명 합니다.

> [!NOTE]
> 또는 [Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli) 및 [배포 템플릿을](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)사용 하 여 봇을 배포할 수 있습니다.

> [!NOTE]
> 다음 단계를 수행할 때 **Publish ...** 가 표시 되지 않는 경우 Visual Studio 설치 관리자를 사용 하 여 **ASP.NET 및 웹 개발** 워크 로드를 추가 합니다.

1. Visual Studio에서 직접 선 음성 채널에서 사용 하도록 구성 된 Echo Bot를 엽니다.

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. **솔루션 탐색기**에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시 ...** 를 선택 합니다.
1. **게시** 라는 제목의 새 창이 열립니다.
1. **Azure**를 선택 하 고 다음을 클릭 한 **다음** **Azure App Service (Windows)** 를 선택 하 고 다음을 클릭 한 **다음** **새 Azure App Service 만들기** ... 녹색 더하기 기호를 클릭 합니다.
1. **App Service (Windows)** 창이 표시 되 면 다음을 수행 합니다.
   * **계정 추가**를 클릭 하 고 Azure 계정 자격 증명을 사용 하 여 로그인 합니다. 이미 로그인한 경우 드롭다운 목록에서 원하는 계정을 선택합니다.
   * **이름**에 대해 봇에 대해 전역적으로 고유한 이름을 입력 해야 합니다. 이 이름은 고유한 bot URL을 만드는 데 사용 됩니다. 날짜 및 시간을 포함 하는 기본값이 채워집니다 (예: "EchoBot20190805125647"). 이 자습서의 기본 이름을 사용할 수 있습니다.
   * **구독**의 경우 **무료 평가판** 으로 설정 합니다.
   * **리소스 그룹**에 대해 **SpeechEchoBotTutorial-ResourceGroup** 을 선택 합니다.
   * **호스팅 계획**의 경우 **SpeechEchoBotTutorial-appserviceplan** 을 선택 합니다.
1. **만들기**를 클릭합니다. 최종 마법사 화면에서 **마침**을 클릭 합니다.
1. 게시 화면 오른쪽에서 **게시** 를 클릭 합니다. Visual Studio는 Azure에 bot을 배포 합니다.
1. Visual Studio의 출력 창에 다음과 같은 성공 메시지가 표시 됩니다.

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 기본 브라우저가 열리고 "봇이 준비 되었습니다!" 라는 페이지가 표시 됩니다.
1. 이 시점에서 Azure Portal 리소스 그룹 **SpeechEchoBotTutorial** 를 확인 하 고 다음 세 가지 리소스를 확인 합니다.

| 속성 | Type  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

## <a name="enable-web-sockets"></a>웹 소켓 사용

봇에서 웹 소켓을 사용 하 여 직접 선 음성 채널과 통신할 수 있도록 작은 구성 변경 작업을 수행 해야 합니다. 웹 소켓을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 App Service를 클릭 합니다. 리소스 이름은 **EchoBot20190805125647** (고유한 앱 이름)과 유사 해야 합니다.
2. 왼쪽 탐색 창의 **설정**에서 **구성**을 클릭 합니다.
3. **일반 설정** 탭을 선택 합니다.
4. **웹 소켓** 에 대해 설정/해제를 찾아 **켜기**로 설정 합니다.
5. **저장**을 클릭합니다.

> [!TIP]
> Azure App Service 페이지 맨 위에 있는 컨트롤을 사용 하 여 서비스를 중지 하거나 다시 시작할 수 있습니다. 이는 문제를 해결할 때 유용할 수 있습니다.

## <a name="create-a-channel-registration"></a>채널 등록 만들기

이제 봇을 호스트 하는 Azure App Service를 만들었으므로 다음 단계는 **Bot 채널 등록**을 만드는 것입니다. 채널 등록을 만드는 것은 직접 선 음성 채널을 포함 하 여 봇 프레임 워크 채널에 봇을 등록 하기 위한 필수 구성 요소입니다. Bot 채널을 사용 하는 방법에 대해 자세히 알아보려면 [채널에 봇 연결](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)을 참조 하세요.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure Bot 채널 등록 만들기 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 몇 가지 정보를 제공 하 라는 메시지가 표시 됩니다.
   * **봇 핸들**에 대해 **SpeechEchoBotTutorial-BotRegistration-# #** # #을 입력 하 고를 **####** 원하는 수로 바꿉니다. Bot 핸들은 전역적으로 고유 해야 합니다. Bot 핸들을 입력 하지만 오류 메시지가 표시 되는 경우 _요청 된 봇 ID를 사용할 수 없습니다_. 다른 숫자를 선택 합니다. 아래 예제에서는 8726을 사용 했습니다.
   * **구독**의 경우 **무료 평가판**을 선택 합니다.
   * **리소스 그룹**에 대해 **SpeechEchoBotTutorial-ResourceGroup**을 선택 합니다.
   * **위치**에서 **미국 서 부**를 선택 합니다.
     * **가격 책정 계층**에 대해 **F0**를 선택 합니다.
     * **메시징 끝점**의 경우 끝에 추가 된 경로를 사용 하 여 웹 앱에 대 한 URL을 입력 합니다 `/api/messages` . 예: 전역적으로 고유한 앱 이름이 **EchoBot20190805125647**인 경우 메시징 끝점은 `https://EchoBot20190805125647.azurewebsites.net/api/messages/` 입니다.
     * **Application insights**의 경우이를 **Off**로 설정할 수 있습니다. 자세한 내용은 [봇 분석](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)을 참조 하세요.
     * **앱 ID 및 암호 자동 생성을**무시 합니다.
5. **Bot 채널 등록** 블레이드의 아래쪽에서 **만들기**를 클릭 합니다.

이 시점에서 Azure Portal 리소스 그룹 **SpeechEchoBotTutorial** 를 확인 합니다. 이제 다음 4 개 이상의 리소스를 표시 해야 합니다.

| 속성 | Type  | 위치 |
|------|-------|----------|
| EchoBot20190805125647 | App Service | 미국 서부 |
| SpeechEchoBotTutorial-AppServicePlan | App Service 계획 | 미국 서부 |
| SpeechEchoBotTutorial-BotRegistration-8726 | Bot 채널 등록 | 전역 |
| SpeechEchoBotTutorial-Speech | Cognitive Services | 미국 서부 |

> [!IMPORTANT]
> 미국 서 부를 선택 했더라도 봇 채널 등록 리소스는 글로벌 지역을 표시 합니다. 예상된 동작입니다.

## <a name="optional-test-in-web-chat"></a>선택 사항: 웹 채팅에서 테스트

Azure Bot 채널 등록 페이지에는 **Bot Management** **의 웹 채팅에 테스트** 옵션이 있습니다. 웹 채팅은 봇에 대해 인증 해야 하므로 봇에서 기본적으로 작동 하지 않습니다. 텍스트 입력을 사용 하 여 배포 된 봇을 테스트 하려면 다음 단계를 수행 합니다. 이러한 단계는 선택 사항이 며 자습서의 다음 단계를 계속 하기 위해 필요 하지 않습니다. 

1. [Azure Portal](https://portal.azure.com) 에서 **EchoBotTutorial-BotRegistration-#** # # 리소스를 찾아서 엽니다.
1. **Bot management** 탐색에서 **설정**을 선택 합니다. **Microsoft 앱 ID** 아래에서 값을 복사 합니다.
1. Visual Studio EchoBot 솔루션을 엽니다. 솔루션 탐색기에서 **appsettings.js** 을 찾아 두 번 클릭 합니다.
1. JSON 파일에서 **MicrosoftAppId** 옆에 있는 빈 문자열을 복사 된 ID 값으로 바꿉니다.
1. Azure Portal로 돌아가서 **봇 관리** 탐색에서 **설정**을 선택 하 고 **Microsoft 앱 ID** 옆의 **(관리)** 를 클릭 합니다.
1. **새 클라이언트 암호**를 클릭 합니다. 설명 (예: "웹 채팅")을 추가 하 고 **추가**를 클릭 합니다. 새 비밀 복사
1. JSON 파일에서 **MicrosoftAppPassword** 옆에 있는 빈 문자열을 복사 된 암호 값으로 바꿉니다.
1. JSON 파일을 저장 합니다. 다음과 같이 표시됩니다.
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. 앱을 다시 게시 합니다. Visual Studio 솔루션 탐색기에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시 ...** 를 선택한 다음 **게시** 단추를 클릭 합니다.
10. 이제 웹 채팅에서 봇을 테스트할 준비가 되었습니다.

## <a name="register-the-direct-line-speech-channel"></a>직접 선 음성 채널 등록

이제 직접 선 음성 채널을 사용 하 여 봇을 등록할 때입니다. 이 채널은 봇과 Speech SDK를 사용 하 여 컴파일된 클라이언트 앱 간에 연결을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **SpeechEchoBotTutorial-BotRegistration-#** # # 리소스를 찾아 엽니다.
1. **Bot management** 탐색에서 **채널**을 선택 합니다.
   * **더 많은 채널**에서 **직접 줄 음성**을 클릭 합니다.
   * **직접 줄 음성 구성**이라는 페이지의 텍스트를 검토 한 다음 **인식 서비스 계정** 드롭다운 메뉴를 확장 합니다.
   * 메뉴에서 이전에 만든 음성 리소스 (예: **SpeechEchoBotTutorial**)를 선택 하 여 봇을 음성 구독 키에 연결 합니다.
   * 나머지 필드는 무시 합니다.
   * **저장**을 클릭합니다.

1. **Bot management** 탐색에서 **설정**을 클릭 합니다.
   * **스트리밍 끝점 사용**확인란을 선택 합니다. 이는 봇과 직접 선 음성 채널 사이에서 웹 소켓을 기반으로 하는 통신 프로토콜을 만드는 데 필요 합니다.
   * **저장**을 클릭합니다.

> [!TIP]
> 자세히 알아보려면 [선 음성으로 연결 하려면 봇 연결](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)을 참조 하세요. 이 페이지에는 추가 정보 및 알려진 문제가 포함 되어 있습니다.

## <a name="run-the-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트 실행

이 단계에서는 Windows 음성 도우미 클라이언트를 실행 합니다. 클라이언트는 [음성 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) 를 사용 하 여 직접 선 음성 채널을 통해 봇과의 통신을 관리 하는 c #의 WINDOWS PRESENTATION FOUNDATION (WPF) 앱입니다. 사용자 지정 클라이언트 앱을 작성 하기 전에이를 사용 하 여 봇과 상호 작용 하 고 테스트 합니다. 오픈 소스 이므로 실행 파일을 다운로드 하 여 실행 하거나 직접 빌드할 수 있습니다.

Windows 음성 도우미 클라이언트에는 bot에 대 한 연결을 구성 하 고, 텍스트 대화를 보고, JSON 형식의 봇 Framework 작업을 보고, 적응 카드를 표시할 수 있는 간단한 UI가 있습니다. 또한 사용자 지정 키워드의 사용을 지원 합니다. 이 클라이언트를 사용 하 여 봇과 이야기 하 고 음성 응답을 받습니다.

> [!NOTE]
> 이 시점에서 마이크와 스피커가 사용 하도록 설정 되 고 작동 하는지 확인 합니다.

1. [Windows 음성 도우미 클라이언트](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)에 대 한 GitHub 리포지토리로 이동 합니다.
1. 다음 중 하나에 제공 된 지침을 따르세요.
   * 실행할 ZIP 패키지에 미리 작성 된 실행 파일 다운로드 또는
   * 리포지토리를 복제 하 고 프로젝트를 빌드하여 실행 파일을 직접 작성 합니다.

1. `VoiceAssistantClient.exe`GitHub 리포지토리의 지침에 따라 클라이언트 응용 프로그램을 시작 하 고 bot에 연결 하도록 구성 합니다.
1. **다시 연결** 을 클릭 하 고 새 대화를 시작 했습니다. 메시지가 표시 되는지 확인 하 고 **마이크 단추를 누릅니다**.
1. 테스트를 수행해 보겠습니다. 마이크 단추를 클릭 하 고 몇 가지 단어를 영어로 말합니다. 인식 된 텍스트가 표시 됩니다. 말하기가 완료 되 면 봇이 고유한 음성으로 회신 하 고 그 뒤에 인식 된 단어가 표시 됩니다.
1. 텍스트를 사용 하 여 봇과 통신할 수도 있습니다. 아래쪽 표시줄에 텍스트를 입력 하면 됩니다. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Windows 음성 도우미 클라이언트의 오류 문제 해결

주 앱 창에 오류 메시지가 표시 되 면이 표를 사용 하 여 오류를 식별 하 고 해결 합니다.

| 오류 | 어떻게 해야 할까요? |
|-------|----------------------|
|오류 (AuthenticationFailure): 인증 오류 (401)로 인해 WebSocket을 업그레이드 하지 못했습니다. 올바른 구독 키 (또는 권한 부여 토큰) 및 지역 이름 확인| 앱의 설정 페이지에서 음성 구독 키와 해당 영역을 올바르게 입력 했는지 확인 합니다.<br>음성 키와 키 영역을 올바르게 입력 했는지 확인 합니다. |
|오류 (ConnectionFailure): 원격 호스트에서 연결을 끊었습니다. 오류 코드: 1011. 오류 세부 정보: 메시지를 보내기 전에 봇에 연결할 수 없습니다. | ["스트리밍 끝점 사용"](#register-the-direct-line-speech-channel) 확인란을 선택 했는지 확인 하 고 [ **웹 소켓** ](#enable-web-sockets) 을 켜기로 전환 합니다.<br>Azure App Service 실행 중인지 확인 합니다. 인 경우 App Service를 다시 시작 하십시오.|
|오류 (ConnectionFailure): 원격 호스트에서 연결을 끊었습니다. 오류 코드: 1002. 오류 세부 정보: 서버에서 상태 코드 ' 101 '이 (가) 필요한 데 상태 코드 ' 503 '이 (가) 반환 되었습니다. | ["스트리밍 끝점 사용"](#register-the-direct-line-speech-channel) 확인란을 선택 했는지 확인 하 고 [ **웹 소켓** ](#enable-web-sockets) 을 켜기로 전환 합니다.<br>Azure App Service 실행 중인지 확인 합니다. 인 경우 App Service를 다시 시작 하십시오.|
|오류 (ConnectionFailure): 원격 호스트에서 연결을 끊었습니다. 오류 코드: 1011. 오류 세부 정보: 응답 상태 코드는 성공: 500 (InternalServerError)을 나타내지 않습니다.| 봇이 output 활동 [말하기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 필드에서 신경망을 지정 했지만 음성 구독 키와 연결 된 Azure 지역은 신경망을 지원 하지 않습니다. [표준 및 신경망을](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)참조 하세요.|

테이블에서 문제가 해결 되지 않으면 [음성 도우미: 질문과 대답](faq-voice-assistants.md)을 참조 하세요. 이 자습서의 모든 단계를 수행한 후에도 문제를 해결할 수 없는 경우,  [음성 도우미 GitHub 페이지](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues)에 새 문제를 입력 하세요.

#### <a name="a-note-on-connection-time-out"></a>연결 제한 시간에 대 한 참고 사항

Bot에 연결 되어 있고 지난 5 분 동안 활동이 발생 하지 않으면 서비스에서 클라이언트와 봇의 websocket 연결을 자동으로 종료 합니다. 이것은 의도적인 것입니다. *"활성 연결 시간이 초과 되었으나 요청 시 다시 연결할 준비가 되었습니다." 라는*메시지가 아래쪽 표시줄에 표시 됩니다. "다시 연결" 단추를 누르지 않아도 됩니다. 즉, 마이크 단추를 누르고 통신을 시작 하거나 문자 메시지를 입력 하거나 키워드 (사용 하도록 설정 된 경우) 라고 합니다. 연결이 자동으로 다시 설정 됩니다.  
### <a name="view-bot-activities"></a>Bot 활동 보기

모든 봇은 **활동** 메시지를 보내고 받습니다. Windows Voice Assistant 클라이언트의 **활동 로그** 창에는 클라이언트가 봇에서 받은 각 활동이 있는 타임 스탬프 로그가 표시 됩니다. 메서드를 사용 하 여 클라이언트에서 봇으로 보낸 작업을 볼 수도 있습니다 [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  . 로그 항목을 선택 하면 연결 된 작업의 세부 정보가 JSON으로 표시 됩니다.

클라이언트에서 받은 작업의 샘플 json은 다음과 같습니다.

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

JSON 출력에서 반환 되는 항목에 대 한 자세한 내용은 [활동의 필드](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)를 참조 하세요. 이 자습서의 목적을 위해 [텍스트](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) 및 필드 [읽어주기](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 에 집중할 수 있습니다.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>음성 SDK 호출에 대 한 클라이언트 소스 코드 보기

Windows 음성 도우미 클라이언트는 [cognitiveservices account](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)를 사용 하며이는 speech SDK를 포함 합니다. 샘플 코드 검토를 시작 하는 좋은 위치는 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 다음 두 가지 음성 SDK 개체를 만드는 파일의 메서드 InitSpeechConnector ()입니다.
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -구성 설정 (예: 음성 구독 키, 키 지역)
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -인식 된 음성 및 봇 응답을 처리 하기 위해 채널 연결 및 클라이언트 구독 이벤트를 관리 합니다.

## <a name="add-custom-keyword-activation"></a>사용자 지정 키워드 활성화 추가

Speech SDK는 사용자 지정 키워드 활성화를 지원 합니다. Microsoft Assistant의 "안녕하세요 Cortana"와 마찬가지로 선택한 키워드를 지속적으로 수신 하는 앱을 작성할 수 있습니다. 키워드는 단일 단어 또는 여러 단어로 된 구를 사용할 수 있습니다.

> [!NOTE]
> 용어 *키워드* 는 종종 용어 *절전 모드 해제 단어*와 같은 의미로 사용 되며 Microsoft 설명서에서 사용 하는 것을 볼 수 있습니다.

키워드 검색은 클라이언트 앱에서 수행 됩니다. 키워드를 사용 하는 경우 키워드가 검색 되 면 오디오는 직접 선 음성 채널에만 스트리밍됩니다. 직접 선 음성 채널에는 *키워드 확인 (KWV)* 이라는 구성 요소가 있습니다 .이 구성 요소는 클라우드에서 더 복잡 한 처리를 수행 하 여 선택한 키워드가 오디오 스트림의 시작 부분에 있는지 확인 합니다. 키 단어 확인이 성공 하면 채널은 봇과 통신 합니다.

다음 단계를 수행 하 여 키워드 모델을 만들고,이 모델을 사용 하도록 Windows Voice Assistant 클라이언트를 구성 하 고, 마지막으로 봇으로 테스트 합니다.

1. [음성 서비스를 사용 하 여 사용자 지정 키워드를 만들려면](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)다음 지침을 따르세요.
2. 이전 단계에서 다운로드 한 모델 파일의 압축을 풉니다. 키워드의 이름을로 지정 해야 합니다. 이라는 파일을 찾고 있습니다 `kws.table` .
3. Windows 음성 도우미 클라이언트에서 **설정** 메뉴를 찾습니다. 오른쪽 위에 있는 기어 아이콘을 찾습니다. **모델 파일 경로** 를 찾고 `kws.table` 2 단계에서 파일의 전체 경로 이름을 입력 합니다.
4. **사용**확인란을 선택 했는지 확인 합니다. 확인란 옆에 "다음 연결 시 키워드를 수신 합니다." 라는 메시지가 표시 됩니다. 잘못 된 파일 또는 잘못 된 경로를 제공한 경우 오류 메시지가 표시 됩니다.
5. 음성 **구독 키**, **구독 키 영역**을 입력 한 다음 **확인** 을 클릭 하 여 **설정** 메뉴를 닫습니다.
6. **다시 연결**을 클릭 합니다. "새 대화가 시작 되었습니다. 형식, 마이크 단추를 누르거나 키워드를 표시 합니다." 라는 메시지가 표시 됩니다. 이제 앱이 지속적으로 수신 대기 중입니다.
7. 키워드로 시작 하는 모든 문구를 말하십시오. 예: "**{your keyword}**, 시간 이란?". 키워드를 uttering 후 일시 중지할 필요가 없습니다. 완료 되 면 다음 두 가지 작업이 수행 됩니다.
   * 스포크 정보에 대 한 기록을 볼 수 있습니다.
   * 잠시 후에 봇의 응답이 표시 됩니다.
8. 봇에서 지 원하는 세 가지 입력 형식으로 계속 시험해 보세요.
   * 아래쪽 표시줄의 형식화 된 텍스트
   * 마이크 아이콘을 누르고 말하기
   * 키워드로 시작 하는 구

### <a name="view-the-source-code-that-enables-keyword"></a>키워드를 사용 하는 소스 코드 보기

Windows 음성 도우미 클라이언트 소스 코드에서 다음 파일을 확인 하 여 키워드 검색을 사용 하는 데 사용 되는 코드를 검토 합니다.

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) 에는 [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-) 디스크의 로컬 파일에서 모델을 인스턴스화하는 데 사용 되는 SPEECH SDK 메서드에 대 한 호출이 포함 되어 있습니다.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) 연속 키워드 검색을 활성화 하는 Speech SDK 메서드에 대 한 호출을 포함 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) 합니다.

## <a name="optional-change-the-language-and-bot-voice"></a>필드 언어 및 봇 음성 변경

사용자가 만든 봇은 영어 (미국) 및 영어 (미국) 음성 텍스트 음성으로 음성을 수신 하 고 응답 합니다. 그러나 영어 또는 기본 음성을 사용 하는 것으로 제한 되지 않습니다. 이 섹션에서는 봇에서 수신 하 고 응답 하는 언어를 변경 하는 방법에 대해 알아봅니다. 해당 언어에 대해 다른 음성을 선택 하는 방법에 대해서도 알아봅니다.

### <a name="change-the-language"></a>언어 변경

[음성 텍스트](language-support.md#speech-to-text) 표에 언급 된 언어 중 하나에서 선택할 수 있습니다. 아래 예제에서는 언어를 독일어로 변경 합니다.

1. Windows 음성 도우미 클라이언트 앱을 열고 설정 단추 (오른쪽 위 기어 아이콘)를 클릭 한 다음 `de-de` 언어 필드에를 입력 합니다 ( [음성 텍스트](language-support.md#speech-to-text) 테이블에서 언급 한 로캘 값). 그러면 음성 언어가 인식 되며 기본값을 재정의 합니다 `en-us` . 또한 직접 선 음성 채널에 봇 회신에 대 한 기본 독일어 음성을 사용 하도록 지시 합니다.
2. 설정 페이지를 닫고 다시 연결 단추를 클릭 하 여 echo 봇에 대 한 새 연결을 설정 합니다.
3. 마이크 단추를 클릭 하 고 독일어로 된 구를 말합니다. 인식 된 텍스트와 echo 봇이 기본 독일 음성으로 회신 하는 것을 볼 수 있습니다.

### <a name="change-the-default-bot-voice"></a>기본 봇 음성 변경

텍스트 음성 변환 음성 및 발음 제어를 선택 하는 것은 봇이 간단한 텍스트가 아닌, SSML ( [음성 합성 마크업 언어](speech-synthesis-markup.md) ) 형식의 회신을 지정 하는 경우 수행할 수 있습니다. Echo 봇은 SSML를 사용 하지 않지만이를 수행 하도록 코드를 쉽게 수정할 수 있습니다. 아래 예제에서는 기본 여성 음성 대신 독일어 voice Stefan 아폴로 (남성 음성)를 사용 하도록 echo bot 회신에 SSML을 추가 합니다. 해당 언어에 대해 지원 되는 [표준 음성](language-support.md#standard-voices) 및 [신경망](language-support.md#neural-voices) 목록을 참조 하세요.

1. 먼저를 열어 보겠습니다 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` .
2. 다음 두 줄을 찾습니다.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 다음으로 바꿉니다.
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Visual Studio에서 솔루션을 빌드하고 빌드 오류를 수정 합니다.

' MessageFactory. Text ' 메서드의 두 번째 인수는 bot reply의 [작업 읽어주기 필드](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) 를 설정 합니다. 위와 같이 변경 하면 기본이 아닌 독일 음성을 지정 하기 위해 단순 텍스트에서 SSML로 대체 되었습니다.

### <a name="redeploy-your-bot"></a>Bot 다시 배포

이제 bot를 변경 했으므로 다음 단계는 Azure App Service에 다시 게시 하 여 사용해 보는 것입니다.

1. 솔루션 탐색기 창에서 **EchoBot** 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시**를 선택 합니다.
2. 이전 배포 구성이 이미 기본값으로 로드 되었습니다. **EchoBot20190805125647-웹 배포 옆의** **게시** 를 클릭 하기만 하면 됩니다.
3. **게시 성공** 메시지가 Visual Studio 출력 창에 표시 되 고 웹 페이지가 "봇 준비 되었습니다!" 라는 메시지와 함께 시작 됩니다.
4. Windows Voice Assistant 클라이언트 앱을 열고 설정 단추 (오른쪽 위 기어 아이콘)를 클릭 한 다음 언어 필드에가 계속 있는지 확인 `de-de` 합니다.
5. [Windows 음성 도우미 클라이언트를 실행](#run-the-windows-voice-assistant-client) 하 여 새로 배포 된 봇에 다시 연결 하 고, 새 언어로 말한 후 새 음성으로 해당 언어로 회신 하는 방법에 대 한 지침을 따르세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 배포 된 echo 봇을 계속 사용 하지 않으려는 경우 Azure 리소스 그룹 **SpeechEchoBotTutorial**을 삭제 하 여 연결 된 모든 azure 리소스를 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure 서비스** 탐색의 **리소스 그룹** 을 클릭 합니다.
2. **SpeechEchoBotTutorial-ResourceGroup**이라는 리소스 그룹을 찾습니다. 점 세 개 (...)를 클릭 합니다.
3. **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK를 사용 하 여 고유한 클라이언트 앱 빌드](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>참조

* [사용자 가까이 Azure 지역](https://azure.microsoft.com/global-infrastructure/locations/) 에 배포 하 여 봇 응답 시간 개선을 확인 하세요.
* 고품질 신경망을 [지 원하는 Azure 지역](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices) 에 배포
* 직접 선 음성 채널과 관련 된 가격 책정:
  * [Bot Service 가격 책정](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 사용자 고유의 음성 사용 봇 빌드 및 배포:
  * [Bot Framework bot](https://dev.botframework.com/)을 빌드합니다. [직접 선 음성 채널](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) 을 사용 하 여 등록 하 고 [음성을 위한 봇 사용자 지정](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 기존 [Bot Framework 솔루션](https://microsoft.github.io/botframework-solutions/index)살펴보기: [가상 길잡이](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) 를 빌드하고 [직접 줄 음성으로 확장](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
