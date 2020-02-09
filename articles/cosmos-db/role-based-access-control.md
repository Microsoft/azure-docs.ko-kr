---
title: Azure Cosmos DB의 역할 기반 액세스 제어
description: Azure Cosmos DB RBAC (Active directory 통합)를 사용 하 여 데이터베이스 보호를 제공 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445094"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB의 역할 기반 액세스 제어

Azure Cosmos DB은 Azure Cosmos DB의 일반적인 관리 시나리오에 대한 기본 제공 RBAC (역할 기반 액세스 제어)를 제공 합니다. Azure Active Directory에 프로필이 있는 개인은 이러한 RBAC 역할을 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 할당 하 여 리소스에 대한 액세스를 부여 하거나 거부 하 고 Azure Cosmos DB 리소스에 대한 작업을 수행할 수 있습니다. 역할 할당은 제어 평면 액세스 전용으로 범위가 지정 됩니다. 여기에는 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제안 (처리량)에 대한 액세스가 포함 됩니다.

## <a name="built-in-roles"></a>기본 제공 역할

Azure Cosmos DB에서 지 원하는 기본 제공 역할은 다음과 같습니다.

|**기본 제공 역할**  |**설명**  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다.|
|[Cosmos DB 계정 판독기](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음.|
|[Cosmos Backup 운영자](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Azure Cosmos 데이터베이스 또는 컨테이너에 대한 복원 요청을 제출할 수 있습니다.|
|[Cosmos DB 연산자](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|는 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 프로 비전 할 수 있지만 데이터에 액세스 하는 데 필요한 키에는 액세스할 수 없습니다.|

> [!IMPORTANT]
> Azure Cosmos DB의 RBAC 지원은 제어 평면 작업에만 적용 됩니다. 데이터 평면 작업은 마스터 키 또는 리소스 토큰을 사용 하 여 보호 됩니다. 자세한 내용은 [Azure Cosmos DB의 데이터에 안전](secure-access-to-data.md) 하 게 액세스를 참조 하세요.

## <a name="identity-and-access-management-iam"></a>ID 및 액세스 관리(Identity and Access Managmenet:  IAM)

Azure Portal의 **액세스 제어 (IAM)** 창은 Azure Cosmos 리소스에 대한 역할 기반 액세스 제어를 구성 하는 데 사용 됩니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다. 다음 스크린샷은 Azure Portal의 IAM (access control)을 사용한 RBAC (Active Directory 통합)를 보여 줍니다.

![Azure Portal에서 액세스 제어(IAM) - 데이터베이스 보안 설명](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>사용자 지정 역할

사용자는 기본 제공 역할 외에도 Azure에서 [사용자 지정 역할](../role-based-access-control/custom-roles.md) 을 만들고 해당 Active Directory 테넌트 내의 모든 구독에서 서비스 주체에 이러한 역할을 적용할 수 있습니다. 사용자 지정 역할은 사용자가 리소스 공급자 작업의 사용자 지정 집합을 사용 하 여 RBAC 역할 정의를 만들 수 있는 방법을 제공 합니다. Azure Cosmos DB에 대한 사용자 지정 역할을 빌드하는 데 사용할 수 있는 작업에 대한 자세한 내용은 [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) 을 참조 하세요.

## <a name="preventing-changes-from-cosmos-sdk"></a>Cosmos SDK에서 변경 방지

Cosmos 리소스 공급자는 계정 키 (즉, Cosmos SDK를 통해 연결 하는 응용 프로그램)를 통해 연결 되는 모든 클라이언트의 Cosmos 계정, 데이터베이스, 컨테이너 및 처리량을 포함 하 여 리소스를 변경할 수 없도록 잠글 수 있습니다. 설정 하는 경우 리소스에 대한 변경 내용은 적절 한 RBAC 역할 및 자격 증명이 있는 사용자 로부터 가져와야 합니다. 이 기능은 Cosmos 리소스 공급자의 `disableKeyBasedMetadataWriteAccess` 속성 값으로 설정 됩니다. 이 속성 설정을 사용 하는 Azure Resource Manager 템플릿의 예는 다음과 같습니다.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 RBAC (역할 기반 액세스 제어) 란?](../role-based-access-control/overview.md)
- [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
