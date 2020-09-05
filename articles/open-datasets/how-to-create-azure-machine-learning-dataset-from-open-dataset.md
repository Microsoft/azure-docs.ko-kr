---
title: Azure Open 데이터 집합을 사용 하 여 데이터 집합 만들기
titleSuffix: Azure Open Datasets
description: Azure Open 데이터 집합에서 Azure Machine Learning 데이터 집합을 만드는 방법에 대해 알아봅니다.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 00718e15b069e9be25abdaf0e4460eae20c6afd0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998077"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Open 데이터 집합에서 Azure Machine Learning 데이터 집합 만들기
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) 데이터 집합 및 [Azure Open 데이터 집합](https://docs.microsoft.com/azure/open-datasets/)을 사용 하 여 큐 레이트 보강 데이터를 로컬 또는 원격 기계 학습 실험으로 가져오는 방법에 대해 알아봅니다. 

[Azure Machine Learning 데이터 집합](../machine-learning/how-to-create-register-datasets.md)을 만들어 메타 데이터의 복사본과 함께 데이터 원본 위치에 대 한 참조를 만듭니다. 데이터 집합은 지연 평가 되 고 데이터는 기존 위치에 남아 있기 때문에
* 추가 저장소 비용이 들지 않습니다.
* 실수로 원래 데이터 원본을 변경 하는 것은 위험 하지 않습니다. 
* ML 워크플로 성능 속도를 향상 시킵니다.

데이터 집합이 Azure Machine Learning의 전체 데이터 액세스 워크플로에 적합 한 위치를 이해 하려면 [안전 하 게 데이터 액세스](../machine-learning/concept-data.md#data-workflow) 문서를 참조 하세요.

Azure Open 데이터 집합은 예측 솔루션을 보강 하 고 정확성을 향상 시키기 위해 시나리오 관련 기능을 추가 하는 데 사용할 수 있는 공용 데이터 집합 큐 레이트. 기계 학습 모델을 학습 하는 데 도움이 되는 공용 [데이터 집합 카탈로그](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) 는 다음과 같은 방법으로 확인할 수 있습니다.

* [예보](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [인구 조사](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [휴일](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [공공 안전](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* 위치

개방형 데이터 집합은 Microsoft Azure의 클라우드에 있으며 [Azure Machine Learning PYTHON SDK](#create-datasets-with-the-sdk) 및 [Azure Machine Learning studio](#create-datasets-with-the-studio)에 모두 포함 되어 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 다음이 필요 합니다.

* Azure 구독 구독이 없으면 시작하기 전에 계정을 만드세요. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](../machine-learning/how-to-manage-workspace.md)입니다.

* 패키지를 포함 하는 [Python 용 AZURE MACHINE LEARNING SDK가 설치 되어](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) `azureml-datasets` 있습니다.

    * 통합 된 노트북 및 SDK가 이미 설치 되어 있는 완전히 구성 되 고 관리 되는 개발 환경인 [Azure Machine Learning 계산 인스턴스](../machine-learning/concept-compute-instance.md#managing-a-compute-instance)를 만듭니다.

    **OR**

    * 사용자 고유의 Python 환경에서 작업 하 고 [다음 지침](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)에 따라 SDK를 설치 합니다.

> [!NOTE]
> 일부 데이터 집합 클래스에는 64 비트 Python과만 호환 되는 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 패키지에 대 한 종속성이 있습니다. Linux 사용자의 경우 이러한 클래스는 Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) 및 CentOS (7) 배포판 에서만 지원 됩니다.

## <a name="create-datasets-with-the-sdk"></a>SDK를 사용 하 여 데이터 집합 만들기

Python SDK에서 Azure Open Dataset 클래스를 통해 Azure Machine Learning 데이터 집합을 만들려면를 사용 하 여 패키지를 설치 했는지 확인 `pip install azureml-opendatasets` 합니다. 각 불연속 데이터 집합은 SDK에서 자체 클래스로 표시 되며 특정 클래스는 Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)또는 둘 다로 사용할 수 있습니다. 클래스의 전체 목록은 [참조 설명서](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) 를 참조 하세요 `opendatasets` .

특정 `opendatasets` 클래스를 또는로 검색 하 여 `TabularDataset` 파일을 `FileDataset` 직접 조작 및/또는 다운로드할 수 있습니다. 다른 클래스는 **only** `get_tabular_dataset()` `get_file_dataset()` `Dataset` Python SDK의 클래스에서 또는 함수를 사용 하 여 데이터 집합을 가져올 수 있습니다.

다음 코드는 MNIST `opendatasets` 클래스가 또는를 반환할 수 있음을 보여 줍니다 `TabularDataset` `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

이 예제에서는 당뇨병 클래스를 `opendatasets` 으로만 사용할 수 `TabularDataset` 있으므로를 사용 합니다 `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>데이터 집합 등록

Azure Machine Learning 데이터 집합을 다른 사용자와 공유 하 고 작업 영역의 실험에서 다시 사용할 수 있도록 작업 영역에 등록 합니다. 개방형 데이터 집합에서 만든 Azure Machine Learning 데이터 집합을 등록 하면 데이터는 즉시 다운로드 되지 않지만 중앙 저장소 위치에서 요청 하는 경우 (예: 학습 중)에는 나중에 데이터에 액세스할 수 있습니다.

작업 영역을 사용 하 여 데이터 집합을 등록 하려면 메서드를 사용 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 합니다. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Studio를 사용 하 여 데이터 집합 만들기

또한 모든 기술 수준의 데이터 과학 전문가 데이터 과학 시나리오를 수행 하기 위한 기계 학습 도구를 포함 하는 통합 웹 인터페이스인 [Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 Azure Open 데이터 집합에서 Azure Machine Learning 데이터 집합을 만들 수 있습니다.

> [!Note]
> Azure Machine Learning studio를 통해 만든 데이터 집합은 작업 영역에 자동으로 등록 됩니다.

1. 작업 영역의 **자산**아래에서 **데이터 집합** 탭을 선택 합니다. **데이터 집합 만들기** 드롭다운 메뉴에서 **개방형 데이터 집합에서**를 선택 합니다.

    ![UI를 사용 하 여 데이터 집합 열기](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. 해당 타일을 선택 하 여 데이터 집합을 선택 합니다. 검색 창을 사용 하 여 필터링 할 수 있는 옵션이 있습니다. **다음**을 선택 합니다.

    ![데이터 집합 선택](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. 데이터 집합을 등록할 이름을 선택 하 고 필요에 따라 사용 가능한 필터를 사용 하 여 데이터를 필터링 합니다. 이 경우 **공용 휴일** 데이터 집합에 대해 기간을 1 년으로, 국가 코드를 미국 으로만 필터링 합니다. 필드 설명 및 날짜 범위와 같은 데이터 정보는 [Azure Open 데이터 집합 카탈로그](https://azure.microsoft.com/services/op[en-datasets/catalog/) 를 참조 하세요. **만들기**를 선택합니다.

    ![데이터 집합 매개 변수 설정 및 데이터 집합 만들기](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    이제 **데이터 집합의**작업 영역에서 데이터 집합을 사용할 수 있습니다. 만든 다른 데이터 집합과 동일한 방법으로 사용할 수 있습니다.


## <a name="access-datasets-for-your-experiments"></a>실험에 대 한 데이터 집합 액세스

기계 학습 실험에서 ML 모델 학습을 위해 데이터 집합을 사용 합니다. [데이터 세트를 사용하여 학습시키는 방법을 알아보세요](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>노트북 예제

개방형 데이터 집합 기능의 예제와 데모는 이러한 [샘플 노트북](samples.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [첫 번째 ML 모델을 학습](../machine-learning/tutorial-1st-experiment-sdk-train.md)합니다.

* [데이터 집합을 사용 하 여 학습](../machine-learning/how-to-train-with-datasets.md)합니다.

* [Azure machine learning 데이터 집합을 만듭니다](../machine-learning/how-to-create-register-datasets.md).



