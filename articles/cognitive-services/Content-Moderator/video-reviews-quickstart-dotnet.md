---
title: .NET을 사용하여 비디오 검토 만들기 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 이 문서에서 제공하는 정보 및 코드 샘플을 통해 C#과 함께 Content Moderator SDK 사용을 빠르게 시작하여 비디오 검토를 만들 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b2fb06c838de480bb73501307ab11cb3d6831921
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919321"
---
# <a name="create-video-reviews-using-net"></a>.NET을 사용하여 비디오 검토 만들기

이 문서에서 제공하는 정보 및 코드 샘플을 통해 [C#과 함께 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 사용을 빠르게 시작하여 아래의 작업을 수행할 수 있습니다.

- 사용자 중재자를 위한 비디오 검토 만들기
- 검토에 프레임 추가
- 검토에 대한 프레임 가져오기
- 검토의 상태 및 세부 정보 가져오기
- 검토 게시

## <a name="prerequisites"></a>필수 구성 요소

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.
- 이 문서에서는 [비디오를 조정(빠른 시작 참조)](video-moderation-api.md)했고 응답 데이터를 보유하고 있다고 가정합니다. 사용자 중재자를 위한 프레임 기반 검토를 만드는 데 필요합니다.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>API 키에서 검토 만들기에 대한 검토 API를 호출할 수 있는지 확인

이전 단계를 완료한 후 Azure Portal에서 시작한 경우 두 개의 Content Moderator 키가 생성됩니다.

SDK 샘플에서 Azure가 제공한 API 키를 사용하려는 경우 [검토 API를 사용하여 Azure 키 사용](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) 섹션에서 언급된 단계를 수행하여 애플리케이션에서 검토 API를 호출하고 검토를 만들도록 허용합니다.

검토 도구에서 생성된 평가판 키를 사용하는 경우 검토 도구 계정은 키에 대해 이미 알고 있으므로 추가 단계가 필요하지 않습니다.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>검토에 대한 비디오 및 비디오 프레임 준비

해당 URL이 필요하기 때문에 검토할 비디오 및 샘플 비디오 프레임을 온라인에 게시해야 합니다.

> [!NOTE]
> 프로그램은 임의 성인/외설 점수가 있는 비디오에서 수동으로 저장된 스크린샷을 사용하여 검토 API의 사용을 나타냅니다. 실제 상황에서는 [비디오 조정 출력](video-moderation-api.md#run-the-program-and-review-the-output)을 사용하여 이미지를 만들고 점수를 할당합니다. 

비디오의 경우 검토 도구가 플레이어 보기에서 비디오를 재생하도록 스트리밍 엔드포인트가 필요합니다.

![비디오 데모 미리 보기](images/ams-video-demo-view.PNG)

- 이 [Azure Media Services 데모](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) 페이지에서 매니페스트 URL의 **URL**을 복사합니다.

비디오 프레임(이미지)의 경우 다음 이미지를 사용합니다.

![비디오 프레임 미리 보기 1](images/ams-video-frame-thumbnails-1.PNG) | ![비디오 프레임 미리 보기 2](images/ams-video-frame-thumbnails-2.PNG) | ![비디오 프레임 미리 보기 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[프레임 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [프레임 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [프레임 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

1. 프로젝트 이름을 **VideoReviews**로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

TermLists 프로젝트에 대해 다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 다음과 같이 수정합니다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>프라이빗 속성 추가

네임 스페이스 **VideoReviews**클래스 **프로그램**에 다음 개인 속성을 추가 합니다. `AzureEndpoint`및 필드를 `CMSubscriptionKey` 끝점 URL 및 구독 키의 값으로 업데이트 합니다. Azure Portal에서 리소스의 **빠른 시작** 탭에서 찾을 수 있습니다.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Content Moderator 클라이언트 개체 만들기

네임 스페이스 **VideoReviews**클래스 **프로그램**에 다음 메서드 정의를 추가 합니다.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>비디오 검토 만들기

**ContentModeratorClient.Reviews.CreateVideoReviews**를 사용하여 비디오 검토를 만듭니다. 자세한 내용은 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)를 참조 하세요.

**CreateVideoReviews**에는 다음 매개 변수가 필요합니다.
1. MIME 형식을 포함하는 문자열로, “application/json”이어야 합니다. 
1. Content Moderator 팀 이름입니다.
1. **IList\<CreateVideoReviewsBodyItem>** 개체입니다. 각 **CreateVideoReviewsBodyItem** 개체는 비디오 검토를 나타냅니다. 이 빠른 시작에서는 한 번에 하나씩 검토를 만듭니다.

**CreateVideoReviewsBodyItem**에는 여러 속성이 있습니다. 최소한 다음 속성을 설정합니다.
- **콘텐츠**. 검토할 비디오의 URL입니다.
- **ContentId**. 비디오 검토에 할당할 ID입니다.
- **상태**. 값을 "게시 취소됨"으로 설정합니다. 값을 설정하지 않을 경우 기본값인 "보류 중"으로 설정되며, 이는 비디오 검토가 게시되었으며 사용자 검토 보류 중임을 의미합니다. 비디오 검토가 게시되고 나면 비디오 프레임, 대본 또는 대본 조정 결과를 더 이상 추가할 수 없습니다.

> [!NOTE]
> **CreateVideoReviews**는 IList\<string>을(를) 반환합니다. 이러한 각 문자열에는 비디오 검토의 ID가 포함되어 있습니다. 이러한 ID는 GUID이며, **ContentId** 속성 값과 동일하지 않습니다. 

VideoReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있으며, 제한을 초과하는 경우 SDK에서 429 오류 코드로 예외를 throw합니다.
>
> 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.

## <a name="add-video-frames-to-the-video-review"></a>비디오 검토에 비디오 프레임 추가

**ContentModeratorClient.Reviews.AddVideoFrameUrl**(비디오 프레임이 온라인에서 호스팅되는 경우) 또는 **ContentModeratorClient.Reviews.AddVideoFrameStream**(비디오 프레임이 로컬로 호스팅되는 경우)을 사용하여 비디오 검토에 비디오 프레임을 추가합니다. 이 빠른 시작은 비디오 프레임이 온라인으로 호스팅된다고 가정하므로 **AddVideoFrameUrl**을 사용합니다. 자세한 내용은 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd)를 참조 하세요.

**AddVideoFrameUrl**에는 다음 매개 변수가 필요합니다.
1. MIME 형식을 포함하는 문자열로, “application/json”이어야 합니다.
1. Content Moderator 팀 이름입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.
1. **IList\<VideoFrameBodyItem>** 개체입니다. 각 **VideoFrameBodyItem** 개체는 비디오 프레임을 나타냅니다.

**VideoFrameBodyItem**에는 다음 속성이 있습니다.
- **타임 스탬프**. 비디오 프레임이 사용된 비디오의 시간(초)을 포함하는 문자열입니다.
- **FrameImage**. 비디오 프레임의 URL입니다.
- **메타 데이터**. IList\<VideoFrameBodyItemMetadataItem>입니다. **VideoFrameBodyItemMetadataItem**은 단순히 키/값 쌍입니다. 올바른 키는 다음과 같습니다.
- **reviewRecommended**. 비디오 프레임의 사용자 검토가 권장되는 경우 True입니다.
- **adultScore**. 비디오 프레임에서 성인 콘텐츠의 심각도 등급을 지정하는 0~1의 값입니다.
- **입니다.** 비디오가 성인 콘텐츠를 포함하는 경우 True입니다.
- **racyScore**. 비디오 프레임에서 외설 콘텐츠의 심각도 등급을 지정하는 0~1의 값입니다.
- **r**. 비디오 프레임이 외설 콘텐츠를 포함하는 경우 True입니다.
- **ReviewerResultTags**. IList\<VideoFrameBodyItemReviewerResultTagsItem>입니다. **VideoFrameBodyItemReviewerResultTagsItem**은 단순히 키/값 쌍입니다. 애플리케이션은 이러한 태그를 사용하여 비디오 프레임을 구성할 수 있습니다.

> [!NOTE]
> 이 빠른 시작은 **adultScore** 및 **racyScore** 속성에 대한 임의 값을 생성합니다. 프로덕션 애플리케이션에서 Azure 미디어 서비스로 배포된 [비디오 조정 서비스](video-moderation-api.md)에서 이러한 값을 가져옵니다.

VideoReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>비디오 검토를 위해 비디오 프레임 가져오기

**ContentModeratorClient.Reviews.GetVideoFrames**를 사용하여 비디오 검토를 위해 비디오 프레임을 가져올 수 있습니다. **GetVideoFrames**에는 다음 매개 변수가 필요합니다.
1. Content Moderator 팀 이름입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.
1. 가져올 첫 번째 비디오 프레임의 0부터 시작하는 인덱스입니다.
1. 가져올 비디오 프레임의 수입니다.

VideoReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>비디오 검토 정보 가져오기

**ContentModeratorClient.Reviews.GetReview**를 사용하여 비디오 검토를 위한 정보를 가져옵니다. **GetReview**에는 다음 매개 변수가 필요합니다.
1. Content Moderator 팀 이름입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.

VideoReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>비디오 검토 게시

**ContentModeratorClient.Reviews.PublishVideoReview**를 사용하여 비디오 검토를 게시합니다. **PublishVideoReview**에는 다음 매개 변수가 필요합니다.
1. Content Moderator 팀 이름입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.

VideoReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>모든 항목 요약

VideoReviews 네임스페이스, Program 클래스에 **Main** 메서드 정의를 추가합니다. 마지막으로, Program 클래스 및 VideoReviews 네임스페이스를 닫습니다.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토
애플리케이션을 실행하면 다음 줄에 출력이 표시됩니다.

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>비디오 검토 확인

마지막으로 비디오 **검토**화면에서 Content Moderator 검토 도구 계정에 비디오 검토를 표시 합니다 > **Video** .

![사용자 중재자를 위한 비디오 검토](images/ams-video-review.PNG)

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져옵니다.

비디오 검토에 [기록 조정](video-transcript-moderation-review-tutorial-dotnet.md)을 추가하는 방법을 알아봅니다. 

[완전한 비디오 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 개발하는 방법에 대한 자세한 자습서를 살펴봅니다.
