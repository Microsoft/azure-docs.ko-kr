---
title: Azure CLI를 사용 하 여 RBAC-Azure Storage를 사용 하 여 blob 및 큐 데이터를 Azure AD 액세스 권한 관리
description: Azure CLI를 사용 하 여 컨테이너 및 역할 기반 액세스 제어 (RBAC)를 사용 하 여 큐에 대 한 액세스를 할당 합니다. Azure Storage는 Azure AD 통해 인증에 대 한 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: de8cb99ae5db93c2438a9ea982ad1c6c9324b47f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483624"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>Azure CLI를 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage blob 또는 큐 데이터에 액세스 하는 데 사용 권한의 공통 집합을 포함 하는 기본 제공 RBAC 역할 집합을 정의 합니다. 

RBAC 역할에는 Azure AD 보안 주체에 할당 된 Azure 부여 해당 보안 주체에 대 한 해당 리소스에 액세스 합니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. 사용자, 그룹, 응용 프로그램 서비스 주체를 Azure AD 보안 주체 수 또는 [Azure 리소스에 대 한 id 관리](../../active-directory/managed-identities-azure-resources/overview.md)합니다.

이 문서에서는 Azure CLI를 사용 하 여 기본 제공 RBAC 역할을 나열 하 고 사용자에 게 할당 하는 방법을 설명 합니다. Azure CLI를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure 명령줄 인터페이스 (CLI)](https://docs.microsoft.com/cli/azure)합니다.

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위를 결정 합니다. 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>사용 가능한 RBAC 역할 목록

Azure CLI를 사용 하 여 사용 가능한 기본 제공 RBAC 역할을 나열 하려면 사용 합니다 [az 역할 정의 목록](/cli/azure/role/definition#az-role-definition-list) 명령:

```azurecli-interactive
az role definition list --out table
```

Azure의 다른 기본 제공 역할 함께 나열 된 기본 제공 Azure Storage 데이터 역할을 볼 수 있습니다.

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>사용자에 게 RBAC 역할 할당

RBAC 역할에 사용자를 할당 하려면 사용 합니다 [az 역할 할당 만들기](/cli/azure/role/assignment#az-role-assignment-create) 명령입니다. 명령의 형식을 할당의 범위에 따라 달라질 수 있습니다. 다음 예제에서는 다양 한 범위의 사용자 역할을 할당 하는 방법을 보여 줍니다.

### <a name="container-scope"></a>컨테이너 범위

컨테이너에 범위가 지정 된 역할에 할당 하려면에 대 한 컨테이너의 범위를 포함 하는 문자열을 지정 합니다 `--scope` 매개 변수입니다. 컨테이너에 대 한 범위는 형식:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

다음 예제에서는 합니다 **Storage Blob 데이터 기여자** 라는 컨테이너에 범위가 지정 된 사용자에 게 역할 *샘플 컨테이너*합니다. 샘플 값와 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿀 수 있는지 확인 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>큐 범위

큐에 범위가 지정 된 역할에 할당 하려면 큐 범위를 포함 하는 문자열을 지정 합니다 `--scope` 매개 변수입니다. 큐에 대 한 범위는 형식:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

다음 예제에서는 합니다 **Storage 큐 데이터 기여자** 라는 큐에 범위가 지정 된 사용자에 게 역할 *샘플 큐*합니다. 샘플 값와 대괄호 안의 자리 표시자 값을 고유한 값으로 바꿀 수 있는지 확인 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>저장소 계정 범위

저장소 계정에 범위가 지정 된 역할에 할당 하려면에 대 한 저장소 계정 리소스의 범위를 지정 합니다 `--scope` 매개 변수입니다. 저장소 계정에 대 한 범위는 형식:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

다음 예제에서는 할당 하는 방법의 **Storage Blob 데이터 판독기** 저장소 계정 수준에서 사용자 역할. 샘플 값을 고유한 값으로 대체 해야 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>리소스 그룹 범위

리소스 그룹으로 범위가 지정 된 역할에 할당 하려면 리소스 그룹 이름 또는 ID에 대 한 지정 된 `--resource-group` 매개 변수입니다. 다음 예제에서는 합니다 **Storage 큐 데이터 판독기** 리소스 그룹 수준의 사용자 역할. 사용자 고유의 값을 사용 하 여 샘플 값과 대괄호 안의 자리 표시자 값을 교체할 수 있는지 확인 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>구독 범위

구독에 범위가 지정 된 역할에 할당 하려면 구독에 대 한 범위를 지정 합니다 `--scope` 매개 변수입니다. 구독에 대 한 범위는 형식:

```
/subscriptions/<subscription>
```

다음 예제에서는 할당 하는 방법의 **Storage Blob 데이터 판독기** 저장소 계정 수준에서 사용자 역할. 샘플 값을 고유한 값으로 대체 해야 합니다. 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>다음 단계

- [RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)
- [Azure PowerShell을 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)
- [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
