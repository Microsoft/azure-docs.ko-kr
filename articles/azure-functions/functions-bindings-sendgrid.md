---
title: Azure Functions SendGrid 바인딩
description: Azure Functions SendGrid 바인딩 참조.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: 663283c4dff16194dea78afbf5dd393f1fa833b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437961"
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 바인딩

이 문서에서는 Azure Functions에서 [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) 바인딩을 사용하여 전자 메일을 보내는 방법에 대해 설명합니다. Azure Functions는 SendGrid에 대해 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

SendGrid 바인딩은 [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

SendGrid 바인딩은 [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>예

언어 관련 예제를 참조하세요.

* [C#](#c-example)
* [C# 스크립트(.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>C# 예제

다음 예제에서는 Service Bus 큐 트리거와 SendGrid 출력 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

#### <a name="synchronous-c-example"></a>동기 C# 예제:

```cs
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
#### <a name="asynchronous-c-example"></a>비동기 C# 예제:

```cs
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

### <a name="c-script-example"></a>C# 스크립트 예제

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

### <a name="java-example"></a>Java 예제

다음 예제에서는 합니다 `@SendGridOutput` 주석을 합니다 [Java 런타임 라이브러리 함수](/java/api/overview/azure/functions/runtime) SendGrid를 사용 하 여 전자 메일을 보낼 출력 바인딩.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>JavaScript 예제

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

## <a name="attributes"></a>특성

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

전체 예제는 [C# 예제](#c-example)를 참조하세요.

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `SendGrid` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type**|| 필수 - `sendGrid`으로 설정해야 합니다.|
|**direction**|| 필수 - `out`으로 설정해야 합니다.|
|**name**|| 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. 반환 값이 하나만 있는 경우 이 값은 ```$return```입니다. |
|**apiKey**|**ApiKey**| API 키가 포함 된 앱 설정의 이름입니다. 설정되지 않은 경우 기본 앱 설정 이름은 "AzureWebJobsSendGridApiKey"입니다.|
|**to**|**To**| 수신자의 전자 메일 주소입니다. |
|**from**|**From**| 발신자의 전자 메일 주소입니다. |
|**subject**|**제목**| 메일의 제목입니다. |
|**text**|**Text**| 전자 메일 내용입니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 아래 예제 host.json 파일에는 이 바인딩에 대한 버전 2.x 설정만 포함되어 있습니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

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

|자산  |Default | 설명 |
|---------|---------|---------| 
|from|해당 없음|모든 함수에서 보낸 사람의 이메일 주소입니다.| 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
