---
title: 필터 및 동적 매니페스트 | Microsoft 문서
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍을 보관하기 위해 Media Services는 동적 매니페스트를 생성합니다.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 229f89a8803f089c24981f56e00e36efe96de3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465201"
---
# <a name="filters-and-dynamic-manifests"></a>필터 및 동적 매니페스트

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [버전 2](media-services-dynamic-manifest-overview.md)
> * [버전 3](../latest/filters-dynamic-manifest-overview.md)

Media Services 2.17 버전부터 자산에 대한 필터를 정의할 수 있습니다. 이 필터는 고객이 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생하거나 자산과 연결된 모든 항목을 변환하는 대신 고객의 디바이스가 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정하는 등을 선택할 수 있도록 하는 서버 측 규칙입니다. 지정한 필터에 따라 비디오를 스트림하는 고객의 요청에 따라 생성된 **동적 매니페스트**를 통해 자산의 필터링이 이루어집니다.

이 토픽에서는 필터를 사용하는 것이 고객에게 유용한 일반적인 시나리오를 설명하고, 프로그래밍 방식으로 필터를 만드는 방법을 보여주는 토픽에 대한 링크를 제공합니다.

## <a name="overview"></a>개요
스트리밍 라이브 이벤트 또는 주문형 비디오 등의 콘텐츠를 고객에게 전달할 경우 다양한 네트워크 조건의 다양한 디바이스에 고품질 비디오를 제공하는 것이 목표입니다. 이 목표를 위해 다음을 수행합니다.

* 사용자의 스트림을 다중 비트 전송률([가변 품질](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) 비디오 스트림으로 인코딩(품질 및 네트워크 상태를 관리) 
* Media Services [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 스트림을 여러 프로토콜로 동적으로 다시 패키징(여러 디바이스의 스트리밍 관리 가능) Media Services서 다음과 같은 적응 비트 전송률 스트리밍 기술 제공을 지원합니다. HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 

### <a name="manifest-files"></a>매니페스트 파일
가변 품질 스트리밍을 위해 자산을 인코딩하면 **매니페스트** (재생 목록) 파일이 만들어집니다(텍스트 기반 또는 XML 기반 파일). **매니페스트** 파일에는 트랙 유형(오디오, 비디오 또는 텍스트), 트랙 이름, 시작 및 종료 시간, 비트 전송률(품질), 트랙 언어, 프레젠테이션 창(고정 기간의 슬라이딩 창), 비디오 코덱(FourCC) 등의 스트리밍 메타 데이터가 포함됩니다. 또한 다음으로 재생할 수 있는 비디오 조각 및 위치에 대한 정보를 제공하여 다음 조각을 검색하도록 플레이어에 지시합니다. 조각(또는 세그먼트)은 비디오 콘텐츠의 실제 "청크"입니다.

다음은 매니페스트 파일의 예입니다. 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>동적 매니페스트
클라이언트가 기본 자산의 매니페스트 파일에 설명된 것 보다 높은 유연성을 필요로 하는 경우의 [시나리오](media-services-dynamic-manifest-overview.md#scenarios) 가 있습니다. 예를 들면 다음과 같습니다.

* 디바이스별: 콘텐츠를 재생하는 데 사용되는 디바이스에서 지원하는 지정된 변환 및/또는 지정된 언어 트랙만 전달합니다("변환 필터링"). 
* 라이브 이벤트의 하위 클립을 표시하는 매니페스트를 줄입니다("하위 클립 필터링").
* 비디오의 시작 부분을 자릅니다("비디오 트리밍").
* 플레이어의 DVR 창 길이를 제한하기 위해 프레젠테이션 창(DVR)을 조정합니다("프레젠테이션 창 조정").

이러한 유연성을 실현하기 위해 Media Services는 미리 정의된 **필터**를 기반으로 [동적 매니페스트](media-services-dynamic-manifest-overview.md#filters)를 제공합니다.  필터를 정의하고 나면 클라이언트가 비디오의 특정 변환 또는 하위 클립을 스트리밍하는 데 해당 필터를 사용할 수 있습니다. 스트리밍 URL에서 필터를 지정할 수 있습니다. [동적 패키징](media-services-dynamic-packaging-overview.md)에서 지원하는 다음과 같은 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. HLS, MPEG-DASH 및 부드러운 스트리밍. 예를 들면 다음과 같습니다.

필터가 있는 MPEG DASH URL

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

필터가 있는 부드러운 스트리밍 URL

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


콘텐츠를 전달하고 스트리밍 URL을 빌드하는 방법에 대한 자세한 내용은 [콘텐츠 배달 개요](media-services-deliver-content-overview.md)를 참조하세요.

> [!NOTE]
> 동적 매니페스트는 자산 및 해당 자산의 기본 매니페스트를 변경하지 않습니다. 클라이언트는 필터와 관계 없이 스트림을 요청할 수 있습니다. 
> 
> 

### <a id="filters"></a>필터
자산 필터에는 두 가지 유형이 있습니다. 

* 전역 필터(Azure Media Services 계정의 모든 자산에 적용 가능, 계정의 수명 동안 보유) 
* 로컬 필터(필터를 만들 때 연결된 자산에만 적용 가능, 자산의 수명 동안 보유) 

전역 및 로컬 필터 형식은 속성과 정확하게 동일합니다. 둘 사이의 주요 차이는 어떤 시나리오에 어떤 유형의 필터가 더 적합한가의 차이입니다. 글로벌 필터는 일반적으로 디바이스 프로필에 적합(변환 필터링)한 반면 로컬 필터는 특정 자산을 트리밍하는 데 사용할 수 있습니다.

## <a id="scenarios"></a>일반적인 시나리오
앞에서 설명한 것처럼 스트리밍 라이브 이벤트 또는 주문형 비디오 등의 콘텐츠를 고객에게 전달할 때는 다양한 네트워크 조건의 다양한 디바이스에 고품질 비디오를 제공하는 것이 목표입니다. 또한 자산 필터링 및 **동적 매니페스트** 사용과 관련된 다른 요구 사항이 있을 수 있습니다. 다음 섹션에서는 다양한 필터링 시나리오에 대한 간략한 개요를 제공합니다.

* 자산과 연결된 모든 변환 대신 특정 디바이스에서 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정합니다. 
* 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생합니다.
* DVR 프레젠테이션 창을 조정합니다.

## <a name="rendition-filtering"></a>변환 필터링
자산을 여러 인코딩 프로필(H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) 및 여러 품질의 비트 전송률로 인코딩할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 이전 Android 디바이스는 H.264 Baseline+AACL만 지원합니다. 이점을 얻을 수 없는 디바이스에 높은 비트 전송률로 전송하면 대역폭과 디바이스 계산 성능을 낭비하게 됩니다. 이런 디바이스가 해당하는 모든 정보를 표시하기 위해서는 디코딩을 통해 규모를 축소해야 합니다.

동적 매니페스트를 사용하면 모바일, 콘솔, HD/SD 등과 같은 디바이스 프로필을 만들고 각 프로필의 일부로 제공할 트랙 및 품질을 포함할 수 있습니다.

![변환 필터링 예제][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 다음 스트리밍 프로토콜 중 하나로 동적으로 패키징할 수 있습니다. HLS, Smooth 및 MPEG DASH.  다이어그램의 맨 위에 필터가 없는 자산을 위한 HLS 매니페스트가 나와 있습니다(7개의 모든 변환 포함).  왼쪽 아래에는 "ott" 필터가 적용된 HLS 매니페스트가 표시되어 있습니다. "ott" 필터는 1Mbps 아래의 모든 비트 전송률을 제거하도록 지정하며, 이 때문에 응답에서 맨 아래의 두 품질 수준이 제거됩니다. 오른쪽 아래에는 "mobile" 필터가 적용된 HLS 매니페스트가 나와 있습니다. "mobile" 필터는 해상도가 720p보다 높은 변환을 제거하도록 지정하며, 이 때문에 두 개의 1080p 변환이 제거됩니다.

![변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등 여러 오디오 언어가 포함될 수 있습니다. 일반적으로 플레이어 SDK는 기본 오디오 트랙 선택 및 사용자별로 사용 가능한 오디오 트랙 선택을 관리합니다. 이러한 플레이어 SDK를 개발하는 것은 까다로운 일로, 디바이스별 플레이어 프레임워크 간에 서로 다른 구현이 필요합니다. 또한 일부 플랫폼은 플레이어 API가 제한되어 있고 오디오 선택 기능이 포함되어 있지 않으므로 사용자는 기본 오디오 트랙을 선택하거나 변경할 수 없습니다. 자산 필터를 사용하면 원하는 오디오 언어만 포함하는 필터를 만들어 동작을 제어할 수 있습니다.

![언어 트랙 필터링][language_filter]

## <a name="trimming-start-of-an-asset"></a>자산의 시작 부분 트리밍
대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트의 시작하기 전에 "프로그램이 곧 시작됩니다"와 같은 슬레이트를 포함할 수 있습니다. 프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나 이 정보가 클라이언트에는 표시되지 않아야 합니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![트리밍 시작][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기
많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다. 다음으로 후처리 없이 별도의 자산을 만들지 않고 가상 프로그램을 개별적으로 게시합니다. 따라서 CDN에서 캐시된 기존 조각의 이점을 얻지 못하게 됩니다. 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용하면 시작/종료 시간을 사용하여 필터를 만들고 라이브 보관의 위에 가상 뷰를 만들 수 있습니다. 

![하위 클립 필터][subclip_filter]

필터링된 자산:

![스키][skiing]

## <a name="adjusting-presentation-window-dvr"></a>프레젠테이션 창(DVR) 조정
현재, Azure Media Services는 5분에서 25시간까지 기간을 구성할 수 있는 순환식 보관을 제공합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창 외부의 데이터를 사용하여 클립을 강조하거나 여러 디바이스에 대해 서로 다른 DVR 창을 제공하려고 할 수 있습니다. 예를 들어 대부분의 모바일 디바이스는 큰 DVR 창을 처리하지 못합니다. 모바일 디바이스의 경우 2분, 데스크톱 클라이언트의 경우 1시간 동안 DVR 창을 표시할 수 있습니다.

![DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정
매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링을 통해 뷰어가 스트림을 수신하기 전에 브로드캐스터가 미리 보기 게시 지점에서 프레젠테이션을 보고 광고 삽입 지점을 만들 수 있습니다(30초까지 백오프됨). 그런 다음, 브로드캐스터는 이러한 광고가 수신되는 시간에 맞춰 해당 광고를 클라이언트 프레임워크에 푸시하고 광고 기회 전에 정보를 처리할 수 있습니다.

광고 지원 외에도 LiveBackoff 설정은 뷰어 위치를 조정하는 데 사용할 수 있습니다. 따라서 클라이언트가 라이브 에지를 생성하고 적중할 때 HTTP 404 또는 412 오류를 발생시키지 않고 서버의 조각을 가져올 수 있습니다.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합
단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 라이브 보관에서 슬레이트를 제거하고 사용 가능한 비트 전송률을 필터링하는 "범위 규칙"을 정의할 수 있습니다. 여러 필터링 규칙을 적용할 때 최종 결과는 모든 규칙의 교집합입니다.

![다중 규칙][multiple-rules]

## <a name="create-filters-programmatically"></a>프로그래밍 방식으로 필터 만들기
다음 문서에서는 필터와 연결된 Media Services 엔터티에 대해 설명합니다. 또한 이 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 보여줍니다.  

[REST API를 사용하여 필터 만들기](media-services-rest-dynamic-manifest.md)

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)
단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 

다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. 비디오 품질을 제한하기 위해 Android 또는 iPAD와 같은 모바일 디바이스에 대한 비디오 품질을 필터링해야 합니다. 원치 않는 품질을 제거하기 위해 디바이스 프로필에 적합한 전역 필터를 만듭니다. 이 문서에서 설명했듯이 전역 필터는 다른 추가적인 연결 없이 동일한 Media Services 계정의 모든 자산에 사용할 수 있습니다. 
2. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 이를 위해서 로컬 필터를 만들고 시작/종료 시간을 설정합니다. 
3. 트리밍 필터에 품질 필터링을 추가해야 하는 결합은 필터 사용을 더 어렵게 합니다. 이렇게 하지 않고 이러한 필터 두 개를 결합하고자 합니다.

필터를 결합하려면 필터 이름을 세미콜론으로 구분한 매니페스트/재생 목록 URL로 설정해야 합니다. 품질을 필터링하는 *MyMobileDevice*라는 필터와 특정 시작 시간으로 설정된 *MyStartTime*이라는 필터가 있다고 가정해 봅시다. 이러한 필터를 다음과 같이 결합할 수 있습니다.

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

최대 3개의 필터를 결합할 수 있습니다. 

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) 를 참조하세요.

## <a name="know-issues-and-limitations"></a>알려진 문제 및 제한 사항
* 동적 매니페스트는 GOP 경계(키 프레임)에서 작동하므로 트리밍에는 GOP 정확도가 있습니다. 
* 로컬 및 전역 필터에 동일한 필터 이름을 사용할 수 있습니다. 로컬 필터의 우선 순위가 더 높으며 글로벌 필터에 우선합니다.
* 필터를 업데이트 하는 경우, 규칙을 새로 고치는 스트리밍 엔드포인트에서 최대 2분이 소요될 수 있습니다. 콘텐츠가 일부 필터로 처리된 경우(및 프록시와 CDN 캐시에서 캐시된 경우) 이들 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터 업데이트 후에는 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우에 서로 다른 필터를 사용 하는 것이 좋습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[고객에 콘텐츠 배달 개요](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
