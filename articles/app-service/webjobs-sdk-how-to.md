---
title: WebJobs SDK를 사용 하는 방법
description: WebJobs SDK에 대한 코드 작성 방법을 알아봅니다. Azure 및 타사 서비스의 데이터에 액세스 하는 이벤트 구동 백그라운드 처리 작업을 만듭니다.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 83884447e9856b5e3db26e4829ccbd3ab1baed13
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549090"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 사용 방법

이 문서에서는 Azure WebJobs SDK를 사용 하는 방법에 대한 지침을 제공 합니다. WebJobs를 즉시 시작 하려면 [이벤트 기반 백그라운드 처리를 위해 AZURE WEBJOBS SDK 시작](webjobs-sdk-get-started.md)을 참조 하세요. 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 버전

버전 3의 주요 차이점은 다음과 같습니다. *x* 및 버전 2. WebJobs SDK의 *x* :

* 버전 3. *x* 는 .net Core에 대한 지원을 추가 합니다.
* 버전 3 *x*, WebJobs SDK에 필요한 저장소 바인딩 확장을 명시적으로 설치 해야 합니다. 버전 2에서 *x*, 저장소 바인딩이 SDK에 포함 되었습니다.
* .NET Core 용 Visual Studio 도구 (3. *x*) 프로젝트는 .NET Framework에 대한 도구 (2)와 다릅니다. *x*) 프로젝트. 자세히 알아보려면 [Visual Studio를 사용 하 여 WebJobs 개발 및 배포-Azure App Service](webjobs-dotnet-deploy-vs.md)를 참조 하세요.

가능 하면 예제가 버전 3에 제공 됩니다. *x* 및 버전 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 는 WebJobs SDK를 기반으로 하며,이 문서에서는 일부 항목의 Azure Functions 설명서에 대한 링크를 제공 합니다. 함수와 WebJobs SDK의 차이점은 다음과 같습니다.
> * Azure Functions 버전 2. *x* 는 WebJobs SDK 버전 3에 해당 합니다. *x*및 Azure Functions 1. *x* 는 WebJobs SDK 2에 해당 합니다. *x*. 소스 코드 리포지토리는 WebJobs SDK 번호 매기기를 사용 합니다.
> * Azure Functions C# 클래스 라이브러리의 샘플 코드는 WebJobs sdk 프로젝트에 `FunctionName` 특성이 필요 하지 않은 경우를 제외 하 고는 WebJobs sdk 코드와 비슷합니다.
> * 일부 바인딩 형식은 http (Webhooks)와 같은 함수 에서만 지원 되며 HTTP를 기반으로 하는 Event Grid 지원 됩니다.
>
> 자세한 내용은 [WebJobs SDK과 Azure Functions 비교](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요.

## <a name="webjobs-host"></a>WebJobs 호스트

호스트는 함수의 런타임 컨테이너입니다.  트리거를 수신 대기하고 함수를 호출합니다. 버전 3 *x*, 호스트는 `IHost`의 구현입니다. 버전 2에서 *x*에서는 `JobHost` 개체를 사용 합니다. 사용자는 코드에서 호스트 인스턴스를 만들고 동작을 사용자 지정하는 코드를 작성하게 됩니다.

WebJobs SDK를 직접 사용 하 고 Azure Functions를 통해 간접적으로 사용 하는 경우의 주요 차이점입니다. Azure Functions에서 서비스는 호스트를 제어 하 고 코드를 작성 하 여 호스트를 사용자 지정할 수 없습니다. Azure Functions를 사용 하면 호스트 json 파일의 설정을 통해 호스트 동작을 사용자 지정할 수 있습니다. 이러한 설정은 코드와는 달리 문자열이 며 수행할 수 있는 사용자 지정의 종류를 제한 합니다.

### <a name="host-connection-strings"></a>호스트 연결 문자열

WebJobs SDK는 로컬에서 실행 하는 경우 또는 WebJob 환경에서 Azure에서 실행 하는 경우 파일에서 연결 문자열 Azure Storage 및 Azure Service Bus를 찾습니다. 기본적으로 `AzureWebJobsStorage` 이라는 저장소 연결 문자열 설정이 필요 합니다.  

버전 2. SDK의 *x* 를 사용 하면 이러한 연결 문자열에 고유한 이름을 사용 하거나 다른 위치에 저장할 수 있습니다. 다음과 같이 [`JobHostConfiguration`]를 사용 하 여 코드에서 이름을 설정할 수 있습니다.

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

버전 3입니다. *x* 는 기본 .net Core 구성 api를 사용 하 고 연결 문자열 이름을 변경 하는 API는 없습니다.

### <a name="host-development-settings"></a>호스트 개발 설정

로컬 개발을 보다 효율적으로 수행하기 위해 개발 모드에서 호스트를 실행할 수 있습니다. 개발 모드에서를 실행할 때 변경 되는 일부 설정은 다음과 같습니다.

| 속성 | 개발 설정 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | 로그 출력을 최대화하는 `TraceLevel.Verbose`. |
| `Queues.MaxPollingInterval`  | 값이 작으면 큐 메서드가 즉시 트리거됩니다.  |
| `Singleton.ListenerLockPeriod` | 신속한 반복 개발에 도움이 되는 시간은 15초입니다. |

개발 모드를 사용 하도록 설정 하는 프로세스는 SDK 버전에 따라 다릅니다. 

#### <a name="version-3x"></a>버전 3. *x*

버전 3. *x* 는 표준 ASP.NET Core api를 사용 합니다. [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스에서 [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) 메서드를 호출 합니다. 다음 예제와 같이 `development`라는 문자열을 전달 합니다.

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

#### <a name="version-2x"></a>버전 2. *x*

`JobHostConfiguration` 클래스에는 개발 모드를 사용하도록 설정하는 `UseDevelopmentSettings` 메서드가 있습니다.  다음 예제에서는 개발 설정을 사용하는 방법을 보여줍니다. 로컬에서 실행 될 때 `true` 반환 `config.IsDevelopment` 하려면 `AzureWebJobsEnv` 이라는 로컬 환경 변수를 `Development`값으로 설정 합니다.

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

### <a name="jobhost-servicepointmanager-settings"></a>동시 연결 관리 (버전 2 *) x*)

버전 3 *기본적으로*연결 제한은 무한 연결로 설정 됩니다. 어떤 이유로이 제한을 변경 해야 하는 경우 [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) 클래스의 [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) 속성을 사용할 수 있습니다.

버전 2에서 *x*는 Servicepointmanager를 사용 하 여 호스트에 대한 동시 연결 수를 제어 합니다 [. defaultconnectionlimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2\. *x*에서는 WebJobs 호스트를 시작 하기 전에이 값을 기본값인 2에서 늘려야 합니다.

`HttpClient`를 사용 하 여 함수에서 수행 하는 모든 나가는 HTTP 요청은 `ServicePointManager`를 통해 흐릅니다. `DefaultConnectionLimit`설정 된 값에 도달 하면 큐에서 요청을 보내기 전에 큐에서 요청을 시작 `ServicePointManager`. `DefaultConnectionLimit`가 2로 설정되었고 코드에서 HTTP 요청 1,000개를 만든다고 가정해 봅시다. 처음에는 OS까지 전달되는 요청이 2개밖에 없습니다. 나머지 998개는 공간이 생길 때까지 큐에서 대기합니다. 즉, 요청이 발생 한 것 처럼 보이지만 OS에서 대상 서버로 요청을 보내지 않았기 때문에 `HttpClient` 시간이 초과 될 수 있습니다. 이와 같은 이유로 로컬 `HttpClient`가 요청을 완료하는 데 10초가 걸리지만 서비스가 200ms 후에 모든 요청을 반환하는 이상한 동작이 관찰될 수 있습니다. 

ASP.NET 응용 프로그램의 기본값은 `Int32.MaxValue`되며, 기본 또는 더 높은 App Service 계획에서 실행 되는 WebJobs에 대해 잘 작동 합니다. WebJobs에는 일반적으로 Always On 설정이 필요 하며,이는 기본 이상 App Service 요금제 에서만 지원 됩니다.

WebJob이 무료 또는 공유 App Service 계획에서 실행되는 경우 애플리케이션이 App Service 샌드박스의 제한을 받으며, 현재 [연결 제한은 300개](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)입니다. `ServicePointManager`에 바인딩되지 않은 연결 제한이 있으면 sandbox 연결 임계값에 도달 하 여 사이트가 종료 될 가능성이 높습니다. 이 경우 `DefaultConnectionLimit`를 50 또는 100처럼 약간 낮추면 이와 같은 상황을 방지하면서도 충분한 처리량을 계속 제공할 수 있습니다.

이 설정은 HTTP 요청을 만들기 전에 구성해야 합니다. 이러한 이유로 WebJobs 호스트는 설정을 자동으로 조정 하지 않아야 합니다. 호스트를 시작 하기 전에 발생 하는 HTTP 요청이 있을 수 있으며,이로 인해 예기치 않은 동작이 발생할 수 있습니다. 가장 좋은 방법은 다음과 같이 `JobHost`를 초기화 하기 전에 `Main` 메서드에서 즉시 값을 설정 하는 것입니다.

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

함수는 public 메서드 여야 하며 트리거 특성이 나 [`NoAutomaticTrigger`](#manual-triggers) 특성이 하나 있어야 합니다.

### <a name="automatic-triggers"></a>자동 트리거

자동 트리거는 이벤트에 대한 응답으로 함수를 호출합니다. Azure Queue storage에 추가 된 메시지에 의해 트리거되는 함수의이 예를 살펴보겠습니다. Azure Blob storage에서 blob을 읽어 응답 합니다.

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` 특성은 큐 메시지가 `myqueue-items` 큐에 나타날 때마다 함수를 호출 하도록 런타임에 지시 합니다. `Blob` 특성은 *샘플* 작업 항목 컨테이너에서 큐 메시지를 사용 하 여 blob를 읽도록 런타임에 지시 합니다. `myQueueItem` 매개 변수에서 함수로 전달 되는 큐 메시지의 내용은 blob의 이름입니다.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>수동 트리거

함수를 수동으로 트리거하려면 다음과 같이 `NoAutomaticTrigger` 특성을 사용 합니다.

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

함수를 수동으로 트리거하는 프로세스는 SDK 버전에 따라 달라 집니다.

#### <a name="version-3x"></a>버전 3. *x*

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

#### <a name="version-2x"></a>버전 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>입력 및 출력 바인딩

입력 바인딩은 Azure 또는 타사 서비스의 데이터를 코드에 사용할 수 있는 선언적 방법을 제공합니다. 출력 바인딩은 데이터를 업데이트하는 방법을 제공합니다. [시작](webjobs-sdk-get-started.md) 문서에서는 각각의 예제를 보여 줍니다.

메서드 반환 값에 특성을 적용 하 여 출력 바인딩에 메서드 반환 값을 사용할 수 있습니다. [Azure Function 반환 값 사용](../azure-functions/functions-bindings-return-value.md)의 예제를 참조 하세요.

## <a name="binding-types"></a>바인딩 형식

바인딩 유형을 설치 하 고 관리 하는 프로세스는 버전 3을 사용 중인지 여부에 따라 달라 집니다. *x* 또는 버전 2. SDK의 *x* 입니다. 특정 바인딩 유형에 대한 설치 패키지는 해당 바인딩 유형의 Azure Functions [참조 문서](#binding-reference-information)에 있는 "패키지" 섹션에서 찾을 수 있습니다. 예외는 Azure Functions에서 지원 하지 않는 파일 트리거 및 바인딩 (로컬 파일 시스템의 경우)입니다.

#### <a name="version-3x"></a>버전 3. *x*

버전 3 *x*는 `Microsoft.Azure.WebJobs.Extensions.Storage` 패키지에 저장소 바인딩이 포함 되어 있습니다. 다음과 같이 `ConfigureWebJobs` 메서드에서 `AddAzureStorage` 확장 메서드를 호출 합니다.

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

다른 트리거 및 바인딩 형식을 사용하려면 해당 항목을 포함하는 NuGet 패키지를 설치하고 확장에서 구현된 `Add<binding>` 확장 메서드를 호출합니다. 예를 들어 Azure Cosmos DB 바인딩을 사용 하려는 경우 `Microsoft.Azure.WebJobs.Extensions.CosmosDB`를 설치 하 고 다음과 같이 `AddCosmosDB`를 호출 합니다.

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

#### <a name="version-2x"></a>버전 2. *x*

이러한 트리거와 바인딩 형식은 버전 2에 포함 되어 있습니다. `Microsoft.Azure.WebJobs` 패키지의 *x* :

* Blob Storage
* Queue Storage
* Table Storage

다른 트리거 및 바인딩 형식을 사용하려면 사용하려는 트리거 및 바인딩 형식이 포함된 NuGet 패키지를 설치하고 `JobHostConfiguration` 개체에서 `Use<binding>` 메서드를 호출합니다. 예를 들어 타이머 트리거를 사용 하려면 `Microsoft.Azure.WebJobs.Extensions`를 설치 하 고 다음과 같이 `Main` 메서드에서 `UseTimers`를 호출 합니다.

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

[`ExecutionContext`] 에 바인딩하는 프로세스는 SDK 버전에 따라 달라 집니다.

#### <a name="version-3x"></a>버전 3. *x*

다음과 같이 `ConfigureWebJobs` 메서드에서 `AddExecutionContextBinding` 확장 메서드를 호출 합니다.

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

#### <a name="version-2x"></a>버전 2. *x*

앞에서 언급한 `Microsoft.Azure.WebJobs.Extensions` 패키지는 `UseCore` 메서드를 호출하여 등록할 수 있는 특별한 바인딩 형식을 제공합니다. 이 바인딩을 사용 하면 함수 서명에서 다음과 같이 사용할 수 있는 [`ExecutionContext`] 매개 변수를 정의할 수 있습니다.

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

일부 트리거와 바인딩의 동작을 구성할 수 있습니다. 이를 구성 하는 프로세스는 SDK 버전에 따라 다릅니다.

* **버전 3. *x*:** `ConfigureWebJobs`에서 `Add<Binding>` 메서드가 호출 될 때 구성을 설정 합니다.
* **버전 2. *x*:** `JobHost`에 전달 하는 구성 개체의 속성을 설정 하 여 구성을 설정 합니다.

이러한 바인딩 별 설정은 Azure Functions의 [호스트 json 프로젝트 파일](../azure-functions/functions-host-json.md) 의 설정과 동일 합니다.

다음 바인딩을 구성할 수 있습니다.

* [Azure CosmosDB 트리거](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs 트리거](#event-hubs-trigger-configuration-version-3x)
* [Queue storage 트리거](#queue-storage-trigger-configuration)
* [SendGrid 바인딩](#sendgrid-binding-configuration-version-3x)
* [Service Bus 트리거](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 트리거 구성 (버전 3). *x*)

이 예제에서는 Azure Cosmos DB 트리거를 구성 하는 방법을 보여 줍니다.

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

자세한 내용은 [Azure CosmosDB binding](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) 문서를 참조 하세요.

### <a name="event-hubs-trigger-configuration-version-3x"></a>트리거 구성 (버전 3)을 Event Hubs 합니다. *x*)

이 예제에서는 Event Hubs 트리거를 구성 하는 방법을 보여 줍니다.

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

자세한 내용은 [Event Hubs 바인딩](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) 문서를 참조 하세요.

### <a name="queue-storage-trigger-configuration"></a>큐 저장소 트리거 구성

다음 예제에서는 Queue storage 트리거를 구성 하는 방법을 보여 줍니다.

#### <a name="version-3x"></a>버전 3. *x*

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

자세한 내용은 [큐 저장소 바인딩](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) 문서를 참조 하세요.

#### <a name="version-2x"></a>버전 2. *x*

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

자세한 내용은 [host-a v1. x 참조](../azure-functions/functions-host-json-v1.md#queues)를 참조 하세요.

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid binding 구성 (버전 3)입니다. *x*)

이 예제에서는 SendGrid 출력 바인딩을 구성 하는 방법을 보여 줍니다.

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

자세한 내용은 [SendGrid 바인딩](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) 문서를 참조 하세요.

### <a name="service-bus-trigger-configuration-version-3x"></a>트리거 구성 (버전 3)을 Service Bus 합니다. *x*)

이 예제에서는 Service Bus 트리거를 구성 하는 방법을 보여 줍니다.

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

자세한 내용은 [Service Bus 바인딩](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) 문서를 참조 하세요.

### <a name="configuration-for-other-bindings"></a>다른 바인딩의 구성

일부 트리거 및 바인딩 형식은 고유한 사용자 지정 구성 유형을 정의 합니다. 예를 들어 파일 트리거를 사용 하면 다음 예제와 같이 모니터링할 루트 경로를 지정할 수 있습니다.

#### <a name="version-3x"></a>버전 3. *x*

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

#### <a name="version-2x"></a>버전 2. *x*

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

경우에 따라 코드에서 큐 이름, blob 이름 또는 컨테이너 또는 테이블 이름을 하드 코딩 하는 대신 지정 하려고 합니다. 예를 들어 구성 파일 또는 환경 변수에서 `QueueTrigger` 특성에 대한 큐 이름을 지정할 수 있습니다.

`NameResolver` 개체를 `JobHostConfiguration` 개체에 전달 하 여이 작업을 수행할 수 있습니다. 트리거 또는 바인딩 특성 생성자 매개 변수에 자리 표시자를 포함하면 `NameResolver` 코드가 해당 자리 표시자 위치에 사용할 실제 값을 제공합니다. 자리 표시자를 백분율 (%)로 묶어서 식별할 수 있습니다. 다음과 같이 서명 합니다.

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

이 코드를 사용하면 테스트 환경에 `logqueuetest`라는 큐를 사용하고 프로덕션 환경에 `logqueueprod`라는 큐를 사용할 수 있습니다. 하드 코드된 큐 이름 대신 `appSettings` 컬렉션의 항목 이름을 지정합니다.

사용자 지정 항목을 제공 하지 않는 경우에는 기본 `NameResolver` 적용 됩니다. 기본값은 앱 설정이나 환경 변수에서 가져옵니다.

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

#### <a name="version-3x"></a>버전 3. *x*

종속성 주입을 사용 하 여 확인자를 구성 합니다. 이러한 샘플에는 다음 `using` 문이 필요합니다.

```cs
using Microsoft.Extensions.DependencyInjection;
```

다음 예제와 같이 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)에 대한 [`ConfigureServices`] 확장 메서드를 호출 하 여 해결 프로그램을 추가 합니다.

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

#### <a name="version-2x"></a>버전 2. *x*

다음과 같이 `NameResolver` 클래스를 `JobHost` 개체에 전달 합니다.

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

`Queue`, `Blob`또는 `Table`와 같은 바인딩 특성을 사용 하기 전에 함수에서 일부 작업을 수행 해야 하는 경우 `IBinder` 인터페이스를 사용할 수 있습니다.

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

Azure Functions 설명서에서는 각 바인딩 형식에 대한 참조 정보를 제공 합니다. 각 바인딩 참조 문서에서 다음 정보를 찾을 수 있습니다. 이 예제는 저장소 큐를 기반으로 합니다.

* [패키지](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). WebJobs SDK 프로젝트에서 바인딩에 대한 지원을 포함 하기 위해 설치 해야 하는 패키지입니다.
* [예](../azure-functions/functions-bindings-storage-queue.md#trigger). 코드 샘플. 클래스 C# 라이브러리 예제는 WebJobs SDK에 적용 됩니다. `FunctionName` 특성만 생략 하면 됩니다.
* [특성](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes-and-annotations). 바인딩 형식에 사용할 특성입니다.
* [구성](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). 특성 속성 및 생성자 매개 변수에 대한 설명입니다.
* [사용 현황](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). 바인딩할 수 있는 형식과 바인딩의 작동 방식에 대한 정보입니다. 예: 폴링 알고리즘, 포이즌 큐 처리.
  
바인딩 참조 문서 목록은 Azure Functions의 [트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md#supported-bindings) 문서에서 "지원 되는 바인딩"을 참조 하세요. 이 목록에서 HTTP, Webhook 및 Event Grid 바인딩은 WebJobs SDK가 아닌 Azure Functions 에서만 지원 됩니다.

## <a name="disable-attribute"></a>Disable 특성 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 특성을 사용 하면 함수를 트리거할 수 있는지 여부를 제어할 수 있습니다. 

다음 예제에서는 앱 설정 `Disable_TestJob` `1` 또는 `True` (대/소문자 구분 안 함) 값이 있는 경우 함수가 실행 되지 않습니다. 이 경우 런타임에서 *Function 'Functions.TestJob' is disabled* 로그 메시지를 작성합니다.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure Portal에서 앱 설정 값을 변경 하면 WebJob이 다시 시작 되어 새 설정이 선택 됩니다.

매개 변수, 메서드 또는 클래스 수준에서 특성을 선언할 수 있습니다. 설정 이름에 바인딩 식을 포함할 수도 있습니다.

## <a name="timeout-attribute"></a>Timeout 특성

[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 특성은 지정 된 시간 내에 완료 되지 않으면 함수를 취소 합니다. 다음 예제에서 함수는 Timeout 특성 없이 1 일 동안 실행 됩니다. Timeout은 15 초 후에 함수가 취소 되도록 합니다.

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

제한 시간 특성은 클래스 또는 메서드 수준에서 적용할 수 있으며 `JobHostConfiguration.FunctionTimeout`를 사용 하 여 전역 시간 제한을 지정할 수 있습니다. 클래스 수준 또는 메서드 수준 시간 제한은 전역 시간 제한을 재정의 합니다.

## <a name="singleton-attribute"></a>Singleton 특성

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 특성은 호스트 웹 앱의 인스턴스가 여러 개 있는 경우에도 하나의 함수 인스턴스만 실행 되도록 합니다. [분산 잠금을](#viewing-lease-blobs)사용 하 여이를 수행 합니다.

이 예에서는 지정 된 시간에 `ProcessImage` 함수의 단일 인스턴스만 실행 됩니다.

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

이러한 설정을 사용하여 함수가 단일 인스턴스에서 싱글톤으로 실행되도록 할 수 있습니다. 웹 앱이 여러 인스턴스로 확장 될 때 함수의 단일 인스턴스만 실행 되도록 하려면 함수 (`[Singleton(Mode = SingletonMode.Listener)]`)에 수신기 수준 singleton 잠금을 적용 합니다. JobHost를 시작 하면 수신기 잠금이 획득 됩니다. 확장된 인스턴스 3개가 동시에 시작되면 인스턴스 중 하나만 잠금을 획득하고 하나의 수신기만 시작됩니다.

### <a name="scope-values"></a>범위 값

Singleton에 *범위 식/값* 을 지정할 수 있습니다. 식/값을 사용 하면 특정 범위에서 함수의 모든 실행이 serialize 됩니다. 이러한 방식으로 보다 세부적인 잠금을 구현 하면 요구 사항에 따라 다른 호출을 serialize 하는 동안 함수에 대해 일정 수준의 병렬 처리를 수행할 수 있습니다. 예를 들어 다음 코드에서 범위 식은 들어오는 메시지의 `Region` 값에 바인딩됩니다. 큐에 동부, 동부 및 서 부 지역에 각각 세 개의 메시지가 포함 되어 있는 경우 지역이 인 메시지는 순차적으로 실행 되 고 지역 서 부는 메시지는 동쪽의 메시지와 병렬로 실행 됩니다.

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

잠금에 대한 기본 범위는 `SingletonScope.Function`입니다. 즉, 잠금 범위 (blob 임대 경로)가 정규화 된 함수 이름에 연결 됩니다. 함수 간에 잠그려면 `SingletonScope.Host`를 지정 하 고 동시에 실행 하지 않으려는 모든 함수에서 동일한 범위 ID 이름을 사용 합니다. 다음 예제에서는 `AddItem` 또는 `RemoveItem`의 인스턴스가 한 번에 하나씩 실행됩니다.

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

WebJobs SDK는 분산 잠금을 구현하기 위해 백그라운드에서 [Azure blob 임대](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)를 사용합니다. Singleton에서 사용 하는 임대 blob은 "잠금" 경로 아래에 있는 `AzureWebJobsStorage` 저장소 계정의 `azure-webjobs-host` 컨테이너에서 찾을 수 있습니다. 예를 들어 앞에서 살펴본 첫 번째 `ProcessImage` 예제의 임대 BLOB 경로가 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`일 수 있습니다. 모든 경로는 JobHost ID(이 예에서는 061851c758f04938a4426aa9ab3869c0)를 포함합니다.

## <a name="async-functions"></a>비동기 함수

비동기 함수를 코딩 하는 방법에 대한 자세한 내용은 [Azure Functions 설명서](../azure-functions/functions-dotnet-class-library.md#async)를 참조 하세요.

## <a name="cancellation-tokens"></a>취소 토큰

취소 토큰을 처리하는 방법은 [취소 토큰 및 정상 종료](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)에 대한 Azure Functions 설명서를 참조하세요.

## <a name="multiple-instances"></a>여러 인스턴스

웹앱이 여러 인스턴스에서 실행되는 경우 연속적인 WebJob이 각 인스턴스에서 실행되고, 트리거를 기다렸다가 함수를 호출합니다. 더 많은 인스턴스로 확장하여 더 많은 부하를 처리할 수 있도록, 작업을 효율적으로 여러 인스턴스에서 공동으로 공유하도록 설계된 다양한 트리거 바인딩이 있습니다.

일부 트리거는 이중 처리를 발생 시킬 수 있지만 queue 및 blob storage 트리거는 함수가 큐 메시지 또는 blob을 두 번 이상 처리 하는 것을 자동으로 방지 합니다. 자세한 내용은 Azure Functions 설명서에서 [동일한 입력을 위한 디자인](../azure-functions/functions-idempotent.md) 을 참조 하세요.

타이머 트리거는 자동으로 타이머의 한 인스턴스만 실행되도록 보장하므로 예약된 특정 시간에 오직 하나의 함수 인스턴스만 실행됩니다.

호스트 웹 앱의 인스턴스가 여러 개 있는 경우에도 하나의 함수 인스턴스만 실행 되도록 하려면 [`Singleton`](#singleton-attribute) 특성을 사용할 수 있습니다.

## <a name="filters"></a>필터

함수 필터(미리 보기)는 사용자 고유의 논리로 WebJobs 실행 파이프라인을 사용자 지정하는 방법을 제공합니다. 필터는 [ASP.NET Core 필터](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)와 유사 합니다. 함수 또는 클래스에 적용 되는 선언적 특성으로 구현할 수 있습니다. 자세한 내용은 [함수 필터](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

ASP.NET 용으로 개발 된 로깅 프레임 워크를 권장 합니다. [시작](webjobs-sdk-get-started.md) 문서에서는이를 사용 하는 방법을 보여 줍니다. 

### <a name="log-filtering"></a>로그 필터링

`ILogger` 인스턴스로 만든 모든 로그에는 `Category` 및 `Level`이 연결됩니다. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) 는 열거형 이며 정수 코드는 상대적 중요도를 나타냅니다.

|LogLevel    |코드|
|------------|---|
|추적       | 0 |
|디버그       | 1 |
|정보 | 2 |
|경고     | 3 |
|오류       | 4 |
|위험    | 5 |
|없음        | 6 |

각 범주를 특정 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)에 독립적으로 필터링 할 수 있습니다. 예를 들어 Blob 트리거 처리에 대한 모든 로그를 보고 싶지만 그 외에는 `Error` 이상만 보고 싶은 경우가 있습니다.

#### <a name="version-3x"></a>버전 3. *x*

버전 3. SDK의 *x* 는 .net Core에 기본 제공 되는 필터링에 의존 합니다. `LogCategories` 클래스를 사용하여 특정 함수, 트리거 또는 사용자의 범주를 정의할 수 있습니다. 또한 `Startup` 및 `Results`와 같은 특정 호스트 상태에 대한 필터를 정의 합니다. 이를 통해 로깅 출력을 미세 조정할 수 있습니다. 정의된 범주 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

`LogCategories`에는 다음 using 문이 필요합니다.

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

다음 예에서는 기본적으로 `Warning` 수준의 모든 로그를 필터링 하는 필터를 생성 합니다. `Function` 및 `results` 범주 (버전 2의 `Host.Results`와 동일 합니다. *x*)는 `Error` 수준에서 필터링 됩니다. 이 필터는 현재 범주를 `LogCategories` 인스턴스의 모든 등록된 수준과 비교하여 가장 긴 일치 항목을 선택합니다. 이는 `Host.Triggers`에 대해 등록 된 `Debug` 수준이 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`일치 함을 의미 합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

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

#### <a name="version-2x"></a>버전 2. *x*

버전 2에서 *x* SDK의 경우 `LogCategoryFilter` 클래스를 사용 하 여 필터링을 제어할 수 있습니다. `LogCategoryFilter`에는 초기 값이 `Information`인 `Default` 속성이 있습니다. 즉, `Information`, `Warning`, `Error`또는 `Critical` 수준의 모든 메시지가 기록 되지만 `Debug` 또는 `Trace` 수준의 모든 메시지는 필터링 됩니다.

버전 3의 `LogCategories`와 동일 합니다. `CategoryLevels` *속성을 사용*하 여 로깅 출력을 세밀 하 게 조정할 수 있도록 특정 범주에 대한 로그 수준을 지정할 수 있습니다. `CategoryLevels` 디렉터리 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

다음 예제는 기본적으로 `Warning` 수준에서 모든 로그를 필터링하는 필터를 작성합니다. `Function` 및 `Host.Results` 범주는 `Error` 수준에서 필터링 됩니다. `LogCategoryFilter`는 현재 범주를 모든 등록된 `CategoryLevels`와 비교하여 가장 긴 일치 항목을 선택합니다. 따라서 `Host.Triggers`에 대해 등록 된 `Debug` 수준이 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`와 일치 합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

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

[Application Insights](../azure-monitor/app/app-insights-overview.md) 에 대한 사용자 지정 원격 분석을 구현 하는 프로세스는 SDK 버전에 따라 달라 집니다. Application Insights를 구성하는 방법을 알아보려면 [Application Insights 로깅 추가](webjobs-sdk-get-started.md#add-application-insights-logging)를 참조하세요.

#### <a name="version-3x"></a>버전 3. *x*

버전 3입니다. WebJobs SDK의 *x* 는 .net Core 일반 호스트에 의존 하므로 사용자 지정 원격 분석 팩터리가 더 이상 제공 되지 않습니다. 그러나 종속성 주입을 사용 하 여 파이프라인에 사용자 지정 원격 분석을 추가할 수 있습니다. 이 섹션의 예제에는 다음 `using` 문이 필요합니다.

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

[`TelemetryConfiguration`]이 생성되면 등록된 모든 [`ITelemetryInitializer`] 유형이 포함됩니다. 자세히 알아보려면 [사용자 지정 이벤트 및 메트릭에 대한 APPLICATION INSIGHTS API](../azure-monitor/app/api-custom-events-metrics.md)를 참조 하세요.

버전 3 *x*에서는 호스트가 중지 될 때 더 이상 [`TelemetryClient`] 를 플러시할 필요가 없습니다. .NET Core 종속성 주입 시스템은 [`TelemetryClient`]를 플러시하는 등록된 `ApplicationInsightsLoggerProvider`를 자동으로 제거합니다.

#### <a name="version-2x"></a>버전 2. *x*

버전 2에서 *x*에서 WebJobs SDK에 대한 Application Insights 공급자에 의해 내부적으로 생성 된 [`TelemetryClient`] 는 [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)를 사용 합니다. Application Insights 엔드포인트를 사용할 수 없거나 들어오는 요청을 제한하는 경우 이 채널은 [웹앱의 파일 시스템에 요청을 저장해 두었다가 나중에 다시 전송](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)합니다.

[`TelemetryClient`]는 `ITelemetryClientFactory`를 구현하는 클래스에서 생성합니다. 기본적으로 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)입니다.

Application Insights 파이프라인의 특정 부분을 수정하려는 경우 사용자 고유의 `ITelemetryClientFactory`를 제공하면 호스트가 해당 클래스를 사용하여 [`TelemetryClient`]를 생성합니다. 예를 들어이 코드는 `DefaultTelemetryClientFactory`를 재정의 하 여 `ServerTelemetryChannel`의 속성을 수정 합니다.

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

`SamplingPercentageEstimatorSettings` 개체는 [적응 샘플링](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)을 구성 합니다. 즉, 특정 고용량 시나리오에서 Application Insights는 원격 분석 데이터의 선택 된 하위 집합을 서버에 보냅니다.

원격 분석 팩터리를 만든 후에 Application Insights 로깅 공급자에 전달 합니다.

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 다음 단계

이 문서에서는 WebJobs SDK 작업에 대한 일반적인 시나리오를 처리 하는 방법을 보여 주는 코드 조각을 제공 했습니다. 전체 샘플은 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)를 참조하세요.

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
