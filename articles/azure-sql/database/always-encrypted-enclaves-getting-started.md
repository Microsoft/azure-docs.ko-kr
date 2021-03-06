---
title: '자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작'
description: 이 자습서에서는 Azure SQL Database에서 보안 enclave를 사용한 Always Encrypted에 대한 기본 환경을 만드는 방법과 현재 위치에서 데이터를 암호화하고, SSMS(SQL Server Management Studio)를 사용하여 암호화된 열에 대해 풍부한 기밀 쿼리를 실행하는 방법을 설명합니다.
keywords: 데이터 암호화, SQL 암호화, 데이터베이스 암호화, 중요한 데이터, Always Encrypted, 보안 enclave, SGX, 증명
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120949"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database용 보안 enclave를 사용한 Always Encrypted는 현재 **공개 미리 보기** 상태입니다.

이 자습서는 Azure SQL Database에서 [보안 enclave를 사용한 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)를 시작하는 방법을 설명합니다. 다음이 설명됩니다.

> [!div class="checklist"]
> - 보안 Enclave를 사용한 Always Encrypted를 테스트 및 평가하기 위한 환경을 만드는 방법
> - SSMS(SQL Server Management Studio)를 사용하여 암호화된 열에 대해 데이터를 바로 암호화하고 풍부한 기밀 쿼리를 실행하는 방법.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 Azure PowerShell 및 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

### <a name="powershell-requirements"></a>PowerShell 요구 사항

Azure PowerShell을 설치하고 실행하는 방법에 대한 자세한 내용은 [Azure PowerShell 개요](/powershell/azure)를 참조하세요. 

증명 작업을 지원하는 데 필요한 Az 모듈의 최소 버전은 다음과 같습니다.

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

아래 명령을 실행하여 모든 Az 모듈의 설치된 버전을 확인합니다.

```powershell
Get-InstalledModule
```

버전이 최소 요구 사항과 일치하지 않으면 `Update-Module` 명령을 실행합니다.

PowerShell 갤러리에는 더 이상 사용되지 않는 TLS(전송 계층 보안) 버전 1.0 및 1.1이 있습니다. TLS 버전 1.2 이상을 사용하는 것이 좋습니다. 1\.2보다 낮은 TLS 버전을 사용하는 경우 다음과 같은 오류가 나타날 수 있습니다.

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

PowerShell 갤러리와 계속 상호 작용하려면 Install-Module 명령을 실행하기 전에 먼저 다음 명령을 실행합니다.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS 요구 사항

SSMS를 다운로드하는 방법에 대한 자세한 내용은 [SSMS(SQL Server Management Studio) 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)를 참조하세요.

필요한 SSMS의 최소 버전은 18.8입니다.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>1단계: 서버 및 DC 시리즈 데이터베이스 만들기 및 구성

이 단계에서는 보안 enclave를 사용한 Always Encrypted에 필요한 DC 시리즈 하드웨어 생성을 사용하여 새 Azure SQL Database 논리 서버와 새 데이터베이스를 만듭니다. 자세한 내용은 [DC 시리즈](service-tiers-vcore.md#dc-series)를 참조하세요.

1. PowerShell 콘솔을 열고 Az의 필수 버전을 가져옵니다.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Azure에 로그인합니다. 필요한 경우 이 자습서에서 사용하는 [구독으로 전환](/powershell/azure/manage-subscriptions-azureps)합니다.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. 새 리소스 그룹 만들기 

  > [!IMPORTANT]
  > DC 시리즈 하드웨어 생성과 Microsoft Azure Attestation을 모두 지원하는 지역(위치)에 리소스 그룹을 만들어야 합니다. DC 시리즈를 지원하는 지역 목록은 [DC 시리즈 가용성](service-tiers-vcore.md#dc-series-1)을 참조하세요. Microsoft Azure Attestation의 지역별 가용성은 [다음](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation)과 같습니다.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Azure SQL 논리 서버를 만듭니다. 메시지가 표시되면 서버 관리자 이름과 암호를 입력합니다. 관리자 이름과 암호를 기억하고 있는지 확인합니다. 나중에 서버에 연결하는 데 필요합니다.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. 지정된 IP 범위에서 액세스를 허용하는 서버 방화벽 규칙을 만듭니다.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. 서버에 관리 시스템 ID를 할당합니다. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. DC 시리즈 데이터베이스를 만듭니다.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. 서버와 데이터베이스에 대한 정보를 검색하고 저장합니다. 이 정보를 비롯하여 이 섹션의 4 단계에 있는 관리자 이름 및 암호는 이후 섹션에서 필요합니다.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>2단계: 증명 공급자 구성 

이 단계에서는 Microsoft Azure Attestation에서 증명 공급자를 만들고 구성합니다. 이는 데이터베이스가 사용하는 보안 enclave를 증명하는 데 필요합니다.

1. 아래 증명 정책을 복사하고 이 정책을 텍스트 파일(txt)에 저장합니다. 아래 정책에 대한 자세한 내용은 [증명 공급자 만들기 및 구성](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)을 참조하세요.

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. 필요한 `Az.Attestation`의 버전을 가져옵니다.  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. 증명 공급자를 만듭니다. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. 증명 정책을 구성합니다.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Azure SQL 논리 서버에 증명 공급자에 대한 액세스 권한을 부여합니다. 이 단계에서는 앞서 서버에 할당한 관리형 서비스 ID의 개체 ID를 사용합니다.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. SGX enclave에 대해 구성한 증명 정책을 가리키는 증명 URL을 검색합니다. 나중에 필요하므로 URL을 저장합니다.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  증명 URL은 다음과 같습니다. `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>3단계: 데이터베이스 채우기

이 단계에서는 테이블을 만들고 나중에 암호화하고 쿼리할 일부 데이터로 채웁니다.

1. SSMS를 열고 데이터베이스 연결에서 Always Encrypted를 **사용하지 않고** 만든 Azure SQL 논리 서버의 **ContosoHR** 데이터베이스에 연결합니다.
    1. **서버에 연결** 대화 상자에서 서버의 정규화된 이름(예: *myserver123.database.windows.net*)을 지정하고, 서버를 만들 때 지정한 관리자 사용자 이름과 암호를 입력합니다.
    2. **옵션 >>** 을 클릭하고 **연결 속성** 탭을 선택합니다. **ContosoHR** 데이터베이스(기본, 마스터 데이터베이스 아님)를 선택해야 합니다. 
    3. **Always Encrypted** 탭을 선택합니다.
    4. **Always Encrypted 사용(열 암호화)** 확인란이 선택되어 있지 **않은지** 확인합니다.

        ![Always Encrypted 없이 연결](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. **연결** 을 클릭합니다.

2. **Employees** 라는 새 테이블을 만듭니다.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. **Employees** 테이블에 몇 개의 직원 레코드를 추가합니다.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>4단계: Enclave 사용 키 프로비전

이 단계에서는 Enclave 계산을 허용하는 열 마스터 키 및 열 암호화 키를 만듭니다.

1. 이전 단계의 SSMS 인스턴스를 사용해, **개체 탐색기** 에서 데이터베이스를 확장하고 **보안** > **Always Encrypted 키** 로 이동합니다.
1. 새 Enclave 사용 열 마스터 키를 프로비전합니다.
    1. **Always Encrypted 키** 를 마우스 오른쪽 단추로 클릭하고 **새 열 마스터 키...** 를 선택합니다.
    2. 열 마스터 키 이름을 선택합니다. **CMK1**.
    3. **Windows 인증서 저장소(현재 사용자 또는 로컬 컴퓨터)** 또는 **Azure Key Vault** 를 선택해야 합니다.
    4. **Enclave 계산 허용** 을 선택합니다.
    5. Azure Key Vault를 선택한 경우 Azure에 로그인하고 Key Vault를 선택합니다. Always Encrypted용 Key Vault를 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Key Vault 관리](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal)를 참조하세요.
    6. 인증서나 Azure 키 값 키가 이미 존재하는 경우 하나를 선택하거나 **인증서 생성** 단추를 클릭하여 새 인증서를 생성합니다.
    7. **확인** 을 선택합니다.

        ![Enclave 계산 허용](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. 새 Enclave 사용 열 암호화 키를 만듭니다.

    1. **Always Encrypted 키** 를 마우스 오른쪽 단추로 클릭하고 **새 열 암호화 키** 를 선택합니다.
    2. 새 열 암호화 키의 이름을 입력합니다. **CEK1**.
    3. **열 마스터 키** 드롭다운에서 이전 단계에서 만든 열 마스터 키를 선택합니다.
    4. **확인** 을 선택합니다.

## <a name="step-5-encrypt-some-columns-in-place"></a>5단계: 일부 열 미리 암호화

이 단계에서는 서버 쪽 Enclave 내에서 **SSN** 및 **Salary** 열에 저장된 데이터를 암호화한 다음, 데이터에서 SELECT 쿼리를 테스트합니다.

1. 새 SSMS 인스턴스를 열고 데이터베이스 연결에 대해 Always Encrypted를 **사용** 하여 데이터베이스에 연결합니다.
    1. SSMS의 새 인스턴스를 시작합니다.
    2. **서버에 연결** 대화 상자에서 서버의 정규화된 이름(예: *myserver123.database.windows.net*)을 지정하고, 서버를 만들 때 지정한 관리자 사용자 이름과 암호를 입력합니다.
    3. **옵션 >>** 을 클릭하고 **연결 속성** 탭을 선택합니다. **ContosoHR** 데이터베이스(기본, 마스터 데이터베이스 아님)를 선택해야 합니다. 
    4. **Always Encrypted** 탭을 선택합니다.
    5. **Always Encrypted(열 암호화) 사용** 확인란이 선택되어 있는지 확인합니다.
    6. [2단계: 증명 공급자 구성](#step-2-configure-an-attestation-provider)의 단계에 따라 얻은 enclave 증명 URL을 지정합니다. 아래 스크린샷을 참조하세요.

        ![증명을 사용하여 연결](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. **연결** 을 선택합니다.
    8. Always Encrypted 쿼리에 대해 매개 변수화를 사용하도록 설정하라는 메시지가 표시되면 **사용** 을 선택합니다.



1. 동일한 SSMS 인스턴스(Always Encrypted 사용)를 사용하여 새 쿼리 창을 열고, 아래 명령문을 실행하여 **SSN** 및 **Salary** 열을 암호화합니다.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > 위의 스크립트에서 데이터베이스에 대한 쿼리 계획 캐시를 지우려면 ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE 문을 확인합니다. 테이블을 변경한 후에는 테이블에 액세스하는 모든 일괄 처리 및 저장 프로시저에 대한 계획을 지워서 매개 변수 암호화 정보를 새로 고쳐야 합니다. 

1. **SSN** 및 **Salary** 열이 지금 암호화되었는지 확인하려면, 데이터베이스 연결에 대해 Always Encrypted를 사용하지 **않고** SSMS 인스턴스에서 새 쿼리 창을 열고 아래 문을 실행합니다. 쿼리 창의 **SSN** 및 **Salary** 열에 암호화된 값이 반환되어야 합니다. Always Encrypted를 사용하는 SSMS 인스턴스에서 동일한 쿼리를 실행하는 경우 암호화된 데이터가 표시되어야 됩니다.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>6단계: 암호화된 열에 대해 리치 쿼리 실행

암호화된 열에 대해 다양한 쿼리를 실행할 수 있습니다. 일부 쿼리 처리는 서버 쪽 Enclave 내에서 수행됩니다. 

1. Always Encrypted를 **사용** 하는 SSMS 인스턴스에서 Always Encrypted에 대해 매개 변수화도 사용하도록 설정되었는지 확인합니다.
    1. SSMS의 주 메뉴에서 **도구** 를 선택합니다.
    2. **옵션...** 을 선택합니다.
    3. **쿼리 실행** > **SQL Server** > **고급** 으로 이동합니다.
    4. **Always Encrypted에 대해 매개 변수화 사용** 이 선택되었는지 확인합니다.
    5. **확인** 을 선택합니다.
2. 새 쿼리 창을 열고 아래 쿼리를 붙여넣은 다음, 실행합니다. 이 쿼리는 지정된 검색 기준에 맞는 일반 텍스트 값 및 행을 반환해야 합니다.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Always Encrypted를 사용하도록 설정되지 않은 SSMS 인스턴스에서 동일한 쿼리를 다시 시도합니다. 오류가 발생합니다.
 
## <a name="next-steps"></a>다음 단계

이 자습서를 완료한 후 다음 자습서 중 하나로 이동할 수 있습니다.
- [자습서: 보안 enclave를 사용한 Always Encrypted를 이용하여 .NET 애플리케이션 개발](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)을 참조하세요.
- [자습서: 보안 enclave를 사용한 Always Encrypted를 이용하여 .NET Framework 애플리케이션 개발](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [자습서: 임의 암호화를 사용하는 enclave 사용 열에 인덱스 만들기 및 사용](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>참고 항목

- [보안 Enclave를 사용한 Always Encrypted 구성 및 사용](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
