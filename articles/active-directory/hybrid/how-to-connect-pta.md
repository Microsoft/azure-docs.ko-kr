---
title: 'Azure AD Connect: 통과 인증 | Microsoft 문서'
description: 이 문서에서는 Azure AD(Azure Active Directory) 통과 인증 및 이 통과 인증을 통해 사용자의 암호를 온-프레미스 Active Directory에 대해 유효성 검사하여 Azure AD 로그인을 허용하는 방법을 설명합니다.
services: active-directory
keywords: Azure AD Connect 통과 인증의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185512"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증으로 사용자 로그인

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Azure Active Directory 통과 인증이란?

Azure AD(Azure Active Directory) 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 애플리케이션 둘 다에서 동일한 암호로 로그인할 수 있습니다. 이 기능은 하나 적은 기억할 암호로 사용자에게 더 나은 환경을 제공하고 사용자는 로그인하는 방법을 잊을 가능성이 적기 때문에 IT 기술 지원팀 비용을 줄입니다. 사용자가 Azure AD를 사용하여 로그인할 때 이 기능은 온-프레미스 Active Directory에 대해 직접 사용자 암호의 유효성을 검사합니다.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

이 기능은 조직에 클라우드 인증과 동일한 혜택을 제공하는 [Azure AD 암호 해시 동기화](how-to-connect-password-hash-synchronization.md)에 대한 대안입니다. 그러나 해당 온-프레미스 Active Directory 보안 및 암호 정책을 적용하려는 특정 조직은 대신 통과 인증을 사용하도록 선택할 수 있습니다. 다양한 Azure AD 로그인 방법을 비교한 결과와 조직에 적합한 로그인 방법을 선택하는 방법을 보려면 [이 가이드](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)를 검토하세요.

![Azure AD 통과 인증](./media/how-to-connect-pta/pta1.png)

통과 인증을 [Seamless Single Sign-On](how-to-connect-sso.md) 기능에 결합할 수 있습니다. 이러한 방식으로 사용자가 회사 네트워크 내부에서 회사 컴퓨터의 애플리케이션에 액세스할 때 로그인하기 위해 암호를 입력할 필요가 없습니다.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증 사용의 주요 혜택

- *멋진 사용자 환경*
  - 사용자는 온-프레미스와 클라우드 기반 애플리케이션에 로그인하는 데 동일한 암호를 사용합니다.
  - 사용자는 암호 관련 문제를 해결하기 위해 IT 기술 지원팀과 소통하는 데 더 적은 시간을 보냅니다.
  - 사용자는 클라우드에서 [셀프 서비스 암호 관리](../authentication/active-directory-passwords-overview.md) 작업을 완료할 수 있습니다.
- *손쉬운 배포 및 관리*
  - 복잡한 온-프레미스 배포 또는 네트워크 구성에 대한 필요가 없습니다.
  - 온-프레미스에 설치되는 간단한 에이전트만 필요합니다.
  - 관리 오버헤드 없음 에이전트는 향상된 기능 및 버그 수정을 자동으로 받습니다.
- *보안*
  - 온-프레미스 암호가 어떤 형태로든 클라우드에 저장되지 않습니다.
  - MFA(Multi-Factor Authentication)를 포함하는 [Azure AD 조건부 액세스 정책](../active-directory-conditional-access-azure-portal.md)을 사용하여 원활하게 작동하고, [레거시 인증을 차단](../conditional-access/concept-conditional-access-conditions.md)하고, [무차별 암호 대입 공격을 필터링](../authentication/howto-password-smart-lockout.md)하여 사용자 계정을 보호합니다.
  - 에이전트는 네트워크 내에서만 아웃바운드 연결을 만듭니다. 따라서 DMZ라고도 하는 경계 네트워크에 에이전트를 설치할 필요가 없습니다.
  - 에이전트와 Azure AD 간의 통신은 인증서 기반 인증을 사용하여 보호됩니다. 이러한 인증서는 Azure AD에서 몇 개월마다 자동으로 갱신됩니다.
- *고가용성*
  - 로그인 요청의 고가용성을 제공하기 위해 여러 온-프레미스 서버에 에이전트를 추가로 설치할 수 있습니다.

## <a name="feature-highlights"></a>주요 기능

- 모든 웹 브라우저 기반 애플리케이션 및 [최신 인증](https://aka.ms/modernauthga)을 사용하는 Microsoft Office 클라이언트 애플리케이션에 사용자 로그인을 지원합니다.
- 로그인 사용자 이름은 온-프레미스 기본 사용자 이름(`userPrincipalName`) 또는 Azure AD Connect에 구성된 다른 특성(`Alternate ID`라고 함) 중 하나일 수 있습니다.
- 이 기능은 Multi-Factor Authentication (MFA)와 같은 [조건부 액세스](../active-directory-conditional-access-azure-portal.md) 기능을 사용 하 여 원활 하 게 작동 하 여 사용자의 보안을 유지 합니다.
- 온-프레미스 Active Directory에 대한 암호 쓰기 저장 및 일반적으로 사용되는 암호 금지에 의한 암호 보호를 포함하여 클라우드 기반 [셀프 서비스 암호 관리](../authentication/active-directory-passwords-overview.md)와 통합되었습니다.
- AD 포리스트 간에 포리스트 트러스트가 있고 이름 접미사 라우팅이 제대로 구성된 경우 다중 포리스트 환경이 지원됩니다.
- 무료 기능이며 이 기능을 사용하는 데는 Azure AD 유료 버전이 필요하지 않습니다.
- [Azure AD Connect](whatis-hybrid-identity.md)를 통해 사용하도록 설정할 수 있습니다.
- 암호 유효성 검사 요청을 수신하고 이에 응답하는 간단한 온-프레미스 에이전트를 사용합니다.
- 여러 에이전트를 설치하면 로그인 요청의 고가용성을 제공합니다.
- 이렇게 하면 클라우드의 무차별 암호 대입 공격으로부터 온-프레미스 계정이 [보호](../authentication/howto-password-smart-lockout.md)됩니다.

## <a name="next-steps"></a>다음 단계

- [빠른](how-to-connect-pta-quick-start.md) 시작-Azure AD 통과 인증을 시작 및 실행 합니다.
- [AD FS에서 통과 인증으로 마이그레이션](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md) - 테넌트에서 사용자 계정을 보호하도록 스마트 잠금 기능을 구성합니다.
- [현재 제한 사항](how-to-connect-pta-current-limitations.md) - 지원되는 시나리오와 지원되지 않는 시나리오에 대해 알아봅니다.
- [기술 심층 분석](how-to-connect-pta-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [FAQ(질문과 대답)](how-to-connect-pta-faq.md) - 질문과 대답을 다루고 있습니다.
- [문제 해결](tshoot-connect-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [보안 심층 분석](how-to-connect-pta-security-deep-dive.md) - 해당 기능에 대한 자세한 추가 기술 정보입니다.
- [Azure AD Seamless SSO](how-to-connect-sso.md) - 보완적인 Azure AD Seamless SSO 기능을 자세히 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
