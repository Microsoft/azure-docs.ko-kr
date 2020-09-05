---
title: Azure Media Services에서 Video.js 플레이어를 사용 하는 방법
description: 이 문서에서는 HTML video 개체와 JavaScript를 사용 하는 방법을 설명 Azure Media Services
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
ms.openlocfilehash: 2730c6ce523e618110cd29b13ba2f37115e2cbd0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267686"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Azure Media Services에서 Video.js 플레이어를 사용 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>개요

Video.js는 HTML5 환경을 위해 작성 된 웹 비디오 플레이어입니다. 플러그 인 또는 플래시를 사용 하지 않고 브라우저에서 적응 미디어 형식 (예: 대시 및 HLS)을 재생 합니다. 대신 Video.js는 개방형 웹 표준 MediaSource 확장 및 암호화 된 미디어 확장을 사용 합니다. 또한 데스크톱 및 모바일 장치에서 비디오 재생을 지원 합니다.

공식 설명서는에서 찾을 수 있습니다 [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>예제 코드
이 문서에 대 한 샘플 코드는 [Azure-samples/media services-3rdparty-플레이어-샘플](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)에서 제공 됩니다.

## <a name="implement-the-player"></a>플레이어 구현

1. `index.html`플레이어를 호스트 하는 파일을 만듭니다. 다음 코드 줄을 추가 합니다 (해당 하는 경우 최신 버전으로 바꿀 수 있음).

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. `index.js`다음 코드를 사용 하 여 파일을 추가 합니다.

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. `manifestUrl`Azure Portal의 스트리밍 로케이터 페이지에서 찾을 수 있는 자산의 스트리밍 로케이터의 HLS 또는 대시 URL로 대체 합니다.
    ![스트리밍 로케이터 Url](media/how-to-shaka-player/streaming-urls.png)

4. `protocolType`다음 옵션으로 대체 합니다.

    - HLS 프로토콜용 "application/x-mpegURL"
    - 대시 프로토콜의 경우 "application/대시 + xml"

### <a name="set-up-captions"></a>캡션 설정

`addRemoteTextTrack`메서드를 실행 하 고를 바꿉니다.

- `subtitleKind` ,, `"captions"` `"subtitles"` `"descriptions"` 또는 중 하나 `"metadata"`  
- `caption` CORS 오류가 발생 하지 않도록 vtt 파일 경로 (vtt 파일은 동일한 호스트에 있어야 함)
- `subtitleLang`언어에 대 한 BCP 47 코드 (예: `"eng"` 영어 또는 스페인어) `"es"`
- `subtitleLabel` 캡션의 원하는 표시 이름으로

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>토큰 인증 설정

토큰은 요청 헤더의 인증 필드에 설정 해야 합니다. CORS와 관련 된 문제를 방지 하려면이 토큰을 URL에 있는 요청에만 설정 해야 합니다 `'keydeliver'` . 다음 코드 줄은 작업을 수행 해야 합니다.

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

그런 다음 위의 함수를 이벤트에 연결 해야 합니다 `videojs.Hls.xhr.beforeRequest` .

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>AES-128 암호화 설정

Video.js는 추가 구성 없이 AES-128 암호화를 지원 합니다. 

> [!NOTE] 
> 현재는 암호화 및 HLS/대시 CMAF 콘텐츠에 문제가 있습니다. 이러한 [문제](https://github.com/videojs/video.js/issues/6717) 는 재생할 수 없습니다.

### <a name="set-up-drm-protection"></a>DRM 보호 설정

DRM 보호를 지원 하기 위해 [videojs-eme](https://github.com/videojs/videojs-contrib-eme) 공식 확장을 추가 해야 합니다. CDN 버전도 작동 합니다.

1. `index.js`위에서 자세히 설명 하는 파일에서 `videoJS.eme();` 비디오 소스를 추가 *하기 전에* 를 추가 하 여 EME 확장을 초기화 해야 합니다.

   ```javascript
    videoJS.eme();
   ```

2. 이제 다음과 같이 DRM 서비스의 Url과 해당 라이선스의 Url을 정의할 수 있습니다.

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>라이선스 URL을 가져오는 중

라이선스 URL을 얻기 위해 다음 작업을 수행할 수 있습니다.

- DRM 공급자 구성을 참조 하세요.
- 또는 샘플을 사용 하는 경우 `output.json` VODs에 대 한 *setup-vod.ps1* PowerShell 스크립트 또는 라이브 스트림의 *start-live.ps1* 스크립트를 실행할 때 생성 된 문서를 참조 하세요. 이 파일 내에서 어린이를 찾을 수도 있습니다.

#### <a name="using-tokenized-drm"></a>토큰화 DRM 사용

토큰화 된 DRM 보호를 지원 하기 위해 플레이어의 속성에 다음 줄을 추가 해야 합니다 `src` .

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>다음 단계

- [Azure Media Player 사용](../azure-media-player/azure-media-player-overview.md)  
- [빠른 시작: 콘텐츠 암호화](encrypt-content-quickstart.md)
