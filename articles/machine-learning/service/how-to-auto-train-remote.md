---
title: 자동화된 ML 원격 컴퓨팅 대상
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 통해 DSVM(Data Science Virtual Machine) 원격 컴퓨팅 대상에서 자동화된 기계 학습을 사용하여 모델을 작성하는 방법에 대해 설명합니다.
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6f2d71abeacee531b21a8276f621367dd39a39d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820340"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>클라우드의 자동화된 기계 학습을 사용하여 모델 학습

Azure Machine Learning에서 관리하는 다양한 종류의 컴퓨팅 리소스에 대해 모델을 교육할 수 있습니다. 계산 대상은 로컬 컴퓨터 또는 클라우드의 컴퓨터입니다.

컴퓨팅 대상을 더 추가하여 기계 학습 실험 규모를 쉽게 확장하거나 스케일 아웃할 수 있습니다. 컴퓨팅 대상 옵션에는 Ubuntu 기반 DSVM(Data Science Virtual Machine) 또는 Azure Machine Learning Compute가 포함됩니다. DSVM은 데이터 과학 수행을 위해 특별히 구축된 Microsoft Azure 클라우드의 사용자 지정 VM 이미지입니다. 여러 인기 있는 데이터 과학 도구와 기타 도구가 미리 설치 및 미리 구성되어 있습니다.  

이 문서에서는 DSVM에서 자동화된 ML을 사용하여 모델을 작성하는 방법을 알아봅니다.

## <a name="how-does-remote-differ-from-local"></a>원격과 로컬의 차이

"[자동화된 기계 학습을 사용하여 분류 모델 학습](tutorial-auto-train-models.md)" 자습서에서는 로컬 컴퓨터를 사용하여 자동화된 ML을 통해 모델을 학습하는 방법을 설명합니다.  로컬로 교육하는 워크플로는 원격 대상에도 적용됩니다. 그러나 원격 컴퓨팅을 사용하면 자동화된 ML 실험 반복이 비동기적으로 실행됩니다. 이 기능을 사용하면 특정 반복을 취소하거나, 실행 상태를 확인하거나, Jupyter 노트의 다른 셀에서 계속 작업할 수 있습니다. 원격으로 학습하려면 먼저 Azure DSVM과 같은 원격 컴퓨팅대상을 만듭니다.  그런 다음, 원격 리소스를 구성하고 코드를 여기에 제출합니다.

이 문서에서는 원격 DSVM에서 자동화된 ML 실험을 실행하는 데 필요한 추가 단계를 보여 줍니다.  이 자습서의 작업 영역 개체 `ws`는 여기 나오는 코드 전체에서 사용됩니다.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>리소스 만들기

아직 DSVM이 없으면 작업 영역(`ws`)에서 만듭니다. 이전에 DSVM을 만든 경우에는 이 코드가 만들기 프로세스를 건너뛰고 기존 리소스 세부 정보를 `dsvm_compute` 개체에 로드합니다.  

**예상 시간**: VM을 만드는 데 약 5분이 소요됩니다.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

이제 `dsvm_compute` 개체를 원격 계산 대상으로 사용할 수 있습니다.

DSVM 이름에는 다음과 같은 제한이 있습니다.
+ 64자보다 짧아야 합니다.  
+ 다음 문자를 포함할 수 없습니다. `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Marketplace 구입 자격에 대한 메시지와 함께 만들기가 실패하는 경우:
>    1. [Azure Portal](https://portal.azure.com)로 이동
>    1. DSVM 만들기 시작 
>    1. "프로그래밍 방식으로 만들기"를 선택하여 프로그래밍 방식 만들기 사용
>    1. VM을 실제로 만들지 않고 종료
>    1. 만들기 코드를 다시 실행

이 코드에서는 프로비전되는 DSVM에 대한 사용자 이름 또는 암호를 만들지 않습니다. VM에 직접 연결하려면 [Azure Portal](https://portal.azure.com)로 이동하여 자격 증명을 만듭니다.  

### <a name="attach-existing-linux-dsvm"></a>기존 Linux DSVM 연결

기존 Linux DSVM을 컴퓨팅 대상으로 연결할 수도 있습니다. 이 예제에서는 기존 DSVM을 사용하지만 새 리소스를 만들지 않습니다.

> [!NOTE]
>
> 다음 코드에서는 합니다 [RemoteCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.remote.remotecompute?view=azure-ml-py) 대상 계산 대상으로 하는 기존 VM을 연결 하는 클래스입니다.
> 합니다 [DsvmCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.dsvmcompute?view=azure-ml-py) 클래스는이 디자인 패턴을 위해 이후 릴리스에서 중단 될 예정입니다.

다음 코드를 실행하여 기존 Linux DSVM에서 컴퓨팅 대상을 만듭니다.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_address_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached-vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

이제 `compute_target` 개체를 원격 계산 대상으로 사용할 수 있습니다.

## <a name="access-data-using-getdata-file"></a>get_data 파일을 사용하여 데이터에 액세스

학습 데이터에 대한 원격 리소스 액세스를 제공합니다. 원격 컴퓨팅에서 실행되는 자동화된 기계 학습 실험의 경우 `get_data()` 함수를 사용하여 데이터를 페치해야 합니다.  

액세스를 제공하려면 다음을 수행해야 합니다.
+ `get_data()` 함수가 포함된 get_data.py 파일 만들기 
+ 해당 파일을 절대 경로로 액세스할 수 있는 디렉터리에 배치 

Blob Storage 또는 get_data.py 파일의 로컬 디스크에서 데이터를 읽도록 코드를 캡슐화할 수 있습니다. 다음 코드 샘플의 데이터는 sklearn 패키지에서 옵니다.

>[!Warning]
>원격 컴퓨팅을 사용하는 경우 데이터 변환이 수행되는 `get_data()`를 사용해야 합니다. get_data()의 일부로 데이터 변환을 위한 추가 라이브러리를 설치해야 하는 경우 추가로 수행할 단계가 있습니다. 자세한 내용은 [auto-ml-dataprep 샘플 노트북](https://aka.ms/aml-auto-ml-data-prep )을 참조하세요.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>실험 구성

`AutoMLConfig`에 대해 설정을 지정합니다.  ([매개 변수 전체 목록](how-to-configure-auto-train.md#configure-experiment) 및 가능한 값을 참조하세요.)

설정에서 `run_configuration`은 DSVM에 대한 설정 및 구성을 포함하는 `run_config` 개체로 설정됩니다.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>모델 설명 사용

선택적인 `model_explainability` 매개 변수를 `AutoMLConfig` 생성자에서 설정합니다. 또한 모델 설명 기능을 사용하려면 유효성 검사 데이터 프레임 개체를 매개 변수 `X_valid`로 전달해야 합니다.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>학습 실험 제출

이제 알고리즘, 하이퍼 매개 변수를 자동으로 선택하는 구성을 제출하고 모델을 교육합니다.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

다음 예제와 비슷한 출력이 표시됩니다.

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>결과 탐색

[학습 자습서](tutorial-auto-train-models.md#explore-the-results)와 동일한 Jupyter 위젯을 사용하여 그래프 및 결과 테이블을 살펴볼 수 있습니다.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
다음은 위젯의 고정 이미지입니다.  Notebook에서 테이블의 아무 줄을 클릭하여 실행 속성을 살펴보고 해당 실행의 로그를 출력할 수 있습니다.   또한 그래프 위에 있는 드롭다운을 사용하여 각 반복에 제공되는 각 메트릭의 그래프를 볼 수 있습니다.

![위젯 테이블](./media/how-to-auto-train-remote/table.png)
![위젯 플롯](./media/how-to-auto-train-remote/plot.png)

위젯은 개별 실행 세부 정보를 살펴보고 탐색하는 데 사용할 수 있는 URL을 표시합니다.
 
### <a name="view-logs"></a>로그 보기

`/tmp/azureml_run/{iterationid}/azureml-logs` 아래에서 DSVM에 대한 로그를 찾습니다.

## <a name="best-model-explanation"></a>최상의 모델 설명

모델 설명 데이터를 검색하면 모델에 대한 자세한 정보를 확인할 수 있으므로 백 엔드에서 실행되는 대상에 대한 투명성이 높아집니다. 이 예제에서는 최적 맞춤 모델에 대해서만 모델 설명을 실행합니다. 파이프라인의 모든 모델에 대해 실행하는 경우 런타임이 크게 늘어납니다. 모델 설명 정보에는 다음이 포함됩니다.

* shap_values: shap lib에서 생성되는 설명 정보입니다.
* expected_values: X_train 데이터 세트에 적용되는 모델의 예상 값입니다.
* overall_summary: 내림차순으로 정렬되는 모델 수준 기능 중요도 값입니다.
* overall: overall_summary와 동일한 순서로 정렬되는 기능 이름입니다.
* per_class_summary: 내림차순으로 정렬되는 클래스 수준 기능 중요도 값입니다. 분류 사례에만 사용할 수 있습니다.
* per_class_imp: per_class_summary와 동일한 순서로 정렬되는 기능 이름입니다. 분류 사례에만 사용할 수 있습니다.

다음 코드를 사용하여 반복에서 최상의 파이프라인을 선택합니다. `get_output` 메서드는 마지막 맞춤 호출에 대한 최적의 실행 및 맞춤 모델을 반환합니다.

```python
best_run, fitted_model = remote_run.get_output()
```

`retrieve_model_explanation` 함수를 가져오고 최상의 모델에서 실행합니다.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

보려면 `best_run` 설명 변수에 대한 결과를 인쇄합니다.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

`best_run` 설명 요약 변수를 인쇄하면 다음 출력이 표시됩니다.

![모델 설명 콘솔 출력](./media/how-to-auto-train-remote/expl-print.png)

작업 영역 내에서 Azure Portal의 웹 UI 뿐만 아니라 위젯 UI를 통해서도 기능 중요도를 시각화할 수 있습니다.

![모델 설명 UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>예

[how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) 노트는 이 문서에 제공되는 개념을 보여 줍니다. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

[자동 학습용 설정을 구성하는 방법](how-to-configure-auto-train.md)을 알아보세요.
