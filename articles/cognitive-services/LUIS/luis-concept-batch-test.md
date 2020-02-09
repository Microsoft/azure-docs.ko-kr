---
title: 일괄 처리 테스트-LUIS
titleSuffix: Azure Cognitive Services
description: 일괄 처리 테스트를 사용하여 애플리케이션을 지속적으로 개선하고 해당 언어에 대한 이해를 향상합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: e9ad7c52af20762633c710b39a64fbebf0cf6213
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487734"
---
# <a name="batch-testing-with-1000-utterances-in-luis-portal"></a>LUIS 포털에서 1000개 발언을 사용한 일괄 처리 테스트

일괄 처리 테스트는 적극적인 학습 버전의 유효성을 검사 하 여 예측 정확도를 측정 합니다. 일괄 처리 테스트를 사용 하면 활성 버전에서 각 의도 및 엔터티의 정확성을 확인 하 고 차트를 사용 하 여 결과를 표시할 수 있습니다. 앱이 utterance 내에서 올바른 의도를 식별 하거나 레이블을 지정 하는 데 자주 실패 하는 경우 길이 발언에 더 많은 예제를 추가 하는 것과 같이 적절 한 조치를 취하는 일괄 처리 테스트 결과를 검토 하 여 정확성을 향상 시킵니다.

## <a name="group-data-for-batch-test"></a>일괄 처리 테스트를 위해 데이터 그룹화

일괄 처리 테스트에 사용되는 발언은 LUIS에 새로 추가된 발언이어야 합니다. 길이 발언 데이터 집합을 사용 하는 경우 길이 발언를 세 개의 집합으로 나눕니다. 예를 들어 길이 발언는 의도에 추가 되 고, 게시 된 엔드포인트에서 수신 되는 길이 발언, 학습 된 후 테스트 길이 발언를 일괄 처리 하는 데 사용 된 LUIS입니다. 

## <a name="a-data-set-of-utterances"></a>길이 발언의 데이터 집합

일괄 테스트를 위해 *데이터 집합*으로 알려진 길이 발언의 배치 파일을 제출 합니다. 데이터 집합은 **중복 되지 않은** 길이 발언 레이블이 지정 된 최대 1000를 포함 하는 JSON 형식의 파일입니다. 앱에서 최대 10 개의 데이터 집합을 테스트할 수 있습니다. 더 많은 테스트를 수행 해야 하는 경우 데이터 집합을 삭제 한 다음 새 데이터 집합을 추가 합니다.

|**규칙**|
|--|
|*중복 발언 없음|
|1000개 이하의 발언|

*중복은 먼저 토큰화된 일치가 아니라 정확한 문자열 일치로 간주됩니다. 

## <a name="entities-allowed-in-batch-tests"></a>일괄 테스트에서 허용되는 엔터티

모델의 모든 사용자 지정 엔터티는 일괄 처리 파일 데이터에 해당 엔터티가 없더라도 일괄 처리 테스트 엔터티 필터에 표시됩니다.

<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>

## <a name="batch-file-format"></a>배치 파일 형식

배치 파일은 발언으로 구성됩니다. 각 발언에는 감지될 것으로 예상하는 모든 [Machine Learning 엔터티](luis-concept-entity-types.md#types-of-entities)와 함께 예상된 의도 예측이 있어야 합니다. 

## <a name="batch-syntax-template-for-intents-with-entities"></a>엔터티를 사용한 의도에 대한 일괄 처리 구문 템플릿

다음 템플릿을 사용하여 일괄 처리 파일을 시작합니다.

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

일괄 처리 파일은 **startPos** 및 **endPos** 속성을 사용하여 엔터티의 시작과 끝을 나타냅니다. 값은 0부터 시작하고 공백으로 시작하거나 끝나면 안 됩니다. 이 파일은 startIndex 및 endIndex 속성을 사용하는 쿼리 로그와 다릅니다. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="batch-syntax-template-for-intents-without-entities"></a>엔터티를 사용하지 않은 의도에 대한 일괄 처리 구문 템플릿

다음 템플릿을 사용하여 엔터티를 사용하지 않고 일괄 처리 파일을 시작합니다.

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": []
  }
]
```

엔터티를 테스트하지 않는 경우 `entities` 속성을 포함하여 빈 배열 `[]`와 같이 값을 정합니다.


## <a name="common-errors-importing-a-batch"></a>배치를 가져오는 중 발생하는 일반적인 오류

일반적인 오류는 다음과 같습니다. 

> * 1,000개 발언 초과
> * 엔터티 속성이 없는 발언 JSON 개체입니다. 속성은 빈 배열일 수 있습니다.
> * 단어에 여러 엔터티로 레이블이 지정됨
> * 공간에서 시작하거나 종료하는 엔터티 레이블.

## <a name="batch-test-state"></a>일괄 처리 테스트 상태

LUIS는 각 데이터 집합의 마지막 테스트 상태를 추적 합니다. 여기에는 크기(일괄 처리의 발언 수), 마지막 실행 날짜, 마지막 결과(성공적으로 예측된 발언 수)가 포함됩니다.

<a name="sections-of-the-results-chart"></a>

## <a name="batch-test-results"></a>일괄 테스트 결과

일괄 처리 테스트 결과는 오류 행렬이라고 하는 분산형 그래프입니다. 이 그래프는 일괄 처리 파일의 발언과 현재 모델의 예측 의도 및 엔터티를 4방향에서 비교한 것입니다. 

**거짓 긍정** 및 **거짓 부정** 섹션의 데이터 요소는 오류를 나타내며, 조사해야 합니다. 모든 데이터 요소가 **참 긍정** 및 **참 부정** 섹션에 있는 경우이 데이터 집합에 대 한 앱의 정확성이 완벽 합니다.

![차트의 4개 섹션](./media/luis-concept-batch-test/chart-sections.png)

이 차트는 현재 학습을 기준으로 LUIS에서 잘못 예측하는 발언을 찾는 데 도움이 됩니다. 차트의 영역별로 결과가 표시됩니다. 그래프에서 개별 요소를 선택하여 발언 정보를 검토하거나, 영역 이름을 선택하여 해당 영역의 발언 결과를 검토합니다.

![일괄 테스트](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>결과 오류

일괄 처리 테스트의 오류는 배치 파일에 명시된 대로 예측되지 않은 의도를 나타냅니다. 차트의 두 빨간색 섹션에 오류가 표시됩니다. 

거짓 긍정 섹션은 발언이 의도 또는 엔터티와 일치하지 않았어야 하는데 일치했음을 나타냅니다. 거짓 부정은 발언이 의도 또는 엔터티와 일치했어야 하는데 일치하지 않았음을 나타냅니다. 

## <a name="fixing-batch-errors"></a>일괄 처리 오류 수정

일괄 처리 테스트에 오류가 있는 경우 의도에 발언을 더 추가하거나, LUIS에서 의도를 구분하는 데 도움이 되도록 더 많은 발언에 엔터티로 레이블을 지정할 수 있습니다. 발언을 추가하고 레이블을 지정했지만 일괄 처리 테스트에서 예측 오류가 계속 발생하는 경우에는 LUIS 학습 속도 향상에 도움이 되도록 도메인 특정 어휘가 포함된 [구 목록](luis-concept-feature.md) 기능을 추가하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

* [일괄 테스트](luis-how-to-batch-test.md) 방법 알아보기
