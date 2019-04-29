---
title: Azure Automation 사용한 VM 시작 및 중지 문제 해결
description: 이 문서에서는 Azure Automation의 VM 중지 및 시작과 관련된 문제 해결 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03bad12b7fcba5a247e05884aa0eb0493163a5c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60579000"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>작업 시간 외 VM 시작/중지 문제 해결

## <a name="deployment-failure"></a>시나리오: VM 시작/중지 솔루션이 제대로 배포되지 않음

### <a name="issue"></a>문제

[작업 시간 외 VM 시작/중지 솔루션](../automation-solution-vm-management.md)을 배포할 때 다음 오류 중 하나가 표시됩니다.

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>원인

다음 이유 중 하나로 인해 배포가 실패할 수 있습니다.

1. 선택한 지역에 동일한 이름의 Automation 계정이 이미 있습니다.
2. VM 시작/중지 솔루션 배포를 허용하지 않는 정책이 적용되었습니다.
3. `Microsoft.OperationsManagement`, `Microsoft.Insights` 또는 `Microsoft.Automation` 리소스 종류가 등록되지 않았습니다.
4. Log Analytics 작업 영역에 잠금이 있습니다.

### <a name="resolution"></a>해결 방법

다음 목록에서 문제의 잠재적인 해결 방법이나 검색 위치를 확인합니다.

1. Automation 계정은 다른 리소스 그룹에 있는 경우에도 Azure 지역 내에서 고유해야 합니다. 대상 지역의 기존 Automation 계정을 확인합니다.
2. 기존 정책 때문에 VM 시작/중지 솔루션을 배포하는 데 필요한 리소스를 사용할 수 없습니다. Azure Portal의 정책 할당으로 이동하여 이 리소스의 배포를 허용하지 않는 정책 할당이 있는지 여부를 확인합니다. 이에 대한 자세한 내용은 [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)를 참조하세요.
3. VM 시작/중지 솔루션을 배포하려면 다음 Azure 리소스 네임스페이스에 구독을 등록해야 합니다.
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   공급자를 등록할 때 발생하는 오류에 대한 자세한 내용은 [리소스 공급자 등록 오류 해결](../../azure-resource-manager/resource-manager-register-provider-errors.md)을 참조하세요.
4. Log Analytics 작업 영역에 잠금이 설정된 경우 Azure Portal에서 해당 작업 영역으로 이동하여 리소스에 대한 잠금을 제거합니다.

## <a name="all-vms-fail-to-startstop"></a>시나리오: 모든 VM을 시작/중지하지는 못 함

### <a name="issue"></a>문제

VM 시작/중지 솔루션을 구성했지만 구성된 모든 VM이 시작 또는 중지되지는 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

1. 일정이 올바르게 구성되지 않았습니다.
2. 실행 계정이 올바르게 구성되어 있지 않을 수 있습니다.
3. Runbook 실행 중에 오류가 발생한 것일 수 있습니다.
4. VM이 제외되었을 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 목록에서 문제의 잠재적인 해결 방법이나 검색 위치를 확인합니다.

* VM 시작/중지 솔루션의 일정을 올바르게 구성했는지 확인합니다. 일정을 구성하는 방법을 알아보려면 [일정](../automation-schedules.md) 문서를 참조하세요.

* 확인 합니다 [작업 스트림의](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 에 오류를 찾아보십시오. 포털에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다. **작업** 페이지에서 다음 runbook 중 하나에서 작업을 찾아봅니다.

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* [실행 계정](../manage-runas-account.md)에 시작 또는 중지하려는 VM에 대한 적절한 권한이 있는지 확인합니다. 리소스에 대한 권한을 확인하는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하고, **계정 실행** 아래에서 **실행 계정**을 선택한 후 적절한 실행 계정을 클릭하여 이 값을 검색할 수 있습니다.

* VM을 명시적으로 제외한 경우에는 시작 또는 중지되지 않을 수 있습니다. 제외되는 VM은 솔루션이 배포된 Automation 계정의 **External_ExcludeVMNames** 변수에 설정합니다. 다음 예제에서는 PowerShell 사용하여 해당 값을 쿼리하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>시나리오: VM 중 일부가 시작 또는 중지되지 않음

### <a name="issue"></a>문제

VM 시작/중지 솔루션을 구성했지만 구성된 일부 VM이 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

이 오류는 다음과 같은 원인 중 하나로 인해 발생할 수 있습니다.

1. 시퀀스 시나리오를 사용하는 경우 태그가 누락되거나 올바르지 않을 수 있습니다.
2. VM을 제외한 것일 수 있습니다.
3. 실행 계정에 VM에 대한 충분한 권한이 없을 수 있습니다.
4. VM에 시작 또는 중지를 방해하는 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 목록에서 문제의 잠재적인 해결 방법이나 검색 위치를 확인합니다.

* 작업 시간 외 VM 시작/중지 솔루션의 [시퀀스 시나리오](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)를 사용하는 경우 시작 또는 중지하려는 각 VM에 적절한 태그가 있는지 확인해야 합니다. 시작하려는 VM에는 `sequencestart` 태그가 있는지, 중지하려는 VM에는 `sequencestop` 태그가 있는지 확인합니다. 두 태그 값은 모두 양의 정수여야 합니다. 다음 예제와 비슷한 쿼리를 사용하여 모든 VM과 태그 및 해당 값을 검색할 수 있습니다.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM을 명시적으로 제외한 경우에는 시작 또는 중지되지 않을 수 있습니다. 제외되는 VM은 솔루션이 배포된 Automation 계정의 **External_ExcludeVMNames** 변수에 설정합니다. 다음 예제에서는 PowerShell 사용하여 해당 값을 쿼리하는 방법을 보여 줍니다.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM을 시작 및 중지하려면 Automation 계정의 실행 계정에 VM에 대한 적절한 사용 권한이 있어야 합니다. 리소스에 대한 권한을 확인하는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하고, **계정 실행** 아래에서 **실행 계정**을 선택한 후 적절한 실행 계정을 클릭하여 이 값을 검색할 수 있습니다.

* VM을 시작 또는 할당 취소하는 데 문제가 발생하는 경우 VM 자체의 문제로 인해 이 동작이 발생할 수 있습니다. 일부 예제 또는 잠재적 문제는 시스템 종료를 시도하거나 서비스 중단이 발생할 때 업데이트가 적용되는 경우입니다. VM 리소스로 이동한 후 **활동 로그**를 확인하여 로그에 오류가 있는지 검토합니다. 이벤트 로그에 오류가 있는지 확인하기 위해 VM에 로그인을 시도할 수도 있습니다. VM 문제를 해결 하는 방법에 대 한 자세한 내용은를 참조 하세요. [문제 해결 Azure virtual machines](../../virtual-machines/troubleshooting/index.md)

* 확인 합니다 [작업 스트림의](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 에 오류를 찾아보십시오. 포털에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다.

## <a name="custom-runbook"></a>시나리오: 내 사용자 지정 Runbook이 VM을 시작 또는 중지하지 못함

### <a name="issue"></a>문제

사용자 지정 Runbook을 작성했거나, PowerShell 갤러리에서 다운로드했으나 제대로 작동하지 않습니다.

### <a name="cause"></a>원인

실패 원인은 여러 가지 중 하나일 수 있습니다. Azure Portal에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다. **작업** 페이지에서 Runbook의 작업을 찾아 작업 실패를 확인합니다.

### <a name="resolution"></a>해결 방법

Azure Automation에서 [작업 시간 외 VM 시작/중지 솔루션](../automation-solution-vm-management.md)을 사용하여 VM을 시작 및 중지하는 것이 좋습니다. 이 솔루션은 Microsoft에서 작성한 것입니다. 사용자 지정 Runbook은 Microsoft에서 지원하지 않습니다. [Runbook 문제 해결](runbooks.md) 문서를 참조하여 사용자 지정 Runbook에 대한 솔루션을 찾을 수 있습니다. 이 문서에서는 모든 유형의 Runbook에 대한 일반 지침 및 문제 해결 방법을 제공합니다. 확인 합니다 [작업 스트림의](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 에 오류를 찾아보십시오. 포털에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다.

## <a name="dont-start-stop-in-sequence"></a>시나리오: VM이 올바른 순서로 시작 또는 중지되지 않음

### <a name="issue"></a>문제

솔루션에서 구성한 VM이 올바른 순서로 시작 또는 중지되지 않습니다.

### <a name="cause"></a>원인

VM의 잘못된 태그 지정 때문입니다.

### <a name="resolution"></a>해결 방법

다음 단계를 수행하여 솔루션이 올바르게 구성되었는지 확인합니다.

1. 시작 또는 중지하려는 모든 VM에 상황에 따라 `sequencestart` 또는 `sequencestop` 태그가 있는지 확인합니다. 이러한 태그 값으로 양의 정수가 필요합니다. VM은 이 값의 오름차순으로 처리됩니다.
2. 시작 또는 중지할 VM의 리소스 그룹이 상황에 따라 `External_Start_ResourceGroupNames` 또는 `External_Stop_ResourceGroupNames` 변수에 있는지 확인합니다.
3. WHATIF 매개 변수를 True로 설정하여 `SequencedStartStop_Parent` Runbook을 실행하는 방식으로 변경 내용을 미리 보면서 테스트합니다.

이 솔루션을 사용하여 VM을 순서대로 시작 및 중지하는 방법에 대한 자세한 내용 및 추가 지침을 보려면 [순서대로 VM 시작 및 중지](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)를 참조하세요.

## <a name="403"></a>시나리오: 403 사용할 수 없음 상태를 나타내며 VM 시작/중지 작업이 실패함 

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지 솔루션 Runbook에 대해 `403 forbidden` 오류를 나타내며 실패한 작업을 찾습니다.

### <a name="cause"></a>원인

이 문제는 잘못 구성되었거나 만료된 실행 계정으로 인해 발생할 수 있습니다. 또한 Automation 계정 실행 계정이 VM 리소스에 대해 부적절한 사용 권한을 갖기 때문일 수 있습니다.

### <a name="resolution"></a>해결 방법

실행 계정이 제대로 구성되어 있는지 확인하려면 Azure Portal에서 Automation 계정으로 이동한 후 **계정 설정** 아래에서 **실행 계정**을 선택합니다. 다음은 실행 계정의 상태입니다. 실행 계정이 잘못 구성되었거나 만료된 경우 이러한 상태가 표시됩니다.

실행 계정이 [잘못 구성된](../manage-runas-account.md#misconfiguration) 경우 실행 계정을 삭제한 후 다시 만들어야 합니다.

실행 계정에 대해 인증서가 만료되면 [자체 서명된 인증서 갱신](../manage-runas-account.md#cert-renewal)의 단계에 따라 인증서를 갱신합니다.

이 문제는 누락된 사용 권한으로 인해 발생할 수 있습니다. 리소스에 대한 권한을 확인하는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용하여 사용자에게 할당된 역할 보기](../../role-based-access-control/check-access.md)를 참조하세요. 실행 계정에서 사용하는 서비스 주체의 애플리케이션 ID를 제공해야 합니다. Azure Portal에서 Automation 계정으로 이동하고, **계정 실행** 아래에서 **실행 계정**을 선택한 후 적절한 실행 계정을 클릭하여 이 값을 검색할 수 있습니다.

## <a name="other"></a>시나리오: 위 목록에 없는 문제가 발생함

### <a name="issue"></a>문제

작업 시간 외 VM 시작/중지 솔루션을 사용할 때 이 페이지에 나열되지 않은 문제 또는 예기치 않은 결과가 발생합니다.

### <a name="cause"></a>원인

이전 및 오래된 버전의 솔루션을 사용하면 많은 시간 오류가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

많은 오류를 해결하려면 솔루션을 제거 및 업데이트하는 것이 좋습니다. 솔루션을 업데이트하는 방법을 알아보려면 [작업 시간 외 VM 시작/중지 솔루션](../automation-solution-vm-management.md#update-the-solution)을 참조하세요. 또한 확인할 수 있습니다는 [작업 스트림의](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) 에 오류를 찾아보십시오. 포털에서 Automation 계정으로 이동한 후 **프로세스 자동화**에서 **작업**을 선택합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
