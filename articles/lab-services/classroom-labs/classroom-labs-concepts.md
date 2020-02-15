---
title: 교실 랩 개념-Azure Lab Services | Microsoft Docs
description: Lab Services의 기본 개념과이를 사용 하 여 랩을 손쉽게 만들고 관리할 수 있는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 348340516f9332f5492c7ce60c3d164da44a008c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120888"
---
# <a name="classroom-labs-concepts"></a>클래스룸 랩 개념

다음 목록에는 주요 랩 서비스 개념 및 정의가 포함 되어 있습니다.

## <a name="quota"></a>할당량

할당량은 교사가 랩 VM을 사용 하도록 학생에 대해 설정할 수 있는 시간 제한 (시간)입니다. 0 또는 특정 시간 수로 설정할 수 있습니다. 할당량이 0으로 설정 되 면 학생이 일정을 실행 하는 경우 또는 교사가 학생의 가상 컴퓨터를 수동으로 켤 때만 가상 컴퓨터를 사용할 수 있습니다.  

학생이 랩 VM 자체를 시작할 때 할당량 시간이 계산 됩니다.  교사가 학생에 대해 랩 VM을 수동으로 시작 하면 해당 학생에 게 할당량 시간이 사용 되지 않습니다.

## <a name="schedules"></a>일정

일정은 교사가 클래스에 대해 만들 수 있는 시간 슬롯 (일회성 또는 되풀이)입니다. 랩의 모든 가상 머신은 일정 시작 시 자동으로 시작 되며 일정 종료 시 중지 됩니다. 일정을 실행 하는 동안에는 할당량 시간이 사용 되지 않습니다.

## <a name="template-virtual-machine"></a>템플릿 가상 컴퓨터

랩의 템플릿 가상 머신은 모든 사용자의 가상 머신을 만드는 기본 가상 머신 이미지입니다. 교육 담당자/랩 작성자는 템플릿 가상 머신을 설정 하 고 랩을 교육 하기 위해 제공 하려는 소프트웨어를 사용 하 여 구성 합니다. 템플릿 VM을 게시 하는 경우 템플릿 VM을 기반으로 랩 Vm을 만들거나 업데이트 Azure Lab Services 합니다.

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
