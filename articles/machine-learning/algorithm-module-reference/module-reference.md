---
title: 알고리즘 및 모듈 참조
description: Azure Machine Learning 디자이너에서 사용할 수 있는 모듈에 대 한 자세한 내용 (미리 보기)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: d3feb62c0c7fa24dd998add08d17ebd1d4e9ee6c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162585"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너에 대 한 알고리즘 & 모듈 참조

이 참조 콘텐츠는 Azure Machine Learning designer (미리 보기)에서 사용할 수 있는 각 기계 학습 알고리즘 및 모듈에 대 한 기술 배경을 제공 합니다.

각 모듈은 필요한 입력을 고려 하 여 독립적으로 실행 하 고 machine learning 작업을 수행할 수 있는 코드 집합을 나타냅니다. 모듈은 특정 알고리즘을 포함하거나 누락된 값 대체 또는 통계 분석과 같이 기계 학습에서 중요한 작업을 수행할 수 있습니다.

알고리즘 선택에 대 한 도움말은 다음을 참조 하세요. 
* [알고리즘을 선택 하는 방법](../how-to-select-algorithms.md)
* [Azure Machine Learning 알고리즘 참고 자료 시트](../algorithm-cheat-sheet.md)

> [!TIP]
> 디자이너의 모든 파이프라인에서 특정 모듈에 대 한 정보를 가져올 수 있습니다. 모듈을 선택한 다음, **빠른 도움말** 창에서 **추가 도움말** 링크를 선택합니다.

## <a name="data-preparation-modules"></a>데이터 준비 모듈


| 기능 | Description | 모듈 |
| --- |--- | --- |
| 데이터 입력 및 출력 | 클라우드 소스에서 파이프라인으로 데이터를 이동 합니다. 파이프라인을 실행 하거나 클라우드 저장소를 사용 하 여 파이프라인 간에 데이터를 교환 하는 동안 결과 또는 중간 데이터를 Azure Storage, SQL database 또는 Hive에 씁니다.  | [수동으로 데이터 입력](enter-data-manually.md) <br/> [데이터 내보내기](export-data.md) <br/> [데이터 가져오기](import-data.md) |
| 데이터 변환 | 데이터 표준화 또는 범주화, 차원 감소, 다양 한 파일 형식 간에 데이터 변환 등 기계 학습에 고유한 데이터에 대 한 작업입니다.| [열 추가](add-columns.md) <br/> [행 추가](add-rows.md) <br/> [수학 연산 적용](apply-math-operation.md) <br/> [SQL 변환 적용](apply-sql-transformation.md) <br/> [누락 데이터 정리](clean-missing-data.md) <br/> [값 자르기](clip-values.md) <br/> [CSV로 변환](convert-to-csv.md) <br/> [데이터 집합으로 변환](convert-to-dataset.md) <br/> [표시기 값으로 변환](convert-to-indicator-values.md) <br/> [메타 데이터 편집](edit-metadata.md) <br/> [데이터 조인](join-data.md) <br/> [데이터 정규화](normalize-data.md) <br/> [파티션 및 샘플](partition-and-sample.md)  <br/> [중복 행 제거](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [열 변환 선택](select-columns-transform.md) <br/> [데이터 집합에서 열 선택](select-columns-in-dataset.md) <br/> [데이터 분할](split-data.md) |
| 기능 선택 | 분석 모델을 작성 하는 데 사용할 관련 된 유용한 기능의 하위 집합을 선택 합니다. | [필터 기반 기능 선택](filter-based-feature-selection.md) <br/> [순열 기능 중요도](permutation-feature-importance.md) |
| 통계 함수 | 데이터 과학과 관련 된 다양 한 통계 방법을 제공 합니다. | [데이터 요약](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>기계 학습 알고리즘

| 기능 | Description | 모듈 |
| --- |--- | --- |
| 회귀 | 값을 예측 합니다. | [승격 된 의사 결정 트리 회귀](boosted-decision-tree-regression.md) <br/> [의사 결정 포리스트 회귀](decision-forest-regression.md) <br/> [선형 회귀](linear-regression.md)  <br/> [신경망 회귀](neural-network-regression.md)  <br/> |
| Clustering | 데이터를 그룹화 합니다.| [K-클러스터링을 의미 합니다.](k-means-clustering.md)
| 분류 | 클래스를 예측 합니다.  이진 파일 (2 클래스) 또는 다중 클래스 알고리즘에서 선택 합니다.| [다중 클래스 승격 된 의사 결정 트리](multiclass-boosted-decision-tree.md) <br/> [다중 클래스 의사 결정 포리스트](multiclass-decision-forest.md) <br/> [다중 클래스 로지스틱 회귀](multiclass-logistic-regression.md)  <br/> [다중 클래스 신경망](multiclass-neural-network.md) <br/> [One 및 All 다중 클래스](one-vs-all-multiclass.md) <br/> [2 클래스 평균 퍼셉트론](two-class-averaged-perceptron.md) <br/>  [2클래스 향상된 의사 결정 트리](two-class-boosted-decision-tree.md)  <br/> [2 클래스 의사 결정 포리스트](two-class-decision-forest.md) <br/>  [2 클래스 로지스틱 회귀](two-class-logistic-regression.md) <br/> [2 클래스 신경망](two-class-neural-network.md) <br/> [2 클래스 지원 벡터 컴퓨터](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>모델 작성 및 평가를 위한 모듈

| 기능 | Description | 모듈 |
| --- |--- | --- |
| 모델 학습 | 알고리즘을 통해 데이터를 실행 합니다. |  [클러스터링 모델 학습](train-clustering-model.md) <br/> [모델 학습](train-model.md)  <br/> [모델 하이퍼 매개 변수 조정](tune-model-hyperparameters.md) |
| 모델 점수 매기기 및 평가 | 학습 된 모델의 정확도를 측정 합니다. | [변환 적용](apply-transformation.md) <br/> [클러스터에 데이터 할당](assign-data-to-clusters.md) <br/> [모델 교차 유효성 검사](cross-validate-model.md) <br/> [모델 평가](evaluate-model.md) <br/> [모델 점수 매기기](score-model.md) |
| Python 언어 | 코드를 작성 하 고이를 모듈에 포함 하 여 Python을 파이프라인과 통합할 수 있습니다. | [Python 모델 만들기](create-python-model.md) <br/> [Python 스크립트 실행](execute-python-script.md) |
| R 언어 | 코드를 작성 하 고 모듈에 포함 하 여 R을 파이프라인과 통합할 수 있습니다. | [R 스크립트 실행](execute-r-script.md) |
| 텍스트 분석 | 구조화 된 텍스트와 구조화 되지 않은 텍스트를 모두 사용 하기 위한 특수 한 계산 도구를 제공 합니다. | [텍스트에서 N 개의 문법 기능 추출](extract-n-gram-features-from-text.md) <br/> [기능 해시](feature-hashing.md) <br/> [텍스트 전처리](preprocess-text.md) |
| 권장 | 권장 사항 모델을 작성 합니다. | [추천 평가](evaluate-recommender.md) <br/> [.SVD 추천 점수 매기기](score-svd-recommender.md) <br/> [.SVD 추천 학습](train-SVD-recommender.md) |

## <a name="error-messages"></a>오류 메시지

Azure Machine Learning 디자이너에서 모듈을 사용 하 여 발생할 수 있는 [오류 메시지 및 예외 코드](designer-error-codes.md) 에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [자습서: 자동 가격을 예측 하기 위해 디자이너에서 모델 빌드](../tutorial-designer-automobile-price-train-score.md)
