---
title: '빠른 시작: cURL을 사용하여 영수증 데이터 추출 - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 cURL과 함께 Form Recognizer REST API를 사용하여 미국 판매 영수증 이미지에서 데이터를 추출합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 4fa31a5be41e89c8fdc821ae77ff151b184316df
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518019"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>빠른 시작: cURL과 Form Recognizer REST API를 사용하여 영수증 데이터 추출

이 빠른 시작에서는 cURL과 함께 Azure Form Recognizer REST API를 사용하여 미국 판매 영수증에서 관련 정보를 추출하고 식별합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.
- [cURL](https://curl.haxx.se/windows/) 설치
- 영수증 이미지의 URL. 이 빠른 시작에서는 [샘플 이미지](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg)를 사용할 수 있습니다.

## <a name="create-a-form-recognizer-resource"></a>Form Recognizer 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>영수증 분석

영수증 분석을 시작하기 위해, 아래 cURL 명령을 사용하여 **[Analyze Receipt](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** API를 호출합니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독에서 얻은 엔드포인트로 바꿉니다.
1. `<your receipt URL>`을 영수증 이미지의 URL 주소로 바꿉니다.
1. `<subscription key>`를 이전 단계에서 복사한 구독 키로 바꿉니다.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<your receipt URL>\"}"
```

**Operation-Location** 헤더를 포함하는 `202 (Success)` 응답을 받게 됩니다. 이 헤더의 값에는 비동기 작업 상태를 쿼리하고 결과를 가져오는 데 사용할 수 있는 작업 ID가 포함됩니다. 다음 예제에서 `operations/` 다음 문자열은 작업 ID입니다.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>영수증 결과 가져오기

**Analyze Receipt** API를 호출한 후 **[Get Analyze Receipt Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** API를 호출하여 작업의 상태와 추출된 데이터를 가져옵니다. 명령을 실행하기 전에 다음과 같이 변경합니다.

1. `<Endpoint>`를 Form Recognizer 구독 키에서 얻은 엔드포인트로 바꿉니다. Form Recognizer 리소스 **개요** 탭에서 찾을 수 있습니다.
1. `<operationId>`를 이전 단계의 작업 ID로 바꿉니다.
1. `<subscription key>`를 구독 키로 바꿉니다.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>응답 검사

JSON 출력이 포함된 `200 (Success)` 응답을 받게 됩니다. 작업의 상태를 나타내는 첫 번째 필드, `"status"`입니다. 작업이 완료되는 경우 `"recognitionResults"` 필드에는 영수증에서 추출된 텍스트의 모든 줄이 `"understandingResults"` 필드에는 영수증의 가장 적절한 부분에 대한 키/값 정보가 포함됩니다. 작업이 완료되지 않는 경우 `"status"`의 값은 `"running"` 또는 `"notStarted"`가 되며, 수동으로 또는 스크립트를 통해 API를 다시 호출해야 합니다. 호출 간에 1초 이상의 간격을 사용하는 것이 좋습니다.

다음 영수증 이미지와 해당 JSON 출력을 참조하세요. 출력은 가독성을 위해 줄였습니다.

![Contoso 매장의 영수증](../media/contoso-allinone.jpg)

`"recognitionResults"` 노드에는 인식된 모든 텍스트가 포함됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. `"understandingResults"` 노드에는 모델이 검색한 수신 특정 값이 포함됩니다. 여기서는 세금, 합계, 판매자 주소 등과 같은 유용한 키/값 쌍을 찾을 수 있습니다.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.0.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 cURL과 함께 Form Recognizer REST API를 사용하여 판매 영수증의 콘텐츠를 추출했습니다. 다음으로, 참조 설명서를 통해 Form Recognizer API에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [REST API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
