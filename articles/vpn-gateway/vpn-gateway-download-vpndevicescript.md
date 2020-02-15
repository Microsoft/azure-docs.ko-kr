---
title: S2S VPN 연결에 대한 VPN 디바이스 구성 스크립트 다운로드
description: 이 문서에서는 Azure Resource Manager를 사용하여 Azure VPN Gateway와의 S2S VPN 연결에 대한 VPN 디바이스 구성 스크립트를 다운로드하는 과정을 안내합니다.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162139"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>S2S VPN 연결에 대한 VPN 디바이스 구성 스크립트 다운로드

이 문서에서는 Azure Resource Manager를 사용하여 Azure VPN Gateway와의 S2S VPN 연결에 대한 VPN 디바이스 구성 스크립트를 다운로드하는 과정을 안내합니다. 다음 다이어그램은 개괄적인 워크플로를 보여 줍니다.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

다음 디바이스는 사용 가능한 스크립트를 포함하고 있습니다.

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>VPN 디바이스 구성 스크립트 정보

크로스-프레미스 VPN 연결은 Azure VPN Gateway, 온-프레미스 VPN 디바이스 및 이 둘을 연결하는 IPsec S2S VPN 터널로 구성됩니다. 일반적인 워크플로에는 다음 단계가 포함됩니다.

1. Azure VPN Gateway(Virtual Network Gateway) 만들기 및 구성
2. 온-프레미스 네트워크 및 VPN 디바이스를 나타내는 Azure 로컬 네트워크 게이트웨이 만들기 및 구성
3. Azure VPN Gateway와 로컬 네트워크 게이트웨이 간에 Azure VPN 연결 만들기 및 구성
4. Azure VPN Gateway와의 실제 S2S VPN 터널을 설정하도록 로컬 네트워크 게이트웨이가 나타내는 온-프레미스 VPN 디바이스 구성

Azure [Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) 또는 [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)를 사용하여 1~3단계를 완료할 수 있습니다. 마지막 단계에서는 Azure 외부의 온-프레미스 VPN 디바이스가 구성됩니다. 이 기능을 사용하면 Azure VPN Gateway, 가상 네트워크 및 온-프레미스 네트워크 주소 접두사의 해당 값과 VPN 연결 속성 등이 미리 채워진 상태로 VPN 디바이스의 구성 스크립트를 다운로드할 수 있습니다. 이 스크립트를 시작점으로 사용하거나, 구성 콘솔을 통해 온-프레미스 VPN 디바이스에 직접 스크립트를 적용할 수 있습니다.

> [!IMPORTANT]
> * VPN 디바이스 구성 스크립트마다 구문이 다르며, 모델 및 펌웨어 버전에 따라 크게 달라집니다. 사용 가능한 템플릿을 참조하여 디바이스 모델 및 버전 정보를 특히 주의해서 확인합니다.
> * 일부 매개 변수 값은 디바이스에서 고유하므로 디바이스에 액세스해야만 알 수 있습니다. Azure에서 생성된 구성 스크립트는 이러한 값을 미리 채우지만 제공된 값이 디바이스에서 유효한지 확인해야 합니다. 예:
>    * 인터페이스 번호
>    * 액세스 제어 목록 번호
>    * 정책 이름 또는 번호, 등
> * 스크립트에 포함된 키워드 "**REPLACE**"를 찾은 후 스크립트를 적용하기 전에 확인해야 하는 매개 변수를 찾습니다.
> * 일부 템플릿에는 구성을 제거하기 위해 적용할 수 있는 "**CLEANUP**" 섹션이 포함되어 있습니다. 정리 섹션은 기본적으로 주석으로 처리됩니다.

## <a name="download-the-configuration-script-from-azure-portal"></a>Azure Portal에서 구성 스크립트 다운로드

Azure VPN Gateway, 로컬 네트워크 게이트웨이 및 이 둘을 연결하는 연결 리소스를 만듭니다. 다음 페이지에서 해당 단계를 안내합니다.

* [Azure Portal에서 사이트 간 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

연결 리소스를 만든 후 아래의 지시에 따라 VPN 디바이스 구성 스크립트를 다운로드합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 만든 연결 리소스로 이동합니다. "모든 서비스"를 클릭한 다음 "네트워킹" 및 "연결"을 클릭하여 모든 연결 리소스의 목록을 찾을 수 있습니다.

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. 구성할 연결을 클릭합니다.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. 연결 개요 페이지에서 빨간색으로 강조 표시된 "구성 다운로드" 링크를 클릭합니다. 그러면 "구성 다운로드" 페이지가 열립니다.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. VPN 디바이스에 대한 모델 제품군 및 펌웨어 버전을 선택하고 "구성 다운로드" 단추를 클릭합니다.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. 브라우저에서 다운로드한 스크립트(텍스트 파일)를 저장하라는 메시지가 표시됩니다.
7. 구성 스크립트를 다운로드한 다음, 텍스트 편집기로 열고 "REPLACE" 키워드를 검색하여 바꿔야 할 수 있는 매개 변수를 검토합니다.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Azure PowerShell을 사용하여 구성 스크립트 다운로드



또한 다음 예제와 같이 Azure PowerShell을 사용하여 구성 스크립트를 다운로드할 수 있습니다.

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>VPN 디바이스에 구성 스크립트 적용

구성 스크립트를 다운로드하고 유효성을 검사한 후에는 VPN 디바이스에 스크립트를 적용해야 합니다. 실제 절차는 VPN 디바이스 제조업체 및 모델에 따라 다릅니다. VPN 디바이스에 대한 작업 설명서 또는 지침 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

[사이트 간 연결](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 구성 작업을 계속 진행합니다.
