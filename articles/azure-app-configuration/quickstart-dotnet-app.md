---
title: .NET Framework 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: 이 빠른 시작에서는 Azure App Configuration을 사용하여 .NET Framework 앱을 만들어 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 6795d10950ddd7b03dfa505ab44d2f43837c9045
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590271"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 .NET Framework 앱 만들기

이 빠른 시작에서는 Azure App Configuration을 .NET Framework 기반 콘솔 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** > **만들기** > **키-값**을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

7. **적용**을 선택합니다.

## <a name="create-a-net-console-app"></a>.NET 콘솔 앱 만들기

1. Visual Studio를 시작하고 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다.

1. **새 프로젝트 만들기**에서 **콘솔** 프로젝트 형식을 필터링하고 **콘솔 앱(.NET Framework)** 을 클릭합니다. **다음**을 선택합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 입력합니다. **Framework**에서 **.NET Framework 4.7.1** 이상을 선택합니다. **만들기**를 선택합니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. 프로젝트의 *App.config* 파일을 다음과 같이 업데이트합니다.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   App Configuration 저장소의 연결 문자열은 `ConnectionString` 환경 변수에서 읽습니다. `appSettings` 섹션의 `configBuilders` 속성에 있는 `MyConfigStore` 앞에 `Environment` 구성 작성기를 추가합니다.

1. *Program.cs*를 열고, `ConfigurationManager`를 호출하여 App Configuration을 사용하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 App Configuration 저장소의 연결 문자열로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행합니다.

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Visual Studio를 다시 시작하여 변경 내용을 적용합니다. Ctrl+F5를 눌러 콘솔 앱을 빌드하고 실행합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고, .NET Framework 콘솔 앱에 사용했습니다. 애플리케이션이 시작된 후에는 `ConfigurationManager`의 `AppSettings` 값은 변경되지 않습니다. 그러나 App Configuration .NET Standard 구성 공급자 라이브러리는 .NET Framework 앱에서도 사용할 수 있습니다. .NET Framework 앱에서 구성 설정을 동적으로 새로 고치도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-dotnet.md)
