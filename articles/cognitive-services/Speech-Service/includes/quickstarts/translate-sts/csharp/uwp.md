---
title: '빠른 시작: 음성 대 음성 번역, C#(UWP) - Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: jhakulin
ms.topic: include
ms.openlocfilehash: 0434ddb1e90789628d14d4782eccb6514fccf8d8
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226532"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>샘플 코드 추가

이제 애플리케이션의 사용자 인터페이스를 정의하는 XAML 코드를 추가하고, C# 코드 숨김 구현을 추가합니다.

1. **솔루션 탐색기**에서 `MainPage.xaml` 파일을 엽니다.

1. 디자이너의 XAML 뷰에서 다음 XAML 코드 조각을 **Grid** 태그(`<Grid>`와 `</Grid>` 사이)에 삽입합니다.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. **솔루션 탐색기**에서 코드 숨김 원본 파일 `MainPage.xaml.cs`를 엽니다. `MainPage.xaml`로 그룹화되어 있습니다.

1. 그 안의 모든 코드를 다음 코드 조각으로 바꿉니다.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. 이 파일의 `SpeechTranslationFromMicrophone_ButtonClicked` 처리기에서 `YourSubscriptionKey` 문자열을 찾아 구독 키로 바꿉니다.

1. `SpeechTranslationFromMicrophone_ButtonClicked` 처리기에서 `YourServiceRegion` 문자열을 찾아 구독과 연결된 [지역](~/articles/cognitive-services/Speech-Service/regions.md)으로 바꿉니다.

1. 메뉴 모음에서 **파일** > **모두 저장**을 선택하여 변경 내용을 저장합니다.

## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행

이제 애플리케이션을 빌드하고 테스트할 준비가 되었습니다.

1. 메뉴 모음에서 **빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 코드는 이제 오류 없이 컴파일됩니다.

1. **디버그** > **디버깅 시작**을 선택하거나, **F5** 키를 눌러 애플리케이션을 시작합니다. **helloworld** 창이 나타납니다.

   ![C#으로 작성된 샘플 UWP 번역 애플리케이션 - 빠른 시작](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. **마이크 사용**을 선택하고, 액세스 권한 요청이 팝업되면 **예**를 선택합니다.

   ![마이크 액세스 권한 요청](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. **마이크 입력의 음성 번역**을 선택하고 디바이스의 마이크에 짧은 영어 구나 문장을 말합니다. 애플리케이션이 음성 서비스로 음성을 전송하고, 여기서 음성을 다른 언어(이 예제에서는 독일어)의 텍스트로 번역합니다. 음성 서비스는 번역된 텍스트를 애플리케이션으로 다시 보내며, 애플리케이션 창에 번역이 표시됩니다.

   ![음성 번역 사용자 인터페이스](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
