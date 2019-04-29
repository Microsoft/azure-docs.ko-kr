---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 및 기계 학습 및 데이터 준비 Python SDK에 대한 최신 업데이트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: e79e610638b5ff271607da2e8855003707ea5932
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821302"
---
# <a name="azure-machine-learning-service-release-notes"></a>Azure Machine Learning 서비스의 릴리스 정보

이 문서에서는 Azure Machine Learning 서비스의 릴리스에 대해 알아봅니다.  각 SDK에 대한 전체 설명은 다음 참조 문서를 참조하세요.
+ Azure Machine Learning의 [**Python용 기본 SDK**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [**Data Prep SDK**](https://aka.ms/data-prep-sdk)


## <a name="2019-04-22"></a>2019-04-22

### <a name="azure-machine-learning-sdk-for-python-v1030"></a>Azure Machine Learning Python v1.0.30 for SDK

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning 데이터 준비 SDK v1.1.2

참고: 데이터 준비 Python SDK는 더 이상 설치할 `numpy` 고 `pandas` 패키지 없습니다. 참조 [설치 지침을 업데이트](https://aka.ms/aml-data-prep-installation)합니다.

+ **새로운 기능**
  + 이제 피벗 변환을 사용할 수 있습니다.
    + 방법 가이드: [피벗 notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + 이제 네이티브 함수에서 정규식을 사용할 수 있습니다.
    + 예제:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + 이제 사용할 수 있습니다 `to_upper`  하 고 `to_lower`  식 언어의 함수입니다.
  + 이제 데이터 프로필에 있는 각 열의 고유 값 개수를 볼 수 있습니다.
  + 자주 사용 되는 판독기 단계 중 일부를 전달할 수 있습니다 이제는 `infer_column_types` 인수입니다. 로 설정 된 경우 `True`, 데이터 준비를 감지 하 여 자동 열 유형으로 변환 하려고 합니다.
    + `inference_arguments` 이제 사용 되지 않습니다.
  + 이제 호출할 수 있습니다 `Dataflow.shape`합니다.

+ **버그 수정 및 향상 된 기능**
  + `keep_columns` 이제 추가 선택적 인수를 허용 `validate_column_exists`를 확인 하는 경우의 결과 `keep_columns` 열이 포함 됩니다.
  + 모든 판독기 단계 (파일에서 읽기)을 추가 하는 선택적 인수를 허용 `verify_exists`합니다.
  + Pandas 데이터 프레임에서 읽고 데이터 프로필 가져오기 성능이 향상 되었습니다.
  + 단일 인덱스를 사용 하 여 조각화를 데이터 흐름에서 한 번 실패 한 버그가 수정 되었습니다.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
+ **새로운 기능**
  + 이제 기존 원격 계산 클러스터에서 실행할 기존 스크립트를 다시 제출할 수 있습니다. 
  + 이제 새 매개 변수를 사용 하 여 파이프라인 탭에서 게시 된 파이프라인을 실행할 수 있습니다. 
  + 실행된 세부 정보는 이제 새 스냅숏 파일 뷰어를 지원 합니다. 특정 실행을 제출할 때 디렉터리의 스냅숏을 볼 수 있습니다. 또한 실행을 시작 하려면에 전송 된 notebook을 다운로드할 수 있습니다.
   + 이제 Azure Portal에서 부모 실행을 취소할 수 있습니다.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning Python v1.0.23 for SDK

+ **새로운 기능**
  + Azure Machine Learning SDK는 이제 Python 3.7을 지원합니다.
  + Azure Machine Learning DNN 추정은 이제 기본 제공 다중 버전 지원을 제공합니다. 예를 들어 `TensorFlow`  평가기 받으므로 `framework_version` 매개 변수 및 사용자 버전 '1.10' 또는 '1.12'를 지정할 수 있습니다. 에서 현재 사용 중인 SDK 버전에서 지원 되는 버전 목록은 호출 `get_supported_versions()` 원하는 프레임 워크 클래스 (예: `TensorFlow.get_supported_versions()`).
  최신 SDK 버전에서 지원 되는 버전 목록은 참조 하세요. 합니다 [DNN 스 티 메이 터 설명서](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)합니다.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.1

+ **새로운 기능**
  + Read_ 변환을 사용 하 여 여러 데이터 저장소/데이터 경로/DataReference 소스를 읽을 수 있습니다.
  + 새 열을 만들 열에서 다음 작업을 수행할 수 있습니다: 나누기, 모듈로, power, floor, 길이입니다.
  + 데이터 준비 이제 Azure ML 진단 제품군의 일부 이며 기본적으로 진단 정보를 기록 합니다.
    + 이 해제 하려면이 환경 변수를 true로 설정 합니다. DISABLE_DPREP_LOGGER

+ **버그 수정 및 향상 된 기능**
  + 일반적으로 사용 되는 클래스 및 함수에 대 한 향상 된 코드 설명서입니다.
  + Excel 파일을 읽지 못했습니다.는 auto_read_file 버그를 수정 했습니다.
  + Read_pandas_dataframe에서 폴더를 덮어쓸 수 추가 옵션입니다.
  + Dotnetcore2 종속성 설치 및 Fedora 27/28 및 Ubuntu 1804 지원 추가 됨된의 성능이 향상 되었습니다.
  + Azure Blob에서 읽기 성능이 향상 되었습니다.
  + 열 유형 검색 이제는 Long 형식의 열을 지원합니다.
  + 일부 날짜 값 Python datetime 개체 대신 타임 스탬프로 표시 되 고 된 되는 버그가 수정 되었습니다.
  + 정수 대신 double로 일부 형식 개수 표시 되 고 되었습니다 있는 버그가 수정 되었습니다.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning Python v1.0.21 for SDK

+ **새로운 기능**
  + 합니다 *azureml.core.Run.create_children* 메서드를 사용 하면 단일 호출으로 실행 되는 여러 자식 지연율이 낮은 생성 합니다.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning 데이터 준비 SDK v1.1.0

+ **주요 변경 내용**
  + 데이터 준비 패키지의 개념에 사용 되지 않으며 더 이상 지원 되지. 하나의 패키지로 여러 데이터 흐름이 유지 하지 않고 데이터 흐름을 개별적으로 유지할 수 있습니다.
    + 방법 가이드: [노트북을 열고 저장 하는 데이터 흐름](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **새로운 기능**
  + 이제 데이터 준비가 특정 의미 체계 형식에 일치 하 고 적절 하 게 분할 하는 열을 인식 합니다. 현재 지원 STypes 포함: 전자 메일 주소, 지리적 좌표 (위도 및 경도), IPv4 및 IPv6 주소, 미국 전화 번호 및 우편 번호입니다.
    + 방법 가이드: [의미 체계 형식 notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + 데이터 준비에는 이제 두 개의 숫자 열에서 결과 열을 생성 하 여 다음 작업: 빼기, 곱하기, 나누기, 모듈로 연산을 수행 합니다.
  + 호출할 수 있습니다 `verify_has_data()` 실행 하는 경우 데이터 흐름에서 레코드를 생성할 것 여부를 확인 하려면 데이터 흐름에서.

+ **버그 수정 및 향상 된 기능**
  + 이제 숫자 열 프로필에 대 한 히스토그램에 사용할 bin의 수를 지정할 수 있습니다.
  + `read_pandas_dataframe` 변환 하도록 문자열-데이터 프레임에서 이제 요구 바이트 형식의 열 이름 또는 합니다.
  + 버그가 수정 되었습니다는 `fill_nulls` 변환, 여기서 값 제대로 채워지지 않은 경우에는 열이 없습니다.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning Python v1.0.18 for SDK

 + **변경된 기능**
   + Azureml tensorboard 패키지 azureml-contrib-tensorboard를 대체합니다.
   + 이 릴리스를 사용 하 여 관리 되는 계산 클러스터 (amlcompute)에서 사용자 계정을 만드는 동안 설정할 수 있습니다. 이 단순히 프로 비전 구성에서 이러한 속성을 전달 하 여 수행할 수 있습니다. 자세한 내용을 찾을 수 있습니다 합니다 [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-)합니다.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 데이터 준비 SDK v1.0.17

+ **새로운 기능**
  + 이제 식 언어를 사용 하 여 결과 열을 생성 하는 두 개의 숫자 열 추가 지원 합니다.

+ **버그 수정 및 향상 된 기능**
  + 향상 된 설명서 및 매개 변수 random_split 확인 합니다.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 데이터 준비 SDK v1.0.16

+ **버그 수정**
  + 서비스 주체 인증 문제가 발생 하는 고정 API 변경으로 합니다.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning Python v1.0.17 for SDK

+ **새로운 기능**

  + Azure Machine Learning은 이제 인기 있는 DNN 프레임 워크 Chainer에 대 한 최고 수준의 지원을 제공합니다. 사용 하 여 [ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 클래스 사용자가 쉽게 학습를 Chainer 모델을 배포 합니다.
    + 자세한 방법 [ChainerMN를 사용 하 여 분산된 교육 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + 자세한 방법 [HyperDrive를 사용 하 여 Chainer를 사용 하 여 하이퍼 매개 변수 튜닝 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning 파이프라인 파이프라인 실행 데이터 저장소, 수정을 기반으로 하는 기능 트리거를 추가 합니다. 파이프라인 [일정 notebook](https://aka.ms/pl-schedule) 이 기능을 소개 하도록 업데이트 됩니다.

+ **버그 수정 및 향상 된 기능**
  + 지원이 추가 되었습니다 Azure Machine Learning 파이프라인 (예: blob storage) 원하는 데이터 저장소로 source_directory_data_store 속성을 설정 하는 것에 대 한 온 [RunConfigurations](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 에 제공 되는 여 [ PythonScriptStep](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)합니다. 기본적으로 단계는 많은 수의 단계를 동시에 실행할 때 제한 문제를 겪을 수 있는 백업 데이터 저장소로 Azure 파일 저장소를 사용 합니다.

### <a name="azure-portal"></a>Azure portal

+ **새로운 기능**
  + 새 끌어서 테이블 보고서에 대 한 편집기 환경입니다. 사용자는 테이블의 미리 보기를 표시할 테이블 영역에 웰에서 열을 끌어 수 있습니다. 열을 다시 배열할 수 있습니다.
  + 새 로그 파일 뷰어
  + 실험 실행, 계산, 모델, 이미지 및 작업 탭에서 배포에 대 한 링크

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning 데이터 준비 SDK v1.0.15

+ **새로운 기능**
  + 데이터 흐름에서 파일 스트림을 쓰는 데이터 준비 이제 지원 합니다. 또한 새 파일 이름을 만들기 위해 파일 스트림을 이름을 조작 하는 기능을 제공 합니다.
    + 방법 가이드: [파일 스트림 작업 notebook](https://aka.ms/aml-data-prep-file-stream-nb)

+ **버그 수정 및 향상 된 기능**
  + T-digest 큰 데이터 집합의 성능이 향상 되었습니다.
  + 이제 데이터 준비는 데이터 경로에서 데이터 읽기를 지원합니다.
  + 이제 핫 인코딩이 부울 및 숫자 열에서 작동합니다.
  + 다른 기타 버그가 수정 되었습니다.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Python용 Azure Machine Learning SDK v1.0.15

+ **새로운 기능**
  + Azure Machine Learning Pipelines에 AzureBatchStep([Notebook](https://aka.ms/pl-azbatch)), HyperDriveStep([Notebook](https://aka.ms/pl-hyperdrive)) 및 시간 기반 일정 예약 기능([Notebook](https://aka.ms/pl-schedule))이 추가되었습니다.
  +  DataTranferStep이 Azure SQL Server 및 Azure Database for PostgreSQL에서 작동하도록 업데이트되었습니다([Notebook](https://aka.ms/pl-data-trans)).

+ **변경된 기능**
  + `PublishedPipeline.get_published_pipeline`이 사용되지 않는 대신 `PublishedPipeline.get`이 사용됩니다.
  + `Schedule.get_schedule`이 사용되지 않는 대신 `Schedule.get`이 사용됩니다.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning 데이터 준비 SDK v1.0.12

+ **새로운 기능**
  + 데이터 준비 SDK는 이제 데이터 저장소를 사용하여 Azure SQL 데이터베이스에서 읽을 수 있도록 지원합니다.
 
+ **변경된 기능**
  + 대용량 데이터에서 특정 작업의 메모리 성능이 크게 향상되었습니다.
  + `read_pandas_dataframe()`에는 이제 `temp_folder`를 지정해야 합니다.
  + `ColumnProfile`의 `name` 속성은 더 이상 사용되지 않습니다. 대신 `column_name`을 사용합니다.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Python용 Azure Machine Learning SDK v1.0.10

+ **변경 내용**: 
  + Azure ML SDK에는 azure-cli 패키지가 더 이상 종속성으로 포함되지 않습니다. 특히 azure-cli-core 및 azure-cli-profile 종속성이 azureml-core에서 제거되었습니다. 사용자에게 영향을 미치는 변경 내용은 다음과 같습니다.
    + “az login”을 수행한 후 azureml-sdk를 사용하는 경우 SDK는 브라우저 또는 디바이스 코드 로그인을 한 번 더 수행합니다. “az login”에서 생성된 자격 증명 상태는 사용되지 않습니다.
    + “az login”을 사용하는 것과 같은 Azure CLI 인증의 경우 _azureml.core.authentication.AzureCliAuthentication_ 클래스를 사용합니다. Azure CLI 인증의 경우 azureml-sdk를 설치한 Python 환경에서 _pip install azure-cli_를 수행합니다.
    + 자동화를 위해 서비스 주체를 사용하여 “az login”을 수행하는 경우에는 Azure CLI에서 생성된 _azureml.core.authentication.ServicePrincipalAuthentication_ 클래스를 사용하는 것이 좋습니다. azureml-sdk는 azure CLI에서 만든 자격 증명 상태를 사용하지 않기 때문입니다. 

+ **버그 수정**: 이 릴리스는 대부분 사소한 버그 수정을 포함합니다.

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **버그 수정**
  + 데이터 프로필 가져오기 성능을 크게 개선했습니다.
  + 오류 보고와 관련한 사소한 버그를 수정했습니다.
  
### <a name="azure-portal-new-features"></a>Azure Portal: 새로운 기능
+ 보고서에 적용할 수 있는 새로운 끌어서 놓기 차트 환경. 사용자가 웰의 열 또는 특성을 차트 영역으로 끌어오면 데이터 형식에 따라 사용자에게 적절한 차트 종류가 자동으로 선택됩니다. 사용자는 차트 종류를 적용 가능한 다른 종류로 변경하거나 다른 특성을 추가할 수 있습니다.

    지원되는 차트 종류:
    - 꺾은선형 차트
    - 히스토그램
    - 누적 가로 막대형 차트
    - 상자 플롯
    - 산점도
    - 거품형 플롯
+ 이제 포털에서는 실험 보고서를 동적으로 생성합니다. 사용자가 실행을 실험에 제출하면 서로 다른 실행 간에 비교할 수 있도록 기록된 메트릭과 그래프가 있는 보고서가 자동으로 생성됩니다. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Python용 Azure Machine Learning SDK v1.0.8

+ **버그 수정**: 이 릴리스는 대부분 사소한 버그 수정을 포함합니다.

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **새로운 기능**
  + 데이터 저장소 기능 향상([데이터 저장소 방법 가이드](https://aka.ms/aml-data-prep-datastore-nb)에 설명되어 있음)
    + 강화 시 Azure 파일 공유 및 ADLS 데이터 저장소에서 데이터를 읽고 쓰는 기능이 추가되었습니다.
    + 이제 데이터 저장소 사용 시 Data Prep에서 대화형 인증 대신 서비스 주체 인증 사용을 지원합니다.
    + wasb 및 wasbs url 지원이 추가되었습니다.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **버그 수정**
  + Spark의 공용 읽기 가능 Azure Blob 컨테이너에서 읽기 관련 버그를 수정했습니다.

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Python용 Azure Machine Learning SDK v1.0.6
+ **버그 수정**: 이 릴리스는 대부분 사소한 버그 수정을 포함합니다.

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **새로운 기능**
  + `to_bool` 함수는 이제 일치하지 않는 값을 오류 값으로 변환하도록 허용합니다. 이는 `to_bool` 및 `set_column_types`에 대한 새로운 기본 불일치 동작인 반면 이전 기본 동작은 일치하지 않는 값을 False로 변환하는 것이었습니다.
  + `to_pandas_dataframe`을 호출할 때 NaN으로 숫자 열에서 null/누락된 값을 해석하는 새 옵션이 있습니다.
  + 일부 식의 반환 형식을 확인하는 추가된 기능은 형식 일관성 및 초기 실패를 확인하는 것입니다.
  + 이제 `parse_json`을 호출하여 JSON 개체로 열의 값을 구문 분석하고 여러 열로 확장할 수 있습니다.

+ **버그 수정**
  + Python 3.5.2에서 `set_column_types`를 충돌한 버그가 수정되었습니다.
  + AML 이미지를 사용하여 데이터 저장소에 연결할 때 충돌한 버그가 수정되었습니다.

+ **업데이트**
  * 자습서 시작, 사례 연구 및 방법 가이드에 대한 [예제 Notebooks](https://aka.ms/aml-data-prep-notebooks)

## <a name="2018-12-04-general-availability"></a>2018-12-04: 일반 공급

이제 Azure Machine Learning Service가 일반 공급됩니다.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
이번 릴리스에서는 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute)을 통해 새로운 관리형 컴퓨팅 환경을 발표합니다. 이 컴퓨팅 대상은 Azure Machine Learning에 대한 Azure Batch AI 컴퓨팅을 대체합니다. 

이 컴퓨팅 대상:
+ 모델 학습 및 일괄 처리 추론에 사용됨
+ 단일-다중 노드 컴퓨팅
+ 클러스터 관리 및 사용자에 대한 작업 예약 수행
+ 기본적으로 자동 크기 조정
+ CPU와 GPU 리소스에 대한 지원 
+ 절감된 비용에 대해 낮은 우선 순위의 VM을 사용할 수 있음

Azure Machine Learning 컴퓨팅은 Azure Portal 또는 CLI를 사용하여 Python에서 만들 수 있습니다. 작업 영역의 지역에서 만들어야 하고 다른 작업 영역에 연결할 수 없습니다. 이 컴퓨팅 대상은 실행에 Docker 컨테이너를 사용하고 종속성을 패키지하여 모든 노드에서 동일한 환경을 복제합니다.

> [!Warning]
> Azure Machine Learning 컴퓨팅을 사용하려면 새 작업 영역을 만드는 것이 좋습니다. 기존 작업 영역에서 Azure Machine Learning 컴퓨팅을 만들려고 하는 사용자에게 오류가 표시될 수 있습니다. 작업 영역의 기존 컴퓨팅은 영향을 받지 않고 계속 작동해야 합니다.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Python용 Azure Machine Learning SDK v1.0.2
+ **주요 변경 내용**
  + 이 릴리스에서는 Azure Machine Learning에서 VM을 만들기 위한 지원을 제거합니다. 기존 클라우드 VM 또는 원격 온-프레미스 서버를 계속 연결할 수 있습니다. 
  + 또한 Batch AI에 대한 지원을 제거하며, 모든 기능은 이제 Azure Machine Learning 컴퓨팅을 통해 지원됩니다.

+ **New**
  + 기계 학습 파이프라인:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **업데이트됨**
  + 기계 학습 파이프라인:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)이 이제 runconfig를 허용함
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)이 이제 SQL 데이터 원본 간에 복사됨
    + 게시된 파이프라인을 실행하기 위한 일정을 만들고 업데이트하는 SDK의 일정 기능

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **주요 변경 내용** 
  * `SummaryFunction.N`의 이름이 `SummaryFunction.Count`로 바뀌었습니다.
  
+ **버그 수정**
  * 원격 실행 시 데이터 저장소에 읽고 쓸 때 최신 AML 실행 토큰을 사용합니다. 이전에는 AML 실행 토큰이 Python에서 업데이트되는 경우 Data Prep 런타임이 업데이트된 AML 실행 토큰으로 업데이트되지 않습니다.
  * 추가적인 더 분명한 오류 메시지
  * Spark가 `Kryo` serialization을 사용할 때 to_spark_dataframe()이 더 이상 충돌하지 않음
  * Value Count Inspector가 1000개 이상의 고유 값을 표시할 수 있음
  * 원래 데이터 흐름에 이름이 없는 경우 임의 분할이 더 이상 실패하지 않음  

+ **자세한 정보**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>문서 및 Notebook
+ ML 파이프라인
  + 파이프라인, 일괄 처리 범위 및 스타일 전송 예제로 시작하기 위한 신규 및 업데이트된 Notebook: https://aka.ms/aml-pipeline-notebooks
  + [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법 알아보기
  + [파이프라인을 사용하여 일괄 처리 예측을 실행](how-to-run-batch-predictions.md)하는 방법 알아보기
+ Azure Machine Learning 컴퓨팅 대상
  + 이제 [샘플 Notebook](https://aka.ms/aml-notebooks)이 새 관리형 컴퓨팅을 사용하도록 업데이트되었습니다.
  + [이 컴퓨팅에 대해 알아보기](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure Portal: 새로운 기능
+ 포털에서 [Azure Machine Learning 컴퓨팅](how-to-set-up-training-targets.md#amlcompute) 형식을 만들고 관리합니다.
+ Azure Machine Learning 컴퓨팅에 대한 할당량 사용 및 [요청 할당량](how-to-manage-quotas.md)을 모니터링합니다.
+ Azure Machine Learning 컴퓨팅 클러스터 상태를 실시간으로 확인합니다.
+ Azure Machine Learning 컴퓨팅 및 Azure Kubernetes Service 만들기에 대한 가상 네트워크 지원이 추가되었습니다.
+ 기존 매개 변수를 사용하여 게시된 파이프라인을 다시 실행합니다.
+ 분류 모델(리프트, 게인, 보정, 모델 확장성 포함 기능 중요성 차트) 및 회귀 모델(오차 및 모델 확장성 포함 기능 중요성 차트)에 대한 새로운 [자동화된 기계 학습 차트](how-to-track-experiments.md#auto). 
+ Azure Portal에서 파이프라인을 볼 수 있음




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Python용 Azure Machine Learning SDK v0.1.80

+ **주요 변경 내용** 
  * *azureml.train.widgets* 네임스페이스가 *azureml.widgets*로 전환되었습니다.
  * *azureml.core.compute.AmlCompute*는 *azureml.core.compute.BatchAICompute* 및 *azureml.core.compute.DSVMCompute* 클래스를 더 이상 사용하지 않습니다. 후자의 클래스는 이후 릴리스에서 제거됩니다. AmlCompute 클래스는 이제 더 쉽게 정의할 수 있으며, vm_size 및 max_nodes만 필요하고, 작업이 제출되면 클러스터가 자동으로 0에서 max_nodes로 확장됩니다. [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/training)이 이 정보로 업데이트되었으며 사용 예제를 제공합니다. 이 간소화와 나중에 릴리스될 많은 흥미로운 기능이 마음에 들기를 바랍니다!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning 데이터 준비 SDK v0.5.1 

[참고 문서](https://aka.ms/data-prep-sdk)를 참조하여 데이터 준비 SDK에 대해 자세히 알아봅니다.
+ **새로운 기능**
   * DataPrep 패키지를 실행하고 데이터 세트 또는 데이터 흐름에 대한 데이터 프로필을 볼 수 있는 새 DataPrep CLI가 만들어졌습니다.
   * 유용성이 향상되도록 SetColumnType API가 다시 설계되었습니다.
   * smart_read_file의 이름이 auto_read_file로 변경되었습니다.
   * 기울이기 및 첨도가 이제 데이터 프로필에 포함되었습니다.
   * 계층화된 샘플링을 통해 샘플링할 수 있습니다.
   * CSV 파일이 포함된 zip 파일에서 읽을 수 있습니다.
   * 임의 분할을 사용하여 데이터 세트를 행 단위로 분할할 수 있습니다(예: 테스트 학습 세트로 분할).
   * `.dtypes`를 호출하여 데이터 흐름 또는 데이터 프로필에서 모든 열 데이터 형식을 가져올 수 있습니다.
   * `.row_count`를 호출하여 데이터 흐름 또는 데이터 프로필에서 행 수를 가져올 수 있습니다.

+ **버그 수정**
   * long에서 double로 변환할 수 있도록 수정되었습니다. 
   * 열 추가 이후의 어설션이 수정되었습니다. 
   * 경우에 따라 그룹을 검색하지 않는 FuzzyGrouping 문제가 해결되었습니다.
   * 다중 열 정렬 순서를 따르도록 정렬 함수가 수정되었습니다.
   * `pandas`에서 처리하는 방식과 비슷하게 처리하도록 식이 수정되었습니다.
   * dbfs 경로에서 읽을 수 있도록 수정되었습니다.
   * 오류 메시지를 더 쉽게 이해할 수 있습니다. 
   * 이제 AML 토큰을 사용하여 원격 컴퓨팅 대상을 읽을 때 더 이상 오류가 발생하지 않습니다.
   * 이제 Linux DSVM에서 더 이상 오류가 발생하지 않습니다.
   * 이제 문자열 조건자에 문자열이 아닌 값이 있는 경우에도 더 이상 충돌하지 않습니다.
   * 이제 Dataflow에서 올바르게 실패해야 하는 경우 어설션 오류를 처리합니다.
   * 이제 Azure Databricks에서 dbutils 탑재 스토리지 위치를 지원합니다.

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure portal 
Azure Machine Learning 서비스에 대한 Azure Portal은 다음과 같이 업데이트되었습니다.
  * 게시된 파이프라인에 대한 새 **파이프라인** 탭.
  * 기존 HDInsight 클러스터를 컴퓨팅 대상으로 연결할 수 있도록 지원이 추가되었습니다.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Python용 Azure Machine Learning SDK v0.1.74

+ **주요 변경 내용** 
  * *Workspace.compute_targets, datastores, experiments, images, models 및 *webservices*는 메서드가 아니라 속성입니다. 예를 들어 *Workspace.compute_targets()* 를 *Workspace.compute_targets*로 바꿉니다.
  * *Run.get_context*는 더 이상 *Run.get_submitted_run*을 사용하지 않습니다. 두 번째 메서드는 후속 릴리스에서 제거될 예정입니다.
  * *PipelineData* 클래스는 이제 데이터 저장소 개체를 datastore_name이 아닌 매개 변수로 예상합니다. 마찬가지로, *파이프라인*은 default_datastore_name 대신 default_datastore를 수락합니다.

+ **새로운 기능**
  * Azure Machine Learning 파이프라인 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb)은 이제 MPI 단계를 사용합니다.
  * Jupyter Notebook용 RunDetails 위젯은 파이프라인의 시각화를 표시하도록 업데이트되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning 데이터 준비 SDK v0.4.0 
 
+ **새로운 기능**
  * 데이터 프로필에 유형 개수 추가 
  * 이제 값 개수 및 히스토그램 사용 가능
  * 데이터 프로필의 백분위수 추가
  * 요약에서 중앙값 사용 가능
  * 이제 Python 3.7 지원
  * 데이터 저장소를 포함하는 데이터 흐름을 DataPrep 패키지에 저장하면 데이터 저장소 정보가 DataPrep 패키지의 일부로 유지됩니다.
  * 데이터 저장소에 쓰기 지원 
        
+ **버그 수정**
  * 이제 64비트 부호 없는 정수 오버플로가 Linux에서 올바르게 처리됨
  * smart_read에서 일반 텍스트 파일의 잘못된 텍스트 레이블 수정
  * 이제 문자열 열 형식을 메트릭 보기로 표시
  * ValueKinds를 개별 항목이 아닌 단일 FieldType에 매핑된 것으로 표시하도록 유형 개수 수정
  * 이제는 경로를 문자열로 입력해도 Write_to_csv가 실패하지 않음
  * 바꾸기를 사용할 때 “찾기”를 비워 두어도 더 이상 실패하지 않음 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Python용 Azure Machine Learning SDK v0.1.68

+ **새로운 기능**
  * 새 작업 영역을 만들 때 다중 테넌트를 지원합니다.

+ **수정된 버그**
  * 웹 서비스를 배포할 때 pynacl 라이브러리 버전을 더 이상 고정할 필요가 없습니다.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning 데이터 준비 SDK v0.3.0

+ **새로운 기능**
  * 사용자가 실행할 Python 파일의 경로를 전달할 수 있는 transform_partition_with_file(script_path) 메서드가 추가되었습니다.

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Python용 Azure Machine Learning SDK v0.1.65
[버전 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65)에는 새로운 기능, 더 많은 설명서, 버그 수정 및 더 많은 [노트북 샘플](https://aka.ms/aml-notebooks)이 포함되었습니다.

알려진 버그 및 해결 방법에 대해 알아 보려면 [알려진 문제 목록](resource-known-issues.md)을 참조하세요.

+ **주요 변경 내용**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services에서 이전에 반환된 목록인 사전을 반환합니다. [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API 설명서를 참조하세요.

  * 자동화된 Machine Learning에서 기본 메트릭의 정규화된 평균 제곱 오차가 제거되었습니다.

+ **HyperDrive**
  * Bayesian에 대한 다양한 HyperDrive 버그가 수정되고, 메트릭 호출 가져오기 성능이 향상되었습니다. 
  * Tensorflow 1.9에서 1.10으로 업그레이드 
  * 콜드 부팅용 Docker 이미지 최적화 
  * 0이 아닌 오류 코드로 인해 종료하는 경우에도 작업에서 올바른 상태를 보고합니다. 
  * SDK에서 RunConfig 특성 유효성 검사 
  * HyperDrive 실행 개체에서 일반 실행과 비슷한 방식으로 취소를 지원합니다(매개 변수를 전달할 필요가 없음). 
  * 분산 실행 및 HyperDrive 실행에 대한 드롭다운 값의 상태를 유지하도록 위젯이 향상되었습니다. 
  * TensorBoard 및 다른 로그 파일에서 매개 변수 서버에 대한 수정을 지원합니다. 
  * 서비스 쪽에서 인텔(R) MPI 지원 
  * BatchAI의 유효성 검사 중에 분산 실행 수정에 대한 매개 변수 튜닝 버그가 수정되었습니다. 
  * 컨텍스트 관리자에서 기본 인스턴스를 식별합니다. 

+ **Azure Portal 환경**
  * log_table() 및 log_row()가 [실행] 세부 정보에서 지원됩니다. 
  * 1, 2 또는 3개의 숫자 열과 선택적 범주별 열이 있는 테이블과 행에 대한 그래프를 자동으로 만듭니다.

+ **자동화된 Machine Learning**
  * 오류 처리 및 설명서가 향상되었습니다. 
  * 실행 속성 검색 성능 문제가 해결되었습니다. 
  * 계속 실행 문제가 해결되었습니다. 
  * ensembling 반복 문제가 해결되었습니다.
  * MAC OS의 학습 중단 버그가 수정되었습니다.
  * 사용자 지정 유효성 검사 시나리오에서 매크로 평균 PR/ROC 곡선의 저해상도가 처리되었습니다.
  * 추가 인덱스 논리가 제거되었습니다.
  * get_output API에서 필터가 제거되었습니다.

+ **파이프라인**
  * 실행 작업을 먼저 실행할 필요 없이 파이프라인을 직접 게시하는 Pipeline.publish() 메서드가 추가되었습니다.   
  * 게시된 파이프라인에서 생성된 파이프라인 실행을 가져오는 PipelineRun.get_pipeline_runs() 메서드가 추가되었습니다.

+ **Project Brainwave**
  * FPGA에서 사용할 수 있는 새 AI 모델에 대한 지원이 업데이트되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning 데이터 준비 SDK v0.2.0
[버전 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/)에 포함된 기능과 버그 수정은 다음과 같습니다.

+ **새로운 기능**
  * One Hot 인코딩 지원
  * 변 위치 변환 지원
   
+ **수정된 버그:**
  * 모든 Tornado 버전에서 작동하므로 Tornado 버전을 다운그레이드할 필요가 없습니다.
  * Value에서 상위 3개 외에도 모든 값의 개수를 계산합니다.

## <a name="2018-09-public-preview-refresh"></a>2018-09(공개 미리 보기 새로 고침)

새로 고친 Azure Machine Learning 릴리스: 이 릴리스에 대한 자세한 내용: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>다음 단계

[Azure Machine Learning Service](../service/overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
