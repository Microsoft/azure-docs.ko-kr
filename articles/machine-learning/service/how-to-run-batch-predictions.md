---
title: 대용량 데이터에서 일괄 예측 실행
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service를 사용하여 대용량 데이터에서 비동기적으로 일괄 예측을 수행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e403ac0d2fbe9572a44fb3cde9d25e4df9b3db4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818513"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Azure Machine Learning Service를 사용하여 대용량 데이터 세트에서 일괄 예측을 실행합니다.

이 문서에서는 Azure Machine Learning 서비스를 사용하여 대용량 데이터를 비동기적으로 예측하는 방법을 알아봅니다.

일괄 예측(또는 일괄 채점)은 비동기 애플리케이션에 대한 최상의 처리량으로 비용 효율적인 유추를 제공합니다. 일괄 예측 파이프라인은 프로덕션 데이터의 테라바이트에 대한 유추를 수행하도록 확장할 수 있습니다. 일괄 예측은 데이터의 대규모 컬렉션에 대한 높은 처리량, fire-and-forget(실행 후 망각) 예측에 맞게 최적화되었습니다.

>[!TIP]
> 시스템에서 짧은 대기 시간 처리(단일 문서 또는 소규모 문서를 빨리 처리)를 요구하는 경우 일괄 예측 대신 [실시간 채점](how-to-consume-web-service.md)을 사용합니다.

다음 단계에서는 [기계 학습 파이프라인](concept-ml-pipelines.md)을 만들어 미리 학습된 컴퓨터 비전 모델([Inception-V3](https://arxiv.org/abs/1512.00567))을 등록합니다. 그런 다음, 미리 학습된 모델을 사용하여 Azure Blob 스토리지 계정에서 사용 가능한 이미지에 일괄 채점을 합니다. 채점에 사용되는 이 이미지는 [ImageNet](http://image-net.org/) 데이터 세트에서 레이블되지 않은 이미지입니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 서비스의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- 개발 환경을 구성하여 Azure Machine Learning SDK를 설치합니다. 자세한 내용은 [Azure Machine Learning에 대한 개발 환경 구성](how-to-configure-environment.md)을 참조하세요.

- 모든 파이프라인 리소스를 수용하는 Azure Machine Learning 작업 영역을 만듭니다. 다음 코드를 사용하거나 더 자세한 옵션은 [작업 영역 구성 파일을 만들기](how-to-configure-environment.md#workspace)를 참조하세요.

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>기계 학습 리소스 설정

다음 단계는 파이프라인을 실행하는 데 필요한 리소스를 설정합니다.

- 이미 미리 학습된 모델, 입력 레이블 및 이미지가 있는 데이터 저장소에 액세스하여 채점합니다(이미 설정되어 있음).
- 데이터 저장소를 설정하여 출력을 저장합니다
-  `DataReference`  개체를 구성하여 이전 데이터 저장소에서 이 데이터를 가리킵니다.
- 파이프라인 단계를 실행할 컴퓨팅 머신 또는 클러스터를 설정합니다.

### <a name="access-the-datastores"></a>데이터 저장소에 액세스

먼저 모델, 레이블 및 이미지가 있는 데이터 저장소에 액세스합니다.

ImageNet 평가 집합의 이미지를 보유하고 있는 *pipelinedata* 계정에서 *sampledata*라는 공용 Blob 컨테이너를 사용합니다. 이 공용 컨테이너에 대한 데이터 저장소 이름은 *images_datastore*입니다. 작업 영역을 사용하여 이 데이터 저장소를 등록 합니다.

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

다음으로, 출력에 대한 기본 데이터 저장소를 사용하도록 설정합니다.

작업 영역을 만들 때 [Azure File](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)  및 [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 는 기본적으로 작업 영역에 연결됩니다. Azure Files는 작업 영역의 기본 데이터 저장소이지만, Blob Storage를 데이터 저장소로 사용할 수도 있습니다. 자세한 내용은 [Azure 스토리지 옵션](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)을 참조하세요.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>데이터 참조 구성

이제 파이프라인 단계에 대해 파이프라인의 데이터를 참조합니다.

파이프라인에서 데이터 원본은 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 개체로 표시됩니다.  `DataReference`  개체는 데이터 저장소에 상주하고 있거나 데이터 저장소에서 액세스할 수 있는 데이터를 가리킵니다. 입력 이미지에 사용되는 디렉터리, 미리 학습된 모델이 저장되는 디렉터리, 레이블 디렉터리 및 출력 디렉터리에 대한 `DataReference`  개체가 필요합니다.

```python
input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")
                           
model_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")                          
                         
label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")                          
                         
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>컴퓨팅 대상 설정

Azure Machine Learning에서 *컴퓨팅*(또는 *컴퓨팅* 대상)은 기계 학습 파이프라인에서 계산 단계를 수행하는 머신 또는 클러스터를 가리킵니다. 예를 들어 `Azure Machine Learning compute`을 만들 수 있습니다.

```python
compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, 
                        compute_name, 
                        provisioning_config)
    
    compute_target.wait_for_completion(
                     show_output=True, 
                     min_node_count=None, 
                     timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>모델 준비

미리 학습된 모델을 사용하려면 모델을 다운로드하여 작업 영역에 등록해야 합니다.

### <a name="download-the-pretrained-model"></a>미리 학습된 모델 다운로드

<http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>에서 미리 학습된 Computer Vision 모델(InceptionV3)을 다운로드합니다. 그런 다음, `models` 하위 폴더에 추출합니다.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>모델 등록

모델을 등록하는 방법은 다음과 같습니다.

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>채점 스크립트 작성

>[!Warning]
>다음 코드는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb)에서 사용되는 [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py)에 포함된 샘플일 뿐입니다. 시나리오에 대한 고유의 채점 스크립트를 만들어야 합니다.

`batch_score.py` 스크립트를 실행하면  *dataset_path*의 입력 이미지,  *model_dir의 미리 학습된 모델*을 가져와서 *results-label.txt* 을  *output_dir*로 출력합니다.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here
  
def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
            
        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>일괄 채점 파이프라인 빌드 및 실행

이제 파이프라인을 빌드하는 데 필요한 모든 준비를 완료했습니다.

### <a name="prepare-the-run-environment"></a>실행 환경 준비

스크립트에 대한 Conda 종속성을 지정합니다. 나중에 파이프라인 단계를 만들 때 이 개체가 필요합니다.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>파이프라인에 대한 매개 변수 지정

기본값으로  [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)  개체를 사용하여 파이프라인 매개 변수를 만듭니다.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>파이프라인 단계 만들기

스크립트, 환경 구성 및 매개 변수를 사용하여 파이프라인 단계를 만듭니다. 스크립트 실행의 대상으로 작업 영역에 이미 연결된 Compute 대상을 지정합니다. [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)을 사용하여 파이프라인 단계를 만듭니다.

```python
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>파이프라인 실행

이제 파이프라인을 실행하고 생성된 출력을 검사합니다. 출력에는 각 입력 이미지에 해당하는 점수가 포함됩니다.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>파이프라인 게시

실행 결과에 만족하면 나중에 다른 입력 값으로 실행할 수 있도록 파이프라인을 게시합니다. 파이프라인을 게시할 때 REST 엔드포인트를 가져옵니다. 이 엔드포인트는 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)를 사용하여 이미 통합된 매개 변수 집합과 함께 파이프라인의 호출을 허용합니다.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>REST 엔드포인트를 사용하여 파이프라인 다시 실행

파이프라인을 다시 실행하려면 [AzureCliAuthentication 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)에서 설명한 대로 Azure Active Directory 인증 헤더 토큰이 필요합니다.

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>다음 단계

이 작업을 전체적으로 보려면 [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)에서 일괄 채점 notebook을 사용해 보세요. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

