---
title: Azure 보안 및 규정 준수 청사진 - UK NHS에 대한 PaaS 웹 애플리케이션
description: Azure 보안 및 규정 준수 청사진 - UK NHS에 대한 PaaS 웹 애플리케이션
services: security
author: jomolesk
ms.assetid: fe409add-6d09-4062-b3c8-d74574747739
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 8af6998c8e9fbfb230c00597600b3aa4f3991f03
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112581"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-uk-nhs"></a>Azure 보안 및 규정 준수 청사진: UK NHS용 PaaS 웹 애플리케이션

## <a name="overview"></a>개요

이 Azure 보안 및 규정 준수 청사진은 의료 데이터의 수집, 저장 및 검색에 적합한 PaaS(서비스 제공 플랫폼) 솔루션에 대한 참조 아키텍처 및 지침을 제공합니다. 이 솔루션에는 영국 보건사회복지부(DHSC)의 파트너인 [NHS Digital](https://digital.nhs.uk/)에서 게시한 [클라우드 보안 모범 사례 가이드](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide)에 제공된 지침을 고객이 준수할 수 있는 방법을 보여줍니다. 클라우드 보안 모범 사례 가이드는 영국 사이버보안센터(NCSC)에서 게시한 14가지 [클라우드 보안 원칙](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)에 기반하고 있습니다.

이 참조 아키텍처, 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이 되며, 프로덕션 환경에서 추가 구성 없이 있는 그대로 사용하면 안됩니다. 요구 사항이 각 고객의 특정 구현에 따라 달라질 수 있으므로 고객은 이 아키텍처를 사용하여 구축된 솔루션에 대한 적절한 보안 및 규정 준수 평가를 수행해야 합니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소

이 솔루션은 Azure SQL Database 백 엔드가 있는 PaaS 웹 애플리케이션에 대한 참조 아키텍처를 제공합니다. 웹 애플리케이션은 Azure 데이터 센터의 개인 전용 환경인 격리된 Azure App Service Environment에서 호스팅됩니다. 환경은 Azure에서 관리되는 가상 머신 간에 웹 애플리케이션에 대한 트래픽의 부하를 분산합니다. 모든 외부 연결에는 TLSv1.2가 필요합니다. 이 아키텍처에는 네트워크 보안 그룹, Application Gateway, Azure DNS 및 Load Balancer도 포함됩니다.

솔루션은 고객이 미사용 데이터의 기밀성을 유지하려면 스토리지 서비스 암호화를 사용하도록 구성할 수 있는 Azure Storage 계정을 사용합니다. Azure는 데이터 복원력을 위해 고객이 선택한 데이터 센터 내에 데이터 복사본 3부를 저장합니다. 지리적 중복 저장소는 수백 킬로미터 떨어진 보조 데이터 센터에 데이터가 복제되고 해당 데이터 센터 내에 복사본 3부가 다시 저장되는지 확인하여 고객의 기본 데이터 센터에서 데이터 손실로 인한 부정적인 이벤트를 방지합니다.

보안 향상을 위해 이 솔루션의 모든 리소스는 Azure Resource Manager를 통해 리소스 그룹으로 관리됩니다. Azure Active Directory 역할 기반 액세스 제어는 Azure Key Vault의 키 및 배포된 리소스에 대한 액세스를 제어하는 데 사용됩니다. 시스템 상태는 Azure Security Center 및 Azure Monitor를 통해 모니터링됩니다. 고객은 로그를 캡처하고 쉽게 탐색할 수 있는 단일 대시보드에 시스템 상태를 표시하도록 두 모니터링 서비스를 구성합니다. Azure Application Gateway는 방지 모드에서 방화벽으로 구성되고 TLSv1.2가 아닌 트래픽을 허용하지 않습니다. 솔루션은 비다중 테넌트 환경에서 웹 계층을 격리하기 위해 Azure Application Service Environment v2를 사용합니다.

**Microsoft에서는 참조 아키텍처 서브넷에 데이터 가져오기 및 관리를 위해 VPN 또는 ExpressRoute 연결을 구성하는 것을 권장합니다.**

![UK NHS용 PaaS 웹 애플리케이션 참조 아키텍처 다이어그램](images/uknhs-paaswa-architecture.png?raw=true "UK NHS용 PaaS 웹 애플리케이션 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처에 대한 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

- Application Gateway
    - 웹 애플리케이션 방화벽
        - 방화벽 모드: 방지
        - 규칙 집합: OWASP
        - 수신기 포트: 443
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
- Azure Virtual Network
    - 네트워크 보안 그룹
- Azure 웹앱

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 사용하면 고객이 솔루션의 리소스를 그룹으로 사용할 수 있습니다. 고객이 조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 고객이 배포용 템플릿을 사용하고, 이 템플릿은 테스트, 준비 및 프로덕션과 같은 다양한 환경에서 사용할 수 있습니다. Resource Manager는 보안, 감사 및 태그 지정 기능을 제공하여 고객이 배포 후에 리소스를 관리할 수 있도록 지원합니다.

**App Service Environment v2**: Azure App Service Environment는 App Service 애플리케이션을 대규모로 안전하게 실행하기 위한 완전히 격리된 전용 환경을 제공하는 App Service 기능입니다.

App Service Environment는 단일 애플리케이션만 실행하도록 격리되며, 항상 가상 네트워크에 배포됩니다. 이 격리 기능을 사용하면 참조 아키텍처는 완전한 테넌트 격리를 갖게 되므로 Azure의 다중 테넌트 환경에서 제거할 수 있습니다. 이 격리 기능은 영국 NHS 원칙 3의 요구 사항을 충족해야 합니다. 고객은 인바운드 및 아웃바운드 애플리케이션 네트워크 트래픽 둘 다에 대해 세밀하게 제어할 수 있고 애플리케이션은 가상 네트워크를 통해 온-프레미스 회사 리소스에 고속 보안 연결을 설정할 수 있습니다. 고객은 부하 메트릭, 사용 가능한 예산 또는 정의된 일정에 따라 App Service Environment를 사용하여 “자동 크기 조정”을 수행할 수 있습니다.


이 아키텍처에 대한 App Service Environment 사용은 다음 제어/구성에 대해 허용됩니다.

- Azure 가상 네트워크 및 네트워크 보안 규칙 내의 호스트
- HTTPS 통신을 위해 자체 서명된 내부 부하 분산 장치 인증서. 모범 사례로서 Microsoft는 강화된 보안을 위해 신뢰할 수 있는 인증 기관 사용을 권장합니다.
- [내부 부하 분산 모드](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) 사용 안 함
- [TLS 암호화](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings) 변경
- [인바운드 트래픽 N/W 포트](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 제어
- [웹 애플리케이션 방화벽 – 데이터 제한](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [Azure SQL Database 트래픽](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) 허용

**Azure Web App**: [Azure App Service](https://docs.microsoft.com/azure/app-service/)를 사용하면 고객이 인프라를 관리할 필요 없이 선택한 프로그래밍 언어로 웹 애플리케이션을 빌드하고 호스트할 수 있습니다. 여기서는 자동 크기 조정 및 고가용성을 제공하고, Windows 및 Linux를 모두 지원하며, GitHub, Azure DevOps 또는 Git 리포지토리에서 자동화된 배포를 사용합니다.

### <a name="virtual-network"></a>Virtual Network

아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 가상 네트워크 내에서 트래픽을 허용하거나 거부하는 액세스 제어 목록이 포함됩니다. 네트워크 보안 그룹은 서브넷 또는 개별 가상 머신 수준에서 트래픽을 보호하는 데 사용될 수 있습니다. 다음과 같은 네트워크 보안 그룹이 있습니다.

- Application Gateway용 네트워크 보안 그룹 1개
- App Service Environment용 네트워크 보안 그룹 1개
- Azure SQL Database용 네트워크 보안 그룹 1개

각 네트워크 보안 그룹에는 솔루션이 안전하고 올바르게 작동할 수 있도록 특정 포트 및 프로토콜이 열려 있습니다. 또한 각 네트워크 보안 그룹에 대해 다음과 같은 구성을 사용합니다.

- [진단 로그 및 이벤트](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)는 저장소 계정에 사용되고 저장됩니다.
- Azure Monitor 로그에 연결 합니다 [네트워크 보안 그룹&#39;s 진단 로그](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**서브넷**: 각 서브넷이 해당 네트워크 보안 그룹에 연결됩니다.

**Azure DNS**: Domain Name System, 즉 DNS는 웹 사이트 또는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)는 Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에서 도메인을 호스팅하면 사용자가 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 또한 Azure DNS는 사설 DNS 도메인도 지원합니다.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)를 사용하면 고객이 애플리케이션의 크기를 조정하고 고가용성 서비스를 만들 수 있습니다. Load Balancer는 인바운드 및 아웃바운드 시나리오를 지원하며, 짧은 대기 시간과 높은 처리량을 제공하고, 모든 TCP 및 UDP 애플리케이션에 대해 수백만 개의 흐름으로 확장됩니다.

### <a name="data-in-transit"></a>전송 중 데이터

Azure는 기본적으로 Azure 데이터 센터와의 모든 통신을 암호화합니다. Azure Portal을 통한 Azure Storage에 대한 모든 트랜잭션은 HTTPS를 통해 발생합니다.

### <a name="data-at-rest"></a>미사용 데이터

이 아키텍처는 암호화, 데이터베이스 감사 및 다른 방법을 통해 미사용 데이터를 보호합니다.

**Azure Storage**: 암호화된 미사용 데이터 요구 사항에 부합하기 위해 모든 [Azure Storage](https://azure.microsoft.com/services/storage/)는 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다. 이렇게 하면 NHS Digital에서 정의한 조직의 보안 약정 및 준수 요구 사항을 지원하기 위해 데이터를 보호할 수 있습니다.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

**Azure SQL Database**: Azure SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.

- [Active Directory 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
- [SQL 데이터베이스 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
- Azure SQL Database는 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하여 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 해독을 수행하고 미사용 정보를 보호하도록 구성됩니다. 투명한 데이터 암호화는 저장된 데이터가 무단으로 액세스되지 못하게 합니다.
- [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
- [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다.
- [암호화된 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않게 합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 권한이 없는 사용자 또는 응용 프로그램에 데이터를 마스킹하여 중요한 데이터의 노출을 제한합니다. 동적 데이터 마스킹은 잠재적으로 중요한 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 이렇게 하면 무단 액세스를 통해 데이터베이스가 종료되지 않도록 데이터에 대한 액세스를 식별하고 줄일 수 있습니다. 고객은 데이터베이스 스키마를 준수하도록 동적 데이터 마스킹 설정을 조정하는 데 책임이 있습니다.

### <a name="identity-management"></a>ID 관리

Azure 환경에서 데이터에 대한 액세스를 관리하는 기능을 제공하는 기술은 다음과 같습니다.

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure SQL Database에 액세스하는 사용자를 포함하여 이 솔루션의 모든 사용자는 Azure Active Directory에서 만들어졌습니다.
- 애플리케이션에 대한 인증은 Azure Active Directory를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화는 Azure Active Directory를 사용하여 Azure SQL Database에 애플리케이션을 인증합니다. 자세한 내용은 [Azure SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
- [Azure 역할 기반 액세스 제어](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 사용하면 관리자가 세분화된 액세스 권한을 정의하여 사용자가 자신의 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 권한을 부여하는 대신 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하면 고객이 특정 정보에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure Active Directory Privileged Identity Management를 사용하여 권한 있는 ID와 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미치는 잠재적 취약성을 탐지하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대응을 구성하며, 의심스러운 인시던트를 조사하여 이를 해결하기 위한 적절한 조치를 수행합니다.

### <a name="security"></a>보안

**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault 기능을 통해 고객이 이러한 데이터를 보호하고 액세스할 수 있습니다.

- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 하드웨어 보안 모듈로 보호되는 2048비트 RSA 키입니다.
- 모든 사용자 및 ID는 역할 기반 액세스 제어를 사용하여 최소 필수 사용 권한을 부여받습니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

**Azure Security Center**: 이 솔루션은 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하며, 공격을 검색하여 대응할 수 있습니다. 또한 Azure Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 상태를 개선하고 데이터를 보호하는 데 유용한 구성과 서비스 권장 사항을 제공합니다.

Azure Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Azure Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 [미리 정의된 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) 집합이 있습니다. Azure Security Center에서 [사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)을 사용하면 고객은 자신의 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.

Azure Security Center에서는 우선 순위가 지정된 보안 경고 및 인시던트를 제공하여 잠재적인 보안 문제를 더 쉽게 검색하고 해결하게 합니다. [위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)는 인시던트 대응 팀이 위협을 조사하고 수정하도록 지원하기 위해 탐지된 각 위협에 대해 생성됩니다.

**Azure Application Gateway**: 이 아키텍처는 웹 애플리케이션 방화벽이 구성되어 있고 OWASP 규칙 집합을 사용할 수 있는 Azure Application Gateway를 통해 보안 취약성의 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

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

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 [다운로드](https://aka.ms/uknhs-paaswa-tm)에서 사용할 수 있거나 아래에 나와 있습니다. 이 모델을 사용하면 고객이 수정할 때 발생할 수 있는 시스템 인프라의 잠재적 위험 요소를 파악할 수 있습니다.

![UK NHS용 PaaS 웹 애플리케이션 위협 모델](images/uknhs-paaswa-threat-model.png?raw=true "UK NHS용 PaaS 웹 애플리케이션 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서

[Azure 보안 및 규정 준수 청사진 - 영국 NHS 고객 책임 매트릭스](https://aka.ms/uknhs-crm)는 영국 NHS의 모든 요구 사항을 나열합니다. 이 매트릭스는 각 원칙 구현이 Microsoft의 책임인지, 고객의 책임인지 아니면 둘의 공동 책임인지 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - UK NHS PaaS 웹 애플리케이션 구현 매트릭스](https://aka.ms/uknhs-paaswa-cim)는 구현이 각 대상 문서의 요구 사항을 충족시키는 방법에 대한 자세한 설명을 포함하여 PaaS 웹 애플리케이션 아키텍처에서 UK NHS 요구 사항에 대처하는 정보를 제공합니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute

이 PaaS 웹 애플리케이션 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure 가상 네트워크 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객이 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 &quot;터널링&quot;할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. [IPsec 터널 모드](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 &quot;있는 그대로&quot; 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
