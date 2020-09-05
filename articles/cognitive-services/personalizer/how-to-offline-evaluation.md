---
title: 오프 라인 평가를 수행 하는 방법-Personalizer
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 오프 라인 평가를 사용 하 여 앱의 효율성을 측정 하 고 학습 루프를 분석 하는 방법을 보여 줍니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244842"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>오프 라인 평가를 사용 하 여 학습 루프 분석

오프라인 평가를 완료하고 결과를 이해하는 방법을 알아봅니다.

오프 라인 평가를 사용 하면 효과적인 Personalizer를 응용 프로그램의 기본 동작과 비교 하 고, 개인 설정에 가장 적합 한 기능을 알아보고, 새 machine learning 값을 자동으로 검색할 수 있습니다.

자세한 내용은 [오프라인 평가](concepts-offline-evaluation.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 구성 된 Personalizer 루프
* Personalizer 루프에는 대표적인 양의 데이터가 있어야 합니다. 개략적인는 의미 있는 평가 결과를 위해 로그에 5만 개 이상의 이벤트를 권장 합니다. 필요에 따라 이전에 내보낸 _학습 정책_ 파일을 동일한 평가에서 비교하고 테스트할 수 있습니다.

## <a name="run-an-offline-evaluation"></a>오프라인 평가 실행

1. [Azure Portal](https://azure.microsoft.com/free/cognitive-services)에서 Personalizer 리소스를 찾습니다.
1. Azure Portal에서 평가 섹션으로 이동 하 고 **평가 만들기**를 **선택 합니다.**
    ![Azure Portal에서 * * 평가 * * 섹션으로 이동 하 고 * * 평가 만들기 * *를 선택 합니다.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 다음 값을 구성 합니다.

    * 평가 이름입니다.
    * 시작 및 종료 날짜-계산에 사용할 데이터 범위를 지정 하는 날짜입니다. 이 데이터는 [데이터 보존](how-to-settings.md) 값에 지정 된 대로 로그에 표시 되어야 합니다.
    * 최적화 검색을 **예**로 설정 합니다.

    > [!div class="mx-imgBorder"]
    > ![오프 라인 평가 설정 선택](./media/offline-evaluation/create-an-evaluation-form.png)

1. **확인**을 선택 하 여 평가를 시작 합니다.

## <a name="review-the-evaluation-results"></a>평가 결과 검토

처리할 데이터의 양, 비교할 학습 정책의 수, 최적화 요청 여부에 따라 평가가 오래 걸릴 수 있습니다.

완료 되 면 평가 목록에서 평가를 선택한 다음 **응용 프로그램의 점수를 다른 잠재적 학습 설정과 비교**를 선택할 수 있습니다. 현재 학습 정책이 새 정책과 비교 하 여 수행 되는 방식을 확인 하려면이 기능을 선택 합니다.

1. [학습 정책의](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)성능을 검토 합니다.

    > [!div class="mx-imgBorder"]
    > [![평가 결과 검토](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. **적용** 을 선택 하 여 데이터에 가장 적합 한 모델을 향상 시키는 정책을 적용 합니다.

## <a name="next-steps"></a>다음 단계

* [오프 라인 평가의 작동 방식](concepts-offline-evaluation.md)에 대해 자세히 알아보세요.
