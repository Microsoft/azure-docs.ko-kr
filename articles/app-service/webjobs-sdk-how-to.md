---
title: WebJobs SDK 사용 방법
description: WebJobs SDK에 대한 코드 작성 방법을 알아봅니다. Azure 및 타사 서비스의 데이터에 액세스하는 이벤트 중심 백그라운드 처리 작업을 만듭니다.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a3bff9854e8e316bf368b2222d2244ab9ee6346
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962012"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 사용 방법

이 문서에서는 Azure WebJobs SDK 사용 방법을 안내합니다. WebJobs를 즉시 시작하려면 [이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 시작](webjobs-sdk-get-started.md)을 참조하세요. 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 버전

다음은 WebJobs SDK 3.*x* 버전과 2.*x* 버전의 주요 차이점입니다.

* 3\.*x* 버전은 .NET Core에 대한 지원을 추가합니다.
* 3\.*x* 버전에서는 WebJobs SDK에 필요한 Storage 바인딩 확장을 명시적으로 설치해야 합니다. 2\.*x* 버전에서는 Storage 바인딩이 SDK 패키지에 포함되어 있습니다.
* .NET Core(3.*x*) 프로젝트용 Visual Studio 도구는 .NET Framework(2.*x*) 프로젝트용 도구와 다릅니다. 자세한 내용은 [Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure App Service](webjobs-dotnet-deploy-vs.md)를 참조하세요.

가능하다면 3.*x* 및 2.*x* 버전의 예제가 제공됩니다.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md)는 WebJobs SDK를 기반으로 하며, 이 문서에서는 일부 토픽에 대한 Azure Functions 설명서의 링크를 제공합니다. Functions와 WebJobs SDK 간에는 다음과 같은 차이점이 있습니다.
> * Azure Functions 2.*x* 버전은 WebJobs SDK 3.*x* 버전에 해당하고, Azure Functions 1.*x* 버전은 WebJobs SDK 2.*x* 버전에 해당합니다. 소스 코드 리포지토리는 WebJobs SDK 번호 매기기를 사용합니다.
> * Azure Functions C# 클래스 라이브러리에 대한 샘플 코드는 WebJobs SDK 프로젝트에 `FunctionName` 특성이 필요 없다는 점을 제외하고 WebJobs SDK 코드와 비슷합니다.
> * HTTP(웹후크), Event Grid(HTTP 기반) 등의 일부 바인딩 형식은 Functions에서만 지원됩니다.
>
> 자세한 내용은 [WebJobs SDK과 Azure Functions 비교](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요.

## <a name="webjobs-host"></a>WebJobs 호스트

호스트는 함수의 런타임 컨테이너입니다.  트리거를 수신 대기하고 함수를 호출합니다. 3\.*x* 버전에서 호스트는 `IHost`의 구현입니다. 2\.*x* 버전에서는 `JobHost` 개체를 사용합니다. 사용자는 코드에서 호스트 인스턴스를 만들고 동작을 사용자 지정하는 코드를 작성하게 됩니다.

이것이 WebJobs SDK를 직접 사용할 때와 Azure Functions를 통해 간접적으로 사용할 때의 주요 차이점입니다. Azure Functions에서는 서비스가 호스트를 제어하므로 사용자가 코드를 작성하여 호스트를 사용자 지정할 수 없습니다. Azure Functions에서는 host.json 파일의 설정을 통해 호스트 동작을 사용자 지정할 수 있습니다. 이러한 설정은 코드가 아닌 문자열이며, 따라서 수행 가능한 사용자 지정의 종류가 제한됩니다.

### <a name="host-connection-strings"></a>호스트 연결 문자열

WebJobs SDK는 로컬에서 실행할 경우 local.settings.json 파일에서 Azure Storage 및 Azure Service Bus 연결 문자열을 찾고, Azure에서 실행할 경우 WebJob 환경에서 찾습니다. 기본적으로 스토리지 연결 문자열 설정 `AzureWebJobsStorage`는 필수입니다.  

SDK 2.*x* 버전을 사용하면 이러한 연결 문자열에 대해 고유한 이름을 사용하거나 다른 위치에 저장할 수 있습니다. 다음과 같이 코드에서 [`JobHostConfiguration`]을 사용하여 이름을 설정할 수 있습니다.

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

3\.*x* 버전은 기본 .NET Core 구성 API를 사용하므로 연결 문자열 이름을 변경하는 API가 없습니다.

### <a name="host-development-settings"></a>호스트 개발 설정

로컬 개발을 보다 효율적으로 수행하기 위해 개발 모드에서 호스트를 실행할 수 있습니다. 다음은 개발 모드에서 실행할 때 변경되는 설정입니다.

| 속성 | 개발 설정 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | 로그 출력을 최대화하는 `TraceLevel.Verbose`. |
| `Queues.MaxPollingInterval`  | 값이 작으면 큐 메서드가 즉시 트리거됩니다.  |
| `Singleton.ListenerLockPeriod` | 신속한 반복 개발에 도움이 되는 시간은 15초입니다. |

개발 모드를 사용하도록 설정하는 프로세스는 SDK 버전에 따라 다릅니다. 

#### <a name="version-3x"></a>3\.*x* 버전

3\.*x* 버전에서는 표준 ASP.NET Core API를 사용합니다. [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스에서 [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) 메서드를 호출합니다. 다음 예제와 같이 `development`라는 문자열을 전달합니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

`JobHostConfiguration` 클래스에는 개발 모드를 사용하도록 설정하는 `UseDevelopmentSettings` 메서드가 있습니다.  다음 예제에서는 개발 설정을 사용하는 방법을 보여줍니다. 로컬로 실행할 때 `config.IsDevelopment`에서 `true`를 반환하게 하려면 `Development` 값을 사용하여 `AzureWebJobsEnv`라는 로컬 환경 변수를 지정합니다.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>동시 연결 관리(2.*x* 버전)

3\.*x* 버전에서 연결 제한은 기본적으로 무한 연결입니다. 어떤 이유로 이 제한을 변경해야 하는 경우 [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) 클래스의 [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) 속성을 사용하면 됩니다.

2\.*x* 버전에서는 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API를 사용하여 호스트에 대한 동시 연결 수를 제어합니다. 2\.*x* 버전에서는 WebJobs 호스트를 시작하기 전에 이 값을 기본값인 2보다 높게 설정하는 것이 좋습니다.

사용자가 `ServicePointManager`를 통해 `HttpClient` 흐름을 사용하여 함수에서 만드는 모든 발신 HTTP 요청. `DefaultConnectionLimit`에 설정된 값에 도달하면 `ServicePointManager`는 요청을 보내기 전에 큐에 넣기 시작합니다. `DefaultConnectionLimit`가 2로 설정되었고 코드에서 HTTP 요청 1,000개를 만든다고 가정해 봅시다. 처음에는 OS까지 전달되는 요청이 2개밖에 없습니다. 나머지 998개는 공간이 생길 때까지 큐에서 대기합니다. 즉, `HttpClient`는 요청을 만들었지만 OS가 요청을 대상 서버로 보낸 적이 없는 것처럼 보여서 시간이 초과될 수 있습니다. 이와 같은 이유로 로컬 `HttpClient`가 요청을 완료하는 데 10초가 걸리지만 서비스가 200ms 후에 모든 요청을 반환하는 이상한 동작이 관찰될 수 있습니다. 

ASP.NET 애플리케이션의 기본값은 `Int32.MaxValue`이고, Basic 이상 App Service 계획에서 실행되는 WebJobs에서 잘 작동할 것입니다. WebJobs는 일반적으로 Always On 설정이 필요한데, 이 설정은 Basic 이상 App Service 계획에서만 지원됩니다.

WebJob이 무료 또는 공유 App Service 계획에서 실행되는 경우 애플리케이션이 App Service 샌드박스의 제한을 받으며, 현재 [연결 제한은 300개](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)입니다. `ServicePointManager`의 바인딩되지 않은 연결 제한 때문에 샌드박스 연결 임계값에 도달하여 사이트가 종료될 가능성이 있습니다. 이 경우 `DefaultConnectionLimit`를 50 또는 100처럼 약간 낮추면 이와 같은 상황을 방지하면서도 충분한 처리량을 계속 제공할 수 있습니다.

이 설정은 HTTP 요청을 만들기 전에 구성해야 합니다. 이러한 이유로 WebJobs 호스트가 자동으로 설정을 조정하면 안 됩니다. 호스트가 시작되기 전에 HTTP 요청이 발생할 수 있으며, 이로 인해 예기치 않은 동작이 발생할 수 있습니다. 가장 좋은 방법은 다음과 같이 `JobHost`를 초기화하기 전에 `Main` 메서드에서 즉시 값을 설정하는 것입니다.

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>트리거

함수는 퍼블릭 메서드여야 하고 트리거 특성 또는 [`NoAutomaticTrigger`](#manual-triggers) 특성이 하나만 있어야 합니다.

### <a name="automatic-triggers"></a>자동 트리거

자동 트리거는 이벤트에 대한 응답으로 함수를 호출합니다. 다음은 Azure Queue storage에 추가된 메시지에 의해 트리거되는 함수의 예입니다. 이 함수는 다음과 같이 Azure Blob Storage에서 BLOB을 읽어 응답합니다.

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`myqueue-items` 특성은 큐 메시지가 `QueueTrigger` 큐에 나타날 때마다 함수를 호출하도록 런타임에 지시합니다. `Blob` 특성은 큐 메시지를 사용하여 *sample-workitems* 컨테이너의 BLOB을 읽도록 런타임에 지시합니다. `samples-workitems` 컨테이너에 있는 BLOB 항목의 이름은 큐 트리거에서 바인딩 식(`{queueTrigger}`)으로 직접 가져옵니다.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>수동 트리거

함수를 수동으로 트리거하려면 다음과 같이 `NoAutomaticTrigger` 특성을 사용합니다.

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

수동으로 함수를 트리거하는 프로세스는 SDK 버전에 따라 달라집니다.

#### <a name="version-3x"></a>3\.*x* 버전

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>입력 및 출력 바인딩

입력 바인딩은 Azure 또는 타사 서비스의 데이터를 코드에 사용할 수 있는 선언적 방법을 제공합니다. 출력 바인딩은 데이터를 업데이트하는 방법을 제공합니다. [시작 문서](webjobs-sdk-get-started.md)는 각각의 예제를 보여줍니다.

메서드 반환 값에 특성을 적용하여 출력 바인딩에 대한 메서드 반환 값을 사용할 수 있습니다. [Azure 함수 반환 값 사용](../azure-functions/functions-bindings-return-value.md)의 예제를 참조하세요.

## <a name="binding-types"></a>바인딩 형식

바인딩 형식을 설치하고 관리하는 프로세스는 사용하는 SDK 버전이 3.*x*인지 아니면 2.*x*인지에 따라 달라집니다. 특정 바인딩 형식에 대해 설치할 패키지는 해당 형식의 Azure Functions에 대한 [참조](#binding-reference-information) 문서의 "패키지" 섹션에서 찾을 수 있습니다. Azure Functions에서 지원하지 않는 파일 트리거 및 바인딩은 예외입니다(로컬 파일 시스템의 경우).

#### <a name="version-3x"></a>3\.*x* 버전

3\.*x* 버전에서는 스토리지 바인딩이 `Microsoft.Azure.WebJobs.Extensions.Storage` 패키지에 포함되어 있습니다. 다음과 같이 `ConfigureWebJobs` 메서드에서 `AddAzureStorage` 확장 메서드를 호출합니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

다른 트리거 및 바인딩 형식을 사용하려면 해당 항목을 포함하는 NuGet 패키지를 설치하고 확장에서 구현된 `Add<binding>` 확장 메서드를 호출합니다. 예를 들어 Azure Cosmos DB 바인딩을 사용하려면 다음과 같이 `Microsoft.Azure.WebJobs.Extensions.CosmosDB`를 설치하고 `AddCosmosDB`를 호출합니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

핵심 서비스의 일부인 타이머 트리거 또는 파일 바인딩을 사용하려면 `AddTimers` 또는 `AddFiles` 확장 메서드를 각각 호출합니다.

#### <a name="version-2x"></a>2\.*x* 버전

다음 트리거 및 바인딩 형식은 `Microsoft.Azure.WebJobs` 패키지 2.*x* 버전에 포함되어 있습니다.

* Blob Storage
* Queue Storage
* Table Storage

다른 트리거 및 바인딩 형식을 사용하려면 사용하려는 트리거 및 바인딩 형식이 포함된 NuGet 패키지를 설치하고 `JobHostConfiguration` 개체에서 `Use<binding>` 메서드를 호출합니다. 예를 들어 타이머 트리거를 사용하려면 다음과 같이 `Microsoft.Azure.WebJobs.Extensions`를 설치하고 `Main` 메서드에서 `UseTimers`를 호출합니다.

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

파일 바인딩을 사용하려면 `Microsoft.Azure.WebJobs.Extensions`를 설치하고 `UseFiles`를 호출합니다.

### <a name="executioncontext"></a>ExecutionContext

WebJobs를 사용하여 [`ExecutionContext`]에 바인딩할 수 있습니다. 이 바인딩을 사용하여 함수 시그니처의 매개 변수로 [`ExecutionContext`]에 액세스할 수 있습니다. 예를 들어 다음 코드에서는 컨텍스트 개체를 사용하여 지정된 함수 호출에 의해 생성된 모든 로그의 상관 관계를 지정하는 데 사용할 수 있는 호출 ID에 액세스합니다.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

[`ExecutionContext`]에 바인딩하는 프로세스는 SDK 버전에 따라 달라집니다.

#### <a name="version-3x"></a>3\.*x* 버전

다음과 같이 `ConfigureWebJobs` 메서드에서 `AddExecutionContextBinding` 확장 메서드를 호출합니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

앞에서 언급한 `Microsoft.Azure.WebJobs.Extensions` 패키지는 `UseCore` 메서드를 호출하여 등록할 수 있는 특별한 바인딩 형식을 제공합니다. 이 바인딩을 사용하면 함수 시그니처에서 [`ExecutionContext`] 매개 변수를 정의할 수 있습니다. 이 매개 변수는 다음과 같이 사용하도록 설정합니다.

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>바인딩 구성

일부 트리거와 바인딩의 동작을 구성할 수 있습니다. 동작을 구성하는 프로세스는 SDK 버전에 따라 다릅니다.

* **버전 3.*x*:** `Add<Binding>` 메서드가 `ConfigureWebJobs`에서 호출될 때 구성을 설정합니다.
* **버전 2.*x*:** `JobHost`에 전달하는 구성 개체의 속성을 설정하여 구성을 설정합니다.

이러한 바인딩 관련 설정은 Azure Functions의 [host.json 프로젝트 파일](../azure-functions/functions-host-json.md) 설정과 동일합니다.

다음 바인딩을 구성할 수 있습니다.

* [Azure CosmosDB 트리거](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs 트리거](#event-hubs-trigger-configuration-version-3x)
* [Queue storage 트리거](#queue-storage-trigger-configuration)
* [SendGrid 바인딩](#sendgrid-binding-configuration-version-3x)
* [Service Bus 트리거](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 트리거 구성(버전 3.*x*)

다음 예제에서는 Azure Cosmos DB 트리거를 구성하는 방법을 보여줍니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

자세한 내용은 [Azure CosmosDB 바인딩](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) 문서를 참조하세요.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs 트리거 구성(버전 3.*x*)

다음 예제에서는 Event Hubs 트리거를 구성하는 방법을 보여줍니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

자세한 내용은 [Event Hubs 바인딩](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) 문서를 참조하세요.

### <a name="queue-storage-trigger-configuration"></a>Queue storage 트리거 구성

다음 예제에서는 Queue storage 트리거를 구성하는 방법을 보여줍니다.

#### <a name="version-3x"></a>3\.*x* 버전

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

자세한 내용은 [Queue storage 바인딩](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) 문서를 참조하세요.

#### <a name="version-2x"></a>2\.*x* 버전

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

자세한 내용은 [host.json v1.x 참조](../azure-functions/functions-host-json-v1.md#queues)를 확인하세요.

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 바인딩 구성(버전 3.*x*)

다음 예제에서는 SendGrid 출력 바인딩을 구성하는 방법을 보여줍니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

자세한 내용은 [SendGrid 바인딩](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) 문서를 참조하세요.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus 트리거 구성(버전 3.*x*)

다음 예제에서는 Service Bus 트리거를 구성하는 방법을 보여줍니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

자세한 내용은 [Service Bus 바인딩](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) 문서를 참조하세요.

### <a name="configuration-for-other-bindings"></a>다른 바인딩의 구성

일부 트리거 및 바인딩 형식은 자체적인 사용자 지정 구성 형식을 정의합니다. 예를 들어 파일 트리거는 다음 예제와 같이 모니터링할 루트 경로를 지정할 수 있습니다.

#### <a name="version-3x"></a>3\.*x* 버전

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>바인딩 식

특성 생성자 매개 변수에서는 다양한 소스의 값을 확인하는 식을 사용할 수 있습니다. 예를 들어 다음 코드에서 `BlobTrigger` 특성의 경로는 `filename`이라는 식을 만듭니다. 출력 바인딩에 사용될 경우 `filename`은 트리거 BLOB의 이름으로 확인됩니다.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

바인딩 식에 대한 자세한 내용은 Azure Functions 설명서의 [바인딩 식 및 패턴](../azure-functions/functions-bindings-expressions-patterns.md)을 참조하세요.

### <a name="custom-binding-expressions"></a>사용자 지정 바인딩 식

경우에 따라 큐 이름, BLOB 이름 또는 컨테이너, 테이블 이름을 하드 코딩하지 않고 코드에서 지정할 수 있습니다. 예를 들어 구성 파일 또는 환경 변수에서 `QueueTrigger` 특성에 대한 큐 이름을 지정할 수 있습니다.

이렇게 하려면 `NameResolver` 개체를 `JobHostConfiguration` 개체에 전달하면 됩니다. 트리거 또는 바인딩 특성 생성자 매개 변수에 자리 표시자를 포함하면 `NameResolver` 코드가 해당 자리 표시자 위치에 사용할 실제 값을 제공합니다. 다음과 같이 자리 표시자를 백분율(%) 기호로 묶어서 식별할 수 있습니다.

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

이 코드를 사용하면 테스트 환경에 `logqueuetest`라는 큐를 사용하고 프로덕션 환경에 `logqueueprod`라는 큐를 사용할 수 있습니다. 하드 코드된 큐 이름 대신 `appSettings` 컬렉션의 항목 이름을 지정합니다.

사용자 지정 이름을 제공하지 않을 때 적용되는 기본 `NameResolver`가 있습니다. 기본값은 앱 설정이나 환경 변수에서 가져옵니다.

`NameResolver` 클래스는 다음과 같이 `appSettings`에서 큐 이름을 가져옵니다.

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>3\.*x* 버전

종속성 주입을 사용하여 확인자를 구성합니다. 이러한 샘플에는 다음 `using` 문이 필요합니다.

```cs
using Microsoft.Extensions.DependencyInjection;
```

다음 예제와 같이 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)에서 [`ConfigureServices`] 확장 메서드를 호출하여 확인자를 추가합니다.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

다음과 같이 `NameResolver` 클래스를 `JobHost` 개체에 전달합니다.

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions가 예제와 같이 앱 설정에서 값을 가져오도록 `INameResolver`를 구현합니다. 사용자는 WebJobs SDK를 직접 사용하는 경우 원하는 소스에서 자리 표시자 대체 값을 가져오는 사용자 지정 구현을 작성할 수 있습니다.

## <a name="binding-at-runtime"></a>런타임에 바인딩

`Queue`, `Blob` 또는 `Table` 같은 바인딩 특성을 사용하려면 함수에서 약간의 작업을 수행해야 하는 경우 `IBinder` 인터페이스를 사용하면 됩니다.

다음 예제에서는 입력 큐 메시지를 사용하여 동일한 내용의 새 메시지를 출력 큐에 만듭니다. 출력 큐 이름은 함수 본문에서 코드로 설정됩니다.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

자세한 내용은 Azure Functions 설명서의 [런타임에 바인딩](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)을 참조하세요.

## <a name="binding-reference-information"></a>바인딩 참조 정보

Azure Functions 설명서에는 각 바인딩 형식에 대한 참조 정보가 담겨 있습니다. 각 바인딩 참조 문서에서 다음 정보를 확인할 수 있습니다. (이 예제는 Storage 큐를 기반으로 합니다.)

* [패키지](../azure-functions/functions-bindings-storage-queue.md). WebJobs SDK 프로젝트에서 바인딩을 지원하려면 반드시 포함해야 하는 패키지입니다.
* [예제](../azure-functions/functions-bindings-storage-queue-trigger.md). 코드 샘플입니다. C# 클래스 라이브러리 예제는 WebJobs SDK에 적용됩니다. `FunctionName` 특성만 생략하면 됩니다.
* [특성](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). 바인딩 형식에 사용할 특성입니다.
* [구성](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). 특성 속성 및 생성자 매개 변수에 대한 설명입니다.
* [사용 현황](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). 바인딩할 수 있는 형식 및 바인딩의 작동 원리에 대한 정보입니다. 예: 폴링 알고리즘, 포이즌 큐 처리.
  
바인딩 참조 문서 목록은 Azure Functions에 대한 [트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md#supported-bindings) 문서의 "지원되는 바인딩"을 참조하세요. 이 목록의 HTTP, Webhook 및 Event Grid 바인딩은 WebJobs SDK가 아닌 Azure Functions에서만 지원됩니다.

## <a name="disable-attribute"></a>Disable 특성 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 특성을 사용하여 함수를 트리거할 수 있는지 여부를 제어할 수 있습니다. 

다음 예제에서 앱 설정 `Disable_TestJob`의 값이 `1` 또는 `True`이면(대/소문자 구분 안 함) 함수가 실행되지 않습니다. 이 경우 런타임에서 *Function 'Functions.TestJob' is disabled* 로그 메시지를 작성합니다.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure Portal에서 앱 설정 값을 변경하면 WebJob이 다시 시작되고, 설정을 새로 선택해야 합니다.

매개 변수, 메서드 또는 클래스 수준에서 특성을 선언할 수 있습니다. 설정 이름에 바인딩 식을 포함할 수도 있습니다.

## <a name="timeout-attribute"></a>Timeout 특성

[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 특성은 지정된 시간 내에 함수가 완료되지 않으면 함수를 취소합니다. 다음 예제의 함수는 Timeout 특성이 없으면 하루 동안 실행됩니다. Timeout 특성이 있으면 함수가 15초 후에 취소됩니다.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

클래스 또는 메서드 수준에서 Timeout 특성을 적용할 수 있으며, `JobHostConfiguration.FunctionTimeout`을 사용하여 글로벌 시간 제한을 지정할 수 있습니다. 클래스 또는 메서드 수준 시간 제한은 글로벌 시간 제한을 재정의합니다.

## <a name="singleton-attribute"></a>Singleton 특성

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 특성은 호스트 웹앱 인스턴스가 여러 개 있어도 함수의 인스턴스 중 하나만 실행되게 합니다. 이 특성은 이렇게 하기 위해 [분산 잠금](#viewing-lease-blobs)을 사용합니다.

다음 예제에서는 `ProcessImage` 함수의 단일 인스턴스만 지정된 시간에 실행됩니다.

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

일부 트리거는 동시성 관리를 기본적으로 지원합니다.

* **QueueTrigger**. `JobHostConfiguration.Queues.BatchSize`를 `1`로 설정합니다.
* **ServiceBusTrigger**. `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls`를 `1`로 설정합니다.
* **FileTrigger**. `FileProcessor.MaxDegreeOfParallelism`를 `1`로 설정합니다.

이러한 설정을 사용하여 함수가 단일 인스턴스에서 싱글톤으로 실행되도록 할 수 있습니다. 웹앱이 여러 인스턴스로 규모 확장될 때 함수의 단일 인스턴스만 실행되게 하려면 함수에서 수신기 수준 싱글톤 잠금을 적용합니다(`[Singleton(Mode = SingletonMode.Listener)]`). 수신기 잠금은 JobHost가 시작될 때 획득합니다. 확장된 인스턴스 3개가 동시에 시작되면 인스턴스 중 하나만 잠금을 획득하고 하나의 수신기만 시작됩니다.

> [!NOTE]
> SingletonMode 작동 방식에 대해 자세히 알아보려면이 [GitHub 리포지토리](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs) 를 참조 하세요.

### <a name="scope-values"></a>범위 값

싱글톤에 대한 *범위 식/값*을 지정할 수 있습니다. 식/값을 지정하면 특정 범위에서 함수의 모든 실행이 직렬화됩니다. 요구 사항에 따라 다른 호출을 직렬화하는 동안 사용자 함수에 대한 병렬 처리의 일정 수준에서 이러한 방식으로 더 세분화된 잠금을 구현할 수 있습니다. 예를 들어 다음 코드의 범위 식은 수신 메시지의 `Region` 값에 바인딩합니다. 큐의 East, East, West 지역에 각각 메시지가 하나씩 포함된 경우 East 지역의 메시지는 직렬로 실행되고 West 지역의 메시지는 East 지역의 메시지와 병렬로 실행됩니다.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

잠금의 기본 범위는 `SingletonScope.Function`입니다. 즉, 잠금 범위(BLOB 임대 경로)가 정규화된 함수 이름에 연결됩니다. 모든 함수에서 잠그려면 `SingletonScope.Host`를 지정하고, 동시에 실행하지 않으려는 모든 함수에서 동일한 범위 ID 이름을 사용하세요. 다음 예제에서는 `AddItem` 또는 `RemoveItem`의 인스턴스가 한 번에 하나씩 실행됩니다.

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>임대 BLOB 보기

WebJobs SDK는 분산 잠금을 구현하기 위해 백그라운드에서 [Azure blob 임대](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)를 사용합니다. 싱글톤에서 사용하는 임대 BLOB은 "locks" 경로 아래의 `AzureWebJobsStorage` 스토리지 계정에 있는 `azure-webjobs-host` 컨테이너에서 찾을 수 있습니다. 예를 들어 앞에서 살펴본 첫 번째 `ProcessImage` 예제의 임대 BLOB 경로가 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`일 수 있습니다. 모든 경로는 JobHost ID(이 예에서는 061851c758f04938a4426aa9ab3869c0)를 포함합니다.

## <a name="async-functions"></a>비동기 함수

비동기 함수를 코딩하는 방법은 [Azure Functions 설명서](../azure-functions/functions-dotnet-class-library.md#async)를 참조하세요.

## <a name="cancellation-tokens"></a>취소 토큰

취소 토큰을 처리하는 방법은 [취소 토큰 및 정상 종료](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)에 대한 Azure Functions 설명서를 참조하세요.

## <a name="multiple-instances"></a>여러 인스턴스

웹앱이 여러 인스턴스에서 실행되는 경우 연속적인 WebJob이 각 인스턴스에서 실행되고, 트리거를 기다렸다가 함수를 호출합니다. 더 많은 인스턴스로 확장하여 더 많은 부하를 처리할 수 있도록, 작업을 효율적으로 여러 인스턴스에서 공동으로 공유하도록 설계된 다양한 트리거 바인딩이 있습니다.

일부 트리거는 이중 처리로 이어질 수 있지만, 큐 및 BLOB 스토리지 트리거는 함수가 큐 메시지 또는 BLOB을 여러 번 처리하지 못하게 자동으로 방지합니다. 자세한 내용은 Azure Functions 설명서에서 [동일한 입력을 위한 디자인](../azure-functions/functions-idempotent.md)을 참조하세요.

타이머 트리거는 자동으로 타이머의 한 인스턴스만 실행되도록 보장하므로 예약된 특정 시간에 오직 하나의 함수 인스턴스만 실행됩니다.

호스트 웹앱의 인스턴스가 여러 개 있더라도 함수의 인스턴스 중 하나만 실행되도록 하려면 [`Singleton`](#singleton-attribute) 특성을 사용하면 됩니다.

## <a name="filters"></a>필터

함수 필터(미리 보기)는 사용자 고유의 논리로 WebJobs 실행 파이프라인을 사용자 지정하는 방법을 제공합니다. 필터는 [ASP.NET Core 필터](/aspnet/core/mvc/controllers/filters)와 비슷합니다. 함수 또는 클래스에 적용되는 선언적 특성으로 구현할 수 있습니다. 자세한 내용은 [함수 필터](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

ASP.NET Core용으로 개발된 로깅 프레임워크를 권장합니다. 사용 방법은 [시작](webjobs-sdk-get-started.md) 문서에 설명되어 있습니다. 

### <a name="log-filtering"></a>로그 필터링

`ILogger` 인스턴스로 만든 모든 로그에는 `Category` 및 `Level`이 연결됩니다. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)은 열거형이며, 정수 코드는 상대적 중요도를 나타냅니다.

|LogLevel    |코드|
|------------|---|
|추적       | 0 |
|디버그       | 1 |
|정보 | 2 |
|Warning     | 3 |
|Error       | 4 |
|위험    | 5 |
|None        | 6 |

각 범주를 특정 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)로 독립적으로 필터링할 수 있습니다. 예를 들어 Blob 트리거 처리에 대한 모든 로그를 보고 싶지만 그 외에는 `Error` 이상만 보고 싶은 경우가 있습니다.

#### <a name="version-3x"></a>3\.*x* 버전

SDK 3.*x* 버전은 .NET Core에 기본 제공된 필터링을 사용합니다. `LogCategories` 클래스를 사용하여 특정 함수, 트리거 또는 사용자의 범주를 정의할 수 있습니다. 또한 `Startup` 및 `Results`와 같은 특정 호스트 상태용 필터도 정의합니다. 이러한 방식으로 로깅 출력을 미세 조정할 수 있습니다. 정의된 범주 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

`LogCategories`에는 다음 using 문이 필요합니다.

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

다음 예제는 기본적으로 `Warning` 수준에서 모든 로그를 필터링하는 필터를 작성합니다. `Function` 및 `results` 범주(2.*x* 버전의 `Host.Results`에 해당)는 `Error` 수준에서 필터링됩니다. 이 필터는 현재 범주를 `LogCategories` 인스턴스의 모든 등록된 수준과 비교하여 가장 긴 일치 항목을 선택합니다. 즉, `Host.Triggers`에 대해 등록된 `Debug` 수준은 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`과 일치합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>2\.*x* 버전

SDK 2.*x* 버전에서는 `LogCategoryFilter` 클래스를 사용하여 필터링을 제어합니다. `LogCategoryFilter`의 `Default` 속성은 초기 값이 `Information`입니다. 즉, 수준이 `Information`, `Warning`, `Error` 또는 `Critical`인 메시지는 로깅되지만 수준이 `Debug` 또는 `Trace`인 메시지는 필터링됩니다.

3\.*x* 버전의 `LogCategories`와 마찬가지로, `CategoryLevels` 속성을 사용하면 특정 범주의 로그 수준을 지정하여 로깅 출력을 조정할 수 있습니다. `CategoryLevels` 디렉터리 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

다음 예제는 기본적으로 `Warning` 수준에서 모든 로그를 필터링하는 필터를 작성합니다. `Function` 또는 `Host.Results` 범주는 `Error` 수준에서 필터링됩니다. `LogCategoryFilter`는 현재 범주를 모든 등록된 `CategoryLevels`와 비교하여 가장 긴 일치 항목을 선택합니다. 따라서 `Host.Triggers`에 대해 등록된 `Debug` 수준은 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`과 일치합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights에 대한 사용자 지정 원격 분석​

[Application Insights](../azure-monitor/app/app-insights-overview.md)에 대한 사용자 지정 원격 분석을 구현하는 프로세스는 SDK 버전에 따라 달라집니다. Application Insights를 구성하는 방법을 알아보려면 [Application Insights 로깅 추가](webjobs-sdk-get-started.md#add-application-insights-logging)를 참조하세요.

#### <a name="version-3x"></a>3\.*x* 버전

WebJobs SDK 3.*x* 버전은 .NET Core 일반 호스트를 사용하므로 더 이상 사용자 지정 원격 분석 팩터리가 제공되지 않습니다. 그러나 종속성 주입을 사용하여 파이프라인에 사용자 지정 원격 분석을 추가할 수 있습니다. 이 섹션의 예제에는 다음 `using` 문이 필요합니다.

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

다음 사용자 지정 [`ITelemetryInitializer`] 구현을 사용하면 기본 [`TelemetryConfiguration`]에 고유한 [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)를 추가할 수 있습니다.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

작성기에서 [`ConfigureServices`]를 호출하여 파이프라인에 사용자 지정 [`ITelemetryInitializer`]를 추가합니다.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

[`TelemetryConfiguration`]이 생성되면 등록된 모든 [`ITelemetryInitializer`] 유형이 포함됩니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭용 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)를 참조하세요.

3\.*x* 버전에서는 호스트가 중지될 때 더 이상 [`TelemetryClient`]를 플러시할 필요가 없습니다. .NET Core 종속성 주입 시스템은 [`TelemetryClient`]를 플러시하는 등록된 `ApplicationInsightsLoggerProvider`를 자동으로 제거합니다.

#### <a name="version-2x"></a>2\.*x* 버전

2\.*x* 버전에서 Application Insights 공급자가 내부적으로 WebJobs SDK용으로 만든 [`TelemetryClient`]는 [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll)을 사용합니다. Application Insights 엔드포인트를 사용할 수 없거나 들어오는 요청을 제한하는 경우 이 채널은 [웹앱의 파일 시스템에 요청을 저장해 두었다가 나중에 다시 전송](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/)합니다.

[`TelemetryClient`]는 `ITelemetryClientFactory`를 구현하는 클래스에서 생성합니다. 기본적으로 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)입니다.

Application Insights 파이프라인의 특정 부분을 수정하려는 경우 사용자 고유의 `ITelemetryClientFactory`를 제공하면 호스트가 해당 클래스를 사용하여 [`TelemetryClient`]를 생성합니다. 예를 들어 다음 코드는 `ServerTelemetryChannel` 속성을 수정하도록 `DefaultTelemetryClientFactory`를 재정의합니다.

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings` 개체는 [적응 샘플링](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)을 구성합니다. 즉, 특정 대량 볼륨 시나리오에서는 Applications Insights가 서버에 선별된 원격 분석 데이터 하위 세트를 보냅니다.

원격 분석 팩터리를 만들었으면 다음과 같이 Application Insights 로깅 공급자에게 전달합니다.

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> 다음 단계

이 문서에서는 WebJobs SDK 사용 방법에 대한 일반적인 시나리오를 처리하는 방법을 보여주는 코드 조각을 제공했습니다. 전체 샘플은 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)를 참조하세요.

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs