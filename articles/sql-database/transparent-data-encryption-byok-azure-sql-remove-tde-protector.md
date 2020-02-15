---
title: TDE 보호기 제거-PowerShell
description: BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 데이터 웨어하우스에 대해 손상 가능성이 있는 TDE 보호기에 대처하기 위한 방법 가이드.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: be187e34e3232c0755e2613ffffe0647da70079c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201665"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 제거

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- Azure PowerShell 설치 되 고 실행 중 이어야 합니다.
- 이 방법 가이드에서는 이미 Azure SQL Database나 데이터 웨어하우스에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. 자세한 내용은 [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요.

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

설치 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

* * *

## <a name="overview"></a>개요

이 방법 가이드는 Azure Key Vault에서 고객 관리 키 - BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하는 Azure SQL Database 또는 Data Warehouse에 대해 손상 가능성이 있는 TDE 보호기에 대처하는 방법을 설명합니다. TDE의 BYOK 지원에 대해 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-azure-sql.md)를 참조하세요.

다음 절차는 극단적인 경우 또는 테스트 환경에서만 수행합니다. Azure Key Vault에서 적극적으로 사용되는 TDE 보호기를 삭제하면 **데이터 손실**을 초래할 수 있으므로 방법 가이드를 주의 깊게 검토하세요.

키가 손상되어 서비스나 사용자가 키에 무단 액세스한 것으로 의심될 경우 키를 삭제하는 것이 가장 좋습니다.

TDE 보호기가 Key Vault에서 삭제된 후에는 **서버 아래의 암호화된 데이터베이스에 대한 모든 연결이 차단되고, 이들 데이터베이스는 오프라인 상태가 되어 24시간 이내에 삭제됩니다**. 손상된 키로 암호화된 기존 백업에는 더 이상 액세스할 수 없습니다.

다음 단계는 지정 된 데이터베이스의 가상 로그 파일 (VLF)에서 아직 사용 중인 TDE 보호기 지문을 확인 하는 방법을 간략하게 설명 합니다.
데이터베이스의 현재 TDE 보호기의 지문과 데이터베이스 ID는 다음을 실행 하 여 찾을 수 있습니다.

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

다음 쿼리는 사용 중인 Vlf 및 해당 지문을 반환 합니다. 각 지문이 Azure Key Vault (AKV)의 다른 키를 참조 합니다.

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 **add-azurermsqlserverkeyvaultkey** 은 쿼리에서 사용 되는 Tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 명령 **az sql server key show** 는 쿼리에 사용 된 Tde 보호기의 지문을 제공 하므로 유지할 키와 AKV에서 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용 하지 않는 키만 Azure Key Vault에서 안전 하 게 삭제할 수 있습니다.

* * *

이 방법 가이드에서는 사건 대처 후 원하는 결과에 따라 두 가지 방법을 살펴봅니다.

- Azure SQL 데이터베이스/Data Warehouse에 **액세스할 수 있도록** 유지
- Azure SQL 데이터베이스/Data Warehouse에 **액세스할 수 없도록** 유지

## <a name="to-keep-the-encrypted-resources-accessible"></a>암호화된 리소스를 액세스할 수 있게 하려면

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault에 새 키](/powershell/module/az.keyvault/add-azkeyvaultkey)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

2. [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 및 [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버에 새 키를 추가 하 고 서버의 새 tde 보호기로 업데이트 합니다.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. [AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용 하 여 서버 및 모든 복제본이 새 tde 보호기로 업데이트 되었는지 확인 합니다.

   > [!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에 [새 키의 백업](/powershell/module/az.keyvault/backup-azkeyvaultkey)을 작성합니다.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. [AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault에서 손상 된 키를 삭제 합니다.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 나중에 [AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet을 사용 하 여 Key Vault 키를 복원 하려면 다음을 수행 합니다.

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

명령 참조는 [Azure CLI keyvault](/cli/azure/keyvault/key)를 참조 하세요.

1. [Key Vault에 새 키](/cli/azure/keyvault/key#az-keyvault-key-create)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

2. 서버에 새 키를 추가 하 고 서버의 새 TDE 보호기로 업데이트 합니다.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 서버 및 모든 복제본이 새 TDE 보호기로 업데이트 되었는지 확인 합니다.

   > [!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault에서 새 키의 백업을 수행 합니다.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Key Vault에서 손상 된 키를 삭제 합니다.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 키를 복원 하 여 나중에 Key Vault 합니다.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>암호화된 리소스를 액세스할 수 없도록 하려면

1. 손상 가능성이 있는 키로 암호화된 데이터베이스를 삭제합니다.

   데이터베이스 및 로그 파일은 자동으로 백업되므로 (키를 제공하는 한) 어느 지점에서나 데이터베이스의 시점 복원을 수행할 수 있습니다. 최대 10분의 가장 최근 트랜잭션에 대한 데이터 손실 가능성을 차단하기 위해 활성 TDE 보호기를 삭제하기 전에 데이터베이스를 삭제해야 합니다.

2. Key Vault에서 TDE 보호기의 키 자료를 백업합니다.
3. Key Vault에서 잠재적으로 손상된 키 제거

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)).
- TDE에 대한 BYOK(Bring Your Own Key) 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-azure-sql-configure.md)
