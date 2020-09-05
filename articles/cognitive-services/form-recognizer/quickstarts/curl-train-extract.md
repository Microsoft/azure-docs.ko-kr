---
title: '빠른 시작: cURL을 사용하여 모델 학습 및 양식 데이터 추출 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL에서 Form Recognizer REST API를 사용하여 모델을 학습시키고 양식에서 데이터를 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: f4e26d74ff0922841d2ceb2ce4eb06b96c697a9f
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719101"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>빠른 시작: cURL에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출

이 빠른 시작에서는 cURL에서 Azure Form Recognizer REST API를 통해 양식을 학습시키고 채점하여 키-값 쌍 및 테이블을 추출합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- [cURL](https://curl.haxx.se/windows/) 설치
- 동일한 형식의 양식 6개 이상으로 구성된 세트. 이 중 다섯 개를 사용하여 모델을 학습한 다음, 여섯 번째 형식으로 테스트합니다. 양식은 파일 형식이 다를 수 있지만 동일한 형식의 문서여야 합니다. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)를 사용할 수 있습니다. 표준 성능 계층 Azure Storage 계정의 Blob 스토리지 컨테이너 루트에 학습 파일을 업로드합니다. 테스트 파일을 별도의 폴더에 배치할 수 있습니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Form Recognizer 모델 학습

먼저 Azure Storage Blob의 학습 데이터 세트가 필요합니다. 주 입력 데이터와 동일한 형식/구조의 채워진 양식(PDF 문서 및/또는 이미지)이 5개 이상 있어야 합니다. 또는 두 개의 채워진 양식이 있는 단일 빈 양식을 사용할 수 있습니다. 빈 양식의 파일 이름에 "empty"라는 단어가 포함되어야 합니다. 학습 데이터를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 집합 빌드](../build-training-data-set.md)를 참조하세요.

> [!NOTE]
> 레이블이 지정된 데이터 기능을 사용하여 미리 수동으로 학습 데이터의 일부 또는 전체에 레이블을 지정할 수 있습니다. 이는 좀 더 복잡한 프로세스지만 학습된 모델을 더 효율적으로 만듭니다. 이 기능에 대한 자세한 내용은 개요의 [레이블을 사용하여 학습](../overview.md#train-with-labels) 섹션을 참조하세요.



Azure Blob 컨테이너의 문서를 사용하여 Form Recognizer 모델을 학습시키려면 다음 cURL 명령을 실행하여 **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `<subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.
1. `<SAS URL>`을 Azure Blob Storage 컨테이너의 SAS(공유 액세스 서명) URL로 바꿉니다. SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 컨테이너를 마우스 오른쪽 단추로 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



**위치** 헤더가 포함된 `201 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 학습 중인 새 모델의 ID입니다.

## <a name="get-training-results"></a>학습 결과 가져오기

학습 작업을 시작한 후에는 새 작업인 **[사용자 지정 모델 가져오기](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** 를 사용하여 학습 상태를 확인합니다. 모델 ID를 이 API 호출에 전달하여 학습 상태를 확인합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다.
1. `<subscription key>`를 구독 키로 바꿉니다.
1. `<model ID>`를 이전 단계에서 받은 모델 ID로 바꿉니다.



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

다음 형식의 JSON 본문이 포함된 `200 (Success)` 응답을 받게 됩니다. `"status"` 필드를 유의하세요. 학습이 완료되면 `"ready"` 값이 포함됩니다. 모델이 학습을 완료하지 않은 경우 명령을 다시 실행하여 서비스를 다시 쿼리해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

`"modelId"` 필드에는 학습 중인 모델의 ID가 포함됩니다. 이 값은 다음 단계에서 필요합니다.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>키-값 쌍 및 테이블에 대한 양식 분석

다음으로, 새로 학습된 모델을 사용하여 문서를 분석하고 키-값 쌍 및 테이블을 추출합니다. 다음 cURL 명령을 실행하여 **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<model ID>`를 이전 섹션에서 받은 모델 ID로 바꿉니다.
1. `<SAS URL>`을 Azure 스토리지의 파일을 잇는 SAS URL로 바꿉니다. 학습 섹션의 단계를 따르되 전체 Blob 컨테이너에 대한 SAS URL을 가져오는 대신 분석하려는 특정 파일에 대한 SAS URL을 가져옵니다.
1. `<subscription key>`를 구독 키로 바꿉니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값은 분석 작업의 결과를 추적하는 데 사용하는 결과 ID를 포함합니다. 다음 단계를 위해 이 결과 ID를 저장합니다.

## <a name="get-the-analyze-results"></a>분석 결과 가져오기

다음 API를 사용하여 분석 작업의 결과를 쿼리합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 가져온 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<result ID>`를 이전 섹션에서 받은 ID로 바꿉니다.
1. `<subscription key>`를 구독 키로 바꿉니다.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

다음 형식의 JSON 본문이 포함된 `200 (Success)` 응답을 받게 됩니다. 출력은 가독성을 위해 줄였습니다. 아래쪽에 `"status"` 필드를 유의하세요. 분석 작업이 완료되면 여기에 `"succeeded"` 값이 포함됩니다. 분석 작업이 완료되지 않은 경우 명령을 다시 실행하여 서비스를 다시 쿼리해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

기본 키/값 쌍 연결 및 테이블은 `"pageResults"` 노드에 있습니다. *includeTextDetails* URL 매개 변수를 통해 일반 텍스트 추출도 지정한 경우 `"readResults"` 노드에는 문서에 있는 모든 텍스트의 콘텐츠와 위치가 표시됩니다.

이 샘플 JSON 출력은 편의상 간소화되었습니다.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1 미리 보기](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
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
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>결과 개선

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 cURL에서 Form Recognizer REST API를 사용하여 모델을 학습시키고 샘플 시나리오에서 실행했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
