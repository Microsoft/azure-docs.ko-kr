---
title: Azure SQL Database 자동, 지역 중복 백업 | Microsoft Docs
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: 3e56244f074e31672cf77bc74998096e215a4db7
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357342"
---
# <a name="automated-backups"></a>자동화된 백업

SQL Database 자동으로 만들고 7-35 일 사이 유지 되는 데이터베이스 백업을 사용 하 여 Azure [읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 데이터 센터를 사용할 수 없는 경우에 유지 됩니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 사용자는 아무 작업도 할 필요가 없습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 백업을 오랜 시간 (최대 10 년)에 대해 사용할 수 있어야 하는 보안 규칙을 구성할 수 있습니다는 [장기 보존](sql-database-long-term-retention.md) 단일 데이터베이스 및 탄력적 풀 합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database SQL Server 기술을 사용 하 여 만듭니다 [전체 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) 매주 [차등 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 12 시간 마다 하 고 [트랜잭션 로그 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 5 ~ 10 분 마다입니다. 백업에 저장 됩니다 [RA-GRS 저장소 blob](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 에 복제를 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md) 데이터 센터 가동 중단 으로부터 보호 하기 위한 합니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.

이러한 백업을 사용하여 다음을 수행할 수 있습니다.

- **과거에는-특정 시점에 기존 데이터베이스를 복원할** Azure 포털, Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 보존 기간입니다. 단일 데이터베이스 및 탄력적 풀에서이 작업은 새 데이터베이스는 원본 데이터베이스와 동일한 서버에 만들어집니다. 관리 되는 인스턴스에서이 작업 데이터베이스 또는 동일 하거나 다른 관리 되는 인스턴스는 동일한 구독에서의 복사본을 만들 수 있습니다.
  - **[백업 보존 기간을 변경](#how-to-change-the-pitr-backup-retention-period)**  백업 정책을 구성 하려면 7-35 일 사이입니다.
  - **10 년까지 장기 보존 정책을 변경** 단일 데이터베이스 및 탄력적 풀 사용 [Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) 하거나 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)합니다.
- **삭제 된 시간에 삭제 된 데이터베이스를 복원** 또는 보존 기간 내에 언제 든 지 합니다. 삭제 된 데이터베이스를 원래 데이터베이스 만들어진 관리 되는 인스턴스를 동일한 논리 서버에만 복원할 수 있습니다.
- **다른 지역에 데이터베이스를 복원**합니다. 이러한 지리적 복원을 사용하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다.
- **특정 장기 백업에서 데이터베이스를 복원** 단일 데이터베이스 또는 탄력적 풀 데이터베이스 장기 보존 정책 (LTR)을 사용 하 여 구성 된 경우에 합니다. LTR을 사용 하면 이전 버전을 사용 하 여 데이터베이스를 복원할 수 있습니다 [Azure portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) 하거나 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) 준수 요청을 충족 하거나 응용 프로그램의 이전 버전을 실행 합니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
- 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 ‘데이터베이스 복제’는  주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다.

다음 예제를 사용 하 여 이러한 작업 중 일부를 시도할 수 있습니다.

| | Azure 포털 | Azure PowerShell |
|---|---|---|
| 백업 보존 변경 | [단일 데이터베이스](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [단일 데이터베이스](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 장기 백업 보존 변경 | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>관리 되는 인스턴스-해당 없음  | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>관리 되는 인스턴스-해당 없음  |
| 특정 시점에서에서 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md#point-in-time-restore) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 삭제된 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage에서 데이터베이스 복원 | 단일 데이터베이스-해당 없음 <br/>관리 되는 인스턴스-해당 없음  | 단일 데이터베이스-해당 없음 <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>백업은 얼마 동안 유지되나요?

각 SQL Database에는 구매 모델 및 서비스 계층에 따라 달라지는 7-35일의 기본 백업 보존 기간이 있습니다. SQL Database 서버의 데이터베이스에 대 한 백업 보존 기간을 업데이트할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조하세요.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어, 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우, 4일 된 백업은 앞으로 3일 동안 더 저장됩니다.

최대 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 탄력적 풀과 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우, LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

### <a name="default-backup-retention-period"></a>기본 백업 보존 기간

#### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

DTU 기반 구매 모델을 사용하여 만든 데이터베이스의 기본 보존 기간은 서비스 계층에 따라 달라집니다.

- 기본 서비스 계층은 **하나의** 주입니다.
- 표준 서비스 계층은 **5** 주입니다.
- 프리미엄 서비스 계층은 **5** 주입니다.

#### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

사용 중인 경우는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md), 기본 백업 보존 기간은 **7** 일 (풀링된 단일에 대 한 데이터베이스 인스턴스). 모든 Azure SQL 데이터베이스(단일, 풀링된 및 인스턴스 데이터베이스)에 대해 [백업 보존 기간을 최대 35일로 변경](#how-to-change-the-pitr-backup-retention-period)할 수 있습니다.

> [!WARNING]
> 현재 보존 기간을 줄이기 위해 새 보존 기간 보다 오래 된 모든 기존 백업을 사용할 수 없게 됩니다. 현재 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다.

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?

### <a name="backups-for-point-in-time-restore"></a>지정 시간 복원에 대한 백업

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 만들어지고, 차등 데이터베이스 백업은 일반적으로 12시간마다 만들어지고, 트랜잭션 로그 백업은 일반적으로 5~10분마다 만들어지며, 이러한 빈도는 컴퓨팅 크기와 데이터베이스 작업량에 따라 달라집니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다. 변경 하거나 백업 작업을 사용 하지 않도록 설정할 수 없습니다. 

PITR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="backups-for-long-term-retention"></a>장기 보존에 대한 백업

단일 및 풀링된 데이터베이스는 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 저장소 컨테이너로 자동으로 복사됩니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 저장소 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 저장소 비용을 추정할 수 있습니다.

PITR과 마찬가질 LTR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="storage-costs"></a>저장소 비용
기본적으로 7일 분량의 자동화된 데이터베이스 백업이 RA-GRS Standard Blob Storage에 복사됩니다. 저장소는 주별 전체 백업, 일별 차등 백업 및 5분마다 복사되는 트랜잭션 로그 백업에 사용됩니다. 트랜잭션 로그의 크기는 데이터베이스 변동률에 따라 다릅니다. 데이터베이스 크기의 100%와 같은 최소 스토리지 용량은 추가 요금 없이 제공됩니다. 추가로 사용되는 백업 스토리지의 경우 GB/월 단위로 요금이 청구됩니다.

스토리지 가격에 대한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조하세요. 

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

데이터베이스가 TDE를 사용하여 암호화된 경우, LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL 데이터베이스에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL 데이터베이스는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="how-does-microsoft-ensure-backup-integrity"></a>백업 무결성은 Microsoft에서 어떻게 보장하나요?

Azure SQL Database 엔지니어링 팀은 지속적으로 서비스 전체에서 데이터베이스의 자동화된 데이터베이스 백업에 대한 복원을 자동으로 테스트합니다. 복원 시 데이터베이스도 DBCC CHECKDB를 사용하여 무결성 검사를 받습니다. 무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. Azure SQL Database의 데이터 무결성에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="how-do-automated-backups-impact-compliance"></a>자동화된 백업은 규정 준수에 어떻게 영향을 주나요?

기본 PITR 보존 기간이 35일인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우, 애플리케이션의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 준수 요구 사항을 충족하지 못하는 경우, PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간은 어떻게 변경하나요?

Azure portal, PowerShell 또는 REST API를 사용 하 여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 지원되는 값은 7, 14, 21, 28 또는 35일입니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다.

> [!NOTE]
> 이러한 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Azure Portal을 사용하여 PITR 백업 보존 기간 변경

Azure portal을 사용 하 여 PITR 백업 보존 기간을 변경 하려면 포털 내에서 변경 하 고 적절 한 옵션을 선택 하려면 해당 보존 기간 서버 개체를 이동할 수정 하는 서버 개체에 기반 합니다.

#### <a name="change-pitr-for-a-sql-database-server"></a>SQL Database 서버의 PITR 변경

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Managed Instance의 PITR 변경

![PITR 변경 Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

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
- 자동화 된 PowerShell를 사용 하 여 Azure Blob storage에 백업을 구성, 관리 및 장기 보존에서 복원 하려면 다음을 참조 하십시오 [PowerShell을 사용 하 여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)합니다.
