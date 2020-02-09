---
title: 공용 클라우드에 Azure 연결 | Microsoft Docs
description: 다른 공용 클라우드에 Azure를 연결 하는 다양 한 방법을 설명 합니다.
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889638"
---
# <a name="connecting-azure-with-public-clouds"></a>공용 클라우드와 Azure 연결

많은 기업은 비즈니스 및 기술 목표 때문에 다중 클라우드 전략을 추구 하 고 있습니다. 여기에는 비용, 유연성, 기능 가용성, 중복성, 데이터 주권 등이 포함 됩니다. 이 전략을 사용 하면 두 클라우드의 성능을 모두 활용할 수 있습니다. 

이 방법을 사용 하면 네트워크 및 응용 프로그램 아키텍처 측면에서 기업의 문제도 발생 합니다. 이러한 문제 중 일부는 대기 시간 및 데이터 처리량입니다. 이러한 문제를 해결 하기 위해 고객은 여러 클라우드에 직접 연결 하려고 합니다. 일부 서비스 공급자는 고객에 대해 여러 클라우드 공급자를 연결 하는 솔루션을 제공 합니다. 다른 경우에는 고객이 자신의 라우터를 배포 하 여 여러 공용 클라우드를 연결할 수 있습니다.
## <a name="connectivity-via-expressroute"></a>Express 경로를 통한 연결
Express 경로를 통해 고객은 연결 공급자가 쉽게 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. Express 경로를 사용 하 여 고객은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

Express 경로를 통해 연결 하는 방법에는 세 가지가 있습니다.

1. 3 공급자
2. Layer2 공급자
3. 직접 연결

### <a name="layer3-provider"></a>3 공급자

3 공급자는 일반적으로 IP VPN 또는 MPLS VPN 공급자 라고 합니다. 고객은 데이터 센터, 분기 및 클라우드 간의 multipoint 연결에 이러한 공급자를 활용 합니다. 고객은 BGP 또는 정적 기본 경로를 통해 L3 공급자에 연결 합니다. 서비스 공급자는 고객 사이트, 데이터 센터 및 공용 클라우드 간에 경로를 보급 합니다. 
 
3 공급자를 통해 연결 하는 경우 Microsoft는 BGP를 통해 고객 VNET 경로를 서비스 공급자에 게 알립니다. 공급자에는 두 가지 구현이 있을 수 있습니다.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

모든 클라우드 공급자의 트래픽이 고객 라우터를 통해 전달 되는 경우 공급자는 별도의 VRF에 각 클라우드 공급자를 연결할 수 있습니다. 고객이 서비스 공급자를 사용 하 여 BGP를 실행 하는 경우 이러한 경로는 기본적으로 다른 클라우드 공급자에 게 다시 보급 됩니다. 

서비스 공급자가 동일한 VRF의 모든 클라우드 공급자에 연결한 경우 경로는 서비스 공급자의 다른 클라우드 공급자에 게 직접 보급 됩니다. 이는 eBGP 경로가 기본적으로 다른 eBGP 환경에 보급 되는 표준 BGP 작업 이라고 가정 합니다.

각 공용 클라우드의 접두사 제한은 서로 다르므로 경로 서비스 공급자를 배포 하는 데는 경로를 배포할 때 주의 해야 합니다.

### <a name="layer2-provider-and-direct-connection"></a>Layer2 공급자 및 직접 연결

두 모델의 실제 연결은 다르지만 3 BGP는 MSEE와 customer 라우터 사이에서 직접 설정 됩니다. Express 경로 직접 고객이 MSEE에 직접 연결 합니다. Layer2의 경우 서비스 공급자는 VLAN을 고객 프레미스에서 클라우드로 확장 합니다. 고객은 layer2 네트워크를 기반으로 BGP를 실행 하 여 Dc를 클라우드에 연결 합니다.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
두 경우 모두, 고객은 각 공용 클라우드에 대한 지점 및 지점 간 연결을 갖습니다. 고객은 각 공용 클라우드에 대한 별도의 BGP 연결을 설정 합니다. 클라우드 공급자 하나에서 받은 경로는 기본적으로 다른 클라우드 공급자에 게 알려집니다. 각 클라우드 공급자는 서로 다른 접두사 제한을 사용 하므로 경로를 보급 하는 동안 고객은 이러한 제한을 처리 해야 합니다. 고객은 다른 공용 클라우드에서 경로를 보급 하는 동안 Microsoft에서 일반적인 BGP 노브를 사용할 수 있습니다.

## <a name="direct-connection-with-expressroute"></a>Express 경로를 사용 하 여 직접 연결

고객은 직접 Express 경로를 클라우드 공급자의 직접 연결 제공에 연결 하도록 선택할 수 있습니다. 두 클라우드 공급자가 다시 백로 연결 되 고, BGP는 라우터 간에 직접 설정 됩니다. 이 유형의 연결은 현재 Oracle에서 사용할 수 있습니다.

## <a name="site-to-site-vpn"></a>사이트 간 VPN

고객은 인터넷을 활용 하 여 Azure의 인스턴스를 다른 공용 클라우드와 연결할 수 있습니다. 거의 모든 클라우드 공급자는 사이트 간 VPN 기능을 제공 합니다. 그러나 특정 변형이 부족 하 여 비 호환성 문제가 발생할 수 있습니다. 예를 들어 일부 클라우드 공급자는 IKEv1만 지원 하므로 해당 클라우드에서 VPN 종료 엔드포인트이 필요 합니다. IKEv2를 지 원하는 클라우드 공급자의 경우 두 클라우드 공급자의 VPN gateway 간에 직접 터널을 설정할 수 있습니다.

사이트 간 VPN은 높은 처리량과 짧은 대기 시간 솔루션으로 간주 되지 않습니다. 그러나 실제 연결에 대한 백업으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Express 경로 및 가상 네트워크 연결에 대한 추가 질문은 [express 경로 FAQ][ER-FAQ] 를 참조 하세요.

Azure와 Oracle 간 연결에 대한 [azure와 Oracle 클라우드 간의 직접 연결 설정][ER-OCI] 을 참조 하세요.

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



