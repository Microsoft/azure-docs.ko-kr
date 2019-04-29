---
title: Media Services에서 Azure Functions 개발
description: 이 항목에서는 Azure Portal을 사용하여 Media Services에서 Azure Functions를 개발하기 시작하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217509"
---
# <a name="develop-azure-functions-with-media-services"></a>Media Services에서 Azure Functions 개발

이 문서에서는 Media Services를 사용하여 Azure Functions 만들기를 시작하는 방법을 보여 줍니다. 이 문서에 정의된 Azure 함수는 새 MP4 파일에 대한 저장소 계정 컨테이너 **input**을 모니터링합니다. 파일이 저장소 컨테이너에 포함되면 blob 트리거가 함수를 실행합니다. Azure Functions를 검토하려면 **Azure Functions** 섹션에서 [개요](../../azure-functions/functions-overview.md) 및 기타 항목을 참조하세요.

Azure Media Services를 사용하는 기존 Azure Functions를 탐색하고 배포하려는 경우 [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)를 확인하세요. 이 리포지토리는 Blob Storage에서 직접 콘텐츠를 수집하고 Blob Storage에 콘텐츠를 인코딩 및 작성하는 데 관련된 워크플로를 표시하는 데 Media Services를 사용하는 예제를 포함합니다. 또한 WebHooks 및 Azure 큐를 통해 작업 알림을 모니터링하는 방법의 예도 포함되어 있습니다. [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) 리포지토리의 예제를 기반으로 함수를 개발할 수도 있습니다. 함수를 배포하려면 **Azure에 배포** 단추를 누릅니다.

## <a name="prerequisites"></a>필수 조건

- 첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [체험 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.
- AMS(Azure Media Services) 계정에서 작업을 수행하거나 Media Services에서 보낸 이벤트를 수신 대기하는 Azure Functions를 만들려는 경우 [여기](media-services-portal-create-account.md)에 설명한 대로 AMS 계정을 만들어야 합니다.
    
## <a name="create-a-function-app"></a>함수 앱 만들기

1. [Azure Portal](https://portal.azure.com) 로 이동하여 Azure 계정으로 로그인합니다.
2. [여기](../../azure-functions/functions-create-function-app-portal.md)에 설명한 대로 함수 앱을 만듭니다.

>[!NOTE]
> **StorageConnection** 환경 변수에 지정(다음 단계 참조)한 스토리지 계정은 앱과 동일한 지역에 있어야 합니다.

## <a name="configure-function-app-settings"></a>함수 앱 구성 설정

Media Services 함수를 개발하는 경우 함수 전체에서 사용할 환경 변수를 쉽게 추가할 수 있습니다. 앱 설정을 구성하려면 앱 설정 구성 링크를 클릭합니다. 자세한 내용은 [Azure 함수 앱 설정을 구성하는 방법](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)을 참조하세요. 

이 문서에 정의된 함수는 앱 설정에 다음 환경 변수가 있다고 가정합니다.

**AMSAADTenantDomain**: Azure AD 테넌트 엔드포인트. AMS API 연결에 대한 자세한 내용은 [이](media-services-use-aad-auth-to-access-ams-api.md) 문서를 참조하세요.

**AMSRESTAPIEndpoint**:  REST API 엔드포인트를 나타내는 URI입니다. 

**AMSClientId**: Azure AD 애플리케이션 클라이언트 ID입니다.

**AMSClientSecret**: Azure AD 애플리케이션 클라이언트 비밀입니다.

**StorageConnection**: Media Services 계정과 연결된 계정의 스토리지 연결입니다. 이 값은 **function.json** 파일 및 **run.csx** 파일(아래 설명 참조)에서 사용됩니다.

## <a name="create-a-function"></a>함수 만들기

함수 앱을 배포하면 **App Services** Azure Functions에서 찾을 수 있습니다.

1. 함수 앱을 선택하고 **새 함수**를 클릭합니다.
2. **C#** 언어 및 **데이터 처리** 시나리오를 선택합니다.
3. **BlobTrigger** 템플릿을 선택합니다. 이 함수는 Blob이 **input** 컨테이너에 업로드될 때마다 트리거됩니다. **input** 이름은 다음 단계에서 **Path**에 지정됩니다.

    ![업로드](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. **BlobTrigger**를 선택하면 페이지에 몇 개의 추가 컨트롤이 표시됩니다.

    ![업로드](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. **만들기**를 클릭합니다. 

## <a name="files"></a>파일

Azure Function은 이 섹션에 설명된 코드 파일 및 기타 파일과 연결됩니다. Azure Portal을 사용하여 함수를 만들 경우 **function.json** 및 **run.csx**가 자동으로 만들어집니다. **project.json** 파일을 추가하고 업로드해야 합니다. 이 섹션의 나머지 부분에서는 각 파일을 간략하게 설명하고 해당 정의를 표시합니다.

![업로드](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json 파일은 함수 바인딩 및 기타 구성 설정을 정의합니다. 런타임은 이 파일을 사용하여 모니터링할 이벤트와 함수 실행에서 데이터를 전달하고 반환하는 방법을 결정합니다. 자세한 내용은 [Azure Functions HTTP 및 웹후크 바인딩](../../azure-functions/functions-reference.md#function-code)을 참조하세요.

>[!NOTE]
>함수가 실행되지 않도록 **disabled** 속성을 **true**로 설정합니다. 

기존 function.json 파일의 내용을 다음 코드로 바꿉니다.

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

project.json 파일은 종속성을 포함합니다. 다음은 Nuget의 필수 .NET Azure Media Services 패키지를 포함하는 **project.json** 파일의 예입니다. 버전 번호가 패키지의 최신 업데이트로 변경되므로 가장 최근 버전을 확인해야 합니다. 

project.json에 다음과 같은 정의를 추가합니다. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

함수에 대한 C# 코드입니다.  아래 정의된 함수는 새 MP4 파일에 대한 저장소 계정 컨테이너 **input**(경로에 지정됨)을 모니터링합니다. 파일이 저장소 컨테이너에 포함되면 blob 트리거가 함수를 실행합니다.
    
이 섹션에 정의된 예제는 다음을 보여 줍니다. 

1. AMS 자산에 blob을 복사하여 Media Services 계정에 자산을 수집하는 방법 
2. Media Encoder Standard의 “적응 스트리밍” 사전 설정을 사용하는 인코딩 작업을 제출하는 방법

실제 시나리오에서는 작업 진행률을 추적한 다음 인코딩된 자산을 게시할 가능성이 높습니다. 자세한 내용은 [Azure 웹후크를 사용하여 Media Services 작업 알림 모니터링](media-services-dotnet-check-job-progress-with-webhooks.md)을 참조하세요. 더 많은 예제는 [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)를 참조하세요.  

기존 run.csx 파일의 내용을 다음 코드로 바꿉니다. 함수를 정의했으면 **저장 및 실행**을 클릭합니다.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>함수 테스트

함수를 테스트하려면 연결 문자열에 지정한 저장소 계정의 **input** 컨테이너에 MP4 파일을 업로드해야 합니다.  

1. **StorageConnection** 환경 변수에 지정된 스토리지 계정을 선택합니다.
2. **Blob**을 클릭합니다.
3. **+ 컨테이너**를 클릭합니다. 컨테이너 **입력**의 이름을 지정합니다.
4. **업로드**를 눌러 업로드하려는 .mp4 파일을 찾습니다.

>[!NOTE]
> 소비 계획에서 Blob 트리거를 사용하는 경우 함수 앱이 유휴 상태가 된 후 새 Blob을 처리하는 데 최대 10분이 지연될 수 있습니다. 함수 앱이 실행된 후에는 Blob이 즉시 처리됩니다. 자세한 내용은 [Blob Storage 트리거 및 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Media Services 애플리케이션 개발을 시작할 준비가 되었습니다. 
 
Azure Media Services에서 Azure Functions 및 Logic Apps를 사용하여 사용자 지정 콘텐츠 만들기 워크플로를 만드는 방법에 대한 자세한 내용 및 전체 샘플/솔루션은 [GitHub의 Media Services .NET 함수 통합 샘플](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)을 참조하세요.

또한 [Azure 웹후크를 사용하여 .NET으로 Media Services 작업 알림 모니터링](media-services-dotnet-check-job-progress-with-webhooks.md)을 참조하세요. 

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

