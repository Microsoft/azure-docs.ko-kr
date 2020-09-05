---
title: Azure Lab Services의 클래스룸 랩에 액세스 | Microsoft Docs
description: 이 자습서에서는 교육자가 설정한 클래스룸 랩의 가상 머신에 액세스합니다.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85443471"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>자습서: Azure Lab Services의 클래스룸 랩에 액세스
이 자습서에서는 사용자가 학생이 되어 클래스룸 랩의 VM(가상 머신)에 연결합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩에 등록
> * VM 시작
> * VM에 연결

## <a name="register-to-the-lab"></a>랩에 등록

1. 교육자로부터 받은 **등록 URL**로 이동합니다. 등록을 완료한 후에는 등록 URL을 사용할 필요가 없습니다. 대신 [https://labs.azure.com](https://labs.azure.com) URL을 사용합니다. Internet Explorer 11은 아직 지원되지 않습니다. 

    ![랩에 등록](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. 학교 계정을 사용하여 서비스에 로그인하여 등록을 완료합니다. 

    > [!NOTE]
    > Azure Lab Services를 사용하려면 Microsoft 계정이 필요합니다. Yahoo 또는 Google 계정과 같은 비 Microsoft 계정을 사용하여 포털에 로그인 하려는 경우 지침에 따라 Microsoft 계정에 연결할 Microsoft 계정을 만듭니다. 그런 다음, 단계에 따라 등록 프로세스를 완료합니다. 
1. 등록 후에는 액세스할 수 있는 랩의 가상 머신이 보이는지 확인합니다. 

    ![액세스 가능한 VM](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. 가상 머신이 준비될 때까지 기다립니다. VM 타일에서 다음 필드를 확인합니다.
    1. 타일의 맨 위에 **랩 이름**이 표시됩니다.
    1. 오른쪽에는 VM의 **OS(운영 체제)** 를 나타내는 아이콘이 표시됩니다. 이 예제에서는 Windows OS입니다. 
    1. 타일의 진행률 표시줄에는 할당된 [할당량 시간](how-to-configure-student-usage.md#set-quotas-for-users) 대비 사용한 시간이 표시됩니다. 이 시간은 랩의 예약된 시간 외에 사용자에게 할당된 추가 시간입니다. 
    1. 타일 아래쪽에는 VM을 시작/중지하고 VM에 연결하기 위한 아이콘/단추가 표시됩니다. 
    1. 단추 오른쪽에는 VM 상태가 표시됩니다. VM 상태가 **중지됨**인지 확인합니다. 

        ![중지됨 상태의 VM](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>VM 시작
1. 다음 이미지와 같이 첫 번째 단추를 선택하여 VM을 **시작**합니다. 이 프로세스는 다소 시간이 걸립니다.  

    ![VM 시작](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM 상태가 **실행 중**으로 설정되어 있는지 확인합니다. 

    ![실행 중 상태의 VM](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    첫 번째 단추의 아이콘이 **중지** 작업을 나타내도록 변경되었는지 확인합니다. 이 단추를 선택하여 VM을 중지할 수 있습니다. 

## <a name="connect-to-the-vm"></a>VM에 연결

1. 다음 이미지와 같이 두 번째 단추를 선택하여 랩의 VM에 **연결**합니다. 

    ![VM에 연결](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 다음 단계 중 하나를 수행합니다. 
    1. **Windows** 가상 머신의 경우 **RDP** 파일을 하드 디스크에 저장합니다. RDP 파일을 열어 가상 머신에 연결합니다. 교육자로부터 받은 **사용자 이름** 및 **암호**를 사용하여 머신에 로그인합니다. 
    3. **Linux** 가상 머신의 경우, **SSH** 또는 **RDP**(사용하도록 설정된 경우)를 사용하여 연결할 수 있습니다. 자세한 내용은 [Linux 머신에 대해 원격 데스크톱 연결을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 교육자로부터 받은 등록 링크를 사용하여 클래스룸 랩에 액세스했습니다.

랩 소유자는 랩에 누가 등록되었는지 확인하고 VM 사용량을 추적하고 싶을 것입니다. 랩 사용량을 추적하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [랩 사용량 추적](tutorial-track-usage.md) 
