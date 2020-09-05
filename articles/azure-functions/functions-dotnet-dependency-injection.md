---
title: .NET Azure Functions에서 종속성 주입 사용
description: .NET 함수의 서비스를 등록하고 사용할 때 종속성 주입을 사용하는 방법에 대해 알아보기
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 6badcedba7fa1e1b605fc5553e5c6eed52c4203b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182074"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 종속성 주입 사용

Azure Functions는 클래스와 해당 종속성 간에 [IoC(제어 반전)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 구현하는 기법인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.

- Azure Functions의 종속성 주입은 .NET Core 종속성 주입 기능을 기반으로 합니다. [.NET Core 종속성 주입](/aspnet/core/fundamentals/dependency-injection)에 대해 잘 알고 있는 것이 좋습니다. Azure Functions의 사용 계획에서 종속성을 재정의하는 방법과 관련 구성 값을 읽는 방법에는 차이가 있습니다.

- 종속성 주입에 대한 지원은 Azure Functions 2.x부터 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

종속성 주입을 사용하려면 먼저 다음 NuGet 패키지를 설치해야 합니다.

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 패키지 버전 1.0.28 이상

## <a name="register-services"></a>서비스 등록

서비스를 등록하려면 구성 요소를 구성하고 `IFunctionsHostBuilder` 인스턴스에 추가하세요.  Azure Functions 호스트는 `IFunctionsHostBuilder` 인스턴스를 만들고 메서드에 직접 전달합니다.

메서드를 등록하려면 시작 시 사용되는 형식 이름을 지정하는 `FunctionsStartup` 어셈블리 특성을 추가합니다.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

이 예제에서는 시작 시 `HttpClient`를 등록하는 데 필요한 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 패키지를 사용합니다.

### <a name="caveats"></a>제한 사항

런타임이 시작 클래스를 처리하기 전후로 일련의 등록 단계가 실행됩니다. 따라서 다음 사항에 유의하세요.

- *시작 클래스는 설치 및 등록에만 사용할 수 있습니다.* 시작 시 등록되는 서비스를 시작 프로세스 중에 사용하지 마세요. 예를 들어 시작 시 등록되는 로거에 메시지를 로깅하려고 하지 마세요. 등록 프로세스의 이 시점에 서비스를 사용하는 것은 너무 이릅니다. `Configure` 메서드가 실행되면 Functions 런타임이 추가 종속성을 계속 등록하여 서비스가 작동하는 방식에 영향을 줄 수 있습니다.

- *종속성 주입 컨테이너에는 명시적으로 등록된 유형만 저장됩니다*. 주입 가능한 형식으로 제공되는 유일한 서비스는 `Configure` 메서드에 설정됩니다. 따라서 `BindingContext` 및 `ExecutionContext` 같은 Functions 관련 형식은 설치 중에 또는 주입 가능한 형식으로 사용할 수 없습니다.

## <a name="use-injected-dependencies"></a>주입된 종속성 사용

생성자 주입은 함수에서 종속성을 사용할 수 있도록 하는 데 사용됩니다. 생성자 주입을 사용 하려면 삽입 된 서비스 또는 함수 클래스에 대해 정적 클래스를 사용 하지 않아야 합니다.

다음 샘플에서는 `IMyService` 및 `HttpClient` 종속성이 HTTP로 트리거되는 함수에 삽입되는 방법을 보여 줍니다.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, MyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

이 예제에서는 시작 시 `HttpClient`를 등록하는 데 필요한 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 패키지를 사용합니다.

## <a name="service-lifetimes"></a>서비스 수명

Azure Functions 앱은 [ASP.NET 종속성 주입](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)과 동일한 서비스 수명을 제공합니다. Functions 앱의 경우 서로 다른 서비스 수명이 다음과 같이 동작합니다.

- **임시**: 임시 서비스는 서비스가 요청될 때마다 생성됩니다.
- **범위**: 범위 서비스 수명은 함수 실행 수명과 일치합니다. 범위 서비스는 1회 실행당 한 번 생성됩니다. 실행 중에 해당 서비스에 대한 이후 요청은 기존 서비스 인스턴스를 다시 사용합니다.
- **싱글톤**: 싱글톤 서비스 수명은 호스트 수명과 일치하며 해당 인스턴스에서 함수를 실행할 때 다시 사용됩니다. `DocumentClient` 또는 `HttpClient` 인스턴스 같은 연결 및 클라이언트에는 싱글톤 수명 서비스가 권장됩니다.

GitHub에서 [다양한 서비스 수명 샘플](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes)을 보거나 다운로드하세요.

## <a name="logging-services"></a>로깅 서비스

사용자 고유의 로깅 공급자가 필요한 경우에는 사용자 지정 유형을 Microsoft의 인스턴스로 등록 합니다 .이 인스턴스는 Microsoft 확장명이. n a m a [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) NuGet 패키지를 통해 사용할 수 있습니다. [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)

Application Insights는 Azure Functions에 의해 자동으로 추가됩니다.

> [!WARNING]
> - `AddApplicationInsightsTelemetry()`는 환경에서 제공하는 서비스와 충돌하는 서비스를 등록하므로 서비스 컬렉션에 추가하지 마세요.
> - 기본 제공 Application Insights 기능을 사용하는 경우 자체 `TelemetryConfiguration` 또는 `TelemetryClient`를 등록하지 마세요. 자체 `TelemetryClient` 인스턴스를 구성해야 하는 경우에는 [Azure Functions 모니터링](./functions-monitoring.md#version-2x-and-later-2)에 나와 있는 것처럼 주입된 `TelemetryConfiguration`을 통해 만듭니다.

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> 및 ILoggerFactory

호스트는 `ILogger<T>` 및 `ILoggerFactory` 서비스를 생성자에 삽입 합니다.  그러나 기본적으로 이러한 새 로깅 필터는 함수 로그에서 필터링 됩니다.  `host.json`추가 필터 및 범주를 옵트인 (opt in) 하도록 파일을 수정 해야 합니다.

다음 예제에서는 `ILogger<HttpTrigger>` 호스트에 노출 되는 로그가 있는를 추가 하는 방법을 보여 줍니다.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

다음 예제 `host.json` 파일은 로그 필터를 추가 합니다.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>함수 앱 제공 서비스

함수 호스트는 많은 서비스를 등록합니다. 애플리케이션에서 종속성으로 사용하기에 안전한 서비스는 다음과 같습니다.

|서비스 유형|수명|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|싱글톤|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|싱글톤|호스트 인스턴스의 ID 제공 담당|

종속성으로 사용하려는 다른 서비스가 있는 경우 [문제를 만들고 GitHub에서 제안합니다](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>호스트 서비스 재정의

호스트에서 제공하는 서비스 재정의는 현재 지원되지 않습니다.  재정의하려는 서비스가 있는 경우 [문제를 만들고 GitHub에서 제안합니다](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>옵션 및 설정 사용

[앱 설정](./functions-how-to-use-azure-function-app-settings.md#settings)에 정의된 값은 `IConfiguration` 인스턴스에서 사용할 수 있으므로 시작 클래스에서 앱 설정 값을 읽을 수 있습니다.

`IConfiguration` 인스턴스에서 사용자 지정 형식으로 값을 추출할 수 있습니다. 앱 설정 값을 사용자 지정 형식으로 복사하면 이러한 값을 주입 가능하게 만들어 서비스를 쉽게 테스트할 수 있습니다. 구성 인스턴스로 읽은 설정은 단순 키/값 쌍이어야 합니다.

앱 설정과 일치하는 이름을 가진 속성을 포함하는 다음 클래스를 살펴보겠습니다.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

사용자 지정 설정을 다음과 같이 구성할 수 있는 `local.settings.json` 파일:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

`Startup.Configure` 메서드 내에서 다음 코드를 사용하여 `IConfiguration` 인스턴스에서 사용자 지정 형식으로 값을 추출할 수 있습니다.

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

`Bind`를 호출하면 구성의 속성 이름과 일치하는 값이 사용자 지정 인스턴스로 복사됩니다. 이제 옵션 인스턴스가 IoC 컨테이너에 제공되며 함수에 주입할 수 있습니다.

옵션 개체는 제네릭 `IOptions` 인터페이스의 인스턴스로 함수에 주입됩니다. `Value` 속성을 사용하여 구성에 있는 값에 액세스할 수 있습니다.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

옵션을 사용하는 방법에 대한 자세한 내용은 [ASP.NET Core의 옵션 패턴](/aspnet/core/fundamentals/configuration/options)을 참조하세요.

## <a name="customizing-configuration-sources"></a>구성 원본 사용자 지정

> [!NOTE]
> 구성 원본 사용자 지정은 Azure Functions 호스트 버전 2.0.14192.0 및 3.0.14191.0부터 사용할 수 있습니다.

추가 구성 소스를 지정 하려면 `ConfigureAppConfiguration` 함수 앱의 클래스에서 메서드를 재정의 `StartUp` 합니다.

다음 샘플에서는 기본 및 선택적 환경 특정 앱 설정 파일의 구성 값을 추가 합니다.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

구성 공급자를 `ConfigurationBuilder` 의 속성에 추가 `IFunctionsConfigurationBuilder` 합니다. 구성 공급자 사용에 대 한 자세한 내용은 [ASP.NET Core 구성](/aspnet/core/fundamentals/configuration/?view=aspnetcore-3.1#configuration-providers)을 참조 하세요.

는 `FunctionsHostBuilderContext` 에서 가져옵니다 `IFunctionsConfigurationBuilder.GetContext()` . 이 컨텍스트를 사용 하 여 현재 환경 이름을 검색 하 고 함수 앱 폴더에서 구성 파일의 위치를 확인 합니다.

기본적으로 *appsettings.js* 와 같은 구성 파일은 함수 앱의 출력 폴더에 자동으로 복사 되지 않습니다. 다음 샘플과 일치 하도록 *.csproj* 파일을 업데이트 하 여 파일이 복사 되었는지 확인 합니다.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> 소비 또는 프리미엄 계획에서 실행 되는 함수 앱의 경우 트리거에 사용 되는 구성 값을 수정 하면 크기 조정 오류가 발생할 수 있습니다. 클래스에서 이러한 속성을 변경 `FunctionsStartup` 하면 함수 앱 시작 오류가 발생 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

- [함수 앱을 모니터링하는 방법](functions-monitoring.md)
- [함수에 대한 모범 사례](functions-best-practices.md)
