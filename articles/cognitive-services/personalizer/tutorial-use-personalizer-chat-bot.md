---
title: 채팅 봇에서 Personalizer 사용 - Personalizer
description: Personalizer 반복을 사용하는 C# .NET 채팅 봇을 사용자 지정하여 작업(기능 포함) 및 컨텍스트 기능에 따라 사용자에게 올바른 콘텐츠를 제공합니다.
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ee037632b068b0d8d2f3ed3b6ea7a83d157c5083
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935656"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>자습서: .NET 채팅 봇에서 Personalizer 사용

Personalizer 반복을 사용하는 C# .NET 채팅 봇을 사용하여 사용자에게 올바른 콘텐츠를 제공합니다. 이 채팅 봇은 사용자에게 특정 커피 또는 차를 제안합니다. 사용자는 해당 제안을 수락하거나 거부할 수 있습니다. 그러면 다음 제안을 더 적절하게 만드는 데 도움이 되는 Personalizer 정보가 제공됩니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure 리소스 설정
> * 봇 구성 및 실행
> * 봇 에뮬레이터를 사용하여 봇과 상호 작용
> * 봇에서 Personalizer를 사용하는 위치 및 방법 이해


## <a name="how-does-the-chat-bot-work"></a>채팅 봇의 작동 방식

채팅 봇은 일반적으로 사용자와 주고받는 대화입니다. 이 특정 채팅 봇은 Personalizer를 사용하여 사용자에게 제공할 최상의 작업(커피 또는 차)을 선택합니다. Personalizer는 보충 학습을 사용하여 해당 항목을 선택합니다.

채팅 봇은 대화의 턴을 관리해야 합니다. 채팅 봇은 [Bot Framework](https://github.com/microsoft/botframework-sdk)를 사용하여 봇 아키텍처와 대화를 관리하고, 인지 서비스인 [LUIS(Language Understanding)](../LUIS/index.yml)를 사용하여 사용자의 자연어 의도를 이해합니다.

채팅 봇은 요청에 응답하는 데 사용할 수 있는 특정 경로(`http://localhost:3978/api/messages`)가 있는 웹 사이트입니다. 봇을 로컬로 개발하는 동안 봇 에뮬레이터를 사용하여 실행 중인 채팅 봇과 시각적으로 상호 작용할 수 있습니다.

### <a name="user-interactions-with-the-bot"></a>봇과의 사용자 상호 작용

텍스트 쿼리를 입력할 수 있는 간단한 채팅 봇입니다.

|사용자가 입력하는 텍스트|봇에서 응답하는 텍스트|응답 텍스트를 결정하기 위해 봇에서 수행하는 작업에 대한 설명|
|--|--|--|
|입력 텍스트 없음 - 봇에서 대화를 시작합니다.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|봇에서 지침 텍스트를 사용하여 대화를 시작하고 컨텍스트(`Tuesday`, `Snowy`)를 알려줍니다.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|LUIS를 사용하여 쿼리 의도를 확인한 다음, 커피 및 차 항목의 메뉴 선택 항목을 표시합니다. 작업의 기능입니다. |
|`What do you suggest`|`How about Latte?`|LUIS를 사용하여 쿼리 의도를 확인한 다음, **순위 API**를 호출하고, 상위 선택 항목을 질문(`How about {response.RewardActionId}?`)으로 표시합니다. 또한 설명을 위해 JSON 호출 및 응답을 표시합니다.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|LUIS를 사용하여 쿼리 의도를 확인한 다음, 보상(`1`)을 사용하여 **보상 API**를 호출하고, 설명을 위해 JSON 호출 및 응답을 표시합니다.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|LUIS를 사용하여 쿼리 의도를 확인한 다음, 보상(`0`)을 사용하여 **보상 API**를 호출하고, 설명을 위해 JSON 호출 및 응답을 표시합니다.|
|`Reset`|지침 텍스트를 반환합니다.|LUIS를 사용하여 쿼리 의도를 확인한 다음, 지침 텍스트를 표시하고, 컨텍스트를 다시 설정합니다.|


### <a name="personalizer-in-this-bot"></a>이 봇의 Personalizer

이 채팅 봇은 Personalizer를 사용하여 _작업_(일부 콘텐츠 형식) 및 컨텍스트 기능의 목록에 따라 상위 작업(특정 커피 또는 차)을 선택합니다.

봇에서 컨텍스트 기능과 함께 작업 목록을 Personalizer 반복에 보냅니다. Personalizer에서 봇에 표시되는 가장 적합한 단일 작업을 봇에 반환합니다.

이 자습서에서 **작업**은 다음과 같은 커피와 차의 유형입니다.

|커피|차|
|--|--|
|카푸치노<br>에스프레소<br>라떼<br>모카|녹차<br>로이보스|

**순위 API:** Personalizer에서 사용자의 작업을 이해하는 데 도움이 되도록 봇에서 각 순위 API 요청을 사용하여 다음을 보냅니다.

* _기능이 포함된_ 작업
* 컨텍스트 기능

모델의 **기능**은 채팅 봇 사용자 기반의 멤버 간에 집계(그룹화)할 수 있는 작업 또는 컨텍스트에 대한 정보입니다. 기능은 개별적으로 구체적(예: 사용자 ID)이거나 매우 구체적(예: 정확한 하루 중 시간)이지 _않습니다_.

기능은 작업을 모델의 현재 컨텍스트에 정렬하는 데 사용됩니다. 모델은 작업, 컨텍스트 및 학습에 따른 결정을 수행할 수 있도록 하는 기능에 대한 Personalizer의 과거 지식을 나타냅니다.

기능이 포함된 모델은 Azure Portal에서 **모델 업데이트 빈도** 설정에 기반한 일정에 따라 업데이트됩니다.

기능은 기술 아키텍처의 스키마 또는 모델에 적용하는 것과 동일한 계획 및 설계를 선택해야 합니다. 기능 값은 비즈니스 논리 또는 타사 시스템을 사용하여 설정할 수 있습니다.

> [!CAUTION]
> 이 애플리케이션의 기능은 데모용이며, 웹앱에서 사용자의 사용 사례에 사용할 수 있는 최상의 기능이 아닐 수도 있습니다.

#### <a name="action-features"></a>작업 기능

각 작업(콘텐츠 항목)에는 커피 또는 차 항목을 구분하는 데 도움이 되는 기능이 있습니다.

기능이 Azure Portal에서 반복 구성의 일부로 구성되지 않았습니다. 대신 각 순위 API 호출과 함께 JSON 개체로 보내집니다. 이렇게 하면 시간에 따라 작업과 기능이 유연하게 확장, 변경 및 축소될 수 있으므로 Personalizer에서 추세를 따를 수 있습니다.

커피와 차의 기능은 다음과 같습니다.

* 커피 원두의 원산지(예: 케냐 및 브라질)
* 커피 또는 차가 유기농인가요?
* 라이트 또는 다크 커피 로스트

커피에는 위의 목록에 있는 세 가지 기능이 있지만, 차에는 하나의 기능만 있습니다. 작업에 적합한 기능만 Personalizer에 전달합니다. 기능이 작업에 적용되지 않는 경우 해당 기능에 대한 빈 값을 전달하지 마세요.

#### <a name="context-features"></a>컨텍스트 기능

컨텍스트 기능은 Personalizer에서 디스플레이 디바이스, 사용자, 위치 및 사용 사례와 관련된 기타 기능과 같은 환경의 컨텍스트를 이해하는 데 도움이 됩니다.

이 채팅 봇의 컨텍스트는 다음과 같습니다.

* 날씨 유형(눈, 비, 맑음)
* 요일

이 채팅 봇의 경우 기능을 임의로 선택할 수 있습니다. 실제 봇에서는 실제 데이터를 컨텍스트 기능에 사용합니다.

### <a name="design-considerations-for-this-bot"></a>이 봇의 디자인 고려 사항

이 대화에 대해 주의해야 할 몇 가지 사항이 있습니다.
* **봇 상호 작용**: 간단한 사용 사례에서 순위 및 보상을 시연하므로 대화가 매우 간단합니다. Bot Framework SDK 또는 Emulator의 전체 기능을 시연하지는 않습니다.
* **Personalizer**: 사용법을 시뮬레이션하기 위해 기능이 임의로 선택됩니다. 프로덕션 Personalizer 시나리오에서는 기능을 임의로 선택하지 마세요.
* **LUIS(Language Understanding)** : LUIS 모델의 몇 가지 발화 예제는 이 샘플에만 해당됩니다. 프로덕션 LUIS 애플리케이션에서는 이러한 몇 가지 발화 예제를 사용하지 마세요.


## <a name="install-required-software"></a>필수 소프트웨어 설치
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). 다운로드 가능한 샘플 리포지토리에는 .NET Core CLI를 사용하려는 경우에 대한 지침이 포함되어 있습니다.
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator)는 봇 개발자가 localhost에서 봇을 테스트 및 디버그하거나 터널을 통해 원격으로 실행할 수 있는 데스크톱 애플리케이션입니다.

## <a name="download-the-sample-code-of-the-chat-bot"></a>채팅 봇의 샘플 코드 다운로드

채팅 봇은 Personalizer 샘플 리포지토리에서 사용할 수 있습니다. 리포지토리를 복제하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip)한 다음, Visual Studio 2019를 사용하여 `/samples/ChatbotExample` 디렉터리에서 샘플을 엽니다.

리포지토리를 복제하려면 Bash 셸(터미널)에서 다음 Git 명령을 사용합니다.

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Personalizer 및 LUIS 리소스 만들기 및 구성

### <a name="create-azure-resources"></a>Azure 리소스 만들기

이 채팅 봇을 사용하려면 Personalizer 및 LUIS(Language Understanding)용 Azure 리소스를 만들어야 합니다.

* [LUIS 리소스를 만듭니다](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal). 작성 리소스 및 예측 리소스가 모두 필요하므로 만들기 단계에서 **모두**를 선택합니다.
* [Personalizer 리소스를 만든](how-to-create-resource.md) 다음, Azure Portal에서 키와 엔드포인트를 복사합니다. 이러한 값은 .NET 프로젝트의 `appsettings.json` 파일에서 설정해야 합니다.

### <a name="create-luis-app"></a>LUIS 앱 만들기

LUIS를 처음 사용하는 경우 [로그인](https://www.luis.ai)하여 계정을 즉시 마이그레이션해야 합니다. 새 리소스를 만들 필요가 없습니다. 대신 이 자습서의 이전 섹션에서 만든 리소스를 선택합니다.

1. 새 LUIS 애플리케이션을 만들려면 [LUIS 포털](https://www.luis.ai)에서 구독 및 작성 리소스를 선택합니다.
1. 그런 다음, 여전히 동일한 페이지에서 **+ 대화용 새 앱**을 선택하고, **JSON으로 가져오기**를 선택합니다.
1. 팝업 대화 상자에서 **파일 선택**, `/samples/ChatbotExample/CognitiveModels/coffeebot.json` 파일을 차례로 선택합니다. `Personalizer Coffee bot`이라는 이름을 입력합니다.
1. LUIS 포털의 오른쪽 위 탐색 영역에서 **학습** 단추를 선택합니다.
1. **게시** 단추를 선택하여 앱을 예측 런타임용 **프로덕션 슬롯**에 게시합니다.
1. **관리**, **설정**을 차례로 선택합니다. **앱 ID**의 값을 복사합니다. 이 값은 .NET 프로젝트의 `appsettings.json` 파일에서 설정해야 합니다.
1. 여전히 **관리** 섹션에서 **Azure 리소스**를 선택합니다. 그러면 앱에 연결된 리소스가 표시됩니다.
1. **예측 리소스 추가**를 선택합니다. 팝업 대화 상자에서 구독 및 이 자습서의 이전 섹션에서 만든 예측 리소스를 선택한 다음, **완료**를 선택합니다.
1. **기본 키** 및 **엔드포인트 URL**의 값을 복사합니다. 이러한 값은 .NET 프로젝트의 `appsettings.json` 파일에서 설정해야 합니다.

### <a name="configure-bot-with-appsettingsjson-file"></a>appsettings.json 파일을 사용하여 봇 구성

1. Visual Studio 2019에서 채팅 봇 솔루션 파일(`ChatbotSamples.sln`)을 엽니다.
1. 프로젝트의 루트 디렉터리에서 `appsettings.json`을 엽니다.
1. 이 자습서의 이전 섹션에서 복사한 5가지 설정을 모두 설정합니다.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>봇 빌드 및 실행

`appsettings.json`이 구성되었으면 채팅 봇을 빌드하고 실행할 준비가 되었습니다. 이렇게 하면 브라우저에서 실행 중인 웹 사이트(`http://localhost:3978`)가 열립니다.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="채팅 봇 웹 사이트를 표시하는 브라우저의 스크린샷":::

자습서에서 봇이 수행하는 작업을 설명하므로 봇과 상호 작용할 수 있도록 웹 사이트를 계속 실행합니다.


## <a name="set-up-the-bot-emulator"></a>봇 에뮬레이터 설정

1. [봇 에뮬레이터]를 열고, **봇 열기**를 선택합니다.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="봇 에뮬레이터 시작 화면의 스크린샷":::


1. 다음 **봇 URL**을 사용하여 봇을 구성한 다음, **연결**을 선택합니다.

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="봇 에뮬레이터 봇 열기 설정의 스크린샷":::

    에뮬레이터에서 채팅 봇에 연결하고, 로컬 개발에 유용한 로깅 및 디버그 정보와 함께 지침 텍스트를 표시합니다.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="첫 번째 대화 턴의 봇 에뮬레이터에 대한 스크린샷":::

## <a name="use-the-bot-in-the-bot-emulator"></a>봇 에뮬레이터에서 봇 사용

1. `I would like to see the menu`를 입력하여 메뉴를 표시하도록 요청합니다. 채팅 봇에서 항목을 표시합니다.
1. `Please suggest a drink for me.`를 입력하여 봇에서 항목을 제안하도록 합니다. 그러면 에뮬레이터에서 전체 JSON을 볼 수 있도록 채팅 창에 순위 요청 및 응답을 표시합니다. 그리고 봇에서 `How about Latte?`와 같이 제안합니다.
1. 해당 제안을 원한다고 대답합니다(`I like it.`). 이는 Personalizer의 상위 순위 선택 항목을 수락함을 의미합니다. 에뮬레이터에서 보상 점수가 1인 보상 요청과 응답을 채팅 창에 표시하므로 전체 JSON을 볼 수 있습니다. 그리고 봇에서 응답합니다(`That’s great! I’ll keep learning your preferences over time.` 및 `Would you like to get a new suggestion or reset the simulated context to a new day?`).

    선택 항목에 대해 `no`로 응답하면 0의 보상 점수를 Personalizer에 보냅니다.


## <a name="understand-the-net-code-using-personalizer"></a>Personalizer를 사용하여 .NET 코드 이해

.NET 솔루션은 간단한 봇 프레임워크 채팅 봇입니다. Personalizer 관련 코드는 다음 폴더에 있습니다.
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` - 봇과 Personalizer 간의 상호 작용에 대한 파일입니다.
* `/samples/ChatbotExample/ReinforcementLearning` - Personalizer 모델에 대한 작업 및 기능을 관리합니다.
* `/samples/ChatbotExample/Model` - Personalizer 작업과 기능 및 LUIS 의도에 대한 파일입니다.

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - Personalizer 작업

`PersonalizerChatbot` 클래스는 `Microsoft.Bot.Builder.ActivityHandler`에서 파생됩니다. 여기에는 대화 흐름을 관리하는 세 가지 속성 및 메서드가 있습니다.

> [!CAUTION]
> 이 자습서의 코드를 복사하지 마세요. [Personalizer 샘플 리포지토리](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)의 샘플 코드를 사용하세요.

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

`Send` 접두사가 있는 메서드는 봇 및 LUIS와의 대화를 관리합니다. `ChooseRankAsync` 및 `RewardAsync` 메서드는 Personalizer와 상호 작용합니다.

#### <a name="calling-rank-api-and-display-results"></a>순위 API 호출 및 결과 표시

`ChooseRankAsync` 메서드는 기능 및 컨텍스트 기능이 포함된 작업을 수집하여 Personalizer의 순위 API에 보낼 JSON 데이터를 작성합니다.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>보상 API 호출 및 결과 표시

`RewardAsync` 메서드는 점수를 확인하여 Personalizer의 보상 API에 보낼 JSON 데이터를 작성합니다. 점수는 사용자 텍스트에서 식별된 LUIS 의도에 따라 결정되며 `OnTurnAsync` 메서드에서 보냅니다.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>봇 디자인 고려 사항

이 샘플은 봇에서 Personalizer의 간단한 엔드투엔드 솔루션을 시연하기 위한 것입니다. 사용자의 사용 사례는 더 복잡할 수 있습니다.

프로덕션 봇에서 Personalizer를 사용하려는 경우 다음을 계획합니다.
* 순위를 선택해야 할 _때마다_ Personalizer에 실시간으로 액세스합니다. 순위 API는 일괄 처리하거나 캐시할 수 없습니다.  보상 호출은 별도의 프로세스로 지연하거나 오프로드할 수 있으며, 시간이 지정된 기간 내에 보상을 반환하지 않으면 이벤트에 대한 기본 보상 값이 설정됩니다.
* 사용 사례 기반의 보상 계산: 이 예제에서는 범위가 없고 음수 값이 아닌 점수인 0과 1의 두 가지 보상을 보여 주었습니다. 시스템에서 더 세분화된 점수 매기기를 수행해야 합니다.
* 봇 채널: 이 샘플은 단일 채널을 사용하지만, 둘 이상의 채널을 사용하거나 단일 채널에서 봇의 변형을 사용하려는 경우 Personalizer 모델의 컨텍스트 기능의 일부로 간주해야 할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하면 다음 리소스를 정리합니다.

* 프로젝트 샘플 디렉터리 삭제
* Azure Portal에서 Personalizer 및 LUIS 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계
* [Personalizer의 작동 원리](how-personalizer-works.md)
* [기능](concepts-features.md): 작업 및 컨텍스트에서 사용하는 기능에 대한 개념 알아보기
* [보상](concept-rewards.md): 보상 계산에 대해 알아보기
