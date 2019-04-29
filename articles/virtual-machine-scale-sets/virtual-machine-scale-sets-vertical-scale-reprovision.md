---
title: Azure 가상 머신 확장 집합을 수직으로 규모 조정 | Microsoft Docs
description: Azure Automation을 사용하여 모니터링 경고에 대한 응답으로 Virtual Machine을 수직으로 확장하는 방법
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: 3846815dabdc9e351f3d8449feb88affb9c6efdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803475"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 사용하여 수직으로 규모 조정

이 문서에서는 다시 프로비저닝을 사용하거나 사용하지 않고 Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 수직으로 확장하는 방법을 설명합니다. 확장 집합에 있지 않은 VM의 수직 규모 조정에 대해서는 [Azure Automation을 사용하여 Azure 가상 머신을 수직으로 확장](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

일명 *강화* 및 *규모 축소*라고도 하는 수직 규모 조정이란 워크로드에 따라 가상 머신(VM) 규모를 늘리거나 줄이는 것을 의미합니다. 이 동작을 VM 수가 워크로드에 따라 변경되는 일명 *스케일 아웃* 및 *규모 감축*이라고도 하는 [수평 규모 조정](virtual-machine-scale-sets-autoscale-overview.md)과 비교해 보십시오.

다시 프로비저닝이란 기존 VM을 제거하고 새 VM으로 바꾸는 것을 의미합니다. 가상 머신 확장 집합에서 VM의 규모를 늘리거나 줄이는 경우 기존 VM을 규모 변경하고 데이터를 보존하기를 원하는 경우도 있고 새 규모의 새 VM을 배포해야 하는 경우도 있습니다. 이 문서에서는 두 경우를 모두 설명합니다.

수직 규모 조정이 유용할 수 있는 경우:

* 가상 머신에 만들어진 서비스는 잘 이용되지 않습니다(예를 들어 주말). VM 규모를 줄이면 월간 비용을 줄일 수 있습니다.
* VM 규모를 늘리면 추가 VM을 만들지 않고 더 큰 수요를 충족할 수 있습니다.

가상 머신 확장 집합에서 메트릭 기반 경고를 바탕으로 수직 규모 조정을 트리거하도록 설정할 수 있습니다. 경고가 활성화될 때 규모를 확장하거나 축소할 수 있는 Runbook을 트리거하는 webhook가 시작됩니다. 수직 규모 조정을 다음과 같은 단계에 따라 구성할 수 있습니다.

1. 실행 기능을 사용하여 Azure Automation 계정을 만듭니다.
2. 가상 머신 확장 집합용 Azure Automation 수직 규모 조정 Runbook을 구독으로 가져옵니다.
3. Webhook을 Runbook에 추가합니다.
4. Webhook 알림을 사용하여 가상 머신 확장 집합에 경고를 추가합니다.

> [!NOTE]
> 첫 번째 Virtual Machine의 크기로 인해 확장할 수 있는 크기가 제한될 수 있습니다. 이는 현재 Virtual Machine이 배포된 클러스터에서 다른 크기의 가용성 때문입니다. 이 문서에서 사용된 게시된 자동화 runbook에서는 이 점을 염두에 두고 VM 크기 쌍 이내에서만 확장합니다. 따라서 Standard_D1v2 Virtual Machine이 갑자기 Standard_G5로 확장되거나 Basic_A0으로 축소되지 않습니다. 또한 제한 된 가상 머신 크기 확장/축소는 지원 되지 않습니다. 다음 규모 쌍 범위로 규모 조정하도록 선택할 수 있습니다.
> 
> | VM 크기 조정 쌍 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>실행 기능을 사용하여 Azure Automation 계정 만들기
가장 먼저 해야 할 일은 가상 머신 확장 집합 인스턴스의 크기를 조정하는 데 사용하는 Runbook을 호스트할 Azure Automation 계정을 만드는 것입니다. 최근 [Azure Automation](https://azure.microsoft.com/services/automation/)에서는 사용자 대신 Runbook을 자동으로 실행하기 위한 서비스 주체를 설정하는 “실행 계정” 기능을 도입했습니다. 자세한 내용은 다음을 참조하세요.

* [Azure 실행 계정으로 Runbook 인증](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>구독으로 Azure Automation 수직 규모 runbook 가져오기

가상 머신 확장 집합을 수직으로 확장하는 데 필요한 Runbook은 Azure Automation Runbook 갤러리에 이미 게시되어 있습니다. 이들을 구독으로 가져오려면 이 문서의 단계를 따릅니다.

* [Azure Automation용 Runbook 및 모듈 갤러리](../automation/automation-runbook-gallery.md)

Runbook 메뉴에서 갤러리 찾아보기 옵션을 선택:

![가져올 Runbook][runbooks]

가져와야 하는 Runbook이 표시됩니다. 수직 규모 조정 시 다시 프로비저닝을 사용할지 여부에 따라 Runbook을 선택합니다.

![Runbook 갤러리][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Runbook에 Webhook 추가

Runbook을 가져온 후에는 가상 머신 확장 집합에서 경고를 통해 트리거될 수 있도록 Runbook에 webhook를 추가합니다. Runbook에 대한 Webhook를 만드는 자세한 방법을 이 문서에서 설명합니다.

* [Azure Automation Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> 다음 섹션에서 필요하므로 webhook 대화 상자를 닫기 전에 webhook URI를 복사해야 합니다.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>가상 머신 확장 집합에 경고 추가

다음은 가상 머신 확장 집합에 경고를 추가하는 방법을 보여 주는 PowerShell 스크립트입니다. 다음 문서를 참조하여 경고를 시작할 메트릭의 이름을 가져옵니다. [Azure Monitor 자동 크기 조정 공용 메트릭](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> 수직 규모 조정 및 너무 잦은 연결 서비스 중단을 방지하기 위해 경고에 대한 합리적인 기간을 구성하는 것이 좋습니다. 최소 20-30 분 이상 기간을 고려해야 합니다. 중단을 방지하기 위해 필요한 경우 수평 규모 조정을 고려합니다.
> 
> 

경고를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Monitor PowerShell 빠른 시작 샘플](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor 플랫폼 간 CLI 빠른 시작 샘플](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>요약

이 문서에서 간단한 수직 규모 조정 예제를 살펴보았습니다. Automation 계정, Runbook, Webhook, 경고 등 이러한 구성 요소를 사용하여 다양한 이벤트를 사용자 지정 작업 집합과 연결할 수 있습니다.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
