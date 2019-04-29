---
title: Azure Lab Services의 클래스룸 랩에서 사용 설정 구성 | Microsoft Docs
description: 랩의 사용자 수를 구성하고, 사용자를 랩에 등록하도록 하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 알아봅니다.
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
ms.openlocfilehash: 494c285f1c096a84925d9d9a4fb98409960e5230
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703781"
---
# <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
이 문서에서는 랩에 사용자를 추가하고, 사용자를 랩에 등록하고, VM을 사용할 수 있는 시간을 제어하는 방법 등을 설명합니다. 


## <a name="add-users-to-the-lab"></a>랩에 사용자 추가
**액세스 제한**이 사용되도록 설정되면 목록에 사용자(이메일 주소)를 추가합니다.

1. 왼쪽 메뉴에서 **사용자**를 선택합니다.
2. 도구 모음에서 **사용자 추가**를 선택합니다. 

    ![사용자 추가 단추](../media/how-to-configure-student-usage/add-users-button.png)
1. **사용자 추가** 페이지에서 여러 줄에 걸쳐 또는 세미콜론으로 구분해서 단일 줄에 사용자의 이메일 주소를 입력합니다. 

    ![사용자 이메일 주소 추가](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **저장**을 선택합니다. 목록에서 사용자의 이메일 주소 및 해당 상태(등록됨 또는 등록 해제됨)를 확인합니다. 

    ![사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>학생 등록 링크 공유
학생 들에 게 등록 링크를 보내도록 다음 방법 중 하나를 사용 합니다. 첫 번째 방법은 등록 링크 및 선택적 메시지를 사용 하 여 학생 들에 게 전자 메일을 보내는 방법을 보여 줍니다. 두 번째 메서드를 원하는 방식으로 다른 사용자와 공유할 수 있는 등록 링크를 가져오는 방법을 보여 줍니다. 

랩에서 **액세스 제한**을 사용하도록 설정하면 사용자 목록의 사용자만 등록 링크를 사용하여 랩에 등록할 수 있습니다. 이 옵션은 기본적으로 사용 됩니다. 

### <a name="send-email-to-users"></a>사용자에 게 전자 메일 보내기
Azure Lab Services 교사 모든 랩 초대 메일에 하거나 하나를 사용 하지 않고도 선택한 학생 메일 클라이언트. 교사 수 목록의 각 학생 또는 선택한 하나 이상의 학생 들에 대 한 전자 메일 아이콘을 확인 하 고 사용할 각각의 학생 위에 마우스를 가져가면 **초대를 보낼** 도구 모음에서 합니다. 이 기능은 등록 링크를 사용 하 여 전자 메일을 보내는 및 메시지 (있는 경우)은 교사 하 여 추가 합니다. 초대 상태 변경 초대를 보낸 후 **초대 보냄** 교사를 추적할 수 있는 학생이 이미 받은 등록 링크를 전송 된 날짜 수 있도록 합니다.

1. 페이지에 아직 표시되지 않으면 **사용자** 보기로 전환합니다. 
2. 목록에서 특정 또는 모든 사용자를 선택합니다. 특정 사용자를 선택하려면 목록 첫 번째 열의 확인란을 선택하세요. 모든 사용자를 선택하려면 첫 번째 열의 제목 앞에 있는 확인란(**이름**)을 선택하거나 목록에 있는 모든 사용자에 대한 확인란을 모두 선택하세요. 목록에서 **초대 상태**의 상태를 볼 수 있습니다.  다음 이미지는 모든 학생의 초대 상태가 **초대를 보내지 않음**으로 설정되어 있습니다. 

    ![학생 선택](../media/tutorial-setup-classroom-lab/select-students.png)
1. 행 중 하나에서 **이메일 아이콘(봉투)** 를 선택하거나 도구 모음에서 **초대 보내기**를 선택합니다. 목록의 학생 이름을 마우스로 가리켜서 이메일 아이콘을 표시할 수도 있습니다. 

    ![이메일로 등록 링크 보내기](../media/tutorial-setup-classroom-lab/send-email.png)
4. **이메일로 등록 링크 보내기** 페이지에서 다음 단계를 수행하세요. 
    1. 학생들에게 보내려는 **선택적 메시지**를 입력합니다. 이메일에는 등록 링크가 자동으로 포함됩니다. 
    2. **이메일로 등록 링크 보내기** 페이지에서 **보내기**를 선택합니다. 초대의 상태가 **초대를 보내는 중**에서 **초대 보냄**으로 차례로 변하는 것을 볼 수 있습니다. 
        
        ![초대 전송됨](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>등록 링크 가져오기
1. 왼쪽 메뉴에서 **사용자**를 선택하여 **사용자** 보기로 전환합니다. 
2. **등록 링크 가져오기** 타일을 선택합니다.

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. **사용자 등록** 대화 상자에서 **복사** 단추를 선택합니다. 링크가 클립보드에 복사됩니다. 이메일 편집기에 붙여넣고 학생에게 이메일을 보냅니다. 

    ![학생 등록 ​링크](../media/tutorial-setup-classroom-lab/registration-link.png)
2. **사용자 등록** 대화 상자에서 **닫기**를 선택합니다. 
4. 공유 합니다 **등록 링크** 학생을 사용 하 여 학생 클래스에 대해 등록할 수 있도록 합니다. 

## <a name="view-users-registered-with-the-lab"></a>랩에 등록된 사용자 보기

왼쪽 메뉴에서 **사용자**를 선택하여 랩에 등록된 사용자 목록을 봅니다. 

![랩에 등록된 사용자 목록](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>사용자당 할당량 설정
다음 단계를 사용하여 사용자당 할당량을 설정할 수 있습니다. 

1. 왼쪽 메뉴에서 **사용자**를 선택합니다.
2. 도구 모음에서 **사용자당 할당량: 제한 없음**을 선택합니다. 
3. **사용자당 할당량** 페이지에서 다음 옵션 중 하나를 선택합니다. 
    1. **없음**. 사용자는 예약된 시간 동안 또는 랩 소유자가 가상 머신을 사용하도록 설정한 경우에만 가상 머신을 사용할 수 있습니다.
    2. **제한 없음(기본값)**. 사용자가 시간 제한 없이 가상 머신을 사용할 수 있습니다.
    3. **사용자당 시간 수 지정**. 사용자는 예약된 시간 외에도 설정된 시간(아래에 지정) 동안 가상 머신을 사용할 수 있습니다. 이 옵션을 선택하는 경우 텍스트 상자에 **시간**을 입력합니다. 

        ![사용자당 시간 수](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. **저장**을 선택합니다. 
5. 이제 도구 모음에서 변경된 값을 확인할 수 있습니다. **사용자당 할당량: &lt;시간 수&gt;** 

    ![사용자당 할당량](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [VM의 예약된 실행 시간](how-to-create-schedules.md)은 사용자에게 할당된 할당량에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간입니다. 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV 파일을 업로드하여 사용자 추가
사용자의 이메일 주소가 있는 CSV 파일을 업로드하여 사용자를 추가할 수도 있습니다.

1. 하나의 열에 사용자의 이메일 주소가 포함된 CSV 파일을 만듭니다.

    ![사용자당 할당량](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 랩의 **사용자** 페이지에서 도구 모음의 **CSV 업로드**를 선택합니다.

    ![CSV 업로드 단추](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 사용자 이메일 주소가 있는 CSV 파일을 선택합니다. CSV 파일을 선택한 후 **열기**를 선택하면 다음 **사용자 추가** 창이 표시됩니다. 이메일 주소 목록은 CSV 파일의 이메일 주소로 채워집니다. 

    ![CSV 파일의 이메일 주소로 채워진 사용자 추가 창](../media/how-to-configure-student-usage/add-users-window.png)
4. **사용자 추가** 창에서 **저장**을 선택합니다. 
5. 사용자 목록에 사용자가 표시되는지 확인합니다. 

    ![추가된 사용자 목록](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>사용자 VM 관리
학생이 사용자가 입력한 등록 링크를 사용하여 Azure Lab Services에 등록하면 **가상 머신** 탭에서 학생에게 할당된 VM이 표시됩니다. 

![학생에게 할당된 가상 머신](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

학생 VM에서 다음 작업을 수행할 수 있습니다. 

- VM이 실행 중인 경우 VM 중지 
- VM이 중지된 경우 VM 시작 
- VM에 연결합니다. 
- VM을 삭제합니다. 
- 사용자가 가상 머신을 사용한 시간을 봅니다. 

## <a name="update-number-of-virtual-machines-in-lab"></a>랩에서 가상 머신 수 업데이트
랩에서 가상 머신의 수를 업데이트하려면 **가상 머신** 페이지에서 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **가상 머신**을 선택합니다. 
2. 도구 모음에서 **랩 용량: &lt;number&gt;개 머신**을 선택합니다. 
3. 가상 머신의 **수**를 입력합니다.
4. **저장**을 선택합니다.

    ![랩의 가상 머신](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 템플릿 설정 및 게시](how-to-create-manage-template.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
