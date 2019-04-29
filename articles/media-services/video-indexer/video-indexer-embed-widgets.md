---
title: 애플리케이션에 Video Indexer 위젯 포함
titlesuffix: Azure Media Services
description: 애플리케이션에 Video Indexer 위젯을 포함하는 방법을 알아봅니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: fe3466dcccf6381f26c823ce3deb2126c9534548
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560422"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>애플리케이션에 Video Indexer 위젯 포함

이 문서에서는 애플리케이션에 Video Indexer 위젯을 포함하는 방법에 대해 설명합니다. Video Indexer는 **인지 인사이트** 및 **플레이어**라는 두 가지 유형의 위젯을 애플리케이션에 포함할 수 있도록 지원합니다. 

버전 2부터 위젯 기본 URL에 계정의 지역이 포함됩니다. 예를 들어 미국 서부 지역의 계정은 `https://wus2.videoindexer.ai/embed/insights/...`를 생성합니다.

## <a name="widget-types"></a>위젯 유형

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

**인지 인사이트** 위젯에는 비디오 인덱싱 프로세스에서 추출한 모든 시각적 인사이트가 포함되어 있습니다. 인사이트 위젯에서 지원하는 선택적 URL 매개 변수는 다음과 같습니다.

|이름|정의|설명|
|---|---|---|
|widgets|쉼표로 구분된 문자열|렌더링하려는 인사이트를 제어할 수 있습니다. <br/>예: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`는 사용자 및 브랜드 UI 인사이트만 제공합니다.<br/>사용 가능한 옵션: people, keywords, annotations, brands, sentiments, transcript, search<br/>version=2에서 URL을 통해 지원되지 않습니다.<br/><br/>**참고:** 위젯 URL 매개 변수는 버전 2에서에서 지원 되지 않습니다. |

### <a name="player-widget"></a>플레이어 위젯

**플레이어** 위젯을 사용하면 적응 비트 전송률을 사용하여 비디오를 스트림할 수 있습니다. 플레이어 위젯에서 지원하는 선택적 URL 매개 변수는 다음과 같습니다.

|이름|정의|설명|
|---|---|---|
|t|시작 시간(초)|플레이어가 지정된 시점에서 재생을 시작하도록 합니다.<br/>예: t=60|
|captions|언어 코드|자막 메뉴에서 사용할 수 있도록 위젯을 로드하는 동안 지정된 언어의 자막을 가져옵니다.<br/>예: captions=en-US|
|showCaptions|부울 값|플레이어가 자막을 사용하도록 설정된 상태로 로드되도록 합니다.<br/>예: showCaptions=true|
|형식||오디오 플레이어 스킨을 활성화합니다(비디오 부분은 제거됨).<br/>예: type=audio|
|autoplay|부울 값|플레이어가 로드되면 비디오 재생을 시작해야 하는지 여부를 나타냅니다(기본값: true).<br/>예: autoplay=false|
|언어|언어 코드|플레이어 언어를 제어합니다(기본값: en-US).<br/>예: language=de-DE|

## <a name="embedding-public-content"></a>공용 콘텐츠 포함

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 사용하려는 비디오를 클릭합니다.
3. 비디오 아래에 표시되는 "포함" 단추를 클릭합니다.

    ![위젯](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    단추를 클릭하면 프로그램에 포함하려는 위젯을 선택할 수 있는 포함 모달이 화면에 나타납니다.
    위젯(**플레이어** 또는 **인지 인사이트**)을 선택하면 애플리케이션에 붙여넣을 수 있는 포함된 코드가 생성됩니다.
 
4. 원하는 위젯 유형(**인지 인사이트** 또는 **플레이어**)을 선택합니다.
5. 포함 코드를 복사하고 애플리케이션에 추가합니다. 

    ![위젯](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>개인 콘텐츠 포함

**공용** 비디오에 대해서만 포함 팝업에서 포함 코드(이전 섹션 참조)를 가져올 수 있습니다. 

**개인** 비디오를 포함하려면 **iframe**의 **src** 특성에 액세스 토큰을 전달해야 합니다.

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
[**인사이트 가져오기 위젯**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API를 사용하여 인지 인사이트 위젯 콘텐츠를 가져오거나, 위와 같이 [**비디오 액세스 토큰 가져오기**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)를 사용하고 URL에 쿼리 매개 변수로 추가합니다. 이 URL을 **iframe**의 **src** 값으로 지정합니다.

웹 애플리케이션에 있는 것처럼 포함된 위젯에 인사이트 편집 기능을 제공하려면 편집 권한이 있는 액세스 토큰을 전달해야 합니다. **&allowEdit=true**인 [**인사이트 가져오기 위젯**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) 또는 [**비디오 액세스 토큰 가져오기**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)를 사용합니다. 

## <a name="widgets-interaction"></a>위젯 상호 작용

**인지 인사이트** 위젯은 애플리케이션의 비디오와 상호 작용할 수 있습니다. 이 섹션에서는 이러한 상호 작용을 수행하는 방법을 보여 줍니다.

![위젯](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>원본 간 통신

Video Indexer 위젯에서 다른 구성 요소와 통신하도록 하려면 Video Indexer 서비스에서 다음을 수행합니다.

- **postMessage** 원본 간 통신 HTML5 메서드를 사용합니다. 그리고 
- VideoIndexer.ai 원본을 통해 메시지의 유효성을 검사합니다. 

사용자 고유의 플레이어 코드를 구현하고 **인지 인사이트** 위젯과 통합하도록 선택하는 경우 사용자가 VideoIndexer.ai.에서 제공하는 메시지의 원본에 대한 유효성을 검사해야 합니다.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>애플리케이션/블로그에 두 가지 유형의 위젯 포함(추천) 

이 섹션에서는 두 개의 Video Indexer 위젯 간의 상호 작용을 수행하는 방법을 보여 주며, 사용자가 애플리케이션에서 인사이트 컨트롤을 클릭하면 플레이어에서 관련 시점으로 이동합니다.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. **플레이어** 위젯 포함 코드를 복사합니다.
2. **인지 인사이트** 포함 코드를 복사합니다.
3. 두 위젯 간의 통신을 처리하는 [**중재자(mediator) 파일**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)을 추가합니다.

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

이제 사용자가 애플리케이션에서 인사이트 컨트롤을 클릭하면 관련 시점으로 이동합니다.

자세한 내용은 [이 데모](https://codepen.io/videoindexer/pen/NzJeOb)를 참조하세요.

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>인지 인사이트 위젯 포함 및 Azure Media Player를 사용하여 콘텐츠 재생

이 섹션에서는 [AMP 플러그 인](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)을 사용하여 **인지 인사이트** 위젯과 Azure Media Player 인스턴스 간의 상호 작용을 수행하는 방법을 보여 줍니다.
 
1. AMP 플레이어용 Video Indexer 플러그 인을 추가합니다.<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Video Indexer 플러그 인을 사용하여 Azure Media Player를 인스턴스화합니다.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
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

        // Init your AMP instance
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
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. **인지 인사이트** 포함 코드를 복사합니다.

이제 Azure Media Player와 통신할 수 있습니다.

자세한 내용은 [이 데모](https://codepen.io/videoindexer/pen/rYONrO)를 참조하세요.

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Video Indexer 인지 인사이트 위젯 포함 및 사용자 고유의 플레이어 사용(모든 플레이어일 수 있음)

고유한 플레이어를 사용하는 경우 자신의 플레이어를 직접 조작하여 통신할 수 있도록 해야 합니다. 

1. 비디오 플레이어를 삽입합니다.

    예를 들어 표준 HTML5 플레이어가 있습니다.

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 인지 인사이트 위젯을 포함합니다.
3. "메시지" 이벤트를 수신 대기하여 플레이어에 대한 통신을 구현합니다. 예를 들면 다음과 같습니다.

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

자세한 내용은 [이 데모](https://codepen.io/videoindexer/pen/YEyPLd)를 참조하세요.

## <a name="adding-subtitles"></a>자막 추가

사용자 고유의 AMP 플레이어에 Video Indexer 인사이트를 포함하는 경우 **GetVttUrl** 메서드를 사용하여 선택 자막(자막)을 가져올 수 있습니다. 또한 Video Indexer AMP 플러그 인인 **getSubtitlesUrl**에서 javascript 메서드를 호출할 수도 있습니다(이전과 같이). 

## <a name="customizing-embeddable-widgets"></a>포함 가능한 위젯 사용자 지정

### <a name="cognitive-insights-widget"></a>인지 인사이트 위젯

API 또는 웹 애플리케이션에서 가져온 포함 코드에 추가된 다음 URL 매개 변수의 값으로 지정하여 원하는 인사이트 유형을 선택할 수 있습니다(`&widgets=<list of wanted widgets>`).

가능한 값은 people, keywords, sentiments, transcript, search입니다.

예를 들어 사람과 검색 인사이트만 있는 위젯을 포함하려는 경우 iframe 포함 URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 창의 제목도 iframe URL에 `&title=<YourTitle>`을 제공하여 사용자 지정할 수 있습니다. (html \<title> 값을 사용자 지정합니다.)
    
예를 들어 iframe 창에 "MyInsights"라는 제목을 지정하려는 경우 URL은 다음과 같습니다.

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

이 옵션은 새 창에서 인사이트를 열어야 하는 경우에만 관련이 있습니다.

### <a name="player-widget"></a>플레이어 위젯

Video Indexer 플레이어를 포함하는 경우 iframe의 크기를 지정하여 플레이어 크기를 선택할 수 있습니다.

예를 들면 다음과 같습니다.

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

비디오가 업로드되면 Video Indexer 플레이어는 기본적으로 비디오에서 추출된 비디오의 전사에 따라 선택 자막을 선택한 소스 언어로 자동으로 생성합니다.

다른 언어로 포함하려는 경우 `&captions=< Language | ”all” | “false” >`를 포함 플레이어 URL에 추가하거나, 사용 가능한 모든 언어 자막을 사용하려면 값으로 "all"을 지정합니다.
자막을 기본적으로 표시하려면 `&showCaptions=true`를 전달하면 됩니다.

그러면 포함 URL이 다음과 같습니다. 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

자막을 사용하지 않도록 설정하려면 captions 매개 변수의 값으로 "false"를 전달할 수 있습니다.

자동 재생 – 기본적으로 플레이어에서 비디오 재생을 시작합니다. &autoplay=false를 위의 포함 URL에 전달하여 자동 재생을 시작하지 않도록 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Video Indexer 인사이트를 보고 편집하는 방법에 대한 자세한 내용은 [이 문서](video-indexer-view-edit.md)를 참조하세요.

또한 체크 아웃 [Video indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ)합니다.
