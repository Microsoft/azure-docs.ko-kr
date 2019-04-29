---
title: Azure Network Watcher의 리소스 문제 해결 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher 리소스 문제 해결 기능에 대한 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: aa7fce21228d4413dc4964d6e828bf60478aee27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60682949"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Azure Network Watcher의 리소스 문제 해결 소개

Virtual Network 게이트웨이는 온-프레미스 리소스 및 Azure 내 다른 가상 네트워크 간의 연결을 제공합니다. 게이트웨이 및 해당 연결을 모니터링하는 것은 통신이 끊기지 않도록 하는 데 중요합니다. Network Watcher는 게이트웨이 및 연결 문제를 해결하는 기능을 제공합니다. 이 기능은 포털, PowerShell, Azure CLI 또는 REST API를 통해 호출할 수 있습니다. 호출하면 Network Watcher가 게이트웨이 또는 연결의 상태를 진단하고 적절한 결과를 반환합니다. 요청은 장기 실행 트랜잭션입니다. 진단이 완료되면 결과가 반환됩니다.

![portal][2]

## <a name="results"></a>결과

반환되는 예비 결과는 전반적인 리소스 상태에 대한 정보를 제공합니다. 다음 섹션에 표시된 대로 리소스에 대한 심층적인 정보를 제공할 수 있습니다.

다음은 문제 해결 API와 함께 반환되는 값의 목록입니다.

* **startTime** - 이 값은 문제 해결 API 호출이 시작된 시간입니다.
* **endTime** - 이 값은 문제 해결이 종료된 시간입니다.
* **code** - 이 값은 단일 진단 오류가 발생할 경우 이 값은 UnHealthy입니다.
* **results** - 연결 또는Virtual Network 게이트웨이에 대해 반환되는 결과의 모음입니다.
    * **id** - 이 값은 오류 유형입니다.
    * **summary** - 이 값은 오류에 대한 요약입니다.
    * **detailed** - 이 값은 오류에 대한 자세한 설명을 제공합니다.
    * **recommendedActions** - 이 속성은 수행을 권장하는 조치의 모음입니다.
      * **actionText** - 이 값은 수행할 조치를 설명하는 텍스트를 포함합니다.
      * **actionUri** - 조치 방법에 대한 설명서로 연결되는 URI를 제공합니다.
      * **actionUriText** - 이 값은 조치 텍스트에 대한 간단한 설명입니다.

다음 표에서는 발생할 수 있는 다양한 오류 유형(이전 목록에서 results 아래의 id)과 오류 로그 생성 여부를 보여줍니다.

### <a name="gateway"></a>게이트웨이

| 오류 유형 | 이유 | 로그|
|---|---|---|
| NoFault | 오류가 발견되지 않은 경우 |예|
| GatewayNotFound | 게이트웨이를 찾을 수 없거나 게이트웨이가 프로비저닝되지 않았습니다. |아닙니다.|
| PlannedMaintenance |  게이트웨이 인스턴스가 유지 관리되고 있습니다.  |아닙니다.|
| UserDrivenUpdate | 이 오류는 사용자 업데이트를 진행 중인 경우 발생합니다. 업데이트는 크기 조정 작업일 수 있습니다. | 아닙니다. |
| VipUnResponsive | 이 오류는 게이트웨이의 주 인스턴스가 상태 프로브 실패로 인해 연결할 수 없을 때 발생합니다. | 아닙니다. |
| PlatformInActive | 플랫폼에 문제가 있습니다. | 아니요|
| ServiceNotRunning | 기본 서비스가 실행되고 있지 않습니다. | 아닙니다.|
| NoConnectionsFoundForGateway | 게이트웨이에 연결이 존재하지 않습니다. 이 오류는 단지 경고일 뿐입니다.| 아닙니다.|
| ConnectionsNotConnected | 연결이 연결되지 않습니다. 이 오류는 단지 경고일 뿐입니다.| 예|
| GatewayCPUUsageExceeded | 현재 게이트웨이 CPU 사용량이 95%를 초과했습니다. | 예 |

### <a name="connection"></a>연결

| 오류 유형 | 이유 | 로그|
|---|---|---|
| NoFault | 오류가 발견되지 않은 경우 |예|
| GatewayNotFound | 게이트웨이를 찾을 수 없거나 게이트웨이가 프로비저닝되지 않았습니다. |아닙니다.|
| PlannedMaintenance | 게이트웨이 인스턴스가 유지 관리되고 있습니다.  |아닙니다.|
| UserDrivenUpdate | 이 오류는 사용자 업데이트를 진행 중인 경우 발생합니다. 업데이트는 크기 조정 작업일 수 있습니다.  | 아닙니다. |
| VipUnResponsive | 이 오류는 게이트웨이의 주 인스턴스가 상태 프로브 실패로 인해 연결할 수 없을 때 발생합니다. | 아닙니다. |
| ConnectionEntityNotFound | 연결 구성이 없습니다. | 아닙니다. |
| ConnectionIsMarkedDisconnected | 연결이 “연결 끊김”으로 표시되었습니다. |아닙니다.|
| ConnectionNotConfiguredOnGateway | 기본 서비스에 연결이 구성되어 있지 않습니다. | 예 |
| ConnectionMarkedStandby | 기본 서비스가 대기로 표시되었습니다.| 예|
| Authentication | 미리 공유한 키가 일치하지 않습니다. | 예|
| PeerReachability | 피어 게이트웨이에 연결할 수 없습니다. | 예|
| IkePolicyMismatch | 피어 게이트웨이에 Azure에서 지원되지 않는 IKE 정책이 있습니다. | 예|
| WfpParse Error | WFP 로그를 구문 분석하는 동안 오류가 발생했습니다. |예|

## <a name="supported-gateway-types"></a>지원되는 게이트웨이 유형

다음 표에는 Network Watcher 문제 해결로 지원되는 게이트웨이 및 연결이 나열되어 있습니다.

|  |  |
|---------|---------|
|**게이트웨이 유형**   |         |
|VPN      | 지원됨        |
|ExpressRoute | 지원되지 않음 |
|**VPN 유형** | |
|경로 기반 | 지원됨|
|정책 기반 | 지원되지 않음|
|**연결 유형**||
|IPSec| 지원됨|
|VNet2Vnet| 지원됨|
|ExpressRoute| 지원되지 않음|
|VPNClient| 지원되지 않음|

## <a name="log-files"></a>로그 파일

리소스 문제 해결 로그 파일은 리소스 문제 해결이 완료된 후 저장소 계정에 저 됩니다. 다음 그림에서는 오류를 발생시킨 호출의 내용을 예를 들어 보여 줍니다.

![Zip 파일][1]

> [!NOTE]
> 일부 경우에는 로그 파일의 하위 집합만 저장소에 기록됩니다.

Azure Storage 계정에서 파일을 다운로드하는 방법에 대한 지침은 [.NET을 사용하여 Azure Blob Storage 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요. 사용할 수 있는 다른 도구는 저장소 탐색기입니다. Storage 탐색기에 대한 자세한 내용은 다음 링크에서 찾을 수 있습니다. [Storage 탐색기](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** 파일에는 송/수신 바이트, 연결 상태 및 연결이 설정된 시간 등 연결의 전체 통계가 포함됩니다.

> [!NOTE]
> 문제 해결 API에 대한 호출 결과가 정상일 경우 Zip 파일에 **ConnectionStats.txt** 파일만 반환됩니다.

이 파일의 내용은 다음 예제와 비슷합니다.

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** 파일에는 CPU 사용량 및 테스트 당시 사용 가능한 메모리 파일이 포함됩니다.  이 파일의 내용은 다음 예제와 비슷합니다.

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** 파일에는 모니터링 중에 발견된 IKE 오류가 포함됩니다.

다음 예제에서는 IKEErrors.txt 파일의 내용을 보여 줍니다. 오류는 문제에 따라 달라질 수 있습니다.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

**Scrubbed wfpdiag.txt** 로그 파일에는 wfp 로그가 포함됩니다. 이 로그에는 패킷 삭제 및 IKE/AuthIP 오류에 대한 기록이 포함됩니다.

다음 예제에서는 Scrubbed-wfpdiag.txt 파일의 내용을 보여 줍니다. 이 예제에서는 아래에서 세 번째 줄에서 볼 수 있듯이 연결의 공유 키가 잘못되었습니다. 다음 예제는 전체 로그의 일부에 불과합니다. 로그는 문제에 따라 길어질 수 있습니다.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

**wfpdiag.txt.sum** 파일은 버퍼 및 처리된 이벤트를 표시하는 로그입니다.

다음 예제는 wfpdiag.txt.sum 파일의 내용입니다.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>다음 단계

게이트웨이 또는 게이트웨이 연결 문제를 진단하는 방법은 [네트워크 간 통신 문제 진단](diagnose-communication-problem-between-networks.md)을 참조하세요.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
