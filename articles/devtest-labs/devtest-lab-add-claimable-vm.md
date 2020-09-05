---
title: Azure DevTest Labs에서 클레임 할 수 있는 Vm 만들기 및 관리 Microsoft Docs
description: Azure Portal를 사용 하 여 Azure DevTest Labs에서 클레임 할 수 있는 가상 컴퓨터를 추가 하는 방법을 알아보고, 가상 컴퓨터를 클레임/클레임 취소 하기 위해 다음 프로세스를 참조 하세요.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1fe949d7104fe6437087e77c403cc0d921ebd025
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270982"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Azure DevTest Labs에서 클레임할 수 있는 VM 생성 및 관리
클레임할 수 있는 VM은 [표준 VM을 추가](devtest-lab-add-vm.md)하는 것과 유사한 방식으로([사용자 지정 이미지](devtest-lab-create-template.md), [수식](devtest-lab-manage-formulas.md) 또는 [Marketplace 이미지](devtest-lab-configure-marketplace-images.md)를 *기반*으로) 랩에 추가합니다. 이 자습서에서는 Azure Portal을 사용하여 클레임할 수 있는 VM을 DevTest Labs의 랩에 추가하는 단계를 안내하고 VM을 클레임 및 클레임 취소하기 위해 따라야 하는 프로세스를 보여줍니다.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 클레임할 수 있는 VM을 추가하는 단계
1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, **DEVOPS** 섹션에서 **DevTest Labs**를 선택합니다. **DEVOPS** 섹션에서 **DevTest Labs** 옆에 있는 *(별표)를 선택하는 경우입니다. 이 작업을 수행하면 다음에 쉽게 액세스할 수 있도록 **DevTest Labs**가 왼쪽 탐색 메뉴에 추가됩니다. 그러면 왼쪽 탐색 메뉴에서 **DevTest Labs**를 선택할 수 있습니다.

    ![모든 서비스 - DevTest Labs 선택](./media/devtest-lab-create-lab/all-services-select.png)
1. 랩 목록에서 VM을 만들려는 랩을 선택합니다.
2. 랩의 **개요** 페이지에서 **+ 추가**를 선택합니다.

    ![VM 단추 추가](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. **기본 선택** 페이지에서 VM에 대한 Marketplace 이미지를 선택합니다.
1. **가상 머신** 페이지의 **기본 설정** 탭에서 다음 작업을 수행합니다.
    1. **가상 머신 이름** 텍스트 상자에서 VM에 대한 이름을 입력합니다. 텍스트 상자에는 자동 생성된 고유 이름이 미리 채워져 있습니다. 이 이름은 뒤에 고유한 3자리 숫자가 붙는 이메일 주소 내의 사용자 이름에 해당합니다. 이 기능을 사용하면 컴퓨터를 만들 때마다 컴퓨터 이름을 생각하고 입력하는 시간이 절약됩니다. 이 자동으로 채워진 필드를 재정의하려는 경우 선택한 이름으로 재정의할 수 있습니다. VM에 대해 자동으로 채워진 이름을 재정의하려면 **가상 머신 이름** 텍스트 상자에 이름을 입력합니다.
    2. 가상 머신에서 관리자 권한이 부여된 **사용자 이름**을 입력합니다. 컴퓨터에 대한 **사용자 이름**은 자동으로 생성된 고유한 이름으로 미리 채워집니다. 이 이름은 이메일 주소 내의 사용자 이름에 해당합니다. 이 기능을 사용하면 새 컴퓨터를 만들 때마다 사용자 이름을 결정하는 시간이 절약됩니다. 다시 이 자동으로 채워진 필드를 재정의하려는 경우 선택한 사용자 이름으로 재정의할 수 있습니다. 사용자 이름에 대해 자동으로 채워진 값을 재정의하려면 **사용자 이름** 텍스트 상자에 값을 입력합니다. 이 사용자에게 가상 머신에서 **관리자** 권한이 부여됩니다.
    3. 랩에서 첫 번째 VM을 만드는 경우 사용자에 대한 **암호**를 입력합니다. 랩과 연결 된 Azure 키 자격 증명 모음에 이 암호를 기본 암호로 저장하려면 **기본 암호로 저장**을 선택합니다. 기본 암호는 **VmPassword**라는 이름으로 키 자격 증명 모음에 저장됩니다. 랩에서 후속 VM을 만들려는 경우 **암호**에 대해 **VmPassword**가 자동으로 선택됩니다. 값을 재정의하려면 **저장된 비밀 사용** 확인란을 선택 취소하고 암호를 입력합니다.

        먼저 키 자격 증명 모음에 비밀을 저장한 다음, 랩에서 VM을 만드는 동안 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음에 비밀 저장](devtest-lab-store-secrets-in-key-vault.md)을 참조하세요. 키 자격 증명 모음에 저장된 암호를 사용하려면 **저장된 비밀 사용**을 선택하고 비밀(암호)에 해당하는 키 값을 지정합니다.
    4. **기타 옵션** 섹션에서 **크기 변경**을 선택합니다. 만드는 VM의 프로세서 코어 수, RAM 크기 및 하드 드라이브 크기를 지정하는 미리 정의된 항목 중 하나를 선택합니다.
    5. **아티팩트 추가 또는 제거**를 선택합니다. 기본 이미지에 추가하려는 아티팩트를 선택하고 구성합니다.
    **참고:** DevTest Lab을 처음 접하거나 아티팩트를 구성 중인 경우 [VM에 기존 아티팩트 추가](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 섹션을 참조한 다음 완료되면 여기로 돌아옵니다.
2. 위쪽에 있는 **고급 설정** 탭으로 전환하고 다음 작업을 수행합니다.
    1. VM이 있는 가상 네트워크를 변경하려면 **VNet 변경**을 선택합니다.
    2. 서브넷을 변경하려면 **서브넷 변경**을 선택합니다.
    3. VM의 IP 주소가 **공용, 프라이빗 또는 공유**인지 여부를 지정합니다.
    4. VM을 자동으로 삭제하려면 **만료 날짜 및 시간**을 지정합니다.
    5. 랩 사용자가 VM을 클레임할 수 있도록 하려면 **이 머신을 클레임 가능하도록 설정** 옵션에 대해 **예**를 선택합니다.
    6. 랩 사용자가 사용할 수 있게 하려는 **VM의 인스턴스 수**를 지정합니다.
3. **만들기** 를 누르고 지정된 VM을 랩에 추가합니다.

   랩 페이지에 VM의 만들기 상태가 표시됩니다. 처음에는 **만드는 중**으로, VM이 시작되면 **실행 중**으로 표시됩니다.

> [!NOTE]
>  [Azure Resource Manager 템플릿](devtest-lab-create-environment-from-arm.md)을 통해 랩 VM을 배포하는 경우 속성 섹션에서 **allowClaim** 속성을 true로 설정하면 클레임할 수 있는 VM을 만들 수 있습니다.


## <a name="using-a-claimable-vm"></a>클레임할 수 있는 VM 사용

다음 단계 중 하나를 수행하면 “클레임할 수 있는 가상 머신” 목록에서 원하는 VM을 클레임할 수 있습니다.

* 랩의 "개요" 창 아래쪽에 있는 "클레임할 수 있는 가상 머신" 목록에서 VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **컴퓨터 클레임**을 선택합니다.

  ![클레임할 수 있는 특정 VM을 요청합니다.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* "개요" 창 맨 위에서 **모두 클레임**을 선택합니다. 클레임할 수 있는 VM 목록에서 임의의 가상 머신이 할당됩니다.

  ![클레임할 수 있는 임의의 VM을 요청합니다.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


사용자가 VM을 클레임 한 후 DevTest Labs는 컴퓨터를 시작 하 고 랩 사용자의 "내 가상 머신" 목록으로 이동 합니다. 즉, 랩 사용자는 이제이 컴퓨터에 대 한 소유자 권한을 갖게 됩니다. 이 단계에 필요한 시간은 시작 시간 및 클레임 이벤트 중 수행 되는 다른 사용자 지정 작업에 따라 달라질 수 있습니다. 요청 된 후에는 더 이상 클레임 할 수 있는 풀에서 컴퓨터를 사용할 수 없습니다.  

## <a name="unclaim-a-vm"></a>VM 클레임 취소

사용자가 클레임된 VM 사용을 마치고 다른 사람이 사용할 수 있도록 만들려면 다음 단계 중 하나를 수행하여 클레임된 VM을 클레임할 수 있는 가상 머신 목록에 반환할 수 있습니다.

- "내 가상 머신" 목록에서 목록에 있는 VM 중 하나를 마우스 오른쪽 단추로 클릭하거나 줄임표(...)를 선택하고 **Unclaim**(클레임 취소)을 선택합니다.

  ![VM 목록에서 VM을 클레임 취소합니다.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- "내 가상 머신" 목록에서 VM을 선택하여 관리 창을 연 다음 위쪽 메뉴 모음에서 **Unclaim**(클레임 취소)를 선택합니다.

  ![VM의 관리 창에서 VM 클레임을 취소합니다.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

사용자가 VM에 대 한 클레임을 해제 하면 해당 랩 VM에 대 한 소유자 권한이 더 이상 없으며, 풀로 반환 된 상태에서 다른 랩 사용자가 해당 vm에 대 한 요청을 받을 수 있습니다. 

### <a name="transferring-the-data-disk"></a>데이터 디스크 전송
클레임할 수 있는 VM에 연결된 데이터 디스크가 있고 사용자가 그에 대한 클레임을 취소하면 데이터 디스크기 VM에 유지됩니다. 그런 다음 또 다른 사용자가 해당 VM을 클레임하면 이 새로운 사용자가 VM은 물론 데이터 디스크를 클레임합니다.

이것을 "데이터 디스크 전송"이라고 합니다. 그러면 데이터 디스크가 새 사용자의 **내 데이터 디스크** 목록에 표시되어 해당 사용자가 관리할 수 있게 됩니다.

![데이터 디스크 클레임을 취소합니다.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>다음 단계
* 생성된 후 해당 관리 창에서 **연결**을 선택하여 VM에 연결할 수 있습니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색 합니다.
