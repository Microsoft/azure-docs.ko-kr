---
title: B2B 협업용 SaaS 앱 구성 - Azure AD
description: Azure Active Directory B2B 협업용 SaaS 앱을 구성하고 사용 가능한 추가 리소스를 표시하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90705513"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B 협업용 SaaS 앱 구성

Azure AD(Azure Active Directory) B2B 협업은 Azure AD와 통합되는 대부분의 앱에서 작동합니다. 이 섹션에서는 Azure AD B2B와 함께 사용되는 일부 인기 있는 SaaS 앱을 구성하는 지침을 안내합니다.

앱별 지침을 살펴보기 전에 다음과 같이 경험에 근거한 몇 가지 규칙이 있습니다.

* 대부분의 앱에서 사용자 설치는 수동으로 진행됩니다. 즉, 앱에서도 사용자를 수동으로 만들어야 합니다.

* Dropbox처럼 자동 설치를 지원하는 앱의 경우 앱에서 별도의 초대가 만들어집니다. 사용자는 각 초대를 수락해야 합니다.

* 게스트 사용자의 잘못된(mangled) UPD(사용자 프로필 디스크) 관련 문제를 완화할 수 있도록 사용자 특성에서 항상 **사용자 식별자** 를 **user.mail** 로 설정해야 합니다.


## <a name="dropbox-business"></a>Dropbox Business

사용자가 조직 계정을 사용하여 로그인할 수 있게 하려면 SAML(Security Assertion Markup Language) ID 공급자로 Azure AD를 사용하도록 수동으로 Dropbox Business를 구성해야 합니다. Dropbox Business가 그렇게 구성되지 않으면 프롬프트를 표시할 수 없거나 사용자가 Azure AD를 사용하여 로그인할 수 없게 됩니다.

1. Azure AD에 Dropbox Business를 추가하려면 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **추가** 를 클릭합니다.

   ![엔터프라이즈 애플리케이션 페이지의 "추가" 단추](media/configure-saas-apps/add-dropbox.png)

2. **애플리케이션 추가** 창의 검색 상자에 **dropbox** 를 입력한 다음, 결과 목록에서 **Dropbox for Business** 를 선택합니다.

   ![애플리케이션 추가 페이지에서 "dropbox" 검색](media/configure-saas-apps/add-app-dialog.png)

3. **Single Sign-On** 페이지의 왼쪽 창에서 **Single Sign-On** 을 선택한 다음 **사용자 식별자** 상자에 **user.mail** 을 입력합니다. (기본적으로 UPN으로 설정됩니다.)

   ![앱에 Single Sign-On 구성](media/configure-saas-apps/configure-app-sso.png)

4. Dropbox 구성에 사용할 인증서를 다운로드하려면 **DropBox 구성** 을 선택한 다음 목록에서 **SAML Single Sign-On 서비스 URL** 을 선택합니다.

   ![Dropbox 구성에 사용할 인증서 다운로드](media/configure-saas-apps/download-certificate.png)

5. **Single Sign-On** 페이지에서 로그온 URL을 사용하여 Dropbox에 로그인합니다.

   ![Dropbox 로그인 페이지를 보여주는 스크린샷](media/configure-saas-apps/sign-in-to-dropbox.png)

6. 메뉴에서 **관리 콘솔** 을 선택합니다.

   ![Dropbox 메뉴의 "관리 콘솔" 링크](media/configure-saas-apps/dropbox-menu.png)

7. **인증** 대화 상자에서 **추가** 를 선택하고, 인증서를 업로드한 다음 **로그인 URL** 상자에 SAML Single Sign-On URL을 입력합니다.

   ![축소된 인증 대화 상자의 "추가" 링크](media/configure-saas-apps/dropbox-auth-01.png)

   ![확장된 인증 대화 상자의 "로그인 URL"](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Azure Portal에서 자동 사용자 설치를 구성하려면 왼쪽 창에서 **프로비전** 을 선택하고, **프로비전 모드** 상자에서 **자동** 을 선택한 다음 **권한 부여** 를 선택합니다.

   ![Azure Portal에서 자동 사용자 프로비전 구성](media/configure-saas-apps/set-up-automatic-provisioning.png)

Dropbox 앱에서 게스트 또는 멤버 사용자가 설정되면 사용자가 Dropbox에서 별도의 초대를 받습니다. Dropbox Single Sign-On을 사용하려면 초대 받는 사람이 초대 안에 포함된 링크를 클릭하여 초대를 수락해야 합니다.

## <a name="box"></a>Box
SAML 프로토콜 기반의 페더레이션을 사용하여 사용자의 Azure AD 계정으로 Box 게스트 사용자를 인증하도록 사용자를 설정할 수 있습니다. 이 절차에서 Box.com에 메타데이터를 업로드합니다.

1. 엔터프라이즈 앱에서 Box 앱을 추가합니다.

2. 다음 순서로 Single Sign-On을 구성합니다.

   ![Single Sign-On 구성 설정을 보여주는 스크린샷](media/configure-saas-apps/configure-box-sso.png)

   a. **로그온 URL** 상자에서 Azure Portal의 Box에 대해 로그온 URL이 적합하게 설정되었는지 확인합니다. 이 URL은 Box.com 테넌트의 URL이며 *https://.box.com* 명명 규칙을 따라야 합니다.  
   **식별자** 는 이 앱에 적용되지 않지만 여전히 필수 필드로 표시됩니다.

   b. **사용자 식별자** 상자에 **user.mail**(게스트 계정의 SSO에 대한)을 입력합니다.

   다. **SAML 서명 인증서** 에서 **새 인증서 만들기** 를 클릭합니다.

   d. ID 공급자로 Azure AD를 사용하도록 Box.com 테넌트를 구성하려면 메타데이터 파일을 다운로드하여 로컬 드라이브에 저장합니다.

   e. 메타데이터 파일을 Box 지원 팀에 전달합니다. 그러면 지원 팀에서 대신 Single Sign-On을 구성할 것입니다.

3. Azure AD 자동 사용자 설치의 경우 왼쪽 창에서 **프로비전** 을 선택한 다음 **권한 부여** 를 선택합니다.

   ![Box에 연결하도록 Azure AD에 권한 부여](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Dropbox 초대 대상자와 마찬가지로 Box 초대 대상자는 Box 앱의 초대를 교환해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [동적 그룹 및 B2B 협업](use-dynamic-groups.md)
- [B2B 협업 사용자 클레임 매핑](claims-mapping.md)
- [Microsoft 365 외부 공유](o365-external-user.md)

