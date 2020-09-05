---
title: Azure Monitor를 사용한 보관 및 보고 - Azure AD 권한 관리
description: Azure Active Directory 권한 관리에서 Azure Monitor를 사용하여 로그를 보관하고 보고서를 만드는 방법을 알아보세요.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89d6379f3fa41036836288ed5c75fbdaad0031da
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783826"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Azure Monitor에서 Azure AD 권한 관리에 대한 로그 및 보고 보관

Azure AD는 감사 로그에 최대 30일 동안 감사 이벤트를 저장합니다. 그러나 Azure Storage 계정에 라우팅하거나 Azure Monitor를 사용하면 [Azure AD에서 보고 데이터를 저장하는 기간](../reports-monitoring/reference-reports-data-retention.md)에 설명된 기본 보존 기간보다 오랫동안 감사 데이터를 유지할 수 있습니다. 그러면 이 데이터에 대한 보고서와 사용자 지정 쿼리 및 통합 문서를 사용할 수 있습니다.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure Monitor를 사용하도록 Azure AD 구성
Azure Monitor 통합 문서를 사용하기 전에 감사 로그의 복사본을 Azure Monitor로 보내도록 Azure AD를 구성해야 합니다.

Azure AD 감사 로그를 보관하려면 Azure 구독에 Azure Monitor가 포함되어 있어야 합니다. Azure Monitor 사용 시 필수 구성 요소 및 예상 비용에 대한 자세한 내용은 [Azure Monitor의 Azure AD 활동 로그](../reports-monitoring/concept-activity-logs-azure-monitor.md)를 참조하세요.

**필수 역할**: 전역 관리자

1. 글로벌 관리자인 사용자로서 Azure Portal에 로그인합니다. Azure Monitor 작업 영역을 포함하는 리소스 그룹에 액세스할 수 있는지 확인합니다.
 
1. **Azure Active Directory**를 선택한 다음 왼쪽 탐색 메뉴의 모니터링에서 **진단 설정**을 클릭합니다. 감사 로그를 해당 작업 영역으로 보내기 위한 설정이 이미 있는지 확인합니다.

1. 설정이 아직 없는 경우 **진단 설정 추가**를 클릭합니다. [Azure AD 로그를 Azure Monitor 로그와 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) 문서의 지침을 사용하여 Azure AD 감사 로그를 Azure Monitor 작업 영역으로 보냅니다.

    ![진단 설정 창](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. 로그를 Azure Monitor에 보낸 후 **Log Analytics 작업 영역**을 선택하고 Azure AD 감사 로그가 포함된 작업 영역을 선택합니다.

1. **사용량 및 예상 비용**을 선택하고 **데이터 보존**을 클릭합니다. 감사 요구 사항을 충족할 수 있도록 데이터를 유지할 기간(일)으로 슬라이더를 변경합니다.

    ![Log Analytics 작업 영역 창](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. 나중에 작업 영역에 유지되는 날짜 범위를 확인하려면 *보관된 로그 날짜 범위* 통합 문서를 사용하면 됩니다.  
    
    1. **Azure Active Directory**를 선택한 다음 **통합 문서**를 클릭합니다. 
    
    1. **Azure Active Directory 문제 해결** 섹션을 확장하고 **보관된 로그 날짜 범위**를 클릭합니다. 


## <a name="view-events-for-an-access-package"></a>액세스 패키지에 대한 이벤트 보기  

액세스 패키지에 대한 이벤트를 보려면 기본 Azure Monitor 작업 영역에 대한 액세스 권한(정보는 [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) 참조)과 다음 역할 중 하나를 보유하고 있어야 합니다. 

- 전역 관리자  
- 보안 관리자  
- 보안 판독기  
- 보고서 읽기 권한자  
- 애플리케이션 관리자  

이벤트를 보려면 다음 절차를 따릅니다. 

1. Azure Portal에서 **Azure Active Directory**를 선택한 다음 **통합 문서**를 클릭합니다. 구독이 하나만 있는 경우 3단계로 이동 합니다. 

1. 구독이 여러 개 있는 경우 작업 영역이 포함된 구독을 선택합니다.  

1. *액세스 패키지 작업*이라는 통합 문서를 선택합니다. 

1. 해당 통합 문서에서 시간 범위를 선택(확실하지 않은 경우 **모두**로 변경)하고 해당 시간 범위 중에 작업이 포함된 모든 액세스 패키지의 드롭다운 목록에서 액세스 패키지 ID를 선택합니다. 선택한 시간 범위 중에 발생한 액세스 패키지와 관련 있는 이벤트가 표시됩니다.  

    ![액세스 패키지 이벤트 보기](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    각 행에는 시간, 액세스 패키지 ID, 작업 이름, 개체 ID, UPN 및 작업을 시작한 사용자의 표시 이름이 포함됩니다.  추가 세부 정보는 JSON에 포함되어 있습니다.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Azure Portal을 이용해 사용자 지정 Azure Monitor 쿼리 만들기
권한 관리 이벤트를 포함하여 Azure AD 감사 이벤트에 대한 고유 쿼리를 만들 수 있습니다.  

1. Azure Portal의 Azure Active Directory에서 왼쪽 탐색 메뉴의 모니터링 섹션에 있는 **로그**를 클릭하여 새 쿼리 페이지를 만듭니다.

1. 작업 영역이 쿼리 페이지의 왼쪽 위에 표시되어야 합니다. Azure Monitor 작업 영역이 여러 개 있고 Azure AD 감사 이벤트를 저장하는 데 사용하는 작업 영역이 표시되지 않는 경우 **범위 선택**을 클릭합니다. 그런 다음 올바른 구독 및 작업 영역을 선택합니다.

1. 다음으로 쿼리 텍스트 영역에서 "검색 *" 문자열을 삭제하고 다음 쿼리로 바꿉니다.

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. 그런 다음 **실행**을 클릭합니다. 

    ![실행을 클릭하여 쿼리 시작](./media/entitlement-management-logs-and-reporting/run-query.png)

테이블에는 기본적으로 지난 1시간 동안의 권한 관리에 대한 감사 로그 이벤트가 표시됩니다. "시간 범위" 설정을 변경하여 이전 이벤트를 볼 수 있습니다. 그러나 이 설정을 변경하면 Azure AD가 Azure Monitor에 이벤트를 보내도록 구성된 후에 발생한 이벤트만 표시됩니다.

Azure Monitor에 유지되는 최고/최신 감사 이벤트를 알아보려면 다음 쿼리를 사용합니다.

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Azure Monitor의 감사 이벤트에 저장된 열에 대한 자세한 내용은 [Azure Monitor에서 Azure AD 감사 로그 스키마 해석](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)을 참조하세요.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Azure PowerShell을 이용해 사용자 지정 Azure Monitor 쿼리 만들기

Azure Monitor에 로그를 보내도록 Azure AD를 구성한 후에는 PowerShell을 통해 로그에 액세스할 수 있습니다. 그러면 테넌트의 전역 관리자가 되지 않고도 스크립트나 PowerShell 명령줄에서 쿼리를 보냅니다. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>사용자 또는 서비스 주체에게 올바른 역할이 할당되어 있는지 확인

Azure AD를 인증할 사용자 또는 서비스 주체가 Log Analytics 작업 영역에 적절한 Azure 역할을 맡고 있는지 확인합니다. 역할 옵션은 Log Analytics 독자 또는 Log Analytics 참가자 중 하나입니다. 이러한 역할 중 한 가지를 이미 맡고 있는 경우 [하나의 Azure 구독을 사용하여 Log Analytics ID 검색](#retrieve-log-analytics-id-with-one-azure-subscription)으로 건너뜁니다.

역할 할당을 설정하고 쿼리를 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 [Log Analytics 작업 영역](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)을 찾습니다.

1. **Access Control(IAM)** 을 선택합니다.

1. 그런 다음 **추가**를 클릭하여 역할 할당을 추가합니다.

    ![역할 할당 추가](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell 모듈 설치

적절한 역할 할당을 보유하고 있으면 PowerShell을 시작하고 다음을 입력하여 [Azure PowerShell 모듈을 설치](/powershell/azure/install-az-ps?view=azps-3.3.0)(아직 없는 경우)합니다.

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
이제 Azure AD를 인증하고, 쿼리 중인 Log Analytics 작업 영역의 ID를 검색할 준비가 되었습니다.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>하나의 Azure 구독을 사용하여 Log Analytics ID 검색
Azure 구독과 Log Analytics 작업 영역이 하나씩만 있는 경우 다음을 입력하여 Azure AD를 인증하고, 해당 구독에 연결하며, 해당 작업 영역을 검색합니다.
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>여러 Azure 구독을 사용하여 Log Analytics ID 검색

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)는 한 번에 하나의 구독에서 작동합니다. 따라서 Azure 구독을 여러 개 보유하고 있는 경우 Azure AD 로그를 사용하여 Log Analytics 작업 영역이 있는 구독에 연결하는지 확인할 수 있습니다. 
 
 다음 cmdlet은 구독 목록을 표시하고 Log Analytics 작업 영역이 있는 구독의 ID를 찾습니다.
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
`Connect-AzAccount –Subscription $subs[0].id` 같은 명령을 사용하여 해당 구독에 대한 PowerShell 세션을 다시 인증하고 연결할 수 있습니다. 비대화형 기능을 포함하여 PowerShell에서 Azure를 인증하는 방법에 대해 자세히 알아보려면 [Azure PowerShell을 사용하여 로그인](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
)을 참조하세요.

해당 구독에 Log Analytics 작업 영역이 여러 개 있는 경우 cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace)에서 작업 영역의 목록을 반환합니다. 그러면 Azure AD 로그가 포함된 작업 영역을 찾을 수 있습니다. 이 cmdlet이 반환하는 `CustomerId` 필드는 Log Analytics 작업 영역 개요의 Azure Portal에 표시되는 "작업 영역 ID"의 값과 동일합니다.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Log Analytics 작업 영역에 쿼리 보내기
마지막으로, 작업 영역을 확인한 후 [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
)를 사용하여 해당 작업 영역에 Kusto 쿼리를 보낼 수 있습니다. 이러한 쿼리는 [Kusto 쿼리 언어](/azure/kusto/query/)로 작성됩니다.
 
예를 들면, 다음과 같은 쿼리를 보내는 데 PowerShell cmdlet을 사용하여 Log Analytics 작업 영역에서 감사 이벤트 레코드의 날짜 범위를 검색할 수 있습니다.
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

또한 다음과 같은 쿼리를 사용해 권한 관리 이벤트도 검색할 수 있습니다.

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>다음 단계:
- [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../../azure-monitor/platform/workbooks-overview.md)