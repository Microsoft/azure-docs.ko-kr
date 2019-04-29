---
title: Azure API Management의 사용자 지정 캐싱
description: Azure API Management에서 키로 항목을 캐시하는 방법을 알아봅니다
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780824"
---
# <a name="custom-caching-in-azure-api-management"></a>Azure API Management의 사용자 지정 캐싱
Azure API Management 서비스에서는 리소스 URL을 키로 사용하는 [HTTP 응답 캐싱](api-management-howto-cache.md) 에 대한 기본 제공 지원을 포함합니다. `vary-by` 속성을 사용하여 요청 헤더에서 키를 수정할 수 있습니다. 전체 HTTP 응답(즉, 표현)을 캐싱하는 데 유용하지만 때때로 표현의 일부를 캐싱하는 데 유용합니다. 새 [cache-lookup-value](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) 및 [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) 정책은 임의의 정책 정의 내에서 데이터를 저장하고 검색하는 기능을 제공합니다. 이제 외부 서비스에서 나오는 응답을 캐시할 수 있기 때문에 이 기능은 이전에 도입된 [보내기 요청](/azure/api-management/api-management-advanced-policies#SendRequest) 정책에 값을 추가합니다.

## <a name="architecture"></a>아키텍처
API Management 서비스는 테넌트 단위 공유 데이터 캐시를 사용하므로 여러 단위까지 확장되어 동일하게 캐시된 데이터에 대한 액세스를 얻습니다. 그러나 다중 하위 지역 배포로 작업할 때 각 지역 내에 독립적인 캐시가 있습니다. 일부 정보가 유일한 원본인 경우 소스 캐시를 데이터 저장소로 처리하지 않는 것이 중요합니다. 이렇게 하고 이후 다중 지역 배포를 활용하기로 결정한 경우 출장 중인 사용자와 고객은 캐시된 데이터에 액세스를 하지 못할 수 있습니다.

## <a name="fragment-caching"></a>부분 캐싱
반환되는 응답이 결정하는 비용이 높고 적절한 시간에 대해 원래 상태를 유지하는 데이터의 일부를 포함하는 경우도 있습니다. 예를 들어 항공 예약, 비행 상태 등과 관련된 정보를 제공하는 항공사에서 작성한 서비스를 고려하는 것이 좋습니다. 사용자가 항공사 포인트 프로그램의 멤버인 경우 항공사는 현재 상태와 누적 주행 거리에 관련된 정보를 보유합니다. 이 사용자 관련 정보는 다른 시스템에 저장될 수 있지만 비행 상태 및 예약에 대한 반환된 응답에 포함하는 것이 바람직할 수 있습니다. 이렇게 하려면 조각 캐싱이라는 프로세스를 사용합니다. 기본 표시는 사용자 관련 정보를 삽입할 위치를 나타내는 일종의 토큰을 사용하여 원본 서버에서 반환할 수 있습니다. 

백 엔드 API에서 다음과 같은 JSON 응답을 고려합니다.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

그리고 다음과 같은 `/userprofile/{userid}` 에 있는 보조 리소스도 고려합니다.

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

포함할 적절한 사용자 정보를 확인하기 위해 API Management는 최종 사용자가 누구인지를 식별해야 합니다. 이 메커니즘은 구현에 따라 다릅니다. 예를 들어 `JWT` 토큰의 `Subject` 클레임을 사용합니다. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management는 `enduserid` 값을 나중에 사용할 컨텍스트 변수에 저장합니다. 다음 단계는 이전 요청이 사용자 정보를 검색하고 캐시에 저장했는지 여부를 결정합니다. 이를 위해 API Management는 `cache-lookup-value` 정책을 사용합니다.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

키 값에 해당하는 캐시에 항목이 없는 경우 어떤 `userprofile` 컨텍스트 변수도 생성되지 않습니다. API Management는 `choose` 제어 흐름 정책을 사용하여 조회가 성공했는지를 확인합니다.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

`userprofile` 컨텍스트 변수가 존재하지 않으면 API Management는 찾아오기 위해 HTTP 요청을 만들어야 합니다.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management는 `enduserid`를 사용하여 사용자 프로필 리소스에 URL을 생성합니다. API Management가 응답을 하면 응답에서 본문 텍스트를 가져오고 컨텍스트 변수에 다시 저장할 수 있습니다.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

API Management에서 이 HTTP 요청을 다시 하지 않으려면 동일한 사용자가 다른 요청을 할 때 사용자 프로필을 캐시에 저장하도록 지정할 수 있습니다.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management는 원래 검색하려고 시도하는 동일한 키를 사용하여 캐시에 값을 저장합니다. API Management에서 값을 저장하도록 선택한 기간은 정보 변경 주기 및 사용자의 오래된 정보 허용치에 기반해야 합니다. 

캐시에서 검색하는 작업이 여전히 out-of-process 네트워크 요청이며 잠재적으로 수십 밀리초를 요청에 추가할 수 있습니다. 사용자 프로필 정보를 확인하는 작업이 여러 백 엔드에서 데이터베이스 쿼리 또는 집계 정보를 수행해야 하기 때문에 많은 시간이 걸릴 때 혜택이 제공됩니다.

프로세스의 마지막 단계는 사용자 프로필 정보를 사용하여 반환된 응답을 업데이트하는 것입니다.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

대체가 발생하지 않더라도 여전히 응답은 유효한 JSON이도록 토큰의 일부로 인용 부호를 포함하도록 선택할 수 있습니다.  

이러한 모든 단계를 함께 결합하면 최종 결과는 다음과 같은 정책입니다.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

이 캐싱 방법은 주로 HTML가 서버 쪽에서 구성되는 웹 사이트에서 사용되므로 단일 페이지로 렌더링될 수 있습니다. 클라이언트가 클라이언트 쪽 HTTP 캐싱을 수행할 수 없는 API에서 유용할 수 있고 또는 클라이언트에서 해당 역할을 넣지 않는 것이 바람직할 수 있습니다.

같은 종류의 조각 캐싱은 Redis caching 서버를 사용하여 백 엔드 웹 서버에서 수행될 수도 있지만 API Management 서비스를 사용하여 이 작업을 수행하면  캐시된 조각이 기본 응답이 아닌 다른 백 엔드에서 생성될 때 유용합니다.

## <a name="transparent-versioning"></a>투명한 버전 관리
API의 여러 다른 구현 버전이 한 번에 지원되는 것이 일반적입니다. 예를 들어 다른 환경을 지원하거나(개발, 테스트, 프로덕션 등) API의 이전 버전을 지원하여 최신 버전으로 마이그레이션할 API 소비자에게 시간을 줄 수 있습니다. 

클라이언트 개발자가 URL을 `/v1/customers`에서 `/v2/customers`로 변경하도록 하는 대신 이를 처리하는 한 가지 방법이 있습니다. 즉, 현재 적절한 백 엔드 URL을 사용하고 호출하려는 API 버전인 소비자의 프로필 데이터를 저장하는 것입니다. 특정 클라이언트에 대한 호출에 올바른 백 엔드 URL를 결정하기 위해 일부 구성 데이터를 쿼리하는 것이 필요합니다. 이 구성 데이터를 캐시하여 API Management는 이 조회를 수행하는 성능 저하를 최소화할 수 있습니다.

첫 번째 단계는 원하는 버전을 구성하는 데 사용되는 식별자를 결정하는 것입니다. 이 예제에서는 제품 구독 키에 버전을 연결하도록 선택합니다. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management는 캐시 조회를 수행하여 이미 원하는 클라이언트 버전을 검색했는지 확인할 수 있습니다.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

그런 다음, API Management는 캐시에서 찾지 않았는지 확인합니다.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

API Management에서 찾지 않은 경우 API Management에서 검색합니다.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

응답에서 응답 본문 텍스트를 추출합니다.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

나중에 사용할 캐시에 다시 저장합니다.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

마지막으로 백 엔드 URL을 업데이트하여 클라이언트가 원하는 서비스의 버전을 선택합니다.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

전체 정책은 다음과 같습니다.

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

클라이언트가 클라이언트를 업데이트하고 다시 배포할 필요 없이 백 엔드 버전에 액세스하도록 투명하게 제어하도록 API 소비자를 사용하도록 설정하는 것이 많은 API 버전 관리 문제를 해결하는 세련된 솔루션입니다.

## <a name="tenant-isolation"></a>테넌트 격리
더 큰 다중 테넌트 배포에서 일부 회사는 백 엔드 하드웨어의 고유한 배포에 테넌트의 별도 그룹을 만듭니다. 백 엔드에서 발생하는 하드웨어 문제로 영향을 받는 고객의 수를 최소화합니다. 또한 새로운 소프트웨어 버전을 단계에서 롤아웃할 수 있습니다. 이상적으로 이 백 엔드 아키텍처는 API 소비자에게 투명해야 합니다. API 키 단위 구성 상태를 사용하여 백 엔드 URL을 조작하는 동일한 기법을 기반으로 하기 때문에 투명한 버전 관리에 비슷한 방식으로 수행될 수 있습니다.  

각 구독 키에 대한 기본 설정된 API 버전을 반환하는 대신 할당된 하드웨어 그룹에 테넌트를 연결하는 식별자를 반환합니다. 적절한 백 엔드 URL을 생성하는 데 해당 식별자를 사용할 수 있습니다.

## <a name="summary"></a>요약
모든 종류의 데이터를 저장하기 위해 Azure API 관리 캐시를 사용하면 인바운드 요청이 처리되는 방식에 영향을 줄 수 있는 구성 데이터에 대해 효율적으로 액세스할 수 있습니다. 또한 백 엔드 API에서 반환된 응답을 강화할 수 있는 데이터 조각을 저장하는 데 사용할 수 있습니다.
