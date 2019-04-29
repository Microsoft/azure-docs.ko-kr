---
title: VM(클래식)에 대한 개인 IP 주소 구성 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 가상 머신(클래식)에 대한 개인 IP 주소를 구성하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72d1c4d2ea3adf7d8751adfbb013435f8f2530f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125749"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신(클래식)에 대한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. [리소스 관리자 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-arm-pportal.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

아래 샘플 단계를 위해서는 간단한 환경을 미리 만들어야 합니다. 이 문서에 표시된 대로 단계를 실행하려는 경우 먼저 [vnet 만들기](virtual-networks-create-vnet-classic-pportal.md)에 설명된 테스트 환경을 구축합니다.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>VM을 만들 때 정적 개인 IP 주소를 지정하는 방법
*192.168.1.101*의 정적 개인 IP 주소를 사용하여 *TestVNet*이라는 VNet의 *FrontEnd* 서브넷에 *DNS01*이라는 VM을 만들려면 다음 단계를 완료하세요.

1. 브라우저에서 https://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. **새로 만들기** > **계산** > **Windows Server 2012 R2 Datacenter**를 선택하고 **배포 모델 선택** 목록에 **클래식**이 이미 표시되는지 확인한 후 **만들기**를 선택합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. **VM 만들기**에서 만들 VM의 이름(이 시나리오에서는*DNS01* ), 로컬 관리자 계정 및 암호를 입력합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. **옵션 구성** > **네트워크** > **Virtual Network**를 선택하고 **TestVNet**을 선택합니다. **TestVNet** 을 사용할 수 없는 경우 *미국 중부* 위치를 사용 중이고 이 문서의 시작 부분에서 설명한 테스트 환경을 만들었는지 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. **네트워크**에서 현재 선택된 서브넷이 *FrontEnd*인지 확인하고 **IP 주소 할당** 아래에서 **IP 주소**를 선택하고 **정적**을 선택한 후 아래와 같이 **IP 주소**에 대해 *192.168.1.101*을 입력합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. **IP 주소** 아래에서 **확인**을 선택하고 **네트워크**에서 **확인**을 선택한 후 **선택적 구성**에서 **확인**을 선택합니다.
7. **VM 만들기**에서 **만들기**를 선택합니다. 대시보드에 아래 타일이 표시되는지 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보를 검색하는 방법
위의 단계를 사용하여 만든 VM에 대한 정적 개인 IP 주소를 보려면 아래 단계를 실행합니다.

1. Azure Portal에서 **모두 찾아보기** > **가상 머신(클래식)** > **DNS01** > **모든 설정** > **IP 주소**를 선택하고 아래와 같이 IP 주소 할당 및 IP 주소를 확인합니다.
   
    ![Azure 포털에서 VM 만들기](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소를 제거하는 방법

다음 그림에 표시된 것처럼 **IP 주소**에서 **IP 주소 할당** 오른쪽에 있는 **동적**을 선택하고 **저장**을 선택한 후 **예**를 선택합니다.
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>기존 VM에 정적 개인 IP 주소를 추가하는 방법

1. 이전에 표시된 것처럼 **IP 주소** 아래에서 **IP 주소 할당** 오른쪽에 있는 **정적**을 선택합니다.
2. **IP 주소**로 *192.168.1.101*을 입력하고 **저장**을 선택한 후 **예**를 선택합니다.

## <a name="set-ip-addresses-within-the-operating-system"></a>운영 체제 내에서 IP 주소 설정

반드시 필요한 경우가 아니면, VM의 운영 체제 내에서 Azure Virtual Machine에 할당된 개인 IP를 고정적으로 할당하는 것은 바람직하지 않습니다. 운영 체제 내에서 개인 IP 주소를 수동으로 설정하는 경우 Azure VM에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. 가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다.

## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.

