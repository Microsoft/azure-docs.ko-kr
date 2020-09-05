---
title: Azure Storage 수명 주기 관리
description: 오래된 데이터를 핫에서 쿨로 전환하고 계층을 보관하는 수명 주기 정책 규칙을 만드는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1bf8fbfb6d2c141a2b18c3599631f6383883908
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074426"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Azure Blob Storage 수명 주기 관리

데이터 집합에는 고유한 수명 주기가 있습니다. 수명 주기 초기에는 사람들이 일부 데이터에 자주 액세스합니다 하지만 액세스 필요성은 데이터가 오래될 수록 크게 줄어듭니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다가 한 번씩 액세스됩니다. 어떤 데이터는 생성 며칠 후 또는 몇 달 후 만료되고, 또 어떤 데이터 세트는 수명 주기 전체에 걸쳐 적극적으로 읽히고 수정됩니다. Azure Blob 저장소 수명 주기 관리는 GPv2 및 Blob storage 계정에 대 한 다양 한 규칙 기반 정책을 제공 합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다.

수명 주기 관리 정책을 사용하여 다음을 수행할 수 있습니다.

- BLOB을 쿨 스토리지 계층으로 전환하여(핫 스토리지 계층에서 쿨 스토리지 계층으로, 핫 스토리지 계층에서 보관 스토리지 계층으로 또는 쿨 스토리지 계층에서 보관 스토리지 계층으로) 성능 및 비용 최적화
- 수명 주기가 끝나면 BLOB 삭제
- 스토리지 계정 수준에서 하루에 한 번 실행할 규칙 정의
- 컨테이너 또는 blob의 하위 집합에 규칙 적용 (이름 접두사 또는 [blob 인덱스 태그](storage-manage-find-blobs.md) 를 필터로 사용)

수명 주기의 초기 단계에서 데이터가 자주 액세스 되는 시나리오를 고려 하 여 2 주 후에만 가끔 액세스 합니다. 첫 번째 달 이후에는 데이터 세트에 거의 액세스하지 않습니다. 이 시나리오에서 초기 단계 동안에는 핫 스토리지 계층이 가장 적절합니다. 쿨 저장소는 간헐적으로 액세스 하는 데 가장 적합 합니다. 보관 저장소는 한 달에 데이터를 사용한 후 가장 좋은 계층 옵션입니다. 데이터의 보존 기간에 따라 스토리지 계층을 조정하면 요구 사항에 맞는 가장 저렴한 스토리지 옵션을 디자인할 수 있습니다. 이와 같이 전환하려면 수명 주기 관리 정책 규칙을 사용하여 오래된 데이터를 쿨 계층으로 이동하면 됩니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

수명 주기 관리 기능은 범용 v2 (GPv2) 계정, Blob storage 계정 및 프리미엄 블록 Blob 저장소 계정에 대 한 모든 Azure 지역에서 사용할 수 있습니다. Azure Portal에서 기존 범용 (GPv1) 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.  

수명 주기 관리 기능은 무료로 제공 됩니다. 고객에 게는 [Set Blob 계층](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API 호출에 대 한 일반 작업 비용이 청구 됩니다. 삭제 작업은 무료입니다. 가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="add-or-remove-a-policy"></a>정책 추가 또는 제거

다음 방법 중 하나를 사용 하 여 정책을 추가, 편집 또는 제거할 수 있습니다.

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

정책은 전체로 읽거나 쓸 수 있습니다. 부분 업데이트는 지원 되지 않습니다. 

> [!NOTE]
> 스토리지 계정에 방화벽 규칙을 사용하도록 설정하면 수명 주기 관리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대 한 예외를 제공 하 여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)의 예외 섹션을 참조하세요.

이 문서에서는 포털 및 PowerShell 메서드를 사용 하 여 정책을 관리 하는 방법을 보여 줍니다.  

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 통해 정책을 추가 하는 방법에는 두 가지가 있습니다. 

* [Azure Portal 목록 뷰](#azure-portal-list-view)
* [Azure Portal 코드 보기](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure Portal 목록 뷰

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 저장소 계정을 검색 하 여 선택 합니다. 

3. **Blob Service**에서 **수명 주기 관리** 를 선택 하 여 규칙을 보거나 변경 합니다.

4. **목록 보기** 탭을 선택 합니다.

5. **규칙 추가** 를 선택한 다음 **작업 집합** 양식 필드를 채웁니다. 다음 예에서는 30 일 동안 수정 되지 않은 경우 blob을 쿨 저장소로 이동 합니다.

   ![Azure Portal의 수명 주기 관리 작업 설정 페이지](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. **필터 집합** 을 선택 하 여 선택적 필터를 추가 합니다. 그런 다음 **찾아보기** 를 선택 하 여 필터링 할 컨테이너와 폴더를 지정 합니다.

   ![Azure Portal의 수명 주기 관리 필터 설정 페이지](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. **검토 + 추가** 를 선택 하 여 정책 설정을 검토 합니다.

9. **추가** 를 선택 하 여 새 정책을 추가 합니다.

#### <a name="azure-portal-code-view"></a>Azure Portal 코드 보기
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 저장소 계정을 검색 하 여 선택 합니다.

3. **Blob Service**에서 **수명 주기 관리** 를 선택 하 여 정책을 보거나 변경 합니다.

4. 다음 JSON은 **코드 보기** 탭에 붙여 넣을 수 있는 정책의 예입니다.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. **저장**을 선택합니다.

6. 이 JSON 예제에 대 한 자세한 내용은 [정책](#policy) 및 [규칙](#rules) 섹션을 참조 하십시오.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용 하 여 저장소 계정에 정책을 추가할 수 있습니다. `$rgname` 변수는 리소스 그룹 이름으로 초기화해야 합니다. `$accountName` 변수는 스토리지 계정 이름으로 초기화해야 합니다.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[템플릿](#tab/template)

Azure Resource Manager 템플릿을 사용 하 여 수명 주기 관리를 정의할 수 있습니다. 다음은 수명 주기 관리 정책으로 RA GRS GPv2 storage 계정을 배포 하는 샘플 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>정책

수명 주기 관리 정책은 JSON 문서의 규칙 컬렉션입니다.

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

정책은 규칙의 컬렉션입니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 |
|----------------|----------------|-------|
| `rules`        | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요 합니다. 정책에서 최대 100 개의 규칙을 정의할 수 있습니다.|

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 |
|----------------|----------------|-------|----------|
| `name`         | 문자열 |규칙 이름에는 최대 256 자의 영숫자 문자를 사용할 수 있습니다. 규칙 이름은 대/소문자를 구분합니다.  정책 내에서 고유해야 합니다. | True |
| `enabled`      | 부울 | 규칙을 일시적으로 사용 하지 않도록 설정할 수 있도록 하는 선택적 부울입니다. 설정 되지 않은 경우 기본값은 true입니다. | 거짓 | 
| `type`         | 열거형 값 | 현재 유효한 형식은 `Lifecycle` 입니다. | True |
| `definition`   | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. | True |

## <a name="rules"></a>규칙

각 규칙 정의는 필터 집합과 작업 집합을 포함합니다. [필터 집합](#rule-filters)은 컨테이너 또는 개체 이름 내에서 개체의 특정 집합으로 규칙 작업을 제한합니다. [작업 집합](#rule-actions)은 계층을 적용하거나 필터링된 개체 집합에 대한 작업을 삭제합니다.

### <a name="sample-rule"></a>샘플 규칙

다음 샘플 규칙은 내부에 존재 하는 개체에 대 한 작업을 실행 하 `container1` 고로 시작 하는 계정을 필터링 합니다 `foo` .  

>[!NOTE]
>- 수명 주기 관리는 블록 blob 유형만 지원 합니다.<br>
>- 수명 주기 관리는 $logs 및 $web와 같은 시스템 컨테이너에 영향을 주지 않습니다.

- 마지막으로 수정한 시점으로부터 30일 후 Blob을 쿨 계층으로 이동
- 마지막으로 수정한 시점으로부터 90일 후 Blob을 보관 계층으로 이동
- 마지막으로 수정한 시점으로부터 2,555일(7년) 후 BLOB 삭제
- 스냅샷을 생성한 시점으로부터 90일 후 Blob 스냅샷 삭제

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>규칙 필터

필터는 규칙 작업을 스토리지 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 둘 이상의 필터를 정의하는 경우 논리적 `AND`가 모든 필터에서 실행됩니다.

필터에는 다음이 포함됩니다.

| 필터 이름 | 필터 형식 | 참고 | 필수 여부 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 현재 릴리스에서는를 지원 `blockBlob` 합니다. | 예 |
| prefixMatch | 일치 시킬 접두사의 문자열 배열입니다. 각 규칙은 최대 10 개의 접두사를 정의할 수 있습니다. 접두사 문자열은 컨테이너 이름으로 시작해야 합니다. 예를 들어에서 규칙에 대 한 모든 blob을 일치 시키려는 경우 `https://myaccount.blob.core.windows.net/container1/foo/...` prefixMatch은 `container1/foo` 입니다. | PrefixMatch를 정의 하지 않으면 규칙은 저장소 계정 내의 모든 blob에 적용 됩니다.  | 아니요 |
| blobIndexMatch | 일치 시킬 Blob 인덱스 태그 키 및 값 조건으로 구성 된 사전 값의 배열입니다. 각 규칙은 최대 10 개의 Blob 인덱스 태그 조건을 정의할 수 있습니다. 예를 들어 규칙에 대해에서의 모든 blob을 일치 시키려는 경우 `Project = Contoso` `https://myaccount.blob.core.windows.net/` blobIndexMatch는 `{"name": "Project","op": "==","value": "Contoso"}` 입니다. | BlobIndexMatch를 정의 하지 않으면 규칙은 저장소 계정 내의 모든 blob에 적용 됩니다. | 아니요 |

> [!NOTE]
> Blob 인덱스는 공개 미리 보기 상태 이며 **캐나다 중부**, **캐나다 동부**, **프랑스 중부**및 **프랑스 남부** 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께 이 기능에 대한 자세한 내용은 [Blob 인덱스(미리 보기)를 사용하여 Azure Blob 스토리지에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)를 참조하세요.

### <a name="rule-actions"></a>규칙 작업

실행 조건이 충족 되 면 필터링 된 blob에 동작이 적용 됩니다.

수명 주기 관리는 blob의 계층화 및 삭제와 blob 스냅숏 삭제를 지원 합니다. Blob 또는 Blob 스냅샷에 대한 각 규칙에 하나 이상의 작업을 정의합니다.

| 작업        | 기본 Blob                                   | 스냅샷      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 현재 핫 계층에서 Blob을 지원합니다.         | 지원되지 않음 |
| tierToArchive | 현재 핫 또는 쿨 계층에서 Blob을 지원합니다. | 지원되지 않음 |
| delete        | 지원됨                                   | 지원됨     |

>[!NOTE]
>동일한 Blob에 작업을 두 개 이상 정의하는 경우 수명 주기 관리는 가장 저렴한 작업을 Blob에 적용합니다. 예를 들어 `delete` 작업은 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업은 `tierToCool` 작업보다 저렴합니다.

실행 조건은 age를 기준으로 합니다. 기본 Blob은 마지막으로 수정된 시간을 사용하여 보존 기간을 추적하고 Blob 스냅샷은 스냅샷 생성 시간을 사용하여 보존 기간을 추적합니다.

| 작업 실행 조건             | 조건 값                          | 설명                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 blob 동작의 조건입니다.     |
| daysAfterCreationGreaterThan     | 일 단위로 보존 기간을 나타내는 정수 값 | Blob 스냅숏 작업 조건 |

## <a name="examples"></a>예

다음 예는 수명 주기 정책 규칙을 사용하여 일반 시나리오를 해결하는 방법을 보여줍니다.

### <a name="move-aging-data-to-a-cooler-tier"></a>오래된 데이터를 쿨 저장소 계층으로 이동

이 예제는 `container1/foo` 또는 `container2/bar` 접두사가 붙은 블록 Blob을 전환하는 방법을 보여줍니다. 이 정책은 30일 넘게 수정되지 않은 BLOB을 쿨 스토리지 계층으로 전환하고, 90일 동안 수정되지 않은 BLOB을 보관 스토리지 계층으로 전환합니다.

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-after-ingest"></a>수집 후 데이터 보관

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다. 다음 수명 주기 정책은 수집 되는 즉시 데이터를 보관 하도록 구성 됩니다. 이 예제에서는 컨테이너 내의 저장소 계정에서 블록 blob을 `archivecontainer` 보관 계층으로 전환 합니다. 전환은 마지막으로 수정한 시간 이후에 0 일 후에 blob에서 작동 하 여 수행 됩니다.

> [!NOTE] 
> Blob을 직접 업로드 하는 것이 더 효율적입니다. [Putblob](https://docs.microsoft.com/rest/api/storageservices/put-blob) 에 대 한 x-y Blob 또는 [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) REST 버전 2018-11-09 이상 또는 최신 blob storage 클라이언트 라이브러리와 함께 사용할 수 있습니다. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>보존 기간에 따라 데이터 만료

일부 데이터는 만든 후 며칠 또는 몇 개월 후에 만료 될 것으로 예상 됩니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에서는 365 일 보다 오래 된 모든 블록 blob를 삭제 하는 정책을 보여 줍니다.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Blob 인덱스 태그를 사용 하 여 데이터 삭제
일부 데이터는 명시적으로 삭제 하도록 표시 된 경우에만 만료 되어야 합니다. Blob 인덱스 키/값 특성으로 태그가 지정 된 데이터를 만료 하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예제에서는로 태그가 지정 된 모든 블록 blob를 삭제 하는 정책을 보여 줍니다 `Project = Contoso` . Blob 인덱스에 대해 자세히 알아보려면 [Blob 인덱스(미리 보기)를 사용하여 Azure Blob 스토리지에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)를 참조하세요.

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>오래된 스냅샷 삭제

전체 수명 주기 동안 주기적으로 수정되고 액세스되는 데이터의 경우 해당 데이터의 이전 버전을 추적하기 위해 스냅샷이 자주 사용됩니다. 스냅샷 기간에 따라 오래된 스냅샷을 삭제하는 정책을 만들 수 있습니다. 스냅샷 기간은 스냅샷 생성 시간을 평가하여 확인합니다. 이 정책 규칙은 `activedata` 컨테이너 내에 있고 스냅샷 생성 후 90일 이상이 경과된 블록 Blob 스냅샷을 삭제합니다.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>FAQ

**새 정책을 만들었으며 작업이 즉시 실행 되지 않는 이유는 무엇 인가요?**  
플랫폼은 하루에 한 번 수명 주기 정책을 실행합니다. 정책을 구성한 후에는 일부 작업을 처음 실행 하는 데 최대 24 시간이 걸릴 수 있습니다.  

**기존 정책을 업데이트 하는 경우 작업이 실행 되는 데 얼마나 걸립니까?**  
업데이트 된 정책은 적용 되는 데 최대 24 시간이 걸립니다. 정책이 적용 되 면 작업이 실행 되는 데 최대 24 시간이 걸릴 수 있습니다. 따라서 정책 작업을 완료 하는 데 최대 48 시간이 걸릴 수 있습니다.   

**보관 된 blob을 수동으로 전환 하 여 보관 계층으로 다시 이동 하는 것을 방지 하려면 어떻게 해야 하나요?**  
Blob을 한 액세스 계층에서 다른 액세스 계층으로 이동 하면 마지막 수정 시간이 변경 되지 않습니다. 보관 된 blob을 핫 계층으로 수동으로 리하이드레이션 수명 주기 관리 엔진에서 보관 계층으로 다시 이동 합니다. 이 blob에 일시적으로 영향을 주는 규칙을 사용 하지 않도록 설정 하 여 다시 보관 되지 않도록 합니다. Blob을 다시 보관 계층으로 안전 하 게 이동할 수 있는 경우 규칙을 다시 사용 하도록 설정 합니다. 핫 또는 쿨 계층을 영구적으로 유지 해야 하는 경우 blob을 다른 위치로 복사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

실수로 삭제된 데이터를 복구하는 방법을 알아봅니다.

- [Azure Storage Blob에 대한 일시 삭제](../blobs/storage-blob-soft-delete.md)

Blob 인덱스를 사용 하 여 데이터를 관리 하 고 찾는 방법에 대해 알아봅니다.

- [Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)
