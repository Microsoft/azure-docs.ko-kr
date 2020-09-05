---
title: Azure의 네트워크 성능 모니터 | Microsoft Docs
description: Azure의 네트워크 성능 모니터를 사용하면 네트워크 성능을 거의 실시간으로 모니터링하여 네트워크 성능 병목을 감지하고 위치를 찾을 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 34e81076c27086ad838cca23de0e150a3c1b076c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798910"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Azure의 네트워크 성능 모니터 솔루션

![네트워크 성능 모니터 기호](./media/network-performance-monitor/npm-symbol.png)


네트워크 성능 모니터는 네트워크 인프라의 다양한 지점 간 네트워크 성능을 모니터링하는 데 도움이 되는 클라우드 기반 하이브리드 네트워크 모니터링 솔루션입니다. 또한 서비스 및 애플리케이션 엔드포인트에 대한 네트워크 연결을 모니터링하고 Azure ExpressRoute의 성능을 모니터링하는 데 도움이 됩니다. 

네트워크 성능 모니터는 트래픽 블랙홀링, 라우팅 오류와 같은 네트워크 문제와 기존 네트워크 모니터링 방법으로 감지할 수 없는 문제를 감지합니다. 이 솔루션은 네트워크 링크에 임계값이 위반되면 경고를 생성하고 사용자에게 알립니다. 또한 네트워크 성능 문제를 적시에 감지하고 문제의 원인을 특정 네트워크 세그먼트 또는 디바이스로 국한시킵니다. 

네트워크 성능 모니터는 세 가지 광범위한 기능을 제공합니다. 

* [성능 모니터](network-performance-monitor-performance-monitor.md): 클라우드 배포 및 온-프레미스 위치, 여러 데이터 센터, 지점, 업무에 중요한 다중 계층 애플리케이션 또는 마이크로 서비스 간의 네트워크 연결을 모니터링할 수 있습니다. 성능 모니터를 사용하면 사용자가 불만을 제기하기 전에 네트워크 문제를 감지할 수 있습니다.

* [서비스 연결 모니터](network-performance-monitor-service-connectivity.md): 관심 있는 서비스에 대한 사용자의 연결을 모니터링하고, 경로에 어떤 인프라가 있는지 확인하고, 네트워크 병목 현상이 발생하는 위치를 식별할 수 있습니다. 사용자보다 먼저 중단에 대해 알 수 있고, 네트워크 경로에서 문제의 위치를 정확히 볼 수 있습니다. 

    이 기능을 사용하면 HTTP, HTTPS, TCP 및 ICMP를 기반으로 테스트를 수행하여 서비스의 가용성 및 응답 시간을 거의 실시간으로 또는 시간별로 모니터링할 수 있습니다. 또한 패킷 손실 및 대기 시간에 대한 네트워크의 영향도 모니터링할 수 있습니다. 네트워크 토폴로지 맵을 통해 네트워크 속도 저하를 격리시킬 수 있습니다. 각 홉의 대기 시간 데이터를 통해, 노드에서 서비스로 가는 네트워크 경로에서 발생하는 문제 지점을 식별할 수 있습니다. 기본 제공 테스트를 통해 사전 구성 없이 Office 365, Dynamics CRM에 대한 네트워크 연결을 모니터링할 수 있습니다. 이 기능을 사용하여 웹 사이트, SaaS 애플리케이션, PaaS 애플리케이션, SQL 데이터베이스 등과 같은 TCP 지원 엔드포인트에 대한 네트워크 연결을 모니터링할 수 있습니다.

* [ExpressRoute 모니터링](network-performance-monitor-expressroute.md): Azure ExpressRoute를 통해 지사와 Azure 간의 엔드투엔드 연결 및 성능을 모니터링합니다.  

[네트워크 성능 모니터](../../networking/network-monitoring-overview.md)에서 지원하는 다양한 기능에 대한 자세한 내용은 온라인으로 제공됩니다.
 
## <a name="supported-regions"></a>지원되는 지역
NPM은 다음 지역 중 한 곳에서 호스트되는 작업 영역에서 전 세계에 모든 곳에 있는 네트워크와 애플리케이션 간의 연결을 모니터링할 수 있습니다.
* 북유럽
* 서유럽
* 스위스 북부
* 프랑스 중부
* 남아프리카 북부
* 캐나다 중부
* 미국 서부
* 미국 중서부
* 미국 중북부
* 미국 중남부
* 미국 중부
* 미국 동부
* 미국 동부 2
* 미국 서부 2
* 일본 동부
* 동남아시아
* 오스트레일리아 동남부
* 오스트레일리아 중부
* 오스트레일리아 동부
* 영국 남부
* 동아시아
* 한국 중부
* 인도 중부
* 미국 버지니아 주 정부
* 미국 정부 애리조나
* 중국 동부 2


ExpressRoute 모니터가 지원되는 지역 목록은 [설명서](../../expressroute/how-to-npm.md?utm_swu=8117)에 제공됩니다.


## <a name="set-up-and-configure"></a>설정 및 구성

> [!NOTE]
> Log Analytics 에이전트를 Microsoft Monitoring Agent(MMA) 또는 OMS Linux 에이전트라고도 합니다.

### <a name="install-and-configure-agents"></a>에이전트 설치 및 구성 

기본 프로세스를 사용 하 여 [Windows 컴퓨터 연결 Azure Monitor에](../platform/agent-windows.md) 에이전트를 설치 하 고 [Operations Manager Azure Monitor에 연결](../platform/om-agents.md)합니다.

### <a name="where-to-install-the-agents"></a>에이전트 설치 위치 

* **성능 모니터:** 다른 하위 네트워크에 대한 네트워크 연결을 모니터링하려는 각 하위 네트워크에 연결된 하나 이상의 노드에 Log Analytics 에이전트를 설치합니다.

    네트워크 링크를 모니터링하려면 해당 링크의 엔드포인트 양쪽 모두에 에이전트를 설치해야 합니다. 네트워크 토폴로지를 확실히 모를 경우 사이의 네트워크 성능을 모니터링하려는 중요 워크로드가 있는 서버에 에이전트를 설치합니다. 예를 들어, 웹 서버와 SQL을 실행하는 서버 간의 네트워크 연결을 모니터링하려는 경우 두 서버 모두에 에이전트를 설치합니다. 에이전트는 호스트 자체가 아닌 호스트 간의 네트워크 연결(링크)을 모니터링합니다. 

* **서비스 연결 모니터:** 서비스 엔드포인트에 대한 네트워크 연결을 모니터링하려는 각 노드에 Log Analytics 에이전트를 설치합니다. 예를 들어 O1, O2 및 O3 레이블이 지정된 사무실 사이트에서 Office 365에 대한 네트워크 연결을 모니터링하려는 경우 O1, O2 및 O3 각각에서 하나 이상의 노드에 Log Analytics 에이전트를 설치합니다. 

* **ExpressRoute 모니터**: Azure 가상 네트워크에 하나 이상의 Log Analytics 에이전트를 설치합니다. 또한 ExpressRoute 프라이빗 피어링을 통해 연결된 온-프레미스 서브네트워크에 에이전트를 하나 이상 설치합니다.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>모니터링을 위한 Log Analytics 에이전트 구성 

네트워크 성능 모니터는 가상 트랜잭션을 사용하여 원본과 대상 에이전트 사이의 네트워크 성능을 모니터링합니다. 성능 모니터 및 서비스 연결 모니터 기능에서는 TCP와 ICMP 중 하나를 모니터링 프로토콜로 선택할 수 있습니다. ExpressRoute 모니터의 경우, TCP만 모니터링 프로토콜로 사용할 수 있습니다. 방화벽을 통해 선택한 프로토콜에서 모니터링하는 데 사용되는 Log Analytics 에이전트 간의 통신이 허용되는지 확인합니다. 

* **TCP 프로토콜:** 모니터링을 위한 프로토콜로 TCP를 선택했으면 네트워크 성능 모니터 및 ExpressRoute 모니터에 사용되는 에이전트에서 방화벽 포트를 열어서 에이전트가 서로 연결할 수 있는지 확인합니다. 포트를 열려면 매개 변수 없이 PowerShell 창에서 관리자 권한으로 [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell 스크립트를 실행합니다.

    스크립트를 통해 솔루션에 필요한 레지스트리 키가 만들어집니다. 또한 에이전트가 서로 TCP 연결을 만들 수 있도록 Windows 방화벽 규칙이 만들어집니다. 스크립트로 만들어진 레지스트리 키는 디버그 로그와 로그 파일의 경로를 기록할지 여부를 지정합니다. 스크립트는 통신에 사용되는 에이전트 TCP 포트도 정의합니다. 이러한 키 값은 스크립트에 의해 자동으로 설정됩니다. 이 키는 수동으로 변경하지 마십시오. 기본적으로 열리는 포트는 8084입니다. 스크립트에 매개 변수 portNumber를 지정하여 사용자 지정 포트를 사용할 수 있습니다. 스크립트가 실행되는 모든 컴퓨터에서 동일한 포트를 사용하십시오. 

    >[!NOTE]
    > 스크립트는 Windows 방화벽만 로컬로 구성합니다. 네트워크 방화벽이 있는 경우 네트워크 성능 모니터에서 사용하는 TCP 포트를 대상으로 하는 트래픽을 허용하는지 확인해야 합니다.

    >[!NOTE]
    > 서비스 연결 모니터에 대 한 [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell 스크립트를 실행할 필요가 없습니다.

    

* **ICMP 프로토콜**: 모니터링을 위한 프로토콜로 ICMP를 선택한 경우 ICMP를 안정적으로 활용하기 위해 다음과 같은 방화벽 규칙을 사용하도록 설정합니다.
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>솔루션 구성 

1. [Azure 마켓플레이스](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)에서 작업 영역에 네트워크 성능 모니터 솔루션을 추가합니다. [솔루션 갤러리에서 Azure Monitor 솔루션 추가](./solutions.md)에 설명 된 프로세스를 사용할 수도 있습니다. 
2. Log Analytics 작업 영역을 열고 **개요** 타일을 선택합니다. 
3. 메시지 솔루션을 사용 하 여 **네트워크 성능 모니터** 타일을 선택 하 고 *추가 구성이 필요*합니다.

   ![네트워크 성능 모니터 타일](media/network-performance-monitor/npm-config.png)

4. **설치** 페이지의 **일반 설정** 보기에는 Log Analytics 에이전트를 설치하고 모니터링하도록 구성하는 옵션이 표시됩니다. 앞에서 설명한 대로 Log Analytics 에이전트를 설치하고 구성한 경우 **설정** 보기를 선택하여 사용하려는 기능을 구성합니다. 

   **성능 모니터**: **기본** 성능 모니터 규칙에서 가상 트랜잭션에 사용할 프로토콜을 선택하고 **저장하고 계속**을 클릭합니다. 이 프로토콜 선택은 시스템에서 생성된 기본 규칙에만 적용됩니다. 성능 모니터 규칙을 명시적으로 만들 때마다 프로토콜을 선택해야 합니다. 항상 **성능 모니터** 탭에서 **기본** 규칙 설정(당일 구성을 완료한 후 표시)으로 이동하고 나중에 프로토콜을 변경할 수 있습니다. 성능 모니터 기능이 필요하지 않은 경우 **성능 모니터** 탭의 **기본** 규칙 설정에서 기본 규칙을 사용하지 않도록 설정할 수 있습니다.

   ![성능 모니터 보기](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **서비스 연결 모니터**: 이 기능은 에이전트에서 Office 365 및 Dynamics 365로의 네트워크 연결을 모니터링하기 위해 미리 구성된 기본 제공 테스트를 제공합니다. 옆에 있는 확인란을 선택하여 모니터링할 Office 365 및 Dynamics 365 서비스를 선택합니다. 모니터링할 에이전트를 선택하려면 **에이전트 추가**를 선택합니다. 이 기능을 사용하지 않거나 나중에 설정하려는 경우 아무것도 선택하지 않은 상태에서 **저장하고 계속**을 선택합니다.

   ![서비스 연결 모니터 보기](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute 모니터**: **지금 검색** 단추를 선택하여 이 Log Analytics 작업 영역에 연결된 Azure 구독의 가상 네트워크에 연결되어 있는 모든 ExpressRoute 프라이빗 피어링을 검색합니다. 

   ![ExpressRoute 모니터 보기](media/network-performance-monitor/npm-express-route.png)

   검색이 완료 되 면 검색 된 회로 및 피어 링이 표에 나열 됩니다. 

   ![네트워크 성능 모니터 구성 페이지](media/network-performance-monitor/npm-private-peerings.png)
    
이러한 회로 및 피어 링에 대 한 모니터링은 처음에는 사용할 수 없는 상태입니다. 모니터링할 각 리소스를 선택 하 고 오른쪽의 자세히 보기에서 모니터링을 구성 합니다. **저장** 을 선택 하 여 구성을 저장 합니다. 자세히 알아보려면 ExpressRoute 모니터링 구성"을 참조하세요. 

설치가 완료된 후 데이터를 채우는 데 30분~1시간이 소요됩니다. 이 솔루션이 네트워크에서 데이터를 집계하는 동안 네트워크 성능 모니터 **개요** 타일에 *솔루션에 추가 구성이 필요합니다.* 라는 메시지가 표시됩니다. 데이터가 수집되고 인덱싱되면 **개요** 타일이 변경되고, 네트워크 상태를 요약으로 알려줍니다. 그런 다음, Log Analytics 에이전트가 설치된 노드 및 환경에서 검색된 서브넷에 대한 모니터링을 편집할 수 있습니다.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>서브넷 및 노드에 대한 모니터링 설정 편집 

하나 이상의 에이전트가 설치 된 모든 서브넷은 구성 페이지의 **서브 네트워크** 탭에 나열 됩니다. 


특정 서브네트워크의 모니터링을 사용하거나 사용하지 않도록 설정하려면:

1. **서브네트워크 ID** 옆에 있는 확인란을 선택하거나 선택 취소합니다. 그런 다음, 필요에 따라 **모니터링에 사용**이 선택되었거나 선택 취소되었는지 확인합니다. 여러 서브넷을 선택 또는 선택 취소할 수 있습니다. 이 설정을 사용하지 않도록 설정할 경우 서브네트워크가 모니터링되지 않고 다른 에이전트에 대한 Ping을 중지하도록 에이전트가 업데이트됩니다. 
2. 특정 서브네트워크에서 모니터링할 노드를 선택합니다. 목록에서 서브네트워크를 선택하고 모니터링되지 않는 노드와 모니터링되는 노드가 있는 목록 사이에서 필요한 노드를 이동합니다. 서브네트워크에 사용자 지정 설명을 추가할 수 있습니다.
3. **저장** 을 선택 하 여 구성을 저장 합니다. 

#### <a name="choose-nodes-to-monitor"></a>모니터링할 노드 선택

에이전트가 설치된 모든 노드는 **노드** 탭에 나열됩니다. 

1. 모니터링하거나 모니터링을 중지하려는 노드를 선택 또는 선택 취소합니다. 
2. 필요에 맞게 **모니터링에 사용**을 선택하거나 선택 취소합니다. 
3. **저장**을 선택합니다. 


원하는 기능 구성:

- [성능 모니터](network-performance-monitor-performance-monitor.md#configuration)
- [서비스 연결 모니터](network-performance-monitor-performance-monitor.md#configuration)
- [Express 경로 모니터](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>데이터 수집 세부 정보
손실 및 대기 시간 정보를 수집하기 위해 네트워크 성능 모니터는 TCP를 프로토콜로 선택하면 TCP SYN-SYNACK-ACK 핸드셰이크 패킷을 사용합니다. 네트워크 성능 모니터는 ICMP를 프로토콜로 선택하면 ICMP ECHO ICMP ECHO REPLY를 사용합니다. 토폴로지 정보를 가져오기 위해 경로 추적도 사용됩니다.

다음 표에는 데이터 수집 방법 및 네트워크 성능 모니터에 대한 데이터가 수집되는 방식에 대한 기타 세부 정보가 나와 있습니다.

| 플랫폼 | 직접 에이전트 | System Center Operations Manager 에이전트 | Azure Storage | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP는 5초마다 핸드셰이크/ICMP ECHO 메시지를 전송하며 3분마다 데이터가 전송됩니다. |
 

 
이 솔루션은 가상 트랜잭션을 사용하여 네트워크 상태를 평가합니다. 네트워크의 다양한 지점에 설치된 Log Analytics 에이전트는 TCP 패킷 또는 ICMP Echo를 서로 교환합니다. 에이전트가 TCP 패킷을 사용할지 또는 ICMP Echo를 사용할지는 모니터링을 위해 선택한 프로토콜에 따라 달라집니다. 이 과정에서 에이전트는 왕복 시간과 패킷 손실(있는 경우)을 확인합니다. 또한 각 에이전트는 다른 에이전트에 대해 정기적으로 추적 경로를 수행하여 네트워크에서 테스트가 필요한 다양한 경로를 모두 찾아냅니다. 에이전트는 이 데이터를 사용하여 네트워크 대기 시간 및 패킷 손실 수치를 추론할 수 있습니다. 테스트는 5초마다 반복됩니다. 데이터는 에이전트가 Azure Monitor의 Log Analytics 작업 영역에 업로드 되기 전에 약 3 분 동안 집계 됩니다.



>[!NOTE]
> 에이전트는 서로 자주 통신하지만 테스트를 수행하는 동안 상당한 네트워크 트래픽을 생성하지는 않습니다. 에이전트는 TCP SYN-SYNACK-ACK 핸드셰이크 패킷에만 의존하여 손실 및 대기 시간을 결정합니다. 데이터 패킷은 교환되지 않습니다. 이 과정에서 에이전트는 필요할 때만 서로 통신합니다. 에이전트 통신 토폴로지는 네트워크 트래픽을 줄이도록 최적화됩니다.

## <a name="use-the-solution"></a>솔루션 사용 

### <a name="network-performance-monitor-overview-tile"></a>네트워크 성능 모니터 개요 타일 

네트워크 성능 모니터 솔루션을 사용하도록 설정하면 **개요** 페이지의 솔루션 타일에 네트워크 상태에 대한 간략한 개요가 표시됩니다. 

 ![네트워크 성능 모니터 개요 타일](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>네트워크 성능 모니터 대시보드 

* **상위 네트워크 상태 이벤트**: 이 페이지에는 시스템의 최근 상태 이벤트 및 경고의 목록과 이벤트가 활성화된 이후 경과된 시간이 표시됩니다. 모니터링 규칙에 대해 선택한 메트릭(손실, 대기 시간, 응답 시간 또는 대역폭 사용률) 값이 임계값을 초과할 때마다 상태 이벤트 또는 경고가 생성됩니다. 

* **ExpressRoute 모니터**: 이 페이지에는 솔루션이 모니터링하는 다양한 ExpressRoute 피어링 연결에 대한 상태 요약이 제공됩니다. **토폴로지** 타일에는 네트워크에서 모니터링되는 ExpressRoute 회로를 통과하는 네트워크 경로 수가 표시됩니다. **토폴로지** 보기로 이동하려면 이 타일을 선택합니다.

* **서비스 연결 모니터**: 이 페이지에는 사용자가 만든 여러 테스트에 대한 상태 요약이 제공됩니다. **토폴로지** 타일에는 모니터링되는 엔드포인트 수가 표시됩니다. **토폴로지** 보기로 이동하려면 이 타일을 선택합니다.

* **성능 모니터**: 이 페이지에는 솔루션이 모니터링하는 **네트워크** 링크 및 **서브네트워크** 링크에 대한 상태 요약이 제공됩니다. **토폴로지** 타일에는 네트워크에서 모니터링되는 네트워크 경로 수가 표시됩니다. **토폴로지** 보기로 이동하려면 이 타일을 선택합니다. 

* **일반 쿼리**: 이 페이지에는 원시 네트워크 모니터링 데이터를 직접 가져오는 검색 쿼리 집합이 포함되어 있습니다. 이러한 쿼리를 시작점으로 사용하여 사용자 지정 보고를 위한 자체 쿼리를 만들 수 있습니다. 

   ![네트워크 성능 모니터 대시보드](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>상세하게 드릴다운 

솔루션 대시보드의 다양한 링크를 선택하여 관심 영역을 상세하게 드릴다운할 수 있습니다. 예를 들어 대시보드에 경고 또는 비정상 네트워크 링크가 나타날 경우 해당 링크를 선택하여 자세히 조사할 수 있습니다. 특정 네트워크 링크에 대한 모든 서브네트워크 링크가 페이지에 나열됩니다. 각 서브네트워크 링크의 손실, 대기 시간 및 상태를 볼 수 있습니다. 문제를 유발한 서브네트워크 링크를 빠르게 알아낼 수 있습니다. **노드 링크 보기** 를 선택 하 여 비정상 서브넷 링크의 모든 노드 링크를 볼 수 있습니다. 그런 다음 노드 간 개별 링크를 확인하고 비정상 노드 링크를 찾을 수 있습니다. 

원본 및 대상 노드 간의 경로에 대 한 홉 단위 토폴로지를 보려면 **토폴로지 보기** 를 선택 합니다. 비정상 경로는 빨간색으로 표시됩니다. 각 홉의 영향을 받은 대기 시간을 확인할 수 있으므로 네트워크의 특정 부분에서 문제를 빠르게 식별할 수 있습니다.

 

### <a name="network-state-recorder-control"></a>네트워크 상태 레코더 컨트롤

각 보기에는 특정 시점에서 네트워크 상태에 대한 스냅샷이 표시됩니다. 기본적으로 가장 최근 상태가 표시됩니다. 페이지 맨 위에 있는 막대에 상태가 표시되는 시점이 표시됩니다. 과거 시간 네트워크 상태의 스냅샷을 보려면 **작업**을 선택합니다. 또한 최신 상태를 보는 동안 페이지 자동 새로 고침을 사용 또는 사용하지 않도록 설정할 수도 있습니다. 

 ![네트워크 상태 레코더](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>추세 차트 

드릴다운하는 각 수준에서 적용 가능한 메트릭의 추세를 확인할 수 있습니다. 손실, 대기 시간, 응답 시간 또는 대역폭 사용량을 확인할 수 있습니다. 추세의 시간 간격을 변경하려면 차트 상단에 있는 시간 컨트롤을 사용합니다. 

추세 차트는 성능 메트릭의 성능에 대한 기존 관점을 보여줍니다. 일부 네트워크 문제는 본질적으로 일시적이며 네트워크의 현재 상태만 보고 파악하기 어렵습니다. 문제를 알아차리기 전에 빠르게 나타나고 사라졌다가 나중에 다시 나타날 수 있습니다. 이렇게 일시적인 문제는 애플리케이션 관리자에게도 어려울 수 있습니다. 이러한 문제로 인해 애플리케이션 구성 요소가 모두 원활하게 실행되는 경우에도 애플리케이션 응답 시간이 설명할 수 없게 증가되기도 합니다. 

추세 차트를 보면 이런 종류의 문제를 쉽게 파악할 수 있습니다. 이 문제는 네트워크 대기 시간이나 패킷 손실이 급증하는 것으로 나타납니다. 문제를 조사하려면 네트워크 상태 레코더 컨트롤을 사용하여 문제가 발생한 시점의 네트워크 스냅샷과 토폴로지를 확인합니다.

 
![추세 차트](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>토폴로지 맵 

네트워크 성능 모니터는 인터랙티브 토폴로지 맵에서 원본 및 대상 엔드포인트 사이의 홉 단위 경로 토폴로지를 보여줍니다. 토폴로지 맵을 보려면 솔루션 대시보드에서 **토폴로지**를 선택합니다. 드릴다운 페이지에서 **토폴로지 보기** 링크를 선택할 수도 있습니다. 

토폴로지 맵에는 원본 및 대상 간 경로 수와 데이터 패킷이 사용하는 경로가 표시됩니다. 각 네트워크 홉으로 인한 대기 시간도 표시됩니다. 전체 경로 대기 시간이 임계값(해당 모니터링 규칙에 설정)을 초과하는 모든 경로가 빨간색으로 표시됩니다. 

노드를 선택하거나 토폴로지 맵에서 노드 위로 마우스를 올리면 노드 속성(예: FQDN 및 IP 주소)이 표시됩니다. 홉의 IP 주소를 보려면 홉을 선택합니다. 문제가 있는 네트워크 홉은 해당 홉으로 인한 대기 시간을 통해 식별할 수 있습니다. 특정 경로를 필터링하려면 축소 가능한 작업 창에서 필터를 사용합니다. 네트워크 토폴로지를 간소화하려면 작업 창에 있는 슬라이더를 사용하여 중간 홉을 숨깁니다. 마우스 휠을 사용하여 토폴로지 맵을 확대하거나 축소할 수 있습니다. 

맵에 표시된 토폴로지는 계층 3 토폴로지이며 계층 2 디바이스와 연결은 포함되지 않습니다. 

 
![토폴로지 맵](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리

네트워크 성능 모니터 대시보드 및 드릴 다운 페이지를 통해 그래픽으로 노출 되는 모든 데이터는 [로그 쿼리에서](../log-query/log-query-overview.md)기본적으로 사용할 수 있습니다. 리포지토리의 데이터를 대화형으로 분석하고 여러 원본의 데이터를 상호 연결할 수 있습니다. 사용자 지정 경고 및 보기를 만들 수 있고 Excel, Power BI 또는 공유할 수 있는 링크로 데이터를 내보낼 수도 있습니다. 대시보드의 **일반 쿼리** 영역에는 고유한 쿼리 및 보고서를 만들기 위한 시작 지점으로 사용할 수 있는 몇 가지 유용한 쿼리가 있습니다. 

## <a name="alerts"></a>경고

네트워크 성능 모니터는 [Azure Monitor](../platform/alerts-overview.md)의 경고 기능을 사용합니다.

즉, 모든 알림은 [작업 그룹](../platform/action-groups.md)을 사용하여 관리됩니다.  

Log Analytics를 통해 경고를 만드는 NPM 사용자인 경우: 
1. Azure Portal로 리디렉션하는 링크가 표시 됩니다. 이 링크를 클릭하여 포털에 액세스합니다.
2. 네트워크 성능 모니터 솔루션 타일을 클릭합니다. 
3. [구성]으로 이동합니다.  
4. 경고를 만들려는 테스트를 선택하고 아래에 설명된 단계를 따릅니다.

Azure Portal를 통해 경고를 만드는 NPM 사용자 인 경우:  
1. 이메일을 직접 입력할 수도 있고 작업 그룹을 통해 경고를 만들 수도 있습니다.
2. 이메일을 직접 입력하기로 선택하면 **NPM Email ActionGroup**이라는 이름의 작업 그룹이 만들어지고 이 작업 그룹에 이메일 id가 추가됩니다.
3. 작업 그룹을 사용하기로 선택하면 이전에 만든 작업 그룹을 선택해야 합니다. 작업 그룹을 만드는 방법은 [여기](../platform/action-groups.md#create-an-action-group-by-using-the-azure-portal)서 배울 수 있습니다. 
4. 경고가 만들어지면 경고 관리를 사용하여 경고를 관리할 수 있습니다. 

경고를 만들 때마다 NPM은 Azure Monitor에 쿼리 기반 로그 경고 규칙을 작성합니다. 이 쿼리는 기본적으로 5 분 마다 트리거됩니다. Azure Monitor에서는 처음 생성된 250개의 로그 경고 규칙에 대해서는 요금을 부과하지 않고 250개 로그 경고 규칙 제한을 초과하는 모든 경고 규칙에 대해 [Azure Monitor 가격 책정 페이지의 경고 가격](https://azure.microsoft.com/pricing/details/monitor/)에 따라 요금을 부과합니다.
알림은 [Azure Monitor 가격 책정 페이지의 알림 가격](https://azure.microsoft.com/pricing/details/monitor/)에 따라 별도로 청구됩니다.


## <a name="pricing"></a>가격 책정

가격 책정에 대한 정보는 [온라인](network-performance-monitor-pricing-faq.md)으로 지원됩니다.

## <a name="provide-feedback"></a>피드백 제공 

* **UserVoice:** 개발되기를 바라는 네트워크 성능 모니터 기능에 대한 아이디어를 게시할 수 있습니다. [UserVoice 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring)를 방문하세요. 

* **코 호트에 참여 하세요.** 새 고객이 코 호트에 참여 하는 데 항상 관심이 있습니다. 그 일환으로 새로운 기능에 초기에 액세스할 수 있고 네트워크 성능 모니터를 개선하는 데 참여할 수 있는 기회가 제공됩니다. 참가 하는 데 관심이 있는 경우이 [빠른 설문 조사](https://aka.ms/npmcohort)를 작성 합니다. 

## <a name="next-steps"></a>다음 단계 
[성능 모니터](network-performance-monitor-performance-monitor.md), [서비스 연결 모니터](network-performance-monitor-performance-monitor.md) 및 [ExpressRoute 모니터](network-performance-monitor-expressroute.md)에 대해 자세히 알아봅니다. 

