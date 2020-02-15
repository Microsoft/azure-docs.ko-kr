---
title: 클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 Azure AD에서 토큰 획득
titleSuffix: Azure Storage
description: Azure Active Directory를 사용 하 여 클라이언트 응용 프로그램 내에서 인증 하 고, OAuth 2.0 토큰을 획득 하 고, Azure Blob storage 및 Queue storage에 대 한 요청에 권한을 부여 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157451"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 Azure AD에서 토큰 획득

Azure Blob 저장소 또는 큐 저장소에서 Azure Active Directory (Azure AD)를 사용 하는 경우의 주요 이점은 자격 증명을 코드에 더 이상 저장할 필요가 없다는 점입니다. 대신 Microsoft id 플랫폼 (이전의 Azure AD)에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Blob 저장소 또는 큐 저장소에 대 한 요청에 권한을 부여할 수 있습니다.

이 문서에서는 Microsoft id platform 2.0 인증을 위해 네이티브 응용 프로그램 또는 웹 응용 프로그램을 구성 하는 방법을 보여 줍니다. 코드 예제에서는 .NET을 사용하지만 다른 언어는 비슷한 접근 방식을 사용합니다. Microsoft id 플랫폼 2.0에 대 한 자세한 내용은 v2.0 [(microsoft identity platform) 개요](../../active-directory/develop/v2-overview.md)를 참조 하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD 보안 주체에 역할 할당

Azure Storage 애플리케이션에서 보안 주체를 인증하려면 먼저 해당 보안 주체에 대해 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Storage는 컨테이너 및 큐에 대 한 사용 권한을 포함 하는 기본 제공 RBAC 역할을 정의 합니다. RBAC 역할이 보안 주체에게 할당되면 해당 보안 주체는 해당 리소스에 대한 액세스 권한이 부여됩니다. 자세한 내용은 [Azure Blob에 대 한 액세스 권한 관리 및 RBAC를 사용 하 여 큐 데이터](storage-auth-aad-rbac.md)를 참조 하세요.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록

Azure AD를 사용 하 여 저장소 리소스에 대 한 액세스 권한을 부여 하는 첫 번째 단계는 클라이언트 응용 프로그램을 [Azure Portal](https://portal.azure.com)의 azure ad 테 넌 트에 등록 하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 응용 프로그램에 대 한 정보를 Azure AD에 제공 합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(*애플리케이션 ID*라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

Azure Storage 응용 프로그램을 등록 하려면 [퀵 스타트: Microsoft id 플랫폼에 응용 프로그램 등록](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)에 표시 된 단계를 따르세요. 다음 이미지는 웹 응용 프로그램을 등록 하는 일반적인 설정을 보여 줍니다.

![Azure AD를 사용 하 여 저장소 응용 프로그램을 등록 하는 방법을 보여 주는 스크린샷](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 **리디렉션 URI**에 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우이 값은 실제 URL 일 필요가 없습니다. 웹 응용 프로그램의 경우 리디렉션 URI는 토큰을 제공 하는 URL을 지정 하므로 유효한 URI 여야 합니다.

애플리케이션을 등록한 후에 **설정** 아래에 애플리케이션 ID(또는 클라이언트 ID)가 표시됩니다.

![클라이언트 ID를 보여 주는 스크린샷](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Azure Storage에 등록된 앱 사용 권한 부여

다음으로 응용 프로그램에 Azure Storage Api를 호출할 수 있는 권한을 부여 합니다. 이 단계를 통해 응용 프로그램은 Azure AD로 Azure Storage 요청에 권한을 부여할 수 있습니다.

1. 등록 된 응용 프로그램에 대 한 **개요** 페이지에서 **API 권한 보기**를 선택 합니다.
1. **API 사용 권한** 섹션에서 **권한 추가** 를 선택 하 고 **Microsoft api**를 선택 합니다.
1. 결과 목록에서 **Azure Storage** 을 선택 하 여 **API 권한 요청** 창을 표시 합니다.
1. **응용 프로그램에 필요한 사용 권한 유형**에서 사용 가능한 권한 유형이 **위임 된 권한**인지 확인 합니다. 이 옵션은 기본적으로 선택 되어 있습니다.
1. **API 권한 요청** 창의 **권한 선택** 섹션에서 **user_impersonation**옆의 확인란을 선택 하 고 **사용 권한 추가**를 클릭 합니다.

    ![저장소에 대 한 사용 권한을 보여 주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-1.png)

이제 **API 권한** 창에는 등록 된 Azure AD 응용 프로그램이 Microsoft Graph 및 Azure Storage에 모두 액세스할 수 있는 것으로 표시 됩니다. Azure AD에 앱을 처음 등록할 때 Microsoft Graph에 대 한 권한이 자동으로 부여 됩니다.

![앱 사용 권한 등록을 보여 주는 스크린샷](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램은 토큰을 요청할 때 해당 id를 증명 하기 위해 클라이언트 암호가 필요 합니다. 클라이언트 암호를 추가 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 앱 등록으로 이동 합니다.
1. **인증서 & 암호** 설정을 선택 합니다.
1. **클라이언트 암호**에서 **새 클라이언트 암호** 를 클릭 하 여 새 암호를 만듭니다.
1. 비밀에 대 한 설명을 제공 하 고 원하는 만료 간격을 선택 합니다.
1. 새 암호의 값을 안전한 위치에 즉시 복사 합니다. 전체 값은 한 번만 표시 됩니다.

    ![클라이언트 암호를 보여 주는 스크린샷](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>토큰 획득을 위한 클라이언트 라이브러리

응용 프로그램을 등록 하 고 Azure Blob 저장소 또는 큐 저장소의 데이터에 액세스할 수 있는 권한을 부여 하면 응용 프로그램에 보안 주체를 인증 하 고 OAuth 2.0 토큰을 획득 하는 코드를 추가할 수 있습니다. 토큰을 인증 하 고 얻으려면 [Microsoft id 플랫폼 인증 라이브러리나](../../active-directory/develop/reference-v2-libraries.md) openid connect Connect 1.0을 지 원하는 다른 오픈 소스 라이브러리 중 하나를 사용할 수 있습니다. 그러면 응용 프로그램에서 액세스 토큰을 사용 하 여 Azure Blob 저장소 또는 큐 저장소에 대 한 요청을 인증할 수 있습니다.

토큰 획득을 지 원하는 시나리오 목록은 [Microsoft 인증 라이브러리 콘텐츠의](/azure/active-directory/develop/msal-overview) [인증 흐름](/en-us/azure/active-directory/develop/msal-authentication-flows) 섹션을 참조 하세요.

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD를 사용하여 인증하기 위해 잘 알려진 값

Azure AD를 사용하여 보안 주체를 인증하려면 코드에서 잘 알려진 몇 가지 값을 포함해야 합니다.

### <a name="azure-ad-authority"></a>Azure AD 권한

Microsoft 퍼블릭 클라우드의 경우 기본 Azure AD 권한은 다음과 같습니다. 여기서 *tenant-id*는 Active Directory 테넌트 ID(또는 디렉터리 ID)입니다.

`https://login.microsoftonline.com/<tenant-id>/`

테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 디렉터리 ID 라고도 합니다. 테 넌 트 ID를 검색 하려면 Azure Portal에서 앱 등록에 대 한 **개요** 페이지로 이동 하 고 여기에서 값을 복사 합니다.

### <a name="azure-storage-resource-id"></a>Azure Storage 리소스 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 Azure AD에서 액세스 토큰을 가져오는 방법을 보여줍니다. 지정한 사용자를 인증한 다음, 블록 Blob을 만드는 요청에 권한을 부여하는 데 액세스 토큰이 사용됩니다. 이 샘플을 사용하려면 먼저 이전 섹션에 설명된 단계를 따릅니다.

토큰을 요청 하려면 앱 등록에서 다음 값이 필요 합니다.

- Azure AD 도메인의 이름입니다. Azure Active Directory의 **개요** 페이지에서이 값을 검색 합니다.
- 테 넌 트 (또는 디렉터리) ID입니다. 앱 등록의 **개요** 페이지에서이 값을 검색 합니다.
- 클라이언트 또는 응용 프로그램 ID입니다. 앱 등록의 **개요** 페이지에서이 값을 검색 합니다.
- 클라이언트 리디렉션 URI입니다. 앱 등록에 대 한 **인증** 설정에서이 값을 검색 합니다.
- 클라이언트 암호의 값입니다. 이전에 복사한 위치에서이 값을 검색 합니다.

### <a name="create-a-storage-account-and-container"></a>스토리지 계정 및 컨테이너 만들기

코드 샘플을 실행 하려면 Azure Active Directory와 동일한 구독 내에 저장소 계정을 만듭니다. 그런 다음 해당 저장소 계정 내에 컨테이너를 만듭니다. 샘플 코드는이 컨테이너에 블록 blob을 만듭니다.

그런 다음, 샘플 코드를 실행 하는 사용자 계정에 **저장소 Blob 데이터 참가자** 역할을 명시적으로 할당 합니다. Azure Portal에서이 역할을 할당 하는 방법에 대 한 지침은 [Azure Portal에서 Azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac-portal.md)지정을 참조 하세요.

> [!NOTE]
> Azure Storage 계정을 만들면 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당 되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD를 사용 하 여 Blob 저장소에 대 한 액세스 권한을 부여 하는 웹 응용 프로그램 만들기

응용 프로그램이 Azure Storage에 액세스 하는 경우 사용자를 대신 하 여이를 수행 합니다. 즉, 로그인 한 사용자의 사용 권한을 사용 하 여 blob 또는 큐 리소스에 액세스 합니다. 이 코드 예제를 사용 하려면 사용자에 게 Azure AD id를 사용 하 여 로그인 하 라는 메시지를 표시 하는 웹 응용 프로그램이 필요 합니다. 사용자 고유의를 만들거나 Microsoft에서 제공 하는 응용 프로그램 예제를 사용할 수 있습니다.

토큰을 획득 하 고이를 사용 하 여 Azure Storage에서 blob을 만드는 완성 된 샘플 웹 응용 프로그램은 [GitHub](https://aka.ms/aadstorage)에서 사용할 수 있습니다. 완성 된 샘플을 검토 하 고 실행 하면 코드 예제를 이해 하는 데 도움이 될 수 있습니다. 완성 된 샘플을 실행 하는 방법에 대 한 지침은 [완료 된 샘플 보기 및 실행](#view-and-run-the-completed-sample)섹션을 참조 하세요.

#### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure Storage 클라이언트 라이브러리를 설치 합니다. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 콘솔 창에 다음 명령을 입력 하 여 .NET 용 Azure Storage 클라이언트 라이브러리에서 필요한 패키지를 설치 합니다.

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

그런 다음 HomeController.cs 파일에 다음 using 문을 추가 합니다.

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>블록 Blob 만들기

다음 코드 조각을 추가 하 여 블록 blob을 만듭니다.

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> OAuth 2.0 토큰을 사용 하 여 blob 및 큐 작업에 권한을 부여 하려면 HTTPS를 사용 해야 합니다.

위의 예제에서 .NET 클라이언트 라이브러리는 블록 Blob 만들기 요청의 권한 부여를 처리합니다. 다른 언어에 대 한 Azure Storage 클라이언트 라이브러리는 요청에 대 한 권한 부여도 처리 합니다. 그러나 REST API를 사용하여 OAuth 토큰을 통해 Azure Storage 작업을 호출하는 경우 OAuth 토큰을 사용하여 요청에 권한을 부여해야 합니다.

OAuth 액세스 토큰을 사용하여 Blob 및 큐 서비스 작업을 호출하려면, **Bearer**(전달자) 체계를 사용하여 **Authorization**(권한 부여) 헤더에 액세스 토큰을 전달하고 2017-11-09 이상의 서비스 버전을 지정합니다.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD에서 OAuth 토큰 가져오기

다음으로, 사용자를 대신 하 여 Azure AD에서 토큰을 요청 하는 메서드를 추가 합니다. 이 메서드는 권한이 부여 되는 범위를 정의 합니다. 사용 권한 및 범위에 대 한 자세한 내용은 [Microsoft id 플랫폼 끝점의 사용 권한 및 동의](../../active-directory/develop/v2-permissions-and-consent.md)를 참조 하세요.

리소스 ID를 사용 하 여 토큰을 가져올 범위를 구성 합니다. 이 예제에서는 리소스 ID를 기본 제공 `user_impersonation` 범위와 함께 사용 하 여 범위를 구성 합니다 .이 범위는 사용자를 대신 하 여 토큰이 요청 됨을 나타냅니다.

사용자가 토큰을 대신해 서 요청 하는 데 동의할 수 있도록 하는 인터페이스를 사용자에 게 제공 해야 할 수 있습니다. 동의가 필요한 경우이 예제에서는 **MsalUiRequiredException** 을 catch 하 고 다른 메서드를 호출 하 여 동의 요청을 용이 하 게 합니다.

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

동의란 사용자가 자신을 대신해 보호되는 리소스에 액세스하기 위해 애플리케이션에 권한을 부여하는 프로세스를 말합니다. Microsoft id 플랫폼 2.0은 증분 동의를 지원 합니다. 즉, 보안 주체가 처음에는 최소 권한 집합을 요청 하 고 필요에 따라 시간에 따라 사용 권한을 추가할 수 있습니다. 코드에서 액세스 토큰을 요청 하는 경우 `scope` 매개 변수에서 지정 된 시간에 앱에 필요한 사용 권한 범위를 지정 합니다. 증분 승인에 대 한 자세한 내용은 [Microsoft identity platform (v2.0)로 업데이트 하는 이유에](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)대 한 **증분 및 동적 동의** 섹션을 참조 하십시오.

다음 메서드는 증분 동의를 요청 하기 위한 인증 속성을 생성 합니다.

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>완성 된 샘플 보기 및 실행

샘플 응용 프로그램을 실행 하려면 먼저 [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal)에서 응용 프로그램을 복제 하거나 다운로드 합니다. 그런 다음, 다음 섹션에 설명 된 대로 응용 프로그램을 업데이트 합니다.

### <a name="provide-values-in-the-settings-file"></a>설정 파일에 값 제공

다음으로, 다음과 같이 사용자 고유의 값으로 *appsettings* 파일을 업데이트 합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>저장소 계정 및 컨테이너 이름 업데이트

*HomeController.cs* 파일에서 블록 blob을 참조 하는 URI를 업데이트 하 여 저장소 계정 및 컨테이너의 이름을 사용 합니다.

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>암시적 허용 흐름 사용

샘플을 실행 하려면 앱 등록에 대 한 암시적 권한 부여 흐름을 구성 해야 할 수 있습니다. 다음 단계를 수행하세요.

1. Azure Portal에서 앱 등록으로 이동 합니다.
1. 관리 섹션에서 **인증** 설정을 선택 합니다.
1. 다음 그림에 표시 된 것 처럼 **고급 설정**의 **암시적 권한 부여** 섹션에서 확인란을 선택 하 여 액세스 토큰 및 ID 토큰을 사용 하도록 설정 합니다.

    ![암시적 권한 부여 흐름에 대 한 설정을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Localhost에서 사용 하는 포트 업데이트

샘플을 실행할 때 런타임에 할당 된 *localhost* 포트를 사용 하도록 앱 등록에 지정 된 리디렉션 URI를 업데이트 해야 하는 경우가 있습니다. 할당 된 포트를 사용 하도록 리디렉션 URI를 업데이트 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 앱 등록으로 이동 합니다.
1. 관리 섹션에서 **인증** 설정을 선택 합니다.
1. **Uri 리디렉션**에서 다음 이미지와 같이 샘플 응용 프로그램에서 사용 하는 것과 일치 하도록 포트를 편집 합니다.

    ![앱 등록에 대 한 리디렉션 Uri를 보여 주는 스크린샷](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>다음 단계

- Microsoft id 플랫폼에 대 한 자세한 내용은 [microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/)을 참조 하세요.
- Azure storage에 대 한 RBAC 역할에 대해 자세히 알아보려면 [rbac를 사용 하 여 저장소 데이터에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.
- Azure Storage에서 Azure 리소스에 관리 id를 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 인증](storage-auth-aad-msi.md)
