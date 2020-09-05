---
title: Azure Lab Services로 랩 계정 설정 | Microsoft Docs
description: Azure Lab Services를 사용하여 랩 계정을 설정하고, 랩 작성자를 추가하고, 랩 계정의 랩에서 사용할 Marketplace 이미지를 지정하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85445069"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>자습서: Azure Lab Services로 랩 계정 설정
Azure Lab Services에서 랩 계정은 조직의 랩을 관리하는 중앙 계정 역할을 합니다. 랩 계정에서 랩을 만들고 랩 계정의 모든 랩에 적용되는 정책을 설정할 수 있는 권한을 다른 사람에게 부여하세요. 이 자습서에서는 랩 계정을 만드는 방법을 알아봅니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩 계정 만들기
> * 랩 작성자 역할에 사용자 추가

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-a-lab-account"></a>랩 계정 만들기
다음 단계는 Azure Portal을 사용하여 Azure DevTest Labs로 랩 계정을 만드는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. **범주**에서 **DevOps**를 선택합니다. 그런 다음, **Lab Services**를 선택합니다. **랩 서비스** 옆에 있는 별표(`*`)를 선택하면 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가됩니다. 다음부터는 **즐겨찾기** 아래에서 **랩 서비스**를 선택합니다.

    ![모든 서비스 -> 랩 서비스](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **Lab Services** 페이지의 도구 모음에서 **추가**를 선택하거나 페이지에서 **랩 계정 만들기** 단추를 선택합니다. 

    ![랩 계정 페이지에서 추가 선택](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **랩 계정 만들기** 페이지의 **기본 사항** 탭에서 다음 작업을 수행합니다. 
    1. **랩 계정 이름**에 이름을 입력합니다. 
    2. 랩 계정을 만들려는 **Azure 구독**을 선택합니다.
    3. **리소스 그룹**의 경우 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.
    4. **위치**의 경우 랩 계정을 만들 위치/지역을 선택합니다. 

        ![랩 계정 - 기본 페이지](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. **검토 + 만들기**를 선택합니다.
    6. 요약을 검토하고 **만들기**를 선택합니다. 

        ![검토 + 만들기 -> 만들기](./media/tutorial-setup-lab-account/create-button.png)    
5. 배포가 완료되면 **다음 단계**를 확장하고 **리소스로 이동**을 선택합니다. 

    ![랩 계정 페이지로 이동](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. **랩 계정** 페이지가 표시되는지 확인합니다. 

    ![랩 계정 페이지](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

> [!NOTE]
> 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 이 자습서에서 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뜁니다. 

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 추가**, **+ 역할 할당 추가**를 차례로 선택합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 랩 계정을 만들었습니다. 교육자로서 클래스룸 랩을 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)

