---
title: Azure 보안 및 규정 준수 청사진 - FedRAMP 준수 IaaS 웹 애플리케이션
description: Azure 보안 및 규정 준수 청사진 - FedRAMP 준수 IaaS 웹 애플리케이션
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586137"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure 보안 및 규정 준수 청사진: FedRAMP용 IaaS 웹 애플리케이션

## <a name="overview"></a>개요

[FedRAMP(Federal Risk and Authorization Management Program)](https://www.fedramp.gov)는 미국 정부 차원 프로그램으로 클라우드 제품 및 서비스에 대한 보안 평가, 권한 부여 및 연속 모니터링에 대한 표준화된 접근 방법을 제공합니다. 이 Azure Security 및 Compliance Blueprint Automation에서는 간단한 인터넷 연결 웹 애플리케이션에 적합한 FedRAMP 규격 IaaS(Infrastructure as a Service) 환경의 배포를 위한 지침을 제공합니다. 이 솔루션은 공통 참조 아키텍처를 위한 Azure 리소스 배포 및 구성을 자동화하여 고객이 특정 보안 및 규정 준수 요구 사항에 부합할 수 있는 방법을 보여 주며 고객이 Azure에서 자체 솔루션을 구축 및 구성하기 위한 기초 역할을 합니다. 이 솔루션은 NIST SP 800-53을 기준으로 FedRAMP 높음 기준에서 제어의 하위 집합을 구현합니다. FedRAMP 요구 사항이 이 솔루션에 대한 자세한 내용은 [규정 준수 설명서](#compliance-documentation)를 참조하세요.
> [!NOTE]
> 이 솔루션은 Azure Government에 배포됩니다.

이 Azure Security 및 Compliance Blueprint Automation은 미리 구성된 보안 제어가 있는 IaaS 웹 애플리케이션 참조 아키텍처를 자동으로 배포하여 고객이 FedRAMP 요구 사항을 준수할 수 있게 합니다. 이 솔루션은 리소스 배포 및 구성을 안내하는 Azure Resource Manager 템플릿과 PowerShell 스크립트로 구성되었습니다.

이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다. 애플리케이션을 수정 없이 이러한 환경에 배포하는 것은 FedRAMP 높음 기준의 요구 사항을 온전히 충족하기에는 부족합니다. 다음 사항에 유의하세요.
- 이 아키텍처는 고객이 FedRAMP 규격에 맞게 Azure를 사용하기 위한 기준을 제공합니다.
- 요구 사항은 각 고객의 구현에서 특정 상황에 따라 달라질 수 있으므로 이 아키텍처를 사용하여 구축된 솔루션의 적합한 보안 및 준수 평가의 수행은 고객의 책임입니다. 

이 솔루션의 작동 방식을 간략히 살펴보려면 배포에 대해 설명하고 보여 주는 이 [비디오](https://aka.ms/fedrampblueprintvideo)를 확인하세요.

배포 지침을 보려면 [여기](https://aka.ms/fedrampblueprintrepo)를 클릭하세요.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
이 솔루션은 SQL Server 백 엔드가 있는 IaaS 웹 애플리케이션에 대한 참조 아키텍처를 배포합니다. 이 아키텍처는 웹 계층, 데이터 계층, Active Directory 인프라, Application Gateway 및 Load Balancer를 포함합니다. 웹 및 데이터 계층에 배포된 가상 머신은 가용성 집합에서 구성되며 SQL Server 인스턴스는 고가용성을 위해 AlwaysOn 가용성 그룹에 구성됩니다. Virtual Machines는 도메인에 조인되며 Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.  요새 호스트는 관리자가 배포된 리소스에 액세스할 수 있는 보안 연결을 제공합니다. **Azure에서는 관리 및 참조 아키텍처 서브넷에 데이터 가져오기를 위해 VPN 또는 Azure ExpressRoute 연결을 구성하는 것이 좋습니다.**

![FedRAMP용 IaaS 웹 애플리케이션 참조 아키텍처 다이어그램](images/fedramp-iaaswa-architecture.png?raw=true "FedRAMP용 IaaS 웹 애플리케이션 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처의 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

- Azure Virtual Machines
    - (1) 요새 호스트(Windows Server 2016 Datacenter)
    - (2) Active Directory 도메인 컨트롤러(Windows Server 2016 Datacenter)
    - (2) SQL Server 클러스터 노드(Windows Server 2016의 SQL Server 2017)
    - (2) Web/IIS(Windows Server 2016 Datacenter)
- 가용성 집합
    - (1) Active Directory 도메인 컨트롤러
    - (1) SQL 클러스터 노드
    - (1) 웹/IIS
- Azure Virtual Network
    - (1) /16 가상 네트워크
    - (5) /24 서브넷
    - DNS 설정이 모든 도메인 컨트롤러에 설정됨
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF 애플리케이션 게이트웨이 사용
        - 방화벽 모드: 방지
        - 규칙 집합: OWASP 3.0
        - 수신기: 443 포트
- Azure Storage
    - (7) 지역 중복 저장소 계정
- Azure 클라우드 감시
- Recovery Services 자격 증명 모음
- Azure Key Vault
- Azure AD(Azure Active Directory)
- Azure 리소스 관리자
- Azure 모니터 (로그)

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소를 자세히 설명합니다.

**요새 호스트**: 베스천 호스트는 관리자가 배포된 리소스에 액세스할 수 있는 안전한 연결을 제공하는 단일 진입점입니다. 요새 호스트의 NSG는 RDP에 대한 TCP 포트 3389에 대해서만 연결을 허용합니다. 고객은 조직 시스템 강화 요구 사항에 맞게 요새 호스트를 추가로 구성할 수 있습니다.

### <a name="virtual-network"></a>가상 네트워크
아키텍처는 10.200.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: 이 솔루션은 가상 네트워크 내부에서 별도 웹 서브넷, 데이터베이스 서브넷, Active Directory 서브넷 및 서브넷 관리를 포함하는 아키텍처에 리소스를 배포합니다. 서브넷은 서브넷 간 트래픽을 시스템 및 관리 기능에 필요한 경우로만 제한하기 위해 개별 서브넷에 적용되는 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다.

이 솔루션으로 배포된 [네트워크 보안 그룹](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json)에 대한 구성을 참조하세요. 고객은 [이 설명서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)를 참조하여 위의 파일을 편집하여 네트워크 보안 그룹을 구성할 수 있습니다.

각 서브넷에는 전용 NSG(네트워크 보안 그룹)가 있습니다.
- 1 NSG - Application Gateway용(LBNSG)
- 1 NSG - 요새 호스트용(MGTNSG)
- 1 NSG - 기본 및 백업 도메인 컨트롤러용(ADNSG)
- 1 NSG - SQL Server용(SQLNSG)
- 1 NSG - 웹 계층용(WEBNSG)

**서브넷**: 각 서브넷이 해당 NSG에 연결되어야 합니다.

### <a name="data-at-rest"></a>미사용 데이터

이 아키텍처는 여러 암호화 방법을 사용하여 미사용 데이터를 보호합니다.

**Azure Storage**: 미사용 데이터 암호화 요구 사항에 부합하기 위해 모든 스토리지 계정은[Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)을 사용합니다.

**SQL Server**: SQL Server는 [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)를 사용하도록 구성됩니다. 이 기능은 데이터 및 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. TDE는 저장된 데이터가 무단으로 액세스되지 못하게 합니다.

고객은 다음과 같은 SQL Server 보안 조치를 구성할 수도 있습니다.
-   [AD 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
-   [SQL 데이터베이스 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다.
-   [Always Encrypted 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않게 합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
-   [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 참조 아키텍처 배포 후에 수행할 수 있습니다. 고객은 데이터베이스 스키마를 준수하도록 동적 데이터 마스킹 설정을 조정해야 합니다.

**Azure Disk Encryption**: Azure Disk Encryption은 Windows IaaS 가상 머신 디스크를 암호화하는 데 사용됩니다. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어 및 관리할 수 있도록 합니다.

### <a name="identity-management"></a>ID 관리

다음 기술은 Azure 환경에서 ID 관리 기능을 제공합니다.
- [Azure AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다.
- 고객 배포 웹 애플리케이션에 대한 인증은 Azure AD를 사용하여 수행할 수 있습니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요.  
- [Azure RBAC(Role-based Access Control)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)은 Azure에 대해 세밀하게 초점을 맞춘 액세스 관리를 구현합니다. 구독 액세스는 구독 관리자로 제한되며 리소스 액세스는 사용자 역할에 따라 제한될 수 있습니다.
- 배포된 IaaS Active Directory 인스턴스는 배포된 IaaS 가상 머신에 대한 OS 수준의 ID 관리를 제공합니다.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault는 이 참조 아키텍처에 대한 IaaS 가상 머신 디스크 암호화 키와 비밀을 관리하는 데 유용합니다.

**패치 관리**: 이 Azure Security 및 Compliance Blueprint Automation에서 배포한 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 이 솔루션은 필요할 때 Windows 서버에 패치를 배포하기 위해 배포 업데이트를 만들 수 있는 Azure Automation 솔루션도 배포합니다.

**맬웨어 방지**: Virtual Machine용 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)는 보호되는 Virtual Machines에서 악성 또는 원치 않는 소프트웨어가 사용자 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능을 제공합니다.

**Application Gateway**: 아키텍처는 WAF(웹 애플리케이션 방화벽)과 OWASP 규칙 집합을 사용하는 Application Gateway를 통해 보안상 위험을 줄입니다. 추가적인 기능은 다음과 같습니다.

- [종단 간 SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL 오프로드](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal) 사용
- [TLS v1.0 및 v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) 사용 안 함
- [WAF(웹 애플리케이션 방화벽)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 모드
- [방지 모드](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal), OWASP 3.0 규칙 집합 사용

### <a name="business-continuity"></a>비즈니스 연속성

**고가용성**: 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 가상 머신을 하나 이상 사용할 수 있습니다. 이 솔루션은 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)에 모든 웹 계층 및 데이터 계층 가상 머신을 배포합니다. 가용성 집합을 사용하면 가상 머신을 격리된 여러 하드웨어 클러스터에 분산하여 가용성을 높일 수 있습니다. 또한, 이 솔루션은 가용성 집합에서 SQL Server 가상 머신을 [AlwaysOn 가용성 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)으로 배포합니다. Always On 가용성 그룹 기능은 고가용성 및 재해 복구 기능을 제공합니다.

**Recovery Services 자격 증명 모음**: [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)은 백업 데이터를 저장하며, 이 아키텍처에 있는 Azure Virtual Machines의 모든 구성을 보호합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있으므로 복원 시간이 단축됩니다.

**클라우드 감시**: [클라우드 감시](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness)는 Azure를 중재 지점으로 활용하는 Windows Server 2016의 장애 조치(failover) 클러스터 쿼럼 감시의 한 유형입니다. 다른 모든 쿼럼 감시와 마찬가지로 클라우드 감시는 투표를 받고 쿼럼 계산에 참여할 수 있지만 공개적으로 사용 가능한 표준 Azure Blob Storage를 사용합니다. 따라서 공용 클라우드에서 호스팅되는 VM에 대한 추가적인 유지 관리 오버헤드가 필요하지 않습니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

Azure Monitor 로그는 시스템 상태 뿐만 아니라 시스템 및 사용자 작업에 대 한 광범위 로깅을 제공 합니다. 합니다 [Azure Monitor 로그](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 솔루션 수집 하 고 Azure 리소스에서 생성 된 데이터를 분석 하 여 온-프레미스 환경입니다.

- **활동 로그:**.  [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- **진단 로그:**  [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그입니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Storage 로그, Key Vault 감사 로그 및 Application Gateway 액세스 및 방화벽 로그가 포함됩니다.
- **로그 보관:**  모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 보존은 조직 특정 보존 요구 사항에 맞게 최대 730일까지 사용자가 구성할 수 있습니다. 이러한 로그는 처리, 저장 및 대시보드 보고에 대 한 Azure Monitor 로그에 연결 합니다.

또한 이 아키텍처의 일부인 다음 모니터링 솔루션이 설치됩니다. 이러한 솔루션을 FedRAMP 보안 컨트롤에 맞게 구성하는 것은 고객의 책임입니다.
-   [AD 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 확인 솔루션은 표준 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
-   [맬웨어 방지 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): 맬웨어 방지 솔루션은 맬웨어, 위협 및 보호 상태를 보고합니다.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation 솔루션은 Runbook을 저장, 실행 및 관리합니다.
-   [보안 및 감사](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): 보안 및 감사 대시보드는 보안 도메인, 주목할 만한 문제, 탐지, 위협 인텔리전스 및 일반적인 보안 쿼리에 대한 메트릭을 제공하여 리소스의 보안 상태에 대한 높은 수준의 인사이트를 제공합니다.
-   [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
-   [업데이트 관리](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): 업데이트 관리 솔루션을 사용하면 고객이 사용 가능한 업데이트 상태 및 필수 업데이트 설치 프로세스를 포함하여 운영 체제 보안 업데이트를 관리할 수 있습니다.
-   [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포되는 에이전트 수와 해당 지리적 분포, 응답이 없는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
-   [Azure 활동 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.
-   [변경 내용 추적](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 변경 내용 추적 솔루션을 사용하면 고객이 환경의 변경 내용을 쉽게 파악할 수 있습니다.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 사용하면 사용자의 Azure 리소스에서 API 호출을 추적하는 것을 포함하여 조직에서 감사, 경고 만들기 및 데이터 보관을 수행할 수 있도록 하여 고객이 성능을 추적하고, 보안을 유지하고, 추세를 식별할 수 있습니다.

## <a name="threat-model"></a>위협 모델
이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 [다운로드](https://aka.ms/fedrampWAdfd)에서 사용할 수 있거나 아래에 나와 있습니다. 이 모델을 사용하면 고객이 수정할 때 발생할 수 있는 시스템 인프라의 잠재적 위험 요소를 파악할 수 있습니다.

![FedRAMP용 IaaS 웹 애플리케이션 위협 모델](images/fedramp-iaaswa-threat-model.png?raw=true "FedRAMP용 IaaS 웹 애플리케이션 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서

[Azure 보안 및 규정 준수 청사진 - FedRAMP High 고객 책임 매트릭스](https://aka.ms/blueprinthighcrm)에는 FedRAMP High 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 매트릭스는 각 컨트롤 구현이 Microsoft의 책임인지, 고객의 책임인지 아니면 둘의 공동 책임인지 자세히 설명합니다.

[Azure Security 및 Compliance Blueprint - FedRAMP IaaS 웹 애플리케이션 High 컨트롤 구현 매트릭스](https://aka.ms/blueprintwacim)에는 FedRAMP High 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 매트릭스는 IaaS 웹 애플리케이션 아키텍처가 어떤 컨트롤을 담당하는지에 대한 정보를 제공하며, 여기에는 구현을 통해 담당 컨트롤의 요구 사항을 충족시키는 방법에 대한 자세한 설명이 포함됩니다.

## <a name="deploy-the-solution"></a>솔루션 배포

이 Azure Security 및 Compliance Blueprint Automation은 Azure Resource Manager의 API 서비스에서 Azure 내에 리소스를 배포하기 위해 처리하는 JSON 구성 파일과 PowerShell 스크립트로 구성됩니다. 자세한 배포 지침은 [여기](https://aka.ms/fedrampblueprintrepo)에 있습니다.
> [!NOTE]
> 이 솔루션은 Azure Government에 배포됩니다.

#### <a name="quickstart"></a>빠른 시작
1. [이](https://aka.ms/fedrampblueprintrepo) GitHub 리포지토리를 로컬 워크스테이션에 복제하거나 다운로드합니다.

2. 미리 배포된 PowerShell 스크립트 azure-blueprint/predeploy/Orchestration_InitialSetup.ps1을 실행합니다.

3. 아래 단추를 클릭하고 Azure Portal에 로그인한 다음 필요한 ARM 템플릿 매개 변수를 입력하고 **구매**를 클릭합니다.

    [![Azure에 배포](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항
### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
이 IaaS 웹 애플리케이션 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure Virtual Network 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객이 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포한 안전하고 완성도가 높은 기술입니다. [IPsec 터널 모드](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. Azure ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. 또한 이 연결은 고객의 통신 공급자의 직접 연결이므로 데이터는 인터넷을 통해 이동하지 않으며, 따라서 노출되지 않습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>고지 사항

- 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.  
- 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.  
- 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.  
- 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.  
- 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
- 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
