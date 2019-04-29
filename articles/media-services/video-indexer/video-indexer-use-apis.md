---
title: Video Indexer API 사용 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer API를 사용하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: d915058e13f3c5287bdb037a1600e7f0a09da436
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560591"
---
# <a name="tutorial-use-the-video-indexer-api"></a>자습서: Video Indexer API 사용

Video Indexer는 Microsoft에서 제공하는 다양한 오디오 및 비디오 AI(인공 지능) 기술을 하나의 통합 서비스로 통합하여 더 쉽게 개발할 수 있도록 합니다. 이 API는 개발자가 클라우드 플랫폼의 규모, 글로벌 범위, 가용성 및 안정성에 대해 걱정하지 않고 미디어 AI 기술 사용에 집중할 수 있도록 설계되었습니다. API를 사용하여 파일을 업로드하고, 자세한 비디오 인사이트를 얻으며, 인사이트 및 플레이어 위젯의 URL을 와서 애플리케이션과 기타 작업에 포함시킬 수 있습니다.

Video Indexer 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 사용하면 Video Indexer에서 웹 사이트 사용자에게 최대 600분의 체험 인덱싱을 제공하고, API 사용자에게는 최대 2,400분의 체험 인덱싱을 제공합니다. 유료 옵션을 사용하면 [Azure 구독 및 Azure Media Services 계정에 연결되는](connect-to-azure.md) Video Indexer 계정을 만듭니다. 인덱싱 시간(분) 및 Azure Media Services 계정과 관련된 요금을 지불합니다. 

이 문서에서는 개발자가 [Video Indexer API](https://api-portal.videoindexer.ai/)를 활용하는 방법을 보여 줍니다.

## <a name="subscribe-to-the-api"></a>API 구독

1. [Video Indexer 개발자 포털](https://api-portal.videoindexer.ai/)에 로그인합니다.
    
    ![로그인](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Video Indexer에 가입할 때 사용한 것과 동일한 공급자를 사용해야 합니다.
   > * 개인 Google 및 Microsoft(Outlook/Live) 계정은 평가판 계정에만 사용할 수 있습니다. Azure에 연결되는 계정에는 Azure AD가 필요합니다.
   > * 이메일당 하나의 활성 계정만 있을 수 있습니다. 사용자가 LinkedIn에 대해 user@gmail.com을 사용하여 로그인한 후에 Google에 대해 user@gmail.com을 사용하여 로그인하려고 하면 나중에 사용자가 이미 존재한다는 오류 페이지가 표시됩니다.

2. 가입합니다.

    [제품](https://api-portal.videoindexer.ai/products) 탭을 선택합니다. 그런 다음, [권한 부여]를 선택하고 가입합니다. 
    
    ![등록](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 새 사용자는 자동으로 권한 부여에 가입됩니다.
    
    가입하면 해당 구독과 기본 및 보조 키를 볼 수 있습니다. 키는 보호해야 하고, 서버 코드에서만 사용할 수 있습니다. 클라이언트 쪽(.js, .html 등)에서는 사용할 수 없습니다.

    ![등록](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer 사용자는 단일 구독 키를 사용하여 여러 Video Indexer 계정에 연결할 수 있습니다. 그런 다음, 이러한 Video Indexer 계정을 다른 Media Services 계정에 연결할 수 있습니다.

## <a name="obtain-access-token-using-the-authorization-api"></a>권한 부여 API를 사용하여 액세스 토큰 가져오기

권한 부여 API를 구독한 경우 액세스 토큰을 가져올 수 있습니다. 이러한 액세스 토큰은 작업 API와 대조하여 인증하는 데 사용됩니다. 

작업 API에 대한 각 호출은 호출의 권한 부여 범위와 일치하는 액세스 토큰과 연결되어야 합니다.

- 사용자 수준 - 사용자 수준 액세스 토큰을 사용하여 **사용자** 수준에서 작업을 수행할 수 있습니다. 예를 들어 연결된 계정 가져오기가 있습니다.
- 계정 수준 – 계정 수준 액세스 토큰을 사용하여 **계정** 수준 또는 **비디오** 수준에서 작업을 수행할 수 있습니다. 예를 들어 비디오 업로드, 모든 비디오 나열, 비디오 인사이트 가져오기 등이 있습니다.
- 비디오 수준 – 비디오 수준 액세스 토큰을 사용하여 특정 **비디오** 작업을 수행할 수 있습니다. 예를 들어 비디오 인사이트 가져오기, 자막 다운로드, 위젯 가져오기 등이 있습니다. 

**allowEdit=true/false**를 지정하여 이러한 토큰이 읽기 전용인지, 아니면 편집할 수 있는지 여부를 제어할 수 있습니다.

대부분의 서버 간 시나리오에서는 **계정** 작업 및 **비디오** 작업을 모두 처리하므로 동일한 **계정** 토큰을 사용합니다. 그러나 클라이언트 쪽에서(예: javascript에서) Video Indexer를 호출하려는 경우 클라이언트에서 전체 계정에 액세스하지 못하도록 **비디오** 액세스 토큰을 사용하려고 합니다. 또한 VideoIndexer 클라이언트 코드를 클라이언트에 포함할 때(예: **인사이트 위젯 가져오기** 또는 **플레이어 위젯 가져오기** 사용) **비디오** 토큰을 제공해야 하는 이유이기도 합니다.

작업을 더 쉽게 수행하려면 **권한 부여** API > **GetAccounts**를 사용하여 먼저 사용자 토큰을 가져오지 않고 계정을 가져올 수 있습니다. 유효한 토큰이 있는 계정을 가져오도록 요청하여 추가 호출을 건너뛰고 계정 토큰을 가져올 수 있습니다.

액세스 토큰은 1시간 후에 만료됩니다. 작업 API를 사용하기 전에 먼저 액세스 토큰이 유효한지 확인해야 합니다. 만료되면 권한 부여 API를 다시 호출하여 새 액세스 토큰을 받으십시오.
 
이제 API와의 통합을 시작할 준비가 되었습니다. [각 Video Indexer REST API에 대한 자세한 설명](https://api-portal.videoindexer.ai/)을 참조하세요.

## <a name="account-id"></a>계정 ID 

계정 ID 매개 변수는 모든 작업 API 호출에 필요합니다. 계정 ID는 다음 방법 중 하나를 사용하여 얻을 수 있는 GUID입니다.

* **Video Indexer 웹 사이트**를 사용하여 계정 ID를 가져옵니다.

    1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
    2. **설정** 페이지로 이동합니다.
    3. 계정 ID를 복사합니다.

        ![계정 ID](./media/video-indexer-use-apis/account-id.png)

* **Video Indexer 개발자 포털**을 사용하여 계정 ID를 프로그래밍 방식으로 가져옵니다.

    [계정 가져오기](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API를 사용합니다.
    
    > [!TIP]
    > `generateAccessTokens=true`를 정의하여 계정에 대한 액세스 토큰을 생성할 수 있습니다.
    
* 계정에 있는 플레이어 페이지의 URL에서 계정 ID를 가져옵니다.

    비디오를 시청할 때 ID는 `accounts` 섹션 뒤 및 `videos` 섹션 앞에 표시됩니다.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>권장 사항

이 섹션에는 Video Indexer API를 사용할 때의 몇 가지 추천 사항이 나와 있습니다.

- 비디오를 업로드하려는 경우 일부 공용 네트워크 위치(예: OneDrive)에 파일을 배치하는 것이 좋습니다. 비디오에 대한 링크를 가져오고, URL을 파일 업로드 매개 변수로 제공합니다. 

    Video Indexer에 제공된 URL은 미디어(오디오 또는 비디오) 파일을 가리켜야 합니다. OneDrive에서 생성된 링크 중 일부는 파일이 포함된 HTML 페이지를 위한 것입니다. URL은 브라우저에 붙여넣어 쉽게 확인할 수 있습니다. 파일 다운로드가 시작되면 좋은 URL일 가능성이 높습니다. 브라우저에서 일부 시각화를 렌더링하는 경우 파일에 대한 링크가 아니라 HTML 페이지일 수 있습니다.
    
- 지정된 비디오에 대한 비디오 인사이트를 가져오는 API를 호출하면 자세한 JSON 출력을 응답 콘텐츠로 가져옵니다. [이 항목에서 반환된 JSON에 대한 자세한 내용을 참조하세요](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>코드 샘플

다음 C# 코드 조각에서는 모든 Video Indexer API를 사용하는 방법을 보여 줍니다.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>참고 항목

- [Video Indexer 개요](video-indexer-overview.md)
- [지역](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>다음 단계

[출력 JSON에 대한 세부 정보 검사](video-indexer-output-json-v2.md)

