---
title: Azure SQL Database 리소스 제한 | Microsoft Docs
description: 이 문서에서는 단일 데이터베이스 및 탄력적 풀에 대 한 Azure SQL Database 리소스 제한에 대 한 개요를 제공 합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: da8c194b7911d2eeda8e0c903cb7412186aacfcb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638258"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database 리소스 제한 및 리소스 관리

이 문서에서는 단일 데이터베이스 및 탄력적 풀을 관리하는 SQL Database 서버의 SQL Database 리소스 한도를 간략하게 설명합니다. 리소스 제한에 도달 하거나 초과 하는 경우 발생 하는 상황에 대 한 정보를 제공 하 고 이러한 제한을 적용 하는 데 사용 되는 리소스 관리 메커니즘을 설명 합니다.

> [!NOTE]
> Managed Instance 한도는 [관리되는 인스턴스에 대한 SQL Database 리소스 한도](sql-database-managed-instance-resource-limits.md)를 참조하세요.

## <a name="maximum-resource-limits"></a>최대 리소스 한도

| 리소스 | 제한 |
| :--- | :--- |
| 서버당 데이터베이스 | 5,000 |
| 모든 지역에서 구독당 서버의 기본 수 | 20 |
| 모든 지역에서 구독당 서버의 최대 수 | 200 |  
| 서버당 DTU/eDTU 할당량 | 54,000 |  
| 서버/인스턴스당 vCore 할당량 | 540 |
| 서버당 최대 풀 | DTU 또는 vCore의 수로 제한됩니다. 예를 들어 각 풀에 DTU가 1000개인 경우 서버는 54개 풀을 지원할 수 있습니다.|
|||

> [!NOTE]
> 더 많은 DTU/eDTU 할당량, vCore 할당량 또는 기본 용량 보다 많은 서버를 얻기 위해 문제 유형이 "Quota" 인 구독의 Azure Portal에 새 지원 요청을 제출할 수 있습니다. 서버 당 DTU/eDTU 할당량 및 데이터베이스 한도는 서버당 탄력적 풀 수를 제한 합니다.

> [!IMPORTANT]
> 데이터베이스 수가 SQL Database 서버당 한도에 근접하면 다음이 발생할 수 있습니다.
>
> - 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> - 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

### <a name="storage-size"></a>스토리지 크기

단일 데이터베이스 리소스 저장소 크기의 경우 가격 책정 계층 당 저장소 크기 제한에 대 한 [DTU 기반 리소스 제한](sql-database-dtu-resource-limits-single-databases.md) 또는 [vcore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md) 을 참조 하세요.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>데이터베이스 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute(DTU 및 eDTU / vCore)

데이터베이스 계산 사용률 (Dtu 및 Edtu 또는 vCores로 측정)이 높으면 쿼리 대기 시간이 증가 하 고 쿼리 시간이 초과 될 수도 있습니다. 이러한 조건에서 쿼리는 서비스에 의해 큐에 대기 될 수 있으며 리소스가 사용 가능 해지면 실행할 리소스를 제공 합니다.
높은 컴퓨팅 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기를 늘려 데이터베이스에 더 많은 컴퓨팅 리소스를 제공합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>Storage

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)가 표시됩니다. SELECT 및 DELETE 문은 계속 성공 합니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 저장소를 더 추가 합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 속하는 경우 데이터베이스를 풀 외부로 이동하여 스토리지 공간이 다른 데이터베이스와 공유되지 않도록 할 수 있습니다.
- 데이터베이스를 축소하여 사용하지 않는 공간을 회수합니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청)

세션 및 작업자의 최대 수는 서비스 계층 및 계산 크기 (Dtu/Edtu 또는 vCores)에 의해 결정 됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 애플리케이션에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 이는 데이터베이스 리소스 제한에 도달 했을 때 발생 하는 최대 부하 기간 동안 쿼리, 큰 차단 체인 또는 과도 한 쿼리 병렬 처리로 인해 작업자를 누적 하는 경우에 특히 그렇습니다.

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 컴퓨팅 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

## <a name="resource-governance"></a>리소스 거버넌스

리소스 제한을 적용 하기 위해 Azure SQL Database는 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), 수정 및 확장을 기반으로 하는 리소스 거 버 넌 스 구현을 사용 하 여 Azure에서 SQL Server Database 서비스를 실행 합니다. 서비스의 각 SQL Server 인스턴스에는 풀 및 그룹 수준에서 리소스 제한을 설정 하 여 [분산 된 데이터베이스 서비스](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)를 제공 하는 여러 [리소스 풀](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) 및 [작업 그룹이](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)있습니다. 사용자 작업 및 내부 작업은 별도의 리소스 풀 및 작업 그룹으로 분류 됩니다. 지역 복제본을 비롯 한 주 및 읽기 가능한 보조 복제본의 사용자 작업은 `SloSharedPool1` 리소스 풀 및 `UserPrimaryGroup.DBId[N]` 작업 그룹으로 분류 됩니다. 여기서 `N`는 데이터베이스 ID 값을 나타냅니다. 또한 다양 한 내부 워크 로드에 대 한 여러 리소스 풀 및 작업 그룹이 있습니다.

는 Resource Governor를 사용 하 여 SQL Server Azure SQL Database 프로세스 내에서 리소스를 제어 하는 것 외에도 프로세스 수준 리소스 관리를 위해 Windows [작업 개체](https://docs.microsoft.com/windows/win32/procthread/job-objects) 를 사용 하 고 저장소 할당량 관리를 위해 windows [FSRM (파일 서버 리소스 관리자)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 을 사용 합니다.

Azure SQL Database 리소스 관리는 본질적으로 계층적입니다. 위에서 아래로, 운영 체제 리소스 거 버 넌 스 메커니즘 및 Resource Governor를 사용 하 여 OS 수준 및 저장소 볼륨 수준에서 제한을 적용 한 다음, Resource Governor를 사용 하 여 리소스 풀 수준에서 다음을 사용 하 여 작업 그룹 수준에서 제한을 적용 합니다. Resource Governor. 현재 데이터베이스 또는 탄력적 풀에 적용 되는 리소스 관리 제한은 [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 뷰에 표시 됩니다. 

### <a name="data-io-governance"></a>데이터 IO 거 버 넌 스

데이터 IO 거 버 넌 스는 데이터베이스의 데이터 파일에 대 한 읽기 및 쓰기 물리적 IO를 제한 하는 데 사용 되는 Azure SQL Database의 프로세스입니다. 각 서비스 수준에 대해 IOPS 제한이 설정 되어 "잡음이 있는 환경" 효과를 최소화 하 고, 다중 테넌트 서비스에서 리소스 할당 범위를 제공 하 고, 기본 하드웨어 및 저장소의 기능을 유지할 수 있습니다.

단일 데이터베이스의 경우 데이터베이스에 대 한 모든 저장소 IO에 작업 그룹 제한이 적용 되는 반면, 리소스 풀 제한은 `tempdb` 데이터베이스를 포함 하 여 동일한 SQL Server 인스턴스에 있는 모든 데이터베이스에 대 한 모든 저장소 IO에 적용 됩니다. 탄력적 풀의 경우 작업 그룹 제한은 풀의 각 데이터베이스에 적용 되는 반면, 리소스 풀 제한은 풀의 모든 데이터베이스 간에 공유 되는 `tempdb` 데이터베이스를 포함 하 여 전체 탄력적 풀에 적용 됩니다. 일반적으로 작업 그룹 제한은 리소스 풀 제한 보다 낮으므로 IOPS/처리량을 단축 하기 때문에 데이터베이스 (단일 또는 풀)에 대해 작업에서 리소스 풀 제한을 달성 하지 못할 수 있습니다. 그러나 같은 SQL Server 인스턴스의 여러 데이터베이스에 대해 결합 된 작업으로 인해 풀 제한에 도달할 수 있습니다.

예를 들어 쿼리가 IO 리소스 관리 없이 1000 IOPS를 생성 하지만 작업 그룹 최대 IOPS 한도가 900 IOPS로 설정 된 경우 쿼리는 900 IOPS를 초과 하 여 생성할 수 없습니다. 그러나 리소스 풀의 최대 IOPS 한도가 1500 IOPS로 설정 되 고 리소스 풀과 연결 된 모든 작업 그룹의 총 IO가 1500 IOPS를 초과 하는 경우 동일한 쿼리의 IO가 작업 그룹 제한 (900 IOPS) 아래로 줄어들 수 있습니다.

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 뷰에서 반환 된 IOPS 및 처리량 최소/최대 값은 보장 되는 것이 아니라 극한/cap 역할을 합니다. 또한 리소스 관리는 특정 저장소 대기 시간을 보장 하지 않습니다. 지정 된 사용자 작업에 대해 가장 적합 한 대기 시간, IOPS 및 처리량은 IO 리소스 거 버 넌 스 제한 뿐만 아니라 사용 되는 IO 크기와 기본 저장소의 기능에 따라 달라 집니다. SQL Server는 512 KB와 4mb 사이의 크기를 변경 하는 IOs를 사용 합니다. IOPS 제한을 적용 하기 위해 모든 IO는 크기와 관계 없이 사용 되며 데이터 파일이 있는 데이터베이스는 Azure Storage에서 제외 됩니다. 이 경우 256 KB 보다 큰 IOs는 Azure Storage IO 계정에 맞게 여러 256 KB IOs로 계산 됩니다.

Azure Storage의 데이터 파일을 사용 하는 기본, 표준 및 범용 데이터베이스의 경우 데이터베이스에이 수의 IOPS를 제공 하는 데 충분 한 데이터 파일이 없거나, 데이터가 여러 파일에 균등 하 게 분산 되지 않거나, 기본 blob의 성능 계층이 리소스 관리 제한 보다 낮은 IOPS/처리량을 제한 하는 경우 `primary_group_max_io` 값을 달성할 수 없습니다. 마찬가지로 빈번한 트랜잭션 커밋에 의해 생성 된 작은 로그 Io를 사용 하는 경우 기본 Azure storage blob에 대 한 IOPS 제한으로 인해 작업에서 `primary_max_log_rate` 값을 달성 하지 못할 수 있습니다.

`avg_data_io_percent` 및 `avg_log_write_percent`와 같은 리소스 사용률 값 (예: [dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)및 [elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 보기)은 최대 리소스 거 버 넌 스 제한의 백분율로 계산 됩니다. 따라서 리소스 관리 외의 요소가 IOPS/처리량을 제한 하는 경우 보고 된 리소스 사용률이 100% 미만으로 유지 되는 경우에도 워크 로드 수가 늘어나면 IOPS/처리량을 평면화 하 고 대기 시간을 늘릴 수 있습니다. 

데이터베이스 파일당 읽기 및 쓰기 IOPS, 처리량 및 대기 시간을 확인 하려면 [dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 함수를 사용 합니다. 이 함수는 `avg_data_io_percent`에 대해 고려 되지 않지만 기본 저장소의 IOPS 및 처리량을 사용 하는 백그라운드 IO를 비롯 하 여 데이터베이스에 대 한 모든 IO를 표시 하며 관찰 된 저장소 대기 시간에 영향을 줄 수 있습니다. 또한 함수는 `io_stall_queued_read_ms` 및 `io_stall_queued_write_ms` 열에서 각각 읽기 및 쓰기에 대 한 IO 리소스 관리에 의해 도입 될 수 있는 추가 대기 시간을 표시 합니다.

### <a name="transaction-log-rate-governance"></a>트랜잭션 로그 요금 거 버 넌 스

트랜잭션 로그 속도 거 버 넌 스는 대량 삽입, SELECT INTO 및 인덱스 빌드와 같은 워크 로드에 대 한 높은 수집 속도를 제한 하는 데 사용 되는 Azure SQL Database의 프로세스입니다. 이러한 한도는 초이 수준에서 로그 레코드 생성 속도까지 추적 되 고 적용 되며, 데이터 파일에 대해 실행할 수 있는 IOs 수에 관계 없이 처리량이 제한 됩니다.  현재 트랜잭션 로그 생성 속도는 하드웨어에 종속 된 지점까지 선형적으로 확장 되며 vCore 구매 모델을 사용 하 여 최대 로그 속도는 96 m b/초까지 허용 됩니다. 

> [!NOTE]
> 실제 실제 Io 트랜잭션 로그 파일은 제어 되거나 제한 되지 않습니다.

로그 속도는 다양 한 시나리오에서 달성 하 고 유지할 수 있도록 설정 되며, 전체 시스템은 사용자 부하에 대 한 영향이 최소화 된 기능을 유지할 수 있습니다. 로그 전송률 관리는 트랜잭션 로그 백업이 게시 된 복구 가능성 Sla 내에 유지 되도록 합니다.  이 거 버 넌 스는 또한 보조 복제본의 과도 한 백로그를 방지 합니다.

로그 레코드가 생성 되 면 각 작업이 평가 되 고, 최대 원하는 로그 전송률 (초당 MB/s)을 유지 하기 위해 지연 되어야 하는지 여부가 평가 됩니다. 로그 레코드가 저장소에 플러시될 때 지연 시간이 추가 되지 않고 로그 속도가 자동으로 생성 되는 동안 로그 속도가 적용 됩니다.

런타임에 적용 되는 실제 로그 생성 요금은 피드백 메커니즘의 영향을 받을 수 있으므로 시스템이 안정화 될 수 있도록 허용 가능한 로그 비율을 일시적으로 줄일 수 있습니다. 로그 파일 공간 관리를 사용 하 여 로그 공간 부족 상태를 방지 하 고 가용성 그룹 복제 메커니즘을 통해 전체 시스템 제한을 일시적으로 낮출 수 있습니다.

로그 전송률 관리자 트래픽 셰이핑은 다음 대기 유형 ( [dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV에 노출 됨)을 통해 표시 됩니다.

| 대기 유형 | 메모 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 데이터베이스 제한 |
| POOL_LOG_RATE_GOVERNOR | 풀 제한 |
| INSTANCE_LOG_RATE_GOVERNOR | 인스턴스 수준 제한 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 피드백 컨트롤, 가용성 그룹 프리미엄/중요 비즈니스용의 물리적 복제를 유지 하지 않음 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 로그 공간 부족 상태를 방지 하기 위해 사용자 의견 제어, 속도 제한 |
|||

원하는 확장성을 hampering 로그 전송률 제한이 발생할 경우 다음 옵션을 고려 하십시오.
- 최대 96 m b/초 로그 속도를 얻기 위해 더 높은 서비스 수준으로 확장 합니다. 
- 데이터를 로드 하는 경우 ETL 프로세스에서 데이터를 준비 하는 것과 같이 임시 데이터는 tempdb로 로드 될 수 있습니다 (최소 기록 됨). 
- 분석 시나리오의 경우 클러스터형 columnstore 대상 테이블로 로드 합니다. 이렇게 하면 압축으로 인해 필요한 로그 전송률이 줄어듭니다. 이 기법은 CPU 사용률을 높이고 클러스터형 columnstore 인덱스를 활용 하는 데이터 집합에만 적용 됩니다. 

## <a name="next-steps"></a>다음 단계

- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)를 참조하세요.
- tempdb 크기 한도에 대한 자세한 내용은 [Azure SQL Database의 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.
