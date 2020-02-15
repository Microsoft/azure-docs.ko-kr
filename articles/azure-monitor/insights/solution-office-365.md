---
title: Azure에서 Office 365 관리 솔루션 | Microsoft Docs
description: 이 문서에서는 Azure에서 Office 365 솔루션을 구성하고 사용하는 방법에 대해 자세히 설명합니다.  또한 Azure Monitor에서 작성되는 Office 365 레코드에 대해서도 자세히 설명합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/08/2019
ms.openlocfilehash: 96d0124941f9f2daa1e1b2d1d254b0b767ffb395
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111556"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure에서 Office 365 관리 솔루션(미리 보기)

![Office 365 로고](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>솔루션 업데이트
> 이 솔루션은 [Azure 센티널](../../sentinel/overview.md) 의 [Office 365](../../sentinel/connect-office-365.md) 일반 공급 솔루션 및 [azure AD 보고 및 모니터링 솔루션](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)으로 대체 되었습니다. 이러한 기능을 함께 사용 하면 향상 된 구성 환경에서 이전 Azure Monitor Office 365 솔루션의 업데이트 된 버전을 제공 합니다. 2020 년 4 월 30 일까 지 기존 솔루션을 계속 사용할 수 있습니다.
> 
> Azure 센티널은 로그를 수집 하 고, 검색, 조사, 구하기 및 기계 학습 기반 통찰력을 비롯 한 추가 SIEM 기능을 제공 하는 클라우드 네이티브 보안 정보 및 이벤트 관리 솔루션입니다. 이제 Azure 센티널을 사용 하 여 Office 365 SharePoint 활동 및 Exchange 관리 로그 수집을 제공 합니다.
> 
> Azure AD reporting은 로그인 이벤트, 감사 이벤트 및 디렉터리 변경을 포함 하 여 사용자 환경에서 Azure AD 활동의 로그에 대 한 보다 포괄적인 보기를 제공 합니다. Azure AD 로그를 연결 하기 위해 azure [센티널 AZURE ad 커넥터](../../sentinel/connect-azure-active-directory.md) 를 사용 하거나 [AZURE MONITOR와 azure ad 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)을 구성할 수 있습니다. 
>
> Azure AD 로그의 컬렉션은 Azure Monitor 가격 책정입니다.  자세한 내용은 [가격 책정 Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) 를 참조 하세요.
>
> Azure 센티널 Office 365 솔루션을 사용 하려면 다음을 수행 합니다.
> 1. Azure 센티널에서 Office 365 커넥터를 사용 하면 작업 영역에 대 한 가격 책정에 영향을 줍니다. 자세한 내용은 [Azure 센티널 가격 책정](https://azure.microsoft.com/pricing/details/azure-sentinel/)을 참조 하세요.
> 2. Azure Monitor Office 365 솔루션을 이미 사용 하 고 있는 경우 [아래 제거 섹션](#uninstall)의 스크립트를 사용 하 여 먼저 솔루션을 제거 해야 합니다.
> 3. 작업 영역에서 [Azure 센티널 솔루션을 사용 하도록 설정](../../sentinel/quickstart-onboard.md) 합니다.
> 4. Azure 센티널의 **데이터 커넥터** 페이지로 이동 하 여 **Office 365** 커넥터를 사용 하도록 설정 합니다.
>
> ## <a name="frequently-asked-questions"></a>질문과 대답
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>Q: 현재와 4 월 30 일 사이에 Office 365 Azure Monitor 솔루션을 온보드 할 수 있나요?
> 아니요, Azure Monitor Office 365 솔루션 등록 스크립트는 더 이상 사용할 수 없습니다. 이 솔루션은 4 월 30 일에 제거 됩니다.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>Q: 테이블 및 스키마를 변경 하 시겠습니까?
> 솔루션 이름 및 스키마는 현재 솔루션에서와 **동일 하 게** 유지 됩니다. Azure AD 데이터를 참조 하는 쿼리를 제외 하 고 새 솔루션에서 동일한 쿼리를 계속 사용할 수 있습니다.
> 
> 새 [AZURE AD 보고 및 모니터링 솔루션](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) 로그는 [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) 및 [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) 테이블에 수집 **됩니다.** 자세한 내용은 azure 센티널 및 Azure Monitor 사용자와 관련 된 [AZURE AD 로그를 분석 하는 방법](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md)을 참조 하세요.
> 
> 다음은 SigninLogs **활동** 에서로 쿼리를 변환 하는 샘플입니다.
> 
> **사용자가 실패 한 로그인 쿼리:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId 
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Azure AD 작업 보기:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>Q: Azure 센티널을 온 보 보드 하려면 어떻게 하나요?
> Azure 센티널은 신규 또는 기존 Log Analytics 작업 영역에서 사용할 수 있는 솔루션입니다. 자세히 알아보려면 [Azure 센티널 온 보 딩 설명서](../../sentinel/quickstart-onboard.md)를 참조 하세요.
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>Q: azure AD 로그를 연결 하려면 Azure 센티널이 필요 한가요?
> Azure 센티널 솔루션과 관련이 없는 [Azure Monitor와 AZURE AD 로그 통합](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)을 구성할 수 있습니다. Azure 센티널은 기본 커넥터를 제공 하 고 Azure AD 로그에 대해 기본 제공 되는 콘텐츠를 제공 합니다. 자세한 내용은 기본 보안 기반 콘텐츠에서 아래 질문을 참조 하세요.
>
> ###   <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>Q: azure 센티널 및 Azure Monitor에서 Azure AD 로그를 연결할 때의 차이점은 무엇 인가요?
> Azure 센티널 및 Azure Monitor는 동일한 [AZURE ad 보고 및 모니터링 솔루션](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md)을 기반으로 azure ad 로그에 연결 합니다. Azure 센티널은 동일한 데이터를 연결 하 고 모니터링 정보를 제공 하는 한 번 클릭으로 기본 커넥터를 제공 합니다.
>
> ###   <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>Q: 새 Azure AD 보고 및 모니터링 테이블로 이동할 때 변경 해야 하는 사항은 무엇 인가요?
> 경고, 대시보드 및 Office 365 Azure AD 데이터를 사용 하 여 만든 모든 콘텐츠의 쿼리를 비롯 하 여 Azure AD 데이터를 사용 하는 모든 쿼리는 새 테이블을 사용 하 여 다시 만들어야 합니다.
>
> Azure 센티널 및 Azure AD는 Azure AD 보고 및 모니터링 솔루션으로 이동할 때 사용할 수 있는 기본 제공 콘텐츠를 제공 합니다. 자세한 내용은 기본 보안 지향 콘텐츠 및 [Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md)에 대 한 다음 질문을 참조 하세요. 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>Q: Azure 센티널 기본 보안 지향 콘텐츠를 어떻게 사용할 수 있나요?
> Azure 센티널은 Office 365 및 Azure AD 로그를 기반으로 하는 기본 보안 지향 대시보드, 사용자 지정 경고 쿼리, 구하기 쿼리, 조사 및 자동화 된 응답 기능을 제공 합니다. 자세한 내용은 Azure 센티널 GitHub 및 자습서를 살펴보세요.
>
> - [기본으로 위협 감지](../../sentinel/tutorial-detect-threats-built-in.md)
> - [의심스러운 위협을 검색하는 사용자 지정 분석 규칙 만들기](../../sentinel/tutorial-detect-threats-custom.md)
> - [데이터 모니터링](../../sentinel/tutorial-monitor-your-data.md)
> - [Azure 센티널을 사용 하 여 인시던트 조사](../../sentinel/tutorial-investigate-cases.md)
> - [Azure 센티널에서 자동화 된 위협 응답 설정](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure 센티널 GitHub 커뮤니티](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>Q: Azure 센티널은 솔루션의 일부로 추가 커넥터를 제공 하나요?
> 예, [Azure 센티널 connect 데이터 원본](../../sentinel/connect-data-sources.md)을 참조 하세요.
> 
> ###   <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>Q: 4 월 30 일에 무슨 일이 발생 하나요? 보드를 미리 등록 취소
> 
> - **Office365** 솔루션에서 데이터를 받을 수 없습니다. 이 솔루션은 Marketplace에서 더 이상 사용할 수 없습니다.
> - Azure 센티널 고객의 경우 Log Analytics 작업 영역 솔루션 **Office365** 이 Azure 센티널 **securityinsights** 솔루션에 포함 됩니다.
> - 솔루션을 수동으로 보드 오프 하지 않는 경우 데이터는 4 월 30 일에 자동으로 연결 됩니다.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>Q: 데이터를 새 솔루션으로 전송 하 시겠습니까?
> 예. 작업 영역에서 **Office 365** 솔루션을 제거 하면 스키마가 제거 되어 해당 데이터를 일시적으로 사용할 수 없게 됩니다. 센티널에서 새로운 **Office 365** 커넥터를 사용 하도록 설정 하면 스키마가 작업 영역에 복원 되 고 이미 수집 된 데이터를 사용할 수 있게 됩니다. 
 

Office 365 관리 솔루션을 사용하면 Azure Monitor에서 Office 365 환경을 모니터링할 수 있습니다.

- Office 365 계정에서 사용자 활동을 모니터링하면 사용량 패턴을 분석하고 동작 경향을 식별할 수 있습니다. 예를 들어 조직 외부나 인기 SharePoint 사이트에서 공유하는 파일과 같은 특정 사용 시나리오를 추출할 수 있습니다.
- 관리자 활동을 모니터링하면 구성 변경 내용이나 높은 권한이 필요한 작업을 추적할 수 있습니다.
- 조직 요구 사항에 따라 사용자 지정할 수 있는 부적절한 사용자 행동을 검색하고 조사할 수 있습니다.
- 감사 및 규정 준수 방식을 제시할 수 있습니다. 예를 들어 기밀 파일에 대한 파일 액세스 작업을 모니터링하여 감사 및 규정 준수 프로세스를 보다 원활하게 진행할 수 있습니다.
- 조직의 Office 365 활동 데이터를 토대로 [로그 쿼리](../log-query/log-query-overview.md) 기능을 사용해 운영상의 문제를 해결할 수 있습니다.


## <a name="uninstall"></a>제거

[관리 솔루션을 제거](solutions.md#remove-a-monitoring-solution)의 프로세스를 사용하여 Office 365 관리 솔루션을 제거할 수 있습니다. 이렇게 해도 Office 365에서 Azure Monitor로 데이터 수집이 중단되지 않습니다. 아래 절차에 따라 Office 365에서 구독을 취소하고 데이터 수집을 중지합니다.

1. 다음 스크립트를 *office365_unsubscribe.ps1*로 저장합니다.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. 다음 명령을 사용하여 스크립트를 실행합니다.

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    예제:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

자격 증명을 입력 하 라는 메시지가 표시 됩니다. Log Analytics 작업 영역에 대 한 자격 증명을 제공 합니다.

## <a name="data-collection"></a>데이터 수집

데이터가 처음으로 수집될 때까지 몇 시간이 걸릴 수 있습니다. 데이터 수집이 시작되면 Office 365는 레코드가 생성될 때마다 상세 데이터가 포함된 [webhook 알림](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications)을 Azure Monitor로 보냅니다. 이 레코드는 수신된 후 몇 분 안에 Azure Monitor에서 사용할 수 있습니다.

## <a name="using-the-solution"></a>솔루션 사용

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Office 365 솔루션을 Log Analytics 작업 영역에 추가하면 대시보드에 **Office 365** 타일이 추가됩니다. 이 타일은 현재 환경의 컴퓨터 수 및 그래픽 표현과 업데이트 준수를 표시합니다.<br><br>
![Office 365 요약 타일](media/solution-office-365/tile.png)  

**Office 365** 타일을 클릭하여 **Office 365** 대시보드를 엽니다.

![Office 365 대시보드](media/solution-office-365/dashboard.png)  

대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 경고를 나열합니다. 열 아래쪽의 모두 보기를 클릭하거나 열 머리글을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | Description |
|:--|:--|
| 작업 | 모니터링되는 모든 Office 365 구독의 활성 사용자에 대한 정보를 제공합니다. 시간에 따라 발생하는 활동의 수도 확인할 수 있습니다.
| Exchange | Add-MailboxPermission, Set-Mailbox 등의 Exchange Server 활동이 자세히 구분되어 표시됩니다. |
| SharePoint | 사용자가 SharePoint 문서에 대해 가장 많이 수행하는 활동이 표시됩니다. 이 타일에서 드릴다운하면 검색 페이지에 이 활동의 위치와 대상 문서 등 이러한 활동의 세부 정보가 표시됩니다. 예를 들어 파일 액세스 이벤트의 경우 액세스한 문서, 연결된 계정 이름 및 IP 주소를 확인할 수 있습니다. |
| Azure Active Directory | 사용자 암호 다시 설정, 로그인 시도 등 자주 수행하는 사용자 활동이 포함됩니다. 드릴다운하면 결과 상태와 같은 이러한 활동의 세부 정보를 확인할 수 있습니다. 이러한 정보는 대개 Azure Active Directory에서 의심스러운 활동을 모니터링하려는 경우에 유용합니다. |




## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드

Office 365 솔루션이 Azure Monitor의 Log Analytics 작업 영역에서 생성하는 모든 레코드의 **Type**은 **OfficeActivity**입니다.  **OfficeWorkload** 속성에 따라 해당 레코드가 참조하는 Office 365 서비스(Exchange, AzureActiveDirectory, SharePoint 또는 OneDrive)가 결정됩니다.  **RecordType** 속성은 작업의 유형을 지정합니다.  속성은 각 작업 유형에 따라 달라지며, 아래 표에 나와 있습니다.

### <a name="common-properties"></a>공용 속성

다음 속성은 모든 Office 365 레코드에 공통적으로 적용됩니다.

| 속성 | Description |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | 활동이 기록될 때 사용된 디바이스의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| OfficeWorkload | 레코드가 참조하는 office 365 서비스입니다.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 작업(Operation) | 사용자 또는 관리자 활동의 이름입니다.  |
| OrganizationId | 조직의 Office 365 테넌트 GUID입니다. 이 값은 값이 나타나는 Office 365 서비스에 관계없이 조직에서 항상 동일하게 유지됩니다. |
| RecordType | 수행한 작업의 유형입니다. |
| ResultStatus | Operation 속성에 지정된 작업이 성공했는지 여부를 나타냅니다. 가능한 값은 Succeeded, PartiallySucceeded 또는 Failed입니다. Exchange 관리자 활동의 경우 값은 True 또는 False입니다. |
| UserId | 레코드가 기록된 원인인 작업을 수행한 사용자의 UPN(사용자 계정 이름)입니다. 예를 들면 my_name@my_domain_name과 같습니다. SHAREPOINT\system 또는 NTAUTHORITY\SYSTEM과 같은 시스템 계정이 수행한 활동에 대한 레코드도 포함됩니다. | 
| UserKey | UserId 속성에 나와 있는 사용자의 대체 ID입니다.  예를 들어 SharePoint, 비즈니스용 OneDrive 및 Exchange에서 사용자가 수행한 이벤트의 경우에는 이 속성에 PUID(Passport 고유 ID)가 입력됩니다. 시스템 계정이 수행한 이벤트와 기타 서비스에서 발생하는 이벤트의 경우 이 속성이 UserID 속성과 같은 값을 지정할 수도 있습니다.|
| UserType | 작업을 수행한 사용자의 유형입니다.<br><br>Admin<br>애플리케이션<br>DcAdmin<br>일반<br>Reserved<br>ServicePrincipal<br>시스템 |


### <a name="azure-active-directory-base"></a>Azure Active Directory 기본 속성

다음 속성은 모든 Azure Active Directory 레코드에 공통적으로 적용됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 이벤트의 유형입니다. |
| ExtendedProperties | Azure AD 이벤트의 확장 속성입니다. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 계정 로그온

다음 레코드는 Active Directory 사용자가 로그온을 시도할 때 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Office 15 등의 계정 로그인 이벤트를 트리거하는 애플리케이션입니다. |
| `Client` | 계정 로그인 이벤트에 사용된 클라이언트 디바이스, 디바이스 OS 및 디바이스 브라우저에 대한 세부 정보입니다. |
| `LoginStatus` | OrgIdLogon.LoginStatus에서 직접 생성되는 속성입니다. 알림 알고리즘을 사용하면 로그온 실패를 다양한 방식으로 매핑할 수 있습니다. |
| `UserDomain` | TII(테넌트 ID 정보)입니다. | 


### <a name="azure-active-directory"></a>Azure Active Directory

이러한 레코드는 Azure Active Directory 개체를 추가하거나 변경할 때 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Operation 속성에서 식별할 수 있는 작업이 수행된 대상 사용자입니다. |
| 행위자 | 작업을 수행한 사용자 또는 서비스 주체입니다. |
| ActorContextId | 작업자가 속한 조직의 GUID입니다. |
| ActorIpAddress | 작업자의 IP 주소(IPV4 또는 IPV6 주소 형식)입니다. |
| InterSystemsId | Office 365 서비스 내에서 구성 요소에 대한 작업을 추적하는 GUID입니다. |
| IntraSystemId |   작업을 추적하기 위해 Azure Active Directory에서 생성하는 GUID입니다. |
| SupportTicketId | "대신 작업"을 수행하는 상황에서 작업의 고객 지원 티켓 ID입니다. |
| TargetContextId | 대상 사용자가 속한 조직의 GUID입니다. |


### <a name="data-center-security"></a>데이터 센터 보안

다음 레코드는 데이터 센터 보안 감사 데이터에서 생성됩니다.  

| 속성 | Description |
|:--- |:--- |
| EffectiveOrganization | 권한 상승/cmdlet의 대상 테넌트 이름입니다. |
| ElevationApprovedTime | 권한 상승이 승인된 시간에 해당하는 타임스탬프입니다. |
| ElevationApprover | Microsoft 관리자의 이름입니다. |
| ElevationDuration | 권한 상승이 활성 상태였던 기간입니다. |
| ElevationRequestId |  권한 상승 요청의 고유 식별자입니다. |
| ElevationRole | 권한 상승이 요청된 역할입니다. |
| ElevationTime | 권한 상승의 시작 시간입니다. |
| Start_Time | cmdlet 실행의 시작 시간입니다. |


### <a name="exchange-admin"></a>Exchange 관리

다음 레코드는 Exchange 구성을 변경하면 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  cmdlet을 실행한 주체(조직 내 사용자, Microsoft 데이터 센터 담당자 또는 데이터 센터 서비스 계정, 위임된 관리자)를 지정합니다. 값이 False이면 조직 내 사용자가 cmdlet을 실행한 것입니다. 값이 True이면 데이터 센터 담당자, 데이터 센터 서비스 계정 또는 위임된 관리자가 cmdlet을 실행한 것입니다. |
| ModifiedObjectResolvedName |  cmdlet에 의해 수정된 개체의 사용자에게 친숙한 이름입니다. cmdlet이 개체를 수정하는 경우에만 이 속성이 기록됩니다. |
| OrganizationName | 테넌트의 이름입니다. |
| OriginatingServer | cmdlet이 실행된 서버의 이름입니다. |
| 매개 변수 | Operations 속성에 나와 있는 cmdlet과 함께 사용된 모든 매개 변수의 이름과 값입니다. |


### <a name="exchange-mailbox"></a>Exchange 사서함

다음 레코드는 Exchange 사서함을 변경하거나 추가하면 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 브라우저 버전, Outlook 버전, 모바일 디바이스 정보 등 작업을 수행하는 데 사용된 전자 메일 클라이언트에 대한 정보입니다. |
| Client_IPAddress | 작업이 기록될 때 사용된 디바이스의 IP 주소입니다. IP 주소는 IPv4 또는 IPv6 주소 형식으로 표시됩니다. |
| ClientMachineName | Outlook 클라이언트를 호스트하는 컴퓨터 이름입니다. |
| ClientProcessName | 사서함에 액세스하는 데 사용된 전자 메일 클라이언트입니다. |
| ClientVersion | 전자 메일 클라이언트의 버전입니다. |
| InternalLogonType | 내부용으로 예약된 속성입니다. |
| Logon_Type | 사서함에 액세스하여 기록된 작업을 수행한 사용자의 유형을 나타냅니다. |
| LogonUserDisplayName |    작업을 수행한 사용자의 사용자에게 친숙한 이름입니다. |
| LogonUserSid | 작업을 수행한 사용자의 SID입니다. |
| MailboxGuid | 액세스한 사서함의 Exchange GUID입니다. |
| MailboxOwnerMasterAccountSid | 사서함 소유자 계정의 마스터 계정 SID입니다. |
| MailboxOwnerSid | 사서함 소유자의 SID입니다. |
| MailboxOwnerUPN | 액세스한 사서함의 소유자 전자 메일 주소입니다. |


### <a name="exchange-mailbox-audit"></a>Exchange 사서함 감사

다음 레코드는 사서함 감사 항목을 만들면 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| 항목 | 작업을 수행한 항목을 나타냅니다. | 
| SendAsUserMailboxGuid | 전자 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendAsUserSmtp | 가장하는 사용자의 SMTP 주소입니다. |
| SendonBehalfOfUserMailboxGuid | 대신 메일을 보내기 위해 액세스한 사서함의 Exchange GUID입니다. |
| SendOnBehalfOfUserSmtp | 대신 전자 메일을 보낸 사용자의 SMTP 주소입니다. |


### <a name="exchange-mailbox-audit-group"></a>Exchange 사서함 감사 그룹

다음 레코드는 Exchange 그룹을 변경하거나 추가하면 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 그룹의 각 항목에 대한 정보입니다. |
| CrossMailboxOperations | 두 개 이상의 사서함에 대해 작업이 수행되었는지를 나타냅니다. |
| DestMailboxId | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 GUID를 지정합니다. |
| DestMailboxOwnerMasterAccountSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 마스터 계정 SID에 대한 SID를 지정합니다. |
| DestMailboxOwnerSid | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함의 SID를 지정합니다. |
| DestMailboxOwnerUPN | CrossMailboxOperations 매개 변수가 True인 경우에만 설정됩니다. 대상 사서함 소유자의 UPN을 지정합니다. |
| DestFolder | 이동 등의 작업에 대한 대상 폴더입니다. |
| 폴더 | 항목 그룹이 있는 폴더입니다. |
| 폴더 |     폴더를 선택한 후 삭제하는 등의 작업에서 사용된 원본 폴더에 대한 정보입니다. |


### <a name="sharepoint-base"></a>Sharepoint 기본 속성

다음 속성은 모든 SharePoint 레코드에 공통적으로 적용됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | SharePoint에서 이벤트가 발생했음을 나타냅니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| ItemType | 액세스했거나 수정한 개체의 형식입니다. 개체 형식에 대한 자세한 내용은 ItemType 표를 참조하세요. |
| MachineDomainInfo | 디바이스 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| MachineId |   디바이스 동기화 작업에 대한 정보입니다. 이 정보는 요청에 있는 경우에만 보고됩니다. |
| Site_ | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 GUID입니다. |
| Source_Name | 감사된 작업을 트리거한 엔터티입니다. 가능한 값은 SharePoint 또는 ObjectModel입니다. |
| UserAgent | 사용자의 클라이언트 또는 브라우저에 대한 정보입니다. 이 정보는 클라이언트 또는 브라우저에서 제공됩니다. |


### <a name="sharepoint-schema"></a>SharePoint 스키마

다음 레코드는 SharePoint 구성을 변경하면 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 사용자 지정 이벤트에 대한 선택적 문자열입니다. |
| Event_Data |  사용자 지정 이벤트에 대한 선택적 페이로드입니다. |
| ModifiedProperties | 이 속성은 사이트 또는 사이트 모음 관리 그룹의 멤버로 사용자를 추가하는 등의 관리 이벤트에 대해 포함됩니다. 이 속성은 수정된 속성의 이름(예: 사이트 관리자 그룹), 수정된 속성의 새 값(예: 사이트 관리자로 추가된 사용자), 그리고 수정된 개체의 이전 값을 포함합니다. |


### <a name="sharepoint-file-operations"></a>SharePoint 파일 작업

다음 레코드는 SharePoint의 파일 작업에 대한 응답으로 생성됩니다.

| 속성 | Description |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 복사하거나 이동한 파일의 파일 확장명입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationFileName | 복사하거나 이동한 파일의 이름입니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| DestinationRelativeUrl | 파일을 복사하거나 이동한 대상 폴더의 URL입니다. SiteURL, DestinationRelativeURL 및 DestinationFileName 매개 변수의 값 조합은 복사한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. 이 속성은 FileCopied 및 FileMoved 이벤트에 대해서만 표시됩니다. |
| SharingType | 리소스를 공유한 사용자에게 할당된 공유 권한 유형입니다. UserSharedWith 매개 변수를 통해 이 사용자를 식별합니다. |
| Site_Url | 사용자가 액세스한 파일 또는 폴더가 있는 사이트의 URL입니다. |
| SourceFileExtension | 사용자가 액세스한 파일의 파일 확장명입니다. 액세스한 개체가 폴더이면 이 속성은 비어 있습니다. |
| SourceFileName |  사용자가 액세스한 파일 또는 폴더의 이름입니다. |
| SourceRelativeUrl | 사용자가 액세스한 파일이 포함된 폴더의 URL입니다. SiteURL, SourceRelativeURL 및 SourceFileName 매개 변수의 값 조합은 사용자가 액세스한 파일의 전체 경로 이름인 ObjectID 속성의 값과 같습니다. |
| UserSharedWith |  리소스를 공유한 사용자입니다. |




## <a name="sample-log-queries"></a>샘플 로그 쿼리

다음 표에서는이 솔루션에서 수집 된 업데이트 레코드에 대 한 샘플 로그 쿼리를 제공 합니다.

| 쿼리 | Description |
| --- | --- |
|Office 365 구독의 모든 작업 수 |OfficeActivity &#124; summarize count() by Operation |
|SharePoint 사이트 사용량|&#124; \| SiteUrl 작업 = ~ "sharepoint" &#124; 는 count ()를 기준으로 count ()를 요약 합니다.|
|사용자 유형별 파일 액세스 작업 | UserType 활동 &#124; 요약 개수 () |
|Exchange에서 외부 작업 모니터링|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>다음 단계

* [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)를 사용하여 자세한 업데이트 데이터를 확인합니다.
* [고유한 대시보드 만들기](../learn/tutorial-logs-dashboards.md)를 수행하여 자주 사용하는 Office 365 검색 쿼리를 표시합니다.
* 중요한 Office 365 활동에 대해 미리 알림을 받을 수 있도록 [경고 만들기](../platform/alerts-overview.md)를 수행합니다.  
