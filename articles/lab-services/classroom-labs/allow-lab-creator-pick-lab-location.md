---
title: 랩 작성자가 Azure Lab Services에서 위치를 선택 하도록 허용
description: 이 문서에서는 랩 계정 관리자가 랩 작성자가 랩의 위치를 선택 하도록 허용 하는 방법을 설명 합니다.
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
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: ea9df341fcc76a38364e590f8541e6a85d7ec466
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211010"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>랩 작성자가 Azure Lab Services에서 랩의 위치를 선택 하도록 허용
Azure Lab Services 랩 계정 소유자는 랩 작성자 (교육자)가 만든 랩의 위치를 선택할 수 있습니다. 이 위치는 랩 계정의 위치와 다를 수 있습니다. 위치는 Azure 지역 그룹입니다. 예를 들어 미국 위치는 미국 동부, 미국 서 부 등의 지역 그룹입니다. 

랩 계정 소유자는 랩 계정을 만들 때 및 랩 계정 (또는 기존 랩 계정)을 만든 후 랩 작성자가 랩 **위치를 선택할 수 있도록 허용** 옵션을 선택할 수 있습니다. 

## <a name="at-the-time-of-lab-account-creation"></a>랩 계정을 만들 때
랩 계정을 만들 때 첫 번째 화면 (**기본** 탭)에이 옵션이 표시 됩니다. 

![랩 생성 시 옵션을 사용 하도록 설정 합니다.](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

**고급** 탭에서 랩 계정에 대 한 피어 가상 네트워크를 선택 하는 경우이 옵션을 사용할 수 없습니다.  

![피어 가상 네트워크를 사용 하는 경우](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>랩 계정이 만들어진 후
랩 계정을 만든 후 다음 단계를 수행 하 여이 옵션을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 설정** 을 선택 합니다.
2. 랩 작성자가 랩 위치를 선택할 수 있도록 허용 하려면 **랩 작성자** 가 랩 위치를 선택 하도록 허용 옵션을 선택 합니다. 사용 하지 않도록 설정 된 경우 랩 계정이 있는 동일한 위치에 랩을 자동으로 만듭니다. 
    
    **피어 가상 네트워크** 필드의 가상 네트워크를 선택 하면이 필드를 사용할 수 없습니다. 랩 계정의 랩은 피어 가상 네트워크의 리소스에 액세스 하기 위해 랩 계정과 동일한 지역에 있어야 하기 때문입니다. 
1. 도구 모음에서 **저장**을 선택합니다. 

    ![랩 설정](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크 및 위치 선택은 허용 되지 않습니다.
이 시나리오에서는 **랩 작성자가 랩 위치를 선택할 수 있음** 옵션을 사용 하도록 설정 하지 않았습니다. 

![랩 위치 없음](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

그런 다음 랩 작성자 (교육자)는 랩의 위치를 선택 하는 옵션을 표시 하지 않습니다. 사용할 수 있는 모든 크기 옵션에 대 한 시간당 가격이 표시 됩니다. 랩을 만들 때 랩 계정이 있는 Azure 지역과 동일한 위치에 있는 Azure 지역에 생성 됩니다. 예를 들어 랩 계정이 **미국 서 부**에 있는 경우에는 미국 서 부에서 랩을 만들 수 **는 있지만** **캐나다 동부**에서는 생성 되지 않습니다. 어디에서 든 선택 하는 지역에 대 한 어떠한 정보도 보증 하지 않습니다. 현재 크기가 제한 된 경우 랩 작성자에 게는 일반적으로 지원 하지만 현재 사용할 수 없는 크기를 볼 수 있는 확인란이 표시 됩니다. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>가상 네트워크 및 위치 선택이 허용 되지 않음
이 시나리오에서는 랩 계정에 대 한 피어 가상 네트워크를 선택 했으므로 **랩 작성자가 랩 위치를 선택할 수 있음** 옵션을 사용할 수 없습니다. 그러면 랩 작성자는 이전 옵션과 동일한 화면을 볼 수 있습니다. 모든 Vm은 가상 네트워크와 동일한 Azure 지역에 있어야 하기 때문에 가상 네트워크가 있는 동일한 Azure 지역에 랩이 생성 됩니다. 특정 지역이 크기에 대해 제한 되는 경우 크기는 사용할 수 없음으로 표시 됩니다. 

## <a name="location-selection-is-enabled"></a>위치 선택이 사용 됩니다.
랩 작성자가 랩 **위치를 선택할 수 있도록 허용**을 선택 하면 랩 작성자 (교육자)가 랩을 만들 때 위치를 선택 하는 옵션을 볼 수 있습니다. 

![랩 위치 선택](../media/allow-lab-creator-pick-lab-location/location-selection.png)

랩 작성자는 크기가 인 모든 위치의 가격 범위를 확인 하 고 위치를 선택할 수 있습니다. 랩은 해당 위치에 매핑되는 모든 Azure 지역에서 만들어집니다.

위치가 제한 되는 경우 기본적으로 목록에 표시 되지 않습니다. 드롭다운 목록을 확장 하 고 **이 크기에 사용할 수 없는 위치 표시**를 선택 합니다. 

![사용할 수 없는 위치 표시](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>비용
이전에는 랩에 대해 선택한 VM 크기를 기준으로 가격 책정을 사용 했습니다. 이제 가격은 OS (운영 체제), 크기 및 위치의 조합을 기반으로 합니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
