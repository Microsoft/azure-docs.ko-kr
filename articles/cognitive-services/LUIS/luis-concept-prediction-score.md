---
title: 예측 점수 - LUIS
description: 예측 점수는 사용자 발화를 기반으로 LUIS API 서비스가 예측 결과에 대해 갖는 신뢰도를 나타냅니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316463"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>예측 점수는 의도 및 엔터티에 대한 예측 정확도를 나타냅니다.

예측 점수는 사용자 발화의 예측 결과에 대한 LUIS의 신뢰도를 나타냅니다.

예측 점수는 0(영)과 1(일) 사이입니다. 신뢰도가 높은 LUIS 점수의 예는 0.99입니다. 신뢰도 점수의 예는 0.01입니다.

|점수 값|신뢰도|
|--|--|
|1|확실한 일치|
|0.99|높은 신뢰도|
|0.01|낮은 신뢰도|
|0|확실한 불일치|

## <a name="top-scoring-intent"></a>상위 점수 의도

모든 발화 예측은 상위 점수 의도를 반환합니다. 이 예측은 예측 점수의 수치 비교입니다.

## <a name="proximity-of-scores-to-each-other"></a>상호 점수의 유사성

상위 두 개의 점수는 서로 차이가 매우 작을 수 있습니다. LUIS는 상위 점수 반환 외에는 이 근접성을 표시하지 않습니다.

## <a name="return-prediction-score-for-all-intents"></a>모든 의도의 예측 점수 반환

테스트 또는 엔드포인트 결과에는 모든 의도가 포함될 수 있습니다. 이 구성은 올바른 쿼리 문자열 이름/값 쌍을 사용하여 엔드포인트에 설정됩니다.

|예측 API|쿼리 문자열 이름|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>유사한 점수를 가진 의도 검토

올바른 의도가 식별될 뿐만 아니라 다음 식별된 의도의 점수가 여러 발화에 일관되게 상당히 더 낮은지 확인하려면 모든 의도의 점수를 검토하는 것이 좋습니다.

여러 의도의 예측 점수가 가까운 경우에는 발화 컨텍스트에 따라 LUIS가 의도 간에 전환할 수 있습니다. 이 상황을 해결하려면 보다 다양한 상황별 차이로 각 의도에 발화를 계속 추가하거나 채팅 봇과 같은 클라이언트 애플리케이션이 두 개의 상위 의도를 처리하는 방법을 프로그래매틱으로 선택하도록 할 수 있습니다.

점수가 너무 가깝게 매겨진 두 개의 의도는 **비결정적 교육** 으로 인해 반전될 수 있습니다. 최고 점수가 두 번째로 높은 점수가 될 수 있고 두 번째로 높은 점수가 최고 점수가 될 수 있습니다. 이 상황을 방지하려면 두 가지 의도를 구분하는 상황 및 단어를 선택하여 해당 발화의 최고 두 가지 의도 각각에 대해 예제 발화를 추가합니다. 두 가지 의도에는 동일한 수의 예제 발언이 있어야 합니다. 학습으로 인한 반전을 방지할 수 있는 일반적인 분리 기준은 15%의 점수 차이입니다.

[모든 데이터로 학습](luis-how-to-train.md#train-with-all-data)시켜 **비결정적 학습** 을 해제할 수 있습니다.

## <a name="differences-with-predictions-between-different-training-sessions"></a>여러 학습 세션 간의 예측 차이

다른 앱에서 동일한 모델을 학습시키는데 점수가 동일하지 않으면 이 차이는 **비결정적 학습**(무작위 요소)이 있기 때문입니다. 두 번째로, 둘 이상 의도의 발화가 겹치는 것은 동일 발화의 상위 의도가 학습에 따라 변경될 수 있음을 의미합니다.

챗봇에 의도의 신뢰도를 나타내는 특정 LUIS 점수가 필요한 경우에는 상위 두 의도 간 점수 차이를 사용해야 합니다. 이 상황은 학습에 변형 유연성을 제공합니다.

[모든 데이터로 학습](luis-how-to-train.md#train-with-all-data)시켜 **비결정적 학습** 을 해제할 수 있습니다.

## <a name="e-exponent-notation"></a>E(지수) 표기법

예측 점수는 `9.910309E-07`과 같이 0~1 범위 위에 ‘나타나는’ 지수 표기법을 사용합니다. 이 점수는 매우 **작은** 수를 나타냅니다.

|E 표기법 점수 |실제 점수|
|--|--|
|9.910309E-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>애플리케이션 설정

[애플리케이션 설정](luis-reference-application-settings.md)을 사용하여 분음 부호와 문장 부호가 예측 점수에 영향을 미치는 방식을 제어합니다.

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
