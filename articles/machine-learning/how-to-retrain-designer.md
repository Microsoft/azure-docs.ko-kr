---
title: Azure Machine Learning designer를 사용 하 여 모델 다시 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning designer에서 게시 된 파이프라인으로 모델을 다시 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 12/15/2019
ms.openlocfilehash: d1382da739fd8ca56d4cc53c2c302331bdfbf1c3
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311872"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Azure Machine Learning designer를 사용 하 여 모델 다시 학습
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 방법에서는 Azure Machine Learning 디자이너를 사용 하 여 Machine Learning 모델을 다시 학습 하는 방법에 대해 알아봅니다. 게시 된 파이프라인을 사용 하 여 재 학습을 위한 기계 학습 워크플로를 자동화 하는 방법을 알아보세요.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 기계 학습 모델 학습
> * 파이프라인 매개 변수를 만듭니다.
> * 학습 파이프라인을 게시 합니다.
> * 모델을 다시 학습 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정](https://aka.ms/AMLFree)을 만듭니다.

* Enterprise SKU를 사용 하는 Azure Machine Learning 작업 영역입니다.

이 방법에서는 디자이너에서 파이프라인을 빌드하는 방법에 대 한 기본 지식이 있다고 가정 합니다. 디자이너에 대한 단계별 소개를 보려면 [자습서](tutorial-designer-automobile-price-train-score.md)를 완료하세요. 

### <a name="sample-pipeline"></a>샘플 파이프라인

이 문서에 사용 된 파이프라인은 [샘플 3 수입 예측](how-to-designer-sample-classification-predict-income.md)에 있는 변경 된 버전입니다. 사용자 고유의 데이터를 사용 하 여 모델을 학습 하는 방법을 보여 주기 위해 샘플 데이터 집합 대신 [데이터 가져오기](algorithm-module-reference/import-data.md) 모듈을 사용 합니다.

![데이터 가져오기 모듈을 강조 표시 하는 상자를 사용 하 여 수정 된 샘플 파이프라인을 보여 주는 스크린샷](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>기계 학습 모델 학습

모델을 다시 학습 하려면 초기 모델이 필요 합니다. 이 섹션에서는 디자이너를 사용 하 여 모델을 학습 하 고 저장 된 모델에 액세스 하는 방법에 대해 알아봅니다.

1. **데이터 가져오기** 모듈을 선택 합니다.
1. 속성 창에서 데이터 원본을 지정 합니다.

    ![데이터 가져오기 모듈의 샘플 구성을 보여 주는 스크린샷](./media/how-to-retrain-designer/import-data-settings.png)

    이 예제에서 데이터는 [Azure 데이터 저장소](how-to-access-data.md)에 저장 됩니다. 데이터 저장소가 아직 없는 경우 **새 데이터 저장소**를 선택 하 여 지금 만들 수 있습니다.

1. 데이터의 경로를 지정 합니다. 또한 **찾아보기 경로** 를 선택 하 여 데이터 저장소를 시각적으로 탐색할 수 있습니다. 

1. 캔버스 위쪽에서 **실행** 을 선택 합니다.
    
    > [!NOTE]
    > 이 파이프라인 초안의 기본 계산을 이미 설정한 경우 파이프라인이 자동으로 실행 됩니다. 그렇지 않으면 설정 창에서 표시 되는 프롬프트에 따라 지금 설정 합니다.

### <a name="locate-your-trained-model"></a>학습 된 모델 찾기

디자이너가 학습 된 모델을 비롯 한 모든 파이프라인 출력을 기본 저장소 계정에 저장 합니다. 디자이너에서 직접 학습 된 모델에 액세스할 수도 있습니다.

1. 파이프라인이 실행을 마칠 때까지 기다립니다.

1. **모델 학습** 모듈을 선택합니다.

1. 설정 창에서 **출력**을 선택 합니다.

1. 모델을 다운로드 하려면 **Trained_model** 을 선택 합니다.

![학습 된 모델을 다운로드 하는 방법을 보여 주는 스크린샷](./media/how-to-retrain-designer/download-model.png)

## <a name="create-a-pipeline-parameter"></a>파이프라인 매개 변수 만들기

파이프라인 매개 변수를 추가 하 여 런타임에 변수를 동적으로 설정 합니다. 이 파이프라인의 경우 새 데이터 집합에서 모델을 다시 학습 수 있도록 학습 데이터 경로에 대 한 매개 변수를 추가 합니다.

1. **데이터 가져오기** 모듈을 선택 합니다.
1. 설정 창에서 **경로** 필드 위에 있는 줄임표를 선택 합니다.
1. **파이프라인 매개 변수에 추가를**선택 합니다.
1. 매개 변수 이름 및 기본값을 제공 합니다.

    > [!NOTE]
    > 파이프라인 초안의 제목 옆에 있는 **설정 기어 아이콘** 을 선택 하 여 파이프라인 매개 변수를 검사 하 고 편집할 수 있습니다. 

![파이프라인 매개 변수를 만드는 방법을 보여 주는 스크린샷](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>학습 파이프라인 게시

파이프라인을 게시 하면 파이프라인 엔드포인트이 만들어집니다. 파이프라인 엔드포인트을 사용 하 여 반복성 및 자동화를 위해 파이프라인을 다시 사용 하 고 관리할 수 있습니다. 이 예제에서는 재 학습을 위한 파이프라인을 설정 했습니다.

1. 디자이너 캔버스 위에서 **게시** 를 선택 합니다.
1. 새 파이프라인 엔드포인트을 선택 하거나 만듭니다.

    > [!NOTE]
    > 단일 엔드포인트에 여러 파이프라인을 게시할 수 있습니다. 엔드포인트의 각 파이프라인에는 파이프라인 엔드포인트을 호출할 때 지정할 수 있는 버전 번호가 제공 됩니다.

1. **게시**를 선택합니다.

## <a name="retrain-your-model"></a>모델 다시 학습시키기

이제 게시 된 학습 파이프라인이 있으므로이를 사용 하 여 새 데이터를 사용 하 여 모델을 다시 학습 수 있습니다. 포털에서 또는 프로그래밍 방식으로 파이프라인 엔드포인트에서 실행을 제출할 수 있습니다.

### <a name="submit-runs-with-the-designer"></a>디자이너를 사용 하 여 실행 제출

다음 단계를 사용 하 여 디자이너에서 실행 되는 파이프라인 엔드포인트을 제출 합니다.

1. **엔드포인트** 페이지로 이동 합니다.

1. **파이프라인 엔드포인트** 탭을 선택 합니다.

1. 파이프라인 엔드포인트을 선택 합니다.

1. 게시 된 **파이프라인** 탭을 선택 합니다.

1. 실행 하려는 파이프라인을 선택 합니다.

1. **실행**을 선택합니다.

1. 설치 대화 상자에서 새 데이터 집합을 가리키는 새 입력 데이터 경로 값을 지정할 수 있습니다.

![디자이너에서 매개 변수가 있는 파이프라인 실행을 설정 하는 방법을 보여 주는 스크린샷](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>코드를 사용 하 여 실행 제출

개발 환경에 따라 프로그래밍 방식으로 REST 엔드포인트에 액세스 하는 방법에는 여러 가지가 있습니다. 파이프라인의 **사용** 탭에서 매개 변수를 사용 하 여 파이프라인 실행을 제출 하는 방법을 보여 주는 코드 샘플을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

디자이너 [자습서](tutorial-designer-automobile-price-train-score.md)에 따라 회귀 모델을 학습하고 배포합니다.
