---
title: Python 클라이언트 라이브러리를 사용하여 데이터 세트에 액세스 - Team Data Science Process
description: Python 클라이언트 라이브러리를 설치하고 사용하여 로컬 Python 환경에서 안전하게 Azure Machine Learning 데이터에 액세스하고 관리합니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b773241fdff41323272422f99e88f0d01e884055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889555"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Azure Machine Learning Python 클라이언트 라이브러리를 사용하여 Python으로 데이터 세트에 액세스
Microsoft Azure Machine Learning Python 클라이언트 라이브러리 미리보기를 사용하면 로컬 Python 환경에서 Azure Machine Learning 데이터 세트로 안전하게 액세스할 수 있고, 작업 영역에 데이터 세트를 생성하여 관리할 수 있습니다.

이 항목에서는 다음 수행 방법에 대한 지침을 제공합니다.

* Machine Learning Python 클라이언트 라이브러리 설치
* 로컬 Python 환경에서 Azure Machine Learning 데이터 세트에 액세스하는 권한을 얻는 방법에 대한 지침을 비롯하여 데이터 세트에 액세스 및 업로드
* 실험에서 중간 데이터 세트에 액세스
* Python 클라이언트 라이브러리를 사용한 데이터 세트 열거, 메타데이터 액세스, 데이터 세트 내용 읽기, 새 데이터 세트 만들기, 기존 데이터 세트 업데이트

## <a name="prerequisites"></a><a name="prerequisites"></a>필수 조건
Python 클라이언트 라이브러리는 다음과 같은 환경에서 테스트되었습니다.

* Windows, Mac 및 Linux
* Python 2.7 및 3.6 이상

다음 패키지에 대한 종속성이 있습니다.

* requests
* python-dateutil
* pandas

Python, IPython 및 설치된 것으로 위에 나열된 세 가지 패키지와 함께 제공되는 [Anaconda](https://www.anaconda.com/) 또는 [Canopy](https://store.enthought.com/downloads/) 등의 Python 배포판을 사용하는 것이 좋습니다. IPython은 반드시 필요하지는 않지만 데이터를 대화식으로 조작하고 시각화는 데 훌륭한 환경입니다.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Azure 기계 학습 Python 클라이언트 라이브러리를 설치하는 방법
이 항목에 설명되어 있는 작업을 완료하려면 Azure Machine Learning Python 클라이언트 라이브러리를 설치해야 합니다. 해당 라이브러리는 [Python 패키지 인덱스](https://pypi.python.org/pypi/azureml)에 있습니다. Python 환경에 설치하려면 로컬 Python 환경에서 다음 명령을 실행합니다.

```console
pip install azureml
```

또는 [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)의 원본에서 다운로드하고 설치할 수 있습니다.

```console
python setup.py install
```

컴퓨터에 git가 설치된 경우 pip를 사용하여 git 리포지토리에서 직접 설치할 수 있습니다.

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>코드 조각을 사용하여 데이터 세트에 액세스
Python 클라이언트 라이브러리를 사용하면 실행된 기존 데이터 세트에 프로그래밍 방식으로 액세스할 수 있습니다.

Azure Machine Learning Studio(클래식) 웹 인터페이스에서 필요한 모든 정보를 포함하는 코드 조각을 생성하여 로컬 컴퓨터에 Pandas DataFrame 개체로 데이터 세트를 다운로드하고 역직렬화할 수 있습니다.

### <a name="security-for-data-access"></a><a name="security"></a>데이터 액세스를 위한 보안
Azure Machine Learning Studio(클래식)에서 Python 클라이언트 라이브러리와 함께 사용하도록 제공하는 코드 조각에는 작업 영역 ID와 권한 부여 토큰이 포함되어 있습니다. 이러한 코드 조각은 작업 영역에 대한 전체 액세스 권한을 제공하고 암호와 같이 보호되어야 합니다.

보안상의 이유로 코드 조각은 이전에 역할이 작업 영역의 **소유자** 로 설정된 사용자만 사용할 수 있습니다. 사용자의 역할은 Azure Machine Learning Studio(클래식)의 **설정** 에서 **사용자** 페이지에 표시됩니다.

![Azure Machine Learning Studio의 사용자 페이지에 있는 설정을 보여주는 스크린샷입니다.][security]

역할이 **소유자** 로 설정되지 않은 경우 소유자로 다시 초대되도록 요청하거나 작업 영역의 소유자에게 코드 조각을 제공하도록 요청할 수 있습니다.

권한 부여 토큰을 가져오기 위해 다음 옵션 중 하나를 선택할 수 있습니다.

* 소유자에서 토큰을 요청합니다. 소유자는 Azure Machine Learning Studio(클래식)의 작업 영역에 있는 설정 페이지에서 권한 부여 토큰에 액세스할 수 있습니다. 왼쪽 창에서 **설정** 을 선택하고 **권한 부여 토큰** 을 클릭하여 기본 및 보조 토큰을 확인할 수 있습니다. 기본 또는 보조 권한 부여 토큰을 코드 조각에서 사용할 수 있지만, 소유자가 보조 권한 부여 토크만 공유하는 것이 좋습니다.

   ![권한 부여 토큰](./media/python-data-access/ml-python-access-settings-tokens.png)

* 소유자의 역할로 승격할 것을 요청합니다: 작업 영역의 현재 소유자가 먼저 작업 영역에서 사용자를 제거한 다음 소유자로 다시 초대해야 합니다.

개발자가 작업 영역 ID와 권한 부여 토큰을 획득하고 나면 역할에 상관 없이 코드 조각을 사용하여 작업 영역에 액세스할 수 있습니다.

권한 부여 토큰은 **설정** 의 **권한 부여 토큰** 페이지에서 관리합니다. 토큰을 다시 생성할 수 있지만 이 절차를 수행하면 이전 토큰에 대한 액세스 권한이 취소됩니다.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>로컬 Python 애플리케이션에서 데이터 세트에 액세스
1. Machine Learning Studio(클래식)에서 왼쪽에 있는 탐색 모음으로 이동해 **데이터 세트** 를 클릭합니다.
2. 액세스하려는 데이터 세트를 선택합니다. **내 데이터 세트** 목록 또는 **샘플** 목록에서 데이터 세트를 선택할 수 있습니다.
3. 아래 쪽 도구 모음에서 **데이터 액세스 코드 생성** 을 클릭합니다. 데이터가 Python 클라이언트 라이브러리와 호환되지 않는 형식이면 이 단추는 비활성화됩니다.
   
    ![데이터 액세스 코드 생성을 포함하는 데이트 세트를 보여주는 스크린샷입니다.][datasets]
4. 표시되는 창에서 코드 조각을 선택하여 클립보드에 복사합니다.
   
    ![데이터 액세스 코드 생성 단추][dataset-access-code]
5. 코드를 로컬 Python 애플리케이션의 Notebook에 붙여넣습니다.
   
    ![Notebook에 코드 붙여넣기][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>기계 학습 실험에서 중간 데이터 세트에 액세스
Machine Learning Studio(클래식)에서 실험을 실행하고 나면 모듈의 출력 노드에서 중간 데이터 세트에 액세스할 수 있습니다. 중간 데이터 세트는 모델 도구가 실행될 때 중간 단계를 위해 생성되고 사용된 데이터입니다.

데이터 형식이 Python 클라이언트 라이브러리와 호환되는 한 중간 데이터 세트에 액세스할 수 있습니다.

지원되는 형식은 다음과 같습니다(해당 형식의 상수는 `azureml.DataTypeIds` 클래스에 있음).

* 일반 텍스트
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

모듈 출력 노드 위에 포인터를 두면 형식을 확인할 수 있습니다. 도구 설명에 노드 이름과 함께 표시됩니다.

[분할][split] 모듈과 같은 일부 모듈은 Python 클라이언트 라이브러리에서 지원하지 않는 `Dataset`라는 형식으로 출력됩니다.

![데이터 세트 형식][dataset-format]

[CSV로 변환][convert-to-csv]과 같은 변환 모듈을 사용하여 출력을 지원되는 형식으로 가져와야 합니다.

![GenericCSV 형식][csv-format]

다음 단계에서는 실험을 만들고 실행하며 중간 데이터 세트에 액세스하는 예를 보여줍니다.

1. 새로운 실험 만들기
2. **성인 인구 조사 소득 이진 분류 데이터 세트** 모듈을 삽입합니다.
3. [분할][split] 모듈을 삽입하고 입력을 데이터 세트 모듈 출력에 연결합니다.
4. [CSV로 변환][convert-to-csv] 모듈을 삽입하고 입력을 [분할][split] 모듈 출력 중 하나에 연결합니다.
5. 실험을 저장하고 실행하여 작업이 완료될 때까지 기다립니다.
6. [CSV로 변환][convert-to-csv] 모듈에서 출력 노드를 클릭합니다.
7. 상황에 맞는 메뉴가 표시되면 **데이터 액세스 코드 생성** 을 선택합니다.
   
    ![상황에 맞는 메뉴][experiment]
8. 표시되는 창에서 코드 조각을 선택하여 클립보드에 복사합니다.
   
    ![바로 가기 메뉴에서 액세스 코드 생성][intermediate-dataset-access-code]
9. 노트북에 코드를 붙여넣습니다.
   
    ![Notebook에 코드 붙여넣기][ipython-intermediate-dataset]
10. Matplotlib를 사용하여 데이터를 시각화할 수 있습니다. 그러면 나이 열에 대한 히스토그램에 표시됩니다.
    
    ![히스토그램][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>기계 학습 Python 클라이언트 라이브러리를 사용하여 데이터 세트에 액세스, 읽기, 만들기 및 관리
### <a name="workspace"></a>작업 영역
작업 영역은 Python 클라이언트 라이브러리의 진입점입니다. `Workspace` 클래스에 인스턴스를 생성할 작업 영역 ID와 권한 부여 토큰을 제공합니다.

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>데이터 세트 열거
지정된 작업 영역에 모든 데이터 세트 열거:

```python
for ds in ws.datasets:
    print(ds.name)
```

사용자 생성 데이터 세트만 열거:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

예제 데이터 세트만 열거:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

데이터 세트 이름(대/소문자 구분)으로 액세스할 수 있습니다.

```python
ds = ws.datasets['my dataset name']
```

또는 인덱스별로 액세스할 수 있습니다.

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>메타데이터
데이터 세트에는 콘텐츠 외에도 메타데이터가 있습니다. (중간 데이터 세트는 이 규칙의 예외로서, 메타데이터가 없습니다.)

일부 메타 데이터 값은 사용자가 생성 시 할당:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

다른 메타 데이터 값은 Azure 기계 학습에서 할당:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

사용 가능한 메타 데이터에 대한 자세한 내용은 `SourceDataset` 클래스를 참조하세요.

### <a name="read-contents"></a>콘텐츠 읽기
Machine Learning Studio(클래식)에서 제공하는 코드 조각은 데이터 세트를 Pandas DataFrame 개체에 자동으로 다운로드하고 역직렬화합니다. 이 작업은 `to_dataframe` 메서드를 통해 수행됩니다.

```python
frame = ds.to_dataframe()
```

원시 데이터를 다운로드하고 직접 역직렬화를 수행하려는 경우 옵션으로 선택할 수 있습니다. 현재로서는 Python 클라이언트 라이브러리에서 역직렬화할 수 없는 ‘ARFF’와 같은 형식의 경우에만 선택 가능합니다.

텍스트로 콘텐츠 읽기:

```python
text_data = ds.read_as_text()
```

이진을 콘텐츠 읽기:

```python
binary_data = ds.read_as_binary()
```

콘텐츠에 스트림만 열 수도 있음:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>새 데이터 세트 만들기
Python 클라이언트 라이브러리를 사용하면 Python 프로그램에서 데이터 세트를 업로드할 수 있습니다. 이러한 데이터 세트는 작업 영역에서 사용할 수 있습니다.

Pandas DataFrame에 데이터가 있는 경우 다음 코드를 사용합니다.

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

데이터가 이미 직렬화된 경우 다음을 사용할 수 있습니다.

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Python 클라이언트 라이브러리에서 Pandas DataFrame을 다음 형식으로 직렬화할 수 있습니다(해당 상수는 `azureml.DataTypeIds` 클래스에 있음).

* 일반 텍스트
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>기존 데이터 세트 업데이트
기존 데이터 세트와 일치하는 이름으로 새 데이터 세트를 업로드하려고 하면 충돌 오류가 발생합니다.

기존 데이터 세트를 업데이트하려면 먼저 기존 데이터 세트에 대한 참조를 얻어야 합니다.

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

그러면 `update_from_dataframe` 을 사용하여 Azure에서 데이터 세트의 콘텐츠를 직렬화하고 바꿀 수 있습니다.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

데이터를 다른 형식으로 직렬화하려면 선택적 `data_type_id` 매개 변수의 값을 지정합니다.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

`description` 매개 변수의 값을 지정하여 선택적으로 새 설명을 설정할 수 있습니다.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

`name` 매개 변수의 값을 지정하여 선택적으로 새로운 이름을 설정할 수 있습니다. 지금부터 새 이름만 사용하여 데이터 세트를 검색합니다. 아래의 코드로 데이터, 이름 및 설명을 업데이트합니다.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id`, `name` 및 `description` 매개 변수는 선택 사항이고 기본적으로 이전 값이 됩니다. `dataframe` 매개 변수는 항상 필요합니다.

데이터가 이미 직렬화된 경우 `update_from_dataframe` 대신 `update_from_raw_data`를 사용합니다. `dataframe` 대신 `raw_data`만 전달하면, 비슷하게 작동합니다.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data