---
title: 음성 텍스트 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 기능을 사용 하면 오디오 스트림의 실시간 기록을 텍스트로 변환할 수 있습니다. 응용 프로그램, 도구 또는 장치는이 텍스트 입력을 사용 하 고, 표시 하 고, 작업을 수행할 수 있습니다. 이 서비스는 텍스트 음성 변환 (음성 합성) 및 음성 번역 기능에서 원활 하 게 작동 합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 90a5ec8c58865c3aa1b210db51b9ffeb7169d641
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640415"
---
# <a name="what-is-speech-to-text"></a>음성 텍스트 변환이란?

음성 인식으로 알려진 음성 서비스의 음성 텍스트는 오디오 스트림에 대 한 실시간 기록을 텍스트에 사용할 수 있도록 합니다. 응용 프로그램, 도구 또는 장치는이 텍스트를 명령 입력으로 사용 하 고, 표시 하 고, 작업을 수행할 수 있습니다. 이 서비스는 Microsoft에서 Cortana 및 Office 제품에 대해 사용 하는 것과 동일한 인식 기술로 구동 됩니다. <a href="./speech-translation.md" target="_blank">번역 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 및 <a href="./text-to-speech.md" target="_blank">텍스트 음성 변환 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 서비스 제공에서 원활 하 게 작동 합니다. 사용 가능한 음성 텍스트 언어의 전체 목록은 [지원 되는 언어](language-support.md#speech-to-text)를 참조 하세요.

음성 텍스트 서비스는 기본적으로 유니버설 언어 모델을 사용 합니다. 이 모델은 Microsoft 소유의 데이터를 사용 하 여 학습 되었으며 클라우드에 배포 됩니다. 이는 대화형 및 받아쓰기 시나리오에 적합 합니다. 고유 환경에서 인식 및 기록을 위해 음성 텍스트를 사용 하는 경우 사용자 지정 음향, 언어 및 발음 모델을 만들고 학습할 수 있습니다. 사용자 지정은 주변 소음 또는 산업별 어휘를 해결 하는 데 유용 합니다.

추가 참조 텍스트를 입력으로 사용 하는 경우 음성 텍스트 서비스를 사용 하 여 음성 발음을 평가 하 고 음성 오디오의 정확성과 능숙에 대 한 발표자 피드백 [을 제공할 수도](rest-speech-to-text.md#pronunciation-assessment-parameters) 있습니다. 발음 평가를 통해 언어 학습자는 사용자의 의견을 얻고 자신 있게 의견을 주고 받을 수 있도록 음성을 향상할 수 있습니다. 교육자는이 기능을 사용 하 여 여러 스피커의 발음을 실시간으로 평가할 수 있습니다. 이 기능은 현재 미국 영어를 지원 하며 전문가 들이 수행한 음성 평가와의 상관 관계를 동일 하 게 합니다.

> [!NOTE]
> Bing Speech는 2019 년 10 월 15 일에 서비스 해제 되었습니다. 응용 프로그램, 도구 또는 제품이 Bing Speech Api를 사용 하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
> - [Bing Speech에서 Speech service로 마이그레이션](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started-with-speech-to-text"></a>음성 텍스트 시작

음성 텍스트 서비스는 [음성 SDK](speech-sdk.md)를 통해 사용할 수 있습니다. 다양 한 언어 및 플랫폼에서 빠른 시작으로 사용할 수 있는 몇 가지 일반적인 시나리오가 있습니다.

 - [빠른 시작: 마이크 입력으로 음성 인식](quickstarts/speech-to-text-from-microphone.md)
 - [빠른 시작: 파일에서 음성 인식](quickstarts/speech-to-text-from-file.md)
 - [빠른 시작: blob 저장소에 저장 된 음성 인식](quickstarts/from-blob.md)

음성 텍스트 REST 서비스를 사용 하는 것을 선호 하는 경우 [Rest api](rest-speech-to-text.md)를 참조 하세요.

 - [빠른 시작: 참조 입력을 사용한 발음 평가](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>자습서 및 샘플 코드

Speech Service를 사용할 기회가 있으면 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

- [자습서: 음성 SDK 및 LUIS를 사용 하 여 음성에서 의도 인식, C 사용 #](how-to-recognize-intents-from-speech-csharp.md)

이 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

- [음성 텍스트 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [발음 평가 샘플 (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>사용자 지정

표준 음성 서비스 모델 외에도 사용자 지정 모델을 만들 수 있습니다. 사용자 지정은 말하는 스타일, 어휘 및 배경 소음과 같은 음성 인식 장벽을 극복 하는 데 도움이 됩니다 [Custom Speech](how-to-custom-speech.md)를 참조 하세요. 언어/로캘에 따라 사용자 지정 옵션은 지원 [되는 언어](supported-languages.md) 를 참조 하세요.

## <a name="batch-transcription"></a>일괄 처리 기록

일괄 처리는 저장소에서 많은 양의 오디오를 높여줄 수 있도록 하는 REST API 작업 집합입니다. SAS (공유 액세스 서명) URI를 사용 하 여 오디오 파일을 가리키고 기록 결과를 비동기적으로 받을 수 있습니다. Batch 기록 API를 사용 하는 방법에 대 한 자세한 내용은 [방법을](batch-transcription.md) 참조 하세요.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>다음 단계

- [평가판 Speech Service 구독 키 받기](get-started.md)
- [Speech SDK 가져오기](speech-sdk.md)
