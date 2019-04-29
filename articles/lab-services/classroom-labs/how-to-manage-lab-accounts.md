---
title: Azure Lab Services의 랩 계정 관리 | Microsoft Docs
description: Azure 구독에서 랩 계정을 만들거나 랩 계정을 모두 보거나 랩 계정을 삭제하는 방법을 알아봅니다.
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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696343"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Azure Lab Services의 랩 계정 관리 
Azure Lab Services의 랩 계정은 클래스 룸 랩과 같은 관리 되는 랩 형식에 대 한 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **DEVOPS** 섹션에서 **랩 계정**을 선택합니다. **랩 계정** 옆에 있는 별표(`*`)를 선택하여 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가합니다. 다음부터는 **즐겨찾기** 아래에서 **랩 계정**을 선택합니다.

    ![모든 서비스 -> 랩 계정](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **랩 계정** 페이지에서 도구 모음에 있는 **추가**를 선택합니다. 

    ![랩 계정 페이지에서 추가 선택](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **랩 계정** 페이지에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**에서 랩 계정을 만들 위치/지역을 선택합니다. 
    5. **가상 네트워크 피어 지정**에서 랩 네트워크에 대한 VNet(가상 네트워크) 피어 지정을 선택합니다. 이 계정에서 생성된 랩은 선택한 VNet에 연결되어 있으며 선택한 VNet의 리소스에 액세스할 수 있습니다. 
    7. **랩 작성자가 랩 위치를 선택하도록 허용** 필드에서 랩 작성자가 랩의 위치를 선택할 수 있도록 할지 여부를 지정합니다. 이 옵션은 기본적으로 '사용 안 함'으로 설정되어 있습니다. 이 기능이 '사용 안 함'으로 설정되면 랩 작성자가 만들고 있는 랩의 위치를 지정할 수 없습니다. 랩은 랩 계정과 가장 가까운 지리적 위치에 만들어집니다. 이 기능이 '사용'으로 설정되면 랩 작성자가 랩을 만들 때 위치를 선택할 수 있습니다.      
    8. **만들기**를 선택합니다. 

        ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 도구 모음의 **종 모양 아이콘**(**알림**)을 선택하고 배포가 성공했는지 확인한 후 **리소스로 이동**을 선택합니다. 

    또는 **랩 계정** 페이지에서 **새로 고침**을 선택하고 생성된 랩 계정을 선택합니다. 

    ![랩 계정 만들기 창](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 다음과 같은 **랩 계정** 페이지가 표시됩니다.

    ![랩 계정 페이지](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 클릭합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>랩 작성자에 사용할 수 있는 Marketplace 이미지 지정
랩 계정 소유자는 랩 작성자가 랩 계정에서 랩을 만들 때 사용할 수 있는 Marketplace 이미지를 지정할 수 있습니다. 

1. 왼쪽 메뉴에서 **Marketplace 이미지**를 선택합니다. 기본적으로 이미지의 전체 목록이 표시됩니다(사용 및 사용 안 함으로 설정된 이미지 모두 포함). 맨 위의 드롭다운 목록에서 **Enabled only**(사용으로 설정된 이미지만)/**Disabled only**(사용 안 함으로 설정된 이미지만) 옵션을 선택하여 사용/사용 안 함으로 설정된 이미지만 표시하도록 목록을 필터링할 수 있습니다. 
    
    ![Marketplace 이미지 페이지](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    다음 조건을 만족하는 Marketplace 이미지만 목록에 표시됩니다.
        
    - 단일 VM을 만듭니다.
    - Azure Resource Manager를 사용하여 VM을 프로비전합니다.
    - 추가 라이선스 플랜을 구입하지 않아도 됩니다.
2. 사용하도록 설정된 Marketplace 이미지를 **사용하지 않도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용 안 함**을 선택합니다. 

        ![하나의 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용 안 함**을 선택합니다. 

        ![여러 이미지를 사용하지 않도록 설정](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 마찬가지로 Marketplace 이미지를 **사용하도록 설정**하려면 다음 작업 중 하나를 수행합니다. 
    1. 마지막 열에서 **...(줄임표)** 를 선택하고, **이미지 사용**을 선택합니다. 
    2. 목록에서 이미지 이름 앞의 확인란을 선택하여 목록에서 하나 이상의 이미지를 선택하고, **선택한 이미지 사용**을 선택합니다. 

## <a name="configure-the-lab-account"></a>랩 계정 구성
1. 에 **랩 계정을** 페이지에서 **Labs 구성** 왼쪽된 메뉴에서.

    ![랩 구성 페이지](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 에 대 한 **피어 가상 네트워크**를 선택 **Enabled** 또는 **비활성**합니다. 기본값은 **사용 안 함**합니다. 피어 가상 네트워크를 사용 하려면 다음 단계를 수행 합니다. 
    1. **사용**을 선택합니다.
    2. 선택 된 **VNet** 드롭 다운 목록에서. 
    3. 도구 모음에서 **저장**을 선택합니다. 
    
        이 계정에 만든 랩 선택한 가상 네트워크에 연결 됩니다. 선택한 가상 네트워크의 리소스에 액세스할 수 있습니다. 
3. 에 대 한는 **랩 위치를 선택 하도록 허용 랩 작성자**를 선택 **Enabled** 랩 작성자 랩에 대 한 위치를 선택할 수 있게 되기를 원하는 경우. 비활성화 된 실습 랩 계정을 존재 하는 동일한 위치에 자동으로 만들어집니다. 

## <a name="view-lab-accounts"></a>랩 계정 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 메뉴에서 **모든 리소스**를 선택합니다. 
3. **형식**에 대한 **랩 계정**을 선택합니다. 
    구독, 리소스 그룹, 위치 및 태그별로 필터링할 수도 있습니다. 

    ![모든 리소스 -> 랩 계정](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>랩 계정의 랩 보기 및 관리

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩**을 선택합니다.

    ![계정의 랩](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. 계정에는 다음 정보가 있는 **랩** 목록이 표시됩니다. 
    1. 랩의 이름
    2. 랩을 만든 날짜 
    3. 랩을 만든 사용자의 메일 주소 
    4. 랩에 허용되는 최대 사용자 수 
    5. 랩 상태 



## <a name="delete-a-lab-in-the-lab-account"></a>랩 계정에서 랩 삭제
이전 섹션의 지침에 따라 랩 계정의 랩 목록을 표시합니다.

1. **...(줄임표)** 를 선택하고 **삭제**를 선택합니다. 

    ![랩 삭제 - 단추](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 경고 메시지에서 **예**를 선택합니다. 

    ![랩 삭제 확인](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>랩 계정 삭제
랩 계정을 목록으로 표시하는 이전 섹션의 지침을 따릅니다. 다음 지침에 따라 랩 계정을 삭제합니다. 

1. 삭제하려는 **랩 계정**을 선택합니다. 
2. 도구 모음에서 **삭제**를 선택합니다. 

    ![랩 계정 -> 삭제 단추](../media/how-to-manage-lab-accounts/delete-button.png)
1. 확인을 위해 **예**를 입력합니다.
1. **삭제**를 선택합니다. 

    ![랩 계정 삭제 - 확인](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)



## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)