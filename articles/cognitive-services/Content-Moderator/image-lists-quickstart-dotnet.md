---
title: C#-Content Moderator의 사용자 지정 목록에 대해 이미지 확인
titleSuffix: Azure Cognitive Services
description: C#용 Content Moderator SDK를 사용하여 사용자 지정 이미지 목록으로 이미지를 조정하는 방법입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: bec31f830adddfc7251ce36e13ef0bfaa0af7638
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931712"
---
# <a name="moderate-with-custom-image-lists-in-c"></a>C#에서 사용자 지정 이미지 목록을 사용하여 조정

이 문서에서는 [.NET용 Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)를 사용하여 다음 작업을 수행할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다.
- 사용자 지정 이미지 목록 만들기
- 목록에서 이미지 추가 및 제거
- 목록에서 모든 이미지의 ID 가져오기
- 목록 메타데이터 검색 및 업데이트
- 목록 검색 인덱스 새로 고침
- 목록의 이미지에 대해 이미지 차단
- 목록에서 모든 이미지 삭제
- 사용자 지정 목록 삭제

> [!NOTE]
> 최대 **5개 이미지 목록**으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.

이 가이드의 콘솔 응용 프로그램에서는 이미지 목록 API를 사용 하 여 수행할 수 있는 작업 중 일부를 시뮬레이션 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator 서비스 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 API 구독 키가 필요합니다. 이를 구하려면 Azure Portal에서 [Content Moderator 서비스를 구독](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)합니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **ImageLists**로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

다음 `using` 문을 추가합니다.

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

다음 코드를 추가하여 구독에 대한 Content Moderator 클라이언트를 만듭니다. `AzureEndpoint`및 필드를 `CMSubscriptionKey` 끝점 URL 및 구독 키의 값으로 업데이트 합니다. Azure Portal에서 리소스의 **빠른 시작** 탭에서 찾을 수 있습니다.

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
    /// The base URL for Content Moderator calls.
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

Program.cs의 **Program** 클래스에 다음 클래스 및 정적 필드를 추가합니다.

```csharp
/// <summary>
/// The minimum amount of time, im milliseconds, to wait between calls
/// to the Image List API.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;

/// <summary>
/// Define constants for the labels to apply to the image list.
/// </summary>
private class Labels
{
    public const string Sports = "Sports";
    public const string Swimsuit = "Swimsuit";
}

/// <summary>
/// Define input data for images for this sample.
/// </summary>
private class Images
{
    /// <summary>
    /// Represents a group of images that all share the same label.
    /// </summary>
    public class Data
    {
        /// <summary>
        /// The label for the images.
        /// </summary>
        public string Label;

        /// <summary>
        /// The URLs of the images.
        /// </summary>
        public string[] Urls;
    }

    /// <summary>
    /// The initial set of images to add to the list with the sports label.
    /// </summary>
    public static readonly Data Sports = new Data()
    {
        Label = Labels.Sports,
        Urls = new string[] {
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample9.png"
        }
    };

    /// <summary>
    /// The initial set of images to add to the list with the swimsuit label.
    /// </summary>
    /// <remarks>We're adding sample16.png (image of a puppy), to simulate
    /// an improperly added image that we will later remove from the list.
    /// Note: each image can have only one entry in a list, so sample4.png
    /// will throw an exception when we try to add it with a new label.</remarks>
    public static readonly Data Swimsuit = new Data()
    {
        Label = Labels.Swimsuit,
        Urls = new string[] {
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
            "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
        }
    };

    /// <summary>
    /// The set of images to subsequently remove from the list.
    /// </summary>
    public static readonly string[] Corrections = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
    };
}

/// <summary>
/// The images to match against the image list.
/// </summary>
/// <remarks>Samples 1 and 4 should scan as matches; samples 5 and 16 should not.</remarks>
private static readonly string[] ImagesToScreen = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png"
};

/// <summary>
/// A dictionary that tracks the ID assigned to each image URL when 
/// the image is added to the list.
/// </summary>
/// <remarks>Indexed by URL.</remarks>
private static readonly Dictionary<string, int> ImageIdMap =
    new Dictionary<string, int>();

/// <summary>
/// The name of the file to contain the output from the list management operations.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private static string OutputFile = "ListOutput.log";

/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// A copy of the list details.
/// </summary>
/// <remarks>Used to initially create the list, and later to update the
/// list details.</remarks>
private static Body listDetails;
```
   
> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있으며, 제한을 초과하는 경우 SDK에서 429 오류 코드로 예외를 throw합니다. 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.


## <a name="create-a-method-to-write-messages-to-the-log-file"></a>로그 파일에 메시지를 작성하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-the-custom-list"></a>사용자 지정 목록을 만드는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Creates the custom list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList CreateCustomList(ContentModeratorClient client)
{
    // Create the request body.
    listDetails = new Body("MyList", "A sample list",
        new BodyMetadata("Acceptable", "Potentially racy"));

    WriteLine($"Creating list {listDetails.Name}.", true);

    var result = client.ListManagementImageLists.Create(
        "application/json", listDetails);
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-add-a-collection-of-images-to-the-list"></a>목록에 이미지의 컬렉션을 추가하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 이 가이드에서는 목록의 이미지에 태그를 적용 하는 방법을 보여 주지 않습니다. 

```csharp
/// <summary>
/// Adds images to an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToAdd">The images to add.</param>
/// <param name="label">The label to apply to each image.</param>
/// <remarks>Images are assigned content IDs when they are added to the list.
/// Track the content ID assigned to each image.</remarks>
private static void AddImages(
ContentModeratorClient client, int listId,
IEnumerable<string> imagesToAdd, string label)
{
    foreach (var imageUrl in imagesToAdd)
    {
        WriteLine();
        WriteLine($"Adding {imageUrl} to list {listId} with label {label}.", true);
        try
        {
            var result = client.ListManagementImage.AddImageUrlInput(
                listId.ToString(), "application/json", new BodyModel("URL", imageUrl), null, label);

            ImageIdMap.Add(imageUrl, Int32.Parse(result.ContentId));

            WriteLine("Response:");
            WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
        }
        catch (Exception ex)
        {
            WriteLine($"Unable to add image to list. Caught {ex.GetType().FullName}: {ex.Message}", true);
        }
        finally
        {
            Thread.Sleep(throttleRate);
        }
    }
}
```

## <a name="create-a-method-to-remove-images-from-the-list"></a>목록에서 이미지를 제거하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Removes images from an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToRemove">The images to remove.</param>
/// <remarks>Images are assigned content IDs when they are added to the list.
/// Use the content ID to remove the image.</remarks>
private static void RemoveImages(
    ContentModeratorClient client, int listId,
    IEnumerable<string> imagesToRemove)
{
    foreach (var imageUrl in imagesToRemove)
    {
        if (!ImageIdMap.ContainsKey(imageUrl)) continue;
        int imageId = ImageIdMap[imageUrl];

        WriteLine();
        WriteLine($"Removing entry for {imageUrl} (ID = {imageId}) from list {listId}.", true);

        var result = client.ListManagementImage.DeleteImage(
            listId.ToString(), imageId.ToString());
        Thread.Sleep(throttleRate);

        ImageIdMap.Remove(imageUrl);

        WriteLine("Response:");
        WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
    }
}
```

## <a name="create-a-method-to-get-all-of-the-content-ids-for-images-in-the-list"></a>목록의 이미지에 대한 모든 콘텐츠 ID를 가져오는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Gets all image IDs in an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageIds GetAllImageIds(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Getting all image IDs for list {listId}.", true);

    var result = client.ListManagementImage.GetAllImageIds(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-update-the-details-of-the-list"></a>목록의 세부 정보를 업데이트하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Updates the details of an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList UpdateListDetails(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Updating details for list {listId}.", true);

    listDetails.Name = "Swimsuits and sports";

    var result = client.ListManagementImageLists.Update(
        listId.ToString(), "application/json", listDetails);
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-retrieve-the-details-of-the-list"></a>목록의 세부 정보를 검색하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다.

```csharp
/// <summary>
/// Gets the details for an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static ImageList GetListDetails(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Getting details for list {listId}.", true);

    var result = client.ListManagementImageLists.GetDetails(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-refresh-the-search-index-of-the-list"></a>목록의 검색 인덱스를 새로 고치는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 목록을 업데이트할 때마다 이미지를 차단하는 데 목록을 사용하기 전에 검색 인덱스를 새로 고쳐야 합니다.

```csharp
/// <summary>
/// Refreshes the search index for an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <returns>The response object from the operation.</returns>
private static RefreshIndex RefreshSearchIndex(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Refreshing the search index for list {listId}.", true);

    var result = client.ListManagementImageLists.RefreshIndexMethod(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="create-a-method-to-match-images-against-the-list"></a>목록에 대해 이미지가 일치하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Matches images against an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
/// <param name="imagesToMatch">The images to screen.</param>
private static void MatchImages(
    ContentModeratorClient client, int listId,
    IEnumerable<string> imagesToMatch)
{
    foreach (var imageUrl in imagesToMatch)
    {
        WriteLine();
        WriteLine($"Matching image {imageUrl} against list {listId}.", true);

        var result = client.ImageModeration.MatchUrlInput(
                "application/json", new BodyModel("URL", imageUrl), listId.ToString());
        Thread.Sleep(throttleRate);

        WriteLine("Response:");
        WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
    }
}
```

## <a name="create-a-method-to-delete-all-images-from-the-list"></a>목록에서 모든 이미지를 삭제하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Deletes all images from an image list.
/// </summary>
/// <param name="client">The Content Modertor client.</param>
/// <param name="listId">The list identifier.</param>
private static void DeleteAllImages(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Deleting all images from list {listId}.", true);

    var result = client.ListManagementImage.DeleteAllImages(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
}
```

## <a name="create-a-method-to-delete-the-list"></a>목록을 삭제하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Deletes an image list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="listId">The list identifier.</param>
private static void DeleteCustomList(
    ContentModeratorClient client, int listId)
{
    WriteLine();
    WriteLine($"Deleting list {listId}.", true);

    var result = client.ListManagementImageLists.Delete(listId.ToString());
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));
}
```

## <a name="create-a-method-to-retrieve-ids-for-all-image-lists"></a>모든 이미지 목록에 대한 ID를 검색하는 메서드 만들기

**Program** 클래스에 다음 메서드를 추가합니다. 

```csharp
/// <summary>
/// Gets all list identifiers for the client.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The response object from the operation.</returns>
private static IList<ImageList> GetAllListIds(ContentModeratorClient client)
{
    WriteLine();
    WriteLine($"Getting all image list IDs.", true);

    var result = client.ListManagementImageLists.GetAllImageLists();
    Thread.Sleep(throttleRate);

    WriteLine("Response:");
    WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    return result;
}
```

## <a name="add-code-to-simulate-the-use-of-an-image-list"></a>코드를 추가하여 이미지 목록의 사용 시뮬레이션

**Main** 메서드에 다음 코드를 추가 합니다. 이 코드는 목록을 정의 및 관리하고 이미지를 차단하도록 목록을 사용하는 데 수행하는 많은 작업을 시뮬레이션합니다. 로깅 기능을 통해 Content Moderator 서비스에 대한 SDK 호출에 의해 생성된 응답 개체를 볼 수 있습니다.

```csharp
// Create the text writer to use for logging, and cache a static reference to it.
using (StreamWriter outputWriter = new StreamWriter(OutputFile))
{
    writer = outputWriter;

    // Create a Content Moderator client.
    using (var client = Clients.NewClient())
    {
        // Create a custom image list and record the ID assigned to it.
        var creationResult = CreateCustomList(client);
        if (creationResult.Id.HasValue)
        {
            // Cache the ID of the new image list.
            int listId = creationResult.Id.Value;

            // Perform various operations using the image list.
            AddImages(client, listId, Images.Sports.Urls, Images.Sports.Label);
            AddImages(client, listId, Images.Swimsuit.Urls, Images.Swimsuit.Label);
                    
            GetAllImageIds(client, listId);
            UpdateListDetails(client, listId);
            GetListDetails(client, listId);

            // Be sure to refresh search index
            RefreshSearchIndex(client, listId);

            // WriteLine();
            WriteLine($"Waiting {latencyDelay} minutes to allow the server time to propagate the index changes.", true);
            Thread.Sleep((int)(latencyDelay * 60 * 1000));

            // Match images against the image list.
            MatchImages(client, listId, ImagesToMatch);

            // Remove images
            RemoveImages(client, listId, Images.Corrections);

            // Be sure to refresh search index
            RefreshSearchIndex(client, listId);

            WriteLine();
            WriteLine($"Waiting {latencyDelay} minutes to allow the server time to propagate the index changes.", true);
            Thread.Sleep((int)(latencyDelay * 60 * 1000));

            // Match images again against the image list. The removed image should not get matched.
            MatchImages(client, listId, ImagesToMatch);

            // Delete all images from the list.
            DeleteAllImages(client, listId);

            // Delete the image list.
            DeleteCustomList(client, listId);

            // Verify that the list was deleted.
            GetAllListIds(client);
            }
        }

        writer.Flush();
        writer.Close();
        writer = null;
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

목록 ID와 이미지 콘텐츠 ID는 애플리케이션을 실행할 때마다 다릅니다.
프로그램에 의해 기록된 로그 파일에는 다음과 같은 출력이 있습니다.

```json
Creating list MyList.
Response:
{
    "Id": 169642,
    "Name": "MyList",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169490",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "233"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "2945548"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_b4d3e20a-0751-4760-8829-475e5da33ce8"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169491",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "215"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "2440050"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_cc1eb6af-2463-4e5e-9145-2a11dcecbc30"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample9.png to list 169642 with label Sports.
Response:
{
    "ContentId": "169492",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "98"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1631958"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_01edc1f2-b448-48cf-b7f6-23b64d5040e9"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169493",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "27"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "17280"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_41f7bc6f-8778-4576-ba46-37b43a6c2434"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169494",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "129"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1242855"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_61a48f33-eb55-4fd9-ac97-20eb0f3622a5"
}

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png to list 169642 with label Swimsuit.
Unable to add image to list. Caught Microsoft.CognitiveServices.ContentModerator.Models.APIErrorException: Operation returned an invalid status code 'Conflict'

Adding https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png to list 169642 with label Swimsuit.
Response:
{
    "ContentId": "169495",
    "AdditionalInfo": [
    {
        "Key": "Source",
        "Value": "169642"
    },
    {
        "Key": "ImageDownloadTimeInMs",
        "Value": "65"
    },
    {
        "Key": "ImageSizeInBytes",
        "Value": "1088127"
    }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_1c1f3de4-58b9-4aa8-82fa-1b0f479f6d7c"
}

Getting all image IDs for list 169642.
Response:
{
    "ContentSource": "169642",
    "ContentIds": [
        169490,
        169491,
        169492,
        169493,
        169494,
        169495
],
"Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
    },
"TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_0d017deb-38fa-4701-a7b1-5b6608c79da2"
}

Updating details for list 169642.
Response:
{
    "Id": 169642,
    "Name": "Swimsuits and sports",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Getting details for list 169642.
Response:
{
    "Id": 169642,
    "Name": "Swimsuits and sports",
    "Description": "A sample list",
    "Metadata": {
        "Key One": "Acceptable",
        "Key Two": "Potentially racy"
    }
}

Refreshing the search index for list 169642.
Response:
{
    "ContentSourceId": "169642",
    "IsUpdateSuccess": true,
    "AdvancedInfo": [],
    "Status": {
        "Code": 3000,
        "Description": "RefreshIndex successfully completed.",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c72255cd-55a0-415e-9c18-0b9c08a9f25b"
}
Waiting 0.5 minutes to allow the server time to propagate the index changes.

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_ec384878-dbaa-4999-9042-6ac986355967",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169493,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_e9db4b8f-3067-400f-9552-d3e6af2474c0",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
        }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_25991575-05da-4904-89db-abe88270b403",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c65d1c91-0d8a-4511-8ac6-814e04adc845",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169495,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Removing entry for https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png (ID = 169495) from list 169642.
Response:
""

Refreshing the search index for list 169642.
Response:
{
    "ContentSourceId": "169642",
    "IsUpdateSuccess": true,
    "AdvancedInfo": [],
    "Status": {
        "Code": 3000,
        "Description": "RefreshIndex successfully completed.",
        "Exception": null
        },
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_b55a375e-30a1-4612-aa7b-81edcee5bffb"
}

Waiting 0.5 minutes to allow the server time to propagate the index changes.

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_00544948-2936-489c-98c8-b507b654bff5",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169493,
            "Source": "169642",
            "Tags": [],
            "Label": "Swimsuit"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample4.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_c36ec646-53c2-4705-86b2-d72b5c2273c7",
    "CacheID": null,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
        ],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png against list 169642.
Response:
{
    TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_22edad74-690d-4fbc-b7d0-bf64867c4cb9",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Matching image https://moderatorsampleimages.blob.core.windows.net/samples/sample16.png against list 169642.
Response:
{
    "TrackingId": "WE_f0527c49616243c5ac65e1cc3482d390_ContentModerator.Preview_abd4a178-3238-4601-8e4f-cf9ee66f605a",
    "CacheID": null,
    "IsMatch": false,
    "Matches": [],
    "Status": {
        "Code": 3000,
        "Description": "OK",
        "Exception": null
    }
}

Deleting all images from list 169642.
Response:
"Reset Successful."

Deleting list 169642.
Response:
""

Getting all image list IDs.
Response:
[]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져오고 통합을 시작합니다.
