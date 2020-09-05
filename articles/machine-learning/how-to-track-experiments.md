---
title: Log ML 실험 & 메트릭
titleSuffix: Azure Machine Learning
description: Azure ML 실험과 실행 메트릭을 모니터링하여 모델 생성 프로세스를 향상시킵니다. 실행 .log, start_logging 또는 ScriptRunConfig를 사용 하 여 학습 스크립트에 로깅을 추가 합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 18c7e5b3a1401540d7a94186fda647d413d562c0
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723846"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Azure ML 학습 실행에서 로깅 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK를 사용 하면 기본 Python 로깅 패키지와 SDK 관련 기능을 모두 사용 하 여 실시간 정보를 기록할 수 있습니다. 로컬에 로그온 하 고 포털에서 작업 영역으로 로그를 보낼 수 있습니다.

로그를 통해 오류 및 경고를 진단 하거나 매개 변수 및 모델 성능과 같은 성능 메트릭을 추적할 수 있습니다. 이 문서에서는 다음 시나리오에서 로깅을 사용 하도록 설정 하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 대화형 학습 세션
> * ScriptRunConfig를 사용 하 여 학습 작업 제출
> * Python 기본 `logging` 설정
> * 추가 원본에서 로깅


> [!TIP]
> 이 문서에서는 모델 학습 프로세스를 모니터링 하는 방법을 보여 줍니다. 할당량, 완료 된 학습 실행 또는 완료 된 모델 배포와 같이 Azure Machine learning의 리소스 사용 및 이벤트를 모니터링 하는 데 관심이 있는 경우 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조 하세요.

## <a name="data-types"></a>데이터 형식

스칼라 값, 목록, 테이블, 이미지, 디렉터리 등을 비롯 한 여러 데이터 형식에 기록할 수 있습니다. 다른 데이터 형식에 대 한 자세한 내용 및 Python 코드 예제는 [클래스 참조 실행 페이지](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)를 참조 하세요.

## <a name="interactive-logging-session"></a>대화형 로깅 세션

대화형 로깅 세션은 일반적으로 노트북 환경에서 사용 됩니다. [Start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) 메서드는 대화형 로깅 세션을 시작 합니다. 세션 중에 기록 된 모든 메트릭은 실험에서 실행 레코드에 추가 됩니다. [Run. complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) 메서드는 세션을 종료 하 고 실행을 완료 된 것으로 표시 합니다.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig 로그

이 섹션에서는 ScriptConfig 실행 내의 로깅 코드를 추가 하는 방법에 대해 알아봅니다. [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) 클래스를 사용 하 여 반복 실행을 위한 스크립트 및 환경을 캡슐화 할 수 있습니다. 이 옵션을 사용 하 여 모니터링을 위한 시각적 Jupyter 노트북 위젯을 표시할 수도 있습니다.

이 예에서는 알파 값에 대 한 매개 변수 스윕를 수행 하 고 [run .log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) 메서드를 사용 하 여 결과를 캡처합니다.

1. 로깅 논리를 포함 하는 학습 스크립트를 만듭니다 `train.py` .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. ```train.py```사용자 관리 환경에서 실행할 스크립트를 제출 합니다. 전체 스크립트 폴더는 학습을 위해 제출 됩니다.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output`매개 변수는 자세한 정보 로깅을 설정 하 여 교육 프로세스의 세부 정보 뿐만 아니라 원격 리소스 또는 계산 대상에 대 한 정보를 볼 수 있습니다. 실험을 제출할 때 자세한 정보 로깅을 설정 하려면 다음 코드를 사용 합니다.

```python
run = exp.submit(src, show_output=True)
```

결과 실행의 `wait_for_completion` 함수에서도 동일한 매개 변수를 사용할 수 있습니다.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>네이티브 Python 로깅

SDK의 일부 로그에는 디버그할 로깅 수준을 설정 하도록 지시 하는 오류가 포함 되어 있을 수 있습니다. 로깅 수준을 설정하려면 스크립트에 다음 코드를 추가합니다.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>추가 로깅 원본

자동화 된 Machine Learning 실행 또는 작업을 실행 하는 Docker 컨테이너와 같은 학습 중 다른 원본의 정보를 기록할 수도 Azure Machine Learning. 이러한 로그는 문서화 되어 있지 않지만 문제가 발생 하 고 Microsoft 지원에 문의 하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.

Azure Machine Learning designer (미리 보기)의 로깅 메트릭에 대 한 자세한 내용은 [디자이너에서 메트릭을 기록 하는 방법 (미리 보기)](how-to-track-designer-experiments.md) 을 참조 하세요.

## <a name="example-notebooks"></a>노트북 예제

이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 사용 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure Machine Learning designer (미리 보기)에서 메트릭을 기록](how-to-track-designer-experiments.md)하는 방법에 대해 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.
