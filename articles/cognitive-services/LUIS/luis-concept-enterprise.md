---
title: 엔터프라이즈 개념-LUIS
titleSuffix: Azure Cognitive Services
description: 규모가 큰 LUIS 앱 또는 LUIS 및 QnA Maker를 함께 포함하는 여러 앱에 대한 디자인 개념을 이해합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619929"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>LUIS 앱의 엔터프라이즈 전략
엔터프라이즈 앱의 이러한 디자인 전략을 검토합니다.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>LUIS 요청이 할당량을 초과할 것으로 예상하는 경우

LUIS에는 Azure 리소스의 가격 책정 계층에 따라 월별 할당량 및 초당 할당량이 있습니다. 

LUIS app request rate가 허용 된 [할당량](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)을 초과 하는 경우 다음을 수행할 수 있습니다.

* [동일한 앱 정의](#use-multiple-apps-with-same-app-definition)를 사용 하 여 더 많은 LUIS 앱에 부하를 분산 합니다. 여기에는 선택적으로 [컨테이너](luis-container-howto.md)에서 LUIS를 실행 하는 작업이 포함 됩니다. 
* 앱에 [여러 키](#assign-multiple-luis-keys-to-same-app) 를 만들고 할당 합니다. 

### <a name="use-multiple-apps-with-same-app-definition"></a>동일한 앱 정의가 포함된 여러 앱 사용
원래 LUIS 앱을 내보낸 다음, 앱을 다시 개별 앱으로 가져옵니다. 각 앱에는 고유한 앱 ID가 있습니다. 게시할 때 모든 앱에서 동일한 키를 사용하는 대신 각 앱에 개별 키를 만듭니다. 단일 앱에 집중되지 않도록 부하를 모든 앱에 분산시킵니다. [Application Insights](luis-tutorial-bot-csharp-appinsights.md)를 추가하여 사용을 모니터링합니다. 

모든 앱 간에 동일한 상위 의도를 가져오려면 첫 번째 의도와 두 번째 의도 간의 의도 예측이 LUIS에서 혼동하지 않을 만큼 충분히 폭넓은지 확인하여 음성의 사소한 변형에 대해 앱 간에 서로 다른 결과를 제공합니다. 

이러한 형제 앱을 학습할 때 [모든 데이터를 학습](luis-how-to-train.md#train-with-all-data)해야 합니다.

단일 앱을 마스터로 지정합니다. 검토를 위해 제안된 모든 발화는 마스터 앱에 추가된 다음, 다시 다른 모든 앱으로 이동되어야 합니다. 이것은 앱의 전체 내보내기이거나 마스터에서 자식으로 레이블이 지정된 발화를 로드하는 것입니다. [단일 발언](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) 또는 [일괄 처리](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)를 위해 [LUIS](luis-reference-regions.md) 웹 사이트 또는 작성 API에서 로드할 수 있습니다. 

활성 학습에 대 한 [엔드포인트 길이 발언](luis-how-to-review-endpoint-utterances.md) 의 모든 2 주마다 정기적으로 검토 한 다음 다시 학습 및 다시 게시를 예약 합니다. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>동일한 앱에 여러 LUIS 키 할당
LUIS 앱이 단일 키 할당량이 허용하는 것보다 많은 엔드포인트 적중 횟수를 수신하는 경우, 더 많은 키를 만들고 LUIS 앱에 할당합니다. 트래픽 관리자 또는 부하 분산 디바이스를 만들어 여러 엔드포인트 키에서 엔드포인트 쿼리를 관리합니다. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>모놀리식 앱이 잘못된 의도를 반환하는 경우
앱이 다양한 사용자 발화를 예측하도록 하려면 [디스패치 모델](#dispatch-tool-and-model)을 구현하는 것이 좋습니다. 모놀리식 앱을 분할하면 LUIS는 부모 앱과 자식 앱에서 의도를 혼동하지 않고 성공적으로 의도 간 검색에 초점을 맞출 수 있습니다. 

활성 학습의 주기적인 [엔드포인트 발화 검토](luis-how-to-review-endpoint-utterances.md)를 예약한 다음(예: 격주), 다시 학습시키고 다시 게시합니다. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>500개 이상의 의도가 필요한 경우
500를 초과 하는 office 길잡이를 개발 하 고 있다고 가정 합니다. 200개의 의도는 모임 예약에 관련되고, 200개는 미리 알림에 관련되고, 200개는 동료 정보 가져오기에 관련되고, 200개는 메일 보내기에 관련된 경우, 각 그룹이 단일 앱에 있도록 의도를 그룹화한 다음, 각 의도가 포함된 최상위 앱을 만듭니다. [디스패치 모델](#dispatch-tool-and-model) 을 사용 하 여 최상위 앱을 빌드 하세요. 그런 다음 [디스패치 모델의 자습서](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)에 표시 된 것 처럼 연계 호출을 사용 하도록 봇을 변경 합니다. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>여러 LUIS 및 QnA Maker 앱을 결합해야 하는 경우
봇에 응답 해야 하는 LUIS 및 QnA maker 앱이 여러 개 있는 경우 [디스패치 모델](#dispatch-tool-and-model) 을 사용 하 여 최상위 앱을 빌드 하세요.  그런 다음 [디스패치 모델의 자습서](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)에 표시 된 것 처럼 연계 호출을 사용 하도록 봇을 변경 합니다. 

## <a name="dispatch-tool-and-model"></a>디스패치 도구 및 모델
[BotBuilder](https://github.com/Microsoft/botbuilder-tools) 에 있는 [디스패치][dispatch-tool] 명령줄 도구를 사용 하 여 여러 LUIS 및/또는 QNA MAKER apps를 부모 LUIS 앱으로 결합 합니다. 이 접근법을 통해 모든 주체 및 다른 자식 주체 도메인을 개별 앱에 포함하는 부모 도메인을 사용할 수 있습니다. 

![디스패치 아키텍처의 개념 이미지](./media/luis-concept-enterprise/dispatch-architecture.png)

부모 도메인은 앱 목록에서 `Dispatch`라는 버전의 LUIS에 표시됩니다. 

채팅 봇은 utterance를 받은 다음 예측을 위해 부모 LUIS 앱에 보냅니다. 부모 앱에서 가장 많이 예측 된 의도는 다음에 호출 되는 LUIS 자식 앱을 결정 합니다. 채팅 봇은 보다 구체적인 예측을 위해 자식 앱에 utterance를 보냅니다.

이 호출 계층이 Bot Builder v4 [디스패처-응용 프로그램-자습서](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)에서 어떻게 수행 되는지 이해 합니다.  

### <a name="intent-limits-in-dispatch-model"></a>디스패치 모델의 의도 제한
디스패치 애플리케이션에는 최댓값으로 500개 의도에 해당하는 500개 디스패치 원본이 있습니다. 

## <a name="more-information"></a>자세한 정보

* [Bot framework SDK](https://github.com/Microsoft/botframework)
* [모델 디스패치 자습서](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [CLI 디스패치](https://github.com/Microsoft/botbuilder-tools)
* 디스패치 모델 봇 샘플- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>다음 단계

* [일괄 테스트](luis-how-to-batch-test.md) 방법 알아보기

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
