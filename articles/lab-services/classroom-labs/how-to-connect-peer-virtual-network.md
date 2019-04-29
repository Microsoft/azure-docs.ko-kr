---
title: Azure Lab Services에서 피어 네트워크에 연결 | Microsoft Docs
description: 피어와 다른 네트워크를 사용 하 여 랩 네트워크를 연결 하는 방법에 알아봅니다. 예를 들어, Azure에서 랩의 virtual network를 사용 하 여 여러분의 학교가 등록 온-프레미스 네트워크를 연결 합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695409"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Azure Lab Services의 피어 가상 네트워크를 사용 하 여 랩의 네트워크 연결 
이 문서에서는 다른 네트워크를 사용 하 여 랩 네트워크를 피어 링에 대 한 정보를 제공 합니다. 

## <a name="overview"></a>개요
가상 네트워크 피어 링 Azure 가상 네트워크를 원활 하 게 연결할 수 있습니다. 가상 네트워크가 피어링되면 연결성을 위해 하나로 표시됩니다. 개인 IP 주소를 통해 동일한 가상 네트워크의 가상 컴퓨터 간에 라우팅되는 것과 마찬가지로 피어 링 된 가상 네트워크의 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 자세한 내용은 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md)을 참조하세요.

다음을 비롯 한 일부 시나리오에서 피어 가상 네트워크를 사용 하 여 랩의 네트워크를 연결 해야 할 수 있습니다.

- 랩에서 가상 머신에 있는 라이선스를 획득 하는 온-프레미스 라이선스 서버에 연결 하는 소프트웨어
- 랩에서 가상 머신 university의 네트워크 공유에 데이터 집합에 대 한 액세스 (또는 다른 파일)이 필요 합니다. 

하거나 Azure 가상 네트워크에 연결 된 특정 온-프레미스 네트워크를 통해 [ExpressRoute](../../expressroute/expressroute-introduction.md) 하거나 [Virtual Network 게이트웨이](../../vpn-gateway/vpn-gateway-about-vpngateways.md)합니다. Azure Lab Services 외부에서 이러한 서비스를 설정 해야 합니다. ExpressRoute를 사용 하 여 Azure에 온-프레미스 네트워크를 연결 하는 방법에 대 한 자세한 내용은 [ExpressRoute 개요] 참조) (... /expressroute/expressroute-introduction.md)입니다. 게이트웨이, 가상 네트워크 게이트웨이 사용 하 여 온-프레미스 연결에 대 한 가상 네트워크를 지정 하 고 랩 계정을 모두 동일한 지역에 이어야 합니다.


## <a name="configure-at-the-time-of-lab-account-creation"></a>랩 계정 만들기의 시 구성
새 랩 계정 만드는 동안 표시 되는 기존 virtual network를 선택할 수 있습니다 합니다 **피어 가상 네트워크** 드롭다운 목록입니다. 선택한 가상 네트워크는 랩 계정 아래에 만든 랩에 connected(peered) 합니다. 모든 가상 컴퓨터를 한 후 만들어지는 랩에서이 변경 했을 리소스에 대 한 액세스 피어 링된 된 가상 네트워크. 

![VNet 피어 링을 선택 합니다.](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> 랩 계정을 만들기 위한 자세한 단계별 지침은 참조 하세요. [랩 계정 설정](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>랩을 만든 후 구성
동일한 속성을 사용할 수 있습니다는 **Labs 구성** 탭의 **랩 계정을** 랩 계정 생성 시 피어 네트워크를 설정 하지 않은 경우 페이지입니다. 이 설정에 변경 내용을 변경 된 후 만들어진 랩에만 적용 됩니다.

![랩을 만든 후 피어 링 된 VNet을 사용할지 설정 합니다.](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

이미지에 보이는 것 처럼이 활성화 하거나 비활성화할 수 있습니다 **피어 가상 네트워크** 실습 랩 계정에 대 한 합니다. 

> [!IMPORTANT]
> 이 설정 변경 기존 랩 필요가 변경이 이루어진 후 만들어진 랩에만 적용 됩니다. 



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)

