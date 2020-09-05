---
title: PowerShell을 사용하는 변경 추적을 사용하여 데이터 증분 복사
description: 이 자습서에서는 델타 데이터를 증분 방식으로 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL Database로 복사하는 Azure Data Factory 파이프라인을 만듭니다.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: 6fab092b0467228fd4fcdbcf95f99fd0981fb1e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079227"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-powershell"></a>PowerShell을 사용하는 변경 추적 정보를 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터 증분 로드

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure SQL Database의 원본 데이터베이스에 있는 **변경 내용 추적** 정보를 기반으로 Azure Blob 스토리지에 델타 데이터를 로드하는 파이프라인을 사용하여 Azure 데이터 팩터리를 만듭니다.  

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기.
> * 원본, 싱크 및 변경 내용 추적 데이터 세트를 만듭니다.
> * 전체 복사 파이프라인을 생성, 실행 및 모니터링합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 증분 복사 파이프라인을 생성, 실행 및 모니터링합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
데이터 통합 솔루션에서 초기 데이터로드 후 데이터 증분을 로드하는 것은 널리 사용되는 시나리오입니다. 경우에 따라 원본 데이터 저장소에서 특정 기간 내에 변경된 데이터를 쉽게 조각낼 수 있습니다(예: LastModifyTime, CreationTime). 데이터를 마지막으로 처리한 이후 델타 데이터를 식별하는 명시적인 방법이 없는 경우가 있습니다. 변경 내용 추적 기술은 Azure SQL Database와 같은 데이터 저장소 기술에 의해 지원되며 SQL Server는 델타 데이터를 파악하는 데 사용될 수 있습니다.  이 자습서는 SQL 변경 내용 추적 기술을 통해 Azure Data Factory를 사용하여 Azure SQL Database에서 Azure Blob Storage로 델타 데이터를 증분 로드하는 방법을 설명합니다.  SQL 변경 내용 추적 기술에 대한 구체적인 정보는 [SQL Server에서 변경 내용 추적](/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 참조하세요.

## <a name="end-to-end-workflow"></a>엔드투엔드 워크플로
다음은 변경 내용 추적 기술을 사용하여 데이터 증분을 로드하는 일반적인 엔드투엔드 워크플로 단계입니다.

> [!NOTE]
> Azure SQL Database와 SQL Server 모두 변경 내용 추적 기술을 지원합니다. 이 자습서는 Azure SQL Database를 원본 데이터 저장소로 사용합니다. SQL Server 인스턴스를 사용할 수도 있습니다.

1. **과거 데이터의 초기 로드**(한 번 실행):
    1. Azure SQL Database의 원본 데이터베이스에서 변경 내용 추적 기술을 사용하도록 설정합니다.
    2. 데이터베이스의 SYS_CHANGE_VERSION 초기 값을 변경된 데이터를 캡처하는 기준선으로 가져옵니다.
    3. 데이터베이스의 전체 데이터를 Azure Blob 스토리지로 로드합니다.
2. **일정에 따라 델타 데이터 증분 로드**(데이터의 초기 로드 후 주기적으로 실행):
    1. SYS_CHANGE_VERSION의 이전 값과 새 값을 가져옵니다.
    2. **sys.change_tracking_tables**의 변경된 행(두 SYS_CHANGE_VERSION 값 사이)의 기본 키를 **원본 테이블**의 데이터와 조인하여 델타 데이터를 로드한 다음 델타 데이터를 대상으로 이동합니다.
    3. 다음 번 델타 로드를 위해 the SYS_CHANGE_VERSION을 업데이트합니다.

## <a name="high-level-solution"></a>대략적인 솔루션
이 자습서에서는 다음 두 가지 작업을 수행하는 파이프라인을 만듭니다.  

1. **초기 로드:** 원본 데이터 저장소(Azure SQL Database)에서 대상 데이터 저장소(Azure Blob Storage)로 전체 데이터를 복사하는 복사 작업이 있는 파이프라인을 만듭니다.

    ![데이터 전체 로드](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **증분 로드:** 다음 작업이 있는 파이프라인을 만들어서 주기적으로 실행합니다.
    1. Azure SQL Database의 이전 및 새 SYS_CHANGE_VERSION을 가져오도록 **두 가지 조회 작업**을 만들어서 복사 작업에 전달합니다.
    2. 두 가지 SYS_CHANGE_VERSION 값 사이에 삽입된/업데이트된/삭제된 데이터를 Azure SQL Database에서 Azure Blob Storage로 복사하도록 **복사 작업을 하나** 만듭니다.
    3. 다음 번 파이프라인 실행을 위해 SYS_CHANGE_VERSION 값을 업데이트하도록 **저장 프로시저 작업을 하나** 만듭니다.

    ![증분 로드 흐름 다이어그램](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure PowerShell. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.
* **Azure SQL Database**. 데이터베이스를 **원본** 데이터 저장소로 사용합니다. Azure SQL Database에 데이터베이스가 없는 경우 데이터베이스를 만드는 단계는 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md) 문서를 참조하세요.
* **Azure Storage 계정**. Blob Storage를 **싱크** 데이터 스토리지로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [스토리지 계정 만들기](../storage/common/storage-account-create.md) 문서를 참조하세요. **adftutorial**이라는 컨테이너를 만듭니다. 

### <a name="create-a-data-source-table-in-your-database"></a>데이터베이스에 데이터 원본 테이블 만들기

1. **SQL Server Management Studio**를 시작하고 SQL Database에 연결합니다.
2. **서버 탐색기**에서 **데이터베이스**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다.
3. 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `data_source_table`이라는 테이블을 만듭니다.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. 다음 SQL 쿼리를 실행하여 데이터베이스 및 원본 테이블(data_source_table)에서 **변경 내용 추적** 메커니즘을 사용하도록 설정합니다.

    > [!NOTE]
    > - &lt;데이터베이스 이름&gt;을 data_source_table이 있는 데이터베이스 이름으로 바꿉니다.
    > - 현재 예제에서 변경된 데이터는 2일간 유지됩니다. 3일(또는 그 이상)마다 변경된 데이터를 로드하는 경우 일부 변경된 데이터가 포함되지 않습니다.  CHANGE_RETENTION 값을 더 큰 숫자로 변경해야 합니다. 또는 변경된 데이터를 로드하는 기간이 2일 이내가 되도록 해야 합니다. 자세한 내용은 [데이터베이스에 대한 변경 내용 추적 사용](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)을 참조하세요.

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 다음 쿼리를 실행하여 새 테이블을 만들고 ChangeTracking_version을 기본값으로 저장합니다.

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > SQL Database에 대한 변경 내용 추적을 사용하도록 설정한 후 데이터가 변경되지 않으면 변경 내용 추적 버전의 값은 0입니다.
6. 다음 쿼리를 실행하여 데이터베이스에 저장 프로시저를 만듭니다. 파이프라인이 이전 단계에서 만든 테이블에서 변경 내용 추적 버전을 업데이트하도록 저장 프로시저를 호출합니다.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기
1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"adfrg"` 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
2. 데이터 팩터리의 위치에 대한 변수를 정의합니다.

    ```powershell
    $location = "East US"
    ```
3. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다.

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.
3. 데이터 팩터리 이름에 대한 변수를 정의합니다.

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. 데이터 팩터리를 만들려면 다음 **Set-AzDataFactoryV2** cmdlet을 실행합니다.

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자**이어야 합니다.
* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics**를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.


## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 Azure Storage 계정과 Azure SQL Database의 데이터베이스에 연결된 서비스를 만듭니다.

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스를 만듭니다.
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. **C:\ADFTutorials\IncCopyChangeTrackingTutorial** 폴더에 다음 내용이 포함된 **AzureStorageLinkedService.json**이라는 JSON 파일을 만듭니다. (이 폴더가 아직 없으면 만듭니다.) 파일을 저장하기 전에 `<accountName>`과 `<accountKey>`를 Azure Storage 계정의 이름과 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. **Azure PowerShell**에서 **C:\ADFTutorials\IncCopyChangeTrackingTutorial** 폴더로 전환합니다.
3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 연결된 서비스를 만듭니다. **AzureStorageLinkedService** 연결된 서비스를 만듭니다. 다음 예제에서는 **ResourceGroupName** 및 **DataFactoryName** 매개 변수에 대한 값을 전달합니다.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database 연결 서비스를 만듭니다.
이 단계에서는 데이터베이스를 데이터 팩터리에 연결합니다.

1. **C:\ADFTutorials\IncCopyChangeTrackingTutorial** 폴더에 다음 내용이 포함된 **AzureSQLDatabaseLinkedService.json**이라는 JSON 파일을 만듭니다. 파일을 저장하기 전에 **&lt;server&gt; &lt;database name&gt;, &lt;user id&gt; 및 &lt;password&gt;** 를 서버 이름, 데이터베이스 이름, 사용자 ID 및 암호로 바꿉니다.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. **Azure PowerShell**에서 **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 **AzureSQLDatabaseLinkedService** 연결된 서비스를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 데이터 원본, 데이터 대상을 나타내는 데이터 세트를 만듭니다. 그리고 SYS_CHANGE_VERSION을 저장할 곳을 만듭니다.

### <a name="create-a-source-dataset"></a>원본 데이터 세트 만들기
이 단계에서는 원본 데이터를 나타내는 데이터 세트를 만듭니다.

1. 동일한 폴더에 다음 내용이 포함된 SourceDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  Set-AzDataFactoryV2Dataset cmdlet을 실행하여 데이터 세트를 만듭니다. SourceDataset 데이터 세트를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>싱크 데이터 세트 만들기
이 단계에서는 원본 데이터 저장소에서 복사된 데이터를 나타내는 데이터 세트를 만듭니다.

1. 동일한 폴더에 다음 내용이 포함된 SinkDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    요구 사항의 일부로 Azure Blob Storage에 adftutorial 컨테이너를 만듭니다. 아직 없는 경우 컨테이너를 만들거나 기존 컨테이너의 이름으로 설정합니다. 이 자습서에서 출력 파일 이름은 @CONCAT('Incremental-', pipeline().RunId, '.txt')식을 사용하여 동적으로 생성됩니다.
2.  Set-AzDataFactoryV2Dataset cmdlet을 실행하여 데이터 세트를 만듭니다. SinkDataset 데이터 세트를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>변경 내용 추적 데이터 세트 만들기
이 단계에서는 변경 내용 추적 버전을 저장하기 위한 데이터 세트를 만듭니다.  

1. 동일한 폴더에 다음 내용이 포함된 ChangeTrackingDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    요구 사항의 일부로 table_store_ChangeTracking_version 테이블을 만듭니다.
2.  Set-AzDataFactoryV2Dataset cmdlet을 실행하여 데이터 세트를 만듭니다. ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>전체 복사본에 대한 파이프라인 만들기
이 단계에서는 원본 데이터 저장소(Azure SQL Database)에서 대상 데이터 저장소(Azure Blob Storage)로 전체 데이터를 복사하는 복사 작업이 있는 파이프라인을 만듭니다.

1. JSON 파일 만들기: 동일한 폴더에 다음 내용이 포함된 FullCopyPipeline.json을 만듭니다.

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Set-AzDataFactoryV2Pipeline cmdlet을 실행하여 파이프라인을 만듭니다. FullCopyPipeline 파이프라인을 만듭니다.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>전체 복사 파이프라인 실행
파이프라인 실행: **Invoke-AzDataFactoryV2Pipeline** cmdlet을 사용하여 **FullCopyPipeline** 파이프라인을 실행합니다.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>전체 복사 파이프라인 모니터링

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 클릭하고 `data factories` 키워드를 사용하여 검색하고 **데이터 팩터리**를 선택합니다.

    ![데이터 팩터리 메뉴](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. 데이터 팩터리 목록에서 **데이터 팩터리**를 검색하고 선택하여 데이터 팩터리 페이지를 시작합니다.

    ![데이터 팩터리 검색](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. 데이터 팩터리 페이지에서 **모니터링 및 관리** 타일을 클릭합니다.

    ![타일 모니터링 및 관리](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. **데이터 통합 애플리케이션**이 별도의 탭에서 시작됩니다. 모든 **파이프라인 실행**과 해당 상태를 볼 수 있습니다. 다음 예제에서 파이프라인 실행의 상태는 **성공**입니다. **매개 변수** 열의 링크를 클릭하면 파이프라인에 전달된 매개 변수를 확인할 수 있습니다. 오류가 있으면 **오류** 열에 링크가 표시됩니다. **작업** 열의 링크를 클릭합니다.

    ![파이프라인 실행](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. **작업** 열에 있는 링크를 클릭하면 파이프라인의 모든 **작업 실행**을 보여주는 다음 페이지가 표시됩니다.

    ![작업 실행](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. **파이프라인 실행** 보기로 다시 전환하려면 다음 이미지와 같이 **파이프라인**을 클릭합니다.


### <a name="review-the-results"></a>결과 검토
`adftutorial` 컨테이너의 `incchgtracking` 폴더에 `incremental-<GUID>.txt`라는 파일이 표시됩니다.

![전체 복사의 출력 파일](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

파일에는 데이터베이스의 데이터가 있어야 합니다.

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>원본 테이블에 데이터 추가

데이터베이스에 다음 쿼리를 실행하여 행을 추가하고 행을 업데이트합니다.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>델타 복사를 위한 파이프라인 만들기
이 단계에서는 다음 작업이 있는 파이프라인을 만들어서 주기적으로 실행합니다. **조회 작업**은 Azure SQL Database에서 SYS_CHANGE_VERSION의 기존 및 새 값을 가져와서 만들어서 복사 작업에 전달합니다. **복사 작업**은 두 가지 SYS_CHANGE_VERSION 값 사이에 삽입된/업데이트된/삭제된 데이터를 Azure SQL Database에서 Azure Blob Storage로 복사합니다. **저장 프로시저 작업**은 다음 번 파이프라인 실행을 위해 SYS_CHANGE_VERSION 값을 업데이트합니다.

1. JSON 파일 만들기: 동일한 폴더에 다음 내용이 포함된 IncrementalCopyPipeline.json을 만듭니다.

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },

                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },

                            "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },

                    {
                        "name": "IncrementalCopyActivity",
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                        }
                        ],

                        "inputs": [
                            {
                            "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                        }
                        ],
                        "outputs": [
                            {
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference"
                            }
                        ]
                    },

                {
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {

                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
                            }
                    },

                        "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                            "type": "LinkedServiceReference"
                        },

                        "dependsOn": [
                        {
                                "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }
                        ]
                    }
                ]

            }
    }

    ```
2. Set-AzDataFactoryV2Pipeline cmdlet을 실행하여 파이프라인을 만듭니다. FullCopyPipeline 파이프라인을 만듭니다.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>증분 복사 파이프라인 실행
파이프라인 실행: **Invoke-AzDataFactoryV2Pipeline** cmdlet을 사용하여 **IncrementalCopyPipeline** 파이프라인을 실행합니다.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>증분 복사 파이프라인 모니터링
1. **데이터 통합 애플리케이션**에서 **파이프라인 실행** 보기를 새로 고칩니다. 목록에 IncrementalCopyPipeline이 표시되는지 확인합니다. **작업** 열의 링크를 클릭합니다.  

    ![파이프라인 실행](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. **작업** 열에 있는 링크를 클릭하면 파이프라인의 모든 **작업 실행**을 보여주는 다음 페이지가 표시됩니다.

    ![작업 실행](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. **파이프라인 실행** 보기로 다시 전환하려면 다음 이미지와 같이 **파이프라인**을 클릭합니다.

### <a name="review-the-results"></a>결과 검토
`adftutorial` 컨테이너의 `incchgtracking` 폴더에 두 번째 파일이 표시됩니다.

![증분 복사의 출력 파일](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

파일에는 데이터베이스의 델타 데이터만 포함되어야 합니다. `U`가 포함된 레코드가 데이터베이스에서 업데이트된 행이고 `I`가 추가된 행입니다.

```
1,update,10,2,U
6,new,50,1,I
```
처음 세 열은 data_source_table에서 변경된 데이터입니다. 마지막 두 열은 변경 내용 추적 시스템 테이블의 메타데이터입니다. 네 번째 열은 변경된 각 행의 SYS_CHANGE_VERSION입니다. 다섯 번째 열에서  U는 업데이트 작업, I는 삽입 작업을 나타냅니다.  변경 내용 추적 정보에 대한 자세한 내용은 [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)을 참조하세요.

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```


## <a name="next-steps"></a>다음 단계
다음 자습서로 넘어가서 해당 LastModifiedDate를 기준으로 새로운 파일과 변경된 파일을 복사하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
>[lastmodifieddate별로 새 파일 복사](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
