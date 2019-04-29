---
title: Azure DevTest Labs의 랩에서 랩 또는 VM 삭제 | Microsoft Docs
description: 이 문서에서는 랩에서 랩 또는 VM을 삭제하는 방법에 대해 설명합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 9634c70566aba21bdd28ee016c9fa94464ec9c1b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127415"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에서 랩 또는 VM 삭제
이 문서에서는 랩에서 랩 또는 VM을 삭제하는 방법에 대해 설명합니다.

## <a name="delete-a-lab"></a>랩 삭제
리소스 그룹에서 DevTest Labs 인스턴스를 삭제하는 경우 DevTest Labs 서비스에서 수행하는 작업은 다음과 같습니다. 

- 랩을 만들 때 자동으로 만들어진 모든 리소스가 자동으로 삭제됩니다. 리소스 그룹 자체는 삭제되지 않습니다. 이 리소스 그룹의 리소스를 수동으로 만든 경우에는 서비스에서 해당 리소스를 삭제하지 않습니다. 
- 랩의 모든 VM과 이러한 VM과 관련된 리소스 그룹은 자동으로 삭제됩니다. 랩에서 VM을 만들면 서비스에서 별도의 리소스 그룹에 있는 해당 VM에 대한 리소스(디스크, 네트워크 인터페이스, 공용 IP 주소 등)를 만듭니다. 그러나 이러한 리소스 그룹에 추가 리소스를 수동으로 만들면 DevTest Labs 서비스에서 해당 리소스와 리소스 그룹을 삭제하지 않습니다. 

랩을 삭제하려면 다음 작업을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택하고, 서비스 유형에 대해 **DevTest Labs**를 선택한 다음, 랩을 선택합니다.

    ![랩 선택](media/devtest-lab-delete-lab-vm/select-lab.png)
3. **DevTest Lab** 페이지의 도구 모음에서 **삭제**를 클릭합니다. 

    ![삭제 단추](media/devtest-lab-delete-lab-vm/delete-button.png)
4. **확인** 페이지에서 랩의 **이름**을 입력하고 **삭제**를 선택합니다. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. 작업 상태를 보려면 **알림** 아이콘(벨)을 선택합니다. 

    ![공지](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>랩에서 VM 삭제
랩에서 VM을 삭제하면 랩을 만들 때 만들어진 일부 리소스가 삭제됩니다(전체가 아님). 삭제되지 않는 리소스는 다음과 같습니다. 

-   기본 리소스 그룹의 키 자격 증명 모음
-   VM 리소스 그룹의 가용성 집합, 부하 분산 장치, 공용 IP 주소. 이러한 리소스는 리소스 그룹의 여러 VM에서 공유합니다. 

VM과 연결된 가상 머신, 네트워크 인터페이스 및 디스크가 삭제됩니다. 

랩에서 VM을 삭제하려면 다음 작업을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택하고, 서비스 유형에 대해 **DevTest Labs**를 선택한 다음, 랩을 선택합니다.

    ![랩 선택](media/devtest-lab-delete-lab-vm/select-lab.png)
3. VM 목록에 있는 VM에 대해 **...(줄임표)** 를 선택하고 **삭제**를 선택합니다. 

    ![메뉴에서 VM 삭제](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. **확인** 대화 상자에서 **확인**을 선택합니다. 
5. 작업 상태를 보려면 **알림** 아이콘(벨)을 선택합니다. 

**Virtual Machine 페이지**에서 VM을 삭제하려면 다음 그림과 같이 도구 모음에서 **삭제**를 선택합니다.

![VM 페이지에서 VM 삭제](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>다음 단계
랩을 만들려면 다음 문서를 참조하세요. 

- [랩 만들기](devtest-lab-create-lab.md)
- [랩에 VM 추가](devtest-lab-add-vm.md)