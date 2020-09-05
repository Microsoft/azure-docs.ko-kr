---
title: 엔터티 형식-LUIS
description: 엔터티는 예측 런타임에 사용자 utterance에서 데이터를 추출 합니다. _선택적인_보조 목적은 엔터티를 기능으로 사용 하 여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8751bdd52bb1c3738103dc074184a3cf72bfeb09
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207758"
---
# <a name="extract-data-with-entities"></a>엔터티를 사용 하 여 데이터 추출

엔터티는 예측 런타임에 사용자 utterance에서 데이터를 추출 합니다. _선택적인_보조 목적은 엔터티를 기능으로 사용 하 여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.

엔터티는 다음과 같은 여러 가지 유형이 있습니다.

* [machine learning 엔터티](reference-entity-machine-learned-entity.md) -기본 엔터티입니다. 다른 엔터티를 사용 하기 전에이 엔터티 형식으로 스키마를 디자인 해야 합니다.
* 비 기계어 학습은 정확히 일치 하는 텍스트 일치, 패턴 일치 또는 미리 빌드된 엔터티에의 한 검색에 필요한 [기능](luis-concept-feature.md) 으로 사용 됩니다.
* [패턴.](#patternany-entity) [패턴](reference-entity-pattern-any.md) 에서 책 제목과 같은 자유 형식 텍스트를 추출 하는 데 사용 됩니다.

기계 학습 엔터티는 가장 넓은 범위의 데이터 추출 선택 항목을 제공 합니다. 비 기계어 학습 엔터티는 텍스트 일치를 통해 작동 하며 기계 학습 엔터티 또는 의도에 대 한 [필수 기능](#design-entities-for-decomposition) 으로 사용 됩니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 utterance에서 가져올 데이터 (예: 이름, 날짜, 제품 이름 또는 단어의 중요 한 그룹)입니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램에서 작업을 수행 하는 데 필요한 데이터가 필요할 _수 있습니다_ .

모델의 각 의도에 대해 모든 학습 길이 발언 엔터티에 일관 되 게 레이블을 지정 해야 합니다.

 사용자 고유의 엔터티를 정의 하거나 미리 작성 된 엔터티를 사용 하 여 [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서 수](luis-reference-prebuilt-ordinal.md), [전자 메일](luis-reference-prebuilt-email.md)및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 일반적인 개념의 시간을 절약할 수 있습니다.

|발화|엔터티|데이터|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>대상|3<br>뉴욕|


### <a name="entities-are-optional-but-recommended"></a>엔터티는 선택 사항 이지만 권장 됩니다.

[의도](luis-concept-intent.md) 는 필수 이지만 엔터티는 선택 사항입니다. 앱의 모든 개념에 대 한 엔터티를 만들 필요는 없지만, 클라이언트 응용 프로그램에 데이터가 필요 하거나 엔터티가 다른 엔터티 또는 의도에 대 한 힌트 또는 신호 역할을 수행 하는 경우에만 해당 됩니다.

응용 프로그램이 개발 되 고 데이터에 대 한 새로운 요구 사항이 확인 되 면 나중에 LUIS 모델에 적절 한 엔터티를 추가할 수 있습니다.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>엔터티는 데이터 추출을 나타냅니다.

엔터티는 _utterance 내의_데이터 개념을 나타냅니다. 의도는 _전체 utterance_을 분류 합니다.

다음 네 가지 길이 발언을 고려 합니다.

|발화|예측된 의도|추출 된 엔터티|설명|
|--|--|--|--|
|도움말|help|-|추출할 항목이 없습니다.|
|항목 보내기|sendSomething|-|추출할 항목이 없습니다. 모델에는이 컨텍스트에서 추출 하는 데 필요한 기능이 없으며 `something` 받는 사람이 언급 되지 않습니다.|
|Bob이 있는 메일 보내기|sendSomething|`Bob`, `present`|모델은 미리 작성 된 `Bob` 엔터티의 필수 기능을 추가 하 여 추출 `personName` 합니다. 기계 학습 엔터티를 추출 하는 데 사용 되었습니다 `present` .|
|Bob에 게 빠졌습니다의 상자를 보냅니다.|sendSomething|`Bob`, `box of chocolates`|두 개의 중요 한 데이터 `Bob` 및는 `box of chocolates` 기계 학습 엔터티에 의해 추출 됩니다.|

## <a name="label-entities-in-all-intents"></a>모든 의도에서 엔터티 레이블

엔터티는 예측 의도에 관계 없이 데이터를 추출 합니다. _모든 예제 길이 발언_ 의 레이블을 지정 해야 합니다. `None`의도 하지 않은 엔터티 레이블 지정은 다른 의도에 대해 훨씬 더 많은 교육 길이 발언 있는 경우에도 혼동을 일으킵니다.

## <a name="design-entities-for-decomposition"></a>분해를 위한 엔터티 디자인

기계 학습 엔터티를 사용 하면 분해를 위해 앱 스키마를 디자인 하 여 큰 개념을 하위 엔터티로 나눌 수 있습니다.

분해를 위한 디자인을 통해 LUIS은 클라이언트 응용 프로그램에 깊이 있는 엔터티 해상도를 반환할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램이 비즈니스 규칙에 집중 하 고 데이터 확인을 LUIS로 유지할 수 있습니다.

기계 학습 엔터티는 예제 길이 발언를 통해 학습 된 컨텍스트를 기반으로 트리거됩니다.

[**기계 학습 엔터티**](tutorial-machine-learned-entity.md) 는 최상위 수준 추출기. 하위 엔터티는 기계 학습 엔터티의 자식 엔터티입니다.

## <a name="effective-machine-learned-entities"></a>유효한 컴퓨터에서 엔터티를 배웠습니다.

컴퓨터에서 얻은 엔터티를 효과적으로 빌드하려면 다음과 같이 합니다.

* 레이블 지정은 의도에 따라 일치 해야 합니다. 여기에는이 엔터티를 포함 하는 **없음** 의도에서 제공 하는 길이 발언 포함 됩니다. 그렇지 않으면 모델에서 시퀀스를 효과적으로 확인할 수 없습니다.
* 하위 엔터티를 사용 하 여 컴퓨터에서 엔터티를 학습 한 경우 엔터티 및 하위 엔터티의 다른 주문 및 변형이 레이블이 지정 된 길이 발언에 표시 되는지 확인 합니다. 레이블이 지정 된 예 길이 발언는 유효한 모든 폼을 포함 하 고, 표시 되 고 존재 하지 않으며 utterance 내에서 순서가 지정 된 엔터티를 포함 해야 합니다.
* 엔터티를 매우 고정 된 집합으로 과잉 맞춤 하는 것을 피해 야 합니다. **과잉 맞춤** 는 모델을 일반화 하지 않고 기계 학습 모델에서 일반적인 문제가 발생 한 경우에 발생 합니다. 이는 앱이 새 데이터에 적절 하 게 작동 하지 않음을 의미 합니다. 또한 앱이 사용자가 제공 하는 제한 된 예를 넘어 일반화할 수 있도록 레이블이 지정 된 예제 길이 발언를 변경 해야 합니다. 표시 된 예제 뿐 아니라 더 많은 개념을 고려 하기 위해 모델에 대 한 변경 내용이 충분 한 하위 엔터티를 다르게 지정 해야 합니다.

## <a name="effective-prebuilt-entities"></a>효과적인 미리 작성 한 엔터티

미리 작성 된 [엔터티에서](luis-reference-prebuilt-entities.md)제공 하는 것과 같은 일반 데이터를 추출 하는 효과적인 엔터티를 작성 하려면 다음 프로세스를 수행 하는 것이 좋습니다.

사용자 고유의 데이터를 기능으로 엔터티에 가져오는 방법으로 데이터 추출을 향상 시킵니다. 이렇게 하면 데이터의 모든 추가 레이블에서 응용 프로그램에 사용자 이름이 있는 컨텍스트를 알 수 있습니다.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>엔터티의 형식

부모에 대 한 하위 엔터티는 기계 학습 엔터티입니다. 대상는 비 기계어 학습 엔터티를 [기능](luis-concept-feature.md)으로 사용할 수 있습니다.

데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

|엔터티 유형|용도|
|--|--|
|[**컴퓨터-학습**](tutorial-machine-learned-entity.md)|레이블이 지정 된 예제에서 배운 중첩 된 복잡 한 데이터를 추출 합니다. |
|[**은**](reference-entity-list.md)|**정확히 일치**하는 텍스트를 사용 하 여 추출 된 항목 및 해당 동의어의 목록입니다.|
|[**패턴. 모든**](#patternany-entity)|엔터티 끝을 찾는 엔터티는 자유 형식 이므로 확인 하기 어렵습니다. [패턴](luis-concept-patterns.md)에서만 사용할 수 있습니다.|
|[**미리 빌드됨**](luis-reference-prebuilt-entities.md)|URL, 전자 메일 등의 특정 종류의 데이터를 추출 하도록 이미 학습 되었습니다. 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](reference-entity-regular-expression.md)|**정확히 일치**하는 텍스트를 위해 정규식을 사용 합니다.|


## <a name="extraction-versus-resolution"></a>추출 및 해결

엔터티는 데이터가 utterance에 표시 될 때 데이터를 추출 합니다. 엔터티는 데이터를 변경 하거나 확인 하지 않습니다. 엔터티가 엔터티에 대 한 유효한 값인 경우 엔터티는 어떤 확인도 제공 하지 않습니다.

해결 방법에 대 한 해결 방법을 제공 하는 방법이 있지만,이로 인해 응용 프로그램의 기능을 변형 및 실수에 대해 면역으로 제한할 수 있습니다.

목록 엔터티 및 정규식 (텍스트 일치) 엔터티를 하위 엔터티에 [필요한 기능](luis-concept-feature.md#required-features) 으로 사용 하 고 추출에 대 한 필터 역할을 할 수 있습니다. 이를 신중 하 게 사용 하 여 앱의 예측 기능을 방해 합니다.

## <a name="extracting-contextually-related-data"></a>컨텍스트 관련 데이터 추출

Utterance에는 데이터의 의미가 utterance 내의 컨텍스트를 기반으로 하는 두 개 이상의 엔터티가 포함 될 수 있습니다. 예를 들어 원본 및 대상 이라는 두 개의 지리적 위치를 포함 하는 비행을 예약 하는 utterance 있습니다.

`Book a flight from Seattle to Cairo`

두 위치는 티켓 구매를 완료 하기 위해 클라이언트 응용 프로그램에서 각 위치의 유형을 알고 있는 방식으로 추출 해야 합니다.

원본 및 대상을 추출 하려면 티켓 주문 기계 학습 엔터티의 일부로 두 개의 하위 엔터티를 만듭니다. 각 하위 엔터티에 대해 geographyV2를 사용 하는 필수 기능을 만듭니다.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>필요한 기능을 사용 하 여 엔터티 제한

[필요한 기능](luis-concept-feature.md) 에 대 한 자세한 정보

## <a name="patternany-entity"></a>Pattern.any 엔터티

패턴입니다. any는 [패턴](luis-concept-patterns.md)에서만 사용할 수 있습니다.

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>엔터티에 대 한 앱 제한 초과

[제한](luis-limits.md#model-limits)보다 더 많이 필요한 경우 지원 담당자에 게 문의 하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="entity-prediction-status-and-errors"></a>엔터티 예측 상태 및 오류

LUIS 포털은 엔터티에 utterance 예제에 대해 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 표시 합니다. 이 다른 점수는 현재 학습 된 모델을 기반으로 합니다. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS 포털은 엔터티에 utterance 예제에 대해 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 표시 합니다.":::

Erroring 텍스트는 예제 utterance 내에서 강조 표시 되 고 utterance line 예제에는 빨간색 삼각형으로 표시 되는 오른쪽에 대 한 오류 표시기가 있습니다. 

이 정보를 사용 하 여 다음 중 하나 이상을 사용 하 여 엔터티 오류를 해결할 수 있습니다.
* 강조 표시 된 텍스트는 자주입니다. 수정, 검토, 수정 및 다시 학습. 
* 엔터티의 개념을 식별 하는 데 도움이 되는 엔터티의 [기능](luis-concept-feature.md) 을 만듭니다.
* 엔터티를 사용 하 여 [예제 길이 발언](luis-concept-utterance.md) 및 레이블 추가
* 엔터티 개념을 식별 하는 데 도움이 될 수 있는 예측 끝점에서 수신 된 길이 발언에 대 한 [활성 학습 제안을 검토](luis-concept-review-endpoint-utterances.md) 합니다.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요.

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

Machine learning 엔터티를 사용 하 여 utterance에서 구조화 된 데이터를 추출 하는 방법에 대해 알아보려면 [자습서: utterance Language Understanding 엔터티를 사용 하 여 사용자에서 구조화 된 데이터 추출 (LUIS)](tutorial-machine-learned-entity.md) 을 참조 하세요.

