---
title: Azure에서 아웃바운드 연결
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure에서 VM이 공용 인터넷 서비스를 사용하여 통신하는 방법을 설명합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 738b54d9fcd86313c2581c5d0f055a7cca8230b8
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706067"
---
# <a name="outbound-connections-in-azure"></a>Azure에서 아웃바운드 연결

Azure Load Balancer는 다른 메커니즘을 통해 아웃 바운드 연결을 제공 합니다. 이 문서에서는 시나리오 및 시나리오를 관리 하는 방법을 설명 합니다. Azure Load Balancer를 통한 아웃 바운드 연결에 문제가 발생 하는 경우 [아웃 바운드 연결에 대 한 문제 해결 가이드](../load-balancer/troubleshoot-outbound-connection.md)를 참조 하세요.

>[!NOTE]
>이 문서에서는 리소스 관리자 배포에 대해 설명 합니다. 프로덕션 워크 로드에 리소스 관리자 하는 것이 좋습니다.

## <a name="terminology"></a>용어

| 용어 | 적용 가능한 프로토콜 | 세부 정보|
| ---------|---------| -------|
| 원본 네트워크 주소 변환 (SNAT) | TCP, UDP | Azure에 배포되는 솔루션은 공용 IP 주소 공간에 있는 Azure 외부의 엔드포인트와 통신할 수 있습니다. 인스턴스가 공용 IP 주소 공간에서 대상에 대한 아웃바운드 흐름을 시작하면 Azure는 개인 IP 주소를 공용 IP 주소에 동적으로 매핑합니다. 이 매핑이 생성되면 이 아웃바운드에서 시작된 흐름에 대한 반환 트래픽도 흐름이 시작된 개인 IP 주소에 연결할 수 있습니다. Azure는 **SNAT (원본 네트워크 주소 변환)** 을 사용 하 여이 기능을 수행 합니다.|
| 포트 가장 SNAT(PAT)| TCP, UDP |  여러 개인 IP 주소가 단일 공용 IP 주소 뒤에 있는 경우 Azure는 **PAT (포트 주소 변환)** 를 사용 하 여 개인 ip 주소를 모조/숨깁니다. 삭제 포트는 PAT에 사용되고 풀 크기에 따라 [미리 할당](#preallocatedports)됩니다. 공용 Load Balancer 리소스가 전용 공용 IP 주소가 없는 VM 인스턴스와 연결되면 각 아웃바운드 연결 원본이 다시 작성됩니다. 원본은 가상 네트워크 개인 IP 주소 공간에서 부하 분산 장치의 프런트 엔드 공용 IP 주소로 다시 작성됩니다. 공용 IP 주소 공간에서 흐름의 5튜플(원본 IP 주소, 원본 포트, IP 전송 프로토콜, 대상 IP 주소, 대상 포트)은 고유해야 합니다. 포트 가장 SNAT는 TCP 또는 UDP IP 프로토콜과 함께 사용할 수 있습니다. 여러 흐름이 단일 공용 IP 주소에서 시작되므로 삭제 포트(SNAT 포트)는 프라이빗 원본 IP 주소를 다시 작성한 후 이 목적에 사용됩니다. 포트 위장 SNAT 알고리즘은 UDP와 TCP의 SNAT 포트를 다르게 할당합니다.|
| SNAT 포트| TCP | SNAT 포트는 특정 공용 IP 원본 주소에 사용할 수 있는 삭제 포트입니다. 단일 대상 IP 주소, 포트에 대한 흐름당 하나의 SNAT 포트가 사용됩니다. 동일한 대상 IP 주소, 포트 및 프로토콜에 대한 여러 TCP 흐름의 경우 각 TCP 흐름은 단일 SNAT 포트를 사용합니다. 이렇게 하면 흐름이 동일한 대상 IP 주소에서 시작하여 동일한 대상 IP 주소, 포트 및 프로토콜로 이동하는 경우 흐름의 고유성이 보장됩니다. 서로 다른 대상 IP 주소, 포트 및 프로토콜에 대한 여러 흐름은 단일 SNAT 포트를 공유합니다. 대상 IP 주소, 포트 및 프로토콜은 공용 IP 주소 공간에서 흐름을 구분하는 추가 원본 포트를 사용하지 않고도 흐름을 고유하게 만들어줍니다.|
|SNAT 포트 | UDP | UDP SNAT 포트는 TCP SNAT 포트와는 다른 알고리즘을 통해 관리됩니다.  부하 분산 장치는 UDP에 "포트 제한 원뿔형 NAT"이라고 하는 알고리즘을 사용합니다.  대상 IP 주소, 포트에 관계없이 각 흐름당 하나의 SNAT 포트가 사용됩니다.|
| 고갈 | - | SNAT 포트 리소스가 고갈되면 기존 흐름에서 SNAT 포트를 릴리스할 때까지 아웃바운드 흐름이 실패합니다. Load Balancer는 흐름이 닫히면 SNAT 포트를 회수하고 유휴 흐름에서 SNAT 포트를 회수하기 위해 [4분 유휴 시간 제한](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)을 사용합니다. 사용되는 알고리즘의 차이로 인해 일반적으로 UDP SNAT 포트가 TCP SNAT 포트보다 훨씬 빠르게 고갈됩니다. 이러한 차이를 염두에 두고 테스트를 설계하고 규모를 결정해야 합니다.|
| SNAT 포트 릴리스 동작 | TCP | 서버/클라이언트 중 하나가 FINACK를 보내면 240초 후에 SNAT 포트가 해제됩니다. RST가 표시되는 경우 15초 후에 SNAT 포트가 해제됩니다. 유휴 시간 제한에 도달하면 포트가 해제됩니다.|
| SNAT 포트 릴리스 동작 | UDP |유휴 시간 제한에 도달하면 포트가 해제됩니다.|
| SNAT 포트 재사용 | TCP, UDP | 포트를 해제한 후에는 필요에 따라 포트를 재사용할 수 있습니다.  SNAT 포트는 지정된 시나리오에서 사용 가능한 최하위부터 최상위까지의 시퀀스로 간주할 수 있으며, 첫 번째 사용 가능한 SNAT 포트는 새 연결에 사용됩니다.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>포트 할당 알고리즘

Azure는 PAT를 사용할 때 백 엔드 풀의 크기에 따라 사용 가능한 미리 할당 된 SNAT 포트 수를 결정 하는 알고리즘을 사용 합니다. 부하 분산 장치와 연결된 각 공용 IP 주소에는 각 IP 전송 프로토콜에 대한 SNAT 포트로 사용할 수 있는 64,000개의 포트가 있습니다. 동일한 수의 SNAT 포트가 UDP 및 TCP에 대해 각각 미리 할당되고 IP 전송 프로토콜별로 독립적으로 사용됩니다.  그러나 SNAT 포트 사용은 흐름이 UDP인지 아니면 TCP인지에 따라 다릅니다. 아웃 바운드 흐름이 생성 되 면 이러한 포트는 동적으로 (최대 미리 할당 된 제한) 사용 되 고 흐름이 닫히거나 [유휴 시간 초과가](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 발생할 때 릴리스됩니다. 포트는 흐름을 고유하게 만드는 데 필요한 경우에만 사용됩니다.

#### <a name="default-snat-ports-allocated"></a><a name="snatporttable"></a> 할당 된 기본 SNAT 포트

다음 표는 백 엔드 풀 크기 계층의 SNAT 포트 미리 할당을 보여줍니다.

| 풀 크기(VM 인스턴스) | IP 구성별로 미리 할당된 SNAT 포트 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

백 엔드 풀의 크기를 변경 하면 설정 된 흐름에 영향을 줄 수 있습니다.

- 백 엔드 풀 크기가 증가하여 그 다음 계층으로 전환되면 그 다음으로 큰 백 앤드 풀 계층으로 전환되는 동안 미리 할당된 SNAT 포트의 절반이 회수됩니다. 회수된 SNAT 포트와 연결된 흐름은 시간이 초과되며 다시 설정해야 합니다. 미리 할당된 포트를 사용할 수 있는 한, 새 흐름이 즉시 성공합니다.
- 백 엔드 풀 크기가 줄고 더 낮은 계층으로 전환되면 사용 가능한 SNAT 포트 수가 증가합니다. 이 경우 기존에 할당된 SNAT 포트와 각각의 흐름은 영향을 받지 않습니다.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>아웃 바운드 연결 시나리오 개요

| 시나리오 | 방법 | IP 프로토콜 | Description |
|  --- | --- | --- | --- |
|  1. 공용 IP 주소를 사용 하는 VM (Azure Load Balancer 유무 | SNAT, 포트 가장 사용 안 함 | TCP, UDP, ICMP, ESP | Azure는 모든 아웃 바운드 흐름에 대해 인스턴스의 NIC IP 구성에 할당 된 공용 IP를 사용 합니다. 인스턴스에 있는 모든 삭제 포트를 사용할 수 있습니다. VM이 부하 분산 되었는지 여부는 중요 하지 않습니다. 이 시나리오는 다른 시나리오에 우선합니다. VM에 할당된 공용 IP는 1:다가 아닌 1:1 관계이며 상태 비저장 1:1 NAT로 구현됩니다. |
| 2. VM과 연결 된 공용 Load Balancer (v m/인스턴스에 공용 IP 주소 없음) | Load Balancer 프런트 엔드를 사용하여 포트를 가장하는(PAT) SNAT | TCP, UDP | 이 시나리오에서는 백 엔드 풀을 사용 하 여 공용 IP 프런트 엔드 간에 링크를 만들기 위해 부하 분산 장치 규칙을 사용 하 여 Load Balancer 리소스를 구성 해야 합니다. 이 규칙 구성을 완료 하지 않으면 동작은 시나리오 3에 설명 된 대로 수행 됩니다. 백 엔드 풀 또는 상태 프로브에 작업 수신기가 있어야만 규칙이 성공하는 것은 아닙니다. VM이 아웃 바운드 흐름을 만들 때 Azure는 아웃 바운드 흐름의 개인 원본 IP 주소를 SNAT를 통해 공용 Load Balancer 프런트 엔드의 공용 IP 주소로 변환 합니다. 부하 분산 장치의 프런트 엔드 공용 IP 주소의 삭제 포트는 VM에서 발생 하는 개별 흐름을 구별 하는 데 사용 됩니다. SNAT은 아웃바운드 흐름이 생성될 때 동적으로 [삭제 포트를 미리 할당](#preallocatedports)합니다. 이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 부릅니다. SNAT 포트는 [할당 된 기본 snat 포트 테이블](#snatporttable)에 설명 된 대로 미리 할당 됩니다. |
| 3. VM (Load Balancer 없음, 공용 IP 주소 없음) 또는 기본 내부 Load Balancer와 연결 된 VM | 포트를 가장하는(PAT) SNAT | TCP, UDP | VM이 아웃바운드 흐름을 만든 경우 Azure에서는 아웃바운드 흐름의 프라이빗 원본 IP 주소를 공용 원본 IP 주소로 변환합니다. 이 공용 IP 주소는 **구성할**수 없으며 예약할 수 없으며 구독의 공용 ip 리소스 제한에 대해 계산 되지 않습니다. VM 또는 가용성 집합이나 가상 머신 확장 집합을 재배포하는 경우에는 이 공용 IP 주소가 해제되고 새 공용 IP 주소가 요청됩니다. IP 주소를 허용 목록에 추가할 때는 이 시나리오를 사용하지 말고, 대신 아웃 바운드 동작을 명시적으로 선언 하는 시나리오 1 또는 2를 사용 합니다. SNAT 포트는 [기본 snat 포트 할당 테이블](#snatporttable)에 설명 된 대로 미리 할당 된 됩니다.

## <a name="outbound-rules"></a><a name="outboundrules"></a>아웃 바운드 규칙

 아웃바운드 규칙을 사용하면 공용 [표준 Load Balancer](load-balancer-standard-overview.md)의 아웃바운드 네트워크 주소 변환을 쉽게 구성할 수 있습니다.  아웃바운드 연결을 완전하게 선언적으로 제어하여 이 기능을 특정 요구에 맞게 확장하고 튜닝할 수 있습니다. 이 섹션에서는 위에 설명 된 시나리오 2 (B)를 확장 합니다.

![Load Balancer 아웃바운드 규칙](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

아웃 바운드 규칙을 사용 하면 Load Balancer를 사용 하 여 아웃 바운드 NAT를 처음부터 정의할 수 있습니다. 기존 아웃 바운드 NAT의 동작을 확장 하 고 조정할 수도 있습니다.

아웃바운드 규칙을 통해 제어할 수 있는 항목은 다음과 같습니다.

- 특정 공용 IP 주소로 변환되어야 하는 가상 머신
- 아웃바운드 SNAT 포트를 할당해야 하는 방법
- 아웃바운드 변환을 제공하는 프로토콜
- 아웃바운드 연결 유휴 시간 제한에 사용할 기간(4-120분)
- 유휴 시간 제한 시 TCP 재설정을 보낼지 여부
- 단일 규칙을 사용 하는 TCP 및 UDP 전송 프로토콜 모두

### <a name="outbound-rule-definition"></a>아웃 바운드 규칙 정의

모든 Load Balancer 규칙 처럼 아웃 바운드 규칙은 부하 분산 및 인바운드 NAT 규칙 ( **프런트 엔드**  +  **매개 변수**  +  **백 엔드 풀**)과 동일한 친숙 한 구문을 따릅니다. 아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_에 대한 아웃바운드 NAT를 _프런트 엔드_로 변환하도록 구성합니다.  _매개 변수_ 는 아웃 바운드 NAT 알고리즘에 대 한 추가 세분화 된 제어를 제공 합니다.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 여러 IP 주소를 사용하여 아웃바운드 NAT 확장

프런트 엔드에서 제공 되는 각 추가 IP 주소는 Load Balancer에 대 한 추가 64000 삭제 포트를 제공 하 여 SNAT 포트로 사용 합니다. 여러 IP 주소를 사용하여 대규모 시나리오를 계획하고, 아웃바운드 규칙을 사용하여 [SNAT 고갈](troubleshoot-outbound-connection.md#snatexhaust) 가능성이 높은 경향의 패턴을 완화할 수 있습니다.  

아웃 바운드 규칙을 사용 하 여 직접 [공용 IP 접두사](https://aka.ms/lbpublicipprefix) 를 사용할 수도 있습니다.  공용 IP 접두사를 사용하면 Azure 배포에서 시작되는 흐름을 더 쉽게 크기 조정하고 허용 목록에 추가를 간소화할 수 있습니다. 공용 IP 주소 접두사를 직접 참조하도록 Load Balancer 리소스 내에 프런트 엔드 IP 구성을 구성할 수 있습니다.  이렇게 하면 Load Balancer에서 공용 IP 접두사를 독점적으로 제어하고, 아웃바운드 연결에 대한 공용 IP 접두사에 포함된 모든 공용 IP 주소가 아웃바운드 규칙에 자동으로 사용될 수 있습니다.  공용 IP 접두사 내의 각 IP 주소는 Load Balancer에 대 한 IP 주소 당 추가 64000 삭제 포트를 제공 하 여 SNAT 포트로 사용 합니다.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 아웃 바운드 흐름 유휴 시간 제한 및 TCP 다시 설정

아웃바운드 규칙은 아웃바운드 흐름 유휴 시간 제한을 제어하고 이를 애플리케이션의 요구와 일치시키는 구성 매개 변수를 제공합니다. 아웃바운드 유휴 시간 제한은 기본적으로 4분입니다. [유휴 시간 제한을 구성](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)하는 방법을 배울 수 있습니다. Load Balancer의 기본 동작은 아웃바운드 유휴 시간 제한에 도달하는 경우 흐름을 자동으로 삭제하는 것입니다.  `enableTCPReset`매개 변수를 사용 하면 보다 예측 가능한 응용 프로그램 동작을 사용 하도록 설정 하 고 아웃 바운드 유휴 시간 제한 시간에 양방향 Tcp Reset (TCP RST)을 보낼지 여부를 제어할 수 있습니다. 지역 가용성을 비롯 한 세부 정보는 [유휴 시간 제한에서 TCP 다시 설정](https://aka.ms/lbtcpreset) 을 검토 합니다.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>아웃바운드 연결 방지

부하 분산 규칙은 아웃 바운드 NAT의 자동 프로그래밍을 제공 합니다. 그러나 일부 시나리오에서는 동작을 제어하거나 구체화할 수 있도록 부하 분산 규칙을 통해 자동 아웃바운드 NAT 프로그래밍을 사용하지 않도록 설정하거나 이렇게 설정해야 합니다.  
이 매개 변수는 다음 두 가지 방법으로 사용할 수 있습니다.

1. 부하 분산 규칙에 대해 아웃 바운드 SNAT를 사용 하지 않도록 설정 하 여 아웃 바운드 SNAT에 대 한 인바운드 IP 주소 사용의 선택적 억제
  
2. 인바운드 및 아웃 바운드에 대해 동시에 사용 되는 IP 주소의 아웃 바운드 SNAT 매개 변수를 조정 합니다.  아웃바운드 규칙을 제어하려면 자동 아웃바운드 NAT 프로그래밍을 사용하지 않도록 설정해야 합니다.  예를 들어 인바운드에도 사용 되는 주소의 SNAT 포트 할당을 변경 하려면 `disableOutboundSnat` 매개 변수를 true로 설정 해야 합니다.  아웃바운드 규칙을 사용하여 인바운드에도 사용되는 IP 주소의 매개 변수를 재정의하려고 하지만 부하 분산 규칙의 아웃바운드 NAT 프로그래밍을 해제하지 않은 경우 아웃바운드 규칙을 구성하는 작업이 실패합니다.

>[!IMPORTANT]
> 이 매개 변수를 true로 설정 하 고 아웃 바운드 연결을 정의 하는 아웃 바운드 규칙을 포함 하지 않는 경우에는 가상 컴퓨터에서 아웃 바운드 연결이 설정 되지 않습니다.  VM 또는 애플리케이션의 일부 작업은 아웃바운드 연결을 사용할 수 있는지에 따라 달라질 수 있습니다. 시나리오의 종속성을 이해하고 이러한 변경의 영향을 고려해야 합니다.

VM이 아웃바운드 흐름을 만들도록 허용하는 것이 바람직하지 않은 경우가 있습니다. 또는 아웃바운드 흐름으로 연결할 수 있는 대상 또는 인바운드 흐름을 시작할 수 있는 대상을 관리하기 위한 요구 사항이 있을 수도 있습니다. 이 경우에 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 사용하여 VM에서 도달할 수 있는 대상을 관리합니다. 또한 NSG를 사용하여 인바운드 흐름을 시작할 수 있는 공용 대상을 관리할 수 있습니다.

부하 분산 VM에 NSG를 적용할 때 [서비스 태그](../virtual-network/security-overview.md#service-tags) 및 [기본 보안 규칙](../virtual-network/security-overview.md#default-security-rules)에 주의해야 합니다. VM에서 Azure Load Balancer의 상태 프로브 요청을 수신할 수 있는지 확인해야 합니다.

NSG가 AZURE_LOADBALANCER 기본 태그의 상태 프로브 요청을 차단할 경우 VM 상태 프로브에 실패하고 VM가 표시됩니다. 부하 분산 장치는 해당 VM에 새 흐름을 보내는 작업을 중지합니다.

## <a name="scenarios-with-outbound-rules"></a>아웃 바운드 규칙을 사용 하는 시나리오

| # | 시나리오| 세부 정보|
|---|---|---|
| I |  특정 공용 IP 주소 집합에 대한 아웃바운드 연결 영구 제거| 아웃바운드 규칙을 사용하여 특정 공용 IP 주소 집합에서 시작된 것으로 보이는 아웃바운드 연결을 영구 제거하여 허용 목록 시나리오를 쉽게 만들 수 있습니다.  이 원본 공용 IP 주소는 부하 분산 규칙에서 사용하는 것과 같거나 부하 분산 규칙에서 사용하는 것과 다른 공용 IP 주소 집합일 수 있습니다.  1. 공용 ip [접두사](https://aka.ms/lbpublicipprefix) (또는 공용 ip 접두사에서 공용 ip 주소) 2를 만듭니다. 공용 표준 Load Balancer 3을 만듭니다. 4를 사용 하려는 공용 IP 접두사 (또는 공용 IP 주소)를 참조 하는 프런트 엔드를 만듭니다. 백 엔드 풀을 다시 사용 하거나 백 엔드 풀을 만들고 Vm을 공용 Load Balancer 5의 백 엔드 풀에 저장 합니다. 프런트 엔드를 사용 하 여 이러한 Vm에 대 한 아웃 바운드 NAT를 프로그래밍 하기 위해 공용 Load Balancer에 대 한 아웃 바운드 규칙을 구성 합니다. 아웃바운드에 부하 분산 규칙을 사용하지 않으려면 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정해야 합니다.
| II |  SNAT 포트 할당 수정| 아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝할 수 있습니다. 예를 들어 아웃바운드 NAT에 대해 하나의 공용 IP 주소를 공유하는 두 개의 가상 머신이 있는 경우 SNAT가 고갈되었으면 기본 1024 포트에서 할당되는 SNAT 포트 수를 늘리는 것이 좋습니다. 각 공용 IP 주소는 최대 64,000개의 사용 후 삭제 포트를 제공할 수 있습니다.  단일 공용 IP 주소 프런트 엔드를 사용하는 아웃바운드 규칙을 구성하면 총 64,000개의 SNAT 포트를 백 엔드 풀의 VM에 배포할 수 있습니다.  두 VM의 경우 아웃바운드 규칙을 사용하여 최대 32,000개의 SNAT 포트를 할당할 수 있습니다(2 x 32,000 = 64,000). 아웃 바운드 규칙을 사용 하 여 기본적으로 할당 된 SNAT 포트를 튜닝할 수 있습니다. 기본 SNAT 포트 할당에 제공 되는 것 보다 더 많은 또는 더 작은 공간을 할당 합니다. 아웃 바운드 규칙의 모든 프런트 엔드 공용 IP 주소는 SNAT 포트로 사용 하기 위해 최대 64000 개의 임시 포트를 제공 합니다.  Load Balancer는 SNAT 포트를 8의 배수로 할당합니다. 8로 나눌 수 없는 값을 제공하면 구성 작업이 거부됩니다.  공용 IP 주소의 수를 기준으로 사용 가능한 것보다 더 많은 SNAT 포트를 할당하려고 하면 구성 작업이 거부됩니다.  예를 들어 VM 당 1만 포트를 할당 하 고 백 엔드 풀의 Vm 7 개에서 단일 공용 IP 주소를 공유 하는 경우 구성이 거부 됩니다 (7 x 1만 SNAT 포트 > 64000 SNAT 포트).  아웃바운드 규칙의 프런트 엔드에 더 많은 공용 IP 주소를 추가하여 시나리오를 사용하도록 설정할 수 있습니다. 포트 수에 0을 지정 하 여 [백 엔드 풀 크기에 따라 기본 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports) 으로 되돌릴 수 있습니다. 이 경우 첫 번째 50 VM 인스턴스는 1024 포트를 얻게 됩니다. 51-100 VM 인스턴스는 [테이블](#snatporttable)에 따라 512을 받습니다.|
| III|  아웃바운드만 사용 | 공용 표준 Load Balancer를 사용하여 VM 그룹에 아웃바운드 NAT를 제공할 수 있습니다. 이 시나리오에서는 추가 규칙 없이 아웃바운드 규칙 자체만 사용할 수 있습니다.|
| IV | VM 전용 아웃바운드 NAT(인바운드 없음) | 공용 표준 Load Balancer를 정의하고, 백 엔드 풀에 VM을 배치하고, 아웃바운드 NAT를 프로그래밍하고 특정 공용 IP 주소에서 시작되는 아웃바운드 연결을 영구 제거하도록 아웃바운드 규칙을 구성합니다. 또한 공용 IP 접두사를 사용하여 아웃바운드 연결의 원본을 허용 목록에 추가하도록 간소화할 수 있습니다. 1. 공용 표준 Load Balancer를 만듭니다. 2. 백 엔드 풀을 만들고 공용 Load Balancer의 백 엔드 풀에 VM을 배치합니다. 3. 공용 Load Balancer에서 이러한 VM에 대해 아웃바운드 NAT를 프로그래밍하도록 아웃바운드 규칙을 구성합니다.
| V| 내부 표준 Load Balancer 시나리오에 대한 아웃바운드 NAT| 내부 표준 Load Balancer를 사용하는 경우 아웃바운드 연결이 명시적으로 선언될 때까지 아웃바운드 NAT를 사용할 수 없습니다. 아웃 바운드 규칙을 사용 하 여 아웃 바운드 연결을 정의 하 고 다음 단계를 통해 내부 표준 Load Balancer 뒤에 Vm에 대 한 아웃 바운드 연결을 만들 수 있습니다. 1. 공용 표준 Load Balancer를 만듭니다. 2. 백 엔드 풀을 만들고 내부 Load Balancer 외에, 공용 Load Balancer의 백 엔드 풀에 VM을 배치합니다. 3. 공용 Load Balancer에서 이러한 VM에 대해 아웃바운드 NAT를 프로그래밍하도록 아웃바운드 규칙을 구성합니다. 이 시나리오에 대 한 자세한 내용은 [이 예제](https://docs.microsoft.com/azure/load-balancer/egress-only)를 참조 하세요. |
| VI | 공용 표준 Load Balancer를 통해 아웃바운드 NAT에 TCP 및 UDP 프로토콜을 모두 사용 | 공용 표준 Load Balancer를 사용하는 경우 제공되는 자동 아웃바운드 NAT 프로그래밍은 부하 분산 규칙의 전송 프로토콜과 일치합니다. 1. 부하 분산 규칙에서 아웃바운드 SNAT를 사용하지 않도록 설정합니다. 2. 동일한 Load Balancer에서 아웃바운드 규칙을 구성합니다. 3. VM에서 이미 사용하고 있는 백 엔드 풀을 다시 사용합니다. 4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다. 인바운드 NAT 규칙만 사용하는 경우 아웃바운드 NAT는 제공되지 않습니다. 1. 백 엔드 풀에 VM을 배치합니다. 2. 공용 ip 주소 또는 공용 IP 접두사 3을 사용 하 여 하나 이상의 프런트 엔드 IP 구성을 정의 합니다. 동일한 Load Balancer에서 아웃바운드 규칙을 구성합니다. 4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다. |


## <a name="limitations"></a>제한 사항

- 프런트 엔드 IP 주소당 사용 가능한 사용 후 삭제 포트의 최대 수는 64,000개입니다.
- 구성 가능한 아웃바운드 유휴 시간 제한 범위는 4-120분(240-7200초)입니다.
- Load Balancer는 아웃바운드 NAT에 대해 ICMP를 지원하지 않습니다.
- 아웃 바운드 규칙은 NIC의 기본 IP 구성에만 적용할 수 있습니다.  VM 또는 NVA의 보조 IP에 대 한 아웃 바운드 규칙을 만들 수 없습니다. 여러 Nic가 지원 됩니다.
- VNet 및 기타 Microsoft 플랫폼 서비스가 없는 웹 작업자 역할은 사전 VNet 서비스 및 다른 플랫폼 서비스 작동 방식의 부작용으로 인해 내부 표준 Load Balancer만 사용할 때 액세스할 수 있습니다. 각 서비스 자체 또는 기본 플랫폼은 사전 통보 없이 변경될 수 있으므로 이 부작용을 사용하지 마세요. 내부 표준 Load Balancer만 사용하는 경우 원하면 명시적으로 아웃 바운드 연결을 만들어야 한다고 항상 가정해야 합니다. 이 문서에서 설명 하는 시나리오 3은 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Standard Load Balancer](load-balancer-standard-overview.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대 한 질문과 대답](load-balancer-faqs.md)을 참조 하세요.
- 표준 공용 Load Balancer에 대한 [아웃바운드 규칙](load-balancer-outbound-rules-overview.md)을 자세히 알아봅니다.
- [Load Balancer](load-balancer-overview.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
- Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)에 대해 알아보세요.
