---
title: 경고를 사용하여 Azure Automation Runbook 트리거
description: 이 문서에서는 Azure 경고가 발생할 때 실행할 Runbook을 트리거하는 방법을 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: ea7979ad4a401d317ec126b7abfe354690475235
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953105"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>경고를 사용하여 Azure Automation Runbook 트리거

[Azure Monitor](../azure-monitor/overview.md)를 사용하여 대부분의 Azure 서비스에 대한 기본 수준의 메트릭과 로그를 모니터링합니다. [작업 그룹](../azure-monitor/alerts/action-groups.md)을 사용하거나 경고에 따라 작업을 자동화하기 위해 Azure Automation Runbook을 호출할 수 있습니다. 이 문서에서는 경고를 사용하여 Runbook을 구성하고 실행하는 방법을 보여 줍니다.

## <a name="alert-types"></a>경고 유형

3가지 경고 유형으로 Automation Runbook을 사용할 수 있습니다.

* 일반적인 경고
* 활동 로그 경고
* 실시간에 가까운 메트릭 경고

> [!NOTE]
> 일반 경고 스키마는 현재 Azure에서 경고 알림에 대한 소비 환경을 표준화합니다. 지금까지 Azure의 세 가지 경고 유형(메트릭, 로그 및 활동 로그)에는 자체 이메일 템플릿, 웹후크 스키마 등이 있었습니다. 자세히 알아보려면 [일반 경고 스키마](../azure-monitor/alerts/alerts-common-schema.md)를 참조하세요.

경고가 Runbook을 호출할 때 실제 호출은 웹후크에 대한 HTTP POST 요청입니다. POST 요청의 본문에는 경고와 관련된 유용한 속성을 포함하는 JSON으로 포맷된 개체가 포함되어 있습니다. 다음 표에는 각 경고 유형에 대한 페이로드 스키마 링크가 나열되어 있습니다.

|경고  |Description|페이로드 스키마  |
|---------|---------|---------|
|[일반 경고](../azure-monitor/alerts/alerts-common-schema.md)|현재 Azure에서 경고 알림에 대한 소비 환경을 표준화하는 일반 경고 스키마입니다.|일반 경고 페이로드 스키마|
|[활동 로그 경고](../azure-monitor/alerts/activity-log-alerts.md)    |Azure 활동 로그의 새 이벤트가 특정 조건과 일치하는 경우 알림을 보냅니다. 예를 들어 `Delete VM` 작업이 **myProductionResourceGroup** 에서 발생하거나 새 Azure Service Health 이벤트가 활성 상태로 표시되는 경우입니다.| [활동 로그 경고 페이로드 스키마](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[근 실시간 메트릭 경고 만들기](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |하나 이상의 플랫폼 수준 메트릭이 지정된 조건을 충족하는 경우 메트릭 경고보다 빠르게 알림을 보냅니다. 예를 들어 VM의 **CPU %** 값이 90보다 큰 경우 및 **네트워크 입력** 의 값이 지난 5분 동안 500MB보다 큰 경우입니다.| [근 실시간 메트릭 경고 페이로드 스키마](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

각 유형의 경고에서 제공되는 데이터가 다르기 때문에 각 경고 유형이 다르게 처리됩니다. 다음 섹션에서는 다양한 유형의 경고를 처리하는 Runbook을 만드는 방법에 대해 배웁니다.

## <a name="create-a-runbook-to-handle-alerts"></a>경고를 처리할 Runbook 만들기

경고와 함께 자동화를 사용하려면 Runbook에 전달되는 경고 JSON 페이로드를 관리하는 논리가 있는 Runbook이 필요합니다. 다음 Runbook 예제는 Azure 경고에서 호출되어야 합니다.

앞 섹션에서 설명한 것처럼 각 경고 유형은 서로 다른 스키마를 사용합니다. 스크립트는 `WebhookData` Runbook 입력 매개 변수의 경고에서 웹후크 데이터를 가져옵니다. 그런 다음, 스크립트가 JSON 페이로드를 평가하여 사용 중인 경고 유형을 확인합니다.

이 예제에서는 VM에서 발생한 경고를 사용합니다. 페이로드에서 VM 데이터를 검색한 다음 해당 정보를 사용하여 VM을 중지합니다. Runbook이 실행되는 Automation 계정에서 연결을 설정해야 합니다. 경고를 사용하여 Runbook을 트리거하는 경우 트리거되는 Runbook에서 경고 상태를 확인하는 것이 중요합니다. 경고 상태가 변경될 때마다 Runbook이 트리거됩니다. 경고에는 여러 상태가 있으며 가장 일반적인 두 가지 상태는 활성화됨 및 해결됨입니다. Runbook 논리의 상태를 확인하여 Runbook이 두 번 이상 실행되지 않도록 합니다. 이 문서의 예제에서는 활성화된 상태인 경고만 찾는 방법을 보여 줍니다.

Runbook은 연결 자산 `AzureRunAsConnection` [실행 계정](./automation-security-overview.md)을 사용하여 VM에 대한 관리 작업을 수행하도록 Azure를 인증합니다.

이 예제를 사용하여 Runbook에서 호출한 **Stop-AzureVmInResponsetoVMAlert** 을 만듭니다. PowerShell 스크립트를 수정하고 여러 다른 리소스와 함께 사용할 수 있습니다.

1. Azure Automation 계정으로 이동합니다.
2. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.
3. Runbook의 목록 맨 위에서 **+ Runbook 만들기** 를 선택합니다.
4. **Runbook 추가** 페이지에서 Runbook에 대해 **Stop-AzureVmInResponsetoVMAlert** 를 입력합니다. Runbook 형식으로 **PowerShell** 을 선택합니다. 그런 다음 **만들기** 를 선택합니다.  
5. 다음 PowerShell 예제를 **편집** 페이지에 복사합니다.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. **게시** 를 선택하여 Runbook을 저장하고 게시합니다.

## <a name="create-the-alert"></a>경고 만들기

경고는 경고에 의해 트리거되는 작업 컬렉션인 작업 그룹을 사용합니다. Runbook은 작업 그룹에서 사용할 수 있는 많은 작업 중 하나일 뿐입니다.

1. Automation 계정의 **모니터링** 에서 **경고** 를 선택합니다.
1. **+ 새 경고 규칙** 을 선택합니다.
1. **리소스** 에서 **선택** 을 클릭합니다. **리소스 선택** 페이지에서 경고를 발생시킬 VM을 선택하고 **완료** 를 클릭합니다.
1. **조건** 에서 **조건 추가** 를 클릭합니다. 사용하려는 신호(예: **백분율 CPU**)를 선택하고 **완료** 를 클릭합니다.
1. **신호 논리 구성 페이지** 의 **경고 논리** 아래에 **임계값** 을 입력하고 **완료** 를 클릭합니다.
1. **작업 그룹** 아래에서 **새로 만들기** 를 선택합니다.
1. **작업 그룹 추가** 페이지에서 작업 그룹에 이름 및 짧은 이름을 지정합니다.
1. 작업에 이름을 지정합니다. 작업 유형으로 **Automation Runbook** 을 선택합니다.
1. **세부 정보 편집** 을 선택합니다. **Runbook 구성** 페이지의 **Runbook 원본** 에서 **사용자** 를 선택합니다.  
1. **구독** 및 **Automation 계정** 을 선택한 다음 **Stop-AzureVmInResponsetoVMAlert** Runbook을 선택합니다.  
1. **일반 경고 스키마 사용** 에 대해 **예** 를 선택합니다.
1. 작업 그룹을 만들려면 **확인** 을 선택합니다.

    ![작업 그룹 페이지 추가](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    만드는 [활동 로그 경고](../azure-monitor/alerts/activity-log-alerts.md) 및 [근 실시간 경고](../azure-monitor/alerts/alerts-overview.md)에서 이 작업 그룹을 사용할 수 있습니다.

1. **경고 세부 정보** 에서 경고 규칙 이름과 설명을 추가하고 **경고 규칙 만들기** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 웹후크를 사용하여 Runbook을 시작하려면 [웹후크에서 Runbook 시작](automation-webhooks.md)을 참조하세요.
* Runbook을 시작하는 다양한 방법을 알아보려면 [Runbook 시작](./start-runbooks.md)을 참조하세요.
* 활동 로그 경고를 만들려면 [활동 로그 경고 만들기](../azure-monitor/alerts/activity-log-alerts.md)를 참조하세요.
* 근 실시간 경고를 만드는 방법을 보려면 [Azure Portal에서 경고 규칙 만들기](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.