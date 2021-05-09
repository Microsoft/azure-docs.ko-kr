---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67181878"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>테이블 엔터티 관리

이제 테이블이 준비되었으므로 테이블에서 엔터티 또는 행을 관리하는 방법을 살펴보겠습니다. 

엔터티에는 3개의 시스템 속성 즉, **PartitionKey**, **RowKey** 및 **Timestamp** 를 포함하여 최대 255개의 속성이 있을 수 있습니다. **PartitionKey** 및 **RowKey** 의 값을 삽입 및 업데이트해야 합니다. 서버는 수정할 수 없는 **타임스팸프** 값을 관리합니다. **PartitionKey** 및 **RowKey** 가 함께 테이블 내의 모든 엔터티를 고유하게 식별합니다.

* **PartitionKey**: 엔터티가 저장되는 파티션을 결정합니다.
* **RowKey**: 파티션 내에서 엔터티를 고유하게 식별합니다.

엔터티에 대한 사용자 지정 속성을 최대 252개 정의할 수 있습니다. 

### <a name="add-table-entities"></a>테이블 엔터티 추가

**Add-AzTableRow** 를 사용하여 테이블에 엔터티를 추가합니다. 이 예제에서는 값이 `partition1` 및 `partition2`인 파티션 키와 주 약어와 같은 행 키를 사용합니다. 각 엔터티의 속성은 `username` 및 `userid`입니다. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>테이블 엔터티 쿼리

**Get-AzTableRow** 명령을 사용하여 테이블의 엔터티를 쿼리할 수 있습니다.

> [!NOTE]
> **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** 및 **Get-AzureStorageTableRowByCustomFilter** cmdlet은 더 이상 사용되지 않으며 이후 버전 업데이트에서 제거될 예정입니다.

#### <a name="retrieve-all-entities"></a>모든 엔터티 검색

```powershell
Get-AzTableRow -table $cloudTable | ft
```

이 명령은 다음 표와 비슷한 결과를 생성합니다.

| userId | 사용자 이름 | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>특정 파티션에 대한 엔터티 검색

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

결과는 다음 표와 비슷합니다.

| userId | 사용자 이름 | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>특정 열의 특정 값에 대한 엔터티 검색

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

이 쿼리는 하나의 레코드를 검색합니다.

|필드(field)|value|
|----|----|
| userId | 1 |
| 사용자 이름 | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>사용자 지정 필터를 사용하여 엔터티 검색 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

이 쿼리는 하나의 레코드를 검색합니다.

|필드(field)|value|
|----|----|
| userId | 1 |
| 사용자 이름 | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>엔터티 업데이트 

엔터티를 업데이트하는 세 가지 단계가 있습니다. 먼저, 변경할 엔터티를 검색합니다. 둘째, 엔터티를 변경합니다. 셋째, **Update-AzTableRow** 를 사용하여 변경 내용을 커밋합니다.

엔터티를 username = 'Jessie'로 업데이트하면 username = 'Jessie2'가 됩니다. 또한 이 예제에서는 .NET 형식을 사용하여 사용자 지정 필터를 만드는 또 다른 방법을 보여 줍니다.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

결과에서 Jessie2 레코드를 보여 줍니다.

|필드(field)|value|
|----|----|
| userId | 2 |
| 사용자 이름 | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>테이블 엔터티 삭제

테이블에서 하나의 엔터티 또는 모든 엔터티를 삭제할 수 있습니다.

#### <a name="deleting-one-entity"></a>하나의 엔터티 삭제

엔터티를 하나만 삭제하려면 해당 엔터티에 대한 참조를 가져와서 **Remove-AzTableRow** 에 파이프합니다.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>테이블의 모든 엔터티 삭제

테이블의 모든 엔터티를 삭제하려면 해당 엔터티를 검색하고 결과를 Remove cmdlet으로 파이프합니다. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
