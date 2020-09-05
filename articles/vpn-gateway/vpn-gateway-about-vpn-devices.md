---
title: 'Azure VPN Gateway: 연결에 대 한 VPN 장치 정보'
description: 이 문서에서는 S2S VPN Gateway 크로스-프레미스 연결에 대한 VPN 디바이스 및 IPsec 매개 변수에 대해 설명합니다. 구성 지침과 샘플에 대한 링크를 제공합니다.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/28/2020
ms.author: yushwang
ms.openlocfilehash: 18eaf520c70b064f26cd1da5f50c023f42adb8ee
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021819"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>사이트 간 VPN Gateway 연결에 대한 VPN 디바이스 및 IPsec/IKE 매개 변수 정보

VPN 게이트웨이를 사용하여 S2S(사이트 간) 프레미스 간 VPN 연결을 구성하려면 VPN 디바이스가 필요합니다. 온-프레미스 네트워크와 가상 네트워크 간의 보안 연결을 만들려고 할 때마다 또는 하이브리드 솔루션을 만드는 데 사이트 간 연결을 사용할 수 있습니다. 이 문서에서는 유효성이 검사된 VPN 디바이스 목록과 VPN 게이트웨이의 IPsec/IKE 매개 변수 목록을 제공합니다.

> [!IMPORTANT]
> 온-프레미스 VPN 디바이스와 VPN 게이트웨이 간에 연결 문제가 있는 경우 [알려진 디바이스 호환성 문제](#known)를 참조하세요.
>

### <a name="items-to-note-when-viewing-the-tables"></a>테이블 확인 시 주의 사항:

* Azure VPN 게이트웨이에 대한 용어가 변경되었습니다. 이름만 변경되었습니다. 기능이 변경되지 않았습니다.
  * 정적 라우팅 = 정책 기반
  * 동적 라우팅 = 경로 기반
* 고성능 VPN 게이트웨이 및 경로 기반 VPN 게이트웨이에 대한 사양은 별도로 언급하지 않는 한 동일합니다. 예를 들어 경로 기반 VPN 게이트웨이와 호환되는 확인된 VPN 디바이스는 고성능 VPN 게이트웨이와도 호환됩니다.

## <a name="validated-vpn-devices-and-device-configuration-guides"></a><a name="devicetable"></a>확인된 VPN 디바이스 및 디바이스 구성 가이드

디바이스 공급업체와 협력하여 표준 VPN 디바이스 집합의 유효성을 검사했습니다. 다음 목록에 포함된 디바이스 제품군의 모든 디바이스는 VPN 게이트웨이에서 작동합니다. 구성하려는 VPN Gateway 솔루션의 VPN 유형(정책 기반 또는 경로 기반)을 이해하려면 [VPN Gateway 설정 정보](vpn-gateway-about-vpn-gateway-settings.md#vpntype)를 참조하세요.

VPN 디바이스를 구성하려면 적절한 디바이스 제품군에 해당하는 링크를 참조하세요. 구성 지침에 대한 링크가 가장 효율적으로 제공됩니다. VPN 디바이스 지원은 디바이스 제조업체에 문의하세요.

|**공급업체**          |**디바이스 패밀리**     |**최소 OS 버전** |**정책 기반 구성 지침** |**경로 기반 구성 지침** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |호환되지 않음  |[구성 가이드](https://www.a10networks.com/wp-content/uploads/A10-DG-16161-EN.pdf)|
| Allied Telesis     |AR 시리즈 VPN 라우터 |AR 시리즈 5.4.7 이상               | [구성 가이드](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router) |[구성 가이드](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Arista | CloudEOS 라우터 | vEOS 4.24.0 FX | (테스트 되지 않음) | [구성 가이드](https://www.arista.com/en/cg-veos-router/veos-router-cloudeos-ipsec-connectivity-to-azure-virtual-network-gateway) |
| Barracuda Networks, Inc. |Barracuda CloudGen Firewall |정책 기반: 5.4.3<br>경로 기반: 6.2.0 |[구성 가이드](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462887/how-to-configure-an-ikev1-ipsec-site-to-site-vpn-to-the-static-microsoft-azure-vpn-gateway/) |[구성 가이드](https://campus.barracuda.com/product/cloudgenfirewall/doc/79462889/how-to-configure-bgp-over-ikev2-ipsec-site-to-site-vpn-to-an-azure-vpn-gateway/) |
| Check Point |Security Gateway |R80.10 |[구성 가이드](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[구성 가이드](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| 시스코              |ASA       |8.3<br>8.4+(IKEv2*) |지원됨 |[구성 가이드 *](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| 시스코 |ASR |정책 기반: IOS 15.1<br>경로 기반: IOS 15.2 |지원됨 |지원됨 |
| 시스코 | CSR | 경로 기반: IOS-XE 16.10 | (테스트 되지 않음) | [구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| 시스코 |ISR |정책 기반: IOS 15.0<br>경로 기반*: IOS 15.1 |지원됨 |지원됨 |
| 시스코 |Meraki |N/A |호환되지 않음 |호환되지 않음 |
| 시스코 | vEdge (Viptela OS) | 18.4.0 (활성/수동 모드)<br><br>19.2 (활성/활성 모드) | 호환되지 않음 |  [수동 구성 (활성/수동)](https://community.cisco.com/t5/networking-documents/how-to-configure-ipsec-vpn-connection-between-cisco-vedge-and/ta-p/3841454)<br><br>[클라우드 Onramp 구성 (활성/활성)](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/Network-Optimization-and-High-Availability/Network-Optimization-High-Availability-book/b_Network-Optimization-and-HA_chapter_00.html) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 이상 |[구성 가이드](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |호환되지 않음 |
| F5 |BIG-IP 시리즈 |12.0 |[구성 가이드](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[구성 가이드](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 | (테스트 되지 않음) |[구성 가이드](https://docs.fortinet.com/document/fortigate/5.6.0/cookbook/255100/ipsec-vpn-to-azure) |
| Hillstone 네트워크 | 다음-Gen 방화벽 (NGFW) | 5.5 r7  | (테스트 되지 않음) | [구성 가이드](https://www.hillstonenet.com/wp-content/uploads/How-to-setup-Site-to-Site-VPN-between-Microsoft-Azure-and-an-on-premise-Hillstone-Networks-Security-Gateway.pdf) |
| IIJ(Internet Initiative Japan) |SEIL 시리즈 |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[구성 가이드](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |호환되지 않음 |
| Juniper |SRX |정책 기반: JunOS 10.2<br>경로 기반: JunOS 11.4 |지원됨 |[구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| Juniper |J 시리즈 |정책 기반: JunOS 10.4r9<br>경로 기반: JunOS 11.4 |지원됨 |[구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |지원됨 |[구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |지원됨 |[구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|지원됨 |[구성 스크립트](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |라우팅 및 원격 액세스 서비스 |Windows Server 2012 |호환되지 않음 |지원됨 |
| 개방형 시스템 AG |핵심 업무 제어 보안 게이트웨이 |N/A |[구성 가이드](https://open-systems.com/wp-content/uploads/2019/12/OpenSystems-AzureVPNSetup-Installation-Guide.pdf) |호환되지 않음 |
| Palo Alto Networks |PAN-OS를 실행하는 모든 디바이스 |PAN-OS<br>정책 기반: 6.1.5 이상<br>경로 기반: 7.1.4 |지원됨 |[구성 가이드](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| Sentrium (개발자) | VyOS | VyOS 1.2.2 | (테스트 되지 않음) | [구성 가이드 ](https://vyos.readthedocs.io/en/latest/appendix/examples/azure-vpn-bgp.html)|
| ShareTech | 차세대 UTM(NU 시리즈) | 9.0.1.3 | 호환되지 않음 | [구성 가이드](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |TZ 시리즈, NSA 시리즈<br>SuperMassive 시리즈<br>E-클래스 NSA 시리즈 |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |호환되지 않음 |[구성 가이드](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG 차세대 방화벽 | XG v17 | (테스트 되지 않음) | [구성 가이드](https://community.sophos.com/kb/127546)<br><br>[구성 가이드 - 다중 SA](https://community.sophos.com/kb/en-us/133154) |
| Synology | MR2200ac <br>RT2600ac <br>RT1900ac | SRM 1.1.5/VpnPlusServer-1.2.0 | (테스트 되지 않음) | [구성 가이드](https://www.synology.com/en-global/knowledgebase/SRM/tutorial/VPN/How_to_set_up_Site_to_Site_VPN_between_Synology_Router_and_MS_Azure) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 | (테스트 되지 않음) | [IKEv2/IPsec을 통한 BGP](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[IKEv2/IPsec을 통한 VTI](https://help.ubnt.com/hc/en-us/articles/115012305347) |
| Ultra | 3E-636L3 | 5.2.0 빌드-13  | (테스트 되지 않음) | [구성 가이드](https://ultra-3eti.com/wp-content/uploads/2020/07/Azure-VPN-636L3-Site-to-Site-Test-Notes.pdf) |
| WatchGuard |모두 |Fireware XTM<br> 정책 기반: v11.11.x<br>경로 기반: v11.12.x |[구성 가이드](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[구성 가이드](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|
| Zyxel |ZyWALL USG 시리즈<br>ZyWALL ATP 시리즈<br>ZyWALL VPN 시리즈 | ZLD v 4.32 + | (테스트 되지 않음) | [IKEv2/IPsec을 통한 VTI](https://businessforum.zyxel.com/discussion/2648/)<br><br>[IKEv2/IPsec을 통한 BGP](https://businessforum.zyxel.com/discussion/2650/)|

> [!NOTE]
>
> (*)Cisco ASA 버전 8.4+ IKEv2 지원은 "UsePolicyBasedTrafficSelectors" 옵션과 함께 사용자 지정 IPsec/IKE 정책을 사용하여 Azure VPN Gateway에 연결할 수 있습니다. 이 [방법 문서](vpn-gateway-connect-multiple-policybased-rm-ps.md)를 참조하세요.
>
> (\*\*)ISR 7200 시리즈 라우터는 정책 기반 VPN만을 지원합니다.

## <a name="download-vpn-device-configuration-scripts-from-azure"></a><a name="configscripts"></a>Azure에서 VPN 디바이스 구성 스크립트 다운로드

특정 디바이스의 경우 Azure에서 직접 구성 스크립트를 다운로드할 수 있습니다. 자세한 내용 및 다운로드 지침은 [VPN 디바이스 구성 스크립트 다운로드](vpn-gateway-download-vpndevicescript.md)를 참조하세요.

### <a name="devices-with-available-configuration-scripts"></a>사용 가능한 구성 스크립트가 있는 디바이스

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="non-validated-vpn-devices"></a><a name="additionaldevices"></a>확인되지 않은 VPN 디바이스

디바이스가 확인된 VPN 디바이스 테이블에 없더라도 사이트 간 연결을 사용할 수 있습니다. 추가 지원 및 구성 지침은 디바이스 제조업체에 문의하세요.

## <a name="editing-device-configuration-samples"></a><a name="editing"></a>디바이스 구성 샘플 편집

제공된 VPN 디바이스 구성 샘플을 다운로드한 후 환경에 대한 설정을 반영하기 위해 일부 값을 바꿔야 합니다.

### <a name="to-edit-a-sample"></a>샘플을 편집하려면

1. 메모장을 사용하여 샘플을 엽니다.
2. 모든 <*text*> 문자열을 검색하여 환경에 관련된 값으로 바꿉니다. < 및 >를 포함해야 합니다. 이름을 지정할 때 선택하는 이름은 고유해야 합니다. 명령이 작동하지 않는 경우 해당 디바이스 제조업체 설명서를 참조하세요.

| **샘플 텍스트** | **변경 대상** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |이 개체에 대해 선택한 이름입니다. 예: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |이 개체에 대해 선택한 이름입니다. 예: myAzureNetwork |
| &lt;RP_AccessList&gt; |이 개체에 대해 선택한 이름입니다. 예: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |이 개체에 대해 선택한 이름입니다. 예: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |이 개체에 대해 선택한 이름입니다. 예: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |범위를 지정합니다. 예: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |서브넷 마스크를 지정합니다. 예: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |온-프레미스 범위를 지정합니다. 예: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |온-프레미스 서브넷 마스크를 지정합니다. 예: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |이 정보는 사용자 가상 네트워크에 대해 고유하며 관리 포털에 **게이트웨이 IP 주소**로 나와 있습니다. |
| &lt;SP_PresharedKey&gt; |이 정보는 가상 네트워크와 관련이 있으며 키 관리인 관리 포털에 있습니다. |

## <a name="ipsecike-parameters"></a><a name="ipsec"></a>IPsec/IKE 매개 변수

> [!IMPORTANT]
> 1. 아래 표는 알고리즘의 조합 및 기본 구성에서 Azure VPN Gateway가 사용하는 매개 변수를 포함합니다. Azure Resource Management 배포 모델을 사용하여 만든 경로 기반 VPN Gateway의 경우 각 개별 연결에 사용자 지정 정책을 지정할 수 있습니다. 자세한 지침은 [IPsec/IKE 정책 구성](vpn-gateway-ipsecikepolicy-rm-powershell.md)을 참조하세요.
>
> 2. 또한 TCP **MSS**를 **1350**에 고정해야 합니다. 또는 VPN 디바이스가 MSS 고정을 지원하지 않는 경우 대신 터널 인터페이스의 **MTU**를 **1400** 바이트로 설정할 수 있습니다.
>

다음 테이블에서

* SA = 보안 연결
* IKE 1단계는 "주 모드"라고도 합니다.
* IKE 2단계는 "빠른 모드"라고도 합니다.

### <a name="ike-phase-1-main-mode-parameters"></a>IKE 1단계(주 모드) 매개 변수

| **속성**          |**정책 기반**    | **경로 기반**    |
| ---                   | ---               | ---               |
| IKE 버전           |IKEv1              |IKEv1 및 IKEv2    |
| Diffie-Hellman 그룹  |그룹 2(1024비트) |그룹 2(1024비트) |
| 인증 방법 |미리 공유한 키     |미리 공유한 키     |
| 암호화 및 해싱 알고리즘 |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| SA 수명           |28,800초     |28,800초     |

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 2단계(빠른 모드) 매개 변수

| **속성**                  |**정책 기반**| **경로 기반**                              |
| ---                           | ---           | ---                                         |
| IKE 버전                   |IKEv1          |IKEv1 및 IKEv2                              |
| 암호화 및 해싱 알고리즘 |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[RouteBased QM SA 제품](#RouteBasedOffers) |
| SA 수명(시간)            |3,600초  |27,000초                               |
| SA 수명(바이트)           |102,400,000 KB |102,400,000 KB                               |
| PFS(Perfect Forward Secrecy) |아니요             |[RouteBased QM SA 제품](#RouteBasedOffers) |
| 작동하지 않는 피어 검색(DPD)     |지원되지 않음  |지원됨                                    |


### <a name="routebased-vpn-ipsec-security-association-ike-quick-mode-sa-offers"></a><a name ="RouteBasedOffers"></a>RouteBased VPN IPsec 보안 연결(IKE 빠른 모드 SA) 제품

다음 표는 IPsec SA(IKE 빠른 모드) 제품을 나열합니다. 제안이 제시되거나 수락되는 기본 설정 순서대로 제안이 나열되어 있습니다.

#### <a name="azure-gateway-as-initiator"></a>Azure 게이트웨이(초기자)

|-  |**암호화**|**인증**|**PFS 그룹**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM(AES256)      |없음         |
| 2 |AES256        |SHA1              |없음         |
| 3 |3DES          |SHA1              |없음         |
| 4 |AES256        |SHA256            |없음         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |없음         |

#### <a name="azure-gateway-as-responder"></a>Azure 게이트웨이(응답자)

|-  |**암호화**|**인증**|**PFS 그룹**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM(AES256)      |없음         |
| 2 |AES256        |SHA1              |없음         |
| 3 |3DES          |SHA1              |없음         |
| 4 |AES256        |SHA256            |없음         |
| 5 |AES128        |SHA1              |None         |
| 6 |3DES          |SHA256            |없음         |
| 7 |DES           |SHA1              |없음         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |없음         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* 경로 기반 및 고성능 VPN 게이트웨이를 사용하여 IPsec ESP NULL 암호화를 지정할 수 있습니다. Null 기반 암호화는 전송 중인 데이터를 보호하지 않으며, 최대 처리량 및 최소 대기 시간이 필요한 경우에만 사용됩니다. 클라이언트에서는 VNet 간 통신 시나리오 또는 솔루션의 다른 곳에서 암호화가 적용된 경우에 이 암호화를 사용할 수 있습니다.
* 인터넷을 통한 프레미스 간 연결의 경우 중요한 통신의 보안을 보장하려면 위의 테이블에 나열된 암호화 및 해시 알고리즘을 사용하는 기본 Azure VPN Gateway 설정을 사용하세요.

## <a name="known-device-compatibility-issues"></a><a name="known"></a>알려진 장치 호환성 문제

> [!IMPORTANT]
> 해당 내용은 타사 VPN 디바이스 및 Azure VPN 게이트웨이 간의 알려진 호환성 문제입니다. Azure 팀은 여기에 나열된 문제를 해결하기 위해 공급 업체와 함께 적극적으로 작업 중입니다. 문제가 해결되면 이 페이지는 가장 최신 정보로 업데이트됩니다. 주기적으로 다시 확인하세요.
>
>

### <a name="feb-16-2017"></a>2017년 2월 16일

Azure 경로 기반 VPN에 대한 **7.1.4 이전 버전으로 Palo Alto Networks 디바이스**: 7.1.4 이전의 PAN-OS 버전으로 Palo Alto Networks에서 VPN 디바이스를 사용하고 Azure 경로 기반 VPN 게이트웨이에 연결 문제가 발생하는 경우 다음 단계를 수행하세요.

1. Palo Alto Networks 디바이스의 펌웨어 버전을 확인합니다. PAN-OS 버전이 7.1.4보다 오래된 경우 7.1.4로 업그레이드하세요.
2. Palo Alto Networks 디바이스에서 Azure VPN Gateway로 연결하는 경우 단계 2 SA(또는 빠른 모드 SA) 수명을 28,800초(8시간)로 변경합니다.
3. 여전히 연결 문제가 발생하면 Azure Portal에서 지원 요청을 여세요.
