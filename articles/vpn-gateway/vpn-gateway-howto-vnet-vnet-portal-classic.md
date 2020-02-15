---
title: 'Vnet: 클래식: Azure Portal 간의 연결을 만듭니다.'
description: PowerShell 및 Azure Portal을 사용하여 Azure 가상 네트워크를 함께 연결합니다.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201852"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet-VNet 연결(클래식) 구성

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

이 문서는 가상 네트워크 간에 VPN 게이트웨이 연결을 만드는 데 도움이 됩니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다. 이 문서의 단계는 클래식 배포 모델 및 Azure Portal에 적용됩니다. 다른 배포 도구 또는 배포 모델을 사용하는 경우 다음 목록에서 별도의 옵션을 선택하여 이 구성을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal(클래식)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [다양한 배포 모델 간 연결 - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [다양한 배포 모델 간 연결 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![VNet 간 연결 다이어그램](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>VNet 간 연결 정보

VPN 게이트웨이를 사용하여 클래식 배포 모델에서 가상 네트워크를 다른 가상 네트워크(VNet-VNet)에 연결하는 것은 가상 네트워크를 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다.

연결하는 VNet은 서로 다른 구독 및 지역에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수 있습니다. 이렇게 하면 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

![VNet 간 연결](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>가상 네트워크에 연결하는 이유

다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

* **지역 간 지리적 중복 및 지리적 상태**

  * 인터넷 연결 엔드포인트로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
  * Azure 부하 분산 장치 및 Microsoft 또는 타사 클러스터링 기술을 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.
* **분리 경계가 뚜렷한 지역별 다중 계층 애플리케이션**

  * 같은 지역 내에서 뚜렷한 경계를 적용해 여러 VNet이 함께 연결된 다중 계층 애플리케이션을 설정하고 계층 간 통신을 보호할 수 있습니다.
* **Azure의 구독 간/조직 간 통신**

  * Azure 구독이 여러 개인 경우 이제 가상 네트워크 간에 여러 구독의 작업을 안전하게 연결할 수 있습니다.
  * 엔터프라이즈 또는 서비스 공급자의 경우 이제 Azure 내의 보안 VPN 기술을 사용하여 조직 간 통신을 사용하도록 설정할 수 있습니다.

VNet 간 연결에 대한 자세한 내용은 이 문서의 끝에 있는 [VNet 간 고려 사항](#faq)을 참조하세요.

### <a name="powershell"></a>Azure PowerShell 사용

대부분의 단계에서 포털을 사용하지만 PowerShell을 사용하여 VNet 간의 연결을 만들어야 합니다. Azure Portal을 사용하여 연결을 만들 수 없습니다. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="plan"></a>1단계 - IP 주소 범위 계획

가상 네트워크를 구성하는 데 사용할 범위를 결정하는 것이 중요합니다. 이 구성의 경우 VNet 범위가 서로 간에 또는 연결된 로컬 네트워크와 겹치지 않는지 확인해야 합니다.

다음 테이블에는 VNet을 정의하는 방법의 예가 나와 있습니다. 범위는 지침으로만 참고하고, 실제 가상 네트워크에 사용할 범위를 기록합니다. 이 정보는 이후 단계에서 필요합니다.

**예제**

| Virtual Network | 주소 공간 | 지역 | 로컬 네트워크 사이트에 연결 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |미국 동부 |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |미국 서부 |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>2단계 - 가상 네트워크 만들기

[Azure Portal](https://portal.azure.com)에서 두 개의 가상 네트워크를 만듭니다. 클래식 가상 네트워크를 만드는 단계는 [클래식 가상 네트워크 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)를 참조하세요. 

포털을 사용하여 클래식 가상 네트워크를 만들 때 다음 단계를 사용하여 가상 네트워크 페이지로 이동해야 합니다. 그렇지 않으면 가상 네트워크를 만드는 옵션이 표시되지 않습니다.

1. '+'를 클릭하여 '새로 만들기' 페이지를 엽니다.
2. ‘Marketplace 검색’ 필드에 ‘Virtual Network’를 입력합니다. 대신, 네트워킹 -> Virtual Network를 선택한 경우 클래식 VNet을 만드는 옵션이 표시되지 않습니다.
3. 반환된 목록에서 'Virtual Network'를 찾아서 클릭하여 Virtual Network 페이지를 엽니다. 
4. 가상 네트워크 페이지에서 '클래식'을 선택하여 클래식 VNet을 만듭니다. 

이 문서를 연습으로 사용하는 경우 다음 예제 값을 사용할 수 있습니다.

**TestVNet1에 대한 값**

이름: TestVNet1<br>
주소 공간: 10.11.0.0/16, 10.12.0.0/16(선택 사항)<br>
서브넷 이름: 기본값<br>
서브넷 주소 범위: 10.11.0.1/24<br>
리소스 그룹: ClassicRG<br>
위치: 미국 동부<br>
게이트웨이 서브넷: 10.11.1.0/27

**TestVNet4에 대한 값**

이름: TestVNet4<br>
주소 공간: 10.41.0.0/16, 10.42.0.0/16(선택 사항)<br>
서브넷 이름: 기본값<br>
서브넷 주소 범위: 10.41.0.1/24<br>
리소스 그룹: ClassicRG<br>
위치: 미국 서부<br>
게이트웨이 서브넷: 10.41.1.0/27

**VNet을 만드는 경우 다음 설정에 유념하세요.**

* **Virtual Network 주소 공간** – Virtual Network 주소 공간 페이지에서 가상 네트워크에 사용할 주소 범위를 지정합니다. 가상 네트워크에 배포하는 VM 및 다른 역할 인스턴스에 할당될 동적 IP 주소입니다.<br>선택하는 주소 공간은 이 VNet이 연결될 다른 VNet 또는 온-프레미스 위치의 주소 공간과 겹칠 수 없습니다.

* **위치** – 가상 네트워크를 만들 때 Azure 위치(지역)와 연결합니다. 예를 들어 실제로 West US에 배치할 가상 네트워크에 VM을 배포하려는 경우 해당 위치를 선택합니다. 가상 네트워크를 만든 후에는 가상 네트워크에 연결된 위치를 변경할 수 없습니다.

**VNet을 만든 후에는 다음 설정을 추가할 수 있습니다.**

* **주소 공간** – 이 구성에는 주소 공간이 더 필요하지 않지만 VNet을 만든 후 주소 공간을 더 추가할 수 있습니다.

* **서브넷** – 이 구성에는 서브넷이 더 필요하지 않지만 다른 역할 인스턴스와 분리된 서브넷에 VM을 두려는 경우가 있을 수 있습니다.

* **DNS 서버** – DNS 서버 이름 및 IP 주소를 입력합니다. 이 설정은 DNS 서버를 만들지 않습니다. 이렇게 하면 이 가상 네트워크에 대한 이름 확인에 사용하려는 DNS 서버를 지정할 수 있습니다.

이 섹션에서는 연결 형식, 로컬 사이트를 구성하고 게이트웨이를 만듭니다.

## <a name="localsite"></a>3단계 - 로컬 사이트 구성

Azure는 각 로컬 네트워크 사이트에 지정된 설정을 사용하여 VNet 간에 트래픽을 라우팅하는 방법을 결정합니다. 각 VNet은 트래픽을 라우팅하려는 각 로컬 네트워크를 가리켜야 합니다. 각 로컬 네트워크 사이트를 참조하는 데 사용할 이름을 확인합니다. 설명이 포함된 이름을 사용하는 것이 가장 좋습니다.

예를 들어 TestVNet1은 'VNet4Local'이라는 이름으로 만든 로컬 네트워크 사이트에 연결됩니다. VNet4Local에 대한 설정에는 TestVNet4의 주소 접두사가 포함됩니다.

각 VNet의 로컬 사이트는 다른 VNet입니다. 다음 예제 값은 여기의 구성에 사용됩니다.

| Virtual Network | 주소 공간 | 지역 | 로컬 네트워크 사이트에 연결 |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |미국 동부 |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |미국 서부 |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Azure Portal에서 TestVNet1을 찾습니다. 페이지의 **VPN 연결** 섹션에서 **게이트웨이**를 클릭합니다.

    ![게이트웨이 없음](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. **새 VPN 연결** 페이지에서 **사이트 간**을 선택합니다.
3. **로컬 사이트**를 클릭하여 로컬 사이트 페이지를 열고 설정을 구성합니다.
4. **로컬 사이트** 페이지에서 로컬 사이트 이름을 지정합니다. 예제의 경우 로컬 사이트 이름을 'VNet4Local'이라고 지정합니다.
5. **VPN 게이트웨이 IP 주소**의 경우 IP 주소가 올바른 형식이기만 하면 무엇이든 사용할 수 있습니다. 일반적으로는 VPN 디바이스의 실제 외부 IP 주소를 사용합니다. 하지만 클래식 VNet-VNet 구성의 경우 VNet의 게이트웨이에 할당된 공용 IP 주소를 사용합니다. 가상 네트워크 게이트웨이를 아직 만들지 않은 경우 유효한 공용 IP 주소라면 무엇이든 자리 표시자로 지정할 수 있습니다.<br>이 구성에서는 선택 사항이 아니므로 이 항목은 비워두면 안 됩니다. 이후 단계에서 Azure가 해당하는 가상 네트워크 게이트웨이 IP 주소를 생성하고 나면 이 설정으로 돌아와서 IP 주소를 구성합니다.
6. **클라이언트 주소 공간**의 경우 다른 VNet의 주소 공간을 사용합니다. 계획 예제를 참조하세요. **확인**을 클릭하여 설정을 저장하고 **새 VPN 연결** 페이지로 다시 돌아갑니다.

    ![로컬 사이트](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>4단계 - 가상 네트워크 게이트웨이 만들기

각 가상 네트워크에는 가상 네트워크 게이트웨이가 있어야 합니다. 가상 네트워크 게이트웨이는 트래픽을 라우팅하고 암호화합니다.

1. **새 VPN 연결** 페이지에서 **게이트웨이 즉시 만들기** 확인란을 선택합니다.
2. **서브넷, 크기 및 라우팅 유형**을 클릭합니다. **게이트웨이 구성** 페이지에서 **서브넷**을 클릭합니다.
3. 게이트웨이 서브넷 이름이 필요한 이름 'GatewaySubnet'으로 자동으로 채워집니다. **주소 범위**는 VPN 게이트웨이 서비스에 할당된 IP 주소를 포함합니다. 일부 구성은 게이트웨이 서브넷 /29를 허용하지만 게이트웨이 서비스에 IP 주소가 더 필요할 수 있는 향후 구성을 수용하기 위해 /28 또는 /27을 사용하는 것이 가장 좋습니다. 여기의 예제에서는 10.11.1.0/27을 사용합니다. 주소 공간을 조정하고 **확인**을 클릭합니다.
4. **게이트웨이 크기**를 구성합니다. 이 설정은 [게이트웨이 SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.
5. **라우팅 유형**을 구성합니다. 이 구성에 대한 라우팅 유형은 **동적**이어야 합니다. 게이트웨이를 없애고 새로 만들지 않는 한 나중에 라우팅 유형을 변경할 수 있습니다.
6. **확인**을 클릭합니다.
7. **새 VPN 연결** 페이지에서 **확인**을 클릭하여 가상 네트워크 게이트웨이 만들기를 시작합니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

## <a name="vnet4settings"></a>5단계 - TestVNet4 설정 구성

[로컬 사이트 만들기](#localsite)와 [가상 네트워크 게이트웨이 만들기](#gw) 단계를 반복하여 TestVNet4를 구성하고 필요한 경우 값을 대체합니다. 연습에서 수행하는 이 작업을 수행하는 경우 [예제 값](#vnetvalues)을 사용하세요.

## <a name="updatelocal"></a>6단계 - 로컬 사이트 업데이트

가상 네트워크 게이트웨이가 두 VNet에 대해 만들어진 후에는 로컬 사이트 **VPN 게이트웨이IP 주소** 값을 조정해야 합니다.

|VNet 이름|연결된 사이트|게이트웨이 IP 주소|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4의 VPN 게이트웨이 IP 주소|
|TestVNet4|VNet1Local|TestVNet1의 VPN 게이트웨이 IP 주소|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1부 - 가상 네트워크 게이트웨이 공용 IP 주소 가져오기

1. Azure Portal에서 가상 네트워크를 찾습니다.
2. 클릭하여 VNet **개요** 페이지를 엽니다. 페이지의 **VPN 연결**에서 가상 네트워크 게이트웨이의 IP 주소를 볼 수 있습니다.

   ![공용 IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. IP 주소를 복사합니다. 다음 섹션에서 사용하게 됩니다.
4. TestVNet4에 대해 이들 단계를 반복합니다.

### <a name="part-2---modify-the-local-sites"></a>2부 - 로컬 사이트 수정

1. Azure Portal에서 가상 네트워크를 찾습니다.
2. VNet **개요** 페이지에서 로컬 사이트를 클릭합니다.

   ![생성된 로컬 사이트](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. **사이트 간 VPN 연결** 페이지에서 수정할 로컬 사이트의 이름을 클릭합니다.

   ![로컬 사이트 열기](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. 수정할 **로컬 사이트**를 클릭합니다.

   ![사이트 수정](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. **VPN 게이트웨이 IP 주소**를 업데이트하고 **확인**을 클릭하여 설정을 저장합니다.

   ![게이트웨이 IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. 다른 페이지를 닫습니다.
7. TestVNet4에 대해 이들 단계를 반복합니다.

## <a name="getvalues"></a>7단계 - 네트워크 구성 파일에서 값 검색

Azure Portal에서 클래식 VNet을 만드는 경우 볼 수 있는 이름은 PowerShell에 사용한 전체 이름이 아닙니다. 예를 들어 포털에 **TestVNet1**이라는 이름으로 표시되는 VNet은 네트워크 구성 파일에서 훨씬 더 긴 이름이 있을 수 있습니다. 해당 이름은 **Group ClassicRG TestVNet1**과 같은 모양일 수 있습니다. 연결을 만드는 경우 네트워크 구성 파일에 있는 값을 사용하는 것이 중요합니다.

다음 단계에서는 연결에 필요한 값을 확보하기 위해 Azure 계정에 연결하고 네트워크 구성 파일을 다운로드하여 살펴봅니다.

1. 최신 버전의 Azure SM(서비스 관리) PowerShell cmdlet을 다운로드하여 설치합니다. 자세한 내용은 [Azure PowerShell 작업](#powershell)을 참조 하세요.

2. 관리자 권한으로 PowerShell 콘솔을 엽니다. 다음 예제를 사용 하 여 연결을 지원 합니다. PowerShell 서비스 관리 모듈을 사용 하 여 이러한 명령을 로컬로 실행 해야 합니다. 서비스 관리로 전환 하려면 다음 명령을 사용 합니다.

   ```powershell
   azure config mode asm
   ```
3. 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```powershell
   Add-AzureAccount
   ```
4. 계정에 대한 구독을 확인합니다.

   ```powershell
   Get-AzureSubscription
   ```
5. 둘 이상의 구독이 있는 경우 사용할 구독을 선택합니다.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. 네트워크 구성 파일을 내보내고 및 살펴봅니다. 컴퓨터에 디렉터리를 만들고 디렉터리에 네트워크 구성 파일을 내보냅니다. 이 예제에서는 네트워크 구성 파일을 **C:\AzureNet**으로 내보냅니다.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. 텍스트 편집기에서 파일을 열고 VNet과 사이트의 이름을 확인합니다. 이러한 이름은 연결을 만들 때 사용 하는 이름이 됩니다.<br>VNet 이름은 **VirtualNetworkSite name =** 으로 나열됩니다.<br>사이트 이름은 **LocalNetworkSiteRef name =** 으로 나열됩니다.

## <a name="createconnections"></a>8단계 - VPN 게이트웨이 연결 만들기

위의 모든 단계를 완료한 후에는 IPsec/IKE 사전 공유 키를 설정하고 연결을 만들 수 있습니다. 이번 단계에서는 PowerShell을 사용합니다. 클래식 배포 모델을 위한 VNet-VNet 연결은 Azure Portal에서 구성할 수 없습니다.

아래 예제에서 공유 키가 정확히 동일한지 확인합니다. 공유 키는 항상 일치해야 합니다. 이 예제의 값을 VNet과 로컬 네트워크 사이트의 정확한 이름으로 변경해야 합니다.

1. TestVNet1 대 TestVNet4 연결을 만듭니다.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. TestVNet4 대 TestVNet1 연결을 만듭니다.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. 연결이 초기화될 때까지 기다립니다. 게이트웨이가 초기화된 후에는 상태가 ‘성공’입니다.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>클래식 VNet에 대한 VNet-VNet 고려 사항
* 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독에 있을 수도 있습니다.
* 가상 네트워크는 같은 Azure 지역(위치)에 있을 수도 있고 다른 Azure 지역(위치)에 있을 수도 있습니다.
* 클라우드 서비스 또는 부하 분산 끝점은 서로 연결 되어 있더라도 가상 네트워크에 걸쳐 있을 수 없습니다.
* 여러 가상 네트워크를 연결할 때 VPN 디바이스는 필요하지 않습니다.
* VNet 간 연결은 Azure Virtual Network 연결을 지원합니다. 그러나 가상 네트워크에 배포되지 않은 가상 머신 또는 클라우드 서비스 연결은 지원하지 않습니다.
* VNet-VNet을 위해서는 동적 라우팅 게이트웨이가 필요합니다. Azure 정적 라우팅 게이트웨이는 지원되지 않습니다.
* 가상 네트워크 연결을 다중 사이트 VPN과 동시에 사용할 수 있습니다. 가상 네트워크 VPN 게이트웨이당 최대 10개의 VPN 터널을 다른 가상 네트워크 또는 온-프레미스 사이트에 연결할 수 있습니다.
* 가상 네트워크 및 온-프레미스 로컬 네트워크 사이트의 주소 공간이 겹쳐서는 안 됩니다. 주소 공간이 겹치면 가상 네트워크 만들기 또는 netcfg 구성 파일 업로드가 실패합니다.
* 가상 네트워크 한 쌍 간의 중복 터널은 지원되지 않습니다.
* P2S VPN을 비롯한 VNet의 모든 VPN 터널은 VPN 게이트웨이의 사용 가능한 대역폭 및 Azure의 동일 VPN 게이트웨이 작동 시간 SLA를 공유합니다.
* VNet-VNet 트래픽은 Azure 백본 전체에서 이동됩니다.

## <a name="next-steps"></a>다음 단계
연결을 확인합니다. [VPN Gateway 연결 확인](vpn-gateway-verify-connection-resource-manager.md)을 참조하세요.
