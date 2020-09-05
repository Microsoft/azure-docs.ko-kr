---
title: 'ML Studio (클래식): 작업 영역 만들기-Azure'
description: Azure Machine Learning Studio (클래식)을 사용 하려면 Machine Learning Studio (클래식) 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: c283ef2f6eb28dca7ca5bc5e391ef30465c65dc0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691278"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (클래식) 작업 영역 만들기 및 공유

**적용 대상:**  ![예](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)   ![아니요](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

Azure Machine Learning Studio (클래식)을 사용 하려면 Machine Learning Studio (클래식) 작업 영역이 있어야 합니다. 이 작업 영역에는 실험을 만들고 관리, 게시하는 데 필요한 도구가 들어 있습니다.

## <a name="create-a-studio-classic-workspace"></a>Studio (클래식) 작업 영역 만들기

Machine Learning Studio (클래식)에서 작업 영역을 열려면 작업 영역을 만드는 데 사용한 Microsoft 계정에 로그인 하거나 작업 영역에 가입 하는 데 필요한 소유자의 초대를 받아야 합니다. Azure Portal에서 액세스를 구성하는 기능을 포함하여 작업 영역을 관리할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)

    > [!NOTE]
    > 로그인 하 고 Studio (클래식) 작업 영역을 만들려면 Azure 구독 관리자 여야 합니다. 
    >
    > 

2. **+ 새로 만들기** 를 클릭 합니다.

3. 검색 상자에 **Machine Learning Studio (클래식) 작업 영역** 을 입력 하 고 일치 하는 항목을 선택 합니다. 그런 다음 페이지 아래쪽에서 **만들기** 클릭을 선택합니다.

4. 작업 영역 정보를 입력합니다.

   - *작업 영역 이름*은 공백으로 끝나지 않고 최대 260자로 구성할 수 있습니다. 이름에는 다음과 같은 문자를 포함하지 않아야 합니다. `< > * % & : \ ? + /`
   - 사용자가 선택하거나 만든 *웹 서비스 계획*은 사용자가 선택한 연결된 *가격 책정 계층*과 함께, 이 작업 영역에서 웹 서비스를 배포하는 경우 사용됩니다.

     ![새 Studio (클래식) 작업 영역 만들기](./media/create-workspace/create-new-workspace.png)

5. **만들기**를 클릭합니다.

   Machine Learning은 현재 제한된 지역에서 사용할 수 있습니다. 구독에 이러한 지역 중 하나가 포함 되지 않은 경우 "허용 된 지역에 구독이 없습니다." 라는 오류 메시지가 표시 될 수 있습니다.  구독에 영역을 추가하도록 요청하려면 Azure Portal에서 Microsoft 지원 요청을 만들고 문제 유형으로 **청구**를 선택한 다음 프롬프트에 따라 요청을 제출합니다.


> [!NOTE]
> Machine Learning Studio (클래식)는 워크플로를 실행할 때 중개 데이터를 저장 하기 위해 제공 하는 Azure storage 계정에 의존 합니다. 작업 영역을 만든 후 스토리지 계정을 삭제하거나 액세스 키를 변경하는 경우 해당 작업 영역의 작동이 중지되고 작업 영역의 모든 실험이 실패합니다.
스토리지 계정을 실수로 삭제한 경우 삭제한 스토리지 계정과 동일한 지역에 동일한 이름으로 스토리지 계정을 다시 만들고 액세스 키를 다시 동기화합니다. 스토리지 계정 액세스 키를 변경한 경우에는, Azure Portal을 사용하여 작업 영역에서 액세스 키를 다시 동기화합니다.

작업 영역이 배포 되 면 Machine Learning Studio (클래식)에서 작업 영역을 열 수 있습니다.

1. 에서 Machine Learning Studio (클래식)로 이동 [https://studio.azureml.net/](https://studio.azureml.net/) 합니다.

2. 오른쪽 위 모서리에서 작업 영역을 선택합니다.

    ![작업 영역 선택](./media/create-workspace/open-workspace.png)

3. **내 실험**을 클릭합니다.

    ![실험 열기](./media/create-workspace/my-experiments.png)

Studio (클래식) 작업 영역을 관리 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning Studio (클래식) 작업 영역 관리](manage-workspace.md)를 참조 하세요.
작업 영역을 만드는 데 문제가 발생 하는 경우 [문제 해결 가이드: Machine Learning Studio (클래식) 작업 영역 만들기 및 연결](troubleshooting-creating-ml-workspace.md)을 참조 하세요.


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (클래식) 작업 영역 공유
Machine Learning Studio (클래식) 작업 영역이 만들어지면 사용자를 작업 영역에 초대 하 여 작업 영역 및 모든 실험, 데이터 집합 등에 대 한 액세스를 공유할 수 있습니다. 다음 두 역할 중 하나에 사용자를 추가할 수 있습니다.

* **사용자** - 작업 영역 사용자는 작업 영역에서 실험, 데이터 세트 등을 만들기, 열기, 수정 및 삭제할 수 있습니다.
* **소유자** - 소유자는 사용자가 수행할 수 있는 작업 외에도 작업 영역에 사용자를 초대 및 제거할 수 있습니다.

> [!NOTE]
> 작업 영역을 만든 관리자 계정은 작업 영역 소유자 권한으로 작업 영역에 자동으로 추가됩니다. 그러나 구독에 있는 다른 관리자 또는 사용자에게는 작업 영역에 대한 액세스 권한이 자동으로 부여되지 않으며 명시적으로 초대해야 합니다.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Studio (클래식) 작업 영역을 공유 하려면

1. Machine Learning Studio (클래식)에 로그인 합니다. [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. 왼쪽 패널에서 **설정**을 클릭합니다.

3. **사용자** 탭을 클릭 합니다.

4. 페이지 맨 아래에 있는 **더 많은 사용자 초대**를 클릭합니다.

    ![Studio 설정](./media/create-workspace/settings.png)

5. 하나 이상의 전자 메일 주소를 입력합니다. 사용자에게는 유효한 Microsoft 계정(Azure Active Directory에서) 또는 Azure Active Directory의 조직 계정만 필요합니다.

6. 사용자를 소유자 또는 사용자로 추가할지 여부를 선택합니다.

7. **확인** 확인 표시 단추를 클릭합니다.

추가된 각 사용자는 공유 작업 영역에 로그인하는 방법에 대한 설명이 포함된 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 작업 영역에서 웹 서비스를 배포 또는 관리할 수 있는 사용자인 경우 Azure 구독에서 참여자 또는 관리자여야 합니다. 

## <a name="troubleshoot-storage-accounts"></a>저장소 계정 문제 해결


Machine Learning 서비스에서 데이터를 저장하려면 스토리지 계정이 필요합니다. 기존 저장소 계정을 사용 하거나 새 Machine Learning Studio (클래식) 작업 영역을 만들 때 새 저장소 계정을 만들 수 있습니다 (새 저장소 계정을 만들 수 있는 할당량이 있는 경우).

새 Machine Learning Studio (클래식) 작업 영역을 만든 후에는 작업 영역을 만드는 데 사용한 Microsoft 계정 사용 하 여 Machine Learning Studio (클래식)에 로그인 할 수 있습니다. 다음 스크린샷 처럼 "작업 영역을 찾을 수 없습니다." 라는 오류 메시지가 표시 되 면 다음 단계를 사용 하 여 브라우저 쿠키를 삭제 하세요.

![작업 영역을 찾을 수 없음](media/troubleshooting-creating-ml-workspace/screen3.png)

**브라우저 쿠키를 삭제하려면**

1. Internet Explorer를 사용하는 경우 오른쪽 위에 있는 **도구** 단추를 클릭하고 **인터넷 옵션**을 선택합니다.  

   ![인터넷 옵션](media/troubleshooting-creating-ml-workspace/screen4.png)

2. **일반** 탭에서 **삭제…** 를 클릭합니다.

   ![일반 탭](media/troubleshooting-creating-ml-workspace/screen5.png)

3. **검색 기록 삭제** 대화 상자에서 **쿠키 및 웹 사이트 데이터**가 선택되었는지 확인하고 **삭제**를 클릭합니다.

   ![쿠키 삭제](media/troubleshooting-creating-ml-workspace/screen6.png)

쿠키를 삭제 한 후 브라우저를 다시 시작 하 고 [Microsoft Azure Machine Learning Studio (클래식)](https://studio.azureml.net) 페이지로 이동 합니다. 사용자 이름과 암호를 묻는 메시지가 표시되면 작업 영역을 만드는 데 사용한 동일한 Microsoft 계정을 입력합니다.


## <a name="next-steps"></a>다음 단계

작업 영역을 관리 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning Studio (클래식) 작업 영역 관리](manage-workspace.md)를 참조 하세요.
