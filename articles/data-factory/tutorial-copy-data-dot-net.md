---
title: Azure Blob Storage에서 Azure SQL Database로 데이터 복사
description: 이 자습서에서는 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 단계별 지침을 제공합니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: b2293c0dd74903921abb58037afd8eb5db3659d9
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85513272"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob에서 Azure SQL Database로 데이터 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 Data Factory 파이프라인을 만듭니다. 이 자습서의 구성 패턴은 파일 기반 데이터 저장소에서 관계형 데이터 저장소로 복사하는 데 적용됩니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

이 자습서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 및 Azure SQL Database 연결된 서비스를 만듭니다.
> * Azure Blob 및 Azure SQL Database 데이터 세트를 만듭니다.
> * 복사 작업이 포함된 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

이 자습서에서는 .NET SDK를 사용합니다. 다른 메커니즘을 사용하여 Azure Data Factory와 상호 작용할 수 있습니다. **빠른 시작** 아래에 있는 샘플을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* *Azure Storage 계정*. Blob Storage를 *원본* 데이터 스토리지로 사용합니다. Azure 스토리지 계정이 없는 경우 [범용 스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.
* *Azure SQL Database*. 데이터베이스를 *싱크* 데이터 저장소로 사용합니다. Azure SQL Database에 데이터베이스가 없는 경우 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)를 참조하세요.
* *Visual Studio* 이 문서의 연습에서는 Visual Studio 2019를 사용합니다.
* *[.NET용 Azure SDK](/dotnet/azure/dotnet-tools)* .
* *Azure Active Directory 애플리케이션*. Azure Active Directory 애플리케이션이 없는 경우 [Azure Active Directory 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 섹션을 참조하세요. [방법: 포털을 사용하여 Azure AD 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md)에서 찾을 수 있습니다. 이후 단계에서 사용할 수 있도록 다음 값을 복사합니다. **애플리케이션(클라이언트) ID**, **인증 키** 및 **디렉터리(테넌트) ID** 동일한 문서의 지침에 따라 애플리케이션을 **기여자** 역할에 할당합니다.

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 원본 블로그와 싱크 SQL 테이블을 만들어서 자습서에서 사용할 Azure Blob 및 Azure SQL Database를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

먼저 컨테이너를 만들고 입력 텍스트 파일을 컨테이너에 업로드하여 원본 Blob을 만듭니다.

1. 메모장을 엽니다. 다음 텍스트를 복사하여 *inputEmp.txt* 파일에 로컬로 저장합니다.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 같은 도구를 사용하여 *adfv2tutorial* 컨테이너를 만들고 *inputEmp.txt* 파일을 이 컨테이너에 업로드합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

다음으로, 싱크 SQL 테이블을 만듭니다.

1. 다음 SQL 스크립트를 사용하여 Azure SQL Database에 *dbo.emp* 테이블을 만듭니다.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Azure 서비스에서 SQL Database에 액세스하도록 허용합니다. Data Factory 서비스에서 SQL Database에 데이터를 쓸 수 있도록 서버의 Azure 서비스에 액세스할 수 있는지 확인합니다. 이 설정을 확인하고 켜려면 다음 단계를 수행합니다.

    1. SQL 서버를 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **SQL 서버**를 검색하여 선택합니다.

    2. 서버를 선택합니다.

    3. SQL server 메뉴의 **보안** 머리글 아래에서 **방화벽 및 가상 네트워크**를 선택합니다.

    4. **방화벽 및 가상 네트워크** 페이지의 **Azure 서비스 및 리소스에서 이 서버에 액세스할 수 있도록 허용**에서 **ON**을 선택합니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio를 사용하여 C# .NET 콘솔 애플리케이션을 만듭니다.

1. Visual Studio를 엽니다.
2. **시작** 창에서 **새 프로젝트 만들기**를 선택합니다.
3. **새 프로젝트 만들기** 창의 프로젝트 형식 목록에서 C# 버전의 **콘솔 앱(.NET Framework)** 을 선택합니다. 그런 후 **다음**을 선택합니다.
4. **새 프로젝트 구성** 창에서 **프로젝트 이름**으로 *ADFv2Tutorial*을 입력합니다. **위치**의 경우 프로젝트를 저장할 디렉터리를 찾아보거나 만듭니다. 그런 다음 **만들기**를 선택합니다. Visual Studio IDE에 새 프로젝트가 표시됩니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

다음으로, NuGet 패키지 관리자를 사용하여 필요한 라이브러리 패키지를 설치합니다.

1. 메뉴 모음에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
2. **패키지 관리자 콘솔** 페이지에서 다음 명령을 실행하여 패키지를 설치합니다. Azure Data Factory NuGet 패키지에 대한 자세한 내용은 [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)를 참조하세요.

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

데이터 팩터리 클라이언트를 만들려면 다음 단계를 수행합니다.

1. *Program.cs*를 열고, 네임스페이스에 대한 참조를 추가하는 다음 코드로 기존 `using` 문을 덮어씁니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. `Main` 메서드에 변수를 설정하는 다음 코드를 추가합니다. 14개 자리 표시자를 사용자 고유의 값으로 바꿉니다.

    현재 Data Factory를 사용할 수 있는 Azure 지역 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요. **제품** 드롭다운 목록에서 **찾아보기** > **Analytics** > **Data Factory**를 선택합니다. 그리고 **Azure 지역** 드롭다운 목록에서 관심 있는 Azure 지역을 선택합니다. 선택한 Azure 지역의 Data Factory 제품 가용성 상태가 포함된 표가 표시됩니다.

    > [!NOTE]
    > Data Factory에서 사용하는 Azure Storage 및 Azure SQL Database 같은 데이터 저장소 및 HDInsight 같은 컴퓨팅은 사용자가 Data Factory에 대해 선택하는 지역과 다른 지역에 있을 수 있습니다.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. `Main` 메서드에 `DataFactoryManagementClient` 클래스 인스턴스를 만드는 다음 코드를 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링합니다.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

*데이터 팩터리*를 만드는 다음 코드를 `Main` 메서드에 추가합니다.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>연결된 서비스 만들기

이 자습서에서는 원본과 싱크에 각각 하나씩, 총 두 개의 연결된 서비스를 만듭니다.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

`Main` 메서드에 *Azure Storage 연결된 서비스*를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보에 대한 자세한 내용은 [Azure Blob 연결된 서비스 속성](connector-azure-blob-storage.md#linked-service-properties)을 참조하세요.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기

`Main` 메서드에 *Azure SQL Database 연결된 서비스*를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보에 대한 자세한 내용은 [Azure SQL Database 연결된 서비스 속성](connector-azure-sql-database.md#linked-service-properties)을 참조하세요.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>데이터 세트 만들기

이 섹션에서는 원본과 싱크에 각각 하나씩, 총 두 개의 데이터 세트를 만듭니다.

### <a name="create-a-dataset-for-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 세트 만들기

`Main` 메서드에 *Azure Blob 데이터 세트*를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보에 대한 자세한 내용은 [Azure Blob 데이터 세트 속성](connector-azure-blob-storage.md#dataset-properties)을 참조하세요.

Azure Blob의 원본 데이터를 나타내는 데이터 세트를 정의합니다. 이 Blob 데이터 세트는 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조하며 다음을 설명합니다.

- 복사할 Blob의 원본 위치: `FolderPath` 및 `FileName`
- 콘텐츠를 구문 분석하는 방법을 나타내는 Blob 형식: `TextFormat` 및 해당 설정(예: 열 구분 기호)
- 이 예에서는 싱크 SQL 테이블에 매핑하는 열 이름과 데이터 형식을 포함한 데이터 구조

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>싱크 Azure SQL Database에 대한 데이터 세트 만들기

`Main` 메서드에 *Azure SQL Database 데이터 세트*를 만드는 다음 코드를 추가합니다. 지원되는 속성 및 세부 정보에 대한 자세한 내용은 [Azure SQL Database 데이터 세트 속성](connector-azure-sql-database.md#dataset-properties)을 참조하세요.

Azure SQL Database의 싱크 데이터를 나타내는 데이터 세트를 정의합니다. 이 데이터 세트는 이전 단계에서 만든 Azure SQL Database 연결된 서비스를 참조합니다. 또한 복사된 데이터를 보관하는 SQL 테이블을 지정합니다.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>파이프라인 만들기

`Main` 메서드에 *복사 작업이 있는 파이프라인*을 만드는 다음 코드를 추가합니다. 이 자습서에서는 이 파이프라인에 하나의 작업(`CopyActivity`), 즉, Blob 데이터 세트를 원본으로, SQL 데이터 세트를 싱크로 사용하는 복사 작업이 포함됩니다. 복사 작업에 대한 자세한 내용은 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

`Main` 메서드에 *파이프라인 실행을 트리거*하는 다음 코드를 추가합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

이제 파이프라인 실행 상태를 검사하는 코드를 삽입하고 복사 작업 실행에 대한 세부 정보를 가져옵니다.

1. `Main` 메서드에 다음 코드를 추가하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. `Main` 메서드에 복사 작업 실행 세부 정보(예: 읽거나 쓴 데이터의 크기)를 검색하는 다음 코드를 추가합니다.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행

**빌드** > **솔루션 빌드**를 선택하여 애플리케이션을 빌드합니다. 그리고 **디버그** > **디버깅 시작**을 선택하여 애플리케이션을 시작하고, 파이프라인 실행을 확인합니다.

콘솔에서 데이터 팩터리, 연결된 서비스, 데이터 세트, 파이프라인 및 파이프라인 실행 만들기에 대한 진행 상황을 출력합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음, SSMS(SQL Server Management Studio) 또는 Visual Studio 같은 도구를 사용하여 대상 Azure SQL Database에 연결하고 지정한 대상 테이블에 데이터가 복사되었는지 확인할 수 있습니다.

### <a name="sample-output"></a>샘플 출력

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>다음 단계

이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure Storage 및 Azure SQL Database 연결된 서비스를 만듭니다.
> * Azure Blob 및 Azure SQL Database 데이터 세트를 만듭니다.
> * 복사 작업이 포함된 파이프라인을 만듭니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 및 작업 실행을 모니터링합니다.

온-프레미스에서 클라우드로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[온-프레미스에서 클라우드로 데이터 복사](tutorial-hybrid-copy-powershell.md)
