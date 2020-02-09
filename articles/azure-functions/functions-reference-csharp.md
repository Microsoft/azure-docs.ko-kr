---
title: Azure Functions C# 스크립트 개발자 참조
description: 스크립트를 사용 하 여 C# Azure Functions를 개발 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 13e16fef2ae66851909e03dddab293e9c7955acb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978784"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# 스크립트 (csx) 개발자 참조

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

이 문서에서는 스크립트 (*csx*)를 사용 C# 하 여 Azure Functions를 개발 하는 방법을 소개 합니다.

Azure Functions는 C# 및 C# 스크립트 프로그래밍 언어를 지원 합니다. [Visual Studio 클래스 라이브러리 프로젝트에서를 사용 C# 하는 방법](functions-develop-vs.md)에 대한 지침을 찾고 있는 경우 [ C# 개발자 참조](functions-dotnet-class-library.md)를 참조 하세요.

이 문서에서는 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 알고 있다고 가정 합니다.

## <a name="how-csx-works"></a>. Csx 작동 방법

Azure Functions C# 에 대한 스크립트 환경은 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction)를 기반으로 합니다. 데이터는 메서드 인수 C# 를 통해 함수로 흐릅니다. 인수 이름은 `function.json` 파일에 지정 되며 함수로 거 및 취소 토큰과 같은 항목에 액세스 하기 위한 미리 정의 된 이름이 있습니다.

*. Csx* 형식을 사용 하면 "상용구"를 적고 함수를 작성 하는 C# 데 집중할 수 있습니다. 네임 스페이스 및 클래스에 모든 항목을 래핑하는 대신 `Run` 메서드만 정의 하면 됩니다. 평소 처럼 파일의 시작 부분에 모든 어셈블리 참조와 네임 스페이스를 포함 합니다.

함수 앱의 *. csx* 파일은 인스턴스가 초기화 될 때 컴파일됩니다. 이 컴파일 단계는 클래스 라이브러리와 비교 하 여 C# C# 스크립트 함수에 대해 콜드 시작과 같은 작업을 더 오래 걸릴 수 있음을 의미 합니다. 이 컴파일 단계는 또한 Azure Portal C# 에서 스크립트 함수를 편집할 수 있는 반면 C# 클래스 라이브러리는 그렇지 않습니다.

## <a name="folder-structure"></a>폴더 구조

C# 스크립트 프로젝트의 폴더 구조는 다음과 같습니다.

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

함수 앱을 구성 하는 데 사용할 수 있는 공유 [호스트나 json](functions-host-json.md) 파일이 있습니다. 각 함수에는 고유한 코드 파일 (csx) 및 바인딩 구성 파일 (함수인 json)이 있습니다.

[버전 2.x 및 이후 버전](functions-versions.md) 의 함수 런타임에 필요한 바인딩 확장은 `bin` 폴더의 실제 라이브러리 파일을 사용 하 여 `extensions.csproj` 파일에 정의 됩니다. 로컬로 개발 하는 경우 [바인딩 확장을 등록](./functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발 하는 경우이 등록이 수행 됩니다.

## <a name="binding-to-arguments"></a>인수에 바인딩

입력 또는 출력 데이터는 *함수 json* 구성 C# 파일의 `name` 속성을 통해 스크립트 함수 매개 변수에 바인딩됩니다. 다음 예제에서는 큐 트리거 함수에 대한 *함수 json* 파일 및 *실행. csx* 파일을 보여 줍니다. 큐 메시지에서 데이터를 수신 하는 매개 변수는 `name` 속성의 값 이므로 `myQueueItem` 이름이 지정 됩니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r` 문은 [이 문서의 뒷부분에서](#referencing-external-assemblies)설명 합니다.

## <a name="supported-types-for-bindings"></a>바인딩에 대해 지원 되는 형식

각 바인딩에는 자체 지원 형식이 있습니다. 예를 들어 blob 트리거는 문자열 매개 변수, POCO 매개 변수, `CloudBlockBlob` 매개 변수 또는 지원 되는 다른 여러 형식에 사용할 수 있습니다. Blob [바인딩에 대한 바인딩 참조 문서](functions-bindings-storage-blob.md#trigger---usage) 는 blob 트리거에 대해 지원 되는 모든 매개 변수 유형을 나열 합니다. 자세한 내용은 [트리거 및](functions-triggers-bindings.md) 바인딩 및 [각 바인딩 형식에 대한 바인딩 참조 문서](functions-triggers-bindings.md#next-steps)를 참조 하세요.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>사용자 지정 클래스 참조

사용자 지정 일반 POCO (CLR Object) 클래스를 사용 해야 하는 경우 동일한 파일 내에 클래스 정의를 포함 하거나 별도의 파일에 배치할 수 있습니다.

다음 예제에서는 POCO 클래스 정의를 포함 하는 *실행. csx* 예제를 보여 줍니다.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

POCO 클래스에는 각 속성에 대해 정의 된 getter 및 setter가 있어야 합니다.

## <a name="reusing-csx-code"></a>Csx 코드 재사용

*Csx* 파일의 다른 *. csx* 파일에 정의 된 클래스와 메서드를 사용할 수 있습니다. 이렇게 하려면 *csx* 파일에 `#load` 지시문을 사용 합니다. 다음 예제에서는 `MyLogger` 이라는 로깅 루틴이 *Mylogger. csx* 에서 공유 되 고 `#load` 지시어를 사용 하 여 *csx* 로 로드 됩니다.

예제 *실행. csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

예: *mylogger. csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

POCO 개체를 사용 하 여 함수 간에 전달 되는 데이터를 강력한 형식으로 하려는 경우에는 공유 *csx* 파일을 사용 하는 것이 일반적인 패턴입니다. 다음 단순화 된 예제에서 HTTP 트리거 및 큐 트리거는 `Order` 라는 POCO 개체를 공유 하 여 주문 데이터를 강력한 형식으로 만듭니다.

HTTP 트리거에 대한 다음 예제를 *실행 합니다.*

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

큐 트리거에 대한 *csx* 예:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

예제 *주문. csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

`#load` 지시어를 사용 하 여 상대 경로를 사용할 수 있습니다.

* `#load "mylogger.csx"`는 함수 폴더에 있는 파일을 로드 합니다.
* `#load "loadedfiles\mylogger.csx"`는 함수 폴더의 폴더에 있는 파일을 로드 합니다.
* `#load "..\shared\mylogger.csx"`는 함수 폴더와 동일한 수준의 폴더에 있는 파일을 로드 합니다. 즉, *wwwroot*바로 아래에 있는 폴더에 있는 파일을 로드 합니다.

`#load` 지시문은 *.cs* 파일이 아닌 *. csx* 파일만 사용할 수 있습니다.

## <a name="binding-to-method-return-value"></a>메서드 반환 값에 바인딩

*Json*에 `$return` 이름을 사용 하 여 출력 바인딩에 메서드 반환 값을 사용할 수 있습니다. 예제는 [트리거 및 바인딩](./functions-bindings-return-value.md)을 참조 하세요.

성공적인 함수 실행으로 인해 항상 반환 값이 출력 바인딩에 전달 되는 경우에만 반환 값을 사용 합니다. 그렇지 않으면 다음 섹션에 나와 있는 것 처럼 `ICollector` 또는 `IAsyncCollector`를 사용 합니다.

## <a name="writing-multiple-output-values"></a>여러 출력 값 쓰기

출력 바인딩에 여러 값을 기록 하거나 함수를 성공적으로 호출 하는 경우 출력 바인딩에 전달할 항목이 없을 수 있는 경우 [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 형식을 사용 합니다. 이러한 형식은 메서드가 완료 될 때 출력 바인딩에 기록 되는 쓰기 전용 컬렉션입니다.

이 예제에서는 `ICollector`를 사용 하 여 여러 큐 메시지를 동일한 큐에 씁니다.

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Naplózás

에서 C#스트리밍 로그에 대한 출력을 기록 하려면 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)형식의 인수를 포함 합니다. 이름을 `log`하는 것이 좋습니다. Azure Functions에서 `Console.Write` 사용 하지 마십시오.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> `TraceWriter`대신 사용할 수 있는 최신 로깅 프레임 워크에 대한 자세한 내용은 **Monitor Azure Functions** 문서의 [함수에서 C# 로그 쓰기](functions-monitoring.md#write-logs-in-c-functions) 를 참조 하세요.

## <a name="async"></a>Async

[비동기](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)함수를 만들려면 `async` 키워드를 사용 하 고 `Task` 개체를 반환 합니다.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

비동기 함수에는 `out` 매개 변수를 사용할 수 없습니다. 출력 바인딩의 경우 [함수 반환 값](#binding-to-method-return-value) 또는 [수집기 개체](#writing-multiple-output-values) 를 대신 사용 합니다.

## <a name="cancellation-tokens"></a>취소 토큰

함수는 함수가 종료 될 때 운영 체제에서 코드를 알릴 수 있도록 하는 [CancellationToken](/dotnet/api/system.threading.cancellationtoken) 매개 변수를 사용할 수 있습니다. 이 알림을 사용 하 여 데이터가 일관 되지 않은 상태로 유지 되는 방식으로 함수가 예기치 않게 종료 되지 않도록 할 수 있습니다.

다음 예제에서는 임박한 함수 종료를 확인 하는 방법을 보여 줍니다.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>네임 스페이스 가져오기

네임 스페이스를 가져와야 하는 경우에는 `using` 절을 사용 하 여 일반적인 방법으로이 작업을 수행할 수 있습니다.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

다음 네임 스페이스는 자동으로 가져오므로 선택적입니다.

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>외부 어셈블리 참조

프레임 워크 어셈블리의 경우 `#r "AssemblyName"` 지시어를 사용 하 여 참조를 추가 합니다.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

다음 어셈블리는 Azure Functions 호스팅 환경에 의해 자동으로 추가 됩니다.

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

다음 어셈블리는 단순 이름 (예: `#r "AssemblyName"`)으로 참조할 수 있습니다.

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>사용자 지정 어셈블리 참조

사용자 지정 어셈블리를 참조 하려면 *공유* 어셈블리나 *전용* 어셈블리 중 하나를 사용 하면 됩니다.

* 공유 어셈블리는 함수 앱 내의 모든 함수에서 공유 됩니다. 사용자 지정 어셈블리를 참조 하려면 [함수 응용 프로그램 루트 폴더](functions-reference.md#folder-structure) (wwwroot)에서 `bin` 라는 폴더에 어셈블리를 업로드 합니다.

* 전용 어셈블리는 지정 된 함수의 컨텍스트에 포함 되 고 다른 버전의 테스트용 로드를 지원 합니다. 전용 어셈블리는 함수 디렉터리의 `bin` 폴더에 업로드 해야 합니다. `#r "MyAssembly.dll"`와 같이 파일 이름을 사용 하 여 어셈블리를 참조 합니다.

함수 폴더에 파일을 업로드 하는 방법에 대한 자세한 내용은 [패키지 관리](#using-nuget-packages)에 대한 섹션을 참조 하세요.

### <a name="watched-directories"></a>감시 디렉터리

함수 스크립트 파일을 포함 하는 디렉터리는 어셈블리에 대한 변경 내용을 자동으로 감시 합니다. 다른 디렉터리의 어셈블리 변경 내용을 확인 하려면이를 [호스트 json](functions-host-json.md)의 `watchDirectories` 목록에 추가 합니다.

## <a name="using-nuget-packages"></a>NuGet 패키지 사용
2\.x 이상 C# 함수에서 NuGet 패키지를 사용 하려면 함수 앱의 파일 시스템에 있는 함수의 폴더에 *함수 proj* 파일을 업로드 합니다. *ProjectOxford* 버전 *1.1.0*에 대한 참조를 추가 하는 예제 *함수인 proj* 파일은 다음과 같습니다.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

사용자 지정 NuGet 피드를 사용 하려면 함수 앱 루트의 *nuget.exe* 파일에 피드를 지정 합니다. 자세한 내용은 [NuGet 동작 구성](/nuget/consume-packages/configuring-nuget-behavior)을 참조 하세요.

> [!NOTE]
> 1\.x C# 함수에서 NuGet 패키지는 *함수 proj* 파일 대신 *프로젝트. json* 파일을 사용 하 여 참조 됩니다.

1\.x 함수의 경우 *프로젝트. json* 파일을 대신 사용 합니다. 다음은 *project. json* 파일의 예입니다.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>함수 proj 파일 사용

1. Azure Portal에서 함수를 엽니다. 로그 탭에는 패키지 설치 출력이 표시 됩니다.
2. *함수 proj* 파일을 업로드 하려면 Azure Functions 개발자 참조 항목에서 [함수 앱 파일을 업데이트](functions-reference.md#fileupdate) 하는 방법 항목에 설명 된 방법 중 하나를 사용 합니다.
3. *함수 proj* 파일을 업로드 한 후 함수의 스트리밍 로그에 다음 예제와 같은 출력이 표시 됩니다.

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Környezeti változók

환경 변수 또는 앱 설정 값을 가져오려면 다음 코드 예제와 같이 `System.Environment.GetEnvironmentVariable`를 사용 합니다.

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>런타임에 바인딩

C# 및 기타 .net 언어에서는 *함수 json*의 [*선언적*](https://en.wikipedia.org/wiki/Declarative_programming) 바인딩과는 반대로 [명령적](https://en.wikipedia.org/wiki/Imperative_programming) 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 디자인 타임이 아닌 런타임에 바인딩 매개 변수를 계산 해야 하는 경우에 유용 합니다. 이 패턴을 사용 하면 함수 코드에서 지원 되는 입력 및 출력 바인딩을 즉시 바인딩할 수 있습니다.

명령적 바인딩을 다음과 같이 정의 합니다.

- 원하는 명령적 바인딩에 대한 *함수 json* 에 항목을 포함 **하지 마세요** .
- 입력 매개 변수 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 또는 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)를 전달 합니다.
- 다음 C# 패턴을 사용 하 여 데이터 바인딩을 수행 합니다.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`는 바인딩을 정의 하는 .NET 특성이 며 `T`는 해당 바인딩 형식에서 지원 되는 입력 또는 출력 형식입니다. `T`는 `out` 매개 변수 형식 (예: `out JObject`)이 될 수 없습니다. 예를 들어 Mobile Apps 테이블 출력 바인딩은 [6 개의 출력 형식을](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)지원 하지만 `T`에 대해 [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 만 사용할 수 있습니다.

### <a name="single-attribute-example"></a>단일 특성 예제

다음 예제 코드는 런타임에 정의 된 blob 경로를 사용 하 여 [저장소 blob 출력 바인딩을](functions-bindings-storage-blob.md#output) 만든 다음 문자열을 blob에 씁니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[Blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) 는 [저장소 blob](functions-bindings-storage-blob.md) 입력 또는 출력 바인딩을 정의 하 고, [TextWriter](/dotnet/api/system.io.textwriter) 는 지원 되는 출력 바인딩 유형입니다.

### <a name="multiple-attribute-example"></a>여러 특성 예제

앞의 예제에서는 함수 앱의 기본 저장소 계정 연결 문자열 (`AzureWebJobsStorage`)에 대한 앱 설정을 가져옵니다. [Storageaccountattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 를 추가 하 고 특성 배열을 `BindAsync<T>()`에 전달 하 여 저장소 계정에 사용할 사용자 지정 앱 설정을 지정할 수 있습니다. `IBinder`아닌 `Binder` 매개 변수를 사용 합니다.  Példa:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

다음 표에서는 각 바인딩 형식에 대한 .NET 특성과 해당 특성이 정의 된 패키지를 나열 합니다.

> [!div class="mx-codeBreakAll"]
> | 바인딩 | Attribútum | Hivatkozás hozzáadása |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Azure Event Hubs-eseményközpontok | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Értesítési központ | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Szolgáltatásbusz | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Tárolási üzenetsor | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | 저장소 테이블 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [모범 사례에 대한 자세한 정보 Azure Functions](functions-best-practices.md)
