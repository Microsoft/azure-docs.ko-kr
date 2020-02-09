---
title: Azure AD FS 지원 (MSAL Python)
titleSuffix: Microsoft identity platform
description: Python 용 Microsoft 인증 라이브러리의 AD FS (Active Directory Federation Services) 지원에 대해 알아보기
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699549"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Python 용 MSAL에서 지원 Active Directory Federation Services

Windows Server의 Active Directory Federation Services (AD FS)를 사용 하면 Python 용 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 앱에 Openid connect Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 추가할 수 있습니다. 앱은 Python 라이브러리에 대해 MSAL을 사용 하 여 AD FS에 대해 직접 사용자를 인증할 수 있습니다. 시나리오에 대 한 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/ad-fs-development)를 참조 하십시오.

일반적으로 AD FS에 대해 인증 하는 방법에는 두 가지가 있습니다.

- MSAL Python은 다른 id 공급자와 페더레이션 되는 Azure Active Directory에 대 한 통신을 제공 합니다. 페더레이션은 AD FS를 통해 발생 합니다. MSAL Python은 azure ad (관리 되는 사용자)에서 관리 되는 사용자 또는 AD FS (페더레이션된 사용자)와 같은 다른 id 공급자가 관리 하는 사용자에 게 로그인 하는 Azure AD에 연결 합니다. MSAL Python은 사용자가 페더레이션 임을 인식 하지 못합니다. 단지 Azure AD와만 통신 합니다. 이 경우 사용 하는 [기관은](msal-client-application-configuration.md#authority) 일반적인 인증 기관 (기관 호스트 이름 + 테넌트, 공용 또는 조직)입니다.
- MSAL Python은 AD FS 기관에 직접 통신 합니다. AD FS 2019 이상 에서만 지원 됩니다.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>AD FS를 사용 하 여 페더레이션 Active Directory에 연결

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>페더레이션된 사용자에 대해 대화형으로 토큰 획득

다음은 Active Directory Federation Services (AD FS) 또는 Active Directory를 통해 직접 연결 하는지 여부에 적용 됩니다.

`acquire_token_by_authorization_code` 또는 `acquire_token_by_device_flow`를 호출 하는 경우 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력 합니다.
2. Azure AD는 "조직의 페이지로 이동" 메시지를 잠깐 표시 하 고 사용자는 id 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용 하 여 사용자 지정 됩니다.

이 페더레이션된 시나리오에서 지원 되는 AD FS 버전은 다음과 같습니다.
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>사용자 이름 및 암호를 통해 토큰 획득

다음은 Active Directory Federation Services (AD FS) 또는 Active Directory를 통해 직접 연결 하는지 여부에 적용 됩니다.

`acquire_token_by_username_password`를 사용 하 여 토큰을 획득 하는 경우 MSAL Python은 사용자 이름에 따라 연락할 id 공급자를 가져옵니다. MSAL Python은 id 공급자 로부터 [SAML 1.1 토큰](reference-saml-tokens.md) 을 가져온 다음 JWT (JSON Web Token)를 반환 하는 Azure AD에 제공 합니다.

## <a name="connecting-directly-to-ad-fs"></a>AD FS에 직접 연결

AD FS에 디렉터리를 연결 하는 경우 응용 프로그램을 빌드하는 데 사용 하려는 기관은 다음과 같습니다 `https://somesite.contoso.com/adfs/`

MSAL Python은 ADFS 2019를 지원 합니다.

ADFS 2016 또는 ADFS v2에 대 한 직접 연결을 지원 하지 않습니다. ADFS 2016에 대 한 직접 연결을 요구 하는 시나리오를 지원 해야 하는 경우 최신 버전의 ADAL Python을 사용 합니다. 온-프레미스 시스템을 ADFS 2019로 업그레이드 한 후 MSAL Python을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 페더레이션된 사례는 [홈 영역 검색 정책을 사용 하 여 응용 프로그램에 대 한 Azure Active Directory 로그인 동작 구성](../manage-apps/configure-authentication-for-federated-users-portal.md) 을 참조 하세요.