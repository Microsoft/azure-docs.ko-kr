---
title: VM에 대한 개인 IP 주소 구성 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 가상 머신에 대한 개인 IP 주소를 구성하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: e5efe0516d1b2dd387532d31a0a6654e6651fe41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596537"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 컴퓨터에 대한 개인 IP 주소 구성

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure Portal(클래식)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell(클래식)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI(클래식)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-classic-pportal.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

다음 샘플 단계를 수행하려면 단순한 환경이 이미 만들어져 있어야 합니다. 이 문서에 표시된 대로 단계를 실행하려는 경우 먼저 [가상 네트워크 만들기](quick-create-portal.md)에 설명된 테스트 환경을 구축합니다.

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>정적 개인 IP 주소 테스트용 VM을 만드는 방법
Azure 포털을 사용하여 리소스 관리자 배포 모드에서 VM을 만드는 동안에는 정적 개인 IP 주소를 설정할 수 없습니다. 먼저 VM를 만든 다음 개인 IP를 고정으로 설정합니다.

*TestVNet*이라는 VNet의 *FrontEnd* 서브넷에 *DNS01*이라는 VM을 만들려면 다음 단계를 따르세요.

1. 브라우저에서 https://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 다음 그림과 같이 **리소스 만들기** > **계산** > **Windows Server 2012 R2 Datacenter**를 클릭하고 **배포 모델 선택** 목록에 **리소스 관리자**가 이미 표시되는지 확인한 다음, **만들기**를 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. **기본 사항** 창에서 만들 VM의 이름(이 시나리오에서는 *DNS01*), 로컬 관리자 계정 및 암호를 다음 그림과 같이 입력합니다.
   
    ![기본 사항 창](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. 선택된 **위치**가 *미국 중부*인지 확인한 후 **리소스 그룹** 아래에서 **기존 선택**을 클릭하고 **리소스 그룹**을 다시 클릭한 후 *TestRG*, **확인**을 차례로 클릭합니다.
   
    ![기본 사항 창](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. **크기 선택** 창에서 **A1 표준**을 선택하고 **선택**을 클릭합니다.
   
    ![크기 선택 창](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. **설정** 창에서 속성이 다음 값으로 설정되어 있는지 확인하고 **확인**을 클릭합니다.
   
    -**Storage 계정**: *vnetstorage*
   
   * **네트워크**: *TestVNet*
   * **서브넷**: *FrontEnd*
     
     ![크기 선택 창](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. **요약** 창에서 **확인**을 클릭합니다. 대시보드에 다음 타일이 표시됩니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

[Windows VM에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)할 때처럼 반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 단계를 사용하여 만든 VM에 대한 고정 개인 IP 주소 정보를 보려면 다음 단계를 실행합니다.

1. Azure Portal에서 **모두 찾아보기** > **가상 머신** > **DNS01** > **모든 설정** > **네트워크 인터페이스**를 클릭한 다음, 나열되는 네트워크 인터페이스 하나를 클릭합니다.
   
    ![VM 타일 배포](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. **네트워크 인터페이스** 창에서 **모든 설정** > **IP 주소**를 클릭하고 **할당** 및 **IP 주소** 값을 확인합니다.
   
    ![VM 타일 배포](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소를 추가하는 방법
위의 단계를 사용하여 만든 VM에 고정 개인 IP 주소를 추가하려면 다음 단계를 따르세요.

1. 위에 표시된 **IP 주소** 창에서 **할당** 아래에 있는 **고정**을 클릭합니다.
2. **IP 주소**에 *192.168.1.101*을 입력하고 **저장**을 클릭합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> **저장**을 클릭한 후에도 할당이 **동적**으로 설정되어 있으면 입력한 IP 주소가 이미 사용 중인 것입니다. 다른 IP 주소로 시도하세요.
> 
> 

[Windows VM에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)할 때처럼 반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법
위에서 만든 VM에서 정적 개인 IP 주소를 제거하려면 다음 단계를 완료합니다.

위에 표시된 **IP 주소** 창에서 **할당** 아래에 있는 **동적**을 클릭한 다음, **저장**을 클릭합니다.

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

[Windows VM에 여러 IP 주소를 할당](virtual-network-multiple-ip-addresses-portal.md)할 때처럼 반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="next-steps"></a>다음 단계

[IP 주소 설정](virtual-network-network-interface-addresses.md) 관리에 대해 자세히 알아봅니다.

