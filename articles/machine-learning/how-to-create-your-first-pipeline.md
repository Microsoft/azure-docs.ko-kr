---
title: ML 파이프라인 만들기 및 실행
titleSuffix: Azure Machine Learning
description: Python용 Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인을 만들고 실행합니다. 기계 학습 (ML) 단계를 함께 사용 하는 워크플로를 만들고 관리 하려면 ML 파이프라인을 사용 합니다. 이러한 단계에는 데이터 준비, 모델 학습, 모델 배포 및 유추/점수 매기기가 포함 됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 8/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 0d917ea686c2003bf46b6fca872ef67fb0363107
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228919"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용 하 여 [기계 학습 파이프라인](concept-ml-pipelines.md) 을 만들고 실행 하는 방법에 대해 알아봅니다. **Ml 파이프라인** 을 사용 하 여 다양 한 ml 단계를 함께 연결 하는 워크플로를 만듭니다. 그런 다음 나중에 액세스 하거나 다른 사용자와 공유 하기 위해 해당 파이프라인을 게시 합니다. ML 파이프라인을 추적 하 여 모델을 실제 세계에서 어떻게 수행 하 고 있는지 확인 하 고 데이터 드리프트를 검색 합니다. ML 파이프라인은 다양 한 계산을 사용 하 여 일괄 처리를 다시 실행 하는 대신 단계를 다시 사용 하 고 ML 워크플로를 다른 사람들과 공유 하는 일괄 처리 점수 매기기 시나리오에 적합 합니다.

ML 작업의 CI/CD 자동화를 위해 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) 이라는 다른 종류의 파이프라인을 사용할 수 있지만 해당 유형의 파이프라인은 작업 영역에 저장 되지 않습니다. [이러한 서로 다른 파이프라인을 비교](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)합니다.

만든 ML 파이프라인은 Azure Machine Learning [작업 영역의](how-to-manage-workspace.md)구성원에 게 표시 됩니다. 

ML 파이프라인은 계산 대상에서 실행 됩니다 ( [Azure Machine Learning에서 계산 대상이 무엇 인지](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)참조). 파이프라인은 지원되는 [Azure Storage](https://docs.microsoft.com/azure/storage/) 위치에서 데이터를 읽고 쓸 수 있습니다.

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

## <a name="prerequisites"></a>필수 구성 요소

* 모든 파이프라인 리소스를 수용하는 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.

* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 를 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

작업 영역을 연결 하 여 시작 합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>기계 학습 리소스 설정

ML 파이프라인을 실행 하는 데 필요한 리소스를 만듭니다.

* 파이프라인 단계에서 필요한 데이터에 액세스하는 데 사용되는 데이터 저장소를 설정합니다.

* `Dataset`데이터 저장소에 상주 하거나 데이터 저장소에 액세스할 수 있는 영구 데이터를 가리키도록 개체를 구성 합니다. `PipelineData`파이프라인 단계 간에 전달 되는 임시 데이터에 대 한 개체를 구성 합니다. 

    > [!TIP]
    > 파이프라인 단계 간에 임시 데이터를 전달 하기 위한 향상 된 환경은 공개 미리 보기 클래스인에서 사용할 수 있습니다  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) .  이 클래스는 [실험적](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#stable-vs-experimental) 미리 보기 기능으로, 언제 든 지 변경 될 수 있습니다.

* 파이프라인 단계가 실행될 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md#compute-targets)을 설정합니다.

### <a name="set-up-a-datastore"></a>데이터 저장소 설정

데이터 저장소는 파이프라인에서 액세스할 데이터를 저장합니다. 각 작업 영역마다 기본 데이터 저장소가 있습니다. 추가 데이터 저장소를 등록할 수 있습니다. 

작업 영역을 만들 때 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 및 [Azure Blob 저장소가](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 작업 영역에 연결 됩니다. 기본 데이터 저장소는 Azure Blob storage에 연결 하기 위해 등록 됩니다. 자세한 내용은 [Azure Files, Azure Blob 또는 Azure Disk를 사용할지 여부 결정](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)을 참조하세요. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

단계에서는 일반적으로 데이터를 사용 하 고 출력 데이터를 생성 합니다. 단계는 모델, 모델과 종속 파일이 있는 디렉터리 또는 임시 데이터와 같은 데이터를 만들 수 있습니다. 그런 다음, 파이프라인의 다른 후속 단계에서 이 데이터를 사용할 수 있습니다. 파이프라인을 데이터에 연결 하는 방법에 대 한 자세한 내용은 [데이터에 액세스 하는 방법](how-to-access-data.md) 및 데이터 [집합을 등록](how-to-create-register-datasets.md)하는 방법 문서를 참조 하세요. 

### <a name="configure-data-with-dataset-and-pipelinedata-objects"></a>`Dataset`및 개체를 사용 하 여 데이터 구성 `PipelineData`

파이프라인에 데이터를 제공 하는 기본 방법은 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) 개체입니다. `Dataset`개체는 데이터 저장소 또는 웹 URL에서 액세스할 수 있거나 액세스할 수 있는 데이터를 가리킵니다. `Dataset`클래스는 추상 클래스 이므로 하나 이상의 파일 `FileDataset` 을 참조 하는 (하나 이상의 파일 참조) 또는 `TabularDataset` 구분 기호로 분리 된 열이 있는 하나 이상의 파일에서 만든의 인스턴스를 만듭니다.


`Dataset` [From_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 또는 [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)와 같은 메서드를 사용 하 여를 만듭니다.

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```
중간 데이터(또는 단계의 출력)는 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 개체로 표시됩니다. `output_data1`은 단계의 출력으로 생성되며 하나 이상 후속 단계의 입력으로 사용됩니다. `PipelineData`는 단계 간에 데이터 종속성을 도입하고 파이프라인에 암시적 실행 순서를 만듭니다. 이 개체는 나중에 파이프라인 단계를 만들 때 사용 됩니다.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")

```

> [!TIP]
> 공개 미리 보기 클래스인를 사용 하 여 파이프라인 단계 사이에 중간 데이터를 유지할 수도 `OutputFileDatasetConfig` 있습니다. `OutputFileDatasetConfig` [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py)의 디자인 패턴 및 메서드에 대해 자세히 알아보세요.

## <a name="set-up-a-compute-target"></a>컴퓨팅 대상 설정


Azure Machine Learning에서 ‘__컴퓨팅__’(또는 ‘__컴퓨팅 대상__’) 용어는 기계 학습 파이프라인에서 계산 단계를 수행하는 머신 또는 클러스터를 가리킵니다.   계산 대상의 전체 목록은 [모델 학습을 위한 계산 대상](concept-compute-target.md#train) 을 참조 하 고 작업 영역에 연결 하는 방법에 대 한 [계산 대상을 만듭니다](how-to-create-attach-compute-sdk.md) .   계산 대상을 만들거나 연결 하는 프로세스는 모델을 학습 하거나 파이프라인 단계를 실행 하는 경우와 동일 합니다. 컴퓨팅 대상을 만들고 연결한 후 [파이프라인 단계](#steps)에서 `ComputeTarget` 개체를 사용합니다.

> [!IMPORTANT]
> 컴퓨팅 대상에 대한 관리 작업 수행은 원격 작업 내에서 지원되지 않습니다. 기계 학습 파이프라인은 원격 작업으로 제출되므로 파이프라인 내부에서 컴퓨팅 대상에 관리 작업을 사용하지 마십시오.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅

단계를 실행하기 위한 Azure Machine Learning 컴퓨팅을 만들 수 있습니다. 다른 계산 대상에 대 한 코드는 형식에 따라 약간 다른 매개 변수를 사용 하는 매우 유사 합니다. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>학습 실행 환경 구성

다음 단계는 원격 학습 실행에 학습 단계에 필요한 모든 종속성이 있는지 확인 하는 것입니다. 개체를 만들고 구성 하 여 종속성 및 런타임 컨텍스트를 설정 `RunConfiguration` 합니다. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

위의 코드는 종속성을 처리 하는 두 가지 옵션을 보여 줍니다. 에서 설명한 대로 `USE_CURATED_ENV = True` 구성은 큐 레이트 환경을 기반으로 합니다. 큐 레이트 환경은 일반적인 상호 종속 라이브러리를 포함 하는 "prebaked" 이며 온라인으로 전환 하는 데 훨씬 더 빠르게 수행할 수 있습니다. 큐 레이트 환경은 [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner)에 미리 빌드된 Docker 이미지를 포함 합니다. 자세한 내용은 [Azure Machine Learning 큐 레이트 environment](resource-curated-environments.md)를 참조 하세요.

로 변경 하면 `USE_CURATED_ENV` `False` 종속성을 명시적으로 설정 하는 패턴이 표시 됩니다. 이 시나리오에서는 새 사용자 지정 Docker 이미지가 만들어지고 리소스 그룹 내의 Azure Container Registry에 등록 됩니다 ( [Azure의 개인 Docker 컨테이너 레지스트리 소개](https://docs.microsoft.com/azure/container-registry/container-registry-intro)참조). 이 이미지를 빌드하고 등록 하려면 몇 분 정도 걸릴 수 있습니다.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>파이프라인 단계 구성

계산 리소스 및 환경을 만들었으면 파이프라인의 단계를 정의할 준비가 된 것입니다. [ `azureml.pipeline.steps` 패키지에 대 한 참조 설명서](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)에서 볼 수 있듯이 Azure Machine Learning SDK를 통해 사용할 수 있는 여러 가지 기본 제공 단계가 있습니다. 가장 유연한 클래스는 Python 스크립트를 실행 하는 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)입니다.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

위의 코드는 일반적인 초기 파이프라인 단계를 보여 줍니다. 데이터 준비 코드는 하위 디렉터리에 있습니다 (이 예제에서는 `"prepare.py"` 디렉터리에 `"./dataprep.src"` 있습니다). 파이프라인 생성 프로세스의 일환으로이 디렉터리는로 압축 되 고에 업로드 되 `compute_target` 고 단계는에 대 한 값으로 지정 된 스크립트를 실행 합니다 `script_name` .

`arguments`, `inputs` 및 값은 `outputs` 단계의 입력과 출력을 지정 합니다. 위의 예제에서 기준선 데이터는 데이터 `my_dataset` 집합입니다. 해당 데이터는 코드에서로 지정 되므로 계산 리소스에 다운로드 됩니다 `as_download()` . 이 스크립트는 `prepare.py` 작업에 적절 한 데이터 변환 태스크를 수행 하 고 데이터를 형식의로 출력 합니다 `output_data1` `PipelineData` . 자세한 내용은 [ML 파이프라인 단계 간 데이터 이동 (Python)](how-to-move-data-in-out-of-pipelines.md)을 참조 하세요. 

단계는 구성을 사용 하 여에 정의 된 컴퓨터에서 실행 됩니다 `compute_target` `aml_run_config` . 

이전 결과 ()를 다시 사용 `allow_reuse` 하는 것은 불필요 한 다시 정렬을 제거 하면 민첩성을 제공 하므로 공동 작업 환경에서 파이프라인을 사용할 때 키입니다. 다시 사용은 script_name, 입력 및 단계의 매개 변수가 동일 하 게 유지 되는 경우의 기본 동작입니다. 다시 사용이 허용 되는 경우 이전 실행의 결과가 다음 단계로 즉시 전송 됩니다. `allow_reuse`가로 설정 된 경우 `False` 파이프라인 실행 중에이 단계에 대해 새 실행이 항상 생성 됩니다.

단일 단계를 사용 하 여 파이프라인을 만들 수는 있지만, 대부분의 경우 전체 프로세스를 여러 단계로 분할 하도록 선택할 수 있습니다. 예를 들어 데이터 준비, 학습, 모델 비교 및 배포 단계가 있을 수 있습니다. 예를 들어 위에 지정 된 후에 `data_prep_step` 다음 단계가 학습 될 수 있다고 가정할 수 있습니다.

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = PipelineData(name = "training_results", 
                                datastore=def_blob_store,
                                output_name="training_results")

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

위의 코드는 데이터 준비 단계와 매우 비슷합니다. 학습 코드는 데이터 준비 코드와는 별개의 디렉터리에 있습니다. `PipelineData`데이터 준비 단계의 출력은 `output_data1` 학습 단계에 대 한 _입력_ 으로 사용 됩니다. `PipelineData` `training_results` 이후 비교 또는 배포 단계에 대 한 결과를 저장 하기 위해 새 개체가 만들어집니다. 


> [!TIP]
> 파이프라인 실행이 끝날 때 향상 된 환경을 제공 하 고 데이터 저장소에 중간 데이터를 다시 작성 하는 기능을 사용 하려면 공개 미리 보기 클래스인를 사용 `OutputFileDatasetConfig` 합니다. `OutputFileDatasetConfig` [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py)의 디자인 패턴 및 메서드에 대해 자세히 알아보세요.

단계를 정의한 후 일부 또는 모든 단계를 사용하여 파이프라인을 빌드합니다.

> [!NOTE]
> 단계를 정의 하거나 파이프라인을 빌드할 때 파일 또는 데이터가 Azure Machine Learning 업로드 되지 않습니다. [실험. submit ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)를 호출 하면 파일이 업로드 됩니다.

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>데이터 세트 사용 

Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 만든 데이터 집합은 모든 파이프라인 단계에 대 한 입력으로 사용할 수 있습니다. [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)에 대 한 출력을 작성 하거나 특정 데이터 저장소에 데이터를 쓰려면 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)를 사용 합니다. 

> [!IMPORTANT]
> PipelineData를 사용 하 여 데이터 저장소에 출력 데이터를 다시 쓰는 것은 Azure Blob 및 Azure 파일 공유 데이터 저장소에 대해서만 지원 됩니다. 
>
> Azure Blob에 출력 데이터를 다시 쓰려면 azure 파일 공유, ADLS gen 1 및 ADLS gen 2 데이터 저장소는 공개 미리 보기 클래스인를 사용 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?view=azure-ml-py) 합니다.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

그런 다음 [input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) 사전을 사용 하 여 파이프라인에서 데이터 집합을 검색 합니다.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

줄 `Run.get_context()` 이 강조 표시 됩니다. 이 함수는 `Run` 현재 실험적 실행을 나타내는를 검색 합니다. 위의 샘플에서는이를 사용 하 여 등록 된 데이터 집합을 검색 합니다. 개체의 또 다른 일반적인 용도는 실험 `Run` 자체와 실험이 있는 작업 영역을 모두 검색 하는 것입니다. 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

데이터를 전달 하 고 액세스 하는 다른 방법을 비롯 한 자세한 내용은 [ML 파이프라인 단계 간 데이터 이동 (Python)](how-to-move-data-in-out-of-pipelines.md)을 참조 하세요.

## <a name="caching--reuse"></a>캐싱 & 재사용  

파이프라인의 동작을 최적화 하 고 사용자 지정 하기 위해 캐싱 및 다시 사용에 대 한 몇 가지 작업을 수행할 수 있습니다. 예를 들어 다음을 선택할 수 있습니다.
+ 단계 정의 중에를 설정 하 여 **단계의 기본 재사용을 해제** 합니다 `allow_reuse=False` [step definition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). 불필요 한 실행을 제거 하면 민첩성을 제공 하므로 공동 작업 환경에서 파이프라인을 사용할 때 재사용할 수 있습니다. 그러나 재사용을 옵트아웃 (opt out) 할 수 있습니다.
+ 를 사용 하 여 **실행의 모든 단계에 대해 출력 다시 생성**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

기본적으로 `allow_reuse` 단계를 사용 하 고 `source_directory` 단계 정의에 지정 된를 해시 합니다. 따라서 지정 된 단계에 대 한 스크립트는 동일 하 게 유지 `script_name` 되 고, 입력 및 매개 변수를 그대로 유지 하 고,의 다른 모든 항목이 변경 되지 않은 경우에는 ` source_directory` 이전 단계 실행의 출력이 다시 사용 되 고, 작업이 계산에 전송 되지 않고, 이전 실행의 결과를 다음 단계에서 즉시 사용할 수 있습니다.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>파이프라인 제출

파이프라인을 제출할 때 Azure Machine Learning는 각 단계에 대 한 종속성을 확인 하 고 지정한 원본 디렉터리의 스냅숏을 업로드 합니다. 소스 디렉터리를 지정하지 않으면 현재 로컬 디렉터리가 업로드됩니다. 또한 스냅숏은 작업 영역에 실험의 일부로 저장 됩니다.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> 자세한 내용은 [스냅샷](concept-azure-machine-learning-architecture.md#snapshots)을 참조하세요.

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

파이프라인을 처음 실행하는 경우 Azure Machine Learning은 다음을 수행합니다.

* 작업 영역과 연결된 Blob Storage에서 컴퓨팅 대상으로 프로젝트 스냅샷을 다운로드합니다.
* 파이프라인의 각 단계에 해당하는 Docker 이미지를 빌드합니다.
* 각 단계에 대 한 Docker 이미지를 컨테이너 레지스트리에서 계산 대상으로 다운로드 합니다.
* 및 개체에 대 한 액세스를 구성 `Dataset` `PipelineData` 합니다. `as_mount()`액세스 모드의 경우 퓨즈를 사용 하 여 가상 액세스를 제공 합니다. 마운트가 지원 되지 않거나 사용자가로 액세스를 지정 하는 경우 `as_download()` 데이터는 대신 계산 대상으로 복사 됩니다.

* 단계 정의에 지정된 컴퓨팅 대상에서 단계를 실행합니다. 
* 단계에서 지정한 로그, stdout, stderr, 메트릭, 출력 등의 아티팩트를 만듭니다. 이러한 아티팩트는 업로드 되어 사용자의 기본 데이터 저장소에 보관 됩니다.

![실험을 파이프라인으로 실행하는 다이어그램](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

자세한 내용은 [실험 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) 참조를 참조 하세요.

## <a name="view-results-of-a-pipeline"></a>파이프라인 결과 보기

모든 파이프라인 목록과 스튜디오에서 실행 세부 정보를 확인 합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. [작업 영역을 봅니다](how-to-manage-workspace.md#view).

1. 왼쪽 **에서 파이프라인을 선택 하** 여 모든 파이프라인 실행을 확인 합니다.
 ![기계 학습 파이프라인 목록](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. 특정 파이프라인을 선택하여 실행 결과를 확인합니다.

### <a name="git-tracking-and-integration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 동료 또는 고객과 파이프라인을 공유 하려면 [machine learning 파이프라인 게시](how-to-deploy-pipelines.md) 를 참조 하세요.
- [GitHub에서 이러한 Jupyter 노트북](https://aka.ms/aml-pipeline-readme) 을 사용 하 여 기계 학습 파이프라인 탐색
- [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 [azureml 파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 패키지에 대 한 SDK 참조 도움말을 참조 하세요.
- 파이프라인 디버깅 및 문제 해결에 대 한 팁은 [방법을](how-to-debug-pipelines.md) 참조 하세요.
- [Jupyter 노트북을 사용하여 이 서비스 검색](samples-notebooks.md) 문서를 따라 노트북을 실행하는 방법을 알아봅니다.
