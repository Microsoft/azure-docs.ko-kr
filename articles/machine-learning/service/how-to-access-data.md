---
title: 학습을 위해 데이터 저장소/Blob의 데이터에 액세스
titleSuffix: Azure Machine Learning service
description: 데이터 저장소를 사용하여 Azure Machine Learning Service로 학습 중에 Blob 데이터 스토리지에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 0ab01187b03b3d658b171029003667588382bd7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820285"
---
# <a name="access-data-from-your-datastores"></a>사용자 데이터 저장소에서 데이터에 액세스

 Azure Machine Learning 서비스에서 데이터 저장소는 소스 코드를 변경 하지 않고도 저장소에 액세스할 위치 독립적인 메커니즘을 계산 합니다. 학습 경로 매개 변수로 수행 하는 코드를 쓰거나 평가기에 직접 데이터 저장소를 제공 하는지 여부를 데이터 저장소 위치에 액세스할 수 있으며 계산 컨텍스트를 제공할 Azure Machine Learning 워크플로 확인 합니다.

이 방법 문서를 사용 하면 다음과 같은 작업의 예가 있습니다.
* [데이터 저장소 선택](#access)
* [데이터 가져오기](#get)
* [업로드 하 고 데이터 저장소에 데이터를 다운로드 합니다.](#up-and-down)
* [학습 중 데이터 저장소 액세스](#train)

## <a name="prerequisites"></a>필수 조건

데이터 저장소를 사용하려면 먼저 [작업 영역](concept-azure-machine-learning-architecture.md#workspace)이 필요합니다.

[새 작업 영역을 만들](setup-create-workspace.md#sdk)거나 기존 작업 영역을 검색합니다.

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>데이터 저장소 선택

기본 데이터 저장소를 사용 하거나 직접 준비 수 있습니다.

### <a name="use-the-default-datastore-in-your-workspace"></a>작업 영역에서 기본 데이터 저장소를 사용 합니다.

 각 작업 영역에 바로 사용할 수 있는 등록된 기본 데이터 저장소.

작업 영역의 기본 데이터 저장소를 가져오려면 다음 코드를 사용합니다.

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>작업 영역을 사용 하 여 사용자 고유의 데이터 저장소를 등록 합니다.

기존 Azure Storage가 있는 경우에는 작업 영역의 데이터 저장소로 등록할 수 있습니다.   모든 등록 방법에는 [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 클래스 및 폼 register_azure_ * 있어야 합니다. 

다음 예제에서는 표시를 데이터 저장소로 Azure Blob 컨테이너 또는 Azure 파일 공유를 등록할 수 있습니다.

+ 에 **Azure Blob 컨테이너 데이터 저장소**를 사용 하 여 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 에 **Azure 파일 공유 데이터 저장소**를 사용 하 여 [ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)합니다. 예를 들면 다음과 같습니다. 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>찾기 및 데이터 저장소를 정의 합니다.

현재 작업 영역에 등록 하는 지정 된 데이터 저장소를 가져오려면 [ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

지정된 된 작업 영역에서 모든 데이터 저장소의 목록을 가져오려면,이 코드를 사용 합니다.

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

현재 작업 영역에 대 한 다른 기본 데이터 저장소를 정의 하기 위해 사용 하 여 [ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>업로드 및 다운로드 데이터
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 하 고 [ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 다음 예제에 설명 된 방법와 관련이 있으며 동일 하 게 작동 합니다 [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 및 [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 클래스입니다.

### <a name="upload"></a>업로드

 Python SDK를 사용하여 데이터 저장소에 디렉터리 또는 개별 파일을 업로드합니다.

데이터 저장소 `ds`에 디렉터리를 업로드하려면 다음 명령을 사용합니다.

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path`는 업로드할 파일 공유 또는 blob 컨테이너의 위치를 지정합니다. 기본 위치는 `None`입니다(데이터가 루트에 업로드됨). `overwrite=True`를 사용하는 경우 `target_path`에서 기존 데이터를 덮어씁니다.

또는 데이터 저장소의 `upload_files()` 메서드를 통해 개별 파일 목록을 데이터 저장소에 업로드합니다.

### <a name="download"></a>다운로드
업로드와 마찬가지로 데이터 저장소의 데이터를 로컬 파일 시스템에 다운로드합니다.

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path`는 데이터를 다운로드할 로컬 디렉터리의 위치입니다. 다운로드할 파일 공유 또는 blob 컨테이너의 폴더 경로를 지정하려면 `prefix`에 해당 경로를 입력합니다. `prefix`가 `None`이면 파일 공유 또는 blob 컨테이너의 모든 콘텐츠가 다운로드됩니다.

<a name="train"></a>
## <a name="access-datastores-during-training"></a>학습 중 데이터 저장소 액세스

계산 대상에서 데이터 저장소를 사용할 수 있도록 면 단순히 학습 스크립트의 매개 변수에 경로를 전달 하 여 교육 실행 (예: 학습 또는 유효성 검사 데이터) 하는 동안 액세스할 수 있습니다.

다음 표에서 [ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 계산 대상을 실행 하는 동안 데이터 저장소를 사용 하는 방법을 지시 하는 방법입니다.

Way|방법|설명|
----|-----|--------
탑재| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 계산 대상에 데이터 저장소를 탑재 하려면 사용 합니다.
다운로드|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|지정 된 위치에 데이터 저장소의 콘텐츠를 다운로드 하는 데 `path_on_compute`합니다. <br> 교육 실행 컨텍스트에 대 한이 다운로드를 실행 하기 전에 발생합니다.
업로드|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 지정 된 위치에서 파일을 업로드 하는 데 `path_on_compute` 데이터 저장소에 있습니다. <br> 교육 실행 컨텍스트에 대 한이 업로드 실행 후 발생합니다.

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

특정 폴더 또는 데이터 저장소에 파일을 참조 하 고 계산 대상에 사용할 수 있도록 하려면 데이터 저장소의 사용 [ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-) 함수입니다.

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> 모든 `ds` 하거나 `ds.path` 개체 이름을 확인 하는 환경 변수 형식의 `"$AZUREML_DATAREFERENCE_XXXX"` 값인 대상 계산의 탑재/다운로드 경로 나타냅니다. 대상 컴퓨터에서 데이터 저장소 경로 학습 스크립트의 실행 경로와 동일한 수 없습니다.

### <a name="compute-context-and-datastore-type-matrix"></a>계산 컨텍스트 및 데이터 저장소 유형 분류표

다음 매트릭스는 다양 한 계산 컨텍스트 및 데이터 저장소 시나리오에 대 한 사용 가능한 데이터 액세스 기능을 표시합니다. 이 행렬의 "파이프라인" 이라는 용어를 입력으로 데이터 저장소를 사용 하거나 출력 하는 기능을 [Azure Machine Learning 파이프라인](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines)합니다.

||로컬 계산|Azure Machine Learning 컴퓨팅|데이터 전송|Databricks|HDInsight|Azure Batch|Azure DataLake Analytics|Virtual Machines|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> 파이프라인|파이프라인|파이프라인|[`as_download()`] <br> [`as_upload()`]|파이프라인||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Pipeline |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||파이프라인|파이프라인|||파이프라인||
|AzureDataLakeGen2Datastore|||파이프라인||||||
|AzureDataPostgresSqlDatastore|||파이프라인||||||
|AzureSqlDatabaseDataDatastore|||파이프라인||||||


> [!NOTE]
> 큰 데이터 프로세스 빠르게 사용 하 여 실행 빈도가 높은 시나리오가 있을 수 있습니다 [`as_download()`] 대신 [`as_mount()`]; 갔다가 검사할 수 있습니다.

### <a name="examples"></a>예 

다음 코드 예제는 관련 된 [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) 학습 중에 데이터 저장소에 액세스 하기 위한 클래스입니다.

이 코드에서는 학습 스크립트를 사용 하 여 평가기 `train.py`에 정의 된 매개 변수를 사용 하 여 표시 된 소스 디렉터리에서 `script_params`, 지정 된 계산 대상에 모두 있습니다.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

스 티 메이 터 생성자에 데이터 저장소 목록에 전달할 수도 있습니다 `inputs` 매개 변수를 탑재 또는 사용자 데이터 저장소에서 복사 합니다. 이 코드 예제:
* 데이터 저장소에 모든 콘텐츠를 다운로드 `ds1` 학습 스크립트 하기 전에 계산 대상에 `train.py` 실행
* 폴더를 다운로드 `'./foo'` 데이터 저장소에 `ds2` 하기 전에 계산 대상에 `train.py` 실행
* 파일을 업로드 `'./bar.pkl'` 계산 대상 데이터 저장소에서에서 `ds3` 스크립트를 실행 한 후

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>다음 단계

* [모델 학습](how-to-train-ml-models.md)

* [모델 배포](how-to-deploy-and-where.md)
