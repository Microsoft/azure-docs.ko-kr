---
title: 기술 자료 개선 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 활성 학습을 사용 하 여 기술 자료의 품질을 향상 시킵니다. 기존 질문을 제거 하거나 변경 하지 않고 검토, 수락 또는 거부, 추가 합니다.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/28/2020
ms.author: diberry
ms.openlocfilehash: cadbf5fa88db7d5e524cb7e075745c03a844f750
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901695"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>활성 학습을 사용하여 기술 자료 개선

활성 학습을 사용하면 사용자가 제출한 정보에 따라 질문과 대답 쌍에 대체 질문을 제안하여 기술 자료 품질을 개선할 수 있습니다. 이 제안을 검토한 다음 기존 질문에 추가하거나 거부합니다.

기술 자료가 자동으로 변경되지는 않습니다. 모든 변경 내용을 적용 하려면 제안에 동의 해야 합니다. 해당 제안과 질문을 수락해도 기존 질문이 변경되거나 제거되지는 않습니다.

## <a name="what-is-active-learning"></a>활성 학습 이란?

QnA Maker는 암시적/명시적 피드백을 사용하여 새로운 질문 변형을 학습합니다.

* [암시적 피드백](#how-qna-makers-implicit-feedback-works) – ranker은 사용자 질문에 매우 가까운 점수와 함께 여러 답변이 있는 경우이를 이해 하 고이를 사용자 의견으로 간주 합니다. 이렇게 하려면 아무것도 수행할 필요가 없습니다.
* [명시적 피드백](#how-you-give-explicit-feedback-with-the-train-api) – 기술 자료에서 점수가 약간 변형 된 여러 답변이 반환 되는 경우 클라이언트 응용 프로그램은 사용자에 게 정확한 질문을 묻습니다. 사용자의 명시적인 피드백이 [학습 API](#train-api)를 사용 하 여 QnA Maker 전송 됩니다.

두 방법 모두 클러스터링 된 유사한 쿼리를 사용 하 여 ranker를 제공 합니다.

## <a name="how-active-learning-works"></a>활성 학습의 작동 방식

활성 학습은 QnA Maker에서 반환 하는 가장 많은 답변의 점수를 기준으로 트리거됩니다. 점수 차이가 작은 범위 내에 있는 경우 쿼리는 가능한 각 QnA 쌍에 대 한 가능한 제안 (대체 질문)으로 간주 됩니다. 특정 QnA 쌍에 대해 제안 된 질문을 수락한 후에는 다른 쌍에 대해 거부 됩니다. 제안을 수락한 후 저장 하 고 학습 해야 합니다.

엔드포인트가 적절한 수와 종류의 사용량 쿼리를 수신하면 활성 학습에서는 가능한 최적의 제안을 제공합니다. 5 개 이상의 유사한 쿼리가 클러스터링 되 면 30 분 마다 QnA Maker 기술 자료 디자이너에 게 허용 하거나 거부할 사용자 기반 질문을 제안 합니다. 모든 제안은 유사성을 기준으로 클러스터되며, 최종 사용자가 특정 쿼리를 전송한 빈도에 따라 대체 질문의 상위 제안이 표시됩니다.

QnA Maker 포털에서 질문이 제안 되 면 해당 제안을 검토 하 고 수락 하거나 거부 해야 합니다. 제안을 관리 하는 API가 없습니다.

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker의 암시적 피드백 작동 방법

QnA Maker의 암시적 피드백은 알고리즘을 사용 하 여 점수의 유사성을 확인 한 다음 활성 학습 제안을 만듭니다. 점수 범위를 결정하는 알고리즘은 단순한 계산이 아닙니다. 다음 예제의 범위는 수정 되지 않지만 알고리즘의 영향을 이해 하기 위한 지침으로 사용 되어야 합니다.

질문의 점수 신뢰도가 80% 등으로 높은 경우 활성 학습용으로 간주되는 점수의 범위도 넓어집니다(대략 10% 이내). 반면 신뢰도 점수가 40% 등으로 낮아지면 점수 범위도 좁아집니다(대략 4% 이내).

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>학습 API를 사용 하 여 명시적 피드백을 제공 하는 방법

어떤 답이 가장 적합 한지에 대 한 명시적인 피드백을 QnA Maker 하는 것이 중요 합니다. 가장 좋은 답은 다음과 같이 결정 됩니다.

* 사용자 의견. 답변 중 하나를 선택 합니다.
* 허용 가능한 점수 범위를 결정 하는 등의 비즈니스 논리
* 사용자 피드백과 비즈니스 논리의 조합입니다.

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>런타임 버전을 업그레이드 하 여 활성 학습 사용

활성 학습은 런타임 버전 4.4.0 이상에서 지원됩니다. 기술 자료가 이전 버전에서 작성된 경우 이 기능을 사용하려면 [런타임을 업그레이드](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)합니다.

## <a name="turn-on-active-learning-to-see-suggestions"></a>활성 학습을 사용 하 여 제안 보기

활성 학습은 기본적으로 해제되어 있습니다. 제안된 질문을 확인하려면 활성 학습을 설정합니다. 활성 학습을 켠 후에는 클라이언트 앱의 정보를 QnA Maker으로 보내야 합니다. 자세한 내용은 [GenerateAnswer를 사용 하는 아키텍처 흐름 및 봇에서 Api 학습](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)을 참조 하세요.

1. **게시** 를 선택 하 여 기술 자료를 게시 합니다. 활성 학습 쿼리는 GenerateAnswer API 예측 엔드포인트 에서만 수집 됩니다. QnA Maker 포털에서 테스트 창에 대 한 쿼리는 활성 학습에 영향을 주지 않습니다.

1. QnA Maker 포털에서 활성 학습을 설정 하려면 오른쪽 위 모서리로 이동 하 여 사용자의 **이름을**선택 하 고 [**서비스 설정**](https://www.qnamaker.ai/UserSettings)으로 이동 합니다.

    ![서비스 설정 페이지에서 활성 학습의 제안 된 질문을 사용 하도록 설정 합니다. 오른쪽 위에 있는 메뉴에서 사용자 이름을 선택한 다음 서비스 설정을 선택 합니다.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker 서비스를 찾은 다음 **활성 학습**을 설정 상태로 전환합니다.

    > [!div class="mx-imgBorder"]
    > [![서비스 설정 페이지에서 활성 학습 기능을 설정/해제 합니다. 기능을 설정/해제할 수 없는 경우 서비스를 업그레이드 해야 할 수 있습니다.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 이전 이미지의 정확한 버전은 예제로만 표시 됩니다. 버전이 다를 수 있습니다.

    **활성 학습** 을 사용 하도록 설정 하면 기술 자료에서 사용자가 제출한 질문에 따라 정기적인 간격으로 새로운 질문을 제안 합니다. 설정을 다시 전환하여 **활성 학습**을 사용하지 않도록 설정할 수 있습니다.

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>기술 자료에서 활성 학습 제안 수락

활성 학습은 제안을 승인한 후 기술 자료 또는 Search Service를 변경 하 고 저장 및 학습 합니다. 제안을 승인 하면 대체 질문으로 추가 됩니다.

1. 제안 된 질문을 보려면 기술 자료 **편집** 페이지에서 **보기 옵션**을 선택한 다음 **활성 학습 제안 표시**를 선택 합니다.

    [포털의 편집 섹션에서 활성 학습의 새로운 질문을 확인 하려면 제안 표시를 선택 합니다. ![](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 질문과 대답 쌍으로 기술 자료를 필터링 하 여 제안 **별로 필터링**을 선택 하 여 제안을 표시 합니다.

    [제안 별 필터 설정/해제를 사용 하 여 활성 학습의 권장 되는 질문에 대 한 대체 유형만 표시 합니다. ![](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 각 QnA 쌍은 질문을 수락 하거나 `x` 제안을 거부 하기 위해 확인 표시를 사용 하는 새로운 질문 (`✔`)을 제안 합니다. 질문을 추가하려면 확인 표시를 선택합니다.

    [녹색 확인 표시 또는 빨간색 삭제 표시를 선택 하 여 활성 학습의 제안 된 질문의 대안을 선택 하거나 거부할 ![.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    상황별 도구 모음에서 모두 **추가** 또는 **모두 거부** 를 선택 하 여 _모든 제안을_ 추가 하거나 삭제할 수 있습니다.

1. **저장 및 학습**을 선택하여 기술 자료에 변경 내용을 저장합니다.

1. **게시** 를 선택 하 여 [generateanswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)에서 변경 내용을 사용할 수 있도록 허용 합니다.

    5 개 이상의 유사한 쿼리가 클러스터링 되 면 30 분 마다 QnA Maker는 허용 또는 거부에 대 한 대체 질문을 제안 합니다.


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>GenerateAnswer를 사용 하 고 봇에서 Api를 학습 하기 위한 아키텍처 흐름

봇 또는 다른 클라이언트 응용 프로그램은 다음 아키텍처 흐름을 사용 하 여 활성 학습을 사용 해야 합니다.

* Bot `top` 속성을 사용 하 여 [기술 자료에서](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) GENERATEANSWER API를 사용 하 여 답변을 확인 하 고 많은 대답을 가져옵니다.
* 봇은 명시적인 피드백을 결정 합니다.
    * [사용자 고유의 사용자 지정 비즈니스 논리](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)를 사용 하 여 낮은 점수를 필터링 합니다.
    * 봇 또는 클라이언트 응용 프로그램에서 사용자에 게 가능한 답변 목록을 표시 하 고 사용자가 선택한 대답을 가져옵니다.
* Bot [선택한 답변을](#bot-framework-sample-code) [학습 API](#train-api)를 사용 하 여 QnA Maker 다시 보냅니다.


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>GenerateAnswer 요청에서 top 속성을 사용 하 여 여러 일치 대답을 가져옵니다.

답변에 대 한 QnA Maker 질문을 제출할 때 JSON 본문의 `top` 속성은 반환할 대답 수를 설정 합니다.

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>비즈니스 논리와 함께 점수 속성을 사용 하 여 사용자 표시에 대 한 답변 목록 가져오기

클라이언트 응용 프로그램 (예: 채팅 봇)이 응답을 받으면 상위 3 개의 질문이 반환 됩니다. `score` 속성을 사용 하 여 점수 간의 유사성을 분석 합니다. 이러한 근접 범위는 사용자 고유의 비즈니스 논리에 의해 결정 됩니다.

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>질문의 점수가 비슷할 때의 클라이언트 애플리케이션 추가 작업

클라이언트 응용 프로그램은 사용자에 게 가장 적합 한 _단일 질문_ 을 선택할 수 있는 옵션과 함께 질문을 표시 합니다.

사용자가 기존 질문 중 하나를 선택 하면 클라이언트 응용 프로그램은 QnA Maker의 학습 API를 사용 하 여 사용자의 선택 항목을 사용자 의견으로 보냅니다. 이 피드백은 활성 학습 피드백 루프를 완료 합니다.

## <a name="train-api"></a>학습 API

활성 학습 피드백은 학습 API POST 요청을 통해 QnA Maker 전송 됩니다. API 시그니처는 다음과 같습니다.

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 요청 속성|이름|유형|용도|
|--|--|--|--|
|URL 경로 매개 변수|기술 자료 ID|문자열|기술 자료를 위한 GUID입니다.|
|사용자 지정 하위 도메인|QnAMaker 리소스 이름|문자열|리소스 이름은 QnA Maker에 대 한 사용자 지정 하위 도메인으로 사용 됩니다. 이 기능은 기술 자료를 게시 한 후 설정 페이지에서 사용할 수 있습니다. `host`으로 나열 됩니다.|
|헤더|콘텐츠 형식|문자열|API로 전송되는 본문의 미디어 유형입니다. 기본값은 `application/json`입니다.|
|헤더|권한 부여|문자열|엔드포인트 키(EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)입니다.|
|본문 게시|JSON 개체|JSON|학습 피드백|

JSON 본문에는 다음과 같은 몇 가지 설정이 있습니다.

|JSON 본문 속성|유형|용도|
|--|--|--|--|
|`feedbackRecords`|array|사용자 의견 목록입니다.|
|`userId`|문자열|제안 된 질문을 수락 하는 사람의 사용자 ID입니다. 사용자 ID 형식은 사용자에 게 있습니다. 예를 들어, 전자 메일 주소는 아키텍처에서 유효한 사용자 ID가 될 수 있습니다. (선택 사항)|
|`userQuestion`|문자열|사용자 쿼리의 정확한 텍스트입니다. 필수 사항입니다.|
|`qnaID`|number|[Generateanswer 응답](metadata-generateanswer-usage.md#generateanswer-response-properties)에 있는 질문의 ID입니다. |

예제 JSON 본문은 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

응답이 성공 하면 204 상태와 JSON 응답 본문이 반환 되지 않습니다.

### <a name="batch-many-feedback-records-into-a-single-call"></a>여러 피드백 레코드를 단일 호출로 일괄 처리

Bot와 같은 클라이언트 쪽 응용 프로그램에서 데이터를 저장 한 다음 `feedbackRecords` 배열의 단일 JSON 본문에서 많은 레코드를 보낼 수 있습니다.

예제 JSON 본문은 다음과 같습니다.

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework 샘플 코드

사용자의 쿼리를 활성 학습에 사용 해야 하는 경우 bot framework 코드에서 학습 API를 호출 해야 합니다. 작성 하는 코드에는 두 가지가 있습니다.

* 쿼리가 활성 학습에 사용 되어야 하는지 결정
* 활성 학습을 위한 QnA Maker의 학습 API로 쿼리를 다시 보냅니다.

[Azure Bot 샘플](https://aka.ms/activelearningsamplebot)에서 이러한 활동은 모두 프로그래밍 되었습니다.

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>봇 C# Framework 4.x를 사용 하는 학습 API에 대 한 예제 코드

다음 코드에서는 학습 API를 사용 하 여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다. 이 [전체 코드 샘플](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore) 은 GitHub에서 사용할 수 있습니다.

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="endpoint">Endpoint URI of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string endpoint, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = endpoint + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>봇 Framework 4.x를 사용 하는 학습 API에 대 한 예제 node.js 코드

다음 코드에서는 학습 API를 사용 하 여 QnA Maker에 정보를 다시 보내는 방법을 보여 줍니다. 이 [전체 코드 샘플](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs) 은 GitHub에서 사용할 수 있습니다.

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);

            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>활성 학습은 내보낸 기술 자료에 저장 됩니다.

앱에서 활성 학습을 사용 하도록 설정 하 고 앱을 내보내는 경우 tsv 파일의 `SuggestedQuestions` 열은 활성 학습 데이터를 유지 합니다.

`SuggestedQuestions` 열은 암시적, `autosuggested`및 명시적인 `usersuggested` 피드백 정보에 대 한 JSON 개체입니다. 사용자가 제출한 단일 사용자 `help` 질문에 대 한이 JSON 개체의 예는 다음과 같습니다.

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

REST 또는 언어 기반 Sdk를 사용 하 여 이러한 변경 내용을 검토 하려면 다운로드 변경 API를 사용할 수도 있습니다.
* [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fc)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.alterationsextensions.getasync?view=azure-dotnet)


이 앱을 다시 가져오는 경우 활성 학습은 계속 해 서 정보를 수집 하 고 기술 자료에 대 한 제안을 권장 합니다.



## <a name="best-practices"></a>모범 사례

활성 학습 사용 시의 모범 사례는 [모범 사례](../Concepts/best-practices.md#active-learning)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GenerateAnswer API와 함께 메타 데이터 사용](metadata-generateanswer-usage.md)
