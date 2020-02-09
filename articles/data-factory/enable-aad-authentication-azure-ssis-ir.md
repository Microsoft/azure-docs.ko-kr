---
title: Azure SSIS Integration Runtime에 AAD 사용
description: 이 문서에서는 Azure Data Factory에 대해 관리 ID와 함께 Azure Active Directory 인증을 사용하도록 설정하여 Azure-SSIS Integration Runtime을 만드는 방법을 설명합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 70367a38fbf7b59486e2eaaf6c05634aa7575869
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927436"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime을 위한 Azure Active Directory 인증 활성화

이 문서에서는 Azure Data Factory (ADF)에 대해 관리 id를 사용 하 여 Azure Active Directory (Azure AD) 인증을 사용 하도록 설정 하 고 기본 인증 방법 (예: SQL 인증) 대신 사용 하 여 다음을 수행 하는 방법을 보여 줍니다.

- 사용자를 대신 하 여 Azure SQL Database 서버/Managed Instance에서 SSISDB (SSIS 카탈로그 데이터베이스)를 프로 비전 하는 IR (Azure-SSIS Integration Runtime)를 만듭니다.

- Azure-SSIS IR에서 SSIS 패키지를 실행할 때 다양 한 Azure 리소스에 연결 합니다.

ADF의 관리 되는 id에 대한 자세한 내용은 [Data Factory 관리 되는 identiy](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)을 참조 하세요.

> [!NOTE]
>-  이 시나리오에서 ADF에 대한 관리 id를 사용 하는 Azure AD 인증은 SSISDB를 프로 비전 하 고 연결 하는 SSIS IR의 생성 및 후속 시작 작업에만 사용 됩니다. SSIS 패키지 실행의 경우, SSIS IR은 SSISDB 프로 비전 중에 생성 되는 완전히 관리 되는 계정으로 SQL 인증을 사용 하 여 SSISDB에 계속 연결 됩니다.
>-  SQL 인증을 사용 하 여 SSIS IR을 이미 만든 경우에는 PowerShell을 통해 Azure AD 인증을 사용 하도록 다시 구성할 수 없지만 Azure Portal/ADF 앱을 통해 수행할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database에서 Azure AD 활성화

Azure SQL Database 서버는 Azure AD 사용자로 데이터베이스 만들기를 지원합니다. 먼저 구성원으로 ADF에 대한 관리 ID를 사용하여 Azure AD 그룹을 만들어야 합니다. 다음으로, Azure SQL Database 서버에 대한 Active Directory 관리자로 Azure AD 사용자를 설정한 다음, SSMS(SQL Server Management Studio)에서 해당 사용자를 사용하여 연결합니다. 마지막으로, Azure-SSIS IR에서 ADF에 대한 관리 ID를 사용하여 사용자 대신 SSISDB를 만들도록 Azure AD 그룹을 나타내는 포함된 사용자를 만들어야 합니다.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>구성원으로 ADF에 대한 관리 ID를 사용하여 Azure AD 그룹을 만듭니다.

기존 Azure AD 그룹을 사용하거나 Azure AD PowerShell을 사용하여 새로 만들 수 있습니다.

1.  [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 모듈을 설치합니다.

2.   `Connect-AzureAD`를 사용하여 로그인하고, 다음 cmdlet을 실행하여 그룹을 만들고, 변수에 저장합니다.

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    결과는 다음 예제와 같으며 변수 값을 표시합니다.

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  ADF의 관리 ID를 그룹에 추가합니다. [Identiy에 대한 관리 되는 Data Factory에 대한](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) 문서를 따라 주 관리 ID 개체 id (예: 765ad4ab-xxxx-XXXX-xxxx-51ed985819dc)를 가져올 수 있지만이 목적을 위해 관리 되는 Id 응용 프로그램 id를 사용 하지는 않습니다.

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    또한 나중에 그룹 구성원을 검사할 수도 있습니다.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure SQL Database 서버를 위한 Azure AD 인증 구성

다음 단계를 사용하여  [SQL을 통해 Azure AD 인증을 구성하고 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)할 수 있습니다.

1.  Azure Portal의 왼쪽 탐색 영역에서 **모든 서비스** -> **SQL 서버**를 선택합니다.

2.  Azure AD 인증을 사용하여 구성할 Azure SQL Database 서버를 선택합니다.

3.  블레이드의 **설정** 섹션에서 **Active Directory 관리자**를 선택합니다.

4.  명령 모음에서 **관리자 설정**을 선택합니다.

5.  서버의 관리자가 될 Azure AD 사용자 계정을 선택 하 고 선택을 선택 **합니다.**

6.  명령 모음에서 **저장**을 선택합니다.

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Azure AD 그룹을 나타내는 Azure SQL Database 서버에 포함된 사용자 만들기

이 다음 단계를 위해 [Microsoft SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1. SSMS를 시작합니다.

2. 서버 **에 연결** 대화 상자의 **서버 이름** 필드에 Azure SQL Database 서버 이름을 입력 합니다.

3. **인증** 필드에서 **MFA를 지 원하는 Active Directory-유니버설** 을 선택 합니다. 다른 두 Active Directory 인증 유형을 사용할 수도 있습니다. SQL에서 [Azure AD 인증 구성 및 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)를 참조 하세요.

4. **사용자 이름** 필드에 서버 관리자로 설정한 Azure AD 계정의 이름을 입력 합니다 (예: testuser@xxxonline.com).

5. **연결** 을 선택 하 고 로그인 프로세스를 완료 합니다.

6. **개체 탐색기**에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

7. **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

8. 쿼리 창에서 다음 T-sql 명령을 입력 하 고 도구 모음에서 **실행** 을 선택 합니다.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

9. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행**을 선택합니다.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   명령이 성공적으로 완료되면 포함된 사용자에게 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

10. SQL 인증을 사용 하 여 SSISDB를 만든 경우 Azure-SSIS IR에 Azure AD 인증을 사용 하도록 전환 하려면 **ssisdb** 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새 쿼리**를 선택 합니다.

11. 쿼리 창에서 다음 T-sql 명령을 입력 하 고 도구 모음에서 **실행** 을 선택 합니다.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

12. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행**을 선택합니다.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    명령이 성공적으로 완료되면 포함된 사용자에게 SSISDB에 액세스할 수 있는 기능이 부여됩니다.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에서 Azure AD 활성화

Azure SQL Database Managed Instance는 직접 ADF에 대한 관리 ID로 데이터베이스 만들기를 지원합니다. Azure AD 그룹에 ADF에 대한 관리 ID를 조인하거나 Managed Instance에서 해당 그룹을 나타내는 포함된 사용자를 만들 필요는 없습니다.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance를 위한 Azure AD 인증 구성

[Managed Instance에 대한 Azure Active Directory 관리자 프로 비전](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)의 단계를 수행 합니다.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>ADF에 대한 관리 ID를 Azure SQL Database Managed Instance의 사용자로 추가

이 다음 단계를 위해 [Microsoft SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1.  SSMS를 시작합니다.

2.  **Sysadmin**인 SQL Server 계정을 사용 하 여 Managed Instance에 연결 합니다. Azure SQL Database Managed Instance에 대한 Azure AD 서버 보안 주체 (로그인)가 GA 되 면 제거 되는 일시적인 제한입니다. Azure AD 관리자 계정을 사용 하 여 로그인을 만드는 경우 메시지 15247, 수준 16, 상태 1, 줄 1 사용자에 게이 작업을 수행할 수 있는 권한이 없는 경우 다음과 같은 오류가 표시 됩니다.

3.  **개체 탐색기**에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

4.  **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.

5.  쿼리 창에서 다음 T-sql 스크립트를 실행 하 여 ADF에 대한 관리 되는 id를 사용자로 추가 합니다.

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    명령이 성공적으로 완료되면 ADF에 대한 관리 ID에 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

6.  SQL 인증을 사용 하 여 SSISDB를 만든 경우 Azure-SSIS IR에 Azure AD 인증을 사용 하도록 전환 하려면 **ssisdb** 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **새 쿼리**를 선택 합니다.

7.  쿼리 창에서 다음 T-sql 명령을 입력 하 고 도구 모음에서 **실행** 을 선택 합니다.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    명령이 성공적으로 완료되면 ADF에 대한 관리 ID에 SSISDB에 액세스할 있는 기능이 부여됩니다.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF 앱에서 Azure-SSIS IR 프로비전

Azure Portal/ADF 앱에서 Azure-SSIS IR을 프로비전하는 경우 **SQL 설정** 페이지에서 **ADF에 대한 관리 ID와 함께 AAD 인증 사용** 옵션을 선택합니다. 다음 스크린샷은 SSISDB를 호스트하는 Azure SQL Database 서버가 포함된 IR에 대한 설정을 표시합니다. SSISDB를 호스트하는 Managed Instance를 사용한 IR의 경우 다른 설정은 동일하나, **카탈로그 데이터베이스 서비스 계층** 및 **Azure 서비스 액세스 허용** 설정은 적용할 수 없습니다.

Azure-SSIS IR을 만드는 방법에 대한 자세한 정보는 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

![Azure-SSIS 통합 런타임에 대한 설정](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell을 사용하여 Azure-SSIS IR 프로비전

PowerShell을 사용하여 Azure-SSIS IR을 프로비전하려면 다음 작업을 수행합니다.

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  모듈을 설치합니다.

2.  스크립트에서 `CatalogAdminCredential` 매개 변수를 설정하지 마세요. 다음은 그 예입니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>관리 Id 인증을 사용 하 여 SSIS 패키지 실행

Azure-SSIS IR에서 SSIS 패키지를 실행 하는 경우 관리 id 인증을 사용 하 여 다양 한 Azure 리소스에 연결할 수 있습니다. 현재는 다음 연결 관리자에서 관리 되는 id 인증을 이미 지원 합니다.

- [OLE DB 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage 연결 관리자](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
