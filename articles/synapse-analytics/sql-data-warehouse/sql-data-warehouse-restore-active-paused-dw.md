---
title: 기존 전용 SQL 풀(이전의 SQL DW) 복원
description: Azure Synapse Analytics에서 기존 전용 SQL 풀을 복원하는 방법에 대한 가이드입니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f433d09079769cca99fd78183b9acbcd23c2adf0
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163574"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>기존 전용 SQL 풀(이전의 SQL DW) 복원

이 문서에서는 Azure Portal 및 PowerShell을 사용하여 기존 전용 SQL 풀(이전의 SQL DW)을 복원하는 방법에 대해 알아봅니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 풀은 기본 DTU 할당량이 있는 [논리 SQL Server](../../azure-sql/database/logical-servers.md)(예: myserver.database.windows.net)에서 호스트합니다. 서버에 데이터베이스를 복원하기에 충분한 DTU 할당량이 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. 복원하려는 기존 복원 지점이 있어야 합니다. 새 복원을 만들려면 [새 사용자 정의 복원 지점을 만드는 자습서](sql-data-warehouse-restore-points.md)를 참조합니다.

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>PowerShell을 통해 기존 전용 SQL 풀(이전의 SQL DW) 복원

복원 지점에서 기존 전용 SQL 풀(이전의 SQL DW)을 복원하려면 [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용합니다.

1. PowerShell을 엽니다.

2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.

3. 복원할 데이터베이스가 포함된 구독을 선택합니다.

4. 전용 SQL 풀(이전의 SQL DW)의 복원 지점을 나열합니다.

5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.

6. [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용하여 전용 SQL 풀(이전의 SQL DW)을 원하는 복원 지점으로 복원합니다.

    1. 전용 SQL 풀(이전의 SQL DW)을 다른 서버로 복원하려면 다른 서버 이름을 지정해야 합니다.  이 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
    2. 다른 구독으로 복원하려면 ‘이동’ 단추를 사용하여 서버를 다른 구독으로 이동합니다.

7. 복원된 전용 SQL 풀(이전의 SQL DW)이 온라인 상태인지 확인합니다.

8. 복원이 완료되면 [복구 후 데이터베이스 구성](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)에 따라 복구된 전용 SQL 풀(이전의 SQL DW)을 구성할 수 있습니다.

```powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Azure Portal을 통해 기존 전용 SQL 풀(이전의 SQL DW) 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 복원하려는 전용 SQL 풀로 이동합니다.
3. [개요] 블레이드의 위쪽에서 **복원** 을 선택합니다.

    ![ 복원 개요](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. 전용 SQL 풀(이전의 SQL DW)에 자동 복원 지점이 없는 경우 복원하기 전에 몇 시간 동안 기다리거나 사용자 정의 복원 지점을 만듭니다. 사용자 정의 복원 지점의 경우 기존 복원 지점을 선택하거나 새로 만듭니다. **서버** 의 경우 다른 리소스 그룹 및 지역에서 서버를 선택하거나 새 서버를 만들 수 있습니다. 모든 매개 변수를 제공한 후 **검토 + 복원** 을 클릭합니다.

    ![자동 복원 지점](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>다음 단계

- [삭제된 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-deleted-dw.md)
- [지역 백업 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-from-geo-backup.md)
