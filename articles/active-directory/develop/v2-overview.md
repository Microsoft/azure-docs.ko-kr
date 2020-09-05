---
title: Microsoft ID 플랫폼 개요 - Azure
description: Microsoft ID 플랫폼의 구성 요소 및 이러한 구성 요소가 애플리케이션에 IAM(ID 및 액세스 관리) 지원을 빌드하는 데 도움이 되는 방법에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40
ms.openlocfilehash: e34eeeca3b17087fb5bd43ae353e43b5ab355831
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119558"
---
# <a name="microsoft-identity-platform-overview"></a>Microsoft ID 플랫폼 개요

Microsoft ID 플랫폼을 통해 사용자 및 고객이 Microsoft ID 또는 소셜 계정을 사용하여 로그인할 수 있는 애플리케이션을 빌드하고 사용자 고유의 API 또는 Microsoft Graph와 같은 Microsoft API에 대한 액세스 권한을 부여할 수 있습니다.

Microsoft ID 플랫폼을 구성하는 몇 가지 구성 요소가 있습니다.

- **OAuth 2.0 및 OpenID Connect 표준 규격 인증 서비스**를 통해 개발자는 다음을 비롯한 여러 가지 ID 유형을 인증할 수 있습니다.
  - Azure AD를 통해 프로비저닝된 회사 또는 학교 계정
  - 개인 Microsoft 계정(예: Skype, Xbox 및 Outlook.com)
  - Azure AD B2C를 통한 소셜 또는 로컬 계정
- **오픈 소스 라이브러리**: MSAL(Microsoft 인증 라이브러리) 및 기타 표준 규격 라이브러리 지원
- **애플리케이션 관리 포털**: 다른 Azure 관리 기능과 함께 Azure Portal의 등록 및 구성 환경입니다.
- **애플리케이션 구성 API 및 PowerShell**: DevOps 작업을 자동화할 수 있도록 Microsoft Graph API 및 PowerShell을 통해 애플리케이션을 프로그래밍 방식으로 구성합니다.
- **개발자 콘텐츠**: 빠른 시작, 자습서, 방법 가이드 및 코드 샘플을 포함한 기술 설명서입니다.

Microsoft ID 플랫폼은 개발자를 위해 ID 및 보안 공간의 최신 혁신(예: 암호 없는 인증, 스텝업 인증 및 조건부 액세스)에 통합됩니다. 이러한 기능을 직접 구현할 필요가 없습니다. Microsoft ID 플랫폼에 통합된 애플리케이션은 이러한 기술 혁신의 이점을 기본적으로 활용합니다.

Microsoft ID 플랫폼을 사용하면 코드를 한 번에 작성하여 사용자를 연결할 수 있습니다. 앱을 한 번에 작성하여 여러 플랫폼에서 작동하거나, 클라이언트는 물론 리소스 애플리케이션(API)으로 작동하는 앱을 빌드할 수 있습니다.

## <a name="getting-started"></a>시작

빌드하려는 [애플리케이션 시나리오](authentication-flows-app-scenarios.md)를 선택합니다. 이러한 각 시나리오 경로는 개요를 시작하고 실행하는 데 도움이 되는 빠른 시작에 대한 링크를 제공합니다.

- [SPA(단일 페이지 앱)](scenario-spa-overview.md)
- [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
- [Web API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)
- [보호된 웹 API](scenario-protected-web-api-overview.md)
- [웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md)
- [데스크톱 앱](scenario-desktop-overview.md)
- [디먼 앱](scenario-daemon-overview.md)
- [모바일 앱](scenario-mobile-overview.md)

Microsoft ID 플랫폼을 사용하여 앱에서 인증 및 권한 부여를 통합하는 경우 가장 일반적인 앱 시나리오와 해당 ID 구성 요소를 간략하게 설명하는 이 이미지를 참조할 수 있습니다. 이미지를 선택하여 전체 크기를 확인합니다.

[![Microsoft ID 플랫폼의 여러 애플리케이션 시나리오를 보여주는 Metro 맵](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>인증 개념 알아보기

이 권장 문서 세트에서 핵심 인증 및 Azure AD 개념을 Microsoft ID 플랫폼에 적용하는 방법에 대해 알아봅니다.

- [인증 기본 사항](./authentication-vs-authorization.md)
- [애플리케이션 및 서비스 주체](app-objects-and-service-principals.md)
- [대상 그룹](v2-supported-account-types.md)
- [권한 및 동의](v2-permissions-and-consent.md)
- [ID 토큰](id-tokens.md)
- [액세스 토큰](access-tokens.md)
- [인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>추가 ID 및 액세스 관리 옵션

[Azure AD B2C](../../active-directory-b2c/overview.md) - 사용자가 Facebook 또는 Google과 같은 소셜 계정을 사용하거나 이메일 주소 및 암호를 사용하여 로그인할 수 있는 고객 지향 애플리케이션을 빌드합니다.

[Azure AD B2B](../external-identities/what-is-b2b.md) - 외부 사용자를 "게스트" 사용자로 Azure AD에 초대하고, 인증을 위해 기존 자격 증명을 사용하는 동안 권한 부여에 대한 권한을 할당합니다.

> [!TIP]
> *Azure Active Directory 개발자 플랫폼(v1.0)* 설명서를 찾으시나요? [개발자용 Azure Active Directory(v1.0) 개요](../azuread-dev/v1-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 계정이 이미 있는 경우 Azure Active Directory 테넌트에 액세스할 수 있지만 대부분의 Microsoft ID 플랫폼 개발자는 "개발 테넌트"인 애플리케이션을 개발하는 동안 사용할 자체 Azure AD 테넌트가 필요합니다.

애플리케이션을 빌드하는 동안 사용할 사용자 고유의 테넌트를 만드는 방법을 알아봅니다.

[빠른 시작: Azure AD 테넌트 설정](quickstart-create-new-tenant.md)