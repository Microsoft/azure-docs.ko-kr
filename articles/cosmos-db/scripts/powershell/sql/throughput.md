---
title: Azure Cosmos DB SQL API 데이터베이스 또는 컨테이너에 대한 처리량(RU/s) 작업을 위한 PowerShell 스크립트
description: Azure Cosmos DB SQL API 데이터베이스 또는 컨테이너에 대한 처리량(RU/s) 작업을 위한 PowerShell 스크립트
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c6aa4a0ad6f9c7f209d9921db4fa75f004a921af
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110679719"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>데이터베이스용 PowerShell 또는 Azure Cosmos DB Core(SQL) API용 컨테이너를 사용한 처리량(RU/s) 작업
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell Az 5.4.0 이상이 필요합니다. `Get-Module -ListAvailable Az`를 실행하여 설치된 버전을 확인합니다.
설치해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure에 로그인합니다.

## <a name="get-throughput"></a>처리량 가져오기

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>처리량 업데이트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>처리량 마이그레이션

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Azure Cosmos DB Core(SQL) API Database에 프로비저닝된 처리량을 가져옵니다. |
| [Get-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Azure Cosmos DB Core(SQL) API 컨테이너에 프로비저닝된 처리량을 가져옵니다. |
| [Update-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Azure Cosmos DB Core(SQL) API Database의 처리량 값을 업데이트합니다. |
| [Update-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Azure Cosmos DB Core(SQL) API 컨테이너의 처리량 값을 업데이트합니다. |
| [Invoke-AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Azure Cosmos DB Core(SQL) API Database의 처리량을 마이그레이션합니다. |
| [Invoke-AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Azure Cosmos DB Core(SQL) API 컨테이너의 처리량을 마이그레이션합니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.
