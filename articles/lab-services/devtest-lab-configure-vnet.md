---
title: Azure DevTest Labs에서 가상 네트워크 구성 | Microsoft 문서
description: 기존 가상 네트워크 및 서브넷을 구성하고 Azure DevTest Labs를 통해 VM에서 사용하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 8fb3b4ac748fcae2e3aad5b3bfb2a893340dc61a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694819"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs에서 가상 네트워크 구성
[랩에 VM 추가](devtest-lab-add-vm.md) 문서에 설명된 대로 랩에 VM을 만들 때 구성된 가상 네트워크를 지정할 수 있습니다. 예를 들어 ExpressRoute 또는 사이트 간 VPN으로 구성된 가상 네트워크를 사용하여 VM에서 회사 네트워크 리소스에 액세스해야 할 수 있습니다.

이 문서에서는 VM을 만들 때 선택할 수 있도록 랩의 Virtual Network 설정으로 기존 가상 네트워크를 추가하는 방법을 설명합니다.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Azure 포털을 사용하여 랩에 대한 가상 네트워크 구성
다음 단계에서는 동일한 랩에서 VM을 만들 때 사용할 수 있도록 랩에 기존 가상 네트워크(및 서브넷)를 추가하는 방법을 안내합니다. 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 원하는 랩을 탭합니다. 
1. 랩의 기본 창에서 **구성 및 정책**을 선택합니다.

    ![랩의 구성 및 정책에 액세스](./media/devtest-lab-configure-vnet/policies-menu.png)
1. **외부 리소스** 섹션에서 **가상 네트워크**를 선택합니다. 랩에 대해 만들어진 기본 가상 네트워크뿐만 아니라 현재 랩에 대해 구성한 가상 네트워크의 목록이 표시됩니다. 
1. **+추가**를 선택합니다.
   
    ![랩에 기존 가상 네트워크 추가](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. **가상 네트워크** 창에서 **[가상 네트워크 선택]** 을 선택합니다.
   
    ![기존 가상 네트워크 선택](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. **가상 네트워크 선택** 창에서 원하는 가상 네트워크를 선택합니다. 구독의 동일 지역에 속하는 모든 가상 네트워크가 랩으로 표시되는 목록이 나타납니다.
1. 가상 네트워크를 선택하면 **가상 네트워크** 창으로 되돌아갑니다. 하단 목록에서 서브넷을 선택합니다.

    ![서브넷 목록](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    랩 서브넷 창이 표시됩니다.

    ![랩 서브넷 창](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - **랩 서브넷 이름**을 지정합니다.
   - 서브넷이 랩 VM 생성에 사용되도록 하려면 **가상 머신을 만들 때 사용**을 선택합니다.
   - [shared public IP address](devtest-lab-shared-ip.md)(공유 공용 IP 주소)를 사용하도록 설정하려면 **Enable shared public IP**(공유 공용 IP 사용)를 선택합니다.
   - 서브넷에서 공용 IP 주소를 허용하려면 **Allow public IP creation(공용 IP 생성 허용)** 을 선택합니다.
   - **사용자당 최대 가상 머신** 필드에서 각 서브넷에 대한 사용자당 최대 VM을 지정합니다. VM 수에 제한을 두지 않으려면 이 필드는 공백으로 둡니다.
1. **확인**을 선택하여 랩 서브넷 창을 닫습니다.
1. **저장**을 선택하여 가상 네트워크 창을 닫습니다.

이제 가상 네트워크를 구성했으므로 VM을 만들 때 선택할 수 있습니다. VM을 만들고 가상 네트워크를 지정하는 방법은 [랩에 VM 추가](devtest-lab-add-vm.md) 문서를 참조하세요. 

Azure의 [가상 네트워크 설명서](https://docs.microsoft.com/azure/virtual-network)에 VNet을 설정하고 관리하고 온-프레미스 네트워크에 연결하는 방법을 포함하여 VNet 사용 방법에 대한 자세한 정보가 나와 있습니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
랩에 원하는 가상 네트워크를 추가한 후에는 [랩에 VM을 추가](devtest-lab-add-vm.md)해야 합니다.

