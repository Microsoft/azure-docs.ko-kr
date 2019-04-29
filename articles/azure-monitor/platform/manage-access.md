---
title: Azure Monitor에서 Log Analytics 작업 영역 관리 | Microsoft Docs
description: 사용자, 계정, 작업 영역 및 Azure 계정에 대한 다양한 관리 태스크를 사용하여 Azure Monitor에서 Log Analytics 작업 영역을 관리할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: 27db27d79a05f24461e63242c0395cfd81315432
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782805"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>로그 데이터 및 Azure Monitor에서 작업 영역 관리
Azure Monitor는 기본적으로 데이터 및 구성 정보가 포함된 컨테이너 인 Log Analytics 작업 영역에 로그 데이터를 저장합니다. 로그 데이터에 대한 액세스를 관리하려면 작업 영역에 관련된 다양한 관리 태스크를 수행합니다. 사용자나 조직의 다른 구성원이 여러 개의 작업 영역을 사용하여 IT 인프라 전체 또는 일부에서 수집되는 각 데이터 집합을 관리할 수 있습니다.

이 문서에서는 로그에 대 한 액세스를 관리 하 고 이들을 포함 하는 작업 영역을 관리 하는 방법을 설명 합니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기
Log Analytics 작업 영역을 만들려면 해야 합니다.

1. Azure 구독이 있어야 합니다.
2. 작업 영역 이름을 선택합니다.
3. 작업 영역을 구독 및 리소스 그룹 중 하나와 연결합니다.
4. 지리적 위치를 선택합니다.

작업 영역 만들기에 대 한 내용은 다음 문서를 참조 하세요.

- [Azure portal에서 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)
- [Azure CLI 2.0을 사용 하 여 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace-cli.md)
- [Azure PowerShell을 사용 하 여 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>필요한 작업 영역의 수 결정
Log Analytics 작업 영역은 Azure 리소스이며 Azure Monitor에서 데이터가 수집, 집계, 분석 및 표시되는 컨테이너입니다. Azure 구독당 여러 작업 영역을 포함할 수 있으며 이들 간의 손쉬운 쿼리 기능을 사용하여 두 개 이상의 작업 영역에 액세스할 수 있습니다. 이 섹션에서는 둘 이상의 작업 영역을 만들 때 유용할 수 있는 시기를 설명합니다.

Log Analytics 작업 영역이 제공하는 정보:

* 데이터 저장소에 대 한 지리적 위치입니다.
* 작업 영역 중심 모드에서 다른 사용자 액세스 권한을 정의 하려면 데이터 격리 합니다. 리소스 중심 모드에서 작동 하는 경우 관련이 없습니다.
* 구성 설정의 범위와 같은 [가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)를 [보존](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) 하 고 [데이터 제한](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)합니다.
* 요금은 데이터 수집 및 보존와 관련 된 작업 영역 리소스에서 수행 됩니다.

사용 관점에서 가능한 한 적은 작업 영역을 만드는 것이 좋습니다. 관리 및 쿼리 환경을 쉽고 빠르게 합니다. 그러나 위의 특성을 기반으로 다음과 같은 경우 여러 작업 영역을 만드는 것이 좋습니다.

* 글로벌 회사이며 데이터 주권 또는 규정 준수 때문에 특정 지역에 저장된 로그 데이터가 필요합니다.
* Azure를 사용하고 있으며 작업 영역을 자신이 관리하는 Azure 리소스와 같은 지역에 두어서 아웃바운드 데이터 전송 요금을 피하려고 합니다.
* 관리되는 서비스 공급자이며 자신이 관리하는 각 고객에 대한 Log Analytics 데이터를 다른 고객의 데이터와 격리되게 유지해야 합니다.
* 여러 고객을 관리 하 고 각 고객 / 부서를 자신의 데이터를 보지만 다른에서 데이터가 아닌 비즈니스 그룹 간 통합된 고객에 대 한 비즈니스 않아도 되며 / 부서 / 비즈니스 그룹 보기. "입니다.

Windows 에이전트를 사용하여 데이터를 수집하는 경우 [각 에이전트를 구성하여 하나 이상의 작업 영역에 보고](../../azure-monitor/platform/agent-windows.md)할 수 있습니다.

System Center Operations Manager를 사용하는 경우 각 Operations Manager 관리 그룹을 한 작업 영역에만 연결할 수 있습니다. Operations Manager에서 관리하는 컴퓨터에 Microsoft 모니터링 에이전트를 설치하고 에이전트가 Operations Manager와 서로 다른 Log Analytics 작업 영역에 모두 보고하게 할 수 있습니다.

작업 영역 아키텍처 정의 되 면이 정책을 사용 하 여 Azure 리소스에서 적용 해야 [Azure 정책](../../governance/policy/overview.md)합니다. 이 자동으로 모든 Azure 리소스에 적용 되는 기본 정의 제공할 수 있습니다. 예를 들어, 특정 지역에 모든 Azure 리소스 특정 작업 영역에 모든 진단 로그를 전송 되도록 하는 정책을 설정할 수 있습니다.

## <a name="view-workspace-details"></a>작업 영역 정보 보기
Log Analytics 작업 영역에서 데이터를 분석 하는 동안 합니다 **Azure Monitor** Azure portal의 메뉴를 생성 하 고 관리 작업 영역에는 **Log Analytics 작업 영역** 메뉴.
 

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics** 작업 영역을 선택합니다.  

    ![Azure portal](media/manage-access/azure-portal-01.png)  

3. 목록에서 작업 영역을 선택합니다.

4. 작업 영역 페이지에는 작업 영역, 시작, 구성 및 추가 정보에 대 한 링크에 대 한 세부 정보가 표시 됩니다.  

    ![작업 영역 정보](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>작업 영역 사용 권한 및 범위
사용자가 액세스할 수 있는 데이터는 다음 표에 나와 있는 여러 요인에 의해 결정 됩니다. 각 작업은 아래 섹션에 설명 되어 있습니다.

| 비율 | 설명 |
|:---|:---|
| [액세스 모드](#access-modes) | 사용자가 사용 하는 메서드는 작업 영역에 액세스 합니다.  사용 가능한 데이터의 범위와 적용 되는 액세스 제어 모드를 정의 합니다. |
| [액세스 제어 모드](#access-control-mode) | 작업 영역 또는 리소스 수준에서 권한을 적용 되었는지 여부를 정의 하는 작업 영역에서 설정 합니다. |
| [권한](#manage-accounts-and-users) | 작업 영역 또는 리소스에 대 한 사용자의 개별 또는 그룹에 적용할 권한입니다. 데이터는 사용자가 액세스할 수를 정의 합니다. |
| [테이블 수준 RBAC](#table-level-rbac) | 해당 액세스 모드 또는 액세스 제어 모드에 관계 없이 모든 사용자에 게 적용 되는 선택적 세분화 된 권한입니다. 사용자가 액세스할 수 있는 데이터 형식을 정의 합니다. |



## <a name="access-modes"></a>액세스 모드
합니다 _액세스 모드_ 사용자가 Log Analytics 작업 영역에 액세스 하는 방법을 참조 하 고 액세스 하는 데이터의 범위를 정의 합니다. 

**작업 영역 중심**: 이 모드에서는 사용자 수 있는 권한이 있는 작업 영역의 모든 로그를 볼 수 있습니다. 이 모드는 쿼리는 모든 데이터 작업 영역에서 모든 테이블에 범위가 지정 됩니다. 이 작업 영역을 사용 하 여 선택 하는 경우와 같은 범위로 로그에 액세스할 때 사용 되는 액세스 모드 **로그** 에서 **Azure Monitor** Azure portal의 메뉴.

**Resource-centric**: 선택 하는 경우와 같은 특정 리소스에 대 한 작업 영역에 액세스할 때 **로그** Azure portal에서 리소스 메뉴에서 액세스할 수 있는 모든 테이블에만 해당 리소스에 대 한 로그를 볼 수 있습니다. 이 모드는 쿼리는 리소스와 연결 된 데이터만 범위가 지정 됩니다. 이 모드에는 세분화 된 역할 기반 액세스 제어를 (RBAC) 수 있습니다. 

> [!NOTE]
> 로그는 관련 리소스를 사용 하 여 제대로 연결 된 경우에 리소스 중심 쿼리에 사용할 수 있습니다. 현재 다음 리소스를 제한 사항이 있습니다. 
> - Azure 외부의 컴퓨터
> - Service Fabric
> - Application Insights
> - 컨테이너
>
> 로그는 쿼리를 실행 하 여 해당 리소스를 사용 하 여 올바르게 연결 하 고 레코드를 검사 하려는 경우를 테스트할 수 있습니다. 올바른 리소스 ID의 경우는 [_ResourceId](log-standard-properties.md#_resourceid) 속성 데이터는 리소스 중심 쿼리에서 사용할 수 있습니다.

### <a name="comparing-access-modes"></a>액세스 모드 비교

다음 표에서 액세스 모드를 보여 줍니다.

| | 작업 영역 중심 | 리소스 중심 |
|:---|:---|:---|
| 각 모델의 대상 | 중앙 관리 합니다. 관리자 데이터 수집 및 사용자를 구성 해야 하는 다양 한 리소스에 액세스 해야 합니다. Azure 외부의 리소스에 대 한 로그에 액세스할 수 있는 사용자에 대해 현재 필요한 수도 있습니다. | 응용 프로그램 팀입니다. 모니터링 되는 Azure 리소스 관리자입니다. |
| 로그를 보려면 사용자 필요한 내용 | 작업 영역에 대 한 권한입니다. 참조 **작업 영역 권한이** 에 [계정 및 사용자 관리](#manage-accounts-and-users)합니다. | 리소스에 대 한 읽기 액세스입니다. 참조 **리소스 사용 권한은** 에 [계정 및 사용자 관리](#manage-accounts-and-users)합니다. 사용 권한 수 있습니다 (예: 포함 된 리소스 그룹)를 상속 또는 리소스에 직접 할당 합니다. 권한 리소스에 대 한 로그를 자동으로 할당 됩니다. |
| 사용 권한 범위는 어떻게 되나요? | 작업 영역입니다. 작업 영역에 대 한 액세스 권한이 있는 사용자를 권한이 있는 테이블에서 해당 영역의 모든 로그를 쿼리할 수 있습니다. 참조 [테이블 액세스 제어](#table-level-rbac) | Azure 리소스입니다. 사용자는 로그를 쿼리할 수 있습니다 리소스에 대 한 모든 작업 영역에서 액세스 권한이 있지만 다른 리소스에 대 한 로그를 쿼리할 수 없습니다. |
| 사용자 액세스 로그를 어떻게 수 있습니까? | 시작 **로그** 에서 **Azure Monitor** 메뉴 또는 **Log Analytics 작업 영역**합니다. | 시작 **로그** Azure 리소스에 대 한 합니다. |


## <a name="access-control-mode"></a>액세스 제어 모드
합니다 _액세스 제어 모드_ 각 작업 영역에서 해당 작업 영역에 대 한 사용 권한이 결정 되는 방식을 정의 하는 설정입니다.

**작업 영역 권한이 필요한**:  이 컨트롤 모드 세분화 된 RBAC를 허용 하지 않습니다. 작업 영역에 액세스할 사용자에 대해 이러한 권한은 부여 해야 특정 테이블이 나 작업 영역에 있습니다. 

사용자 중심 작업 영역 모드에서 작업 영역에 액세스 하는 경우 갖습니다 모든 데이터에 대 한 액세스 권한을 부여 된 모든 테이블. 사용자 리소스 중심 모드에서 작업 영역에 액세스 하는 경우에 대 한 액세스 부여 된 모든 테이블에서 해당 리소스에 대 한 데이터에만 액세스를 해야 합니다.

2019 년 3 월 이전에 만든 모든 작업 영역에 대 한 기본 설정입니다.

**리소스 또는 작업 영역 권한을 사용 하 여**: 이 컨트롤 모드에서는 세분화 된 RBAC를 수 있습니다. 사용자가 Azure 권한을 갖고 있는 리소스를 통해 볼 수 있도록 하는 리소스와 관련 된 데이터만에 대 한 액세스를 부여할 수는 `read` 권한. 

사용자 중심 작업 영역 모드에서 작업 영역에 액세스할 때 작업 영역 사용 권한은 적용 됩니다. 사용자가 리소스 중심 모드에서 작업 영역에 액세스 하면 리소스 사용 권한만 확인할 수 및 작업 영역 권한이 무시 됩니다. 작업 영역 권한에서 제거할 때 인식 되도록 하려면 해당 리소스 권한을 허용 하 여 사용자에 대 한 RBAC를 사용 합니다.

2019 년 3 월 이후에 만든 모든 작업 영역에 대 한 기본 설정입니다.

> [!NOTE]
> 사용자의 작업 영역에 리소스 사용 권한만 있으면 됩니다만 볼 수 사용 하 여 작업 영역 액세스 [리소스 중심 모드](#access-modes)합니다.


### <a name="define-access-control-mode-in-azure-portal"></a>Azure portal에서 액세스 제어 모드 정의
현재 작업 영역 액세스 제어 모드에서 볼 수 있습니다는 **개요** 페이지에서 작업 영역에는 **Log Analytics 작업 영역** 메뉴.

![작업 영역 액세스 제어 모드 보기](media/manage-access/view-access-control-mode.png)

이 설정을 변경할 수는 **속성** 작업 영역에 대 한 페이지입니다. 작업 영역을 구성 하는 권한이 없는 경우 설정을 변경 비활성화 됩니다.

![작업 영역 액세스 모드 변경](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>PowerShell에서 액세스 제어 모드 정의

다음 명령을 사용 하 여 구독에서 모든 작업 영역에 대 한 액세스 제어 모드를 검사 합니다.

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

특정 작업 영역에 대 한 액세스 제어 모드를 설정 하려면 다음 스크립트를 사용 합니다.

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

다음 스크립트를 사용 하 여 구독에서 모든 작업 영역에 대 한 액세스 제어 모드 설정

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Resource Manager 템플릿에 대 한 액세스 모드를 정의 합니다.
Azure Resource Manager 템플릿에서 액세스 모드를 구성 하려면 설정 합니다 **enableLogAccessUsingOnlyResourcePermissions** 다음 값 중 하나에 작업 영역에서 플래그 기능입니다.

- **false**: 작업 영역 중심 사용 권한으로 작업 영역을 설정 합니다. 플래그를 설정 하지 않은 경우 기본 설정입니다.
- **true**: 작업 영역 리소스 중심 사용 권한을 설정 합니다.


## <a name="manage-accounts-and-users"></a>계정 및 사용자 관리
특정 사용자에 게 적용 되는 작업 영역에 대 한 권한을 액세스 모드에 의해 정의 됩니다 및 [액세스 제어 모드](#access-control-mode) 작업 영역입니다. **작업 영역 권한이** 사용자가 사용 하 여 모든 작업 영역에 액세스할 때 적용 됩니다 **작업 영역 중심** 에서 [작업 영역 중심 모드](#access-modes)합니다. **리소스 사용 권한은** 사용자가 사용 하 여 작업 영역에 액세스할 때 적용 됩니다 **리소스 또는 작업 영역 권한을 사용 하 여** [액세스 제어 모드](#access-control-mode) 사용 하 여 [리소스 중심 모드 ](#access-modes).

### <a name="workspace-permissions"></a>작업 영역 사용 권한
각 작업 영역에는 여러 계정이 연결될 수 있으며, 각 계정이 여러 개의 작업 영역에 액세스할 수 있습니다. 액세스는 [Azure 역할 기반 액세스](../../role-based-access-control/role-assignments-portal.md)를 통해 관리됩니다. 


다음 활동에도 Azure 권한이 필요합니다.

| 액션(Action)                                                          | 필요한 Azure 권한 | 메모 |
|-----------------------------------------------------------------|--------------------------|-------|
| 추가 및 제거 모니터링 솔루션                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 이러한 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다. |
| 가격 책정 계층 변경                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* 및 *Site Recovery* 솔루션 타일에서 데이터 보기 | 관리자 / 공동 관리자 | 클래식 배포 모델을 사용하여 배포된 리소스 액세스 |
| Azure Portal에서 작업 영역 만들기                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Azure 권한을 사용 하 여 Log Analytics 작업 영역에 대 한 액세스 관리 
Azure 권한을 사용하여 Log Analytics 작업 영역에 대한 액세스 권한을 부여하려면 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)의 단계를 따릅니다.

Azure의 Log Analytics 작업 영역에는 기본 제공되는 2개의 사용자 역할이 있습니다.
- Log Analytics 독자
- Log Analytics 참가자

*Log Analytics 독자* 역할의 멤버는 다음을 수행할 수 있습니다.
- 모든 모니터링 데이터 검색 및 보기 
- 모든 Azure 리소스에 대한 Azure 진단 구성 보기를 포함해 모니터링 설정을 봅니다.

Log Analytics 독자 역할에는 다음 Azure 작업이 포함됩니다.

| Type    | 사용 권한 | 설명 |
| ------- | ---------- | ----------- |
| 액션(Action) | `*/read`   | 모든 Azure 리소스 및 리소스 구성 보는 기능. 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 |
| 액션(Action) | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 로그 검색 v2 쿼리를 수행할 수 있습니다. |
| 액션(Action) | `Microsoft.OperationalInsights/workspaces/search/action` | 로그 검색 v1 쿼리를 수행할 수 있습니다. |
| 액션(Action) | `Microsoft.Support/*` | 지원 사례를 열 수 있습니다. |
|동작 없음 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 데이터 컬렉션 API를 사용하고 에이전트를 설치하는 데 필요한 작업 영역 키 읽는 것 방지. 사용자가 작업 영역에 새 리소스 추가 방지 |


*Log Analytics 참가자* 역할의 멤버는 다음을 수행할 수 있습니다.
- Log Analytics 독자로서 모든 모니터링 데이터 읽기  
- Automation 계정 만들기 및 구성  
- 관리 솔루션 추가 및 제거    
    > [!NOTE] 
    > 마지막 두 작업을 성공적으로 수행하려면 이 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다.  

- 저장소 계정 키 읽기   
- Azure Storage에서 로그 수집 구성  
- 다음을 포함한 Azure 리소스의 모니터링 설정 편집
  - VM에 VM 확장 추가
  - 모든 Azure 리소스에 대한 Azure 진단 구성

> [!NOTE] 
> 가상 머신을 완전히 제어하기 위해 가상 머신으로 가상 머신 확장을 추가할 수 있는 기능을 사용할 수 있습니다.

Log Analytics 기여자 역할에는 다음 Azure 작업이 포함됩니다.

| 사용 권한 | 설명 |
| ---------- | ----------- |
| `*/read`     | 다음을 포함해 모든 리소스 및 리소스 구성을 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 |
| `Microsoft.Automation/automationAccounts/*` | Runbook 추가 및 편집을 포함해 Azure Automation 계정을 만들고 구성할 수 있습니다. |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 확장 및 Linux 확장용 OMS Agent를 포함해 가상 머신 확장 추가, 업데이트 및 제거 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 저장소 계정 키를 봅니다. Azure Storage 계정에서 로그를 읽을 Log Analytics 구성 필요 |
| `Microsoft.Insights/alertRules/*` | 규칙 추가, 업데이트 및 제거 |
| `Microsoft.Insights/diagnosticSettings/*` | Azure 리소스에 대한 진단 설정 추가, 업데이트 및 제거 |
| `Microsoft.OperationalInsights/*` | Log Analytics 작업 영역에 대한 구성 추가, 업데이트 및 제거 |
| `Microsoft.OperationsManagement/*` | 관리 솔루션 추가 및 제거 |
| `Microsoft.Resources/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |

사용자 역할에 사용자를 추가 및 제거하려면 `Microsoft.Authorization/*/Delete` 및 `Microsoft.Authorization/*/Write` 권한이 있어야 합니다.

이러한 역할을 사용하여 사용자에게 다양한 범주에서 액세스를 제공합니다.
- 구독 - 구독에서 모든 작업 영역에 대한 액세스
- 리소스 그룹 - 리소스 그룹에서 모든 작업 영역에 대한 액세스
- 리소스 - 지정된 작업 영역에만 액세스

정확한 액세스 제어를 수행 하기 위해 리소스 수준 (작업 영역)에서 할당을 수행 해야 합니다.  [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 사용하여 필요한 특정 권한이 있는 역할을 만듭니다.

### <a name="resource-permissions"></a>리소스 사용 권한 
중심 리소스 액세스를 사용 하 여 작업 영역에서 사용자 쿼리를 기록 하는 경우 리소스에 대해 다음 권한이 해야 합니다.

| 사용 권한 | 설명 |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>예제:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 리소스에 대 한 모든 로그 데이터를 볼 수 있습니다.  |


이 사용 권한을 포함 하는 역할에서 일반적으로 허용 됩니다  _\*/읽기 또는_ _\*_ 같은 기본 제공 권한 [판독기](../../role-based-access-control/built-in-roles.md#reader) 하고[ 참가자](../../role-based-access-control/built-in-roles.md#contributor) 역할입니다. 참고를 특정 작업을 포함 하는 사용자 지정 역할이 나 전용된 기본 제공 역할이이 권한은 포함 되지 않을 수 있습니다.

참조 [테이블당 액세스 제어를 정의](#table-level-rbac) 서로 다른 테이블에 대 한 다른 액세스 제어를 만들려는 경우에는 아래.


## <a name="table-level-rbac"></a>테이블 수준 RBAC
**테이블 수준 RBAC** 다른 권한 뿐 아니라 Log Analytics 작업 영역에서 데이터를 보다 세부적으로 제어를 제공할 수 있습니다. 이 컨트롤을 사용 하면 사용자의 특정 집합에만 액세스할 수 있는 특정 데이터 형식을 정의할 수 있습니다.

사용 하 여 테이블 액세스 제어를 구현 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md) 권한을 부여 하거나 특정에 대 한 액세스를 거부 [테이블](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) 작업 영역에서 합니다. 이러한 역할은 작업 영역 중심 또는 리소스 중심 작업 영역에 적용 됩니다 [액세스 제어 모델](#access-control-mode) 사용자에 관계 없이 [액세스 모드](#access-modes)합니다.

만들기는 [사용자 지정 역할](../../role-based-access-control/custom-roles.md) 테이블 액세스 제어에 대 한 액세스를 정의 하는 다음 작업을 사용 하 여 합니다.

- 테이블에 대 한 액세스에 권한을 부여 하려면에 포함 된 **작업** 역할 정의의 섹션입니다.
- 테이블에 대 한 액세스를 거부 하려면에 포함 된 **NotActions** 역할 정의의 섹션입니다.
- 사용 하 여 * 모든 테이블을 지정 합니다.

에 대 한 액세스를 사용 하 여 역할을 만드는 예는 _하트 비트_ 및 _AzureActivity_ 테이블을 만들려면 다음 작업을 사용 하 여 사용자 지정 역할을 합니다.

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

대 한 액세스를 사용 하 여 역할을 만드는 _SecurityBaseline_ 다른 테이블에 다음 작업을 사용 하 여 사용자 지정 역할을 만듭니다.

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>사용자 지정 로그
 사용자 지정 로그는 사용자 지정 로그 및 HTTP 데이터 수집기 API와 같은 데이터 원본에서 생성 됩니다. 아래 나열 된 테이블을 확인 하 여 로그의 유형을 식별 하는 가장 쉬운 방법은 됩니다 [로그 스키마의 사용자 지정 로그](../log-query/get-started-portal.md#understand-the-schema)합니다.

 현재 부여 하거나 개별 사용자 지정 로그에 대 한 액세스를 거부할 수는 없지만 또는 모든 사용자 지정 로그에 대 한 액세스를 거부할 수 있습니다. 모든 사용자 지정 로그에 대 한 액세스를 사용 하 여 역할을 만들려면 다음 작업을 사용 하 여 사용자 지정 역할을 만듭니다.

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>고려 사항

- 사용자는 전역 부여 된 경우에 대 한 읽기 권한을 포함 하는 표준 읽기 권한자 또는 참가자 역할을 사용 하 여는  _\*/읽기_ 동작을 재정의 테이블당 access control 및 모든 로그 데이터에 액세스 하 게 합니다.
- 테이블당 액세스 하지만 다른 사용 권한을 사용자 부여 되 면 API에서 하지만 Azure portal에서가 아니라 로그 데이터를 액세스할 수 없게 됩니다. Azure portal에 대 한 액세스를 제공 하려면 Log Analytics 독자를 사용 하 여 해당 기본 역할을 합니다.
- 구독에 대해 관리자에는 다른 권한 설정에 관계 없이 모든 데이터 형식에 액세스할을 수 있습니다.
- 작업 영역 소유자는 테이블당 액세스 제어에 대 한 다른 사용자 처럼 취급 됩니다.
- 역할 할당의 수를 줄이기 위해 개별 사용자 대신 보안 그룹에 할당 해야 합니다. 이 데도 도움이 기존 그룹 관리 도구를 사용 하 여 구성 하 고 액세스를 확인 합니다.




## <a name="next-steps"></a>다음 단계
* 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에서 데이터를 수집하려면 [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)를 참조하세요.
* Azure VM에서 데이터 수집을 구성하려면 [Azure Virtual Machines에 대한 데이터 수집](../../azure-monitor/learn/quick-collect-azurevm.md)을 참조하세요.  

