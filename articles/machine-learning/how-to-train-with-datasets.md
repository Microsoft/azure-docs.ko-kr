---
title: Azureml-데이터 집합으로 학습
titleSuffix: Azure Machine Learning
description: 학습에서 데이터 집합을 사용 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b20612756050ae2e9d39f59d049b8c097e3b8010
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651219"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 집합으로 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 학습 실험에서 [Azure Machine Learning 데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) 을 사용 하는 방법에 대해 알아봅니다.  연결 문자열 또는 데이터 경로에 대 한 걱정 없이 로컬 또는 원격 계산 대상에서 데이터 집합을 사용할 수 있습니다.

Azure Machine Learning 데이터 집합은 [평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), 하이퍼 [드라이브](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) 및 [Azure Machine Learning 파이프라인과](how-to-create-your-first-pipeline.md) [같은 Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)교육 제품과 원활한 통합을 제공 합니다.

## <a name="prerequisites"></a>전제 조건

데이터 집합을 만들고 학습 하려면 다음이 필요 합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.

* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.

> [!Note]
> 일부 데이터 집합 클래스에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS 배포판 에서만 지원 됩니다.

## <a name="access-and-explore-input-datasets"></a>입력 데이터 집합 액세스 및 탐색

작업 영역에서 실험의 학습 스크립트를 사용 하 여 기존 TabularDataset에 액세스 하 고 로컬 환경에서 추가 탐색을 위해 해당 데이터 집합을 pandas 데이터 프레임에 로드할 수 있습니다.

다음 코드에서는 클래스의 메서드를 사용 하 여 [`get_context()`]() [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 학습 스크립트의 기존 입력 TabularDataset에 액세스 합니다 `titanic` . 그런 다음 메서드를 사용 하 여 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 학습 전에 추가 데이터 탐색 및 준비를 위해 해당 데이터 집합을 pandas 데이터 프레임에 로드 합니다.

> [!Note]
> 원래 데이터 원본에 NaN, 빈 문자열 또는 빈 값이 포함 된 경우 to_pandas_dataframe ()를 사용 하면 해당 값이 *Null* 값으로 대체 됩니다. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

준비 된 데이터를 메모리 내 pandas 데이터 프레임의 새 데이터 집합에 로드 해야 하는 경우 parquet와 같은 로컬 파일에 데이터를 쓰고 해당 파일에서 새 데이터 집합을 만듭니다. 또한 로컬 파일 또는 datastores의 경로에서 데이터 집합을 만들 수 있습니다. [데이터 집합을 만드는 방법](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

## <a name="use-datasets-directly-in-training-scripts"></a>학습 스크립트에서 직접 데이터 집합 사용

구조화 된 데이터를 데이터 집합으로 아직 등록 하지 않은 경우 TabularDataset를 만들어 로컬 또는 원격 실험을 위한 학습 스크립트에서 직접 사용 합니다.

이 예제에서는 등록 되지 않은 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 을 만들고이를 학습을 위한 개체에 대 한 직접 입력으로 사용 `estimator` 합니다. 작업 영역의 다른 실험에서이 TabularDataset를 다시 사용 하려면 [작업 영역에 데이터 집합을 등록 하는 방법](how-to-create-register-datasets.md#register-datasets)을 참조 하세요.

### <a name="create-a-tabulardataset"></a>TabularDataset 만들기

다음 코드는 웹 url에서 등록 되지 않은 TabularDataset을 만듭니다.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 개체는 TabularDataset의 데이터를 pandas 또는 Spark 데이터 프레임로 로드 하는 기능을 제공 하므로 노트북을 떠나지 않고도 익숙한 데이터 준비 및 학습 라이브러리를 사용할 수 있습니다. 이 기능을 활용 하려면 [입력 데이터 집합 액세스 및 탐색](#access-and-explore-input-datasets)을 참조 하세요.

### <a name="configure-the-estimator"></a>평가기 구성

[평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 개체는 실험 실행을 제출 하는 데 사용 됩니다. Azure Machine Learning 일반적인 기계 학습 프레임 워크에 대 한 사전 구성 된 추정 및 일반 평가기 있습니다.

이 코드는 `est` 를 지정 하는 제네릭 평가기 개체를 만듭니다.

* 스크립트에 대 한 스크립트 디렉터리입니다. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다.
* 학습 스크립트 *train_titanic. py*.
* 학습을 위한 입력 데이터 집합 `titanic_ds` 입니다. `as_named_input()` 는 학습 스크립트의 할당 된 이름으로 입력 데이터 집합을 참조할 수 있도록 해야 `titanic` 합니다. 
* 실험의 계산 대상입니다.
* 실험에 대 한 환경 정의입니다.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>원격 계산 대상에 파일 탑재

구조화 되지 않은 데이터가 있는 경우 [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) 을 만들고 데이터 파일을 탑재 하거나 다운로드 하 여 교육을 위해 원격 계산 대상에서 사용할 수 있도록 합니다. 원격 학습 실험을 위해 [탑재 및 다운로드](#mount-vs-download) 를 사용 하는 경우에 대해 알아봅니다. 

다음 예에서는 FileDataset을 만들고 학습을 위해 평가기에 인수로 전달 하 여 계산 대상에 데이터 집합을 탑재 합니다. 

### <a name="create-a-filedataset"></a>FileDataset 만들기

다음 예제에서는 웹 url에서 등록 되지 않은 FileDataset을 만듭니다. 다른 원본에서 [데이터 집합을 만드는 방법](how-to-create-register-datasets.md) 에 대해 자세히 알아보세요.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>평가기 구성

탑재할 때 데이터 집합을 인수로 전달 하는 것이 좋습니다. 평가기의 매개 변수를 통해 데이터 집합을 전달 하는 것 외에 `inputs` 도 데이터 집합을 통해 데이터 집합을 전달 `script_params` 하 고 인수를 통해 학습 스크립트의 데이터 경로 (탑재 지점)를 가져올 수 있습니다. 이러한 방식으로 모든 클라우드 플랫폼에서 로컬 디버깅 및 원격 교육에 대해 동일한 학습 스크립트를 사용할 수 있습니다.

Scikit [학습](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 평가기 개체는에 대 한 실행을 제출 하는 데 사용 됩니다. 실행을 제출한 후에는 데이터 집합에서 참조 하는 데이터 파일이 `mnist` 계산 대상으로 탑재 됩니다. [평가기](how-to-train-scikit-learn.md)학습을 사용한 학습에 대해 자세히 알아보세요.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>학습 스크립트에서 데이터 검색

다음 코드에서는 스크립트에서 데이터를 검색 하는 방법을 보여 줍니다.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>탑재 및 다운로드

모든 형식의 파일을 탑재 하거나 다운로드 하는 것은 Azure Blob storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 만든 데이터 집합에 대해 지원 됩니다. 

데이터 집합을 **탑재** 하는 경우 데이터 집합에서 참조 하는 파일을 디렉터리 (탑재 지점)에 연결 하 여 계산 대상에서 사용할 수 있도록 합니다. 탑재는 Azure Machine Learning 계산, 가상 컴퓨터 및 HDInsight를 포함 하 여 Linux 기반 계산에 대해 지원 됩니다. 

데이터 집합을 **다운로드** 하면 데이터 집합에서 참조 하는 모든 파일이 계산 대상으로 다운로드 됩니다. 다운로드는 모든 계산 형식에 대해 지원 됩니다. 

스크립트가 데이터 집합에서 참조 하는 모든 파일을 처리 하 고 계산 디스크가 전체 데이터 집합에 적합 한 경우 storage 서비스에서 데이터를 스트리밍하는 오버 헤드를 방지 하기 위해를 다운로드 하는 것이 좋습니다. 데이터 크기가 계산 디스크 크기를 초과 하는 경우 다운로드를 수행할 수 없습니다. 이 시나리오에서는 스크립트에서 사용 하는 데이터 파일만 처리할 때 로드 되므로 탑재 하는 것이 좋습니다.

다음 코드는 `dataset` 에서 임시 디렉터리에 탑재 합니다. `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="access-datasets-in-your-script"></a>스크립트의 데이터 집합에 액세스

등록 된 데이터 집합은 Azure Machine Learning 계산 등의 계산 클러스터에서 로컬로 또는 원격으로 액세스할 수 있습니다. 실험에서 등록 된 데이터 집합에 액세스 하려면 다음 코드를 사용 하 여 작업 영역에 액세스 하 고 이름으로 등록 된 데이터 집합에 액세스 합니다. 기본적으로 [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 클래스의 메서드는 `Dataset` 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>학습 도중에 소스 코드 액세스

Azure Blob Storage는 Azure 파일 공유보다 처리 속도가 빠르며 동시에 시작되는 여러 작업으로 스케일링됩니다. 이러한 이유로 소스 코드 파일을 전송할 때 Blob Storage를 사용하도록 실행을 구성하는 것이 좋습니다.

다음 코드 예제는 실행 구성에서 소스 코드 전송에 사용할 BLOB 데이터 저장소를 지정합니다.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook 예제

[데이터 집합 노트북](https://aka.ms/dataset-tutorial) 은이 문서의 개념을 시연 하 고 확장 합니다.

## <a name="next-steps"></a>다음 단계

* TabularDatasets를 사용 하 여 [기계 학습 모델을 자동으로 학습](how-to-auto-train-remote.md) 합니다.

* FileDatasets을 사용 하 여 [이미지 분류 모델을 학습](https://aka.ms/filedataset-samplenotebook) 합니다.

* [파이프라인을 사용 하 여 데이터 집합으로 학습](how-to-create-your-first-pipeline.md)합니다.
