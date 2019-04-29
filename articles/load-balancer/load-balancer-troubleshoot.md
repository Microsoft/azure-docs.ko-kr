---
title: Azure Load Balancer 문제 해결
titlesuffix: Azure Load Balancer
description: Azure Load Balancer의 알려진 문제 해결
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: c5f92d564a93823fd9c0f932fa95f20d4e827761
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734478"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer 문제 해결

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

이 페이지에서는 일반적인 Azure Load Balancer 질문에 대한 문제 해결 정보를 제공합니다. Load Balancer 연결을 사용할 수 없을 때 가장 일반적인 증상은 다음과 같습니다. 
- Load Balancer 뒤의 VM이 상태 프로브에 응답하지 않습니다. 
- Load Balancer 뒤의 VM이 구성된 포트의 트래픽에 응답하지 않습니다.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>증상: Load Balancer 뒤의 VM이 상태 프로브에 응답하지 않습니다.
백 엔드 서버가 Load Balancer 집합에 참여하려면 프로브 검사를 통과해야 합니다. 상태 프로브에 대한 자세한 내용은 [Load Balancer 프로브 이해](load-balancer-custom-probe-overview.md)를 참조하세요. 

Load Balancer 백 엔드 풀 VM은 다음 이유 중 하나로 인해 프로브에 응답하지 않을 수 있습니다. 
- Load Balancer 백 엔드 풀 VM이 정상적인 상태가 아닙니다. 
- Load Balancer 백 엔드 풀 VM이 프로브 포트에서 수신하지 않습니다. 
- 방화벽 또는 네트워크 보안 그룹이 Load Balancer 백 엔드 풀 VM에서 포트를 차단하고 있습니다. 
- Load Balancer의 기타 구성 오류

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>원인 1: Load Balancer 백 엔드 풀 VM이 정상적인 상태가 아닙니다. 

**유효성 검사 및 해결**

이 문제를 해결하려면 참여하는 VM에 로그인하고 VM 상태가 정상인지와 풀에 있는 다른 VM의 **PsPing** 또는 **TCPing**에 응답할 수 있는지 확인합니다. VM이 정상이 아니거나 프로브에 응답할 수 없으면 부하 분산에 사용하기 전에 문제를 해결하고 VM을 다시 정상 상태로 복구해야 합니다.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>원인 2: Load Balancer 백 엔드 풀 VM이 프로브 포트에서 수신하지 않습니다.
VM이 정상 상태인데도 프로브에 응답하지 않을 경우 한 가지 가능한 원인은 프로브 포트가 참여 VM에서 열려 있지 않거나 VM이 해당 포트에서 수신하지 않는 것입니다.

**유효성 검사 및 해결**

1. 백 엔드 VM에 로그인합니다. 
2. 명령 프롬프트를 열고 다음 명령을 실행하여 프로브 포트에서 수신 대기하는 애플리케이션이 있는지 확인합니다.   
            netstat -an
3. 포트 상태가 **LISTENING**으로 표시되지 않으면 해당 포트를 구성합니다. 
4. 또는 **LISTENING**으로 표시되는 다른 포트를 선택한 후 부하 분산 장치 구성을 적절하게 업데이트합니다.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>원인 3: 방화벽 또는 네트워크 보안 그룹이 부하 분산 장치 백 엔드 풀 VM에서 포트를 차단하고 있습니다.  
VM의 방화벽이 프로브 포트를 차단하고 있거나 서브넷 또는 VM에 구성된 하나 이상의 네트워크 보안 그룹이 프로브가 포트에 연결하도록 허용하지 않을 경우 VM이 상태 프로브에 응답할 수 없게 됩니다.          

**유효성 검사 및 해결**

* 방화벽이 사용하도록 설정된 경우 프로브 포트를 허용하도록 구성되어 있는지 확인합니다. 그렇지 않으면 프로브 포트의 트래픽을 허용하도록 방화벽을 구성한 후 다시 테스트합니다. 
* 네트워크 보안 그룹 목록에서 프로브 포트에서 들어오거나 나가는 트래픽에 간섭에 있는지 확인합니다. 
* 또한 VM 또는 서브넷의 NIC에 대해 LB 프로브 또는 트래픽을 허용하는 기본 규칙보다 우선 순위가 더 높은 **모두 거부** 네트워크 보안 그룹 규칙이 있는지 확인합니다(네트워크 보안 그룹은 168.63.129.16의 Load Balancer IP를 허용해야 함). 
* 이러한 규칙에 의해 프로브 트래픽이 차단되는 경우 해당 규칙을 제거한 후 프로브 트래픽을 허용하도록 다시 구성합니다.  
* VM이 상태 프로브에 응답하기 시작했는지 테스트합니다. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>원인 4: Load Balancer의 기타 구성 오류
위의 모든 원인에 대해 유효성이 검사되고 문제가 적절히 해결된 것 같으나 백 엔드 VM이 여전히 상태 프로브에 응답하지 않으면 연결을 수동으로 테스트하고 연결을 이해하기 위한 몇 가지 추적을 수집합니다.

**유효성 검사 및 해결**

* VNet 내의 다른 VM 중 하나에서 **Psping**을 사용하여 프로브 포트 응답을 테스트하고(예: .\psping.exe -t 10.0.0.4:3389) 결과를 기록합니다. 
* VNet 내의 다른 VM 중 하나에서 **TCPing**을 사용하여 프로브 포트 응답을 테스트하고(예: ..\tcping.exe 10.0.0.4 3389) 결과를 기록합니다. 
* 이러한 ping 테스트에서 응답이 수신되지 않으면 다음을 수행합니다.
    - 동일한 VNet에서 대상 백 엔드 풀 VM 및 다른 테스트 VM에 대해 동시 Netsh 추적을 실행합니다. 이제 얼마 동안 PsPing 테스트를 실행하고 일부 네트워크 추적을 수집한 후 테스트를 중지합니다. 
    - 네트워크 캡처를 분석하고 ping 쿼리와 관련해서 들어오고 나가는 패킷이 둘다 있는지 확인합니다. 
        - 백 엔드 풀 VM에서 들어오는 패킷이 확인되지 않으면 트래픽을 차단하는 네트워크 보안 그룹 또는 UDR 구성 오류가 있는 것일 수 있습니다. 
        - 백 엔드 풀 VM에서 나가는 패킷이 확인 되 면 VM 관련 되지 않은 문제 (예: 프로브 포트를 차단 하는 응용 프로그램)에 있는지 확인 해야 합니다. 
    - 프로브 패킷이 부하 분산 장치에 도달하기 전에 강제로 다른 대상으로 전달되는지 확인합니다(UDR 설정을 통해). 이로 인해 트래픽이 백엔드 VM에 절대 도달하지 못할 수 있습니다. 
* 프로브 형식을 변경하고(예: HTTP에서 TCP로) 네트워크 보안 그룹 ACL의 해당 포트 및 방화벽이 프로브 응답 구성에 문제가 있는지 평가하도록 구성합니다. 상태 프로브 구성에 대한 자세한 내용은 [엔드포인트 부하 분산 장치 상태 프로브 구성](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)을 참조하세요.

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>증상: Load Balancer 뒤의 VM이 구성된 데이터 포트의 트래픽에 응답하지 않습니다.

백 엔드 풀 VM이 정상으로 표시되고 상태 프로브에 응답하지만 여전히 부하 분산에 참여하지 않거나 데이터 트래픽에 응답하지 않을 경우 다음 이유 중 하나 때문일 수 있습니다. 
* Load Balancer 백 엔드 풀 VM이 데이터 포트에서 수신하지 않습니다. 
* 네트워크 보안 그룹이 Load Balancer 백 엔드 풀 VM에서 포트를 차단하고 있습니다.  
* 동일한 VM 및 NIC에서 Load Balancer에 액세스 
* 참여하는 Load Balancer 백 엔드 풀 VM에서 인터넷 Load Balancer 프런트 엔드에 액세스 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>원인 1: Load Balancer 백 엔드 풀 VM이 데이터 포트에서 수신 대기하지 않습니다. 
VM이 데이터 트래픽에 응답하지 않을 경우 대상 포트가 참여 VM에서 열려 있지 않거나 VM이 해당 포트에서 수신하지 않기 때문일 수 있습니다. 

**유효성 검사 및 해결**

1. 백 엔드 VM에 로그인합니다. 
2. 명령 프롬프트를 열고 다음 명령을 실행하여 데이터 포트에서 수신 대기하는 애플리케이션이 있는지 확인합니다.   netstat -an 
3. 포트 상태가 “LISTENING” 상태로 표시되지 않으면 해당 수신기 포트를 구성합니다. 
4. 포트가 Listening으로 표시되어 있는 경우 해당 포트의 대상 애플리케이션에 문제가 있는지 확인합니다. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>원인 2: 네트워크 보안 그룹이 Load Balancer 백 엔드 풀 VM에서 포트를 차단하고 있습니다.  

서브넷 또는 VM에 구성된 하나 이상의 네트워크 보안 그룹이 원본 IP 또는 포트를 차단하는 경우 VM이 응답할 수 없습니다.

* 백 엔드 VM에 구성된 네트워크 보안 그룹을 나열합니다. 자세한 내용은 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하세요.
* 네트워크 보안 그룹 목록에서 다음을 확인합니다.
    - 데이터 포트에서 들어오거나 나가는 트래픽에 간섭이 있습니다. 
    - VM 또는 서브넷의 NIC에 대해 Load Balancer 프로브 및 트래픽을 허용하는 기본 규칙보다 우선 순위가 더 높은 **모두 거부** 네트워크 보안 그룹 규칙(네트워크 보안 그룹은 프로브 포트에 해당하는 168.63.129.16의 부하 분산 장치 IP를 허용해야 함). 
* 규칙에 의해 트래픽이 차단되는 경우 해당 규칙을 제거한 후 데이터 트래픽을 허용하도록 다시 구성합니다.  
* VM이 상태 프로브에 응답하기 시작했는지 테스트합니다.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>원인 3: 동일한 VM 및 네트워크 인터페이스에서 Load Balancer에 액세스 

Load Balancer의 백 엔드 VM에서 호스트된 애플리케이션이 동일한 네트워크 인터페이스를 통해 동일한 백엔드 VM에서 호스트되는 다른 애플리케이션에 액세스하려고 하는 경우 이는 지원되지 않는 시나리오이며 실패합니다. 

**해결 방법** 다음 방법 중 하나를 사용하여 이 문제를 해결할 수 있습니다.
* 애플리케이션마다 별도 백 엔드 풀 VM을 구성합니다. 
* 각 애플리케이션이 자체 네트워크 인터페이스 및 IP 주소를 사용하도록 이중 NIC VM에 애플리케이션을 구성합니다. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>원인 4: 참여하는 Load Balancer 백 엔드 풀 VM에서 내부 Load Balancer 프런트 엔드에 액세스

내부 Load Balancer가 VNet 내에서 구성되고, 참여하는 백 엔드 Load Balancer 중 하나가 내부 Load Balancer 프런트 엔드에 액세스하려고 하면 흐름이 원본 VM에 매핑될 때 오류가 발생할 수 있습니다. 이 시나리오는 지원되지 않습니다. 자세한 내용은 [제한 사항](load-balancer-overview.md#limitations)을 검토하세요.

**해결 방법** 프록시를 사용하여이 시나리오의 차단을 해제하는 방법이 몇 가지 있습니다. Application Gateway 또는 기타 타사 프록시(예: nginx 또는 haproxy)를 평가하세요. Application Gateway에 대한 자세한 내용은 [Application Gateway에 대한 개요](../application-gateway/application-gateway-introduction.md)를 참조하세요.

## <a name="additional-network-captures"></a>추가 네트워크 캡처
지원 사례를 열기로 결정한 경우 더 빠른 해결을 위해 다음 정보를 수집합니다. 단일 백 엔드 VM을 선택하여 다음과 같은 테스트를 수행합니다.
- VNet 내의 백 엔드 VM 중 하나에서 Psping을 사용하여 프로브 포트 응답을 테스트하고(예: psping 10.0.0.4:3389) 결과를 기록합니다. 
- 이러한 ping 테스트에서 응답이 수신되지 않으면 PsPing을 실행하는 동안 백 엔드 VM 및 VNet 테스트 VM에 대해 동시 Netsh 추적을 실행한 후 Netsh 추적을 중지합니다. 
  
## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.

