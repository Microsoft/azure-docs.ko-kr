---
title: Durable Functions의 HTTP 기능-Azure Functions
description: Azure Functions Durable Functions 확장의 통합 HTTP 기능에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: a7d8891c6f925cfac326685f01ba5f6149a1b233
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262863"
---
# <a name="http-features"></a>HTTP 기능

Durable Functions에는 지 속성 오케스트레이션 및 엔터티를 HTTP 워크플로에 쉽게 통합할 수 있는 몇 가지 기능이 있습니다. 이 문서에서는 이러한 기능 중 일부에 대해 자세히 설명 합니다.

## <a name="exposing-http-apis"></a>HTTP Api 노출

HTTP 요청을 사용 하 여 오케스트레이션 및 엔터티를 호출 하 고 관리할 수 있습니다. Durable Functions 확장 프로그램은 기본 제공 HTTP Api를 노출 합니다. 또한 HTTP 트리거 함수 내에서 오케스트레이션 및 엔터티와 상호 작용 하기 위한 Api를 제공 합니다.

### <a name="built-in-http-apis"></a>기본 제공 HTTP Api

Durable Functions 확장 프로그램은 HTTP Api 집합을 Azure Functions 호스트에 자동으로 추가 합니다. 이러한 Api를 사용 하면 코드를 작성 하지 않고도 오케스트레이션 및 엔터티를 조작 하 고 관리할 수 있습니다.

지원 되는 기본 제공 HTTP Api는 다음과 같습니다.

* [새 오케스트레이션 시작](durable-functions-http-api.md#start-orchestration)
* [오케스트레이션 인스턴스 쿼리](durable-functions-http-api.md#get-instance-status)
* [오케스트레이션 인스턴스 종료](durable-functions-http-api.md#terminate-instance)
* [오케스트레이션에 외부 이벤트 보내기](durable-functions-http-api.md#raise-event)
* [오케스트레이션 기록 제거](durable-functions-http-api.md#purge-single-instance-history)
* [엔터티에 작업 이벤트 보내기](durable-functions-http-api.md#signal-entity)
* [엔터티 상태 가져오기](durable-functions-http-api.md#get-entity)
* [엔터티 목록 쿼리](durable-functions-http-api.md#list-entities)

Durable Functions 확장에 의해 노출 되는 모든 기본 제공 HTTP Api에 대 한 자세한 설명은 [HTTP api 문서](durable-functions-http-api.md) 를 참조 하세요.

### <a name="http-api-url-discovery"></a>HTTP API URL 검색

[오케스트레이션 클라이언트 바인딩은](durable-functions-bindings.md#orchestration-client) 편리한 HTTP 응답 페이로드를 생성할 수 있는 api를 노출 합니다. 예를 들어 특정 오케스트레이션 인스턴스에 대 한 관리 Api에 대 한 링크가 포함 된 응답을 만들 수 있습니다. 다음 예에서는 새 오케스트레이션 인스턴스에이 API를 사용 하는 방법을 보여 주는 HTTP 트리거 함수를 보여 줍니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

이전에 표시 된 HTTP 트리거 함수를 사용 하 여 orchestrator 함수를 시작 하려면 모든 HTTP 클라이언트를 사용 해야 합니다. 다음 말아 명령은 `DoWork`orchestrator 함수를 시작 합니다.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

다음은 해당 ID로 `abc123` 있는 오케스트레이션에 대 한 예제 응답입니다. 명확 하 게 하기 위해 일부 세부 정보가 제거 되었습니다.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

이전 예제에서 `Uri`로 끝나는 각 필드는 기본 제공 HTTP API에 해당 합니다. 이러한 Api를 사용 하 여 대상 오케스트레이션 인스턴스를 관리할 수 있습니다.

> [!NOTE]
> Webhook Url의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 다릅니다. 이전 예제는 Azure Functions 2.0 호스트를 위한 것입니다.

모든 기본 제공 HTTP Api에 대 한 설명은 [HTTP api 참조](durable-functions-http-api.md)를 참조 하세요.

### <a name="async-operation-tracking"></a>비동기 작업 추적

앞에서 언급한 HTTP 응답은 Durable Functions를 사용하여 장기 실행 HTTP 비동기 API를 구현하는 데 도움이 되도록 설계되었습니다. 이 패턴을 *폴링 소비자 패턴이*라고도 합니다. 클라이언트/서버 흐름은 다음과 같이 작동합니다.

1. 클라이언트는 orchestrator 함수와 같은 장기 실행 프로세스를 시작 하기 위해 HTTP 요청을 발급 합니다.
1. 대상 HTTP 트리거는 "statusQueryGetUri" 값을 가진 Location 헤더를 사용 하 여 HTTP 202 응답을 반환 합니다.
1. 클라이언트는 Location 헤더의 URL을 폴링합니다. 클라이언트는 위치 헤더를 사용 하 여 HTTP 202 응답을 계속 표시 합니다.
1. 인스턴스가 완료 되거나 실패 하면 Location 헤더의 엔드포인트에서 HTTP 200을 반환 합니다.

이 프로토콜을 사용 하면 HTTP 엔드포인트을 폴링하고 Location 헤더를 따를 수 있는 외부 클라이언트 또는 서비스를 사용 하 여 장기 실행 프로세스를 조정할 수 있습니다. 이 패턴의 클라이언트 및 서버 구현은 모두 Durable Functions HTTP Api에 기본 제공 됩니다.

> [!NOTE]
> 기본적으로 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)에서 제공하는 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 이 기능을 사용하면 Logic Apps 워크플로의 일부로 장기 실행 지속성 함수를 포함할 수 있습니다. 비동기 HTTP 패턴에 대 한 Logic Apps 지원에 대 한 자세한 내용은 [Azure Logic Apps 워크플로 작업 및 트리거 설명서](../../logic-apps/logic-apps-workflow-actions-triggers.md)에서 확인할 수 있습니다.

> [!NOTE]
> 오케스트레이션과의 상호 작용은 HTTP로 트리거되는 함수 뿐만 아니라 모든 함수 형식에서 수행할 수 있습니다.

클라이언트 Api를 사용 하 여 오케스트레이션 및 엔터티를 관리 하는 방법에 대 한 자세한 내용은 [인스턴스 관리 문서](durable-functions-instance-management.md)를 참조 하세요.

## <a name="consuming-http-apis"></a>HTTP Api 사용

[Orchestrator 함수 코드 제약 조건](durable-functions-code-constraints.md)에 설명 된 대로 오 케 스트레이 터 함수는 i/o를 직접 수행할 수 없습니다. 대신 일반적으로 i/o 작업을 수행 하는 [작업 함수](durable-functions-types-features-overview.md#activity-functions) 를 호출 합니다.

Durable Functions 2.0부터 오케스트레이션은 [오케스트레이션 트리거 바인딩을](durable-functions-bindings.md#orchestration-trigger)사용 하 여 HTTP api를 기본적으로 사용할 수 있습니다.

다음 예제 코드는 아웃 바운드 HTTP 요청을 만드는 오 케 스트레이 터 함수를 보여 줍니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

"Call HTTP" 작업을 사용 하 여 orchestrator 함수에서 다음 작업을 수행할 수 있습니다.

* 오케스트레이션 함수에서 직접 HTTP Api를 호출 하 고 나중에 설명 하는 몇 가지 제한 사항이 있습니다.
* 클라이언트 쪽 HTTP 202 상태 폴링 패턴을 자동으로 지원 합니다.
* [Azure 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 다른 azure 엔드포인트에 대 한 권한 있는 HTTP 호출을 만듭니다.

Orchestrator 함수에서 직접 HTTP Api를 사용 하는 기능은 특정 한 일반적인 시나리오 집합의 편의를 위해 작성 되었습니다. 활동 함수를 사용 하 여 이러한 모든 기능을 직접 구현할 수 있습니다. 대부분의 경우 작업 함수는 더 많은 유연성을 제공할 수 있습니다.

### <a name="http-202-handling"></a>HTTP 202 처리

"Call HTTP" API는 폴링 소비자 패턴의 클라이언트 쪽을 자동으로 구현할 수 있습니다. 호출 된 API가 Location 헤더를 사용 하 여 HTTP 202 응답을 반환 하는 경우 오 케 스트레이 터 함수는 202 이외의 응답을 받을 때까지 위치 리소스를 자동으로 폴링합니다. 이 응답은 오 케 스트레이 터 함수 코드에 반환 되는 응답입니다.

> [!NOTE]
> 또한 오 케 스트레이 터 함수는 [비동기 작업 추적](#async-operation-tracking)에 설명 된 대로 서버 쪽 폴링 소비자 패턴을 기본적으로 지원 합니다. 이 지원은 한 함수 앱의 오케스트레이션이 다른 함수 앱의 오 케 스트레이 터 함수를 쉽게 조정할 수 있음을 의미 합니다. 이는 [하위 오케스트레이션](durable-functions-sub-orchestrations.md) 개념과 유사 하지만 앱 간 통신을 지원 합니다. 이 지원은 마이크로 서비스 스타일 앱 개발에 특히 유용 합니다.

### <a name="managed-identities"></a>관리 ID

Durable Functions은 권한 부여를 위해 Azure Active Directory (Azure AD) 토큰을 허용 하는 Api에 대 한 호출을 기본적으로 지원 합니다. 이 지원에서는 [Azure 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 이러한 토큰을 가져옵니다.

다음 코드는 .NET orchestrator 함수의 예제입니다. 이 함수는 [REST API Azure Resource Manager 가상](https://docs.microsoft.com/rest/api/compute/virtualmachines)컴퓨터를 사용 하 여 가상 컴퓨터를 다시 시작 하도록 인증 된 호출을 수행 합니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

이전 예제에서 `tokenSource` 매개 변수는 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)에 대 한 Azure AD 토큰을 획득 하도록 구성 됩니다. 토큰은 리소스 URI `https://management.core.windows.net`로 식별 됩니다. 이 예제에서는 현재 함수 앱이 로컬로 실행 되 고 있거나 관리 id를 사용 하 여 함수 앱으로 배포 된 것으로 가정 합니다. 로컬 id 또는 관리 id에 `myRG`지정 된 리소스 그룹의 Vm을 관리할 수 있는 권한이 있다고 가정 합니다.

런타임에 구성 된 토큰 소스는 OAuth 2.0 액세스 토큰을 자동으로 반환 합니다. 그런 다음 소스는 들어오는 요청의 권한 부여 헤더에 토큰을 전달자 토큰으로 추가 합니다. 이 모델은 다음 이유로 인해 HTTP 요청에 권한 부여 헤더를 수동으로 추가 하는 것 보다 향상 되었습니다.

* 토큰 새로 고침이 자동으로 처리 됩니다. 만료 된 토큰에 대해 걱정 하지 않아도 됩니다.
* 토큰은 내구성이 있는 오케스트레이션 상태에 저장 되지 않습니다.
* 토큰 획득을 관리 하기 위해 코드를 작성할 필요가 없습니다.

[미리 컴파일된 C# RestartVMs 샘플](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)에서 더 완전 한 예제를 찾을 수 있습니다.

관리 id는 Azure 리소스 관리로 제한 되지 않습니다. 관리 id를 사용 하 여 Microsoft 및 파트너의 웹 앱에서 Azure 서비스를 비롯 한 Azure AD 전달자 토큰을 허용 하는 모든 API에 액세스할 수 있습니다. 파트너의 웹 앱은 다른 함수 앱 일 수도 있습니다. Azure AD로 인증을 지 원하는 Microsoft의 Azure 서비스 목록은 [AZURE ad 인증을 지 원하는 azure 서비스](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조 하세요.

### <a name="limitations"></a>제한 사항

HTTP Api 호출에 대 한 기본 제공 지원은 편리한 기능입니다. 일부 시나리오에는 적합 하지 않습니다.

오 케 스트레이 터 함수에서 보낸 HTTP 요청 및 응답은 큐 메시지로 직렬화 되 고 지속 됩니다. 이러한 큐 동작을 통해 HTTP 호출은 [안정적이 고 오케스트레이션 재생에 안전](durable-functions-orchestrations.md#reliability)합니다. 그러나 큐 동작에는 다음과 같은 제한 사항도 있습니다.

* 각 HTTP 요청에는 네이티브 HTTP 클라이언트와 비교할 때 추가 대기 시간이 포함 됩니다.
* 큐 메시지에 포함할 수 없는 큰 요청 또는 응답 메시지는 오케스트레이션 성능을 크게 저하 시킬 수 있습니다. 메시지 페이로드를 blob 저장소로 오프 로드 하는 오버 헤드로 인해 성능이 저하 될 수 있습니다.
* 스트리밍, 청크 분할 및 이진 페이로드는 지원 되지 않습니다.
* HTTP 클라이언트의 동작을 사용자 지정 하는 기능이 제한 됩니다.

이러한 제한 사항으로 인해 사용 사례에 영향을 줄 수 있는 경우에는 대신 작업 함수와 언어별 HTTP 클라이언트 라이브러리를 사용 하 여 아웃 바운드 HTTP 호출을 수행 하는 것이 좋습니다.

> [!NOTE]
> .NET 개발자 인 경우이 기능이 기본 제공 .NET **HttpRequestMessage** 및 **HttpResponseMessage** 형식 대신 **DurableHttpRequest** 및 **DurableHttpResponse** 형식을 사용 하는 이유를 궁금할 수 있습니다.
>
> 이 디자인 선택은 의도적입니다. 주된 이유는 사용자 지정 형식을 사용 하 여 사용자가 내부 HTTP 클라이언트의 지원 되는 동작에 대해 잘못 된 가정을 하지 못하도록 하는 것입니다. Durable Functions 관련 형식만 API 디자인을 간소화할 수 있습니다. 또한 [관리 id 통합](#managed-identities) 및 [폴링 소비자 패턴과](#http-202-handling)같은 특수 기능을 보다 쉽게 사용할 수 있습니다. 

### <a name="extensibility-net-only"></a>확장성 (.NET만 해당)

[Azure Functions .net 종속성 주입](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)을 사용 하 여 오케스트레이션의 내부 HTTP 클라이언트 동작을 사용자 지정할 수 있습니다. 이 기능은 작은 동작 변경을 수행 하는 데 유용할 수 있습니다. 모의 개체를 삽입 하 여 HTTP 클라이언트를 단위 테스트 하는 데에도 유용할 수 있습니다.

다음 예제에서는 종속성 주입을 사용 하 여 외부 HTTP 엔드포인트을 호출 하는 오 케 스트레이 터 함수에 대해 SSL 인증서 유효성 검사를 사용 하지 않도록

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지 속성 엔터티에 대해 알아보기](durable-functions-entities.md)
