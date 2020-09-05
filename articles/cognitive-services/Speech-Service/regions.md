---
title: 지역-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 텍스트, 텍스트 음성 변환, 음성 번역을 비롯 하 여 음성 서비스에 사용할 수 있는 지역 및 끝점의 목록입니다.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b0574c41042e172af78365bb273c81729ce204ab
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749305"
---
# <a name="speech-service-supported-regions"></a>음성 서비스 지원 지역

음성 서비스를 통해 애플리케이션에서 오디오를 텍스트로 변환하고, 음성 번역을 수행하고, 텍스트를 음성으로 변환할 수 있습니다. 음성 SDK 및 REST API에 대한 고유한 엔드포인트를 사용하여 여러 지역에서 서비스를 사용할 수 있습니다.

모든 지역의 음성 환경에 대 한 사용자 지정 구성을 수행 하는 음성 포털은 다음 위치에서 제공 됩니다. https://speech.microsoft.com

지역을 고려할 때 다음 사항에 유의 하세요.

* 응용 프로그램에서 [음성 SDK](speech-sdk.md)를 사용 하는 경우 음성 구성을 만들 때와 같은 지역 식별자를 제공 `westus` 합니다.
* 애플리케이션에서 Speech 서비스의 [REST API](rest-apis.md) 중 하나를 사용하는 경우 지역은 요청 시 사용하는 엔드포인트 URI의 일부가 됩니다.
* 지역에 대해 만든 키는 해당 지역에서만 유효합니다. 다른 지역에서 사용하려고 하면 인증 오류가 발생합니다.

## <a name="speech-sdk"></a>Speech SDK

[음성 SDK](speech-sdk.md)에서 지역을 문자열로 지정합니다(예를 들어, C#용 Speech SDK에서 `SpeechConfig.FromSubscription`에 대한 매개 변수로 지정).

### <a name="speech-to-text-text-to-speech-and-translation"></a>음성 텍스트, 텍스트 음성 변환 및 번역

음성 사용자 지정 포털은 다음 위치에서 제공 됩니다. https://speech.microsoft.com

음성 **인식**, **텍스트 음성**변환 및 **번역**을 위해 음성 서비스를 이러한 지역에서 사용할 수 있습니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

[SPEECH SDK](speech-sdk.md)를 사용 하는 경우 **지역 식별자** (예:에 대 한 매개 변수로)가 영역을 지정 `SpeechConfig.FromSubscription` 합니다. 지역이 구독의 지역과 일치 하는지 확인 합니다.

### <a name="intent-recognition"></a>의도 인식

Speech SDK를 통해 **의도 인식**을 사용할 수 있는 지역은 다음과 같습니다.

| 글로벌 지역 | 지역           | 영역 식별자 |
| ------------- | ---------------- | -------------------- |
| 아시아          | 동아시아        | `eastasia`           |
| 아시아          | 동남아시아   | `southeastasia`      |
| 오스트레일리아     | 오스트레일리아 동부   | `australiaeast`      |
| 유럽        | 북유럽     | `northeurope`        |
| 유럽        | 서유럽      | `westeurope`         |
| 북아메리카 | 미국 동부          | `eastus`             |
| 북아메리카 | 미국 동부 2        | `eastus2`            |
| 북아메리카 | 미국 중남부 | `southcentralus`     |
| 북아메리카 | 미국 중서부  | `westcentralus`      |
| 북아메리카 | 미국 서부          | `westus`             |
| 북아메리카 | 미국 서부 2        | `westus2`            |
| 남아메리카 | 브라질 남부     | `brazilsouth`        |

[LUIS(Language Understanding Service)](/azure/cognitive-services/luis/luis-reference-regions)에서 지원하는 게시 지역의 하위 집합입니다.

### <a name="voice-assistants"></a>음성 도우미

[음성 SDK](speech-sdk.md) 는 이러한 지역에서 **음성 도우미** 기능을 지원 합니다.

| 지역         | 영역 식별자 |
| -------------- | -------------------- |
| 미국 서부        | `westus`             |
| 미국 서부 2      | `westus2`            |
| 미국 동부        | `eastus`             |
| 미국 동부 2      | `eastus2`            |
| 서유럽    | `westeurope`         |
| 북유럽   | `northeurope`        |
| 동남아시아 | `southeastasia`      |

### <a name="speaker-recognition"></a>Speaker Recognition

Speaker Recognition 현재 지역 에서만 사용할 수 있습니다 `westus` .

## <a name="rest-apis"></a>REST API

음성 서비스는 또한 음성-텍스트 및 텍스트-음성 변환 요청에 대한 REST 엔드포인트를 노출합니다.

### <a name="speech-to-text"></a>음성 텍스트 변환

음성 텍스트 참조 설명서는 [음성 텍스트 REST API](rest-speech-to-text.md)를 참조 하세요.

REST API 끝점에는 다음과 같은 형식이 있습니다.

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

`<REGION_IDENTIFIER>`이 테이블에서 구독의 지역과 일치 하는 식별자로 대체 합니다.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP 오류가 발생하지 않도록 언어 매개 변수를 URL에 추가해야 합니다. 예를 들어 미국 서부 엔드포인트를 사용하는 미국 영어로 설정된 언어는 `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`입니다.

### <a name="text-to-speech"></a>텍스트 음성 변환

텍스트 음성 변환 참조 설명서는 [텍스트 음성 변환 REST API](rest-text-to-speech.md)을 참조 하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
