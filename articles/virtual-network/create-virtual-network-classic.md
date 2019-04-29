---
title: 여러 서브넷이 있는 Azure Virtual Network(클래식) 만들기 | Microsoft Docs
description: Azure에서 여러 서브넷이 있는 가상 네트워크(클래식)를 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: e40648ef47b108050486d43eefdb1564786c053e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098119"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>여러 서브넷이 있는 가상 네트워크(클래식) 만들기

> [!IMPORTANT]
> Azure에는 두 개의 [다양 한 배포 모델](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 만들고 리소스를 사용 하 여 작업 합니다. Resource Manager 및 클래식 합니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. [Resource Manager](quick-create-portal.md) 배포 모델을 통해 최신 가상 네트워크를 만드는 것이 좋습니다.

이 자습서에서는 별도의 공용 및 개인 서브넷이 있는 기본 Azure Virtual Network(클래식)를 만드는 방법에 대해 알아봅니다. Virtual Machines 및 서브넷에 있는 Cloud Services와 같은 Azure 리소스를 만들 수 있습니다. 가상 네트워크(클래식)에서 생성된 리소스는 서로 간에 통신할 수 있으며 가상 네트워크에 연결된 다른 네트워크의 리소스와도 통신할 수 있습니다.

모든 [가상 네트워크](manage-virtual-network.md) 및 [서브넷](virtual-network-manage-subnet.md) 설정에 대해 자세히 확인하세요.

> [!WARNING]
> [구독이 비활성화되는](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit) 경우 Azure에서 가상 네트워크(클래식)가 즉시 삭제됩니다. 가상 네트워크(클래식)는 리소스가 가상 네트워크에 존재하는지 여부에 관계 없이 삭제됩니다. 구독을 나중에 다시 사용하는 경우 가상 네트워크에 존재하는 리소스를 다시 만들어야 합니다.

[Azure Portal](#portal), [Azure CLI(명령줄 인터페이스) 1.0](#azure-cli) 또는 [PowerShell](#powershell)을 사용하여 가상 네트워크(클래식)를 만들 수 있습니다.

## <a name="portal"></a>포털

1. 인터넷 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다. [Azure 계정](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)을 사용하여 로그인합니다. Azure 계정이 없으면 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p)에 등록할 수 있습니다.
2. 포털에서 **리소스 만들기**를 클릭합니다.
3. 표시되는 **새로 만들기** 창의 맨 위에 있는 **Marketplace 검색** 상자에 *가상 네트워크*를 입력합니다. **가상 네트워크**가 검색 결과에 표시되면 클릭합니다.
4. 표시되는 **Virtual Network** 창의 **배포 모델 선택** 상자에서 **클래식**을 선택하고 **만들기**를 클릭합니다. 
5. **가상 네트워크 만들기(클래식)** 창에서 다음 값을 입력하고 **만들기**를 클릭합니다.

    |설정|값|
    |---|---|
    |이름|myVnet|
    |주소 공간|10.0.0.0/16|
    |서브넷 이름|공용|
    |서브넷 주소 범위|10.0.0.0/24|
    |리소스 그룹|**새로 만들기**를 선택한 채로 두고 **myResourceGroup**을 입력합니다.|
    |구독 및 위치|구독 및 위치를 선택합니다.

    Azure를 처음 사용하는 경우 [리소스 그룹](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 [위치](https://azure.microsoft.com/regions)(*지역*이라고도 함)에 대해 자세히 알아봅니다.
4. 포털에서 가상 네트워크를 만들 때 서브넷을 하나만 만들 수 있습니다. 이 자습서에서는 가상 네트워크를 만든 후 두 번째 서브넷을 만듭니다. 나중에 인터넷에서 액세스할 수 있는 리소스를 **공용** 서브넷에 만들 수 있습니다. 또한 **개인** 서브넷에는 인터넷에서 액세스할 수 없는 리소스를 만들 수 있습니다. 두 번째 서브넷을 만들려면 페이지 위쪽의 **리소스 검색** 상자에 **myVnet**을 입력합니다. **myVnet**이 검색 결과에 표시되면 클릭합니다.
5. 표시되는 **가상 네트워크 만들기(클래식)** 창에서 **서브넷**(**설정** 섹션)을 클릭합니다.
6. 표시되는 **myVnet - 서브넷** 창에서 **+추가**를 클릭합니다.
7. **서브넷 추가** 창에서 **이름**에 **개인**을 입력합니다. **주소 범위**에 **10.0.1.0/24**를 입력합니다.  **확인**을 클릭합니다.
8. **myVnet - 서브넷** 창에서 직접 만든 **공용** 및 **개인** 서브넷을 확인할 수 있습니다.
9. **선택 사항**: 이 자습서를 마친 후에 사용 요금이 발생 하지 않도록 작성 했던 리소스를 삭제 하는 것이 좋습니다.
    - **myVnet** 창에서 **개요**를 클릭합니다.
    - **myVnet** 창에서 **삭제** 아이콘을 클릭합니다.
    - 삭제를 확인하려면 **가상 네트워크 삭제** 상자에서 **예**를 클릭합니다.

## <a name="azure-cli"></a>Azure CLI

1. [Azure CLI를 설치 및 구성](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하거나 Azure Cloud Shell 내에서 CLI를 사용할 수 있습니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. CLI 명령에 대한 도움말을 보려면 `azure <command> --help`를 입력합니다. 
2. CLI 세션에서 다음에 나오는 명령을 사용하여 Azure에 로그인합니다. 아래 상자에서 **시도**를 클릭하면 Cloud Shell이 열립니다. 다음 명령을 입력하지 않고도 Azure 구독에 로그인할 수 있습니다.

    ```azurecli-interactive
    azure login
    ```

3. CLI가 서비스 관리 모드인지 확인하려면 다음 명령을 입력합니다.

    ```azurecli-interactive
    azure config mode asm
    ```

4. 개인 서브넷을 사용하는 가상 네트워크를 만듭니다.

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. 가상 네트워크 내에서 공용 서브넷을 만듭니다.

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. 가상 네트워크 및 서브넷을 검토합니다.

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **선택 사항**: 사용량 요금이 발생 하지 않도록이 자습서를 마친 후 만든 리소스를 삭제 하는 것이 좋습니다.

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> CLI를 사용하여 가상 네트워크(클래식)를 만들도록 리소스 그룹을 지정할 수 없긴 하지만, Azure에서 *기본 네트워킹*이라는 리소스 그룹에 가상 네트워크를 만듭니다.

## <a name="powershell"></a>PowerShell

1. 최신 버전의 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 모듈을 설치합니다. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 개요](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
2. PowerShell 세션을 시작합니다.
3. PowerShell에서 `Add-AzureAccount` 명령을 입력하여 Azure에 로그인합니다.
4. 다음 경로 및 파일 이름을 적절하게 변경한 다음, 기존 네트워크 구성 파일을 내보냅니다.

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. 공용 및 개인 서브넷을 사용하여 가상 네트워크를 만들려면 텍스트 편집기를 사용하여 다음에 나오는 **VirtualNetworkSite** 요소를 네트워크 구성 파일에 추가합니다.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    전체 [네트워크 구성 파일 스키마](https://msdn.microsoft.com/library/azure/jj157100.aspx)를 검토합니다.

6. 네트워크 구성 파일을 가져옵니다.

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > 변경된 네트워크 구성 파일을 가져오면 구독의 기존 가상 네트워크가 변경될 수 있습니다. 이전 가상 네트워크만 추가하고, 구독에서 기존 가상 네트워크를 변경하거나 제거하지 않도록 합니다. 

7. 가상 네트워크 및 서브넷을 검토합니다.

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **선택 사항**: 사용 요금이 발생 하지 않도록이 자습서를 마친 후 만든 리소스를 삭제 하려고 할 수 있습니다. 가상 네트워크를 삭제하려면 4-6단계를 다시 완료하고, 이번에는 5단계에서 추가했던 **VirtualNetworkSite** 요소를 제거합니다.
 
> [!NOTE]
> PowerShell을 사용하여 가상 네트워크(클래식)를 만들도록 리소스 그룹을 지정할 수 없긴 하지만, Azure에서 *기본 네트워킹*이라는 리소스 그룹에 가상 네트워크를 만듭니다.

---

## <a name="next-steps"></a>다음 단계

- 모든 가상 네트워크 및 서브넷 설정에 대해 알아보려면 [가상 네트워크 관리](manage-virtual-network.md) 및 [가상 네트워크 서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요. 프로덕션 환경에서 가상 네트워크 및 서브넷을 사용할 때 다양한 요구 사항을 충족하기 위한 여러 옵션이 있습니다.
- [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 만든 다음, 기존 가상 네트워크에 연결합니다.
- 같은 Azure 위치의 두 가상 네트워크를 연결하려면 가상 네트워크 간에 [가상 네트워크 피어링](create-peering-different-deployment-models.md)을 만듭니다. 가상 네트워크(Resource Manager)를 가상 네트워크(클래식)에 피어링할 수 있지만 두 가상 네트워크(클래식) 간 피어링은 만들 수 없습니다.
- [VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 회로를 사용하여 가상 네트워크를 온-프레미스 네트워크에 연결합니다.
