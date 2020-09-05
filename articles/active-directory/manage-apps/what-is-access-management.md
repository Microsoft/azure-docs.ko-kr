---
title: Azure AD를 사용하는 앱에 대한 액세스 관리
description: Azure Active Directory는 조직이 앱을 각 사용자가 액세스할 수 있도록 지정하는 방법을 설명합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 7738bd2f2dc169ab52677928c6fecbc193ff2f35
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639922"
---
# <a name="managing-access-to-apps"></a>앱에 대한 액세스 관리

앱이 조직의 ID 시스템에 통합된 후에 진행 중인 액세스 관리, 사용 평가 및 보고는 계속 쉽지 않을 것입니다. 대부분의 경우 IT 관리자나 기술 지원팀이 앱에 대한 액세스를 관리하는 데 지속적이고 적극적인 역할을 맡습니다. 경우에 따라 할당은 일반 또는 부서 IT 팀에서 수행됩니다. 종종 할당 의사 결정은 비즈니스 의사 결정자에게 위임되며 IT가 할당을 수행하기 전에 승인을 필요로 합니다.  다른 조직에서는 역할 기반 Access Control(RBAC) 또는 특성 기반 Access Control(ABAC)과 같은 기존의 자동화된 ID 및 액세스 관리 시스템의 통합에 투자합니다. 통합 및 규칙 개발은 모두 전문화되고 비용이 높은 경향이 있습니다. 관리 방법에 대한 모니터링 또는 보고는 별도로 비용이 드는 복잡한 투자입니다.

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory는 어떻게 지원합니까?

Azure AD는 구성된 애플리케이션에 대한 광범위한 액세스 관리를 지원하며 이는 조직이 위임을 통해 자동으로 특성 기반 할당(ABAC 또는 RBAC 시나리오)에 걸쳐서 관리자 관리를 포함하는 올바른 액세스 정책을 쉽게 달성할 수 있도록 합니다. Azure AD를 사용하여 단일 애플리케이션에 여러 관리 모델을 결합하는 복잡한 정책을 쉽게 달성할 수 있으며 동일한 대상 그룹이 있는 애플리케이션에서 관리 규칙을 다시 사용할 수도 있습니다.

Azure AD를 사용하여 사용 및 할당 보고를 완전히 통합하여 관리자가 할당 상태, 할당 오류 및 사용을 쉽게 보고하도록 설정합니다.

### <a name="assigning-users-and-groups-to-an-app"></a>애플리케이션에 사용자 및 그룹 할당

Azure AD의 애플리케이션 할당은 두 가지 기본 할당 모드에 중점을 둡니다.

* **개별 할당** 디렉터리 전역 관리자 권한을 가진 IT 관리자는 개별 사용자 계정을 선택하고 애플리케이션에 액세스 권한을 부여할 수 있습니다.

* **그룹 기반 할당(Azure AD Premium P1 또는 P2 필요)** 디렉터리 전역 관리자 권한을 가진 IT 관리자는 애플리케이션에 그룹을 할당할 수 있습니다. 특정 사용자의 액세스는 애플리케이션에 액세스하려고 할 때 그룹의 멤버인지 여부로 결정됩니다. 즉, 관리자는 "할당된 그룹의 현재 멤버는 애플리케이션에 액세스한다"는 내용의 할당 규칙을 효율적으로 만들 수 있습니다. 이 할당 옵션을 사용하여 관리자는 [특성 기반 동적 그룹](../fundamentals/active-directory-groups-create-azure-portal.md), 외부 시스템 그룹(예: 온-프레미스 Active Directory 또는 Workday), 관리자 관리 또는 셀프 서비스 관리된 그룹을 포함하는 Azure AD 그룹 관리 옵션 중 하나에서 혜택을 줄 수 있습니다. 단일 그룹은 쉽게 여러 앱에 할당할 수 있으며 할당 선호도가 있는 애플리케이션은 할당 규칙을 공유하여 전반적인 관리 복잡성을 줄일 수 있습니다. 현재 중첩된 그룹 구성원은 이번 애플리케이션에 대한 그룹 기반 할당에 지원되지 않습니다.

이러한 두 가지 할당 모드 관리자를 사용하면 바람직한 할당 관리 방법을 달성할 수 있습니다.

### <a name="requiring-user-assignment-for-an-app"></a>애플리케이션에 대한 사용자 할당 요구

특정 유형의 애플리케이션에는 [사용자를 애플리케이션에 할당하도록 요구](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment)하는 옵션이 있습니다. 이렇게 하면 애플리케이션에 명시적으로 할당한 사용자를 제외한 모든 사용자가 로그인할 수 없습니다. 다음 유형의 애플리케이션은 이 옵션을 지원합니다.

* SAML 기반 인증을 사용하여 페더레이션된 SSO(Single Sign-On)에 대해 구성된 애플리케이션
* Azure Active Directory 사전 인증을 사용하는 애플리케이션 프록시 애플리케이션
* 사용자 또는 관리자가 해당 애플리케이션에 동의한 후 OAuth 2.0/OpenID Connect 인증을 사용하는 Azure AD 애플리케이션 플랫폼을 기반으로 하는 애플리케이션. 특정 엔터프라이즈 애플리케이션은 로그인이 허용된 사용자에 대한 추가 제어를 제공합니다.

사용자 할당을 *요구 하지*않으면 할당 되지 않은 사용자는 내 앱에 앱이 표시 되지 않지만 응용 프로그램 자체 (SP에서 시작 된 로그온이 라고도 함)에 로그인 하거나 응용 프로그램의 **속성** 페이지에서 **사용자 액세스 URL** (IDP 시작 로그온이 라고도 함)을 사용할 수 있습니다.

일부 애플리케이션의 경우 사용자 할당을 요구하는 옵션을 애플리케이션의 속성에서 사용할 수 없습니다. 이러한 경우 PowerShell을 사용하여 서비스 주체에 대해 appRoleAssignmentRequired 속성을 설정할 수 있습니다.

### <a name="determining-the-user-experience-for-accessing-apps"></a>애플리케이션 액세스를 위한 사용자 환경 결정

Azure AD는 조직의 최종 사용자에게 [애플리케이션을 배포하는 사용자 지정 가능한 여러 방법](end-user-experiences.md)을 제공합니다.

* Azure AD 내 앱
* Office 365 애플리케이션 실행 프로그램
* 페더레이션된 앱에 직접 로그온(서비스 주체)
* 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

엔터프라이즈 앱에 할당 된 사용자가 내 앱 및 Office 365 응용 프로그램 시작 관리자에서 볼 수 있는지 여부를 확인할 수 있습니다.

## <a name="example-complex-application-assignment-with-azure-ad"></a>예제: Azure AD를 사용하여 복잡한 애플리케이션 할당
Salesforce와 같은 애플리케이션을 고려합니다. 많은 조직에서 Salesforce는 마케팅 및 판매 팀에서 주로 사용됩니다. 종종 영업 팀의 멤버가 Salesforce에 대해 제한된 액세스만 가능한 반면 마케팅 팀의 멤버는 높은 액세스 권한이 있습니다. 대부분의 경우 정보 근로자의 광범위한 집단은 애플리케이션에 액세스가 제한되었습니다. 이러한 규칙의 예외는 문제를 복잡하게 만듭니다. 이러한 일반 규칙에 관계 없이 사용자에게 액세스 권한을 부여하거나 해당 역할을 변경하는 것은 마케팅 또는 판매 리더십 팀의 특권입니다.

Azure AD를 사용하여 Single Sign-On(SSO) 및 자동화된 프로비전에 Salesforce와 같은 애플리케이션을 미리 구성할 수 있습니다. 애플리케이션이 구성되면 관리자는 일회성 작업을 만들어 적절한 그룹을 만들고 할당할 수 있습니다. 이 예제에서 관리자는 다음의 할당을 실행할 수 있습니다.

* [동적 그룹](../fundamentals/active-directory-groups-create-azure-portal.md) 은 역할 또는 부서와 같은 특성을 사용하여 자동으로 마케팅 및 판매 팀의 모든 멤버를 나타내도록 정의될 수 있습니다.
  
  * 마케팅 그룹의 모든 멤버는 Salesforce에서 "마케팅" 역할에 할당됩니다.
  * 판매 팀 그룹의 모든 멤버는 Salesforce에서 "판매" 역할에 할당됩니다. 추가로 개선하여 다른 Salesforce 역할에 할당된 지역 판매 팀을 나타내는 여러 그룹을 사용할 수 있습니다.

* 예외 메커니즘을 사용하려면 각 역할에 셀프 서비스 그룹을 만들 수 있습니다. 예를 들어 "Salesforce 마케팅 예외" 그룹을 셀프 서비스 그룹으로 만들 수 있습니다. 그룹을 Salesforce 마케팅 역할에 할당할 수 있고 마케팅 리더십 팀을 소유자가 만들 수 있습니다. 마케팅 리더십 팀의 멤버는 사용자를 추가 또는 제거하거나 가입 정책을 설정, 개별 사용자의 가입 요청을 승인 또는 거부합니다. 이 메커니즘은 소유자 또는 멤버에 대한 특별한 교육을 요구하지 않는 정보 작업자에 적절한 환경을 통해 지원됩니다.

이 경우 해당 역할 할당이 Salesforce에서 업데이트된 다른 그룹에 추가되면서 모든 할당된 사용자는 자동으로 Salesforce에 프로비전됩니다. 사용자는 내 앱, Office 웹 클라이언트를 통해 또는 조직 Salesforce 로그인 페이지로 이동 하 여 Salesforce를 검색 하 고 액세스할 수 있습니다. 관리자는 Azure AD 보고를 사용하여 사용 및 할당 상태를 쉽게 볼 수 있습니다.

관리자는 [Azure AD 조건부 액세스](../conditional-access/concept-conditional-access-users-groups.md)를 채택하여 특정 역할에 대한 액세스 정책을 설정할 수 있습니다. 이러한 정책은 기업 환경 외부 및 Multi-Factor Authentication 또는 디바이스 요구에 액세스가 허용되는지 여부를 포함하여 다양한 경우에 액세스를 달성할 수 있습니다.

## <a name="access-to-microsoft-applications"></a>Microsoft 애플리케이션에 대한 액세스

타사 SaaS 애플리케이션 또는 Single Sign-On을 위해 Azure AD와 통합하는 다른 애플리케이션과는 약간 다른 방법으로 Microsoft 애플리케이션(예: Office 365 Exchange, SharePoint, Yammer 등)을 할당하고 관리합니다.

사용자는 세 가지 방법으로 Microsoft 게시 애플리케이션에 대한 액세스 권한을 얻을 수 있습니다.

- Office 365 또는 기타 유료 도구 모음에 있는 애플리케이션의 경우 사용자는 **라이선스 할당**을 통해 해당 사용자 계정으로 직접 액세스가 부여되거나 그룹 기반 라이선스 할당 기능을 사용하여 그룹을 통해 액세스가 부여됩니다.
- Microsoft 또는 타사에서 누구나 자유롭게 사용하도록 게시한 애플리케이션의 경우 사용자는 [사용자 동의](configure-user-consent.md)를 통해 액세스 권한을 부여받을 수 있습니다. 즉, 해당 Azure AD 회사 또는 학교 계정을 사용하여 애플리케이션에 로그인하고 해당 계정에 대한 일부 제한된 데이터 집합에 대한 액세스 권한을 가질 수 있습니다.
- Microsoft 또는 타사에서 누구나 자유롭게 사용하도록 게시한 애플리케이션의 경우 사용자도 [관리자 동의](manage-consent-requests.md)를 통해 액세스 권한을 부여받을 수 있습니다. 즉, 관리자는 전역 관리자 계정 사용하여 애플리케이션에 로그인하고 조직의 모든 사용자에게 액세스 권한을 부여하도록 조직의 모든 사용자가 애플리케이션을 사용할 수 있게 결정했습니다.

일부 애플리케이션에서는 이러한 방법이 결합됩니다. 예를 들어 특정 Microsoft 애플리케이션은 Office 365 구독의 일부지만 여전히 동의가 필요합니다.

사용자는 Office 365 포털을 통해 Office 365 애플리케이션에 액세스할 수 있습니다. 또한 디렉터리의 **사용자 설정**에서 [office 365 표시 유형 설정/해제](hide-application-from-user-portal.md) 를 사용 하 여 내 앱에서 office 365 응용 프로그램을 표시 하거나 숨길 수 있습니다. 

엔터프라이즈 앱과 마찬가지로 Azure Portal을 통해(또는 포털 옵션을 사용할 수 없는 경우 PowerShell을 사용하여) 특정 Microsoft 애플리케이션에 [사용자를 할당](assign-user-or-group-access-portal.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [조건부 액세스를 사용한 앱 보호](../conditional-access/concept-conditional-access-cloud-apps.md)
* [셀프 서비스 그룹 관리/SSAA](../users-groups-roles/groups-self-service-management.md)
