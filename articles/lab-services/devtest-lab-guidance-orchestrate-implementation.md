---
title: Azure DevTest Labs의 구현 오케스트레이션
description: 이 문서에서는 조직의 Azure DevTest Labs 구현을 오케스트레이션하기 위한 지침을 제공합니다.
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
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127347"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Azure DevTest Labs의 구현 오케스트레이션
이 문서에서는 Azure DevTest Labs의 빠른 배포와 구현을 위한 권장 방식을 제공합니다. 아래 그림에는 다양한 업계 요구 사항과 시나리오 지원을 위한 유연성을 확인하면서 진행할 수 있는 전체 프로세스가 규범 지침 형식으로 강조 표시되어 있습니다.

![Azure DevTest Labs 구현을 위한 단계](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>가정
이 문서에서는 DevTest Labs 파일럿을 구현하기 전에 다음 항목을 준비했다고 가정합니다.

- **Azure 구독**: 파일럿 팀에는 Azure 구독에 리소스를 배포할 액세스 권한이 있어야 합니다. 개발 및 테스트 워크로드만 구현에 포함되는 경우에는 Windows 가상 머신의 요금이 더 저렴하며 사용 가능한 이미지는 추가로 제공되는 Enterprise DevTest 제품을 선택하는 것이 좋습니다.
- **온-프레미스 액세스**: 필요한 경우 온-프레미스 액세스를 이미 구성한 상태여야 합니다. 사이트 간 VPN 연결 또는 Express 경로를 통해 온-프레미스에 액세스할 수 있습니다. Express 경로를 통한 연결은 대개 설정하는 데 몇 주가 걸릴 수 있으므로 프로젝트를 시작하기 전에 Express 경로를 준비해 두는 것이 좋습니다.
- **파일럿 팀**: DevTest Labs를 사용하는 초기 개발 프로젝트 팀과 함께 해당 개발 또는 테스트 활동을 확인했으며, 이러한 팀의 요구 사항/목표/목적을 설정해야 합니다.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>중요 시점 1: 초기 네트워크 토폴로지 및 디자인 설정
Azure DevTest Labs 솔루션 배포 시 처음으로 확인해야 하는 주요 분야는 가상 머신에 대해 계획된 연결을 설정하는 것입니다. 아래 단계에서는 필요한 절차를 대략적으로 설명합니다.

1. Azure에서 DevTest Labs 구독에 할당되는 **IP 초기 IP 주소 범위**를 정의합니다. 이 단계에서는 이후 확장에 대비해 충분히 큰 블록을 제공할 수 있도록 VM 수의 예상 사용량을 예측해야 합니다.
2. DevTest Labs에 대해 **원하는 액세스 방법**을 확인합니다(예: 외부/내부 액세스). 이 단계에서는 가상 머신에 공용 IP 주소를 사용할지(인터넷에서 해당 가상 머신에 직접 액세스할 수 있는지) 여부를 결정해야 합니다.
3. 나머지 Azure 클라우드 환경 및 온-프레미스와의 **연결 방법**을 확인하고 설정합니다. Express 경로를 통한 강제 라우팅을 사용하는 경우 가상 머신에는 회사 방화벽을 통과할 수 있는 적절한 프록시 구성이 필요할 가능성이 많습니다.
4. VM을 **도메인에 가입**시키려는 경우에는 클라우드 기반 도메인(예: AAD Directory Services)에 가입할지 아니면 온-프레미스 도메인에 가입할지를 결정합니다. 온-프레미스의 경우 가상 머신이 가입하는 Active Directory 내 OU(조직 구성 단위)를 결정합니다. 또한 사용자에게 VM이 가입된 도메인 액세스 권한이 있는지를 확인하거나 도메인에서 컴퓨터 레코드를 만들 수 있는 서비스 계정을 설정합니다.

## <a name="milestone-2-deploy-the-pilot-lab"></a>중요 시점 2: 파일럿 랩 배포
네트워크 토폴로지를 구축한 후에는 다음 단계를 수행하여 첫 번째/파일럿 랩을 만들 수 있습니다.

1. 초기 DevTest Labs 환경을 만듭니다. 단계별 지침은 [여기](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md)서 확인할 수 있습니다.
2. 랩에서 사용할 허용 가능한 VM 이미지와 크기를 결정합니다. DevTest Labs에 사용할 사용자 지정 이미지를 Azure에 업로드할 수 있는지 여부를 결정합니다.
3. 랩용 초기 RBAC(역할 기반 액세스 제어)를 작성하여 랩 액세스를 보호합니다(랩 소유자 및 랩 사용자). DevTest Labs의 ID로는 Azure Active Directory와 동기화되는 Active Directory 계정을 사용하것는 이 좋습니다.
4. 일정, 비용 관리, 클레임할 수 있는 VM, 사용자 지정 이미지 또는 수식과 같은 정책을 사용하도록 DevTest Labs를 구성합니다.
5. Azure Repos/Git 등의 온라인 리포지토리를 설정합니다.
6. 사용할 리포지토리(공용 리포지토리, 개인 리포지토리 또는 두 리포지토리의 조합)를 결정합니다. 배포 및 장기 유지용으로 JSON 템플릿을 구성합니다.
7. 필요한 경우 사용자 지정 아티팩트를 만듭니다. 이 단계는 선택 사항입니다. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>중요 시점 3: 설명서, 지원, 학습, 개선
초기 파일럿 팀이 구현을 시작하려면 심층 지원이 필요할 수도 있습니다. 초기 팀의 경험을 토대로 하여 Azure DevTest Labs를 지속적으로 출시하는 데 적합한 설명서와 지원 방법을 준비합니다.

1. 파일럿 팀에게 신규 DevTest Labs 리소스(데모, 설명서)를 소개합니다.
2. 파일럿 팀의 경험을 토대로 하여 필요한 설명서를 계획하고 제공합니다.
3. 공식적인 신규 팀 온보딩 프로세스를 결정합니다(랩 작성/구성, 액세스 권한 제공 등).
4. 초기 활용도에 따라 원래 예측했던 IP 주소 공간이 현재도 적절하며 정확한지를 확인합니다.
5. 적절한 규정 준수 및 보안 검토가 완료되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조하세요. [Azure DevTest Labs 인프라의 거버넌스](devtest-lab-guidance-governance-resources.md)
