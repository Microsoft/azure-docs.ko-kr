---
title: Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API 보호 | Microsoft Docs
description: Azure Active Directory 및 API Management로 웹 API 백 엔드를 보호하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: cfe2620801f743831f77fb76f344c156676966d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657563"
---
# <a name="protect-an-api-by-using-oauth-20-with-azure-active-directory-and-api-management"></a>Azure Active Directory 및 API Management에서 OAuth 2.0을 사용하여 API 보호

이 가이드에서는 Azure AD(Azure Active Directory)에서 OAuth 2.0 프로토콜을 사용하여 API를 보호하도록 Azure API Management 인스턴스를 구성하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
이 문서의 단계를 따르려면 다음이 있어야 합니다.
* API Management 인스턴스
* API Management 인스턴스를 사용하는 게시되고 있는 API
* Azure AD 테넌트

## <a name="overview"></a>개요

다음은 단계에 대한 간략한 개요입니다.

1. API를 나타내기 위해 Azure AD에 애플리케이션(backend-app) 등록합니다.
2. API를 호출해야 하는 클라이언트 애플리케이션을 나타내기 위해 Azure AD에 다른 애플리케이션(client-app)을 등록합니다.
3. Azure AD에서 client-app에 backend-app을 호출할 수 있도록 권한을 부여합니다.
4. OAuth 2.0 사용자 권한 부여를 사용하도록 개발자 콘솔 구성합니다.
5. **validate-jwt** 정책을 추가하여 들어오는 요청마다 OAuth 토큰의 유효성을 검사합니다.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API를 나타내기 위해 Azure AD에 애플리케이션 등록

Azure AD에서 API를 보호하려는 경우 첫 번째 단계는 API를 나타내는 애플리케이션을 Azure AD에서 등록하는 것입니다. 

1. Azure AD 테넌트로 이동한 다음, **앱 등록**으로 이동합니다.

2. **새 애플리케이션 등록**을 선택합니다. 

3. 애플리케이션 이름을 지정합니다. (이 예제에서는 이름이 `backend-app`입니다.)  

4. **애플리케이션 유형**으로 **웹앱/API**를 선택합니다. 

5. **로그온 URL**에 대해 `https://localhost`를 자리 표시자로 사용할 수 있습니다.

6. **만들기**를 선택합니다.

애플리케이션이 만들어지면 이후 단계에서 사용할 수 있도록 **애플리케이션 ID**를 적어둡니다. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>클라이언트 애플리케이션을 나타내기 위해 Azure AD에 다른 애플리케이션 등록

API를 호출하는 모든 클라이언트 애플리케이션을 Azure AD에서도 애플리케이션으로 등록해야 합니다. 이 예제에서 샘플 클라이언트 애플리케이션은 API Management 개발자 포털의 개발자 콘솔입니다. 다음은 개발자 콘솔을 나타내기 위해 Azure AD에 다른 애플리케이션을 등록하는 방법입니다.

1. **새 애플리케이션 등록**을 선택합니다. 

2. 애플리케이션 이름을 지정합니다. (이 예제에서는 이름이 `client-app`입니다.)

3. **애플리케이션 유형**으로 **웹앱/API**를 선택합니다.  

4. **로그온 URL**에 대해 `https://localhost`를 자리 표시자로 사용하거나 API Management 인스턴스의 로그인 URL을 사용할 수 있습니다. (이 예제에서는 URL이 `https://contoso5.portal.azure-api.net/signin`입니다.)

5. **만들기**를 선택합니다.

애플리케이션이 만들어지면 이후 단계에서 사용할 수 있도록 **애플리케이션 ID**를 적어둡니다. 

이제 후속 단계에서 사용하기 위해 이 애플리케이션에 대한 클라이언트 암호를 만듭니다.

1. **설정**을 다시 선택하고 **키**로 이동합니다.

2. **암호** 아래 **키 설명**을 제공합니다. 키가 만료되는 시점을 선택하고 **저장**을 선택합니다.

키 값을 적어둡니다. 

## <a name="grant-permissions-in-azure-ad"></a>Azure AD에서 권한 부여

API 및 개발자 콘솔을 나타내는 두 개의 애플리케이션을 등록했으니 client-app가 backend-app를 호출할 수 있도록 권한을 부여해야 합니다.  

1. **애플리케이션 등록**으로 이동합니다. 

2. `client-app`을 선택하고 **설정**으로 이동합니다.

3. **필수 권한** > **추가**를 선택합니다.

4. **API 선택**을 선택하고 `backend-app`을 검색합니다.

5. **위임된 권한**에서 `Access backend-app`을 선택합니다. 

6. **선택**을 선택한 다음, **완료**를 선택합니다. 

> [!NOTE]
> **Azure Active Directory**가 다른 애플리케이션에 대한 사용 권한에 나열되지 않으면 **추가**를 선택하고 목록에서 추가합니다.

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>개발자 포털에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정

이제 Azure AD에서 애플리케이션을 만들었으며 client-app에서 backend-app을 호출하도록 허용하는 적절한 권한을 부여했습니다. 

이 예제에서는 개발자 콘솔이 client-app입니다. 아래 단계에서는 개발자 콘솔에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정하는 방법을 설명합니다. 

1. Azure Portal에서 API Management 인스턴스로 이동합니다.

2. **OAuth 2.0** > **추가**를 선택합니다.

3. **표시 이름** 및 **설명**을 제공합니다.

4. **클라이언트 등록 페이지 URL**에 대해 `http://localhost`와 같은 자리 표시자 값을 입력합니다. **클라이언트 등록 페이지 URL** 은 이를 지원하는 OAuth 2.0 공급자에 대한 자체 계정을 사용자가 만들고 구성하는 데 사용할 수 있는 페이지를 가리킵니다. 이 예제에서는 사용자가 자신의 계정을 만들고 구성하지 않으므로 대신 자리 표시자를 사용합니다.

5. **권한 부여 유형**에 **인증 코드**를 선택합니다.

6. **권한 부여 엔드포인트 URL** 및**토큰 엔드포인트 URL**을 지정합니다. 이러한 값은 Azure AD 테넌트의 **엔드포인트** 페이지에서 검색합니다. **앱 등록** 페이지로 다시 이동하여 **엔드포인트**를 선택합니다.

    >[!NOTE]
    > 여기에서 **v1** 엔드포인트를 사용합니다.

7. **OAuth 2.0 권한 부여 엔드포인트**를 복사하여 **권한 부여 엔드포인트 URL** 텍스트 상자에 붙여 넣습니다.

8. **OAuth 2.0 토큰 엔드포인트**를 복사하여 **토큰 엔드포인트 URL** 텍스트 상자에 붙여 넣습니다. 토큰 엔드포인트에 붙여넣는 것 외에도 **resource**라는 본문 매개 변수를 추가합니다. 이 매개 변수의 값에는 백 엔드 앱의 **애플리케이션 ID**를 사용합니다.

9. 다음으로 클라이언트 자격 증명을 지정합니다. 이러한 자격 증명은 client-app에 대한 자격 증명입니다.

10. **클라이언트 ID**에는 client-app에 대한 **애플리케이션 ID**를 사용합니다.

11. **클라이언트 암호**로 이전에 client-app에 대해 만든 키를 사용합니다. 

12. 클라이언트 암호 바로 뒤에는 권한 부여 코드 권한 유형에 대한 **redirect_url**이 나옵니다. 이 URL을 적어둡니다.

13. **만들기**를 선택합니다.

14. client-app의 **설정** 페이지로 돌아갑니다.

15. **회신 URL**을 선택하고 **redirect_url**을 첫 번째 행에 붙여 넣습니다. 이 예제에서는 `https://localhost`를 첫 번째 행의 URL로 바꾸었습니다.  

OAuth 2.0 권한 부여 서버를 구성했으므로 개발자 콘솔에서 Azure AD의 액세스 토큰을 가져올 수 있습니다. 

다음 단계는 API에 OAuth 2.0 사용자 권한 부여를 사용하도록 설정하는 것입니다. 이를 통해 개발자 콘솔에서 API를 호출하기 전에 사용자를 대신하여 액세스 토큰을 확보해야 한다는 것을 알 수 있습니다.

1. API Management 인스턴스로 이동하고 **API**로 이동합니다.

2. 보호하려는 API를 선택합니다. 이 예제에서는 `Echo API`를 사용합니다.

3. **설정**으로 이동합니다.

4. **보안** 아래에서 **OAuth 2.0**을 선택하고 이전에 구성한 OAuth 2.0 서버를 선택합니다. 

5. **저장**을 선택합니다.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>개발자 포털에서 API 호출 성공

> [!NOTE]
> 이 섹션은 개발자 포털을 지원하지 않는 **소비** 계층에는 적용되지 않습니다.

`Echo API`에서 OAuth 2.0 사용자 권한 부여를 사용하도록 설정했으므로 개발자 콘솔은 API를 호출하기 전에, 사용자 대신 액세스 토큰을 얻습니다.

1. 개발자 포털의 `Echo API` 아래 작업으로 이동하여 **시도**를 선택합니다. 개발자 콘솔로 이동됩니다.

2. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

3. 권한 부여 드롭다운 목록에서 **권한 부여 코드**를 선택하면 Azure AD 테넌트에 로그인하라는 메시지가 표시됩니다. 이미 이 계정으로 로그인한 경우 메시지가 나타나지 않을 수 있습니다.

4. 성공적으로 로그인한 후 `Authorization` 헤더가 Azure AD의 액세스 토큰과 함께 요청에 추가됩니다. 다음은 샘플 토큰입니다(Base64로 인코딩됨).

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

5. **보내기**를 선택하면 API를 성공적으로 호출할 수 있습니다.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>미리 요청 권한을 부여하도록 JWT 유효성 검사 정책 구성

이 때 사용자가 개발자 콘솔에서 호출을 시도하면 로그인하라는 메시지가 표시됩니다. 개발자 콘솔은 사용자를 대신하여 액세스 토큰을 얻습니다.

토큰 없이 또는 잘못된 토큰을 사용하여 API를 호출하면 어떻게 될까요? 예를 들어 `Authorization` 헤더를 삭제하더라도 API를 호출할 수 있습니다. API Management가 이 시점에는 액세스 토큰의 유효성을 검사하지 않기 때문입니다. 백 엔드 API에 `Authorization` 헤더만 전달합니다.

API Management에서 [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 들어오는 요청마다 액세스 토큰에 대한 유효성을 검사하면 요청을 사전 승인할 수 있습니다. 요청에 유효한 토큰이 없으면 API Management에서 차단합니다. 예를 들어 `Echo API`의 `<inbound>` 정책 섹션에 아래 정책을 추가할 수 있습니다. 액세스 토큰에서 audience 클레임을 확인하고 토큰이 유효하지 않은 경우 오류 메시지를 반환합니다. 정책을 구성하는 방법에 대한 내용은 [정책 설정 또는 편집](set-edit-policies.md)을 참조하세요.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>API를 호출하는 애플리케이션 빌드

이 가이드에서는 OAuth 2.0으로 보호되는 `Echo API`를 호출하기 위해 API Management에서 개발자 콘솔을 샘플 클라이언트 애플리케이션으로 사용했습니다. OAuth 2.0을 구현하고 애플리케이션을 빌드하는 방법을 자세히 알려보려면 [Azure Active Directory 코드 샘플](../active-directory/develop/sample-v1-code.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-scenarios.md)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.

* [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)

* [첫 번째 API 관리](import-and-publish.md)
