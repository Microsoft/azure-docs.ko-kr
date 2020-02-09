---
title: 라이브 스트리밍의 Azure Media Services 신호 시간 제한 메타 데이터
description: 이 사양에서는 수집 및 streaming이 Azure Media Services 때 시간 지정 되는 메타 데이터 신호를 위한 메서드를 간략하게 설명 합니다. 여기에는 ad 삽입 및 스플라이스 조건 신호에 대 한 SCTE-35 신호 뿐만 아니라 일반 시간 제한 메타 데이터 신호 (ID3)도 지원 됩니다.
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
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 03b40dea4949bb50c30f7755b56294ac53107403
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905253"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>라이브 스트리밍의 신호 타이밍 메타데이터 

마지막 업데이트: 2019-08-22

### <a name="conformance-notation"></a>적합성 표기

이 문서의 키워드인 "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" 및 "OPTIONAL"은 RFC 2119에서 설명한 대로 해석되어야 합니다.

## <a name="1-introduction"></a>1. 소개 

클라이언트 플레이어에서 보급 알림 또는 사용자 지정 메타 데이터 이벤트를 삽입 하도록 신호를 보내기 위해 방송사은 종종 비디오 내에 포함 된 시간 제한 메타 데이터를 사용 합니다. 이러한 시나리오를 사용 하기 위해 Media Services 라이브 스트리밍 채널의 수집 지점에서 클라이언트 응용 프로그램으로 시간 제한 된 메타 데이터를 전송 하는 기능을 지원 합니다.
이 사양은 라이브 스트리밍 신호 내에서 시간이 지정 된 메타 데이터에 대 한 Media Services 지원 되는 몇 가지 모드를 간략하게 설명 합니다.

1. [Scte-35], [SCTE-214-1], [SCTE-214-3] 및 [RFC8216]에 설명 된 표준을 준수 하는 [SCTE-35] 신호

2. [SCTE-35] RTMP 광고 신호에 대 한 레거시 [Adobe Primetime] 사양을 따르는 신호입니다.
   
3. [Scte-35]이 **아니고** 응용 프로그램 개발자가 정의한 기타 사용자 지정 스키마를 사용할 수 있는 메시지에 대 한 일반적인 타이밍 메타 데이터 신호 모드입니다.

## <a name="11-terms-used"></a>1.1 용어 사용 됨

| 조건                | 정의                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 광고 중단            | 하나 이상의 광고를 배달 하도록 예약할 수 있는 위치 또는 시점 활용 가능 및 배치 기회와 동일 합니다.                                                                                                                     |
| Ad 의사 결정 서비스 | 사용자에 게 표시 되는 ad 및 기간을 결정 하는 외부 서비스입니다. 서비스는 일반적으로 파트너에 의해 제공 되며이 문서에 대 한 범위를 벗어났습니다.                                                                    |
| 암시                 | 예정 된 ad 중단의 시간 및 매개 변수를 나타냅니다. 큐는 ad 중단에 대 한 보류 중인 전환, ad 중단 내에서 다음 ad로의 보류 중인 스위치, 보류 중인 스위치를 광고에서 주 콘텐츠로 나타낼 수 있습니다.           |
| Packager            | "스트리밍 엔드포인트" Azure Media Services는 대시 및 HLS에 대 한 동적 패키징 기능을 제공 하며 미디어 업계에서 "패키지 작성" 이라고 합니다.                                                                              |
| 프레젠테이션 시간   | 이벤트가 뷰어에 제공되는 시간입니다. 시간은 뷰어에 이벤트가 표시되는 미디어 타임라인의 순간을 나타냅니다. 예를 들어 SCTE-35 splice_info() 명령 메시지의 프레젠테이션 시간은 splice_time()입니다. |
| 도착 시간        | 이벤트 메시지가 도착하는 시간입니다. 이벤트 메시지가 이벤트의 프레젠테이션 시간에 앞서 보내지므로 이 시간은 일반적으로 이벤트의 프레젠테이션 시간과 구분됩니다.                                                    |
| 스파스 트랙        | 연속적이지 않으며, 부모 트랙 또는 컨트롤 트랙과 시간 동기화되는 미디어 트랙입니다.                                                                                                                                                  |
| 원본              | Azure 미디어 스트리밍 서비스                                                                                                                                                                                                             |
| 채널 싱크        | Azure 미디어 라이브 스트리밍 서비스                                                                                                                                                                                                        |
| HLS                 | Apple HTTP 라이브 스트리밍 프로토콜                                                                                                                                                                                                            |
| DASH                | HTTP를 통한 동적 적응 스트리밍                                                                                                                                                                                                          |
| Smooth              | 부드러운 스트리밍 프로토콜                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG-2 전송 스트림                                                                                                                                                                                                                      |
| RTMP                | 실시간 멀티미디어 프로토콜                                                                                                                                                                                                                 |
| uimsbf              | 부호 없는 정수, 가장 중요한 비트 우선                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 표준 참조

다음 문서에는이 문서의 프로 비전을 구성 하는이 텍스트의 참조를 통해 제공 되는 프로 비전이 포함 되어 있습니다. 모든 문서는 표준 본문에 의해 수정 될 수 있으며, 독자는 아래 나열 된 최신 버전의 문서를 적용할 가능성을 조사 하는 것이 좋습니다. 또한 판독기는 최신 버전의 참조 된 문서를 Azure Media Services에 대해이 시간 제한 된 메타 데이터 사양과 호환 되지 않을 수 있음을 미리 알려 주는 것입니다.


| Standard          | 정의                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital Program 삽입 신호 사양 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-플래시]  | [FLASH ActionScript 언어 참조](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| AMF0            | ["작업 메시지 형식 AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [IOP]     | 대시 업계 포럼 Interop 지침 v 4.2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | HTTP 라이브 스트리밍에 대 한 시간 제한 메타 데이터- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [CMAF (Common Media Application Format)의 시간 제한 된 메타 데이터](https://aomediacodec.github.io/av1-id3/)                                                                                                        |
| [ID3v2]           | ID3 태그 버전 2.4.0 [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12:12 부 ISO 기본 미디어 파일 형식, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | 정보 기술-HTTP를 통한 동적 적응 스트리밍 (대시)-1 부: 미디어 프레젠테이션 설명 및 세그먼트 형식 2014 년 5 월 게시할지. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | 정보 기술--멀티미디어 응용 프로그램 형식 (MPEG)--19 부: 분할 된 미디어에 대 한 CMAF (Common media application format)입니다. 2018 년 1 월 게시할지. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | 정보 기술-MPEG systems 기술--7 부: ISO 기본 미디어 파일 형식 파일의 일반적인 암호화 2 월 2016입니다. 게시할지. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft 부드러운 스트리밍 프로토콜", 2014 년 5 월 15 일](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-수집]  | [Azure Media Services 조각화된 MP4 라이브 수집 사양](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. ...; 5. HTTP 라이브 스트리밍. 8 월 2017. 정보 제공용 메시지입니다. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Base16, Base32 및 Base64 데이터 인코딩- [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Adobe의 실시간 메시징 프로토콜", 2012 년 12 월 21 일](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-케이블로 https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf 디지털 프로그램 삽입 케이블용 메시지                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – IP 기반 케이블 서비스의 MPEG 파선 1: MPD 제약 조건 및 확장                                                                                                                 |
| [SCTE-214-3]      | IP 기반 케이블 서비스에 대 한 SCTE 214-3 2015 MPEG 쇄선 3 부: 대시/FF 프로필                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – 이벤트 일정 예약 및 알림 인터페이스                                                                                                                                                  |
| [SCTE-250]        | ESAM (이벤트 및 신호 관리 API)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. 시간 제한 메타 데이터 수집

Azure Media Services는 [RTMP] 및 부드러운 스트리밍 [MS SSTR] 프로토콜에 대 한 실시간 대역 내 메타 데이터를 지원 합니다. 실시간 메타 데이터는 고유한 사용자 지정 스키마 (JSON, 이진, XML) 뿐만 아니라 고유한 사용자 지정 스키마 (JSON, 이진, XML)를 사용 하 여 사용자 지정 이벤트를 정의 하는 데 사용 될 수 있으며, 브로드캐스트 스트림의 광고 신호에 대해 ID3 또는 SCTE-35과 같은 업계 정의 형식 

이 문서에서는 Azure Media Services 지원 되는 수집 프로토콜을 사용 하 여 사용자 지정 시간 제한 메타 데이터 신호를 보내는 방법에 대해 자세히 설명 합니다. 또한 HLS, 대시 및 부드러운 스트리밍에 대 한 매니페스트를 시간 지정 된 메타 데이터 신호로 데코레이팅하는 방법 뿐만 아니라 HLS를 위한 CMAF (MP4 조각) 또는 TS (Transport Stream) 세그먼트를 사용 하 여 콘텐츠를 전달할 때 대역 내에 전달 되는 방법에 대해서도 설명 합니다. 

시간이 지정 되는 메타 데이터에 대 한 일반적인 사용 사례 시나리오는 다음과 같습니다.

 - 라이브 이벤트 또는 선형 브로드캐스트에서 광고 중단을 트리거하는 SCTE-35 ad 신호
 - 클라이언트 응용 프로그램에서 이벤트를 트리거할 수 있는 사용자 지정 ID3 메타 데이터 (브라우저, iOS 또는 Android)
 - 클라이언트 응용 프로그램에서 이벤트를 트리거하기 위해 정의 된 사용자 지정 JSON, 이진 또는 XML 메타 데이터
 - 라이브 인코더, IP 카메라 또는 Drone에서 원격 분석
 - 동작, 얼굴 감지 등과 같은 IP 카메라의 이벤트
 - 작업 카메라, 드 론 또는 장치 이동의 지리적 위치 정보
 - 노래 가사
 - 선형 라이브 피드의 프로그램 경계
 - 라이브 피드에 표시할 이미지 또는 보강 된 메타 데이터
 - 스포츠 점수 또는 게임 클록 정보
 - 브라우저에서 비디오와 함께 표시 될 대화형 광고 패키지
 - 퀴즈 또는 설문 조사
  
Azure Media Services 라이브 이벤트 및 패키지 작성 도구는 이러한 시간이 지정 된 메타 데이터 신호를 수신 하 여 HLS 및 대시 같은 표준 기반 프로토콜을 사용 하 여 클라이언트 응용 프로그램에 연결할 수 있는 메타 데이터 스트림으로 변환할 수 있습니다.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP 시간 제한 메타 데이터

[RTMP] 프로토콜을 사용 하면 사용자 지정 메타 데이터 및 SCTE-35 ad 신호를 비롯 한 다양 한 시나리오에 대해 시간 지정 된 메타 데이터 신호를 보낼 수 있습니다. 

광고 신호 (큐 메시지)는 [RTMP] 스트림 내에 포함 된 [AMF0] 큐 메시지로 전송 됩니다. 실제 이벤트 또는 [SCTE35] ad 스플라이스 신호를 발생 시키려면 먼저 큐 메시지를 보낼 수 있습니다. 이 시나리오를 지원 하기 위해 이벤트의 실제 프레젠테이션 타임 스탬프가 큐 메시지 내에 전송 됩니다. 자세한 내용은 [AMF0]을 참조하세요.

다음 [AMF0] 명령은 RTMP 수집에 대해 Azure Media Services에서 지원 됩니다.

- **Onuserdataevent** -사용자 지정 메타 데이터 또는 [ID3v2] 타이밍 메타 데이터에 사용 됩니다.
- **Onadcue** -라이브 스트림의 광고 배치 기회에 주로 신호를 보내는 데 사용 됩니다. 간단한 모드와 "SCTE-35" 모드의 두 가지 형태의 큐가 지원 됩니다. 
- **onCuePoint** -[SCTE35] 메시지에 신호를 보내기 위해 정령 Live encoder와 같은 특정 온-프레미스 하드웨어 인코더에서 지원 됩니다. 
  

다음 표에서는 Media Services "simple" 및 [SCTE35] 메시지 모드 모두를 수집 하는 AMF 메시지 페이로드의 형식을 설명 합니다.

[AMF0] 메시지의 이름은 동일한 유형의 여러 이벤트 스트림을 구별 하는 데 사용할 수 있습니다.  [SCTE-35] 메시지와 "simple" 모드 모두에서 AMF 메시지의 이름은 [Adobe-Primetime] 사양에 필요한 대로 "onAdCue" 여야 합니다.  아래에 나열 되지 않은 필드는 수집 시 Azure Media Services에서 무시 해야 합니다.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>"onUserDataEvent"를 사용 하는 사용자 지정 메타 데이터가 포함 된 2.1.1 RTMP

RTMP 프로토콜을 사용 하 여 업스트림 인코더, IP 카메라, 드 론 또는 장치에서 사용자 지정 메타 데이터 피드를 제공 하려면 "onUserDataEvent" [AMF0] 데이터 메시지 명령 유형을 사용 합니다.

**"Onuserdataevent"** 데이터 메시지 명령은 Media Services에서 캡처되고 HLS, 대시 및 부드러운 스트리밍에 대 한 매니페스트 뿐만 아니라 대역내 파일 형식으로 패키지 되는 다음 정의가 포함 된 메시지 페이로드를 전달 해야 합니다.
시간 제한 된 메타 데이터 메시지를 0.5 초 (500ms)에 한 번 이상 또는 라이브 스트림의 안정성 문제로 인해 전송 하는 것이 좋습니다. 프레임 수준 메타 데이터를 제공 해야 하는 경우 각 메시지는 여러 프레임의 메타 데이터를 집계할 수 있습니다. 다중 비트 전송률 스트림을 보내는 경우에는 대역폭을 줄이고 비디오/오디오 처리에 방해가 되지 않도록 단일 비트 전송률에 메타 데이터를 제공 하는 것이 좋습니다. 

**"Onuserdataevent"** 의 페이로드는 [MPEGDASH] EventStream XML 형식 메시지 여야 합니다. 이렇게 하면 HLS 또는 대시 프로토콜을 통해 제공 되는 CMAF [MPEGCMAF] 콘텐츠에 대 한 ' emsg ' 페이로드의 대역 내에서 수행할 수 있는 사용자 지정 정의 스키마를 쉽게 전달할 수 있습니다. 각 대시 이벤트 스트림 메시지는 URN 메시지 체계 식별자로 작동 하 고 메시지의 페이로드를 정의 하는 schemeIdUri를 포함 합니다. [ID3v2]에 대"https://aomedia.org/emsg/ID3"한 , 또는 **urn: scte: scte35:2013: bin 35의 urn: scte:: 2013: bin** 과 같은 일부 구성표는 상호 운용성을 위해 업계 consortia 표준화 됩니다. 모든 응용 프로그램 공급자는 사용자가 제어 하는 URL (소유 도메인)을 사용 하 여 고유한 사용자 지정 체계를 정의할 수 있으며, 선택 하는 경우 해당 URL에 대 한 사양을 제공할 수 있습니다. 플레이어에 정의 된 스키마에 대 한 처리기가 있는 경우이 구성 요소는 페이로드 및 프로토콜을 이해 해야 합니다.

[MPEG-2] EventStream XML 페이로드에 대 한 스키마는 (대시 ISO-IEC-23009-1-3 버전에서 발췌)로 정의 됩니다. 현재 "EventStream" 당 하나의 "EventType"만 지원 됩니다. **EventStream**에서 여러 이벤트를 제공 하는 경우 첫 번째 **이벤트** 요소만 처리 됩니다.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>ID3 스키마 ID 및 b a s e 64로 인코딩된 데이터 페이로드가 포함 된 예제 XML 이벤트 스트림입니다.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>사용자 지정 스키마 ID 및 b a s e 64로 인코딩된 이진 데이터를 사용 하는 예제 이벤트 스트림  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>사용자 지정 스키마 ID 및 사용자 지정 JSON을 사용 하는 예제 이벤트 스트림  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>기본 제공 지원 스키마 ID Uri
| 스키마 ID URI                 | Description                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | [ID3v2] 메타 데이터를 CMAF 호환 [MPEGCMAF] 조각화 된 MP4에서 timed metadata로 전달 하는 방법을 설명 합니다. 자세한 내용은 [CMAF (Common Media Application Format)의 시간 제한 된 메타 데이터](https://aomediacodec.github.io/av1-id3/) 를 참조 하세요. |

### <a name="event-processing-and-manifest-signaling"></a>이벤트 처리 및 매니페스트 신호

유효한 **"Onuserdataevent"** 이벤트가 수신 되 면 Azure Media Services에서 EventStreamType ([MPEGDASH]에 정의 됨)과 일치 하는 유효한 XML 페이로드를 검색 하 고,이 xml 페이로드를 구문 분석 하 여 라이브 보관 및 Media Services 패키지에 대 한 전송의 저장소에 대해 [MPEGCMAF] MP4 조각 ' emsg ' 버전 1 상자로 변환 합니다.   패키지 작성 도구는 라이브 스트림에서 ' emsg ' 상자를 검색 하 고 다음을 수행 합니다.

- (a) HLS 시간 제한 메타 데이터 사양 [HLS-TMD]을 준수 하 여 HLS 클라이언트에 배달 하기 위해 TS 세그먼트로 "동적 패키지"
- (b) HLS 또는 대시를 통해 CMAF 조각에서 배달을 위해 전달 하거나 
- (c) 부드러운 스트리밍 [MS SSTR]를 통해 배달할 스파스 트랙 신호로 변환 합니다.

HLS에 대 한 MPD (대역 외) ' emsg ' 형식 CMAF 또는 TS PE 패킷 외에도 대시 () 및 부드러운 스트리밍에 대 한 매니페스트는 대역 내 이벤트 스트림 (부드러운 스트리밍의 스파스 스트림 트랙 라고도 함)에 대 한 참조를 포함 합니다. 

개별 이벤트 또는 해당 데이터 페이로드는 HLS, 대시 또는 부드러운 매니페스트에 직접 출력 되지 않습니다. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>OnUserDataEvent 이벤트의 추가 정보 제약 조건 및 기본값

- EventStream 요소에 시간 간격을 설정 하지 않으면 기본적으로 RTMP 1 kHz 시간 표시줄이 사용 됩니다.
- OnUserDataEvent 메시지 배달은 500ms max 마다 한 번만 제한 됩니다. 자주 이벤트를 전송 하는 경우 라이브 피드의 대역폭과 안정성에 영향을 줄 수 있습니다.

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP ad 큐 신호를 "onAdCue"로

Azure Media Services는 라이브 스트림에서 여러 실시간 동기화 메타 데이터에 신호를 보내는 데 사용할 수 있는 여러 [AMF0] 메시지 유형을 수신 하 고 응답할 수 있습니다.  [Adobe-Primetime] 사양에서는 "simple" 및 "SCTE-35" 모드 라는 두 가지 큐 유형을 정의 합니다. "Simple" 모드의 경우, Media Services "Simple Mode" 신호에 대해 정의 된 아래 테이블과 일치 하는 페이로드를 사용 하 여 "onAdCue" 라는 단일 AMF 큐 메시지를 지원 합니다.  

다음 섹션에서는 HLS, 대시 및 Microsoft 부드러운 스트리밍의 클라이언트 매니페스트로 전달 되는 기본 "spliceOut" 광고 신호를 신호 하는 데 사용할 수 있는 RTMP "simple" mode "페이로드를 보여 줍니다. 이는 고객에 게 복잡 한 SCTE-35 기반 광고 신호 배포 나 삽입 시스템이 없고 API를 통해 기본 온-프레미스 인코더를 사용 하 여 큐 메시지에 보내는 시나리오에 매우 유용 합니다. 일반적으로 온-프레미스 인코더는이 신호를 트리거하기 위한 REST 기반 API를 지원 합니다 .이 신호는 IDR 프레임을 비디오에 삽입 하 고 새 GOP를 시작 하 여 비디오 스트림을 "스플라이스" 할 수도 있습니다.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP ad 큐 신호를 "onAdCue"-단순 모드

| 필드 이름 | 필드 형식 | 필수 여부 | 설명                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | 필수  | 이벤트 메시지입니다.  단순 모드 스플라이스를 지정하려면 "SpliceOut"이어야 합니다.                                                                                                                                                                                                         |
| id         | String     | 필수  | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.                                                                                                                                                                                       |
| duration   | 수     | 필수  | 스플라이스의 재생 시간입니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                                           |
| elapsed    | 수     | 선택 사항  | 선국(tune in)을 지원하기 위해 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. 단순 모드를 사용하는 경우, 이 값은 스플라이스의 원래 재생 시간을 초과하지 않아야 합니다. |
| time       | 수     | 필수  | 프레젠테이션 시간에 있는 스플라이스의 시간이어야 합니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP simple mode를 사용 하는 경우의 예제 MPEG 대시 매니페스트 출력

[Adobe simple mode를 사용 하는 Mpd EventStream 예제 3.3.2.1를](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode) 참조 하세요.

[단일 기간 및 Adobe simple 모드를 사용 하는 3.3.3.1 대시 매니페스트 예를](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals) 참조 하세요.

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Adobe RTMP simple 모드를 사용 하는 경우 HLS 매니페스트 출력 예제

[Adobe simple 모드 및 EXT-CUE 태그를 사용 하는 예제 3.2.2 HLS 매니페스트를](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy) 참조 하세요.

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP ad 큐 신호를 "onAdCue"-SCTE-35 모드로 전환

전체 SCTE-35 페이로드 메시지를 HLS 또는 대시 매니페스트로 전달 해야 하는 고급 브로드캐스트 프로덕션 워크플로를 사용 하는 경우 [Primetime] 사양의 "SCTE-35 모드"를 사용 하는 것이 가장 좋습니다.  이 모드는 온-프레미스 라이브 인코더에 직접 전송 되는 대역 내 SCTE-35 신호를 지원 합니다. 그러면이 신호는 [Adobe-Primetime] 사양에 지정 된 "SCTE-35 모드"를 사용 하 여 RTMP 스트림으로 신호를 인코딩합니다. 

일반적으로 SCTE-35 메시지는 온-프레미스 인코더의 MPEG-2 TS (전송 스트림) 입력에만 나타날 수 있습니다. SCTE-35을 포함 하는 전송 스트림 수집을 구성 하는 방법에 대 한 자세한 내용을 보려면 인코더 제조업체에 문의 하 고, Adobe SCTE-35 모드에서 RTMP로 통과 하도록 설정 하는 방법에 대 한 자세한 내용을 확인 하세요.

이 시나리오에서는 **"Onadcue"** [AMF0] 메시지 유형을 사용 하 여 온-프레미스 인코더에서 다음 페이로드를 전송 해야 합니다.

| 필드 이름 | 필드 형식 | 필수 여부 | 설명                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| cue        | String     | 필수  | 이벤트 메시지입니다.  [SCTE-35] 메시지의 경우 메시지를 HLS, Smooth 및 대시로 클라이언트에 보내려면이 메시지는 b a s e 64로 인코딩된 [RFC4648] 이진 splice_info_section () 이어야 합니다.                                                                                                                                                                                                                               |
| type       | String     | 필수  | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우 **"scte35"** 로 서 메시지를 HLS, Smooth 및 대시로 클라이언트에 전송 하려면 [Adobe-Primetime]를 준수 **해야** 합니다. 필요에 따라 URN "urn: scte: scte35:2013: bin"을 사용 하 여 [SCTE-35] 메시지에 신호를 보낼 수도 있습니다.                                                                                                        |
| id         | String     | 필수  | 스플라이스 또는 세그먼트를 설명하는 고유 식별자입니다. 메시지의 이 인스턴스를 식별합니다.  동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다.                                                                                                                                                                                                                                                       |
| duration   | 수     | 필수  | 알려진 경우 이벤트 또는 광고 스플라이스 세그먼트의 재생 시간입니다. 알 수 없는 **경우 값은 0 이어야 합니다.**                                                                                                                                                                                                                                                                                                                    |
| elapsed    | 수     | 선택 사항  | 선국(tune in)하기 위해 [SCTE-35] 광고 신호가 반복되는 경우. 이 필드는 스플라이스가 시작된 이후 경과한 프레젠테이션 시간의 양이어야 합니다. 단위는 소수 자릿수 초입니다. [SCTE-35] 모드에서 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다.                                                                                                                   |
| time       | 수     | 필수  | 이벤트 또는 광고 스플라이스의 프레젠테이션 시간입니다.  프레젠테이션 시간과 기간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 2의 SAP (스트림 액세스 점수)와 일치 **해야 합니다** . HLS 송신의 경우 시간과 기간이 세그먼트 경계와 일치 **해야** 합니다. 동일한 이벤트 스트림 내의 다른 이벤트 메시지에 대한 프레젠테이션 시간과 재생 시간은 겹치지 않아야 합니다. 단위는 소수 자릿수 초입니다. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>SCTE-35 모드가 포함 된 mpd manifest의 예
[3.3.3.2 예: SCTE를 사용 하 여 대시 매니페스트 섹션](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling) 을 참조 하세요.-35

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>M3u8-aapl-v3 예: SCTE-35 mode 신호를 사용 하는 예제 HLS
[3.2.1.1 예제 HLS manifest WITH SCTE-35 섹션을](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35) 참조 하세요.

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP Ad 신호를 "onCuePoint"로 사용

정령 Live 온-프레미스 인코더는 RTMP 신호의 광고 표식을 지원 합니다. 현재 Azure Media Services는 RTMP에 대 한 "onCuePoint" Ad 표식 유형만 지원 합니다.  이는 "**ad_markers**"을 "onCuePoint"로 설정 하 여 미디어 라이브 인코더 설정 또는 API의 Adobe RTMP 그룹 설정에서 사용 하도록 설정할 수 있습니다.  자세한 내용은 정령 Live 설명서를 참조 하세요. RTMP 그룹에서이 기능을 사용 하도록 설정 하면 Azure Media Services에서 처리할 Adobe RTMP 출력에 SCTE-35 신호를 전달 합니다.

"OnCuePoint" 메시지 유형은 [Adobe-Flash]에서 정의 되며, RTMP 출력에서 전송 될 때 다음과 같은 페이로드 구조가 있습니다.


| 속성   | Description                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | 이름은 '**scte35**'이 고,                                                                                                                                                                              |
| time       | 타임 라인 중 비디오 파일에서 큐 지점이 발생 하는 시간 (초)입니다.                                                                                                                                           |
| type       | 큐 지점의 유형은 "**event**"로 설정 해야 합니다.                                                                                                                                                                             |
| 매개 변수 | Id 및 기간을 포함 하 여 SCTE-35 메시지의 정보를 포함 하는 이름/값 쌍 문자열의 결합형 배열입니다. 이러한 값은 Azure Media Services에 의해 구문 분석 되 고 매니페스트 장식 태그에 포함 됩니다. |


이 모드의 ad 표식을 사용 하면 HLS 매니페스트 출력이 Adobe "Simple" 모드와 유사 합니다.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>MPEG 대시 MPD, 단일 기간, Adobe Simple mode 신호의 예

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>HLS 재생 목록 예, Adobe Simple mode는 EXT--CUE 태그를 사용 하 여 신호를 보냅니다 (잘림 "..."). 간단 하 게

다음 예에서는 Adobe "simple" 모드 신호 및 레거시 [Primetime] RTMP 태그를 사용 하 여 수집 스트림에 대 한 Media Services 동적 포장기의 출력을 보여 줍니다.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 취소 및 업데이트

동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다. 프레젠테이션 시간과 ID는 이벤트를 고유하게 식별하며, 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 이벤트는 이전에 받은 메시지를 대체합니다. 미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 조각난 MP4 수집(부드러운 스트리밍)

라이브 스트림 수집에 대 한 요구 사항은 [MS STR-수집]을 참조 하세요. 다음 섹션에서는 타이밍 프레젠테이션 메타데이터의 수집에 대한 세부 정보를 제공합니다.  타이밍 프레젠테이션 메타데이터는 라이브 서버 매니페스트 상자(MS-SSTR 참조)와 동영상 상자('moov') 모두에 정의된 스파스 트랙으로 수집됩니다.  

각 스파스 조각은 동영상 조각 상자('moof')와 미디어 데이터 상자('mdat')로 구성되며, 'mdat' 상자는 이진 메시지입니다.

프레임을 정확 하 게 표시 하기 위해 인코더는 큐를 삽입 해야 하는 프레젠테이션 시간에 조각을 분할 해야 합니다.  새로 만든 IDR 프레임으로 시작 하거나, [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 2의 스트림 액세스 요소 (SAP)로 시작 하는 새 조각을 만들어야 합니다. 이를 통해 Azure 미디어 패키지는 HLS 매니페스트와 새 기간이 프레임 정확한 스플라이스 조건 화 된 프레젠테이션 시간에서 시작 하는 대시 다중 기간 매니페스트를 제대로 생성할 수 있습니다.

### <a name="221-live-server-manifest-box"></a>2.2.1 라이브 서버 매니페스트 상자

**\<textstream\>** 항목을 사용 하 여 라이브 서버 매니페스트 상자에서 스파스 트랙을 선언 **해야** 하며, **이 항목** 에는 다음과 같은 특성 집합이 있어야 합니다.

| **특성 이름** | **필드 형식** | **필수 여부** | **설명**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | 수         | 필수      | "0" 이어야 **합니다** . 알 수 없는 가변 비트 전송률이 있는 트랙을 나타냅니다.                                                                                                                                                          |
| parentTrackName    | String         | 필수      | 는 스파스 트랙 시간 코드의 날짜/시간 정렬 기준이 되는 부모 트랙의 이름 이어야 **합니다** . 부모 트랙은 스파스 트랙이 될 수 없습니다.                                                                             |
| manifestOutput     | 부울        | 필수      | 스파스 트랙이 부드러운 클라이언트 매니페스트에 포함 될 것임을 나타내려면 "true" 여야 **합니다** .                                                                                                                        |
| Subtype            | String         | 필수      | 4 자 코드 "DATA **" 여야 합니다** .                                                                                                                                                                                  |
| 구성표             | String         | 필수      | 메시지 구성표를 식별 하는 URN 또는 **URL 이어야 합니다** . [SCTE-35] 메시지의 경우 "urn: scte: scte35:2013: bin"을 사용 하 여 메시지를 HLS, Smooth 및 파선 클라이언트에 게 전송 하려면 [SCTE-35]를 준수 **해야 합니다** . |
| trackName          | String         | 필수      | 은 스파스 트랙의 이름 **이어야 합니다.** 같은 체계를 사용 하 여 여러 이벤트 스트림을 구별할 수 있습니다. 고유한 각 이벤트 스트림에는 고유한 트랙 이름이 **있어야** 합니다.                                |
| timescale          | 수         | 선택 사항      | 부모 트랙의 시간 간격 **이어야** 합니다.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 동영상 상자

동영상 상자('moov')는 라이브 서버 매니페스트 상자의 뒤를 이어 스파스 트랙에 대한 스트림 헤더의 일부가 됩니다.

' Moov ' 상자에는 다음과 같은 제약 조건을 사용 하 여 [ISO-14496-12]에 정의 된 대로 기능 **(' tkhd ')** 상자가 포함 **되어야 합니다** .

| **필드 이름** | **필드 형식**          | **필수 여부** | **설명**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 부호 없는 64비트 정수 | 필수      | 트랙 상자에 샘플이 없고 트랙 상자에 있는 샘플의 총 기간이 0 이므로 **0 이어야 합니다** . |

---

' Moov ' 상자는 다음과 같은 제약 조건을 사용 하 여 [ISO-14496-12]에 정의 된 **handlerbox (' hdlr ')** 를 포함 **해야 합니다** .

| **필드 이름** | **필드 형식**          | **필수 여부** | **설명**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 부호 없는 32비트 정수 | 필수      | ' Meta **' 이어야 합니다** . |

---

' Stsd ' 상자에는 [ISO-14496-12]에 정의 된 대로 코딩 이름의 MetaDataSampleEntry 상자가 포함 **되어야** 합니다.  예를 들어 SCTE-35 메시지의 경우 코딩 이름은 ' scte **' 여야 합니다** .

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 동영상 조각 상자 및 미디어 데이터 상자

스파스 트랙 조각은 동영상 조각 상자(‘moof’) 및 미디어 데이터 상자('mdat')로 구성됩니다.

> [!NOTE]
> 프레임을 정확 하 게 표시 하기 위해 인코더는 큐를 삽입 해야 하는 프레젠테이션 시간에 조각을 분할 해야 합니다.  새로 만든 IDR 프레임으로 시작 하거나, [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 2의 스트림 액세스 요소 (SAP)로 시작 하는 새 조각을 만들어야 합니다.
> 

MovieFragmentBox (' moof ') 상자에는 다음 필드를 사용 하 여 [MS SSTR]에 정의 된 **TrackFragmentExtendedHeaderBox (' uuid ')** 상자가 포함 **되어야 합니다** .

| **필드 이름**         | **필드 형식**          | **필수 여부** | **설명**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 부호 없는 64비트 정수 | 필수      | 는 이벤트 도착 **시간 이어야 합니다** . 이 값은 메시지를 부모 트랙과 맞춥니다.           |
| fragment_duration      | 부호 없는 64비트 정수 | 필수      | 이벤트의 **기간 이어야 합니다** . 재생 시간을 알 수 없다는 것을 나타내기 위해 재생 시간이 0이 될 수 있습니다. |

---


MediaDataBox (' mdat ') 상자의 형식은 다음과 **같아야 합니다** .

| **필드 이름**          | **필드 형식**                   | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전                 | 부호 없는 32비트 정수(uimsbf) | 필수      | 'mdat' 상자의 콘텐츠 형식을 결정합니다. 인식할 수 없는 버전은 무시됩니다. 현재 지원되는 유일한 버전은 1입니다.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 부호 없는 32비트 정수(uimsbf) | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 즉, ID가 동일한 이벤트 메시지 상자 하나만 처리하면 됩니다.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 부호 없는 32비트 정수(uimsbf) | 필수      | TrackFragmentExtendedHeaderBox에 지정 된 fragment_absolute_time의 합계와 presentation_time_delta는 이벤트의 프레젠테이션 시간 **이어야** 합니다. 프레젠테이션 시간과 기간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 2의 SAP (스트림 액세스 점수)와 일치 **해야 합니다** . HLS 송신의 경우 시간과 기간이 세그먼트 경계와 일치 **해야** 합니다. 동일한 이벤트 스트림 내의 서로 다른 이벤트 메시지의 프레젠테이션 시간과 기간은 겹치지 **않아야** 합니다. |
| message                 | 바이트 배열                       | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우 메시지는 이진 splice_info_section ()입니다. [SCTE-35] 메시지의 경우 [SCTE-35]를 준수 하 여 메시지를 HLS, Smooth 및 대시 클라이언트에 보내려면이 splice_info_section () 이어야 **합니다** . [SCTE-35] 메시지의 경우 이진 splice_info_section ()는 ' mdat ' 상자의 페이로드 이며 b a s e 64로 인코딩되지 **않습니다** .                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 취소 및 업데이트

동일한 프레젠테이션 시간과 ID를 사용하여 여러 개의 메시지를 보내면 메시지를 취소하거나 업데이트할 수 있습니다.  프레젠테이션 시간과 ID는 이벤트를 고유하게 식별합니다. 미리 받기 제약 조건을 충족하는 특정 프레젠테이션 시간 동안 받은 마지막 메시지는 처리되는 메시지입니다. 업데이트된 메시지는 이전에 받은 메시지를 대체합니다.  미리 받기 제약 조건은 4초입니다. 프레젠테이션 시간보다 최소 4초 전에 받은 메시지가 처리됩니다. 


## <a name="3-timed-metadata-delivery"></a>3 타이밍 메타데이터 배달

이벤트 스트림 데이터는 Media Services에 불투명합니다. Media Services는 수집 엔드포인트와 클라이언트 엔드포인트 간에 세 가지 정보만 전달합니다. 다음 속성은 [SCTE-35] 및/또는 클라이언트의 스트리밍 프로토콜을 준수 하 여 클라이언트에 전달 됩니다.

1.  구성표 - 메시지 구성표를 식별하는 URN 또는 URL입니다.
2.  프레젠테이션 시간 - 미디어 타임라인의 이벤트에 대한 프레젠테이션 시간입니다.
3.  재생 시간 - 이벤트의 재생 시간입니다.
4.  ID – 이벤트에 대한 선택적 고유 식별자입니다.
5.  메시지 - 이벤트 데이터입니다.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft 부드러운 스트리밍 매니페스트  

스파스 메시지 트랙의 형식을 지정 하는 방법에 대 한 자세한 내용은 스파스 트랙 처리 [MS SSTR]를 참조 하세요. [SCTE35] 메시지의 경우 부드러운 스트리밍는 b a s e 64로 인코딩된 splice_info_section ()를 스파스 조각으로 출력 합니다.
StreamIndex에는 "DATA"의 하위 형식이 **있어야** 하며 Customattributes에 Name = "Schema" 및 Value = "urn: scte: scte35:2013: bin"의 특성이 포함 **되어야** 합니다.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Base64 인코딩 [SCTE35] splice_info_section ()를 보여 주는 부드러운 클라이언트 매니페스트 예제
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS 매니페스트 장식

Azure Media Services는 라이브 또는 요청 시 발생 하는 이벤트 중에 ad에서 사용할 수 있는 정보를 신호로 보내기 위해 다음과 같은 HLS 매니페스트 태그를 지원 합니다. 

- Apple HLS에 정의 된 EXT-X-DATERANGE [RFC8216]
- [Adobe-Primetime]에 정의 된 대로 EXT-큐는 "레거시"로 간주 됩니다. 가능 하면 고객이 DATERANGE 태그를 채택 해야 합니다.

각 태그에 대 한 데이터 출력은 사용 된 수집 신호 모드에 따라 달라 집니다. 예를 들어 Adobe Simple 모드로 RTMP 수집에는 전체 SCTE-35 base64 인코딩 페이로드가 포함 되지 않습니다.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1-DATERANGE를 사용 하 여 Apple HLS 사용 (권장)

Apple HTTP 라이브 스트리밍 [RFC8216] 지정에서는 [SCTE-35] 메시지에 대 한 신호를 사용할 수 있습니다. 메시지는 "SCTE-35을 EXT-DATERANGE에 매핑" 이라는 [RFC8216] 섹션의 DATERANGE 태그에 있는 세그먼트 재생 목록에 삽입 됩니다.  클라이언트 응용 프로그램 계층은 M3U 재생 목록을 구문 분석 하 고 M3U 태그를 처리 하거나 Apple 플레이어 프레임 워크를 통해 이벤트를 받을 수 있습니다.  

Azure Media Services (버전 3 API)에서 **권장** 되는 방법은 [RFC8216]를 따르고 매니페스트에서 [SCTE35] ad의 사용 가능한 데코레이션에 대 한 X_DATERANGE EXT 태그를 사용 하는 것입니다.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 m3u8-aapl-v3의 DATERANGE 신호 35를 보여 주는 예제 HLS.

Media Services 동적 패키지의 다음 예제 HLS 매니페스트 출력에서는 스트림의 SCTE-35 이벤트를 신호 하는 [RFC8216]에서 DATERANGE 태그를 사용 하는 방법을 보여 줍니다. 또한이 스트림에는 [Adobe-Primetime]에 대 한 "레거시" EXT-큐 태그가 포함 됩니다.

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>Adobe Primetime을 사용 하 여 Apple HLS 3.2.2 (레거시)

[Primetime] "SCTE-35 Mode"에 정의 된 대로 EXT-CUE 태그를 사용 하는 Azure Media Services (버전 2 및 3 API)에서 제공 하는 "레거시" 구현도 있습니다. 이 모드에서 Azure Media Services는 b a s e 64로 인코딩된 [SCTE-35] splice_info_section ()를 EXT-CUE 태그에 포함 합니다.  

"레거시" EXT--CUE 태그는 아래와 같이 정의 되며 [Adobe-Primetime] 사양에서 표준 참조할 수도 있습니다. 이는 필요한 경우 레거시 SCTE35 신호에만 사용 해야 합니다. 그렇지 않으면 권장 태그는 [RFC8216]에서 EXT-DATERANGE로 정의 됩니다. 

| **특성 이름** | **형식**                      | **필수 여부**                             | **설명**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CUE                | 따옴표가 붙은 문자열                 | 필수                                  | [RFC4648]에 설명 된 대로 base64 인코딩 문자열로 인코딩된 메시지입니다. [SCTE-35] 메시지의 경우 b a s e 64로 인코딩된 splice_info_section ()입니다.                                                                                                                                      |
| 유형               | 따옴표가 붙은 문자열                 | 필수                                  | 메시지 구성표를 식별하는 URN 또는 URL입니다. [SCTE-35] 메시지의 경우 이 특성은 특수 값인 "scte35"를 사용합니다.                                                                                                                                                                          |
| ID                 | 따옴표가 붙은 문자열                 | 필수                                  | 이벤트에 대한 고유 식별자입니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                                                                              |
| DURATION           | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 재생 시간입니다. 알 수 없는 **경우 값은 0 이어야 합니다.** 단위는 소수 자릿수 초입니다.                                                                                                                                                                                           |
| ELAPSED            | 10진수 부동 소수점 숫자 | 선택 사항이지만, 슬라이딩 시간 범위에 필수임 | 슬라이딩 프레젠테이션 창을 지원 하기 위해 신호가 반복 되는 경우이 필드는 이벤트가 시작 된 이후 경과한 프레젠테이션 시간의 양 **이어야 합니다** . 단위는 소수 자릿수 초입니다. 이 값은 스플라이스 또는 세그먼트의 지정된 원래 재생 시간을 초과할 수 있습니다. |
| TIME               | 10진수 부동 소수점 숫자 | 필수                                  | 이벤트의 프레젠테이션 시간입니다. 단위는 소수 자릿수 초입니다.                                                                                                                                                                                                                        |


HLS 플레이어 애플리케이션 계층에서는 TYPE을 사용하여 메시지 형식을 식별하고, 메시지를 디코딩하며, 필요한 시간 변환을 적용하고, 이벤트를 처리합니다.  이벤트는 이벤트 타임스탬프에 따라 부모 트랙의 세그먼트 재생 목록에서 시간 동기화됩니다.  이러한 이벤트는 가장 가까운 세그먼트(#EXTINF 태그) 앞에 삽입됩니다.

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 m3u8-aapl-v3 매니페스트 예제를 사용 하 여 "레거시" Adobe Primetime-X-큐

다음 예제에서는 Adobe Primetime 태그를 사용 하 여 HLS 매니페스트 장식을 보여 줍니다.  "CUE" 매개 변수는 TYPE 및 Duration 속성만 포함 하며,이는 Adobe "simple" 모드 신호를 사용 하는 RTMP 원본입니다.  이 항목이 SCTE-35 모드 신호가 면 태그에 [3.2.1.1 예제](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)에 표시 된 것 처럼 base64 인코딩된 이진 scte-35 페이로드가 포함 됩니다.

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>"레거시" Adobe Primetime EXT-3.2.4 HLS 메시지 처리

이벤트는 각 비디오 및 오디오 트랙의 세그먼트 재생 목록에서 신호를 받을 수 있습니다. EXT-CUE 태그의 위치는 항상 첫 HLS 세그먼트 바로 앞 (스플라이스 out 또는 세그먼트 시작의 경우) 또는 시간 및 기간 특성이 참조 하는 마지막 HLS 세그먼트 (splice의 경우) 바로 앞 ([Adobe-Primetime]에 **필요 함)** 입니다.

슬라이딩 프레젠테이션 창이 설정 된 경우, 확장-X-큐 태그는 splice 또는 세그먼트가 세그먼트 재생 목록에서 항상 완전히 설명 되는 만큼 자주 **반복 되어야 하며** , 경과 된 특성을 사용 하 여 splice 또는 세그먼트가 활성화 된 시간 ([Primetime]에 필요 함)을 나타내야 **합니다** .

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 EXT-X-CUE 태그가 세그먼트 재생 목록에서 제거됩니다.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 대시 매니페스트 장식 (MPD)

[MPEGDASH]는 이벤트 신호를 제공 하는 세 가지 방법을 제공 합니다.

1.  MPD EventStream에서 신호를 받은 이벤트
2.  이벤트 메시지 상자 (' emsg ')를 사용 하 여 대역내 신호를 받은 이벤트
3.  1과 2의 조합

MPD EventStream에서 신호를 받는 이벤트는 클라이언트가 모든 이벤트에 액세스할 수 있기 때문에 MPD를 다운로드 하는 즉시 VOD 스트리밍에 유용 합니다. SSAI 신호에도 유용 합니다 .이 경우 다운스트림 SSAI 공급 업체는 여러 기간 MPD 매니페스트에서 신호를 구문 분석 하 고 ad 콘텐츠를 동적으로 삽입 해야 합니다.  대역내 (' emsg ') 솔루션은 클라이언트가 MPD를 다시 다운로드할 필요가 없는 라이브 스트리밍 또는 클라이언트와 원본 간에 SSAI 매니페스트 조작이 발생 하지 않는 라이브 스트리밍에 유용 합니다. 

대시의 기본 동작은 이벤트 메시지 상자 (' emsg ')를 사용 하 여 MPD EventStream와 대역내 모두에 신호를 보내는 것입니다. Azure Media Services

[RTMP] 또는 [수집]에 대 한 큐 메시지는 대역 외 ' emsg ' 상자 및/또는 MPD EventStreams을 사용 하 여 대시 이벤트에 매핑됩니다. 

대역 내 SCTE-35 신호는 [SCTE-214-3]에 정의 된 정의 및 요구 사항과 [IOP] 섹션 13.12.2 (' SCTE35 Events ')에도 적용 됩니다. 

대역내 [SCTE-35] 캐리지의 경우 이벤트 메시지 상자 (' emsg ')는 schemeId = "urn: scte: scte35:2013: bin"을 사용 합니다. MPD 매니페스트 장식의 경우 EventStream schemeId는 "urn: scte: scte35:2014: xml + bin"을 사용 합니다.  이 형식은 수집 시 도착 한 전체 SCTE-35 메시지의 base64로 인코딩된 이진 출력을 포함 하는 이벤트의 XML 표현입니다. 

[SCTE-35] 줄의 표준 참조 정의 (대시)는 [SCTE-214-1] sec 6.7.4 (MPD) 및 [SCTE-214-3] sec 7.3.2 (SCTE 35 큐 메시지의 캐리지)에서 사용할 수 있습니다.

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG 대시 (MPD) EventStream 신호

Manifest (MPD) 이벤트의 데코레이션은 Period 요소 내에 표시 되는 EventStream 요소를 사용 하 여 MPD에서 신호를 받습니다. 사용 되는 schemeId는 "urn: scte: scte35:2014: xml + bin"입니다.

> [!NOTE]
> 간단히 하기 위해 [SCTE-35]을 사용 하면 완전히 구문 분석 된 큐 메시지의 캐리지에 대 한 대 안으로 신호. 이진 요소 (SpliceInfoSection 요소 대신)에서 base64 인코딩 섹션을 사용할 수 있습니다.
> Azure Media Services는이 ' xml + bin ' 방식을 사용 하 여 MPD 매니페스트의 신호를 합니다.
> [IOP]에 사용 되는 권장 방법 이기도 합니다. [IOP 안내선의 ' Ad 삽입 이벤트 스트림 ' 섹션을](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams) 참조 하세요.
> 

EventStream 요소에 포함되는 특성은 다음과 같습니다.

| **특성 이름** | **형식**                | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 이 값은 메시지 구성표를 식별 하는 URN 또는 URL 이어야 **합니다** . 지원 되는 출력 schemeId는 "urn: scte: scte35:2014: xml + bin" (MPD) 214-1 당 "urn: scte:: 2014: xml + bin" 이어야 합니다. |
| 값              | 문자열                  | 선택 사항      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 스키마를 사용 하 여 여러 이벤트 스트림을 구분 하려면 값을 이벤트 스트림의 이름 ([RTMP] 수집에 대 한 [MS SSTR-수집] 또는 AMF 메시지 이름)으로 설정 **해야** 합니다.                                                                         |
| 시간 간격          | 부호 없는 32비트 정수 | 필수      | 시간 간격 (초당 틱)입니다.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>MPEG 대시 용 3.3.2 예제 이벤트 스트림

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 예에서는 Adobe simple 모드를 사용 하 여 RTMP 스트리밍의 mpd 매니페스트 신호를 사용 합니다.

다음 예제에서는 Adobe "simple" 모드 신호를 사용 하 여 RTMP 스트림에 대 한 Media Services 동적 패키지 작성의 발췌 한 EventStream를 보여 줍니다.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 예 mpd 모드를 사용 하 35 여 RTMP 스트림의 매니페스트 신호

다음 예제에서는 Adobe SCTE-35 모드 신호를 사용 하 여 RTMP 스트림에 대 한 Media Services 동적 패키지 작성의 발췌 한 EventStream를 보여 줍니다.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> PresentationTime은 메시지 도착 시간이 아니라 기간 시작 시간을 기준으로 변환 되는 [SCTE-35] 이벤트의 프레젠테이션 시간입니다.
> [MPEGDASH]는 기간의 시작을 기준으로 이벤트의 프레젠테이션 시간을 지정 하는 Event@presentationTime 정의 합니다.
> 프레젠테이션 시간 값 (초)은이 특성의 값과 EventStream@timescale 특성의 값을 나눈 값입니다.
> 표시 되지 않는 경우 프레젠테이션 시간 값은 0입니다.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 단일 마침표를 사용 하는 MPD (MPEG 대시 매니페스트) 예제 Adobe simple mode 신호 사용

다음 예제에서는 Adobe "simple" 모드 ad 신호 메서드를 사용 하 여 원본 RTMP 스트림에 대 한 Media Services 동적 패키지 작성의 출력을 보여 줍니다. 출력은 "urn: com: adobe: dpi: simple: 2015" 및 value 속성을 "simplesignal"로 설정 하 여 EventStream를 표시 하는 단일 기간 매니페스트입니다.
각 단순 신호는 들어오는 단순 신호에 따라 채워진 @presentationTime, @duration및 @id 속성을 사용 하 여 이벤트 요소에 제공 됩니다.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 예: 다중 마침표를 사용 하는 MPD (MPEG 대시 매니페스트) 및 Adobe SCTE35 mode 신호 사용

다음 예제에서는 Adobe SCTE35 모드 신호를 사용 하 여 원본 RTMP 스트림에 대 한 Media Services 동적 패키지 작성의 출력을 보여 줍니다.
이 경우 출력 매니페스트는 EventStream 요소와 @schemeIdUri 속성이 "urn: scte: scte35:2014: xml + bin"으로 설정 되 고 @value 속성이 "scte35"로 설정 된 여러 개의 mpd입니다. EventStream의 각 이벤트 요소는 전체 base64 인코딩된 이진 SCTE35 신호를 포함 합니다. 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG 파선 대역내 이벤트 메시지 상자 신호

대역 내 이벤트 스트림을 사용하려면 MPD에 적응 설정 수준의 InbandEventStream 요소가 있어야 합니다.  이 요소는 필수 schemeIdUri 특성 및 선택적 timescale 특성을 가지고 있으며 이벤트 메시지 상자('emsg')에도 나타납니다.  MPD에 정의 되지 않은 체계 식별자가 있는 이벤트 메시지 **상자가 없어야 합니다** .

대역 내 [SCTE-35] 캐리지의 경우 신호는 schemeId = "urn: scte: scte35:2013: bin"을 사용 **해야 합니다** .
[SCTE-35] 대역 내 메시지의 표준 정의는 [SCTE-214-3] sec 7.3.2 (SCTE 35 큐 메시지의 캐리지)에 정의 되어 있습니다.

다음 세부 정보에는 클라이언트가 ' emsg '에서 [SCTE-214-3]을 준수 하는 것으로 간주 되는 특정 값에 대 한 개요가 나와 있습니다.

| **필드 이름**          | **필드 형식**          | **필수 여부** | **설명**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | 문자열                  | 필수      | 메시지의 구성표를 식별합니다. 이 구성표는 라이브 서버 매니페스트 상자에 있는 Scheme 특성의 값으로 설정됩니다. 이 값은 메시지 구성표를 식별 하는 URN 이어야 **합니다** . [SCTE-35] 메시지의 경우 "urn: scte: scte35:2013: bin"은 [SCTE-214-3]와 호환 **되어야 합니다** .          |
| 값                   | 문자열                  | 필수      | 메시지의 의미 체계를 사용자 지정하기 위해 구성표 소유자가 추가로 사용하는 문자열 값입니다. 동일한 구성표를 사용하여 여러 이벤트 스트림을 구별하기 위해 값을 이벤트 스트림의 이름(부드러운 수집의 경우 trackName 또는 RTMP 수집의 경우 AMF 메시지 이름)으로 설정합니다. |
| 시간 간격               | 부호 없는 32비트 정수 | 필수      | 'emsg' 상자 내의 시간 및 재생 시간 필드의 날짜 표시줄(초당 틱 수 단위)입니다.                                                                                                                                                                                                            |
| Presentation_time_delta | 부호 없는 32비트 정수 | 필수      | 이벤트의 프레젠테이션 시간에 대한 미디어 프레젠테이션 시간 델타 및 이 세그먼트에서 가장 빠른 프레젠테이션 시간입니다. 프레젠테이션 시간과 기간은 [ISO-14496-12] 부록 I에서 정의한 대로 유형 1 또는 2의 SAP (스트림 액세스 점수)와 일치 **해야 합니다** .                                  |
| event_duration          | 부호 없는 32비트 정수 | 필수      | 이벤트의 재생 시간이거나 알 수 없는 재생 시간을 나타내는 0xFFFFFFFF입니다.                                                                                                                                                                                                                              |
| Id                      | 부호 없는 32비트 정수 | 필수      | 메시지의 이 인스턴스를 식별합니다. 동일한 의미 체계를 갖는 메시지의 값은 동일해야 합니다. 메시지가 수집될 때 ID를 지정하지 않으면 Azure Media Services에서 고유 ID를 생성합니다.                                                                                        |
| Message_data            | 바이트 배열              | 필수      | 이벤트 메시지입니다. [SCTE-35] 메시지의 경우 메시지 데이터는 [SCTE-214-3]과 호환 되는 이진 splice_info_section ()입니다.                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Adobe Simple 모드용 InBandEvenStream 엔터티 예제
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 대시 메시지 처리

이벤트는 비디오 및 오디오 트랙 모두에 대해 'emsg' 상자 내의 대역 내에서 신호를 받습니다.  presentation_time_delta가 15초 이하인 모든 세그먼트 요청에 대해 신호가 발생합니다. 

슬라이딩 프레젠테이션 시간 범위를 사용하도록 설정되면, 이벤트 메시지의 시간과 재생 시간의 합이 매니페스트에 있는 미디어의 데이터 시간보다 작은 경우 이벤트 메시지가 MPD에서 제거됩니다.  즉, 참조하는 미디어 시간이 슬라이딩 프레젠테이션 시간 범위를 벗어나는 경우 이벤트 메시지가 매니페스트에서 제거됩니다.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 인코더 공급 업체를 위한 수집 구현 지침

다음 지침은이 사양의 인코더 공급 업체의 구현에 영향을 줄 수 있는 일반적인 문제입니다.  아래 지침은 다른 사용자에 대 한이 사양을 보다 쉽게 구현할 수 있도록 실제 파트너 의견을 기반으로 수집 되었습니다. 

[SCTE-35] 메시지는 [수집]에 대 한 **"urn: scte: scte35:2013: bin"** 체계와 [RTMP] 수집에 대 한 **"scte35"** 체계를 사용 하 여 이진 형식으로 합니다. MPEG-2 전송 스트림 PTS(프레젠테이션 시간스탬프)를 기반으로 하는 [SCTE-35] 타이밍을 쉽게 변환할 수 있도록 하기 위해 PTS(splice_time()의 pts_time + pts_adjustment)와 미디어 타임라인 간의 매핑은 이벤트 프레젠테이션 시간(부드러운 수집의 fragment_absolute_time 필드 및 RTMP 수집의 시간 필드)에서 제공됩니다. 33비트 PTS 값이 대략 26.5시간마다 롤오버되기 때문에 매핑이 필요합니다.

부드러운 스트리밍 수집 [MS SSTR-수집]을 사용 하려면 미디어 Data Box (' mdat ')에 [SCTE-35]에 정의 된 **splice_info_section ()** 이 **있어야** 합니다. 

RTMP 수집의 경우 AMF 메시지의 cue 특성은 [SCTE-35]에 정의 된 b a s e 64로 인코딩된 **splice_info_section ()** 로 설정 됩니다.  

메시지에 위에 설명 된 형식이 있는 경우 위에 정의 된 대로 HLS, 부드러운 및 대시 클라이언트에 전송 됩니다.  

Azure Media Services 플랫폼을 사용 하 여 구현을 테스트할 때 인코딩 라이브 테스트로 이동 하기 전에 먼저 "통과" 라이브 테스트를 시작 하세요.

---

## <a name="change-history"></a>변경 내역

| 날짜     | 변경                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | SCTE35 지원에 대 한 RTMP 수집을 수정 했습니다. 정령 Live의 RTMP "onCuePoint"를 추가 했습니다.                                  |
| 08/22/19 | 사용자 지정 메타 데이터에 대 한 OnUserDataEvent를 RTMP에 추가 하도록 업데이트 되었습니다.                                                          |
| 1/08/20  | RTMP Simple 및 RTMP SCTE35 모드에서 오류를 수정 했습니다. "OnCuePoint"에서 "onAdCue"로 변경 되었습니다. 단순 모드 테이블을 업데이트 했습니다. |

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로 보기.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공하기
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
