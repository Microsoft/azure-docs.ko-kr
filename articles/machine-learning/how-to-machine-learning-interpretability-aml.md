---
title: Python에서 ML 모델 설명 & 해석 (미리 보기)
titleSuffix: Azure Machine Learning
description: 기계 학습 모델이 기능 중요도를 결정 하 고 Azure Machine Learning SDK를 사용 하는 경우 예측을 수행 하는 방법에 대 한 설명을 가져오는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8a0853d4a863f6b7677f665513bdac9ca8e6b673
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144110"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Interpretability 패키지를 사용 하 여 Python의 ML 모델 & 예측 (미리 보기)을 설명 합니다.

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 방법 가이드에서는 Azure Machine Learning Python SDK의 interpretability 패키지를 사용 하 여 다음 작업을 수행 하는 방법을 알아봅니다.


* 로컬에서 개인 컴퓨터의 전체 모델 동작 또는 개별 예측을 설명 합니다.

* Interpretability 기술을 사용 하 여 엔지니어링 된 기능을 지원 합니다.

* Azure에서 전체 모델 및 개별 예측의 동작을 설명 합니다.

* 시각화 대시보드를 사용 하 여 모델 설명과 상호 작용할 수 있습니다.

* 모델에 설명 점수 매기기를 배포 하 여 추론 중에 설명을 관찰 합니다.



지원 되는 interpretability 기술 및 기계 학습 모델에 대 한 자세한 내용은 [모델 interpretability의 Azure Machine Learning](how-to-machine-learning-interpretability.md) 및 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)을 참조 하세요.

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>개인용 컴퓨터에서 기능 중요도 값 생성 
다음 예제에서는 Azure 서비스에 연결 하지 않고 개인 컴퓨터에서 interpretability 패키지를 사용 하는 방법을 보여 줍니다.

1. `azureml-interpret`및 패키지를 설치 `azureml-contrib-interpret` 합니다.
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```

2. 로컬 Jupyter 노트북에서 샘플 모델을 학습 합니다.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. 설명를 로컬로 호출 합니다.
   * 설명 개체를 초기화 하려면 모델 및 일부 학습 데이터를 설명의 생성자에 전달 합니다.
   * 설명 및 시각화에 대 한 자세한 정보를 제공 하기 위해 분류를 수행 하는 경우 기능 이름 및 출력 클래스 이름을 전달 하도록 선택할 수 있습니다.

   다음 코드 블록은, 및을 로컬로 사용 하 여 설명 개체를 인스턴스화하는 방법을 보여 줍니다 `TabularExplainer` `MimicExplainer` `PFIExplainer` .
   * `TabularExplainer` 는 (, 또는) 아래에 있는 세 개의 SHAP explainers 중 하나를 호출 `TreeExplainer` `DeepExplainer` `KernelExplainer` 합니다.
   * `TabularExplainer` 는 사용 사례에 가장 적합 한 항목을 자동으로 선택 하지만 세 가지 기본 explainers를 직접 호출할 수 있습니다.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    또는

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    또는

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>전체 모델 동작 설명 (전역 설명) 

집계 (전역) 기능 중요도 값을 가져오는 데 도움이 되는 다음 예를 참조 하세요.

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>개별 예측 설명 (로컬 설명)
개별 인스턴스 또는 인스턴스 그룹에 대 한 설명을 호출 하 여 서로 다른 datapoints의 개별 기능 중요도 값을 가져옵니다.
> [!NOTE]
> `PFIExplainer` 는 로컬 설명을 지원 하지 않습니다.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>원시 기능 변환

엔지니어링 된 기능이 아니라 원시 레이크의 기능을 사용 하 여 설명을 볼 수 있습니다. 이 옵션의 경우 기능 변환 파이프라인을 설명에 전달 `train_explain.py` 합니다. 그렇지 않으면 설명는 엔지니어링 된 기능 측면에서 설명을 제공 합니다.

지원 되는 변환 형식은 [pandas](https://github.com/scikit-learn-contrib/sklearn-pandas)에서 설명한 것과 같습니다. 일반적으로 모든 변환은 단일 열에 대해 작동 하는 한, 즉 일대다를 명확 하 게 수행 하는 동안 지원 됩니다.

또는에 적합 한 `sklearn.compose.ColumnTransformer` 변환기 튜플 목록과 함께 또는를 사용 하 여 원시 기능에 대 한 설명을 가져옵니다. 다음 예에서는를 사용 `sklearn.compose.ColumnTransformer` 합니다.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

피팅 변환기 튜플 목록으로 예제를 실행 하려는 경우 다음 코드를 사용 합니다.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>원격 실행을 통해 기능 중요도 값 생성

다음 예제에서는 클래스를 사용 하 여 `ExplanationClient` 원격 실행에 대해 모델 interpretability를 사용 하도록 설정 하는 방법을 보여 줍니다. 다음을 제외 하 고는 로컬 프로세스와 개념적으로 유사 합니다.

* `ExplanationClient`원격 실행에서를 사용 하 여 interpretability 컨텍스트를 업로드 합니다.
* 로컬 환경에서 나중에 컨텍스트를 다운로드 합니다.

1. `azureml-interpret`및 패키지를 설치 `azureml-contrib-interpret` 합니다.
    ```bash
    pip install azureml-interpret
    pip install azureml-contrib-interpret
    ```
1. 로컬 Jupyter 노트북에 학습 스크립트를 만듭니다. 예들 들어 `train_explain.py`입니다.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. 계산 대상으로 Azure Machine Learning 계산을 설정 하 고 학습 실행을 제출 합니다. 지침은 [PYTHON SDK를 사용 하 여 계산 대상 만들기](how-to-create-attach-compute-sdk.md#amlcompute) 를 참조 하세요. 또한 [예제 노트북이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) 도움이 될 것입니다.

1. 로컬 Jupyter 노트북에서 설명을 다운로드 합니다.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>시각화

로컬 Jupyter 노트북에서 설명을 다운로드 한 후 시각화 대시보드를 사용 하 여 모델을 이해 하 고 해석할 수 있습니다.

### <a name="understand-entire-model-behavior-global-explanation"></a>전체 모델 동작 이해 (전역 설명) 

다음 그림에서는 학습 된 모델을 예측 및 설명과 함께 전체 보기를 제공 합니다.

|그림|설명|
|----|-----------|
|데이터 탐색| 예측 값과 함께 데이터 집합의 개요를 표시 합니다.|
|글로벌 중요도|개별 datapoints의 기능 중요도 값을 집계 하 여 모델의 전체 상위 K (구성 가능 K) 중요 기능을 표시 합니다. 기본 모델의 전반적인 동작을 이해 하는 데 도움이 됩니다.|
|설명 탐색|기능이 모델의 예측 값 또는 예측 값의 확률 변경에 영향을 주는 방법을 보여 줍니다. 기능 상호 작용의 영향을 보여 줍니다.|
|요약 중요도|모든 데이터 요소에서 개별 기능 중요도 값을 사용 하 여 예측 값에 대 한 각 기능의 영향 분포를 표시 합니다. 이 다이어그램을 사용 하 여 기능 값이 예측 값에 영향을 주는 방향을 조사 합니다.
|

[![시각화 대시보드 전역](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>개별 예측 이해 (로컬 설명) 

전체 플롯에서 개별 데이터 요소 중 하나를 클릭 하 여 모든 데이터 요소에 대 한 개별 기능 중요도 그림을 로드할 수 있습니다.

|그림|설명|
|----|-----------|
|로컬 중요도|개별 예측에 대 한 최상위 K (구성 가능 K) 중요 기능을 보여 줍니다. 특정 데이터 요소에 대 한 기본 모델의 로컬 동작을 보여 줍니다.|
|Perturbation 탐색 (what-if 분석)|선택한 데이터 요소의 기능 값에 대 한 변경 내용을 허용 하 고 그 결과 예측 값에 대 한 변경 내용을 관찰 합니다.|
|개별 조건부 예상 (ICE)| 최소값에서 최대값으로 기능 값을 변경할 수 있습니다. 기능이 변경 될 때 데이터 요소의 예측이 어떻게 변경 되는지를 보여 줍니다.|

[![시각화 대시보드 로컬 기능 중요도](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![시각화 대시보드 기능 Perturbation](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![시각화 대시보드 ICE 플롯](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Jupyter 커널을 시작 하기 전에 시각화 대시보드에 대 한 위젯 확장을 사용 하도록 설정 해야 합니다.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

시각화 대시보드를 로드 하려면 다음 코드를 사용 합니다.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, dataset=x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Azure Machine Learning studio의 시각화

Azure Machine Learning 실행 기록에 생성 된 설명 업로드) [원격 interpretability](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) 단계를 완료 한 경우 [Azure Machine Learning studio](https://ml.azure.com)에서 시각화 대시보드를 볼 수 있습니다. 이 대시보드는 위에 설명 된 시각화 대시보드의 더 간단한 버전입니다 (실시간 계산을 수행할 수 있는 스튜디오의 활성 계산이 없기 때문에 설명 탐색 및 ICE 플롯은 사용 하지 않도록 설정 됨).

데이터 집합, 전역 및 로컬 설명을 사용할 수 있는 경우 데이터는 모든 탭 (Perturbation 탐색 및 ICE 제외)을 채웁니다. 글로벌 설명만 사용할 수 있는 경우 요약 중요도 탭과 모든 로컬 설명 탭을 사용할 수 없습니다.

다음 경로 중 하나를 따라 Azure Machine Learning studio에서 시각화 대시보드에 액세스 합니다.

* **실험** 창 (미리 보기)
  1. 왼쪽 창에서 **실험** 을 선택 하 여 Azure Machine Learning에서 실행 한 실험의 목록을 확인 합니다.
  1. 특정 실험을 선택 하 여 해당 실험의 모든 실행을 볼 수 있습니다.
  1. 실행을 선택한 다음 설명 시각화 대시보드에 **설명 탭을 선택 합니다.**

   [![시각화 대시보드 로컬 기능 중요도](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **모델** 창
  1. [Azure Machine Learning를 사용 하 여 모델 배포](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)의 단계를 수행 하 여 원래 모델을 등록 한 경우 왼쪽 창에서 **모델** 을 선택 하 여 볼 수 있습니다.
  1. 모델을 선택한 다음 **설명 탭을 클릭 하 여 설명** 시각화 대시보드를 확인 합니다.

## <a name="interpretability-at-inference-time"></a>유추 시 Interpretability

원본 모델과 함께 설명를 배포 하 고 유추 시이를 사용 하 여 새 모든 요소에 대 한 개별 기능 중요도 값 (로컬 설명)을 제공할 수 있습니다. 또한 유추 시 interpretability 성능을 향상 시키기 위해 가벼운 점수 매기기 explainers 제공 합니다. 가벼운 점수 매기기 설명를 배포 하는 프로세스는 모델 배포와 유사 하며 다음 단계를 포함 합니다.

1. 설명 개체를 만듭니다. 예를 들어 다음을 사용할 수 있습니다 `TabularExplainer` .

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. 설명 개체를 사용 하 여 점수 매기기 설명을 만듭니다.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. 점수 매기기 설명 모델을 사용 하는 이미지를 구성 하 고 등록 합니다.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. 선택적 단계로 클라우드에서 설명 점수 매기기를 검색 하 고 설명을 테스트할 수 있습니다.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. 다음 단계를 수행 하 여 계산 대상에 이미지를 배포 합니다.

   1. 필요한 경우 [Azure Machine Learning를 사용 하 여 모델 배포](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)의 단계를 수행 하 여 원래 예측 모델을 등록 합니다.

   1. 점수 매기기 파일을 만듭니다.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. 배포 구성을 정의합니다.

         이 구성은 모델 요구 사항에 따라 달라 집니다. 다음 예제에서는 하나의 CPU 코어 및 1GB의 메모리를 사용 하는 구성을 정의 합니다.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. 환경 종속성이 있는 파일을 만듭니다.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. G + +가 설치 된 사용자 지정 dockerfile을 만듭니다.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. 만든 이미지를 배포 합니다.
   
         이 프로세스는 5 분 정도 걸립니다.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. 배포를 테스트합니다.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. 정리합니다.

   배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.

## <a name="next-steps"></a>다음 단계

[모델 interpretability에 대 한 자세한 정보](how-to-machine-learning-interpretability.md)

[Azure Machine Learning Interpretability 샘플 노트북 확인](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

