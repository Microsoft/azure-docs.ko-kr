---
title: WebJob SDK 사용 방법 - Azure
description: WebJobs SDK에 대한 코드 작성 방법을 알아봅니다. 이벤트 중심 백그라운드 Azure 서비스 및 타사 서비스에서 데이터에 액세스 하는 처리 작업을 만듭니다.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831795"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 사용 방법

이 문서에서는 Azure WebJobs SDK를 사용 하는 방법을 안내 합니다. WebJobs를 사용 하 여 지금 바로 시작, 참조 [이벤트 중심 백그라운드 처리를 위해 Azure WebJobs SDK 시작](webjobs-sdk-get-started.md)합니다. 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 버전

이들은 3 버전 간의 주요 차이점입니다. *x* 과 버전 2. *x* WebJobs SDK의:

* 버전 3입니다. *x* .NET Core에 대 한 지원이 추가 되었습니다.
* 버전 3입니다. *x*를 명시적으로 WebJobs SDK에 필요한 저장소 바인딩 확장을 설치 해야 합니다. 버전 2입니다. *x*, Storage 바인딩 SDK에 포함 되었습니다.
* .NET Core 용 도구 visual Studio (3. *x*)에서.NET Framework에 대 한 도구와 다른 프로젝트 (2. *x*) 프로젝트. 자세한 내용은 참조 하세요 [개발 및 Visual Studio-Azure App Service를 사용 하 여 WebJobs 배포](webjobs-dotnet-deploy-vs.md)합니다.

가능 하면 두 버전 3에 대 한 예제가 제공 됩니다. *x* 과 버전 2. *x*합니다.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 는 WebJobs SDK를 기반으로 하 고이 문서에서는 일부 항목에 대 한 Azure Functions 설명서에 대 한 링크를 제공 합니다. Functions 및 WebJobs SDK 간의 이러한 차이 note:
> * Azure Functions 버전 2입니다. *x* WebJobs SDK 버전 3에 해당 합니다. *x*, 및 Azure Functions 1. *x* WebJobs SDK 2에 해당 합니다. *x*합니다. 소스 코드 리포지토리는 WebJobs SDK 번호 매기기를 사용 합니다.
> * Azure Functions에 대 한 샘플 코드 C# 필요가 점을 제외 하 고 클래스 라이브러리는 WebJobs SDK 코드와는 `FunctionName` WebJobs SDK 프로젝트에는 특성입니다.
> * 일부 바인딩 형식은 HTTP (Webhook) 및 Event Grid (HTTP 기반의)와 같은 함수 에서만 지원 됩니다.
>
> 자세한 내용은 [WebJobs SDK과 Azure Functions 비교](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요.

## <a name="webjobs-host"></a>WebJobs 호스트

호스트는 함수의 런타임 컨테이너입니다.  트리거를 수신 대기하고 함수를 호출합니다. 버전 3입니다. *x*, 호스트의 구현인 `IHost`합니다. 버전 2입니다. *x*를 사용 하는 `JobHost` 개체입니다. 사용자는 코드에서 호스트 인스턴스를 만들고 동작을 사용자 지정하는 코드를 작성하게 됩니다.

이것이 WebJobs SDK를 사용 하 여 직접 및 사용할 때 직접 Azure Functions를 통해 중요 한 차이점입니다. Azure Functions에서 서비스 호스트를 제어 하 고 코드를 작성 하 여 호스트를 사용자 지정할 수 없습니다. Azure Functions host.json 파일의 설정을 통해 호스트 동작을 사용자 지정할 수 있습니다. 이러한 설정은 코드가 아닌 문자열 및이 수행할 수 있는 사용자 지정의 종류를 제한 합니다.

### <a name="host-connection-strings"></a>호스트 연결 문자열

WebJobs SDK는 Azure에서 실행할 때 local.settings.json 파일을 로컬로 실행할 때 또는 WebJob의 환경에서 Azure Storage 및 Azure Service Bus 연결 문자열에 대 한 찾습니다. 기본적으로 저장소 연결 문자열 설정을 `AzureWebJobsStorage` 필요 합니다.  

버전 2입니다. *x* SDK의 이러한 연결 문자열에 대 한 고유한 이름을 사용 하거나 다른 곳에서 저장할 수 있습니다. 이름을 사용 하 여 코드에서 설정할 수는 [ `JobHostConfiguration` ]다음과 같이 합니다.

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

때문에 버전 3입니다. *x* 기본.NET Core 구성 Api를 사용 하 여 연결 문자열 이름을 변경 하려면 API는 없습니다.

### <a name="host-development-settings"></a>호스트 개발 설정

로컬 개발을 보다 효율적으로 수행하기 위해 개발 모드에서 호스트를 실행할 수 있습니다. 개발 모드에서 실행할 때 변경 된 설정 중 일부를 다음과 같습니다.

| 자산 | 개발 설정 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | 로그 출력을 최대화하는 `TraceLevel.Verbose`. |
| `Queues.MaxPollingInterval`  | 값이 작으면 큐 메서드가 즉시 트리거됩니다.  |
| `Singleton.ListenerLockPeriod` | 신속한 반복 개발에 도움이 되는 시간은 15초입니다. |

개발 모드를 사용 하도록 설정 하는 프로세스는 SDK 버전에 따라 달라 집니다. 

#### <a name="version-3x"></a>버전 3입니다. *x*

버전 3입니다. *x* 표준 ASP.NET Core Api를 사용 합니다. 호출 된 [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) 메서드를 [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스. 라는 문자열을 전달 `development`이 예제와 같이:

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>버전 2입니다. *x*

`JobHostConfiguration` 클래스에는 개발 모드를 사용하도록 설정하는 `UseDevelopmentSettings` 메서드가 있습니다.  다음 예제에서는 개발 설정을 사용하는 방법을 보여줍니다. 되도록 `config.IsDevelopment` 반환 `true` 로컬로 실행 될 때 라는 로컬 환경 변수 설정 `AzureWebJobsEnv` 값을 사용 하 여 `Development`입니다.

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

### <a name="jobhost-servicepointmanager-settings"></a>동시 연결 관리 (버전 2. *x*)

버전 3입니다. *x*, 무한 연결에 기본 연결 제한 값입니다. 어떤 이유로이 제한을 변경 해야 하는 경우 사용할 수 있습니다 합니다 [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) 의 속성을 [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) 클래스입니다.

버전 2입니다. *x*를 사용 하 여 호스트에 대 한 동시 연결 수를 제어 합니다 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2. *x*, WebJobs 호스트를 시작 하기 전에 기본값인 2에서이 값을 늘려야 합니다.

사용 하 여 함수에서 수행한 모든 나가는 HTTP 요청 `HttpClient` 통과 `ServicePointManager`합니다. 에 설정 된 값에 도달 하면 `DefaultConnectionLimit`, `ServicePointManager` 보내기 전에 큐 요청을 시작 합니다. `DefaultConnectionLimit`가 2로 설정되었고 코드에서 HTTP 요청 1,000개를 만든다고 가정해 봅시다. 처음에는 OS까지 전달되는 요청이 2개밖에 없습니다. 나머지 998개는 공간이 생길 때까지 큐에서 대기합니다. 즉, 프로그램 `HttpClient` 요청 보낸 적 OS 대상 서버에 있지만 요청 된 표시 되기 때문에 시간 초과 될 수 있습니다. 이와 같은 이유로 로컬 `HttpClient`가 요청을 완료하는 데 10초가 걸리지만 서비스가 200ms 후에 모든 요청을 반환하는 이상한 동작이 관찰될 수 있습니다. 

ASP.NET 응용 프로그램에 대 한 기본값은 `Int32.MaxValue`를 기본 이상의 App Service 계획에서 실행 되는 WebJobs에서 잘 작동 하는 일을 할입니다. WebJobs는 일반적으로 Always On 설정 해야 하 고 Basic 이상 App Service 계획 에서만 지원 되는 키를 누릅니다.

WebJob이 무료 또는 공유 App Service 계획에서 실행되는 경우 애플리케이션이 App Service 샌드박스의 제한을 받으며, 현재 [연결 제한은 300개](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)입니다. 바인딩되지 않은 제한 된 `ServicePointManager`, 가능성이 샌드박스 연결 임계값에 도달할 수 하 여 사이트가 종료 됩니다. 이 경우 `DefaultConnectionLimit`를 50 또는 100처럼 약간 낮추면 이와 같은 상황을 방지하면서도 충분한 처리량을 계속 제공할 수 있습니다.

이 설정은 HTTP 요청을 만들기 전에 구성해야 합니다. 이러한 이유로 WebJobs 호스트 설정을 조정 하지 않아야 자동으로 합니다. 예기치 않은 동작이 발생할 수 있는 호스트 시작 되기 전에 발생 하는 HTTP 요청 있을 수 있습니다. 즉시 값을 설정 하는 가장 좋은 방법은 프로그램 `Main` 메서드를 초기화 하기 전에 `JobHost`여기에 표시 된 것 처럼:

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

함수는 공용 메서드여야 및 트리거 특성 하나 있어야 합니다. 또는 [ `NoAutomaticTrigger` ](#manual-triggers) 특성입니다.

### <a name="automatic-triggers"></a>자동 트리거

자동 트리거는 이벤트에 대한 응답으로 함수를 호출합니다. 이 예제에서는 Azure 큐 저장소에 추가 된 메시지에 의해 트리거되는 함수를 고려 합니다. Azure Blob storage에서 blob을 읽는 중 응답:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

합니다 `QueueTrigger` 특성에는 큐 메시지에 나타날 때마다 함수를 호출 하도록 런타임에 지시 합니다 `myqueue-items` 큐입니다. `Blob` 특성에서 blob을 읽는 큐 메시지를 사용 하려면 런타임에 *샘플 workitems* 컨테이너입니다. 함수에 전달 된 큐 메시지의 콘텐츠는 `myQueueItem` 매개 변수는 blob의 이름입니다.


### <a name="manual-triggers"></a>수동 트리거

수동으로 함수 트리거를 사용 하 여는 `NoAutomaticTrigger` 특성을 다음과 같이 합니다.

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

수동으로 함수를 트리거하는 프로세스는 SDK 버전에 따라 달라 집니다.

#### <a name="version-3x"></a>버전 3입니다. *x*

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

#### <a name="version-2x"></a>버전 2입니다. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>입력 및 출력 바인딩

입력 바인딩은 Azure 또는 타사 서비스의 데이터를 코드에 사용할 수 있는 선언적 방법을 제공합니다. 출력 바인딩은 데이터를 업데이트하는 방법을 제공합니다. 합니다 [시작](webjobs-sdk-get-started.md) 문서에서는 각각의 예제를 보여 줍니다.

메서드 반환 값으로 특성을 적용 하 여 출력 바인딩에 메서드 반환 값을 사용할 수 있습니다. 예제를 참조 하세요 [값을 반환 하는 Azure 함수를 사용 하 여](../azure-functions/functions-bindings-return-value.md)입니다.

## <a name="binding-types"></a>바인딩 형식

설치 및 바인딩 형식을 관리에 대 한 프로세스 버전 3의 사용 여부에 따라 달라 집니다. *x* 버전 2. *x* SDK입니다. 해당 바인딩 형식의 Azure Functions의 "패키지" 섹션에서 특정 바인딩 형식에 대 한 설치를 위해 패키지를 찾을 수 있습니다 [참조 문서](#binding-reference-information)합니다. 예외가 파일 트리거 및 바인딩을 (로컬 파일 시스템의 경우), Azure Functions에서 지원 되지 않는 경우

#### <a name="version-3x"></a>버전 3입니다. *x*

버전 3입니다. *x*, storage 바인딩에 포함 된를 `Microsoft.Azure.WebJobs.Extensions.Storage` 패키지 있습니다. 호출을 `AddAzureStorage` 확장 메서드는 `ConfigureWebJobs` 메서드를 다음과 같이 합니다.

```cs
static void Main()
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
        host.Run();
    }
}
```

다른 트리거 및 바인딩 형식을 사용하려면 해당 항목을 포함하는 NuGet 패키지를 설치하고 확장에서 구현된 `Add<binding>` 확장 메서드를 호출합니다. Azure Cosmos DB 바인딩을 사용 하려는 경우 설치 하는 예를 들어 `Microsoft.Azure.WebJobs.Extensions.CosmosDB` 호출 `AddCosmosDB`, 다음과 같은:

```cs
static void Main()
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
        host.Run();
    }
}
```

핵심 서비스의 일부인 타이머 트리거 또는 파일 바인딩을 사용하려면 `AddTimers` 또는 `AddFiles` 확장 메서드를 각각 호출합니다.

#### <a name="version-2x"></a>버전 2입니다. *x*

이러한 트리거 및 바인딩 형식은 버전 2에에서 포함 됩니다. *x* 의 `Microsoft.Azure.WebJobs` 패키지:

* Blob 저장소
* 큐 저장소
* 테이블 저장소

다른 트리거 및 바인딩 형식을 사용하려면 사용하려는 트리거 및 바인딩 형식이 포함된 NuGet 패키지를 설치하고 `JobHostConfiguration` 개체에서 `Use<binding>` 메서드를 호출합니다. 타이머 트리거를 사용 하려는 경우 설치 하는 예를 들어 `Microsoft.Azure.WebJobs.Extensions` 호출 `UseTimers` 에 `Main` 메서드를 다음과 같이 합니다.

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

프로세스에 대 한 바인딩에 [ `ExecutionContext` ] SDK 버전에 따라 달라 집니다.

#### <a name="version-3x"></a>버전 3입니다. *x*

호출을 `AddExecutionContextBinding` 확장 메서드는 `ConfigureWebJobs` 메서드를 다음과 같이 합니다.

```cs
static void Main()
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
        host.Run();
    }
}
```

#### <a name="version-2x"></a>버전 2입니다. *x*

앞에서 언급한 `Microsoft.Azure.WebJobs.Extensions` 패키지는 `UseCore` 메서드를 호출하여 등록할 수 있는 특별한 바인딩 형식을 제공합니다. 이 바인딩을 정의할 수는 [ `ExecutionContext` ] 다음과 같이 사용할 수 있는 함수 시그니처의 매개 변수:

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

일부 트리거 및 바인딩 동작을 구성할 수 있습니다. 구성 하는 프로세스는 SDK 버전에 따라 달라 집니다.

* **버전 3입니다. *x*:** 구성을 설정할 때 합니다 `Add<Binding>` 메서드는 `ConfigureWebJobs`.
* **버전 2입니다. *x*:** 에 전달 하는 구성 개체의 속성을 설정 하 여 구성을 설정할 `JobHost`합니다.

이러한 바인딩 관련 설정은의 설정에 해당 하는 [host.json 프로젝트 파일](../azure-functions/functions-host-json.md) Azure Functions에서.

다음 바인딩을 구성할 수 있습니다.

* [Azure CosmosDB 트리거](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs 트리거](#event-hubs-trigger-configuration-version-3x)
* Queue Storage 트리거
* [SendGrid 바인딩](#sendgrid-binding-configuration-version-3x)
* [Service Bus 트리거](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 트리거 구성 (버전 3. *x*)

이 예제에서는 Azure Cosmos DB 트리거를 구성 하는 방법을 보여 줍니다.

```cs
static void Main()
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

        host.Run();
    }
}
```

자세한 내용은 참조는 [Azure cosmos Db 바인딩](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) 문서.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs 트리거 구성 (버전 3. *x*)

이 예제에서는 Event Hubs 트리거를 구성 하는 방법을 보여 줍니다.

```cs
static void Main()
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

        host.Run();
    }
}
```

자세한 내용은 참조는 [Event Hubs 바인딩](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) 문서.

### <a name="queue-storage-trigger-configuration"></a>큐 저장소 트리거 구성

이 예제에서는 Queue storage 트리거를 구성 하는 방법을 보여 줍니다.

#### <a name="version-3x"></a>버전 3입니다. *x*

```cs
static void Main()
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

        host.Run();
    }
}
```

자세한 내용은 참조는 [storage 바인딩은 큐](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) 문서.

#### <a name="version-2x"></a>버전 2입니다. *x*

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

자세한 내용은 참조는 [host.json v1.x 참조](../azure-functions/functions-host-json-v1.md#queues)합니다.

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 바인딩 구성 (버전 3. *x*)

SendGrid를 구성 하는 방법을 보여 주는이 예제 출력 바인딩:

```cs
static void Main()
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

        host.Run();
    }
}
```

자세한 내용은 참조는 [SendGrid 바인딩](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) 문서.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus 트리거 구성 (버전 3. *x*)

이 예제에서는 Service Bus 트리거를 구성 하는 방법을 보여 줍니다.

```cs
static void Main()
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

        host.Run();
    }
}
```

자세한 내용은 참조는 [Service Bus 바인딩](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) 문서.

### <a name="configuration-for-other-bindings"></a>다른 바인딩의 구성

일부 트리거 및 바인딩 형식은 고유한 사용자 지정 구성 형식을 정의합니다. 예를 들어 파일 트리거는 이러한 예제와 같이 모니터링할 루트 경로 지정할 수 있습니다.

#### <a name="version-3x"></a>버전 3입니다. *x*

```cs
static void Main()
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

        host.Run();
    }
}
```

#### <a name="version-2x"></a>버전 2입니다. *x*

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

큐 이름, blob 이름 또는 컨테이너 또는 테이블 이름을 하드 코딩 하는 것이 아니라 코드 지정 하려는 경우가 있습니다. 예를 들어 구성 파일 또는 환경 변수에서 `QueueTrigger` 특성에 대한 큐 이름을 지정할 수 있습니다.

전달 하 여 수행할 수 있습니다는 `NameResolver` 개체에 `JobHostConfiguration` 개체입니다. 트리거 또는 바인딩 특성 생성자 매개 변수에 자리 표시자를 포함하면 `NameResolver` 코드가 해당 자리 표시자 위치에 사용할 실제 값을 제공합니다. 자리 표시자 백분율 (%)로 묶어서 식별 기호를 다음과 같이 합니다.

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

이 코드를 사용하면 테스트 환경에 `logqueuetest`라는 큐를 사용하고 프로덕션 환경에 `logqueueprod`라는 큐를 사용할 수 있습니다. 하드 코드된 큐 이름 대신 `appSettings` 컬렉션의 항목 이름을 지정합니다.

기본값이 `NameResolver` 적용 됩니다는 사용자 지정을 제공 하지 않습니다. 기본값은 앱 설정이나 환경 변수에서 가져옵니다.

프로그램 `NameResolver` 클래스에서 큐 이름을 가져옵니다 `appSettings`다음과 같이 합니다.

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>버전 3입니다. *x*

종속성 주입을 사용 하 여 해결 프로그램을 구성 합니다. 이러한 샘플에는 다음 `using` 문이 필요합니다.

```cs
using Microsoft.Extensions.DependencyInjection;
```

호출 하 여 해결 프로그램을 추가 합니다 [ `ConfigureServices` ] 확장 메서드를 [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder)이 예제와 같이:

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

#### <a name="version-2x"></a>버전 2입니다. *x*

전달 하 `NameResolver` 클래스를 `JobHost` 개체를 다음과 같이 합니다.

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

같은 바인딩 특성을 사용 하기 전에 함수에서 일부 작업을 수행 해야 하는 경우 `Queue`, `Blob`, 또는 `Table`를 사용할 수는 `IBinder` 인터페이스입니다.

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

Azure Functions 설명서는 각 바인딩 형식에 대 한 참조 정보를 제공 합니다. 각 바인딩 참조 문서에서 다음 정보를 알려드립니다. (이 예제에서는 저장소 큐에 기반 합니다.)

* [패키지](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x)합니다. WebJobs SDK 프로젝트에는 바인딩에 대 한 지원을 포함 하도록 설치 해야 하는 패키지입니다.
* [예제](../azure-functions/functions-bindings-storage-queue.md#trigger---example)합니다. 코드 샘플입니다. C# 클래스 라이브러리 예제 WebJobs SDK에 적용 됩니다. 생략 된 `FunctionName` 특성입니다.
* [특성](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes)합니다. 바인딩 형식에 사용할 특성입니다.
* [구성](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration)합니다. 특성 속성 및 생성자 매개 변수를 설명 합니다.
* [사용 현황](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). 형식에 바인딩할 수 있습니다 및 바인딩의 작동 방식에 대 한 정보. 예: 폴링 알고리즘, 포이즌 큐 처리.
  
바인딩 참조 문서를 목록에서 "지원 되는 바인딩" 참조를 [트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md#supported-bindings) Azure Functions에 대 한 문서. 이 목록의 HTTP, 웹 후크를 및 Event Grid 바인딩은 WebJobs SDK가 아닌 Azure Functions 에서만 지원 됩니다.

## <a name="disable-attribute"></a>Disable 특성 

합니다 [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 함수 여부를 제어할 특성 수를 트리거할 수 있습니다. 

다음 예의 경우 앱 설정 `Disable_TestJob` 값이 `1` 또는 `True` (대/소문자 구분), 함수가 실행 되지 않습니다. 이 경우 런타임에서 *Function 'Functions.TestJob' is disabled* 로그 메시지를 작성합니다.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Azure portal에서 앱 설정 값을 변경 하면 WebJob 새 설정을 선택 하려면 다시 시작 합니다.

매개 변수, 메서드 또는 클래스 수준에서 특성을 선언할 수 있습니다. 설정 이름에 바인딩 식을 포함할 수도 있습니다.

## <a name="timeout-attribute"></a>Timeout 특성

합니다 [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 특성을 사용 하면 지정된 된 기간 내에 끝나지 않으면 경우 취소 하는 함수입니다. 다음 예제에서는 시간 제한 특성이 없으면 1 일에 대 한 함수 실행 됩니다. 시간 제한 하면 15 초가 지난 후 취소할 함수.

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

클래스 또는 메서드 수준에서 Timeout 특성을 적용할 수 있으며 사용 하 여 전역 시간 제한을 지정할 수 있습니다 `JobHostConfiguration.FunctionTimeout`합니다. 클래스 수준 또는 메서드 수준 시간 제한을 전역 시간 제한을 재정의합니다.

## <a name="singleton-attribute"></a>Singleton 특성

합니다 [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 특성 하면 함수의 인스턴스를 하나만 호스트 웹 앱의 인스턴스가 여러 개 있는 경우에 실행 합니다. 사용 하 여 이렇게 [분산 잠금](#viewing-lease-blobs)합니다.

이 예의 단일 인스턴스만 `ProcessImage` 함수는 지정된 된 시간에 실행 합니다.

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

일부 트리거는 동시성 관리를 기본적으로 지원합니다.

* **QueueTrigger**. `JobHostConfiguration.Queues.BatchSize`을 `1`로 설정합니다.
* **ServiceBusTrigger**. `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls`을 `1`로 설정합니다.
* **FileTrigger**. `FileProcessor.MaxDegreeOfParallelism`을 `1`로 설정합니다.

이러한 설정을 사용하여 함수가 단일 인스턴스에서 싱글톤으로 실행되도록 할 수 있습니다. 웹 앱이 여러 인스턴스로 확장 하는 경우 함수의 단일 인스턴스만 실행 되 고 있는지을 보장 하려면 함수에서 수신기 수준 싱글톤 잠금을 적용 (`[Singleton(Mode = SingletonMode.Listener)]`). 수신기 잠금은 JobHost 시작 하는 경우. 확장된 인스턴스 3개가 동시에 시작되면 인스턴스 중 하나만 잠금을 획득하고 하나의 수신기만 시작됩니다.

### <a name="scope-values"></a>범위 값

지정할 수 있습니다는 *식을/값 범위* singleton에서. 식/값은 특정 범위에서 함수의 모든 실행을 직렬화할지 확인 합니다. 이러한 방식으로 더 세분화 된 잠금을 구현 요구 사항에 따라 다른 호출을 직렬화 하는 동안 일부 함수에 대 한 병렬 처리 수준을 허용할 수 있습니다. 예를 들어, 다음 코드에서 scope 식 바인딩되는 `Region` 들어오는 메시지의 값입니다. 동부, 동부, 및 각각 서 부 지역에 세 개의 메시지를 포함 하는 큐, 서 부 동부의 병렬로 실행 될 영역을 사용 하 여 메시지를 동안 동부 순차적으로 실행 되는 지역에 있는 메시지입니다.

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

잠금에 대 한 기본 범위는 `SingletonScope.Function`, 정규화 된 함수 이름에 연결 되어 잠금 범위 (blob 임대 경로)를 의미 합니다. 함수에서 잠그려면, 지정 `SingletonScope.Host` 일치 하는 동시에 실행 하지 않으려는 모든 함수는 범위 ID 이름을 사용 합니다. 다음 예제에서는 `AddItem` 또는 `RemoveItem`의 인스턴스가 한 번에 하나씩 실행됩니다.

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

WebJobs SDK는 분산 잠금을 구현하기 위해 백그라운드에서 [Azure blob 임대](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)를 사용합니다. Singleton에서 사용 하는 임대 blob에서 찾을 수 있습니다 합니다 `azure-webjobs-host` 컨테이너에는 `AzureWebJobsStorage` "locks" 경로 아래 저장소 계정입니다. 예를 들어 앞에서 살펴본 첫 번째 `ProcessImage` 예제의 임대 BLOB 경로가 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`일 수 있습니다. 모든 경로는 JobHost ID(이 예에서는 061851c758f04938a4426aa9ab3869c0)를 포함합니다.

## <a name="async-functions"></a>비동기 함수

비동기 함수를 코딩 하는 방법에 대 한 내용은 참조는 [Azure Functions 설명서](../azure-functions/functions-dotnet-class-library.md#async)합니다.

## <a name="cancellation-tokens"></a>취소 토큰

취소 토큰을 처리하는 방법은 [취소 토큰 및 정상 종료](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)에 대한 Azure Functions 설명서를 참조하세요.

## <a name="multiple-instances"></a>여러 인스턴스

웹앱이 여러 인스턴스에서 실행되는 경우 연속적인 WebJob이 각 인스턴스에서 실행되고, 트리거를 기다렸다가 함수를 호출합니다. 더 많은 인스턴스로 확장하여 더 많은 부하를 처리할 수 있도록, 작업을 효율적으로 여러 인스턴스에서 공동으로 공유하도록 설계된 다양한 트리거 바인딩이 있습니다.

큐 및 blob 트리거를 자동으로 큐 메시지를 처리 하는 함수를 방지 하거나 두 번 이상; blob 함수를 idempotent 필요가 없습니다.

타이머 트리거는 자동으로 타이머의 한 인스턴스만 실행되도록 보장하므로 예약된 특정 시간에 오직 하나의 함수 인스턴스만 실행됩니다.

호스트 웹 앱의 여러 인스턴스가 필요한 경우에 함수 실행의 인스턴스를 하나만 있는지 확인 하려는 경우, 사용할 수는 [ `Singleton` ](#singleton-attribute) 특성입니다.

## <a name="filters"></a>필터

함수 필터(미리 보기)는 사용자 고유의 논리로 WebJobs 실행 파이프라인을 사용자 지정하는 방법을 제공합니다. 필터는 비슷합니다 [ASP.NET Core 필터](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)합니다. 함수 또는 클래스에 적용 되는 선언적 특성으로 구현할 수 있습니다. 자세한 내용은 [함수 필터](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

ASP.NET 용으로 개발 된 로깅 프레임 워크를 사용 하는 것이 좋습니다. 합니다 [시작](webjobs-sdk-get-started.md) 문서에 사용 하는 방법을 보여 줍니다. 

### <a name="log-filtering"></a>로그 필터링

`ILogger` 인스턴스로 만든 모든 로그에는 `Category` 및 `Level`이 연결됩니다. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) 열거형 이며 정수 코드는 상대적 중요도 나타냅니다.

|LogLevel    |코드|
|------------|---|
|추적       | 0 |
|디버그       | 1 |
|정보 | 2 |
|Warning     | 3 |
|오류       | 4 |
|중요    | 5 |
|없음        | 6 |

필터링 할 수 있습니다 독립적으로 없습니다 각 범주를 특정 [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel)합니다. 예를 들어 Blob 트리거 처리에 대한 모든 로그를 보고 싶지만 그 외에는 `Error` 이상만 보고 싶은 경우가 있습니다.

#### <a name="version-3x"></a>버전 3입니다. *x*

버전 3입니다. *x* SDK의.NET Core로 빌드되는 필터링에 의존 합니다. `LogCategories` 클래스를 사용하여 특정 함수, 트리거 또는 사용자의 범주를 정의할 수 있습니다. 또한 정의 특정 호스트 상태에 대 한 필터와 같은 `Startup` 고 `Results`입니다. 이 옵션을 사용 하면 로깅 출력을 미세 조정할 수 있습니다. 정의된 범주 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

`LogCategories`에는 다음 using 문이 필요합니다.

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

다음 예제에서는 기본적으로 모든 로그를 필터링 하는 필터를 작성 합니다 `Warning` 수준입니다. 합니다 `Function` 하 고 `results` 범주 (같음 `Host.Results` 버전 2에서에서. *x*)에서 필터링 되는 `Error` 수준입니다. 이 필터는 현재 범주를 `LogCategories` 인스턴스의 모든 등록된 수준과 비교하여 가장 긴 일치 항목을 선택합니다. 즉 합니다 `Debug` 수준에 대 한 등록 `Host.Triggers` 일치 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

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

#### <a name="version-2x"></a>버전 2입니다. *x*

버전 2입니다. *x* SDK의 사용을 `LogCategoryFilter` 필터링을 제어 하는 클래스입니다. `LogCategoryFilter` 에 `Default` 속성의 초기 값을 사용 하 여 `Information`, 즉에서 모든 메시지를 `Information`, `Warning`, `Error`, 또는 `Critical` 수준 위반은 기록 되지만에 메시지를 `Debug` 또는 `Trace` 수준 필터링 됩니다.

와 마찬가지로 `LogCategories` 버전 3. *x*, `CategoryLevels` 속성을 사용 하면 로깅 출력을 미세 조정할 수 있도록 특정 범주에 대 한 로그 수준을 지정할 수 있습니다. `CategoryLevels` 디렉터리 내에 일치 항목이 없는 경우 메시지를 필터링할 것인지 결정할 때 필터가 `Default` 값으로 대체됩니다.

다음 예제는 기본적으로 `Warning` 수준에서 모든 로그를 필터링하는 필터를 작성합니다. `Function` 하 고 `Host.Results` 범주에서 필터링 된는 `Error` 수준입니다. `LogCategoryFilter`는 현재 범주를 모든 등록된 `CategoryLevels`와 비교하여 가장 긴 일치 항목을 선택합니다. 하므로 `Debug` 수준에 대 한 등록 `Host.Triggers` 일치 `Host.Triggers.Queue` 또는 `Host.Triggers.Blob`합니다. 따라서 각각을 추가하지 않고도 더 광범위한 범주를 제어할 수 있습니다.

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

에 대 한 사용자 지정 원격 분석을 구현 하기 위한 프로세스 [Application Insights](../azure-monitor/app/app-insights-overview.md) SDK 버전에 따라 달라 집니다. Application Insights를 구성하는 방법을 알아보려면 [Application Insights 로깅 추가](webjobs-sdk-get-started.md#add-application-insights-logging)를 참조하세요.

#### <a name="version-3x"></a>버전 3입니다. *x*

때문에 버전 3입니다. *x* WebJobs SDK의 사용자 지정 원격 분석 팩터리를 제네릭 호스트는 더 이상 제공 하는.NET Core에 의존 합니다. 하지만 종속성 주입을 사용 하 여 사용자 지정 원격 분석 파이프라인에 추가할 수 있습니다. 이 섹션의 예제에는 다음 `using` 문이 필요합니다.

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
static void Main()
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

        host.Run();
    }
}
```

[`TelemetryConfiguration`]이 생성되면 등록된 모든 [`ITelemetryInitializer`] 유형이 포함됩니다. 자세한 내용은 참조 하세요 [사용자 지정 이벤트 및 메트릭 용 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)합니다.

버전 3입니다. *x*, 플러시를 더 이상 합니다 [ `TelemetryClient` ] 호스트가 중지 되 면 합니다. .NET Core 종속성 주입 시스템은 [`TelemetryClient`]를 플러시하는 등록된 `ApplicationInsightsLoggerProvider`를 자동으로 제거합니다.

#### <a name="version-2x"></a>버전 2입니다. *x*

버전 2입니다. *x*서 [ `TelemetryClient` ] WebJobs SDK를 사용 하는 Application Insights 공급자가 내부적으로 만든 [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)합니다. Application Insights 엔드포인트를 사용할 수 없거나 들어오는 요청을 제한하는 경우 이 채널은 [웹앱의 파일 시스템에 요청을 저장해 두었다가 나중에 다시 전송](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)합니다.

[`TelemetryClient`]는 `ITelemetryClientFactory`를 구현하는 클래스에서 생성합니다. 기본적으로 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)입니다.

Application Insights 파이프라인의 특정 부분을 수정하려는 경우 사용자 고유의 `ITelemetryClientFactory`를 제공하면 호스트가 해당 클래스를 사용하여 [`TelemetryClient`]를 생성합니다. 예를 들어이 코드 재정의 `DefaultTelemetryClientFactory` 속성을 수정 하려면 `ServerTelemetryChannel`:

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

합니다 `SamplingPercentageEstimatorSettings` 개체를 구성 [적응 샘플링](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)합니다. 이 특정 대량 볼륨 시나리오에서 Application Insights 원격 분석 데이터의 선택한 하위 집합에서 서버로 보내는 것을 의미 합니다.

원격 분석 팩터리를 만든 후 전달할에서 Application Insights 로깅 공급자:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 다음 단계

이 문서에서는 WebJobs SDK를 사용 하 여 작업에 대 한 일반적인 시나리오를 처리 하는 방법을 보여주는 코드 조각을 제공 했습니다. 전체 샘플은 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples)를 참조하세요.

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
