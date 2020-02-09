---
title: 세션 호스트 Azure Automation 크기 조정-Azure
description: Azure Automation를 사용 하 여 Windows 가상 데스크톱 세션 호스트 크기를 자동으로 조정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: helohr
ms.openlocfilehash: c201df03bb156bac3f63d03cc4ca35215792f65c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061524"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure Automation를 사용 하 여 세션 호스트 크기 조정

Vm (가상 머신)을 확장 하 여 총 Windows 가상 데스크톱 배포 비용을 줄일 수 있습니다. 즉, 사용량이 적은 시간에 세션 호스트 Vm을 종료 하 고 할당을 취소 한 다음, 사용량이 많은 시간 동안 다시 설정 하 고 다시 할당 합니다.

이 문서에서는 Windows 가상 데스크톱 환경에서 세션 호스트 가상 컴퓨터의 크기를 자동으로 조정 하는 Azure Automation 및 Azure Logic Apps를 사용 하 여 빌드한 크기 조정 도구에 대해 알아봅니다. 크기 조정 도구를 사용 하는 방법에 대 한 자세한 내용은 [필수 구성 요소](#prerequisites)로 건너뜁니다.

## <a name="how-the-scaling-tool-works"></a>크기 조정 도구의 작동 원리

크기 조정 도구는 세션 호스트 VM 비용을 최적화 하려는 고객을 위한 저비용의 자동화 옵션을 제공 합니다.

크기 조정 도구를 사용 하 여 다음을 수행할 수 있습니다.
 
- 최대 업무 시간 및 사용률이 낮은 업무 시간에 따라 Vm을 시작 하 고 중지 하도록 예약 합니다.
- CPU 코어 당 세션 수에 따라 Vm을 확장 합니다.
- 사용량이 적은 시간에 Vm의 크기를 조정 합니다. 즉, 실행 중인 세션 호스트 Vm의 최소 수는 그대로 둡니다.

크기 조정 도구는 Azure Automation PowerShell runbook, webhook 및 Azure Logic Apps의 조합을 사용 하 여 작동 합니다. 도구가 실행 되 면 Azure Logic Apps 웹 후크를 호출 하 여 Azure Automation runbook을 시작 합니다. 그런 다음 runbook에서 작업을 만듭니다.

최대 사용 시간 동안 작업은 현재 세션 수와 각 호스트 풀에 대해 현재 실행 중인 세션 호스트의 VM 용량을 확인 합니다. 이 정보를 사용 하 여 실행 중인 세션 호스트 Vm이 **createazurelogicapp** 파일에 대해 정의 된 *SessionThresholdPerCPU* 매개 변수를 기반으로 기존 세션을 지원할 수 있는지 여부를 계산 합니다. 세션 호스트 Vm에서 기존 세션을 지원할 수 없는 경우 작업은 호스트 풀에서 추가 세션 호스트 Vm을 시작 합니다.

>[!NOTE]
>*SessionThresholdPerCPU* 는 VM의 세션 수를 제한 하지 않습니다. 이 매개 변수는 연결의 부하를 분산 하기 위해 새 Vm을 시작 해야 하는 경우에만 결정 합니다. 세션 수를 제한 하려면 *MaxSessionLimit* 매개 변수를 적절 하 게 구성 하는 명령 [집합 RdsHostPool](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/set-rdshostpool) 을 따라야 합니다.

사용량이 많지 않은 사용 시간 동안 작업은 사용 시간을 결정 *하는 데* 사용 되는 세션 호스트 vm을 결정 합니다. 작업은 세션 호스트 Vm을 드레이닝 모드로 설정 하 여 호스트에 연결 하는 새 세션을 방지 합니다. *LimitSecondsToForceLogOffUser* 매개 변수를 0이 아닌 값으로 설정 하는 경우 스크립트는 현재 로그인 한 사용자에 게 작업을 저장 하 라고 알리고 구성 된 시간 동안 기다린 다음 사용자가 로그 아웃 하도록 합니다. 세션 호스트 VM의 모든 사용자 세션이 로그 아웃 되 면 스크립트는 VM을 종료 합니다.

*LimitSecondsToForceLogOffUser* 매개 변수를 0으로 설정 하면 작업에서 지정 된 그룹 정책의 세션 구성 설정을 허용 하 여 사용자 세션의 로그 오프를 처리할 수 있습니다. 이러한 그룹 정책을 보려면 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > 터미널 **서비스** > **터미널 서버** > **세션 시간 제한**으로 이동 합니다. 세션 호스트 VM에 활성 세션이 있으면 작업에서 세션 호스트 VM이 실행 되는 상태로 유지 됩니다. 활성 세션이 없으면 작업에서 세션 호스트 VM이 종료 됩니다.

작업은 설정 된 되풀이 간격에 따라 주기적으로 실행 됩니다. Windows 가상 데스크톱 환경의 크기에 따라이 간격을 변경할 수 있지만, 가상 컴퓨터를 시작 하 고 종료 하는 데 다소 시간이 걸릴 수 있으므로 지연 시간을 고려해 야 합니다. 되풀이 간격을 15 분 간격으로 설정 하는 것이 좋습니다.

그러나이 도구에는 다음과 같은 제한 사항도 있습니다.

- 이 솔루션은 풀링된 세션 호스트 Vm에만 적용 됩니다.
- 이 솔루션은 모든 지역에서 Vm을 관리 하지만 Azure Automation 계정과 동일한 구독 에서만 사용 하 고 Azure Logic Apps 수 있습니다.

>[!NOTE]
>크기 조정 도구는 크기를 조정 하는 호스트 풀의 부하 분산 모드를 제어 합니다. 이는 최고 및 사용률이 낮은 시간에 대해 너비 우선 부하 분산으로 설정 합니다.

## <a name="prerequisites"></a>필수 조건

크기 조정 도구를 설정 하기 전에 다음 항목을 준비 해야 합니다.

- [Windows 가상 데스크톱 테넌트 및 호스트 풀](create-host-pools-arm-template.md)
- Windows 가상 데스크톱 서비스를 사용 하 여 구성 하 고 등록 한 세션 호스트 풀 Vm
- Azure 구독에 대 한 [참가자 액세스 권한이](../role-based-access-control/role-assignments-portal.md) 있는 사용자

도구를 배포 하는 데 사용 하는 컴퓨터에는 다음이 있어야 합니다. 

- Windows PowerShell 5.1 이상
- Microsoft Az PowerShell module

모든 것이 준비 된 경우 시작 하겠습니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

먼저 PowerShell runbook을 실행할 Azure Automation 계정이 필요 합니다. 계정을 설정 하는 방법은 다음과 같습니다.

1. 관리자 권한으로 Windows PowerShell을 엽니다.
2. 다음 cmdlet을 실행 하 여 Azure 계정에 로그인 합니다.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >계정에는 크기 조정 도구를 배포 하려는 Azure 구독에 대 한 참가자 권한이 있어야 합니다.

3. Azure Automation 계정을 만들기 위한 스크립트를 다운로드 하려면 다음 cmdlet을 실행 합니다.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1" -OutFile "your local machine path\ createazureautomationaccount.ps1"
     ```

4. 다음 cmdlet을 실행 하 여 스크립트를 실행 하 고 Azure Automation 계정을 만듭니다.

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. Cmdlet의 출력에는 webhook URI가 포함 됩니다. Azure 논리 앱에 대 한 실행 일정을 설정할 때 매개 변수로 사용할 수 있으므로 URI의 레코드를 유지 해야 합니다.

Azure Automation 계정을 설정한 후에는 Azure 구독에 로그인 하 고 다음 이미지와 같이 Azure Automation 계정 및 관련 runbook이 지정 된 리소스 그룹에 표시 되는지 확인 합니다.

![새로 만든 automation 계정 및 runbook을 보여 주는 Azure 개요 페이지의 이미지입니다.](media/automation-account.png)

웹 후크가 어디에 있는지 확인 하려면 화면 왼쪽의 리소스 목록으로 이동 하 고 **webhook**를 선택 합니다.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정 만들기

이제 Azure Automation 계정이 있으므로 Azure 리소스에 액세스 하기 위해 Azure Automation 실행 계정을 만들어야 합니다.

[Azure Automation 실행 계정은](../automation/manage-runas-account.md) azure cmdlet을 사용 하 여 azure에서 리소스를 관리 하기 위한 인증을 제공 합니다. 실행 계정을 만들 때 Azure Active Directory에 새 서비스 주체 사용자를 만들고 구독 수준에서 서비스 주체 사용자에 게 참가자 역할을 할당 합니다. Azure 실행 계정은로 안전 하 게 인증할 수 있는 좋은 방법입니다. 자격 증명 개체에 사용자 이름과 암호를 저장 하지 않아도 되는 인증서 및 서비스 사용자 이름입니다. 실행 인증에 대해 자세히 알아보려면 [실행 계정 권한 제한](../automation/manage-runas-account.md#limiting-run-as-account-permissions)을 참조 하세요.

구독 관리자 역할의 구성원이 고 구독의 공동 관리자 인 사용자는 다음 섹션의 지침에 따라 실행 계정을 만들 수 있습니다.

Azure 계정에서 실행 계정을 만들려면 다음을 수행 합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Automation 계정**을 입력 하 고 선택 합니다.

2. **Automation 계정** 페이지에서 automation 계정의 이름을 선택 합니다.

3. 창의 왼쪽 창에서 계정 설정 섹션 아래에 있는 **실행 계정** 을 선택 합니다.

4. **Azure 실행 계정**을 선택 합니다. **Azure 실행 계정 추가** 창이 나타나면 개요 정보를 검토 한 다음 **만들기** 를 선택 하 여 계정 만들기 프로세스를 시작 합니다.

5. Azure에서 실행 계정을 만드는 데 몇 분 정도 기다립니다. 알림 아래에 있는 메뉴에서 만들기 진행률을 추적할 수 있습니다.

6. 프로세스가 완료 되 면 지정 된 Automation 계정에 AzureRunAsConnection 이라는 자산이 생성 됩니다. 연결 자산은 응용 프로그램 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유 합니다. 나중에 사용할 수 있기 때문에 응용 프로그램 ID를 잊지 마세요.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows Virtual Desktop에서 역할 할당 수행하기

다음으로 AzureRunAsConnection가 Windows 가상 데스크톱과 상호 작용할 수 있도록 역할 할당을 만들어야 합니다. PowerShell을 사용 하 여 역할 할당을 만들 수 있는 권한이 있는 계정으로 로그인 해야 합니다.

먼저 PowerShell 세션에서 사용할 [Windows 가상 데스크톱 powershell 모듈](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 을 다운로드 하 고 가져옵니다 (아직 없는 경우). 다음 PowerShell cmdlet을 실행하여 Windows Virtual Desktop에 연결하고 테넌트를 표시합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

크기를 조정 하려는 호스트 풀이 있는 테넌트를 찾았으면 다음 cmdlet에서 [Azure Automation 계정 만들기](#create-an-azure-automation-account) 의 지침을 따르고 이전 cmdlet에서 가져온 테넌트 이름을 사용 하 여 역할 할당을 만듭니다.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure 논리 앱 및 실행 일정 만들기

마지막으로, Azure 논리 앱을 만들고 새 크기 조정 도구에 대 한 실행 일정을 설정 해야 합니다.

1.  관리자 권한으로 Windows PowerShell을 엽니다.

2.  다음 cmdlet을 실행 하 여 Azure 계정에 로그인 합니다.

     ```powershell
     Login-AzAccount
     ```

3. 다음 cmdlet을 실행 하 여 로컬 컴퓨터에서 createazurelogicapp 스크립트 파일을 다운로드 합니다.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. 다음 cmdlet을 실행 하 여 RDS Owner 또는 RDS 참여자 권한이 있는 계정으로 Windows 가상 데스크톱에 로그인 합니다.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. 다음 PowerShell 스크립트를 실행 하 여 Azure 논리 앱 및 실행 일정을 만듭니다.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you’d like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you’d like the job to run in minutes, e.g. ‘15’"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don’t want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     스크립트를 실행 한 후 다음 그림에 표시 된 것 처럼 논리 앱이 리소스 그룹에 표시 됩니다.

     ![예제 Azure 논리 앱에 대 한 개요 페이지 이미지입니다.](media/logic-app.png)

되풀이 간격 또는 표준 시간대를 변경 하는 등의 실행 일정을 변경 하려면 자동 크기 조정 스케줄러로 이동 하 고 **편집** 을 선택 하 여 Logic Apps 디자이너로 이동 합니다.

![Logic Apps 디자이너의 이미지입니다. 사용자가 되풀이 시간 및 웹 후크 파일을 편집할 수 있는 되풀이 및 Webhook 메뉴가 열립니다.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>크기 조정 도구 관리

이제 크기 조정 도구를 만들었으므로 출력에 액세스할 수 있습니다. 이 섹션에서는 유용할 수 있는 몇 가지 기능에 대해 설명 합니다.

### <a name="view-job-status"></a>작업 상태 보기

모든 runbook 작업의 요약 된 상태를 보거나 Azure Portal의 특정 runbook 작업에 대 한 자세한 상태를 볼 수 있습니다.

선택한 Automation 계정의 오른쪽에 있는 "작업 통계"에서 모든 runbook 작업의 요약 목록을 볼 수 있습니다. 창의 왼쪽에서 **작업** 페이지를 열면 현재 작업 상태, 시작 시간 및 완료 시간이 표시 됩니다.

![작업 상태 페이지의 스크린샷](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>로그 보기 및 크기 조정 도구 출력

Runbook을 열고 작업 이름을 선택 하 여 스케일 아웃 및 규모 확장 작업의 로그를 볼 수 있습니다.

Azure Automation 계정을 호스팅하는 리소스 그룹의 runbook (기본 이름은 WVDAutoScaleRunbook)으로 이동 하 고 **개요**를 선택 합니다. 다음 그림에 표시 된 것 처럼 개요 페이지에서 최근 작업 아래의 작업을 선택 하 여 크기 조정 도구 출력을 볼 수 있습니다.

![크기 조정 도구에 대 한 출력 창의 이미지입니다.](media/tool-output.png)
