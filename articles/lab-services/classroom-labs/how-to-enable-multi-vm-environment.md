---
title: 다중 VM 환경을 Azure Lab Services를 사용 하도록 설정 | Microsoft Docs
description: Azure Lab Services의 클래스 룸 랩의 템플릿에 가상 머신 내에서 여러 가상 컴퓨터를 사용 하 여 environment를 만드는 방법에 알아봅니다.
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
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 6faf32232c42f863bff52fdfb3c0714aee8e9b88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702438"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>템플릿 클래스 룸 랩의 VM 내에서 여러 Vm을 사용 하 여 환경 만들기
현재 Azure Lab Services를 사용 하는 랩의 가상 머신 템플릿 설정 및 단일 복사본을 각 사용자를 사용할 수 있도록 수 있습니다. 하지만 네트워크를 통해 여러 가상 컴퓨터가 서로 통신할 수 있습니다 environment를 사용 하 여 각 학생 들에 게 제공 해야 서버나 방화벽을 설정 하는 방법에는 IT 클래스를 가르치는 교수 인 경우.

중첩 된 가상화를 사용 하는 랩의 템플릿에 가상 머신 내에서 다중 VM 환경을 만들 수 있습니다. 서식 파일을 게시 내의 여러 Vm을 사용 하 여 설정 하는 가상 머신과 랩의 각 사용자를 제공 합니다.

## <a name="what-is-nested-virtualization"></a>중첩 된 가상화 란?
중첩 된 가상화를 사용 하면 가상 머신 내에서 가상 컴퓨터를 만들 수 있습니다. 중첩 된 가상화에서 Hyper-v를 통해 수행 됩니다와 Windows Vm에서 사용할 수만 있습니다.

중첩 된 가상화에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure에서 중첩 된 가상화](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM에서 중첩 된 가상화를 사용 하는 방법](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure Lab Services의 중첩 된 가상화 사용
중요 한 단계는 다음과 같습니다.

1. 만들기는 **큰** 크기 조정 **Windows** 랩에 대 한 템플릿 컴퓨터. 
2. 연결 하 고 [중첩 된 가상화 사용](../../virtual-machines/windows/nested-virtualization.md)합니다.


다음 절차는 자세한 단계를 제공합니다. 

1. 하면 아직 없는 경우 랩 계정을 만듭니다. 자세한 내용은 [자습서: Azure Lab Services로 랩 계정 설정](tutorial-setup-lab-account.md)합니다.
2. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 
3. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다. 
4. **새 랩** 창에서 다음 작업을 수행합니다. 
    1. 랩에 대한 **이름**을 지정합니다. 
    2. 랩에서 **가상 머신의 최대 수**를 지정합니다. 랩을 만든 후 또는 기존 랩에서 VM 수를 늘리거나 줄일 수 있습니다. 자세한 내용은 [랩의 VM 수 업데이트](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)를 참조하세요.
    6. **저장**을 선택합니다.

        ![클래스룸 랩 만들기](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **가상 머신 사양 선택** 페이지에서 다음 단계를 수행합니다.
    1. 선택 **Large** (Vm)는 랩에서 만들 가상 머신의 크기에 대 한 합니다. 현재 큰 크기에만 중첩 된 가상화를 지원합니다.
    2. 선택 된 가상 머신 이미지를 **Windows 이미지**합니다. 중첩 된 가상화는 Windows 컴퓨터에서 사용할 수만 있습니다. 
    3. **다음**을 선택합니다.

        ![VM 사양 지정](../media/how-to-enable-multi-vm-environment/large-windows-vm.png)    
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
8. 에 **구성 템플릿을** 페이지에서 **Connect** 템플릿 중첩 된 가상화를 구성 하는 VM에 연결할 합니다. 구성할 수도 있습니다 나중에이 마법사의 단계를 완료 합니다. 
9. 템플릿에 가상 머신 내에서 중첩 된 가상화를 설정 하 고 여러 virtual machines를 사용 하 여 가상 네트워크를 구성 합니다. 자세한 단계별 지침은 [Azure VM에서 중첩 된 가상화를 사용 하도록 설정 하는 방법을](../../virtual-machines/windows/nested-virtualization.md)합니다. 요약 단계는 다음과 같습니다. 
    1. 템플릿 가상 컴퓨터에서 Hyper-v 기능을 사용 합니다.
    2. 중첩 된 가상 컴퓨터에 대 한 인터넷 연결을 사용 하 여 내부 가상 네트워크 설정
    3. Hyper-v 관리자를 통해 가상 머신 만들기
    4. 가상 컴퓨터에 IP 주소를 할당 합니다.
10. 템플릿 페이지에서 **다음**을 선택합니다. 
11. **템플릿 게시** 페이지에서 다음 작업을 수행합니다. 
    1. 템플릿을 즉시 게시하려면 **게시**를 선택합니다.  

        > [!WARNING]
        > 게시한 후에는 게시를 취소할 수 없습니다. 
    2. 나중에 게시하려면 **나중을 위해 저장**을 선택합니다. 마법사가 완료되면 템플릿 VM을 게시할 수 있습니다. 마법사가 완료된 후에 템플릿을 구성하고 게시하는 방법에 대한 자세한 내용은 [클래스룸 랩을 관리하는 방법](how-to-manage-classroom-labs.md) 문서의 [템플릿 게시](how-to-create-manage-template.md#publish-the-template-vm) 섹션을 참조하세요.

        ![템플릿 게시](../media/how-to-enable-multi-vm-environment/publish-template-page.png)
11. 템플릿 **게시 진행률**이 표시됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다. 

    ![템플릿 게시 - 진행률](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 템플릿이 성공적으로 게시되면 다음 페이지가 표시됩니다. **완료**를 선택합니다.

    ![템플릿 게시 - 성공](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 랩의 **대시보드**가 표시됩니다. 
    
    ![클래스룸 랩 대시보드](../media/how-to-enable-multi-vm-environment/dashboard.png)


## <a name="next-steps"></a>다음 단계

이제 각 사용자에 내 다중 VM 환경을 포함 하는 단일 가상 컴퓨터를 가져옵니다. 랩에 사용자를 추가 하 여 등록 링크를 전송 하는 방법을 알아보려면 다음 문서를 참조 합니다. [랩에 사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)합니다.