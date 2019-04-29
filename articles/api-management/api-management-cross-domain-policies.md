---
title: Azure API Management 도메인 간 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 도메인 간 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: ecbc1af97ce5ed158138f2bcf47f5729842c0fe9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657495"
---
# <a name="api-management-cross-domain-policies"></a>도메인 정책 간 API Management
이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

## <a name="CrossDomainPolicies"></a> 도메인 간 정책

- [도메인 간 호출 허용](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 합니다.
- [CORS](api-management-cross-domain-policies.md#CORS) - CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.
- [JSONP](api-management-cross-domain-policies.md#JSONP) - 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.

## <a name="AllowCrossDomainCalls"></a> 도메인 간 호출 허용
`cross-domain` 정책을 사용하여 API를 Adobe Flash 및 Microsoft Silverlight 브라우저 기반 클라이언트에서 액세스할 수 있도록 설정합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>예

```xml
<cross-domain>
    <cross-domain-policy>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain-policy>
</cross-domain>
```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cross-domain|루트 요소입니다. 자식 요소는 [Adobe 도메인 간 정책 파일 사양](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)을 준수해야 합니다.|예|

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** global

## <a name="CORS"></a> CORS
`cors` 정책은 CORS(Cross-Origin Resource Sharing) 지원을 작업 또는 API에 추가하여 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다.

CORS를 통해 브라우저와 서버가 상호 작용하여 특정 원본 간 요청(즉, 웹 페이지의 JavaScript에서 다른 도메인으로 실행한 XMLHttpRequests 호출)을 허용할지 여부를 결정할 수 있습니다. 따라서 동일 원본의 요청만 허용하는 것보다 유연성이 더 뛰어나며 모든 원본 간 요청을 허용하는 것보다 보안도 더 높아집니다.

### <a name="policy-statement"></a>정책 문

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>예
이 예제에서는 GET 및 POST 이외의 메서드 또는 사용자 지정 헤더를 비롯하여 사전 요청을 지원하는 방법을 보여 줍니다. 사용자 지정 헤더 및 추가 HTTP 동사를 지원하려면 다음 예제와 같이 `allowed-methods` 및 `allowed-headers` 섹션을 사용하세요.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>요소

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|cors|루트 요소입니다.|예|N/A|
|allowed-origins|도메인 간 요청에 대해 허용되는 원본을 설명하는 `origin` 요소를 포함합니다. `allowed-origins`는 모든 원본을 허용하도록 `*`를 지정하는 단일 `origin` 요소 또는 URI를 포함하는 하나 이상의 `origin` 요소를 포함할 수 있습니다.|예|N/A|
|원본|값은 모든 원본을 허용하는 `*`이거나 단일 원본을 지정하는 URI일 수 있습니다. URI에는 체계, 호스트 및 포트가 포함되어야 합니다.|예|URI에서 포트를 생략하면 HTTP에 포트 80이 사용되고 HTTPS에 포트 443이 사용됩니다.|
|allowed-methods|GET 또는 POST 이외의 메서드가 허용되는 경우 이 요소가 필요합니다. 지원되는 HTTP 동사를 지정하는 `method`를 포함합니다.|아닙니다.|이 섹션이 없으면 GET 및 POST가 지원됩니다.|
|메서드|HTTP 동사를 지정합니다.|`allowed-methods` 섹션이 있는 경우 하나 이상의 `method` 요소가 필요합니다.|N/A|
|allowed-headers|이 요소는 요청에 포함할 수 있는 헤더 이름을 지정하는 `header` 요소를 포함합니다.|아닙니다.|N/A|
|expose-headers|이 요소는 클라이언트가 액세스할 수 있는 헤더 이름을 지정하는 `header` 요소를 포함합니다.|아닙니다.|N/A|
|머리글|헤더 이름을 지정합니다.|섹션이 있는 경우 `allowed-headers` 또는 `expose-headers`에 하나 이상의 `header` 요소가 필요합니다.|N/A|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|allow-credentials|사전 응답의 `Access-Control-Allow-Credentials` 헤더가 이 특성 값으로 설정되고 도메인 간 요청에서 자격 증명을 제출하는 클라이언트 기능에 영향을 줍니다.|아닙니다.|false|
|preflight-result-max-age|사전 응답의 `Access-Control-Max-Age` 헤더가 이 특성 값으로 설정되고 사전 응답을 캐싱하는 사용자 에이전트 기능에 영향을 줍니다.|아닙니다.|0|

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** global, API, operation

## <a name="JSONP"></a> JSONP
`jsonp` 정책은 패딩이 있는 JSON(JSONP) 지원을 작업 또는 API에 추가하여 JavaScript 브라우저 기반 클라이언트의 도메인 간 호출을 허용합니다. JSONP는 JavaScript 프로그램에서 다른 도메인의 서버로부터 데이터를 요청하는 데 사용하는 메서드입니다. JSONP는 웹 페이지에 대한 액세스 권한이 동일한 도메인 내에 있어야 하는 경우 대부분의 웹 브라우저에서 적용하는 제한을 무시합니다.

### <a name="policy-statement"></a>정책 문

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>예

```xml
<jsonp callback-parameter-name="cb" />
```

콜백 매개 변수 ?cb=XXX 없이 메서드를 호출하는 경우 일반 JSON(함수 호출 래퍼 없이)이 반환됩니다.

콜백 매개 변수 `?cb=XXX`를 추가한 경우 `XYZ('<json result goes here>');`와 같이 콜백 함수 주위에 원래 JSON 결과를 래핑한 JSONP 결과가 반환됩니다.

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|jsonp|루트 요소입니다.|예|

### <a name="attributes"></a>특성

|이름|설명|필수|기본값|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|함수가 상주하는 정규화된 도메인 이름이 접두사로 지정된 도메인 간 JavaScript 함수 호출|예|N/A|

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** outbound
- **정책 범위:** global, product, API, operation

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)   
