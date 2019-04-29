---
title: API Management 서비스를 사용하여 HTTP 요청 생성
description: API Management에서 요청 및 응답 정책을 사용하여 API에서 외부 서비스를 호출하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 12/15/2016
ms.author: v-yiso
ms.date: 04/22/2019
ms.openlocfilehash: 2c4e5d0117f046343b140ef2b2c46c074c835075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557940"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Azure API Management 서비스에서 외부 서비스 사용
Azure API Management 서비스에서 사용할 수 있는 정책은 순수하게 들어오는 요청, 보내는 응답 및 기본 구성 정보에 기반하여 다양한 범위의 유용한 작업을 수행할 수 있습니다. 그러나 API Management 정책에서 외부 서비스와 상호 작용할 수 있으면 더 많은 기회를 얻게 됩니다.

[로깅, 모니터링 및 분석에 대한 Azure 이벤트 허브 서비스](api-management-log-to-eventhub-sample.md)와 상호 작용하는 방법은 이전에 살펴보았습니다. 이 문서에서는 외부 HTTP 기반 서비스와 상호 작용할 수 있도록 하는 정책을 보여 줍니다. 이러한 정책은 원격 이벤트를 트리거하거나 원래 요청 및 응답을 몇 가지 방식으로 조작하는 데 사용할 정보를 검색하는 데에 사용할 수 있습니다.

## <a name="send-one-way-request"></a>일방-요청-보내기
아마도 가장 간단한 외부 상호 작용은 외부 서비스가 일종의 중요한 이벤트 알림을 받을 수 있도록 하는 자체 유도 스타일의 요청일 것입니다. 제어 흐름 정책 `choose`는 관심이 있는 모든 유형의 조건을 검색하는 데 사용될 수 있습니다.  조건을 만족하는 경우 [send-one-way-request](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) 정책을 사용하여 외부 HTTP 요청을 만들 수 있습니다. Hipchat 및 Slack과 같은 메시징 시스템에 대한 요청이나 SendGrid 및 MailChimp와 같은 메일 API, 또는 PagerDuty와 같은 중요한 지원 인시던트일 수 있습니다. 이러한 메시징 시스템에는 모두 호출될 수 있는 간단한 HTTP API가 있습니다.

### <a name="alerting-with-slack"></a>Slack에 경고
다음 예제에서는 HTTP 응답 상태 코드가 500 이상인 경우 Slack 대화방에 메시지를 보내는 방법을 보여줍니다. 500 범위 오류는 API의 클라이언트가 자체로 해결할 수 없는 백 엔드 API에 문제가 있음을 나타냅니다. 일반적으로 API Management 부분에 일종의 개입이 필요합니다.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Slack에는 인바운드 웹 후크가 있습니다. 인바운드 웹 후크를 구성할 때 Slack은 간단한 게시 요청을 수행하고 Slack 채널에 메시지를 전달하는 특별한 URL을 생성합니다. 만든 JSON 본문은 Slack에서 정의된 형식을 기반으로 합니다.

![Slack 웹 후크](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>자체 유도로 충분합니까?
자체 유도 스타일의 요청을 사용할 때 장단점이 있습니다. 어떤 이유가 있는 경우 요청이 실패한 다음 실패가 보고되지 않습니다. 이 특정 상황에서는 보조 오류 보고 시스템이 가진 복잡성 및 응답을 기다리는 추가 성능 비용이 보장됩니다. 응답을 확인하는 데 중요한 시나리오의 경우 [전송-요청](/azure/api-management/api-management-advanced-policies#SendRequest) 정책이 더 효율적입니다.

## <a name="send-request"></a>전송-요청
`send-request` 정책을 사용하면 복잡한 처리 기능을 수행하는 외부 서비스를 사용할 수 있고 이후 정책 처리에 사용할 수 있는 API 관리 서비스에 데이터를 반환할 수 있습니다.

### <a name="authorizing-reference-tokens"></a>참조 토큰 권한 부여
API Management의 주요 기능은 백 엔드 리소스를 보호하는 것입니다. API에서 사용하는 권한 부여 서버가 [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md)처럼 [JWT 토큰](https://jwt.io/)을 해당 OAuth2 흐름의 일부로 만드는 경우 `validate-jwt` 정책을 사용하여 토큰의 유효성을 확인할 수 있습니다. 일부 권한 부여 서버는 권한 부여 서버에 다시 호출하지 않으면 확인할 수 없는 [참조 토큰](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/)이라는 것을 만듭니다.

### <a name="standardized-introspection"></a>표준화된 검사
과거에는 권한 부여 서버를 사용하여 참조 토큰을 확인하는 표준화된 방법이 없었습니다. 그러나 최근에 제안된 표준 [RFC 7662](https://tools.ietf.org/html/rfc7662) 는 리소스 서버가 토큰의 유효성을 검사하는 방법을 정의하는 IETF에서 게시되었습니다.

### <a name="extracting-the-token"></a>토큰 추출
첫 번째 단계는 권한 부여 헤더에서 토큰을 추출하는 작업입니다. 헤더 값은 `Bearer` 권한 부여 체계, 공백 및 권한 부여 토큰을 [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1)단위로 형식이 지정되어야 합니다. 하지만 권한 부여 체계를 생략하는 경우가 있습니다. 구문 분석할 때 이를 세려면 API Management는 헤더 값을 공간에 분할하고 반환된 문자열 배열에서 마지막 문자열을 선택합니다. 잘못된 형식의 권한 부여 헤더에 대한 해결 방법을 제공합니다.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>유효성 검사 요청하기
API Management에 권한 부여 토큰이 있다면 API Management는 토큰의 유효성 검사를 요청할 수 있습니다. RFC 7662는 프로세스 검사를 호출하고 검사 리소스에 `POST` HTML 양식을 필요로 합니다. HTML 양식은 키 `token`를 통해 적어도 키/값 쌍을 포함해야 합니다. 또한 권한 부여 서버에 대한 요청은 인증되어 악의적인 클라이언트가 유효한 토큰을 방해할 수 없도록 해야 합니다.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>응답 확인
`response-variable-name` 특성은 반환된 응답의 액세스를 제공하는 데 사용됩니다. 이 속성에 정의된 이름은 `context.Variables` 사전에 키로 사용하여 `IResponse` 개체에 액세스할 수 있습니다.

응답 개체에서 본문을 검색할 수 있고 RFC 7622는 응답이 JSON 개체를 해야 하며 API Management에 최소한 부울 값인 `active`라는 속성을 포함해야 한다고 알립니다. `active` 가 true인 경우 토큰이 유효한 것으로 간주됩니다.

### <a name="reporting-failure"></a>오류 보고
`<choose>` 정책을 사용하여 토큰이 유효한지 감지할 수 있으며 그럴 경우 401 응답을 반환합니다.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

또한 `bearer` 토큰을 사용해야 하는 방법을 설명하는 [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3)대로 API Management는 401 응답을 사용하여 `WWW-Authenticate` 헤더를 반환합니다. WWW 인증은 클라이언트에게 적절한 권한이 있는 요청을 생성하는 방법을 지시하는 데 사용됩니다. OAuth2 프레임워크에 다양한 접근 방법이 가능하기 때문에 필요한 모든 정보를 통신하기가 어렵습니다. 다행스럽게도 [클라이언트가 리소스 서버에 대한 요청 권한을 제대로 부여하는 방법을 검색](https://tools.ietf.org/html/draft-jones-oauth-discovery-00)하도록 노력하고 있습니다.

### <a name="final-solution"></a>최종 솔루션
마지막으로 다음 정책을 얻을 수 있습니다.

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

`send-request` 정책이 API Management 서비스를 통해 흐르는 요청 및 응답 프로세스에 유용한 외부 서비스를 통합하는 데 사용할 수 있는 방법에 대한 여러 가지 방법 중 하나입니다.

## <a name="response-composition"></a>응답 컴퍼지션
이전 예제에서 살펴본 대로 `send-request` 정책은 백 엔드 시스템에 대한 기본 요청을 개선하기 위해 사용되거나 백 엔드 호출의 전체 바꾸기로 사용될 수 있습니다. 이 기술을 사용하여 여러 다른 시스템에서 집계되는 복합 리소스를 쉽게 만들 수 있습니다.

### <a name="building-a-dashboard"></a>대시보드 작성
예를 들어 대시보드를 통하여 여러 백 엔드 시스템에 존재하는 정보를 노출시키려고 할 수 있습니다. KPI는 모두 다른 백 엔드에서 가져오지만 직접 액세스를 제공하는 편을 선호하지 않으며 단일 요청에서 모든 정보를 검색할 수 있는 경우가 좋습니다. 아마도 일부 백 엔드 정보는 먼저 조각화 및 분할 및 처리가 필요합니다! 해당 복합 리소스를 캐시할 수 있으면 사용자는 성능이 저하된 메트릭이 변경되는지 알아보기 위해 F5 키를 계속 누르는 습관이 있기 때문에 백 엔드 부하를 줄이는데 유용합니다.    

### <a name="faking-the-resource"></a>리소스 가장
대시보드 리소스를 구축하는 첫 단계는 Azure Portal에서 새 작업을 구성하는 것입니다. 동적 리소스를 작성하는 컴퍼지션 정책을 구성하는 데 사용되는 자리 표시자 작업입니다.

![대시보드 작업](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>요청하기
작업이 만들어지면 해당 작업에 대해 특별히 정책을 구성할 수 있습니다. 

![대시보드 작업](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

첫 번째 단계는 들어오는 요청에서 쿼리 매개 변수를 추출하므로 백 엔드에 전달할 수 있습니다. 이 예에서 대시보드는 시간의 경과에 따라 정보를 보여주며 따라서 `fromDate` 및 `toDate` 매개 변수가 있습니다. `set-variable` 정책을 사용하여 요청 URL에서 정보를 추출할 수 있습니다.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

이 정보가 있는 경우 모든 백 엔드 시스템에 요청을 할 수 있습니다. 각 요청은 매개 변수 정보를 포함하는 새 URL을 생성하고 관련 서버를 호출하며 컨텍스트 변수의 응답을 저장합니다.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

이러한 요청은 순서대로 실행되며 이는 가장 좋은 방법은 아닙니다. 

### <a name="responding"></a>응답
복합 응답을 생성하려면 [반환-응답](/azure/api-management/api-management-advanced-policies#ReturnResponse) 정책을 사용할 수 있습니다. `set-body` 요소는 속성으로 포함 된 모든 구성 요소 표현을 사용하여 새 `JObject`을 생성하도록 식을 사용할 수 있습니다.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

완성된 정책은 다음과 같습니다.

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

자리 표시자 작업의 구성에서 적어도 1시간 동안 캐시되도록 대시보드 리소스를 구성할 수 있습니다. 

## <a name="summary"></a>요약
Azure API Management 서비스는 HTTP 트래픽에 선택적으로 적용할 수 있는 유연한 정책을 제공하고 백 엔드 서비스의 컴퍼지션을 사용할 수 있습니다. 함수, 확인, 유효성 검사 기능의 경고를 통해 API 게이트웨이를 개선하거나 여러 백 엔드 서비스를 기반으로 새 복합 리소스를 만들 경우 `send-request` 및 관련된 정책은 새로운 가능성을 엽니다.


