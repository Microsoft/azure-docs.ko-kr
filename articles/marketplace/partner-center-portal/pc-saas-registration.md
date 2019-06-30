---
title: SaaS 응용 프로그램 등록 | Azure Marketplace
description: Azure portal을 사용 하 여 SaaS 응용 프로그램을 등록 하는 방법에 설명 합니다.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 1f644dca7a057667fb37f5a79a4683c592059e7a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331607"
---
# <a name="register-a-saas-application"></a>SaaS 애플리케이션 등록

이 문서에서는 Microsoft를 사용 하 여 SaaS 응용 프로그램을 등록 하는 방법에 설명 [Azure portal](https://portal.azure.com/)합니다.  등록에 성공한 후 SaaS Fulfillment Api에 액세스 하는 데 사용할 수 있는 Azure Active Directory (Azure AD) 보안 토큰을 받게 됩니다.  Azure AD에 대 한 자세한 내용은 참조 하세요. [인증 이란?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>서비스 간 인증 흐름

다음 다이어그램은 새 고객에게 구독 흐름과 이러한 API가 사용되는 경우를 보여 줍니다.

![SaaS 제안 API 흐름](./media/saas-offer-publish-api-flow-v1.png)

Azure는 해당 최종 사용자에게 SaaS 서비스가 노출하는 인증을 제한하지 않습니다. 그러나 인증 SaaS Fulfillment Api를 사용 하 여 Azure portal 통해 SaaS 앱을 등록 하 여 가져온 일반적으로 Azure AD 보안 토큰을 사용 하 여 수행 됩니다. 


## <a name="register-an-azure-ad-secured-app"></a>Azure AD로 보호 된 앱 등록

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 애플리케이션이 위치한 URL, 사용자가 인증된 후 회신을 보낼 URL, 앱을 식별하는 URI 등과 같이 애플리케이션에 대한 Azure AD 세부 정보의 제공이 포함됩니다.  Azure Portal을 사용하여 새 애플리케이션을 등록하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  둘 이상에 대 한 액세스, 오른쪽 위 모서리에서 계정을 클릭 하 고 원하는 Azure ad 포털 세션을 설정 하면 계정에서는 테 넌 트 경우.
3.  왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록** 및 **새 애플리케이션 등록**을 차례로 클릭합니다.

    ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1.png)

4.  만들기 페이지에서 애플리케이션의 등록\' 정보를 입력합니다.
    -   **이름**: 의미 있는 애플리케이션 이름을 입력합니다.
    -   **애플리케이션 형식**: 
        - 장치에 로컬로 설치된 [클라이언트 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)의 경우 **네이티브**를 선택합니다. 이 설정은 OAuth 공개 [네이티브 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)에 사용됩니다.
        - 보안 서버에 설치된 [클라이언트 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) 및 [리소스/API 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)의 경우 **웹앱/API**를 선택합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) 및 공용 [사용자 에이전트 기반 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)에 사용됩니다.
        동일한 애플리케이션이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.
    -   **로그온 URL**: 웹앱/API 애플리케이션에서 앱의 기준 URL을 제공합니다.웹 애플리케이션의 경우 앱의 기준 URL을 제공합니다. 예를 들어 **http://localhost:31544** 은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
    -   **리디렉션 URI**: 네이티브 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션에 고유하게 해당되는 값을 입력합니다(예: **http://MyFirstAADApp** ).

        ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1-2.png)

        웹 응용 프로그램 또는 네이티브 응용 프로그램에 대 한 구체적인 예에 대 한 빠른 시작을 확인해 단계별 설치 과정에서 사용할 수 있는 합니다 *시작* 섹션을 [Azure AD 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)합니다.

5.  작업을 마쳤으면 **만들기**를 클릭합니다. Azure AD가 고유한 할당 *응용 프로그램 ID* 응용 프로그램을\'응용 프로그램을 이동으로 다시\'기본 등록 페이지입니다. 애플리케이션이 웹 또는 네이티브 애플리케이션인지에 따라 애플리케이션에 기능을 추가하기 위해 다른 옵션이 제공됩니다.

>[!Note]
>기본적으로 새로 등록된 된 응용 프로그램은 응용 프로그램에 로그인 하는 동일한 테 넌 트의 사용자만을 허용 하도록 구성 됩니다.


## <a name="using-the-azure-ad-security-token"></a>Azure AD 보안 토큰을 사용 하 여

응용 프로그램을 등록 한 후에 프로그래밍 방식으로 Azure AD 보안 토큰을 요청할 수 있습니다.  게시자는이 토큰을 사용 하 고 문제를 해결 하는 요청을 수행 해야 합니다.  다양 한 처리 Api를 사용 하는 경우 토큰 쿼리 매개 변수 경우 URL에서 사용자가 Azure에서 SaaS 웹 사이트로 리디렉션됩니다.  이 토큰은 1만 유효 합니다.  URL은 또한 사용 하기 전에 브라우저에서 토큰 값을 디코딩합니다.

이러한 토큰에 대 한 자세한 내용은 참조 하세요. [Azure Active Directory 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)합니다.


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
|  Resource           | True         |  토큰이 요청된 대상 리소스입니다. 기본값은 `62d94f6c-d599-489b-a797-3e10e42fbe22`입니다. |
|  |  |  |


*응답*

|  **Name**  | **형식**       |  **설명**    |
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

Azure AD로 보호 된 앱을 이제 사용할 수는 [SaaS Fulfillment API 버전 2](./pc-saas-fulfillment-api-v2.md)합니다.
