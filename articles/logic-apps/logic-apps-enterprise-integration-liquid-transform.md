---
title: 수냉 템플릿으로 JSON 및 XML 변환
description: Azure Logic Apps의 맵으로 액체 템플릿을 사용 하 여 JSON 및 XML 변환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: b3919cbbe0ba7a796a21ae566afb8e2d9fa784db
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716676"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Azure Logic Apps의 맵으로 액체 템플릿을 사용 하 여 JSON 및 XML 변환

논리 앱에서 기본 JSON 변환을 수행 하려는 경우 JSON **작성** 또는 **구문**분석과 같은 네이티브 [데이터 작업](../logic-apps/logic-apps-perform-data-operations.md) 을 사용할 수 있습니다. 반복, 제어 흐름, 변수 등의 요소를 포함 하는 JSON 변환과 고급 및 복잡 한 JSON의 경우에는 [액체](https://shopify.github.io/liquid/) 오픈 소스 템플릿 언어를 사용 하 여 이러한 변환을 설명 하는 템플릿을 만들고 사용 합니다. JSON에서 텍스트, XML에서 JSON 및 XML에서 텍스트로의 [다른 변환을 수행할](#other-transformations)수도 있습니다.

논리 앱에서 액체 변환을 수행 하려면 먼저 원하는 매핑을 정의 하는 액체 템플릿을 만들어야 합니다. 그런 다음 템플릿을 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)에 [맵으로 업로드](../logic-apps/logic-apps-enterprise-integration-maps.md) 합니다. Json **을 json으로 변환-액체** 작업을 논리 앱에 추가 하는 경우 사용할 동작에 대 한 맵으로 액체 템플릿을 선택할 수 있습니다.

이 문서에서는 다음 작업을 완료 하는 방법을 보여 줍니다.

* 액체 템플릿을 만듭니다.
* 통합 계정에 템플릿을 추가 합니다.
* 사용자의 논리 앱에 액체 변형 작업을 추가 합니다.
* 사용 하려는 맵으로 템플릿을 선택 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [수냉 템플릿 언어](https://shopify.github.io/liquid/) 에 대 한 기본 지식

  > [!NOTE]
  > Json **을 json으로 변환-액체** 작업은 액체에 대 한 [DotLiquid 구현을](https://github.com/dotliquid/dotliquid)따릅니다 .이는 액체에 [대 한 Shopify 구현과](https://shopify.github.io/liquid)관련 된 특정 사례에서 차이가 있습니다. 자세한 내용은 [액체 템플릿 고려 사항](#liquid-template-considerations)을 참조 하세요.

## <a name="create-the-template"></a>템플릿 만들기

1. JSON 변환에 대 한 맵으로 사용 하는 액체 템플릿을 만듭니다. 원하는 모든 편집 도구를 사용할 수 있습니다.

   이 예제에서는이 섹션에 설명 된 대로 샘플 액체 템플릿을 만듭니다.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. 확장을 사용 하 여 템플릿을 저장 `.liquid` 합니다. 이 예에서는 `SimpleJsonToJsonTemplate.liquid`를 사용합니다.

## <a name="upload-the-template"></a>템플릿 업로드

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정**을 선택 합니다.

   !["통합 계정" 찾기](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. 통합 계정을 찾아 선택 합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. **개요** 창의 **구성 요소**에서 **맵**을 선택 합니다.

    !["맵" 타일 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. **지도** 창에서 **추가** 를 선택 하 고 맵에 대 한 세부 정보를 제공 합니다.

   | 속성 | 값 | Description |
   |----------|-------|-------------|
   | **이름** | `JsonToJsonTemplate` | 맵의 이름이며, 이 예제에서는 "JsonToJsonTemplate"입니다. |
   | **맵 유형** | **liquid** | 맵의 형식입니다. JSON부터 JSON 변환의 경우 **Liquid**를 선택해야 합니다. |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | 변환에 사용할 기존 Liquid 템플릿이나 맵 파일이며 이 예제에서는 "SimpleJsonToJsonTemplate.liquid"입니다. 이 파일을 찾으려면 파일 선택기를 사용할 수 있습니다. 지도 크기 제한에 대해서는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)을 참조 하세요. |
   |||

   ![액체 템플릿 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>액체 변형 작업 추가

1. Azure Portal에서 다음 단계에 따라 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Logic Apps 디자이너에서 논리 앱에 [요청 트리거](../connectors/connectors-native-reqres.md#add-request)를 추가합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다. 검색 상자에서를 필터로 입력 하 `liquid` 고이 작업: json **을 Json으로 변환-액체** 를 선택 합니다.

   ![Liquid 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. **지도** 목록을 열고이 예제에서 "JsonToJsonTemplate" 인 액체 템플릿을 선택 합니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   맵 목록이 비어 있는 경우 논리 앱이 통합 계정에 연결 되지 않았을 가능성이 높습니다. 
   Liquid 템플릿 또는 지도를 포함한 통합 계정에 논리 앱을 연결하려면 다음 단계를 따릅니다.

   1. 논리 앱 메뉴에서 **워크플로 설정**을 선택합니다.

   1. **통합 계정 선택** 목록에서 통합 계정을 선택 하 고 **저장**을 선택 합니다.

      ![통합 계정에 논리 앱 연결](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. 이제이 작업에 **Content** 속성을 추가 합니다. **새 매개 변수 추가** 목록을 열고 **콘텐츠**를 선택 합니다.

   ![작업에 "콘텐츠" 속성 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. **Content** 속성 값을 설정 하려면 **콘텐츠** 상자 내부를 클릭 하 여 동적 콘텐츠 목록이 표시 되도록 합니다. 트리거의 본문 내용 출력을 나타내는 **본문** 토큰을 선택 합니다.

   !["Content" 속성 값의 "Body" 토큰을 선택 합니다.](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   완료되면 작업은 다음 예제와 같습니다.

   !["Json을 JSON으로 변환" 작업 완료](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>논리 앱 테스트

[Postman](https://www.getpostman.com/postman) 또는 유사한 도구를 사용 하 여 논리 앱에 JSON 입력을 게시 합니다. 논리 앱에서 변환된 JSON 출력은 이 예제와 같습니다.

![예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>액체 템플릿 고려 사항

* 액체 템플릿은 Azure Logic Apps [맵의 파일 크기 제한을](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) 따릅니다.

* Json에서 json **으로 변환-액체** 작업은 [액체에 대 한 DotLiquid 구현을](https://github.com/dotliquid/dotliquid)따릅니다. 이 구현은 [Shopify 구현에서 액체에 대 한](https://shopify.github.io/liquid/) .NET Framework 포트 이며 [특정 사례](https://github.com/dotliquid/dotliquid/issues)에서 다릅니다.

  알려진 차이점은 다음과 같습니다.

  * Json **을 json으로 변환-액체** 작업은 JSON, XML, HTML 등을 포함할 수 있는 문자열을 기본적으로 출력 합니다. 액체 작업은 액체 템플릿의 예상 텍스트 출력이 JSON 문자열 임을 나타냅니다. 작업은 입력을 JSON 개체로 구문 분석 하 고, 액체에서 JSON 구조를 해석할 수 있도록 래퍼를 적용 하도록 논리 앱에 지시 합니다. 변환 후 작업은 텍스트 출력을 액체에서 JSON으로 구문 분석 하도록 논리 앱에 지시 합니다.

    DotLiquid은 기본적으로 JSON을 인식 하지 않으므로 백슬래시 문자 ( `\` )와 기타 예약 된 JSON 문자를 이스케이프 해야 합니다.

  * 템플릿이 [액체 필터](https://shopify.github.io/liquid/basics/introduction/#filters)를 사용 하는 경우 [DotLiquid 및 c # 명명 규칙](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing)을 준수 해야 합니다 .이 규칙은 *문장 대/소문자*를 사용 합니다. 모든 액체 변환에 대해 템플릿의 필터 이름이 문장 대/소문자도 사용 하는지 확인 합니다. 그렇지 않으면 필터가 작동 하지 않습니다.

    예를 들어 필터를 사용 하는 경우 `replace` `Replace` not을 사용 `replace` 합니다. [DotLiquid online](http://dotliquidmarkup.org/try-online)에서 예제를 사용해 보면 동일한 규칙이 적용 됩니다. 자세한 내용은 [Shopify 액체 필터](https://shopify.dev/docs/themes/liquid/reference/filters) 및 [DotLiquid 액체 필터](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters)를 참조 하세요. Shopify 사양에는 각 필터에 대 한 예제가 포함 되어 있으므로 비교를 위해 [DotLiquid-온라인](https://dotliquidmarkup.org/try-online)에서 다음 예제를 시도할 수 있습니다.

  * `json`Shopify 확장 필터의 필터는 현재 [DotLiquid에서 구현 되지 않습니다](https://github.com/dotliquid/dotliquid/issues/384). 일반적으로이 필터를 사용 하 여 JSON 문자열 구문 분석에 대 한 텍스트 출력을 준비할 수 있습니다. 대신 필터를 사용 해야 `Replace` 합니다.

  * `Replace` [DotLiquid 구현의](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) 표준 필터는 [정규식 (RegEx) 일치](/dotnet/standard/base-types/regular-expression-language-quick-reference)를 사용 하는 반면 [Shopify 구현](https://shopify.github.io/liquid/filters/replace/) 에서는 [간단한 문자열 일치](https://github.com/Shopify/liquid/issues/202)를 사용 합니다. 두 구현 모두 match 매개 변수에서 RegEx 예약 문자 또는 이스케이프 문자를 사용할 때와 동일한 방식으로 작동 하는 것 처럼 보입니다.

    예를 들어 RegEx로 예약 된 백슬래시 ( `\` ) 이스케이프 문자를 이스케이프 하려면 `| Replace: '\\', '\\'` 를 사용 하 고는 사용 하지 않고를 사용 `| Replace: '\', '\\'` 합니다. 다음 예에서는 `Replace` 백슬래시 문자를 이스케이프 하려고 할 때 필터가 다르게 동작 하는 방식을 보여 줍니다. 이 버전은 정상적으로 작동 합니다.

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    그러면 다음 결과가 나타납니다.

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    이 버전은 실패 합니다.

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    다음 오류가 발생 했습니다.

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    자세한 내용은 [표준 필터에서 RegEx 패턴 일치를 사용](https://github.com/dotliquid/dotliquid/issues/385)합니다 .를 참조 하세요.

  * `Sort` [DotLiquid 구현의](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) 필터는 배열 또는 컬렉션의 항목을 속성으로 정렬 하지만 다음과 같은 차이점이 있습니다.<p>

    * [Shopify의 정렬 동작이](https://shopify.github.io/liquid/filters/sort/)아닌 [Shopify의 sort_natural 동작](https://shopify.github.io/liquid/filters/sort_natural/)을 따릅니다.

    * 문자열 영숫자 순서만을 기준으로 정렬 합니다. 자세한 내용은 [Numeric sort](https://github.com/Shopify/liquid/issues/980)를 참조 하세요.

    * 대/소문자를 *구분* 하지 않고 대/소문자를 구분 하지 않는 순서를 사용 합니다. 자세한 내용은 [정렬 필터는 Shopify의 사양에서 대/소문자 구분 동작을 따르지 않습니다]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>액체를 사용 하는 기타 변환

액체는 JSON 변환 으로만 제한 되지 않습니다. 또한 액체를 사용 하 여 다른 변환을 수행할 수도 있습니다. 예를 들면 다음과 같습니다.

* [JSON에서 텍스트로](#json-text)
* [XML에서 JSON으로](#xml-json)
* [XML에서 텍스트로](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON을 텍스트로 변환

다음은이 예제에 사용 되는 액체 템플릿입니다.

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

샘플 입력 및 출력은 다음과 같습니다.

![JSON에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML에서 JSON으로 변환

다음은이 예제에 사용 되는 액체 템플릿입니다.

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

`JSONArrayFor`루프는 후행 쉼표를 사용 하지 않는 JSON 페이로드를 만들 수 있도록 XML 입력에 대 한 사용자 지정 루핑 메커니즘입니다. 또한 `where` 이 사용자 지정 반복 메커니즘의 조건은 다른 액체 필터와 같은 요소의 값이 아닌 비교에 XML 요소 이름을 사용 합니다. 자세한 내용은 [집합 본문 정책에 대 한 심층 정보-항목 컬렉션](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy)을 참조 하세요.

샘플 입력 및 출력은 다음과 같습니다.

![XML에서 JSON으로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML에서 텍스트로 변환

다음은이 예제에 사용 되는 액체 템플릿입니다.

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

샘플 입력 및 출력은 다음과 같습니다.

![XML에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>다음 단계

* [Shopify 수냉 언어 및 예제](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid-온라인으로 시도](https://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub 문제](https://github.com/dotliquid/dotliquid/issues/)
* [Maps](../logic-apps/logic-apps-enterprise-integration-maps.md) 에 대 한 자세한 정보
