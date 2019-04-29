---
title: 가상 네트워크 VPN Gateway 및 PowerShell을 사용 하 여 여러 사이트에 연결 합니다. 클래식 | Microsoft Docs
description: VPN Gateway를 사용하여 여러 로컬 온-프레미스 사이트를 클래식 가상 네트워크에 연결합니다.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836101"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>기존 VPN Gateway 연결이 있는 VNet에 사이트 간 연결 추가(클래식)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell(클래식)](vpn-gateway-multi-site.md)
>
>

이 문서에서는 PowerShell을 사용하여 기존 연결이 있는 VPN 게이트웨이에 S2S(사이트 간) 연결을 추가하는 과정을 안내합니다. 이러한 유형의 연결을 "다중 사이트" 구성이라고 합니다. 이 문서의 단계는 클래식 배포 모델(서비스 관리라고도 함)을 사용하여 만들어진 가상 네트워크에 적용됩니다. 이러한 단계가 ExpressRoute/사이트 간 공존 연결 구성에는 적용되지 않습니다.

### <a name="deployment-models-and-methods"></a>배포 모델 및 메서드

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

새 문서 및 추가 도구를 이 구성에 사용할 수 있게 되었으므로 다음 표를 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>연결 정보

단일 가상 네트워크에 여러 온-프레미스 사이트를 연결할 수 있습니다. 이 방식은 하이브리드 클라우드 솔루션을 구축하는 경우 특히 유용합니다. Azure Virtual Network 게이트웨이에 대한 다중 사이트 연결을 만드는 작업은 다른 사이트 간 연결을 만드는 작업과 비슷합니다. 실제로, 게이트웨이가 동적(경로 기반)이라면 기존 Azure VPN 게이트웨이를 사용할 수 있습니다.

이미 정적 게이트웨이가 가상 네트워크에 연결된 경우에는 가상 네트워크를 다시 빌드하지 않고도 게이트웨이 유형을 동적으로 변경하여 다중 사이트를 수용할 수 있습니다. 라우팅 유형을 변경하기 전에 온-프레미스 VPN 게이트웨이가 경로 기반 VPN 구성을 지원하는지 확인합니다.

![다중 사이트 다이어그램](./media/vpn-gateway-multi-site/multisite.png "다중 사이트")

## <a name="points-to-consider"></a>고려할 항목

**이 가상 네트워크 변경을 위해 포털을 사용할 수 없습니다.** 포털을 사용하는 대신 네트워크 구성 파일을 변경해야 합니다. 포털에서 변경한 경우, 사용자의 다중 사이트 참조 설정을 덮어씁니다.

따라서 다중 사이트 절차를 완료할 때까지는 네트워크 구성 파일을 사용하는 익숙해져야 합니다. 그러나 네트워크 구성을 여러 사용자가 사용하는 경우에는 이 제한에 대해 모든 사람이 알 수 있도록 해야 합니다. 포털을 아예 사용할 수 없다는 뜻은 아닙니다. 이 특정 가상 네트워크에 대한 구성 변경을 제외한 모든 작업에 관리 포털을 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음이 있는지 확인합니다.

* 각 온-프레미스 위치에서 호환되는 VPN 하드웨어. 사용하려는 디바이스가 호환 가능한 것인지 확인하려면 [Virtual Network 연결을 위한 VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* 각 VPN 디바이스에 대한 외부 연결 공용 IPv4 IP 주소. IP 주소는 NAT 뒤에 배치할 수 없습니다. 이는 필수 요구 사항입니다.
* 최신 버전의 Azure PowerShell cmdlet을 설치해야 합니다. SM(서비스 관리)와 Resource Manager 버전을 모두 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview) 을 참조하세요.
* VPN 하드웨어 구성에 능숙한 사용자. 사용자가 VPN 디바이스 구성 방법에 대해 매우 잘 알고 있거나 이와 관련된 지식이 있는 사람과 작업해야 합니다.
* 가상 네트워크에 사용할 IP 주소 범위(아직 만들지 않은 경우).
* 연결할 각 로컬 네트워크 사이트에 대한 IP 주소 범위. 연결하려는 각 로컬 네트워크 사이트의 IP 주소 범위가 겹치지 않도록 해야 합니다. 그렇지 않은 경우 포털 또는 REST API는 업로드 중인 구성을 거절합니다.<br>예를 들어 두 로컬 네트워크 사이트에 IP 주소 범위 10.2.3.0/24가 모두 포함되어 있고 대상 주소가 10.2.3.3인 패키지가 있는 경우, 주소 범위가 겹치기 때문에 Azure는 어떤 사이트에 패키지를 전송할지 알지 못하게 됩니다. 라우팅 문제를 방지하기 위해 Azure에서는 범위가 겹치는 구성 파일의 업로드를 허용하지 않습니다.

## <a name="1-create-a-site-to-site-vpn"></a>1. 사이트 간 VPN 만들기
동적 라우팅 게이트웨이를 사용한 사이트 간 VPN이 이미 있으면 좋습니다. [가상 네트워크 구성 설정 내보내기](#export)로 진행할 수 있습니다. 아직 만들지 않은 경우는 아래 작업을 수행합니다.

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>사이트 간 가상 네트워크가 이미 있지만 정적(정책 기반) 라우팅 게이트웨이가 있는 경우:
1. 게이트웨이 유형을 동적 라우팅으로 변경합니다. 다중 사이트 VPN에는 동적(경로 기반이라고도 함) 라우팅 게이트웨이가 필요합니다. 게이트웨이 유형을 변경하려면 먼저 기존 게이트웨이를 삭제한 다음 새로 만들어야 합니다.
2. 새 게이트웨이를 구성하고 VPN 터널을 만듭니다. 지침에 대해서는 [SKU와 VPN 유형 지정](vpn-gateway-howto-site-to-site-classic-portal.md#sku)을 참조하세요. 라우팅 유형을 '동적'으로 지정해야 합니다.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>사이트 간 가상 네트워크 사이트가 없는 경우:
1. 이러한 지침을 사용 하 여 사이트 간 가상 네트워크를 만듭니다. [사이트 간 VPN 연결으로 가상 네트워크 만들기](vpn-gateway-site-to-site-create.md)합니다.  
2. 이러한 지침을 사용 하 여 동적 라우팅 게이트웨이 구성 합니다. [VPN Gateway 구성](vpn-gateway-configure-vpn-gateway-mp.md)합니다. 사용 중인 게이트웨이 유형에 맞는 **동적 라우팅** 을 선택해야 합니다.

## <a name="export"></a>2. 네트워크 구성 파일을 내보내기
다음 명령을 실행하여 Azure 네트워크 구성 파일을 내보냅니다. 필요한 경우 다른 위치로 내보낼 파일의 위치를 변경할 수 있습니다.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. 네트워크 구성 파일 열기
마지막 단계에서 다운로드한 네트워크 구성 파일을 엽니다. 원하는 xml 편집기를 사용합니다. 파일은 다음과 유사하게 나타납니다.

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. 여러 사이트 참조 추가
사이트 참조 정보를 추가 또는 제거하면 ConnectionsToLocalNetwork/LocalNetworkSiteRef의 구성이 변경됩니다. 새 로컬 사이트 참조를 추가하면 Azure에서 새 터널을 만듭니다. 아래 예제에서 네트워크 구성은 단일 사이트 연결용입니다. 모두 변경했으면 파일을 저장합니다.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

추가 사이트 참조를 추가하려면(다중 사이트 구성 만들기) 아래 예제와 같이 추가 "LocalNetworkSiteRef" 줄을 추가하기만 하면 됩니다.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. 네트워크 구성 파일 가져오기
네트워크 구성 파일을 가져옵니다. 변경 내용과 함께 이 파일을 가져오면 새 터널이 추가됩니다. 터널은 이전에 만든 동적 게이트웨이를 사용합니다. PowerShell을 사용하여 파일을 가져올 수 있습니다.

## <a name="6-download-keys"></a>6. 키 다운로드
새 터널을 추가한 후 PowerShell cmdlet 'Get-AzureVNetGatewayKey'를 사용하여 각 터널의 IPsec/IKE 사전 공유 키를 가져옵니다.

예를 들면 다음과 같습니다.

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

원하는 경우 *Virtual Network 게이트웨이 공유 키 가져오기* REST API를 사용하여 사전 공유 키를 가져올 수도 있습니다.

## <a name="7-verify-your-connections"></a>7. 연결 확인
다중 사이트 터널 상태를 확인합니다. 각 터널에 대한 키를 다운로드한 후 연결을 확인할 수 있습니다. 아래 예제에 나온 것처럼 'Get-AzureVnetConnection'을 사용하여 가상 네트워크 터널 목록을 가져옵니다. VNet1이 VNet의 이름입니다.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

예제는 다음을 반환합니다.

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>다음 단계

VPN Gateway에 대한 자세한 내용은 [VPN Gateway 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.
