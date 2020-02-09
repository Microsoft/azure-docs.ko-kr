---
title: .NET Azure Functions에서 종속성 주입 사용
description: .NET 함수에서 서비스를 등록 하 고 사용 하는 데 종속성 주입을 사용 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a17ff15e71251e781cd30c33a5616af85e4f4eb9
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260086"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 종속성 주입 사용

Azure Functions는 클래스와 해당 종속성 간의 [IoC (제어 반전)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 를 구현 하는 기술인 DI (종속성 주입) 소프트웨어 디자인 패턴을 지원 합니다.

- Azure Functions의 종속성 주입은 .NET Core 종속성 주입 기능을 기반으로 합니다. [.Net Core 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 에 대해 잘 알고 있는 것이 좋습니다. 그러나 소비 계획의 Azure Functions에서 종속성을 재정의 하는 방법과 구성 값을 읽는 방법에는 차이가 있습니다.

- 종속성 주입에 대한 지원은 Azure Functions 2.x로 시작 합니다.

## <a name="prerequisites"></a>필수 조건

종속성 주입을 사용 하려면 먼저 다음 NuGet 패키지를 설치 해야 합니다.

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28 [패키지](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 버전 이상 (영문)

## <a name="register-services"></a>서비스 등록

서비스를 등록 하려면 `IFunctionsHostBuilder` 인스턴스에 구성 하 고 구성 요소를 추가 하는 메서드를 만듭니다.  Azure Functions 호스트는 `IFunctionsHostBuilder` 인스턴스를 만들고 메서드로 직접 전달 합니다.

메서드를 등록 하려면 시작 하는 동안 사용 되는 형식 이름을 지정 하는 `FunctionsStartup` assembly 특성을 추가 합니다.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>제한 사항

런타임이 시작 클래스를 처리 하기 전후에 일련의 등록 단계가 실행 됩니다. 따라서 다음 항목을 염두에 두어야 합니다.

- *Startup 클래스는 설치 및 등록을 위해서만 사용할 수 있습니다.* 시작 시 등록 된 서비스를 시작 프로세스 중에 사용 하지 마십시오. 예를 들어 시작 하는 동안 등록 되는로 거에 메시지를 기록 하지 않습니다. 등록 프로세스의이 시점에서 서비스를 사용할 수 있는 것은 너무 이릅니다. `Configure` 메서드를 실행 한 후에는 함수 런타임이 추가 종속성을 계속 등록 하 여 서비스가 작동 하는 방식에 영향을 줄 수 있습니다.

- *종속성 주입 컨테이너는 명시적으로 등록 된 유형만 보유*합니다. Injectable 형식으로 사용할 수 있는 유일한 서비스는 `Configure` 메서드에서 설정 하는 것입니다. 따라서 `BindingContext` 및 `ExecutionContext`와 같은 함수 관련 형식은 설치 중 또는 injectable 형식으로 사용할 수 없습니다.

## <a name="use-injected-dependencies"></a>삽입 된 종속성 사용

생성자 삽입은 함수에서 종속성을 사용할 수 있도록 하는 데 사용 됩니다. 생성자 주입을 사용 하려면 정적 클래스를 사용 하지 않아야 합니다.

다음 샘플에서는 `IMyService` 및 `HttpClient` 종속성이 HTTP로 트리거되는 함수에 삽입 되는 방법을 보여 줍니다. 이 예제에서는 시작 시 `HttpClient`를 등록 하는 데 필요한 [Microsoft Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 패키지를 사용 합니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>서비스 수명

Azure Functions 앱은 [ASP.NET 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)과 동일한 서비스 수명을 제공 합니다. 함수 앱의 경우 서로 다른 서비스 수명이 다음과 같이 동작 합니다.

- **임시**: 임시 서비스는 서비스의 각 요청에 대해 만들어집니다.
- **범위가**지정 된 서비스 수명은 함수 실행 수명과 일치 합니다. 범위가 지정 된 서비스는 실행 당 한 번 생성 됩니다. 실행 중에 해당 서비스에 대한 이후 요청은 기존 서비스 인스턴스를 다시 사용 합니다.
- **Singleton**: 단일 서비스 수명이 호스트 수명과 일치 하 고 해당 인스턴스의 함수 실행에서 다시 사용 됩니다. `SqlConnection` 또는 `HttpClient` 인스턴스와 같은 연결 및 클라이언트에는 Singleton 수명 서비스를 권장 합니다.

GitHub의 [다른 서비스 수명 샘플](https://aka.ms/functions/di-sample) 을 보거나 다운로드 합니다.

## <a name="logging-services"></a>로깅 서비스

사용자 고유의 로깅 공급자가 필요한 경우 사용자 지정 형식을 `ILoggerProvider` 인스턴스로 등록 합니다. Application Insights는 Azure Functions 자동으로 추가 됩니다.

> [!WARNING]
> - 환경에서 제공 하는 서비스와 충돌 하는 서비스를 등록 하므로 서비스 컬렉션에 `AddApplicationInsightsTelemetry()`를 추가 하지 마세요.
> - 기본 제공 Application Insights 기능을 사용 하는 경우 사용자 고유의 `TelemetryConfiguration` 또는 `TelemetryClient`를 등록 하지 마십시오.

## <a name="function-app-provided-services"></a>함수 앱 제공 서비스

함수 호스트는 많은 서비스를 등록 합니다. 다음 서비스는 응용 프로그램에서 종속성으로 사용 하기에 안전 합니다.

|서비스 유형|수명|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|호스트 인스턴스의 ID를 제공 해야 합니다.|

종속성을 적용 하려는 다른 서비스가 있는 경우 [문제를 만들어 GitHub에서 제안](https://github.com/azure/azure-functions-host)합니다.

### <a name="overriding-host-services"></a>호스트 서비스 재정의

호스트에서 제공 하는 서비스 재정의는 현재 지원 되지 않습니다.  재정의 하려는 서비스가 있는 경우 [문제를 만들어 GitHub에서 제안](https://github.com/azure/azure-functions-host)합니다.

## <a name="working-with-options-and-settings"></a>옵션 및 설정 작업

[앱 설정](./functions-how-to-use-azure-function-app-settings.md#settings) 에 정의 된 값은 `IConfiguration` 인스턴스에서 사용할 수 있으며,이 경우 시작 클래스에서 앱 설정 값을 읽을 수 있습니다.

`IConfiguration` 인스턴스에서 사용자 지정 형식으로 값을 추출할 수 있습니다. 앱 설정 값을 사용자 지정 형식으로 복사 하면 이러한 값을 injectable 하 여 서비스를 쉽게 테스트할 수 있습니다. 구성 인스턴스로 읽은 설정은 단순 키/값 쌍 이어야 합니다.

앱 설정과 일치 하는 라는 속성을 포함 하는 다음 클래스를 살펴보겠습니다.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

사용자 지정 설정을 다음과 같이 구성할 수 있는 `local.settings.json` 파일
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

`Startup.Configure` 메서드 내에서 다음 코드를 사용 하 여 `IConfiguration` 인스턴스에서 사용자 지정 형식으로 값을 추출할 수 있습니다.

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

`Bind`를 호출 하면 구성의 속성 이름과 일치 하는 값이 사용자 지정 인스턴스로 복사 됩니다. 이제 옵션 인스턴스를 IoC 컨테이너에서 함수에 삽입할 수 있습니다.

Options 개체는 제네릭 `IOptions` 인터페이스의 인스턴스로 함수에 삽입 됩니다. `Value` 속성을 사용 하 여 구성에 있는 값에 액세스할 수 있습니다.

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

옵션 사용에 대한 자세한 내용은 [ASP.NET Core의 옵션 패턴](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) 을 참조 하세요.

> [!WARNING]
> *로컬. settings* 또는 appsettings와 같은 파일에서 값을 읽으려고 시도 하지 마십시오 *. { 환경}.* 트리거 연결과 관련 된 이러한 파일에서 읽은 값은 호스팅 인프라가 구성 정보에 액세스할 수 없기 때문에 앱이 확장 될 때 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

- [함수 앱을 모니터링 하는 방법](functions-monitoring.md)
- [함수에 대한 모범 사례](functions-best-practices.md)
