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
ms.openlocfilehash: c5514a43602106cf045b575d289e02b591468359
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561666"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Azure DevTest Labs 인프라의 거버넌스 - 리소스
이 문서에서는 조직 내 DevTest Labs용으로 리소스를 조정하고 관리하는 방법을 설명합니다. 

## <a name="align-within-an-azure-subscription"></a>Azure 구독 내의 조정 

### <a name="question"></a>질문
Azure 구독 내에서 DevTest Labs 리소스를 조정하려면 어떻게 해야 하나요?

### <a name="answer"></a>응답
조직에서 Azure를 사용한 일반 애플리케이션 개발을 시작하려면 IT 플래너가 먼저 전체 서비스 포트폴리오의 일부분으로 기능을 도입하는 방법을 검토해야 합니다. 검토 영역에는 다음과 같은 문제가 포함되어야 합니다.

- 애플리케이션 개발 수명 주기 관련 비용을 측정하는 방법
- 조직에서 제안 서비스 제품을 회사 보안 정책에 맞게 조정하는 방법 
- 개발 환경과 프로덕션 환경 분리를 위한 세그먼트화의 필요 여부 
- 관리, 안정성 및 확장을 장기적으로 쉽게 진행하기 위해 도입되는 제어 기능

**첫 번째 권장 방식**은 프로덕션 구독과 개발 구독을 구분하는 방식이 요약되어 있는 조직의 Azure 분류법을 검토하는 것입니다. 아래 다이어그램에 나와 있는 제안 분류법을 사용하면 개발/테스트 환경과 프로덕션 환경을 논리적으로 분리할 수 있습니다. 이 방식을 사용하는 경우 조직은 각 환경과 연결된 비용을 개별적으로 추적하는 청구 코드를 도입할 수 있습니다. 자세한 내용은 [규범적 구독 거버넌스](/azure/architecture/cloud-adoption/appendix/azure-scaffold)를 참조하세요. 또한 [Azure 태그](../azure-resource-manager/resource-group-using-tags.md)를 사용하여 추적 및 청구용으로 리소스를 구성할 수도 있습니다.

**두 번째 권장 방식**은 Azure Enterprise Portal 내에서 DevTest 구독을 사용하도록 설정하는 것입니다. 이 경우 조직은 Azure Enterprise 구독에서 일반적으로 제공되지 않는 클라이언트 운영 체제를 실행할 수 있습니다. 그런 다음, 라이선싱에 신경쓰지 않고 수행하는 계산에 대한 요금만 결제하는 엔터프라이즈 소프트웨어를 사용하면 됩니다. 이 경우 Microsoft SQL Server와 같은 IaaS의 갤러리 이미지를 비롯한 지정된 서비스의 대금이 사용량을 기준으로만 청구됩니다. EA(기업계약) 고객의 경우 [여기](https://azure.microsoft.com/offers/ms-azr-0148p/)에서, 그리고 종량제 고객의 경우 [여기](https://azure.microsoft.com/offers/ms-azr-0023p/)에서 Azure DevTest 구독에 대한 세부 정보를 확인할 수 있습니다.

![구독에 맞게 리소스 조정](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

이 모델을 사용하는 조직은 Azure DevTest Labs를 원하는 규모로 유연하게 배포할 수 있습니다. 조직은 병렬로 실행되는 가상 머신 100~1,000개가 포함된 랩 수백 개를 여러 사업부용으로 지원할 수 있습니다. 따라서 동일한 구성 관리 및 보안 제어 원칙을 공유할 수 있는 중앙 집중식 엔터프라이즈 랩 솔루션의 개념을 더욱 확실하게 구축할 수 있습니다.

또한 이 모델을 사용하는 조직에서는 Azure 구독과 연결된 리소스 한도가 소진되는 상황이 발생하지 않습니다. 구독 및 서비스 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요. DevTest Labs 프로비전 프로세스에서는 리소스 그룹을 많이 사용할 수 있습니다. Azure DevTest 구독의 지원 요청을 통해 한도 상향을 요청할 수 있습니다. 개발 구독 사용량이 늘어도 프로덕션 구독 내의 리소스에는 영향이 없습니다. DevTest Labs 크기 조정에 대한 자세한 내용은 [DevTest Labs의 할당량 및 한도 규모 조정](devtest-lab-scale-lab.md)을 참조하세요.

일반적으로 고려해야 하는 구독 수준 제한은 프로덕션 구독과 개발 구독을 모두 지원하기 위해 네트워크 IP 범위가 할당되는 방식입니다. 기업이 Azure 구현을 기본적으로 사용하지 않고 네트워킹 스택을 직접 관리해야 하는 다른 네트워킹 토폴로지나 온-프레미스 연결이 사용된다고 가정할 때, 이러한 할당에서는 장기적인 확장을 고려해야 합니다. 권장 방식은 서브넷이 몇 개만 포함된 가상 네트워크를 여러 개 사용하는 대신 대규모 IP 주소 접두사가 할당되며 큰 서브넷 여러 개로 구분되는 적은 수의 가상 네트워크를 사용하는 것입니다. 예를 들어 구독이 10개이면 가상 네트워크를 구독당 하나씩 10개 정의할 수 있습니다. 격리하지 않아도 되는 모든 랩은 구독의 가상 네트워크에서 같은 서브넷을 공유할 수 있습니다.

## <a name="maintain-naming-conventions"></a>명명 규칙 유지 관리

### <a name="question"></a>질문
DevTest Labs 환경 전반에 걸쳐 명명 규칙을 유지 관리하려면 어떻게 해야 하나요?

### <a name="answer"></a>응답
현재 엔터프라이즈 명명 규칙을 Azure 작업으로 확장 적용하고 DevTest Labs 환경에서도 일관되게 사용할 수 있습니다.

DevTest Labs를 배포할 때는 구체적인 시작 정책을 결정하는 것이 좋습니다. 일관성을 유지하려면 중앙 스크립트와 JSON 템플릿을 사용하여 이러한 정책을 배포합니다. 구독 수준에서 적용된 Azure 정책을 통해 명명 정책을 구현할 수 있습니다. Azure Policy의 JSON 샘플은 [Azure Policy 샘플](../governance/policy/samples/index.md)을 참조하세요.

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>랩당 사용자 수/조직당 랩 수

### <a name="question"></a>질문 
랩당 사용자 비율과 조직 전체에서 필요한 전체 랩 수는 어떻게 결정할 수 있나요?

### <a name="answer"></a>응답
동일 개발 프로젝트와 연결된 사업부와 개발 그룹은 같은 랩에 연결하는 것이 좋습니다. 그러면 같은 유형의 정책, 이미지 및 종료 정책을 두 그룹에 모두 적용할 수 있습니다. 

지리적 경계도 고려해야 할 수 있습니다. 예를 들어 미국 북동부의 개발자는 미국 동부 2에 프로비전된 랩을 사용할 수 있습니다. 그리고 텍사스 주 댈러스와 콜로라도 주 덴버의 개발자는 미국 중남부의 리소스를 사용하도록 지정될 수 있습니다. 외부 타사와 공동 작업을 진행하는 경우 내부 개발자가 사용하지 않는 랩에 타사를 할당할 수 있습니다. 

Azure DevOps 프로젝트 내에서 특정 프로젝트에 대해 랩을 사용할 수도 있습니다. 그리고 지정된 Azure Active Directory 그룹을 통해 보안을 적용하면 두 리소스 집합에 모두 액세스할 수 있습니다. 랩에 할당된 가상 네트워크가 사용자를 통합하는 또 다른 경계로 사용될 수도 있습니다.

## <a name="deletion-of-resources"></a>리소스 삭제

### <a name="question"></a>질문
랩 내에서 리소스 삭제를 방지하려면 어떻게 해야 하나요?

### <a name="answer"></a>응답
권한이 부여된 사용자만 리소스를 삭제하거나 랩 정책을 변경할 수 있도록 랩 수준에서 적절한 권한을 설정하는 것이 좋습니다. 개발자는 **DevTest Labs 사용자** 그룹 내에 포함되어야 합니다. 수석 개발자 또는 인프라 책임자는 **DevTest Labs 소유자**여야 합니다. 랩 소유자는 두 명만 지정하는 것이 좋습니다. 손상 방지를 위해 이 정책은 코드 리포지토리로 확대 적용됩니다. 랩 사용자는 리소스를 사용할 수는 있지만 랩 정책을 업데이트할 수는 없습니다. 각 기본 그룹이 랩 내에서 소유하는 역할과 권한 목록이 나열된 [Azure DevTest Labs에 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md) 문서를 참조하세요.

## <a name="move-lab-to-another-resource-group"></a>다른 리소스 그룹으로 랩 이동 

### <a name="question"></a>질문
랩을 다른 리소스 그룹으로 이동할 수 있나요?

### <a name="answer"></a>응답
예. 랩의 홈 페이지에서 리소스 그룹 페이지로 이동합니다. 그런 다음 도구 모음에서 **이동**을 선택하고 다른 리소스 그룹으로 이동할 랩을 선택합니다. 랩을 만들면 리소스 그룹이 자동으로 생성됩니다. 하지만 엔터프라이즈 명명 규칙을 따르는 다른 리소스 그룹으로 랩을 이동할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[비용 및 소유권](devtest-lab-guidance-governance-cost-ownership.md)에 대해 알아봅니다.
