---
title: 오픈 소스 미디어 프레임워크용 부드러운 스트리밍 플러그 인
description: Adobe 오픈 소스 미디어 프레임워크용 Azure Media Services 부드러운 스트리밍 플러그 인을 사용하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c40d8b93a7487619cc94586c7e6b4cdc550435cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825594"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Adobe 오픈 소스 미디어 프레임워크용 Microsoft 부드러운 스트리밍 플러그 인을 사용하는 방법  
## <a name="overview"></a>개요
오픈 소스 미디어 프레임워크용 Microsoft 부드러운 스트리밍 플러그 인 2.0(SS for OSMF)은 OSMF의 기본 기능을 확장하며 기존 및 새로운 OSMF 플레이어에 Microsoft 부드러운 스트리밍 콘텐츠 재생을 추가합니다. 이 플러그 인은 또한 SMP(Strobe Media Playback)에 부드러운 스트리밍 재생 기능을 추가합니다.

SS for OSMF에는 두 가지 버전의 플러그 인이 포함됩니다.

* OSMF용 정적 부드러운 스트리밍 플러그 인(.swc)
* OSMF용 동적 부드러운 스트리밍 플러그 인(.swf)

이 문서는 OSMF 및 OSMF 플러그 인에 대한 실용적인 일반 지식을 가진 독자를 대상으로 합니다. OSMF에 대한 자세한 내용은 [OSMF 공식 사이트](http://osmf.org/)(영문)에 있는 설명서를 참조하십시오.

### <a name="smooth-streaming-plugin-for-osmf-20"></a>OSMF용 부드러운 스트리밍 플러그 인 2.0
이 플러그 인은 다음 기능을 통해 주문형 부드러운 스트리밍 콘텐츠의 로딩과 재생을 지원합니다.

* 주문형 부드러운 스트리밍 재생(재생, 일시 중지, 검색, 중지)
* Live Smooth Streaming 재생(재생)
* Live DVR 기능(일시 중지, 검색, DVR 재생, 라이브 전환)
* 비디오 코덱 지원 - H.264
* 오디오 코덱 지원 - AAC
* OSMF 기본 제공 API로 여러 오디오 언어 전환
* OSMF 기본 제공 API로 최대 재생 품질 선택
* OSMF 캡션 플러그 인으로 사이드카 선택 캡션
* Adobe&reg; Flash&reg; Player 11.4 이상.
* 이 버전은 OSMF 2.0만 지원함

## <a name="supported-features-and-known-issues"></a>지원되는 기능 및 알려진 문제
지원되는 기능, 지원되지 않는 기능 및 알려진 문제의 전체 목록은 [이 문서](https://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf)를 참조하세요.

## <a name="loading-the-plugin"></a>플러그 인 로드
OSMF 플러그 인은 정적으로(컴파일 시간에) 또는 동적으로(런타임에) 로드할 수 있습니다. OSMF용 부드러운 스트리밍 플러그 인 다운로드에는 동적 버전과 정적 버전이 모두 있습니다.

* 정적 로드: 정적으로 로드하려면 정적 라이브러리(SWC) 파일이 필요합니다. 정적 플러그 인이 프로젝트에 참조로 추가되고 컴파일 시간에 최종 출력 파일 내에 병합됩니다.
* 동적 로드: 동적으로 로드하려면 미리 컴파일된(SWF) 파일이 필요합니다. 동적 플러그 인이 런타임에 로드되며 프로젝트 출력에 포함되지 않습니다. (컴파일된 출력) 동적 플러그 인은 HTTP 및 FILE 프로토콜을 사용하여 로드할 수 있습니다.

정적 및 동적 로드에 대한 자세한 내용은 공식 [OSMF 플러그 인 페이지](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)(영문)를 참조하십시오.

### <a name="ss-for-osmf-static-loading"></a>SS for OSMF 정적 로드
아래 코드 조각은 OSMF용 SS 플러그 인을 정적으로 로드하고 OSMF MediaFactory 클래스를 사용하여 기본 비디오를 재생하는 방법을 보여 줍니다. SS for OSMF 코드를 포함하기 전에 먼저 프로젝트 참조에 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" 정적 플러그 인이 있는지 확인하십시오.

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS for OSMF 동적 로드
아래 코드 조각은 OSMF용 SS 플러그 인을 동적으로 로드하고 OSMF MediaFactory 클래스를 사용하여 기본 비디오를 재생하는 방법을 보여 줍니다. SS for OSMF 코드를 포함하기 전에 먼저 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" 동적 플러그 인을 프로젝트 폴더에 복사(FILE 프로토콜을 사용하여 로드하려는 경우)하거나 HTTP 로드용 웹 서버 아래에 복사합니다. 프로젝트 참조에 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc"를 포함할 필요는 없습니다.

package {

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media Playback 및 SS ODMF 동적 플러그 인
OSMF용 부드러운 스트리밍 동적 플러그 인은 [SMP(Strobe Media Playback)](http://osmf.org/strobe_mediaplayback.html)(영문)와 호환됩니다. SS for OSMF 플러그 인을 사용하여 SMP에 부드러운 스트리밍 콘텐츠 재생을 추가할 수 있습니다. 이렇게 하려면 다음 단계에 따라 "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf"를 HTTP 로드용 웹 서버 아래에 복사합니다.

1. [Strobe Media Playback 설정 페이지](http://osmf.org/dev/2.0gm/setup.html)(영문)로 이동합니다. 
2. src를 부드러운 스트리밍 원본(예: http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest))으로 설정합니다. 
3. 원하는 대로 구성을 변경하고 Preview and Update를 클릭합니다.
   
   **참고** 콘텐츠 웹 서버에는 유효한 crossdomain.xml이 필요합니다. 
4. 즐겨 사용하는 텍스트 편집기에서 코드를 복사하여 다음 예와 같이 간단한 HTML 페이지에 붙여넣습니다.

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. 부드러운 스트리밍 OSMF 플러그 인을 embed 태그에 추가하고 저장합니다.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. HTML 페이지를 저장하고 웹 서버에 게시합니다. 즐겨 사용하는 Flash&reg; Player 지원 인터넷 브라우저(Internet Explorer, Chrome, Firefox 등)를 사용하여 게시된 웹 페이지로 이동합니다.
3. Adobe&reg; Flash&reg; Player에서 부드러운 스트리밍 콘텐츠를 즐깁니다.

일반적인 OSMF 개발에 대한 자세한 내용은 공식 [OSMF 개발 페이지](http://osmf.org/resources.html)(영문)를 참조하십시오.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[OSMF용 Microsoft 적응 스트리밍 플러그 인 업데이트](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

