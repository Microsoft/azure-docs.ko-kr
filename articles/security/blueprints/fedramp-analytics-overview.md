---
title: Azure 보안 및 규정 준수 청사진 - Analytics for FedRAMP
description: Azure 보안 및 규정 준수 청사진 - Analytics for FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586290"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure 보안 및 규정 준수 청사진: FedRAMP에 대 한 분석

## <a name="overview"></a>개요

[FedRAMP(Federal Risk and Authorization Management Program)](https://www.fedramp.gov/)는 미국 정부 차원 프로그램으로 클라우드 제품 및 서비스에 대한 보안 평가, 권한 부여 및 연속 모니터링에 대한 표준화된 접근 방법을 제공합니다. 이 Azure 보안 및 규정 준수 청사진은 FedRAMP 하이 컨트롤의 하위 집합을 구현하는 데 도움이 되는 Microsoft Azure 분석 아키텍처를 제공하는 방법에 대한 지침을 제공합니다. 이 솔루션은 공통 참조 아키텍처를 위한 Azure 리소스를 배포하고 구성하는 방법에 대한 지침을 제공하고, 고객이 특정 보안 및 규정 준수 요구 사항에 부합할 수 있는 방법을 보여주고, 고객이 Azure에서 자체 분석 솔루션을 구축 및 구성하기 위한 기초 역할을 합니다.

이 참조 아키텍처, 관련 컨트롤 구현 가이드 및 위협 모델은 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이 되며, 프로덕션 환경에서 있는 그대로 사용하면 안 됩니다. 애플리케이션을 수정 없이 이러한 환경에 배포하는 것은 FedRAMP 높음 기준의 요구 사항을 온전히 충족하기에는 부족합니다. 다음 사항에 유의하세요.
- 아키텍처는 고객이 FedRAMP 규격 방식으로 워크로드를 Azure에 배포할 수 있도록 지원하기 위한 기준을 제공합니다.
- 요구 사항이 각 고객의 특정 구현에 따라 달라질 수 있으므로 고객은 이 아키텍처를 사용하여 구축된 솔루션에 대한 적절한 보안 및 규정 준수 평가를 수행해야 합니다.

## <a name="architecture-diagram-and-components"></a>아키텍처 다이어그램 및 구성 요소

이 솔루션은 고객이 자신의 분석 도구를 구축할 수 있는 분석 플랫폼을 제공합니다. 참조 아키텍처에서는 고객이 SQL/데이터 관리자에 의한 대량 데이터 가져오기 또는 운영 사용자에 의한 운영 데이터 업데이트를 통해 데이터를 입력하는 일반적인 사용 사례를 설명합니다. 두 작업 스트림은 모두 데이터를 SQL Database에 가져오는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)를 통합합니다. 고객이 Azure Portal을 통해 Azure Functions를 구성하여 각 고객 고유의 분석 요구 사항에 고유한 가져오기 작업을 처리해야 합니다.

Microsoft Azure는 고객을 위해 다양한 보고 및 분석 서비스를 제공합니다. 그러나 이 솔루션은 Azure Analysis Services를 Azure SQL Database와 통합하여 데이터를 빠르게 탐색하고 더 효율적인 고객 데이터 모델링을 통해 더 빠른 결과를 제공합니다. Azure Analytics Services는 데이터 세트 간의 새로운 관계를 검색하여 쿼리 속도를 높이기 위한 기계 학습의 한 형태입니다. 데이터가 여러 통계 함수를 통해 학습되면 쿼리 워크로드를 분산하고 응답 시간을 줄이기 위해 최대 7개의 추가 쿼리 풀(고객 서버를 포함하여 총 8개)을 동일한 테이블 형식 모델과 동기화할 수 있습니다.

향상된 분석 및 보고를 위해 columnstore 인덱스를 사용하여 SQL Database를 구성할 수 있습니다. Azure Analytics Services와 SQL Databases 둘 다 고객의 사용량에 따라 확장/축소하거나 완전히 종료할 수 있습니다. 모든 SQL 트래픽은 자체 서명된 인증서를 포함하여 SSL로 암호화됩니다. Azure는 강화된 보안을 위해 신뢰할 수 있는 인증 기관을 사용하는 것이 가장 좋습니다.

데이터가 Azure SQL Database에 업로드되고 Azure Analysis Services에서 학습되면 운영 사용자와 SQL/데이터 관리자는 모두 Power BI를 사용하여 이러한 데이터를 요약합니다. Power BI는 직관적으로 데이터를 표시하고, 더 많은 통찰력을 얻기 위해 여러 데이터 세트에서 정보를 가져옵니다. 높은 수준의 적응성과 Azure SQL Database와의 손쉬운 통합에 따라 고객은 비즈니스 요구 사항에 따라 다양한 시나리오를 처리하도록 구성할 수 있습니다.

전체 솔루션은 계정 고객이 Azure Portal에서 구성하는 Azure Storage를 기반으로 합니다. Azure Storage는 스토리지 서비스 암호화를 통해 모든 데이터를 암호화하여 미사용 데이터의 기밀을 유지합니다.  GRS(지역 중복 저장소)는 수백 마일 떨어진 별도의 위치에 보조 복사본이 저장되므로 고객의 주 데이터 센터에서 발생하는 부작용으로 인해 데이터가 손실되지 않도록 합니다.

이 아키텍처는 강화된 보안을 위해 Azure Active Directory 및 Azure Key Vault를 사용하여 리소스를 관리합니다. 시스템 상태는 Azure Monitor를 통해 모니터링됩니다. 고객은 로그를 캡처하고 쉽게 탐색할 수 있는 단일 대시보드에 시스템 상태를 표시하도록 두 모니터링 서비스를 구성합니다.

Azure SQL Database는 일반적으로 보안 VPN 또는 ExpressRoute 연결을 통해 Azure SQL Database에 액세스하도록 구성된 로컬 머신에서 실행되는 SSMS(SQL Server Management Studio)를 통해 관리됩니다. **Azure에서는 관리 및 참조 아키텍처 리소스 그룹에 데이터 가져오기를 위해 VPN 또는 Azure ExpressRoute 연결을 구성하는 것이 좋습니다**.

![Analytics for FedRAMP에 대한 참조 아키텍처 다이어그램](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics for FedRAMP에 대한 참조 아키텍처 다이어그램")

### <a name="roles"></a>역할
분석 청사진은 세 가지 일반 사용자 형식인 운영 사용자, SQL/데이터 관리자 및 시스템 엔지니어를 사용하여 시나리오를 설명합니다. Azure RBAC(역할 기반 액세스 제어)는 기본 제공 사용자 지정 역할을 통해 정확한 액세스 관리를 구현할 수 있도록 합니다. [역할 기반 액세스 제어](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)를 구성하고 [미리 정의된 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)을 설명 및 구현하는 데 사용할 수 있는 리소스가 제공됩니다.

#### <a name="systems-engineer"></a>시스템 엔지니어
시스템 엔지니어는 Azure에 대한 고객 구독을 소유하고 Azure Portal을 통해 솔루션 배포를 구성합니다.

#### <a name="sqldata-administrator"></a>SQL/데이터 관리자
SQL/데이터 관리자는 Azure SQL 데이터베이스에 업로드하기 위한 대량 데이터 가져오기 함수 및 운영 데이터 업데이트 함수를 설정합니다. SQL/데이터 관리자는 데이터베이스의 운영 데이터 업데이트에 대한 책임은 없지만 Power BI를 통해 데이터를 볼 수 있게 될 것입니다.

#### <a name="operational-user"></a>운영 사용자
운영 사용자는 데이터를 정기적으로 업데이트하고 일상적으로 생성되는 데이터를 소유합니다. 또한 운영 사용자는 Power BI를 통해 결과를 해석합니다.

### <a name="azure-services"></a>Azure 서비스

이 솔루션에서는 다음과 같은 Azure 서비스를 사용합니다. 배포 아키텍처에 대한 세부 정보는 [배포 아키텍처](#deployment-architecture) 섹션에 있습니다.
- Azure 기능
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- Azure 모니터 (로그)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI 대시보드

## <a name="deployment-architecture"></a>배포 아키텍처
다음 섹션에서는 개발 및 구현 요소를 자세히 설명합니다.

![대체 텍스트](images/fedramp-analytics-components.png?raw=true "FedRAMP 분석 구성 요소 다이어그램")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 대부분의 프로그래밍 언어를 통해 클라우드에서 작은 코드를 실행 하기 위한 시나리오도 있습니다. 이 솔루션의 함수는 Azure Storage와 통합되어 자동으로 고객 데이터를 클라우드로 가져와서 다른 Azure 서비스와의 통합을 지원합니다. Functions는 쉽게 확장 가능하며 실행될 때만 비용이 발생합니다.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) 엔터프라이즈 데이터 모델링 및 Azure 데이터 플랫폼 서비스와의 통합을 제공 합니다. Azure Analysis Service는 여러 소스의 데이터를 단일 데이터 모델에 결합하여 대량의 데이터를 검색하는 속도를 높입니다.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) 분석을 제공 하 고 해당 데이터 처리 활동에서 대 한 이해력을 가져오려고 시도 하는 고객을 위한 기능을 보고 합니다.

### <a name="networking"></a>네트워킹
**네트워크 보안 그룹**: [Nsg](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 배포 된 리소스 및 서비스에서 트래픽을 관리할 수 있도록 설정 되어 있습니다. 네트워크 보안 그룹은 deny-by-default 스키마로 설정되며 미리 구성된 ACL(액세스 제어 목록)에 포함된 트래픽만 허용합니다.

각 NSG에는 솔루션이 안전하고 올바르게 작동할 수 있도록 특정 포트 및 프로토콜이 열려 있습니다. 또한 각 NSG에 대해 다음과 같은 구성을 사용합니다.
  - [진단 로그 및 이벤트](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)는 저장소 계정에 사용되고 저장됩니다.
  - [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) NSG의 진단 로그에 연결 되어 있습니다.

### <a name="data-at-rest"></a>미사용 데이터
이 아키텍처는 암호화, 데이터베이스 감사 및 다른 방법을 통해 미사용 데이터를 보호합니다.

**데이터 복제** Azure Government에는 두 가지 [데이터 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy) 옵션이 있습니다.
 - 데이터 복제에 대한 기본 설정인 **GRS(지역 중복 저장소)** 는 고객 데이터를 주 지역 외부에 있는 별도의 데이터 센터에 비동기적으로 저장합니다. 이렇게 하면 주 데이터 센터의 데이터 전체가 손실되더라도 데이터를 복구할 수 있습니다.
 - **LRS(로컬 중복 스토리지)** 는 Azure Storage 계정을 통해 구성할 수도 있습니다. LRS는 저장소 배율 단위 이내의 데이터를 복제하며, 이 데이터는 고객이 자신의 계정을 만드는 지역과 동일한 지역에 호스트됩니다. 주 저장소 배율 단위에 장애가 발생해도 백업 데이터가 손실되지 않도록 모든 데이터가 동시에 복제됩니다.

**Azure Storage**: 암호화된 미사용 데이터 요구 사항을 충족하기 위해, 이 참조 아키텍처에서 배포된 모든 서비스는 [스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용하여 데이터를 저장하는 [Azure Storage](https://azure.microsoft.com/services/storage/)를 활용합니다.

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

### <a name="logging-and-audit"></a>로깅 및 감사
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)는 활동 로그, 메트릭 및 진단 데이터를 포함한 모니터링 데이터를 전체적으로 표시하여 고객이 시스템 상태를 전체적으로 이해할 수 있게 해줍니다.  
[Azure Monitor 로그](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) 시스템 상태 뿐만 아니라 시스템 및 사용자 작업에 대 한 광범위 로깅을 제공 합니다. Azure 및 온-프레미스 환경의 리소스에서 생성된 데이터를 수집하고 분석합니다.
- **활동 로그**: [활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 구독에 있는 리소스에서 수행된 작업에 대한 인사이트를 제공합니다.
- **진단 로그**: [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)는 모든 리소스에서 내보낸 모든 로그를 포함합니다. 이러한 로그에는 Windows 이벤트 시스템 로그, Azure Blob Storage, 테이블 및 큐 로그가 포함됩니다.
- **방화벽 로그.**: Application Gateway는 전체 진단 및 액세스 로그를 제공합니다. 방화벽 로그는 WAF가 활성화된 Application Gateway 리소스에 사용할 수 있습니다.
- **로그 보관**: 모든 진단 로그는 정의된 보존 기간이 2일인 보관을 위해 암호화된 중앙 집중식 Azure Storage 계정에 기록됩니다. 이러한 로그는 처리, 저장 및 대시보드 보고에 대 한 Azure Monitor 로그에 연결 합니다.

또한 이 아키텍처의 일부로 포함된 모니터링 솔루션은 다음과 같습니다.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Azure Automation 솔루션은 Runbook을 저장, 실행 및 관리합니다.
-   [보안 및 감사](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): 보안 및 감사 대시보드는 보안 도메인, 주목할 만한 문제, 탐지, 위협 인텔리전스 및 일반적인 보안 쿼리에 대한 메트릭을 제공하여 리소스의 보안 상태에 대한 높은 수준의 인사이트를 제공합니다.
-   [SQL 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): SQL 상태 확인 솔루션은 일정한 간격으로 서버 환경의 위험과 상태를 평가하고, 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 고객에게 제공합니다.
-   [Azure 활동 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): 활동 로그 분석 솔루션은 고객에 대한 모든 구독에서 활동 로그를 분석하는 데 도움을 줍니다.

### <a name="identity-management"></a>ID 관리
-   애플리케이션에 대한 인증은 Azure AD를 사용하여 수행됩니다. 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)을 참조하세요. 또한 데이터베이스 열 암호화는 Azure AD를 사용하여 Azure SQL Database에 대해 애플리케이션을 인증합니다. 자세한 내용은 [SQL Database의 중요 데이터 보호](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)를 참조하세요.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 미치는 잠재적 취약성을 탐지하고, 조직의 ID와 관련하여 검색된 의심스러운 작업에 대한 자동화된 대응을 구성하며, 의심스러운 인시던트를 조사하여 이를 해결하기 위한 적절한 조치를 수행합니다.
-   [Azure RBAC(Role-based Access Control)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)은 Azure에 초점을 맞춘 액세스 관리를 지원합니다. 구독 액세스는 구독 관리자로 제한됩니다.

Azure SQL Database의 보안 기능 사용에 대한 자세한 내용은 [Contoso Clinic 데모 애플리케이션](https://github.com/Microsoft/azure-sql-security-sample) 샘플을 참조하세요.

### <a name="security"></a>보안
**비밀 관리**: 이 솔루션은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키와 비밀을 관리합니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다.

## <a name="guidance-and-recommendations"></a>지침 및 권장 사항

### <a name="expressroute-and-vpn"></a>ExpressRoute 및 VPN
이 데이터 분석 참조 아키텍처의 일부로 배포된 리소스에 대한 연결을 안전하게 설정하도록 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 보안 VPN 터널을 구성해야 합니다. ExpressRoute 연결은 인터넷을 통해 수행되지 않으므로 인터넷을 통한 일반적인 연결보다 높은 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공합니다. ExpressRoute 또는 VPN을 적절히 설정하면 고객이 전송 중인 데이터에 대한 보호 레이어를 추가할 수 있습니다.

### <a name="azure-active-directory-setup"></a>Azure Active Directory 설정
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)는 환경과 상호 작용하는 직원에 대한 배포 및 프로비전 액세스를 관리하는 데 필수적입니다. 기존의 Windows Server Active Directory는 [네 번의 클릭](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)으로 AAD와 통합될 수 있습니다. 고객은 배포된 Active Directory 인프라를 AAD 포리스트의 하위 도메인으로 만들어 배포된 Active Directory 인프라(도메인 컨트롤러)를 기존 AAD에 연결할 수도 있습니다.

### <a name="additional-services"></a>서비스
#### <a name="iaas---vm-considerations"></a>IaaS-VM 고려 사항
이 PaaS 솔루션은 Azure IaaS VM을 포함하고 있지 않습니다. 고객은 여러 PaaS 서비스를 실행하는 Azure VM을 만들 수 있습니다. 이 경우 특정 기능 및 비즈니스 연속성 및 Azure Monitor 로그에 대 한 서비스를 활용할 수 있습니다.

##### <a name="business-continuity"></a>비즈니스 연속성
- **고가용성**: 서버 워크 로드에서 그룹화 되는 [가용성 집합](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure에서 가상 머신의 고가용성을 보장 하도록 합니다. 계획되거나 계획되지 않은 유지 관리 이벤트 중에 99.95% Azure SLA를 충족하는 가상 머신을 하나 이상 사용할 수 있습니다.

- **Recovery Services 자격 증명 모음**: [Recovery Services 자격 증명 모음](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview)은 백업 데이터를 저장하며, 이 아키텍처에 있는 Azure Virtual Machines의 모든 구성을 보호합니다. Recovery Services 자격 증명 모음을 사용하면 전체 VM을 복원하지 않고도 IaaS VM에서 파일과 폴더를 복원할 수 있으므로 복원 시간이 단축됩니다.

##### <a name="monitoring-solutions"></a>모니터링 솔루션
-   [AD 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Active Directory 상태 확인 솔루션은 표준 간격으로 서버 환경의 위험과 상태를 평가하고 배포된 서버 인프라에 관련된 권장 사항의 우선 순위 목록을 제공합니다.
-   [맬웨어 방지 평가](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): 맬웨어 방지 솔루션은 맬웨어, 위협 및 보호 상태를 보고합니다.
-   [업데이트 관리](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): 업데이트 관리 솔루션을 사용하면 고객이 사용 가능한 업데이트 상태 및 필수 업데이트 설치 프로세스를 포함하여 운영 체제 보안 업데이트를 관리할 수 있습니다.
-   [에이전트 상태](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): 에이전트 상태 솔루션은 배포되는 에이전트 수와 해당 지리적 분포, 응답이 없는 에이전트 수 및 운영 데이터를 제출하는 에이전트 수를 보고합니다.
-   [변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking): 변경 내용 추적 솔루션을 사용하면 고객이 환경의 변경 내용을 쉽게 파악할 수 있습니다.

##### <a name="security"></a>보안
- **맬웨어 방지**: Virtual Machine용 [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)는 보호되는 Virtual Machines에서 악성 또는 원치 않는 소프트웨어가 사용자 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 알림을 통해 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능을 제공합니다.
- **패치 관리**: 이 참조 아키텍처의 일부로 배포된 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동 업데이트를 받도록 구성됩니다. 또한 이 솔루션에는 필요한 경우, 가상 머신을 패치하기 위해 업데이트된 배포를 만들 수 있는 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) 서비스가 포함되어 있습니다.

#### <a name="azure-commercial"></a>Azure Commercial
이 데이터 분석 아키텍처는 [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/) 환경에 배포할 용도로 제작되지는 않았지만, 이 참조 아키텍처에 설명된 서비스와 Azure Commercial 환경에서만 사용할 수 있는 추가 서비스를 통해 비슷한 목표를 달성할 수 있습니다. Azure Commercial은 보통 영향 수준에서 FedRAMP JAB P-ATO를 유지하며, 따라서 정부 기관 및 파트너가 Azure Commercial 환경을 활용하여 중요도 보통인 정보를 클라우드에 배포할 수 있습니다.

Azure Commercial은 대량의 데이터에서 인사이트를 끌어낼 수 있는 다양한 분석 서비스를 제공합니다.
-   [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/)의 구성 요소인 [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio)는 하나 이상의 데이터 원본으로 예측 분석 모델을 개발합니다. 통계 함수는 여러 반복에 사용되어 Power BI 같은 애플리케이션에서 사용할 수 있는 효과적인 모델을 생성합니다.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)를 사용하면 운영 및 예비 분석을 위해 한 장소에서 원하는 크기, 유형 및 수집 속도의 데이터를 캡처할 수 있습니다. Azure Data Lake Store는 Hadoop 에코시스템의 오픈 소스 구성 요소 대부분과 호환되며 다른 Azure 서비스와 원활하게 통합됩니다.

## <a name="threat-model"></a>위협 모델

이 참조 아키텍처에 대한 DFD(데이터 흐름 다이어그램)는 [다운로드](https://aka.ms/blueprintanalyticsthreatmodel)에서 사용할 수 있거나 아래에 나와 있습니다.

## <a name="compliance-documentation"></a>규정 준수 설명서
[Azure 보안 및 규정 준수 청사진 – FedRAMP High 고객 책임 매트릭스](https://aka.ms/blueprinthighcrm)에는 FedRAMP High 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 마찬가지로, [Azure 보안 및 규정 준수 청사진 – FedRAMP Moderate 고객 책임 매트릭스](https://aka.ms/blueprintanalyticscimmod)에는 FedRAMP Moderate 기준에서 요구하는 모든 보안 컨트롤이 나열되어 있습니다. 두 문서는 각 컨트롤 구현이 Microsoft의 책임인지, 고객의 책임인지 아니면 둘의 공동 책임인지 자세히 설명합니다.

[Azure 보안 및 규정 준수 청사진 - FedRAMP High 컨트롤 구현 매트릭스](https://aka.ms/blueprintanalyticscimhigh) 및 [Azure 보안 및 규정 준수 청사진 - FedRAMP Moderate 컨트롤 구현 매트릭스](https://aka.ms/blueprintanalyticscimmod)는 포함된 각 컨트롤의 요구 사항을 구현에서 어떻게 충족하는지에 대한 자세한 설명을 포함하여 각 FedRAMP 기준에 대한 분석 아키텍처의 범위에 포함되는 컨트롤에 대한 정보를 제공합니다.

## <a name="disclaimer"></a>고지 사항

 - 이 문서는 오직 정보 제공을 목적으로 합니다. Microsoft는 이 문서의 정보에 관해 어떠한 명시적, 묵시적 또는 법적 보증도 하지 않습니다. 이 문서는 "있는 그대로" 제공됩니다. URL 및 기타 인터넷 웹 사이트 참조를 포함하여 본 문서에 명시된 정보 및 보기는 통지 없이 변경될 수 있습니다. 이 문서를 읽는 고객은 그 사용에 따른 위험을 감수합니다.
 - 이 문서는 Microsoft 제품 또는 솔루션의 지적 소유권에 대한 법적 권한을 고객에게 제공하지 않습니다.
 - 고객은 이 문서는 내부 참조용으로만 복사 및 사용할 수 있습니다.
 - 이 문서의 특정 권장 사항으로 인해 Azure에서 데이터, 네트워크 또는 계산 리소스 사용량이 증가할 수 있으며 이로 인해 고객의 Azure 라이선스 또는 구독 비용이 증가할 수 있습니다.
 - 이 아키텍처는 고객이 특정 요구 사항에 맞게 조정할 수 있는 기반이며 있는 프로덕션 환경에 그대로 사용해서는 안됩니다.
 - 이 문서는 참조용으로 작성되었으며 고객이 특정 규정 준수 요구 사항 및 규정을 충족할 수 있는 모든 수단을 정의하는 데 사용되어서는 안됩니다. 고객은 승인된 고객 구현에 대해 자체 조직에서 법률 지원을 받아야 합니다.
