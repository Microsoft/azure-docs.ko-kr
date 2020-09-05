---
title: 사용자 지정 키워드 만들기-음성 서비스
titleSuffix: Azure Cognitive Services
description: 장치는 항상 키워드 (또는 구)를 수신 대기 합니다. 사용자가 키워드를 표시 하면 장치는 사용자가 말하기를 중단할 때까지 모든 후속 오디오를 클라우드로 보냅니다. 키워드를 사용자 지정 하는 것은 장치를 차별화 하 고 브랜딩을 강화 하는 효과적인 방법입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: d80f244f7b5e17d730451093070b971e9aa041b9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919015"
---
# <a name="custom-keyword-basics"></a>사용자 지정 키워드 기본 사항

이 문서에서는 Speech Studio 및 Speech SDK를 사용 하 여 사용자 지정 키워드로 작업 하는 기본 사항을 알아봅니다. 키워드는 제품이 음성으로 활성화 될 수 있도록 하는 단어 또는 짧은 문구입니다. Speech Studio에서 키워드 모델을 만든 다음 응용 프로그램에서 Speech SDK와 함께 사용 하는 모델 파일을 내보냅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계에는 음성 구독과 음성 SDK가 필요 합니다. 아직 구독이 없는 경우 [음성 서비스를 무료로 사용해 보세요](get-started.md). SDK를 다운로드 하려면 해당 플랫폼에 대 한 [설치 가이드](quickstarts/setup-platform.md) 를 참조 하세요.

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio에서 키워드 만들기

사용자 지정 키워드를 사용 하려면 [Speech Studio](https://aka.ms/sdsdk-speechportal)에서 [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지를 사용 하 여 키워드를 만들어야 합니다. 키워드를 제공한 후에는 `.table` SPEECH SDK에서 사용할 수 있는 파일을 생성 합니다.

> [!IMPORTANT]
> 사용자 지정 키워드 모델 및 결과 `.table` 파일은 Speech Studio **에서만** 만들 수 있습니다.
> SDK 또는 REST 호출을 사용 하 여 사용자 지정 키워드를 만들 수 없습니다.

1. [Speech Studio](https://aka.ms/sdsdk-speechportal) 로 이동 하 여 **로그인** 하거나, 아직 음성 구독이 없는 경우 [**구독 만들기**](https://go.microsoft.com/fwlink/?linkid=2086754)를 선택 합니다.

1. [사용자 지정 키워드](https://aka.ms/sdsdk-wakewordportal) 페이지에서 **새 프로젝트**를 만듭니다. 

1. **이름**, **설명**(선택 사항)을 입력 하 고 언어를 선택 합니다. 언어 당 하나의 프로젝트가 필요 하 고 현재 언어에 대 한 지원이 제한 됩니다 `en-US` .

    ![키워드 프로젝트 설명](media/custom-keyword/custom-kws-portal-new-project.png)

1. 목록에서 프로젝트를 선택 합니다. 

    ![키워드 프로젝트를 선택 합니다.](media/custom-keyword/custom-kws-portal-project-list.png)

1. 새 키워드 모델을 만들려면 **모델 학습**을 클릭 합니다.

1. 모델 **이름** , 선택적 **설명**및 선택한 **키워드** 를 입력 한 후 **다음**을 클릭 합니다. 효과적인 키워드를 선택 하는 방법에 대 한 [지침](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) 을 참조 하세요.

    ![키워드 입력](media/custom-keyword/custom-kws-portal-new-model.png)

1. 포털에서 키워드에 대 한 후보 발음을 만듭니다. 재생 단추를 클릭 하 여 각 후보를 수신 하 고 잘못 된 발음 옆의 검사를 제거 합니다. 좋은 발음만 선택 하면 **학습** 을 클릭 하 여 키워드 모델 생성을 시작 합니다. 

    ![키워드 검토](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 모델을 생성 하는 데 최대 30 분 정도 걸릴 수 있습니다. 모델이 완료 되 면 키워드 목록이 **처리** 에서 **성공** 으로 변경 됩니다. 그런 다음 파일을 다운로드할 수 있습니다.

    ![키워드 검토](media/custom-keyword/custom-kws-portal-download-model.png)

1. 다운로드 한 파일은 보관 파일입니다 `.zip` . 보관 파일의 압축을 풀고 확장명이 인 파일이 표시 `.table` 됩니다. 다음 섹션에서 SDK와 함께 사용 하는 파일 이므로 해당 경로를 확인 해야 합니다. 파일 이름은 키워드 이름을 미러링합니다. 예를 들어 **장치 활성화** 키워드는 파일 이름 `Activate_device.table` 입니다.

## <a name="use-a-keyword-model-with-the-sdk"></a>SDK와 함께 키워드 모델 사용

먼저을 반환 하는 정적 함수를 사용 하 여 키워드 모델 파일을 로드 `FromFile()` `KeywordRecognitionModel` 합니다. `.table`Speech Studio에서 다운로드 한 파일의 경로를 사용 합니다. 또한 `AudioConfig` 기본 마이크를 사용 하 여를 만든 다음, `KeywordRecognizer` 오디오 구성을 사용 하 여 새를 인스턴스화합니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

다음으로, 모델 개체를 전달 하 여에 대 한 호출로 키워드 인식을 실행 `RecognizeOnceAsync()` 합니다. 그러면 키워드가 인식 될 때까지 지속 되는 키워드 인식 세션이 시작 됩니다. 따라서 일반적으로 다중 스레드 응용 프로그램에서이 디자인 패턴을 사용 하거나, 절전 모드 해제 단어를 무기한 대기할 수 있는 사용 사례에서이 디자인 패턴을 사용 합니다.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 여기에 표시 된 예제에서는 `SpeechConfig` 인증 컨텍스트에 개체가 필요 하지 않으며 백 엔드에 연결 하지 않기 때문에 로컬 키워드 인식을 사용 합니다. 그러나 [연속 백 엔드 연결을 활용](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)하 여 키워드 인식 및 확인을 모두 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[음성 장치 SDK 퀵 스타트](https://aka.ms/sdsdk-quickstart)를 사용 하 여 사용자 지정 키워드를 테스트 합니다.
