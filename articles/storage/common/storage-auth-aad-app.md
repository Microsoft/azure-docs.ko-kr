---
title: 클라이언트 응용 프로그램에서 blob 및 큐 데이터에 액세스 하려면 Azure Active Directory를 사용 하 여 인증
description: Azure Active Directory를 사용 하 여 클라이언트 응용 프로그램 내에서 인증, OAuth 2.0 토큰을 획득 및 Azure Blob storage 및 Queue storage에 대 한 요청 권한을 부여 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e57291292d8957fd323f9be03bb7df0492484ea8
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341627"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Blob 및 큐에 대 한 액세스를 위해 응용 프로그램에서 Azure Active Directory를 사용 하 여 인증

Azure Active Directory (Azure AD)를 사용 하 여 Azure Blob 저장소 또는 큐 저장소를 사용 하 여의 핵심 이점은 자격 증명이 코드에 저장할 필요 없는 경우 대신 Microsoft id 플랫폼 (이전 Azure AD)에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 보안 주체 (사용자, 그룹 또는 서비스 주체)를 인증 하는 azure AD 응용 프로그램을 실행 합니다. 인증이 성공 하면 Azure AD 응용 프로그램에 대 한 액세스 토큰을 반환 하 고 응용 프로그램 Azure Blob 저장소 또는 큐 저장소에 대 한 요청 권한을 부여 하는 데 액세스 토큰을 사용할 수 있습니다.

이 문서에는 Microsoft id 플랫폼 2.0 사용 하 여 네이티브 응용 프로그램 또는 인증에 대 한 웹 응용 프로그램을 구성 하는 방법을 보여 줍니다. 코드 예제에서는 .NET을 사용하지만 다른 언어는 비슷한 접근 방식을 사용합니다. Microsoft id 플랫폼 2.0에 대 한 자세한 내용은 참조 하세요. [Microsoft id 플랫폼 (v2.0) 개요](../../active-directory/develop/v2-overview.md)합니다.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Azure AD 보안 주체에 역할 할당

Azure Storage 애플리케이션에서 보안 주체를 인증하려면 먼저 해당 보안 주체에 대해 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure Storage 컨테이너 및 큐에 대 한 권한을 포함 하는 기본 제공 RBAC 역할을 정의 합니다. RBAC 역할이 보안 주체에게 할당되면 해당 보안 주체는 해당 리소스에 대한 액세스 권한이 부여됩니다. 자세한 내용은 [RBAC 사용 하 여 Azure Blob 및 큐 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록

Azure AD를 사용 하 여 저장소 리소스에 대 한 액세스 권한을 부여 하는 첫 번째 단계에서 Azure AD 테 넌 트를 사용 하 여 클라이언트 응용 프로그램을 등록 합니다 [Azure portal](https://portal.azure.com)합니다. 클라이언트 응용 프로그램을 등록 하면 Azure AD에 응용 프로그램에 대 한 정보를 제공 합니다. 그러면 Azure AD는 런타임 시 애플리케이션을 Azure AD와 연결하는 데 사용하는 클라이언트 ID(*애플리케이션 ID*라고도 함)를 제공합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

Azure Storage 응용 프로그램을 등록 하려면에 설명 된 단계에 따라 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](../../active-directory/develop/quickstart-configure-app-access-web-apis.md)합니다. 다음 이미지는 웹 응용 프로그램을 등록 하는 것에 대 한 일반적인 설정을 보여 줍니다.

![Azure AD를 사용 하 여 storage 응용 프로그램을 등록 하는 방법을 보여 주는 스크린샷](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> 애플리케이션을 네이티브 애플리케이션으로 등록하는 경우 **리디렉션 URI**에 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램에 대 한이 값이 실제 url 없습니다. 웹 응용 프로그램에 대 한 리디렉션 URI를 토큰은 제공 된 URL을 지정 하기 때문에 유효한 URI 여야 합니다.

애플리케이션을 등록한 후에 **설정** 아래에 애플리케이션 ID(또는 클라이언트 ID)가 표시됩니다.

![클라이언트 ID를 보여 주는 스크린샷](./media/storage-auth-aad-app/app-registration-client-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Azure Storage에 등록된 앱 사용 권한 부여

다음으로, Azure Storage Api를 호출 하 여 응용 프로그램 사용 권한을 부여 합니다. 이 단계에서는 응용 프로그램을 Azure AD 사용 하 여 Azure Storage에 요청 권한을 부여 합니다.

1. 에 **개요** 선택 등록 된 응용 프로그램에 대 한 페이지 **API 사용 권한 보기**합니다.
1. 에 **API 사용 권한** 섹션에서 **권한을 추가** 선택한 **내 조직에서 사용 하는 Api**합니다.
1. 아래는 **내 조직에서 사용 하는 Api** 섹션, "Azure Storage"에 대 한 검색 및 선택 **Azure Storage** 표시 하도록 결과 목록에서를 **API 요청 권한을** 창입니다.

    ![저장소에 대 한 표시 권한을 스크린 샷](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. 아래 **사용 권한 유형을 응용 프로그램에 필요한?** , 사용 가능한 사용 권한 유형이 관찰 **위임 된 권한**. 이 옵션은를 기본으로 선택 됩니다.
1. 에 **권한을 선택** 섹션 합니다 **요청 API 사용 권한** 창 옆의 확인란을 선택 **user_impersonation**, 클릭 **추가 사용 권한**합니다.
1. 합니다 **API 사용 권한** 창 이제 Azure AD 응용 프로그램에 Microsoft Graph 및 Azure Storage에 대 한 액세스를 보여 줍니다. 먼저 Azure AD를 사용 하 여 앱을 등록할 권한이 자동으로 Microsoft Graph에 부여 됩니다.

    ![스크린샷 앱 사용 권한 등록](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램에 토큰을 요청할 때 해당 id를 증명 하기 위해 클라이언트 암호가 필요 합니다. 클라이언트 비밀을 추가 하려면 다음이 단계를 수행 합니다.

1. Azure portal에서 앱 등록으로 이동 합니다.
1. 선택 된 **인증서 및 비밀** 설정 합니다.
1. 아래 **클라이언트 비밀**, 클릭 **새 클라이언트 암호** 새 비밀을 만듭니다.
1. 암호에 대 한 설명을 제공 하 고 원하는 만료 간격을 선택 합니다.
1. 즉시 안전한 위치에 새 암호 값을 복사 합니다. 전체 값을 한 번만 표시 됩니다.

    ![스크린 샷 보여 주는 클라이언트 암호](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>토큰 획득에 대 한 클라이언트 라이브러리

응용 프로그램을 등록 하 고 Azure Blob 저장소 또는 큐 저장소에서 데이터에 액세스할 권한이 보안 주체를 인증 하 고 OAuth 2.0 토큰을 획득 하도록 응용 프로그램 코드를 추가할 수 있습니다. 인증 토큰 획득을 중 하나를 사용할 수 있습니다 합니다 [Microsoft id 플랫폼 인증 라이브러리](../../active-directory/develop/reference-v2-libraries.md) 또는 OpenID Connect 1.0을 지 원하는 다른 오픈 소스 라이브러리입니다. 다음 응용 프로그램 Azure Blob 저장소 또는 큐 저장소에 대 한 요청 권한을 부여 하 액세스 토큰을 사용할 수 있습니다.

토큰을 가져오는 동안 지원 되는 시나리오 목록을 참조 하세요. 합니다 [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 [.NET 용 Microsoft 인증 라이브러리 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 리포지토리.

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 Azure AD에서 액세스 토큰을 가져오는 방법을 보여줍니다. 지정한 사용자를 인증한 다음, 블록 Blob을 만드는 요청에 권한을 부여하는 데 액세스 토큰이 사용됩니다. 이 샘플을 사용하려면 먼저 이전 섹션에 설명된 단계를 따릅니다.

토큰을 요청 하려면 앱의 등록에서 다음 값을 사용 해야 합니다.

- Azure AD 도메인의 이름입니다. 이 값을 검색 합니다 **개요** Azure Active Directory의 페이지입니다.
- 테 넌 트 (또는 디렉터리) id입니다. 이 값을 검색 합니다 **개요** 앱 등록 페이지입니다.
- 클라이언트 (또는 응용 프로그램) id입니다. 이 값을 검색 합니다 **개요** 앱 등록 페이지입니다.
- 클라이언트 리디렉션 URI입니다. 이 값을 검색 합니다 **인증** 앱 등록에 대 한 설정입니다.
- 클라이언트 비밀의 값입니다. 이전에 복사 하는 위치에서이 값을 검색 합니다.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Azure AD를 사용하여 인증하기 위해 잘 알려진 값

Azure AD를 사용하여 보안 주체를 인증하려면 코드에서 잘 알려진 몇 가지 값을 포함해야 합니다.

#### <a name="azure-ad-authority"></a>Azure AD 권한

Microsoft 공용 클라우드의 경우 기본 Azure AD 권한은 다음과 같습니다. 여기서 *tenant-id*는 Active Directory 테넌트 ID(또는 디렉터리 ID)입니다.

`https://login.microsoftonline.com/<tenant-id>/`

테넌트 ID는 인증에 사용할 Azure AD 테넌트를 식별합니다. 또한 이라고 하 여 directory ID 테 넌 트 ID를 검색 하려면로 이동 합니다 **개요** Azure portal에서 앱 등록 페이지 및 여기에서 값을 복사 합니다.

#### <a name="storage-resource-id"></a>저장소 리소스 ID

Azure Storage 리소스 ID를 사용 하 여 Azure Storage에 대 한 요청 권한 부여에 대 한 토큰을 획득 합니다.

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>저장소 계정 및 컨테이너 만들기

코드 샘플을 실행 하려면 Azure Active Directory와 동일한 구독 내에서 저장소 계정을 만듭니다. 그런 다음 해당 저장소 계정 내에 컨테이너를 만듭니다. 샘플 코드는이 컨테이너에 블록 blob을 만듭니다.

다음으로 명시적으로 할당 합니다 **Storage Blob 데이터 기여자** 샘플 코드는 실행할 사용자 계정에는 역할입니다. Azure portal에서이 역할을 할당 하는 방법에 지침은 [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](storage-auth-aad-rbac-portal.md)합니다.

> [!NOTE]
> Azure Storage 계정을 만들 때 Azure AD 통해 데이터를 액세스할 수 있는 권한이 없습니다 자동으로 할당 됩니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 저장소 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Azure AD 사용 하 여 Blob storage에 대 한 액세스를 인증 하는 웹 응용 프로그램 만들기

응용 프로그램에서 Azure Storage에 액세스할 때 의미에 로그온 한 사용자의 권한을 사용 하 여 blob 또는 큐 리소스에 액세스 하는 사용자 대신을 등지 않습니다. 이 코드 예제를 실행 하려면 Azure AD id를 사용 하 여 로그인 하 라는 메시지는 웹 응용 프로그램을 해야 합니다. 직접 작성 하거나 Microsoft에 제공한 샘플 응용 프로그램을 사용 수 있습니다.

토큰을 획득 하 고 Azure Storage에 blob 만들기 하는 데 사용 하는 완성 된 샘플 웹 응용 프로그램은에서 사용할 수 있습니다 [GitHub](https://aka.ms/aadstorage)합니다. 검토 및 완료 된 샘플을 실행할 코드 예제를 이해 하는 데 유용할 수 있습니다. 완성 된 샘플을 실행 하는 방법에 대 한 지침 섹션을 참조 하세요 [뷰와 완성 된 샘플을 실행](#view-and-run-the-completed-sample)합니다.

#### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure Storage 클라이언트 라이브러리를 설치 합니다. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. .NET 용 Azure Storage 클라이언트 라이브러리에서 필요한 패키지를 설치 하 여 콘솔 창에 다음 명령을 입력 합니다.

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

다음으로, 다음을 추가 하려면 HomeController.cs 파일 문을 사용 하 여:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>블록 Blob 만들기

블록 blob를 만들려면 다음 코드를 추가 합니다.

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

위의 예제에서 .NET 클라이언트 라이브러리는 블록 Blob 만들기 요청의 권한 부여를 처리합니다. 또한 다른 언어에 대 한 azure Storage 클라이언트 라이브러리를 요청의 권한 부여를 처리합니다. 그러나 REST API를 사용하여 OAuth 토큰을 통해 Azure Storage 작업을 호출하는 경우 OAuth 토큰을 사용하여 요청에 권한을 부여해야 합니다.

OAuth 액세스 토큰을 사용하여 Blob 및 큐 서비스 작업을 호출하려면, **Bearer**(전달자) 체계를 사용하여 **Authorization**(권한 부여) 헤더에 액세스 토큰을 전달하고 2017-11-09 이상의 서비스 버전을 지정합니다.

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Azure AD에서 OAuth 토큰 가져오기

다음으로 Azure AD에서 토큰을 요청하는 메서드를 추가합니다. 사용자를 대신 하 여 토큰 요청 되며 GetTokenOnBehalfOfUser 메서드를 사용 합니다.

에서는 최근 로그인 및 토큰을 요청 하는 경우는 `storage.azure.com` 사용자 본인을 대신해 작업에는 사용자가 동의할 수 있는 UI를 표시 해야 리소스입니다. Catch 해야 할 수 있도록를 `MsalUiRequiredException` 다음 예와에서 같이 사용자 동의 요청 하는 기능을 추가 합니다.

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
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

동의란 사용자가 자신을 대신해 보호되는 리소스에 액세스하기 위해 애플리케이션에 권한을 부여하는 프로세스를 말합니다. Microsoft id 플랫폼 2.0 보안 주체 권한의 최소 집합을 처음에 요청 하 고 필요에 따라 시간이 지남에 따라 사용 권한을 추가 수를 의미 하는 증분 동의 지원 합니다. 코드 액세스 토큰을 요청 하면 앱에 의해 지정된 된 시간에 필요한 사용 권한 범위를 지정 합니다 `scope` 매개 변수입니다. 증분 동의 대 한 자세한 내용은 섹션을 참조 하세요 **증분 및 동적 동의** 에 [Microsoft id 플랫폼 (v2.0)를 업데이트 하는 이유?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)합니다.

다음 메서드는 증분 동의 요청에 대 한 인증 속성을 생성 합니다.

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>보기 및 완성 된 샘플을 실행 합니다.

샘플 응용 프로그램을 실행 하려면 먼저 복제 또는에서 다운로드할 [GitHub](https://aka.ms/aadstorage)합니다. 다음 섹션에 설명 된 대로 응용 프로그램을 업데이트 합니다.

### <a name="provide-values-in-the-settings-file"></a>설정 파일의 값을 제공 합니다.

다음으로 업데이트 합니다 *appsettings.json* 고유한 값을 사용 하 여 다음과 같이 파일:

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

### <a name="update-the-storage-account-and-container-name"></a>저장소 계정 및 컨테이너 이름을 업데이트 합니다.

에 *HomeController.cs* 파일, 저장소 계정 및 컨테이너 이름을 사용 하 여 블록 blob을 참조 하는 URI를 업데이트 합니다.

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>암시적 허용 흐름을 사용 하도록 설정

샘플을 실행 하려면 앱 등록에 대 한 암시적 허용 흐름을 구성 해야 합니다. 다음 단계를 수행하세요.

1. Azure portal에서 앱 등록으로 이동 합니다.
1. 관리 섹션에서 선택 합니다 **인증** 설정 합니다.
1. 아래 **고급 설정**를 **암시적 권한 부여** 섹션에서 다음 이미지에 표시 된 대로 액세스 토큰 및 ID 토큰을 사용 하도록 설정 하려면 확인란을 선택:

    ![암시적 허용 흐름에 대 한 설정을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Localhost 사용 하는 포트를 업데이트 합니다.

샘플을 실행 하는 경우 있습니다 사용 하 여 앱 등록에 지정 된 URI 리디렉션을 업데이트 해야 합니다 *localhost* 런타임에 할당 된 포트입니다. 리디렉션 URI 할당 된 포트를 사용 하도록 업데이트 하려면 다음이 단계를 수행 합니다.

1. Azure portal에서 앱 등록으로 이동 합니다.
1. 관리 섹션에서 선택 합니다 **인증** 설정 합니다.
1. 아래 **리디렉션 Uri**, 샘플 응용 프로그램에 다음 이미지와 같이 일치 하는 포트를 편집 합니다.

    ![앱 등록에 대 한 리디렉션 Uri를 보여 주는 스크린샷](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>다음 단계

- Microsoft id 플랫폼에 대 한 자세한 내용은 참조 하세요 [Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/)합니다.
- Azure storage에 대 한 RBAC 역할에 대 한 자세한 내용은 참조 하세요 [RBAC 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.
- 관리 되는 id를 사용 하 여 Azure Storage를 사용 하 여 Azure 리소스에 대 한 자세한 내용은 참조 하세요 [Azure 리소스에 대 한 blob 및 Azure Active Directory 및 관리 되는 id를 사용 하 여 큐에 대 한 액세스를 인증](storage-auth-aad-msi.md)합니다.
