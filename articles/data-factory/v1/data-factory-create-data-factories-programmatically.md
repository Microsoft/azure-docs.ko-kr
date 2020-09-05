---
title: Azure .NET SDK를 사용 하 여 데이터 파이프라인 만들기
description: 데이터 팩터리 SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하고, 관리하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c9dce90b0dc563fb0fa7ecaaa0882167eef444a0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019898"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Azure Data Factory .NET SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

## <a name="overview"></a>개요
데이터 팩터리 .NET SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하며, 관리할 수 있습니다. 이 문서에는 데이터 팩터리를 만들고 모니터링하는 샘플 .NET 콘솔 애플리케이션을 만들 수 있는 연습이 포함되어 있습니다. 

> [!NOTE]
> 이 문서는 모든 데이터 팩터리 .NET API를 다루지 않습니다. 데이터 팩터리용 .NET API에 대한 포괄적인 설명서는 [데이터 팩터리 .NET API 참조](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012, 2013 또는 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 설치합니다.
* Azure PowerShell. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell을 사용하여 Azure Active Directory 애플리케이션을 만듭니다.

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션 만들기
Azure Active Directory 애플리케이션을 만든 다음 애플리케이션의 서비스 주체를 만들고 **데이터 팩터리 참가자** 역할에 할당합니다.

1. **PowerShell**을 시작합니다.
2. 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

    ```powershell
    Connect-AzAccount
    ```
3. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzSubscription
    ```
4. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;을 Azure 구독의 이름으로 바꿉니다.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > 이 명령의 출력에서 **SubscriptionId** 및 **TenantId**를 적어둡니다.

5. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup** 이라는 Azure 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    리소스 그룹이 이미 있다면 업데이트(Y)할지 또는 유지(N)할지를 지정합니다.

    다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.
6. Azure Active Directory 애플리케이션을 만듭니다.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    다음과 같은 오류가 발생하면 다른 URL을 지정하고 명령을 다시 실행합니다.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. AD 서비스 주체를 만듭니다.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. **데이터 팩터리 참가자** 역할에 서비스 주체를 추가합니다.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. 애플리케이션 ID를 가져옵니다.

    ```powershell
    $azureAdApplication 
    ```
    출력에서 애플리케이션 ID(applicationID)를 적어둡니다.

이 단계에서 다음과 같은 4가지 값이 있어야 합니다.

* 테넌트 ID
* 구독 ID
* 애플리케이션 UI
* (첫 번째 명령에 지정된)암호

## <a name="walkthrough"></a>연습
이 연습에서는 복사 작업이 포함된 파이프라인으로 데이터 팩터리를 만듭니다. 복사 작업은 Azure Blob Storage의 폴더에서 동일한 Blob 스토리지에 있는 다른 폴더로 데이터를 복사합니다. 

복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.

1. Visual Studio 2012/2013/2015를 사용하여 C# .NET 콘솔 애플리케이션을 만듭니다.
   1. **Visual Studio** 2012/2013/2015를 실행합니다.
   2. **File**을 클릭하고 **New**를 가리킨 다음 **프로젝트**를 클릭합니다.
   3. **템플릿**을 확장하고 **Visual C#** 를 선택합니다. 이 연습에서는 C#을 사용하지만 모든 .NET 언어를 사용할 수 있습니다.
   4. 오른쪽의 프로젝트 형식 목록에서 **콘솔 애플리케이션**을 선택합니다.
   5. 이름에 **DataFactoryAPITestApp** 을 입력합니다.
   6. **C:\ADFGetStarted**를 [위치]로 선택합니다.
   7. **확인**을 클릭하여 프로젝트를 만듭니다.
2. **도구**를 클릭하고 **NuGet 패키지 관리자**를 가리킨 다음 **패키지 관리자 콘솔**을 클릭합니다.
3. **패키지 관리자 콘솔**에서 다음 단계를 수행합니다.
   1. 다음 명령을 실행하여 Data Factory 패키지를 설치합니다. `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Azure Active Directory 패키지를 설치하려면 다음 명령을 실행합니다(코드에서 Active Directory API를 사용함). `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. 프로젝트의 **App.config** 파일 콘텐츠를 다음 콘텐츠로 바꿉니다. 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. App.Config 파일에서 ** &lt; 응용 프로그램 &gt; id**, ** &lt; 암호 &gt; **, ** &lt; 구독 id &gt; **및 ** &lt; 테 넌 트 id &gt; ** 의 값을 고유한 값으로 업데이트 합니다.
6. 프로젝트의 **Program.cs** 파일에 다음 **using** 문을 추가합니다.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. **DataPipelineManagementClient** 클래스의 인스턴스를 만드는 다음 코드를 **Main** 메서드에 추가합니다. 이 개체를 사용하여 데이터 팩터리, 연결된 서비스, 입력 및 출력 데이터 세트와 파이프라인을 만듭니다. 또한 이 개체를 사용하여 런타임 시 데이터 세트의 조각을 모니터링합니다.

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > **resourceGroupName** 값을 Azure 리소스 그룹의 이름으로 바꿉니다. [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다.
   >
   > 데이터 팩터리 이름(dataFactoryName)을 고유한 이름으로 업데이트합니다. 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
7. **데이터 팩터리**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. **Azure Storage 연결된 서비스**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

   > [!IMPORTANT]
   > **storageaccountname** 및 **accountkey**를 Azure Storage 계정의 이름 및 키로 바꿉니다.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. **입력 및 출력 데이터 세트**를 만드는 다음 코드를 **Main** 메서드에 추가합니다.

    입력 blob에 대 한 **FolderPath** 는 **adftutorial/** 로 설정 됩니다. 여기서 **adftutorial** 은 blob storage에 있는 컨테이너의 이름입니다. 이 컨테이너가 Azure blob storage에 없으면 **adftutorial** 이름을 사용 하 여 컨테이너를 만들고 텍스트 파일을 컨테이너에 업로드 합니다.

    출력 Blob의 FolderPath는 **adftutorial/apifactoryoutput/{Slice}** 로 설정됩니다. 여기서 **Slice**는 **SliceStart**(각 조각의 시작 날짜-시간) 값을 기반으로 동적으로 계산됩니다.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. **파이프라인을 만들고 활성화**하는 다음 코드를 **Main** 메서드에 추가합니다. 이 파이프라인에는 **BlobSource**를 원본으로 사용하고 **BlobSink**를 싱크로 사용하는 **CopyActivity**가 포함되어 있습니다.

    복사 작업은 Azure Data Factory에서 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 참조하세요.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. **Main** 메서드에 다음 코드를 추가하여 출력 데이터 세트의 데이터 조각 상태를 가져옵니다. 이 샘플에서는 한 개의 조각만 필요합니다.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(선택 사항)** 데이터 조각의 실행 정보를 가져오는 다음 코드를 **Main** 메서드에 추가합니다.

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. **Main** 메서드에서 사용하는 다음 도우미 메서드를 **Program** 클래스에 추가합니다. 이 메서드는 Azure Portal에 로그인하는 데 사용하는 **사용자 이름** 및 **암호**를 입력할 수 있는 대화 상자를 표시합니다.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. 솔루션 탐색기에서 프로젝트: **DataFactoryAPITestApp**를 확장 하 고 **참조**를 마우스 오른쪽 단추로 클릭 한 다음 **참조 추가**를 클릭 합니다. `System.Configuration` 어셈블리에 대한 확인란을 선택하고 **확인**을 클릭합니다.
15. 콘솔 애플리케이션을 빌드합니다. 메뉴에서 **빌드**를 클릭하고 **솔루션 빌드**를 클릭합니다.
16. 이 시작 자습서에서는 Microsoft Azure File Storage 사용에 대한 기본 사항을 설명합니다. 그렇지 않은 경우 메모장에서 다음 내용이 포함된 Emp.txt 파일을 만들어 adftutorial 컨테이너에 업로드합니다.

    ```
    John, Doe
    Jane, Doe
    ```
17. 메뉴에서 **디버그** -> **디버깅 시작**을 클릭하여 샘플을 실행합니다. **데이터 조각의 실행 정보 가져오기**가 표시되면 몇 분 동안 기다린 다음 **ENTER** 키를 누릅니다.
18. Azure 포털을 사용하여 데이터 팩터리 **APITutorialFactory** 가 다음 아티팩트로 생성되었는지 확인합니다.
    * 연결된 서비스: **AzureStorageLinkedService**
    * 데이터 세트: **DatasetBlobSource** 및 **DatasetBlobDestination**
    * 파이프라인: **PipelineBlobSample**
19. 출력 파일이 **adftutorial** 컨테이너의 **apifactoryoutput** 폴더에 만들어졌는지 확인 합니다.

## <a name="get-a-list-of-failed-data-slices"></a>실패한 데이터 조각 목록 가져오기 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>다음 단계
Azure blob 저장소에서 Azure SQL Database로 데이터를 복사 하는 .NET SDK를 사용 하 여 파이프라인을 만드는 다음 예제를 참조 하세요. 

- [Blob Storage에서 SQL Database로 데이터를 복사하는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-dotnet-api.md)
