---
title: WebJobs SDK 시작
description: 이벤트 구동 백그라운드 처리를 위한 WebJobs SDK에 대해 간략히 소개합니다. Azure 서비스 및 타사 서비스에서 데이터에 액세스하는 방법을 알아봅니다.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: eebdf053cc3eea1dfc91476bff3817891bec42a3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959060"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 시작

이 문서에서는 Visual Studio 2019를 사용하여 Azure WebJobs SDK 프로젝트를 만들고 로컬로 실행한 후 [Azure App Service](overview.md)에 배포하는 방법을 보여 줍니다. WebJobs SDK 3.x 버전은 .NET Core와 .NET Framework 콘솔 앱을 모두 지원합니다. WebJobs SDK로 작업하는 방법에 대해 자세히 알아보려면 [이벤트 기반 백그라운드 처리에 Azure WebJobs SDK를 사용하는 방법](webjobs-sdk-how-to.md)을 참조하세요.

이 문서에서는 WebJobs를 .NET Core 콘솔 앱으로 배포하는 방법을 보여 줍니다. WebJobs를 .NET Framework 콘솔 앱으로 배포 하려면 [WebJobs를 .NET Framework 콘솔 앱으로 배포](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps)를 참조하세요. .NET Framework만 지원하는 WebJobs SDK 2.x 버전에 관심이 있다면 [Visual Studio를 사용하여 WebJobs 개발 및 배포 - Azure App Service](webjobs-dotnet-deploy-vs.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 개발** 워크로드를 사용하여 [Visual Studio 2019](/visualstudio/install/)를 설치합니다. Visual Studio는 있지만 해당 워크로드가 없는 경우 **도구 > 도구 및 기능 가져오기**를 선택하여 워크로드를 추가합니다.

* WebJobs SDK 프로젝트를 Azure에 게시하려면 [Azure 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)이 있어야 합니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.

2. **콘솔 앱(.NET Core)** 을 선택합니다.

3. *WebJobsSDKSample* 프로젝트의 이름을 지정한 다음, **만들기**를 선택합니다.

   ![새 프로젝트 대화 상자](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet 패키지

1. 안정적인 최신 3.x 버전의 [`Microsoft.Azure.WebJobs.Extensions` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)를 설치합니다(`Microsoft.Azure.WebJobs` 포함).

     **패키지 관리자 콘솔** 명령은 다음과 같습니다.

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    이 명령에서 `<3_X_VERSION>`을 지원되는 패키지 버전으로 바꾸세요. 

## <a name="create-the-host"></a>호스트 만들기

호스트는 함수에 대한 런타임 컨테이너이며, 트리거를 수신 대기하고 함수를 호출합니다. 다음은 ASP.NET Core의 일반 호스트인 [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)를 구현하는 호스트를 만드는 단계입니다.

1. *Program.cs*에 다음과 같은 `using` 문을 추가합니다.

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. `Main` 메서드를 다음 코드로 바꿉니다.

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
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

ASP.NET Core에서 호스트 구성은 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스의 메서드를 호출하여 설정됩니다. 자세한 내용은 [.NET 일반 호스트](/aspnet/core/fundamentals/host/generic-host)를 참조하세요. `ConfigureWebJobs` 확장 메서드는 WebJobs 호스트를 초기화합니다. `ConfigureWebJobs`에서 특정 WebJobs 확장을 초기화하고 해당 확장의 속성을 설정합니다.  

## <a name="enable-console-logging"></a>콘솔 로깅 사용

이 섹션에서는 [ASP.NET Core 로깅 프레임워크](/aspnet/core/fundamentals/logging)를 사용하는 콘솔 로깅을 설정합니다.

1. 안정적인 최신 버전의 [`Microsoft.Extensions.Logging.Console` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)를 설치합니다(`Microsoft.Extensions.Logging` 포함).

   **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   이 명령에서 `<3_X_VERSION>`을 지원되는 3.x 패키지 버전으로 바꾸세요.

1. *Program.cs*에 `using` 문을 추가합니다.

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)에서 [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 메서드를 호출합니다. [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) 메서드는 구성에 콘솔 로깅을 추가합니다.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` 메서드는 이제 다음과 같습니다.

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
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    이 업데이트는 다음을 수행합니다.

    * [대시보드 로깅](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)을 사용하지 않도록 설정합니다. 대시보드는 레거시 모니터링 도구이며, 처리량이 많은 프로덕션 시나리오에서는 대시보드 로깅을 사용하지 않는 것이 좋습니다.
    * 콘솔 공급자에 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 추가합니다.

이제 Azure Storage 큐에 도착하는 메시지에 의해 트리거되는 함수를 추가할 수 있습니다.

## <a name="install-the-storage-binding-extension"></a>Storage 바인딩 확장 설치

3\.x 버전부터는 WebJobs SDK에 필요한 Storage 바인딩 확장을 명시적으로 설치해야 합니다. 이전 버전에서는 Storage 바인딩이 SDK 패키지에 포함되어 있습니다.

1. [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지의 안정적인 최신 버전인 버전 3.x를 설치합니다. 

    **패키지 관리자 콘솔** 명령은 다음과 같습니다.

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    이 명령에서 `<3_X_VERSION>`을 지원되는 패키지 버전으로 바꾸세요. 

2. `ConfigureWebJobs` 확장 메서드에서 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스의 `AddAzureStorage` 메서드를 호출하여 Storage 확장을 초기화합니다. 이 시점에서 `ConfigureWebJobs` 메서드는 다음 예제와 유사합니다.

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>함수 만들기

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목...** , **클래스**를 차례로 선택한 다음, 새 C# 클래스 파일의 이름을 *Functions.cs*로 지정하고 **추가**를 선택합니다.

1. Functions.cs에서 생성된 템플릿을 다음 코드로 바꿉니다.
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   `QueueTrigger` 특성은 런타임에 새 메시지가 `queue`라는 Azure Storage 큐에 기록될 때 이 함수를 호출하도록 지시합니다. 큐 메시지의 내용은 `message` 매개 변수의 메서드 코드에 제공됩니다. 메서드 본문은 트리거 데이터를 처리하는 위치입니다. 이 예제에서 코드는 단지 메시지만 기록합니다.

   `message` 매개 변수는 문자열일 필요는 없습니다. JSON 개체, 바이트 배열 또는 [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) 개체에 바인딩할 수도 있습니다. [큐 트리거 사용을 참조하세요](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). 각 바인딩 형식(예: 큐, Blob 또는 테이블)에는 바인딩할 수 있는 다른 매개 변수 형식 집합이 있습니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

로컬로 실행 되는 Azure Storage 에뮬레이터에는 WebJobs SDK에 필요한 기능이 모두 포함 되어 있지 않습니다. 따라서 이 섹션에서는 Azure에서 Storage 계정을 만들고 이를 사용하도록 프로젝트를 구성합니다. 이미 스토리지 계정이 있는 경우 6단계로 건너뜁니다.

1. Visual Studio에서 **서버 탐색기**를 열고 Azure에 로그인합니다. **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음, **Microsoft Azure 구독에 연결**을 선택합니다.

   ![Azure에 로그인](./media/webjobs-sdk-get-started/sign-in.png)

1. **서버 탐색기**의 **Azure** 노드 아래에서 **Storage**를 마우스 오른쪽 단추로 클릭한 다음, **Storage 계정 만들기**를 선택합니다.

   ![Storage 계정 만들기 메뉴](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. **Storage 계정 만들기** 대화 상자에서 Storage 계정에 대한 고유한 이름을 입력합니다.

1. App Service 앱을 만든 지역과 동일한**지역** 또는 가까운 지역을 선택합니다.

1. **만들기**를 선택합니다.

   ![Storage 계정 만들기](./media/webjobs-sdk-get-started/create-storage-account.png)

1. **서버 탐색기**의 **Storage** 노드 아래에서 새 Storage 계정을 선택합니다. **속성** 창에서 **연결 문자열** 값 필드의 오른쪽에 있는 줄임표( **...** )를 선택합니다.

   ![연결 문자열 줄임표](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 연결 문자열을 복사하고, 이 값을 쉽게 다시 복사할 수 있는 특정 위치에 저장합니다.

   ![연결 문자열 복사](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>로컬로 실행되도록 스토리지 구성

WebJobs SDK는 Azure의 애플리케이션 설정에서 스토리지 연결 문자열을 찾습니다. 로컬로 실행하는 경우 로컬 구성 파일 또는 환경 변수에서 이 값을 찾습니다.

1. 프로젝트를 오른쪽 단추로 클릭하고 **추가** > **새 항목...** 을 선택하고 **JavaScript JSON 구성 파일**을 선택한 후 새 파일에 *appsettings.json*이라는 이름을 지정한 다음, **추가**를 선택합니다. 

1. 새 파일에서 다음 예제와 같이 `AzureWebJobsStorage` 필드를 추가합니다.

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. *{스토리지 연결 문자열}* 을 앞에서 복사한 연결 문자열로 바꿉니다.

1. 솔루션 탐색기에서 *appsettings.json* 파일을 선택하고 **속성** 창에서 **출력 디렉터리에 복사**를 **새 버전이면 복사**로 설정합니다.

나중에 Azure App Service의 앱에 동일한 연결 문자열 앱 설정을 추가합니다.

## <a name="test-locally"></a>로컬에서 테스트

이 섹션에서는 프로젝트를 로컬로 빌드하고, 실행하고, 큐 메시지를 만들어 함수를 트리거합니다.

1. **Ctrl+F5**를 눌러 프로젝트를 실행합니다.

   콘솔에는 런타임에서 함수를 찾았고 이 함수를 트리거하기 위해 큐 메시지를 기다리고 있음이 표시됩니다. 다음 출력은 v3.x 호스트에 의해 생성됩니다.

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. 콘솔 창을 닫습니다.

1. Visual Studio의 **서버 탐색기**에서 새 스토리지 계정에 대한 노드를 펼친 다음, **큐**를 마우스 오른쪽 단추로 클릭합니다.

1. **큐 만들기**를 선택합니다.

1. 큐에 대한 이름으로 *queue*를 입력한 다음, **확인**을 선택합니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/create-queue.png)

1. 새 큐에 대한 노드를 마우스 오른쪽 단추로 클릭한 다음, **큐 보기**를 선택합니다.

1. **메시지 추가** 아이콘을 선택합니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/create-queue-message.png)

1. **메시지 추가** 대화 상자에서 *Hello World!* 를 **메시지 텍스트**로 입력한 다음, **확인**을 선택합니다. 이제 큐에 메시지가 있습니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/hello-world-text.png)

1. 프로젝트를 다시 실행합니다.

   `ProcessQueueMessage` 함수에서 `QueueTrigger` 특성을 사용했으므로 WeJobs SDK 런타임은 시작할 때 큐 메시지를 수신 대기합니다. *queue*라는 큐에서 새 큐 메시지를 찾아 함수를 호출합니다.

   [큐 폴링 지수 백오프](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)로 인해 런타임에서 메시지를 찾고 함수를 호출하는 데 2분 정도 걸릴 수 있습니다. 이 대기 시간은 [개발 모드](webjobs-sdk-how-to.md#host-development-settings)에서 실행하여 줄일 수 있습니다.

   콘솔 출력은 다음과 같습니다.

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. 콘솔 창을 닫습니다. 

1. 큐 창으로 돌아가서 새로 고칩니다. 메시지가 로컬로 실행되는 함수에 의해 처리되었으므로 메시지가 사라집니다. 

## <a name="add-application-insights-logging"></a>Application Insights 로깅 추가

프로젝트가 Azure에서 실행되면 콘솔 출력을 확인하여 함수 실행을 모니터링할 수 없습니다. 권장되는 모니터링 솔루션은 [Application Insights](../azure-monitor/app/app-insights-overview.md)입니다. 자세한 내용은 [Azure Functions 모니터링](../azure-functions/functions-monitoring.md)을 참조하세요.

이 섹션에서는 Azure에 배포하기 전에 Application Insights 로깅을 설정하기 위해 다음 작업을 수행합니다.

* 사용할 App Service 앱과 Application Insights 인스턴스가 있는지 확인합니다.
* Application Insights 인스턴스와 이전에 만든 스토리지 계정을 사용하도록 App Service 앱을 구성합니다.
* Application Insights에 로깅할 프로젝트를 설정합니다.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service 앱 및 Application Insights 인스턴스 만들기

1. 사용할 수 있는 App Service 앱이 아직 없는 경우 [하나를 만듭니다](quickstart-dotnet-framework.md). 앱을 만들 때 연결된 Application Insights 리소스를 만들 수도 있습니다. 이 작업을 수행하면 앱에서 `APPINSIGHTS_INSTRUMENTATIONKEY`가 자동으로 설정됩니다.

1. 사용할 수 있는 Application Insights 리소스가 아직 없는 경우 [하나를 만듭니다](../azure-monitor/app/create-new-resource.md ). **애플리케이션 종류**를 **일반**으로 설정하고, **계측 키 복사**를 수행하는 섹션을 건너뜁니다.

1. 사용하려는 Application Insights 리소스가 이미 있는 경우 [계측 키를 복사합니다](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>앱 설정 구성 

1. Visual Studio의 **서버 탐색기**에서 **Azure** 아래의 **App Service** 노드를 펼칩니다.

1. App Service 앱이 있는 리소스 그룹을 펼친 다음, App Service 앱을 마우스 오른쪽 단추로 클릭합니다.

1. **설정 보기**를 선택합니다.

1. **연결 문자열** 상자에서 다음 항목을 추가합니다.

   |속성  |연결 문자열  |데이터베이스 유형|
   |---------|---------|------|
   |AzureWebJobsStorage | {앞에서 복사한 Storage 연결 문자열}|사용자 지정|

1. **애플리케이션 설정** 상자에 Application Insights 계측 키가 없으면 앞에서 복사한 계측 키를 추가합니다. (계측 키는 App Service 앱을 만든 방법에 따라 이미 있을 수 있습니다.)

   |속성  |값  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {계측 키} |

1. *{계측 키}* 를 사용 중인 Application Insights 리소스의 계측 키로 바꿉니다.

1. **저장**을 선택합니다.

1. 로컬로 실행할 수 있도록 프로젝트에 Application Insights 연결을 추가합니다. *appsettings.json* 파일에서 다음 예제와 같이 `APPINSIGHTS_INSTRUMENTATIONKEY` 필드를 추가합니다.

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    *{계측 키}* 를 사용 중인 Application Insights 리소스의 계측 키로 바꿉니다.

1. 변경 내용을 저장합니다.

### <a name="add-application-insights-logging-provider"></a>Application Insights 로깅 공급자 추가

[Application Insights](../azure-monitor/app/app-insights-overview.md) 로깅을 활용하려면 다음을 수행하도록 로깅 코드를 업데이트합니다.

* 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 사용하여 Application Insights 로깅 공급자 추가 로컬로 실행하는 경우 모든 정보 및 상위 수준의 로그가 콘솔과 Application Insights 모두에 기록됩니다.
* 호스트가 종료될 때 로그 출력이 플러시되도록 [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) 개체를 `using` 블록에 배치합니다.

1. 안정적인 최신 3.x 버전의 [`Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)를 설치합니다.

   **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    이 명령에서 `<3_X_VERSION>`을 지원되는 패키지 버전으로 바꾸세요.

1. *Program.cs*를 열고 `Main` 메서드의 코드를 다음 코드로 바꿉니다.

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    이렇게 하면 사용자가 이전에 앱 설정에 추가한 키를 사용하여 Application Insights 공급자가 로깅에 추가됩니다.

## <a name="test-application-insights-logging"></a>Application Insights 로깅 테스트

이 섹션에서는 로컬로 다시 실행하여 로깅 데이터가 이제 Application Insights 및 콘솔 둘 다로 이동하는지 확인합니다.

1. *Hello App Insights!* 를 메시지 텍스트로 입력하는 것을 제외하고는 [이전](#test-locally)과 동일한 방식으로 Visual Studio의 **서버 탐색기**를 사용하여 큐 메시지를 만듭니다.

1. 프로젝트를 실행합니다.

   WebJobs SDK에서 큐 메시지를 처리하고, 콘솔 창에 로그를 표시합니다.

1. 콘솔 창을 닫습니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하여 Application Insights 리소스를 봅니다. **Application Insights**를 검색하고 선택합니다.

1. Application Insights 인스턴스를 선택합니다.

1. **검색**을 선택합니다.

   ![검색 선택](./media/webjobs-sdk-get-started/select-search.png)

1. *Hello App Insights!* 가 표시되지 않으면 몇 분 동안 정기적으로 **새로 고침**을 선택합니다. (Application Insights 클라이언트에서 처리하는 로그를 플러시하는 데 시간이 걸리기 때문에 로그가 즉시 표시되지 않습니다.)

   ![Application Insights의 로그](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. 콘솔 창을 닫습니다.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Azure에 배포

배포 중에 함수를 실행할 앱 서비스 인스턴스를 만듭니다. Azure에서 App Service에 .NET Core 콘솔 앱을 게시하면 자동으로 WebJob으로 실행됩니다. 게시에 대해 자세히 알아보려면 [Visual Studio를 사용하여 WebJobs 개발 및 배포](webjobs-dotnet-deploy-vs.md)를 참조하세요.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Azure에서 함수 트리거

1. 로컬로 실행하고 있지 않은지 확인합니다(아직 열려 있는 경우 콘솔 창을 닫음). 그렇지 않으면 만든 큐 메시지를 로컬 인스턴스에서 가장 먼저 처리할 수 있습니다.

1. Visual Studio의 **큐** 페이지에서 이전처럼 큐에 메시지를 추가합니다.

1. **큐** 페이지를 새로 고치면 Azure에서 실행 중인 함수가 새 메시지를 처리했기 때문에 새 메시지가 사라집니다.

   > [!TIP]
   > Azure에서 테스트하는 경우 [개발 모드](webjobs-sdk-how-to.md#host-development-settings)를 사용하여 큐 트리거 함수를 즉시 호출하고 [큐 폴링 지수 백오프](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)로 인한 지연을 방지하도록 합니다.

### <a name="view-logs-in-application-insights"></a>Application Insights에서 로그 보기

1. [Azure Portal](https://portal.azure.com/)을 열고, Application Insights 리소스로 이동합니다.

1. **검색**을 선택합니다.

1. *Hello Azure!* 메시지가 표시되지 않으면 몇 분 동안 정기적으로 **새로 고침**을 선택합니다.

   이전 섹션에서 입력한 *Hello Azure!* 텍스트를 포함하여 WebJob에서 실행되는 함수의 로그가 표시됩니다.

## <a name="add-an-input-binding"></a>입력 바인딩 추가

입력 바인딩은 데이터를 읽는 코드를 간소화합니다. 다음 예제에서는 큐 메시지가 Blob 이름이 되고, Azure Storage에서 Blob 이름을 사용하여 Blob을 찾고 읽습니다.

1. *Functions.cs*에서 `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   이 코드에서 `queueTrigger`는 런타임에 다른 값으로 확인되는 [바인딩 식](../azure-functions/functions-bindings-expressions-patterns.md)입니다.  런타임에 큐 메시지의 내용이 포함됩니다.

1. `using`을 추가합니다.

   ```cs
   using System.IO;
   ```

1. 스토리지 계정에 Blob 컨테이너를 만듭니다.

   a. Visual Studio의 **서버 탐색기**에서 스토리지 계정에 대한 노드를 펼치고, **Blob**을 마우스 오른쪽 단추로 클릭한 다음, **Blob 컨테이너 만들기**를 선택합니다.

   b. **Blob 컨테이너 만들기** 대화 상자에서 *container*를 컨테이너 이름으로 입력한 다음, **확인**을 클릭합니다.

1. *Program.cs* 파일을 Blob 컨테이너에 업로드합니다. (여기서는 이 파일이 예제로 사용되며, 모든 텍스트 파일을 업로드하고, 파일 이름이 포함된 큐 메시지를 만들 수 있습니다.)

   a. **서버 탐색기**에서 만든 컨테이너의 노드를 두 번 클릭합니다.

   b. **컨테이너** 창에서 **업로드** 단추를 선택합니다.

   ![Blob 업로드 단추](./media/webjobs-sdk-get-started/blob-upload-button.png)

   다. *Program.cs*를 찾아 선택한 다음, **확인**을 선택합니다.

1. 메시지 텍스트로 *Program.cs*가 포함된 큐 메시지를 앞에서 만든 큐에 만듭니다.

   ![Program.cs 큐 메시지](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 로컬로 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고 해당 길이를 로깅합니다. 콘솔 출력은 다음과 같습니다.

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>출력 바인딩 추가

출력 바인딩은 데이터를 쓰는 코드를 간소화합니다. 이 예제에서는 크기를 로깅하지 않고 Blob 복사본을 작성하여 이전 예제를 수정합니다. Blob Storage 바인딩은 이전에 설치한 Azure Storage 확장 패키지에 포함되어 있습니다.

1. `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 메시지 텍스트로 *Program.cs*가 포함된 또 다른 큐 메시지를 만듭니다.

1. 로컬로 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고, 해당 길이를 로깅하고, 새 Blob을 만듭니다. 콘솔 출력은 동일하지만, Blob 컨테이너 창으로 이동하여 **새로 고침**을 선택하면 *copy-Program.cs*라는 새 Blob이 표시됩니다.

## <a name="republish-the-updates-to-azure"></a>Azure에 업데이트 다시 게시

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시** 대화 상자에서 현재 프로필이 선택되어 있는지 확인한 다음, **게시**를 선택합니다. 게시 결과는 **출력** 창에 자세히 표시됩니다.
 
1. Blob 컨테이너에 파일을 다시 업로드하고 큐에 업로드된 파일의 이름을 메시지로 추가하여 Azure에서 함수를 확인합니다. 큐에서 제거된 메시지와 Blob 컨테이너에 생성된 파일의 복사본이 표시됩니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 WebJobs SDK 3.x 프로젝트를 만들고 실행하고 배포하는 방법을 살펴보았습니다.

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)