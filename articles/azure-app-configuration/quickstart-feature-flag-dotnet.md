---
title: .NET Framework 앱에 기능 플래그를 추가하는 빠른 시작 | Microsoft Docs | Microsoft Docs
description: .NET Framework 앱에 기능 플래그 추가 및 Azure App Configuration에서 기능 플래그 관리에 대한 빠른 시작
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 998527a8557db0b0cf271dc8b05d5771660a175c
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209433"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>빠른 시작: .NET Framework 앱에 기능 플래그 추가

이 빠른 시작에서는 Azure App Configuration을 .NET Framework 앱에 통합하여 엔드투엔드 기능 관리를 구현하는 방법을 보여 줍니다. App Configuration 서비스를 사용하여 중앙에서 모든 기능 플래그를 저장하고 상태를 제어할 수 있습니다. 

.NET 기능 관리 라이브러리는 포괄적인 기능 플래그 지원을 통해 프레임워크를 확장합니다. 이 라이브러리는 .NET 구성 시스템을 기반으로 빌드됩니다. 또한 해당 .NET 구성 공급자를 통해 App Configuration과 원활하게 통합됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **기능 관리자** >  **+추가**를 선택하여 `Beta`라는 기능 플래그를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![Beta라는 기능 플래그 사용](media/add-beta-feature-flag.png)

    지금은 `label`을 정의하지 않은 상태로 둡니다.

## <a name="create-a-net-console-app"></a>.NET 콘솔 앱 만들기

1. Visual Studio를 시작하고 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다.

1. **새 프로젝트 만들기**에서 **콘솔** 프로젝트 형식을 필터링하고 **콘솔 앱(.NET Framework)** 을 클릭합니다. **다음**을 클릭합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 입력합니다. **프레임워크** 아래에서 **.NET Framework 4.8** 이상을 선택합니다. **만들기**를 클릭합니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다. 찾을 수 없으면 **시험판 포함** 확인란을 선택합니다.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. *Program.cs*를 열고, 다음 명령문을 추가합니다.

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. 기능 플래그가 검색되도록 `UseFeatureFlags` 옵션을 지정하여 App Configuration에 연결하도록 `Main` 메서드를 업데이트합니다. 그런 다음, `Beta` 기능 플래그가 설정된 경우 메시지를 표시합니다.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 App Configuration 저장소의 연결 문자열로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행합니다.

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Visual Studio를 다시 시작하여 변경 내용을 적용합니다. 

1. Ctrl+F5를 눌러 콘솔 앱을 빌드하고 실행합니다.

    ![기능 플래그가 설정된 앱](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 App Configuration에 기능 플래그를 만들고, .NET Framework 콘솔 앱에 사용했습니다. 애플리케이션을 다시 시작하지 않고 기능 플래그 및 기타 구성 값을 동적으로 업데이트하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-dotnet.md)
