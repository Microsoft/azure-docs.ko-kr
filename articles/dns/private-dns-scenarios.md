---
title: Azure DNS Private Zones 시나리오
description: Azure DNS Private Zones를 사용하기 위한 일반적인 시나리오 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 409595febded7b242eae876ebb2cb35ae4999e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686860"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones 시나리오
Azure DNS Private Zones는 가상 네트워크 간뿐 아니라 가상 네트워크 내에서의 이름 확인을 제공합니다. 이 문서에서는 이 기능을 사용하여 실현할 수 있는 몇 가지 일반적인 시나리오를 살펴봅니다. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>시나리오: 단일 가상 네트워크에 범위가 지정 된 이름 확인
이 시나리오에서는 VM(가상 머신)을 포함한 내부에 다수의 Azure 리소스가 있는 Azure의 가상 네트워크가 있습니다. 특정 도메인 이름(DNS 영역)을 통해 가상 네트워크 내에서 리소스를 확인하고자 하며, 이름 확인은 개인적이고 인터넷에서 액세스할 수 없어야 합니다. 또한 VNET 내의 VM의 경우 Azure를 DNS 영역에 자동으로 등록해야 합니다. 

이 시나리오는 아래에 표시됩니다. 이름이 “A”인 가상 네트워크에는 2개의 VM(VNETA-VM1 및 VNETA-VM2)이 포함되어 있습니다. 이러한 VM에는 각각 연결된 개인 IP가 있습니다. contoso.com이라는 개인 영역을 만들고 이 가상 네트워크를 등록 가상 네트워크로 연결하고 나면, 설명된 것처럼 Azure DNS에서 자동으로 영역에 두 개의 A 레코드를 만들게 됩니다. 이제 VNETA-VM2.contoso.com를 확인하기 위한 VNETA-VM1의 DNS 쿼리가 VNETA-VM2의 개인 IP를 포함하는 DNS 응답을 받게 됩니다. 또한 VNETA-VM2에서 발행한 VNETA-VM1(10.0.0.1)의 개인 IP에 대한 역방향 DNS 쿼리(PTR)는 예상 대로 VNETA-VM1의 이름을 포함하는 DNS 응답을 받게 됩니다. 

![단일 가상 네트워크 확인](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>시나리오: 가상 네트워크 간에 이름 확인

이 시나리오는 여러 가상 네트워크를 사용하여 개인 영역에 연결해야 하는 더 일반적인 사례입니다. 이 시나리오는 중앙 허브 가상 네트워크가 여러 다른 스포크 가상 네트워크가 연결되어 있는 허브 및 스포크 모델과 같은 아키텍처에 적합할 수 있습니다. 중앙 허브 가상 네트워크는 개인 영역에 등록 가상 네트워크로 연결될 수 있으며, 스포크 가상 네트워크는 확인 가상 네트워크로 연결될 수 있습니다. 

다음 다이어그램에는 A와 B라는 두 개의 가상 네트워크만 있는 이 시나리오의 간단한 버전이 나와 있습니다. A는 등록 가상 네트워크로 지정되고 B는 확인 가상 네트워크로 지정됩니다. 의도는 두 가상 네트워크가 모두 공용 영역 contoso.com을 공유하도록 하는 것입니다. 영역이 만들어지고 확인 및 등록 가상 네트워크가 영역에 연결되면, Azure는 가상 네트워크 A의 VM(VNETA-VM1 및 VNETA-VM2)에 대한 DNS 레코드를 자동으로 등록합니다. DNS 레코드를 확인 가상 네트워크 B의 VM에 대한 영역에 수동으로 추가할 수도 있습니다. 이 설정을 통해 다음과 같은 정방향 및 역방향 DNS 쿼리의 동작을 확인할 수 있습니다.
* 확인 가상 네트워크에서 VNETB-VM1의 DNS 쿼리는(VNETA-VM1.contoso.com의 경우) VNETA-VM1의 개인 IP를 포함하는 DNS 응답을 받게 됩니다.
* 확인 가상 네트워크 B에서 VNETB-VM2의 역방향 DNS(PTR) 쿼리는(10.1.0.1의 경우) FQDN VNETB-VM1.contoso.com을 포함하는 DNS 응답을 받게 됩니다. 이유는 역방향 DNS 쿼리의 범위가 동일한 가상 네트워크로 지정되어 있기 때문입니다. 
* 확인 네트워크 B에서 VNETB-VM3의 역방향 DNS(PTR) 쿼리는(10.0.0.1의 경우) NXDOMAIN을 받게 됩니다. 이유는 역방향 DNS 쿼리의 범위가 동일한 가상 네트워크로만 지정되어 있기 때문입니다. 


![여러 가상 네트워크 확인](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>시나리오: 분할-수평 기능

이 시나리오에서 사용 사례를 통해 동일한 DNS 영역에 대해 클라이언트가 배치된 위치(Azure 내에서 또는 인터넷에서)에 따라 다른 DNS 확인 동작을 수행할 수 있습니다. 예를 들어 기능 또는 동작이 다른 애플리케이션의 개인 및 공용 버전이 있는데, 두 버전 모두에 동일한 도메인 이름을 사용하고자 할 수 있습니다. 이 시나리오는 Azure DNS로 실현할 수 있습니다. 동일한 이름을 사용하여 개인 영역과 공용 DNS 영역을 만들면 됩니다.

다음 다이어그램에 이 시나리오가 설명되어 있습니다. 개인 IP와 공용 IP가 모두 할당되었으며 두 VM(VNETA-VM1 및 VNETA-VM2)이 있는 가상 네트워크 A가 있습니다. contoso.com이라는 공용 DNS 영역을 만들고 영역 내에서 DNS 레코드로 이러한 VM에 대한 공용 IP를 등록합니다. 또한 A를 등록 가상 네트워크로 지정하는 contoso.com이라고도 하는 사설 DNS 영역을 만들 수 있습니다. Azure는 자동으로 자신의 개인 IP를 가리키는 개인 영역에 VM을 A 레코드로 등록합니다.

이제 인터넷 클라이언트가 VNETA-VM1.contoso.com을 조회할 DNS 쿼리를 발급하면 Azure는 자동으로 공용 영역에서 공용 IP 레코드를 반환합니다. 다른 VM에서 동일한 DNS 쿼리가 실행 된 경우 (예: VNETA-VM2) 동일한 가상 네트워크에는 Azure는 개인 IP 레코드를 반환 개인 영역에서 합니다. 

![분할 Brian 확인](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>다음 단계
사설 DNS 영역에 대해 자세히 알아보려면 [사설 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요.

Azure DNS에서 [사설 DNS 영역을 만드는](./private-dns-getstarted-powershell.md) 방법을 알아봅니다.

에 방문 하 여 DNS 영역 및 레코드에 대해 알아봅니다. [DNS 영역 및 레코드 개요](dns-zones-records.md)합니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

