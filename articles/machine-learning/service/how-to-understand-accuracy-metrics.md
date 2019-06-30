---
title: 자동화 된 기계 학습의 정확도 메트릭 학습
titleSuffix: Azure Machine Learning service
description: 각 프로그램 실행에 대 한 정확도 메트릭을 학습 하는 자동화 된 컴퓨터에 알아봅니다.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 44dfa387b289afe4dc5f030cca0b13325c04e811
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313276"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>메트릭 사용 하 여 자동화 된 기계 학습에서 학습 정확도 평가 합니다.

여러 가지 방법으로 각 실행된 반복에 대 한 학습 정확도 메트릭을 볼 수 있습니다.

* 사용 하 여 [Jupyter 위젯](how-to-track-experiments.md#view-run-details)
* 사용 하 여 [는 `get_metrics()` 함수](how-to-track-experiments.md#query-run-metrics) 에서 `Run` 개체
* 보기 [Azure portal에서 실험 메트릭](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="classification-metrics"></a>분류 메트릭

다음 메트릭은 분류 작업에 대 한 실행된 각 반복에 저장 됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
AUC_Macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Micro(마이크로)는 각 클래스의 참 긍정과 거짓 긍정을 결합하여 전역적으로 컴퓨팅됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 점수의 산술 평균입니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |없음|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Macro는 각 클래스 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Micro(마이크로)는 각 차단에서의 참 긍정과 거짓 긍정을 결합하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스에 대한 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Macro(매크로)는 각 클래스에 대한 F1 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Micro(마이크로)는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이 함수는 (다항) 로지스틱 회귀와 그 확장(예: 신경망)에 사용되는 손실 함수로, 지정된 확률적 분류자 예측에 대한 true 레이블의 음수 로그 유사도로 정의됩니다. {0,1}에서 true 레이블 yt와 예상 확률 yp를 갖는 단일 샘플에서 yt = 1인 경우 로그 손실은 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|없음|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이 값은 norm_macro_recall := (recall_score_macro - R)/(1 - R)로 얻습니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro", (recall_score_macro - R)/(1 - R)이 됩니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|
precision_score_macro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 부정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Weighted accuracy(가중 정확도)는 각 예제에 제공된 가중치가 해당 예제의 true 클래스에 있는 true 인스턴스의 비율과 같아지는 정확도입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

## <a name="regression-and-forecasting-metrics"></a>회귀 및 예측 메트릭

다음 메트릭은 회귀 또는 예측 작업에 대 한 실행된 각 반복에 저장 됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|없음|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|없음|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. \- 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|없음|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|없음|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|없음|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|없음|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|없음|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|
