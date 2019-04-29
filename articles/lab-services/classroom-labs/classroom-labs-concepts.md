---
title: 클래스 룸 랩 개념-Azure Lab Services | Microsoft Docs
description: 랩 서비스와 어떻게 수 있도록 하기 쉽게 만들고 랩 관리의 기본 개념에 알아봅니다.
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 82f9d7090d7283e0b00b36e0928fffb3395ca4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679089"
---
# <a name="classroom-labs-concepts"></a>클래스룸 랩 개념
다음은 Lab Services의 주요 개념 및 정의 포함 되어 있습니다.

## <a name="quota"></a>할당량
할당량은 제한 시간 (시간)에서 교사 설정할 수 있는 랩 VM을 사용 하는 학생에 대 한 합니다. 0, 무제한 또는 특정 시간 기간을 설정할 수 있습니다. 할당량을 0으로 설정 하는 경우 학생만 사용할 수 가상 컴퓨터 또는 교사 수동으로 바뀐 경우 가상 머신에서 학생에 대 한 일정을 실행 중인 경우.
 
## <a name="schedules"></a>일정
일정은 교사 클래스를 만들 수 있는 시간 슬롯 (한 번 또는 되풀이)입니다. 처음부터 일정에 자동으로 시작 되는 랩의 모든 virtual machines 및 일정 끝날 때 중지 합니다. 할당량 시간 일정에 실행 중일 때 사용 되지 않습니다.

## <a name="template-virtual-machine"></a>가상 컴퓨터 템플릿
랩에서 템플릿 가상 컴퓨터에는 모든 사용자의 가상 컴퓨터 생성 되는 기본 가상 머신 이미지입니다. 강사/랩 작성자 템플릿 가상 머신을 설정 하 고 실습을 위해 학습 참석자에 게 제공 하고자 하는 소프트웨어를 사용 하 여 구성 합니다. 템플릿으로 VM을 게시 하면 Azure Lab Services 만들거나 VM 템플릿을 기반으로 하는 랩 Vm을 업데이트 합니다. 


## <a name="user-profiles"></a>사용자 프로필
이 문서에서는 Azure Lab Services에 있는 다른 사용자 프로필을 설명합니다. 

### <a name="lab-account-owner"></a>랩 계정 소유자
Azure 구독을 소유한 조직내 클라우드 리소스의 IT 관리자는 일반적으로 랩 계정 소유자 역할을 하며 다음 작업을 수행합니다.   

- 조직에 대한 랩 계정을 설정합니다.
- 모든 랩에서 정책을 관리하고 구성합니다.
- 조직의 사람들에게 랩 계정으로 랩을 만들 수있는 권한을 부여합니다.

### <a name="professor"></a>교수
일반적으로 강사 또는 온라인 강사와 같은 사용자는 랩 계정에 속한 클래스룸 랩을 만듭니다. 강사가 수행하는 작업은 다음과 같습니다. 

- 클래스룸 랩을 만듭니다.
- 랩에서 가상 머신을 만듭니다. 
- 가상 머신에 적절한 소프트웨어를 설치합니다.
- 랩에 액세스할 수 있는 사용자를 지정합니다.
- 학생들에게 랩에 대한 등록 링크를 제공합니다.

### <a name="student"></a>학생
학생이 수행하는 작업은 다음과 같습니다.

- 랩 사용자는 랩 작성자로부터 받은 등록 링크를 사용하여 랩에 등록합니다. 
- 랩의 가상 머신에 연결하고 수업, 과제 및 프로젝트를 수행하는 데 사용합니다. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 클래스룸 랩을 만드는 데 필요한 랩 계정을 설정합니다.

- [랩 계정 설정](tutorial-setup-lab-account.md)
