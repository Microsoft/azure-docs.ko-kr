---
title: Azure Functions SendGrid 바인딩
description: Azure Functions SendGrid 바인딩 참조.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 32734ff9df2e55d24789742cd49984d8da212a17
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212197"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 바인딩

이 문서에서는 Azure Functions에서 [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) 바인딩을 사용하여 전자 메일을 보내는 방법에 대해 설명합니다. Azure Functions는 SendGrid에 대해 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

SendGrid 바인딩은 [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

SendGrid 바인딩은 [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 Service Bus 큐 트리거와 SendGrid 출력 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

### <a name="synchronous"></a>동기

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

### <a name="asynchronous"></a>비동기

```cs
using SendGrid.Helpers.Mail;

...

[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

"AzureWebJobsSendGridApiKey"라는 앱 설정에 API 키가 있는 경우 특성의 `ApiKey` 속성 설정을 생략할 수 있습니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 SendGrid 출력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 SendGrid 출력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, input) {
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 SendGrid 바인딩을 사용 하 여 전자 메일을 보내는 HTTP 트리거 함수를 보여 줍니다. 바인딩 구성에서 기본값을 제공할 수 있습니다. 예를 들어 *보낸* 사람 전자 메일 주소는 *function.js*에 구성 됩니다. 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "sendGrid",
      "name": "sendGridMessage",
      "direction": "out",
      "apiKey": "SendGrid_API_Key",
      "from": "sender@contoso.com"
    }
  ]
}
```

다음 함수는 선택적 속성에 대 한 사용자 지정 값을 제공 하는 방법을 보여 줍니다.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, sendGridMessage: func.Out[str]) -> func.HttpResponse:

    value = "Sent from Azure Functions"

    message = {
        "personalizations": [ {
          "to": [{
            "email": "user@contoso.com"
            }]}],
        "subject": "Azure Functions email with SendGrid",
        "content": [{
            "type": "text/plain",
            "value": value }]}

    sendGridMessage.set(json.dumps(message))

    return func.HttpResponse(f"Sent")
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 `@SendGridOutput` [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime) 의 주석을 사용 하 여 SendGrid 출력 바인딩을 사용 하는 전자 메일을 보냅니다.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerSendGrid {

    @FunctionName("HttpTriggerSendGrid")
    public HttpResponseMessage run(

        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.GET, HttpMethod.POST },
            authLevel = AuthorizationLevel.FUNCTION)
                HttpRequestMessage<Optional<String>> request,

        @SendGridOutput(
            name = "message",
            dataType = "String",
            apiKey = "SendGrid_API_Key",
            to = "user@contoso.com",
            from = "sender@contoso.com",
            subject = "Azure Functions email with SendGrid",
            text = "Sent from Azure Functions")
                OutputBinding<String> message,

        final ExecutionContext context) {

        final String toAddress = "user@contoso.com";
        final String value = "Sent from Azure Functions";

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"personalizations\": [{ \"to\": [{ \"email\": \"%s\"}]}],")
            .append("\"content\": [{\"type\": \"text/plain\", \"value\": \"%s\"}]")
            .append("}");

        final String body = String.format(builder.toString(), toAddress, value);

        message.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs) 특성을 사용합니다.

구성할 수 있는 특성 속성에 대한 자세한 내용은 [구성](#configuration)을 참조하세요. 다음은 메서드 서명의 `SendGrid` 특성 예제입니다.

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

전체 예제는 [c # 예제](#example)를 참조 하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Sendgridoutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/SendGridOutput.java) 주석을 사용 하면 구성 값을 제공 하 여 SendGrid 바인딩을 선언적으로 구성할 수 있습니다. 자세한 내용은 [예제](#example) 및 [구성](#configuration) 섹션을 참조하세요.

---

## <a name="configuration"></a>구성

다음 표에서는  *function.js* 파일 및 `SendGrid` 특성/주석에 사용할 수 있는 바인딩 구성 속성을 보여 줍니다.

| 속성 *function.js* | 특성/주석 속성 | 설명 | 선택 |
|--------------------------|-------------------------------|-------------|----------|
| 형식 |해당 없음| `sendGrid`로 설정해야 합니다.| 아니요 |
| direction |해당 없음| `out`로 설정해야 합니다.| 예 |
| name |해당 없음| 요청 또는 요청 본문의 함수 코드에 사용 되는 변수 이름입니다. 반환 값이 하나만 있는 경우 이 값은 `$return`입니다. | 아니요 |
| apiKey | ApiKey | API 키가 포함 된 앱 설정의 이름입니다. 설정 되지 않은 경우 기본 앱 설정 이름은 *Azurewebjobssendgridapikey*입니다.| 예 |
| to| 대상 | 받는 사람의 이메일 주소입니다. | 예 |
| 원본| 보낸 사람 | 보낸 사람의 전자 메일 주소입니다. |  예 |
| subject| 제목 | 전자 메일의 제목입니다. | 예 |
| text| 텍스트 | 전자 메일 내용입니다. | 예 |

선택적 속성은 바인딩에 정의 된 기본값을 포함 하 고 프로그래밍 방식으로 추가 또는 재정의 될 수 있습니다.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래 파일에 host.js예제에는이 바인딩에 대 한 버전 2.x + 설정만 포함 되어 있습니다. 2.x 이상 버전의 전역 구성 설정에 대 한 자세한 내용은 [ Azure Functions에 대 한 참조host.js](functions-host-json.md)를 참조 하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|속성  |기본값 | Description |
|---------|---------|---------| 
|원본|해당 없음|모든 함수에서 보낸 사람의 이메일 주소입니다.| 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
