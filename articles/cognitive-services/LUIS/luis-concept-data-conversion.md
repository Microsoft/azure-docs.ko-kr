---
title: 데이터 변환 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)에서 예측 전에 발언이 변경될 수 있는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 42a9caff0433808734ee853cbad90a2088bf4e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95019248"
---
# <a name="convert-data-format-of-utterances"></a>발언의 데이터 형식 변환
LUIS는 예측 전에 사용자 발화의 다음 변환을 제공합니다.

* [Cognitive Services Speech](../Speech-Service/overview.md) 서비스를 사용하여 음성을 텍스트로 변환합니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

음성 텍스트 변환은 LUIS와의 통합으로 제공됩니다.

### <a name="intent-conversion-concepts"></a>의도 변환 개념
LUIS의 음성 텍스트 변환 기능을 사용하여 음성 발언을 엔드포인트로 보내고, LUIS 예측 응답을 수신할 수 있습니다. 이 프로세스는 [Speech](/azure/cognitive-services/Speech) Service를 LUIS와 통합하는 것입니다. [자습서](../speech-service/how-to-recognize-intents-from-speech-csharp.md)에서 음성 의도 변환에 대해 자세히 알아보세요.

### <a name="key-requirements"></a>핵심 요구 사항
이 통합을 위해 **Bing Speech API** 키를 만들 필요는 없습니다. Azure Portal에서 만든 **Language Understanding** 키가 이 통합에 사용됩니다. LUIS 시작 키는 사용하지 마세요.

### <a name="pricing-tier"></a>가격 책정 계층
이 통합에서는 Language Understanding 가격 책정 계층 이외의 다른 [가격 책정](luis-limits.md#key-limits) 모델을 사용합니다.

### <a name="quota-usage"></a>할당량 사용량
[제한 키](luis-limits.md#key-limits)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데이터 추출](luis-concept-data-extraction.md)