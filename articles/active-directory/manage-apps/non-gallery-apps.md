---
title: 앱 갤러리에 나열 되지 않은 응용 프로그램에 Azure AD 사용
description: Azure AD 갤러리에 나열 되지 않은 앱을 통합 하는 방법에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 262c3a7c8a668ef25d092b5d523743e0ece89cc3
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641146"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>앱 갤러리에 나열 되지 않은 응용 프로그램에 Azure AD 사용

[앱 추가](add-application-portal.md) 빠른 시작에서 Azure AD 테 넌 트에 앱을 추가 하는 방법을 알아봅니다.

[Azure AD 애플리케이션 갤러리](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)에서 선택하는 것 외에도 **비갤러리 애플리케이션**를 추가하는 옵션이 있습니다. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Azure AD 갤러리에 나열 되지 않은 앱에 대 한 기능

조직에 이미 있는 애플리케이션 또는 Azure AD 애플리케이션 갤러리의 일부가 아닌 공급업체의 타사 애플리케이션을 추가할 수 있습니다. [사용권 계약](https://azure.microsoft.com/pricing/details/active-directory/)에 따라 다음과 같은 기능을 사용할 수 있습니다.

- [SAML(Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID 공급자를 지원하는 애플리케이션의 셀프 서비스 통합(SP 시작 또는 IdP 시작)
- [암호 기반 SSO](sso-options.md#password-based-sso)를 사용하여 HTML 기반 로그인 페이지가 있는 웹 애플리케이션의 셀프 서비스 통합
- [사용자 프로비저닝에 SCIM(System for Cross-Domain Identity Management) 프로토콜](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 사용하는 애플리케이션의 셀프 서비스 연결
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [내 앱](sso-options.md#linked-sign-on) 의 응용 프로그램에 링크를 추가 하는 기능

사용자 지정 앱을 Azure AD와 통합 하는 방법에 대 한 개발자 가이드를 찾고 있는 경우 [AZURE ad에 대 한 인증 시나리오](../develop/authentication-scenarios.md)를 참조 하세요. [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)와 같은 최신 프로토콜을 사용하여 사용자를 인증하는 앱을 개발할 때 Azure Portal에서 [앱 등록](../develop/quickstart-register-app.md) 경험을 사용하여 Microsoft ID 플랫폼에 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [앱 관리에 대 한 빠른 시작 시리즈](view-applications-portal.md)

