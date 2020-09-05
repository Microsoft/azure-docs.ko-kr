---
title: 앱에 Video Indexer 위젯을 포함 합니다.
titleSuffix: Azure Media Services
description: 앱에 Video Indexer 위젯을 포함 하는 방법에 대해 알아봅니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: juliako
ms.custom: devx-track-javascript
ms.openlocfilehash: 5f418384d4ec1272600df2488dc71d2102d6a24d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011806"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>앱에 Video Indexer 위젯을 포함 합니다.

이 문서에서는 앱에 Video Indexer 위젯을 포함 하는 방법을 보여 줍니다. Video Indexer는 다음 세 가지 유형의 위젯을 앱에 포함하는 것을 지원합니다. *인지적 인사이트*, *플레이어* 및 *편집기*.

버전 2부터 위젯 기준 URL에는 지정 된 계정의 지역이 포함 됩니다. 예를 들어 미국 서부 지역의 계정은 `https://www.videoindexer.ai/embed/insights/.../?location=westus2`를 생성합니다.

## <a name="widget-types"></a>위젯 유형

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

인지 인사이트 위젯에는 비디오 인덱싱 프로세스에서 추출한 모든 시각적 인사이트가 포함되어 있습니다. 인식 통찰력 위젯은 다음과 같은 선택적 URL 매개 변수를 지원 합니다.

|Name|정의|설명|
|---|---|---|
|`widgets` | 쉼표로 구분된 문자열 | 렌더링 하려는 정보를 제어할 수 있습니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` 사용자 및 키워드 UI 정보를 렌더링 합니다.<br/>사용 가능한 옵션: 사람, animatedCharacters, 키워드, 레이블, 정서, 감정을, 토픽, 키 프레임, 성적 증명서, ocr, 스피커, 장면 및 namedEntities.|
|`controls`|쉼표로 구분된 문자열|렌더링 하려는 컨트롤을 제어할 수 있습니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` 검색 옵션 및 다운로드 단추만 렌더링 합니다.<br/>사용 가능한 옵션: 검색, 다운로드, 사전 설정, 언어|
|`language`|짧은 언어 코드 (언어 이름)|Insights 언어를 제어 합니다.<br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>또는 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 짧은 언어 코드 | UI의 언어를 제어 합니다. 기본값은 `en`입니다. <br/>예: `locale=de`.|
|`tab` | 기본 선택 된 탭 | 기본적으로 렌더링 되는 **Insights** 탭을 제어 합니다. <br/>예: `tab=timeline` **타임 라인** 탭이 선택 된 상태에서 정보를 렌더링 합니다.|
|`location` ||`location`매개 변수는 포함 된 링크에 포함 되어야 합니다. 해당 [지역의 이름을 가져오는 방법](regions.md)을 참조 하세요. 계정이 미리 보기 상태인 경우 `trial` 위치 값에 대해를 사용 해야 합니다. `trial``location`매개 변수의 기본값입니다.| 

### <a name="player-widget"></a>플레이어 위젯

플레이어 위젯을 사용 하 여 적응 비트 전송률을 사용 하 여 비디오를 스트리밍할 수 있습니다. 플레이어 위젯은 다음과 같은 선택적 URL 매개 변수를 지원 합니다.

|Name|정의|설명|
|---|---|---|
|`t` | 시작부터 초 | 지정 된 시간 지점에서 플레이어의 재생을 시작 합니다.<br/> 예: `t=60`. |
|`captions` | 언어 코드 | **캡션** 메뉴에서 위젯 로드를 사용할 수 있도록 지정 된 언어의 캡션을 페치합니다.<br/> 예: `captions=en-US`. |
|`showCaptions` | 부울 값 | 플레이어가 자막을 사용하도록 설정된 상태로 로드되도록 합니다.<br/> 예: `showCaptions=true`. |
|`type`| | 오디오 플레이어 스킨을 활성화 합니다 (비디오 부분이 제거 됨).<br/> 예: `type=audio`. |
|`autoplay` | 부울 값 | 플레이어가 로드 될 때 비디오 재생을 시작 해야 하는지 여부를 나타냅니다. 기본값은 `true`입니다.<br/> 예: `autoplay=false`. |
|`language`/`locale` | 언어 코드 | 플레이어 언어를 제어 합니다. 기본값은 `en-US`입니다.<br/>예: `language=de-DE`.|
|`location` ||`location`매개 변수는 포함 된 링크에 포함 되어야 합니다. 해당 [지역의 이름을 가져오는 방법](regions.md)을 참조 하세요. 계정이 미리 보기 상태인 경우 `trial` 위치 값에 대해를 사용 해야 합니다. `trial``location`매개 변수의 기본값입니다.| 

### <a name="editor-widget"></a>편집기 위젯

편집기 위젯을 사용 하 여 새 프로젝트를 만들고 비디오의 정보를 관리할 수 있습니다. 편집기 위젯은 다음과 같은 선택적 URL 매개 변수를 지원 합니다.

|Name|정의|Description|
|---|---|---|
|`accessToken`<sup>*</sup> | String | 위젯을 포함 하는 데 사용 되는 계정에만 있는 비디오에 대 한 액세스를 제공 합니다.<br> 편집기 위젯에는 `accessToken` 매개 변수가 필요 합니다. |
|`language` | 언어 코드 | 플레이어 언어를 제어 합니다. 기본값은 `en-US`입니다.<br/>예: `language=de-DE`. |
|`locale` | 짧은 언어 코드 | Insights 언어를 제어 합니다. 기본값은 `en`입니다.<br/>예: `language=de`. |
|`location` ||`location`매개 변수는 포함 된 링크에 포함 되어야 합니다. 해당 [지역의 이름을 가져오는 방법](regions.md)을 참조 하세요. 계정이 미리 보기 상태인 경우 `trial` 위치 값에 대해를 사용 해야 합니다. `trial``location`매개 변수의 기본값입니다.| 

<sup>*</sup>소유자는 신중 하 게 제공 해야 합니다 `accessToken` .

## <a name="embedding-videos"></a>비디오 포함

이 섹션에서는 공용 및 개인 콘텐츠를 앱에 포함 하는 방법을 설명 합니다.

`location`매개 변수는 포함 된 링크에 포함 되어야 합니다. 해당 [지역의 이름을 가져오는 방법](regions.md)을 참조 하세요. 계정이 미리 보기 상태인 경우 `trial` 위치 값에 대해를 사용 해야 합니다. `trial``location`매개 변수의 기본값입니다. 예: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`

> [!IMPORTANT]
> **플레이어** 또는 **Insights** 위젯에 대 한 링크를 공유 하면 액세스 토큰이 포함 되며 계정에 읽기 전용 권한을 부여 합니다.

### <a name="public-content"></a>공용 콘텐츠

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트에 로그인 합니다.
1. 작업 하려는 비디오를 선택 하 고 **재생**을 누릅니다.
1. 원하는 위젯 유형 (**인지 정보**, **플레이어**또는 **편집기**)을 선택 합니다.
1. ** &lt; / &gt; Embed**를 클릭 합니다.
5. Embed 태그를 복사 합니다 ( **공유 & embed** 대화 상자에 **포함 된 코드 복사** 에 표시).
6. 앱에 코드를 추가 합니다.

### <a name="private-content"></a>개인 콘텐츠

개인 비디오를 포함 하려면 iframe의 특성에 액세스 토큰을 전달 해야 합니다 `src` .

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
인지 Insights 위젯 콘텐츠를 가져오려면 다음 방법 중 하나를 사용 합니다.

- [Insights 위젯 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API입니다.<br/>
- [비디오 액세스 토큰을 가져옵니다](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). URL에 쿼리 매개 변수로 추가 합니다. `src`앞에서 설명한 것 처럼 iframe의 값으로이 URL을 지정 합니다.

포함 된 위젯에 편집 정보 기능을 제공 하려면 편집 권한을 포함 하는 액세스 토큰을 전달 해야 합니다. 에서 [Insights 위젯 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) 또는 [비디오 액세스 토큰 가져오기](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) 를 사용 `&allowEdit=true` 합니다.

## <a name="widgets-interaction"></a>위젯 상호 작용

인식 통찰력 위젯은 앱의 비디오와 상호 작용할 수 있습니다. 이 섹션에서는 이러한 상호 작용을 수행하는 방법을 보여 줍니다.

![인식 Insights 위젯 Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>흐름 개요

기록을 편집할 때 다음 흐름이 발생 합니다.

1. 타임 라인에서 성적 증명서를 편집 합니다.
1. 이러한 업데이트를 Video Indexer 하 여 언어 모델의 내용에 대 한 기록의 [편집 내용](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) 에 저장 합니다.
1. 캡션이 업데이트 됩니다.

    * Video Indexer 플레이어 위젯을 사용 하는 경우 자동으로 업데이트 됩니다.
    * 외부 플레이어를 사용 하는 경우- **비디오 캡션 가져오기** 호출을 통해 새 캡션 파일을 가져옵니다.

### <a name="cross-origin-communications"></a>원본 간 통신

다른 구성 요소와 통신 하기 위해 Video Indexer 위젯을 얻으려면 Video Indexer 서비스:

- 원본 간 통신 HTML5 메서드를 사용 `postMessage` 합니다.
- VideoIndexer.ai 원본을 통해 메시지의 유효성을 검사합니다.

사용자 고유의 플레이어 코드를 구현 하 고 인식 Insights 위젯에 통합 하는 경우 VideoIndexer.ai에서 제공 하는 메시지 원본의 유효성을 검사 해야 합니다.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>앱 또는 블로그의 위젯 포함 (권장)

이 섹션에서는 사용자가 앱에서 통찰력 컨트롤을 선택할 때 플레이어가 관련 순간으로 이동 하도록 두 Video Indexer widget 간의 상호 작용을 구현 하는 방법을 보여 줍니다.

1. 플레이어 위젯 포함 코드를 복사합니다.
2. 인지 인사이트 포함 코드를 복사합니다.
3. 두 위젯 간의 통신을 처리하는 [중재자(mediator) 파일](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)을 추가합니다.<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

이제 사용자가 앱에서 정보 컨트롤을 선택 하면 플레이어는 해당 순간으로 이동 합니다.

자세한 내용은 [Video Indexer 두 위젯 데모 모두 포함](https://codepen.io/videoindexer/pen/NzJeOb)을 참조 하세요.

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>인지 인사이트 위젯 포함 및 Azure Media Player를 사용하여 콘텐츠 재생

이 섹션에서는 [AMP 플러그](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)인을 사용 하 여 인지 Insights 위젯 및 Azure Media Player 인스턴스 간의 상호 작용을 구현 하는 방법을 보여 줍니다.

1. AMP player에 대 한 Video Indexer 플러그 인을 추가 합니다.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Video Indexer 플러그 인을 사용 하 여 Azure Media Player를 인스턴스화합니다.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. 인지 인사이트 포함 코드를 복사합니다.

이제 Azure Media Player와 통신할 수 있습니다.

자세한 내용은 [Azure Media Player + VI Insights 데모](https://codepen.io/videoindexer/pen/rYONrO)를 참조 하세요.

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Video Indexer 인식 Insights 위젯을 포함 하 고 다른 비디오 플레이어를 사용 합니다.

Azure Media Player 이외의 비디오 플레이어를 사용 하는 경우 비디오 플레이어를 수동으로 조작 하 여 통신을 수행 해야 합니다.

1. 비디오 플레이어를 삽입합니다.

    예를 들어 표준 HTML5 플레이어는 다음과 같습니다.

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. 인지 인사이트 위젯을 포함합니다.
3. "메시지" 이벤트를 수신 대기하여 플레이어에 대한 통신을 구현합니다. 예를 들어:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

자세한 내용은 [Azure Media Player + VI Insights 데모](https://codepen.io/videoindexer/pen/YEyPLd)를 참조 하세요.

## <a name="adding-subtitles"></a>자막 추가

사용자 고유의 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용 하 여 Video Indexer 정보를 포함 하는 경우 메서드를 사용 하 여 `GetVttUrl` 자막 (자막)을 가져올 수 있습니다. 이전 처럼 Video Indexer AMP 플러그 인에서 JavaScript 메서드를 호출할 수도 있습니다 `getSubtitlesUrl` .

## <a name="customizing-embeddable-widgets"></a>포함 가능한 위젯 사용자 지정

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

원하는 유형의 정보를 선택할 수 있습니다. 이렇게 하려면 API 또는 웹 앱에서 가져온 embed 코드에 추가 되는 다음 URL 매개 변수에 값으로 지정 `&widgets=<list of wanted widgets>` 합니다.

가능한 값은 `people` ,,,,,,,,,,, `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` 및 `namedEntities` 입니다.

예를 들어 사람 및 키워드 insights만 포함 된 위젯을 포함 하려는 경우 iframe embed URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

iframe 창의 제목도 iframe URL에 `&title=<YourTitle>`을 제공하여 사용자 지정할 수 있습니다. (HTML 값을 사용자 지정 `<title>` 합니다).
   
예를 들어 iframe 창에 "MyInsights"라는 제목을 지정하려는 경우 URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

이 옵션은 새 창에서 인사이트를 열어야 하는 경우에만 관련이 있습니다.

### <a name="player-widget"></a>플레이어 위젯

Video Indexer 플레이어를 포함하는 경우 iframe의 크기를 지정하여 플레이어 크기를 선택할 수 있습니다.

예를 들어:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

기본적으로 Video Indexer 플레이어는 비디오의 내용에 따라 자동으로 닫힌 캡션을 생성 합니다. 비디오에서 비디오를 업로드할 때 선택한 원본 언어로 인 증명서가 추출 됩니다.

다른 언어로 포함 하려는 경우 `&captions=<Language Code>` 포함 플레이어 URL에를 추가할 수 있습니다. 기본적으로 캡션을 표시 하려면 &showCaptions = true를 전달 하면 됩니다.

그러면 포함 URL이 다음과 같습니다.

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>자동 실행

기본적으로 플레이어는 비디오 재생을 시작 합니다. 이전 embed URL에 전달 하 여 하지 않도록 선택할 수 있습니다 `&autoplay=false` .

## <a name="code-samples"></a>코드 샘플

Video Indexer API 및 위젯의 샘플이 포함 된 [코드 샘플](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) 리포지토리를 참조 하세요.

| 파일/폴더                       | 설명                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 사용자 지정 Azure Media Player 비디오 인덱서 비디오를 로드 합니다.                        |
| `azure-media-player-vi-insights`  | 사용자 지정 Azure Media Player를 사용 하 여 VI 정보를 포함 합니다.                             |
| `control-vi-embedded-player`      | VI 플레이어를 포함 하 고 외부에서 제어 합니다.                                    |
| `custom-index-location`           | 사용자 지정 외부 위치에서 VI 정보를 포함 합니다 (고객이 blob 일 수 있음).     |
| `embed-both-insights`             | 플레이어와 통찰력을 모두 사용 하 여 VI 정보를 기본으로 사용 합니다.                            |
| `embed-insights-with-AMP`         | 사용자 지정 Azure Media Player를 사용 하 여 VI Insights 위젯을 포함 합니다.                      |
| `customize-the-widgets`           | 사용자 지정 된 옵션을 사용 하 여 VI 위젯을 포함 합니다.                                     |
| `embed-both-widgets`              | VI 플레이어와 정보를 포함 하 고 서로 통신 합니다.                      |
| `url-generator`                   | 사용자가 지정한 옵션에 따라 위젯 사용자 지정 embed URL을 생성 합니다.             |
| `html5-player`                    | 기본 HTML5 비디오 플레이어를 사용 하 여 VI 정보를 포함 합니다.                           |

## <a name="next-steps"></a>다음 단계

Video Indexer insights를 보고 편집 하는 방법에 대 한 자세한 내용은 [Video Indexer Insights 보기 및 편집](video-indexer-view-edit.md)을 참조 하세요.

또한 [Video 인덱서 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)를 확인 하세요.
