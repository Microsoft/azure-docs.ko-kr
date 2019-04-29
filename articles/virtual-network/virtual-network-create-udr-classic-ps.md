---
title: Azure Virtual Network에서 라우팅 제어 - PowerShell - 클래식 | Microsoft Docs
description: PowerShell을 사용하여 VNet에서 라우팅을 제어하는 방법 알아보기 | 클래식
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 1441ee9a3d4a563ab35cd9b01e8347d8f51b827a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743412"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>PowerShell 사용하여 라우팅 제어 및 가상 어플라이언스(클래식) 사용

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell(클래식)](virtual-network-create-udr-classic-ps.md)
> * [CLI(클래식)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Azure 리소스로 작업하기 전에 Azure에는 현재 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](../azure-resource-manager/resource-manager-deployment-model.md) 를 이해해야 합니다. 이 문서의 윗부분에 있는 옵션을 선택하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 Azure PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [PowerShell을 사용하여 VNet(클래식) 만들기](virtual-networks-create-vnet-classic-netcfg-ps.md)에 표시된 환경을 구축합니다.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 프런트 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. 다음 명령을 실행하여 경로 테이블에 경로를 만들고 백 엔드 서브넷(192.168.2.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 다음 명령을 실행하여 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>백 엔드 서브넷에 대한 UDR 만들기
시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 마칩니다.

1. 다음 명령을 실행하여 백 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. 다음 명령을 실행하여 경로 테이블에 경로를 만들고 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 다음 명령을 실행하여 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>FW1 VM에서 IP 전달을 사용하도록 설정합니다.

FW1 VM에 IP 전달을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 다음 명령을 실행하여 IP 전달 상태를 확인합니다.

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. 다음 명령을 실행하여 *FW1* VM에 대한 IP 전달을 사용하도록 설정합니다.

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
