---
title: LUIS 포털에서 앱 테스트
description: Language Understanding(LUIS)을 사용하여 애플리케이션을 지속적으로 개선하고 해당 언어의 이해를 향상합니다.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152910"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>LUIS 포털에서 LUIS 앱 테스트

앱 [테스트](luis-concept-test.md) 는 반복적인 프로세스입니다. LUIS 앱을 학습시킨 후 샘플 발화로 앱을 테스트하여 의도 및 엔터티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>테스트 전 학습

최신 버전의 활성 앱에 대해 테스트 하려면 테스트 하기 전에 상단 메뉴에서 **학습** 을 선택 합니다.

## <a name="test-an-utterance"></a>발화 테스트

테스트 utterance는 응용 프로그램의 예제 길이 발언와 정확 하 게 일치 하면 안 됩니다. 테스트 utterance에는 사용자에 게 필요한 단어 선택, 구 길이 및 엔터티 사용이 포함 되어야 합니다.

1. **내 앱** 페이지에서 해당 이름을 선택하여 앱에 액세스합니다.

1. **테스트** 슬라이드 아웃 패널에 액세스하려면 애플리케이션의 위쪽 패널에서 **테스트**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![& 테스트 앱 페이지를 학습](./media/luis-how-to-interactive-test/test.png)

1. 텍스트 상자에 발화를 입력하고 Enter 키를 선택합니다. **테스트**에서 원하는 만큼 테스트 발화를 입력할 수 있지만 한 번에 하나의 발화만 입력할 수 있습니다.

1. 발화, 상위 의도 및 점수가 텍스트 상자 아래 발화 목록에 추가됩니다.

    ![대화형 테스트에서 잘못된 의도 식별](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>점수 검사

**검사** 패널에서 테스트 결과의 세부 정보를 검사합니다.

1. **테스트** 슬라이드 아웃 패널이 열려 있을 때 비교할 발화에 **검사**를 선택합니다.

    ![테스트 결과에 대한 자세한 내용을 보려면 [검사] 단추를 선택합니다.](./media/luis-how-to-interactive-test/inspect.png)

1. **검사** 패널이 나타납니다. 패널에는 식별된 엔터티뿐만 아니라 상위 채점 의도도 포함됩니다. 패널에는 선택한 발화의 결과가 표시됩니다.

    ![패널에는 식별된 엔터티뿐만 아니라 상위 채점 의도도 포함됩니다. 패널에는 선택한 발화의 결과가 표시됩니다.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>상위 점수 의도 수정

1. 상위 점수 의도가 잘못된 경우, **편집** 단추를 선택합니다.

1.  드롭다운 목록에서 발화의 올바른 의도를 선택합니다.

    ![올바른 의도 선택](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>감정 결과 보기

**감정 분석**이 **[게시](luis-how-to-publish-app.md#enable-sentiment-analysis)** 페이지에 구성되어 있으면 테스트 결과에는 발화에서 발견된 감정이 포함됩니다.

![감정 분석이 포함된 테스트 창 이미지](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>일치하는 패턴의 의도 수정

[패턴](luis-concept-patterns.md)을 사용 중이고 발화가 패턴과 일치했지만 잘못된 의도가 예측된 경우에는 패턴 옆의 **편집** 링크를 선택한 다음, 올바른 의도를 선택합니다.

## <a name="compare-with-published-version"></a>게시된 버전과 비교

게시된 [엔드포인트](luis-glossary.md#endpoint) 버전을 사용하여 앱의 활성 버전을 테스트할 수 있습니다. **검사** 패널에서 **게시된 버전과 비교**를 선택합니다. 게시된 모델에 대한 모든 테스트는 Azure 구독 할당량 잔액에서 차감됩니다.

![게시된 버전과 비교](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>테스트 패널에서 엔드포인트 JSON 보기
**JSON 보기 표시**를 선택하여 비교에 반환된 엔드포인트 JSON을 볼 수 있습니다.

![게시된 JSON 응답](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>테스트 패널의 추가 설정

### <a name="luis-endpoint"></a>LUIS 엔드포인트

여러 개의 LUIS 엔드포인트가 있는 경우 테스트의 [게시됨] 창에서 **추가 설정** 링크를 사용하여 테스트에 사용되는 엔드포인트를 변경합니다. 사용할 엔드포인트가 확실하지 않은 경우 기본 **Starter_Key**를 선택합니다.

> [!div class="mx-imgBorder"]
> 추가 설정 링크가 강조 표시 된 테스트 패널 ![강조 표시](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>일괄 테스트
일괄 테스트 [개념](luis-concept-batch-test.md)을 확인하고 발화를 일괄 테스트하는 [방법](luis-how-to-batch-test.md)을 알아봅니다.

## <a name="next-steps"></a>다음 단계

테스트에서 LUIS 앱이 올바른 의도와 엔터티를 인식하지 못하는 것으로 나타나면 추가 발화에 레이블을 지정하거나 기능을 추가하여 LUIS 앱의 정확도를 개선할 수 있습니다.

* [LUIS로 제안된 발화에 레이블 지정](luis-how-to-review-endpoint-utterances.md)
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md)
