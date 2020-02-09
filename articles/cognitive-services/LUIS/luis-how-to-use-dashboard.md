---
title: 대시보드-Language Understanding-LUIS
titleSuffix: Azure Cognitive Services
description: 학습 된 앱의 대시보드를 사용 하 여 의도 및 엔터티를 수정 합니다. 대시보드는 전체 앱 정보를 표시 하 고 수정 해야 하는 의도를 강조 표시 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888207"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>대시보드를 사용 하 여 앱을 개선 하는 방법

예제 길이 발언를 사용 하는 경우 학습 된 앱의 의도에 대 한 문제를 찾아 해결 합니다. 대시보드는 전체 앱 정보를 표시 하 고 수정 해야 하는 의도를 강조 표시 합니다. 

검토 대시보드 분석은 반복적인 프로세스 이므로 모델을 변경 하 고 개선할 때 반복 합니다.

이 페이지에는 _패턴 전용_ 앱 이라고 하는 길이 발언 예제를 포함 하지 않는 앱에 대 한 관련 분석이 없습니다. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>대시보드에서 어떤 문제를 해결할 수 있나요?

대시보드에서 해결 된 세 가지 문제는 다음과 같습니다.

|문제|차트 색|설명|
|--|--|--|
|데이터 불균형|-|이는 예 길이 발언의 수량이 현저 하 게 다를 때 발생 합니다. 모든 의도에는 _거의_ 동일한 수의 예 길이 발언 (없음)를 포함 해야 합니다. 앱에서 길이 발언의 총 수량에 대해 10%-15%만 있어야 합니다.<br><br> 데이터의 균형이 맞지 않지만 의도 정확도가 특정 임계값을 초과 하는 경우 이러한 불균형은 문제로 보고 되지 않습니다.<br><br>**이 문제부터 시작 하 여 다른 문제의 근본 원인일 수 있습니다.**|
|명확 하지 않은 예측|주황색|이는 [부정적인 샘플링](luis-how-to-train.md#train-with-all-data) 또는 더 많은 예제 길이 발언가 의도에 추가 되었기 때문에 다음 학습에 대해 전환할 수 있을 만큼의 최고 도와 다음 의도의 점수가 가까이 있을 때 발생 합니다. |
|잘못 된 예측|빨강|이는 utterance 예제에서 레이블이 지정 된 의도 (의 의도)에 대해 예측 하지 않을 때 발생 합니다.|

올바른 예측은 파란색으로 표시 됩니다.

대시보드는 이러한 문제를 표시 하 고 영향을 받는 의도를 알려 주고 앱을 개선 하기 위해 수행할 작업을 제안 합니다. 

## <a name="before-app-is-trained"></a>앱 학습 전 

앱을 학습 하기 전에 대시보드는 수정 제안을 포함 하지 않습니다. 앱을 학습 하 여 이러한 제안을 확인 하세요.  

## <a name="check-your-publishing-status"></a>게시 상태 확인

**게시 상태** 카드에는 활성 버전의 마지막 게시에 대 한 정보가 포함 되어 있습니다. 

활성 버전이 수정 하려는 버전 인지 확인 합니다. 

![대시보드는 앱의 외부 서비스, 게시 된 지역 및 집계 된 엔드포인트 적중을 표시 합니다.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

여기에는 외부 서비스, 게시 된 지역 및 집계 된 엔드포인트 적중도 표시 됩니다. 

## <a name="review-training-evaluation"></a>학습 평가 검토

**학습 평가** 카드에는 앱의 전체 정확도에 대 한 집계 요약이 포함 되어 있습니다. 점수는 의도 품질을 나타냅니다. 

![학습 평가 카드에는 앱의 전반적인 정확성에 대 한 첫 번째 정보 영역이 포함 되어 있습니다.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

차트는 정확한 예측 의도 및 다른 색의 문제 영역을 나타냅니다. 제안에 따라 앱을 개선 하면서 이러한 점수가 증가 합니다. 

권장 픽스는 문제 유형별로 구분 되며 앱에 가장 중요 합니다. 발생 한 문제를 검토 하 고 수정 하려면 페이지 맨 아래에 오류 카드를 사용 하는 **[의도](#intents-with-errors)** 를 사용 합니다. 

각 문제 영역에는 수정이 필요한 의도가 있습니다. 의도 이름을 선택 하면 길이 발언에 필터가 적용 된 **의도** 페이지가 열립니다. 이 필터를 사용 하면 문제를 일으킨 길이 발언에 집중할 수 있습니다.

### <a name="compare-changes-across-versions"></a>버전 간 변경 내용 비교

앱을 변경 하기 전에 새 버전을 만듭니다. 새 버전에서 의도의 예제 길이 발언에 대 한 제안 사항을 변경한 후 다시 학습 합니다. 대시보드 페이지의 **학습 평가** 카드에서 **학습 된 버전의 변경 내용 표시** 를 사용 하 여 변경 내용을 비교 합니다. 

![버전 간 변경 내용 비교](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>길이 발언 및 재 학습 예제를 추가 또는 편집 하 여 버전을 수정 합니다.

앱을 수정 하는 주요 방법은 길이 발언 및 다시 학습 예제를 추가 하거나 편집 하는 것입니다. 새로운 또는 변경 된 길이 발언는 [다양 한 길이 발언](luis-concept-utterance.md)에 대 한 지침을 따라야 합니다.

예제 길이 발언를 추가 하는 작업은 다음을 수행 하는 사용자가 수행 해야 합니다.

* 는 다른 의도에 있는 길이 발언에 대해 높은 수준의 이해를 제공 합니다.
* 한 의도의 길이 발언 다른 의도와 혼동 될 수 있는 방법을 알고 있습니다.
* 는 서로 혼동 하는 두 가지 의도를 단일 의도로 축소 해야 하는지 여부를 결정할 수 있습니다. 이 경우 다른 데이터를 엔터티로 가져와야 합니다.

### <a name="patterns-and-phrase-lists"></a>패턴 및 문구 목록

분석 페이지는 [패턴이](luis-concept-patterns.md) 나 [문구 목록을](luis-concept-feature.md)사용할 시기를 나타내지 않습니다. 이러한 항목을 추가 하는 경우 잘못 되었거나 명확 하지 않은 예측에 도움이 되지만 데이터 불균형에는 도움이 되지 않습니다. 

### <a name="review-data-imbalance"></a>데이터 불균형 검토

이 문제부터 시작 하 여 다른 문제의 근본 원인일 수 있습니다.

데이터 **불균형** 의도 목록에는 데이터 불균형을 수정 하기 위해 더 많은 길이 발언이 필요한 의도가 표시 됩니다. 

**이 문제를 해결 하려면**:

* 길이 발언를 더 추가 하 고 다시 학습 합니다. 

대시보드에 제안 된 경우를 제외 하 고는 없음 길이 발언를 추가 하지 마세요.

> [!Tip]
> **길이 발언 (number)** 설정으로 **길이 발언** 에 대 한 페이지의 세 번째 섹션을 사용 하 여 더 많은 길이 발언를 필요로 하는 의도에 대 한 빠른 시각적 가이드를 참조 하세요.  
    ' 길이 발언 (number) '를 사용 하 여 데이터 불균형의 의도를 찾을 ![.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>잘못 된 예측 검토

**잘못 된 예측** 의도 목록에는 특정 의도에 대 한 예제로 사용 되지만 다른 의도에 대해 예측 되는 길이 발언가 있는 의도가 표시 됩니다. 

**이 문제를 해결 하려면**:

* 길이 발언를 편집 하 고 다시 학습 합니다.
* 길이 발언이 너무 밀접 하 게 정렬 되 고 다시 학습 하는 경우 의도를 결합 합니다.

### <a name="review-unclear-predictions"></a>명확 하지 않은 예측 검토

명확 하지 않은 **예측** 의도 목록은 길이 발언에 가장 가까운 경쟁에서 충분 하지 않은 예측 점수를 사용 하는 의도를 보여 줍니다. [부정 샘플링](luis-how-to-train.md#train-with-all-data)으로 인 한 utterance에 대 한 상위 의도가 다음 학습에서 변경 될 수 있습니다.

**이 문제를 해결 하려면**

* 길이 발언를 편집 하 고 다시 학습 합니다.
* 길이 발언이 너무 밀접 하 게 정렬 되 고 다시 학습 하는 경우 의도를 결합 합니다.

## <a name="utterances-per-intent"></a>길이 발언

이 카드는 해당 의도에 전체 앱 상태를 표시 합니다. 의도 및 다시 학습을 수정 하는 경우이 카드에서 문제를 계속 살펴보세요.

다음 차트는 해결할 문제가 거의 없는 잘 조정 된 앱을 보여 줍니다.

![다음 차트는 해결할 문제가 거의 없는 잘 조정 된 앱을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

다음 차트에서는 많은 문제를 해결 해야 하는 균형이 맞지 않는 앱을 보여 줍니다.

![다음 차트는 해결할 문제가 거의 없는 잘 조정 된 앱을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

각 의도의 막대를 마우스로 가리켜서 의도에 대 한 정보를 얻습니다. 

![다음 차트는 해결할 문제가 거의 없는 잘 조정 된 앱을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

**정렬 기준** 기능을 사용 하 여 문제 유형별 의도를 정렬 하 고 해당 문제에 대 한 가장 문제가 있는 의도에 집중할 수 있습니다. 

## <a name="intents-with-errors"></a>오류가 있는 의도

이 카드를 사용 하 여 특정 의도에 대 한 문제를 검토할 수 있습니다. 이 카드의 기본 보기는 문제에 대 한 가장 중요 한 것 이므로 노력에 집중할 수 있는 위치를 알 수 있습니다.

![오류가 있는 의도 카드를 사용 하면 특정 의도에 대 한 문제를 검토할 수 있습니다. 기본적으로 카드는 문제가 가장 많은 의도에 따라 필터링 되므로 노력에 집중할 수 있는 위치를 알 수 있습니다.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

위쪽 도넛형 차트는 세 가지 문제 유형에 서의 의도와 관련 된 문제를 보여 줍니다. 세 가지 문제 유형에 문제가 있는 경우 각 형식에는 아래와 같은 자체 차트가 있으며 모든 경쟁 요소가 있습니다. 

### <a name="filter-intents-by-issue-and-percentage"></a>문제 및 백분율 별 필터 의도

카드의이 섹션에서는 오류 임계값을 벗어나 길이 발언 예제를 찾을 수 있습니다. 이상적으로 올바른 예측이 중요 하 게 하려는 것이 좋습니다. 이 비율은 비즈니스 및 고객 중심입니다. 

비즈니스에 적합 한 임계값 비율을 결정 합니다. 

필터를 사용 하 여 특정 문제에 대 한 의도를 찾을 수 있습니다.

|Filter|제안 된 비율|목적|
|--|--|--|
|가장 문제가 있는 의도|-|**여기부터 시작** -이 의도에서 길이 발언를 수정 하면 앱이 다른 픽스 보다 더 향상 됩니다.|
|아래에서 올바른 예측|60%|이 값은 선택 된 의도에서 올바르지만 정확도 점수가 임계값 보다 길이 발언 비율입니다. |
|위의 명확 하지 않은 예측|15%|선택한 의도에서 가장 가까운 경쟁 길이 발언 혼동 되는 백분율입니다.|
|위의 잘못 된 예측|15%|선택 된 의도에서 잘못 예측 된 길이 발언의 백분율입니다. |

### <a name="correct-prediction-threshold"></a>올바른 예측 임계값

자신에 게 확신 하는 예측 신뢰성 점수는 무엇 인가요? 앱 개발이 시작 될 때 60%가 대상일 수 있습니다. 선택 된 의도에서 수정 해야 하는 길이 발언을 찾으려면 60%의 백분율로 **올바른 예측** 을 사용 합니다.

### <a name="unclear-or-incorrect-prediction-threshold"></a>명확 하지 않거나 잘못 된 예측 임계값

이러한 두 필터를 사용 하 여 임계값을 초과 하 여 선택한 의도에서 길이 발언을 찾을 수 있습니다. 이러한 두 백분율을 오류 비율로 간주할 수 있습니다. 예측에 10-15% 오류 비율에 대해 어려우면 필터 임계값을 15%로 설정 하 여이 값 위의 모든 길이 발언를 찾습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리](luis-how-to-azure-subscription.md)
