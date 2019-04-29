---
title: 콘텐츠 유형 처리 - Azure Logic Apps | Microsoft Docs
description: Logic Apps가 디자인 타임 및 런타임에 콘텐츠 형식을 처리하는 방법 알아보기
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2a9318317d5a01136a42b4fb6d580bafaf53ec4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685767"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Azure Logic Apps에서 콘텐츠 형식 처리

JSON, XML, 플랫 파일, 이진 데이터 등 다양한 형식의 콘텐츠가 논리 앱을 통해 흐를 수 있습니다. Logic Apps가 모든 콘텐츠 형식을 지원하지만, 일부 형식은 네이티브 지원이 제공되므로 논리 앱에서 캐스팅 또는 변환이 필요하지 않습니다. 형식에 따라 캐스팅 또는 변환이 필요할 수도 있습니다. 이 문서에서는 Logic Apps가 콘텐츠 형식을 처리하는 방법 및 필요할 때 이러한 형식을 올바르게 캐스팅 또는 변환하는 방법을 설명합니다.

콘텐츠 형식을 처리하는 적절한 방법을 결정하기 위해 Logic Apps는 HTTP 호출의 `Content-Type` 헤더 값을 사용하며, 다음은 그 예입니다.

* [application/json](#application-json)(네이티브 형식)
* [text/plain](#text-plain)(네이티브 형식)
* [application/xml 및 application/octet-stream](#application-xml-octet-stream)
* [다른 콘텐츠 형식](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps는 *application/json* 콘텐츠 형식을 JSON(JavaScript Notation) 개체로 사용하여 요청을 저장하고 처리합니다. 기본적으로 캐스팅 없이 JSON 콘텐츠를 구문 분석할 수 있습니다. "application/json" 콘텐츠 형식의 헤더가 있는 요청을 구문 분석하려면 언어 식을 사용하면 됩니다. 이 예제는 캐스팅 없이 `animal-type` 배열에서 `dog` 값을 반환합니다. 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

헤더를 지정하지 않는 JSON 데이터를 사용하는 경우 다음과 같이 [json() 함수](../logic-apps/workflow-definition-language-functions-reference.md#json)를 사용하여 수동으로 해당 데이터를 JSON으로 캐스팅할 수 있습니다. 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>JSON 속성에 대한 토큰 만들기

Logic Apps는 JSON 콘텐츠의 속성을 나타내는 친숙한 토큰을 생성하는 기능을 제공하므로 논리 앱 워크플로에서 이러한 속성을 보다 쉽게 참조하고 사용할 수 있습니다.

* **요청 트리거**

  논리 앱 디자이너에서 이 트리거를 사용하면 받을 것으로 예상되는 페이로드를 설명하는 JSON 스키마를 제공할 수 있습니다. 
  이 디자이너는 이 스키마를 사용하여 JSON 콘텐츠를 구문 분석하고 JSON 콘텐츠의 속성을 나타내는 친숙한 토큰을 생성합니다. 
  그 후 이러한 속성을 논리 앱 워크플로 전체에서 간편하게 참조하고 사용할 수 있습니다. 
  
  스키마가 없는 경우 스키마를 생성할 수 있습니다. 
  
  1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.  
  
  2. **샘플 JSON 페이로드를 입력하거나 붙여넣기**에서 샘플 페이로드를 입력하고 **완료**를 선택합니다. 예를 들면 다음과 같습니다. 

     ![샘플 JSON 페이로드 입력](./media/logic-apps-content-type/request-trigger.png)

     생성된 스키마가 트리거에 나타납니다.

     ![샘플 JSON 페이로드 입력](./media/logic-apps-content-type/generated-schema.png)

     다음은 코드 보기 편집기의 요청 트리거에 대한 기본 정입니다.

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. 요청에서, `Content-Type` 헤더를 포함하고 헤더 값을 `application/json`으로 설정합니다.

* **JSON 구문 분석 작업**

  논리 앱 디자이너에서 이 작업을 사용할 때 JSON 출력을 구문 분석하고 JSON 콘텐츠의 속성을 나타내는 친숙한 토큰을 생성할 수 있습니다. 
  그 후 이러한 속성을 논리 앱 워크플로 전체에서 간편하게 참조하고 사용할 수 있습니다. 요청 트리거와 마찬가지로, 구문 분석할 JSON 콘텐츠를 설명하는 JSON 스키마를 입력하거나 생성할 수 있습니다. 
  이 방법으로 Azure Service Bus, Azure Cosmos DB 등의 데이터를 보다 쉽게 사용할 수 있습니다.

  ![Parse JSON](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>텍스트/일반

`Content-Type` 헤더가 `text/plain`으로 설정된 HTTP 메시지를 논리 앱에서 수신하면 논리 앱은 해당 메시지를 원시 형식으로 저장합니다. 이 메시지를 캐스팅하지 않고 후속 작업에 포함하면 `Content-Type` 헤더가 `text/plain`으로 설정된 상태로 요청이 전송됩니다. 

예를 들어 플랫 파일을 작업할 때 `Content-Type` 헤더가 `text/plain` 콘텐츠 형식으로 설정된 HTTP 요청을 받을 수 있습니다.

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

이후 작업에서 이 요청을 다른 요청의 본문으로 보내면(예: `@body('flatfile')`) 두 번째 요청의 `Content-Type` 헤더도 `text/plain`으로 설정됩니다. 일반 텍스트인 데이터를 작업하면서 헤더를 지정하지 않으면 이 언어 식처럼 [string() 함수](../logic-apps/workflow-definition-language-functions-reference.md#string)를 사용하여 수동으로 해당 데이터를 텍스트로 캐스팅할 수 있습니다. 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>application/xml 및 application/octet-stream

Logic Apps는 항상 수신한 HTTP 요청 또는 응답의 `Content-Type`을 유지합니다. 따라서 `Content-Type`이 `application/octet-stream`으로 설정된 컨텐츠를 논리 앱이 수신하고, 사용자가 캐스팅 없이 이 콘텐츠를 후속 작업에 포함하면 보내는 요청의 `Content-Type`도 `application/octet-stream`으로 설정됩니다. 이 방법으로 Logic Apps는 워크플로를 통해 이동하는 데이터의 무손실을 보장할 수 있습니다. 그러나 상태가 워크플로를 통해 이동할 때 작업 상태 또는 입력 및 출력이 JSON 개체에 저장됩니다. 

## <a name="converter-functions"></a>변환기 함수

일부 데이터 형식을 유지하기 위해, Logic Apps는 자동으로 변환되는 `$content` 및 `$content-type` 페이로드를 모두 보존하는 적절한 메타데이터를 사용하여 콘텐츠를 이진 base64 인코딩 문자열로 변환합니다. 

이 목록은 다음 [함수](../logic-apps/workflow-definition-language-functions-reference.md)를 사용할 때 Logic Apps가 콘텐츠를 변환하는 방법을 설명합니다.

* `json()`: 데이터를 캐스팅 `application/json`
* `xml()`: 데이터를 캐스팅 `application/xml`
* `binary()`: 데이터를 캐스팅 `application/octet-stream`
* `string()`: 데이터를 캐스팅 `text/plain`
* `base64()`: 콘텐츠를 base64 문자열로 변환합니다.
* `base64toString()`: Base64로 인코딩된 문자열을 변환 `text/plain`
* `base64toBinary()`: Base64로 인코딩된 문자열을 변환 `application/octet-stream`
* `encodeDataUri()`: 문자열을 dataUri 바이트 배열로 인코딩합니다.
* `decodeDataUri()`: 디코딩 한 `dataUri` 바이트 배열로

예를 들어 이 콘텐츠처럼 `Content-Type`이 `application/xml`로 설정된 HTTP 요청을 받는 경우:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

`@xml(triggerBody())` 식을 `xml()` 및 `triggerBody()` 함수에 사용하여 이 콘텐츠를 캐스팅하면 나중에 이 콘텐츠를 사용할 수 있습니다. 또는 `@xpath(xml(triggerBody()), '/CustomerName')` 식을 `xpath()` 및 `xml()` 함수에 사용할 수 있습니다. 

## <a name="other-content-types"></a>다른 콘텐츠 형식

Logic Apps는 다른 콘텐츠 형식과 호환 및 지원되지만 `$content` 변수를 디코딩하여 메시지 본문을 직접 가져와야 할 수도 있습니다.

예를 들어 콘텐츠 형식이 `application/x-www-url-formencoded`인 요청을 통해 논리 앱이 트리거되었다고 가정해 봅시다. 모든 데이터를 유지하기 위해 요청 본문의 `$content` 변수에는 base64 문자열로 인코딩된 페이로드가 있습니다.

`CustomerName=Frank&Address=123+Avenue`

요청이 일반 텍스트 또는 JSON이 아니기 때문에 다음과 같이 작업에 저장됩니다.

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps는 양식 데이터를 처리하기 위한 다음과 같은 네이티브 함수를 제공합니다. 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

또는 이 예제와 같은 언어 식을 사용하여 데이터에 직접 액세스할 수 있습니다.

`@string(body('formdataAction'))` 

나가는 요청에 동일한 `application/x-www-url-formencoded` 콘텐츠 유형 헤더를 포함하려면 `@body('formdataAction')` 같은 언어 식을 사용한 캐스팅 없이 요청을 작업 본문에 추가할 수 있습니다. 그러나 이 방법은 본문이 `body` 입력의 유일한 매개 변수인 경우에만 작동합니다. `application/json` 요청에서 `@body('formdataAction')` 언어 식을 사용하려고 시도하면 인코딩된 본문이 전송되기 때문에 런타임 오류가 발생합니다.
