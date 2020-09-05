---
title: 자동 지역 중복 백업
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance 몇 분 마다 자동으로 로컬 데이터베이스 백업을 만들고 지역 중복에 대해 Azure 읽기 액세스 지역 중복 저장소를 사용 합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 08/04/2020
ms.openlocfilehash: 485a137f552ca06fba366d261eb38268d821ccaf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853210"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>자동화 된 백업-SQL Managed Instance & Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>데이터베이스 백업이란?

데이터베이스 백업은 손상 또는 삭제 로부터 데이터를 보호 하기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 이러한 백업은 구성 된 보존 기간 내의 특정 시점으로 데이터베이스를 복원할 수 있도록 합니다. 데이터 보호 규칙에서 오랜 시간 동안 백업을 사용할 수 있어야 하는 경우 (최대 10 년), 단일 데이터베이스 및 풀링된 데이터베이스에 대 한 [장기 보존](long-term-retention-overview.md) 을 구성할 수 있습니다.

### <a name="backup-frequency"></a>Backup 주기

SQL Database와 SQL Managed Instance는 모두 SQL Server 기술을 사용 하 여 매주 [전체 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) , 12-24 시간 마다 [차등 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 및 5 ~ 10 분 마다 [트랜잭션 로그 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 만듭니다. 트랜잭션 로그 백업의 빈도는 컴퓨팅 크기와 데이터베이스 작업의 양을 기준으로 합니다.

사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 판단합니다.

### <a name="backup-storage-redundancy"></a>백업 저장소 중복성

기본적으로 SQL Database 및 SQL Managed Instance는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)에 복제 되는 지역 중복 (RA-GRS) [저장소 blob](../../storage/common/storage-redundancy.md) 에 데이터를 저장 합니다. 이를 통해 주 지역의 백업 저장소에 영향을 주는 중단 으로부터 보호 하 고 재해 발생 시 다른 지역으로 서버를 복원할 수 있습니다. 

SQL Managed Instance는 저장소 중복성을 LRS (로컬 중복) 또는 ZRS (영역 중복) 저장소 blob로 변경 하 여 관리 되는 인스턴스가 배포 되는 동일한 지역 내에 데이터가 유지 되도록 하는 기능을 도입 했습니다. 저장소 중복성 메커니즘은 일시적인 하드웨어 오류, 네트워크 또는 정전 또는 대규모 자연 재해를 비롯 하 여 계획 되거나 계획 되지 않은 이벤트에서 보호 되도록 데이터의 여러 복사본을 저장 합니다. 

백업 저장소 중복성을 구성 하는 옵션은 SQL Managed Instance에 대 한 LRS, ZRS 또는 RA GRS storage blob 중에서 선택할 수 있는 유연성을 제공 합니다. 관리 되는 인스턴스 만들기 프로세스 중에 백업 저장소 중복성 구성 리소스가 프로 비전 되 면 저장소 중복성을 더 이상 변경할 수 없습니다. (ZRS (영역 중복 저장소)는 현재 [특정 지역](../../storage/common/storage-redundancy.md#zone-redundant-storage)에서만 사용할 수 있습니다.)


> [!IMPORTANT]
> SQL Managed Instance에서 구성 된 백업 중복성은 PITR (지정 시간 복원)에 사용 되는 단기 백업 보존 설정과 장기 백업 (LTR)에 사용 되는 장기 보존 백업 모두에 적용 됩니다.


> [!NOTE]
> 구성 가능한 백업 저장소 중복성은 현재 동남 아시아 Azure 지역의 특정 고객에 대해 제한 된 비공개 미리 보기로 제공 됩니다. Azure SQL Database 이 비공개 미리 보기에서 등록을 고려 하려면에 문의 하세요 [sqlbackuppreview@microsoft.com](mailto:sqlbackuppreview@microsoft.com) . 

데이터 보호 규칙에서 오랜 시간 동안 백업을 사용할 수 있어야 하는 경우 (최대 10 년), 단일 데이터베이스 및 풀링된 데이터베이스에 대 한 [장기 보존](long-term-retention-overview.md) 을 구성할 수 있습니다.

### <a name="backup-usage"></a>백업 사용


백업을 사용하여 다음을 수행할 수 있습니다.

- **기존 데이터베이스**  -  의 지정 시간 복원 Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 기존 데이터베이스를 보존 기간 내의 [특정 시점으로 복원](recovery-using-backups.md#point-in-time-restore) 합니다. SQL Database에서이 작업은 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만들지만 다른 이름을 사용 하 여 원본 데이터베이스를 덮어쓰지 않도록 합니다. 복원이 완료 된 후 원본 데이터베이스를 삭제할 수 있습니다. 또는 원래 데이터베이스의 [이름을 바꾼](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) 다음 복원 된 데이터베이스의 이름을 원래 데이터베이스 이름으로 바꿀 수 있습니다. 마찬가지로 SQL Managed Instance의 경우이 작업은 동일한 구독 및 동일한 지역에 있는 동일 하거나 다른 관리 되는 인스턴스에 데이터베이스의 복사본을 만듭니다.
- **삭제 된 데이터베이스**  -  의 지정 시간 복원 삭제 된 [데이터베이스를 삭제 시간으로 복원](recovery-using-backups.md#deleted-database-restore) 하거나 보존 기간 내의 특정 시점으로 복원 합니다. 삭제 된 데이터베이스는 원래 데이터베이스가 만들어진 것과 동일한 서버 또는 관리 되는 인스턴스에서만 복원할 수 있습니다. 데이터베이스를 삭제 하는 경우 서비스는 데이터 손실을 방지 하기 위해 삭제 전에 최종 트랜잭션 로그 백업을 수행 합니다.
- **지역 복원**  -  [데이터베이스를 다른 지역으로 복원](recovery-using-backups.md#geo-restore)합니다. 지역 복원 기능을 사용 하면 주 지역에서 데이터베이스 또는 백업에 액세스할 수 없는 경우 지리적 재해 로부터 복구할 수 있습니다. 모든 Azure 지역의 기존 서버 또는 관리 되는 인스턴스에 새 데이터베이스를 만듭니다.
   > [!IMPORTANT]
   > 지역 복원은 GRS (지역 중복) 백업 저장소가 구성 된 관리 되는 인스턴스에서만 사용할 수 있습니다.
- **장기 백업**  -  에서 복원 데이터베이스를 LTR (장기 보존 정책)로 구성한 경우 단일 데이터베이스 또는 풀링된 데이터베이스의 [특정 장기 백업에서 데이터베이스를 복원](long-term-retention-overview.md) 합니다. LTR을 사용하면 [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) 또는 [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell)을 사용해 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 애플리케이션을 실행할 수 있습니다. 자세한 내용은 [장기 보존](long-term-retention-overview.md)을 참조하세요.

복원을 수행하려면 [백업에서 데이터베이스 복원](recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 blob을 복사 하는 것을 의미 합니다. SQL에서 *데이터베이스 복제* 는 여러 보조 데이터베이스를 주 데이터베이스와 동기화 된 상태로 유지 하는 데 사용 되는 다양 한 기술을 나타냅니다.

다음 예제를 사용 하 여 백업 구성 및 복원 작업을 시도할 수 있습니다.

| 작업(Operation) | Azure portal | Azure PowerShell |
|---|---|---|
| **백업 보존 변경** | [SQL 데이터베이스](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Managed Instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL 데이터베이스](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **장기 백업 보존 변경** | [SQL 데이터베이스](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL Managed Instance-해당 없음  | [SQL 데이터베이스](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **지정 시간에서 데이터베이스 복원** | [SQL 데이터베이스](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **삭제된 데이터베이스 복원** | [SQL 데이터베이스](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Azure Blob Storage에서 데이터베이스 복원** | SQL Database-해당 없음 <br/>SQL Managed Instance-해당 없음  | SQL Database-해당 없음 <br/>[SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>백업 일정

첫 번째 전체 백업은 새 데이터베이스를 만들거나 복원한 직후에 예약 됩니다. 이 백업은 일반적으로 30분 이내에 완료되지만 데이터베이스의 크기가 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어 초기 백업은 복원 된 데이터베이스 또는 데이터베이스 복사본에서 더 오래 걸릴 수 있으며이는 일반적으로 새 데이터베이스 보다 더 큽니다. 첫 번째 전체 백업 후에는 모든 추가 백업이 예약 되 고 자동으로 관리 됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전체 시스템 작업 부하를 분산 하는 SQL Database 또는 SQL Managed Instance 서비스에 의해 결정 됩니다. 백업 작업의 일정을 변경 하거나 사용 하지 않도록 설정할 수 없습니다.

> [!IMPORTANT]
> 새 데이터베이스, 복원 된 데이터베이스 또는 복사 된 데이터베이스의 경우 초기 전체 백업 다음에 오는 초기 트랜잭션 로그 백업이 생성 된 시점부터 지정 시간 복원 기능을 사용할 수 있게 됩니다.

## <a name="backup-storage-consumption"></a>백업 스토리지 사용량

SQL Server 백업 및 복원 기술을 사용 하 여 데이터베이스를 지정 시간으로 복원 하려면 하나의 전체 백업, 선택적으로 하나의 차등 백업 및 하나 이상의 트랜잭션 로그 백업으로 구성 된 중단 없는 백업 체인이 필요 합니다. SQL Database 및 SQL Managed Instance 백업 일정에는 매주 하나의 전체 백업이 포함 됩니다. 따라서 전체 보존 기간 내에 PITR를 사용 하도록 설정 하려면 시스템은 구성 된 보존 기간 보다 최대 한 주 동안 더 많은 전체, 차등 및 트랜잭션 로그 백업을 저장 해야 합니다. 

즉, 보존 기간 중의 특정 시점에는 보존 기간에서 가장 오래 된 시간 보다 오래 된 전체 백업이 있어야 하 고, 다음 전체 백업에서 해당 전체 백업으로의 중단 된 차등 및 트랜잭션 로그 백업 체인도 있어야 합니다.

> [!NOTE]
> PITR를 사용 하도록 설정 하기 위해 추가 백업은 구성 된 보존 기간 보다 최대 일주일 동안 저장 됩니다. 백업 저장소는 모든 백업에 대해 동일한 속도로 청구 됩니다. 

PITR 기능을 제공 하는 데 더 이상 필요 하지 않은 백업은 자동으로 삭제 됩니다. 차등 백업 및 로그 백업에서 복원 가능한 하기 위해 이전 전체 백업을 수행 해야 하기 때문에 세 가지 백업 유형이 모두 주별 집합에서 함께 제거 됩니다.

[Tde 암호화 된](transparent-data-encryption-tde-overview.md) 데이터베이스를 포함 하는 모든 데이터베이스의 경우 백업 저장소 압축 및 비용을 줄이기 위해 백업이 압축 됩니다. 평균 백업 압축 비율은 3-4 회 이지만 데이터의 특성과 데이터베이스에서 데이터 압축이 사용 되는지 여부에 따라 훨씬 더 낮을 수 있습니다.

SQL Database 및 SQL Managed Instance 사용 된 총 백업 저장소를 누적 값으로 계산 합니다. 매시간 이 값은 Azure 청구 파이프라인에 보고되며, 매시간 사용량을 집계하여 각 월말에 사용량을 계산하는 데 사용됩니다. 데이터베이스가 삭제 된 후에는 백업이 만료 되 고 삭제 되 면 소비가 줄어듭니다. 모든 백업이 삭제 되 고 PITR 더 이상 불가능 한 경우 청구는 중지 됩니다.
   
> [!IMPORTANT]
> 데이터베이스가 삭제 된 경우에도 PITR를 사용 하도록 설정 하기 위해 데이터베이스 백업이 유지 됩니다. 데이터베이스를 삭제 하 고 다시 만들면 저장소 및 계산 비용이 절감 될 수 있지만, 서비스는 삭제 될 때마다 삭제 된 각 데이터베이스에 대 한 백업을 유지 하므로 백업 저장소 비용이 늘어날 수 있습니다. 

### <a name="monitor-consumption"></a>사용량 모니터링

VCore 데이터베이스의 경우 각 백업 유형 (전체, 차등 및 로그)에서 사용 하는 저장소는 데이터베이스 모니터링 블레이드에서 별도의 메트릭으로 보고 됩니다. 다음 다이어그램에서는 단일 데이터베이스에 대한 백업 스토리지 사용량을 모니터링하는 방법을 보여 줍니다. 이 기능은 현재 관리 되는 인스턴스에 사용할 수 없습니다.

![Azure Portal에서 데이터베이스 백업 사용량 모니터링](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>백업 스토리지 사용량 미세 조정

데이터베이스의 최대 데이터 크기까지 백업 저장소 사용량에는 요금이 부과 되지 않습니다. 과도 한 백업 저장소 소비는 개별 데이터베이스의 워크 로드 및 최대 크기에 따라 달라 집니다. 백업 스토리지 사용량을 줄이기 위해 다음과 같은 몇 가지 튜닝 기법을 고려합니다.

- [백업 보존 기간](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)을 사용자 요구에 따라 가능한 최소 기간으로 줄입니다.
- 인덱스 다시 빌드와 같은 대량 쓰기 작업은 수행하지 않는 것이 좋습니다.
- 대량 데이터 로드 작업의 경우에는 [클러스터형 columnstore 인덱스](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) 와 관련 된 [모범 사례](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)를 사용 하 고 클러스터 되지 않은 인덱스의 수를 줄이는 것이 좋습니다.
- 범용 서비스 계층에서 프로 비전 된 데이터 저장소는 백업 저장소의 가격 보다 저렴 합니다. 과도 한 백업 저장소 비용이 지속적으로 증가 하는 경우 백업 저장소에 저장 하기 위해 데이터 저장소를 늘릴 수 있습니다.
- 임시 결과 및/또는 임시 데이터를 저장 하기 위해 응용 프로그램 논리에서 영구 테이블 대신 TempDB를 사용 합니다.
- 가능 하면 항상 로컬 중복 백업 저장소 사용 (예: 개발/테스트 환경)

## <a name="backup-retention"></a>백업 보존

모든 새로 복원 및 복사 된 데이터베이스, Azure SQL Database 및 Azure SQL Managed Instance는 최근 7 일 내에 기본적으로 PITR을 허용 하기 위해 충분 한 백업을 유지 합니다. Hyperscale 데이터베이스를 제외 하 고 1-35 일 범위의 각 활성 데이터베이스에 대해 [백업 보존 기간을 변경할](#change-the-pitr-backup-retention-period) 수 있습니다. [백업 저장소](#backup-storage-consumption)사용에 설명 된 대로 PITR를 사용 하도록 설정 된 백업이 보존 기간 보다 오래 되었을 수 있습니다. Azure SQL Managed Instance에만 해당 하는 경우 데이터베이스가 0-35 일 범위에서 삭제 되 면 PITR 백업 보존 률을 설정할 수 있습니다. 

데이터베이스를 삭제 하는 경우 시스템은 해당 보존 기간을 사용 하 여 온라인 데이터베이스에 대해 수행 하는 것과 동일한 방식으로 백업을 유지 합니다. 삭제 된 데이터베이스에 대 한 백업 보존 기간을 변경할 수 없습니다.

> [!IMPORTANT]
> 서버 또는 관리 되는 인스턴스를 삭제 하는 경우 해당 서버 또는 관리 되는 인스턴스의 모든 데이터베이스도 삭제 되 고 복구할 수 없습니다. 삭제 된 서버 또는 관리 되는 인스턴스는 복원할 수 없습니다. 그러나 데이터베이스 또는 관리 되는 인스턴스에 대해 LTR (장기 보존)를 구성한 경우에는 장기 보존 백업이 삭제 되지 않으며 동일한 구독의 다른 서버 또는 관리 되는 인스턴스의 데이터베이스를 장기 보존 백업이 수행 된 시점으로 복원 하는 데 사용할 수 있습니다.

최근 1-35 일 이내에 PITR의 용도에 대 한 백업 보존은 단기 백업 보존이 라고도 합니다. 최대 단기 보존 기간 35 일 보다 오래 백업을 유지 해야 하는 경우 [장기 보존](long-term-retention-overview.md)을 사용 하도록 설정할 수 있습니다.

### <a name="long-term-retention"></a>장기 보존

SQL Database 및 SQL Managed Instance 모두 Azure Blob storage에서 최대 10 년 동안 전체 백업 LTR (장기 보존)을 구성할 수 있습니다. LTR 정책이 구성 된 후에는 전체 백업이 매주 다른 저장소 컨테이너에 자동으로 복사 됩니다. 다양 한 규정 준수 요구 사항을 충족 하기 위해 매주, 매월 및/또는 매년 전체 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 저장소 소비는 LTR 백업의 선택 된 빈도 및 보존 기간에 따라 달라 집니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 스토리지 비용을 추정할 수 있습니다.

LTR에 대한 자세한 내용은 [장기 백업 보존](long-term-retention-overview.md)을 참조하세요.

## <a name="storage-costs"></a>스토리지 비용

백업 저장소의 가격은 구매 모델 (DTU 또는 vCore), 선택 된 백업 저장소 중복성 옵션 및 해당 지역에 따라 달라 집니다. 백업 저장소는 g b/월 단위로 청구 됩니다. 가격은 가격 책정 페이지 및 [AZURE SQL Managed Instance 가격](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) 책정 페이지를 [Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) 참조 하세요.

### <a name="dtu-model"></a>DTU 모델

DTU 모델에서는 데이터베이스 및 탄력적 풀에 대 한 백업 저장소에 대 한 추가 요금이 부과 되지 않습니다. 백업 저장소의 가격은 데이터베이스 또는 풀 가격의 일부입니다.

### <a name="vcore-model"></a>vCore 모델

SQL Database의 단일 데이터베이스의 경우 데이터베이스에 대 한 최대 데이터 저장소 크기의 100%와 같은 백업 저장소 양은 추가 비용 없이 제공 됩니다. 탄력적 풀 및 관리 되는 인스턴스의 백업 저장소 크기는 풀에 대 한 최대 데이터 저장소의 100% 또는 최대 인스턴스 저장소 크기와 동일 하 게 추가 비용 없이 제공 됩니다. 

단일 데이터베이스의 경우이 수식은 총 청구 가능한 백업 저장소 사용량을 계산 하는 데 사용 됩니다.

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

풀링된 데이터베이스의 경우 총 청구 가능 백업 저장소 크기는 풀 수준에서 집계 되며 다음과 같이 계산 됩니다.

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

관리 되는 인스턴스의 경우 총 청구 가능 백업 저장소 크기는 인스턴스 수준에서 집계 되며 다음과 같이 계산 됩니다.

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

청구 가능한 총 백업 저장소 (있는 경우)는 g b/월 단위로 요금이 청구 됩니다. 이 백업 저장소 사용량은 개별 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스의 워크 로드 및 크기에 따라 달라 집니다. 자주 수정 되는 데이터베이스의 경우에는 이러한 백업의 크기가 데이터 변경의 양에 비례 하므로 차등 및 로그 백업이 크게 증가 합니다. 따라서 이러한 데이터베이스에는 백업 비용이 더 많이 듭니다.

SQL Database 및 SQL Managed Instance은 모든 백업 파일에서 총 청구 가능 백업 저장소를 누적 값으로 계산 합니다. 1 시간 마다이 값이 시간당 사용량을 집계 하 여 각 월의 끝에 백업 저장소 소비를 가져오는 Azure 청구 파이프라인에 보고 됩니다. 데이터베이스가 삭제 된 경우 오래 된 백업 기간이 초과 되 면 백업 저장소 사용량이 점차 감소 하 고 삭제 됩니다. 차등 백업 및 로그 백업에서 복원 가능한 하기 위해 이전 전체 백업을 수행 해야 하기 때문에 세 가지 백업 유형이 모두 주별 집합에서 함께 제거 됩니다. 모든 백업이 삭제 되 면 청구는 중지 됩니다. 

간단한 예로, 데이터베이스가 744 GB의 백업 저장소를 누적 했으며 데이터베이스가 완전히 유휴 상태 이기 때문에이 금액이 전체 월에 걸쳐 일정 하 게 유지 된다고 가정 합니다. 이 누적 스토리지 사용량을 시간당 사용량으로 변환하려면 744(매월 31일 X 매일 24시간)로 나눕니다. SQL Database는 데이터베이스가 일정 한 속도로 1 GB의 PITR 백업을 사용 하는 것을 Azure 청구 파이프라인에 보고 합니다. Azure 청구는 이 사용량을 집계하고 전체 월에 대해 744GB의 사용량을 표시합니다. 이 비용은 해당 지역의 금액/g b/월 요금을 기준으로 합니다.

이제 좀 더 복잡한 예를 들겠습니다. 동일한 유휴 데이터베이스의 보존이 12 일에서 14 일 사이에 연장 되어 있다고 가정 합니다. 이렇게 하면 전체 백업 저장소의 크기가 1488 GB로 배가 됩니다. SQL Database는 시간 1에서 372 (해당 월의 첫 번째 절반)에 대해 1gb의 사용량을 보고 합니다. 373 ~ 744 (해당 월의 두 번째 절반)의 시간에 대 한 2gb로 사용량을 보고 합니다. 이 사용량은 최종 청구 1,116GB/월로 집계됩니다.

실제 백업 청구 시나리오는 더 복잡 합니다. 데이터베이스의 변경 률은 작업에 따라 달라 지 며 시간이 지남에 따라 달라 지므로 각 차등 및 로그 백업의 크기는 달라질 뿐만 아니라 매시간 백업 저장소 사용이 그에 따라 변동 됩니다. 또한 각 차등 백업에는 마지막 전체 백업 이후 데이터베이스에 수행 된 모든 변경 내용이 포함 되어 있으므로 모든 차등 백업의 전체 크기는 한 주 동안 점진적으로 늘어나고 이전에는 전체, 차등 및 로그 백업의 이전 집합이 삭제 되 면 완전히 삭제 됩니다. 예를 들어 전체 백업이 완료 된 후에도 인덱스 다시 작성 등의 많은 쓰기 작업이 실행 된 경우 인덱스 다시 작성을 통해 수행 된 수정 내용은 다시 작성 하는 동안 수행 되는 트랜잭션 로그 백업, 다음 차등 백업 및 다음 전체 백업이 수행 될 때까지 발생 하는 모든 차등 백업에 포함 됩니다. 큰 데이터베이스의 두 번째 시나리오에서는 차등 백업이 지나치게 커지는 경우 서비스에서 최적화를 통해 차등 백업 대신 전체 백업을 만듭니다. 이렇게 하면 다음 전체 백업이 수행 될 때까지 모든 차등 백업의 크기가 줄어듭니다.

[소비 모니터링](#monitor-consumption)에 설명 된 시간에 따라 각 백업 유형 (전체, 차등, 트랜잭션 로그)에 대 한 총 백업 저장소 사용량을 모니터링할 수 있습니다.

### <a name="backup-storage-redundancy"></a>백업 저장소 중복성

백업 저장소 중복성은 다음과 같은 방식으로 백업 비용에 영향을 줍니다.
- LRS price = x
- ZRS price = 1.25 x
- RA-GRS price = 2x

백업 저장소 가격 책정에 대 한 자세한 내용은 가격 책정 페이지 및 [AZURE SQL Managed Instance 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)를 [Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) 하세요.

> [!IMPORTANT]
> 백업에 대 한 구성 가능한 저장소 중복성은 현재 SQL Managed Instance 에서만 사용할 수 있으며 관리 되는 인스턴스 만들기 프로세스 중에만 지정할 수 있습니다. 리소스가 프로 비전 되 면 백업 저장소 중복성 옵션을 변경할 수 없습니다.

### <a name="monitor-costs"></a>비용 모니터링

백업 스토리지 비용을 이해하려면 Azure Portal의 **비용 관리 + 청구**로 이동하여 **비용 관리**를 선택한 다음, **비용 분석**을 선택합니다. 원하는 구독을 **범위**로 선택하고 관심 있는 기간 및 서비스를 필터링합니다.

**서비스 이름**에 대한 필터를 추가한 다음, 드롭다운 목록에서 **SQL 데이터베이스**를 선택합니다. **미터 하위 범주** 필터를 사용하여 서비스에 대한 청구 카운터를 선택합니다. 단일 데이터베이스 또는 Elastic Database 풀의 경우 **단일/탄력적 풀 PITR 백업 스토리지**를 선택합니다. 관리되는 인스턴스의 경우 **MI PITR 백업 스토리지**를 선택합니다. **스토리지** 및 **계산** 하위 범주에도 관심이 갈지 모르지만, 이 둘은 백업 스토리지 비용과는 관련이 없습니다.

![백업 스토리지 비용 분석](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 미터는 현재 사용 중인 카운터에 대해서만 표시 됩니다. 카운터를 사용할 수 없는 경우 범주를 현재 사용 하 고 있지 않을 수 있습니다. 예를 들어 관리 되는 인스턴스가 배포 되지 않은 고객에 게는 관리 되는 인스턴스 카운터가 표시 되지 않습니다. 마찬가지로 저장소를 사용 하지 않는 리소스에 대해서는 저장소 카운터가 표시 되지 않습니다. 

## <a name="encrypted-backups"></a>암호화된 백업

데이터베이스가 TDE를 사용하여 암호화된 경우 LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL의 모든 새 데이터베이스는 기본적으로 TDE를 사용 하도록 설정 된 상태로 구성 됩니다. TDE에 대 한 자세한 내용은  [투명한 데이터 암호화 SQL Database & SQL Managed Instance](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조 하세요.

## <a name="backup-integrity"></a>백업 무결성

Azure SQL 엔지니어링 팀은 지속적으로 자동화 된 데이터베이스 백업의 복원을 자동으로 테스트 합니다. 이 테스트는 현재 SQL Managed Instance에서 사용할 수 없습니다. 지정 시간 복원 시 데이터베이스에서 DBCC CHECKDB 무결성 확인도 수신 합니다.

무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. 자세한 내용은 [SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조 하세요.

모든 데이터베이스 백업은 추가 백업 무결성을 제공 하는 CHECKSUM 옵션과 함께 수행 됩니다.

## <a name="compliance"></a>규정 준수

DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 규정 준수 요구 사항을 충족 하지 않는 경우 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [PITR 백업 보존 기간 변경](#change-the-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간 변경

Azure Portal, PowerShell 또는 REST API를 사용하여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간 보다 오래 된 시점으로 복원 하는 기능이 손실 됩니다. 새 보존 기간 내에 PITR를 제공 하는 데 더 이상 필요 하지 않은 백업이 삭제 됩니다. 현재 보존 기간을 늘리면 새 보존 기간 내에 이전 시점으로 복원 하는 기능이 즉시 제공 되지 않습니다. 시간이 지남에 따라 시스템이 백업을 보존 하기 시작 하므로이 기능을 사용할 수 있습니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스의 LTR을 구성한 경우에는 LTR이 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 내용은 [장기 보존](long-term-retention-overview.md)을 참조하세요.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Azure Portal을 사용하여 PITR 백업 보존 기간 변경

Azure Portal를 사용 하 여 활성 데이터베이스에 대 한 PITR 백업 보존 기간을 변경 하려면 변경 하려는 보존 기간이 포함 된 데이터베이스가 있는 서버 또는 관리 되는 인스턴스로 이동 합니다. 

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

SQL Database에 대 한 PITR 백업 보존에 대 한 변경 내용은 포털의 서버 페이지에서 수행 됩니다. 서버의 데이터베이스에 대 한 PITR 보존을 변경 하려면 서버 개요 블레이드로 이동 합니다. 왼쪽 창에서 **백업 관리** 를 선택 하 고, 변경 범위에서 데이터베이스를 선택한 다음, 화면 위쪽에서 **보존 구성** 을 선택 합니다.

![PITR 보존 변경, 서버 수준](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

SQL Managed Instance에 대 한 PITR 백업 보존에 대 한 변경 내용은 개별 데이터베이스 수준에서 수행 됩니다. Azure Portal에서 인스턴스 데이터베이스의 PITR 백업 보존 기간을 변경하려면 개별 데이터베이스 개요 블레이드로 이동합니다. 그런 다음, 화면 맨 위에 있는 **백업 보존 구성**을 선택합니다.

![PITR 보존 변경, 관리되는 인스턴스](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM 모듈은 SQL Database 및 SQL Managed Instance에서 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. 자세한 내용은 [AzureRM.SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈의 명령에 대한 인수는 AzureRm 모듈의 인수와 실질적으로 동일합니다.

#### <a name="sql-database"></a>[SQL 데이터베이스](#tab/single-database)

활성 Azure SQL Database에 대 한 PITR 백업 보존을 변경 하려면 다음 PowerShell 예제를 사용 합니다.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

**개별 활성** SQL Managed Instance 데이터베이스에 대 한 PITR 백업 보존을 변경 하려면 다음 PowerShell 예제를 사용 합니다.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

**모든 활성** SQL Managed Instance 데이터베이스에 대 한 PITR 백업 보존을 변경 하려면 다음 PowerShell 예제를 사용 합니다.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

**삭제 된 개별** SQL Managed Instance 데이터베이스에 대 한 PITR 백업 보존을 변경 하려면 다음 PowerShell 예제를 사용 합니다.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

**삭제 된 모든** SQL Managed Instance 데이터베이스에 대 한 PITR 백업 보존을 변경 하려면 다음 PowerShell 예제를 사용 합니다.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

0 일 보존은 백업이 즉시 삭제 되 고 삭제 된 데이터베이스에 대해 더 이상 유지 되지 않음을 나타냅니다.
삭제 된 데이터베이스에 대 한 PITR 백업 보존이 줄어들면 더 이상 증가 하지 않습니다.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>REST API를 사용하여 PITR 백업 보존 기간 변경

#### <a name="sample-request"></a>샘플 요청

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>샘플 응답

상태 코드: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

자세한 내용은 [백업 보존 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)를 참조하세요.

#### <a name="sample-request"></a>샘플 요청

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>요청 본문

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>샘플 응답

상태 코드: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

자세한 내용은 [백업 보존 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)를 참조하세요.

## <a name="configure-backup-storage-redundancy"></a>백업 저장소 중복성 구성

> [!NOTE]
> 백업에 대 한 구성 가능한 저장소 중복성은 현재 SQL Managed Instance 에서만 사용할 수 있으며 관리 되는 인스턴스 만들기 프로세스 중에만 지정할 수 있습니다. 리소스가 프로 비전 되 면 백업 저장소 중복성 옵션을 변경할 수 없습니다.

관리 되는 인스턴스의 백업 저장소 중복성은 인스턴스를 만드는 동안에만 설정할 수 있습니다. 기본값은 지역 중복 저장소 (RA-GRS)입니다. LRS (로컬 중복), ZRS (영역 중복) 및 지역 중복 (RA-GRS) 백업 저장소 간의 가격 차이에 대 한 자세한 내용은 [관리 되는 인스턴스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)를 참조 하세요.

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 백업 저장소 중복성 구성

Azure Portal에서 백업 저장소 중복성을 변경 하는 옵션은 SQL Managed Instance를 만들 때 **기본** 탭의 **구성 Managed Instance** 옵션에서 액세스할 수 있는 **Compute + storage** 블레이드에 있습니다.
![Compute + 저장소 구성-블레이드 열기](./media/automated-backups-overview/open-configuration-blade-mi.png)

**계산 + 저장소** 블레이드에서 백업 저장소 중복성을 선택 하는 옵션을 찾습니다.
![백업 저장소 중복성 구성](./media/automated-backups-overview/select-backup-storage-redundancy-mi.png)

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조 하세요.
- [Azure Portal을 사용하여 지정 시간으로 데이터베이스를 복원](recovery-using-backups.md)하는 방법에 대해 자세히 알아봅니다.
- [PowerShell을 사용하여 지정 시간으로 데이터베이스를 복원](scripts/restore-database-powershell.md)하는 방법에 대해 자세히 알아봅니다.
- Azure Portal을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [Azure Portal을 사용하여 장기 백업 보존 관리](long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Blob Storage에서 자동화된 백업의 장기 보존에 따라 구성, 관리 및 복원하는 방법에 대한 내용은 [PowerShell을 사용하여 장기 백업 보존 관리](long-term-backup-retention-configure.md)를 참조하세요.
- Azure SQL Managed Instance에 대 한 백업 저장소 보존 및 비용을 미세 조정 하는 방법을 알아보려면 [Managed Instance에서 백업 저장소 비용 미세 조정](https://aka.ms/mi-backup-tuning)을 참조 하세요.
