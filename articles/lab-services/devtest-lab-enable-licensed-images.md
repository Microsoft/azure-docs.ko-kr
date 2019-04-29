---
title: Azure DevTest Labs에서 랩의 라이선스 이미지 사용 | Microsoft Docs
description: Azure Portal을 사용하여 Azure DevTest Labs에서 라이선스 이미지를 사용하도록 설정하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61294338"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩의 라이선스 이미지 사용

Azure DevTest Labs에서 라이선스 이미지는 일반적으로 타사의 사용 약관을 포함하고 있으며, 랩의 사용자가 이미지에 액세스하기 전에 먼저 동의해야 합니다. 다음 섹션에서는 가상 머신을 만드는 데 라이선스 이미지를 사용할 수 있도록 하는 방법을 설명합니다.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>라이선스 이미지를 사용자가 사용할 수 있는지 여부 결정
사용자가 라이선스 이미지에서 VM을 만들 수 있도록 하려면 먼저 라이선스 이미지의 사용 약관에 동의했는지 확인해야 합니다. 다음 단계에서는 라이선스 이미지의 제공 상태를 확인하고 필요한 경우 해당 사용 약관에 동의하는 방법을 보여 줍니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 왼쪽 패널의 **설정**에서 **구성 및 정책**을 선택합니다.

1. 왼쪽 패널의 **가상 머신 기본**에서 **Marketplace 이미지**를 선택합니다. 

    ![Marketplace 이미지 메뉴 항목](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    각 이미지에 대한 **제공 상태**를 포함하여 사용 가능한 모든 Marketplace 이미지가 표시됩니다.

    ![각 이미지에 대한 제공 상태를 표시하는 Marketplace 이미지 목록](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    라이선스 이미지는 다음 제공 상태를 표시합니다. 
    
    - **약관에 동의함:** 라이선스 이미지를 사용자가 VM 생성에 사용할 수 있습니다. 
    - **약관을 검토해야 함:** 현재 라이선스 이미지를 사용자가 사용할 수 없습니다. 랩 사용자가 VM을 만드는 데 사용하려면 라이선스의 사용 약관에 동의해야 합니다. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>라이선스 이미지를 랩 사용자가 사용하도록 허용
라이선스 이미지를 랩 사용자가 사용할 수 있게 하려면 관리자 권한이 있는 랩 소유자가 먼저 해당 라이선스 이미지에 대한 사용 약관에 동의해야 합니다. 라이선스 이미지와 연결된 구독에 대해 프로그래밍 방식 배포를 사용하도록 설정하면 해당 이미지의 법률 조항 및 개인 정보 처리 방침이 자동으로 동의됩니다. [Azure Resource Manager에서 Marketplace 이미지 사용](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/)에서는 Marketplace 이미지의 프로그래밍 방식 배포에 대한 추가 정보를 제공합니다.

다음 단계를 수행하여 라이선스 이미지에 대한 프로그래밍 방식 배포를 사용하도록 설정할 수 있습니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 **Marketplace 이미지** 목록으로 이동합니다.

1. 사용자가 액세스하려고 하지만 해당 약관에 동의하지 않은 라이선스 이미지를 식별합니다. 예를 들어 **약관에 동의함** 또는 **약관을 검토해야 함** 상태를 표시하는 데이터 과학 Virtual Machine이 있을 수 있습니다.

    ![프로그래밍 방식 배포 구성 창](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > 데이터 과학 VM은 데이터 분석, 기계 학습 및 AI 교육에 일반적으로 사용되는 몇 가지 인기 있는 도구로, 미리 설치되고 구성되고 테스트된 Azure Virtual Machine 이미지입니다. [Linux/Windows용 Azure 데이터 과학 Virtual Machine에 대한 소개](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)에서는 DSVM에 대한 풍부한 정보를 제공합니다.
   >
   >

1. 이미지의 **제공 상태** 열에서 **약관을 검토해야 함**을 선택합니다.

1. 프로그래밍 방식 배포 구성 창에서 **사용**을 선택합니다.

    ![프로그래밍 방식 배포 구성 창](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > 프로그래밍 방식 배포 구성 창에 나열된 여러 구독을 볼 수 있습니다. 의도한 구독에 대해서만 프로그래밍 방식 배포를 사용하도록 설정하는지 확인합니다.
   >
   >


1. **저장**을 선택합니다. 

    Marketplace 이미지 목록에서 해당 이미지는 이제 **약관에 동의함**으로 표시되며 사용자가 가상 머신을 만드는 데 사용할 수 있습니다.

> [!NOTE]
> 사용자는 라이선스 이미지에서 사용자 지정 이미지를 만들 수 있습니다. 자세한 내용은 [VHD 파일에서 사용자 지정 이미지 만들기](devtest-lab-create-template.md)를 참조하세요.
>
>


## <a name="related-blog-posts"></a>관련 블로그 게시물

- [사용자 지정 이미지 또는 수식?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Azure DevTest Labs 간의 사용자 지정 이미지 복사](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>다음 단계

- [VM에서 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [VHD 파일에서 사용자 지정 이미지 만들기](devtest-lab-create-template.md)
- [랩에 VM 추가](devtest-lab-add-vm.md)