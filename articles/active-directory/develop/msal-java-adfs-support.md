---
title: AD FS 지원 (Java 용 MSAL)
titleSuffix: Microsoft identity platform
description: Java 용 Microsoft Authentication Library (MSAL4j)의 Active Directory Federation Services (AD FS) 지원에 대해 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696217"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Java 용 MSAL에서 Active Directory Federation Services 지원

Windows Server의 Active Directory Federation Services (AD FS)를 사용 하 여 Java 용 MSAL 용 Microsoft 인증 라이브러리 앱에 Openid connect Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 추가할 수 있습니다. 통합 된 앱은 Azure AD를 통해 페더레이션된 AD FS 사용자를 인증할 수 있습니다. 시나리오에 대 한 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/ad-fs-development)를 참조 하십시오.

Java 용 MSAL을 사용 하는 앱은 Azure Active Directory (Azure AD)와 통신 하 여 AD FS 페더레이션.

Java 용 MSAL은 azure ad (관리 되는 사용자)에서 관리 되는 사용자 또는 AD FS (페더레이션된 사용자)와 같은 다른 id 공급자가 관리 하는 사용자에 게 로그인 하는 Azure AD에 연결 합니다. Java 용 MSAL은 사용자가 페더레이션된 임을 인식 하지 못합니다. 단지 Azure AD와만 통신 합니다.

이 경우 사용 하는 [기관은](msal-client-application-configuration.md#authority) 일반적인 인증 기관 (기관 호스트 이름 + 테넌트, 공용 또는 조직)입니다.

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>페더레이션된 사용자에 대해 대화형으로 토큰 획득

`AuthorizationCodeParameters` 또는 `DeviceCodeParameters`를 사용 하 여 `ConfidentialClientApplication.AcquireToken()` 또는 `PublicClientApplication.AcquireToken()`를 호출 하는 경우 일반적으로 사용자 환경이 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력 합니다.
2. Azure AD에는 "조직의 페이지로 이동"이 잠깐 표시 되 고 사용자는 id 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용 하 여 사용자 지정 됩니다.

이 페더레이션된 시나리오에서 지원 되는 AD FS 버전은 다음과 같습니다.
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>사용자 이름 및 암호를 통해 토큰 획득

`IntegratedWindowsAuthenticationParameters` 또는 `UsernamePasswordParameters`에서 `ConfidentialClientApplication.AcquireToken()` 또는 `PublicClientApplication.AcquireToken()`를 사용 하 여 토큰을 획득 하는 경우 Java 용 MSAL은 사용자 이름을 기준으로 연락할 id 공급자를 가져옵니다. Java 용 MSAL은 id 공급자 로부터 [SAML 1.1 토큰](reference-saml-tokens.md) 토큰을 가져온 다음 JWT (JSON Web Token)를 반환 하는 Azure AD에 제공 합니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례는 [홈 영역 검색 정책을 사용 하 여 응용 프로그램에 대 한 Azure Active Directory 로그인 동작 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 을 참조 하세요.