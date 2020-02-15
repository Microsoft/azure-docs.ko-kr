---
title: Azure Lab Services에서 랩 계정 구성 Microsoft Docs
description: 이 문서에서는 Azure Lab Services에서 랩 계정을 만들거나 모든 랩 계정을 보거나 랩 계정을 삭제 하는 방법을 설명 합니다.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210581"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure Lab Services에서 랩 계정 구성 
Azure Lab Services에서 랩 계정은 교실 labs와 같은 관리 되는 랩 형식에 대 한 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 이 문서는 랩 계정을 만들거나, 모든 랩 계정을 보거나, 랩 계정을 삭제하는 방법을 설명합니다.

## <a name="connect-with-a-peer-virtual-network"></a>피어 가상 네트워크에 연결
가상 네트워크를 피어 네트워크로 랩의 가상 네트워크에 연결 하려면 다음 단계를 수행 합니다.

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 구성** 을 선택 합니다.

    ![랩 구성 페이지](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. **피어 가상 네트워크**의 경우 **사용** 또는 **사용 안 함**을 선택 합니다. 기본값은 **사용 안 함**입니다. 피어 가상 네트워크를 사용 하도록 설정 하려면 다음 단계를 수행 합니다. 
    1. **사용**을 선택합니다.
    2. 드롭다운 목록에서 **VNet** 을 선택 합니다. 
3. 도구 모음에서 **저장**을 선택합니다. 

이 계정에서 만든 랩은 선택한 가상 네트워크에 연결 됩니다. 선택한 가상 네트워크의 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Lab Services에서 랩의 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)을 참조 하세요.

**피어 가상 네트워크** 필드의 가상 네트워크를 선택 하는 경우 **랩 작성자가 랩 위치를 선택할 수 있음** 옵션을 사용할 수 없습니다. 랩 계정의 랩은 피어 가상 네트워크의 리소스와 연결 하기 위해 랩 계정과 동일한 지역에 있어야 하기 때문입니다. 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>랩에서 Vm의 주소 범위 지정
다음 절차에는 랩에서 Vm에 대 한 주소 범위를 지정 하는 단계가 있습니다. 이전에 지정한 범위를 업데이트 하는 경우 수정 된 주소 범위는 변경 후에 생성 된 Vm에만 적용 됩니다. 

유의 해야 하는 주소 범위를 지정할 때 몇 가지 제한 사항이 있습니다. 

- 접두사는 23 보다 작거나 같아야 합니다. 
- 가상 네트워크가 랩 계정으로 피어 링 되는 경우 제공 된 주소 범위는 피어 링 가상 네트워크의 주소 범위와 겹칠 수 없습니다.

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 구성** 을 선택 합니다.
2. **주소 범위** 필드에서 랩에서 생성 될 vm의 주소 범위를 지정 합니다. 주소 범위는 클래스 없는 CIDR (도메인 간 라우팅) 표기법 (예: 10.20.0.0/23)에 있어야 합니다. 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![주소 범위 구성](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>랩 작성자 역할에 사용자 추가
랩 계정에서 클래스룸 랩을 설정하려면 사용자는 랩 계정에서 **랩 작성자** 역할의 멤버여야 합니다. 랩 계정을 만드는 데 사용한 계정이 이 역할에 자동으로 추가됩니다. 동일한 사용자 계정을 사용하여 클래스룸 랩을 만들려는 경우 이 단계를 건너뛸 수 있습니다. 다른 사용자 계정을 사용하여 클래스룸 랩을 만들려면 다음 단계를 수행합니다. 

강사가 클래스를 위한 랩을 만들 수 있도록 권한을 제공하려면 강사를 **랩 작성자** 역할에 추가합니다.

1. **랩 계정** 페이지에서 **액세스 제어(IAM)** 를 선택하고, 도구 모음에서 **+ 역할 할당 추가**를 클릭합니다. 

    ![액세스 제어 -> 역할 할당 추가 단추](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **역할 할당 추가** 페이지에서 **역할**에 대한 **랩 작성자**를 선택하고, 랩 작성자 역할에 추가할 사용자를 선택하고, **저장**을 선택합니다. 

    ![랩 작성자 추가](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Microsoft 계정 없는 사용자를 랩 작성자로 추가 하는 경우 [랩 작성자로 Microsoft 계정 되지 않은 사용자 추가](#add-a-non-microsoft-account-user-as-a-lab-creator) 섹션을 참조 하세요. 

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

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>랩 작성자로 Microsoft 계정 되지 않은 사용자 추가
사용자를 랩 작성자로 추가 하려면 해당 전자 메일 계정을 사용 합니다. 다음과 같은 유형의 전자 메일 계정을 사용할 수 있습니다.

- 사용자의 학교 Office 365 Azure Active Directory (AAD)에서 제공 하는 전자 메일 계정입니다. 
- `@outlook.com`, `@hotmail.com`, `@msn.com`, `@live.com`등의 Microsoft 전자 메일 계정입니다.
- Yahoo 또는 Google에서 제공 하는 것과 같은 타사 전자 메일 계정. 그러나 이러한 유형의 계정은 Microsoft 계정 연결 해야 합니다.
- GitHub 계정. 이 계정은 Microsoft 계정 연결 해야 합니다.

### <a name="using-a-non-microsoft-email-account"></a>타사 전자 메일 계정 사용
랩 작성자/강사는 타사 전자 메일 계정을 사용 하 여 교실 랩에 등록 하 고 로그인 할 수 있습니다.  그러나 Lab Services 포털에 로그인 하려면 먼저 강사가 타사 전자 메일 주소에 연결 된 Microsoft 계정를 만들어야 합니다.

대부분의 강사는 타사 전자 메일 주소에 연결 된 Microsoft 계정 이미 있을 수 있습니다. 예를 들어 강사는 Microsoft의 다른 제품 또는 서비스 (예: Office, Skype, OneDrive, Windows)와 전자 메일 주소를 사용한 경우 이미 Microsoft 계정 있습니다.  

강사가 Lab Services 포털에 로그인 하면 전자 메일 주소 및 암호를 입력 하 라는 메시지가 표시 됩니다. 강사가 Microsoft 계정 연결 되지 않은 Microsoft 계정를 사용 하 여 로그인 하려고 하면 강사는 다음과 같은 오류 메시지를 받게 됩니다. 

![오류 메시지](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft 계정 등록 하려면 강사는 [http://signup.live.com](http://signup.live.com)으로 이동 해야 합니다.  


### <a name="using-a-github-account"></a>GitHub 계정 사용
강사는 기존 GitHub 계정을 사용 하 여 교실 랩에 등록 하 고 로그인 할 수도 있습니다. 강사에 게 GitHub 계정에 연결 된 Microsoft 계정 이미 있는 경우 이전 섹션에 표시 된 대로 로그인 하 고 암호를 제공할 수 있습니다. 아직 GitHub 계정을 Microsoft 계정에 연결 하지 않은 경우 **로그인 옵션**을 선택 해야 합니다.

![로그인 옵션 링크](../media/how-to-configure-student-usage/signin-options.png)

**로그인 옵션** 페이지에서 **GitHub로 로그인**을 선택 합니다.

![GitHub 링크를 사용 하 여 로그인](../media/how-to-configure-student-usage/signin-github.png)

마지막으로, GitHub 계정에 연결 된 Microsoft 계정를 만들라는 메시지가 표시 됩니다. 강사가 **다음**을 선택 하면 자동으로 발생 합니다.  강사가 즉시 로그인 하 여 교실 랩에 연결 됩니다.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>연결을 끊을 때 Vm 자동 종료
원격 데스크톱 연결을 끊은 후 Windows 랩 Vm (템플릿 또는 학생)의 자동 종료를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 또한 사용자가 자동으로 종료 되기 전에 사용자가 다시 연결 될 때까지 Vm에서 대기할 기간을 지정할 수 있습니다.

![랩 계정에서 자동 종료 설정](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

이 설정은 랩 계정에 생성 된 모든 랩에 적용 됩니다. 랩 소유자는 랩 수준에서이 설정을 재정의할 수 있습니다. 랩 계정에서이 설정을 변경 하면 변경 후에 생성 된 랩에서만 영향을 받습니다.

랩 소유자가 랩 수준에서이 설정을 구성할 수 있는 방법에 대해 알아보려면 [이 문서](how-to-enable-shutdown-disconnect.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
