---
title: Azure Machine Learning 파이프라인 이란?
description: 기계 학습 (ML) 파이프라인이 기계 학습 워크플로를 빌드, 최적화 및 관리 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 08/17/2020
ms.custom: devx-track-python
ms.openlocfilehash: a62d12aa92e41d4a91f963d962616af11d917195
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604456"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인 이란?

이 문서에서는 기계 학습 워크플로를 빌드, 최적화 및 관리 하는 데 Azure Machine Learning 파이프라인이 어떻게 도움이 될 지 알아봅니다. 이러한 워크플로에는 다음과 같은 다양 한 이점이 있습니다. 

+ 단순함
+ 속도
+ 반복성
+ 유연성
+ 버전 관리 및 추적
+ 성과 
+ 품질 보증
+ 비용 제어

이러한 이점은 machine learning 프로젝트가 순수한 탐색을 벗어나 반복으로 이동 하는 즉시 중요 합니다. 간단한 1 단계 파이프라인도 중요할 수 있습니다. 기계 학습 프로젝트는 종종 복잡 한 상태 이며 단일 워크플로의 정확한 이룰 있었습니다을 간단 하 게 처리할 수 있도록 하는 릴리프 일 수 있습니다.

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>어떤 Azure 파이프라인 기술을 사용 해야 하나요?

Azure 클라우드는 각각 다른 용도로 여러 다른 파이프라인을 제공 합니다. 다음 표에서는 다양 한 파이프라인 및 사용 되는 방법을 보여 줍니다.

| 시나리오 | 주 가상 사용자 | Azure 제품 | OSS 제품 | 정식 파이프 | 강점 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 모델 오케스트레이션 (기계 학습) | 데이터 과학자 | Azure Machine Learning 파이프라인 | Kubeflow 파이프라인 | 데이터 > 모델 | 배포, 캐싱, 코드 우선, 다시 사용 | 
| 데이터 오케스트레이션 (데이터 준비) | 데이터 엔지니어 | [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 데이터 > 데이터 | 강력 하 게 형식화 된 이동, 데이터 중심 활동 |
| 코드 & 앱 오케스트레이션 (CI/CD) | 앱 개발자/Ops | [Azure DevOps 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 코드 + 모델-> App/Service | 가장 강력 하 고 유연한 활동 지원, 승인 큐, 제어를 사용 하는 단계 | 

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 파이프라인에서 수행할 수 있는 작업은 무엇 인가요?

Azure Machine Learning 파이프라인은 전체 기계 학습 작업에 대해 독립적으로 실행 되는 워크플로입니다. 하위 작업은 파이프라인 내에서 일련의 단계로 캡슐화됩니다. Azure Machine Learning 파이프라인은 Python 스크립트를 호출 하는 것 처럼 간단할 수 있으므로 어떤 것 _이라도 수행할 수 있습니다._ 파이프라인은 다음과 같은 기계 학습 작업에 집중 _해야 합니다_ .

+ 데이터 준비(가져오기, 유효성 검사 및 정리, 마이닝/변환, 정규화 및 준비 포함)
+ 학습 구성(인수 매개 변수화, 파일 경로 및 구성 로깅/보고 포함)
+ 효율적이 고 반복적으로 교육 하 고 유효성을 검사 합니다. 효율성은 특정 데이터 하위 집합, 다른 하드웨어 계산 리소스, 분산 처리 및 진행률 모니터링을 지정 하 여 가져올 수 있습니다.
+ 배포(버전 관리, 크기 조정, 프로비저닝 및 액세스 제어 포함)

독립적인 단계를 통해 여러 데이터 과학자가 과도 한 처리 시간이 소모 계산 리소스 없이 동시에 동일한 파이프라인에서 작업할 수 있습니다. 또한 별도의 단계를 통해 각 단계에 서로 다른 계산 형식/크기를 쉽게 사용할 수 있습니다.

파이프라인 설계 후에는 종종 파이프라인의 학습 루프를 미세 조정합니다. 파이프라인을 다시 실행 하면 실행이 업데이트 된 학습 스크립트와 같이 다시 실행 해야 하는 단계로 이동 합니다. 다시 실행할 필요가 없는 단계는 건너뜁니다. 

파이프라인을 사용 하 여 작업 마다 다른 하드웨어를 사용 하도록 선택할 수 있습니다. Azure는 사용 하는 다양 한 [계산 대상을](concept-azure-machine-learning-architecture.md) 조정 하므로 중간 데이터가 다운스트림 계산 대상으로 원활 하 게 흐릅니다.

Azure Portal 또는 [작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com)에서 직접 [파이프라인 실험의 메트릭을 추적할](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) 수 있습니다. 파이프라인이 게시 된 후에는 모든 플랫폼 또는 스택에서 파이프라인을 다시 실행할 수 있는 REST 끝점을 구성할 수 있습니다.

간단히 말해서, 기계 학습 수명 주기의 모든 복잡 한 작업은 파이프라인에 도움이 될 수 있습니다. 다른 Azure 파이프라인 기술에는 고유한 강도가 있습니다. 뛰어나지만 파이프라인은 데이터를 사용 하 여 작업 하 고 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) 지속적인 통합 및 배포에 적합 한 도구입니다. [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) 그러나 사용자의 포커스가 machine learning 인 경우 Azure Machine Learning 파이프라인이 워크플로 요구에 가장 적합 한 선택이 될 수 있습니다. 

### <a name="analyzing-dependencies"></a>종속성 분석

많은 프로그래밍 에코 시스템 리소스, 라이브러리 또는 컴파일 종속성을 오케스트레이션 하는 도구를 포함 합니다. 일반적으로 이러한 도구는 파일 타임 스탬프를 사용 하 여 종속성을 계산 합니다. 파일이 변경 되 면 해당 파일 및 해당 종속 파일만 업데이트 (다운로드, 다시 컴파일 또는 패키지) 됩니다. Azure ML 파이프라인은이 개념을 확장 합니다. 기존 빌드 도구와 마찬가지로 파이프라인은 단계 간의 종속성을 계산 하 고 필요한 재계산만 수행 합니다. 

Azure ML 파이프라인에서 종속성 분석은 단순한 타임 스탬프 보다 더 정교 합니다. 모든 단계는 다른 하드웨어 및 소프트웨어 환경에서 실행 될 수 있습니다. 데이터 준비는 시간이 많이 소요 될 수 있지만, 강력한 Gpu를 사용 하는 하드웨어에서 실행할 필요가 없습니다. 특정 단계에는 OS 관련 소프트웨어가 필요할 수 있으며, 분산 교육 등을 사용 하는 등의 작업을 할 수 있습니다. 

Azure Machine Learning은 파이프라인 단계 간의 모든 종속성을 자동으로 오케스트레이션 합니다. 이 오케스트레이션에는 Docker 이미지 회전, 계산 리소스 연결 및 분리, 단계 간 데이터 이동이 일관 되 고 자동으로 포함 될 수 있습니다.

### <a name="coordinating-the-steps-involved"></a>관련 단계 조정

개체를 만들고 실행 하는 경우 `Pipeline` 다음과 같은 개략적인 단계가 발생 합니다.

+ 각 단계에서 서비스는 다음에 대 한 요구 사항을 계산 합니다.
    + 하드웨어 계산 리소스
    + OS 리소스 (Docker 이미지)
    + 소프트웨어 리소스 (Conda/virtualenv 종속성)
    + 데이터 입력 
+ 서비스는 단계 간 종속성을 확인 하 여 동적 실행 그래프를 생성 합니다.
+ 실행 그래프의 각 노드가 실행 될 때:
    + 이 서비스는 필요한 하드웨어 및 소프트웨어 환경을 구성 합니다 (기존 리소스를 다시 사용 하는 경우).
    + 단계를 실행 하 여 포함 하는 개체에 로깅 및 모니터링 정보 제공 `Experiment`
    + 단계가 완료 되 면 해당 출력은 다음 단계에 대 한 입력으로 준비 되 고 저장소에 기록 됩니다.
    + 더 이상 필요 하지 않은 리소스는 종료 및 분리 됨

![파이프라인 단계](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK를 사용 하 여 파이프라인 빌드

[Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)에서 파이프라인은 모듈에 정의 된 python 개체입니다 `azureml.pipeline.core` . [파이프라인](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 개체는 하나 이상의 [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) 개체의 순서가 지정 된 시퀀스를 포함 합니다. `PipelineStep`클래스가 추상 클래스이 고 실제 단계는 [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)또는 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)와 같은 서브 클래스입니다. [Modulestep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) 클래스는 파이프라인 간에 공유할 수 있는 재사용 가능한 단계 시퀀스를 포함 합니다. 는 `Pipeline` 의 일부로 실행 됩니다 `Experiment` .

Azure ML 파이프라인은 Azure Machine Learning 작업 영역에 연결 되 고 파이프라인 단계는 해당 작업 영역 내에서 사용할 수 있는 계산 대상과 연결 됩니다. 자세한 내용은 [Azure Portal에서 Azure Machine Learning 작업 영역 만들기 및 관리](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 또는 [Azure Machine Learning에서 계산 대상 이란?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)를 참조 하세요.

### <a name="a-simple-python-pipeline"></a>간단한 Python 파이프라인

이 코드 조각은 기본를 만들고 실행 하는 데 필요한 개체 및 호출을 보여 줍니다 `Pipeline` .

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

코드 조각은 일반적인 Azure Machine Learning 개체, a, a, a `Workspace` `Datastore` [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)및으로 시작 `Experiment` 합니다. 그런 다음 코드는 및을 보유할 개체를 `input_data` 만듭니다 `output_data` . 배열은 `steps` `PythonScriptStep` 데이터 개체를 사용 하 고에서 실행 되는 단일 요소를 보유 합니다 `compute_target` . 그런 다음 코드는 `Pipeline` 작업 영역 및 단계 배열을 전달 하 여 개체 자체를 인스턴스화합니다. 에 대 한 호출은 `experiment.submit(pipeline)` AZURE ML 파이프라인 실행을 시작 합니다. 에 대 한 호출은 `wait_for_completion()` 파이프라인이 완료 될 때까지 차단 됩니다. 

파이프라인을 데이터에 연결 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning의 데이터 액세스](concept-data.md) 및 [ML 파이프라인 단계 간 데이터 이동 (Python)](how-to-move-data-in-out-of-pipelines.md)문서를 참조 하세요. 

## <a name="building-pipelines-with-the-designer"></a>디자이너를 사용 하 여 파이프라인 빌드

시각적 디자인 화면을 선호 하는 개발자는 Azure Machine Learning 디자이너를 사용 하 여 파이프라인을 만들 수 있습니다. 작업 영역 홈페이지의 **디자이너** 선택 영역에서이 도구에 액세스할 수 있습니다.  디자이너를 사용 하 여 디자인 화면으로 단계를 끌어서 놓을 수 있습니다. 

파이프라인을 시각적으로 디자인 하는 경우 단계의 입력 및 출력이 눈에 띄게 표시 됩니다. 데이터 연결을 끌어서 놓을 수 있으므로 파이프라인의 데이터 흐름을 빠르게 이해 하 고 수정할 수 있습니다.

![Azure Machine Learning 디자이너 예](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로에 파이프라인을 사용 하는 경우의 주요 이점은 다음과 같습니다.

|주요 장점|설명|
|:-------:|-----------|
|**무인&nbsp;실행**|안정적이 고 무인 방식으로 병렬로 실행 하거나 순서 대로 실행 하는 단계를 예약 합니다. 데이터 준비 및 모델링은 마지막 일 또는 몇 주, 그리고 파이프라인을 사용 하 여 프로세스가 실행 되는 동안 다른 작업에 집중할 수 있습니다. |
|**다른 유형의 계산**|다른 유형의 확장 가능한 계산 리소스 및 저장소 위치에서 안정적으로 조정 된 여러 파이프라인을 사용 합니다. HDInsight, GPU 데이터 과학 Vm 및 Databricks 같은 다양 한 계산 대상에서 개별 파이프라인 단계를 실행 하 여 사용 가능한 계산 리소스를 효율적으로 사용 합니다.|
|**재사용성**|재 학습 및 일괄 처리 점수 매기기와 같은 특정 시나리오에 대 한 파이프라인 템플릿을 만듭니다. 간단한 REST 호출을 통해 외부 시스템에서 게시 된 파이프라인을 트리거합니다.|
|**추적 및 버전 관리**|반복하면서 데이터 및 결과 경로를 수동으로 추적하는 대신, Pipelines SDK를 사용하여 데이터 원본, 입력 및 출력의 이름과 버전을 명시적으로 지정합니다. 생산성 향상을 위해 스크립트와 데이터를 별도로 관리할 수도 있습니다.|
| **성과** | 중요 한 영역을 분리 하 고 변경 내용을 격리 하면 소프트웨어 품질이 높은 속도로 향상 됩니다. | 
|**협업**|파이프라인을 통해 데이터 과학자는 기계 학습 디자인 프로세스의 모든 영역에서 공동 작업을 수행할 수 있으며 파이프라인 단계에서 동시에 작업할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

Azure ML 파이프라인은 초기 개발 단계에서 가치를 제공 하기 시작 하는 강력한 기능입니다. 이 값은 팀과 프로젝트가 증가 함에 따라 증가 합니다. 이 문서에서는 Azure에서 Azure Machine Learning Python SDK 및 오케스트레이션를 사용 하 여 파이프라인을 지정 하는 방법에 대해 설명 했습니다. 몇 가지 기본 소스 코드를 확인 하 여 사용할 수 있는 몇 가지 클래스를 소개 했습니다 `PipelineStep` . Azure ML 파이프라인을 사용 하는 경우와 Azure가이를 실행 하는 방법을 이해 해야 합니다. 


+ [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md)방법에 대해 알아봅니다.

+ [대량 데이터에서 일괄 처리 예측을 실행](tutorial-pipeline-batch-scoring-classification.md )하는 방법에 대해 알아봅니다.

+ [파이프라인 코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 및 [파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)는 SDK 참조 문서를 참조 하세요.

+ 예제 Jupyter 노트북 보여주는 [Azure Machine Learning 파이프라인](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)을 사용해 보세요. 노트북을 실행 하 여 [이 서비스를 탐색](samples-notebooks.md)하는 방법을 알아봅니다.
