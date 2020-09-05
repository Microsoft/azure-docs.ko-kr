---
title: Azure Firewall Manager란?
description: Azure Firewall Manager 기능에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 08/25/2020
ms.author: victorh
ms.openlocfilehash: ae220a1b70be7178c4c2fea01103991c8729ae79
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855042"
---
# <a name="what-is-azure-firewall-manager"></a>Azure Firewall Manager란?

Azure Firewall Manager는 클라우드 기반 보안 경계에 대한 중앙 보안 정책 및 경로 관리를 제공하는 보안 관리 서비스입니다. 

Firewall Manager는 다음 두 가지 네트워크 아키텍처 유형에 대한 보안 관리를 제공할 수 있습니다.

- **보안 가상 허브**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources)는 허브 및 스포크 아키텍처를 쉽게 만들 수 있는 Microsoft 관리 리소스입니다. 보안 및 라우팅 정책이 이러한 허브와 연결된 경우에는 *[보안 가상 허브](secured-virtual-hub.md)* 라고도 합니다. 
- **Hub 가상 네트워크**

   이는 사용자가 직접 만들고 관리하는 표준 Azure 가상 네트워크입니다. 이러한 허브와 연결된 보안 정책을 *보안 가상 허브*라고도 합니다. 지금은 Azure Firewall Policy만 지원됩니다. 워크로드 서버 및 서비스가 포함된 스포크 가상 네트워크를 피어링할 수 있습니다. 어떤 스포크에도 피어링되지 않는 독립 실행형 가상 네트워크에서 방화벽을 관리할 수도 있습니다.

*보안 가상 허브* 및 *허브 가상 네트워크* 아키텍처에 대한 자세한 비교는 [Azure Firewall Manager 아키텍처 옵션이란?](vhubs-and-vnets.md)을 참조하세요.

![방화벽-관리자](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Azure Firewall Manager 기능

Azure Firewall Manager는 다음과 같은 기능을 제공합니다.

### <a name="central-azure-firewall-deployment-and-configuration"></a>중앙 Azure Firewall 배포 및 구성

여러 다른 Azure 지역 및 구독에 걸쳐 있는 여러 Azure Firewall 인스턴스를 중앙에서 배포하고 구성할 수 있습니다. 

### <a name="hierarchical-policies-global-and-local"></a>계층 구조 정책(글로벌 및 로컬)

Azure Firewall Manager를 사용하여 여러 보안 가상 허브에서 Azure Firewall 정책을 중앙에서 관리할 수 있습니다. 중앙의 IT 팀은 글로벌 방화벽 정책을 작성하여 팀 간에 조직 전체 방화벽 정책을 적용할 수 있습니다. 로컬로 작성된 방화벽 정책을 사용하면 DevOps 셀프 서비스 모델을 사용하여 민첩성을 강화할 수 있습니다.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>고급 보안을 위해 타사 Security-as-a-Service와 통합

Azure Firewall 외에도 타사의 SECaaS(Security as a Service) 공급자를 통합하여 VNet 및 분기 인터넷 연결에 대한 추가 네트워크 보호를 제공할 수 있습니다.

이 기능은 보안 가상 허브 배포에만 사용할 수 있습니다.

- V2I(VNet과 인터넷 간) 트래픽 필터링

   - 선호하는 타사 보안 공급자를 사용하여 아웃바운드 가상 네트워크 트래픽을 필터링합니다.
   - Azure에서 실행되는 클라우드 워크로드에 고급 사용자 인식 인터넷 보호를 활용합니다.

- B2I(분기와 인터넷 간) 트래픽 필터링

   Azure 연결 및 글로벌 배포를 활용하여 분기와 인터넷 간 시나리오에 대해 타사 필터링을 쉽게 추가할 수 있습니다.

보안 파트너 공급자에 대한 자세한 내용은 [Azure Firewall Manager 보안 파트너 공급자란?](trusted-security-partners.md)을 참조하세요.

### <a name="centralized-route-management"></a>중앙 경로 관리

스포크 가상 네트워크에서 UDR(사용자 정의 경로)을 수동으로 설정하지 않고도 필터링 및 로깅을 위해 보안 허브로 트래픽을 쉽게 라우팅할 수 있습니다. 

이 기능은 보안 가상 허브 배포에만 사용할 수 있습니다.

B2I(분기와 인터넷 간) 트래픽 필터링을 위한 타사 공급자를 B2V(분기와 VNet 간), V2V(VNet 간) 및 V2I(VNet과 인터넷 간)를 위한 Azure Firewall과 함께 사용할 수 있습니다. B2V 또는 V2V에 Azure Firewall이 필요하지 않은 한 V2I 트래픽 필터링을 위해 타사 공급자를 사용할 수도 있습니다. 

## <a name="region-availability"></a>지역 가용성

Azure Firewall 정책은 여러 지역에 걸쳐 사용할 수 있습니다. 예를 들어, 미국 서부에서 만든 정책을 미국 동부에서 사용할 수 있습니다. 

## <a name="known-issues"></a>알려진 문제

Azure Firewall Manager에는 다음과 같이 알려진 문제가 있습니다.

|문제  |설명  |완화 방법  |
|---------|---------|---------|
|트래픽 분할|Office 365 및 Azure Public PaaS 트래픽 분할은 현재 지원되지 않습니다. 따라서 V2I 또는 B2I에 대해 타사 공급자를 선택하면 모든 Azure 퍼블릭 PaaS 및 Office 365 트래픽도 파트너 서비스를 통해 전송됩니다.|허브에서 트래픽 분할을 조사 중입니다.
|지역당 하나의 보안 가상 허브|지역당 둘 이상의 보안 가상 허브를 가질 수 없습니다.|한 지역에 여러 가상 WAN을 만듭니다.|
|기본 정책은 로컬 정책과 동일한 지역에 있어야 합니다.|기본 정책과 동일한 지역에 모든 로컬 정책을 만듭니다. 보안 허브의 한 지역에 생성된 정책을 다른 지역에서 계속 적용할 수 있습니다.|조사|
|보안 가상 허브 배포에서 허브 간 트래픽 필터링|보안 Virtual Hub 간 통신 필터링은 아직 지원되지 않습니다. 그러나 Azure Firewall을 통한 프라이빗 트래픽 필터링을 사용하지 않는 경우 허브 간 통신은 계속 작동합니다.|조사|
|가상 허브와 다른 지역의 스포크|가상 허브와 다른 지역의 스포크는 지원되지 않습니다.|조사<br><br>지역별 허브를 만들고 허브와 동일한 지역에 피어 Vnet을 만듭니다.|
|프라이빗 트래픽 필터링이 활성화된 분기 간 트래픽|프라이빗 트래픽 필터링이 활성화된 경우 분기 간 트래픽이 지원되지 않습니다. |조사 중입니다.<br><br>분기 간 연결이 중요한 경우 프라이빗 트래픽을 보호하지 마세요.|
|동일한 가상 WAN을 공유하는 모든 보안 Virtual Hubs는 동일한 리소스 그룹에 있어야 합니다.|이 동작은 현재 Virtual WAN Hubs에 맞춰 조정됩니다.|여러 Virtual WAN을 만들어 다른 리소스 그룹에서 보안 Virtual Hubs를 만들 수 있도록 합니다.|
|대량 IP 주소 추가 실패|여러 공용 IP 주소를 추가하면 보안 허브 방화벽이 실패한 상태가 됩니다.|더 작은 공용 IP 주소 증분을 추가합니다. 예를 들어 한 번에 10개를 추가합니다.|
|사용자 지정 DNS(미리 보기)가 구성된 보안 허브에서 애플리케이션 규칙이 실패합니다.|사용자 지정 DNS(미리 보기)는 강제 터널링이 설정된 보안 허브 배포 및 Hub 가상 네트워크 배포에서 작동하지 않습니다.|조사 중에 수정합니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Firewall Manager 배포 개요](deployment-overview.md) 검토
- [보안 가상 허브](secured-virtual-hub.md)에 대해 알아보기
