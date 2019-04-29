---
title: Azure 보안 및 규정 준수 청사진 - Australia PROTECTED용 PaaS 웹 애플리케이션
description: Azure 보안 및 규정 준수 청사진 - Australia PROTECTED용 PaaS 웹 애플리케이션
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: c17f16ce796c9f296facd69c18de4effc7ff5258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610152"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure 보안 및 규정 준수 청사진 - Australia PROTECTED용 PaaS 웹 애플리케이션

## <a name="overview"></a>개요

이 Azure 보안 및 규정 준수 청사진은 ASD(Australian Signals Directorate)에서 제작한 호주 정부 ISM(정보 보안 매뉴얼)의 목표에 부합하는 AU-PROTECTED 정부 데이터의 수집, 저장, 검색에 적합한 PaaS(서비스로서의 플랫폼) 환경 배포에 대한 지침을 제공합니다. 이 청사진은 공통 참조 아키텍처를 소개하며, 안전하고 규정을 준수하는 다중 계층 환경에서 민감한 정부 데이터의 적절한 처리 방법을 보여주는 데 도움이 됩니다.

이 참조 아키텍처, 구현 가이드 및 위협 모델은 고객이 계획과 시스템 승인 프로세스를 수행할 수 있는 기반을 마련해 주어, ASD를 준수하는 방식으로 Azure에 워크로드를 배포하도록 도와줍니다. 고객은 페더레이션된 서비스를 사용하도록 Azure VPN Gateway 또는 ExpressRoute를 구현하고, Azure 리소스와 온-프레미스 리소스를 통합하도록 선택할 수 있습니다. 고객의 온-프레미스 리소스 사용 시 보안 문제를 고려해야 합니다. 각 고객의 구현 세부 사항에 따른 요구 사항을 충족하려면 추가 구성이 필요합니다.

ASD 규정 준수를 구현하려면 정보 보안 등록 평가자가 시스템을 감사해야 합니다. 요구 사항이 각 고객의 특정 구현에 따라 달라질 수 있으므로 고객은 이 아키텍처를 사용하여 구축된 솔루션에 대한 적절한 보안 및 규정 준수 평가를 수행해야 합니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
이 솔루션은 Azure SQL Database 백 엔드가 있는 PaaS 웹 애플리케이션에 대한 참조 아키텍처를 제공합니다. 웹 애플리케이션은 Azure 데이터 센터의 개인 전용 환경인 격리된 Azure App Service Environment에서 호스팅됩니다. 환경은 Azure에서 관리되는 가상 머신 간에 웹 애플리케이션에 대한 트래픽의 부하를 분산합니다. 모든 웹 애플리케이션 연결에는 최소 1.2 버전의 TLS가 필요합니다. 이 아키텍처에는 네트워크 보안 그룹, Application Gateway, Azure DNS 및 Load Balancer도 포함됩니다.

아키텍처는 온-프레미스 네트워크를 Azure 허용 웹 기반 작업으로 확장하는 보안 하이브리드 환경을 제공하여 조직의 비공개 로컬 영역 네트워크나 인터넷을 통해 안전하게 액세스할 수 있게 됩니다. 온-프레미스 솔루션의 경우 고객이 보안, 작업 및 규정 준수의 모든 측면을 담당합니다.

이 솔루션에 포함된 Azure 리소스는 VPN Gateway를 사용하는 IPSec VPN 및 ExpressRoute를 통해 온-프레미스 네트워크 또는 데이터 센터 코로케이션 시설(예: 캔버라의 CDC)에 연결할 수 있습니다. VPN 활용을 결정할 때는 전송되는 데이터의 등급과 네트워크 경로를 염두에 두어야 합니다. 빅 데이터 요구 사항을 포함하는 대규모의 중요 업무용 워크로드를 실행하는 고객은 Azure 서비스에 대한 개인 네트워크 연결에 ExpressRoute를 사용하는 하이브리드 네트워크 아키텍처를 사용하는 것이 좋습니다. Azure에 연결하는 메커니즘에 대한 자세한 내용은 [지침 및 권장 사항](#guidance-and-recommendations) 섹션을 참조하세요.

사용자가 온-프레미스 자격 증명을 사용하여 인증하고, 온-프레미스 Active Directory Federation Services 인프라를 사용하여 클라우드의 모든 리소스에 액세스하도록 하려면 Azure Active Directory와의 페더레이션을 사용해야 합니다. Active Directory Federation Services는 이러한 하이브리드 환경에 간소화된 보안 ID 페더레이션 및 웹 Single Sign-On 기능을 제공할 수 있습니다. Azure Active Directory 설정에 대한 자세한 내용은 [지침 및 권장 사항](#guidance-and-recommendations) 섹션을 참조하세요.

솔루션은 고객이 미사용 데이터의 기밀성을 유지하려면 스토리지 서비스 암호화를 사용하도록 구성할 수 있는 Azure Storage 계정을 사용합니다. Azure는 데이터 복원력을 위해 고객이 선택한 지역 내에 데이터 복사본 3부를 저장합니다. Azure 지역은 탄력적인 지역 쌍에 배포되며, 지리적 중복 저장소는 복사본 3부와 함께 두 번째 지역에 복제됩니다. 따라서 고객의 주 데이터 위치에서 데이터 손실을 유발하는 부정적인 이벤트를 방지합니다.

보안 향상을 위해 이 솔루션의 모든 Azure 리소스는 Azure Resource Manager를 통해 리소스 그룹으로 관리됩니다. Azure Active Directory 역할 기반 액세스 제어는 Azure Key Vault의 키 및 배포된 리소스에 대한 액세스를 제어하는 데 사용됩니다. 시스템 상태는 Azure Security Center 및 Azure Monitor를 통해 모니터링됩니다. 고객은 로그를 캡처하고 쉽게 탐색할 수 있는 단일 대시보드에 시스템 상태를 표시하도록 두 모니터링 서비스를 구성합니다. Azure Application Gateway는 방지 모드에서 방화벽으로 구성되고 TLS v1.2 이상이 아닌 트래픽을 허용하지 않습니다. 솔루션은 비다중 테넌트 환경에서 웹 계층을 격리하기 위해 Azure Application Service Environment v2를 사용합니다.

![용 PaaS 웹 애플리케이션 참조 아키텍처](images/au-protected-paaswa-architecture.png?raw=true "AU-PROTECTED용 PaaS 웹 애플리케이션 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 자세한 내용은 [배포 아키텍처](#deployment-architecture) 섹션에 나와 있습니다.

- Application Gateway
    - 웹 애플리케이션 방화벽
        - 방화벽 모드: 방지
        - 규칙 집합: OWASP
        - 수신기 포트: 443
- Application Insights
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure 리소스 관리자
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Monitor 로그
- Azure Virtual Network
    - (1) /16 Network
    - (4) /24 네트워크
    - 네트워크 보안 그룹
- 네트워크 보안 그룹
- Recovery Services 자격 증명 모음
- Azure 웹앱

이 청사진에는 ACSC(Australian Cyber Security Centre)에서 Protected 등급에서 사용을 인증하지 않은 Azure 서비스가 포함되어 있습니다. Microsoft에서는 이러한 Azure 서비스와 관련하여 게시된 보안 및 감사 보고서를 살펴보고, 위험 관리 프레임워크를 사용하여 해당 Azure 서비스가 내부 승인에 적합하고 Protected 등급에서 사용하기 적합한지 확인할 것을 권장합니다.

## <a name="deployment-architecture"></a>배포 아키텍처
다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 사용하면 고객이 솔루션의 리소스를 그룹으로 사용할 수 있습니다. 고객이 조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 고객이 배포용 템플릿을 사용하고, 이 템플릿은 테스트, 준비 및 프로덕션과 같은 다양한 환경에서 사용할 수 있습니다. Resource Manager는 보안, 감사 및 태그 지정 기능을 제공하여 고객이 배포 후에 리소스를 관리할 수 있도록 지원합니다.

**요새 호스트**: 요새 호스트는 사용자가 이 환경에서 배포된 리소스에 액세스할 수 있도록 하는 단일 진입점입니다. 요새 호스트는 안전한 목록에 있는 공용 IP 주소의 원격 트래픽만 허용하여 배포된 리소스에 대한 보안 연결을 제공합니다. RDP(원격 데스크톱 프로토콜) 트래픽을 허용하려면 트래픽의 원본을 네트워크 보안 그룹에서 정의해야 합니다.

이 솔루션은 다음 구성을 사용하여 가상 머신을 도메인에 조인된 요새 호스트로 만듭니다.
-   [맬웨어 방지 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure 진단 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault를 사용하는 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   사용하지 않을 때 가상 머신 사용을 줄이는 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)

**App Service Environment v2**: [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)는 App Service 애플리케이션을 대규모로 안전하게 실행하기 위한 완전히 격리된 전용 환경을 제공하는 App Service 기능입니다.

App Service Environment는 단일 고객의 애플리케이션만 실행하도록 격리되며 항상 가상 네트워크에 배포됩니다. 고객은 인바운드 및 아웃바운드 애플리케이션 네트워크 트래픽 둘 다에 대해 세밀하게 제어할 수 있고 애플리케이션은 가상 네트워크를 통해 온-프레미스 회사 리소스에 고속 보안 연결을 설정할 수 있습니다.

이 아키텍처에 대한 App Service Environment 사용은 다음 제어/구성에 대해 허용됩니다.

- App Service Environment는 격리 서비스 요금제를 사용하도록 구성해야 함
    - 애플리케이션 분류별로 다른 App Service Environment 구성
- 보안된 Azure 가상 네트워크에서 호스트 및 네트워크 보안 규칙
- HTTPS 통신을 위해 자체 서명된 내부 부하 분산 장치 인증서로 구성된 App Service Environment. 모범 사례로, Microsoft에서는 보안 강화를 위해 신뢰할 수 있는 인증 기관을 사용하는 것을 권장합니다.
- [내부 부하 분산 모드](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)(모드 3)
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) 사용 안 함
- [TLS 암호화](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) 변경
- [인바운드 트래픽 N/W 포트](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 제어
- [웹 애플리케이션 방화벽 – 데이터 제한](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Azure SQL Database 트래픽](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) 허용

**Azure Web App**: [Azure App Service](https://docs.microsoft.com/azure/app-service/)를 사용하면 고객이 인프라를 관리할 필요 없이 선택한 프로그래밍 언어로 웹 애플리케이션을 빌드하고 호스트할 수 있습니다. 여기서는 자동 크기 조정 및 고가용성을 제공하고, Windows 및 Linux를 모두 지원하며, GitHub, Azure DevOps Services 또는 Git 리포지토리에서 자동화된 배포를 사용합니다.

### <a name="virtual-network"></a>Virtual Network
아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 가상 네트워크 내에서 트래픽을 허용하거나 거부하는 액세스 제어 목록이 포함됩니다. 네트워크 보안 그룹은 서브넷 또는 개별 가상 머신 수준에서 트래픽을 보호하는 데 사용될 수 있습니다. 다음과 같은 네트워크 보안 그룹이 있습니다.
- Application Gateway용 네트워크 보안 그룹 1개
- App Service Environment용 네트워크 보안 그룹 1개
- Azure SQL Database용 네트워크 보안 그룹 1개
- 요새 호스트용 네트워크 보안 그룹 1개

각 네트워크 보안 그룹에는 솔루션이 안전하고 올바르게 작동할 수 있도록 특정 포트 및 프로토콜이 열려 있습니다. 또한 각 네트워크 보안 그룹에 대해 다음과 같은 구성을 사용합니다.

  - [진단 로그 및 이벤트](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)는 저장소 계정에 사용되고 저장됩니다.
  - Azure Monitor 로그에 연결 되는 [네트워크 보안 그룹 진단](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**서브넷**: 각 서브넷이 해당 네트워크 보안 그룹에 연결됩니다.

**Azure DNS**: Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)는 Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에서 도메인을 호스팅하면 사용자가 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 또한 Azure DNS는 사설 DNS 도메인도 지원합니다.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)를 사용하면 고객이 애플리케이션의 크기를 조정하고 고가용성 서비스를 만들 수 있습니다. Load Balancer는 인바운드 및 아웃바운드 시나리오를 지원하며, 짧은 대기 시간과 높은 처리량을 제공하고, 모든 TCP 및 UDP 애플리케이션에 대해 수백만 개의 흐름으로 확장됩니다.

### <a name="data-in-transit"></a>전송 중 데이터
Azure는 기본적으로 Azure 데이터 센터와의 모든 통신을 암호화합니다. 

이 아키텍처는 고객 소유의 네트워크에서 전송되는 Protected 데이터에 IPSEC가 구성된 VPN Gateway를 사용하는 인터넷 또는 ExpressRoute를 사용합니다.

또한 Azure 관리 포털을 통해 Azure에 대 한 모든 트랜잭션을 TLS v1.2를 활용 하 여 HTTPS를 통해 발생 합니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 암호화, 데이터베이스 감사 및 다른 방법을 통해 미사용 데이터를 보호합니다.

**Azure Storage**: 암호화된 미사용 데이터 요구 사항에 부합하기 위해 모든 [Azure Storage](https://azure.microsoft.com/services/storage/)는 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다. 이렇게 하면 호주 정부 ISM에서 정의한 조직의 보안 약정 및 준수 요구 사항을 지지하는 데이터를 안전하게 보호할 수 있습니다.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

**Azure SQL Database**: Azure SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.
-   [Active Directory 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
-   [SQL 데이터베이스 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
-   Azure SQL Database는 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하여 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 해독을 수행하고 미사용 정보를 보호하도록 구성됩니다. 투명한 데이터 암호화는 저장된 데이터가 무단으로 액세스되지 못하게 합니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다. SQL Threat Detection은 의심스러운 활동에 대한 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 작업을 포함한 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다.
-   [Always Encrypted 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않게 합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 권한이 없는 사용자 또는 응용 프로그램에 데이터를 마스킹하여 중요한 데이터의 노출을 제한합니다. 동적 데이터 마스킹은 잠재적으로 중요한 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 이렇게 하면 중요한 데이터가 무단 액세스를 통해 데이터베이스에서 나가지 않도록 액세스를 줄일 수 있습니다. 고객은 데이터베이스 스키마를 준수하도록 동적 데이터 마스킹 설정을 조정해야 합니다.

### <a name="identity-management"></a>ID 관리
고객은 온-프레미스 Active Directory Federated Services를 활용하여 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스인 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)와 페더레이션할 수 있습니다. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)는 Azure Active Directory와 온-프레미스 디렉터리를 통합합니다. Azure SQL Database에 액세스하는 사용자를 포함한 이 솔루션의 모든 사용자는 Azure Active Directory 계정이 필요합니다. 사용자는 페더레이션 로그인을 사용하여 Azure Active Directory에 로그인하고 온-프레미스 자격 증명을 사용하여 Azure 리소스에 인증할 수 있습니다.

또한 다음과 같은 Azure Active Directory 기능은 Azure 환경의 데이터에 대한 액세스를 관리하는 데 도움이 됩니다.
- 애플리케이션에 대한 인증은 Azure Active Directory를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화는 Azure Active Directory를 사용하여 Azure SQL Database에 애플리케이션을 인증합니다. 자세한 내용은 [Azure SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
- [Azure 역할 기반 액세스 제어](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하면 고객이 특정 정보에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미치는 잠재적 취약성을 탐지하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대응을 구성하며, 의심스러운 인시던트를 조사하여 이를 해결하기 위한 적절한 조치를 수행합니다.

**Azure Multi-Factor Authentication**: ID를 보호하려면 다단계 인증을 구현해야 합니다. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)은 인증의 두 번째 메서드를 제공하여 사용자를 보호하는 사용하기 쉽고 확장 가능한 신뢰할 수 있는 솔루션입니다. Azure Multi-Factor Authentication은 클라우드의 강력한 기능을 이용하며 온-프레미스 Active Directory와 사용자 지정 애플리케이션을 통합합니다. 이러한 보호는 고용량 업무상 중요한 시나리오에 확장됩니다.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. 고객은 다음 Azure Key Vault 기능을 통해 데이터를 보호할 수 있습니다.
- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 하드웨어 보안 모듈로 보호되는 2,048비트 RSA 키입니다.
- 모든 사용자 및 ID는 역할 기반 액세스 제어를 사용하여 최소 필수 사용 권한을 부여받습니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

**Azure Security Center**: 고객은 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하고, 공격을 검색하여 대응할 수 있습니다. 또한 Azure Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 상태를 개선하고 데이터를 보호하는 데 유용한 구성과 서비스 권장 사항을 제공합니다.

Azure Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Azure Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 [미리 정의된 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) 집합이 있습니다. Azure Security Center에서 [사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)을 사용하면 고객은 자신의 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.

Azure Security Center에서는 우선 순위가 지정된 보안 경고 및 인시던트를 제공하여 고객이 잠재적인 보안 문제를 더 쉽게 검색하고 해결하게 합니다. [위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)는 인시던트 대응 팀이 위협을 조사하고 수정하도록 지원하기 위해 탐지된 각 위협에 대해 생성됩니다.

**Application Gateway**: 아키텍처는 웹 애플리케이션 방화벽과 OWASP 규칙 집합을 사용하는 Application Gateway를 통해 보안상 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [웹 응용 프로그램 방화벽](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)(방지 모드)
- [방지 모드](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용
- [진단 로깅](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) 사용
- [사용자 지정 상태 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) 및 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)는 추가적인 보호 및 알림을 제공합니다. Azure Security Center는 평판 시스템도 제공합니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Storage 로그, Key Vault 감사 로그 및 Application Gateway 액세스 및 방화벽 로그가 포함됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다.

**Azure Monitor 로그**: 이러한 로그에 통합 됩니다 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 처리, 저장 및 대시보드를 보고 합니다. 수집이 완료되면 데이터는 각 데이터 형식에 대해 별도 테이블로 구성되어 원본에 관계 없이 모든 데이터가 함께 분석되도록 합니다. 또한 Azure Security Center는 Kusto 쿼리를 사용 하 여 보안 이벤트 데이터를 액세스 하 고 다른 서비스의 데이터와 결합 하 여 고객은 Azure Monitor 로그와 통합 됩니다.

다음 Azure [모니터링 솔루션](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 이 아키텍처의 일부로 포함 됩니다.
-   [Active Directory 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
- [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
- [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포되는 에이전트 수와 해당 지리적 분포, 응답이 없는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
-   [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 구독에서 활동 로그를 분석하는 데 도움을 줍니다.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)은 Runbook을 저장, 실행 및 관리합니다. 이 솔루션에서 Runbook은 Azure SQL Database에서 로그를 수집하는 데 도움이 됩니다. Automation [변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking) 솔루션을 사용하면 고객이 환경의 변경 내용을 쉽게 파악할 수 있습니다.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 사용하면 사용자의 Azure 리소스에서 API 호출을 추적하는 것을 포함하여 조직에서 감사, 경고 만들기 및 데이터 보관을 수행할 수 있도록 하여 사용자가 성능을 추적하고, 보안을 유지하고, 추세를 식별할 수 있습니다.

Azure Network Watcher: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고 메트릭을 확인하고 로그를 활성화 또는 비활성화할 수 있는 도구를 제공합니다.  연방 엔터티는 NSG 및 Virtual Machines에 대한 Network Watcher 흐름 로그를 구현해야 합니다. 이러한 로그는 보안 로그만 저장되는 전용 스토리지 계정에 저장되어야 하며, 스토리지 계정에 대한 액세스는 역할 기반 액세스 제어로 보호되어야 합니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 [다운로드](https://aka.ms/au-protected-paaswa-tm)에서 사용할 수 있거나 아래에 나와 있습니다. 이 모델을 사용하면 고객이 수정할 때 발생할 수 있는 시스템 인프라의 잠재적 위험 요소를 파악할 수 있습니다.

![AU-PROTECTED용 PaaS 웹 애플리케이션 위협 모델](images/au-protected-paaswa-threat-model.png?raw=true "AU-PROTECTED용 PaaS 웹 애플리케이션 위협 모델 다이어그램")

## <a name="compliance-documentation"></a>규정 준수 설명서
이 규정 준수 설명서는 Microsoft에서 제공하는 플랫폼과 서비스를 기반으로 제작한 것입니다. 고객 배포 환경이 매우 다양하기 때문에 이 문서에서는 Azure 환경에서만 호스팅되는 솔루션에 맞게 일반화된 접근법을 제공합니다. 고객은 자신의 고유한 운영 환경과 비즈니스 성과에 따라 대체 제품과 서비스를 식별하여 사용할 수 있습니다. 온-프레미스 리소스를 사용하도록 선택하는 고객은 이러한 온-프레미스 리소스의 보안 및 운영을 처리해야 합니다. 고객은 특정 온-프레미스 및 보안 요구 사항을 해결하기 위해 문서화된 솔루션을 사용자 지정할 수 있습니다.

[Azure 보안 및 규정 준수 청사진 - AU-Prot 고객 책임 매트릭스](https://aka.ms/au-protected-crm)에는 AU-PROTECTED에서 요구하는 모든 보안 제어가 나와 있습니다. 이 매트릭스는 각 제어의 구현이 Microsoft, 고객 또는 둘 모두의 책임인지 여부를 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - AU-PROTECTED PaaS 웹 애플리케이션 구현 매트릭스](https://aka.ms/au-protected-paaswa-cim)는 구현이 적용된 각 제어의 요구 사항을 충족시키는 방법에 대한 자세한 설명을 포함하여 PaaS 웹 애플리케이션 아키텍처에서 처리되는 AU-PROTECTED 제어에 대한 정보를 제공합니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항
### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute

분류된 정보의 경우 이 IaaS 웹 애플리케이션 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 IPSec VPN 터널을 구성해야 합니다. IPSec VPN을 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 레이어를 추가할 수 있습니다.

Azure를 통해 보안 IPSec VPN 터널을 구현하면 온-프레미스 네트워크와 Azure 가상 네트워크 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객이 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. 

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 비공개 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 링크이며 비공개 네트워크로 간주됩니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도 및 짧은 대기 시간을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

분류된 데이터를 보호하려면 인터넷을 사용하든 Azure ExpressRoute를 사용하든 관계없이 다음 설정을 적용하여 IPSec VPN을 구성해야 합니다.

•   고객 VPN 개시 장치에 14400초 이하의 SA 수명을 구성해야 합니다.
•   고객 VPN 개시 장치는 IKEv1 적극적인 모드를 사용하지 않아야 합니다.
•   고객 VPN 개시 장치는 PFS(Perfect Forward Secrecy)를 구성해야 합니다.
•   고객 VPN 개시 장치는 HMAC-SHA256 이상을 사용하도록 구성해야 합니다.

VPN 디바이스 및 IPSec/IKE 매개 변수의 구성 옵션을 [검토](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)할 수 있습니다.

### <a name="azure-active-directory-setup"></a>Azure Active Directory 설정
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 환경과 상호 작용하는 직원에 대한 배포 및 프로비전 액세스를 관리하는 데 필수적입니다. 기존의 Windows Server Active Directory는 [네 번의 클릭](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)으로 Azure Active Directory와 통합될 수 있습니다.

또한 [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)를 통해 온-프레미스 [Active Directory Federation Services]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) 및 Azure Active Directory와 페더레이션을 구성할 수 있습니다. 페더레이션 로그인에서, 사용자가 자신의 온-프레미스 암호로 Azure Active Directory 기반 서비스에 로그인 하고 자신의 암호를 다시 입력하지 않고도 회사 네트워크에 로그인하도록 설정할 수 있습니다. Active Directory Federation Services와 페더레이션 옵션을 사용하여 Active Directory Federation Services의 새 설치를 배포하거나 Windows Server 2012 R2 팜에 기존 설치를 지정할 수 있습니다.

고객은 분류된 데이터가 Azure Active Directory에 동기화되지 않도록 Azure Active Directory Connect의 다음 설정을 적용하여 Azure Active Directory에 복제되는 특성을 제한할 수 있습니다.

- [필터링 사용](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [암호 해시 동기화 사용 안 함](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [비밀번호 쓰기 저장 사용 안 함](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [디바이스 쓰기 저장 사용 안 함](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   [실수로 인한 삭제 방지](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) 및 [자동 업그레이드](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)의 기본 설정을 그대로 둡니다.


## <a name="disclaimer"></a>고지 사항

 - 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
 - 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
 - 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
 - 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
 - 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
 - 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
