---
title: 알고리즘 및 모듈 참조
description: 사용자 고유의 기계 학습 프로젝트를 만드는 데 사용할 수 있는 Azure Machine Learning 디자이너 모듈에 대해 알아봅니다.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94376845"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너에 대한 알고리즘 및 모듈 참조

이 참조 콘텐츠는 Azure Machine Learning 디자이너에서 사용할 수 있는 각 기계 학습 알고리즘 및 모듈에 대한 기술적 배경을 제공합니다.

각 모듈은 필요한 입력을 고려하여 독립적으로 실행되고 기계 학습 작업을 수행할 수 있는 코드 세트를 나타냅니다. 모듈은 특정 알고리즘을 포함하거나 누락된 값 대체 또는 통계 분석과 같이 기계 학습에서 중요한 작업을 수행할 수 있습니다.

알고리즘 선택에 대한 도움말은 다음을 참조하세요. 
* [알고리즘을 선택하는 방법](../how-to-select-algorithms.md)
* [Azure Machine Learning 알고리즘 치트 시트](../algorithm-cheat-sheet.md)

> [!TIP]
> 디자이너의 모든 파이프라인에서 특정 모듈에 대한 정보를 가져올 수 있습니다. 모듈 목록 또는 모듈의 오른쪽 창에서 모듈을 마우스로 가리킬 때 모듈 카드에서 **자세한 정보** 링크를 선택합니다.

## <a name="data-preparation-modules"></a>데이터 준비 모듈


| 기능 | Description | 모듈 |
| --- |--- | --- |
| 데이터 입력 및 출력 | 클라우드 소스에서 파이프라인으로 데이터를 이동합니다. 파이프라인을 실행하거나 클라우드 스토리지를 사용하여 파이프라인 간에 데이터를 교환하는 동안 결과 또는 중간 데이터를 Azure Storage, SQL 데이터베이스 또는 Hive에 씁니다.  | [수동으로 데이터 입력](enter-data-manually.md) <br/> [데이터 내보내기](export-data.md) <br/> [데이터 가져오기](import-data.md) |
| 데이터 변환 | 데이터의 표준화 또는 범주화, 차원 감소 및 다양한 파일 형식 간의 데이터 변환 등 기계 학습에 고유한 데이터에 대한 작업입니다.| [열 추가](add-columns.md) <br/> [행 추가](add-rows.md) <br/> [수학 연산 적용](apply-math-operation.md) <br/> [SQL 변환 적용](apply-sql-transformation.md) <br/> [누락 데이터 정리](clean-missing-data.md) <br/> [값 잘라내기](clip-values.md) <br/> [CSV로 변환](convert-to-csv.md) <br/> [데이터 세트로 변환](convert-to-dataset.md) <br/> [표시기 값으로 변환](convert-to-indicator-values.md) <br/> [메타데이터 편집](edit-metadata.md) <br/> [Bin으로 데이터 그룹화](group-data-into-bins.md) <br/> [데이터 조인](join-data.md) <br/> [데이터 정규화](normalize-data.md) <br/> [파티션 및 샘플](partition-and-sample.md)  <br/> [중복 행 제거](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [열 변환 선택](select-columns-transform.md) <br/> [데이터 세트의 열 선택](select-columns-in-dataset.md) <br/> [데이터 분할](split-data.md) |
| 기능 선택 | 분석 모델을 빌드하는 데 사용할 관련된 유용한 기능의 하위 집합을 선택합니다. | [필터 기반 기능 선택](filter-based-feature-selection.md) <br/> [순열 기능 중요도](permutation-feature-importance.md) |
| 통계 함수 | 데이터 과학과 관련된 다양한 통계 방법을 제공합니다. | [데이터 요약](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>기계 학습 알고리즘

| 기능 | Description | 모듈 |
| --- |--- | --- |
| 회귀 | 값을 예측합니다. | [향상된 의사 결정 트리 회귀](boosted-decision-tree-regression.md) <br/> [의사 결정 포리스트 회귀](decision-forest-regression.md) <br/> [빠른 포리스트 분위수 회귀](fast-forest-quantile-regression.md)  <br/> [선형 회귀](linear-regression.md)  <br/> [신경망 회귀](neural-network-regression.md)  <br/> [포아송 회귀](poisson-regression.md)  <br/>|
| Clustering | 데이터를 그룹화합니다.| [K-Means 클러스터링](k-means-clustering.md)
| 분류 | 클래스를 예측합니다.  이진 파일(2클래스) 또는 다중 클래스 알고리즘에서 선택합니다.| [다중 클래스 향상된 의사 결정 트리](multiclass-boosted-decision-tree.md) <br/> [다중 클래스 의사 결정 포리스트](multiclass-decision-forest.md) <br/> [다중 클래스 로지스틱 회귀](multiclass-logistic-regression.md)  <br/> [다중 클래스 신경망](multiclass-neural-network.md) <br/> [일대다 모든 다중 클래스](one-vs-all-multiclass.md) <br/> [하나와 하나의 다중 클래스 비교](one-vs-one-multiclass.md) <br/>[2클래스 평균 퍼셉트론](two-class-averaged-perceptron.md) <br/>  [2클래스 향상된 의사 결정 트리](two-class-boosted-decision-tree.md)  <br/> [2클래스 의사 결정 포리스트](two-class-decision-forest.md) <br/>  [2클래스 로지스틱 회귀](two-class-logistic-regression.md) <br/> [2클래스 신경망](two-class-neural-network.md) <br/> [2클래스 Support Vector Machine](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>모델 빌드 및 평가를 위한 모듈

| 기능 | Description | 모듈 |
| --- |--- | --- |
| 모델 학습 | 알고리즘을 통해 데이터를 실행합니다. |  [클러스터링 모델 학습](train-clustering-model.md) <br/> [모델 학습](train-model.md) <br/> [Pytorch 모델 학습](train-pytorch-model.md) <br/> [모델 하이퍼 매개 변수 튜닝](tune-model-hyperparameters.md) |
| 모델 채점 및 평가 | 학습된 모델의 정확도를 측정합니다. | [변환 적용](apply-transformation.md) <br/> [클러스터에 데이터 할당](assign-data-to-clusters.md) <br/> [모델 교차 유효성 검사](cross-validate-model.md) <br/> [모델 평가](evaluate-model.md) <br/> [이미지 모델 채점](score-image-model.md) <br/> [모델 채점](score-model.md) |
| Python 언어 | 코드를 작성하고 이를 모듈에 포함하여 Python을 파이프라인과 통합합니다. | [Python 모델 만들기](create-python-model.md) <br/> [Python 스크립트 실행](execute-python-script.md) |
| R 언어 | 코드를 작성하고 이를 모듈에 포함하여 R을 파이프라인과 통합합니다. | [R 스크립트 실행](execute-r-script.md) |
| 텍스트 분석 | 구조화된 텍스트와 구조화되지 않은 텍스트를 모두 사용하기 위한 특수한 계산 도구를 제공합니다. |  [Word를 벡터로 변환](convert-word-to-vector.md) <br/> [텍스트에서 N Gram 기능 추출](extract-n-gram-features-from-text.md) <br/> [기능 해싱](feature-hashing.md) <br/> [텍스트 전처리](preprocess-text.md) <br/> [잠재적 Dirichlet 할당](latent-dirichlet-allocation.md) <br/> [Vowpal Wabbit 모델 점수 매기기](score-vowpal-wabbit-model.md) <br/> [Vowpal Wabbit 모델 학습](train-vowpal-wabbit-model.md)|
| Computer Vision | 이미지 데이터 전처리 및 이미지 인식 관련 모듈. |  [이미지 변환 적용](apply-image-transformation.md) <br/> [이미지 디렉터리로 변환](convert-to-image-directory.md) <br/> [이미지 변환 시작](init-image-transformation.md) <br/> [이미지 디렉터리 분할](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| 권장 | 권장 사항 모델을 빌드합니다. | [추천 평가](evaluate-recommender.md) <br/> [SVD 추천 점수 매기기](score-svd-recommender.md) <br/> [와이드 및 딥 추천 점수 매기기](score-wide-and-deep-recommender.md)<br/> [SVD 추천 학습](train-SVD-recommender.md) <br/> [와이드 및 딥 추천 학습](train-wide-and-deep-recommender.md)|
| 이상 감지 | 변칙 검색 모델을 빌드합니다. | [PCA 기반 변칙 검색](pca-based-anomaly-detection.md) <br/> [변칙 검색 모델 학습](train-anomaly-detection-model.md) |


## <a name="web-service"></a>웹 서비스

Azure Machine Learning 디자이너에서 실시간 유추에 필요한 [웹 서비스 모듈](web-service-input-output.md)에 대해 알아봅니다.

## <a name="error-messages"></a>오류 메시지

Azure Machine Learning 디자이너에서 모듈을 사용하여 발생할 수 있는 [오류 메시지 및 예외 코드](designer-error-codes.md)에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [자습서: 디자이너에서 모델을 빌드하여 자동 가격 예측](../tutorial-designer-automobile-price-train-score.md)
