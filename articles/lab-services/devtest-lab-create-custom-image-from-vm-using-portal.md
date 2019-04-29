---
title: VHD에서 Azure DevTest Labs 사용자 지정 이미지 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 프로비저닝된 VM에서 Azure DevTest Labs에 사용자 지정 이미지를 만드는 방법에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868502"
---
# <a name="create-a-custom-image-from-a-vm"></a>VM에서 사용자 지정 이미지 만들기

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>단계별 지침

프로비저닝된 VM에서 사용자 지정 이미지를 만든 다음, 사용자 지정 이미지를 사용하여 동일한 VM을 만들 수 있습니다. 다음 단계에서는 VM에서 사용자 지정 이미지를 만드는 방법을 보여 줍니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 주 창에서 **내 가상 머신**을 선택합니다.
 
1. **내 가상 머신** 창에서 사용자 지정 이미지를 만들 VM을 선택합니다.

1. VM의 관리 창에서 **사용자 지정 이미지 만들기(VHD)** 를 선택합니다.

    ![사용자 지정 이미지 만들기 메뉴 항목](./media/devtest-lab-create-template/create-custom-image.png)

1. **사용자 지정 이미지** 창에서 사용자 지정 이미지에 대한 이름 및 설명을 입력합니다. 이 정보는 VM을 만들 때 기본 목록에 표시됩니다. 사용자 지정 이미지에는 OS 디스크 및 가상 머신에 연결된 모든 데이터 디스크가 포함됩니다.

    ![사용자 지정 이미지 만들기 창](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. sysprep이 VM에서 실행되었는지 여부를 선택합니다. sysprep이 VM에서 실행되지 않은 경우 사용자 지정 이미지가 만들어질 때 sysprep을 VM에서 실행할 것인지 여부를 지정합니다.

1. 완료했으면 **확인** 을 선택하여 사용자 지정 이미지를 만듭니다.

몇 분 후 사용자 지정 이미지가 만들어지고 랩의 저장소 계정 내에 저장됩니다. 랩 사용자가 새 VM을 만들려는 경우 기본 이미지의 목록에서 이미지를 사용할 수 있습니다.

![기본 이미지 목록에서 사용할 수 있는 사용자 지정 이미지](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>다음 단계

- [랩에 VM 추가](devtest-lab-add-vm.md)
