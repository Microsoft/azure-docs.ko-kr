---
title: 클라이언트 인증서를 사용 하 여 RESTful 서비스 보호
titleSuffix: Azure AD B2C
description: 클라이언트 인증서를 사용하여 Azure AD B2C에서 사용자 지정 REST API 클레임 교환 보호
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6f4c24bb5587ca67fd2de0d8aad2c6f5218a41b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848513"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>클라이언트 인증서를 사용하여 RESTful 서비스 보호

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

관련된 문서에서 Azure AD B2C(Azure Active Directory B2C)와 상호 작용하는 [RESTful 서비스를 만듭니다](rest-api-claims-exchange-dotnet.md).

이 문서에서는 클라이언트 인증서를 사용하여 Azure 웹앱(RESTful API)에 대한 액세스를 제한하는 방법에 대해 알아봅니다. 이 메커니즘은 TLS 상호 인증 또는 *클라이언트 인증서 인증*이라고 합니다. Azure AD B2C와 같은 적절한 인증서를 가진 서비스만이 서비스에 액세스할 수 있습니다.

>[!NOTE]
>또한 [HTTP 기본 인증](secure-rest-api-dotnet-basic-auth.md)을 사용하여 RESTful 서비스를 보호할 수 있습니다. 그러나 클라이언트 인증서를 통한 HTTP 기본 인증의 보안 수준은 낮은 것으로 간주됩니다. 이 문서에 설명된 대로 클라이언트 인증서 인증을 사용하여 RESTful 서비스를 보호하는 것이 좋습니다.

이 문서에서는 다음과 같은 방법을 자세히 설명합니다.
* 클라이언트 인증서 인증을 사용하도록 웹앱 설정
* Azure AD B2C 정책 키에 인증서 업로드
* 클라이언트 인증서를 사용하도록 사용자 지정 정책 구성

## <a name="prerequisites"></a>필수 조건
* [REST API 클레임 교환 통합](rest-api-claims-exchange-dotnet.md) 문서의 단계 완료
* 유효한 인증서(프라이빗 키를 포함한 .pfx 파일) 가져오기.

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>1단계: 클라이언트 인증서 인증을 위한 웹앱 구성
**Azure App Service**가 클라이언트 인증서를 요구하도록 설정하려면 웹앱 `clientCertEnabled` 사이트를 *true*로 설정합니다. 이 변경 내용을 적용하려면 Azure Portal에서 웹앱 페이지를 엽니다. 왼쪽 탐색 메뉴의 **설정** 아래에서 **SSL 설정**을 선택합니다. **클라이언트 인증서** 섹션에서 **들어오는 클라이언트 인증서** 옵션을 설정합니다.

>[!NOTE]
>Azure App Service 계획이 표준 이상이어야 합니다. 자세한 내용은 [Azure App Service 계획 심층 개요](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)를 참조하세요.

>[!NOTE]
>**clientCertEnabled** 속성을 설정하는 방법에 대한 자세한 내용은 [웹앱에 TLS 상호 인증 구성](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)을 참조하세요.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2단계: Azure AD B2C 정책 키에 인증서 업로드
`clientCertEnabled`를 *true*로 설정한 후에 RESTful API와 통신하기 위해 클라이언트 인증서가 필요합니다. Azure AD B2C 테넌트에서 클라이언트 인증서를 가져오고, 업로드하고, 저장하려면 다음을 수행합니다.
1. Azure AD B2C 테넌트에서 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.

2. 테넌트에 사용 가능한 키를 보려면 **정책 키**를 선택합니다.

3. **추가**를 선택합니다.
    **키 만들기** 창이 열립니다.

4. **옵션** 상자에서 **업로드**를 선택합니다.

5. **이름** 상자에 **B2cRestClientCertificate**를 입력합니다.
    *B2C_1A_* 접두사가 자동으로 추가됩니다.

6. **파일 업로드** 상자에서 프라이빗 키가 있는 인증서 .pfx 파일을 선택합니다.

7. **암호** 상자에 인증서의 암호를 입력합니다.

    ![Azure Portal의 키 만들기 페이지에서 정책 키를 업로드 합니다.](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-upload.png)

7. **만들기**를 선택합니다.

8. 테넌트에서 사용 가능한 키를 보고 `B2C_1A_B2cRestClientCertificate` 키를 만들었음을 확인하려면 **정책 키**를 선택합니다.

## <a name="step-3-change-the-technical-profile"></a>3단계: 기술 프로필 변경
사용자 지정 정책에서 클라이언트 인증서 인증을 지원하려면 다음을 수행하여 기술 프로필을 변경합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 확장 정책 파일을 엽니다.

2. `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 검색합니다.

3. `<Metadata>` 요소를 찾습니다.

4. 다음과 같이 *AuthenticationType*을 *ClientCertificate*로 변경합니다.

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. `<Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    코드 조각을 추가한 후에 기술 프로필은 다음 XML 코드와 비슷하게 표시됩니다.

    ![ClientCertificate 인증 XML 요소를 설정합니다.](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4단계: 테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.

1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.

1. **ID 경험 프레임워크**를 선택합니다.

1. **모든 정책**을 선택합니다.

1. **정책 업로드**를 선택합니다.

1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

1. *TrustFrameworkExtensions.xml* 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5단계: 지금 실행을 사용하여 사용자 지정 정책 테스트
1. **Azure AD B2C 설정**을 연 다음 **ID 경험 프레임워크**를 선택합니다.

    >[!NOTE]
    >지금 실행을 사용하려면 하나 이상의 애플리케이션이 테넌트에 미리 등록되어 있어야 합니다. 애플리케이션을 등록하는 방법은 Azure AD B2C [시작](tutorial-create-tenant.md) 문서 또는 [애플리케이션 등록](tutorial-register-applications.md) 문서를 참조하세요.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.

3. **지정된 이름** 상자에 **테스트**를 입력하여 프로세스를 테스트합니다.
    Azure AD B2C는 창의 위쪽에 오류 메시지를 표시합니다.

    ![지정 된 이름 텍스트 상자가 강조 표시 되 고 입력 유효성 검사 오류가 표시 됨](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-test.png)

4. **지정된 이름** 상자에서 ("Test" 이외의) 이름을 입력합니다.
    Azure AD B2C는 사용자를 등록한 후 전용 번호를 애플리케이션에 보냅니다. 이 JWT 예제의 번호를 적어둡니다.

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

   >[!NOTE]
   >*이름이 유효하지 않습니다. 유효한 이름을 입력하세요.* 라는 오류 메시지가 표시되면 Azure AD B2C에서 클라이언트 인증서를 제공하는 동안 성공적으로 RESTful 서비스를 호출했음을 의미합니다. 다음 단계에서는 인증서의 유효성을 검사합니다.

## <a name="step-6-add-certificate-validation"></a>6단계: 인증서 유효성 검사 추가
Azure AD B2C에서 RESTful 서비스에 전송한 클라이언트 인증서는 인증서가 있는지를 확인하는 것을 제외하고 Azure App Service 플랫폼에 의한 유효성 검사를 거치지 않습니다. 인증서의 유효성을 검사하는 작업은 웹앱에서 담당합니다.

이 섹션에서는 인증을 위해 인증서 속성의 유효성을 검사하는 샘플 ASP.NET 코드를 추가합니다.

> [!NOTE]
>클라이언트 인증서 인증을 위해 Azure App Service를 구성하는 방법에 대한 자세한 내용은 [웹앱에 TLS 상호 인증 구성](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)을 참조하세요.

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 프로젝트의 web.config 파일에 애플리케이션 설정 추가
앞서 만든 Visual Studio 프로젝트에서 `appSettings` 요소 뒤에 *web.config* 파일에 다음 애플리케이션 설정을 추가합니다.

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

인증서의 **주체 이름**, **발급자 이름** 및 **인증서 지문** 값을 인증서 값으로 바꿉니다.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 IsValidClientCertificate 함수 추가
*Controllers\IdentityController.cs* 파일을 열고 다음 함수를 `Identity` 컨트롤러 클래스에 추가합니다.

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

위의 샘플 코드에서 다음 조건이 모두 충족되는 경우에만 인증서를 유효한 것으로 수락합니다.
* 인증서가 만료되지 않고 현재 서버에서 활성화되어 있습니다.
* 인증서의 `Subject` 이름은 `ClientCertificate:Subject` 애플리케이션 설정 값과 같습니다.
* 인증서의 `Issuer` 이름은 `ClientCertificate:Issuer` 애플리케이션 설정 값과 같습니다.
* 인증서의 `thumbprint`는 `ClientCertificate:Thumbprint` 애플리케이션 설정 값과 같습니다.

>[!IMPORTANT]
>서비스의 중요도에 따라 더 많은 유효성 검사를 추가해야 합니다. 예를 들어 인증서가 신뢰할 수 있는 루트 인증 기관, 발급자 조직 이름 유효성 검사 등에 연결되었는지 테스트해야 합니다.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 IsValidClientCertificate 함수 호출
*Controllers\IdentityController.cs* 파일을 열고 `SignUp()` 함수의 시작 부분에서 다음 코드 조각을 추가합니다.

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

코드 조각을 추가한 후에 `Identity` 컨트롤러는 다음 코드와 비슷하게 표시됩니다.

![인증서 유효성 검사 코드 추가](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>7단계: Azure에 프로젝트 게시 및 테스트
1. **솔루션 탐색기**에서 **Contoso.AADB2C.API** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시**를 선택합니다.

2. "6단계"를 반복하고 인증서 유효성 검사를 사용하여 사용자 지정 정책을 다시 테스트합니다. 정책을 실행하고 유효성 검사를 추가한 후에 제대로 작동하는지 확인합니다.

3. *web.config* 파일에서 `ClientCertificate:Subject` 값을 **잘못된** 값으로 변경합니다. 정책을 다시 실행하면 오류 메시지가 표시됩니다.

4. 다시 값을 **유효한** 값으로 변경하고 정책이 REST API를 호출할 수 있는지 확인합니다.

이 단계의 문제를 해결해야 하는 경우 [Application Insights를 사용하여 로그 수집](troubleshoot-with-application-insights.md)을 참조하세요.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(선택 사항)완성 정책 파일 및 코드 다운로드
* [사용자 지정 정책 시작](custom-policy-get-started.md) 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)이 제공됩니다.
* [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)에서 전체 코드를 다운로드할 수 있습니다.
