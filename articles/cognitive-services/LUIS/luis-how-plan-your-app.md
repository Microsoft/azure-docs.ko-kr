---
title: 앱 계획-LUIS
description: 관련 앱 의도 및 엔터티를 간략하게 설명한 다음, LUIS(Language Understanding Intelligent Services)에서 애플리케이션 계획을 만듭니다.
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 38d80a77303e248ab137f87f8063b5cbc358f51a
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892918"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>주제 도메인 및 데이터 추출을 사용 하 여 LUIS 앱 스키마 계획

LUIS 앱 스키마는 주체 [도메인과](luis-glossary.md#domain)관련 된 [의도](luis-glossary.md#intent) 및 [엔터티](luis-glossary.md#entity) 를 포함 합니다. 의도는 사용자 [길이 발언](luis-glossary.md#utterance)를 분류 하 고 엔터티는 사용자 길이 발언에서 데이터를 추출 합니다.

## <a name="identify-your-domain"></a>도메인 식별

LUIS 앱은 주제 도메인을 중심으로 합니다. 예를 들어 티켓, 항공편, 호텔 및 임대 자동차 예약을 처리 하는 여행 앱이 있을 수 있습니다. 또 다른 앱에서는 운동, 피트니스 노력 추적 및 목표 설정과 관련된 콘텐츠를 제공할 수 있습니다. 도메인을 식별 하면 도메인에 관련 된 단어 또는 구를 찾을 수 있습니다.

> [!TIP]
> LUIS는 많은 일반적인 시나리오에 대해 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 제공합니다. 미리 빌드된 도메인을 앱의 시작점으로 사용할 수 있는지 확인하세요.

## <a name="identify-your-intents"></a>의도 식별

응용 프로그램의 작업에 중요 한 [의도](luis-concept-intent.md) 를 생각해 보십시오.

항공편을 예약하고 사용자 목적지의 날씨를 확인하는 기능이 있는 여행 앱을 예로 들어보겠습니다. `BookFlight` `GetWeather` 이러한 작업에 대 한 및 의도를 정의할 수 있습니다.

더 복잡 한 응용 프로그램에서는 더 많은 기능을 사용할 수 있으며, 의도 한 것이 너무 구체적이 지 않도록 신중 하 게 정의 해야 합니다. 예를 들어 `BookFlight` 및은 `BookHotel` 별도의 의도 여야 하지만 및는 `BookInternationalFlight` `BookDomesticFlight` 너무 유사할 수 있습니다.

> [!NOTE]
> 앱의 기능을 수행하는 데 필요한 만큼의 의도만 사용하는 것이 좋습니다. 너무 많은 의도를 정의하면 LUIS에서 발화를 올바르게 분류하기가 더욱 어려워집니다. 너무 적은 수를 정의 하는 경우 중복 될 수 있습니다.

전반적인 사용자 의도를 식별할 필요가 없는 경우 모든 예제 사용자 길이 발언를 의도에 추가 합니다 `None` . 앱이 더 많은 의도를 필요로 하는 경우 나중에 만들 수 있습니다.

## <a name="create-example-utterances-for-each-intent"></a>각 의도의 예제 발화 만들기

시작 하려면 각 의도에 대해 너무 많은 길이 발언를 만들지 마십시오. 의도를 결정 한 후에는 길이 발언의 15 ~ 30 예제를 만듭니다. 각 utterance는 이전에 제공 된 길이 발언과 달라 야 합니다. 길이 발언에는 전체 단어 수, 단어 선택, 동사 시제 및 [문장 부호가](luis-reference-application-settings.md#punctuation-normalization)포함 됩니다.

자세한 내용은 [LUIS 앱에 대 한 좋은 길이 발언 이해](luis-concept-utterance.md)를 참조 하세요.

## <a name="identify-your-entities"></a>엔터티 식별

예제 발화에서 추출하려는 엔터티를 식별합니다. 비행을 예약 하려면 대상, 날짜, 항공, 티켓 범주 및 여행 클래스와 같은 정보가 필요 합니다. 이러한 데이터 형식에 대 한 엔터티를 만든 다음 길이 발언 예제에서 [엔터티](luis-concept-entity-types.md) 를 표시 합니다. 엔터티는 의도를 수행 하는 데 중요 합니다.

앱에서 사용할 엔터티를 결정할 때는 개체 형식 간의 관계를 캡처하기 위한 엔터티 형식이 서로 다르다는 점에 유의 하세요. [LUIS의 엔터티](luis-concept-entity-types.md)에서는 다양한 형식에 대해 자세히 설명합니다.

> [!TIP]
> LUIS는 일반적인 대화형 사용자 시나리오에 대해 [미리 빌드된 엔터티](luis-prebuilt-entities.md) 를 제공 합니다. 미리 작성 된 엔터티를 응용 프로그램 개발의 시작 지점으로 사용 하는 것이 좋습니다.

## <a name="resolution-with-intent-or-entity"></a>의도가 나 엔터티에 대 한 해결 방법

대부분의 경우, 특히 자연 스런 대화를 사용 하는 경우 사용자는 둘 이상의 함수나 의도를 포함할 수 있는 utterance를 제공 합니다. 이를 해결 하기 위해 일반적으로 출력의 표현이 의도 및 엔터티 모두에서 수행 될 수 있음을 이해 하는 것이 일반적입니다. 이 표현은 클라이언트 응용 프로그램 작업에 대 한 매핑 가능 해야 하며, 의도로 제한 될 필요가 없습니다.

**Int-동률** 은 작업 (일반적으로 의도 한 것으로 인식 됨)도 엔터티로 캡처되고 특정 작업에 매핑할 수 있는 출력 JSON의이 형식에 의존 하는 개념입니다. _부정_ 은 전체 추출에 대 한 도와 엔터티 모두에 대해 이러한 의존도를 활용 하기 위해 일반적으로 사용 됩니다.

단어 선택이 매우 유사 하지만 결과가 다른 두 개의 길이 발언 고려 합니다.

|발화|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

별도의 두 가지를 사용 하는 대신 machine learning 엔터티를 사용 하 여 단일 의도를 만듭니다 `FlightAction` . Machine learning 엔터티는 일정 예약 및 취소 요청 뿐만 아니라 원본 또는 대상 위치에 대 한 작업 세부 정보를 추출 해야 합니다.

`FlightAction`엔터티는 machine learning 엔터티 및 하위 엔터티의 의사 (pseudo) 스키마에 따라 구조화 됩니다.

* FlightAction
    * 작업
    * 원본
    * 대상

하위 엔터티에 기능을 추가 하는 데 도움이 됩니다. 사용자 길이 발언에 표시 될 것으로 예측 된 어휘와 예측 응답에서 반환 하려는 값을 기준으로 기능을 선택 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS development lifecylce 학습](luis-concept-app-iteration.md)

