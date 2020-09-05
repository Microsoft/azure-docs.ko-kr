---
title: 텍스트 음성 변환-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스의 텍스트 음성 변환 기능을 사용 하면 응용 프로그램, 도구 또는 장치에서 텍스트를 자연 스러운 사람이 아닌 합성 음성으로 변환할 수 있습니다. 미리 설정 된 음성을 선택 하거나 고유한 사용자 지정 음성을 만듭니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 42d5ca12e91de5e0e906fb017183ea684b63b49c
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167711"
---
# <a name="what-is-text-to-speech"></a>텍스트 음성 변환이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

음성 서비스에서 음성 텍스트를 음성으로 변환 하면 응용 프로그램, 도구 또는 장치에서 텍스트를 사람이 비슷한 합성 음성으로 변환할 수 있습니다. 표준 및 신경망에서 선택 하거나 제품 또는 브랜드에 고유한 사용자 지정 음성을 만드세요. 75 + standard 음성은 45 개 이상의 언어 및 로캘에서 사용할 수 있으며, 5 개의 신경망은 선택 된 수의 언어 및 로캘로 사용할 수 있습니다. 지원 되는 음성, 언어 및 로캘의 전체 목록은 [지원 되는 언어](language-support.md#text-to-speech)를 참조 하세요.

> [!NOTE]
> Bing Speech는 2019 년 10 월 15 일에 서비스 해제 되었습니다. 응용 프로그램, 도구 또는 제품이 Bing Speech Api 또는 Custom Speech를 사용 하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
> - [Bing Speech에서 Speech service로 마이그레이션](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>핵심 기능

* 음성 합성-표준, 신경망 또는 사용자 지정 음성을 사용 하 여 텍스트를 음성으로 변환 하려면 [음성 SDK](quickstarts/text-to-speech-audio-file.md) 또는 [REST API](rest-text-to-speech.md) 을 사용 합니다.

* 긴 오디오의 비동기 합성- [긴 오디오 API](long-audio-api.md) 를 사용 하 여 10 분 (예: 오디오 서적 또는 강의) 보다 긴 텍스트 음성 변환 파일을 비동기적으로 합성 합니다. 음성 SDK 또는 음성-텍스트 REST API를 사용 하 여 수행 하는 합성과 달리 응답은 실시간으로 반환 되지 않습니다. 요청이 비동기적으로 전송 되 고, 응답이 폴링 되며, 서비스에서 사용할 수 있게 되 면 합성 오디오가 다운로드 됩니다. 사용자 지정 신경망만 지원 됩니다.

* 표준 음성-통계 패라메트릭 합성 및/또는 연결 합성 기술을 사용 하 여 생성 됩니다. 이러한 음성은 매우 있는 하 고 자연 스러운 소리를 가집니다. 다양 한 음성 옵션을 사용 하 여 응용 프로그램에서 45 개 이상의 언어를 쉽게 사용할 수 있습니다. 이러한 음성은 약어, 머리글자어 확장, 날짜/시간 해석, polyphones 등에 대 한 지원을 포함 하 여 높은 발음 정확도를 제공 합니다. 표준 음성의 전체 목록은 [지원 되는 언어](language-support.md#text-to-speech)를 참조 하세요.

* 신경망-심층 신경망은 음성 언어의 스트레스 및 intonation 관련 하 여 기존 음성 합성의 제한을 극복 하는 데 사용 됩니다. Prosody 예측 및 음성 합성은 동시에 수행 되므로 더 많은 유체와 자연 스런 출력이 발생 합니다. 신경망을 사용 하면 자연 스런 봇 및 음성 도우미와의 상호 작용을 보다 자연스럽 게 수행 하 고, 전자 책과 같은 디지털 텍스트를 오디오 책으로 변환 하 고, 자동차 내 탐색 시스템을 향상 시킬 수 있습니다. 인간 같은 자연 스러운 자연 proaaady와 명확한 articulation을 사용 하 여 AI 시스템을 조작할 때 신경망은 듣기 피로를 크게 줄입니다. 신경망의 전체 목록은 [지원 되는 언어](language-support.md#text-to-speech)를 참조 하세요.

* SSML (speech 합성 Markup Language)-음성 텍스트 출력을 사용자 지정 하는 데 사용 되는 XML 기반 태그 언어입니다. SSML를 사용 하면 피치를 조정 하 고, 일시 중지를 추가 하 고, 발음을 높이고, 속도를 높이 거 나 낮출 수 있으며, 볼륨을 늘리거나 줄일 수 있으며, 여러 음성 특성을 단일 문서로 사용할 수 있습니다. [SSML](speech-synthesis-markup.md)을 참조 하십시오.

## <a name="get-started"></a>시작하기

텍스트 음성 변환 시작을 위한 [빠른](get-started-text-to-speech.md) 시작을 참조 하세요. 텍스트 음성 변환 서비스는 [음성 SDK](speech-sdk.md), [REST API](rest-text-to-speech.md)및 [음성 CLI](spx-overview.md) 를 통해 사용할 수 있습니다.

## <a name="sample-code"></a>예제 코드

텍스트 음성 변환에 대 한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 가장 인기 있는 프로그래밍 언어의 텍스트 음성 변환 변환을 다룹니다.

- [텍스트 음성 변환 샘플 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [텍스트 음성 변환 샘플(REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>사용자 지정

표준 및 신경망 외에도 제품 또는 브랜드에 고유한 사용자 지정 음성을 만들고 미세 조정할 수 있습니다. 시작 하는 데 사용 되는 모든 작업은 몇 가지 오디오 파일 및 관련 된 음성입니다. 자세한 내용은 [사용자 지정 음성 시작](how-to-custom-voice.md) 을 참조 하세요.

## <a name="pricing-note"></a>가격 정보

텍스트 음성 변환 서비스를 사용 하는 경우 문장 부호를 포함 하 여 음성으로 변환 되는 각 문자에 대 한 요금이 청구 됩니다. SSML 문서 자체에는 요금이 청구 되지 않지만 음소 및 피치와 같이 텍스트를 음성으로 변환 하는 방법을 조정 하는 데 사용 되는 선택적 요소는 청구 가능 문자로 계산 됩니다. 다음은 청구 가능한 항목 목록입니다.

- 요청의 SSML 본문에서 텍스트 음성 변환 서비스에 전달 된 텍스트입니다.
- `<speak>`및 태그를 제외한 SSML 형식으로 된 요청 본문의 텍스트 필드에 포함 된 모든 태그 `<voice>`
- 문자, 문장 부호, 공백, 탭, 태그 및 모든 공백 문자
- 유니코드에 정의된 모든 코드 포인트

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조 하세요.

> [!IMPORTANT]
> 각 중국어, 일본어 및 한국어 문자는 청구를 위해 두 문자로 계산 됩니다.

## <a name="reference-docs"></a>참조 문서

- [Speech SDK](speech-sdk.md)
- [REST API: 텍스트 음성 변환](rest-text-to-speech.md)

## <a name="next-steps"></a>다음 단계

- [무료 음성 서비스 구독 가져오기](get-started.md)
- [Speech SDK 가져오기](speech-sdk.md)
