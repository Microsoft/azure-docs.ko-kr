---
title: '쓰기: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK를 사용하여 데이터를 쓰는 방법을 알아봅니다. 데이터 흐름의 모든 지점에서, 지원되는 위치(로컬 파일 시스템, Azure Blob Storage 및 Azure Data Lake Storage)의 파일에 데이터를 쓸 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 92f04d80ea956f3036d7778a5d6de62e53b969ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817362"
---
# <a name="write-and-configure-data-using-azure-machine-learning"></a>작성 하 고 Azure Machine Learning을 사용 하 여 데이터를 구성 합니다.

이 문서에서는 다양 한 방법을 사용 하 여 데이터를 쓸 알아봅니다 합니다 [Azure Machine Learning 데이터 준비 Python SDK](https://aka.ms/data-prep-sdk) 및 실험에 대 한 데이터를 구성 하는 방법의 [Python용AzureMachineLearningSDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  언제 든 지 데이터 흐름의 출력 데이터를 작성할 수 있습니다. 쓰기 결과 데이터 흐름에는 단계와 다음이 단계 실행 될 때마다 데이터 흐름 실행에 추가 됩니다. 데이터는 병렬 쓰기가 가능하도록 여러 파티션 파일에 기록됩니다.

파이프라인에 포함되는 쓰기 단계의 수에는 제한이 없으므로 문제 해결 또는 다른 파이프라인에 대한 중간 결과를 얻기 위해 추가 쓰기 단계를 쉽게 추가할 수 있습니다.

쓰기 단계를 실행할 때마다 데이터 흐름에서 데이터를 완전하게 가져옵니다. 예를 들어 쓰기 단계가 3개 있는 데이터 흐름은 데이터 세트의 레코드를 세 번 읽고 처리합니다.

## <a name="supported-data-types-and-location"></a>지원되는 데이터 형식 및 위치

지원되는 파일 형식은 다음과 같습니다.
-   구분 기호로 분리된 파일(CSV, TSV 등)
-   Parquet 파일

Azure Machine Learning 데이터 준비 Python SDK를 사용 하 여 데이터를 작성할 수 있습니다.
+ 로컬 파일 시스템
+ Azure Blob Storage
+ Azure Data Lake 스토리지

## <a name="spark-considerations"></a>Spark 고려 사항

Spark에서 데이터 흐름을 실행하는 경우 빈 폴더에 써야 합니다. 기존 폴더에 쓰기를 실행하려고 시도하면 오류가 발생합니다. 대상 폴더가 비어 있거나 실행마다 다른 대상 위치를 사용해야 합니다. 그렇지 않으면 쓰기가 실패합니다.

## <a name="monitoring-write-operations"></a>쓰기 작업 모니터링

편의를 위해 쓰기가 완료되면 SUCCESS라는 sentinel 파일이 생성됩니다. 이렇게 하면 전체 파이프라인이 완료될 때까지 기다리지 않고 중간 쓰기가 완료된 시점을 확인할 수 있습니다.

## <a name="example-write-code"></a>쓰기 코드 예제

예를 들어 사용 하 여 데이터 흐름에 데이터를 로드 하 여 시작 `auto_read_file()`합니다. 이 데이터를 다른 형식으로 재사용합니다.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

예제 출력:

| | 열1 | 열2 | 열3 | 열4 | 열5 | 열6 | 열7 | 열8 | 열9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | 없음 | 아니요 | 아니요 | ENRS | NaN | NaN | NaN |   
|1| 10003.0 | 99999.0 | 없음 | 아니요 | 아니요 | ENSO | NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | 없음 | 아니요 | JN | ENJA | 70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | 없음 | 아니요 | 아니요 |      | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | 없음 | 아니요 | 아니요 | ENSO | 59783.0 | 5350.0 |  500.0|

### <a name="delimited-file-example"></a>구분 기호로 분리된 파일 예제

다음 코드에서는 합니다 [ `write_to_csv()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-csv-directory-path--destinationpath--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow) 구분 기호로 분리 된 파일로 데이터를 작성 하는 함수입니다.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

예제 출력:

| | 열1 | 열2 | 열3 | 열4 | 열5 | 열6 | 열7 | 열8 | 열9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | 오류 | 아니요 | 아니요 | ENRS | NaN    | NaN | NaN |   
|1| 10003.0 | 99999.0 | 오류 | 아니요 | 아니요 | ENSO |    NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | 오류 | 아니요 | JN | ENJA |    70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | 오류 | 아니요 | 아니요 |     | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | 오류 | 아니요 | 아니요 | ENSO |    59783.0 | 5350.0 |  500.0|

앞의 출력에서 올바르게 구문 분석되지 않은 숫자로 인해 숫자 열에 몇 가지 오류가 나타나는 것을 볼 수 있습니다. CSV로 쓸 때 null 값은 기본적으로 "ERROR" 문자열로 대체됩니다.

쓰기 호출의 일부로 매개 변수를 추가하고 null 값을 나타내는 데 사용할 문자열을 지정합니다.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

앞의 코드에서 생성하는 출력은 다음과 같습니다.

| | 열1 | 열2 | 열3 | 열4 | 열5 | 열6 | 열7 | 열8 | 열9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000.0 | 99999.0 | BadData | 아니요 | 아니요 | ENRS | NaN  | NaN | NaN |   
|1| 10003.0 | 99999.0 | BadData | 아니요 | 아니요 | ENSO |  NaN | NaN | NaN |   
|2| 10010.0 | 99999.0 | BadData | 아니요 | JN | ENJA |  70933.0 | -8667.0 | 90.0 |
|3| 10013.0 | 99999.0 | BadData | 아니요 | 아니요 |   | NaN | NaN | NaN |
|4| 10014.0 | 99999.0 | BadData | 아니요 | 아니요 | ENSO |  59783.0 | 5350.0 |  500.0|

### <a name="parquet-file-example"></a>Parquet 파일 예제

비슷합니다 `write_to_csv()`는 [ `write_to_parquet()` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#write-to-parquet-file-path--typing-union--destinationpath--nonetype----none--directory-path--typing-union--destinationpath--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow) 쓰기 데이터 흐름 실행 시 실행 되는 Parquet 단계를 사용 하 여 새 데이터 흐름을 반환 합니다.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

데이터 흐름을 실행하여 쓰기 작업을 시작합니다.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

앞의 코드에서 생성하는 출력은 다음과 같습니다.

|   | 열1 | 열2 | 열3 | 열4 | 열5 | 열6 | 열7 | 열8 | 열9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000.0 | 99999.0 | MiscreantData | 아니요 | 아니요 | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003.0 | 99999.0 | MiscreantData | 아니요 | 아니요 | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010.0 | 99999.0 | MiscreantData | 아니요| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0 | 99999.0 | MiscreantData | 아니요| 아니요| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0 | 99999.0 | MiscreantData | 아니요| 아니요| ENSO|   59783.0|    5350.0| 500.0|

## <a name="configure-data-for-automated-machine-learning-training"></a>자동화 된 machine learning 학습에 대 한 데이터를 구성 합니다.

새로 작성 된 데이터 파일을로 전달 된 [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig) 자동화 된 machine learning 학습을 위한 준비 과정에서 개체입니다. 

다음 코드 예제에서는 Pandas 데이터 프레임에 데이터 흐름 변환 이후에, 자동화 된 machine learning 학습에 학습 및 테스트 데이터 집합으로 분할 하는 방법을 보여 줍니다.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

앞의 예제에서와 같이 모든 중간 데이터 준비 단계는 필요 하지 않은 경우 데이터 흐름에 직접 전달할 수 있습니다 `AutoMLConfig`합니다.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>다음 단계
* SDK를 참조 하세요 [개요](https://aka.ms/data-prep-sdk) 디자인 패턴 및 사용 예제 
* 자동화 된 기계 학습을 참조 하세요 [자습서](tutorial-auto-train-models.md) 회귀 모델 예제