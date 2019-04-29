---
title: Visual Studio를 사용하여 ASP.NET 프로젝트에 Key Vault 지원 추가 - Azure Key Vault | Microsoft Docs
description: 이 자습서를 통해 ASP.NET 또는 ASP.NET Core 웹 애플리케이션에 Key Vault 지원을 추가하는 방법을 배울 수 있습니다.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764519"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Visual Studio 연결된 서비스를 사용하여 웹 애플리케이션에 Key Vault 추가

이 자습서에서는 ASP.NET Core를 사용하든 아니면 모든 종류의 ASP.NET 프로젝트를 사용하든 Visual Studio에서 웹 프로젝트에 대한 사용자의 비밀을 관리하기 위해 Azure Key Vault를 사용하여 시작해야 하는 모든 항목을 쉽게 추가하는 방법에 대해 알아봅니다. Visual Studio에서 연결 된 서비스 기능을 사용 하 여 모든 NuGet 패키지 및 Azure의 Key Vault에 연결 하는 데 필요한 구성 설정을 자동으로 추가 하는 Visual Studio 할 수 있습니다. 

Key Vault를 사용하도록 설정하기 위해 프로젝트에서 연결된 서비스에서 수행하는 변경 내용에 대한 자세한 내용은 [Key Vault 연결된 서비스 - 내 ASP.NET 4.7.1 프로젝트에서 변경된 내용](#how-your-aspnet-framework-project-is-modified) 또는 [Key Vault 연결된 서비스 - 내 ASP.NET Core 프로젝트에서 변경된 내용](#how-your-aspnet-core-project-is-modified)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
- **Visual Studio 2019** 또는 **Visual Studio 2017 버전 15.7** 사용 하 여 합니다 **웹 개발** 워크 로드가 설치 되어 있습니다. [여기에서 다운로드하세요](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Visual Studio 2017을 사용 하 여 ASP.NET (Core 아님)에 대 한.NET Framework 4.7.1 또는 이후 개발 도구, 기본적으로 설치 되어 있지 않은 것이 해야 합니다. 설치하려면 Visual Studio 설치 관리자를 시작하고, **수정**을 선택한 다음, **개별 구성 요소**를 선택한 다음, 오른쪽에서 **ASP.NET 및 웹 개발**을 확장하고 **.NET Framework 4.7.1 개발 도구**를 선택합니다.
- ASP.NET 4.7.1 이상에서 또는 ASP.NET Core 2.0 웹 프로젝트를 엽니다.

## <a name="add-key-vault-support-to-your-project"></a>프로젝트에 Key Vault 지원 추가

1. **솔루션 탐색기**에서 **추가** > **연결된 서비스**를 선택합니다.
   연결된 서비스 페이지가 프로젝트에 추가할 수 있는 서비스와 함께 표시됩니다.
1. 사용 가능한 서비스 메뉴에서 **Azure Key Vault로 비밀 보호**를 선택합니다.

   ![“Azure Key Vault로 비밀 보호” 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Visual Studio에 로그인하고 사용자 계정과 연결된 Azure 구독이 있는 경우 구독이 포함된 드롭다운 목록이 페이지에 표시됩니다. Visual Studio에 로그인되어 있는지, 로그인에 사용한 계정이 Azure 구독에 사용한 계정과 동일한지 확인합니다.

1. 사용하려는 구독을 선택한 다음, 새로운 Key Vault 또는 기존 Key Vault를 선택하거나 편집 링크를 선택하여 자동으로 생성된 이름을 수정합니다.

   ![구독 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Key Vault에 사용할 이름을 입력합니다.

   ![Key Vault의 이름 변경 및 리소스 그룹 선택](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 기존 리소스 그룹을 선택 하거나를 자동으로 생성 된 고유한 이름으로 새 레코드를 만들려면 선택 합니다.  다른 이름으로 새 그룹을 만들려는 경우 [Azure Portal](https://portal.azure.com)을 사용한 다음, 페이지를 닫고 리소스 그룹의 목록을 다시 로드할 수 있습니다.
1. Key Vault를 만들 지역을 선택합니다. 웹 애플리케이션이 Azure에서 호스트되는 경우 최적의 성능을 위해 웹 애플리케이션을 호스팅하는 지역을 선택합니다.
1. 가격 책정 모델을 선택합니다. 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요.
1. 확인을 선택하여 구성 선택을 수락합니다.
1. **추가**를 선택하여 Key Vault를 만듭니다. 이미 사용되는 이름을 선택하면 만들기 프로세스에 실패할 수 있습니다.  이렇게 되면 **편집** 링크를 사용하여 Key Vault 이름을 바꾸고 다시 시도합니다.

   ![프로젝트에 연결된 서비스 추가 중](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 이제 Azure의 Key Vault에 비밀을 추가합니다. 포털에서 올바른 위치로 이동하려면 이 Key Vault에 저장된 비밀 관리 링크를 클릭합니다. 페이지 또는 프로젝트를 닫은 후 [Azure Portal](https://portal.azure.com)에서 **보안** 아래의 **모든 서비스**를 선택하고, **Key Vault**를 선택한 후 만든 Key Vault를 선택하여 그것으로 이동합니다.

   ![포털로 이동](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 만든 키 자격 증명 모음의 Key Vault 섹션에서 **비밀**을 선택한 다음, **생성/가져오기**를 선택합니다.

   ![비밀 생성/가져오기](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. “MySecret”과 같은 비밀을 입력하고 임의의 문자열을 테스트로 제공한 다음 **만들기** 버튼을 선택합니다.

   ![비밀 만들기](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (선택 사항) 다른 비밀을 입력합니다. 단, 이번에는 “비밀--MySecret”이라는 이름을 지정하여 범주에 포함시킵니다. 이 구문은 “MySecret”이란 비밀을 포함하는 “비밀” 범주를 지정합니다.
 
이제 코드에서 비밀에 액세스할 수 있습니다. 다음 단계는 ASP.NET 4.7.1 또는 ASP.NET Core를 사용하는지에 따라 달라집니다.

## <a name="access-your-secrets-in-code"></a>코드의 비밀에 액세스

1. 이러한 두 NuGet 패키지 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 라이브러리를 설치합니다.

2. Program.cs 파일을 열고 해당 코드를 다음 코드로 업데이트합니다. 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. 다음 About.cshtml.cs 파일을 열고 다음 코드를 작성 합니다.
   1. 문을 사용 하 여이 Microsoft.Extensions.Configuration에 대 한 참조를 포함 합니다.

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. 이 생성자를 추가 합니다.

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. OnGet 메서드를 업데이트합니다. 위의 명령에서 만든 비밀 이름의 여기에 표시 된 자리 표시자 값을 업데이트 합니다.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

정보 페이지로 이동 하 여 앱을 로컬로 실행 합니다. 검색 하 여 비밀 값이 표시 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 Key Vault와 관련된 리소스가 삭제됩니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 검색 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제**를 선택합니다.
3. **TYPE THE RESOURCE GROUP NAME:** 상자에 리소스 그룹 이름을 입력하고 **삭제**를 선택합니다.

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core 프로젝트를 수정 하는 방법

이 섹션에서는 Visual Studio를 사용 하 여 서비스에 연결 된 Key Vault를 추가 하는 경우 ASP.NET 프로젝트에 대 한 정확한 변경 내용을 식별 합니다.

### <a name="added-references"></a>추가된 참조

프로젝트 파일.NET 참조 및 NuGet 패키지 참조를 영향을 줍니다.

| Type | 참조 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>추가된 파일

- 연결된 서비스 공급자, 버전 및 설명서 링크에 대한 정보를 기록하는 ConnectedService.json이 추가됩니다.

### <a name="project-file-changes"></a>프로젝트 파일 변경 내용

- 연결된 서비스 ItemGroup 및 ConnectedServices.json 파일이 추가됩니다.

### <a name="launchsettingsjson-changes"></a>launchsettings.json changes

- IIS Express 프로필 및 웹 프로젝트 이름과 일치하는 프로필에 다음 환경 변수 항목이 추가됩니다.

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Azure 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET 프레임 워크 프로젝트는 수정 하는 방법

이 섹션에서는 Visual Studio를 사용 하 여 서비스에 연결 된 Key Vault를 추가 하는 경우 ASP.NET 프로젝트에 대 한 정확한 변경 내용을 식별 합니다.

### <a name="added-references"></a>추가된 참조

프로젝트 파일.NET 참조에 영향을 줍니다 및 `packages.config` (NuGet 참조).

| Type | 참조 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>추가된 파일

- 연결된 서비스 공급자, 버전 및 설명서에 대한 링크 정보를 기록하는 ConnectedService.json이 추가됩니다.

### <a name="project-file-changes"></a>프로젝트 파일 변경 내용

- 연결된 서비스 ItemGroup 및 ConnectedServices.json 파일이 추가됩니다.
- [추가된 참조](#added-references) 섹션에 설명된 .NET 어셈블리에 대한 참조입니다.

### <a name="webconfig-or-appconfig-changes"></a>web.config 또는 app.config 변경 내용

- 다음 구성 항목을 추가했습니다.

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Azure 변경 내용

- 리소스 그룹이 생성됩니다. (또는 기존 그룹이 사용됩니다.)
- 지정된 리소스 그룹에 Key Vault가 생성됩니다.

## <a name="next-steps"></a>다음 단계

[Key Vault 개발자 가이드](key-vault-developers-guide.md)를 확인하여 Key Vault 개발에 대해 자세히 알아봅니다.