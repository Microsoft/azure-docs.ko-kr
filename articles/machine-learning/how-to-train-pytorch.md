---
title: 심층 학습 PyTorch 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 PyTorch 평가기 클래스를 사용 하 여 엔터프라이즈 규모에서 PyTorch 학습 스크립트를 실행 하는 방법을 알아봅니다.  예제 스크립트는 닭 및 터키 이미지를 분류 하 여 PyTorch의 전송 학습 자습서에 따라 심층 학습 신경망을 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 28a2d5c34da9f7996524d29a88c7969adf197b38
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270651"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 대규모로 Pytorch 심층 학습 모델 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning의 [PyTorch 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 클래스를 사용 하 여 엔터프라이즈 규모에서 [PyTorch](https://pytorch.org/) 학습 스크립트를 실행 하는 방법에 대해 알아봅니다.  

이 문서의 예제 스크립트는 PyTorch의 전송 학습 [자습서](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)에 따라 딥 러닝 신경망을 빌드하기 위해 닭 및 터키 이미지를 분류 하는 데 사용 됩니다. 

처음부터 심층 학습 PyTorch 모델을 학습 하 고 있거나 기존 모델을 클라우드로 가져오는 경우에는 Azure Machine Learning를 사용 하 여 탄력적 클라우드 계산 리소스를 사용 하 여 오픈 소스 학습 작업을 확장할 수 있습니다. Azure Machine Learning를 사용 하 여 프로덕션 등급 모델을 빌드, 배포, 버전 및 모니터링할 수 있습니다. 

[심층 학습 vs machine learning](concept-deep-learning-vs-machine-learning.md)에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

이러한 환경 중 하나에서이 코드를 실행 합니다.

- Azure Machine Learning 컴퓨팅 인스턴스 - 다운로드 또는 설치 필요 없음

    - 이 자습서를 시작하기 전에 [자습서: SDK 및 샘플 리포지토리로 미리 로드된 전용 Notebook 서버를 만들기 위한 환경 및 작업 영역](tutorial-1st-experiment-sdk-setup.md)을 설정합니다.
    - 노트북 서버의 샘플 심층 학습 폴더에서 다음 디렉터리로 이동 하 여 완료 되 고 확장 된 노트북을 찾습니다. **사용 방법-azureml > 교육-심층 학습 > 학습-hyperparameter 변수-pytorch** 폴더. 
 
 - 사용자 고유의 Jupyter Notebook 서버

    - [AZURE MACHINE LEARNING SDK를 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)합니다.
    - [작업 영역 구성 파일을 만듭니다](how-to-configure-environment.md#workspace).
    - [샘플 스크립트 파일 다운로드](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    GitHub 샘플 페이지에서이 가이드의 전체 [Jupyter Notebook 버전](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) 을 찾을 수도 있습니다. 노트북에는 지능형 하이퍼 매개 변수 튜닝, 모델 배포 및 노트북 위젯을 다루는 확장 된 섹션이 포함 되어 있습니다.

## <a name="set-up-the-experiment"></a>실험 설정

이 섹션에서는 필요한 python 패키지를 로드 하 고, 작업 영역을 초기화 하 고, 실험을 만들고, 학습 데이터 및 학습 스크립트를 업로드 하 여 학습 실험을 설정 합니다.

### <a name="import-packages"></a>패키지 가져오기

먼저 필요한 Python 라이브러리를 가져옵니다.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

`config.json` [전제 조건 섹션](#prerequisites)에서 만든 파일에서 작업 영역 개체를 만듭니다.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>심층 학습 실험 만들기

학습 스크립트를 보관할 실험 및 폴더를 만듭니다. 이 예에서는 "pytorch" 라는 실험을 만듭니다.

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>데이터 가져오기

데이터 집합은 각 클래스에 대해 100 유효성 검사 이미지를 사용 하 여 chickens 키와 각각에 대 한 약 120 학습 이미지로 구성 됩니다. 데이터 집합은 학습 스크립트의 일부로 다운로드 하 고 압축을 풉니다 `pytorch_train.py` . 이미지는 [개방형 이미지 V5 데이터 집합](https://storage.googleapis.com/openimages/web/index.html)의 하위 집합입니다.

### <a name="prepare-training-scripts"></a>학습 스크립트 준비

이 자습서에서는 학습 스크립트 인 `pytorch_train.py` 가 이미 제공 되어 있습니다. 실제로는 사용자 지정 학습 스크립트를 그대로 사용 하 고 Azure Machine Learning를 사용 하 여 실행할 수 있습니다.

Pytorch 교육 스크립트를 업로드 `pytorch_train.py` 합니다.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

그러나 Azure Machine Learning 추적 및 메트릭 기능을 사용 하려는 경우 학습 스크립트 내에 작은 양의 코드를 추가 해야 합니다. 메트릭 추적의 예는에서 찾을 수 있습니다 `pytorch_train.py` .

## <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기

PyTorch 작업을 실행할 계산 대상을 만듭니다. 이 예제에서는 GPU 사용 Azure Machine Learning 계산 클러스터를 만듭니다.

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

계산 대상에 대 한 자세한 내용은 [계산 대상 이란?](concept-compute-target.md) 문서를 참조 하세요.

## <a name="create-a-pytorch-estimator"></a>PyTorch 평가기 만들기

[PyTorch 평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) 는 계산 대상에서 PyTorch 학습 작업을 시작 하는 간단한 방법을 제공 합니다.

PyTorch 평가기는 [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 프레임 워크를 지 원하는 데 사용할 수 있는 제네릭 클래스를 통해 구현 됩니다. 일반 예측 도구를 사용하는 학습 모델에 대한 자세한 내용은 [예측 도구를 사용하여 Azure Machine Learning에서 모델 학습](how-to-train-ml-models.md)을 참조하세요.

학습 스크립트에 추가 pip 또는 conda 패키지를 실행 해야 하는 경우 및 인수를 통해 해당 이름을 전달 하 여 결과 docker 이미지에 패키지를 설치할 수 있습니다 `pip_packages` `conda_packages` .

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신 데이터 [저장소](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)를 사용 하 여 데이터에 액세스 합니다.

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요.

## <a name="submit-a-run"></a>실행 제출

[실행 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 는 작업이 실행 되는 동안 그리고 작업이 완료 된 후 실행 기록에 인터페이스를 제공 합니다.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

실행이 실행 되 면 다음 단계를 거칩니다.

- **준비**: PyTorch 평가기에 따라 docker 이미지가 생성 됩니다. 이미지는 작업 영역 컨테이너 레지스트리로 업로드 되 고 나중에 실행할 수 있도록 캐시 됩니다. 로그는 실행 기록에도 스트리밍되 고 진행률을 모니터링 하기 위해 볼 수 있습니다.

- **크기 조정**: 클러스터는 현재 사용 가능한 것 보다 더 많은 노드를 실행 하는 Batch AI 클러스터가 필요한 경우 확장을 시도 합니다.

- **실행 중**: 스크립트 폴더의 모든 스크립트가 계산 대상으로 업로드 되 고, 데이터 저장소가 탑재 되거나 복사 되 고, entry_script 실행 됩니다. Stdout의 출력과./clogs 폴더는 실행 기록으로 스트리밍되 며 실행을 모니터링 하는 데 사용할 수 있습니다.

- **사후 처리**: 실행의./출력 폴더가 실행 기록에 복사 됩니다.

## <a name="register-or-download-a-model"></a>모델 등록 또는 다운로드

모델을 학습 한 후에는 작업 영역에 등록할 수 있습니다. 모델 등록을 사용하면 모델을 작업 영역에 저장하고 버전을 지정하여 [모델 관리 및 배포](concept-model-management-and-deployment.md)를 간소화할 수 있습니다.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> 방금 등록 한 모델은 학습에 사용한 평가기에 관계 없이 Azure Machine Learning에서 등록 된 다른 모델과 정확히 동일한 방식으로 배포 됩니다. 배포 방법에는 모델 등록에 대 한 섹션이 포함 되어 있지만 등록 된 모델이 이미 있기 때문에 배포에 대 한 [계산 대상을 직접 만드는](how-to-deploy-and-where.md#choose-a-compute-target) 것으로 건너뛸 수 있습니다.

Run 개체를 사용 하 여 모델의 로컬 복사본을 다운로드할 수도 있습니다. 학습 스크립트에서 `pytorch_train.py` PyTorch save 개체는 모델을 로컬 폴더 (계산 대상의 로컬 폴더)에 유지 합니다. Run 개체를 사용 하 여 복사본을 다운로드할 수 있습니다.

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

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)또한 평가기는 CPU 및 GPU 클러스터에서 분산 된 학습을 지원 합니다. Distributed PyTorch 작업을 쉽게 실행할 수 있으며, Azure Machine Learning는 오케스트레이션을 관리할 수 있습니다.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) 는 uber에서 개발한 분산 교육을 위한 오픈 소스를 모두 줄인 프레임 워크입니다. Distributed GPU PyTorch 작업에 대 한 쉬운 경로를 제공 합니다.

Horovod를 사용 하려면 [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) `distributed_training` PyTorch 생성자에서 매개 변수에 대 한 개체를 지정 합니다. 이 매개 변수는 학습 스크립트에서 사용할 Horovod 라이브러리가 설치 되어 있는지 확인 합니다.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.4',
                      use_gpu=True)
```
Horovod 및 해당 종속성이 설치 되므로 학습 스크립트에서 다음과 같이 가져올 수 있습니다 `train.py` .

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>ONNX로 내보내기

[Onnx 런타임으로](concept-onnx.md)유추를 최적화 하려면 학습 된 PyTorch 모델을 onnx 형식으로 변환 합니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다. 예제는 [자습서](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 PyTorch Azure Machine Learning에서를 사용 하 여 딥 러닝 신경망을 학습 하 고 등록 했습니다. 모델을 배포 하는 방법에 대 한 자세한 내용은 모델 배포 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
* [Azure의 분산 심층 학습 교육에 대 한 참조 아키텍처](/azure/architecture/reference-architectures/ai/training-deep-learning)

