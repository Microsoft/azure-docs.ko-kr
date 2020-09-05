---
title: Azure Functions HTTP 트리거
description: HTTP를 통해 Azure 함수를 호출하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 29ec547a6033b77d92ad7949df286dc94e3243a2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213937"
---
# <a name="azure-functions-http-trigger"></a>Azure Functions HTTP 트리거

HTTP 트리거를 사용하면 HTTP 요청으로 함수를 호출할 수 있습니다. HTTP 트리거를 사용하여 서버리스 API를 만들고 웹후크에 응답할 수 있습니다.

HTTP 트리거 함수의 기본 반환 값은 다음과 같습니다.

- Functions 2.x 이상에서 빈 본문이 있는 `HTTP 204 No Content`
- Functions 1.x에서 빈 본문이 있는 `HTTP 200 OK`

HTTP 응답을 수정하려면 [출력 바인딩](./functions-bindings-http-webhook-output.md)을 구성합니다.

HTTP 바인딩에 대한 자세한 내용은 [개요](./functions-bindings-http-webhook.md) 및 [출력 바인딩 참조](./functions-bindings-http-webhook-output.md)를 참조하세요.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

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

`HttpRequest` 대신 사용자 지정 개체에 바인딩할 수 있습니다. 이 개체는 요청 본문에서 만들어지고 JSON으로 구문 분석됩니다. 마찬가지로, 형식을 HTTP 응답 출력 바인딩으로 전달할 수도 있습니다. 그러면 `200` 상태 코드를 갖는 응답 본문으로 반환됩니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

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

# <a name="python"></a>[Python](#tab/python)

다음 예제는 *function.json* 파일의 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](functions-reference-python.md)를 보여줍니다. 함수는 쿼리 문자열이나 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

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

# <a name="java"></a>[Java](#tab/java)

* [쿼리 문자열에서 매개 변수 읽기](#read-parameter-from-the-query-string)
* [POST 요청에서 본문 읽기](#read-body-from-a-post-request)
* [경로에서 매개 변수 읽기](#read-parameter-from-a-route)
* [POST 요청에서 POJO 본문 읽기](#read-pojo-body-from-a-post-request)

다음 예제에서는 HTTP 트리거 바인딩을 보여 줍니다.

#### <a name="read-parameter-from-the-query-string"></a>쿼리 문자열에서 매개 변수 읽기

이 예제에서는 쿼리 문자열에서 `id`라는 매개 변수를 읽고 이 매개 변수를 사용하여 콘텐츠 형식 `application/json`으로 클라이언트에 반환되는 JSON 문서를 빌드합니다.

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

#### <a name="read-body-from-a-post-request"></a>POST 요청에서 본문 읽기

이 예제에서는 POST 요청의 본문을 `String`으로 읽고, 이 매개 변수를 사용하여 콘텐츠 형식 `application/json`으로 클라이언트에 반환되는 JSON 문서를 빌드합니다.

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

#### <a name="read-parameter-from-a-route"></a>경로에서 매개 변수 읽기

이 예제에서는 라우팅 경로에서 `id`라는 필수 매개 변수와 선택적 매개 변수 `name`을 읽은 후 이러한 매개 변수를 사용하여 콘텐츠 형식 `application/json`으로 클라이언트에 반환되는 JSON 문서를 빌드합니다. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>POST 요청에서 POJO 본문 읽기

다음은 이 예제에서 참조되는 `ToDoItem` 클래스의 코드입니다.

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

이 예제에서는 POST 요청의 본문을 읽습니다. 요청 본문은 자동으로 `ToDoItem` 개체로 역직렬화된 후 콘텐츠 형식 `application/json`을 사용하여 클라이언트에 반환됩니다. `ToDoItem` 매개 변수는 `HttpMessageResponse.Builder` 클래스의 `body` 속성에 할당될 때 Functions 런타임에 의해 직렬화됩니다.

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

---

## <a name="attributes-and-annotations"></a>특성 및 주석

[C# 클래스 라이브러리](functions-dotnet-class-library.md) 및 Java에서는 함수를 구성하는 데 `HttpTrigger` 특성을 사용할 수 있습니다.

특성 생성자 매개 변수, 웹후크 유형 및 경로 템플릿에 권한 부여 수준 및 허용되는 HTTP 메서드를 설정할 수 있습니다. 이러한 구성 설정에 대한 자세한 내용은 [구성](#configuration)을 참조하세요.

# <a name="c"></a>[C#](#tab/csharp)

이 예제에서는 [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) 특성을 사용하는 방법을 보여 줍니다.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

전체 예제는 [트리거 예제](#example)를 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

이 예제에서는 [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) 특성을 사용하는 방법을 보여 줍니다.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

전체 예제는 [트리거 예제](#example)를 참조하세요.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `HttpTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
| **type** | 해당 없음| 필수 - `httpTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음| 필수 - `in`으로 설정해야 합니다. |
| **name** | 해당 없음| 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |키가 있는 경우 함수를 호출하기 위해 요청에 포함되어야 하는 키를 결정합니다. 권한 부여 수준은 다음 값 중 하나일 수 있습니다. <ul><li><code>anonymous</code>&mdash;: API 키가 필요하지 않습니다.</li><li><code>function</code>&mdash;: 함수 전용 API 키가 필요합니다. authLevel 속성 값을 제공하지 않을 경우 기본값입니다.</li><li><code>admin</code>&mdash;: 마스터 키가 필요합니다.</li></ul> 자세한 내용은 [권한 부여 키](#authorization-keys)에 대한 섹션을 참조하세요. |
| **methods** |**메서드** | 함수에서 응답할 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 함수에서 모든 HTTP 메서드에 응답합니다. [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **route** | **Route** | 경로 템플릿을 정의하여 함수에서 응답할 요청 URL을 제어합니다. 값을 제공하지 않을 경우 기본값은 `<functionname>`입니다. 자세한 내용은 [HTTP 엔드포인트 사용자 지정](#customize-the-http-endpoint)을 참조하세요. |
| **webHookType** | **WebHookType** | _버전 1.x 런타임에서만 지원됩니다._<br/><br/>HTTP 트리거가 지정된 공급자의 [웹후크](https://en.wikipedia.org/wiki/Webhook) 수신기(receiver)로 작동하도록 구성합니다. 이 속성을 설정하면 `methods` 속성을 설정하지 마십시오. 웹후크 형식은 다음 값 중 하나일 수 있습니다.<ul><li><code>genericJson</code>&mdash;특정 공급자를 위한 논리가 없는 범용 webhook 엔드포인트입니다. 이 설정은 HTTP POST 및 `application/json` 콘텐츠 형식을 사용하는 요청으로만 제한됩니다.</li><li><code>github</code>&mdash;이 함수는 [GitHub 웹후크](https://developer.github.com/webhooks/)에 응답합니다. GitHub 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 GitHub 웹후크 섹션을 참조하세요.</li><li><code>slack</code>&mdash;이 함수는 [Slack 웹후크](https://api.slack.com/outgoing-webhooks)에 응답합니다. Slack 웹후크에는 _authLevel_ 속성을 사용하지 마십시오. 자세한 내용은 이 문서의 뒷부분에서 Slack 웹후크 섹션을 참조하세요.</li></ul>|

## <a name="payload"></a>페이로드

트리거 입력 형식은 `HttpRequest` 또는 사용자 지정 형식으로 선언됩니다. `HttpRequest`를 선택하면 요청 개체에 대한 모든 권한을 갖게 됩니다. 사용자 지정 형식의 경우 런타임은 JSON 요청 본문의 구문을 분석하여 개체 속성을 설정하려고 합니다.

## <a name="customize-the-http-endpoint"></a>HTTP 엔드포인트 사용자 지정

기본적으로 HTTP 트리거용 함수를 만드는 경우 폼의 경로를 사용하여 이 함수의 주소를 지정할 수 있습니다.

```http
http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>
```

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

이 구성으로 함수 코드에서 주소의 두 매개 변수, _category_ 및 _id_를 지원할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

매개 변수에서 [웹 API 경로 제약 조건](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)을 사용할 수 있습니다. 다음 C# 함수 코드는 두 매개 변수를 모두 사용합니다.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

매개 변수에서 [웹 API 경로 제약 조건](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)을 사용할 수 있습니다. 다음 C# 함수 코드는 두 매개 변수를 모두 사용합니다.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

노드에서 함수 런타임은 `context` 개체의 요청 본문을 제공합니다. 자세한 내용은 [JavaScript 트리거 예제](#example)를 참조하세요.

다음 예제에서는 `context.bindingData`에서 경로 매개 변수를 읽는 방법을 보여줍니다.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

함수 실행 컨텍스트는 `func.HttpRequest`로 선언된 매개 변수를 통해 노출됩니다. 이 인스턴스를 사용하면 함수에서 데이터 경로 매개 변수, 쿼리 문자열 값 및 HTTP 응답을 반환할 수 있는 메서드를 액세스할 수 있습니다.

정의되면 경로 매개 변수는 `route_params` 메서드를 호출하여 함수에 사용할 수 있습니다.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

함수 실행 컨텍스트는 `HttpTrigger` 특성에 선언된 속성입니다. 특성을 사용하여 경로 매개 변수, 권한 부여 수준, HTTP 동사 및 들어오는 요청 인스턴스를 정의할 수 있습니다.

경로 매개 변수는 `HttpTrigger` 특성을 통해 정의됩니다.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

기본적으로 모든 함수 경로에는 *api* 접두사가 붙습니다. [host.json](functions-host-json.md) 파일에서 `extensions.http.routePrefix` 속성을 사용하여 접두사를 사용자 지정하거나 제거할 수도 있습니다. 다음 예제에서는 *host.json* 파일에서 빈 문자열을 접두사로 사용하여 *api* 경로 접두사를 제거합니다.

```json
{
    "extensions": {
        "http": {
            "routePrefix": ""
        }
    }
}
```

## <a name="using-route-parameters"></a>경로 매개 변수 사용

함수의 `route` 패턴을 정의하는 경로 매개 변수는 각 바인딩에 사용할 수 있습니다. 예를 들어 `"route": "products/{id}"`로 정의된 경로가 있는 경우 테이블 스토리지 바인딩은 바인딩 구성에서 `{id}` 매개 변수의 값을 사용할 수 있습니다.

다음 구성에서는 `{id}` 매개 변수가 바인딩의 `rowKey`로 전달되는 방법을 보여 줍니다.

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>클라이언트 ID 사용

함수 앱이 [App Service 인증 / 권한 부여](../app-service/overview-authentication-authorization.md)를 사용하는 경우 코드에서 인증된 클라이언트에 대한 정보를 볼 수 있습니다. 이 정보는 [플랫폼에 의해 삽입된 요청 헤더](../app-service/app-service-authentication-how-to.md#access-user-claims)로서 사용할 수 있습니다. 

데이터 바인딩에서 이 정보를 읽을 수도 있습니다. 이 기능은 2.x 이상의 Functions 런타임에서만 사용할 수 있습니다. 또한 이 기능은 현재 .NET 언어에 대해서만 사용할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

인증된 클라이언트에 대한 정보는 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)로 제공됩니다. 다음 예제에 표시된 대로 ClaimsPrincipal을 요청 컨텍스트의 일부로 사용할 수 있습니다.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

인증된 클라이언트에 대한 정보는 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)로 제공됩니다. 다음 예제에 표시된 대로 ClaimsPrincipal을 요청 컨텍스트의 일부로 사용할 수 있습니다.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

인증된 사용자는 [HTTP 헤더](../app-service/app-service-authentication-how-to.md#access-user-claims)를 통해 사용할 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

인증된 사용자는 [HTTP 헤더](../app-service/app-service-authentication-how-to.md#access-user-claims)를 통해 사용할 수 있습니다.

# <a name="java"></a>[Java](#tab/java)

인증된 사용자는 [HTTP 헤더](../app-service/app-service-authentication-how-to.md#access-user-claims)를 통해 사용할 수 있습니다.

---

## <a name="function-access-keys"></a><a name="authorization-keys"></a>함수 액세스 키

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

## <a name="obtaining-keys"></a>키 확보

키는 Azure에 함수 앱의 일부로 저장되며 나머지는 암호화되어 있습니다. 키를 보거나 새 키를 만들거나 키를 새 값으로 전환하려면 [Azure Portal](https://portal.azure.com)에서 HTTP 트리거 함수 중 하나로 이동한 다음 **관리**를 선택합니다.

![포털에서 함수 키를 관리합니다.](./media/functions-bindings-http-webhook/manage-function-keys.png)

[키 관리 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)를 사용하여 프로그래밍 방식으로 함수 키를 가져올 수 있습니다.

## <a name="api-key-authorization"></a>API 키 권한 부여

대다수 HTTP 트리거 템플릿을 사용할 때는 요청에 API 키가 필요합니다. 따라서 HTTP 요청은 일반적으로 다음 URL과 같습니다.

```http
https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>
```

위에 나와 있는 것처럼 쿼리 문자열 변수 `code`에 키를 포함할 수 있습니다. `x-functions-key` HTTP 헤더에 키를 포함할 수도 있습니다. 키 값은 함수에 대해 정의된 모든 function 키 또는 모든 호스트 키일 수 있습니다.

키를 요구하지 않는 익명 요청을 허용할 수 있습니다. 마스터 키를 사용하도록 요구할 수도 있습니다. 바인딩 JSON에서 `authLevel` 속성을 사용하여 기본 권한 수준을 변경합니다. 자세한 내용은 [트리거 - 구성](#configuration)을 참조하세요.

> [!NOTE]
> 함수를 로컬로 실행할 때는 지정된 권한 부여 수준 설정에 관계없이 권한 부여가 사용하지 않도록 설정됩니다. Azure에 게시하고 나면 트리거의 `authLevel` 설정이 적용됩니다. [컨테이너에서 로컬로](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally) 실행하는 경우에도 키가 필요합니다.


## <a name="secure-an-http-endpoint-in-production"></a>프로덕션 환경에서 HTTP 엔드포인트 보호

프로덕션 환경에서 함수 엔드포인트를 완벽하게 보호하려면 다음의 함수 앱 수준 보안 옵션 중 하나를 구현해야 합니다. 이러한 함수 앱 수준 보안 방법 중 하나를 사용할 때는 HTTP 트리거 함수 인증 수준을 `anonymous`로 설정해야 합니다.

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

#### <a name="deploy-your-function-app-in-isolation"></a>격리로 함수 앱 배포

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

## <a name="webhooks"></a>Webhook

> [!NOTE]
> 웹후크 모드는 Functions 런타임의 버전 1.x에서만 사용 가능합니다. 버전 2.x 이상에서 HTTP 트리거의 성능 향상을 위해 이렇게 변경되었습니다.

버전 1.x에서 웹후크 템플릿은 웹후크 페이로드용으로 추가 유효성 검사를 제공합니다. 버전 2.x 이상의 경우 기본 HTTP 트리거가 계속 작동하며, 웹후크에는 이 방식을 사용하는 것이 좋습니다. 

### <a name="github-webhooks"></a>GitHub 웹후크

GitHub 웹후크에 응답하려면 먼저 HTTP 트리거를 사용하여 함수를 만들고 **webHookType** 속성을 `github`로 설정합니다. 그런 다음 URL 및 API 키를 GitHub 리포지토리의 **웹후크 추가** 페이지에 복사합니다. 

![함수에 대 한 webhook를 추가 하는 방법을 보여 주는 스크린샷](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack 웹후크

Slack webhook은 함수 전용 키를 지정하는 대신 사용자를 위한 토큰을 생성하므로 Slack의 토큰을 사용하여 함수 전용 키를 구성해야 합니다. [권한 부여 키](#authorization-keys)를 참조하세요.

## <a name="webhooks-and-keys"></a>웹후크 및 키

웹후크 인증은 HTTP 트리거의 일부로 웹후크 수신기 구성 요소에서 처리하며 메커니즘은 웹후크 유형에 따라 다릅니다. 각 메커니즘은 키를 사용합니다. 기본적으로 "default"라는 함수 키가 사용됩니다. 다른 키를 사용하려면 다음 중 한 가지 방법으로 요청과 함께 키 이름을 보내도록 웹후크 공급자를 구성합니다.

* **쿼리 문자열**: 공급자에서 `clientid` 쿼리 문자열 매개 변수에 키 이름을 전달합니다(예: `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`).
* **요청 헤더**: 공급자에서 `x-functions-clientid` 헤더에 키 이름을 전달합니다.

## <a name="content-types"></a>내용 유형

비 C # 함수에 이진 및 폼 데이터를 전달 하려면 적절 한 content-type 헤더를 사용 해야 합니다. 지원 되는 콘텐츠 형식은 `octet-stream` 이진 데이터 및 [다중 파트 형식](https://www.iana.org/assignments/media-types/media-types.xhtml#multipart)에 포함 됩니다.

### <a name="known-issues"></a>알려진 문제

비 C # 함수에서 content-type을 사용 하 여 전송 된 요청 `image/jpeg` 은 `string` 함수에 전달 된 값을 반환 합니다. 이와 같은 경우에는 `string` 값을 바이트 배열로 수동으로 변환 하 여 원시 이진 데이터에 액세스할 수 있습니다.

## <a name="limits"></a>제한

HTTP 요청 길이는 100MB(104,857,600바이트)로 제한되고 URL 길이는 4KB(4,096바이트)로 제한됩니다. 이러한 제한은 런타임의 [Web.config 파일](https://github.com/Azure/azure-functions-host/blob/v3.x/src/WebJobs.Script.WebHost/web.config)의 `httpRuntime` 요소에 의해 지정됩니다.

HTTP 트리거를 사용하는 함수가 약 230초 안에 완료되지 않으면 [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)가 시간 제한을 적용하고 HTTP 502 오류를 반환합니다. 함수는 계속 실행되지만 HTTP 응답은 반환할 수 없습니다. 장기 실행 함수의 경우 비동기 패턴을 따르고 요청 상태를 ping할 수 있는 위치를 반환하는 것이 좋습니다. 함수 실행 시간에 대한 정보는 [크기 조정 및 호스팅 - 소비 계획](functions-scale.md#timeout)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [함수에서 HTTP 응답 반환](./functions-bindings-http-webhook-output.md)
