---
title: Azure에 대한 TIC(Trusted Internet Connections) 지침
description: Azure 및 SaaS 서비스에 대한 TIC(Trusted Internet Connections) 지침입니다.
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608094"
---
# <a name="trusted-internet-connections-guidance"></a>TIC(Trusted Internet Connections) 지침

이 문서에서는 정부 기관에서 Microsoft Azure Government를 사용하여 TIC(Trusted Internet Connections) 이니셔티브를 준수하도록 지원할 수 있는 방법에 대해 설명합니다. 그리고 Azure IaaS(Infrastructure as a Service)와 Azure PaaS(Platform as a Service) 제품에서 Azure Government를 사용하는 방법도 설명합니다.

## <a name="trusted-internet-connections-overview"></a>TIC(Trusted Internet Connections) 개요

TIC 이니셔티브는 연방 기관에서 사용하는 개별 외부 네트워크 연결의 보안을 최적화하고 표준화하기 위한 것입니다. 정책은 [OMB(미국 관리예산처) 각서 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf)에 요약되어 있습니다.

2007년 11월에 OMB는 연방 네트워크 경계 보안 및 인시던트 응답 기능을 개선하기 위해 TIC 프로그램을 수립했습니다. TIC는 모든 인바운드 및 아웃바운드 .gov 트래픽에 대항 네트워크 분석을 수행하여 특정 서명 및 패턴 기반 데이터를 식별하도록 설계되었습니다. TIC는 봇 네트 활동과 같은 동작 이상을 발견합니다. 기관에서는 자체의 외부 네트워크 연결을 통합하고 EINSTEIN으로 알려진 침입 탐지 및 방지 디바이스를 통해 모든 트래픽을 라우팅해야 합니다. 디바이스는 _TIC(Trusted Internet Connections)_ 라고 하는 제한된 수의 네트워크 엔드포인트에서 호스팅됩니다.

TIC의 목표는 기관에서 다음 항목을 파악하도록 하는 것입니다.
- 내 네트워크에 있는 사람(권한 있음 또는 권한 없음)
- 내 네트워크에 액세스하는 시간 및 이유
- 액세스하는 리소스

모든 기관 외부 연결은 현재 OMB 승인 TIC를 통해 라우팅해야 합니다. 연방 기관은 TICAP(TIC 액세스 공급자)로 TIC 프로그램에 참여하거나 주요 계층 1 인터넷 서비스 공급자 중 하나와 서비스를 계약해야 합니다. 이러한 공급자는 MTIPS(Managed Trusted Internet Protocol Service) 공급자라고 합니다. TIC에는 기관 및 MTIPS 공급자에서 수행하는 중요한 필수 기능이 포함되어 있습니다. 현재 버전의 TIC에서는 EINSTEIN 버전 2 침입 탐지 및 EINSTEIN 버전 3 가속화(3A) 침입 방지 디바이스가 각 TICAP 및 MTIPS에 배포됩니다. 기관은 연방 시스템에 EINSTEIN 기능을 배포하기 위해 DHS(미국 국토안보부)와 "양해 각서"를 체결합니다.

.gov 네트워크를 보호해야 하는 책임의 일환으로, DHS는 기관 네트워크 흐름 데이터의 원시 데이터 피드를 통해 연방 엔터프라이즈 전체에서 인시던트의 상관 관계를 지정하고 특수 도구를 사용하여 분석을 수행해야 합니다. DHS 라우터는 인터페이스를 들어오거나 나갈 때 IP 네트워크 트래픽을 수집하는 기능을 제공합니다. 네트워크 관리자는 네트워크 흐름 데이터를 분석하여 트래픽 원본 및 대상, 서비스 클래스 등을 파악할 수 있습니다. 네트워크 흐름 데이터는 헤더, 원본 IP, 대상 IP 등과 비슷한 "콘텐츠가 아닌 데이터"로 간주됩니다. DHS는 콘텐츠가 아닌 데이터를 통해 내용, 즉 누구에게 무엇을 얼마나 오랫동안 수행하고 있었는지에 대해 알 수 있습니다.

또한 이니셔티브에는 온-프레미스 인프라를 가정하는 보안 정책, 지침 및 프레임워크가 포함되어 있습니다. 정부 기관이 비용 절감, 운영 효율성 및 혁신을 달성하기 위해 클라우드로 전환하면서 TIC의 구현 요구 사항으로 인해 네트워크 트래픽이 지연될 수 있습니다. 이에 따라 정부 사용자가 클라우드 기반 데이터에 액세스할 수 있는 속도와 민첩성이 제한됩니다.

## <a name="azure-networking-options"></a>Azure 네트워킹 옵션

Azure 서비스에 연결하는 세 가지 주요 옵션은 다음과 같습니다.

- 직접 인터넷 연결: 개방형 인터넷 연결을 통해 Azure 서비스에 직접 연결합니다. 매체와 연결은 공용입니다. 애플리케이션 및 전송 수준 암호화를 통해 개인 정보를 보호합니다. 대역폭은 사이트의 인터넷 연결로 인해 제한됩니다. 둘 이상의 활성 공급자를 사용하여 복원력을 보장합니다.
- VPN(가상 사설망) VPN 게이트웨이를 사용하여 Azure 가상 네트워크에 비공개로 연결합니다.
매체는 사이트의 표준 인터넷 연결을 트래버스하기 때문에 공개되지만, 개인 정보를 보호하기 위해 터널에서 연결이 암호화됩니다. 대역폭은 선택한 구성 및 VPN 디바이스에 따라 제한됩니다. Azure 지점 간 연결은 일반적으로 100Mbps로 제한되고, 사이트 간 연결은 1.25Gbps로 제한됩니다.
- Azure ExpressRoute: ExpressRoute는 Microsoft 서비스에 대한 직접 연결입니다. 연결이 격리된 파이버 채널을 통해 이루어지므로 사용된 구성에 따라 연결이 공개 또는 비공개가 될 수 있습니다. 대역폭은 일반적으로 최대 10Gbps로 제한됩니다.

DHS의 “TIC(Trusted Internet Connections) 참조 아키텍처 문서 버전 2.0”에서 명시한 대로 TIC 부록 H(클라우드 고려 사항) 요구 사항이 충족되는 몇 가지 방법이 있습니다. 이 문서에서 DHS TIC 지침은 **TIC 2.0**이라고 합니다.

D/A(부처 또는 기관) TIC를 통해 트래픽을 라우팅하지 않고 **D/A**에서 Azure 또는 Office 365로 연결할 수 있으려면 D/A에서 CSP(클라우드 서비스 공급자)에 대한 암호화된 터널 또는 전용 연결을 사용해야 합니다. CSP 서비스는 D/A 클라우드 자산에 대한 연결이 기관 담당자의 직접 액세스를 위해 공용 인터넷에 제공되지 않도록 보장할 수 있습니다.

Office 365는 [Microsoft 피어링](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings)을 사용하도록 설정된 ExpressRoute 또는 TLS 1.2를 통해 모든 트래픽을 암호화하는 인터넷 연결을 사용하여 TIC 2.0 부록 H를 준수합니다. D/A 네트워크의 D/A 최종 사용자는 인터넷을 통해 기관 네트워크 및 TIC 인프라에 연결할 수 있습니다. Office 365에 대한 모든 원격 인터넷 액세스는 차단되며 기관을 통해 라우팅됩니다. D/A는 Microsoft 피어링(공용 피어링 유형)을 사용하도록 설정된 ExpressRoute를 통해 Office 365에 연결할 수도 있습니다.  

Azure의 경우에만 두 번째 옵션(VPN) 및 세 번째 옵션(ExpressRoute)이 인터넷 액세스를 제한하는 서비스와 함께 사용될 때 이러한 요구 사항을 충족할 수 있습니다.

![Microsoft TIC(Trusted Internet Connection)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Azure IaaS(Infrastructure as a Service) 제품

Azure 고객이 자신의 가상 네트워크 라우팅을 관리하므로 Azure IaaS를 사용하여 TIC 정책을 준수하는 것은 비교적 간단합니다.

TIC 참조 아키텍처를 준수하는 데 도움이 되는 주요 요구 사항은 가상 네트워크가 D/A 네트워크의 비공개 확장인지 확인하는 것입니다. _비공개_ 확장이 되려면 정책에 따라 온-프레미스 TIC 네트워크 연결을 통하는 경우를 제외하고는 네트워크에서 나가는 트래픽이 필요하지 않습니다. 이 프로세스를 _강제 터널링_이라고 합니다. TIC를 준수하기 위해 이 프로세스는 모든 트래픽을 CSP 환경의 모든 시스템에서 조직의 네트워크에 있는 온-프레미스 게이트웨이를 통과하여 TIC를 통해 인터넷으로 라우팅합니다.

Azure IaaS TIC 준수는 다음 두 가지 주요 단계로 구분됩니다.

- 1단계: 구성
- 2단계: 감사

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC 규정 준수: 구성

Azure를 사용하여 TIC 준수 아키텍처를 구성하려면 먼저 가상 네트워크에 대한 직접 인터넷 연결을 방지한 다음, 온-프레미스 네트워크를 통해 인터넷 트래픽을 강제로 적용해야 합니다.

#### <a name="prevent-direct-internet-access"></a>직접 인터넷 액세스 방지

Azure IaaS 네트워킹은 가상 머신의 NIC(네트워크 인터페이스 컨트롤러)가 연결된 서브넷으로 구성된 가상 네트워크를 통해 수행됩니다.

TIC 준수를 지원하는 가장 간단한 시나리오는 가상 머신 또는 가상 머신 컬렉션에서 외부 리소스에 연결할 수 없도록 하는 것입니다. NSG(네트워크 보안 그룹)를 사용하여 외부 네트워크와의 연결을 끊습니다. NSG를 사용하여 가상 네트워크에 있는 하나 이상의 NIC 또는 서브넷에 대한 트래픽을 제어합니다. NSG에는 트래픽 방향, 프로토콜, 원본 주소 및 포트, 대상 주소 및 포트에 따라 트래픽을 허용하거나 거부하는 액세스 제어 규칙이 포함되어 있습니다. NSG의 규칙은 언제든지 변경할 수 있으며, 변경 내용은 연결된 모든 인스턴스에 적용됩니다. NSG를 만드는 방법에 대한 자세한 내용은 [네트워크 보안 그룹을 통해 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)을 참조하세요.

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>온-프레미스 네트워크를 통한 인터넷 트래픽 강제 적용

Azure는 시스템 경로를 자동으로 만들고 가상 네트워크의 각 서브넷에 경로를 할당합니다. 시스템 경로는 만들거나 제거할 수 없지만, 일부 시스템 경로는 사용자 지정 경로를 사용하여 재정의할 수 있습니다. Azure는 각 서브넷에 대한 기본 시스템 경로를 만듭니다. Azure는 특정 Azure 기능을 사용하는 경우 특정 서브넷 또는 모든 서브넷에 선택적 기본 경로를 추가합니다. 이 유형의 라우팅은 다음을 보장합니다.
- 가상 네트워크 내에서 예정된 트래픽은 가상 네트워크 내에서 유지됩니다.
- 10.0.0.0/8과 같이 IANA에서 지정한 개인 주소 공간은 가상 네트워크 주소 공간에 포함되지 않으면 삭제됩니다.
- "마지막 수단"으로 가상 네트워크 인터넷 엔드포인트에 0.0.0.0/0을 라우팅합니다.

![TIC 강제 터널링](media/tic-diagram-c.png)

모든 트래픽에서 D/A TIC를 트래버스하도록 하려면 가상 네트워크에서 나가는 모든 트래픽이 온-프레미스 연결을 통해 라우팅되어야 합니다. 사용자 정의 경로를 만들거나 온-프레미스 네트워크 게이트웨이와 Azure VPN 게이트웨이 간에 BGP(Border Gateway Protocol) 경로를 교환하여 사용자 지정 경로를 만듭니다. 사용자 정의 경로에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅: 사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)를 참조하세요. BGP에 대한 자세한 내용은 [가상 네트워크 트래픽 라우팅: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)을 참조하세요.

#### <a name="add-user-defined-routes"></a>사용자 정의 경로 추가

경로 기반 가상 네트워크 게이트웨이를 사용하는 경우 Azure에서 강제 터널링을 수행할 수 있습니다. 가상 네트워크 게이트웨이의 **다음 홉**으로 라우팅하도록 0.0.0.0/0 트래픽을 설정하는 사용자 정의 경로(UDR)를 추가합니다. Azure에서 사용자 정의 경로는 시스템 정의 경로보다 우선 순위가 높습니다. 모든 비가상 네트워크 트래픽은 가상 네트워크 게이트웨이로 전송된 다음, 해당 게이트웨이에서 이 트래픽을 온-프레미스로 라우팅할 수 있습니다. UDR이 정의되면 Azure 환경의 모든 가상 네트워크 내에서 경로를 기존 서브넷 또는 새 서브넷과 연결합니다.

![사용자 정의 경로 및 TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Border Gateway Protocol 사용

ExpressRoute 또는 BGP 지원 가상 네트워크 게이트웨이를 사용하는 경우 BGP가 경로 보급에 대한 기본 설정 메커니즘입니다. BGP 보급 경로가 0.0.0.0/0이면 ExpressRoute 및 BGP 인식 가상 네트워크 게이트웨이에서 이 기본 경로가 가상 네트워크 내의 모든 서브넷에 적용되도록 합니다.

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC 규정 준수: 감사

Azure는 TIC 준수를 감사하는 몇 가지 방법을 제공합니다.

#### <a name="view-effective-routes"></a>유효한 경로 보기

[Azure Portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) 또는 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell)에서 특정 가상 머신, 특정 NIC 또는 사용자 정의 경로 테이블에 대한 _유효 경로_를 관찰하여 기본 경로가 전파되는지 확인합니다. **유효 경로**는 다음 그림과 같이 관련 사용자 정의 경로, BGP 보급 경로 및 관련 엔터티에 적용되는 시스템 경로를 보여 줍니다.

![유효한 경로](media/tic-screen-1.png)

> [!NOTE]
> NIC가 실행 중인 가상 머신과 연결되어 있지 않으면 NIC에 대한 유효 경로를 볼 수 없습니다.

#### <a name="use-azure-network-watcher"></a>Azure Network Watcher 사용

Azure Network Watcher는 TIC 준수를 감사하는 몇 가지 도구를 제공합니다. 자세한 내용은 [Network Watcher에 대한 개요](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)를 참조하세요.

##### <a name="capture-network-security-group-flow-logs"></a>네트워크 보안 그룹 흐름 로그 캡처 

Network Watcher를 사용하여 IP 트래픽을 둘러싼 메타데이터를 나타내는 네트워크 흐름 로그를 캡처합니다. 네트워크 흐름 로그에는 대상의 원본/대상 주소 및 다른 데이터가 포함되어 있습니다. 이 데이터를 가상 네트워크 게이트웨이, 온-프레미스 에지 디바이스 또는 TIC의 로그와 함께 사용하여 모든 트래픽이 온-프레미스에서 라우팅되는지 모니터링할 수 있습니다. 

## <a name="azure-platform-as-a-service-offerings"></a>Azure Platform as a Service 제품

Azure Storage와 같은 Azure PaaS 서비스는 인터넷 연결 가능한 URL을 통해 액세스할 수 있습니다. 승인된 자격 증명만 있으면 누구든지 TIC을 트래버스하지 않고 임의 위치에서 저장소 계정 등의 리소스에 액세스할 수 있습니다. 이러한 이유로 많은 정부 고객은 Azure PaaS 서비스에서 TIC 정책을 준수하지 않는다고 잘못된 결론을 내립니다. 많은 Azure PaaS 서비스는 TIC 정책을 준수할 수 있습니다. 아키텍처가 [앞에서 설명한 대로](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings) TIC 준수 IaaS 환경과 동일하고 서비스가 Azure 가상 네트워크에 연결되면 해당 서비스에서 이러한 정책을 준수합니다.

Azure PaaS 서비스가 가상 네트워크와 통합되면 해당 가상 네트워크에서 비공개로 이 서비스에 액세스할 수 있습니다. 사용자 정의 경로 또는 BGP를 통해 0.0.0.0/0에 대한 사용자 지정 라우팅을 적용할 수 있습니다. 사용자 지정 라우팅을 사용하면 모든 인터넷에 바인딩된 모든 트래픽이 온-프레미스에서 TIC를 트래버스하도록 라우팅할 수 있습니다. 다음 패턴을 사용하여 Azure 서비스를 가상 네트워크에 통합합니다.

- **서비스의 전용 인스턴스 배포**: 가상 네트워크에 연결된 엔드포인트가 있는 전용 인스턴스로 배포될 수 있는 PaaS 서비스의 수가 점점 증가하고 있습니다. 네트워크 엔드포인트를 가상 네트워크로 제한할 수 있도록 "격리" 모드에서 PowerApps용 App Service Environment를 배포할 수 있습니다. 그러면 App Service Environment에서 Azure Web Apps, Azure API Management 및 Azure Functions와 같이 다양한 Azure PaaS 서비스를 호스팅할 수 있습니다.
- **가상 네트워크 서비스 엔드포인트 사용**: 엔드포인트를 공용 주소 대신 가상 네트워크 사설 IP로 이동하는 옵션을 허용하는 PaaS 서비스 수가 점점 증가하고 있습니다.

2018년 5월 현재 전용 인스턴스를 가상 네트워크에 배포하거나 서비스 엔드포인트를 사용할 수 있도록 지원하는 서비스는 다음 표에 나와 있습니다.

> [!Note]
> 가용성 상태는 상업적으로 사용할 수 있는 Azure 서비스에 해당합니다. Azure Government의 Azure 서비스에 대한 가용성 상태는 보류 중입니다.

### <a name="support-for-service-endpoints"></a>서비스 엔드포인트 지원

|서비스                        |가용성      |
|-------------------------------|------------------|
|Azure Key Vault                | 비공개 미리 보기  |
|Azure Cosmos DB                | 비공개 미리 보기  |
|ID 서비스              | 비공개 미리 보기  |
|Azure Data Lake                | 비공개 미리 보기  |
|Azure Database for PostgreSQL  | 비공개 미리 보기  |
|Azure Database for MySQL       | 비공개 미리 보기  |
|Azure SQL Data Warehouse       | 공개 미리 보기   |
|Azure SQL Database             | GA(일반 공급) |
|Azure Storage                  | GA               |

### <a name="support-for-virtual-network-injection"></a>가상 네트워크 삽입 지원

|서비스                               |가용성      |
|--------------------------------------|------------------|
|Azure SQL Database Managed Instance   | 공개 미리 보기   |
|AKS(Azure Kubernetes Service)        | 공개 미리 보기   |
|Azure Service Fabric                  | GA               |
|Azure API Management                  | GA               |
|Azure Active Directory                | GA               |
|Azure Batch                           | GA               |
|App Service 환경               | GA               |
|Azure Cache for Redis                     | GA               |
|Azure HDInsight                       | GA               |
|가상 머신 크기 집합             | GA               |
|Azure Cloud Services                  | GA               |


### <a name="virtual-network-integration-details"></a>가상 네트워크 통합 세부 정보

다음 다이어그램에서는 PaaS 서비스 액세스에 대한 일반적인 네트워크 흐름을 보여 줍니다. 가상 네트워크 삽입 및 가상 네트워크 서비스 터널링에서의 액세스가 모두 표시되어 있습니다. 네트워크 서비스 게이트웨이, 가상 네트워크 및 서비스 태그에 대한 자세한 내용은 [네트워크 및 애플리케이션 보안 그룹: 서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)를 참조하세요.

![TIC 준수 PaaS 연결 옵션](media/tic-diagram-e.png)

1. ExpressRoute를 사용하여 Azure에 비공개로 연결됩니다. 강제 터널링을 사용하는 ExpressRoute 개인 피어링은 모든 고객 가상 네트워크 트래픽이 ExpressRoute를 통과하도록 강제하고 온-프레미스로 다시 보내는 데 사용됩니다. Microsoft 피어링은 필요하지 않습니다.
2. Azure VPN Gateway를 ExpressRoute Microsoft 피어링과 함께 사용하면 고객 가상 네트워크와 온-프레미스 에지 간의 종단 간 IPSec 암호화를 오버레이할 수 있습니다. 
3. 고객 가상 네트워크에 대한 네트워크 연결은 고객이 IP, 포트 및 프로토콜에 따라 허용/거부할 수 있는 NSG를 사용하여 제어됩니다.
4. 고객 가상 네트워크는 고객의 서비스에 대한 서비스 엔드포인트를 만들어 PaaS 서비스로 확장됩니다.
5. PaaS 서비스 엔드포인트는 **모든 액세스를 기본적으로 거부**하고 고객 가상 네트워크 내의 지정된 서브넷의 액세스만 허용하도록 보안이 설정됩니다. 기본 거부에는 인터넷에서 발생한 연결도 포함됩니다.
6. 고객 가상 네트워크 내의 리소스에 액세스해야 하는 다른 Azure 서비스는 다음 중 하나여야 합니다.  
   - 가상 네트워크에 직접 배포됩니다.
   - 각각의 Azure 서비스의 지침에 따라 선택적으로 허용됩니다.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>옵션 A: 서비스의 전용 인스턴스 배포(가상 네트워크 삽입)

가상 네트워크 삽입을 사용하면 고객이 HDInsight와 같은 지정된 Azure 서비스의 전용 인스턴스를 고객 고유의 가상 네트워크에 선택적으로 배포할 수 있습니다. 서비스 인스턴스는 고객 가상 네트워크의 전용 서브넷에 배포됩니다. 가상 네트워크 삽입을 사용하면 인터넷에서 라우팅할 수 없는 주소를 통해 서비스 리소스에 액세스할 수 있습니다. 온-프레미스 인스턴스는 ExpressRoute 또는 사이트 간 VPN을 사용하여 방화벽을 공용 인터넷 주소 공간으로 여는 대신, 가상 네트워크 주소 공간을 통해 서비스 인스턴스에 직접 액세스합니다. 전용 인스턴스가 엔드포인트에 연결되면 IaaS TIC 준수와 동일한 전략을 사용할 수 있습니다. 기본 라우팅은 인터넷에 바인딩된 트래픽이 온-프레미스에 바인딩된 가상 네트워크 게이트웨이로 리디렉션되도록 합니다. 지정된 서브넷에 대한 NSG를 통해 인바운드 및 아웃바운드 액세스를 추가로 제어할 수 있습니다.

![가상 네트워크 삽입 개요](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>옵션 B: 가상 네트워크 서비스 엔드포인트 사용(서비스 터널)

"서비스 엔드포인트"를 제공하는 Azure 다중 테넌트 서비스의 수가 점점 증가하고 있습니다. 서비스 엔드포인트는 Azure 가상 네트워크에 통합하기 위한 대체 방법입니다. 가상 네트워크 서비스 엔드포인트는 직접 연결을 통해 가상 네트워크 IP 주소 공간 및 서비스에 대한 가상 네트워크의 ID를 확장합니다. 가상 네트워크에서 Azure 서비스로의 트래픽은 항상 Azure 백본 네트워크 내에서 유지됩니다. 

서비스에 대한 서비스 엔드포인트를 사용하도록 설정하면 서비스에서 노출하는 정책을 사용하여 서비스에 대한 연결을 해당 가상 네트워크로 제한합니다. 액세스 검사는 Azure 서비스에서 플랫폼에 적용합니다. 잠긴 리소스에 대한 액세스는 요청이 허용된 가상 네트워크 또는 서브넷에서 발생하거나 ExpressRoute를 사용할 때 온-프레미스 트래픽을 식별하는 데 사용되는 두 IP에서 발생한 경우에만 부여됩니다. 이 방법을 사용하여 인바운드/아웃바운드 트래픽이 PaaS 서비스에서 직접 나가지 않도록 효과적으로 방지합니다.

![서비스 엔드포인트 개요](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>네트워크 상황 인식을 위해 클라우드 네이티브 도구

Azure는 네트워크 트래픽 흐름을 파악하는 데 필요한 상황 인식을 제공하는 데 도움이 되는 클라우드 네이티브 도구를 제공합니다. 이 도구는 TIC 정책을 준수하는 데 필요하지 않으며, 보안 기능을 크게 향상시킬 수 있습니다.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md)는 규정 준수 이니셔티브를 감사하고 적용할 수 있는 더 나은 기능을 조직에 제공하는 Azure 서비스입니다. 고객은 이제 Azure Policy 규칙을 계획하고 테스트하여 미래의 TIC 준수를 보장할 수 있습니다.

Azure Policy는 구독 수준을 대상으로 합니다. 이 서비스는 다음과 같은 규정 준수 작업을 수행할 수 있는 중앙 집중식 인터페이스를 제공합니다.
- 이니셔티브 관리
- 정책 정의 구성
- 규정 준수 감사
- 규정 준수 적용
- 예외 관리

관리자는 다양한 기본 제공 정의와 함께 간단한 JSON 템플릿을 사용하여 사용자 고유의 사용자 지정 정의를 정의할 수 있습니다. 가능한 경우 적용보다 감사의 우선 순위가 높은 것이 좋습니다.

TIC 준수 시나리오에 사용할 수 있는 샘플 정책은 다음과 같습니다.

|정책  |샘플 시나리오  |Template  |
|---------|---------|---------|
|사용자 정의 경로 테이블 적용 | 모든 가상 네트워크의 기본 경로가 온-프레미스로 라우팅되도록 승인된 가상 네트워크 게이트웨이를 가리키는지 확인합니다.    | [이 템플릿](../../governance/policy/samples/no-user-defined-route-table.md)으로 시작합니다. |
|지역에 대해 Network Watcher를 사용하도록 설정되지 않은 경우 감사  | 사용된 모든 지역에 대해 Network Watcher를 사용하도록 설정되어 있는지 확인합니다.  | [이 템플릿](../../governance/policy/samples/network-watcher-not-enabled.md)으로 시작합니다. |
|모든 서브넷에서 NSG x  | 인터넷 트래픽이 차단된 NSG(또는 승인된 NSG 집합)가 모든 가상 네트워크의 모든 서브넷에 적용되어 있는지 확인합니다. | [이 템플릿](../../governance/policy/samples/nsg-on-subnet.md)으로 시작합니다. |
|모든 NIC에서 NSG x | 인터넷 트래픽이 차단된 NSG가 모든 가상 머신의 모든 NIC에 적용되어 있는지 확인합니다. | [이 템플릿](../../governance/policy/samples/nsg-on-nic.md)으로 시작합니다. |
|가상 머신 네트워크 인터페이스에 승인된 가상 네트워크 사용  | 모든 NIC가 승인된 가상 네트워크에 있는지 확인합니다. | [이 템플릿](../../governance/policy/samples/use-approved-vnet-vm-nics.md)으로 시작합니다. |
|허용되는 위치 | 모든 리소스가 규정 준수 가상 네트워크 및 Network Watcher 구성이 있는 지역에 적용되어 있는지 확인합니다.  | [이 템플릿](../../governance/policy/samples/allowed-locations.md)으로 시작합니다. |
|허용되지 않는 리소스 종류(예: **PublicIPs**) | 규정 준수 계획이 없는 리소스 종류의 배포를 금지합니다. 공용 IP 주소 리소스의 배포를 금지하려면 이 정책을 사용합니다. NSG 규칙을 사용하여 인바운드 인터넷 트래픽을 효과적으로 차단할 수 있지만, 공용 IP를 사용하지 않도록 하면 공격 표면이 훨씬 줄어듭니다.   | [이 템플릿](../../governance/policy/samples/not-allowed-resource-types.md)으로 시작합니다.  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher 트래픽 분석

Network Watcher [트래픽 분석](https://azure.microsoft.com/blog/traffic-analytics-in-preview/)은 흐름 로그 데이터 및 기타 로그를 사용하여 네트워크 트래픽에 대한 개괄적인 개요를 제공합니다. 이 데이터는 TIC 준수를 감사하고 문제 지점을 식별하는 데 유용합니다. 고급 대시보드를 사용하여 인터넷과 통신하는 가상 머신을 빠르게 화면에 표시하고 TIC 라우팅에 초점을 맞춘 목록을 얻을 수 있습니다.

![트래픽 분석](media/tic-traffic-analytics-1.png)

**지역 지도**를 사용하여 가상 머신에 필요한 인터넷 트래픽의 물리적 대상을 빠르게 식별합니다. 의심스러운 위치 또는 패턴 변경을 식별하고 심사할 수 있습니다.

![지역 지도](media/tic-traffic-analytics-2.png)

**가상 네트워크 토폴로지**를 사용하여 기존 가상 네트워크를 빠르게 조사합니다.

![네트워크 토폴로지 맵](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher 다음 홉 테스트

Network Watcher에서 모니터링하는 지역의 네트워크는 다음 홉 테스트를 수행할 수 있습니다. Azure Portal에서 Network Watcher의 샘플 네트워크 흐름에 대한 원본과 대상을 입력하여 다음 홉 대상을 확인할 수 있습니다. 가상 머신 및 샘플 인터넷 주소에 대해 이 테스트를 실행하여 다음 홉 대상이 필요한 네트워크 가상 게이트웨이인지 확인합니다.

![다음 홉 테스트](media/tic-network-watcher.png)

## <a name="conclusions"></a>결론

Microsoft Azure, Office 365 및 Dynamics 365에 대한 액세스를 쉽게 구성하여 2018년 5월에 작성하고 정의된 TIC 2.0 부록 H 지침을 준수할 수 있습니다. Microsoft는 TIC 지침이 변경될 수 있음을 인식하고 있습니다. Microsoft는 새 지침이 발표될 때마다 고객이 적시에 지침을 준수할 수 있도록 지원하기 위해 노력하고 있습니다.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>부록: 일반적인 워크로드에 대한 TIC(Trusted Internet Connections) 패턴

| Category | 워크로드 | IaaS | 전용 PaaS/가상 네트워크 삽입  | 서비스 엔드포인트  |
|---------|---------|---------|---------|--------|
| 컴퓨팅 | Azure Linux 가상 머신 | 예 | | |
| 컴퓨팅 | Azure Windows 가상 머신 | 예 | | |
| 컴퓨팅 | 가상 머신 크기 집합 | 예 | | |
| 컴퓨팅 | Azure 기능 | | App Service 환경 | |
| 웹 및 모바일 | 내부 웹 애플리케이션 | | App Service 환경| |
| 웹 및 모바일 | 내부 모바일 애플리케이션 | | App Service 환경 | |
| 웹 및 모바일 | API 애플리케이션 | | App Service 환경 | |
| 컨테이너 | Azure Container Service | | | 예 |
| 컨테이너 | AKS(Azure Kubernetes Service) \* | | | 예 |
| 데이터베이스 | Azure SQL Database | | Azure SQL Database Managed Instance \* | Azure SQL |
| 데이터베이스 | Azure Database for MySQL | | | 예 |
| 데이터베이스 | Azure Database for PostgreSQL | | | 예 |
| 데이터베이스 | Azure SQL Data Warehouse | | | 예 |
| 데이터베이스 | Azure Cosmos DB | | | 예 |
| 데이터베이스 | Azure Cache for Redis | | 예 | |
| Storage | Linux에서 File Storage 사용에 대한 자세한 내용은 Linux에서 Azure 파일 스토리지 사용 방법을 참조하세요. | 예 | | |
| Storage | Azure 파일 | 예 | | |
| Storage | Azure Queue 저장소 | 예 | | |
| Storage | Azure Table Storage | 예 | | |
| Storage | Azure Disk Storage | 예 | | |

\* Azure Government의 공개 미리 보기, 2018년 5월 기준
