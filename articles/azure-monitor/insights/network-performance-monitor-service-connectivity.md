---
title: Azure Log Analytics의 네트워크 성능 모니터 솔루션 | Microsoft Docs
description: 네트워크 성능 모니터에서 서비스 연결 모니터 기능을 사용하여 열린 TCP 포트가 있는 모든 엔드포인트에 대한 네트워크 연결을 모니터링합니다.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 0c4b8d1646ba851acc6a0e2d9a3b920634098846
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119338"
---
# <a name="service-connectivity-monitor"></a>서비스 연결 모니터

[네트워크 성능 모니터](network-performance-monitor.md)에서 서비스 연결 모니터 기능을 사용하여 열린 TCP 포트가 있는 모든 엔드포인트에 대한 네트워크 연결을 모니터링할 수 있습니다. 이러한 엔드포인트에는 웹 사이트, SaaS 애플리케이션, PaaS 애플리케이션 및 SQL Database가 포함됩니다. 

서비스 연결 모니터를 사용하여 다음 기능을 수행할 수 있습니다. 

- 여러 지사 또는 위치에서 애플리케이션 및 네트워크 서비스에 대한 네트워크 연결을 모니터링합니다. 응용 프로그램 및 네트워크 서비스에는 Office 365, Dynamics CRM, 내부 LOB(기간 업무) 애플리케이션 및 SQL 데이터베이스가 포함됩니다.
- 기본 제공 테스트를 사용하여 Office 365 및 Dynamics 365 엔드포인트에 대한 네트워크 연결을 모니터링합니다. 
- 엔드포인트에 연결할 때 발생하는 응답 시간, 네트워크 대기 시간, 패킷 손실을 확인합니다.
- 애플리케이션 성능 저하가 네트워크 때문인지 또는 애플리케이션 공급자 끝의 문제 때문인지 확인합니다.
- 토폴로지 맵의 각 홉으로 인한 대기 시간을 확인하여 애플리케이션 성능 저하를 일으킬 수 있는 네트워크의 핫스폿 식별입니다.


![서비스 연결 모니터](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>구성 
네트워크 성능 모니터에 대한 구성을 열려면 [네트워크 성능 모니터 솔루션](network-performance-monitor.md)을 열고 **구성**을 선택합니다.

![네트워크 성능 모니터 구성](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>모니터링을 위한 Log Analytics 에이전트 구성
솔루션이 서비스 엔드포인트에 대한 노드에서 토폴로지를 검색할 수 있도록 모니터링에 사용되는 노드에서 다음과 같은 방화벽 규칙을 사용하도록 설정합니다. 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>서비스 연결 모니터 테스트 만들기 

서비스 엔드포인트에 대한 네트워크 연결을 모니터링하기 위한 테스트를 만들기 시작합니다.

1. **서비스 연결 모니터** 탭을 선택합니다.
2. **테스트 추가**를 선택하고 테스트 이름 및 설명을 입력합니다. 
3. 테스트 유형을 선택합니다.<br>

    * **웹**을 선택하여 HTTP/S 요청에 응답하는 서비스(예: outlook.office365.com 또는 bing.com)에 대한 연결을 모니터링합니다.<br>
    * **네트워크**를 선택하여 TCP 요청에 응답하지만 HTTP/S 요청에 응답하지 않는 서비스(예: SQL Server, FTP 서버 Ehsms SSH 포트)에 대한 연결을 모니터링합니다. 
4. 네트워크 대기 시간, 패킷 손실 및 토폴로지 검색과 같은 네트워크 측정을 수행하지 않으려는 경우 **네트워크 측정 수행** 확인란의 선택을 취소합니다. 이 기능을 최대한 활용하기 위해 이 옵션을 선택해 둡니다. 
5. **대상**에서 네트워크 연결을 모니터링하려는 URL/FQDN/IP 주소를 입력합니다.
6. **포트 번호**에서 대상 서비스의 포트 번호를 입력합니다. 
7. **테스트 빈도**에서 테스트를 실행하려는 빈도에 대한 값을 입력합니다. 
8. 서비스에 대한 네트워크 연결을 모니터링하려는 노드를 선택합니다. 테스트 당 추가 에이전트의 개수 보다 작거나 150 인지 확인 합니다. 에이전트는 최대 150 끝점/에이전트를 테스트할 수 있습니다.

    >[!NOTE]
    > Windows 서버 기반 노드의 경우 이 기능은 TCP 기반 요청을 사용하여 네트워크 측정을 수행합니다. Windows 클라이언트 기반 노드의 경우 이 기능은 ICMP 기반 요청을 사용하여 네트워크 측정을 수행합니다. 경우에 따라 노드가 Windows 클라이언트에 기반하는 경우 대상 애플리케이션은 들어오는 ICMP 기반 요청을 차단합니다. 솔루션은 네트워크 측정을 수행할 수 없습니다. 이러한 경우에 Windows Server 기반 노드를 사용하는 것이 좋습니다. 

9. 선택한 항목에 대해 상태 이벤트를 만들지 않으려는 경우 **이 테스트에서 검사되는 대상에서 상태 모니터링 사용**의 선택을 취소합니다. 
10. 모니터링 조건을 선택합니다. 임계값을 입력하여 상태 이벤트 생성에 대한 사용자 지정 임계값을 설정할 수 있습니다. 조건 값이 선택한 네트워크 또는 서브네트워크 쌍에 선택된 임계값을 초과할 경우 상태 이벤트가 생성됩니다. 
11. **저장** 을 선택하여 구성을 저장합니다. 

    ![서비스 연결 모니터 테스트 구성](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>연습 

네트워크 성능 모니터 대시보드 보기로 이동합니다. 만든 다른 테스트의 상태 요약을 가져오려면 **서비스 연결 모니터** 페이지를 확인합니다. 

![서비스 연결 모니터 페이지](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

타일을 선택하여 **테스트** 페이지에서 테스트의 세부 정보를 봅니다. 왼쪽 표에서는 모든 테스트에 대한 지정 시간 상태와 서비스 응답 시간, 네트워크 대기 시간 및 패킷 손실 값을 볼 수 있습니다. 네트워크 상태 레코더 컨트롤을 사용하여 이전의 다른 시간에 대한 네트워크 스냅숏을 볼 수 있습니다. 조사하려는 표에서 테스트를 선택합니다. 오른쪽 창의 차트에서 손실, 대기 시간 및 응답 시간 값의 기록 추세를 볼 수 있습니다. 각 노드의 성능을 확인하려면 **테스트 세부 정보** 링크를 선택합니다.

![서비스 연결 모니터 테스트](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

**테스트 노드** 보기에서 각 노드의 네트워크 연결을 확인할 수 있습니다. 성능이 저하된 노드를 선택합니다. 애플리케이션이 느리게 실행되는 것으로 확인된 노드입니다.

애플리케이션의 응답 시간과 네트워크 대기 시간 간의 상관 관계를 관찰하여 애플리케이션 성능 저하가 네트워크 때문인지 아니면 애플리케이션 공급자 끝의 문제 때문인지 확인합니다. 

* **애플리케이션 문제:** 응답 시간이 갑자기 증가하지만 네트워크 대기 시간이 일정한 경우 네트워크는 제대로 작동하고 있으며 애플리케이션 끝에 문제가 있는 것을 의미합니다. 

    ![서비스 연결 모니터 애플리케이션 문제](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **네트워크 문제:** 응답 시간이 해당하는 네트워크 대기 시간과 더불어 갑자기 증가하는 경우 네트워크 대기 시간의 증가로 인해 응답 시간이 증가하는 것을 의미합니다. 

    ![서비스 연결 모니터 네트워크 문제](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

네트워크로 인한 문제인지 확인한 후에 **토폴로지** 보기 링크를 선택하여 토폴로지 맵에서 문제가 있는 홉을 식별합니다. 예제가 다음 이미지에 표시됩니다. 노드와 애플리케이션 엔드포인트 사이의 총 105ms 대기 시간 중에서 96ms가 빨간색으로 표시된 홉 때문입니다. 문제가 있는 홉을 식별한 후에 정정 작업을 수행할 수 있습니다. 

![서비스 연결 모니터 테스트](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>진단 

비정상 상태를 확인한 경우 다음 단계를 수행합니다.

* 서비스 응답 시간, 네트워크 손실 및 대기 시간이 NA로 표시되면 다음 이유 중 하나 이상 때문일 수 있습니다.

    - 애플리케이션이 다운되었습니다.
    - 서비스에 대한 네트워크 연결을 확인하는 데 사용되는 노드가 중단되었습니다.
    - 테스트 구성에 입력한 대상이 올바르지 않습니다.
    - 노드는 네트워크에 연결되지 않습니다.

* 서비스 응답 시간은 올바른 것으로 나타나지만 네트워크 손실 및 대기 시간이 NA로 표시되면 다음 이유 중 하나 이상 때문일 수 있습니다.

    - 서비스에 대한 네트워크 연결을 확인하는 데 사용되는 노드가 Windows 클라이언트 컴퓨터인 경우 대상 서비스가 ICMP 요청을 차단하거나 네트워크 방화벽이 노드에서 시작되는 ICMP 요청을 차단하게 됩니다.
    - **네트워크 측정 수행** 확인란이 테스트 구성에서 비어 있습니다. 

* 서비스 응답 시간은 NA이지만 네트워크 손실 및 대기 시간은 유효한 경우 대상 서비스가 웹 애플리케이션이 아닐 수 있습니다. 테스트 구성을 편집하고, 테스트 형식을 **웹**이 아닌 **네트워크**로 선택합니다. 

* 애플리케이션이 느리게 실행되는 경우 애플리케이션 성능 저하가 네트워크 때문인지 또는 애플리케이션 공급자 끝의 문제 때문인지 확인합니다.


## <a name="next-steps"></a>다음 단계
자세한 네트워크 성능 데이터 레코드를 보려면 [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 수행합니다.
