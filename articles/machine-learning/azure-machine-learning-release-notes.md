---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 최신 업데이트 및 기계 학습 및 데이터 준비 Python Sdk에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 648a1ab7eac05e42a2d3ae292cccef8c0833a84d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226814"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 릴리스 정보

이 문서에서는 Azure Machine Learning 릴리스에 대해 알아봅니다.  전체 SDK 참조 콘텐츠는 Azure Machine Learning의 [**Python 용 기본 SDK**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 페이지를 참조 하세요.

알려진 버그 및 해결 방법에 대해 알아 보려면 [알려진 문제 목록](resource-known-issues.md)을 참조하세요.

## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK for Python v 1.13.0
+ **미리 보기 기능**
  + **azureml-core**
  
    새 출력 데이터 집합 기능을 사용 하면 Blob, ADLS Gen 1, ADLS Gen 2 및 파일 공유를 포함 하 여 클라우드 저장소에 다시 쓸 수 있습니다. 데이터를 출력 하는 위치, 데이터를 출력 하는 방법 (탑재 또는 업로드를 통해), 나중에 다시 사용 하기 위해 출력 데이터를 등록 하 고 파이프라인 단계 간에 중간 데이터를 전달할 것인지 여부를 구성할 수 있습니다. 이렇게 하면 재현 가능성을 공유 하 고, 데이터 중복을 방지 하며, 비용 효율성과 생산성을 높일 수 있습니다. [사용 방법 알아보기](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?view=azure-ml-py)
    
+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoML에 대 한 모든 pip 종속성을 고정 하기 위해 validated_ {platform} _requirements.txt 파일을 추가 했습니다.
    + 이 릴리스에서는 4Gb 보다 큰 모델을 지원 합니다.
    + 자동 Ml 종속성 업그레이드: `scikit-learn` (now 0.22.1), (now `pandas` 0.25.1), `numpy` (이제 1.18.2).
  + **azureml-automl-runtime**
    + Horovod를 설정 하 여 항상 fp16 압축을 사용 하도록 합니다.
    + 이 릴리스에서는 4Gb 보다 큰 모델을 지원 합니다.
    + ImportError를 사용 하 여 AutoML이 실패 하는 문제를 해결 함: 이름을 가져올 수 없습니다 `RollingOriginValidator` .
    + 자동 Ml 종속성 업그레이드: `scikit-learn` (now 0.22.1), (now `pandas` 0.25.1), `numpy` (이제 1.18.2).
  + **azureml-dnn-자동 ml-예측**
    + 자동 Ml 종속성 업그레이드: `scikit-learn` (now 0.22.1), (now `pandas` 0.25.1), `numpy` (이제 1.18.2).
  + **azureml-contrib-fairness**
    + Azureml에 대 한 간단한 설명을 제공 합니다.
  + **azureml-contrib-pipeline-steps**
    + 이 패키지는 더 이상 사용 되지 않으며 사용자가 azureml-파이프라인 단계를 대신 사용 해야 함을 나타내는 메시지가 추가 되었습니다.
  + **azureml-core**
    + 작업 영역에 대 한 목록 키 명령이 추가 되었습니다.
    + 작업 영역 SDK 및 CLI에서 tags 매개 변수를 추가 합니다.
    + 로 인해 데이터 집합을 사용 하 여 자식 실행을 제출 하는 작업이 실패 하는 버그를 수정 `TypeError: can't pickle _thread.RLock objects` 했습니다.
    + 모델 목록 ()에 대 한 page_count 기본/설명서를 추가 하는 중입니다.
    + Adbworkspace 매개 변수를 사용 하 고 작업 영역 adb 링크/연결 해제 runner를 추가 하도록 CLI&SDK를 수정 합니다.
    + Dataset에서 버그를 수정 합니다. 최신 데이터 집합 버전을 업데이트 하는 데에는에 대 한 데이터 집합 업데이트 버전이 호출 되지 않습니다. 
    + 데이터 집합의 버그를 수정 합니다. get_by_name는 특정 이전 버전이 실제로 검색 된 경우에도 최신 데이터 집합 버전에 대 한 태그를 표시 합니다.
  + **azureml-interpret**
    + Azureml의 shap 점수 매기기 explainers에 확률 출력이 추가 되었습니다 .이는 원래 설명의 shap_values_output 매개 변수를 기반으로 해석 됩니다.
  + **azureml-pipeline-core**
    + 개선 된 `PipelineOutputAbstractDataset.register` 설명서입니다.
  + **azureml-train-automl-client**
    + 자동 Ml 종속성 업그레이드: `scikit-learn` (now 0.22.1), (now `pandas` 0.25.1), `numpy` (이제 1.18.2).
  + **azureml-train-automl-runtime**
    + 자동 Ml 종속성 업그레이드: `scikit-learn` (now 0.22.1), (now `pandas` 0.25.1), `numpy` (이제 1.18.2).
  + **azureml-train-core**
    + 이제 사용자가 HyperDriveConfig를 만들 때 유효한 hyperparameter_sampling 인수를 제공 해야 합니다. 또한 HyperDriveRunConfig에 대 한 설명서는 사용자에 게 HyperDriveRunConfig의 사용 중단을 알리기 위해 편집 되었습니다.
    + PyTorch 기본 버전을 1.4로 되돌립니다.
    + PyTorch 1.6 & Tensorflow 2.2 이미지 및 큐 레이트 환경을 추가 합니다.

## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK for Python v 1.12.0

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 모델에 image_name 및 image_label 매개 변수를 추가 하 여 기본 패키지 이미지의 이름을 바꿀 수 있습니다.
  + **azureml-automl-core**
    + AutoML은 읽는 동안 콘텐츠를 수정할 때 dataprep에서 새 오류 코드를 발생 시킵니다.
  + **azureml-automl-runtime**
    + 데이터에 누락 된 값이 포함 되어 있지만 기능화가 해제 되어 있는 경우 사용자에 대 한 경고를 추가 했습니다.
    + 데이터가 nan을 포함 하 고 기능화가 해제 되어 있는 경우 자식 실행 오류가 해결 되었습니다.
    + AutoML은 읽는 동안 콘텐츠를 수정할 때 dataprep에서 새 오류 코드를 발생 시킵니다.
    + 그레인에서 발생 하는 예측 메트릭의 정규화를 업데이트 했습니다.
    + Lookback 기능을 사용 하지 않도록 설정 하면 예측 변 위치 계산이 향상 되었습니다.
    + AutoML 후에 설명을 계산할 때 bool 스파스 행렬 처리가 수정 되었습니다.
  + **azureml-core**
    + 이제 새 메서드에 `run.get_detailed_status()` 현재 실행 상태에 대 한 자세한 설명이 표시 됩니다. 현재 상태에 대 한 설명만 표시 됩니다 `Queued` .
    + 모델에 image_name 및 image_label 매개 변수를 추가 하 여 기본 패키지 이미지의 이름을 바꿀 수 있습니다.
    + `set_pip_requirements()`전체 pip 섹션을 한 번에 설정 하는 새 메서드입니다 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) .
    + 자격 증명을 등록 하지 ADLS Gen2 데이터 저장소를 사용 합니다.
    + 잘못 된 데이터 집합 형식을 다운로드 하거나 탑재 하려고 할 때 오류 메시지가 개선 되었습니다.
    + 필터 최적화를 제공 하는 partition_timestamp에 대 한 추가 예제가 포함 된 시계열 데이터 집합 필터 샘플 노트북을 업데이트 합니다.
    + 개인 끝점 연결을 삭제할 때 ArmResourceId 대신 subscriptionId, resourceGroup, workspaceName, peConnectionName를 매개 변수로 허용 하도록 sdk 및 cli를 변경 합니다.
    + 실험적 데코레이터는 쉽게 식별할 수 있도록 클래스 이름을 표시 합니다.
    + 모델 내의 자산에 대 한 설명은 더 이상 실행을 기반으로 자동으로 생성 되지 않습니다.
  + **azureml-datadrift**
    + DataDriftDetector의 create_from_model API를 사용 되지 않는 것으로 표시 합니다.
  + **azureml-dataprep**
    + 잘못 된 데이터 집합 형식을 다운로드 하거나 탑재 하려고 할 때 오류 메시지가 개선 되었습니다.
  + **azureml-pipeline-core**
    + 등록 된 데이터 집합을 포함 하는 파이프라인 그래프를 deserialize 할 때 버그가 수정 되었습니다.
  + **azureml-pipeline-steps**
    + RScriptStep은 azureml의 RSection을 지원 합니다.
    + 공용 API에서 passthru_automl_config 매개 변수를 제거 `AutoMLStep` 하 고 내부 전용 매개 변수로 변환 했습니다.
  + **azureml-train-automl-client**
    + 자동 Ml에서 로컬 비동기 관리 되는 환경을 제거 했습니다. 모든 로컬 실행이 실행이 시작 된 환경에서 실행 됩니다.
    + 사용자 제공 스크립트 없이 AutoML 실행을 제출할 때 발생 하는 스냅숏 문제를 수정 했습니다.
    + 데이터가 nan을 포함 하 고 기능화가 해제 되어 있는 경우 자식 실행 오류가 해결 되었습니다.
  + **azureml-train-automl-runtime**
    + AutoML은 읽는 동안 콘텐츠를 수정할 때 dataprep에서 새 오류 코드를 발생 시킵니다.
    + 사용자 제공 스크립트 없이 AutoML 실행을 제출할 때 발생 하는 스냅숏 문제를 수정 했습니다.
    + 데이터가 nan을 포함 하 고 기능화가 해제 되어 있는 경우 자식 실행 오류가 해결 되었습니다.
  + **azureml-train-core**
    + To 매개 변수에 전달 된 pip 요구 사항 파일에서 pip 옵션 (예:--extra-url)을 지정 하기 위한 지원이 추가 되었습니다 [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) `pip_requirements_file` .


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK for Python v 1.11.0

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + Cli 모델 등록 경로의 실행 개체에 전달 되지 않은 모델 프레임 워크 및 모델 프레임 워크 수정
    + Cli amlcompute identity show 명령을 수정 하 여 테 넌 트 id 및 보안 주체 id를 표시 합니다. 
  + **azureml-train-automl-client**
    + 연결 된 모델을 다운로드 하지 않고 AutoML 실행에 대 한 최상의 자식 실행을 AutoMLRun ()에 get_best_child ()을 추가 했습니다.
    + 모델을 로컬로 다운로드 하지 않고도 예측 또는 예측이 원격 학습 환경에서 실행 될 수 있도록 하는 ModelProxy 개체가 추가 되었습니다.
    + AutoML의 처리 되지 않은 예외는 이제 오류에 대 한 자세한 정보를 찾을 수 있는 알려진 문제 HTTP 페이지를 가리킵니다.
  + **azureml-core**
    + 모델 이름은 255 자까지 가능 합니다.
    + Get_image_details () 반환 개체 형식이 변경 되었습니다. `DockerImageDetails` 클래스를 대체 했습니다 `dict` . 새 클래스 속성에서 이미지 세부 정보를 사용할 수 있습니다. 변경 내용은 이전 버전과 호환 됩니다.
    + From_pip_requirements ()에 대 한 버그를 수정 하 여 종속성 구조를 유지 합니다.
    + Int와 double이 동일한 목록에 포함 된 경우 log_list 실패 하는 버그를 수정 했습니다.
    + 기존 작업 영역에서 개인 링크를 사용 하도록 설정 하는 동안 작업 영역에 연결 된 계산 대상이 있는 경우 해당 대상이 작업 영역 개인 끝점과 동일한 가상 네트워크 뒤에 있지 않으면 해당 대상이 작동 하지 않습니다.
    + `as_named_input`실험에서 데이터 집합을 사용 하 고 및에 추가 하는 경우 선택적 `as_mount` `as_download` 으로 수행 `FileDataset` 됩니다. `as_mount`또는가 호출 되 면 입력 이름이 자동으로 생성 됩니다 `as_download` .
  + **azureml-automl-core**
    + AutoML의 처리 되지 않은 예외는 이제 오류에 대 한 자세한 정보를 찾을 수 있는 알려진 문제 HTTP 페이지를 가리킵니다.
    + 연결 된 모델을 다운로드 하지 않고 AutoML 실행에 대 한 최상의 자식 실행을 AutoMLRun ()에 get_best_child ()을 추가 했습니다.
    + 모델을 로컬로 다운로드 하지 않고도 예측 또는 예측이 원격 학습 환경에서 실행 될 수 있도록 하는 ModelProxy 개체가 추가 되었습니다.
  + **azureml-pipeline-steps**
    + `enable_default_model_output`에를 추가 하 고 `enable_default_metrics_output` 에 플래그를 추가 `AutoMLStep` 합니다. 이러한 플래그를 사용 하 여 기본 출력을 사용/사용 하지 않도록 설정할 수 있습니다.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK for Python v 1.10.0

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoML을 사용 하는 경우 경로가 AutoMLConfig 개체로 전달 되 고 아직 없는 경우 자동으로 생성 됩니다.
    + 이제 사용자는 매개 변수를 사용 하 여 예측 태스크에 대 한 시계열 빈도를 지정할 수 있습니다 `freq` .
  + **azureml-automl-runtime**
    + AutoML을 사용 하는 경우 경로가 AutoMLConfig 개체로 전달 되 고 아직 없는 경우 자동으로 생성 됩니다.
    + 이제 사용자는 매개 변수를 사용 하 여 예측 태스크에 대 한 시계열 빈도를 지정할 수 있습니다 `freq` .
    + 이제 AutoML 예측은 테스트 또는 유효성 검사 설정의 길이가 입력 된 수평선 보다 긴 사용 사례에 적용 되 고 알려진 y_pred 값이 예측 컨텍스트로 사용 되는 롤링 평가를 지원 합니다.
  + **azureml-core**
    + 실행의 데이터 저장소에서 파일이 다운로드 되지 않은 경우 경고 메시지가 인쇄 됩니다.
    + 에 대 한 설명서를에 추가 했습니다 `skip_validation` `Datastore.register_azure_sql_database method` .
    + 자동 승인 된 개인 끝점을 만들려면 사용자가 sdk v 1.10.0 이상으로 업그레이드 해야 합니다. 여기에는 VNet 뒤에서 사용할 수 있는 노트북 리소스가 포함 됩니다.
    + 가져오기 작업 영역에 대 한 응답에 참고 Bookinfo를 노출 합니다.
    + 을 (를) 변경 하면 계산 대상을 나열 하 고 계산 대상을 가져오는 호출이 원격 실행에서 성공 합니다. 계산 대상과 목록 작업 영역을 가져오는 Sdk 함수는 이제 원격 실행에서 작동 합니다.
    + 사용 중단 메시지를 azureml의 클래스 설명에 추가 합니다.
    + 작업 영역 개인 끝점 생성에 실패 하는 경우 예외를 Throw 하 고 작업 영역 및 종속 리소스를 정리 합니다.
    + 작업 영역 업데이트 방법에서 작업 영역 sku 업그레이드를 지원 합니다.
  + **azureml-datadrift**
    + Matplotlib 버전을 3.0.2에서 3.2.1로 업데이트 하 여 python 3.8를 지원 합니다.
  + **azureml-dataprep**
    + 또는 요청을 사용 하 여 웹 url 데이터 원본에 대 한 지원이 추가 되었습니다 `Range` `Head` . 
    + 파일 데이터 집합 탑재 및 다운로드를 위한 안정성이 향상 되었습니다.
  + **azureml-train-automl-client**
    + Setuptools에서의 제거와 관련 된 문제를 해결 `RequirementParseError` 했습니다.
    + "Compute_target = ' local '"을 사용 하 여 제출 된 로컬 실행에 대해 conda 대신 docker를 사용 합니다.
    + 콘솔에 인쇄 된 반복 기간이 수정 되었습니다. 이전에는 반복 기간이 실행 종료 시간을 뺀 시간으로 인쇄 되는 경우도 있습니다. 실행 종료 시간이 실행 시작 시간을 뺀 값과 동일 하 게 수정 되었습니다.
    + AutoML을 사용 하는 경우 경로가 AutoMLConfig 개체로 전달 되 고 아직 없는 경우 자동으로 생성 됩니다.
    + 이제 사용자는 매개 변수를 사용 하 여 예측 태스크에 대 한 시계열 빈도를 지정할 수 있습니다 `freq` .
  + **azureml-train-automl-runtime**
    + 최상의 모델 설명이 실패할 경우 향상 된 콘솔 출력입니다.
    + "Backlist_models" 입력 매개 변수를 "blocked_models"로 바꿨습니다.
      + "Whitelist_models" 입력 매개 변수를 "allowed_models"로 바꿨습니다.
    + 이제 사용자는 매개 변수를 사용 하 여 예측 태스크에 대 한 시계열 빈도를 지정할 수 있습니다 `freq` .

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK for Python v 1.9.0

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + Get_model_path ()를 AutoML 자동 생성 된 점수 매기기 스크립트의 AZUREML_MODEL_DIR 환경 변수로 바꿉니다. 또한 init () 중에 오류를 추적 하기 위해 원격 분석을 추가 했습니다.
    + AutoMLConfig의 일부로 지정 하는 기능을 제거 했습니다. `enable_cache`
    + 특정 예측 실행 중에 서비스 오류로 인해 실행이 실패할 수 있는 버그를 수정 했습니다.
    + 에서 특정 모델에 대 한 오류 처리 향상 `get_output`
    + Y 변환기를 사용 하 여 분류를 위한 fitted_model (X, y) 호출을 수정 했습니다.
    + 사용 하도록 설정 된 사용자 지정 된 전방 채우기 작업을 위한 작업입니다.
    + Dict 형식의 예측 매개 변수 대신 새 ForecastingParameters 클래스가 사용 됩니다.
    + 향상 된 대상 지연 인코딩이
    + BERT를 사용 하 여 멀티-noded 바인딩된 다중 gpu 분산 기능화의 제한 된 가용성 추가
  + **azureml-automl-runtime**
    + 이제 Prophet는 곱하기 대신 추가 계절성 모델링을 수행 합니다.
    + Short 조직 때 문제가 해결 되 고, 긴 조직 주파수와 다른 빈도가 있으면 실행이 실패 합니다.
  + **azureml-자동 dnn-비전**
    + 학습 및 평가에 대 한 시스템/gpu 통계 및 로그 평균 수집
  + **azureml-mir**
    + ManagedInferencing에서 app insights 플래그 사용에 대 한 지원이 추가 됨
  + **azureml-core**
    + 현재 계산에서 데이터 소스에 액세스할 수 없는 경우 유효성 검사를 건너뛰도록 허용 하 여 이러한 API에 대 한 유효성 검사 매개 변수입니다.
      + TabularDataset time_before (end_time, include_boundary = True, validate = True)
      + TabularDataset time_after (start_time, include_boundary = True, validate = True)
      + TabularDataset time_recent (time_delta, include_boundary = True, validate = True)
      + TabularDataset time_between (start_time, end_time, include_boundary = True, validate = True)
    + 모델 목록에 대 한 프레임 워크 필터링 지원을 추가 하 고 노트북에서 NCD automl 샘플을 추가 했습니다.
    + 데이터 저장소의 register_azure_blob_container 및 데이터 저장소. register_azure_file_share (SAS 토큰을 지 원하는 옵션만)에서는 `sas_token` 일반적인 읽기 및 쓰기 시나리오에 대 한 최소 권한 요구 사항을 포함 하도록 필드의 문서 문자열을 업데이트 했습니다.
    + 사용 중단 ()의 _with_auth 매개 변수를 get_mlflow_tracking_uri 합니다.
  + **azureml.mlflow**
    + AzureML-MLflow를 사용 하 여 로컬 file://모델 배포에 대 한 지원 추가
    + 사용 중단 ()의 _with_auth 매개 변수를 get_mlflow_tracking_uri 합니다.
  + **azureml-opendatasets**
    + 최근 게시 된 Covid-19 추적 데이터 집합을 이제 SDK와 함께 사용할 수 있습니다.
  + **azureml-pipeline-core**
    + "Azureml-기본값"이 pip 종속성의 일부로 포함 되지 않은 경우 로그 아웃 경고
    + 메모 렌더링을 개선 합니다.
    + 구분 된 파일을 PipelineOutputFileDataset로 구문 분석할 때 따옴표로 묶인 줄 바꿈에 대 한 지원이 추가 되었습니다.
    + PipelineDataset 클래스는 더 이상 사용 되지 않습니다. 자세한 내용은 https://aka.ms/dataset-deprecation를 참조하세요. 파이프라인에서 데이터 집합을 사용 하는 방법에 대 한 자세한 내용은을 참조 https://aka.ms/pipeline-with-dataset 하세요.
  + **azureml-pipeline-steps**
    + Azureml-파이프라인 단계로 문서를 업데이트 합니다.
    +  `load_yaml()`사용자가 구성의 나머지 부분과 별도 파일을 사용 하 여 인라인으로 환경을 정의할 수 있는 ParallelRunConfig의 지원이 추가 되었습니다.
  + **azureml-자동 ml-클라이언트**.
    + AutoMLConfig의 일부로 지정 하는 기능을 제거 했습니다. `enable_cache`
  + **azureml-train-automl-runtime**
    + BERT를 사용 하 여 멀티-noded 바인딩된 다중 gpu distributed 기능화의 제한 된 가용성을 추가 했습니다.
    + ADB 기반 자동화 된 machine learning 실행에서 호환 되지 않는 패키지에 대 한 오류 처리를 추가 했습니다.
  + **azureml-widgets**
    + Azureml 위젯에 대 한 문서 업데이트입니다.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK for Python v 1.8.0
  
  + **미리 보기 기능**
    + **azureml-서-공평** `azureml-contrib-fairness` 패키지는 오픈 소스 공평 평가 및 unfairness 완화 패키지 [Fairlearn](https://fairlearn.github.io) 와 Azure Machine Learning studio 간의 통합을 제공 합니다. 특히 패키지를 사용 하면 모델 공평 평가 대시보드가 AzureML 실행의 일부로 업로드 되어 Azure Machine Learning studio에 표시 됩니다.

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + Init 컨테이너의 로그 가져오기를 지원 합니다.
    + 새 CLI 명령을 추가 하 여 새 인스턴스를 관리 합니다.
  + **azureml-automl-core**
    + 사용자는 이제 과도 하 게 일치 시킬 수 있는 경고를 사용 하 여 시계열 작업에 대해 stack 앙상블 반복을 사용 하도록 설정할 수 있습니다.
    + `azureml.automl.core.shared.exceptions.CacheStoreCorruptedException`캐시 저장소 콘텐츠가 변조 된 경우 발생 하는 새로운 유형의 사용자 예외를 추가 했습니다.
  + **azureml-automl-runtime**
    + 사용자가 기능화를 사용 하지 않도록 설정 하면 클래스 균형 조정이 더 이상 사용 되지 않습니다.  
  + **azureml-고-itp**
    + CmAks 계산 형식이 지원 됩니다. 사용자 고유의 AKS 클러스터를 학습 작업을 위한 작업 영역에 연결할 수 있습니다.
  + **azureml-contrib-notebook**
    + Azureml의 문서 향상-노트북 패키지.
  + **azureml-contrib-pipeline-steps**
    + Azureml의 문서 개선 사항--파이프라인 단계 패키지.
  + **azureml-core**
    + 고객이 작업 영역 연결 리소스에서 작업 하는 set_connection, get_connection, list_connections, delete_connection 함수를 추가 합니다.
    + Azureml-coore/azureml 패키지에 대 한 설명서 업데이트입니다.
    + Azureml 코어 패키지에 대 한 설명서 업데이트입니다.
    + 문서를 업데이트 합니다.
    + Azureml-core/azureml. 계산 패키지의 문서 개선
    + Azureml의 웹 서비스 관련 클래스에 대 한 향상 된 문서입니다.
    + 사용자가 선택한 데이터 저장소에서 프로 파일링 데이터를 저장 하도록 지원
    + 모델 목록 API에 대 한 확장 및 page_count 속성 추가 됨
    + 덮어쓰기 속성을 제거 하면 제출 된 실행이 deserialization 오류로 인해 실패 하는 버그가 수정 되었습니다.
    + 단일 파일을 참조 하는 FileDataset을 다운로드 하거나 탑재할 때 일관 되지 않은 폴더 구조를 수정 했습니다.
    + 이제 parquet 파일 to_spark_dataframe의 데이터 집합을 로드 하는 속도가 빨라지고 모든 parquet 및 Spark SQL 데이터 형식을 지원 합니다.
    + Init 컨테이너의 로그 가져오기를 지원 합니다.
    + AutoML 실행은 이제 병렬 실행 단계의 자식 실행으로 표시 되었습니다.
  + **azureml-datadrift**
    + Azureml의 문서 향상-노트북 패키지.
  + **azureml-dataprep**
    + 이제 parquet 파일 to_spark_dataframe의 데이터 집합을 로드 하는 속도가 빨라지고 모든 parquet 및 Spark SQL 데이터 형식을 지원 합니다.
    + To_pandas_dataframe에 대 한 OutOfMemory 문제가 발생 하는 경우 메모리 처리가 향상 됩니다.
  + **azureml-interpret**
    + 업그레이드 된 azureml-해석 커뮤니티 버전 0.12. *를 사용 하도록 해석 합니다.
  + **azureml.mlflow**
    + Azureml-mlflow의 향상 된 문서입니다.
    + MLFlow를 사용 하 여 AML 모델 레지스트리에 대 한 지원을 추가 합니다.
  + **azureml-opendatasets**
    + Python 3.8에 대한 지원이 추가됨
  + **azureml-pipeline-core**
    + `PipelineDataset`내부 클래스 인지 명확 하 게 하기 위해 업데이트 된 설명서입니다.
    + ParallelRunStep는 한 인수에 대해 여러 값을 허용 하도록 업데이트 합니다 (예: "--group_column_names", "Col1", "Col2", "Col3").
    + 파이프라인에서 AutoMLStep를 사용 하는 중간 데이터 사용에 대 한 passthru_automl_config 요구 사항이 제거 되었습니다.
  + **azureml-pipeline-steps**
    + Azureml-파이프라인 단계 패키지의 문서 개선
    + 파이프라인에서 AutoMLStep를 사용 하는 중간 데이터 사용에 대 한 passthru_automl_config 요구 사항이 제거 되었습니다.
  + **azureml-telemetry**
    + Azureml의 문서 개선-원격 분석.
  + **azureml-train-automl-client**
    + `experiment.submit()`개체에 대해를 두 번 호출 하 여 다른 동작을 발생 시킨 버그를 수정 `AutoMLConfig` 했습니다.
    + 사용자는 이제 과도 하 게 일치 시킬 수 있는 경고를 사용 하 여 시계열 작업에 대해 stack 앙상블 반복을 사용 하도록 설정할 수 있습니다.
    + 서비스에서 사용자 오류를 throw 하는 경우 UserErrorException을 발생 시키기 위해 AutoML 실행 동작이 변경 되었습니다.
    + 원격 계산 대상에서 AutoML 실험을 수행할 때 로그가 생성 되지 않거나 로그가 누락 될 azureml_automl 발생 하는 버그를 수정 합니다.
    + 불균형 클래스를 포함 하는 분류 데이터 집합의 경우, 기능 스위퍼 하위 샘플링 된 데이터에 대해 결정 하는 경우 가중치 분산을 적용 하면 특정 임계값으로 분류 작업의 성능을 향상 시킬 수 있습니다.
    + AutoML 실행은 이제 병렬 실행 단계의 자식 실행으로 표시 되었습니다.
  + **azureml-train-automl-runtime**
    + 서비스에서 사용자 오류를 throw 하는 경우 UserErrorException을 발생 시키기 위해 AutoML 실행 동작이 변경 되었습니다.
    + AutoML 실행은 이제 병렬 실행 단계의 자식 실행으로 표시 되었습니다.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK for Python v 1.7.0

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + Cli 명령 및 패키지 종속성을 정리 하 여 mir에서 모델 프로 파일링을 제거 했습니다. 모델 프로 파일링은 코어에서 사용할 수 있습니다.
    + 최소 Azure Cli 버전을 2.3.0로 업그레이드 합니다.
  + **azureml-automl-core**
    + 사용자 지정 변환기 매개 변수로 인해 기능화 단계 fit_transform ()에 대 한 더 나은 예외 메시지입니다.
    + 자동화 된 ML의 BERT와 같은 심층 학습 변환기 모델에 대 한 여러 언어에 대 한 지원을 추가 합니다.
    + 설명서에서 사용 되지 않는 lag_length 매개 변수를 제거 합니다.
    + 예측 매개 변수 설명서가 개선 되었습니다. Lag_length 매개 변수는 사용 되지 않습니다.
  + **azureml-automl-runtime**
    + 예측/테스트 시간에 범주 열 중 하나가 비어 있을 때 발생 하는 오류를 수정 했습니다.
    + Lookback 기능을 사용 하도록 설정 하 고 데이터에 짧은 조직 포함 될 때 발생 하는 실행 오류를 수정 합니다.
    + 지연 또는 롤링 창이 ' 자동 '으로 설정 된 경우 중복 된 시간 인덱스 오류 메시지 문제를 수정 했습니다.
    + Lookback 기능을 포함 하는 데이터 집합에 대 한 Prophet 및 Arima 모델 문제를 해결 했습니다.
    + 열에 2262-04-11 1677-09-21 이전 날짜에 대 한 지원이 추가 되 고 그 다음에는 예측 작업의 날짜 시간이 추가 되었습니다. 향상 된 오류 메시지.
    + 예측 매개 변수 설명서가 개선 되었습니다. Lag_length 매개 변수는 사용 되지 않습니다.
    + 사용자 지정 변환기 매개 변수로 인해 기능화 단계 fit_transform ()에 대 한 더 나은 예외 메시지입니다.
    + 자동화 된 ML의 BERT와 같은 심층 학습 변환기 모델에 대 한 여러 언어에 대 한 지원을 추가 합니다.
    + 일부 OSErrors를 발생 시키는 캐시 작업은 사용자 오류를 발생 시킵니다.
    + 학습 및 유효성 검사 데이터의 개수와 열 집합이 같은지 확인 하는 검사를 추가 했습니다.
    + 데이터에 따옴표가 포함 된 경우 자동으로 생성 되는 AutoML 점수 매기기 스크립트 문제 해결
    + Prophet 모델을 포함 하는 AutoML Prophet 및 ensembled 모델에 대 한 설명을 사용 하도록 설정 합니다.
    + 최근 고객 문제는 클래스 분산 논리가 제대로 사용 하도록 설정 되지 않은 경우에도 클래스 균형 해제를 따라 메시지를 기록 하는 라이브 사이트 버그로 나타났습니다. 이 PR을 사용 하 여 해당 로그/메시지를 제거 합니다.
  + **azureml-cli-일반**
    + Cli 명령 및 패키지 종속성을 정리 하 여 mir에서 모델 프로 파일링을 제거 했습니다. 모델 프로 파일링은 코어에서 사용할 수 있습니다.
  + **azureml-contrib-reinforcementlearning**
    + 부하 테스트 도구
  + **azureml-core**
    + Py에 대 Script_run_config 한 설명서 변경 내용
    + 전송 파이프라인 실행 CLI의 출력을 인쇄 하 여 버그를 수정 합니다.
    + Azureml-core/azureml 데이터에 대 한 설명서 개선 사항
    + Hdfs getconf 명령을 사용 하 여 저장소 계정을 검색 하는 문제를 수정 합니다.
    + 향상 된 register_azure_blob_container 및 register_azure_file_share 설명서
  + **azureml-datadrift**
    + 데이터 집합 드리프트 모니터 비활성화 및 활성화를 위한 향상 된 구현
  + **azureml-interpret**
    + 설명 클라이언트에서 아티팩트에서 업로드할 때 json serialization 전에 Nan 또는 Inf를 제거 합니다.
    + 최신 버전의 해석-커뮤니티로 업데이트 하 여 많은 기능과 클래스를 사용 하는 전역 설명의 메모리 부족 오류를 개선
    + 설명 업로드에 선택적 매개 변수 true_ys 추가 하 여 studio UI에서 추가 기능을 사용 하도록 설정 합니다.
    + Download_model_explanations () 및 list_model_explanations () 성능 향상
    + 디버깅에 도움이 되는 약간의 전자 필기장 조정
  + **azureml-opendatasets**
    + azureml-opendatasets은 azureml 1.4.0 rep 버전이 필요 합니다. 낮은 버전이 검색 되 면 경고가 추가 됨
  + **azureml-pipeline-core**
    + 이 변경을 통해 사용자는 모듈을 호출할 때 moduleVersion에 선택적 .runconfig를 제공할 수 있습니다. Publish_python_script.
    + 노드 사용 계정은 ParallelRunStep의 파이프라인 매개 변수가 될 수 있습니다.
  + **azureml-pipeline-steps**
    + 이 변경을 통해 사용자는 모듈을 호출할 때 moduleVersion에 선택적 .runconfig를 제공할 수 있습니다. Publish_python_script.
  + **azureml-train-automl-client**
    + 자동화 된 ML의 BERT와 같은 심층 학습 변환기 모델에 대 한 여러 언어에 대 한 지원을 추가 합니다.
    + 설명서에서 사용 되지 않는 lag_length 매개 변수를 제거 합니다.
    + 예측 매개 변수 설명서가 개선 되었습니다. Lag_length 매개 변수는 사용 되지 않습니다.
  + **azureml-train-automl-runtime**
    + Prophet 모델을 포함 하는 AutoML Prophet 및 ensembled 모델에 대 한 설명을 사용 하도록 설정 합니다.
    + Azureml-자동 ml-* 패키지에 대 한 설명서 업데이트
  + **azureml-train-core**
    + PyTorch 평가기에서 TensorFlow 버전 2.1 지원
    + Azureml 교육-핵심 패키지의 향상 된 기능.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK for Python v 1.6.0

+ **새로운 기능**
  + **azureml-automl-runtime**
    + AutoML 예측은 이제 모델을 다시 학습 하지 않고 미리 지정 된 최대 수평을 초과 하는 고객의 예측을 지원 합니다. 예측 대상이 지정 된 최대 한도 보다 더 오래 지속 되 면 예측 () 함수는 재귀 작업 모드를 사용 하 여 나중에 지점 예측을 수행 합니다. 새 기능에 대 한 자세한 내용은 [폴더](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)의 "예측-예측-함수" 노트북의 "최대 수평 보다 더 많이 예측" 섹션을 참조 하세요.
  
  + **azureml-pipeline-steps**
    + ParallelRunStep는 이제 릴리스 되었으며 **azureml-파이프라인 단계** 패키지의 일부입니다. **Azureml** -ParallelRunStep 패키지의 기존는 사용 되지 않습니다. 공개 미리 보기 버전의 변경 내용:
      + `run_max_try`지정 된 일괄 처리에 대 한 실행 메서드에 대 한 max 호출을 제어 하는 구성 가능한 선택적 매개 변수를 추가 했습니다. 기본값은 3입니다.
      + 더 이상 생성 되는 PipelineParameters 없습니다. 다음 구성 가능한 값은 명시적으로 PipelineParameter로 설정할 수 있습니다.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Process_count_per_node의 기본값은 1로 변경 됩니다. 성능 향상을 위해 사용자가이 값을 조정 해야 합니다. 모범 사례는 GPU 또는 CPU 노드 수로를 설정 하는 것입니다.
      + ParallelRunStep는 패키지를 주입 하지 않으며, 사용자는 **azureml-코어** 및 **azureml-dataprep [pandas, 퓨즈]** 패키지를 환경 정의에 포함 해야 합니다. User_managed_dependencies와 함께 사용자 지정 docker 이미지를 사용 하는 경우 사용자가 이미지에 conda를 설치 해야 합니다.
      
+ **주요 변경 내용**
  + **azureml-pipeline-steps**
    + AutoMLConfig에 대 한 유효한 입력 형식으로 azureml를 사용 하지 않습니다.
  + **azureml-train-automl-client**
    + AutoMLConfig에 대 한 유효한 입력 형식으로 azureml를 사용 하지 않습니다.

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + `get_output`요청 하는 사용자가 클라이언트를 다운 그레이드 하는 동안 경고가 인쇄 될 수 있는 버그를 수정 했습니다.
    + 아직 버전 10에서 사용할 수 없으므로 cudatoolkit = 9.0을 사용 하도록 Mac을 업데이트 했습니다.
    + 원격 계산에 대해 학습 된 경우 phrophet 및 xgboost 모델에 대 한 제한을 제거 합니다.
    + AutoML의 향상 된 로깅
    + 예측 작업의 사용자 지정 기능화에 대 한 오류 처리가 향상 되었습니다.
    + 사용자가 예측을 생성 하기 위해 오래 된 상태인 기능을 포함할 수 있도록 하는 기능을 추가 했습니다.
    + 오류 메시지를 업데이트 하 여 사용자 오류를 올바르게 표시 합니다.
    + Training_data에서 사용할 cv_split_column_names 지원
    + 예외 메시지 로깅 및 traceback을 업데이트 합니다.
  + **azureml-automl-runtime**
    + Guardrails에 imputations 값을 예측 하도록 설정 합니다.
    + AutoML의 향상 된 로깅
    + 데이터 준비 예외에 대 한 세분화 된 오류 처리 추가
    + 원격 계산에 대해 학습 된 경우 phrophet 및 xgboost 모델에 대 한 제한을 제거 합니다.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime` 에는, 및에 대 한 종속성이 업데이트 되었습니다 `pytorch` `scipy` `cudatoolkit` . 이제 `pytorch==1.4.0` , 및를 지원 `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` 합니다.
    + 예측 작업의 사용자 지정 기능화에 대 한 오류 처리가 향상 되었습니다.
    + 예측 데이터 집합 빈도 검색 메커니즘이 향상 되었습니다.
    + 일부 데이터 집합에 대 한 Prophet 모델 학습 문제를 해결 했습니다.
    + 예측 중에 최대 수평의 자동 검색이 향상 되었습니다.
    + 사용자가 예측을 생성 하기 위해 오래 된 상태인 기능을 포함할 수 있도록 하는 기능을 추가 했습니다.
    +  예측 모델을 다시 학습 하지 않고 학습 된 수평 이상으로 예측을 제공할 수 있도록 예측 함수의 기능을 추가 합니다.
    + Training_data에서 사용할 cv_split_column_names 지원
  + **azureml-dnn-자동 ml-예측**
    + AutoML의 향상 된 로깅
  + **azureml-mir**
    + ManagedInferencing의 Windows 서비스에 대 한 지원이 추가 됨
    + Attach MIR compute와 같은 이전 MIR 워크플로를 제거 합니다. SingleModelMirWebservice 클래스-MIR 패키지에 배치 된 모델 프로 파일링
  + **azureml-contrib-pipeline-steps**
    + YAML 지원에 대 한 사소한 수정
    + ParallelRunStep는 일반 공급으로 릴리스 되었습니다. 즉, 사용 중단 알림이 있고 azureml. 단계로 이동 합니다.
  + **azureml-contrib-reinforcementlearning**
    + RL 부하 테스트 도구
    + RL 평가기에 스마트 기본값 있음
  + **azureml-core**
    + Attach MIR compute와 같은 이전 MIR 워크플로를 제거 합니다. SingleModelMirWebservice 클래스-MIR 패키지에 배치 된 모델 프로 파일링
    + 프로 파일링 실패 시 사용자에 게 제공 되는 정보를 수정 함: 포함 된 요청 id 및 메시지를 보다 의미 있게 다시 작성 합니다. 프로 파일링 러너에 새 프로 파일링 워크플로 추가
    + 데이터 집합 실행 실패가 발생 하는 경우 오류 텍스트가 크게 향상 되었습니다.
    + 작업 영역 개인 링크 CLI 지원이 추가 되었습니다.
    + 잘못 된 JSON이 `invalid_lines` `Dataset.Tabular.from_json_lines_files` 포함 된 줄을 처리 하는 방법을 지정할 수 있는 선택적 매개 변수를에 추가 했습니다.
    + 다음 릴리스에서 계산의 실행 기반 생성을 사용 중단 합니다. 실제 Amlcompute 클러스터를 영구 계산 대상으로 만들고, 실행 구성에서 계산 대상으로 클러스터 이름을 사용 하는 것이 좋습니다. 예제 노트북 참조: aka.ms/amlcomputenb
    + 데이터 집합 실행 실패가 발생 하는 경우 오류 메시지가 크게 향상 되었습니다.
  + **azureml-dataprep**
    + Pyarrow 버전을 더 명시적으로 업그레이드 하는 경고가 발생 했습니다.
    + 데이터 흐름을 실행 하는 데 실패 하는 경우 오류 처리 및 메시지 반환 기능이 향상 되었습니다.
  + **azureml-interpret**
    + Azureml의 문서 업데이트-패키지 해석
    + 최신 버전의 최신 버전 업데이트와 호환 되도록 interpretability 패키지 및 노트북 수정
  + **azureml-opendatasets**
    + 반환 된 데이터가 없으면 None을 반환 합니다.
    + To_pandas_dataframe의 성능을 향상 시킵니다.
  + **azureml-pipeline-core**
    + YAML에서 로드가 중단 된 ParallelRunStep에 대 한 빠른 수정
    + ParallelRunStep는 일반 공급으로 릴리스 되었습니다. 즉, 사용 중단 알림이 있고 azureml로 이동 합니다.-새로운 기능은 다음과 같습니다. 1. PipelineParameter 2로 서의 데이터 집합. 새 매개 변수 run_max_retry 3입니다. 구성 가능한 append_row 출력 파일 이름
  + **azureml-pipeline-steps**
    + 더 이상 사용 되지 않는 azureml를 입력 데이터의 올바른 형식으로 사용 합니다.
    + YAML에서 로드가 중단 된 ParallelRunStep에 대 한 빠른 수정
    + ParallelRunStep는 일반 공급으로 릴리스 되었습니다. 즉, 사용 중단 알림이 있고 azureml로 이동 합니다.-새로운 기능은 다음과 같습니다.
      + PipelineParameter로 서의 데이터 집합
      + 새 매개 변수 run_max_retry
      + 구성 가능한 append_row 출력 파일 이름
  + **azureml-telemetry**
    + 예외 메시지 로깅 및 traceback을 업데이트 합니다.
  + **azureml-train-automl-client**
    + AutoML의 향상 된 로깅
    + 오류 메시지를 업데이트 하 여 사용자 오류를 올바르게 표시 합니다.
    + Training_data에서 사용할 cv_split_column_names 지원
    + 더 이상 사용 되지 않는 azureml를 입력 데이터의 올바른 형식으로 사용 합니다.
    + 아직 버전 10에서 사용할 수 없으므로 cudatoolkit = 9.0을 사용 하도록 Mac을 업데이트 했습니다.
    + 원격 계산에 대해 학습 된 경우 phrophet 및 xgboost 모델에 대 한 제한을 제거 합니다.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime` 에는, 및에 대 한 종속성이 업데이트 되었습니다 `pytorch` `scipy` `cudatoolkit` . 이제 `pytorch==1.4.0` , 및를 지원 `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` 합니다.
    + 사용자가 예측을 생성 하기 위해 오래 된 상태인 기능을 포함할 수 있도록 하는 기능을 추가 했습니다.
  + **azureml-train-automl-runtime**
    + AutoML의 향상 된 로깅
    + 데이터 준비 예외에 대 한 세분화 된 오류 처리 추가
    + 원격 계산에 대해 학습 된 경우 phrophet 및 xgboost 모델에 대 한 제한을 제거 합니다.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime` 에는, 및에 대 한 종속성이 업데이트 되었습니다 `pytorch` `scipy` `cudatoolkit` . 이제 `pytorch==1.4.0` , 및를 지원 `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` 합니다.
    + 오류 메시지를 업데이트 하 여 사용자 오류를 올바르게 표시 합니다.
    + Training_data에서 사용할 cv_split_column_names 지원
  + **azureml-train-core**
    + 새 HyperDrive 특정 예외 집합을 추가 했습니다. 이제는 자세한 예외가 throw 됩니다.
  + **azureml-widgets**
    + AzureML 위젯은 JupyterLab에 표시 되지 않습니다.
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK for Python v 1.5.0

+ **새로운 기능**
  + **미리 보기 기능**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning는 [광선](https://ray.io) 프레임 워크를 사용 하 여 보충 Learning에 대 한 미리 보기 지원을 릴리스 합니다. 를 `ReinforcementLearningEstimator` 사용 하면 Azure Machine Learning의 GPU 및 CPU 계산 대상에서 보충 learning 에이전트의 학습을 수행할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 이전 PR의 경고 로그를 실수로 남겨진 문제를 수정 합니다. 로그가 디버깅에 사용 되었고 실수로는 남아 있습니다.
    + 버그 수정: 프로 파일링 하는 동안 클라이언트에 부분 오류를 알립니다.
  + **azureml-automl-core**
    + 데이터 집합에 여러 시계열이 있는 경우 시계열에 대 한 병렬 맞춤을 사용 하도록 설정 하 여 AutoML 예측에서 Prophet/AutoArima 모델을 가속화 합니다. 이 새로운 기능을 활용 하기 위해 AutoMLConfig에서 "max_cores_per_iteration =-1" (즉, 사용 가능한 모든 cpu 코어 사용)을 설정 하는 것이 좋습니다.
    + 콘솔 인터페이스에서 인쇄 guardrails의 KeyError 수정
    + Experimentation_timeout_hours에 대 한 오류 메시지 수정
    + AutoML에 사용 되지 않는 Tensorflow 모델입니다.
  + **azureml-automl-runtime**
    + Experimentation_timeout_hours에 대 한 오류 메시지 수정
    + 캐시 저장소에서 deserialize 할 때 미분류 예외가 수정 되었습니다.
    + 데이터 집합에 여러 시계열이 있는 경우 시계열에 대 한 병렬 맞춤을 사용 하도록 설정 하 여 AutoML 예측에서 Prophet/AutoArima 모델을 가속화 합니다.
    + 테스트/예측 집합에 학습 집합의 조직 중 하나가 포함 되어 있지 않은 데이터 집합에서 설정 된 롤링 창을 사용 하 여 예측을 수정 했습니다.
    + 누락 된 데이터의 향상 된 처리
    + 시간에 정렬 되지 않은 시계열을 포함 하 여 데이터 집합에 대해 예측 하는 동안 예측 간격이 문제를 해결 했습니다.
    + 예측 태스크에 대 한 데이터 셰이프의 유효성 검사가 향상 되었습니다.
    + 빈도 검색 기능이 향상 되었습니다.
    + 예측 태스크에 대 한 교차 유효성 검사 접기를 생성할 수 없는 경우 더 나은 오류 메시지가 생성 됩니다.
    + 누락 값 guardrail 올바르게 인쇄 되도록 콘솔 인터페이스를 수정 합니다.
    + AutoMLConfig의 cv_split_indices 입력에 대해 datatype 검사를 적용 합니다.
  + **azureml-cli-일반**
    + 버그 수정: 프로 파일링 하는 동안 클라이언트에 부분 오류를 알립니다.
  + **azureml-mir**
    + 현재 배포 된 MIR 수정 버전 및 사용자가 지정한 최신 버전에 대 한 정보를 릴레이 하는 mir 클래스를 추가 합니다. 이 클래스는 MirWebservice 개체의 ' deployment_status ' 특성에 포함 되어 있습니다.
    + MirWebservice 및 해당 자식 클래스 SingleModelMirWebservice의 Webservices에 대 한 업데이트를 사용 하도록 설정 합니다.
  + **azureml-contrib-reinforcementlearning**
    + 광선 0.8.3에 대 한 지원 추가
    + AmlWindowsCompute는 탑재 된 저장소로 Azure Files만 지원 합니다.
    + Health_check_timeout 이름이 health_check_timeout_seconds
    + 일부 클래스/메서드 설명을 수정 했습니다.
  + **azureml-core**
    + WASB-USGovernment 및 중국 클라우드에서 > Blob 변환을 사용 하도록 설정 합니다.
    + 판독기 역할에서 az ml run CLI 명령을 사용 하 여 실행 정보를 얻을 수 있도록 하는 버그를 수정 합니다.
    + 입력 데이터 집합을 사용 하 여 Azure ML 원격 실행 중에 불필요 한 로깅을 제거 했습니다.
    + 이제 RCranPackage는 CRAN 패키지 버전에 대 한 "version" 매개 변수를 지원 합니다.
    + 버그 수정: 프로 파일링 하는 동안 클라이언트에 부분 오류를 알립니다.
    + Azureml 코어에 대 한 유럽 스타일의 부동 처리를 추가 했습니다.
    + Azure ml sdk의 작업 영역 개인 링크 기능을 사용 하도록 설정 했습니다.
    + 을 사용 하 여 TabularDataset를 만들 때 `from_delimited_files` 부울 인수를 설정 하 여 빈 값을 None으로 로드할지 아니면 빈 문자열로 로드할지를 지정할 수 있습니다 `empty_as_string` .
    + 데이터 집합에 대 한 유럽 스타일의 부동 처리를 추가 했습니다.
    + 데이터 집합 탑재 오류에 대 한 오류 메시지가 개선 되었습니다.
  + **azureml-datadrift**
    + SDK의 데이터 드리프트 결과 쿼리에 최소, 최대 및 평균 기능 메트릭을 구분 하지 않아 중복 값이 발생 하는 버그가 있습니다. 메트릭 이름에 대상 또는 기준을 접두사로 하 여이 버그를 수정 했습니다. 이전: 중복 된 min, max, mean. 이후: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + 데이터 배달에 .NET 종속성이 필요한 경우 쓰기 제한 된 python 환경 처리를 향상 시킵니다.
    + 선행 빈 레코드가 있는 파일에 대 한 데이터 흐름 생성을 수정 했습니다.
    + 와 유사 하 게에 대 한 오류 처리 옵션이 추가 되었습니다 `to_partition_iterator` `to_pandas_dataframe` .
  + **azureml-interpret**
    + Windows 제한에 대 한 이동 가능성을 줄이도록 설명 경로 길이 제한을 줄였습니다.
    + 선형 서로게이트 모델을 사용 하 여 설명 모방로 생성 된 스파스 설명에 대 한 버그 수정
  + **azureml-opendatasets**
    + MNIST의 열 문제 해결은 int 여야 하는 문자열로 구문 분석 됩니다.
  + **azureml-pipeline-core**
    + ModuleStep에 포함 된 모듈을 사용 하는 경우 옵션을 regenerate_outputs 수 있습니다.
  + **azureml-train-automl-client**
    + AutoML에 사용 되지 않는 Tensorflow 모델입니다.
    + 사용자를 수정 하 여 지원 되지 않는 알고리즘을 로컬 모드로 나열할 수 있습니다.
    + AutoMLConfig에 대 한 문서 수정.
    + AutoMLConfig의 cv_split_indices 입력에 대해 datatype 검사를 적용 합니다.
    + Show_output에서 자동 Ml 실행 실패가 발생 하는 문제 해결
  + **azureml-train-automl-runtime**
    + 앙상블 반복에서 버그를 수정 하 여 시작에서 모델 다운로드 제한 시간을 초과 했습니다.
  + **azureml-train-core**
    + Dnn. Nccl 클래스에서 오타를 수정 합니다.
    + PyTorch 평가기에서 PyTorch 버전 1.5 지원
    + 학습 프레임 워크를 사용 하는 경우 Fairfax 지역에서 프레임 워크 이미지를 인출할 수 없는 문제를 해결 합니다. 추정

  
## <a name="2020-05-04"></a>2020-05-04
**새 노트북 환경**

이제 Azure Machine Learning의 스튜디오 웹 환경 내에서 직접 machine learning 노트와 파일을 만들고, 편집 하 고, 공유할 수 있습니다. 이러한 노트북 내에서 [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 에서 사용할 수 있는 모든 클래스와 메서드를 사용할 수 있습니다. [여기서](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks) 시작 하세요.

**도입 된 새로운 기능:**

+ VS Code에서 사용 하는 향상 된 편집기 (모나코 편집기) 
+ UI/UX 개선 사항
+ 셀 도구 모음
+ 새 노트북 도구 모음 및 계산 컨트롤
+ 노트북 상태 표시줄 
+ 인라인 커널 전환
+ R 지원
+ 접근성 및 지역화 개선 사항
+ 명령 팔레트
+ 추가 바로 가기 키
+ 끄려면
+ 향상 된 성능 및 안정성

스튜디오에서 다음 웹 기반 제작 도구에 액세스 합니다.
    
| 웹 기반 도구  |     Description  | 버전 | 
|---|---|---|
| Azure ML Studio 노트북   |     전자 필기장 파일의 첫 번째 내 클래스 작성 및 Azure ML Python SDK에서 사용할 수 있는 모든 작업을 지원 합니다. | Basic & Enterprise  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK for Python v 1.4.0

+ **새로운 기능**
  + 이제 AmlCompute 클러스터가 프로 비전 시 클러스터에서 관리 id를 설정 하도록 지원 합니다. 시스템 할당 id 또는 사용자 할당 id를 사용할지 여부를 지정 하 고 후자의 경우 identityId를 전달 합니다. 그런 다음 현재 AmlCompute에서 사용 하는 토큰 기반 방법 대신 계산 id를 사용 하 여 데이터에 안전 하 게 액세스 하는 방식으로 저장소나 ACR과 같은 다양 한 리소스에 액세스할 수 있는 권한을 설정할 수 있습니다. 매개 변수에 대 한 자세한 내용은 SDK 참조를 확인 하세요.
  

+ **주요 변경 내용**
  + AmlCompute 클러스터는 실행 기반 만들기에 대 한 미리 보기 기능을 지원 했습니다. 2 주 동안 사용 중단를 계획 하 고 있습니다. 언제 든 지 Amlcompute 클래스를 사용 하 여 영구 계산 대상을 계속 만들 수 있지만, 실행 구성에서 계산 대상으로 "amlcompute" 식별자를 지정 하는 특정 방식은 곧 지원 되지 않습니다. 

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-runtime**
    + 열에서 고유 값 수를 계산할 때 unhashable 형식을 지원 하도록 설정 합니다.
  + **azureml-core**
    + TabularDataset를 사용 하 여 Azure Blob Storage에서 읽을 때 안정성 향상.
    + 의 매개 변수에 대 한 설명서를 개선 `grant_workspace_msi` `Datastore.register_azure_blob_store` 했습니다.
    + `datastore.upload`또는으로 끝나는 인수를 지원 하기 위해를 사용 하 여 버그를 수정 `src_dir` `/` `\` 했습니다.
    + 액세스 키 또는 SAS 토큰이 없는 Azure Blob Storage 데이터 저장소에 업로드 하려고 할 때 조치 가능한 오류 메시지가 추가 되었습니다.
  + **azureml-interpret**
    + 업로드 된 설명에서 시각화 데이터의 파일 크기에 상한을 추가 했습니다.
  + **azureml-train-automl-client**
    + AutoMLConfig에 대 한 label_column_name & weight_column_name 매개 변수를 문자열 형식으로 명시적으로 확인 합니다.
  + **azureml-contrib-pipeline-steps**
    + 이제 ParallelRunStep에서 데이터 집합을 파이프라인 매개 변수로 지원 합니다. 사용자는 샘플 데이터 집합을 사용 하 여 파이프라인을 생성 하 고 새 파이프라인 실행을 위해 동일한 유형 (파일 또는 테이블 형식)의 입력 데이터 집합을 변경할 수 있습니다.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK for Python v 1.3.0

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 사후 학습 작업에 대 한 추가 원격 분석을 추가 했습니다.
    + 100 보다 긴 일련의 조건부 CSS (제곱 합계) 학습을 사용 하 여 자동 ARIMA 학습 속도를 향상 시킵니다. 사용 되는 길이는/src/azureml-automl-core/azureml/automl/core/shared/constants.py의 Times Esinternal 클래스에 있는 ARIMA_TRIGGER_CSS_TRAINING_LENGTH의 상수로 저장 됩니다.
    + 예측 실행에 대 한 사용자 로깅이 개선 되었습니다. 이제 현재 실행 중인 단계에 대 한 자세한 정보가 로그에 표시 됩니다.
    + 2 보다 작은 값으로 설정 될 target_rolling_window_size 허용 되지 않습니다.
  + **azureml-automl-runtime**
    + 중복 타임 스탬프가 있는 경우 표시 되는 오류 메시지가 개선 되었습니다.
    + 2 보다 작은 값으로 설정 될 target_rolling_window_size 허용 되지 않습니다.
    + 대체 지연 시간을 수정 했습니다. 계절별로를 분리 하는 데 필요한 관측값 수가 부족 하 여이 문제가 발생 했습니다. "Seasonalized" 데이터는 지연 길이를 확인 하기 위해 부분 autocorrelation 관계 함수 (PACF)를 계산 하는 데 사용 됩니다.
    + 기능화 config의 예측 태스크에 대 한 열 용도 기능화 사용자 지정을 사용 합니다. 예측 태스크에 대 한 열 용도가 있는 숫자 및 범주는 이제 지원 됩니다.
    + 기능화 config의 예측 태스크에 대 한 drop column 기능화 사용자 지정을 사용 하도록 설정 했습니다.
    + 기능화 config의 예측 작업에 대해 대체 사용자 지정을 사용 하도록 설정 했습니다. 대상 열에 대 한 상수 값과 학습 데이터의 평균, 중앙값, most_frequent 및 상수 값 대체이 지원 됩니다.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunConfig에 전달할 문자열 계산 이름을 허용 합니다.
  + **azureml-core**
    +  환경 개체의 복사본을 만들기 위한 new_name API를 추가 했습니다.
    +  Environment.docker는 filepath를 허용 base_dockerfile. 파일을 확인할 수 있는 경우 base_dockerfile 환경 속성으로 콘텐츠를 읽습니다.
    + 사용자가 Environment.docker에서 수동으로 값을 설정 하는 경우 base_image 및 base_dockerfile에 대해 상호 배타적인 값을 자동으로 다시 설정
    + 환경이 사용자에 의해 관리 되는지 또는 AzureML에 의해 관리 되는지 여부를 나타내는 RSection에 user_managed 플래그를 추가 했습니다.
    + 데이터 집합: 유니코드 문자를 포함 하는 데이터 경로인 경우 고정 데이터 집합 다운로드에 실패 합니다.
    + 데이터 집합: 데이터 집합 탑재 캐싱 메커니즘을 개선 하 여 노드를 사용할 수 없게 하 고 작업을 취소 하는 것을 방지 하는 Azure Machine Learning 계산의 최소 디스크 공간 요구 사항을 준수 합니다.
    + 데이터 집합: 시계열 데이터 집합에 액세스할 때 시계열 열에 대 한 인덱스를 추가 합니다. pandas 데이터 프레임는 시계열 기반 데이터 액세스에 대 한 액세스 속도를 높이는 데 사용 됩니다.  이전에는 인덱스에 타임 스탬프 열과 동일한 이름이 지정 되었으며,이는 실제 타임 스탬프 열과 인덱스에 대 한 혼동을 가진 사용자에 대 한 것입니다. 이제는 열로 사용할 수 없으므로 인덱스에 특정 이름을 지정 하지 않습니다. 
    + 데이터 집합: 소 버린 클라우드에서 데이터 집합 인증 문제를 수정 했습니다.
    + 데이터 집합: `Dataset.to_spark_dataframe` Azure PostgreSQL datastores에서 만든 데이터 집합에 대 한 오류를 수정 했습니다.
  + **azureml-interpret**
    + 로컬 중요도 값이 스파스 인 경우 시각화에 전역 점수를 추가 했습니다.
    + 업데이트 된 azureml-해석 사용-커뮤니티 0.9. *
    + 스파스 평가 데이터가 포함 된 다운로드 설명의 문제를 해결 함
    + AutoML에서 설명 개체의 스파스 형식에 대 한 지원이 추가 됨
  + **azureml-pipeline-core**
    + 파이프라인에서 계산 대상으로 서 계산 Einstance 지원
  + **azureml-train-automl-client**
    + 사후 학습 작업에 대 한 추가 원격 분석을 추가 했습니다.
    + 초기 중지에서 회귀 수정
    + 더 이상 사용 되지 않는 azureml를 입력 데이터의 올바른 형식으로 사용 합니다.
    +  기본 AutoML 실험 시간을 6 일로 변경 합니다.
  + **azureml-train-automl-runtime**
    + 사후 학습 작업에 대 한 추가 원격 분석을 추가 했습니다.
    + 추가 된 스파스 AutoML e2e 지원
  + **azureml-opendatasets**
    + 서비스 모니터에 대 한 추가 원격 분석을 추가 했습니다.
    + Blob에 대 한 front 도어를 사용 하 여 안정성 향상 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK for Python v 1.2.0

+ **주요 변경 내용**
  + Python 2.7에 대 한 Drop 지원

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + CLI의 명령에 "--subscription-id"를 추가 합니다. `az ml model/computetarget/service`
    + ACI 배포를 위한 CMK (고객 관리 키) vault_url, key_name 및 key_version를 전달 하기 위한 지원 추가
  + **azureml-automl-core** 
    + X 및 y 데이터 예측 작업 모두에 대해 상수 값으로 사용자 지정 된 대체를 사용 하도록 설정 했습니다.
    + 에서 사용자에 게 오류 메시지를 표시 하는 문제를 해결 했습니다.    
  + **azureml-automl-runtime**
    + 행을 하나만 포함 하는 조직 포함 하는 데이터 집합에 대 한 예측을 사용 하는 문제를 해결 했습니다.
    + 예측 작업에 필요한 메모리 양을 줄였습니다.
    + 시간 열에 잘못 된 형식이 있는 경우 더 나은 오류 메시지가 추가 되었습니다.
    + X 및 y 데이터 예측 작업 모두에 대해 상수 값으로 사용자 지정 된 대체를 사용 하도록 설정 했습니다.
  + **azureml-core**
    + AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID 및 AZUREML_SERVICE_PRINCIPAL_PASSWORD 환경 변수에서 ServicePrincipal을 로드 하기 위한 지원이 추가 되었습니다.
    + 에는에 대 한 새 매개 변수가 도입 `support_multi_line` `Dataset.Tabular.from_delimited_files` 되었습니다. 기본적으로 ( `support_multi_line=False` )에서는 따옴표 붙은 필드 값을 포함 하 여 모든 줄 바꿈이 레코드 중단으로 해석 됩니다. 이러한 방식으로 데이터를 읽으면 여러 CPU 코어에서 병렬 실행에 더 빠르고 최적화 됩니다. 그러나이로 인해 잘못 된 필드 값이 있는 더 많은 레코드가 자동으로 생성 될 수 있습니다. `True`구분 기호로 분리 된 파일이 따옴표 붙은 줄 바꿈을 포함 하는 것으로 알려진 경우이를로 설정 해야 합니다.
    + Azure Machine Learning CLI에 ADLS Gen2를 등록 하는 기능이 추가 됨
    + 매개 변수의 사용을 보다 잘 반영 하기 위해 TabularDataset의 with_timestamp_columns () 메서드에 대해 ' fine_grain_timestamp ' 매개 변수를 ' timestamp ' 및 ' coarse_grain_timestamp ' 매개 변수에 ' partition_timestamp '로 변경 했습니다.
    + 최대 실험 이름 길이를 255로 늘립니다.
  + **azureml-interpret**
    + 업데이트 된 azureml-해석-커뮤니티 0.7. *
  + **azureml-sdk**
    + 시험판 및 안정적인 릴리스에 대 한 패치 지원에 대해 호환 되는 버전 물결표를 사용 하는 종속성을 변경 합니다.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK for Python v 1.1.5

+ **기능 사용 중단**
  + **Python 2.7**
    + Python 2.7을 지 원하는 마지막 버전

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + 버전 1.1부터 Azure ML Python SDK는 의미 체계 버전 관리 2.0.0를 도입 합니다. [자세한 내용은 여기를 참조](https://semver.org/)하세요. 모든 후속 버전은 새 번호 매기기 체계 및 의미 체계 버전 관리 계약을 따릅니다. 

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 일관성을 위해 끝점 CLI 명령 이름을 ' az ml endpoint aks '에서 ' az ml endpoint real time '으로 변경 합니다.
    + 안정적이 고 실험적 분기 CLI에 대 한 CLI 설치 지침 업데이트
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
  + **azureml-automl-core**
    + AutoML ONNX 모델에 대해 일괄 처리 모드 유추를 사용 하도록 설정 합니다 (여러 행을 한 번 사용).
    + 데이터를 포함 하지 않거나 불규칙 한 데이터 요소를 포함 하는 데이터 집합의 빈도 검색 향상
    + 기준 빈도를 준수 하지 않는 데이터 요소를 제거 하는 기능이 추가 되었습니다.
    + 생성자의 입력이 해당 열에 대 한 대체 옵션을 적용 하는 옵션 목록을 사용 하도록 변경 되었습니다.
    + 오류 로깅이 개선 되었습니다.
  + **azureml-automl-runtime**
    + 테스트 집합에 표시 된 학습 집합에 그레인이 없는 경우 throw 되는 오류와 관련 된 문제를 해결 했습니다.
    + 예측 서비스의 점수를 매기는 동안 y_query 요구 사항이 제거 되었습니다.
    + 데이터 집합에 긴 시간 간격이 있는 short 조직 포함 된 경우 예측 관련 문제를 해결 했습니다.
    + Auto max 구간이 on으로 설정 되 고 date 열에 문자열 형식의 날짜가 포함 된 경우이 문제가 해결 됩니다. 날짜로의 변환이 불가능 한 경우에 대 한 적절 한 변환 및 오류 메시지가 추가 되었습니다.
    + 기본 NumPy 및 SciPy를 사용 하 여 FileCacheStore에 대 한 중간 데이터 직렬화 및 역직렬화 (로컬 AutoML 실행에 사용 됨)
    + 실패 한 자식 실행 상태가 중단 될 수 있는 버그를 수정 했습니다.
    + 기능화의 속도가 향상 되었습니다.
    + 점수 매기기 중 빈도 확인이 수정 되었습니다. 이제 예측 작업에 학습 및 테스트 집합 간에 엄격한 빈도가 동일 하지 않아도 됩니다.
    + 생성자의 입력이 해당 열에 대 한 대체 옵션을 적용 하는 옵션 목록을 사용 하도록 변경 되었습니다.
    + 지연 유형 선택과 관련 된 오류를 수정 했습니다.
    + 데이터 집합에 대해 발생 하는 분류 되지 않은 오류를 수정 하 여 단일 행이 있는 조직를 수정 했습니다.
    + Frequency detection 속도 저하와 관련 된 문제를 해결 했습니다.
    + 자동 Ml 예외 처리의 버그를 수정 하 여 학습 실패의 실제 원인을 AttributeError로 대체 합니다.
  + **azureml-cli-일반**
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
  + **azureml-mir**
    + MirWebservice 클래스의 기능을 추가 하 여 액세스 토큰을 검색 합니다.
    + MirWebservice () 호출을 수행 하는 동안 기본적으로 MirWebservice에 대 한 토큰 인증을 사용 합니다.
    + 이제 Mir webservice 배포에는 각각 [Ds2v2, A2v2 및 F16] 대신 적절 한 Sku [Standard_DS2_v2, Standard_F16, Standard_A2_v2]가 필요 합니다.
  + **azureml-contrib-pipeline-steps**
    + 선택적 매개 변수 side_inputs ParallelRunStep에 추가 됩니다. 이 매개 변수는 컨테이너에 폴더를 탑재 하는 데 사용할 수 있습니다. 현재 지원 되는 형식은 DataReference 및 PipelineData입니다.
    + ParallelRunConfig에 전달 된 매개 변수는 이제 파이프라인 매개 변수를 전달 하 여 덮어쓸 수 있습니다. 지원 되는 새 파이프라인 매개 변수 aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count 및 aml_process_count_per_node는 이미 이전 릴리스의 일부입니다.)
  + **azureml-core**
    + 배포 된 AzureML Webservices는 이제 기본적으로 `INFO` 로깅입니다. 이는 `AZUREML_LOG_LEVEL` 배포 된 서비스에서 환경 변수를 설정 하 여 제어할 수 있습니다.
    + Python sdk는 검색 서비스를 사용 하 여 ' 파이프라인 ' 대신 ' api ' 끝점을 사용 합니다.
    + 모든 SDK 호출에서 새 경로로 바꿉니다.
    + ModelManagementService 호출을 새 통합 구조로 라우팅하는 변경 되었습니다.
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 되었습니다.
      + 사용자가 이미지 빌드에 대 한 계산을 업데이트할 수 있도록 작업 영역 업데이트 메서드에서 image_build_compute 매개 변수를 추가 했습니다.
    + 이전 프로 파일링 워크플로에 사용 중단 메시지를 추가 했습니다. 프로 파일링 cpu 및 메모리 제한을 수정 했습니다.
    + R 작업을 실행 하는 환경의 일부로 RSection을 추가 했습니다.
    + 데이터 집합의 원본에 액세스할 수 없거나 데이터를 포함 하지 않는 경우에 유효성 검사를 추가 하 `Dataset.mount` 여 오류를 발생 시킵니다.
    + `--grant-workspace-msi-access`VNet 뒤에 있는 Blob 컨테이너를 등록 하는 데 사용할 수 있는 Azure Blob 컨테이너를 등록 하기 위한 데이터 저장소 CLI의 추가 매개 변수로 추가 되었습니다.
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
    + Aks.py _deploy에서 문제를 해결 했습니다.
    + 자동 저장소 오류를 방지 하기 위해 업로드할 모델의 무결성을 검사 합니다.
    + 이제 사용자가 webservices에 대 한 키를 다시 생성할 때 auth 키에 대 한 값을 지정할 수 있습니다.
    + 대문자를 데이터 집합의 입력 이름으로 사용할 수 없는 버그를 수정 했습니다.
  + **azureml-기본값**
    + `azureml-dataprep` 이제의 일부로 설치 됩니다 `azureml-defaults` . 데이터 집합을 탑재 하려면 더 이상 계산 대상에 데이터 준비 [퓨즈]를 수동으로 설치 하지 않아도 됩니다.
  + **azureml-interpret**
    + 업데이트 된 azureml-해석-커뮤니티 0.6. *
    + 업데이트 된 azureml-해석-커뮤니티 0.5.0에 종속 된 것으로 해석
    + Azureml-해석에 azureml 스타일 예외를 추가 했습니다.
    + Keras 모델에 대 한 DeepScoringExplainer serialization 수정
  + **azureml.mlflow**
    + 소 버린 클라우드에 대 한 지원을 azureml mlflow에 추가 합니다.
  + **azureml-pipeline-core**
    + 이제 파이프라인 일괄 처리 점수 매기기 노트북에서 ParallelRunStep 사용
    + 인수 목록을 변경 하는 경우에도 PythonScriptStep 결과가 잘못 재사용 될 수 있는 버그를 수정 했습니다.
    + 에서 parse_ * 메서드를 호출할 때 열 형식을 설정 하는 기능이 추가 되었습니다. `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + 를 `AutoMLStep` `azureml-pipeline-steps` 패키지로 이동 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
    + 데이터 집합에 대 한 문서 예제를 PythonScriptStep 입력으로 추가 했습니다.
  + **azureml-tensorboard**
    + tensorflow 2.0를 지원 하도록 azureml tensorboard 업데이트
    + 계산 인스턴스에서 사용자 지정 Tensorboard 포트를 사용 하는 경우 올바른 포트 번호 표시
  + **azureml-train-automl-client**
    + 특정 패키지가 원격 실행 시 잘못 된 버전에 설치 될 수 있는 문제를 해결 했습니다.
    + 사용자 지정 기능화 config를 필터링 하는 FeaturizationConfig 재정의 문제를 수정 했습니다.
  + **azureml-train-automl-runtime**
    + 원격 실행에서 빈도 감지와 관련 된 문제를 해결 했습니다.
    + 패키지에서을 (를) 이동 `AutoMLStep` `azureml-pipeline-steps` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + PyTorch 평가기에서 PyTorch 버전 1.4 지원
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.2 rc0 (시험판)

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoML ONNX 모델에 대해 일괄 처리 모드 유추를 사용 하도록 설정 합니다 (여러 행을 한 번 사용).
    + 데이터를 포함 하지 않거나 불규칙 한 데이터 요소를 포함 하는 데이터 집합의 빈도 검색 향상
    + 기준 빈도를 준수 하지 않는 데이터 요소를 제거 하는 기능이 추가 되었습니다.
  + **azureml-automl-runtime**
    + 테스트 집합에 표시 된 학습 집합에 그레인이 없는 경우 throw 되는 오류와 관련 된 문제를 해결 했습니다.
    + 예측 서비스의 점수를 매기는 동안 y_query 요구 사항이 제거 되었습니다.
  + **azureml-mir**
    + MirWebservice 클래스의 기능을 추가 하 여 액세스 토큰을 검색 합니다.
  + **azureml-core**
    + 배포 된 AzureML Webservices는 이제 기본적으로 `INFO` 로깅입니다. 이는 `AZUREML_LOG_LEVEL` 배포 된 서비스에서 환경 변수를 설정 하 여 제어할 수 있습니다.
    + `Dataset.get_all`작업 영역에 등록 된 모든 데이터 집합을 반환 하는 반복을 수정 합니다.
    + 데이터 집합 생성 Api의 인수에 잘못 된 형식이 전달 되 면 오류 메시지를 개선 `path` 합니다.
    + Python sdk는 검색 서비스를 사용 하 여 ' 파이프라인 ' 대신 ' api ' 끝점을 사용 합니다.
    + 모든 SDK 호출에서 새 경로로 전환 합니다.
    + ModelManagementService 호출을 새 통합 구조로 라우팅하는 변경
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 되었습니다.
      + 사용자가 이미지 빌드에 대 한 계산을 업데이트할 수 있도록 작업 영역 업데이트 메서드에서 image_build_compute 매개 변수를 추가 했습니다.
    +  이전 프로 파일링 워크플로에 사용 중단 메시지를 추가 했습니다. 프로 파일링 cpu 및 메모리 제한 수정
  + **azureml-interpret**
    + 업데이트 azureml-해석-커뮤니티 0.6. *
  + **azureml.mlflow**
    + 소 버린 클라우드에 대 한 지원을 azureml mlflow에 추가 합니다.
  + **azureml-pipeline-steps**
    + `AutoMLStep`을로 이동 `azureml-pipeline-steps package` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + 특정 패키지가 원격 실행 시 잘못 된 버전에 설치 될 수 있는 문제를 해결 했습니다.
  + **azureml-train-automl-runtime**
    + 원격 실행에서 빈도 감지와 관련 된 문제를 해결 했습니다.
    + `AutoMLStep`을로 이동 `azureml-pipeline-steps package` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + `AutoMLStep`을로 이동 `azureml-pipeline-steps package` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.1 rc0 (시험판)

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
  + **azureml-automl-core**
    + 오류 로깅이 개선 되었습니다.
  + **azureml-automl-runtime**
    + 데이터 집합에 긴 시간 간격이 있는 short 조직 포함 된 경우 예측 관련 문제를 해결 했습니다.
    + Auto max 구간이 on으로 설정 되 고 date 열에 문자열 형식의 날짜가 포함 된 경우이 문제가 해결 됩니다. 날짜로 변환할 수 없는 경우 적절 한 변환과 적절 한 오류를 추가 했습니다.
    + 기본 NumPy 및 SciPy를 사용 하 여 FileCacheStore에 대 한 중간 데이터 직렬화 및 역직렬화 (로컬 AutoML 실행에 사용 됨)
    + 실패 한 자식 실행 상태가 중단 될 수 있는 버그를 수정 했습니다.
  + **azureml-cli-일반**
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
  + **azureml-core**
    + `--grant-workspace-msi-access`VNet 뒤에 있는 Blob 컨테이너를 등록 하는 데 사용할 수 있는 Azure Blob 컨테이너를 등록 하기 위한 데이터 저장소 CLI의 추가 매개 변수로 추가 되었습니다.
    + 단일 인스턴스 프로 파일링이 권장 사항을 생성 하기 위해 수정 되었으며 핵심 sdk에서 제공 되었습니다.
    + Aks.py _deploy에서 문제를 해결 했습니다.
    + 자동 저장소 오류를 방지 하기 위해 업로드할 모델의 무결성을 검사 합니다.
  + **azureml-interpret**
    + azureml-해석에 azureml 스타일 예외를 추가 했습니다.
    + keras 모델에 대 한 DeepScoringExplainer serialization 수정
  + **azureml-pipeline-core**
    + 이제 파이프라인 일괄 처리 점수 매기기 노트북에서 ParallelRunStep 사용
  + **azureml-pipeline-steps**
    + 패키지에서을 (를) 이동 `AutoMLStep` `azureml-pipeline-steps` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + 선택적 매개 변수 side_inputs ParallelRunStep에 추가 됩니다. 이 매개 변수는 컨테이너에 폴더를 탑재 하는 데 사용할 수 있습니다. 현재 지원 되는 형식은 DataReference 및 PipelineData입니다.
  + **azureml-tensorboard**
    + tensorflow 2.0를 지원 하도록 azureml tensorboard 업데이트
  + **azureml-train-automl-client**
    + 사용자 지정 기능화 config를 필터링 하는 FeaturizationConfig 재정의 문제를 수정 했습니다.
  + **azureml-train-automl-runtime**
    + 패키지에서을 (를) 이동 `AutoMLStep` `azureml-pipeline-steps` 했습니다. 에서 사용 되지 않습니다 `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + PyTorch 평가기에서 PyTorch 버전 1.4 지원
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.0 rc0 (시험판)

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + 버전 1.1부터 Azure ML Python SDK는 의미 체계 버전 관리 2.0.0를 도입 합니다. [자세한 내용은 여기를 참조](https://semver.org/)하세요. 모든 후속 버전은 새 번호 매기기 체계 및 의미 체계 버전 관리 계약을 따릅니다. 
  
+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-runtime**
    + 기능화의 속도가 향상 되었습니다.
    + 점수 매기기 중 빈도 확인을 수정 했습니다. 이제 예측 작업에서 학습 및 테스트 집합 간에 엄격한 빈도가 동일 하지 않습니다.
  + **azureml-core**
    + 이제 사용자가 webservices에 대 한 키를 다시 생성할 때 auth 키에 대 한 값을 지정할 수 있습니다.
  + **azureml-interpret**
    + 업데이트 된 azureml-해석-커뮤니티 0.5.0에 종속 된 것으로 해석
  + **azureml-pipeline-core**
    + 인수 목록을 변경 하는 경우에도 PythonScriptStep 결과가 잘못 재사용 될 수 있는 버그를 수정 했습니다.
  + **azureml-pipeline-steps**
    + 데이터 집합에 대 한 문서 예제를 PythonScriptStep 입력으로 추가 했습니다.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunConfig에 전달 된 매개 변수는 이제 파이프라인 매개 변수를 전달 하 여 덮어쓸 수 있습니다. 지원 되는 새 파이프라인 매개 변수 aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count 및 aml_process_count_per_node는 이미 이전 릴리스의 일부입니다.)
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK for Python v 1.0.85

+ **새로운 기능**
  + **azureml-core**
    + 지정 된 작업 영역 및 구독에서 AmlCompute 리소스에 대 한 현재 코어 사용량 및 할당량 제한을 가져옵니다.
  
  + **azureml-contrib-pipeline-steps**
    + 사용자가 테이블 형식 데이터 집합을 이전 단계에서 parallelrunstep로 중간 결과로 전달 하도록 설정

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-runtime**
    + 배포 된 예측 서비스에 대 한 요청의 y_query 열에 대 한 요구 사항이 제거 되었습니다. 
    + ' Y_query '이 (가) Dominick의 주황색 주스 노트북 서비스 요청 섹션에서 제거 되었습니다.
    + 날짜 시간 열이 포함 된 데이터 집합에서 작동 하는 배포 된 모델에 대 한 예측을 방지 하는 버그를 수정 했습니다.
    + Matthews 상관 계수를 이진 및 다중 클래스 분류에 대 한 분류 메트릭으로 추가 했습니다.
  + **azureml-contrib-interpret**
    + Azureml에서 텍스트 explainers 제거 됨-텍스트 설명이 곧 출시 될 해석 텍스트 리포지토리로 이동 되었습니다.
  + **azureml-core**
    + 데이터 집합: 파일 데이터 집합에 대 한 사용은 더 이상 python env에 설치 되는 numpy 및 pandas에 종속 되지 않습니다.
    + 상태 끝점에 대 한 ping을 시도 하기 전에 로컬 Docker 컨테이너의 상태를 확인 하 고 실패 한 배포를 보고 하는 데 걸리는 시간을 크게 줄여 LocalWebservice. wait_for_deployment ()를 변경 했습니다.
    + Localwebservice () 생성자를 사용 하 여 기존 배포에서 서비스 개체를 만들 때 LocalWebservice. reload ()에서 사용 되는 내부 속성의 초기화가 수정 되었습니다.
    + 설명에 대 한 오류 메시지를 편집 했습니다.
    + 액세스 토큰, 타임 스탬프 이후 새로 고침, 타임 스탬프 및 토큰 형식에 대 한 만료를 포함 하는 AksServiceAccessToken 개체를 반환 하는 AksWebservice () get_access_token 라는 새 메서드를 추가 했습니다. 
    + AksWebservice에서 사용 되지 않는 기존 get_token () 메서드는 새 메서드는이 메서드가 반환 하는 모든 정보를 반환 합니다.
    + Az ml service get-access 토큰 명령의 출력을 수정 했습니다. 토큰 이름을 accessToken로 바꾸고 refreshby를 Refreshby로 바꿉니다. ExpiryOn 및 tokenType 속성을 추가 했습니다.
    + 고정 get_active_runs
  + **azureml-explain-model**
    + shap를 0.33.0로 업데이트 하 고-community를 0.4으로 해석 합니다. *
  + **azureml-interpret**
    + shap를 0.33.0로 업데이트 하 고-community를 0.4으로 해석 합니다. *
  + **azureml-train-automl-runtime**
    + Matthews 상관 계수를 이진 및 다중 클래스 분류에 대 한 분류 메트릭으로 추가 했습니다.
    + 사용 중단는 코드에서 전처리 플래그를 설정 하 고 기능화-기능화은 기본적으로 설정 되어 있습니다.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK for Python v 1.0.83

+ **새로운 기능**
  + 데이터 집합: 두 옵션을 추가 하 `on_error` 고, `out_of_range_datetime` `to_pandas_dataframe` 데이터에 오류 값이 포함 된 경우 데이터를로 채우지 않고 실패 `None` 합니다.
  + 작업 영역: `hbi_workspace` 추가 암호화를 사용 하 고 작업 영역에서 고급 진단을 사용 하지 않도록 설정 하는 중요 한 데이터가 포함 된 작업 영역에 대 한 플래그 또한 작업 영역을 만들 때 및 매개 변수를 지정 하 여 연결 된 Cosmos DB 인스턴스에 대 한 사용자 고유의 키를 가져오기 위한 지원을 추가 했습니다. 그러면 작업 `cmk_keyvault` `resource_cmk_uri` 영역을 프로 비전 하는 동안 구독에 Cosmos DB 인스턴스를 만듭니다. [여기서 자세히 알아보세요.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-runtime**
    + 3.5.4 아래의 Python 버전에서 AutoML을 실행할 때 TypeError 발생 하는 재발 문제를 수정 했습니다.
  + **azureml-core**
    + `datastore.upload_files`로 시작 하지 않은 상대 경로를 사용할 수 없는에서 버그가 수정 `./` 되었습니다.
    + 모든 이미지 클래스 codepaths에 대해 사용 되지 않는 메시지 추가
    + Azure 중국 21Vianet 지역에 대 한 모델 관리 URL 생성을 수정 했습니다.
    + Source_dir를 사용 하는 모델을 Azure Functions 패키지할 수 없는 문제를 해결 했습니다.    
    + [Build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 에 옵션을 추가 하 여 AzureML 작업 영역 컨테이너 레지스트리에 이미지를 푸시합니다.
    + 이전 버전과 호환 되는 방식으로 Azure synapse에서 새로운 토큰 라이브러리를 사용 하도록 SDK를 업데이트 했습니다.
  + **azureml-interpret**
    + 다운로드할 수 있는 설명이 없는 경우 아무것도 반환 되지 않는 버그를 수정 했습니다. 는 현재 다른 곳에서 일치 하는 동작을 발생 시킵니다.
  + **azureml-pipeline-steps**
    + 에서를 `DatasetConsumptionConfig` 사용 하 `Estimator` 는 `inputs` 경우의 매개 변수에를 전달할 수 `Estimator` `EstimatorStep` 없습니다.
  + **azureml-sdk**
    + 자동 ml 클라이언트를 azureml sdk 패키지에 추가 하 여 전체 AutoML 패키지를 설치 하지 않고 원격 AutoML 실행을 전송할 수 있도록 합니다.
  + **azureml-train-automl-client**
    + AutoML 실행에 대 한 콘솔 출력의 맞춤을 수정 했습니다.
    + Pandas의 잘못 된 버전이 원격 amlcompute에 설치 될 수 있는 버그를 수정 했습니다.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK for Python v 1.0.81

+ **버그 수정 및 향상 된 기능**
  + **azureml-contrib-interpret**
    + shap 종속성을 해석 하 여 azureml의 community-해석
  + **azureml-core**
    + 이제 계산 대상을 해당 배포 구성 개체에 대 한 매개 변수로 지정할 수 있습니다. 특히 SDK 개체가 아닌 배포할 계산 대상의 이름입니다.
    + 모델 및 서비스 개체에 CreatedBy 정보를 추가 했습니다. 는를 통해 액세스할 수 있습니다 <var> created_by
    + Docker 컨테이너의 HTTP 포트를 올바르게 설정 하지 않은 Get-containerimage ()를 수정 했습니다.
    + `azureml-dataprep`CLI 명령에 대 한 옵션 만들기 `az ml dataset register`
    + `TabularDataset.to_pandas_dataframe`가 대체 판독기로 잘못 대체 되 고 경고를 출력 하는 버그를 수정 했습니다.
  + **azureml-explain-model**
    + shap 종속성을 해석 하 여 azureml의 community-해석
  + **azureml-pipeline-core**
    + `NotebookRunnerStep`파이프라인의 한 단계로 로컬 노트북을 실행 하는 새 파이프라인 단계가 추가 되었습니다.
    + PublishedPipelines, 일정 및 PipelineEndpoints에 대해 사용 되지 않는 get_all 함수를 제거 했습니다.
  + **azureml-train-automl-client**
    + AutoML에 대 한 입력으로 data_script의 사용 중단을 시작 했습니다.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK for Python v 1.0.79

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 로그할 featurizationConfig을 제거 했습니다.
      + "자동"/"꺼짐"/"사용자 지정"을 기록 하도록 로깅을 업데이트 했습니다.
  + **azureml-automl-runtime**
    + Pandas에 대 한 지원이 추가 되었습니다. Series and pandas. 열 데이터 형식을 검색 하는 범주입니다. 이전에는 numpy만 지원 됩니다.
      + 범주를 올바르게 처리 하는 관련 코드 변경 내용이 추가 되었습니다.
    + 예측 함수 인터페이스가 향상 되었습니다. y_pred 매개 변수를 선택 사항으로 만들었습니다. -Docstrings가 개선 되었습니다.
  + **azureml-contrib-dataset**
    + 레이블이 지정 된 데이터 집합을 탑재할 수 없는 버그를 수정 했습니다.
  + **azureml-core**
    + 에 대 한 버그 수정 `Environment.from_existing_conda_environment(name, conda_environment_name)` 사용자는 로컬 환경의 정확한 복제본 인 환경 인스턴스를 만들 수 있습니다.
    + 시계열 관련 데이터 집합 메서드가 기본적으로로 변경 되었습니다 `include_boundary=True` .
  + **azureml-train-automl-client**
    + 출력 표시가 false로 설정 된 경우 유효성 검사 결과가 인쇄 되지 않는 문제가 해결 되었습니다.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK for Python v 1.0.76

+ **주요 변경 내용**
  + Azureml-학습-AutoML 업그레이드 문제
    + Azureml-학습-automl>= 1.0.76<에서 1.0.76로 업그레이드 하면 부분 설치로 인해 일부 AutoML 가져오기가 실패 하 게 됩니다. 이 문제를 해결 하기 위해에 있는 설치 스크립트를 실행할 수 있습니다 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . 또는 pip를 직접 사용 하는 경우 다음을 수행할 수 있습니다.
      + "pip 설치--azureml 업그레이드-automl"
      + "pip 설치--무시-설치 된 azureml-클라이언트"
    + 또는 업그레이드 하기 전에 이전 버전을 제거할 수 있습니다.
      + "pip 제거 azureml-학습-automl"
      + "pip install azureml-학습-automl"

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-runtime**
    + 이제 AutoML은 이진 분류 태스크에 대 한 평균 스칼라 메트릭을 계산할 때 true 및 false 클래스를 모두 고려 합니다.
    + AzureML에서 기계 학습 및 교육 코드를 새 패키지 AzureML-AutoML-런타임으로 이동 했습니다.
  + **azureml-contrib-dataset**
    + `to_pandas_dataframe`다운로드 옵션을 사용 하 여 레이블이 지정 된 데이터 집합에서 호출 하는 경우 이제 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
    + 또는를 호출할 때 시계열 `keep_columns` `drop_columns` , 레이블 또는 이미지 열이 삭제 되는 경우 데이터 집합에 대해서도 해당 기능이 삭제 됩니다.
    + 개체 검색 태스크에 대 한 pytorch 로더의 문제를 수정 했습니다.
  + **azureml-contrib-interpret**
    + 제거 된 설명 대시보드 위젯이 azureml에서 새 interpret_community 항목을 참조 하도록 변경 됩니다.
    + 업데이트 된 버전의 해석-커뮤니티에서 0.2.0로
  + **azureml-core**
    + 의 성능을 향상 시킵니다 `workspace.datasets` .
    + 사용자 이름 및 암호 인증을 사용 하 여 Azure SQL Database 데이터 저장소를 등록 하는 기능이 추가 됨
    + 상대 경로에서 RunConfigurations 로드를 수정 합니다.
    + `keep_columns`또는를 호출할 때 `drop_columns` 시계열 열이 삭제 되는 경우 데이터 집합에 대해서도 해당 기능이 삭제 됩니다.
  + **azureml-interpret**
    + 업데이트 된 버전의 해석-커뮤니티에서 0.2.0로
  + **azureml-pipeline-steps**
    + `runconfig_pipeline_params`Azure machine learning 파이프라인 단계에 대해 문서화 된 지원 값
  + **azureml-pipeline-core**
    + 파이프라인 명령에 대 한 json 형식으로 출력을 다운로드 하는 CLI 옵션이 추가 되었습니다.
  + **azureml-학습-automl**
    + AzureML-학습-AutoML을 두 개의 패키지로 분할 합니다. 클라이언트 패키지 AzureML-자동 Ml-클라이언트 및 ML 학습 패키지 AzureML-자동 Ml-런타임
  + **azureml-train-automl-client**
    + 로컬에서 machine learning 종속성을 설치할 필요 없이 AutoML 실험을 전송 하기 위한 씬 클라이언트를 추가 했습니다.
    + 원격 실행에서 자동으로 검색 된 지연, 롤링 창 크기 및 최대 horizons의 로깅이 수정 되었습니다.
  + **azureml-train-automl-runtime**
    + 클라이언트에서 machine learning 및 런타임 구성 요소를 격리 하는 새 AutoML 패키지를 추가 했습니다.
  + **azureml-고-교육-rl**
    + SDK에 보충 learning 지원이 추가 되었습니다.
    + RL SDK에서 AmlWindowsCompute 지원을 추가 했습니다.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK for Python v 1.0.74

  + **미리 보기 기능**
    + **azureml-contrib-dataset**
      + Azureml-데이터 집합을 가져온 후에는 대신를 호출 `Dataset.Labeled.from_json_lines` 하 여 `._Labeled` 레이블이 지정 된 데이터 집합을 만들 수 있습니다.
      + `to_pandas_dataframe`다운로드 옵션을 사용 하 여 레이블이 지정 된 데이터 집합에서 호출 하는 경우 이제 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
      + 또는를 호출할 때 시계열 `keep_columns` `drop_columns` , 레이블 또는 이미지 열이 삭제 되는 경우 데이터 집합에 대해서도 해당 기능이 삭제 됩니다.
      + 를 호출할 때 PyTorch loader와 관련 된 문제가 해결 `dataset.to_torchvision()` 되었습니다.

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + Preview CLI에 모델 프로 파일링을 추가 했습니다.
    + AzureML CLI가 실패 하는 Azure Storage의 주요 변경 사항을 수정 합니다.
    + AKS 형식에 대 한 Load Balancer 형식을 MLC에 추가 했습니다.
  + **azureml-automl-core**
    + 누락 된 값과 여러 조직 있는 시계열에 대 한 최대 마감일 검색 문제를 해결 했습니다.
    + 교차 유효성 검사 분할을 생성 하는 동안 오류가 발생 하는 문제를 해결 했습니다.
    + 이 섹션을 릴리스 정보에 표시 되는 markdown 형식의 메시지로 바꿉니다.-예측 데이터 집합에서 짧은 조직의 향상 된 처리.
    + 로깅하는 동안 일부 사용자 정보를 마스킹 하는 문제를 해결 했습니다. -예측 실행 중에 발생 하는 오류 로깅 향상
    + 자동 생성 된 iisnode.yml 배포 파일에 conda 종속성으로 psutil 추가
  + **azureml-mir**
    + AzureML CLI가 실패 하는 Azure Storage의 주요 변경 사항을 수정 합니다.
  + **azureml-core**
    + Azure Functions에 배포 된 모델을 생성 하 여 500s를 생성 하는 버그를 수정 합니다.
    + Amlignore 파일이 스냅숏에 적용 되지 않은 문제를 수정 했습니다.
    + 지정 된 amlcompute에서 실행 중 이며 지연 된 실행에 대 한 생성기를 반환 하는 get_active_runs 새 API amlcompute를 추가 했습니다.
    + AKS 형식에 대 한 Load Balancer 형식을 MLC에 추가 했습니다.
    + Run.py에서 download_files에 append_prefix bool 매개 변수를 추가 하 고 artifacts_client에서 download_artifacts_from_prefix. 이 플래그는 원본 파일 경로를 선택적으로 평면화 하는 데 사용 되므로 파일 또는 폴더 이름만 output_directory에 추가 됩니다.
    + 데이터 집합 사용에 대 한 deserialization 문제를 수정 `run_config.yml` 합니다.
    + `keep_columns`또는를 호출할 때 `drop_columns` 시계열 열이 삭제 되는 경우 데이터 집합에 대해서도 해당 기능이 삭제 됩니다.
  + **azureml-interpret**
    + 업데이트 된 해석-커뮤니티 버전 0.1.0.3
  + **azureml-학습-automl**
    + Automl_step 유효성 검사 문제를 인쇄 하지 않을 수 있는 문제를 해결 했습니다.
    + 모델 환경에 종속성이 로컬에서 누락 된 경우에도 수정 된 register_model 성공 합니다.
    + 일부 원격 실행이 docker를 사용 하도록 설정 되지 않은 문제를 해결 했습니다.
    + 로컬 실행이 중간에 실패 하도록 하는 예외의 로깅을 추가 합니다.
  + **azureml-train-core**
    + 자동화 된 하이퍼 매개 변수 튜닝 최상의 자식 실행의 계산에서 실행 resume_from을 고려 합니다.
  + **azureml-pipeline-core**
    + 파이프라인 인수 생성에서 매개 변수 처리가 수정 되었습니다.
    + 파이프라인 설명 및 단계 유형 yaml 매개 변수를 추가 했습니다.
    + 파이프라인 단계의 새 yaml 형식이 며 이전 형식에 대 한 사용 중단 경고가 추가 되었습니다.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>웹 환경

의 공동 작업 영역 방문 페이지는 [https://ml.azure.com](https://ml.azure.com) Azure Machine Learning studio (미리 보기)로 향상 되 고 다시 브랜드가 제공 되었습니다.

스튜디오에서 데이터 집합, 파이프라인, 모델, 끝점 등의 Azure Machine Learning 자산을 학습, 테스트, 배포 및 관리할 수 있습니다.

스튜디오에서 다음 웹 기반 제작 도구에 액세스 합니다.

| 웹 기반 도구 | Description | 버전 |
|-|-|-|
| 노트북 VM (미리 보기) | 완전히 관리 되는 클라우드 기반 워크스테이션 | Basic & Enterprise |
| [자동화 된 machine learning](tutorial-first-experiment-automated-ml.md) (미리 보기) | 기계 학습 모델 개발을 자동화 하기 위한 코드 환경 없음 | Enterprise |
| [Designer](concept-designer.md) (미리 보기) | 이전에 디자이너로 알려진 기계 학습 모델링 도구 끌어서 놓기 | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning designer 향상 된 기능

+ 이전의 시각적 인터페이스 
+    기능 엔지니어링, 교차 유효성 검사 및 데이터 변환을 포함 한 추천, 분류자 및 학습 유틸리티를 포함 한 새로운 [모듈](algorithm-module-reference/module-reference.md)

### <a name="r-sdk"></a>R SDK 
 
데이터 과학자 및 AI 개발자는 [R 용 AZURE MACHINE LEARNING SDK](tutorial-1st-r-experiment.md) 를 사용 하 여 Azure Machine Learning에서 기계 학습 워크플로를 빌드하고 실행 합니다.

R에 대 한 Azure Machine Learning SDK는 패키지를 사용 하 여 `reticulate` PYTHON SDK에 바인딩합니다. R 용 SDK를 사용 하면 Python에 직접 바인딩하여 선택한 R 환경에서 Python SDK에 구현 된 핵심 개체 및 메서드에 액세스할 수 있습니다.

SDK의 주요 기능에는 다음이 포함 됩니다.

+    기계 학습 실험을 모니터링, 로깅 및 구성하는 클라우드 리소스를 관리합니다.
+    GPU 가속 모델 교육을 비롯 한 클라우드 리소스를 사용 하 여 모델을 학습 합니다.
+    ACI (webservices on Azure Container Instances) 및 AKS (Azure Kubernetes Service)로 모델을 배포 합니다.

전체 설명서는 [패키지 웹 사이트](https://azure.github.io/azureml-sdk-for-r) 를 참조 하세요.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Event Grid와 Azure Machine Learning 통합 

현재 Event Grid에 대 한 리소스 공급자 Azure Machine Learning Azure Portal 또는 Azure CLI를 통해 Machine Learning 이벤트를 구성할 수 있습니다. 사용자는 실행 완료, 모델 등록, 모델 배포 및 데이터 드리프트 검색에 대 한 이벤트를 만들 수 있습니다. 이러한 이벤트는 Event Grid에서 지 원하는 이벤트 처리기로 라우팅될 수 있습니다. 자세한 내용은 machine learning 이벤트 [스키마](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) 및 [자습서](how-to-use-event-grid.md) 문서를 참조 하세요.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK for Python v 1.0.72

+ **새로운 기능**
  + [**Azureml datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) 패키지를 통해 데이터 집합 모니터를 추가 하 여 데이터 드리프트 또는 시간에 따른 기타 통계적 변화에 대 한 시계열 데이터 집합 모니터링을 허용 합니다. 드리프트가 검색 되거나 데이터의 다른 조건이 충족 되는 경우 경고 및 이벤트를 트리거할 수 있습니다. 자세한 내용은 [설명서](how-to-monitor-datasets.md) 를 참조 하세요.
  + Azure Machine Learning에서 두 개의 새로운 버전 (SKU 라고도 함)을 발표 합니다. 이 릴리스에서는 이제 Basic 또는 Enterprise Azure Machine Learning 작업 영역을 만들 수 있습니다. 모든 기존 작업 영역은 기본 버전으로 기본 설정 되며, Azure Portal 또는 스튜디오로 이동 하 여 언제 든 지 작업 영역을 업그레이드할 수 있습니다. Azure Portal에서 기본 또는 엔터프라이즈 작업 영역을 만들 수 있습니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 를 참조 하세요. SDK에서 작업 영역 개체의 "sku" 속성을 사용 하 여 작업 영역의 버전을 확인할 수 있습니다.
  + 또한 Azure Machine Learning 계산에 대 한 향상 된 기능을 제공 했습니다. 이제 디버깅을 위한 진단 로그를 볼 수 있을 뿐 아니라 Azure Monitor에서 클러스터의 메트릭 (예: 총 노드, 실행 중인 노드, 총 코어 할당량)을 볼 수 있습니다. 또한 클러스터에서 현재 실행 중이거나 큐에 대기 중인 실행 및 클러스터에 있는 다양 한 노드의 Ip와 같은 세부 정보를 볼 수도 있습니다. 포털에서 또는 SDK 또는 CLI에서 해당 하는 함수를 사용 하 여 볼 수 있습니다.

  + **미리 보기 기능**
    + Azure Machine Learning Compute에서 로컬 SSD의 디스크 암호화에 대 한 미리 보기 지원을 릴리스 합니다. 이 기능을 사용 하려면 구독 허용이 나열 되도록 기술 지원 티켓을 제기 하세요.
    + Azure Machine Learning 일괄 처리 유추의 공개 미리 보기입니다. Azure Machine Learning 일괄 처리 유추는 시간이 중요 하지 않은 대량 유추 작업을 대상으로 합니다. 일괄 처리 유추는 비동기 응용 프로그램에 대해 탁월한 처리량을 제공 하는 비용 효율적인 유추 계산 크기 조정 기능을 제공 합니다. 큰 데이터 컬렉션에 대 한 높은 처리량, 화재 및 기억나지 않는 유추에 최적화 되어 있습니다.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + 레이블이 지정 된 데이터 집합에 대 한 기능 사용
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 이제 CLI에서 모델 패키징을 지원 합니다.
    + 데이터 집합 CLI를 추가 했습니다. 자세한 내용: `az ml dataset --help`
    + InferenceConfig 인스턴스 없이 지원 되는 모델 (ONNX, scikit 및 TensorFlow)을 배포 하 고 패키징을 위한 지원이 추가 되었습니다.
    + SDK 및 CLI에서 서비스 배포 (ACI 및 AKS)에 대 한 덮어쓰기 플래그를 추가 했습니다. 제공 되는 경우 이름이 인 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없는 경우는 새 서비스를 만듭니다.
    + 모델은 Onnx 및 Tensorflow의 두 가지 새로운 프레임 워크로 등록할 수 있습니다. -모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대 한 리소스 구성을 허용 합니다.
  + **azureml-automl-core**
    + 반복 교육은 런타임 제약 조건이 설정 된 경우에만 자식 프로세스에서 실행 됩니다.
    + 지정 된 max_horizon가 지정 된 컴퓨터에서 메모리 문제를 발생 시키는 지 여부를 확인 하기 위해 예측 태스크에 대 한 guardrail를 추가 했습니다. 표시 되 면 guardrail 메시지가 표시 됩니다.
    + 2 년 및 1 개월과 같은 복잡 한 빈도에 대 한 지원이 추가 되었습니다. -Frequency를 확인할 수 없는 경우 이해도 오류 메시지가 추가 되었습니다.
    + Azureml 추가-모델 배포 오류를 해결 하기 위해 자동 생성 된 conda env에 기본값을 추가 합니다.
    + Azure Machine Learning 파이프라인의 중간 데이터를 테이블 형식 데이터 집합으로 변환 하 고에서 사용할 수 `AutoMLStep` 있습니다.
    + 스트리밍에 대 한 열 용도 업데이트를 구현 했습니다.
    + 스트리밍에 대 한  Uter 및 HashOneHotEncoder의 변환기 매개 변수 업데이트를 구현 했습니다.
    + 유효성 검사 오류 메시지에 현재 데이터 크기 및 필요한 최소 데이터 크기를 추가 했습니다.
    + 각 유효성 검사 접기에서 최소 두 개의 샘플을 보장 하기 위해 교차 유효성 검사에 필요한 최소 데이터 크기를 업데이트 했습니다.
  + **azureml-cli-일반**
    + 이제 CLI에서 모델 패키징을 지원 합니다.
    + 모델은 Onnx 및 Tensorflow의 두 가지 새로운 프레임 워크로 등록할 수 있습니다.
    + 모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대 한 리소스 구성을 허용 합니다.
  + **azureml-및 리브-gbdt**
    + 노트북의 릴리스 채널을 수정 했습니다.
    + 지원 하지 않는 AmlCompute 계산 대상에 대 한 경고가 추가 되었습니다.
    + LightGMB 평가기를 azureml에 추가 했습니다.
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + 이제 CLI에서 모델 패키징을 지원 합니다.
    + 사용 되지 않는 데이터 집합 Api에 대 한 사용 중단 경고를 추가 합니다. 에서 데이터 집합 API 변경 알림을 참조 https://aka.ms/tabular-dataset 하세요.
    + [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)데이터 집합이 등록 된 경우 등록 이름 및 버전 반환으로 변경 합니다.
    + ScriptRunConfig를 사용 하 여 데이터 집합을 사용 하는 버그를 수정 하 여 실험 실행을 제출할 수 없습니다.
    + 실행 중에 검색 된 데이터 집합은 추적 되 고 실행 세부 정보 페이지에 표시 되거나 실행이 완료 된 후를 호출 하 여 확인할 수 있습니다 [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) .
    + Azure Machine Learning 파이프라인의 중간 데이터를 테이블 형식 데이터 집합으로 변환 하 고에서 사용할 수 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) 있습니다.
    + InferenceConfig 인스턴스 없이 지원 되는 모델 (ONNX, scikit 및 TensorFlow)을 배포 하 고 패키징을 위한 지원이 추가 되었습니다.
    + SDK 및 CLI에서 서비스 배포 (ACI 및 AKS)에 대 한 덮어쓰기 플래그를 추가 했습니다. 제공 되는 경우 이름이 인 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없는 경우는 새 서비스를 만듭니다.
    +  모델은 Onnx 및 Tensorflow의 두 가지 새로운 프레임 워크로 등록할 수 있습니다. 모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대 한 리소스 구성을 허용 합니다.
    + Azure Database for MySQL에 대 한 새 데이터 저장소를 추가 했습니다. Azure Machine Learning 파이프라인에서 DataTransferStep의 Azure Database for MySQL 사용에 대 한 예제를 추가 했습니다.
    + 추가 된 기능을 추가 하 고 제거 하는 기능을 추가 하 여 실행에서 태그를 제거 하는 기능 추가
    + SDK 및 CLI에서 서비스 배포 (ACI 및 AKS)에 대 한 덮어쓰기 플래그를 추가 했습니다. 제공 되는 경우 이름이 인 서비스가 이미 있는 경우 기존 서비스를 덮어씁니다. 서비스가 없는 경우는 새 서비스를 만듭니다.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + 에서 `azureml-contrib-datadrift` 로 이동 `azureml-datadrift`
    + 드리프트 및 기타 통계 측정값에 대 한 시계열 데이터 집합 모니터링에 대 한 지원이 추가 됨
    + `create_from_model()` `create_from_dataset()` 클래스에 대 한 새 [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) 메서드 `create()`메서드는 더 이상 사용 되지 않습니다.
    + Azure Machine Learning studio에서 Python 및 UI의 시각화를 조정 합니다.
    + 데이터 집합 모니터의 일별 뿐만 아니라 주별 및 월별 모니터 일정을 지원 합니다.
    + 데이터 집합 모니터의 기록 데이터를 분석 하는 데이터 모니터 메트릭의 백필을 지원 합니다.
    + 다양한 버그 수정
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep는 파이프라인 파일에서 Azure Machine Learning 파이프라인 실행을 전송 하는 데 더 이상 필요 하지 않습니다 `yaml` .
  + [**azureml-학습-automl**](/python/api/azureml-train-automl-runtime/)
    + Azureml 추가-모델 배포 오류를 해결 하기 위해 자동 생성 된 conda env에 기본값을 추가 합니다.
    + 이제 AutoML 원격 학습에는 추론에 대 한 학습 env를 다시 사용할 수 있도록 azureml 기본값이 포함 됩니다.
  + **azureml-train-core**
    + 평가기에 PyTorch 1.3 지원이 추가 됨 [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch)

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>시각적 인터페이스 (미리 보기)

+ Azure Machine Learning 시각적 인터페이스 (미리 보기)는 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)에서 실행 점검 했습니다 되었습니다. 시각적 인터페이스에서 작성 된 파이프라인 (이전에는 실험 이라고 함)은 이제 핵심 Azure Machine Learning 환경과 완벽 하 게 통합 됩니다.
  + SDK 자산과 통합 된 관리 환경
  + 시각적 인터페이스 모델, 파이프라인 및 끝점에 대 한 버전 관리 및 추적
  + 새롭게 디자인된 UI
  + 추가 된 일괄 처리 유추 배포
  + 유추 계산 대상에 대 한 AKS (Azure Kubernetes Service) 지원 추가
  + 새 Python-단계 파이프라인 제작 워크플로
  + 시각적 제작 도구에 대 한 새 [방문 페이지](https://ml.azure.com)

+ **새 모듈**
  + 수학 연산 적용
  + SQL 변환 적용
  + 값 자르기
  + 데이터 요약
  + SQL Database에서 가져오기

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python v 1.0.69

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 모든 실행에 대 한 설명을 계산 하지 않고 모델 설명을 최상의 실행으로 제한 로컬, 원격 및 ADB에 대해이 동작을 변경 합니다.
    + UI에 대 한 요청 시 모델 설명에 대 한 지원 추가
    + Psutil을의 종속성으로 추가 `automl` 하 고 amlcompute에서 conda 종속성으로 포함 된 psutil을 추가 했습니다.
    + 예측 데이터 집합에서 추론 지연 및 롤링 창 크기에 대 한 문제를 해결 했습니다. 일부 시리즈는 선형 대 수 오류를 일으킬 수 있습니다.
      + 예측 실행에서 결정 된 발견적 매개 변수에 대 한 출력을 추가 했습니다.
  + **azureml-datadrift**
    + 첫 번째 섹션에 데이터 집합 수준 드리프트가 없는 경우 출력 메트릭을 만드는 동안 보호 기능이 추가 되었습니다.
  + **azureml-contrib-interpret**
    + azureml-설명-설명-모델 패키지의 이름이 azureml로 바뀌었습니다.
  + **azureml-core**
    + 데이터 집합의 등록을 취소 하는 API가 추가 되었습니다. `dataset.unregister_all_versions()`
    + azureml-설명-모델 패키지의 이름이 azureml-대/소문자 구분-해석으로 바뀌었습니다.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + 데이터 집합의 등록을 취소 하는 API가 추가 되었습니다. 집합. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + 데이터 변경 시간을 확인 하는 데이터 집합 API를 추가 했습니다. `dataset.data_changed_time`.
    + `FileDataset` `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` Azure Machine Learning 파이프라인에서 및을, 및에 대 한 입력으로 사용할 수 있습니다.
    + `FileDataset.mount`많은 수의 파일이 있는 폴더에 대 한 성능이 향상 되었습니다.
    + [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) 및 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) 을 Azure Machine Learning 파이프라인의 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)및 [hyperdrivestep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) 에 대 한 입력으로 사용할 수 있습니다.
    + FileDataset의 성능입니다. 여러 파일을 포함 하는 폴더에 대해 [mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) 가 개선 되었습니다.
    + 실행 세부 정보에 알려진 오류 권장 사항에 대 한 URL이 추가 되었습니다.
    + 실행 중인 버그를 수정 했습니다. 실행에 자식이 너무 많으면 요청이 실패 하는 get_metrics.
    + 실행 중인 버그를 수정 했습니다. 실행에 자식이 너무 많으면 요청이 실패 하는 [get_metrics.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-)
    + Arcadia 클러스터에서 인증에 대 한 지원이 추가 되었습니다.
    + 실험 개체를 만들면 실행 기록 추적을 위한 Azure Machine Learning 작업 영역에서 실험을 가져오거나 만듭니다. 실험 ID 및 보관 된 시간은 생성 시 실험 개체에 채워집니다. 예: 실험 = 실험 (작업 영역, "New 실험") experiment_id = experiment.id archive () 및 다시 활성화 ()는 실험에서 실험을 숨기고 복원 하기 위해 호출할 수 있는 함수입니다. 보관 된 실험과 동일한 이름으로 새 실험을 만든 경우 새 이름을 전달 하 여 다시 활성화할 때 보관 된 실험의 이름을 바꿀 수 있습니다. 지정 된 이름을 가진 활성 실험은 하나만 있을 수 있습니다. 예: experiment1 = 실험 (작업 영역, "활성 실험") experiment1 () # 보관 된와 동일한 이름을 사용 하 여 새 활성 실험을 만듭니다. experiment2. = 실험 (작업 영역, "활성 실험") experiment1 (new_name = "이전 활성 실험") 실험에서 정적 메서드 목록 ()은 이름 필터 및 ViewType 필터를 사용할 수 있습니다. ViewType 값에는 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL" 예제가 있습니다. archived_experiments = 실험. 목록 (작업 영역, view_type = "ARCHIVED_ONLY") all_first_experiments = 실험. 목록 (작업 영역, 이름 = "First 실험", view_type = "ALL")
    + 모델 배포 및 서비스 업데이트를 위한 환경 사용 지원
  + **azureml-datadrift**
    + Datadriftbariclass의 show 특성은 선택적 인수 ' with_details '을 (를) 더 이상 지원 하지 않습니다. 표시 특성은 기능 열의 데이터 드리프트 계수 및 데이터 드리프트 기여도만 제공 합니다.
    + DataDriftDetector 특성 ' get_output ' 동작 변경 내용:
      + 입력 매개 변수 start_time end_time는 필수 항목이 아닌 선택적 요소입니다.
      + 동일한 호출에서 특정 run_id를 사용 하는 입력 특정 start_time 및/또는 end_time는 함께 사용할 수 없으므로 값 오류 예외가 발생 합니다.
      + 입력 특정 start_time 및/또는 end_time를 통해 예약 된 실행 결과만 반환 됩니다.
      + ' Daily_latest_only ' 매개 변수는 사용 되지 않습니다.
    + 데이터 집합 기반 데이터 드리프트 출력 검색을 지원 합니다.
  + **azureml-explain-model**
    + Azureml-설명 모델의 이름을 AzureML로 해석 하 여 이전 패키지를 현재 이전 버전과의 호환성을 유지 합니다.
    + `automl`ExplanationClient에서 다운로드 시 기본적으로 회귀 대신 분류 태스크로 설정 된 원시 설명이 포함 된 버그가 수정 되었습니다.
    + 를 `ScoringExplainer` 사용 하 여 직접 만들 수 있도록에 대 한 지원 추가 `MimicWrapper`
  + **azureml-pipeline-core**
    + 대량 파이프라인 생성을 위한 향상 된 성능
  + **azureml-train-core**
    + TensorFlow 평가기에서 TensorFlow 2.0 지원을 추가 했습니다.
  + **azureml-학습-automl**
    + [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) 개체를 만들면 실행 기록 추적을 위한 Azure Machine Learning 작업 영역에서 실험을 가져오거나 만듭니다. 실험 ID 및 보관 된 시간은 생성 시 실험 개체에 채워집니다. 예:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) 및 [재 활성화 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) 는 실험에서 실험을 숨기 거 나 복원 하기 위해 호출할 수 있는 함수입니다 .이 함수는 실험에서 실험을 숨기 거 나 복원 하 여 목록 실험 호출에서 기본적으로 반환 됩니다. 보관 된 실험과 동일한 이름으로 새 실험을 만든 경우 새 이름을 전달 하 여 다시 활성화할 때 보관 된 실험의 이름을 바꿀 수 있습니다. 지정 된 이름을 가진 활성 실험은 하나만 있을 수 있습니다. 예:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        실험에서 정적 메서드 [목록 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) 은 이름 필터 및 ViewType 필터를 사용할 수 있습니다. ViewType 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL"입니다. 예:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 모델 배포 및 서비스 업데이트를 위한 환경 사용을 지원 합니다.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + [Datadriftdetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 클래스의 show 특성은 선택적 인수 ' with_details '을 (를) 더 이상 지원 하지 않습니다. 표시 특성은 기능 열의 데이터 드리프트 계수 및 데이터 드리프트 기여도만 제공 합니다.
    + DataDriftDetector 함수 [get_output] https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 동작 변경 내용:
      + 입력 매개 변수 start_time end_time는 필수 항목이 아닌 선택적 요소입니다.
      + 동일한 호출에서 특정 run_id를 사용 하는 입력 특정 start_time 및/또는 end_time는 함께 사용할 수 없으므로 값 오류 예외가 발생 합니다.
      + 입력 특정 start_time 및/또는 end_time를 통해 예약 된 실행 결과만 반환 됩니다.
      + ' Daily_latest_only ' 매개 변수는 사용 되지 않습니다.
    + 데이터 집합 기반 데이터 드리프트 출력 검색을 지원 합니다.
  + **azureml-explain-model**
    + 현재 이전 패키지와 이전 버전과의 호환성을 유지 하도록 azureml-설명 모델 패키지의 이름을 AzureML-해석으로 바꿉니다.
    + ExplanationClient에서 다운로드 시 기본적으로 회귀 대신 분류 태스크로 설정 된 원시 설명이 있는 AutoML 버그를 수정 했습니다.
    + MimicWrapper를 사용 하 여 직접 만들 [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) 에 대 한 지원 추가
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + 대량 파이프라인 생성을 위한 향상 된 성능.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 평가기에서 TensorFlow 2.0 지원을 추가 했습니다.
  + **[azureml-학습-automl](/python/api/azureml-train-automl-runtime/)**
    + 오케스트레이션이 이미 처리 하 고 있으므로 설치 반복에 실패 하는 경우 부모 실행은 더 이상 실패 하지 않습니다.
    + AutoML 실험에 대 한 로컬 docker 및 로컬 conda 지원 추가
    + AutoML 실험에 대 한 로컬 docker 및 로컬 conda 지원이 추가 되었습니다.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역에 대 한 새로운 웹 환경 (미리 보기)

데이터 과학자에서 실험을 보다 효율적으로 모니터링할 수 있도록 [새 작업 영역 포털](https://ml.azure.com) 의 실험 탭이 업데이트 되었습니다. 다음 기능을 탐색할 수 있습니다.
+ 실험 목록을 쉽게 필터링 하 고 정렬 하는 실험 메타 데이터
+ 실행을 시각화 하 고 비교할 수 있는 간소화 된 고성능 실험 세부 정보 페이지
+ 학습 실행을 이해 하 고 모니터링 하기 위해 세부 정보 페이지를 실행 하는 새로운 디자인

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v 1.0.65

  + **새로운 기능**
    + 큐 레이트 환경을 추가 했습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성 되었으며 더 빠른 실행을 위해 Docker 이미지로 미리 빌드 및 캐시 되었습니다. 기본적으로 작업 영역의 환경 목록에 접두사 "AzureML"와 함께 표시 됩니다.
    + 큐 레이트 환경을 추가 했습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성 되었으며 더 빠른 실행을 위해 Docker 이미지로 미리 빌드 및 캐시 되었습니다. 기본적으로 [작업 영역의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)환경 목록에 접두사 "AzureML"와 함께 표시 됩니다.

  + **azureml-학습-automl**
  + **[azureml-학습-automl](/python/api/azureml-train-automl-runtime/)**
    + ADB 및 HDI에 대 한 ONNX 변환 지원을 추가 했습니다.

+ **미리 보기 기능**
  + **azureml-학습-automl**
  + **[azureml-학습-automl](/python/api/azureml-train-automl-runtime/)**
    + 지원 되는 BERT 및 BiLSTM as text featurizer (미리 보기 전용)
    + 열 용도 및 변환기 매개 변수에 대해 지원 되는 기능화 사용자 지정 (미리 보기 전용)
    + 사용자가 학습 중에 모델 설명을 사용 하도록 설정 하는 경우 지원 되는 원시 설명 (미리 보기 전용)
    + `timeseries`학습 가능 파이프라인으로 예측에 Prophet 추가 됨 (미리 보기 전용)

  + **azureml-datadrift**
    + Azureml에서 재배치 되는 패키지-datadrift-datadrift; `contrib` 이후 릴리스에서 패키지가 제거 됩니다.

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoMLConfig 및 AutoMLBaseSettings에 FeaturizationConfig를 도입 했습니다.
    + [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 및 AutoMLBaseSettings에 FeaturizationConfig를 도입 했습니다.
      + 지정 된 열 및 기능 유형으로 기능화의 열 용도를 재정의 합니다.
      + 변환기 매개 변수 재정의
    + Explain_model () 및 retrieve_model_explanations에 대 한 사용 중단 메시지 추가 ()
    + 학습 가능 파이프라인으로 Prophet 추가 됨 (미리 보기 전용)
    + Explain_model () 및 retrieve_model_explanations ()에 대 한 사용 중단 메시지를 추가 했습니다.
    + Prophet를 학습 가능 파이프라인 (미리 보기 전용)으로 추가 했습니다.
    + 대상 지연, 롤링 창 크기 및 최대 가로선의 자동 검색에 대 한 지원이 추가 되었습니다. Max_horizon target_rolling_window_size target_lags 중 하나가 ' 자동 '으로 설정 된 경우 학습 데이터를 기반으로 해당 매개 변수의 값을 예측 하기 위해 추론을 적용 합니다.
    + 예측 수정-데이터 집합에 하나의 그레인 열이 포함 된 경우이 수준은 숫자 유형이 며 학습 및 테스트 집합 사이에 간격이 있습니다.
    + 예측 작업에서의 원격 실행에서 중복 된 인덱스에 대 한 오류 메시지를 수정 했습니다.
    + 예측 수정 데이터 집합에 하나의 그레인 열이 포함 된 경우이 수준은 숫자 유형이 며 학습 집합과 테스트 집합 사이에 간격이 있습니다.
    + 예측 작업에서 원격 실행의 중복 된 인덱스에 대 한 오류 메시지를 수정 했습니다.
    + 데이터 집합이 불균형 하 게 분산 되었는지 여부를 확인 하는 guardrail이 추가 되었습니다. 이 경우 guardrail 메시지가 콘솔에 기록 됩니다.
  + **azureml-core**
    + 모델 개체를 통해 저장소에서 모델에 대 한 SAS URL을 검색 하는 기능을 추가 했습니다. 예: 모델 get_sas_url ()
    + `run.get_details()['datasets']`제출 된 실행에 연결 된 데이터 집합을 가져오기 위해 소개
    + API `Dataset.Tabular.from_json_lines_files` 를 추가 하 여 JSON 줄 파일에서 TabularDataset를 만듭니다. TabularDataset의 JSON 줄 파일에서이 표 형식 데이터에 대해 자세히 알아보려면 설명서를 보려면 [이 문서](how-to-create-register-datasets.md) 를 참조 하세요.
    + Supported_vmsizes () 함수에 추가 VM 크기 필드 (OS 디스크, Gpu 수)를 추가 했습니다.
    + List_nodes () 함수에 추가 필드를 추가 하 여 실행, 개인 및 공용 IP, 포트 등을 표시 합니다.
    + 클러스터 프로 비전 중에 새 필드를 지정 하는 기능--클러스터를 만들 때 SSH 포트를 열어 둘지 여부에 따라 사용 또는 사용 안 함으로 설정할 수 있는 remotelogin_port_public_access. 지정 하지 않으면 서비스는 VNet 내에 클러스터를 배포 하 고 있는지 여부에 따라 포트를 열거나 현명 합니다.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + 모델 개체를 통해 저장소에서 모델에 대 한 SAS URL을 검색 하는 기능을 추가 했습니다. 예: model. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 실행을 소개 합니다. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' 데이터 집합 ']을 사용 하 여 제출 된 실행에 연결 된 데이터 집합을 가져옵니다.
    + API `Dataset.Tabular` 를 추가 합니다.[ from_json_lines_files ()을 (를)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 통해 Json 줄 파일에서 TabularDataset을 만듭니다. TabularDataset의 JSON 줄 파일에서이 테이블 형식 데이터에 대 한 자세한 내용은 설명서를 참조 하세요 https://aka.ms/azureml-data .
    + [Supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 함수에 추가 VM 크기 필드 (OS 디스크, gpu 수)를 추가 했습니다.
    + [List_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 함수에 추가 필드를 추가 하 여 실행, 개인 및 공용 IP, 포트 등을 표시 합니다.
    + 클러스터를 만들 때 SSH 포트를 열어 둘지 여부에 따라 클러스터를 [프로 비전](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) 하는 동안 새 필드를 지정 하는 기능을 `--remotelogin_port_public_access` 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 지정 하지 않으면 서비스는 VNet 내에 클러스터를 배포 하 고 있는지 여부에 따라 포트를 열거나 현명 합니다.
  + **azureml-explain-model**
    + 분류 시나리오의 설명 출력에 대 한 설명서를 개선 했습니다.
    + 평가 예제에 대 한 설명에 예측 된 y 값을 업로드 하는 기능이 추가 되었습니다. 더 유용한 시각화의 잠금을 해제 합니다.
    + 설명 속성을 MimicWrapper에 추가 하 여 기본 MimicExplainer를 가져올 수 있도록 합니다.
  + **azureml-pipeline-core**
    + 모듈, ModuleVersion 및 ModuleStep를 설명 하는 노트북을 추가 했습니다.
  + **azureml-pipeline-steps**
    + AML 파이프라인을 통해 R 스크립트 실행을 지 원하는 RScriptStep을 추가 했습니다.
    + "매개 변수 SubscriptionId에 대 한 할당이 지정 되지 않았습니다." 라는 오류 메시지를 발생 시킨 AzureBatchStep에서 메타 데이터 매개 변수가 구문 분석 되었습니다.
  + **azureml-학습-automl**
    + 데이터 입력 형식으로 지원 되는 training_data, validation_data, label_column_name, weight_column_name
    + Explain_model () 및 retrieve_model_explanations에 대 한 사용 중단 메시지 추가 ()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + [모듈](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion 및 [modulestep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)를 설명 하는 [노트북](https://aka.ms/pl-modulestep) 을 추가 했습니다.
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + AML 파이프라인을 통해 R 스크립트 실행을 지 원하는 [Rscriptstep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) 을 추가 했습니다.
    + "매개 변수 SubscriptionId에 대 한 할당이 지정 되지 않았습니다." 라는 오류 메시지를 발생 시킨 [AzureBatchStep]에서 메타 데이터 매개 변수가 구문 분석 되었습니다.
  + **[azureml-학습-automl](/python/api/azureml-train-automl-runtime/)**
    + 지원 되는 training_data, validation_data, label_column_name, weight_column_name 데이터 입력 형식입니다.
    + [Explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) 및 [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)에 대 한 사용 중단 메시지를 추가 했습니다.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v 1.0.62

+ **새로운 기능**
  + `timeseries`TabularDataset에 대 한 특성을 도입 했습니다. 이러한 특성을 사용 하 여 TabularDataset 데이터에 대 한 쉽게 타임 스탬프를 필터링 할 수 있습니다. 예를 들어 시간 범위 또는 최신 데이터의 모든 데이터를 가져올 수 있습니다.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 예제 노트북.
  + TabularDataset 및 FileDataset으로 학습을 사용 하도록 설정 했습니다. 

  + **azureml-train-core**
      + `Nccl` `Gloo` PyTorch 평가기에서 추가 및 지원

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + AutoML 설정 ' lag_length ' 및 LaggingTransformer 사용 되지 않습니다.
    + 데이터 흐름 형식으로 지정 된 경우 입력 데이터의 올바른 유효성 검사를 수정 했습니다.
    + Py fit_pipeline를 수정 하 여 graph json을 생성 하 고 아티팩트에 업로드 합니다.
    + 사용 중인 그래프를 `userrun` 렌더링 `Cytoscape` 했습니다.
  + **azureml-core**
    + ADB 코드에서 예외 처리를 확인 하 고 새 오류 처리에 따라를 변경 합니다.
    + 노트북 Vm에 대 한 자동 MSI 인증을 추가 했습니다.
    + 실패 한 재시도로 인해 손상 되었거나 비어 있는 모델을 업로드할 수 있는 버그를 수정 합니다.
    + `DataReference` `DataReference` 모드가 변경 될 때 (예: `as_upload` , 또는를 호출할 때 `as_download` `as_mount` ) 이름이 변경 되는 버그를 수정 했습니다.
    + `mount_point` `target_path` 및의 경우 및를 선택 `FileDataset.mount` `FileDataset.download` 합니다.
    + 할당 된 타임 스탬프 열을 할당 하지 않고 시간 serials 관련 API를 호출 하거나 할당 된 타임 스탬프 열을 삭제 한 경우에는 타임 스탬프 열을 찾을 수 없습니다. 예외가 throw 됩니다.
    + Serials 열에는 형식이 Date 인 열이 할당 되어야 합니다. 그렇지 않으면 예외가 예상 됩니다.
    + ' With_timestamp_columns ' API를 할당 하는 시간 serials 열은 이전에 할당 된 타임 스탬프 열을 제거 하는 None 값을 사용할 수 없습니다.
    + 드롭다운 with_time_stamp 목록에서 timestamp 열을 제외 하 고 삭제 하는 사용자에 대 한 표시를 사용 하 여 작업을 삭제 하는 사용자에 대 한 표시를 사용 하 여 예외를 발생 시킬 수 있습니다.
    + 열 사용 with_time_stamp 목록에 timestamp 열을 포함 하 여 열을 포함 하는 사용자에 대 한 표시와 함께 열 사용 목록에 타임 스탬프 열을 포함 하는 경우에는 예외를 발생 시킬 수 있습니다.
    + 등록 된 모델의 크기에 대 한 로깅을 추가 했습니다.
  + **azureml-explain-model**
    + "패키지" python 패키지가 설치 되지 않은 경우 콘솔에 수정 된 경고가 발생 했습니다. "lightgbm의 지원 되는 버전 보다 오래 된 버전을 사용 하려면 2.2.1 보다 큰 버전으로 업그레이드 하세요."
    + 다양 한 기능을 갖춘 글로벌 설명에 대 한 분할를 사용 하 여 다운로드 모델 설명 수정
    + 출력 설명에 대 한 설명 누락 된 초기화 예제를 수정 했습니다.
    + 두 가지 모델 유형을 사용 하는 설명 클라이언트를 사용 하 여 업로드할 때 설정 속성에 대 한 변경할 수 없는 오류 해결
    + 설명 ()의 점수를 매기는 get_raw 매개 변수를 추가 하 여 설명 한 점수 매기기에서 엔지니어링 된 값과 원시 값을 모두 반환할 수 있습니다.
  + **azureml-학습-automl**
    + Automl 기능화를 분리 `automl` 하 고 automl 모델에 대 한 azureml 설명 sdk의 Sdk 통합 원시 설명 지원을 설명 하는 방법 설명 sdk-최신 방법 설명에서 지원 하기 위해 automl의 공용 api를 도입 했습니다.
    + 원격 학습 환경에서 azureml 기본값 제거
    + Azure Databricks 코드 경로에서 AutoML에 대해 AzureFileCacheStore one으로 FileCacheStore의 기본 캐시 저장소 위치를 변경 했습니다.
    + 데이터 흐름 형식으로 지정 된 경우 입력 데이터의 올바른 유효성 검사를 수정 했습니다.
  + **azureml-train-core**
    + Source_directory_data_store 사용 중단을 되돌립니다.
    + Azureml 설치 패키지 버전을 재정의 하는 기능이 추가 되었습니다.
    + 추정의 매개 변수에 dockerfile 지원이 추가 되었습니다 `environment_definition` .
    + 추정의 단순화 된 분산 교육 매개 변수

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역에 대 한 새로운 웹 환경 (미리 보기)
새로운 웹 환경을 통해 데이터 과학자 및 데이터 엔지니어가 prepping에서 종단 간 기계 학습 수명 주기를 완료 하 고 데이터를 시각화 하 여 단일 위치에서 모델을 학습 하 고 배포할 수 있습니다.

![Azure Machine Learning 작업 영역 UI (미리 보기)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**주요 기능:**

이제이 새로운 Azure Machine Learning 인터페이스를 사용 하 여 다음을 수행할 수 있습니다.
+ 노트북 관리 또는 Jupyter에 연결
+ [자동화 된 ML 실험 실행](tutorial-first-experiment-automated-ml.md)
+ [로컬 파일, datastores, & 웹 파일에서 데이터 집합 만들기](how-to-create-register-datasets.md)
+ 모델을 만들기 위한 데이터 집합 준비 & 탐색
+ 모델에 대 한 데이터 드리프트 모니터링
+ 대시보드에서 최근 리소스 보기

이 릴리스에서는 Chrome, Firefox, Safari 및 Microsoft Edge Preview와 같은 브라우저가 지원 됩니다.

**알려진 문제:**

1. "문제가 발생 했습니다." 라는 메시지가 표시 되 면 브라우저를 새로 고칩니다. 배포가 진행 중일 때 "청크 파일을 로드 하는 동안 오류가 발생 했습니다.

1. 노트북 및 파일에서 파일을 삭제 하거나 이름을 바꿀 수 없습니다. 공개 미리 보기 중에는 노트북 VM에서 Jupyter UI 또는 터미널을 사용 하 여 파일 업데이트 작업을 수행할 수 있습니다. 탑재 된 네트워크 파일 시스템은 모두 변경 되므로 노트북 VM에 대 한 설정은 노트북 작업 영역에 즉시 반영 됩니다.

1. 노트북 VM으로 SSH를 수행 하려면:
   1. VM을 설치 하는 동안 생성 된 SSH 키를 찾습니다. 또는 Azure Machine Learning 작업 영역에서 키를 찾고, 목록에서 노트북 VM을 찾고, > 목록에서 노트북 VM을 찾아 해당 속성을 엽니다. 대화 상자에서 키 복사를 > > 합니다.
   1. 이러한 공개 및 개인 SSH 키를 로컬 컴퓨터로 가져옵니다.
   1. 이를 사용 하 여 노트북 VM에 SSH 합니다.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v 1.0.60

+ **새로운 기능**
  + 에는 데이터 저장소 또는 public url의 단일 또는 여러 파일을 참조 하는 filedataset이 도입 되었습니다. 파일은 모든 형식일 수 있습니다. FileDataset은 계산에 파일을 다운로드 하거나 탑재 하는 기능을 제공 합니다. 
  + PythonScript Step, Adla Step, Databricks Step, DataTransferStep 및 AzureBatch 단계에 대 한 파이프라인 Yaml 지원 추가 됨

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + 이제 AutoArima는 미리 보기용 으로만 suggestable 파이프라인입니다.
    + 예측에 대 한 오류 보고 기능이 향상 되었습니다.
    + 예측 작업에서 제네릭이 아닌 사용자 지정 예외를 사용 하 여 로깅이 향상 되었습니다.
    + Max_concurrent_iterations에 대 한 검사를 전체 반복 횟수 보다 작게 제거 했습니다.
    + AutoML 모델은 이제 AutoMLExceptions을 반환 합니다.
    + 이 릴리스는 자동화 된 machine learning 로컬 실행의 실행 성능을 향상 시킵니다.
  + **azureml-core**
    + 데이터 집합을 소개 합니다. get_all (작업 영역)은 `TabularDataset` `FileDataset` 등록 이름으로 키가 지정 된 및 개체의 사전을 반환 합니다.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + `parition_format`및에 인수를 `Dataset.Tabular.from_delimited_files` 도입 `Dataset.Tabular.from_parquet.files` 합니다. 각 데이터 경로의 파티션 정보는 지정 된 형식에 따라 열로 추출 됩니다. ' {column_name} '은 (는) 문자열 열을 만들며 ' {column_name: yyyy/MM/dd/HH/MM/ss} '는 datetime 열을 만듭니다. 여기서 ' yyyy ', ' MM ', ' dd ', ' HH ', ' mm ' 및 ' ss '는 datetime 형식의 연도, 월, 일, 시간 Partition_format는 파일 경로가 끝날 때까지 첫 번째 파티션 키의 위치부터 시작 해야 합니다. 예를 들어 '.. 경로를 지정 합니다. /USA/2019/01/01/data.csv ' 파티션이 국가별로, partition_format = '/{Country}/{PartitionDate: yyyy/MM/dd}/data.csv '은 값이 ' USA ' 인 ' Country ' 문자열 열과 ' 2019-01-01 ' 값이 포함 된 datetime 열을 만듭니다.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + `partition_format`및에 인수를 `Dataset.Tabular.from_delimited_files` 도입 `Dataset.Tabular.from_parquet.files` 합니다. 각 데이터 경로의 파티션 정보는 지정 된 형식에 따라 열로 추출 됩니다. ' {column_name} '은 (는) 문자열 열을 만들며 ' {column_name: yyyy/MM/dd/HH/MM/ss} '는 datetime 열을 만듭니다. 여기서 ' yyyy ', ' MM ', ' dd ', ' HH ', ' mm ' 및 ' ss '는 datetime 형식의 연도, 월, 일, 시간 Partition_format는 파일 경로가 끝날 때까지 첫 번째 파티션 키의 위치부터 시작 해야 합니다. 예를 들어 '.. 경로를 지정 합니다. /USA/2019/01/01/data.csv ' 파티션이 국가별로, partition_format = '/{Country}/{PartitionDate: yyyy/MM/dd}/data.csv '은 값이 ' USA ' 인 ' Country ' 문자열 열과 ' 2019-01-01 ' 값이 포함 된 datetime 열을 만듭니다.
    + `to_csv_files` 및 `to_parquet_files` 메서드가에 추가 되었습니다 `TabularDataset` . 이러한 메서드는 `TabularDataset` `FileDataset` 데이터를 지정 된 형식의 파일로 변환 하 여와 간의 변환을 가능 하 게 합니다.
    + 모델인 ()에서 생성 된 Dockerfile을 저장할 때 기본 이미지 레지스트리에 자동으로 로그인 합니다.
    + ' gpu_support '은 더 이상 필요 하지 않습니다. AML은 이제 사용할 수 있는 경우 nvidia docker 확장을 자동으로 검색 하 고 사용 합니다. 후속 릴리스에서 제거될 예정입니다.
    + PipelineDrafts을 만들고, 업데이트 하 고, 사용할 수 있는 지원이 추가 되었습니다.
    + 이 릴리스는 자동화 된 machine learning 로컬 실행의 실행 성능을 향상 시킵니다.
    + 사용자는 이름으로 실행 기록에서 메트릭을 쿼리할 수 있습니다.
    + 예측 작업에서 제네릭이 아닌 사용자 지정 예외를 사용 하 여 로깅이 향상 되었습니다.
  + **azureml-explain-model**
    + Feature_maps 매개 변수를 새 MimicWrapper에 추가 하 여 사용자가 원시 기능 설명을 가져올 수 있도록 합니다.
    + 이제 설명 업로드에 대 한 데이터 집합 업로드가 기본적으로 해제 되어 있으며, upload_datasets = True를 사용 하 여 다시 사용 하도록 설정할 수 있습니다.
    + 설명 목록에 "is_law" 필터링 매개 변수를 추가 하 고 함수를 다운로드 합니다.
    + `get_raw_explanation(feature_maps)`전역 및 로컬 설명 개체에 메서드를 추가 합니다.
    + 아래 지원 되는 버전의 경우 인쇄 된 경고로 lightgbm에 버전 검사가 추가 되었습니다.
    + 일괄 처리에 대 한 설명을 일괄 처리할 때 최적화 된 메모리 사용
    + AutoML 모델은 이제 AutoMLExceptions을 반환 합니다.
  + **azureml-pipeline-core**
    + PipelineDrafts 만들기, 업데이트 및 사용에 대 한 추가 지원은 변경 가능한 파이프라인 정의를 유지 관리 하 고 대화형으로 실행 하는 데 사용할 수 있습니다.
  + **azureml-학습-automl**
    + :::no-loc text="cuda":::원격 python 런타임 환경에서 BERT/XLNet을 사용 하도록 설정 하는 데 필요한 gpu 지원 pytorch v 1.1.0, toolkit 9.0, pytorch의 특정 버전을 설치 하는 기능을 만들었습니다.
  + **azureml-train-core**
    + 서버 쪽 대신 sdk에서 직접 일부 하이퍼 매개 변수 공간 정의 오류가 발생 합니다.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.14
+ **버그 수정 및 향상 된 기능**
  + 원시 경로 및 자격 증명을 사용 하 여 ADLS/ADLSGen2에 쓰기가 가능 합니다.
  + `include_path=True`에 대해 작동 하지 않는 버그를 수정 `read_parquet` 했습니다.
  + `to_pandas_dataframe()`"잘못 된 속성 값: hostSecret" 예외로 인해 오류가 수정 되었습니다.
  + Spark 모드의 DBFS에서 파일을 읽을 수 없는 버그를 수정 했습니다.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v 1.0.57
+ **새로운 기능**
  + AutomatedML에서 사용 하도록 설정 됩니다 `TabularDataset` . 에 대해 자세히 알아보려면를 `TabularDataset` 참조 하세요 https://aka.ms/azureml/howto/createdatasets .

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + 이제 Microsoft에서 생성 된 및 고객 인증서 모두 AKS 클러스터에 배포 된 점수 매기기 끝점에 대 한 TLS/SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + 레이블이 누락 된 행이 제대로 제거 되지 않은 AutoML에서 문제가 해결 되었습니다.
    + AutoML에서 오류 로깅이 개선 됨 이제 전체 오류 메시지는 항상 로그 파일에 기록 됩니다.
    + AutoML에서, 및를 포함 하도록 패키지 고정을 업데이트 했습니다 `azureml-defaults` `azureml-explain-model` `azureml-dataprep` . AutoML은 패키지 불일치에 대해 더 이상 경고 하지 않습니다 ( `azureml-train-automl` 패키지 제외).
    + `timeseries`Cv 분할 크기가 동일 하지 않아 bin 계산이 실패 하는 문제를 해결 했습니다.
    + 교차 유효성 검사 학습 유형에 대해 앙상블 반복을 실행할 때 전체 데이터 집합에 대해 학습 된 모델을 다운로드 하는 데 문제가 있으면 모델 가중치와 투표 앙상블에 제공 되는 모델 간에 불일치가 발생 했습니다.
    + 학습 및/또는 유효성 검사 레이블 (y 및 y_valid)이 numpy 배열이 아닌 pandas 데이터 프레임 형식으로 제공 될 때 발생 하는 오류를 수정 했습니다.
    + 입력 테이블의 부울 열에서 아무것도 발견 되지 않은 경우 예측 태스크에 대 한 문제를 해결 했습니다.
    + 자동 Ml 사용자가 예측 시 충분 하지 않은 학습 시리즈를 삭제할 수 있습니다. -자동 Ml 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직를 삭제할 수 있습니다.
  + **azureml-core**
    + Blob_cache_timeout 매개 변수 순서 지정 문제를 해결 했습니다.
    + 외부 맞춤 및 변환 예외 유형을 시스템 오류에 추가 했습니다.
    + 원격 실행에 대 한 Key Vault 암호에 대 한 지원이 추가 되었습니다. 작업 영역과 연결 된 keyvault의 암호를 추가, 가져오기 및 나열 하려면 azureml를 추가 합니다. 지원 되는 작업은 다음과 같습니다.
      + azureml. 작업 영역. get_default_keyvault ()
      + azureml. keyvault. set_secret (이름, 값)
      + azureml. 키 자격 증명 모음. keyvault (secrets_dict)
      + azureml. keyvault. 키 자격 증명 모음. get_secret (이름)
      + azureml. 키 자격 증명 모음. keyvault (secrets_list)
      + azureml. 키 자격 증명 모음. Keyvault. list_secrets ()
    + 원격 실행 중에 기본 키 자격 증명 모음을 가져오고 암호를 가져오는 추가 방법:
      + azureml. 작업 영역. get_default_keyvault ()
      + azureml. 실행. get_secret (이름)
      + azureml. get_secrets (secrets_list)
    + 전송-hyperdrive CLI 명령에 추가 재정의 매개 변수를 추가 했습니다.
    + API 호출의 안정성 향상은 일반적인 요청 라이브러리 예외에 대 한 다시 시도를 확장 합니다.
    + 제출 된 실행에서 실행을 제출 하기 위한 지원을 추가 합니다.
    + FileWatcher의 만료 된 SAS 토큰 문제를 수정 했습니다 .이로 인해 초기 토큰이 만료 된 후 파일의 업로드가 중지 됩니다.
    + 데이터 집합 python SDK에서 HTTP csv/tsv 파일 가져오기를 지원 합니다.
    + 작업 영역. setup () 메서드를 사용 하지 않습니다. 사용자에 게 create () 또는 get ()/from_config ()를 대신 사용 하는 것으로 표시 되는 경고 메시지입니다.
    + 환경 add_private_pip_wheel ()을 추가 했습니다 .이 기능을 사용 하면 개인 사용자 지정 python 패키지를 `whl` 작업 영역에 업로드 하 고이를 안전 하 게 사용 하 여 환경을 빌드하거나 구체화할 수 있습니다.
    + 이제 Microsoft에서 생성 된 및 고객 인증서 모두 AKS 클러스터에 배포 된 점수 매기기 끝점에 대 한 TLS/SSL 인증서를 업데이트할 수 있습니다.
  + **azureml-explain-model**
    + 업로드에 대 한 설명에 모델 ID를 추가 하는 매개 변수를 추가 했습니다.
    + `is_raw`메모리의 설명에 태그를 추가 하 고 업로드 합니다.
    + Pytorch 패키지에 대 한 지원 및 테스트가 추가 되었습니다.
  + **azureml-opendatasets**
    + 자동 테스트 환경 검색 및 로깅을 지원 합니다.
    + 국가 및 zip을 기준으로 인구를 가져오도록 클래스를 추가 했습니다.
  + **azureml-pipeline-core**
    + 입력 및 출력 포트 정의에 레이블 속성을 추가 했습니다.
  + **azureml-telemetry**
    + 잘못 된 원격 분석 구성을 수정 했습니다.
  + **azureml-학습-automl**
    + 설치 오류가 발생 하는 동안 오류가 발생 하 여 설치 실행에 대 한 "오류" 필드가 기록 되지 않아 부모 실행 "오류"로 저장 되지 않은 버그를 수정 했습니다.
    + 레이블이 누락 된 행이 제대로 제거 되지 않은 AutoML에서 문제가 해결 되었습니다.
    + 자동 Ml 사용자가 예측 시 충분 하지 않은 학습 시리즈를 삭제할 수 있습니다.
    + 자동 Ml 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직를 삭제할 수 있도록 허용 합니다.
    + 이제 AutoMLStep `automl` 가 새 구성 매개 변수의 변경 또는 추가에 대 한 문제를 방지 하기 위해 config를 백 엔드로 전달 합니다.
    + AutoML 데이터 Guardrail은 현재 공개 미리 보기로 제공 됩니다. 사용자는 학습 후에 데이터 Guardrail 보고서 (분류/회귀 작업)를 보고 SDK API를 통해 액세스할 수 있습니다.
  + **azureml-train-core**
    + PyTorch 평가기에서 torch 1.2 지원을 추가 했습니다.
  + **azureml-widgets**
    + 분류 학습을 위한 향상 된 혼동 행렬 차트

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.12
+ **새로운 기능**
  + 이제 문자열 목록을 메서드에 대 한 입력으로 전달할 수 있습니다 `read_*` .

+ **버그 수정 및 향상 된 기능**
  + `read_parquet`Spark에서 실행 하는 경우의 성능이 크게 향상 되었습니다.
  + `column_type_builder`모호한 날짜 형식의 단일 열에 대해 실패 한 경우 문제가 해결 되었습니다.

### <a name="azure-portal"></a>Azure portal
+ **미리 보기 기능**
  + 이제 실행 정보 페이지에 대 한 로그 및 출력 파일 스트리밍을 사용할 수 있습니다. 이 파일은 미리 보기 토글이 설정 된 경우 실시간으로 업데이트를 스트리밍합니다.
  + 작업 영역 수준에서 할당량을 설정 하는 기능은 미리 보기로 릴리스됩니다. AmlCompute 할당량은 구독 수준에서 할당 되지만 이제 작업 영역 간에 해당 할당량을 배포 하 고 공평 하 게 공유 및 거 버 넌 스에 할당할 수 있습니다. 작업 영역의 왼쪽 탐색 모음에서 **사용량 + 할당량** 블레이드를 클릭 하 고 **할당량 구성** 탭을 선택 하면 됩니다. 작업 영역 간 작업 이므로 작업 영역 수준에서 할당량을 설정할 수 있으려면 구독 관리자 여야 합니다.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python v 1.0.55

+ **새로운 기능**
  + 이제 AKS에 배포 된 점수 매기기 끝점에 대 한 호출에 대해 토큰 기반 인증이 지원 됩니다. 현재 키 기반 인증을 계속 지원 하 고 사용자가 다음 인증 메커니즘 중 하나를 한 번에 사용할 수 있습니다.
  + VNet (가상 네트워크) 뒤에 있는 blob 저장소를 데이터 저장소로 등록 하는 기능입니다.

+ **버그 수정 및 향상 된 기능**
  + **azureml-automl-core**
    + CV 분할에 대 한 유효성 검사 크기가 작고 회귀 및 예측에 대 한 잘못 된 예측 및 실제 차트를 생성 하는 버그를 수정 합니다.
    + 원격 실행에 대 한 예측 작업 로깅이 향상 되었습니다. 이제 실행이 실패 한 경우에는 포괄적인 오류 메시지와 함께 사용자가 제공 됩니다.
    + `Timeseries`전처리 플래그가 True 인 경우 오류를 수정 했습니다.
    + 일부 예측 데이터 유효성 검사 오류 메시지를 보다 효율적으로 수행할 수 있습니다.
    + 데이터 집합을 삭제 및/또는 지연 로드 하 여 자동 Ml 실행의 메모리 소비가 감소 합니다. 특히 프로세스 생성 사이에 있습니다.
  + **azureml-설명-모델**
    + 사용자가 모델 형식에 대 한 기본 자동 유추 논리를 재정의할 수 있도록 하 model_task 플래그를 explainers에 추가 했습니다.
    + 위젯 변경 내용:를 사용 하 여 자동으로 설치 `contrib` 하 고, 더 이상 `nbextension` 설치/활성화-지원 설명을 제공 하지 않습니다 (예: Permutative).
    + 대시보드 변경: 요약 페이지에 플롯 하는 것 외에도, Box 플롯 및 바이올린 플롯 `beeswarm` - `beeswarm` ' 하향식 ' 슬라이더 변경 내용-rerendering를 계산 하는 방법을 설명 하는 유용한 메시지입니다.
  + **azureml-core**
    + 모델 및 해당 종속성을 캡슐화 하는 Docker 이미지 및 Dockerfiles을 만들기 위한 package () 메서드를 추가 했습니다.
    + 환경 개체를 포함 하는 InferenceConfigs를 허용 하도록 로컬 webservices 업데이트 되었습니다.
    + 모델을 수정 했습니다. register () '. '로 잘못 된 모델을 생성 합니다. (현재 디렉터리의 경우)은 model_path 매개 변수로 전달 됩니다.
    + Submit_child를 추가 합니다 .이 기능은 실행을 전송 된 자식 실행의 부모로 지정 하는 동안 실험을 미러링합니다. 제출 합니다.
    + 모델에서 구성 옵션을 지원 합니다. register_model를 등록 하십시오.
    + 기존 클러스터에서 JAR 작업을 실행할 수 있습니다.
    + 이제 instance_pool_id 및 cluster_log_dbfs_path 매개 변수를 지원 합니다.
    + 웹 서비스에 모델을 배포할 때 환경 개체 사용에 대 한 지원이 추가 되었습니다. 이제 환경 개체를 InferenceConfig 개체의 일부로 제공할 수 있습니다.
    + 새 지역에 대 한 appinsifht 매핑 추가-centralus-westus-northcentralus
    + 모든 데이터 저장소 클래스의 모든 특성에 대 한 설명서를 추가 했습니다.
    + 에 blob_cache_timeout 매개 변수를 추가 했습니다 `Datastore.register_azure_blob_container` .
    + Save_to_directory 및 load_from_directory 메서드를 azureml에 추가 했습니다. 환경.
    + CLI에 "az ml environment download" 및 "az ml environment register" 명령이 추가 되었습니다.
    + Add_private_pip_wheel 메서드를 추가 했습니다.
  + **azureml-explain-model**
    + 데이터 집합 서비스 (미리 보기)를 사용 하 여 설명에 데이터 집합 추적을 추가 했습니다.
    + 10k에서 100로 글로벌 설명을 스트리밍할 때 기본 일괄 처리 크기를 줄였습니다.
    + Explainers에 model_task 플래그를 추가 하 여 사용자가 모델 형식에 대 한 기본 자동 유추 논리를 재정의할 수 있도록 합니다.
  + **azureml.mlflow**
    + Build_image에서 중첩 된 디렉터리가 무시 되는 버그를 수정 했습니다.
  + **azureml-pipeline-steps**
    + 기존 Azure Databricks 클러스터에서 JAR 작업을 실행 하는 기능이 추가 되었습니다.
    + DatabricksStep 단계에 대 한 지원 instance_pool_id 및 cluster_log_dbfs_path 매개 변수가 추가 되었습니다.
    + DatabricksStep 단계에서 파이프라인 매개 변수에 대 한 지원이 추가 되었습니다.
  + **azureml-학습-automl**
    + `docstrings`앙상블 관련 파일에 대해 추가 되었습니다.
    + 및에 대 한 보다 적절 한 언어로 문서 업데이트 `max_cores_per_iteration``max_concurrent_iterations`
    + 원격 실행에 대 한 예측 작업 로깅이 향상 되었습니다. 이제 실행이 실패 한 경우에는 포괄적인 오류 메시지와 함께 사용자가 제공 됩니다.
    + 파이프라인 노트북에서 get_data 제거 `automlstep` 되었습니다.
    + 에서의 지원이 시작 되었습니다 `dataprep` `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.10

+ **새로운 기능**
  + 이제 특정 열에 대해 특정 검사기 (예: 히스토그램, 산 점도 등)를 실행 하도록 요청할 수 있습니다.
  + 에 병렬 처리 인수를 추가 했습니다 `append_columns` . True 이면 데이터가 메모리에 로드 되지만 실행은 병렬로 실행 됩니다. False 이면 실행은 스트리밍 이지만 단일 스레드는 됩니다.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python v 1.0.53

+ **새로운 기능**
  + 이제 자동화 된 Machine Learning에서 원격 계산 대상의 교육 ONNX 모델을 지원 합니다.
  + Azure Machine Learning은 이제 이전 실행, 검사점 또는 모델 파일에서 학습을 다시 시작할 수 있는 기능을 제공 합니다.
    + [추정를 사용 하 여 이전 실행에서 학습을 다시 시작](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/training/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) 하는 방법을 알아봅니다.

+ **버그 수정 및 향상 된 기능**
  + **azure-cli-ml**
    + CLI 명령 "모델 배포" 및 "서비스 업데이트"는 이제 매개 변수, 구성 파일 또는 둘의 조합을 허용 합니다. 매개 변수는 파일의 특성 보다 우선 합니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
    + AutoML 추정 내에서 사용할 NimbusML 추정 & 파이프라인에 대 한 지원을 추가 합니다.
    + 점수가 일정 하 게 유지 된 경우에도 결과 앙상블을 불필요 하 게 증가 시킨 앙상블 선택 프로시저의 버그를 수정 합니다.
    + 예측 작업에 대해 CV 분할에서 일부 featurizations를 다시 사용할 수 있습니다. 이는 지연 및 롤링 창과 같은 고가의 featurizations에 대 한 n_cross_validations의 대략적인 실행 시간을 단축 합니다.
    + 시간이 pandas 지원 되는 시간 범위를 벗어난 경우 문제를 해결 합니다. 이제 시간이 pd 보다 작은 경우 DataException이 발생 합니다. Timestamp. min 또는 pd 보다 큽니다. Timestamp. 최대
    + 이제 예측을 통해 학습 및 테스트 집합에서 서로 다른 빈도로 정렬할 수 있습니다. 예를 들어 "분기별 1 월부터 시작 하 고" 분기별 10 월에 시작 "은 정렬할 수 있습니다.
    + "Parameters" 속성이 TimeSeriesTransformer에 추가 되었습니다.
    + 이전 예외 클래스를 제거 합니다.
    + 예측 작업에서 `target_lags` 매개 변수는 이제 단일 정수 값 이나 정수 목록을 허용 합니다. 정수를 제공한 경우에는 한 개의 지연만 생성 됩니다. 목록이 제공 되는 경우에는 고유한 지연 값이 사용 됩니다. target_lags = [1, 2, 2, 4]은 1, 2, 4 기간의 지연 시간을 만듭니다.
    + 변환 후 열 유형 손실에 대 한 버그 수정 (버그 링크)
    + 에서 `model.forecast(X, y_query)` y_query는 begin (#459519)에서 None (s)을 포함 하는 개체 유형이 될 수 있습니다.
    + 예상 값을 출력에 추가 합니다. `automl`
  + **azureml-datadrift**
    +  데이터를 보강 때 azureml (azureml)를 포함 하는 예제 노트북의 향상 된 기능-opendatasets 및 성능 향상
  + **azureml-설명-모델**
    + Azureml의 원시 기능 중요도에 대 한 라임 설명의 수정 된 변환 인수-설명-모델 패키지
    + AzureML segmentations 패키지에 대 한 이미지 설명의 이미지 설명에 추가 되었습니다.
    + LimeExplainer에 대 한 scipy sparse 지원 추가
    + `batch_size` `include_local=False` DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리로 전역 설명을 스트리밍하는 경우 설명 하는 경우에 추가 되었습니다.
  + **azureml-featureengineering**
    + Set_featurizer_timeseries_params () 호출에 대 한 수정: dict 값 형식 변경 및 null 확인-featurizer에 대 한 전자 필기장 추가 `timeseries`
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
  + **azureml-core**
    + AzureML CLI에서 dbfs 데이터 저장소를 연결 하는 기능이 추가 됨
    + 로 시작 하는 경우 빈 폴더가 만들어지는 데이터 저장소 업로드를 사용 하 여 버그를 수정 `target_path` 했습니다. `/`
    + `deepcopy`ServicePrincipalAuthentication의 문제를 해결 했습니다.
    + CLI에 "az ml environment show" 및 "az ml environment list" 명령이 추가 되었습니다.
    + 이제 환경에서 이미 빌드된 base_image 대신 base_dockerfile 지정을 지원 합니다.
    + 사용 되지 않는 RunConfiguration 설정 auto_prepare_environment는 사용 되지 않는 것으로 표시 되었습니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있습니다.
    + 버그 수정: 이제 모델 및 이미지 삭제는 업스트림 종속성으로 인해 삭제가 실패할 경우 해당 개체에 종속 된 업스트림 개체를 검색 하는 방법에 대 한 자세한 정보를 제공 합니다.
    + 일부 환경에 대 한 작업 영역을 만들 때 발생 하는 배포에 대해 빈 기간을 인쇄 한 버그가 수정 되었습니다.
    + 작업 영역 만들기 오류 예외를 개선 했습니다. 사용자가 "작업 영역을 만들 수 없습니다. 찾을 수 없음 ... " 메시지를 표시 하 고 대신 실제 생성 오류를 표시 합니다.
    + AKS webservices에서 토큰 인증에 대 한 지원을 추가 합니다.
    + `get_token()`개체에 메서드를 추가 `Webservice` 합니다.
    + Machine learning 데이터 집합을 관리 하기 위해 CLI 지원이 추가 되었습니다.
    + `Datastore.register_azure_blob_container` 이제는 `blob_cache_timeout` 이 데이터 저장소에 대 한 캐시 만료를 사용 하도록 blobfuse의 탑재 매개 변수를 구성 하는 값 (초)을 선택적으로 사용 합니다. 기본값은 시간 제한이 없습니다. 즉, blob을 읽을 때 작업이 완료 될 때까지 로컬 캐시에 유지 됩니다. 대부분의 작업은이 설정을 선호 하지만, 일부 작업은 해당 노드에 적합 한 것 보다 큰 데이터 집합에서 더 많은 데이터를 읽어야 합니다. 이러한 작업의 경우이 매개 변수를 조정 하면 성공할 수 있습니다. 이 매개 변수를 튜닝할 때 주의 해야 합니다. 값을 너무 낮게 설정 하면 epoch에서 사용 된 데이터가 다시 사용 되기 전에 만료 될 수 있으므로 성능이 저하 될 수 있습니다. 즉, 모든 읽기는 로컬 캐시 대신 blob 저장소 (즉, 네트워크)에서 수행 되므로 학습 시간에 부정적인 영향을 줍니다.
    + 이제 등록 후 모델 설명을 올바르게 업데이트할 수 있습니다.
    + 이제 모델 및 이미지 삭제는 해당 개체에 종속 된 업스트림 개체에 대 한 자세한 정보를 제공 하므로 삭제가 실패 합니다.
    + Azureml mlflow를 사용 하 여 원격 실행의 리소스 사용률을 향상 시킵니다.
  + **azureml-explain-model**
    + Azureml의 원시 기능 중요도에 대 한 라임 설명의 수정 된 변환 인수-설명-모델 패키지
    + LimeExplainer에 대 한 scipy sparse 지원 추가
    + 선형 모델을 설명 하는 shape 선형 설명 래퍼 뿐만 아니라 테이블 형식 설명에 다른 수준 추가
    + 모델 라이브러리 설명의 모방 설명는 스파스 데이터 입력에 대 한 include_local = False 인 경우 오류 해결
    + 예상 값을 출력에 추가 합니다. `automl`
    + 원시 기능 중요도를 얻기 위해 변환 인수를 제공 했을 때 순열 기능 중요도 수정 됨
    + `batch_size` `include_local=False` DecisionTreeExplainableModel의 실행 시간을 향상 시키기 위해 일괄 처리로 전역 설명을 스트리밍하는 경우 설명 하는 경우에 추가 되었습니다.
    + model explainability library의 경우 예측에 pandas 데이터 프레임 input이 필요한 explainers 고정 된 블랙 박스
    + `explanation.expected_values`때때로 float가 있는 목록이 아닌 float를 반환 하는 버그를 수정 했습니다.
  + **azureml.mlflow**
    + Mlflow의 성능 향상 set_experiment (experiment_name)
    + Mlflow tracking_uri에 대해 InteractiveLoginAuthentication를 사용 하 여 버그 수정
    + Azureml mlflow를 사용 하 여 원격 실행의 리소스 사용률을 향상 시킵니다.
    + Azureml-mlflow 패키지의 설명서 개선
    + Mlflow. log_artifacts ("my_dir")가 "> 아티팩트-경로 <" 대신 "my_dir/<아티팩트-경로>" 아래에 아티팩트를 저장 하는 패치 버그
  + **azureml-opendatasets**
    + `pyarrow` `opendatasets` 새로 도입 된 메모리 문제로 인해 이전 버전 (<0.14.0)에 고정 합니다.
    + Azureml를 azureml-opendatasets 집합으로 이동 합니다.
    + 열려 있는 데이터 집합 클래스를 Azure Machine Learning 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있도록 허용 합니다.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.
  + **azureml-pipeline-steps**
    + DBFS 데이터 저장소는 이제 DatabricksStep의 입력 및 출력에 대해 지원 됩니다.
    + 입력/출력과 관련 하 여 Azure Batch 단계에 대 한 업데이트 된 설명서입니다.
    + AzureBatchStep에서 *delete_batch_job_after_finish* 기본값을 *true*로 변경 합니다.
  + **azureml-telemetry**
    +  Azureml를 azureml-opendatasets 집합으로 이동 합니다.
    + 열려 있는 데이터 집합 클래스를 Azure Machine Learning 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있도록 허용 합니다.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.
  + **azureml-학습-automl**
    + 실제 반환 형식을 반영 하 고 키 속성을 검색 하는 방법에 대 한 추가 정보를 제공 하는 get_output에 대 한 문서가 업데이트 되었습니다
    + NimbusML 종속성을 1.2.0 버전 (현재 최신 버전)으로 업데이트 합니다.
    + 예상 값을 출력에 추가 합니다. `automl`
  + **azureml-train-core**
    + 이제 문자열은 자동화 된 하이퍼 매개 변수 튜닝을 위한 계산 대상으로 수락 됩니다.
    + 사용 되지 않는 RunConfiguration 설정 auto_prepare_environment는 사용 되지 않는 것으로 표시 되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning 데이터 준비 SDK v 1.1.9

+ **새로운 기능**
  + Http 또는 https url에서 직접 파일을 읽기 위한 지원이 추가 되었습니다.

+ **버그 수정 및 향상 된 기능**
  + 원격 원본에서 Parquet 데이터 집합을 읽으려고 할 때 (현재 지원 되지 않음) 오류 메시지가 개선 되었습니다.
  + ADLS Gen 2에서 Parquet 파일 형식에 쓸 때 버그를 수정 하 고 경로에서 ADLS Gen 2 컨테이너 이름을 업데이트 합니다.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>시각적 인터페이스
+ **미리 보기 기능**
  + 시각적 인터페이스에서 "R 스크립트 실행" 모듈을 추가 했습니다.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v 1.0.48

+ **새로운 기능**
  + **azureml-opendatasets**
    + **azureml-및 리브-opendatasets** 은 이제 **azureml-opendatasets**으로 사용할 수 있습니다. 이전 패키지는 계속 작동할 수 있지만 더 풍부한 기능과 향상 된 기능을 위해 **azureml-opendatasets** 을 사용 하는 것이 좋습니다.
    + 이 새 패키지를 사용 하면 열려 있는 데이터 집합을 Azure Machine Learning 작업 영역에서 데이터 집합으로 등록 하 고 데이터 집합에서 제공 하는 모든 기능을 활용할 수 있습니다.
    + 또한 Pandas/SPARK 데이터 프레임로 개방형 데이터 집합을 사용 하는 것과 같은 기존 기능과 날씨와 같은 일부 데이터 집합에 대 한 위치 조인을 포함 합니다.

+ **미리 보기 기능**
    + 이제 HyperDriveConfig는 파이프라인을 사용 하 여 하이퍼 매개 변수 튜닝을 지원 하기 위해 파이프라인 개체를 매개 변수로 허용할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + **azureml-학습-automl**
    + 변환 후 열 유형 손실에 대 한 버그를 수정 했습니다.
    + Y_query 시작 부분에 없음 (s)을 포함 하는 개체 형식일 수 있도록 버그를 수정 했습니다.
    + 앙상블 선택 프로시저에서 점수가 일정 하 게 유지 된 경우에도 결과 앙상블를 불필요 하 게 증가 시킨 문제를 해결 했습니다.
    + AutoMLStep에서 list_models 허용 및 list_models 설정 차단 문제를 해결 했습니다.
    + Azure ML 파이프라인의 컨텍스트에서 AutoML을 사용 하는 경우 전처리 사용을 방해 하는 문제를 해결 했습니다.
  + **azureml-opendatasets**
    + Azureml 이동-opendatasets 집합을 azureml-opendatasets으로 이동 했습니다.
    + Azure Machine Learning 작업 영역에 등록 하 고 AML 데이터 집합 기능을 원활 하 게 활용할 수 있도록 개방형 데이터 집합 클래스를 허용 했습니다.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능이 크게 향상 되었습니다.
  + **azureml-explain-model**
    + Interpretability 개체에 대 한 온라인 설명서가 업데이트 되었습니다.
    + `batch_size` `include_local=False` 모델 explainability 라이브러리의 실행 시간을 향상 시키기 위해 일괄 처리로 전역 설명을 스트리밍하는 경우 설명 경우에 추가 됩니다.
    + `explanation.expected_values`때때로 float가 있는 목록이 아닌 float을 반환 하는 문제를 해결 했습니다.
    + `automl`설명 모델 라이브러리의 모방 설명에 대 한 예상 값을 출력에 추가 했습니다.
    + 원시 기능 중요도를 얻기 위해 변환 인수를 제공 하면 순열 기능 중요도가 수정 되었습니다.
  + **azureml-core**
    + AzureML CLI에서 dbfs 데이터 저장소를 연결 하는 기능이 추가 되었습니다.
    + 로 시작 하는 경우 빈 폴더가 만들어지는 데이터 저장소 업로드 문제를 해결 `target_path` `/` 했습니다.
    + 두 데이터 집합의 비교를 사용 합니다.
    + 이제 모델 및 이미지 삭제는 업스트림 종속성으로 인해 삭제가 실패할 경우 해당 개체에 종속 된 업스트림 개체를 검색 하는 방법에 대 한 자세한 정보를 제공 합니다.
    + Auto_prepare_environment에서 사용 되지 않는 RunConfiguration 설정을 사용 하지 않습니다.
  + **azureml.mlflow**
    + Azureml mlflow를 사용 하는 원격 실행의 리소스 사용률이 향상 되었습니다.
    + Azureml-mlflow 패키지의 설명서가 개선 되었습니다.
    + Mlflow. log_artifacts ("my_dir")가 "아티팩트 경로" 대신 "my_dir/경로"로 아티팩트를 저장 하는 문제를 해결 했습니다.
  + **azureml-pipeline-core**
    + 모든 파이프라인 단계에 대 한 매개 변수 hash_paths은 더 이상 사용 되지 않으며 나중에 제거 될 예정입니다. 기본적으로 source_directory의 내용이 해시 됩니다 (amlignore 또는 .gitignore에 나열 된 파일 제외).
    + 파이프라인에서 계산 유형별 모듈 사용을 잠금 해제 하기 위해 RunConfiguration 통합 및 기타 변경 내용을 준비 하기 위해 모듈 및 ModuleStep를 지속적으로 개선 하 여 계산 형식 관련 모듈을 지원 합니다.
  + **azureml-pipeline-steps**
    + AzureBatchStep: 입력/출력과 관련 된 향상 된 설명서입니다.
    + AzureBatchStep: 기본값 delete_batch_job_after_finish true로 변경 되었습니다.
  + **azureml-train-core**
    + 이제 문자열은 자동화 된 하이퍼 매개 변수 튜닝을 위한 계산 대상으로 허용 됩니다.
    + Auto_prepare_environment에서 사용 되지 않는 RunConfiguration 설정을 사용 하지 않습니다.
    + 사용 되지 않는 매개 변수 `conda_dependencies_file_path` 및 `pip_requirements_file_path` 각각 및의 우선 순위 `conda_dependencies_file` `pip_requirements_file` 입니다.
  + **azureml-opendatasets**
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능을 크게 개선 합니다.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python v 1.0.33가 릴리스 되었습니다.

+ [Fpgas](how-to-deploy-fpga-web-service.md) Azure ML 하드웨어 가속 모델은 일반적으로 사용할 수 있습니다.
  + 이제 [azureml 가속 패키지를 사용](how-to-deploy-fpga-web-service.md) 하 여 다음을 수행할 수 있습니다.
    + 지원 되는 심층 신경망 (ResNet 50, ResNet 152, DenseNet-121, VGG-16 및 SSD-VGG)의 가중치를 학습 합니다.
    + 지원 되는 DNN로 전송 학습 사용
    + 모델 관리 서비스를 사용 하 여 모델 등록 및 모델 컨테이너 화
    + AKS (Azure Kubernetes Service) 클러스터에서 FPGA을 사용 하 여 Azure VM에 모델 배포
  + 컨테이너를 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) 서버 장치에 배포
  + 이 [샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models) 에서 grpc 끝점을 사용 하 여 데이터 점수 매기기

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

+ 성능 최적화를 위해 동적으로 추가할 수 있는 기능 스윕 :::no-loc text="featurizers"::: . 새 :::no-loc text="featurizers"::: 내용: work 포함, 증거의 무게, 대상 인코딩, 텍스트 대상 인코딩, 클러스터 거리
+ 자동화 된 ML 내에서 학습/유효한 분할을 처리 하는 스마트 CV
+ 몇 가지 메모리 최적화 변경 및 런타임 성능 향상
+ 모델 설명의 성능 향상
+ 로컬 실행에 대 한 ONNX 모델 변환
+ 추가 된 서브 샘플링 지원
+ 종료 조건이 정의 되지 않은 경우 지능형 중지
+ 누적 앙상블

+ 시계열 예측
  + 새 예측 예측 함수
  + 이제 시계열 데이터에 대 한 롤링 원본 교차 유효성 검사를 사용할 수 있습니다.
  + 시계열 지연 구성에 추가 된 새로운 기능
  + 롤링 창 집계 기능을 지원 하기 위해 추가 된 새 기능
  + 실험 설정에서 국가 코드가 정의 된 경우 새 휴일 검색 및 featurizer

+ Azure Databricks
  + 시계열 예측 및 모델 explainabilty/interpretability 기능 사용
  + 이제 자동화 된 ML 실험을 취소 하 고 다시 시작할 수 있습니다 (계속).
  + 다중 코어 처리에 대 한 지원 추가

### <a name="mlops"></a>MLOps
+ **점수 매기기 컨테이너에 대 한 로컬 배포 & 디버깅**<br/> 이제 ML 모델을 로컬로 배포 하 고 점수 매기기 파일 및 종속성을 빠르게 반복 하 여 예상 대로 동작 하는지 확인할 수 있습니다.

+ **InferenceConfig () &에 도입 되었습니다.**<br/> 모델 배포에서는 이제 .Runconfig와 동일한 항목 스크립트가 포함 된 원본 폴더를 지정할 수 있습니다.  또한 모델 배포는 단일 명령으로 간소화 되었습니다.

+ **Git 참조 추적**<br/> 고객은 종단 간 감사 내역을 유지 하는 데 도움이 되므로 일정 시간 동안 기본 Git 통합 기능을 요청 했습니다. Git 관련 메타 데이터 (리포지토리, 커밋, 정리 상태)에 대해 Azure ML의 주요 엔터티 간 추적을 구현 했습니다. 이 정보는 SDK 및 CLI에서 자동으로 수집 됩니다.

+ **모델 프로 파일링 & 유효성 검사 서비스**<br/> 고객은 유추 서비스와 관련 된 계산의 크기를 적절 하 게 조정 하는 데 자주 어려움이 있습니다. 모델 프로 파일링 서비스를 사용 하는 경우 고객은 샘플 입력을 제공할 수 있으며, 배포에 대 한 최적의 크기 조정을 결정 하기 위해 16 개의 다른 CPU/메모리 구성에서 프로 파일링 합니다.

+ **유추를 위한 고유한 기본 이미지 가져오기**<br/> 또 다른 일반적인 불만은 실험에서 유추 다시 공유 종속성으로 이동 하는 데 어려움이 있습니다. 이제 새로운 기본 이미지 공유 기능을 사용 하 여 유추를 위해 실험 기본 이미지, 종속성 및 모두를 재사용할 수 있습니다. 이렇게 하면 배포 속도가 향상 되 고 내부에서 외부 루프로의 간격이 줄어듭니다.

+ **향상 된 Swagger 스키마 생성 환경**<br/> 이전 swagger 생성 방법은 오류가 발생 하기 쉬우며 자동화할 수 없습니다. 데코레이터를 통해 Python 함수에서 swagger 스키마를 생성 하는 새로운 인라인 방법이 있습니다. 이 코드는 오픈 소스 이며, 스키마 생성 프로토콜은 Azure ML 플랫폼과 결합 되지 않습니다.

+ **GA (일반 공급) Azure ML CLI**<br/> 이제 단일 CLI 명령을 사용 하 여 모델을 배포할 수 있습니다. Jupyter 노트북에서 ML 모델을 배포 하지 않는 일반적인 고객 의견이 있습니다. [**CLI 참조 설명서**](https://aka.ms/azmlcli) 가 업데이트 되었습니다.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python v 1.0.30가 릴리스 되었습니다.

는 [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) 동일한 끝점을 유지 하면서 게시 된 파이프라인의 새 버전을 추가 하기 위해 도입 되었습니다.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure portal
  + 이제 기존 원격 계산 클러스터에서 기존 스크립트 실행을 다시 제출할 수 있습니다.
  + 이제 파이프라인 탭에서 새 매개 변수를 사용 하 여 게시 된 파이프라인을 실행할 수 있습니다.
  + 이제 실행 세부 정보에서 새로운 스냅숏 파일 뷰어를 지원 합니다. 특정 실행을 제출할 때 디렉터리의 스냅숏을 볼 수 있습니다. 전송 된 노트북을 다운로드 하 여 실행을 시작할 수도 있습니다.
  + 이제 Azure Portal에서 부모 실행을 취소할 수 있습니다.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python v 1.0.23

+ **새로운 기능**
  + Azure Machine Learning SDK는 이제 Python 3.7를 지원 합니다.
  + Azure Machine Learning DNN 추정는 이제 기본 제공 다중 버전 지원 기능을 제공 합니다. 예를 들어 `TensorFlow`   이제 평가기는 `framework_version` 매개 변수를 허용 하 고 사용자는 ' 1.10 ' 또는 ' 1.12 ' 버전을 지정할 수 있습니다. 현재 SDK 릴리스에서 지원 되는 버전 목록을 보려면 `get_supported_versions()` 원하는 프레임 워크 클래스 (예:)에서를 호출 `TensorFlow.get_supported_versions()` 합니다.
  최신 SDK 릴리스에서 지원 되는 버전 목록은 [DNN 평가기 설명서](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)를 참조 하세요.

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python v 1.0.21

+ **새로운 기능**
  + *Azureml. create_children* 메서드를 사용 하면 단일 호출로 여러 자식 실행을 짧은 대기 시간으로 만들 수 있습니다.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python v 1.0.18

 + **변경**
   + Azureml tensorboard 패키지는 azureml-tensorboard를 대체 합니다.
   + 이 릴리스에서는 생성 하는 동안 관리 되는 계산 클러스터 (amlcompute)에서 사용자 계정을 설정할 수 있습니다. 프로 비전 구성에서 이러한 속성을 전달 하 여이 작업을 수행할 수 있습니다. 자세한 내용은 [SDK 참조 설명서](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)에서 확인할 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning 데이터 준비 SDK v 1.0.17

+ **새로운 기능**
  + 에서는 이제 식 언어를 사용 하 여 결과 열을 생성 하는 두 개의 숫자 열을 추가할 수 있습니다.

+ **버그 수정 및 향상 된 기능**
  + Random_split에 대 한 설명서 및 매개 변수 확인이 향상 되었습니다.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning 데이터 준비 SDK v 1.0.16

+ **버그 수정**
  + API 변경으로 인해 발생 한 서비스 주체 인증 문제를 수정 했습니다.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python v 1.0.17

+ **새로운 기능**

  + 이제 Azure Machine Learning는 인기 있는 DNN framework 체 이너를 위한 첫 번째 클래스 지원을 제공 합니다. [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)클래스 사용자를 사용 하면 체 이너 모델을 쉽게 학습 하 고 배포할 수 있습니다.
    + [ChainerMN를 사용 하 여 분산 교육을 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/training/distributed-chainer/distributed-chainer.ipynb) 하는 방법 알아보기
    + [하이퍼 매개 변수를 사용 하 여 체 매개 변수 튜닝을 사용](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) 하 여 체를 실행 하는 방법 알아보기
  + Azure Machine Learning 파이프라인은 데이터 저장소 수정에 따라 파이프라인 실행을 트리거하는 기능을 추가 했습니다. 이 기능을 보여 주기 위해 파이프라인 [일정 노트북이](https://aka.ms/pl-schedule) 업데이트 됩니다.

+ **버그 수정 및 향상 된 기능**
  + [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)에 제공 되는 [runconfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 에서 source_directory_data_store 속성을 원하는 데이터 저장소 (예: blob storage)로 설정 하기 위한 Azure Machine Learning 파이프라인에 대 한 지원이 추가 되었습니다. 기본적으로 Azure 파일 저장소는 백업 데이터 저장소로 사용 됩니다 .이는 많은 수의 단계가 동시에 실행 될 때 제한 문제가 발생할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

+ **새로운 기능**
  + 새 보고서 끌어서 놓기 테이블 편집기 환경 사용자는 테이블의 미리 보기가 표시 되는 테이블 영역에서 열을 끌 수 있습니다. 열을 다시 정렬할 수 있습니다.
  + 새 로그 파일 뷰어
  + 작업 탭에서 실험 실행, 계산, 모델, 이미지 및 배포에 대 한 링크

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](overview-what-is-azure-ml.md)에 대한 개요를 참조하세요.
