---
title: AI(인공 지능)
description: LUIS은 AI (인공 지능)를 사용 하 여 사용자가 정의한 스키마에 따라 데이터에 대 한 언어 이해를 제공 합니다.
ms.topic: conceptual
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bdcede01ec6bab5f8b43d89ff40d588a011d5dfb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751353"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Language Understanding의 인공 지능 (LUIS)

LUIS은 AI (인공 지능)를 사용 하 여 사용자가 정의한 스키마에 따라 데이터에 대 한 NLU (자연어 인식)를 제공 합니다.

## <a name="natural-language-processing-nlp"></a>자연어 처리 (NLP)

NLU (자연 Language Understanding)는 NLP (자연어 처리)의 특정 하위 주제입니다.

자연어 처리는 모든 형태의 텍스트 데이터 처리를 처리 하는 광범위 한 개념입니다. 여기에는 다음과 같은 항목이 포함 됩니다.

* 토큰화
* 음성 (pos) 태그 지정
* 분할
* 형태학 상의 변형인 분석
* 의미 체계 유사성
* 담론(Discourse)
* Translation

## <a name="natural-language-processing-in-luis"></a>LUIS에서 자연어 처리

자연어 처리는 다음과 같은 방법으로 LUIS 앱에서 사용할 수 있습니다.
* [자연어 이해](#natural-language-processing-nlp) (LUIS)
* LUIS에서 구성 가능한 NLP 측면:
    * [토큰화](luis-language-support.md#tokenization)
    * 분음 부호, 문장 부호 및 word forms [API 설정을](luis-reference-application-settings.md) 통해 Morphology
* Utterance와 같은 다른 [Cognitive Services](../Welcome.md) 에서 제공 하는 쿼리를 전처리 또는 후 처리 합니다.
    * [변환](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>NU (자연어 인식)

NLU는 언어적 문을 사용자에 게 자연스럽 게 이해할 수 있도록 하는 표현으로 _변환_ 하는 기능입니다. 자연어를 이해 하는 것은 매우 어려운 문제 이며 _AI 하드_ 문제로 정의 됩니다.

LUIS은 의도 및 추출에 초점을 맞추기 위한 것 이며, 다음과 같은 사항을 식별할 수 있습니다.
* 사용자가 원하는 항목
* 이야기 하 고 있는 것입니다.

LUIS에는 예제에서 명시적으로 식별 하지 않고도 의미 체계 유사성과 같은 광범위 한 _NLP_ 측면에 대해 전혀 알지 못합니다. 예를 들어 다음 토큰 (단어)은 제공 된 예제의 유사한 컨텍스트에서 사용할 때까지 세 가지 다른 항목입니다.

* 구입
* 구입
* 구입한

## <a name="next-steps"></a>다음 단계

* LUIS 앱의 [개발 수명 주기에](luis-concept-app-iteration.md) 대 한 자세한 정보