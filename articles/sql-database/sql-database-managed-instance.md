---
title: SQL 관리 되는 인스턴스 개요
description: 이 문서에서는 관리 되는 인스턴스 Azure SQL Database 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 01/21/2020
ms.openlocfilehash: b9fdd1b25e53e1cdc8aa76564304a61adaa8d804
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201597"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스는 무엇입니까?

Managed Instance는 Azure SQL Database의 새로운 배포 옵션입니다. 최신 SQL Server 온-프레미스(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 구현과 온-프레미스 SQL Server 고객이 편리하게 사용할 수 있는 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. Managed Instance 배포 모델을 사용하면 기존 SQL Server 고객은 애플리케이션 및 데이터베이스 변경을 최소화하고 온-프레미스 애플리케이션을 클라우드로 이동할 수 있습니다. 이와 동시에 Managed Instance 배포 옵션은 모든 PaaS 기능(자동 패치 및 버전 업데이트, [자동화된 백업](sql-database-automated-backups.md), [고가용성](sql-database-high-availability.md))을 유지하므로 관리 오버헤드와 TCO를 크게 줄일 수 있습니다.

> [!IMPORTANT]
> Managed Instance 배포 옵션을 현재 사용할 수 있는 지역의 목록은 [지원되는 지역](sql-database-managed-instance-resource-limits.md#supported-regions)을 참조하세요.

다음 다이어그램은 Managed Instance의 주요 기능을 설명합니다.

![주요 기능](./media/sql-database-managed-instance/key-features.png)

Managed Instance 배포 모델은 가능한 한 적은 마이그레이션 작업을 통해 많은 수의 앱을 온-프레미스 또는 IaaS, 자체 구축 또는 ISV 제공 환경에서 완전히 관리되는 PaaS 클라우드 환경으로 마이그레이션하려는 고객을 위해 설계되었습니다. Azure의 완전 자동화 [DMS(Database Migration Service)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)를 사용하면 온-프레미스 SQL Server를 Managed Instance로 옮길 수 있습니다. 여기에서는 SQL Server 온-프레미스와의 호환성이 제공되고 네이티브 VNet 지원을 통해 고객 인스턴스를 완벽하게 격리합니다.  소프트웨어 보증을 사용 하면 [SQL Server에 대 한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 사용 하 여 관리 되는 인스턴스의 할인 요금에 대 한 기존 라이선스를 교환할 수 있습니다.  Managed Instance는 클라우드에서 높은 보안과 풍부한 프로그래밍 기능 영역이 필요한 SQL Server 인스턴스를 위한 최상의 마이그레이션 대상입니다.

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
|격리된 환경([VNet 통합](sql-database-managed-instance-connectivity-architecture.md), 단일 테넌트 서비스, 전용 컴퓨팅 및 스토리지) <br>[TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 인증](sql-database-aad-authentication.md), Single Sign-On 지원 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 서버 보안 주체 (로그인)</a>  <br>Azure SQL 데이터베이스와 동일한 표준 준수 <br>[SQL 감사](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |서비스 프로비전 및 크기 조정을 자동화하는 Azure Resource Manager API <br>수동 서비스 프로비전 및 크기 조정을 위한 Azure Portal 기능 <br>데이터 마이그레이션 서비스

> [!IMPORTANT]
> Azure SQL Database (모든 배포 옵션)은 다양 한 규정 준수 표준에 대해 인증 되었습니다. 자세한 내용은 SQL Database 준수 인증의 최신 목록을 찾을 수 있는 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 를 참조 하세요.

Managed Instance의 주요 기능을 다음 표에서 볼 수 있습니다.

|기능 | Description|
|---|---|
| SQL Server 버전/빌드 | SQL Server 데이터베이스 엔진(안정적인 최신 버전) |
| 관리되는 자동 백업 | yes |
| 기본 제공 인스턴스/데이터베이스 모니터링 및 메트릭 | yes |
| 자동 소프트웨어 패치 | yes |
| 최신 데이터베이스 엔진 기능 | yes |
| 데이터베이스당 데이터 파일(행) 수 | 여러 접두사 |
| 데이터베이스당 로그 파일(로그) 수 | 1 |
| VNet - Azure Resource Manager 배포 | yes |
| VNet - 클래식 배포 모델 | 예 |
| 포털 지원 | yes|
| 기본 제공 통합 서비스(SSIS) | 아니요 - SSIS는 [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)의 일부입니다. |
| 기본 제공 분석 서비스(SSAS) | 아니요 - SSAS는 별도의 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview)입니다. |
| 기본 제공 보고 서비스(SSRS) | 아니요 - Power BI 또는 SSRS IaaS를 사용합니다. |
|||

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

Managed Instance의 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)은 유연성, 제어 기능, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 사용하면 워크로드 요구 사항에 따라 컴퓨팅, 메모리 및 스토리지를 선택할 수 있습니다. VCore 모델은 [SQL Server Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 최대 55%까지 비용을 절감할 수 있습니다.

vCore 모델에서는 하드웨어 세대를 선택할 수 있습니다.

- **Gen4** 논리적 Cpu는 Intel E5-2673 v3 (Haswell) 2.4 g h z 프로세서, 연결 된 SSD, 실제 코어 수, 코어 당 8GB RAM 및 계산 크기 (8 ~ 24)를 기반으로 합니다.
- **Gen5** 논리적 Cpu는 Intel E5-2673 v4 (Broadwell) 2.3 GHz 및 Intel SP-8160 (Skylake) 프로세서, fast NVMe SSD, 하이퍼 스레드 논리 코어 및 4 ~ 80 코어 간 계산 크기를 기반으로 합니다.

하드웨어 세대 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)을 참조하세요.

## <a name="managed-instance-service-tiers"></a>Managed Instance 서비스 계층

관리형 인스턴스는 두 개의 서비스 계층에서 사용할 수 있습니다.

- **범용: 일반적인**성능 및 IO 대기 시간 요구 사항이 있는 응용 프로그램을 위해 설계 되었습니다.
- **업무상 중요**: IO 대기 시간 요구 사항이 적고 워크 로드에 대 한 기본 유지 관리 작업의 영향을 최소화 하는 응용 프로그램용으로 설계 되었습니다.

두 서비스 계층은 모두 99.99% 가용성을 보장하며 스토리지 크기와 컴퓨팅 용량을 독립적으로 선택할 수 있습니다. Azure SQL Database의 고가용성 아키텍처에 대한 자세한 내용은 [고가용성 및 Azure SQL Database](sql-database-high-availability.md)를 참조하세요.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

다음 목록에서는 범용 서비스 계층의 주요 특징을 설명합니다.

- 일반적인 성능 요구 사항이 있는 대부분의 비즈니스 애플리케이션에 적합한 디자인
- 고성능 Azure Blob Storage(8TB)
- 신뢰할 수 있는 Azure Blob Storage 및 [Azure Service Fabric](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)에 기반한 [고가용성](../service-fabric/service-fabric-overview.md) 기본 제공

자세한 내용은 [범용 계층의 스토리지 레이어](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) 및 [Managed Instance(범용)에 대한 스토리지 성능 모범 사례 및 고려 사항](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)을 참조하세요.

서비스 계층 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)을 참조하세요.

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

중요 비즈니스용 서비스 계층은 IO 요구 사항이 높은 애플리케이션용으로 빌드됩니다. 여러 개의 격리된 복제본을 사용하여 장애에 대한 최고의 복원력을 제공합니다.

다음 목록에서는 중요 비즈니스용 서비스 계층의 주요 특징을 설명합니다.

- 최고의 성능과 HA 요구 사항을 가진 대부분의 비즈니스 애플리케이션용으로 설계됨
- 초고속 로컬 SSD 스토리지(4세대의 최대 1TB 및 5세대의 최대 4TB)와 함께 제공됨
- [Always On 가용성 그룹](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 및 [Azure Service Fabric](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)을 기반으로 [고가용성](../service-fabric/service-fabric-overview.md) 기본 제공
- 보고 및 기타 읽기 전용 워크로드에 사용할 수 있는 추가적인 [읽기 전용 데이터베이스 복제본](sql-database-read-scale-out.md) 기본 제공
- 고성능 요구 사항이 있는 워크로드에 사용할 수 있는 [메모리 내 OLTP](sql-database-in-memory.md)  

서비스 계층 간의 차이점에 대한 자세한 내용은 [Managed Instance 리소스 제한](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)을 참조하세요.


## <a name="managed-instance-management-operations"></a>관리 되는 인스턴스 관리 작업

Azure SQL Database에서는 관리되는 새로운 인스턴스를 자동으로 배포하고, 인스턴스 속성을 업데이트하며, 더 이상 필요하지 않은 경우 인스턴스를 삭제하는 데 사용할 수 있는 관리 작업을 제공합니다. 이 섹션에서는 관리 작업 및 일반적인 기간에 대 한 정보를 제공 합니다.

[Azure vnet (가상 네트워크) 내에서 배포](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) 를 지원 하 고 고객에 대 한 격리 및 보안을 제공 하기 위해 관리 되는 인스턴스는 고객의 가상 네트워크 서브넷 내에 배포 된 격리 된 가상 머신의 전용 집합을 나타내는 [가상 클러스터](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture)에 의존 합니다. 기본적으로 빈 서브넷에 있는 모든 관리 되는 인스턴스 배포는 새 가상 클러스터가 buildout을 생성 합니다.

배포 된 관리 되는 인스턴스의 후속 작업은 기본 가상 클러스터에도 영향을 미칠 수 있습니다. 이는 관리 작업 기간에 영향을 줍니다. 추가 가상 컴퓨터를 배포 하는 경우 새 배포 또는 기존 관리 되는 인스턴스에 대 한 업데이트를 계획 하는 경우 고려해 야 하는 오버 헤드가 발생 합니다.

모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포 (새 인스턴스 생성). 
- 인스턴스 업데이트 (vCores 또는 예약 된 저장소와 같은 인스턴스 속성 변경)
- 인스턴스를 삭제 합니다.

일반적으로 가상 클러스터에 대 한 작업은 가장 긴 시간이 걸립니다. 가상 클러스터에 대 한 작업 기간-다음은 기존 서비스 원격 분석 데이터에 따라 일반적으로 예측할 수 있는 값입니다.

- 가상 클러스터를 만듭니다. 이는 인스턴스 관리 작업의 동기 단계입니다. **작업의 90%가 4 시간 내에 완료**되었습니다.
- 가상 클러스터 크기 조정 (확장 또는 축소). 확장은 동기 단계로, 축소는 인스턴스 관리 작업의 기간에 영향을 주지 않고 비동기적으로 수행 됩니다. **클러스터 확장의 90%가 2.5 시간 이내에 완료**되었습니다.
- 가상 클러스터를 삭제 합니다. 삭제는 비동기 단계 이지만 빈 가상 클러스터에서 [수동으로 시작할](sql-database-managed-instance-delete-virtual-cluster.md) 수도 있습니다 .이 경우에는 동기적으로 실행 됩니다. **가상 클러스터 삭제의 90%가 1.5 시간 내에 완료**되었습니다.

또한 인스턴스 관리에는 호스트 된 데이터베이스에 대 한 작업 중 하나가 포함 될 수 있으며,이로 인해 기간이 길어집니다.

- Azure Storage에서 데이터베이스 파일을 연결 합니다. 이는 compute (vCore) 또는 범용 서비스 계층의 저장소 확장/축소와 같은 동기 단계입니다. **이러한 작업의 90%는 5 분 안에 완료**됩니다.
- 가용성 그룹 시드를 Always On 합니다. 이는 계산 (vCore), 중요 비즈니스용 서비스 계층의 저장소 크기 조정 및 서비스 계층을 일반 용도에서 중요 비즈니스용 (또는 그 반대로)로 변경 하는 등의 동기 단계입니다. 이 작업의 기간은 총 데이터베이스 크기 및 현재 데이터베이스 작업 (활성 트랜잭션 수)에 비례 합니다. 데이터베이스 작업 인스턴스를 업데이트할 때 전체 기간에 상당한 차이를 일으킬 수 있습니다. **이러한 작업의 90%는 220 g b/시간 이상에서 실행**됩니다.

다음 표에는 작업과 일반적인 전체 기간이 요약 되어 있습니다.

|Category  |작업(Operation)  |장기 실행 세그먼트  |예상 기간  |
|---------|---------|---------|---------|
|**배포** |빈 서브넷의 첫 번째 인스턴스|가상 클러스터 만들기|4 시간 내에 완료 된 작업의 90%|
|배포 |비어 있지 않은 서브넷에 있는 다른 하드웨어 생성의 첫 번째 인스턴스 (예: Gen 4 인스턴스가 있는 서브넷의 첫 번째 Gen 5 인스턴스)|가상 클러스터 만들기 *|4 시간 내에 완료 된 작업의 90%|
|배포 |비어 있거나 비어 있지 않은 서브넷에 있는 4 개 vCores의 첫 번째 인스턴스 생성|가상 클러스터 만들기 * *|4 시간 내에 완료 된 작업의 90%|
|배포 |비어 있지 않은 서브넷 (두 번째, 세 번째 등) 내에서 후속 인스턴스 만들기|가상 클러스터 크기 조정|2\.5 시간 내에 완료 된 작업의 90%|
|**Update** |인스턴스 속성 변경 (관리자 암호, AAD 로그인, Azure 하이브리드 혜택 플래그)|해당 없음|최대 1 분|
|업데이트 |인스턴스 저장소 확장/축소 (범용 서비스 계층)|데이터베이스 파일 연결|5 분 내에 완료 된 작업의 90%|
|업데이트 |인스턴스 저장소 확장/축소 (중요 비즈니스용 서비스 계층)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|2\.5 시간에 완료 된 작업의 90% + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (범용)|-가상 클러스터 크기 조정<br>-데이터베이스 파일 연결|2\.5 시간 내에 완료 된 작업의 90%|
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (중요 비즈니스용)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|2\.5 시간에 완료 된 작업의 90% + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|업데이트 |인스턴스를 4 개까지 확장 (범용)|-가상 클러스터 크기 조정 (처음으로 수행 하는 경우 가상 클러스터를 만드는 데 필요 합니다. * *)<br>-데이터베이스 파일 연결|4 h 5 분 내에 완료 된 작업의 90%|
|업데이트 |인스턴스를 4 개 (중요 비즈니스용)까지 확장|-가상 클러스터 크기 조정 (처음으로 수행 하는 경우 가상 클러스터를 만드는 데 필요 합니다. * *)<br>-Always On 가용성 그룹 시드|4 시간 동안 완료 된 작업의 90% + 모든 데이터베이스 초기값 (220 g b/시간)|
|업데이트 |인스턴스 서비스 계층 변경 (일반적인 중요 비즈니스용 용도 및 그 반대로)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|2\.5 시간에 완료 된 작업의 90% + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|**삭제할지**|인스턴스 삭제|모든 데이터베이스에 대 한 비상 백업 로그|90% 작업이 최대 1 분 내에 완료 되었습니다.<br>참고: 서브넷의 마지막 인스턴스가 삭제 된 경우이 작업은 12 시간 후에 가상 클러스터 삭제를 예약 합니다. * * *|
|삭제|가상 클러스터 삭제 (사용자 시작 작업)|가상 클러스터 삭제|90%의 작업이 최대 1.5 시간 내에 완료 되었습니다.|

\* 가상 클러스터는 하드웨어 생성에 따라 빌드됩니다.

\*\* 4 vCores 배포 옵션은 6 월 2019에 출시 되었으며 새 가상 클러스터 버전이 필요 합니다. 6 월 12 일 이전에 생성 된 대상 서브넷에 인스턴스가 있는 경우 새 가상 클러스터가 4 개 vCore 인스턴스를 호스트 하기 위해 자동으로 배포 됩니다.

\*\*\* 12 시간이 현재 구성 이지만 나중에 변경 될 수 있으므로 하드 종속성을 주지 않습니다. 이전에 가상 클러스터를 삭제 해야 하는 경우 (예: 서브넷을 해제 하려면) [Azure SQL Database 관리 되는 인스턴스를 삭제 한 후 서브넷 삭제](sql-database-managed-instance-delete-virtual-cluster.md)를 참조 하세요.

### <a name="instance-availability-during-management"></a>관리 중 인스턴스 가용성

관리 되는 인스턴스는 배포 및 삭제 작업 중에 클라이언트 응용 프로그램에서 사용할 수 없습니다.

관리 되는 인스턴스는 업데이트 작업 중에 사용할 수 있지만 일반적으로 최대 10 초 동안 발생 하는 업데이트의 끝에서 발생 하는 장애 조치 (failover)로 인 한 가동 중지 시간이 짧습니다. 이에 대 한 예외는 일반 용도의 서비스 계층의 예약 된 저장소 공간 업데이트로, 장애 조치 (failover)가 발생 하거나 인스턴스 가용성에 영향을 주지 않습니다.

> [!IMPORTANT]
> [복구 시간이 길어질 때](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process)데이터베이스에서 발생 하는 장기 실행 트랜잭션의 경우 장애 조치 (failover) 기간이 크게 달라질 수 있습니다. 따라서 관리 되는 인스턴스의 계산 또는 Azure SQL Database 저장소 크기를 조정 하거나 장기 실행 트랜잭션 (데이터 가져오기, 데이터 처리 작업, 인덱스 다시 작성 등)을 사용 하 여 서비스 계층을 동시에 변경 하지 않는 것이 좋습니다. 작업이 끝날 때 수행 되는 데이터베이스 장애 조치 (failover)는 진행 중인 트랜잭션을 취소 하 고 복구 시간이 길어질 수 있습니다.

> [!TIP]
> 범용 서비스 계층의 예약 된 저장소 공간 업데이트는 장애 조치 (failover)가 발생 하거나 인스턴스 가용성에 영향을 주지 않습니다.

[가속화 된 데이터베이스 복구](sql-database-accelerated-database-recovery.md) 는 현재 Azure SQL Database 관리 되는 인스턴스에 사용할 수 없습니다. 이 기능을 사용 하도록 설정 하면 장기 실행 트랜잭션의 경우에도 장애 조치 (failover) 시간의 가변성을 크게 줄일 수 있습니다.

### <a name="canceling-management-operations"></a>관리 작업 취소

다음 표에는 특정 관리 작업을 취소 하는 기능과 일반적인 전체 기간이 요약 되어 있습니다.

Category  |작업(Operation)  |취소  |예상 취소 기간  |
|---------|---------|---------|---------|
|배포 |인스턴스 만들기 |예 |  |
|업데이트 |인스턴스 저장소 확장/축소 (범용) |예 |  |
|업데이트 |인스턴스 저장소 확장/축소 (중요 비즈니스용) |yes |5 분 내에 완료 된 작업의 90% |
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (범용) |yes |5 분 내에 완료 된 작업의 90% |
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (중요 비즈니스용) |yes |5 분 내에 완료 된 작업의 90% |
|업데이트 |인스턴스 서비스 계층 변경 (일반적인 중요 비즈니스용 용도 및 그 반대로) |yes |5 분 내에 완료 된 작업의 90% |
|DELETE |인스턴스 삭제 |예 |  |
|DELETE |가상 클러스터 삭제 (사용자 시작 작업) |예 |  |

관리 작업을 취소 하려면 개요 블레이드로 이동 하 고 진행 중인 작업의 알림 상자를 클릭 합니다. 오른쪽에서 진행 중인 작업이 있는 화면이 표시 되 고 작업 취소 단추가 표시 됩니다. 을 클릭 한 후 다시 클릭 하 라는 메시지가 표시 되 고 작업 취소를 확인 합니다.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

취소 요청을 제출 하 고 처리 한 후에는 제출 취소 성공 또는 실패 한 경우 알림을 받게 됩니다. 

취소가 성공 하면 몇 분 후에 관리 작업이 취소 되 고 오류가 발생 합니다.

![작업 결과 취소](./media/sql-database-managed-instance/canceling-operation-result.png)

요청 취소 또는 취소 단추가 활성 상태가 아닌 경우 관리 작업을 취소할 수 없음 상태가 되 고 몇 분 안에 완료 됩니다. 관리 작업은 완료 될 때까지 실행을 계속 합니다.

> [!IMPORTANT]
> 취소 작업은 현재 포털 에서만 지원 됩니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

Managed Instance 배포 옵션은 Azure 클라우드와 SQL Server 데이터베이스 엔진에서 제공하는 고급 보안 기능을 결합합니다.

### <a name="managed-instance-security-isolation"></a>Managed Instance 보안 격리

Managed Instance는 Azure 클라우드의 다른 테넌트와 추가로 격리되는 보안을 제공합니다. 보안 격리에는 다음이 포함됩니다.

- Azure ExpressRoute 또는 VPN Gateway를 사용하여 [기본 가상 네트워크 구현](sql-database-managed-instance-connectivity-architecture.md) 및 온-프레미스 환경에 연결
- 기본 배포에서 SQL 끝점은 개인 IP 주소를 통해서만 노출 되므로 개인 Azure 또는 하이브리드 네트워크에서 안전 하 게 연결할 수 있습니다.
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
- [Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) 은 서비스에 기본 제공 되는 추가 보안 인텔리전스 계층을 제공 하 여 [감사](sql-database-managed-instance-auditing.md) 를 보완 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 검색 합니다. 의심스러운 활동, 잠재적 취약성 및 SQL 삽입 공격은 물론 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. [Azure Security Center](https://azure.microsoft.com/services/security-center/) 에서 Advanced threat Protection 경고를 확인 하 고 의심 스러운 활동의 세부 정보를 제공 하 고 위협을 조사 하 고 완화 하는 방법에 대 한 조치를 수행할 수 있습니다.  
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)은 권한이 없는 사용자에게 중요한 데이터를 마스킹하여 데이터 노출을 제한합니다. 동적 데이터 마스킹을 수행하면 애플리케이션 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 데이터베이스의 데이터는 변경되지 않으면서 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security)을 통해 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. RLS(행 수준 보안)는 애플리케이션의 보안 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 소속 부서와 관련된 데이터 행에만 액세스하게 하거나 데이터 액세스를 관련 데이터로 제한할 수 있습니다.
- [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 Managed Instance 데이터 파일을 암호화하고, 이를 미사용 데이터 암호화라고 합니다. TDE는 데이터 및 로그 파일에 대한 실시간 I/O 암호화 및 암호 해독을 수행합니다. 암호화에는 복구 중에 사용 가능하도록 데이터베이스 부트 레코드에 저장된 DEK(데이터베이스 암호화 키)가 사용됩니다. 투명한 데이터 암호화를 사용하여 Managed Instance의 모든 데이터베이스를 보호할 수 있습니다. TDE는 스토리지 미디어의 도용으로부터 보호하기 위해 많은 규정 준수 표준에 필요한 정지된 암호화 기술로써 SQL Server에서 입증되었습니다.

암호화된 데이터베이스를 Managed Instance로 마이그레이션하는 작업은 Azure DMS(Database Migration Service) 또는 네이티브 복원을 통해 지원됩니다. 기본 복원을 사용 하 여 암호화 된 데이터베이스를 마이그레이션하려는 경우 SQL Server 온-프레미스 또는 가상 머신의 SQL Server에서 관리 되는 인스턴스로 기존 TDE 인증서를 마이그레이션하는 것이 필수 단계입니다. 마이그레이션 옵션에 대한 자세한 내용은 [SQL Server 인스턴스를 Managed Instance로 마이그레이션](sql-database-managed-instance-migrate.md)을 참조하세요.

## <a name="azure-active-directory-integration"></a>Azure Active Directory 통합

Managed Instance 배포 옵션은 기존 SQL 서버 데이터베이스 엔진 로그인 및 AAD(Azure Active Directory)와 통합된 로그인을 지원합니다. Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)는 온-프레미스 환경에서 사용 중인 온-프레미스 데이터베이스 로그인의 Azure 클라우드 버전입니다. Azure AD 서버 보안 주체 (로그인)를 사용 하면 동일한 관리 되는 내 데이터베이스 간 쿼리를 포함 하 여 인스턴스 수준 작업을 수행할 수 있는 진정한 인스턴스 범위 보안 주체로 Azure Active Directory 테 넌 트의 사용자 및 그룹을 지정할 수 있습니다. 인스턴스.

**외부 공급자에서**Azure AD 서버 보안 주체 (로그인)를 만들기 위해 새 구문이 도입 되었습니다. 구문에 대한 자세한 내용은 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>을 참조하고 [Managed Instance에 대한 Azure Active Directory 관리자 프로비전](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)을 검토하세요.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication

Managed Instance 배포 옵션을 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md))을 제공하여 SSO(Single Sign-On) 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시킵니다.

### <a name="authentication"></a>인증

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

- Wide World Importers 표준 데이터베이스 백업 파일을 복원하는 방법을 보여주는 빠른 시작은 [Managed Instance에 백업 파일 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요. 이 빠른 시작에서는 Azure blob storage에 백업 파일을 업로드 하 고 SAS (공유 액세스 서명) 키를 사용 하 여 보호 해야 합니다.
- URL에서 복원하는 방법에 대한 자세한 내용은 [URL에서 네이티브 복원](sql-database-managed-instance-migrate.md#native-restore-from-url)을 참조하세요.

> [!IMPORTANT]
> Managed Instance에서 백업은 다른 Managed Instance로 복원될 수만 있습니다. 온-프레미스 SQL Server 또는 단일 데이터베이스/탄력적 풀로 복원할 수 없습니다.

### <a name="data-migration-service"></a>데이터 마이그레이션 서비스

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure VM의 Azure SQL Database(단일 데이터베이스, 탄력적 풀의 풀링된 데이터베이스 및 관리형 인스턴스의 인스턴스 데이터베이스) 및 SQL Server로 이동하는 데 필요한 작업을 간소화합니다. [DMS를 사용하여 온-프레미스 데이터베이스를 Managed Instance로 마이그레이션하는 방법](https://aka.ms/migratetoMIusingDMS)을 참조하세요.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

Managed Instance 배포 옵션의 목표는 서비스의 일반 공급이 시작될 때까지 단계별로 제공되는 온-프레미스 SQL Server와 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 기능 및 비교 목록은 [SQL Database 기능 비교](sql-database-features.md)를 참조하고, Managed Instance와 SQL Server 간의 T-SQL 차이점 목록은 [Managed Instance 및 SQL Server 간의 T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

Managed Instance 배포 옵션은 SQL 2008 데이터베이스와 호환됩니다. SQL 2005 데이터베이스 서버에서 직접 마이그레이션할 수 있으며, 마이그레이션된 SQL 2005 데이터베이스의 호환성 수준은 SQL 2008로 업데이트됩니다.
  
다음 다이어그램은 Managed Instance의 노출 영역 호환성을 설명합니다.  

![마이그레이션](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>SQL Server 온-프레미스와 Managed Instance의 주요 차이점

Managed Instance 배포 옵션은 클라우드에서 항상 최신 상태로 유지되는 이점이 있습니다. 즉, 온-프레미스 SQL Server의 일부 기능이 사용되지 않거나 사용 중지되거나 대체될 수 있습니다. 특정 기능이 약간 다른 방식으로 작동 하거나 완전히 제어 하지 않는 환경에서 서비스가 실행 되는 것을 도구에서 인식 해야 하는 경우가 있습니다.

- [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 기본 제공되고 미리 구성됩니다.
- 자동 백업 및 특정 시점 복원. 고객은 자동 백업 체인을 방해하지 않는 `copy-only` 백업을 시작할 수 있습니다.
- 관리 되는 인스턴스는 전체 실제 경로를 지정 하는 것을 허용 하지 않으므로 모든 해당 시나리오를 다르게 지원 해야 합니다. RESTORE DB는 이동을 지원 하지 않으며, CREATE DB는 실제 경로를 허용 하지 않고, BULK INSERT Azure Blob 에서만 작동 합니다.
- Managed Instance는 Windows 인증의 클라우드 대안으로 [Azure AD 인증](sql-database-aad-authentication.md)을 지원합니다.
- Managed Instance는 메모리 내 OLTP 개체가 포함된 데이터베이스의 XTP 파일 그룹 및 파일을 자동으로 관리합니다.
- Managed Instance는 SSIS(SQL Server Integration Services)를 지원하며 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)를 호스트할 수 있지만, ADF(Azure Data Factory)의 관리되는 Azure-SSIS IR(Integration Runtime)에서 실행됩니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. SQL Database의 SSIS 기능을 비교 하려면 [Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)를 참조 하세요.

### <a name="managed-instance-administration-features"></a>Managed Instance 관리 기능

SQL Database 서비스는 사용자를 위해 작업을 수행하거나 이러한 작업을 크게 간소화하므로 Managed Instance 배포 옵션을 사용하여 시스템 관리자는 관리 작업 시간을 크게 단축할 수 있습니다. [OS/RDBMS 설치 및 패치](sql-database-high-availability.md), [동적 인스턴스 크기 조정 및 구성](sql-database-single-database-scale.md), [백업](sql-database-automated-backups.md), [데이터베이스 복제](replication-with-sql-database-managed-instance.md)(시스템 데이터베이스 포함), [고가용성 구성](sql-database-high-availability.md), 상태 및 [성능 모니터링](../azure-monitor/insights/azure-sql.md) 데이터 스트림 구성 등을 예로 들 수 있습니다.

> [!IMPORTANT]
> 지원되는 기능, 부분적으로 지원되는 기능 및 지원되지 않는 기능 목록은 [SQL Database 기능](sql-database-features.md)을 참조하세요. Managed Instance와 SQL Server의 T-SQL 차이점 목록은 SQL Server의 [Managed Instance T-SQL 차이점](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Managed Instance를 프로그래밍 방식으로 식별하는 방법

다음 표는 Transact SQL을 통해 액세스할 수 있으며 애플리케이션이 Managed Instance와 함께 작동 중인지 감지하고 중요한 속성을 검색하는 데 사용할 수 있는 여러 속성을 보여줍니다.

|속성|값|주석|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure(RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|이 값은 SQL Database와 같습니다. 이 **는** SQL 엔진 버전 12 (SQL Server 2014)를 나타내지 않습니다. 관리 되는 인스턴스는 항상 사용 가능한 최신 RTM 버전의 SQL Server와 같거나 높은 안정적인 최신 SQL 엔진 버전을 항상 실행 합니다.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY('EngineEdition')`|8|이 값은 Managed Instance를 고유하게 식별합니다.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|`<instanceName>`.`<dnsPrefix>`.database.windows.net 형식의 전체 인스턴스 DNS 이름. 여기서 `<instanceName>`은 고객이 제공한 이름이고, `<dnsPrefix>`는 글로벌 DNS 이름의 고유성을 보장하기 위해 이름에서 자동으로 생성되는 부분(예: “wcus17662feb9ce98”)입니다.|예: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](sql-database-managed-instance-get-started.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [Managed Instance VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요.
- 백업 파일에서 Managed Instance를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 Managed Instance 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 되는 문제 해결 인텔리전스를 사용 하 여 관리 되는 인스턴스 데이터베이스 성능의 고급 모니터링은 [Azure SQL 분석를 사용 하 여 Azure SQL Database 모니터링](../azure-monitor/insights/azure-sql.md)을 참조 하세요.
- 가격 정보는 [SQL Database Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
