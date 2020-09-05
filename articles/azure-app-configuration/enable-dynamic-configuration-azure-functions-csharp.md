---
title: Azure Functions 앱에서 Azure App Configuration 동적 구성 사용에 대한 자습서 | Microsoft Docs
description: 이 자습서에서는 Azure Functions 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: e603aa8ba85fdd214c04de515f405bcf9028791e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207100"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>자습서: Azure Functions 앱에서 동적 구성 사용

App Configuration .NET 표준 구성 공급자는 애플리케이션 작업 기반의 구성을 동적으로 캐싱하고 새로 고치는 것을 지원합니다. 이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에서 소개한 Azure Functions 앱을 기반으로 합니다. 계속 진행하기 전에, 먼저 [Azure App Configuration을 사용하여 Azure Functions 앱 만들기](./quickstart-azure-functions-csharp.md)를 완료합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경 내용에 따라 구성을 업데이트하도록 Azure Functions 앱을 설정합니다.
> * Azure Functions 호출에 최신 구성을 삽입합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- **Azure 개발** 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- [Azure App Configuration으로 Azure Functions 앱 만들기](./quickstart-azure-functions-csharp.md) 빠른 시작 완료

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드

1. *Function1.cs* 파일을 엽니다. `static` 속성 `Configuration` 외에도, 나중에 Functions 호출 중에 구성 업데이트 신호를 보내는 데 사용할 `IConfigurationRefresher`의 싱글톤 인스턴스를 유지하도록 새 `static` 속성 `ConfigurationRefresher`를 추가합니다.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. 생성자를 업데이트하고 `ConfigureRefresh` 메서드를 사용하여 App Configuration 저장소에서 새로 고칠 설정을 지정합니다. `IConfigurationRefresher` 인스턴스는 `GetRefresher` 메서드를 사용하여 검색됩니다. 필요에 따라 구성 캐시 만료 시간을 기본값인 30초에서 1분으로 변경합니다.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Functions 호출을 시작할 때 `Run` 메서드를 업데이트하고 `TryRefreshAsync` 메서드를 사용하여 구성을 새로 고치도록 신호를 보냅니다. 이 동작은 캐시 만료 시간이 되기 전에는 작동하지 않습니다. 차단하지 않고 구성을 새로 고치려면 `await` 연산자를 제거합니다.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString**이라는 환경 변수를 설정하고, 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

3. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![빠른 시작 함수 로컬 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

5. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

6. **구성 탐색기**를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 데이터 - 업데이트됨 |

7. 브라우저를 몇 번 새로 고칩니다. 캐시된 설정이 1분 후 만료되면 페이지에 업데이트된 값이 있는 Functions 호출의 응답이 표시됩니다.

    ![빠른 시작 함수 새로 고침 로컬](./media/quickstarts/dotnet-core-function-refresh-local.png)

이 자습서에 사용된 예제 코드는 [App Configuration GitHub 리포지토리](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)에서 다운로드할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Functions 앱을 사용하도록 설정하여 App Configuration의 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
