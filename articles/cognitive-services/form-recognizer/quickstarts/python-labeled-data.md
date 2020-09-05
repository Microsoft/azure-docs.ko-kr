---
title: '빠른 시작: REST API 및 Python을 사용하여 레이블로 학습 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: REST API 및 Python과 함께 Form Recognizer 레이블 지정 데이터 기능을 사용하여 사용자 지정 모델을 학습시키는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: e96940960b6ee131068b77bca4818499377ea3dd
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723500"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>REST API 및 Python을 사용하여 레이블로 Form Recognizer 모델 학습

이 빠른 시작에서는 Python과 함께 Form Recognizer REST API를 사용하여 수동 레이블 지정 데이터로 사용자 지정 모델을 학습시킬 것입니다. 이 기능에 대한 자세한 내용은 개요의 [레이블로 학습](../overview.md#train-with-labels) 섹션을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- 샘플을 로컬로 실행하려면 [Python](https://www.python.org/downloads/)이 설치되어 있어야 합니다.
- 동일한 형식의 양식 6개 이상으로 구성된 세트. 이 데이터를 사용하여 모델을 학습시키고 양식을 테스트합니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)를 사용할 수 있습니다. 표준 성능 계층 Azure Storage 계정의 Blob 스토리지 컨테이너 루트에 학습 파일을 업로드합니다.

> [!NOTE]
> 이 빠른 시작에서는 URL로 액세스하는 원격 문서를 사용합니다. 로컬 파일을 대신 사용하려면 [v2.0에 대한 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) 및 [v2.1에 대한 참조 설명서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/TrainCustomModelAsync)를 참조하세요.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>학습 데이터 설정

다음으로, 필요한 입력 데이터를 설정해야 합니다. 레이블이 지정된 데이터 기능에는 레이블이 없는 사용자 지정 모델을 학습하는 데 필요한 것 이상의 특별한 입력 요구 사항이 있습니다.

모든 학습 문서가 동일한 형식인지 확인합니다. 여러 형식의 양식이 있으면 공용 형식에 따라 하위 폴더로 구성합니다. 학습시키는 경우 API를 하위 폴더로 전달해야 합니다.

레이블 지정 데이터를 사용하여 모델을 학습시키려면 하위 폴더의 입력으로 다음 파일이 필요합니다. 아래에서 이러한 파일을 만드는 방법을 알아봅니다.

* **원본 양식** – 데이터를 추출할 양식입니다. 지원되는 형식은 JPEG, PNG, PDF 또는 TIFF입니다.
* **OCR 레이아웃 파일** - 각 원본 양식에서 읽을 수 있는 모든 텍스트의 크기와 위치를 설명하는 JSON 파일입니다. Form Recognizer 레이아웃 API를 사용하여 이 데이터를 생성할 것입니다. 
* **레이블 파일** - 사용자가 수동으로 입력한 데이터 레이블을 설명하는 JSON 파일입니다.

이러한 모든 파일은 동일한 하위 폴더에 있어야 하고 다음과 같은 형식이어야 합니다.

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Form Recognizer [샘플 레이블 지정 도구](./label-tool.md)를 사용하여 양식의 레이블을 지정할 때 이 도구는 이러한 레이블과 OCR 레이아웃 파일을 자동으로 만듭니다.

### <a name="create-the-ocr-output-files"></a>OCR 출력 파일 만들기

서비스에서 레이블 지정 학습에 해당 입력 파일을 고려하려면 OCR 결과 파일이 필요합니다. 지정된 원본 양식에 대한 OCR 결과를 얻으려면 다음 단계를 수행합니다.

1. 요청 본문의 일부로 입력 파일을 사용하여 읽기 레이아웃 컨테이너에서 **[분석 레이아웃](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API를 호출합니다. 응답의 **작업 위치** 헤더에서 찾은 ID를 저장합니다.
1. 이전 단계의 작업 ID를 사용하여 **[분석 레이아웃 결과 가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API를 호출합니다.
1. 응답을 가져오고 파일에 콘텐츠를 씁니다. 각 원본 양식과 관련하여, 해당 OCR 파일의 원래 파일 이름에 `.ocr.json`을 추가해야 합니다. OCR JSON 출력 형식은 다음과 같습니다. 전체 예제는 [샘플 OCR 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json)을 참조하세요. 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>레이블 파일 만들기

레이블 파일에는 사용자가 수동으로 입력한 키-값 연결이 포함되어 있습니다. 레이블 지정 데이터 학습에 필요하지만, 모든 원본 파일에 해당 레이블 파일이 있어야 하는 것은 아닙니다. 레이블이 없는 원본 파일은 일반적인 학습 문서로 취급됩니다. 안정적인 학습을 위해 레이블 지정 파일을 5개 이상 사용하는 것이 좋습니다. [샘플 레이블 지정 도구](./label-tool.md)와 같은 UI 도구를 사용하여 이러한 파일을 생성할 수 있습니다.

레이블 파일을 만들 때 필요에 따라 영역, 즉, 문서에서 값의 정확한 값 위치를 지정할 수 있습니다. 이렇게 하면 학습의 정확도가 대폭 향상됩니다. 영역은 네 가지 X, Y 좌표(왼쪽 위, 오른쪽 위, 오른쪽 아래, 왼쪽 아래)에 해당하는 8개의 값 세트로 형식이 지정됩니다. 좌표 값은 0에서 1 사이이며, 페이지 크기에 맞게 조정됩니다.

각 원본 양식과 관련하여, 해당 레이블 파일의 원래 파일 이름에 `.labels.json`을 추가해야 합니다. 레이블 파일의 형식은 다음과 같습니다. 전체 예제는 [샘플 레이블 파일](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json)을 참조하세요.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> 각 텍스트 요소에 레이블을 하나만 적용할 수 있으며, 각 레이블은 페이지마다 한 번만 적용할 수 있습니다. 여러 페이지에 레이블을 적용할 수 없습니다.


## <a name="train-a-model-using-labeled-data"></a>레이블 지정 데이터를 사용하여 모델 학습

레이블 지정 데이터로 모델을 학습시키려면 다음 python 코드를 실행하여 **[사용자 지정 모델 학습](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API를 호출합니다. 코드를 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 리소스의 엔드포인트 URL로 대체합니다.
1. `<SAS URL>`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
1. `<Blob folder name>`을 입력 데이터가 있는 Blob 컨테이너의 폴더 이름으로 바꿉니다. 또는 데이터가 루트에 있는 경우에는 이 값을 비워 두고 HTTP 요청 본문에서 `"prefix"` 필드를 제거합니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.1/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>학습 결과 가져오기

학습 작업을 시작한 후에는 반환된 ID를 사용하여 작업 상태를 가져옵니다. Python 스크립트 맨 아래에 다음 코드를 추가합니다. 이 코드는 학습 호출의 ID 값을 새 API 호출에 사용합니다. 학습 작업은 비동기식이므로, 이 스크립트는 학습 상태가 완료될 때까지 API를 일정한 간격으로 호출합니다. 1초 이상의 간격을 사용하는 것이 좋습니다.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

학습 프로세스가 완료되면 다음과 같은 JSON 콘텐츠가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 응답은 간단한 설명을 위해 단축되었습니다.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

다음 단계에서 사용할 수 있도록 `"modelId"` 값을 복사합니다.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

프로세스가 완료되면 다음 형식의 JSON 콘텐츠가 포함된 `202 (Success)` 응답을 받게 됩니다. 이 응답은 간단한 설명을 위해 단축되었습니다. 기본 키/값 연결은 `"documentResults"` 노드에 있습니다. `"selectionMarks"` 노드(v2.1 미리 보기에서)는 모든 선택 표시(확인란, 라디오 표시) 및 상태가 "선택됨" 또는 "선택되지 않음"인지 여부를 표시합니다. 레이아웃 API 결과(문서에 있는 모든 텍스트의 콘텐츠 및 위치)는 `"readResults"` 노드에 있습니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
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
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>결과 개선

`"documentResults"` 노드 아래에서 각 키/값 결과에 대한 `"confidence"` 값을 검사합니다. 레이아웃 작업에 해당하는 `"readResults"` 노드의 신뢰도 점수도 확인해야 합니다. 레이아웃 결과의 신뢰도는 키/값 추출 결과의 신뢰도에 영향을 주지 않으므로 둘 다 확인해야 합니다.
* 레이아웃 작업에 대한 신뢰도 점수가 낮으면 입력 문서의 품질을 개선합니다([입력 요구 사항](../overview.md#input-requirements) 참조).
* 키/값 추출 작업의 신뢰도 점수가 낮으면 분석 중인 문서가 학습 세트에 사용된 문서와 동일한 유형인지 확인합니다. 학습 세트의 문서 모양이 변형된 경우 각 변형에 대해 하나의 모델을 학습하고 다른 폴더로 분할하는 것이 좋습니다.

### <a name="avoid-cluttered-labels"></a>복잡한 레이블 방지

동일한 텍스트 줄 내에 여러 레이블을 적용하다 보면 서비스에서 이러한 레이블을 하나의 필드에 병합하는 경우가 발생할 수 있습니다. 예를 들어 주소에서 구/군/시, 시/도 및 우편 번호를 서로 다른 필드로 레이블링할 수 있지만, 예측 중에는 이러한 필드를 별도로 인식하지 못합니다.

이 시나리오는 고객에게 매우 중요하다는 것을 이해하고 있으며, 향후 이 부분을 개선할 수 있도록 노력하고 있습니다. 현재 사용자는 여러 복잡한 필드를 하나의 필드로 레이블링한 다음, 추출 결과의 사후 처리에서 용어를 구분하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python과 함께 Form Recognizer REST API를 사용하여 수동 레이블 지정 데이터로 모델을 학습시키는 방법을 알아보았습니다. 다음으로, API 참조 설명서를 통해 Form Recognizer API에 대해 자세히 살펴보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
