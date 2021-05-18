---
title: LUIS 포털에서 앱 테스트
description: Language Understanding(LUIS)을 사용하여 애플리케이션을 지속적으로 개선하고 해당 언어의 이해를 향상합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98790921"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>LUIS 포털에서 LUIS 앱 테스트

앱 [테스트](luis-concept-test.md)는 반복적인 프로세스입니다. LUIS 앱을 학습시킨 후 샘플 발화로 앱을 테스트하여 의도 및 엔터티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>테스트 전 학습

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.
1. 최신 버전의 활성 앱에 대해 테스트하려면 테스트하기 전에 상단 메뉴에서 **학습** 을 선택합니다.

## <a name="test-an-utterance"></a>발화 테스트

테스트 발화는 앱의 예제 발화와 정확하게 일치하면 안 됩니다. 테스트 발화에는 사용자에게 필요한 단어 선택, 구문 길이 및 엔터티 사용이 포함되어야 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스** 를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. **내 앱** 페이지에서 해당 이름을 선택하여 앱을 엽니다.

1. **테스트** 슬라이드 아웃 패널에 액세스하려면 애플리케이션의 위쪽 패널에서 **테스트** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 학습 및 테스트 페이지](./media/luis-how-to-interactive-test/test.png)

1. 텍스트 상자에 발화를 입력하고 Enter 키를 선택합니다. **테스트** 에서 원하는 만큼 테스트 발화를 입력할 수 있지만 한 번에 하나의 발화만 입력할 수 있습니다.

1. 발화, 상위 의도 및 점수가 텍스트 상자 아래 발화 목록에 추가됩니다.

    > [!div class="mx-imgBorder"]
    > ![대화형 테스트에서 잘못된 의도 식별](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>예측 검사

**검사** 패널에서 테스트 결과의 세부 정보를 검사합니다.

1. **테스트** 슬라이드 아웃 패널이 열려 있을 때 비교할 발화에 **검사** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![테스트 결과에 대한 자세한 내용을 보려면 [검사] 단추를 선택합니다.](./media/luis-how-to-interactive-test/inspect.png)

1. **검사** 패널이 나타납니다. 패널에는 식별된 엔터티뿐만 아니라 상위 채점 의도도 포함됩니다. 패널에는 선택한 발화의 예측이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![테스트 검사 패널의 부분 스크린샷](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>예제 발화에 추가

검사 패널에서 **예제 발화에 추가** 를 선택하여 테스트 발화를 의도에 추가할 수 있습니다.

## <a name="disable-required-features"></a>필수 기능 사용 안 함

이 토글은 학습된 앱이 필요한 기능을 기준으로 엔터티를 올바르게 예측하는지 여부를 확인하는 데 도움이 됩니다. 기본 설정은 예측 중에 필요에 따라 기능을 적용하는 것입니다. 하위 엔터티의 기능이 필요하지 않은 경우 이 토글을 선택하여 예측을 확인할 수 있습니다.

### <a name="when-to-disable-required-features"></a>필수 기능을 사용하지 않도록 설정하는 경우

학습된 앱은 다음 중 하나에 따라 기계 학습 엔터티를 잘못 예측할 수 있습니다.
* 예제 발화의 레이블 지정이 잘못되었습니다.
* 필수 기능이 텍스트와 일치하지 않습니다.

사용자 이름의 하위 엔터티가 있는 기계 학습 엔터티를 예로 들 수 있습니다.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="필수 기능이 포함된 LUIS 포털 기계 학습 엔터티 스키마의 스크린샷":::

이 기계 학습 엔터티의 예제 발화는 `Assign Bob Jones to work on the new security feature`입니다.

추출은 `Assign ticket` 엔터티의 두 개의 하위 엔터티인 티켓 설명 `security feature`, 엔지니어 `Bob Jones`여야 합니다.

하위 엔터티가 성공적으로 예측할 수 있도록 하려면 미리 빌드된 엔터티 [PersonName](luis-reference-prebuilt-person.md)을 `engineer` 하위 엔터티에 기능으로 추가합니다. 이 기능을 필수 기능으로 하는 경우 PersonName 미리 빌드된 엔터티가 텍스트에 대해 예측되는 경우에만 하위 엔터티가 추출됩니다. 즉, PersonName 하위 엔터티를 사용하여 예측하지 않는 텍스트의 모든 이름이 레이블이 지정된 하위 엔터티 `engineer`로 반환되지 않습니다.

대화형 테스트 창을 사용하는 경우 필수 기능을 사용하여 하위 엔터티가 예측되지 않으면 이 설정을 전환하여 필수 기능 없이 하위 엔터티가 예측되는지 확인합니다. 예제 발화의 올바른 레이블 지정으로 인해 필수 기능 없이 하위 엔터티를 올바르게 예측할 수 있습니다.

## <a name="view-sentiment-results"></a>감정 결과 보기

**감정 분석** 이 **[게시](luis-how-to-publish-app.md#enable-sentiment-analysis)** 페이지에 구성되어 있으면 테스트 결과에는 발화에서 발견된 감정이 포함됩니다.

## <a name="correct-matched-patterns-intent"></a>일치하는 패턴의 의도 수정

[패턴](luis-concept-patterns.md)을 사용 중이고 발화가 패턴과 일치했지만 잘못된 의도가 예측된 경우에는 패턴 옆의 **편집** 링크를 선택한 다음, 올바른 의도를 선택합니다.

## <a name="compare-with-published-version"></a>게시된 버전과 비교

게시된 [엔드포인트](luis-glossary.md#endpoint) 버전을 사용하여 앱의 활성 버전을 테스트할 수 있습니다. **검사** 패널에서 **게시된 버전과 비교** 를 선택합니다. 게시된 모델에 대한 모든 테스트는 Azure 구독 할당량 잔액에서 차감됩니다.

> [!div class="mx-imgBorder"]
> ![게시된 버전과 비교](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>테스트 패널에서 엔드포인트 JSON 보기
**JSON 보기 표시** 를 선택하여 비교에 반환된 엔드포인트 JSON을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![게시된 JSON 응답](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>테스트 패널의 추가 설정

### <a name="luis-endpoint"></a>LUIS 엔드포인트

여러 개의 LUIS 엔드포인트가 있는 경우 테스트의 [게시됨] 창에서 **추가 설정** 링크를 사용하여 테스트에 사용되는 엔드포인트를 변경합니다. 사용할 엔드포인트가 확실하지 않은 경우 기본 **Starter_Key** 를 선택합니다.

> [!div class="mx-imgBorder"]
> ![[추가 설정] 링크가 강조 표시된 테스트 패널](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>일괄 테스트
일괄 테스트 [개념](./luis-how-to-batch-test.md)을 확인하고 발화를 일괄 테스트하는 [방법](luis-how-to-batch-test.md)을 알아봅니다.

## <a name="next-steps"></a>다음 단계

테스트에서 LUIS 앱이 올바른 의도와 엔터티를 인식하지 못하는 것으로 나타나면 추가 발화에 레이블을 지정하거나 기능을 추가하여 LUIS 앱의 정확도를 개선할 수 있습니다.

* [LUIS로 제안된 발화에 레이블 지정](luis-how-to-review-endpoint-utterances.md)
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md)