---
title: Azure Network Watcher를 사용하여 패킷 캡처 관리 - Azure CLI | Microsoft Docs
description: 이 페이지에서는 Azure CLI를 사용하여 Network Watcher의 패킷 캡처 기능을 관리하는 방법에 대해 설명합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: cf03872607546f38d19a280f65f641abf627268b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726879"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Azure CLI에서 Azure Network Watcher를 사용하여 패킷 캡처 관리

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher 패킷 캡처를 사용하면 가상 컴퓨터 간에 트래픽을 추적하는 캡처 세션을 만들 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버깅할 수 있습니다. 이 기능은 원격으로 패킷 캡처를 트리거할 수 있게 하여 원하는 컴퓨터에서 수동으로 패킷 캡처를 실행하는 부담을 줄이고 시간을 단축합니다.

이 문서의 단계를 수행하려면 [Mac, Linux 및 Windows용 Azure 명령줄 인터페이스(Azure CLI)를 설치](/cli/azure/install-azure-cli)해야 합니다.

이 문서에서는 패킷 캡처를 위해 현재 사용할 수 있는 여러 관리 태스크를 설명합니다.

- [**패킷 캡처 시작**](#start-a-packet-capture)
- [**패킷 캡처 중지**](#stop-a-packet-capture)
- [**패킷 캡처 삭제**](#delete-a-packet-capture)
- [**패킷 캡처 다운로드**](#download-a-packet-capture)

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자에게 다음 리소스가 있는 것으로 가정합니다.

- 패킷 캡처를 만들려는 영역의 Network Watcher 인스턴스
- 패킷 캡처 확장을 사용하는 Virtual Machine

> [!IMPORTANT]
> 패킷 캡처에는 가상 머신에서 실행되는 에이전트가 필요합니다. 에이전트는 확장으로 설치됩니다. VM 확장에 대한 지침은 [Virtual Machine 확장 및 기능](../virtual-machines/windows/extensions-features.md)을 참조하세요.

## <a name="install-vm-extension"></a>VM 확장 설치

### <a name="step-1"></a>1단계

`az vm extension set` cmdlet을 실행하여 게스트 가상 머신에 패킷 캡처 에이전트를 설치합니다.

Windows Virtual Machines의 경우:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linux 가상 머신의 경우:

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux--version 1.4
```

### <a name="step-2"></a>2단계

에이전트가 설치되어 있는지 확인하려면 `vm extension show` cmdlet을 실행하고 리소스 그룹과 가상 머신 이름을 전달합니다. 결과 목록을 확인하여 에이전트가 설치되어 있는지 확인합니다.

```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

다음 샘플은 실행 중인 `az vm extension show`에서 응답의 예제입니다.

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>패킷 캡처 시작

이전 단계가 완료되면 패킷 캡처 에이전트는 가상 머신에 설치됩니다.

### <a name="step-1"></a>1단계

다음 단계는 Network Watcher 인스턴스를 검색하는 것입니다. Network Watcher의 이름이 4단계의 `az network watcher show` cmdlet으로 전달됩니다.

```azurecli
az network watcher show --resource-group resourceGroup --name networkWatcherName
```

### <a name="step-2"></a>2단계

저장소 계정을 검색합니다. 이 저장소 계정은 패킷 캡처 파일을 저장하는 데 사용됩니다.

```azurecli
azure storage account list
```

### <a name="step-3"></a>3단계

패킷 캡처에 의해 저장되는 데이터를 제한하는 데 필터를 사용할 수 있습니다. 다음 예제에서는 몇 가지 필터를 사용하여 패킷 캡처를 설정합니다.  처음 세 개의 필터는 로컬 IP 10.0.0.3에서 대상 포트 20, 80 및 443으로 나가는 TCP 트래픽을 수집합니다.  마지막 필터는 UDP 트래픽만을 수집합니다.

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

다음 예제는 `az network watcher packet-capture create` cmdlet을 실행하는 예상된 출력입니다.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>패킷 캡처 가져오기

`az network watcher packet-capture show-status` cmdlet을 실행하고 현재 실행 중이거나 완료된 패킷 캡처의 상태를 검색합니다.

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

다음 예제는 `az network watcher packet-capture show-status` cmdlet의 출력입니다. 다음 예제는 TimeExceeded StopReason으로 인해 캡처가 중지된 경우입니다. 

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>패킷 캡처 중지

`az network watcher packet-capture stop` cmdlet을 실행하여 캡처 세션이 진행 중인 경우 중지됩니다.

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> cmdlet은 현재 실행 중인 캡처 세션 또는 이미 중지된 기존 세션에서 실행되는 경우 응답을 반환하지 않습니다.

## <a name="delete-a-packet-capture"></a>패킷 캡처 삭제

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> 패킷 캡처를 삭제하면 저장소 계정에서 파일을 삭제하지 않습니다.

## <a name="download-a-packet-capture"></a>패킷 캡처 다운로드

패킷 캡처 세션이 완료되면 캡처 파일을 Blob Storage 또는 VM의 로컬 파일에 업로드할 수 있습니다. 패킷 캡처의 저장 위치는 세션 생성 시 정의됩니다. 스토리지 계정에 저장되는 이러한 캡처 파일에 액세스하는 편리한 도구는 Microsoft Azure Storage 탐색기이며 https://storageexplorer.com/에서 다운로드할 수 있습니다.

저장소 계정이 지정되어 있으면 패킷 캡처 파일은 다음 위치에서 저장소 계정에 저장됩니다.

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>다음 단계

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

[IP 흐름 확인 확인](diagnose-vm-network-traffic-filtering-problem.md)을 방문하여 특정 트래픽이 VM에서 허용되는지 알아봅니다.

<!-- Image references -->
