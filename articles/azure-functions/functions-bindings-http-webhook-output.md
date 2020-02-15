---
title: Azure Functions HTTP 출력 바인딩
description: Azure Functions에서 HTTP 응답을 반환 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210906"
---
# <a name="azure-functions-http-output-bindings"></a>Azure Functions HTTP 출력 바인딩

HTTP 요청 발신기(sender)에 응답하려면 HTTP 출력 바인딩을 사용합니다. 이 바인딩에는 HTTP 트리거가 필요하며 트리거 요청과 관련된 응답을 사용자 지정할 수 있습니다.

HTTP 트리거 함수의 기본 반환 값은 다음과 같습니다.

- 함수 2.x 이상에서 본문이 비어 있는 `HTTP 204 No Content`
- 함수 1. x의 본문이 비어 있는 `HTTP 200 OK`

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. C# 클래스 라이브러리의 경우 *function.json* 속성에 해당하는 attribute 속성이 없습니다.

|속성  |Description  |
|---------|---------|
| **type** |`http`로 설정해야 합니다. |
| **direction** | `out`로 설정해야 합니다. |
| **name** | 응답에 대한 함수 코드에 사용되는 변수 이름이거나 반환 값을 사용하는 `$return`입니다. |

## <a name="usage"></a>사용

HTTP 응답을 보내려면 언어 표준 응답 패턴을 사용합니다. C# 또는 C# 스크립트에서는 함수 반환 형식을 `IActionResult` 또는 `Task<IActionResult>`로 만듭니다. C#에서는 반환 값 특성이 필요 없습니다.

예제 응답은 [트리거 예제](./functions-bindings-http-webhook-trigger.md#example)를 참조하세요.

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래의 예제 호스트 json 파일에는이 바인딩에 대 한 버전 2.x + 설정만 포함 되어 있습니다. 2\.x 이상 버전의 전역 구성 설정에 대 한 자세한 내용은 [Azure Functions에 대 한 호스트 json 참조](functions-host-json.md)를 참조 하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md#http)를 참조하세요.

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|속성  |기본값 | Description |
|---------|---------|---------| 
| customHeaders|none|HTTP 응답에서 사용자 지정 헤더를 설정할 수 있습니다. 이전 예제에서는 콘텐츠 형식 스니핑을 방지 하기 위해 `X-Content-Type-Options` 헤더를 응답에 추가 합니다. |
|dynamicThrottlesEnabled|true<sup>\*</sup>|이 설정을 사용 하면 요청 처리 파이프라인이 `connections/threads/processes/memory/cpu/etc`와 같은 시스템 성능 카운터를 주기적으로 확인 하 고, 해당 카운터가 기본 제공 높은 임계값 (80%)을 초과 하는 경우에는 카운터가 정상 수준으로 돌아올 때까지 `429 "Too Busy"` 응답으로 요청이 거부 됩니다.<br/><sup>\*</sup> 소비 계획의 기본값은 `true`입니다. 전용 계획의 기본값은 `false`입니다.|
|hsts|사용 안 함|`isEnabled`을 `true`로 설정 하면 [`HstsOptions` 클래스](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)에 정의 된 대로 [.NET CORE의 Hsts (HTTP Strict Transport Security) 동작이](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) 적용 됩니다. 또한 위의 예제에서는 [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) 속성을 10 일로 설정 합니다. `hsts` 지원 되는 속성은 다음과 같습니다. <table><tr><th>속성</th><th>Description</th></tr><tr><td>excludedHosts</td><td>HSTS 헤더가 추가 되지 않은 호스트 이름의 문자열 배열입니다.</td></tr><tr><td>includeSubDomains</td><td>엄격한 전송 보안 헤더의 includeSubDomain 도메인 매개 변수를 사용할 수 있는지 여부를 나타내는 부울 값입니다.</td></tr><tr><td>maxAge</td><td>엄격한 전송 보안 헤더의 최대 보존 기간 매개 변수를 정의 하는 문자열입니다.</td></tr><tr><td>미리 로드</td><td>엄격한 전송 보안 헤더의 미리 로드 매개 변수를 사용할 수 있는지 여부를 나타내는 부울입니다.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|병렬로 실행 되는 최대 HTTP 함수 수입니다. 이 값을 사용 하면 리소스 사용률을 관리 하는 데 도움이 되는 동시성을 제어할 수 있습니다. 예를 들어 많은 수의 시스템 리소스 (메모리/c p u/소켓)를 사용 하는 HTTP 함수를 사용 하 여 동시성이 너무 높을 때 문제를 발생 시킬 수 있습니다. 또는 타사 서비스에 아웃 바운드 요청을 하는 함수를 사용 하 고 해당 호출에 대 한 요금이 제한 되어야 할 수 있습니다. 이러한 경우 여기에서 제한을 적용하는 것이 좋습니다. <br/><sup>*</sup> 소비 계획의 기본값은 100입니다. 전용 계획의 기본값은 제한 없음 (`-1`)입니다.|
|maxOutstandingRequests|200<sup>\*</sup>|지정된 시간에 보유할 미해결 요청의 최대 수입니다. 이 제한에는 대기 중이지만 실행이 시작되지 않은 요청과 진행 중인 모든 실행이 포함됩니다. 이 한도를 초과하여 들어오는 요청이 있으면 429 "Too Busy" 응답으로 거부됩니다. 그러면 호출자가 시간 기반 다시 시도 전략을 사용할 수 있고 최대 요청 대기 시간을 제어할 수 있습니다. 이 옵션은 스크립트 호스트 실행 경로 내에서 발생하는 큐만을 제어합니다. ASP.NET 요청 큐와 같은 다른 큐는 여전히 적용되며 이 설정의 영향을 받지 않습니다. <br/><sup>\*</sup> 소비 계획의 기본값은 200입니다. 전용 계획의 기본값은 제한 없음 (`-1`)입니다.|
|routePrefix|api|모든 경로에 적용되는 경로 접두사입니다. 기본 접두사를 제거하려면 빈 문자열을 사용하십시오. |

## <a name="next-steps"></a>다음 단계

- [HTTP 요청에서 함수 실행](./functions-bindings-http-webhook-trigger.md)