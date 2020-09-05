---
title: .NET을 사용하여 비디오 대본 만들기 - Content Moderator
titleSuffix: Azure Cognitive Services
description: .NET 용 Azure Cognitive Services Content Moderator SDK를 사용 하 여 비디오 성적 리뷰를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f2d0ddae8a9bd8054c740402b8beb3bb0bccfa9f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919219"
---
# <a name="create-video-transcript-reviews-using-net"></a>.NET을 사용하여 비디오 대본 검토 만들기

이 문서에서 제공하는 정보 및 코드 샘플을 통해 [C#과 함께 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 사용을 빠르게 시작하여 아래의 작업을 수행할 수 있습니다.

- 사용자 중재자를 위한 비디오 검토 만들기
- 검토에 조정된 대본 추가
- 검토 게시

## <a name="prerequisites"></a>필수 구성 요소

- 아직 수행 하지 않은 경우 Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만듭니다.
- 이 문서에서는 사용자 결정을 위한 검토 도구에서 [비디오를 조정](video-moderation-api.md)하고 [비디오 검토를 생성](video-reviews-quickstart-dotnet.md)했다고 가정합니다. 이제 검토 도구에서 조정된 비디오 대본을 추가하려고 합니다.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>API 키에서 검토 API를 호출할 수 있는지 확인(작업 생성)

이전 단계를 완료한 후 Azure Portal에서 시작한 경우 두 개의 Content Moderator 키가 생성됩니다.

SDK 샘플에서 Azure가 제공한 API 키를 사용하려는 경우 [검토 API를 사용하여 Azure 키 사용](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) 섹션에서 언급된 단계를 수행하여 애플리케이션에서 검토 API를 호출하고 검토를 만들도록 허용합니다.

검토 도구에서 생성된 평가판 키를 사용하는 경우 검토 도구 계정은 키에 대해 이미 알고 있으므로 추가 단계가 필요하지 않습니다.

## <a name="prepare-your-video-for-review"></a>검토할 비디오 준비

비디오 검토에 대본을 추가합니다. 비디오를 온라인에 게시해야 합니다. 해당 스트리밍 엔드포인트가 필요합니다. 스트리밍 엔드포인트를 사용하면 검토 도구 비디오 플레이어에서 비디오를 재생할 수 있습니다.

![비디오 데모 미리 보기](images/ams-video-demo-view.PNG)

- 이 [Azure Media Services 데모](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) 페이지에서 매니페스트 URL의 **URL**을 복사합니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

1. 프로젝트의 이름을 **VideoTranscriptReviews**로 지정합니다.

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

다음 개인 속성을 **videorec**# 클래스 **프로그램**의 네임 스페이스에 추가 합니다. `AzureEndpoint`및 필드를 `CMSubscriptionKey` 끝점 URL 및 구독 키의 값으로 업데이트 합니다. Azure Portal에서 리소스의 **빠른 시작** 탭에서 찾을 수 있습니다.

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

VideoTranscriptReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

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
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있습니다. 제한을 초과하는 경우 SDK는 429 오류 코드로 예외를 throw합니다.
>
> 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.

## <a name="add-transcript-to-video-review"></a>비디오 검토에 대본 추가

**ContentModeratorClient.Reviews.AddVideoTranscript**를 사용하여 비디오 검토에 대본을 추가합니다. **AddVideoTranscript**에는 다음 매개 변수가 필요합니다.
1. Content Moderator 팀 ID입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.
1. 대본을 포함하는 **Stream** 개체입니다.

대본은 WebVTT 형식이어야 합니다. 자세한 내용은 [WebVTT: Web Video Text Tracks 형식](https://www.w3.org/TR/webvtt1/)을 참조하세요.

> [!NOTE]
> 프로그램은 VTT 형식의 샘플 대본을 사용합니다. 실제 솔루션에서는 Azure Media Indexer 서비스를 사용하여 비디오에서 [대본을 생성](https://docs.microsoft.com/azure/media-services/media-services-index-content)합니다.

VideotranscriptReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>비디오 검토에 대본 조정 결과 추가

비디오 검토에 대본을 추가하는 것 외에, 해당 대본의 조정 결과도 추가합니다. **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**를 사용하여 이 작업을 수행합니다. 자세한 내용은 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff)를 참조 하세요.

**AddVideoTranscriptModerationResult**에는 다음 매개 변수가 필요합니다.
1. MIME 형식을 포함하는 문자열로, “application/json”이어야 합니다. 
1. Content Moderator 팀 이름입니다.
1. **CreateVideoReviews**에서 반환된 비디오 검토 ID입니다.
1. IList\<TranscriptModerationBodyItem>입니다. **TranscriptModerationBodyItem**에는 다음 속성이 있습니다.
1. **용어**. IList\<TranscriptModerationBodyItemTermsItem>입니다. **TranscriptModerationBodyItemTermsItem**에는 다음 속성이 있습니다.
1. **인덱스**. 용어의 0부터 시작하는 인덱스입니다.
1. **용어**. 용어를 포함하는 문자열입니다.
1. **타임 스탬프**. 대본에서 용어가 발견된 시간(초)을 포함하는 문자열입니다.

대본은 WebVTT 형식이어야 합니다. 자세한 내용은 [WebVTT: Web Video Text Tracks 형식](https://www.w3.org/TR/webvtt1/)을 참조하세요.

VideoTranscriptReviews 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다. 이 메서드는 **ContentModeratorClient.TextModeration.ScreenText** 메서드에 대본을 제출합니다. 또한 결과를 IList\<TranscriptModerationBodyItem>로 변환하고 **AddVideoTranscriptModerationResult**에 제출합니다.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

VideoTranscriptReviews 네임스페이스, Program 클래스에 **Main** 메서드 정의를 추가합니다. 마지막으로, Program 클래스 및 VideoTranscriptReviews 네임스페이스를 닫습니다.

> [!NOTE]
> 프로그램은 VTT 형식의 샘플 대본을 사용합니다. 실제 솔루션에서는 Azure Media Indexer 서비스를 사용하여 비디오에서 [대본을 생성](https://docs.microsoft.com/azure/media-services/media-services-index-content)합니다.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>비디오 대본 검토로 이동

비디오 기록 **검토**화면에서 Content Moderator 검토 도구의 비디오 녹음/녹음/리뷰로 이동 > **Video** > **Transcript** 합니다.

다음 기능이 표시됩니다.
- 추가한 두 줄의 대본
- 텍스트 조정 서비스에서 검색하여 강조 표시한 욕설
- 대본 텍스트를 선택하면 해당 타임스탬프에서 비디오가 시작됨

![사용자 중재자를 위한 비디오 대본 검토](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져옵니다.

검토 도구에서 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.

[완전한 비디오 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 개발하는 방법에 대한 자세한 자습서를 살펴봅니다.
