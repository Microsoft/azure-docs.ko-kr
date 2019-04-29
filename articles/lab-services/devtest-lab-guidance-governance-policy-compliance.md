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
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127364"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Azure DevTest Labs 인프라의 거버넌스 - 회사 정책 및 규정 준수
이 문서에서는 Azure DevTest Labs 인프라의 회사 정책 및 규정 준수를 제어하기 위한 지침을 제공합니다. 

## <a name="public-vs-private-artifact-repository"></a>공용 및 개인 아티팩트 리포지토리

### <a name="question"></a>질문
조직은 DevTest Labs에서 개인 아티팩트 리포지토리와 공용 아티팩트 리포지토리 중에서 어떤 것을 사용해야 하나요?

### <a name="answer"></a>응답
[공용 아티팩트 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)는 가장 일반적으로 사용되는 초기 소프트웨어 패키지 집합을 제공합니다. 이것은 일반적인 개발자 도구와 추가 기능을 재현하는 데 시간을 들일 필요 없이 빠르게 배포하는 데 도움이 됩니다. 사용자 고유의 개인 리포지토리를 배포하도록 선택할 수 있습니다. 공용 리포지토리와 개인 리포지토리를 함께 사용할 수 있습니다. 공용 리포지토리를 사용하지 않도록 설정할 수도 있습니다. 개인 리포지토리 배포 기준을 다음 질문 및 고려 사항에 따라 결정하는 것이 좋습니다.

- 조직이 해당 DevTest Labs 제안의 일부로 회사용 라이선스 소프트웨어를 보유해야 하나요? 대답이 예인 경우 개인 리포지토리를 생성해야 합니다.
- 조직이 전체 프로비전 프로세스의 일부로 필요한 특정 작업을 제공하는 사용자 지정 소프트웨어를 개발하고 있나요? 대답이 예인 경우 개인 리포지토리를 배포해야 합니다.
- 조직의 거버넌스 정책이 격리를 요구하며 외부 리포지토리 구성을 조직이 직접 관리하지 않는 경우 개인 아티팩트 리포지토리를 배포해야 합니다. 이 프로세스의 일부로, 공용 리포지토리의 초기 복사본을 복사하고 개인 리포지토리에 통합할 수 있습니다. 그런 다음, 조직의 누구도 더 이상 액세스할 수 없도록 공용 저장소를 사용하지 않도록 설정할 수 있습니다. 이 방법은 조직 내의 모든 사용자가 조직이 승인한 단일 리포지토리만을 강제로 보유하게 하고 구성 드리프트를 최소화합니다.

### <a name="single-repository-or-multiple-repositories"></a>단일 리포지토리 또는 여러 리포지토리 

### <a name="question"></a>질문
조직은 단일 리포지토리를 계획해야 하나요 아니면 여러 리포지토리를 허용해야 하나요?

### <a name="answer"></a>응답
조직의 전반적인 거버넌스 및 구성 관리 전략의 일환으로, 중앙 집중식 리포지토리를 사용하는 것이 좋습니다. 여러 리포지토리를 사용하면 시간이 지나면서 관리되지 않는 소프트웨어의 사일로가 될 수 있습니다. 중앙 저장소를 사용하면 여러 팀이 프로젝트에서 이 리포지토리의 아티팩트를 사용할 수 있습니다. 또한 표준화, 보안, 관리 용이성이 구현되고, 중복 작업을 수행할 필요가 없어집니다. 중앙 집중화의 일환으로, 장기 관리 및 지속성을 위해 다음 사례가 권장됩니다.

- Azure 구독이 인증 및 권한 부여에 사용하는 동일한 Azure Active Directory 테넌트에 Azure Repos를 연결합니다.
- Azure Active Directory에 중앙에서 관리되는 **모든 DevTest Labs 개발자** 그룹을 만듭니다. 아티팩트 개발에 참여하는 모든 개발자는 이 그룹에 배치되어야 합니다.
- 동일한 Azure Active Directory 그룹을 사용하여 Azure Repos 리포지토리 및 랩에 대한 액세스 권한을 부여할 수 있습니다.
- Azure Repos에서 분기 또는 포크를 사용하여 기본 프로덕션 리포지토리에서 개발용 리포지토리를 분리해야 합니다. 콘텐츠는 적절한 코드 검토 후에 끌어오기 요청을 통해서만 마스터 분기에 추가됩니다. 코드 검토자가 변경을 승인하면 마스터 분기의 유지 관리를 담당하는 수석 개발자가 업데이트된 코드를 병합합니다. 

## <a name="corporate-security-policies"></a>회사 보안 정책

### <a name="question"></a>질문
조직은 회사 보안 정책이 적절히 설정되도록 하기 위해 어떻게 해야 하나요?

### <a name="answer"></a>응답
조직은 이를 위해 다음 작업을 수행할 수 있습니다.

1. 포괄적인 보안 정책 개발 및 게시. 정책은 소프트웨어, 클라우드 자산 사용과 관련해서 허용되는 사용에 대한 규칙을 명시합니다. 또한 정책을 명확히 위반하는 행동도 정의합니다. 
2. 사용자 지정 이미지, 사용자 지정 아티팩트를 개발하고, Active Directory를 사용하여 정의한 보안 영역 내의 오케스트레이션을 허용하는 배포 프로세스를 개발합니다. 이 방법은 회사 경계를 적용하고 공통된 환경 컨트롤 집합을 설정합니다. 개발자가 전체 프로세스의 일부로 보안 개발 수명 주기를 개발하고 따를 때 고려할 수 있는 환경에 대한 컨트롤입니다. 그 목표는 개발을 방해할 만큼 지나치게 제한적이지 않으면서 적절한 컨트롤 집합을 제공하는 것입니다. 랩 가상 머신을 포함하는 OU(조직 구성 단위)의 그룹 정책은 프로덕션 환경에 있는 전체 그룹 정책의 하위 집합일 수 있습니다. 또는 식별된 위험을 적절히 완화하기 위한 추가 집합일 수 있습니다.

## <a name="data-integrity"></a>데이터 무결성

### <a name="question"></a>질문
조직은 원격 개발자가 코드를 제거하거나 맬웨어 또는 승인되지 않은 소프트웨어를 도입할 수 없도록 하기 위해 어떤 방식으로 데이터 무결성을 보장할 수 있나요?

### <a name="answer"></a>응답
외부 컨설턴트, 계약자 또는 DevTest Labs에서 공동으로 작업하는 원격지 직원으로 인한 위협을 완화하기 위해 여러 계층의 컨트롤이 사용됩니다. 

앞서 설명한 것처럼, 첫 번째 단계는 누군가가 정책을 위반할 때 그 결과를 명확히 설명하는 허용되는 사용 정책을 작성하고 정의하는 것입니다. 

원격 액세스에 대한 첫 번째 컨트롤 계층은 랩에 직접 연결되지 않은 VPN 연결을 통해 원격 액세스 정책을 적용하는 것입니다. 

두 번째 컨트롤 계층은 원격 데스크톱을 통한 복사 및 붙여넣기를 금지하는 그룹 정책 개체 집합을 적용하는 것입니다. 환경 외부의 RDP 서비스 및 환경의 아웃바운드 서비스(예: FTP)를 허용하지 않도록 네트워크 정책을 구현할 수 있습니다. 사용자 정의 라우팅은 모든 Azure 네트워크 트래픽을 강제로 온-프레미스로 다시 보낼 수 있지만, 콘텐츠 및 세션을 검색하기 위한 프록시를 통해 제어되지 않는다면, 이러한 라우팅에서 데이터의 업로드를 허용할 수 있는 모든 URL이 고려되지 못할 수도 있습니다. 공용 IP는 외부 네트워크 리소스의 브리징를 허용하지 않도록 DevTest Labs를 지원하는 가상 네트워크 내에서 제한될 수 있습니다.

궁극적으로 이동식 미디어 또는 콘텐츠 게시를 허용할 수 있는 외부 URL을 비롯한 가능한 모든 방법을 고려해야 하는 동일한 유형의 제한을 조직 전체에 적용해야 합니다. 보안 전문가와 함께 보안 정책을 검토하고 구현하세요. 자세한 권장 사항에 대해서는 [Microsoft 사이버 보안](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)을 참조하세요.


## <a name="next-steps"></a>다음 단계
[애플리케이션 마이그레이션 및 통합](devtest-lab-guidance-governance-application-migration-integration.md)을 참조하세요.
