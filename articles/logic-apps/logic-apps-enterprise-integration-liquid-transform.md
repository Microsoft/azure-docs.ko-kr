---
title: Liquid 변환을 사용하여 JSON 데이터 변환 - Azure Logic Apps | Microsoft Docs
description: Logic Apps 및 Liquid 템플릿을 사용하여 고급 JSON 변환에 대한 변환 또는 맵을 만듭니다.
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 5472a8ce2670a34174d6d39f0d90faca8a7002ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467528"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Azure Logic Apps에서 Liquid 템플릿을 사용하여 고급 JSON 변환 수행

**작성** 또는 **구문 분석 JSON**과 같은 네이티브 데이터 조작 작업을 사용하여 Logic Apps에서 기본 JSON 변환을 수행할 수 있습니다. 고급 JSON 변환을 수행하려면 유연한 웹앱을 위한 오픈 소스 템플릿 언어인 [Liquid](https://shopify.github.io/liquid/)를 사용하여 템플릿 또는 맵을 만들 수 있습니다. Liquid 템플릿을 JSON 출력을 변환 하는 방법을 정의 하 고 반복, 제어 흐름, 변수 등 더 복잡 한 JSON 변환을 지원 합니다. 

논리 앱에서 Liquid 변환을 수행할 수 있습니다, 전에 통합 계정에 매핑되는 Liquid 템플릿 및 저장소를 사용 하 여 JSON 매핑 JSON를 먼저 정의 해야 있습니다. 이 문서에서는 이러한 Liquid 템플릿 또는 맵을 만들고 사용하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 또는 [종량제 구독에 등록합니다](https://azure.microsoft.com/pricing/purchase-options/).

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 기본 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [Liquid 템플릿 언어](https://shopify.github.io/liquid/)에 대한 기본 지식입니다.

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>통합 계정에 대한 Liquid 템플릿 또는 맵 만들기

1. 이 예제에서는 이 단계에 설명된 샘플 Liquid 템플릿을 만듭니다. Liquid 템플릿을 사용할 수 있습니다 [Liquid 필터링](https://shopify.github.io/liquid/basics/introduction/#filters)를 사용 하는 [DotLiquid](https://dotliquidmarkup.org/) 및 C# 명명 규칙입니다. 

   > [!NOTE]
   > 필터 이름을 사용 했는지 *문장 대/소문자 구분* 템플릿에서 합니다. 그렇지 않은 경우 필터가 작동 하지 않습니다.

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

2. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 주 메뉴에서 **모든 리소스**를 선택합니다. 검색 상자에서 통합 계정을 찾고 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  **구성 요소** 아래에서 **맵**을 선택합니다.

    ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. **추가**를 선택하고 맵에 이러한 세부 정보를 제공합니다.

   | 자산 | 값 | 설명 | 
   |----------|-------|-------------|
   | **Name** | JsonToJsonTemplate | 맵의 이름이며, 이 예제에서는 "JsonToJsonTemplate"입니다. | 
   | **맵 유형** | **liquid** | 맵의 형식입니다. JSON부터 JSON 변환의 경우 **Liquid**를 선택해야 합니다. | 
   | **Map** | "SimpleJsonToJsonTemplate.liquid" | 변환에 사용할 기존 Liquid 템플릿이나 맵 파일이며 이 예제에서는 "SimpleJsonToJsonTemplate.liquid"입니다. 이 파일을 찾으려면 파일 선택기를 사용할 수 있습니다. |
   ||| 

   ![Liquid 템플릿 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>JSON 변환에 대한 Liquid 작업 추가

1. Azure Portal에서 다음 단계에 따라 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Logic Apps 디자이너에서 논리 앱에 [요청 트리거](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)를 추가합니다.

3. 트리거 아래에서 **새 단계**를 선택합니다. 
   검색 상자에서 필터로 "liquid"를 입력하고, **JSON을 JSON으로 변환 - Liquid** 작업을 선택합니다.

   ![Liquid 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. **내용** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시하고 **Body** 토큰을 선택합니다.
  
   ![본문 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. **맵** 목록에서 Liquid 템플릿을 선택합니다. 이 예제에서는 "JsonToJsonTemplate"입니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   맵 목록이 비어 있으면 논리 앱이 통합 계정에 연결되어 있지 않을 수 있습니다. 
   Liquid 템플릿 또는 지도를 포함한 통합 계정에 논리 앱을 연결하려면 다음 단계를 따릅니다.

   1. 논리 앱 메뉴에서 **워크플로 설정**을 선택합니다.

   2. **통합 계정 선택** 목록에서 통합 계정을 선택하고 **저장**을 선택합니다.

      ![통합 계정에 논리 앱 연결](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>논리 앱 테스트

[Postman](https://www.getpostman.com/postman) 또는 유사한 도구의 논리 앱에 JSON 입력을 게시합니다. 논리 앱에서 변환된 JSON 출력은 이 예제와 같습니다.
  
![예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>더 많은 Liquid 작업 예제
Liquid는 JSON 변환으로만 제한되지 않습니다. 다음은 Liquid를 사용하는 다른 사용 가능한 변환 작업입니다.

* JSON을 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   샘플 입력 및 출력은 다음과 같습니다.
  
   ![JSON에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML에서 JSON으로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   샘플 입력 및 출력은 다음과 같습니다.

   ![XML에서 JSON으로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML에서 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   샘플 입력 및 출력은 다음과 같습니다.

   ![XML에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  
* [맵에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")  

