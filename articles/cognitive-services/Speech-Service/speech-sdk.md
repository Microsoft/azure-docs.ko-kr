---
title: 음성 SDK-음성 서비스 정보
titleSuffix: Azure Cognitive Services
description: 음성 SDK (소프트웨어 개발 키트)는 많은 음성 서비스 기능을 제공 하므로 음성 지원 응용 프로그램을 보다 쉽게 개발할 수 있습니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: ad26fe0d869a2e892a419b1732727c3ff1d1e9a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870717"
---
# <a name="about-the-speech-sdk"></a>Speech SDK 정보

음성 SDK (소프트웨어 개발 키트)는 음성 지원 응용 프로그램을 개발 하는 데 도움이 되는 많은 음성 서비스 기능을 제공 합니다. Speech SDK는 많은 프로그래밍 언어 및 모든 플랫폼에서 사용할 수 있습니다.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>시나리오 기능

Speech SDK는 음성 서비스의 많은 기능을 노출 하지만 일부는 제공 하지 않습니다. 음성 SDK의 기능은 시나리오와 연결 되는 경우가 많습니다. Speech SDK는 로컬 장치, 파일, Azure blob storage 및 입력 및 출력 스트림을 사용 하 여 실시간 및 비 실시간 시나리오 모두에 적합 합니다. 음성 SDK를 사용 하 여 시나리오를 달성할 수 없는 경우 REST API 대안을 찾습니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

음성 [텍스트](speech-to-text.md) ( *음성 인식이*라고도 함)는 응용 프로그램, 도구 또는 장치에서 사용 하거나 표시할 수 있는 텍스트로 오디오 스트림을 speech 합니다. [LUIS(Language Understanding)](../luis/index.yml)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. 음성 [번역](speech-translation.md) 을 사용 하 여 한 번의 호출로 음성 입력을 다른 언어로 번역 합니다. 자세한 내용은 [음성 텍스트 기본 사항](speech-to-text-basics.md)을 참조 하세요.

**SR (음성 인식), 구 목록, 의도, 번역 및 온-프레미스 컨테이너** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Windows & Linux & macOS
  - C # (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (Jre 및 Android)
  - JavaScript (브라우저 및 NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Go (SR 전용)

### <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성](text-to-speech.md) 변환 ( *음성 합성*이 라고도 함)은 텍스트를 사람이 나 비슷한 합성 음성으로 변환 합니다. 입력 텍스트는 문자열 리터럴 또는 [SSML (Speech 합성 Markup Language)](speech-synthesis-markup.md)을 사용 하는 것입니다. 표준 또는 신경망 음성에 대 한 자세한 내용은 [텍스트 음성 변환 언어 및 음성 지원](language-support.md#text-to-speech)을 참조 하세요.

**TTS (텍스트 음성 변환)** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Windows & Linux
  - C #/Windows & UWP & Unity
  - Java (Jre 및 Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST API은 다른 모든 상황에서 사용할 수 있습니다.

### <a name="voice-assistants"></a>음성 도우미

개발자 [는 음성 SDK를 사용 하](voice-assistants.md) 여 응용 프로그램 및 환경에 대 한 자연 스러운 대화형 인터페이스를 만들 수 있습니다. 음성 도우미 서비스는 장치와 비서 간에 빠르고 안정적인 상호 작용을 제공 합니다. 구현에서는 작업을 완료 하는 데 봇 프레임 워크의 Direct Line Speech channel 또는 통합 사용자 지정 명령 (미리 보기) 서비스를 사용 합니다. 또한 음성 도우미는 [사용자 지정 음성 포털](https://aka.ms/customvoice) 에서 만든 사용자 지정 음성을 사용 하 여 고유한 음성 출력 환경을 추가할 수 있습니다.

**음성 도우미** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (음성 장치 SDK)

#### <a name="keyword-spotting"></a>키워드 발견

[발견 키워드](speech-devices-sdk-create-kws.md) 의 개념은 Speech SDK에서 지원 됩니다. 키워드 발견는 음성에서 키워드를 식별 한 후 키워드를 청력으로 작업을 수행 하는 동작입니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이를 활성화 합니다.

**KWS (Keyword 발견)** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Windows & Linux
  - C #/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (음성 장치 SDK)
  - KWS (Keyword 발견) 기능은 모든 마이크 종류에 사용할 수 있지만, 공식 KWS 지원은 현재 Azure Kinect 진한 하드웨어 또는 음성 장치 SDK에 있는 마이크 배열로 제한 되어 있습니다.

### <a name="meeting-scenarios"></a>모임 시나리오

Speech SDK는 단일 장치 또는 다중 장치 대화에서 든 상관 없이 찍으면 되므로 간편 모임 시나리오에 적합 합니다.

#### <a name="conversation-transcription"></a>대화 전사

[대화 기록을](conversation-transcription.md) 통해 각 스피커 ( *diarization*라고도 함)에 대 한 실시간 (및 비동기) 음성 인식, 발표자 식별 및 문장 특성을 사용할 수 있습니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다.

다음과 같은 플랫폼에서 **대화 기록을** 사용할 수 있습니다.

  - C + +/Windows & Linux
  - C # (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (음성 장치 SDK)

#### <a name="multi-device-conversation"></a>다중 디바이스 대화

[다중 장치 대화](multi-device-conversation.md)를 사용 하 여 대화의 여러 장치 또는 클라이언트를 연결 하 여 음성 기반 또는 텍스트 기반 메시지를 전송 하 고 기록 및 번역을 간편 하 게 지원 합니다.

**다중 장치 대화** 는 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Windows
  - C # (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>사용자 지정/에이전트 시나리오

음성 SDK는 전화 통신 데이터가 생성 되는 찍으면 되므로 간편 call center 시나리오에 사용할 수 있습니다.

#### <a name="call-center-transcription"></a>호출 센터 전사

[콜 센터](call-center-transcription.md) 기록은 IVR (대화형 음성 응답)과 같은 다양 한 시스템에서 발생할 수 있는 많은 찍으면 되므로 간편의 전화 통신 데이터에 대해 음성 텍스트에 대 한 일반적인 시나리오입니다. 찍으면 되므로 간편의 음성 서비스 excel에서의 최신 음성 인식 모델은 사용자가 데이터를 이해 하기 어려운 경우에도이 전화 통신 데이터를 확인 합니다.

**콜 센터 기록을** REST API를 통해 Batch 음성 서비스를 통해 사용할 수 있으며 모든 상황에서 사용할 수 있습니다.

### <a name="codec-compressed-audio-input"></a>코덱 압축 오디오 입력

몇 가지 음성 SDK 프로그래밍 언어는 코덱 압축 오디오 입력 스트림을 지원 합니다. 자세한 내용은 <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">압축 된 오디오 입력 형식 <span class="docon docon-navigate-external x-hidden-focus"></span> 사용 </a>을 참조 하세요.

**코덱 압축 오디오 입력** 은 다음 플랫폼에서 사용할 수 있습니다.

  - C + +/Linux
  - C #/Linux
  - Java/Linux, Android 및 iOS

## <a name="rest-api"></a>REST API

Speech SDK는 음성 서비스의 많은 기능을 다룹니다. 일부 시나리오에서는 REST API를 사용 하는 것이 좋습니다.

### <a name="batch-transcription"></a>일괄 처리 기록

[일괄 처리 기록을](batch-transcription.md) 사용 하면 대용량 데이터의 비동기 음성-텍스트 기록을 사용할 수 있습니다. 일괄 처리는 REST API 에서만 가능 합니다. 음성 오디오를 텍스트로 변환 하는 것 외에도 batch 음성-텍스트는 diarization 및 감정를 허용 합니다.

## <a name="customization"></a>사용자 지정

음성 서비스는 음성 텍스트, 텍스트 음성 변환 및 음성 변환에서 기본 모델과 함께 뛰어난 기능을 제공 합니다. 경우에 따라 고유한 사용 사례를 사용 하 여 기본 성능을 향상 시킬 수 있습니다. 음성 서비스에는 쉽게 만들 수 있는 다양 한 코드 사용자 지정 도구가 있으며 사용자 고유의 데이터를 기반으로 사용자 지정 모델을 사용 하 여 경쟁 우위를 만들 수 있습니다. 이러한 모델은 사용자와 사용자의 조직 에서만 사용할 수 있습니다.

### <a name="custom-speech-to-text"></a>Custom Speech 텍스트

고유 환경에서 인식 및 기록에 대 한 음성 텍스트를 사용 하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습 하 여 주변 소음 또는 산업별 어휘를 해결할 수 있습니다. 코드 없는 Custom Speech 모델의 생성 및 관리는 [Custom Speech 포털](https://aka.ms/customspeech)을 통해 사용할 수 있습니다. Custom Speech 모델을 게시 한 후에는 Speech SDK에서 사용할 수 있습니다.

### <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

사용자 지정 음성이 라고도 하는 사용자 지정 텍스트 음성은 사용자의 브랜드에 대해 인식할 수 있는 일종의 음성을 만들 수 있는 온라인 도구 집합입니다. 코드 없는 사용자 지정 음성 모델의 생성 및 관리는 [사용자 지정 음성 포털](https://aka.ms/customvoice)을 통해 사용할 수 있습니다. 사용자 지정 음성 모델을 게시 한 후에는 음성 SDK에서 해당 모델을 사용할 수 있습니다.

## <a name="get-the-speech-sdk"></a>Speech SDK 가져오기

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[OWA(Outlook Web Access)](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[브라우저](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
