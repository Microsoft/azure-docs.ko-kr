---
title: SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션 | Microsoft Docs
description: SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61315557"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [Azure SQL Database 관리형 인스턴스](sql-database-managed-instance.md)로 마이그레이션하는 방법에 대해 설명합니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 대한 자세한 내용은 [단일 또는 풀링된 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure 데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/sql-database-managed-instance-migration/migration-process.png)

- [관리되는 인스턴스 호환성 평가](#assess-managed-instance-compatibility)
- [앱 연결 옵션 선택](sql-database-managed-instance-connect-app.md)
- [최적 크기의 관리되는 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance)
- [마이그레이션 방법 선택 및 마이그레이션](#select-migration-method-and-migrate)
- [애플리케이션 모니터링](#monitor-applications)

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.

## <a name="assess-managed-instance-compatibility"></a>관리되는 인스턴스 호환성 평가

먼저 관리되는 인스턴스가 애플리케이션의 데이터베이스 요구 사항과 호환되는지 확인합니다. 관리되는 인스턴스 배포 옵션은 온-프레미스 또는 가상 머신에서 SQL Server를 사용하는 대부분의 기존 애플리케이션에서 리프트 앤 시프트 방식으로 쉽게 마이그레이션할 수 있도록 설계되었습니다. 그러나 경우에 따라 아직 지원되지 않는 기능이 필요할 수 있으며 해결 방법을 구현하는 데 드는 비용이 너무 높습니다.

[DMA(Data Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview)를 사용하여 Azure SQL Database에서 데이터베이스 기능에 영향을 주는 잠재적인 호환성 문제를 검색합니다. DMA는 아직 관리형 인스턴스를 마이그레이션 대상으로 지원하지 않지만, Azure SQL Database에 대한 평가를 실행하고 제품 설명서에 대해 보고된 기능 패리티 및 호환성 문제 목록을 신중하게 검토하는 것이 좋습니다. Azure SQL Database로 마이그레이션하지 못하도록 차단하는 대부분의 문제는 관리형 인스턴스를 통해 제거되었으므로 관리되는 인스턴스에서 차단하지 않는 일부 보고된 차단 문제가 있는지 확인하려면 [Azure SQL Database 기능](sql-database-features.md)을 참조하세요. 예를 들어 데이터베이스 간 쿼리, 동일한 인스턴스 내의 데이터베이스 간 트랜잭션, 다른 SQL 원본에 연결된 서버, CLR, 글로벌 임시 테이블, 인스턴스 수준 보기, Service Broker 등과 같은 기능은 관리되는 인스턴스에서 사용할 수 있습니다.

관리되는 인스턴스 배포 옵션에서 제거되지 않은 일부 보고된 차단 문제가 있는 경우 [Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해야 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- 운영 체제 또는 파일 시스템에 직접 액세스해야 하는 경우(예: SQL Server가 있는 동일한 가상 머신에 타사 또는 사용자 지정 에이전트를 설치하는 경우)
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원되지 않는 기능에 대한 엄격한 종속성이 있는 경우
- 특정 버전의 SQL Server(예: 2012)를 반드시 유지해야 하는 경우
- 컴퓨팅 요구 사항이 관리되는 인스턴스에서 제공하는 것보다 훨씬 낮고(예: 하나의 vCore) 데이터베이스 통합이 허용되지 않는 옵션인 경우

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

관리되는 인스턴스는 클라우드로 이동할 온-프레미스 워크로드에 맞게 조정됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](sql-database-service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리되는 인스턴스에 대한 구매 모델은 가상 코어 수 또는 "vCore 수"를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다.

배포 시 컴퓨팅 및 저장소 리소스를 선택한 다음, 나중에 [Azure Portal](sql-database-scale-resources.md)을 사용하여 애플리케이션의 가동 중지 시간을 도입하지 않고 변경할 수 있습니다.

![관리되는 인스턴스 크기 조정](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

VNet 인프라와 관리되는 인스턴스를 만드는 방법을 알아보려면 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.

> [!IMPORTANT]
> 언제나 대상 VNet 및 서브넷을 [관리되는 인스턴스 VNet 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 따라 유지해야 합니다. 호환되지 않는 경우 새 인스턴스를 만들거나 이미 만든 인스턴스를 사용하지 못할 수 있습니다. [새 네트워크 만들기](sql-database-managed-instance-create-vnet-subnet.md) 및 [기존 네트워크 구성](sql-database-managed-instance-configure-vnet-subnet.md)에 대해 자세히 알아보세요.

## <a name="select-migration-method-and-migrate"></a>마이그레이션 방법 선택 및 마이그레이션

관리되는 인스턴스 배포 옵션은 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스 마이그레이션이 필요한 사용자 시나리오를 대상으로 합니다. 인스턴스 수준 및/또는 데이터베이스 간 기능을 정기적으로 사용하는 애플리케이션의 백 엔드를 리프트 앤 시프트 방식으로 이동해야 하는 경우에 최적의 선택입니다. 이러한 시나리오의 경우 애플리케이션을 다시 구성하지 않고도 Azure에서 전체 인스턴스를 해당 환경으로 이동할 수 있습니다.

SQL 인스턴스를 이동하려면 다음을 신중하게 계획해야 합니다.

- 배치해야 하는 모든 데이터베이스(동일한 인스턴스에서 실행되는 데이터베이스)의 마이그레이션
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자, 서버 수준 트리거를 포함하여 애플리케이션이 종속된 인스턴스 수준 개체의 마이그레이션

관리되는 인스턴스는 정기적인 DBA 작업의 일부를 기본 제공되는 플랫폼에 위임할 수 있도록 하는 관리되는 서비스입니다. 따라서 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 정기 백업을 위한 유지 관리 작업 또는 Always On 구성과 같이 일부 인스턴스 수준 데이터는 마이그레이션할 필요가 없습니다.

관리되는 인스턴스에서 지원하는 데이터베이스 마이그레이션 옵션은 다음과 같습니다(현재 지원되는 유일한 마이그레이션 방법임).

- Azure Database Migration Service - 거의 제로에 가까운 가동 중지 시간으로 마이그레이션
- 네이티브 `RESTORE DATABASE FROM URL` - SQL Server의 네이티브 백업을 사용하며 약간의 가동 중지 시간 필요

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure DMS(Database Migration Service)](../dms/dms-overview.md)는 가동 중지 시간을 최소화하면서 여러 데이터베이스 원본에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure로 이동하는 데 필요한 작업을 간소화합니다. 공개 미리 보기의 배포 옵션에는 Azure SQL Database의 데이터베이스 및 Azure Virtual Machine의 SQL Server 데이터베이스가 포함됩니다. DMS는 엔터프라이즈 작업에 권장되는 마이그레이션 방법입니다.

SQL Server 온-프레미스에서 SSIS(SQL Server Integration Services)를 사용하는 경우, DMS는 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)의 마이그레이션을 아직 지원하지 않지만, 관리되는 인스턴스에 새 SSISDB를 만들 Azure-SSIS IR(Integration Runtime)을 ADF(Azure Data Factory)에 프로비전하고 해당 패키지를 다시 배포할 수 있습니다. [ADF에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

이 시나리오와 DMS 구성 단계에 대해 자세히 알아보려면 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  

### <a name="native-restore-from-url"></a>URL에서 네이티브 복원

[Azure Storage](https://azure.microsoft.com/services/storage/)에서 사용 가능한 온-프레미스 SQL Server 또는 [Virtual Machines의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)에서 가져온 네이티브 백업(.bak 파일)의 복원은 SQL DB 관리되는 인스턴스 배포 옵션의 주요 기능 중 하나이며, 오프라인 데이터베이스 마이그레이션을 빠르고 쉽게 수행할 수 있도록 합니다.

다음 다이어그램은 프로세스의 상위 수준 개요를 제공합니다.

![마이그레이션 흐름](./media/sql-database-managed-instance-migration/migration-flow.png)

다음 표에서는 실행되는 원본 SQL Server 버전에 따라 사용할 수 있는 방법에 대한 자세한 정보를 제공합니다.

|단계|SQL 엔진 및 버전|백업/복원 방법|
|---|---|---|
|Azure Storage에 백업 저장|SQL 2012 SP1 CU2 이전|Azure Storage에 .bak 파일 직접 업로드|
||2012 SP1 CU2~2016|사용되지 않는 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 구문을 사용하여 직접 백업|
||2016 이상|[WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 구문을 사용하여 직접 백업|
|Azure Storage에서 관리되는 인스턴스로 복원|[SAS CREDENTIAL을 사용하여 URL에서 복원](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - 원시 복원 옵션을 사용하여 [투명한 데이터 암호화](transparent-data-encryption-azure-sql.md)로 보호된 데이터베이스를 관리되는 인스턴스로 마이크레이션하는 경우에는 데이터베이스를 복원하기 전에 온-프레미스 또는 IaaS SQL Server의 해당 인증서를 마이그레이션해야 합니다. 자세한 단계는 [관리되는 인스턴스로 TDE 인증서 마이그레이션](sql-database-managed-instance-migrate-tde-certificate.md)을 참조하세요.
> - 시스템 데이터베이스의 복원은 지원되지 않습니다. master 또는 msdb 데이터베이스에 저장된 인스턴스 수준 개체를 마이그레이션하려면, 이러한 개체를 스크립팅하고 대상 인스턴스에서 T-SQL 스크립트를 실행하는 것이 좋습니다.

SAS 자격 증명을 사용하여 데이터베이스 백업을 관리되는 인스턴스에 복원하는 방법을 보여주는 빠른 시작은 [백업에서 관리되는 인스턴스로 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>애플리케이션 모니터링

마이그레이션 후에 애플리케이션의 동작과 성능을 추적합니다. 관리되는 인스턴스에서 일부 변경은 [데이터베이스 호환성 수준이 변경된 경우](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)에만 활성화됩니다. Azure SQL Database로의 데이터베이스 마이그레이션은 대부분의 경우 원래의 호환성 수준을 유지합니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90인 경우 업그레이드된 데이터베이스에서 호환성 수준은 관리되는 인스턴스에서 지원되는 가장 낮은 호환성 수준인 100으로 설정됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다.

마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다. [최신 SQL Server 버전으로 업그레이드하는 동안 성능 안정성 유지](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)에서 설명한 대로, 데이터베이스 호환성 수준 변경 전후의 작업 성능에 대한 정보를 얻기 위한 최적의 도구로 쿼리 저장소를 사용합니다.

완전히 관리되는 플랫폼에 있는 경우 SQL Database 서비스의 일부로 자동으로 제공되는 이점을 활용합니다. 예를 들어 서비스에서 백업을 자동으로 수행하므로 관리되는 인스턴스에 백업을 만들 필요가 없습니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. 관리되는 인스턴스는 [PITR(지정 시간 복구)](sql-database-recovery-using-backups.md#point-in-time-restore)을 사용하여 이 보존 기간 내의 특정 시점으로 복원하는 기능을 제공합니다. 또한 [고가용성](sql-database-high-availability.md)이 기본 제공되므로 고가용성 설정에 대해 걱정할 필요가 없습니다.

보안을 강화하려면 다음과 같이 사용할 수 있는 기능 중 일부를 사용하는 것이 좋습니다.

- 데이터베이스 수준에서의 Azure Active Directory 인증
- [감사](sql-database-managed-instance-auditing.md), [위협 탐지](sql-database-advanced-data-security.md), [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 및 [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)과 같은 [고급 보안 기능](sql-database-security-overview.md)을 사용하여 인스턴스를 보호합니다.

## <a name="next-steps"></a>다음 단계

- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](sql-database-managed-instance.md)을 참조하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DMS를 사용한 마이그레이션을 보여 주는 자습서는 [DMS를 사용하여 온-프레미스 데이터베이스를 관리되는 인스턴스로 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.  
