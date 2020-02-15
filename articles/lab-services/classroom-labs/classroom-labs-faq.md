---
title: Azure Lab Services의 교실 랩-FAQ | Microsoft Docs
description: 이 문서에서는 Azure Lab Services의 교실 labs에 대 한 FAQ (질문과 대답)를 제공 합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: a0361203f4a8a2e57d179b39ba6da2fb62f68720
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252062"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure Lab Services의 교실 랩-질문과 대답 (FAQ)
Azure Lab Services에서 교실 labs에 대해 가장 일반적인 질문 중 일부에 대 한 답변을 받으세요. 

## <a name="quotas"></a>할당량

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>사용자 당 또는 주당 할당량 또는 랩의 전체 기간에 대 한 할당량 입니까? 
랩에 대해 설정 하는 할당량은 랩의 전체 기간에 대 한 각 학생에 대해 설정 됩니다. 그리고 [vm의 예약 된 실행 시간은](how-to-create-schedules.md) 사용자에 게 할당 된 할당량에 계산 되지 않습니다. 할당량은 학생이 VM을 사용하는 일정 시간 이외의 시간을 반영합니다.  할당량에 대 한 자세한 내용은 [사용자에 대 한 할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)을 참조 하세요.

## <a name="schedules"></a>일정

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>일정이 설정 되 면 랩의 모든 Vm이 자동으로 시작 됩니까? 
아니요. 모든 Vm이 아닙니다. 일정에 따라 사용자에 게 할당 된 Vm만 사용자에 게 할당 되지 않은 Vm이 자동으로 시작 되지 않습니다. 이것은 의도 된 것입니다. 

## <a name="lab-accounts"></a>랩 계정

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>주소 범위를 사용할 수 없기 때문에 랩을 만들 수 없는 이유는 무엇입니까? 
교실 랩에서는 Azure Portal에서 랩 계정을 만들 때 지정 하는 IP 주소 범위 내에서 랩 Vm을 만들 수 있습니다. 주소 범위가 제공 되 면 랩 Vm에 대해 512 IP 주소를 할당 한 후 생성 된 각 랩입니다. 랩 계정에 대 한 주소 범위는 랩 계정에서 만들려는 랩을 모두 수용할 수 있을 만큼 커야 합니다. 

예를 들어/19-10.0.0.0/19 블록이 있는 경우이 주소 범위는 8192 IP 주소 및 16 개 랩 (8192/512 = 16 labs)을 수용 합니다. 이 경우 17 일 lab 만들기에서 랩 만들기가 실패 합니다.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 컴퓨터에 연결 하려면 조직의 방화벽 설정에서 어떤 포트 범위를 열어야 하나요?

포트는 49152 – 65535입니다. 교실 랩은 부하 분산 장치 뒤에 있으므로 랩의 모든 가상 컴퓨터에는 단일 IP 주소가 있으며 랩의 각 가상 컴퓨터에는 고유한 포트가 있습니다. 포트 번호와 공용 IP 주소는 랩이 다시 게시 될 때마다 변경 될 수 있습니다.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>RDP/SSH를 통해 랩 가상 컴퓨터에 연결 하기 위해 내 조직의 방화벽 설정에서 어떤 공용 IP 주소 범위를 열어야 하나요?
Azure의 데이터 센터에 대 한 공용 IP 주소 범위를 제공 하는 [AZURE Ip 범위 및 서비스 태그 (공용 클라우드)](https://www.microsoft.com/download/details.aspx?id=56519)를 참조 하세요. 랩 계정이 있는 지역에 대 한 IP 주소를 열 수 있습니다.

## <a name="virtual-machine-images"></a>가상 컴퓨터 이미지

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>랩 작성자로 서 새 랩을 만들 때 가상 머신 이미지 드롭다운에서 추가 이미지 옵션을 사용 하도록 설정할 수 없는 이유는 무엇입니까?

관리자가 랩 계정에 랩 작성자로 사용자를 추가 하면 랩을 만들 수 있는 권한이 부여 됩니다. 하지만 사용 하도록 설정 된 가상 컴퓨터 이미지의 목록을 포함 하 여 랩 계정 내의 설정을 편집할 수 있는 권한이 없습니다. 추가 이미지를 사용 하도록 설정 하려면 랩 계정 관리자에 게 문의 하거나 관리자에 게 랩 계정에 참가자 역할로 추가 하도록 요청 하십시오. 참가자 역할은 랩 계정에서 가상 컴퓨터 이미지 목록을 편집할 수 있는 권한을 부여 합니다.

## <a name="users"></a>사용자

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>클래스 룸 랩에서는 얼마나 많은 사용자를 사용할 수 있나요?
클래스 룸 랩에 최대 400 명의 사용자를 추가할 수 있습니다. 

## <a name="blog-post"></a>블로그 게시물
[Azure Lab Services 블로그](https://azure.microsoft.com/blog/tag/azure-lab-services/)를 구독 합니다.

## <a name="update-notifications"></a>업데이트 알림
Lab services [업데이트](https://azure.microsoft.com/updates/?product=lab-services) 를 구독 하 여 lab services의 새로운 기능에 대 한 정보를 계속 받을 수 있습니다.

## <a name="general"></a>일반
### <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?
질문이 여기에 나열 되지 않은 경우 알려 주시기 바랍니다. 답변을 찾을 수 있습니다.

- 이 FAQ의 끝에 질문을 게시합니다. 
- 더 광범위 한 대상에 도달 하려면 [Azure Lab Services-Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-lab-services)에 질문을 게시 하세요. 
- 기능 요청에 대 한 요청 및 아이디어를 [Azure Lab Services-사용자 의견](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)을 제출 합니다.

