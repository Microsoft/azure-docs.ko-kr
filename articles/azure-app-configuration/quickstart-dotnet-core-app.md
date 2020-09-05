---
title: .NET Core 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration을 사용하여 .NET Core 앱을 만들어 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 5925d80f23052324e5512908241ed573c681d835
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590254"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>빠른 시작: App Configuration을 사용하여 .NET Core 앱 만들기

이 빠른 시작에서는 Azure App Configuration을 .NET Core 콘솔 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - [Azure Cloud Shell](https://shell.azure.com)에서도 사용할 수 있습니다.

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** > **만들기** > **키-값**을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

7. **적용**을 선택합니다.

## <a name="create-a-net-core-console-app"></a>.NET Core 콘솔 앱 만들기

[.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 새 .NET Core 콘솔 앱 프로젝트를 만듭니다. Visual Studio 대신 .NET Core CLI를 사용하면 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있다는 이점이 있습니다.  또는 [Azure Cloud Shell](https://shell.azure.com)에서 사용할 수 있는 미리 설치된 도구를 사용합니다.

1. 프로젝트에 대한 새 폴더를 만듭니다.

2. 새 폴더에서 다음 명령을 실행하여 새 ASP.NET Core 콘솔 앱 프로젝트를 만듭니다.

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 다음 명령을 실행하여 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 패키지에 대한 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

    ```dotnetcli
    dotnet restore
    ```

3. *Program.cs*를 열고, .NET Core App Configuration 공급자에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. `builder.AddAzureAppConfiguration()` 메서드를 호출하여 App Configuration을 사용하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 설정하고, App Configuration 스토리지에 대한 액세스 키로 설정합니다. 명령줄에서 다음 명령을 실행합니다.

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다. 환경 변수의 값을 출력하여 올바르게 설정되었는지 확인합니다.

2. 다음 명령을 실행하여 콘솔 앱을 빌드합니다.

    ```dotnetcli
    dotnet build
    ```

3. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 앱을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고, [App Configuration 공급자](https://go.microsoft.com/fwlink/?linkid=2074664)를 통해 .NET Core 콘솔 앱에서 사용했습니다. 구성 설정을 동적으로 새로 고치도록 .NET Core 앱을 구성하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-dotnet-core.md)
