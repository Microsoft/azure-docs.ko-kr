---
title: 초급자를 위한 데이터 과학
titleSuffix: Azure Machine Learning Studio
description: 초급자를 위한 데이터 과학에서는 데이터 과학으로 답변할 수 있는 5가지 질문부터 시작해서 5가지 짧은 비디오를 통해 기본적인 개념을 설명합니다. Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: d89a701f1d4528e1f3dff08daf31873891778f07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751393"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>초급자를 위한 데이터 과학 비디오 1: 데이터 과학으로 답변할 수 있는 5가지 질문
최고의 데이터 과학자가 제공하는 5개의 짧은 비디오로 구성된 *초급자를 위한 데이터 과학*에서 데이터 과학을 빠르게 살펴보세요. 이러한 비디오는 기초적이지만 데이터 과학에 관심이 있든 데이터 과학자와 함께 일하든 상관없이 유용한 정보를 제공합니다.

이 첫 번째 비디오는 데이터 과학이 대답할 수 있는 종류의 질문에 대한 것입니다. 시리즈를 최대한 활용하려면 모두 시청하는 것이 좋습니다. [비디오 목록으로 이동](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>이 시리즈의 다른 비디오
*초급자를 위한 데이터 과학* 은 총 25분 동안 데이터 과학을 간략히 소개합니다. 5개의 비디오를 모두 확인해 보세요.

* 비디오 1: 데이터 과학으로 답변할 수 있는 5가지 질문
* 비디오 2: [데이터 과학에 사용할 수 있게 데이터가 준비되었나요?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4분 56초)*
* 비디오 3: [데이터로 대답할 수 있는 질문하기](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4분 17초)*
* 비디오 4: [단순 모델을 사용하여 답변 예측](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7분 42초)*
* 비디오 5: [데이터 과학을 수행하기 위해 다른 사람의 작품 복사](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3분 18초)*

## <a name="transcript-the-5-questions-data-science-answers"></a>대본: 데이터 과학으로 답변할 수 있는 5가지 질문
안녕하세요. 비디오 시리즈 *초급자를 위한 데이터 과학*에 오신 것을 환영합니다.

데이터 과학이 어렵게 느껴질 수 있으므로, 여기서는 방정식이나 컴퓨터 프로그래밍 특수 용어 없이 기본 개념을 소개하겠습니다.

이 첫 번째 비디오에서는 "데이터 과학으로 답변할 수 있는 5가지 질문"에 대해 살펴보겠습니다.

데이터 과학은 숫자와 이름(범주 또는 레이블이라고도 함)을 사용하여 질문에 대한 답변을 예측합니다.

뜻밖일 수도 있지만 *데이터 과학으로 답변되는 질문은 다음 5개뿐입니다*.

* 이것은 A인가요 B인가요?
* 이것은 이상한가요?
* 양 또는 개수는 얼마인가요?
* 어떻게 구성되어 있나요?
* 다음에는 어떻게 해야 하나요?

이러한 각 질문은 알고리즘이라고 하는 별도의 기계 학습 방법군에 따라 답변됩니다.

알고리즘을 레시피로, 데이터를 재료로 생각하면 도움이 될 것입니다. 알고리즘은 답변을 얻기 위해 데이터를 조합하고 혼합하는 방법을 설명합니다. 컴퓨터는 믹서기와 같습니다. 알고리즘의 어려운 작업 대부분을 꽤 빠르게 처리합니다.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>질문 1: 이것은 A인가요 B인가요?에서는 분류 알고리즘을 사용합니다.
먼저 질문을 사용하여 시작해 보겠습니다. 이것은 A인가요 B인가요?

![분류 알고리즘: 이것은 A인가요 B인가요?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

이 알고리즘군을 2클래스 분류라고 합니다.

두 가지 가능한 답변이 있는 질문에 유용합니다.

예를 들면 다음과 같습니다.

* 이 타이어는 다음 1,000마일을 가는 동안 터질까요? 예 또는 아니요?
* 어떤 방식이 더 많은 고객을 이끌까요? 5달러 쿠폰 또는 25% 할인?

이 질문을 3개 이상의 옵션을 포함하도록 이것은 A인가요? B인가요? C인가요? D인가요? 등과 같이 다시 쓸 수 있습니다.  이것을 다중 클래스 분류라고 하며 이 분류 방식은 몇 개 또는 몇천 개의 가능한 답변이 있을 때 유용합니다. 다중 클래스 분류는 가능성이 가장 높은 하나를 선택합니다.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>질문 2: 이것은 이상한가요?는 변칙 검색 알고리즘을 사용합니다.
데이터 과학으로 답변을 얻을 수 있는 다음 질문은 다음과 같습니다. 이것은 이상한가요? 이 질문은 변칙 감지라고 하는 알고리즘군을 통해 답변됩니다.

![변칙 검색 알고리즘: 이것은 이상한가요?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

신용 카드가 있는 경우 이미 변칙 감지를 통한 혜택을 보고 있는 것입니다. 여러분의 신용 카드 회사는 구매 패턴을 분석하므로 가능한 사기 행위를 여러분에게 경고할 수 있습니다. "이상하게" 요금이 청구되었다면 평소에 물건을 구매하지 않던 매장에서 구매하거나 굉장히 비싼 가격의 상품을 구매해서일 수 있습니다.

이 질문은 다양한 측면에서 유용할 수 있습니다. 예:

* 자동차에 압력 계기판이 있는 경우 이 압력 계기판에 정상적인 수치가 표시되는지 알고 싶을 수 있습니다.
* 인터넷을 모니터링하는 경우 다음을 알고 싶습니다. 인터넷의 이 메시지가 정상적인가요?

변칙 감지는 예기치 않거나 비정상적인 이벤트 또는 동작에 플래그를 지정합니다. 어디서 문제점을 찾아내야 할지에 대한 단서를 줍니다.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>질문 3: 양 또는 개수는 얼마인가요?에서는 회귀 알고리즘을 사용합니다.
또한 기계 학습을 통해 양 또는 개수는 얼마인가요?에 대한 답변을 예측할 수 있습니다. 이 질문에 답변하는 알고리즘군을 회귀라고 합니다.

![회귀 알고리즘: 양 또는 개수는 얼마인가요?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

회귀 알고리즘은 다음과 같은 수치 예측을 수행합니다.

* 다음 화요일의 기온은 얼마나 될까요?  
* 4사분기 매출은 얼마나 될까요?

수치를 요구하는 질문에 답변하는 데 도움이 됩니다.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>질문 4: 어떻게 구성되어 있나요?에서는 클러스터링 알고리즘을 사용합니다.
마지막 두 개의 질문은 좀 더 수준이 높습니다.

데이터 집합의 구조를 이해하고 싶을 수 있습니다. 이러한 경우 어떻게 구성되어 있나요?라고 질문할 수 있습니다. 이 질문의 경우 여러분이 결과를 이미 알고 있는 예제는 없을 것입니다.

데이터 구조를 조작할 수 있는 방법이 많이 있습니다. 한 가지 방법은 클러스터링입니다. 이 방식은 보다 쉬운 해석을 위해 데이터를 적절한 "그룹"으로 분리합니다. 클러스터링을 사용하면 한 가지 정답은 없습니다.

![클러스터링 알고리즘: 어떻게 구성되어 있나요?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

질문을 클러스터링하는 일반적인 예제는 다음과 같습니다.

* 어떤 시청자들이 같은 종류의 영화를 좋아하나요?
* 어떤 프린터 모델에서 동일한 방식으로 오류가 발생하나요?

데이터가 구성된 방식을 이해하면 동작 및 이벤트를 더 잘 이해하고 예측할 수 있게 됩니다.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>질문 5: 이제 어떻게 해야 하나요?에서는 강화 학습 알고리즘을 사용합니다.
이제 어떻게 해야 하나요?라는 마지막 질문에서는 강화 학습이라는 알고리즘군을 사용합니다.

강화 학습은 쥐와 인간의 뇌가 벌과 보상에 반응하는 방식에서 영감을 얻었습니다. 이러한 알고리즘은 결과를 통해 학습하고 다음 동작을 결정합니다.

일반적으로 강화 학습은 사람의 지시 없이 많은 사소한 결정을 내려야 하는 자동화 시스템에 적합합니다.

![강화 학습 알고리즘: 다음에는 어떻게 해야 하나요?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

답변되는 질문은 일반적으로 기계나 로봇에 의해 진행되는 작업에 대한 것입니다. 예:

* 내가 주택의 온도 제어 시스템이라면 온도를 조절할까요? 아니면 현재 상태로 둘까요?  
* 내가 자동 주행 자동차라면 노란색 신호등에서 브레이크를 밟을까요? 아니면 가속 패달을 밟을까요?  
* 로봇 진공청소기의 경우 계속 청소를 할까요? 아니면 충전 스테이션으로 되돌아갈까요?

강화 학습 알고리즘은 진행하면서 데이터를 수집하고 시행 착오를 통해 학습합니다.

지금까지 데이터 과학으로 답변할 수 있는 5가지 질문에 대해 알아보았습니다.

## <a name="next-steps"></a>다음 단계
* [Machine Learning Studio로 첫 번째 데이터 과학 실험 시도](create-experiment.md)
* [Microsoft Azure의 Machine Learning 소개 보기](/azure/machine-learning/preview/overview-what-is-azure-ml)
