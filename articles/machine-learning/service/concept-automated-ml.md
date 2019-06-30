---
title: 자동화 된 기계 학습 이란 / automl
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service에서 알고리즘을 자동으로 선택하고, 모델에 가장 적합한 알고리즘을 선택하기 위해 사용자가 제공한 매개 변수 및 기준에 따라 모델을 생성하여 시간을 절약하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: b9fe8ff710cbfe7fbb4a4d8bd351028bb50efcb0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331744"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

자동화 된 기계 학습, 기계 학습 모델 개발 시간 소모적이 고 반복적인 작업을 자동화 하는 과정은 autoML, 라고도 합니다. 데이터 과학자, 분석가 및 개발자가 모델 품질을 유지 하면서 높은 확장성, 효율성 및 생산성을 사용 하 여 ML 모델을 빌드할 수 있습니다.

기존 기계 학습 모델을 개발이 리소스를 많이 사용, 중요 한 도메인 정보 및 생성 하 고 수십 개의 모델을 비교 하는 시간을 요구 합니다. Azure Machine Learning을 학습 하 고 지정할 대상 메트릭을 사용 하 여 모델을 조정 하려는 경우 자동화 된 기계 학습을 적용 합니다. 서비스는 다음 기능 선택, 각 반복 학습 점수를 사용 하 여 모델을 생성 하는 위치를 사용 하 여 쌍을 이루는 기계 학습 알고리즘을 반복 합니다. 높을수록 점수, 더욱 모델 데이터를 "맞추기"로 간주 됩니다.

자동화 된 machine learning을 사용 하 여 유용한 쉽고 효율적 프로덕션이 준비 된 ML 모델을 시작 하는 데 걸리는 시간을 가속화할 수 있습니다.

## <a name="when-to-use-automated-ml"></a>자동화 된 기계 학습을 사용 하는 경우

자동화 된 ML 전유물이 기계 학습 모델 개발 프로세스 하 고 데이터 과학 전문 지식을 관계 없이 해당 사용자가 문제에 대 한 종단 간 기계 학습 파이프라인을 식별할 수 있습니다.

데이터 과학자, 분석가 및 산업의 개발자는 자동화 된 ML 따르면 됩니다.

+ 광범위 한 프로그래밍 지식 없이 기계 학습 솔루션 구현
+ 시간과 리소스가 절약
+ 데이터 과학에 대 한 유용한 정보를 활용 합니다.
+ Agile 문제 해결을 제공 합니다.

## <a name="how-automated-ml-works"></a>자동화 된 방법을 ML 작동

사용 하 여 **Azure Machine Learning 서비스**를 디자인 하 고 다음이 단계를 사용 하 여 자동화 된 ML 학습 실험을 실행할 수 있습니다.

1. **기계 학습 문제를 식별할** 해결 해야 할: 분류, 예측 또는 회귀

1. **원본 및 레이블이 지정 된 학습 데이터의 형식을 지정**: Pandas 데이터 프레임 또는 Numpy 배열을

1. **모델 학습을 위한 계산 대상 구성**와 같은 프로그램 [로컬 컴퓨터, Azure Machine Learning에서 계산, 원격 Vm 또는 Azure Databricks](how-to-set-up-training-targets.md)합니다.  자동화 된 교육에 알아봅니다 [원격 리소스에 대해](how-to-auto-train-remote.md)합니다.

1. **자동화 된 machine learning 매개 변수 구성** 다른 모델 하이퍼 매개 변수 설정을 통해 반복 횟수 전처리/기능화 (featurization), 고급 및 최상의 모델을 결정할 때 살펴봐야 할 메트릭이 무엇 인지 결정 하는 합니다.  자동 학습 실험에 대 한 설정을 구성할 수 있습니다 [Azure portal에서](how-to-create-portal-experiments.md) 하거나 [SDK를 사용 하 여](how-to-configure-auto-train.md)입니다.

1. **교육 실행을 제출 합니다.**

  ![자동화 된 Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

학습 중 Azure Machine Learning 서비스는 다양 한 다른 알고리즘 및 매개 변수는 병렬 파이프라인에서 만듭니다. 실험에 정의 된 종료 조건에 도달 했 되 면 중지 됩니다.

실행된 기록된 정보를 검사할 수도 있습니다는 [메트릭이 포함](how-to-understand-accuracy-metrics.md) 실행 중에 수집 합니다. 직렬화 하는 Python 개체를 생성 하는 교육 실행 (`.pkl` 파일) 모델 및 데이터 전처리를 포함 하는 합니다.

모델 빌드를 자동화 하는 동안 수도 있습니다 [어떻게 중요 하거나 관련 기능을 알아봅니다는](how-to-configure-auto-train.md#explain) 를 사용 하면 생성 된 모델입니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>전처리

모든 자동화 된 machine learning 실험에서에서 데이터는 기본 메서드를 사용 하 고 필요에 따라 고급 통해 전처리에 전처리 됩니다.

### <a name="automatic-preprocessing-standard"></a>자동 (표준)를 전처리 합니다.

모든 자동화 된 machine learning 실험에서에서 데이터는 자동으로 크기를 조정 하거나 정규화 알고리즘을 잘 수행 합니다.  모델 학습 중 각 모델에 적용할 수는 다음 크기 조정 또는 정규화 방법 중 하나입니다.

|크기 조정&nbsp;&&nbsp;정규화| 설명 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 평균 제거 및 크기 조정 단위 분산 하 여 기능을 표준화  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 각 기능을 해당 열의 최소값 및 최대값을 확장 하 여 기능 변환  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |해당 최대 절대값으로 각 기능 확장 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |해당 변 위치 범위에 따라이 Scaler 기능 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터의 특이값 분해를 사용 하 여 하위 차원 공간에 프로젝션 하는 선형 차원성 감소 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변환기는 잘린된 단수형 SVD (특이값 분해)를 사용 하 여 선형 차원성 감소를 수행 합니다. PCA를 달리이 스 티 메이이 터 특이값 분해를 계산 하기 전에 데이터를 맞추지 않습니다. 즉, scipy.sparse 행렬을 사용 하 여 효율적으로 작업할 수 있습니다. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 적어도 하나의 0이 아닌 구성 요소를 사용 하 여 각 샘플 (즉, 데이터 행렬의 각 행) 다른 샘플 독립적으로 확장 다시는 해당 norm (l1 또는 l2)이 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>전처리 고급: 선택적 기능화 (featurization)

추가 고급 전처리 및 기능화 (featurization) 예: 누락 값 대체, 인코딩 및 변환을 사용할 수 있습니다. [어떤 기능화 (featurization) 포함에 대 한 자세한](how-to-create-portal-experiments.md#preprocess)합니다. 사용 하 여이 설정을 사용 하도록 설정 합니다.

+ Azure Portal: 선택 하는 **전처리** 에서 확인란을 선택 합니다 **고급 설정** [이 단계를 사용 하 여](how-to-create-portal-experiments.md).

+ Python SDK: 지정 `"preprocess": True` 에 대 한 합니다 [ `AutoMLConfig` 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)합니다.


## <a name="time-series-forecasting"></a>시계열 예측
수익, 인벤토리, 판매량 또는 고객 요청 인지 예측 구축이 모든 비즈니스의 필수적인 부분입니다. 자동화 된 기계 학습 기법 및 접근 방식을 결합 하 여 권장 되는, 고품질 시계열 예측을 가져오기에 사용할 수 있습니다. 

자동화 된 시계열 실험 다변량 회귀 문제로 처리 됩니다. 이전 시계열 값은 "피벗" 추가 차원이 다른 예측자 함께 회귀 변수에 대해 되도록 합니다. 고전 시계열 메서드, 달리이 방식에서는 여러 컨텍스트 변수 및 해당 관계를 학습 하는 동안 자연스럽 게 통합 하는 이점은 있습니다. 자동화 된 기계 학습 데이터 집합 및 예측 지평에서 모든 항목에 대 한 단일 하지만 내부적으로 자주 분기 된 모델을 학습. 더 많은 데이터를 모델 매개 변수를 예측 하 사용할 수 있으므로 보이지 않는 계열에 일반화가 가능해 집니다. 

자세히 알아보고 예제를 보려면 [시계열 예측에 대 한 machine learning 자동화 된](how-to-auto-train-forecast.md)합니다.

## <a name="ensemble-models"></a>앙상블 모델

자동화 된 machine learning을 사용 하 여 앙상블 모델을 학습 시킬 수는 [정렬 된 앙상블 초기화를 사용 하 여 Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)합니다. 달리 단일 모델을 사용 하 여 여러 모델을 결합 함으로써 여 machine learning 결과 예측 성능을 개선 하는 앙상블 학습 합니다. 앙상블 반복 실행의 마지막 반복으로 표시 됩니다.

## <a name="use-with-onnx-in-c-apps"></a>ONNX 사용 C# 앱

Azure Machine Learning을 사용 하 여 자동화 된 기계 학습 Python 모델을 빌드 및 ONNX 형식으로 변환 하 게 사용할 수 있습니다. ONNX 런타임에서 지원 C#에서 자동으로 작성 된 모델을 사용할 수 있으므로 프로그램 C# 기록이 필요 또는 REST 끝점을 소개 하는 네트워크 대기 시간 없이 앱. 이 흐름의 예를 봅니다 [이 Jupyter notebook에서](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)합니다.

## <a name="automated-ml-across-microsoft"></a>Microsoft에서 자동화 된 기계 학습

자동화 된 기계 학습은 같은 다른 Microsoft 솔루션에서 사용할 수 있는:

|통합|설명|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|자동 모델 선택 및 교육 ML.NET를 사용 하 여 Visual Studio 및 Visual Studio Code를 사용 하 여.NET 앱에서 기계 학습 (미리 보기)를 자동화 합니다.|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|동시에 HDInsight 클러스터에서 Spark의 ML에 자동화 된 교육 작업을 확장 합니다.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Power BI (미리 보기)에서 직접 기계 학습 모델을 호출 합니다.|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|SQL Server 2019 빅 데이터 클러스터에서 데이터 모델을 학습 하는 새 컴퓨터를 만듭니다.|

## <a name="next-steps"></a>다음 단계

예제를 참조 하 고 자동화 된 machine learning을 사용 하 여 모델을 빌드하는 방법을 알아봅니다.

+ 수행 된 [자습서: Azure 자동화된 Machine Learning을 사용하여 자동으로 분류 모델 학습시키기](tutorial-auto-train-models.md)

+ 자동 학습 실험에 대 한 설정을 구성 합니다.
  + Azure 포털 인터페이스에서 [이 단계를 사용 하 여](how-to-create-portal-experiments.md)입니다.
  + Python SDK를 사용 하 여 [이 단계를 사용 하 여](how-to-configure-auto-train.md)입니다.

+ 자동으로 시계열 데이터를 사용 하 여 학습 하는 방법을 알아봅니다 [이 단계를 사용 하 여](how-to-auto-train-forecast.md)입니다.

+ 사용해 [자동화 된 machine learning에 대 한 샘플 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
