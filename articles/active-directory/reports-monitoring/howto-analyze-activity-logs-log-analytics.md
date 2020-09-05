---
title: Azure Monitor 로그를 사용하여 활동 로그 분석 | Microsoft Docs
description: Azure Monitor 로그를 사용하여 Azure Active Directory 활동 로그를 분석하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab60cb83e68a0c325ec1e2ac102870493016b57
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231234"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 Azure AD 활동 로그 분석

[Azure AD 활동 로그를 Azure Monitor 로그에 통합](howto-integrate-activity-logs-with-log-analytics.md)하면 Azure Monitor 로그의 기능을 사용하여 사용자 환경에 대한 인사이트를 얻을 수 있습니다. [Azure AD 활동 로그에 대한 Log Analytics 보기](howto-install-use-log-analytics-views.md)를 설치하여 사용자 환경의 감사 및 로그인 이벤트에 대한 미리 빌드된 보고서에 액세스할 수도 있습니다.

이 문서에서는 Log Analytics 작업 영역에서 Azure AD 활동 로그를 분석하는 방법을 알아봅니다. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항 

필수 조건을 충족하려면 다음이 필요합니다.

* Azure 구독의 Log Analytics 작업 영역. [Log Analytics 작업 영역을 만드는 방법](../../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.
* 먼저 [Azure AD 활동 로그를 Log Analytics 작업 영역으로 라우팅](howto-integrate-activity-logs-with-log-analytics.md)하는 단계를 완료합니다.
*  Log Analytics 작업 영역에 [액세스](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)
* Azure Active Directory에서 다음 역할(Azure Active Directory 포털을 통해 Log Analytics에 액세스하는 경우)
    - 보안 관리자
    - 보안 판독기
    - 보고서 읽기 권한자
    - 전역 관리자
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Log Analytics 작업 영역으로 이동

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory**를 선택한 다음, **모니터링** 섹션에서 **로그**를 선택하여 Log Analytics 작업 영역을 엽니다. 작업 영역이 기본 쿼리로 열립니다.

    ![기본 쿼리](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Azure AD 활동 로그에 대한 스키마 보기

로그는 작업 영역의 **AuditLogs** 및 **SigninLogs** 테이블로 푸시됩니다. 이러한 테이블의 스키마를 보려면 다음을 수행합니다.

1. 이전 섹션의 기본 쿼리 보기에서 **스키마**를 선택하고 작업 영역을 확장합니다. 

2. **로그 관리** 섹션을 확장하고 **AuditLogs** 또는 **SigninLogs**를 확장하여 로그 스키마를 확인합니다.
    ![감사 로그](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![로그인 로그](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Azure AD 활동 로그 쿼리

이제 작업 영역에 로그가 있으므로 이에 대해 쿼리를 실행할 수 있습니다. 예를 들어, 지난주에 사용된 애플리케이션을 가져오려면 기본 쿼리를 다음으로 바꾸고 **실행**을 선택합니다.

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

지난주에 대한 상위 감사 이벤트를 가져오려면 다음 쿼리를 사용합니다.

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Azure AD 활동 로그 데이터에 대한 경고

쿼리에 대해 경고를 설정할 수도 있습니다. 예를 들어, 지난주에 10개가 넘는 애플리케이션을 사용한 경우 경고를 구성하려면 다음을 수행합니다.

1. 작업 영역에서 **경고 설정**을 선택하여 **규칙 만들기** 페이지를 엽니다.

    ![경고 설정](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. 경고에서 만든 기본 **경고 조건**을 선택하고 기본 메트릭의 **임계값**을 10으로 업데이트합니다.

    ![경고 조건](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. 경고의 이름과 설명을 입력하고, 심각도 수준을 선택합니다. 예를 들어, **정보**로 설정할 수 있습니다.

4. 신호가 발생할 때 경고를 받을 **작업 그룹**을 선택합니다. 메일 또는 문자 메시지를 통해 팀에게 알리도록 선택하거나, 웹후크, Azure Functions 또는 논리 앱을 사용하여 작업을 자동화할 수도 있습니다. [Azure Portal에서 경고 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)에 대해 자세히 알아봅니다.

5. 경고를 구성한 후에는 **경고 만들기**를 선택하여 사용하도록 설정합니다. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Azure AD 활동 로그의 미리 빌드한 보기 설치 및 사용

Azure AD 활동 로그에서 미리 빌드된 로그 분석 보기를 다운로드할 수도 있습니다. 이 보기는 감사 및 로그인 이벤트와 관련된 일반적인 시나리오에 대한 몇 가지 보고서를 제공합니다. 이전 섹션에서 설명한 단계를 사용하여 보고서에 제공된 데이터에 대해 경고를 발생할 수도 있습니다.

* **Azure AD 계정 프로비전 이벤트**: 프로비전 활동 감사(예: 새로 프로비전된 사용자 수 및 프로비전 실패 횟수, 업데이트된 사용자 수 및 업데이트 실패 횟수, 프로비전 해제된 사용자 수 및 해당 실패 횟수)와 관련된 보고서가 표시됩니다.    
* **로그인 이벤트**: 로그인 활동 모니터링(예: 애플리케이션, 사용자, 디바이스별 로그인)과 관련된 가장 적절한 보고서와 시간 경과에 따른 로그인 수를 추적하는 요약 보기가 표시됩니다.
* **사용자 동의 수행**: 사용자 동의(예: 사용자에 의한 동의 허용, 동의한 사용자의 로그인 및 모든 동의 기반 애플리케이션에 대한 애플리케이션의 로그인)와 관련된 보고서가 표시됩니다. 

[Azure AD 활동 로그에 대한 로그 분석 보기 설치 및 사용](howto-install-use-log-analytics-views.md) 방법에 대해 알아보세요. 


## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그에서 쿼리 시작](../../azure-monitor/log-query/get-started-queries.md)
* [Azure Portal에서 경고 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)
* [Azure Active Directory용 로그 분석 보기 설치 및 사용](howto-install-use-log-analytics-views.md)