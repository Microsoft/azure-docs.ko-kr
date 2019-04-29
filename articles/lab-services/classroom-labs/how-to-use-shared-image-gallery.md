---
title: Azure Lab Services에서 공유 이미지 갤러리를 사용 하 여 | Microsoft Docs
description: 사용자를 다른 이미지를 공유할 수 있으며 다른 사용자는 랩에서 VM 템플릿을 만들려면 이미지를 사용할 수 있도록 공유 이미지 갤러리를 사용 하는 랩 계정을 구성 하는 방법에 알아봅니다.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695212"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services에서 공유 이미지 갤러리를 사용 합니다.
이 문서에서는 교사/랩 관리자 사람이 다시 사용할 수에 대 한 템플릿 가상 머신 이미지를 저장할 수 하는 방법을 보여 줍니다. 이러한 이미지를 Azure에 저장 됩니다 [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)합니다. 첫 번째 단계로, 랩 관리자는 기존 공유 이미지 갤러리를 랩 계정에 연결합니다. 공유 이미지 갤러리 연결 되 면 랩 계정에 만든 랩 공유 이미지 갤러리에 이미지를 저장할 수 있습니다. 다른 교사는 해당 클래스에 대 한 템플릿을 만들려면 공유 이미지 갤러리에서이 이미지를 선택할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
공유 이미지 갤러리를 사용 하 여 만들기 [Azure PowerShell](../../virtual-machines/windows/shared-images.md) 하거나 [Azure CLI](../../virtual-machines/linux/shared-images.md)합니다.

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>공유 이미지 갤러리 랩 계정에 연결
다음 절차는 랩 계정에 공유 이미지 갤러리를 연결 하는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 서비스**를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 랩 계정을 확인 하려면 선택 합니다 **랩 계정을** 페이지입니다. 
4. 선택 **공유 이미지 갤러리** 선택 고 왼쪽된 메뉴에서 **연결** 도구 모음에서 합니다. 

    ![공유 갤러리 이미지-단추를 추가 합니다.](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 에 **기존 공유 이미지 갤러리를 첨부할** 페이지, 공유 이미지 갤러리를 선택 하 고, 선택 **확인**합니다.

    ![기존 갤러리를 선택 합니다.](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 다음과 같은 화면이 표시 됩니다. 

    ![목록에 내 갤러리](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    이 예제의 경우 이미지가 없습니다 공유 이미지 갤러리에서 아직

Azure Lab Services id는 랩에 연결 된 공유 이미지 갤러리에 contributor로 추가 됩니다. 교사 수 있도록 공유 이미지 갤러리에 이미지를 IT 관리자에 가상 머신을 저장 하는 /입니다. 모든 랩이 랩 계정에서 만든 연결 된 공유 이미지 갤러리에 액세스할 수 있으며 

연결 된 공유 이미지 갤러리의 모든 이미지는 기본적으로 활성화 됩니다. 목록에서 선택 하 고 사용 하 여 선택한 이미지를 사용 하지 않도록 설정 하거나 설정할 수 있습니다 합니다 **선택한 이미지를 사용 하도록 설정** 하거나 **선택한 이미지를 사용 하지 않도록 설정** 단추입니다. 

## <a name="detach-a-shared-image-gallery"></a>공유 이미지 갤러리를 분리 합니다.
랩에 하나의 공유 이미지 갤러리를 연결할 수 있습니다. 다른 공유 이미지 갤러리를 연결 하려는 경우 새 연결 하기 전에 현재의 분리 합니다. 랩에서 공유 이미지 갤러리를 분리 하려면 선택 **분리** 도구 모음에서 분리 작업을 확인 합니다. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>공유 이미지 갤러리에 이미지 저장
공유 이미지 갤러리를 연결한 후 교사 저장할 수 템플릿 이미지를 공유 하는 이미지 갤러리에 다른 교사에서 다시 사용할 수 있도록 합니다.

![갤러리에서 가상 머신 이미지를 저장 합니다.](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리에서 이미지를 사용 합니다.
교사/교수 템플릿에 대 한 공유 이미지 갤러리에서 새 랩 만들기 중에 제공 되는 사용자 지정 이미지를 선택할 수 있습니다.

![갤러리에서 사용 하 여 가상 머신 이미지](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리에 대 한 자세한 내용은 참조 하세요. [공유 이미지 갤러리](../../virtual-machines/windows/shared-image-galleries.md)합니다.
