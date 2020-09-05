---
title: Azure Active Directory를 사용하기 위해 애플리케이션 등록 | Microsoft Docs
description: IT 전문가를 위해 작성된 이 문서는 Active Directory와 Azure 애플리케이션 통합에 대한 지침을 제공합니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d662689d44bcdbd468c882de0679fd60c9ab4f4
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640449"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Azure Active Directory용 기간 업무 앱 개발
이 가이드는 Azure AD(Active Directory)에 대한 LoB(기간 업무) 애플리케이션 개발의 개요를 제공하며, Active Directory/Office 365 글로벌 관리자용으로 작성되었습니다.

## <a name="overview"></a>개요
Azure AD와 통합된 애플리케이션을 구축하면 조직의 사용자에게 Office 365를 사용하여 Single Sign-On을 제공합니다. Azure AD에 애플리케이션이 있다면 애플리케이션에 대한 인증 정책을 통한 제어를 제공합니다. 조건부 액세스에 대 한 자세한 내용과 MFA (multi-factor authentication)를 사용 하 여 앱을 보호 하는 방법에 대 한 자세한 내용은 [액세스 규칙 구성](../conditional-access/app-based-mfa.md)을 참조 하세요.

Azure Active Directory를 사용하기 위해 애플리케이션을 등록해야 합니다. 애플리케이션을 등록하면 개발자가 Azure AD를 사용하여 사용자를 인증하고 이메일, 일정, 문서 등과 같은 사용자 리소스에 대한 액세스를 요청할 수 있습니다.

디렉터리(게스트 아님)의 멤버는 애플리케이션을 등록할 수 있습니다.( *애플리케이션 개체 만들기*라고 함)

애플리케이션을 등록하면 사용자가 다음을 수행할 수 있습니다.

* Azure AD가 인식하는 애플리케이션에 ID 가져오기
* 애플리케이션에서 사용할 수 있는 하나 이상의 암호/키를 가져와서 AD에 자신을 인증
* Azure 포털에서 사용자 지정 이름, 로고 등으로 애플리케이션 브랜딩
* 다음을 포함하여 앱에 대한 Azure AD 권한 부여 기능 적용

  * 역할 기반 Access Control(RBAC)
  * OAuth 권한 부여 서버인 Azure Active Directory(애플리케이션에서 노출된 API 보호)
* 애플리케이션에 예상 대로 작동하는 데 필요한 다음을 포함하는 사용 권한을 선언합니다.

     - 앱 사용 권한(전역 관리자만 해당) 예: 다른 Azure AD 애플리케이션에서 역할 멤버 자격 또는 Azure 리소스, 리소스 그룹 또는 구독에 상대적인 역할 멤버 자격
     - 위임된 권한(모든 사용자). 예: Azure AD, 로그인 및 프로필 읽기

> [!NOTE]
> 기본적으로 모든 멤버는 애플리케이션을 등록할 수 있습니다. 특정 멤버에 애플리케이션 등록에 대한 사용 권한을 제한하는 방법을 알아보려면 [Azure AD에 애플리케이션을 추가하는 방법](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)을 참조하세요.
>
>

다음은 개발자가 자신의 애플리케이션을 생산할 준비를 돕기 위해 글로벌 관리자가 수행해야 하는 사항입니다.

* 액세스 규칙 구성(액세스 정책/MFA)
* 앱을 구성하여 사용자 할당 요구 및 사용자 할당
* 기본 사용자 동의 환경 무시

## <a name="configure-access-rules"></a>액세스 규칙 구성
SaaS 앱에 애플리케이션별 액세스 규칙을 구성합니다. 예를 들어 MFA를 요구하거나 신뢰할 수 있는 네트워크의 사용자에 대한 액세스만 허용할 수 있습니다. 이에 대한 세부 정보는 [액세스 규칙 구성](../conditional-access/app-based-mfa.md)문서에서 사용할 수 있습니다.

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>앱을 구성하여 사용자 할당 요구 및 사용자 할당
기본적으로 사용자는 할당되지 않아도 애플리케이션에 액세스할 수 있습니다. 그러나 응용 프로그램이 역할을 노출 하거나 사용자의 내 앱에 응용 프로그램을 표시 하려는 경우에는 사용자 할당이 필요 합니다.

Azure AD Premium 또는 Enterprise Mobility Suite(EMS) 구독자인 경우 그룹을 사용하는 것이 좋습니다. 애플리케이션에 그룹을 할당하면 그룹의 소유자에게 지속적인 액세스 관리를 위임할 수 있습니다. 그룹을 만들거나 조직에서 책임 파티를 요청하여 그룹 관리 기능을 사용하여 그룹을 만들 수 있습니다.

[애플리케이션에 사용자 및 그룹 할당](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>사용자 동의 무시
기본적으로 각 사용자는 로그인하기 위해 동의 환경을 거칩니다. 사용자에게 애플리케이션에 대한 사용 권한을 부여하도록 요청하는 동의 환경은 그런 결정을 내리는 데 익숙하지 않은 사용자에게 혼란을 줄 수 있습니다.

신뢰할 수 있는 애플리케이션의 경우 조직을 대신하여 애플리케이션에 동의하여 사용자 환경을 간소화할 수 있습니다.

Azure에서 동의 및 동의 환경에 대한 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)을 참조하세요.

## <a name="related-articles"></a>관련 문서
* [Azure AD 애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션에 대한 보안 원격 액세스 사용](application-proxy.md)
* [Azure AD를 사용하는 앱에 대한 액세스 관리](what-is-access-management.md)

