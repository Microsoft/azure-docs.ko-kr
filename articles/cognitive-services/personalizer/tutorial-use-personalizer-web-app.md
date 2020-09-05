---
title: 웹앱 사용 - Personalizer
description: Personalizer 반복을 사용하는 C# .NET 웹앱을 사용자 지정하여 작업(기능 포함) 및 컨텍스트 기능에 따라 사용자에게 올바른 콘텐츠를 제공합니다.
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e9ce4c433a038008b1ffd75dc6c4b2f9d0b57fde
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935622"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>자습서: .NET 웹앱에 Personalizer 추가

Personalizer 반복을 사용하는 C# .NET 웹앱을 사용자 지정하여 작업(기능 포함) 및 컨텍스트 기능에 따라 사용자에게 올바른 콘텐츠를 제공합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * Personalizer 키 및 엔드포인트 설정
> * 기능 수집
> * 순위 및 보상 API 호출
> * _rewardActionId_로 지정된 상위 작업 표시



## <a name="select-the-best-content-for-a-web-app"></a>웹앱에 가장 적합한 콘텐츠 선택

표시할 단일 상위 항목(rewardActionId)에 개인 맞춤해야 하는 _작업_(일부 콘텐츠 형식) 목록이 웹 페이지에 있는 경우 웹앱에서 Personalizer를 사용해야 합니다. 작업 목록의 예로 뉴스 기사, 단추 배치 위치 및 제품 이름에 대한 단어 선택이 있습니다.

컨텍스트 기능과 함께 작업 목록을 Personalizer 반복에 보냅니다. Personalizer는 최상의 단일 작업을 선택한 다음, 해당 작업을 웹앱에 표시합니다.

이 자습서에서 작업은 다음과 같은 음식 유형입니다.

* 파스타
* 아이스크림
* 주스
* 샐러드
* 팝콘
* 커피
* 스프

Personalizer에서 작업을 이해할 수 있도록 지원하기 위해 각 순위 API 요청을 사용하여 _기능이 포함된 작업_ 및 _콘텍스트 기능_을 모두 보냅니다.

모델의 **기능**은 웹앱 사용자 기반의 멤버 간에 집계(그룹화)할 수 있는 작업 또는 컨텍스트에 대한 정보입니다. 기능은 개별적으로 구체적(예: 사용자 ID)이거나 매우 구체적(예: 정확한 하루 중 시간)이지 _않습니다_.

### <a name="actions-with-features"></a>기능이 포함된 작업

각 작업(콘텐츠 항목)에는 음식 항목을 구별하는 데 도움이 되는 기능이 있습니다.

기능이 Azure Portal에서 반복 구성의 일부로 구성되지 않았습니다. 대신 각 순위 API 호출과 함께 JSON 개체로 보내집니다. 이렇게 하면 시간에 따라 작업과 기능이 유연하게 확장, 변경 및 축소될 수 있으므로 Personalizer에서 추세를 따를 수 있습니다.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>컨텍스트 기능

컨텍스트 기능은 Personalizer에서 작업의 컨텍스트를 이해하는 데 도움이 됩니다. 이 애플리케이션 샘플에 대한 컨텍스트에는 다음이 포함됩니다.

* 시간(하루 중) - 오전, 오후, 저녁, 밤
* 사용자의 취향 선호도 - 짠맛, 단맛, 쓴맛, 신맛 또는 자극적인 맛
* 브라우저의 컨텍스트 - 사용자 에이전트, 지리적 위치, 참조 페이지

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>웹앱에서 Personalizer를 어떻게 사용할까요?

웹앱은 Personalizer를 사용하여 음식 선택 목록에서 최상의 작업을 선택합니다. 이를 위해 각 순위 API 호출과 함께 다음 정보를 보냅니다.
* 기능(예: `taste` 및 `spiceLevel`)이 포함된 **작업**
* **컨텍스트** 기능(예: 하루 중 `time`, 사용자의 `taste` 선호도, 브라우저의 사용자 에이전트 정보 및 컨텍스트 기능)
* **제외하는 작업**(예: 주스)
* **eventId**(순위 API에 대한 호출마다 다름)

## <a name="personalizer-model-features-in-a-web-app"></a>웹앱의 Personalizer 모델 기능

Personalizer에는 작업(콘텐츠) 및 현재 컨텍스트(사용자 및 환경)에 대한 기능이 필요합니다. 기능은 작업을 모델의 현재 컨텍스트에 정렬하는 데 사용됩니다. 모델은 작업, 컨텍스트 및 학습에 따른 결정을 수행할 수 있도록 하는 기능에 대한 Personalizer의 과거 지식을 나타냅니다.

기능이 포함된 모델은 Azure Portal에서 **모델 업데이트 빈도** 설정에 기반한 일정에 따라 업데이트됩니다.

> [!CAUTION]
> 이 애플리케이션의 기능은 기능 및 기능 값을 설명하기 위한 것이지만 웹앱에서 사용할 수 있는 최상의 기능은 아닙니다.

### <a name="plan-for-features-and-their-values"></a>기능 및 해당 값에 대한 계획

기능은 기술 아키텍처의 스키마 또는 모델에 적용하는 것과 동일한 계획 및 설계를 선택해야 합니다. 기능 값은 비즈니스 논리 또는 타사 시스템을 사용하여 설정할 수 있습니다. 기능 값은 기능 그룹 또는 기능 클래스에 적용되지 않을 정도로 구체적이지 않아야 합니다.

### <a name="generalize-feature-values"></a>기능 값 일반화

#### <a name="generalize-into-categories"></a>범주로 일반화

이 앱은 `time`을 기능으로 사용하지만, 시간을 `morning`, `afternoon`, `evening` 및 `night`와 같은 범주로 그룹화합니다. 이는 시간 정보를 사용하는 예이지만, `10:05:01 UTC+2`와 같은 매우 구체적인 방법이 아닙니다.

#### <a name="generalize-into-parts"></a>구체적으로 일반화

이 앱은 브라우저의 HTTP 요청 기능을 사용합니다. 이는 모든 데이터가 포함된 매우 구체적인 문자열로 시작합니다. 예를 들어 다음과 같습니다.

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures** 클래스 라이브러리는 이 문자열을 개별 값이 있는 **userAgentInfo** 개체로 일반화합니다. 너무 구체적인 값은 빈 문자열로 설정됩니다. 요청에 대한 컨텍스트 기능을 보내는 경우 다음과 같은 JSON 형식을 사용합니다.

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>웹앱 샘플 사용

브라우저 기반 웹앱 샘플(모든 코드가 제공됨)에는 앱을 실행하기 위해 다음 애플리케이션이 설치되어 있어야 합니다.

다음 소프트웨어를 설치합니다.

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) - 백 엔드 서버 샘플에서 .NET Core를 사용합니다.
* [Node.js](https://nodejs.org/) - 클라이언트/프런트 엔드에서 이 애플리케이션을 사용합니다.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 또는 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) - Visual Studio 2019의 개발자 환경 또는 .NET Core CLI를 사용하여 앱을 만들고 실행합니다.

### <a name="set-up-the-sample"></a>샘플 설정
1. Azure Personalizer 샘플 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. _samples/HttpRequestFeatures_로 이동하여 `HttpRequestFeaturesExample.sln` 솔루션을 엽니다.

    요청되는 경우 Visual Studio에서 Personalizer용 .NET 패키지를 업데이트하도록 허용합니다.

### <a name="set-up-azure-personalizer-service"></a>Azure Personalizer 서비스 설정

1. Azure Portal에서 [Personalizer 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).

1. Azure Portal의 **키 및 엔드포인트** 탭에서 `Endpoint` 및 `Key1` 또는 `Key2`(둘 중 하나가 작동함)를 찾습니다. 이러한 항목은 `PersonalizerServiceEndpoint` 및 `PersonalizerApiKey`입니다.
1. **appsettings.json**에서 `PersonalizerServiceEndpoint`를 입력합니다.
1. 다음 방법 중 하나를 사용하여 `PersonalizerApiKey`를 [앱 비밀](https://docs.microsoft.com/aspnet/core/security/app-secrets)로 구성합니다.

    * .NET Core CLI를 사용하는 경우 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` 명령을 사용할 수 있습니다.
    * Visual Studio를 사용하는 경우 마우스 오른쪽 단추로 프로젝트를 클릭하고, **사용자 비밀 관리** 메뉴 옵션을 선택하여 Personalizer 키를 구성할 수 있습니다. 이렇게 하면 Visual Studio에서 다음과 같이 키를 추가할 수 있는 `secrets.json` 파일이 열립니다.

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>샘플 실행

다음 방법 중 하나를 사용하여 HttpRequestFeaturesExample을 빌드하고 실행합니다.

* Visual Studio 2019: **F5** 키를 누릅니다.
* .NET Core CLI: `dotnet build`, `dotnet run`을 차례로 실행합니다.

웹 브라우저를 통해 순위 요청과 보상 요청을 보내고, 해당 응답과 사용자 환경에서 추출된 http 요청 기능을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행합니다. 브라우저 창이 열려 단일 페이지 애플리케이션을 표시합니다.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Personalizer 반복 데모

1. **새 순위 요청 생성** 단추를 선택하여 순위 API 호출에 대한 새 JSON 개체를 만듭니다. 그러면 작업(기능 포함)과 컨텍스트 기능이 만들어지고 값이 표시되므로 JSON이 표시되는 모양을 확인할 수 있습니다.

    사용자 고유의 향후 애플리케이션의 경우 작업과 기능은 클라이언트, 서버, 이 둘의 혼합 또는 다른 서비스에 대한 호출에서 생성될 수 있습니다.

1. **순위 요청 보내기**를 선택하여 JSON 개체를 서버에 보냅니다. 서버에서 Personalizer 순위 API를 호출합니다. 서버에서 응답을 받고, 높은 순위의 작업을 표시할 수 있도록 클라이언트에 보냅니다.

1. 보상 값을 설정한 다음, **보상 요청 보내기** 단추를 선택합니다. 보상 값을 변경하지 않으면 클라이언트 애플리케이션에서 항상 `1` 값을 Personalizer에 보냅니다.

    > [!div class="mx-imgBorder"]
    > ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행합니다. 브라우저 창이 열려 단일 페이지 애플리케이션을 표시합니다.](./media/tutorial-web-app/reward-score-api-call.png)

    사용자 고유의 향후 애플리케이션의 경우 보상 점수는 서버의 비즈니스 논리와 함께 클라이언트에서 사용자의 동작으로부터 정보를 수집한 후에 생성될 수 있습니다.

## <a name="understand-the-sample-web-app"></a>웹앱 샘플 이해

웹앱 샘플에는 기능 컬렉션을 관리하고 Personalizer 엔드포인트에 대한 HTTP 호출을 보내고 받는 **C# .NET** 서버가 있습니다.

웹앱 샘플은 **녹아웃 프런트 엔드 클라이언트 애플리케이션**을 사용하여 기능을 캡처하고, 단추를 클릭하여 데이터를 .NET 서버에 보내는 것과 같은 사용자 인터페이스 작업을 처리합니다.

다음 섹션에서는 개발자가 Personalizer를 사용하기 위해 이해해야 하는 서버와 클라이언트 부분에 대해 설명합니다.

## <a name="rank-api-client-application-sends-context-to-server"></a>순위 API: 클라이언트 애플리케이션에서 서버에 컨텍스트 보내기

클라이언트 애플리케이션에서 사용자의 브라우저 _사용자 에이전트_를 수집합니다.

> [!div class="mx-imgBorder"]
> ![HTTPRequestFeaturesExample 프로젝트를 빌드하고 실행합니다. 브라우저 창이 열려 단일 페이지 애플리케이션을 표시합니다.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>순위 API: 클라이언트 애플리케이션에서 Personalizer 호출

이는 클라이언트 애플리케이션이 있는 전형적인 .NET 웹앱이며, 대부분의 상용구 코드가 제공됩니다. Personalizer와 관련이 없는 코드는 다음 코드 조각에서 제거되므로 Personalizer 관련 코드에만 집중할 수 있습니다.

### <a name="create-personalizer-client"></a>Personalizer 클라이언트 만들기

서버의 **Startup.cs**에서 Personalizer 엔드포인트 및 키를 사용하여 Personalizer 클라이언트를 만듭니다. 클라이언트 애플리케이션은 이 앱에서 Personalizer와 통신할 필요가 없으며, 서버를 대신 사용하여 SDK 호출을 수행합니다.

웹 서버의 .NET 시작 코드는 다음과 같습니다.

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>최상의 작업 선택

서버의 **PersonalizerController.cs**에서 **GenerateRank** 서버 API는 다음과 같이 순위 API를 호출하기 위한 준비를 요약합니다.

* 순위 호출에 대한 새 `eventId` 만들기
* 작업 목록 가져오기
* 사용자로부터 기능 목록 가져오기 및 컨텍스트 기능 만들기
* 필요에 따라 제외되는 작업 설정
* 순위 API 호출 및 클라이언트에 결과 반환

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

작업(기능 포함)과 현재 컨텍스트 기능이 모두 포함되어 Personalizer에 보내지는 JSON은 다음과 같습니다.

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Personalizer rewardActionId를 클라이언트에 반환

순위 API는 선택한 최상의 작업인 **rewardActionId**를 서버에 반환합니다.

**rewardActionId**에서 반환된 작업이 표시됩니다.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>클라이언트에서 rewardActionId 작업 표시

이 자습서에서는 `rewardActionId` 값이 표시됩니다.

사용자 고유의 향후 애플리케이션의 경우 이는 정확한 텍스트, 단추 또는 강조 표시된 웹 페이지의 섹션일 수 있습니다. 콘텐츠 순서가 아니라 점수의 사후 분석에 대한 목록이 반환됩니다. `rewardActionId` 콘텐츠만 표시됩니다.

## <a name="reward-api-collect-information-for-reward"></a>보상 API: 보상 정보 수집

[보상 점수](concept-rewards.md)는 기능 계획과 마찬가지로 신중하게 계획해야 합니다. 보상 점수는 일반적으로 0에서 1까지의 값이어야 합니다. 이 값은 클라이언트 애플리케이션에서 사용자 동작에 따라 부분적으로, 그리고 서버에서 비즈니스 논리와 목표에 따라 부분적으로 _계산할 수 있습니다_.

Azure Portal에서 Personalizer 리소스에 대해 구성된 **보상 대기 시간** 내에 서버에서 Reward API를 호출하지 않는 경우 **기본 보상**(Azure Portal에도 구성됨)이 해당 이벤트에 사용됩니다.

이 애플리케이션 샘플에서 값을 선택하여 보상이 선택에 미치는 영향을 확인할 수 있습니다.

## <a name="additional-ways-to-learn-from-this-sample"></a>이 샘플에서 추가로 학습할 수 있는 방법

이 샘플에서는 Azure Portal에서 Personalizer 리소스에 대해 구성된 몇 가지 시간 기반 이벤트를 사용합니다. 해당 값을 사용하여 재생한 다음, 이 웹앱 샘플로 돌아가서 순위 및 보상 호출에 미치는 영향을 확인합니다.

* 보상 대기 시간
* 모델 업데이트 빈도

재생할 추가 설정은 다음과 같습니다.
* 기본 보상
* 검색 비율


## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하면 다음 리소스를 정리합니다.

* 프로젝트 샘플 디렉터리 삭제
* Personalizer 리소스 삭제 - Personalizer 리소스를 작업 및 컨텍스트 전용 리소스로 간주하여 음식을 작업 주제 영역으로 계속 사용하는 경우에만 이러한 리소스를 다시 사용합니다.


## <a name="next-steps"></a>다음 단계
* [Personalizer의 작동 원리](how-personalizer-works.md)
* [기능](concepts-features.md): 작업 및 컨텍스트에서 사용하는 기능에 대한 개념 알아보기
* [보상](concept-rewards.md): 보상 계산에 대해 알아보기
