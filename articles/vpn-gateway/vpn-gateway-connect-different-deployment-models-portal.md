---
title: 'Azure Resource Manager VNet에 클래식 가상 네트워크를 연결하는 방법: 포털 | Microsoft Docs'
description: VPN Gateway 및 포털을 사용하여 클래식 VNet을 Resource Manager VNet에 연결하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: bf7d80bbbe63204cda47719a7d7c019013ad800b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124034"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>포털을 사용하여 다양한 배포 모델에서 가상 네트워크 연결

이 문서에서는 Resource Manager VNet에 클래식 VNet을 연결하여 별도의 배포 모델에 있는 리소스가 서로 통신하도록 허용하는 방법을 보여 줍니다. 이 문서의 단계는 주로 Azure Portal을 사용하지만 이 목록에서 문서를 선택하여 PowerShell을 통해 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [포털](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

클래식 VNet을 Resource Manager VNet에 연결하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 다른 구독 및 다른 지역에 있는 VNet 간에 연결을 만들 수 있습니다. 또한 함께 구성된 게이트웨이가 동적이거나 경로 기반이면 온-프레미스 네트워크에 이미 연결된 VNet을 연결할 수 있습니다. VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 FAQ](#faq)를 참조하세요. 

가상 네트워크 게이트웨이가 아직 없으며 만들지 않으려면 VNet 피어링을 사용하여 VNet을 연결하는 것이 좋습니다. VNet 피어링은 VPN Gateway를 사용하지 않습니다. 자세한 내용은 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

### <a name="before"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* 이 단계에서는 두 VNet이 이미 만들어졌다고 가정합니다. 이 문서를 사용하여 연습을 하는 경우 VNet이 없으면 단계 내에 VNet 만들기를 도와주는 링크가 있습니다.
* VNet에 대한 주소 범위가 서로 겹치거나 게이트웨이가 연결되어 있는 다른 연결의 범위와 겹치지 않는지 확인합니다.
* Resource Manager와 Service Management(클래식)에 대해 최신 PowerShell cmdlet을 설치합니다. 이 문서에서는 Azure Portal 및 PowerShell을 모두 사용합니다. PowerShell은 클래식 VNet에서 Resource Manager VNet에 연결을 만드는 데 필요합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 

### <a name="values"></a>예제 설정

이러한 값을 사용하여 테스트 환경을 만들거나 이 값을 참조하여 이 문서의 예제를 더 잘 이해할 수 있습니다.

**클래식 VNet**

VNet 이름 = ClassicVNet <br>
Address space = 10.0.0.0/24 <br>
서브넷 이름 = Subnet-1 <br>
서브넷 주소 범위 = 10.0.0.0/27 <br>
구독 = 사용할 구독 <br>
Resource Group = ClassicRG <br>
Location = West US <br>
GatewaySubnet = 10.0.0.32/28 <br>
Local site = RMVNetLocal <br>

**Resource Manager VNet**

VNet 이름 = RMVNet <br>
Address space = 192.168.0.0/16 <br>
Resource Group = RG1 <br>
Location = East US <br>
서브넷 이름 = Subnet-1 <br>
주소 범위 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Virtual network gateway name = RMGateway <br>
Gateway type = VPN <br>
VPN type = Route-based <br>
SKU = VpnGw1 <br>
Location = East US <br>
가상 네트워크 = RMVNet <br> (VPN 게이트웨이를 이 VNet에 연결) 첫 번째 IP 구성 = rmgwpip <br> (게이트웨이 공용 IP 주소) 로컬 네트워크 게이트웨이 = ClassicVNetLocal <br>
연결 이름 = RMtoClassic

### <a name="connectoverview"></a>연결 개요

이 구성에 대해 가상 네트워크 사이에 IPsec/IKE VPN 터널을 통해 VPN 게이트웨이 연결을 만듭니다. VNet 범위가 서로 간에 또는 연결된 로컬 네트워크와 겹치지 않는지 확인해야 합니다.

다음 테이블은 예제 VNet 및 로컬 사이트가 어떻게 정의되는지 보여줍니다.

| Virtual Network | 주소 공간 | 지역 | 로컬 네트워크 사이트에 연결 |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |미국 서부 | RMVNetLocal(192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |미국 동부 |ClassicVNetLocal(10.0.0.0/24) |

## <a name="classicvnet"></a>섹션 1 - 클래식 VNet 설정 구성

이 섹션에서는 클래식 VNet, 로컬 네트워크(로컬 사이트) 및 가상 네트워크 게이트웨이를 만듭니다. 스크린샷은 예제로 제공됩니다. 값을 사용자의 값으로 대체하거나 [예제](#values) 값을 사용합니다.

### 1. <a name="classicvnet"></a>클래식 VNet 만들기

클래식 VNet이 없는 상태에서 이 단계를 연습으로 실행하는 경우에는 [이 문서](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)와 위의 [예제](#values) 설정 값을 사용하여 VNet을 만들 수 있습니다.

VPN 게이트웨이가 있는 VNet이 이미 있는 경우 해다 게이트웨이가 동적인지 확인합니다. 정적인 경우 [로컬 사이트를 구성](#local)하기 전에 먼저 VPN 게이트웨이를 삭제해야 합니다.

1. [Azure 포털](https://ms.portal.azure.com)에서 Azure 계정으로 로그인합니다.
2. **+ 리소스 만들기**를 클릭하여 '새' 페이지를 엽니다.
3. ‘Marketplace 검색’ 필드에 ‘Virtual Network’를 입력합니다. 대신, 네트워킹 -> Virtual Network를 선택한 경우 클래식 VNet을 만드는 옵션이 표시되지 않습니다.
4. 반환된 목록에서 'Virtual Network'를 찾아서 클릭하여 Virtual Network 페이지를 엽니다. 
5. 가상 네트워크 페이지에서 '클래식'을 선택하여 클래식 VNet을 만듭니다. 여기에서 기본값을 사용하면, 대신 Resource Manager VNet을 사용하게 됩니다.

### 2. <a name="local"></a>로컬 사이트 구성

1. **모든 리소스**로 이동하여 목록에서 **ClassicVNet**을 찾습니다.
2. **개요** 페이지의 **VPN 연결** 섹션에서 **게이트웨이**를 클릭하여 게이트웨이를 만듭니다.
  ![VPN 게이트웨이 구성](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN 게이트웨이 구성")
3. **새 VPN 연결** 페이지에서 **연결 유형**으로 **사이트 간**을 선택합니다.
4. **로컬 사이트**로 **필요한 설정 구성**을 클릭합니다. 그러면 **로컬 사이트** 페이지가 열립니다.
5. **로컬 사이트** 페이지에서 리소스 관리자 VNet을 언급하는 이름을 만듭니다. 예: 'RMVNetLocal'.
6. Resource Manager VNet에 대한 VPN 게이트웨이에 공용 IP 주소가 이미 있는 경우 **VPN 게이트웨이 IP 주소** 필드의 값을 사용합니다. 이 단계를 연습으로 수행하는 경우 또는 아직 Resource Manager VNet에 대한 가상 네트워크 게이트웨이가 없는 경우 자리 표시자 IP 주소를 만들 수 있습니다. 자리 표시자 IP 주소 형식이 올바른지 확인합니다. 나중에 자리 표시자 IP 주소를 Resource Manager 가상 네트워크 게이트웨이의 공용 IP 주소로 바꿉니다.
7. **클라이언트 주소 공간**에 Resource Manager VNet에 대한 가상 네트워크 IP 주소 공간 [값](#connectoverview)을 사용합니다. 이 설정은 Resource Manager 가상 네트워크로 라우팅할 주소 공간을 지정하는 데 사용합니다. 이 예에서는 RMVNet의 주소 범위인 192.168.0.0/16을 사용합니다.
8. **확인**을 클릭하여 값을 저장하고 **새 VPN 연결** 페이지로 돌아갑니다.

### <a name="classicgw"></a>3. 가상 네트워크 게이트웨이 만들기

1. **새 VPN 연결** 페이지에서 **게이트웨이 즉시 만들기** 확인란을 선택합니다.
2. **선택적 게이트웨이 구성**을 클릭하여 **게이트웨이 구성** 페이지를 엽니다.

   ![게이트웨이 구성 페이지 열기](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "게이트웨이 구성 페이지 열기")
3. **서브넷 - 필요한 설정 구성**을 클릭하여 **서브넷 추가** 페이지를 엽니다. **이름**이 다음 필수 값으로 이미 구성되어 있습니다. **GatewaySubnet**.
4. **주소 범위**는 게이트웨이 서브넷에 대한 범위를 나타냅니다. 게이트웨이 서브넷을 /29 주소 범위(주소 3개)로 만들 수 있지만 더 많은 IP 주소를 포함하는 게이트웨이 서브넷을 만드는 것이 좋습니다. 그러면 사용 가능한 IP 주소가 필요할 수 있는 향후 구성을 수용하게 됩니다. 가능하면 /27 또는 /28을 사용합니다. 이 단계를 연습으로 사용하는 경우 다음 [예제 값](#values)을 참조할 수 있습니다. 이 예제에서는 '10.0.0.32/28'을 사용합니다. **확인**을 클릭하여 게이트웨이 서브넷을 만듭니다.
5. **게이트웨이 구성** 페이지에서 **크기**는 게이트웨이 SKU를 나타냅니다. VPN 게이트웨이에 대한 게이트웨이 SKU를 선택합니다.
6. **라우팅 유형**이 **동적**인지 확인하고 **확인**을 클릭하여 **새 VPN 연결** 페이지로 돌아갑니다.
7. **새 VPN 연결** 페이지에서 **확인**을 클릭하여 VPN 게이트웨이 만들기를 시작합니다. VPN 게이트웨이 만들기를 완료하는 데 최대 45분이 걸릴 수 있습니다.

### <a name="ip"></a>4. 가상 네트워크 게이트웨이 공용 IP 주소 복사

가상 네트워크 게이트웨이를 만든 후에 게이트웨이 IP 주소를 볼 수 있습니다. 

1. 클래식 VNet으로 이동한 후 **개요**를 클릭합니다.
2. **VPN 연결**을 클릭하여 VPN 연결 페이지를 엽니다. VPN 연결 페이지에서 공용 IP 주소를 볼 수 있습니다. 이 주소는 가상 네트워크 게이트웨이에 할당된 공용 IP 주소입니다. IP 주소를 기록해 둡니다. 나중 단계에서 Resource Manager 로컬 네트워크 게이트웨이 구성 설정 관련 작업을 수행할 때 사용합니다. 
3. 게이트웨이 연결의 상태를 볼 수 있습니다. 만든 로컬 네트워크 사이트는 '연결 중'으로 표시됩니다. 연결을 만든 후에는 상태가 변경됩니다. 상태 보기를 마치면 이 페이지를 닫을 수 있습니다.

## <a name="rmvnet"></a>섹션 2 - Resource Manager VNet 설정 구성

이 섹션에서는 Resource Manager VNet에 대한 가상 네트워크 게이트웨이 및 로컬 네트워크 게이트웨이를 만듭니다. 스크린샷은 예제로 제공됩니다. 값을 사용자의 값으로 대체하거나 [예제](#values) 값을 사용합니다.

### <a name="1-create-a-virtual-network"></a>1. 가상 네트워크 만들기

**예제 값:**

* VNet 이름 = RMVNet <br>
* Address space = 192.168.0.0/16 <br>
* Resource Group = RG1 <br>
* Location = East US <br>
* 서브넷 이름 = Subnet-1 <br>
* 주소 범위 = 192.168.1.0/24 <br>


Resource Manager VNet이 없는 상태에서 이러한 단계를 연습으로 실행하는 경우에는 예제 값을 사용하여 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md)의 단계에 따라 가상 네트워크를 만듭니다.

### <a name="2-create-a-gateway-subnet"></a>2. 게이트웨이 서브넷 만들기

**예제 값:** GatewaySubnet = 192.168.0.0/26

가상 네트워크 게이트웨이를 구성하려면 먼저 게이트웨이 서브넷을 만들어야 합니다. CIDR 개수가 /28 이상인 게이트웨이 서브넷을 만듭니다(/27, /26 등). 연습의 일환으로 이것을 만드는 경우 예제 값을 사용할 수 있습니다.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. 가상 네트워크 게이트웨이 만들기

**예제 값:**

* Virtual network gateway name = RMGateway <br>
* Gateway type = VPN <br>
* VPN type = Route-based <br>
* SKU = VpnGw1 <br>
* Location = East US <br>
* 가상 네트워크 = RMVNet <br>
* 첫 번째 IP 구성 = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. 로컬 네트워크 게이트웨이 만들기

**예제 값:** Local network gateway = ClassicVNetLocal

| Virtual Network | 주소 공간 | 지역 | 로컬 네트워크 사이트에 연결 |게이트웨이 공용 IP 주소|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |미국 서부 | RMVNetLocal(192.168.0.0/16) |ClassicVNet 게이트웨이에 할당된 공용 IP 주소|
| RMVNet | (192.168.0.0/16) |미국 동부 |ClassicVNetLocal(10.0.0.0/24) |RMVNet 게이트웨이에 할당된 공용 IP 주소|

로컬 네트워크 게이트웨이는 클래식 VNet 및 가상 네트워크 게이트웨이에 연결된 주소 범위 및 공용 IP 주소를 지정합니다. 이 단계를 연습으로 수행하는 경우 예제 값을 참조하세요.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>섹션 3 - 클래식 VNet 로컬 사이트 설정 수정

이 섹션에서는 로컬 사이트 설정을 지정할 때 사용한 자리 표시자 IP 주소를 Resource Manager VPN 게이트웨이 IP 주소와 바꿉니다. 이 섹션에서는 클래식(SM) PowerShell cmdlet을 사용합니다.

1. Azure Portal에서 클래식 가상 네트워크로 이동합니다.
2. 가상 네트워크에 대한 페이지에서 **개요**를 클릭합니다.
3. **VPN 연결** 섹션에서 그래픽의 로컬 사이트 이름을 클릭합니다.

   ![VPN-connections](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN 연결")
4. **사이트 간 VPN 연결** 페이지에서 사이트의 이름을 클릭합니다.

   ![Site-name](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "로컬 사이트 이름")
5. 로컬 사이트에 대한 연결 페이지에서 로컬 사이트의 이름을 클릭하여 **로컬 사이트** 페이지를 엽니다.

   ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "로컬 사이트 열기")
6. **로컬 사이트** 페이지에서 **VPN 게이트웨이 IP 주소**를 리소스 관리자 게이트웨이의 IP 주소로 바꿉니다.

   ![Gateway-ip-address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "게이트웨이 IP 주소")
7. **확인**을 클릭하여 IP 주소를 업데이트합니다.

## <a name="RMtoclassic"></a>섹션 4 - 클래식 연결에 대해 Resource Manager 만들기

이번 단계에서는 Azure Portal을 사용하여 Resource Manager VNet에서 클래식 VNet으로 연결을 구성합니다.

1. **모든 리소스**에서 로컬 네트워크 게이트웨이를 찾습니다. 이 예제의 경우 로컬 네트워크 게이트웨이는 **ClassicVNetLocal**입니다.
2. **구성**을 클릭하여 IP 주소 값이 클래식 VNet에 대한 VPN 게이트웨이인지 확인합니다. 필요한 경우 업데이트하고 **저장**을 클릭합니다. 페이지를 닫습니다.
3. **모든 리소스**에서 로컬 네트워크 게이트웨이를 클릭합니다.
4. **연결**을 클릭하여 연결 페이지를 엽니다.
5. **연결** 페이지에서 **+** 를 클릭하여 연결을 추가합니다.
6. **연결 추가** 페이지에서 연결의 이름을 지정합니다. 예: 'RMtoClassic'.
7. **사이트 간**이 페이지에 이미 선택되어 있습니다.
8. 이 사이트와 연결할 가상 네트워크 게이트웨이를 선택합니다.
9. **공유 키**를 만듭니다. 이 키는 클래식 VNet에서 Resource Manager VNet에 만드는 연결에도 사용됩니다. 키를 생성하거나 만들 수 있습니다. 이 예제에서는 'abc123'을 사용했지만 좀 더 복잡한 항목을 사용할 수 있고 사용해야 합니다.
10. **확인**을 클릭하여 연결을 만듭니다.

## <a name="classictoRM"></a>섹션 5 - 클래식에서 Resource Manager 연결 만들기

이번 단계에서는 클래식 VNet에서 Resource Manager VNet까지 연결을 구성합니다. 이러한 단계에는 PowerShell이 필요합니다. 포털에서는 이 연결을 만들 수 없습니다. 클래식(SM) 및 Resource Manager(RM) PowerShell cmdlet을 모두 다운로드하고 설치해야 합니다.

### <a name="1-connect-to-your-azure-account"></a>1. Azure 계정에 연결

관리자 권한으로 PowerShell 콘솔을 열고 Azure 계정에 로그인합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정이 다운로드됩니다. 다음 cmdlet은 Resource Manager 배포 모델의 Azure 계정에 대한 로그인 자격 증명을 묻습니다.

```powershell
Connect-AzAccount
```

Azure 구독 목록을 가져옵니다.

```powershell
Get-AzSubscription
```

둘 이상의 구독이 있는 경우 사용할 구독을 지정합니다.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

다음으로, 클래식 PowerShell cmdlet(Service Management)을 사용하려면 로그인합니다. 클래식 배포 모델의 Azure 계정을 추가하려면 다음 명령을 사용합니다.

```powershell
Add-AzureAccount
```

구독 목록을 가져옵니다. Azure 모듈 설치에 따라 Service Management cmdlet을 추가할 때 이 단계가 필요할 수 있습니다.

```powershell
Get-AzureSubscription
```

둘 이상의 구독이 있는 경우 사용할 구독을 지정합니다.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. 네트워크 구성 파일 값 확인

Azure Portal에서 VNet을 만든 경우 Azure에서 사용되는 전체 이름은 Azure Portal에 표시되지 않습니다. 예를 들어 Azure Portal에서 'ClassicVNet'이라는 이름으로 표시되는 VNet은 네트워크 구성 파일에 훨씬 더 긴 이름이 있을 수 있습니다. 이름은 다음과 같이 표시될 수 있습니다. 'Group ClassicRG ClassicVNet'. 이 단계에서는 네트워크 구성 파일을 다운로드하고 값을 살펴봅니다.

컴퓨터에 디렉터리를 만들고 디렉터리에 네트워크 구성 파일을 내보냅니다. 이 예제에서는 네트워크 구성 파일을 C:\AzureNet으로 내보냅니다.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

텍스트 편집기로 파일을 열고 클래식 VNet의 이름을 확인합니다. PowerShell cmdlet을 실행할 때는 네트워크 구성 파일의 이름을 사용해야 합니다.

- VNet 이름은 **VirtualNetworkSite name =** 으로 나열됩니다.
- 사이트 이름은 **LocalNetworkSite name =** 으로 나열됩니다.

### <a name="3-create-the-connection"></a>3. 연결 만들기

공유 키를 설정하고 클래식 VNet에서 Resource Manager VNet으로 연결을 만듭니다. 포털을 사용하여 공유 키를 설정할 수 없습니다. 클래식 버전의 PowerShell cmdlet을 사용하여 로그인한 상태에서 이러한 단계를 실행해야 합니다. 이 작업에는 **Add-AzureAccount**를 사용합니다. 그러지 않으면 '-AzureVNetGatewayKey'를 설정할 수 없습니다.

- 이 예제에서 **-VNetName**은 클래식 VNet의 이름이며 네트워크 구성 파일에 있습니다. 
- **-LocalNetworkSiteName**은 로컬 사이트에 대해 지정한 이름이며 네트워크 구성 파일에 있습니다.
- **-SharedKey**는 사용자가 생성하고 지정하는 값입니다. 이 예제에서는 *abc123*을 사용했으나 좀 더 복잡한 항목을 생성할 수 있습니다. 중요한 점은 여기에서 지정한 값이 클래식 연결에 대해 Resource Manager를 만들 때 지정한 값과 동일해야 한다는 것입니다.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>섹션 6 - 연결 확인

Azure Portal 또는 PowerShell을 사용하여 연결을 확인할 수 있습니다. 확인 시, 연결이 만들어지는 동안 1~2분 정도 기다려야 할 수 있습니다. 연결이 완료되면 연결 상태가 '연결 중'에서 '연결됨'으로 변경됩니다.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>클래식 VNet에서 Resource Manager VNet으로의 연결을 확인하려면

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Resource Manager VNet에서 클래식 VNet으로의 연결을 확인하려면

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>VNet 간 FAQ

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
