---
title: Azure 보안 및 준수 청사진 - UK OFFICIAL에 대한 3계층 IaaS 웹 애플리케이션
description: Azure 보안 및 준수 청사진 - UK OFFICIAL에 대한 3계층 IaaS 웹 애플리케이션
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 13ea2b68027c81bca7b43cef62cf7039aa0ea8dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609483"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure 보안 및 준수 청사진 - UK OFFICIAL에 대한 3계층 IaaS 웹 애플리케이션

## <a name="overview"></a>개요

 이 문서에서는 영국에서 공식으로 분류된 다양한 워크로드를 처리하기에 적절한 Microsoft Azure 3계층 웹 기반 아키텍처를 제공하는 지침 및 자동화 스크립트를 제공합니다.

 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 템플릿 집합은 인프라를 코드 방식을 사용하여 영국 NCSC(National Cyber Security Centre) 14개의 [클라우드 보안 원칙](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) 및 CIS(Center for Internet Security)의 [중요 보안 제어](https://www.cisecurity.org/critical-controls.cfm)에 맞는 환경을 배포합니다.

 NCSC에서는 고객이 서비스의 보안 속성을 평가하고 고객과 공급 업체 간에 책임 분배에 대한 이해를 돕기 위해 해당 클라우드 보안 원칙을 사용하도록 권장합니다. 책임 분배를 쉽게 이해할 수 있도록 이러한 원칙 각각에 대한 정보를 제공했습니다.

 이 아키텍처와 해당 Azure Resource Manager 템플릿은 Microsoft 백서 [Microsoft Azure를 사용하여 영국 클라우드용 14개의 클라우드 보안 컨트롤](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1)에서 지원됩니다. 이 문서는 Azure 서비스를 용도로 영국 NCSC 14 클라우드 보안 원칙을 조직을 신속히 이행할 전세계 및 영국 Microsoft Azure에서 클라우드 기반 서비스를 사용 하는 규정 준수 의무를 충족 하는 기능을 사용할 수 있게를 사용 하 여 정렬 클라우드입니다.

 이 템플릿은 워크로드에 대한 인프라를 배포합니다. 애플리케이션 코드 및 지원되는 비즈니스 계층과 데이터 계층 소프트웨어를 설치하고 구성해야 합니다. 자세한 배포 지침은 [여기](https://aka.ms/ukwebappblueprintrepo)에 있습니다.

 Azure 구독이 없는 경우 빠르고 쉽게 등록할 수 있습니다. [Azure를 시작](https://azure.microsoft.com/get-started/)하세요.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소

 Azure 템플릿은 UK OFFICIAL 워크로드를 지원하는 Azure 클라우드 환경에서 3계층 웹 애플리케이션 아키텍처를 제공합니다. 아키텍처는 온-프레미스 네트워크를 Azure 허용 웹 기반 작업으로 확장하는 보안 하이브리드 환경을 제공하여 인터넷에서 회사 사용자가 안전하게 액세스할 수 있게 됩니다.

![UK OFFICIAL에 대한 3계층 IaaS 웹 응용 프로그램 참조 아키텍처 다이어그램](images/ukofficial-iaaswa-architecture.png?raw=true "UK OFFICIAL에 대한 3계층 IaaS 웹 응용 프로그램 참조 아키텍처 다이어그램")

 이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처의 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

(1) /16 Virtual Network - 운영 VNet
- (3) /24 서브넷 - 3계층(웹, Biz, 데이터)
- (1) /27 서브넷 - ADDS
- (1) /27 서브넷 - 게이트웨이 서브넷
- (1) /29 서브넷 - Application Gateway 서브넷
- 기본 (Azure 제공) DNS 사용
- 관리 VNet에 활성화된 피어링
- 트래픽 흐름을 관리하는 NSG(보안 그룹 네트워크)

(1) /24 Virtual Network - 관리 VNet
- (1) /27 서브넷
- (2) ADDS DNS 및 (1) Azure DNS 항목 사용
- 운영 VNet에 활성화된 피어링
- 트래픽 흐름을 관리하는 NSG(보안 그룹 네트워크)

(1) Application Gateway
- WAF - 사용
- WAF 모드 - 방지
- 규칙 집합: OWASP 3.0
- 포트 80의 HTTP 수신기
- NSG를 통해 조정된 연결/트래픽
- 공용 IP 주소 엔드포인트 정의(Azure)

(1) VPN - 경로 기반 사이트 간 IPSec VPN 터널
- 공용 IP 주소 엔드포인트 정의(Azure)
- NSG를 통해 조정된 연결/트래픽
- (1) 로컬 네트워크 게이트웨이(온-프레미스 엔드포인트)
- (1) Azure 네트워크 게이트웨이(Azure 엔드포인트)

(9) Virtual Machines - 모든 VM은 Azure IaaS 맬웨어 방지 DSC 설정을 사용하여 배포됩니다.

- (2) Active Directory Domain Services 도메인 컨트롤러(Windows Server 2012 R2)
  - (2) DNS 서버 역할 - VM당 한 개
  - (2) 운영 VNet에 연결된 NIC - VM당 한 개
  - 모두 템플릿에 정의된 도메인에 도메인 가입됩니다.
    - 배포의 일부로 만들어진 도메인


- (1) Jumpbox(요새 호스트) 관리 VM
  - 공용 IP 주소를 사용하는 관리 VNet에 한 개의 NIC
    - NSG는 특정 원본에 대한 트래픽(입/출력)을 제한하기 위해 사용됩니다.
  - 도메인 가입 안 됨


- (2) 웹 계층 VM
  - (2) IIS 서버 역할 - VM당 한 개
  - (2) 운영 VNet에 연결된 NIC - VM당 한 개
  - 도메인 가입 안 됨


- (2) Biz 계층 VM
  - (2) 운영 VNet에 연결된 NIC - VM당 한 개
  - 도메인 가입 안 됨


- (2) 데이터 계층 VM
  - (2) 운영 VNet에 연결된 NIC - VM당 한 개
  - 도메인 가입 안 됨

가용성 집합
- (1) Active Directory 도메인 컨트롤러 VM 집합 - 2대의 VM
- (1) 웹 계층 VM 집합 - 2대의 VM
- (1) Biz 계층 VM 집합 - 2대의 VM
- (1) 데이터 계층 VM 집합 - 2대의 VM

Load Balancer
- (1) 웹 계층 부하 분산 장치
- (1) Biz 계층 부하 분산 장치
- (1) 데이터 계층 부하 분산 장치

Storage
- (14) 총 저장소 계정
  - Active Directory 도메인 컨트롤러 가용성 집합
    - (2) 주 LRS(로컬 중복 저장소) 계정 - 각 VM에 한 개  
    - (1) ADDS 가용성 집합에 대한 진단 LRS(로컬 중복 저장소) 계정
  - 관리 Jumpbox VM
    - (1) Jumpbox VM에 대한 주 LRS(로컬 중복 저장소) 계정
    - (1) Jumpbox VM에 대한 진단 LRS(로컬 중복 저장소) 계정
  - 웹 계층 VM
    - (2) 주 LRS(로컬 중복 저장소) 계정 - 각 VM에 한 개  
    - (1) 웹 계층 가용성 집합에 대한 진단 LRS(로컬 중복 저장소) 계정
  - Biz 계층 VM
    - (2) 주 LRS(로컬 중복 저장소) 계정 - 각 VM에 한 개  
    - (1) Biz 계층 가용성 집합에 대한 진단 LRS(로컬 중복 저장소) 계정
  - 데이터 계층 VM
    - (2) 주 LRS(로컬 중복 저장소) 계정 - 각 VM에 한 개  
    - (1) 데이터 계층 가용성 집합에 대한 진단 LRS(로컬 중복 저장소) 계정

### <a name="deployment-architecture"></a>배포 아키텍처:

**온-프레미스 네트워크**: 조직에서 구현되는 개인 로컬 영역 네트워크입니다.

**프로덕션 VNet**: 프로덕션 [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview)(가상 네트워크)은 Azure에서 실행 중인 애플리케이션 및 다른 작업 리소스를 호스팅합니다. 각 VNet에는 네트워크 트래픽을 격리하고 관리하는 데 사용되는 여러 서브넷이 포함될 수 있습니다.

**웹 계층**: 들어오는 HTTP 요청을 처리합니다. 이 계층을 통해 응답이 반환됩니다.

**비즈니스 계층**: 비즈니스 프로세스 및 시스템에 대한 다른 기능 논리를 구현합니다.

**데이터베이스 계층**: 고가용성을 위해 [SQL Server Always On 가용성 그룹](https://msdn.microsoft.com/library/hh510230.aspx)을 사용하여 영구 데이터 스토리지를 제공합니다. 고객은 PaaS 대신 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)를 사용할 수 있습니다.

**게이트웨이**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)는 온-프레미스 네트워크의 라우터와 프로덕션 VNet 간에 연결을 제공합니다.

**인터넷 게이트웨이 및 공용 IP 주소**: 인터넷 게이트웨이는 인터넷을 통해 사용자에게 애플리케이션 서비스를 노출합니다. 이러한 서비스에 액세스하는 트래픽은 WAF(웹 애플리케이션 방화벽) 보호 기능을 포함하는 계층 7 라우팅 및 부하 분산 기능을 제공하는 [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)를 사용하여 보호됩니다.

**관리 VNet**: 이 [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)에는 프로덕션 VNet에서 실행되는 워크로드에 관리 및 모니터링 기능을 구현하는 리소스가 포함되어 있습니다.

**Jumpbox**: [요새 호스트](https://en.wikipedia.org/wiki/Bastion_host)라고도 하며 관리자가 프로덕션 VNet의 VM에 연결하는 데 사용하는 네트워크의 보안 VM입니다. jumpbox는 안전 목록에 있는 공용 IP 주소의 원격 트래픽만 허용하는 NSG를 사용합니다. RDP(원격 데스크톱) 트래픽을 허용하려면 트래픽의 원본이 NSG에 정의되어야 합니다. 프로덕션 리소스는 보안 Jumpbox VM을 사용하여 RDP를 통해 관리됩니다.

**사용자 정의 경로**: [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)는 Azure VNet 내에서 IP 트래픽 흐름을 정의하는 데 사용됩니다.

**네트워크 피어링된 VNet**: 프로덕션 및 관리 VNet은 [VNet 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 사용하여 연결됩니다.
이 VNet은 여전히 별도 리소스로 관리되지만 이러한 가상 머신에 대한 모든 연결 용도 중 하나로 표시됩니다. 이러한 네트워크는 개인 IP 주소를 사용하여 직접 서로 통신합니다. VNet 피어링은 동일한 Azure 지역에 있는 VNet에 종속됩니다.

**네트워크 보안 그룹**: [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 VNet 내에서 트래픽을 허용하거나 거부하는 액세스 제어 목록이 포함됩니다. NSG는 서브넷 또는 개별 VM 수준에서 트래픽을 보호하는 데 사용할 수 있습니다.

**AD DS(Active Directory Domain Services)**: 이 아키텍처에서는 전용 [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) 배포를 제공합니다.

**로깅 및 감사**: [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 작업을 시작한 사용자, 작업이 발생한 시기, 작업의 상태 및 작업을 조사할 수 있는 다른 속성의 값과 같은 구독의 리소스에서 수행된 작업을 캡처합니다. Azure 활동 로그는 구독에서 모든 작업을 캡처하는 Azure 플랫폼 서비스입니다. 필요한 경우 로그를 보관하거나 내보낼 수 있습니다.

**네트워크 모니터링 및 경고**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)는 VNet 내에서 네트워크 트래픽에 대한 네트워크 패킷 캡처, 흐름 로깅, 토폴로지 도구 및 진단 유틸리티를 제공하는 플랫폼 서비스입니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="business-continuity"></a>비즈니스 연속성

**고가용성**: 서버 워크로드는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 그룹화되어 Azure에서 가상 머신의 고가용성을 보장합니다. 이 구성을 통해 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있고 99.95% Azure SLA가 충족되도록 합니다.

### <a name="logging-and-audit"></a>로깅 및 감사

**모니터링**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)는 모든 Azure 리소스의 활동 로그, 메트릭 및 진단 로그를 모니터링하기 위해 단일 원본을 제공하는 플랫폼 서비스입니다. Azure Monitor는 Azure의 리소스에서 비롯된 메트릭과 로그에 대해 시각화, 쿼리, 라우팅, 보관 및 조치를 수행하도록 구성될 수 있습니다. 리소스 기반 액세스 제어를 사용하여 사용자가 로그를 수정하는 기능을 갖지 못하도록 하기 위해 감사 내역을 보호하는 것이 좋습니다.

**활동 로그**: [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)를 구성하여 구독에 있는 리소스에서 수행된 작업에 대한 자세한 정보를 제공합니다.

**진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 리소스에서 내보낸 모든 로그입니다. 이러한 로그는 Windows 이벤트 시스템 로그, Blob, 테이블 및 큐 로그를 포함할 수 있습니다.

**방화벽 로그.**: Application Gateway는 전체 진단 및 액세스 로그를 제공합니다. 방화벽 로그는 WAF를 사용하도록 설정한 Application Gateway 리소스에 사용할 수 있습니다.

**로그 보관**: 보관 및 정의 된 보존 기간에 대 한 중앙 집중화 된 Azure 저장소 계정에 쓸 로그 데이터 저장소를 구성할 수 있습니다. Azure Monitor 로그를 사용 하 여 로그를 처리할 수 있습니다 또는 타사 SIEM 시스템으로 합니다.

### <a name="identity"></a>ID

**Active Directory Domain Services**: 이 아키텍처는 Azure에서 Active Directory Domain Services 배포를 제공합니다. Azure에서 Active Directory를 구현하는 방법에 대한 특정 권장 구성은 다음 문서를 참조하세요.

[AD DS(Active Directory Domain Services)를 Azure로 확장](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain)

[Azure Virtual Machines에 Windows Server Active Directory를 배포하기 위한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)

**Active Directory 통합**: 고객은 전용 AD DS 아키텍처 대신 [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) 통합 또는 [온-프레미스 포리스트에 가입된 Azure의 Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity)를 사용하는 것이 좋습니다.

### <a name="security"></a>보안

**관리 보안**: 이 청사진을 통해 관리자는 신뢰할 수 있는 원본의 RDP를 사용하여 관리 VNet 및 Jumpbox에 연결할 수 있습니다. NSG를 사용하여 관리 VNet에 대한 네트워크 트래픽을 제어합니다. 포트 3389에 대한 액세스는 Jumpbox를 비롯한 서브넷에 액세스할 수 있는 신뢰할 수 있는 IP 범위의 트래픽으로 제한됩니다.

또한 고객은 [강화된 보안 관리 모델](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access)을 사용하여 관리 VNet 및 Jumpbox에 연결할 때 환경을 보호할 수 있습니다. 보안 향상을 위해 고객이 [권한 있는 액세스 워크스테이션](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) 및 RDGateway 구성을 사용하는 것이 좋습니다. 네트워크 가상 어플라이언스 및 공개/개인 DMZ를 사용하면 더욱 강화된 보안 기능을 제공합니다.

**네트워크 보안**: NSG([네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg))는 잘못 구성되었거나 사용할 수 없는 게이트웨이를 무시하는 인바운드 트래픽에 대해 두 번째 수준의 보호를 제공하는 각 서브넷에 권장됩니다. 예제 - [NSG 배포용 Resource Manager 템플릿](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**공용 엔드포인트 보안**: 인터넷 게이트웨이는 인터넷을 통해 사용자에게 애플리케이션 서비스를 노출합니다. 이러한 서비스에 액세스하는 트래픽은 [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)를 사용하여 보호됩니다. 여기서는 웹 애플리케이션 방화벽 및 HTTPS 프로토콜 관리를 제공합니다.

**IP 범위**: 아키텍처의 IP 범위는 제안된 범위입니다. 고객은 고유한 환경을 고려하고 적절한 범위를 사용하는 것이 좋습니다.

**하이브리드 연결**: 클라우드 기반 워크로드는 Azure VPN Gateway를 사용하여 IPSEC VPN을 통해 온-프레미스 데이터 센터에 연결됩니다. 고객은 Azure에 연결할 적절한 VPN Gateway를 사용하고 있는지 확인해야 합니다. 예제 - [VPN Gateway Resource Manager 템플릿](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). 빅 데이터 요구 사항을 포함하는 대규모의 중요 업무용 워크로드를 실행하는 고객은 Microsoft 클라우드 서비스에 대한 개인 네트워크 연결에 [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute)를 사용하는 하이브리드 네트워크 아키텍처를 사용하는 것이 좋습니다.

**문제의 분리** 이 참조 아키텍처는 관리 작업 및 비즈니스 운영에 대한 VNet을 분리합니다. 별도 VNet 및 서브넷은 [Microsoft 클라우드 서비스 및 네트워크 보안](https://docs.microsoft.com/azure/best-practices-network-security) 모범 사례를 따르는 네트워크 세그먼트 간에 NSG를 사용하여 트래픽 수신 및 송신 제한을 비롯한 트래픽 관리를 허용합니다.

**리소스 관리**: VM, VNet 및 부하 분산 장치와 같은 Azure 리소스는 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)으로 함께 그룹화하여 관리됩니다. 리소스 기반 액세스 제어 역할을 각 리소스 그룹에 할당하여 권한 있는 사용자에 대한 액세스만을 제한할 수 있습니다.

**액세스 제어 제한**: RBAC([역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal))를 사용하여 [사용자 정의 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)을 사용하는 애플리케이션의 리소스를 관리합니다. RBAC를 사용하여 DevOps가 각 계층에서 수행할 수 있는 작업을 제한할 수 있습니다. 사용 권한을 부여할 때 [최소 권한의 원칙](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)을 사용합니다. 모든 관리 작업을 기록하고 정기 감사를 수행하여 구성 변경을 계획했는지 확인합니다.

**인터넷 액세스**: 이 참조 아키텍처는 [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) 를 인터넷 게이트웨이 및 부하 분산 장치를 연결 합니다. 또한 일부 고객은 [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) 대신 네트워킹 보안의 추가 계층에 타사 네트워크 가상 어플라이언스를 사용할 수 있습니다.

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 구독에서 리소스 보안 상태를 중앙에서 볼 수 있으며 리소스 손상을 방지하기 위한 권장 사항을 확인할 수 있습니다. 또한 더 세분화된 정책을 활성화하는 데 사용할 수 있습니다. 예를 들어 정책은 특정 리소스 그룹에 적용될 수 있습니다. 그러면 기업이 해당 상태를 위험에 맞게 조정할 수 있습니다. 고객은 Azure 구독에서 Azure Security Center를 사용하는 것이 좋습니다.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC 클라우드 보안 원칙 준수 설명서

Crown Commercial Service(정부에 의한 상업 및 조달 활동을 개선하기 위해 작동하는 에이전시)는 Microsoft 범위 내의 엔터프라이즈 클라우드 서비스의 분류를 공식 수준에 해당하는 모든 제품을 포함하는 G-Cloud v6로 갱신했습니다. Azure와 G-Cloud의 세부 정보는 [Azure 영국 G-Cloud 보안 평가 요약](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud)에서 찾을 수 있습니다.

이 청사진은 NCSC [클라우드 보안 원칙](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)에 설명된 14개의 클라우드 보안 원칙에 맞추어 환경에서 UK-OFFICIAL로 분류된 워크로드를 지원하도록 합니다.

[고객 책임 매트릭스](https://aka.ms/ukofficial-crm)(Excel 통합 문서)은 14개의 클라우드 보안 원칙을 모두 나열하고 행렬은 각 원칙(또는 원칙 하위 부분)에서 원칙 구현이 Microsoft, 고객 또는 둘 간에 공유된 책임인지를 나타냅니다.

[원칙 구현 행렬](https://aka.ms/ukofficial-iaaswa-pim)(Excel 통합 문서)은 14개의 클라우드 보안 원칙을 모두 나열하고 행렬은 고객 책임 행렬에서 고객 책임을 지정한 각 원칙(또는 원칙 하위 부분)에서 1) Blueprint 자동화가 원칙을 구현하는지 및 2) 구현을 원칙 요구 사항에 맞추는 방법을 나타냅니다.

또한 CSA(Cloud Security Alliance) 클라우드 서비스를 이동하기 전에 클라우드 공급자의 평가에서 고객을 지원하고 응답해야 하는 질문을 식별하는 클라우드 제어 행렬을 게시했습니다. 응답에서 Microsoft Azure는 [CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)(CSA Consensus Assessment Initiative Questionnaire)에 대답했습니다. 여기서는 Microsoft에서 제안된 원칙을 해결하는 방법을 설명합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

이 Blueprint 자동화를 배포하기 위해 배포 사용자가 사용할 수 있는 방법은 두 가지입니다. 첫 번째 메서드는 두 번째 방법은 참조 아키텍처를 배포 하려면 Azure portal을 사용 하는 반면 PowerShell 스크립트를 사용 합니다. 자세한 배포 지침은 [여기](https://aka.ms/ukofficial-iaaswa-repo)에 있습니다.

## <a name="disclaimer"></a>고지 사항

 - 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
 - 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
 - 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
 - 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
 - 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
 - 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
