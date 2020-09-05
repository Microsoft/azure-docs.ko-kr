---
title: Azure Functions Twilio 바인딩
description: Azure Functions와 함께 Twilio 바인딩을 사용하는 방법을 배웁니다.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: devx-track-csharp, H1Hack27Feb2017
ms.openlocfilehash: 9a3ffeb4ff58cc4af2309268f1a95cd9d354de8b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206634"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions에 대한 Twilio 바인딩

이 문서에서는 Azure Functions에서 [Twilio](https://www.twilio.com/) 바인딩을 사용하여 문자 메시지를 보내는 방법에 대해 설명합니다. Azure Functions는 Twilio에 대한 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Twilio 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 패키지 버전 1.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

Twilio 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>예제-함수 2.x 이상

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 큐 메시지에서 트리거되면 문자 메시지를 보내는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

이 예제에서는 메서드 반환 값과 `TwilioSms` 특성을 사용합니다. 또는 `out CreateMessageOptions` 매개 변수나, `ICollector<CreateMessageOptions>` 또는 `IAsyncCollector<CreateMessageOptions>` 매개 변수와 특성을 사용합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 Twilio 출력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 이 함수는 `out` 매개 변수를 사용하여 문자 메시지를 보냅니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Out 매개 변수는 비동기 코드에서 사용할 수 없습니다. 비동기 C# 스크립트 코드 예제는 다음과 같습니다.

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 Twilio 출력 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 함수](functions-reference-node.md)에서 테이블 출력 바인딩을 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 다음 *function.js*에 정의 된 대로 출력 바인딩을 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

Serialize 된 JSON 개체를 `func.Out` 매개 변수로 전달 하 여 SMS 메시지를 보낼 수 있습니다.

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 주석을 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다. , 및에 대 한 값 `to` `from` `body` 은 프로그래밍 방식으로 재정의 하는 경우에도 특성 정의에 필요 합니다.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) 특성을 사용합니다.

구성할 수 있는 특성 속성에 대한 자세한 내용은 [구성](#configuration)을 참조하세요. 다음은 메서드 서명의 `TwilioSms` 특성 예제입니다.

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
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

[TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 주석을 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 매개 변수에 추가 `T` 합니다. 여기서은,, `int` `String` `byte[]` 또는 pojo 형식과 같은 네이티브 Java 형식일 수 있습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `TwilioSms` 특성에 설정된 바인딩 구성 속성을 설명합니다.

| v1 function.json 속성 | v2 function.json 속성 | 특성 속성 |Description|
|---------|---------|---------|----------------------|
|**type**|**type**| `twilioSms`로 설정해야 합니다.|
|**direction**|**direction**| `out`로 설정해야 합니다.|
|**name**|**name**| Twilio SMS 텍스트 메시지에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| 이 값은 Twilio 계정 Sid ()를 포함 하는 앱 설정의 이름으로 설정 해야 합니다 `TwilioAccountSid` . 설정되지 않은 경우 기본 앱 설정 이름은 "AzureWebJobsTwilioAccountSid"입니다. |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| 이 값은 Twilio 인증 토큰 ()을 보유 하는 앱 설정의 이름으로 설정 해야 합니다 `TwilioAccountAuthToken` . 설정되지 않은 경우 기본 앱 설정 이름은 "AzureWebJobsTwilioAuthToken"입니다. |
|**to**| 해당 없음 - 코드에서 지정 | **수행할 작업**| 이 값은 SMS 텍스트 메시지가 전송된 전화 번호로 설정됩니다.|
|**from**|**from** | **From**| 이 값은 SMS 텍스트 메시지를 보낸 전화 번호로 설정됩니다.|
|**body**|**body** | **본문**| 이 값은 함수에 대한 코드에서 동적으로 설정할 필요가 없는 경우에 SMS 텍스트 메시지를 하드 코딩하는 데 사용할 수 있습니다. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
