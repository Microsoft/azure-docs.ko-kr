---
title: Azure Data Factory 파이프라인 분기
description: 분기 및 연결 작업을 통해 Azure Data Factory에서 데이터 흐름을 제어하는 방법을 알아봅니다.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 0330e72ad74726f97bfdfd78ef8d5f9b24a5d172
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85513323"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory 파이프라인 분기 및 연결 작업

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 몇 가지 제어 흐름 기능을 보여 주는 Data Factory 파이프라인을 만듭니다. 이 파이프라인은 Azure Blob Storage의 컨테이너에서 동일한 스토리지 계정의 다른 컨테이너에 복사합니다. 복사 활동이 성공하면 파이프라인에서 성공적인 복사 작업에 대한 세부 정보를 이메일로 보냅니다. 이 정보에는 기록된 데이터의 양이 포함될 수 있습니다. 복사 활동이 실패하면 복사 실패에 대한 세부 정보(예: 오류 메시지)를 이메일로 보냅니다. 자습서 전체에서 매개 변수를 전달하는 방법을 확인할 수 있습니다.

다음 그래픽에서는 시나리오에 대한 개요를 제공합니다.

![개요](media/tutorial-control-flow/overview.png)

이 자습서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

> [!div class="checklist"]
> * 데이터 팩터리 만들기
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 세트 만들기
> * 복사 작업 및 웹 작업이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 사용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이 자습서에서는 .NET SDK를 사용합니다. 다른 메커니즘을 사용하여 Azure Data Factory와 상호 작용할 수 있습니다. Data Factory 빠른 시작에 대해서는 [5분 빠른 시작](/azure/data-factory/quickstart-create-data-factory-portal)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Storage 계정. Blob 스토리지를 원본 데이터 저장소로 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.
* Azure Storage Explorer. 이 도구를 설치하려면 [Azure Storage Explorer](https://storageexplorer.com/)를 참조하세요.
* Azure SQL Database. 데이터베이스를 싱크 데이터 저장소로 사용합니다. Azure SQL Database에 데이터베이스가 없는 경우 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)를 참조하세요.
* Visual Studio. 이 문서에서는 Visual Studio 2019를 사용합니다.
* Azure .NET SDK. [Azure .NET SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 설치합니다.

현재 Data Factory를 사용할 수 있는 Azure 지역의 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요. 데이터 저장소와 컴퓨팅은 다른 지역에 있을 수 있습니다. 저장소에는 Azure Storage 및 Azure SQL Database가 포함됩니다. 컴퓨팅에는 Data Factory에서 사용하는 HDInsight가 포함됩니다.

[Azure Active Directory 애플리케이션 만들기](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)에서 설명한 대로 애플리케이션을 만듭니다. 동일한 문서의 지침에 따라 애플리케이션을 "**기여자**" 역할에 할당합니다. 이 자습서의 이후 부분에는 **애플리케이션(클라이언트) ID** 및 **디렉터리(테넌트) ID**와 같은 몇 가지 값이 필요합니다.

### <a name="create-a-blob-table"></a>Blob 테이블 만들기

1. 텍스트 편집기를 엽니다. 다음 텍스트를 복사하여 *input.txt*로 로컬로 저장합니다.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Azure Storage Explorer를 엽니다. 스토리지 계정을 펼칩니다. 마우스 오른쪽 단추로 **Blob 컨테이너**를 클릭하고 **Blob 컨테이너 만들기**를 선택합니다.
1. 새 컨테이너의 이름을 *adfv2branch*로 지정하고, **업로드**를 선택하여 *input.txt* 파일을 컨테이너에 추가합니다.

## <a name="create-visual-studio-project"></a>Visual Studio 프로젝트 만들기<a name="create-visual-studio-project"></a>

C# .NET 콘솔 애플리케이션을 만듭니다.

1. Visual Studio를 시작하고, **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고, **다음**을 선택합니다.
1. 프로젝트 이름을 *ADFv2BranchTutorial*로 지정합니다.
1. **.NET 버전 4.5.2** 이상, **만들기**를 차례로 선택합니다.

### <a name="install-nuget-packages"></a>NuGet 패키지 설치

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
1. **패키지 관리자 콘솔**에서 다음 명령을 실행하여 패키지를 설치합니다. 자세한 내용은 [Microsoft.Azure.Management.DataFactory NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)를 참조하세요.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>데이터 팩터리 클라이언트 만들기

1. *Program.cs*를 열고, 다음 명령문을 추가합니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. 다음 정적 변수를 `Program` 클래스에 추가합니다. 자리 표시자를 사용자의 고유 값으로 바꿉니다.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. `Main` 메서드에 다음 코드를 추가합니다. 이 코드는 `DataFactoryManagementClient` 클래스의 인스턴스를 만듭니다. 그런 다음, 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만듭니다. 또한 이 개체를 사용하여 파이프라인 실행 세부 정보를 모니터링할 수도 있습니다.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. `CreateOrUpdateDataFactory` 메서드를 *Program.cs* 파일에 추가합니다.

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. 데이터 팩터리를 만드는 `Main` 메서드에 다음 줄을 추가합니다.

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

1. `StorageLinkedServiceDefinition` 메서드를 *Program.cs* 파일에 추가합니다.

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Azure Storage 연결된 서비스를 만드는`Main` 메서드에 다음 줄을 추가합니다.

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

지원되는 속성 및 세부 정보에 대한 자세한 내용은 [연결된 서비스 속성](connector-azure-blob-storage.md#linked-service-properties)을 참조하세요.

## <a name="create-datasets"></a>데이터 세트 만들기

이 섹션에서는 원본 및 싱크에 대해 하나씩 두 개의 데이터 세트를 만듭니다.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>원본 Azure Blob에 대한 데이터 세트 만들기

*Azure Blob 데이터 세트*를 만드는 메서드를 추가합니다. 지원되는 속성 및 세부 정보에 대한 자세한 내용은 [Azure Blob 데이터 세트 속성](connector-azure-blob-storage.md#dataset-properties)을 참조하세요.

`SourceBlobDatasetDefinition` 메서드를 *Program.cs* 파일에 추가합니다.

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Azure Blob의 원본 데이터를 나타내는 데이터 세트를 정의합니다. 이 Blob 데이터 세트는 이전 단계에서 지원되는 Azure Storage 연결된 서비스를 참조합니다. Blob 데이터 세트는 복사할 원본 Blob의 위치(*FolderPath* 및 *FileName*)를 설명합니다.

*FolderPath*에 매개 변수를 사용하는지 확인합니다. `sourceBlobContainer`는 매개 변수의 이름이고, 식은 파이프라인 실행에서 전달된 값으로 바뀝니다. 매개 변수를 정의하는 구문은 `@pipeline().parameters.<parameterName>`입니다.

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>싱크 Azure Blob에 대한 데이터 세트 만들기

1. `SourceBlobDatasetDefinition` 메서드를 *Program.cs* 파일에 추가합니다.

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Azure Blob 원본 및 싱크 데이터 세트를 모두 만드는 `Main` 메서드에 다음 코드를 추가합니다.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>C# 클래스 만들기: EmailRequest

C# 프로젝트에서 `EmailRequest`라는 클래스를 만듭니다. 이 클래스는 이메일을 보낼 때 파이프라인이 본문 요청에서 보내는 속성을 정의합니다. 이 자습서에서 파이프라인은 다음 네 가지 파이프라인 속성을 전자 메일로 보냅니다.

* Message. 이메일의 본문입니다. 성공적인 복사의 경우 이 속성에는 기록된 데이터의 양이 포함됩니다. 실패한 복사의 경우 이 속성에는 오류에 대한 세부 정보가 포함됩니다.
* 데이터 팩터리 이름. 데이터 팩터리의 이름입니다.
* 파이프라인 이름. 파이프라인의 이름입니다.
* 받는 사람. 전달하는 매개 변수입니다. 이 속성은 전자 메일의 받는 사람을 지정합니다.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>전자 메일 워크플로 엔드포인트 만들기

전자 메일 보내기를 트리거하려면 [Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 워크플로를 정의합니다. Logic Apps 워크플로를 만드는 방법에 대한 자세한 내용은 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 참조하세요.

### <a name="success-email-workflow"></a>성공 전자 메일 워크플로

[Azure Portal](https://portal.azure.com)에서 *CopySuccessEmail*이라는 Logic Apps 워크플로를 만듭니다. 워크플로 트리거를 `When an HTTP request is received`로 정의합니다. 요청 트리거의 경우 `Request Body JSON Schema`를 다음 JSON으로 채웁니다.

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

워크플로는 다음 예제와 같습니다.

![성공 전자 메일 워크플로](media/tutorial-control-flow/success-email-workflow-trigger.png)

이 JSON 콘텐츠는 이전 섹션에서 만든 `EmailRequest` 클래스와 일치합니다.

`Office 365 Outlook – Send an email` 작업을 추가합니다. **이메일 보내기** 작업의 경우 요청 **본문** JSON 스키마에 전달된 속성을 사용하여 이메일의 형식을 지정하는 방법을 사용자 지정합니다. 예를 들면 다음과 같습니다.

![논리 앱 디자이너 - 이메일 보내기 작업](media/tutorial-control-flow/customize-send-email-action.png)

워크플로가 저장되면 트리거에서 **HTTP POST URL** 값을 복사하여 저장합니다.

## <a name="fail-email-workflow"></a>실패 전자 메일 워크플로

**CopySuccessEmail**을 *CopyFailEmail*이라는 다른 Logic Apps 워크플로로 복제합니다. 요청 트리거에서 `Request Body JSON schema`은 동일합니다. `Subject`와 같은 전자 메일의 형식을 변경하여 실패 전자 메일에 맞게 조정합니다. 다음은 예제입니다.

![논리 앱 디자이너 - 실패 이메일 워크플로](media/tutorial-control-flow/fail-email-workflow.png)

워크플로가 저장되면 트리거에서 **HTTP POST URL** 값을 복사하여 저장합니다.

이제 다음 예제와 같이 두 개의 워크플로 URL이 있습니다.

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>파이프라인 만들기

Visual Studio에서 프로젝트로 돌아갑니다. 이제 복사 활동과 `DependsOn` 속성을 사용하여 파이프라인을 만드는 코드를 추가합니다. 이 자습서에서 파이프라인에는 Blob 데이터 세트를 원본으로 사용하고 다른 Blob 데이터 세트를 싱크로 사용하는 복사 활동인 하나의 활동이 포함됩니다. 복사 활동이 성공하거나 실패하면 다른 이메일 작업을 호출합니다.

이 파이프라인에서 사용하는 기능은 다음과 같습니다.

* 매개 변수
* 웹 활동
* 작업 종속성
* 활동의 출력을 다른 활동의 입력으로 사용

1. 이 메서드를 프로젝트에 추가합니다. 다음 섹션에서는 더 자세히 설명합니다.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. 파이프라인을 만드는 `Main` 메서드에 다음 코드를 추가합니다.

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>매개 변수

파이프라인 코드의 첫 번째 섹션에서는 매개 변수를 정의합니다.

* `sourceBlobContainer`입니다. 원본 Blob 데이터 세트는 파이프라인에서 이 매개 변수를 사용합니다.
* `sinkBlobContainer`입니다. 싱크 Blob 데이터 세트는 파이프라인에서 이 매개 변수를 사용합니다.
* `receiver`입니다. 성공 또는 실패 이메일을 받는 사람에게 보내는 파이프라인의 두 웹 활동에서 이 매개 변수를 사용합니다.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>웹 활동

웹 활동은 모든 REST 엔드포인트에 대한 호출을 허용합니다. 활동에 대한 자세한 내용은 [Azure Data Factory의 웹 활동](control-flow-web-activity.md)을 참조하세요. 다음 파이프라인은 웹 활동을 사용하여 Logic Apps 이메일 워크플로를 호출합니다. 두 개의 웹 활동, 즉 `CopySuccessEmail` 워크플로를 호출하는 활동과 `CopyFailWorkFlow`를 호출하는 활동을 만듭니다.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

`Url` 속성에서 Logic Apps 워크플로의 **HTTP POST URL** 엔드포인트를 붙여넣습니다. `Body` 속성에서 `EmailRequest` 클래스의 인스턴스를 전달합니다. 전자 메일 요청에 포함되는 속성은 다음과 같습니다.

* Message. `@{activity('CopyBlobtoBlob').output.dataWritten` 값을 전달합니다. 이전 복사 활동의 속성에 액세스하고, `dataWritten` 값을 전달합니다. 실패의 경우 `@{activity('CopyBlobtoBlob').error.message` 대신 오류 출력을 전달합니다.
* Data Factory 이름. `@{pipeline().DataFactory}` 값을 전달합니다. 이 시스템 변수를 사용하면 해당 데이터 팩터리 이름에 액세스할 수 있습니다. 시스템 변수의 목록은 [시스템 변수](control-flow-system-variables.md)를 참조하세요.
* 파이프라인 이름. `@{pipeline().Pipeline}` 값을 전달합니다. 이 시스템 변수를 사용하면 해당 파이프라인 이름에 액세스할 수 있습니다.
* 받는 사람. `"@pipeline().parameters.receiver"` 값을 전달합니다. 파이프라인 매개 변수에 액세스합니다.

이 코드는 이전 복사 활동에 따라 새 활동 종속성을 만듭니다.

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

파이프라인 실행을 트리거하는 `Main` 메서드에 다음 코드를 추가합니다.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main 클래스

최종 `Main` 메서드는 다음과 같습니다.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

파이프라인 실행을 트리거하는 프로그램을 빌드하고 실행합니다!

## <a name="monitor-a-pipeline-run"></a>파이프라인 실행 모니터링

1. `Main` 메서드에 다음 코드를 추가합니다.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    이 코드는 데이터 복사가 완료될 때까지 실행 상태를 계속 확인합니다.

1. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 `Main` 메서드에 다음 코드를 추가합니다.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>코드 실행

애플리케이션을 빌드하고 시작한 다음, 파이프라인 실행을 확인합니다.

애플리케이션에서 데이터 팩터리, 연결된 서비스, 데이터 세트, 파이프라인 및 파이프라인 실행을 만드는 작업에 대한 진행 상황을 표시합니다. 그런 다음 파이프라인 실행 상태를 확인합니다. 데이터를 읽고/쓴 크기가 있는 복사 작업 실행 세부 정보가 표시될 때까지 기다립니다. 그런 다음, Azure Storage Explorer와 같은 도구를 사용하여 변수에 지정한 대로 Blob이 *inputBlobPath*에서 *outputBlobPath*로 복사되었는지 확인합니다.

출력은 다음 샘플과 비슷합니다.

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 데이터 팩터리 만들기
> * Azure Storage 연결된 서비스 만들기
> * Azure Blob 데이터 세트 만들기
> * 복사 작업 및 웹 작업이 포함된 파이프라인 만들기
> * 후속 작업에 작업 출력 보내기
> * 매개 변수 전달 및 시스템 변수 사용
> * 파이프라인 실행 시작
> * 파이프라인 및 작업 실행 모니터링

이제 개념 섹션으로 계속 진행하여 Azure Data Factory에 대해 자세히 알아볼 수 있습니다.
> [!div class="nextstepaction"]
>[파이프라인 및 활동](concepts-pipelines-activities.md)
