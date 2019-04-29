---
title: Machine Learning Studio 작업 영역 만들기
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio를 사용하려면 Machine Learning Studio 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109351"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio 작업 영역 만들기 및 공유

Azure Machine Learning Studio를 사용하려면 Machine Learning Studio 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.

## <a name="create-a-studio-workspace"></a>Studio 작업 영역 만들기

1.  [Azure 포털](https://portal.azure.com/)

    > [!NOTE]
    > 로그인하여 Studio 작업 영역을 만들려면 Azure 구독 관리자여야 합니다. 
    >
    > 

2. **+새로 만들기**를 클릭합니다.

3. 검색 상자에 **Machine Learning Studio 작업 영역**을 입력하고 일치하는 항목을 선택합니다. 그런 다음 페이지 아래쪽에서 **만들기** 클릭을 선택합니다.

4. 작업 영역 정보를 입력합니다.

   - *작업 영역 이름*은 공백으로 끝나지 않고 최대 260자로 구성할 수 있습니다. 이름에는 다음과 같은 문자를 포함하지 않아야 합니다. `< > * % & : \ ? + /`
   - 사용자가 선택하거나 만든 *웹 서비스 계획*은 사용자가 선택한 연결된 *가격 책정 계층*과 함께, 이 작업 영역에서 웹 서비스를 배포하는 경우 사용됩니다.

     ![새 Studio 작업 영역 만들기](./media/create-workspace/create-new-workspace.png)

5. **만들기**를 클릭합니다.

> [!NOTE]
> Machine Learning Studio는 워크플로를 실행할 때 사용자가 제공한 Azure Storage 계정을 사용하여 중간 데이터를 저장합니다. 작업 영역을 만든 후 스토리지 계정을 삭제하거나 액세스 키를 변경하는 경우 해당 작업 영역의 작동이 중지되고 작업 영역의 모든 실험이 실패합니다.
스토리지 계정을 실수로 삭제한 경우 삭제한 스토리지 계정과 동일한 지역에 동일한 이름으로 스토리지 계정을 다시 만들고 액세스 키를 다시 동기화합니다. 저장소 계정 액세스 키를 변경한 경우에는, Azure Portal을 사용하여 작업 영역에서 액세스 키를 다시 동기화합니다.

작업 영역이 배포되면 Machine Learning Studio에서 열 수 있습니다.

1. [https://studio.azureml.net/](https://studio.azureml.net/)의 Machine Learning Studio를 찾아봅니다.

2. 오른쪽 위 모서리에서 작업 영역을 선택합니다.

    ![작업 영역 선택](./media/create-workspace/open-workspace.png)

3. **내 실험**을 클릭합니다.

    ![실험 열기](./media/create-workspace/my-experiments.png)

Studio 작업 영역을 관리하는 방법에 대한 자세한 내용은 [Azure Machine Learning Studio 작업 영역 관리](manage-workspace.md)를 참조하세요.
작업 영역을 만드는 데 문제가 있으면 [문제 해결 가이드: Machine Learning Studio 작업 영역 만들기 및 연결](troubleshooting-creating-ml-workspace.md)을 참조하세요.


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio 작업 영역 공유
Machine Learning Studio 작업 영역이 만들어진 후에는 사용자를 작업 영역에 초대하고 작업 영역과 모든 실험, 데이터 세트, Notebook 등에 대한 액세스를 공유할 수 있습니다. 이 두 역할 중 하나에 사용자를 추가할 수 있습니다.

* **사용자** - 작업 영역 사용자는 작업 영역에서 실험, 데이터 세트 등을 만들기, 열기, 수정 및 삭제할 수 있습니다.
* **소유자** - 소유자는 사용자가 수행할 수 있는 작업 외에도 작업 영역에 사용자를 초대 및 제거할 수 있습니다.

> [!NOTE]
> 작업 영역을 만든 관리자 계정은 작업 영역 소유자 권한으로 작업 영역에 자동으로 추가됩니다. 그러나 구독에 있는 다른 관리자 또는 사용자에게는 작업 영역에 대한 액세스 권한이 자동으로 부여되지 않으며 명시적으로 초대해야 합니다.
> 
> 

### <a name="to-share-a-studio-workspace"></a>Studio 작업 영역을 공유하려면 다음을 수행합니다.

1. [https://studio.azureml.net/Home](https://studio.azureml.net/Home)의 Machine Learning Studio에 로그인합니다.

2. 왼쪽 패널에서 **설정**을 클릭합니다.

3. **사용자** 탭을 클릭합니다.

4. 페이지 맨 아래에 있는 **더 많은 사용자 초대**를 클릭합니다.

    ![Studio 설정](./media/create-workspace/settings.png)

5. 하나 이상의 전자 메일 주소를 입력합니다. 사용자에게는 유효한 Microsoft 계정(Azure Active Directory에서) 또는 Azure Active Directory의 조직 계정만 필요합니다.

6. 사용자를 소유자 또는 사용자로 추가할지 여부를 선택합니다.

7. **확인** 확인 표시 단추를 클릭합니다.

추가된 각 사용자는 공유 작업 영역에 로그인하는 방법에 대한 설명이 포함된 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 작업 영역에서 웹 서비스를 배포 또는 관리할 수 있는 사용자인 경우 Azure 구독에서 참여자 또는 관리자여야 합니다. 



