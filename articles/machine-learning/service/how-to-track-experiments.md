---
title: 교육 실행 하는 동안 로그 메트릭
titleSuffix: Azure Machine Learning service
description: 학습 스크립트에 로깅을 추가하는 방법, 실험을 제출하는 방법, 실행 작업의 진행 상황을 확인하는 방법 및 실행 결과를 보는 방법에 대해 알아봅니다. 실험을 추적할 수 있으며 모델 생성 프로세스를 향상 시키기 위해 메트릭을 모니터링할 수 있습니다.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59a801fd149a0f647298d94522f9eefc1ea6eb9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820905"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Azure Machine Learning에서 학습 하는 동안 로그 메트릭 실행

이 문서에서는 학습 스크립트에 로깅 추가, 실험 실행을 제출, 실행, 모니터링 및 Azure Machine Learning 서비스에서 실행의 결과 확인 하는 방법에 알아봅니다. 실험을 추적 및 메트릭을 모니터링 하 여 모델 생성 프로세스를 향상 시킵니다. 

## <a name="list-of-training-metrics"></a>학습 메트릭 목록 

실험을 학습하는 동안 실행에 추가할 수 있는 메트릭은 다음과 같습니다. 실행 시 추적할 수 있는 메트릭에 대한 자세한 목록을 보려면 [Run 클래스 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)를 참조하세요.

|Type| Python 함수 | 메모|
|----|:----|:----|
|스칼라 값 |함수:<br>`run.log(name, value, description='')`<br><br>예제:<br>run.log(“accuracy”, 0.95) |숫자 또는 문자열 값을 지정된 이름의 실행에 기록합니다. 메트릭을 실행에 기록하면 해당 메트릭이 실험의 실행 기록에 저장됩니다.  하나의 실행 내에서 동일한 메트릭을 여러 번 기록할 수 있으며 결과는 해당 메트릭의 벡터로 간주됩니다.|
|목록|함수:<br>`run.log_list(name, value, description='')`<br><br>예제:<br>run.log_list(“accuracies”, [0.6, 0.7, 0.87]) | 값 목록을 지정된 이름의 실행에 기록합니다.|
|행|함수:<br>`run.log_row(name, description=None, **kwargs)`<br>예제:<br>run.log_row(“Y over X”, x=1, y=0.4) | *log_row*를 사용하여 kwargs에 설명된 대로 열이 여러 개 있는 메트릭을 만듭니다. 명명된 각 매개 변수는 지정된 값이 있는 열을 생성합니다.  *log_row*는 임의의 튜플을 로깅하기 위해 한 번 호출되거나 루프에서 여러 번 호출되어 전체 테이블을 생성할 수 있습니다.|
|테이블|함수:<br>`run.log_table(name, value, description='')`<br><br>예제:<br>run.log_table(“Y over X”, {”x”:[1, 2, 3], “y”:[0.6, 0.7, 0.89]}) | 사전 개체를 지정된 이름의 실행에 기록합니다. |
|이미지|함수:<br>`run.log_image(name, path=None, plot=None)`<br><br>예제:<br>`run.log_image("ROC", plt)` | 이미지를 실행 기록에 로깅합니다. log_image를 사용하여 이미지 파일이나 matplotlib 도면을 실행에 기록합니다.  이러한 이미지는 실행 기록에서 볼 수 있고 비교할 수 있습니다.|
|실행 태그 지정|함수:<br>`run.tag(key, value=None)`<br><br>예제:<br>run.tag(“selected”, “yes”) | 문자열 키와 선택적 문자열 값을 사용하여 실행에 대한 태그를 지정합니다.|
|파일 또는 디렉터리 업로드|함수:<br>`run.upload_file(name, path_or_stream)`<br> <br> 예제:<br>run.upload_file("best_model.pkl", "./model.pkl") | 파일을 실행 기록에 업로드합니다. 실행은 지정된 출력 디렉터리에서 파일을 자동으로 캡처합니다. 이 디렉터리는 대부분의 실행 형식에 대해 기본적으로 "./outputs"로 지정됩니다.  pload_file은 추가 파일을 업로드해야 하거나 출력 디렉터리를 지정하지 않은 경우에만 사용합니다. outputs 디렉터리에 업로드되도록 이름에 `outputs`를 추가하는 것이 좋습니다. `run.get_file_names()`를 호출하여 이 실행 기록와 연결된 모든 파일을 나열할 수 있습니다.|

> [!NOTE]
> 스칼라, 목록, 행 및 테이블에 대한 메트릭은 부동 소수점, 정수 또는 문자열 형식일 수 있습니다.

## <a name="start-logging-metrics"></a>로깅 메트릭 시작

실험을 추적하거나 모니터링하려면 실행을 제출할 때 로깅을 시작하는 코드를 추가해야 합니다. 실행 제출을 트리거하는 방법은 다음과 같습니다.
* __Run.start_logging__ - 학습 스크립트에 로깅 함수를 추가하고 지정된 실험에서 대화형 로깅 세션을 시작합니다. **start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.
* __ScriptRunConfig__ - 학습 스크립트에 로깅 함수를 추가하고 실행을 사용하여 전체 스크립트 폴더를 로드합니다.  **ScriptRunConfig**는 스크립트 실행 구성을 설정하는 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.

## <a name="set-up-the-workspace"></a>작업 영역 설정
로깅을 추가하고 실험을 제출하기 전에 작업 영역을 설정해야 합니다.

1. 작업 영역을 로드합니다. 단계에 따라 작업 영역 구성을 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 서비스 작업 영역 만들기](setup-create-workspace.md#sdk)합니다.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>옵션 1: start_logging 사용

**start_logging**은 노트북과 같은 시나리오에서 사용할 대화형 실행을 만듭니다. 세션 중에 기록된 모든 메트릭이 실험의 실행 기록에 추가됩니다.

다음 예제에서는 로컬 Jupyter 노트북에서 간단한 sklearn Ridge 모델을 로컬로 학습합니다. 다른 환경에 실험을 제출하는 방법에 대해 자세히 알아 보려면 [Azure Machine Learning 서비스를 사용하여 모델을 학습하기 위한 계산 대상 설정](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)을 참조하세요.

1. 로컬 Jupyter 노트북에 학습 스크립트를 만듭니다. 

   ``` python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Azure Machine Learning 서비스 SDK를 사용하여 실험 추적을 추가하고 지속형 모델을 실험 실행 기록에 업로드합니다. 다음 코드는 태그를 지정하고, 기록하고, 모델 파일을 실험 실행에 업로드합니다.

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

스크립트는 ```run.complete()```로 끝나며 실행이 완료됨으로 표시됩니다.  이 함수는 일반적으로 대화형 노트북 시나리오에서 사용됩니다.

## <a name="option-2-use-scriptrunconfig"></a>옵션 2: ScriptRunConfig 사용

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 실행 하는 스크립트에 대 한 구성 설정에 대 한 클래스입니다. 이 옵션을 사용하면 완료 알림을 받거나 모니터링할 시각적 위젯을 가져오는 모니터링 코드를 추가할 수 있습니다.

이 예제에서는 위의 기본 sklearn Ridge 모델을 확장합니다. 실험의 실행에서 메트릭 및 학습된 모델을 캡처하기 위해 모델의 알파 값을 스윕하는 간단한 매개 변수 스윕을 수행합니다. 예제는 사용자 관리 환경에 대해 로컬로 실행됩니다. 

1. 학습 스크립트 `train.py`를 만듭니다.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. `train.py` 스크립트는 Ridge 모델에서 사용할 알파 값 목록을 가져올 수 있는 `mylib.py`를 참조합니다.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. 사용자 관리 로컬 환경을 구성합니다.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. ```train.py``` 스크립트를 제출하여 사용자 관리 환경에서 실행합니다. 이 스크립트 폴더 전체는 ```mylib.py``` 파일을 포함하여 학습을 위해 제출됩니다.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>실행 관리

합니다 [시작, 모니터링 및 교육 실행 취소](how-to-manage-runs.md) 문서 실험을 관리 하는 방법에 대 한 특정 Azure Machine Learning 워크플로 강조 표시 합니다.

## <a name="view-run-details"></a>실행 세부 정보 보기

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Jupyter 노트북 위젯을 사용하여 실행 모니터링
**ScriptRunConfig** 메서드를 사용하여 실행을 제출하면 Jupyter 노트북 위젯을 통해 실행 진행 상황을 확인할 수 있습니다. 실행 제출과 마찬가지로, 위젯은 비동기이며 작업이 완료될 때까지 10-15초 간격으로 라이브 업데이트를 제공합니다.

1. 실행이 완료될 때까지 기다리는 동안 Jupyter 위젯을 봅니다.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Jupyter 노트북 위젯의 스크린샷](./media/how-to-track-experiments/widgets.PNG)

2. **[자동화된 기계 학습 실행의 경우]** 이전 실행에서 차트에 액세스합니다. 대체 `<<experiment_name>>` 적절 한 실험 이름:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![자동화된 기계 학습을 위한 Jupyter Notebook 위젯](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


파이프라인의 세부 정보를 보려면 테이블에서 살펴보려는 파이프라인을 클릭합니다. Azure Portal의 팝업 항목에 차트가 렌더링됩니다.

### <a name="get-log-results-upon-completion"></a>완료 시 로그 결과 가져오기

모델 학습 및 모니터링은 백그라운드에서 수행되므로 기다리는 동안 다른 작업을 실행할 수 있습니다. 또한 모델에서 학습을 완료할 때까지 기다린 후에 더 많은 코드를 실행할 수도 있습니다. **ScriptRunConfig**를 사용하면 ```run.wait_for_completion(show_output = True)```를 사용하여 모델 학습이 완료된 시점을 표시할 수 있습니다. ```show_output``` 플래그는 자세한 정보를 출력합니다. 
  
### <a name="query-run-metrics"></a>실행 메트릭 쿼리

```run.get_metrics()```를 사용하여 학습된 모델의 메트릭을 볼 수 있습니다. 이제 위의 예제에 기록된 모든 메트릭을 가져와 최상의 모델을 결정할 수 있습니다.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Azure Portal에서 실험 보기

실험에서 실행이 완료되면 기록된 실험 실행 기록을 찾아볼 수 있습니다. 다음 두 가지 방법으로 기록에 액세스할 수 있습니다.

* URL을 실행으로 직접 가져옵니다(```print(run.get_portal_url())```).
* 실행 이름(여기서는 ```run```)을 제출하여 실행 세부 정보를 봅니다. 이렇게 하면 실험 이름, ID, 유형, 상태, 세부 정보 페이지, Azure Portal에 대한 링크 및 문서에 대한 링크가 표시됩니다.

실행 링크를 클릭하면 Azure Portal의 실행 세부 정보 페이지로 바로 이동합니다. 여기서는 실험에 기록된 모든 속성, 추적된 메트릭, 이미지 및 차트를 볼 수 있습니다. 이 경우 MSE와 알파 값을 기록했습니다.

  ![Azure Portal의 실행 세부 정보](./media/how-to-track-experiments/run-details-page-web.PNG)

또한 실행에 대한 모든 출력 또는 로그를 보거나 제출한 실험의 스냅숏을 다운로드하여 실험 폴더를 다른 사용자와 공유할 수도 있습니다.

### <a name="viewing-charts-in-run-details"></a>실행 세부 정보에서 차트 보기

로깅 API를 사용하여 실행하는 동안 다른 유형의 메트릭을 기록하고 이를 Azure Portal에서 차트로 확인하는 여러 가지 방법이 있습니다. 

|기록된 값|예제 코드| 포털에서 보기|
|----|----|----|
|숫자 값의 배열 기록| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|단일 변수 꺾은선형 차트|
|반복적으로 사용되는 동일한 메트릭 이름(for 루프 내에서와 같이)을 사용하여 단일 숫자 값 기록| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 단일 변수 꺾은선형 차트|
|2개의 숫자 열을 반복적으로 사용하여 행 기록|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|두 개의 변수 꺽은선형 차트|
|두 개의 숫자 열을 사용하여 테이블 기록|`run.log_table(name='Sine Wave', value=sines)`|두 개의 변수 꺽은선형 차트|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>자동화된 ML 차트 이해

노트북에서 자동화된 ML 작업을 제출하면 기계 학습 서비스 작업 영역에서 이러한 실행 기록을 찾을 수 있습니다. 

다음에 대해 자세히 알아봅니다.
+ [분류 모델에 대한 차트 및 곡선](#classification)
+ [회귀 모델에 대한 차트 및 그래프](#regression)
+ [모델 설명 기능](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>실행 차트 보기

1. 작업 영역으로 이동합니다. 

1. 작업 영역의 맨 왼쪽 패널에서 **실험**을 선택합니다.

   ![실험 메뉴 스크린샷](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. 관심 있는 실험을 선택합니다.

   ![실험 목록](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. 테이블에서 실행 번호를 선택합니다.

   ![실험 실행](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. 테이블에서 자세히 살펴보려는 모델의 반복 번호를 선택합니다.

   ![실험 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>분류

Azure Machine Learning의 자동화된 기계 학습 기능을 사용하여 빌드하는 모든 분류 모델에 대해 다음 차트를 볼 수 있습니다. 
+ [혼동 행렬](#confusion-matrix)
+ [정밀도-리콜 차트](#precision-recall-chart)
+ [ROC(수신기 작동 특성)](#roc)
+ [리프트 곡선](#lift-curve)
+ [게인 곡선](#gains-curve)
+ [보정 플롯](#calibration-plot)

#### <a name="confusion-matrix"></a>혼동 행렬

혼동 행렬은 분류 모델의 성능을 설명하는 데 사용됩니다. 각 행은 true 클래스 인스턴스를 표시하고, 각 열은 예측된 클래스 인스턴스를 나타냅니다. 혼동 행렬은 지정된 모델에 대해 올바르게 분류된 레이블과 잘못 분류된 레이블을 표시합니다.

분류 문제를 위해 Azure Machine Learning은 빌드된 각 모델에 대한 혼동 행렬을 자동으로 제공합니다. 각 혼동 행렬에 대해 자동화된 ML에서 올바르게 분류된 레이블은 녹색으로 표시되고 잘못 분류된 레이블은 빨간색으로 표시됩니다. 원의 크기는 해당 bin의 샘플 수를 나타냅니다. 또한 각 예측된 레이블 및 각 올바른 레이블의 빈도 개수가 인접한 막대형 차트에 제공됩니다. 

예제 1: 정확성이 낮은 분류 모델 ![정확성이 낮은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

예 2: 정확성이 높은 분류 모델(이상적) ![정확성이 높은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>정밀도-리콜 차트

이 차트를 사용하면 각 모델의 정밀도-리콜 곡선을 비교하여 특정 비즈니스 문제에 대해 정밀도와 리콜 간에 허용되는 관계가 있는 모델을 확인할 수 있습니다. 이 차트에는 매크로 평균 정밀도-리콜, 마이크로 평균 정밀도-리콜 및 모델의 모든 클래스와 관련된 정밀도-리콜이 표시됩니다.

정밀도라는 용어는 분류자가 모든 인스턴스에 올바른 레이블을 지정할 수 있는 기능을 나타냅니다. 리콜은 분류자가 특정 레이블의 인스턴스를 모두 찾을 수 있는 기능을 나타냅니다. 정밀도-리콜 곡선은 이러한 두 개념 간의 관계를 표시합니다. 모델이 100% 정밀도와 100% 정확도를 갖는 것이 이상적입니다.

예제 1: 정밀도와 리콜이 낮은 분류 모델 ![정밀도와 리콜이 낮은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

예 2: ~100% 정밀도 및 ~100% 리콜인 분류 모델(이상적) ![정밀도와 리콜이 높은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

ROC(수신기 작동 특성)는 특정 모델에 대해 올바르게 분류된 레이블 및 잘못 분류된 레이블의 플롯입니다. ROC 곡선은 가양성 레이블을 표시하지 않으므로 바이어스가 높은 데이터 세트에서 모델을 학습할 때는 제공하는 정보가 적을 수 있습니다.

예제 1: 올바른 레이블이 낮고 잘못된 레이블이 높은 분류 모델 ![올바른 레이블이 낮고 잘못된 레이블이 높은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

예 2: 올바른 레이블이 높고 잘못된 레이블이 낮은 분류 모델 ![올바른 레이블이 높고 잘못된 레이블이 낮은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>리프트 곡선

특정 모델의 값 게인을 보기 위해 Azure Machine Learning을 사용하여 자동으로 빌드된 모델 리프트를 기준선과 비교할 수 있습니다.

리프트 차트는 분류 모델의 성능을 평가하는 데 사용됩니다. 모델을 사용하지 않을 경우와 비교해서 모델을 사용할 경우 예상할 수 있는 성능 향상을 보여 줍니다. 

예제 1: 임의 선택 모델보다 모델 성능이 더 낮음 ![임의 선택 모델보다 성능이 더 낮은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

예 2: 임의 선택 모델보다 모델 성능이 더 높음 ![성능이 더 높은 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>게인 곡선

게인 차트는 데이터의 각 부분별로 분류 모델의 성능을 평가합니다. 데이터 세트의 각 백분위수에 대해, 임의 선택 모델과 비교해서 예상할 수 있는 성능 향상을 보여 줍니다.

누적 게인 차트를 사용하면 모델에서 원하는 게인에 해당하는 백분율을 사용하여 분류 경계를 선택하는 데 도움이 됩니다. 이 정보는 동반되는 리프트 차트의 결과를 살펴보는 또 다른 방법을 제공합니다.

예제 1: 최소 게인의 분류 모델 ![최소 게인의 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

예 2: 상당한 게인의 분류 모델 ![상당한 게인의 분류 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>보정 플롯

모든 분류 문제에 대해 마이크로 평균, 매크로 평균 및 지정된 예측 모델의 각 클래스의 보정 선을 검토할 수 있습니다. 

보정 플롯은 예측 모델의 신뢰도를 표시하는 데 사용됩니다. 이 작업을 위해 예측 확률과 실제 확률 간의 관계를 보여 주며, 여기서 “확률”은 특정 인스턴스가 일부 레이블에서 속할 가능성을 나타냅니다. 잘 보정된 모델은 y=x 선과 일치하며, 이 경우 해당 예측의 신뢰도가 상당히 높음을 의미합니다. 신뢰도가 과도한 모델은 y=0 선과 일치하며, 이 경우 예측된 확률은 있지만 실제 확률이 없음을 의미합니다.

예제 1: 잘 보정된 모델 ![ 잘 보정된 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

예 2: 신뢰도가 과도한 모델 ![신뢰도가 과도한 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>회귀
Azure Machine Learning의 자동화된 기계 학습 기능을 사용하여 빌드하는 모든 회귀 모델에 대해 다음 차트를 볼 수 있습니다. 
+ [예측 대 실제](#pvt)
+ [나머지 히스토그램](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>예측 대 True 

예측 대 실제는 회귀 문제에 대한 예측된 값과 상호 연관된 실제 값 사이의 관계를 보여 줍니다. 예측된 값이 y=x 선에 가까울수록 예측 모델의 정확도가 향상되기 때문에 이 그래프를 사용하여 모델의 성능을 측정할 수 있습니다.

각 실행 후에 각 회귀 모델에 대한 예측 대 실제 그래프를 확인할 수 있습니다. 데이터 개인 정보를 보호하기 위해 값은 함께 bin 처리되고 각 bin의 크기가 차트 영역의 아래쪽에 막대형 그래프로 표시됩니다. 밝은 음영 영역을 통해 오차 범위를 표시하는 예측 모델과 모델의 이상적인 값을 비교할 수 있습니다.

예제 1: 예측 정확도가 낮은 회귀 모델 ![예측 정확도가 낮은 회귀 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

예 2: 예측 정확도가 높은 회귀 모델 ![예측 정확도가 높은 회귀 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>나머지 히스토그램

나머지는 관찰된 y - 예측된 y를 나타냅니다. 바이어스가 낮은 오차 범위를 표시하려면 나머지 히스토그램의 모양이 0을 중심으로 하는 벨 곡선이어야 합니다. 

예제 1: 오차에 바이어스가 있는 회귀 모델 ![오차에 바이어스가 있는 회귀 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

예 2: 오차 분포가 더 균일한 회귀 모델 ![오차 분포가 더 균일한 회귀 모델](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>모델 설명 기능 및 기능 중요도

기능 중요도는 각 기능이 모델 구성에서 얼마나 중요했는지를 나타내는 점수를 제공합니다. 모델 전체 및 예측 모델의 클래스별로 기능 중요도 점수를 검토할 수 있습니다. 각 클래스 및 전체와 비교해서 기능별로 중요도를 확인할 수 있습니다.

![기능 설명 기능](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>노트북 예제
이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

다음 단계를 수행하여 Python용 Azure Machine Learning SDK를 사용하는 방법을 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

* [Azure Machine Learning을 사용하여 PyTorch 모델을 학습하는 방법](how-to-train-pytorch.md)을 알아보세요.
