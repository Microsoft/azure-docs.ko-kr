---
title: C#에서 REST 호출을 사용하여 예측 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 4cbec342bc20de35c0c62284e4e1fe1ae8b8e8a4
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966722"
---
## <a name="prerequisites"></a>사전 요구 사항

* [.NET Core V2.2 이상](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>예측을 위한 LUIS 런타임 키 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **Language Understanding 클라이언트 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 클릭
1. 런타임 키에 필요한 모든 설정을 입력합니다.

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 `endpoint` 및 `key`를 수집합니다.

## <a name="get-intent-programmatically"></a>프로그래밍 방식으로 의도 가져오기

C#(.NET Core)을 사용하여 [예측 엔드포인트](https://aka.ms/luis-apim-v3-prediction)를 쿼리하고 예측 결과를 가져옵니다.

1. `predict-with-rest`의 프로젝트 및 폴더 이름을 사용하여 C# 언어를 대상으로 하는 새 콘솔 애플리케이션을 만듭니다.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. 방금 만든 `predict-with-rest` 디렉터리로 변경하고 다음 명령을 사용하여 필요한 종속성을 설치합니다.

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. 즐겨찾는 IDE 또는 편집기에서 `Program.cs`를 엽니다. 그런 다음, `Program.cs`를 다음 코드로 덮어씁니다.

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. `YOUR-KEY` 및 `YOUR-ENDPOINT` 값을 고유한 예측 키 및 엔드포인트로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-KEY`|32자 예측 키입니다.|
    |`YOUR-ENDPOINT`| 예측 URL 엔드포인트입니다. `replace-with-your-resource-name.api.cognitive.microsoft.com`)을 입력합니다.|

1. 다음 명령을 사용하여 콘솔 애플리케이션을 빌드합니다.

    ```console
    dotnet build
    ```

1. 콘솔 애플리케이션을 실행합니다. 콘솔 출력에 브라우저 창에서 앞서 본 것과 동일한 JSON이 표시됩니다.

    ```console
    dotnet run
    ```

1. JSON으로 반환되는 예측 응답을 검토합니다.

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    가독성을 위한 JSON 응답 형식:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료한 후 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [발화 및 학습 추가](../get-started-get-model-rest-apis.md)