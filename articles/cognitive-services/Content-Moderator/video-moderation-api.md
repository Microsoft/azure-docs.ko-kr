---
title: C#에서 비디오 콘텐츠의 유해한 자료 분석 - Content Moderator
titleSuffix: Azure Cognitive Services
description: .NET용 Content Moderator SDK를 사용하여 다양하고 불쾌한 자료에 대한 텍스트 비디오 콘텐츠를 분석하는 방법
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b389841bdba107ba27371387d4a6e5d1f009d41
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919355"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>C#에서 불쾌한 자료에 대한 텍스트 콘텐츠 분석

이 문서에서는 [.NET용 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)를 사용하여 성인 또는 성적 콘텐츠에 대한 비디오 콘텐츠를 검사하는 데 도움이 되는 정보와 코드 샘플을 제공합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

Content Moderator의 비디오 조정 기능은 AMS(Azure Media Services)에서 무료 공개 미리 보기 **미디어 프로세서**로 사용할 수 있습니다. Azure Media Services는 비디오 콘텐츠 저장 및 스트리밍에 대한 전문 Azure 서비스입니다. 

### <a name="create-an-azure-media-services-account"></a>Azure Media Services 계정 만들기

[Azure Media Services 계정 만들기](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account)의 지침에 따라 AMS를 구독하고 연결된 Azure Storage 계정을 만듭니다. 해당 스토리지 계정에서 새 Blob 스토리지 컨테이너를 만듭니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

Azure Portal에서 새 AMS 구독으로 이동한 후 측면 메뉴에서 **API 액세스**를 선택합니다. **서비스 주체를 사용하여 Azure Media Services에 연결**을 선택합니다. 나중에 필요하므로 **REST API 엔드포인트** 필드의 값을 기록합니다.

**Azure AD 앱** 섹션에서 **새로 만들기**를 선택하고 새 Azure AD 애플리케이션 등록에 이름을 지정합니다(예: "VideoModADApp"). **저장**을 클릭하고 애플리케이션이 구성되는 동안 몇 분 기다립니다. 그런 다음, 페이지의 **Azure AD 앱** 섹션에 새로운 앱 등록이 표시됩니다.

앱 등록을 선택하고 아래의 **애플리케이션 관리** 단추를 클릭합니다. 나중에 필요하므로 **애플리케이션 ID** 필드의 값을 기록합니다. **설정**  >  **키**를 선택 하 고 새 키에 대 한 설명 (예: "videomodkey")을 입력 합니다. **저장**을 클릭하면 새 키 값이 나타납니다. 이 문자열을 복사하고 안전한 곳에 저장합니다.

위 프로세스에 대한 자세한 안내는 [Azure AD 인증 시작](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)을 참조하세요.

이 작업을 수행하고 나면 비디오 조정 미디어 프로세서를 두 가지 다른 방법으로 사용할 수 있습니다.

## <a name="use-azure-media-services-explorer"></a>Azure Media Services 탐색기 사용

Azure Media Services 탐색기는 AMS에 대한 사용자 친화적인 프런트 엔드입니다. 이 탐색기를 사용하여 AMS 계정을 찾아보고, 비디오를 업로드하며 Content Moderator 미디어 프로세서로 콘텐츠를 검사할 수 있습니다. [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)에서 다운로드하여 설치하거나, 자세한 내용은 [Azure Media Services Explorer 블로그 게시물](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)을 참조하세요.

![Content Moderator를 사용하는 Azure Media Services 탐색기](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **VideoModeration**으로 이름을 지정합니다. 
1. 솔루션에 다른 프로젝트가 있는 경우 이것을 단일 시작 프로젝트로 선택합니다.
1. 필요한 NuGet 패키지를 가져옵니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 누르고 **NuGet 패키지 관리**를 선택한 후 다음 패키지를 찾아 설치합니다.
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>비디오 조정 코드 추가

다음으로 이 가이드에서 프로젝트로 코드를 복사하여 붙여넣고 기본 콘텐츠 조정 시나리오를 구현합니다.

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

_Program.cs_ 파일 위에 다음 `using` 문을 추가합니다.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>리소스 참조 설정

_Program.cs_의 **Program** 클래스에 다음 정적 필드를 추가 합니다. 이 필드에는 AMS 구독 연결에 필요한 정보가 있습니다. 위의 단계에서 얻은 값으로 입력하세요. `CLIENT_ID`는 Azure AD 앱의 **애플리케이션 ID** 값이고 `CLIENT_SECRET`는 해당 앱에 대해 사용자가 생성한 "VideoModKey"의 값입니다.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

로컬 비디오 파일을 사용하려면(가장 간단한 사례) 프로젝트에 추가하고 `INPUT_FILE` 값으로 경로를 입력합니다(상대 경로는 실행 디렉터리와 상대적임).

또한 현재 디렉터리에 _preset.json_ 파일을 만들고 버전 번호를 지정하기 위해 사용해야 합니다. 예를 들면

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>입력 비디오를 로드합니다.

**Program** 클래스의 **Main** 메서드는 먼저 Azure Media 컨텍스트를 만들고, 비디오가 Blob 스토리지에 있는 경우에는 Azure Storage 컨텍스트를 만듭니다. 코드의 나머지 부분은 Azure Storage 컨테이너 내의 로컬 폴더, Blob 또는 여러 Blob에서 비디오를 검색합니다. 다른 코드 줄을 주석으로 처리하여 모든 옵션을 시도할 수 있습니다.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Azure Media 컨텍스트 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 AMS 자격 증명을 사용하여 AMS와의 통신을 허용합니다.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Azure Storage 컨텍스트를 만드는 코드 추가

**Program** 클래스에 다음 메서드를 추가합니다. 스토리지 자격 증명에서 만든 Storage 컨텍스트를 사용하여 Blob 스토리지에 액세스합니다.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>로컬 파일 및 Blob에서 Azure 미디어 자산을 만드는 코드 추가

Content Moderator 미디어 프로세서는 Azure Media Services 플랫폼 내의 **자산**에 대해 작업을 실행합니다.
이러한 메서드는 로컬 파일 또는 연결된 Blob에서 자산을 만듭니다.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>컨테이너 내에서 비디오 컬렉션(blob으로)을 검색하는 코드 추가

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Content Moderator 작업을 실행하기 위한 메서드 추가

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>도우미 함수 추가

이러한 메서드는 Azure Media Services 자산에서 Content Moderator 출력 파일(JSON)을 다운로드하고 프로그램이 콘솔에 실행 중 상태를 로깅할 수 있도록 조정 작업의 상태를 추적하는 데 도움을 줍니다.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

Content Moderation 작업이 완료되면 JSON 응답을 분석합니다. 응답은 다음 요소로 구성됩니다.

- 비디오 정보 요약
- **Shots**를 "**fragments**"로 지정
- **Key frames**를 **Adult** 및 **Racy** 점수를 기준으로 하는 **reviewRecommended" (= true or false)"** 플래그를 사용하여 "**events**"로 지정
- **start**, **duration**, **totalDuration** 및 **timestamp**는 "ticks"로 지정합니다. **timescale**로 나누어 초 수를 가져옵니다.
 
> [!NOTE]
> - `adultScore`는 잠재적으로 특정 상황에서 성적으로 노골적이거나 성인용으로 간주될 수 있는 콘텐츠의 존재 가능성 및 예측 점수를 나타냅니다.
> - `racyScore`는 잠재적으로 특정 상황에서 성적으로 외설적이거나 도발적인 것으로 간주될 수 있는 콘텐츠의 존재 가능성 및 예측 점수를 나타냅니다.
> - `adultScore` 및 `racyScore`의 범위는 0부터 1 사이입니다. 점수가 높을수록 모델이 예측하는 범주에 해당할 가능성이 높아집니다. 이 미리 보기는 수동으로 코딩된 결과가 아닌 통계 모델을 사용합니다. 고유한 콘텐츠로 테스트하여 각 범주가 요구 사항과 얼마나 일치하는지 확인하는 것이 좋습니다.
> - `reviewRecommended`는 내부 점수 임계값에 따라 true 또는 false입니다. 고객은 이 값을 사용할지 또는 콘텐츠 정책에 따라 사용자 지정 임계값을 결정할지를 평가해야 합니다.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>다음 단계

조정 출력에서 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.

비디오 검토에 [기록 조정](video-transcript-moderation-review-tutorial-dotnet.md)을 추가합니다.

[완전한 비디오 및 기록 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 빌드하는 방법에 대한 자세한 자습서를 살펴봅니다.

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Visual Studio 솔루션을 다운로드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)합니다.
