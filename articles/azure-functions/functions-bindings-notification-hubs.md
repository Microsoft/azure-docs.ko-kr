---
title: Azure Functions에 대한 Notification Hubs 바인딩
description: Azure Functions에서 Azure 알림 허브 바인딩을 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 79ea9455fec7d31f800b2b5d36df6a2a53f502c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438198"
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Azure Functions에 대한 Notification Hubs 출력 바인딩

이 문서는 Azure Functions에서 [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) 바인딩을 사용하여 푸시 알림을 보내는 방법을 설명합니다. Azure Functions는 Notification Hub의 출력 바인딩을 지원합니다.

Azure Notification Hubs는 사용할 PNS(플랫폼 알림 서비스)에 대해 구성되어야 합니다. Notification Hubs에서 클라이언트 앱의 푸시 알림을 받는 방법을 알아보려면 [Notification Hubs 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)을 참조하고 페이지 위쪽의 드롭다운 목록에서 대상 클라이언트 플랫폼을 선택하세요.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!IMPORTANT]
> Google에 [메시징 GCM (Google Cloud)를 위해 FCM Firebase Cloud Messaging () 사용 되지 않는](https://developers.google.com/cloud-messaging/faq)합니다. 이 출력 바인딩은 FCM을 지원 하지 않습니다. FCM을 사용 하 여 알림을 보내려면 다음을 사용 합니다 [Firebase API](https://firebase.google.com/docs/cloud-messaging/server#choosing-a-server-option) 함수 또는 사용 하 여에서 직접 [템플릿 알림을](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)합니다.

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Notification Hubs는 [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs) NuGet 패키지 버전 1.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.NotificationHubs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

이 바인딩은 Functions 2.x에서 사용할 수 없습니다.

## <a name="example---template"></a>예제 - 템플릿

보내는 알림은 기본 알림 또는 [템플릿 알림](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)일 수 있습니다. 기본 알림은 출력 바인딩의 `platform` 속성에 구성된 특정 클라이언트 플랫폼을 대상으로 합니다. 템플릿 알림은 여러 플랫폼을 대상으로 하는 데 사용할 수 있습니다.   

언어 관련 예제를 참조하세요.

* [C# 스크립트 - out 매개 변수](#c-script-template-example---out-parameter)
* [C# 스크립트 - 비동기](#c-script-template-example---asynchronous)
* [C# 스크립트 - JSON](#c-script-template-example---json)
* [C# 스크립트 - 라이브러리 형식](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>C# 스크립트 템플릿 예제 - out 매개 변수

이 예제에서는 템플릿에 `message` 자리 표시자가 포함된 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>C# 스크립트 템플릿 예제 - 비동기

비동기 코드를 사용하는 경우 out 매개 변수가 허용되지 않습니다. 이 경우 `IAsyncCollector`를 사용하여 템플릿 알림을 반환합니다. 다음 코드는 위 코드의 비동기 예제입니다. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>C# 스크립트 템플릿 예제 - JSON

이 예제에서는 유효한 JSON 문자열을 사용하여 템플릿에 `message` 자리 표시자가 포함된 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>C# 스크립트 템플릿 예제 - 라이브러리 형식

이 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>F# 템플릿 예제

이 예제에서는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>JavaScript 템플릿 예제

이 예제에서는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)에 대한 알림을 보냅니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>예제 - APNS 기본

이 C# 스크립트 예제는 기본 APNS 알림을 보내는 방법을 보여줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.LogInformation($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.LogInformation($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---wns-native"></a>예제 - WNS 기본

이 C# 스크립트 예제에서는 [Microsoft Azure Notification Hubs 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 형식을 사용하여 기본 WNS 알림 메시지를 보내는 방법을 보여 줍니다. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) 특성을 사용합니다.

특성의 생성자 매개 변수 및 속성은 [구성](#configuration) 섹션에 설명되어 있습니다.

## <a name="configuration"></a>구성

다음 테이블에서는 *function.json* 파일 및 `NotificationHub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** |해당 없음| "notificationHub"로 설정해야 합니다. |
|**direction** |해당 없음| "out"으로 설정해야 합니다. | 
|**name** |해당 없음| 알림 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**tagExpression** |**TagExpression** | 태그 식을 사용하면 태그 식과 일치하는 알림을 수신하도록 등록된 일련의 디바이스에 배달하도록 지정할 수 있습니다.  자세한 내용은 [라우팅 및 태그 식](../notification-hubs/notification-hubs-tags-segment-push-message.md)을 참조하세요. |
|**hubName** | **HubName** | Azure Portal에서 알림 허브 리소스의 이름입니다. |
|**연결** | **ConnectionStringSetting** | Notification Hubs 연결 문자열을 포함하는 앱 설정의 이름입니다.  연결 문자열은 알림 허브의 *DefaultFullSharedAccessSignature* 값으로 설정해야 합니다. 이 문서의 뒷부분에 나오는 [연결 문자열 설정](#connection-string-setup)을 참조하세요.|
|**platform** | **플랫폼** | platform 속성은 알림의 대상으로 지정된 클라이언트 플랫폼을 나타냅니다. 기본적으로 출력 바인딩에서 platform 속성을 생략하면 템플릿 알림을 사용하여 Azure Notification Hub에 구성된 플랫폼을 대상으로 지정할 수 있습니다. 일반적으로 Azure 알림 허브 알림에서 템플릿을 사용하여 플랫폼 간 알림을 보내는 방법에 대한 자세한 내용은 [템플릿](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)을 참조하세요. **platform**을 설정한 경우 다음 값 중 하나여야 합니다. <ul><li><code>apns</code>&mdash;Apple Push Notification Service. APNS에 대한 알림 허브를 구성하고 클라이언트 앱에서 알림을 받는 방법에 대한 자세한 내용은 [Azure Notification Hubs를 사용하여 iOS에 푸시 알림 보내기](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md)를 참조하세요.</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). ADM에 대한 Notification Hubs를 구성하고 Kindle 앱에서 알림을 받는 방법에 대한 자세한 내용은 [Kindle 앱에 대한 Notification Hubs 시작](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md)을 참조하세요.</li><li><code>wns</code>&mdash;Windows 플랫폼을 대상으로 하는 [Windows 푸시 알림 서비스](/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) Windows Phone 8.1 이상도 WNS에서 지원됩니다. 자세한 내용은 [Windows 유니버설 플랫폼 앱용 Notification Hubs 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)을 참조하세요.</li><li><code>mpns</code>&mdash;[Microsoft 푸시 알림 서비스](/previous-versions/windows/apps/ff402558(v=vs.105)). 이 플랫폼은 Windows Phone 8 및 이전 Windows Phone 플랫폼을 지원합니다. 자세한 내용은 [Windows Phone에서 Azure Notification Hubs를 사용하여 푸시 알림 보내기](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)를 참조하세요.</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>function.json 파일 예제

다음은 *function.json* 파일의 Notification Hubs 바인딩 예제입니다.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "apns"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>연결 문자열 설정

알림 허브 출력 바인딩을 사용하려면 허브에 대한 연결 문자열을 구성해야 합니다. 기존 알림 허브를 선택하거나 Azure Portal의 *통합* 탭에서 새 알림 허브를 만들 수 있습니다. 연결 문자열을 수동으로 구성할 수도 있습니다. 

연결 문자열을 기존 알림 허브로 구성하려면

1. [Azure Portal](https://portal.azure.com)에서 알림 허브로 이동한 후 **액세스 정책**을 선택하고 **DefaultFullSharedAccessSignature** 정책 옆에 있는 복사 단추를 선택합니다. 그러면 *DefaultFullSharedAccessSignature* 정책에 대한 연결 문자열이 알림 허브로 복사됩니다. 이 연결 문자열을 사용하면 함수로 허브에 알림 메시지를 보낼 수 있습니다.
    ![알림 허브 연결 문자열 복사](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Azure Portal에서 함수 앱으로 이동한 후 **애플리케이션 설정**을 선택하고 **MyHubConnectionString**과 같은 키를 추가한 다음, 알림 허브에 대해 복사한 *DefaultFullSharedAccessSignature*를 값으로 붙여넣고 **저장**을 클릭합니다.

이 애플리케이션 설정의 이름은 *function.json* 또는 .NET 특성의 출력 바인딩 연결 설정에 들어가는 내용입니다. 이 문서의 앞쪽에 있는 [구성 섹션](#configuration)을 참조하세요.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| 알림 허브 | [운영 가이드](https://docs.microsoft.com/rest/api/notificationhubs/) |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

