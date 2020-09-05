---
title: ASP.NET Core 앱에서 Azure App Configuration Key Vault 참조를 사용하기 위한 자습서 | Microsoft Docs
description: 이 자습서에서는 ASP.NET Core 앱에서 Azure App Configuration의 Key Vault 참조를 사용하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 3e6403f41d8e4b52ca64e9fa452524fa25efe870
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213250"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>자습서: ASP.NET Core 앱에서 Key Vault 참조 사용

이 자습서에서는 Azure Key Vault와 함께 Azure App Configuration 서비스를 사용하는 방법을 알아봅니다. App Configuration 및 Key Vault는 대부분의 애플리케이션 배포에서 함께 사용되는 보완 서비스입니다.

App Configuration을 사용하면 Key Vault에 저장된 값을 참조하는 키를 만들어 이러한 서비스를 함께 사용할 수 있습니다. App Configuration은 이러한 키를 만들 때 값 자체가 아니라 Key Vault 값의 URI를 저장합니다.

애플리케이션은 App Configuration에 저장된 다른 키와 마찬가지로 App Configuration 클라이언트 공급자를 사용하여 Key Vault 참조를 검색합니다. 이 경우 App Configuration에 저장된 값은 Key Vault의 값을 참조하는 URI입니다. Key Vault 값 또는 자격 증명이 아닙니다. 클라이언트 공급자는 이 키를 Key Vault 참조로 인식하므로 Key Vault를 사용하여 해당 값을 검색합니다.

애플리케이션은 App Configuration 및 Key Vault 모두에 대해 제대로 인증할 책임이 있습니다. 두 서비스는 직접 통신하지 않습니다.

이 자습서에서는 코드로 Key Vault 참조를 구현하는 방법을 보여 줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. 예를 들어 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 운영 체제에서 사용할 수 있는 크로스 플랫폼 코드 편집기입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Key Vault에 저장된 값을 참조하는 App Configuration 키 만들기
> * ASP.NET Core 웹 애플리케이션에서 이 키의 값에 액세스

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>자격 증명 모음 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기** 옵션을 선택합니다.

    ![Key Vault 만들기가 완료된 후 출력](./media/quickstarts/search-services.png)
1. 검색 상자에 **Key Vault**를 입력합니다.
1. 결과 목록의 왼쪽에서 **Key Vault**를 선택합니다.
1. **Key Vault**에서 **추가**를 선택합니다.
1. 오른쪽에 있는 **Key Vault 만들기**에서 다음 정보를 제공합니다.
    - **구독**을 선택하여 구독을 선택합니다.
    - **리소스 그룹**에서 **새로 만들기**를 선택하고 리소스 그룹 이름을 입력합니다.
    - **Key Vault 이름**에는 고유한 이름이 필요합니다. 이 자습서에서는 **Contoso-vault2**를 입력합니다.
    - **지역** 드롭다운 목록에서 위치를 선택합니다.
1. 다른 **Key Vault 만들기** 옵션은 기본값 그대로 둡니다.
1. **만들기**를 선택합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에 액세스할 권한이 있는 유일한 계정입니다.

![Key Vault 만들기가 완료된 후 출력](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

자격 증명 모음에 비밀을 추가하려면 몇 가지 추가 단계를 수행해야 합니다. 이 경우 Key Vault 검색을 테스트하는 데 사용할 수 있는 메시지를 추가합니다. 메시지는 **Message**라고 하며 여기에 "Hello from Key Vault" 값을 저장합니다.

1. Key Vault 속성 페이지에서 **비밀**을 선택합니다.
1. **생성/가져오기**를 선택합니다.
1. **비밀 만들기** 창에서 다음 값을 입력합니다.
    - **업로드 옵션**: **수동**을 입력합니다.
    - **Name**: **Message**를 입력합니다.
    - **값**: **Hello from Key Vault**를 입력합니다.
1. 다른 **비밀 만들기** 속성은 기본값 그대로 둡니다.
1. **만들기**를 선택합니다.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration에 Key Vault 참조 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택한 다음, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기**를 선택합니다.

1. **+ 만들기** > **Key Vault 참조**를 선택하고 다음 값을 지정합니다.
    - **키**: **TestApp:Settings:KeyVaultMessage**를 선택합니다.
    - **레이블**: 이 값은 빈 상태로 둡니다.
    - **구독**, **리소스 그룹** 및 **Key Vault**: 이전 섹션에서 만든 Key Vault의 값에 해당하는 값을 입력합니다.
    - **비밀**: 이전 섹션에서 만든 **Message**라는 비밀을 선택합니다.

## <a name="connect-to-key-vault"></a>Key Vault에 연결

1. 이 자습서에서는 Key Vault에 대한 인증에 서비스 주체를 사용합니다. 서비스 주체를 만들려면 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용합니다.

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    이 작업을 수행하면 일련의 키/값 쌍이 반환됩니다.

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 다음 명령을 실행하여 서비스 주체가 키 자격 증명 모음에 액세스하도록 허용합니다.

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. 환경 변수를 추가하여 *clientId*, *clientSecret* 및 *tenantId* 값을 저장합니다.

    #### <a name="windows-command-prompt"></a>[Windows 명령 프롬프트](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Key Vault 자격 증명은 애플리케이션 내에서만 사용됩니다. 애플리케이션은 이러한 자격 증명을 사용하여 Key Vault에 직접 인증합니다. App Configuration 서비스로 절대 전달되지 않습니다.

1. 터미널을 다시 시작하여 이러한 새 환경 변수를 로드합니다.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 참조를 사용하도록 코드 업데이트

1. 다음 명령을 실행하여 필요한 NuGet 패키지에 대한 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. *Program.cs*를 열고 다음과 같은 필요한 패키지에 대한 참조를 추가합니다.

    ```csharp
    using Azure.Identity;
    ```

1. `config.AddAzureAppConfiguration` 메서드를 호출하여 App Configuration을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다. `ConfigureKeyVault` 옵션을 포함하고 올바른 자격 증명을 Key Vault에 전달합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. 앱 구성에 대한 연결을 초기화하는 경우 `ConfigureKeyVault` 메서드를 호출하여 Key Vault에 대한 연결을 설정합니다. 초기화한 후 일반적인 App Configuration 키 값에 액세스하는 것과 동일한 방식으로 Key Vault 참조의 값에 액세스할 수 있습니다.

    이 프로세스가 실행되는 것을 보려면 **보기** > **홈** 폴더에서 *Index.cshtml*을 엽니다. 해당 내용을 다음 코드로 바꿉니다.

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    **TestApp:Settings:Message** 구성 값과 동일한 방식으로 Key Vault 참조 **TestApp:Settings:KeyVaultMessage**의 값에 액세스합니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

    ```dotnetcli
    dotnet build
    ```

1. 빌드가 완료된 후 다음 명령을 사용하여 웹앱을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run
    ```

1. 브라우저 창을 열고, 로컬로 호스팅되는 웹앱에 대한 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![로컬 앱 빠른 시작](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Key Vault에 저장된 값을 참조하는 App Configuration 키를 만들었습니다. App Configuration 및 Key Vault에 대한 액세스를 간소화하는 Azure 관리 서비스 ID를 추가하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
