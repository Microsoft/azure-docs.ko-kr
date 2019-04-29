---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/14/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 36b3fcfa90b5b1de9c9d3262da1f3e519cc99c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456481"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>사용자 지정 IPsec/IKE 정책은 모든 Azure VPN Gateway SKU에서 지원되나요?
사용자 지정 IPsec/IKE 정책은 **VpnGw1, VpnGw2, VpnGw3, 표준** 및 **HighPerformance** VPN Gateway에서 지원됩니다. **기본** SKU는 지원되지 **않습니다**.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>연결에서 얼마나 많은 정책을 지정할 수 있나요?
지정된 연결에 대해 ***하나의*** 정책 조합만 지정할 수 있습니다.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>연결에 대해 부분적 정책을 지정할 수 있나요? (예: IPsec을 제외하고 IKE 알고리즘만 해당)
아니요, IKE(주 모드) 및 IPsec(빠른 모드) 모두에 대한 모든 알고리즘 및 매개 변수를 지정해야 합니다. 부분 정책 지정은 허용되지 않습니다.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>사용자 지정 정책에서 지원되는 알고리즘과 키 강도는 어떻게 되나요?
다음 표에는 고객이 구성 가능하도록 지원되는 암호화 알고리즘 및 키 강도가 나와 있습니다. 모든 필드에 대해 한 가지 옵션을 선택해야 합니다.

| **IPsec/IKEv2**  | **옵션**                                                                   |
| ---              | ---                                                                           |
| IKEv2 암호화 | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2 무결성  | SHA384, SHA256, SHA1, MD5                                                     |
| DH 그룹         | DHGroup24, ECP384, ECP256, DHGroup14(DHGroup2048), DHGroup2, DHGroup1, 없음 |
| IPsec 암호화 | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, 없음      |
| IPsec 무결성  | GCMAES256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                            |
| PFS 그룹        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, 없음                              |
| QM SA 수명   | 초(정수, **최소 300**/기본값 27,000초)<br>KB(정수, **최소 1,024**/기본값 102,400,000KB)           |
| 트래픽 선택기 | UsePolicyBasedTrafficSelectors($True/$False, 기본값: $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 및 PFS2048은 IKE 및 IPsec PFS의 Diffie-Hellman 그룹 **14**와 동일합니다. 전체 매핑은 [Diffie-Hellman 그룹](#DH)을 참조하세요.
> 2. GCMAES 알고리즘의 경우 IPsec 암호화 및 무결성 모두에 대해 동일한 GCMAES 알고리즘 및 키 길이를 지정해야 합니다.
> 3. IKEv2 주 모드 SA 수명은 Azure VPN Gateway에서 28,800초로 고정됩니다.
> 4. QM SA 수명은 선택적 매개 변수입니다. 지정되지 않으면 기본값인 27,000초(7.5시간) 및 102,400,000KB(102GB)가 사용됩니다.
> 5. UsePolicyBasedTrafficSelector는 연결에 대한 옵션 매개 변수입니다. "UsePolicyBasedTrafficSelectors"에 대한 다음 FAQ 항목을 참조하세요.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Azure VPN Gateway 정책과 온-프레미스 VPN 디바이스 구성 간에 모든 항목이 일치해야 하나요?
온-프레미스 VPN 디바이스 구성은 Azure IPsec/IKE 정책에서 지정한 다음 알고리즘 및 매개 변수가 일치하거나 포함해야 합니다.

* IKE 암호화 알고리즘
* IKE 무결성 알고리즘
* DH 그룹
* IPsec 암호화 알고리즘
* IPsec 무결성 알고리즘
* PFS 그룹
* 트래픽 선택기(*)

SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.

**UsePolicyBasedTrafficSelectors**를 사용하도록 설정한 경우 VPN 디바이스에 온-프레미스 네트워크(로컬 네트워크 게이트웨이) 접두사 및 Azure Virtual Network 접두사 간의 모든 조합으로 정의된 일치하는 트래픽 선택기가 있는지 확인해야 합니다. 예를 들어 온-프레미스 네트워크 접두사가 10.1.0.0/16 및 10.2.0.0/16이고 가상 네트워크 접두사가 192.168.0.0/16 및 172.16.0.0/16이면 다음 트래픽 선택기를 지정해야 합니다.
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

자세한 내용은 [여러 온-프레미스 정책 기반 VPN 디바이스 연결](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

### <a name ="DH"></a>어떤 Diffie-Hellman 그룹이 지원됩니까?
아래 표에는 IKE(DHGroup) 및 IPsec(PFSGroup)에 지원되는 Diffie-Hellman 그룹이 나열되어 있습니다.

| **Diffie-Hellman 그룹**  | **DHGroup**              | **PFSGroup** | **키 길이** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768비트 MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024비트 MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048비트 MODP  |
| 19                        | ECP256                   | ECP256       | 256비트 ECP    |
| 20                        | ECP384                   | ECP384       | 384비트 ECP    |
| 24                        | DHGroup24                | PFS24        | 2048비트 MODP  |
|                           |                          |              |                |

자세한 내용은 [RFC3526](https://tools.ietf.org/html/rfc3526) 및 [RFC5114](https://tools.ietf.org/html/rfc5114)를 참조하세요.

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>사용자 지정 정책이 Azure VPN Gateway에 대한 기본 IPsec/IKE 정책 집합을 대체하나요?
예, 연결에 사용자 지정 정책이 지정되면 Azure VPN Gateway는 IKE 개시 장치 및 IKE 응답기로의 연결에만 정책을 사용합니다.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>사용자 지정 IPsec/IKE 정책을 제거하면 연결이 보호되지 않나요?
아니요, 연결은 IPsec/IKE로 계속 보호됩니다. 연결에서 사용자 지정 정책을 제거하면 Azure VPN Gateway는 [IPsec/IKE 제안의 기본 목록](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)으로 다시 되돌아와서 온-프레미스 VPN 디바이스에서 IKE 핸드셰이크를 다시 시작합니다.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>IPsec/IKE 정책을 추가 또는 업데이트하는 것이 VPN 연결에 방해가 될까요?
예, Azure VPN Gateway가 기존 연결을 삭제하고 IKE 핸드셰이크를 다시 시작하여 새로운 암호화 알고리즘 및 매개 변수로 IPsec 터널을 다시 설정하므로 약간의 서비스 중단(몇 초)이 발생할 수 있습니다. 중단을 최소화하려면 온-프레미스 VPN 디바이스가 일치하는 알고리즘 및 키 강도로 구성되었는지도 확인하세요.

### <a name="can-i-use-different-policies-on-different-connections"></a>다른 연결에 다른 정책을 사용할 수 있나요?
예. 사용자 지정 정책은 각 연결 단위로 적용됩니다. 다른 연결에 서로 다른 IPsec/IKE 정책을 만들어 적용할 수 있습니다. 또한 연결의 하위 집합에 대해 사용자 지정 정책을 적용하도록 선택할 수도 있습니다. 나머지는 Azure 기본 IPsec/IKE 정책 집합을 사용합니다.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>VNet 간 연결에서도 사용자 지정 정책을 사용할 수 있나요?
예, IPsec 프레미스 간 연결 또는 VNet 간 연결 모두에 사용자 지정 정책을 적용할 수 있습니다.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>두 VNet 간 연결 리소스에 동일한 정책을 지정해야 하나요?
예. Azure에서 VNet 간 터널은 두 개의 연결 리소스(각 방향당 하나씩)로 구성됩니다. 두 연결 리소스에 동일한 정책이 있어야 합니다. 그렇지 않으면 VNet 간 연결이 설정되지 않습니다.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>ExpressRoute 연결에서 사용자 지정 IPsec/IKE 정책이 작동하나요?
아니요. IPsec/IKE 정책은 Azure VPN Gateway를 통해 S2S VPN 및 VNet 간 연결에서만 작동합니다.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>IPsec에 대한 추가 구성 정보는 어디에서 찾을 수 있나요?
[S2S 또는 VNet 간 연결에 대한 IPsec/IKE 정책 구성](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.
