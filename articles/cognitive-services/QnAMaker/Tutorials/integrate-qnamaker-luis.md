---
title: LUIS 및 QnAMaker - 봇 통합
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료가 확장되면 단일 모놀리식 집합으로 유지 관리하기가 어려워지며, 기술 자료를 보다 작은 논리적 청크로 분할해야 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e1ea234bde96ce84259841bbc592bf6373bc639
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802804"
---
# <a name="use-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>QnA Maker 및 LUIS와 함께 봇을 사용하여 기술 자료 배포
QnA Maker 기술 자료가 확장되면 단일 모놀리식 집합으로 유지 관리하기가 어려워지며, 기술 자료를 보다 작은 논리적 청크로 분할해야 합니다.

QnA Maker에서 간단하게 여러 기술 자료를 만들 수 있지만, 들어오는 질문을 적절한 기술 자료로 라우팅하기 위한 논리가 필요합니다. LUIS를 사용하여 이 작업을 수행할 수 있습니다.

이 문서에서는 Bot Framework v3 SDK를 사용합니다. 이 정보의 Bot Framework v4 SDK 버전에 관심이 있는 경우 이 [Bot Framework 문서](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp)를 참조하세요.

## <a name="architecture"></a>아키텍처

![Language Understanding 아키텍처로 QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

위의 시나리오에서 QnA Maker는 먼저 LUIS 모델에서 들어오는 질문의 의도를 가져온 다음, 이 의도를 사용하여 올바른 QnA Maker 기술 자료로 라우팅합니다.

## <a name="create-a-luis-app"></a>LUIS 앱 만들기

1. [LUIS](https://www.luis.ai/) 포털에 로그인합니다.
1. [앱을 만듭니다](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. 각 QnA Maker 기술 자료에 대해 [의도를 추가](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents)합니다. 예제 발언은 QnA Maker 기술 자료의 질문과 일치해야 합니다.
1. [LUIS 앱을 학습](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train)하고 [LUIS 앱을 게시](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp)합니다.
1. **관리** 섹션에서 LUIS 앱 ID, LUIS 엔드포인트 키 및 [사용자 지정 도메인 이름을](../../cognitive-services-custom-subdomains.md)적어 둡니다. 나중에 이러한 값이 필요합니다. 

## <a name="create-qna-maker-knowledge-bases"></a>QnA Maker 기술 자료 만들기

1. [QnA Maker](https://qnamaker.ai)에 로그인합니다.
1. LUIS 앱의 각 의도에 대해 기술 자료를 [만듭니다](https://www.qnamaker.ai/Create).
1. 기술 자료를 테스트하고 게시합니다. 각 KB를 게시할 때 KB ID, 리소스 이름 ( _azurewebsites.net/qnamaker_이전 사용자 지정 하위 도메인) 및 권한 부여 엔드포인트 키를 적어 둡니다. 나중에 이러한 값이 필요합니다. 

    이 문서에서는 기술 자료가 모두 동일한 Azure QnA Maker 구독에서 생성된다고 가정합니다.

    ![QnA Maker HTTP 요청](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>웹앱 봇

1. LUIS 앱을 자동으로 포함 하는 ["기본" 웹 앱 봇을 만듭니다](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0) . 프로그래밍 C# 언어를 선택 합니다.

1. 웹앱 봇이 만들어지면 Azure Portal에서 해당 웹앱 봇을 선택합니다.
1. 웹앱 봇 서비스 탐색에서 **애플리케이션 설정**을 선택하고 아래로 스크롤하여 사용 가능한 설정의 **애플리케이션 설정** 섹션으로 이동합니다.
1. **LuisAppId**를 이전 섹션에서 만든 LUIS 앱의 값으로 변경한 후 **저장**을 선택합니다.


## <a name="change-code-in-basicluisdialogcs"></a>BasicLuisDialog.cs에서 코드 변경
1. Azure Portal에서 웹앱 봇 탐색의 **봇 관리** 섹션에서 **빌드**를 선택합니다.
2. **온라인 코드 편집기 열기**를 선택합니다. 새 브라우저 탭에서 온라인 편집 환경이 열립니다. 
3. **WWWROOT** 섹션에서 **Dialogs** 디렉터리를 선택하고 **BasicLuisDialog.cs**를 엽니다.
4. **BasicLuisDialog.cs** 파일 맨 위에 종속성을 추가합니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. 아래 클래스를 추가하여 QnA Maker 응답을 deserialize합니다.

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. 다음 클래스를 추가하여 QnA Maker 서비스에 대한 HTTP 요청을 수행합니다. **인증** 헤더 값에는 `EndpointKey` 단어와 공백이 포함되어 있습니다. JSON 결과가 이전 클래스로 deserialize되고 첫 번째 응답이 반환됩니다.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. BasicLuisDialog 클래스를 수정합니다. 각 LUIS 의도에는 **LuisIntent**로 데코레이트된 메서드가 있습니다. 데코레이션에 대한 매개 변수는 실제 LUIS 의도 이름입니다. 가독성 및 유지 관리를 위해 데코레이트된 메서드 이름으로 LUIS 의도 이름을 사용하는 것이 _좋지만_ 디자인 타임이나 런타임에 동일할 필요는 없습니다.  

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all KBs are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";
        
        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>봇 빌드
1. 코드 편집기에서 `build.cmd`를 마우스 오른쪽 단추로 클릭하고 **콘솔에서 실행**을 선택합니다.

    ![콘솔에서 실행](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. 코드 보기는 빌드의 진행 상황 및 결과를 표시하는 터미널 창으로 바뀝니다.

    ![콘솔 빌드](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>봇 테스트
Azure Portal에서 **웹 채팅에서 테스트**를 선택하여 봇을 테스트합니다. 다양한 의도의 메시지를 입력하여 해당하는 기술 자료의 응답을 가져옵니다.

![웹 채팅 테스트](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker를 위한 비즈니스 연속성 플랜 만들기](../How-To/business-continuity-plan.md)
