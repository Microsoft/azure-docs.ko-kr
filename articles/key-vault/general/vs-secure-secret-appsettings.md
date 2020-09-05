---
title: 웹 애플리케이션의 애플리케이션 비밀 설정을 안전하게 저장 - Azure Key Vault | Microsoft Docs
description: ASP.NET Core Key Vault Provider, 사용자 비밀 또는 .NET 4.7.1을 사용하여 Azure 자격 증명 또는 타사 API와 같은 비밀 애플리케이션 설정을 안전하게 저장하는 방법
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.custom: devx-track-csharp
ms.openlocfilehash: 79fa01e53b53f3066e55736c105d6489ccbd96e7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019847"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>웹 애플리케이션의 애플리케이션 비밀 설정을 안전하게 저장

## <a name="overview"></a>개요
이 문서에서는 Azure 애플리케이션의 비밀 애플리케이션 구성 설정을 안전하게 저장하는 방법에 대해 설명합니다.

일반적으로 모든 웹 응용 프로그램 구성 설정은 Web.config와 같은 구성 파일에 저장 됩니다. 이 연습에서는 클라우드 자격 증명과 같은 비밀 설정을 GitHub와 같은 공용 소스 제어 시스템에 체크인합니다. 한편 소스 코드를 변경하고 개발 설정을 재구성하는데 필요한 오버헤드로 인해 보안 모범 사례를 따르기 어려울 수 있습니다.

개발 프로세스를 안전하게 유지하기 위해, 최소한의 코드 수정만으로 어플리케이션 비밀 구성 설정을 지킬 수 있는 도구 및 프레임워크 라이브러리들이 준비되어 있습니다.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET 및 .NET Core 응용 프로그램

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>소스 제어 폴더 외부의 사용자 비밀 저장소에 비밀 설정 저장
빠른 프로토타이핑을 수행 중이거나 인터넷에 액세스할 수 없는 경우, 비밀 세팅을 소스 제어 폴더 외부의 사용자 비밀 저장소로 이동하는 것부터 시작합니다. 사용자 비밀 저장소는 사용자 프로필 폴더 아래에 저장된 파일이므로 비밀이 소스 제어로 체크인되지 않습니다. 다음 다이어그램은 [사용자 비밀](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)의 작동 원리를 보여줍니다.

![사용자 비밀은 비밀 설정을 소스 컨트롤 외부에 유지합니다.](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

.NET Core 콘솔 애플리케이션을 실행하는 경우 Key Vault를 사용하여 비밀을 안전하게 저장하세요.

### <a name="save-secret-settings-in-azure-key-vault"></a>Azure Key Vault에 비밀 설정 저장
프로젝트를 개발하면서 소스 코드를 안전하게 공유해야 하는 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용합니다.

1. Azure 구독에 Key Vault를 만듭니다. UI에서 필수 필드를 모두 채우고 블레이드 아래쪽에서 *만들기*를 클릭합니다.

    ![Azure Key Vault 만들기](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. 나와 나의 팀 멤버에게 Key Vault에 대한 액세스 권한을 부여합니다. 팀이 큰 경우 [Azure Active Directory 그룹](../../active-directory/active-directory-groups-create-azure-portal.md)을 만들어서 해당 보안 그룹 액세스를 Key Vault에 추가할 수 있습니다. *비밀 권한* 드롭다운에서 *비밀 관리 작업* 아래의 *가져오기*와 *목록*을 선택합니다.
웹 앱을 이미 만든 경우에는 앱 설정 또는 파일에 비밀 구성을 저장 하지 않고 키 자격 증명 모음에 액세스할 수 있도록 웹 앱에 Key Vault에 대 한 액세스 권한을 부여 합니다. 이름으로 웹 앱을 검색 하 고 사용자에 게 액세스 권한을 부여 하는 것과 동일한 방식으로 웹 앱을 추가 합니다.

    ![Key Vault 액세스 정책 추가](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Azure Portal에서 Key Vault에 비밀을 추가 합니다. 중첩된 구성 설정의 경우 Key Vault 비밀 이름이 유효하도록 ':'을 '--'로 바꿉니다. ':'은 Key Vault 비밀의 이름에 사용할 수 없습니다.

    ![Key Vault 비밀 추가](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Visual Studio 2017 V 15.6 이전에는 Visual Studio 용 Azure 서비스 인증 확장을 설치 하는 것이 좋습니다. 그러나이 기능은 Visual Studio 내에서 통합 되기 때문에 이제는 더 이상 사용 되지 않습니다. 따라서 이전 버전의 visual Studio 2017을 사용 하는 경우이 기능을 기본적으로 사용 하 고 Visual Studio 로그인 Id 자체를 사용 하 여 키 자격 증명 모음에 액세스할 수 있도록 VS 2017 15.6 이상으로 업데이트 하는 것이 좋습니다.
    >

4. 다음 NuGet 패키지를 프로젝트에 추가합니다.

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. 다음 코드를 Program.cs 파일에 추가합니다.

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
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
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Key Vault URL을 launchsettings.json 파일에 추가합니다. 환경 변수 이름인 *KEYVAULT_ENDPOINT*는 6단계에서 추가한 코드에서 정의됩니다.

    ![Key Vault URL을 프로젝트 환경 변수로 추가](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. 프로젝트 디버그를 시작합니다. 성공적으로 실행되어야 합니다.

## <a name="aspnet-and-net-applications"></a>ASP.NET 및 .NET 애플리케이션

.NET 4.7.1은 비밀이 코드 변경 없이 소스 제어 폴더 외부로 이동하도록 보장하는 Key Vault 및 비밀 구성 빌더를 지원합니다.
계속하려면 [.NET 4.7.1을 다운로드](https://www.microsoft.com/download/details.aspx?id=56115)하고 이전 버전의 .NET Framework를 사용하는 경우 애플리케이션을 마이그레이션합니다.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>소스 제어 폴더 외부에 있는 비밀 파일에 비밀 설정 저장
빠른 프로토타입을 작성하면서 Azure 리소스를 프로비전하지 않으려면 이 옵션을 사용하세요.

1. 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **사용자 암호 관리**를 선택 합니다. 이렇게 하면 urationBuilders에서 NuGet **Microsoft.Configuration.Config** 패키지를 설치 하 고, web.config 파일 외부에 비밀 설정을 저장 하기 위한 파일을 만들고, web.config 파일에 **configbuilders** 섹션을 추가 합니다.

2. 비밀 설정을 root 요소 아래에 배치 합니다. 예는 다음과 같습니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. appSettings 섹션에서 비밀 구성 빌더를 사용하는 것으로 지정합니다. 비밀 설정에 더미 값이 있는 항목이 있는지 확인 합니다.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. 앱을 디버그합니다. 성공적으로 실행되어야 합니다.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Azure Key Vault에 비밀 설정 저장
ASP.NET Core 섹션의 지침에 따라 프로젝트의 Key Vault를 구성합니다.

1. 다음 NuGet 패키지를 프로젝트에 설치합니다.
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. Web.config에서 Key Vault 구성 빌더를 정의 합니다. 이 섹션을 *appSettings* 섹션 앞에 배치 합니다. Key Vault 글로벌 Azure에 있는 경우 *vaultName* 를 Key Vault 이름으로 바꾸고, 소 버린 cloud를 사용 하는 경우 전체 URI로 바꿉니다.

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. appSettings 섹션에서 Key Vault 구성 빌더를 사용하는 것으로 지정합니다. 더미 값을 사용하여 비밀 설정에 대한 항목이 있도록 합니다.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. 프로젝트 디버그를 시작합니다. 성공적으로 실행되어야 합니다.
