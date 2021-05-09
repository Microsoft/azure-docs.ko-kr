---
title: Azure 표준 Load Balancer에 Azure Firewall 통합
description: Azure 표준 Load Balancer(공용 또는 내부)를 사용하여 가상 네트워크에 Azure Firewall을 통합할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505907"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Azure 표준 Load Balancer에 Azure Firewall 통합

Azure 표준 Load Balancer(공용 또는 내부)를 사용하여 가상 네트워크에 Azure Firewall을 통합할 수 있습니다. 

기본 설정 디자인은 Azure Firewall과 내부 부하 분산 장치를 통합하는 것입니다 .이는 훨씬 간단한 디자인이기 때문입니다. 이미 배포된 공용 부하 분산 장치를 보유하고 있고, 그 상태로 유지하려는 경우 공용 부하 분산 장치를 사용할 수 있습니다. 그러나 공용 부하 분산 장치 시나리오에서 기능에 영향을 줄 수 있는 비대칭 라우팅 문제에 주의해야 합니다.

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](../load-balancer/load-balancer-overview.md)를 참조하세요.

## <a name="public-load-balancer"></a>공용 부하 분산 장치

공용 부하 분산 장치를 사용할 경우 부하 분산 장치는 공용 프런트 엔드 IP 주소를 사용하여 배포됩니다.

### <a name="asymmetric-routing"></a>비대칭 라우팅

비대칭 라우팅은 패킷이 대상에 대한 하나의 경로를 사용하고, 원본으로 돌아갈 때는 다른 경로를 사용하는 문제입니다. 이 문제는 서브넷에 방화벽의 개인 IP 주소로 가는 기본 경로가 있을 때 사용자가 공용 부하 분산 장치를 사용하는 경우에 발생합니다. 이 경우 들어오는 부하 분산 장치 트래픽은 해당 공용 IP 주소를 통해 수신되지만 반환 경로는 방화벽의 개인 IP 주소를 거칩니다. 방화벽은 상태를 저장하고 이러한 연결된 세션을 인식하지 않으므로 반환 패킷을 삭제합니다.

### <a name="fix-the-routing-issue"></a>라우팅 문제 해결

Azure Firewall을 서브넷에 배포할 때 한 가지 단계는 AzureFirewallSubnet에 있는 방화벽의 개인 IP 주소를 통해 패킷을 전달하는 서브넷의 기본 경로를 만드는 것입니다. 자세한 내용은 [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md#create-a-default-route)을 참조하세요.

부하 분산 장치 시나리오에 방화벽을 도입하는 경우 인터넷 트래픽이 방화벽의 공용 IP 주소를 통해 들어오도록 할 수 있습니다. 여기에서 방화벽은 해당 방화벽 규칙을 적용하고, 패킷을 부하 분산 장치의 공용 IP 주소로 NAT합니다. 여기서 문제가 발생합니다. 패킷은 방화벽의 공용 IP 주소에 도착하지만 개인 IP 주소(기본 경로 사용)를 통해 방화벽으로 돌아갑니다.
이 문제를 방지하려면 방화벽의 공용 IP 주소에 대해 추가 호스트 경로를 만듭니다. 방화벽의 공용 IP 주소로 이동하는 패킷은 인터넷을 통해 라우팅됩니다. 이 경우 방화벽의 개인 IP 주소로의 기본 경로가 사용되지 않게 됩니다.

![비대칭 라우팅](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>경로 테이블 예

예를 들어, 다음 경로는 공용 IP 주소 20.185.97.136 및 개인 IP 주소 10.0.1.4에서 방화벽용으로 사용됩니다.

> [!div class="mx-imgBorder"]
> ![경로 테이블](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT 규칙 예

다음 예에서 NAT 규칙은 RDP 트래픽을 20.185.97.136의 방화벽으로 변환하고 20.42.98.220의 부하 분산 장치로 변환합니다.

> [!div class="mx-imgBorder"]
> ![NAT 규칙](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>상태 프로브

TCP 상태 프로브를 포트 80에 사용하거나 HTTP/HTTPS 프로브를 사용하는 경우 부하 분산 장치 풀의 호스트에서 웹 서비스를 실행해야 합니다.

## <a name="internal-load-balancer"></a>내부 부하 분산 장치

내부 부하 분산 장치를 사용할 경우 부하 분산 장치는 프라이빗 프런트 엔드 IP 주소를 사용하여 배포됩니다.

이 시나리오에서는 비대칭 라우팅 문제가 없습니다. 수신 패킷은 방화벽의 공용 IP 주소에 도착하고, 부하 분산 장치의 개인 IP 주소로 변환된 후 같은 반환 경로를 사용하여 방화벽의 개인 IP 주소로 돌아갑니다.

따라서 공용 부하 분산 장치 시나리오와 비슷하게 이 시나리오를 배포할 수 있지만 방화벽 공용 IP 주소 호스트 경로는 필요하지 않습니다.

백 엔드 풀의 가상 머신은 Azure Firewall을 통해 아웃바운드 인터넷 연결을 가질 수 있습니다. 방화벽을 다음 홉으로 사용하여 가상 머신의 서브넷에 사용자 정의 경로를 구성합니다.


## <a name="additional-security"></a>추가 보안

부하 분산 시나리오의 보안을 강화하려면 NSG(네트워크 보안 그룹)를 사용할 수 있습니다.

예를 들어, 부하 분산된 가상 머신이 있는 백 엔드 서브넷에 NSG를 만들 수 있습니다. 방화벽 IP 주소/포트에서 발생하는 수신 트래픽을 허용합니다.

![네트워크 보안 그룹](media/integrate-lb/nsg-01.png)

NSG에 대한 자세한 내용은 [보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.