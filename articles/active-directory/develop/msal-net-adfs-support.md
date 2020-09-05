---
title: MSAL.NET의 AD FS 지원 | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)의 Active Directory Federation Services (AD FS) 지원에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4107b0785ef4635e0e5e745a35b605d2634a220d
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166232"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET에서 지원 Active Directory Federation Services
Windows Server의 Active Directory Federation Services (AD FS)를 사용 하면 개발 중인 응용 프로그램에 Openid connect Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 추가할 수 있습니다. 이러한 응용 프로그램은 AD FS에 대해 직접 사용자를 인증할 수 있습니다. 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)를 참조 하세요.

Microsoft Authentication Library for .NET (MSAL.NET)은 AD FS에 대해 인증 하는 두 가지 시나리오를 지원 합니다.

- MSAL.NET는 AD FS와 *페더레이션* 되는 Azure Active Directory에 대해 설명 합니다.
- MSAL.NET는 ADFS 기관에 **직접** 통신 합니다. AD FS 2019 이상 에서만 지원 됩니다. 이러한 강조 표시 되는 시나리오 중 하나는 지원 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 입니다.


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL은 AD FS와 페더레이션된 Azure AD에 연결 합니다.
MSAL.NET는 관리 되는 사용자 (Azure AD에서 관리 되는 사용자) 또는 페더레이션된 사용자 (AD FS와 같은 다른 id 공급자가 관리 하는 사용자)에 로그인 하는 Azure AD에 대 한 연결을 지원 합니다. MSAL.NET는 사용자가 페더레이션 된다는 사실을 알지 못합니다. 중요 한 것 처럼 Azure AD와 통신 합니다.

이 경우 사용 하는 [기관은](msal-client-application-configuration.md#authority) 일반적인 인증 기관 (기관 호스트 이름 + 테 넌 트, 공용 또는 조직)입니다.

### <a name="acquiring-a-token-interactively"></a>대화형으로 토큰 가져오기
메서드를 호출 하는 경우 `AcquireTokenInteractive` 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력 합니다.
2. Azure AD는 "조직의 페이지로 이동" 메시지를 잠깐 표시 합니다.
3. 사용자가 id 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용 하 여 사용자 지정 됩니다.

이 페더레이션된 시나리오에서 지원 되는 AD FS 버전은 AD FS v2, AD FS v3 (Windows Server 2012 R2) 및 AD FS v4 (AD FS 2016)입니다.

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication 또는 AcquireTokenByUsernamePassword를 사용 하 여 토큰 가져오기
또는 메서드를 사용 하 여 토큰을 획득 하는 경우 `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` MSAL.NET는 사용자 이름을 기준으로 연락할 id 공급자를 가져옵니다.  MSAL.NET는 id 공급자에 연결한 후 [SAML 1.1 토큰](reference-saml-tokens.md) 을 수신 합니다.  그런 다음 MSAL.NET은 JWT를 가져오기 위해 Azure AD에 사용자 어설션으로 SAML 토큰을 제공 합니다 (단계별 [흐름과](msal-authentication-flows.md#on-behalf-of)비슷함).

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL은 AD FS에 직접 연결 됩니다.
MSAL.NET는 Open ID Connect 규격 이며 PKCE 및 범위를 이해 하는 AD FS 2019에 대 한 연결을 지원 합니다. 이 지원을 사용 하려면 Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) 가 Windows Server에 적용 되어야 합니다. AD FS에 직접 연결 하는 경우 응용 프로그램을 빌드하는 데 사용 하려는 기관은와 비슷합니다 `https://mysite.contoso.com/adfs/` .

현재 다음에 대 한 직접 연결을 지원할 계획이 없습니다.

- 16 AD FS PKCE를 지원 하지 않고 범위를 사용 하지 않고 리소스를 계속 사용 합니다.
- OIDC 규격이 아닌 AD FS v2

 AD FS 2016에 대 한 직접 연결을 요구 하는 시나리오를 지원 해야 하는 경우 [Azure Active Directory 인증 라이브러리](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)의 최신 버전을 사용 합니다. 온-프레미스 시스템을 AD FS 2019으로 업그레이드 한 경우 MSAL.NET를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례는 [홈 영역 검색 정책을 사용 하 여 응용 프로그램에 대 한 Azure Active Directory 로그인 동작 구성](../manage-apps/configure-authentication-for-federated-users-portal.md) 을 참조 하세요.