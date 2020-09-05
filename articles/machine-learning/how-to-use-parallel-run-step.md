---
title: 빅 데이터에서 일괄 예측 실행
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 ParallelRunStep을 사용하여 대량의 데이터에 대한 비동기 유추를 수행하는 방법을 알아봅니다. ParallelRunStep은 기본적으로 병렬 처리 기능을 제공하며 빅 데이터 사용 사례를 위한 높은 처리량의 fire-and-forget(실행 후 망각) 유추에 맞게 최적화됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 04d1e531f3041ef0a6231607cc795c67168ebf2e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651202"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Azure Machine Learning을 사용하여 대량의 데이터에 대한 일괄 처리 유추 실행
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 모델을 병렬로 실행하여 대량의 데이터를 신속하게 평가하는 방법을 보여줍니다. 

대규모 데이터 세트 또는 복잡한 모델에 대한 추론은 시간이 많이 걸릴 수 있습니다. `ParallelRunStep` 클래스를 사용하면 병렬로 처리를 수행하여 잠재적으로 전체 결과를 더 빠르게 얻을 수 있습니다. 단일 평가를 실행하는 것이 매우 빠르게 수행되는 경우에도 많은 시나리오(개체 검색, 비디오 처리, 자연어 처리 등)는 많은 평가를 실행하는 것과 관련이 있습니다. 

`ParallelRunStep`를 사용하면 대규모 머신 클러스터에 대한 일괄 처리 추론을 쉽게 확장할 수 있습니다. 이러한 클러스터는 향상된 생산성과 최적화된 비용으로 테라바이트 단위의 정형 또는 비정형 데이터를 처리할 수 있습니다.

이 문서에서는 다음 작업에 대해 알아봅니다.

> 1. 기계 학습 리소스 설정
> 1. 일괄 처리 유추 데이터 입력 및 출력을 구성합니다.
> 1. [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 데이터 세트를 기반으로 미리 학습된 이미지 분류 모델을 준비합니다. 
> 1.  유추 스크립트를 작성합니다.
> 1. ParallelRunStep을 포함하는 [기계 학습 파이프라인](concept-ml-pipelines.md)을 만들고 MNIST 테스트 이미지에서 일괄 처리 유추를 실행합니다. 
> 1. 새로운 데이터 입력과 매개 변수를 사용하여 일괄 처리 유추 실행을 다시 전송합니다. 
> 1. 결과를 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* 안내형 빠른 시작의 경우 Azure Machine Learning 작업 영역이 아직 없으면 [설정 자습서](tutorial-1st-experiment-sdk-setup.md)를 완료하세요. 

* 사용자 고유의 환경과 종속성을 관리하려면 사용자 고유의 로컬 환경을 구성하는 방법에 대한 [방법 가이드](how-to-configure-environment.md)를 참조하세요.

## <a name="set-up-machine-learning-resources"></a>기계 학습 리소스 설정

다음은 일괄 처리 유추 파이프라인을 실행하는 데 필요한 기계 학습 리소스를 설정하는 작업입니다.

- 작업 영역에 연결합니다.
- 컴퓨팅 리소스를 새로 만들거나 기존 컴퓨팅 리소스를 연결합니다.

### <a name="configure-workspace"></a>작업 영역 구성

기존 작업 영역에서 작업 영역 개체를 만듭니다. `Workspace.from_config()`는 config.json 파일을 읽고 ws라는 개체에 세부 정보를 로드합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모 디렉터리에 저장될 것으로 예상합니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 관리](how-to-manage-workspace.md)를 참조하세요. 파일에 구성 저장에 대한 자세한 내용은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace)를 참조 하세요.

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

Azure Machine Learning에서 *컴퓨팅*(또는 *컴퓨팅* 대상)은 기계 학습 파이프라인에서 계산 단계를 수행하는 머신 또는 클러스터를 가리킵니다. 다음 코드를 실행하여 [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 대상을 기반으로 CPU를 만듭니다.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>입력 및 출력 구성

### <a name="create-a-datastore-with-sample-images"></a>샘플 이미지로 데이터 저장소 만들기

`pipelinedata` 계정의 공용 Blob 컨테이너 `sampledata`에서 MNIST 평가 세트를 가져옵니다. 이 컨테이너를 가리키는 이름인 `mnist_datastore`를 사용하여 데이터 저장소를 만듭니다. 이어지는 `register_azure_blob_container` 호출에서, `overwrite` 플래그를 `True`로 설정하면 이전에 만든 데이터 저장소를 해당 이름으로 덮어씁니다. 

`datastore_name`, `container_name` 및 `account_name`에 대한 고유한 값을 입력하여 Blob 컨테이너를 가리키도록 이 단계를 변경할 수 있습니다.

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

다음으로, 작업 영역 기본 데이터 저장소를 출력 데이터 저장소로 설정합니다. 유추 출력에 사용할 것입니다.

작업 영역을 만들 때 [Azure File](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  및 [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 는 기본적으로 작업 영역에 연결됩니다. Azure Files는 작업 영역의 기본 데이터 저장소이지만, Blob Storage를 데이터 저장소로 사용할 수도 있습니다. 자세한 내용은 [Azure 스토리지 옵션](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)을 참조하세요.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>데이터 입력 만들기

일괄 처리 유추의 입력은 병렬 처리를 위해 분할하려는 데이터입니다. 일괄 처리 유추 파이프라인은 [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)를 통해 데이터 입력을 수락합니다.

`Dataset`는 Azure Machine Learning에서 데이터를 탐색, 변환 및 관리하는 데 사용됩니다. 형식은 [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 및 [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) 두 가지입니다. 이 예제에서는 `FileDataset`를 입력으로 사용합니다. `FileDataset`는 컴퓨터에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. 데이터 세트를 만들어 데이터 원본 위치에 대한 참조를 만듭니다. 데이터 세트에 하위 집합 변환을 적용한 경우 하위 집합 변환 역시 데이터 세트에 저장됩니다. 데이터는 기존 위치에 그대로 남아 있으므로 추가 스토리지 비용이 발생하지 않습니다.

Azure Machine Learning 데이터 세트에 대한 자세한 내용은 [데이터 세트 만들기 및 액세스(미리 보기)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)를 참조하세요.

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

일괄 처리 유추 파이프라인을 실행할 때 동적 데이터 입력을 사용하려면 입력 `Dataset`를 [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)로 정의하면 됩니다. 일괄 처리 유추 파이프라인 실행을 다시 제출할 때마다 입력 데이터 세트를 지정할 수 있습니다.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>출력 만들기

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 개체는 파이프라인 단계 간에 중간 데이터를 전송하는 데 사용됩니다. 이 예제에서는 유추 출력에 사용합니다.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>모델 준비

[미리 학습된 이미지 분류 모델을 다운로드](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)한 다음, `models` 디렉터리로 추출합니다.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

그런 다음, 컴퓨팅 리소스에 사용할 수 있도록 모델을 작업 영역에 등록합니다.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>유추 스크립트 작성

>[!Warning]
>다음 코드는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)에서 사용하는 샘플일 뿐입니다. 각자 시나리오에 맞는 고유의 스크립트를 만들어야 합니다.

스크립트에 다음 두 함수가 *포함되어야 합니다*.
- `init()`: 이후 유추를 위해 비용이 많이 드는 준비 또는 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각 `mini_batch` 인스턴스에 대해 실행됩니다.
    -  `mini_batch`: `ParallelRunStep`은(는) run 메서드를 호출하고 목록 또는 pandas `DataFrame`을(를) 메서드에 인수로 전달합니다. 입력이 `FileDataset`이면 mini_batch의 각 항목이 파일 경로가 되고 입력이 `TabularDataset`이면 Pandas `DataFrame`입니다.
    -  `response`: run() 메서드는 Pandas `DataFrame` 또는 배열을 반환해야 합니다. append_row output_action의 경우 반환되는 요소가 공통 출력 파일에 추가됩니다. summary_only의 경우 요소의 내용이 무시됩니다. 모든 출력 작업의 경우 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 입력을 실행 출력 결과에 매핑하기에 충분한 데이터가 실행 결과에 포함되어 있는지 확인합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 입력에 매핑해야 합니다.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

다른 파일 또는 폴더가 유추 스크립트와 동일한 디렉터리에 있는 경우 현재 작업 디렉터리를 찾아 참조할 수 있습니다.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>ParallelRunStep을 포함하는 파이프라인 빌드 및 실행

이제 필요한 데이터 입력, 모델, 출력 및 유추 스크립트가 모두 준비되었습니다. 이제 ParallelRunStep을 포함하는 일괄 처리 유추 파이프라인을 빌드하겠습니다.

### <a name="prepare-the-environment"></a>환경 준비

먼저 스크립트의 종속성을 지정합니다. 이렇게 하면 pip 패키지를 설치하고 환경을 구성할 수 있습니다.

pip 패키지 목록에 항상 **azureml-core** 및 **azureml-dataset-runtime[pandas, fuse]** 를 포함합니다. 사용자 지정 docker 이미지(user_managed_dependencies=True)를 사용하는 경우에도 conda가 설치되어 있어야 합니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>ParallelRunConfig를 사용하여 매개 변수 지정

`ParallelRunConfig`는 Azure Machine Learning 파이프라인 내에서 `ParallelRunStep` 인스턴스에 대한 주요 구성입니다. 스크립트를 래핑하고 다음 항목을 포함하여 필요한 매개 변수를 구성하는 데 사용됩니다.
- `entry_script`: 여러 노드에서 병렬로 실행되는 로컬 파일 경로인 사용자 스크립트입니다. `source_directory`가 있으면 상대 경로를 사용합니다. 없으면 머신에서 액세스할 수 있는 아무 경로를 사용합니다.
- `mini_batch_size`: 단일 `run()` 호출에 전달된 미니 일괄 처리의 크기입니다. (선택 사항이며 기본값은 `FileDataset`의 경우 `10` 파일이고 `TabularDataset`의 경우 `1MB`임)
    - `FileDataset`의 경우 최솟값이 `1`인 파일 수입니다. 여러 파일을 한 미니 일괄 처리로 결합할 수 있습니다.
    - `TabularDataset`의 경우 데이터의 크기입니다. 예제 값은 `1024`, `1024KB`, `10MB` 및 `1GB`입니다. 권장 값은 `1MB`입니다. `TabularDataset`의 미니 일괄 처리는 파일 경계를 넘지 않습니다. 예를 들어 다양한 크기의 .csv 파일이 있는 경우 가장 작은 파일은 100KB이고 가장 큰 파일은 10MB입니다. `mini_batch_size = 1MB`를 설정하는 경우 크기가 1MB보다 작은 파일은 하나의 미니 일괄 처리로 취급됩니다. 크기가 1MB보다 큰 파일은 여러 개의 미니 일괄 처리로 분할됩니다.
- `error_threshold`: 처리 중에 무시해야 하는 `FileDataset`에 대한 `TabularDataset` 및 파일 오류에 대한 레코드 실패 횟수입니다. 전체 입력의 오류 횟수가 이 값을 초과하면 작업이 중단됩니다. 오류 임계값은 `run()` 메서드로 전송되는 개별 미니 일괄 처리가 아닌 전체 입력에 적용됩니다. 범위는 `[-1, int.max]`입니다. `-1` 부분은 처리 중에 발생하는 모든 오류를 무시한다는 뜻입니다.
- `output_action`: 다음 값 중 하나는 출력을 구성하는 방법을 나타냅니다.
    - `summary_only`: 사용자 스크립트는 출력을 저장합니다. `ParallelRunStep`은 출력을 오류 임계값 계산에만 사용합니다.
    - `append_row`: 모든 입력에서, 출력 폴더에 줄로 구분된 모든 출력을 추가하는 하나의 파일만 생성됩니다.
- `append_row_file_name`: append_row output_action의 출력 파일 이름을 사용자 지정하는 방법은 다음과 같습니다(선택 사항이며 기본값은 `parallel_run_step.txt`).
- `source_directory`: 컴퓨팅 대상에서 실행할 모든 파일이 포함된 폴더 경로입니다(선택 사항).
- `compute_target`: `AmlCompute`만 지원됩니다.
- `node_count`: 사용자 스크립트를 실행하는 데 사용되는 컴퓨팅 노드의 수입니다.
- `process_count_per_node`: 노드당 프로세스 수입니다. 모범 사례는 한 노드에 있는 GPU 또는 CPU 수로 설정하는 것입니다(선택 사항이며 기본값은 `1`).
- `environment`: Python 환경 정의입니다. 기존 Python 환경을 사용하도록 구성할 수도 있고 임시 환경을 설정하도록 구성할 수도 있습니다. 이 정의는 필요한 애플리케이션 종속성을 설정하는 역할도 담당합니다(선택 사항).
- `logging_level`: 로그의 자세한 정도입니다. 값의 자세한 정도는 `WARNING`, `INFO`, `DEBUG` 순서로 높아집니다. (선택 사항이며 기본값은 `INFO`)
- `run_invocation_timeout`: `run()` 메서드 호출 시간 제한(초)입니다. (선택 사항이며 기본값은 `60`)
- `run_max_try`: 미니 일괄 처리의 최대 `run()` 시도 횟수입니다. 예외가 throw되거나 `run_invocation_timeout`에 도달할 때 아무 것도 반환되지 않으면 `run()`이 실패합니다(선택 사항이며 기본값은 `3`). 

`mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` 및 `run_max_try`를 `PipelineParameter`로 지정할 수 있습니다. 그러면 파이프라인 실행을 다시 제출할 때 매개 변수 값을 세밀하게 조정할 수 있습니다. 이 예제에서는 `mini_batch_size` 및 `Process_count_per_node`에 `PipelineParameter`을(를) 사용하며 나중에 실행을 다시 제출할 때 이 값을 변경할 것입니다. 

이 예에서는 앞에서 설명한 `digit_identification.py` 스크립트를 사용한다고 가정합니다. 고유한 스크립트를 사용하는 경우 그에 따라 `source_directory` 및 `entry_script` 매개 변수를 변경합니다.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>ParallelRunStep 만들기

스크립트, 환경 구성 및 매개 변수를 사용하여 ParallelRunStep을 만듭니다. 유추 스크립트의 실행 대상으로 작업 영역에 이미 연결한 컴퓨팅 대상을 지정합니다. `ParallelRunStep`을 사용하여 다음 매개 변수를 모두 사용하는 일괄 처리 유추 파이프라인 단계를 만듭니다.
- `name`: 단계의 이름이며, 고유한 3-32자 문자와 regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$로 구성되어야 한다는 명명 제한이 있습니다.
- `parallel_run_config`: 이전에 정의된 `ParallelRunConfig` 개체입니다.
- `inputs`: 병렬 처리를 위해 분할하려는 하나 이상의 단일 형식 Azure Machine Learning 데이터 세트입니다.
- `side_inputs`: 분할할 필요 없이 측면 입력으로 사용되는 하나 이상의 참조 데이터 또는 데이터 세트입니다.
- `output`: 출력 디렉터리에 해당하는 `PipelineData` 개체입니다.
- `arguments`: 사용자 스크립트에 전달된 인수 목록입니다. 항목 스크립트에서 인수를 검색하려면 unknown_args를 사용합니다(선택 사항).
- `allow_reuse`: 동일한 설정/입력으로 실행할 때 이전 결과를 단계에 다시 사용할 것인지 여부를 지정합니다. 이 매개 변수가 `False`이면 파이프라인 실행 중에 이 단계에 대해 항상 새로운 실행이 생성됩니다. (선택 사항이며 기본값은 `True`)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>파이프라인 만들기 및 실행

이제 파이프라인을 실행합니다. 먼저, 이전에 만든 작업 영역 참조와 파이프라인 단계를 사용하여 [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 개체를 만듭니다. `steps` 매개 변수는 단계의 배열입니다. 여기서는 일괄 처리 유추에 대한 단계가 하나만 있습니다. 여러 단계가 있는 파이프라인을 빌드하려면 이 배열에서 단계를 순서대로 배치합니다.

다음으로, `Experiment.submit()` 함수를 사용하여 실행할 파이프라인을 제출합니다.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>일괄 처리 유추 작업 모니터링

일괄 처리 유추 작업을 완료하는 데 시간이 오래 걸릴 수 있습니다. 이 예제에서는 Jupyter 위젯을 사용하여 진행률을 모니터링합니다. 다음을 사용하여 작업의 진행률을 모니터링할 수도 있습니다.

* Azure Machine Learning Studio 
* [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) 개체의 콘솔 출력

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>새 데이터 입력 및 매개 변수를 사용하여 실행을 다시 전송

`PipelineParameter`로 입력과 여러 구성을 만들었으므로, 파이프라인을 완전히 새로 만들지 않고도 다른 데이터 세트 입력을 사용하여 일괄 처리 유추 실행을 다시 제출하고 매개 변수를 미세 조정할 수 있습니다. 동일한 데이터 저장소를 사용하지만 데이터 입력으로 단일 이미지만 사용합니다.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>결과 보기

위의 실행 결과는 `PipelineData` 개체에 지정된 `DataStore`에 출력 데이터로 기록됩니다. 이 경우 *추론*이라고 합니다. 결과는 기본 Bob 컨테이너에 저장되며, 스토리지 계정으로 이동하여 Storage Explorer를 통해 볼 수 있습니다. 파일 경로는 azureml-blobstore-*GUID*/azureml/*RunId*/*output_dir*입니다.

이 데이터를 다운로드하여 결과를 볼 수도 있습니다. 아래는 처음 10개 행을 표시하는 샘플 코드입니다.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>다음 단계

이 프로세스가 작동하는 모습을 처음부터 끝까지 보려면 [일괄 처리 유추 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)을 사용해 보세요. 

ParallelRunStep에 대한 디버깅 및 문제 해결 지침은 [이 방법 가이드](how-to-debug-parallel-run-step.md)를 참조하세요.

파이프라인의 디버깅 및 문제 해결 지침은 [방법 가이드](how-to-debug-pipelines.md)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

