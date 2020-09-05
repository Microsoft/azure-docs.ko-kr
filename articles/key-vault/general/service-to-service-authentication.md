---
title: .NET을 사용하여 Azure Key Vault에 서비스 간 인증
description: Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하여 .NET으로 Azure Key Vault에 인증합니다.
keywords: Azure Key Vault 인증 로컬 자격 증명
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 08/08/2020
ms.topic: how-to
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: 860f9b0e49423b5d144d56ecd965153f7a362d87
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180918"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET을 사용하여 Azure Key Vault에 서비스 간 인증

> [!NOTE]
> **Microsoft. Azure. AppAuthentication** 은 새로운 Key Vault SDK와 함께 사용 하는 것이 더 이상 권장 되지 않습니다. 이는 .NET, Java, TypeScript 및 Python에 사용할 수 있는 새 Azure Id 라이브러리 **DefaultAzureCredentials** 대체 되었으며 모든 새 개발에 사용 해야 합니다. 자세한 내용은 [인증 및 AZURE SDK에서](https://devblogs.microsoft.com/azure-sdk/authentication-and-the-azure-sdk/)찾을 수 있습니다.

Azure Key Vault에 인증 하려면 공유 암호 또는 인증서 인 Azure Active Directory (Azure AD) 자격 증명이 필요 합니다.

이러한 자격 증명을 관리 하는 것은 어려울 수 있습니다. 원본 또는 구성 파일에 자격 증명을 포함 하 여 앱에 자격 증명을 번들로 묶는 것이 좋습니다. .NET 라이브러리에 대한 `Microsoft.Azure.Services.AppAuthentication`은 이 문제를 단순화합니다. 개발자의 자격 증명을 사용하여 로컬 개발 동안 인증합니다. 나중에 Azure에 솔루션이 배포되면 라이브러리는 애플리케이션 자격 증명으로 자동으로 전환됩니다. 로컬 개발 중에 개발자 자격 증명을 사용 하는 것은 개발자 간에 Azure AD 자격 증명을 만들거나 자격 증명을 공유할 필요가 없기 때문에 더 안전 합니다.

`Microsoft.Azure.Services.AppAuthentication`라이브러리는 자동으로 인증을 관리 하므로 자격 증명이 아닌 솔루션에 집중할 수 있습니다. Microsoft Visual Studio, Azure CLI 또는 Azure AD 통합 인증을 사용 하 여 로컬 개발을 지원 합니다. 관리 ID를 지원하는 Azure 리소스에 배포된 라이브러리는 [Azure 리소스용 관리 ID](../../active-directory/msi-overview.md)를 자동으로 사용합니다. 코드 또는 구성을 변경할 필요가 없습니다. 또한 라이브러리는 관리 되는 id를 사용할 수 없는 경우 또는 로컬 개발 중에 개발자의 보안 컨텍스트를 확인할 수 없는 경우 Azure AD [클라이언트 자격 증명](../../azure-resource-manager/resource-group-authenticate-service-principal.md) 을 직접 사용 하도록 지원 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Visual studio 2019](https://www.visualstudio.com/downloads/) 또는 [visual studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Visual Studio 용 응용 프로그램 인증 확장 프로그램은 Visual Studio 2017 업데이트 5에 대 한 별도의 확장으로 제공 되 고 업데이트 6 이상의 제품과 함께 제공 됩니다. 업데이트 6 이상에서는 Visual Studio 설치 관리자에서 Azure 개발 도구를 선택 하 여 앱 인증 확장의 설치를 확인할 수 있습니다.

## <a name="using-the-library"></a>라이브러리 사용

.NET 애플리케이션의 경우 `Microsoft.Azure.Services.AppAuthentication` 패키지를 통해 관리 ID를 사용하는 것이 가장 간단한 방법입니다. 시작 방법은 다음과 같습니다.

1. **도구**  >  **nuget 패키지 관리자**  >  **솔루션용 nuget 패키지 관리** 를 선택 하 여 프로젝트에 대 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 한 참조를 프로젝트에 추가 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) 합니다.

1. 다음 코드를 추가합니다.

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

는 `GetAccessTokenAsync` `AzureServiceTokenProvider` 토큰을 메모리에 캐시 하 고 만료 직전에 Azure AD에서 검색 하기 때문에 메서드를 호출 하기 전에 토큰의 만료를 확인할 필요가 없습니다. 

`GetAccessTokenAsync` 메서드는 리소스 식별자가 필요합니다. Microsoft Azure services에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/msi-overview.md)을 참조 하세요.

## <a name="local-development-authentication"></a>로컬 개발 인증

로컬 개발을 위해 [Azure 서비스에](#authenticating-to-azure-services)인증 하 고 [사용자 지정 서비스에](#authenticating-to-custom-services)인증 하는 두 가지 기본 인증 시나리오가 있습니다.

### <a name="authenticating-to-azure-services"></a>Azure 서비스에 인증

로컬 컴퓨터는 Azure 리소스에 대 한 관리 되는 id를 지원 하지 않습니다. 따라서 `Microsoft.Azure.Services.AppAuthentication` 라이브러리가 개발자 자격 증명을 사용하여 로컬 개발 환경에서 실행됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID를 사용하여 OAuth 2.0 클라이언트 자격 증명 부여 흐름으로 전환합니다. 이 방법은 걱정 없이 동일한 코드를 로컬 및 원격으로 테스트할 수 있음을 의미 합니다.

로컬 개발의 경우 `AzureServiceTokenProvider`는 **Visual Studio**, **Azure CLI**(명령줄 인터페이스) 또는 **Azure AD 통합 인증**을 사용하여 토큰을 페치합니다. 각 옵션을 순차적으로 시도하여 라이브러리가 성공하는 첫 번째 옵션을 사용합니다. 작동하는 옵션이 없는 경우 자세한 정보와 함께 `AzureServiceTokenProviderException` 예외가 throw됩니다.

#### <a name="authenticating-with-visual-studio"></a>Visual Studio를 사용하여 인증

Visual Studio를 사용 하 여 인증 하려면:

1. Visual Studio에 로그인 하 고 **도구** &nbsp; > &nbsp; **옵션** 을 사용 하 여 **옵션**을 엽니다.

1. **Azure 서비스 인증**을 선택 하 고, 로컬 개발을 위한 계정을 선택 하 고, **확인**을 선택 합니다.

토큰 공급자 파일과 관련 된 오류와 같이 Visual Studio를 사용 하 여 문제가 발생 하는 경우 앞의 단계를 신중 하 게 검토 합니다.

개발자 토큰을 다시 인증 해야 할 수도 있습니다. 이렇게 하려면 **도구** &nbsp; > &nbsp; **옵션**을 선택한 다음, **Azure &nbsp; 서비스 &nbsp; 인증**을 선택 합니다. 선택한 계정에서 **다시 인증** 링크를 찾습니다. 링크를 선택하여 인증합니다.

#### <a name="authenticating-with-azure-cli"></a>Azure CLI를 사용하여 인증

로컬 개발에 Azure CLI을 사용 하려면 [Azure CLI v 2.0.12 이상을](/cli/azure/install-azure-cli) 이상 버전을 사용 해야 합니다.

Azure CLI을 사용 하려면:

1. Windows 작업 표시줄에서 Azure CLI를 검색 하 여 **Microsoft Azure 명령 프롬프트**를 엽니다.

1. Azure Portal: *az login* 에 로그인 하 여 Azure에 로그인 합니다.

1. *Az account get-help--resource https: \/ /vault.azure.net*를 입력 하 여 액세스를 확인 합니다. 오류가 표시 되는 경우 올바른 버전의 Azure CLI 올바르게 설치 되었는지 확인 합니다.

   Azure CLI 기본 디렉터리에 설치 되지 않은 경우 Azure CLI 경로를 찾을 수 없는 오류 보고가 표시 될 수 있습니다 `AzureServiceTokenProvider` . **Azureclipath** 환경 변수를 사용 하 여 Azure CLI 설치 폴더를 정의 합니다. `AzureServiceTokenProvider`는 필요한 경우 **AzureCLIPath** 환경 변수에 지정된 디렉터리를 **Path** 환경 변수에 추가합니다.

1. 여러 계정을 사용 하 여 Azure CLI에 로그인 했거나 계정이 여러 구독에 액세스할 수 있는 경우 사용할 구독을 지정 해야 합니다. 명령 *az account set--subscription <subscription-id>* 을 입력 합니다.

이 명령은 실패한 경우에만 출력을 생성합니다. 현재 계정 설정을 확인 하려면 명령을 입력 `az account list` 합니다.

#### <a name="authenticating-with-azure-ad-authentication"></a>Azure AD 인증을 사용 하 여 인증

Azure AD 인증을 사용하려면 다음을 확인합니다.

- 온-프레미스 Active Directory는 Azure AD에 동기화 됩니다. 자세한 내용은 [Azure Active Directory를 사용 하는 하이브리드 id 란?](../../active-directory/connect/active-directory-aadconnect.md)을 참조 하세요.

- 도메인에 가입 된 컴퓨터에서 코드가 실행 되 고 있습니다.

### <a name="authenticating-to-custom-services"></a>사용자 지정 서비스에 인증

Azure 서비스를 사용하면 사용자와 애플리케이션에 모두 액세스할 수 있으므로 서비스에서 Azure 서비스를 호출하는 경우 이전 단계가 작동합니다.

사용자 지정 서비스를 호출하는 서비스를 만들 경우 로컬 개발 인증에 Azure AD 클라이언트 자격 증명을 사용합니다. 두 가지 옵션 중이 있습니다.

- 서비스 주체를 사용하여 Azure에 로그인:

    1. 서비스 주체를 생성합니다. 자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

    1. Azure CLI를 사용 하 여 다음 명령을 사용 하 여 로그인 합니다.

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        서비스 주체에 구독에 대한 액세스 권한이 없을 수 있으므로 `--allow-no-subscriptions` 인수를 사용합니다.

- 환경 변수를 사용하여 서비스 주체 세부 사항을 지정합니다. 자세한 내용은 [서비스 주체를 사용 하 여 응용 프로그램 실행](#running-the-application-using-a-service-principal)을 참조 하세요.

Azure에 로그인 한 후에는에서 `AzureServiceTokenProvider` 서비스 주체를 사용 하 여 로컬 개발에 대 한 토큰을 검색 합니다.

이 방법은 로컬 개발에만 적용 됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID 인증으로 전환됩니다.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>관리 id 또는 사용자 할당 id를 사용 하 여 응용 프로그램 실행

Azure App Service 또는 활성화된 관리 ID를 사용하는 Azure VM에서 코드를 실행하는 경우 라이브러리는 자동으로 관리 ID를 사용합니다. 코드를 변경 하지 않아도 되지만 관리 되는 id에는 키 자격 증명 모음에 대 한 *GET* 권한이 있어야 합니다. 키 자격 증명 모음 *액세스 정책을*통해 관리 Id에 *GET* 권한을 부여할 수 있습니다.

또는 사용자 할당 id를 사용 하 여 인증할 수 있습니다. 사용자 할당 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 Id 정보](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 참조 하세요. 사용자 할당 id를 사용 하 여 인증 하려면 연결 문자열에 사용자 할당 id의 클라이언트 ID를 지정 해야 합니다. 연결 문자열은 [연결 문자열 지원](#connection-string-support)에서 지정 됩니다.

## <a name="running-the-application-using-a-service-principal"></a>서비스 주체를 사용하여 애플리케이션 실행

인증할 Azure AD 클라이언트 자격 증명을 만들어야 할 수 있습니다. 다음 예에서는 이러한 상황이 발생할 수 있습니다.

- 코드가 로컬 개발 환경에서 실행되지만 개발자의 ID 아래에서는 실행되지 않습니다. 예를 들어 Service Fabric은 로컬 개발에 [NetworkService 계정](../../service-fabric/service-fabric-application-secret-management.md)을 사용합니다.

- 로컬 개발 환경에서 코드가 실행되고 사용자 지정 서비스에 인증하므로 개발자 ID를 사용할 수 없습니다.

- Azure Batch와 같은 Azure 리소스에 대 한 관리 되는 id를 아직 지원 하지 않는 Azure 계산 리소스에서 코드가 실행 되 고 있습니다.

서비스 주체를 사용 하 여 응용 프로그램을 실행 하는 세 가지 기본 방법이 있습니다. 이러한 항목 중 하나를 사용 하려면 먼저 서비스 주체를 만들어야 합니다. 자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>로컬 키 저장소의 인증서를 사용 하 여 Azure AD에 로그인

1. Azure CLI [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 서비스 사용자 인증서를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    이 명령은 홈 디렉터리에 저장 된 pem 파일 (개인 키)을 만듭니다. 이 인증서를 *LocalMachine* 또는 *CurrentUser* 저장소에 배포 합니다.

    > [!Important]
    > CLI 명령은 pem 파일을 생성 하지만 Windows는 PFX 인증서를 기본 으로만 지원 합니다. 대신 PFX 인증서를 생성 하려면 [자체 서명 된 인증서를 사용 하 여 서비스 주체 만들기](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)에 표시 된 PowerShell 명령을 사용 합니다. 이러한 명령은 인증서도 자동으로 배포 합니다.

1. **Azureservicesauthconnectionstring** 이라는 환경 변수를 다음 값으로 설정 합니다.

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    *{AppId}*, *{TenantId}* 및 *{Thumbprint}* 를 1단계에서 생성된 값으로 바꿉니다. 배포 계획에 따라 *{CertificateStore}* 을 *LocalMachine*' 또는 *CurrentUser*로 바꿉니다.

1. 애플리케이션을 실행합니다.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>공유 암호 자격 증명을 사용 하 여 Azure AD에 로그인

1. --Sdk-auth 매개 변수와 함께 Azure CLI [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 암호를 사용 하 여 서비스 주체 인증서를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. **Azureservicesauthconnectionstring** 이라는 환경 변수를 다음 값으로 설정 합니다.

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    _{AppId}_, _{TenantId}_ 및 _{ClientSecret}_ 을 1단계에서 생성된 값으로 바꿉니다.

1. 애플리케이션을 실행합니다.

모두가 올바로 설정되면 더 이상 코드를 변경할 필요가 없습니다. `AzureServiceTokenProvider`는 환경 변수 및 인증서를 사용하여 Azure AD에 인증합니다.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Key Vault에서 인증서를 사용 하 여 Azure AD에 로그인

이 옵션을 사용 하면 서비스 주체의 클라이언트 인증서를 Key Vault에 저장 하 고 서비스 주체 인증에 사용할 수 있습니다. 다음 시나리오에이 옵션을 사용할 수 있습니다.

- 명시적 서비스 주체를 사용 하 여 인증 하 고 키 자격 증명 모음에서 서비스 주체 자격 증명을 안전 하 게 유지 하려는 로컬 인증. 개발자 계정에는 키 자격 증명 모음에 대 한 액세스 권한이 있어야 합니다.

- 명시적 자격 증명을 사용 하 고 키 자격 증명 모음에서 서비스 주체 자격 증명을 안전 하 게 유지 하려는 Azure의 인증입니다. 이 옵션은 테 넌 트 간 시나리오에 사용할 수 있습니다. 관리 id는 key vault에 대 한 액세스 권한이 있어야 합니다.

관리 id 또는 개발자 id에는 Key Vault에서 클라이언트 인증서를 검색할 수 있는 권한이 있어야 합니다. AppAuthentication 라이브러리는 검색 된 인증서를 서비스 주체의 클라이언트 자격 증명으로 사용 합니다.

서비스 주체 인증에 클라이언트 인증서를 사용 하려면 다음을 수행 합니다.

1. 서비스 사용자 인증서를 만들어 Key Vault에 자동으로 저장 합니다. Azure CLI [az ad sp create-- \<keyvaultname> cert \<certificatename> ](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) --cert--cert---------------------------

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    인증서 식별자는 다음 형식의 URL이 됩니다. `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. `{KeyVaultCertificateSecretIdentifier}`이 연결 문자열에서을 인증서 식별자로 바꿉니다.

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    예를 들어 키 자격 증명 모음을 *Mykeyvault* 라고 하 고 *myCert*이라는 인증서를 만든 경우 인증서 식별자는 다음과 같습니다.

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>연결 문자열 지원

기본적으로는 `AzureServiceTokenProvider` 토큰을 검색 하기 위해 다음 인증 방법을 순서 대로 시도 합니다.

- [Azure 리소스에 대 한 관리 id](../..//active-directory/managed-identities-azure-resources/overview.md)
- Visual Studio 인증
- [Azure CLI 인증](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Windows 통합 인증](/aspnet/web-api/overview/security/integrated-windows-authentication)

프로세스를 제어하려면 `AzureServiceTokenProvider` 생성자에 전달되거나 *AzureServicesAuthConnectionString* 환경 변수에 지정된 연결 문자열을 사용합니다.  다음과 같은 옵션이 지원됩니다.

| 연결 문자열 옵션 | 시나리오 | 의견|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 로컬 개발 | `AzureServiceTokenProvider` 는 AzureCli를 사용 하 여 토큰을 가져옵니다. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 로컬 개발 | `AzureServiceTokenProvider` Visual Studio를 사용 하 여 토큰을 가져옵니다. |
| `RunAs=CurrentUser` | 로컬 개발 | .NET Core에서는 지원 되지 않습니다. `AzureServiceTokenProvider` Azure AD 통합 인증을 사용 하 여 토큰을 가져옵니다. |
| `RunAs=App` | [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` 관리 id를 사용 하 여 토큰을 가져옵니다. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure 리소스에 대 한 사용자 할당 id](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` 사용자 할당 id를 사용 하 여 토큰을 가져옵니다. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 사용자 지정 서비스 인증 | `KeyVaultCertificateSecretIdentifier` 인증서의 암호 식별자입니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 서비스 주체 |`AzureServiceTokenProvider`는 암호를 사용하여 Azure AD에서 토큰을 가져옵니다. |

## <a name="samples"></a>샘플

`Microsoft.Azure.Services.AppAuthentication`작업 중인 라이브러리를 확인 하려면 다음 코드 샘플을 참조 하세요.

- [관리 ID를 사용하여 런타임 시 Azure Key Vault에서 암호 검색](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [관리 ID를 사용하여 Azure VM에서 프로그래밍 방식으로 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [.NET Core 샘플 및 관리 ID를 사용하여 Azure Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>AppAuthentication 문제 해결

### <a name="common-issues-during-local-development"></a>로컬 개발 중 일반적인 문제

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI 설치 되어 있지 않거나 로그인 하지 않았거나 최신 버전이 없습니다.

*Az account get-access 토큰* 을 실행 하 여 Azure CLI 토큰이 표시 되는지 확인 합니다. **이러한 프로그램이 없는**경우 [최신 버전의 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치 합니다. 로그인하라는 메시지가 표시될 수 있습니다.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider Azure CLI에 대 한 경로를 찾을 수 없습니다.

AzureServiceTokenProvider는 기본 설치 위치에서 Azure CLI를 찾습니다. Azure CLI를 찾을 수 없는 경우 환경 변수 **Azureclipath** 를 Azure CLI 설치 폴더로 설정 합니다. AzureServiceTokenProvider는 Path 환경 변수에 환경 변수를 추가 합니다.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>여러 계정을 사용 하 여 Azure CLI에 로그인 했거나, 동일한 계정에서 여러 테 넌 트의 구독에 액세스할 수 있거나, 로컬 개발 중에 호출 하려고 할 때 액세스 거부 오류가 발생 합니다.

Azure CLI를 사용 하 여 기본 구독을 사용 하려는 계정을 포함 하는 구독으로 설정 합니다. 구독은 액세스 하려는 리소스와 같은 테 넌 트에 있어야 합니다. **az account set--subscription [subscription-id]**. 출력이 표시 되지 않으면 성공 합니다. 이제 **az account list**를 사용 하 여 올바른 계정이 기본 계정 인지 확인 합니다.

### <a name="common-issues-across-environments"></a>환경 간의 일반적인 문제

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>무단 액세스, 액세스 거부, 사용 권한 없음 또는 유사한 오류

사용된 보안 주체가 액세스하려는 리소스에 대한 액세스 권한이 없습니다. 사용자 계정이 나 App Service의 MSI "참가자"에 게 리소스에 대 한 액세스 권한을 부여 합니다. 이 중 하나는 로컬 컴퓨터에서 샘플을 실행 하 고 있는지 Azure에 App Service에 배포 했는지에 따라 달라 집니다. 주요 자격 증명 모음과 같은 일부 리소스에는 사용자, 앱, 그룹 등의 보안 주체에 대 한 액세스 권한을 부여 하는 데 사용 하는 고유한 [액세스 정책도](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) 있습니다.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Azure App Service에 배포할 때 발생 하는 일반적인 문제

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>관리 id가 App Service에 설정 되어 있지 않습니다.

[Kudu debug 콘솔](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)을 사용 하 여 MSI_ENDPOINT 환경 변수를 확인 하 고 MSI_SECRET 있습니다. 이러한 환경 변수가 없는 경우 관리 Id는 App Service에서 사용 하도록 설정 되지 않습니다.

### <a name="common-issues-when-deployed-locally-with-iis"></a>IIS를 사용 하 여 로컬로 배포할 때 발생 하는 일반적인 문제

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>IIS에서 앱을 디버그할 때 토큰을 검색할 수 없습니다.

기본적으로 AppAuth는 IIS의 다른 사용자 컨텍스트에서 실행 됩니다. 개발자 id를 사용 하 여 액세스 토큰을 검색할 수 있는 권한이 없는 이유 때문입니다. 다음 두 단계를 통해 사용자 컨텍스트를 사용 하 여 실행 하도록 IIS를 구성할 수 있습니다.
- 현재 사용자 계정으로 실행 되도록 웹 앱에 대 한 응용 프로그램 풀을 구성 합니다. 자세한 내용은 [여기](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)를 참조하세요.
- "SetProfileEnvironment"를 "True"로 구성 합니다. 자세한 내용은 [여기](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)를 참조 하세요. 

    - % Windir% \System32\inetsrv\config\applicationHost.config (으)로 이동
    - "SetProfileEnvironment"를 검색 합니다. "False"로 설정 되어 있으면 "True"로 변경 합니다. 존재 하지 않는 경우 processModel 요소 ()에 특성으로 추가 하 /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment 고이를 "True"로 설정 합니다.

- [Azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/index.yml)에 대해 자세히 알아보세요.
- [Azure AD 인증 시나리오](../../active-directory/develop/active-directory-authentication-scenarios.md)에 대해 자세히 알아보기.
