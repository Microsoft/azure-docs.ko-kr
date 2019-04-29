---
title: .NET을 사용하여 Azure Key Vault에 서비스 간 인증
description: Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하여 .NET으로 Azure Key Vault에 인증합니다.
keywords: Azure Key Vault 인증 로컬 자격 증명
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 03/05/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 642c30c4df233476a8f649f7b5f30d0538b0e83f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635604"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET을 사용하여 Azure Key Vault에 서비스 간 인증

Azure Key Vault에 인증하려면 Azure AD(Active Directory) 자격 증명, 공유 암호 또는 인증서가 필요합니다. 이러한 자격 증명은 관리가 어려울 수 있어 자격 증명을 원본 또는 구성 파일에 포함하여 앱에 번들로 묶습니다.

.NET 라이브러리에 대한 `Microsoft.Azure.Services.AppAuthentication`은 이 문제를 단순화합니다. 개발자의 자격 증명을 사용하여 로컬 개발 동안 인증합니다. 나중에 Azure에 솔루션이 배포되면 라이브러리는 애플리케이션 자격 증명으로 자동으로 전환됩니다.  

로컬 개발 동안 개발자 자격 증명을 사용하는 것이 Azure AD 자격 증명을 만들거나 개발자 간에 자격 증명을 공유할 필요가 없으므로 더 안전합니다.

`Microsoft.Azure.Services.AppAuthentication` 라이브러리는 자동으로 인증을 관리하므로 순서대로 수행하면 자격 증명 대신 솔루션에 다시 초점을 맞출 수 있습니다.

`Microsoft.Azure.Services.AppAuthentication` 라이브러리는 Microsoft Visual Studio, Azure CLI 또는 Azure AD 통합 인증을 사용한 로컬 개발을 지원합니다. 관리 ID를 지원하는 Azure 리소스에 배포된 라이브러리는 [Azure 리소스용 관리 ID](../active-directory/msi-overview.md)를 자동으로 사용합니다. 코드 또는 구성을 변경할 필요가 없습니다. 또한 라이브러리를 사용하면 관리 ID를 사용할 수 없거나 로컬 개발 동안 개발자의 보안 컨텍스트를 확인할 수 없는 경우 Azure AD [클라이언트 자격 증명](../azure-resource-manager/resource-group-authenticate-service-principal.md)을 직접 사용할 수도 있습니다.

## <a name="using-the-library"></a>라이브러리 사용

.NET 애플리케이션의 경우 `Microsoft.Azure.Services.AppAuthentication` 패키지를 통해 관리 ID를 사용하는 것이 가장 간단한 방법입니다. 시작 방법은 다음과 같습니다.

1. 애플리케이션에 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 패키지의 참조를 추가합니다. 

2. 다음 코드를 추가합니다.

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` 클래스는 메모리에서 토큰을 캐시하여 만료 직전에 Azure AD에서 검색합니다. 따라서 더 이상 `GetAccessTokenAsync` 메서드를 호출하기 전에 만료를 확인할 필요가 없습니다. 토큰을 사용하려면 메서드를 호출하기만 하면 됩니다. 

`GetAccessTokenAsync` 메서드는 리소스 식별자가 필요합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스](../active-directory/msi-overview.md)를 참조하세요.

## <a name="samples"></a>샘플

다음 샘플에서는 작업 중인 `Microsoft.Azure.Services.AppAuthentication` 라이브러리를 보여 줍니다.

1. [관리 ID를 사용하여 런타임 시 Azure Key Vault에서 암호 검색](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [관리 ID를 사용하여 Azure VM에서 프로그래밍 방식으로 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

3. [.NET Core 샘플 및 관리 ID를 사용하여 Azure Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="local-development-authentication"></a>로컬 개발 인증

로컬 개발의 경우 다음 두 가지 기본 인증 시나리오가 있습니다.

- [Azure 서비스에 인증](#authenticating-to-azure-services)
- [사용자 지정 서비스에 인증](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Azure 서비스에 인증

로컬 컴퓨터는 Azure 리소스에 대한 관리 ID를 지원하지 않습니다.  따라서 `Microsoft.Azure.Services.AppAuthentication` 라이브러리가 개발자 자격 증명을 사용하여 로컬 개발 환경에서 실행됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID를 사용하여 OAuth 2.0 클라이언트 자격 증명 부여 흐름으로 전환합니다.  즉, 로컬 및 원격으로 동일한 코드를 걱정 없이 테스트할 수 있습니다.

로컬 개발의 경우 `AzureServiceTokenProvider`는 **Visual Studio**, **Azure CLI**(명령줄 인터페이스) 또는 **Azure AD 통합 인증**을 사용하여 토큰을 페치합니다. 각 옵션을 순차적으로 시도하여 라이브러리가 성공하는 첫 번째 옵션을 사용합니다. 작동하는 옵션이 없는 경우 자세한 정보와 함께 `AzureServiceTokenProviderException` 예외가 throw됩니다.

### <a name="authenticating-with-visual-studio"></a>Visual Studio를 사용하여 인증

Visual Studio를 사용 하 여 인증 하려면 다음 필수 조건이 있습니다.

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) 이상.

2. 합니다 [Visual Studio에 대 한 앱 인증 확장 프로그램](https://go.microsoft.com/fwlink/?linkid=862354)업데이트 6 이상 제품과 함께 제공 되 고 Visual Studio 2017 업데이트 5에 대 한 별도 확장으로 사용할 수 있습니다. 업데이트 6 이상, Visual Studio 설치 관리자 내에서 Azure 개발 도구를 선택 하 여 앱 인증 확장 프로그램의 설치를 확인할 수 있습니다.
 
Visual Studio에 로그인 하 고 사용 하 여 **도구가**&nbsp;>&nbsp;**옵션**&nbsp;>&nbsp;**Azure 서비스 인증** 로컬 개발에 대 한 계정을 선택 합니다. 

Visual Studio를 사용하여 토큰 공급자 파일 관련 오류와 같은 문제가 발생되면 다음 단계를 주의 깊게 검토합니다. 

개발자 토큰을 다시 인증해야 할 수도 있습니다. 이렇게 하려면 **도구**&nbsp;>&nbsp;**옵션**>**Azure&nbsp;서비스&nbsp;인증** 으로 이동하여 선택한 계정 아래 **다시 인증** 링크를 찾습니다.  링크를 선택하여 인증합니다. 

### <a name="authenticating-with-azure-cli"></a>Azure CLI를 사용하여 인증

로컬 개발에 Azure CLI를 사용하려면:

1. [Azure CLI v2.0.12](/cli/azure/install-azure-cli) 이상을 설치합니다. 이전 버전을 업그레이드합니다. 

2. **az login**을 사용하여 Azure에 로그인합니다.

`az account get-access-token`을 사용하여 액세스를 확인합니다.  오류가 발생할 경우 1단계가 성공적으로 완료되었는지 확인합니다. 

Azure CLI가 기본 디렉터리에 설치되지 않은 경우 `AzureServiceTokenProvider`가 Azure CLI 경로를 찾을 수 없다는 오류가 보고될 수 있습니다.  사용 된 **AzureCLIPath** 환경 변수를 Azure CLI 설치 폴더를 정의 합니다. `AzureServiceTokenProvider`는 필요한 경우 **AzureCLIPath** 환경 변수에 지정된 디렉터리를 **Path** 환경 변수에 추가합니다.

여러 계정을 사용하여 Azure CLI에 로그인하거나 계정에 여러 구독에 대한 액세스 권한이 있는 경우 특정 구독이 사용되도록 지정해야 합니다.  이렇게 하려면 다음을 사용합니다.

```
az account set --subscription <subscription-id>
```

이 명령은 실패한 경우에만 출력을 생성합니다.  현재의 계정 설정을 확인하려면 다음을 사용합니다.

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Azure AD 통합 인증을 사용하여 인증

Azure AD 인증을 사용하려면 다음을 확인합니다.

- 온-프레미스 Active Directory를 [Azure AD로 동기화](../active-directory/connect/active-directory-aadconnect.md).

- 코드가 도메인에 가입된 컴퓨터에서 실행 중입니다.


### <a name="authenticating-to-custom-services"></a>사용자 지정 서비스에 인증

Azure 서비스를 사용하면 사용자와 애플리케이션에 모두 액세스할 수 있으므로 서비스에서 Azure 서비스를 호출하는 경우 이전 단계가 작동합니다.  

사용자 지정 서비스를 호출하는 서비스를 만들 경우 로컬 개발 인증에 Azure AD 클라이언트 자격 증명을 사용합니다.  두 가지 옵션이 있습니다. 

1.  서비스 주체를 사용하여 Azure에 로그인:

    1.  [서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Azure CLI를 사용하여 로그인:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        서비스 주체에 구독에 대한 액세스 권한이 없을 수 있으므로 `--allow-no-subscriptions` 인수를 사용합니다.

2.  환경 변수를 사용하여 서비스 주체 세부 사항을 지정합니다.  자세한 내용은 [서비스 주체를 사용하여 애플리케이션 실행](#running-the-application-using-a-service-principal)을 참조하세요.

Azure에 로그인하고 나면 `AzureServiceTokenProvider`는 서비스 주체를 사용하여 로컬 개발에 대한 토큰을 검색합니다.

이 경우는 로컬 개발에만 적용됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID 인증으로 전환됩니다.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>관리 되는 id 또는 사용자 할당 id를 사용 하 여 응용 프로그램 실행 

Azure App Service 또는 활성화된 관리 ID를 사용하는 Azure VM에서 코드를 실행하는 경우 라이브러리는 자동으로 관리 ID를 사용합니다. 코드 변경은 필요하지 않습니다. 

사용자 할당 id를 사용 하 여 인증할 수 있습니다. 사용자 할당 id에 대 한 자세한 내용은 참조 하세요. [Azure 리소스에 대 한 Id에 대 한 관리 하는 방법](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)합니다. 연결 문자열에 지정 된 된 [연결 문자열을 지 원하는](#connection-string-support) 아래의 섹션입니다.

## <a name="running-the-application-using-a-service-principal"></a>서비스 주체를 사용하여 애플리케이션 실행 

인증할 Azure AD 클라이언트 자격 증명을 만들어야 할 수 있습니다. 일반적인 예제는 다음을 포함합니다.

1. 코드가 로컬 개발 환경에서 실행되지만 개발자의 ID 아래에서는 실행되지 않습니다.  예를 들어 Service Fabric은 로컬 개발에 [NetworkService 계정](../service-fabric/service-fabric-application-secret-management.md)을 사용합니다.
 
2. 로컬 개발 환경에서 코드가 실행되고 사용자 지정 서비스에 인증하므로 개발자 ID를 사용할 수 없습니다. 
 
3. 코드가 Azure 리소스에 대해 관리 ID를 아직 지원하지 않는 Azure 계산 리소스(예: Azure Batch)에서 실행 중입니다.

인증서를 사용하여 Azure AD에 로그인하려면:

1. [서비스 주체 인증서](../azure-resource-manager/resource-group-authenticate-service-principal.md)를 만듭니다. 

2. *LocalMachine* 또는 *CurrentUser* 저장소에 인증서를 배포합니다. 

3. **AzureServicesAuthConnectionString**이라는 환경 변수를 설정하여 다음을 수행합니다.

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    *{AppId}*, *{TenantId}* 및 *{Thumbprint}* 를 1단계에서 생성된 값으로 바꿉니다. 배포 계획에 따라 *{CertificateStore}* 를 `LocalMachine` 또는 `CurrentUser`로 바꿉니다.

4. 애플리케이션을 실행합니다. 

Azure AD 공유 암호 자격 증명을 사용하여 로그인하려면:

1. [암호를 사용하는 서비스 주체](../azure-resource-manager/resource-group-authenticate-service-principal.md)를 만들고 Key Vault에 대한 액세스 권한을 부여합니다. 

2. **AzureServicesAuthConnectionString**이라는 환경 변수를 설정하여 다음을 수행합니다.

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    _{AppId}_, _{TenantId}_ 및 _{ClientSecret}_ 을 1단계에서 생성된 값으로 바꿉니다.

3. 애플리케이션을 실행합니다. 

모두가 올바로 설정되면 더 이상 코드를 변경할 필요가 없습니다.  `AzureServiceTokenProvider`는 환경 변수 및 인증서를 사용하여 Azure AD에 인증합니다. 

## <a name="connection-string-support"></a>연결 문자열 지원

기본적으로 `AzureServiceTokenProvider`는 여러 메서드를 사용하여 토큰을 검색합니다. 

프로세스를 제어하려면 `AzureServiceTokenProvider` 생성자에 전달되거나 *AzureServicesAuthConnectionString* 환경 변수에 지정된 연결 문자열을 사용합니다. 

다음과 같은 옵션이 지원됩니다.

| 연결&nbsp;문자열&nbsp;옵션 | 시나리오 | 설명|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 로컬 개발 | AzureServiceTokenProvider는 AzureCli를 사용하여 토큰을 가져옵니다. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 로컬 개발 | AzureServiceTokenProvider는 Visual Studio를 사용하여 토큰을 가져옵니다. |
| `RunAs=CurrentUser` | 로컬 개발 | AzureServiceTokenProvider는 Azure AD 통합 인증을 사용하여 토큰을 가져옵니다. |
| `RunAs=App` | [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider는 관리 ID를 사용하여 토큰을 가져옵니다. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure 리소스에 대 한 사용자 할당 id](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider는 사용자 할당 id를 사용 하 여 토큰을 가져옵니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`   | 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 서비스 주체 |`AzureServiceTokenProvider`는 암호를 사용하여 Azure AD에서 토큰을 가져옵니다. |



## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/index.yml)에 대해 자세히 알아보세요.
- [Azure AD 인증 시나리오](../active-directory/develop/active-directory-authentication-scenarios.md)에 대해 자세히 알아보기.
