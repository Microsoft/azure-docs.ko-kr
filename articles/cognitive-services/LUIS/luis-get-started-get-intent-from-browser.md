---
title: '빠른 시작: 브라우저를 사용한 예측 쿼리 - LUIS'
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 브라우저의 대화형 텍스트에서 사용자의 의도를 판단합니다.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 24c7a8aca410ad10b8a9299c4bca2f13d0feb780
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245998"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>빠른 시작: 사용자 텍스트로 예측 런타임 쿼리

LUIS 예측 엔드포인트가 반환하는 값을 이해하려면 웹 브라우저에서 예측 결과를 확인하세요.

## <a name="prerequisites"></a>사전 요구 사항

공용 앱을 쿼리하려면 다음이 필요합니다.

* LUIS(Language Understanding) 리소스 정보:
    * **예측 키** - [LUIS Portal](https://www.luis.ai/)에서 가져올 수 있습니다. 아직 키를 만들 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/cognitive-services)으로 등록할 수 있습니다.
    * **예측 엔드 포인트 하위 도메인** - 하위 도메인은 LUIS 리소스의 **이름**이기도 합니다.
* LUIS 앱 ID - `df67dcdb-c37d-46af-88e1-8b97951ca1c2`의 공용 IoT 앱 ID를 사용합니다. 빠른 시작 코드에 사용되는 사용자 쿼리는 해당 앱에만 적용됩니다.

## <a name="use-the-browser-to-see-predictions"></a>브라우저를 사용하여 예측 보기

1. 웹 브라우저를 엽니다.
1. 아래 전체 URL을 사용하여 `YOUR-KEY`를 사용자의 고유한 LUIS 예측 키로 바꿉니다. 요청은 GET 요청이며 LUIS 예측 키가 있는 권한 부여를 쿼리 문자열 매개 변수로 포함합니다.

    #### <a name="v3-prediction-request"></a>[V3 예측 요청](#tab/V3-1-1)


    **GET** 엔드포인트(슬롯) 요청의 V3 URL 형식은 다음과 같습니다.

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 예측 요청](#tab/V2-1-2)

    **GET** 엔드포인트 요청의 V2 URL 형식은 다음과 같습니다.

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. 브라우저 창에 URL을 붙여넣고 Enter 키를 누릅니다. LUIS에서 `HomeAutomation.TurnOn` 의도(최고 의도)와 `on` 값이 있는 `HomeAutomation.Operation` 엔터티를 검색한다고 나타내는 JSON 결과가 브라우저에 표시됩니다.

    #### <a name="v3-prediction-response"></a>[V3 예측 응답](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2 예측 응답](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. 모든 의도를 보려면 적절한 쿼리 문자열 매개 변수를 추가합니다.

    #### <a name="v3-prediction-endpoint"></a>[V3 예측 엔드포인트](#tab/V3-3-1)

    QueryString의 끝에 `show-all-intents=true`를 추가하여 **모든 의도를 표시**합니다.

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2 예측 엔드포인트](#tab/V2)

    QueryString의 끝에 `verbose=true`를 추가하여 **모든 의도를 표시**합니다.

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
* [V3 예측 엔드포인트](luis-migration-api-v3.md)
* [사용자 지정 하위 도메인](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [LUIS 포털에서 앱 만들기](get-started-portal-build-app.md)
