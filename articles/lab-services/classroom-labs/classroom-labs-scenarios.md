---
title: 클래스 룸 랩 교육-Azure Lab Services를 사용 하 여 | Microsoft Docs
description: 학습 시나리오에 Azure DevTest Labs를 사용하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695294"
---
# <a name="use-classroom-labs-for-trainings"></a>학습에 사용 하 여 클래스 룸 랩
학습 하기 위한 랩을 설정할 수 있습니다. Azure Lab Services의 클래스 룸 랩의 각 실습생이 학습에 대 한 격리 된 환경과 동일한을 사용 하는 교육에 대 한 랩을 만들 수 있습니다. 각 실습생이 필요할 때에만 교육 환경을 이용할 수 있도록 하고 교육에 필요한 리소스(예: 가상 머신)를 충분히 포함하는 정책을 적용할 수 있습니다. 

![클래스 룸 랩](../media/classroom-labs-scenarios/classroom.png)

클래스 룸 랩에는 모든 가상 환경에서 학습을 수행 하는 데 필요한 다음 요구 사항을 충족 합니다. 

- 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
- 모든 학습 컴퓨터는 동일해야 합니다.
- 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
- 실습생이 학습에 필요한 만큼의 VM만 사용하고 사용하지 않을 때는 VM을 종료하도록 하여 비용을 제어합니다.
- 각 실습생과 학습 랩을 쉽게 공유합니다.
- 학습 랩을 반복해서 다시 사용합니다.

이 문서에서는 학습용 랩을 설정 하는 데 수행할 수 있는 자세한 단계와 앞에서 설명한 학습 요구 사항을 충족 하기 위해 사용할 수 있는 다양 한 Azure Lab Services 기능에 대해 알아봅니다.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>랩 계정 관리자로 랩 계정 만들기
Azure Lab Services를 사용 하 여 첫 번째 단계는 Azure portal에서 랩 계정을 만드는 것입니다. 랩 계정에서 랩 계정 관리자를 만든 후 관리자에 랩을 만들고 싶으세요 사용자를 추가 하는 합니다 **랩 작성자** 역할입니다. 교육 담당자는 학생에 게는 자신이 가르치는 과정에 대 한 연습을 수행 하기 위해 virtual machines를 사용 하 여 실습을 만듭니다. 세부 정보를 참조 하세요 [만들기 및 관리 랩 계정을](how-to-manage-lab-accounts.md)합니다.

## <a name="create-and-manage-classroom-labs"></a>만들고 클래스 룸 랩 관리
랩 계정 내에서 랩 작성자 역할의 구성원 인 강사를 랩 계정에 하나 이상의 실습을 만들 수 있습니다. 만들고 과정의 연습을 수행 하기 위한 모든 필수 소프트웨어를 사용 하 여 VM 템플릿을 구성 합니다. 클래스 룸 랩 만들기에 대 한 사용 가능한 이미지에서 바로 사용할 수 있는 이미지를 선택 하 고 랩에 필요한 소프트웨어를 설치 하 여 사용자 지정 합니다. 세부 정보를 참조 하세요 [만들기 및 클래스 룸 랩 관리](how-to-manage-classroom-labs.md)합니다.

## <a name="configure-usage-settings-and-policies"></a>사용 설정 및 정책 구성
랩 작성자 추가 하거나 랩에 사용자를 제거, 랩 등에서 사용할 수 있는 Vm 수를 업데이트 하는 사용자별로 개별 할당량 설정 같은 정책을 설정 하 고 랩 사용자에 게 등록 링크를 가져올 수 있습니다. 자세한 내용은 참조 하세요 [사용 설정 및 정책을 구성](how-to-configure-student-usage.md)합니다.

## <a name="create-and-manage-schedules"></a>일정 만들기 및 관리
일정을 사용하면 랩의 VM이 지정된 시간에 자동으로 시작 및 종료되도록 Classroom(강의실) 랩을 구성할 수 있습니다. 일회성 일정이나 되풀이 일정을 정의할 수 있습니다. 자세한 내용은 참조 하세요 [만들기 클래스 룸 랩에 대 한 일정을 관리 하 고](how-to-create-schedules.md)입니다.

## <a name="set-up-and-publish-a-template-vm"></a>설정 하 고 VM 템플릿을 게시합니다
랩의 템플릿은 모든 사용자의 가상 머신이 만들어지는 기본 가상 머신 이미지입니다. 구성 된 정확 하 게 원하는 교육 참석자에 게 제공 되도록 템플릿 VM 설정 합니다. 랩 사용자에게 표시되는 템플릿의 이름 및 설명을 제공할 수 있습니다. 그런 후 랩 사용자가 템플릿 VM의 인스턴스를 사용할 수 있도록 템플릿을 게시합니다. 템플릿을 게시하면 Azure Lab Services가 템플릿을 사용하여 랩에 VM을 만듭니다. 이 과정에서 만들어지는 VM 수는 랩에 허용되는 최대 사용자 수와 같으며, 이 값은 랩의 사용 정책에서 설정할 수 있습니다. 모든 가상 머신은 템플릿과 구성이 동일합니다. 세부 정보를 참조 하세요 [설정 및 가상 컴퓨터 템플릿을 게시](how-to-create-manage-template.md)합니다. 

## <a name="use-vms-in-the-classroom-lab"></a>클래스 룸 랩에 Vm을 사용 합니다.
학생 또는 교육 참석자를 랩에 등록 하 고 과정에 대 한 연습을 위해 VM에 연결 합니다. 자세한 내용은 참조 하세요 [클래스 룸 랩에 액세스 하는 방법을](how-to-use-classroom-lab.md)합니다.

## <a name="next-steps"></a>다음 단계
문서의 지침에 따라 클래스 룸 랩의 랩 계정 만들기를 시작 합니다. [자습서: Azure Lab Services로 랩 계정 설정](tutorial-setup-lab-account.md)합니다.