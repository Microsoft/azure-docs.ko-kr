---
title: '빠른 시작: Python Form Recognizer를 사용하여 텍스트 및 레이아웃 정보 추출'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Python에서 Form Recognizer Layout REST API를 사용하여 양식에서 텍스트 및 테이블 데이터를 읽습니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: e6c18df2d90f8d6efc9425f2d9fdff057e06ad51
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719117"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>빠른 시작: Python과 Form Recognizer REST API를 사용하여 텍스트 및 레이아웃 정보 추출

이 빠른 시작에서는 Python과 Azure Form Recognizer REST API를 사용하여 양식 문서에서 텍스트 레이아웃 정보와 테이블 데이터를 추출합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- 양식 문서입니다. 이 빠른 시작에 대한 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)에서 이미지를 다운로드할 수 있습니다.

> [!NOTE]
> 이 빠른 시작에서는 로컬에 저장된 문서를 사용합니다. URL로 액세스하는 원격 파일을 사용하는 방법을 알아보려면 [참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)를 참조하세요.


## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>양식 레이아웃 분석

레이아웃 분석을 시작하기 위해, 아래 Python 스크립트를 사용하여 **[Analyze Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API를 호출합니다. 스크립트를 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `<path to your form>`을 로컬 양식 문서에 대한 경로로 바꿉니다.
1. `<subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0) 
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```   
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)  
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```  
    
    
      ---


1. 코드를 확장명이 .py인 파일로 저장합니다. 예를 들어 *form-recognizer-layout.py*입니다.
1. 명령 프롬프트 창을 엽니다.
1. 프롬프트에서 `python` 명령을 사용하여 샘플을 실행합니다. 예들 들어 `python form-recognizer-layout.py`입니다.

**Operation-Location** 헤더가 포함된 `202 (Success)` 응답을 받게 되며, 스크립트가 콘솔에 인쇄됩니다. 이 헤더에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 작업 ID가 포함됩니다. 다음 예제 값에서 `operations/` 다음 문자열은 작업 ID입니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)   
```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
``` 
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)  
```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```  

---
    



## <a name="get-the-layout-results"></a>레이아웃 결과 가져오기

**Analyze Layout** API를 호출한 후 **[Get Analyze Layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. Python 스크립트 맨 아래에 다음 코드를 추가합니다. 이 코드는 새 API 호출에서 작업 ID 값을 사용합니다. 이 스크립트는 결과를 사용할 수 있을 때까지 API를 일정한 간격으로 호출합니다. 1초 이상의 간격을 사용하는 것이 좋습니다.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
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
1. `python` 명령을 다시 사용하여 샘플을 실행합니다. 예들 들어 `python form-recognizer-layout.py`입니다.

### <a name="examine-the-response"></a>응답 검사

스크립트는 **레이아웃 분석** 작업이 완료될 때까지 콘솔에 응답을 출력합니다. 그런 다음, 추출된 데이터를 JSON 형식으로 출력합니다. `"readResults"` 노드에는 페이지의 각 경계 상자 배치가 있는 모든 텍스트 줄이 포함되어 있습니다. `"selectionMarks"` 노드(v2.1 미리 보기에서)는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 "선택됨" 또는 "선택되지 않음"인지 여부를 표시합니다. `"pageResults"` 필드는 테이블 내의 모든 텍스트 부분을 표시하며, 각 텍스트에는 행-열 좌표가 포함됩니다.

다음 청구서 이미지와 해당 JSON 출력을 참조하세요. 출력은 가독성을 위해 줄였습니다.

:::image type="content" source="../media/contoso-invoice.png" alt-text="테이블이 포함된 Contoso 프로젝트 문 문서.":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---



## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python과 Form Recognizer REST API를 사용하여 청구서의 텍스트 레이아웃을 추출했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
