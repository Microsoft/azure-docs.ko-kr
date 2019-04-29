---
title: 추정기를 사용하여 ML 모델 학습
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 Estimator 클래스를 사용하여 일반적인 Machine Learning 및 딥 러닝 모델의 단일 노드 분산 학습을 수행하는 방법을 알아봅니다.
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 7b479556543c6a9dff88643fdc587dec3f832f39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818482"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>추정기를 사용하여 Azure Machine Learning에서 모델 학습

Azure Machine Learning에서는 [RunConfiguration 개체](how-to-set-up-training-targets.md#whats-a-run-configuration) 및 [ScriptRunConfig 개체](how-to-set-up-training-targets.md#submit)를 사용하여 학습 스크립트를 [다양한 컴퓨팅 대상](how-to-set-up-training-targets.md#compute-targets-for-training)에 제출할 수 있습니다. 이러한 패턴은 풍부한 유연성과 최대의 제어 능력을 제공합니다.

딥 러닝 모델 학습을 용이하게 하기 위해 Azure Machine Learning Python SDK는 사용자가 실행 구성을 쉽게 생성할 수 있게 하는 높은 수준의 대체 추상화인 estimator 클래스를 제공합니다. 제네릭 [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)를 만들고 사용하여 컴퓨팅 대상(로컬 머신, Azure의 단일 VM, Azure의 GPU 클러스터 등)에서 실행하고자 하는 선택한 학습 프레임워크(예: scikit-learn)를 사용하여 학습 스크립트를 제출할 수 있습니다. PyTorch, TensorFlow 및 Chainer 작업의 경우 Azure Machine Learning은 이러한 프레임워크 사용을 간소화할 수 있도록 각각의 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 및 [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 추정기를 제공합니다.

## <a name="train-with-an-estimator"></a>추정기를 사용하여 학습

[작업 영역](concept-azure-machine-learning-architecture.md#workspace)을 만들고 [개발 환경](how-to-configure-environment.md)을 설정하고 난 후, Azure Machine Learning에서 모델을 학습하려면 다음 단계를 수행해야 합니다.  
1. [원격 컴퓨팅 대상](how-to-set-up-training-targets.md) 만들기(컴퓨팅 대상으로 로컬 컴퓨터를 사용할 수도 있음)
2. [학습 데이터](how-to-access-data.md)를 데이터 저장소에 업로드(선택 사항)
3. [학습 스크립트](tutorial-train-models-with-aml.md#create-a-training-script) 만들기
4. `Estimator` 개체 만들기
5. 추정기를 작업 영역 아래 실험 개체에 제출

이 문서에서는 4-5단계를 중점적으로 다룹니다. 1-3단계의 경우 [모델 학습 자습서](tutorial-train-models-with-aml.md)에서 예제를 참조하세요.

### <a name="single-node-training"></a>단일 노드 학습

scikit-learn 모델을 위해 Azure의 원격 계산에서 실행되는 단일 노드 학습에 `Estimator`를 사용합니다. `compute_target` [계산 대상](how-to-set-up-training-targets.md#amlcompute) 개체와 `ds` [데이터 저장소](how-to-access-data.md) 개체는 이미 만들어져 있어야 합니다.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

이 코드 조각에서 `Estimator` 생성자에 지정한 매개 변수는 다음과 같습니다.

매개 변수 | 설명
--|--
`source_directory`| 학습 작업에 필요한 모든 코드가 포함된 로컬 디렉터리입니다. 이 폴더는 로컬 머신에서 원격 컴퓨팅으로 복사됩니다. 
`script_params`| 학습 스크립트에 명령줄 인수를 지정 하는 사전 `entry_script`, 형식의 < 명령줄 인수를 값 > 쌍입니다. Verbose 플래그를 지정 하려면 `script_params`를 사용 하 여 `<command-line argument, "">`입니다.
`compute_target`| 이 경우 학습 스크립트가 Azure Machine Learning 컴퓨팅([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) 클러스터에서 실행되는 원격 컴퓨팅 대상입니다. AmlCompute 클러스터가 일반적으로 사용되는 대상이기는 하지만 Azure VM 또는 로컬 컴퓨터와 같은 다른 컴퓨팅 대상 유형을 선택할 수도 있습니다.
`entry_script`| 원격 계산에서 실행할 학습 스크립트의 파일 경로(`source_directory` 기준)입니다. 이 파일 및 이 파일이 의존하는 추가 파일은 이 폴더에 있어야 합니다.
`conda_packages`| conda를 통해 설치할 학습 스크립트에 필요한 Python 패키지의 목록입니다.  

생성자에는 필요한 모든 pip 패키지에 사용하는 `pip_packages`라는 매개 변수도 있습니다.

`Estimator` 개체를 만들었으므로 [Experiment](concept-azure-machine-learning-architecture.md#experiment) 개체`experiment`의 `submit` 함수 호출을 통해 원격 계산에서 실행할 학습 작업을 제출합니다. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> 두 개의 **특수 폴더**인 *outputs* 및 *logs*는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs*라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 아티팩트를 만들려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://aka.ms/aml-notebook-tb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

### <a name="distributed-training-and-custom-docker-images"></a>분산 학습 및 사용자 지정 Docker 이미지

`Estimator`를 사용하여 수행할 수 있는 두 가지 추가 학습 시나리오는 다음과 같습니다.
* 사용자 지정 Docker 이미지 사용
* 다중 노드 클러스터에서 분산 학습

다음 코드에서는 Keras 모델에 대한 분산 학습을 수행하는 방법을 보여 줍니다. 또한 기본 Azure Machine Learning 이미지를 사용하는 대신, 학습을 위해 Docker Hub `continuumio/miniconda`의 사용자 지정 Docker 이미지를 지정합니다.

[계산 대상](how-to-set-up-training-targets.md#amlcompute) 개체 `compute_target`는 이미 만들어져 있어야 합니다. 추정기는 다음과 같이 만듭니다.

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

위의 코드는 `Estimator` 생성자에 다음과 같은 새 매개 변수를 표시합니다.

매개 변수 | 설명 | 기본값
--|--|--
`custom_docker_base_image`| 사용하려는 이미지의 이름입니다. 공용 Docker 리포지토리(여기서는 Docker 허브)에서 사용할 수 있는 이미지만 제공합니다. 개인 Docker 리포지토리의 이미지를 사용하려면 생성자의 `environment_definition` 매개 변수를 대신 사용합니다. [예제를 참조하세요](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| 학습 작업에 사용할 노드의 수입니다. | `1`
`process_count_per_node`| 각 노드에서 실행할 프로세스(또는 “작업자”)의 수입니다. 여기서는 각 노드에서 사용할 수 있는 `2` GPU를 사용합니다.| `1`
`distributed_backend`| Estimator가 MPI를 통해 제공하는 분산 학습 시작을 위한 백 엔드입니다.  병렬 또는 분산 학습을 수행하려면(예: `node_count`>1, `process_count_per_node`>1 또는 둘 다) `distributed_backend='mpi'`를 설정합니다. AML에서 사용되는 MPI 구현은 [Open MPI](https://www.open-mpi.org/)입니다.| `None`

마지막으로, 학습 작업을 제출합니다.
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="examples"></a>예
추정기 패턴의 기본 사항을 보여 주는 Notebook은 다음을 참조하세요.
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

추정기를 사용하여 scikit-learn 모델을 학습하는 Notebook은 다음을 참조하세요.
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

딥 러닝 프레임워크 관련 추정기를 사용하는 학습 모델에 대한 Notebook은 다음을 참조하세요.
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [PyTorch 모델 학습](how-to-train-pytorch.md)
* [TensorFlow 모델 학습](how-to-train-tensorflow.md)
* [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
