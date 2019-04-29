---
title: Video Indexer를 사용하여 비디오 업로드 및 인덱싱
titlesuffix: Azure Media Services
description: 이 항목에서는 API를 사용하여 Video Indexer에서 비디오를 업로드하고 인덱싱하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 89903d3f65c74da6903e53284f168d2d6a12168a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559911"
---
# <a name="upload-and-index-your-videos"></a>비디오 업로드 및 인덱싱  

Video Indexer API를 사용하여 비디오를 업로드할 때 다음과 같은 업로드 옵션이 있습니다. 

* URL에서 비디오를 업로드합니다(기본 설정).
* 비디오 파일을 요청 본문의 바이트 배열로 보냅니다.
* 제공 하 여 기존 Azure Media Services 자산을 사용 합니다 [자산 ID](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (유료 계정 에서만 지원).

이 문서에서는 [비디오 업로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API를 사용하여 URL에 따라 비디오를 업로드하고 인덱싱하는 방법을 보여 줍니다. 이 문서의 코드 샘플에는 바이트 배열을 업로드하는 방법을 보여 주는 주석 처리된 코드가 포함되어 있습니다. <br/>또한 이 문서에서는 API의 프로세스 및 출력을 변경하기 위해 API에 설정할 수 있는 몇 가지 매개 변수에 대해서도 설명합니다.

비디오가 업로드되면 Video Indexer가 필요에 따라 비디오를 인코딩합니다(이 문서에서 설명). Video Indexer 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 사용하면 Video Indexer에서 웹 사이트 사용자에게 최대 600분의 체험 인덱싱을 제공하고, API 사용자에게는 최대 2,400분의 체험 인덱싱을 제공합니다. 유료 옵션을 사용하면 [Azure 구독 및 Azure Media Services 계정에 연결되는](connect-to-azure.md) Video Indexer 계정을 만듭니다. 인덱싱 시간(분) 및 미디어 계정과 관련된 요금을 지불합니다. 

## <a name="uploading-considerations"></a>업로드 고려 사항

- URL을 기반으로 하여 비디오를 업로드하는 경우(기본 설정) TLS 1.2 이상을 사용하여 엔드포인트를 보호해야 합니다.
- URL 옵션을 사용 하 여 업로드 크기는 30GB로 제한
- 요청 URL 길이 2048 자로 제한 됩니다.
- 바이트 배열 옵션을 사용하면 업로드 크기가 2GB로 제한됩니다.
- 바이트 배열 옵션은 30분 후 시간 초과됩니다.
- `videoURL` 매개 변수에 제공된 URL은 인코딩해야 합니다.
- URL에서 인덱싱와 동일한 제한 사항이 Media Services 자산 인덱싱
- Video Indexer는 단일 파일에 대해 4 시간의 최대 지속 시간 제한

> [!Tip]
> 이전 .NET Framework는 기본적으로 TLS 1.2로 설정되지 않으므로 .NET Framework 버전 4.6.2 이상을 사용하는 것이 좋습니다.
>
> 이전 .NET Framework를 사용해야 하는 경우 REST API를 호출하기 전에 코드에 다음과 같은 한 줄을 추가합니다.  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>구성 및 매개 변수

이 섹션에서는 선택적 매개 변수 중 일부와 매개 변수를 설정해야 하는 경우에 대해 설명합니다.

### <a name="externalid"></a>externalID 

이 매개 변수를 사용하면 비디오와 연결할 ID를 지정할 수 있습니다. ID는 외부 "VCM(비디오 콘텐츠 관리)" 시스템 통합에 적용할 수 있습니다. Video Indexer 포털에 있는 비디오는 지정된 외부 ID를 사용하여 검색할 수 있습니다.

### <a name="callbackurl"></a>callbackUrl

POST 요청을 사용하여 고객에게 다음 이벤트를 알리는 데 사용되는 URL입니다.

- 인덱싱 상태 변경 
    - 속성    
    
        |이름|설명|
        |---|---|
        |id|비디오 ID|
        |state|비디오 상태|  
    - 예제: https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- 비디오에서 식별된 사용자
  - properties
    
      |이름|설명|
      |---|---|
      |id| 비디오 ID|
      |faceId|비디오 인덱스에 표시되는 얼굴 ID|
      |knownPersonId|얼굴 모델 내에서 고유한 사람 ID|
      |personName|사람의 이름|
        
    - 예제: https://test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>메모

- Video Indexer는 원래 URL에 제공된 기존 매개 변수를 반환합니다.
- 제공된 URL은 인코딩해야 합니다.

### <a name="indexingpreset"></a>indexingPreset

원시 또는 외부 녹음에 백그라운드 노이즈가 있는 경우 이 매개변수를 사용합니다. 이 매개 변수는 인덱싱 프로세스를 구성하는 데 사용됩니다. 지정할 수 있는 값은 다음과 같습니다.

- `Default` – 오디오와 비디오를 모두 사용하여 인사이트를 인덱싱하고 추출합니다.
- `AudioOnly` – 오디오만(비디오 무시) 사용하여 인사이트를 인덱싱하고 추출합니다.
- `DefaultWithNoiseReduction` – 오디오 스트림에 노이즈 감소 알고리즘을 적용하는 한편, 오디오와 비디오 모두에서 인사이트를 인덱싱하고 추출합니다.

가격은 선택한 인덱싱 옵션에 따라 달라집니다.  

### <a name="priority"></a>우선 순위

비디오는 Video Indexer에서 우선 순위에 따라 인덱싱됩니다. **priority** 매개 변수를 사용하여 인덱스 우선 순위를 지정합니다. 유효한 값은 **낮음**, **보통**(기본값), **높음**입니다.

**priority** 매개 변수는 유료 계정에서만 지원됩니다.

### <a name="streamingpreset"></a>streamingPreset

비디오가 업로드되면 Video Indexer에서 필요에 따라 비디오를 인코딩합니다. 그런 다음, 비디오 인덱싱 및 분석을 계속 진행합니다. Video Indexer에서 분석이 완료되면 비디오 ID가 있는 알림을 받게 됩니다.  

[비디오 업로드 ](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) 또는 [비디오 재인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API를 사용하는 경우 선택적 매개 변수 중 하나는 `streamingPreset`입니다. `streamingPreset`을 `Default`, `SingleBitrate` 또는 `AdaptiveBitrate`로 설정하면 인코딩 프로세스가 트리거됩니다. 인덱싱 및 인코딩 작업이 완료되면 비디오를 스트림할 수 있도록 비디오가 게시됩니다. 비디오를 스트림하려는 스트리밍 엔드포인트는 **실행 중** 상태여야 합니다.

인덱싱 및 인코딩 작업을 실행하려면 [Video Indexer 계정에 연결된 Azure Media Services 계정](connect-to-azure.md)에 예약 단위가 필요합니다. 자세한 내용은 [미디어 처리 크기 조정](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)을 참조하세요. 이러한 작업은 컴퓨팅 집약적 작업이므로 S3 단위 유형을 사용하는 것이 좋습니다. RU의 수는 병렬로 실행할 수 있는 작업의 최대 수를 정의합니다. 기본 추천 사항은 10개의 S3 RU입니다. 

비디오 인덱싱만 수행하고 인코딩은 수행하지 않으려면 `streamingPreset`을 `NoStreaming`으로 설정합니다.

### <a name="videourl"></a>videoUrl

인덱싱할 비디오/오디오 파일의 URL입니다. URL은 미디어 파일을 가리켜야 합니다(HTML 페이지는 지원되지 않음). 파일은 URI의 일부로 제공되는 액세스 토큰으로 보호할 수 있으며, 파일을 제공하는 엔드포인트는 TLS 1.2 이상으로 보호해야 합니다. URL은 인코딩해야 합니다. 

`videoUrl`이 지정되지 않으면 Video Indexer에서 파일을 다중 파트/양식 본문 콘텐츠로 전달해야 합니다.

## <a name="code-sample"></a>코드 샘플

다음 C# 코드 조각에서는 모든 Video Indexer API를 사용하는 방법을 보여 줍니다.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```
## <a name="common-errors"></a>일반 오류

다음 표에 나열된 상태 코드는 업로드 작업에서 반환될 수 있습니다.

|상태 코드|ErrorType(응답 본문 내)|설명|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|지정된 계정에서 동일한 비디오가 이미 처리되고 있습니다.|
|400|VIDEO_ALREADY_FAILED|지정된 계정에서 2시간 이내에 동일한 비디오를 처리하지 못했습니다. API 클라이언트에서 2시간 이상 기다린 후에 비디오를 다시 업로드해야 합니다.|

## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 Azure 비디오 인덱서 출력 검토](video-indexer-output-json-v2.md)
