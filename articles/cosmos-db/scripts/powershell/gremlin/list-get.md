---
title: Azure Cosmos DB Gremlin API에 대한 작업을 나열하고 가져오는 PowerShell 스크립트
description: Azure PowerShell 스크립트 - Gremlin API용 Azure Cosmos DB 작업 나열 및 가져오기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c003c50e39f9223f22645c50cb57024615a3a7af
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110680227"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---gremlin-api"></a>Azure Cosmos DB - Gremlin API에 대한 데이터베이스, 그래프 나열 및 가져오기
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

이 샘플에는 Azure PowerShell Az 5.4.0 이상이 필요합니다. `Get-Module -ListAvailable Az`를 실행하여 설치된 버전을 확인합니다.
설치해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure에 로그인합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-list-get.ps1 "List or get databases or graphs for Gremlin API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Cosmos DB 계정을 나열하거나 지정된 Cosmos DB 계정을 가져옵니다. |
| [Get-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabase) | 계정의 Gremlin API Databases를 나열하거나 계정에 지정된 Gremlin API Databases를 가져옵니다. |
| [Get-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraph) | 데이터베이스의 Gremlin API Graphs를 나열하거나 데이터베이스에 지정된 Gremlin API Table을 가져옵니다. |
|**Azure 리소스 그룹**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.
