---
title: Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 데이터 웨어하우스
description: Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 데이터 웨어하우스
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609565"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 준수 데이터 웨어하우스

## <a name="overview"></a>개요
[NIST 특별 발행물 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf)은 비연방 정보 시스템 및 조직에 있는 CUI(통제 미분류 정보)를 보호하기 위한 지침을 제공하고 있습니다. NIST SP 800-171에는 CUI 기밀성을 보호하기 위한 14개 보안 요구 사항이 규정되어 있습니다.

이 Azure 보안 및 규정 준수 청사진에서는 고객이 Azure에서 NIST SP 800-171 제어의 하위 집합을 구현하는 데이터 웨어하우스 아키텍처를 배포하는 데 도움이 되는 지침을 제공합니다. 이 솔루션은 고객이 특정 보안 및 규정 준수 요구 사항을 충족할 수 있는 방식을 제시합니다. 또한 고객이 Azure에서 데이터 웨어하우스 솔루션을 직접 빌드하고 구성할 수 있는 기준으로도 사용됩니다.

이 참조 아키텍처, 관련 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 작업 방식을 조정할 수 있는 기준으로 사용하기 위해 제공되는 것으로, 프로덕션 환경에서 있는 그대로 사용해서는 안 됩니다. 고객은 이 아키텍처를 사용하여 빌드된 솔루션에 대해 적절한 보안 및 규정 준수 평가를 수행해야 합니다. 요구 사항은 각 고객의 구체적인 구현에 따라 달라질 수 있습니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소
이 솔루션은 고성능 보안 클라우드 데이터 웨어하우스를 구현하는 참조 아키텍처를 제공합니다. 이 아키텍처에는 두 가지 개별 데이터 계층이 있습니다. 그 중 하나는 클러스터형 SQL 환경 내에서 데이터를 가져와 저장하고 준비하는 계층이고, 다른 하나는 SQL Data Warehouse용 계층입니다. 이 계층에서는 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL 쿼리와 같은 ETL(추출-변환-로드) 도구를 사용하여 처리용으로 데이터를 로드합니다. 데이터가 SQL Data Warehouse에 저장되고 나면 분석을 대규모로 실행할 수 있습니다.

Azure는 고객에게 다양한 보고 및 분석 서비스를 제공합니다. 이 솔루션에는 SQL Data Warehouse에서 보고서를 빠르게 만들 수 있는 SQL Server Reporting Services가 포함됩니다. 모든 SQL 트래픽은 자체 서명된 인증서를 포함하여 SSL로 암호화됩니다. 모범 사례에 따라 보안 강화를 위해 신뢰할 수 있는 인증 기관을 사용하는 것이 좋습니다.

Azure SQL Data Warehouse는 열 형식 저장소를 사용하여 관계형 테이블에 데이터를 저장합니다. 이 형식으로 인해 데이터 저장소 비용이 상당히 감소하고 쿼리 성능이 향상됩니다. 계산 리소스가 필요한 활성 프로세스가 없으면 사용량 요구 사항에 따라 SQL Data Warehouse 계산 리소스를 강화/규모 축소하거나 완전히 종료할 수 있습니다.

SQL Server Load Balancer는 높은 성능을 유지하기 위해 SQL 트래픽을 관리합니다. 이 참조 아키텍처의 모든 VM(가상 머신)은 AlwaysOn 가용성 그룹에 구성된 SQL Server 인스턴스가 있는 가용성 집합에 배포됩니다. 이 구성에서는 고가용성 및 재해 복구 기능을 제공합니다.

또한 이 데이터 웨어하우스 참조 아키텍처에는 아키텍처 내의 리소스를 관리하는 Active Directory 계층도 포함됩니다. Active Directory 서브넷은 대형 Active Directory 포리스트 구조에서도 쉽게 채택할 수 있습니다. 그러므로 대규모 포리스트에 액세스할 수 없을 때도 환경을 지속적으로 운영할 수 있습니다. 모든 VM은 Active Directory 계층으로 도메인 조인되며, Active Directory 그룹 정책을 사용하여 운영 체제 수준에서 보안 및 규정 준수 구성을 적용합니다.

솔루션은 고객이 미사용 데이터의 기밀성을 유지하려면 스토리지 서비스 암호화를 사용하도록 구성할 수 있는 Azure Storage 계정을 사용합니다. Azure는 데이터 복원력을 위해 고객이 선택한 데이터 센터 내에 데이터 복사본 3부를 저장합니다. 이처럼 지역 중복 저장소가 사용되므로 데이터가 매우 멀리 떨어진 보조 데이터 센터에 복제되며, 해당 데이터 센터 내에도 복사본 3부로 다시 저장됩니다. 이러한 방식이 사용되므로 고객의 기본 데이터 센터에서 사고가 발생하더라도 데이터가 손실되지 않습니다.

보안 향상을 위해 이 솔루션의 모든 리소스는 Azure Resource Manager를 통해 리소스 그룹으로 관리됩니다. 그리고 Azure AD(Active Directory) RBAC(역할 기반 액세스 제어)를 사용하여 배포된 리소스 액세스를 제어합니다. 이러한 리소스는 Azure Key Vault의 고객 키를 포함합니다. 시스템 상태는 Azure Security Center 및 Azure Monitor를 통해 모니터링됩니다. 고객은 로그를 캡처하도록 두 모니터링 서비스를 구성합니다. 시스템 상태는 쉽게 사용할 수 있는 단일 대시보드에 표시됩니다.

VM이 관리 배스천 호스트로 사용되며 관리자가 배포된 리소스에 액세스할 수 있는 보안 연결을 제공합니다. 데이터는 이 관리 요새 호스트를 통해 준비 영역에 로드됩니다. *참조 아키텍처 서브넷으로 데이터를 가져오는 작업 및 관리 작업용으로 VPN 또는 Azure ExpressRoute 연결을 구성하는 것이 좋습니다.*

![NIST SP 800-171 준수 데이터 웨어하우스 참조 아키텍처 다이어그램](images/nist171-dw-architecture.png "NIST SP 800-171 준수 데이터 웨어하우스 참조 아키텍처 다이어그램")

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 자세한 내용은 [배포 아키텍처](#deployment-architecture) 섹션을 참조하세요.

- 가용성 집합
    - Active Directory 도메인 컨트롤러
    - SQL Server 클러스터 노드 및 감시
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure 모니터 (로그)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) 요새 호스트
    - (2) Active Directory 도메인 컨트롤러
    - (2) SQL Server 클러스터 노드
    - (1) SQL Server 감시
- Azure Virtual Network
    - (1) /16 네트워크
    - (4) /24 네트워크
    - (4) 네트워크 보안 그룹
- Recovery Services 자격 증명 모음
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>배포 아키텍처
다음 섹션에서는 개발 및 구현 요소에 대해 자세히 설명합니다.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) 엔터프라이즈 데이터 웨어하우스를 빠르게 페타바이트 데이터에서 복잡 한 쿼리를 실행 하는 방대한 병렬 처리 활용 됩니다. 사용자는 간단한 PolyBase T-SQL 쿼리를 사용하여 빅 데이터를 SQL Data Warehouse로 가져오고 대규모 병렬 처리 기능을 활용하여 고성능 분석을 실행할 수 있습니다.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) 테이블, 차트, 지도, 계기, 행렬 및 SQL Data Warehouse에 대 한 더 빠른 보고서 만들기를 제공 합니다.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog)를 사용하면 데이터를 관리하는 사용자가 데이터 원본을 쉽게 검색하고 이해할 수 있습니다. 일반적인 데이터 원본은 데이터를 등록, 태그 지정 및 검색할 수 있습니다. 데이터는 기존 위치에 그대로 유지되지만 메타데이터의 복사본이 Data Catalog에 추가됩니다. 데이터 원본 위치에 대한 참조도 포함됩니다. 메타데이터가 인덱싱되므로 검색을 통해 각 데이터 원본을 쉽게 검색할 수 있습니다. 또한 데이터 원본을 검색하는 사용자가 해당 원본을 쉽게 이해할 수 있습니다.

**요새 호스트**: 요새 호스트에는 사용자가이 환경에서 배포 된 리소스에 액세스 하는 데 사용할 수 있는 항목의 단일 지점입니다. 배스천 호스트는 수신 허용 목록에 있는 공용 IP 주소의 원격 트래픽만 허용하여 배포된 리소스에 대한 보안 연결을 제공합니다. 원격 데스크톱 트래픽을 허용하려면 트래픽의 원본을 네트워크 보안 그룹에 정의해야 합니다.

이 솔루션은 다음 구성을 사용하여 도메인 조인 배스천 호스트로 VM을 만듭니다.
-   [맬웨어 방지 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Diagnostics 확장](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   Key Vault를 사용하는 [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)
-   사용하지 않을 때 VM 리소스 사용량을 줄이기 위한 [자동 종료 정책](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)
-   자격 증명과 기타 비밀이 실행 중인 운영 체제에서 격리되는 보호된 환경에서 실행되도록 [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) 사용

### <a name="virtual-network"></a>가상 네트워크
이 아키텍처는 10.0.0.0/16 주소 공간으로 개인 가상 네트워크를 정의합니다.

**네트워크 보안 그룹**: [NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)에는 가상 네트워크 내에서 트래픽을 허용하거나 거부하는 액세스 제어 목록이 포함됩니다. NSG는 서브넷 또는 개별 VM 수준에서 트래픽을 보호하는 데 사용할 수 있습니다. 다음과 같은 NSG가 있습니다.
  - 데이터 계층용 NSG(SQL Server 클러스터, SQL Server 감시 및 SQL Load Balancer)
  - 관리 요새 호스트용 NSG
  - Active Directory용 NSG
  - SQL Server Reporting Services용 NSG

솔루션이 안전하고 올바르게 작동할 수 있도록 각 NSG에서는 특정 포트 및 프로토콜이 열려 있습니다. 또한 각 NSG에 대해 다음과 같은 구성을 사용합니다.
  - [진단 로그 및 이벤트](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)가 사용하도록 설정되며 저장소 계정에 저장됩니다.
  - Azure Monitor 로그에 연결 합니다 [NSG의 진단](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)합니다.

**서브넷**: 각 서브넷이 해당 NSG에 연결되어야 합니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 여러 조치를 통해 미사용 데이터를 보호합니다. 이러한 조치에는 암호화 및 데이터베이스 감사가 포함됩니다.

**Azure Storage**: 모든 [스토리지](https://azure.microsoft.com/services/storage/)에서는 암호화된 미사용 데이터 관련 요구 사항을 충족하기 위해 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용합니다. 이 기능을 사용하면 조직의 보안 약정 및 준수 요구 사항을 지원하는 데이터를 안전하게 보호할 수 있습니다.

**Azure Disk Encryption**: [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Windows의 BitLocker 기능을 사용하여 운영 체제 및 데이터 디스크에 볼륨 암호화를 제공합니다. 이 솔루션은 디스크 암호화 키를 쉽게 제어 및 관리할 수 있도록 Key Vault와 통합됩니다.

**Azure SQL Database**: SQL Database 인스턴스는 다음 데이터베이스 보안 조치를 사용합니다.
-   [Active Directory 인증 및 권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)를 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 ID를 중앙의 한 위치에서 집중적으로 관리할 수 있습니다.
-   [SQL Database 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록합니다.
-   SQL Database는 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용하도록 구성됩니다. 투명한 데이터 암호화는 데이터베이스, 관련 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. 투명한 데이터 암호화를 사용하는 경우 저장된 데이터에 무단으로 액세스할 수 없습니다.
-   [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)은 적절한 권한이 부여될 때까지 데이터베이스 서버에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 데이터베이스에 대한 액세스를 허용합니다.
-   [SQL 위협 검색](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)을 사용하면 잠재적 위협 발생 시 검색하여 대응을 할 수 있습니다. SQL 위협 검색에서는 의심스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 패턴에 대한 보안 경고를 제공합니다.
-   [암호화된 열](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)을 사용하면 중요한 데이터가 데이터베이스 시스템 내에서 일반 텍스트로 표시되지 않습니다. 데이터 암호화를 사용하도록 설정하면 키에 액세스할 수 있는 클라이언트 애플리케이션 또는 앱 서버만 일반 텍스트 데이터에 액세스할 수 있습니다.
- [확장 속성](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql)을 사용하면 데이터 주체 처리를 중단할 수 있습니다. 사용자는 데이터베이스 개체에 사용자 지정 속성을 추가할 수 있습니다. 또한 애플리케이션 논리를 지원하기 위해 데이터에 “중단됨” 태그를 지정하여 연결된 금융 데이터 처리를 방지할 수 있습니다.
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)을 사용하면 사용자가 데이터 액세스를 제한하여 처리를 중단하는 정책을 정의할 수 있습니다.
- [SQL Database 동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)은 권한이 없는 사용자 또는 애플리케이션에 데이터를 마스킹하여 중요한 데이터의 노출을 제한합니다. 이 기능은 중요할 가능성이 있는 데이터를 자동으로 검색하고 적용할 적절한 마스크를 제안할 수 있습니다. 동적 데이터 마스킹을 사용하면 무단 액세스로 인해 중요한 데이터가 데이터베이스 외부로 유출되지 않도록 액세스 횟수를 줄일 수 있습니다. *고객은 데이터베이스 스키마를 준수하도록 설정을 조정할 책임이 있습니다.*

### <a name="identity-management"></a>ID 관리
Azure 환경에서 데이터에 대한 액세스를 관리하는 기능을 제공하는 기술은 다음과 같습니다.
-   [Azure AD](https://azure.microsoft.com/services/active-directory/)는 Microsoft 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 이 솔루션의 모든 사용자(SQL 데이터베이스에 액세스하는 사용자 포함)는 Azure AD에서 생성됩니다.
-   Azure AD를 사용하여 애플리케이션에 대한 인증을 수행합니다. 자세한 내용은 [Azure AD와 애플리케이션을 통합하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 데이터베이스 열 암호화에서는 SQL Database에 애플리케이션을 인증할 때도 Azure AD를 사용합니다. 자세한 내용은 [SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
-   관리자는 [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 사용하여 세분화된 액세스 권한을 정의할 수 있습니다. RBAC를 사용하면 사용자가 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 관리자는 모든 사용자에게 Azure 리소스에 대한 무제한 액세스를 부여하는 대신, 리소스와 데이터에 액세스하기 위한 특정 작업만 허용할 수 있습니다. 구독 액세스는 구독 관리자로 제한됩니다.
- 고객은 [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하여 데이터 등의 특정 정보에 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. 관리자는 Azure AD Privileged Identity Management를 사용하여 권한 있는 ID와 이러한 ID의 리소스 액세스 권한을 검색, 제한 및 모니터링할 수 있습니다. 필요한 경우 이 기능을 사용하여 요청 시 JIT(Just-In-Time) 관리 액세스를 적용할 수도 있습니다.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직 ID에 영향을 주는 잠재적 취약성을 검색하며, 조직 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 응답을 구성합니다. 또한 의심스러운 인시던트를 조사하며 해결을 위해 적절한 작업을 수행합니다.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Key Vault를 사용하면 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호할 수 있습니다. 고객은 다음 Key Vault 기능을 통해 데이터를 보호할 수 있습니다.
- 필요에 따라 고급 액세스 정책이 구성됩니다.
- Key Vault 액세스 정책은 키와 비밀에 대한 최소 필수 권한으로 정의됩니다.
- Key Vault의 모든 키와 비밀에는 만료 날짜가 있습니다.
- Key Vault의 모든 키는 특별한 하드웨어 보안 모듈을 통해 보호됩니다. 키 유형은 하드웨어 보안 모듈로 보호되는 2,048비트 RSA 키입니다.
- RBAC를 통해 모든 사용자와 ID에 최소 필수 권한이 부여됩니다.
- Key Vault에 대한 진단 로그는 365일 이상의 보존 기간 동안 사용하도록 설정됩니다.
- 키에 허용되는 암호화 작업은 필요한 것으로 제한됩니다

**패치 관리**: 이 참조 아키텍처의 일부분으로 배포 하는 Windows Vm은 Windows 업데이트 서비스에서 자동 업데이트를 수신 하려면 기본적으로 구성 됩니다. 또한 이 솔루션에는 필요한 경우 VM을 패치하기 위해 업데이트된 배포를 만들 수 있는 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) 서비스가 포함되어 있습니다.

**맬웨어 방지**: [Microsoft 맬웨어 방지 프로그램](https://docs.microsoft.com/azure/security/azure-security-antimalware) Vm에 대 한 실시간 보호 기능을 통해 식별 하 고 바이러스, 스파이웨어 및 기타 악성 소프트웨어 제거를 제공 합니다. 고객은 알려진 악성 소프트웨어 또는 원치 않는 소프트웨어가 보호된 VM에서 설치 또는 실행을 시도할 때 생성되는 경고를 구성할 수 있습니다.

**Azure Security Center**: 고객은 [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 여러 워크로드에 걸쳐 보안 정책을 중앙에서 적용 및 관리하고, 위협에 대한 노출을 제한하고, 공격을 검색하여 대응할 수 있습니다. 또한 Security Center는 Azure 서비스의 기존 구성에 액세스하여 보안 태세를 개선하고 데이터를 보호하는 데 유용한 구성 및 서비스 권장 사항을 제공합니다.

Security Center는 다양한 검색 기능을 사용하여 고객에게 환경을 대상으로 하는 잠재적인 공격에 대해 경고합니다. 이러한 경고는 트리거된 경고, 대상으로 지정된 리소스 및 공격의 출처에 대한 중요한 정보를 포함합니다. Security Center에는 위협 또는 의심스러운 활동이 발생할 때 트리거되는 미리 정의된 [보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) 집합이 있습니다. 고객은 [사용자 지정 경고 규칙](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)을 사용하여 환경에서 이미 수집된 데이터를 기반으로 하는 새 보안 경고를 정의할 수 있습니다.

Security Center는 우선 순위가 지정된 보안 경고 및 인시던트를 제공합니다. 고객은 Security Center를 통해 잠재적 보안 문제를 더 간편하게 검색하고 해결할 수 있습니다. 검색된 각 위협에 대해 [위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)가 생성됩니다. 인시던트 대응 팀은 위협을 조사하고 수정할 때 해당 보고서를 사용할 수 있습니다.

이 참조 아키텍처는 Security Center에서 [취약성 평가](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) 기능을 사용합니다. 이 아키텍처를 구성하고 나면 Qualys 등의 파트너 에이전트가 파트너 관리 플랫폼에 취약성 데이터를 보고합니다. 차례로 파트너의 관리 플랫폼은 취약성 및 상태 모니터링 데이터를 Security Center에 다시 제공합니다. 고객은 이 정보를 사용하여 취약한 VM을 빠르게 파악할 수 있습니다.

### <a name="business-continuity"></a>비즈니스 연속성
**고가용성**: 서버 워크 로드에서 그룹화 되는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure에서 Vm의 고가용성을 보장 하도록 합니다. 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 VM을 하나 이상 사용할 수 있습니다.

**Recovery Services 자격 증명 모음**: 합니다 [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) 백업 데이터를 저장 하 고이 아키텍처에서 Vm의 모든 구성을 보호 합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있습니다. 이 프로세스를 수행하면 복원 시간을 줄일 수 있습니다.

### <a name="logging-and-auditing"></a>로깅 및 감사

Azure 서비스는 시스템 및 사용자 활동, 시스템 상태를 광범위하게 기록합니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그는 작업의 개시 장치, 발생 시간 및 상태를 결정하는 데 도움이 될 수 있습니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Storage 로그, Key Vault 감사 로그 및 Azure Application Gateway 액세스 및 방화벽 로그가 포함됩니다. 모든 진단 로그는 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 사용자는 특정 요구 사항에 맞게 보존 기간을 최대 730일까지 구성할 수 있습니다.

**Azure Monitor 로그**: 이러한 로그에 통합 됩니다 [Azure Monitor 로그](https://azure.microsoft.com/services/log-analytics/) 처리, 저장 및 대시보드를 보고 합니다. 수집된 데이터는 Log Analytics 작업 영역 내에서 각 데이터 형식용 개별 테이블로 구성됩니다. 이러한 방식으로 인해 원래 원본에 관계없이 모든 데이터를 함께 분석할 수 있습니다. Security Center는 Azure Monitor 로그와 통합 됩니다. 고객에 게 Kusto 쿼리를 사용 하 여 보안 이벤트 데이터를 액세스 하 고 다른 서비스의 데이터와 결합 하 수 있습니다.

다음 Azure [모니터링 솔루션](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) 이 아키텍처의 일부로 포함 됩니다.
-   [Active Directory 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 검사 솔루션은 일정한 간격으로 서버 환경의 위험 및 상태를 평가합니다. 이 솔루션은 배포된 서버 인프라 관련 우선 순위가 지정된 권장 사항 목록을 제공합니다.
- [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 검사 솔루션은 일정한 간격으로 서버 환경의 위험 및 상태를 평가합니다. 이 솔루션은 배포된 서버 인프라 관련 우선 순위가 지정된 권장 사항 목록을 고객에게 제공합니다.
- [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포된 에이전트의 수와 에이전트의 지리적 분산을 보고합니다. 또한 응답하지 않는 에이전트의 수와 작동 데이터를 제출하는 에이전트의 수도 보고합니다.
-   [활동 로그 분석](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 구독에서 활동 로그를 분석하는 데 도움을 줍니다.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)은 Runbook을 저장, 실행 및 관리합니다. 이 솔루션에서 Runbook은 SQL Database에서 로그를 수집하는 데 도움이 됩니다. 고객은 Automation [변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking) 솔루션을 사용하여 환경의 변경 내용을 쉽게 파악할 수 있습니다.

**Azure Monitor**: 고객은 [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)를 통해 성능을 추적하고 보안을 유지하고 추세를 파악할 수 있습니다. 조직에서는 Monitor를 사용하여 감사를 수행하고 경고를 작성하고 데이터를 보관할 수 있습니다. 또한 Azure 리소스의 API 호출을 추적할 수도 있습니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 데이터 흐름 다이어그램은 아래에 나와 있으며 [다운로드](https://aka.ms/nist171-dw-tm)할 수도 있습니다. 고객은 이 모델을 통해 수정 시 시스템 인프라에서 발생 가능한 위험 요소를 파악할 수 있습니다.

![NIST SP 800-171 준수 데이터 웨어하우스 위협 모델](images/nist171-dw-threat-model.png "NIST SP 800-171 준수 데이터 웨어하우스 위협 모델")

## <a name="compliance-documentation"></a>규정 준수 설명서
[Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 고객 책임 매트릭스](https://aka.ms/nist171-crm)에는 NIST SP 800-171에서 요구하는 모든 보안 제어가 나와 있습니다. 이 매트릭스는 각 제어의 구현이 Microsoft, 고객 또는 둘 모두의 책임인지 여부를 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - NIST SP 800-171 데이터 웨어하우스 컨트롤 구현 매트릭스](https://aka.ms/nist171-dw-cim)는 데이터 웨어하우스 아키텍처를 통해 적용되는 NIST SP 800-171 컨트롤에 대한 정보를 제공합니다. 이 매트릭스에는 포함되는 각 컨트롤의 요구 사항을 구현이 충족하는 방식에 대한 상세 설명이 포함되어 있습니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="vpn-and-expressroute"></a>VPN 및 ExpressRoute
이 데이터 웨어하우스 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 보안 VPN 터널 또는 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 구성해야 합니다. VPN 또는 ExpressRoute를 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 계층을 추가할 수 있습니다.

Azure를 통해 보안 VPN 터널을 구현하면 온-프레미스 네트워크와 Azure 가상 네트워크 간의 가상 사설 연결을 만들 수 있습니다. 이 연결은 인터넷을 통해 이루어지며, 고객은 이 연결을 사용하여 고객의 네트워크와 Azure 간에 암호화된 링크 내에서 정보를 안전하게 "터널링"할 수 있습니다. 사이트 간 VPN은 수십 년 동안 모든 규모의 기업에서 배포해 온 안전하고 완성도 높은 기술입니다. [IPsec 터널 모드](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))는 이 옵션에서 암호화 메커니즘으로 사용됩니다.

VPN 터널 내의 트래픽은 사이트 간 VPN을 사용하여 인터넷을 통과하므로 Microsoft는 훨씬 더 안전한 또 다른 연결 옵션을 제공합니다. ExpressRoute는 Azure 및 온-프레미스 위치 또는 Exchange 호스팅 공급자 간의 전용 WAN 링크입니다. ExpressRoute 연결은 고객이 사용하는 통신 서비스 공급자에 직접 연결됩니다. 그러므로 데이터가 인터넷을 통해 전송되지 않으며 인터넷에 노출되지 않습니다. 이러한 연결은 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

온-프레미스 네트워크를 Azure로 확장하는 보안 하이브리드 네트워크를 구현하는 모범 사례를 [사용할 수 있습니다](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>추출-변환-로드 프로세스
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)는 별도의 ETL 또는 가져오기 도구를 사용할 필요 없이 데이터를 SQL Data Warehouse에 로드할 수 있습니다. PolyBase를 사용하면 T-SQL 쿼리를 통해 데이터에 액세스할 수 있습니다. Microsoft 비즈니스 인텔리전스 및 분석 스택, 그리고 SQL Server와 호환되는 타사 도구를 PolyBase와 함께 사용할 수 있습니다.

### <a name="azure-ad-setup"></a>Azure AD 설정
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 환경과 상호 작용하는 직원에 대한 배포 및 프로비전 액세스를 관리하는 데 필수적입니다. [클릭 네 번](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)으로 온-프레미스 Active Directory를 간편하게 Azure AD와 통합할 수 있습니다. 또한 고객은 배포된 Active Directory 인프라(도메인 컨트롤러)를 Azure AD에 연결할 수도 있습니다. 이렇게 하려면 배포된 Active Directory 인프라를 Azure AD 포리스트의 하위 도메인으로 지정합니다.

### <a name="optional-services"></a>선택적 서비스
Azure는 서식이 지정되거나 지정되지 않은 데이터의 저장과 준비를 지원하는 다양한 서비스를 제공합니다. 다음 참조 서비스는 고객 요구 사항에 따라 이 참조 아키텍처에 추가할 수 있습니다.
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)는 복잡한 하이브리드 ETL(추출-변환-로드) 및 데이터 통합 프로젝트를 위해 구축된 관리되는 클라우드 서비스입니다. Data Factory에는 데이터를 쉽게 추적하고 찾을 수 있는 기능이 포함되어 있습니다. 시각화 및 모니터링 도구를 통해 데이터가 수신된 시간과 전송된 위치를 파악할 수 있습니다. 고객은 서로 다른 데이터 저장소의 데이터를 수집하는 데이터 기반 워크플로인 파이프라인을 만들고 예약할 수 있습니다. 그리고 파이프라인을 사용하여 내부 및 외부 원본의 데이터를 수집할 수 있습니다. 그런 다음 데이터를 처리하여 SQL Data Warehouse와 같은 데이터 저장소로 전송할 출력용으로 변환할 수 있습니다.
- 고객은 [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)에서 구조화되지 않은 데이터를 준비하여 운영 및 예비 분석용으로 모든 크기/유형/수집 속도의 데이터를 한 곳에서 캡처할 수 있습니다. Azure Data Lake에는 데이터를 추출하고 변환할 수 있는 기능이 있습니다. Data Lake 저장소는 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환됩니다. 또한 SQL Data Warehouse 등의 다른 Azure 서비스와도 원활하게 통합됩니다.

## <a name="disclaimer"></a>고지 사항

 - 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
 - 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
 - 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
 - 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
 - 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
 - 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
