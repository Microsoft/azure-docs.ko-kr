---
title: Azure Media Services에서 Shaka 플레이어를 사용 하는 방법
description: 이 문서에서는 Azure Media Services에 Shaka 플레이어를 사용 하는 방법을 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: 5988a3ca2d44df161a27144d7bfb47ecab1e236e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267312"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Azure Media Services에서 Shaka 플레이어를 사용 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>개요

Shaka Player는 적응 미디어를 위한 오픈 소스 JavaScript 라이브러리입니다. 플러그 인 또는 플래시를 사용 하지 않고 브라우저에서 적응 미디어 형식 (예: 대시 및 HLS)을 재생 합니다. 대신, Shaka Player는 개방형 웹 표준 미디어 원본 확장 및 암호화 된 미디어 확장을 사용 합니다.

[Mux.js](https://github.com/videojs/mux.js/) 사용 하지 않는 것이 좋습니다. Shaka player는 HLS CMAF 형식을 지원 하지만 hls TS는 지원 하지 않습니다.

[Shaka player 설명서](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)에서 공식 설명서를 찾을 수 있습니다.

## <a name="sample-code"></a>예제 코드
이 문서에 대 한 샘플 코드는 [Azure-samples/media services-3rdparty-플레이어-샘플](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)에서 제공 됩니다.

## <a name="implementing-the-player"></a>플레이어 구현

플레이어의 고유한 인스턴스를 구현 해야 하는 경우 다음 지침을 따르세요.

1. `index.html`플레이어를 호스트 하는 파일을 만듭니다. 다음 코드 줄을 추가 합니다 (해당 하는 경우 최신 버전으로 바꿀 수 있음).

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 다음 코드를 사용 하 여 JavaScript 파일을 추가 합니다.

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. `manifestUrl`자산의 스트리밍 로케이터에서 HLS 또는 대시 URL로 대체 합니다 .이 URL은 Azure Portal의 스트리밍 로케이터 페이지에서 찾을 수 있습니다.
    ![스트리밍 로케이터 Url](media/how-to-shaka-player/streaming-urls.png)

1. 서버를 실행 하 고 (예: `npm http-server` ) 플레이어를 작동 해야 합니다.

## <a name="set-up-captions"></a>캡션 설정

### <a name="set-up-vod-captions"></a>VOD 캡션 설정

다음 코드 줄을 실행 하 고를 `captionUrl` vtt 디렉터리 (CORS 오류를 방지 하기 위해 vtt 파일은 동일한 호스트에 있어야 함), `lang` 언어에 대 한 두 문자 코드, `type` 또는 중 하나 `caption` `subtitle` 를 사용 하 여를 바꿉니다.

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>라이브 스트림 캡션 설정

라이브 스트림에서 캡션 사용은 다음 코드 줄을 추가 하 여 구성 됩니다.

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>토큰 인증 설정

다음 코드 줄을 실행 하 고를 `token` 토큰 문자열로 바꿉니다.

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>AES-128 암호화 설정

Shaka Player는 현재 AES-128 암호화를 지원 하지 않습니다.

이 기능의 상태를 준수 하는 GitHub [문제](https://github.com/google/shaka-player/issues/850) 에 대 한 링크입니다.

## <a name="set-up-drm-protection"></a>DRM 보호 설정

Shaka Player는 사용할 보안 URL이 필요한 EME (암호화 된 미디어 확장)를 사용 합니다. 따라서 DRM으로 보호 된 콘텐츠를 테스트 하려면 https를 사용 해야 합니다. 사이트에서 https를 사용 하는 경우 매니페스트와 모든 세그먼트에는 https도 사용 해야 합니다. 이는 혼합 된 콘텐츠 요구 사항 때문입니다.

라이선스 서버의 URL을 Shaka 관리 하는 기본 설정 순서:

1. 디버깅에 사용 되는 ClearKey 구성은 다른 모든 항목을 재정의 해야 합니다. 응용 프로그램은 여전히 ClearKey 라이선스 서버를 지정할 수 있습니다.
2. 응용 프로그램에서 구성 된 서버 (있는 경우)는 매니페스트에서 모든 항목을 재정의 해야 합니다.
3. 매니페스트 제공 라이선스 서버는 다른 것이 지정 되지 않은 경우에만 사용 됩니다.

Widevine 또는 PlayReady에 대 한 라이선스 서버 URL을 지정 하려면 다음 코드를 사용할 수 있습니다.

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

모든 FairPlay 콘텐츠를 사용 하려면 서버 인증서를 설정 해야 합니다. 플레이어 구성에서 설정 됩니다.

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

자세한 내용은 [Shaka PLAYER DRM 보호 설명서](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Media Player 사용](../azure-media-player/azure-media-player-overview.md)
* [빠른 시작: 콘텐츠 암호화](encrypt-content-quickstart.md)
