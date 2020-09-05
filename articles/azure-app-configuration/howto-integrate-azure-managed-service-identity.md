---
title: 관리 되는 id를 사용 하 여 앱 구성에 액세스
titleSuffix: Azure App Configuration
description: 관리 id를 사용 하 여 Azure 앱 구성에 인증
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: b1efeeef09e7c228eb8fc14de52a6beb2e9ffffe
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206830"
---
# <a name="use-managed-identities-to-access-app-configuration"></a>관리 되는 id를 사용 하 여 앱 구성에 액세스

Azure Active Directory [관리 되는 id는](../active-directory/managed-identities-azure-resources/overview.md) 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 합니다. 관리 되는 id를 사용 하 여 코드는 실행 되는 Azure 서비스에 대해 만든 서비스 주체를 사용할 수 있습니다. Azure Key Vault 또는 로컬 연결 문자열에 저장된 별도의 자격 증명 대신 관리 ID를 사용합니다.

Azure 앱 구성과 해당 .NET Core, .NET Framework 및 Java 스프링 클라이언트 라이브러리에는 관리 되는 id 지원이 기본적으로 제공 됩니다. 이를 반드시 사용할 필요는 없지만 관리 되는 id를 사용 하면 암호를 포함 하는 액세스 토큰이 필요 하지 않습니다. 코드는 서비스 끝점만 사용 하 여 앱 구성 저장소에 액세스할 수 있습니다. 비밀을 노출 하지 않고 코드에이 URL을 직접 포함할 수 있습니다.

이 문서에서는 관리 되는 id를 활용 하 여 앱 구성에 액세스 하는 방법을 보여 줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 하기 전에 먼저  [앱 구성을 사용 하 여 ASP.NET Core 앱을 만듭니다](./quickstart-aspnet-core-app.md) .

또한이 문서에서는 관리 되는 id를 앱 구성의 Key Vault 참조와 함께 사용할 수 있는 방법을 보여 줍니다. 관리 되는 단일 id를 사용 하 여 Key Vault 및 앱 구성의 구성 값에서 모두 원활 하 게 액세스할 수 있습니다. 이 기능을 탐색 하려면 먼저 [ASP.NET Core를 사용 하 여 Key Vault 참조 사용](./use-key-vault-references-dotnet-core.md) 을 완료 합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * App Configuration에 대한 관리 ID 액세스 권한을 부여합니다.
> * App Configuration에 연결할 때 관리 ID를 사용하도록 앱을 구성합니다.
> * 필요에 따라 앱 구성 Key Vault 참조를 통해 Key Vault에 연결할 때 관리 id를 사용 하도록 앱을 구성 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 필요합니다.

* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell 구성](https://docs.microsoft.com/azure/cloud-shell/quickstart)되었습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>관리형 ID 추가

포털에서 관리 id를 설정 하려면 먼저 응용 프로그램을 만든 후 기능을 사용 하도록 설정 합니다.

1. 일반적으로 [Azure Portal](https://portal.azure.com) 에서 App Services 인스턴스를 만듭니다. 포털에서 이동합니다.

1. 왼쪽 창에서 **설정** 그룹까지 아래로 스크롤하고 **ID**를 선택합니다.

1. **시스템 할당** 탭에서 **상태**를 **켜기**로 전환하고 **저장**을 선택합니다.

1. 시스템 할당 관리 id를 사용 하도록 설정 하 라는 메시지가 표시 되 면 **예** 를 대답 합니다.

    ![App Service에서 관리형 ID 설정](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration에 대한 액세스 권한 부여

1. [Azure Portal](https://portal.azure.com)에서 **모든 리소스** 를 선택 하 고 빠른 시작에서 만든 앱 구성 저장소를 선택 합니다.

1. **액세스 제어(IAM)** 를 선택합니다.

1. **액세스 권한 확인** 탭의 **역할 할당 추가** 카드 UI에서 **추가**를 선택합니다.

1. **역할**아래에서 **앱 구성 데이터 판독기**를 선택 합니다. **다음에 대한 액세스 할당**의 **시스템이 할당한 관리 ID** 아래에서 **App Service**를 선택합니다.

1. **구독** 아래에서 Azure 구독을 선택합니다. 앱의 App Service 리소스를 선택합니다.

1. **저장**을 선택합니다.

    ![관리형 ID 추가](./media/add-managed-identity.png)

1. 선택 사항: Key Vault에 대 한 액세스 권한을 부여 하려는 경우 [관리 id를 사용 하 여 Key Vault 인증 제공](https://docs.microsoft.com/azure/key-vault/managed-identity)의 지침을 따르세요.

## <a name="use-a-managed-identity"></a>관리 ID 사용

1. *Azure id* 패키지에 대 한 참조를 추가 합니다.

    ```cli
    dotnet add package Azure.Identity
    ```

1. 앱 구성 저장소에 대 한 끝점을 찾습니다. 이 URL은 Azure Portal의 저장소에 대 한 **액세스 키** 탭에 나열 됩니다.

1. *appsettings.json*을 열고 다음 스크립트를 추가합니다. 괄호를 포함 하 여 *\<service_endpoint>* 를 앱 구성 저장소에 대 한 URL로 바꿉니다.

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. *Program.cs*을 열고 `Azure.Identity` 및 네임 스페이스에 대 한 참조를 추가 합니다 `Microsoft.Azure.Services.AppAuthentication` .

    ```csharp-interactive
    using Azure.Identity;
    ```

1. 앱 구성에 직접 저장 된 값에만 액세스 하려는 경우 `CreateWebHostBuilder` 메서드를 대체 하 여 메서드를 업데이트 합니다 `config.AddAzureAppConfiguration()` .

    > [!IMPORTANT]
    > .NET Core 3.0에서 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다.  사용자 환경에 따라 올바른 구문을 선택합니다.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. 앱 구성 값과 Key Vault 참조를 모두 사용 하려면 아래와 같이 *Program.cs* 를 업데이트 합니다. 이 코드 `KeyVaultClient` 는를 사용 하 여 새를 만들고 `AzureServiceTokenProvider` 이 참조를 메서드에 대 한 호출에 전달 `UseAzureKeyVault` 합니다.

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    이제 다른 앱 구성 키와 마찬가지로 Key Vault 참조에 액세스할 수 있습니다. 구성 공급자는 `KeyVaultClient` Key Vault에 대 한 인증을 위해 구성한를 사용 하 고 값을 검색 합니다.

> [!NOTE]
> `ManagedIdentityCredential` 에서는 관리 되는 id 인증만 지원 합니다. 로컬 환경에서는 작동 하지 않습니다. 코드를 로컬로 실행 하려면 `DefaultAzureCredential` 서비스 주체 인증도 지 원하는를 사용 하는 것이 좋습니다. 자세한 내용은 [링크](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential) 를 확인 하세요.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>로컬 Git에서 배포

Kudu 빌드 서버를 사용 하 여 앱에 로컬 Git 배포를 사용 하도록 설정 하는 가장 쉬운 방법은 [Azure Cloud Shell](https://shell.azure.com)를 사용 하는 것입니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu로 로컬 Git을 사용하도록 설정
앱에 대 한 로컬 git 리포지토리가 없으면 하나를 초기화 해야 합니다. 로컬 git 리포지토리를 초기화 하려면 앱의 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```cmd
git init
git add .
git commit -m "Initial version"
```

Kudu 빌드 서버를 사용 하 여 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하려면 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Cloud Shell에서를 실행 합니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

이 명령은 다음과 같은 출력을 제공 합니다.

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>프로젝트 배포

_로컬 터미널 창_에서 로컬 Git 리포지토리에 Azure 원격을 추가 합니다. _\<url>_ [Kudu를 사용 하 여 로컬 git 사용](#enable-local-git-with-kudu)에서 가져온 GIT 원격의 URL로 대체 합니다.

```bash
git remote add azure <url>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. 암호를 입력하라는 메시지가 표시되면 [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력합니다. Azure Portal에 로그인하는 데 사용하는 암호는 사용하지 마세요.

```bash
git push azure master
```

출력에 ASP.NET용 MSBuild, Node.js용 `npm install` 및 Python용 `pip install`과 같은 런타임 관련 자동화가 표시될 수 있습니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

브라우저를 통해 웹앱으로 이동하여 콘텐츠가 배포되었는지 확인합니다.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>다른 언어의 관리형 ID 사용

.NET Framework 및 Java Spring 용 App Configuration 공급자에는 관리형 ID에 대한 지원 기능이 내장되어 있습니다. 이러한 공급자 중 하나를 구성할 때 전체 연결 문자열 대신 상점의 URL 끝점을 사용할 수 있습니다.

예를 들어 빠른 시작에서 만든 .NET Framework 콘솔 앱을 업데이트 하 여 *App.config* 파일에서 다음 설정을 지정할 수 있습니다.

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 앱 구성에 대 한 액세스를 간소화 하 고 앱에 대 한 자격 증명 관리를 개선 하기 위해 Azure 관리 id를 추가 했습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [CLI 샘플](./cli-samples.md)
