---
title: 계산 대상에 학습 실행 제출
titleSuffix: Azure Machine Learning
description: 다양 한 교육 환경 (계산 대상)에서 machine learning 모델을 학습 합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 학습을 시작합니다. 규모 확장이 필요한 경우 클라우드 기반 컴퓨팅 대상으로 전환합니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: ca76ec5bef1d908ca3cea6ce0f58b1205c1676ca
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144093"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>계산 대상에 학습 실행 제출

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 다양 한 교육 환경 ([계산 대상](concept-compute-target.md))을 사용 하 여 machine learning 모델을 학습 하는 방법에 대해 알아봅니다.

학습 시에는 보통 로컬 컴퓨터에서 시작한 후 나중에 다른 컴퓨팅 대상에서 해당 학습 스크립트를 실행합니다. Azure Machine Learning를 사용 하면 학습 스크립트를 변경 하지 않고도 다양 한 계산 대상에서 스크립트를 실행할 수 있습니다.

**스크립트 실행 구성**내에서 각 계산 대상에 대 한 환경을 정의 하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험
* [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)`ws`
* 계산 대상인 `my_compute_target` 입니다.  다음을 사용 하 여 계산 대상을 만듭니다.
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>스크립트 실행 구성 이란?

[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 개체를 사용 하 여 학습 실험을 제출 합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함하는 원본 디렉터리
* **스크립트**: 학습 스크립트 식별
* **run_config**: [실행 구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)으로, 학습의 발생 위치를 정의 합니다. 에서 `run_config` 학습 스크립트를 실행할 때 사용할 계산 대상과 환경을 지정 합니다.  

## <a name="whats-an-environment"></a>환경 이란?

Azure Machine Learning [환경은](concept-environments.md) Machine Learning 교육이 발생 하는 환경을 캡슐화 하는 것입니다. 이들은 학습 및 점수 매기기 스크립트와 관련 된 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 실행 시간 (Python, Spark 또는 Docker)도 지정 합니다.  

환경은 내의 개체에서 지정 됩니다  `run_config` `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>모델 학습

학습 실행을 제출하는 코드 패턴은 모든 유형의 컴퓨팅 대상에서 동일합니다.

1. 실행할 실험 만들기
1. 스크립트가 실행 되는 환경 만들기
1. 계산 대상 및 환경을 참조 하는 스크립트 실행 구성 만들기
1. 실행 제출
1. 실행이 완료 될 때까지 대기

또는

* [추정기를 사용하여 ML 모델 학습](how-to-train-ml-models.md)에 표시된 대로 `Estimator` 개체와 함께 실험을 제출합니다.
* [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)에 대한 HyperDrive 실행을 제출합니다.
* [VS Code 확장](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)을 통해 실험을 제출합니다.

## <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 실험을 만듭니다.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>환경 만들기

큐 레이트 환경에는 Python 패키지 컬렉션이 포함 되며, 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 환경은 캐시 된 Docker 이미지로 지원 되므로 실행 준비 비용이 줄어듭니다. 원격 계산 대상의 경우 다음의 인기 있는 큐 레이트 환경 중 하나를 사용 하 여 시작할 수 있습니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

환경에 대 한 자세한 내용 및 세부 정보는 [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md)을 참조 하세요.
  
### <a name="local-compute-target"></a>로컬 컴퓨팅 대상

계산 대상이 **로컬 컴퓨터**이면 스크립트가 실행 되는 Python 환경에서 필요한 모든 패키지를 사용할 수 있는지 확인 해야 합니다.  `python.user_managed_dependencies`를 사용 하 여 현재 python 환경 또는 지정 된 경로에서 python을 사용 합니다.

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>스크립트 실행 구성 만들기

이제 계산 대상 ( `compute_target` ) 및 환경 ()이 있으므로 `my_environment` , `train.py` 디렉터리에 있는 학습 스크립트 ()를 실행 하는 스크립트 실행 구성을 만듭니다 `project_folder` .

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

실행에 대 한 프레임 워크를 설정할 수도 있습니다.

* HDI 클러스터의 경우:
    ```python
    src.run_config.framework = "pyspark"
    ```

* 원격 가상 컴퓨터의 경우:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>실험 제출

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> 학습 실행을 제출하면 학습 스크립트가 포함된 디렉터리의 스냅샷이 생성되어 컴퓨팅 대상으로 전송됩니다. 또한 작업 영역에 실험의 일부로 저장됩니다. 파일을 변경하고 실행을 다시 제출하면 변경된 파일만 업로드됩니다.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 스냅숏에 대 한 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshots)을 참조 하십시오.


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="notebook-examples"></a>Notebook 예제

이러한 Notebook에서 다양한 컴퓨팅 대상으로 학습하는 예를 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [RunConfiguration 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 참조를 확인합니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](how-to-enable-virtual-network.md)
