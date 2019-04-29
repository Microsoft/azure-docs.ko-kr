---
title: Azure Virtual Network에서 라우팅 제어 - CLI - 클래식 | Microsoft Docs
description: 클래식 배포 모델에서 Azure CLI를 사용하여 VNet에서 라우팅을 제어하는 방법 알아보기
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: e1b8bb3544a08b60564ceb5bd7e1666214059e09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743924"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Azure CLI를 사용하여 라우팅 제어 및 가상 어플라이언스(클래식) 사용

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell(클래식)](virtual-network-create-udr-classic-ps.md)
> * [CLI(클래식)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 라우팅을 제어하고 가상 어플라이언스를 사용](tutorial-create-route-table-cli.md)할 수도 있습니다.

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 Azure CLI 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [Azure CLI를 사용하여 VNet(클래식) 만들기](virtual-networks-create-vnet-classic-cli.md)에 표시된 환경을 구축합니다.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 클래식 모드로 전환합니다.

    ```azurecli
    azure config mode asm
    ```

    출력

        info:    New mode is asm

2. 다음 명령을 실행하여 프런트 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    출력
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    매개 변수
   
   * **-l (or --location)**. 새 NSG를 만들 Azure 지역입니다. 이 시나리오에서는 *westus*입니다.
   * **-n (or --name)**. 새 NSG의 이름입니다. 이 시나리오에서는 *NSG-FrontEnd*입니다.
3. 다음 명령을 실행하여 경로 테이블에 경로를 만들고 백 엔드 서브넷(192.168.2.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    출력
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    매개 변수
   
   * **-r(또는 --route-table-name)**. 경로가 추가될 경로 테이블의 이름입니다. 이 시나리오에서는 *UDR-FrontEnd*입니다.
   * **-a(또는 --address-prefix)**. 패킷을 보내는 서브넷에 대한 주소 접두사입니다. 이 시나리오에서는 *192.168.2.0/24*입니다.
   * **-t(또는 --next-hop-type)**. 전송할 개체 트래픽 유형입니다. 가능한 값은 *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* 또는 *None*입니다.
   * **-p(또는 --next-hop-ip-address**). 다음 홉에 대한 IP 주소입니다. 이 시나리오에서는 *192.168.0.4*입니다.
4. 다음 명령을 실행하여 만든 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    출력
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    매개 변수
   
   * **-t(또는 --vnet-name)**. 서브넷이 위치한 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
   * **-n(또는 --subnet-name**). 경로 테이블이 추가될 서브넷의 이름입니다. 이 시나리오에서는 *FrontEnd*입니다.

## <a name="create-the-udr-for-the-back-end-subnet"></a>백 엔드 서브넷에 대한 UDR 만들기
시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 마칩니다.

1. 다음 명령을 실행하여 백 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. 다음 명령을 실행하여 경로 테이블에 경로를 만들고 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. 다음 명령을 실행하여 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

