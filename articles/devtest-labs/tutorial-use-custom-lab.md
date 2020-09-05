---
title: Azure DevTest Labs에서 랩에 액세스 | Microsoft Docs
description: 이 자습서에서는 Azure DevTest Labs를 사용하여 만들어진 랩에 액세스하고 가상 머신을 클레임하고, 사용한 후 클레임 취소합니다.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: b4477e0b98ef534b8170ee297edf88ac6fa62dd7
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85476447"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>자습서: Azure DevTest Labs에서 랩에 액세스
이 자습서에서는 [ 자습서: Azure DevTest Labs에서 랩 만들기](tutorial-create-custom-lab.md)에서 만들어진 랩을 사용합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩에서 VM(가상 머신) 클레임
> * VM에 연결
> * VM 클레임 취소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="access-the-lab"></a>랩 액세스

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 
3. 리소스 유형에 대해 **DevTest Labs**를 선택합니다. 
4. 랩을 선택합니다. 

    ![랩 선택](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>VM 클레임

1. **클레임할 수 있는 가상 머신** 목록에서 **...** (줄임표)를 선택하고 **머신 클레임**을 선택합니다.

    ![가상 머신 클레임](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **내 가상 머신** 목록에 VM이 있는지 확인합니다.

    ![내 가상 머신](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM에 연결

1. 목록에서 VM을 선택합니다. VM에 대한 **가상 머신 페이지**가 표시됩니다. 도구 모음에서 **연결**을 선택합니다.

    ![가상 머신에 연결](./media/tutorial-use-custom-lab/connect-button.png)
2. 다운로드한 **RDP** 파일을 하드 디스크에 저장하고 그것을 사용하여 가상 머신에 연결합니다. 이전 섹션에서 VM을 만들 때 언급한 사용자 이름과 암호를 지정합니다. 

    Linux VM에 연결하려면 VM에 대한 SSH 및/또는 RDP 액세스가 활성화되어야 합니다. RDP를 통해 Linux VM에 연결하는 단계는 [Azure에서 Linux VM에 연결하도록 원격 데스크톱 설치 및 구성](../virtual-machines/linux/use-remote-desktop.md)을 참조하세요. 

    > [!NOTE]
    > VM의 Virtual Machine 페이지로 이동하는 다른 방법이 있습니다. 다음은 그 중 일부입니다. 
    > 
    > 1. 구독의 모든 VM을 검색합니다. VM 목록에서 VM을 선택하여 **Virtual Machine** 페이지로 이동합니다.
    > 2. 리소스 그룹의 **리소스 그룹** 페이지로 이동합니다. 그런 다음, 리소스 그룹의 리소스 목록에서 VM을 선택하여 **Virtual Machine** 페이지로 이동합니다. 
    >
    > 이러한 옵션을 사용하여 나타나는 **Virtual Machine** 페이지의 도구 모음에 있는 **연결** 단추를 사용하지 마세요. 그 대신, 이 문서에서 보여드리는 것처럼 **DevTest Labs** 페이지의 **Virtual Machine** 페이지로 이동한 다음, 도구 모음의 **연결** 단추를 사용하세요.


## <a name="unclaim-the-vm"></a>VM 클레임 취소
VM 사용을 마친 후에는 다음 단계에 따라 VM을 클레임 취소합니다. 

1. 가상 머신 페이지에서 도구 모음의 **Unclaim**을 선택합니다. 

    ![VM 클레임 취소](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. VM은 클레임 취소되기 전에 종료됩니다. 알림에서 이 작업의 상태를 볼 수 있습니다.  
3. 맨 위의 이동 경로 탐색 메뉴에서 랩 이름을 클릭하여 DevTest Lab 페이지로 다시 이동합니다. 
    
    ![랩으로 다시 이동](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. 하단의 **클레임할 수 있는 가상 머신** 목록에 VM이 표시되는지 확인합니다.

    
## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure DevTest Labs를 사용하여 작성된 랩에 액세스하여 사용하는 방법을 배웠습니다. 랩에서 VM 액세스 및 사용에 대한 자세한 내용은 다음을 참조하세요. 

> [!div class="nextstepaction"]
> [방법: 랩에서 VM 사용](devtest-lab-add-vm.md)

