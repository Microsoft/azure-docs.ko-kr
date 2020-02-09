---
title: 의도 및 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 단일 의도는 사용자가 수행 하려는 작업 또는 작업을 나타냅니다. 사용자의 발언으로 표현되는 목적 또는 목표입니다. 사용자가 애플리케이션에서 수행하려는 작업에 해당하는 의도 집합을 정의합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280897"
---
# <a name="intents-in-your-luis-app"></a>LUIS 앱의 의도

의도는 사용자가 수행하려는 작업을 나타냅니다. 사용자의 [발화](luis-concept-utterance.md)로 표현되는 목적 또는 목표입니다.

사용자가 애플리케이션에서 수행하려는 작업에 해당하는 의도 집합을 정의합니다. 예를 들어, 여행 앱에서는 다음과 같은 여러 의도를 정의합니다.

여행 앱 의도   |   예제 발언   | 
------|------|
 BookFlight     |   “Book me a flight to Rio next week” <br/> “Fly me to Rio on the 24th” <br/> “I need a plane ticket next Sunday to Rio de Janeiro”    |
 Greeting     |   “Hi” <br/>“hello” <br/>“Good morning”  |
 CheckWeather | “What's the weather like in Boston?” <br/> “Show me the forecast for this weekend” |
 없음         | “Get me a cookie recipe”<br>“Did the Lakers win?” |

모든 응용 프로그램에는 대체 의도 인 미리 정의 된 의도 "[None](#none-intent)"이 함께 제공 됩니다. 

## <a name="prebuilt-domains-provide-intents"></a>미리 빌드된 도메인이 의도를 제공함
를 정의 하는 것 외에도 미리 작성 된 [도메인](luis-how-to-use-prebuilt-domains.md)중 하나에서 미리 작성 된 의도를 사용할 수 있습니다. 

## <a name="return-all-intents-scores"></a>모든 의도의 점수 반환
단일 의도에 하나의 발화를 할당합니다. LUIS에서 엔드포인트에 대 한 utterance를 수신 하면 기본적으로 해당 utterance의 최상위 의도를 반환 합니다. 

Utterance에 대 한 모든 의도의 점수를 원하는 경우 예측 API의 쿼리 문자열에 플래그를 제공할 수 있습니다. 

|예측 API 버전|플래그|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>의도 및 엔터티 비교
의도는 응용 프로그램이 사용자에 대해 수행 해야 하는 작업을 나타내며 전체 utterance을 기반으로 합니다. 발화에는 하나의 상위 점수 의도만 포함되지만, 여러 엔터티가 포함될 수 있습니다. 

<a name="how-do-intents-relate-to-entities"></a>

사용자가 checkweather () 함수 호출과 같이 클라이언트 응용 프로그램에서 작업을 _트리거하는 의도_ 를 만듭니다. 그런 다음 작업을 실행 하는 데 필요한 매개 변수를 나타내는 엔터티를 만듭니다. 

|Intent   | 엔터티 | 예제 발화   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | What's the weather like in `Seattle` `tomorrow`? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Show me the forecast for `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>미리 빌드된 도메인 의도

[미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md) 은 길이 발언를 사용 하 여 의도를 제공 합니다. 

## <a name="none-intent"></a>None 의도

**None** 의도가 생성되지만 고의로 비워 둡니다. **None** 의도는 필수 의도이며 삭제하거나 이름을 바꿀 수 없습니다. 이 의도를 도메인 외부에 있는 발화로 채웁니다.

어떤 경우에 **도** 모든 앱에서 중요 한 대체 의도가 없으며 총 길이 발언의 10%가 있어야 합니다. 앱 도메인(주체 영역)에서 중요하지 않은 LUIS 발화를 학습시키는 데 사용됩니다. **None** 의도의 발화를 추가하지 않으면 LUIS는 도메인 외부에 있는 발화를 도메인 의도 중 하나에 적용합니다. 이로 인해 LUIS가 발화의 잘못된 의도를 학습하게 되어 예측 점수가 왜곡됩니다. 

Utterance가 None으로 예측 되 면 클라이언트 응용 프로그램은 더 많은 질문을 하거나 사용자에 게 유효한 선택 항목을 제공 하는 메뉴를 제공할 수 있습니다. 

## <a name="negative-intentions"></a>부정적 의도 
“자동차가 **필요해**” 및 “자동차가 **필요하지 않아**”와 같이 부정적 의도와 긍정적 의도를 결정하려면 두 개의 의도(긍정적 의도 및 부정적 의도)를 만들고 각 의도에 적절한 발화를 추가합니다. 또는 단일 의도를 만들고 두 가지 긍정적 및 부정적 용어를 엔터티로 표시할 수 있습니다.  

## <a name="intents-and-patterns"></a>의도 및 패턴

일부 또는 전체에 정규식으로 정의할 수 있는 예제 길이 발언이 있는 경우 [패턴과](luis-concept-patterns.md)쌍을 이루는 [정규식 엔터티](luis-concept-entity-types.md#regular-expression-entity) 를 사용 하는 것이 좋습니다. 

정규식 엔터티를 사용 하면 패턴이 일치 하도록 데이터 추출을 보장 합니다. 패턴 일치는 정확한 의도가 반환 되도록 보장 합니다. 

## <a name="intent-balance"></a>의도 균형
앱 도메인 의도는 각 의도에 발화를 균형 있게 분산해야 합니다. 한 의도에 10개의 발화를 포함하고 다른 의도에 500개의 발화를 포함하지 마세요. 균형이 맞지 않습니다. 이 상황이 발생하면 500개의 발화가 포함된 의도를 검토하여 많은 의도를 [패턴](luis-concept-patterns.md)으로 재구성할 수 있는지 확인합니다. 

**None** 의도는 균형에 포함되지 않습니다. 앱의 총 발화 중 10%가 이 의도에 포함되어야 합니다.

## <a name="intent-limits"></a>의도 제한
[제한](luis-boundaries.md#model-boundaries)을 검토하여 모델에 추가할 수 있는 의도 수를 이해합니다. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>최대 의도 수보다 더 많이 필요한 경우 
먼저 시스템이 너무 많은 의도를 사용하고 있는지 여부를 고려합니다. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>여러 의도를 엔터티가 포함된 단일 의도로 결합할 수 있음 
너무 비슷한 의도의 경우, LUIS가 의도를 구분하기가 더 어려울 수 있습니다. 의도는 사용자가 요청하는 기본 작업을 캡처하기에 충분히 다양해야 하지만, 코드가 사용하는 모든 경로를 캡처할 필요는 없습니다. 예를 들어, BookFlight 및 FlightCustomerService는 여행 앱에서 개별 의도일 수 있지만, BookInternationalFlight 및 BookDomesticFlight는 너무 비슷합니다. 시스템이 이러한 의도를 구분해야 하는 경우, 의도 대신에 엔터티 또는 기타 논리를 사용하세요. 

### <a name="dispatcher-model"></a>디스패처 모델
[디스패치 모델](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)과 LUIS 및 QnA Maker 앱을 결합하는 방법에 대해 자세히 알아봅니다. 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>많은 의도가 포함된 앱의 도움말 요청
의도 수를 줄이거나 의도를 여러 앱으로 나눈 경우 앱이 작동하지 않으면 지원에 문의하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="next-steps"></a>다음 단계

* 의도에 관련된 중요 단어인 [엔터티](luis-concept-entity-types.md)에 대해 자세히 알아보기
* LUIS 앱에서 [의도를 추가 및 관리](luis-how-to-add-intents.md)하는 방법에 대해 알아봅니다.
* 의도 [모범 사례](luis-concept-best-practices.md) 검토
