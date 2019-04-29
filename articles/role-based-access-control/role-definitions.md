---
title: Azure 리소스에 대한 RBAC의 역할 정의 이해 | Microsoft Docs
description: Azure 리소스의 세분화된 액세스 관리에 사용되는 RBAC(역할 기반 액세스 제어)의 역할 정의에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121829"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Azure 리소스에 대한 역할 정의 이해

역할의 작동 방식을 파악하려고 하거나, [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)을 만드는 경우 역할이 정의되는 방식을 파악하면 도움이 됩니다. 이 문서에서는 역할 정의에 대한 자세한 내용을 설명하고 몇 가지 예제를 제공합니다.

## <a name="role-definition-structure"></a>역할 정의 구조

*역할 정의*는 권한의 모음입니다. 때로는 *역할*이라고 합니다. 역할 정의에는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업이 나열됩니다. 또한 수행할 수 없는 작업이나 기본 데이터와 관련된 작업이 나열될 수도 있습니다. 역할 정의의 구조는 다음과 같습니다.

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

작업은 다음과 같은 형식의 문자열로 지정됩니다.

- `{Company}.{ProviderName}/{resourceType}/{action}`

작업 문자열의 `{action}` 부분은 리소스 종류에서 수행할 수 있는 작업의 유형을 지정합니다. 예를 들어 `{action}`에 표시되는 부분 문자열은 다음과 같습니다.

| 작업 부분 문자열    | 설명         |
| ------------------- | ------------------- |
| `*` | 와일드카드 문자는 문자열과 일치하는 모든 작업에 대한 액세스 권한을 부여합니다. |
| `read` | 읽기 작업(GET)을 사용하도록 설정합니다. |
| `write` | 쓰기 작업(PUT, POST 및 PATCH)을 사용하도록 설정합니다. |
| `delete` | 삭제 작업(DELETE)을 사용하도록 설정합니다. |

JSON 형식의 [기여자](built-in-roles.md#contributor) 역할 정의가 있습니다. `Actions`에 포함된 와일드카드(`*`) 작업은 이 역할에 할당된 주체가 모든 작업을 수행할 수 있음, 즉 모든 항목을 관리할 수 있음을 나타냅니다. 여기에는 나중에 Azure에서 새 리소스 종류를 추가함에 따라 정의되는 작업이 포함됩니다. `NotActions`에 속한 작업은 `Actions`에서 제외됩니다. [기여자](built-in-roles.md#contributor) 역할의 경우 `NotActions`는 리소스에 대한 액세스를 관리하고 할당하는 이 역할의 기능을 제거합니다.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations-preview"></a>관리 및 데이터 작업(미리 보기)

관리 작업에 대한 역할 기반 액세스 제어는 역할 정의의 `Actions` 및 `NotActions` 속성에 지정됩니다. Azure의 관리 작업에 대한 몇 가지 예는 다음과 같습니다.

- 저장소 계정에 대한 액세스 관리
- Blob 컨테이너 만들기, 업데이트 또는 삭제
- 리소스 그룹 및 해당하는 모든 리소스 삭제

관리 액세스 권한은 데이터에 상속되지 않습니다. 이렇게 분리하면 와일드카드(`*`)가 있는 역할에서 데이터에 무제한으로 액세스할 수 없게 됩니다. 예를 들어 구독에 [읽기 권한자](built-in-roles.md#reader) 역할이 있는 사용자는 저장소 계정을 볼 수 있지만 기본적으로 기본 데이터는 볼 수 없습니다.

이전에는 역할 기반 액세스 제어가 데이터 작업에 사용되지 않았습니다. 데이터 작업에 대한 권한 부여는 리소스 공급자에 따라 다양합니다. 관리 작업에 사용되는 것과 동일한 역할 기반 액세스 제어 권한 부여 모델이 데이터 작업(현재 미리 보기)으로 확장되었습니다.

데이터 작업을 지원하기 위해 새로운 데이터 속성이 역할 정의 구조에 추가되었습니다. 데이터 작업은 `DataActions` 및 `NotDataActions` 속성에서 지정됩니다. 이러한 데이터 속성을 추가함으로써 관리와 데이터 간의 분리가 유지됩니다. 이렇게 하면 와일드카드(`*`)와 함께 현재 역할 할당을 사용하여 갑자기 데이터에 액세스하는 것을 방지할 수 있습니다. `DataActions` 및 `NotDataActions`에서 지정할 수 있는 데이터 작업은 다음과 같습니다.

- 컨테이너의 Blob 목록 읽기
- 컨테이너에 Storage Blob 쓰기
- 큐의 메시지 삭제

다음은 [Storage Blob 데이터 판독기](built-in-roles.md#storage-blob-data-reader) 둘 다에서 작업을 포함 하는 역할 정의 `Actions` 및 `DataActions` 속성. 이 역할을 사용하면 Blob 컨테이너 및 기본 Blob 데이터를 읽을 수 있습니다.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

데이터 작업만 `DataActions` 및 `NotDataActions` 속성에 추가할 수 있습니다. 리소스 공급자는 `isDataAction` 속성을 `true`로 설정하여 데이터 작업을 식별합니다. `isDataAction`이 `true`인 작업 목록을 보려면 [리소스 공급자 작업](resource-provider-operations.md)을 참조하세요. 데이터 작업이 없는 역할은 역할 정의 내에 `DataActions` 및 `NotDataActions` 속성이 없어도 됩니다.

모든 관리 작업 API 호출에 대한 권한 부여는 Azure Resource Manager에서 처리합니다. 데이터 작업 API 호출에 대한 권한 부여는 리소스 공급자 또는 Azure Resource Manager에 의해 처리됩니다.

### <a name="data-operations-example"></a>데이터 작업 예제

관리 및 데이터 작업 방법에 대한 이해를 돕기 위한 구체적인 예를 살펴 보겠습니다. Alice는 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당 받았습니다. Bob이 할당 합니다 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) 저장소 계정 범위에서 역할입니다. 다음 다이어그램은 이 예제를 보여 줍니다.

![역할 기반 액세스 제어가 관리 및 데이터 작업을 모두 지원하도록 확장되었습니다.](./media/role-definitions/rbac-management-data.png)

합니다 [소유자](built-in-roles.md#owner) Alice에 대 한 역할 및 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) Bob에 대 한 역할이 다음 작업을 합니다.

소유자

&nbsp;&nbsp;&nbsp;&nbsp;작업<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob 데이터 Contributor

&nbsp;&nbsp;&nbsp;&nbsp;작업<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Alice는 구독 범위에서 와일드카드(`*`) 작업을 수행하므로 그녀의 권한은 그녀가 모든 관리 작업을 수행할 수 있도록 상속됩니다. Alice는 컨테이너를 읽고 쓰며 삭제할 수 있습니다. 그러나 Alice는 추가 단계를 수행하지 않고도 데이터 작업을 수행할 수 없습니다. 예를 들어 Alice는 기본적으로 컨테이너 내부의 Blob을 읽을 수 없습니다. Blob을 읽으려면 스토리지 액세스 키를 검색하고 사용하여 Blob에 액세스해야 합니다.

Bob의 권한은 제한만 `Actions` 하 고 `DataActions` 에 지정 된 된 [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor) 역할. 역할에 따라 Bob은 관리 및 데이터 작업을 모두 수행할 수 있습니다. 예를 들어 Bob은 지정된 저장소 계정에서 컨테이너를 읽고, 쓰고, 삭제할 수 있으며 Blob을 읽고, 쓰고, 삭제할 수도 있습니다.

스토리지의 관리 및 데이터 평면 보안에 대한 자세한 내용은 [Azure Storage 보안 가이드](../storage/common/storage-security-guide.md)를 참조하세요.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>RBAC를 사용한 데이터 작업을 지원하는 도구는 무엇인가요?

데이터 작업을 보고 작업하려면 도구 또는 SDK의 올바른 버전이 있어야 합니다.

| 도구  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 이상 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 이상 |
| [.NET용 Azure](/dotnet/azure/) | 2.8.0-미리 보기 이상 버전 |
| [Azure SDK for Go](/go/azure/azure-sdk-go-install) | 15.0.0 이상 |
| [Java용 Azure](/java/azure/) | 1.9.0 이상 |
| [Python용 Azure](/python/azure) | 0.40.0 이상 |
| [Ruby용 Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 이상 |

REST API에서 데이터 작업을 보고 사용하려면 **api-version** 매개 변수를 다음 버전 이상으로 설정해야 합니다.

- 2018-01-01-preview

Azure Portal에서는 또한 Azure AD 환경에서 큐 및 Blob 컨테이너의 콘텐츠를 찾아보고 관리할 수 있습니다. 큐 또는 Blob 컨테이너 콘텐츠를 보고 관리하려면 스토리지 계정 개요에서 **Azure AD 미리 보기를 사용하여 데이터 알아보기**를 클릭합니다.

![Azure AD 미리 보기를 사용하여 큐 및 Blob 컨테이너 알아보기](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>작업

`Actions` 권한은 역할에서 수행할 수 있는 관리 작업을 지정합니다. Azure 리소스 공급자의 보안 개체 작업을 식별하는 작업 문자열 모음입니다. `Actions`에서 사용할 수 있는 관리 작업의 몇 가지 예제는 다음과 같습니다.

| 작업 문자열    | 설명         |
| ------------------- | ------------------- |
| `*/read` | 모든 Azure 리소스 공급자에 있는 모든 리소스 종류의 읽기 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Compute/*` | Microsoft.Compute 리소스 공급자에 있는 모든 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Network/*/read` | Microsoft.Network 리소스 공급자에 있는 모든 리소스 종류의 읽기 작업에 대한 액세스 권한을 부여합니다.|
| `Microsoft.Compute/virtualMachines/*` | 가상 머신 및 해당 자식 리소스 종류의 모든 작업에 대한 액세스 권한을 부여합니다.|
| `microsoft.web/sites/restart/Action` | 웹앱을 다시 시작하기 위한 액세스 권한을 부여합니다.|

## <a name="notactions"></a>NotActions

`NotActions` 권한은 허용된 `Actions`에서 제외되는 관리 작업을 지정합니다. 제한된 작업을 제외하여 허용하려는 작업 집합을 더 쉽게 정의하는 경우 `NotActions` 권한을 사용합니다. 역할(유효 사용 권한)로 부여되는 액세스 권한은 `Actions` 작업에서 `NotActions` 작업을 제외하여 계산됩니다.

> [!NOTE]
> 사용자에게 `NotActions`에서 작업을 제외하는 역할이 할당되고 동일한 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 사용자는 해당 작업을 수행할 수 있습니다. `NotActions`는 거부 규칙이 아니며, 특정 작업을 제외해야 할 경우 허용되는 작업 집합을 만드는 편리한 방법일 뿐입니다.
>

## <a name="dataactions-preview"></a>DataActions(미리 보기)

`DataActions` 권한은 역할에서 해당 개체 내의 데이터에 대해 수행할 수 있는 데이터 작업을 지정합니다. 예를 들어 사용자가 저장소 계정에 대한 Blob 데이터 읽기 액세스 권한이 있는 경우 해당 저장소 계정 내의 Blob을 읽을 수 있습니다. `DataActions`에서 사용할 수 있는 데이터 작업의 몇 가지 예제는 다음과 같습니다.

| 작업 문자열    | 설명         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Blob 또는 Blob 목록 반환 |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Blob 쓰기 결과 반환 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | 메시지 반환 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | 메시지 또는 메시지 작성/삭제 결과를 반환합니다. |

## <a name="notdataactions-preview"></a>NotDataActions(미리 보기)

`NotDataActions` 권한은 허용된 `DataActions`에서 제외되는 데이터 작업을 지정합니다. 역할(유효 사용 권한)로 부여되는 액세스 권한은 `DataActions` 작업에서 `NotDataActions` 작업을 제외하여 계산됩니다. 각 리소스 공급자는 데이터 작업을 수행하기 위한 각각의 API 집합을 제공합니다.

> [!NOTE]
> 사용자에게 `NotDataActions`에서 데이터 작업을 제외하는 역할이 할당되고 동일한 데이터 작업에 대한 액세스 권한을 부여하는 두 번째 역할이 할당된 경우 사용자는 해당 데이터 작업을 수행할 수 있습니다. `NotDataActions`는 거부 규칙이 아니며, 특정 데이터 작업을 제외해야 할 경우 허용되는 데이터 작업 집합을 만드는 편리한 방법일 뿐입니다.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes` 속성에서는 역할을 할당할 수 있는 범위(구독, 리소스 그룹 또는 리소스)를 지정합니다. 역할이 필요한 구독 또는 리소스 그룹에만 역할을 할당할 수 있도록 하며, 나머지 구독 또는 리소스 그룹에 대해 혼란스러운 사용자 환경이 아닙니다. 구독, 리소스 그룹 또는 리소스 ID를 적어도 하나 사용해야 합니다.

기본 제공 역할에는 루트 범위(`"/"`)로 설정된 `AssignableScopes`가 있습니다. 루트 범위는 모든 범위에서 역할을 할당에 사용할 수 있음을 나타냅니다. 유효한 할당 가능한 범위의 예는 다음과 같습니다.

| 시나리오 | 예 |
|----------|---------|
| 역할은 단일 구독에 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 역할은 두 개의 구독에 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| 역할은 네트워크 리소스 그룹에만 할당할 수 있습니다. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| 역할은 모든 범위에 할당할 수 있습니다. | `"/"` |

사용자 지정 역할의 `AssignableScopes`에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)
* [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
* [Azure Resource Manager 리소스 공급자 작업](resource-provider-operations.md)
