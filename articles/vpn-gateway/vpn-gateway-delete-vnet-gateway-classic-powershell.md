---
title: '가상 네트워크 게이트웨이 삭제: PowerShell: Azure 클래식 | Microsoft Docs'
description: 클래식 배포 모델에서 PowerShell을 사용하여 가상 네트워크 게이트웨이를 삭제합니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863983"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>PowerShell(클래식)을 사용하여 가상 네트워크 삭제

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [클래식 - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

이 문서에서는 PowerShell을 사용하여 클래식 배포 모델에서 VPN Gateway를 삭제하도록 도와줍니다. 가상 네트워크 게이트웨이가 삭제되면 네트워크 구성 파일을 수정하여 더 이상 사용하지 않는 요소를 제거합니다.

## <a name="connect"></a>1단계: Azure에 연결

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. 최신 PowerShell cmdlet을 설치합니다.

최신 버전의 Azure SM(서비스 관리) PowerShell cmdlet을 다운로드하여 설치합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

### <a name="2-connect-to-your-azure-account"></a>2. Azure 계정에 연결합니다. 

상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```powershell
Add-AzureAccount
```

## <a name="export"></a>2단계: 네트워크 구성 파일 내보내기 및 확인

컴퓨터에 디렉터리를 만들고 디렉터리에 네트워크 구성 파일을 내보냅니다. 이 파일을 사용하여 현재 구성 정보를 보고 네트워크 구성을 수정할 수 있습니다.

이 예제에서는 네트워크 구성 파일을 C:\AzureNet으로 내보냅니다.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

텍스트 편집기로 파일을 열고 클래식 VNet의 이름을 확인합니다. Azure Portal에서 VNet을 만든 경우 Azure에서 사용되는 전체 이름은 포털에 표시되지 않습니다. 예를 들어 Azure Portal에서 'ClassicVNet1'이라는 이름의 VNet은 네트워크 구성 파일에서 훨씬 더 긴 이름으로 유지될 수 있습니다. 이름은 다음과 같이 표시될 수 있습니다. 'Group ClassicRG1 ClassicVNet1'. 가상 네트워크 이름은 **‘VirtualNetworkSite name =’** 으로 나열됩니다. PowerShell cmdlet을 실행할 때는 네트워크 구성 파일의 이름을 사용해야 합니다.

## <a name="delete"></a>3단계: 가상 네트워크 게이트웨이 삭제

가상 네트워크 게이트웨이를 삭제하면 게이트웨이를 통해 설정된 VNet에 대한 모든 연결이 끊어집니다. P2S 클라이언트를 VNet에 연결한 경우 경고 없이 연결이 끊어집니다.

이 예제에서는 가상 네트워크 게이트웨이를 삭제합니다. 네트워크 구성 파일에서 가상 네트워크의 전체 이름을 사용하는지 확인해야 합니다.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

성공하면 반환되는 출력에 다음이 표시됩니다.

```
Status : Successful
```

## <a name="modify"></a>4단계: 네트워크 구성 파일 수정

가상 네트워크 게이트웨이를 삭제하는 경우 cmdlet에서 네트워크 구성 파일을 수정하지 않습니다. 더 이상 사용 되지 않는 요소를 제거하려면 파일을 수동으로 수정해야 합니다. 다음 섹션에서는 다운로드한 네트워크 구성 파일을 수정하는 방법을 알아봅니다.

### <a name="lnsref"></a>로컬 네트워크 사이트 참조

사이트 참조 정보를 제거하려면 구성을 **ConnectionsToLocalNetwork/LocalNetworkSiteRef**로 변경합니다. 로컬 사이트 참조를 제거하면 Azure에서 터널을 삭제합니다. 만든 구성에 따라 **LocalNetworkSiteRef**가 나열되지 않을 수 있습니다.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

예제:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="lns"></a>로컬 네트워크 사이트 수

더 이상 사용하지 않는 모든 로컬 사이트를 제거합니다. 만든 구성에 따라 **LocalNetworkSite**가 나열되지 않았을 수 있습니다.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

이 예제에서는 Site3만 제거했습니다.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>클라이언트 AddressPool

P2S를 VNet에 연결한 경우 **VPNClientAddressPool**이 있습니다. 삭제한 가상 네트워크 게이트웨이에 해당하는 클라이언트 주소 풀을 제거합니다.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

예제:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>게이트웨이 서브넷

VNet에 해당하는 **GatewaySubnet**을 삭제합니다.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

예제:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>5단계: 네트워크 구성 파일 업로드

변경 내용을 저장하고 Azure에 네트워크 구성 파일을 업로드합니다. 사용자 환경에 필요한 대로 파일 경로를 변경해야 합니다.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

성공한 경우 반환되는 출력에 다음 예제와 유사한 결과가 표시됩니다.

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
