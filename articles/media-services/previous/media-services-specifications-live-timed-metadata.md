---
title: Azure Media Services - 라이브 스트리밍의 신호 타이밍 메타데이터 | Microsoft Docs
description: 이 사양은 라이브 스트리밍 내의 신호 타이밍 메타데이터에 대해 Media Services에서 지원하는 두 가지 모드를 설명합니다. 여기에는 제네릭 타이밍 메타데이터 신호 및 광고 스플라이스 삽입을 위한 SCTE-35 신호에 대한 지원이 포함됩니다.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: 10dbf7e8cf67ab721cf525d4a1e7594473592bd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459116"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>라이브 스트리밍의 신호 타이밍 메타데이터 


## <a name="1-introduction"></a>1 소개 
광고 또는 사용자 지정 이벤트를 클라이언트 플레이어에 쉽게 삽입할 수 있도록 하기 위해 방송사에서 비디오 내에 포함되는 타이밍 메타데이터를 사용하는 경우가 많습니다. 이러한 시나리오를 사용할 수 있도록 Media Services는 라이브 스트리밍 채널의 수집 지점에서 클라이언트 애플리케이션까지 미디어와 함께 타이밍 메타데이터를 전송하도록 지원합니다.
이 사양은 라이브 스트리밍 신호 내의 타이밍 메타데이터에 대해 Media Services에서 지원하는 두 가지 모드를 설명합니다.

1. [SCTE-67]에서 설명한 권장 사례에 따른 [SCTE-35] 신호

2. [SCTE-35]가 아닌 메시지에 대한 제네릭 타이밍 메타데이터 신호 모드

### <a name="12-conformance-notation"></a>1.2 적합성 표기법
이 문서의 키워드인 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" 및 "OPTIONAL"은 RFC 2119에서 설명한 대로 해석되어야 합니다.

### <a name="13-terms-used"></a>1.3 사용되는 용어

| 용어              | 정의                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 프레젠테이션 시간 | 이벤트가 뷰어에 제공되는 시간입니다. 시간은 뷰어에 이벤트가 표시되는 미디어 타임라인의 순간을 나타냅니다. 예를 들어 SCTE-35 splice_info() 명령 메시지의 프레젠테이션 시간은 splice_time()입니다. |
| 도착 시간      | 이벤트 메시지가 도착하는 시간입니다. 이벤트 메시지가 이벤트의 프레젠테이션 시간에 앞서 보내지므로 이 시간은 일반적으로 이벤트의 프레젠테이션 시간과 구분됩니다.                                     |
| 스파스 트랙      | 연속적이지 않으며, 부모 트랙 또는 컨트롤 트랙과 시간 동기화되는 미디어 트랙입니다.                                                                                                                                    |
| 원본            | Azure 미디어 스트리밍 서비스                                                                                                                                                                                                |
| 채널 싱크      | Azure 미디어 라이브 스트리밍 서비스                                                                                                                                                                                           |
| HLS               | Apple HTTP 라이브 스트리밍 프로토콜                                                                                                                                                                                               |
| DASH              | HTTP를 통한 동적 적응 스트리밍                                                                                                                                                                                             |
| Smooth            | 부드러운 스트리밍 프로토콜                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG-2 전송 스트림                                                                                                                                                                                                         |
| RTMP              | 실시간 멀티미디어 프로토콜                                                                                                                                                                                                    |
| uimsbf            | 부호 없는 정수, 가장 중요한 비트 우선                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 타이밍 메타데이터 수집
## <a name="21-rtmp-ingest"></a>2.1 RTMP 수집
RTMP는 RTMP 스트림에 포함된 AMF 큐 메시지로 보내는 타이밍 메타데이터 신호를 지원합니다. 큐 메시지는 실제 이벤트 또는 광고 스플라이스 삽입이 발생하기 전에 특정 시간 동안 보낼 수 있습니다. 이 시나리오를 지원하기 위해 스플라이스 또는 세그먼트의 실제 시간이 큐 메시지 내에 보내집니다. 자세한 내용은 [AMF0]을 참조하세요.

다음 표에서는 Media Services에서 수집하는 AMF 메시지 페이로드의 형식을 설명합니다.

AMF 메시지의 이름은 동일한 형식의 여러 이벤트 스트림을 구별하는 데 사용할 수 있습니다.  [SCTE-35] 메시지의 경우, AMF 메시지의 이름은 [SCTE-67]에서 권장한 대로 반드시 "onAdCue"여야 합니다.  아래에 나열되지 않은 모든 필드는 무시되어야 하므로 이후에는 이 디자인의 혁신이 금지되지 않습니다.

### <a name="signal-syntax"></a>신호 구문
RTMP 단순 모드의 경우 Media Services는 다음 형식의 "onAdCue"라는 단일 AMF 큐 메시지를 지원합니다.

### <a name="simple-mode"></a>단순 모드

| 필드 이름 | 필드 형식 | Required? | 설명                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | String     | 필수 | 이벤트 메시지입니다.  단순 모드 스플라이스를 지정하려면 "SpliceOut"이어야 합니다.                                              |
| id         | String     | 필수 | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.                            |
| duration   | Number     | 필수 | 스플라이스의 재생 시간입니다. 단위는 소수 자릿수 초입니다.                                                                |
| elapsed    | Number     | 옵션 | 선국(tune in)을 지원하기 위해 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. 단순 모드를 사용하는 경우, 이 값은 스플라이스의 원래 재생 시간을 초과하지 않아야 합니다.                                                  |
| time       | Number     | 필수 | 프레젠테이션 시간에 있는 스플라이스의 시간이어야 합니다. 단위는 소수 자릿수 초입니다.                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35 모드

| 필드 이름 | 필드 형식 | Required? | 설명                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | String     | 필수 | 이벤트 메시지입니다.  [SCTE-35] 메시지의 경우, [SCTE-67]에 따라 메시지를 HLS, Smooth, Dash 클라이언트로 보내려면 이 메시지는 base64(IETF RFC 4648) 이진 인코딩된 splice_info_section()이어야 합니다.                                              |
| 형식       | String     | 필수 | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우, [SCTE-67]에 따라 메시지를 HLS, Smooth, Dash 클라이언트로 보내려면 이 메시지는 "urn:scte:scte35:2013a:bin"이어야 합니다.  |
| id         | String     | 필수 | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.  동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다.|
| duration   | Number     | 필수 | 알려진 경우 이벤트 또는 광고 스플라이스 세그먼트의 재생 시간입니다. 알 수 없는 경우 값은 0이어야 합니다.                                                                 |
| elapsed    | Number     | 옵션 | 선국(tune in)하기 위해 [SCTE-35] 광고 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. [SCTE-35] 모드에서 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다.                                                  |
| time       | Number     | 필수 | 이벤트 또는 광고 스플라이스의 프레젠테이션 시간입니다.  프레젠테이션 시간과 재생 시간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 유형 2의 SAP(스트림 액세스 포인트)와 일치해야 합니다. HLS 송신의 경우, 시간과 재생 시간은 세그먼트 경계와 일치해야 합니다. 동일한 이벤트 스트림 내의 다른 이벤트 메시지에 대한 프레젠테이션 시간과 재생 시간은 겹치지 않아야 합니다. 단위는 소수 자릿수 초입니다.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 취소 및 업데이트

동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다. 프레젠테이션 시간과 ID는 이벤트를 고유하게 식별하며, 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 이벤트는 이전에 받은 메시지를 대체합니다. 미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 조각난 MP4 수집(부드러운 스트리밍)
라이브 스트림 수집에 대한 요구 사항은 [LIVE-FMP4]를 참조하세요. 다음 섹션에서는 타이밍 프레젠테이션 메타데이터의 수집에 대한 세부 정보를 제공합니다.  타이밍 프레젠테이션 메타데이터는 라이브 서버 매니페스트 상자(MS-SSTR 참조)와 동영상 상자('moov') 모두에 정의된 스파스 트랙으로 수집됩니다.  각 스파스 조각은 동영상 조각 상자('moof')와 미디어 데이터 상자('mdat')로 구성되며, 'mdat' 상자는 이진 메시지입니다.

#### <a name="221-live-server-manifest-box"></a>2.2.1 라이브 서버 매니페스트 상자
스파스 트랙은 \<textstream\>(텍스트 스트림) 항목이 있는 라이브 서버 매니페스트 상자에 선언되어야 하며, 다음과 같은 특성이 설정되어 있어야 합니다.

| **특성 이름** | **필드 형식** | **필수 여부** | **설명**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | 필수      | "0"이어야 하며, 알 수 없는 가변 비트 전송률이 있는 트랙을 나타냅니다.                                                                                                                                                                                                 |
| parentTrackName    | String         | 필수      | 스파스 트랙 타임 코드가 날짜 표시줄에 맞춰진 부모 트랙의 이름이어야 합니다. 부모 트랙은 스파스 트랙이 될 수 없습니다.                                                                                                                    |
| manifestOutput     | Boolean        | 필수      | 스파스 트랙이 Smooth 클라이언트 매니페스트에 포함된다는 것을 나타내려면 "true"여야 합니다.                                                                                                                                                               |
| Subtype            | String         | 필수      | 네 문자 코드인 "DATA"여야 합니다.                                                                                                                                                                                                                         |
| 구성표             | String         | 필수      | 메시지 구성표를 식별하는 URN 또는 URL이어야 합니다. [SCTE-35] 메시지의 경우, [SCTE-67]에 따라 메시지를 HLS, Smooth, Dash 클라이언트로 보내려면 이 메시지는 "urn:scte:scte35:2013a:bin"이어야 합니다. |
| trackName          | String         | 필수      | 스파스 트랙의 이름이어야 합니다. trackName은 동일한 구성표를 사용하여 여러 이벤트 스트림을 구별하는 데 사용할 수 있습니다. 각각의 고유 이벤트 스트림에는 고유한 트랙 이름이 있어야 합니다.                                                                           |
| timescale          | Number         | 옵션      | 부모 트랙의 날짜 표시줄이어야 합니다.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 동영상 상자

동영상 상자('moov')는 라이브 서버 매니페스트 상자의 뒤를 이어 스파스 트랙에 대한 스트림 헤더의 일부가 됩니다.

'moov' 상자에는 [ISO-14496-12]에서 정의한 대로 다음 제약 조건이 있는 **TrackHeaderBox('tkhd')** 상자가 포함되어야 합니다.

| **필드 이름** | **필드 형식**          | **필수 여부** | **설명**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 부호 없는 64비트 정수 | 필수      | 트랙 상자에 샘플이 없고 트랙 상자에 있는 샘플의 총 재생 시간이 0이므로 0이어야 합니다. |

-------------------------------------

'moov' 상자에는 [ISO-14496-12]에서 정의한 대로 다음 제약 조건이 있는 **HandlerBox(‘hdlr’)** 상자가 포함되어야 합니다.

| **필드 이름** | **필드 형식**          | **필수 여부** | **설명**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 부호 없는 32비트 정수 | 필수      | 'meta'여야 합니다. |

-------------------------------------

'stsd' 상자에는 [ISO-14496-12]에서 정의한 대로 코딩 이름이 있는 MetaDataSampleEntry 상자가 포함되어야 합니다.  예를 들어 SCTE-35 메시지의 경우 코딩 이름은 'scte'여야 합니다.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 동영상 조각 상자 및 미디어 데이터 상자

스파스 트랙 조각은 동영상 조각 상자(‘moof’) 및 미디어 데이터 상자('mdat')로 구성됩니다.

MovieFragmentBox('moof') 상자에는 [MS-SSTR]에서 정의한 대로 다음 필드가 있는 **TrackFragmentExtendedHeaderBox('uuid')** 상자가 포함되어야 합니다.

| **필드 이름**         | **필드 형식**          | **필수 여부** | **설명**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 부호 없는 64비트 정수 | 필수      | 이벤트의 도착 시간이어야 합니다. 이 값은 메시지를 부모 트랙과 맞춥니다.   |
| fragment_duration      | 부호 없는 64비트 정수 | 필수      | 이벤트의 재생 시간이어야 합니다. 재생 시간을 알 수 없다는 것을 나타내기 위해 재생 시간이 0이 될 수 있습니다. |

------------------------------------


MediaDataBox('mdat') 상자의 형식은 다음과 같아야 합니다.

| **필드 이름**          | **필드 형식**                   | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 버전                 | 부호 없는 32비트 정수(uimsbf) | 필수      | 'mdat' 상자의 콘텐츠 형식을 결정합니다. 인식할 수 없는 버전은 무시됩니다. 현재 지원되는 유일한 버전은 1입니다.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 부호 없는 32비트 정수(uimsbf) | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 즉, ID가 동일한 이벤트 메시지 상자 하나만 처리하면 됩니다.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 부호 없는 32비트 정수(uimsbf) | 필수      | TrackFragmentExtendedHeaderBox에 지정된 fragment_absolute_time과 presentation_time_delta의 합이 이벤트의 프레젠테이션 시간이어야 합니다. 프레젠테이션 시간과 재생 시간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 유형 2의 SAP(스트림 액세스 포인트)와 일치해야 합니다. HLS 송신의 경우, 시간과 재생 시간은 세그먼트 경계와 일치해야 합니다. 동일한 이벤트 스트림 내의 다른 이벤트 메시지에 대한 프레젠테이션 시간과 재생 시간은 겹치지 않아야 합니다. |
| Message                 | 바이트 배열                       | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우, 메시지는 이진 splice_info_section()이지만 [SCTE-67]에서는 다른 형식을 권장하고 있습니다. [SCTE-35] 메시지의 경우, [SCTE-67]에 따라 메시지를 HLS, Smooth, Dash 클라이언트로 보내려면 이 메시지는 splice_info_section()이어야 합니다. [SCTE-35] 메시지의 경우, 이진 splice_info_section()은 'mdat' 상자의 페이로드이며 base64로 인코딩되지 않습니다.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 취소 및 업데이트
동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다.  프레젠테이션 시간과 ID는 이벤트를 고유하게 식별합니다. 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 메시지는 이전에 받은 메시지를 대체합니다.  미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다. 


## <a name="3-timed-metadata-delivery"></a>3 타이밍 메타데이터 배달

이벤트 스트림 데이터는 Media Services에 불투명합니다. Media Services는 수집 엔드포인트와 클라이언트 엔드포인트 간에 세 가지 정보만 전달합니다. [SCTE-67] 및/또는 클라이언트의 스트리밍 프로토콜에 따라 클라이언트에 배달되는 속성은 다음과 같습니다.

1.  구성표 - 메시지 구성표를 식별하는 URN 또는 URL입니다.

2.  프레젠테이션 시간 - 미디어 타임라인의 이벤트에 대한 프레젠테이션 시간입니다.

3.  재생 시간 - 이벤트의 재생 시간입니다.

4.  ID – 이벤트에 대한 선택적 고유 식별자입니다.

5.  메시지 - 이벤트 데이터입니다.


## <a name="31-smooth-streaming-delivery"></a>3.1 부드러운 스트리밍 배달

[MS-SSTR]에서 스파스 트랙 처리 세부 정보를 참조하세요.

#### <a name="smooth-client-manifest-example"></a>Smooth 클라이언트 매니페스트 예제
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS 배달
Apple HLS(HTTP 라이브 스트리밍)에 대한 타이밍 메타데이터는 사용자 지정 M3U 태그 내의 세그먼트 재생 목록에 포함될 수 있습니다.  애플리케이션 계층에서는 M3U 재생 목록을 구문 분석하고 M3U 태그를 처리할 수 있습니다. Azure Media Services는 [HLS]에 정의된 EXT-X-CUE 태그에 타이밍 메타데이터를 포함합니다.  EXT-X-CUE 태그는 현재 ADI3 형식의 메시지에 대한 DynaMux에서 사용됩니다.  SCTE-35 및 비SCTE-35 메시지를 지원하려면 EXT-X-CUE 태그의 특성을 아래에서 정의한 대로 설정합니다.

| **특성 이름** | **형식**                      | **필수 여부**                             | **설명**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | 따옴표가 붙은 문자열                 | 필수                                  | [IETF RFC 4648](https://tools.ietf.org/html/rfc4648)에서 설명한 대로 base64 문자열로 인코딩된 메시지입니다. [SCTE-35] 메시지의 경우 base64로 인코딩된 splice_info_section()입니다.                                                                                                |
| 형식               | 따옴표가 붙은 문자열                 | 필수                                  | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우 이 특성은 특수 값인 "scte35"를 사용합니다.                                                                                                                                |
| ID                 | 따옴표가 붙은 문자열                 | 필수                                  | 이벤트에 대한 고유 식별자입니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                                                                          |
| DURATION           | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 재생 시간입니다. 알 수 없는 경우 값은 0이어야 합니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                           |
| ELAPSED            | 10진수 부동 소수점 숫자 | 선택 사항이지만, 슬라이딩 시간 범위에 필수임 | 슬라이딩 프레젠테이션 시간 범위를 지원하기 위해 신호가 반복되는 경우. 이 필드는 이벤트가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다. |
| TIME               | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 프레젠테이션 시간입니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                                    |


HLS 플레이어 애플리케이션 계층에서는 TYPE을 사용하여 메시지 형식을 식별하고, 메시지를 디코딩하며, 필요한 시간 변환을 적용하고, 이벤트를 처리합니다.  이벤트는 이벤트 타임스탬프에 따라 부모 트랙의 세그먼트 재생 목록에서 시간 동기화됩니다.  이러한 이벤트는 가장 가까운 세그먼트(#EXTINF 태그) 앞에 삽입됩니다.

#### <a name="hls-segment-playlist-example"></a>HLS 세그먼트 재생 목록 예제
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>HLS 메시지 처리

이벤트는 각 비디오 및 오디오 트랙의 세그먼트 재생 목록에서 신호를 받습니다. EXT-X-CUE 태그의 위치는 [HLS]에서 요구하는 대로 항상 첫 번째 HLS 세그먼트(스플라이스 끝 또는 세그먼트 시작) 바로 앞 또는 TIME 및 DURATION 특성이 참조하는 마지막 HLS 세그먼트(스플라이스 시작 또는 세그먼트 끝) 바로 뒤에 있어야 합니다.

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, [HLS]에서 요구하는 대로 EXT-X-CUE 태그가 항상 세그먼트 재생 목록에서 스플라이스 또는 세그먼트를 완전히 설명할 수 있을 만큼 자주 반복되어야 하며, 스플라이스 또는 세그먼트가 활성 상태인 시간을 나타내기 위해 ELAPSED 특성을 사용해야 합니다.

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 EXT-X-CUE 태그가 세그먼트 재생 목록에서 제거됩니다.

## <a name="33--dash-delivery"></a>3.3 DASH 배달
[DASH]는 이벤트 신호를 받는 세 가지 방법을 제공합니다.

1.  MPD에서 신호를 받는 이벤트
2.  표현에서 대역 내 신호를 받는 이벤트(이벤트 메시지 상자('emsg') 사용)
3.  1과 2의 조합

MPD에서 신호를 받는 이벤트는 MPD를 다운로드하는 즉시 클라이언트에서 모든 이벤트에 액세스할 수 있으므로 VOD 스트리밍에 유용합니다. 대역 내 솔루션은 클라이언트에서 MPD를 다시 다운로드할 필요가 없으므로 라이브 스트리밍에 유용합니다. 시간 기반 구분의 경우 클라이언트는 현재 세그먼트의 재생 시간 및 타임스탬프를 추가하여 다음 세그먼트에 대한 URL을 결정합니다. 요청이 실패하면 클라이언트에서 불연속성을 가정하고 MPD를 다운로드하지만, 그렇지 않으면 MPD를 다운로드하지 않고 스트리밍을 계속합니다.

Azure Media Services는 이벤트 메시지 상자를 사용하여 MPD 신호와 대역 내 신호를 모두 처리합니다.

#### <a name="mpd-signaling"></a>MPD 신호

이벤트는 Period 요소 내에 나타나는 EventStream 요소를 사용하여 MPD에서 신호를 받습니다.

EventStream 요소에 포함되는 특성은 다음과 같습니다.

| **특성 이름** | **형식**                | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 이 값은 메시지 구성표를 식별하는 URN 또는 URL이어야 합니다(예: “urn:scte:scte35:2013a:bin”).                                                                |
| 값              | 문자열                  | 옵션      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 구성표를 사용하여 여러 이벤트 스트림을 구별하기 위해 값을 이벤트 스트림의 이름(부드러운 수집의 경우 trackName 또는 RTMP 수집의 경우 AMF 메시지 이름)으로 설정해야 합니다. |
| 시간 간격          | 부호 없는 32비트 정수 | 필수      | 'emsg' 상자 내의 시간 및 재생 시간 필드의 날짜 표시줄(초당 틱 수 단위)입니다.                                                                                                                                                                                                       |


0개 이상의 Event 요소가 EventStream 요소 내에 포함되며, 포함되는 특성은 다음과 같습니다.

| **특성 이름**  | **형식**                | **필수 여부** | **설명**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 부호 없는 64비트 정수 | 옵션      | Period 요소의 시작과 관련된 이벤트의 미디어 프레젠테이션 시간이어야 합니다. 프레젠테이션 시간과 재생 시간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 유형 2의 SAP(스트림 액세스 포인트)와 일치해야 합니다. |
| duration            | 부호 없는 32비트 정수 | 옵션      | 이벤트의 재생 시간입니다. 재생 시간을 알 수 없는 경우 이 특성은 생략해야 합니다.                                                                                                                                                 |
| id                  | 부호 없는 32비트 정수 | 옵션      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.             |
| 이벤트 요소 값 | 문자열                  | 필수      | [IETF RFC 4648](https://tools.ietf.org/html/rfc4648)에서 설명한 대로 base64 문자열로 된 이벤트 메시지입니다.                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>DASH 매니페스트(MPD) 신호에 대한 XML 구문 및 예제

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>presentationTime은 메시지의 도착 시간이 아니라 이벤트의 프레젠테이션 시간입니다.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 대역 내 이벤트 메시지 상자 신호
대역 내 이벤트 스트림을 사용하려면 MPD에 적응 설정 수준의 InbandEventStream 요소가 있어야 합니다.  이 요소는 필수 schemeIdUri 특성 및 선택적 timescale 특성을 가지고 있으며 이벤트 메시지 상자('emsg')에도 나타납니다.  MPD에 정의되지 않은 구성표 식별자가 있는 이벤트 메시지 상자가 없어야 합니다. DASH 클라이언트에서 MPD에 정의되지 않은 구성표가 있는 이벤트 메시지 상자를 검색하면 클라이언트는 이를 무시해야 합니다.
이벤트 메시지 상자('emsg')는 미디어 프레젠테이션 시간과 관련된 제네릭 이벤트에 대한 신호를 제공합니다. 'emsg' 상자가 있는 경우 'moof' 상자 앞에 배치해야 합니다.

### <a name="dash-event-message-box-emsg"></a>'emsg' DASH 이벤트 메시지 상자
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

DASHEventMessageBox의 필드는 아래와 같이 정의됩니다.

| **필드 이름**          | **필드 형식**          | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 이 값은 메시지 구성표를 식별하는 URN 또는 URL이어야 합니다. [SCTE-35] 메시지의 경우, "urn:scte:scte35:2013a:bin"이라는 특수 값이 필요하지만, [SCTE-67]에서는 다른 값을 권장하고 있습니다. |
| 값                   | 문자열                  | 필수      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 구성표를 사용하여 여러 이벤트 스트림을 구별하기 위해 값을 이벤트 스트림의 이름(부드러운 수집의 경우 trackName 또는 RTMP 수집의 경우 AMF 메시지 이름)으로 설정합니다.                                                                  |
| 시간 간격               | 부호 없는 32비트 정수 | 필수      | 'emsg' 상자 내의 시간 및 재생 시간 필드의 날짜 표시줄(초당 틱 수 단위)입니다.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 부호 없는 32비트 정수 | 필수      | 이벤트의 프레젠테이션 시간에 대한 미디어 프레젠테이션 시간 델타 및 이 세그먼트에서 가장 빠른 프레젠테이션 시간입니다. 프레젠테이션 시간과 재생 시간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 유형 2의 SAP(스트림 액세스 포인트)와 일치해야 합니다.                                                                                            |
| event_duration          | 부호 없는 32비트 정수 | 필수      | 이벤트의 재생 시간이거나 알 수 없는 재생 시간을 나타내는 0xFFFFFFFF입니다.                                                                                                                                                                                                                                                                                          |
| Id                      | 부호 없는 32비트 정수 | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                                                                                    |
| Message_data            | 바이트 배열              | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우, 메시지 데이터는 이진 splice_info_section()이지만 [SCTE-67]에서는 다른 형식을 권장하고 있습니다.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 DASH 메시지 처리

이벤트는 비디오 및 오디오 트랙 모두에 대해 'emsg' 상자 내의 대역 내에서 신호를 받습니다.  presentation_time_delta가 15초 이하인 모든 세그먼트 요청에 대해 신호가 발생합니다. 슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 이벤트 메시지의 시간과 재생 시간의 합이 매니페스트에 있는 미디어의 데이터 시간보다 작은 경우 이벤트 메시지가 MPD에서 제거됩니다.  즉, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 이벤트 메시지가 매니페스트에서 제거됩니다.

## <a name="4-scte-35-ingest"></a>4 SCTE-35 수집

[SCTE-35] 메시지는 부드러운 수집의 경우 **"urn:scte:scte35:2013a:bin"** 구성표, RTMP 수집의 경우 **"scte35"** 형식을 사용하여 이진 형식으로 수집됩니다. MPEG-2 전송 스트림 PTS(프레젠테이션 시간스탬프)를 기반으로 하는 [SCTE-35] 타이밍을 쉽게 변환할 수 있도록 하기 위해 PTS(splice_time()의 pts_time + pts_adjustment)와 미디어 타임라인 간의 매핑은 이벤트 프레젠테이션 시간(부드러운 수집의 fragment_absolute_time 필드 및 RTMP 수집의 시간 필드)에서 제공됩니다. 33비트 PTS 값이 대략 26.5시간마다 롤오버되기 때문에 매핑이 필요합니다.

부드러운 스트리밍 수집의 경우, 미디어 데이터 상자('mdat')가 [SCTE-35]의 표 8-1에서 정의한 **splice_info_section()** 을 포함해야 합니다. RTMP 수집의 경우, AMF 메시지의 cue 특성은 base64로 인코딩된 **splice_info_section()** 으로 설정됩니다. 메시지가 위에서 설명한 형식이면 [SCTE-67]에 따라 메시지가 HLS, Smooth 및 Dash 클라이언트로 전송됩니다.


## <a name="5-references"></a>5 참고 자료

**[SCTE-35]** ANSI/SCTE 35 2013a - 케이블용 디지털 프로그램 삽입 큐 메시지, 2013a

**[SCTE-67]** ANSI/SCTE 67 2014 –Recommended Practice for SCTE 35: Digital Program Insertion Cueing Message for Cable

**[DASH]** ISO/IEC 23009-1 2014 – Information technology – Dynamic adaptive streaming over HTTP (DASH) – Part 1: Media Presentation description and segment formats, 2nd edition

**[HLS]** ["HTTP 라이브 스트리밍", draft-pantos-http-live-streaming-14, 2014년 10월 14일](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** ["Microsoft 부드러운 스트리밍 프로토콜", 2014년 5월 15일](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** ["동작 메시지 형식 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]** [Azure Media Services 조각난 MP4 라이브 수집 사양](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Part 12 ISO base media file format, Fourth Edition 2012-07-15.

**[RTMP]** ["Adobe 실시간 메시징 프로토콜", 2012년 12월 21일](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로 보기.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
