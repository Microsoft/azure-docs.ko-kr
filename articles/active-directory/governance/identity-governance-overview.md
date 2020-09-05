---
title: ID 거버넌스 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory ID 거버넌스를 사용하면 올바른 프로세스와 표시 유형을 사용하여 조직의 보안과 직원 생산성 간에 적절한 균형을 유지할 수 있습니다.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711903b106bf6ff7d8f669e2b1d3489057e09fbc
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783300"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD Identity Governance란?

Azure AD(Azure Active Directory) ID 거버넌스를 사용하면 올바른 프로세스와 표시 유형을 사용하여 조직의 보안과 직원 생산성 간에 적절한 균형을 유지할 수 있습니다. 적절한 사용자에게 적절한 리소스에 대한 적절한 액세스 권한을 제공할 수 있습니다. 이러한 Azure AD 및 Enterprise Mobility + Security 기능을 사용하면 중요한 자산에 대한 액세스를 보호, 모니터링 및 감사하여 액세스 위험을 완화하면서도 직원 및 비즈니스 파트너의 생산성을 높일 수 있습니다.  

ID 거버넌스는 온-프레미스와 클라우드에서 직원, 비즈니스 파트너와 공급 업체, 서비스와 애플리케이션 전체에 걸쳐 다음을 수행하는 기능을 조직에 제공합니다.

- ID 수명 주기 거버넌스
- 액세스 수명 주기 거버넌스
- 관리를 위한 권한 있는 액세스 보호

특히 조직이 이러한 네 가지 주요 질문을 해결할 수 있도록 합니다.

- 액세스할 수 있는 리소스 및 사용자는 무엇인가요?
- 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?
- 액세스를 관리하기 위한 유효한 조직 컨트롤이 있나요?
- 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

## <a name="identity-lifecycle"></a>ID 수명 주기

조직에서는 ID 거버넌스를 통해 *생산성*(사용자가 조직에 가입할 때처럼 필요한 리소스에 액세스하는 속도)과 *보안*(고용 상태의 변화처럼 시간에 따른 사용자의 액세스 권한 변화) 간에 적절한 균형을 유지할 수 있습니다.  ID 수명 주기 관리는 ID 거버넌스의 기반이며 효과적인 대규모 거버넌스를 원한다면 애플리케이션의 ID 수명 주기 관리 인프라를 현대화해야 합니다.

![ID 수명 주기](./media/identity-governance-overview/identity-lifecycle.png)

많은 조직에서 직원의 ID 수명 주기가 HCM(인적 자원 관리) 시스템에서 해당 사용자의 표현에 연결됩니다.  Azure AD Premium은 [Workday 인바운드 프로비저닝 자습서](../saas-apps/workday-inbound-tutorial.md)에 설명된 대로 Active Directory와 Azure Active Directory의 Workday에 표시된 사람에 대한 사용자 ID를 자동으로 유지합니다.  또한 Azure AD Premium에는 SAP, Oracle eBusiness 및 Oracle PeopleSoft와 같은 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 [Microsoft Identity Manager](/microsoft-identity-manager/)가 포함됩니다.

점점 더 많은 시나리오에서는 조직 외부 사람들과 협업해야 합니다. [Azure AD B2B](/azure/active-directory/b2b/) 협업을 사용하면 조직의 애플리케이션과 서비스를 게스트 사용자 및 모든 조직의 외부 파트너와 안전하게 공유하면서 고유한 회사 데이터에 대한 제어를 유지합니다.  [Azure AD 권한 관리](entitlement-management-overview.md)을 사용하면 액세스 요청을 허용하고 조직의 디렉터리에 B2B 게스트로 추가할 사용자를 선택할 수 있으며, 이 게스트가 더 이상 액세스할 필요가 없을 때 이 게스트를 제거할 수 있습니다.

## <a name="access-lifecycle"></a>액세스 수명 주기

조직은 사용자의 ID를 만들 때 해당 사용자에 대해 처음으로 프로비전된 범위를 넘어서 액세스를 관리하는 프로세스가 필요합니다.  또한 기업 조직은 액세스 정책 및 컨트롤을 지속적으로 개발하고 적용할 수 있도록 효율적으로 규모를 조정해야 합니다.

![액세스 수명 주기](./media/identity-governance-overview/access-lifecycle.png)

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다.  또한 IT에는 사용자가 스스로가 포함될 수 있습니다.  예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자는 초대된 조직의 데이터에 대해 처리 중인 요구 사항을 인식하지 못할 수 있습니다.

조직은 [동적 그룹](../users-groups-roles/groups-dynamic-membership.md)과 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화하여 [SaaS 앱](../saas-apps/tutorial-list.md) 또는 [SCIM과 통합된 앱](../app-provisioning/use-scim-to-provision-users-and-groups.md)에 프로비전한 사용자와 결합할 수 있습니다.  조직은 [온-프레미스 애플리케이션에 액세스할 수 있는 게스트 사용자](../external-identities/hybrid-cloud-to-on-premises.md)를 제어할 수도 있습니다.  그런 다음, 정기 [Azure AD 액세스 검토](access-reviews-overview.md)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.   또한 [Azure AD 권한 관리](entitlement-management-overview.md)을 사용하여 사용자가 그룹과 팀 멤버 자격, 애플리케이션 역할 및 SharePoint Online 역할의 패키지에서 액세스를 요청하는 방법을 정의할 수도 있습니다.

사용자가 애플리케이션에 액세스하려고 하는 경우 Azure AD는 [조건부 액세스](../conditional-access/index.yml) 정책을 적용합니다. 예를 들어 조건부 액세스 정책에는 애플리케이션에 액세스하기 전에 [사용 약관](../conditional-access/terms-of-use.md)을 표시하고 [사용자가 해당 약관에 동의했는지 확인](../conditional-access/require-tou.md)하는 작업이 포함될 수 있습니다.

## <a name="privileged-access-lifecycle"></a>권한 있는 액세스 수명 주기

과거에 다른 공급 업체에서는 권한 있는 액세스를 ID 거버넌스의 별도 기능으로 설명했습니다. 그러나 Microsoft에서는 권한 있는 액세스가 ID 거버넌스의 중요한 부분이라고 생각합니다. 특히 해당 관리자 권한과 연결된 오용 가능성이 조직에서 발생할 수 있기 때문입니다. 관리 권한을 사용하는 직원, 공급 업체 및 계약자를 관리해야 합니다.

![권한 있는 액세스 수명 주기](./media/identity-governance-overview/privileged-access-lifecycle.png)

[PIM(Azure AD Privileged Identity Management)](../privileged-identity-management/pim-configure.md)에서는 Azure AD, Azure 및 기타 Microsoft 온라인 서비스에서 리소스에 대한 액세스 권한을 보호하기 위해 조정된 추가 컨트롤을 제공합니다.  다단계 인증 및 조건부 액세스 외에도, Just-In-Time 액세스 및 Azure AD PIM에서 제공된 역할 변경 내용 경고 기능은 포괄적인 거버넌스 제어 세트를 제공하여 회사의 리소스(디렉터리, Office 365 및 Azure 리소스 역할)를 보호하는 데 도움을 줍니다. 다른 양식의 액세스를 사용하는 경우와 마찬가지로 조직은 액세스 검토를 사용하여 관리자 역할의 모든 사용자에 대해 정기 액세스 재인증을 구성할 수 있습니다.

## <a name="getting-started"></a>시작

Azure Portal에서 **ID 거버넌스**의 시작 탭을 확인하여 권한 관리, 액세스 검토, Privileged Identity Management 및 사용 약관 사용을 시작하세요.

![ID 거버넌스 시작](./media/identity-governance-overview/getting-started.png)


ID 거버넌스 기능에 대한 피드백이 있는 경우 Azure Portal에서 **피드백이 있나요?** 를 클릭하여 피드백을 제출해 주세요. 팀에서 여러분의 피드백을 정기적으로 검토합니다.

모든 고객에게 맞는 완벽한 솔루션이나 권장 사항은 없지만, 다음 구성 가이드에서는 직원의 보안과 생산성을 보다 높일 수 있는 Microsoft의 권장 기준 정책을 제공합니다.

- [ID 및 디바이스 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [권한 있는 액세스 보안](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>부록 - ID 거버넌스 기능을 관리하기 위한 최소 권한 역할

ID 거버넌스에서 관리 작업을 수행할 때 최소 권한 역할을 사용하는 것이 가장 좋습니다. 이러한 작업을 수행하기 위해 필요할 때 Azure AD PIM을 사용하여 역할을 활성화 는 것이 좋습니다. 다음은 ID 거버넌스 기능을 구성하는 데 필요한 최소 권한 디렉터리 역할입니다.

| 기능 | 최소 권한 역할 |
| ------- | --------------------- |
| 권한 관리 | 사용자 관리자(카탈로그에 SharePoint Online 사이트를 추가하는 권한 제외. 이 작업은 전역 관리자 권한이 필요) |
| 액세스 검토 | 사용자 관리자(Azure 또는 Azure AD 역할의 액세스 검토 제외. 이 작업은 권한 있는 역할 관리자가 필요) |
|Privileged Identity Management | 권한 있는 역할 관리자 |
| 사용 약관 | 보안 관리자 또는 조건부 액세스 관리자 |

## <a name="next-steps"></a>다음 단계

- [Azure AD 권한 관리란?](entitlement-management-overview.md)
- [Azure AD 액세스 검토란?](access-reviews-overview.md)
- [Azure AD Privileged Identity Management란?](../privileged-identity-management/pim-configure.md)
- [사용 약관으로 무엇을 할 수 있나요?](../conditional-access/terms-of-use.md)