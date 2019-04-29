---
title: Azure Active Directory 인증 및 Resource Manager | Microsoft Docs
description: 앱을 다른 Azure 구독과 통합하기 위한 Azure Resource Manager API 및 Azure Active Directory를 사용한 권한 부여 개발자 가이드.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: ae405d5dd99a0e2acced924ccccab292b4489cde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61063295"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Resource Manager 인증 API를 사용하여 구독에 액세스

고객의 Azure 리소스를 관리 하는 앱을 만드는 데는 소프트웨어 개발자 인 경우이 문서에서는 Azure Resource Manager Api를 사용 하 여 인증 하 고 다른 구독의 리소스에 액세스 하는 방법을 보여줍니다.

앱에서 두 가지 방법으로 Resource Manager API에 액세스할 수 있습니다.

1. **사용자 + 앱 액세스**:에서 로그인 한 사용자에 대 한 리소스에 액세스 하는 앱에 대 한 합니다. 이 방법은 웹앱 및 명령줄 도구 등 Azure 리소스의 "대화형 관리"만 처리하는 앱에만 적용됩니다.
2. **앱 전용 액세스**: 디먼 서비스 및 예약된 작업을 실행하는 앱. 리소스에 대한 직접 액세스 권한을 앱의 ID에 부여합니다. 이 방법은 Azure에 대한 장기적인 헤드리스 액세스(자동)가 필요한 앱에 작동합니다.

이 문서에서는 이러한 권한 부여 방법을 모두 채택하는 앱을 만드는 단계별 지침을 제공합니다. REST API를 사용 하 여 각 단계를 수행 하는 방법을 보여 줍니다 또는 C#입니다. 전체 ASP.NET MVC 애플리케이션은 [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense)에서 사용할 수 있습니다.

## <a name="what-the-web-app-does"></a>웹앱이 수행하는 작업

웹앱:

1. Azure 사용자를 로그인합니다.
2. 사용자에게 Resource Manager에 대한 웹앱 액세스 권한을 부여하도록 요청합니다.
3. Resource Manager에 액세스하기 위한 사용자 + 앱 액세스 토큰을 가져옵니다.
4. 3단계의 토큰을 사용하여 구독의 역할에 앱의 서비스 주체를 할당합니다. 이 단계에서 구독에 앱 장기 액세스 권한을 제공합니다.
5. 앱 전용 액세스 토큰을 가져옵니다.
6. 토큰(5단계)을 사용하여 Resource Manager를 통해 구독에 있는 리소스를 관리합니다.

웹 애플리케이션의 흐름은 다음과 같습니다.

![Resource Manager 인증 흐름](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

사용자는 사용하려는 구독에 대한 구독 ID를 제공합니다.

![구독 ID 제공](./media/resource-manager-api-authentication/sample-ux-1.png)

로그인에 사용할 계정을 선택합니다.

![계정 선택](./media/resource-manager-api-authentication/sample-ux-2.png)

자격 증명을 제공 합니다.

![자격 증명 제공](./media/resource-manager-api-authentication/sample-ux-3.png)

Azure 구독에 대한 앱 액세스 권한을 부여합니다.

![액세스 권한 부여](./media/resource-manager-api-authentication/sample-ux-4.png)

연결된 구독을 관리합니다.

![구독 연결](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>애플리케이션 등록
코딩을 시작하기 전에 Azure Active Directory(AD)를 사용하여 웹앱을 등록합니다. 앱 등록은 Azure AD의 사용자 앱에 대한 중앙 ID를 만듭니다. 이는 애플리케이션이 Azure Resource Manager API를 인증하고 액세스하는 데 사용하는 OAuth 클라이언트 ID, 회신 URL 및 자격 증명 등 애플리케이션에 관한 기본 정보를 저장합니다. 앱 등록에는 또한 응용 프로그램에 필요한 사용자에 대 한 Microsoft Api에 액세스할 때 다양 한 위임 된 권한을 기록 합니다.

앱을 등록 하려면 참조 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록](../active-directory/develop/quickstart-register-app.md)합니다. 선택한 앱에 이름을 **모든 조직 디렉터리의 계정** 지원 되는 계정 유형에 대 한 합니다. 리디렉션 URL에 대 한 Azure Active Directory와 연결 된 도메인을 제공 합니다.

AD 응용 프로그램으로 로그인 한 응용 프로그램 ID 및 암호 필요 합니다. 응용 프로그램 ID는 응용 프로그램에 대 한 개요에 표시 됩니다. 비밀 만들기 API 사용 권한 요청을 참조 하세요. [빠른 시작: 웹 Api에 액세스 하는 클라이언트 응용 프로그램 구성](../active-directory/develop/quickstart-configure-app-access-web-apis.md)합니다. 새 클라이언트 암호를 제공 합니다. API 사용 권한 선택 **Azure Service Management**합니다. 선택 **위임 된 권한** 하 고 **user_impersonation**합니다.

### <a name="optional-configuration---certificate-credential"></a>선택적 구성 - 인증서 자격 증명
또한 Azure AD는 애플리케이션에 대한 인증서 자격 증명을 지원합니다. 즉, 자체 서명된 인증서를 만들고, 개인 키를 유지하고, Azure AD 애플리케이션 등록에 공개 키를 추가합니다. 인증을 위해 애플리케이션이 개인 키를 사용하여 서명한 Azure AD에 작은 페이로드를 보내면 Azure AD가 등록된 공개 키를 사용하여 서명의 유효성을 검사합니다.

인증서를 사용하여 AD 앱을 만드는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) 또는 [Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기](resource-group-authenticate-service-principal-cli.md)를 참조하세요.

## <a name="get-tenant-id-from-subscription-id"></a>구독 ID에서 테넌트 ID 가져오기
Resource Manager를 호출하는 데 사용할 수 있는 토큰을 요청하기 위해서는 애플리케이션이 Azure 구독을 호스트하는 Azure AD 테넌트의 테넌트 ID를 알고 있어야 합니다. 대부분의 경우 사용자는 자신의 구독 ID를 알고 있지만 Azure Active Directory에 대한 테넌트 ID는 모를 수 있습니다. 사용자의 테넌트 ID를 얻으려면 사용자에게 구독 ID를 요청합니다. 구독에 대한 요청을 보낼 때 이 구독 ID를 제공합니다.

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

요청에는 사용자가 아직 로그인 하지 않은 하지만 응답에서 테 넌 트 ID를 검색할 수 없으므로 실패 합니다. 이 예외에서 **WWW-Authenticate**에 대한 응답 헤더 값에서 테넌트 ID를 검색합니다. 이 구현은 [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) 메서드에 표시됩니다.

## <a name="get-user--app-access-token"></a>사용자 + 앱 액세스 토큰 가져오기
애플리케이션이 OAuth 2.0 권한 부여 요청을 사용하여 사용자를 Azure AD로 리디렉션하여 사용자의 자격 증명을 인증하고 권한 부여 코드를 돌려 줍니다. 애플리케이션은 권한 부여 코드를 사용하여 Resource Manager에 대한 액세스 토큰을 가져옵니다. [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) 메서드는 권한 부여 요청을 생성합니다.

이 문서는 사용자를 인증하는 REST API 요청을 보여 줍니다. 또한 코드에서 인증 도우미 라이브러리를 사용할 수 있습니다. 이러한 라이브러리에 대한 자세한 내용은 [Azure Active Directory 인증 라이브러리](../active-directory/active-directory-authentication-libraries.md)를 참조하세요. 애플리케이션에서 ID 관리를 통합하는 지침은 [Azure Active Directory 개발자 가이드](../active-directory/develop/v1-overview.md)를 참조하세요.

### <a name="auth-request-oauth-20"></a>인증 요청(OAuth 2.0)
Azure AD 권한 부여 엔드포인트에 대한 Open ID Connect/OAuth2.0 권한 부여 요청을 실행합니다.

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [인증 코드 요청](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) 문서에서 설명합니다.

다음 예제에서는 OAuth2.0 권한 부여를 요청하는 방법을 보여 줍니다.

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD는 사용자를 인증하고 필요한 경우 사용자에게 앱 사용 권한 부여를 요청합니다. 그러면 애플리케이션의 회신 URL에 인증 코드가 반환됩니다. 요청한 response_mode에 따라 Azure AD는 쿼리 문자열에 데이터를 다시 보내거나 데이터를 게시합니다.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>인증 요청(Open ID Connect)
뿐만 아니라 사용자에 대 한 Azure Resource Manager에 액세스 하려면 사용자가 해당 Azure AD 계정을 사용 하 여 응용 프로그램에 로그인을 허용할 경우에 Open ID Connect 권한 부여 요청을 실행 합니다. 또한 Open ID Connect를 사용하면 애플리케이션이 Azure AD로부터 앱에서 사용자를 로그인하는 데 사용할 수 있는 id_token을 받습니다.

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [로그인 요청 보내기](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) 문서에서 설명합니다.

Open ID Connect 요청 예제:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD는 사용자를 인증하고 필요한 경우 사용자에게 앱 사용 권한 부여를 요청합니다. 그러면 애플리케이션의 회신 URL에 인증 코드가 반환됩니다. 요청한 response_mode에 따라 Azure AD는 쿼리 문자열에 데이터를 다시 보내거나 데이터를 게시합니다.

Open ID Connect 응답 예제:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>토큰 요청(OAuth2.0 코드 부여 흐름)
이제 응용 프로그램에 Azure AD에서 권한 부여 코드를 받은, 액세스 토큰을 가져올 Azure Resource Manager에 대 한 시간입니다.  OAuth2.0 코드 부여 토큰 요청을 Azure AD 토큰 엔드포인트에 게시:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [인증 코드 사용](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) 문서에서 설명합니다.

다음 예제에서는 암호 자격 증명을 사용하여 코드 부여 토큰에 대한 요청을 보여 줍니다.

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

인증서 자격 증명을 사용하여 작업할 때 JSON 웹 토큰(JWT)을 만들고 애플리케이션의 인증서 자격 증명의 개인 키를 사용하여 서명합니다(RSA SHA256). 이 토큰을 빌드하는 과정은 [클라이언트 자격 증명 흐름](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate)에 표시됩니다.  참고로 클라이언트 어설션 JWT 토큰에 서명하는 방법은 [Active Directory 인증 라이브러리(.NET) 코드](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) 를 참조하세요.

클라이언트 인증에 대한 자세한 내용은 [Open ID Connect 사양](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) 을 참조하세요.

다음 예제에서는 인증서 자격 증명을 사용하여 코드 부여 토큰에 대한 요청을 보여 줍니다.

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

코드 부여 토큰에 대한 응답 예제:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>코드 부여 토큰 응답 처리
성공적인 토큰 응답은 Azure Resource Manager에 대한(사용자 + 앱) 액세스 토큰을 포함합니다. 응용 프로그램 사용자에 대 한 Resource Manager에 액세스 하려면이 액세스 토큰을 사용 합니다. Azure AD에서 발급하는 액세스 토큰의 수명은 1시간입니다. 웹 응용 프로그램 (사용자 + 앱)를 갱신 해야 한다고 그럴 가능성은 액세스 토큰입니다. 액세스 토큰을 갱신해야 한다면 애플리케이션이 토큰 응답에서 받는 새로 고침 토큰을 사용합니다. OAuth2.0 토큰 요청을 Azure AD 토큰 엔드포인트에 게시:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

새로 고침 요청에 사용할 매개 변수는 [액세스 토큰 새로 고침](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens)에서 설명합니다.

다음 예제에서는 새로 고침 토큰을 사용하는 방법을 보여 줍니다.

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Azure Resource Manager에 대 한 새 액세스 토큰을 가져오기 위해 새로 고침 토큰을 사용할 수 있지만 응용 프로그램에서 오프 라인 액세스에 대 한 적합 한 되지 않습니다. 새로 고침 토큰 수명은 제한되어 있고 새로 고침 토큰은 사용자에게 바인딩되기 때문입니다. 사용자가 조직을 떠나면 새로 고침 토큰을 사용하는 애플리케이션은 액세스 권한을 잃게 됩니다. 이 방법은 Azure 리소스를 관리하는 팀이 사용하는 애플리케이션에 적합하지 않습니다.

## <a name="check-if-user-can-assign-access-to-subscription"></a>사용자가 구독에 액세스를 할당할 수 있는지 확인
이제 응용 프로그램이 사용자에 대 한 Azure Resource Manager에 액세스 하는 토큰입니다. 다음 단계에서는 앱을 구독에 연결합니다. 연결되면 앱은 사용자가 없어도(장기 오프라인 액세스) 해당 구독을 관리할 수 있습니다.

연결할 각 구독에 대해 [Resource Manager 목록 권한](https://docs.microsoft.com/rest/api/authorization/permissions) API를 호출하여 사용자가 구독에 대한 액세스 관리 권한을 가지고 있는지 여부를 결정합니다.

ASP.NET MVC 샘플 앱의 [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) 메서드가 이 호출을 구현합니다.

다음 예제에서는 구독에 대한 사용자의 권한을 요청하는 방법을 보여 줍니다. 83cfe939-2402-4581-b761-4f59b0a041e4는 구독의 ID입니다.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

구독에 대한 사용자의 권한을 가져오기 위한 응답 예는 다음과 같습니다.

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

사용 권한 API가 여러 사용 권한을 반환합니다. 각 사용 권한은 허용되는 작업(**actions**) 및 허용되지 않는 작업(**notactions**)으로 구성됩니다. 작업 사용 권한의 허용 되는 작업에 있고 해당 권한의 허용 되지 않는 작업에 없는 경우 사용자가 해당 작업을 수행할 수 있습니다. **microsoft.authorization/roleassignments/write**는 액세스 관리 권한을 부여하는 작업입니다. 애플리케이션은 사용 권한 결과를 구문 분석하여 각 권한의 **actions** 및 **notactions**에서 이 작업 문자열에 대한 정규식 일치(regex match)를 찾습니다.

## <a name="get-app-only-access-token"></a>앱 전용 액세스 토큰 가져오기
이제 사용자가 Azure 구독에 액세스 권한을 할당할 수 있는지 알게 되었습니다. 다음 단계는 다음과 같습니다.

1. 구독에 대한 애플리케이션의 ID에 대해 적절한 RBAC 역할을 할당합니다.
2. 구독에 대한 애플리케이션의 사용 권한을 쿼리하거나 애플리케이션 전용 토큰을 사용해 Resource Manager에 액세스하여 액세스 할당의 유효성을 검사합니다.
3. 애플리케이션의 "연결된 구독" 데이터 구조에 연결을 기록하여 구독의 ID를 유지합니다.

첫 번째 단계를 자세히 살펴보겠습니다. 애플리케이션의 ID에 적절한 RBAC 역할을 할당하려면 다음을 결정해야 합니다.

* 사용자의 Azure Active Directory에서 애플리케이션 ID의 개체 ID
* 애플리케이션이 구독에 대해 요구하는 RBAC 역할의 ID

애플리케이션이 Azure AD에서 사용자를 인증할 때 애플리케이션에 대한 서비스 주체 개체가 해당 Azure AD에 생성됩니다. Azure는 RBAC 역할을 서비스 주체에 할당하여 Azure 리소스에 대한 직접 액세스 권한을 해당 애플리케이션에 부여할 수 있습니다. 이 작업이 바로 원했던 결과입니다. Azure AD Graph API를 쿼리하여 로그인한 사용자의 Azure AD에서 애플리케이션의 서비스 주체의 ID를 결정합니다.

사용자는 Azure Resource Manager에 대한 액세스 토큰만 가지고 있으므로 Azure AD Graph API를 호출하려면 새 액세스 토큰이 필요합니다. Azure AD의 모든 애플리케이션은 서비스 주체 개체를 직접 쿼리하는 권한을 가지고 있으므로 앱 전용 액세스 토큰이면 충분합니다.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Azure AD Graph API에 대한 응용 프로그램 전용 액세스 토큰 가져오기

앱을 인증 하 고 Azure AD Graph API에 토큰을 가져오기, 실행 클라이언트 자격 증명 부여 OAuth2.0 흐름 토큰 요청을 Azure AD 토큰 끝점 (**https:\//login.microsoftonline.com/{directory_domain_name}/OAuth2/Token** ).

ASP.net MVC 샘플 애플리케이션의 [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) 메서드는 Active Directory Authentication Library for .NET을 사용하여 Graph API에 대한 앱 전용 액세스 토큰을 가져옵니다.

이 요청에 사용할 수 있는 쿼리 문자열 매개 변수는 [액세스 토큰 요청](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) 문서에서 설명합니다.

클라이언트 자격 증명 부여 토큰에 대한 요청 예제:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

클라이언트 자격 증명 부여 토큰에 대한 응답 예제:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>사용자 Azure AD에서 애플리케이션 서비스 주체의 ObjectId 가져오기
이제 애플리케이션 전용 액세스 토큰을 사용하여 [Azure AD Graph 서비스 주체](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API를 쿼리하여 디렉터리에 있는 애플리케이션의 서비스 주체의 개체 ID를 결정합니다.

ASP.net MVC 샘플 애플리케이션의 [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) 메서드가 이 호출을 구현합니다.

다음 예제에서는 애플리케이션의 서비스 주체를 요청하는 방법을 보여 줍니다. a0448380-c346-4f9f-b897-c18733de9394는 애플리케이션의 클라이언트 ID입니다.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

다음 예제에서는 애플리케이션의 서비스 주체 요청에 대한 응답을 보여 줍니다.

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Azure RBAC 역할 ID 가져오기
서비스 주체에 적절한 RBAC 역할을 할당하려면 Azure RBAC 역할의 ID를 결정해야 합니다.

애플리케이션에 대한 올바른 RBAC 역할:

* 애플리케이션이 구독을 변경하지 않고 모니터링만 한다면 구독에 대한 독자 권한만 필요합니다. **독자** 역할을 할당합니다.
* 애플리케이션이 Azure 구독을 관리하는 경우(엔터티 만들기/수정/삭제) 참가자 권한 중 하나가 필요합니다.
  * 특정 유형의 리소스를 관리하려면 리소스별 참가자 역할(Virtual Machine 참가자, Virtual Network 참가자, Storage 계정 참가자 등)을 할당합니다.
  * 모든 리소스 유형을 관리하려면 **참가자** 역할을 할당합니다.

애플리케이션에 대한 역할 할당은 사용자가 볼 수 있으므로 필요한 최소 권한을 선택합니다.

호출을 [Resource Manager 역할 정의 API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) 모든 Azure RBAC 역할 목록 및 다음 이름으로 역할 정의 찾으려면 결과 반복 합니다.

ASP.net MVC 샘플 앱의 [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) 메서드가 이 호출을 구현합니다.

다음 요청 예제는 Azure RBAC 역할 ID를 가져오는 방법을 보여 줍니다. 09cbd307-aa71-4aca-b346-5f253e6e3ebb는 구독의 ID입니다.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

응답은 다음 형식으로 되어 있습니다.

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

이 API는 지속적으로 호출할 필요가 없습니다. 역할 정의의 잘 알려진 GUID를 결정한 후 다음과 같이 역할 정의 ID를 생성할 수 있습니다.

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

다음은 널리 사용되는 기본 제공 역할의 식별자입니다.

| 역할 | GUID |
| --- | --- |
| 판독기 |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| 참가자 |b24988ac-6180-42a0-ab88-20f7382dd24c |
| 가상 머신 참가자 |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Virtual Network 참여자 |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Storage 계정 참여자 |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| 웹 사이트 참가자 |de139f84-1756-47ae-9be6-808fbbe84772 |
| 웹 계획 참가자 |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| SQL Server 참여자 |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| SQL DB 참가자 |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>애플리케이션에 RBAC 역할 할당
응용 프로그램의 [Resource Manager 역할 할당 만들기](https://docs.microsoft.com/rest/api/authorization/roleassignments) API를 사용하여 서비스 주체에 적절한 RBAC 역할을 할당하기 위해 필요한 모든 것을 가졌습니다.

ASP.net MVC 샘플 앱의 [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) 메서드가 이 호출을 구현합니다.

애플리케이션에 RBAC 역할을 할당하는 요청 예제:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

요청에 다음과 같은 값을 사용합니다.

| Guid | 설명 |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |구독의 ID |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |애플리케이션의 서비스 주체의 개체 ID |
| b24988ac-6180-42a0-ab88-20f7382dd24c |참가자 역할의 ID |
| 4f87261d-2816-465d-8311-70a27558df4c |새 역할 할당에 대해 만든 새 GUID |

응답은 다음 형식으로 되어 있습니다.

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Azure Resource Manager에 대한 응용 프로그램 전용 액세스 토큰 가져오기
해당 앱의 유효성을 검사 하려면 구독에 액세스할을 테스트 앱 전용 토큰을 사용 하 여 구독에서 작업 합니다.

리소스 매개 변수에 대해 서로 다른 값을 사용하여 앱 전용 액세스 토큰을 가져오려면 [Azure AD Graph API에 대한 응용 프로그램 전용 액세스 토큰 가져오기](#app-azure-ad-graph)섹션의 지침을 따릅니다.

    https://management.core.windows.net/

ASP.NET MVC 샘플 애플리케이션의 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) 메서드는 Active Directory Authentication Library for .net을 사용하여 Azure Resource Manager에 대한 애플리케이션 전용 액세스 토큰을 가져옵니다.

#### <a name="get-applications-permissions-on-subscription"></a>구독에 대한 애플리케이션의 권한 가져오기
응용 프로그램이 Azure 구독에 액세스할 수 있습니다, 호출할 수도 있습니다 있는지 확인 하는 [Resource Manager 사용 권한](https://docs.microsoft.com/rest/api/authorization/permissions) API. 이 방법은 사용자가 구독에 대해 액세스 관리 권한을 가지고 있는지 여부를 결정하는 방법과 유사합니다. 그러나 이번에는 이전 단계에서 수신한 앱 전용 액세스 토큰을 사용하여 사용 권한 API를 호출합니다.

ASP.NET MVC 샘플 앱의 [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) 메서드가 이 호출을 구현합니다.

## <a name="manage-connected-subscriptions"></a>연결된 구독 관리
구독에 대해 애플리케이션의 서비스 주체에 적절한 RBAC 역할이 할당된 경우 애플리케이션은 Azure Resource Manager에 대한 애플리케이션 전용 액세스 토큰을 사용하여 해당 역할에 대한 모니터링/관리를 유지할 수 있습니다.

구독 소유자가 포털 또는 명령줄 도구를 사용하여 애플리케이션의 역할 할당을 제거하는 경우 애플리케이션은 더 이상 해당 구독에 액세스할 수 없습니다. 이 경우 사용자에게 구독과의 연결이 애플리케이션 외부에서 끊어졌음을 알리고 연결을 "복구"하는 옵션을 사용자에게 제공해야 합니다. 여기서 "복구"는 오프라인 상태에서 삭제된 역할 할당을 다시 만드는 것입니다.

사용자가 애플리케이션에 대한 자신의 구독을 연결할 수 있도록 하는 것과 마찬가지로 사용자가 구독의 연결을 끊는 것도 허용해야 합니다. 액세스 관리 관점에서 연결 끊기란 애플리케이션의 서비스 주체가 구독에 대해 가지고 있는 역할 할당을 제거하는 것을 의미합니다. 필요에 따라 구독에 대한 애플리케이션의 상태도 제거할 수 있습니다.
구독에 대 한 액세스 관리 권한 가진 사용자만 구독에 연결을 끊을 수 있습니다.

ASP.net MVC 샘플 앱의 [RevokeRoleFromServicePrincipalOnSubscription 메서드](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) 가 이 호출을 구현합니다.

이와 같이 사용자는 이제 애플리케이션을 사용하여 쉽게 Azure 구독을 연결 및 관리할 수 있습니다.
