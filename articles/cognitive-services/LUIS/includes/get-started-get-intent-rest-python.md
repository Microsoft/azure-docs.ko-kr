---
title: Python에서 REST 호출을 사용하여 의도 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987790"
---
## <a name="prerequisites"></a>사전 요구 사항

* [Python 3.6](https://www.python.org/downloads/) 이상
* [Visual Studio Code](https://code.visualstudio.com/)
* 공용 앱 ID: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>예측을 위한 LUIS 런타임 키 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **Language Understanding 클라이언트 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 클릭
1. **런타임** 키에 필요한 모든 설정을 입력합니다.

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 `endpoint` 및 `key`를 수집합니다.

## <a name="get-intent-from-the-prediction-endpoint"></a>예측 엔드포인트에서 의도 가져오기

Python을 사용하여 [예측 엔드포인트](https://aka.ms/luis-apim-v3-prediction)를 쿼리하고 예측 결과를 가져옵니다.

1. 이 코드 조각을 `predict.py`라는 파일에 복사합니다.

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. `YOUR-KEY` 및 `YOUR-ENDPOINT` 값을 고유한 예측 **런타임** 키 및 엔드포인트로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-KEY`|32자 예측 **런타임** 키입니다.|
    |`YOUR-ENDPOINT`| 예측 URL 엔드포인트입니다. `replace-with-your-resource-name.api.cognitive.microsoft.com`)을 입력합니다.|

1. `requests` 종속성을 설치합니다. 이는 HTTP 요청을 수행하는 데 사용됩니다.

    ```console
    pip install requests
    ```

1. 다음 콘솔 명령을 사용하여 스크립트를 실행합니다.

    ```console
    python predict.py
    ```

1. JSON으로 반환되는 예측 응답을 검토합니다.

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    가독성을 위해 형식이 지정된 JSON 응답은 다음과 같습니다.

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