---
title: Azure Functions에서 프록시 사용 | Microsoft Docs
description: Azure Functions 프록시를 사용하는 방법의 개요
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 2fbf29385b9a14cf5d4a9df621f0767a32079587
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020998"
---
# <a name="work-with-azure-functions-proxies"></a>Azure Functions 프록시 사용

이 문서에서는 Azure Functions 프록시를 구성하고 사용하는 방법을 설명합니다. 이 기능을 사용하면 다른 리소스에서 구현된 함수 앱에 엔드포인트를 지정할 수 있습니다. 이러한 프록시를 사용하면 클라이언트에 대해 단일 API 화면을 계속 제공하면서 큰 API를 여러 개의 함수 앱으로 나눌 수 있습니다(마이크로 서비스 아키텍처 참조).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> 프록시 실행에는 표준 함수 요금이 적용됩니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.

## <a name="create"></a>프록시 만들기

이 섹션에서는 함수 포털에서 프록시를 만드는 방법을 보여 줍니다.

1. [Azure Portal]을 열고 함수 앱으로 이동합니다.
2. 왼쪽 창에서 **새 프록시**를 선택합니다.
3. 프록시의 이름을 제공합니다.
4. **경로 템플릿** 및 **HTTP 메서드**를 지정하여 이 함수 앱에 노출되는 엔드포인트를 구성합니다. 이러한 매개 변수는 [HTTP 트리거]에 대한 규칙에 따라 동작합니다.
5. **백 엔드 URL**을 다른 엔드포인트로 설정합니다. 이러한 엔드포인트는 다른 함수 앱의 함수이거나 다른 API일 수 있습니다. 정적 값이 아니어도 되며 [애플리케이션 설정] 및 [원래 클라이언트 요청의 매개 변수]를 참조할 수 있습니다.
6. **만들기**를 클릭합니다.

이제 프록시는 함수 앱에서 새 엔드포인트로 존재합니다. 클라이언트 관점에서 Azure Functions의 HttpTrigger에 같습니다. 프록시 URL을 복사하고 자주 사용하는 HTTP 클라이언트에서 테스트하여 새 프록시를 시험해볼 수 있습니다.

## <a name="modify-requests-responses"></a>요청 및 응답 수정

Azure Functions 프록시를 사용해서 백 엔드에서 요청 및 응답을 수정할 수 있습니다. 이러한 변환은 [변수 사용]에 정의된 대로 변수를 사용할 수 있습니다.

### <a name="modify-backend-request"></a>백 엔드 요청 수정

기본적으로 백 엔드 요청은 원래 요청의 복사본으로 초기화됩니다. 백 엔드 URL을 설정하는 것 외에도 HTTP 메서드, 헤더 및 쿼리 문자열 매개 변수를 변경할 수 있습니다. 수정된 값은 [애플리케이션 설정] 및 [원래 클라이언트 요청의 매개 변수]를 참조할 수 있습니다.

프록시 세부 정보 페이지의 *요청 재정의* 섹션을 확장하여 포털에서 백 엔드 요청을 수정할 수 있습니다. 

### <a name="modify-response"></a>응답 수정

기본적으로 클라이언트 요청은 원래 응답의 복사본으로 초기화됩니다. 응답의 상태 코드, 이유 구문, 헤더 및 본문을 변경할 수 있습니다. 수정된 값은 [애플리케이션 설정], [원래 클라이언트 요청의 매개 변수] 및 [백 엔드 응답의 매개 변수]를 참조할 수 있습니다.

프록시 세부 정보 페이지의 *응답 재정의* 섹션을 확장하여 포털에서 백 엔드 응답을 수정할 수 있습니다. 

## <a name="using-variables"></a>변수 사용

프록시에 대한 구성은 정적일 필요가 없습니다. 원래 클라이언트 요청, 백 엔드 응답 또는 애플리케이션 설정의 변수를 사용하도록 조건을 지정할 수 있습니다.

### <a name="reference-localhost"></a>로컬 함수 참조
`localhost`를 사용하면 왕복 프록시 요청없이 동일한 함수 앱 내에 있는 함수를 직접 참조할 수 있습니다.

`"backendurl": "https://localhost/api/httptriggerC#1"`은 `/api/httptriggerC#1` 경로의 로컬 HTTP 트리거 함수를 참조합니다.

 
>[!Note]  
>함수에서 *function, admin 또는 sys* 권한 부여 수준을 사용하는 경우 원본 함수 URL에 따라 코드 및 clientId를 제공해야 합니다. 이 예에서는 참조가 `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`처럼 보입니다.

### <a name="request-parameters"></a>요청 매개 변수 참조

요청 매개 변수는 백 엔드 URL 속성에 대한 입력 또는 요청 및 응답을 수정하는 일부로 사용할 수 있습니다. 기본 프록시 구성에 지정된 경로 템플릿에서 제한할 수 있는 매개 변수도 있고 들어오는 요청의 속성에서 가져올 수 있는 매개 변수도 있습니다.

#### <a name="route-template-parameters"></a>경로 템플릿 매개 변수
경로 템플릿에 사용된 매개 변수는 이름으로 참조할 수 있습니다. 이러한 매개 변수 이름은 중괄호({})로 묶습니다.

예를 들어 프록시에 `/pets/{petId}`와 같은 경로 템플릿이 있는 경우 백 엔드 URL은 `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`와 같이 `{petId}` 값을 포함할 수 있습니다. 경로 템플릿이 와일드카드로 끝나면(예: `/api/{*restOfPath}`) 값 `{restOfPath}`는 들어오는 요청의 나머지 경로 세그먼트에 대한 문자열 표현이 됩니다.

#### <a name="additional-request-parameters"></a>추가 요청 매개 변수
경로 템플릿 매개 변수 외에도 구성 값에 다음 값을 사용할 수 있습니다.

* **{request.method}**: 원래 요청에 사용된 HTTP 메서드입니다.
* **{request.headers.\<HeaderName\>}**: 원래 요청에서 읽어올 수 있는 헤더입니다. *\<HeaderName\>* 을 읽으려는 헤더 이름으로 바꿉니다. 헤더가 요청에 포함되지 않으면 값은 비어 있는 문자열이 됩니다.
* **{request.querystring.\<ParameterName\>}**: 원래 요청에서 읽어올 수 있는 쿼리 문자열 매개 변수입니다. *\<ParameterName\>* 을 읽으려는 매개 변수 이름으로 바꿉니다. 매개 변수가 요청에 포함되지 않으면 값은 비어 있는 문자열이 됩니다.

### <a name="response-parameters"></a>백 엔드 응답 매개 변수 참조

응답 매개 변수는 클라이언트에 대한 응답을 수정하는 일부로 사용할 수 있습니다. 구성 값에 다음 값을 사용할 수 있습니다.

* **{backend.response.statusCode}**: 백 엔드 응답에 반환되는 HTTP 상태 코드입니다.
* **{backend.response.statusReason}**: 백 엔드 응답에 반환되는 HTTP 이유 구문입니다.
* **{backend.response.headers.\<HeaderName\>}**: 백 엔드 응답에서 읽어올 수 있는 헤더입니다. *\<HeaderName\>* 을 읽으려는 헤더 이름으로 바꿉니다. 헤더가 응답에 포함되지 않으면 값은 비어 있는 문자열이 됩니다.

### <a name="use-appsettings"></a>애플리케이션 설정 참조

설정 이름을 백분율 기호(%)로 묶어 [함수 앱에 대해 정의된 애플리케이션 설정](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)을 참조할 수도 있습니다.

예를 들어 *https://%ORDER_PROCESSING_HOST%/api/orders*의 백 엔드 URL에서 "%ORDER_PROCESSING_HOST%"는 ORDER_PROCESSING_HOS 설정 값으로 바뀝니다.

> [!TIP] 
> 배포 또는 테스트 환경이 여러 개 있는 경우 백 엔드 호스트에 대해 애플리케이션 설정을 사용하세요. 이러한 방식으로 항상 해당 환경에 적합한 백 엔드에 정보를 전달할 수 있습니다.

## <a name="debugProxies"></a>프록시 문제 해결

`"debug":true` 플래그를 `proxies.json`의 프록시에 추가하면 디버그 로깅을 사용하도록 설정됩니다. 로그는 `D:\home\LogFiles\Application\Proxies\DetailedTrace`에 저장되며 고급 도구(kudu)를 통해 액세스할 수 있습니다. 모든 HTTP 응답에는 로그 파일에 액세스할 수 있는 URL이 포함된 `Proxy-Trace-Location` 헤더도 포함됩니다.

`true`로 설정된 `Proxy-Trace-Enabled` 헤더를 추가하면 클라이언트 쪽에서 프록시를 디버그할 수 있습니다. 또한 파일 시스템에 추적을 기록하며 추적 URL을 응답의 헤더로 반환합니다.

### <a name="block-proxy-traces"></a>프록시 추적 차단

보안상의 이유로 서비스 호출자가 추적을 생성하도록 허용하지 않을 수 있습니다. 로그인 자격 증명이 없으면 추적 내용에 액세스할 수 없지만 추적을 생성하면 리소스가 소비되고 Function 프록시가 사용되는 것이 노출됩니다.

`proxies.json`의 특정 프록시에 `"debug":false`를 추가하면 추적을 완전히 사용하지 않도록 설정됩니다.

## <a name="advanced-configuration"></a>고급 구성

구성하는 프록시는 함수 앱 디렉터리의 루트에 있는 *proxies.json* 파일에 저장됩니다. 이 파일을 수동으로 편집하고 함수가 지원하는 [배포 방법](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) 중 하나를 사용하여 앱의 일부로 배포할 수 있습니다. 

> [!TIP] 
> 배포 방법 중 하나를 설정하지 않은 경우 포털에서 *proxies.json* 파일로 작업할 수도 있습니다. 함수 앱으로 이동하여 **플랫폼 기능**을 선택한 후 **App Service 편집기**를 선택합니다. 이렇게 하여 함수 앱의 전체 파일 구조를 보고 변경할 수 있습니다.

*Proxies.json*은 명명된 프록시 및 해당 정의로 구성된 프록시 개체로 정의됩니다. 편집기에서 코드 완성을 지원하는 경우 필요에 따라 [JSON 스키마](http://json.schemastore.org/proxies)를 참조할 수 있습니다. 예제 파일은 다음과 유사할 수 있습니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

각 프록시에는 위의 코드 예제의 *proxy1*과 같은 이름이 지정됩니다. 해당하는 프록시 정의 개체는 다음과 같은 속성으로 정의됩니다.

* **matchCondition**: 필수--이 프록시의 실행을 트리거하는 요청을 정의하는 개체입니다. [HTTP 트리거]와 공유되는 두 가지 속성이 포함되어 있습니다.
    * _methods_: 프록시가 응답하는 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 프록시는 경로의 모든 HTTP 메서드에 응답합니다.
    * _route_: 필수--경로 템플릿을 정의하여 프록시에서 응답할 요청 URL을 제어합니다. HTTP 트리거와 달리 기본값이 없습니다.
* **backendUri**: 요청이 프록시되어야 하는 백 엔드 리소스의 URL입니다. 이 값은 애플리케이션 설정 및 원래 클라이언트 요청의 매개 변수를 참조할 수 있습니다. 이 속성을 포함하지 않으면 Azure Functions는 HTTP 200 OK로 응답합니다.
* **requestOverrides**: 변환을 백 엔드 요청으로 정의하는 개체입니다. [requestOverrides 개체 정의]를 참조하세요.
* **responseOverrides**: 클라이언트 응답으로 변환을 정의하는 개체입니다. [responseOverrides 개체 정의]를 참조하세요.

> [!NOTE] 
> Azure Functions 프록시의 *route* 속성은 Function App 호스트 구성의 *routePrefix* 속성을 유지하지 않습니다. `/api` 같은 접두사를 포함하려는 경우 *route* 속성에 포함해야 합니다.

### <a name="disableProxies"></a> 개별 프록시 사용 안 함

`proxies.json` 파일의 프록시에 `"disabled": true`를 추가하면 개별 프록시를 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 matchCondition을 충족하는 모든 요청이 404를 반환합니다.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="applicationSettings"></a> 애플리케이션 설정

여러 앱 설정에 따라 프록시 동작을 제어할 수 있습니다. [함수 앱 설정 참조](./functions-app-settings.md)에 모두 설명되어 있습니다.

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a> 예약된 문자(문자열 형식)

프록시는 \를 이스케이프 기호로 사용하여 JSON 파일에서 모든 문자열을 읽습니다. 프록시는 또한 중괄호를 해석합니다. 아래 예제 전체를 참조하세요.

|문자|이스케이프된 문자|예|
|-|-|-|
|{ 또는 }|{{ 또는 }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>requestOverrides 개체 정의

requestOverrides 개체는 백 엔드 리소스가 호출될 때 요청에 대한 변경 내용을 정의합니다. 개체는 다음 속성으로 정의됩니다.

* **backend.request.method**: 백 엔드를 호출하는 데 사용되는 HTTP 메서드입니다.
* **backend.request.querystring.\<ParameterName\>**: 백 엔드에 대한 호출에 설정할 수 있는 쿼리 문자열 매개 변수입니다. *\<ParameterName\>* 을 설정하려는 매개 변수 이름으로 바꿉니다. 빈 문자열이 제공되면 매개 변수는 백 엔드 요청에 포함되지 않습니다.
* **backend.request.headers.\<HeaderName\>**: 백 엔드 호출을 위해 설정할 수 있는 헤더입니다. *\<HeaderName\>* 을 설정하려는 헤더 이름으로 바꿉니다. 빈 문자열을 제공하면 헤더는 백 엔드 요청에 포함되지 않습니다.

값은 애플리케이션 설정 및 원래 클라이언트 요청의 매개 변수를 참조할 수 있습니다.

예제 구성은 다음과 유사할 수 있습니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="responseOverrides"></a>responseOverrides 개체 정의

requestOverrides 개체는 클라이언트에 다시 전달된 응답에 대한 변경 내용을 정의합니다. 개체는 다음 속성으로 정의됩니다.

* **response.statusCode**: 클라이언트에 반환할 HTTP 상태 코드입니다.
* **response.statusReason**: 클라이언트에 반환할 HTTP 이유 구문입니다.
* **response.body**: 클라이언트에 반환할 본문의 문자열 표현입니다.
* **response.headers.\<HeaderName\>**: 클라이언트에 대한 응답에 설정할 수 있는 헤더입니다. *\<HeaderName\>* 을 설정하려는 헤더 이름으로 바꿉니다. 빈 문자열을 제공하면 헤더는 응답에 포함되지 않습니다.

값은 애플리케이션 설정, 원래 클라이언트 요청의 매개 변수 및 백 엔드 응답의 매개 변수를 참조할 수 있습니다.

예제 구성은 다음과 유사할 수 있습니다.

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> 이 예제에서 응답 본문은 직접 설정되므로 `backendUri` 속성이 필요하지 않습니다. 다음 예제에서는 모의 API에 Azure Functions 프록시를 어떻게 사용할 수 있는지를 보여 줍니다.

[Azure Portal]: https://portal.azure.com
[HTTP 트리거]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[requestOverrides 개체 정의]: #requestOverrides
[responseOverrides 개체 정의]: #responseOverrides
[애플리케이션 설정]: #use-appsettings
[변수 사용]: #using-variables
[원래 클라이언트 요청의 매개 변수]: #request-parameters
[백 엔드 응답의 매개 변수]: #response-parameters
