---
title: Azure Analysis Services에 대한 진단 로깅 | Microsoft Docs
description: Azure Analysis Services 서버를 모니터링 하기 위해 Azure 리소스 진단 로깅을 설정 하는 방법을 설명 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 05ba1d97d4eba92f492289375f85425f8920510b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749755"
---
# <a name="setup-diagnostic-logging"></a>진단 로깅 설정

Analysis Services 솔루션의 중요한 기능은 서버가 작동하는 방법을 모니터링하는 것입니다. [Azure 리소스 진단 로그](../azure-monitor/platform/platform-logs-overview.md)에서 로그를 모니터링하여 [Azure Storage](https://azure.microsoft.com/services/storage/)로 전송하고, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)로 스트리밍하고, [Azure Monitor 로그](../azure-monitor/azure-monitor-log-hub.md)로 내보낼 수 있습니다.

![Storage, Event Hubs 또는 Azure Monitor 로그에 진단 로깅](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>다음이 로깅됩니다.

**엔진**, **서비스** 및 **메트릭** 범주를 선택할 수 있습니다.

### <a name="engine"></a>엔진

**엔진**을 선택하면 모든 [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events)를 기록합니다. 개별 이벤트를 선택할 수 없습니다. 

|XEvent 범주 |이벤트 이름  |
|---------|---------|
|보안 감사    |   로그인 감사      |
|보안 감사    |   로그아웃 감사      |
|보안 감사    |   서버 시작 및 중지 감사      |
|진행률 보고서     |   진행률 보고 시작      |
|진행률 보고서     |   진행률 보고 종료      |
|진행률 보고서     |   진행률 보고 현재      |
|쿼리     |  쿼리 시작       |
|쿼리     |   쿼리 종료      |
|명령     |  명령 시작       |
|명령     |  명령 종료       |
|오류 및 경고     |   오류      |
|검색     |   검색 종료      |
|알림     |    알림     |
|Session     |  세션 초기화       |
|잠금    |  교착 상태       |
|쿼리 처리     |   VertiPaq SE 쿼리 시작      |
|쿼리 처리     |   VertiPaq SE 쿼리 종료      |
|쿼리 처리     |   VertiPaq SE 쿼리 캐시 일치      |
|쿼리 처리     |   직접 쿼리 시작      |
|쿼리 처리     |  직접 쿼리 종료       |

### <a name="service"></a>서비스

|작업 이름  |발생 시기  |
|---------|---------|
|ResumeServer     |    서버 다시 시작     |
|SuspendServer    |   서버 일시 중지      |
|DeleteServer     |    서버 삭제     |
|RestartServer    |     사용자가 SSMS 또는 PowerShell을 통해 서버 다시 시작    |
|GetServerLogFiles    |    사용자가 PowerShell을 통해 서버 로그 내보내기     |
|ExportModel     |   사용자가 Visual Studio에서 열기를 사용하여 포털에서 모델 내보내기     |

### <a name="all-metrics"></a>모든 메트릭

메트릭 범주는 동일한 [서버 메트릭을](analysis-services-monitor.md#server-metrics) azuremetrics 테이블에 기록 합니다. 쿼리 [확장](analysis-services-scale-out.md) 을 사용 하 고 각 읽기 복제본에 대해 메트릭을 분리 해야 하는 경우 azurediagnostics 테이블을 대신 사용 합니다. 여기서 **OperationName** 은 **logmetric**과 같습니다.

## <a name="setup-diagnostics-logging"></a>진단 로깅 설정

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com) > 서버의 왼쪽 탐색 영역에서 **진단 로그**를 클릭한 다음 **진단 로그 켜기**를 클릭합니다.

    ![Azure Portal에서 Azure Cosmos DB에 대한 진단 로깅 설정](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. **진단 설정**에서 다음 옵션을 지정합니다. 

    * **이름**. 만들 로그에 대한 이름을 입력합니다.

    * **스토리지 계정에 보관**. 이 옵션을 사용하려면 연결할 기존 스토리지 계정이 필요합니다. [스토리지 계정 만들기](../storage/common/storage-create-storage-account.md)를 참조하세요. 지침에 따라 리소스 관리자와 범용 계정을 만든 다음 포털에서 이 페이지로 돌아가 해당 스토리지 계정을 선택합니다. 새로 만든 스토리지 계정이 드롭다운 메뉴에 나타나기까지 몇 분 정도 걸릴 수 있습니다.
    * **이벤트 허브로 스트림**. 이 옵션을 사용하려면 연결할 기존 Event Hub 네임스페이스 및 이벤트 허브가 필요합니다. 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요. 그런 다음 포털의 이 페이지로 돌아가 Event Hub 네임스페이스 및 정책 이름을 선택합니다.
    * **Azure Monitor(Log Analytics 작업 영역)로 보내기**. 이 옵션을 사용하려면 기존 작업 영역을 사용하거나, 포털에서 [새 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md) 리소스를 사용합니다. 로그를 보는 방법에 대한 자세한 내용은 이 문서의 [Log Analytics 작업 영역에서 로그 보기](#view-logs-in-log-analytics-workspace)를 참조하세요.

    * **엔진**. xEvents를 기록하려면 이 옵션을 선택합니다. 스토리지 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **서비스**. 서비스 수준 이벤트를 기록하려면 이 옵션을 선택합니다. 스토리지 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **메트릭**. [Metrics](analysis-services-monitor.md#server-metrics)에 자세한 데이터를 저장하려면 이 옵션을 선택합니다. 스토리지 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.

3. **저장**을 클릭합니다.

    “\<작업 영역 이름>의 진단을 업데이트하지 못했습니다. \<subscription id> 구독은 microsoft.insights를 사용하도록 등록되지 않았습니다.” 오류를 수신하는 경우 [Azure Diagnostics 문제 해결](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) 지침을 따라 계정을 등록한 다음, 이 절차를 다시 시도합니다.

    나중에 진단 로그를 저장하는 방법을 변경하려면 이 페이지로 돌아와서 설정을 수정할 수 있습니다.

### <a name="powershell"></a>PowerShell

계속할 기본 명령은 다음과 같습니다. PowerShell을 사용하여 스토리지 계정에 대한 로깅을 설정하는 방법에 대한 단계별 도움말을 보려면 이 문서의 뒷부분에 나오는 자습서를 참조하세요.

PowerShell을 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- 스토리지 계정에서 진단 로그의 스토리지를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   스토리지 계정 ID는 로그를 보낼 스토리지 계정에 대한 리소스 ID입니다.

- 이벤트 허브로의 진단 로그 스트리밍을 활성화하려면 다음 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus 규칙 ID는 다음 형식의 문자열입니다.

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 이 명령을 사용합니다.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 다음 명령을 사용하여 Log Analytics 작업 영역의 리소스 ID를 가져올 수 있습니다.

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

### <a name="rest-api"></a>REST API

[Azure Monitor REST API를 사용하여 진단 설정을 변경](https://docs.microsoft.com/rest/api/monitor/)하는 방법을 알아봅니다. 

### <a name="resource-manager-template"></a>Resource Manager 템플릿

[Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정을 활성화](../azure-monitor/platform/diagnostic-settings-template.md)하는 방법을 알아봅니다. 

## <a name="manage-your-logs"></a>로그 관리

로그는 일반적으로 로깅을 설정하는 몇 시간 내에 사용할 수 있습니다. 스토리지 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.
* 이전 로그가 스토리지 계정에서 삭제되도록 보존 기간을 설정해야 합니다.

## <a name="view-logs-in-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 보기

메트릭 및 서버 이벤트는 병렬 분석을 위해 Log Analytics 작업 영역 리소스의 xEvents와 통합됩니다. 다른 Azure 서비스에서 이벤트를 수신하여 아키텍처의 진단 로깅 데이터를 전체적으로 표시하도록 Log Analytics 작업 영역을 구성할 수도 있습니다.

진단 데이터를 보려면 Log Analytics 작업 영역의 왼쪽 메뉴에서 **로그**를 엽니다.

![Azure Portal에서 로그 검색 옵션](./media/analysis-services-logging/aas-logging-open-log-search.png)

쿼리 작성기에서 **LogManagement** > **AzureDiagnostics**를 확장합니다. AzureDiagnostics에는 엔진 및 서비스 이벤트가 포함됩니다. 쿼리는 즉석에서 생성됩니다. EventClass\_의 필드에는 xEvent 이름이 포함됩니다. 온-프레미스 로깅에 xEvents를 사용한 경우 익숙해 보일 수 있습니다. **EventClass\_s** 또는 이벤트 이름 중 하나를 클릭하면 Log Analytics 작업 영역이 계속 쿼리를 생성합니다. 나중에 다시 사용하도록 쿼리를 저장해야 합니다.

### <a name="example-queries"></a>쿼리 예

#### <a name="example-1"></a>예 1

다음 쿼리는 모델 데이터베이스 및 서버에 대한 각 쿼리 종료/새로 고침 종료 이벤트에 대한 기간을 반환 합니다. 스케일 아웃 하는 경우 복제본 번호가 ServerName_s에 포함 되기 때문에 복제본을 통해 결과가 구분 됩니다. RootActivityId_g 그룹화 하면 Azure 진단 REST API에서 검색 되는 행 수를 줄이고 [Log Analytics Rate limits](https://dev.loganalytics.io/documentation/Using-the-API/Limits)에 설명 된 대로 제한 내에서 유지 하는 데 도움이 됩니다.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>예제 2

다음 쿼리는 서버에 대한 메모리 및 QPU 소비량을 반환 합니다. 스케일 아웃 하는 경우 복제본 번호가 ServerName_s에 포함 되기 때문에 복제본을 통해 결과가 구분 됩니다.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>예제 3

다음 쿼리는 서버에 대한 행 읽기/초 Analysis Services 엔진 성능 카운터를 반환 합니다.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

수백 개의 쿼리를 사용할 수 있습니다. 쿼리에 대한 자세한 내용은 [Azure Monitor 로그 쿼리 시작](../azure-monitor/log-query/get-started-queries.md)을 참조하세요.


## <a name="turn-on-logging-by-using-powershell"></a>PowerShell을 사용하여 로깅 켜기

이 빠른 자습서에서는 Analysis Service 서버와 동일한 구독 및 리소스 그룹에서 스토리지 계정을 만듭니다. 그런 다음 AzDiagnosticSetting를 사용 하 여 진단 로깅을 설정 하 고 출력을 새 저장소 계정으로 보냅니다.

### <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 리소스가 필요합니다.

* 기존 Azure Analysis Services 서버 서버 리소스를 만드는 방법에 대한 지침은 [Azure Portal에서 서버 만들기](analysis-services-create-server.md) 또는 [PowerShell을 사용하여 Azure Analysis Services 서버 만들기](analysis-services-create-powershell.md)를 참조하세요.

### <a name="aconnect-to-your-subscriptions"></a></a>구독에 연결

Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.  

```powershell
Connect-AzAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 가져와서 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Azure Key Vault을 만드는 데 사용된 특정된 하나를 지정해야 합니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzSubscription
```

그런 다음 로깅하려는 Azure Analysis Services 계정과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 사용자 계정에 여러 구독이 연결된 경우 구독을 지정하는 것이 중요합니다.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>로그에 대한 새 스토리지 계정 만들기

서버와 동일한 구독에서 제공되는 로그에 기존 스토리지 계정을 사용할 수 있습니다. 이 자습서의 경우 Analysis Services 로그에 전용인 새 스토리지 계정을 만듭니다. 편의를 위해 스토리지 계정 세부 정보를 **sa**라는 변수에 저장합니다.

또한 Analysis Services 서버를 포함하는 것과 동일한 리소스 그룹을 사용합니다. 또한 `awsales_resgroup`, `awsaleslogs` 및 `West Central US`의 값을 고유한 값으로 바꿉니다.

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>로그에 대한 서버 계정을 식별합니다

계정 이름을 **account**라는 변수로 설정합니다. 여기서 ResourceName은 계정의 이름입니다.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>로깅 사용

로깅을 사용 하려면 새 저장소 계정, 서버 계정 및 범주에 대한 변수와 함께 AzDiagnosticSetting cmdlet을 사용 합니다. 다음 명령을 실행하고 **-Enabled** 플래그를 **$true**로 설정합니다.

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

출력은 다음 예제와 비슷합니다.

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

이 출력은 이제 서버에 대한 로깅을 사용할 수 있으며 스토리지 계정에 정보가 저장됨을 확인합니다.

이전 로그를 자동으로 삭제하도록 로그에 대한 보존 정책을 설정할 수도 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 사용하는 보존 정책을 **$true**로 설정하고 **-RetentionInDays** 매개 변수를 **90**으로 설정합니다. 90일보다 오래된 로그는 자동으로 삭제됩니다.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>다음 단계

[Azure 리소스 진단 로그](../azure-monitor/platform/platform-logs-overview.md)에 대해 자세히 알아보기

PowerShell 도움말에서 [AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) 을 참조 하세요.
