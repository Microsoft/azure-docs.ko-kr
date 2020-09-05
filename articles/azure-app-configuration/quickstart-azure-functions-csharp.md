---
title: Azure Functions와 Azure App Configuration에 대한 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration 및 C#을 사용하여 Azure Functions 앱을 만듭니다. App Configuration 저장소를 만들고 연결합니다. 로컬로 함수를 테스트합니다.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: e15c83504ee0eebb925c122d0efd4896e7b55916
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590288"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration으로 Azure Functions 앱 만들기

이 빠른 시작에서는 Azure App Configuration 서비스를 Azure Functions 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- **Azure 개발** 워크로드를 사용하는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** >  **+ 만들기** > **키-값**을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

7. **적용**을 선택합니다.

## <a name="create-a-functions-app"></a>Functions 앱 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 패키지를 검색하여 프로젝트에 추가합니다. 찾을 수 없으면 **시험판 포함** 확인란을 선택합니다.

2. *Function1.cs* 파일을 열고, .NET Core 구성 및 App Configuration 공급자의 네임스페이스를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. `Configuration`이라는 `static` 속성을 추가하여 `IConfiguration`의 단일 인스턴스를 만듭니다. 그런 다음, `AddAzureAppConfiguration()`를 호출하여 App Configuration에 연결하는 `static` 생성자를 추가합니다. 그러면 애플리케이션이 시작될 때 구성이 한 번 로드됩니다. 이후부터는 모든 Functions 호출에 동일한 구성 인스턴스가 사용됩니다.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. 구성에서 값을 읽도록 `Run` 메서드를 업데이트합니다.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString**이라는 환경 변수를 설정하고, App Configuration 스토리지에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. F5를 눌러 함수를 테스트합니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

3. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

4. 브라우저의 주소 표시줄에 HTTP 요청에 대한 URL을 붙여 넣습니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![빠른 시작 함수 로컬 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 스토리지를 만들고, [App Configuration 공급자](https://go.microsoft.com/fwlink/?linkid=2074664)를 통해 Azure Functions 앱에서 사용했습니다. 구성 설정을 동적으로 새로 고치도록 Azure Functions 앱을 구성하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-azure-functions-csharp.md)
