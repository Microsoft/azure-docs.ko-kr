---
title: 보충 learning 모델 (미리 보기)을 학습 하 고 배포 합니다.
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 보충 Learning (미리 보기)을 사용 하 여 Ping를 재생 하는 RL 에이전트를 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.date: 05/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b5ad09925c4a81dd09bd1ddf171ffccb8413b12b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650828"
---
# <a name="reinforcement-learning-preview-with-azure-machine-learning"></a>Azure Machine Learning을 사용하는 보충 학습(미리 보기)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Azure Machine Learning 보충 학습은 현재 미리 보기 기능입니다. 현재 Ray 및 RLlib 프레임워크만 지원됩니다.

이 문서에서는 Pong 비디오 게임을 재생하도록 RL(보충 학습) 에이전트를 학습시키는 방법에 대해 알아봅니다. Azure Machine Learning에서 오픈 소스 Python 라이브러리인 [Ray RLlib](https://ray.readthedocs.io/en/master/rllib.html)를 사용하여 분산된 RL 작업의 복잡성을 관리합니다.

이 문서에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * 실험 설정
> * 헤드 및 작업자 노드 정의
> * RL 예측 도구 만들기
> * 실험을 제출하여 실행 시작
> * 결과 보기

이 문서는 Azure Machine Learning Notebook [GitHub 리포지토리](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)에서 찾을 수 있는 [RLlib Pong 예제](https://aka.ms/azureml-rl-pong)를 기반으로 합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 환경 중 하나에서 이 코드를 실행합니다. 가장 빠른 시작 환경을 위해 Azure Machine Learning 컴퓨팅 인스턴스를 사용하는 것이 좋습니다. 보충 샘플 Notebook은 Azure Machine Learning 컴퓨팅 인스턴스에서 빠르게 복제하고 실행하는 데 사용할 수 있습니다.

 - Azure Machine Learning 컴퓨팅 인스턴스

     - [자습서: 환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md)에서 Notebook 샘플을 복제하는 방법을 알아봅니다.
         - **tutorials** 대신 **how-to-use-azureml** 폴더를 복제합니다.
     - `/how-to-use-azureml/reinforcement-learning/setup/devenv_setup.ipynb`에 있는 가상 네트워크 설치 Notebook을 실행하여 분산된 보충 학습에 사용되는 네트워크 포트를 엽니다.
     - `/how-to-use-azureml/reinforcement-learning/atari-on-distributed-compute/pong_rllib.ipynb` Notebook 샘플을 실행합니다.
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 설치합니다.
    - [Azure Machine Learning RL SDK](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/?view=azure-ml-py)를 설치합니다(`pip install --upgrade azureml-contrib-reinforcementlearning`).
    - [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 만듭니다.
    - 가상 네트워크 [설치 Notebook](https://aka.ms/azure-rl-env-setup)을 실행하여 분산된 보충 학습에 사용되는 네트워크 포트를 엽니다.


## <a name="how-to-train-a-pong-playing-agent"></a>Pong 재생 에이전트를 학습시키는 방법

RL(보충 학습)은 연습을 통해 학습하는 기계 학습 방법입니다. 다른 기계 학습 기술은 입력 데이터를 소극적으로 가져와서 포함된 패턴을 찾는 방식으로 학습하지만, RL은 **학습 에이전트**를 사용하여 적극적으로 결정하고 결과를 통해 학습합니다.

학습 에이전트는 **시뮬레이션된 환경**에서 Pong을 재생하는 방법을 학습합니다. 학습 에이전트는 게임의 모든 프레임에서 패들을 위아래로 이동하거나 제자리에 유지하도록 결정합니다. 게임의 상태(화면의 RGB 이미지)를 확인하여 결정합니다.

RL은 **보상**을 사용하여 결정의 성공 여부를 에이전트에 알려줍니다. 이 환경에서 에이전트는 포인트 점수를 매기는 경우 긍정 보상을 받고, 포인트를 기준으로 점수가 매겨진 경우 부정 보상을 받습니다. 학습 에이전트는 많은 반복에서 현재 상태에 따라 예상되는 미래 보상의 합계를 최적화하는 작업을 선택하도록 학습합니다.

**DNN(심층 신경망)** 모델을 사용하여 RL에서 이 최적화를 수행하는 것이 일반적입니다. 처음에는 학습 에이전트가 비효율적으로 수행되지만 모든 게임에서 추가 샘플을 생성하여 모델을 더욱 향상시킵니다.

에이전트가 교육 기간에 18점의 평균 보상 점수에 도달하면 학습이 종료됩니다. 즉 에이전트가 최대 21회의 시합에서 평균 18점 이상으로 상대를 이겼습니다.

시뮬레이션을 통해 반복하고 DNN을 다시 학습하는 프로세스는 계산 비용이 많이 들고 많은 양의 데이터가 필요합니다. RL 작업의 성능을 향상시키는 한 가지 방법은 여러 학습 에이전트에서 동시에 작업하고 학습할 수 있도록 **작업 병렬화**를 수행하는 것입니다. 그러나 분산된 RL 환경을 관리하는 작업은 복잡할 수 있습니다.

Azure Machine Learning은 이러한 복잡성을 관리하여 RL 워크로드를 확장할 수 있는 프레임워크를 제공합니다.

## <a name="set-up-the-environment"></a>환경 설정

로컬 RL 환경은 필요한 Python 패키지를 로드하고, 작업 영역을 초기화하며, 실험을 만들고, 구성된 가상 네트워크를 지정하여 설정합니다.

### <a name="import-libraries"></a>라이브러리 가져오기

이 예제의 나머지 부분을 실행하는 데 필요한 Python 패키지를 가져옵니다.

```python
# Azure ML Core imports
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
from azureml.core.runconfig import EnvironmentDefinition
from azureml.widgets import RunDetails
from azureml.tensorboard import Tensorboard

# Azure ML Reinforcement Learning imports
from azureml.contrib.train.rl import ReinforcementLearningEstimator, Ray
from azureml.contrib.train.rl import WorkerConfiguration
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역](concept-workspace.md)은 최상위 수준의 Azure Machine Learning 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다.

[사전 요구 사항 섹션](#prerequisites)에서 만든 `config.json` 파일의 작업 영역 개체를 초기화합니다. Azure Machine Learning 컴퓨팅 인스턴스에서 이 코드를 실행하는 경우 구성 파일이 이미 만들어져 있습니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-reinforcement-learning-experiment"></a>보충 학습 실험 만들기

보충 학습 실행을 추적하는 [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)을 만듭니다. Azure Machine Learning에서 실험은 실행 로그, 기록, 출력 등을 구성하는 데 관련된 시험의 논리적 컬렉션입니다.

```python
experiment_name='rllib-pong-multi-node'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="specify-a-virtual-network"></a>가상 네트워크 지정

여러 컴퓨팅 대상을 사용하는 RL 작업의 경우 작업자 노드와 헤드 노드에서 서로 통신할 수 있는 개방 포트가 있는 가상 네트워크를 지정해야 합니다. 가상 네트워크는 모든 리소스 그룹에 있을 수 있지만 작업 영역과 동일한 지역에 있어야 합니다. 가상 네트워크 설정에 대한 자세한 내용은 사전 요구 사항 섹션에서 찾을 수 있는 [작업 영역 설치 Notebook](https://aka.ms/azure-rl-env-setup)을 참조하세요. 여기서는 리소스 그룹의 가상 네트워크 이름을 지정합니다.

```python
vnet = 'your_vnet'
```

## <a name="define-head-and-worker-compute-targets"></a>헤드 및 작업자 컴퓨팅 대상 정의

이 예제에서는 Ray 헤드 및 작업자 노드에 대해 별도의 컴퓨팅 대상을 사용합니다. 이러한 설정을 통해 예상 워크로드에 따라 컴퓨팅 리소스를 확장하거나 축소할 수 있습니다. 실험의 요구에 따라 노드 수와 각 노드의 크기를 설정합니다.

### <a name="head-computing-target"></a>헤드 컴퓨팅 대상

이 예제에서는 GPU 장착 헤드 클러스터를 사용하여 딥 러닝 성능을 최적화합니다. 헤드 노드는 에이전트에서 결정할 때 사용하는 신경망을 학습시킵니다. 또한 헤드 노드는 작업자 노드에서 데이터 요소를 수집하여 신경망을 추가로 학습시킵니다.

헤드 컴퓨팅에서 단일 [`STANDARD_NC6` VM(가상 머신)](https://docs.microsoft.com/azure/virtual-machines/nc-series)을 사용합니다. 6개의 가상 CPU가 있습니다. 즉 작업을 6개의 작업 CPU에 배포할 수 있습니다.


```python
from azureml.core.compute import AmlCompute, ComputeTarget

# choose a name for the Ray head cluster
head_compute_name = 'head-gpu'
head_compute_min_nodes = 0
head_compute_max_nodes = 2

# This example uses GPU VM. For using CPU VM, set SKU to STANDARD_D2_V2
head_vm_size = 'STANDARD_NC6'

if head_compute_name in ws.compute_targets:
    head_compute_target = ws.compute_targets[head_compute_name]
    if head_compute_target and type(head_compute_target) is AmlCompute:
        print(f'found head compute target. just use it {head_compute_name}')
else:
    print('creating a new head compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = head_vm_size,
                                                                min_nodes = head_compute_min_nodes, 
                                                                max_nodes = head_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    head_compute_target = ComputeTarget.create(ws, head_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    head_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(head_compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="worker-computing-cluster"></a>작업자 컴퓨팅 클러스터

이 예제에서는 작업자 컴퓨팅 대상에 대해 4개의 [`STANDARD_D2_V2` VM](https://docs.microsoft.com/azure/virtual-machines/nc-series)을 사용합니다. 각 작업자 노드에는 2개의 사용 가능한 CPU가 있으며, 총 8개의 사용 가능한 CPU를 통해 작업을 병렬화할 수 있습니다.

작업자 노드에서 딥 러닝을 수행하지 않으므로 GPU는 필요하지 않습니다. 작업자는 게임 시뮬레이션을 실행하고 데이터를 수집합니다.

```python
# choose a name for your Ray worker cluster
worker_compute_name = 'worker-cpu'
worker_compute_min_nodes = 0 
worker_compute_max_nodes = 4

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
worker_vm_size = 'STANDARD_D2_V2'

# Create the compute target if it hasn't been created already
if worker_compute_name in ws.compute_targets:
    worker_compute_target = ws.compute_targets[worker_compute_name]
    if worker_compute_target and type(worker_compute_target) is AmlCompute:
        print(f'found worker compute target. just use it {worker_compute_name}')
else:
    print('creating a new worker compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = worker_vm_size,
                                                                min_nodes = worker_compute_min_nodes, 
                                                                max_nodes = worker_compute_max_nodes,
                                                                vnet_resourcegroup_name = ws.resource_group,
                                                                vnet_name = vnet_name,
                                                                subnet_name = 'default')

    # create the cluster
    worker_compute_target = ComputeTarget.create(ws, worker_compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    worker_compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(worker_compute_target.get_status().serialize())
```

## <a name="create-a-reinforcement-learning-estimator"></a>보충 학습 예측 도구 만들기

이 섹션에서는 [ReinforcementLearningEstimator](https://docs.microsoft.com/python/api/azureml-contrib-reinforcementlearning/azureml.contrib.train.rl.reinforcementlearningestimator?view=azure-ml-py)를 사용하여 학습 작업을 Azure Machine Learning에 제출하는 방법을 알아봅니다.

Azure Machine Learning은 예측 도구 클래스를 사용하여 실행 구성 정보를 캡슐화합니다. 이렇게 하면 스크립트 실행을 구성하는 방법을 쉽게 지정할 수 있습니다. Azure Machine Learning 예측 도구 패턴에 대한 자세한 내용은 [예측 도구를 사용하여 모델을 학습시키는 방법](how-to-train-ml-models.md)을 참조하세요.

### <a name="define-a-worker-configuration"></a>작업자 구성 정의

WorkerConfiguration 개체는 항목 스크립트를 실행하는 작업자 클러스터를 초기화하는 방법을 Azure Machine Learning에 지시합니다.

```python
# Pip packages we will use for both head and worker
pip_packages=["ray[rllib]==0.8.3"] # Latest version of Ray has fixes for isses related to object transfers

# Specify the Ray worker configuration
worker_conf = WorkerConfiguration(
    
    # Azure ML compute cluster to run Ray workers
    compute_target=worker_compute_target, 
    
    # Number of worker nodes
    node_count=4,
    
    # GPU
    use_gpu=False, 
    
    # PIP packages to use
    pip_packages=pip_packages
)
```

### <a name="define-script-parameters"></a>스크립트 매개 변수 정의

`pong_rllib.py` 항목 스크립트는 학습 작업을 실행하는 방법을 정의하는 매개 변수의 목록을 허용합니다. 이러한 매개 변수를 예측 도구를 통해 캡슐화 계층으로 전달하면 스크립트 매개 변수를 쉽게 변경하고 구성을 서로 독립적으로 실행할 수 있습니다.

올바른 `num_workers`를 지정하면 병렬 처리 작업을 최대한 활용할 수 있습니다. 작업자 수를 사용 가능한 CPU 수와 동일하게 설정합니다. 다음 예제에서는 이를 다음과 같이 계산할 수 있습니다.

헤드 노드는 6개의 vCPU가 있는 [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series)입니다. 작업자 클러스터는 각각 2개의 CPU가 있는 4개의 [Standard_D2_V2 VM](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs#dv2-series)이며, 총 8개의 CPU가 있습니다. 그러나 하나는 헤드 노드 역할 전용이어야 하므로 작업자 수에서 하나의 CPU를 빼야 합니다. 즉 6개 CPU + 8개 CPU - 1개 헤드 CPU = 13개 동시 작업자입니다. Azure Machine Learning은 헤드 및 작업자 클러스터를 사용하여 컴퓨팅 리소스를 구분합니다. 그러나 Ray는 헤드와 작업자를 구분하지 않으며, 모든 CPU가 작업자 스레드 실행에 사용할 수 있는 CPU입니다.


```python
training_algorithm = "IMPALA"
rl_environment = "PongNoFrameskip-v4"

# Training script parameters
script_params = {
    
    # Training algorithm, IMPALA in this case
    "--run": training_algorithm,
    
    # Environment, Pong in this case
    "--env": rl_environment,
    
    # Add additional single quotes at the both ends of string values as we have spaces in the 
    # string parameters, outermost quotes are not passed to scripts as they are not actually part of string
    # Number of GPUs
    # Number of ray workers
    "--config": '\'{"num_gpus": 1, "num_workers": 13}\'',
    
    # Target episode reward mean to stop the training
    # Total training time in seconds
    "--stop": '\'{"episode_reward_mean": 18, "time_total_s": 3600}\'',
}
```

### <a name="define-the-reinforcement-learning-estimator"></a>보충 학습 예측 도구 정의

매개 변수 목록과 작업자 구성 개체를 사용하여 예측 도구를 구성합니다.

```python
# RL estimator
rl_estimator = ReinforcementLearningEstimator(
    
    # Location of source files
    source_directory='files',
    
    # Python script file
    entry_script="pong_rllib.py",
    
    # Parameters to pass to the script file
    # Defined above.
    script_params=script_params,
    
    # The Azure ML compute target set up for Ray head nodes
    compute_target=head_compute_target,
    
    # Pip packages
    pip_packages=pip_packages,
    
    # GPU usage
    use_gpu=True,
    
    # RL framework. Currently must be Ray.
    rl_framework=Ray(),
    
    # Ray worker configuration defined above.
    worker_configuration=worker_conf,
    
    # How long to wait for whole cluster to start
    cluster_coordination_timeout_seconds=3600,
    
    # Maximum time for the whole Ray job to run
    # This will cut off the run after an hour
    max_run_duration_seconds=3600,
    
    # Allow the docker container Ray runs in to make full use
    # of the shared memory available from the host OS.
    shm_size=24*1024*1024*1024
)
```

### <a name="entry-script"></a>항목 스크립트

`pong_rllib.py` [항목 스크립트](https://aka.ms/azure-rl-pong-script)는 `PongNoFrameSkip-v4` [OpenAI Gym 환경](https://github.com/openai/gym/)을 사용하여 신경망을 학습시킵니다. OpenAI Gym은 클래식 Atari 게임에서 보충 학습 알고리즘을 테스트하기 위한 표준화된 인터페이스입니다.

다음 예제에서는 [IMPALA(Importance Weighted Actor-Learner Architecture)](https://arxiv.org/abs/1802.01561)라는 학습 알고리즘을 사용합니다. IMPALA는 각 개별 학습 행위자를 병렬화하여 속도 또는 안정성을 유지하면서 여러 컴퓨팅 노드를 통해 크기를 조정합니다.

[Ray Tune](https://ray.readthedocs.io/en/latest/tune.html)은 IMPALA 작업자 작업을 오케스트레이션합니다.

```python
import ray
import ray.tune as tune
from ray.rllib import train

import os
import sys

from azureml.core import Run
from utils import callbacks

DEFAULT_RAY_ADDRESS = 'localhost:6379'

if __name__ == "__main__":

    # Parse arguments
    train_parser = train.create_parser()

    args = train_parser.parse_args()
    print("Algorithm config:", args.config)

    if args.ray_address is None:
        args.ray_address = DEFAULT_RAY_ADDRESS

    ray.init(address=args.ray_address)

    tune.run(run_or_experiment=args.run,
             config={
                 "env": args.env,
                 "num_gpus": args.config["num_gpus"],
                 "num_workers": args.config["num_workers"],
                 "callbacks": {"on_train_result": callbacks.on_train_result},
                 "sample_batch_size": 50,
                 "train_batch_size": 1000,
                 "num_sgd_iter": 2,
                 "num_data_loader_buffers": 2,
                 "model": {
                    "dim": 42
                 },
             },
             stop=args.stop,
             local_dir='./logs')
```

### <a name="logging-callback-function"></a>로깅 콜백 함수


항목 스크립트는 유틸리티 함수를 사용하여 메트릭을 Azure Machine Learning 작업 영역에 기록하도록 [사용자 지정 RLlib 콜백 함수](https://docs.ray.io/en/latest/rllib-training.html#callbacks-and-custom-metrics)를 정의합니다. 이러한 메트릭을 보는 방법은 [모니터링 및 결과 보기](#monitor-and-view-results) 섹션에서 알아보세요.

```python
'''RLlib callbacks module:
    Common callback methods to be passed to RLlib trainer.
'''
from azureml.core import Run

def on_train_result(info):
    '''Callback on train result to record metrics returned by trainer.
    '''
    run = Run.get_context()
    run.log(
        name='episode_reward_mean',
        value=info["result"]["episode_reward_mean"])
    run.log(
        name='episodes_total',
        value=info["result"]["episodes_total"])
```

## <a name="submit-a-run"></a>실행 제출

[실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)은 진행 중이거나 완료된 작업의 실행 기록을 처리합니다. 

```python
run = exp.submit(config=rl_estimator)
```
> [!NOTE]
> 실행을 완료하는 데 최대 30-45분 정도 걸릴 수 있습니다.

## <a name="monitor-and-view-results"></a>모니터링 및 결과 보기

Azure Machine Learning Jupyter 위젯을 사용하여 실행 상태를 실시간으로 확인합니다. 다음 예제에서는 위젯에서 두 개의 자식 실행(각각 헤드 및 작업자의 경우)을 보여 줍니다. 

```python
from azureml.widgets import RunDetails

RunDetails(run).show()
run.wait_for_completion()
```

1. 위젯이 로드될 때까지 기다립니다.
1. 실행 목록에서 헤드 실행을 선택합니다.

스튜디오에서 추가 실행 정보를 보려면 **Azure Machine Learning Studio에서 실행을 확인하려면 여기를 클릭하세요.** 를 선택합니다. 이 정보는 실행이 진행 중이거나 완료되는 경우에 액세스할 수 있습니다.

![실행 세부 정보 위젯을 보여주는 선 그래프](./media/how-to-use-reinforcement-learning/pong-run-details-widget.png)

**episode_reward_mean** 도표에서는 교육 기간당 득점한 평균 점수를 보여 줍니다. 처음에는 학습 에이전트에서 비효율적으로 수행되어 한 점의 점수도 득점하지 못한 채 시합에서 패했음을 알 수 있습니다(-21의 reward_mean으로 표시됨). 학습 에이전트는 100회 반복 내에서 평균 18점으로 컴퓨터 상대를 이기는 방법을 학습했습니다.

자식 실행 로그를 찾아보면 driver_log.txt 파일에 기록된 평가 결과를 확인할 수 있습니다. 실행 페이지에서 이러한 메트릭을 사용할 수 있을 때까지 몇 분 정도 기다려야 할 수 있습니다.

간단한 작업에서 Pong을 매우 효율적으로 실행할 수 있도록 보충 학습 에이전트를 학습시키기 위해 여러 컴퓨팅 리소스를 구성하는 방법을 알아보았습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IMPALA 학습 에이전트를 사용하여 보충 학습 에이전트를 학습시키는 방법을 알아보았습니다. 추가 예제를 보려면 [Azure Machine Learning 보충 학습 GitHub 리포지토리](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/reinforcement-learning/README.md)로 이동하세요.
