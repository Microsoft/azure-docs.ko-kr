---
title: SaaS 응용 프로그램에 자동 사용자 계정 프로 비전 보고
description: 자동 사용자 계정 프로비전 작업의 상태를 확인하는 방법과 개별 사용자의 프로비전 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 2de6c07395a559085db237eb1bc7f885998860d0
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235080"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>자습서: 자동 사용자 계정 프로비전에 대한 보고

Azure AD (Azure Active Directory)에는 종단 간 id 수명 주기 관리를 위해 SaaS 앱 및 기타 시스템에서 사용자 계정의 프로 비전 해제를 자동화 하는 데 도움이 되는 [사용자 계정 프로 비전 서비스가](user-provisioning.md) 포함 되어 있습니다. Azure AD는 [여기](../saas-apps/tutorial-list.md)에 사용자 프로비저닝 자습서를 포함 하는 모든 응용 프로그램 및 시스템에 대해 사전 통합 된 사용자 프로 비전 커넥터를 지원 합니다.

이 문서에서는 프로비전 작업을 설정한 후 해당 상태를 확인하는 방법과 개별 사용자 및 그룹의 프로비전 문제를 해결하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

프로비전 커넥터는 지원되는 애플리케이션에 [제공된 설명서](../saas-apps/tutorial-list.md)에 따라 [Azure Portal](https://portal.azure.com)을 사용하여 설정하고 구성합니다. 일단 구성되고 실행된 후에는 다음 두 가지 방법 중 하나를 사용하여 프로비전 작업을 보고할 수 있습니다.

* **Azure Portal** -이 문서에서는 기본적으로 프로 비전 요약 보고서와 지정 된 응용 프로그램에 대 한 자세한 프로 비전 감사 로그를 모두 제공 하는 [Azure Portal](https://portal.azure.com)에서 보고서 정보를 검색 하는 방법을 설명 합니다.
* **감사 API** - Azure Active Directory는 자세한 프로비저닝 감사 로그를 프로그래밍 방식으로 검색할 수 있게 해주는 감사 API도 제공합니다. 이 API의 사용과 관련하여 [Azure Active Directory 감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 문서를 참조하세요. 이 문서에서는 API를 사용하는 방법을 구체적으로 다루지 않지만, 감사 로그에 기록되는 프로비전 이벤트 유형에 대해서는 자세히 설명합니다.

### <a name="definitions"></a>정의

이 문서에서 사용하는 용어는 다음과 같이 정의됩니다.

* **원본 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 원본이 되는 사용자의 리포지토리입니다. Azure Active Directory는 대부분의 사전 통합된 프로비전 커넥터를 위한 원본 시스템이지만, Workday 인바운드 동기화와 같이 일부 예외가 있습니다.
* **대상 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 대상이 되는 사용자의 리포지토리입니다. 일반적으로 SaaS 응용 프로그램 (예: Salesforce, ServiceNow, G Suite, Dropbox for Business) 이지만, 경우에 따라 Active Directory (예: Active Directory에 대 한 Workday 인바운드 동기화)와 같은 온-프레미스 시스템 일 수 있습니다.

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Azure Portal에서 프로 비전 보고서 가져오기

지정 된 응용 프로그램에 대 한 프로 비전 보고서 정보를 가져오려면 먼저 [Azure Portal](https://portal.azure.com) 를 **Azure Active Directory** 시작 하 고 &gt; **Enterprise Apps** &gt; **작업** 섹션에서 Enterprise Apps **프로 비전 로그 (미리 보기)** 를 Azure Active Directory 합니다. 프로 비전이 구성 된 엔터프라이즈 응용 프로그램으로 이동할 수도 있습니다. 예를 들어 사용자를 LinkedIn Elevate로 프로비전하는 경우 애플리케이션 세부 정보의 탐색 경로는 다음과 같습니다.

**Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션 &gt; LinkedIn Elevate**

여기에서 아래에 설명 된 프로 비전 진행률 표시줄과 프로 비전 로그 모두에 액세스할 수 있습니다.

## <a name="provisioning-progress-bar"></a>프로 비전 진행률 표시줄

[프로 비전 진행률 표시줄이](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) 지정 된 응용 프로그램의 **프로 비전** 탭에 표시 됩니다. **설정**아래의 **현재 상태** 섹션에 있으며 현재 초기 또는 증분 주기의 상태를 표시 합니다. 이 섹션에는 다음도 나와 있습니다.

* 동기화되어 현재의 원본 시스템과 대상 시스템 간 프로비전에 해당하는 범위에 포함된 총 사용자 및/또는 그룹 수
* 동기화가 마지막으로 실행된 시간 - 일반적으로 동기화는 [초기 순환이](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 완료 된 후 20-40 분 마다 발생 합니다.
* [초기 순환이](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) 완료 되었는지 여부입니다.
* 프로비저닝 프로세스의 격리 여부 및 격리 상태에 대한 이유(예: 잘못된 관리자 자격 증명으로 인해 대상 시스템과 통신하지 못하는 경우).

**현재 상태** 는 관리자가 프로 비전 작업의 작동 상태를 확인 하는 첫 번째 위치 여야 합니다.

 ![요약 보고서](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>로그 프로 비전 (미리 보기)

프로 비전 서비스에서 수행 하는 모든 작업은 Azure AD [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)에 기록 됩니다. **Azure Active Directory** &gt; 작업 섹션에서 Azure Active Directory **Enterprise Apps** &gt; **프로 비전 로그 (미리 보기)** **Activity** 를 선택 하 여 Azure Portal에서 프로 비전 로그에 액세스할 수 있습니다. 원본 시스템이 나 대상 시스템에서 사용자의 이름 또는 식별자를 기반으로 프로 비전 데이터를 검색할 수 있습니다. 자세한 내용은 [로그 프로 비전 (미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)을 참조 하세요. 기록되는 활동 이벤트 유형은 다음과 같습니다.

## <a name="troubleshooting"></a>문제 해결

프로 비전 요약 보고서 및 프로비저닝 로그는 관리자가 다양 한 사용자 계정 프로 비전 문제를 해결 하는 데 도움이 되는 주요 역할을 합니다.

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](../app-provisioning/application-provisioning-config-problem.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)