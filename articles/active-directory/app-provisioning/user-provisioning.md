---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d061d830df9cef305982ff54e91a886d4406d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066084"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Azure Active Directory 사용 하 여 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화

Azure AD (Azure Active Directory)에서 용어 **앱 프로 비전은** 사용자가 액세스 해야 하는 클라우드 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만드는 것을 의미 합니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 일반적인 시나리오에는 Azure AD 사용자를 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 등과 같은 애플리케이션에 프로비저닝하는 것이 포함됩니다.

![프로 비전 개요 다이어그램](./media/user-provisioning/provisioning-overview.png)

이 기능을 통해 다음을 수행할 수 있습니다.

- **프로 비전 자동화**: 팀 또는 조직에 참여할 때 새 사용자를 위해 올바른 시스템에 새 계정을 자동으로 만듭니다.
- **프로 비전 해제 자동화:** 사용자가 팀 이나 조직을 떠날 때 적절 한 시스템에서 계정을 자동으로 비활성화 합니다.
- **시스템 간 데이터 동기화:** 디렉터리 또는 인적 자원 시스템의 변경 내용에 따라 앱 및 시스템의 id를 최신 상태로 유지 해야 합니다.
- **프로 비전 그룹:** 이를 지 원하는 응용 프로그램에 그룹을 프로 비전 합니다.
- **액세스 제어:** 응용 프로그램에 프로 비전 된 사용자를 모니터링 하 고 감사 합니다.
- **갈색 필드 시나리오에서 원활 하 게 배포:** 시스템 간에 기존 id를 일치 시키고 사용자가 대상 시스템에 이미 있는 경우에도 쉽게 통합할 수 있습니다.
- **다양 한 사용자 지정 사용:** 원본 시스템에서 대상 시스템으로 이동 해야 하는 사용자 데이터를 정의 하는 사용자 지정 가능한 특성 매핑을 활용 합니다.
- **중요 이벤트에 대 한 경고 받기:** 프로 비전 서비스는 중요 이벤트에 대 한 경고를 제공 하며, 비즈니스 요구에 맞게 사용자 지정 경고를 정의할 수 있는 Log Analytics 통합을 허용 합니다.

## <a name="benefits-of-automatic-provisioning"></a>자동 프로 비전의 이점

최신 조직에서 사용 되는 응용 프로그램 수가 계속 증가 함에 따라 IT 관리자는 대규모로 액세스 관리를 수행 합니다. SAML (Security 어설션이 Markup Language) 또는 OIDC (Open ID Connect)와 같은 표준을 사용 하면 관리자가 SSO (Single Sign-On)를 신속 하 게 설정할 수 있지만 액세스를 위해서는 사용자를 앱에 프로 비전 해야 합니다. 대부분의 관리자는 프로 비전을 통해 모든 사용자 계정을 수동으로 만들거나 매주 CSV 파일을 업로드 하는 것을 의미 하지만 이러한 프로세스는 시간이 많이 걸리고, 비용이 많이 들고, 오류가 발생 하기 쉽습니다. 프로 비전을 자동화 하기 위해 SAML JIT (just-in-time)와 같은 솔루션을 채택 했지만, 조직에서 탈퇴 하거나 역할 변경에 따라 특정 앱에 더 이상 액세스할 필요가 없는 경우에는 사용자를 프로 비전 해제 하는 솔루션이 필요 합니다.

자동 프로비저닝을 사용 하는 일반적인 몇 가지 동기는 다음과 같습니다.

- 프로 비전 프로세스의 효율성과 정확성을 극대화 합니다.
- 사용자 지정 개발 프로 비전 솔루션 및 스크립트의 호스팅 및 유지 관리와 관련 된 비용을 절감 합니다.
- 조직에서 나갈 때 키 SaaS 앱에서 사용자 id를 즉시 제거 하 여 조직의 보안을 유지 합니다.
- 특정 SaaS 응용 프로그램 또는 시스템으로 많은 사용자를 쉽게 가져올 수 있습니다.
- 단일 정책 집합을 포함 하 여 프로 비전 된 사용자 및 앱에 로그인 할 수 있는 사람을 결정 합니다.

Azure AD 사용자 프로 비전은 이러한 문제를 해결 하는 데 도움이 될 수 있습니다. 고객이 Azure AD 사용자 프로 비전을 사용 하는 방법에 대해 자세히 알아보려면 [Asos 사례 연구](https://aka.ms/asoscasestudy)를 읽을 수 있습니다. 아래 비디오는 Azure AD에서 사용자 프로 비전의 개요를 제공 합니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD는 널리 사용 되는 많은 SaaS 앱 및 인적 자원 시스템에 대 한 사전 통합 된 지원과 [Scim 2.0 표준](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)의 특정 부분을 구현 하는 앱에 대 한 일반 지원을 제공 합니다.

* **사전 통합 된 응용 프로그램 (갤러리 SaaS 앱)** . Azure AD [가 사용자 프로 비전을 위한 응용 프로그램 자습서 목록](../saas-apps/tutorial-list.md)에서 사전 통합 된 프로 비전 커넥터를 지 원하는 모든 응용 프로그램을 찾을 수 있습니다. 갤러리에 나열 된 사전 통합 된 응용 프로그램은 일반적으로 프로 비전을 위해 SCIM 2.0 기반 사용자 관리 Api를 사용 합니다. 

   ![Salesforce 로고](./media/user-provisioning/gallery-app-logos.png)

   프로 비전을 위해 새 응용 프로그램을 요청 하려는 경우 [응용 프로그램을 앱 갤러리와 통합 하도록 요청할](../develop/howto-app-gallery-listing.md)수 있습니다. 사용자 프로 비전 요청의 경우 응용 프로그램에 SCIM 규격 엔드포인트이 있어야 합니다. 앱을 플랫폼에 빠르게 등록할 수 있도록 응용 프로그램 공급 업체가 SCIM 표준을 따르도록 요청 하세요.

* **SCIM 2.0을 지 원하는 응용 프로그램**입니다. 일반적으로 SCIM 2.0 기반 사용자 관리 Api를 구현 하는 응용 프로그램을 연결 하는 방법에 대 한 자세한 내용은 [scim 엔드포인트 빌드 및 사용자 프로 비전 구성](use-scim-to-provision-users-and-groups.md)을 참조 하세요.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>SCIM (도메인 간 Id 관리)에 대 한 시스템 이란?

프로 비전 및 프로 비전 해제를 자동화 하기 위해 앱은 독점적인 사용자 및 그룹 Api를 노출 합니다. 그러나 둘 이상의 앱에서 사용자를 관리 하려는 사용자는 모든 앱이 사용자 만들기 또는 업데이트, 그룹에 사용자 추가 또는 프로 비전 해제와 같은 간단한 작업을 수행 하려고 한다는 것을 알 수 있습니다. 그러나 이러한 모든 간단한 작업은 다른 엔드포인트 경로, 사용자 정보를 지정 하는 다양 한 메서드 및 정보의 각 요소를 나타내는 다른 스키마를 사용 하 여 약간 약간 다르게 구현 됩니다.

이러한 문제를 해결 하기 위해 SCIM 사양은 사용자가 앱으로 전환 하 고 앱을 확장 하는 데 도움이 되는 공용 사용자 스키마를 제공 합니다. SCIM은 프로 비전을 위한 사실상 표준으로 사용 되며, SAML 또는 Openid connect Connect와 같은 페더레이션 표준과 함께 사용 될 경우 관리자에 게 액세스 관리를 위한 종단 간 표준 기반 솔루션을 제공 합니다.

SCIM을 사용 하 여 응용 프로그램에 사용자 및 그룹의 프로 비전 및 프로 비전 해제를 자동화 하는 방법에 대 한 자세한 지침은 [scim 엔드포인트 빌드 및 사용자 프로 비전 구성](use-scim-to-provision-users-and-groups.md)을 참조 하세요.

## <a name="manual-vs-automatic-provisioning"></a>수동 및 자동 프로비저닝

Azure AD 갤러리의 응용 프로그램은 다음 두 가지 프로 비전 모드 중 하나를 지원 합니다.

* **수동** 프로 비전은 앱에 대 한 자동 Azure AD 프로 비전 커넥터가 아직 없음을 의미 합니다. 사용자 계정을 수동으로 만들어야 합니다. 예를 들어 사용자를 앱의 관리 포털에 직접 추가 하거나 사용자 계정 세부 정보를 사용 하 여 스프레드시트를 업로드 합니다. 앱에서 제공 하는 설명서를 참조 하거나 앱 개발자에 게 문의 하 여 사용할 수 있는 메커니즘을 확인 하세요.

* **자동**이란 Azure AD 프로비전 커넥터가 이 애플리케이션에 대해 개발되었음을 의미합니다. 응용 프로그램에 대 한 프로 비전을 설정 하는 것과 관련 된 설정 자습서를 따라야 합니다. 앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

Azure AD 갤러리에서 자동 프로 비전을 지 원하는 응용 프로그램은 **프로 비전** 아이콘으로 지정 됩니다. 새 갤러리 미리 보기 환경으로 전환 하 여 이러한 아이콘을 표시 합니다. ( **응용 프로그램 추가 페이지**의 맨 위에 있는 배너에서 여기를 클릭 하 여 **새롭고 향상 된 앱 갤러리를 사용해 보세요**.) 링크를 선택 합니다.

![응용 프로그램 갤러리의 프로 비전 아이콘](./media/user-provisioning/browse-gallery.png)

응용 프로그램을 **엔터프라이즈 앱**에 추가한 후에는 응용 프로그램에서 지 원하는 프로 비전 모드가 **프로 비전** 탭에도 표시 됩니다.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

갤러리에 나열 된 사전 통합 된 응용 프로그램의 경우 자동 프로 비전을 설정 하는 데 단계별 지침을 사용할 수 있습니다. [통합 갤러리 앱에 대 한 자습서 목록](../saas-apps/tutorial-list.md)을 참조 하세요. 다음 비디오는 SalesForce에 대 한 자동 사용자 프로 비전을 설정 하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0을 지 원하는 다른 응용 프로그램의 경우 [scim 엔드포인트 빌드 및 사용자 프로 비전 구성](use-scim-to-provision-users-and-groups.md)문서의 단계를 따릅니다.


## <a name="related-articles"></a>관련 문서

- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [사용자 프로 비전을 위한 특성 매핑 사용자 지정](customize-application-attributes.md)
- [특성 매핑에 대 한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)
- [사용자 프로 비전을 위한 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM 엔드포인트 빌드 및 사용자 프로 비전 구성](use-scim-to-provision-users-and-groups.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
