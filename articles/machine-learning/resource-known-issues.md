---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 오류 또는 오류를 찾고 수정 하는 데 도움을 받으세요. 알려진 문제, 문제 해결 및 해결 방법에 대해 알아봅니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4
ms.date: 08/13/2020
ms.openlocfilehash: 4dced0e0597e4df2fe215c9f4b85e3e8defd92c3
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230384"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Azure Machine Learning의 알려진 문제 및 문제 해결

이 문서는 Azure Machine Learning을 사용할 때 발생할 수 있는 알려진 문제를 해결 하는 데 도움이 됩니다. 

문제 해결에 대 한 자세한 내용은이 문서의 끝에 있는 [다음 단계](#next-steps) 를 참조 하세요.

> [!TIP]
> 오류 또는 기타 문제는 Azure Machine Learning 작업할 때 발생 하는 [리소스 할당량](how-to-manage-quotas.md) 의 결과일 수 있습니다. 

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 일부 로그를 보려면 다음을 수행 합니다. 
1. [Azure Machine Learning studio](https://ml.azure.com)를 방문 합니다. 
1. 왼쪽에서 **실험** 을 선택 합니다. 
1. 실험을 선택 합니다.
1. 실행을 선택 합니다.
1. 위쪽에서 **출력 + 로그**를 선택 합니다.

> [!NOTE]
> 자동 Ml 또는 교육 작업을 실행 하는 Docker 컨테이너와 같은 학습 중 다양 한 원본에서 정보를 기록 Azure Machine Learning 합니다. 이러한 로그는 대부분 문서화 되어 있지 않습니다. 문제가 발생하여 Microsoft 지원에 문의하는 경우 이러한 로그를 사용하여 문제를 해결할 수 있습니다.


## <a name="installation-and-import"></a>설치 및 가져오기
                           
* **Pip 설치: 종속성이 한 줄 설치와 일치 하지 않을 수 있습니다.** 

   이는 단일 선으로를 설치할 때 작동 하는 종속성 해결 프로그램이 없기 때문에 pip의 알려진 제한 사항입니다. 첫 번째 고유 종속성은 보이는 유일한 종속성입니다. 

   다음 코드에서 `azureml-datadrift` 및 `azureml-train-automl` 는 모두 한 줄 pip 설치를 사용 하 여 설치 됩니다. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   이 예에서는 `azureml-datadrift` > 1.0 버전이 필요 하며, `azureml-train-automl` < 1.2 버전이 필요 합니다. 최신 버전의 `azureml-datadrift` 가 1.3 인 경우 `azureml-train-automl` 이전 버전의 패키지 요구 사항에 관계 없이 두 패키지가 모두 1.3로 업그레이드 됩니다. 

   패키지에 대 한 적절 한 버전이 설치 되었는지 확인 하려면 다음 코드와 같이 여러 줄을 사용 하 여를 설치 합니다. Pip는 다음 줄 호출의 일부로 명시적으로 다운 그레이드 때문에 순서가 문제가 되지 않습니다. 그러면 적절 한 버전 종속성이 적용 됩니다.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Azureml-자동 ml-클라이언트를 설치 하는 경우 설명 패키지가 설치 되어 있지 않을 수 있습니다.** 
   
   모델 설명이 설정 된 원격 AutoML run을 실행 하는 경우 "모델 설명을 위한 azureml (azureml) 모델 패키지를 설치 하세요." 라는 오류 메시지가 표시 됩니다. 이것은 알려진 문제입니다. 해결 방법으로 다음 단계 중 하나를 수행 합니다.
  
  1. Azureml를 로컬로 설치 합니다.
   ```
      pip install azureml-explain-model
   ```
  2. AutoML 구성에서 model_explainability = False를 전달 하 여 explainability 기능을 완전히 사용 하지 않도록 설정 합니다.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda 오류: 일반적으로 AutoML 실험을 수행 하는 동안 표시 됩니다.**
   
   Pip를 사용 하 여 환경을 수동으로 설정 하는 경우 지원 되지 않는 패키지 버전이 설치 되어 있기 때문에 특성 오류 (특히 pandas)를 알 수 있습니다. 이러한 오류를 방지 하려면 automl_setup을 [사용 하 여 AutoML SDK를 설치 하세요](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md).
   
    1. Anaconda 프롬프트를 열고 샘플 노트북 집합에 대 한 GitHub 리포지토리를 복제 합니다.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. 샘플 노트북이 추출 된 후 실행 되는 사용 방법-azureml/자동-기계 학습 폴더로 이동 합니다.
    
    ```bash
    automl_setup
    ```
    
* **KeyError: 로컬 계산 또는 Azure Databricks 클러스터에서 AutoML을 실행 하는 경우 ' 브랜드 '**

    2020 년 6 월 10 일 이후에 새 환경을 만든 경우 SDK 1.7.0 또는 이전 버전을 사용 하면 py-cpuinfo 패키지의 업데이트로 인해이 오류가 발생 하 여 교육이 실패할 수 있습니다. 2020 년 6 월 10 일 이전에 만들어진 환경은 캐시 된 학습 이미지를 사용 하기 때문에 원격 계산에서 실험을 실행 하는 것 처럼 영향을 받지 않습니다. 이 문제를 해결 하려면 다음 두 단계 중 하나를 수행 합니다.
    
    * SDK 버전을 1.8.0 이상으로 업데이트 합니다 (다운 그레이드 py-cpuinfo to 5.0.0).
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Py-cpuinfo의 설치 된 버전을 5.0.0로 다운 그레이드 합니다.
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **오류 메시지: ‘PyYAML’을 제거할 수 없습니다.**

    Python 용 Azure Machine Learning SDK: PyYAML는 `distutils` 설치 된 프로젝트입니다. 따라서 부분 제거가 있는 경우 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Azure Machine Learning SDK를 설치 하지 못했습니다. 예외: ModuleNotFoundError: ' ImportError ' 또는 ': a 모듈이 없습니다. yamel. yaml ' 라는 모듈이 없습니다.**
   
   이 문제는 Python 용 Azure Machine Learning SDK의 모든 릴리스 버전에 대 한 conda 기본 환경의 최신 pip (>20.1.1)에 Python 용 Azure Machine Learning SDK를 설치 하는 경우에 발생 합니다. 다음 해결 방법을 참조 하세요.

    * Conda 기본 환경에 Python SDK를 설치 하지 말고 conda 환경을 만들고 새로 만든 사용자 환경에 SDK를 설치 합니다. 최신 pip는 새 conda 환경에서 작동 해야 합니다.

    * Docker에서 이미지를 만드는 경우 conda 기본 환경에서 다른 곳으로 전환할 수 없습니다. docker 파일에서 pip<= 20.1.1을 고정 하세요.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **패키지를 설치할 때 Databricks 오류 발생**

    추가 패키지가 설치 되 면 Azure Databricks에서 Azure Machine Learning SDK 설치가 실패 합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 설치 오류를 방지 하려면 라이브러리 버전을 고정 하 여 패키지를 설치 합니다. 이 문제는 Azure Machine Learning SDK가 아닌 Databricks와 관련이 있습니다. 다른 라이브러리 에서도이 문제가 발생할 수 있습니다. 예:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    또는 Python 라이브러리와의 연결을 유지 하는 경우 init 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원 되지 않습니다. 자세한 내용은 [클러스터 범위 init 스크립트](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)를 참조 하세요.

* **Databricks 가져오기 오류: `Timedelta` 에서 `pandas._libs.tslibs` 이름을 가져올 수 없습니다 **. 자동화 된 machine learning을 사용 하는 경우이 오류가 표시 되 면 노트북에서 다음 두 줄을 실행 합니다.
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks 가져오기 오류: ' pandas ' 모듈이 없습니다**. 자동화 된 machine learning을 사용 하는 경우이 오류가 표시 됩니다.

    1. Azure Databricks 클러스터에 두 개의 패키지를 설치 하려면이 명령을 실행 합니다.
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. 클러스터를 분리 했다가 노트북에 다시 연결 합니다.
    
    이러한 단계를 수행 해도 문제가 해결 되지 않으면 클러스터를 다시 시작 하십시오.

* **Databricks FailToSendFeather**: `FailToSendFeather` Azure Databricks 클러스터에서 데이터를 읽는 동안 오류가 표시 되는 경우 다음 해결 방법을 참조 하세요.
    
    * `azureml-sdk[automl]`패키지를 최신 버전으로 업그레이드 합니다.
    * `azureml-dataprep`버전 1.1.8 이상을 추가 합니다.
    * `pyarrow`버전 0.11 이상을 추가 합니다.
    
## <a name="create-and-manage-workspaces"></a>작업 영역 만들기 및 관리

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동하거나 소유하는 구독을 새 테넌트로 이동하는 것은 지원되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.

* **Azure Portal**: SDK 또는 포털의 공유 링크에서 작업 영역을 직접 확인 하는 경우 확장에서 구독 정보가 포함 된 일반 **개요** 페이지를 볼 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인 해야 하는 경우 [Azure Machine Learning studio](https://ml.azure.com) 로 직접 이동 하 여 작업 영역 이름을 검색 합니다.

* **Azure Machine Learning studio 웹 포털에서 지원 되는 브라우저**: 운영 체제와 호환 되는 최신 브라우저를 사용 하는 것이 좋습니다. 다음과 같은 브라우저가 지원됩니다.
  * Microsoft Edge (새로운 Microsoft Edge, 최신 버전) Microsoft Edge 레거시 아님)
  * Safari(최신 버전, Mac만 해당)
  * Chrome(최신 버전)
  * Firefox(최신 버전)

## <a name="set-up-your-environment"></a>환경 설정

* **AmlCompute 생성 문제**: GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 AmlCompute를 만들지 못할 수 있습니다. 서비스에 대 한 지원 요청을 발생 시키거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 해제할 수 있습니다.

* **Azure Container Registry은 현재 리소스 그룹 이름에서 유니코드 문자를 지원 하지 않습니다**. 리소스 그룹 이름에 유니코드 문자가 포함 되어 있어 ACR 요청이 실패할 수 있습니다. 이 문제를 완화 하려면 다른 이름의 리소스 그룹에 ACR을 만드는 것이 좋습니다.

## <a name="work-with-data"></a>데이터 작업

### <a name="overloaded-azurefile-storage"></a>오버 로드 된 AzureFile 저장소

오류가 표시 `Unable to upload project files to working directory in AzureFile because the storage is overloaded` 되 면 다음 해결 방법을 적용 합니다.

데이터 전송과 같은 다른 작업에 대해 파일 공유를 사용 하는 경우 파일 공유를 사용 하 여 실행을 제출할 수 있도록 blob을 사용 하는 것이 좋습니다. 작업을 서로 다른 두 작업 영역 간에 분할할 수도 있습니다.

### <a name="passing-data-as-input"></a>데이터를 입력으로 전달

*  **TypeError: FileNotFound: 해당 파일 또는 디렉터리가 없습니다**.이 오류는 사용자가 제공 하는 파일 경로가 파일이 있는 위치가 아닌 경우에 발생 합니다. 계산 대상에서 데이터 집합을 탑재 한 위치와 파일을 참조 하는 방법이 일치 하는지 확인 해야 합니다. 결정적 상태를 보장 하려면 계산 대상에 데이터 집합을 탑재할 때 추상 경로를 사용 하는 것이 좋습니다. 예를 들어 다음 코드에서 계산 대상의 파일 시스템 루트 아래에 데이터 집합을 탑재 `/tmp` 합니다. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    선행 슬래시 ('/')를 포함 하지 않는 경우 `/mnt/batch/.../tmp/dataset` 데이터 집합을 탑재할 위치를 나타내기 위해 계산 대상에서 작업 디렉터리 (예:)에 접두사를 추가 해야 합니다.

### <a name="data-labeling-projects"></a>데이터 레이블 지정 프로젝트

|문제  |해결 방법  |
|---------|---------|
|Blob 데이터 저장소에 생성 되는 데이터 집합만 사용할 수 있습니다.     |  현재 릴리스의 알려진 제한 사항입니다.       |
|프로젝트를 만든 후에는 오랜 시간 동안 "초기화 중"이 표시 됩니다.     | 페이지를 수동으로 새로 고칩니다. 초기화는 초당 약 20 datapoints 진행 되어야 합니다. Autorefresh의 부족은 알려진 문제입니다.         |
|이미지를 검토할 때 새로 레이블이 지정 된 이미지는 표시 되지 않습니다.     |   레이블이 지정 된 모든 이미지를 로드 하려면 **첫 번째** 단추를 선택 합니다. **첫 번째** 단추는 목록 맨 앞으로 다시 이동 하지만 레이블이 지정 된 모든 데이터를 로드 합니다.      |
|개체 검색에 대 한 레이블을 지정 하는 동안 Esc 키를 누르면 왼쪽 위 모퉁이에 크기가 0 인 레이블이 생성 됩니다. 이 상태의 레이블 전송에 실패 합니다.     |   옆의 십자 표시를 클릭 하 여 레이블을 삭제 합니다.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> 데이터 드리프트 모니터

데이터 드리프트 모니터의 제한 사항 및 알려진 문제:

* 기록 데이터를 분석 하는 시간 범위는 모니터의 빈도 설정의 31 개로 제한 됩니다. 
* 기능 목록이 지정 되지 않은 경우 (모든 기능이 사용 됨) 200 기능의 제한 사항
* 계산 크기는 데이터를 처리할 수 있을 만큼 커야 합니다.
* 지정 된 모니터 실행의 시작 및 종료 날짜 내에 데이터 집합의 데이터가 있는지 확인 합니다.
* 데이터 집합 모니터는 50 개 이상의 행이 포함 된 데이터 집합에 대해서만 작동 합니다.
* 데이터 집합의 열 또는 기능은 다음 표의 조건에 따라 범주 또는 숫자로 분류 됩니다. 이 기능이 이러한 조건을 충족 하지 않는 경우 (예를 들어 >100 고유 값이 포함 된 문자열 형식의 열)이 기능은 데이터 드리프트 알고리즘에서 삭제 되었지만 여전히 프로 파일링 됩니다. 

    | 기능 유형 | 데이터 형식 | 조건 | 제한 사항 | 
    | ------------ | --------- | --------- | ----------- |
    | 범주 | string, bool, int, float | 이 기능의 고유 값 수는 100 보다 작고 행 수의 5% 미만입니다. | Null은 고유한 범주로 처리 됩니다. | 
    | 숫자 | int, float | 기능의 값은 숫자 데이터 형식이 며 범주 기능의 조건을 충족 하지 않습니다. | 값의 15% >null 인 경우 기능이 삭제 됩니다. | 

* [데이터 드리프트 모니터를 만들었지만](how-to-monitor-datasets.md) Azure Machine Learning studio의 데이터 **집합 모니터** 페이지에서 데이터를 볼 수 없는 경우 다음을 시도 합니다.

    1. 페이지 맨 위에서 올바른 날짜 범위를 선택 했는지 확인 합니다.  
    1. **데이터 집합 모니터** 탭에서 실험 링크를 선택 하 여 실행 상태를 확인 합니다.  이 링크는 테이블의 오른쪽 끝에 있습니다.
    1. 실행이 성공적으로 완료 되 면 드라이버 로그를 확인 하 여 생성 된 메트릭 수 또는 경고 메시지가 있는지 확인 합니다.  실험을 클릭 한 후 **출력 + 로그** 탭에서 드라이버 로그를 찾습니다.

* SDK 함수에서 `backfill()` 예상 되는 출력을 생성 하지 않는 경우 인증 문제가 원인일 수 있습니다.  이 함수에 전달할 계산을 만들 때는을 사용 하지 마십시오 `Run.get_context().experiment.workspace.compute_targets` .  대신, 다음과 같이 [ServicePrincipalAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) 를 사용 하 여 해당 함수에 전달 하는 계산을 만듭니다 `backfill()` . 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

* **긴 계산 준비 시간:**

계산 대상을 처음으로 연결 하거나 만드는 경우 몇 분 정도 걸릴 수 있습니다. 

모델 데이터 수집기는 blob storage 계정에 데이터가 도착할 때까지 최대 10 분 정도 걸릴 수 있습니다. 아래 셀이 실행 되도록 10 분 동안 기다립니다.

```python
import time
time.sleep(600)
```

* **실시간 끝점에 대 한 로그:**

실시간 끝점의 로그는 고객 데이터입니다. 실시간 끝점 문제 해결을 위해 다음 코드를 사용 하 여 로그를 사용 하도록 설정할 수 있습니다. 

[이 문서](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights#query-logs-for-deployed-models)에서 웹 서비스 끝점 모니터링에 대 한 자세한 내용을 참조 하세요.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
여러 테 넌 트가 있는 경우 다음 인증 코드를 추가 해야 할 수 있습니다. `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>모델 학습

* **Moduleerrors (이름이 지정 된 모듈 없음)**: Azure ML에서 실험을 제출 하는 동안 moduleerrors를 실행 하는 경우 학습 스크립트는 패키지를 설치 해야 하지만 추가 되지 않았음을 의미 합니다. 패키지 이름을 제공 하 고 나면 Azure ML은 학습 실행에 사용 되는 환경에 패키지를 설치 합니다. 

    [추정](concept-azure-machine-learning-architecture.md#estimators) 를 사용 하 여 실험을 제출 하는 경우 `pip_packages` 패키지를 설치 하려는 `conda_packages` 원본에 기반 하 여 평가기에서 또는 매개 변수를 통해 패키지 이름을 지정할 수 있습니다. 를 사용 하 여 모든 종속성이 포함 된 iisnode.yml 파일을 지정 `conda_dependencies_file` 하거나 매개 변수를 사용 하 여 txt 파일에 모든 pip 요구 사항을 나열할 수도 있습니다 `pip_requirements_file` . 평가기에서 사용 하는 기본 이미지를 재정의 하려는 고유한 Azure ML Environment 개체가 있는 경우 `environment` 평가기 생성자의 매개 변수를 통해 해당 환경을 지정할 수 있습니다.

    또한 Azure ML은 TensorFlow, PyTorch, 체 이너 및 추정에 대 한 프레임 워크 관련 제공 합니다. 이러한 추정을 사용 하 여 학습에 사용 되는 환경에서 핵심 프레임 워크 종속성을 대신 설치 하도록 합니다. 위에서 설명한 대로 추가 종속성을 지정 하는 옵션이 있습니다. 
 
    Azure ML에서 관리 하는 docker 이미지와 해당 콘텐츠는 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 볼 수 있습니다.
    프레임 워크 관련 종속성은 해당 프레임 워크 설명서- [체](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), 고 지 사항 [배우기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)에 나열 되어 있습니다.

    > [!Note]
    > 특정 패키지가 Azure 기계 학습에서 유지 관리 되는 이미지 및 환경에 추가할 수 있는 것으로 생각 되는 경우 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 GitHub 문제를 제기 하세요. 
 
* **Nameerror (이름이 정의 되지 않음), attributeerror (개체에 특성이 없음)**:이 예외는 학습 스크립트에서 제공 되어야 합니다. Azure Portal에서 로그 파일을 확인 하 여 지정 되지 않은 특정 이름 또는 특성 오류에 대 한 자세한 정보를 볼 수 있습니다. SDK에서를 사용 `run.get_details()` 하 여 오류 메시지를 확인할 수 있습니다. 또한 실행을 위해 생성 된 모든 로그 파일을 나열 합니다. 실행을 다시 전송 하기 전에 학습 스크립트를 확인 하 고 오류를 수정 하세요. 

* **Horovod 종료**됨: 대부분의 경우 "AbortedError: Horovod가 종료 되었습니다."이 예외는 Horovod 종료를 일으킨 프로세스 중 하나에 기본 예외가 있음을 의미 합니다. MPI 작업의 각 순위는 Azure ML의 전용 로그 파일을 가져옵니다. 이러한 로그의 이름은 `70_driver_logs` 입니다. 분산 교육의 경우 로그를 쉽게 구분할 수 있도록 로그 이름의 접미사가 지정 됩니다 `_rank` . Horovod가 종료 되는 정확한 오류를 찾으려면 모든 로그 파일을 확인 하 고 `Traceback` driver_log 파일의 끝에 있는을 찾습니다. 이러한 파일 중 하나는 실제 기본 예외를 제공 합니다. 

* **실행 또는 실험 삭제**: 실험을 사용 하 [여 보관 하거나](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) "실험 보관" 단추를 통해 Azure Machine Learning studio 클라이언트의 실험 탭 보기에서 보관할 수 있습니다. 이 동작을 수행 하면 쿼리 및 뷰 목록에서 실험을 숨길 수 있지만 삭제 하지는 않습니다.

    개별 실험 또는 실행을 영구적으로 삭제 하는 것은 현재 지원 되지 않습니다. 작업 영역 자산을 삭제 하는 방법에 대 한 자세한 내용은 [Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제](how-to-export-delete-data.md)를 참조 하세요.

* **메트릭 문서가 너무 큼**: Azure Machine Learning 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체 크기에 대 한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 커서" 오류가 발생 하는 경우 목록을 더 작은 청크로 분할 해 보세요. 예를 들면 다음과 같습니다.

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    내부적으로 Azure ML은 동일한 메트릭 이름을 가진 블록을 연속 된 목록에 연결 합니다.

## <a name="automated-machine-learning"></a>자동화된 기계 학습

* 최신 **버전에 대 한 AutoML 종속성의 최근 업그레이드는 compatibilitity를 중단 합니다**. SDK 버전 1.13.0에서 이전 패키지에 고정 된 이전 버전의 비 호환성으로 인해 이전 sdk에 모델이 로드 되지 않고 현재 고정 된 최신 버전입니다. 다음과 같은 오류가 표시 됩니다.
  * 모듈을 찾을 수 없음: 예. `No module named 'sklearn.decomposition._truncated_svd` ,
  * 가져오기 오류: 예: `ImportError: cannot import name 'RollingOriginValidator'` ,
  * 특성 오류: 예. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  이 문제를 해결 하려면 AutoML SDK 교육 버전에 따라 다음 두 단계 중 하나를 수행 합니다.
  1. AutoML SDK 교육 버전이 1.13.0 보다 큰 경우 및가 필요 `pandas == 0.25.1` `sckit-learn==0.22.1` 합니다. 버전이 일치 하지 않는 경우 아래와 같이 scikit 및/또는 pandas를 올바른 버전으로 업그레이드 합니다.
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. AutoML SDK 교육 버전이 1.12.0 보다 작거나 같은 경우 및가 필요 `pandas == 0.23.4` `sckit-learn==0.20.3` 합니다. 버전이 일치 하지 않는 경우 아래와 같이 scikit 및/또는 pandas를 올바른 버전으로 다운 그레이드 합니다.
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **TensorFlow**: SDK 버전 1.5.0을 기준으로 자동화 된 machine learning은 기본적으로 TensorFlow 모델을 설치 하지 않습니다. TensorFlow를 설치 하 고 자동 ML 실험에서 사용 하려면 TensorFlow = = 1.12.0 via CondaDependecies를 설치 합니다. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **실험 차트**: 자동화 된 ML 실험 반복에 표시 되는 이진 분류 차트 (정밀도-리콜, ROC, 게인 곡선 등)는 4/12 이후 사용자 인터페이스에서 올바르게 렌더링 되지 않습니다. 차트 플롯에는 현재 더 낮은 결과가 포함 된 모델을 더 잘 수행 하는 역 결과가 표시 됩니다. 확인 중입니다.

* Databricks에서 자동화 된 machine learning **실행 취소**: Azure Databricks에서 자동화 된 machine learning 기능을 사용 하는 경우 실행을 취소 하 고 새 실험 실행을 시작 하려면 Azure Databricks 클러스터를 다시 시작 합니다.

* **자동화 된 machine learning에 대 한 Databricks >10 회 반복**: 자동화 된 기계 학습 설정에서 10 개 이상의 반복이 있는 경우 `show_output` 실행을 제출할 때를로 설정 `False` 합니다.

* **AZURE MACHINE LEARNING sdk 및 자동화 된 기계 학습을 위한 Databricks 위젯**: Azure Machine Learning SDK 위젯은 Databricks 노트북에서 HTML 위젯을 구문 분석할 수 없기 때문에 지원 되지 않습니다. Azure Databricks 노트북 셀에서이 Python 코드를 사용 하 여 포털에서 위젯을 볼 수 있습니다.

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup 실패**: 
    * Windows에서는 Anaconda 프롬프트에서 automl_setup를 실행 합니다. Miniconda를 설치 하려면 [여기](https://docs.conda.io/en/latest/miniconda.html)를 클릭 하세요.
    * 명령을 실행 하 여 32 비트가 아닌 conda 64 비트를 설치 했는지 확인 `conda info` 합니다. 는 `platform` `win-64` Windows 또는 Mac 용 이어야 합니다 `osx-64` .
    * Conda 4.4.10 이상이 설치 되어 있는지 확인 합니다. 명령을 사용 하 여 버전을 확인할 수 있습니다 `conda -V` . 이전 버전이 설치 되어 있는 경우 명령을 사용 하 여 업데이트할 수 있습니다 `conda update conda` .
    * 용 `gcc: error trying to exec 'cc1plus'`
      *  `gcc: error trying to exec 'cc1plus': execvp: No such file or directory`오류가 발생 하면 해당 명령을 사용 하 여 build essentials를 설치 `sudo apt-get install build-essential` 합니다.
      * 새 이름을 automl_setup에 대 한 첫 번째 매개 변수로 전달 하 여 새 conda 환경을 만듭니다. 를 사용 하 여 기존 conda 환경을 보고 `conda env list` 제거 `conda env remove -n <environmentname>` 합니다.
      
* **automl_setup_linux sh 실패**: automl_setup_linus. sh가 오류가 발생 한 Ubuntu Linux에서 실패 합니다. `unable to execute 'gcc': No such file or directory`-
  1. 아웃 바운드 포트 53 및 80을 사용 하도록 설정 했는지 확인 합니다. Azure VM에서 VM을 선택 하 고 네트워킹을 클릭 하 여 Azure Portal에서이 작업을 수행할 수 있습니다.
  2. `sudo apt-get update` 명령을 실행합니다.
  3. `sudo apt-get install build-essential --fix-missing` 명령을 실행합니다.
  4. `automl_setup_linux.sh`다시 실행

* **구성. ipynb 실패**:
  * 로컬 conda의 경우 먼저 automl_setup susccessfully를 실행 했는지 확인 합니다.
  * Subscription_id 올바른지 확인 하십시오. 모든 서비스를 선택 하 고 구독을 선택 하 여 Azure Portal에서 subscription_id를 찾습니다. 문자 "<" 및 ">"는 subscription_id 값에 포함 되지 않아야 합니다. 예를 들어에는 `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` 유효한 형식이 있습니다.
  * 구독에 대 한 참가자 또는 소유자의 액세스 권한이 있는지 확인 합니다.
  * 지역이 지원 되는 지역,,,,,,, 중 하나 인지 확인 `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` `southcentralus` 합니다.
  * Azure Portal을 사용 하 여 지역에 대 한 액세스를 보장 합니다.
  
* **AutoMLConfig 가져오기 실패**: 자동화 된 machine learning 버전 1.0.76에서 패키지 변경 내용이 있습니다 .이는 새 버전으로 업데이트 하기 전에 이전 버전을 제거 해야 합니다. `ImportError: cannot import name AutoMLConfig`V 1.0.76에서 v 1.0.76 이상으로 업그레이드 한 후에이 발생 하면을 실행 하 여 오류를 해결 한 다음를 실행 `pip uninstall azureml-train automl` `pip install azureml-train-auotml` 합니다. Automl_setup 스크립트는이를 자동으로 수행 합니다. 

* **작업 영역. from_config 실패**: Ws = 작업 영역. from_config () ' 호출이 실패 합니다.
  1. 구성. ipynb 노트북이 성공적으로 실행 되었는지 확인 합니다.
  2. 가 실행 된 폴더에 없는 폴더에서 노트북을 실행 하는 경우 `configuration.ipynb` 폴더 aml_config 폴더를 복사 하 고 해당 폴더에 포함 된 config.js파일을 새 폴더에 복사 합니다. 작업 영역. from_config 노트북 폴더 또는 해당 부모 폴더에 대 한 config.js를 읽습니다.
  3. 새 구독, 리소스 그룹, 작업 영역 또는 지역이 사용 중인 경우에는 다시 노트북을 실행 해야 `configuration.ipynb` 합니다. 지정 된 구독에서 지정 된 리소스 그룹에 작업 영역이 이미 있는 경우에만 config.js의 변경 내용이 적용 됩니다.
  4. 지역을 변경 하려면 작업 영역, 리소스 그룹 또는 구독을 변경 하세요. `Workspace.create` 는 이미 있는 경우 작업 영역을 만들거나 업데이트 하지 않습니다. 지정 된 지역이 다른 경우에도 마찬가지입니다.
  
* **샘플 노트북 실패**: 샘플 노트북에 오류가 발생 하면 preperty, method 또는 library가 존재 하지 않습니다.
  * Correctcorrect 커널이 jupyter 노트북에서 선택 되었는지 확인 합니다. 커널은 노트북 페이지의 오른쪽 위에 표시 됩니다. 기본값은 azure_automl입니다. 커널은 노트북의 일부로 저장 됩니다. 따라서 새 conda 환경으로 전환 하는 경우 노트북에서 새 커널을 선택 해야 합니다.
      * Azure Notebooks의 경우 Python 3.6 이어야 합니다. 
      * 로컬 conda 환경의 경우에는 automl_setup에서 지정한 conda envioronment 이름 이어야 합니다.
  * 사용 중인 SDK 버전에 대 한 노트북이 있는지 확인 합니다. Jupyter 노트북 셀에서를 실행 하 여 SDK 버전을 확인할 수 있습니다 `azureml.core.VERSION` . 단추를 클릭 하 `Branch` `Tags` 고 탭을 선택한 다음 버전을 선택 하 여 GitHub에서 이전 버전의 샘플 노트북을 다운로드할 수 있습니다.

* **Windows에서 Numpy 가져오기 실패**: 일부 windows 환경에서는 최신 Python 버전 3.6.8를 사용 하 여 Numpy를 로드 하는 동안 오류가 발생 합니다. 이 문제가 표시 되는 경우 Python 버전 3.6.7으로 시도 하세요.

* **Numpy 가져오기 실패**: 자동화 된 ml conda 환경에서 tensorflow 버전을 확인 합니다. 지원 되는 버전은 < 1.13입니다. 버전이 >= 1.13 인 경우 환경에서 tensorflow를 제거 합니다. tensorflow의 버전을 확인 하 고 다음과 같이 제거할 수 있습니다.
  1. 명령 셸을 시작 하 고 자동 ml 패키지가 설치 된 conda 환경을 활성화 합니다.
  2. 을 입력 `pip freeze` 하 고 검색 `tensorflow` 하는 경우 나열 된 버전 < 1.13 이어야 합니다.
  3. 표시 된 버전이 지원 되는 버전이 아닌 경우 `pip uninstall tensorflow` 명령 셸에서 y를 입력 하 여 확인 합니다.

## <a name="deploy--serve-models"></a>모델 배포 및 제공

다음 오류에 대해이 작업을 수행 합니다.

|오류  | 해결 방법  |
|---------|---------|
|웹 서비스 배포 시 이미지 작성 오류     |  이미지 구성을 위해 "pConda acl = = 1.2.1"을 파일에 대 한 pip 종속성으로 추가 합니다.       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   배포에 사용 되는 Vm의 SKU를 메모리를 더 많이 포함 하는 Vm으로 변경 합니다. |
|FPGA 오류     |  요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS 클러스터의 Azure Machine Learning 구성 요소 업데이트

Azure Kubernetes Service 클러스터에 설치 된 Azure Machine Learning 구성 요소에 대 한 업데이트를 수동으로 적용 해야 합니다. 

Azure Machine Learning 작업 영역에서 클러스터를 분리 하 여 이러한 업데이트를 적용 한 다음 클러스터를 작업 영역에 다시 연결할 수 있습니다. 클러스터에서 TLS를 사용 하는 경우 클러스터를 다시 연결할 때 TLS/SSL 인증서와 개인 키를 제공 해야 합니다. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

TLS/SSL 인증서와 개인 키가 더 이상 없거나 Azure Machine Learning에서 생성 한 인증서를 사용 하는 경우를 사용 하 여 클러스터에 연결 하 `kubectl` 고 암호를 검색 하 여 클러스터를 분리 하기 전에 파일을 검색할 수 있습니다 `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes는 암호를 base-64로 인코딩된 형식으로 저장 합니다. `cert.pem` `key.pem` 에 제공 하기 전에 비밀의 및 구성 요소를 64으로 디코드 해야 `attach_config.enable_ssl` 합니다. 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes 서비스 실패의 Webservices

를 사용 하 여 클러스터에 연결 하 여 Azure Kubernetes Service에서 많은 webservice 오류를 디버그할 수 있습니다 `kubectl` . 을 `kubeconfig.json` 실행 하 여 Azure Kubernetes 서비스 클러스터에 대 한를 가져올 수 있습니다.

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>인증 오류

원격 작업에서 컴퓨팅 대상에 대한 관리 작업을 수행하는 경우 다음 오류 중 하나가 나타납니다. 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

예를 들어 원격 실행을 위해 제출된 ML 파이프라인에서 컴퓨팅 대상을 만들거나 연결하려고 시도하면 오류가 나타납니다.

## <a name="missing-user-interface-items-in-studio"></a>스튜디오에 사용자 인터페이스 항목이 없습니다.

Azure 역할 기반 액세스 제어를 사용 하 여 Azure Machine Learning에서 수행할 수 있는 작업을 제한할 수 있습니다. 이러한 제한으로 인해 Azure Machine Learning studio에서 사용자 인터페이스 항목이 표시 되지 않을 수 있습니다. 예를 들어 계산 인스턴스를 만들 수 없는 역할이 할당 된 경우 계산 인스턴스를 만드는 옵션은 스튜디오에 표시 되지 않습니다.

자세한 내용은 [사용자 및 역할 관리](how-to-assign-roles.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에 대 한 자세한 문제 해결 문서를 참조 하세요.

* [Azure Machine Learning에서 Docker 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Machine learning 파이프라인 디버그](how-to-debug-pipelines.md)
* [Azure Machine Learning SDK에서 ParallelRunStep 클래스 디버그](how-to-debug-parallel-run-step.md)
* [VS Code 사용 하 여 machine learning 계산 인스턴스의 대화형 디버깅](how-to-debug-visual-studio-code.md)
* [Application Insights를 사용 하 여 기계 학습 파이프라인 디버그](how-to-debug-pipelines-application-insights.md)
