---
title: 클라이언트 쪽에 광고 삽입 | Microsoft 문서
description: 이 문서에서는 클라이언트 쪽의 미디어에 광고를 삽입 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 70db1ff63e3c2a7e7806e5ef3c55b4e4af4a5259
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263572"
---
# <a name="inserting-ads-on-the-client-side"></a>클라이언트 쪽에 광고 삽입

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

이 문서에서는 클라이언트 측에서 다양한 유형의 광고를 삽입하는 방법에 대해 설명합니다.

라이브 스트리밍 비디오에서 캡션 및 광고 지원에 대한 정보는 [지원되는 캡션 및 Ad 삽입 표준](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads)을 참조하세요.

> [!NOTE]
> Azure Media Player는 현재 광고를 지원하지 않습니다.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>미디어에 광고 삽입
Azure Media Services는 Windows 미디어 플랫폼: 플레이어 프레임워크를 통해 광고 삽입에 대한 지원을 제공합니다. 광고를 지원하는 플레이어 프레임워크는 Windows 8, Silverlight, Windows Phone 8 및 iOS 디바이스에 사용할 수 있습니다. 각 플레이어 프레임워크는 플레이어 애플리케이션을 구현하는 방법을 보여주는 샘플 코드를 포함합니다. media:list에는 세 가지 유형의 광고를 삽입할 수 있습니다.

* **선형** -기본 비디오를 일시 중지하는 전체 프레임 광고
* **비선형** -기본 비디오를 재생할 때 표시되는 오버레이 광고로, 일반적으로 플레이어 내에 배치된 로고나 기타 정적 이미지
* **동반** -플레이어 외부에 표시되는 광고

광고는 기본 비디오 타임 라인에 언제든지 배치할 수 있습니다. 플레이어에 광고를 재생하는 시기 및 재생하는 광고를 알려야 합니다. 이 작업은 표준 XML 기반 파일 집합을 사용하여 수행됩니다. VAST(Video Ad Service Template), VMAP(Digital Video Multiple Ad Playlist), MAST(Media Abstract Sequencing Template) 및 VPAID(Digital Video Player Ad Interface Definition). VAST 파일은 표시할 광고를 지정합니다. VMAP 파일은 다양한 광고를 언제 재생할지를 지정합니다(이 파일에 VAST XML이 포함됨). MAST 파일을 사용하여 광고의 순서를 지정할 수도 있습니다(이 파일에도 VAST XML이 포함됨). VPAID 파일은 비디오 플레이어와 광고 또는 광고 서버 간의 인터페이스를 정의합니다.

각 플레이어 프레임워크는 서로 다르게 작동하고 각각 별도의 문서에서 설명합니다. 이 문서에서는 광고 삽입에 사용되는 기본 메커니즘을 설명합니다. 비디오 플레이어 애플리케이션은 광고 서버에서 광고를 요청합니다. 광고 서버는 다양한 방법으로 응답할 수 있습니다.

* VAST 파일 반환
* VMAP 파일 반환(포함된 VAST와 함께)
* MAST 파일 반환(포함된 VAST와 함께)
* VAST 파일 반환(VPAID 광고와 함께)

### <a name="using-a-video-ad-service-template-vast-file"></a>VAST(Video Ad Service Template) 파일 사용
VAST 파일은 표시할 광고를 지정합니다. 다음 XML은 선형 광고에 대한 VAST 파일의 예입니다.

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

선형 광고는 <**Linear**> 요소로 설명됩니다. 광고 기간, 추적 이벤트, 클릭 방문, 클릭 추적 및 다양한 **MediaFile** 요소를 지정합니다. 추적 이벤트는 <**TrackingEvents**> 요소 내에서 지정되고, 이를 통해 광고 서버에서 광고를 보는 동안 발생하는 다양한 이벤트를 추적할 수 있습니다. 이 경우 start, midpoint, complete 및 expand 이벤트가 추적됩니다. start 이벤트는 광고가 표시될 때 발생합니다. midpoint 이벤트는 광고 타임라인의 50% 이상이 표시되었을 때 발생합니다. complete 이벤트는 광고가 끝까지 실행되었을 때 발생합니다. expand 이벤트는 사용자가 비디오 플레이어를 전체 화면으로 확장할 때 발생합니다. 광고 방문은 <**VideoClicks**> 요소 내의 <**ClickThrough**> 요소로 지정되고, 사용자가 광고를 클릭할 때 표시할 리소스에 대한 URI를 지정합니다. 클릭 추적은 <**VideoClicks**> 요소 내의 <**ClickTracking**> 요소에서 지정되고, 사용자가 광고를 클릭할 때 플레이어에서 요청할 추적 리소스를 지정합니다. <**MediaFile**> 요소는 광고의 특정 인코딩에 대한 정보를 지정합니다. <**MediaFile**> 요소가 둘 이상 있으면 비디오 플레이어에서 플랫폼에 가장 적합한 인코딩을 선택할 수 있습니다.

선형 광고는 지정된 순서대로 표시될 수 있습니다. 이렇게 하려면 VAST 파일에 다른 `<Ad>` 요소를 추가하고 시퀀스 특성을 사용하여 순서를 지정합니다. 다음 예제에서는 이에 대해 설명합니다.

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

비선형 광고도 `<Creative>` 요소에서 지정합니다. 다음 예제에서는 비선형 광고를 설명하는 `<Creative>` 요소를 보여 줍니다.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

<**NonLinearAds**> 요소에는 각각 비선형 광고를 설명할 수 있는 <**NonLinear**> 요소가 둘 이상 포함될 수 있습니다. <**NonLinear**> 요소는 비선형 광고의 리소스를 지정합니다. 리소스는 <**StaticResource**>, <**IFrameResource**> 또는 <**HTMLResource**>일 수 있습니다. \<**StaticResource**> 비 HTML 리소스를 설명 하 고 리소스가 표시 되는 방법을 지정 하는 creativeType 특성을 정의 합니다.

Image/gif, image/jpeg, image/png – 리소스가 HTML <**img**> 태그에 표시됩니다.

Application/x-javascript – 리소스가 HTML <**script**> 태그에 표시됩니다.

Application/x-shockwave-flash – 리소스가 Flash Player에 표시됩니다.

**IFrameResource**는 IFrame에 표시할 수 있는 HTML 리소스를 설명합니다. **HTMLResource**는 웹 페이지에 삽입할 수 있는 HTML 코드 조각을 설명합니다. **TrackingEvents**는 추적 이벤트와 이벤트가 발생할 때 요청할 URI를 지정합니다. 이 샘플에서는 acceptInvitation 및 collapse 이벤트가 추적됩니다. **NonLinearAds** 요소 및 해당 자식에 대한 자세한 내용은 IAB.NET/VAST를 참조하세요. **TrackingEvents** 요소는 **NonLinear** 요소가 아닌 **NonLinearAds** 요소 내에 있습니다.

캠페인(Companion) 광고는 `<CompanionAds>` 요소 내에서 정의됩니다. `<CompanionAds>` 요소에는 하나 이상의 `<Companion>` 요소가 포함될 수 있습니다. 각 `<Companion>` 요소는 동반을 설명하며, 비선형 광고에서와 같은 방법으로 지정되는 `<StaticResource>`, `<IFrameResource>` 또는 `<HTMLResource>`를 포함할 수 있습니다. VAST 파일은 여러 캠페인 광고를 포함할 수 있고 플레이어 애플리케이션은 표시할 가장 적합한 광고를 선택할 수 있습니다. VAST에 대한 자세한 내용은 [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf)(영문)을 참조하세요.

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>디지털 VMAP(Video Multiple Ad Playlist) 파일 사용
VMAP 파일을 사용하여 광고가 발생하는 시기, 각 광고가 지속되는 기간, 광고 시간 내에 표시될 수 있는 광고 수, 광고 시간 중에 표시될 수 있는 광고 유형을 지정할 수 있습니다. 단일 광고를 정의하는 예제 VMAP 파일은 다음과 같습니다.

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

VMAP 파일은 각각 광고를 정의하는 하나 이상의 `<AdBreak>` 요소를 포함하는 `<VMAP>` 요소로 시작합니다. 각 광고는 광고 유형, 광고 ID 및 시간 오프셋을 지정합니다. breakType 특성은 광고 중에 표시할 수 있는 광고 유형을 선형, 비선형 또는 표시 중에서 지정합니다. 표시 광고는 VAST 동반 광고에 매핑됩니다. 둘 이상의 광고 유형은 공백 없이 쉼표로 구분된 목록으로 지정할 수 있습니다. breakID는 광고의 선택적 식별자입니다. timeOffset은 광고가 표시되어야 하는 시기를 지정합니다. 다음 방법의 하나로 지정할 수 있습니다.

1. Time – hh:mm:ss 또는 hh:mm:ss.mmm 형식으로 지정합니다. 여기서 .mmm은 밀리초입니다. 이 특성 값은 비디오 타임라인 시작부터 광고 시작까지 지나는 시간을 지정합니다.
2. Percentage – n% 형식으로 지정합니다. 여기서 n은 광고를 재생하기 전에 재생할 비디오 타임라인의 백분율입니다.
3. Start/End – 비디오가 표시되기 전이나 후에 광고가 표시되도록 지정합니다.
4. Position – 라이브 스트리밍과 같이 광고 타이밍을 알 수 없을 때 광고 순서를 지정합니다. 각 광고 순서는 #n 형식으로 지정합니다. 여기서 n은 정수 1 이상입니다. 1은 광고가 첫 번째 기회에 재생되어야 함을 나타내고, 2는 광고가 두 번째 기회에 재생되어야 함을 나타냅니다.

`<AdBreak>` 요소 내에는 <**AdSource**> 요소 하나가 있을 수 있습니다. <**AdSource**> 요소는 다음 특성을 포함합니다.

1. Id – 광고 소스의 식별자를 지정합니다.
2. allowMultipleAds – 광고 중에 여러 광고를 표시할 수 있는지를 지정하는 부울 값입니다.
3. followRedirects – 비디오 플레이어가 광고 응답 내에서 리디렉션을 제공해야 하는지 지정하는 선택적 부울 값입니다.

<**AdSource**> 요소는 플레이어에 인라인 광고 응답이나 광고 응답에 대한 참조를 제공합니다. 다음 요소의 하나를 포함할 수 있습니다.

* `<VASTAdData>`는 VAST 광고 응답이 VMAP 파일 내에 포함됨을 나타냅니다.
* `<AdTagURI>`는 다른 시스템에서 나오는 광고 응답을 참조하는 URI입니다.
* `<CustomAdData>`는 비 VAST 응답을 나타내는 임의 문자열입니다.

이 예제에서 인라인 광고 응답은 VAST 광고 응답을 포함하는 `<VASTAdData>` 요소로 지정합니다. 기타 요소에 대한 자세한 내용은 [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)(영문)를 참조하세요.

<**AdBreak**> 요소는 하나의 <**TrackingEvents**> 요소도 포함할 수 있습니다. <**TrackingEvents**> 요소를 사용하여 광고의 시작 또는 종료를 추적하거나 광고 중에 오류가 발생했는지를 추적할 수 있습니다. <**TrackingEvents**> 요소는 각각 추적 이벤트와 추적 URI를 지정하는 <**Tracking**> 요소를 하나 이상 포함합니다. 가능한 추적 이벤트는 다음과 같습니다.

1. breakStart – 광고의 시작을 추적합니다.
2. breakStart – 광고의 완료를 추적합니다.
3. error – 광고 중에 발생한 오류를 추적합니다.

다음 예제에서는 추적 이벤트를 지정하는 VMAP 파일을 보여 줍니다.

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

<**TrackingEvents**> 요소 및 해당 자식에 대한 자세한 내용은 http://iab.net/VMAP.pdf를 참조하세요.

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>MAST(Media Abstract Sequencing Template) 파일 사용
MAST 파일을 사용하여 광고가 표시되는 시기를 정의하는 트리거를 지정할 수 있습니다. 재생 전 광고, 재생 중 광고 및 재생 후 광고에 대한 트리거를 포함하는 예제 MAST 파일은 다음과 같습니다.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


MAST 파일은 하나의 **triggers** 요소를 포함하는 **MAST** 요소로 시작합니다. `<triggers>` 요소는 광고가 재생되는 시기를 정의하는 **trigger** 요소를 하나 이상 포함합니다.

**trigger** 요소는 광고 재생을 언제 시작할지를 지정하는 **startConditions** 요소를 포함합니다. **startConditions** 요소는 `<condition>` 요소를 하나 이상 포함합니다. 각 `<condition>`이 true로 평가되면 `<condition>`이 각각 **startConditions** 또는 **endConditions** 요소 안에 포함되는지에 따라 트리거가 시작되거나 취소됩니다. 여러 `<condition>` 요소가 있으면 암시적 OR로 처리되고, 모든 조건이 true로 평가되어 트리거가 시작됩니다. `<condition>` 요소는 중첩될 수 있습니다. 자식 `<condition>` 요소가 기본 설정되면 암시적 AND로 처리되고, 트리거가 시작되려면 모든 조건이 true로 평가되어야 합니다. `<condition>` 요소는 조건을 정의하는 다음 특성을 포함합니다.

1. **type** – 조건, 이벤트 또는 속성 유형을 지정
2. **name** – 평가 중에 사용할 속성 또는 이벤트의 이름
3. **value** – 속성을 평가할 기준 값
4. **operator** – 평가 중에 사용할 연산: EQ(같음), NEQ(같지 않음), GTR(보다 큼), GEQ(크거나 같음), LT(보다 작음), LEQ(작거나 같음), MOD(나머지)

**endConditions**도 `<condition>` 요소를 포함합니다. 조건이 true로 평가되면 트리거가 다시 설정됩니다. `<trigger>` 요소에는 `<source>` 요소를 하나 이상 포함하는 `<sources>` 요소도 포함됩니다. `<source>` 요소는 광고 응답에 대한 URI와 광고 응답 유형을 정의합니다. 다음 예제에서는 VAST 응답에 URI가 지정되어 있습니다.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>VPAID(Video Player-Ad Interface Definition) 사용
VPAID는 실행 가능한 광고 단위가 비디오 플레이어와 통신하는 데 사용되는 API입니다. 이를 통해 높은 수준의 대화형 광고 환경이 가능합니다. 사용자는 광고를 조작할 수 있고 광고는 뷰어가 수행한 작업에 응답할 수 있습니다. 예를 들어 사용자가 자세한 정보나 더 긴 광고를 볼 수 있도록 하는 단추가 광고에 표시될 수 있습니다. 비디오 플레이어에서 VPAID API를 지원해야 하고 실행 가능한 광고에서 API를 구현해야 합니다. 플레이어가 광고 서버의 광고를 요청하면 서버에서는 VPAID 광고가 포함된 VAST 응답으로 응답할 수 있습니다.

실행 가능한 광고는 Adobe Flash™ 또는 JavaScript와 같이 웹 브라우저에서 실행할 수 있는 런타임 환경에서 실행되어야 하는 코드로 생성됩니다. 광고 서버에서 VPAID 광고가 포함된 VAST 응답을 반환할 때 `<MediaFile>` 요소의 apiFramework 특성 값은 “VPAID”여야 합니다. 이 특성은 포함된 광고가 VPAID 실행 가능한 광고임을 지정합니다. type 특성은 “application/x-shockwave-flash” 또는 “application/x-javascript”와 같은 실행 파일의 MIME 형식으로 설정되어야 합니다. 다음 XML 조각에서는 VPAID 실행 가능한 광고가 포함된 VAST 응답의 `<MediaFile>` 요소를 보여 줍니다.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

실행 가능한 광고는 VAST 응답의 `<Linear>` 또는 `<NonLinear>` 요소 내에서 `<AdParameters>` 요소를 사용하여 초기화할 수 있습니다. `<AdParameters>` 요소에 대한 자세한 내용은 [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf)(영문)을 참조하세요. VPAID API에 대한 자세한 내용은 [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)(영문)을 참조하세요.

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>광고 지원이 포함된 Windows 또는 Windows Phone 8 플레이어 구현
Microsoft Media Platform: Windows 8 및 Windows Phone 8용 플레이어 프레임워크에는 프레임워크를 사용하여 비디오 플레이어 애플리케이션을 구현하는 방법을 보여 주는 샘플 애플리케이션 컬렉션이 포함됩니다. [Windows 8 및 Windows Phone 8용 플레이어 프레임워크](https://playerframework.codeplex.com)에서 플레이어 프레임워크와 샘플을 다운로드할 수 있습니다.

Microsoft.PlayerFramework.Xaml.Samples 솔루션을 열면 프로젝트 내에 많은 폴더가 표시됩니다. Advertising 폴더에는 광고 지원이 있는 비디오 플레이어를 만드는 방법과 관련된 샘플 코드가 들어 있습니다. Advertising 폴더 내에는 각각 광고를 다르게 삽입하는 방법을 보여 주는 다양한 XAML/cs 파일이 있습니다. 다음 목록은 각각에 대해 설명합니다.

* AdPodPage.xaml - 광고 포드를 표시하는 방법을 보여 줍니다.
* AdSchedulingPage.xaml - 광고를 예약하는 방법을 보여 줍니다.
* FreeWheelPage.xaml - FreeWheel 플러그 인을 사용하여 광고를 예약하는 방법을 보여 줍니다.
* MastPage.xaml - MAST 파일을 사용하여 광고를 예약하는 방법을 보여 줍니다.
* ProgrammaticAdPage.xaml - 프로그래밍 방식으로 광고를 비디오에 예약하는 방법을 보여 줍니다.
* ScheduleClipPage.xaml - VAST 파일 없이 광고를 예약하는 방법을 보여 줍니다.
* VastLinearCompanionPage.xaml - 선형 및 동반 광고를 삽입하는 방법을 보여 줍니다.
* VastNonLinearPage.xaml - 비선형 광고를 삽입하는 방법을 보여 줍니다.
* VmapPage.xaml - VMAP 파일을 사용하여 광고를 지정하는 방법을 보여 줍니다.

이들 샘플에서는 각각 플레이어 프레임워크에서 정의된 MediaPlayer 클래스를 사용합니다. 대부분 샘플에서는 다양한 광고 응답 형식에 대한 지원을 추가하는 플러그 인을 사용합니다. ProgrammaticAdPage 샘플에서는 MediaPlayer 인스턴스를 프로그래밍 방식으로 조작합니다.

### <a name="adpodpage-sample"></a>AdPodPage 샘플
이 샘플에서는 AdSchedulerPlugin을 사용하여 광고를 표시할 시기를 정의합니다. 이 예제에서는 재생 중 광고(중간 광고: mid-roll)가 5초 후에 재생되도록 예약 설정됩니다. 광고 포드(순서대로 표시할 광고 그룹)는 광고 서버에서 반환되는 VAST 파일로 지정됩니다. VAST 파일에 대한 URI는 `<RemoteAdSource>` 요소에서 지정됩니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

AdSchedulerPlugin에 대한 자세한 내용은 [Windows 8 및 Windows Phone 8의 플레이어 프레임워크에서 광고](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
이 샘플에서도 AdSchedulerPlugin을 사용합니다. 세 가지 광고인 재생 전 광고, 재생 중 광고 및 재생 후 광고를 예약합니다. 각 광고의 VAST 파일에 대한 URI는 `<RemoteAdSource>` 요소에서 지정됩니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
이 샘플에서는 광고 예약 정보와 광고 콘텐츠를 지정하는 SmartXML 파일을 가리키는 URI를 지정하는 Source 특성을 지정하는 FreeWheelPlugin을 사용합니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
이 샘플에서는 MAST 파일을 사용할 수 있게 하는 MastSchedulerPlugin을 사용합니다. Source 특성은 MAST 파일의 위치를 지정합니다.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
이 샘플에서는 MediaPlayer를 프로그래밍 방식으로 조작합니다. ProgrammaticAdPage.xaml 파일은 MediaPlayer를 인스턴스화합니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

ProgrammaticAdPage.xaml.cs 파일은 AdHandlerPlugin을 만들고, TimelineMarker를 추가하여 광고가 언제 표시되는지를 지정한 다음, MarkerReached 이벤트에 대한 처리기를 추가합니다(이 처리기는 VAST 파일에 대한 URI를 지정하여 RemoteAdSource를 로드한 다음, 광고를 재생함).

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
이 샘플에서는 AdSchedulerPlugin을 사용하여 광고가 포함된 .wmv 파일을 지정하는 방법으로 재생 중 광고를 예약합니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
이 샘플에서는 AdSchedulerPlugin을 사용하여 재생 중 선형 광고를 캠페인(companion) 광고와 함께 예약하는 방법을 보여줍니다. `<RemoteAdSource>` 요소는 VAST 파일의 위치를 지정합니다.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
이 샘플에서는 AdSchedulerPlugin을 사용하여 선형 및 비선형 광고를 예약합니다. VAST 파일 위치는 `<RemoteAdSource>` 요소로 지정합니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
이 샘플에서는 VmapSchedulerPlugin을 사용하여 VMAP 파일을 사용하는 광고 예약을 설정합니다. VMAP 파일에 대한 URI는 `<VmapSchedulerPlugin>` 요소의 Source 특성으로 지정합니다.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>광고 지원이 포함된 iOS 비디오 플레이어 구현
Microsoft Media Platform: iOS용 플레이어 프레임워크에는 프레임워크를 사용하여 비디오 플레이어 애플리케이션을 구현하는 방법을 보여 주는 샘플 애플리케이션 컬렉션이 포함됩니다. [Azure Media Player 프레임워크](https://github.com/CloudMetal/azure-media-player-framework)에서 플레이어 프레임워크와 샘플을 다운로드할 수 있습니다. GitHub 페이지에는 플레이어 프레임 워크에 대 한 추가 정보를 포함 하는 Wiki에 대 한 링크와 플레이어 샘플에 대 한 소개 인 [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework)가 있습니다.

### <a name="scheduling-ads-with-vmap"></a>VMAP를 사용하여 광고 예약
다음 예제에서는 VMAP 파일을 사용하여 광고를 예약하는 방법을 보여 줍니다.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>VAST를 사용하여 광고 예약
다음 샘플에서는 런타임에 바인딩 VAST 광고를 예약하는 방법을 보여 줍니다.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   다음 샘플에서는 컴파일 시 바인딩 VAST 광고를 예약하는 방법을 보여 줍니다.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

다음 샘플에서는 RCE(가편집)를 사용하여 광고를 삽입하는 방법을 보여 줍니다.

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

다음 예제에서는 광고 포드를 예약하는 방법을 보여 줍니다.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

다음 예제에서는 비고정 재생 중 광고를 예약하는 방법을 보여 줍니다. 비고정 광고는 뷰어가 수행하는 검색과 관계없이 한 번만 재생됩니다.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

다음 예제에서는 고정 재생 중 광고를 예약하는 방법을 보여 줍니다. 고정 광고는 비디오 타임라인의 지정된 지점에 도달할 때마다 표시됩니다.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

다음 샘플에서는 재생 후 광고를 예약하는 방법을 보여 줍니다.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

다음 샘플에서는 재생 전 광고를 예약하는 방법을 보여 줍니다.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

다음 샘플에서는 재생 중 오버레이 광고를 예약하는 방법을 보여 줍니다.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
