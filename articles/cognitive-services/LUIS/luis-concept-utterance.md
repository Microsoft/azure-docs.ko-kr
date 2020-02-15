---
title: 좋은 예 길이 발언-LUIS
titleSuffix: Azure Cognitive Services
description: 발언은 앱이 해석해야 하는 사용자의 입력입니다. 사용자가 입력할 것으로 생각되는 구를 수집합니다. 같은 내용을 의미하지만, 단어 길이 및 단어 배치가 다르게 구성된 발언을 포함합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280804"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>LUIS 앱에 적합한 발언이 무엇인지 이해

**발언**은 앱이 해석해야 하는 사용자의 입력입니다. 여기에서 의도와 엔터티를 추출하도록 LUIS를 학습시키려면 각 의도에 대한 다양한 예제 발언을 캡처하는 것이 중요합니다. 활성 학습 또는 새 발언에 대한 지속적인 학습 프로세스는 LUIS에서 제공하는 기계 학습 인텔리전스에 반드시 필요합니다.

사용자가 입력할만한 발언을 수집합니다. 같은 의미를 갖지만 다르게 구성된 다양한 발언을 포함시킵니다.

* 발언 길이 - 클라이언트 애플리케이션에 대해 짧게, 중간으로, 길게
* 단어 길이 및 문구 길이 
* 단어 배치 - 발언의 시작, 중간 및 끝에 있는 엔터티
* 문법 
* 복수형
* 형태소 분석
* 명사 및 동사 선택
* 문장 부호 - 올바른 문법, 틀린 문법이 사용되거나 문법이 사용되지 않은 다양한 경우

## <a name="how-to-choose-varied-utterances"></a>다양한 발언을 선택하는 방법

LUIS 모델에 [예제 발언을 추가](luis-how-to-add-example-utterances.md)하여 처음 시작할 때 다음 원칙에 유의하세요.

### <a name="utterances-arent-always-well-formed"></a>발언이 항상 올바른 형식인 것은 아닙니다.

"내 파리행 티켓 예약"과 같은 문장이나 "예약" 또는 “파리행 항공권”과 같은 문장의 일부분이 될 수도 있습니다.  사용자는 종종 맞춤법 오류를 범합니다. 앱을 계획할 때는 사용자 입력을 LUIS에 전달하기 전에 수정하기 위해 [Bing Spell Check](luis-tutorial-bing-spellcheck.md)를 사용할지 여부를 고려합니다. 

사용자 발언의 맞춤법을 검사하지 않으면 입력 오류 및 철자 오류가 포함된 발언으로 LUIS를 학습하게 됩니다.

### <a name="use-the-representative-language-of-the-user"></a>사용자의 대표 언어 사용

발언을 선택할 때는 내가 일반적인 용어나 문구라고 생각하는 것이 클라이언트 애플리케이션의 일반적인 사용자에게는 올바르지 않을 수도 있다는 것을 인지해야 합니다. 도메인 환경이 없는 경우도 있습니다. 사용자가 전문가인 경우에만 말할 수 있는 용어나 문구를 사용할 때는 주의하십시오.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>관용구 뿐만 아니라 변형 용어 선택

아무리 다양한 문장 패턴을 만들기 위해 애쓰더라도 일부 어휘가 여전히 반복될 수 있습니다.

다음과 같은 예제 발언을 사용하세요.

|예제 발언|
|--|
|컴퓨터는 어떻게 구입합니까?|
|컴퓨터는 어디에서 구입합니까?|
|컴퓨터를 구입하고 싶습니다. 어떻게 하면 되나요?|
|컴퓨터는 언제 구입합니까?| 

여기서 핵심 용어 "computer"는 다양 하지 않습니다. 데스크톱 컴퓨터, 랩톱, 워크스테이션 또는 간단히 머신과 같은 대체 용어를 사용합니다. LUIS는 컨텍스트에서 동의어를 지능적으로 유추할 수 있지만 교육용 길이 발언을 만드는 경우에는 항상 변경 하는 것이 좋습니다.

## <a name="example-utterances-in-each-intent"></a>각 의도의 예제 발언

각 의도에는 15개 이상의 예제 발언이 필요합니다. 예제 발언이 없는 의도로는 LUIS를 학습시킬 수 없습니다. 길이 발언 예를 하나 이상 사용 하는 경우 LUIS가 의도를 정확 하 게 예측 하지 못할 수 있습니다. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>각 작성 반복에 대해 15개의 소규모 발언 그룹 추가

모델의 각 반복에서 많은 양의 발언을 추가하지 않도록 합니다. 15개 정도의 발언을 추가합니다. [학습](luis-how-to-train.md), [게시](luis-how-to-publish-app.md) 및 [테스트](luis-interactive-test.md)를 다시 진행합니다.  

LUIS는 LUIS 모델 작성자가 신중하게 선택한 발언으로 효과적인 모델을 빌드합니다. 너무 많은 발언을 추가하면 혼동을 가져오므로 유용하지 않습니다.

처음에는 소수의 발언으로 시작한 후 정확한 의도 예측 및 엔터티 추출을 위해 [엔드포인트 발언을 검토](luis-how-to-review-endpoint-utterances.md)합니다.

## <a name="utterance-normalization"></a>Utterance 정규화

Utterance 표준화는 학습 및 예측 중에 문장 부호와 분음 부호의 효과를 무시 하는 프로세스입니다.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>분음 부호 및 문장 부호에 대 한 Utterance 정규화

Utterance 정규화는 앱 JSON 파일의 설정 이므로 앱을 만들거나 가져올 때 정의 됩니다. Utterance 정규화 설정은 기본적으로 해제 되어 있습니다. 

분음 부호는 다음과 같이 텍스트에서 표시 되거나 서명 됩니다. 

```
İ ı Ş Ğ ş ğ ö ü
```

앱에서 정규화를 설정 하면 **테스트** 창의 점수, 일괄 처리 테스트 및 끝점 쿼리가 분음 부호 나 문장 부호를 사용 하 여 모든 길이 발언에 대해 변경 됩니다.

`settings` 매개 변수에서 LUIS JSON 앱 파일에 대 한 분음 부호 또는 문장 부호에 대 한 utterance 정규화를 설정 합니다.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

**문장 부호** 표준화는 모델이 학습 되 고 끝점 쿼리가 예측 되기 전에 길이 발언에서 문장 부호가 제거 된다는 것을 의미 합니다. 

**분음 부호** 를 정규화 하면 길이 발언의 분음 부호 문자를 일반 문자로 바꿉니다. 예를 들어 `Je parle français` `Je parle francais`됩니다. 

정규화는 예제 길이 발언 또는 예측 응답에서 문장 부호와 분음 부호를 표시 하지 않는다는 것을 의미 하지 않습니다. 단순히 학습 및 예측 중에 무시 됩니다.


### <a name="punctuation-marks"></a>문장 부호

문장 부호는 LUIS에서 별도 토큰입니다. 끝에 마침표를 포함 하지 않는 end와 utterance를 포함 하는 utterance는 두 개의 개별 길이 발언 이며 두 개의 다른 예측을 받을 수 있습니다. 

문장 부호가 정규화 되지 않은 경우에는 일부 클라이언트 응용 프로그램이 이러한 표시에 중요 한 영향을 줄 수 있으므로 LUIS는 기본적으로 문장 부호를 무시 하지 않습니다. 문장 부호를 사용하는 예제 발언과 문장 부호를 사용하지 않은 예제 발언이 모두 동일한 관련 점수를 반환하는지 확인해야 합니다. 

예 길이 발언 (문장 부호 포함 안 함) 또는 특수 구문을 사용 하 여 문장 부호를 무시 하기 쉬운 [패턴](luis-concept-patterns.md) 에서 모델이 구두점을 처리 하는지 확인 합니다 `I am applying for the {Job} position[.]`.

클라이언트 응용 프로그램에서 문장 부호에 특정 한 의미가 없으면 문장 부호를 정규화 하 여 문장 부호를 [무시](#utterance-normalization) 하는 것이 좋습니다. 

### <a name="ignoring-words-and-punctuation"></a>단어 및 문장 부호 무시

패턴에서 특정 단어나 문장 부호를 무시 하려면 대괄호의 _ignore_ 구문이 있는 [패턴](luis-concept-patterns.md#pattern-syntax) 을 사용 `[]`합니다. 

## <a name="training-utterances"></a>발언 학습

학습은 일반적으로 비결정적입니다. 발언 예측은 버전이나 앱마다 약간 다를 수 있습니다. 모든 교육 데이터를 사용하기 위해 `UseAllTrainingData` 이름/값 쌍으로 [버전 설정](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API를 업데이트하여 비결정적 학습을 제거할 수 있습니다.

## <a name="testing-utterances"></a>발언 테스트 

개발자는 발언을 [예측 엔드포인트](luis-how-to-azure-subscription.md) URL로 전송하여 실제 트래픽으로 LUIS 애플리케이션 테스트를 시작해야 합니다. 이러한 발언은 [발언 검토](luis-how-to-review-endpoint-utterances.md)로 의도 및 엔터티의 성능을 개선하는 데 사용됩니다. LUIS 웹 사이트 테스트 창을 통해 제출된 테스트는 엔드포인트로 전송되지 않으므로 활성 학습에 기여하지 않습니다. 

## <a name="review-utterances"></a>발언 검토

모델이 학습되고, 게시되고 [엔드포인트](luis-glossary.md#endpoint) 쿼리를 수신하면 LUIS에서 제안한 [발언을 검토](luis-how-to-review-endpoint-utterances.md)합니다. LUIS는 의도 또는 엔터티에 대해 낮은 점수를 갖는 엔드포인트 발언을 선택합니다. 

## <a name="best-practices"></a>모범 사례

[모범 사례](luis-concept-best-practices.md)를 검토하고 일반 제작 주기의 일환으로 적용합니다.

## <a name="label-for-word-meaning"></a>단어 의미에 대한 레이블

단어 선택이나 단어 배열은 동일하지만 같은 의미를 나타내지 않는 경우 엔터티로 레이블을 지정하지 마세요. 

다음 발화에서 단어 `fair`는 동형이의어입니다. 즉, 철자는 동일하지만 의미가 다릅니다.

|발화|
|--|
|올 여름 시애틀 지역에서 열리는 정기시로는 어떤 게 있나요?|
|시애틀 리뷰에 매겨긴 현재 평점은 공정합니까?|

이벤트 엔터티에서 모든 이벤트 데이터를 찾도록 하려면 두 번째 발화가 아닌 첫 번째 발화에서 `fair` 단어에 레이블을 지정하세요.


## <a name="next-steps"></a>다음 단계
사용자 발언을 이해하도록 LUIS 앱을 학습하는 방법에 대한 내용은 [예제 발언 추가](luis-how-to-add-example-utterances.md)를 참조하세요.

