---
title: Azure Functions 준비 트리거
description: Azure Functions에서 워밍업 트리거를 사용 하는 방법을 이해 합니다.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 함수, 함수, 이벤트 처리, 준비, 콜드 시작, 프리미엄, 동적 계산, 서버를 사용 하지 않는 아키텍처
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167320"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions 준비 트리거

이 문서에서는 Azure Functions에서 준비 트리거를 사용 하는 방법을 설명 합니다. 워밍업 트리거는 [프리미엄 계획](functions-premium-plan.md)에서 실행 되는 함수 앱에 대해서만 지원 됩니다. 준비 트리거는 실행 중인 함수 앱의 크기를 조정 하기 위해 인스턴스를 추가할 때 호출 됩니다. 준비 트리거를 사용 하 여 [전처리 프로세스](./functions-premium-plan.md#pre-warmed-instances) 중에 사용자 지정 종속성을 미리 로드 하 여 함수가 즉시 요청 처리를 시작할 수 있도록 할 수 있습니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

[WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지, 버전 **3.0.5** 이상이 필요 합니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 리포지토리에 있습니다. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>트리거

워밍업 트리거를 사용 하면 실행 중인 앱에 추가 될 때 새 인스턴스에서 실행 되는 함수를 정의할 수 있습니다. 워밍업 함수를 사용 하 여 연결을 열거나, 종속성을 로드 하거나, 앱이 트래픽 수신을 시작 하기 전에 다른 사용자 지정 논리를 실행할 수 있습니다. 

워밍업 트리거는 앱의 다른 함수에서 사용 하는 공유 종속성을 만들기 위한 것입니다. [여기에서 공유 종속성의 예를 참조](./manage-connections.md#client-code-examples)하세요.

준비 트리거는 수평 확장 작업 중에만 호출 되 고 다시 시작 하는 경우 또는 다른 비 확장 작업 중에만 호출 됩니다. 사용자의 논리가 준비 트리거를 사용 하지 않고 필요한 모든 종속성을 로드할 수 있도록 해야 합니다. 지연 로드는이를 위해 좋은 패턴입니다.

## <a name="trigger---example"></a>트리거 - 예제

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제에서는 앱에 추가 될 때 각 새 인스턴스에서 실행 되는 [ C# 함수](functions-dotnet-class-library.md) 를 보여 줍니다. 반환 값 특성은 필요 하지 않습니다.


* 함수는 ```warmup``` (대/소문자 구분 안 함)로 명명 되어야 하 고 앱 당 하나의 준비 함수만 있을 수 있습니다.
* 워밍업을 .NET 클래스 라이브러리 함수로 사용 하려면 WebJobs에 대 한 패키지 참조가 있는지 확인 하세요. **> = 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


자리 표시자 주석은 응용 프로그램에서 공유 종속성을 선언 하 고 초기화 하는 위치를 보여 줍니다. 
[공유 종속성에 대 한 자세한 내용은 여기를 참조](./manage-connections.md#client-code-examples)하세요.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)


다음 예제에서는 *함수 json* 파일의 워밍업 트리거와 앱에 추가 될 때 각 새 인스턴스에서 실행 되는 [ C# 스크립트 함수](functions-reference-csharp.md) 를 보여 줍니다.

함수는 ```warmup``` (대/소문자 구분 안 함)로 명명 되어야 하며, 앱 당 하나의 준비 함수만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 `HttpRequest`에 바인딩하는 C# 스크립트 코드입니다.

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 *함수 json* 파일의 워밍업 트리거와 앱에 추가 될 때 각 새 인스턴스에서 실행 되는 [JavaScript 함수](functions-reference-node.md) 를 보여 줍니다.

함수는 ```warmup``` (대/소문자 구분 안 함)로 명명 되어야 하 고 앱 당 하나의 준비 함수만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

다음 예제에서는 *함수 json* 파일의 워밍업 트리거와 앱에 추가 될 때 각 새 인스턴스에서 실행 되는 [Python 함수](functions-reference-python.md) 를 보여 줍니다.

함수는 ```warmup``` (대/소문자 구분 안 함)로 명명 되어야 하 고 앱 당 하나의 준비 함수만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

다음 예제에서는 각 새 인스턴스가 앱에 추가 될 때 실행 되는 워밍업 트리거를 보여 줍니다.

함수는 `warmup` (대/소문자 구분 안 함)로 명명 되어야 하 고 앱 당 하나의 준비 함수만 있을 수 있습니다.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>트리거 - 특성

[ C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 함수를 구성 하는 데 `WarmupTrigger` 특성을 사용할 수 있습니다.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

이 예제에서는 [워밍업](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) 특성을 사용 하는 방법을 보여 줍니다.

함수는 ```Warmup``` 호출 되어야 하며, 앱 당 하나의 준비 함수만 사용할 수 있습니다.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

전체 예제는 [트리거 예](#trigger---example)를 참조 하세요.

# <a name="c-scripttabcsharp-script"></a>[C#스크립트도](#tab/csharp-script)

스크립트에서 C# 특성을 지원 하지 않습니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원 하지 않습니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python에서 특성을 지원 하지 않습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

준비 트리거는 Java에서 특성으로 지원 되지 않습니다.

---

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `WarmupTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
| **type** | 해당 없음| 필수 - `warmupTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음| 필수 - `in`으로 설정해야 합니다. |
| **name** | 해당 없음| 필수-함수 코드에 사용 되는 변수 이름입니다.|

## <a name="trigger---usage"></a>트리거 - 사용

호출 된 준비 트리거 함수에 대 한 추가 정보가 제공 되지 않습니다.

## <a name="trigger---limits"></a>트리거 - 제한

* 워밍업 트리거는 [프리미엄 계획](./functions-premium-plan.md)에서 실행 되는 앱 에서만 사용할 수 있습니다.
* 준비 트리거는 다시 시작 또는 다른 비 확장 작업 중에만 확장 작업 중에 호출 됩니다. 사용자의 논리가 준비 트리거를 사용 하지 않고 필요한 모든 종속성을 로드할 수 있도록 해야 합니다. 지연 로드는이를 위해 좋은 패턴입니다.
* 인스턴스가 이미 실행 중인 경우에는 워밍업 트리거를 호출할 수 없습니다.
* 함수 앱 당 하나의 준비 트리거 함수만 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
