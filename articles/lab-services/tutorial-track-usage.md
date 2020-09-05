---
title: Azure Lab Services에서 랩 사용량 추적 | Microsoft Docs
description: 이 자습서에서는 랩 작성자/소유자로서 랩 사용량을 추적합니다.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: a9a9b49b568decc621be1969a8578d61ac7e4861
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85445035"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>자습서: Azure Lab Services에서 랩 사용량 추적
이 자습서에서는 랩 작성자/소유자가 랩 사용량을 추적하는 방법을 보여줍니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 랩에 등록된 사용자 보기
> * 랩의 VM 사용량 보기
> * 학생 VM 관리 


## <a name="view-registered-users"></a>등록된 사용자 보기

1. [Azure Lab Services 웹 사이트](https://labs.azure.com)로 이동합니다. 
2. **로그인**을 선택하고 자격 증명을 입력합니다. Azure Lab Services는 조직 계정 및 Microsoft 계정을 지원합니다.
3. **내 랩** 페이지에서, 사용량을 추적하려는 랩을 선택합니다. 
4. 왼쪽 메뉴에서 **사용자** 또는 **사용자** 타일을 선택합니다. 랩에 등록한 학생이 표시됩니다.  

    ![등록된 사용자](./media/tutorial-track-usage/registered-users.png)

    랩 사용자를 추가하고 관리하는 방법에 대한 자세한 내용은 [랩 사용자 추가 및 관리](how-to-configure-student-usage.md)를 참조하세요.

## <a name="view-the-usage-of-vms"></a>VM 사용량 보기

1. 왼쪽 메뉴에서 **가상 머신**을 선택합니다. 
2. VM 상태 및 VM 실행 시간이 보이는지 확인합니다. 학생 VM에서 랩 소유자가 소비한 시간은 마지막 열에 표시된 사용량 시간에 포함되지 않습니다. 

    ![VM 사용량](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>학생 VM 관리 
이 페이지에서 **Status** 열 또는 도구 모음 목록에서 컨트롤을 사용하여 학생 VM을 시작, 중지 또는 다시 설정할 수 있습니다.

![VM 작업](./media/tutorial-track-usage/vm-controls.png)

랩의 가상 머신 풀을 관리하는 방법에 대한 자세한 내용은 [가상 머신 풀 설정 및 관리](how-to-set-virtual-machine-passwords.md)를 참조하세요.

> [!NOTE]
> 교육자가 학생 VM을 켜는 경우 학생에 대한 할당량에 영향을 주지 않습니다. 사용자에 대한 할당량은 사용자가 예약된 수업 시간 이외의 시간에 사용할 수 있는 랩 시간 수를 지정합니다. 할당량에 대한 자세한 내용은 [사용자에 대한 할당량 설정](how-to-configure-student-usage.md?#set-quotas-for-users)을 참조하세요.

## <a name="next-steps"></a>다음 단계
클래스룸 랩에 대한 자세한 내용은 [방법 가이드](how-to-manage-lab-accounts.md)의 문서를 참조하세요.
