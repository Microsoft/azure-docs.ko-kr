---
title: 데이터에 액세스 하는 Azure Machine Learning 데이터 집합 만들기
titleSuffix: Azure Machine Learning
description: 기계 학습 실험 실행을 위해 데이터에 액세스 하는 Azure Machine Learning 데이터 집합을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 0bfaef72be23f148c01e02e910b11128cec1659e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116728"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning 데이터 집합 만들기

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 로컬 또는 원격 실험을 위해 데이터에 액세스 하는 Azure Machine Learning 데이터 집합을 만드는 방법에 대해 알아봅니다.

Azure Machine Learning 데이터 집합을 사용 하 여 다음을 수행할 수 있습니다.

* 데이터 집합에서 참조 하는 데이터의 단일 복사본을 저장소에 보관 합니다.

* 연결 문자열 또는 데이터 경로를 걱정 하지 않고 모델 학습 중에 데이터에 원활 하 게 액세스 합니다.

* 데이터를 공유 하 고 다른 사용자와 공동 작업 합니다.

## <a name="prerequisites"></a>사전 요구 사항

데이터 집합을 만들고 작업 하려면 다음이 필요 합니다.

* Azure 구독 계정이 없는 경우 시작 하기 전에 무료 계정을 만듭니다. [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)입니다.

* Azureml 데이터 집합 패키지가 포함 된 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)있습니다.

> [!NOTE]
> 일부 데이터 집합 클래스에는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS 배포판 에서만 지원 됩니다.

## <a name="dataset-types"></a>데이터 세트 형식

사용자가 학습에서 사용 하는 방법에 따라 두 가지 데이터 집합 형식이 있습니다.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 는 제공 된 파일 또는 파일 목록을 구문 분석 하 여 테이블 형식으로 데이터를 나타냅니다. 이를 통해 Pandas 또는 Spark 데이터 프레임로 데이터를 구체화할 수 있습니다. .Csv,. tsv, parquet,. jsonl 파일 및 SQL 쿼리 결과에서 `TabularDataset` 개체를 만들 수 있습니다. 전체 목록은 [TabularDatasetFactory 클래스](https://aka.ms/tabulardataset-api-reference)를 참조 하세요.

* [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 클래스는 데이터 저장소 또는 public url의 단일 또는 여러 파일을 참조 합니다. 이 방법에서는 파일을 FileDataset 개체로 계산에 다운로드 하거나 탑재할 수 있습니다. 이 파일은 심층 학습을 비롯 하 여 다양 한 기계 학습 시나리오를 가능 하 게 하는 모든 형식일 수 있습니다.

예정 된 API 변경 내용에 대 한 자세한 내용은 [데이터 집합 api 변경 공지](https://aka.ms/tabular-dataset)를 참조 하세요.

## <a name="create-datasets"></a>데이터 세트 만들기

데이터 집합을 만들면 데이터 원본 위치에 대 한 참조와 해당 메타 데이터의 복사본을 만듭니다. 데이터가 기존 위치에 남아 있으므로 추가 저장소 비용이 발생 하지 않습니다. Python SDK 또는 작업 영역 방문 페이지 (미리 보기)를 사용 하 여 `TabularDataset` 및 `FileDataset` 데이터 집합을 모두 만들 수 있습니다.

Azure Machine Learning에서 데이터에 액세스할 수 있도록 하려면 [Azure 데이터 저장소](how-to-access-data.md) 또는 공용 웹 url의 경로에서 데이터 집합을 만들어야 합니다.

### <a name="use-the-sdk"></a>SDK 사용

Python SDK를 사용 하 여 [Azure 데이터 저장소](how-to-access-data.md) 에서 데이터 집합을 만들려면 다음을 수행 합니다.

1. 등록 된 Azure 데이터 저장소에 대 한 `contributor` 또는 `owner` 액세스 권한이 있는지 확인 합니다.

2. 데이터 저장소에서 경로를 참조 하 여 데이터 집합을 만듭니다.
> [!Note]
> 여러 데이터 저장소의 여러 경로에서 데이터 집합을 만들 수 있습니다. 데이터 집합을 만들 수 있는 파일 수 또는 데이터 크기에 대 한 하드 제한은 없습니다. 그러나 각 데이터 경로에 대해 몇 가지 요청은 저장소 서비스로 전송 되어 파일이 나 폴더를 가리키는지 확인 합니다. 이러한 오버 헤드로 인해 성능이 저하 되거나 오류가 발생할 수 있습니다. 내에서 1000 파일을 포함 하는 한 개의 폴더를 참조 하는 데이터 집합은 하나의 데이터 경로를 참조 하 고 최적의 성능을 위해 데이터 저장소에서 100 미만의 경로를 참조 하는 데이터 집합을 만드는 것이 좋습니다.


#### <a name="create-a-tabulardataset"></a>TabularDataset 만들기

SDK를 통해 또는 Azure Machine Learning studio를 사용 하 여 TabularDatasets를 만들 수 있습니다. 

`TabularDatasetFactory` 클래스의 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) 메서드를 사용 하 여 .csv 또는. tsv 형식의 파일을 읽고 등록 되지 않은 TabularDataset를 만듭니다. 여러 파일에서 읽는 경우 결과는 하나의 테이블 형식 표현으로 집계 됩니다.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

기본적으로 TabularDataset를 만들면 열 데이터 형식이 자동으로 유추 됩니다. 유추 된 형식이 예상과 일치 하지 않는 경우에는 다음 코드를 사용 하 여 열 형식을 지정할 수 있습니다. [지원 되는 데이터 형식에 대해 자세히 알아볼](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)수도 있습니다.

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|남은|Pclass|속성|성|Age|SibSp|Parch|Ticket|택시|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs Bradley (Florence Briggs Th ...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, 누락. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

`TabularDatasetFactory` 클래스에 대 한 [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) 메서드를 사용 하 여 Azure SQL Database를 읽습니다.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

TabularDatasets에서는 데이터의 열에서 타임 스탬프를 지정 하거나, 경로 패턴 데이터가 저장 되는 위치에서 시간 계열 특성을 사용할 수 있도록 타임 스탬프를 지정할 수 있습니다. 이 사양에서는 시간을 기준으로 쉽고 효율적인 필터링을 수행할 수 있습니다.

`TabularDataset` 클래스에서 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) 메서드를 사용 하 여 타임 스탬프 열을 지정 하 고 시간별 필터링을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [NOAA 날씨 데이터를 사용 하 여 테이블 형식 시계열 관련 API 데모](https://aka.ms/azureml-tsd-notebook)를 참조 하세요.

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>FileDataset 만들기

`FileDatasetFactory` 클래스에 대 한 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 메서드를 사용 하 여 모든 형식의 파일을 로드 하 고 등록 되지 않은 filedataset을 만듭니다.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>웹에서 
다음 단계와 애니메이션은 https://ml.azure.comAzure Machine Learning studio에서 데이터 집합을 만드는 방법을 보여 줍니다.

![UI를 사용 하 여 데이터 집합 만들기](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Studio에서 데이터 집합을 만들려면 다음을 수행 합니다.
1. https://ml.azure.com에서 로그인 합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 집합** 을 선택 합니다. 
1. 데이터 집합 **만들기** 를 선택 하 여 데이터 집합의 원본을 선택 합니다. 이 원본은 로컬 파일, 데이터 저장소 또는 공용 Url 일 수 있습니다.
1. 데이터 집합 형식으로 **테이블** 형식 또는 **파일** 을 선택 합니다.
1. **다음** 을 선택 하 여 **데이터 저장소 및 파일 선택** 양식을 엽니다. 이 폼에서 데이터 집합을 만든 후 데이터 집합에 사용할 데이터 파일을 선택할 뿐만 아니라 데이터 집합을 보관할 위치를 선택할 수 있습니다. 
1. **다음** 을 선택 하 여 **설정 및 미리 보기** 및 **스키마** 폼을 채웁니다. 이러한 형식은 파일 형식에 따라 지능적으로 채워지며 이러한 폼에서 만들기 전에 데이터 집합을 추가로 구성할 수 있습니다. 
1. **다음** 을 선택 하 여 **확인 세부 정보** 양식을 검토 합니다. 선택 항목을 확인 하 고 데이터 집합에 대 한 선택적 데이터 프로필을 만듭니다. [데이터 프로파일링](how-to-create-portal-experiments.md#profile)에 대한 자세한 정보 
1. **만들기** 를 선택 하 여 데이터 집합 만들기를 완료 합니다.

## <a name="register-datasets"></a>데이터 집합 등록

만들기 프로세스를 완료 하려면 작업 영역을 사용 하 여 데이터 집합을 등록 합니다. [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 메서드를 사용 하 여 다른 사람과 공유 하 고 다양 한 실험에서 다시 사용 하기 위해 작업 영역에 데이터 집합을 등록 합니다.

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Azure Machine Learning studio를 통해 만든 데이터 집합은 작업 영역에 자동으로 등록 됩니다.

## <a name="create-datasets-with-azure-open-datasets"></a>Azure Open 데이터 집합을 사용 하 여 데이터 집합 만들기

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 데이터 세트에는 기계 학습 모델을 학습시키고 예측 솔루션을 보강할 수 있는 날씨, 인구, 휴일, 공공 안전 및 위치에 대한 공개 도메인 데이터가 포함되어 있습니다. 개방형 데이터 집합은 Microsoft Azure의 클라우드에 있으며 SDK 및 작업 영역 UI에 모두 포함 되어 있습니다.

### <a name="use-the-sdk"></a>SDK 사용

SDK에서 Azure Open 데이터 집합을 사용 하 여 데이터 집합을 만들려면 `pip install azureml-opendatasets`를 사용 하 여 패키지를 설치 했는지 확인 합니다. 각 불연속 데이터 집합은 SDK에서 자체 클래스로 표시 되며 특정 클래스는 `TabularDataset`, `FileDataset`또는 둘 다로 사용할 수 있습니다. 클래스의 전체 목록은 [참조 설명서](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) 를 참조 하세요.

특정 클래스를 `TabularDataset` 또는 `FileDataset`로 검색 하 여 파일을 직접 조작 및/또는 다운로드할 수 있습니다. 다른 클래스는 `get_tabular_dataset()` 또는 `get_file_dataset()` 함수 중 하나만 **사용 하 여 데이터 집합** 을 가져올 수 있습니다. 다음 코드 샘플에서는 이러한 형식의 클래스에 대 한 몇 가지 예를 보여 줍니다.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

개방형 데이터 집합에서 만든 데이터 집합을 등록 하면 데이터는 즉시 다운로드 되지 않지만 중앙 저장소 위치에서 요청 하는 경우 (예: 학습 중)에는 나중에 데이터에 액세스할 수 있습니다.

### <a name="use-the-ui"></a>UI 사용

UI를 통해 개방형 데이터 집합 클래스에서 데이터 집합을 만들 수도 있습니다. 작업 영역의 **자산**아래에서 **데이터 집합** 탭을 선택 합니다. **데이터 집합 만들기** 드롭다운 메뉴에서 **개방형 데이터 집합에서**를 선택 합니다.

![UI를 사용 하 여 데이터 집합 열기](./media/how-to-create-register-datasets/open-datasets-1.png)

해당 타일을 선택 하 여 데이터 집합을 선택 합니다. 검색 창을 사용 하 여 필터링 할 수 있는 옵션이 있습니다. **다음**을 선택 합니다.

![데이터 집합 선택](./media/how-to-create-register-datasets/open-datasets-2.png)

데이터 집합을 등록할 이름을 선택 하 고 필요에 따라 사용 가능한 필터를 사용 하 여 데이터를 필터링 합니다. 이 경우 공용 휴일 데이터 집합에 대해 기간을 1 년으로, 국가 코드를 미국 으로만 필터링 합니다. **만들기**를 선택합니다.

![데이터 집합 매개 변수 설정 및 데이터 집합 만들기](./media/how-to-create-register-datasets/open-datasets-3.png)

이제 **데이터 집합의**작업 영역에서 데이터 집합을 사용할 수 있습니다. 만든 다른 데이터 집합과 동일한 방법으로 사용할 수 있습니다.

## <a name="version-datasets"></a>버전 데이터 집합

새 버전을 만들어 동일한 이름으로 새 데이터 집합을 등록할 수 있습니다. 데이터 집합 버전은 실험 또는 향후 복제를 위해 특정 버전의 데이터 집합을 적용할 수 있도록 데이터의 상태에 책갈피를 지정 하는 방법입니다. [데이터 집합 버전](how-to-version-track-datasets.md)에 대해 자세히 알아보세요.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>스크립트의 데이터 집합에 액세스

등록 된 데이터 집합은 Azure Machine Learning 계산 등의 계산 클러스터에서 로컬로 또는 원격으로 액세스할 수 있습니다. 실험에서 등록 된 데이터 집합에 액세스 하려면 다음 코드를 사용 하 여 작업 영역에 액세스 하 고 이름으로 등록 된 데이터 집합에 액세스 합니다. 기본적으로 `Dataset` 클래스의 [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) 메서드는 작업 영역에 등록 된 데이터 집합의 최신 버전을 반환 합니다.

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

## <a name="next-steps"></a>다음 단계

* [데이터 집합을 사용 하 여 학습 하는 방법을](how-to-train-with-datasets.md)알아봅니다.
* 자동화 된 machine learning을 사용 하 여 [TabularDatasets으로 학습](https://aka.ms/automl-dataset)합니다.
* 더 많은 데이터 집합 학습 예제는 [샘플 노트북](https://aka.ms/dataset-tutorial)을 참조 하세요.
