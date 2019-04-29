---
title: Azure 보안 및 규정 준수 청사진 - Data Warehouse for FedRAMP 자동화
description: Azure 보안 및 규정 준수 청사진 - Data Warehouse for FedRAMP 자동화
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586035"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure 보안 및 규정 준수 청사진: FedRAMP 자동화에 대 한 데이터 웨어하우스

## <a name="overview"></a>개요

[FedRAMP(Federal Risk and Authorization Management Program)](https://www.fedramp.gov/)는 미국 정부 차원 프로그램으로 클라우드 제품 및 서비스에 대한 보안 평가, 권한 부여 및 연속 모니터링에 대한 표준화된 접근 방법을 제공합니다. 이 Azure 보안 및 규정 준수 청사진은 FedRAMP 하이 컨트롤의 하위 집합을 구현하는 데 도움이 되는 Microsoft Azure 데이터 웨어하우스 아키텍처를 제공하는 방법에 대한 지침을 제공합니다. 이 솔루션은 공통 참조 아키텍처를 위한 Azure 리소스를 배포하고 구성하는 방법에 대한 지침을 제공하고, 고객이 특정 보안 및 규정 준수 요구 사항에 부합할 수 있는 방법을 보여주고, 고객이 Azure에서 자체 데이터 웨어하우스 솔루션을 구축 및 구성하기 위한 기초 역할을 합니다.

이 참조 아키텍처, 관련 컨트롤 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이 되며, 프로덕션 환경에서 있는 그대로 사용하면 안 됩니다. 애플리케이션을 수정 없이 이러한 환경에 배포하는 것은 FedRAMP 높음 기준의 요구 사항을 온전히 충족하기에는 부족합니다. 다음 사항에 유의하세요.
- 아키텍처는 고객이 FedRAMP 규격 방식으로 워크로드를 Azure에 배포할 수 있도록 지원하기 위한 기준을 제공합니다.
- 요구 사항이 각 고객의 특정 구현에 따라 달라질 수 있으므로 고객은 이 아키텍처를 사용하여 구축된 솔루션에 대한 적절한 보안 및 규정 준수 평가를 수행해야 합니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소

이 솔루션은 고성능 보안 클라우드 데이터 웨어하우스를 구현하는 데이터 웨어하우스 아키텍처를 제공합니다. 이 아키텍처에는 별도의 두 데이터 계층이 있습니다. 하나는 클러스터된 SQL 환경 내에서 데이터를 가져오고 저장하고 준비하는 계층이며, 다른 하나는 ETL 도구(예: [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL 쿼리)를 사용하여 처리할 데이터를 로드하는 Azure SQL Data Warehouse에 대한 계층입니다. 데이터가 Azure SQL Data Warehouse에 저장되면 분석이 대규모로 실행될 수 있습니다.

Microsoft Azure는 고객에게 다양한 보고 및 분석 서비스를 제공합니다. 이 솔루션에는 Azure SQL Data Warehouse에서 보고서를 빠르게 만들 수 있는 SSRS(SQL Server Reporting Services)가 포함됩니다. 모든 SQL 트래픽은 자체 서명된 인증서를 포함하여 SSL로 암호화됩니다. Azure는 강화된 보안을 위해 신뢰할 수 있는 인증 기관을 사용하는 것이 가장 좋습니다.

Azure SQL Data Warehouse의 데이터는 데이터 저장소 비용을 크게 줄이면서 쿼리 성능을 향상시키는 형식인 열 형식 저장소가 있는 관계형 테이블에 저장됩니다.  계산 리소스가 필요한 활성 프로세스가 없으면 Azure SQL Data Warehouse 계산 리소스는 사용량 요구 사항에 따라 확장/축소하거나 완전히 종료할 수 있습니다.

SQL Load Balancer는 SQL 트래픽을 관리하여 고성능을 보장합니다. 이 참조 아키텍처의 모든 가상 머신은 고가용성 및 재해 복구 기능을 위해 AlwaysOn 가용성 그룹에 구성된 SQL Server 인스턴스가 있는 가용성 집합에 배포됩니다.

또한 이 데이터 웨어하우스 참조 아키텍처에는 아키텍처 내의 리소스를 관리하는 AD(Active Directory) 계층도 포함됩니다. Active Directory 서브넷을 사용하면 더 큰 AD 포리스트 구조에서 쉽게 채택할 수 있으므로 더 큰 포리스트에 액세스할 수 없는 경우에도 환경을 지속적으로 작동할 수 있습니다. 모든 가상 머신은 Active Directory 계층의 도메인에 조인되며, Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.

가상 머신은 관리자가 배포된 리소스에 액세스할 수 있는 보안 연결을 제공하는 관리 요새 호스트 역할을 합니다. 데이터는 이 관리 요새 호스트를 통해 준비 영역에 로드됩니다. **Azure에서는 관리 및 참조 아키텍처 서브넷에 데이터 가져오기를 위해 VPN 또는 Azure ExpressRoute 연결을 구성하는 것이 좋습니다**.

![Data Warehouse for FedRAMP 참조 아키텍처 다이어그램](images/fedramp-datawarehouse-architecture.png?raw=true "Data Warehouse for FedRAMP 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처에 대한 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.

Azure Virtual Machines
-   (1) 요새 호스트
-   (2) Active Directory 도메인 컨트롤러
-   (2) SQL Server 클러스터 노드
-   (1) SQL Server 감시

가용성 집합
-   (1) Active Directory 도메인 컨트롤러
-   (1) SQL 클러스터 노드 및 감시

Virtual Network
-   (4) 서브넷
-   (4) 네트워크 보안 그룹

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Recovery Services 자격 증명 모음

Azure Key Vault

Azure Monitor 로그

## <a name="deployment-architecture"></a>배포 아키텍처

다음 섹션에서는 개발 및 구현 요소를 자세히 설명합니다.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) 대규모 병렬 처리 (MPP)를 빠르게 페타바이트 데이터에서 복잡 한 쿼리를 실행 하는 활용 하는 엔터프라이즈 데이터 웨어하우스 (EDW) 됩니다. 간단한 PolyBase T-SQL 쿼리를 사용하여 빅 데이터를 SQL Data Warehouse로 가져온 후 MPP 기능을 사용하여 고성능 분석을 실행합니다.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) 보고서 테이블, 차트, 지도, 계기, 행렬 및 Azure SQL Data Warehouse에 대 한 더 빠르게 만들 수 있습니다.

**요새 호스트**: 요새 호스트는 사용자가 이 환경에서 배포된 리소스에 액세스할 수 있도록 하는 단일 진입점입니다. 요새 호스트는 안전한 목록에 있는 공용 IP 주소의 원격 트래픽만 허용하여 배포된 리소스에 대한 보안 연결을 제공합니다. RDP(원격 데스크톱 프로토콜) 트래픽을 허용하려면 트래픽의 원본을 NSG(네트워크 보안 그룹)에 정의해야 합니다.

다음 구성을 사용하여 가상 머신을 도메인에 가입된 요새 호스트로 만들었습니다.
-   [맬웨어 방지 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Monitor 기록 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   Azure Key Vault(Azure Government, PCI DSS, HIPAA 및 기타 요구 사항 적용)를 사용하는 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   사용하지 않을 때 가상 머신 사용을 줄이는 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard)를 사용하도록 설정하여 자격 증명과 다른 비밀이 실행 중인 운영 체제와 격리된 보호된 환경에서 실행됩니다.

### <a name="virtual-network"></a>가상 네트워크
이 아키텍처는 10.0.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 VNet 내에서 트래픽을 허용하거나 거부하는 ACL(액세스 제어 목록)이 포함됩니다. NSG는 서브넷 또는 개별 VM 수준에서 트래픽을 보호하는 데 사용할 수 있습니다. 다음과 같은 NSG가 있습니다.
  - 데이터 계층용 NSG(SQL Server 클러스터, SQL Server 감시 및 SQL Load Balancer)
  - 관리 요새 호스트용 NSG
  - Active Directory용 NSG
  - SQL Server Reporting Services용 NSG

각 NSG에는 솔루션이 안전하고 올바르게 작동할 수 있도록 특정 포트 및 프로토콜이 열려 있습니다. 또한 각 NSG에 대해 다음과 같은 구성을 사용합니다.
  - [진단 로그 및 이벤트](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)는 저장소 계정에 사용되고 저장됩니다.
  - Azure Monitor 로그에 연결 되는 [NSG의 진단](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**서브넷**: 각 서브넷이 해당 NSG에 연결되어야 합니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 암호화, 데이터베이스 감사 및 다른 방법을 통해 미사용 데이터를 보호합니다.

**Azure Storage** 암호화된 미사용 데이터 요구 사항을 충족하기 위해 모든 [Azure Storage](https://azure.microsoft.com/services/storage/)에서 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 활용하여 OS와 데이터 디스크에 대한 볼륨 암호화를 제공합니다. 이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키를 제어하고 관리하는 데 유용합니다.

**Azure SQL Database**: Azure SQL Database 인스턴스에서 사용하는 데이터베이스 보안 조치는 다음과 같습니다.
-   [AD 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
-   [SQL 데이터베이스 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
-   SQL Database는 [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하도록 구성됩니다. 이 기능은 데이터 및 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. TDE는 저장된 데이터가 무단으로 액세스되지 못하게 합니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [SQL 위협 요소 탐지](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)를 사용하면 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 제공하여 발생할 수 있는 잠재적 위협을 탐지하고 대응할 수 있습니다.
-   [Always Encrypted 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)은 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 나타나지 않게 합니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 애플리케이션 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
-   [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 참조 아키텍처 배포 후에 수행할 수 있습니다. 고객은 데이터베이스 스키마를 준수하도록 동적 데이터 마스킹 설정을 조정해야 합니다.

### <a name="business-continuity"></a>비즈니스 연속성
**고가용성**: 서버 워크 로드에서 그룹화 되는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure에서 가상 머신의 고가용성을 보장 하도록 합니다. 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 가상 머신을 하나 이상 사용할 수 있습니다.

**Recovery Services 자격 증명 모음**: [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)은 백업 데이터를 저장하며, 이 아키텍처에 있는 Azure Virtual Machines의 모든 구성을 보호합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있으므로 복원 시간이 단축됩니다.

### <a name="logging-and-audit"></a>로깅 및 감사
[Azure Monitor 로그](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 시스템 상태 뿐만 아니라 시스템 및 사용자 작업에 대 한 광범위 로깅을 제공 합니다. 합니다 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 솔루션 수집 하 고 Azure 리소스에서 생성 된 데이터를 분석 하 여 온-프레미스 환경입니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Blob Storage, 테이블 및 큐 로그가 포함됩니다.
- **방화벽 로그.**: Application Gateway는 전체 진단 및 액세스 로그를 제공합니다. 방화벽 로그는 WAF가 활성화된 Application Gateway 리소스에 사용할 수 있습니다.
- **로그 보관**: 모든 진단 로그는 정의된 보존 기간이 2일인 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 이러한 로그는 처리, 저장 및 대시보드 보고에 대 한 Azure Monitor 로그에 연결 합니다.

또한 이 아키텍처의 일부로 포함된 모니터링 솔루션은 다음과 같습니다.
-   [AD 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 확인 솔루션은 표준 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
-   [맬웨어 방지 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): 맬웨어 방지 솔루션은 맬웨어, 위협 및 보호 상태를 보고합니다.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation 솔루션은 Runbook을 저장, 실행 및 관리합니다.
-   [보안 및 감사](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): 보안 및 감사 대시보드는 보안 도메인, 주목할 만한 문제, 탐지, 위협 인텔리전스 및 일반적인 보안 쿼리에 대한 메트릭을 제공하여 리소스의 보안 상태에 대한 높은 수준의 인사이트를 제공합니다.
-   [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
-   [업데이트 관리](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): 업데이트 관리 솔루션을 사용하면 고객이 사용 가능한 업데이트 상태 및 필수 업데이트 설치 프로세스를 포함하여 운영 체제 보안 업데이트를 관리할 수 있습니다.
-   [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포되는 에이전트 수와 해당 지리적 분포, 응답이 없는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
-   [Azure 활동 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics 솔루션은 고객에 대한 모든 Azure 구독에서 Azure 활동 로그를 분석하는 데 도움을 줍니다.
-   [변경 내용 추적](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 변경 내용 추적 솔루션을 사용하면 고객이 환경의 변경 내용을 쉽게 파악할 수 있습니다.

### <a name="identity-management"></a>ID 관리
다음 기술은 Azure 환경에서 ID 관리 기능을 제공합니다.
-   [AD(Azure Active Directory)](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스일 수 있습니다. SQL Database에 액세스하는 사용자를 포함하여 모든 솔루션 사용자가 Azure Active Directory에서 만들어졌습니다.
-   애플리케이션에 대한 인증은 Azure AD를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화는 Azure AD를 사용하여 Azure SQL Database에 대해 애플리케이션을 인증합니다. 자세한 내용은 [SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미치는 잠재적 취약성을 탐지하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대응을 구성하며, 의심스러운 인시던트를 조사하여 이를 해결하기 위한 적절한 조치를 수행합니다.
-   [Azure RBAC(Role-based Access Control)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)은 Azure에 초점을 맞춘 액세스 관리를 지원합니다. 구독 액세스는 구독 관리자로 제한됩니다.

Azure SQL Database의 보안 기능 사용에 대한 자세한 내용은 [Contoso Clinic 데모 애플리케이션](https://github.com/Microsoft/azure-sql-security-sample) 샘플을 참조하세요.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다.

**맬웨어 방지**: Virtual Machine용 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)는 보호되는 Virtual Machines에서 악성 또는 원치 않는 소프트웨어가 사용자 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능을 제공합니다.

**패치 관리**: 이 참조 아키텍처의 일부로 배포된 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 또한 이 솔루션에는 필요한 경우, 가상 머신을 패치하기 위해 업데이트된 배포를 만들 수 있는 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) 서비스가 포함되어 있습니다.


## <a name="guidance-and-recommendations"></a>지침 및 권장 사항
### <a name="expressroute-and-vpn"></a>ExpressRoute 및 VPN
이 데이터 웨어하우스 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 보안 VPN 터널을 구성해야 합니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. ExpressRoute 또는 VPN을 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 레이어를 추가할 수 있습니다.

### <a name="extract-transform-load-etl-process"></a>ETL(추출, 변환, 로드) 프로세스
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)는 별도의 ETL 또는 가져오기 도구를 사용할 필요 없이 데이터를 Azure SQL Data Warehouse에 로드할 수 있습니다. PolyBase를 사용하면 T-SQL 쿼리를 통해 데이터에 액세스할 수 있습니다. Microsoft의 비즈니스 인텔리전스 및 분석 스택뿐만 아니라 SQL Server와 호환되는 타사 도구도 PolyBase와 함께 사용할 수 있습니다.

### <a name="azure-active-directory-setup"></a>Azure Active Directory 설정
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 환경과 상호 작용하는 직원에 대한 배포 및 프로비전 액세스를 관리하는 데 필수적입니다. 기존의 Windows Server Active Directory는 [네 번의 클릭](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)으로 AAD와 통합될 수 있습니다. 고객은 배포된 Active Directory 인프라를 AAD 포리스트의 하위 도메인으로 만들어 배포된 Active Directory 인프라(도메인 컨트롤러)를 기존 AAD에 연결할 수도 있습니다.

### <a name="additional-services"></a>서비스
이 데이터 웨어하우스 아키텍처는 [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) 환경에 배포할 용도로 제작되지는 않았지만, 이 참조 아키텍처에 설명된 서비스와 Azure Commercial 환경에서만 사용할 수 있는 추가 서비스를 통해 비슷한 목표를 달성할 수 있습니다. Azure Commercial은 보통 영향 수준에서 FedRAMP JAB P-ATO를 유지하며, 따라서 정부 기관 및 파트너가 Azure Commercial 환경을 활용하여 중요도 보통인 정보를 클라우드에 배포할 수 있습니다.

Azure Commercial은 다음을 포함하여 형식이 지정된 데이터 및 형식이 지정되지 않은 데이터 저장소와 데이터 웨어하우징에 사용되는 스테이징을 처리하는 다양한 서비스를 제공합니다.
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)는 복잡한 하이브리드 ETL(추출-변환-로드), ELT(추출-로드-변환) 및 데이터 통합 프로젝트를 위해 구축된 관리되는 클라우드 서비스입니다. 고객이 Azure Data Factory를 사용하여 서로 다른 데이터 저장소의 데이터를 수집하는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약할 수 있습니다. 그런 다음, 고객이 Azure SQL Data Warehouse와 같은 데이터 저장소로 출력할 데이터를 처리하고 변환할 수 있습니다.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)를 사용하면 운영 및 예비 분석을 위해 한 장소에서 원하는 크기, 유형 및 수집 속도의 데이터를 캡처할 수 있습니다. Azure Data Lake Store는 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환되며 Azure SQL Data Warehouse와 같은 다른 Azure 서비스와 원활하게 통합됩니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 DFD(데이터 흐름 다이어그램)는 [다운로드](https://aka.ms/blueprintdwthreatmodel)에서 사용할 수 있거나 아래에 나와 있습니다.

![Data Warehouse for FedRAMP 위협 모델](images/fedramp-datawarehouse-threat-model.png?raw=true "Data Warehouse for FedRAMP 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서
[Azure 보안 및 규정 준수 청사진 – FedRAMP High 고객 책임 매트릭스](https://aka.ms/blueprinthighcrm)에는 FedRAMP High 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 마찬가지로, [Azure 보안 및 규정 준수 청사진 – FedRAMP Moderate 고객 책임 매트릭스](https://aka.ms/blueprintcrmmod)에는 FedRAMP Moderate 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 두 문서는 각 컨트롤 구현이 Microsoft의 책임인지, 고객의 책임인지 아니면 둘의 공동 책임인지 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - FedRAMP High 컨트롤 구현 매트릭스](https://aka.ms/blueprintdwcimhigh) 및 [Azure 보안 및 규정 준수 청사진 - FedRAMP Moderate 컨트롤 구현 매트릭스](https://aka.ms/blueprintdwcimmod)는 포함된 각 컨트롤의 요구 사항을 구현에서 어떻게 충족하는지에 대한 자세한 설명을 포함하여 각 FedRAMP 기준에 대한 데이터 웨어하우스 아키텍처의 범위에 포함되는 컨트롤에 대한 정보를 제공합니다.

## <a name="disclaimer"></a>고지 사항

 - 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
 - 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
 - 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
 - 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
 - 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
 - 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
