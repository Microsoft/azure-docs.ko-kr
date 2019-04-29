---
title: Azure Lab Services에서 Classroom(강의실) 랩의 일정 만들기 | Microsoft Docs
description: 랩의 VM이 지정된 시간에 시작 및 종료되도록 Azure Lab Services에서 Classroom(강의실) 랩의 일정을 만드는 방법을 알아봅니다.
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
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695993"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Azure Lab Services에서 Classroom(강의실) 랩의 일정 만들기 및 관리 
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 다음 절차에서는 Classroom(강의실) 랩의 일정을 만들고 관리하는 단계를 제공합니다. 

> [!IMPORTANT]
> VM의 예약된 실행 시간은 [사용자에게 할당된 할당량](how-to-configure-student-usage.md#set-quotas-per-user)에 계산되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다. 

## <a name="add-a-schedule-once"></a>일정 추가(한 번)

1. **일정** 페이지로 전환한 다음, 도구 모음에서 **일정 추가**를 선택합니다. 

    ![일정 페이지의 일정 추가 단추](../media/how-to-create-schedules/add-schedule-button.png)
2. **일정 추가** 페이지의 맨 위에서 **한 번** 옵션이 선택되었는지 확인합니다. 선택되지 않은 경우 **한 번**을 선택합니다. 
3. **일정 날짜(필수)** 에서 날짜를 입력하거나 달력 아이콘을 선택하여 날짜를 선택합니다. 
4. **시작 시간**에서 VM을 시작할 시간을 선택합니다. 중지 시간이 설정되지 않은 경우 시작 시간이 필요합니다. 중지 시간만 지정하려면 **시작 이벤트 제거**를 선택합니다. **시작 시간**을 사용할 수 없는 경우 드롭다운 목록 옆에 있는 **시작 이벤트 추가**를 선택하여 사용합니다. 
5. **중지 시간**에서 VM을 종료할 시간을 선택합니다. 시작 시간이 설정되지 않은 경우 중지 시간이 필요합니다. 시작 시간만 지정하려면 **중지 이벤트 제거**를 선택합니다. **중지 시간**을 사용할 수 없는 경우 드롭다운 목록 옆에 있는 **중지 이벤트 추가**를 선택하여 사용합니다.
6. **표준 시간대(필수)** 에서 지정한 시작 및 중지 시간의 표준 시간대를 선택합니다. 
7. **메모**에서 일정에 대한 설명 또는 메모를 입력합니다. 
8. **저장**을 선택합니다. 

    ![일회성 일정](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>되풀이 일정 추가(매주)

1. **일정** 페이지로 전환한 다음, 도구 모음에서 **일정 추가**를 선택합니다. 

    ![일정 페이지의 일정 추가 단추](../media/how-to-create-schedules/add-schedule-button.png)
2. **일정 추가** 페이지의 맨 위에서 **매주**로 전환합니다. 
3. **일정 요일(필수)** 에서 일정을 적용할 요일을 선택합니다. 다음 예제에서는 월요일-금요일이 선택되었습니다. 
4. **시작** 필드에서 **일정 시작 날짜**를 입력하거나 **달력** 단추를 선택하여 날짜를 선택합니다. 이 필드는 필수입니다. 
5. **일정 종료 날짜**에서 VM을 종료할 종료 날짜를 입력하거나 선택합니다. 
6. **시작 시간**에서 VM을 시작할 시간을 선택합니다. 중지 시간이 설정되지 않은 경우 시작 시간이 필요합니다. 중지 시간만 지정하려면 **시작 이벤트 제거**를 선택합니다. **시작 시간**을 사용할 수 없는 경우 드롭다운 목록 옆에 있는 **시작 이벤트 추가**를 선택하여 사용합니다. 
7. **중지 시간**에서 VM을 종료할 시간을 선택합니다. 시작 시간이 설정되지 않은 경우 중지 시간이 필요합니다. 시작 시간만 지정하려면 **중지 이벤트 제거**를 선택합니다. **중지 시간**을 사용할 수 없는 경우 드롭다운 목록 옆에 있는 **중지 이벤트 추가**를 선택하여 사용합니다.
8. **표준 시간대(필수)** 에서 지정한 시작 및 중지 시간의 표준 시간대를 선택합니다.  
9. **메모**에서 일정에 대한 설명 또는 메모를 입력합니다. 
10. **저장**을 선택합니다. 

    ![주별 일정](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>달력에서 일정 보기
다음 이미지와 같이 예약된 날짜 및 시간이 달력 보기에서 강조 표시된 것을 확인할 수 있습니다.

![달력 보기의 일정](../media/how-to-create-schedules/schedules-in-calendar.png)

오른쪽 위에 있는 **오늘** 단추를 선택하여 달력에서 현재 날짜로 전환합니다. **왼쪽 화살표**를 선택하면 이전 주로 전환되고, **오른쪽 화살표**를 선택하면 달력에서 다음 주로 전환됩니다. 

## <a name="edit-a-schedule"></a>일정 편집
달력에서 강조 표시된 일정을 두 번 클릭하거나 도구 모음의 **연필** 단추를 선택하면 **일정 편집** 페이지가 표시됩니다. 이 페이지의 설정을 업데이트하는 것은 [되풀이 일정 추가](#add-a-recurring-schedule-weekly) 섹션에서 설명한 대로 **일정 추가** 페이지의 설정을 업데이트하는 것과 같습니다. 

![일정 편집 페이지](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>일정 삭제

1. 일정을 삭제하려면 다음 이미지와 같이 도구 모음에서 휴지통(삭제) 단추를 선택합니다.

    ![도구 모음의 삭제 단추](../media/how-to-create-schedules/delete-schedule-button.png)

    달력에서 예약된 날짜 및 시간에 대한 삭제 단추를 사용하고 **삭제**를 선택할 수 있습니다. 
2. **일정 삭제** 페이지에서 **예**를 선택합니다.

    ![일정 삭제 확인](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [관리자 권한으로 랩 계정 만들기 및 관리](how-to-manage-lab-accounts.md)
- [랩 소유자 권한으로 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [랩 소유자 권한으로 랩 사용 구성 및 제어](how-to-configure-student-usage.md)
- [랩 사용자 권한으로 클래스룸 랩 액세스](how-to-use-classroom-lab.md)
