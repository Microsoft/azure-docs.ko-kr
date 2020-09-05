---
title: Azure 구독을 다른 Azure AD 디렉터리 (미리 보기)에 전송
description: Azure 구독 및 알려진 관련 리소스를 다른 Azure Active Directory (Azure AD) 디렉터리에 전송 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: rolyon
ms.openlocfilehash: 9873bd8f94c80caccd75033e2a8a4bc2cffcde03
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227035"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory-preview"></a>Azure 구독을 다른 Azure AD 디렉터리 (미리 보기)에 전송

> [!IMPORTANT]
> 다른 Azure AD 디렉터리로 구독을 전송 하는 다음 단계는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

조직에는 여러 Azure 구독이 있을 수 있습니다. 각 구독은 특정 Azure Active Directory (Azure AD) 디렉터리와 연결 됩니다. 관리를 용이 하 게 하려면 다른 Azure AD 디렉터리로 구독을 전송 하는 것이 좋습니다. 다른 Azure AD 디렉터리로 구독을 전송 하는 경우 일부 리소스는 대상 디렉터리에 전송 되지 않습니다. 예를 들어 Azure RBAC (역할 기반 액세스 제어)의 모든 역할 할당 및 사용자 지정 역할은 원본 디렉터리에서 **영구적으로** 삭제 되며 대상 디렉터리로 전송 되지 않습니다.

이 문서에서는 다른 Azure AD 디렉터리로 구독을 전송 하 고 전송 후 일부 리소스를 다시 만들기 위해 수행할 수 있는 기본 단계를 설명 합니다.

> [!NOTE]
> Azure CSP (클라우드 서비스 공급자) 구독의 경우 구독에 대 한 Azure AD 디렉터리를 변경 하는 것은 지원 되지 않습니다.

## <a name="overview"></a>개요

다른 Azure AD 디렉터리에 Azure 구독을 전송 하는 것은 신중 하 게 계획 하 고 실행 해야 하는 복잡 한 프로세스입니다. 대부분의 Azure 서비스에는 보안 주체 (id)가 정상적으로 작동 하거나 다른 Azure 리소스도 관리 해야 합니다. 이 문서에서는 보안 주체에 크게 의존 하지만 포괄적이 지 않는 대부분의 Azure 서비스를 처리 하려고 합니다.

> [!IMPORTANT]
> 일부 시나리오에서는 구독을 전송 하는 동안 가동 중지 시간이 필요할 수 있습니다. 전송에 가동 중지 시간을 요구 하는지 여부를 평가 하려면 신중한 계획이 필요 합니다.

다음 다이어그램은 구독을 다른 디렉터리로 전송할 때 수행 해야 하는 기본 단계를 보여 줍니다.

1. 전송 준비

1. Azure 구독을 다른 디렉터리로 전송

1. 역할 할당, 사용자 지정 역할 및 관리 되는 id와 같은 대상 디렉터리에 리소스를 다시 만듭니다.

    ![구독 다이어그램 전송](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>다른 디렉터리에 구독을 전송할지 여부 결정

다음은 구독을 전송할 수 있는 몇 가지 이유입니다.

- 회사 합병 또는 획득으로 인해 기본 Azure AD 디렉터리에서 획득 한 구독을 관리 하려고 합니다.
- 조직의 누군가가 구독을 만들고 관리를 특정 Azure AD 디렉터리에 통합 하려고 합니다.
- 특정 구독 ID 또는 URL에 종속 된 응용 프로그램이 있으며 응용 프로그램 구성 또는 코드를 수정 하기가 쉽지 않습니다.
- 비즈니스의 일부가 별도의 회사로 분할 되었으며 일부 리소스를 다른 Azure AD 디렉터리로 이동 해야 합니다.
- 보안 격리를 위해 다른 Azure AD 디렉터리에서 일부 리소스를 관리 하려고 합니다.

구독을 전송 하려면 프로세스가 완료 될 때까지 가동 중지 시간이 필요 합니다. 시나리오에 따라 리소스를 다시 만들고 대상 디렉터리 및 구독에 데이터를 복사 하는 것이 더 좋을 수 있습니다.

### <a name="understand-the-impact-of-transferring-a-subscription"></a>구독 전송의 영향 이해

여러 Azure 리소스는 구독 또는 디렉터리에 대 한 종속성이 있습니다. 다음 표에서는 상황에 따라 구독을 전송할 때 알려진 영향을 보여 줍니다. 이 문서의 단계를 수행 하 여 구독 이전에 존재 했던 리소스 중 일부를 다시 만들 수 있습니다.

> [!IMPORTANT]
> 이 섹션에서는 구독에 종속 된 알려진 Azure 서비스 또는 리소스를 나열 합니다. Azure의 리소스 종류가 지속적으로 진화 하기 때문에 환경에 대 한 주요 변경 내용을 일으킬 수 있는 추가 종속성이 여기에 나열 되지 않을 수 있습니다. 

| 서비스 또는 리소스 | 줄 | Recoverable | 영향을 받고 있나요? | 수행 가능한 작업 |
| --------- | --------- | --------- | --------- | --------- |
| 역할 할당 | 예 | 예 | [역할 할당 나열](#save-all-role-assignments) | 모든 역할 할당이 영구적으로 삭제 됩니다. 사용자, 그룹 및 서비스 주체를 대상 디렉터리의 해당 개체에 매핑해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| 사용자 지정 역할 | 예 | 예 | [사용자 지정 역할 나열](#save-custom-roles) | 모든 사용자 지정 역할은 영구적으로 삭제 됩니다. 사용자 지정 역할 및 역할 할당을 다시 만들어야 합니다. |
| 시스템 할당 관리 id | 예 | 예 | [관리 id 나열](#list-role-assignments-for-managed-identities) | 관리 되는 id를 사용 하지 않도록 설정 했다가 다시 사용 하도록 설정 해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| 사용자 할당 관리 id | 예 | 예 | [관리 id 나열](#list-role-assignments-for-managed-identities) | 관리 되는 id를 삭제 하 고 다시 만든 다음 적절 한 리소스에 연결 해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| Azure Key Vault | 예 | 예 | [Key Vault 액세스 정책 나열](#list-key-vaults) | 키 자격 증명 모음과 연결 된 테 넌 트 ID를 업데이트 해야 합니다. 새 액세스 정책을 제거 하 고 추가 해야 합니다. |
| Azure AD 인증 통합이 사용 되는 azure SQL database | 예 | 아니요 | [Azure AD 인증을 사용 하 여 Azure SQL 데이터베이스 확인](#list-azure-sql-databases-with-azure-ad-authentication) |  |  |
| Azure Storage 및 Azure Data Lake Storage Gen2 | 예 | 예 |  | Acl을 다시 만들어야 합니다. |
| Azure Data Lake Storage Gen1 | 예 | 예 |  | Acl을 다시 만들어야 합니다. |
| Azure 파일 | 예 | 예 |  | Acl을 다시 만들어야 합니다. |
| Azure 파일 동기화 | 예 | 예 |  |  |
| Azure Managed Disks | 예 | 해당 없음 |  |  |
| Kubernetes 용 Azure Container Service | 예 | 예 |  |  |
| Azure Active Directory Domain Services | 예 | 아니요 |  |  |
| 앱 등록 | 예 | 예 |  |  |

> [!WARNING]
> 전송 중인 동일한 구독에 **없는** key vault에 대 한 종속성이 있는 리소스 (예: 저장소 계정 또는 SQL 데이터베이스)에 미사용 암호화를 사용 하는 경우 복구할 수 없는 시나리오가 발생할 수 있습니다. 이러한 상황이 발생 하는 경우 다른 키 자격 증명 모음을 사용 하거나 고객이 관리 하는 키를 일시적으로 사용 하지 않도록 설정 하 여이 복구할 수 없는 시나리오를 방지 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료 하려면 다음이 필요 합니다.

- Azure Cloud Shell 또는 [Azure CLI](https://docs.microsoft.com/cli/azure) [의 Bash](/azure/cloud-shell/overview)
- 원본 디렉터리에서 전송 하려는 구독의 계정 관리자
- 대상 디렉터리의 [소유자](built-in-roles.md#owner) 역할

## <a name="step-1-prepare-for-the-transfer"></a>1 단계: 전송 준비

### <a name="sign-in-to-source-directory"></a>원본 디렉터리에 로그인

1. 관리자 권한으로 Azure에 로그인 합니다.

1. [Az account list](/cli/azure/account#az-account-list) 명령을 사용 하 여 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

1. [Az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 을 사용 하 여 전송 하려는 활성 구독을 설정 합니다.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-resource-graph-extension"></a>리소스 그래프 확장을 설치 합니다.

 리소스 그래프 확장을 사용 하면 [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) 명령을 사용 하 여 Azure Resource Manager에서 관리 하는 리소스를 쿼리할 수 있습니다. 이후 단계에서이 명령을 사용 합니다.

1. [Az extension list](https://docs.microsoft.com/cli/azure/extension#az-extension-list) 를 사용 하 여 *리소스 그래프* 확장이 설치 되어 있는지 확인 합니다.

    ```azurecli
    az extension list
    ```

1. 그렇지 않으면 *리소스 그래프* 확장을 설치 합니다.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>모든 역할 할당 저장

1. [Az role 할당 목록을](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-list) 사용 하 여 모든 역할 할당 (상속 된 역할 할당 포함)을 나열 합니다.

    목록을 보다 쉽게 검토할 수 있도록 출력을 JSON, TSV 또는 테이블로 내보낼 수 있습니다. 자세한 내용은 [AZURE RBAC를 사용 하 여 역할 할당 나열 및 Azure CLI](role-assignments-list-cli.md)을 참조 하세요.

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. 역할 할당 목록을 저장 합니다.

    구독을 전송할 때 모든 역할 할당이 **영구적** 으로 삭제 되므로 복사본을 저장 하는 것이 중요 합니다.

1. 역할 할당의 목록을 검토 합니다. 대상 디렉터리에는 역할 할당이 필요 하지 않을 수 있습니다.

### <a name="save-custom-roles"></a>사용자 지정 역할 저장

1. [Az role definition list](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-list) 를 사용 하 여 사용자 지정 역할을 나열 합니다. 자세한 내용은 [Azure CLI를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-cli.md)를 참조 하세요.

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. 대상 디렉터리에 필요한 각 사용자 지정 역할을 별도의 JSON 파일로 저장 합니다.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. 사용자 지정 역할 파일의 복사본을 만듭니다.

1. 다음 형식을 사용 하도록 각 복사본을 수정 합니다.

    이러한 파일을 나중에 사용 하 여 대상 디렉터리에 사용자 지정 역할을 다시 만듭니다.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>사용자, 그룹 및 서비스 사용자 매핑 결정

1. 역할 할당 목록에 따라 대상 디렉터리에서 매핑할 사용자, 그룹 및 서비스 사용자를 결정 합니다.

    각 역할 할당의 속성을 살펴보면 보안 주체의 유형을 식별할 수 있습니다 `principalType` .

1. 필요한 경우 대상 디렉터리에서 필요한 모든 사용자, 그룹 또는 서비스 주체를 만듭니다.

### <a name="list-role-assignments-for-managed-identities"></a>관리 id에 대 한 역할 할당 나열

구독을 다른 디렉터리로 전송할 때 관리 id는 업데이트 되지 않습니다. 따라서 기존 시스템 할당 또는 사용자 할당 관리 id가 중단 됩니다. 전송 후 시스템 할당 관리 되는 id를 다시 사용 하도록 설정할 수 있습니다. 사용자 할당 관리 id의 경우 대상 디렉터리에서 다시 만들고 연결 해야 합니다.

1. 관리 id를 [지 원하는 Azure 서비스 목록을](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 검토 하 여 관리 되는 id를 사용할 수 있는 위치를 확인 합니다.

1. [Az ad sp list](/cli/azure/identity?view=azure-cli-latest#az-identity-list) 를 사용 하 여 시스템 할당 및 사용자 할당 관리 id를 나열 합니다.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. 관리 id 목록에서 시스템 할당 및 사용자가 할당 된 id를 확인 합니다. 다음 조건을 사용 하 여 형식을 결정할 수 있습니다.

    | 조건 | 관리 ID 유형 |
    | --- | --- |
    | `alternativeNames` 속성 포함 `isExplicit=False` | 시스템 할당 |
    | `alternativeNames` 속성에 포함 되지 않는 내용 `isExplicit` | 시스템 할당 |
    | `alternativeNames` 속성 포함 `isExplicit=True` | 사용자 할당 |

    [Az identity list](https://docs.microsoft.com/cli/azure/identity#az-identity-list) 를 사용 하 여 사용자 할당 관리 id만 나열할 수도 있습니다. 자세한 내용은 [Azure CLI를 사용 하 여 사용자 할당 관리 Id 만들기, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 참조 하세요.

    ```azurecli
    az identity list
    ```

1. `objectId`관리 id의 값 목록을 가져옵니다.

1. 역할 할당 목록을 검색 하 여 관리 되는 id에 대 한 역할 할당이 있는지 확인 합니다.

### <a name="list-key-vaults"></a>주요 자격 증명 모음 나열

키 자격 증명 모음을 만들 때 생성된 해당 구독에 대한 기본 Azure Active Directory 테넌트 ID에 자동으로 연결됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. 자세한 내용은 [다른 구독으로 Azure Key Vault 이동](../key-vault/general/move-subscription.md)을 참조 하세요.

> [!WARNING]
> 전송 중인 동일한 구독에 **없는** key vault에 대 한 종속성이 있는 리소스 (예: 저장소 계정 또는 SQL 데이터베이스)에 미사용 암호화를 사용 하는 경우 복구할 수 없는 시나리오가 발생할 수 있습니다. 이러한 상황이 발생 하는 경우 다른 키 자격 증명 모음을 사용 하거나 고객이 관리 하는 키를 일시적으로 사용 하지 않도록 설정 하 여이 복구할 수 없는 시나리오를 방지 해야 합니다.

- 키 자격 증명 모음을 사용 하는 경우 [az keyvault show](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-show) 를 사용 하 여 액세스 정책을 나열 합니다. 자세한 내용은 [액세스 제어 정책을 사용 하 여 Key Vault 인증 제공](../key-vault/key-vault-group-permissions-for-apps.md)을 참조 하세요.

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure AD 인증을 사용 하 여 Azure SQL 데이터베이스 나열

- [Az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) 및 [az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) extension을 사용 하 여 azure ad 인증 통합이 사용 하도록 설정 된 azure sql database를 사용 하 고 있는지 확인 합니다. 자세한 내용은 [SQL을 사용 하 여 Azure Active Directory 인증 구성 및 관리](../azure-sql/database/authentication-aad-configure.md)를 참조 하세요.

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Acl 나열

1. Azure Data Lake Storage Gen1를 사용 하는 경우 Azure Portal 또는 PowerShell을 사용 하 여 모든 파일에 적용 되는 Acl을 나열 합니다.

1. Azure Data Lake Storage Gen2를 사용 하는 경우 Azure Portal 또는 PowerShell을 사용 하 여 모든 파일에 적용 되는 Acl을 나열 합니다.

1. Azure Files를 사용 하는 경우 모든 파일에 적용 되는 Acl을 나열 합니다.

### <a name="list-other-known-resources"></a>기타 알려진 리소스 나열

1. [Az account show](https://docs.microsoft.com/cli/azure/account#az-account-show) 를 사용 하 여 구독 ID를 가져옵니다.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. [Az graph](https://docs.microsoft.com/cli/azure/ext/resource-graph/graph) extension을 사용 하 여 알려진 azure AD 디렉터리 종속성이 있는 다른 azure 리소스를 나열 합니다.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>2 단계: 구독 전송

이 단계에서는 구독을 원본 디렉터리에서 대상 디렉터리로 전송 합니다. 이러한 단계는 청구 소유권을 전송할지 여부에 따라 달라 집니다.

> [!WARNING]
> 구독을 전송 하면 원본 디렉터리의 모든 역할 할당이 **영구적으로** 삭제 되며 복원할 수 없습니다. 구독을 전송한 후에는 다시 이동할 수 없습니다. 이 단계를 수행 하기 전에 이전 단계를 완료 해야 합니다.

1. 또한 청구 소유권을 다른 계정으로 전송할지 여부를 결정 합니다.

1. 다른 디렉터리로 구독을 전송 합니다.

    - 현재 청구 소유권을 유지 하려면 [Azure Active Directory 테 넌 트에 Azure 구독 연결 또는 추가](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)의 단계를 따르세요.
    - 또한 청구 소유권을 양도 하려면 [Azure 구독의 청구 소유권을 다른 계정에 양도](../cost-management-billing/manage/billing-subscription-transfer.md)의 단계를 따르세요. 다른 디렉터리로 구독을 전송 하려면 **AZURE AD 테 넌 트 구독** 확인란을 선택 해야 합니다.

1. 구독 전송이 완료 되 면이 문서로 돌아와서 대상 디렉터리에 리소스를 다시 만듭니다.

## <a name="step-3-re-create-resources"></a>3 단계: 리소스 다시 만들기

### <a name="sign-in-to-target-directory"></a>대상 디렉터리에 로그인

1. 대상 디렉터리에서 전송 요청을 수락한 사용자로 로그인 합니다.

    전송 요청을 수락한 새 계정의 사용자 에게만 리소스를 관리할 수 있는 권한이 있습니다.

1. [Az account list](https://docs.microsoft.com/cli/azure/account#az-account-list) 명령을 사용 하 여 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

1. [Az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 를 사용 하 여 사용 하려는 활성 구독을 설정 합니다.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>사용자 지정 역할 만들기
        
- [Az role definition create](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create) 를 사용 하 여 이전에 만든 파일에서 각 사용자 지정 역할을 만듭니다. 자세한 내용은 [Azure CLI를 사용 하 여 Azure 사용자 지정 역할 만들기 또는 업데이트](custom-roles-cli.md)를 참조 하세요.

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="create-role-assignments"></a>역할 할당 만들기

- [Az role 할당 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 를 사용 하 여 사용자, 그룹 및 서비스 사용자에 대 한 역할 할당을 만듭니다. 자세한 내용은 [AZURE RBAC 및 Azure CLI를 사용 하 여 역할 할당 추가 또는 제거](role-assignments-cli.md)를 참조 하세요.

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>시스템 할당 관리 id 업데이트

1. 시스템 할당 관리 id를 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 합니다.

    | Azure 서비스 | 추가 정보 | 
    | --- | --- |
    | 가상 머신 | [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 가상 머신 크기 집합 | [Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | 기타 서비스 | [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. [Az role 할당 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 를 사용 하 여 시스템 할당 관리 id에 대 한 역할 할당을 만듭니다. 자세한 내용은 [Azure CLI를 사용 하 여 리소스에 관리 id 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)을 참조 하세요.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>사용자 할당 관리 id 업데이트

1. 사용자 할당 관리 id를 삭제 하 고, 다시 만들고, 연결 합니다.

    | Azure 서비스 | 추가 정보 | 
    | --- | --- |
    | 가상 머신 | [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 가상 머신 크기 집합 | [Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | 기타 서비스 | [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Azure CLI를 사용하여 사용자 할당 관리 ID 생성, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. [Az role 할당 create](https://docs.microsoft.com/cli/azure/role/assignment#az-role-assignment-create) 를 사용 하 여 사용자 할당 관리 id에 대 한 역할 할당을 만듭니다. 자세한 내용은 [Azure CLI를 사용 하 여 리소스에 관리 id 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)을 참조 하세요.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>주요 자격 증명 모음 업데이트

이 섹션에서는 주요 자격 증명 모음을 업데이트 하는 기본 단계에 대해 설명 합니다. 자세한 내용은 [다른 구독으로 Azure Key Vault 이동](../key-vault/general/move-subscription.md)을 참조 하세요.

1. 구독의 모든 기존 키 자격 증명 모음과 연결 된 테 넌 트 ID를 대상 디렉터리에 업데이트 합니다.

1. 모든 기존 액세스 정책 항목을 제거합니다.

1. 대상 디렉터리와 연결 된 새 액세스 정책 항목을 추가 합니다.

### <a name="update-acls"></a>Acl 업데이트

1. Azure Data Lake Storage Gen1를 사용 하는 경우 적절 한 Acl을 할당 합니다. 자세한 내용은 [Azure Data Lake Storage Gen1에 저장된 데이터 보안](../data-lake-store/data-lake-store-secure-data.md)을 참조하세요.

1. Azure Data Lake Storage Gen2를 사용 하는 경우 적절 한 Acl을 할당 합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../storage/blobs/data-lake-storage-access-control.md)를 참조하세요.

1. Azure Files를 사용 하는 경우 적절 한 Acl을 할당 합니다.

### <a name="review-other-security-methods"></a>다른 보안 방법 검토

전송 중에 역할 할당이 제거 되더라도 원래 소유자 계정의 사용자는 다음을 비롯 한 다른 보안 방법을 통해 구독에 계속 액세스할 수 있습니다.

- 스토리지와 같은 서비스에 대한 액세스 키.
- 사용자 관리자에 게 구독 리소스에 대 한 액세스 권한을 부여 하는 [관리 인증서](../cloud-services/cloud-services-certs-create.md) 입니다.
- Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

대상 디렉터리에서 액세스할 수 없도록 원본 디렉터리의 사용자에 대 한 액세스 권한을 제거 하려면 자격 증명을 회전 하는 것을 고려해 야 합니다. 자격 증명을 업데이트 하기 전 까지는 사용자가 전송 후에도 계속 액세스할 수 있습니다.

1. 저장소 계정 액세스 키를 회전 합니다. 자세한 내용은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조하세요.

1. Azure SQL Database 또는 Azure Service Bus 메시징과 같은 다른 서비스에 대 한 액세스 키를 사용 하는 경우 액세스 키를 회전 합니다.

1. 비밀을 사용 하는 리소스의 경우 리소스에 대 한 설정을 열고 암호를 업데이트 합니다.

1. 인증서를 사용 하는 리소스의 경우 인증서를 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 청구 소유권을 다른 계정에 양도](../cost-management-billing/manage/billing-subscription-transfer.md)
- [구독자와 CSP 간에 Azure 구독 전송](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
