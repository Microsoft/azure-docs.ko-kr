---
title: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너 (미리 보기) 란?
description: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너에 대한 자세한 정보
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436783"
---
# <a name="what-are-trusted-security-partners-preview"></a>신뢰할 수 있는 보안 파트너(미리 보기)란?

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 방화벽 관리자의 신뢰할 수 있는 *보안 파트너 (미리 보기)* 를 사용 하면 친숙 하 고 가장 적합 한 타사 secaas security as a service) 제품을 사용 하 여 사용자에 대한 인터넷 액세스를 보호할 수 있습니다.

빠른 구성에서는 지원 되는 보안 파트너를 사용 하 여 허브를 보호 하 고, 가상 네트워크 (Vnet) 또는 지역 내의 분기 위치에서 인터넷 트래픽을 라우팅하고 필터링 할 수 있습니다. 이 작업은 UDRs (사용자 정의 경로)를 설정 및 관리 하지 않고도 자동 경로 관리를 사용 하 여 수행 됩니다.

여러 Azure 지역에서 선택한 보안 파트너를 사용 하 여 구성 된 보안 허브를 배포 하 여 해당 지역의 전 세계 어디에서 나 사용자에 대한 연결 및 보안을 얻을 수 있습니다. 인터넷/SaaS 응용 프로그램 트래픽에 대한 보안 파트너의 제품 및 보안 허브의 개인 트래픽에 대한 Azure 방화벽을 사용 하는 기능을 사용 하 여 전역으로 분산 된 사용자 및 응용 프로그램에 가까운 Azure에서 보안에 지를 구축할 수 있습니다.

이 미리 보기의 경우 지원 되는 보안 파트너는 **ZScaler** 및 **iboss**입니다. 지원 되는 지역은 WestCentralUS, NorthCentralUS, WestUS, WestUS2 및 EastUS입니다.

![신뢰할 수 있는 보안 파트너](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>주요 시나리오

보안 파트너를 사용 하 여 다음과 같은 시나리오에서 인터넷 트래픽을 필터링 할 수 있습니다.

- 인터넷에 대한 Virtual Network (VNet)

   Azure에서 실행되는 클라우드 워크로드에 고급 사용자 인식 인터넷 보호를 활용합니다.

- 인터넷으로 분기

   Azure 연결 및 글로벌 배포를 활용 하 여 인터넷 시나리오에 분기에 대한 타사 NSaaS 필터링을 쉽게 추가할 수 있습니다. Azure 가상 WAN을 사용 하 여 글로벌 전송 네트워크 및 보안에 지를 빌드할 수 있습니다.

다음과 같은 시나리오가 지원됩니다.
-   타사 파트너 제품을 통한 VNet에서 인터넷으로
-   타사 파트너 제품을 통해 인터넷으로 분기 합니다.
-   Azure 방화벽을 통해 타사 파트너 제품, 나머지 개인 트래픽 (스포크-스포크, 스포크-분기, 스포크)을 통해 인터넷으로 분기 합니다.

지원 되지 않는 시나리오는 다음과 같습니다.

- 파트너 제품을 통한 VNet 간 VNet은 개인 트래픽에 대해 Azure 방화벽과 함께 사용할 수 없습니다. 다음 제한 사항을 참조 하세요.

## <a name="current-limitations"></a>현재 제한 사항

- VNet에서 인터넷을 사용할 경우 개인 트래픽에 대한 Azure 방화벽 추가와 인터넷 트래픽에 대한 파트너 제품을 혼합할 수 없습니다. 보안 가상 허브의 Azure 방화벽 또는 타사 보안 파트너 제품에 인터넷 트래픽을 보낼 수 있습니다. 
- 가상 허브 당 최대 하나의 보안 파트너를 배포할 수 있습니다. 공급자를 변경 해야 하는 경우 기존 파트너를 제거 하 고 새 파트너를 추가 해야 합니다.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>보안 가상 허브에서 인터넷 트래픽 필터링 모범 사례

인터넷 트래픽은 일반적으로 웹 트래픽을 포함 합니다. 하지만 Office 365 (O365)와 같은 SaaS 응용 프로그램 및 Azure Storage, Azure Sql 등의 Azure 공용 PaaS 서비스를 대상으로 하는 트래픽도 포함 합니다. 다음은 이러한 서비스에 대한 트래픽을 처리 하기 위한 최선의 권장 방법입니다.

### <a name="handling-azure-paas-traffic"></a>Azure PaaS 트래픽 처리
 
- 트래픽이 대부분의 Azure PaaS로 구성 된 경우 보호를 위해 Azure 방화벽을 사용 하 고, IP 주소, Fqdn, 서비스 태그 또는 FQDN 태그를 사용 하 여 응용 프로그램에 대한 리소스 액세스를 필터링 할 수 있습니다.

- 트래픽이 SaaS 응용 프로그램 액세스로 구성 되어 있거나 사용자에 게 인식 되는 필터링 (예: VDI (가상 데스크톱 인프라) 워크 로드의 경우) 또는 고급 인터넷 필터링 기능이 필요한 경우 허브에서 타사 파트너 솔루션을 사용 합니다.

![Azure 방화벽 관리자에 대한 모든 시나리오](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365 (O365) 트래픽 처리

전역적으로 분산 된 분기 위치 시나리오에서는 Azure 보안 허브에 남은 인터넷 트래픽을 보내기 전에 지점에서 직접 Office 365 트래픽을 리디렉션해야 합니다.

Office 365의 경우 성공적인 사용자 환경을 위해 네트워크 대기 시간 및 성능이 매우 중요 합니다. 최적의 성능 및 사용자 환경에 대해 이러한 목표를 달성 하기 위해 고객은 Azure를 통해 나머지 인터넷 트래픽 라우팅을 고려 하기 전에 Office 365 직접 및 로컬 이스케이프를 구현 해야 합니다.

[Office 365 네트워크 연결 원칙](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) 은 사용자 분기 또는 모바일 장치에서 로컬로, 인터넷을 통해 직접 라우팅할 Microsoft 네트워크 상태에 있는 키 office 365 네트워크 연결에 대한 호출입니다.

또한 Office 365 연결은 개인 정보 보호를 위해 강력 하 게 암호화 되며 성능상의 이유로 효율적이 고 독점적인 프로토콜을 사용 합니다. 이렇게 하면 기존 네트워크 수준 보안 솔루션에 대한 이러한 연결을 적용 하기가 어렵습니다. 이러한 이유로 Azure를 통해 나머지 트래픽을 전송 하기 전에 고객이 지점에서 직접 Office 365 트래픽을 전송 하는 것이 좋습니다. Microsoft는 Azure와 Office 365을 통합 하는 여러 SD WAN 솔루션 공급자와 협력 하 여 고객이 쉽게 Office 365 직접 및 로컬 인터넷을 사용할 수 있도록 합니다. 자세한 내용은 [가상 WAN을 통해 내 O365 정책 설정 어떻게 할까요?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

[Azure 방화벽 관리자를 사용 하 여 보안 허브에 신뢰할 수 있는 보안 제품을 배포](deploy-trusted-security-partner.md)합니다.
