---
title: 자동 지역 중복 백업
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 스토리지를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: f460bc3e4809b8a1cbabe1161c888255a7a484db
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157519"
---
# <a name="automated-backups"></a>자동화된 백업

SQL Database는 구성 된 보존 기간 동안 유지 되는 데이터베이스 백업을 자동으로 만들고, Azure [읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-redundancy.md) 를 사용 하 여 데이터 센터를 사용할 수 없는 경우에도 유지 되도록 합니다. 이러한 백업은 자동으로 생성 됩니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 보안 규칙에 따라 오랫동안 백업을 사용할 수 있어야 하는 경우 (최대 10 년) 단일 데이터베이스 및 탄력적 풀에 대 한 [장기 보존](sql-database-long-term-retention.md) 을 구성할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용 하 여 매주 [전체 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) , 12 시간 마다 [차등 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 및 5-10 분 마다 [트랜잭션 로그 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 만듭니다. 백업은 데이터 센터 가동 중단 으로부터 보호 하기 위해 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md) 에 복제 되는 [RA GRS storage blob](../storage/common/storage-redundancy.md) 에 저장 됩니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

- Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 기존 데이터베이스를 보존 기간 내의 **특정 시점으로 복원** 합니다. 단일 데이터베이스 및 탄력적 풀에서이 작업은 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만듭니다. Managed Instance이 작업을 수행 하면 데이터베이스의 복사본 또는 동일한 구독에서 동일 하거나 다른 Managed Instance을 만들 수 있습니다.
- 삭제 **된 데이터베이스를 삭제 된 시간** 또는 보존 기간 내에 언제 든 지 복원 합니다. 삭제 된 데이터베이스는 원래 데이터베이스가 만들어진 동일한 논리 서버 또는 Managed Instance 에서만 복원할 수 있습니다.
- **데이터베이스를 다른 지역으로 복원**합니다. 이러한 지리적 복원을 사용하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다.
- Single Database 또는 Elastic Pool에 대 한 **특정 장기 백업에서 데이터베이스를 복원** 합니다. 단, 데이터베이스가 LTR (장기 보존 정책)로 구성 된 경우에는를 사용 합니다. LTR을 사용 하면 [Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) 또는 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) 를 사용 하 여 이전 버전의 데이터베이스를 복원 하 여 규정 준수 요청을 충족 하거나 이전 버전의 응용 프로그램을 실행할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
- 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 ‘데이터베이스 복제’는주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

다음 예제를 사용 하 여 이러한 작업 중 일부를 시도할 수 있습니다.

| | Azure 포털 | Azure PowerShell |
|---|---|---|
| 백업 보존 변경 | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 장기 백업 보존 기간 변경 | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance-해당 없음  | [Single Database](sql-database-long-term-backup-retention-configure.md)<br/>Managed Instance-해당 없음  |
| 특정 시점에서 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md#point-in-time-restore) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 삭제된 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage에서 데이터베이스 복원 | 단일 데이터베이스-해당 없음 <br/>Managed Instance-해당 없음  | 단일 데이터베이스-해당 없음 <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Backup 주기

### <a name="point-in-time-restore"></a>지정 시간 복원

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 만들어지고, 차등 데이터베이스 백업은 일반적으로 12시간마다 만들어지고, 트랜잭션 로그 백업은 일반적으로 5~10분마다 만들어지며, 이러한 빈도는 컴퓨팅 크기와 데이터베이스 작업량에 따라 달라집니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 백업 작업은 변경 하거나 사용 하지 않도록 설정할 수 없습니다.

PITR 백업은 지역 중복 저장소를 사용 하 여 보호 됩니다. 자세한 내용은 [Azure Storage 중복성](../storage/common/storage-redundancy.md)(영문)을 참조 하세요.

자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="long-term-retention"></a>장기 보존

단일 및 풀링된 데이터베이스는 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 스토리지 컨테이너로 자동으로 복사됩니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 스토리지 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 스토리지 비용을 추정할 수 있습니다.

PITR와 마찬가지로 LTR 백업은 지역 중복 저장소를 사용 하 여 보호 됩니다. 자세한 내용은 [Azure Storage 중복성](../storage/common/storage-redundancy.md)(영문)을 참조 하세요.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="backup-storage-consumption"></a>백업 저장소 사용량 

단일 데이터베이스의 경우 총 백업 저장소 사용량은 다음과 같이 계산 됩니다.   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`입니다.

탄력적 풀의 경우 총 백업 저장소 크기는 풀 수준에서 집계 되며 다음과 같이 계산 됩니다.   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`입니다. 

보존 기간 보다 오래 된 백업은 타임 스탬프에 따라 자동으로 제거 됩니다. 차등 백업 및 로그 백업에는 유용 하 게 사용할 수 있는 이전 전체 백업이 필요 하기 때문에 주 청크에서 함께 제거 됩니다. 

Azure SQL Database는 전체 보존 기간 백업 저장소를 누적 값으로 계산 합니다. 1 시간 마다이 값이 시간당 사용량을 집계 하 여 각 월의 끝에 소비를 계산 하는 것을 담당 하는 Azure 청구 파이프라인에 보고 됩니다. 데이터베이스를 삭제 한 후 소비는 백업 기간으로 줄어듭니다. 백업이 보존 기간 보다 오래 되 면 청구는 중지 됩니다. 


### <a name="monitoring-consumption"></a>사용량 모니터링

각 백업 유형 (전체, 차등 및 로그)은 데이터베이스 모니터링 블레이드에서 별도의 메트릭으로 보고 됩니다. 다음 다이어그램에서는 백업 저장소 사용량을 모니터링 하는 방법을 보여 줍니다.  

![Azure Portal의 데이터베이스 모니터링 블레이드에서 데이터베이스 백업 사용량 모니터링](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>백업 저장소 사용량 미세 조정

과도 한 백업 저장소 소비는 개별 데이터베이스의 워크 로드 및 크기에 따라 달라 집니다. 백업 저장소 사용을 더욱 줄이기 위해 다음과 같은 튜닝 기술 중 일부를 구현 하는 것을 고려할 수 있습니다.

* [백업 보존 기간은](#change-pitr-backup-retention-period-using-azure-portal) 사용자의 요구에 따라 최소로 줄이십시오.
* 인덱스 재작성과 같이 필요한 것 보다 더 자주 많은 쓰기 작업을 수행 하지 마십시오.
* 대량 데이터 로드 작업의 경우 클러스터형 [columnstore 인덱스](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)를 사용 하 고, 비클러스터형 인덱스의 수를 줄이고, 행 수가 100만를 기준으로 대량 로드 작업을 수행 하는 것이 좋습니다.
* 일반적으로 사용 되는 서비스 계층에서 프로 비전 된 데이터 저장소는 과도 한 백업 저장소 비용이 지속적으로 증가 하는 고객이 데이터 저장소의 증가를 고려 하 여 백업 저장소.
* 영구 테이블 대신 임시 결과를 저장 하기 위해 ETL 논리에서 TempDB를 사용 합니다 (관리 되는 인스턴스에만 적용 가능).
* 중요 한 데이터 (예를 들어 개발 또는 테스트 데이터베이스)를 포함 하지 않는 데이터베이스에 대해서는 TDE 암호화 기능을 해제 하는 것이 좋습니다. 암호화 되지 않은 데이터베이스에 대 한 백업은 일반적으로 더 높은 압축 비율로 압축 됩니다.

> [!IMPORTANT]
> 분석, 데이터 마트 \ 데이터 웨어하우스 워크 로드의 경우 [클러스터형 columnstore 인덱스](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)를 사용 하 고, 비클러스터형 인덱스 수를 줄이고, 초과 된 백업 저장소 사용량을 줄이기 위해 행 수가 100만 인 대량 로드 작업을 수행 하는 것이 좋습니다.


## <a name="storage-costs"></a>스토리지 비용


### <a name="dtu-model"></a>DTU 모델

DTU 모델을 사용 하는 데이터베이스 및 탄력적 풀의 백업 저장소에 대해서는 추가 요금이 부과 되지 않습니다. 

### <a name="vcore-model"></a>vCore 모델

단일 데이터베이스의 경우 데이터베이스 크기의 100%와 같은 최소 백업 저장소 양은 추가 비용 없이 제공 됩니다. 탄력적 풀 및 관리 되는 인스턴스의 경우 각각 풀 또는 인스턴스 크기에 할당 된 데이터 저장소의 100%와 같은 최소 백업 저장소 양은 추가 비용 없이 제공 됩니다. 추가로 사용되는 백업 스토리지의 경우 GB/월 단위로 요금이 청구됩니다. 이러한 추가 사용은 개별 데이터베이스의 워크 로드 및 크기에 따라 달라 집니다.

Azure SQL DB는 총 보존 기간 백업 저장소를 누적 값으로 계산 합니다. 1 시간 마다이 값이 시간당 사용량을 집계 하 여 각 월의 끝에 소비를 확보 하는 Azure 청구 파이프라인에 보고 됩니다. 데이터베이스를 삭제 한 후에는 백업 보존 기간으로 소비를 줄일 수 있습니다. 보존 기간 보다 오래 되 면 청구는 중지 됩니다. 모든 로그 백업과 차등 백업은 전체 보존 기간 동안 유지 되므로 크게 수정 된 데이터베이스는 백업 비용이 더 높습니다. 

데이터베이스가 744 GB의 백업 저장소를 누적 했으며,이 금액은 한 달 전체에 걸쳐 일정 하 게 유지 된다고 가정 하겠습니다. 이 누적 저장소 사용량을 시간별로 변환 하기 위해 744.0 (매월 31 일 * 하루 24 시간)으로 나눕니다. 따라서 SQL DB는 매시간 1gb의 PITR 백업을 사용 하는 데이터베이스를 보고 합니다. Azure 청구는이를 집계 하 고 전체 월에 대해 744 GB를 사용 하 고 지역의 $/GB/mo 요금을 기준으로 비용을 표시 합니다. 

이제 더 복잡 한 예제입니다. 데이터베이스의 보존이 월 중간에 14 일로 증가 하 고이 (가상으로)로 인해 총 백업 저장소가 1488 GB로 증가 하는 경우를 가정 합니다. SQL DB는 시간 1-372에 대 한 1gb의 사용량을 보고 하 고 시간 373-744에 대해 2gb로 사용량을 보고 합니다. 이는 1116 g b/mo 최종 청구로 집계 됩니다. 

Azure 구독 비용 분석을 사용 하 여 백업 저장소에 대 한 현재 비용을 확인할 수 있습니다.

![백업 저장소 비용 분석](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

예를 들어 관리 되는 인스턴스의 백업 저장소 비용을 이해 하려면 Azure Portal 구독으로 이동 하 여 비용 분석 블레이드를 엽니다. 측정기 하위 범주 **mi pitr backup 저장소** 를 선택 하 여 현재 백업 비용 및 요금 예측을 표시 합니다. 또한 **관리 되는 인스턴스 범용 저장소** 또는 **관리 되는 인스턴스 범용-compute gen5** 와 같은 다른 미터 하위 범주를 포함 하 여 백업 저장소 비용을 다른 비용 범주와 비교할 수 있습니다.

## <a name="backup-retention"></a>Backup 보존

모든 Azure SQL 데이터베이스 (단일, 풀링된 및 관리 되는 인스턴스 데이터베이스)의 기본 백업 보존 기간은 **7** 일입니다. [백업 보존 기간은 최대 35 일까지 변경할](#change-pitr-backup-retention-period)수 있습니다.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어, 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우, 4일 된 백업은 앞으로 3일 동안 더 저장됩니다.

최대 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 탄력적 풀과 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우, LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

## <a name="encrypted-backups"></a>암호화된 백업

데이터베이스가 TDE를 사용하여 암호화된 경우, LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL 데이터베이스에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL 데이터베이스는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="backup-integrity"></a>백업 무결성

Azure SQL Database 엔지니어링 팀은 지속적으로 논리 서버 및 탄력적 풀에 배치 된 데이터베이스의 자동화 된 데이터베이스 백업 복원을 자동으로 테스트 합니다 .이는 Managed Instance에서 사용할 수 없습니다. 지정 시간 복원 시 데이터베이스는 DBCC CHECKDB를 사용 하 여 무결성 검사를 수신 합니다.

마이그레이션이 완료 되 면 기본 `RESTORE` 명령 또는 데이터 마이그레이션 서비스를 사용 하 여 복원 된 데이터베이스의 `CHECKSUM`를 사용 하 여 자동 초기 백업을 수행 Managed Instance 합니다.

무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. Azure SQL Database의 데이터 무결성에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="compliance"></a>규정 준수

DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우 응용 프로그램의 데이터 복구 정책이 손상 되지 않도록 PITR 보존이 보존 됩니다. 기본 보존이 준수 요구 사항을 충족하지 못하는 경우, PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#change-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>PITR 백업 보존 기간 변경

Azure Portal, PowerShell 또는 REST API를 사용 하 여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간 보다 오래 된 기존 백업은 더 이상 사용할 수 없습니다. 현재 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Azure Portal를 사용 하 여 PITR 백업 보존 기간 변경

Azure Portal를 사용 하 여 PITR 백업 보존 기간을 변경 하려면 포털 내에서 보존 기간을 변경 하려는 서버 개체로 이동한 후 수정 하려는 서버 개체에 따라 적절 한 옵션을 선택 합니다.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[단일 데이터베이스 및 탄력적 풀](#tab/single-database)

단일 Azure SQL Database에 대 한 PITR 백업 보존 변경은 서버 수준에서 수행 됩니다. 서버 수준에서 변경한 내용은 해당 서버의 데이터베이스에 적용 됩니다. Azure Portal에서 Azure SQL Database server에 대 한 PITR를 변경 하려면 서버 개요 블레이드로 이동 하 여 탐색 메뉴에서 백업 관리를 클릭 한 다음 탐색 모음에서 보존 구성을 클릭 합니다.

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

SQL Database 관리 되는 인스턴스에 대 한 PITR 백업 보존 변경은 개별 데이터베이스 수준에서 수행 됩니다. Azure Portal에서 인스턴스 데이터베이스의 PITR 백업 보존 기간을 변경 하려면 개별 데이터베이스 개요 블레이드로 이동한 다음 탐색 모음에서 백업 보존 구성을 클릭 합니다.

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API를 사용하여 PITR 보존 기간 변경

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

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure Portal을 사용하여 지정 시간으로 복원하려면 [Azure Portal을 사용하여 지정 시간으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복원하려면 [PowerShell을 사용하여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
- Azure Portal을 사용하여 Azure Blob Storage에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용 하 여 Azure Blob storage에서 자동화 된 백업의 장기 보존을 구성, 관리 및 복원 하려면 PowerShell을 [사용 하 여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조 하세요.
