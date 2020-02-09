---
title: 작업 시간 외 VM 시작/중지 솔루션
description: 이 VM 관리 솔루션은 일정에 따라 Azure Resource Manager 가상 컴퓨터를 시작 및 중지 하 고 Azure Monitor 로그에서 사전에 모니터링 합니다.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 37fee7f96a27942a1295cb8c2315fedffc5bdefe
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030164"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Automation의 작업 시간 외 VM 시작/중지 솔루션

작업 시간 외 VM 시작/중지 솔루션은 사용자 정의 일정에 따라 Azure virtual machines를 시작 및 중지 하 고, Azure Monitor 로그를 통해 정보를 제공 하 고, [작업 그룹](../azure-monitor/platform/action-groups.md)을 사용 하 여 선택적 전자 메일을 보냅니다. 대부분의 시나리오에서 Azure Resource Manager 및 클래식 VM이 둘 다 지원됩니다. 클래식 Vm에서이 솔루션을 사용 하려면 기본적으로 생성 되지 않는 클래식 실행 계정이 필요 합니다. 클래식 실행 계정을 만드는 방법에 대한 지침은 [클래식 실행 계정](automation-create-standalone-account.md#classic-run-as-accounts)을 참조 하세요.

> [!NOTE]
> 작업 시간 외 VM 시작/중지 솔루션은 솔루션을 배포할 때 Automation 계정으로 가져온 Azure 모듈을 사용 하 여 테스트 되었습니다. 솔루션은 현재 최신 버전의 Azure 모듈에서 작동 하지 않습니다. 작업 시간 외 VM 시작/중지 솔루션을 실행 하는 데 사용 하는 Automation 계정에만 영향을 줍니다. [에서 Azure PowerShell 모듈을 업데이트 하는 방법](automation-update-azure-modules.md) 에 설명 된 대로 다른 Automation 계정에서 최신 버전의 Azure 모듈을 계속 사용할 수 있습니다 Azure Automation

이 솔루션은 VM 비용을 최적화하려는 사용자에게 분산된 저비용 자동화 옵션을 제공합니다. 이 솔루션을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- VM 시작/중지 예약
- Azure Tags(클래식 VM에서는 지원되지 않음)를 사용하여 VM 시작/중지를 오름차순으로 예약
- 낮은 CPU 사용량을 기준으로 VM 자동 중지

현재 솔루션에 대한 제한 사항은 다음과 같습니다.

- 이 솔루션은 모든 지역의 VM을 관리하지만 Azure Automation 계정과 동일한 구독에서만 사용할 수 있습니다.
- 이 솔루션은 Log Analytics 작업 영역, Azure Automation 계정 및 Alerts를 지원하는 Azure 및 AzureGov의 모든 지역에서 사용할 수 있습니다. AzureGov 지역은 현재 이메일 기능을 지원하지 않습니다.

> [!NOTE]
> 클래식 VM용 솔루션을 사용하는 경우 모든 VM이 클라우드 서비스마다 순차적으로 처리됩니다. 가상 머신은 다른 클라우드 서비스에서 여전히 병렬로 처리됩니다. 클라우드 서비스 당 Vm이 20 개를 초과 하는 경우에는 부모 runbook **ScheduledStartStop_Parent** 를 사용 하 여 여러 일정을 만들고 일정 당 20 개의 vm을 지정 하는 것이 좋습니다. 일정 속성에서를 쉼표로 구분 된 목록으로 지정 하 고 **vmlist** 매개 변수에서 VM 이름을 지정 합니다. 그렇지 않고이 솔루션에 대한 자동화 작업이 3 시간 이상 실행 되 면 공평 하 게 [공유](automation-runbook-execution.md#fair-share) 한도에 따라 일시적으로 언로드되고 중지 됩니다.
>
> Azure CSP(Cloud Solution Provider) 구독은 Azure Resource Manager 모델만 지원하므로 Azure Resource Manager 이외의 서비스는 프로그램에서 사용할 수 없습니다. 시작/중지 솔루션을 실행하면 클래식 리소스를 관리하기 위한 cmdlet에 발생하는 오류가 발생할 수 있습니다. CSP에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요. CSP 구독을 사용하는 경우 배포 후에 [**External_EnableClassicVMs**](#variables) 변수를 **False**로 수정해야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>필수 조건

이 솔루션에 대한 Runbook은 [Azure 실행 계정](automation-create-runas-account.md)을 통해 작동합니다. 실행 계정은 자주 만료되거나 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 선호하는 인증 방법입니다.

VM 시작/중지 솔루션에 별도의 Automation 계정을 사용 하는 것이 좋습니다. Azure 모듈 버전이 자주 업그레이드 되 고 해당 매개 변수가 변경 될 수 있기 때문입니다. VM 시작/중지 솔루션은 동일한 흐름에서 업그레이드 되지 않으므로 사용 하는 cmdlet의 최신 버전에서는 작동 하지 않을 수 있습니다. 또한 프로덕션 Automation 계정으로 가져오기 전에 테스트 자동화 계정에서 모듈 업데이트를 테스트 하는 것이 좋습니다.

### <a name="permissions-needed-to-deploy"></a>배포 하는 데 필요한 권한

사용자가 휴가 시간 동안 Vm 시작/중지 솔루션을 배포 해야 하는 특정 권한이 있습니다. 이러한 권한은 미리 만든 Automation 계정 및 Log Analytics 작업 영역을 사용 하거나 배포 중에 새 작업 영역을 만드는 경우에 다릅니다. Azure Active Directory 테넌트의 구독 및 전역 관리자에 대한 참가자 인 경우 다음 사용 권한을 구성할 필요가 없습니다. 이러한 권한이 없거나 사용자 지정 역할을 구성 해야 하는 경우 아래에 필요한 사용 권한을 참조 하세요.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>기존 Automation 계정 및 Log Analytics 작업 영역

업무 시간 외 Vm 시작/중지 솔루션을 기존 Automation 계정 및 Log Analytics 작업 영역에 배포 하려면 솔루션을 배포 하는 사용자에 게 **리소스 그룹**에 대한 다음 권한이 필요 합니다. 역할에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요.

| 사용 권한 | 범위|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/variables/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/schedules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/runbooks/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/connections/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/modules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/modules/read | 리소스 그룹 |
| Microsoft.automation/automationAccounts/jobSchedules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/read | 리소스 그룹 |
| Microsoft.OperationsManagement/solutions/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/* | 리소스 그룹 |
| Microsoft.Insights/diagnosticSettings/write | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/Write | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/read | 리소스 그룹 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 |
| Microsoft.Resources/deployments/* | 리소스 그룹 |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>새 Automation 계정 및 새 Log Analytics 작업 영역

업무 시간 외 Vm 시작/중지 솔루션을 새 Automation 계정 및 Log Analytics 작업 영역에 배포 하려면 솔루션을 배포 하는 사용자에 게 이전 섹션에 정의 된 권한과 다음 사용 권한이 필요 합니다.

- 구독에 대한 공동 관리자-클래식 Vm을 관리 하려는 경우 클래식 실행 계정을 만드는 데만 필요 합니다. [클래식 실행 계정은](automation-create-standalone-account.md#classic-run-as-accounts) 더 이상 기본적으로 생성 되지 않습니다.
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **응용 프로그램 개발자** 역할의 멤버입니다. 실행 계정을 구성 하는 방법에 대한 자세한 내용은 [실행 계정을 구성할 수 있는 권한](manage-runas-account.md#permissions)을 참조 하세요.
- 구독에 대한 참가자 또는 다음 사용 권한.

| 사용 권한 |범위|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/write | 리소스 그룹 |

## <a name="deploy-the-solution"></a>솔루션 배포

작업 시간 외 VM 시작/중지 솔루션을 Automation 계정에 추가하고 솔루션을 사용자 지정하도록 변수를 구성하려면 다음 단계를 수행합니다.

1. Automation 계정의 **관련 리소스** 아래에서 **VM 시작/중지**를 선택합니다. 여기서 **자세히 알아보고 솔루션을 사용하도록 설정하세요**를 클릭할 수 있습니다. VM 시작/중지 솔루션이 이미 표시되어 있으면 **솔루션 관리**를 클릭하여 선택하고 목록에서 찾을 수 있습니다.

   ![Automation 계정에서 사용하도록 설정](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure Portal의 어디서나 **리소스 만들기**를 클릭하여 만들 수도 있습니다. Marketplace 페이지에서 **시작** 또는 **시작/중지**와 같은 키워드를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 또는 솔루션의 전체 이름에서 하나 이상의 키워드를 입력한 다음 Enter 키를 누를 수도 있습니다. 검색 결과에서 **작업 시간 외 VM 시작/중지**를 선택합니다.

2. 선택한 솔루션에 대한 **작업 시간 외 VM 시작/중지** 페이지에서 요약 정보를 검토한 다음, **만들기**를 클릭합니다.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

3. **솔루션 추가** 페이지가 나타납니다. Automation 구독으로 솔루션을 가져오려면 솔루션을 구성하라는 메시지가 표시됩니다.

   ![VM 관리 솔루션 추가 페이지](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. **솔루션 추가** 페이지에서 **작업 영역**을 선택합니다. Automation 계정이 속하는 동일한 Azure 구독에 연결된 Log Analytics 작업 영역을 선택합니다. 작업 영역이 없으면 **새 작업 영역 만들기**를 선택합니다. **Log Analytics 작업 영역** 페이지에서 다음 단계를 수행 합니다.
   - 새 **Log Analytics 작업 영역**에 대한 이름 (예: "ContosoLAWorkspace")을 지정 합니다.
   - 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
   - **리소스 그룹**의 경우, 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.
   - **위치**를 선택합니다.
   - **가격 책정 계층**을 선택합니다. **GB당(독립 실행형)** 옵션을 선택합니다. Azure Monitor 로그의 [가격은](https://azure.microsoft.com/pricing/details/log-analytics/) 업데이트 되며 GB 당 계층만 유일한 옵션입니다.

   > [!NOTE]
   > 솔루션을 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.
   >
   > 지원 되는 매핑 쌍 목록은 [Automation 계정 및 Log Analytics 작업 영역에 대한 지역 매핑](how-to/region-mappings.md)을 참조 하세요.

5. **Log Analytics 작업 영역** 페이지에서 필수 정보를 입력한 후 **만들기**를 클릭합니다. 메뉴의 **알림**에서 진행률을 추적할 수 있습니다. 완료한 후에는 **솔루션 추가** 페이지로 돌아갑니다.
6. **솔루션 추가** 페이지에서 **Automation 계정**을 선택합니다. 새 Log Analytics 작업 영역을 만드는 경우 연결될 새 Automation 계정을 만들거나 아직 Log Analytics 작업 영역에 연결되지 않은 기존 Automation 계정을 선택할 수 있습니다. 기존 Automation 계정을 선택하거나 **Automation 계정 만들기**를 클릭하고, **Automation 계정 추가** 페이지에서 다음 정보를 제공합니다.
   - **이름** 필드에서 Automation 계정의 이름을 입력합니다.

     다른 모든 옵션은 선택한 Log Analytics 작업 영역을 기반으로 자동으로 채워집니다. 이러한 옵션은 수정할 수 없습니다. Azure 실행 계정은 이 솔루션에 포함된 Runbook에 대한 기본 인증 방법입니다. **확인**을 클릭하면 구성 옵션의 유효성이 검사되고 Automation 계정이 생성됩니다. 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

7. 마지막으로 **솔루션 추가** 페이지에서 **구성**을 선택합니다. **매개 변수** 페이지가 나타납니다.

   ![솔루션에 대한 매개 변수 페이지](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   여기에서는 다음을 묻는 메시지가 표시됩니다.
   - **대상 ResourceGroup 이름**을 지정합니다. 이 값은 이 솔루션에서 관리되는 VM을 포함하는 리소스 그룹 이름입니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 구독 내 모든 리소스 그룹의 VM을 대상으로 하려는 경우에는 와일드카드 사용이 지원됩니다. 이 값은 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames** 변수에 저장됩니다.
   - **VM 제외 목록(문자열)** 을 지정합니다. 이 값은 대상 리소스 그룹에 있는 하나 이상의 가상 머신 이름입니다. 이름은 두 개 이상 입력할 수 있으며 각 이름을 쉼표로 구분해야 하고 대/소문자는 구분되지 않습니다. 와일드카드를 사용할 수 있습니다. 이 값은 **External_ExcludeVMNames** 변수에 저장됩니다.
   - **일정**을 선택합니다. 일정의 날짜 및 시간을 선택 합니다. 반복 된 일별 일정은 선택한 시간부터 생성 됩니다. 다른 지역을 선택할 수는 없습니다. 솔루션을 구성한 후 일정을 특정 표준 시간대로 구성하려면 [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)을 참조하세요.
   - 작업 그룹에서 **메일 알림**을 받으려면 기본값인 **예**를 그대로 두고 유효한 메일 주소를 제공합니다. **아니요**를 선택하지만 나중에 메일 알림을 수신하려면 쉼표로 구분된 유효한 메일 주소로 만들어진 [작업 그룹](../azure-monitor/platform/action-groups.md)을 업데이트하면 됩니다. 또한 다음 경고 규칙을 사용하도록 설정해야 합니다.

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > **대상 ResourceGroup 이름**의 기본값은 **&ast;** 입니다. 이것은 구독에 포함된 모든 VM을 대상으로 합니다. 솔루션에서 구독의 모든 VM을 대상으로 지정하지 않으려면 일정을 사용하기 전에 이 값을 리소스 그룹 이름 목록으로 업데이트해야 합니다.

8. 솔루션에 필요한 초기 설정을 구성한 후에 **확인**을 클릭하여 **매개 변수** 페이지를 닫고 **만들기**를 선택합니다. 모든 설정이 확인되면 솔루션이 구독에 배포됩니다. 이 프로세스는 완료하는 데 수 초가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

> [!NOTE]
> Azure CSP (클라우드 솔루션 공급자) 구독이 있는 경우 배포가 완료 된 후 Automation 계정에서 **공유 리소스** 아래의 **변수로** 이동 하 고 [**External_EnableClassicVMs**](#variables) 변수를 **False**로 설정 합니다. 이렇게 하면 솔루션이 클래식 VM 리소스를 검색하지 않습니다.

## <a name="scenarios"></a>시나리오

솔루션에는 세 가지 고유한 시나리오가 포함되어 있습니다. 이러한 시나리오는 다음과 같습니다.

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>시나리오 1: 일정에 따라 VM 시작/중지

이 시나리오는 솔루션을 처음 배포할 때 기본 구성입니다. 예를 들어 저녁에 사무실을 나갈 때 구독의 모든 VM을 중지하고, 아침에 사무실로 복귀할 때 시작하도록 구성할 수 있습니다. 배포 중에 일정 **Scheduled-StartVM** 및 **Scheduled-StopVM**을 구성할 경우 대상 VM을 시작 및 중지합니다. VM을 중지하도록 이 솔루션을 구성하려면 [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)을 참조하여 사용자 지정 일정을 구성하는 방법을 알아봅니다.

> [!NOTE]
> 표준 시간대는 예약 시간 매개 변수를 구성할 당시의 현재 표준 시간대입니다. 하지만 Azure Automation에서 UTC 형식으로 저장됩니다. 따라서 배포 중에 처리될 때 표준 시간대 변환 작업을 수행할 필요가 없습니다.

다음 변수 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames**를 구성하여 범위 안에 포함되는 VM을 제어합니다.

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로만 작업 대상을 지정할 수 있습니다.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. 대상 VM을 지정하도록 **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수를 구성합니다.
2. **Scheduled-StartVM** 및 **Scheduled-StopVM** 일정을 설정하고 업데이트합니다.
3. ACTION 매개 변수를 **start**로 설정하고 WHATIF 매개 변수를 **True**로 설정하여 **ScheduledStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. ACTION 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가한 다음, WHATIF 매개 변수를 **True**로 설정하여 **ScheduledStartStop_Parent** Runbook을 실행합니다. 변경 사항을 미리 봅니다.
1. 쉼표로 구분된 VM 목록(VM1, VM2, VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.
1. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

> [!NOTE]
> **Target ResourceGroup Names**의 값은 두 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**의 값으로 저장됩니다. 추가 세분성을 위해 각기 다른 리소스 그룹을 대상으로 하도록 이러한 각 변수를 수정할 수 있습니다. 시작 작업의 경우 **External_Start_ResourceGroupNames**를 사용하고, 중지 작업의 경우 **External_Stop_ResourceGroupNames**를 사용합니다. VM이 시작 및 중지 일정에 자동으로 추가됩니다.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>시나리오 2: 태그를 사용하여 시퀀스의 VM 시작/중지

분산 워크로드를 지원하는 여러 VM에 둘 이상의 구성 요소를 포함하는 환경에서는 순서대로 시작 및 중지되는 구성 요소 시퀀스를 지원하는 것이 중요합니다. 다음 단계를 수행하여 이 시나리오를 수행할 수 있습니다.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames** 변수에서 대상으로 지정된 VM에 양의 정수 값으로 **sequencestart** 및 **sequencestop** 태그를 추가합니다. 시작 및 중지 작업은 오름차순으로 수행됩니다. VM에 태그를 지정하는 방법을 알아보려면 [Azure에서 Windows 가상 머신에 태그 지정](../virtual-machines/windows/tag.md) 및 [Azure에서 Linux 가상 머신에 태그 지정](../virtual-machines/linux/tag.md)을 참조하세요.
1. 요구 사항을 충족하는 날짜 및 시간으로 일정 **Sequenced-StartVM** 및 **Sequenced StopVM**을 수정하고 일정을 사용하도록 설정합니다.
1. ACTION 매개 변수를 **start**로 설정하고 WHATIF 매개 변수를 **True**로 설정하여 **SequencedStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.
1. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 Runbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. 양의 정수 값이 포함된 **sequencestart** 및 **sequencestop** 태그를 **VMList** 매개 변수에 추가하려는 VM에 추가합니다.
1. ACTION 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가한 다음, WHATIF 매개 변수를 **True**로 설정하여 **SequencedStartStop_Parent** Runbook을 실행합니다. 변경 사항을 미리 봅니다.
1. 쉼표로 구분된 VM 목록(VM1, VM2, VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.
1. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.
1. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 monitoring-and-diagnostics/monitoring-action-groupsrunbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>시나리오 3: CPU 사용률에 따라 자동으로 시작/중지

이 솔루션은 구독에서 가상 머신 실행에 필요한 비용을 관리하는 데 도움을 줄 수 있습니다. 사용량이 많지 않은 기간 동안에 사용되지 않는 Azure VM을 평가하고, 프로세서 사용률이 x% 이하일 경우(예: 몇 시간 후) 자동으로 종료하도록 합니다.

기본적으로 솔루션은 CPU 백분율 메트릭을 평가하고 평균 사용률이 5% 이하인지 파악하도록 미리 구성됩니다. 이 시나리오는 다음 변수를 통해 제어되며, 기본값이 요구 사항을 충족하지 않는 경우 수정할 수 있습니다.

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로만 작업 대상을 지정할 수 있습니다.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>중지 작업의 대상으로 구독 및 리소스 그룹 지정

1. 대상 VM을 지정하도록 **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수를 구성합니다.
1. **Schedule_AutoStop_CreateAlert_Parent** 일정을 사용하도록 설정하고 업데이트합니다.
1. ACTION 매개 변수를 **start**로 설정하고 WHATIF 매개 변수를 **True**로 설정하여 **AutoStop_CreateAlert_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. ACTION 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가하고, WHATIF 매개 변수를 **True**로 설정하여 **AutoStop_CreateAlert_Parent** Runbook을 실행합니다. 변경 사항을 미리 봅니다.
1. 쉼표로 구분된 VM 목록(VM1, VM2, VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.
1. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

CPU 사용량에 따라 VM을 중지하는 일정을 만들었으므로 다음 일정 중 하나를 사용하도록 설정하여 시작해야 합니다.

- 구독 및 리소스 그룹으로 시작 작업 대상 지정. **Scheduled-StartVM** 일정을 테스트하고 사용하도록 설정하려면 [시나리오 1](#scenario-1-startstop-vms-on-a-schedule)의 단계를 참조하세요.
- 구독, 리소스 그룹 및 태그별로 시작 작업 대상 지정. **Sequenced-StartVM** 일정을 테스트하고 사용하도록 설정하려면 [시나리오 2](#scenario-2-startstop-vms-in-sequence-by-using-tags)의 단계를 참조하세요.

## <a name="solution-components"></a>솔루션 구성 요소

이 솔루션에는 비즈니스 요구에 맞게 가상 머신의 시작 및 종료를 조정할 수 있도록 미리 구성 된 runbook, 일정 및 Azure Monitor 로그와의 통합이 포함 되어 있습니다.

### <a name="runbooks"></a>Runbook

다음 표에서는 이 솔루션이 Automation 계정에 배포한 Runbook을 나열합니다. Runbook 코드를 변경하지 마십시오. 대신, 새 기능에 대한 고유한 Runbook을 작성할 수는 있습니다.

> [!IMPORTANT]
> 해당 이름에 "자식"이 추가된 모든 Runbook을 직접 실행하지 마십시오.

모든 부모 Runbook에는 _WhatIf_ 매개 변수가 포함됩니다. 이 매개 변수가 **True**로 설정되면 _WhatIf_는 _WhatIf_ 매개 변수 없이 실행될 때 Runbook이 수행하는 정확한 동작에 대한 자세한 설명을 지원하며, 올바른 VM이 대상으로 지정되었는지 확인합니다. Runbook은 _WhatIf_ 매개 변수가 **False**로 설정된 경우에만 정의된 작업을 수행합니다.

|Runbook | 매개 변수 | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 부모 Runbook에서 호출됩니다. 이 Runbook은 AutoStop 시나리오에서 리소스 기준으로 경고를 만듭니다.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True 또는 False  | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_.<br> *WhatIf*는 Runbook 논리를 실행하지 않고 유효성을 검사합니다.|
|AutoStop_Disable | none | AutoStop 경고 및 기본 일정을 사용하지 않도록 설정합니다.|
|AutoStop_StopVM_Child | WebHookData | 부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 VM을 중지합니다.|
|Bootstrap_Main | none | 일반적으로 Azure Resource Manager에서 액세스할 수 없는 webhookURI 같은 부트스트랩 구성을 설정하기 위해 한 번 사용됩니다. 배포가 성공하면 이 Runbook은 자동으로 제거됩니다.|
|ScheduledStartStop_Child | VMName <br> 작업: 시작 또는 중지 <br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 예약된 중지에서 시작 또는 중지 작업을 실행합니다.|
|ScheduledStartStop_Parent | 작업: 시작 또는 중지 <br>VMList <br> WhatIf: True 또는 False | 이 설정은 구독에 있는 모든 VM에 영향을 줍니다. 이러한 대상이 지정된 리소스 그룹에서만 실행되도록 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**를 편집합니다. **External_ExcludeVMNames** 변수를 업데이트하여 특정 VM을 제외할 수도 있습니다.<br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_.<br> _WhatIf_는 Runbook 논리를 실행하지 않고 유효성을 검사합니다.|
|SequencedStartStop_Parent | 작업: 시작 또는 중지 <br> WhatIf: True 또는 False<br>VMList| 시작/중지 작업을 시퀀스하려는 각 VM에 **sequencestart** 및 **sequencestop**이라고 명명된 태그를 생성합니다. 이 태그 이름은 대/소문자를 구분합니다. 태그 값은 시작하거나 중지하려는 순서에 해당하는 양의 정수(1, 2, 3)여야 합니다. <br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_. <br> _WhatIf_는 Runbook 논리를 실행하지 않고 유효성을 검사합니다. <br> **참고**: VM은 Azure Automation 변수에 External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames 및 External_ExcludeVMNames으로 정의된 리소스 그룹 내에 있어야 합니다. 작업이 적용되려면 적절한 태그가 있어야 합니다.|

### <a name="variables"></a>변수

다음 표에는 Automation 계정에서 만든 변수가 나열되어 있습니다. **External** 접두사가 붙은 변수만 수정합니다. 변수의 접두사를 **Internal**로 수정하면 원치 않는 결과로 이어집니다.

|변수 | Description|
|---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 사용 가능한 값은 **GreaterThan**, **GreaterThanOrEqual**, **LessThan** 및 **LessThanOrEqual**입니다.|
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.|
|External_AutoStop_Threshold | _External_AutoStop_MetricName_ 변수에 지정된 Azure 경고 규칙에 대한 임계값입니다. 백분율 값의 범위는 1에서 100까지입니다.|
|External_AutoStop_TimeAggregationOperator | 조건을 평가하기 위해 선택한 기간 크기에 적용되는 시간 집계 연산자입니다. 사용 가능한 값은 **Average**, **Minimum**, **Maximum**, **Total** 및 **Last**입니다.|
|External_AutoStop_TimeWindow | Azure에서 경고를 트리거하기 위해 선택된 메트릭을 분석하는 기간입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다.|
|External_EnableClassicVMs| 솔루션이 클래식 VM을 대상으로 하는지 여부를 지정합니다. 기본값은 True입니다. CSP 구독의 경우 False로 설정해야 합니다. 클래식 Vm에는 [클래식 실행 계정이](automation-create-standalone-account.md#classic-run-as-accounts)필요 합니다.|
|External_ExcludeVMNames | 공백 없이 쉼표로 이름을 구분하여 제외할 VM 이름을 입력합니다. VM은 140개로 제한됩니다. 이 쉼표로 구분 된 목록에 140 개 이상의 Vm을 추가 하는 경우 제외 되도록 설정 된 Vm이 실수로 시작 되거나 중지 될 수 있습니다.|
|External_Start_ResourceGroupNames | 시작 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|
|External_Stop_ResourceGroupNames | 중지 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|
|Internal_AutoSnooze_WebhookUri | AutoStop 시나리오에 대해 호출되는 Webhook URI를 지정합니다.|
|Internal_AzureSubscriptionId | Azure 구독 ID를 지정합니다.|
|Internal_ResourceGroupName | Automation 계정 리소스 그룹 이름을 지정합니다.|

모든 시나리오에서 **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** 및 **ScheduledStartStop_Parent** Runbook에 대해 쉼표로 구분된 VM 목록을 제공하는 경우를 제외하고 대상 VM을 지정하는 데 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수가 필요합니다. 즉, 시작 및 중지 작업이 진행되려면 VM이 대상 리소스 그룹에 있어야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정하며 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure 정책과 유사하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작 및 중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다. 이를 수정 하거나 사용자 지정 일정을 만들 수 있습니다. 기본적으로 **Scheduled_StartVM** 및 **Scheduled_StopVM**를 제외 하 고 모든 일정은 사용 하지 않도록 설정 됩니다.

일정 작업이 겹칠 수 있기 때문에 모든 일정을 사용하도록 설정해서는 안 됩니다. 그에 맞게 수행하고 수정할 최적화를 결정하는 것이 가장 좋습니다. 추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.

|일정 이름 | 빈도 | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 | AutoStop_CreateAlert_Parent Runbook을 8시간 간격으로 실행합니다. 그러면 Azure Automation 변수에서 External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames 및 External_ExcludeVMNames에서 VM 기반 값이 중지됩니다. 또는 VMList 매개 변수를 사용하여 쉼표로 구분된 VM 목록을 지정할 수 있습니다.|
|Scheduled_StopVM | 사용자 정의, 매일 | 매일 지정된 시간에 _Stop_ 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다. 자산 변수에 의해 정의 된 규칙을 충족 하는 모든 Vm이 자동으로 중지 됩니다. 관련 일정, 예약 된 **StartVM**을 사용 하도록 설정 합니다.|
|Scheduled_StartVM | 사용자 정의, 매일 | 매일 지정된 시간에 _Start_ 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다. 적절한 변수로 정의된 규칙을 충족하는 모든 VM이 자동으로 시작됩니다. 관련 일정, **예약 된 StopVM**을 사용 하도록 설정 합니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 매주 금요일 지정된 시간에 _Stop_ 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다. 순차적 (오름차순)은 적절 한 변수에 의해 정의 된 **SequenceStop** 태그를 사용 하 여 모든 vm을 중지 합니다. 태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요. 관련 일정, **시퀀싱-StartVM**을 사용 하도록 설정 합니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 매주 월요일 지정된 시간에 _Start_ 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다. 적절한 변수로 **SequenceStart** 태그가 정의되어 있는 모든 VM이 순차적으로(내림차순으로) 시작됩니다. 태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요. 관련된 일정, **Sequenced-StopVM**을 사용하도록 설정합니다.|

## <a name="azure-monitor-logs-records"></a>Azure Monitor 로그 레코드

Automation은 Log Analytics 작업 영역에 작업 로그 및 작업 스트림의 두 가지 유형의 레코드를 만듭니다.

### <a name="job-logs"></a>작업 로그

|속성 | Description|
|----------|----------|
|Caller |  작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
|범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다.|
|CorrelationId | Runbook 작업의 상관 관계 ID인 GUID입니다.|
|JobId | Runbook 작업의 ID인 GUID입니다.|
|operationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다.|
|resourceId | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
|ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다. Automation에 대한 값은 Azure Automation입니다.|
|ResourceType | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|resultType | runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- Succeeded입니다.|
|resultDescription | runbook 작업 결과 상태를 설명합니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다.|
|RunbookName | Runbook의 이름을 지정합니다.|
|SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다. Automation의 경우 값은 OpsManager입니다.|
|StreamType | 이벤트의 유형을 지정합니다. 가능한 값은 다음과 같습니다.<br>- Verbose입니다.<br>- 출력<br>- 오류<br>- 경고|
|SubscriptionId | 작업의 구독 ID를 지정합니다.
|시간 | runbook 작업이 실행된 날짜 및 시간입니다.|

### <a name="job-streams"></a>작업 스트림

|속성 | Description|
|----------|----------|
|Caller |  작업을 시작한 사람입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다.|
|범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다.|
|JobId | Runbook 작업의 ID인 GUID입니다.|
|operationName | Azure에서 수행되는 작업 유형을 지정합니다. Automation의 경우 이 값은 Job입니다.|
|ResourceGroup | Runbook 작업의 리소스 그룹 이름을 지정합니다.|
|resourceId | Azure의 리소스 ID를 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|ResourceProvider | 배포하고 관리할 수 있는 리소스를 제공하는 Azure 서비스를 지정합니다. Automation에 대한 값은 Azure Automation입니다.|
|ResourceType | Azure의 리소스 유형을 지정합니다. Automation의 경우 값은 runbook과 연결된 자동화 계정입니다.|
|resultType | 이벤트가 생성될 당시 Runbook 작업의 결과입니다. 가능한 값은 다음과 같습니다.<br>- InProgress입니다.|
|resultDescription | runbook의 출력 스트림을 포함합니다.|
|RunbookName | runbook의 이름입니다.|
|SourceSystem | 제출된 데이터에 대한 원본 시스템을 지정합니다. Automation의 경우 값은 OpsManager입니다.|
|StreamType | 작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행률<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다.|
|시간 | runbook 작업이 실행된 날짜 및 시간입니다.|

**JobLogs** 또는 **JobStreams**의 범주 레코드를 반환하는 로그 검색을 수행하는 경우, 검색에 의해 반환되는 업데이트를 요약하는 타일 집합을 표시하는 **JobLogs** 또는 **JobStreams** 보기를 선택할 수 있습니다.

## <a name="sample-log-searches"></a>샘플 로그 검색

다음 테이블은 이 솔루션에 의해 수집된 작업 레코드에 대한 샘플 로그 검색을 제공합니다.

|쿼리 | Description|
|----------|----------|
|성공적으로 완료된 ScheduledStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|성공적으로 완료된 SequencedStartStop_Parent Runbook에 대한 작업을 찾습니다. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>솔루션 보기

솔루션에 액세스하려면 Automation 계정으로 이동하고, **관련 리소스** 아래에서 **작업 영역**을 선택합니다. Log analytics 페이지의 **일반**에서 **솔루션** 을 선택 합니다. **솔루션** 페이지의 목록에서 **Start-Stop-VM[workspace]** 를 선택합니다.

솔루션을 선택하면 **Start-Stop-VM[workspace]** 솔루션 페이지가 표시됩니다. 여기에서 **StartStopVM** 타일과 같은 중요한 세부 정보를 검토할 수 있습니다. Log Analytics 작업 영역에서와 마찬가지로 이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표시가 나타납니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

여기에서 도넛형 타일을 클릭하면 작업 레코드의 추가 분석을 수행할 수 있습니다. 솔루션 대시보드에는 작업 기록 및 미리 정의된 로그 검색 쿼리가 표시됩니다. 검색 쿼리를 기준으로 검색 하려면 log analytics 고급 포털로 전환 합니다.

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

솔루션이 배포된 후 메일 알림을 변경하려면 배포 중에 만들어진 작업 그룹을 수정합니다.  

> [!NOTE]
> Azure Government 클라우드의 구독은 이 솔루션의 이메일 기능을 지원하지 않습니다.

Azure Portal에서 [모니터] -> [작업 그룹]으로 이동합니다. **StartStop_VM_Notication**이라는 작업 그룹을 선택합니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-monitor.png)

**StartStop_VM_Notification** 페이지의 **세부 정보** 아래에서 **세부 정보 편집**을 클릭합니다. 그러면 **메일/SMS/푸시/음성** 페이지가 열립니다. 메일 주소를 업데이트하고 **확인**을 클릭하여 변경 내용을 저장합니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/change-email.png)

또는 작업 그룹에 추가 작업을 추가할 수 있습니다. 작업 그룹에 대한 자세한 내용은 [작업 그룹](../azure-monitor/platform/action-groups.md)을 참조하세요.

솔루션이 가상 머신을 종료할 때 전송되는 예제 메일은 다음과 같습니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>VM 추가/제외

이 솔루션은 솔루션의 대상이 되는 VM을 추가하거나 특별히 솔루션에서 머신을 제외할 수 있는 기능을 제공합니다.

### <a name="add-a-vm"></a>VM 추가

VM이 실행될 때 시작/중지 솔루션에 포함되도록 하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.

* 솔루션의 각 부모 [runbook](#runbooks) 에는 **vmlist** 매개 변수가 있습니다. 상황에 맞는 부모 runbook을 예약할 때 쉼표로 구분 된 VM 이름 목록을이 매개 변수에 전달할 수 있으며, 이러한 Vm은 솔루션이 실행 될 때 포함 됩니다.

* 여러 VM을 선택하려면 시작하거나 중지하려는 VM이 포함된 리소스 그룹 이름을 사용하여 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**를 설정합니다. 솔루션이 구독의 모든 리소스 그룹에 대해 실행되도록 하려면 이 값을 `*`로 설정할 수도 있습니다.

### <a name="exclude-a-vm"></a>VM 제외

솔루션에서 VM을 제외하려면 해당 VM을 **External_ExcludeVMNames** 변수에 추가할 수 있습니다. 이 변수는 시작/중지 솔루션에서 제외할 특정 Vm의 쉼표로 구분 된 목록입니다. 이 목록은 140개의 VM으로 제한됩니다. 이 쉼표로 구분 된 목록에 140 개 이상의 Vm을 추가 하는 경우 제외 되도록 설정 된 Vm이 실수로 시작 되거나 중지 될 수 있습니다.

## <a name="modify-the-startup-and-shutdown-schedules"></a>시작 및 종료 일정 수정

이 솔루션에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](automation-schedules.md)에 설명된 대로 동일한 단계를 따릅니다. VM을 시작하고 중지하려면 별도의 일정이 있어야 합니다.

특정 시간에 VM을 중지하도록 솔루션을 구성하는 작업이 지원됩니다. 이 시나리오에서는 **중지** 일정만 만들고 해당하는 **시작** 일정은 만들지 않습니다. 이렇게 하려면 다음을 수행해야 합니다.

1. **External_Stop_ResourceGroupNames** 변수에서 종료할 VM에 대한 리소스 그룹을 추가했는지 확인합니다.
2. VM을 종료하려는 시간에 대한 고유한 일정을 만듭니다.
3. **ScheduledStartStop_Parent** Runbook으로 이동하고 **일정**을 클릭합니다. 이 옵션을 사용하면 이전 단계에서 만든 일정을 선택할 수 있습니다.
4. **매개 변수 및 실행 설정**을 선택하고 ACTION 매개 변수를 "중지"로 설정합니다.
5. **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="update-the-solution"></a>솔루션 업데이트

이 솔루션의 이전 버전을 배포한 경우 업데이트된 릴리스를 배포하기 전에 먼저 계정에서 삭제해야 합니다. 다음 단계에 따라 [솔루션을 제거](#remove-the-solution)한 다음, 위의 단계를 수행하여 [솔루션을 배포](#deploy-the-solution)합니다.

## <a name="remove-the-solution"></a>솔루션 제거

솔루션을 더 이상 사용할 필요가 없으면 Automation 계정에서 삭제할 수 있습니다. 솔루션을 삭제하면 Runbook만 제거됩니다. 솔루션을 추가할 때 생성된 일정 또는 변수는 삭제되지 않습니다. 다른 Runbook에서 사용하지 않는 경우 해당 자산을 수동으로 삭제해야 합니다.

솔루션을 삭제하려면 다음 단계를 수행합니다.

1. Automation 계정의 **관련 리소스**에서 **연결 된 작업 영역**을 선택 합니다.
1. **작업 영역으로 이동을**선택 합니다.
1. **일반**에서 **솔루션**을 선택 합니다. 
1. **솔루션** 페이지에서 솔루션 **Start-Stop-VM[Workspace]** 를 선택합니다. **VMManagementSolution[Workspace]** 페이지의 메뉴에서 **삭제**를 클릭합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. **솔루션 삭제** 창에서 솔루션을 삭제할 것임을 확인합니다.
1. 정보가 확인되고 솔루션이 삭제되는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 솔루션 제거 프로세스가 시작되면 **솔루션** 페이지로 돌아갑니다.

이 프로세스의 일부로 Automation 계정 및 Log Analytics 작업 영역이 삭제되지는 않습니다. Log Analytics 작업 영역을 유지하지 않으려는 경우 수동으로 삭제해야 합니다. Azure Portal에서도 이 작업을 수행할 수 있습니다.

1. Azure Portal에서 **Log Analytics 작업 영역**을 검색 하 고 선택 합니다.
1. **Log Analytics 작업 영역** 페이지에서 작업 영역을 선택 합니다.
1. 작업 영역 설정 페이지에 있는 메뉴에서 **삭제**를 선택합니다.

Azure Automation 계정 구성 요소를 유지하지 않으려면 각각을 수동으로 삭제할 수 있습니다. 솔루션에서 만든 Runbook, 변수 및 일정 목록은 [솔루션 구성 요소](#solution-components)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 다른 검색 쿼리를 생성 하 고 Azure Monitor 로그를 사용 하 여 Automation 작업 로그를 검토 하는 방법에 대한 자세한 내용은 [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조 하세요.
- runbook 실행, runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [runbook 작업 추적](automation-runbook-execution.md)을 참조하세요.
- Azure Monitor 로그 및 데이터 수집 소스에 대한 자세한 내용은 [Azure Monitor 로그에서 Azure storage 데이터 수집 개요](../azure-monitor/platform/collect-azure-metrics-logs.md)를 참조 하세요.
