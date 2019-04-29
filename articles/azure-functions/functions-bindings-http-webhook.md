---
title: Azure Functions HTTP 트리거 및 바인딩
description: Azure Functions에서 HTTP 트리거 및 바인딩을 사용하는 방법에 대해 설명합니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버리스 아키텍처, HTTP, API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: a1d66cf4506e3b8f58572576db908812f4e2be07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104376"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions HTTP 트리거 및 바인딩

이 문서에서는 Azure Functions에서 HTTP 트리거 및 출력 바인딩을 사용하는 방법에 대해 설명합니다.

HTTP 트리거는 [웹후크](https://en.wikipedia.org/wiki/Webhook)에 응답하도록 사용자 지정할 수 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

이 문서의 코드는 기본적으로 .NET Core를 사용하는 Functions 2.x 구문으로 설정됩니다. 1.x 구문에 대한 자세한 내용은 [1.x 기능 템플릿](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)을 참조하세요.

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

HTTP 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet 패키지 버전 1.x에 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

HTTP 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) NuGet 패키지 버전 3.x에 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>트리거

HTTP 트리거를 사용하면 HTTP 요청으로 함수를 호출할 수 있습니다. HTTP 트리거를 사용하여 서버리스 API를 만들고 웹후크에 응답할 수 있습니다.

기본적으로 HTTP 트리거는 Functions 1.x에서 본문이 비어 있는 HTTP 200 OK 또는 Functions 2.x에서 본문이 비어 있는 HTTP 204 No Content를 반환합니다. 응답을 수정하려면 [HTTP 출력 바인딩](#output)을 구성합니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다. 반환 값은 출력 바인딩에 사용되지만, 반환 값 특성은 필요 없습니다.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 `HttpRequest`에 바인딩하는 C# 스크립트 코드입니다.

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

`HttpRequest` 대신 사용자 지정 개체에 바인딩할 수 있습니다. 이 개체는 요청 본문에서 만들어지고 JSON으로 구문 분석됩니다. 마찬가지로, 형식을 HTTP 응답 출력 바인딩으로 전달할 수도 있습니다. 그러면 200 상태 코드를 갖는 응답 본문으로 반환됩니다.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>트리거 - F# 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](functions-reference-fsharp.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

F# 코드는 다음과 같습니다.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

다음 예제와 같이 NuGet을 사용하여 `FSharp.Interop.Dynamic` 및 `Dynamitey` 어셈블리를 참조하는 `project.json` 파일이 필요합니다.

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예제는 *function.json* 파일의 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### <a name="trigger---python-example"></a>트리거 - Python 예제

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여 줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>트리거 - Java 예제

* [쿼리 문자열에서 매개 변수 읽기](#read-parameter-from-the-query-string-java)
* [POST 요청에서 본문 읽기](#read-body-from-a-post-request-java)
* [경로에서 매개 변수 읽기](#read-parameter-from-a-route-java)
* [POST 요청에서 POJO 본문 읽기](#read-pojo-body-from-a-post-request-java)

다음 예제는 *function.json* 파일의 HTTP 트리거 바인딩과 이 바인딩을 사용하는 해당 [Java 함수](functions-reference-java.md)를 보여 줍니다. 

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>쿼리 문자열에서 매개 변수 읽기(Java)  

이 예제에서는 쿼리 문자열에서 ```id```라는 매개 변수를 읽고 이 매개 변수를 사용하여 콘텐츠 형식 ```application/json```으로 클라이언트에 반환되는 JSON 문서를 빌드합니다. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>POST 요청에서 본문 읽기(Java)  

이 예제에서는 POST 요청의 본문을 ```String```으로 읽고, 이 매개 변수를 사용하여 콘텐츠 형식 ```application/json```으로 클라이언트에 반환되는 JSON 문서를 빌드합니다.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>경로에서 매개 변수 읽기(Java)  

이 예제에서는 라우팅 경로에서 ```id```라는 필수 매개 변수와 선택적 매개 변수 ```name```을 읽은 후 이러한 매개 변수를 사용하여 콘텐츠 형식 ```application/json```으로 클라이언트에 반환되는 JSON 문서를 빌드합니다. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>POST 요청에서 POJO 본문 읽기(Java)  

다음은 이 예제에서 참조되는 ```ToDoItem``` 클래스의 코드입니다.

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

이 예제에서는 POST 요청의 본문을 읽습니다. 요청 본문은 자동으로 ```ToDoItem``` 개체로 역직렬화된 후 콘텐츠 형식 ```application/json```을 사용하여 클라이언트에 반환됩니다. ```ToDoItem``` 매개 변수는 ```HttpMessageResponse.Builder``` 클래스의 ```body``` 속성에 할당될 때 Functions 런타임에 의해 직렬화됩니다.

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 특성을 사용합니다.

특성 생성자 매개 변수에 권한 부여 수준 및 허용되는 HTTP 메서드를 설정할 수 있으며 웹후크 유형 및 경로 템플릿에 대한 속성이 있습니다. 이러한 설정에 대한 자세한 내용은 [트리거 - 구성](#trigger---configuration)을 참조하세요. 다음은 메서드 서명의 `HttpTrigger` 특성입니다.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `HttpTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
| **type** | 해당 없음| 필수 - `httpTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음| 필수 - `in`으로 설정해야 합니다. |
| **name** | 해당 없음| 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |키가 있는 경우 함수를 호출하기 위해 요청에 포함되어야 하는 키를 결정합니다. 권한 부여 수준은 다음 값 중 하나일 수 있습니다. <ul><li><code>anonymous</code>&mdash;: API 키가 필요하지 않습니다.</li><li><code>function</code>&mdash;: 함수 전용 API 키가 필요합니다. authLevel 속성 값을 제공하지 않을 경우 기본값입니다.</li><li><code>admin</code>&mdash;: 마스터 키가 필요합니다.</li></ul> 자세한 내용은 [권한 부여 키](#authorization-keys)에 대한 섹션을 참조하세요. |
| **methods** |**메서드** | 함수에서 응답할 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 함수에서 모든 HTTP 메서드에 응답합니다. [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **route** | **Route** | 경로 템플릿을 정의하여 함수에서 응답할 요청 URL을 제어합니다. 값을 제공하지 않을 경우 기본값은 `<functionname>`입니다. 자세한 내용은 [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **webHookType** | **WebHookType** | _버전 1.x 런타임에서만 지원됩니다._<br/><br/>HTTP 트리거가 지정된 공급자의 [웹후크](https://en.wikipedia.org/wiki/Webhook) 수신기(receiver)로 작동하도록 구성합니다. 이 속성을 설정하면 `methods` 속성을 설정하지 마십시오. 웹후크 형식은 다음 값 중 하나일 수 있습니다.<ul><li><code>genericJson</code>&mdash;특정 공급자를 위한 논리가 없는 범용 webhook 엔드포인트입니다. 이 설정은 HTTP POST 및 `application/json` 콘텐츠 형식을 사용하는 요청으로만 제한됩니다.</li><li><code>github</code>&mdash;이 함수는 [GitHub 웹후크](https://developer.github.com/webhooks/)에 응답합니다. GitHub 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 GitHub 웹후크 섹션을 참조하세요.</li><li><code>slack</code>&mdash;이 함수는 [Slack 웹후크](https://api.slack.com/outgoing-webhooks)에 응답합니다. Slack 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 Slack 웹후크 섹션을 참조하세요.</li></ul>|

## <a name="trigger---usage"></a>트리거 - 사용

C# 및 F# 함수의 경우 트리거 입력 형식을 `HttpRequest` 또는 사용자 지정 형식으로 선언할 수 있습니다. `HttpRequest`를 선택하면 요청 개체에 대한 모든 권한을 갖게 됩니다. 사용자 지정 형식의 경우 런타임은 JSON 요청 본문의 구문을 분석하여 개체 속성을 설정하려고 합니다.

JavaScript 함수의 경우 함수 런타임은 요청 개체 대신 요청 본문을 제공합니다. 자세한 내용은 [JavaScript 트리거 예제](#trigger---javascript-example)를 참조하세요.

### <a name="customize-the-http-endpoint"></a>HTTP 엔드포인트 사용자 지정

기본적으로 HTTP 트리거용 함수를 만드는 경우 폼의 경로를 사용하여 이 함수의 주소를 지정할 수 있습니다.

    http://<yourapp>.azurewebsites.net/api/<funcname>

HTTP 트리거의 입력 바인딩에서 선택적 `route` 속성을 사용하여 이 경로를 사용자 지정할 수 있습니다. 예를 들어 다음 *function.json* 파일은 HTTP 트리거에 대한 `route` 속성을 정의합니다.

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

이 구성을 사용하면 원래 경로 대신 다음 경로를 사용하여 함수의 주소를 지정할 수 있습니다.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

이렇게 하면 함수 코드에서 주소의 두 매개 변수, _category_ 및 _id_를 지원할 수 있습니다. 매개 변수에서 [웹 API 경로 제약 조건](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)을 사용할 수 있습니다. 다음 C# 함수 코드는 두 매개 변수를 모두 사용합니다.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

다음은 동일한 경로 매개 변수를 사용하는 Node.js 함수 코드입니다.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

기본적으로 모든 함수 경로에는 *api* 접두사가 붙습니다. [host.json](functions-host-json.md) 파일에서 `http.routePrefix` 속성을 사용하여 접두사를 사용자 지정하거나 제거할 수도 있습니다. 다음 예제에서는 *host.json* 파일에서 빈 문자열을 접두사로 사용하여 *api* 경로 접두사를 제거합니다.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>클라이언트 ID 사용

함수 앱이 [App Service 인증 / 권한 부여](../app-service/overview-authentication-authorization.md)를 사용하는 경우 코드에서 인증된 클라이언트에 대한 정보를 볼 수 있습니다. 이 정보는 [플랫폼에 의해 삽입된 요청 헤더](../app-service/app-service-authentication-how-to.md#access-user-claims)로서 사용할 수 있습니다. 

데이터 바인딩에서 이 정보를 읽을 수도 있습니다. 이 기능은 Functions 2.x 런타임에서만 사용할 수 있습니다. 또한 이 기능은 현재 .NET 언어에 대해서만 사용할 수 있습니다.

.NET 언어에서 이 정보는 [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal?view=netstandard-2.0)로 사용할 수 있습니다. 다음 예제에 표시된 대로 ClaimsPrincipal을 요청 컨텍스트의 일부로 사용할 수 있습니다.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

또는 ClaimsPrincipal은 함수 시그니처에 단순히 추가 매개 변수로 포함될 수 있습니다.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>권한 부여 키

함수에서는 키를 사용해 개발 중에 HTTP 함수 엔드포인트 액세스를 더 어렵게 만들 수 있습니다.  표준 HTTP 트리거의 경우 요청에 이러한 API 키가 있어야 할 수 있습니다. 

> [!IMPORTANT]
> 키가 있으면 개발 중에 HTTP 엔드포인트를 난독 처리할 수는 있지만, 프로덕션 환경에서 HTTP 트리거를 보호할 수는 없습니다. 자세히 알아보려면 [프로덕션 환경에서 HTTP 엔드포인트 보호](#secure-an-http-endpoint-in-production)를 참조하세요.

> [!NOTE]
> Functions 1.x 런타임에서는 웹후크 공급자가 키를 사용하여 공급자가 지원하는 항목에 따라 다양한 방식으로 요청에 권한을 부여할 수 있습니다. 여기에 대해서는 [웹후크 및 키](#webhooks-and-keys)에서 다룹니다. 버전 2.x 런타임에서는 웹후크 공급자가 기본적으로 지원되지 않습니다.

다음과 같이 두 가지 유형의 키가 있습니다.

* **호스트 키**: 함수 앱 내의 모든 함수에서 공유합니다. API 키로 사용되면 이 키를 통해 함수 앱 내의 모든 함수에 액세스할 수 있습니다.
* **함수 키**: 정의된 특정 함수에만 적용됩니다. API 키로 사용되면 이 키를 통해 해당 함수에만 액세스할 수 있습니다.

각 키의 이름은 참조될 수 있도록 지정되며 함수 및 호스트 수준에서는 "default"라는 기본 키가 있습니다. function 키는 호스트 키보다 우선합니다. 두 키가 동일한 이름으로 정의되면 항상 함수 키가 사용됩니다.

각 함수 앱에는 특수 **마스터 키**도 있습니다. 이 키는 런타임 API에 대한 관리자 권한을 제공하는 `_master` 호스트 키입니다. 이 키를 취소할 수 없습니다. 권한 부여 수준 `admin`을 설정하는 경우 요청은 마스터 키를 사용해야 하며, 다른 키를 사용하는 경우 권한 부여가 실패합니다.

> [!CAUTION]  
> 함수 앱에서는 마스터 키를 통해 높은 권한이 부여되므로, 이 키를 제3자와 공유하거나 네이티브 클라이언트 애플리케이션에 배포해서는 안 됩니다. 따라서 관리자 권한 부여 수준을 선택할 때는 주의해야 합니다.

### <a name="obtaining-keys"></a>키 확보

키는 Azure에 함수 앱의 일부로 저장되며 나머지는 암호화되어 있습니다. 키를 보거나 새 키를 만들거나 키를 새 값으로 전환하려면 [Azure Portal](https://portal.azure.com)에서 HTTP 트리거 함수 중 하나로 이동한 다음 **관리**를 선택합니다.

![포털에서 함수 키를 관리합니다.](./media/functions-bindings-http-webhook/manage-function-keys.png)

프로그래밍 방식으로 함수 키를 확인하는 데 사용할 수 있도록 지원되는 API는 없습니다.

### <a name="api-key-authorization"></a>API 키 권한 부여

대다수 HTTP 트리거 템플릿을 사용할 때는 요청에 API 키가 필요합니다. 따라서 HTTP 요청은 일반적으로 다음 URL과 같습니다.

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

위에 나와 있는 것처럼 쿼리 문자열 변수 `code`에 키를 포함할 수 있습니다. `x-functions-key` HTTP 헤더에 키를 포함할 수도 있습니다. 키 값은 함수에 대해 정의된 모든 function 키 또는 모든 호스트 키일 수 있습니다.

키를 요구하지 않는 익명 요청을 허용할 수 있습니다. 마스터 키를 사용하도록 요구할 수도 있습니다. 바인딩 JSON에서 `authLevel` 속성을 사용하여 기본 권한 수준을 변경합니다. 자세한 내용은 [트리거 - 구성](#trigger---configuration)을 참조하세요.

> [!NOTE]
> 함수를 로컬로 실행할 때는 지정된 권한 부여 수준 설정에 관계없이 권한 부여가 사용하지 않도록 설정됩니다. Azure에 게시하고 나면 트리거의 `authLevel` 설정이 적용됩니다.



### <a name="secure-an-http-endpoint-in-production"></a>프로덕션 환경에서 HTTP 엔드포인트 보호

프로덕션 환경에서 함수 엔드포인트를 완벽하게 보호하려면 다음의 함수 앱 수준 보안 옵션 중 하나를 구현해야 합니다.

* 함수 앱에 대해 App Service 인증/권한 부여를 설정합니다. App Service 플랫폼에서는 AAD(Azure Active Directory) 및 기타 여러 타사 ID 공급자를 사용하여 클라이언트를 인증할 수 있습니다. 이 플랫폼을 통해 함수용 사용자 지정 권한 부여 규칙을 구현할 수 있으며, 함수 코드에서 사용자 정보를 사용할 수 있습니다. 자세히 알아보려면 [Azure App Service에서 인증 및 권한 부여](../app-service/overview-authentication-authorization.md) 및 [클라이언트 ID 사용](#working-with-client-identities)을 참조하세요.

* Azure APIM(API Management)를 사용하여 요청을 인증합니다. APIM은 수신 요청에 사용할 수 있는 여러 가지 API 보안 옵션을 제공합니다. 자세히 알아보려면 [API Management 인증 정책](../api-management/api-management-authentication-policies.md)을 참조하세요. APIM을 적용하면 APIM 인스턴스의 IP 주소에서 보내는 요청만 수락하도록 함수 앱을 구성할 수 있습니다. 자세히 알아보려면 [IP 주소 제한](ip-addresses.md#ip-address-restrictions)을 참조하세요.

* Azure ASE(App Service Environment)에 함수 앱을 배포합니다. ASE는 함수를 실행할 전용 호스팅 환경을 제공합니다. ASE 사용 시에는 모든 수신 요청을 인증하는 데 사용할 수 있는 단일 프런트 엔드 게이트웨이를 구성할 수 있습니다. 자세한 내용은 [App Service Environment용 WAF(웹 애플리케이션 방화벽) 구성](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)을 참조하세요.

이러한 함수 앱 수준 보안 방법 중 하나를 사용할 때는 HTTP 트리거 함수 인증 수준을 `anonymous`로 설정해야 합니다.

### <a name="webhooks"></a>Webhook

> [!NOTE]
> 웹후크 모드는 Functions 런타임의 버전 1.x에서만 사용 가능합니다. 버전 2.x에서 HTTP 트리거의 성능 향상을 위해 이렇게 변경되었습니다.

버전 1.x에서 웹후크 템플릿은 웹후크 페이로드용으로 추가 유효성 검사를 제공합니다. 버전 2.x의 경우 기본 HTTP 트리거가 계속 작동하며, 웹후크에는 이 방식을 사용하는 것이 좋습니다. 

#### <a name="github-webhooks"></a>GitHub 웹후크

GitHub 웹후크에 응답하려면 먼저 HTTP 트리거를 사용하여 함수를 만들고 **webHookType** 속성을 `github`로 설정합니다. 그런 다음 URL 및 API 키를 GitHub 리포지토리의 **웹후크 추가** 페이지에 복사합니다. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Slack 웹후크

Slack webhook은 함수 전용 키를 지정하는 대신 사용자를 위한 토큰을 생성하므로 Slack의 토큰을 사용하여 함수 전용 키를 구성해야 합니다. [권한 부여 키](#authorization-keys)를 참조하세요.

### <a name="webhooks-and-keys"></a>웹후크 및 키

웹후크 인증은 HTTP 트리거의 일부로 웹후크 수신기 구성 요소에서 처리하며 메커니즘은 웹후크 유형에 따라 다릅니다. 각 메커니즘은 키를 사용합니다. 기본적으로 "default"라는 함수 키가 사용됩니다. 다른 키를 사용하려면 다음 중 한 가지 방법으로 요청과 함께 키 이름을 보내도록 웹후크 공급자를 구성합니다.

* **쿼리 문자열**: 공급자에서 `clientid` 쿼리 문자열 매개 변수에 키 이름을 전달합니다(예: `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`).
* **요청 헤더**: 공급자에서 `x-functions-clientid` 헤더에 키 이름을 전달합니다.

## <a name="trigger---limits"></a>트리거 - 제한

HTTP 요청 길이는 100MB(104,857,600바이트)로 제한되고 URL 길이는 4KB(4,096바이트)로 제한됩니다. 이러한 제한은 런타임의 [Web.config 파일](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)의 `httpRuntime` 요소에 의해 지정됩니다.

HTTP 트리거를 사용하는 함수가 약 2.5분 안에 완료되지 않으면 게이트웨이가 시간 제한을 적용하고 HTTP 502 오류를 반환합니다. 함수는 계속 실행되지만 HTTP 응답은 반환할 수 없습니다. 장기 실행 함수의 경우 비동기 패턴을 따르고 요청 상태를 ping할 수 있는 위치를 반환하는 것이 좋습니다. 함수 실행 시간에 대한 정보는 [크기 조정 및 호스팅 - 소비 계획](functions-scale.md#consumption-plan)을 참조하세요.

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](functions-host-json.md) 파일에는 HTTP 트리거 동작을 제어하는 설정이 포함됩니다.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>출력

HTTP 요청 발신기(sender)에 응답하려면 HTTP 출력 바인딩을 사용합니다. 이 바인딩에는 HTTP 트리거가 필요하며 트리거 요청과 관련된 응답을 사용자 지정할 수 있습니다. HTTP 출력 바인딩을 제공하지 않으면 HTTP 트리거는 Functions 1.x에서 본문이 비어 있는 HTTP 200 OK 또는 Functions 2.x에서 본문이 비어 있는 HTTP 204 No Content를 반환합니다.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일에 설정된 바인딩 구성 속성을 설명합니다. C# 클래스 라이브러리의 경우 *function.json* 속성에 해당하는 attribute 속성이 없습니다.

|자산  |설명  |
|---------|---------|
| **type** |`http`로 설정해야 합니다. |
| **direction** | `out`로 설정해야 합니다. |
|**name** | 응답에 대한 함수 코드에 사용되는 변수 이름이거나 반환 값을 사용하는 `$return`입니다. |

## <a name="output---usage"></a>출력 - 사용

HTTP 응답을 보내려면 언어 표준 응답 패턴을 사용합니다. C# 또는 C# 스크립트에서는 함수 반환 형식을 `IActionResult` 또는 `Task<IActionResult>`로 만듭니다. C#에서는 반환 값 특성이 필요 없습니다.

예제 응답은 [트리거 예제](#trigger---example)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
