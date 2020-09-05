---
title: '빠른 시작: Python을 사용하여 명함 데이터 추출 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python과 함께 Form Recognizer REST API를 사용하여 영어로 된 명함 이미지에서 데이터를 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 8132358dcd0ad9d87dc6687afd2adef1942f3b67
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823907"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>빠른 시작: Python과 함께 Form Recognizer REST API를 사용하여 명함 데이터 추출

이 빠른 시작에서는 Python과 Azure Form Recognizer REST API를 사용하여 명함의 관련 정보를 추출하고 식별합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- 명함의 이미지입니다. 이 빠른 시작에서는 [샘플 이미지](../media/business-card-english.jpg)를 사용할 수 있습니다.

> [!NOTE]
> 이 빠른 시작에서는 로컬 파일을 사용합니다. URL로 액세스하는 원격 명함 이미지를 대신 사용하려면 [참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)를 참조하세요.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>명함 분석

명함 분석을 시작하려면 아래 Python 스크립트를 사용하여 **[Analyze Business Card](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** API를 호출합니다. 스크립트를 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `<path to your business card>`을 로컬 양식 문서에 대한 경로로 바꿉니다.
1. `<subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. 코드를 확장명이 .py인 파일로 저장합니다. 예를 들어 *form-recognizer-businesscards.py*입니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. 예들 들어 `python form-recognizer-businesscards.py`입니다.

**Operation-Location** 헤더가 포함된 `202 (Success)` 응답을 받게 되며, 스크립트가 콘솔에 인쇄됩니다. 이 헤더에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 작업 ID가 포함됩니다. 다음 예제 값에서 `operations/` 다음 문자열은 작업 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>명함 결과 가져오기

**Analyze Business Card** API를 호출한 후 **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. Python 스크립트 맨 아래에 다음 코드를 추가합니다. 새 API 호출에서 작업 ID 값을 사용합니다. 이 스크립트는 결과를 사용할 수 있을 때까지 API를 일정한 간격으로 호출합니다. 1초 이상의 간격을 사용하는 것이 좋습니다.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. 스크립트를 저장합니다.
1. `python` 명령을 다시 사용하여 샘플을 실행합니다. 예들 들어 `python form-recognizer-businesscards.py`입니다.

### <a name="examine-the-response"></a>응답 검사
![Contoso 회사의 명함](../media/business-card-english.jpg)

이 샘플은 Form Recognizer에서 반환하는 JSON 출력을 보여줍니다. 이 샘플은 예제의 가독성을 위해 잘렸습니다.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

스크립트는 **명함 분석** 작업이 완료될 때까지 콘솔에 응답을 출력합니다. `"readResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `"documentResults"` 노드에는 모델이 검색한 명함 특정 값이 포함됩니다. 여기서 회사 이름, 이름, 성, 전화 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python과 함께 Form Recognizer REST API를 사용하여 명함의 콘텐츠를 추출했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
