---
title: ML 파이프라인 게시
titleSuffix: Azure Machine Learning
description: 기계 학습 파이프라인과 Python 용 Azure Machine Learning SDK를 사용 하 여 기계 학습 워크플로를 실행 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 4515eda6723a9a347a7a0aa56187f40ed4269cdc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88942152"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Machine learning 파이프라인 게시 및 추적

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 동료 또는 고객과 machine learning 파이프라인을 공유 하는 방법을 보여 줍니다.

Machine learning 파이프라인은 기계 학습 작업에 다시 사용할 수 있는 워크플로입니다. 파이프라인의 장점 중 하나는 공동 작업입니다. 또한 새 버전에서 작업 하는 동안 고객이 현재 모델을 사용할 수 있도록 파이프라인의 버전을 지정할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 모든 파이프라인 리소스를 저장할 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md) 을 만듭니다.

* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 를 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

* 다음 [자습서: 일괄 처리 점수 매기기를 위한 Azure Machine Learning 파이프라인 빌드](tutorial-pipeline-batch-scoring-classification.md)와 같은 기계 학습 파이프라인을 만들고 실행 합니다. 기타 옵션은 [AZURE MACHINE LEARNING SDK를 사용 하 여 machine learning 파이프라인 만들기 및 실행](how-to-create-your-first-pipeline.md) 을 참조 하세요.

## <a name="publish-a-pipeline"></a>파이프라인 게시

파이프라인을 실행 한 후에는 다른 입력으로 실행 되도록 파이프라인을 게시할 수 있습니다. 매개 변수를 허용 하기 위해 이미 게시 된 파이프라인의 REST 끝점의 경우에는 `PipelineParameter` 다른 인수에 대해 개체를 사용 하도록 파이프라인을 구성 해야 합니다.

1. 파이프라인 매개 변수를 만들려면 기본값으로 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 개체를 사용합니다.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 다음과 같이 이 `PipelineParameter` 개체를 파이프라인의 단계에 대한 매개 변수로 추가합니다.

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 이 파이프라인을 게시하면 호출 시 매개 변수를 허용합니다.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>게시된 파이프라인 실행

게시된 모든 파이프라인에는 REST 엔드포인트가 있습니다. 파이프라인 끝점을 사용 하 여 Python이 아닌 클라이언트를 비롯 한 모든 외부 시스템에서 파이프라인 실행을 트리거할 수 있습니다. 이 엔드포인트는 일괄 처리 채점 및 다시 학습 시나리오에서 “관리되는 반복 가능성”을 지원합니다.

이전 파이프라인의 실행을 호출 하려면 Azure Active Directory 인증 헤더 토큰이 필요 합니다. 이러한 토큰을 가져오는 방법은 [Azurecliauthentication 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) 참조 및 [Azure Machine Learning 노트북의 인증](https://aka.ms/pl-restep-auth) 에 설명 되어 있습니다.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>버전 지정 파이프라인 끝점 만들기

게시 된 여러 파이프라인이 있는 파이프라인 끝점을 만들 수 있습니다. 그러면 ML 파이프라인을 반복 하 고 업데이트할 때 고정 REST 끝점을 제공 합니다.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>파이프라인 끝점에 작업 제출

파이프라인 끝점의 기본 버전에 작업을 제출할 수 있습니다.

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

특정 버전으로 작업을 제출할 수도 있습니다.

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

REST API를 사용 하 여 동일한 작업을 수행할 수 있습니다.

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>스튜디오에서 게시 된 파이프라인 사용

또한 스튜디오에서 게시 된 파이프라인을 실행할 수 있습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. [작업 영역을 봅니다](how-to-manage-workspace.md#view).

1. 왼쪽에서 **끝점**을 선택 합니다.

1. 위쪽에서 **파이프라인 끝점**을 선택 합니다.
 ![machine learning 게시 된 파이프라인 목록](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 파이프라인 끝점의 이전 실행 결과를 실행, 사용 또는 검토 하려면 특정 파이프라인을 선택 합니다.

## <a name="disable-a-published-pipeline"></a>게시 된 파이프라인 사용 안 함

게시 된 파이프라인 목록에서 파이프라인을 숨기려면 스튜디오 또는 SDK에서 파이프라인을 사용 하지 않도록 설정 합니다.

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

에서 다시 사용 하도록 설정할 수 있습니다 `p.enable()` . 자세한 내용은 [PublishedPipeline 클래스](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) 참조를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [GitHub의 Jupyter 노트북](https://aka.ms/aml-pipeline-readme)을 사용하여 기계 학습 파이프라인을 추가로 살펴봅니다.
- [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 azureml- [파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 패키지에 대 한 SDK 참조 도움말을 참조 하세요.
- 파이프라인 디버깅 및 문제 해결에 대 한 팁은 [방법을](how-to-debug-pipelines.md) 참조 하세요.
