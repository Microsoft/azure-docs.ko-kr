---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f72ce02a8655ea97497098dc1412f69e07686861
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320154"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>지점 및 사이트 간 구성에서 VPN 클라이언트 엔드포인트를 몇 개까지 지정할 수 있습니까?

게이트웨이 SKU에 따라 다릅니다. 지원되는 연결 수에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.

### <a name="supportedclientos"></a>지점 및 사이트 간 연결에 사용할 수 있는 클라이언트 운영 체제는 무엇인가요?

다음과 같은 클라이언트 운영 체제가 지원됩니다.

* Windows 7(32비트 및 64비트)
* Windows Server 2008 R2(64비트 전용)
* Windows 8.1(32비트 및 64비트)
* Windows Server 2012(64비트 전용)
* Windows Server 2012 R2(64비트 전용)
* Windows Server 2016(64비트 전용)
* 윈도우 10
* Mac OS X 버전 10.11 이상
* Linux(StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>지점 및 사이트 간 기능을 사용하여 프록시와 방화벽을 트래버스할 수 있습니까?

Azure에서는 두 가지 형식의 지점 및 사이트 VPN 옵션을 지원합니다.

* SSTP(Secure Socket Tunneling Protocol) SSTP는 Microsoft 등록 SSL 기반 솔루션으로 대부분의 방화벽이 443 SSL에서 사용하는 TCP 포트를 열기 때문에 방화벽을 뚫을 수 있습니다.

* IKEv2 VPN IKEv2 VPN은 표준 기반 IPsec VPN 솔루션으로 UDP 포트 500과 4500 및 IP 주소 프로토콜 번호를 사용합니다. 50. 방화벽이 항상 이러한 포트를 열지는 않습니다. 따라서 IKEv2 VPN이 프록시 및 방화벽을 트래버스할 수 없는 가능성이 있습니다.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>지점 및 사이트 간 연결에 대해 구성된 클라이언트 컴퓨터를 다시 시작하면 VPN이 자동으로 다시 연결됩니까?

기본적으로 클라이언트 컴퓨터에서는 VPN 연결을 자동으로 다시 설정하지 않습니다.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>지점 및 사이트 간 연결은 VPN 클라이언트에서 자동 다시 연결 및 DDNS를 지원합니까?

자동 다시 연결과 DDNS는 현재 지점 및 사이트 간 VPN에서 지원되지 않습니다.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>동일한 가상 네트워크에 대해 사이트 간 구성과 지점 및 사이트 간 구성을 함께 사용할 수 있습니까?

예. Resource Manager 배포 모델의 경우 게이트웨이에 대한 경로 기반 VPN 형식이 있어야 합니다. 클래식 배포 모델의 경우 동적 게이트웨이가 필요합니다. 고정 라우팅 VPN Gateway 또는 경로 기반 VPN Gateway에 지점 및 사이트 간 연결을 지원하지 않습니다.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>지점 및 사이트 간 클라이언트를 여러 가상 네트워크에 동시에 연결하도록 구성할 수 있습니까?

아니요. 지점 및 사이트 간 클라이언트만이 가상 네트워크 게이트웨이가 있는 VNet의 리소스에 연결할 수 있습니다.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>사이트 간 연결 또는 지점 및 사이트 간 연결을 통해 어느 정도의 처리량을 제공할 수 있습니까?

VPN 터널의 정확한 처리량을 유지하는 것은 어렵습니다. IPsec과 SSTP는 암호화 중심 VPN 프로토콜입니다. 또한 처리량은 프레미스와 인터넷 간의 대기 시간과 대역폭에 의해 제한됩니다. IKEv2 지점 및 사이트 VPN 연결을 포함한 VPN Gateway의 경우 예상할 수 있는 총 처리량은 게이트웨이 SKU에 따라 달라집니다. 처리량에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>SSTP 및/또는 IKEv2를 지원하는 지점 및 사이트 간 연결에 소프트웨어 VPN 클라이언트를 사용할 수 있나요?

아니요. SSTP의 경우 Windows에서 네이티브 VPN 클라이언트를 사용하고 IKEv2의 경우 Mac에서 네이티브 VPN 클라이언트를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제 목록을 참조하세요.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure는 Windows에서 IKEv2 VPN을 지원합니까?

IKEv2는 Windows 10 및 Server 2016에서 지원됩니다. 그러나 IKEv2를 사용하려면 업데이트를 설치하고 로컬로 레지스트리 키 값을 설정해야 합니다. Windows 10 이전의 OS 버전은 지원 되지 않으며 SSTP만 사용할 수 있습니다 또는 **OpenVPN® 프로토콜**합니다.

IKEv2에 대해 Windows 10 또는 Server 2016을 준비하려면:

1. 업데이트를 설치합니다.

   | OS 버전 | Date | 번호/링크 |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 버전 1607 | 2018년 1월 17일 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 버전 1703 | 2018년 1월 17일 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 버전 1709 | 2018년 3월 22일 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. 레지스트리 키 값을 설정합니다. 레지스트리에 “HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload” REG_DWORD 키를 만들거나 1로 설정합니다.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>P2S VPN 연결에 대해 SSTP 및 IKEv2를 모두 구성되면 어떻게 되나요?

혼합된 환경(Windows 및 Mac 디바이스로 구성)에서 SSTP 및 IKEv2 모두를 구성하는 경우 Windows VPN 클라이언트는 항상 IKEv2 터널을 먼저 시도하지만 IKEv2 연결이 실패하는 경우 SSTP로 대체합니다. MacOSX는 IKEv2를 통해서만 연결합니다.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Azure는 P2S VPN에 대해 Windows나 Mac 이외에 다른 어떤 플랫폼을 지원합니까?

Azure는 P2S VPN에 대해 Windows, Mac 및 Linux를 지원합니다.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>배포된 Azure VPN Gateway가 이미 있습니다. 여기에서 RADIUS 및/또는 IKEv2 VPN을 사용할 수 있습니까?

예, 사용하는 게이트웨이 SKU에서 RADIUS 및/또는 IKEv2를 지원하는 경우 Powershell 또는 Azure Portal을 사용하여 이미 배포된 게이트웨이에서 이러한 새 기능을 사용할 수 있습니다. 예를 들어 VPN 게이트웨이 기본 SKU는 RADIUS 또는 IKEv2를 지원하지 않습니다.
