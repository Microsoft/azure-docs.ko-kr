---
title: Azure DevTest Labs의 랩에서 VM 다시 시작 | Microsoft Docs
description: Azure DevTest Labs에서 가상 머신을 다시 시작하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864431"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에서 VM 다시 시작
이 문서의 단계에 따라 DevTest Labs에서 가상 머신을 빠르고 쉽게 다시 시작할 수 있습니다. VM을 다시 시작하기 전에 다음을 고려하세요.

- 다시 시작 기능을 사용하려면 VM이 실행 중이어야 합니다.
- 다시 시작을 수행할 때 사용자가 실행 중인 VM에 연결된 경우 백업이 시작된 후 VM에 다시 연결해야 합니다.
- VM을 다시 시작할 때 아티팩트가 적용 중인 경우 아티팩트가 적용되지 않을 수 있다는 경고가 표시됩니다.

    ![아티팩트를 적용하는 중 다시 시작할 때 경고](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > 아티팩트를 적용하는 중 VM이 중지된 경우에는 VM 다시 시작 기능을 사용하여 문제를 해결할 수도 있습니다.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에서 VM을 다시 시작하는 단계
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 다시 시작할 VM이 포함된 랩을 선택합니다.
1. 왼쪽 패널에서 **내 Virtual Machine**을 선택합니다.
1. VM 목록에서 실행 중인 VM을 선택합니다.
1. VM 관리 창의 맨 위에서 **다시 시작**을 선택합니다.

    ![VM 다시 시작 단추](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. 창 오른쪽 맨 위에 있는 **알림** 아이콘을 선택하여 다시 시작 상태를 모니터링합니다.

    ![VM 다시 시작 상태 보기](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

**내 Virtual Machines** 목록에서 줄임표(...)를 선택하여 실행 중인 VM을 다시 시작할 수도 있습니다.

![줄임표를 통해 VM 다시 시작](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>다음 단계
* 다시 시작되면 해당 관리 창에서 **연결**을 선택하여 VM에 다시 연결할 수 있습니다.
* [DevTest Labs Azure Resource Manager quickStart template gallery](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)(DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리)를 탐색합니다.
