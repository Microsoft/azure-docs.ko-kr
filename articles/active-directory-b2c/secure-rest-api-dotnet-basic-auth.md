---
title: HTTP 기본 인증을 사용 하 여 RESTful 서비스 보호
titleSuffix: Azure AD B2C
description: HTTP 기본 인증을 사용하여 Azure AD B2C에서 사용자 지정 REST API 클레임 교환 보호
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 986ad1845fa2699bb3462dceb3865300a035cb71
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848539"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>HTTP 기본 인증을 사용하여 RESTful 서비스 보호

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[관련된 Azure AD B2C 문서](rest-api-claims-exchange-dotnet.md)에서 인증하지 않고 Azure AD B2C(Azure Active Directory B2C) 사용자 경험에 통합되는 RESTful 서비스(Web API)를 만듭니다.

이 문서에서는 B2C를 포함하여 확인된 사용자만이 API에 액세스할 수 있도록 RESTful 서비스에 HTTP 기본 인증을 추가합니다. HTTP 기본 인증을 사용하여 사용자 지정 정책에서 사용자 자격 증명(앱 ID 및 앱 암호)를 설정합니다.

자세한 내용은 [ASP.NET Web API에서 기본 인증](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

[Azure AD B2C 사용자 경험에서 REST API 클레임 교환 통합](rest-api-claims-exchange-dotnet.md) 문서에서 단계를 완료합니다.

## <a name="step-1-add-authentication-support"></a>1단계: 인증 지원 추가

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>1\.1단계: 프로젝트의 web.config 파일에 애플리케이션 설정 추가

1. 앞에서 만든 Visual Studio 프로젝트를 엽니다.

2. 다음 애플리케이션 설정을 `appSettings` 요소의 web.config 파일에 추가합니다.

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. 암호를 만들고 `WebApp:ClientSecret` 값을 설정합니다.

    복잡한 암호를 생성하려면 다음 PowerShell 코드를 실행합니다. 임의의 값을 사용할 수 있습니다.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>1\.2단계: OWIN 라이브러리 설치

시작하려면 Visual Studio 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 프로젝트에 추가합니다.

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>1\.3단계: 인증 미들웨어 클래스 추가

*App_Start* 폴더 아래에 `ClientAuthMiddleware.cs` 클래스를 추가합니다. 이렇게 하려면 다음을 수행합니다.

1. *App_Start* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 후 **클래스**를 선택합니다.

   ![App_Start 폴더에서 ClientAuthMiddleware.cs 클래스를 추가합니다.](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. **이름** 상자에서 **ClientAuthMiddleware.cs**를 입력합니다.

   ![Visual Studio의 C# 새 항목 추가 대화 상자에서 새 클래스 만들기](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. *App_Start\ClientAuthMiddleware.cs* 파일을 열고 파일 콘텐츠를 다음 코드로 바꿉니다.

    ```csharp

    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;

    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];

            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }

            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }

            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);

                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }

                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;

                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }

                return Next(environment);
            }

            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;

                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }

                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }

                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);

                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>1\.4단계: OWIN 시작 클래스 추가

`Startup.cs`라는 OWIN 시작 클래스를 API에 추가합니다. 이렇게 하려면 다음을 수행합니다.
1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **새 항목** **추가** > 를 선택한 다음 **OWIN**을 검색합니다.

   ![Visual Studio의 새 항목 추가 대화 상자에서 OWIN 시작 클래스 만들기](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. *Startup.cs* 파일을 열고 파일 콘텐츠를 다음 코드로 바꿉니다.

    ```csharp
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>1\.5단계: ID API 클래스 보호

Controllers\IdentityController.cs를 열고 `[Authorize]` 태그를 컨트롤러 클래스에 추가합니다. 이 태그는 권한 부여 요구 사항을 충족하는 사용자에게 컨트롤러에 대한 액세스를 제한합니다.

![컨트롤러에 권한 부여 태그 추가](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>2단계: Azure에 게시

프로젝트를 게시하려면 솔루션 탐색기에서 **Contoso.AADB2C.API** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>3단계: Azure AD B2C에 RESTful 서비스 앱 ID 및 앱 암호 추가

클라이언트 ID(사용자 이름) 및 암호로 RESTful 서비스를 보호한 후에 Azure AD B2C 테넌트에서 자격 증명을 저장해야 합니다. 사용자 지정 정책은 RESTful 서비스를 호출할 때 자격 증명을 제공합니다.

### <a name="step-31-add-a-restful-services-client-id"></a>3\.1단계: RESTful 서비스 클라이언트 ID 추가

1. Azure AD B2C 테넌트에서 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.


2. 테넌트에 사용 가능한 키를 보려면 **정책 키**를 선택합니다.

3. **추가**를 선택합니다.

4. **옵션**에서 **수동**을 선택합니다.

5. **이름**에 **B2cRestClientId**를 입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.

6. **암호** 상자에 앞에서 정의한 앱 ID를 입력합니다.

7. **키 사용**으로는 **서명**을 선택합니다.

8. **만들기**를 선택합니다.

9. `B2C_1A_B2cRestClientId` 키를 만들었는지 확인합니다.

### <a name="step-32-add-a-restful-services-client-secret"></a>3\.2단계: RESTful 서비스 클라이언트 암호 추가

1. Azure AD B2C 테넌트에서 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.

2. **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.

3. **추가**를 선택합니다.

4. **옵션**에서 **수동**을 선택합니다.

5. **이름**에 **B2cRestClientSecret**을 입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.

6. **암호** 상자에 앞에서 정의한 앱 암호를 입력합니다.

7. **키 사용**으로는 **서명**을 선택합니다.

8. **만들기**를 선택합니다.

9. `B2C_1A_B2cRestClientSecret` 키를 만들었는지 확인합니다.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>4단계: 확장 정책에서 기본 인증을 지원하도록 기술 프로필 변경

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.

2. `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 검색합니다.

3. `<Metadata>` 요소를 찾습니다.

4. 다음과 같이 *AuthenticationType*을 *기본*으로 변경합니다.

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. `<Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    코드 조각을 추가한 후에 기술 프로필은 다음 XML 코드와 비슷하게 표시됩니다.

    ![TechnicalProfile에 기본 인증 XML 요소 추가](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5단계: 테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.

1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.

1. **ID 경험 프레임워크**를 선택합니다.

1. **모든 정책**을 엽니다.

1. **정책 업로드**를 선택합니다.

1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

1. *TrustFrameworkExtensions.xml* 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6단계: 지금 실행을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 연 다음 **ID 경험 프레임워크**를 선택합니다.

    >[!NOTE]
    >지금 실행을 사용하려면 하나 이상의 애플리케이션이 테넌트에 미리 등록되어 있어야 합니다. 애플리케이션을 등록하는 방법은 Azure AD B2C [시작](tutorial-create-tenant.md) 문서 또는 [애플리케이션 등록](tutorial-register-applications.md) 문서를 참조하세요.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.

3. **지정된 이름** 상자에 **테스트**를 입력하여 프로세스를 테스트합니다.
    Azure AD B2C는 창의 위쪽에 오류 메시지를 표시합니다.

    ![Id API에서 지정 된 이름 입력 유효성 검사 테스트](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. **지정된 이름** 상자에서 ("Test" 이외의) 이름을 입력합니다.
    Azure AD B2C는 사용자를 등록한 후 전용 번호를 애플리케이션에 보냅니다. 이 예제에서 번호를 적어둡니다.

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(선택 사항)완성 정책 파일 및 코드 다운로드

* [사용자 지정 정책 시작](custom-policy-get-started.md) 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)이 제공됩니다.
* [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)에서 전체 코드를 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [클라이언트 인증서를 사용하여 RESTful API 보호](secure-rest-api-dotnet-certificate-auth.md)
