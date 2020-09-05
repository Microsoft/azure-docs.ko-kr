---
title: Azure AD에서 자동화된 SaaS 앱 사용자 프로비저닝이란?
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: b6eb571c7c37a628d11f07b4e1b207e38830250b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235403"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>Azure AD에서 자동화된 SaaS 앱 사용자 프로비저닝이란?

Azure AD(Azure Active Directory)에서 **앱 프로비저닝**이라는 용어는 사용자가 액세스해야 하는 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 애플리케이션에서 사용자 ID와 역할을 자동으로 만드는 것을 의미합니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 일반적인 시나리오에는 Azure AD 사용자를 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 등과 같은 애플리케이션에 프로비저닝하는 것이 포함됩니다.

![프로비저닝 개요 다이어그램](./media/user-provisioning/provisioning-overview.png)

이 기능을 통해 다음을 수행할 수 있습니다.

- **프로비저닝 자동화**: 새로운 사람이 팀 또는 조직에 가입할 때 적절한 시스템에서 새 계정을 자동으로 만듭니다.
- **프로비저닝 해제 자동화**: 사용자가 팀 또는 조직을 떠날 때 적절한 시스템에서 계정을 자동으로 비활성화합니다.
- **시스템 간 데이터 동기화:** 디렉터리 또는 인사 관리 시스템의 변경 내용에 따라 앱 및 시스템의 ID를 최신 상태로 유지합니다.
- **그룹 프로비저닝:** 그룹을 지원하는 애플리케이션에 그룹을 프로비저닝합니다.
- **액세스 제어:** 애플리케이션에 프로비저닝된 사용자를 모니터링하고 감사합니다.
- **브라운 필드 시나리오에서 원활하게 배포:** 이미 사용자가 대상 시스템에 있더라도 시스템 간에 기존 ID를 매칭하고 쉽게 통합할 수 있습니다.
- **다양한 사용자 지정 사용:** 원본 시스템에서 대상 시스템으로 이동할 사용자 데이터를 정의하는 사용자 지정 가능한 특성 매핑을 활용합니다.
- **중요 이벤트에 대한 경고 받기:** 프로비저닝 서비스는 중요 이벤트에 대한 경고를 제공하며, 비즈니스 요구 사항에 맞게 사용자 지정 경고를 정의할 수 있는 Log Analytics 통합이 가능합니다.

## <a name="benefits-of-automatic-provisioning"></a>자동 프로비저닝의 이점

현대 조직에서 사용하는 애플리케이션 수가 증가하면서, IT 관리자는 액세스 관리를 대규모로 수행해야 합니다. SAML(Security Assertions Markup Language) 또는 OIDC(Open ID Connect) 같은 표준을 사용하면 관리자가 신속하게 SSO(Single Sign-On)를 설정할 수 있지만, 사용자가 앱에 액세스하려면 사용자를 앱에 프로비저닝해야 합니다. 많은 관리자가 프로비저닝할 때 사용자 계정을 매번 수동으로 만들거나 매주 CSV 파일을 업로드합니다. 그러나 이러한 프로세스는 시간과 비용이 많이 들고 오류가 발생하기 쉽습니다. 기업에서는 프로비저닝을 자동화하기 위해 SAML JIT(Just-In-Time) 같은 솔루션을 도입했지만, 직원이 퇴사하거나 직원의 역할이 변경되면서 특정 앱에 더 이상 액세스할 필요가 없게 될 때 사용자를 프로비저닝 해제하는 솔루션이 여전히 필요합니다.

자동 프로비저닝을 사용해야 하는 일반적인 동기는 다음과 같습니다.

- 프로비저닝 프로세스의 효율성 및 정확도를 최대화합니다.
- 맞춤형으로 개발된 프로비저닝 솔루션과 스크립트를 호스트하고 유지하는 데 관련된 비용을 절감합니다.
- 사용자가 퇴사하는 즉시 주요 SaaS 앱에서 사용자 ID를 제거하여 조직의 보안을 유지합니다.
- 특정 SaaS 애플리케이션 또는 시스템에 다수의 사용자를 손쉽게 가져옵니다.
- 단일 정책 세트를 사용하여 프로비저닝된 사용자와 앱에 로그인할 수 있는 사용자를 편리하게 확인합니다.

Azure AD 사용자 프로비저닝은 이러한 문제를 해결하는 데 도움이 될 수 있습니다. 고객이 Azure AD 사용자 프로비저닝을 사용하는 방법에 대한 자세한 내용은 [ASOS 사례 연구](https://aka.ms/asoscasestudy)를 참조하세요. 아래 비디오는 Azure AD의 사용자 프로비저닝에 대한 개요를 제공합니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD는 [SCIM 2.0 표준](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)의 특정 부분을 구현하는 앱에 대한 일반적 지원뿐 아니라 여러 인기 있는 SaaS 앱 및 인사 관리 시스템에 대한 사전 통합된 지원도 제공합니다.

* **사전 통합된 애플리케이션(갤러리 SaaS 앱)** . Azure AD가 미리 통합된 프로비저닝 커넥터를 지원하는 모든 애플리케이션은 [사용자 프로비저닝에 대한 애플리케이션 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다. 갤러리에 나열된 사전 통합된 애플리케이션은 일반적으로 SCIM 2.0 기반 사용자 관리 API를 프로비저닝에 사용합니다. 

   ![Salesforce 로고](./media/user-provisioning/gallery-app-logos.png)

   프로비저닝에 사용할 새 애플리케이션을 요청하려면 [애플리케이션을 앱 갤러리와 통합하도록 요청](../azuread-dev/howto-app-gallery-listing.md)하면 됩니다. 사용자 프로비저닝 요청의 경우 애플리케이션에 SCIM 규격 엔드포인트가 있어야 합니다. 앱을 플랫폼에 빠르게 온보딩할 수 있도록 애플리케이션 공급업체에게 SCIM 표준을 준수해 달라고 요청하세요.

* **SCIM 2.0을 지원하는 애플리케이션**. SCIM 2.0 기반 사용자 관리 API를 구현하는 애플리케이션을 일반적인 방법으로 연결하려면 [SCIM 엔드포인트를 빌드하고 사용자 프로비저닝 구성](use-scim-to-provision-users-and-groups.md)을 참조하세요.

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>SCIM(System for Cross-domain Identity Management)이란?

프로비저닝 및 프로비저닝 해제를 자동화하기 위해, 앱에서는 독점적인 사용자 및 그룹 API를 노출합니다. 그러나 여러 앱에서 사용자를 관리하려고 시도해 본 분들은 모든 앱에서 사용자 만들기 또는 업데이트, 그룹에 사용자 추가, 사용자 프로비저닝 해제 등의 간단하고 동일한 작업을 수행하려고 한다는 것을 알고 있습니다. 하지만 이 모든 간단한 작업은 여러 엔드포인트 경로, 사용자 정보를 지정하는 여러 메서드, 정보의 각 요소를 나타내는 여러 스키마를 사용하여 서로 조금씩 다르게 구현됩니다.

이러한 문제를 해결하기 위해 SCIM 사양은 사용자가 앱으로 들어가고, 앱에서 나가고, 앱을 탐색하는 데 도움이 되는 공통 사용자 스키마를 제공합니다. 사실상 프로비저닝의 표준으로 자리를 잡아 가고 있는 SCIM은 SAML 또는 OpenID Connect 같은 페더레이션 표준과 함께 사용하면 액세스 관리를 위한 엔드투엔드 표준 기반 솔루션을 관리자에게 제공합니다.

사용자 및 그룹을 애플리케이션에 프로비저닝하고 프로비저닝 해제하는 작업을 자동화하는 방법에 대한 자세한 지침은 [SCIM 엔드포인트를 빌드하고 사용자 프로비저닝 구성](use-scim-to-provision-users-and-groups.md)을 참조하세요. 갤러리의 사전 통합된 애플리케이션(Slack, Azure Databricks, Snowflake 등)은 개발자 설명서를 건너뛰고 [여기](../saas-apps/tutorial-list.md)에 제공된 자습서를 사용하면 됩니다.

## <a name="manual-vs-automatic-provisioning"></a>수동 및 자동 프로비저닝

Azure AD 갤러리의 애플리케이션은 다음 두 가지 프로비저닝 모드 중 하나를 지원합니다.

* **수동** 프로비저닝은 앱에 대한 자동 Azure AD 프로비저닝 커넥터가 아직 없다는 뜻입니다. 사용자 계정을 수동으로 만들어야 합니다. 예를 들어 사용자를 앱의 관리 포털에 직접 추가하거나 사용자 계정 세부 정보가 포함된 스프레드시트를 업로드해야 합니다. 앱에서 제공하는 설명서를 참조하거나 앱 개발자에게 문의하여 사용할 수 있는 메커니즘을 확인하세요.

* **자동**이란 Azure AD 프로비전 커넥터가 이 애플리케이션에 대해 개발되었음을 의미합니다. 애플리케이션의 프로비저닝 설정에 대한 설정 자습서를 따라야 합니다. 앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

Azure AD 갤러리에서 자동 프로비저닝을 지원하는 애플리케이션에는 **프로비저닝** 아이콘이 표시됩니다. 새 갤러리 미리 보기 환경으로 전환하면 이 아이콘을 볼 수 있습니다(**애플리케이션 페이지 추가** 맨 위에 있는 배너에서 **여기를 클릭하여 새롭고 향상된 앱 갤러리 사용해 보기** 링크를 선택).

![애플리케이션 갤러리의 프로비저닝 아이콘](./media/user-provisioning/browse-gallery.png)

**엔터프라이즈 앱**에 애플리케이션을 추가한 후에는 애플리케이션에서 지원하는 프로비저닝 모드가 **프로비저닝** 탭에도 표시됩니다.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

갤러리에 나열된 사전 통합된 애플리케이션의 경우 자동 프로비저닝을 설정하기 위한 단계별 지침이 제공됩니다. [통합 갤러리 앱에 대한 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요. 다음 비디오는 SalesForce에 대한 자동 사용자 프로비저닝을 설정하는 방법을 보여줍니다.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0을 지원하는 다른 애플리케이션은 [SCIM 엔드포인트를 빌드하고 사용자 프로비저닝 구성](use-scim-to-provision-users-and-groups.md) 문서의 단계를 따르세요.


## <a name="next-steps"></a>다음 단계

- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
- [사용자 프로비저닝에 대한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
