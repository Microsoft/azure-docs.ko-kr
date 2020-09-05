---
title: Azure CLI를 사용 하 여 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법을 선택 합니다.
titleSuffix: Azure Storage
description: Azure CLI를 사용 하 여 blob 또는 큐 데이터에 대해 데이터 작업에 권한을 부여 하는 방법을 지정 합니다. Azure AD 자격 증명을 사용 하 여 계정 액세스 키를 사용 하거나 공유 액세스 서명 (SAS) 토큰을 사용 하 여 데이터 작업에 권한을 부여할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: a089ffb7631ded7bd36a4eee5fb862ced3fd2ad0
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589065"
---
# <a name="choose-how-to-authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Azure CLI를 사용 하 여 blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 방법을 선택 합니다.

Azure Storage는 blob 또는 큐 데이터에 대해 작업 권한을 부여 하는 방법을 지정할 수 있는 Azure CLI에 대 한 확장을 제공 합니다. 다음과 같은 방법으로 데이터 작업에 권한을 부여할 수 있습니다.

- Azure Active Directory (Azure AD) 보안 주체를 사용 합니다. Microsoft는 Azure AD 자격 증명을 사용 하 여 보안을 강화 하 고 사용 편의성을 권장 합니다.
- 계정 액세스 키 또는 SAS (공유 액세스 서명) 토큰을 사용 합니다.

## <a name="specify-how-data-operations-are-authorized"></a>데이터 작업에 권한을 부여 하는 방법 지정

Blob 및 큐 데이터를 읽고 쓰기 위한 명령에는 선택적 `--auth-mode` 매개 변수가 포함 됩니다. Azure CLI 데이터 작업에 권한을 부여 하는 방법을 나타내려면이 매개 변수를 지정 합니다.

- 매개 변수를로 설정 하 여 `--auth-mode` `login` Azure AD 보안 주체를 사용 하 여 로그인 합니다 (권장).
- `--auth-mode` `key` 권한 부여에 사용할 계정 액세스 키를 검색 하려는 경우 매개 변수를 레거시 값으로 설정 합니다. 매개 변수를 생략 하는 경우에 `--auth-mode` 도 Azure CLI는 액세스 키를 검색 하려고 시도 합니다.

매개 변수를 사용 하려면 `--auth-mode` Azure CLI 버전 2.0.46 이상을 설치 했는지 확인 합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

> [!IMPORTANT]
> 매개 변수를 생략 `--auth-mode` 하거나로 설정 하 `key` 는 경우 Azure CLI은 권한 부여를 위해 계정 액세스 키를 사용 하려고 시도 합니다. 이 경우 명령 또는 **AZURE_STORAGE_KEY** 환경 변수에 액세스 키를 제공 하는 것이 좋습니다. 환경 변수에 대 한 자세한 내용은 [권한 부여 매개 변수에 대 한 환경 변수 설정](#set-environment-variables-for-authorization-parameters)섹션을 참조 하세요.
>
> 액세스 키를 제공 하지 않으면 Azure CLI Azure Storage 리소스 공급자를 호출 하 여 각 작업에 대 한 검색을 수행 하려고 시도 합니다. 리소스 공급자를 호출 해야 하는 많은 데이터 작업을 수행 하면 제한이 발생할 수 있습니다. 리소스 공급자 제한에 대 한 자세한 내용은 [Azure Storage 리소스 공급자에 대 한 확장성 및 성능 목표](scalability-targets-resource-provider.md)를 참조 하세요.

## <a name="authorize-with-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 권한 부여

Azure AD 자격 증명을 사용 하 여 Azure CLI에 로그인 하면 OAuth 2.0 액세스 토큰이 반환 됩니다. 이 토큰은 Azure CLI에서 Blob 또는 큐 저장소에 대 한 후속 데이터 작업에 권한을 부여 하는 데 자동으로 사용 됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

RBAC (역할 기반 액세스 제어)를 통해 blob에 사용 권한을 할당 하 고 Azure AD 보안 주체에 데이터를 큐에 할당할 수 있습니다. Azure Storage의 Azure 역할에 대 한 자세한 내용은 [RBAC를 사용 하 여 데이터 Azure Storage에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.

### <a name="permissions-for-calling-data-operations"></a>데이터 작업 호출에 대 한 사용 권한

Azure Storage 확장은 blob 및 큐 데이터의 작업에 대해 지원 됩니다. 호출할 수 있는 작업은 Azure CLI 로그인 하는 데 사용 하는 Azure AD 보안 주체에 부여 된 권한에 따라 달라 집니다. Azure Storage 컨테이너 또는 큐에 대 한 사용 권한은 RBAC를 통해 할당 됩니다. 예를 들어 **Blob 데이터 판독기** 역할이 할당 된 경우 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob 데이터 참가자** 역할이 할당 된 경우 컨테이너 또는 큐 또는 포함 된 데이터를 읽거나, 쓰고, 삭제 하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너 또는 큐에서 각 Azure Storage 작업에 필요한 사용 권한에 대 한 자세한 내용은 [OAuth 토큰을 사용 하 여 저장소 작업 호출](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)을 참조 하세요.  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>예: Azure AD 자격 증명을 사용 하 여 컨테이너를 만들도록 작업 권한 부여

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure CLI에서 컨테이너를 만드는 방법을 보여 줍니다. 컨테이너를 만들려면 Azure CLI에 로그인 해야 하며, 리소스 그룹 및 저장소 계정이 필요 합니다. 이러한 리소스를 만드는 방법을 알아보려면 [빠른 시작: Azure CLI을 사용 하 여 Blob 만들기, 다운로드 및 나열](../blobs/storage-quickstart-blobs-cli.md)을 참조 하세요.

1. 컨테이너를 만들기 전에 [Storage Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자 인 경우에도 저장소 계정에 대해 데이터 작업을 수행 하려면 명시적 권한이 필요 합니다. Azure 역할 할당에 대 한 자세한 내용은 [Azure Portal에서 azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac.md)지정을 참조 하세요.

    > [!IMPORTANT]
    > Azure 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

1. 매개 변수가로 설정 된 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 명령을 호출 `--auth-mode` 하 여 `login` Azure AD 자격 증명을 사용 하 여 컨테이너를 만듭니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>계정 액세스 키를 사용 하 여 권한 부여

계정 키를 소유 하는 경우 모든 Azure Storage 데이터 작업을 호출할 수 있습니다. 일반적으로 계정 키를 사용 하는 것은 안전 하지 않습니다. 계정 키가 손상 되 면 계정에 있는 모든 데이터가 손상 될 수 있습니다.

다음 예제에서는 계정 액세스 키를 사용 하 여 컨테이너를 만드는 방법을 보여 줍니다. 계정 키를 지정 하 고 `--auth-mode` 다음 값으로 매개 변수를 제공 합니다 `key` .

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>SAS 토큰을 사용 하 여 권한 부여

SAS 토큰을 소유 하는 경우 SAS에서 허용 하는 데이터 작업을 호출할 수 있습니다. 다음 예제에서는 SAS 토큰을 사용 하 여 컨테이너를 만드는 방법을 보여 줍니다.

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>권한 부여 매개 변수에 대 한 환경 변수 설정

환경 변수에서 권한 부여 매개 변수를 지정 하 여 Azure Storage 데이터 작업에 대 한 모든 호출에 포함 되지 않도록 할 수 있습니다. 다음 표에서는 사용 가능한 환경 변수에 대해 설명 합니다.

| 환경 변수                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    스토리지 계정 이름입니다. 이 변수는 저장소 계정 키 또는 SAS 토큰과 함께 사용 해야 합니다. 둘 다 없는 경우 Azure CLI는 인증 된 Azure AD 계정을 사용 하 여 저장소 계정 액세스 키를 검색 하려고 시도 합니다. 한 번에 많은 수의 명령이 실행 되는 경우 Azure Storage 리소스 공급자 제한에 도달할 수 있습니다. 리소스 공급자 제한에 대 한 자세한 내용은 [Azure Storage 리소스 공급자에 대 한 확장성 및 성능 목표](scalability-targets-resource-provider.md)를 참조 하세요.             |
|    AZURE_STORAGE_KEY                  |    스토리지 계정 키입니다. 이 변수는 저장소 계정 이름과 함께 사용 해야 합니다.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    저장소 계정 키 또는 SAS 토큰을 포함 하는 연결 문자열입니다. 이 변수는 저장소 계정 이름과 함께 사용 해야 합니다.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    SAS (공유 액세스 서명) 토큰입니다. 이 변수는 저장소 계정 이름과 함께 사용 해야 합니다.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    명령을 실행할 때 사용 되는 권한 부여 모드입니다. 허용 되는 값은 `login` (권장) 또는 `key` 입니다. 을 지정 하 `login` 는 경우 Azure CLI는 AZURE AD 자격 증명을 사용 하 여 데이터 작업에 권한을 부여 합니다. 레거시 모드를 지정 하는 경우 `key` Azure CLI는 계정 액세스 키에 대 한 쿼리를 시도 하 고 키를 사용 하 여 명령에 권한을 부여 합니다.    |

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용 하 여 blob 및 큐 데이터에 액세스 하기 위한 Azure 역할을 할당 합니다.](storage-auth-aad-rbac-cli.md)
- [Azure 리소스에 대 한 관리 id를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한 부여](storage-auth-aad-msi.md)
