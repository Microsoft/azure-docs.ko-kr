---
title: .NET을 사용 하 여 중재 작업 사용-Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator .NET SDK를 사용 하 여 Azure Content Moderator에서 이미지 또는 텍스트 콘텐츠에 대 한 종단 간 콘텐츠 중재 작업을 시작 합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ec101786f33aa6f2525d685993d6b6c891ab2e9a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936319"
---
# <a name="define-and-use-moderation-jobs-net"></a>중재 작업 정의 및 사용 (.NET)

중재 작업은 콘텐츠 조정, 워크플로 및 검토 기능을 위한 일종의 래퍼 역할을 합니다. 이 가이드는 [.net 용 CONTENT MODERATOR SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 를 사용 하 여 시작 하는 데 도움이 되는 정보와 코드 샘플을 제공 합니다.

- 검사할 조정 작업을 시작하고 중재자를 위한 검토 만들기
- 보류 중인 검토의 상태 가져오기
- 검토의 최종 상태 추적 및 가져오기
- 검토 결과를 콜백 URL에 제출 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>API 키에서 검토 만들기에 대한 검토 API를 호출할 수 있는지 확인

이전 단계를 완료한 후 Azure Portal에서 시작한 경우 두 개의 Content Moderator 키가 생성됩니다.

SDK 샘플에서 Azure가 제공한 API 키를 사용하려는 경우 [검토 API를 사용하여 Azure 키 사용](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) 섹션에서 언급된 단계를 수행하여 애플리케이션에서 검토 API를 호출하고 검토를 만들도록 허용합니다.

검토 도구에서 생성된 평가판 키를 사용하는 경우 검토 도구 계정은 키에 대해 이미 알고 있으므로 추가 단계가 필요하지 않습니다.

## <a name="define-a-custom-moderation-workflow"></a>사용자 지정 조정 워크플로 정의

중재 작업은 API를 사용하여 콘텐츠를 검사하고 **워크플로**를 사용하여 검토를 만들지 여부를 결정합니다.
검토 도구에는 기본 워크플로가 포함되지만, 이 빠른 시작에서는 [사용자 지정 워크플로를 정의](Review-Tool-User-Guide/Workflows.md)해보겠습니다.

조정 작업을 시작하는 코드에서 워크플로의 이름을 사용합니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **CreateReviews**로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 수정합니다.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator 클라이언트 만들기

다음 코드를 추가하여 구독에 대한 Content Moderator 클라이언트를 만듭니다.

> [!IMPORTANT]
> 끝점 URL 및 구독 키의 값으로 **Azureendpoint** 및 **cmsubscriptionkey** 필드를 업데이트 합니다.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>애플리케이션 관련 설정 초기화

Program.cs의 **Program** 클래스에 다음 상수 및 정적 필드를 추가합니다.

> [!NOTE]
> TeamName 상수를 Content Moderator 구독을 만들 때 사용한 이름으로 설정합니다. Content Moderator 웹 사이트에서 TeamName을 검색합니다.
> 로그인하면 **설정**(톱니 모양) 메뉴에서 **자격 증명**을 선택합니다.
>
> 팀 이름은 **API** 섹션에서 **Id** 필드의 값입니다.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>자동 조정에 코드 추가, 검토 만들기 및 작업 세부 정보 가져오기

> [!Note]
> 실제로 콜백 URL **CallbackEndpoint**를 수동 검토의 결과를 받는(HTTP POST 요청을 통해) URL로 설정합니다.

**Main** 메서드에 다음 코드를 추가하여 시작합니다.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있습니다. 제한을 초과하는 경우 SDK는 429 오류 코드로 예외를 throw합니다.
>
> 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

콘솔에서 다음과 같은 예제 출력이 표시됩니다.

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Content Moderator 검토 도구에 로그인하여 보류 중인 이미지 검토를 확인합니다.

**다음** 단추를 사용하여 제출합니다.

![사용자 중재자를 위한 이미지 검토](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>로그 파일에서 예제 출력 확인

> [!NOTE]
> 출력 파일에서 **Teamname**, **ContentId**, **CallBackEndpoint** 및 **WorkflowId** 문자열은 이전에 사용한 값을 반영합니다.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>콜백 Url(제공되는 경우)은 이 응답을 받습니다.

다음 예제와 같은 응답이 표시됩니다.

> [!NOTE]
> 사용자의 콜백 응답에서 **ContentId** 및 **WorkflowId** 문자열은 이전에 사용한 값을 반영합니다.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져오고 통합을 시작합니다.
