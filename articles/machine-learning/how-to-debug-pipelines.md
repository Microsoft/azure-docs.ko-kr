---
title: ML 파이프라인의 디버그 & 문제 해결
titleSuffix: Azure Machine Learning
description: Python에서 Azure Machine Learning 파이프라인을 디버깅 합니다. 파이프라인 개발에 대 한 일반적인 문제 및 원격 실행 전후에 스크립트를 디버그 하는 데 도움이 되는 팁에 대해 알아봅니다. Visual Studio Code를 사용 하 여 기계 학습 파이프라인을 대화형으로 디버깅 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: 0f051e5b5711cec9fd8e72ec2b84c18f80430a0a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018062"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>기계 학습 파이프라인 디버그 및 문제 해결
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 및 [Azure Machine Learning designer (미리 보기)](https://docs.microsoft.com/azure/machine-learning/concept-designer)에서 [machine learning 파이프라인](concept-ml-pipelines.md) 의 문제를 해결 하 고 디버그 하는 방법에 대해 알아봅니다. 

## <a name="troubleshooting-tips"></a>문제 해결 팁

다음 표에는 잠재적인 솔루션을 포함 하는 파이프라인 개발 중에 일반적인 문제가 포함 되어 있습니다.

| 문제 | 가능한 해결 방법 |
|--|--|
| 디렉터리에 데이터를 전달할 수 없습니다. `PipelineData` | 파이프라인이 단계 출력 데이터를 기대 하는 위치에 해당 하는 디렉터리를 스크립트에 만들었는지 확인 합니다. 대부분의 경우 입력 인수는 출력 디렉터리를 정의 하 고 디렉터리를 명시적으로 만듭니다. `os.makedirs(args.output_dir, exist_ok=True)`를 사용 하 여 출력 디렉터리를 만듭니다. 이 디자인 패턴을 보여 주는 점수 매기기 스크립트 예제에 대 한 [자습서](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) 를 참조 하세요. |
| 종속성 버그 | 원격 파이프라인에서 로컬 테스트할 때 발생 하지 않은 종속성 오류가 표시 되 면 원격 환경 종속성 및 버전이 테스트 환경의 종속성과 일치 하는지 확인 합니다. [환경 빌드, 캐싱 및 재사용](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse) 을 참조 하세요.|
| 계산 대상의 모호한 오류 | 계산 대상을 삭제 하 고 다시 만들어 보세요. 계산 대상을 다시 만드는 과정은 빠르게 수행할 수 있으며 일부 일시적인 문제를 해결할 수 있습니다. |
| 파이프라인이 단계를 다시 사용 하지 않음 | 단계 다시 사용은 기본적으로 사용 되지만 파이프라인 단계에서 사용 하지 않도록 설정 하지 않았는지 확인 합니다. 다시 사용 하지 않도록 설정 된 경우 `allow_reuse` 단계의 매개 변수는로 설정 됩니다 `False` . |
| 파이프라인이 불필요 하 게 다시 실행 되 고 있습니다. | 기본 데이터 또는 스크립트가 변경 될 때만 단계가 다시 실행 되도록 하려면 각 단계에 대 한 소스 코드 디렉터리를 분리 합니다. 여러 단계에 동일한 원본 디렉터리를 사용 하는 경우 불필요 한 다시 실행이 발생할 수 있습니다. `source_directory`파이프라인 단계 개체에서 매개 변수를 사용 하 여 해당 단계에 대 한 격리 된 디렉터리를 가리키고 여러 단계에 대해 동일한 경로를 사용 하지 않는지 확인 `source_directory` 합니다. |


## <a name="debugging-techniques"></a>디버깅 기술

파이프라인 디버깅에는 세 가지 주요 기술이 있습니다. 

* 로컬 컴퓨터에서 개별 파이프라인 단계 디버그
* 로깅 및 Application Insights를 사용 하 여 문제의 원인을 격리 하 고 진단 합니다.
* Azure에서 실행 되는 파이프라인에 원격 디버거 연결

### <a name="debug-scripts-locally"></a>로컬에서 스크립트 디버그

파이프라인에서 가장 일반적으로 발생 하는 오류 중 하나는 도메인 스크립트가 의도 한 대로 실행 되지 않거나 디버깅 하기 어려운 원격 계산 컨텍스트에서 런타임 오류를 포함 하는 것입니다.

파이프라인 자체를 로컬로 실행할 수는 없지만 로컬 컴퓨터에서 격리 된 스크립트를 실행 하면 계산 및 환경 빌드 프로세스를 기다릴 필요가 없기 때문에 더 빠르게 디버그할 수 있습니다. 이 작업을 수행 하려면 몇 가지 개발 작업이 필요 합니다.

* 데이터가 클라우드 데이터 저장소에 있는 경우 데이터를 다운로드 하 여 스크립트에 사용할 수 있도록 해야 합니다. 데이터의 작은 샘플을 사용 하는 것은 런타임에 빠르게 중단 하 고 스크립트 동작에 대 한 피드백을 빠르게 얻는 좋은 방법입니다.
* 중간 파이프라인 단계를 시뮬레이션 하려는 경우 이전 단계에서 특정 스크립트가 예상 하는 개체 유형을 수동으로 빌드해야 할 수 있습니다.
* 또한 사용자 고유의 환경을 정의 하 고 원격 계산 환경에 정의 된 종속성을 복제 해야 합니다.

로컬 환경에서 스크립트를 설치 하 고 나면 다음과 같은 디버깅 작업을 수행 하는 것이 훨씬 쉽습니다.

* 사용자 지정 디버그 구성 연결
* 실행 일시 중지 및 개체 상태 검사
* 런타임이 될 때까지 노출 되지 않는 형식 또는 논리 오류를 catch 합니다.

> [!TIP] 
> 스크립트가 예상 대로 실행 되는지 확인 한 후에는 여러 단계로 이루어진 파이프라인에서 실행을 시도 하기 전에 단일 단계 파이프라인에서 스크립트를 실행 하는 것이 좋습니다.

## <a name="configure-write-to-and-review-pipeline-logs"></a>파이프라인 로그 구성, 쓰기 및 검토

스크립트를 로컬로 테스트 하는 것은 파이프라인 빌드를 시작 하기 전에 주요 코드 조각과 복잡 한 논리를 디버깅 하는 좋은 방법 이지만, 특정 시점에는 특히 파이프라인 단계 간의 상호 작용 중에 발생 하는 동작을 진단할 때 실제 파이프라인 실행 중에 스크립트를 디버그 해야 할 수 있습니다. `print()`JavaScript 코드를 디버그 하는 방법과 비슷하게 원격 실행 중에 개체 상태와 예상 값을 볼 수 있도록 단계 스크립트에서 문을 매우 사용 하는 것이 좋습니다.

### <a name="logging-options-and-behavior"></a>로깅 옵션 및 동작

다음 표에서는 파이프라인에 대 한 다양 한 디버그 옵션에 대 한 정보를 제공 합니다. 여기에 표시 된 Azure Machine Learning, Python 및 OpenCensus 뿐 아니라 다른 옵션도 존재 하므로 완전 한 목록은 아닙니다.

| 라이브러리                    | Type   | 예제                                                          | 대상                                  | 리소스                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | 메트릭 | `run.log(name, val)`                                             | Azure Machine Learning 포털 UI             | [실험을 추적 하는 방법](how-to-track-experiments.md)<br>[azureml 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Python 인쇄/로깅    | 로그    | `print(val)`<br>`logging.info(message)`                          | 드라이버 로그, Azure Machine Learning 디자이너 | [실험을 추적 하는 방법](how-to-track-experiments.md)<br><br>[Python 로깅](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | 로그    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights 추적                | [Application Insights에서 파이프라인 디버깅](how-to-debug-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python 로깅 cookbook](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>로깅 옵션 예제

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

### <a name="finding-and-reading-pipeline-log-files"></a>파이프라인 로그 파일 찾기 및 읽기

로그 파일에는 `70_driver_log.txt` 다음이 포함 됩니다. 

* 스크립트를 실행 하는 동안 모든 인쇄 된 문
* 스크립트에 대 한 스택 추적입니다. 

포털에서이 파일 및 기타 로그 파일을 찾으려면 먼저 작업 영역에서 파이프라인 실행을 클릭 합니다.

![파이프라인 실행 목록 페이지](./media/how-to-debug-pipelines/pipelinerun-01.png)

파이프라인 실행 세부 정보 페이지로 이동 합니다.

![파이프라인 실행 세부 정보 페이지](./media/how-to-debug-pipelines/pipelinerun-02.png)

특정 단계에 대 한 모듈을 클릭 합니다. **로그** 탭으로 이동 합니다. 기타 로그에는 환경 이미지 빌드 프로세스 및 단계 준비 스크립트에 대 한 정보가 포함 됩니다.

![파이프라인 실행 세부 정보 페이지 로그 탭](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> 게시 된 *파이프라인* 에 대 한 실행은 작업 영역의 **끝점** 탭에서 찾을 수 있습니다. *게시 되지 않은 파이프라인* 에 대 한 실행은 **실험** 또는 **파이프라인**에서 찾을 수 있습니다.

에서 로깅 및 추적 하는 방법에 대 한 자세한 내용은 `ParallelRunStep` [ParallelRunStep 디버그 및 문제 해결](how-to-debug-parallel-run-step.md)을 참조 하세요.

## <a name="logging-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning designer에서 로깅 (미리 보기)

디자이너에서 만든 파이프라인의 경우 제작 페이지나 파이프라인 실행 세부 정보 페이지에서 **70_driver_log** 파일을 찾을 수 있습니다.

### <a name="enable-logging-for-real-time-endpoints"></a>실시간 끝점에 대 한 로깅 사용

디자이너에서 실시간 끝점의 문제를 해결 하 고 디버그 하려면 SDK를 사용 하 여 응용 프로그램 정보 로깅을 사용 하도록 설정 해야 합니다. 로깅을 사용 하면 모델 배포 및 사용 문제를 해결 하 고 디버그할 수 있습니다. 자세한 내용은 [배포 된 모델에 대 한 로깅](how-to-enable-logging.md#logging-for-deployed-models)을 참조 하세요. 

### <a name="get-logs-from-the-authoring-page"></a>제작 페이지에서 로그 가져오기

파이프라인 실행을 제출 하 고 제작 페이지에 유지 하는 경우 각 모듈의 실행이 완료 되 면 각 모듈에 대해 생성 되는 로그 파일을 찾을 수 있습니다.

1. 작성 캔버스에서 실행이 완료 된 모듈을 선택 합니다.
1. 모듈의 오른쪽 창에서  **출력 + 로그** 탭으로 이동 합니다.
1. 오른쪽 창을 확장 하 고 **70_driver_log.txt** 를 선택 하 여 브라우저에서 파일을 봅니다. 로그를 로컬로 다운로드할 수도 있습니다.

    ![디자이너의 확장 된 출력 창](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>파이프라인 실행에서 로그 가져오기

파이프라인 실행 세부 정보 페이지에서 특정 실행에 대 한 로그 파일을 찾을 수도 있습니다 .이 페이지는 스튜디오의 **파이프라인** 또는 **실험** 섹션에서 찾을 수 있습니다.

1. 디자이너에서 만든 파이프라인 실행을 선택 합니다.

    ![파이프라인 실행 페이지](./media/how-to-debug-pipelines/designer-pipelines.png)

1. 미리 보기 창에서 모듈을 선택 합니다.
1. 모듈의 오른쪽 창에서  **출력 + 로그** 탭으로 이동 합니다.
1. 오른쪽 창을 확장 하 여 브라우저에서 **70_driver_log.txt** 파일을 보거나 파일을 선택 하 여 로그를 로컬로 다운로드 합니다.

> [!IMPORTANT]
> 파이프라인 실행 정보 페이지에서 파이프라인을 업데이트 하려면 파이프라인 실행을 새 파이프라인 초안으로 **복제** 해야 합니다. 파이프라인 실행은 파이프라인의 스냅숏입니다. 로그 파일과 비슷하며 변경할 수 없습니다. 

## <a name="application-insights"></a>Application Insights
이러한 방식으로 OpenCensus Python 라이브러리를 사용 하는 방법에 대 한 자세한 내용은이 가이드: [Application Insights machine learning 파이프라인 디버그 및 문제 해결](how-to-debug-pipelines-application-insights.md) 을 참조 하세요.

## <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code를 사용한 대화형 디버깅

ML 파이프라인에서 사용 되는 Python 코드를 대화형으로 디버깅 해야 하는 경우도 있습니다. Visual Studio Code (VS Code) 및 debugpy를 사용 하 여 학습 환경에서 실행 되는 코드에 연결할 수 있습니다. 자세한 내용은 [VS Code의 대화형 디버깅 가이드](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azureml-파이프라인-코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 패키지 및 [azureml 파이프라인 단계](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 패키지에 대 한 도움말은 SDK 참조를 참조 하세요.

* [디자이너 예외 및 오류 코드](algorithm-module-reference/designer-error-codes.md)의 목록을 참조 하세요.
