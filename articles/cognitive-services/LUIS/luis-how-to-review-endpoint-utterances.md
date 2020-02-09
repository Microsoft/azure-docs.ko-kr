---
title: 사용자 길이 발언 검토-LUIS
titleSuffix: Azure Cognitive Services
description: 활성 학습에서 캡처한 길이 발언을 검토 하 여 의도를 선택 하 고 엔터티를 읽기 전용 길이 발언로 표시 합니다. 변경 내용 적용, 학습 및 게시
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775145"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>엔드포인트 길이 발언을 검토 하 여 LUIS 앱을 개선 하는 방법

올바른 예측에 대 한 엔드포인트 길이 발언를 검토 하는 프로세스를 [활성 학습](luis-concept-review-endpoint-utterances.md)이라고 합니다. 활성 학습은 엔드포인트 쿼리를 캡처하고 사용자의 엔드포인트 길이 발언를 선택 합니다. 이러한 길이 발언를 검토 하 여 이러한 읽기 전용 길이 발언에 대 한 의도 및 표시 엔터티를 선택 합니다. 예제에 이러한 변경 내용을 적용 한 다음 학습 및 게시 길이 발언. LUIS는 길이 발언를 보다 정확 하 게 식별 합니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>활성 학습 사용

활성 학습을 사용 하도록 설정 하려면 사용자 쿼리를 기록해 야 합니다. 이는 `log=true` querystring 매개 변수 및 값을 사용 하 여 [엔드포인트 쿼리](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) 를 호출 하 여 수행 됩니다.

LUIS 포털을 사용 하 여 올바른 엔드포인트 쿼리를 생성 합니다.

1. [PREVIEW LUIS 포털](https://preview.luis.ai/)의 앱 목록에서 앱을 선택 합니다.
1. **관리** 섹션으로 이동한 다음 **Azure 리소스**를 선택 합니다.
1. 할당 된 예측 리소스의 경우 **쿼리 매개 변수 변경**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용 하 여 활성 학습에 필요한 로그를 저장 합니다.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. **로그 저장** 을 전환 하 고 **완료**를 선택 하 여 저장 합니다.

    > [!div class="mx-imgBorder"]
    > ![LUIS 포털을 사용 하 여 활성 학습에 필요한 로그를 저장 합니다.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     이 작업은 `log=true` querystring 매개 변수를 추가 하 여 URL의 예를 변경 합니다. 런타임 엔드포인트에 대 한 예측 쿼리를 만들 때 변경 된 예제 쿼리 URL을 복사 하 여 사용 합니다.

## <a name="correct-intent-predictions-to-align-utterances"></a>길이 발언를 맞추기 위한 올바른 의도 예측

각 발화에는 **맞춤 의도** 열에 표시되는 제안된 의도가 포함됩니다.

> [!div class="mx-imgBorder"]
> [LUIS가 확실 하지 않은 길이 발언 검토 엔드포인트을 ![.](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

해당 의도에 동의 하면 확인 표시를 선택 합니다. 제안에 동의하지 않는 경우, 맞춤 의도 드롭다운 목록에서 올바른 의도를 선택한 다음, 맞춤 의도 오른쪽에 있는 확인 표시를 선택합니다. 확인 표시를 선택한 후에는 utterance이 의도 한 것으로 이동 하 고 **검토 엔드포인트 길이 발언** 목록에서 제거 됩니다.

> [!TIP]
> **길이 발언 검토** 목록에서 모든 예제 길이 발언의 엔터티 예측을 검토 하 고 수정 하려면 의도 세부 정보 페이지로 이동 하는 것이 중요 합니다.

## <a name="delete-utterance"></a>발화 삭제

검토 목록에서 각 발화를 삭제할 수 있습니다. 삭제되면 목록에 다시 나타나지 않습니다. 이는 사용자가 엔드포인트에서 동일한 발화를 입력하는 경우에도 적용됩니다.

Utterance를 삭제 해야 하는지 확실 하지 않은 경우이를 없음 의도로 이동 하거나 `miscellaneous`와 같은 새 의도를 만들어 utterance을 해당 의도로 이동 합니다.

## <a name="disable-active-learning"></a>활성 학습 사용 안 함

활성 학습을 사용 하지 않도록 설정 하려면 사용자 쿼리를 기록 하지 않습니다. 이렇게 하려면 기본값은 false 이므로 querystring 값 `log=false` querystring 매개 변수 및 값을 사용 하 여 [엔드포인트 쿼리](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) 를 설정 합니다.

## <a name="next-steps"></a>다음 단계

제안된 발화에 레이블을 지정한 후 성능이 얼마나 향상되는지 테스트하려면 위쪽 패널에서 **테스트**를 선택하여 테스트 콘솔에 액세스할 수 있습니다. 테스트 콘솔을 사용하여 앱을 테스트하는 방법에 대한 자세한 내용은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.
