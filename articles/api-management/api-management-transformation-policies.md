---
title: Azure API Management 변환 정책 | Microsoft Docs
description: Azure API Management에서 사용할 수 있는 변환 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 72348085a69746306e40029bc7473df271b60221
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946612"
---
# <a name="api-management-transformation-policies"></a>API Management 변환 정책
이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

##  <a name="TransformationPolicies"></a> 변환 정책

-   [XML로 JSON 변환](api-management-transformation-policies.md#ConvertJSONtoXML) - 요청 또는 응답 본문을 JSON에서 XML로 변환합니다.

-   [JSON으로 XML 변환](api-management-transformation-policies.md#ConvertXMLtoJSON) - 요청 또는 응답 본문을 XML에서 JSON으로 변환합니다.

-   [본문 문자열 찾기 및 바꾸기](api-management-transformation-policies.md#Findandreplacestringinbody) - 요청 또는 응답 하위 문자열을 찾아 다른 하위 문자열로 바꿉니다.

-   [콘텐츠의 URL 마스킹](api-management-transformation-policies.md#MaskURLSContent) - 응답 본문의 링크를 다시 써서(마스킹) 게이트웨이를 통해 동일한 링크를 가리키도록 합니다.

-   [백 엔드 서비스 설정](api-management-transformation-policies.md#SetBackendService) - 들어오는 요청의 백 엔드 서비스를 변경합니다.

-   [본문 설정](api-management-transformation-policies.md#SetBody) -들어오고 나가는 요청의 메시지 본문을 설정합니다.

-   [HTTP 헤더 설정](api-management-transformation-policies.md#SetHTTPheader) - 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새로운 응답 및/또는 요청 헤더를 추가합니다.

-   [쿼리 문자열 매개 변수 설정](api-management-transformation-policies.md#SetQueryStringParameter) - 요청 쿼리 문자열 매개 변수를 추가하거나 그 값을 바꾸거나 삭제합니다.

-   [URL 다시 쓰기](api-management-transformation-policies.md#RewriteURL) - 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.

-   [XSLT를 사용하여 XML 변환](api-management-transformation-policies.md#XSLTransform) - 요청 또는 응답 본문의 XML에 XSL 변환을 적용합니다.

##  <a name="ConvertJSONtoXML"></a> XML로 JSON 변환
 `json-to-xml` 정책은 요청 또는 응답 본문을 JSON에서 XML로 변환합니다.

### <a name="policy-statement"></a>정책 문

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|json-to-xml|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|apply|속성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> - always: 항상 전환을 적용합니다.<br />- content-type-json: 응답 Content-Type 헤더에서 JSON의 존재를 나타내는 경우에만 변환합니다.|예|N/A|
|consider-accept-header|속성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> - true: 요청 Accept 헤더에서 JSON을 요청하는 경우 변환을 적용합니다.<br />- false: 항상 전환을 적용합니다.|아닙니다.|true|
|parse-date|`false`로 설정하면 변환 중에 날짜 값이 복사됩니다.|아닙니다.|true|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, on-error

-   **정책 범위:** global, product, API, operation

##  <a name="ConvertXMLtoJSON"></a> JSON으로 XML 변환
 `xml-to-json` 정책은 요청 또는 응답 본문을 XML에서 JSON으로 변환합니다. 이 정책은 XML 전용 백 엔드 웹 서비스를 기반으로 하는 API를 최신 형식으로 변환하는 데 사용할 수 있습니다.

### <a name="policy-statement"></a>정책 문

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|xml-to-json|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|kind|속성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> - javascript-friendly: 변환된 JSON에는 JavaScript 개발자에게 익숙한 양식이 있습니다.<br />- direct: 변환된 JSON은 원래 XML 문서의 구조를 반영합니다.|예|N/A|
|apply|속성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> - always: 항상 변환합니다.<br />- content-type-xml: 응답 Content-Type 헤더에서 XML의 존재를 나타내는 경우에만 변환합니다.|예|N/A|
|consider-accept-header|속성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> - true: 요청 Accept 헤더에서 XML을 요청하는 경우 변환을 적용합니다.<br />- false: 항상 전환을 적용합니다.|아닙니다.|true|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, on-error

-   **정책 범위:** global, product, API, operation

##  <a name="Findandreplacestringinbody"></a> 본문 문자열 찾기 및 바꾸기
 `find-and-replace` 정책은 요청 또는 응답 하위 문자열을 찾고 다른 하위 문자열로 바꿉니다.

### <a name="policy-statement"></a>정책 문

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>예

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|find-and-replace|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|from|검색할 문자열|예|N/A|
|to|대체 문자열입니다. 검색 문자열을 제거하려면 길이가 0인 대체 문자열을 지정합니다.|예|N/A|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** global, product, API, operation

##  <a name="MaskURLSContent"></a> 콘텐츠의 URL 마스킹
 `redirect-content-urls` 정책은 응답 본문에 링크를 다시 작성(마스킹)하여 게이트웨이를 통해 동등한 링크를 가리키도록 합니다. outbound 섹션에서 응답 본문 링크를 다시 작성하여 게이트웨이를 가리키도록 합니다. 반대 효과를 가져오려면 inbound 섹션에 사용합니다.

> [!NOTE]
>  이 정책은 `Location` 헤더와 같은 헤더 값을 변경하지 않습니다. 헤더 값을 변경하려면 [set-header](api-management-transformation-policies.md#SetHTTPheader) 정책을 사용합니다.

### <a name="policy-statement"></a>정책 문

```xml
<redirect-content-urls />
```

### <a name="example"></a>예

```xml
<redirect-content-urls />
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|redirect-content-urls|루트 요소입니다.|예|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound

-   **정책 범위:** global, product, API, operation

##  <a name="SetBackendService"></a> 백 엔드 서비스 설정
 `set-backend-service` 정책을 사용하여 들어오는 요청을 해당 작업의 API 설정에 지정된 것과 다른 백 엔드로 리디렉션합니다. 이 정책은 들어오는 요청의 백 엔드 서비스 기준 URL을 정책에 지정된 URL로 변경합니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-backend-service base-url="base URL of the backend service" />
```

또는

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> 관리를 통해 백 엔드 엔터티를 관리할 수 있습니다 [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) 하 고 [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement)합니다.

### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
이 예제에서 백 엔드 서비스 설정 정책은 쿼리 문자열에 전달된 버전 값에 기반한 요청을 API에 지정된 것과 다른 백 엔드 서비스로 라우팅합니다.

처음에는 백 엔드 서비스 기준 URL이 API 설정에서 파생되었습니다. 따라서 `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` 요청 URL은 `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef`가 되고, 여기서 `http://contoso.com/api/10.4/`는 API 설정에 지정된 백 엔드 서비스 URL입니다.

[<choose\>](api-management-advanced-policies.md#choose) 정책 문을 적용하면 백 엔드 서비스 기준 URL은 버전 요청 쿼리 매개 변수의 값에 따라 `http://contoso.com/api/8.2` 또는 `http://contoso.com/api/9.1`로 다시 변경될 수 있습니다. 예를 들어 값이 `"2013-15"`이면 최종 요청 URL은 `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`가 됩니다.

추가 변환 요청이 필요한 경우 다른 [변환 정책](api-management-transformation-policies.md#TransformationPolicies)을 사용할 수 있습니다. 예를 들어 요청이 버전 특정 백 엔드로 라우팅되고 있어 버전 쿼리 매개 변수를 제거하려면 [쿼리 문자열 설정 매개 변수](api-management-transformation-policies.md#SetQueryStringParameter) 정책을 사용하여 현재의 중복 버전 특성을 제거할 수 있습니다.

### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
이 예제에서 정책은 userId 쿼리 문자열을 파티션 키로 사용하고 파티션의 주 복제본을 사용하여 서비스 패브릭 백 엔드로 요청을 라우팅합니다.

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|set-backend-service|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|base-url|새 백 엔드 서비스 기준 URL입니다.|중 하나 `base-url` 또는 `backend-id` 있어야 합니다.|N/A|
|backend-id|라우팅할 백 엔드의 식별자입니다. (백 엔드 엔터티를 통해 관리 됩니다 [API](https://docs.microsoft.com/en-us/rest/api/apimanagement/backend) 하 고 [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|중 하나 `base-url` 또는 `backend-id` 있어야 합니다.|N/A|
|sf-partition-key|백 엔드가 Service Fabric 서비스이고 'backend-id'를 사용하여 지정된 경우에만 적용됩니다. 이름 확인 서비스에서 특정 파티션을 확인하는 데 사용됩니다.|아닙니다.|N/A|
|sf-replica-type|백 엔드가 Service Fabric 서비스이고 'backend-id'를 사용하여 지정된 경우에만 적용됩니다. 요청이 파티션의 주 복제본으로 이동되는지, 보조 복제본으로 이동되는지를 제어합니다. |아닙니다.|N/A|
|sf-resolve-condition|백 엔드가 Service Fabric 서비스인 경우에만 적용됩니다. 새로 확인할 때마다 Service Fabric 백 엔드에 대한 호출을 반복해야 하는지를 식별하는 조건입니다.|아닙니다.|N/A|
|sf-service-instance-name|백 엔드가 Service Fabric 서비스인 경우에만 적용됩니다. 런타임에 서비스 인스턴스를 변경할 수 있습니다. |아닙니다.|N/A|
|sf-listener-name|백 엔드가 Service Fabric 서비스이고 'backend-id'를 사용하여 지정된 경우에만 적용됩니다. Service Fabric Reliable Services를 사용하면 서비스에서 여러 수신기를 만들 수 있습니다. 백 엔드 Reliable Service에 둘 이상의 수신기가 있을 때 이 특성은 사용하여 특정 수신기를 선택합니다. 이 특성을 지정하지 않으면 API Management에서는 이름 없이 수신기를 사용하려고 합니다. 이름이 없는 수신기는 수신기가 하나만 있는 Reliable Services에 일반적입니다. |아닙니다.|N/A|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, backend

-   **정책 범위:** global, product, API, operation

##  <a name="SetBody"></a> 본문 설정
 `set-body` 정책을 사용하여 들어오고 나가는 요청의 메시지 본문을 설정합니다. 메시지 본문에 액세스하려면 정책이 inbound 또는 outbound 섹션에 있는지에 따라 `context.Request.Body` 또는 `context.Response.Body` 속성을 사용할 수 있습니다.

> [!IMPORTANT]
>  기본적으로 `context.Request.Body` 또는 `context.Response.Body`를 사용하여 메시지 본문에 액세스하면 원래 메시지 본문이 손실되며 해당 본문을 식으로 다시 반환하여 설정해야 합니다. 본문 내용을 보존하려면 메시지에 액세스할 때 `preserveContent` 매개 변수를 `true`로 설정합니다. `preserveContent`가 `true`로 설정되고 식에서 다른 본문이 반환되면 반환된 본문을 사용합니다.
> 
>  `set-body` 정책을 사용할 때 다음을 고려해야 합니다.
> 
> - `set-body` 정책을 사용하여 새 본문 또는 업데이트된 본문을 반환하는 경우 명시적으로 새 본문 내용을 제공하므로 `preserveContent`를 `true`로 설정할 필요가 없습니다.
>   -   인바운드 파이프라인에서 응답 내용을 보존하는 것은 아직 응답이 없기 때문에 이치에 맞지 않습니다.
>   -   아웃바운드 파이프라인에서 요청 내용을 보존하는 것은 이 시점에서 요청을 이미 백 엔드로 보냈기 때문에 이치에 맞지 않습니다.
>   -   인바운드 GET에서와 같이 메시지 본문이 없을 때 이 정책을 사용하면 예외가 발생합니다.

 자세한 내용은 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables) 표의 `context.Request.Body`, `context.Response.Body` 및 `IMessage` 섹션을 참조하세요.

### <a name="policy-statement"></a>정책 문

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>예

#### <a name="literal-text-example"></a>리터럴 텍스트 예제

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>문자열로 액세스하는 예제 파이프라인에서 나중에 액세스할 수 있도록 원래 요청 본문을 유지합니다.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>JObject로 본문에 액세스하는 예제 원래 요청 본문을 보존하지 않으므로 파이프라인에서 나중에 액세스하면 예외가 발생합니다.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>제품 기준 응답 필터링
 이 예제에서는 `Starter` 제품을 사용할 때 백 엔드 서비스에서 받은 응답의 데이터 요소를 제거하여 콘텐츠 필터링을 수행하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky를 사용하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)을 참조하고 34분 30초로 빠르게 돌리세요. 31분 50초 재생 시점에서 시작하는 [Dark Sky Forecast API](https://developer.forecast.io/)(영문) 개요를 확인하세요.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="using-liquid-templates-with-set-body"></a>본문이 설정된 Liquid 템플릿 사용
[Liquid](https://shopify.github.io/liquid/basics/introduction/) 템플릿 언어를 사용하여 요청 또는 응답의 본문을 변환하도록 `set-body` 정책을 구성할 수 있습니다. 이 방법은 메시지 형식을 완전히 바꾸어야 하는 경우에 매우 효과적일 수 있습니다.

> [!IMPORTANT]
> `set-body` 정책에 사용되는 Liquid 구현은 'C# 모드'로 구성됩니다. 이러한 방식은 필터링과 같은 작업을 수행하는 경우에 특히 중요합니다. 예를 들어 날짜 필터를 사용하려면 다음과 같이 파스칼식 대/소문자 및 C# 날짜 형식을 사용해야 합니다.
>
> {{body.foo.startDateTime| Date:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Liquid 템플릿을 사용하여 XML 본문에 올바르게 바인딩하려면 `set-header` 정책을 사용하여 Content-Type을 application/xml, text/xml(또는 +xml로 끝나는 임의 형식)로 설정하세요. JSON 본문의 경우에는 application/json, text/json(또는 +json으로 끝나는 임의 형식)이어야 합니다.

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Liquid 템플릿을 사용하여 JSON을 SOAP으로 변환
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="transform-json-using-a-liquid-template"></a>Liquid 템플릿을 사용 하 여 JSON 변환
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|set-body|루트 요소입니다. 본문 텍스트 또는 본문을 반환하는 식을 포함합니다.|예|

### <a name="properties"></a>properties

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|template|본문 설정 정책이 실행될 템플릿 모드를 변경하는 데 사용됩니다. 현재 지원되는 유일한 값:<br /><br />- liquid - 본문 설정 정책은 liquid 템플릿 엔진을 사용합니다. |아닙니다.|liquid|

요청 및 응답에 대한 정보에 액세스할 수 있도록 Liquid 템플릿은 다음 속성을 갖는 컨텍스트 개체에 바인딩할 수 있습니다. <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend

-   **정책 범위:** global, product, API, operation

##  <a name="SetHTTPheader"></a> HTTP 헤더 설정
 `set-header` 정책은 기존 응답 및/또는 요청 헤더에 값을 할당하거나 새 응답 및/또는 요청 헤더를 추가합니다.

 HTTP 헤더 목록을 HTTP 메시지에 삽입합니다. 이 정책을 인바운드 파이프라인에 지정하면 이 정책은 대상 서비스로 전달되는 요청의 HTTP 헤더를 설정합니다. 이 정책은 아웃바운드 파이프라인에 배치되는 경우 게이트웨이의 클라이언트로 보내는 응답의 HTTP 헤더를 설정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>예

#### <a name="example"></a>예

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>컨텍스트 정보를 백 엔드 서비스로 전달
 이 예제에서는 백 엔드 서비스에 컨텍스트 정보를 제공하기 위해 API 수준에서 정책을 적용하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky를 사용하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)을 참조하고 10분 30초로 빠르게 돌리세요. 12분 10초 재생 시점에는 개발자 포털에서 작업을 호출하는 데모가 있으며, 여기서 작동 중인 정책을 볼 수 있습니다.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

> [!NOTE]
> 헤더의 여러 값 예를 들어 CSV 문자열로 연결 됩니다. `headerName: value1,value2,value3`
>
> 예외에는 표준화된 헤더가 포함됩니다. 값은 다음과 같습니다.
> - 쉼표를 포함할 수 있고(`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - 날짜를 포함할 수 있고(`Cookie`, `Set-Cookie`, `Warning`),
> - 날짜를 포함합니다(`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since` , `Last-Modified`, `Retry-After`).
>
> 해당 예외를 발생 시 여러 헤더 값을 하나의 문자열로 연결 수는 및 예를 들어 별도 헤더로 전달 됩니다. `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|set-header|루트 요소입니다.|예|
|값|설정할 헤더의 값을 지정합니다. 동일한 이름을 가진 여러 헤더에 대해서는 추가 `value` 요소를 추가합니다.|예|

### <a name="properties"></a>properties

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|exists-action|헤더가 이미 지정되어 있는 경우 수행할 작업을 지정합니다. 이 특성에는 다음 값 중 하나가 있어야 합니다.<br /><br /> - override: 기존 헤더 값을 바꿉니다.<br />- skip: 기존 헤더 값을 바꾸지 않습니다.<br />- append: 기존 헤더 값에 값을 추가합니다.<br />- delete: 요청에서 헤더를 제거합니다.<br /><br /> `override`로 설정할 때 동일한 이름의 여러 항목을 등록하면 모든 항목(여러 번 나열됨)에 따라 헤더가 설정되며, 나열된 값만 결과에 설정됩니다.|아닙니다.|override|
|이름|설정할 헤더의 이름을 지정합니다.|예|N/A|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound, backend, on-error

-   **정책 범위:** global, product, API, operation

##  <a name="SetQueryStringParameter"></a> 쿼리 문자열 매개 변수 설정
 `set-query-parameter` 정책은 요청 쿼리 문자열 매개 변수의 추가, 값 바꾸기 또는 삭제를 수행합니다. 백 엔드 서비스에 필요한 쿼리 매개 변수를 전달하는 데 사용하며, 이러한 매개 변수는 선택적이거나 요청에 절대로 존재하지 않습니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>예

#### <a name="example"></a>예

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>컨텍스트 정보를 백 엔드 서비스로 전달
 이 예제에서는 백 엔드 서비스에 컨텍스트 정보를 제공하기 위해 API 수준에서 정책을 적용하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky를 사용하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)을 참조하고 10분 30초로 빠르게 돌리세요. 12분 10초 재생 시점에는 개발자 포털에서 작업을 호출하는 데모가 있으며, 여기서 작동 중인 정책을 볼 수 있습니다.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|set-query-parameter|루트 요소입니다.|예|
|값|설정할 쿼리 매개 변수의 값을 지정합니다. 동일한 이름을 가진 여러 쿼리 매개 변수에 대해서는 추가 `value` 요소를 추가합니다.|예|

### <a name="properties"></a>properties

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|exists-action|쿼리 매개 변수가 이미 지정되어 있는 경우 수행할 작업을 지정합니다. 이 특성에는 다음 값 중 하나가 있어야 합니다.<br /><br /> - override: 기존 쿼리 매개 변수 값을 바꿉니다.<br />- skip: 기존 쿼리 매개 변수 값을 바꾸지 않습니다.<br />- append: 기존 쿼리 매개 변수 값에 값을 추가합니다.<br />- delete: 요청에서 쿼리 매개 변수를 제거합니다.<br /><br /> `override`로 설정할 때 동일한 이름의 여러 항목을 등록하면 모든 항목(여러 번 나열됨)에 따라 쿼리 매개 변수가 설정되며, 나열된 값만 결과에 설정됩니다.|아닙니다.|override|
|이름|설정할 쿼리 매개 변수의 이름을 지정합니다.|예|N/A|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, backend

-   **정책 범위:** global, product, API, operation

##  <a name="RewriteURL"></a> URL 다시 쓰기
 `rewrite-uri` 정책은 다음 예제와 같이 요청 URL을 공용 양식에서 웹 서비스에 필요한 양식으로 변환합니다.

- 공용 URL - `http://api.example.com/storenumber/ordernumber`

- 요청 URL - `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  이 정책은 사람 및/또는 브라우저에 친숙한 URL을 웹 서비스에 필요한 URL 형식으로 변환해야 하는 경우에 사용할 수 있습니다. 이 정책은 쿼리 문자열을 포함하지 않고 리소스 경로(체계 및 권한 뒤)만 포함하는 전적으로 구조적인 URL인 간편 URL, RESTful URL, 사용자에게 친숙한 URL 또는 SEO 지원 URL과 같은 대체 URL 형식을 표시할 때만 적용해야 합니다. 보통 미학, 사용 편의성 또는 SEO(검색 엔진 최적화)를 위해 사용합니다.

> [!NOTE]
>  정책을 사용하여 쿼리 문자열 매개 변수만 추가할 수 있습니다. 다시 쓰기 URL에 추가 템플릿 경로 매개 변수를 추가할 수 없습니다.

### <a name="policy-statement"></a>정책 문

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|rewrite-uri|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|특성|설명|필수|기본값|
|---------------|-----------------|--------------|-------------|
|template|모든 쿼리 문자열 매개 변수가 포함된 실제 웹 서비스 URL입니다. 식을 사용하는 경우 전체 값이 식이어야 합니다.|예|N/A|
|copy-unmatched-params|원본 URL 템플릿에 없는 들어오는 요청의 쿼리 매개 변수가 re-write 템플릿에 의해 정의된 URL에 추가되는지 여부를 지정합니다.|아닙니다.|true|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** global, product, API, operation

##  <a name="XSLTransform"></a> XSLT를 사용하여 XML 변환
 `Transform XML using an XSLT` 정책은 요청 또는 응답 본문의 XML에 XSL 변환을 적용합니다.

### <a name="policy-statement"></a>정책 문

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>예

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|xsl-transform|루트 요소입니다.|예|
|매개 변수|변환에 사용되는 변수를 정의하는 데 사용됩니다.|아닙니다.|
|xsl:stylesheet|루트 스타일시트 요소입니다. 표준 [XSLT 사양](https://www.w3.org/TR/xslt)(영문)에 정의된 모든 요소와 특성입니다.|예|

### <a name="usage"></a>사용 현황
 이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound

-   **정책 범위:** global, product, API, operation

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 항목을 참조하십시오.

+ [API Management의 정책](api-management-howto-policies.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)
