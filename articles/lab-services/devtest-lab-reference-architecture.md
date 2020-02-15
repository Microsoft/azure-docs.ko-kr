---
title: Azure DevTest Labs에 대 한 엔터프라이즈 참조 아키텍처
description: 이 문서에서는 엔터프라이즈의 Azure DevTest Labs에 대 한 참조 아키텍처 지침을 제공 합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132847"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>엔터프라이즈를 위한 Azure DevTest Labs 참조 아키텍처
이 문서에서는 기업의 Azure DevTest Labs 기반 솔루션을 배포 하는 데 도움이 되는 참조 아키텍처를 제공 합니다. 여기에는 다음이 포함 됩니다.
- Azure Express 경로를 통한 온-프레미스 연결
- 가상 컴퓨터에 원격으로 로그인 하는 원격 데스크톱 게이트웨이
- 개인 아티팩트에 대 한 아티팩트 리포지토리에 연결
- 랩에서 사용 되는 기타 PaaS 서비스

![참조 아키텍처 다이어그램](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>아키텍처
참조 아키텍처의 핵심 요소는 다음과 같습니다.

- **Azure Active Directory (AZURE ad)** : DevTest Labs는 [Id 관리에 Azure AD 서비스](../active-directory/fundamentals/active-directory-whatis.md)를 사용 합니다. DevTest Labs를 기반으로 환경에 대 한 액세스 권한을 사용자에 게 부여 하는 경우 다음 두 가지 주요 측면을 고려 하세요.
    - **리소스 관리**: 리소스를 관리 하는 Azure Portal에 대 한 액세스를 제공 합니다 (가상 머신 만들기, 환경 만들기, 시작, 중지, 다시 시작, 삭제 및 아티팩트 적용 등). 리소스 관리는 RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure에서 수행 됩니다. 사용자에 게 역할을 할당 하 고 리소스 및 액세스 수준 사용 권한을 설정 합니다.
    - **가상 컴퓨터 (네트워크 수준)** : 기본 구성에서 가상 컴퓨터는 로컬 관리자 계정을 사용 합니다. 사용 가능한 도메인 ([Azure AD Domain Services](../active-directory-domain-services/overview.md), 온-프레미스 도메인 또는 클라우드 기반 도메인)이 있으면 컴퓨터를 도메인에 조인할 수 있습니다. 그러면 사용자가 해당 도메인 기반 id를 사용 하 여 Vm에 연결할 수 있습니다.
- **온-프레미스 연결**: 아키텍처 다이어그램에서 [express](../expressroute/expressroute-introduction.md) 경로를 사용 합니다. 하지만 [사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)을 사용할 수도 있습니다. DevTest Labs에서는 Express 경로를 사용 하지 않아도 되지만 일반적으로 기업에서 사용 됩니다. Express 경로는 회사 리소스에 액세스 해야 하는 경우에만 필요 합니다. 일반적인 시나리오는 다음과 같습니다.
    - 클라우드로 이동할 수 없는 온-프레미스 데이터가 있습니다.
    - 랩의 가상 컴퓨터를 온-프레미스 도메인에 가입 하는 것이 좋습니다.
    - 보안/규정 준수를 위해 온-프레미스 방화벽을 통해 클라우드 환경에서 들어오고 나가는 모든 네트워크 트래픽을 강제로 적용 하려고 합니다.
- **네트워크 보안 그룹**: 원본 및 대상 IP 주소를 기반으로 클라우드 환경 (또는 클라우드 환경 내)으로 트래픽을 제한 하는 일반적인 방법은 [네트워크 보안 그룹](../virtual-network/security-overview.md)을 사용 하는 것입니다. 예를 들어 회사 네트워크에서 발생 하는 트래픽만 랩의 네트워크로 사용할 수 있습니다.
- **원격 데스크톱 게이트웨이**: 기업은 일반적으로 회사 방화벽에서 나가는 원격 데스크톱 연결을 차단 합니다. DevTest Labs에서 클라우드 기반 환경에 연결 하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 예를 들면 다음과 같습니다.
  - [원격 데스크톱 게이트웨이](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)를 사용 하 고 게이트웨이 부하 분산 장치의 고정 IP 주소를 허용 합니다.
  - Express 경로/사이트 간 VPN 연결을 통해 [들어오는 모든 RDP 트래픽을 보냅니다](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) . 이 기능은 기업이 DevTest Labs 배포를 계획할 때 일반적인 고려 사항입니다.
- **네트워크 서비스 (가상 네트워크, 서브넷)** : [Azure 네트워킹](../networking/networking-overview.md) 토폴로지는 DevTest Labs 아키텍처의 또 다른 주요 요소입니다. 랩의 리소스가 통신 하 고 온-프레미스 및 인터넷에 액세스할 수 있는지 여부를 제어 합니다. 아키텍처 다이어그램에는 고객이 DevTest Labs를 사용 하는 가장 일반적인 방법, 즉 [허브-스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 을 사용 하 여 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md) 을 통해 온-프레미스에 대 한 express 경로/사이트 간 VPN 연결로 연결 하는 모든 랩이 포함 되어 있습니다. 그러나 DevTest Labs는 Azure Virtual Network를 직접 사용 하므로 네트워킹 인프라를 설정 하는 방법에 대 한 제한이 없습니다.
- **DevTest labs**: DevTest labs는 전반적인 아키텍처의 핵심 부분입니다. 서비스에 대 한 자세한 정보는 [DevTest Labs 정보](devtest-lab-overview.md)를 참조 하세요.
- **가상 컴퓨터 및 기타 리소스 (SaaS, PaaS, IaaS)** : virtual Machines는 DevTest Labs에서 다른 Azure 리소스와 함께 지 원하는 주요 작업입니다. DevTest Labs를 사용 하면 엔터프라이즈에서 Vm 및 기타 Azure 리소스를 비롯 한 Azure 리소스에 쉽게 액세스할 수 있습니다. [개발자](devtest-lab-developer-lab.md) 와 [테스터](devtest-lab-test-env.md)를 위한 Azure 액세스에 대해 자세히 알아보세요.

## <a name="scalability-considerations"></a>확장성 고려 사항
DevTest Labs에는 기본 제공 할당량 또는 한도가 없지만 랩의 일반 작업에 사용 되는 다른 Azure 리소스에는 [구독 수준 할당량이](../azure-resource-manager/management/azure-subscription-service-limits.md)있습니다. 따라서 일반적인 엔터프라이즈 배포에서는 DevTest Labs의 대량 배포를 처리 하기 위해 여러 Azure 구독이 필요 합니다. 기업에서 가장 일반적으로 연결 하는 할당량은 다음과 같습니다.

- **리소스 그룹**: 기본 구성에서 DevTest Labs는 모든 새 가상 머신에 대해 리소스 그룹을 만들거나 사용자가 서비스를 사용 하 여 환경을 만듭니다. 구독은 [최대 980 개의 리소스 그룹을](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)포함할 수 있습니다. 따라서이는 구독의 가상 컴퓨터 및 환경에 대 한 제한입니다. 고려해 야 할 두 가지 다른 구성이 있습니다.
    - **[모든 가상 컴퓨터는 동일한 리소스 그룹으로 이동 합니다](resource-group-control.md)** .이 설정은 리소스 그룹 제한을 충족 하는 데 도움이 되지만 리소스 유형 별 리소스 그룹 제한에 영향을 줍니다.
    - **공유 공용 Ip 사용**: 동일한 크기와 지역의 모든 vm이 동일한 리소스 그룹으로 이동 합니다. 이 구성은 가상 컴퓨터에 공용 IP 주소를 사용할 수 있는 경우 리소스 그룹 할당량 및 리소스 유형 그룹 할당량 마다 "중간 중심"입니다.
- **리소스 그룹당**리소스 그룹당 리소스: 리소스 유형별 리소스 그룹당 기본 제한은 [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)입니다.  모든 Vm을 사용 하 여 *동일한 리소스 그룹 구성으로 이동* 하는 경우, 특히 vm에 추가 디스크가 많은 경우이 구독 제한을 훨씬 더 빨리 맞 더 합니다.
- **Storage 계정**: DevTest Labs의 랩에는 저장소 계정이 제공 됩니다. [구독 당 지역별 저장소 계정 수](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)에 대 한 Azure 할당량은 250입니다. 동일한 지역에 있는 DevTest Labs의 최대 수는 250입니다.
- **역할 할당**: 역할 할당은 사용자 또는 사용자에 게 리소스에 대 한 액세스 권한 (소유자, 리소스, 권한 수준)을 부여 하는 방법입니다. Azure의 [구독 당 역할 할당은 2000 개로 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits)됩니다. 기본적으로 DevTest Labs 서비스는 각 VM에 대 한 리소스 그룹을 만듭니다. 소유자에 게는 DevTest Labs VM에 대 한 *소유자* 권한 및 리소스 그룹에 대 한 *읽기* 권한이 부여 됩니다. 이러한 방식으로 사용자가 만드는 각 새 VM은 사용자에 게 랩에 대 한 사용 권한을 부여할 때 사용 되는 할당 외에도 두 개의 역할 할당을 사용 합니다.
- **API 읽기/쓰기**: REST Api, PowerShell, Azure CLI 및 azure SDK를 비롯 한 다양 한 방법으로 Azure 및 DevTest Labs를 자동화할 수 있습니다. Automation을 통해 API 요청에 대 한 다른 제한에 도달할 수 있습니다. 각 구독은 최대 [12000 읽기 요청 및 시간당 1200 쓰기 요청](../azure-resource-manager/management/request-limits-and-throttling.md)을 허용 합니다. DevTest Labs를 자동화 하는 경우이 한도를 염두에 두어야 합니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항
DevTest Labs에는 단일 랩으로 작업 하기 위한 뛰어난 관리 사용자 인터페이스가 있습니다. 그러나 기업에서는 여러 Azure 구독 및 많은 랩을 보유 하 고 있을 것입니다. 모든 랩에 일관성 있게 변경 하려면 스크립팅/자동화가 필요 합니다. DevTest Labs 배포에 대 한 몇 가지 예제 및 최선의 관리 사례는 다음과 같습니다.

- **랩 설정 변경**: 일반적인 시나리오는 배포의 모든 랩에서 특정 랩 설정을 업데이트 하는 것입니다. 예를 들어 새 VM 인스턴스 크기를 사용할 수 있으며이를 허용 하도록 모든 랩을 업데이트 해야 합니다. PowerShell 스크립트, CLI 또는 REST Api를 사용 하 여 이러한 변경 내용을 자동화 하는 것이 가장 좋습니다.  
- **아티팩트 리포지토리 개인용 액세스 토큰**: 일반적으로 Git 리포지토리의 개인용 액세스 토큰은 90 일, 1 년 또는 2 년 후에 만료 됩니다. 연속성을 보장 하려면 개인용 액세스 토큰을 확장 하거나 새로 만드는 것이 중요 합니다. 그런 다음 자동화를 사용 하 여 모든 랩에 새 개인용 액세스 토큰을 적용 합니다.
- **랩 설정에 대 한 변경 제한**: 종종 특정 설정 (예: 마켓플레이스 이미지 사용 허용)을 제한 해야 합니다. Azure Policy를 사용 하 여 리소스 종류에 대 한 변경을 방지할 수 있습니다. 또는 사용자 지정 역할을 만들고 랩의 *소유자* 역할 대신 해당 역할을 사용자에 게 부여할 수 있습니다. 랩의 대부분 설정 (내부 지원, 랩 알림, 허용 된 VM 크기 등)에 대해이 작업을 수행할 수 있습니다.
- **Vm이 명명 규칙을 따르도록 요구**: 일반적으로 관리자는 클라우드 기반 개발 및 테스트 환경에 포함 된 vm을 쉽게 식별 합니다. [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)를 사용 하 여이 작업을 수행할 수 있습니다.

DevTest Labs는 동일한 방식으로 관리 되는 기본 Azure 리소스 (네트워킹, 디스크, 계산 등)를 사용 합니다. 예를 들어 Azure Policy은 랩에서 만든 가상 컴퓨터에 적용 됩니다. VM 준수에 대해 보고할 수 Azure Security Center. 그리고 Azure Backup 서비스는 랩에서 Vm에 대 한 정기 백업을 제공할 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항
Azure DevTest Labs는 Azure에서 기존 리소스 (계산, 네트워킹 등)를 사용 합니다. 따라서 플랫폼에 기본 제공 되는 보안 기능을 자동으로 활용 합니다. 예를 들어 들어오는 원격 데스크톱 연결이 회사 네트워크 에서만 시작 되도록 하려면 원격 데스크톱 게이트웨이의 가상 네트워크에 네트워크 보안 그룹을 추가 하면 됩니다. 추가 보안 고려 사항은 일상적인 실습을 사용 하는 팀 멤버에 게 부여 하는 권한 수준입니다. 가장 일반적인 권한은 [ *소유자* 및 *사용자*](devtest-lab-add-devtest-user.md)입니다. 이러한 역할에 대 한 자세한 내용은 [Azure DevTest Labs에 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조 하세요. [Azure DevTest Labs 인프라](devtest-lab-guidance-scale.md)강화
