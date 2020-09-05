---
title: Azure Firewall 강제 터널링
description: 추가 처리를 위해 인터넷 바인딩된 트래픽을 추가 방화벽 또는 네트워크 가상 어플라이언스로 라우팅하도록 강제 터널링을 구성할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612601"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure Firewall 강제 터널링

새 Azure Firewall을 구성하는 경우 인터넷으로 직접 이동하는 대신 인터넷에 바인딩된 모든 트래픽을 지정된 다음 홉으로 라우팅할 수 있습니다. 예를 들어 온-프레미스 에지 방화벽이나 기타 NVA(네트워크 가상 어플라이언스)를 통해 네트워크 트래픽을 인터넷에 전달하기 전에 처리할 수 있습니다. 그러나 강제 터널링을 위해 기존 방화벽을 구성할 수는 없습니다.

기본적으로 강제 터널링은 Azure Firewall에서 모든 아웃바운드 Azure 종속성이 충족되도록 허용되지 않습니다. 인터넷으로 직접 이동하지 않는 기본 경로가 있는 *AzureFirewallSubnet*에 대한 UDR(사용자 정의 경로) 구성이 비활성화되어 있습니다.

## <a name="forced-tunneling-configuration"></a>강제 터널링 구성

강제 터널링을 지원하기 위해 서비스 관리 트래픽은 고객 트래픽과 구분됩니다. 연결된 공용 IP 주소에는 *AzureFirewallManagementSubnet*(최소 서브넷 크기/26)이라는 추가 전용 서브넷이 필요합니다. 이 서브넷에서 허용되는 유일한 경로는 인터넷에 대한 기본 경로이며, BGP 경로 전파를 사용하지 않도록 설정해야 합니다.

BGP를 통해 온-프레미스에 대 한 트래픽을 강제로 수행 하는 기본 경로를 알린 경우 방화벽을 배포 하기 전에 *AzureFirewallSubnet* 및 *AzureFirewallManagementSubnet* 를 만들고 기본 경로를 사용 하 여 udr을 사용 하도록 설정 하 고 **게이트웨이 경로** 를 사용 하지 않도록 설정 해야 합니다.

이 구성에서 *AzureFirewallSubnet* 는 이제 온-프레미스 방화벽 또는 nva에 대 한 경로를 포함 하 여 인터넷에 전달 되기 전에 트래픽을 처리할 수 있습니다. 이 서브넷에서 **게이트웨이 경로 전파** 를 사용 하는 경우 BGP를 통해 이러한 경로를 게시할 수도 있습니다 *AzureFirewallSubnet* .

예를 들어 온-프레미스 장치에 대 한 다음 홉으로 VPN gateway를 사용 하 여 *AzureFirewallSubnet* 에서 기본 경로를 만들 수 있습니다. 또는 **전파 게이트웨이 경로** 를 사용 하도록 설정 하 여 온-프레미스 네트워크에 대 한 적절 한 경로를 가져올 수 있습니다.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="가상 네트워크 게이트웨이 경로 전파":::

강제 터널링을 사용 하도록 설정 하는 경우 인터넷에 바인딩된 트래픽은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 연결 되어 온-프레미스 방화벽에서 원본을 숨깁니다.

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 그러나 공용 IP 주소 범위를 SNAT **하지 않도록** Azure 방화벽을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

강제 터널링을 지원하도록 Azure Firewall을 구성하면 구성을 실행 취소할 수 없습니다. 방화벽에서 다른 모든 IP 구성을 제거하면 관리 IP 구성도 제거되고 방화벽의 할당이 취소됩니다. 관리 IP 구성에 할당된 공용 IP 주소를 제거할 수 없지만 다른 공용 IP 주소를 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성](tutorial-hybrid-portal.md)
