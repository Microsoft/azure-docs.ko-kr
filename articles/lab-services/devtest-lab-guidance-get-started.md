---
title: Azure DevTest Labs를 사용 하는 인기 있는 시나리오
description: 이 문서에서는 Azure DevTest Labs 및 두 가지 일반적인 경로 사용 하 여 조직에서 서비스 사용을 시작 하는 기본 시나리오를 제공 합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773811"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Azure DevTest Labs를 사용 하는 인기 있는 시나리오
엔터프라이즈의 요구에 따라 DevTest Labs는 서로 다른 요구 사항을 충족 하도록 구성할 수 있습니다.  이 문서에서는 널리 사용 되는 시나리오를 설명합니다. 각 시나리오에서는 이러한 시나리오를 구현 하는 데 DevTest Labs 및 리소스를 사용 하 여 가져온 혜택을 설명 합니다.  

- 개발자 데스크톱
- 테스트 환경
- 교육 세션, 실습 랩 및 해커 톤
- 샌드박스가 적용 된 조사
- 클래스룸 랩

## <a name="developer-desktops"></a>개발자 데스크톱
개발자가 각 프로젝트에 사용하는 개발 컴퓨터의 요구 사항은 서로 다른 경우가 많습니다. DevTest Labs를 사용 하 여 개발자는 해당 가장 일반적인 시나리오에 맞게 구성 된 주문형 가상 컴퓨터에 액세스할을 수 있습니다. DevTest Labs에서는 다음과 같은 이점을 제공합니다.

- 조직에서는 팀 전체에서 일관성을 유지 하는 일반적인 개발 컴퓨터를 제공할 수 있습니다.
- 개발자가 신속 하 게 필요에 따라 해당 개발 컴퓨터를 프로 비전 할 수 또는 [미리 구성 된 기존 컴퓨터를 클레임](devtest-lab-add-claimable-vm.md)합니다.
- 개발자는 구독 수준 사용 권한 없이 셀프 서비스 방식으로 리소스를 프로비저닝할 수 있습니다.
- IT 관리자 수도 [미리 네트워킹 토폴로지를 정의](devtest-lab-configure-vnet.md) 개발자가 직접 사용 하 여 간단 하 고 직관적인 방식으로 모든 특별 한 액세스를 요구 하지 않고 있습니다.
- 개발자가 손쉽게 수행할 수 있습니다 [사용자 지정](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 필요에 따라 개발 컴퓨터.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 개발자 [더 많은 Vm을 가져올 수 없습니다](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) 개발에 필요한 것 보다
    - [Vm은 종료](devtest-lab-set-lab-policy.md#set-auto-shutdown) 사용 하지 않을 때
    - 만 [VM 인스턴스 크기의 하위 집합 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) 특정 랩
    - [비용 목표 및 알림을 관리](devtest-lab-configure-cost-management.md) 각 랩에 대 한 합니다.

추가 정보 참조 [개발자를 위한 Azure DevTest Labs를 사용 하 여](devtest-lab-developer-lab.md)입니다. 

## <a name="test-environments"></a>테스트 환경
기업 전반에서 테스트 환경을 만들고 관리하려면 많은 작업을 수행해야 합니다. DevTest Labs를 사용 하 여 테스트 환경 수 쉽게 생성, 업데이트 또는 복제 합니다. 팀을 필요할 때 완전히 구성 된 환경에 액세스할 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 조직에는 팀에서 일관성을 유지 하는 일반적인 테스트 환경 제공할 수 있습니다.
- 테스터 신속 하 게 재사용 가능한 템플릿을 사용 하 여 Windows 및 Linux 환경을 프로비저닝 하 여 해당 응용 프로그램의 최신 버전을 테스트할 수 있습니다.
- 관리자는 DevOps 시나리오를 사용 하도록 설정 하려면 Azure DevOps에 랩을 연결할 수 있습니다.
- 랩 소유자는 함으로써 비용을 제어할 수 있습니다.
    - [환경에서 Vm을 종료](devtest-lab-set-lab-policy.md#set-auto-shutdown) 사용 하지 않을 때
    - 만 [에 대 한 VM 인스턴스 크기의 하위 집합 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) 특정 실습
    - [비용 목표 및 알림을 관리](devtest-lab-configure-cost-management.md) 각 랩에 대 한 합니다.

자세한 내용은 [사용 하 여 Azure DevTest Labs VM 및 PaaS 테스트 환경](devtest-lab-test-env.md)합니다.

## <a name="sandboxed-investigations"></a>샌드박스가 적용 된 조사
개발자는 종종 다양 한 기술 또는 인프라 디자인에 조사합니다. 기본적으로 DevTest Labs를 사용 하 여 만든 모든 환경 자체 리소스 그룹에 생성 됩니다. DevTest Labs 사용자는 해당 리소스에 대 한 읽기 권한만 가져옵니다. 하지만 좀더 제어가 필요한 개발자를 위한 랩 수준 설정을 업데이트할 수 있도록 [참가자 권한을](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) 자신이 만든 모든 환경에 대 한 원래 DevTest Labs 사용자에 게 합니다.  DevTest Labs를 통해 개발자는 자신이 만든 랩의 환경에 자동으로 대 한 참가자 권한을 부여할 수 있습니다.  이 시나리오에서는 개발자가 추가 및/또는 해당 개발 또는 테스트 환경에 필요한 Azure 리소스를 변경 합니다. 합니다 [리소스별 비용](devtest-lab-configure-cost-management.md#view-cost-by-resource) 페이지 랩 소유자 조사에 사용 되는 각 환경의 비용을 추적할 수 있습니다.

자세한 내용은 [환경 리소스 그룹에 대 한 액세스 권한을 집합](https://aka.ms/dtl-sandbox)합니다.

## <a name="trainings-hands-on-labs-and-hackathons"></a>교육, 실습 및 해커 톤 
Azure DevTest Labs에서 랩 워크숍, 실습, 학습 자료 또는 해커 톤 같은 일시적인 활동에 대 한 훌륭한 컨테이너로 작동합니다.  이 서비스에서는 각 실습생이 동일한 격리형 학습용 환경을 만드는 데 사용할 수 있는 사용자 지정 템플릿을 제공할 수 있는 랩을 만들 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- [정책](devtest-lab-set-lab-policy.md) 실습생이 필요한 가상 머신과 같은 리소스의 수만 얻을 수 있습니다.
- 미리 구성 된 및 만든 머신은 [요청한](devtest-lab-add-claimable-vm.md) 실습생이에서 단일 작업으로 합니다.
- 랩에 액세스 하 여 실습생이 사용 하 여 공유 되 [랩에 대 한 URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)합니다.
- [만료 날짜](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) 가상 머신에서 후 더 이상 필요 없는 컴퓨터 삭제 되었는지 확인 합니다.
- 하기 쉽습니다 [랩 삭제](devtest-lab-delete-lab-vm.md#delete-a-lab) all [관련 된 리소스](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) 학습 스타일러스가 합니다.

자세한 내용은 [학습에 사용 하 여 Azure DevTest Labs](devtest-lab-training-lab.md)합니다.  

## <a name="proof-of-concept-vs-scaled-deployment"></a>확장된 배포 및 개념 증명
DevTest Labs 탐색 하려는 경우 일반 경로 앞으로 두 가지 있습니다. 개념 및 배포 된 증명 합니다.  

**크기가 조정된 배포**에서는 개발자 수가 매우 많은 기업 전체에 DevTest Labs를 배포하기 위해 몇 주/몇 달간 검토 및 계획을 진행합니다.

A **개념 증명** 배포 조직 값을 설정 하는 단일 팀에서 집중적인된 노력이에 중점을 둡니다. 크기가 조정된 배포가 더 쉬워 보일 수도 있지만 이 방식은 개념 증명 옵션보다 실패 빈도가 더 높습니다. 따라서 일단은 소규모로 배포를 시작하고 첫 번째 팀에서 필요한 정보를 파악한 다음 2~3개 팀을 추가해 동일한 방식을 반복한 후에 확인된 사실을 바탕으로 크기가 조정된 배포를 계획하는 것이 좋습니다. 개념을 적절하게 증명하려면 1~2개 팀을 선택하여 해당 시나리오(개발 환경과 테스트 환경용 시나리오)를 파악하고 현재 사용 사례를 문서로 작성한 다음 DevTest Labs를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [DevTest Lab 개념](devtest-lab-concepts.md)
- [DevTest Labs FAQ](devtest-lab-faq.md)

