---
title: NOAA 데이터를 사용하는 Jupyter Notebooks 예제
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에 대한 Jupyter Notebook 예제를 통해 공개 데이터 세트를 열어서 데모 데이터를 보강하는 데 사용하는 방법을 알아봅니다. 데이터를 처리하기 위해 Spark 및 Pandas 기술이 사용됩니다.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: c24363caac1db8dd8ce21b690ef989b2beb97f2d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506059"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Jupyter Notebook 예제에서는 Open Datasets로 데이터를 보강하는 방법을 보여줍니다. 
Azure Open Datasets에 대한 Jupyter Notebook 예제에서는 공개 데이터 세트를 로드하여 데모 데이터를 보강하는 방법을 보여줍니다. 데이터를 처리하기 위해 Spark 및 Pandas 기술이 사용됩니다.

>[!IMPORTANT]
>비 Spark 환경에서 작업하는 경우 Open Datasets는 대량의 데이터 세트와 관련된 MemoryError를 방지하기 위해 특정 클래스를 사용하여 한 번에 1개월 분량의 데이터만 다운로드할 수 있습니다.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA ISD(Integrated Surface Database) 데이터 로드 
|Notebook        | Description                                    |
|----------------|------------------------------------------------|
|[최근 한 달의 날씨 데이터를 Pandas 데이터 프레임에 로드](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | 자주 사용하는 Pandas 데이터 프레임에 과거의 날씨 데이터를 로드하는 방법을 알아봅니다. |
|[최근 한 달의 날씨 데이터를 Spark 데이터 프레임에 로드](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | 자주 사용하는 Spark 데이터 프레임에 과거의 날씨 데이터를 로드하는 방법을 알아봅니다.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>데모 데이터를 NOAA ISD 데이터와 조인 
|Notebook        | Description                                    |
|----------------|------------------------------------------------|
|[데모 데이터를 날씨 데이터와 조인 - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Pandas 데이터 프레임에서 1개월 분량의 센서 위치 데모 데이터 세트를 날씨 판독값과 조인합니다.  |
|[데모 데이터를 날씨 데이터와 조인 - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Spark 데이터 프레임에서 센서 위치 데모 데이터 세트를 날씨 판독값과 조인합니다. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>NYC 택시 데이터를 NOAA ISD 데이터와 조인 
|Notebook        | Description                                    |
|----------------|------------------------------------------------|
|[택시 주행 데이터를 날씨 데이터로 보강 - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Pandas 데이터 프레임에서 1개월을 초과하는 NYC 그린 택시 데이터를 로드한 후 날씨 데이터로 보강합니다. 이 예제에서는 `get_pandas_limit` 메서드를 재정의하고 데이터 로드 성능과 데이터 양 사이의 균형을 적절하게 조정합니다.|
|[택시 주행 데이터를 날씨 데이터로 보강 - Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Spark 데이터 프레임에서 NYC 그린 택시 데이터를 로드한 후 날씨 데이터로 보강합니다.  |

## <a name="next-steps"></a>다음 단계

* [자습서: 자동화된 기계 학습 및 공개 데이터 세트를 사용하여 회귀 모델 만들기](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [공개 데이터 세트용 Python SDK](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure Open Datasets 카탈로그](https://azure.microsoft.com/services/open-datasets/catalog/)
* [공개 데이터 세트에서 Azure Machine Learning 데이터 세트 만들기](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)