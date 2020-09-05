---
title: Azure SQL을 사용하는 Azure Active Directory 서비스 주체
description: Azure AD 응용 프로그램 (서비스 주체)은 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 Azure AD 사용자 만들기를 지원 합니다.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: d8268ebf89bed6b67919e77576118343b58edb6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516625"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure SQL을 사용하는 Azure Active Directory 서비스 주체

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure ad 응용 프로그램 (서비스 사용자)을 대신 하 여 Azure SQL Database (SQL DB) 및 azure [Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 에서 Azure Active Directory (azure ad) 사용자 생성은 현재 **공개 미리 보기로**제공 됩니다.

> [!NOTE]
> 이 기능은 SQL Managed Instance에 대해 이미 지원 됩니다.

## <a name="service-principal-azure-ad-applications-support"></a>서비스 사용자 (Azure AD 응용 프로그램) 지원

이 문서는 azure AD와 통합 된 응용 프로그램에 적용 되며 Azure AD 등록의 일부입니다. 이러한 응용 프로그램에는 다양 한 작업을 수행 하기 위해 Azure SQL에 대 한 인증 및 권한 부여 액세스가 필요 합니다. 이제 **공개 미리 보기** 의이 기능을 사용 하 여 서비스 사용자가 SQL Database 및 azure Synapse에서 azure AD 사용자를 만들 수 있습니다. 제거 된 Azure AD 응용 프로그램을 대신 하 여 Azure AD 개체 생성을 방지 하는 제한 사항이 있었습니다.

Azure Portal 또는 PowerShell 명령을 사용 하 여 Azure AD 응용 프로그램을 등록 하면 Azure AD 테 넌 트에서 두 개체가 만들어집니다.

- 애플리케이션 개체
- 서비스 주체 개체

Azure AD 응용 프로그램에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md) 및 [Azure PowerShell를 사용 하 여 Azure 서비스 주체 만들기](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0)를 참조 하세요.

SQL Database, Azure Synapse 및 SQL Managed Instance는 다음과 같은 Azure AD 개체를 지원 합니다.

- Azure AD 사용자 (관리, 페더레이션 및 게스트)
- Azure AD 그룹 (관리 및 페더레이션)
-  Azure AD 애플리케이션 

`CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER`이제 AZURE AD 응용 프로그램을 대신 하 여 t-sql 명령이 SQL Database 및 Azure Synapse에 대해 지원 됩니다.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>서비스 주체를 사용 하 여 Azure AD 사용자 만들기의 기능

이 기능을 지 원하는 것은 azure ad 개체가 사용자의 상호 작용 없이 SQL Database 및 Azure Synapse에서 생성 및 유지 관리 되는 Azure AD 응용 프로그램 자동화 프로세스에 유용 합니다. 서비스 주체는 그룹 또는 개별 사용자의 일부로 SQL 논리 서버에 대 한 Azure AD 관리자가 될 수 있습니다. 응용 프로그램은 시스템 관리자로 실행 될 때 SQL Database 및 Azure Synapse에서 Azure AD 개체 생성을 자동화할 수 있으며 추가 SQL 권한이 필요 하지 않습니다. 이를 통해 데이터베이스 사용자 생성을 완벽 하 게 자동화할 수 있습니다. 이 기능은 시스템 할당 관리 id 및 사용자 할당 관리 id에 대해서도 지원 됩니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/managed-identities-azure-resources/overview.md) 을 참조 하세요.

## <a name="enable-service-principals-to-create-azure-ad-users"></a>서비스 주체를 사용 하 여 Azure AD 사용자 만들기

Azure ad 응용 프로그램을 대신 하 여 SQL Database 및 Azure Synapse에서 Azure AD 개체 만들기를 사용 하도록 설정 하려면 다음 설정이 필요 합니다.

1. 서버 id 할당
    - 새 Azure SQL 논리 서버에 대해 다음 PowerShell 명령을 실행 합니다.
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    자세한 내용은 [AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 명령을 참조 하세요.

    - 기존 Azure SQL 논리 서버에 대해 다음 명령을 실행 합니다.
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    자세한 내용은 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 명령을 참조하세요.

    - 서버 id가 서버에 할당 되었는지 확인 하려면 AzSqlServer 명령을 실행 합니다.

    > [!NOTE]
    > CLI 명령을 사용 하 여 서버 id를 할당할 수도 있습니다. 자세한 내용은 [az sql server create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 및 [az sql server update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update)를 참조 하십시오.

2. 서버에 생성 되거나 할당 된 서버 id에 대 한 Azure AD [**디렉터리 판독기**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 권한을 부여 합니다.
    - 이 권한을 부여 하려면 [AZURE AD 관리자 프로 비전 (sql Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance) 문서에서 사용할 수 있는 sql Managed Instance에 사용 된 설명을 따르세요.
    - 이 사용 권한을 부여 하는 Azure AD 사용자는 Azure AD **전역 관리자** 또는 **권한 있는 역할 관리자** 역할의 일부 여야 합니다.

> [!IMPORTANT]
> 1 단계와 2 단계는 위의 순서 대로 실행 해야 합니다. 먼저 서버 id를 만들거나 할당 한 다음 [**디렉터리 판독기**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 권한을 부여 합니다. 이러한 단계 중 하나 또는 둘 다 생략 하면 azure AD 응용 프로그램을 대신 하 여 azure SQL에서 azure AD 개체를 만드는 동안 실행 오류가 발생 합니다. Azure ad 응용 프로그램을 대신 하 여 Azure AD 사용자를 만드는 단계별 지침은 [자습서: azure ad 응용 프로그램을 사용 하 여 azure](authentication-aad-service-principal-tutorial.md)Ad 사용자 만들기를 참조 하세요.
>
> **공개 미리 보기**에서 Azure AD의 그룹에 **디렉터리 독자** 역할을 할당할 수 있습니다. 그런 다음 그룹 소유자는이 그룹의 구성원으로 관리 되는 id를 추가할 수 있습니다 .이 경우 **전역 관리자** 또는 권한 있는 **역할 관리자가** **디렉터리 구독자** 역할을 부여할 필요가 없습니다. 이 기능에 대 한 자세한 내용은 [AZURE SQL Azure Active Directory의 디렉터리 판독기 역할](authentication-aad-directory-readers-role.md)을 참조 하세요.

## <a name="troubleshooting-and-limitations-for-public-preview"></a>공개 미리 보기에 대 한 문제 해결 및 제한 사항

- 서버 id를 사용 하도록 설정 하 고 **디렉터리 판독기** 권한을 부여 하지 않고 azure ad 응용 프로그램을 대신 하 여 azure SQL에서 azure ad 개체를 만드는 경우 작업이 실패 하 고 다음과 같은 오류가 발생할 수 있습니다. 아래 예제 오류는 `myapp` [자습서: azure ad 응용 프로그램을 사용 하 여 azure Ad 사용자 만들기](authentication-aad-service-principal-tutorial.md)문서에서 SQL Database 사용자를 만드는 PowerShell 명령 실행에 대 한 것입니다.
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - 위의 오류에 대 한 단계를 수행 하 여 [AZURE sql 논리 서버에 id를 할당](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) 하 고 [sql 논리 서버 Id에 디렉터리 판독기 권한을 할당](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)합니다.
    > [!NOTE]
    > 위에 표시 된 오류 메시지는 Azure AD 응용 프로그램 지원에 대 한 누락 된 설정 요구 사항을 명확 하 게 파악 하기 위해 기능 GA 이전에 변경 될 예정입니다.
- Azure AD 응용 프로그램을 SQL Managed Instance에 대 한 Azure AD 관리자로 설정 하는 것은 CLI 명령을 사용 하 고 [Az. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 이상을 사용 하는 PowerShell 명령을 사용 하는 경우에만 지원 됩니다. 자세한 내용은 [az sql mi ad-admin create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) 및 [AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 명령을 참조 하세요. 
    - SQL Managed Instance에 대 한 Azure Portal를 사용 하 여 Azure AD 관리자를 설정 하려는 경우 Azure AD 그룹을 만드는 것이 좋습니다. 그런 다음 서비스 주체 (Azure AD 응용 프로그램)를이 그룹에 추가 하 고이 그룹을 SQL Managed Instance에 대 한 Azure AD 관리자로 설정 합니다.
    - SQL Database 및 Azure Synapse에 대 한 Azure AD 관리자로 서비스 주체 (Azure AD 응용 프로그램)를 설정 하는 것은 Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)및 [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) 명령을 사용 하 여 지원 됩니다.
- 다른 테 넌 트에서 서비스 사용자와 Azure AD 응용 프로그램을 사용 하면 다른 테 넌 트에서 만든 SQL Database 또는 SQL Managed Instance에 액세스할 수 없게 됩니다. 이 응용 프로그램에 할당 된 서비스 주체는 SQL 논리 서버와 같은 테 넌 트 또는 Managed Instance 이어야 합니다.
- [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 모듈 이상(PowerShell을 사용하여 개별 Azure AD 애플리케이션을 Azure SQL에 대한 Azure AD 관리자로 설정할 때 필요함). 최신 모듈로 업그레이드해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal-tutorial.md)


