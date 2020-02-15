---
title: 릴리스 정보-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스 기능 릴리스, 향상 된 기능, 버그 수정 및 알려진 문제에 대 한 실행 로그입니다.
services: cognitive-services
author: oscholz
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 235d8788b47355925d93cb3e3835d32e25c1b51f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168156"
---
# <a name="release-notes"></a>릴리스 정보

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020-1 월 릴리스

**새로운 기능**

- 다중 장치 대화: 동일한 음성 또는 텍스트 기반 대화에 여러 장치를 연결 하 고 필요에 따라 전송 되는 메시지를 변환 합니다. [이 문서](multi-device-conversation.md)에서 자세히 알아보세요. 
- Aar 패키지에 대 한 키워드 인식 지원이 추가 되었으며 x86 및 x64 기능에 대 한 지원이 추가 되었습니다. 
- 목적-C: `Connection` 개체에 추가 된 `SendMessage` 및 `SetMessageProperty` 메서드입니다. [여기](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)에서 설명서를 참조 하세요.
- 이제 C++ TTS api는 합성 텍스트 입력으로 `std::wstring` 지원 하므로 WSTRING을 SDK로 전달 하기 전에 문자열로 변환할 필요가 없습니다. 자세한 내용은 [여기](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)를 참조하세요. 
- C#: [언어 ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) 및 [소스 언어 구성을](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) 이제 사용할 수 있습니다.
- JavaScript: 음성 서비스에서 콜백 `receivedServiceMessage`으로 사용자 지정 메시지를 전달 하기 위해 `Connection` 개체에 기능을 추가 했습니다.
- JavaScript: 온-프레미스 컨테이너 및 소 버린 클라우드에서 편리 하 게 사용할 수 있는 `FromHost API`에 대 한 지원이 추가 되었습니다. [여기](speech-container-howto.md)에서 설명서를 참조 하세요.
- JavaScript: 이제 [orgads](https://github.com/orgads)의 기여 덕분에 `NODE_TLS_REJECT_UNAUTHORIZED`을 인식 합니다. 자세한 내용은 [여기](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)를 참조하세요.


**주요 변경 내용**

- `OpenSSL` 버전 1.1.1 b로 업데이트 되었으며 Linux 용 Speech SDK core 라이브러리에 정적으로 연결 되어 있습니다. 이로 인해 수신함 `OpenSSL` 시스템의 `/usr/lib/ssl` 디렉터리에 설치 되지 않은 경우 중단 될 수 있습니다. 문제를 해결 하려면 Speech SDK 문서에서 [설명서](how-to-configure-openssl-linux.md) 를 확인 하세요.
- `WordLevelTimingResult.Offset`에 대해 C# 반환 되는 데이터 형식을 `int`에서 `long`으로 변경 하 여 음성 데이터가 2 분 보다 길면 `WordLevelTimingResults`에 대 한 액세스를 허용 합니다.
- 이제 `PushAudioInputStream` 및 `PullAudioInputStream`에서 `AudioStreamFormat`기반으로 하는 음성 서비스에 wav 헤더 정보를 보내고, 해당 정보를 만들 때 선택적으로 지정 합니다. 이제 고객이 [지원 되는 오디오 입력 형식을](how-to-use-audio-input-streams.md)사용 해야 합니다. 다른 모든 형식은 최적의 인식 결과를 얻거나 다른 문제를 일으킬 수 있습니다. 


**버그 수정**

- 위의 주요 변경 내용에서 `OpenSSL` 업데이트를 참조 하세요. Linux 및 Java에서 간헐적인 충돌 및 성능 문제 (높은 부하 상태에서 잠금 경합)가 모두 수정 되었습니다. 
- Java: 높은 동시성 시나리오에서 개체 닫기를 개선 했습니다.
- NuGet 패키지를 재구성. Lib 폴더의 `Microsoft.CognitiveServices.Speech.core.dll` 및 `Microsoft.CognitiveServices.Speech.extension.kws.dll` 복사본 3 개를 제거 하 여 NuGet 패키지를 더 작고 더 빠르게 다운로드 하 고 일부 C++ 네이티브 앱을 컴파일하는 데 필요한 헤더를 추가 했습니다.
- [여기서](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)는 빠른 시작 샘플을 수정 했습니다. Linux, MacOS, Windows에서 "마이크를 찾을 수 없음" 예외를 표시 하지 않고 종료 되었습니다.
- [이 샘플과](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)같은 특정 코드 경로에서 긴 음성 인식이 있는 SDK 충돌 문제를 수정 했습니다.
- [이 고객 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)를 해결 하기 위해 Azure 웹 앱 환경에서 SDK 배포 오류가 수정 되었습니다.
- [이 고객 문제](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)를 해결 하기 위해 다중 `<voice>` 태그 또는 `<audio>` 태그를 사용 하는 동안 TTS 오류가 수정 되었습니다. 
- SDK가 일시 중단에서 복구 될 때 TTS 401 오류가 수정 되었습니다.
- JavaScript: [euirim](https://github.com/euirim)에서 기여 하 여 오디오 데이터의 순환 가져오기를 수정 했습니다. 
- JavaScript: 1.7에 추가 된 대로 서비스 속성을 설정 하기 위한 지원이 추가 되었습니다.
- JavaScript: 연결 오류가 발생 하 여 실패 한 websocket 다시 연결 시도가 연속적으로 발생 하는 문제를 해결 했습니다.


**샘플**

- Android에 대 한 키워드 인식 샘플이 [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)에 추가 되었습니다.
- [여기](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)에 서버 시나리오에 대 한 TTS 샘플이 추가 되었습니다.
- 및 C# C++ 에 대 한 다중 장치 대화 퀵 [스타트를 추가](quickstarts/multi-device-conversation.md)했습니다.


**기타 변경 내용**

- Android에서 SDK core 라이브러리 크기를 최적화 했습니다.
- 1\.9.0의 SDK는 대화 Transcriber의 음성 서명 버전 필드에서 `int` 및 `string` 형식을 모두 지원 합니다.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019-11 월 릴리스

**새로운 기능**

- 온-프레미스 컨테이너 및 소 버린 클라우드에서 편리 하 게 사용할 수 있도록 `FromHost()` API가 추가 되었습니다.
- 음성 인식에 대 한 자동 소스 언어 감지 추가 됨 ( C++Java 및)
- 음성 인식에 대 한 `SourceLanguageConfig` 개체를 추가 하 여 필요한 원본 언어를 지정 하 C++는 데 사용 합니다 (Java 및).
- NuGet 및 Unity 패키지를 통해 Windows (UWP), Android 및 iOS에 대 한 `KeywordRecognizer` 지원을 추가 했습니다.
- 비동기 일괄 처리에서 대화 기록을 수행 하기 위해 원격 대화 Java API가 추가 되었습니다.

**주요 변경 내용**

- 대화 Transcriber 기능이 네임 스페이스 `Microsoft.CognitiveServices.Speech.Transcription`에서 이동 했습니다.
- 대화 Transcriber 메서드의 일부가 new `Conversation` 클래스로 이동 됩니다.
- 32 비트 (ARMv7 및 x86) iOS에 대 한 지원 삭제

**버그 수정**

- 유효한 음성 서비스 구독 키 없이 로컬 `KeywordRecognizer` 사용 되는 경우 충돌 해결

**샘플**

- `KeywordRecognizer` 용 Xamarin 샘플
- `KeywordRecognizer`에 대 한 Unity 샘플
- C++자동 원본 언어 감지에 대 한 및 Java 샘플입니다.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019-9 월 릴리스

**새로운 기능**

- UWP (Xamarin on 유니버설 Windows 플랫폼), Android 및 iOS에 대 한 beta 지원이 추가 됨
- Unity에 대 한 iOS 지원 추가
- Android, iOS 및 Linux에서 형식이 alaw, Mulaw, FLAC에 대 한 `Compressed` 입력 지원을 추가 했습니다.
- 서비스에 메시지를 보내기 위한 `Connection` 클래스에 `SendMessageAsync` 추가 됨
- 메시지의 속성을 설정 하기 위해 `Connection` 클래스에 `SetMessageProperty` 추가 됨
- Java (Jre 및 Android), Python, Swift 및 목표에 대 한 TTS 추가 된 바인딩-C
- TTS Os, iOS 및 Android에 대 한 재생 지원을 추가 했습니다.
- TTS에 대 한 "단어 경계" 정보를 추가 했습니다.

**버그 수정**

- Android 용 Unity 2019에서 IL2CPP 빌드 문제가 수정 됨
- Wav 파일 입력에서 잘못 된 헤더의 문제를 해결 했습니다.
- 일부 연결 속성에서 고유 하지 않은 Uuid와 관련 된 문제가 해결 됨
- Swift 바인딩에서 null 허용 여부 지정자에 대 한 몇 가지 경고를 수정 했습니다 (작은 코드를 변경 해야 할 수 있음).
- 네트워크 로드를 통해 websocket 연결을 비정상적 하는 버그를 수정 했습니다.
- `DialogServiceConnector`에서 사용 하는 중복 된 임프레션 Id가 발생 하는 Android에서 문제가 해결 되었습니다.
- 에서 발생 하는 경우 다중 턴 상호 작용 및 오류 보고 (`Canceled` 이벤트를 통해)의 연결 안정성이 향상 되었습니다 `DialogServiceConnector`
- `DialogServiceConnector` 세션 시작은 활성 `StartKeywordRecognitionAsync()` 동안 `ListenOnceAsync()`를 호출할 때를 포함 하 여 이벤트를 정상적으로 제공
- 수신 중인 `DialogServiceConnector` 작업과 관련 된 충돌을 해결 했습니다.

**샘플**

- Xamarin에 대 한 빠른 시작
- Linux ARM64 정보를 사용 하 여 CPP 빠른 시작 업데이트
- IOS 정보를 사용 하 여 Unity 빠른 시작 업데이트

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019-6 월 릴리스

**샘플**

- UWP 및 Unity의 텍스트를 음성으로 변환 하기 위한 빠른 시작 샘플
- IOS의 Swift에 대 한 빠른 시작 샘플
- 음성 & 의도 인식 및 번역을 위한 Unity 샘플
- `DialogServiceConnector`에 대 한 빠른 시작 샘플 업데이트

**향상된 기능/변경 사항**

- 대화 상자 네임 스페이스:
  - `SpeechBotConnector`는 `DialogServiceConnector`로 이름이 변경되었습니다.
  - `BotConfig`는 `DialogServiceConfig`로 이름이 변경되었습니다.
  - `BotConfig::FromChannelSecret()` `DialogServiceConfig::FromBotSecret()`에 다시 매핑 되었습니다.
  - 이름 바꾸기 후에는 기존의 모든 Direct Line Speech 클라이언트도 계속 지원 됩니다.
- 프록시, 영구 연결을 지원 하도록 TTS REST 어댑터 업데이트
- 잘못 된 지역이 전달 되 면 오류 메시지 개선
- Swift/Objective-C:
  - 향상 된 오류 보고: 오류를 일으킬 수 있는 메서드는 오류 처리를 위해 `NSError` 개체를 노출 하는 메서드와 예외를 발생 시키는 두 가지 버전으로 제공 됩니다. Swift에는 전자가 노출 됩니다. 이렇게 변경 하려면 기존 Swift 코드를 adaptation 해야 합니다.
  - 향상 된 이벤트 처리

**버그 수정**

- TTS 용 수정: 오디오가 렌더링을 완료할 때까지 기다리지 않고 나중에 `SpeakTextAsync` 반환 됩니다.
- 전체 언어 지원을 가능 하 C# 게 하기 위해에서 문자열 마샬링을 수정 합니다.
- 샘플에서: net461 대상 프레임 워크를 사용 하 여 핵심 라이브러리를 로드 하는 .NET core 앱 문제에 대 한 수정
- 샘플의 출력 폴더에 네이티브 라이브러리를 배포 하기 위해 가끔씩 발생 하는 문제 해결
- 안정적으로 웹 소켓을 닫는 문제 해결
- Linux에서 부하가 매우 높은 상태에서 연결을 여는 동안 가능한 충돌 해결
- MacOS 용 framework 번들의 누락 된 메타 데이터에 대 한 수정
- Windows에서 `pip install --user` 문제 해결

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

이 릴리스는 버그 수정 릴리스 이며 네이티브/관리 SDK에만 영향을 줍니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

**버그 수정**

- 대화 기록을 사용할 때 FromSubscription을 수정 합니다.
- 음성 도우미에 대 한 발견 키워드의 버그를 수정 합니다.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-릴리스 수

**새로운 기능**

- 이제 Windows 및 Linux에서 KWS (Keyword 발견)를 사용할 수 있습니다. KWS 기능은 모든 마이크 종류에 사용할 수 있지만, 공식 KWS 지원은 현재 Azure Kinect 진한 하드웨어 또는 음성 장치 SDK에 있는 마이크 배열로 제한 되어 있습니다.
- 구문 힌트 기능은 SDK를 통해 사용할 수 있습니다. 자세한 내용은 [여기](how-to-phrase-lists.md)를 참조하세요.
- 대화 내용 기능은 SDK를 통해 사용할 수 있습니다. [여기](conversation-transcription-service.md)를 참조하세요.
- 직접 선 음성 채널을 사용 하 여 음성 도우미에 대 한 지원을 추가 합니다.

**샘플**

- 새 기능 또는 SDK에서 지 원하는 새로운 서비스에 대 한 샘플이 추가 되었습니다.

**향상된 기능/변경 사항**

- 서비스 동작 또는 서비스 결과 (예: 마스킹 및 기타)를 조정 하는 다양 한 인식기 속성이 추가 되었습니다.
- 이제 인식기 `FromEndpoint`를 만든 경우라도 표준 구성 속성을 통해 인식기를 구성할 수 있습니다.
- 목표-C: `OutputFormat` 속성이 `SPXSpeechConfiguration`에 추가 되었습니다.
- SDK는 이제 Linux 배포판으로 Debian 9를 지원 합니다.

**버그 수정**

- 발표자 리소스가 텍스트를 음성으로 너무 일찍 소멸 하는 문제를 해결 했습니다.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

이 릴리스는 버그 수정 릴리스 이며 네이티브/관리 SDK에만 영향을 줍니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- 웹 팩에서 https-프록시 에이전트를 로드 하지 못하도록 합니다.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019-4 월 릴리스

**새로운 기능**

- 이제 SDK는 텍스트 음성 변환 서비스를 베타 버전으로 지원 합니다. 및 C++ C#의 Windows 및 Linux 데스크톱에서 지원 됩니다. 자세한 내용은 [텍스트 음성 변환 개요](text-to-speech.md#get-started)를 참조 하세요.
- 이제 SDK는 MP3 및 Opus/OGG 오디오 파일을 스트림 입력 파일로 지원 합니다. 이 기능은 및 C++ C# 의 Linux 에서만 사용할 수 있으며 현재 베타 버전입니다 (자세한 내용은 [여기](how-to-use-codec-compressed-audio-input-streams.md)참조).
- Java, .NET core C++ 및 객관적인 C 용 Speech SDK는 macos를 지원 합니다. MacOS에 대 한 목표-C 지원은 현재 베타 버전입니다.
- iOS: iOS 용 Speech SDK (목표-C)도 CocoaPod으로 게시 됩니다.
- JavaScript: 기본이 아닌 마이크를 입력 장치로 지원 합니다.
- JavaScript: node.js에 대 한 프록시 지원.

**샘플**

- MacOS의 목적과 함께 Speech SDK C++ 를 사용 하는 샘플이 추가 되었습니다.
- 텍스트 음성 변환 서비스의 사용을 보여 주는 샘플이 추가 되었습니다.

**향상된 기능/변경 사항**

- Python: 인식 결과의 추가 속성이 이제 `properties` 속성을 통해 노출 됩니다.
- 추가 개발 및 디버그 지원을 위해 SDK 로깅 및 진단 정보를 로그 파일로 리디렉션할 수 있습니다 (자세한 내용은 [여기](how-to-use-logging.md)참조).
- JavaScript: 오디오 처리 성능을 향상 시킵니다.

**버그 수정**

- Mac/iOS: 음성 서비스에 대 한 연결을 설정할 수 없는 경우 오랜 대기를 야기 하는 버그를 수정 했습니다.
- Python: Python 콜백에서 인수에 대 한 오류 처리를 향상 시킵니다.
- JavaScript: RequestSession에서 종료 된 음성에 대 한 잘못 된 상태 보고가 수정 되었습니다.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019-2 월 새로 고침

이 릴리스는 버그 수정 릴리스 이며 네이티브/관리 SDK에만 영향을 줍니다. SDK의 JavaScript 버전에는 영향을 주지 않습니다.

**버그 수정**

- 마이크 입력을 사용할 때 메모리 누수가 수정 되었습니다. 스트림 기반 또는 파일 입력에 영향을 주지 않습니다.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-2 월 릴리스

**새로운 기능**

- Speech SDK는 `AudioConfig` 클래스를 통해 입력 마이크의 선택을 지원 합니다. 이를 통해 기본이 아닌 마이크에서 음성 서비스로 오디오 데이터를 스트리밍할 수 있습니다. 자세한 내용은 [오디오 입력 장치 선택](how-to-select-audio-input-devices.md)에 대 한 설명서를 참조 하세요. 이 기능은 JavaScript에서 아직 사용할 수 없습니다.
- 이제 Speech SDK는 베타 버전의 Unity를 지원합니다. [GitHub 샘플 리포지토리의](https://aka.ms/csspeech/samples)문제 섹션을 통해 피드백을 제공 합니다. 이 릴리스에서는 Windows x86 및 x64(데스크톱 또는 유니버설 Windows 플랫폼 애플리케이션)와 Android(ARM32/64, x86)의 Unity를 지원합니다. 자세한 내용은 [Unity 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)을 참조하세요.
- 이전 릴리스에서 제공 된 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` 파일은 더 이상 필요 하지 않습니다. 이제 기능이 핵심 SDK로 통합 되었습니다.

**샘플**

다음 새 콘텐츠는 [샘플 리포지토리](https://aka.ms/csspeech/samples)에서 사용할 수 있습니다.

- `AudioConfig.FromMicrophoneInput`에 대 한 추가 샘플입니다.
- 의도 인식 및 변환에 대한 추가 Python 샘플
- IOS에서 `Connection` 개체를 사용 하기 위한 추가 샘플입니다.
- 오디오 출력을 사용한 변환에 대한 추가 Java 예제
- [일괄 처리 기록 REST API](batch-transcription.md)

**향상된 기능/변경 사항**

- Python
  - `SpeechConfig`에서 매개 변수 확인 및 오류 메시지가 개선 되었습니다.
  - `Connection` 개체에 대 한 지원을 추가 합니다.
  - Windows에서 32비트 Python(x86)을 지원합니다.
  - Python용 Speech SDK가 베타를 벗어났습니다.
- iOS
  - SDK는 이제 iOS SDK 버전 12.1용으로 빌드되었습니다.
  - SDK는 이제 iOS 버전 9.2 이상을 지원합니다.
  - 참조 설명서를 개선하고 몇 가지 속성 이름을 수정합니다.
- JavaScript
  - `Connection` 개체에 대 한 지원을 추가 합니다.
  - 번들로 묶은 JavaScript의 형식 정의 파일을 추가했습니다.
  - 구문 힌트의 초기 지원 및 구현이 제공됩니다.
  - 인식을 위해 서비스 JSON을 사용하는 속성 컬렉션을 반환합니다.
- 이제 Windows DLL이 버전 리소스를 포함합니다.
- 인식기 `FromEndpoint` 만드는 경우 끝점 URL에 매개 변수를 직접 추가할 수 있습니다. `FromEndpoint` 사용 하 여 표준 구성 속성을 통해 인식기를 구성할 수 없습니다.

**버그 수정**

- 빈 프록시 이름과 프록시 암호가 올바르게 처리되지 않았습니다. 이 릴리스에서는 프록시 이름과 프록시 암호를 빈 문자열로 설정하면 프록시에 연결될 때 제출되지 않습니다.
- SDK에서 만든 SessionId가 항상 일부 언어&nbsp;/ 환경에서 무작위인 것은 아니었습니다. 이 문제를 해결 하기 위해 임의 생성기 초기화를 추가 했습니다.
- 권한 부여 토큰 처리를 개선했습니다. 권한 부여 토큰을 사용 하려면 `SpeechConfig`에서를 지정 하 고 구독 키를 비워 둡니다. 그런 다음, 평소처럼 인식기를 만듭니다.
- 경우에 따라 `Connection` 개체가 올바르게 해제 되지 않았습니다. 이 문제가 해결되었습니다.
- Safari에서도 오디오 출력의 변환 합성을 지원하도록 JavaScript 샘플을 수정했습니다.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

JavaScript 전용 릴리스입니다. 추가한 기능은 없습니다. 다음과 같이 수정했습니다.

- speech.end가 아닌 turn.end에서 스트림 끝을 실행합니다.
- 현재 전송이 실패한 경우 다음 전송을 예약하지 않는 오디오 펌프의 버그를 수정했습니다.
- 인증 토큰을 사용한 연속 인식을 수정했습니다.
- 다른 인식기/엔드포인트의 버그를 수정했습니다.
- 설명서 향상된 기능입니다.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-12 월 릴리스

**새로운 기능**

- Python
  - 이 릴리스에서는 베타 버전의 Python(3.5 이상)이 지원됩니다. 자세한 내용은 여기를 참조 하십시오.] (python.md).
- JavaScript
  - JavaScript용 Speech SDK가 오픈 소스로 제공됩니다. 소스 코드는 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)에서 사용할 수 있습니다.
  - 이제 Node.js를 지원합니다. 자세한 정보는 [여기](quickstart-js-node.md)에서 확인할 수 있습니다.
  - 오디오 세션에 대한 길이 제한이 제거되었으므로 백그라운드에서 자동으로 다시 연결됩니다.
- `Connection` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다.
  - `Recognizer`에서 `Connection` 개체에 액세스할 수 있습니다. 이 개체를 사용하면 명시적으로 서비스 연결을 시작하고 연결 및 연결 끊기 이벤트를 구독할 수 있습니다.
    이 기능은 JavaScript 및 Python에서 아직 사용할 수 없습니다.
- Ubuntu 18.04 지원
- Android
  - APK 생성 중에 ProGuard 지원이 설정되었습니다.

**향상된 기능**

- 스레드, 잠금, 뮤텍스 수를 줄이면서 내부 스레드 사용이 개선되었습니다.
- 오류 보고/정보가 개선되었습니다. 일부 경우에는 오류 메시지가 모든 방식으로 전파 되지 않습니다.
- 최신 모듈을 사용하도록 JavaScript의 개발 종속성을 업데이트했습니다.

**버그 수정**

- `RecognizeAsync`의 형식 불일치로 인해 메모리 누수가 수정 되었습니다.
- 어떤 경우에는 예외가 유출되었습니다.
- 번역 이벤트 인수에서 메모리 누수가 수정되었습니다.
- 장기 실행 세션에서 다시 연결 시 잠금 문제가 해결되었습니다.
- 실패 한 번역의 최종 결과가 누락 될 수 있는 문제를 해결 했습니다.
- C#: `async` 작업이 주 스레드에서 대기 되지 않은 경우에는 비동기 작업이 완료 되기 전에 인식기를 삭제할 수 있었습니다.
- Java: 문제를 해결 하 여 Java VM의 작동이 중단 되는 문제를 해결 했습니다.
- 목표-C: 열거형 매핑 수정 `RecognizingIntent`대신 RecognizedIntent이 반환 되었습니다.
- JavaScript: `SpeechConfig`에서 기본 출력 형식을 ' 단순 '으로 설정 합니다.
- JavaScript: JavaScript 및 기타 언어에서 구성 개체의 속성 간에 불일치를 제거 합니다.

**샘플**

- 여러 샘플 (예: 번역에 대 한 음성 출력 등)을 업데이트 하 고 수정 했습니다.
- Node.js 샘플을 [샘플 리포지토리](https://aka.ms/csspeech/samples)에 추가했습니다.

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**새로운 기능**

- Android x86/x64를 지원합니다.
- 프록시 지원: 이제 `SpeechConfig` 개체에서 함수를 호출 하 여 프록시 정보 (호스트 이름, 포트, 사용자 이름 및 암호)를 설정할 수 있습니다. iOS에서는 아직 이 기능을 사용할 수 없습니다.
- 향상된 오류 코드 및 메시지입니다. 인식이 오류를 반환한 경우 이미 `Reason`(취소된 이벤트의) 또는 `CancellationDetails`(인식 결과의) `Error`로 설정되어 있습니다. 취소 이벤트에는 이제 두 개의 추가 멤버 `ErrorCode` 및 `ErrorDetails`가 포함됩니다. 서버에서 보고된 오류를 사용하여 추가 오류 정보를 반환하는 경우 해당 서버를 새 멤버에서 사용할 수 있습니다.

**향상된 기능**

- 인식기 구성에서 추가 확인을 추가하고, 추가 오류 메시지를 추가했습니다.
- 오디오 파일의 중간에 긴 무음 시간 처리가 향상되었습니다.
- NuGet 패키지: .NET Framework의 경우 프로젝트는 AnyCPU 구성을 통한 빌드를 방지합니다.

**버그 수정**

- 인식기에서 발견된 몇 가지 예외를 수정했습니다. 또한 예외가 catch 되 고 `Canceled` 이벤트로 변환 됩니다.
- 속성 관리에서 메모리 누수를 해결합니다.
- 오디오 입력 파일이 인식기 작동을 중지할 수 있는 버그가 수정되었습니다.
- 세션 중지 이벤트 후 이벤트를 수신할 수 있는 버그가 수정되었습니다.
- 스레딩에서 일부 경합 상태가 수정되었습니다.
- 결국 충돌을 일으킬 수 있는 iOS 호환성 문제가 해결되었습니다.
- Android 마이크 지원의 안정성 향상.
- JavaScript의 인식기가 인식 언어를 무시하는 버그가 수정되었습니다.
- JavaScript에서 `EndpointId` (경우에 따라)를 설정 하는 것을 방지 하는 버그를 수정 했습니다.
- JavaScript의 AddIntent에서 매개 변수 순서가 변경 되었으며 누락 된 `AddIntent` JavaScript 서명이 추가 되었습니다.

**샘플**

- C++ [샘플 리포지토리에서](https://aka.ms/csspeech/samples)끌어오기 및 푸시 스트림 사용에 대 한 및 C# 샘플이 추가 되었습니다.

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

안정성 향상 및 버그 수정:

- 삭제 중인 인식기의 경합으로 인한 잠재적인 심각한 오류가 수정되었습니다.
- 속성이 설정되지 않은 경우의 잠재적인 심각한 오류가 수정되었습니다.
- 추가 오류 및 매개 변수 검사가 추가되었습니다.
- Objective-C: NSString에서 이름 재정의로 인한 잠재적인 심각한 오류가 수정되었습니다.
- Objectice-C: API의 표시 유형이 조정되었습니다.
- JavaScript: 관련 이벤트 및 해당 페이로드가 수정되었습니다.
- 설명서 향상된 기능입니다.

[샘플 리포지토리](https://aka.ms/csspeech/samples)에서 JavaScript에 대한 새로운 샘플이 추가되었습니다.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018년 9월 release

**새로운 기능**

- iOS에서 Objective-C를 지원합니다. [iOS용 Objective-C 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)을 확인하세요.
- 브라우저에서 JavaScript를 지원합니다. [JavaScript 빠른 시작](quickstart-js-browser.md)을 확인하세요.

**주요 변경 내용**

- 이 릴리스에서는 몇 가지 주요 변경 사항이 도입 되었습니다.
  자세한 내용은 [이 페이지](https://aka.ms/csspeech/breakingchanges_1_0_0) 를 확인 하세요.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018년 8월 릴리스

**새로운 기능**

- Speech SDK를 사용하여 빌드된 UWP 앱은 이제 WACK(Windows 앱 인증 키트)를 전달할 수 있습니다.
  [UWP 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)을 확인하세요.
- Linux(Ubuntu 16.04 x64)에서 .NET 표준 2.0을 지원합니다.
- 실험: Windows(64비트) 및 Linux(Ubuntu 16.04 x64)에서 Java 8을 지원합니다.
  [Java Runtime Environment 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)을 확인하세요.

**기능 변경**

- 연결 오류에 대한 추가 오류 세부 정보를 표시합니다.

**주요 변경 내용**

- Java(Android)에서, `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 함수는 더 이상 경로 매개 변수를 요구하지 않습니다. 이제 지원되는 모든 플랫폼에서 경로가 자동으로 검색됩니다.
- Java 및 C#에서 `EndpointUrl` 속성의 get-accessor가 제거되었습니다.

**버그 수정**

- Java에서, 번역 인식기의 오디오 합성 결과가 구현되었습니다.
- 비활성 스레드 및 사용되지 않는 열린 소켓 수가 증가하는 버그가 수정되었습니다.
- 오래 실행되는 인식이 전송 중에 종료될 수 있는 문제가 해결되었습니다.
- 인식기 종료에서 발생하는 경합 조건을 수정했습니다.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018년 7월 릴리스

**새로운 기능**

- Android 플랫폼(API 23: Android 6.0 Marshmallow 이상)을 지원합니다. [Android 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)을 확인하세요.
- Windows에서 .NET Standard 2.0을 지원합니다. [.NET Core 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)을 확인하세요.
- 실험: Windows(버전 1709 이상)에서 UWP를 지원합니다.
  - [UWP 빠른 시작](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)을 확인하세요.
  - 참고: Speech SDK를 사용하여 빌드된 UWP 앱은 아직 WACK(Windows 앱 인증 키트)를 제공하지 않습니다.
- 자동 다시 연결을 통해 장기 실행 인식을 지원합니다.

**기능 변경 내용**

- `StartContinuousRecognitionAsync()`에서 장기 실행 인식을 지원합니다.
- 인식 결과에 더 많은 필드가 포함됩니다. 인식된 텍스트의 오디오 시작 및 지속 시간의 오프셋(두 가지 모두 틱 단위) 및 인식 상태를 나타내는 추가 값(예: `InitialSilenceTimeout`, `InitialBabbleTimeout`)입니다.
- 팩터리 인스턴스를 만들기 위한 AuthorizationToken을 지원합니다.

**주요 변경 내용**

- 인식 이벤트: `NoMatch` 이벤트 유형이 `Error` 이벤트에 병합 되었습니다.
- SpeechOutputFormat의 C# `OutputFormat` 이름이로 변경 되어에 맞춰 정렬 C++됩니다.
- `AudioInputStream` 인터페이스의 일부 메서드 반환 형식이 다음과 같이 약간 변경되었습니다.
  - Java에서 `read` 메서드는 이제 `long` 대신 `int`을 반환합니다.
  - C#에서 `Read` 메서드는 이제 `uint` 대신 `int`을 반환합니다.
  - C++에서 `Read` 및 `GetFormat` 메서드는 이제 `size_t` 대신 `int`를 반환합니다.
- C++: 오디오 입력 스트림의 인스턴스가 이제 `shared_ptr`로만 전달될 수 있습니다.

**버그 수정**

- `RecognizeAsync()` 시간이 초과될 때 결과의 잘못된 반환 값이 수정되었습니다.
- Windows에서 미디어 파운데이션 라이브러리에 대한 종속성이 제거되었습니다. SDK에 이제 Core Audio API가 사용됩니다.
- 설명서 수정 사항: 지원되는 지역을 설명하는 [지역](regions.md) 페이지가 추가되었습니다.

**알려진 문제**

- Android용 Speech SDK에서는 번역에 대한 음성 합성 결과를 보고하지 않습니다. 이 문제는 다음 릴리스에서 해결될 예정입니다.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018년 6월 릴리스

**기능 변경 내용**

- AudioInputStream

  이제 인식기가 스트림을 오디오 원본으로 사용할 수 있습니다. 자세한 내용은 관련 [방법 가이드](how-to-use-audio-input-streams.md)를 참조하세요.

- 자세한 출력 형식

  `SpeechRecognizer`를 만들 때 `Detailed` 또는 `Simple` 출력 형식을 요청할 수 있습니다. `DetailedSpeechRecognitionResult`에는 신뢰도 점수, 인식된 텍스트, 원시 어휘 형식, 정규화된 형식 및 마스킹된 욕설이 포함된 정규화된 형식이 포함됩니다.

**호환성이 손상되는 변경**

- C#의 `SpeechRecognitionResult.Text`가 `SpeechRecognitionResult.RecognizedText`로 변경되었습니다.

**버그 수정**

- 종료하는 동안 USP 계층에서 발생할 수 있는 콜백 문제가 해결되었습니다.
- 인식기가 오디오 입력 파일을 사용한 경우, 필요한 것보다 더 긴 파일 핸들을 사용하고 있었습니다.
- 메시지 펌프와 인식기 간에 여러 가지 교착 상태가 제거되었습니다.
- 서비스 응답 시간이 초과되면 `NoMatch` 결과를 실행합니다.
- Windows의 미디어 파운데이션 라이브러리는 지연 로드됩니다. 이 라이브러리는 마이크 입력에만 필요합니다.
- 오디오 데이터의 업로드 속도는 원본 오디오 속도의 두 배 정도로 제한됩니다.
- Windows에서 C# .NET 어셈블리에 이제 강력한 이름이 지정됩니다.
- 설명서 수정: `Region`은 인식기를 만드는 데 필요한 정보입니다.

추가 샘플이 추가되었고 지속적으로 업데이트되고 있습니다. 최신 샘플 집합은 [Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018년 5월 릴리스

이 릴리스는 Cognitive Services Speech SDK의 첫 번째 공개 미리 보기 릴리스입니다.
