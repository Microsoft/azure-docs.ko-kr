---
title: Azure VPN Gateway에 Cisco ASA 디바이스를 연결하는 샘플 구성 | Microsoft Docs
description: 이 문서에서는 Azure VPN Gateway에 Cisco ASA 디바이스를 연결하는 샘플 구성을 제공합니다.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 10/19/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: e575fac61a1c5d9351391d39d200b87e34ff26cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648762"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>샘플 구성: Cisco ASA 디바이스(IKEv2/BGP 아님)
이 문서에서는 Azure VPN Gateway에 Cisco ASA(적응 보안 어플라이언스) 디바이스를 연결하는 샘플 구성을 제공합니다. 이 예제는 BGP(경계 게이트웨이 프로토콜) 없이 IKEv2를 실행하는 Cisco ASA 디바이스에 적용됩니다. 

## <a name="device-at-a-glance"></a>디바이스 개요

|                        |                                   |
| ---                    | ---                               |
| 디바이스 공급 업체          | 시스코                             |
| 디바이스 모델           | ASA                               |
| 대상 버전         | 8.4 이상                     |
| 테스트 모델           | ASA 5505                          |
| 테스트 버전         | 9.2                               |
| IKE 버전            | IKEv2                             |
| BGP                    | 아닙니다.                                |
| Azure VPN 게이트웨이 유형 | 경로 기반 VPN Gateway           |
|                        |                                   |

> [!NOTE]
> 샘플 구성은 Cisco ASA 디바이스를 Azure **경로 기반** VPN Gateway에 연결합니다. 연결은 [이 문서](vpn-gateway-connect-multiple-policybased-rm-ps.md)에 설명된 대로 **UsePolicyBasedTrafficSelectors** 옵션이 있는 사용자 지정 IPsec/IKE 정책을 사용합니다.
>
> 샘플에서는 ASA가 VTI 기반이 아닌 액세스 목록 기반 구성으로 **IKEv2** 정책을 사용해야 합니다. 해당 VPN 디바이스 공급업체 사양을 참조하여 IKEv2 정책이 온-프레미스 VPN 디바이스에서 지원되는지 확인하세요.


## <a name="vpn-device-requirements"></a>VPN 디바이스 요구 사항
Azure VPN Gateway는 표준 IPsec/IKE 프로토콜 도구 모음을 사용하여 S2S(사이트 간) VPN 터널을 설정합니다. 자세한 IPsec/IKE 프로토콜 매개 변수 및 Azure VPN Gateway에 대한 기본 암호화 알고리즘은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.

> [!NOTE]
> [암호화 요구 사항 정보](vpn-gateway-about-compliance-crypto.md)에 설명된 대로 특정 연결에 대한 암호화 알고리즘 및 키 길이의 정확한 조합을 선택적으로 지정할 수 있습니다. 암호화 알고리즘 및 키 길이의 정확한 조합을 지정하는 경우 VPN 디바이스에서 해당 사양을 사용해야 합니다.

## <a name="single-vpn-tunnel"></a>단일 VPN 터널
이 구성은 Azure VPN Gateway와 온-프레미스 VPN 디바이스 간에 단일 S2S VPN 터널로 구성됩니다. 필요에 따라 VPN 터널에서 BGP를 구성할 수 있습니다.

![단일 S2S VPN 터널](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Azure 구성을 빌드하는 단계별 지침은 [단일 VPN 터널 설정](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)을 참조하세요.

### <a name="virtual-network-and-vpn-gateway-information"></a>네트워크 및 VPN Gateway 정보
이 섹션은 샘플에 대한 매개 변수를 나열합니다.

| **매개 변수**                | **값**                    |
| ---                          | ---                          |
| 가상 네트워크 주소 접두사        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN 게이트웨이 IP         | Azure_Gateway_Public_IP      |
| 온-프레미스 주소 접두사 | 10.51.0.0/16<br>10.52.0.0/16 |
| 온-프레미스 VPN 디바이스 IP    | OnPrem_Device_Public_IP     |
| *가상 네트워크 BGP ASN                | 65010                        |
| *Azure BGP 피어 IP           | 10.12.255.30                 |
| *온-프레미스 BGP ASN         | 65050                        |
| *온-프레미스 BGP 피어 IP     | 10.52.255.254                |
|                              |                              |

\*BGP에만 해당하는 선택적 매개 변수

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE 정책 및 매개 변수
다음 표에서는 샘플에 사용된 IPsec/IKE 알고리즘 및 매개 변수를 나열합니다. 알고리즘이 VPN 디바이스 모델 및 펌웨어 버전에 지원되는지 확인하려면 VPN 디바이스 사양을 참조하세요.

| **IPsec/IKEv2**  | **값**                            |
| ---              | ---                                  |
| IKEv2 암호화 | AES256                               |
| IKEv2 무결성  | SHA384                               |
| DH 그룹         | DHGroup24                            |
| *IPsec 암호화 | AES256                               |
| *IPsec 무결성  | SHA1                                 |
| PFS 그룹        | PFS24                                |
| QM SA 수명   | 7,200초                         |
| 트래픽 선택기 | UsePolicyBasedTrafficSelectors $True |
| 미리 공유한 키   | PreSharedKey                         |
|                  |                                      |

\*IPsec 암호화 알고리즘이 AES GCM에서 사용될 경우 일부 디바이스에서 IPsec 무결성은 null 값이어야 합니다.

### <a name="asa-device-support"></a>ASA 디바이스 지원

* IKEv2를 지원하려면 ASA 버전 8.4 이상이 필요합니다.

* 그룹 5 이외에 DH 그룹 및 PFS 그룹 지원에는 ASA 버전 9.x가 필요합니다.

* GCM AES를 사용하는 IPsec 암호화 및 SHA-256, SHA-384 또는 SHA-512를 사용하는 IPsec 무결성의 지원에는 ASA 버전 9.x가 필요합니다. 이 지원 요구 사항은 새로운 ASA 디바이스에 적용됩니다. 게시하는 시점에 ASA 모델 5505, 5510, 5520, 5540, 5550 및 5580은 이러한 알고리즘을 지원하지 않습니다. 알고리즘이 VPN 디바이스 모델 및 펌웨어 버전에 지원되는지 확인하려면 VPN 디바이스 사양을 참조하세요.


### <a name="sample-device-configuration"></a>샘플 디바이스 구성
스크립트는 이전 섹션에서 설명하는 구성 및 매개 변수를 기반으로 하는 샘플을 제공합니다. S2S VPN 터널 구성은 다음과 같은 부분으로 이루어져 있습니다.

1. 인터페이스 및 경로
2. 액세스 목록
3. IKE 정책 및 매개 변수(1단계 또는 기본 모드)
4. IPsec 정책 및 매개 변수(2단계 또는 빠른 모드)
5. 기타 매개 변수(예: TCP MSS 고정)

> [!IMPORTANT]
> 샘플 스크립트를 사용하기 전에 다음 단계를 완료합니다. 스크립트에서 자리 표시자 값을 구성에 대한 디바이스 설정으로 바꿉니다.

* 인터페이스 내부와 외부에 대해 인터페이스 구성을 지정합니다.
* 내부/개인 및 외부/공용 네트워크에 대한 경로를 식별합니다.
* 디바이스에서 모든 이름 및 정책 번호가 고유한지 확인합니다.
* 디바이스에서 암호화 알고리즘이 지원되는지 확인합니다.
* 다음 **자리 표시자 값**을 구성에 대한 실제 값으로 바꿉니다.
  - 외부 인터페이스 이름: **outside**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - 가상 네트워크 및 로컬 네트워크 게이트웨이 이름: **VNetName** 및 **LNGName**
  - 가상 네트워크 및 온-프레미스 네트워크 주소 **접두사**
  - 적절한 **네트워크 마스크**

#### <a name="sample-script"></a>샘플 스크립트

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>간단한 디버깅 명령

디버깅 목적을 위한 일해 다음 ASA 명령을 사용합니다.

* IPsec 또는 IKE SA(보안 연결)를 보여줍니다.
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* 디버그 모드를 입력합니다.
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` 명령은 콘솔에서 중요한 출력을 생성할 수 있습니다.

* 디바이스에서 현재 구성을 보여줍니다.
    ```
    show run
    ```
    `show` 하위 명령을 사용하여 디바이스 구성의 특정 부분을 나열합니다. 예:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>다음 단계
활성-활성 크로스-프레미스 및 VNet 간 연결을 구성하려면 [활성-활성 VPN Gateway 구성](vpn-gateway-activeactive-rm-powershell.md)을 참조하세요.
<!--Update_Description: wording update -->