---
title: '.NET Framework 자습서: Azure App Configuration의 동적 구성'
description: 이 자습서에서는 Azure App Configuration을 사용하여 .NET Framework 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 49ab9090dd2f1860bed84d0494cb862212840e82
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207042"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>자습서: .NET Framework에서 동적 구성 사용

App Configuration .NET 클라이언트 라이브러리는 애플리케이션을 다시 시작하지 않고도 주문형 구성 설정 세트의 업데이트를 지원합니다. 이는 구성 공급 기업의 옵션에서 먼저 `IConfigurationRefresher`의 인스턴스를 가져온 다음, 코드의 해당 인스턴스에서 `TryRefreshAsync`를 호출하여 구현할 수 있습니다.

설정을 업데이트하고 구성 저장소에 대한 너무 많은 호출을 피하기 위해 각 설정에 대해 캐시를 사용합니다. 설정의 캐시된 값이 만료될 때까지 새로 고침 작업은 구성 저장소에서 값이 변경된 경우에도 값을 업데이트하지 않습니다. 각 요청의 기본 만료 시간은 30초지만, 필요한 경우 재정의할 수 있습니다.

이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 .NET Framework 앱 만들기](./quickstart-dotnet-app.md)를 완료합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경에 따라 .NET Framework 앱의 해당 구성을 업데이트하도록 설정합니다.
> * 애플리케이션에 최신 구성을 삽입합니다.
## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 이상](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** >  **+ 만들기** > **키-값**을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

7. **적용**을 선택합니다.

## <a name="create-a-net-framework-console-app"></a>.NET Framework 콘솔 앱 만들기

1. Visual Studio를 시작하고 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다.

1. **새 프로젝트 만들기**에서 **콘솔** 프로젝트 형식을 필터링하고 **콘솔 앱(.NET Framework)** 을 클릭합니다. **다음**을 클릭합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 입력합니다. **Framework**에서 **.NET Framework 4.7.1** 이상을 선택합니다. **만들기**를 클릭합니다.

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드
1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet 패키지를 검색하여 프로젝트에 추가합니다. 찾을 수 없으면 **시험판 포함** 확인란을 선택합니다.

1. *Program.cs*를 열고, .NET Core App Configuration 공급자에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 구성 관련 개체를 저장하는 두 개의 변수를 추가합니다.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. 지정된 새로 고침 옵션을 사용하여 App Configuration에 연결하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    `ConfigureRefresh` 메서드는 새로 고침 작업이 트리거될 때 App Configuration 저장소로 구성 데이터를 업데이트하는 데 사용되는 설정을 지정하는 데 사용됩니다. `AddAzureAppConfiguration` 메서드에 제공된 옵션에서 `GetRefresher` 메서드를 호출하여 `IConfigurationRefresher`의 인스턴스를 검색할 수 있으며, 이 인스턴스의 `TryRefreshAsync` 메서드를 사용하여 코드의 아무 곳에서나 새로 고침 작업을 트리거할 수 있습니다.

    > [!NOTE]
    > 구성 설정에 대한 기본 캐시 만료 시간은 30초이지만 `ConfigureRefresh` 메서드에 대한 인수로 전달된 옵션 이니셜라이저의 `SetCacheExpiration` 메서드를 호출하여 재정의할 수 있습니다.

1. App Configuration에서 구성 데이터의 수동 새로 고침을 트리거하는 `PrintMessage()`라는 메서드를 추가합니다.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 설정하고, App Configuration 스토리지에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Visual Studio를 다시 시작하여 변경 내용을 적용합니다. 

1. Ctrl+F5를 눌러 콘솔 앱을 빌드하고 실행합니다.

    ![로컬로 앱 시작](./media/dotnet-app-run.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기**를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 데이터 - 업데이트됨 |

1. 실행 중인 애플리케이션으로 돌아가서 Enter 키를 눌러 새로 고침을 트리거하고 명령 프롬프트 또는 PowerShell 창에서 업데이트된 값을 출력합니다.

    ![로컬로 앱 새로 고침](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 새로 고침 작업에 대한 구성을 지정하는 동안 `SetCacheExpiration` 메서드를 사용하여 캐시 만료 시간이 10초로 설정되었으므로 해당 설정에 대한 마지막 새로 고침 이후 최소 10초가 경과한 경우에만 구성 설정 값이 업데이트됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 .NET Framework 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
