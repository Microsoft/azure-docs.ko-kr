---
title: 비 갤러리 응용 프로그램 추가-Microsoft identity platform | Microsoft Docs
description: 비 갤러리 응용 프로그램을 Azure AD 테넌트에 추가 합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063614"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>나열 되지 않은 (비 갤러리) 응용 프로그램을 Azure AD 조직에 추가

[AZURE AD 응용 프로그램 갤러리](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)의 선택 사항 외에도 **비 갤러리 응용 프로그램**을 추가 하는 옵션이 있습니다. 조직에 이미 있는 응용 프로그램이 나 아직 Azure AD 갤러리에 속하지 않은 공급 업체의 타사 응용 프로그램을 추가할 수 있습니다. [사용권 계약](https://azure.microsoft.com/pricing/details/active-directory/)에 따라 다음과 같은 기능을 사용할 수 있습니다.

- [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) id 공급자를 지 원하는 모든 응용 프로그램의 셀프 서비스 통합 (SP 시작 또는 IdP 시작)
- [암호 기반 SSO](what-is-single-sign-on.md#password-based-sso)
- [사용자 프로 비전을 위해 SCIM (도메인 간 Id 관리) 프로토콜에 시스템](../app-provisioning/use-scim-to-provision-users-and-groups.md) 을 사용 하는 응용 프로그램의 셀프 서비스 연결
- [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](what-is-single-sign-on.md#linked-sign-on)에서 애플리케이션에 대한 링크를 추가하는 기능

이 문서에서는 코드를 작성 하지 않고 Azure Portal의 **엔터프라이즈 응용** 프로그램에 비 갤러리 응용 프로그램을 추가 하는 방법을 설명 합니다. 사용자 지정 앱을 Azure AD와 통합 하는 방법에 대 한 개발자 가이드를 찾고 있는 경우 [AZURE ad에 대 한 인증 시나리오](../develop/authentication-scenarios.md)를 참조 하세요. [Openid connect Connect/OAuth](../develop/active-directory-v2-protocols.md) 와 같은 최신 프로토콜을 사용 하 여 사용자를 인증 하는 앱을 개발 하는 경우 Azure Portal에서 [앱 등록](../develop/quickstart-register-app.md) 환경을 사용 하 여 Microsoft id 플랫폼에 등록할 수 있습니다.

## <a name="add-a-non-gallery-application"></a>비갤러리 애플리케이션 추가

1. Microsoft id 플랫폼 관리자 계정을 사용 하 여 [Azure Active Directory 포털](https://aad.portal.azure.com/) 에 로그인 합니다.

2. **엔터프라이즈 응용 프로그램** > **새 응용 프로그램**을 선택 합니다.

3. (선택 사항 이지만 권장 됨) **AZURE AD 갤러리** 검색 검색 상자에 응용 프로그램의 표시 이름을 입력 합니다. 

4. **사용자 고유의 응용 프로그램 만들기**를 선택 합니다. **사용자 고유의 응용 프로그램 만들기** 페이지가 나타납니다.

   ![응용 프로그램 추가](media/add-non-gallery-app/create-your-own-application.png)

5. 새 응용 프로그램에 대 한 표시 이름 입력을 시작 합니다. 비슷한 이름의 갤러리 응용 프로그램이 있는 경우 검색 결과 목록에 표시 됩니다.

   > [!NOTE]
   > 가능 하면 항상 갤러리 버전의 응용 프로그램을 사용 하는 것이 좋습니다. 추가 하려는 응용 프로그램이 검색 결과에 표시 되 면 응용 프로그램을 선택 하 고이 절차의 나머지 부분을 건너뜁니다.

6. **응용 프로그램으로 어떤 작업을 수행 하 시겠습니까?** 에서 **갤러리에서 찾을 수 없는 다른 응용 프로그램 통합**을 선택 합니다. 이 옵션은 일반적으로 SAML 및 WS 공급 응용 프로그램에 사용 됩니다.

   > [!NOTE]
   > 다른 두 옵션은 다음 시나리오에서 사용 됩니다.
   >* **온-프레미스 응용 프로그램에 대 한 보안 원격 액세스에 대 한 응용 프로그램 프록시 구성** Azure AD 응용 프로그램 프록시 및 커넥터에 대 한 구성 페이지를 엽니다.
   >* **AZURE AD와 통합 하기 위해 작업 중인 응용 프로그램을 등록** 하면 **앱 등록** 페이지가 열립니다. 이 옵션은 일반적으로 Openid connect Connect 응용 프로그램에 사용 됩니다.

7. **만들기**를 선택합니다. 애플리케이션 **개요** 페이지가 열립니다.

## <a name="configure-user-sign-in-properties"></a>사용자 로그인 속성 구성

1. **속성**을 선택하여 편집할 속성 창을 엽니다.

    ![속성 창 블레이드](media/add-non-gallery-app/edit-properties.png)

2. 애플리케이션에 할당되거나 할당되지 않은 사용자가 애플리케이션에 로그인할 수 있는 방법과 사용자가 액세스 패널에서 애플리케이션을 볼 수 있는지 여부를 결정하려면 다음 옵션을 설정합니다.

    - **사용자가 로그인할 수 있습니까**는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당 필요**는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시**는 앱에 할당된 사용자가 액세스 패널 및 O365 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다.

      **할당된** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당된 사용자 경험 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당된 사용자가 로그인할 수 있습니까? | 할당된 사용자가 애플리케이션을 볼 수 있습니까?* |
       | 예 | 예 | 예 | 예 | 예  |
       | 예 | 예 | no  | 예 | no   |
       | 예 | no  | 예 | 예 | 예  |
       | 예 | no  | no  | 예 | no   |
       | no  | 예 | 예 | no  | no   |
       | no  | 예 | no  | no  | no   |
       | no  | no  | 예 | no  | no   |
       | no  | no  | no  | no  | no   |

      **할당되지 않은** 사용자에 대한 동작:

       | 애플리케이션 속성 설정 | | | 할당되지 않은 사용자 환경 | |
       |---|---|---|---|---|
       | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 할당되지 않은 사용자가 로그인할 수 있습니까? | 할당되지 않은 사용자가 애플리케이션을 볼 수 있습니다? * |
       | 예 | 예 | 예 | no  | no   |
       | 예 | 예 | no  | no  | no   |
       | 예 | no  | 예 | 예 | no   |
       | 예 | no  | no  | 예 | no   |
       | no  | 예 | 예 | no  | no   |
       | no  | 예 | no  | no  | no   |
       | no  | no  | 예 | no  | no   |
       | no  | no  | no  | no  | no   |

     *사용자가 액세스 패널 및 Office 365 앱 시작 관리자에서 애플리케이션을 볼 수 있습니까?

3. 사용자 지정 로고를 사용하려면 215 x 215 픽셀 로고를 만들고, PNG 형식으로 저장합니다. 해당 로고를 찾아서 업로드합니다.

    ![로고 변경](media/add-non-gallery-app/change-logo.png)

4. 완료되면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 조직에 애플리케이션을 추가했으므로, 이제 사용할 [Single Sign-On 방법을 선택](what-is-single-sign-on.md#choosing-a-single-sign-on-method)하고 아래에서 적절한 문서를 참조합니다.

- [SAML 기반 Single Sign-On 구성](configure-single-sign-on-non-gallery-applications.md)
- [암호 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)
- [연결된 로그온 구성](configure-linked-sign-on.md)
