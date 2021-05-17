---
title: 스트리밍 로케이터 생성 및 URL 빌드
description: 이 문서에서는 스트리밍 로케이터를 만들고 URL을 빌드하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 71713fc061165673f132f5ab778391bba3e0709a
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685355"
---
# <a name="create-a-streaming-locator-and-build-urls"></a>스트리밍 로케이터 생성 및 URL 빌드

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 스트리밍 URL을 빌드하려면 먼저 [스트리밍 로케이터](stream-streaming-locators-concept.md)를 만들어야 합니다. 그런 다음, [스트리밍 엔드포인트](/rest/api/media/streamingendpoints) 호스트 이름과 **스트리밍 로케이터** 경로를 연결합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트** 가 사용됩니다. Media Service 계정을 처음으로 만들 때 이 *기본* **스트리밍 엔드포인트** 가 중지된 상태이므로 스트리밍을 시작하려면 **Start** 를 호출해야 합니다.

이 문서에서는 Java 및 .NET SDK를 사용하여 스트리밍 로케이터를 만들고 스트리밍 URL을 빌드하는 방법을 보여줍니다.

## <a name="prerequisite"></a>필수 조건

[동적 패키징](encode-dynamic-packaging-concept.md) 미리 보기

## <a name="java"></a>Java

```java
/**
* Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
* Once the StreamingLocator is created the output asset is available to clients for playback.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param assetName     The name of the output asset
* @param locatorName   The StreamingLocator name (unique in this case)
* @return              The locator created
*/
private static StreamingLocator getStreamingLocator(MediaManager manager, String resourceGroup, String accountName,
    String assetName, String locatorName) {
    // Note that we are using one of the PredefinedStreamingPolicies which tell the Origin component
    // of Azure Media Services how to publish the content for streaming.
    System.out.println("Creating a streaming locator...");
    StreamingLocator locator = manager
        .streamingLocators().define(locatorName)
        .withExistingMediaservice(resourceGroup, accountName)
        .withAssetName(assetName)
        .withStreamingPolicyName("Predefined_ClearStreamingOnly")
        .create();

    return locator;
}

/**
* Checks if the streaming endpoint is in the running state, if not, starts it.
* @param manager       The entry point of Azure Media resource management
* @param resourceGroup The name of the resource group within the Azure subscription
* @param accountName   The Media Services account name
* @param locatorName   The name of the StreamingLocator that was created
* @param streamingEndpoint     The streaming endpoint.
* @return              List of streaming urls
*/
private static List<String> getStreamingUrls(MediaManager manager, String resourceGroup, String accountName,
    String locatorName, StreamingEndpoint streamingEndpoint) {
    List<String> streamingUrls = new ArrayList<>();

    ListPathsResponse paths = manager.streamingLocators().listPathsAsync(resourceGroup, accountName, locatorName)
        .toBlocking().first();
    
    for (StreamingPath path: paths.streamingPaths()) {
        StringBuilder uriBuilder = new StringBuilder();
        uriBuilder.append("https://")
            .append(streamingEndpoint.hostName())
            .append("/")
            .append(path.paths().get(0));

        streamingUrls.add(uriBuilder.toString());
    }
    return streamingUrls;
}
```

전체 코드 샘플([EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java))을 참조하세요.

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Creates a StreamingLocator for the specified asset and with the specified streaming policy name.
/// Once the StreamingLocator is created the output asset is available to clients for playback.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="assetName">The name of the output asset.</param>
/// <param name="locatorName">The StreamingLocator name (unique in this case).</param>
/// <returns>A task.</returns>
private static async Task<StreamingLocator> CreateStreamingLocatorAsync(
    IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string locatorName)
{
    Console.WriteLine("Creating a streaming locator...");
    StreamingLocator locator = await client.StreamingLocators.CreateAsync(
        resourceGroup,
        accountName,
        locatorName,
        new StreamingLocator
        {
            AssetName = assetName,
            StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
        });

    return locator;
}

/// <summary>
/// Checks if the streaming endpoint is in the running state,
/// if not, starts it. Then, builds the streaming URLs.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="locatorName">The name of the StreamingLocator that was created.</param>
/// <param name="streamingEndpoint">The streaming endpoint.</param>
/// <returns>A task.</returns>
private static async Task<IList<string>> GetStreamingUrlsAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    String locatorName,
    StreamingEndpoint streamingEndpoint)
{
    IList<string> streamingUrls = new List<string>();

    ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

    foreach (StreamingPath path in paths.StreamingPaths)
    {
        UriBuilder uriBuilder = new UriBuilder
        {
            Scheme = "https",
            Host = streamingEndpoint.HostName,

            Path = path.Paths[0]
        };
        streamingUrls.Add(uriBuilder.ToString());
    }

    return streamingUrls;
}
```

전체 코드 샘플([EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/Encoding_PredefinedPreset/Program.cs))을 참조하세요.

## <a name="see-also"></a>추가 정보

* [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-how-to.md)
* [REST를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
* [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-how-to.md)

## <a name="next-steps"></a>다음 단계

[DRM을 사용하여 콘텐츠를 보호합니다](drm-protect-with-drm-tutorial.md).
