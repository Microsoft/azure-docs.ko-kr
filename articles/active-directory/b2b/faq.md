---
title: B2B 공동 작업 Faq-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 협업에 대해 자주 묻는 질문의 대답을 얻습니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 11/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8be047caf8631cda4e48b7bf10987db616a9b86f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608611"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B 협업 FAQ

Azure Active Directory(Azure AD) 기업 간(B2B) 협업에 대한 이러한 질문과 대답(FAQ)은 새 항목을 포함하도록 정기적으로 업데이트됩니다.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>B2B 협업 게스트 사용자에게 더 직관적인 환경이 되도록 로그인 페이지를 사용자 지정할 수 있나요?
그렇습니다. [이 기능에 대한 블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)을 참조하세요. 조직의 로그인 페이지를 사용자 지정하는 방법에 대한 자세한 내용은 [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가](../fundamentals/customize-branding.md)를 참조하세요.

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B 협업 사용자가 SharePoint Online 및 OneDrive에 액세스할 수 있습니까?
예. 그러나 사용자 선택을 사용하여 SharePoint Online에서 기존 게스트 사용자를 검색하는 기능은 기본적으로 **꺼져** 있습니다. 기존 게스트 사용자를 검색하는 옵션을 설정하려면 **ShowPeoplePickerSuggestionsForGuestUsers**를 **켜기**로 설정합니다. 테넌트 수준 또는 사이트 컬렉션 수준에서 이 설정을 사용할 수 있습니다. Set-SPOTenant 및 Set-SPOSite cmdlet을 사용하여 이 설정을 변경할 수 있습니다. 구성원은 이러한 cmdlet을 사용하여 디렉터리에 있는 기존의 모든 게스트 사용자를 검색할 수 있습니다. 테넌트 범위에 대한 변경 내용은 이미 프로비전된 SharePoint Online 사이트에 영향을 주지 않습니다.

### <a name="is-the-csv-upload-feature-still-supported"></a>CSV 업로드 기능이 계속 지원되나요?
예. .csv 파일 업로드 기능을 사용하는 방법에 대한 자세한 내용은 [이 PowerShell 샘플](code-samples.md)을 참조하세요.

### <a name="how-can-i-customize-my-invitation-emails"></a>초대 전자 메일을 어떻게 사용자 지정할 수 있습니까?
[B2B 초대 API](customize-invitation-api.md)를 사용하여 거의 모든 초대자 프로세스를 사용자 지정할 수 있습니다.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>게스트 사용자가 Multi-Factor Authentication 방법을 재설정할 수 있나요?
예. 게스트 사용자는 일반 사용자와 동일한 방식으로 Multi-Factor Authentication 방법을 재설정할 수 있습니다.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>어떤 조직이 Multi-Factor Authentication 라이선스를 담당하나요?
초대하는 조직은 Multi-Factor Authentication을 수행합니다. 초대하는 조직은 Multi-Factor Authentication을 사용하는 자신의 B2B 사용자에게 라이선스를 충분히 제공해야 합니다.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>파트너 조직이 이미 Multi-Factor Authentication을 설정한 경우에 어떻게 하나요? 해당 Multi-Factor Authentication을 신뢰하고 자체적인 Multi-Factor Authentication을 사용하지 않나요?
이 기능은 현재 지원 되지 않습니다. 조직 리소스에 대 한 액세스가 multi-factor authentication을 요구 하는 경우 파트너 조직은 (초대) 조직에서 multi-factor authentication을 등록 해야 합니다.

### <a name="how-can-i-use-delayed-invitations"></a>지연된 초대는 어떻게 사용할 수 있나요?
어떤 조직에서는 B2B 협업 사용자를 추가하고 필요한 경우 애플리케이션에 프로비전한 다음, 초대를 보내려고 합니다. B2B 협업 초대 API를 사용하여 온보딩 워크플로를 사용자 지정할 수 있습니다.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>게스트 사용자를 Exchange 전역 주소 목록에 표시할 수 있나요?
예. 게스트 개체는 기본적으로 조직의 GAL (전체 주소 목록)에 표시 되지 않지만 Azure Active Directory PowerShell을 사용 하 여 표시 되도록 할 수 있습니다. [전체 주소 목록에 게스트 개체를 표시할 수 있나요?를 참조 하세요](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list) .

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>게스트 사용자를 제한된 관리자로 지정할 수 있나요?
당연히 그렇습니다. 자세한 내용은 [역할에 게스트 사용자 추가](add-guest-to-role.md)를 참조하세요.

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B 협업을 사용하면 B2B 사용자가 Azure Portal에 액세스할 수 있나요?
사용자에 게 제한 된 관리자 역할이 할당 되지 않으면 B2B 공동 작업 사용자가 Azure Portal에 액세스할 필요가 없습니다. 그러나 제한 된 관리자의 역할이 할당 된 B2B 공동 작업 사용자는 포털에 액세스할 수 있습니다. 또한 이러한 관리자 역할 중 하나가 할당되지 않은 게스트 사용자가 포털에 액세스하는 경우 사용자는 특정 부분의 환경에 액세스할 수 있습니다. 게스트 사용자 역할에는 디렉터리에 대한 일부 사용 권한이 있습니다.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>게스트 사용자를 위해 Azure Portal에 대한 액세스를 차단할 수 있습니까?

예. 모든 게스트 및 외부 사용자가 Azure Portal에 액세스 하지 못하도록 차단 하는 조건부 액세스 정책을 만들 수 있습니다. 이 정책을 구성할 경우 실수로 구성원과 관리자에 대한 액세스를 차단하지 않도록 주의하세요.

1. [Azure Portal](https://portal.azure.com/)에 보안 관리자 또는 조건부 액세스 관리자 권한으로 로그인합니다.
2. Azure Portal에서 **Azure Active Directory**를 선택합니다. 
3. **관리**에서 **보안**을 선택 합니다.
4. **보호**아래에서 **조건부 액세스**를 선택 합니다. **새 정책**을 선택합니다.
5. **새로 만들기** 페이지의 **이름** 텍스트 상자에 정책의 이름을 입력 합니다 (예: "게스트가 포털에 액세스 하지 못하도록 차단").
6. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
7. **포함** 탭에서 **사용자 및 그룹 선택**을 선택 하 고 **모든 게스트 및 외부 사용자 (미리 보기)** 를 선택 합니다.
9. **완료** 를 선택합니다.
10. **새로 만들기** 페이지의 **할당** 섹션에서 **클라우드 앱 또는 작업**을 선택 합니다.
11. **클라우드 앱 또는 작업** 페이지에서 **앱 선택**을 선택한 다음 **선택**을 선택 합니다.
12. **선택** 페이지에서 **Microsoft Azure 관리**를 선택한 후 **선택**을 선택합니다.
13. **클라우드 앱 또는 작업** 페이지에서 **완료**를 선택 합니다.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B 협업에서는 Multi-Factor Authentication 및 소비자 이메일 계정을 지원하나요?
예. Multi-Factor Authentication 및 소비자 이메일 계정은 둘 다 Azure AD B2B 협업에 지원됩니다.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B 협업 사용자를 위한 암호 재설정을 지원하나요?
Azure AD 테넌트가 사용자의 홈 디렉터리이면 Azure portal에서 [사용자의 암호를 다시 설정](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal)할 수 있습니다. 하지만 다른 Azure AD 디렉터리 또는 외부 ID 공급자가 관리하는 계정으로 로그인한 게스트 사용자에 대한 암호는 직접 재설정할 수는 없습니다. 게스트 사용자 또는 사용자의 홈 디렉터리에 있는 관리자만 암호를 재설정할 수 있습니다. 다음은 게스트 사용자에 대해 암호 재설정이 작동하는 방식에 대한 몇 가지 예입니다.
 
* Microsoft 계정(예: guestuser@live.com)으로 로그인한 게스트 사용자는 Microsoft 계정 SSPR(셀프 서비스 암호 재설정)을 사용하여 사용자 고유의 암호를 재설정할 수 있습니다. [Microsoft 계정 암호를 재설정하는 방법](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)을 참조하세요.
* Google 계정 또는 다른 외부 ID 공급자로 로그인한 게스트 사용자는 ID 공급자의 SSPR 메서드를 사용하여 사용자 고유의 암호를 재설정할 수 있습니다. 예를 들어 Google 계정 guestuser@gmail.com을 사용하는 게스트 사용자는 [암호 변경 또는 재설정](https://support.google.com/accounts/answer/41078)의 지침에 따라 자신의 암호를 재설정할 수 있습니다.
* ID 테넌트가 JIT(Just-In-Time) 또는 “바이럴” 테넌트(별개의 관리되지 않는 Azure 테넌트를 의미)인 경우 게스트 사용자만 암호를 재설정할 수 있습니다. 직원이 회사 이메일 주소를 사용하여 서비스에 가입할 때 생성된 [바이럴 테넌트 관리를 인계](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)받는 조직도 있습니다. 조직이 바이럴 테넌트를 인계받고 나면 해당 조직의 관리자만이 사용자 암호를 재설정하거나 SSPR을 사용하도록 설정할 수 있습니다. 필요한 경우 초대 조직 관리자는 디렉터리에서 게스트 사용자 계정을 제거하고 초대를 다시 보낼 수 있습니다.
* 게스트 사용자의 홈 디렉터리가 Azure AD 테넌트인 경우 사용자의 암호를 재설정할 수 있습니다. 예를 들어 온-프레미스 Active Directory에서 사용자를 만들거나 동기화하고 UserType을 Guest로 설정할 수 있습니다. 이 사용자는 디렉터리에 속해 있기 때문에 Azure Portal에서 암호를 재설정할 수 있습니다.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365에서 Azure AD B2B 협업에 대한 온라인 지원을 제공합니까?
예, Dynamics 365(온라인)는 Azure AD B2B 협업을 지원합니다. 자세한 내용은 Dynamics 365 문서 [Azure AD B2B 협업에 사용자 초대](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)를 참조하세요.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>새로 만든 B2B 협업 사용자의 초기 암호 수명은 어떻게 됩니까?
Azure AD에는 모든 Azure AD 클라우드 사용자 계정에 동등하게 적용되는 고정된 문자 집합, 암호 강도 및 계정 잠금 요구 사항이 있습니다. 클라우드 사용자 계정은 다른 ID 공급자와 페더레이션되지 않은 계정입니다. 예를 들면 다음과 같습니다. 
* Microsoft 계정
* Facebook
* Active Directory Federation Services
* 다른 클라우드 테넌트(B2B 협업용)

페더레이션 계정의 경우 암호 정책은 온-프레미스 테넌트 및 사용자의 Microsoft 계정 설정에서 적용되는 정책에 따라 다릅니다.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>조직에서는 해당 애플리케이션에서 테넌트 사용자와 게스트 사용자에게 다른 환경을 제공할 수 있습니다. 이에 대한 표준 지침이 있습니까? ID 공급자 클레임의 현재 상태가 사용할 모델이 맞나요?
게스트 사용자는 인증하는 데 ID 공급자를 사용할 수 있습니다. 자세한 내용은 [B2B 협업 사용자의 속성](user-properties.md)을 참조하세요. **UserType** 속성을 사용하여 사용자 환경을 결정합니다. **UserType** 클레임은 현재 토큰에 포함되지 않습니다. 애플리케이션은 Graph API를 사용하여 사용자에게 디렉터리를 쿼리하고 UserType을 가져와야 합니다.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>솔루션을 공유하고 아이디어를 제출하는 B2B 협업 커뮤니티를 어디에서 찾을 수 있나요?
B2B 협업을 개선하기 위해 사용자의 의견을 지속적으로 수렴하고 있습니다. Azure AD B2B 협업에 대한 사용자 시나리오, 모범 사례 및 원하는 기능을 공유해 주세요. [Microsoft 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)에서 토론에 참여합니다.
 
[B2B 협업 아이디어](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)에서 아이디어를 제출하고 향후 기능에 대해 투표해 주세요.

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>사용자가 곧 "준비"될 수 있도록 자동으로 상환되는 초대장을 보낼 수 있나요? 아니면 사용자가 항상 상환 URL을 클릭해야 하나요?
UI, PowerShell 스크립트 또는 API를 사용하여 파트너 조직의 다른 사용자를 초대할 수 있습니다. 그런 다음, 게스트 사용자에게 공유 앱에 대한 직접 링크를 보낼 수 있습니다. 대부분의 경우에는 이메일 초대를 열고 상환 URL을 클릭할 필요가 없습니다. [Azure Active Directory B2B 협업 초대 상환](redemption-experience.md)을 참조하세요.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>초대된 파트너가 페더레이션을 사용하여 자체 온-프레미스 인증을 추가하면 B2B 협업이 어떻게 진행되나요?
파트너에게 온-프레미스 인증 인프라로 페더레이션된 Azure AD 테넌트가 있는 경우 온-프레미스 SSO(Single Sign-On)가 자동으로 이루어집니다. 파트너에게 Azure AD 테넌트가 없는 경우 새로운 사용자에게 Azure AD 계정이 생성됩니다. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>B2C에서는 이러한 종류의 계정을 사용했는데 Azure AD B2B에서는 gmail.com 및 outlook.com 전자 메일 주소를 허용하지 않나요?
지원되는 ID라는 측면에서 B2B 및 B2C(business-to-consumer) 협업 간의 차이점을 제거하고 있습니다. 사용되는 ID로 B2B를 사용할지 아니면 B2C를 사용할지 결정하는 것은 좋은 기준이 아닙니다. 협업 옵션을 선택하는 방법에 대한 정보는 [Azure Active Directory에서 B2B 협업과 B2C 비교](compare-with-b2c.md)를 참조하세요.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>어떤 애플리케이션 및 서비스에서 Azure B2B 게스트 사용자를 지원하나요?
모든 Azure AD 통합 응용 프로그램은 Azure B2B 게스트 사용자를 지원할 수 있지만, 게스트 사용자를 인증 하려면 테넌트로 설정 된 엔드포인트을 사용 해야 합니다. 게스트 사용자가 앱을 인증할 때 발행되는 SAML 토큰에서 [클레임을 사용자 지정](claims-mapping.md)해야 할 수도 있습니다. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>파트너가 Multi-Factor Authentication을 설치하지 않은 경우 B2B 게스트 사용자에 대해 Multi-Factor Authentication을 강제할 수 있나요?
예. 자세한 내용은 [B2B 공동 작업 사용자에 대 한 조건부 액세스](conditional-access.md)를 참조 하세요.

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>SharePoint에서 외부 사용자에 대한 "허용" 또는 "거부" 목록을 정의할 수 있습니다. Azure에서 수행할 수 있나요?
예. Azure AD B2B 협업은 허용 목록 및 거부 목록을 지원합니다. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Azure AD B2B를 사용해야 하는 라이선스는 무엇인가요?
조직이 Azure AD B2B를 사용해야 하는 라이선스에 대한 정보는 [Azure Active Directory B2B 협업 라이선스 지침](licensing-guidance.md)을 참조하세요.

### <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)

