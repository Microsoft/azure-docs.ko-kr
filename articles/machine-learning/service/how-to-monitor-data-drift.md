---
title: AKS 배포에서 데이터 드리프트 (미리 보기)를 검색 하는 방법
titleSuffix: Azure Machine Learning service
description: Azure Kubernetes Service에서 데이터 드리프트 Azure Machine Learning 서비스에서 모델 배포를 검색 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333165"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 배포 된 모델에서 데이터 드리프트 (미리 보기)를 검색 하는 방법
이 문서에 대 한 모니터링 하는 방법을 알아봅니다 [데이터 드리프트](concept-data-drift.md) 배포 된 모델의 학습 데이터 집합 및 유추 데이터 사이입니다. 

데이터 드리프트 모델 정확도 시간에 지나면서 가치가 저하 되므로 여기서는 가장 중요 한 이유 중 하나입니다. 프로덕션 환경에서 모델을 제공 하는 데이터 모델의 학습에 사용 되는 데이터와 다른 경우 발생 합니다. Azure Machine Learning 서비스는 데이터 드리프트 탐지기를 사용 하 여 데이터 드리프트를 모니터링할 수 있습니다. 드리프트가 검색 하는 경우 서비스에 경고를 보낼 수 있습니다.  

> [!Note]
> 이 서비스 (미리 보기)는 구성 옵션의 제한. 참조 하세요 우리의 [API 설명서](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) 하 고 [릴리스](azure-machine-learning-release-notes.md) 세부 정보 및 업데이트에 대 한 합니다. 

Azure Machine Learning 서비스를 사용 하 여 AKS에 배포 된 모델에 대 한 입력을 모니터링할 수 있으며 모델의 학습 데이터 집합에이 데이터를 비교할 수 있습니다. 정기적으로 유추 데이터가 [스냅숏 생성 및 프로 파일링](how-to-explore-prepare-data.md), 다음 데이터 드리프트 분석을 생성 하기 위해 기준 데이터 집합에 대해 계산 하는: 

+ 데이터 드리프트를 드리프트 계수 호출의 크기를 측정 합니다.
+ 측정값 데이터 기여 기능 데이터 드리프트 발생을 알리는 기능별 드리프트 합니다.
+ 측정값에는 메트릭을 거리입니다. 현재 Wasserstein 및 에너지 거리 계산 됩니다.
+ 기능의 배포를 측정합니다. 현재 커널 밀도 예측 및 히스토그램입니다.
+ 데이터 경고 전자 메일을 통해 드리프트를 보냅니다.

이러한 메트릭을 계산 하는 방법에 대 한 내용은 참조는 [데이터 드리프트 개념](concept-data-drift.md) 문서.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.

- [환경을 설정](how-to-configure-environment.md), 한 다음 데이터 드리프트 다음 명령을 사용 하 여 SDK를 설치 합니다.

    ```
    pip install azureml-contrib-datadrift
    ```

- 만들기는 [데이터 집합](how-to-create-register-datasets.md) 모델의 학습 데이터에서.

- 학습 데이터 집합을 지정할 때 [등록](concept-model-management-and-deployment.md) 모델입니다. 다음 예제는 `datasets` 학습 데이터 집합을 지정 하려면 매개 변수:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- 설정 된 [모델 데이터 수집기](how-to-enable-data-collection.md) AKS 배포 모델에서에서 데이터를 수집 하 고 데이터를 확인 하려면에서 수집 되는 `modeldata` blob 컨테이너입니다.

## <a name="import-dependencies"></a>종속성 가져오기 
이 가이드에서 사용 되는 종속성을 가져옵니다.

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>데이터 드리프트를 구성 합니다. 

다음 Python 예제를 구성 하는 방법을 보여 줍니다는 `DataDriftDetector` 개체:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

자세한 내용은 참조는 [DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) 참조 합니다.

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector 실행 제출

구성 DataDriftDetector를 사용 하 여 제출할 수 있습니다는 [실행 하는 데이터 드리프트](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) 모델에 대 한 지정된 된 날짜에 있습니다. 

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="get-data-drift-analysis-results"></a>데이터 드리프트 분석 결과 가져오기

다음 Python 예제에서는 관련 데이터 드리프트 메트릭을 그리는 방법을 보여 줍니다. 반환 된 메트릭을 사용 하 여 사용자 지정 시각화를 만들 수 있습니다.

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![데이터 드리프트 Show](media/how-to-monitor-data-drift/drift_show.png)

계산 되는 메트릭에 대 한 자세한 내용은 참조는 [데이터 드리프트 개념](concept-data-drift.md) 문서.

## <a name="schedule-data-drift-detection"></a>일정 데이터 드리프트 검색 

지정 된 빈도로 실행을 DataDriftDetector 수행 데이터 드리프트 일정을 사용 하도록 설정 합니다. 드리프트 계수를 지정 된 임계값을 초과 하는 경우 전자 메일이 전송 됩니다. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Azure portal에서 모델 세부 정보 페이지에서 데이터 드리프트 감지기의 구성을 볼 수 있습니다.

![Azure portal 데이터 드리프트 구성](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Azure ML 작업 영역 UI에서 결과 보기

Azure ML 작업 영역 UI에 결과 보려면 모델 페이지로 이동 합니다. 모델의 세부 정보 탭에서 데이터 드리프트 구성이 표시 됩니다. ' 데이터 드리프트 (미리 보기)' 탭 데이터 드리프트 메트릭을 시각화 출시 되었습니다. 

![Azure portal 데이터 드리프트](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>경고 설정 

경고 임계값 및 메일 주소를 제공 드리프트 계수를 설정 하는 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 드리프트 계수 임계값을 초과 하는 경우 전자 메일 경고가 전송 됩니다. 모든 데이터 드리프트 메트릭은 사용자 지정 경고 또는 작업을 설정할 수 있습니다에 대 한 Azure Machine Learning 서비스 작업 영역과 연결 된 app insights 리소스에 저장 됩니다. App insights 쿼리를 전자 메일 경고의 링크를 따르면 됩니다.

![데이터 드리프트 전자 메일 경고](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>다음 단계

* 데이터 드리프트를 사용 하는 전체 예제를 보려면 합니다 [Azure ML 데이터 드리프트 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)합니다. 이 Jupyter Notebook 사용을 보여 줍니다.는 [Azure 공개 데이터 집합](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) 날씨 예측 모델을 학습 하려면 AKS에 배포 및 데이터 드리프트를 모니터링 합니다. 

* 크게 주시면 사용자 질문, 의견 또는 제안 데이터 드리프트 가용성 일반 방향으로 이동 합니다. 아래 제품 피드백 단추를 사용 합니다. 
