---
title: Azure Machine Learning 파이프라인 예약
titleSuffix: Azure Machine Learning
description: Python 용 Azure Machine Learning SDK를 사용 하 여 Azure Machine Learning 파이프라인을 예약 합니다. 예약 된 파이프라인을 사용 하 여 데이터 처리, 학습 및 모니터링과 같은 일상적인 시간 사용 작업을 자동화할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: fed411ea171274513308ec3efa68da80e4d25f8a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116749"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Python 용 Azure Machine Learning SDK를 사용 하 여 기계 학습 파이프라인 예약

이 문서에서는 Azure에서 실행 하기 위해 프로그래밍 방식으로 파이프라인을 예약 하는 방법을 알아봅니다. 경과 된 시간 또는 파일 시스템 변경 내용에 따라 일정을 만들도록 선택할 수 있습니다. 시간 기반 일정을 사용 하 여 데이터 드리프트 모니터링과 같은 일상적인 작업을 처리할 수 있습니다. 변경 기반 일정을 사용 하 여 새 데이터를 업로드 하거나 이전 데이터를 편집 하는 등의 비정상 또는 예기치 않은 변경 내용에 대응할 수 있습니다. 일정을 만드는 방법을 학습 한 후에는이를 검색 및 비활성화 하는 방법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정](https://aka.ms/AMLFree)을 만듭니다.

* Python 용 Azure Machine Learning SDK가 설치 된 Python 환경 자세한 내용은 [Azure Machine Learning를 사용 하 여 학습 및 배포를 위한 재사용 가능한 환경 만들기 및 관리](how-to-use-environments.md) 를 참조 하세요.

* 게시 된 파이프라인이 있는 Machine Learning 작업 영역입니다. Azure Machine Learning SDK를 사용 하 여 [machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md)에서 빌드된 빌드를 사용할 수 있습니다.

## <a name="initialize-the-workspace--get-data"></a>작업 영역을 초기화 하 여 데이터 가져오기 &

파이프라인을 예약 하려면 작업 영역에 대 한 참조, 게시 된 파이프라인의 식별자 및 일정을 만들려는 실험의 이름을 제공 해야 합니다. 다음 코드를 사용 하 여 이러한 값을 가져올 수 있습니다.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>일정 만들기

반복 하 여 파이프라인을 실행 하려면 일정을 만듭니다. `Schedule`은 파이프라인, 실험 및 트리거를 연결 합니다. 트리거는 실행 간 대기를 설명 하는`ScheduleRecurrence` 이거나, 변경 내용을 감시 하는 디렉터리를 지정 하는 데이터 저장소 경로 일 수 있습니다. 두 경우 모두 일정을 만들 실험의 이름과 파이프라인 식별자가 필요 합니다.

Python 파일의 맨 위에서 `Schedule` 및 `ScheduleRecurrence` 클래스를 가져옵니다.

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>시간 기반 일정 만들기

`ScheduleRecurrence` 생성자에는 "Minute", "Hour", "Day", "Week" 또는 "Month" 문자열 중 하나 여야 하는 필수 `frequency` 인수가 있습니다. 또한 일정 시작 사이에 경과 해야 하는 `frequency` 단위의 수를 지정 하는 정수 `interval` 인수도 필요 합니다. 선택적 인수를 사용 하면 [SCHEDULERECURRENCE SDK 문서](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)에 설명 된 대로 시작 시간에 대해 보다 구체적으로 지정할 수 있습니다.

15 분 마다 실행을 시작 하는 `Schedule`을 만듭니다.

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>변경 기반 일정 만들기

파일 변경에 의해 트리거되는 파이프라인이 시간 기반 일정 보다 효율적일 수 있습니다. 예를 들어 파일이 변경 될 때 또는 새 파일이 데이터 디렉터리에 추가 될 때 전처리 단계를 수행 하는 것이 좋습니다. 데이터 저장소에 대 한 변경 내용 또는 데이터 저장소 내의 특정 디렉터리 내 변경 내용을 모니터링할 수 있습니다. 특정 디렉터리를 모니터링 하는 경우 해당 디렉터리의 하위 디렉터리에 있는 변경 내용은 실행을 트리거하지 _않습니다_ .

파일-사후 `Schedule`을 만들려면 [Schedule. 만들기](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)에 대 한 호출에서 `datastore` 매개 변수를 설정 해야 합니다. 폴더를 모니터링 하려면 `path_on_datastore` 인수를 설정 합니다.

`polling_interval` 인수를 사용 하면 데이터 저장소에서 변경 내용이 확인 되는 빈도 (분)를 지정할 수 있습니다.

파이프라인이 [데이터 경로](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)를 사용 하 여 생성 된 경우 `data_path_parameter_name` 인수를 설정 하 여 해당 변수를 변경 된 파일의 이름으로 설정할 수 있습니다.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>일정을 만들 때 선택적 인수

앞에서 설명한 인수 외에도 `"Disabled"`에 대 한 `status` 인수를 설정 하 여 비활성 일정을 만들 수 있습니다. 마지막으로 `continue_on_step_failure`를 사용 하 여 파이프라인의 기본 오류 동작을 재정의 하는 부울을 전달할 수 있습니다.

## <a name="view-your-scheduled-pipelines"></a>예약 된 파이프라인 보기

웹 브라우저에서 Azure Machine Learning로 이동 합니다. 탐색 패널의 **끝점** 섹션에서 **파이프라인 끝점**을 선택 합니다. 그러면 작업 영역에 게시 된 파이프라인의 목록으로 이동 합니다.

![AML의 파이프라인 페이지](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

이 페이지에서 이름, 설명, 상태 등의 작업 영역에 있는 모든 파이프라인에 대 한 요약 정보를 볼 수 있습니다. 파이프라인을 클릭 하 여 드릴업 합니다. 결과 페이지에 파이프라인에 대 한 자세한 내용이 있으며 개별 실행으로 드릴 다운할 수 있습니다.

## <a name="deactivate-the-pipeline"></a>파이프라인 비활성화

게시 된 `Pipeline` 있지만 예약 되지 않은 경우 다음을 사용 하 여 사용 하지 않도록 설정할 수 있습니다.

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

파이프라인이 예약 된 경우 먼저 일정을 취소 해야 합니다. 포털에서 또는를 실행 하 여 일정의 식별자를 검색 합니다.

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

`schedule_id`를 사용 하지 않도록 설정 하려는 경우 다음을 실행 합니다.

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

그런 다음 `Schedule.list(ws)`를 다시 실행 하면 빈 목록이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Python 용 Azure Machine Learning SDK를 사용 하 여 두 가지 다른 방법으로 파이프라인을 예약 했습니다. 경과 된 클록 시간에 따라 일정 하나가 되풀이 됩니다. 지정 된 `Datastore` 또는 해당 저장소의 디렉터리 내에서 파일이 수정 된 경우에는 다른 일정이 실행 됩니다. 포털을 사용 하 여 파이프라인 및 개별 실행을 검사 하는 방법을 살펴보았습니다. 마지막으로, 파이프라인의 실행이 중지 되도록 일정을 사용 하지 않도록 설정 하는 방법을 알아보았습니다.

자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [일괄 처리 점수 매기기에 Azure Machine Learning 파이프라인 사용](tutorial-pipeline-batch-scoring-classification.md)

* [파이프라인](concept-ml-pipelines.md) 에 대 한 자세한 정보
* [Jupyter를 사용 하 여 Azure Machine Learning 탐색](samples-notebooks.md) 에 대해 자세히 알아보기

