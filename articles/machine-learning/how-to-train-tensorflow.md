---
title: TensorFlow를 사용 하 여 신경망 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 대규모로 TensorFlow 교육 스크립트를 실행 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: a1c3e1948d53a168ce9a3e99cd932fa04e2fafc4
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114376"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 대규모로 TensorFlow 심층 학습 모델 빌드
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning의 [TensorFlow 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 클래스를 사용 하 여 대규모로 [TensorFlow](https://www.tensorflow.org/overview) 교육 스크립트를 실행 하는 방법을 보여 줍니다. 이 예제에서는 TensorFlow 모델을 학습 하 고 등록 하 여 DNN (심층 신경망)를 사용 하 여 필기 된 숫자를 분류 합니다.

TensorFlow 모델을 처음부터 개발 하 든, [기존 모델](how-to-deploy-existing-model.md) 을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 오픈 소스 학습 작업을 확장 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다.

[심층 학습 vs machine learning](concept-deep-learning-vs-machine-learning.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

이러한 환경 중 하나에서이 코드를 실행 합니다.

 - Azure Machine Learning 계산 인스턴스-다운로드 또는 설치 필요 없음

     - [자습서: 설치 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md) 을 완료 하 여 SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 한 전용 노트북 서버를 만듭니다.
    - 노트북 서버의 샘플 심층 학습 폴더에서이 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > ml-프레임 워크 > tensorflow > 배포 > 학습-hyperparameter** 폴더. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - `mnist-tf.py` [샘플 스크립트 파일을 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) 하 고 `utils.py`
     
    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 Python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다. Python SDK에서 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다.

[전제 조건 섹션](#prerequisites)에서 만든 `config.json` 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>심층 학습 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예제에서는 "tf-mnist" 라는 실험을 만듭니다.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>파일 데이터 집합 만들기

`FileDataset` 개체는 작업 영역 데이터 저장소 또는 공용 URL에서 하나 이상의 파일을 참조합니다. 파일은 모든 형식일 수 있으며, 클래스는 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. `FileDataset`를 생성하여 데이터 원본 위치에 대한 참조를 만듭니다. 데이터 세트에 변환을 적용한 경우 데이터 세트에도 저장됩니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 스토리지 비용이 발생하지 않습니다. 자세한 내용은 [ 패키지에 대한 ](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)방법`Dataset` 가이드를 참조하세요.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

`register()` 메서드를 사용 하 여 데이터 집합을 다른 사용자와 공유 하 고, 다양 한 실험에서 재사용 하 고, 학습 스크립트에서 이름으로 참조할 수 있도록 작업 영역에 데이터 집합을 등록할 수 있습니다.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

TensorFlow 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

## <a name="create-a-tensorflow-estimator"></a>TensorFlow 평가기 만들기

[TensorFlow 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 는 계산 대상에서 TensorFlow 학습 작업을 시작 하는 간단한 방법을 제공 합니다.

TensorFlow 평가기는 프레임 워크를 지 원하는 데 사용할 수 있는 제네릭 [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 클래스를 통해 구현 됩니다. 제네릭 평가기을 사용한 학습 모델에 대 한 자세한 내용은 [평가기를 사용 하 여 Azure Machine Learning를](how-to-train-ml-models.md) 사용 하 여 모델 학습을 참조 하세요.

학습 스크립트에 추가 pip 또는 conda 패키지를 실행 해야 하는 경우 `pip_packages` 및 `conda_packages` 인수를 통해 해당 이름을 전달 하 여 결과 Docker 이미지에 패키지를 설치할 수 있습니다.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Tensorflow 평가기 클래스에 **Tensorflow 2.0** 에 대 한 지원이 추가 되었습니다. 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) 을 참조 하세요.

Python 환경을 사용자 지정 하는 방법에 대 한 자세한 내용은 [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요. 

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: TensorFlow 평가기에 따라 Docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용 하면 모델 [관리 및 배포](concept-model-management-and-deployment.md)를 간소화 하기 위해 작업 영역에 모델을 저장 하 고 버전을 지정할 수 있습니다. `model_framework`, `model_framework_version`및 `resource_configuration`매개 변수를 지정 하면 코드 없는 모델 배포를 사용할 수 있게 됩니다. 이렇게 하면 등록 된 모델에서 모델을 웹 서비스로 직접 배포할 수 있으며 `ResourceConfiguration` 개체는 웹 서비스의 계산 리소스를 정의 합니다.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Run 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트 `mnist-tf.py`에서 TensorFlow 보호기 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. Run 개체를 사용 하 여 복사본을 다운로드할 수 있습니다.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>분산 학습

또한 [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) 평가기는 CPU 및 GPU 클러스터에서 분산 된 학습을 지원 합니다. Distributed TensorFlow 작업을 쉽게 실행할 수 있으며, Azure Machine Learning는 오케스트레이션을 관리할 수 있습니다.

Azure Machine Learning은 TensorFlow에서 두 가지 분산 학습 방법을 지원합니다.

- [Horovod](https://github.com/uber/horovod) framework를 사용 하는 [MPI 기반](https://www.open-mpi.org/) 분산 교육
- Parameter server 메서드를 사용 하는 네이티브 [Distributed TensorFlow](https://www.tensorflow.org/deploy/distributed)

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) 는 uber에서 개발한 분산 교육을 위한 오픈 소스 프레임 워크입니다. Distributed GPU TensorFlow 작업에 대 한 쉬운 경로를 제공 합니다.

Horovod를 사용 하려면 TensorFlow 생성자에서 `distributed_training` 매개 변수에 대 한 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) 개체를 지정 합니다. 이 매개 변수는 학습 스크립트에서 사용할 Horovod 라이브러리가 설치 되어 있는지 확인 합니다.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>매개 변수 서버

매개 변수 서버 모델을 사용하는 [기본 분산 TensorFlow](https://www.tensorflow.org/deploy/distributed)를 실행할 수도 있습니다. 이 방법에서는 매개 변수 서버 및 작업자 클러스터 전반에 걸쳐 학습을 실행합니다. 작업자는 학습 중에 그라데이션을 계산하고 매개 변수 서버는 그라데이션을 집계합니다.

매개 변수 서버 메서드를 사용 하려면 TensorFlow 생성자에서 `distributed_training` 매개 변수에 대 한 [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) 개체를 지정 합니다.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>' TF_CONFIG '에서 클러스터 사양을 정의 합니다.

또한 [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)에 대 한 클러스터의 네트워크 주소와 포트가 필요 하므로 Azure Machine Learning `TF_CONFIG` 환경 변수를 설정 해야 합니다.

`TF_CONFIG` 환경 변수는 JSON 문자열입니다. 매개 변수 서버의 변수 예제는 다음과 같습니다.

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

TensorFlow의 상위 수준 [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) API의 경우 TensorFlow는 `TF_CONFIG` 변수를 구문 분석 하 고 클러스터 사양을 작성 합니다.

TensorFlow의 낮은 수준의 핵심 Api를 학습 하려면 `TF_CONFIG` 변수를 구문 분석 하 고 `tf.train.ClusterSpec`를 학습 코드에 작성 합니다.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deployment"></a>배포

방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포할 수 있습니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

### <a name="preview-no-code-model-deployment"></a>모드 코드 없는 모델 배포

기존 배포 경로 대신 Tensorflow에 대 한 비 코드 배포 기능 (미리 보기)을 사용할 수도 있습니다. 위에 표시 된 대로 `model_framework`, `model_framework_version`및 `resource_configuration` 매개 변수를 사용 하 여 모델을 등록 하면 단순히 `deploy()` 정적 함수를 사용 하 여 모델을 배포할 수 있습니다.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Azure Machine Learning의 배포에 [대 한 자세한 내용을](how-to-deploy-and-where.md) 자세히 설명 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 TensorFlow 모델을 학습 하 고 등록 했으며 배포 옵션에 대해 알아보았습니다. Azure Machine Learning에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)
