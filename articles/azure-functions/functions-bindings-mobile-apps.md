---
title: Azure Functions의 Mobile Apps 바인딩
description: Azure Functions에서 Azure Mobile Apps 바인딩을 사용하는 방법을 파악합니다.
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
ms.openlocfilehash: 5fd220f15f363c1987f1576009519e4b2feae6b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438128"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Azure Functions의 Mobile Apps 바인딩 

> [!NOTE]
> Azure Mobile Apps 바인딩은 Azure Functions 1.x에만 사용할 수 있습니다. 이는 Azure Functions 2.x에서 지원되지 않습니다.

이 문서에서는 Azure Functions에서 [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 바인딩을 사용하여 작업하는 방법을 설명합니다. Azure Functions는 Mobile Apps에 대한 입력 및 출력 바인딩을 지원합니다.

Mobile Apps 바인딩을 사용하면 모바일 앱에서 데이터 테이블을 읽고 업데이트할 수 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Mobile Apps는 [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet 패키지 버전 1.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>입력

Mobile Apps 입력 바인딩은 모바일 테이블 엔드포인트에서 레코드를 로드하여 함수에 전달합니다. C# 및 F# 함수에서 함수가 성공적으로 종료되면 레코드에 변경한 내용을 자동으로 다시 테이블에 전송합니다.

## <a name="input---example"></a>입력 - 예제

언어 관련 예제를 참조하세요.

* [C# 스크립트(.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>입력 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 Mobile Apps 입력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 레코드 식별자가 있는 큐 메시지에 의해 트리거됩니다. 함수는 지정된 레코드를 읽고 해당 `Text` 속성을 수정합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>입력 - JavaScript

다음 예에서는 *function.json* 파일의 Mobile Apps 입력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 레코드 식별자가 있는 큐 메시지에 의해 트리거됩니다. 함수는 지정된 레코드를 읽고 해당 `Text` 속성을 수정합니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
[구성](#input---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>입력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 특성을 사용합니다.

구성할 수 있는 특성 속성에 대한 자세한 내용은 [다음 구성 섹션](#input---configuration)을 참조하세요.

## <a name="input---configuration"></a>입력 - 구성

다음 표에서는 *function.json* 파일 및 `MobileTable` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
| **type**|| "mobileTable"로 설정해야 합니다.|
| **direction**||"in"으로 설정해야 합니다.|
| **name**|| 함수 시그니처의 입력 매개 변수 이름입니다.|
|**tableName** |**TableName**|모바일 앱 데이터 테이블의 이름입니다.|
| **id**| **Id** | 검색할 레코드의 식별자입니다. 정적이거나 함수를 호출하는 트리거를 기반으로 할 수 있습니다. 예를 들어, 함수에 대해 큐 트리거를 사용하는 경우 `"id": "{queueTrigger}"`는 검색할 레코드 ID로 큐 메시지의 문자열 값을 사용합니다.|
|**연결**|**연결**|모바일 앱의 URL이 있는 앱 설정의 이름입니다. 함수는 이 URL을 사용하여 모바일 앱에 대해 필요한 나머지 작업을 구성합니다. 모바일 앱의 URL이 포함된 함수 앱에 앱 설정을 만든 다음 입력 바인딩의 `connection` 속성에 앱 설정의 이름을 지정합니다. URL은 다음과 같습니다. `http://<appname>.azurewebsites.net`
|**apiKey**|**ApiKey**|모바일 앱의 API 키가 있는 앱 설정의 이름입니다. [Node.js 모바일 앱에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)하거나 [.NET 모바일 앱에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)하는 경우 API 키를 제공합니다. 키를 제공하려면 API 키가 포함된 함수 앱의 앱 설정을 만단 다음 `apiKey` 속성을 앱 설정의 이름과 함께 입력 바인딩에 추가합니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API 키를 모바일 앱 클라이언트와 공유하지 마십시오. Azure Functions처럼 서비스 측 클라이언트에게만 안전하게 배포되어야 합니다. Azure Functions는 사용자의 소스 제어 리포지토리를 확인하지 않도록 사용자 연결 정보 및 API 키를 앱 설정으로 저장합니다. 이는 사용자의 중요한 정보를 보호합니다.

## <a name="input---usage"></a>입력 - 사용

C# 함수에서 지정된 ID가 있는 레코드를 찾으면 명명된 [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) 매개 변수로 전달됩니다. 레코드를 찾을 수 없는 경우 매개 변수 값은 `null`입니다. 

JavaScript 함수에서는 레코드가 `context.bindings.<name>` 개체로 전달됩니다. 레코드를 찾을 수 없는 경우 매개 변수 값은 `null`입니다. 

C# 및 F# 함수에서 함수가 성공적으로 종료되면 입력 레코드에 변경한 내용(입력 매개 변수)을 자동으로 다시 테이블에 전송합니다. JavaScript 함수에서는 레코드를 수정할 수 없습니다.

## <a name="output"></a>출력

Mobile Apps 출력 바인딩을 사용하여 Mobile Apps 테이블에 새 레코드를 작성합니다.  

## <a name="output---example"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [C#](#output---c-example)
* [C# 스크립트(.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>출력 - C# 예제

다음 예에서는 큐 메시지에 의해 트리거된 [C# 함수](functions-dotnet-class-library.md)를 보여주고 모바일 앱 테이블에 레코드를 만듭니다.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>출력 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 Mobile Apps 출력 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 큐 메시지에 의해 트리거되고 `Text` 속성에 대해 하드 코딩된 값이 있는 새 레코드를 만듭니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>출력 - JavaScript 예제

다음 예에서는 *function.json* 파일의 Mobile Apps 출력 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 큐 메시지에 의해 트리거되고 `Text` 속성에 대해 하드 코딩된 값이 있는 새 레코드를 만듭니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) 특성을 사용합니다.

구성할 수 있는 특성 속성에 대한 자세한 내용은 [출력 - 구성](#output---configuration)을 참조하세요. 다음은 메서드 서명의 `MobileTable` 특성 예제입니다.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#output---c-example)를 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `MobileTable` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
| **type**|| "mobileTable"로 설정해야 합니다.|
| **direction**||"out"으로 설정해야 합니다.|
| **name**|| 함수 시그니처의 출력 매개 변수 이름입니다.|
|**tableName** |**TableName**|모바일 앱 데이터 테이블의 이름입니다.|
|**연결**|**MobileAppUriSetting**|모바일 앱의 URL이 있는 앱 설정의 이름입니다. 함수는 이 URL을 사용하여 모바일 앱에 대해 필요한 나머지 작업을 구성합니다. 모바일 앱의 URL이 포함된 함수 앱에 앱 설정을 만든 다음 입력 바인딩의 `connection` 속성에 앱 설정의 이름을 지정합니다. URL은 다음과 같습니다. `http://<appname>.azurewebsites.net`
|**apiKey**|**ApiKeySetting**|모바일 앱의 API 키가 있는 앱 설정의 이름입니다. [Node.js 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)하거나 [.NET 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)하는 경우 API 키를 제공합니다. 키를 제공하려면 API 키가 포함된 함수 앱의 앱 설정을 만단 다음 `apiKey` 속성을 앱 설정의 이름과 함께 입력 바인딩에 추가합니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> API 키를 모바일 앱 클라이언트와 공유하지 마십시오. Azure Functions처럼 서비스 측 클라이언트에게만 안전하게 배포되어야 합니다. Azure Functions는 사용자의 소스 제어 리포지토리를 확인하지 않도록 사용자 연결 정보 및 API 키를 앱 설정으로 저장합니다. 이는 사용자의 중요한 정보를 보호합니다.

## <a name="output---usage"></a>출력 - 사용

C# 스크립트 함수에서 `out object` 형식의 명명된 출력 매개 변수를 사용하여 출력 레코드에 액세스합니다. C# 클래스 라이브러리에서 `MobileTable` 특성은 다음 유형 중 하나와 함께 사용할 수 있습니다.

* `T`가 `JObject` 또는 `public string Id` 속성이 있는 type인 경우, `ICollector<T>` 또는 `IAsyncCollector<T>`.
* `out JObject`
* `T`가 `public string Id` 속성이 있는 type인 경우, `out T` 또는 `out T[]`.

Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 레코드에 액세스합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
