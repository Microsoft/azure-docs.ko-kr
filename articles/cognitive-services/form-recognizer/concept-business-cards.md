---
title: 명함-양식 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한을 사용 하 여 비즈니스 카드 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 1163531fb5a6aa7158bd81ff9095ed1ee29e73c1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004904"
---
# <a name="business-card-concepts"></a>명함 개념

Azure 양식 인식기는 미리 빌드된 모델 중 하나를 사용 하 여 비즈니스 카드에서 연락처 정보를 분석 하 고 추출할 수 있습니다. 비즈니스 카드 API는 강력한 OCR (광학 문자 인식) 기능을 비즈니스 카드 이해 모델과 결합 하 여 영어로 된 비즈니스 카드에서 핵심 정보를 추출 합니다. 개인 연락처 정보, 회사 이름, 직위 등을 추출 합니다. 미리 작성 된 비즈니스 카드 API는 인식기 v 2.1 preview에서 공개적으로 사용할 수 있습니다. 

## <a name="what-does-the-business-card-api-do"></a>비즈니스 카드 API는 무엇을 하나요?

비즈니스 카드 API는 비즈니스 카드에서 키 필드를 추출 하 여 구성 된 JSON 응답으로 반환 합니다.

![FOTT + JSON 출력의 Contoso 항목별로 이미지](./media/business-card-english.jpg)

### <a name="fields-extracted"></a>추출 된 필드:

* 연락처 이름 
  * 이름
  * 성
* 회사 이름 
* Departments 
* 직위 
* 전자 메일 
* Websites 
* 주소 
* 전화 번호 
  * 휴대폰 
  * 없으며 
  * 직장 전화 
  * 기타 전화 

비즈니스 카드 API는 비즈니스 카드에서 인식 된 모든 텍스트를 반환할 수도 있습니다. 이 OCR 출력은 JSON 응답에 포함 됩니다.  

### <a name="input-requirements"></a>입력 요구 사항 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>비즈니스 카드 분석 작업

[분석 회사 카드](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 는 회사 카드의 이미지나 PDF를 입력으로 사용 하 고 원하는 값을 추출 합니다. 호출은 라는 응답 헤더 필드를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 결과 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>비즈니스 카드 분석 결과 가져오기 작업

두 번째 단계는 [Get The Business Card Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult) 작업을 호출 하는 것입니다. 이 작업은 비즈니스 카드 분석 작업에서 만든 결과 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)를 초과 하지 않도록 3 ~ 5 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 분석 작업이 시작 되지 않았습니다.<br /><br />실행 중: 분석 작업이 진행 중입니다.<br /><br />실패: 분석 작업이 실패 했습니다.<br /><br />성공: 분석 작업이 성공 했습니다.|

**상태** 필드에 **성공** 값이 있는 경우 JSON 응답에는 비즈니스 카드의 이해 및 선택적 텍스트 인식 결과가 포함 됩니다 (요청한 경우). 명함 이해 결과는 명명 된 필드 값의 사전으로 구성 됩니다. 여기서 각 값에는 추출 된 텍스트, 정규화 된 값, 경계 상자, 신뢰도 및 해당 단어 요소가 포함 됩니다. 텍스트 인식 결과는 텍스트, 경계 상자 및 신뢰도 정보를 사용 하 여 선과 단어의 계층 구조로 구성 됩니다.

![샘플 비즈니스 카드 출력](./media/business-card-results.png)

### <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예를 참조 하세요. "readResults" 노드에는 인식 된 모든 텍스트가 포함 됩니다. 텍스트는 페이지별로, 그 다음에는 줄별로, 그 다음에는 개별 단어별로 정리됩니다. "DocumentResults" 노드에는 모델에서 검색 한 명함의 특정 값이 포함 되어 있습니다. 여기에서 이름, 성, 회사 이름 등의 유용한 연락처 정보를 찾을 수 있습니다.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                "lines": 
                          {
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
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
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
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
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
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
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
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

[비즈니스 카드 추출 데이터](./QuickStarts/python-business-cards.md) 빠른 시작을 따라 Python 및 REST API를 사용 하 여 비즈니스 카드 데이터 추출을 구현 합니다.

## <a name="customer-scenarios"></a>고객 시나리오  

비즈니스 카드 API를 사용 하 여 추출 된 데이터를 사용 하 여 다양 한 작업을 수행할 수 있습니다. 이 연락처 정보를 추출 하면 클라이언트 쪽 역할의 시간에 대 한 시간이 자동으로 절약 됩니다. 다음은 고객이 비즈니스 카드 API를 사용 하 여 수행한 작업의 몇 가지 예입니다.

* 비즈니스 카드에서 연락처 정보를 추출 하 고 전화 연락처를 빠르게 만듭니다. 
* CRM과 통합 하 여 비즈니스 카드 이미지를 사용 하 여 연락처를 자동으로 만듭니다. 
* 판매 잠재 고객을 추적 합니다.  
* 기존 비즈니스 카드 이미지에서 연락처 정보를 대량으로 추출 합니다. 

또한 비즈니스 카드 API는 [AIBuilder 비즈니스 카드 처리 기능](https://docs.microsoft.com/ai-builder/prebuilt-business-card)을 향상 시킵니다.

## <a name="next-steps"></a>다음 단계

- 빠른 시작을 수행 하 여 [비즈니스 카드 API Python 빠른](./quickstarts/python-business-cards.md) 시작을 시작 하세요.
- [양식 인식기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 에 대해 알아보세요 REST API
- [양식 인식기](overview.md) 에 대 한 자세한 정보

