---
title: 경고를 사용하여 Azure Automation Runbook 트리거
description: Azure 경고가 발생할 경우 실행할 Runbook을 트리거하는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073863"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>경고를 사용하여 Azure Automation Runbook 트리거

[Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json)를 사용하여 대부분의 Azure 서비스에 대한 기본 수준의 메트릭과 로그를 모니터링합니다. [작업 그룹](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json)을 사용하거나 경고에 따라 작업을 자동화하기 위한 클래식 경고를 통해 Azure Automation Runbook을 호출할 수 있습니다. 이 문서에서는 경고를 사용하여 Runbook을 구성하고 실행하는 방법을 보여 줍니다.

## <a name="alert-types"></a>경고 유형

3가지 경고 유형으로 Automation Runbook을 사용할 수 있습니다.
* 클래식 메트릭 경고
* 활동 로그 경고
* 근 실시간 메트릭 경고

경고가 Runbook을 호출할 때 실제 호출은 웹후크에 대한 HTTP POST 요청입니다. POST 요청의 본문에는 경고와 관련된 유용한 속성을 포함하는 JSON으로 포맷된 개체가 포함되어 있습니다. 다음 표에는 각 경고 유형에 대한 페이로드 스키마 링크가 나열되어 있습니다.

|경고  |설명|페이로드 스키마  |
|---------|---------|---------|
|[클래식 메트릭 경고](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |모든 플랫폼 수준의 메트릭이 특정 조건을 충족하는 경우 알림을 보냅니다. 예를 들어 VM의 **CPU %** 값이 지난 5분 동안 **90**보다 큰 경우입니다.| [클래스 메트릭 경고 페이로드 스키마](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[활동 로그 경고](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Azure 활동 로그의 새 이벤트가 특정 조건과 일치하는 경우 알림을 보냅니다. 예를 들어 `Delete VM` 작업이 **myProductionResourceGroup**에서 발생하거나 새 Azure Service Health 이벤트가 **활성** 상태로 표시되는 경우입니다.| [활동 로그 경고 페이로드 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[근 실시간 메트릭 경고 만들기](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |하나 이상의 플랫폼 수준 메트릭이 지정된 조건을 충족하는 경우 메트릭 경고보다 빠르게 알림을 보냅니다. 예를 들어 VM의 **CPU %** 값이 **90**보다 큰 경우 및 **네트워크 입력**의 값이 지난 5분 동안 **500MB**보다 큰 경우입니다.| [근 실시간 메트릭 경고 페이로드 스키마](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

각 유형의 경고에서 제공되는 데이터가 다르기 때문에 각 경고 유형이 다르게 처리됩니다. 다음 섹션에서는 다양한 유형의 경고를 처리하는 Runbook을 만드는 방법에 대해 배웁니다.

## <a name="create-a-runbook-to-handle-alerts"></a>경고를 처리할 Runbook 만들기

경고와 함께 자동화를 사용하려면 Runbook에 전달되는 경고 JSON 페이로드를 관리하는 논리가 있는 Runbook이 필요합니다. 다음 Runbook 예제는 Azure 경고에서 호출되어야 합니다.

앞 섹션에서 설명한 것처럼 각 경고 유형은 서로 다른 스키마를 사용합니다. 스크립트는 경고의 `WebhookData` Runbook 입력 매개 변수에서 웹후크 데이터를 가져옵니다. 그런 다음 스크립트가 JSON 페이로드를 평가하여 어떤 경고 유형이 사용되었는지 확인합니다.

이 예제에서는 VM에서 발생한 경고를 사용합니다. 페이로드에서 VM 데이터를 검색한 다음 해당 정보를 사용하여 VM을 중지합니다. Runbook이 실행되는 Automation 계정에서 연결을 설정해야 합니다. 경고를 사용하여 Runbook을 트리거하는 경우 트리거되는 Runbook에서 경고 상태를 확인하는 것이 중요합니다. 경고 상태가 변경될 때마다 Runbook이 트리거됩니다. 경고에는 여러 가지 상태가 있으며, 가장 일반적인 두 가지 상태는 `Activated` 및 `Resolved`입니다. Runbook 논리에서 이 상태를 확인하여 Runbook이 두 번 이상 실행되지 않도록 합니다. 이 문서의 예제에서는 `Activated` 경고만 찾는 방법을 보여 줍니다.

Runbook은 **AzureRunAsConnection** [실행 계정](automation-create-runas-account.md)을 사용하여 VM에 대한 관리 작업을 수행하도록 Azure를 인증합니다.

이 예제를 사용하여 Runbook에서 호출한 **Stop-AzureVmInResponsetoVMAlert**을 만듭니다. PowerShell 스크립트를 수정하고 여러 다른 리소스와 함께 사용할 수 있습니다.

1. Azure Automation 계정으로 이동합니다.
1. **프로세스 자동화**에서 **Runbook**을 선택합니다.
1. Runbook의 목록 맨 위에서 **Runbook 추가**를 선택합니다. 
1. **Runbook 추가** 페이지에서 **빨리 만들기**를 선택합니다.
1. Runbook 이름으로 **Stop-AzureVmInResponsetoVMAlert**을 입력합니다. Runbook 형식으로 **PowerShell**을 선택합니다. 그런 다음 **만들기**를 선택합니다.  
1. 다음 PowerShell 예제를 **편집** 창에 복사합니다. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. **게시**를 선택하여 Runbook을 저장하고 게시합니다.

## <a name="create-an-action-group"></a>작업 그룹 만들기

작업 그룹은 경고에 의해 트리거되는 작업의 컬렉션입니다. Runbook은 작업 그룹에서 사용할 수 있는 많은 작업 중 하나일 뿐입니다.

1. Azure Portal에서 **모니터** > **설정** > **작업 그룹**을 선택합니다.
1. **작업 그룹 추가**를 선택한 다음 필요한 정보를 입력합니다.  
    1. **작업 그룹 이름** 상자에 이름을 입력합니다.
    1. **짧은 이름** 상자에 이름을 입력합니다. 약식 이름은 이 작업 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.
    1. **구독** 상자가 현재 구독으로 자동으로 채워집니다. 이 구독은 작업 그룹이 저장되는 위치입니다.
    1. 작업 그룹이 저장되는 리소스 그룹을 선택합니다.

이 예제에서는 두 가지 작업, Runbook 작업과 알림 작업을 만듭니다.

### <a name="runbook-action"></a>Runbook 작업

작업 그룹에서 Runbook 작업을 만들려면:

1. **작업**에서 **작업 이름**에 작업의 이름을 입력합니다. **작업 유형**으로 **Automation Runbook**을 선택합니다.
1. **세부 정보**에서 **세부 정보 편집**을 선택합니다.  
1. **Runbook 구성** 페이지의 **Runbook 원본**에서 **사용자**를 선택합니다.  
1. **구독** 및 **Automation 계정**을 선택한 다음 **Stop-AzureVmInResponsetoVMAlert** Runbook을 선택합니다.  
1. 작업을 마쳤으면 **확인**을 선택합니다.

### <a name="notification-action"></a>알림 동작

작업 그룹에 알림 작업을 만들려면:

1. **작업**에서 **작업 이름**에 작업의 이름을 입력합니다. **작업 유형**으로 **메일**을 선택합니다.  
1. **세부 정보**에서 **세부 정보 편집**을 선택합니다.  
1. **메일** 페이지에서 알림에 사용할 메일 주소를 입력한 다음 **확인**을 선택합니다. 작업으로 Runbook 외에 메일 주소를 추가하는 것은 유용합니다. Runbook이 시작될 때 알림을 받습니다.  

    작업 그룹은 다음과 같은 이미지여야 합니다.

   ![작업 그룹 페이지 추가](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. 작업 그룹을 만들려면 **확인**을 선택합니다.

만드는 [활동 로그 경고](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) 및 [근 실시간 경고](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json)에서 이 작업 그룹을 사용할 수 있습니다.

## <a name="classic-alert"></a>클래식 경고

클래식 경고는 메트릭을 기반으로 하기 때문에 작업 그룹을 사용하지 않습니다. 그러나 클래식 경고에 따라 Runbook 작업을 설정할 수 있습니다. 

클래식 경고를 만들려면:

1. **메트릭 경고 추가**를 선택합니다.
1. 메트릭 경고 이름을 **myVMCPUAlert**으로 지정합니다. 경고에 대한 간략한 설명을 입력합니다.
1. 메트릭 경고 조건으로 **보다 큼**을 선택합니다. **임계값**의 경우 **10**을 선택합니다. **기간** 값으로 **지난 5분 이상**을 선택합니다.
1. **작업 수행**에서 **이 경고에서 Runbook 실행**을 선택합니다.
1. **Runbook 구성** 페이지에서 **Runbook 원본**에 대해 **사용자**를 선택합니다. 자동화 계정을 선택하고 **Stop-AzureVmInResponsetoVMAlert** Runbook을 선택합니다. **확인**을 선택합니다.
1. 경고 규칙을 저장하려면 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 웹후크를 사용하여 Automation Runbook을 시작하는 방법에 대한 자세한 내용은 [웹후크에서 Runbook 시작](automation-webhooks.md)을 참조하세요.
* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 활동 로그 경고를 만드는 방법을 알아보려면 [활동 로그 경고 만들기](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.
* 근 실시간 경고를 만드는 방법을 보려면 [Azure Portal에서 경고 규칙 만들기](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json)를 참조하세요.

