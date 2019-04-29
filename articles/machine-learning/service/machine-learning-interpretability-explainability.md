---
title: 모델 해석력
titleSuffix: Azure Machine Learning service
description: 모델에서 Azure Machine Learning Interpretability SDK를 사용 하 여 예측을 수행 하는 이유를 설명 하는 방법에 알아봅니다. 모델에서 예측을 수행 하는 방법을 이해 하려면 학습 및 추론 하는 동안 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817258"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용 하 여 모델 interpretability

이 문서에서는 모델의 예측을 수행 하는 이유를 설명 하는 방법을 배우게 됩니다 Azure Machine Learning Interpretability SDK를 사용 하 여 만든 것입니다. 모델 설명 수은 다음과 같은 이유로 중요 합니다.

* 고객 및 관련자 알아야 **에 모델을 사용 하면 예측을 신뢰할 수 있는 경우**합니다.
* 데이터 과학자를 파악 하려는 **파악 하도록 모델을 쿼리 하는 방법을**합니다. 또한에서 합리적인된 결정을 내릴 수 있는 도구가 필요 **모델을 향상 시킬 방법**합니다.
* 회사를 알아야 **다양 한 모델의 동작 입력 배포판** 및 **모델의 동작 방식을 특정 입력을 분석 하는 동안**.

Machine learning interpretability은 두 단계의 기계 학습 개발 주기에서 중요 합니다. 

* 하는 동안 **교육**: 모델 디자이너 및 계산기 interpretability 도구 신뢰를 구축 하기 이해 관계자에 게 모델의 출력을 설명 하기 위해 필요 합니다. 또한 모델에 대 한 정보는 모델을 디버그 하 고 동작 해당 목표를 일치 하는지 여부를 결정을 내릴 수 있도록 해야 합니다. 마지막으로, 모델이 없습니다 편향 되어 있는지 확인 해야 합니다.

* 하는 동안 **추론**: 예측 모델을 사용 하는 사람들에 게 설명할 수 있기 때문 되도록 해야 합니다. 예를 들어, 이유 모델 담보 대출, 거부 또는 않은 투자 포트폴리오를 위험이 높은 예측?

Azure Machine Learning Interpretability SDK 통합 기술을 Microsoft에서 개발한 및 타사 라이브러리 (예: SHAP 및 라임)를 입증 합니다. SDK 통합된 라이브러리 간에 공통 API를 만들고 Azure Machine Learning 서비스를 통합 합니다. 이 SDK를 사용 하 여, 기계 학습 모델을 설명할 수 있습니다 **모든 데이터에 대해 전 세계**, 또는 **특정 데이터 요소에 로컬로** 최신의 기술을 사용 하기 쉬운 하 고 확장 가능한 방식으로 사용 하 합니다.

## <a name="how-does-it-work"></a>어떻게 작동합니까?

모델의 전역 동작이 나 특정 예측을 이해 하려면 azure Machine Learning Interpretability는 적용할 수 있습니다. 전자는 전역 설명 호출 되 고 후자는 로컬 설명 호출 됩니다.

Azure Machine Learning Interpretability 메서드 메서드 모델을 알 수 없는 또는 특정 모델 인지에 따라도 분류할 수 있습니다. 일부 메서드는 특정 유형의 모델 대상입니다. 예를 들어 SHAP의 트리 설명만 트리 기반 모델에 적용 됩니다. 일부 메서드는 모방 설명 등 SHAP의 커널 설명 검은색 상자로 모델을 처리합니다. Azure Machine Learning Interpretability SDK는 데이터 집합, 모델 형식 및 사용 사례에 따라 이러한 다른 접근 방식을 활용 합니다.

Azure Machine Learning Interpretability 모델에서 해당 예측을 사용 하는 방법에 정보의 집합을 반환 합니다. 정보는와 같은 항목을 포함 합니다.

* 글로벌/로컬 상대적 기능 중요도
* 글로벌/로컬 기능과 예측 관계

## <a name="architecture"></a>아키텍처

Azure Machine Learning Interpretability SDK는 두 개의 Python 패키지로 구성 되어 있습니다.

* [azureml.explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -Microsoft에서 지원 되는 기능을 포함 하는 기본 패키지입니다.
* `azureml.contrib.explain.model` -미리 보기 및 시도할 수 있는 실험적 기능입니다.

    > [!IMPORTANT]
    > 사항 contrib에 완벽 하 게 지원 되지 않습니다. 실험적 기능 성숙 해지면 점진적으로 주 패키지로 이동지 것입니다.

### <a name="explainers"></a>Explainers

Azure Machine Learning Interpretability SDK explainers의 두 집합을 제공합니다. 직접 Explainers 및 메타 Explainers 합니다.

__직접 explainers__ 통합된 라이브러리에서 제공 됩니다. SDK는 공통 API 및 출력 형식을 노출 되도록 모든 explainers를 래핑합니다. 다음은 SDK에서 사용할 수 있는 직접 explainers의 목록입니다.

> [!TIP]
> 더 편리 하 게 직접 이러한 explainers 인 경우 출력 형식을 확인 하 고 공용 API를 사용 하는 대신 직접 호출할 수 있습니다.

* **설명 트리**: SHAP의 트리 설명 다항 시간 빠른 SHAP 값 예측 알고리즘 트리 및 트리 앙상블에 중점을 둡니다.
* **전체 설명**: "DeepLIFT SHAP NIPS 문서에 설명 된 연결에서 작성 하는 심층 학습 모델에서 SHAP 값에 대 한 고속 근사값 알고리즘은 SHAP, 심층 설명에서에서 설명에 따라 TensorFlow 모델과 Keras는 TensorFlow 백 엔드를 사용 하 여 지원 됩니다 (이기도 PyTorch에 대 한 예비 지원) ".
* **커널 설명**: SHAP의 커널 설명 모든 모델에 대 한 SHAP 값을 예측 하려면 특별히 가중치가 적용 된 로컬 선형 회귀를 사용 합니다.
* **설명 모방**: 모방 설명 전역 서로게이트 모델 개념을 기반으로 합니다. 전역 서로게이트 모델은 본질적으로 해석할 수 있는 모델을 최대한 정확 하 게 검정 상자 모델의 예측을 예상 하도록 학습 됩니다. 데이터 과학자는 블랙 박스 모델에 대 한 결론을 그릴 서로게이트 모델을 해석할 수 있습니다.
* **라임 설명**: 라임에 따라 라임 설명 로컬 서로게이트 모델을 만들의 최신 로컬 해석할 수 있는 모델을 알 수 없는 설명 (라임) 알고리즘을 사용 합니다. 전역 서로게이트 모델과 달리 라임 개별 예측을 설명 하기 위해 로컬 서로게이트 모델 학습에 중점을 둡니다.
* **HAN 텍스트 설명**: HAN 텍스트 설명 지정된 블랙 박스 텍스트 모델에 대 한 텍스트 데이터에서 모델 설명을 가져오는 Hierarchical Attention Network를 사용 합니다. 모델을 학습 합니다 HAN 서로게이트 지정된 교사 모델의 예측 된 출력에 있습니다. 텍스트 모음에서 전역적으로 학습, 이후에 설명의 정확도 개선 하기 위해 특정 문서에 대 한 미세 조정 단계를 추가 했습니다. HAN 문장 및 단어 주의 양방향 같은 메시지가 두 주의 레이어를 사용 하 여 사용합니다. DNN 교사 모델에서 학습 된 특정 문서 미세 조정 하는 고 주의 계층에서 word importances 추출 수 것입니다. HAN 시간도 학습 텍스트 데이터에 대 한 라임 또는 SHAP 보다 더 정확 하지만 더 비용이 많이 드는 용어를 발견 했습니다. 그러나 학습 시간이 향상 된 기능을 이지만 여전히 느린 사용자 글러브 단어 포함을 사용 하 여 네트워크를 초기화할 수 있는 옵션을 제공 하 여 했습니다. HAN 원격 Azure GPU VM에서 실행 하 여 학습 시간을 크게 향상 될 수 있습니다. HAN 구현의 ' 문서 분류 (Yang et al, 2016)에 대 한 계층적 주의 네트워크 '에서 설명한 ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ 자동으로 적합 한를 직접 설명을 선택 하 고 지정 된 모델 및 데이터 집합에 따라 최상의 설명 정보를 생성 합니다. Meta explainers을 활용 하 여 모든 라이브러리 (SHAP 라임, 모방, 등)를 개발 중이거나 통합 했습니다. 다음은 SDK에서 사용할 수 있는 메타 explainers입니다.

* **테이블 형식 설명**: 테이블 형식 데이터 집합을 사용 합니다.
* **텍스트 설명**: 텍스트 데이터 집합을 사용 합니다.

또한에 메타 선택의 직접 explainers, 메타 explainers 기본 라이브러리를 기반으로 추가 기능을 개발 및 직접 explainers 통해 속도 및 확장성을 개선 합니다.

현재 `TabularExplainer` 직접 Explainers를 호출 하는 다음 논리를 사용 합니다.

1. 트리 기반 모델을 이면 적용 `TreeExplainer`, 그렇지 않으면
2. DNN 모델 인 경우 적용 `DeepExplainer`, 그렇지 않으면
3. 블랙 박스 모델으로 처리 하 고 적용 `KernelExplainer`

에 빌드된 인텔리전스 `TabularExplainer` 더 많은 라이브러리는 SDK를 통합 하 고 각 설명의 장단점에 대해 살펴보고 보다 정교한 됩니다.

`TabularExplainer` 직접 Explainers 통해 중요 한 기능 및 성능 향상 있게 되었습니다.

* **초기화 데이터 집합의 요약**합니다. 여기서 설명 속도가 가장 중요 한 경우에 초기화 데이터 집합을 요약 하 고 전역 및 로컬 설명 속도 작은 대표 샘플 집합을 생성 합니다.
* **평가 데이터 집합을 샘플링**합니다. 사용자 평가 샘플의 큰 집합을 전달 했지만 모든 평가할 실제로 필요 하지 않습니다, 경우 샘플링 매개 변수는 전역 설명은 속도를 true로 설정할 수 있습니다.

다음 다이어그램은 메타 explainers 직접의 두 집합 간의 관계를 보여 줍니다.

[![Machine Learning Interpretability 아키텍처](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>지원 되는 모델

Python의 데이터 집합에서 학습 된 모델 `numpy.array`, `pandas.DataFrame`를 `iml.datatypes.DenseData`, 또는 `scipy.sparse.csr_matrix` 형식이 Machine Learning Interpretability SDK에서 지원 됩니다.

입력으로 모델 및 파이프라인을 허용 하는 함수를 설명 합니다. 모델을 제공 하는 경우 모델 예측 함수를 구현 해야 합니다 `predict` 또는 `predict_proba` Scikit 규칙을 준수 합니다. 파이프라인 (파이프라인 스크립트의 이름)를 제공 하는 경우 예측을 반환 하는 실행 중인 파이프라인 스크립트 설명 함수 가정 합니다.

### <a name="local-and-remote-compute-target"></a>로컬 및 원격 계산 대상

Machine Learning Interpretability SDK는 모두 로컬 및 원격 계산 대상으로 작동 하도록 설계 되었습니다. 를 로컬로 실행 하는 경우 SDK 함수는 모든 Azure 서비스 연락 하지 않습니다. Azure Machine Learning Compute에서 설명을 원격으로 실행할 수 있으며 설명 정보를 Azure Machine Learning 실행 기록 서비스에 로그인 할 수 있습니다. 이 정보를 로그인 후 보고서 및 시각화 설명에서 사용자 분석에 대 한 Azure Machine Learning 작업 영역 포털에 쉽게 나와 있습니다.

## <a name="train-and-explain-locally"></a>학습 하 고 로컬로 설명

1. 로컬 Jupyter notebook에서 모델을 학습 합니다. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. 설명을 호출 합니다. 설명 개체를 시작 하려면 모델, 학습 데이터, 출력 클래스 이름과 원하는 (선택 사항)의 기능을 전달 해야 (경우 분류)를 설명 합니다. 사용 하는 설명 개체를 인스턴스화하는 방법은 다음과 같습니다 [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py)를 [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), 및 `LimeExplainer` 로컬로 합니다. `TabularExplainer` 아래 세 가지 explainers 중 하나를 호출 하는 (`TreeExplainer`, `DeepExplainer`, 또는 `KernelExplainer`)은 자동으로 가장 적절 한 사용 사례에 대 한 선택 하 고 있습니다. 그러나 각각의 세 가지 기본 explainers의 직접 호출 합니다.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    또는
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. 전역 기능 중요도 값을 가져옵니다.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. 로컬 기능 중요도 값: 개별 인스턴스 또는 인스턴스 그룹을 설명 하기 위해 다음 함수 호출을 사용 합니다.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    또는
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>학습 하 고 원격으로 설명

Azure Machine Learning 서비스에서 지 원하는 다양 한 계산 대상에서 학습할 수, 하는 동안이 섹션의 예제 AMLCompute를 사용 하 여 수행 하는 방법을 보여 줍니다.

1. 로컬 Jupyter notebook (예를 들어 run_explainer.py)에서 학습 스크립트를 만듭니다.

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. 지침에 따라 [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md#amlcompute) 하는 Azure Machine Learning Compute 계산 대상으로 설정 하 고 학습 실행을 제출 하는 방법을 알아봅니다.

3. 로컬 Jupyter notebook에 설명이 다운로드 합니다. 
    > [!IMPORTANT]
    > 사항 contrib에 완벽 하 게 지원 되지 않습니다. 실험적 기능 성숙 해지면 점진적으로 주 패키지로 이동지 것입니다.

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>다음 단계

위의 지침을 보여주는 Jupyter notebook의 컬렉션을 참조 합니다 [Azure Machine Learning Interpretability 샘플 노트북이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)합니다.
