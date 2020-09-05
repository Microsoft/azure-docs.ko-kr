---
title: Microsoft ID 플랫폼 및 SAML 전달자 어설션 흐름 | Azure
description: SAML 전달자 어설션 흐름을 사용하여 사용자에게 자격 증명을 묻는 메시지를 표시하지 않고 Microsoft Graph에서 데이터를 페치하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 6e7e4dd6383b1f264ff2da7893d9f86a3708217d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227919"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft ID 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름
OAuth 2.0 SAML 전달자 어설션 흐름을 사용하면 클라이언트에서 기존 트러스트 관계를 사용해야 할 때 SAML 어설션을 사용하여 OAuth 액세스 토큰을 요청할 수 있습니다. SAML 어설션에 적용되는 서명은 권한 있는 앱의 인증을 제공합니다. SAML 어설션은 ID 공급자가 발급하고 서비스 공급자가 사용하는 XML 보안 토큰입니다. 서비스 공급자는 해당 콘텐츠를 사용하여 보안 관련 목적의 어설션 주체를 식별합니다.

SAML 어설션이 OAuth 토큰 엔드포인트에 게시됩니다.  엔드포인트는 어설션을 처리하고 앱의 이전 승인에 따라 액세스 토큰을 발급합니다. 클라이언트는 새로 고침 토큰을 보유하거나 저장할 필요가 없으며 토큰 엔드포인트에 클라이언트 비밀을 전달할 필요도 없습니다.

SAML 전달자 어설션 흐름은 사용자에게 자격 증명을 묻는 메시지를 표시하지 않고 Microsoft Graph API(위임된 권한만 지원)에서 데이터를 페치할 때 유용합니다. 이 시나리오에서는 백그라운드 프로세스에 선호되는 클라이언트 자격 증명 부여가 작동하지 않습니다.

대화형 브라우저 기반 로그인을 수행하여 SAML 어설션을 가져온 다음, OAuth로 보호된 API(예: Microsoft Graph)에 대한 액세스를 추가하려는 애플리케이션의 경우 API에 대한 액세스 토큰을 가져오라는 OAuth 요청을 수행할 수 있습니다. 브라우저가 사용자를 인증하기 위해 Azure AD로 리디렉션되는 경우 브라우저는 SAML 로그인에서 세션을 선택하고 사용자는 자격 증명을 입력하지 않아도 됩니다.

OAuth SAML 전달자 어설션 흐름은 Azure Active Directory에 페더레이션된 Active Directory Federation Services (ADFS)와 같은 id 공급자를 사용 하 여 인증 하는 사용자에 대해서만 지원 됩니다.  ADFS에서 가져온 SAML 어설션은 OAuth 흐름에서 사용자를 인증하는 데 사용할 수 있습니다.

![OAuth 흐름](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>SAML 전달자 어설션을 사용하여 그래프 호출
이제 프로그래밍 방식으로 SAML 어설션을 실제로 페치할 수 있는 방법에 대해 알아보겠습니다. 이 접근 방식은 ADFS로 테스트됩니다. 그러나 이 방식은 SAML 어설션의 반환을 프로그래밍 방식으로 지원하는 모든 ID 공급자에서 작동합니다. 기본 프로세스는 SAML 어설션을 가져오고, 액세스 토큰을 가져오고, Microsoft Graph에 액세스하는 것입니다.

### <a name="prerequisites"></a>필수 구성 요소

권한 부여 서버/환경(Microsoft 365)과 ID 공급자 또는 SAML 2.0 전달자 어설션의 발급자(ADFS) 간에 트러스트 관계를 설정합니다. Single Sign-On 및 ID 공급자에 대한 ADFS를 구성하려면 [이 문서](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365)를 참조할 수 있습니다.

[포털](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)에 애플리케이션을 등록합니다.
1. [포털의 앱 등록 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)에 로그인합니다. Graph API용 v2.0 엔드포인트를 사용하고 있으므로 이 포털에서 애플리케이션을 등록해야 합니다. 그렇지 않은 경우 Azure Active Directory에서 등록을 사용할 수 있습니다. 
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다. 
    1. **이름** -앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다.
    1. **지원되는 계정 유형** - 애플리케이션이 지원하려는 계정을 선택합니다.
    1. **리디렉션 URI(선택 사항)** - 빌드하는 앱의 유형을 웹 또는 퍼블릭 클라이언트(모바일 및 데스크톱)로 선택한 다음, 애플리케이션의 리디렉션 URI(또는 답장 URL)를 입력합니다.
    1. 작업을 마쳤으면 **등록**을 선택합니다.
1. 애플리케이션(클라이언트) ID를 적어 둡니다.
1. 왼쪽 창에서 **인증서 및 암호**를 선택합니다. **새 클라이언트 비밀** 섹션에서 **새 클라이언트 비밀**을 선택합니다. 새 클라이언트 암호를 복사하면 블레이드를 나갈 때를 검색할 수 없습니다.
1. 왼쪽 창에서 **API 사용 권한**을 선택한 다음, **권한 추가**를 선택합니다. **Microsoft Graph**를 선택하고 **위임된 권한**을 선택한 후 Outlook Graph API를 사용하려고 하므로 **Tasks.read**를 선택합니다. 

샘플 요청을 테스트하는 데 필요한 도구인 [Postman](https://www.getpostman.com/)을 설치합니다.  나중에 요청을 코드로 변환할 수 있습니다.

### <a name="get-the-saml-assertion-from-adfs"></a>ADFS에서 SAML 어설션 가져오기
SAML 어설션을 페치하기 위해 SOAP 봉투를 사용하여 ADFS 엔드포인트에 대한 POST 요청을 만듭니다.

![SAML 어설션 가져오기](./media/v2-saml-bearer-assertion/2.png)

헤더 값:

![헤더 값](./media/v2-saml-bearer-assertion/3.png)

ADFS 요청 본문:

![ADFS 요청 본문](./media/v2-saml-bearer-assertion/4.png)

이 요청이 성공적으로 게시되면 ADFS에서 SAML 어설션을 받게 됩니다. **SAML:Assertion** 태그 데이터만 필요하므로 후속 요청에서 사용할 수 있도록 base64 인코딩으로 변환합니다.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>SAML 어설션을 사용하여 OAuth2 토큰 가져오기 
이 단계에서는 ADFS 어설션 응답을 사용하여 OAuth2 토큰을 페치합니다.

1. 헤더 값을 사용하여 아래와 같이 POST 요청을 만듭니다.

    ![POST 요청](./media/v2-saml-bearer-assertion/5.png)
1. 요청 본문에서 **client_ID**, **client_secret** 및 **assertion**(이전 단계에서 가져온 base64 인코딩 SAML 어설션)을 바꿉니다.

    ![요청 본문](./media/v2-saml-bearer-assertion/6.png)
1. 요청이 성공적으로 완료되면 Azure Active Directory에서 액세스 토큰을 받게 됩니다.

### <a name="get-the-data-with-the-oauth-token"></a>Oauth 토큰을 사용하여 데이터 가져오기

액세스 토큰을 받은 후 Graph API(이 예제에서는 Outlook 작업)를 호출합니다. 

1. 이전 단계에서 페치한 액세스 토큰을 사용하여 GET 요청을 만듭니다.

    ![GET 요청](./media/v2-saml-bearer-assertion/7.png)

1. 요청이 성공적으로 완료되면 JSON 응답을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

다른 [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아봅니다.
