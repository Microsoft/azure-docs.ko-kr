---
title: 데이터가 대답할 수 있는 질문하기
titleSuffix: Azure Machine Learning Studio
description: 초급자를 위한 데이터 과학 비디오 3에서 정확한 데이터 과학 질문을 작성하는 방법을 알아봅니다. 분류 및 회귀 질문에 대한 비교가 포함되어 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 815aeb9ae6036f08fc87e41980a623e2be8414e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751260"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>데이터로 대답할 수 있는 질문하기
## <a name="video-3-data-science-for-beginners-series"></a>비디오 3: 초급자를 위한 데이터 과학 시리즈
초급자를 위한 데이터 과학 비디오 3에서 데이터 과학 문제를 질문으로 작성하는 방법을 알아봅니다. 이 비디오에는 분류 및 회귀 알고리즘 질문에 대한 비교가 포함되어 있습니다.

시리즈를 최대한 활용하려면 모두 시청하는 것이 좋습니다. [비디오 목록으로 이동](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>이 시리즈의 다른 비디오
*초급자를 위한 데이터 과학* 은 다섯 개의 짧은 비디오를 통해 데이터 과학을 간략히 소개합니다.

* 비디오 1: [데이터 과학으로 답변할 수 있는 5가지 질문](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5분 14초)*
* 비디오 2: [데이터 과학에 사용할 수 있게 데이터가 준비되었나요?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4분 56초)*
* 비디오 3: 데이터로 대답할 수 있는 질문하기
* 비디오 4: [단순 모델을 사용하여 답변 예측](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7분 42초)*
* 비디오 5: [데이터 과학을 수행하기 위해 다른 사람의 작품 복사](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3분 18초)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>대본: 데이터로 대답할 수 있는 질문하기
"초급자를 위한 데이터 과학" 시리즈 중 3번째 비디오를 시작합니다.  

여기에서는 데이터로 답할 수 있는 질문을 작성하는 방법과 관련된 몇 가지 팁을 얻을 수 있습니다.

이 시리즈의 이전 두 개의 비디오를 먼저 시청하면 이 비디오를 최대한 활용할 수 있습니다. "데이터 과학으로 답변할 수 있는 5가지 질문" 및 "데이터 과학에 사용할 수 있게 데이터가 준비되었나요?"

## <a name="ask-a-sharp-question"></a>정확하게 질문하기
데이터 과학이 왜 이름(범주 또는 레이블)과 숫자를 사용하여 질문에 대한 답변을 예측하는 프로세스인가에 대해 알아보았습니다. 하지만 아무 질문이나 되는 것은 아니고 *정확한 질문*

모호한 질문에는 이름이나 숫자로 답변할 필요가 없습니다. 하지만 정확한 질문에는 그렇게 해야만 합니다.

묻는 질문에 정직하게 답하는 지니의 요술 램프를 찾았다고 상상해 보겠습니다. 하지만 짓궂은 지니는, 빠져나갈 구멍을 만들기 위해 모호하고 혼란스러운 답을 주려고 할 것입니다. 빈틈없이 완벽한 질문으로 그를 꼼짝 못하게 하면, 원하는 것을 알려주지 않고는 못 배길 것입니다.

“내 주식이 어떻게 될까요?”라는 모호한 질문을 한다면, 지니는 “가격이 변할 것입니다.”라고 답변할 것입니다. 정직한 답변이지만 별로 도움은 되지 않습니다.

하지만, “다음 주 내 주식의 판매 가격은 얼마가 될까요?”라고 정확하게 질문하면, 지니는 구체적인 답을 주고 판매 가격을 예측해 주지 않고는 못 배길 것입니다.

## <a name="examples-of-your-answer-target-data"></a>답변에 대한 예제: 대상 데이터
질문을 작성하고 난 후, 답변에 대한 사례가 데이터에 있는지를 확인합니다.

질문이 “다음 주 내 주식의 판매 가격은 얼마가 될까요?”인 경우 데이터에 주식 가격 기록이 포함되어 있는지 확인해야 합니다.

질문이 "내가 보유한 자동차 중 가장 먼저 고장 날 자동차는 어떤 것일까요"인 경우 데이터에 이전 고장에 대한 정보가 포함되어 있는지 확인해야 합니다.

![대상 데이터 - 답변에 대한 사례. 데이터 과학 질문 작성.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

답변에 대한 이러한 사례를 대상이라고 부릅니다. 대상은 향후 데이터 요소에 대해 예측하려고 하는 것입니다. 이것은 범주나 숫자가 될 수도 있습니다.

대상 데이터가 없으면, 확보해야 합니다. 이러한 데이터가 없으면 질문에 답변할 수 없습니다.

## <a name="reformulate-your-question"></a>질문 다시 작성
보다 유용한 답변을 얻기 위해서 질문을 바꾸어 말할 수 있습니다.

“이것은 데이터 요소 A입니까? 아니면 B입니까?”라는 질문은 범주(또는 이름 또는 레이블)를 예측합니다. 여기에 답변하려면 *분류 알고리즘*을 사용합니다.

"얼마?" 또는 "몇 개?"라는 질문은 수량을 예측합니다. 여기에 답변하려면 *회귀 알고리즘*을 사용합니다.

이러한 질문에서 말을 바꾸는 방법을 알아보기 위해, “새 스토리 중에서 독자들에게 가장 흥미로운 것은 무엇입니까?”라는 질문을 살펴보겠습니다. 이것은 다수의 가능성 중에서 하나를 예측하여 선택하도록 요청합니다. 다시 말해, 이것은 A 또는 B 또는 C 또는 D 중에 무엇입니까?라는 질문이며, 분류 알고리즘을 사용합니다.

하지만, “이 목록에 있는 스토리가 독자들에게 얼마나 흥미롭습니까?”라고 말을 바꾸면, 쉬운 질문이 될 수 있습니다. 이제 각 스토리에 숫자 점수를 부여하면, 최고 득점을 한 스토리를 식별하는 것이 쉬워집니다. 이것이 분류 질문을 회귀 질문 또는 얼마나?로 바꾸어 말하는 것입니다.

![질문 다시 작성. 분류 질문 및 회귀 질문.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

질문을 하는 방식은 답을 줄 수 있는 알고리즘을 알아내는 단서입니다.

특정한 알고리즘 군은(새 스토리 사례에 언급된 것과 같은) 밀접하게 관련되어 있습니다. 가장 유용한 답변을 주는 알고리즘을 사용하기 위해 질문을 다시 작성할 수 있습니다.

하지만, 가장 중요한 것은, 정확한 질문 즉, 데이터로 답할 수 있는 질문을 하는 것입니다. 그리고 그에 답할 수 있는 올바른 데이터가 있어야 합니다.

데이터로 답할 수 있는 질문의 기본적인 원리에 대해 알아보았습니다.

Microsoft Azure Machine Learning Studio의 “초급자를 위한 데이터 과학”에 포함된 다른 비디오도 확인해보세요.

## <a name="next-steps"></a>다음 단계
* [Machine Learning Studio로 첫 번째 데이터 과학 실험 시도](create-experiment.md)
* [Microsoft Azure의 Machine Learning 소개 보기](/azure/machine-learning/preview/overview-what-is-azure-ml)
