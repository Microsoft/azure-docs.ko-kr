---
title: Azure Automation 업데이트 관리 문제 해결
description: 이 문서에서는 Azure Automation 업데이트 관리와 관련된 문제를 해결하는 방법을 설명합니다.
services: automation
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: b0b1e31a8c10ba372473c36e35c19044ef02898a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003357"
---
# <a name="troubleshoot-update-management-issues"></a>업데이트 관리 문제 해결

이 문서에서는 머신에 업데이트 관리 기능을 배포할 때 발생할 수 있는 문제에 대해 설명합니다. 기본 문제를 확인하기 위한 Hybrid Runbook Worker 에이전트용 에이전트 문제 해결사가 제공됩니다. 문제 해결사에 대해 자세히 알아보려면 [Windows 업데이트 에이전트 문제 해결](update-agent-issues.md) 및 [Linux 업데이트 에이전트 문제 해결](update-agent-issues-linux.md)을 참조하세요. 다른 기능 배포 문제는 [기능 배포 문제 해결](onboarding.md)을 참조하세요.

>[!NOTE]
>Windows 컴퓨터에 업데이트 관리를 배포할 때 문제가 발생 하는 경우 Windows 이벤트 뷰어를 열고 로컬 컴퓨터의 **응용 프로그램 및 서비스 로그** 아래에서 **Operations Manager** 이벤트 로그를 확인 합니다. 이벤트 ID가 4502인 이벤트 및 `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`를 포함하는 이벤트 정보를 찾아봅니다.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>시나리오: "업데이트 솔루션을 사용하도록 설정하지 못했습니다." 오류가 표시됨

### <a name="issue"></a>문제

Automation 계정에서 업데이트 관리를 사용하도록 설정하려고 하면 다음과 같은 오류가 발생합니다.

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* Log Analytics 에이전트에 대한 네트워크 방화벽 요구 사항이 올바르게 구성되지 않았을 수 있습니다. 이러한 상황으로 인해 DNS URL을 확인할 때 에이전트가 실패할 수 있습니다.

* 업데이트 관리 대상이 잘못 구성되었으며 머신이 예상대로 업데이트를 받지 못합니다.

* **규정 준수**에서 머신이 `Non-compliant` 상태를 표시하는 것을 확인할 수도 있습니다. 동시에 **에이전트 데스크톱 분석**에서 에이전트를 `Disconnected`로 보고합니다.

### <a name="resolution"></a>해결 방법

* OS에 따라 [Windows](update-agent-issues.md#troubleshoot-offline) 또는 [Linux](update-agent-issues-linux.md#troubleshoot-offline)에 대한 문제 해결사를 실행합니다.

* [네트워크 구성](../automation-hybrid-runbook-worker.md#network-planning)으로 이동하여 업데이트 관리가 작동하려면 어떤 주소 및 포트를 허용해야 하는지 알아보세요.  

* 범위 구성 문제가 있는지 확인합니다. [범위 구성](../update-management/update-mgmt-scope-configuration.md)은 업데이트 관리에 대해 구성되는 머신을 결정합니다. 컴퓨터가 작업 영역에 표시 되지만 업데이트 관리에 표시 되지 않는 경우 컴퓨터를 대상으로 하는 범위 구성을 설정 해야 합니다. 범위 구성에 대해 알아보려면 [작업 영역에서 컴퓨터 사용](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace)를 참조하세요.

* [온-프레미스 Windows 컴퓨터에서 Hybrid Runbook Worker 제거](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) 또는 컴퓨터에서 [온-프레미스 Linux 컴퓨터에서 Hybrid Runbook Worker 제거](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)의 단계에 따라 작업자 구성을 제거합니다.

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>시나리오: 대체된 업데이트가 업데이트 관리에 누락된 것으로 표시됨

### <a name="issue"></a>문제

이전 업데이트가 대체된 경우에도 Automation 계정에 대해 누락된 것으로 나타납니다. 대체된 업데이트는 동일한 취약성을 해결하는 후속 업데이트를 사용할 수 있기 때문에 설치하지 않아도 되는 업데이트입니다. 업데이트 관리가 대체된 업데이트를 무시하고 대체하는 업데이트 대신 적용하지 않습니다. 관련 문제에 대한 자세한 내용은 [업데이트가 대체됨](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)을 참조하세요.

### <a name="cause"></a>원인

대체된 업데이트가 적용되지 않는 것으로 간주될 수 있는 거부됨으로 제대로 표시되지 않습니다.

### <a name="resolution"></a>해결 방법

대체된 업데이트가 100% 적용할 수 없게 되면 해당 업데이트의 승인 상태를 `Declined`로 변경해야 합니다. 모든 업데이트에 대한 승인 상태를 변경하려면

1. Automation 계정에서 **업데이트 관리**를 선택하여 머신 상태를 확인합니다. [업데이트 평가 보기](../update-management/update-mgmt-view-update-assessments.md)를 참조하세요.

2. 대체된 업데이트를 확인하여 100% 적용되지 않는지 확인합니다. 

3. 업데이트에 대해 문의 사항이 없으면 업데이트를 거부됨으로 표시합니다. 

4. **컴퓨터**를 선택하고 **규정 준수** 열에서 규정 준수를 강제로 다시 검사합니다. [Vm에 대 한 업데이트 관리](../update-management/update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

5. 대체된 다른 업데이트에 대해 위 단계를 반복합니다.

6. 정리 마법사를 실행하여 거부된 업데이트에서 파일을 삭제합니다. 

7. WSUS(Windows Server Update Services)의 경우 대체된 모든 업데이트를 수동으로 정리하여 인프라를 새로 고칩니다.

8. 주기적으로 이 절차를 반복하여 표시 문제를 해결하고 업데이트 관리에 사용되는 디스크 공간의 크기를 최소화합니다.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>시나리오: 포털의 업데이트 관리 아래에 머신이 표시되지 않음

### <a name="issue"></a>문제

머신에 다음과 같은 증상이 있습니다.

* 머신은 VM의 업데이트 관리 보기에서 `Not configured`를 표시합니다.

* Azure Automation 계정의 업데이트 관리 보기에 사용하는 머신이 없습니다.

* 사용하는 머신이 **규정 준수**에서 `Not assessed`로 표시됩니다. 그러나 Hybrid Runbook Worker에 대한 Azure Monitor 로그에는 하트비트 데이터가 표시되지만 업데이트 관리의 경우에는 표시되지 않습니다.

### <a name="cause"></a>원인

이 이슈는 로컬 구성 이슈나 부적절하게 구성된 범위 구성으로 인해 발생할 수 있습니다. 가능한 구체적인 원인은 다음과 같습니다.

* Hybrid Runbook Worker를 다시 등록하고 다시 설치해야 할 수 있습니다.

* 작업 영역에 정의한 할당량에 도달했으며 이로 인해 추가 데이터 저장이 진행되지 않았을 수 있습니다.

### <a name="resolution"></a>해결 방법

1. OS에 따라 [Windows](update-agent-issues.md#troubleshoot-offline) 또는 [Linux](update-agent-issues-linux.md#troubleshoot-offline)에 대한 문제 해결사를 실행합니다.

2. 머신이 올바른 작업 영역에 보고하고 있는지 확인합니다. 이 측면을 확인 하는 방법에 대 한 지침은 [에이전트에서 Azure Monitor에 대 한 연결 확인](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor)을 참조 하세요. 또한 이 작업 영역이 Azure Automation 계정에 연결되어 있는지 확인합니다. 이를 확인하려면 Automation 계정으로 이동하고 **관련 리소스**에서 **연결된 작업 영역**을 선택합니다.

3. Automation 계정에 연결된 Log Analytics 작업 영역에 머신이 표시되는지 확인합니다. Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. 쿼리 결과에 머신이 표시되지 않으면 최근에 체크인되지 않은 것입니다. 로컬 구성 문제가 있는 것이므로 [에이전트를 다시 설치](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)해야 합니다. 

5. 머신이 쿼리 결과에 표시되면 범위 구성에 문제가 있는지 확인합니다. [범위 구성](../update-management/update-mgmt-scope-configuration.md)은 업데이트 관리에 대해 구성되는 머신을 결정합니다. 

6. 머신이 작업 영역에 표시되지만 업데이트 관리에는 표시되지 않는 경우 해당 머신을 대상으로 하도록 범위 구성을 지정해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [작업 영역에서 머신 사용](../update-management/update-mgmt-enable-automation-account.md#enable-machines-in-the-workspace)를 참조하세요.

7. 작업 영역에서 이 쿼리를 실행합니다.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` 결과를 얻는 경우 작업 영역에 정의된 할당량에 도달하여 중지된 데이터를 저장하지 못한 것입니다. 작업 영역에서 **사용량 및 예상 비용**에서 **데이터 볼륨 관리**로 이동하고 할당량을 변경하거나 제거합니다.

9. 그래도 문제가 해결되지 않으면 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md)의 단계에 따라 Windows용 Hybrid Worker를 다시 설치합니다. Linux의 경우 [Linux Hybrid Runbook Worker 배포](../automation-linux-hrw-install.md)의 단계를 따르세요.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>시나리오: Automation 리소스 공급자를 구독에 등록할 수 없음

### <a name="issue"></a>문제

Automation 계정에서 기능 배포를 사용하는 경우 다음과 같은 오류가 발생합니다.

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>원인

Automation 리소스 공급자가 구독에 등록되어 있지 않습니다.

### <a name="resolution"></a>해결 방법

Automation 리소스 공급자를 등록하려면 Azure Portal에서 다음 단계를 수행합니다.

1. 포털의 맨 아래에 있는 Azure 서비스 목록에서 **모든 서비스**를 클릭하고 일반 서비스 그룹에서 **구독**을 선택합니다.

2. 구독을 선택합니다.

3. **설정** 아래에서 **리소스 공급자**를 선택합니다.

4. 리소스 공급자 목록에서 Microsoft.Automation 리소스 공급자가 등록되어 있는지 확인합니다.

5. 나열되지 않은 경우 [리소스 공급자 등록 오류 해결](../../azure-resource-manager/templates/error-register-resource-provider.md)의 단계를 수행하여 Microsoft.Automation 공급자를 등록합니다.

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>시나리오: 동적 일정에 따라 예약된 업데이트에서 일부 머신이 누락됨

### <a name="issue"></a>문제

업데이트 미리 보기에 포함된 머신 중 일부가 예약된 실행 동안 패치된 머신 목록에 표시되지 않습니다.

### <a name="cause"></a>원인

이 이슈는 다음 원인 중 하나 때문일 수 있습니다.

* 동적 쿼리의 범위에서 정의된 구독은 등록된 Automation 리소스 공급자에 대해 구성되지 않습니다.

* 일정이 실행될 때 머신을 사용할 수 없거나 머신에 적절한 태그가 없습니다.

### <a name="resolution"></a>해결 방법

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>등록된 Automation 리소스 공급자에 대해 구독이 구성되지 않음

Automation 리소스 공급자에 대해 구독이 구성되지 않은 경우 해당 구독의 머신에서 정보를 쿼리하거나 가져올 수 없습니다. 다음 단계를 사용하여 구독에 대한 등록을 확인합니다.

1. [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)에서 Azure 서비스 목록에 액세스합니다.

2. **모든 서비스**를 선택한 후 일반 서비스 그룹에서 **구독**을 선택합니다. 

3. 배포 범위에서 정의된 구독을 찾습니다.

4. **설정**에서 **리소스 공급자**를 선택합니다.

5. Microsoft.Automation 리소스 공급자가 등록되어 있는지 확인합니다.

6. 나열되지 않은 경우 [리소스 공급자 등록 오류 해결](../../azure-resource-manager/templates/error-register-resource-provider.md)의 단계를 수행하여 Microsoft.Automation 공급자를 등록합니다.

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>예약 실행 시 머신을 사용할 수 없거나 태그가 올바르게 지정되지 않음

구독이 Automation 리소스 공급자에 대해 구성되어 있지만 지정된 [동적 그룹](../update-management/update-mgmt-groups.md)을 사용하여 업데이트 일정을 실행할 경우 일부 머신이 누락되면 다음 절차를 사용합니다.

1. Azure Portal에서 Automation 계정을 열고 **업데이트 관리**를 선택합니다.

2. 업데이트 배포가 실행된 정확한 시간을 확인하려면 [업데이트 관리 기록](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment)를 확인합니다.

3. 업데이트 관리에서 누락된 것으로 의심되는 머신의 경우 ARG(Azure Resource Graph)를 사용하여 [머신 변경 내용을 찾습니다](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. 업데이트 배포를 실행하기 전에 하루와 같은 상당히 긴 기간 동안의 변경 내용을 검색합니다.

5. 이 기간 동안 삭제 또는 업데이트 변경과 같은 머신의 시스템 변경 내용을 검색 결과에서 확인합니다. 이러한 변경 내용은 머신 상태 또는 태그를 변경하여 업데이트를 배포할 때 머신 목록에서 머신이 선택되지 않도록 합니다.

6. 머신 상태 또는 태그 문제를 해결하기 위해 필요에 따라 머신 및 리소스 설정을 조정합니다.

7. 지정된 동적 그룹이 있는 배포에 모든 머신이 포함되도록 업데이트 일정을 다시 실행합니다.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>시나리오: 예상 머신이 동적 그룹에 대한 미리 보기에 표시되지 않음

### <a name="issue"></a>문제

동적 그룹의 선택한 범위에 대한 VM이 Azure Portal 미리 보기 목록에 표시되지 않습니다. 이 목록은 선택한 범위에 대한 ARG 쿼리로 검색된 모든 머신으로 구성됩니다. Hybrid Runbook Worker가 설치되어 있고 사용자에게 액세스 권한이 있는 머신에 대해 범위가 필터링됩니다.

### <a name="cause"></a>원인

이 이슈의 잠재적 원인은 다음과 같습니다.

* 선택한 범위에 대한 올바른 액세스 권한이 없습니다.
* ARG 쿼리가 필요한 머신을 검색하지 않습니다.
* Hybrid Runbook Worker가 머신에 설치되어 있지 않습니다.

### <a name="resolution"></a>해결 방법 

#### <a name="incorrect-access-on-selected-scopes"></a>선택한 범위에 대한 잘못된 액세스

Azure Portal에는 지정된 범위에서 쓰기 권한이 있는 머신만 표시됩니다. 범위에 대한 올바른 액세스 권한이 없는 경우 [자습서: RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](../../role-based-access-control/quickstart-assign-role-user-portal.md)를 참조하세요.

#### <a name="arg-query-doesnt-return-expected-machines"></a>ARG 쿼리가 예상 머신을 반환하지 않음

아래 단계에 따라 쿼리가 제대로 작동하고 있는지 확인합니다.

1. Azure Portal의 Resource Graph 탐색기 블레이드에서 아래와 같이 형식이 지정된 ARG 쿼리를 실행합니다. 이 쿼리는 업데이트 관리에서 동적 그룹을 만들 때 선택한 필터를 모방합니다. [업데이트 관리에서 동적 그룹 사용](../update-management/update-mgmt-groups.md)을 참조하세요.

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   다음은 예제입니다.

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. 검색 중인 머신이 쿼리 결과에 나열되는지 확인합니다. 

3. 머신이 나열되지 않는 경우 동적 그룹에서 선택된 필터에 이슈가 있을 수 있습니다. 필요에 따라 그룹 구성을 조정합니다.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker가 머신에 설치되지 않음

머신이 ARG 쿼리 결과에 표시되지만 여전히 동적 그룹 미리 보기에는 표시되지 않습니다. 이 경우 머신이 Hybrid Worker로 지정되지 않을 수 있으므로 Azure Automation 및 업데이트 관리 작업을 실행할 수 없습니다. 표시하려는 머신이 Hybrid Runbook Worker로 설정되었는지 확인하려면

1. Azure Portal에서 올바르게 표시되지 않는 머신에 대한 Automation 계정으로 이동합니다.

2. **프로세스 자동화**에서 **Hybrid Worker 그룹**을 선택합니다.

3. **시스템 Hybrid Worker 그룹** 탭을 선택합니다.

4. 해당 머신에 대한 Hybrid Worker가 있는지 확인합니다.

5. 머신이 Hybrid Worker로 설정되어 있지 않은 경우에는[Hybrid Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화](../automation-hybrid-runbook-worker.md)의 지침을 사용하여 조정합니다.

6. Hybrid Runbook Worker 그룹에 머신을 가입합니다.

7. 미리 보기에 표시되지 않은 모든 머신에 대해 위 단계를 반복합니다.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>시나리오: 업데이트 관리 구성 요소가 사용하도록 설정되었으나 VM이 계속 구성 중으로 표시됨

### <a name="issue"></a>문제

배포를 시작하고 15분 후에 VM에서 다음 메시지가 계속 표시됩니다.

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* Automation 계정과의 통신이 차단되었습니다.

* 원본 컴퓨터 ID가 다른 중복된 컴퓨터 이름이 있습니다. 이 시나리오는 특정 컴퓨터 이름의 VM이 다른 리소스 그룹에 만들어지고 구독의 동일한 Logistics Agent 작업 영역에 보고하는 경우에 발생합니다.

* 배포되는 VM 이미지는 설치된 Windows용 Log Analytics 에이전트를 사용하여 시스템 준비(sysprep)로 준비되지 않은 복제된 머신에서 가져온 것일 수 있습니다.

### <a name="resolution"></a>해결 방법

VM의 정확한 문제를 파악하기 위해 Automation 계정에 연결된 Log Analytics 작업 영역에서 다음 쿼리를 실행합니다.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Automation 계정에 대한 통신이 차단됨

[네트워크 계획](../update-management/update-mgmt-overview.md#ports)으로 이동하여 업데이트 관리가 작동하려면 어떤 주소 및 포트를 허용해야 하는지 알아보세요.

#### <a name="duplicate-computer-name"></a>중복 컴퓨터 이름

VM의 이름을 변경하여 환경에서 고유한 이름을 유지하도록 합니다.

#### <a name="deployed-image-from-cloned-machine"></a>복제된 머신에서 배포된 이미지

복제된 이미지를 사용하는 경우 다른 컴퓨터 이름의 원본 컴퓨터 ID가 같습니다. 이 경우 다음과 같습니다.

1. Log Analytics 작업 영역의 범위 구성(표시되는 경우) `MicrosoftDefaultScopeConfig-Updates`에 대한 저장된 검색에서 VM을 제거합니다. 저장된 검색은 작업 영역의 **일반**에서 찾을 수 있습니다.

2. 다음 cmdlet을 실행합니다.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. `Restart-Service HealthService`를 실행하여 상태 서비스를 다시 시작합니다. 이 작업을 수행하면 키가 다시 만들어지고 새 UUID가 생성됩니다.

4. 이 방법이 작동하지 않으면 먼저 이미지에서 sysprep을 실행한 다음, MMA를 설치합니다.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>시나리오: 다른 Azure 테넌트에 있는 머신의 업데이트 배포를 만들 때 연결된 구독 오류가 발생합니다.

### <a name="issue"></a>문제

다른 Azure 테넌트에 있는 머신의 업데이트 배포를 만들려고 하면 다음 오류가 발생합니다.

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>원인

이 오류는 업데이트 배포에 포함된 다른 테넌트에 Azure VM이 있는 업데이트 배포를 만들 때 발생합니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법을 사용하여 이러한 항목을 예약합니다. `ForUpdateConfiguration` 매개 변수에서 [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule?view=azps-3.7.0) cmdlet을 사용하여 일정을 만들 수 있습니다. 그런 다음, [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet을 사용하여 다른 테넌트의 머신을 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>시나리오: 설명되지 않고 다시 부팅됨

### <a name="issue"></a>문제

**다시 부팅 제어** 옵션을**다시 부팅 안 함**으로 설정한 경우에도 업데이트가 설치된 후에 머신이 다시 부팅됩니다.

### <a name="cause"></a>원인

Windows 업데이트는 여러 레지스트리 키로 수정할 수 있으며, 이중 하나는 다시 부팅 동작을 수정할 수 있습니다.

### <a name="resolution"></a>해결 방법

[레지스트리를 편집하여 자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) 및 [다시 시작을 관리하는 데 사용되는 레지스트리 키](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)에 나열된 레지스트리 키를 검토하여 머신이 올바르게 구성되었는지 확인합니다.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>시나리오: 업데이트 배포에서 머신이 "시작하지 못함"을 표시함

### <a name="issue"></a>문제

머신이 `Failed to start` 상태를 표시합니다. 머신에 대한 특정 세부 정보를 볼 때 다음과 같은 오류가 표시됩니다.

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>원인

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 머신이 더 이상 존재하지 않습니다.
* 머신이 꺼져 있고 연결할 수 없습니다.
* 머신에 네트워크 연결 이슈가 있으므로 머신의 Hybrid Worker에 연결할 수 없습니다.
* 원본 컴퓨터 ID를 변경한 MMA가 업데이트되었습니다.
* Automation 계정에서 200의 동시 작업 제한에 도달 하는 경우 업데이트 실행이 제한 되었습니다. 각 배포는 하나의 작업으로 간주되며, 업데이트 배포의 각 머신은 하나의 작업으로 계산됩니다. Automation 계정에서 현재 실행되고 있는 다른 모든 Automation 작업 또는 업데이트 배포는 동시 작업 제한으로 계산됩니다.

### <a name="resolution"></a>해결 방법

해당하는 경우 업데이트 배포에 [동적 그룹](../update-management/update-mgmt-groups.md)을 사용합니다. 또한 다음 단계를 수행할 수 있습니다.

1. 머신이 존재하고 연결할 수 있는지 확인합니다. 
2. 머신이 존재하지 않는 경우 배포를 편집하고 머신을 제거합니다.
3. 업데이트 관리에 필요한 포트 및 주소 목록은 [네트워크 계획](../update-management/update-mgmt-overview.md#ports) 섹션을 참조하고 머신이 이러한 요구 사항을 충족하는지 확인합니다.
4. Hybrid Runbook Worker 에이전트 문제 해결사를 사용하여 Hybrid Runbook Worker에 대한 연결을 확인합니다. 이 문제 해결사에 대한 자세한 내용은 [업데이트 에이전트 문제 해결](update-agent-issues.md)을 참조하세요.
5. Log Analytics에서 다음 쿼리를 실행하여 환경에서 원본 컴퓨터 ID가 변경된 머신을 찾을 수 있습니다. `Computer` 값이 같지만 `SourceComputerId` 값이 다른 컴퓨터를 찾습니다.

   ```kusto
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

6. 영향을 받는 머신을 찾은 후에는 해당 머신을 대상으로 하는 업데이트 배포를 편집한 다음, `SourceComputerId`가 올바른 값을 반영하도록 머신을 제거했다가 다시 추가합니다.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>시나리오: 배포 없이 업데이트가 설치됨

### <a name="issue"></a>문제

업데이트 관리에서 Windows 머신을 등록하면 배포 없이 설치된 업데이트가 표시됩니다.

### <a name="cause"></a>원인

Windows에서는 업데이트가 사용 가능해지는 즉시 자동으로 설치됩니다. 머신에 업데이트를 배포하도록 예약하지 않은 경우 이 동작으로 인해 혼란이 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` 레지스트리 키의 기본 설정은 4: `auto download and install`입니다.

업데이트 관리 클라이언트의 경우 이 키를 3: `auto download but do not auto install`로 설정하는 것이 좋습니다.

자세한 내용은 [자동 업데이트 구성](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)을 참조하세요.

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>시나리오: 컴퓨터가 이미 다른 계정에 등록되어 있습니다.

### <a name="issue"></a>문제

다음과 같은 오류 메시지가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>원인

머신이 이미 업데이트 관리를 위한 다른 작업 영역에 배포되었습니다.

### <a name="resolution"></a>해결 방법

1. [머신이 업데이트 관리에 표시되지 않음](#nologs)의 단계에 따라 머신이 올바른 작업 영역으로 보고하는지 확인합니다.
2. 머신에서 [Hybrid Runbook 그룹을 삭제](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)하여 아티팩트를 정리한 후 다시 시도합니다.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>시나리오: 머신이 서비스와 통신할 수 없음

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>원인

네트워크 통신을 차단하는 프록시, 게이트웨이 또는 방화벽이 있을 수 있습니다. 

### <a name="resolution"></a>해결 방법

네트워크를 검토하고 적합한 포트 및 주소가 허용되었는지 확인합니다. 업데이트 관리 및 Hybrid Runbook Worker에 필요한 포트 및 주소 목록은 [네트워크 요구 사항](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>시나리오: 자체 서명된 인증서를 만들 수 없습니다.

### <a name="issue"></a>문제

다음과 같은 오류 메시지 중 하나가 표시됩니다.

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>원인

Hybrid Runbook Worker가 자체 서명 인증서를 생성할 수 없습니다.

### <a name="resolution"></a>해결 방법

시스템 계정에 **C:\ProgramData\Microsoft\Crypto\RSA** 폴더에 대한 읽기 액세스 권한이 있는지 확인하고 다시 시도합니다.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>시나리오: 예약된 업데이트가 MaintenanceWindowExceeded 오류로 인해 실패함

### <a name="issue"></a>문제

업데이트를 위한 기본 유지 관리 기간은 120분입니다. 유지 관리 기간을 최대 6시간 또는 360분으로 늘릴 수 있습니다.

### <a name="resolution"></a>해결 방법

모든 실패한 예약 업데이트 배포를 편집하고 유지 관리 기간을 늘립니다.

유지 관리 기간에 대한 자세한 내용은 [업데이트 설치](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment)를 참조하세요.

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>시나리오: 머신이 "평가되지 않음"으로 표시되고 HRESULT 예외를 표시함

### <a name="issue"></a>문제

* 머신이 **규정 준수**에서 `Not assessed`로 표시되며 그 아래에 예외 메시지가 표시됩니다.
* 포털에 HRESULT 오류 코드가 표시됩니다.

### <a name="cause"></a>원인

업데이트 에이전트(Windows의 Windows 업데이트 에이전트, Linux 배포용 패키지 관리자)가 올바르게 구성되어 있지 않습니다. 업데이트 관리는 머신의 업데이트 에이전트를 사용하여 필요한 업데이트, 패치의 상태 및 배포된 패치의 결과를 제공합니다. 이 정보가 없으면 업데이트 관리에서 필요하거나 설치된 패치를 제대로 보고할 수 없습니다.

### <a name="resolution"></a>해결 방법

머신에서 로컬로 업데이트를 수행합니다. 이 작업이 실패하면 일반적으로 업데이트 에이전트 구성 오류가 있음을 의미합니다.

이 문제는 네트워크 구성 및 방화벽 이슈로 인해 발생하는 경우가 많습니다. 다음 검사를 사용하여 문제를 해결하세요.

* Linux의 경우 해당 설명서를 확인하여 패키지 리포지토리의 네트워크 엔드포인트에 연결할 수 있는지 확인합니다.

* Windows의 경우 [업데이트가 인트라넷 엔드포인트(WSUS/SCCM)에서 다운로드되지 않음](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)에 나열된 에이전트 구성을 확인합니다.

  * Windows 업데이트에 대해 머신이 구성된 경우 [HTTP/프록시와 관련된 이슈](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에 설명된 엔드포인트에 연결할 수 있는지 확인합니다.
  * 머신이 WSUS(Windows Server Update Services)용으로 구성된 경우 [WUServer 레지스트리 키](/windows/deployment/update/waas-wu-settings)로 구성된 WSUS 서버에 연결할 수 있는지 확인합니다.

HRESULT가 표시되는 경우 전체 예외 메시지를 보려면 빨간색으로 표시된 예외를 두 번 클릭합니다. 다음 표에서 잠재적 해결 방법 또는 권장 작업을 검토합니다.

|예외  |해결 방법 또는 작업  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | [Windows 업데이트 오류 코드 목록](https://support.microsoft.com/help/938205/windows-update-error-code-list)에서 관련 오류 코드를 검색하여 예외의 원인에 대한 추가 세부 정보를 찾을 수 있습니다.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 네트워크 연결 이슈를 나타냅니다. 머신이 네트워크를 통해 업데이트 관리에 연결되어 있는지 확인합니다. 필요한 포트 및 주소 목록은 [네트워크 계획](../update-management/update-mgmt-overview.md#ports) 섹션을 참조하세요.        |
|`0x8024001E`| 서비스 또는 시스템이 종료 중이어서 업데이트 작업이 완료되지 않았습니다.|
|`0x8024002E`| Windows 업데이트 서비스를 사용할 수 없습니다.|
|`0x8024402C`     | WSUS 서버를 사용하는 경우 레지스트리 키 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 아래에 있는 `WUServer` 및 `WUStatusServer`의 레지스트리 값에 올바른 WSUS 서버를 지정합니다.        |
|`0x80072EE2`|네트워크 연결 이슈 또는 구성된 WSUS 서버와의 통신 이슈가 있습니다. WSUS 설정을 확인하고 클라이언트에서 서비스에 액세스할 수 있는지 확인합니다.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows 업데이트 서비스(wuauserv)가 실행되고 있고 사용 안 함으로 설정되어 있지 않은지 확인합니다.        |
|`0x80070005`| 다음 중 하나로 인해 액세스 거부 오류가 발생할 수 있습니다.<br> 감염된 컴퓨터<br> Windows 업데이트 설정이 올바르게 구성되지 않음<br> %WinDir%\SoftwareDistribution 폴더의 파일 사용 권한 오류<br> 시스템 드라이브(C:)의 디스크 공간 부족
|다른 제네릭 예외     | 인터넷에서 가능한 해결 방법을 검색하고 현지 IT 지원 팀과 함께 해결합니다.         |

**%Windir%\Windowsupdate.log** 파일을 검토하면 가능한 원인을 확인하는 데 도움이 될 수도 있습니다. 로그를 읽는 방법에 대한 자세한 내용은 [Windowsupdate.log 파일을 읽는 방법](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)을 참조하세요.

또한 [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)를 다운로드한 후 실행하여 머신의 Windows 업데이트에 이슈가 있는지 확인할 수 있습니다.

> [!NOTE]
> [Windows 업데이트 문제 해결사](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) 설명서는 해당 기능이 Windows 클라이언트용이지만 Windows Server에도 적용된다고 설명합니다.

## <a name="scenario-update-run-returns-failed-status-linux"></a>시나리오: 업데이트 실행 시 실패 상태 반환(Linux)

### <a name="issue"></a>문제

업데이트 실행은 시작되지만 실행 중 오류가 발생합니다.

### <a name="cause"></a>원인

가능한 원인:

* 패키지 관리자가 비정상 상태입니다.
* 업데이트 에이전트(Windows용 WUA, Linux용 배포판 특정 패키지 관리자)가 잘못 구성되었습니다.
* 특정 패키지가 클라우드 기반 패치를 방해할 수 있습니다.
* 머신에 연결할 수 없습니다.
* 업데이트에 해결되지 않은 종속성이 있습니다.

### <a name="resolution"></a>해결 방법

업데이트 실행이 제대로 시작된 후 실행 중에 실패할 경우 실행에서 영향을 받는 머신의 [작업 출력을 확인합니다](../update-management/update-mgmt-deploy-updates.md#view-results-of-a-completed-update-deployment). 머신에서 특정 오류 메시지를 찾아 조치를 취할 수 있습니다. 업데이트 관리에서 업데이트 배포에 성공하려면 패키지 관리자가 정상 상태여야 합니다.

작업이 실패하기 직전에 특정 패치, 패키지 또는 업데이트가 표시되는 경우 다음 업데이트 배포에서 이러한 항목을 [제외](../update-management/update-mgmt-deploy-updates.md#schedule-an-update-deployment)할 수 있습니다. Windows 업데이트에서 로그 정보를 수집하려면 [Windows 업데이트 로그 파일](/windows/deployment/update/windows-update-logs)을 참조하세요.

패치 이슈를 해결할 수 없는 경우 **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** 파일의 복사본을 만들고, 다음 업데이트 배포가 시작되기 전에 문제를 해결할 수 있도록 유지합니다.

## <a name="patches-arent-installed"></a>패치가 설치되지 않음

### <a name="machines-dont-install-updates"></a>머신이 업데이트를 설치하지 않음

머신에서 직접 업데이트를 실행해 봅니다. 머신이 업데이트를 적용할 수 없는 경우 [문제 해결 가이드의 잠재적 오류 목록](#hresult)을 참조하세요.

업데이트가 로컬로 실행되는 경우 [업데이트 관리에서 VM 제거](../update-management/update-mgmt-remove-vms.md)의 지침에 따라 머신에서 에이전트를 제거했다가 다시 설치해 봅니다.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>업데이트를 사용할 수 있는 것을 알지만, 내 머신에는 업데이트가 사용 가능한 것으로 표시되지 않음

이 문제는 머신이 WSUS 또는 Microsoft Endpoint Configuration Manager에서 업데이트를 가져오도록 구성되었지만 WSUS 및 Configuration Manager가 업데이트를 승인하지 않은 경우에 주로 발생합니다.

`UseWUServer` 레지스트리 키를 [이 문서의 레지스트리를 편집하여 자동 업데이트 구성 섹션](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)에 나오는 레지스트리 키와 상호 참조하여 머신이 WSUS 및 SCCM에 대해 구성되었는지 확인할 수 있습니다.

업데이트가 WSUS에서 승인되지 않으면 설치되지 않습니다. 다음 쿼리를 실행하여 Log Analytics에서 승인되지 않은 업데이트를 확인할 수 있습니다.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>업데이트가 설치된 것으로 표시되지만, 머신에서 업데이트를 찾을 수 없음

업데이트가 종종 다른 업데이트로 대체됩니다. 자세한 내용은 Windows 업데이트 문제 해결 가이드의 [업데이트가 대체됨](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)을 참조하세요.

### <a name="installing-updates-by-classification-on-linux"></a>Linux에서 분류를 기준으로 업데이트 설치

분류("중요 업데이트 및 보안 업데이트")를 기준으로 Linux에 업데이트를 배포할 때, 특히 CentOS와 관련하여 중요한 주의 사항이 있습니다. 이러한 제한 사항은 [업데이트 관리 개요 페이지](../update-management/update-mgmt-overview.md#linux)에 설명되어 있습니다.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602가 지속적으로 누락됨

KB2267602는 [Windows Defender 정의 업데이트](https://www.microsoft.com/wdsi/definitions)이며 매일 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가의 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
