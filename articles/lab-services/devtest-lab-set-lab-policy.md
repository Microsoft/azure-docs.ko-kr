---
title: Azure DevTest Labs에서 랩 정책 관리 | Microsoft 문서
description: VM 크기, 사용자당 최대 VM 수 및 자동 종료와 같은 랩 정책을 정의하는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636522"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 대한 모든 정책 관리

Azure DevTest Labs에서는 각 랩의 정책(설정)을 관리하여 랩에서 비용을 관리하고 낭비를 최소화할 수 있습니다. 이 문서에서는 각 정책을 설정하는 방법에 대해 단계별로 설명합니다.  

## <a name="set-allowed-virtual-machine-sizes"></a>허용된 가상 머신 크기를 설정합니다.
허용 VM 크기를 설정하는 정책은 랩에서 허용되는 VM 크기를 지정함으로써 랩의 낭비가 최소화되도록 돕습니다. 이 정책이 활성화되면 이 목록의 VM 크기만 사용하여 VM을 만들 수 있습니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩을 선택한 다음 **구성 및 정책**을 선택합니다.

    ![랩의 구성 및 정책에 액세스](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. 랩의 **구성 및 정책** 창에서 **허용된 가상 머신 크기**를 선택합니다.
   
    ![허용된 가상 머신 크기](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

1. 이 정책을 사용하도록 설정한 경우 랩에서 만들 수 있는 하나 이상의 VM 크기를 선택합니다.

1. **저장**을 선택합니다.

## <a name="set-virtual-machines-per-user"></a>사용자당 가상 머신을 설정합니다.
**사용자당 가상 머신**에 대한 정책을 사용하면 개별 사용자가 만들 수 있는 VM 수를 지정할 수 있습니다. 사용자 제한에 도달하면 사용자가 VM을 만들거나 클레임하는 경우 VM을 만들거나 클레임할 수 없다는 오류 메시지가 표시됩니다. 

1. 랩의 **구성 및 정책** 창에서 **사용자당 가상 머신**을 선택합니다.
   
    ![사용자당 가상 머신](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. **예**를 선택하여 사용자당 VM 수를 제한합니다. 사용자당 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 사용자가 만들거나 클레임할 수 있는 VM 수를 나타내는 숫자 값을 입력합니다. 

1. **예**를 선택하여 SSD(반도체 디스크)를 사용할 수 있는 사용자당 VM 수를 제한합니다. SSD를 사용할 수 있는 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 SSD를 사용하여 만들 수 있는 VM 수를 나타내는 값을 입력합니다. 

1. **저장**을 선택합니다.

## <a name="set-virtual-machines-per-lab"></a>랩당 가상 머신을 설정합니다.
**랩당 가상 머신**에 대한 정책을 사용하면 현재 랩에 대해 만들 수 있는 VM 수를 지정할 수 있습니다. 랩 제한에 도달하면 사용자가 VM을 만들려고 하는 경우 VM을 만들 수 없다는 오류 메시지가 표시됩니다. 

1. 랩의 **구성 및 정책** 창에서 **랩당 가상 머신**를 선택합니다.
   
    ![랩당 가상 머신](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. **예**를 선택하여 랩당 VM 수를 제한합니다. 랩당 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 사용자가 만들거나 클레임할 수 있는 VM 수를 나타내는 숫자 값을 입력합니다. 

1. **예**를 선택하여 SSD(반도체 디스크)를 사용할 수 있는 사용자당 VM 수를 제한합니다. SSD를 사용할 수 있는 VM 수를 제한하지 않으려면 **아니요**를 선택합니다. **예**를 선택하면 SSD를 사용하여 만들 수 있는 VM 수를 나타내는 값을 입력합니다. 

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

## <a name="set-auto-shutdown-policy"></a>자동 종료 정책 설정
랩 소유자로서 랩에서 모든 VM에 대한 종료 일정을 구성할 수 있습니다. 이 작업을 수행하면 사용되지 않는(유휴 상태) 머신의 실행 비용을 절약할 수 있습니다. 모든 랩 VM에서 중앙 집중식으로 종료 정책을 적용할 수 있을 뿐만 아니라 랩 사용자가 개별 머신에 대한 일정을 설정하는 수고를 줄일 수도 있습니다. 이 기능을 사용하면 랩 사용자에게 모든 권한에 대한 권한 없음 제공에서 시작하는 랩 일정에 대한 정책을 설정할 수 있습니다. 랩 소유자로서 다음 단계를 수행하여 이 정책을 구성할 수 있습니다.

1. 랩의 홈 페이지에서 **구성 및 정책**을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료 정책**을 선택합니다.
3. 옵션 중 하나를 선택합니다. 다음 섹션에서는 이러한 옵션에 대 한 자세한 정보를 제공 합니다. 정책 설정에는 기존 Vm 아니라 lab에서 생성 하는 새 Vm에만 적용 됩니다. 

    ![자동 종료 정책 옵션](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>사용자가 일정을 설정하고 옵트아웃할 수 있음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 랩 사용자에게 해당 VM의 자동 종료 일정에 대한 모든 권한을 부여합니다. 랩 사용자는 해당 VM 자동 종료 일정 페이지에 변경 내용이 표시되지 않습니다.

![자동 종료 정책 옵션 - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>사용자가 일정을 설정하지만 옵트아웃할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책을 옵트아웃할 수 없습니다. 이 옵션을 통해 랩의 모든 머신이 자동 종료 일정에 포함됩니다. 랩 사용자는 해당 VM의 자동 종료 일정을 업데이트하고, 종료 알림을 설정할 수 있습니다.

![자동 종료 정책 옵션 - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>랩 관리자가 설정한 일정을 사용자가 제어할 수 없음
이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 없습니다. 이 옵션은 랩 관리자에게 랩의 모든 머신에 대한 일정의 완전한 권한을 제공합니다. 랩 사용자는 해당 VM에 대한 자동 종료 알림만 설정할 수 있습니다.

![자동 종료 정책 옵션 - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>자동 시작 설정
자동 시작 정책을 사용하면 현재 랩의 VM을 시작할 시기를 지정할 수 있습니다.  

1. 랩의 **구성 및 정책** 창에서 **자동 시작**을 선택합니다.
   
    ![자동 시작](./media/devtest-lab-set-lab-policy/auto-start.png)

2. 이 정책을 사용하도록 설정하려면 **설정**을 선택하고 사용하지 않도록 설정하려면 **해제**를 선택합니다.

3. 이 정책을 사용하도록 설정한 경우 예약 시작 시간, 표준 시간대 및 해당 시간이 적용되는 요일을 지정합니다. 

4. **저장**을 선택합니다.

사용하도록 설정해도 이 정책이 현재 랩의 VM에 자동으로 적용되지 않습니다. 이 설정을 특정 VM에 적용하려면 VM의 관리 창을 열고 해당 **자동 시작** 설정을 변경합니다.

## <a name="set-expiration-date"></a>만료 날짜 설정
[VM을 만들 때](devtest-lab-add-vm.md) 만료 날짜를 설정할 수 있습니다. **고급 설정**에서 달력 아이콘을 선택하고 VM이 자동으로 삭제될 날짜를 지정합니다. 기본적으로 VM은 만료되지 않습니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
랩에 대한 다양한 VM 정책 설정을 정의 및 적용한 후에는 다음 작업을 수행하세요.

* [공유 IP 주소 이해](devtest-lab-shared-ip.md) - DevTest Labs에서 공유 IP 주소를 사용하여 랩 VM에 연결하는 데 필요한 공용 IP 주소 수를 최소화하는 방법을 설명합니다.
* [비용 관리 구성](devtest-lab-configure-cost-management.md) - **월간 예상 원가 추세** 차트 사용 방법을 보여 줍니다  
  현재까지의 예상 비용과 예상되는 월말 비용을 봅니다.
* [사용자 지정 이미지 만들기](devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 Marketplace 이미지 중에서 기본 이미지를 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
* [마켓플레이스 이미지 구성](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs에서 Azure Marketplace 이미지에 기반하여 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
* [랩에서 VM 만들기](devtest-lab-add-vm.md) - 기본 이미지(사용자 지정 또는 Marketplace 이미지)에서 VM을 만드는 방법 및 VM에서 아티팩트 작업 방법에 대해 설명합니다.

