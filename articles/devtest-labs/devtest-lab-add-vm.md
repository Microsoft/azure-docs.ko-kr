---
title: Azure DevTest Labs에서 랩에 VM 추가 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure DevTest Labs에서 랩에 가상 머신을 추가 하는 방법에 대해 알아봅니다. 사용자 지정 이미지 또는 수식 중 하나를 선택할 수 있습니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c61e6e685a52ac3658cb9b9ca37f64fc51f3ce1e
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270719"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 VM 추가
[첫 번째 VM을 이미 만든 경우](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) 미리 로드된 [Marketplace 이미지](devtest-lab-configure-marketplace-images.md)에서 만들었을 것입니다. 이제 이후의 VM을 랩에 추가하려면 [사용자 지정 이미지](devtest-lab-create-template.md) 또는 [수식](devtest-lab-manage-formulas.md)인 *기본*을 선택하면 됩니다. 이 자습서에서는 DevTest Labs에서 랩에 VM을 추가하기 위해 Azure Portal을 사용하는 방법을 설명합니다.

또한 이 문서에서는 랩에서 VM의 아티팩트를 만드는 방법을 보여 줍니다.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩에 VM을 추가하는 단계
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

        ![기본 선택](./media/tutorial-create-custom-lab/new-virtual-machine.png)

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

        ![기본 선택](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. **만들기** 를 누르고 지정된 VM을 랩에 추가합니다.

   랩 페이지에 VM의 만들기 상태가 표시됩니다. 처음에는 **만드는 중**으로, VM이 시작되면 **실행 중**으로 표시됩니다.

    ![VM 만들기 상태](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>VM에 기존 아티팩트 추가
VM을 만드는 동안 기존 아티팩트를 추가할 수 있습니다. 각 랩에는 공용 DevTest Lab 아티팩트 리포지토리의 아티팩트 및 사용자가 만들어서 사용자 고유 아티팩트 리포지토리에 추가한 아티팩트가 포함되어 있습니다.

* Windows PowerShell 스크립트 실행, Bash 명령 실행 및 소프트웨어 설치 등 Azure DevTest Labs *아티팩트*를 통해 VM을 프로비전할 때 수행하는 *작업*을 지정할 수 있습니다.
* 아티팩트 *매개 변수* 를 사용 하 여 특정 시나리오에 대 한 아티팩트를 사용자 지정할 수 있습니다.

아티팩트를 만드는 방법을 알아보려면 [DevTest Labs와 함께 사용할 사용자 고유의 아티팩트를 저작하는 방법 알아보기](devtest-lab-artifact-author.md)문서를 참조하세요.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **모든 서비스**를 선택한 다음, 목록에서 **DevTest Labs**를 선택합니다.
1. 랩 목록에서 사용하려는 VM을 포함하는 랩을 선택합니다.
1. **내 가상 머신**를 선택합니다.
1. 원하는 VM을 선택합니다.
1. **아티팩트 관리**를 선택합니다.
1. **아티팩트 적용**을 선택합니다.
1. **아티팩트 적용** 창에서 VM에 추가하려는 아티팩트를 선택합니다.
1. **아티팩트 추가** 창에서 필수 매개 변수 값 및 필요한 선택적 매개 변수를 입력합니다.
1. **추가**를 선택하여 아티팩트를 추가하고 **아티팩트 적용** 창으로 돌아갑니다.
1. VM에 필요한 만큼 계속해서 아티팩트를 추가합니다.
1. 아티팩트를 추가한 후에는 [아티팩트가 실행되는 순서를 변경](#change-the-order-in-which-artifacts-are-run)할 수 있습니다. 뒤로 돌아가서 [아티팩트를 확인 또는 수정](#view-or-modify-an-artifact)할 수도 있습니다.
1. 아티팩트 추가를 마친 경우 **적용**을 선택합니다.

## <a name="change-the-order-in-which-artifacts-are-run"></a>아티팩트가 실행되는 순서 변경
기본적으로 아티팩트 작업은 VM에 추가된 순서로 실행됩니다.
다음 단계에서는 아티팩트가 실행되는 순서를 변경하는 방법을 보여 줍니다.

1. **아티팩트 적용** 창의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.

    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 창에서 아티팩트를 원하는 순서로 끌어다 놓습니다. **참고:** 아티팩트를 끌어 놓는 데 문제가 있으면 아티팩트의 왼쪽에서 끌어 놓으세요.
1. 완료되면 **확인** 을 선택합니다.

## <a name="view-or-modify-an-artifact"></a>아티팩트를 확인 또는 수정
다음 단계에서는 아티팩트의 매개 변수를 확인 또는 수정하는 방법을 보여 줍니다.

1. **아티팩트 적용** 창의 맨 위에서 VM에 추가된 아티팩트 수를 나타내는 링크를 선택합니다.

    ![VM에 추가된 아티팩트의 수](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **선택한 아티팩트** 창에서 확인 또는 편집하려는 아티팩트를 선택합니다.
1. **아티팩트 추가** 창에서 필요한 부분을 변경하고 **확인**을 선택하여 **아티팩트 추가** 창을 닫습니다.
1. **확인**을 선택하여 **선택한 아티팩트** 창을 닫습니다.

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 저장
Azure Resource Manager 템플릿을 사용하면 반복 가능한 배포를 선언적으로 정의할 수 있습니다.
다음 단계는 생성 중인 VM에 대한 Azure Resource Manager 템플릿을 저장하는 방법을 설명합니다.
저장한 Azure Resource Manager 템플릿은 [Azure PowerShell로 새 VM을 배포](../azure-resource-manager/templates/overview.md)하는 데 사용할 수 있습니다.

1. **가상 머신** 창에서 **Azure Resource Manager 템플릿 보기**를 선택합니다.
2. **Azure Resource Manager 템플릿 보기** 창에서 템플릿 텍스트를 선택합니다.
3. 선택한 텍스트를 클립보드에 복사합니다.
4. **확인**을 선택하여 **Azure Resource Manager 템플릿 창 보기**를 닫습니다.
5. 텍스트 편집기를 엽니다.
6. 클립보드의 템플릿 텍스트를 붙여넣습니다.
7. 나중에 사용할 수 있도록 파일을 저장합니다.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* VM을 만든 후에는 해당 VM의 창에서 **연결** 을 선택하여 VM에 연결할 수 있습니다.
* [DevTest Labs VM용 사용자 지정 아티팩트 작성](devtest-lab-artifact-author.md)방법을 알아봅니다.
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색 합니다.
