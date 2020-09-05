---
title: PowerShell & REST Api
description: 데이터베이스를 일시 중지 하 고 다시 시작 하는 방법을 포함 하 여 Azure Synapse Analytics SQL 풀의 상위 PowerShell cmdlet을 찾습니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: aaf290fac71fec0ff4106e3f8a1895dd65b94e09
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072896"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Azure Synapse Analytics 용 PowerShell & REST Api SQL 풀

Azure PowerShell cmdlet 또는 REST Api를 사용 하 여 많은 Azure Synapse Analytics SQL 풀 관리 작업을 관리할 수 있습니다.  다음은 PowerShell 명령을 사용 하 여 SQL 풀에서 일반적인 작업을 자동화 하는 방법에 대 한 몇 가지 예입니다.  유용한 REST 예제는 [REST를 사용하여 확장성 관리](sql-data-warehouse-manage-compute-rest-api.md) 문서를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet 시작

1. Windows PowerShell을 엽니다.
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>데이터 웨어하우스 일시 중지 예제

"Server01."라는 서버에서 호스트하는 "Database02"라는 데이터베이스를 일시 중지합니다.  서버는 "ResourceGroup1."이라는 Azure 리소스 그룹 내에 있습니다.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

변형으로,이 예제에서는 검색 된 개체를 [AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)로 파이프 합니다.  그 결과로 데이터베이스가 일시 중지됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>데이터 웨어하우스 시작 예제

"Server01."이라는 서버에서 호스트하는 "Database02"라는 데이터베이스의 작동을 다시 시작합니다. 서버는 "ResourceGroup1."이라는 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

변형인 이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 검색 된 개체를 [다시 시작-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)로 파이프 합니다.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.

## <a name="other-supported-powershell-cmdlets"></a>지원되는 기타 PowerShell cmdlet

이러한 PowerShell cmdlet은 Azure Synapse Analytics 데이터 웨어하우스에서 지원 됩니다.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [일시 중단-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>다음 단계

더 많은 PowerShell 예제는 다음을 참조하세요.

* [PowerShell을 사용 하 여 데이터 웨어하우스 만들기](create-data-warehouse-powershell.md)
* [데이터베이스 복원](sql-data-warehouse-restore-points.md)

PowerShell을 사용 하 여 자동화할 수 있는 다른 작업은 [Azure SQL Database cmdlet](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조 하세요. 모든 Azure SQL Database cmdlet이 Azure Synapse Analytics 데이터 웨어하우스에 대해 지원 되는 것은 아닙니다. REST로 자동화할 수 있는 작업 목록은 [Azure SQL Database에 대 한 작업](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조 하세요.
