---
title: SQL Server에서 Azure SQL Managed Instance로 마이그레이션
description: SQL Server에서 Azure SQL Managed Instance로 데이터베이스를 마이그레이션하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: b7623a3c89f9ae4b20385caaac676b972f55f85e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209490"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 SQL Server 인스턴스 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 SQL Server 2005 이상 버전 인스턴스를 [AZURE SQL Managed Instance](sql-managed-instance-paas-overview.md)로 마이그레이션하는 방법에 대해 알아봅니다. 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하는 방법에 대 한 자세한 내용은 [SQL Database로 마이그레이션](../database/migrate-to-database-from-sql-server.md)을 참조 하세요. 다른 플랫폼에서 마이그레이션하는 방법에 대한 마이그레이션 정보는 [Azure Database 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

> [!NOTE]
> Azure SQL Managed Instance를 신속 하 게 시작 하 고 시도 하려면이 페이지 대신 [빠른 시작 가이드](quickstart-content-reference-guide.md) 로 이동 하는 것이 좋습니다.

높은 수준에서 데이터베이스 마이그레이션 프로세스는 다음과 같습니다.

![마이그레이션 프로세스](./media/migrate-to-instance-from-sql-server/migration-process.png)

- 마이그레이션을 방해할 수 있는 차단 문제가 없는지 확인 해야 하는 [SQL Managed Instance 호환성을 평가](#assess-sql-managed-instance-compatibility) 합니다.
  
  이 단계에는 원본 SQL Server 인스턴스에서 리소스 사용량을 확인 하는 [성능 기준](#create-a-performance-baseline) 생성도 포함 됩니다. 이 단계는 적절히 크기가 지정 된 관리 되는 인스턴스를 배포 하 고 마이그레이션 후의 성능에 영향을 주지 않는지 확인 하는 데 필요 합니다.
- [앱 연결 옵션을 선택](connect-application-instance.md)합니다.
- 관리 되는 인스턴스의 기술 특성 (vCores 수, 메모리 양) 및 성능 계층 (중요 비즈니스용, 범용)을 선택할 수 있는 [최적의 크기의 관리 되는 인스턴스에 배포](#deploy-to-an-optimally-sized-managed-instance) 합니다.
- 오프 라인 마이그레이션 (네이티브 백업/복원, 데이터베이스 가져오기/내보내기) 또는 온라인 마이그레이션 (Azure Data Migration Service, 트랜잭션 복제)을 사용 하 여 데이터베이스를 마이그레이션하는 [마이그레이션 방법 및](#select-a-migration-method-and-migrate) 마이그레이션을 선택 합니다.
- [응용 프로그램을 모니터링](#monitor-applications) 하 여 성능이 예상 되는지 확인 합니다.

> [!NOTE]
> 개별 데이터베이스를 단일 데이터베이스 또는 탄력적 풀로 마이그레이션하려면 [SQL Server 데이터베이스를 Azure SQL Database로 마이그레이션](../database/migrate-to-database-from-sql-server.md)을 참조 하세요.

## <a name="assess-sql-managed-instance-compatibility"></a>SQL Managed Instance 호환성 평가

먼저 SQL Managed Instance 응용 프로그램의 데이터베이스 요구 사항과 호환 되는지 확인 합니다. SQL Managed Instance는 SQL Server를 사용 하는 대부분의 기존 응용 프로그램에 대 한 간편한 리프트 및 이동 마이그레이션을 제공 하도록 설계 되었습니다. 그러나 아직 지원 되지 않는 기능 또는 기능을 필요로 하는 경우가 있으며 해결 방법을 구현 하는 비용이 너무 높습니다.

[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) 를 사용 하 여 Azure SQL Database의 데이터베이스 기능에 영향을 주는 잠재적 호환성 문제를 검색 합니다. 일부 보고 된 차단 문제가 있는 경우 [AZURE VM의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)와 같은 대체 옵션을 고려해 야 할 수 있습니다. 다음은 몇 가지 예입니다.

- 운영 체제 또는 파일 시스템에 직접 액세스 해야 하는 경우 (예를 들어 SQL Server를 사용 하 여 동일한 가상 컴퓨터에 타사 또는 사용자 지정 에이전트를 설치 하는 경우)
- FileStream/FileTable, PolyBase 및 인스턴스 간 트랜잭션과 같이 아직 지원 되지 않는 기능에 대해 엄격한 종속성이 있는 경우
- 특정 버전의 SQL Server을 유지 해야 하는 경우 (예를 들어 2012)
- 계산 요구 사항이 관리 되는 인스턴스 (예를 들어 vCore 하나)에서 제공 하는 것 보다 훨씬 더 낮은 경우 데이터베이스 통합은 허용 되는 옵션이 아닙니다.

식별 된 모든 마이그레이션 차단기를 해결 하 고 SQL Managed Instance로의 마이그레이션을 계속 하는 경우 일부 변경 내용이 워크 로드의 성능에 영향을 줄 수 있습니다.

- 정기적으로 단순/대량 로그 모델을 사용 했거나 요청 시 백업을 중지 한 경우 필수 전체 복구 모델 및 정기적인 자동 백업 일정은 워크 로드 또는 유지 관리/ETL 작업의 성능에 영향을 줄 수 있습니다.
- 다른 서버 또는 데이터베이스 수준 구성 (예: 추적 플래그 또는 호환성 수준)
- TDE (투명 데이터베이스 암호화) 또는 자동 장애 조치 (failover) 그룹과 같은 사용 중인 새로운 기능은 CPU 및 IO 사용에 영향을 줄 수 있습니다.

SQL Managed Instance는 중요 한 시나리오 에서도 99.99%의 가용성을 보장 하므로 이러한 기능으로 인해 발생 하는 오버 헤드는 사용 하지 않도록 설정할 수 없습니다. 자세한 내용은 [SQL Server 및 AZURE SQL Managed Instance에서 다른 성능을 발생 시킬 수 있는 근본 원인](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)을 참조 하세요.

### <a name="create-a-performance-baseline"></a>성능 기준선 만들기

SQL Server에서 실행 되는 원래 워크 로드와 관리 되는 인스턴스의 워크 로드 성능을 비교 해야 하는 경우 비교에 사용할 성능 기준선을 만들어야 합니다.

성능 기준은 평균/최대 CPU 사용량, 평균/최대 디스크 IO 대기 시간, 처리량, IOPS, 평균/최대 페이지 수명 예상 및 tempdb의 평균 최대 크기와 같은 매개 변수 집합입니다. 마이그레이션 후에 비슷하거나 더 나은 매개 변수를 사용할 수 있으므로 이러한 매개 변수의 기준 값을 측정 하 고 기록 하는 것이 중요 합니다. 시스템 매개 변수 외에도 워크 로드에서 대표적인 쿼리 집합 또는 가장 중요 한 쿼리를 선택 하 고 선택한 쿼리의 최소/평균/최대 기간 및 CPU 사용량을 측정 해야 합니다. 이러한 값을 사용 하면 관리 되는 인스턴스에서 실행 되는 워크 로드의 성능을 원본 SQL Server 인스턴스의 원래 값과 비교할 수 있습니다.

SQL Server 인스턴스에서 측정 해야 하는 매개 변수는 다음과 같습니다.

- [SQL Server 인스턴스의 cpu 사용량을 모니터링](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) 하 고 평균 및 최대 cpu 사용량을 기록 합니다.
- [SQL Server 인스턴스의 메모리 사용량을 모니터링](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) 하 고 버퍼 풀, 계획 캐시, 열 저장소 풀, [메모리 내 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)등의 여러 구성 요소에서 사용 하는 메모리 양을 확인 합니다. 또한 페이지 수명 예상 메모리 성능 카운터의 평균 및 피크 값을 찾아야 합니다.
- [Dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 뷰 또는 [성능 카운터](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)를 사용 하 여 원본 SQL Server 인스턴스에서 디스크 IO 사용량을 모니터링 합니다.
- SQL Server 2016 이상 버전에서 마이그레이션하는 경우 동적 관리 뷰 또는 쿼리 저장소를 검토 하 여 워크 로드 및 쿼리 성능 또는 SQL Server 인스턴스를 모니터링할 수 있습니다. 작업에서 가장 중요 한 쿼리의 평균 기간 및 CPU 사용량을 확인 하 여 관리 되는 인스턴스에서 실행 되는 쿼리와 비교 합니다.

> [!Note]
> 높은 CPU 사용량, 상수 메모리 부족 또는 tempdb 또는 매개 변수화와 같은 SQL Server 작업에 문제가 발생 하는 경우에는 기준선 및 마이그레이션을 수행 하기 전에 원본 SQL Server 인스턴스에서 해당 문제를 해결 해야 합니다. 알려진 문제를 새 시스템으로 마이그레이션하면 예기치 않은 결과가 발생 하 고 성능 비교가 무효화 될 수 있습니다.

이 작업의 결과로 원본 시스템의 CPU, 메모리 및 IO 사용에 대 한 평균 및 최고 값과 워크 로드에서 가장 중요 한 쿼리의 평균 및 최대 기간과 CPU 사용량을 문서화 해야 합니다. 이러한 값을 나중에 사용 하 여 관리 되는 인스턴스의 작업 성과를 원본 SQL Server 인스턴스에 대 한 작업의 기준 성능과 비교할 수 있습니다.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>최적 크기의 관리되는 인스턴스에 배포

SQL Managed Instance는 클라우드로 이동할 계획인 온-프레미스 워크 로드에 맞게 조정 됩니다. 작업에 적합한 수준의 리소스를 선택할 때 유연성이 높은 [새 구매 모델](../database/service-tiers-vcore.md)이 도입되었습니다. 온-프레미스 환경에서는 실제 코어 및 IO 대역폭을 사용하여 이러한 작업의 크기를 조정하는 데 익숙할 것입니다. 관리되는 인스턴스에 대한 구매 모델은 가상 코어 수 또는 "vCore 수"를 기반으로 하며, 추가 스토리지 및 IO를 별도로 사용할 수 있습니다. vCore 모델은 현재 온-프레미스에서 사용하는 제품과 비교하여 클라우드의 컴퓨팅 요구 사항을 더 쉽게 이해할 수 있는 방법입니다. 새로운 이 모델을 사용하면 클라우드에서 대상 환경의 크기를 올바르게 조정할 수 있습니다. 올바른 서비스 계층 및 특성을 선택 하는 데 도움이 되는 몇 가지 일반적인 지침은 여기에 설명 되어 있습니다.

- 기준 CPU 사용량을 기준으로 SQL Server에서 사용 하는 코어 수와 일치 하는 관리 되는 인스턴스를 프로 비전 할 수 있습니다. 관리 되는 [인스턴스가 설치 된 VM 특성과](resource-limits.md#hardware-generation-characteristics)일치 하도록 CPU 특성을 확장 해야 할 수도 있습니다.
- 기준 메모리 사용량에 따라 일치 하는 [메모리가 있는 서비스 계층](resource-limits.md#hardware-generation-characteristics)을 선택 합니다. 메모리 크기를 직접 선택할 수 없기 때문에 일치 하는 메모리가 있는 Vcores의 크기 (예: Gen5의 5.1 g b/Vcores)를 사용 하 여 관리 되는 인스턴스를 선택 해야 합니다.
- 파일 하위 시스템의 기준 IO 대기 시간에 따라 일반적인 용도 (5 밀리초 보다 긴 대기 시간)와 중요 비즈니스용 (대기 시간 3 밀리초 미만) 서비스 계층 중에서 선택 합니다.
- 기준 처리량을 기준으로 데이터 또는 로그 파일의 크기를 미리 할당 하 여 예상 IO 성능을 얻습니다.

배포 시 계산 및 저장소 리소스를 선택한 후 [Azure Portal](../database/scale-resources.md)를 사용 하 여 응용 프로그램의 가동 중지 시간을 발생 시 키 지 않고 나중에 변경할 수 있습니다.

![관리 되는 인스턴스 크기 조정](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

VNet 인프라와 관리되는 인스턴스를 만드는 방법을 알아보려면 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.

> [!IMPORTANT]
> [관리 되는 인스턴스 vnet 요구 사항](connectivity-architecture-overview.md#network-requirements)에 따라 대상 VNet 및 서브넷을 유지 하는 것이 중요 합니다. 호환되지 않는 경우 새 인스턴스를 만들거나 이미 만든 인스턴스를 사용하지 못할 수 있습니다. [새 네트워크 만들기](virtual-network-subnet-create-arm-template.md) 및 [기존 네트워크 구성](vnet-existing-add-subnet.md)에 대해 자세히 알아보세요.

## <a name="select-a-migration-method-and-migrate"></a>마이그레이션 방법 선택 및 마이그레이션

SQL Managed Instance은 온-프레미스 또는 Azure VM 데이터베이스 구현에서 대량 데이터베이스 마이그레이션이 필요한 사용자 시나리오를 대상으로 합니다. 정기적으로 인스턴스 수준 및/또는 데이터베이스 간 기능을 사용 하는 응용 프로그램의 백 엔드를 리프트 앤 시프트 해야 하는 경우에 적합 합니다. 이러한 시나리오의 경우 애플리케이션을 다시 구성하지 않고도 Azure에서 전체 인스턴스를 해당 환경으로 이동할 수 있습니다.

SQL 인스턴스를 이동하려면 다음을 신중하게 계획해야 합니다.

- 배치 된 해야 하는 모든 데이터베이스 (동일한 인스턴스에서 실행 되는 데이터베이스)의 마이그레이션
- 로그인, 자격 증명, SQL 에이전트 작업 및 연산자 및 서버 수준 트리거를 포함 하 여 응용 프로그램이 종속 된 인스턴스 수준 개체의 마이그레이션

SQL Managed Instance는 기본 제공 되는 DBA 활동 중 일부를 플랫폼에 위임할 수 있는 관리 되는 서비스입니다. 따라서 [고가용성](../database/high-availability-sla.md) 이 기본적으로 제공 되므로 정기적인 백업 또는 Always On 구성에 대 한 유지 관리 작업과 같이 일부 인스턴스 수준 데이터를 마이그레이션할 필요가 없습니다.

SQL Managed Instance는 다음과 같은 데이터베이스 마이그레이션 옵션을 지원 합니다 (현재 유일 하 게 지원 되는 마이그레이션 방법).

- Azure Database Migration Service-가동 중지 시간이 거의 없는 마이그레이션
- 네이티브 `RESTORE DATABASE FROM URL` - SQL Server의 네이티브 백업을 사용하며 약간의 가동 중지 시간 필요

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database Migration Service](../../dms/dms-overview.md) 은 가동 중지 시간을 최소화 하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활 하 게 마이그레이션할 수 있도록 설계 된 완전히 관리 되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure로 이동 하는 데 필요한 작업을 간소화 합니다. 공개 미리 보기의 배포 옵션에는 Azure SQL Database 데이터베이스와 Azure 가상 컴퓨터의 SQL Server 데이터베이스가 포함 됩니다. Database Migration Service은 엔터프라이즈 워크 로드에 대해 권장 되는 마이그레이션 방법입니다.

SQL Server 온-프레미스에서 SSIS (SQL Server Integration Services)를 사용 하는 경우 Database Migration Service는 SSIS 패키지를 저장 하는 SSISDB (ssis 카탈로그) 마이그레이션을 아직 지원 하지 않지만, Azure Data Factory에서 IR (Azure-SSIS Integration Runtime)을 프로 비전 할 수 있습니다. 그러면 관리 되는 인스턴스에서 새 SSISDB를 만들어 패키지를 다시 배포할 수 있습니다. [Azure Data Factory에서 Azure-SSIS IR 만들기를](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)참조 하세요.

이 시나리오 및 Database Migration Service 구성 단계에 대 한 자세한 내용은 Database Migration Service를 [사용 하 여 온-프레미스 데이터베이스를 관리 되는 인스턴스로 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조 하세요.  

### <a name="native-restore-from-url"></a>URL에서 네이티브 복원

[Azure Storage](https://azure.microsoft.com/services/storage/)에서 사용할 수 있는 SQL Server 인스턴스에서 가져온 네이티브 백업 (.bak 파일)의 복원은 SQL Managed Instance의 주요 기능 중 하나로, 빠르고 쉽게 오프 라인 데이터베이스 마이그레이션을 가능 하 게 합니다.

다음 다이어그램은 프로세스의 상위 수준 개요를 제공합니다.

![마이그레이션 흐름](./media/migrate-to-instance-from-sql-server/migration-flow.png)

다음 표에서는 실행되는 원본 SQL Server 버전에 따라 사용할 수 있는 방법에 대한 자세한 정보를 제공합니다.

|단계|SQL 엔진 및 버전|백업/복원 방법|
|---|---|---|
|Azure Storage에 백업 저장|2012 SP1 이전 CU2|.Bak 파일을 Azure Storage에 직접 업로드|
||2012 SP1 CU2~2016|사용되지 않는 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 구문을 사용하여 직접 백업|
||2016 이상|[WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 구문을 사용하여 직접 백업|
|Azure Storage에서 관리 되는 인스턴스로 복원|[SAS CREDENTIAL을 사용하여 URL에서 복원](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - [투명한 데이터 암호화](../database/transparent-data-encryption-tde-overview.md) 로 보호 되는 데이터베이스를 네이티브 복원 옵션을 사용 하 여 관리 되는 인스턴스로 마이그레이션하려는 경우에는 온-프레미스 또는 Azure VM SQL Server의 해당 인증서를 데이터베이스 복원 전에 마이그레이션해야 합니다. 자세한 단계는 [TDE 인증서를 관리 되는 인스턴스로 마이그레이션](tde-certificate-migrate.md)을 참조 하세요.
> - 시스템 데이터베이스의 복원은 지원되지 않습니다. Master 또는 msdb 데이터베이스에 저장 된 인스턴스 수준 개체를 마이그레이션하려면 대상 인스턴스에서 T-sql 스크립트를 실행 하 고 실행 하는 것이 좋습니다.

SAS 자격 증명을 사용하여 데이터베이스 백업을 관리되는 인스턴스에 복원하는 방법을 보여주는 빠른 시작은 [백업에서 관리되는 인스턴스로 복원](restore-sample-database-quickstart.md)을 참조하세요.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>애플리케이션 모니터링

관리 되는 인스턴스로의 마이그레이션을 완료 한 후에는 작업의 응용 프로그램 동작 및 성능을 추적 해야 합니다. 이 프로세스에는 다음과 같은 작업이 포함 됩니다.

- [관리 되는 인스턴스에서 실행 되는 워크 로드의 성능을](#compare-performance-with-the-baseline) [원본 SQL Server 인스턴스에서 만든 성능 기준선](#create-a-performance-baseline)과 비교 합니다.
- [작업 성능을](#monitor-performance) 지속적으로 모니터링 하 여 잠재적인 문제 및 개선을 파악 합니다.

### <a name="compare-performance-with-the-baseline"></a>성과를 기준선과 비교

성공적으로 마이그레이션한 후 즉시 수행 해야 하는 첫 번째 작업은 작업의 성능과 기준 워크 로드 성능을 비교 하는 것입니다. 이 작업의 목표는 관리 되는 인스턴스의 워크 로드 성능이 요구를 충족 하는지 확인 하는 것입니다.

관리 되는 인스턴스로 데이터베이스를 마이그레이션하면 대부분의 경우 데이터베이스 설정과 원래 호환성 수준이 유지 됩니다. 원본 SQL Server 인스턴스와 비교 하 여 일부 성능 저하 위험을 줄이기 위해 가능한 경우 원래 설정이 유지 됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 100 이상인 경우 마이그레이션 후에도 동일하게 유지됩니다. 마이그레이션 전에 사용자 데이터베이스의 호환성 수준이 90 인 경우 업그레이드 된 데이터베이스에서는 호환성 수준이 관리 되는 인스턴스에서 지원 되는 가장 낮은 호환성 수준인 100로 설정 됩니다. 시스템 데이터베이스의 호환성 수준은 140입니다. 관리 되는 인스턴스로의 마이그레이션은 실제로 최신 버전의 SQL Server 데이터베이스 엔진으로 마이그레이션하기 때문에 몇 가지 놀라운 성능 문제를 방지 하기 위해 워크 로드의 성능을 다시 테스트 해야 한다는 점에 유의 해야 합니다.

필수 구성 요소로, 다음 작업을 완료 했는지 확인 합니다.

- 다양 한 인스턴스, 데이터베이스, tempdb 설정 및 구성을 조사 하 여 관리 되는 인스턴스의 설정을 원본 SQL Server 인스턴스의 설정과 맞춥니다. 첫 번째 성능 비교를 실행 하기 전에 호환성 수준 또는 암호화와 같은 설정을 변경 하지 않았는지 확인 하거나 사용 하도록 설정한 일부 새로운 기능이 일부 쿼리에 영향을 줄 수 있다는 것을 허용 합니다. 마이그레이션 위험을 줄이려면 성능 모니터링 후에만 데이터베이스 호환성 수준을 변경합니다.
- 더 나은 성능을 얻기 위해 파일 크기를 미리 할당 하는 등 [일반적인 용도로 저장소 모범 사례 지침](https://techcommunity.microsoft.com)을 구현 합니다.
- [관리 되는 인스턴스와 SQL Server 간의 성능 차이를 일으킬 수 있는 주요 환경 차이점](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)에 대해 알아보고 성능에 영향을 줄 수 있는 위험을 식별 합니다.
- 관리 되는 인스턴스에서 쿼리 저장소 및 자동 조정을 계속 사용 하도록 설정 해야 합니다. 이러한 기능을 사용 하 여 워크 로드 성능을 측정 하 고 잠재적인 성능 문제를 자동으로 해결할 수 있습니다. [최신 SQL Server 버전으로 업그레이드 하는 동안 성능 안정성 유지](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)의 설명에 따라 데이터베이스 호환성 수준 변경 전후에 워크 로드 성능에 대 한 정보를 얻기 위한 최적의 도구로 쿼리 저장소를 사용 하는 방법을 알아봅니다.
온-프레미스 환경과 최대한 유사한 환경을 준비한 후에는 워크 로드 실행을 시작 하 고 성능을 측정할 수 있습니다. 측정 프로세스 [에는 원본 SQL Server 인스턴스에서 작업 측정값의 기준선 성능을 생성 하는 동안](#create-a-performance-baseline)측정 한 것과 동일한 매개 변수가 포함 되어야 합니다.
결과적으로 성능 매개 변수를 기준선과 비교 하 고 중요 한 차이점을 식별 해야 합니다.

> [!NOTE]
> 대부분의 경우 관리 되는 인스턴스 및 SQL Server에서 정확히 일치 하는 성능을 얻을 수 없습니다. Azure SQL Managed Instance은 SQL Server 데이터베이스 엔진 이지만 관리 되는 인스턴스의 인프라 및 고가용성 구성은 약간의 차이를 일으킬 수 있습니다. 일부 쿼리는 속도가 더 빠르지만 일부 쿼리는 속도가 느릴 수 있습니다. 비교의 목표는 관리 되는 인스턴스의 워크 로드 성능이 SQL Server (평균) 성능과 일치 하는지 확인 하 고, 원래 성능과 일치 하지 않는 성능으로 중요 한 쿼리를 식별 하는 것입니다.

성능 비교의 결과는 다음과 같을 수 있습니다.

- 관리 되는 인스턴스의 워크 로드 성능이 SQL Server의 워크 로드 성능 보다 잘 맞춰져 있습니다. 이 경우 마이그레이션이 성공적으로 완료 되었음을 확인 했습니다.
- 성능 매개 변수 및 워크 로드의 쿼리 대부분은 성능이 저하 되는 몇 가지 예외를 제외 하 고는 정상적으로 작동 합니다. 이 경우 차이점 및 중요도를 확인 해야 합니다. 성능이 저하 된 몇 가지 중요 한 쿼리가 있는 경우 기본 SQL 계획이 변경 되었는지 또는 쿼리가 일부 리소스 제한에 도달 하는지 조사 해야 합니다. 이 경우에는 계획 지침을 사용 하거나 계획 지침을 사용 하 여 계획에 영향을 줄 수 있는 통계 및 인덱스를 다시 작성 하거나 만들 수 있는 중요 한 쿼리 (예: 변경 된 호환성 수준, 레거시 카디널리티 평가기)에 몇 가지 힌트를 적용할 수 있습니다.
- 대부분의 쿼리는 원본 SQL Server 인스턴스와 비교 하 여 관리 되는 인스턴스에서 속도가 느립니다. 이 경우 IO 제한, 메모리 제한, 인스턴스 로그 전송률 제한 등과 같은 [리소스 제한에 도달](resource-limits.md#service-tier-characteristics) 하는 것과 같은 차이의 근본 원인을 파악 하려고 합니다. 차이를 일으킬 수 있는 리소스 제한이 없는 경우 데이터베이스의 호환성 수준을 변경 하거나 레거시 카디널리티 예측과 같은 데이터베이스 설정을 변경 하 고 테스트를 다시 시작 하십시오. 관리 되는 인스턴스 또는 쿼리 저장소 보기에서 제공 하는 권장 사항을 검토 하 여 성능을 재발 시킨 쿼리를 확인 합니다.

> [!IMPORTANT]
> Azure SQL Managed Instance에는 기본적으로 사용 되는 자동 계획 수정 기능이 기본적으로 제공 됩니다. 이 기능을 사용 하면 나중에 붙여넣을 때 잘 작동 하 던 쿼리가 저하 되지 않습니다. 이 기능을 사용 하도록 설정 하 고 새 설정을 변경 하기 전에 이전 설정으로 충분 한 시간 동안 워크 로드를 실행 했는지 확인 하 여 관리 되는 인스턴스가 기준 성능과 계획에 대해 알아볼 수 있도록 합니다

요구 사항에 맞는 워크 로드 성능을 얻을 때까지 최적의 구성으로 수렴 하도록 매개 변수 또는 업그레이드 서비스 계층을 변경 합니다.

### <a name="monitor-performance"></a>성능 모니터링

SQL Managed Instance는 모니터링 및 문제 해결을 위한 다양 한 고급 도구를 제공 하며, 이러한 도구를 사용 하 여 인스턴스의 성능을 모니터링 해야 합니다. 모니터링 해야 하는 매개 변수 중 일부는 다음과 같습니다.

- 프로 비전 한 vCores 수가 워크 로드와 적절 한지 확인 하기 위해 인스턴스의 CPU 사용량입니다.
- [추가 메모리가 필요한](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)지 여부를 확인 하기 위해 관리 되는 인스턴스의 페이지 수명 예상입니다.
- 또는와 같은 통계 `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` 는 특히 일반적인 용도 계층에서 저장소 io 문제가 있는지 여부를 알려 줍니다. io 성능을 향상 시키기 위해 파일을 미리 할당 해야 할 수도 있습니다.

## <a name="leverage-advanced-paas-features"></a>고급 PaaS 기능 활용

완전히 관리 되는 플랫폼을 사용 하 고 있고 워크 로드 성능이 SQL Server 워크 로드 성능과 일치 하는지 확인 한 후에는 서비스의 일부로 자동으로 제공 되는 이점을 활용 합니다.

마이그레이션하는 동안 관리 되는 인스턴스를 일부 변경 하지 않는 경우에도 최신 데이터베이스 엔진의 향상 된 기능을 활용 하기 위해 인스턴스를 운영 하는 동안 몇 가지 새로운 기능을 설정할 가능성이 높습니다. 일부 변경 내용은 [데이터베이스 호환성 수준이 변경](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)된 후에만 사용할 수 있습니다.

예를 들어 관리 되는 인스턴스에서 백업을 만들 필요가 없습니다. 서비스에서 자동으로 백업을 수행 합니다. 백업 예약, 가져오기 및 관리에 대해 더 이상 걱정할 필요가 없습니다. SQL Managed Instance는 [PITR (지정 시간 복구)](../database/recovery-using-backups.md#point-in-time-restore)를 사용 하 여이 보존 기간 내의 특정 시점으로 복원 하는 기능을 제공 합니다. 또한 고가용성이 기본적으로 제공 되므로 고가용성을 설정 하 [는 것에](../database/high-availability-sla.md) 대해 걱정할 필요가 없습니다.

보안을 강화 하려면 [Azure Active Directory 인증](../database/security-overview.md), [감사](auditing-configure.md), [위협 검색](../database/advanced-data-security.md), [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)및 [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)을 사용 하는 것이 좋습니다.

관리 되는 인스턴스는 고급 관리 및 보안 기능 외에도 [워크 로드를 모니터링 하 고 조정](../database/monitor-tune-overview.md)하는 데 도움이 되는 고급 도구 집합을 제공 합니다. [Azure SQL 분석](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) 를 사용 하면 많은 수의 관리 되는 인스턴스를 모니터링 하 고 많은 수의 인스턴스와 데이터베이스를 중앙 집중식으로 모니터링할 수 있습니다. 관리 되는 인스턴스에서 [자동 튜닝](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) 은 SQL 계획 실행 통계의 성능을 지속적으로 모니터링 하 고 식별 된 성능 문제를 자동으로 해결 합니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL Managed Instance에 대 한 자세한 내용은 [AZURE sql Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- 백업에서 복원을 포함한 자습서는 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Database Migration Service를 사용 하 여 마이그레이션을 보여 주는 자습서는 Database Migration Service를 사용 하 여 [온-프레미스 데이터베이스를 AZURE SQL Managed Instance로](../../dms/tutorial-sql-server-to-managed-instance.md)마이그레이션을 참조 하세요.  
