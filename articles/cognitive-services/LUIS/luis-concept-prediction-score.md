---
title: 예측 점수-LUIS
titleSuffix: Azure Cognitive Services
description: 예측 점수는 사용자 utterance를 기반으로 LUIS API 서비스가 예측 결과에 대해 갖는 신뢰도를 나타냅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280819"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>예측 점수는 의도 및 엔터티에 대한 예측 정확도를 나타냅니다.

예측 점수는 사용자 utterance의 예측 결과에 대 한 신뢰도 LUIS의 수준을 나타냅니다.

예측 점수는 0(영)과 1(일) 사이입니다. 신뢰도가 높은 LUIS 점수의 예는 0.99입니다. 신뢰도 점수의 예는 0.01입니다. 

|점수 값|신뢰도|
|--|--|
|1|확실한 일치|
|0.99|높은 신뢰도|
|0.01|낮은 신뢰도|
|0|확실한 불일치|

## <a name="top-scoring-intent"></a>상위 점수 의도

모든 발화 예측은 상위 점수 의도를 반환합니다. 이 예측은 예측 점수의 숫자 비교입니다. 

## <a name="proximity-of-scores-to-each-other"></a>점수의 유사성

상위 2 개 점수가 매우 작은 차이를 가질 수 있습니다. LUIS는이 근접 점수를 반환 하는 것 외에는이를 나타내지 않습니다.  

## <a name="return-prediction-score-for-all-intents"></a>모든 의도의 예측 점수 반환

테스트 또는 엔드포인트 결과에는 모든 의도가 포함될 수 있습니다. 이 구성은 올바른 querystring 이름/값 쌍을 사용 하 여 엔드포인트에 설정 됩니다.

|예측 API|Querystring 이름|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>유사한 점수를 가진 의도 검토

모든 의도에 대 한 점수를 검토 하는 것은 올바른 의도가 무엇 인지를 확인 하는 좋은 방법 이지만 확인 된 다음 의도의 점수가 길이 발언에 대해 일관적이 고 일관 되 게 낮습니다.

여러 의도의 예측 점수가 가까운 경우에는 발화 컨텍스트에 따라 LUIS가 의도 간에 전환할 수 있습니다. 이러한 상황을 해결 하려면 더 광범위 한 컨텍스트 차이를 사용 하 여 각 의도에 길이 발언를 추가 하거나 채팅 봇과 같은 클라이언트 응용 프로그램을 사용 하 여 2 개의 인기를 처리 하는 방법에 대해 프로그래밍 방식으로 선택 합니다.

명확 하지 않은 두 가지 의도는 **명확 하지 않은 교육**으로 인해 반전 될 수 있습니다. 최고 점수가 두 번째로 높은 점수가 될 수 있고 두 번째로 높은 점수가 최고 점수가 될 수 있습니다. 이러한 상황을 방지 하기 위해 두 가지 의도를 차별화 하는 단어를 선택 하 고 컨텍스트를 사용 하 여 utterance에 대 한 길이 발언 예를 각 상위 두 의도에 추가 합니다. 두 가지 의도에는 동일한 수의 예제 발언이 있어야 합니다. 학습으로 인한 반전을 방지할 수 있는 일반적인 분리 기준은 15%의 점수 차이입니다.

[모든 데이터로 학습](luis-how-to-train.md#train-with-all-data)하 여 **비 결정적인 학습** 을 해제할 수 있습니다.

## <a name="differences-with-predictions-between-different-training-sessions"></a>여러 학습 세션 간의 예측 차이

다른 앱에서 동일한 모델을 학습 하 고 점수가 동일 하지 않은 경우 이러한 차이는 **명확 하지 않은 교육** (무작위성 요소)이 있기 때문입니다. 두 번째로, 둘 이상 의도의 발화가 겹치는 것은 동일 발화의 상위 의도가 학습에 따라 변경될 수 있음을 의미합니다.

채팅 봇에 특정 LUIS 점수가 필요한 경우에는 상위 두 의도 사이의 점수 차이를 사용 해야 합니다. 이러한 상황은 학습의 변화에 대 한 유연성을 제공 합니다.

[모든 데이터로 학습](luis-how-to-train.md#train-with-all-data)하 여 **비 결정적인 학습** 을 해제할 수 있습니다.

## <a name="e-exponent-notation"></a>E(지수) 표기법

예측 점수는 `9.910309E-07`과 같이 0~1 범위 위에 ‘나타나는’ 지수 표기법을 사용합니다. 이 점수는 매우 **작은** 수를 나타냅니다.

|E 표기법 점수 |실제 점수|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>문장 부호

문장 부호를 사용 하거나 무시 하는 방법에 [대해 자세히 알아보세요](luis-concept-utterance.md#punctuation-marks) . 

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
