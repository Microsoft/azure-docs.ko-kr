---
title: Microsoft id 플랫폼 & SAML 전달자 어설션 흐름 | Microsoft
description: SAML 전달자 어설션 흐름을 사용 하 여 사용자에 게 자격 증명을 묻는 메시지를 표시 하지 않고 Microsoft Graph에서 데이터를 인출 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700212"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft id 플랫폼 및 OAuth 2.0 SAML 전달자 어설션 흐름
OAuth 2.0 SAML 전달자 어설션 흐름을 사용 하면 클라이언트에서 기존 트러스트 관계를 사용 해야 할 때 SAML 어설션을 사용 하 여 OAuth 액세스 토큰을 요청할 수 있습니다. SAML 어설션에 적용 되는 서명은 권한 있는 앱의 인증을 제공 합니다. SAML 어설션은 id 공급자가 발급 하 고 서비스 공급자가 사용 하는 XML 보안 토큰입니다. 서비스 공급자는 해당 콘텐츠를 사용 하 여 보안 관련 목적의 어설션의 주체를 식별 합니다.

SAML 어설션이 OAuth 토큰 엔드포인트에 게시 됩니다.  엔드포인트은 어설션을 처리 하 고 앱의 이전 승인에 따라 액세스 토큰을 발급 합니다. 클라이언트는 새로 고침 토큰을 보유 하거나 저장할 필요가 없으며 토큰 엔드포인트에 전달 하는 데 필요한 클라이언트 비밀이 아닙니다.

SAML 전달자 어설션 흐름은 사용자에 게 자격 증명을 묻는 메시지를 표시 하지 않고 Microsoft Graph Api (위임 된 권한만 지원)에서 데이터를 가져올 때 유용 합니다. 이 시나리오에서는 백그라운드 프로세스에 선호 되는 클라이언트 자격 증명 부여가 작동 하지 않습니다.

대화형 브라우저 기반 로그인을 수행 하 여 SAML 어설션을 가져온 다음 OAuth로 보호 된 API (예: Microsoft Graph)에 대 한 액세스를 추가 하려는 응용 프로그램의 경우 API에 대 한 액세스 토큰을 가져오도록 OAuth 요청을 수행할 수 있습니다. 브라우저가 사용자를 인증 하기 위해 Azure AD로 리디렉션되는 경우 브라우저는 SAML 로그인에서 세션을 선택 하 고 사용자는 자격 증명을 입력 하지 않아도 됩니다.

Azure Active Directory에 페더레이션된 Active Directory Federation Services (ADFS)와 같은 id 공급자를 사용 하 여 인증 하는 사용자에 대해서도 OAuth SAML 전달자 어설션 흐름이 지원 됩니다.  ADFS에서 가져온 SAML 어설션은 OAuth 흐름에서 사용자를 인증 하는 데 사용할 수 있습니다.

![OAuth 흐름](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>SAML 전달자 어설션을 사용 하 여 그래프 호출
이제 프로그래밍 방식으로 SAML 어설션을 실제로 페치할 수 있는 방법에 대해 알아보겠습니다. 이 접근 방식은 ADFS로 테스트 됩니다. 그러나이는 SAML 어설션의 반환을 프로그래밍 방식으로 지 원하는 모든 id 공급자와 함께 작동 합니다. 기본 프로세스는 SAML 어설션을 가져오고, 액세스 토큰을 가져오고, Microsoft Graph 액세스 하는 것입니다.

### <a name="prerequisites"></a>필수 조건

권한 부여 서버/환경 (Microsoft 365)과 id 공급자 또는 SAML 2.0 전달자 어설션 (ADFS)의 발급자 간에 트러스트 관계를 설정 합니다. Single Sign-On 및 id 공급자로 ADFS를 구성 하려면 [이 문서](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)를 참조할 수 있습니다.

[포털](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)에서 응용 프로그램을 등록 합니다.
1. [포털의 앱 등록 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 에 로그인 합니다 (Graph API에 v2.0 엔드포인트을 사용 하 고 있으므로이 포털에서 응용 프로그램을 등록 해야 합니다. 그렇지 않은 경우 Azure active directory에서 등록을 사용할 수 있습니다. 
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다. 
    1. **이름** -앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름을 입력합니다.
    1. **지원되는 계정 유형** - 애플리케이션이 지원하려는 계정을 선택합니다.
    1. **URI 리디렉션 (선택 사항)** -& 빌드할 앱 유형 (선택 사항)을 선택 하 고 응용 프로그램에 대 한 리디렉션 URI 또는 회신 URL을 입력 합니다.
    1. 작업을 마쳤으면 **등록**을 선택합니다.
1. 응용 프로그램 (클라이언트) ID를 적어 둡니다.
1. 왼쪽 창에서 **인증서 & 암호**를 선택 합니다. **클라이언트 암호** 섹션에서 **새 클라이언트 암호** 를 클릭 합니다. 새 클라이언트 암호를 복사 하면 블레이드를 나갈 때를 검색할 수 없습니다.
1. 왼쪽 창에서 **API 권한** 을 선택한 다음 **사용 권한을 추가**합니다. **Microsoft Graph**, 위임 된 **권한**을 차례로 선택한 다음 Outlook Graph API를 사용 하기 때문에 작업을 선택 **합니다.** 

샘플 요청을 테스트 하는 데 필요한 도구인 [Postman](https://www.getpostman.com/)을 설치 합니다.  나중에 요청을 코드로 변환할 수 있습니다.

### <a name="get-the-saml-assertion-from-adfs"></a>ADFS에서 SAML 어설션 가져오기
SAML 어설션을 인출 하기 위해 SOAP envelope을 사용 하 여 ADFS 엔드포인트에 대 한 POST 요청을 만듭니다.

![SAML 어설션 가져오기](./media/v2-saml-bearer-assertion/2.png)

헤더 값:

![헤더 값](./media/v2-saml-bearer-assertion/3.png)

ADFS 요청 본문:

![ADFS 요청 본문](./media/v2-saml-bearer-assertion/4.png)

이 요청이 성공적으로 게시 되 면 ADFS에서 SAML 어설션을 받게 됩니다. **SAML: Assertion** 태그 데이터만 필요 합니다. 추가 요청에서 사용할 수 있도록 base64 인코딩으로 변환 합니다.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>SAML 어설션을 사용 하 여 OAuth2 토큰 가져오기 
이 단계에서는 ADFS 어설션 응답을 사용 하 여 OAuth2 토큰을 인출 합니다.

1. 헤더 값을 사용 하 여 아래와 같이 POST 요청을 만듭니다.

    ![POST 요청](./media/v2-saml-bearer-assertion/5.png)
1. 요청 본문에서 **client_id**, **client_secret**및 **어설션** (이전 단계에서 가져온 base64 인코딩된 SAML 어설션)을 바꿉니다.

    ![요청 본문](./media/v2-saml-bearer-assertion/6.png)
1. 요청이 성공적으로 완료 되 면 Azure active directory에서 액세스 토큰을 받게 됩니다.

### <a name="get-the-data-with-the-oauth-token"></a>Oauth 토큰을 사용 하 여 데이터 가져오기

액세스 토큰을 받은 후 Graph Api (이 예제에서는 Outlook 작업)를 호출 합니다. 

1. 이전 단계에서 인출 된 액세스 토큰을 사용 하 여 GET 요청을 만듭니다.

    ![GET 요청](./media/v2-saml-bearer-assertion/7.png)

1. 요청이 성공적으로 완료 되 면 JSON 응답을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

다양 한 [인증 흐름 및 응용 프로그램 시나리오](authentication-flows-app-scenarios.md)에 대해 알아봅니다.