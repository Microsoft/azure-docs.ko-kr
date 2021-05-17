---
title: REST를 사용하여 사용자 할당 관리 ID 관리 - Azure AD
description: REST API를 호출하기 위해 사용자 할당 관리 ID를 만들고 나열하고 삭제하는 방법에 대한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546695"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>REST API 호출을 사용하여 사용자 할당 관리 ID 만들기, 나열 또는 삭제

Azure 리소스에 대한 관리 ID는 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증하는 기능을 Azure 서비스에 제공합니다. 

이 문서에서는 CURL을 통해 REST API를 호출하여 사용자 할당 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 모든 명령은 클라우드에서 또는 로컬로 실행할 수 있습니다.
    - 클라우드에서 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
    - 로컬로 실행하려면 [넘기기](https://curl.haxx.se/download.html)와 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.

## <a name="obtain-a-bearer-access-token"></a>전달자 액세스 토큰 가져오기

1. 로컬로 실행하는 경우 Azure CLI를 통해 Azure에 로그인합니다.

    ```
    az login
    ```

1. [Az account get-token](/cli/azure/account#az_account_get_access_token)을 사용하여 액세스 토큰 가져오기

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**요청 헤더**

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

**요청 본문**

|이름  |설명  |
|---------|---------|
|위치     | 필수 요소. 리소스 위치.        |

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

> [!NOTE]
> 사용자 할당 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. CURL을 사용하여 VM에서 사용자 할당 관리 ID를 제거하려면 [Azure VM에서 사용자 할당 ID 제거](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)를 참조하세요.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

## <a name="next-steps"></a>다음 단계

CURL을 사용하여 Azure VM/VMSS에 사용자 할당 관리 ID를 할당하는 방법에 대한 자세한 내용은 [REST API 호출을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-rest-vm.md#user-assigned-managed-identity) 및 [REST API 호출을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-rest-vmss.md#user-assigned-managed-identity)을 참조하세요.
