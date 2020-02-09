---
title: Azure 방호 호스트 만들기 | Microsoft Docs
description: 이 문서에서는 Azure 방호 호스트를 만드는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990422"
---
# <a name="create-an-azure-bastion-host"></a>Azure 방호 호스트 만들기

이 문서에서는 Azure Portal를 사용 하 여 Azure 방호 호스트를 만드는 방법을 보여 줍니다. 가상 네트워크에서 Azure 방호 서비스를 프로 비전 한 후에는 동일한 가상 네트워크의 모든 Vm에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure 방호 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크를 기준으로 합니다.

모든 설정을 수동으로 지정 하거나 기존 VM에 해당 하는 설정을 사용 하 여 포털에서 새 요새 호스트 리소스를 만들 수 있습니다. 필요에 따라 [Azure Powershell](bastion-create-host-powershell.md) 을 사용 하 여 azure 방호 호스트를 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

요새는 다음과 같은 Azure 공용 지역에서 제공 됩니다.

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>요새 호스트 만들기-설정 지정

이 섹션은 Azure Portal에서 새 Azure 방호 리소스를 만드는 데 도움이 됩니다.

1. [Azure Portal](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기**를 선택 합니다.

1. **새로 만들기** 페이지의 *Marketplace 검색* 필드에 **요새**를 입력 한 다음 **Enter** 를 클릭 하 여 검색 결과를 가져옵니다.

1. 결과에서 **방호**를 클릭 합니다. 게시자가 *Microsoft*이고 범주가 *네트워킹*인지 확인합니다.

1. **요새** 페이지에서 **만들기** 를 클릭 하 여 **요새 만들기** 페이지를 엽니다.

1. **요새 만들기** 페이지에서 새 요새 리소스를 구성 합니다. 요새 리소스에 대한 구성 설정을 지정 합니다.

    ![요새 만들기](./media/bastion-create-host-portal/settings.png)

    * **구독**: 새 요새 리소스를 만드는 데 사용할 Azure 구독입니다.
    * **리소스 그룹**: 새 요새 리소스가 생성 될 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **이름**: 새 요새 리소스의 이름입니다.
    * **지역**: 리소스가 생성 될 Azure 공개 지역입니다.
    * **가상 네트워크**:에서 방호 리소스를 만들 가상 네트워크입니다. 이 과정에서 포털에 새 가상 네트워크를 만들거나 기존 가상 네트워크를 사용할 수 있습니다. 기존 가상 네트워크를 사용 하는 경우 기존 가상 네트워크에 방호 서브넷 요구 사항을 수용 하기에 충분 한 여유 주소 공간이 있는지 확인 합니다.
    * **서브넷**: 새 요새 호스트 리소스가 배포 될 가상 네트워크의 서브넷입니다. **AzureBastionSubnet**이름 값을 사용 하 여 서브넷을 만들어야 합니다. 이 값을 통해 Azure는 요새 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과는 다릅니다. /27 이상 (/27,/26 등)의 서브넷을 사용 해야 합니다.
    
       경로 테이블 또는 위임 없이 **AzureBastionSubnet** 를 만듭니다. **AzureBastionSubnet**의 네트워크 보안 그룹을 사용 하는 경우 [nsgs 작업](bastion-nsg.md) 문서를 참조 하세요.
    * **공용 ip 주소**: RDP/SSH를 액세스할 수 있는 방호 리소스 (443 포트를 통해)의 공용 ip입니다. 새 공용 IP를 만들거나 기존 공용 IP를 사용 합니다. 공용 IP 주소는 만들려는 방호 리소스와 동일한 지역에 있어야 합니다.
    * **공용 ip 주소 이름**: 공용 ip 주소 리소스의 이름입니다.
    * **공용 IP 주소 SKU**:이 설정은 기본적으로 **표준**으로 미리 채워져 있습니다. Azure 방호는 표준 공용 IP SKU만 사용/지원 합니다.
    * **할당**:이 설정은 기본적으로 **정적**으로 미리 채워져 있습니다.

1. 설정 지정이 완료 되 면 **검토 + 만들기**를 클릭 합니다. 값의 유효성을 검사 합니다. 유효성 검사가 성공 하면 생성 프로세스를 시작할 수 있습니다.
1. **요새 만들기** 페이지에서 **만들기**를 클릭 합니다.
1. 배포가 진행 되 고 있음을 알리는 메시지가 표시 됩니다. 리소스가 생성 되 면이 페이지에 상태가 표시 됩니다. 요새 리소스를 만들고 배포 하는 데 약 5 분이 걸립니다.

## <a name="createvmset"></a>요새 호스트 만들기-VM 설정 사용

기존 VM을 사용 하 여 포털에서 요새 호스트를 만드는 경우 다양 한 설정이 자동으로 가상 머신 및/또는 가상 네트워크에 해당 하는 기본값으로 설정 됩니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 가상 머신으로 이동한 다음 **연결**을 클릭 합니다.

   ![VM 연결](./media/bastion-create-host-portal/vmsettings.png)
1. 오른쪽 사이드바에서 **방호**를 클릭 한 다음, **요새를 사용**합니다.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. 요새 페이지에서 다음 설정 필드를 입력 합니다.

   * **이름**: 만들려는 요새 호스트의 이름입니다.
   * **서브넷**: 가상 네트워크 내에서, 요새 리소스가 배포 되는 서브넷입니다. **AzureBastionSubnet**이름으로 서브넷을 만들어야 합니다. 이를 통해 Azure는 요새 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과는 다릅니다. /27 이상 (/27,/26 등)의 서브넷을 사용 해야 합니다. 네트워크 보안 그룹, 경로 테이블 또는 위임 없이 서브넷을 만듭니다. 나중에 **AzureBastionSubnet**에서 네트워크 보안 그룹을 사용 하도록 선택 하는 경우 [nsgs 작업](bastion-nsg.md)을 참조 하세요.
   
     **서브넷 구성 관리** 를 클릭 하 여 **AzureBastionSubnet**를 만듭니다.  **만들기** 를 클릭 하 여 서브넷을 만든 후 다음 설정을 계속 진행 합니다.
   * **공용 ip 주소**: RDP/SSH를 액세스할 수 있는 방호 리소스 (443 포트를 통해)의 공용 ip입니다. 새 공용 IP를 만들거나 기존 공용 IP를 사용 합니다. 공용 IP 주소는 만들려는 방호 리소스와 동일한 지역에 있어야 합니다.
   * **공용 ip 주소 이름**: 공용 ip 주소 리소스의 이름입니다.
1. 유효성 검사 화면에서 **만들기**를 클릭 합니다. 요새 리소스를 만들고 배포 하는 데 약 5 분 정도 기다립니다.

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [요새 FAQ](bastion-faq.md) 를 참조 하세요.

* Azure 방호 서브넷에서 네트워크 보안 그룹을 사용 하려면 [NSGs 작업](bastion-nsg.md)을 참조 하세요.