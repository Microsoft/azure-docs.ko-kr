---
title: LUIS(Language Understanding)란?
description: LUIS(Language Understanding) - 의미를 예측하고 정보를 추출하기 위해 대화형 자연어에 기계 학습을 사용하는 클라우드 기반 API 서비스입니다.
keywords: Azure, 인공 지능, ai, 자연어 처리, nlp, 자연어 인식, nlu, ai 대화, 대화형 ai, ai 챗봇, 챗봇 작성자, LUIS, nlp ai, luis ai, azure luis, 자연어 이해
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 98d801f6a34feb40d56215f8b6257a68ec628e10
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320055"
---
# <a name="what-is-language-understanding-luis"></a>LUIS(Language Understanding)란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

LUIS(Language Understanding)는 사용자 지정 기계 학습 인텔리전스를 사용자의 자연스러운 기존 언어 텍스트에 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 API 서비스입니다.

LUIS용 클라이언트 애플리케이션은 자연어로 사용자와 통신하여 작업을 완료하는 대화형 애플리케이션입니다. 클라이언트 애플리케이션의 예로는 소셜 미디어 앱, 챗봇 및 음성 지원 데스크톱 애플리케이션을 들 수 있습니다.

![Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지](./media/luis-overview/luis-entry-point.png "Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지")

## <a name="use-luis-in-a-chat-bot"></a>채팅 봇에서 LUIS 사용

<a name="Accessing-LUIS"></a>

LUIS 앱이 게시되면 클라이언트 애플리케이션은 LUIS 자연어 처리 엔드포인트 [API][endpoint-apis]에 발언을 전송하고, 결과를 JSON 응답으로 수신합니다. LUIS에 사용되는 일반 클라이언트 애플리케이션은 챗봇입니다.


![채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지](./media/luis-overview/LUIS-chat-bot-request-response.svg "채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지")

|단계|작업|
|:--|:--|
|1|클라이언트 애플리케이션이 사용자 _발언_(사용자가 한 말의 텍스트) "I want to call my HR rep."를 LUIS 엔드포인트에 HTTP 요청으로 보냅니다.|
|2|LUIS를 사용하면 사용자 지정 언어 모델을 만들어서 애플리케이션에 인텔리전스를 추가합니다. 기계 학습 언어 모델은 사용자의 비정형 입력 텍스트를 가져와서 최상위 의도인 `HRContact`와 함께 JSON 형식의 응답을 반환합니다. 최소 JSON 엔드포인트 응답에는 최소한 쿼리 발언 및 최상위 채점 의도가 포함됩니다. _연락처 유형_ 엔터티와 같은 데이터를 추출할 수도 있습니다.|
|3|클라이언트 애플리케이션은 JSON 응답을 사용하여 사용자의 요청을 처리하는 방법에 대한 결정을 내립니다. 이러한 의사 결정에는 봇 프레임워크 코드의 의사 결정 트리 및 기타 서비스에 대한 호출이 포함될 수 있습니다. |

LUIS 앱은 클라이언트 애플리케이션이 현명한 선택을 내릴 수 있도록 인텔리전스를 제공합니다. LUIS는 이러한 선택 항목을 제공하지 않습니다.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>NLU(자연어 인식)

NLP(자연어 처리)의 하위 집합인 NLU 형태로 [LUIS가 AI(인공 지능)를 제공](artificial-intelligence.md)합니다.

LUIS 앱은 도메인별 자연어 모델을 포함합니다. 미리 작성된 모델로 LUIS 앱을 시작할 수도 있고, 직접 작성할 수도 있고, 미리 작성된 도메인 조각을 사용자 고유의 정보와 혼합할 수도 있습니다.

* **미리 작성된 모델** LUIS에는 의도, 발언 및 미리 작성된 엔터티를 비롯하여 미리 작성된 여러 도메인 모델이 포함되어 있습니다. 미리 작성된 모델의 의도 및 발언을 사용하지 않고도 미리 작성된 엔터티를 사용할 수 있습니다. [미리 작성된 도메인 모델](luis-how-to-use-prebuilt-domains.md)에는 모든 디자인이 포함되며 LUIS를 빠르게 시작하는 유용한 방법입니다.

* **사용자 지정 모델** LUIS는 의도와 엔터티를 포함한 사용자 지정 모델을 식별하는 여러 가지 방법을 제공합니다. 엔터티에는 기계 학습 엔터티, 특정 또는 리터럴 엔터티 및 기계 학습과 리터럴의 조합이 포함됩니다.

[NLP](artificial-intelligence.md) 및 NLU의 LUIS 관련 영역에 대해 자세히 알아봅니다.

## <a name="step-1-design-and-build-your-model"></a>1단계: 모델 디자인 및 빌드

**[의도](luis-concept-intent.md)** 라고 하는 사용자 의도 범주로 모델을 디자인합니다. 각 의도에는 사용자 **[발언](luis-concept-utterance.md)** 예제가 필요합니다. 각 발화는 [기계 학습 엔터티](luis-concept-entity-types.md#effective-machine-learned-entities)로 추출해야 하는 데이터를 제공할 수 있습니다.

|예제 사용자 발언|Intent|추출된 데이터|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm, Bob|

[작성](https://go.microsoft.com/fwlink/?linkid=2092087) API 또는 [**LUIS 포털**](https://www.luis.ai)(또는 둘 다)을 사용하여 모델을 빌드합니다. [포털](get-started-portal-build-app.md) 및 [SDK 클라이언트 라이브러리](azure-sdk-quickstart.md)를 사용하여 빌드하는 방법에 대해 자세히 알아봅니다.

## <a name="step-2-get-the-query-prediction"></a>2단계: 쿼리 예측 가져오기

앱의 모델을 학습하여 엔드포인트에 게시하면 클라이언트 애플리케이션(예: 채팅 봇)은 예측 [엔드포인트](https://go.microsoft.com/fwlink/?linkid=2092356) API에 발화를 보냅니다. API는 분석을 위해 발화에 모델을 적용하고 JSON 형식의 예측 결과로 응답합니다.

최소 JSON 엔드포인트 응답에는 최소한 쿼리 발언 및 최상위 채점 의도가 포함됩니다. 다음 **연락처 유형** 엔터티와 전반적인 감정 등의 데이터를 추출할 수도 있습니다.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>3단계: 모델 예측 개선

LUIS 앱이 게시되고 실제 사용자 발화를 받은 후 LUIS가 엔드포인트 발화의 [활성 학습](luis-concept-review-endpoint-utterances.md)을 제공하여 예측 정확도를 높입니다. 개발 수명 주기에서 정기 유지 관리 작업의 일환으로 이러한 제안 사항을 검토합니다.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>개발 수명 주기 및 도구
LUIS는 전체 [개발 수명 주기](luis-concept-app-iteration.md)에 통합될 수 있는 도구, 버전 관리 및 다른 LUIS 작성자와의 협업을 제공합니다.

LUIS(Language Understanding)는 REST API로서 HTTP 요청과 함께 모든 제품, 서비스 또는 프레임워크에 사용할 수 있습니다. 또한 LUIS는 여러 상위 프로그래밍 언어에 대한 클라이언트 라이브러리(SDK)를 제공합니다. 제공된 [개발자 리소스](developer-reference-resource.md)에 대해 자세히 알아봅니다.

봇에서 LUIS를 빠르고 쉽게 사용할 수 있는 도구는 다음과 같습니다.
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) - NPM 패키지에서 독립 실행형 명령줄 도구 또는 가져오기를 사용하여 작성 및 자동 완성을 제공합니다.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) - LUISGen은 내보낸 LUIS 모델에서 강력한 형식의 C# 및 TypeScript 소스 코드를 생성하는 도구입니다.
* [디스패치](https://aka.ms/dispatch-tool)는 디스패처 모델을 사용하는 부모 앱에서 여러 LUIS 및 QnA Maker 앱을 사용할 수 있도록 허용합니다.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) - 봇용 언어 모델을 관리하는 데 도움이 되는 명령줄 도구입니다.

## <a name="integrate-with-a-bot"></a>봇과 통합

[Microsoft Bot Framework](https://dev.botframework.com/)와 함께 [Azure Bot 서비스](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)를 사용하여 채팅 봇을 빌드 및 배포합니다. 상위 봇 시나리오를 위해 디자인된 그래픽 인터페이스 도구, [작성기](https://docs.microsoft.com/composer/) 또는 [작업 봇 샘플](https://github.com/microsoft/BotBuilder-Samples)을 사용하여 디자인하고 개발합니다.

## <a name="integrate-with-other-cognitive-services"></a>다른 Cognitive Services와 통합

LUIS에 사용되는 다른 Cognitive Services:
* [QnA Maker][qnamaker]에서는 몇 가지 유형의 텍스트를 질문 및 답변 기술 자료로 결합할 수 있습니다.
* [음성 서비스](../Speech-Service/overview.md)는 음성 언어 요청을 텍스트로 변환합니다.

LUIS는 기존 LUIS 리소스의 일부로 Text Analytics의 기능을 제공합니다. 이 기능에는 미리 빌드된 keyPhrase 엔터티를 사용하는 [감정 분석](luis-how-to-publish-app.md#configuring-publish-settings) 및 [키 구문 추출](luis-reference-prebuilt-keyphrase.md)이 포함됩니다.

## <a name="learn-with-the-quickstarts"></a>빠른 시작으로 학습

[포털](get-started-portal-build-app.md) 및 [SDK 클라이언트 라이브러리](azure-sdk-quickstart.md)를 사용하여 실습 빠른 시작이 포함된 LUIS에 대해 알아봅니다.


## <a name="next-steps"></a>다음 단계

* 서비스 및 설명서의 [새로운 기능](whats-new.md)
* [의도](luis-concept-intent.md) 및 [엔터티](luis-concept-entity-types.md)를 사용하여 [앱을 계획](luis-how-plan-your-app.md)합니다.
* [예측 엔드포인트를 쿼리](luis-get-started-get-intent-from-browser.md)합니다.
* LUIS의 [개발자 리소스](developer-reference-resource.md).

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
