---
title: Azure Virtual Machines의 여러 IP 주소 - 포털 | Microsoft Docs
description: Azure Portal | Resource Manager를 사용하여 가상 머신에 여러 IP 주소를 할당하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
ms.openlocfilehash: b1873b770a6b4280b7098c68ecb75cc1411fe453
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746989"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신에 여러 IP 주소 할당

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> 이 문서는 Azure Portal을 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 머신)을 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

여러 IP 주소 또는 고정 개인 IP 주소를 사용하여 VM을 만들려면 PowerShell 또는 Azure CLI를 사용하여 만들어야 합니다. 방법을 알아보려면 이 문서의 맨 위에 있는 PowerShell 또는 CLI 옵션을 클릭합니다. 단일 동적 개인 IP 주소 및 (선택 사항) 단일 공용 IP 주소를 사용하여 VM을 만들 수 있습니다. [Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 또는 [Linux VM 만들기](../virtual-machines/linux/quick-create-portal.md) 문서의 단계에 따라 포털을 사용합니다. VM을 만든 후에 이 문서의 [VM에 IP 주소 추가](#add) 섹션에 나오는 단계에 따라 포털을 사용하여 IP 주소 형식을 동적에서 고정으로 변경하고 추가 IP 주소를 추가할 수 있습니다.

## <a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 Azure 네트워크 인터페이스에 추가할 수 있습니다. 다음 섹션의 예제는 [시나리오](#scenario)에서 설명한 3개의 IP로 구성된 VM이 이미 있다고 가정하지만 필수 사항은 아닙니다.

### <a name="coreadd"></a>핵심 단계

1. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
2. 포털에서 **더 많은 서비스**를 클릭하고 필터 상자에 *가상 머신*를 입력하고 **가상 머신**를 클릭합니다.
3. **가상 머신** 창에서 IP 주소를 추가할 VM을 클릭합니다. 표시되는 가상 머신 창에서 **네트워크 인터페이스**를 클릭하고 IP 주소를 추가할 네트워크 인터페이스를 선택합니다. 다음 그림의 예제에서는 *myVM*이라는 VM에서 *myNIC*라는 NIC가 선택됩니다.

    ![Linux](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. 선택한 NIC에 대해 표시되는 창에서 **IP 구성**을 클릭합니다.

추가할 IP 주소 형식에 따라, 이어지는 섹션 중 하나의 단계를 완료합니다.

### <a name="add-a-private-ip-address"></a>**개인 IP 주소 추가**

새 개인 IP 주소를 추가하려면 다음 단계를 완료합니다.

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. **추가**를 클릭합니다. 표시되는 **IP 구성 추가** 창에서 *고정* 개인 IP 주소가 *10.0.0.7*인 *IPConfig-4*라는 IP 구성을 만든 다음 **확인**을 클릭합니다.

    > [!NOTE]
    > 고정 IP 주소를 추가할 경우 NIC가 연결된 서브넷의 사용하지 않은 유효한 주소를 지정해야 합니다. 선택한 주소를 사용할 수 없는 경우 포털에 IP 주소로 X가 표시되며 다른 주소를 선택해야 합니다.

3. 확인을 클릭하면 창이 닫히고 새 IP 구성이 나열됩니다. **확인**을 클릭하여 **IP 구성 추가** 창을 닫습니다.
4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 있는 단계를 완료하여 VM 운영 체제에 개인 IP 주소를 추가합니다.

### <a name="add-a-public-ip-address"></a>공용 IP 주소 추가

공용 IP 주소 리소스를 새 IP 구성 또는 기존 IP 구성에 연결하면 공용 IP 주소가 추가됩니다.

> [!NOTE]
> 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
> 

### <a name="create-public-ip"></a>공용 IP 주소 리소스 만들기

공용 IP 주소는 공용 IP 주소 리소스에 대한 한 가지 설정입니다. IP 구성에 연결하려는 IP 구성에 현재 연결되어 있지 않은 공용 IP 주소 리소스가 있는 경우 다음 단계를 건너뛰고 필요에 따라 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다. 사용 가능한 공용 IP 주소 리소스가 없는 경우 다음 단계를 완료하여 리소스를 하나 만듭니다.

1. https://portal.azure.com의 Azure Portal로 이동한 후 필요한 경우 로그인합니다.
3. 포털에서 **리소스 만들기** > **네트워킹** > **공용 IP 주소**를 클릭합니다.
4. 다음 그림과 같이 표시되는 **공용 IP 주소 만들기** 창에서 **이름**을 입력하고 **IP 주소 할당** 형식, **구독**, **리소스 그룹** 및 **위치**를 선택한 다음, **만들기**를 클릭합니다.

    ![공용 IP 주소 리소스 만들기](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. 공용 IP 주소 리소스를 IP 구성에 연결하려면 이어지는 섹션 중 하나에 나와 있는 단계를 완료합니다.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>새 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. **추가**를 클릭합니다. 표시되는 **IP 구성 추가** 창에서 *IPConfig-4*라는 IP 구성을 만듭니다. 표시되는 **공용 IP 주소 선택** 창에서 **공용 IP 주소**를 사용하도록 설정하고 사용 가능한 기존 공용 IP 주소 리소스를 선택합니다.

    공용 IP 주소 리소스를 선택한 후 **확인**을 클릭하면 창이 닫힙니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다. 

3. 새 IP 구성을 검토합니다. 개인 IP 주소가 명시적으로 할당되지 않았더라도 모든 IP 구성에는 개인 IP 주소가 있어야 하므로 IP 구성에 자동으로 주소가 하나 할당되어 있습니다.
4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다.
5. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>기존 IP 구성에 공용 IP 주소 리소스 연결

1. 이 문서의 [핵심 단계](#coreadd) 섹션에 나오는 단계를 완료합니다.
2. 공용 IP 주소 리소스를 추가할 IP 구성을 클릭합니다.
3. 표시되는 IPConfig 창에서 **IP 주소**를 클릭합니다.
4. 표시되는 **공용 IP 주소 선택** 창에서 공용 IP 주소를 선택합니다.
5. **저장**을 클릭하면 창이 닫힙니다. 기존 공용 IP 주소가 없는 경우 이 문서의 [공용 IP 주소 리소스 만들기](#create-public-ip) 섹션에 나와 있는 단계를 완료하여 주소를 하나 만들 수 있습니다.
3. 새 IP 구성을 검토합니다.
4. **추가**를 클릭하여 다른 IP 구성을 추가하거나 열려 있는 모든 블레이드를 닫아 IP 주소 추가를 완료할 수 있습니다. 운영 체제에 공용 IP 주소를 추가하지 마세요.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
