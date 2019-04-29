---
title: Azure DevTest Labs 인프라의 거버넌스
description: 이 문서에서는 Azure DevTest Labs 인프라 거버넌스를 위한 지침을 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 7832691812d8f10342dc7df20a7cfab7265f2d9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775715"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure DevTest Labs 인프라의 거버넌스 - 비용 및 소유권 관리
비용 및 소유권은 개발 및 테스트 환경의 구축을 고려할 때 주요 관심사입니다. 이 섹션에서는 비용을 최적화하고 사용자 환경에서 소유권을 조정하는 데 유용한 도움이 되는 정보를 찾을 수 있습니다.

## <a name="optimize-for-cost"></a>비용 최적화

### <a name="question"></a>질문
DevTest Labs 환경 내에서 비용을 최적화하려면 어떻게 해야 하나요?

### <a name="answer"></a>응답
비용 최적화에 도움이 되는 DevTest Labs의 다양한 기본 제공 기능이 있습니다. 사용자 작업을 제한하려면 [비용 관리, 임계값](devtest-lab-configure-cost-management.md) [ 및 정책](devtest-lab-set-lab-policy.md) 문서를 참조하세요. 

개발 및 테스트 워크로드에 DevTest Labs를 활용할 때 기업계약의 일부로 [Enterprise 개발/테스트 구독 혜택](https://azure.microsoft.com/offers/ms-azr-0148p/)을 사용하는 것이 적절할 수 있습니다. 또는 종량제 고객인 경우 [종량제 DevTest 제안](https://azure.microsoft.com/offers/ms-azr-0023p/)을 고려할 수 있습니다.

이 접근 방식은 다음과 같은 여러 이점을 제공할 수 있습니다.

- Windows Virtual Machines, Cloud Services, HDInsight, App Service 및 Logic Apps에 대한 저렴한 특별 개발/테스트 요금
- 다른 Azure 서비스에 대한 높은 EA(기업계약) 요금
- Windows 8.1 및 Windows 10 등 갤러리의 전용 개발/테스트 이미지에 액세스
 
현재 Visual Studio를 구독(표준 구독, 연간 클라우드 구독 및 월간 클라우드 구독) 중인 경우에만 Enterprise 개발/테스트 구독 내에서 실행되는 Azure 리소스를 사용할 수 있습니다. 그러나 최종 사용자는 애플리케이션에 액세스하여 피드백을 제공하거나 승인 테스트를 수행할 수도 있습니다. 이 구독 내에서 리소스 사용은 애플리케이션 개발 및 테스트로 제한되며, 작동 시간은 보장되지 않습니다.

DevTest 제안을 사용하기로 결정한 경우 이 혜택이 개발 및 애플리케이션 테스트에만 해당된다는 것에 유의합니다. Azure DevOps 및 HockeyApp을 사용하는 경우를 제외하고 구독 내에서의 사용에는 재정적으로 지원되는 SLA가 적용되지 않습니다.

## <a name="define-a-role-based-access-across-your-organization"></a>조직 전체에서 역할 기반 액세스 정의
### <a name="question"></a>질문
개발자/테스트 담당자가 해당 작업을 수행하는 동안 IT 부서에서 관리할 수 있도록 내 DevTest Labs의 역할 기반 액세스 제어를 정의하려면 어떻게 해야 하나요? 

### <a name="answer"></a>응답
광범위하게 적용되는 한 가지 패턴이 있지만 세부 사항은 조직에 따라 다릅니다.

중앙 IT 부서는 필요한 사항만 소유하고 프로젝트 및 애플리케이션 팀이 필요한 수준의 제어 권한을 갖도록 하는 것이 좋습니다. 이에 따라 일반적으로 중앙 IT 부서는 구독을 소유하고 네트워킹 구성과 같은 핵심 IT 기능을 처리합니다. 구독의 **소유자** 집합은 소규모인 것이 좋습니다. 이러한 소유자는 필요할 때 추가 소유자를 지정하거나, 구독자 수준 정책(예: “공용 IP 없음”)을 적용할 수 있습니다.

계층 1 또는 계층 2 지원과 같이 구독 전반에서 액세스 권한이 필요한 사용자 하위 집합도 있을 수 있습니다. 이 경우 이러한 사용자에게 리소스를 관리할 수 있도록 **참가자** 액세스 권한을 부여하되, 사용자 액세스 또는 정책 조정 권한은 부여하지 않는 것이 좋습니다.

DevTest Labs 리소스는 프로젝트/응용 프로그램 팀에 가까운 소유자가 소유하는 것이 좋습니다. 컴퓨터 및 필수 소프트웨어에 대한 자신들의 요구 사항을 잘 이해하고 있기 때문입니다. 대부분의 조직에서 이 DevTest Labs 리소스의 소유자는 일반적으로 프로젝트/개발 책임자입니다. 이 소유자는 랩 환경 내에서 사용자 및 정책을 관리하고 DevTest Labs 환경에서 모든 VM을 관리할 수 있습니다.

프로젝트/응용 프로그램 팀 멤버를 DevTest Labs 사용자 역할에 추가하는 것이 좋습니다. 이러한 사용자는 가상 머신을 만들 수 있습니다(랩 및 구독 수준 정책에 따라). 또한 자신의 가상 머신을 관리할 수도 있습니다. 다른 사용자에게 속하는 가상 머신은 관리할 수 없습니다.

자세한 내용은 [Azure 엔터프라이즈 스캐폴드: 규범적 구독 거버넌스](/azure/architecture/cloud-adoption/appendix/azure-scaffold) 설명서를 참조하세요.


## <a name="next-steps"></a>다음 단계
[회사 정책 및 규정 준수](devtest-lab-guidance-governance-policy-compliance.md)를 참조하세요.
