---
title: Azure Lab Services에서 클래스룸 랩 템플릿 관리 | Microsoft Docs
description: Azure Lab Services에서 클래스룸 랩 템플릿을 만들고 관리하는 방법을 알아봅니다.
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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: b287a67c470cc1697065838e52916c285a2233a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704447"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services에서 클래스룸 템플릿 만들기 및 관리
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 랩 사용자에게 제공하려는 구성과 정확히 일치하도록 템플릿 가상 머신을 설정합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다.

이 문서에서는 Azure Lab Services의 클래스룸 랩에서 템플릿 가상 머신을 만들고 관리하는 방법을 설명합니다. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>클래스룸 랩을 만드는 동안 템플릿 게시
먼저, 클래스룸 랩을 만드는 동안 템플릿을 설정하고 게시할 수 있습니다.

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
3. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 랩에 허용되는 최대 **사용자 수**를 지정합니다. 
    6. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 머신 사양 선택** 페이지에서 다음 단계를 수행합니다.
    1. 랩에서 만드는 VM(가상 머신)의 **크기**를 선택합니다. 
    2. VM을 만들려는 **지역**을 선택합니다. 
    3. 랩에서 VM을 만드는 데 사용할 **VM 이미지**를 선택합니다. 
    4. **다음**을 선택합니다.

        ![VM 사양 지정](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. **자격 증명 설정** 페이지에서 랩의 모든 VM에 대한 기본 자격 증명을 지정합니다. 
    1. 랩의 모든 VM에 대한 **사용자 이름**을 지정합니다.
    2. 사용자에 대한 **암호**를 지정합니다. 

        > [!IMPORTANT]
        > 사용자 이름과 암호를 적어 둡니다. 다시 표시되지 않습니다.
    3. **만들기**를 선택합니다. 

        ![자격 증명 설정](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. **템플릿 구성** 페이지에 랩 만들기 프로세스의 상태가 표시됩니다. 랩에서 템플릿을 만드는 데는 최대 20분이 걸립니다. 

    ![템플릿 구성](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 템플릿 구성이 완료되면 다음 페이지가 표시됩니다. 

    ![완료된 템플릿 페이지 구성](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 다음 단계는 이 자습서의 선택적 단계입니다. 
    1. **시작**을 선택하여 템플릿 VM을 시작합니다.
    2. **연결**을 선택하여 템플릿 VM에 연결합니다. 
    3. 템플릿 VM에 소프트웨어를 설치하고 구성합니다. 
    4. VM을 **중지**합니다.  
    5. 템플릿에 대한 **설명**을 입력합니다.

        ![템플릿 구성 페이지의 다음 단추](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 템플릿 페이지에서 **다음**을 선택합니다. 
10. **템플릿 게시** 페이지에서 다음 작업을 수행합니다. 
    1. 템플릿을 즉시 게시하려면 *게시한 후에는 템플릿을 수정할 수 없습니다. 이 프로세스는 한 번만 수행할 수 있으며 최대 1시간이 걸릴 수 있습니다.* 확인란을 선택하고, **게시**를 선택합니다.  

        > [!WARNING]
        > 게시한 후에는 게시를 취소할 수 없습니다. 
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사가 완료되면 템플릿 VM을 게시할 수 있습니다. 마법사가 완료된 후에 구성과 게시를 수행하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 템플릿 게시 섹션을 참조하세요.

        ![템플릿 게시](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료**를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>템플릿 제목 및 설명 설정 또는 업데이트
다음 단계에 따라 제목과 설명을 처음 설정한 후 나중에 업데이트합니다. 

1. **템플릿** 섹션에서 마우스를 템플릿의 **이름** 또는 템플릿의 **설명**으로 이동한 후 선택합니다. 
2. 템플릿에 대해 **새 이름** 또는 **새 설명**을 입력하고 **Enter** 키를 누릅니다.

    ![템플릿 이름 및 설명](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>템플릿 VM 설정 또는 업데이트
 템플릿 VM에 연결하고 필요한 소프트웨어를 설치한 후 학생에게 제공합니다. 다음 단계를 사용하여 처음으로 템플릿 VM을 설정하거나 VM을 업데이트합니다. 

1. 템플릿 가상 머신이 준비될 때까지 기다립니다. 가상 머신이 준비되면 **시작** 단추가 활성화됩니다. VM을 시작하려면 **시작**을 선택합니다.

    ![템플릿 VM 시작](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 경고를 검토하고 **시작**을 선택합니다. 

    ![템플릿 시작 - 경고](../media/how-to-create-manage-template/start-template-warning.png)
2. **템플릿** 섹션의 랩 타일에서 상태를 확인할 수 있습니다.

    ![템플릿 시작 - 상태](../media/how-to-create-manage-template/template-start-status.png)
1. 시작한 후에 VM에 연결하려면 **연결**을 선택하고 지침을 따릅니다. 

    ![템플릿 VM에 연결 또는 중지](../media/how-to-create-manage-template/connect-stop-vm.png)
1. 학생들이 랩을 사용하는 데 필요한 소프트웨어를 설치합니다(예: Visual Studio, Azure Storage Explorer 등). 
2. 템플릿 VM의 연결을 종료합니다(원격 데스크톱 세션 종료). 
3. **중지**를 선택하여 템플릿 VM을 **중지**합니다. 

## <a name="publish-the-template-vm"></a>템플릿 VM 게시  
랩을 만드는 동안 템플릿을 게시하지 않으면 나중에 게시할 수 있습니다. 게시하기 전에 템플릿 VM에 연결하고 소프트웨어를 사용하여 업데이트할 수 있습니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 

1. **템플릿** 섹션에서 **게시**를 선택합니다. 

    ![템플릿 VM 게시](../media/tutorial-setup-classroom-lab/public-access.png)
1. **템플릿 게시** 메시상지 자에서 메시지를 검토하고, **게시**를 선택합니다. 이 프로세스는 만들려는 VM 개수에 따라 다소 시간이 걸릴 수 있습니다.
    
    > [!IMPORTANT]
    > 템플릿이 게시되면 게시를 취소할 수 없습니다. 그렇지만 템플릿을 다시 게시할 수는 있습니다. 
4. 템플릿의 상태가 **게시됨**으로 변경될 때까지 기다립니다. 

    ![게시 상태](../media/how-to-create-manage-template/publish-status.png)
1. **가상 머신** 페이지로 전환하고, **할당되지 않음** 상태인 가상 머신이 보이는지 확인합니다. 이러한 VM은 아직 학생들에게 할당되지 않았습니다. VM이 만들어질 때까지 기다립니다. **중지됨** 상태일 것입니다. 이 페이지에서 학생 VM을 시작하고, VM에 연결하고, VM을 중지하고, VM을 삭제할 수 있습니다. 이 페이지에서 VM을 시작할 수도 있고, 학생들이 VM을 시작하게 할 수도 있습니다. 

    ![중지됨 상태의 가상 머신](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>템플릿 다시 게시 
템플릿을 게시한 후 템플릿 VM에 연결하고, 업데이트하고, 다시 게시할 수 있습니다. 템플릿 VM을 다시 게시하는 경우 모든 사용자 VM의 연결이 끊어지고, 업데이트된 템플릿을 기준으로 다시 만들어집니다. 

1. 랩의 대시보드 페이지에 있는 템플릿 섹션에서 **다시 게시**를 선택합니다. 

    ![다시 게시 단추](../media/how-to-create-manage-template/republish-button.png)
2. **템플릿 다시 게시** 메시지 상자에서 텍스트를 검토하고 **다시 게시**를 선택하여 계속합니다. 그렇지 않은 경우 **취소**를 선택합니다. 

    ![템플릿 메시지 다시 게시](../media/how-to-create-manage-template/republish-template-message.png)
3. **템플릿** 섹션의 타일에서 다시 게시의 상태를 확인할 수 있습니다.

    ![다시 게시의 상태](../media/how-to-create-manage-template/republish-status-publishing.png)
4. 템플릿이 게시되면 상태가 **게시됨**으로 설정됩니다. 

    ![다시 게시 성공](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
