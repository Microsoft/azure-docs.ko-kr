---
title: 외부 ID 비교 - Azure Active Directory | Microsoft Docs
description: Azure AD 외부 ID를 사용하면 조직 외부 사용자가 자신의 ID를 사용하여 앱과 리소스에 액세스할 수 있습니다. Azure Active Directory B2B 협업 및 Azure AD B2C를 비롯한 외부 ID에 대한 솔루션을 비교합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910583"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Azure Active Directory의 외부 ID란?

Azure AD의 외부 ID를 사용하여 조직 외부 사용자가 앱과 리소스에 액세스하도록 허용하는 동시에 원하는 모든 ID를 사용하여 로그인하도록 할 수 있습니다. 파트너, 유통업체, 제공업체, 공급업체 및 기타 게스트 사용자는 "자신의 ID를 가져올 수 있습니다". Azure AD 또는 다른 IT 관리형 시스템의 일부이거나 Google 또는 Facebook과 같은 관리되지 않는 소셜 ID가 있는 경우에는 자체 자격 증명을 사용하여 로그인할 수 있습니다. ID 공급자가 외부 사용자의 ID를 관리하고, 사용자가 Azure AD를 통해 앱에 대한 액세스를 관리하여 리소스의 보호 상태를 유지합니다. 

## <a name="external-identities-scenarios"></a>외부 ID 시나리오

Azure AD 외부 ID는 조직과 사용자의 관계 및 개인이 앱과 리소스에 로그인하는 데 사용할 방법에 중점을 둘 수 있습니다. 이 프레임워크 내에서 Azure AD는 B2B(기업 간) 협업부터 고객과 소비자를 위한 앱 개발(기업과 소비자 간 또는 B2C)까지 다양한 시나리오를 지원합니다.

- **외부 사용자와 앱 공유(B2B 협업)** . 자신의 테넌트에 외부 사용자를 "게스트" 사용자로 초대하여 권한을 할당할 수 있고(권한 부여), 해당 사용자가 기존 자격 증명을 사용하도록 허용할 수 있습니다(인증). 사용자는 자신의 회사 계정, 학교 계정 또는 이메일 계정으로 간단한 초대 및 사용 프로세스를 통해 공유 리소스에 로그인합니다. 그리고 이제 셀프 서비스 가입 사용자 흐름(미리 보기)을 사용할 수 있으므로 공유하려는 애플리케이션을 통해 외부 사용자에 대한 로그인 환경을 제공할 수도 있습니다. 사용자가 애플리케이션에 가입하는 방법을 제어하고 회사 계정, 학교 계정 또는 원하는 소셜 ID(예: Google 또는 Facebook)를 사용하도록 허용하는 사용자 흐름 설정을 구성할 수 있습니다.  자세한 내용은 [Azure AD B2B 설명서](index.yml)를 참조하세요.

- **다른 Azure AD 테넌트(단일 테넌트 또는 다중 테넌트)용 앱 개발**. Azure AD용 애플리케이션을 개발할 때 단일 조직의 사용자(단일 테넌트) 또는 Azure AD 테넌트가 이미 있는 조직의 사용자(다중 테넌트 애플리케이션이라고 함)를 대상으로 지정할 수 있습니다. 이러한 다중 테넌트 애플리케이션은 사용자가 자체 Azure AD에 한 번 등록하면 추가 작업 없이 모든 조직의 모든 Azure AD 사용자가 사용할 수 있습니다.

- **Azure AD B2C(소비자 및 고객)용 화이트 라벨 앱 개발** 고객용 앱을 만드는 기업 또는 개발자는 Azure AD B2C를 사용하여 소비자, 고객 또는 시민용으로 확장할 수 있습니다. 개발자는 해당 애플리케이션의 전체 기능을 갖춘 ID 시스템으로 Azure AD를 사용할 수 있고 고객이 미리 설정한 ID(예: Facebook 또는 Gmail)를 사용하여 로그인하도록 합니다. Azure AD B2C를 사용하여 고객이 가입하고, 로그인하고, 애플리케이션 사용 중에 자신의 프로필을 관리하는 방법을 완전히 사용자 지정하고 제어할 수 있습니다. 자세한 내용은 [Azure AD B2C 설명서](https://docs.microsoft.com/azure/active-directory-b2c/)를 참조하세요.

## <a name="compare-external-identities-solutions"></a>External Identities 솔루션 비교

아래 표에서는 Azure AD 외부 ID를 사용하여 구현할 수 있는 다양한 시나리오를 비교한 자세한 설명입니다.

| 다중 테넌트 애플리케이션  | 외부 사용자 협업(B2B) | 소비자 또는 고객용 앱(B2C)  |
| ---- | --- | --- |
| 주요 시나리오: 엔터프라이즈 SaaS(Software-as-a-Service) | 주요 시나리오: Microsoft 애플리케이션(Microsoft 365, Teams 등) 또는 자체 협업 소프트웨어를 사용하는 협업입니다.  | 주요 시나리오: 사용자 지정 개발 애플리케이션을 사용하는 트랜잭션 애플리케이션   |
| 대상: 여러 기업 고객에게 소프트웨어를 제공하려는 조직    | 대상: ID 공급자에 관계 없이 파트너 조직의 사용자를 인증할 수 있기를 원하는 조직    | 대상: 개인이든, 기관이든, 조직이든 상관없이 모바일 및 웹앱 고객을 조직의 자체 디렉터리와 구분된 Azure AD 디렉터리에 초대하려는 사용자 |
| 지원되는 ID: Azure AD 계정이 있는 직원 | 지원되는 ID: 회사 또는 학교 계정이 있는 직원, 회사 또는 학교 계정이 있는 파트너 또는 모든 이메일 주소입니다. 직접 페더레이션 기능을 곧 지원합니다.      | 지원되는 ID: 로컬 애플리케이션 계정(모든 이메일 주소 또는 사용자 이름)이 있는 소비자 사용자 또는 직접 페더레이션이 포함된 지원되는 소셜 ID입니다.       |
| 외부 사용자는 애플리케이션이 등록된 디렉터리와 격리된 자체 디렉터리에서 관리됩니다.    | 외부 사용자는 직원과 같은 디렉터리에서 관리되지만 특별 주석이 추가됩니다. 이들은 직원과 같은 방식으로 관리하고 동일한 그룹에 추가할 수 있습니다.    | 외부 사용자는 애플리케이션 디렉터리에서 관리됩니다. 이들은 조직의 직원 및 파트너 디렉터리(있는 경우)와 별도로 관리됩니다.  |
| Single Sign-On: 모든 Azure AD 연결 앱에 대한 SSO가 지원됩니다.          | Single Sign-On: 모든 Azure AD 연결 앱에 대한 SSO가 지원됩니다. 예를 들어 Microsoft 365 또는 온-프레미스 앱 및 다른 SaaS 앱(예: Salesforce 또는 Workday)에 대한 액세스를 제공할 수 있습니다.    | Single Sign-On: Azure AD B2C 테넌트 내의 고객 소유 앱에 대한 SSO가 지원됩니다. Microsoft 365 또는 다른 Microsoft SaaS 앱에 대한 SSO가 지원되지 않습니다.    |
| 고객 수명 주기: 사용자의 홈 조직에서 관리합니다.      | 파트너 수명 주기: 호스트/초대한 조직에 의해 관리됩니다.    | 고객 수명 주기: 셀프 서비스 또는 애플리케이션에 의해 관리됩니다.      |
| 보안 정책 및 규정 준수: 호스트/초대한 조직에 의해 관리됩니다(예: [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access) 사용).           | 보안 정책 및 규정 준수: 호스트/초대한 조직에 의해 관리됩니다(예: [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access) 사용). | 보안 정책 및 규정 준수: 애플리케이션에 의해 관리됩니다.        |
| 브랜딩: 호스트/초대한 조직의 브랜드가 사용됩니다.   | 브랜딩: 호스트/초대한 조직의 브랜드가 사용됩니다.    | 브랜딩: 애플리케이션에 의해 관리됩니다. 일반적으로 조직의 존재가 희미해지고 제품 브랜드가 되는 경향이 있습니다.   |
| 추가 정보: [다중 테넌트 애플리케이션의 ID 관리](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [방법 가이드](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | 추가 정보: [블로그 게시물](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [설명서](what-is-b2b.md)                   | 추가 정보: [제품 페이지](https://azure.microsoft.com/services/active-directory-b2c/), [설명서](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Azure AD 외부 ID를 사용하여 조직 외부에 있는 고객과 파트너를 보호하고 관리합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure AD B2C 정보](https://docs.microsoft.com/azure/active-directory-b2c/overview)
