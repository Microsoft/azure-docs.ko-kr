---
title: 엔터프라이즈에 대 한 Azure DevTest Labs에 대 한 참조 아키텍처
description: 이 문서에서는 기업에서 Azure DevTest Labs에 대 한 참조 아키텍처 지침을 제공합니다.
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
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561530"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Azure DevTest Labs-엔터프라이즈에 대 한 참조 아키텍처
이 문서에서는 기업에서 Azure DevTest Labs를 기반으로 솔루션을 배포 하기 위한 참조 아키텍처를 제공 합니다. 가상 컴퓨터를 원격으로 로그인 하려면 원격 데스크톱 게이트웨이, 개인 아티팩트 및 환경에서 사용 되는 다른 PaaS 서비스에 대 한 아티팩트 리포지토리를 연결할 Express 경로 통해 온-프레미스 연결을 포함 합니다.

![참조 아키텍처](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>아키텍처
참조 아키텍처의 주요 요소는 다음과 같습니다.

- **Azure Active Directory (AAD)**: Azure DevTest Labs에서 사용 하 여 [id 관리를 위한 Azure Active Directory 서비스](../active-directory/fundamentals/active-directory-whatis.md)합니다. 두 가지 키를 사용자에 게 DevTest Labs에 따라 환경에 대 한 액세스를 제공 하는 것이 좋습니다.
    - **리소스 관리**:  리소스를 관리 하려면 Azure portal에 대 한 액세스 제공 (가상 머신 만들기, 환경, 시작/중지/다시 시작/삭제/applyartifacts, 및 등을 만들기). Roble 기반 액세스 제어 (RBAC)를 사용 하 여 Azure에 사용자, 설정 리소스 및 액세스 수준 권한에 대 한 역할 할당을 적용 하 여 수행 됩니다.
    - **가상 컴퓨터 (네트워크 수준)**:  기본 구성으로 가상 컴퓨터 로컬 관리자 계정을 사용 합니다.  도메인을 사용할 경우 ([AAD Domain services](../active-directory-domain-services/active-directory-ds-overview.md)는 온-프레미스 도메인 또는 클라우드 기반 도메인), 컴퓨터를 도메인에 조인할 수 있습니다. 조인 된 후 사용자가 자신의 도메인 기반 id를 Vm에 연결할 때 사용 합니다.
- **온-프레미스 연결**: 위의 아키텍처 다이어그램 [Express 경로](../expressroute/expressroute-introduction.md) i 사용 되지만 사용할 수도 있습니다는 [사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). DevTest Labs에 대 한 필요 하지는 않지만 기업에서 일반적으로 표시 됩니다. 회사 리소스에 연결 하는 이유 인지만 필요 했습니다. 일반적인 원인은 다음과 같습니다. 
    - 아직 클라우드로 이동할 수 없는 온-프레미스 데이터
    - 랩의 가상 컴퓨터를 온-프레미스 도메인에 연결 하려면 기본 설정
    - 보안/규정 준수 상의 이유로 온-프레미스 방화벽을 통해 클라우드 환경에서 들어오고 나가는 모든 네트워크 트래픽을 강제 적용
- **네트워크 보안 그룹**: 클라우드 환경 (또는 클라우드 환경 내에서) 트래픽을 제한 하는 일반적인 방법은 소스를 기반으로 하며 대상 IP 주소를 사용 하는 [네트워크 보안 그룹](../virtual-network/security-overview.md)합니다. 예를 들어, 랩의 네트워크에 회사 네트워크에서 시작 된 네트워크 트래픽만을 허용 합니다.
- **원격 데스크톱 게이트웨이**:  일반적으로 기업에서는 회사 방화벽에서 나가는 원격 데스크톱 연결을 차단합니다. DevTest Labs에서 클라우드 기반 환경에 대 한 연결을 사용 하도록 설정 하는 사용과 같은 몇 가지 옵션을 [원격 데스크톱 게이트웨이](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (게이트웨이 부하 분산 장치의 고정 IP 화이트 리스트) 또는 [들어오는 모든 연결 RDP 트래픽을](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) Express 경로/사이트 및 사이트 간 VPN 연결을 통해. 기업에서 DevTest Labs의 배포를 계획할 때 일반적인 고려 사항입니다.
- **Azure 네트워킹 (Vnet, 서브넷)**:  합니다 [Azure 네트워킹](../networking/networking-overview.md) 토폴로지는 전체 DevTest Labs 아키텍처의 또 다른 핵심 요소입니다. 통신 (여부), 온-프레미스에 대 한 액세스 (또는 비공유) (또는 비공유) 인터넷에 액세스 하는 랩의 리소스를 수 있습니다. 아키텍처 다이어그램은 고객이 DevTest Labs를 사용 하는 가장 일반적인 방법은 포함 되어 있습니다 (통해 연결 된 모든 랩 [VNet 피어 링](../virtual-network/virtual-network-peering-overview.md) 를 사용 하는 [허브-스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Express 경로/사이트 및 사이트 간 VPN 연결에 온-프레미스)에 DevTest Labs 이후 하지만 사용 하 여 Azure 네트워킹 직접 네트워킹 인프라를 설정 하는 방법에 제한이 없습니다.
- **DevTest Labs**:  DevTest Labs는 전체 아키텍처의 핵심 부분입니다. 서비스를 알아보려면 [DevTest Labs에 대 한](devtest-lab-overview.md)합니다.
- **가상 머신과 다른 리소스 (SaaS, PaaS, IaaS)**:  DevTest Labs에서 지원 되는 주요 작업 중 하나는 다른 Azure 리소스와 함께 가상 머신입니다.  DevTest Labs를 사용 하면 쉽고 빠르게 Azure 리소스 (가상 머신 및 다른 Azure 리소스 포함)에 액세스할 수 있도록 엔터프라이즈에 대 한 합니다.  Azure에 대 한 액세스에 자세히 알아보려면 [개발자](devtest-lab-developer-lab.md) 하 고 [테스터](devtest-lab-test-env.md)합니다.

## <a name="scalability-considerations"></a>확장성 고려 사항
DevTest Labs가 없는 기본 제공 할당량 또는 제한이 있나요 일반적인 다른 Azure 리소스 사용 됩니다 랩의 작업 권한이 [구독 수준 할당량](../azure-subscription-service-limits.md)합니다. 결과적으로, 일반적인 엔터프라이즈 배포에서 DevTest Labs의 대규모 배포를 처리 하기 위해 여러 Azure 구독이 있어야 합니다. 기업에 가장 일반적으로 도달 하면 할당량은 다음과 같습니다.

- **리소스 그룹**:  기본 구성에서 DevTest Labs는 모든 새 가상 컴퓨터나 서비스를 사용 하는 사용자가 환경에 대 한 리소스 그룹을 만듭니다. 구독에 포함 될 수 있습니다는 [980 리소스 그룹의 최대](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)이므로이 제한은 상한 virtual machines 및 구독에는 환경입니다. 가지 고려해 야 하는 다른 두 구성이 있습니다.
    - **[모든 virtual machines는 동일한 리소스 그룹으로 이동](resource-group-control.md)**:  도움이 리소스 그룹 제한에 있지만 리소스 그룹 당 리소스 종류 영향을 줍니다.
    - **공용 Ip를 공유를 사용 하 여**:  같은 크기와 동일한 지역의 모든 Vm은 동일한 리소스 그룹으로 이동합니다. 가상 머신에 공용 IP 주소를 보유 하도록 허용 된 경우 리소스 그룹 할당량 및 리소스 그룹 할당량 당 리소스 종류 간에 ' 중간 지점을 '입니다. 
- **리소스 당 리소스 그룹 리소스 형식 당**: 에 대 한 기본 제한이 [리소스 그룹 리소스 당 리소스는 800](../azure-subscription-service-limits.md#resource-group-limits)합니다.  적중 될 때 모든 Vm을 사용 하 여 동일한 리소스 그룹 구성으로 이동, 사용자는이 구독 제한을 훨씬 더 빨리 Vm 추가 디스크가 많은 경우에 특히 합니다.
- **Storage 계정**: DevTest Labs에서 랩 저장소 계정 및 Azure의 할당량을 수반 [지역당 구독 당 저장소 계정 수는 250](../azure-subscription-service-limits.md#storage-limits)합니다. 즉, 동일한 지역에 DevTest Labs의 번호의 상한 값 250 이기도 합니다.
- **역할 할당**: 역할 할당은 리소스 (소유자, 리소스, 권한 수준)에 사용자 또는 보안 주체가 액세스할 수 있도록 하는 방법입니다. Azure에서 있습니다를 [구독 당 2000 역할 할당의 제한](../azure-subscription-service-limits.md#role-based-access-control-limits)합니다. DevTest Labs 서비스는 기본 구성에서 각 VM에 대 한 리소스 그룹을 만듭니다 및 소유자가 부여할 합니다 **소유자** DevTest Labs VM에 대 한 권한 및 **판독기** 할은 리소스 그룹입니다.  이러한 방식으로 만든 각 새 VM 역할 할당이 만들어져 사용자에 게 랩에는 권한을 제공 하는 경우에 두 개의 추가 역할 할당을 사용 합니다.
- **API 읽기/쓰기**: Azure DevTest Labs를 자동화 하는 (REST Api, PowerShell, CLI, Azure SDK 및 등)에 여러 가지가 있습니다 이며 자동화를 통해 API 요청에 다른 제한에 도달할 수입니다. 각 구독은 최대 허용 [12000 읽기 요청 및 1200 쓰기 시간당 요청](../azure-resource-manager/resource-manager-request-limits.md)합니다.  이 DevTest Labs 자동화 하는 경우 알아야 할 제한 됩니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항
DevTest Labs는 단일 랩 작업할 때 유용한 관리 사용자 인터페이스가 있습니다. 기업에서는 있을 가능성이 여러 Azure 구독 및 많은 실습 합니다. 변경 하는 모든 랩 일관 되 게 스크립팅 자동화 해야 한다는 의미입니다.  다음은 몇 가지 예제와 DevTest Labs 배포를 관리 하는 가장 좋은 방법입니다.

- **랩 설정의 변경 내용을**: 일반적인 시나리오 배포에 모든 랩을 통해 특정 랩 설정 업데이트 하는 것입니다. 예를 들어, 새 VM 인스턴스 크기를 사용할 수 및 모든 랩을 허용 하도록 업데이트 해야 합니다.  Azure PowerShell 스크립트, Azure CLI 또는 REST Api를 사용 하 여 이러한 변경 내용을 자동화 하는 것이 좋습니다.  
- **아티팩트 리포지토리에 개인용 액세스 토큰**:  일반적으로 Git 리포지토리에 대 한 개인용 액세스 토큰 만료 (90 일이 지난 1 년, 2 년). 연속성을 위해 개인용 액세스 토큰을 확장 하거나 새로 만듭니다 및 자동화를 사용 하 여 모든 랩에 새 개인 액세스 토큰을 적용할 것입니다.
- **랩 설정 변경 제한**:  이 경우가 제한 해야 하는 특정 설정 (예를 들어 허용 Marketplace 이미지를 사용)가 있습니다. 수행할 수 있습니다 (해당 리소스 형식에 변경 내용을 방지) 하는 Azure 정책을 통해 또는 사용자 지정 역할을 만들고 '소유자' 랩에는 대신 해당 역할을 부여 합니다. 랩 (내부 지원, 랩 공지, 허용 VM 크기 및 등)의 설정을 대부분 수행할 수 있습니다.
- **Vm 명명 규칙에 따라 필요한**: 쉽게 클라우드 기반 개발 및 테스트 환경에 포함 된 Vm을 식별 하는 일반적인 요청 것입니다. 그렇게 할 수 있습니다 [Azure Policy를 사용 하 여](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)입니다.

DevTest Labs는 Azure에서 동일한 방식으로 관리 되는 Azure (네트워킹, 디스크, 계산, 등)에서 기본 리소스를 사용 하는 것이 반드시 합니다.  예를 들어 Azure policy는 랩에서 만든 Virtual Machines에 적용 됩니다. Azure Security center는 VM 규정 준수에 보고할 수 있습니다. Azure Backup 서비스는 랩에서 Vm에 대 한 정기 백업을 제공 하 고 등 수 있습니다. 

## <a name="security-considerations"></a>보안 고려 사항
Azure DevTest Labs (계산, 네트워킹 및 등) Azure에서 기존 리소스를 사용 하 및 따라서 자동으로 혜택 기본 제공 매우 유용한 보안 기능에서을 플랫폼입니다. 예를 들어, 회사 네트워크에서 시작만 모든 들어오는 원격 데스크톱 연결을 보호 하려면 간단 원격 데스크톱 게이트웨이에서 가상 네트워크에 네트워크 보안 그룹을 추가 하는 것입니다. Azure DevTest Labs에 대 한 추가 보안 고려 사항에는 사용 하는 실습 매일 기준 팀 멤버에 게 제공 하는 사용 권한 수준입니다.  지정 된 일반적인 사용 권한이 ["소유자" 및 "DevTest Labs User"](devtest-lab-add-devtest-user.md)합니다. 이러한 역할에 대 한 자세한 내용은 참조 하세요. [Azure DevTest Labs에 소유자 및 사용자 추가](devtest-lab-add-devtest-user.md)합니다.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조하세요. [Azure DevTest Labs 인프라 확장](devtest-lab-guidance-scale.md)
