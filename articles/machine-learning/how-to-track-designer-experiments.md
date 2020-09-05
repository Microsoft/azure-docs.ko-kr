---
title: 디자이너의 로그 메트릭 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure ML 디자이너 실험을 모니터링 합니다. Python 스크립트 실행 모듈을 사용 하 여 로깅을 사용 하도록 설정 하 고 스튜디오에서 기록 된 결과를 확인 합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 20845a6f1238095b40c9b05b5f5d8d85217b6db5
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950411"
---
# <a name="enable-logging-in-azure-machine-learning-designer-preview-pipelines"></a>Azure Machine Learning designer (미리 보기) 파이프라인에서 로깅 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 디자이너 파이프라인에 로깅 코드를 추가 하는 방법에 대해 알아봅니다. 또한 Azure Machine Learning studio 웹 포털을 사용 하 여 이러한 로그를 보는 방법을 알아봅니다.

SDK 제작 환경을 사용 하 여 메트릭을 로깅하는 방법에 대 한 자세한 내용은 [AZURE ML 실험 실행 및 메트릭 모니터링](how-to-track-experiments.md)을 참조 하세요.

## <a name="enable-logging-with-execute-python-script"></a>Python 스크립트 실행을 사용 하 여 로깅 사용

__Python 스크립트 실행__ 모듈을 사용 하 여 디자이너 파이프라인에서 로깅을 사용 하도록 설정 합니다. 이 워크플로를 사용 하 여 모든 값을 기록할 수는 있지만 __모델 평가__ 모듈의 메트릭을 기록 하 여 실행에서 모델 성능을 추적 하는 것이 특히 유용 합니다.

다음 예에서는 모델 평가 및 Python 스크립트 실행 모듈을 사용 하 여 학습 된 두 모델의 평균 제곱 오차를 기록 하는 방법을 보여 줍니다.

1. __모델 평가__ 모듈의 출력에 __Python 스크립트 실행__ 모듈을 연결 합니다.

    ![Python 스크립트 실행 모듈을 모델 평가 모듈에 연결](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 다음 코드를 __Python 스크립트 실행__ 코드 편집기에 붙여넣어 학습 된 모델에 대 한 절대 평균 오차를 기록 합니다. 유사한 패턴을 사용 하 여 디자이너에서 다른 값을 기록할 수 있습니다.

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
이 코드는 Azure Machine Learning Python SDK를 사용 하 여 값을 기록 합니다. Get_context ()를 사용 하 여 현재 실행의 컨텍스트를 가져옵니다. 그런 다음, run. parent. log () 메서드를 사용 하 여 해당 컨텍스트에 값을 기록 합니다. 를 사용 `parent` 하 여 모듈 실행이 아니라 부모 파이프라인 실행에 값을 기록 합니다.

Python SDK를 사용 하 여 값을 기록 하는 방법에 대 한 자세한 내용은 [AZURE ML 학습 실행에서 로깅 사용](how-to-track-experiments.md)을 참조 하세요.

## <a name="view-logs"></a>로그 보기

파이프라인 실행이 완료 되 면 실험 페이지에서 *Mean_Absolute_Error* 를 확인할 수 있습니다.

1. **실험** 섹션으로 이동 합니다.
1. 실험을 선택 합니다.
1. 보려는 실험에서 실행을 선택 합니다.
1. **메트릭**을 선택합니다.

    ![스튜디오에서 실행 메트릭 보기](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 디자이너에서 로그를 사용 하는 방법을 알아보았습니다. 다음 단계는 다음 관련 문서를 참조 하세요.

* 디자이너 파이프라인 문제를 해결 하는 방법에 대해 알아봅니다. [ML 파이프라인 문제 해결 디버그 &](how-to-debug-pipelines.md#logging-in-azure-machine-learning-designer-preview)를 참조 하세요.
* Python SDK를 사용 하 여 SDK 제작 환경에서 메트릭을 기록 하는 방법에 대해 알아봅니다. [AZURE ML 학습 실행에서 로깅 사용](how-to-track-experiments.md)을 참조 하세요.
