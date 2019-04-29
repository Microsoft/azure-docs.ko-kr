---
title: Azure DevTest Labs에서 기본 랩 정책 관리 | Microsoft Docs
description: DevTest Labs에서 랩에 대한 기본 정책(설정)을 지정하는 방법에 대해 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773845"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 대한 기본 정책 관리

Azure DevTest Labs를 통해 각 랩에 대한 정책(설정)을 관리하여 비용을 제어하고 랩에서의 낭비를 최소화할 수 있습니다. 이 문서에서는 가장 중요한 두 가지 정책을 설정하여 단일 사용자가 클레임하거나 생성할 수 있는 VM(가상 머신) 수를 제한하고 자동 종료를 구성하는 방법을 확인하여 정책을 시작합니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩 정책에 액세스
다음 단계에서는 Azure DevTest Labs에서 랩에 대한 정책을 설정하는 과정을 안내합니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.   

1. **구성 및 정책**을 선택합니다.

    ![정책 설정 창](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **구성 및 정책** 창에는 지정할 수 있는 설정 메뉴가 있습니다. 이 문서에는 **사용자당 가상 머신 수**, **자동 종료** 및 **자동 시작**에 대한 설정만 포함되어 있습니다. 나머지 설정에 대해 알아보려면 [Azure DevTest Labs에서 랩에 대한 모든 정책 관리](./devtest-lab-set-lab-policy.md)를 참조하세요. 
   
## <a name="set-virtual-machines-per-user"></a>사용자당 가상 머신을 설정합니다.
**사용자당 가상 머신** 에 대한 정책을 사용하면 개별 사용자가 만들 수 있는 최대 VM 수를 지정할 수 있습니다. 사용자 제한에 도달하면 사용자가 VM을 만들거나 클레임하는 경우 VM을 만들거나 클레임할 수 없다는 오류 메시지가 표시됩니다. 

1. 랩의 **구성 및 정책** 메뉴에서 **사용자당 가상 머신**를 선택합니다.
   
    ![사용자당 가상 머신](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. **예**를 선택하여 사용자당 VM 수를 제한합니다. 사용자당 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 사용자가 만들거나 클레임할 수 있는 최대 VM 수를 나타내는 숫자 값을 입력합니다. 

1. **예**를 선택하여 SSD(반도체 디스크)를 사용할 수 있는 사용자당 VM 수를 제한합니다. SSD를 사용할 수 있는 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 SSD를 사용하여 만들 수 있는 최대 VM 수를 나타내는 값을 입력합니다. 

1. **저장**을 선택합니다.

## <a name="set-auto-shutdown"></a>자동 종료 설정
자동 종료 정책에서는 이 랩의 VM이 종료되는 시간을 지정하여 랩 낭비를 최소화할 수 있도록 돕습니다.

1. 랩의 **구성 및 정책** 창에서 **자동 종료**를 선택합니다.
   
    ![자동 종료](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간(및 표준 시간대)을 지정합니다.

1. 지정된 자동 종료 시간 15분 전에 알림을 보내는 옵션으로 **예** 또는 **아니요**를 지정합니다. **예**를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다.

   웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 

1. **저장**을 선택합니다.

기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 특정 VM에서 이 설정을 제거하려면 VM의 관리 창을 열고 해당 **자동 종료** 설정을 변경합니다.

## <a name="set-auto-start"></a>자동 시작 설정
자동 시작 정책을 사용하면 현재 랩의 VM을 시작할 시기를 지정할 수 있습니다.  

1. 랩의 **구성 및 정책** 창에서 **자동 시작**을 선택합니다.
   
    ![자동 시작](./media/devtest-lab-set-lab-policy/auto-start.png)

2. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

3. 이 정책을 사용하도록 설정한 경우 예약 시작 시간, 표준 시간대 및 해당 시간이 적용되는 요일을 지정합니다. 

4. **저장**을 선택합니다.

사용하도록 설정해도 이 정책이 현재 랩의 VM에 자동으로 적용되지 않습니다. 이 설정을 기존 VM에 적용하려면 VM의 관리 창을 열고 해당 **자동 시작** 설정을 변경합니다.

## <a name="next-steps"></a>다음 단계

- [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md) - 다른 랩 정책을 수정하는 방법을 알아봅니다.
