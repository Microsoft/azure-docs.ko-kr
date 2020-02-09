---
title: Azure Cosmos DB를 사용 하 여 데이터 작업에 대한 사용자 액세스 제한
description: Azure Cosmos DB를 사용 하 여 데이터 작업에 대한 액세스를 제한 하는 방법을 알아봅니다.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980375"
---
# <a name="restrict-user-access-to-data-operations-only"></a>데이터 작업에 대한 사용자 액세스 제한

Azure Cosmos DB에서는 데이터베이스 서비스와의 상호 작용을 인증 하는 두 가지 방법이 있습니다.
- Azure Portal와 상호 작용할 때 Azure Active Directory id 사용
- Api 및 Sdk에서 호출을 실행 하는 경우 Azure Cosmos DB [키](secure-access-to-data.md#master-keys) 또는 [리소스 토큰](secure-access-to-data.md#resource-tokens) 을 사용 합니다.

각 인증 방법에서는 여러 작업 집합에 대한 액세스를 제공 하며, 이러한 작업에는 인증 유형별 작업 분할 ![있습니다](./media/how-to-restrict-user-data/operations.png)

일부 시나리오에서는 조직의 일부 사용자가 데이터 작업 (CRUD 요청 및 쿼리)만 수행 하도록 제한할 수 있습니다. 이는 일반적으로 리소스를 만들거나 삭제할 필요가 없는 개발자 또는 작업 중인 컨테이너의 프로 비전 된 처리량을 변경 하는 경우에 해당 합니다.

다음 단계를 적용 하 여 액세스를 제한할 수 있습니다.
1. 액세스를 제한 하려는 사용자에 대한 사용자 지정 Azure Active Directory 역할을 만듭니다. 사용자 지정 Active Directory 역할은 Azure Cosmos DB의 [세분화 된 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)을 사용 하 여 작업에 대한 세분화 된 액세스 수준을 가져야 합니다.
1. 키를 사용 하 여 비 데이터 작업의 실행을 허용 하지 않습니다. 이러한 작업은 호출만 Azure Resource Manager 하도록 제한 하 여 달성할 수 있습니다.

이 문서의 다음 섹션에서는 이러한 단계를 수행 하는 방법을 보여 줍니다.

> [!NOTE]
> 다음 섹션에서 명령을 실행 하려면 수정 하려는 구독에 대한 [Azure 소유자 역할](../role-based-access-control/built-in-roles.md#owner) 뿐만 아니라 Azure PowerShell Module 3.0.0 이상을 설치 해야 합니다.

다음 섹션의 PowerShell 스크립트에서 다음 자리 표시자를 사용자 환경에 해당 하는 값으로 대체 합니다.
- `$MySubscriptionId`-사용 권한을 제한 하려는 Azure Cosmos 계정을 포함 하는 구독 ID입니다. Például: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`-Azure Cosmos 계정이 포함 된 리소스 그룹입니다. Például: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`-Azure Cosmos 계정의 이름입니다. Például: `mycosmosdbsaccount`.
- `$MyUserName`-액세스를 제한 하려는 사용자의 로그인 (username@domain)입니다. Például: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését

Azure PowerShell 명령을 실행 하려면 로그인 하 고 구독을 선택 하 여 명령을 실행 해야 합니다.

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>사용자 지정 Azure Active Directory 역할 만들기

다음 스크립트는 Azure Cosmos 계정에 대한 "키 전용" 액세스 권한이 있는 Azure Active Directory 역할 할당을 만듭니다. 이 역할은 [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md) 및 [Azure Cosmos DB에 대한 세부적인 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)을 기반으로 합니다. 이러한 역할 및 작업은 `Microsoft.DocumentDB` Azure Active Directory 네임 스페이스의 일부입니다.

1. 먼저 다음과 같은 내용이 포함 된 `AzureCosmosKeyOnlyAccess.json` JSON 문서를 만듭니다.

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 다음 명령을 실행 하 여 역할 할당을 만들고 사용자에 게 할당 합니다.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>비 데이터 작업 실행 허용 안 함

다음 명령은 키를 사용 하 여 다음을 수행 하는 기능을 제거 합니다.
- 리소스 만들기, 수정 또는 삭제
- 컨테이너 설정 (인덱싱 정책, 처리량 등 포함)을 업데이트 합니다.

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Következő lépések

- [Cosmos DB의 역할 기반 access control](role-based-access-control.md) 에 대한 자세한 정보
- [Cosmos DB 데이터에 대한 보안 액세스](secure-access-to-data.md) 개요 보기
