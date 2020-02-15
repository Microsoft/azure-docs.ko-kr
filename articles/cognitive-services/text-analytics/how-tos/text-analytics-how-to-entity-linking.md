---
title: 텍스트 분석 API에서 엔터티 인식 사용
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API를 사용 하 여 텍스트에 있는 엔터티의 id를 식별 하 고 명확 하 게 구분 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 607b65d6a6893901ce23cd48c277c14209128866
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137963"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics에서 명명된 엔터티 인식 사용 방법

텍스트 분석 API를 사용 하면 구조화 되지 않은 텍스트를 가져와 웹에 대 한 자세한 정보에 대 한 링크가 포함 된 명확 하지 않은 엔터티 목록을 반환할 수 있습니다. API는 NER (명명 된 엔터티 인식) 및 엔터티 연결을 모두 지원 합니다.

### <a name="entity-linking"></a>엔터티 연결

엔터티 연결은 텍스트에 있는 엔터티의 id를 식별 하 고 구분 하는 기능입니다. 예를 들어 `Mars` 단어가 행성을 참조 하는지 또는 전쟁의 로마자를 참조 하는지 확인 합니다. 이 프로세스에서는 인식 된 엔터티를 텍스트에 연결 하기 위해 기술 자료가 적절 한 언어로 존재 해야 합니다. 엔터티 링크는이 기술 자료로 [위키백과](https://www.wikipedia.org/) 를 사용 합니다.


### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

NER (명명 된 엔터티 인식)는 텍스트에서 다양 한 엔터티를 식별 하 고 미리 정의 된 클래스 또는 형식으로 분류 하는 기능입니다. 예: 사람, 장소 및 조직.

## <a name="named-entity-recognition-versions-and-features"></a>명명 된 엔터티 인식 버전 및 기능

텍스트 분석 API는 두 가지 버전의 명명 된 엔터티 인식-v2 및 v3을 제공 합니다. 버전 3 (공개 미리 보기)은 검색 및 분류 될 수 있는 엔터티에서 세부 정보를 제공 합니다.

| 기능                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 단일 및 일괄 처리 요청을 위한 메서드                          | X      | X      |
| 여러 범주에서 기본 엔터티 인식              | X      | X      |
| 인식 된 엔터티의 확장 된 분류                 |        | X      |
| 엔터티 연결 및 NER 요청을 보내기 위한 별도의 끝점입니다. |        | X      |
| 모델 버전 관리                                                |        | X      |

자세한 내용은 [언어 지원](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) 을 참조 하세요.


#### <a name="version-30-previewtabversion-3"></a>[버전 3.0-미리 보기](#tab/version-3)

### <a name="entity-types"></a>엔터티 형식

명명 된 엔터티 인식 v3은 여러 형식에서 확장 된 검색을 제공 합니다. 현재 NER v3은 다음과 같은 범주의 엔터티를 인식할 수 있습니다.

* 일반
* 개인 정보 

지원 되는 엔터티 및 언어의 자세한 목록은 [NER v3 지원 되는 엔터티 형식](../named-entity-types.md) 문서를 참조 하세요.

### <a name="request-endpoints"></a>요청 끝점

명명 된 엔터티 인식 v3은 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용 합니다. 요청에 따라 아래 URL 형식을 사용 합니다.

NER
* 일반 엔터티-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 개인 정보-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>모델 버전 관리

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

### <a name="entity-types"></a>엔터티 형식

> [!NOTE]
> NER (명명 된 엔터티 인식) 버전 2는 다음 엔터티만 지원 합니다. NER v3은 공개 미리 보기 상태 이며 텍스트에서 인식 되는 엔터티의 수와 깊이를 크게 확장 합니다.   

| 형식  | 하위 유형 | 예제 |
|:-----------   |:------------- |:---------|
| 사용자        | 해당 없음\*         | "Jeff", "Bill Gates"     |
| 위치      | 해당 없음\*         | "Redmond, Washington", "Paris"  |
| 조직  | 해당 없음\*         | "Microsoft"   |
| 수량      | Number        | "6", "six"     |
| 수량      | 백분율    | "50%", "fifty percent"|
| 수량      | Ordinal       | "2nd", "second"     |
| 수량      | 처리 기간           | "90 day old", "30 years old"    |
| 수량      | 통화      | "$10.99"     |
| 수량      | Dimension     | "10 miles", "40 cm"     |
| 수량      | 온도   | "32 degrees"    |
| DateTime      | 해당 없음\*         | "6:30PM February 4, 2012"      |
| DateTime      | 날짜          | "May 2nd, 2017", "05/02/2017"   |
| DateTime      | 시간          | "8am", "8:00"  |
| DateTime      | DateRange     | "May 2nd to May 5th"    |
| DateTime      | TimeRange     | "6pm to 7pm"     |
| DateTime      | 지속 시간      | "1 minute and 45 seconds"   |
| DateTime      | 설정           | "every Tuesday"     |
| URL           | 해당 없음\*         | "https:\//www.bing.com"    |
| Email         | 해당 없음\*         | "support@contoso.com" |
| 미국 전화 번호  | 해당 없음\*         | (미국 전화 번호만 해당) "(312) 555-0176" |
| IP 주소    | 해당 없음\*         | 10.0.0.100 |

\* 입력 및 추출된 엔터티에 따라, 특정 엔터티에 `SubType`이 없을 수 있습니다.  나열 된 모든 지원 되는 엔터티 형식은 영어, 중국어 간체, 프랑스어, 독일어 및 스페인어 언어에 대해서만 사용할 수 있습니다.

### <a name="request-endpoints"></a>요청 끝점

명명 된 엔터티 인식 v2는 NER 및 엔터티 연결 요청에 대해 단일 끝점을 사용 합니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기

### <a name="preparation"></a>준비

ID, 텍스트, 언어 형식의 JSON 문서가 있어야 합니다.

각 문서는 5120 자 미만 이어야 하 고 컬렉션 당 최대 1000 항목 (Id)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. 다음 링크에서 [Postman](text-analytics-how-to-call-api.md) 또는 **API 테스트 콘솔** 을 사용 하 여 신속 하 게 구조를 구성 하 고 보낼 수 있습니다. 

> [!NOTE]
> Azure portal에서 Text Analytics 리소스에 대 한 키 및 끝점을 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0-미리 보기](#tab/version-3)

[명명 된 엔터티 인식 v3 참조](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

버전 3에서는 NER 및 엔터티 연결 요청에 대 한 별도의 끝점을 사용 합니다. 요청에 따라 아래 URL 형식을 사용 합니다.

NER
* 일반 엔터티-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 개인 정보 엔터티-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

[NER (명명 된 엔터티 인식) v2 참조](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

버전 2는 엔터티 링크 및 NER 요청에 대해 다음 끝점을 사용 합니다. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 요청 본문에서 준비한 JSON 문서를 제공 합니다.

### <a name="example-ner-request"></a>예제 NER 요청 

다음은 API에 보낼 수 있는 콘텐츠의 예입니다. 두 API 버전의 요청 형식은 동일합니다.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

## <a name="view-results"></a>결과 보기

모든 POST 요청에서는 Id와 검색 된 엔터티 속성을 사용 하 여 JSON 형식의 응답을 반환 합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다.


#### <a name="version-30-previewtabversion-3"></a>[버전 3.0-미리 보기)](#tab/version-3)

### <a name="example-v3-responses"></a>V3 응답 예

버전 3은 NER 및 엔터티 링크에 대 한 별도의 끝점을 제공 합니다. 두 작업 모두에 대 한 응답은 아래와 같습니다.

#### <a name="example-ner-response"></a>NER 응답 예제

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>엔터티 연결 응답 예제

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>예제 NER v2 응답
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 텍스트 분석을 사용하여 엔터티 링크 설정에 대한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 명명 된 엔터티 인식은 두 버전에서 선택한 언어로 사용할 수 있습니다.
* 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
* POST 요청은 개인 설정 된 [액세스 키와](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 구독에 유효한 끝점을 사용 하 여 하나 이상의 끝점으로 전송 됩니다.
* 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 애플리케이션에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
