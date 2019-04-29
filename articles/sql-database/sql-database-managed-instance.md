---
title: Azure SQL Database 고급 데이터 보안 개요 | Microsoft Docs
description: 이 항목에서는 Azure SQL Database 고급 데이터 보안에 대해 설명하고 작동 원리 및 Azure SQL Database의 단일 또는 풀링된 데이터베이스와 다른 점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 46c6972e20df69da236c151516d7d889f9db6084
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098543"
---
# <a name="use-sql-database-advanced-data-security-with-virtual-networks-and-near-100-compatibility"></a>가상 네트워크 및 거의 100%의 호환성으로 SQL Database 고급 데이터 보안 사용

Managed Instance는 Azure SQL Database의 새로운 배포 옵션입니다. 최신 SQL Server 온-프레미스(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 구현과 온-프레미스 SQL Server 고객이 편리하게 사용할 수 있는 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. Managed Instance 배포 모델을 사용하면 기존 SQL Server 고객은 애플리케이션 및 데이터베이스 변경을 최소화하고 온-프레미스 애플리케이션을 클라우드로 이동할 수 있습니다. 이와 동시에 Managed Instance 배포 옵션은 모든 PaaS 기능(자동 패치 및 버전 업데이트, [자동화된 백업](sql-database-automated-backups.md), [고가용성](sql-database-high-availability.md))을 유지하므로 관리 오버헤드와 TCO를 크게 줄일 수 있습니다.

> [!IMPORTANT]
> Managed Instance 배포 옵션을 현재 사용할 수 있는 지역의 목록은 [지원되는 지역](sql-database-managed-instance-resource-limits.md#supported-regions)을 참조하세요.

다음 다이어그램은 Managed Instance의 주요 기능을 설명합니다.

![주요 기능](./media/sql-database-managed-instance/key-features.png)

Managed Instance 배포 모델은 가능한 한 적은 마이그레이션 작업을 통해 많은 수의 앱을 온-프레미스 또는 IaaS, 자체 구축 또는 ISV 제공 환경에서 완전히 관리되는 PaaS 클라우드 환경으로 마이그레이션하려는 고객을 위해 설계되었습니다. Azure의 완전 자동화 [DMS(Database Migration Service)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)를 사용하면 온-프레미스 SQL Server를 Managed Instance로 옮길 수 있습니다. 여기에서는 SQL Server 온-프레미스와의 호환성이 제공되고 네이티브 VNet 지원을 통해 고객 인스턴스를 완벽하게 격리합니다.  Software Assurance를 사용하면 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 Managed Instance에서 기존 라이선스를 할인된 가격으로 교환할 수 있습니다.  Managed Instance는 클라우드에서 높은 보안과 풍부한 프로그래밍 기능 영역이 필요한 SQL Server 인스턴스를 위한 최상의 마이그레이션 대상입니다.

Managed Instance 배포 옵션의 목표는 단계별 릴리스 계획을 통해 최신 온-프레미스 SQL Server 버전과 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다.

Azure SQL Database 배포 옵션인 단일 데이터베이스, 풀링된 데이터베이스, 관리되는 인스턴스 및 가상 머신에서 호스트되는 SQL Server 중에서 결정하려면 [Azure에서 올바른 SQL Server 버전을 선택하는 방법](sql-database-paas-vs-sql-server-iaas.md)을 참조하세요.

## <a name="key-features-and-capabilities"></a>주요 특징 및 기능

Managed Instance는 Azure SQL Database와 SQL Server 데이터베이스 엔진 모두에서 사용할 수 있는 최상의 기능을 결합합니다.

> [!IMPORTANT]
> Managed Instance에는 온라인 작업, 자동 계획 수정 및 기타 엔터프라이즈 성능 향상을 비롯한 SQL Server 최신 버전의 모든 기능이 실행됩니다. 제공되는 기능에 대한 비교는 [기능 비교: Azure SQL Database 및 SQL Server](sql-database-features.md)를 참조하세요.

| **PaaS의 이점** | **비즈니스 연속성** |
| --- | --- |
|하드웨어를 구입하고 관리할 필요가 없음 <br>기본 인프라 관리를 위한 오버헤드가 없음 <br>신속한 프로비전 및 서비스 크기 조정 <br>자동 패치 적용 및 버전 업그레이드 <br>다른 PaaS 데이터 서비스와 통합 |99.99% 작동 시간 SLA  <br>기본 제공 [고가용성](sql-database-high-availability.md) <br>[자동화된 백업](sql-database-automated-backups.md)으로 보호되는 데이터 <br>고객이 구성 가능한 백업 보존 기간 <br>사용자가 시작하는 [백업](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[특정 시점 데이터베이스 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 기능 |
|**보안 및 규정 준수** | **관리**|
|격리된 환경([VNet 통합](sql-database-managed-instance-connectivity-architecture.md), 단일 테넌트 서비스, 전용 계산 및 스토리지) <br>[TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 인증](sql-database-aad-authentication.md), Single Sign-On 지원 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 서버 보안 주체(로그인)</a>(**공개 미리 보기**) <br>Azure SQL 데이터베이스와 동일한 표준 준수 <br>[SQL 감사](sql-database-managed-instance-auditing.md) <br>[위협 검색](sql-database-managed-instance-threat-detection.md) |서비스 프로비전 및 크기 조정을 자동화하는 Azure Resource Manager API <br>수동 서비스 프로비전 및 크기 조정을 위한 Azure Portal 기능 <br>데이터 마이그레이션 서비스

> [!IMPORTANT]
> Azure SQL Database (모든 배포 옵션)을 여러 규정 준수 표준에 대해 인증 받았습니다. 자세한 내용은 참조는 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 있는 SQL Database 규정 준수 인증의 최신 목록을 찾을 수 있습니다.

Managed Instance의 주요 기능을 다음 표에서 볼 수 있습니다.

|기능 | 설명|
|---|---|
| SQL Server 버전/빌드 | SQL Server 데이터베이스 엔진(안정적인 최신 버전) |
| 관리되는 자동 백업 | 예 |
| 기본 제공 인스턴스/데이터베이스 모니터링 및 메트릭 | 예 |
| 자동 소프트웨어 패치 | 예 |
| 최신 데이터베이스 엔진 기능 | 예 |
| 데이터베이스당 데이터 파일(행) 수 | 여러 접두사 |
| 데이터베이스당 로그 파일(로그) 수 | 1 |
| VNet - Azure Resource Manager 배포 | 예 |
| VNet - 클래식 배포 모델 | 아닙니다. |
| 포털 지원 | 예|
| 기본 제공 통합 서비스(SSIS) | 아니요 - SSIS는 [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)의 일부입니다. |
| 기본 제공 분석 서비스(SSAS) | 아니요 - SSAS는 별도의 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)입니다. |
| 기본 제공 보고 서비스(SSRS) | 아니요 - Power BI 또는 SSRS IaaS를 사용합니다. |
|||

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

Managed Instance의 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)은 유연성, 제어 기능, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 사용하면 워크로드 요구 사항에 따라 계산, 메모리 및 스토리지를 선택할 수 있습니다. 또한 vCore 모델은 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 통해 최대 30%까지 비용을 절약할 수 있습니다.

vCore 모델에서는 하드웨어 세대를 선택할 수 있습니다.

- **Gen4** 논리 CPU는 Intel E5-2673 v3(Haswell) 2.4GHz 프로세서, SSD 장착, 물리적 코어, 코어당 7GB RAM 및 vCore가 8~24개인 계산 규모를 기반으로 합니다.
- **Gen5** 논리 CPU는 Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서, 고속 NVMe SSD, 하이퍼 스레드 논리 코어 및 코어가 8~80개인 계산 규모를 기반으로 합니다.

하드웨어 세대 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)을 참조하세요.

## <a name="managed-instance-service-tiers"></a>Managed Instance 서비스 계층

Managed Instance는 두 개의 서비스 계층에서 사용할 수 있습니다.

- **범용**: 일반적인 성능 및 IO 대기 시간 요구 사항이 있는 애플리케이션용으로 설계됩니다.
- **중요 비즈니스용**: IO 대기 시간 요구 사항이 낮고 기본 유지 관리 작업으로 워크로드에 미치는 영향이 최소화된 애플리케이션용으로 설계되었습니다.

두 서비스 계층은 모두 99.99% 가용성을 보장하며 스토리지 크기와 계산 용량을 독립적으로 선택할 수 있습니다. Azure SQL Database의 고가용성 아키텍처에 대한 자세한 내용은 [고가용성 및 Azure SQL Database](sql-database-high-availability.md)를 참조하세요.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

다음 목록에서는 범용 서비스 계층의 주요 특징을 설명합니다.

- 일반적인 성능 요구 사항이 있는 대부분의 비즈니스 애플리케이션에 적합한 디자인
- 고성능 Azure Blob Storage(8TB)
- 신뢰할 수 있는 Azure Blob Storage 및 [Azure Service Fabric](../service-fabric/service-fabric-overview.md)에 기반한 [고가용성](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) 기본 제공

자세한 내용은 [범용 계층의 스토리지 레이어](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) 및 [Managed Instance(범용)에 대한 스토리지 성능 모범 사례 및 고려 사항](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)을 참조하세요.

서비스 계층 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)을 참조하세요.

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

중요 비즈니스용 서비스 계층은 IO 요구 사항이 높은 애플리케이션용으로 빌드됩니다. 여러 개의 격리된 복제본을 사용하여 장애에 대한 최고의 복원력을 제공합니다.

다음 목록에서는 중요 비즈니스용 서비스 계층의 주요 특징을 설명합니다.

- 최고의 성능과 HA 요구 사항을 가진 대부분의 비즈니스 애플리케이션용으로 설계됨
- 초고속 로컬 SSD 스토리지(4세대의 최대 1TB 및 5세대의 최대 4TB)와 함께 제공됨
- [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 및 [Azure Service Fabric](../service-fabric/service-fabric-overview.md)을 기반으로 [고가용성](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 기본 제공
- 보고 및 기타 읽기 전용 워크로드에 사용할 수 있는 추가적인 [읽기 전용 데이터베이스 복제본](sql-database-read-scale-out.md) 기본 제공
- 고성능 요구 사항이 있는 워크로드에 사용할 수 있는 [메모리 내 OLTP](sql-database-in-memory.md)  

서비스 계층 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)을 참조하세요.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

Managed Instance 배포 옵션은 Azure 클라우드와 SQL Server 데이터베이스 엔진에서 제공하는 고급 보안 기능을 결합합니다.

### <a name="managed-instance-security-isolation"></a>Managed Instance 보안 격리

Managed Instance는 Azure 클라우드의 다른 테넌트와 추가로 격리되는 보안을 제공합니다. 보안 격리에는 다음이 포함됩니다.

- Azure ExpressRoute 또는 VPN Gateway를 사용하여 [기본 가상 네트워크 구현](sql-database-managed-instance-connectivity-architecture.md) 및 온-프레미스 환경에 연결
- 기본 배포의 경우에 SQL 끝점은 개인 Azure 또는 하이브리드 네트워크에서 안전 하 게 연결을 허용 하는 개인 IP 주소를 통해서만 노출 됩니다.
- 전용 기본 인프라(컴퓨팅, 스토리지)를 제공하는 단일 테넌트

다음 다이어그램은 애플리케이션에 대한 다양한 연결 옵션을 설명합니다.

![고가용성](./media/sql-database-managed-instance/application-deployment-topologies.png)  

서브넷 수준의 VNet 통합 및 네트워킹 정책 적용에 대한 자세한 내용은 [Managed Instance에 대한 VNet 아키텍처](sql-database-managed-instance-connectivity-architecture.md) 및 [애플리케이션을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.

> [!IMPORTANT]
> 동일한 서브넷에서 보안 요구 사항에 따라 허용되는 곳마다 여러 개의 관리되는 인스턴스를 배치하면 추가적인 이점을 얻을 수 있습니다. 인스턴스를 동일한 서브넷에 배치하면 네트워킹 인프라 유지 관리가 크게 단순화되고 인스턴스 프로비저닝 시간이 단축됩니다. 긴 프로비저닝 기간은 첫 번째로 관리되는 인스턴스를 서브넷에 배포하는 비용과 관련되기 때문입니다.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database 보안 기능

Azure SQL Database는 데이터를 보호하는 데 사용할 수 있는 일단의 고급 보안 기능을 제공합니다.

- [Managed Instance 감사](sql-database-managed-instance-auditing.md)는 데이터베이스 이벤트를 추적하고 Azure 스토리지 계정에 있는 감사 로그 파일에 이를 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.
- 이동 중인 데이터 암호화 - Managed Instance는 전송 계층 보안을 사용하여 이동 중인 데이터를 암호화함으로써 데이터를 보호합니다. 전송 계층 보안 외에도, Managed Instance 배포 옵션은 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 전송 중인 데이터, 미사용 데이터, 쿼리를 처리 중인 데이터를 보호하는 기능을 제공합니다. Always Encrypted는 중요한 데이터의 도용을 비롯한 위반에 대해 최상의 데이터 보안을 제공하는 업계 최고의 기능입니다. 예를 들어 Always Encrypted를 사용하여 쿼리를 처리하는 동안에도 신용 카드 번호가 데이터베이스에 암호화되어 저장됩니다. 또한 해당 데이터를 처리해야 하는 권한이 부여된 직원 또는 애플리케이션에 의해 사용 시점에 암호를 해독할 수 있습니다.
- [위협 검색](sql-database-managed-instance-threat-detection.md)은 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 감지하는 서비스에 내장된 추가적인 보안 인텔리전스 계층을 제공하여 [감사](sql-database-managed-instance-auditing.md) 기능을 보완합니다. 의심스러운 활동, 잠재적 취약성 및 SQL 삽입 공격은 물론 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. 위협 검색 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 볼 수 있으며 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.  
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)은 권한이 없는 사용자에게 중요한 데이터를 마스킹하여 데이터 노출을 제한합니다. 동적 데이터 마스킹을 수행하면 애플리케이션 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 동적 데이터 마스킹은 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다. 이때 데이터베이스의 데이터 자체는 변경되지 않습니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security)을 통해 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. RLS(행 수준 보안)는 애플리케이션의 보안 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 소속 부서와 관련된 데이터 행에만 액세스하게 하거나 데이터 액세스를 관련 데이터로 제한할 수 있습니다.
- [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 Managed Instance 데이터 파일을 암호화하고, 이를 미사용 데이터 암호화라고 합니다. TDE는 데이터 및 로그 파일에 대한 실시간 I/O 암호화 및 암호 해독을 수행합니다. 암호화에는 복구 중에 사용 가능하도록 데이터베이스 부트 레코드에 저장된 DEK(데이터베이스 암호화 키)가 사용됩니다. 투명한 데이터 암호화를 사용하여 Managed Instance의 모든 데이터베이스를 보호할 수 있습니다. TDE는 스토리지 미디어의 도용으로부터 보호하기 위해 많은 규정 준수 표준에 필요한 정지된 암호화 기술로써 SQL Server에서 입증되었습니다.

암호화된 데이터베이스를 Managed Instance로 마이그레이션하는 작업은 Azure DMS(Database Migration Service) 또는 네이티브 복원을 통해 지원됩니다. 네이티브 복원을 사용 하 여 암호화 된 데이터베이스를 마이그레이션하려는 경우 기존 TDE 인증서의 온-프레미스 SQL Server 또는 SQL Server에서 가상 컴퓨터에서 관리 되는 인스턴스 마이그레이션에 필요한 단계입니다. 마이그레이션 옵션에 대한 자세한 내용은 [SQL Server 인스턴스를 Managed Instance로 마이그레이션](sql-database-managed-instance-migrate.md)을 참조하세요.

## <a name="azure-active-directory-integration"></a>Azure Active Directory 통합

Managed Instance 배포 옵션은 기존 SQL 서버 데이터베이스 엔진 로그인 및 AAD(Azure Active Directory)와 통합된 로그인을 지원합니다. Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)는 온-프레미스 환경에서 사용 중인 온-프레미스 데이터베이스 로그인의 Azure 클라우드 버전입니다. Azure AD 서버 보안 주체(로그인)를 사용하면 동일한 Managed Instance 내에서 데이터베이스 간 쿼리를 비롯한 모든 인스턴스 수준 작업을 수행할 수 있는 실제 인스턴스 범위의 보안 주체로 Azure Active Directory 테넌트의 사용자 및 그룹을 지정할 수 있습니다.

Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)를 만들기 위한 새 구문인 **FROM EXTERNAL PROVIDER**가 소개되었습니다. 구문에 대한 자세한 내용은 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>을 참조하고 [Managed Instance에 대한 Azure Active Directory 관리자 프로비전](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)을 검토하세요.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication

Managed Instance 배포 옵션을 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md))을 제공하여 SSO(Single Sign-On) 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시킵니다.

### <a name="authentication"></a>Authentication

Managed Instance 인증은 사용자가 데이터베이스에 연결할 때 자신의 ID를 증명하는 방법을 나타냅니다. SQL Database는 두 가지 인증 유형을 지원합니다.  

- **SQL 인증**:

  이 인증 방법은 사용자 이름과 암호를 사용합니다.
- **Azure Active Directory 인증**:

  이 인증 방법은 Azure Active Directory에서 관리하는 ID를 사용하며, 관리되는 도메인과 통합된 도메인에 대해 지원됩니다. [가능한 경우](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory 인증(통합 보안)을 사용합니다.

### <a name="authorization"></a>권한 부여

권한 부여는 사용자가 Azure SQL Database에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 역할 멤버 자격과 개체 수준 권한으로 제어합니다. Managed Instance는 SQL Server 2017과 동일한 권한 부여 기능을 갖고 있습니다.

## <a name="database-migration"></a>데이터베이스 마이그레이션

Managed Instance는 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스를 마이그레이션하는 사용자 시나리오를 대상으로 합니다. Managed Instance는 여러 데이터베이스 마이그레이션 옵션을 지원합니다.

### <a name="back-up-and-restore"></a>백업 및 복원  

마이그레이션 방식에서는 Azure Blob Storage에 SQL을 백업합니다. Azure Storage Blob에 저장된 백업은 [T-SQL RESTORE 명령](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)을 사용하여 Managed Instance에 직접 복원할 수 있습니다.

- Wide World Importers 표준 데이터베이스 백업 파일을 복원하는 방법을 보여주는 빠른 시작은 [Managed Instance에 백업 파일 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요. 이 빠른 시작에서는 백업 파일을 Azure Blob 저장소에 업로드하고 SAS(보안 공유 액세스 서명) 키를 사용하여 보호해야 한다는 것을 보여줍니다.
- URL에서 복원하는 방법에 대한 자세한 내용은 [URL에서 네이티브 복원](sql-database-managed-instance-migrate.md#native-restore-from-url)을 참조하세요.

> [!IMPORTANT]
> Managed Instance에서 백업은 다른 Managed Instance로 복원될 수만 있습니다. 온-프레미스 SQL Server 또는 단일 데이터베이스/탄력적 풀로 복원할 수 없습니다.

### <a name="data-migration-service"></a>데이터 마이그레이션 서비스

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure VM의 Azure SQL Database(단일 데이터베이스, 탄력적 풀의 풀링된 데이터베이스 및 Managed Instance의 인스턴스 데이터베이스) 및 SQL Server로 이동하는 데 필요한 작업을 간소화합니다. [DMS를 사용하여 온-프레미스 데이터베이스를 Managed Instance로 마이그레이션하는 방법](https://aka.ms/migratetoMIusingDMS)을 참조하세요.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

Managed Instance 배포 옵션의 목표는 서비스의 일반 공급이 시작될 때까지 단계별로 제공되는 온-프레미스 SQL Server와 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 기능 및 비교 목록은 [SQL Database 기능 비교](sql-database-features.md)를 참조하고, Managed Instance와 SQL Server 간의 T-SQL 차이점 목록은 [Managed Instance 및 SQL Server 간의 T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

Managed Instance 배포 옵션은 SQL 2008 데이터베이스와 호환됩니다. SQL 2005 데이터베이스 서버에서 직접 마이그레이션할 수 있으며, 마이그레이션된 SQL 2005 데이터베이스의 호환성 수준은 SQL 2008로 업데이트됩니다.
  
다음 다이어그램은 Managed Instance의 노출 영역 호환성을 설명합니다.  

![마이그레이션](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server 온-프레미스와 Managed Instance의 주요 차이점

Managed Instance 배포 옵션은 클라우드에서 항상 최신 상태로 유지되는 이점이 있습니다. 즉, 온-프레미스 SQL Server의 일부 기능이 사용되지 않거나 사용 중지되거나 대체될 수 있습니다. 특정 기능이 약간 다른 방식으로 작동하거나 사용자에게 전체 제어 권한이 없는 환경에서 서비스가 실행되면 이 사실을 도구에서 인식해야 하는 경우가 있습니다.

- [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 기본 제공되고 미리 구성됩니다.
- 자동 백업 및 특정 시점 복원. 고객은 자동 백업 체인을 방해하지 않는 `copy-only` 백업을 시작할 수 있습니다.
- Managed Instance는 전체 실제 경로 지정을 허용하지 않으므로 해당하는 모든 시나리오를 약간씩 다르게 지원해야 합니다. RESTORE DB는 WITH MOVE를 지원하지 않고, CREATE DB는 실제 경로를 허용하지 않고, BULK INSERT는 Azure Blob에서만 작동합니다.
- Managed Instance는 Windows 인증의 클라우드 대안으로 [Azure AD 인증](sql-database-aad-authentication.md)을 지원합니다.
- Managed Instance는 메모리 내 OLTP 개체가 포함된 데이터베이스의 XTP 파일 그룹 및 파일을 자동으로 관리합니다.
- Managed Instance는 SSIS(SQL Server Integration Services)를 지원하며 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)를 호스트할 수 있지만, ADF(Azure Data Factory)의 관리되는 Azure-SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. SQL Database의 SSIS 기능을 비교하려면 [Azure SQL Database 단일 데이터베이스/탄력적 풀 및 Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)를 참조하세요.

### <a name="managed-instance-administration-features"></a>Managed Instance 관리 기능

SQL Database 서비스는 사용자를 위해 작업을 수행하거나 이러한 작업을 크게 간소화하므로 Managed Instance 배포 옵션을 사용하여 시스템 관리자는 관리 작업 시간을 크게 단축할 수 있습니다. [OS/RDBMS 설치 및 패치](sql-database-high-availability.md), [동적 인스턴스 크기 조정 및 구성](sql-database-single-database-scale.md), [백업](sql-database-automated-backups.md), [데이터베이스 복제](replication-with-sql-database-managed-instance.md)(시스템 데이터베이스 포함), [고가용성 구성](sql-database-high-availability.md), 상태 및 [성능 모니터링](../azure-monitor/insights/azure-sql.md) 데이터 스트림 구성 등을 예로 들 수 있습니다.

> [!IMPORTANT]
> 지원되는 기능, 부분적으로 지원되는 기능 및 지원되지 않는 기능 목록은 [SQL Database 기능](sql-database-features.md)을 참조하세요. Managed Instance와 SQL Server의 T-SQL 차이점 목록은 SQL Server의 [Managed Instance T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Managed Instance를 프로그래밍 방식으로 식별하는 방법

다음 표는 Transact SQL을 통해 액세스할 수 있으며 애플리케이션이 Managed Instance와 함께 작동 중인지 감지하고 중요한 속성을 검색하는 데 사용할 수 있는 여러 속성을 보여줍니다.

|자산|값|주석|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure(RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY('EngineEdition')`|8|이 값은 Managed Instance를 고유하게 식별합니다.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|`<instanceName>`.`<dnsPrefix>`.database.windows.net 형식의 전체 인스턴스 DNS 이름. 여기서 `<instanceName>`은 고객이 제공한 이름이고, `<dnsPrefix>`는 글로벌 DNS 이름의 고유성을 보장하기 위해 이름에서 자동으로 생성되는 부분(예: “wcus17662feb9ce98”)입니다.|예: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](sql-database-managed-instance-get-started.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [Managed Instance VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
- 백업 파일에서 Managed Instance를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 Managed Instance 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 문제 해결 인텔리전스를 사용하는 Managed Instance 데이터베이스 성능의 고급 모니터링에 대해 자세히 알아보려면 [Azure SQL 분석을 사용하여 Azure SQL Database 모니터링](../azure-monitor/insights/azure-sql.md)을 참조하세요.
- 가격 정보는 [SQL Database Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
