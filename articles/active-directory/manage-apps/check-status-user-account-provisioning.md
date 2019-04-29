---
title: SaaS 애플리케이션에 대한 Azure Active Directory 자동 사용자 계정 프로비전 보고 | Microsoft Docs
description: 자동 사용자 계정 프로비전 작업의 상태를 확인하는 방법과 개별 사용자의 프로비전 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: e293eb555269a1f8beeeccd74000358abe1ef5c8
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759755"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>자습서: 자동 사용자 계정 프로비전에 대한 보고


Azure Active Directory에는 종단 간 ID 수명 주기 관리를 위해 SaaS 앱 및 기타 시스템에서 사용자 계정을 자동으로 프로비전하거나 프로비전 해제하는 데 유용한 [사용자 계정 프로비전 서비스](user-provisioning.md)가 포함되어 있습니다. Azure AD는 [Azure AD 애플리케이션 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)의 "추천 앱" 섹션에 있는 모든 애플리케이션 및 시스템에 대해 사전 통합된 사용자 프로비전 커넥터를 지원합니다.

이 문서에서는 프로비전 작업을 설정한 후 해당 상태를 확인하는 방법과 개별 사용자 및 그룹의 프로비전 문제를 해결하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

프로비전 커넥터는 지원되는 애플리케이션에 [제공된 설명서](../saas-apps/tutorial-list.md)에 따라 [Azure Portal](https://portal.azure.com)을 사용하여 설정하고 구성합니다. 일단 구성되고 실행된 후에는 다음 두 가지 방법 중 하나를 사용하여 프로비전 작업을 보고할 수 있습니다.

* **Azure 관리 포털** - 이 문서에서는 주로 지정된 애플리케이션에 대한 프로비저닝 요약 보고서와 자세한 프로비저닝 감사 로그를 제공하는 [Azure Portal](https://portal.azure.com)에서 보고서 정보를 검색하는 방법에 대해 설명합니다.

* **감사 API** - Azure Active Directory는 자세한 프로비저닝 감사 로그를 프로그래밍 방식으로 검색할 수 있게 해주는 감사 API도 제공합니다. 이 API의 사용과 관련하여 [Azure Active Directory 감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 문서를 참조하세요. 이 문서에서는 API를 사용하는 방법을 구체적으로 다루지 않지만, 감사 로그에 기록되는 프로비전 이벤트 유형에 대해서는 자세히 설명합니다.

### <a name="definitions"></a>정의

이 문서에서 사용하는 용어는 다음과 같이 정의됩니다.

* **원본 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 원본이 되는 사용자의 리포지토리입니다. Azure Active Directory는 대부분의 사전 통합된 프로비전 커넥터를 위한 원본 시스템이지만, 몇 가지 예외(예: Workday 인바운드 동기화)가 있습니다.

* **대상 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 대상이 되는 사용자의 리포지토리입니다. 이 시스템은 일반적으로 SaaS 애플리케이션(예: Salesforce, ServiceNow, G Suite, Dropbox for Business) 경우에 따라 Active Directory와 같은 온-프레미스 시스템을 수 있지만 (예: Active Directory에 대한 Workday 인바운드 동기화)일 수도 있습니다.


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Azure 관리 포털에서 프로비전 보고서 가져오기

지정된 애플리케이션에 대한 프로비전 보고서 정보를 가져오려면 [Azure 관리 포털](https://portal.azure.com)을 시작하고 프로비전이 구성된 엔터프라이즈 애플리케이션을 검색합니다. 예를 들어 사용자를 LinkedIn Elevate로 프로비전하는 경우 애플리케이션 세부 정보의 탐색 경로는 다음과 같습니다.

**Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션 &gt; LinkedIn Elevate**

여기서는 아래에서 설명하는 프로비저닝 요약 보고서와 프로비저닝 감사 로그에 모두 액세스할 수 있습니다.


## <a name="provisioning-summary-report"></a>요약 보고서 프로비전

프로비전 요약 보고서는 지정된 애플리케이션에 대한 **프로비전** 탭에 표시됩니다. **설정** 아래의 **동기화 세부 정보** 섹션에 있으며, 제공되는 정보는 다음과 같습니다.

* 동기화되어 현재의 원본 시스템과 대상 시스템 간 프로비전에 해당하는 범위에 포함된 총 사용자 및/또는 그룹 수

* 동기화가 마지막으로 실행된 시간 - 일반적으로 [초기 동기화](user-provisioning.md#what-happens-during-provisioning)가 완료된 후 20-40분마다 동기화가 발생합니다.

* [초기 동기화](user-provisioning.md#what-happens-during-provisioning)가 완료되었는지 여부.

* 프로비저닝 프로세스의 격리 여부 및 격리 상태에 대한 이유(예: 잘못된 관리자 자격 증명으로 인해 대상 시스템과 통신하지 못하는 경우).

프로비전 요약 보고서는 프로비전 작업의 작동 상태를 확인하기 위해 관리자가 먼저 확인해야 합니다.

 ![요약 보고서](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>감사 로그 프로비전
프로비전 서비스에서 수행되는 모든 활동은 Azure AD 감사 로그에 기록되며, **계정 프로비전** 범주 아래의 **감사 로그** 탭에서 볼 수 있습니다. 기록되는 활동 이벤트 유형은 다음과 같습니다.

* **가져오기 이벤트** - Azure AD 프로비전 서비스에서 원본 시스템 또는 대상 시스템의 개별 사용자 또는 그룹에 대한 정보를 검색할 때마다 "가져오기" 이벤트가 기록됩니다. 동기화하는 동안 먼저 "가져오기" 이벤트로 기록된 결과와 함께 원본 시스템에서 사용자를 검색합니다. 그런 다음 검색된 사용자와 일치하는 ID에 대해 대상 시스템에 쿼리하여 "가져오기" 이벤트로 기록된 결과와 함께 해당 ID가 있는지 확인합니다. 이러한 이벤트는 이벤트 발생 시 Azure AD 프로비전 서비스에서 표시한 모든 매핑된 사용자 특성과 해당 값을 기록합니다. 

* **동기화 규칙 이벤트** - 원본 및 대상 시스템에서 사용자 데이터를 가져와서 평가한 후에 특성 매핑 규칙 및 구성된 모든 범위 지정 필터에 기반한 결과를 보고합니다. 예를 들어 원본 시스템의 사용자가 프로비전 범위에 포함되지만 대상 시스템에 존재하지 않는 것으로 간주되는 경우 이 이벤트는 대상 시스템에 해당 사용자를 프로비전할 것이라고 기록합니다. 

* **내보내기 이벤트** - Azure AD 프로비전 서비스에서 사용자 계정 또는 그룹 개체를 대상 시스템에 작성할 때마다 "내보내기" 이벤트가 기록됩니다. 이러한 이벤트는 이벤트 발생 시 Azure AD 프로비전 서비스에서 작성한 모든 사용자 특성과 해당 값을 기록합니다. 사용자 계정 또는 그룹 개체를 대상 시스템에 작성하는 동안 오류가 발생하면 여기에 표시됩니다.

* **프로세스 에스크로 이벤트** - 프로비전 서비스에서 작업을 시도하는 동안 오류가 발생하고 백오프 간격으로 작업 다시 시도를 시작할 때 프로세스 에스크로가 발생합니다. 프로비저닝 작업이 다시 시도될 때마다 “에스크로” 이벤트가 기록됩니다.

개별 사용자에 대한 프로비전 이벤트를 볼 때 일반적으로 다음과 같은 순서로 이벤트가 발생합니다.

1. 가져오기 이벤트: 원본 시스템에서 사용자를 검색합니다.

2. 가져오기 이벤트: 검색된 사용자의 존재 여부를 확인하기 위해 대상 시스템을 쿼리합니다.

3. 동기화 규칙 이벤트: 구성된 특성 매핑 규칙 및 범위 지정 필터를 통해 원본 및 대상 시스템의 사용자 데이터를 평가하여 수행해야 할 작업(있는 경우)을 결정합니다.

4. 내보내기 이벤트: 동기화 규칙 이벤트에서 수행해야 할 작업(추가, 업데이트, 삭제)을 지시하면 해당 작업 결과를 내보내기 이벤트에 기록합니다.

![Azure AD 테스트 사용자 만들기](./media/check-status-user-account-provisioning/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>특정 사용자에 대한 프로비전 이벤트 조회

프로비저닝 감사 로그에 대한 가장 일반적인 사용 사례는 개별 사용자 계정의 프로비저닝 상태를 확인하는 것입니다. 특정 사용자에 대한 마지막 프로비전 이벤트를 조회하려면 다음을 수행합니다.

1. **감사 로그** 섹션으로 이동합니다.

2. **범주** 메뉴에서 **계정 프로비전**을 선택합니다.

3. **날짜 범위** 메뉴에서 검색할 날짜 범위를 선택합니다.

4. **검색** 창에서 검색하려는 사용자의 사용자 ID를 입력합니다. ID 값의 형식은 특성 매핑 구성에서 일치하는 기본 ID로 선택한 것과 일치해야 합니다(예: userPrincipalName 또는 직원 ID 번호). 필요한 ID 값은 [대상] 열에 표시됩니다.

5. Enter 키를 눌러 검색합니다. 가장 최근의 프로비전 이벤트가 먼저 반환됩니다.

6. 이벤트가 반환되면 활동 유형과 성공 또는 실패 여부를 기록합니다. 결과가 반환되지 않으면 사용자가 존재하지 않거나 전체 동기화가 아직 완료되지 않은 경우 프로비전 프로세스에서 해당 사용자를 검색하지 못했음을 의미합니다.

7. 개별 이벤트를 클릭하면 이벤트의 일부로 검색, 평가 또는 작성된 모든 사용자 특성을 포함하여 펼쳐진 세부 정보를 볼 수 있습니다.

감사 로그를 사용하는 방법에 대한 데모는 아래 비디오를 참조하세요. 감사 로그는 5:30 태그에 표시됩니다.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>프로비전 감사 로그 보기 팁

Azure Portal에서 최상의 가독성을 얻으려면 **열** 단추를 선택하고 다음 열을 선택합니다.

* **날짜** - 이벤트가 발생한 날짜를 표시합니다.
* **대상** - 이벤트의 주체인 앱 이름과 사용자 ID를 표시합니다.
* **활동** - 앞에서 설명한 활동 유형입니다.
* **상태** - 이벤트가 성공했는지 여부입니다.
* **상태 설명** - 프로비전 이벤트에서 발생한 상황에 대한 요약입니다.


## <a name="troubleshooting"></a>문제 해결

프로비저닝 요약 보고서 및 감사 로그는 관리자가 다양한 사용자 계정 프로비저닝 문제를 해결하는 데 중요한 역할을 합니다.

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](application-provisioning-config-problem.md)를 참조하세요.


## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](what-is-single-sign-on.md)
