---
title: 대규모 데이터 세트로 작업
description: Azure Resource Graph를 사용하는 동안 큰 데이터 세트를 가져오고 제어하는 방법을 파악합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/01/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 729e9fe749212942c6dc18fc7d6301934e7dd184
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775900"
---
# <a name="working-with-large-azure-resource-data-sets"></a>큰 Azure 리소스 데이터 세트 작업

Azure Resource Graph는 Azure 환경의 리소스로 작업하고 해당 정보를 가져오기 위한 것입니다. Resource Graph를 사용하면 수천 개의 레코드를 쿼리하는 경우에도 이 데이터를 빠르게 가져올 수 있습니다. Resource Graph에는 이러한 큰 데이터 세트 작업을 위한 여러 가지 옵션이 있습니다.

## <a name="data-set-result-size"></a>데이터 세트 결과 크기

기본적으로 Resource Graph는 **100**개의 레코드만 반환하도록 쿼리를 제한합니다. 이 제어는 큰 데이터 세트를 생성하는 의도하지 않은 쿼리로부터 사용자와 서비스를 둘 다 보호합니다. 이 이벤트는 주로 고객이 특정 요구 사항에 맞는 방식으로 리소스를 찾고 필터링하기 위해 쿼리를 실험하는 경우에 발생합니다. 이 제어는 [top](/azure/kusto/query/topoperator) 또는 [limit](/azure/kusto/query/limitoperator) Azure Data Explorer 언어 연산자를 사용하여 결과를 제한하는 것과 다릅니다.

> [!NOTE]
> 사용 하는 경우 **첫 번째**를 사용 하 여 열을 하나 이상으로 결과 정렬 하려면 것이 좋습니다 `asc` 또는 `desc`합니다. 정렬 하지 않고 반환 된 결과 임의 및 반복 되지 않습니다.

Resource Graph를 조작하는 모든 메서드를 통해 기본 제한을 재정의할 수 있습니다. 다음 예제에서는 데이터 세트 크기 제한을 _200_으로 변경하는 방법을 보여 줍니다.

```azurecli-interactive
az graph query -q "project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -First 200
```

[REST API](/rest/api/azureresourcegraph/resources/resources)에서 제어는 **$top**이며 **QueryRequestOptions**의 일부입니다.

‘가장 제한적’인 제어가 적용됩니다. 예를 들어 쿼리에서 **top** 또는 **limit** 연산자를 사용하며 **First**보다 많은 레코드를 생성하는 경우 반환되는 최대 레코드 수는 **First**와 같습니다. 마찬가지로, **top** 또는 **limit**가 **First**보다 작은 경우 반환되는 레코드 집합은 **top** 또는 **limit**로 구성된 더 작은 값이 됩니다.

**First**의 현재 허용된 최댓값은 _5000_입니다.

## <a name="skipping-records"></a>레코드 건너뛰기

큰 데이터 세트 작업을 위한 다음 옵션은 **Skip** 제어입니다. 이 제어를 사용하면 쿼리가 결과를 반환하기 전에 정의된 레코드 수를 점프하거나 건너뛸 수 있습니다. **Skip**은 결과 집합의 중간에 있는 레코드에 접근하기 위해 의미 있는 방식으로 결과를 정렬하는 쿼리에 유용합니다. 필요한 결과가 반환된 데이터 세트의 끝에 있는 경우, 다른 정렬 구성을 사용하고 대신 데이터 세트의 맨 위에서 결과를 검색하는 것이 더 효율적입니다.

> [!NOTE]
> 사용 하는 경우 **건너뛰기**를 사용 하 여 열을 하나 이상으로 결과 정렬 하려면 것이 좋습니다 `asc` 또는 `desc`합니다. 정렬 하지 않고 반환 된 결과 임의 및 반복 되지 않습니다.

다음 예제에서는 반환된 결과 집합을 11번째 레코드로 시작하는 대신, 쿼리에서 생성하는 처음 _10_개의 레코드를 건너뛰는 방법을 보여 줍니다.

```azurecli-interactive
az graph query -q "project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "project name | order by name asc" -Skip 10
```

[REST API](/rest/api/azureresourcegraph/resources/resources)에서 제어는 **$skip**이며 **QueryRequestOptions**의 일부입니다.

## <a name="paging-results"></a>페이징 결과

결과 집합 처리에 대 한 레코드의 더 작은 집합으로 분할 해야 할 때 또는 결과 집합의 허용 되는 최대값을 초과 하므로 _1000_ 페이징을 사용 하 여 레코드를 반환 합니다. [REST API](/rest/api/azureresourcegraph/resources/resources) **QueryResponse**는 결과 집합이 분할되었음을 나타내는 값(**resultTruncated** 및 **$skipToken**)을 제공합니다.
**resultTruncated**는 응답에 반환되지 않은 추가 레코드가 있는지 여부를 소비자에게 알리는 부울 값입니다. **count** 속성이 **totalRecords** 속성보다 작은 경우에도 이 조건을 식별할 수 있습니다. **totalRecords**는 쿼리와 일치하는 레코드 수를 정의합니다.

**resultTruncated**가 **true**이면 **$skipToken** 속성이 응답에 설정됩니다. 이 값은 동일한 쿼리 및 구독 값과 함께 사용되어 쿼리와 일치하는 다음 레코드 집합을 가져옵니다.

> [!IMPORTANT]
> 페이지 매김이 작동하려면 쿼리가 **ID** 필드를 **project**해야 합니다. 쿼리에서 누락 된 경우 REST API 응답에 포함 되지 않습니다 합니다 **$skipToken**합니다.

예제는 REST API 문서에서 [다음 페이지 쿼리](/rest/api/azureresourcegraph/resources/resources#next_page_query)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어 알아보기
- [고급 쿼리](../samples/advanced.md)의 고급 사용법 알아보기
- [리소스 탐색](explore-resources.md)에 대해 알아보기