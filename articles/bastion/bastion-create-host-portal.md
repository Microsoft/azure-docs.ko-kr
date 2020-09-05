---
title: 'Azure 방호 호스트 만들기: 포털'
description: 이 문서에서는 가상 네트워크의 모든 가상 머신에 RDP/SSH 연결을 제공 하는 Azure 방호 호스트를 만드는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270753"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>포털을 사용 하 여 Azure 방호 호스트 만들기

이 문서에서는 Azure Portal를 사용 하 여 Azure 방호 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure 방호 서비스를 프로 비전 한 후에는 동일한 가상 네트워크의 모든 Vm에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

모든 설정을 수동으로 지정 하거나 기존 VM에 해당 하는 설정을 사용 하 여 포털에서 새 요새 호스트 리소스를 만들 수 있습니다. VM 설정을 사용 하 여 요새 호스트를 만들려면 [빠른](quickstart-host-portal.md) 시작 문서를 참조 하세요. 필요에 따라 [Azure PowerShell](bastion-create-host-powershell.md) 를 사용 하 여 Azure 방호 호스트를 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

요새는 다음과 같은 Azure 공용 지역에서 제공 됩니다.

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion 호스트 만들기

이 섹션은 Azure Portal에서 새 Azure 방호 리소스를 만드는 데 도움이 됩니다.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기**를 선택합니다.

1. **새로 만들기** 페이지의 *Marketplace 검색* 필드에 **요새**를 입력 한 다음 **Enter** 를 클릭 하 여 검색 결과를 가져옵니다.

1. 결과에서 **방호**를 클릭 합니다. 게시자가 *Microsoft*이고 범주가 *네트워킹*인지 확인합니다.

1. **요새** 페이지에서 **만들기** 를 클릭 하 여 **요새 만들기** 페이지를 엽니다.

1. **요새 만들기** 페이지에서 새 요새 리소스를 구성 합니다. 요새 리소스에 대 한 구성 설정을 지정 합니다.

    ![요새 만들기](./media/bastion-create-host-portal/settings.png)

    * **구독**: 새 요새 리소스를 만드는 데 사용할 Azure 구독입니다.
    * **리소스 그룹**: 새 요새 리소스가 생성 될 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **이름**: 새 요새 리소스의 이름입니다.
    * **지역**: 리소스가 생성 될 Azure 공개 지역입니다.
    * **가상 네트워크**:에서 방호 리소스를 만들 가상 네트워크입니다. 이 과정에서 포털에 새 가상 네트워크를 만들거나 기존 가상 네트워크를 사용할 수 있습니다. 기존 가상 네트워크를 사용 하는 경우 기존 가상 네트워크에 방호 서브넷 요구 사항을 수용 하기에 충분 한 여유 주소 공간이 있는지 확인 합니다.
    * **서브넷**: 가상 네트워크에서 새 요새 호스트가 배포 되는 서브넷입니다. 서브넷은 요새 호스트 전용으로 지정 되며 **AzureBastionSubnet**로 이름을 지정 해야 합니다. 이 서브넷은 최소/27 이상 이어야 합니다.
    
       **AzureBastionSubnet** 는 [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 지원 하지 않지만 [네트워크 보안 그룹](bastion-nsg.md)은 지원 합니다.
    * **공용 ip 주소**: RDP/SSH를 액세스할 수 있는 방호 리소스 (443 포트를 통해)의 공용 ip입니다. 새 공용 IP를 만들거나 기존 항목을 사용합니다. 공용 IP 주소는 만들려는 Bastion 리소스와 동일한 지역에 있어야 합니다.
    * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
    * **공용 IP 주소 SKU**:이 설정은 기본적으로 **표준**으로 미리 채워져 있습니다. Azure 방호는 표준 공용 IP SKU만 사용/지원 합니다.
    * **할당**:이 설정은 기본적으로 **정적**으로 미리 채워져 있습니다.

1. 설정 지정이 완료 되 면 **검토 + 만들기**를 클릭 합니다. 값의 유효성을 검사 합니다. 유효성 검사가 성공 하면 생성 프로세스를 시작할 수 있습니다.
1. **요새 만들기** 페이지에서 **만들기**를 클릭 합니다.
1. 배포가 진행 되 고 있음을 알리는 메시지가 표시 됩니다. 리소스가 생성 되 면이 페이지에 상태가 표시 됩니다. 요새 리소스를 만들고 배포 하는 데 약 5 분이 걸립니다.

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [요새 FAQ](bastion-faq.md) 를 참조 하세요.

* Azure Bastion 서브넷에서 네트워크 보안 그룹을 사용하려면 [NSG 사용](bastion-nsg.md)을 참조하세요.
