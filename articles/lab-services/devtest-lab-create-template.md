---
title: VHD 파일에서 Azure DevTest Labs 사용자 지정 이미지 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지를 만드는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296052"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>VHD 파일에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 Azure Portal을 사용하여 VHD 파일에서 사용자 지정 이미지를 만드는 과정을 안내합니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 기본 창에서 **구성 및 정책**을 선택합니다. 

1. **구성 및 정책** 창에서 **사용자 지정 이미지**를 선택합니다.

1. **사용자 지정 이미지** 창에서 **+추가**를 선택합니다.

    ![사용자 지정 이미지 추가](./media/devtest-lab-create-template/add-custom-image.png)

1. 사용자 지정 이미지의 이름을 입력합니다. 이 이름은 VM을 만들 때 기본 이미지 목록에 표시됩니다.

1. 사용자 지정 이미지에 대한 설명을 입력합니다. 이 설명은 VM을 만들 때 기본 이미지 목록에 표시됩니다.

1. **OS 형식**의 경우 **Windows** 또는 **Linux**를 선택합니다.

    - **Windows**를 선택하는 경우 확인란을 통해 컴퓨터에서 *Sysprep*이 실행되었는지를 지정합니다. 
    - **Linux**를 선택하는 경우 확인란을 통해 컴퓨터에서 *deprovision*이 실행되었는지를 지정합니다. 

1. 드롭다운 메뉴에서 **VHD**를 선택합니다. 새 사용자 지정 이미지를 만드는 데 사용될 VHD입니다. 필요한 경우 **PowerShell을 사용하여 VHD 업로드**를 선택합니다.

1. 사용자 지정 이미지를 만드는 데 사용되는 이미지가 라이선스 이미지(Microsoft에서 게시)가 아닌 경우 계획 이름, 계획 제안 및 계획 게시자를 입력할 수도 있습니다.

   - **계획 이름:** 이 사용자 지정 이미지 생성은 Marketplace 이미지 (SKU)의 이름 입력 
   - **계획 제품:** 이 사용자 지정 이미지를 만들 Marketplace 이미지의 제품 (제안)를 입력 합니다. 
   - **계획 게시자:** 이 사용자 지정 이미지를 만들 Marketplace 이미지의 게시자를 입력 합니다.

   > [!NOTE]
   > 사용자 지정 이미지를 만드는 데 사용되는 이미지가 **라이선스 이미지가 아닌 경우** 이러한 필드는 비어 있으며, 해당 필드를 선택하면 채울 수 있습니다. **라이선스 이미지인 경우** 계획 정보가 자동으로 필드에 채워집니다. 이 경우 변경하려고 하면 경고 메시지가 표시됩니다.
   >
   >

1. **확인** 을 선택하여 사용자 지정 이미지를 만듭니다.

몇 분 후 사용자 지정 이미지가 만들어지고 랩의 저장소 계정 내에 저장됩니다. 랩 사용자가 새 VM을 만들려는 경우 기본 이미지의 목록에서 이미지를 사용할 수 있습니다.

![기본 이미지 목록에서 사용할 수 있는 사용자 지정 이미지](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>다음 단계

- [랩에 VM 추가](./devtest-lab-add-vm.md)
