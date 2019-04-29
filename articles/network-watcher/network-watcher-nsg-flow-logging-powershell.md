---
title: Azure Network Watcher를 사용하여 네트워크 보안 그룹 흐름 로그 관리 - PowerShell | Microsoft Docs
description: 이 페이지에서는 PowerShell을 사용하여 Azure Network Watcher의 네트워크 보안 그룹 흐름 로그를 관리하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: ebeebfa4490797493a781bf462d363d1cbcf2d55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680372"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>PowerShell을 사용하여 네트워크 보안 그룹 흐름 로그 구성

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

네트워크 보안 그룹 흐름 로그는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 json 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여줍니다.

## <a name="register-insights-provider"></a>Insights 공급자 등록

흐름 로깅이 성공적으로 작동하기 위해서 **Microsoft.Insights** 공급자를 등록해야 합니다. **Microsoft.Insights** 공급자가 등록되어 있는지 확실하지 않은 경우 다음 스크립트를 실행합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>네트워크 보안 그룹 흐름 로그 및 트래픽 분석 사용

다음 예제에서는 흐름 로그를 사용하도록 설정하는 명령이 표시됩니다.

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

지정한 저장소 계정은 Microsoft 서비스 또는 특정 가상 네트워크에 대한 네트워크 액세스를 제한하도록 구성된 네트워크 규칙을 가질 수 없습니다. 저장소 계정은 동일하거나 흐름 로그를 활성화하는 NSG와 다른 Azure 구독에 있을 수 있습니다. 서로 다른 구독을 사용하는 경우 동일한 Azure Active Directory 테넌트에 연결되어야 합니다. 각 구독에 대해 사용하는 계정에 [필요한 권한](required-rbac-permissions.md)이 있어야 합니다.

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>트래픽 분석 및 네트워크 보안 그룹 흐름 로그 사용 안 함

다음 예제를 사용하여 트래픽 분석 및 흐름 로그를 사용하지 않도록 설정합니다.

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>흐름 로그 다운로드

흐름 로그의 저장소 위치를 만들 때 정의합니다. 스토리지 계정에 저장되는 이러한 흐름 로그에 액세스하는 편리한 도구는 Microsoft Azure Storage 탐색기이며 https://storageexplorer.com/에서 다운로드할 수 있습니다.

스토리지 계정이 지정되어 있으면 흐름 로그 파일은 다음 위치에서 스토리지 계정에 저장됩니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

로그의 구조에 대한 내용은 [네트워크 보안 그룹 흐름 로그 개요](network-watcher-nsg-flow-logging-overview.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계

[PowerBI를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)하는 방법 알아보기

[오픈 소스 도구를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)하는 방법 알아보기
