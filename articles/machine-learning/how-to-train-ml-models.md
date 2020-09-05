---
title: 추정기를 사용하여 ML 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Estimator 클래스를 사용하여 일반적인 기계 학습 및 딥 러닝 모델의 단일 노드 분산 학습을 수행하는 방법을 알아봅니다.
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 70e965e26d3b82cdc63a3c0e147919b8b40585af
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146592"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>추정기를 사용하여 Azure Machine Learning에서 모델 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning에서는 [RunConfiguration 개체](how-to-set-up-training-targets.md#whats-a-run-configuration) 및 [ScriptRunConfig 개체](how-to-set-up-training-targets.md#submit)를 사용하여 학습 스크립트를 [다양한 컴퓨팅 대상](how-to-set-up-training-targets.md)에 제출할 수 있습니다. 이러한 패턴은 풍부한 유연성과 최대의 제어 능력을 제공합니다.


예측 도구 클래스를 사용하면 딥 러닝 및 보충 학습을 통해 보다 쉽게 모델을 학습시킬 수 있습니다. 실행 구성을 쉽게 구성할 수 있는 높은 수준의 추상화를 제공합니다. 제네릭 [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)를 만들고 사용하여 선택한 컴퓨팅 대상(로컬 머신, Azure의 단일 VM, Azure의 GPU 클러스터 등)에서 선택한 학습 프레임워크(예: scikit-learn)를 사용하여 학습 스크립트를 제출할 수 있습니다. PyTorch, TensorFlow, Chainer 및 보충 학습 작업의 경우 Azure Machine Learning은 이러한 프레임워크 사용을 간소화할 수 있도록 각각의 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 및 [보충 학습](how-to-use-reinforcement-learning.md) 예측 도구를 제공합니다.

## <a name="train-with-an-estimator"></a>추정기를 사용하여 학습

[작업 영역](concept-workspace.md)을 만들고 [개발 환경](how-to-configure-environment.md)을 설정하고 난 후, Azure Machine Learning에서 모델을 학습하려면 다음 단계를 수행해야 합니다.  
1. [원격 계산 대상을](how-to-create-attach-compute-sdk.md) 만듭니다 (또는 로컬 컴퓨터를 계산 대상으로 사용할 수도 있음).
2. [학습 데이터](how-to-access-data.md)를 데이터 저장소에 업로드(선택 사항)
3. [학습 스크립트](tutorial-train-models-with-aml.md#create-a-training-script) 만들기
4. `Estimator` 개체 만들기
5. 추정기를 작업 영역 아래 실험 개체에 제출

이 문서에서는 4-5단계를 중점적으로 다룹니다. 1-3단계의 경우 [모델 학습 자습서](tutorial-train-models-with-aml.md)에서 예제를 참조하세요.

### <a name="single-node-training"></a>단일 노드 학습

scikit-learn 모델을 위해 Azure의 원격 컴퓨팅에서 실행되는 단일 노드 학습에 `Estimator`를 사용합니다. [컴퓨팅 대상](how-to-create-attach-compute-sdk.md#amlcompute) 개체 `compute_target` 및 [FileDataset](how-to-create-register-datasets.md) 개체 `ds`는 이미 만들어져 있어야 합니다.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

이 코드 조각에서 `Estimator` 생성자에 지정한 매개 변수는 다음과 같습니다.

매개 변수 | Description
--|--
`source_directory`| 학습 작업에 필요한 모든 코드가 포함된 로컬 디렉터리입니다. 이 폴더는 로컬 머신에서 원격 컴퓨팅으로 복사됩니다.
`script_params`| `<command-line argument, value>` 쌍 형식으로 학습 스크립트 `entry_script`에 전달하도록 명령줄 인수를 지정하는 사전입니다. `script_params`에서 자세한 정보 플래그를 지정하려면 `<command-line argument, "">`를 사용합니다.
`compute_target`| 이 경우 학습 스크립트가 Azure Machine Learning 컴퓨팅([AmlCompute](how-to-create-attach-compute-sdk.md#amlcompute)) 클러스터에서 실행되는 원격 컴퓨팅 대상입니다. (AmlCompute 클러스터가 일반적으로 사용되는 대상이기는 하지만 Azure VM 또는 로컬 컴퓨터와 같은 다른 컴퓨팅 대상 유형을 선택할 수도 있습니다.)
`entry_script`| 원격 컴퓨팅에서 실행할 학습 스크립트의 파일 경로(`source_directory` 기준)입니다. 이 파일 및 이 파일이 의존하는 추가 파일은 이 폴더에 있어야 합니다.
`conda_packages`| conda를 통해 설치할 학습 스크립트에 필요한 Python 패키지의 목록입니다.  

생성자에는 필요한 모든 pip 패키지에 사용하는 `pip_packages`라는 매개 변수도 있습니다.

`Estimator` 개체를 만들었으므로 [Experiment](concept-azure-machine-learning-architecture.md#experiments) 개체`experiment`의 `submit` 함수 호출을 통해 원격 컴퓨팅에서 실행할 학습 작업을 제출합니다. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> 두 개의 **특수 폴더**인 *outputs* 및 *logs*는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs*라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 아티팩트를 만들려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

### <a name="distributed-training-and-custom-docker-images"></a>분산 학습 및 사용자 지정 Docker 이미지

`Estimator`를 사용하여 수행할 수 있는 두 가지 추가 학습 시나리오는 다음과 같습니다.
* 사용자 지정 Docker 이미지 사용
* 다중 노드 클러스터에서 분산 학습

다음 코드에서는 Keras 모델에 대한 분산 학습을 수행하는 방법을 보여 줍니다. 또한 기본 Azure Machine Learning 이미지를 사용하는 대신, 학습을 위해 Docker Hub `continuumio/miniconda`의 사용자 지정 Docker 이미지를 지정합니다.

[컴퓨팅 대상](how-to-create-attach-compute-sdk.md#amlcompute) 개체 `compute_target`은 이미 만들어져 있어야 합니다. 추정기는 다음과 같이 만듭니다.

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

위의 코드는 `Estimator` 생성자에 다음과 같은 새 매개 변수를 표시합니다.

매개 변수 | Description | 기본값
--|--|--
`custom_docker_image`| 사용하려는 이미지의 이름입니다. 공용 Docker 리포지토리(여기서는 Docker 허브)에서 사용할 수 있는 이미지만 제공합니다. 프라이빗 Docker 리포지토리의 이미지를 사용하려면 생성자의 `environment_definition` 매개 변수를 대신 사용합니다.| `None`
`node_count`| 학습 작업에 사용할 노드의 수입니다. | `1`
`process_count_per_node`| 각 노드에서 실행할 프로세스(또는 “작업자”)의 수입니다. 여기서는 각 노드에서 사용할 수 있는 `2` GPU를 사용합니다.| `1`
`distributed_training`| MPI 백 엔드를 사용하여 분산 학습을 시작하기 위한 [MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 개체입니다.  | `None`


마지막으로, 학습 작업을 제출합니다.
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>모델 등록

모델을 학습한 후에는 작업 영역에 저장하고 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

다음 코드를 실행하면 모델을 작업 영역에 등록하고, 원격 컴퓨팅 컨텍스트 또는 배포 스크립트에서 이름으로 참조할 수 있도록 합니다. 자세한 내용 및 추가 매개 변수는 참조 문서에서 [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)을 참조하세요.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="examples"></a>예제

예측 도구를 사용하여 scikit-learn 모델을 학습하는 Notebook은 다음을 참조하세요.
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

딥 러닝 프레임워크 관련 예측 도구를 사용하는 학습 모델에 대한 Notebook은 다음을 참조하세요.

* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [PyTorch 모델 학습](how-to-train-pytorch.md)
* [TensorFlow 모델 학습](how-to-train-tensorflow.md)
* [보충 학습 심층 신경망 학습](how-to-use-reinforcement-learning.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)