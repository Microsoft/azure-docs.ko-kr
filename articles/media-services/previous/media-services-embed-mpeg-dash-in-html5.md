---
title: DASH.js를 사용하여 HTML5 애플리케이션에 MPEG-DASH 적응 스트리밍 비디오 포함 | Microsoft Docs
description: 이 토픽에서는 DASH.js를 사용하여 HTML5 애플리케이션에 MPEG-DASH 적응 스트리밍 비디오를 포함시키는 방법을 보여 줍니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f521fd11a2053cf8cf1ea0f9f91667fe475f0eee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464201"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>DASH.js를 사용하여 HTML5 애플리케이션에 MPEG-DASH 적응 스트리밍 비디오 포함  

## <a name="overview"></a>개요
MPEG-DASH는 고품질 적응 비디오 스트리밍 출력을 전달하려는 개발자에게 많은 혜택을 제공하는 비디오 콘텐츠의 적응 스트리밍을 위한 ISO 표준입니다. MPEG-DASH를 사용하면 네트워크 정체 상태일 때 비디오 스트림이 자동으로 낮은 화질로 조정됩니다. 따라서 플레이어가 재생할 다음 몇 초를 다운로드(버퍼링)하는 동안 뷰어에 "일시 중지된" 비디오가 표시될 가능성을 줄여줍니다. 네트워크 정체가 줄어들면 비디오 플레이어가 높은 품질의 스트림에 다시 돌아갑니다. 또한 이렇게 필요한 대역폭으로 조정하는 기능 덕분에 비디오의 시작 시간이 더욱 빨라집니다. 즉, 처음 몇 초는 낮은 품질로 세그먼트를 빠르게 다운로드하여 재생한 다음 충분한 콘텐츠가 버퍼링되고 나면 높은 품질로 설정할 수 있습니다.

Dash.js는 JavaScript로 작성된 오픈 소스 MPEG-DASH 비디오 플레이어입니다. 목표는 비디오 재생이 필요한 애플리케이션에서 자유롭게 다시 사용할 수 있는 강력한 플랫폼 간 플레이어를 제공하는 것입니다. W3C MSE(미디어 원본 확장)를 지원하는 모든 브라우저에서 MPEG-DASH 재생을 제공합니다. 현재는 Chrome, Microsoft Edge 및 IE11에서 지원되며 다른 브라우저는 MSE를 지원하지 않는 것으로 보입니다. DASH.js에 대한 자세한 내용은 GitHub dash.js 리포지토리를 참조하세요.

## <a name="creating-a-browser-based-streaming-video-player"></a>브라우저 기반 스트리밍 비디오 플레이어 만들기
재생, 일시 중지, 되감기 등의 원하는 컨트롤이 포함된 비디오 플레이어를 표시하는 간단한 웹 페이지를 만들려면 다음을 수행해야 합니다.

1. HTML 페이지 만들기
2. 비디오 태그 추가
3. dash.js 플레이어 추가
4. 플레이어 초기화
5. 일부 CSS 스타일 추가
6. MSE를 구현하는 브라우저에서 결과 보기

몇 줄의 JavaScript 코드로 플레이어 초기화를 완료할 수 있습니다. dash.js를 사용하면 매우 간단하게 브라우저 기반 애플리케이션에 MPEG-DASH 비디오를 포함할 수 있습니다.

## <a name="creating-the-html-page"></a>HTML 페이지 만들기
첫 번째 단계는 다음 예제에 나온 것처럼 **video** 요소가 포함된 표준 HTML 페이지를 만들고 이 파일을 basicPlayer.html로 저장하는 것입니다.

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>DASH.js 플레이어 추가
응용 프로그램에 dash.js 참조 구현을 추가 하려면 dash.js 프로젝트의 최신 버전에서 dash.all.js 파일을 해야 합니다. 이 파일은 애플리케이션의 JavaScript 폴더에 저장되어 있어야 합니다. 이 파일은 단일 파일에 모든 필요한 dash.js 코드를 간편하게 취합해 놓은 파일입니다. dash.js 리포지토리를 살펴보면 개별 파일, 테스트 코드 등을 찾을 수 있지만 dash.js만 사용하려는 경우는 dash.all.js 파일이 필요합니다.

애플리케이션에 dash.js 플레이어를 추가하려면 basicPlayer.html의 헤드 섹션에 다음 스크립트 태그를 추가합니다.

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

다음으로, 페이지가 로드될 때 플레이어를 초기화하는 함수를 만듭니다. dash.all.js를 로드하는 행 뒤에 다음 스크립트를 추가합니다.

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

이 함수는 먼저 DashContext를 만듭니다. 이 항목은 특정 런타임 환경에 맞게 애플리케이션을 구성하는 데 사용됩니다. 기술적인 관점에서 보면, 애플리케이션을 생성할 때 종속성 주입 프레임 워크를 사용해야 하는 클래스를 정의합니다. 대부분의 경우, Dash.di.DashContext를 사용합니다.

다음으로, dash.js 프레임워크의 기본 클래스인 MediaPlayer를 인스턴스화합니다. 이 클래스는 재생 및 일시 중지 등의 필요한 핵심 메서드를 포함하며, 비디오 요소와의 관계를 관리하고 재생할 비디오를 설명하는 MPD(미디어 프레젠테이션 설명) 파일의 해석을 관리할 수 있습니다.

플레이어가 비디오를 재생할 준비가 되도록 MediaPlayer 클래스의 startup() 함수가 호출됩니다. 무엇보다도 함수는 컨텍스트에 정의된 대로 필요한 모든 클래스가 로드되었는지 확인합니다. 플레이어가 준비되면 attachView() 함수를 사용하여 비디오 요소를 연결할 수 있습니다. 시작 함수를 사용하면 MediaPlayer가 비디오 스트림을 요소에 주입하고 필요에 따라 재생을 제어할 수도 있습니다.

MediaPlayer에 MPD 파일의 URL을 전달하여 재생해야 하는 비디오를 알립니다. 페이지가 완전히 로드되면 방금 만든 setupVideo() 함수가 실행되어야 합니다. body 요소의 onload 이벤트를 사용하여 이 작업을 수행합니다. `<body>` 요소를 다음으로 변경합니다.

```html
    <body onload="setupVideo()">
```

마지막으로, CSS를 사용하여 video 요소의 크기를 설정합니다. 적응 스트리밍 환경에서는 네트워크 상태 변화에 따라 재생이 조정되면 재생 중인 비디오의 크기가 변경될 수 있으므로 이 작업이 특히 중요합니다. 이 간단한 데모에서는 페이지의 헤드 섹션에 다음 CSS를 추가하여 사용 가능한 브라우저 창의 80%가 되도록 비디오 요소를 강제 적용합니다.

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>비디오 재생
비디오를 재생하려면 브라우저가 basicPlayback.html 파일을 가리키도록 지정하고 표시된 비디오 플레이어에서 재생을 클릭합니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목
[비디오 플레이어 애플리케이션 개발](media-services-develop-video-players.md)

[GitHub dash.js 리포지토리](https://github.com/Dash-Industry-Forum/dash.js) 

