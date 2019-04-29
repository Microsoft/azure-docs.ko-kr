---
title: 포함 파일
description: 포함 파일
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550210"
---
### <a name="general-requirements"></a>일반 요구 사항

* VNet은 풀을 만들 때 사용한 Batch 계정과 동일한 구독 및 지역에 있어야 합니다.

* VNet을 사용하는 풀은 최대 4096개의 노드를 가질 수 있습니다.

* 풀에 대해 지정한 서브넷에는 풀에 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 즉 풀의 `targetDedicatedNodes` 및 `targetLowPriorityNodes` 속성 합계입니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 풀은 계산 노드를 부분적으로 할당하고 크기 조정 오류를 반환합니다. 

* VNet을 서비스하는 모든 사용자 지정 DNS 서버에서 Azure Storage 엔드포인트를 확인할 수 있어야 합니다. 특히 `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` 및 `<account>.blob.core.windows.net` 양식의 URL을 확인할 수 있어야 합니다. 

추가적인 VNet 요구 사항은 Batch 풀이 Virtual Machine 구성인지 또는 Cloud Services 구성인지에 따라 다릅니다. 새 풀을 VNet에 배포하는 경우 Virtual Machine 구성을 권장합니다.

### <a name="pools-in-the-virtual-machine-configuration"></a>Virtual Machine 구성의 풀

**VNet 지원** - Azure Resource Manager 기반 VNet만

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**사용 권한** - VNet 구독에서 보안 정책이나 잠금 또는 리소스 그룹이 VNet 관리를 위한 사용자 권한을 제한하고 있는지 확인합니다.

**추가 네트워킹 리소스** - Batch는 VNet을 포함하는 리소스 그룹에서 추가 네트워킹 리소스를 자동으로 할당합니다. 각 50 전용된 노드 (또는 각 20 개의 우선 순위가 낮은 노드)에 대 한 일괄 처리를 할당 합니다. 1 NSG (네트워크 보안 그룹), 공용 IP 주소 1과 1에는 부하 분산 장치. 이러한 리소스는 구독의 [리소스 할당량](../articles/azure-subscription-service-limits.md)으로 제한됩니다. 대형 풀의 경우 하나 이상의 리소스에 대한 할당량 증대를 요청해야 할 수 있습니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

서브넷에서는 계산 노드에서 작업을 예약하기 위해 Batch 서비스로부터의 인바운드 통신을 허용하고 Azure Storage 또는 기타 리소스와의 통신을 위해 아웃바운트 통신을 허용해야 합니다. Virtual Machine 구성의 풀에서 Batch는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

* 29876 및 29877 포트에서 Batch 서비스 역할 IP 주소로부터의 인바운드 TCP 트래픽  
* 원격 액세스 허용을 위한 포트 22(Linux 노드) 또는 포트 3389(Windows 노드) 인바운드 TCP 트래픽 Batch 계산 노드를 포함 하는 서브넷에서 Ip에 대 한 SSH 포트 22 트래픽을 허용 해야 (예: MPI)는 Linux에서 다중 인스턴스 작업 유형, 특정 합니다.
* 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽
* 인터넷에 대한 모든 포트의 아웃바운드 트래픽

> [!IMPORTANT]
> Batch 구성 NSG에서 인바운드 또는 아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. 지정된 서브넷에서 계산 노드와의 통신을 NSG에서 거부한 경우 Batch 서비스는 계산 노드의 상태를 **사용할 수 없음**으로 설정합니다.

Batch 구성에는 자체 NSG가 있으므로 서브넷 수준에서 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG(네트워크 보안 그룹) 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 외부 원본에서 풀 Vm에 원격 액세스를 허용 해야 하는 경우에 (Windows) 포트 3389에서 인바운드 트래픽을 또는 22 (Linux)를 구성 합니다. 풀 VM이 사용 가능할 필요는 없습니다. 특정 유형의 MPI 같은 다중 인스턴스 작업을 사용 하는 경우 Linux에 대 한 포트 22에서 가상 네트워크 서브넷 트래픽을 사용 하도록 설정 해야 note 합니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 서비스 태그 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 액션(Action) |
| --- | --- | --- | --- | --- | --- | --- |
| N/A | `BatchNodeManagement` [서비스 태그](../articles/virtual-network/security-overview.md#service-tags) | * | 모두 | 29876-29877 | TCP | 허용 |
| 필요한 경우 계산 노드 및/또는 Linux 다중 인스턴스 작업에 대 한 계산 노드 서브넷의 원격 액세스에 대 한 사용자 원본 Ip입니다. | N/A | * | 모두 | 3389(Windows), 22(Linux) | TCP | 허용 |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 서비스 태그 | 프로토콜 | 액션(Action) |
| --- | --- | --- | --- | --- | --- |
| 모두 | 443 | [Service 태그](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (동일한 지역의 Batch 계정 및 VNet으로)  | 모두 | 허용 |

### <a name="pools-in-the-cloud-services-configuration"></a>Cloud Services 구성의 풀

**VNet 지원** -클래식 VNet 전용

**서브넷 식별자** - Batch API를 사용하여 서브넷을 지정할 경우 서브넷의 *리소스 식별자*를 사용합니다. 서브넷 식별자 형식은 다음과 같습니다.

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**사용 권한** - `MicrosoftAzureBatch` 서비스 주체에는 지정된 VNet에 대한 `Classic Virtual Machine Contributor` RBAC(역할 기반 Access Control) 역할이 있어야 합니다.

#### <a name="network-security-groups"></a>네트워크 보안 그룹

서브넷에서는 계산 노드에서 작업을 예약하기 위해 Batch 서비스로부터의 인바운드 통신을 허용하고 Azure Storage 또는 기타 리소스와의 통신을 위해 아웃바운트 통신을 허용해야 합니다.

Batch는 Batch IP 주소로부터 풀 노드로 가는 인바운드 통신만 구성하므로 NSG를 지정할 필요가 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 지정된 서브넷에서 계산 노드와의 통신을 NSG에서 거부한 경우 Batch 서비스는 계산 노드의 상태를 **사용할 수 없음**으로 설정합니다.

풀 노드에 RDP 액세스를 허용 하는 경우 Windows에 대 한 포트 3389에서 인바운드 트래픽을 구성 합니다. 풀 노드가 사용 가능할 필요는 없습니다.

**인바운드 보안 규칙**

| 원본 IP 주소 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 액션(Action) |
| --- | --- | --- | --- | --- | --- |
모두 <br /><br />이렇게 하면 결과적으로 “모두 허용”이 필요하지만 Batch 서비스는 모든 Batch 외 서비스 IP 주소를 필터링하는 각 노드 수준에서 ACL을 적용합니다. | * | 모두 | 10100, 20100, 30100 | TCP | 허용 |
| 선택적으로 계산 노드에 대 한 RDP 액세스를 허용 합니다. | * | 모두 | 3389 | TCP | 허용 |

**아웃바운드 보안 규칙**

| 원본 | 원본 포트 | 대상 | 대상 포트 | 프로토콜 | 액션(Action) |
| --- | --- | --- | --- | --- | --- |
| 모두 | * | 모두 | 443  | 모두 | 허용 |
