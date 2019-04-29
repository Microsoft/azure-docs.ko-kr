---
title: Azure DevTest Labs에 대한 필수 아티팩트 지정 | Microsoft Docs
description: 랩의 가상 머신(VM)에서 모든 사용자 선택 아티팩트를 설치하기 전에 설치해야 하는 필수 아티팩트를 지정하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 090236ec3647c7c3e38eb862780a615f854e952b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562206"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에 대한 필수 아티팩트 지정
랩의 소유자는 랩에서 만들어진 모든 컴퓨터에 적용 되는 필수 아티팩트를 지정할 수 있습니다. 랩의 각 머신을 회사 네트워크에 연결하려는 경우의 시나리오를 가정합니다. 이 경우 각 랩 사용자는 해당 머신이 회사 도메인에 연결됐는지 확인하려면 가상 머신 생성 동안 도메인 가입 아티팩트를 추가해야 합니다. 즉, 랩 사용자가 해당 머신에 필수 아티팩트를 적용하는 것을 잊어버린 경우 기본적으로 머신을 다시 만들어야 합니다. 랩 소유자는 도메인 가입 아티팩트를 랩의 필수 아티팩트로 만듭니다. 이 단계를 수행하면 각 머신이 회사 네트워크에 연결되어 있는지 확인하여 랩 사용자의 시간과 노력을 절약해줍니다.
 
기타 필수 아티팩트에는 팀이 사용하는 일반적인 도구 또는 기본적으로 각 머신이 갖춰야 할 플랫폼 관련 보안 팩이 포함될 수 있습니다. 즉, 랩의 모든 머신이 갖춰야 하는 일반 소프트웨어가 필수 아티팩트가 됩니다. 필수 아티팩트가 적용된 머신에서 사용자 지정 이미지를 만든 다음, 해당 이미지에서 새 머신을 만들 경우 생성 동안 필수 아티팩트가 머신에 다시 적용됩니다. 이 동작은 사용자 지정 이미지가 오래됐다고 해도 해당 이미지에서 머신을 만들 때마다 생성 흐름 동안 필수 아티팩트의 최신 업데이트 버전이 해당 이미지에 적용된다는 의미이기도 합니다. 
 
매개 변수가 없는 아티팩트만 필수 아티팩트로 지원됩니다. 랩 사용자는 랩 생성 동안 추가 매개 변수를 입력할 필요가 없으므로 VM 생성 프로세스를 간소화합니다. 

## <a name="specify-mandatory-artifacts"></a>필수 아티팩트 지정
Windows 및 Linux 머신에 대한 필수 아티팩트를 각각 선택할 수 있습니다. 적용하려는 순서에 따라 이러한 아티팩트를 다시 정렬할 수도 있습니다. 

1. 랩의 홈 페이지의 **설정** 아래에서 **구성 및 정책**을 선택합니다. 
3. **외부 리소스** 아래에서 **필수 아티팩트**를 선택합니다. 
4. **Windows** 또는 **Linux** 섹션에서 **편집**을 선택합니다. 이 예제에서는 **Windows** 옵션을 사용합니다. 

    ![필수 아티팩트 페이지 - 편집 단추](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. 아티팩트를 선택합니다. 이 예제에서는 **7-Zip** 옵션을 사용합니다. 
5. **아티팩트 추가** 페이지에서 **추가**를 선택합니다. 

    ![필수 아티팩트 페이지 - 7-zip 추가](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. 다른 아티팩트를 추가하려면 문서를 선택하고 **추가**를 선택합니다. 이 예제에서는 **Chrome**을 두 번째 필수 아티팩트로 추가합니다.

    ![필수 아티팩트 페이지 - Chrome 추가](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. **필수 아티팩트** 페이지에 선택한 아티팩트 수를 지정하는 메시지가 표시됩니다. 메시지를 클릭하면 선택한 아티팩트가 표시됩니다. **저장**을 선택하여 저장합니다. 

    ![필수 아티팩트 페이지 - 아티팩트 저장](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Linux VM에 대한 필수 아티팩트를 지정하는 단계를 반복합니다. 
    
    ![필수 아티팩트 페이지 - Windows 및 Linux 아티팩트](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. 목록에서 아티팩트를 **삭제**하려면 행의 끝에서 **...(줄임표)** 를 선택하고 **삭제**를 선택합니다. 
10. 목록에서 아티팩트를 **다시 정렬**하려면 행의 시작 부분에 표시되는 **...(줄임표)** 를 선택하고 항목을 끌어 새 위치로 이동합니다. 
11. 랩에 필수 아티팩트를 저장하려면 **저장**을 선택합니다. 

    ![필수 아티팩트 페이지 - 랩에 아티팩트 저장](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. 랩에 대 한 홈 페이지로 돌아가려면 **구성 및 정책** 페이지를 닫습니다(오른쪽 위 모서리에서 **X** 선택).  

## <a name="delete-a-mandatory-artifact"></a>필수 아티팩트 삭제
랩에서 필수 아티팩트를 삭제하려면 다음 작업을 수행합니다. 

1. **설정** 아래에서 **구성 및 정책**을 선택합니다. 
2. **외부 리소스** 아래에서 **필수 아티팩트**를 선택합니다. 
3. **Windows** 또는 **Linux** 섹션에서 **편집**을 선택합니다. 이 예제에서는 **Windows** 옵션을 사용합니다. 
4. 맨 위에 있는 필수 아티팩트 수를 사용하여 메시지를 선택합니다. 

    ![필수 아티팩트 페이지 - 메시지 선택](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. **선택한 아티팩트** 페이지에서 삭제될 아티팩트에 대해 **...(줄임표)** 를 선택하고 **제거**를 선택합니다. 
    
    ![필수 아티팩트 페이지 - 아티팩트 제거](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. **확인**을 선택하여 **선택한 아티팩트** 페이지를 닫습니다. 
7. **필수 아티팩트** 페이지에서 **저장**을 선택합니다.
8. 필요한 경우 **Linux** 이미지에 대한 단계를 반복합니다. 
9. **저장**를 선택하여 모든 변경 내용을 랩에 저장합니다. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>VM을 만들 때 필수 아티팩트 보기
이제 랩 사용자는 랩에서 VM을 만드는 동안 필수 아티팩트 목록을 볼 수 있습니다. 랩 소유자가 랩에서 설정한 필수 아티팩트를 편집하거나 삭제할 수 없습니다.

1. 랩에 대한 홈 페이지의 메뉴에서 **개요**를 선택합니다.
2. 랩에 VM을 추가하려면 **+ 추가**를 선택합니다. 
3. **기본 이미지**를 선택합니다. 이 예제에서는 **Windows Server 버전 1709**를 사용합니다.
4. 선택한 필수 아티팩트 수를 사용하여 **아티팩트**에 대한 메시지를 확인합니다. 
5. **아티팩트**를 선택합니다. 
6. 랩의 구성 및 정책에서 지정한 **필수 아티팩트**가 표시되는지 확인합니다. 

    ![VM 만들기 - 필수 아티팩트](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>다음 단계
* [랩에 Git 아티팩트 리포지토리를 추가](devtest-lab-add-artifact-repo.md)하는 방법에 대해 알아봅니다.

