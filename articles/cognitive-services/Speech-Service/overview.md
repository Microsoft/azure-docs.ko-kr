---
title: Speech Service란?
titleSuffix: Azure Cognitive Services
description: Speech Service는 음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. Speech SDK, Speech Devices SDK 또는 REST API를 사용하여 애플리케이션, 도구 및 디바이스에 음성을 추가합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 7ddfae430e6aa4ec9549e40c937e5edcfd927f6d
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119916"
---
# <a name="what-is-the-speech-service"></a>Speech Service란?

Speech Service는 음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역을 단일 Azure 구독에 통합한 것입니다. [Speech SDK](speech-sdk-reference.md), [Speech Devices SDK](https://aka.ms/sdsdk-quickstart) 또는 [REST API](rest-apis.md)를 사용하여 애플리케이션, 도구 및 디바이스에 음성을 쉽게 추가할 수 있습니다.

> [!IMPORTANT]
> Speech Service는 Bing Speech API, Translator Speech 및 Custom Speech를 대체했습니다. 마이그레이션 지침에 대해서는 _방법 가이드 > 마이그레이션_을 참조하세요.

Speech Service를 구성하는 기능은 다음과 같습니다. 이 표의 링크를 사용하여 각 기능의 일반적인 사용 사례를 알아보거나 API 참조를 검색할 수 있습니다.

| 서비스 | 기능 | Description | SDK) | REST (영문) |
| ------- | ------- | ----------- | --- | ---- |
| [음성 텍스트 변환](speech-to-text.md) | 음성 텍스트 변환 | 음성 텍스트 변환은 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 기록합니다. [LUIS(Language Understanding)](https://docs.microsoft.com/azure/cognitive-services/luis/)에서 음성 텍스트 변환을 사용하여 기록된 음성에서 사용자 의도를 파생시키고 음성 명령 작업에 따라 행동합니다. | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [일괄 처리 기록](batch-transcription.md) | 일괄 처리 기록을 사용하면 대량의 비동기 음성 텍스트 변환 기록을 수행할 수 있습니다. 이 서비스는 사용자 지정 및 모델 관리와 같은 엔드포인트를 사용하는 REST 기반 서비스입니다. | 예 | [예](https://westus.cris.ai/swagger/ui/index) |
| | [다중 디바이스 대화](multi-device-conversation.md) | 대화의 다중 디바이스 또는 클라이언트를 연결하여 음성 또는 텍스트 기반 메시지를 전송하고 전사 및 번역을 간편하게 지원합니다.| yes | 예 |
| | [대화 전사](conversation-transcription-service.md) | 실시간 음성 인식, 화자 식별 및 다이어리화를 가능하게 해줍니다. 화자를 구별하는 기능을 사용하여 직접 만남을 전사하는 데 이상적입니다. | yes | 예 |
| | [Custom Speech 모델 만들기](#customize-your-speech-experience) | 고유한 환경에서 인식 및 기록을 위해 음성 텍스트 변환을 사용하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습하여 주변 소음 또는 산업용 어휘를 처리할 수 있습니다. | 예 | [예](https://westus.cris.ai/swagger/ui/index) |
| [텍스트 음성 변환](text-to-speech.md) | 텍스트 음성 변환 | 텍스트 음성 변환은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)을 사용하여 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다. 표준 음성 및 인공신경망 음성 중에서 선택합니다([언어 지원](language-support.md) 참조). | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [사용자 지정 음성 만들기](#customize-your-speech-experience) | 브랜드 또는 제품에 고유한 사용자 지정 음성 글꼴을 만듭니다. | 예 | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Speech Translation](speech-translation.md) | 음성 번역 | 음성 번역을 사용하면 음성에 대한 실시간 엔드투엔드 다중 언어 번역을 애플리케이션, 도구 및 디바이스에 추가할 수 있습니다. 이 서비스는 음성을 음성으로 변환 및 음성을 텍스트로 변환을 위해 사용합니다. | [예](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 예 |
| [음성 도우미](voice-assistants.md) | 음성 도우미 | Speech Service를 사용하는 음성 도우미를 사용하면 개발자가 자신의 애플리케이션과 환경에 맞게 자연스럽고 인간적인 대화형 인터페이스를 만들 수 있습니다. 음성 도우미는 작업 완료를 위해 Bot Framework의 Direct Line Speech 채널 또는 통합 사용자 지정 명령(미리 보기) 서비스를 사용하는 도우미 구현과 디바이스 사이에 빠르고 안정적인 상호 작용을 제공합니다. | [예](voice-assistants.md) | 예 |

## <a name="try-the-speech-service"></a>Speech Service 사용해 보기

각각이 10분 이내에 코드를 실행할 수 있게 디자인된 가장 인기 있는 프로그래밍 언어로 빠른 시작을 제공합니다. 이 표에는 각 기능에 대해 가장 인기 있는 빠른 시작이 포함되어 있습니다. 왼쪽 탐색 영역을 사용하여 추가 언어 및 플랫폼을 확인하세요.

| 음성 텍스트 변환(SDK) | 텍스트 음성 변환(SDK) | 번역(SDK) |
| -------------------- | -------------------- | ----------------- |
| [오디오 파일에서 음성 인식](quickstarts/speech-to-text-from-file.md) | [오디오 파일로 음성 합성](quickstarts/text-to-speech-audio-file.md) | [음성을 텍스트로 변환](quickstarts/translate-speech-to-text.md) |
| [마이크에서 음성 인식](quickstarts/speech-to-text-from-microphone.md) | [스피커로 음성 합성](quickstarts/text-to-speech.md) | [음성을 여러 대상 언어로 번역](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Blob 스토리지에 저장된 음성 인식](quickstarts/from-blob.md) | [긴 형식의 오디오에 대한 비동기 합성](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [음성을 음성으로 번역](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> 음성 텍스트 변환 및 텍스트 음성 변환은 REST 엔드포인트와 연결된 빠른 시작일 수도 있습니다.

Speech Service를 사용할 기회가 있으면 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

- [자습서: Speech SDK 및 LUIS, C#을 사용하여 음성에서 의도 인식](how-to-recognize-intents-from-speech-csharp.md)
- [자습서: 음성 SDK, C#으로 음성을 지원하도록 봇 설정](tutorial-voice-enable-your-bot-speech-sdk.md)
- [자습서: 텍스트 번역, 감정 분석 및 번역된 텍스트를 음성, REST로 합성하는 Flask 앱 빌드](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>샘플 코드 가져오기

샘플 코드는 Speech Service를 위해 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다. 다음 링크를 사용하여 SDK 및 REST 샘플을 확인하세요.

- [음성 텍스트 변환, 텍스트 음성 변환 및 음성 번역 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [음성 도우미 샘플(SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>음성 환경 사용자 지정

Speech Service는 기본 제공 모델에서 효율적으로 작동하지만, 제품 또는 환경에 맞게 경험을 추가로 사용자 지정하고 튜닝할 수 있습니다. 사용자 지정 옵션은 음향 모델 조정부터 브랜드를 위한 고유한 음성 글꼴까지 다양합니다.

| Speech Service | 플랫폼 | Description |
| -------------- | -------- | ----------- |
| 음성 텍스트 변환 | [Custom Speech](https://aka.ms/customspeech) | 사용자의 요구 사항과 사용 가능한 데이터에 맞게 음성 인식 모델을 사용자 지정합니다. 말하기 스타일, 어휘 및 배경 소음과 같은 음성 인식 장벽을 해결하세요. |
| 텍스트 음성 변환 | [Custom Voice](https://aka.ms/customvoice) | 사용 가능한 발언 데이터를 사용하여 텍스트 음성 변환 앱에 대한 다양한 종류의 음성을 만듭니다. 음성 매개 변수 세트를 조정하여 음성 출력을 세밀하게 조정할 수 있습니다. |

## <a name="reference-docs"></a>참조 문서

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: 음성 텍스트 변환](rest-speech-to-text.md)
- [REST API: 텍스트 음성 변환](rest-text-to-speech.md)
- [REST API: 일괄 처리 기록 및 사용자 지정](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [평가판 Speech Service 구독 키 받기](get-started.md)
