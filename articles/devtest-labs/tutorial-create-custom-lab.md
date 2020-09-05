---
title: Azure DevTest Labs를 사용하여 랩 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. 랩 관리자는 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 9d016b38beb2f338a97ac862a060dc5ec3a77780
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85476566"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>자습서: Azure DevTest Labs를 사용하여 랩 설정
이 자습서에서는 Azure Portal을 사용하여 랩을 만듭니다. 랩 관리자는 조직에 랩을 설정하고, 랩에 VM을 만들고, 정책을 구성합니다. 랩 사용자(예: 개발자 및 테스터)는 랩에서 VM을 클레임하고 연결하여 사용합니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 만들기
> * 가상 머신을 랩에 추가합니다.
> * 사용자를 Lab User에 추가

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-a-lab"></a>랩 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs에서 랩을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽의 주 메뉴에서 **리소스 만들기**(목록 상단)를 선택하고, **개발자 도구**를 가리킨 다음 **DevTest Labs**를 클릭합니다. 

    ![새로운 DevTest Lab 메뉴](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. **DevTest Lab 만들기** 창에서 다음 작업을 수행합니다. 
    1. **Lab 이름**인 경우 랩 이름을 입력합니다. 
    2. **구독**인 경우 랩을 만들 구독을 선택합니다. 
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다. 
    4. **위치**인 경우 랩을 만들고자 하는 위치/지역을 선택합니다. 
    5. **만들기**를 선택합니다. 
    6. **대시보드에 고정**을 선택합니다. 랩을 만든 후에는 랩이 대시 보드에 나타납니다. 

        ![DevTest Labs의 랩 만들기 섹션](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. 알림을 확인하여 랩이 성공적으로 만들어졌는지 확인합니다. **리소스로 이동**을 선택합니다.  

    ![알림](./media/tutorial-create-custom-lab/creation-notification.png)
3. 랩에 대한 **DevTest Lab** 페이지가 표시되는지 확인합니다. 

    ![랩에 대한 홈 페이지](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>랩에 VM 추가

1. **DevTest Lab** 페이지의 도구 모임에서 **+ Add**를 선택합니다. 

    ![추가 단추](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. **기본 선택** 페이지에서 키워드(예: Windows, Ubuntu)로 검색하고 목록에서 기본 이미지 중 하나를 선택합니다. 
1. **가상 머신** 페이지에서 다음 작업을 수행합니다. 
    1. **가상 머신 이름**에서 가상 머신의 이름을 입력합니다. 
    2. **사용자 이름**의 경우 가상 머신에 대한 액세스 권한이 있는 사용자의 이름을 입력합니다. 
    3. **암호**의 경우 사용자에 대한 암호를 입력합니다. 

        ![기본 선택](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. **고급 설정** 탭을 선택합니다.
    1. **이 컴퓨터를 클레임할 수 있게 만들기**의 경우, **예**를 선택합니다.
    2. **인스턴스 개수**가 **1**로 설정되었는지 확인합니다. **2**로 설정하면 2개의 VM이 다음 이름으로 생성됩니다. `<base image name>00' and <base image name>01`. 예: `win10vm00` 및 `win10vm01`     
    3. **제출**을 선택합니다. 

        ![기본 선택](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. **클레임할 수 있는 가상 머신** 목록에 VM 상태가 표시됩니다. 가상 머신를 만드는 데 약 25분이 걸릴 수 있습니다. VM은 랩이 있는 현재 리소스 그룹의 이름으로 시작하는 별도의 Azure 리소스 그룹에 만들어집니다. 예를 들어 랩이 `labrg`에 있는 경우 VM은 리소스 그룹 `labrg3988722144002`에 만들어질 수 있습니다. 

        ![VM 만들기 상태](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. VM이 만들어지면 **클레임할 수 있는 가상 머신** 목록에 표시됩니다. 

    > [!NOTE] 
    > **고급 설정** 페이지에서 VM에 대한 공용, 개인 또는 공유 IP 주소를 구성할 수 있습니다. **공유 IP**를 사용하는 경우 Azure DevTest Labs에서는 자동으로 Windows VM에 RDP 및 Linux VM에 SSH를 사용하도록 설정합니다. **공용 IP** 주소를 통해 VM을 만드는 경우 RDP 및 SSH는 DevTest Labs에서 변경 없이 사용하도록 설정됩니다.  

## <a name="add-a-user-to-the-lab-user-role"></a>사용자를 Lab User에 추가

1. 왼쪽 메뉴에서 **구성 및 정책**을 선택합니다. 

    ![구성 및 정책](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. 메뉴에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 선택합니다. 

    ![역할 할당 추가 - 단추](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. **권한 추가** 페이지에서 다음 작업을 수행합니다.
    1. **역할**에 대해 **DevTest Labs User**를 선택합니다. 
    2. 추가하려는 **사용자**를 선택합니다. 
    3. **저장**을 선택합니다.

        ![사용자 추가](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>리소스 정리
다음 자습서에서는 랩 사용자가 랩에서 VM을 클레임하고 연결하는 방법을 보여줍니다. 해당 자습서를 하고 싶지 않고 이 자습서의 일부로 작성된 리소스를 정리하려면 다음 단계를 수행합니다. 

1. Azure Portal의 목록에서 **리소스 그룹**을 선택합니다. 

    ![리소스 그룹](./media/tutorial-create-custom-lab/resource-groups.png)
1. 랩을 만든 리소스 그룹을 선택합니다. 
1. 도구 모음에서 **리소스 그룹 삭제**를 선택합니다. 리소스 그룹을 삭제하면 랩을 포함하여 그룹 내 모든 리소스를 삭제합니다. 

    ![랩 리소스 그룹](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. `<your resource group name><random numbers>`이란 이름으로 만들어진 추가 리소스 그룹을 삭제하려면 이 단계를 반복합니다. 예: `splab3988722144001` VM은 랩이 존재하는 리소스 그룹이 아니라 이 리소스 그룹에서 만들어집니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 VM을 사용하여 랩을 만들고 사용자에게 랩에 액세스할 수 있는 권한을 부여합니다. 랩 사용자로 랩에 액세스하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [자습서: 랩에 액세스](tutorial-use-custom-lab.md)

