---
title: ML 파이프라인에서 자동화 된 ML 사용
titleSuffix: Azure Machine Learning
description: AutoMLStep를 사용 하면 파이프라인에서 자동화 된 machine learning을 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 08/26/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0daa094a6d804cd8a40c4ba76b696e3c9b580f8a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230350"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Python의 Azure Machine Learning 파이프라인에서 자동화 된 ML 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning의 자동화 된 ML 기능을 사용 하면 가능한 모든 방법을 reimplementing 하지 않고도 고성능 모델을 검색할 수 있습니다. Azure Machine Learning 파이프라인과 결합 하 여 데이터에 가장 적합 한 알고리즘을 신속 하 게 검색할 수 있는 배포 가능한 워크플로를 만들 수 있습니다. 이 문서에서는 자동화 된 ML 단계에 데이터 준비 단계를 효율적으로 조인 하는 방법을 보여 줍니다. 자동화 된 ML은 사용자의 데이터에 가장 적합 한 알고리즘을 신속 하 게 검색할 수 있으며, 파이프라인으로 운영 화 된 MLOps 및 모델 수명 주기로 이동 하 게 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.  

* Azure의 [자동화 된 machine learning](concept-automated-ml.md) 및 [machine learning 파이프라인](concept-ml-pipelines.md) 시설 및 SDK에 대 한 기본 지식.

## <a name="review-automated-mls-central-classes"></a>자동화 된 ML의 중앙 클래스 검토

파이프라인의 자동화 된 ML은 개체로 표시 됩니다 `AutoMLStep` . `AutoMLStep` 클래스는 `PipelineStep`의 서브클래스입니다. 개체의 그래프는 `PipelineStep` 를 정의 `Pipeline` 합니다.

에는 여러 개의 하위 클래스가 있습니다 `PipelineStep` . 외에도 `AutoMLStep` 이 문서에서는 `PythonScriptStep` 데이터 준비 및 모델 등록을 위한을 보여 줍니다.

처음에 데이터를 ML 파이프라인 _으로_ 이동 하는 기본 방법은 개체를 사용 하는 것입니다 `Dataset` . 단계 _간에_ 데이터를 이동 하려면 개체를 사용 하는 것이 좋습니다 `PipelineData` . 에서 사용 하려면 `AutoMLStep` `PipelineData` 개체를 개체로 변환 해야 합니다 `PipelineOutputTabularDataset` . 자세한 내용은 [ML 파이프라인의 입력 및 출력 데이터](how-to-move-data-in-out-of-pipelines.md)를 참조 하세요.


> [!TIP]
> 파이프라인 단계 간에 임시 데이터를 전달 하기 위한 향상 된 환경은 공개 미리 보기 클래스인 및에서 사용할 수 있습니다  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py) .  이러한 클래스는 [실험적](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#stable-vs-experimental) 미리 보기 기능 이며 언제 든 지 변경 될 수 있습니다.

는 `AutoMLStep` 개체를 통해 구성 됩니다 `AutoMLConfig` . `AutoMLConfig` 는 [Python에서 자동화 된 ML 실험 구성](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings)에 설명 된 대로 유연한 클래스입니다. 

는 `Pipeline` 에서 실행 됩니다 `Experiment` . 파이프라인에는 `Run` 각 단계에 대 한 자식가 있습니다 `StepRun` . 자동화 된 ML의 출력은 `StepRun` 학습 메트릭과 최고 성능의 모델입니다.

구체적으로 설명 하기 위해이 문서에서는 분류 태스크에 대 한 간단한 파이프라인을 만듭니다. 이 태스크는 Titanic을 예측 하 고 있지만, 전달 하는 것을 제외 하 고 데이터 또는 태스크에 대해서는 다루지 않습니다.

## <a name="get-started"></a>시작

### <a name="retrieve-initial-dataset"></a>초기 데이터 집합 검색

일반적으로 ML 워크플로는 기존 기준 데이터로 시작 합니다. 이는 등록 된 데이터 집합에 적합 한 시나리오입니다. 데이터 집합은 작업 영역 전체에 표시 되 고 버전 관리가 지원 되며 대화형으로 탐색할 수 있습니다. [Azure Machine Learning 데이터 집합 만들기](how-to-create-register-datasets.md)에 설명 된 대로 여러 가지 방법으로 데이터 집합을 만들고 채울 수 있습니다. Microsoft는 Python SDK를 사용 하 여 파이프라인을 만들기 때문에 SDK를 사용 하 여 기준 데이터를 다운로드 하 고 ' titanic_ds ' 이름으로 등록 합니다.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

코드는 먼저 **config.js** 에 정의 된 Azure Machine Learning 작업 영역에 로그인 합니다. 설명은 [자습서: Python SDK를 사용 하 여 첫 번째 ML 실험 만들기 시작](tutorial-1st-experiment-sdk-setup.md)을 참조 하세요. 등록 된 데이터 집합이 아직 없는 경우 `'titanic_ds'` 새로 만듭니다. 이 코드는 웹에서 CSV 데이터를 다운로드 하 고이를 사용 하 여를 인스턴스화한 `TabularDataset` 다음 작업 영역에 데이터 집합을 등록 합니다. 마지막으로 함수는 `Dataset.get_by_name()` `Dataset` 를에 할당 합니다 `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>저장소 및 계산 대상 구성

파이프라인이 필요로 하는 추가 리소스는 저장소 이며 일반적으로 Azure Machine Learning 계산 리소스입니다. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

데이터 준비와 자동 ML 단계 사이의 중간 데이터를 작업 영역의 기본 데이터 저장소에 저장할 수 있으므로 개체에 대해 호출 하는 것 보다 더 많은 작업을 수행할 필요가 없습니다 `get_default_datastore()` `Workspace` . 

그런 다음, 코드는 AML 계산 대상이 이미 있는지 여부를 확인 합니다 `'cpu-cluster'` . 그렇지 않은 경우에는 작은 CPU 기반 계산 대상이 필요 하도록 지정 합니다. 자동화 된 ML의 심층 학습 기능 (예를 들어 DNN를 지 원하는 텍스트 기능화)을 사용 하려는 경우 [gpu 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu)에 설명 된 대로 강력한 gpu를 지 원하는 계산을 선택 해야 합니다. 

이 코드는 대상이 프로 비전 될 때까지 차단한 다음, 위에서 만든 계산 대상의 세부 정보를 인쇄 합니다. 마지막으로, 명명 된 계산 대상이 작업 영역에서 검색 되 고에 할당 됩니다 `compute_target` . 

### <a name="configure-the-training-run"></a>학습 실행 구성

다음 단계는 원격 학습 실행에 학습 단계에 필요한 모든 종속성이 있는지 확인 하는 것입니다. 개체를 만들고 구성 하 여 종속성 및 런타임 컨텍스트를 설정 `RunConfiguration` 합니다. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
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
        pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

위의 코드는 종속성을 처리 하는 두 가지 옵션을 보여 줍니다. 에서 설명한 대로 `USE_CURATED_ENV = True` 구성은 큐 레이트 환경을 기반으로 합니다. 큐 레이트 환경은 일반적인 상호 종속 라이브러리를 포함 하는 "prebaked" 이며 온라인으로 전환 하는 데 훨씬 더 빠르게 수행할 수 있습니다. 큐 레이트 환경은 [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner)에 미리 빌드된 Docker 이미지를 포함 합니다. 로 변경 하면 `USE_CURATED_ENV` `False` 종속성을 명시적으로 설정 하는 패턴이 표시 됩니다. 이 시나리오에서는 새 사용자 지정 Docker 이미지가 만들어지고 리소스 그룹 내의 Azure Container Registry에 등록 됩니다 ( [Azure의 개인 Docker 컨테이너 레지스트리 소개](https://docs.microsoft.com/azure/container-registry/container-registry-intro)참조). 이 이미지를 빌드하고 등록 하려면 몇 분 정도 걸릴 수 있습니다. 

## <a name="prepare-data-for-automated-machine-learning"></a>자동화 된 기계 학습을 위한 데이터 준비

### <a name="write-the-data-preparation-code"></a>데이터 준비 코드 작성

기준선 Titanic 데이터 집합은 숫자와 텍스트의 혼합 된 데이터로 구성 되며 일부 값은 누락 됩니다. 자동화 된 기계 학습을 준비 하기 위해 데이터 준비 파이프라인 단계는 다음을 수행 합니다.

- 임의의 데이터 또는 "알 수 없음"에 해당 하는 범주를 사용 하 여 누락 된 데이터를 채웁니다.
- 범주 데이터를 정수로 변환
- 사용 하지 않으려는 열 삭제
- 학습 및 테스트 집합으로 데이터 분할
- 변환 된 데이터를 다음 중 하나에 씁니다.
    - `PipelineData` 출력 경로

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

위의 코드 조각은 Titanic 데이터에 대 한 데이터 준비의 완전 한 예제 이며 최소한의 예제입니다. 코드 조각을 파일에 출력 하는 Jupyter "매직 command"로 시작 합니다. Jupyter 노트북을 사용 하지 않는 경우 해당 줄을 제거 하 고 수동으로 파일을 만듭니다.

`prepare_`위의 코드 조각에 있는 다양 한 함수는 입력 데이터 집합에서 관련 열을 수정 합니다. 이러한 함수는 Pandas 개체로 변경 된 데이터에 대해 작동 `DataFrame` 합니다. 각각의 경우 누락 된 데이터는 대표적인 임의의 데이터 또는 "알 수 없음"을 나타내는 범주 데이터로 채워집니다. 텍스트 기반 범주 데이터는 정수에 매핑됩니다. 더 이상 필요 하지 않은 열을 덮어쓰거나 삭제 합니다. 

코드에서 데이터 준비 함수를 정의한 후에는 데이터를 쓰려는 경로인 입력 인수를 구문 분석 합니다. 이러한 값은 `PipelineData` 다음 단계에서 설명 하는 개체에 따라 결정 됩니다. 이 코드는 등록 된를 검색 `'titanic_cs'` `Dataset` 하 고, Pandas로 변환 하 `DataFrame` 고, 다양 한 데이터 준비 함수를 호출 합니다. 

는 정규화 `output_path` 되어 있으므로 `os.makedirs()` 디렉터리 구조를 준비 하는 데 함수를 사용 합니다. 이 시점에서를 사용 하 여 `DataFrame.to_csv()` 출력 데이터를 작성할 수 있지만 Parquet 파일이 더 효율적입니다. 이러한 효율성은 이러한 작은 데이터 집합의 경우와 관련이 없을 수도 있지만 **PyArrow** 패키지의 및 함수를 사용 하는 `from_pandas()` `write_table()` 것은 보다 약간의 키 입력 `to_csv()` 입니다.

Parquet 파일은 아래에 설명 된 자동화 된 ML 단계에서 기본적으로 지원 되므로 특수 한 처리를 사용 하는 데 필요 하지 않습니다. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>데이터 준비 파이프라인 단계 () 작성 `PythonScriptStep`

위에서 설명한 데이터 준비 코드는 `PythonScripStep` 파이프라인과 함께 사용할 개체에 연결 되어 있어야 합니다. Parquet 데이터 준비 출력이 쓰여지는 경로는 개체에 의해 생성 됩니다 `PipelineData` . , 및와 같이 앞에서 준비한 리소스는 `ComputeTarget` `RunConfig` `'titanic_ds' Dataset` 사양을 완료 하는 데 사용 됩니다.

PipelineData 사용자
```python
from azureml.pipeline.core import PipelineData

from azureml.pipeline.steps import PythonScriptStep
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```
`prepped_data_path`개체는 형식입니다 `PipelineOutputFileDataset` . 및 인수 모두에 지정 되어 있는지 확인 `arguments` `outputs` 합니다. 이전 단계를 검토 하는 경우 데이터 준비 코드 내에서 인수의 값은 `'--output_path'` Parquet 파일을 쓴 파일 경로를 확인할 수 있습니다. 

> [!TIP]
> 파이프라인 단계 사이에 중간 데이터를 전달 하는 향상 된 환경은 공개 미리 보기 클래스인에서 사용할 수 있습니다 `OutputFileDatasetConfig` . `OutputFileDatasetConfig` [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py)의 디자인 패턴 및 메서드에 대해 자세히 알아보세요.

## <a name="train-with-automlstep"></a>AutoMLStep로 학습

자동화 된 ML 파이프라인 단계 구성은 클래스를 사용 하 여 수행 `AutoMLConfig` 합니다. 이 유연한 클래스는 [Python에서 자동화 된 ML 실험 구성](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)에 설명 되어 있습니다. 데이터 입력 및 출력은 ML 파이프라인에서 특별 한 주의가 필요한 구성의 유일한 측면입니다. 파이프라인의에 대 한 입력 및 출력 `AutoMLConfig` 은 아래에서 자세히 설명 합니다. 데이터 외에도 ML 파이프라인을 사용 하는 경우 각 단계에 서로 다른 계산 대상을 사용할 수 있습니다. 자동화 된 ML 프로세스에 대해서만 보다 강력한를 사용 하도록 선택할 수 있습니다 `ComputeTarget` . 이렇게 하는 것은 `RunConfiguration` `AutoMLConfig` 개체의 매개 변수에 더 강력한를 할당 하는 것 만큼 간단 `run_configuration` 합니다.

### <a name="send-data-to-automlstep"></a>데이터 보내기 `AutoMLStep`

ML 파이프라인에서 입력 데이터는 개체 여야 합니다 `Dataset` . 가장 높은 방법은 입력 데이터를 개체 형식으로 제공 하는 것입니다 `PipelineOutputTabularDataset` . 개체 등의 또는을 사용 하 여 해당 형식의 개체를 만듭니다 `parse_parquet_files()` `parse_delimited_files()` `PipelineOutputFileDataset` `prepped_data_path` .

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

위의 코드 조각은 `PipelineOutputTabularDataset` `PipelineOutputFileDataset` 데이터 준비 단계의 출력에서 높은 성능을 생성 합니다.

> [!TIP]
> 공개 미리 보기 클래스에는 `OutputFileDatasetConfig` `OutputFileDatasetConfig` [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py) automl 실행에서 사용 하기 위해를로 변환 하는 기능도 있습니다. `OutputFileDatasetConfig` [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py)의 디자인 패턴 및 메서드에 대해 자세히 알아보세요.

또 다른 옵션은 `Dataset` 작업 영역에 등록 된 개체를 사용 하는 것입니다.

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

두 가지 방법을 비교 합니다.

| 방법 | 이점 및 단점 | 
|-|-|
|`PipelineOutputTabularDataset`| 성능 향상 | 
|| 일반 경로 `PipelineData` | 
|| 파이프라인 실행 후 데이터가 지속 되지 않습니다. |
|| [기술을 보여 주는 노트북 `PipelineOutputTabularDataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| 등록 `Dataset` | 낮은 성능 |
| | 여러 가지 방법으로 생성할 수 있습니다. | 
| | 데이터는 지속 되 고 작업 영역 전체에 표시 됩니다. |
| | [등록 된 기술을 보여 주는 노트북 `Dataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>자동화 된 ML 출력 지정

의 출력은 `AutoMLStep` 성능이 높고 모델 자체의 최종 메트릭 점수입니다. 추가 파이프라인 단계에서 이러한 출력을 사용 하려면 `PipelineData` 개체를 받도록 준비 합니다.

```python

from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

위의 코드 조각은 `PipelineData` 메트릭 및 모델 출력에 대 한 두 개체를 만듭니다. 각에는 이름이로 지정 되 고, 앞에서 검색 된 기본 데이터 저장소에 할당 되며,에서 특정의와 연결 됩니다 `type` `TrainingOutput` `AutoMLStep` . 이러한 개체를 할당 하기 때문에 `pipeline_output_name` `PipelineData` 해당 값은 개별 파이프라인 단계 뿐만 아니라 파이프라인 전체에서 사용할 수 있습니다 .이에 대해서는 아래 "파이프라인 결과 검사" 섹션에서 설명 합니다. 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>자동화 된 ML 파이프라인 단계 구성 및 만들기

입력 및 출력이 정의 되 면 및를 만들 차례 `AutoMLConfig` `AutoMLStep` 입니다. 구성의 세부 정보는 [Python에서 자동화 된 ML 실험 구성](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)에 설명 된 대로 작업에 따라 달라 집니다. Titanic 생존 분류 작업의 경우 다음 코드 조각은 간단한 구성을 보여 줍니다.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
이 코드 조각에서는에서 일반적으로 사용 되는 방법을 보여 줍니다 `AutoMLConfig` . 변경 가능성이 낮은 값은 생성자에서 직접 지정 되는 반면, 유체 (하이퍼 매개 변수-길쭉한) 인 인수는 별도 사전에 지정 됩니다 `AutoMLConfig` . 이 경우에는 `automl_settings` 짧은 실행을 지정 합니다. 실행은 두 번의 반복 또는 15 분 중 먼저 발생 한 후에 중지 됩니다.

`automl_settings`사전은 `AutoMLConfig` 생성자에 kwargs로 전달 됩니다. 다른 매개 변수는 복잡 하지 않습니다.

- `task` 이 예제에서는가로 설정 됩니다 `classification` . 다른 유효한 값은 `regression` 및입니다. `forecasting`
- `path` 및는 `debug_log` 프로젝트 경로와 디버그 정보가 기록 될 로컬 파일을 설명 합니다. 
- `compute_target` 는 `compute_target` 이 예제에서 저렴 한 CPU 기반 컴퓨터를 정의 하는 이전에 정의 된입니다. AutoML의 심층 학습 기능을 사용 하는 경우 계산 대상을 GPU 기반으로 변경 해야 합니다.
- `featurization`이 `auto`로 설정됩니다. 자세한 내용은 자동화 된 ML 구성 문서의 [Data 기능화](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) 섹션에서 찾을 수 있습니다. 
- `label_column_name` 예측 하려는 열을 나타냅니다. 
- `training_data` 는 `PipelineOutputTabularDataset` 데이터 준비 단계의 출력에서 만든 개체로 설정 됩니다. 

자체는를 `AutoMLStep` 사용 하 `AutoMLConfig` 고, `PipelineData` 메트릭과 모델 데이터를 저장 하기 위해 만든 개체를 출력으로 사용 합니다. 

>[!Important]
> 을 사용 하 `enable_default_model_output` `enable_default_metrics_output`  는 경우에만로 설정 해야 합니다 `True`  `AutoMLStepRun` .

이 예에서는 자동화 된 ML 프로세스가에서 교차 유효성 검사를 수행 합니다 `training_data` . 인수를 사용 하 여 교차 유효성 검사의 수를 제어할 수 있습니다 `n_cross_validations` . 데이터 준비 단계 중에 학습 데이터를 이미 분할 한 경우 `validation_data` 해당 데이터를 자체로 설정할 수 있습니다 `Dataset` .

데이터 `X` 기능 및 데이터 레이블에 대 한 사용이 때때로 표시 될 수 있습니다 `y` . 이 기술은 더 이상 사용 되지 않으며, 입력에를 사용 해야 합니다 `training_data` . 

## <a name="register-the-model-generated-by-automated-ml"></a>자동 ML에 의해 생성 된 모델 등록 

기본 ML 파이프라인의 마지막 단계는 생성 된 모델을 등록 하는 것입니다. 작업 영역 모델 레지스트리에 모델을 추가 하 여 포털에서 사용할 수 있으며 버전을 지정할 수 있습니다. 모델을 등록 하려면 `PythonScriptStep` 의 출력을 사용 하는 다른 모델을 작성 합니다 `model_data` `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>모델을 등록 하는 코드 작성

모델은에 등록 되어 `Workspace` 있습니다. 를 사용 하 여 `Workspace.from_config()` 로컬 컴퓨터의 작업 영역에 로그온 하는 것에 익숙하고 실행 중인 ML 파이프라인 내에서 작업 영역을 가져오는 또 다른 방법이 있습니다. 는 `Run.get_context()` 활성를 검색 합니다 `Run` . 이 `run` 개체는 여기에 사용 된을 포함 하 여 많은 중요 한 개체에 대 한 액세스를 제공 `Workspace` 합니다.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>PythonScriptStep 코드 작성

모델 등록은 `PythonScriptStep` `PipelineParameter` 인수 중 하나에 대해를 사용 합니다. 파이프라인 매개 변수는 실행 제출 시 쉽게 설정할 수 있는 파이프라인에 대 한 인수입니다. 선언 되 면 일반 인수로 전달 됩니다. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>자동화 된 ML 파이프라인 만들기 및 실행

을 포함 하는 파이프라인을 만들고 실행 하 `AutoMLStep` 는 것은 일반 파이프라인과 다릅니다. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

위의 코드는 데이터 준비, 자동화 된 ML 및 모델 등록 단계를 하나의 개체로 결합 합니다 `Pipeline` . 그런 다음 개체를 만듭니다 `Experiment` . `Experiment`생성자는 명명 된 실험 (있는 경우)을 검색 하거나 필요한 경우 만듭니다. 를에 전송 하 여 `Pipeline` `Experiment` 파이프라인을 비동기식으로 `Run` 실행 하는 개체를 만듭니다. `wait_for_completion()`함수는 실행이 완료 될 때까지 차단 됩니다.

### <a name="examine-pipeline-results"></a>파이프라인 결과 검사 

`run`이 완료 되 면이 할당 된 개체를 검색할 수 있습니다 `PipelineData` `pipeline_output_name` . 결과를 다운로드 하 고 추가 처리를 위해 로드할 수 있습니다.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

다운로드 한 파일은 하위 디렉터리에 기록 됩니다 `azureml/{run.id}/` . 메트릭 파일은 JSON 형식 이므로 조사를 위해 Pandas 데이터 프레임 변환할 수 있습니다.

로컬 처리의 경우 Pandas, Pickle, AzureML SDK 등의 관련 패키지를 설치 해야 할 수 있습니다. 이 예에서는 자동화 된 ML에서 발견 한 최상의 모델이 XGBoost에 종속 될 가능성이 높습니다.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

위의 코드 조각에서는 Azure 데이터 저장소의 해당 위치에서 로드 되는 메트릭 파일을 보여 줍니다. 설명에 나와 있는 것 처럼 다운로드 한 파일에서 로드할 수도 있습니다. Deserialize 하 고 Pandas 데이터 프레임로 변환한 후에는 자동화 된 ML 단계의 각 반복에 대 한 자세한 메트릭을 볼 수 있습니다.

모델 파일을 `Model` 추론, 추가 메트릭 분석 등에 사용할 수 있는 개체로 deserialize 할 수 있습니다. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

기존 모델을 로드 하 고 사용 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 기존 모델 사용](how-to-deploy-existing-model.md)을 참조 하세요.

### <a name="download-the-results-of-an-automated-ml-run"></a>자동화 된 ML 실행 결과 다운로드

문서와 함께 다음을 수행 했다면 인스턴스화된 `run` 개체가 있습니다. 하지만 개체를 기준으로에서 완료 된 개체를 검색할 수도 있습니다 `Run` `Workspace` `Experiment` .

작업 영역에는 모든 실험 및 실행에 대 한 전체 레코드가 포함 되어 있습니다. 포털을 사용 하 여 실험의 출력을 찾아서 다운로드 하거나 코드를 사용할 수 있습니다. 기록 실행에서 레코드에 액세스 하려면 Azure Machine Learning를 사용 하 여 관심 있는 실행의 ID를 찾습니다. 해당 ID를 사용 하 여 및를 통해 특정를 선택할 수 있습니다 `run` `Workspace` `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

위의 코드에 있는 문자열을 기록 실행의 세부 사항으로 변경 해야 합니다. 위의 코드 조각에서는 `ws` 일반적인와 관련 된를에 할당 했다고 가정 합니다 `Workspace` `from_config()` . 관심이 있는 실험을 직접 검색 한 다음 코드에서 `Run` 값을 일치 시켜 관심 있는를 찾습니다 `run.id` .

`Run`개체가 있으면 메트릭과 모델을 다운로드할 수 있습니다. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

각 `Run` 개체에는 `StepRun` 개별 파이프라인 단계 실행에 대 한 정보를 포함 하는 개체가 포함 되어 있습니다. 는에 `run` `StepRun` 대 한 개체를 검색 합니다 `AutoMLStep` . 메트릭 및 모델은 `PipelineData` 의 매개 변수에 개체를 전달 하지 않는 경우에도 사용할 수 있는 기본 이름을 사용 하 여 검색 됩니다 `outputs` `AutoMLStep` . 

마지막으로, 위의 "파이프라인 결과 검사" 섹션에 설명 된 대로 실제 메트릭 및 모델이 로컬 컴퓨터에 다운로드 됩니다.

## <a name="next-steps"></a>다음 단계

- 회귀를 사용 하 여 taxi 정의 요금를 예측 하는 [파이프라인에서 자동화 된 ML의 전체 예제](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) 를 보여 주는이 jupyter 노트북을 실행 합니다.
- [코드를 작성 하지 않고 자동화 된 ML 실험 만들기](how-to-use-automated-ml-for-ml-models.md)
- 자동화 된 ML을 [보여 주는 다양 한 Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 살펴보기
- 의 파이프라인을 [종단 간 MLOps](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) 에 통합 또는 [Mlops GitHub 리포지토리](https://github.com/Microsoft/MLOpspython) 조사를 참조 하세요. 
