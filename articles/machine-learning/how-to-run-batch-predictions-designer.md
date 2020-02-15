---
title: Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행(미리 보기)
titleSuffix: Azure Machine Learning
description: 디자이너를 사용하여 모델을 학습하고 일괄 처리 예측 파이프라인을 설정하는 방법에 대해 알아봅니다. 파이프라인을 매개 변수가 있는 웹 서비스로 배포합니다. 그러면 HTTP 라이브러리에서 트리거될 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 01/13/2020
ms.custom: Ignite2019
ms.openlocfilehash: 95a3c1b892cacd802f359fdc03de74fa60a1e118
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138100"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너를 사용하여 일괄 처리 예측 실행
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 디자이너를 사용 하 여 일괄 처리 예측 파이프라인을 만드는 방법에 대해 알아봅니다. 일괄 처리 예측을 사용 하면 모든 HTTP 라이브러리에서 트리거할 수 있는 웹 서비스를 사용 하 여 요청 시 대량 데이터 집합의 점수를 지속적으로 지정할 수 있습니다.

이 방법에서는 다음 작업을 수행 하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 일괄 처리 유추 파이프라인 만들기 및 게시
> * 파이프라인 끝점 사용
> * 끝점 버전 관리

SDK를 사용 하 여 일괄 처리 점수 매기기 서비스를 설정 하는 방법에 대 한 자세한 내용은 함께 제공 되는 [방법](how-to-run-batch-predictions.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 방법에서는 학습 파이프라인이 이미 있다고 가정 합니다. 디자이너에 대 한 단계별 소개는 [디자이너 자습서의 1 부](tutorial-designer-automobile-price-train-score.md)를 완료 합니다. 

## <a name="create-a-batch-inference-pipeline"></a>일괄 처리 유추 파이프라인 만들기

추론 파이프라인을 만들려면 최소한 한 번 이상 학습 파이프라인이 실행 되어야 합니다.

1. 작업 영역의 **디자이너** 탭으로 이동 합니다.

1. 예측을 만드는 데 사용 하려는 모델을 학습 하는 학습 파이프라인을 선택 합니다.

1. 파이프라인을 **실행** 합니다.

    ![파이프라인 실행](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

이제 학습 파이프라인이 실행 되었으므로 일괄 처리 유추 파이프라인을 만들 수 있습니다.

1. **실행**옆에서 새로 만들기 드롭다운 **유추 파이프라인**을 선택 합니다.

1. **일괄 처리 유추 파이프라인**을 선택 합니다.

    ![일괄 처리 유추 파이프라인 만들기](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
그러면 기본 일괄 처리 유추 파이프라인이 생성됩니다. 

### <a name="add-a-pipeline-parameter"></a>파이프라인 매개 변수 추가

새 데이터에 대 한 예측을 만들려면이 파이프라인 초안 보기에서 다른 데이터 집합을 수동으로 연결 하거나 데이터 집합에 대 한 매개 변수를 만들 수 있습니다. 매개 변수를 사용 하면 런타임에 batch 추론 프로세스의 동작을 변경할 수 있습니다.

이 섹션에서는 예측을 만들 다른 데이터 집합을 지정 하는 데이터 집합 매개 변수를 만듭니다.

1. 데이터 집합 모듈을 선택 합니다.

1. 캔버스 오른쪽에 창이 표시 됩니다. 창 아래쪽에서 **파이프라인 매개 변수로 설정**을 선택 합니다.
   
    매개 변수의 이름을 입력 하거나 기본값을 적용 합니다.

## <a name="publish-your-batch-inferencing-pipeline"></a>Batch 추론 파이프라인 게시

이제 추론 파이프라인을 배포할 준비가 되었습니다. 파이프라인을 배포 하 고 다른 사용자가 사용할 수 있도록 합니다.

1. **게시** 단추를 선택합니다.

1. 표시 되는 대화 상자에서 **PipelineEndpoint**에 대 한 드롭다운을 확장 하 고 **New PipelineEndpoint**를 선택 합니다.

1. 끝점 이름과 설명 (선택 사항)을 제공 합니다.

    대화 상자 아래쪽에서 학습 중에 사용 되는 데이터 집합 ID의 기본값을 사용 하 여 구성 된 매개 변수를 볼 수 있습니다.

1. **게시**를 선택합니다.

![파이프라인 게시](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>끝점 사용

이제 데이터 집합 매개 변수를 사용 하는 게시 된 파이프라인이 있습니다. 파이프라인은 학습 파이프라인에서 만든 학습 된 모델을 사용 하 여 매개 변수로 제공 하는 데이터 집합의 점수를 계산 합니다.

### <a name="submit-a-pipeline-run"></a>파이프라인 실행 제출 

이 섹션에서는 수동 파이프라인 실행을 설정 하 고 파이프라인 매개 변수를 변경 하 여 새 데이터의 점수를 표시 합니다. 

1. 배포가 완료 되 면 **끝점** 섹션으로 이동 합니다.

1. **파이프라인 끝점**을 선택 합니다.

1. 만든 끝점의 이름을 선택 합니다.

![엔드포인트 링크](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. **게시 된 파이프라인**을 선택 합니다.

    이 화면에는이 끝점에 게시 된 모든 게시 된 파이프라인이 표시 됩니다.

1. 게시 한 파이프라인을 선택 합니다.

    파이프라인 세부 정보 페이지에는 파이프라인에 대 한 자세한 실행 기록 및 연결 문자열 정보가 표시 됩니다. 
    
1. **실행** 을 선택 하 여 파이프라인의 수동 실행을 만듭니다.

    ![파이프라인 세부 정보](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 다른 데이터 집합을 사용 하도록 매개 변수를 변경 합니다.
    
1. **실행** 을 선택 하 여 파이프라인을 실행 합니다.

### <a name="use-the-rest-endpoint"></a>REST 끝점 사용

파이프라인 끝점 및 게시 된 파이프라인을 사용 하는 방법에 대 한 정보는 **끝점** 섹션에서 찾을 수 있습니다.

파이프라인 끝점의 REST 끝점은 실행 개요 패널에서 찾을 수 있습니다. 끝점을 호출 하 여 기본 게시 된 파이프라인을 사용 합니다.

**게시 된 파이프라인 페이지에서** 게시 된 파이프라인을 사용할 수도 있습니다. 게시 된 파이프라인을 선택 하 고 해당 파이프라인의 REST 끝점을 찾습니다. 

![Rest 끝점 세부 정보](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

REST 호출을 수행 하려면 OAuth 2.0 전달자 유형 인증 헤더가 필요 합니다. 작업 영역에 대한 인증을 설정하고 매개 변수가 있는 REST 호출을 수행하는 방법에 대한 자세한 내용은 다음 [자습서 섹션](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)을 참조하세요.

## <a name="versioning-endpoints"></a>끝점 버전 관리

디자이너는 끝점에 게시 하는 각 후속 파이프라인에 버전을 할당 합니다. REST 호출에서 매개 변수로 실행 하려는 파이프라인 버전을 지정할 수 있습니다. 버전 번호를 지정 하지 않으면 디자이너에서 기본 파이프라인을 사용 합니다.

파이프라인을 게시할 때 해당 파이프라인을 해당 끝점에 대 한 새 기본 파이프라인으로 만들도록 선택할 수 있습니다.

![기본 파이프라인 설정](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

끝점의 **게시 된 파이프라인** 탭에서 새 기본 파이프라인을 설정할 수도 있습니다.

![기본 파이프라인 설정](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>다음 단계

디자이너 [자습서](tutorial-designer-automobile-price-train-score.md)에 따라 회귀 모델을 학습하고 배포합니다.
