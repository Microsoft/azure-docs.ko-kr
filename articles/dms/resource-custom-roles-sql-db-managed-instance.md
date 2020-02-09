---
title: '사용자 지정 역할: 온라인 SQL Server SQL 관리 되는 인스턴스 마이그레이션'
titleSuffix: Azure Database Migration Service
description: SQL Server에 대한 사용자 지정 역할을 사용 하 여 관리 되는 인스턴스 온라인 마이그레이션을 Azure SQL Database 하는 방법을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: 85c70968eb0f8a5752bbfa338918a8bba2fe7794
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437688"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>SQL Database 관리 되는 인스턴스 온라인 마이그레이션에 SQL Server 하기 위한 사용자 지정 역할

Azure Database Migration Service는 앱 ID를 사용 하 여 Azure 서비스와 상호 작용 합니다. 앱 ID를 사용 하려면 구독 수준의 참가자 역할 (많은 회사 보안 부서에서 허용 하지 않음) 또는 Azure database migration Service에 필요한 특정 사용 권한을 부여 하는 사용자 지정 역할을 만들어야 합니다. Azure Active Directory에는 사용자 지정 역할 2000의 제한이 있으므로 앱 ID에 필요한 모든 사용 권한을 하나 또는 두 개의 사용자 지정 역할로 결합 한 다음 앱 ID에 특정 개체 또는 리소스 그룹에 대한 사용자 지정 역할을 부여 하는 것이 좋습니다 (vs. c level). 사용자 지정 역할 수가 그다지 중요 하지 않은 경우에는 리소스 유형별로 사용자 지정 역할을 분할 하 여 아래에 설명 된 대로 총 세 개의 사용자 지정 역할을 만들 수 있습니다.

역할 정의 json 문자열의 AssignableScopes 섹션에서는 포털의 **역할 할당 추가** UI에 사용 권한이 표시 되는 위치를 제어할 수 있습니다. 추가 역할이 있는 UI를 복잡 하 게 방지 하기 위해 리소스 그룹 또는 리소스 수준에서 역할을 정의 하는 것이 좋습니다. 실제 역할 할당을 수행 하지 않습니다.

## <a name="minimum-number-of-roles"></a>최소 역할 수

현재 리소스 수준에서 하나 및 구독 수준에서 앱 ID에 대한 최소 두 개의 사용자 지정 역할을 만드는 것이 좋습니다.

> [!NOTE]
> 새 SQL Database 관리 되는 인스턴스 코드를 Azure에 배포 하면 최종적으로 마지막 사용자 지정 역할 요구 사항이 제거 될 수 있습니다.

**앱 ID에 대한 사용자 지정 역할**입니다. 이 역할은 *리소스* 또는 *리소스 그룹* 수준에서 마이그레이션을 Azure Database Migration Service 하는 데 필요 합니다. 앱 ID에 대한 자세한 내용은 포털을 [사용 하 여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)문서를 참조 하세요.

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**앱 ID-구독에 대한 사용자 지정 역할**입니다. 이 역할은 *구독* 수준에서 Azure Database Migration Service 마이그레이션을 위해 필요 합니다.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

위의 json은 3 개의 텍스트 파일에 저장 해야 하며, AzureRM, AZ PowerShell cmdlet 또는 Azure CLI를 사용 하 여 **get-azurermroledefinition (AzureRM)** 또는 **AzRoleDefinition (AZ)** 를 사용 하 여 역할을 만들 수 있습니다.

자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)문서를 참조 하세요.

이러한 사용자 지정 역할을 만든 후에는 사용자 및 앱 ID에 역할 할당을 적절 한 리소스 또는 리소스 그룹에 추가 해야 합니다.

* "DMS 역할-앱 ID" 역할은 마이그레이션에 사용 되는 앱 ID에 부여 되어야 하 고 저장소 계정, Azure Database Migration Service 인스턴스 및 SQL Database 관리 되는 인스턴스 리소스 수준 에서도 부여 되어야 합니다.
* 구독 수준에서 앱 ID에 "DMS 역할-앱 ID-Sub" 역할을 부여 해야 합니다. 리소스 또는 리소스 그룹에 대한 부여는 실패 합니다. 이 요구 사항은 코드 업데이트가 배포 될 때까지 일시적입니다.

## <a name="expanded-number-of-roles"></a>확장 된 역할 수

Azure Active Directory의 사용자 지정 역할 수가 별로 중요 하지 않을 경우 총 세 개의 역할을 만드는 것이 좋습니다. 여전히 "DMS 역할-앱 ID – Sub" 역할이 필요 하지만 위의 "DMS 역할-앱 ID" 역할은 리소스 유형별로 두 개의 다른 역할로 분할 됩니다.

**SQL Database 관리 되는 인스턴스의 앱 ID에 대한 사용자 지정 역할**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**저장소에 대한 앱 ID에 대한 사용자 지정 역할**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>역할 할당

사용자/앱 ID에 역할을 할당 하려면 Azure Portal을 열고 다음 단계를 수행 합니다.

1. 리소스 그룹 또는 리소스 (구독에 부여 해야 하는 역할 제외)로 이동 하 고 **Access Control**로 이동한 다음 스크롤하여 방금 만든 사용자 지정 역할을 찾습니다.

2. 적절 한 역할을 선택 하 고 앱 ID를 선택한 다음 변경 내용을 저장 합니다.

  이제 앱 ID가 **역할 할당** 탭에 나열 됩니다.

## <a name="next-steps"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 시나리오에 대한 마이그레이션 지침을 검토 합니다.
