---
title: 'Azure Express 경로: 암호화 정보'
description: Express 경로 암호화에 대해 알아봅니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: 7dd9106539b6756d74629ac663241a5b5562cefb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437053"
---
# <a name="expressroute-encryption"></a>Express 경로 암호화
 
Express 경로는 네트워크와 Microsoft 네트워크 사이에서 데이터를 이동 하는 기밀성과 무결성을 보장 하기 위해 몇 가지 암호화 기술을 지원 합니다.

## <a name="point-to-point-encryption-by-macsec-faq"></a>MACsec 별 지점 및 지점 간 암호화 FAQ
MACsec는 [IEEE 표준](https://1.ieee802.org/security/802-1ae/)입니다. MAC (미디어 액세스 제어) 수준 또는 네트워크 계층 2에서 데이터를 암호화 합니다. [Express 경로 직접](expressroute-erdirect-about.md)를 통해 microsoft에 연결 하는 경우 macsec를 사용 하 여 네트워크 장치와 microsoft 네트워크 장치 간의 실제 링크를 암호화할 수 있습니다. MACsec은 기본적으로 Express 경로 직접 포트에서 사용 하지 않도록 설정 됩니다. 암호화에 대한 사용자 고유의 MACsec 키를 가져와서 [Azure Key Vault](../key-vault/key-vault-overview.md)에 저장 합니다. 키를 회전 하는 시기를 결정 합니다. 아래 Faq를 참조 하세요.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Express 경로 공급자가 프로 비전 하는 Express 경로 회로에서 MACsec를 사용 하도록 설정할 수 있나요?
아닙니다. MACsec는 단일 엔터티 (예: 고객)가 소유한 키를 사용 하 여 실제 링크의 모든 트래픽을 암호화 합니다. 따라서 Express 경로 직접 에서만 사용할 수 있습니다.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Express 경로 직접 포트에서 일부 Express 경로 회로를 암호화 하 고 암호화 되지 않은 동일한 포트에서 다른 회로를 그대로 둘 수 있나요? 
아닙니다. MACsec를 사용 하도록 설정 하면 모든 네트워크 제어 트래픽 (예: BGP 데이터 트래픽 및 고객 데이터 트래픽)이 암호화 됩니다. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>MACsec를 사용/사용 하지 않도록 설정 하거나 MACsec 키를 업데이트 하면 온-프레미스 네트워크는 Express 경로를 통해 Microsoft에 연결 되지 않습니다.
예. MACsec 구성의 경우 미리 공유한 키 모드만 지원 합니다. 즉, 장치 및 Microsoft의 (API를 통해)에서 키를 업데이트 해야 합니다. 이 변경은 원자성이 아니므로 두 면 간에 키가 일치 하지 않는 경우 연결이 손실 됩니다. 구성 변경에 대한 유지 관리 기간을 예약 하는 것이 좋습니다. 가동 중지 시간을 최소화 하려면 네트워크 트래픽을 다른 링크로 전환한 후 한 번에 하나의 Express 경로 직접 연결에서 구성을 업데이트 하는 것이 좋습니다.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>내 장치와 Microsoft에서 MACsec 키가 일치 하지 않는 경우 트래픽이 계속 진행 되나요?
아닙니다. MACsec를 구성 하 고 키 불일치가 발생 하면 Microsoft에 대한 연결이 끊어집니다. 즉, 데이터를 노출 하는 암호화 되지 않은 연결로 대체 되지 않습니다. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Express 경로에서 MACsec를 사용 하도록 설정 하면 네트워크 성능이 저하 되나요?
MACsec 암호화 및 암호 해독은 사용 하는 라우터의 하드웨어에서 발생 합니다. 그 쪽에는 성능에 영향을 주지 않습니다. 그러나 사용 하는 장치에 대한 네트워크 공급 업체에 문의 하 여 MACsec에 성능에 영향을 주는 것이 있는지 확인 해야 합니다.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>암호화에 대해 지원 되는 암호 그룹은 무엇 인가요?
AES128 및 AES256를 지원 합니다.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec에의 한 종단 간 암호화 FAQ
IPsec은 [IETF 표준](https://tools.ietf.org/html/rfc6071)입니다. IP (인터넷 프로토콜) 수준이 나 네트워크 계층 3에서 데이터를 암호화 합니다. IPsec을 사용 하 여 온-프레미스 네트워크와 Azure의 가상 네트워크 (VNET) 간에 종단 간 연결을 암호화할 수 있습니다. 아래 Faq를 참조 하세요.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Express 경로 직접 포트에서 MACsec 외에 IPsec을 사용 하도록 설정할 수 있나요?
예. MACsec은 사용자와 Microsoft 간의 물리적 연결을 보호 합니다. IPsec은 사용자와 Azure의 가상 네트워크 간 종단 간 연결을 보호 합니다. 독립적으로 사용 하도록 설정할 수 있습니다. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Azure VPN gateway를 사용 하 여 온-프레미스 네트워크와 Azure 가상 네트워크 간에 IPsec 터널을 설정할 수 있나요?
예. Express 경로 회로의 Microsoft 피어 링을 통해이 IPsec 터널을 설정할 수 있습니다. [구성 가이드](site-to-site-vpn-over-microsoft-peering.md)를 따르세요.
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Azure VPN gateway를 사용 하 여 Azure 개인 피어 링을 통해 IPsec 터널을 설정할 수 있나요?
Azure 가상 WAN을 채택 하는 경우 [다음 단계](../virtual-wan/vpn-over-expressroute.md) 에 따라 종단 간 연결을 암호화할 수 있습니다. 일반 Azure VNET이 있는 경우 VNET에 타사 VPN gateway를 배포 하 고이 게이트웨이 및 온-프레미스 VPN gateway 간에 IPsec 터널을 설정할 수 있습니다.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Express 경로 연결에서 IPsec을 사용 하도록 설정한 후에 발생 하는 처리량은 무엇 인가요?
Azure VPN gateway를 사용 하는 경우 [여기에서 성능 수치](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 확인 합니다. 타사 VPN gateway를 사용 하는 경우 공급 업체에 문의 하 여 성능 수치를 확인 합니다.

## <a name="next-steps"></a>다음 단계
MACsec 구성에 대한 자세한 내용은 [Macsec 구성](expressroute-howto-macsec.md) 을 참조 하세요.

IPsec 구성에 대한 자세한 내용은 [Ipsec 구성](site-to-site-vpn-over-microsoft-peering.md) 을 참조 하세요.
