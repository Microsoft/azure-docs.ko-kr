---
title: SaaS 응용 프로그램 등록 | Azure Marketplace
description: Azure Portal를 사용 하 여 SaaS 응용 프로그램을 등록 하는 방법을 설명 합니다.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: b2e02e42ab63f893574ca5217fd2f36c7481aabd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827951"
---
# <a name="register-a-saas-application"></a>SaaS 애플리케이션 등록

이 문서에서는 Microsoft [Azure Portal](https://portal.azure.com/)를 사용 하 여 SaaS 응용 프로그램을 등록 하는 방법을 설명 합니다.  성공적으로 등록 된 후 SaaS 처리 Api에 액세스 하는 데 사용할 수 있는 Azure Active Directory (Azure AD) 보안 토큰을 받게 됩니다.  Azure AD에 대 한 자세한 내용은 [인증 이란?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios) 을 참조 하세요.


## <a name="service-to-service-authentication-flow"></a>서비스 간 인증 흐름

다음 다이어그램은 새 고객에게 구독 흐름과 이러한 API가 사용되는 경우를 보여 줍니다.

![SaaS 제안 API 흐름](./media/saas-offer-publish-api-flow-v1.png)

Azure는 해당 최종 사용자에게 SaaS 서비스가 노출하는 인증을 제한하지 않습니다. 그러나 SaaS 처리 Api를 사용 하는 인증은 Azure AD 보안 토큰을 사용 하 여 수행 됩니다. 일반적으로 Azure Portal를 통해 SaaS 앱을 등록 하 여 가져옵니다. 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD 보안 앱 등록

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 애플리케이션이 위치한 URL, 사용자가 인증된 후 회신을 보낼 URL, 앱을 식별하는 URI 등과 같이 애플리케이션에 대한 Azure AD 세부 정보의 제공이 포함됩니다.  Azure Portal을 사용하여 새 애플리케이션을 등록하려면 다음 단계를 수행합니다.

1.  [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2.  계정에서 둘 이상의 액세스를 제공 하는 경우 오른쪽 위 모서리에서 자신의 계정을 클릭 하 고 포털 세션을 원하는 Azure AD 테넌트로 설정 합니다.
3.  왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록** 및 **새 애플리케이션 등록**을 차례로 클릭합니다.

    ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1.png)

4.  만들기 페이지에서 애플리케이션의 등록\' 정보를 입력합니다.
    -   **이름**: 의미 있는 애플리케이션 이름을 입력합니다.
    -   **애플리케이션 형식**: 
        - 디바이스에 로컬로 설치된 **클라이언트 애플리케이션**의 경우 [네이티브](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)를 선택합니다. 이 설정은 OAuth 공개 [네이티브 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)에 사용됩니다.
        - 보안 서버에 설치된 **클라이언트 애플리케이션** 및 [리소스/API 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)의 경우 [웹앱/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)를 선택합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) 및 공용 [사용자 에이전트 기반 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)에 사용됩니다.
        동일한 애플리케이션이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.
    -   **로그온 URL**: 웹앱/API 애플리케이션에서 앱의 기준 URL을 제공합니다. 예를 들어 **http://localhost:31544** 은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
    -   **리디렉션 URI**: 네이티브 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션에 고유하게 해당되는 값을 입력합니다(예: **http://MyFirstAADApp** ).

        ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1-2.png)

        웹 응용 프로그램 또는 네이티브 응용 프로그램에 대 한 특정 예제를 보려면 [AZURE AD 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)의 시작 섹션에서 사용할 수 있는 빠른 *시작* 안내를 확인 하세요.

5.  작업을 마쳤으면 **만들기**를 클릭합니다. Azure AD는 응용 프로그램에 고유한 *응용 프로그램 ID* 를 할당 하 고, 응용 프로그램\'s 주 등록 페이지로 다시 이동\'. 애플리케이션이 웹 또는 네이티브 애플리케이션인지에 따라 애플리케이션에 기능을 추가하기 위해 다른 옵션이 제공됩니다.

>[!Note]
>기본적으로 새로 등록 된 응용 프로그램은 동일한 테넌트의 사용자만 응용 프로그램에 로그인 할 수 있도록 구성 됩니다.


## <a name="using-the-azure-ad-security-token"></a>Azure AD 보안 토큰 사용

응용 프로그램을 등록 한 후에는 프로그래밍 방식으로 Azure AD 보안 토큰을 요청할 수 있습니다.  게시자는이 토큰을 사용 하 고이 토큰을 확인 하도록 요청 해야 합니다.  다양 한 처리 Api를 사용 하는 경우 사용자가 Azure에서 SaaS 웹 사이트로 리디렉션되는 경우 URL에 토큰 쿼리 매개 변수가 있습니다.  이 토큰은 1 시간 동안만 유효 합니다.  또한 사용 하기 전에 브라우저에서 토큰 값을 URL로 디코드 해야 합니다.

이러한 토큰에 대 한 자세한 내용은 [Azure Active Directory 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)을 참조 하세요.


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD 앱에 따라 토큰 가져오기

HTTP 메서드

`POST`

*요청 URL*

**https://login.microsoftonline.com/ *{tenantId}* /oauth2/token**

*URI 매개 변수*

|  **매개 변수 이름**  | **필수**  | **설명**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 등록된 AAD 애플리케이션의 테넌트 ID입니다.   |
|  |  |  |


*요청 헤더*

|  **헤더 이름**  | **필수** |  **설명**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  콘텐츠 형식     | True         | 요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다.  |
|  |  |  |


*요청 본문*

| **속성 이름**   | **필수** |  **설명**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 권한 부여 유형입니다. 기본값은 `client_credentials`입니다.                    |
|  Client_id          | True         |  Azure AD 앱과 연결된 클라이언트/앱 식별자입니다.                  |
|  client_secret      | True         |  Azure AD 앱과 연결된 암호입니다.                               |
|  리소스           | True         |  토큰이 요청된 대상 리소스입니다. 기본값은 `62d94f6c-d599-489b-a797-3e10e42fbe22`입니다. |
|  |  |  |


*응답*

|  **이름**  | **형식**       |  **설명**    |
| ---------- | -------------  | ------------------- |
| 200 정상    | TokenResponse  | 요청 성공   |
|  |  |  |

*TokenResponse*

샘플 응답 토큰:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>다음 단계

이제 Azure AD 보안 앱에서 [SaaS 앱 API 버전 2](./pc-saas-fulfillment-api-v2.md)를 사용할 수 있습니다.
