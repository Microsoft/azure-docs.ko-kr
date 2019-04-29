---
title: Azure API Management 캐싱 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 캐싱 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 08b6f803d6994015432bf68c7b3edae14af8f976
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095451"
---
# <a name="api-management-caching-policies"></a>API Management 캐싱 정책
이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](https://go.microsoft.com/fwlink/?LinkID=398186)을 참조하세요.

## <a name="CachingPolicies"></a> 캐싱 정책

- 응답 캐싱 정책
    - [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) - 캐시 조회를 수행하여 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다.
    - [캐시에 저장](api-management-caching-policies.md#StoreToCache) - 지정된 캐시 제어 구성에 따라 응답을 캐시합니다.
- 값 캐싱 정책
    - [캐시에서 값 가져오기](#GetFromCacheByKey) - 키로 캐시된 항목을 검색합니다.
    - [값을 캐시에 저장](#StoreToCacheByKey) - 키로 캐시에 항목을 저장합니다.
    - [캐시에서 값을 제거](#RemoveCacheByKey) - 키로 캐시에서 항목을 제거합니다.

## <a name="GetFromCache"></a> 캐시에서 가져오기
`cache-lookup` 정책을 사용하여 캐시 조회를 수행하며 사용 가능한 경우 올바르게 캐시된 응답을 반환합니다. 이 정책은 응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다. 응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

> [!NOTE]
> 이 정책에는 해당하는 [캐시에 저장](api-management-caching-policies.md#StoreToCache) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
  <vary-by-header>Accept</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Accept-Charset</vary-by-header>
  <!-- should be present in most cases -->
  <vary-by-header>Authorization</vary-by-header>
  <!-- should be present when allow-private-response-caching is "true"-->
  <vary-by-header>header name</vary-by-header>
  <!-- optional, can repeated several times -->
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>
  <!-- optional, can repeated several times -->
</cache-lookup>
```

### <a name="examples"></a>예

#### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>정책 식을 사용하는 예제
이 예제에서는 지원 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 API Management 응답 캐싱 기간을 구성하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky를 사용하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)을 참조하고 25분 25초로 빠르게 돌리세요.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cache-lookup|루트 요소입니다.|예|
|vary-by-header|지정된 헤더 값당 시작 캐싱 응답입니다(예: Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match).|아닙니다.|
|vary-by-query-parameter|지정된 쿼리 매개 변수의 값에 따라 응답 캐싱을 시작합니다. 단일 또는 여러 매개 변수를 입력합니다. 세미콜론을 구분 기호로 사용합니다. 지정하지 않은 경우 모든 쿼리 매개 변수가 사용됩니다.|아닙니다.|

### <a name="attributes"></a>특성

| 이름                           | 설명                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| allow-private-response-caching | `true`로 설정하면 Authorization 헤더를 포함하는 요청의 캐싱을 허용합니다.                                                                                                                                                                                                                                                                        | 아닙니다.       | false             |
| caching-type               | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external` [Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 아닙니다.       | `prefer-external` |
| downstream-caching-type        | 이 특성은 다음 값 중 하나로 설정해야 합니다.<br /><br /> -   none - 다운스트림 캐싱이 허용되지 않습니다.<br />-   private - 다운스트림 캐싱이 허용됩니다.<br />-   public - 개인 및 공유 다운스트림 캐싱이 허용됩니다.                                                                                                          | 아닙니다.       | 없음              |
| must-revalidate                | 다운스트림 캐싱을 사용하는 경우 이 특성이 게이트웨이 응답에서 `must-revalidate` 캐시 제어 지시문을 설정 또는 해제합니다.                                                                                                                                                                                                                      | 아닙니다.       | true              |
| vary-by-developer              | [구독 키](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)별 캐시 응답을 위해서는 `true`로 설정합니다.                                                                                                                                                                                                                                                                                                         | 예      |         거짓          |
| vary-by-developer-groups       | [사용자 그룹](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)별 캐시 응답을 위해서는 `true`로 설정합니다.                                                                                                                                                                                                                                                                                                             | 예      |       거짓            |

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** API, operation, product

## <a name="StoreToCache"></a> 캐시에 저장
`cache-store` 정책은 지정된 캐시 설정에 따라 응답을 캐시합니다. 이 정책은 응답 콘텐츠가 일정 기간 동안 정적 상태인 경우 적용해야 합니다. 응답 캐싱은 백 엔드 웹 서버에 부과된 대역폭 및 처리 요구 사항을 줄이며 API 소비자가 인지하는 대기 시간을 줄여 줍니다.

> [!NOTE]
> 이 정책에는 해당하는 [캐시에서 가져오기](api-management-caching-policies.md#GetFromCache) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-store duration="seconds" />
```

### <a name="examples"></a>예

#### <a name="example"></a>예

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">
            <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
        </cache-lookup>
    </inbound>
    <outbound>
        <base />
        <cache-store duration="3600" />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>정책 식을 사용하는 예제
이 예제에서는 지원 서비스의 `Cache-Control` 지시문에 지정된 대로 백 엔드 서비스의 응답 캐싱과 일치하는 API Management 응답 캐싱 기간을 구성하는 방법을 보여 줍니다. 이 정책을 구성하고 사용하는 데모는 [클라우드 표지 에피소드 177: Vlad Vinogradsky를 사용하는 추가 API Management 기능](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)을 참조하고 25분 25초로 빠르게 돌리세요.

```xml
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->

<!-- Copy this snippet into the inbound section -->
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >
  <vary-by-header>Accept</vary-by-header>
  <vary-by-header>Accept-Charset</vary-by-header>
</cache-lookup>

<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->
<cache-store duration="@{
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;
  }"
 />
```

자세한 내용은 [정책 식](api-management-policy-expressions.md) 및 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)를 참조하세요.

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cache-store|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| 이름             | 설명                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | 캐시된 항목의 TTL(Time-to-Live)로 초 단위로 지정합니다.                                                                                                                                                                                                                                                                                                   | 예      | N/A               |

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** outbound
- **정책 범위:** API, operation, product

## <a name="GetFromCacheByKey"></a> 캐시에서 값 가져오기
`cache-lookup-value` 정책을 사용하여 키별 캐시 조회를 수행하며 캐시된 값을 반환합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

> [!NOTE]
> 이 정책에는 해당하는 [값을 캐시에 저장](#StoreToCacheByKey) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>예
이 정책에 대한 자세한 내용과 예제는 [Azure API Management의 사용자 지정 캐싱](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)을 참조하세요.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cache-lookup-value|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| 이름             | 설명                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external` [Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 아닙니다.       | `prefer-external` |
| default-value    | 캐시 키 조회 시 누락 항목이 있는 경우 변수에 할당할 값입니다. 이 특성을 지정하지 않으면 `null`이 할당됩니다.                                                                                                                                                                                                           | 아닙니다.       | `null`            |
| key              | 조회에 사용할 캐시 키 값입니다.                                                                                                                                                                                                                                                                                                                       | 예      | N/A               |
| variable-name    | 조회에 성공한 경우 조회된 값이 할당될 [컨텍스트 변수](api-management-policy-expressions.md#ContextVariables)의 이름입니다. 조회 시 누락 항목이 있는 경우 변수에 `default-value` 특성 또는 `null`(`default-value` 특성이 생략된 경우)이 할당됩니다.                                       | 예      | N/A               |

### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** global, API, operation, product

## <a name="StoreToCacheByKey"></a> 값을 캐시에 저장
`cache-store-value`는 키로 캐시 저장을 수행합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

> [!NOTE]
> 이 정책에는 해당하는 [캐시에서 값 가져오기](#GetFromCacheByKey) 정책이 있어야 합니다.

### <a name="policy-statement"></a>정책 문

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>예
이 정책에 대한 자세한 내용과 예제는 [Azure API Management의 사용자 지정 캐싱](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)을 참조하세요.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cache-store-value|루트 요소입니다.|예|

### <a name="attributes"></a>특성

| 이름             | 설명                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external` [Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 아닙니다.       | `prefer-external` |
| duration         | 제공된 기간 값 동안 값(초 단위로 지정)이 캐시됩니다.                                                                                                                                                                                                                                                                                 | 예      | N/A               |
| key              | 값을 저장할 캐시 키입니다.                                                                                                                                                                                                                                                                                                                   | 예      | N/A               |
| 값            | 캐시될 값입니다.                                                                                                                                                                                                                                                                                                                                     | 예      | N/A               |
### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** global, API, operation, product

### <a name="RemoveCacheByKey"></a> 캐시에서 값 제거
`cache-remove-value` 키로 식별된 캐시 항목을 삭제합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다.

#### <a name="policy-statement"></a>정책 문

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>예

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>요소

|이름|설명|필수|
|----------|-----------------|--------------|
|cache-remove-value|루트 요소입니다.|예|

#### <a name="attributes"></a>특성

| 이름             | 설명                                                                                                                                                                                                                                                                                                                                                 | 필수 | 기본값           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| caching-type | 다음 특성 값 중에서 선택합니다.<br />- `internal` 기본 제공 API Management 캐시를 사용합니다.<br />- `external` [Azure API Management에서 외부 Azure Cache for Redis 사용](api-management-howto-cache-external.md)에 설명된 대로 외부 캐시를 사용합니다.<br />- `prefer-external` 구성된 경우 외부 캐시를 사용하고 그렇지 않으면 내부 캐시를 사용합니다. | 아닙니다.       | `prefer-external` |
| key              | 캐시에서 제거할 이전에 캐시된 값의 키입니다.                                                                                                                                                                                                                                                                                        | 예      | N/A               |

#### <a name="usage"></a>사용 현황
이 정책은 다음과 같은 정책 [섹션](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) 및 [범위](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, backend, on-error
- **정책 범위:** global, API, operation, product

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

+ [API Management의 정책](api-management-howto-policies.md)
+ [API 변환](transform-api.md)
+ [정책 참조](api-management-policy-reference.md)(정책 문 및 해당 설정에 대한 전체 목록)
+ [정책 샘플](policy-samples.md)
