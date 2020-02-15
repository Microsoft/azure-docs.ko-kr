---
title: PowerShell을 사용하여 여러 테이블 증분 복사
description: 이 자습서에서는 델타 데이터를 증분 방식으로 온-프레미스 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL Database로 복사하는 Azure Data Factory 파이프라인을 만듭니다.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/30/2020
ms.openlocfilehash: 5654e1f8b8a55c705798368df70ce300241c9dff
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989089"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>SQL Server의 여러 테이블에서 Azure SQL Database로 데이터 증분 로드

이 자습서에서는 델타 데이터를 온-프레미스 SQL Server의 여러 테이블에서 Azure SQL Database로 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.    

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * Integration Runtime을 설치합니다. 
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

## <a name="overview"></a>개요
이 솔루션을 만드는 중요한 단계는 다음과 같습니다. 

1. **워터마크 열을 선택합니다**.

    원본 데이터 저장소에서 각 테이블에 대해 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 식별할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.

2. **워터마크 값을 저장할 데이터 저장소를 준비합니다**.

    이 자습서에서는 SQL 데이터베이스에 워터마크 값을 저장합니다.

3. **다음 작업을 사용하여 파이프라인을 만듭니다.**
    
    a. 파이프라인에 매개 변수로 전달되는 원본 테이블 이름 목록을 반복하는 ForEach 작업을 만듭니다. 각 원본 테이블에 해당 테이블에 대한 델타 로드를 수행하는 다음 작업을 호출합니다.

    b. 두 가지 조회 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다.

    다. 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 복사 작업을 만듭니다. 그런 다음 원본 데이터 스토리지의 델타 데이터를 새 파일로 Azure Blob Storage에 복사합니다.

    d. 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 StoredProcedure 작업을 만듭니다. 

    대략적인 솔루션 다이어그램은 다음과 같습니다. 

    ![데이터 증분 로드](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* **SQL Server**. 이 자습서에서는 온-프레미스 SQL Server 데이터베이스를 원본 데이터 저장소로 사용합니다. 
* **Azure SQL Database**. SQL 데이터베이스를 싱크 데이터 저장소로 사용합니다. SQL 데이터베이스가 없는 경우 만드는 단계를 [Azure SQL 데이터베이스 만들기](../sql-database/sql-database-get-started-portal.md)에서 참조하세요. 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server 데이터베이스에 원본 테이블 만들기

1. [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)를 열고 온-프레미스 SQL Server 데이터베이스에 연결합니다.

2. **서버 탐색기(SSMS)** 또는 **연결 창(Azure Data Studio)** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**을 선택합니다.

3. 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`(이)라는 테이블을 만듭니다.

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Azure SQL Database에 대상 테이블 만들기

1. [SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 또는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)를 열고 온-프레미스 SQL Server 데이터베이스에 연결합니다.

2. **서버 탐색기(SSMS)** 또는 **연결 창(Azure Data Studio)** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**을 선택합니다.

3. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`(이)라는 테이블을 만듭니다.  

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>상위 워터마크 값을 저장하기 위해 Azure SQL Database에 또 다른 테이블 만들기

1. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장할 `watermarktable` 테이블을 만듭니다. 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. 두 원본 테이블의 초기 워터마크 값을 워터마크 테이블에 삽입합니다.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Azure SQL Database에 저장 프로시저 만들기 

다음 명령을 실행하여 SQL 데이터베이스에 저장 프로시저를 만듭니다. 이 저장 프로시저는 파이프라인의 실행이 끝날 때마다 워터마크 값을 업데이트합니다. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Azure SQL Database에 데이터 형식 및 추가 저장 프로시저 만들기

다음 쿼리를 실행하여 SQL 데이터베이스에 두 개의 데이터 형식과 두 개의 저장 프로시저를 만듭니다. 원본 테이블의 데이터를 대상 테이블에 병합하는 데 사용됩니다. 

여정을 쉽게 시작할 수 있도록 테이블 변수를 통해 델타 데이터를 전달하는 이러한 저장 프로시저를 직접 사용한 다음, 대상 저장소에 병합합니다. 테이블 변수에 "많은" 수(100개 초과)의 델타 행이 저장될 수 없으니 주의하세요.  

많은 수의 델타 행을 대상 저장소에 병합해야 하는 경우, 먼저 복사 작업을 사용하여 모든 델타 데이터를 대상 저장소의 임시 "준비" 테이블에 복사한 다음, 테이블 변수를 사용하지 않고 저장 프로시저를 직접 빌드하여 “준비” 테이블에서 “최종” 테이블로 병합하는 것이 좋습니다. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END
```

### <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell 설치 및 구성](/powershell/azure/azurerm/install-azurerm-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름을 지정한 다음 명령을 실행합니다. 예제는 `"adfrg"`입니다.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 실행합니다.

2. 데이터 팩터리의 위치에 대한 변수를 정의합니다. 

    ```powershell
    $location = "East US"
    ```
3. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다. 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 실행합니다.

4. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유하게 업데이트합니다. 예: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. 데이터 팩터리를 만들려면 다음 **Set-AzDataFactoryV2** cmdlet을 실행합니다. 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

다음 사항에 유의하세요.

* 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도합니다.

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 참여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다.

* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, SQL Database 등) 및 계산(Azure HDInsight 등)은 다른 지역에 있을 수 있습니다.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 온-프레미스 SQL Server 데이터베이스와 Azure SQL Database에 연결된 서비스를 만듭니다. 

### <a name="create-the-sql-server-linked-service"></a>SQL Server에 연결된 서비스 만들기

이 단계에서 온-프레미스 SQL Server 데이터베이스를 데이터 팩터리에 연결합니다.

1. C:\ADFTutorials\IncCopyMultiTableTutorial 폴더에 다음 내용이 포함된 **SqlServerLinkedService.json**이라는 JSON 파일을 만듭니다(아직 없는 경우 로컬 폴더 생성). Microsoft SQL Server에 연결하는 데 사용하는 인증을 기반으로 올바른 섹션을 선택합니다.  

    > [!IMPORTANT]
    > Microsoft SQL Server에 연결하는 데 사용하는 인증을 기반으로 올바른 섹션을 선택합니다.

    SQL 인증을 사용하는 경우 다음 JSON 정의를 복사합니다.

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Windows 인증을 사용하는 경우 다음 JSON 정의를 복사합니다.

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Microsoft SQL Server에 연결하는 데 사용하는 인증을 기반으로 올바른 섹션을 선택합니다.
    > - &lt;integration runtime name>을 사용자의 통합 런타임 이름으로 바꿉니다.
    > - 파일을 저장하기 전에 &lt;servername>, &lt;databasename>, &lt;username> 및 &lt;password>를 SQL Server 데이터베이스 값으로 바꿉니다.
    > - 슬래시 문자(`\`)를 사용자 계정 또는 서버 이름에 사용해야 할 경우 이스케이프 문자(`\`)를 사용합니다. 예제는 `mydomain\\myuser`입니다.

2. PowerShell에서 다음 cmdlet을 실행하여 C:\ADFTutorials\IncCopyMultiTableTutorial 폴더로 전환합니다.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 AzureStorageLinkedService라는 연결된 서비스를 만듭니다. 다음 예제에서는 *ResourceGroupName* 및 *DataFactoryName* 매개 변수에 대한 값을 전달합니다. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>SQL 데이터베이스에 연결된 서비스 만들기

1. C:\ADFTutorials\IncCopyMultiTableTutorial 폴더에 다음 내용이 포함된 **AzureSQLDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. (ADF 폴더가 없으면 해당 폴더를 만듭니다.) 파일을 저장하기 전에 &lt;servername&gt;, &lt;database name&gt;, &lt;user name&gt; 및 &lt;password&gt;를 SQL Server 데이터베이스 이름, 데이터베이스 이름, 사용자 이름 및 암호로 바꿉니다. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
2. PowerShell에서 **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 AzureSQLDatabaseLinkedService라는 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>데이터 세트 만들기

이 단계에서는 데이터 원본, 데이터 대상 및 워터마크를 저장할 위치를 나타내는 데이터 세트를 만듭니다.

### <a name="create-a-source-dataset"></a>원본 데이터 세트 만들기

1. 동일한 폴더에 다음 내용이 포함된 **SourceDataset.json**이라는 JSON 파일을 만듭니다. 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    파이프라인의 복사 작업은 전체 테이블을 로드하는 대신 SQL 쿼리를 사용하여 데이터를 로드합니다.

2. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 SourceDataset 데이터 세트를 만듭니다.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>싱크 데이터 세트 만들기

1. 동일한 폴더에 다음 내용이 포함된 **SinkDataset.json**이라는 JSON 파일을 만듭니다. tableName 요소는 런타임에 동적으로 파이프라인에 의해 설정됩니다. 파이프라인의 ForEach 작업은 테이블 이름 목록을 반복하고 반복할 때마다 테이블 이름을 이 데이터 세트에 전달합니다. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

2. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 SinkDataset 데이터 세트를 만듭니다.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>워터마크에 대한 데이터 세트 만들기

이 단계에서는 상위 워터마크 값을 저장하기 위한 데이터 세트를 만듭니다. 

1. 동일한 폴더에 다음 내용이 포함된 **WatermarkDataset.json**이라는 JSON 파일을 만듭니다. 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 WatermarkDataset 데이터 세트를 만듭니다.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>파이프라인 만들기

파이프라인에서는 테이블 이름 목록을 매개 변수로 사용합니다. **ForEach 작업**은 테이블 이름 목록을 반복하고 다음 작업을 수행합니다. 

1. **Lookup 작업**을 사용하여 이전 워터마크 값(초기 값 또는 마지막 반복에서 사용된 값)을 검색합니다.

2. **Lookup 작업**을 사용하여 새로운 워터마크 값(원본 테이블의 워터마크 열의 최댓값)을 검색합니다.

3. **Copy 작업**을 사용하여 원본 데이터베이스에서 대상 데이터베이스로 2개의 워터마크 값 사이에 데이터를 복사합니다.

4. **StoredProcedure 작업**을 사용하여 다음 반복의 첫 번째 단계에 사용할 이전 워터마크 값을 업데이트합니다. 

### <a name="create-the-pipeline"></a>파이프라인 만들기

1. 동일한 폴더에 다음 내용이 포함된 **IncrementalCopyPipeline.json**이라는 JSON 파일을 만듭니다. 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
2. **Set-AzDataFactoryV2Pipeline** cmdlet을 실행하여 IncrementalCopyPipeline 파이프라인을 만듭니다.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   샘플 출력은 다음과 같습니다. 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>파이프라인 실행

1. 동일한 폴더에 다음 내용이 포함된 **Parameters.json**이라는 매개 변수 파일을 만듭니다.

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
2. **Invoke-AzDataFactoryV2Pipeline** cmdlet을 사용하여 IncrementalCopyPipeline 파이프라인을 실행합니다. 자리 표시자를 사용자의 리소스 그룹 및 데이터 팩터리 이름으로 바꿉니다.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모든 서비스**를 선택하고 *데이터 팩터리* 키워드를 사용하여 검색하고 **데이터 팩터리**를 선택합니다. 

3. 데이터 팩터리 목록에서 데이터 팩터리를 검색하고 선택하여 **데이터 팩터리** 페이지를 시작합니다. 

4. **Data Factory** 페이지에서 **작성 및 모니터링**을 선택하여 별도의 탭에서 Azure Data Factory를 선택합니다.

5. **같이 시작해 볼까요?** 페이지의 왼쪽에서 **모니터링**을 선택합니다. 
![파이프라인 실행](media/doc-common-process/get-started-page-monitor-button.png)    

6. 모든 파이프라인 실행과 해당 상태를 볼 수 있습니다. 다음 예제에서 파이프라인 실행의 상태는 **성공**입니다. 파이프라인에 전달된 매개 변수를 확인하려면 **매개 변수** 열의 링크를 선택합니다. 오류가 발생하면 **오류** 열에 링크가 표시됩니다.

    ![파이프라인 실행](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. **작업** 열에 있는 링크를 선택하면 파이프라인에 대한 모든 활동 실행이 표시됩니다. 

8. **파이프라인 실행** 보기로 돌아가려면 **모든 파이프라인 실행**을 선택합니다. 

## <a name="review-the-results"></a>결과 검토

SQL Server Management Studio에서 대상 SQL 데이터베이스에 대해 다음 쿼리를 실행하여 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다. 

## <a name="add-more-data-to-the-source-tables"></a>원본 테이블에 데이터 추가

원본 SQL Server 데이터베이스에 대해 다음 쿼리를 실행하여 customer_table의 기존 행을 업데이트합니다. project_table에 새 행을 삽입합니다. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>파이프라인 다시 실행

1. 이제 다음 PowerShell 명령을 실행하여 파이프라인을 다시 실행합니다.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. [파이프라인 모니터링](#monitor-the-pipeline) 섹션의 지침에 따라 파이프라인 실행을 모니터링합니다. 파이프라인 상태가 **진행 중**이면 **작업** 아래 파이프라인 실행을 취소하는 다른 작업 링크가 표시됩니다. 

3. **새로 고침**을 선택하여 파이프라인 실행이 성공할 때까지 목록을 새로 고칩니다. 

4. 필요에 따라, **작업** 아래 **View Activity Runs**(작업 실행 보기) 링크를 클릭하여 이 파이프라인 실행과 연결된 모든 작업 실행을 표시합니다. 

## <a name="review-the-final-results"></a>최종 결과 검토

SQL Server Management Studio에서 대상 데이터베이스에 대해 다음 쿼리를 실행하여 업데이트된/새로운 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

3번 **PersonID**에 대한 **Name** 및 **LastModifytime**의 새 값을 확인합니다. 

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

**NewProject** 항목이 project_table에 추가되었습니다. 

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 IR(통합 런타임)을 만듭니다.
> * Integration Runtime을 설치합니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[변경 내용 추적 기술을 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터 증분 로드](tutorial-incremental-copy-change-tracking-feature-powershell.md)


