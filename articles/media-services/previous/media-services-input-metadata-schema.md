---
title: Azure Media Services 입력 메타데이터 스키마 | Microsoft 문서
description: 이 항목에서는 Azure Media Services 입력 메타데이터 스키마에 대한 개요를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: fa4487b07f130947ac5da2a5dbae6776b06acbe7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463772"
---
# <a name="input-metadata"></a>입력 메타데이터 

인코딩 작업은 일부 인코딩 태스크를 수행할 입력 자산(또는 자산)과 연결됩니다.  태스크가 완료되는 즉시 출력 자산이 생성됩니다.  출력 자산에는 비디오, 오디오, 미리 보기, 매니페스트 등이 포함됩니다. 출력 자산에는 입력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타데이터 XML 파일의 이름 형식은 &lt;asset_id&gt;_metadata.xml입니다(예: 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml). 여기서 &lt;asset_id&gt;는 입력 자산의 AssetId 값입니다.  

Media Services는 메타데이터를 생성하기 위해 선제적으로 입력 자산을 검사하지 않습니다. 입력 자산이 작업에서 처리되는 경우 입력 메타데이터는 아티팩트로만 생성됩니다. 따라서 이 아티팩트는 출력 자산에 기록됩니다. 다른 도구는 입력 자산 및 출력 자산에 대한 메타데이터를 생성하는 데 사용됩니다. 따라서 입력 메타데이터는 출력 메타데이터와 스키마가 약간 다를 수 있습니다.

메타데이터 파일을 검사하려는 경우 **SAS** 로케이터를 만들어 로컬 컴퓨터에 파일을 다운로드할 수 있습니다. [Media Services .NET SDK 확장을 사용](media-services-dotnet-get-started.md)하면 SAS 로케이터를 만들고 파일을 다운로드하는 방법에 대한 예제를 찾을 수 있습니다.  

이 문서에서는 입력 메타데이터(&lt; asset_id&gt;_metadata.xml)의 기초가 되는 XML 스키마의 요소 및 형식에 대해 설명합니다.  출력 자산에 대한 메타데이터가 포함된 파일에 대한 자세한 내용은 [출력 메타데이터](media-services-output-metadata-schema.md)를 참조하세요.  

[스키마 코드](media-services-input-metadata-schema.md#code) 및 [XML 예제](media-services-input-metadata-schema.md#xml)는 이 문서의 끝에 있습니다.  
 

## <a name="AssetFiles"></a> AssetFiles 요소(루트 요소)
인코딩 작업에 대한 [AssetFile 요소](media-services-input-metadata-schema.md#AssetFile) 컬렉션이 포함됩니다.  

이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

| 이름 | 설명 |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |단일 자식 요소입니다. 자세한 내용은 [AssetFile 요소](media-services-input-metadata-schema.md#AssetFile)를 참조하세요. |

## <a name="AssetFile"></a> AssetFile 요소
 자산 파일을 설명하는 속성과 요소를 포함하고 있습니다.  

 이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Name**<br /><br /> 필수 |**xs:string** |자산 파일의 이름입니다. |
| **크기**<br /><br /> 필수 |**xs:long** |자산 파일의 크기(바이트)입니다. |
| **Duration**<br /><br /> 필수 |**xs:duration** |콘텐츠 재생 시간입니다. 예제: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> 필수 |**xs:int** |자산 파일의 스트림 수입니다. |
| **FormatNames**<br /><br /> 필수 |**xs: string** |형식 이름입니다. |
| **FormatVerboseNames**<br /><br /> 필수 |**xs: string** |자세한 형식 이름입니다. |
| **StartTime** |**xs:duration** |콘텐츠 시작 시간입니다. 예제: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |자산 파일의 평균 비트 전송률(Kbps)입니다. |

> [!NOTE]
> 다음 4개의 자식 요소가 순서대로 나타나야 합니다.  
> 
> 

### <a name="child-elements"></a>자식 요소
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Programs**<br /><br /> minOccurs="0" | |자산 파일의 형식이 MPEG-TS인 경우 모든 [Programs 요소](media-services-input-metadata-schema.md#Programs)의 컬렉션입니다. |
| **VideoTracks**<br /><br /> minOccurs="0" | |각각의 실제 자산 파일에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. 이 요소에는 자산 파일의 일부인 모든 [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) 컬렉션이 포함됩니다. |
| **AudioTracks**<br /><br /> minOccurs="0" | |각각의 실제 자산 파일에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. 이 요소에는 자산 파일의 일부인 모든 [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) 컬렉션이 포함됩니다. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |key/value 문자열로 표시되는 자산 파일의 메타데이터입니다. 예를 들면 다음과 같습니다.<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Id**<br /><br /> 필수 |**xs:int** |이 오디오 또는 비디오 트랙의 0 기준 인덱스입니다.<br /><br /> 반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. |
| **Codec** |**xs:string** |비디오 트랙 코덱 문자열입니다. |
| **CodecLongName** |**xs: string** |오디오 또는 비디오 트랙 코덱의 긴 이름입니다. |
| **TimeBase**<br /><br /> 필수 |**xs:string** |시간 기준입니다. 예제: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |프레임 수입니다(비디오 트랙의 경우). |
| **StartTime** |**xs: duration** |트랙 시작 시간입니다. 예제: StartTime="PT2.669S" |
| **Duration** |**xs:duration** |트랙 지속 시간입니다. 예제: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> 다음 2개의 자식 요소가 순서대로 나타나야 합니다.  
> 
> 

### <a name="child-elements"></a>자식 요소
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |프레젠테이션 정보가 포함됩니다(예: 특정 오디오 트랙이 시각 장애 시청자를 위한 것인지 여부). |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |다양한 정보를 저장하는 데 사용할 수 있는 일반 key/value 문자열입니다. 예제: key=”language” 및 value=”eng” |

## <a name="AudioTrackType"></a> AudioTrackType(TrackType에서 상속)
 **AudioTrackType**는 [TrackType](media-services-input-metadata-schema.md#TrackType)에서 상속되는 전역 복합 형식입니다.  

 형식은 자산 파일의 특정 오디오 트랙을 나타냅니다.  

 이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |샘플 형식입니다. |
| **ChannelLayout** |**xs: string** |채널 레이아웃입니다. |
| **Channels**<br /><br /> 필수 |**xs:int** |오디오 채널 수입니다(0개 이상). |
| **SamplingRate**<br /><br /> 필수 |**xs:int** |오디오 샘플링 속도(샘플/초 또는 Hz)입니다. |
| **Bitrate** |**xs:int** |자산 파일에서 계산되는 평균 오디오 비트 전송률(bps)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 이 개수에 포함되지 않습니다. |
| **BitsPerSample** |**xs:int** |wFormatTag 형식 샘플당 비트입니다. |

## <a name="VideoTrackType"></a> VideoTrackType(TrackType에서 상속)
**VideoTrackType**는 [TrackType](media-services-input-metadata-schema.md#TrackType)에서 상속되는 전역 복합 형식입니다.  

형식은 자산 파일의 특정 비디오 트랙을 나타냅니다.  

이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **FourCC**<br /><br /> 필수 |**xs:string** |비디오 코덱 FourCC 코드입니다. |
| **프로필** |**xs: string** |비디오 트랙의 프로필입니다. |
| **Level** |**xs: string** |비디오 트랙의 수준입니다. |
| **PixelFormat** |**xs: string** |비디오 트랙의 픽셀 형식입니다. |
| **Width**<br /><br /> 필수 |**xs:int** |인코딩된 비디오 너비(픽셀)입니다. |
| **Height**<br /><br /> 필수 |**xs:int** |인코딩된 비디오 높이(픽셀)입니다. |
| **DisplayAspectRatioNumerator**<br /><br /> 필수 |**xs: double** |비디오 디스플레이 가로 세로 비율의 분자입니다. |
| **DisplayAspectRatioDenominator**<br /><br /> 필수 |**xs:double** |비디오 디스플레이 가로 세로 비율의 분모입니다. |
| **DisplayAspectRatioDenominator**<br /><br /> 필수 |**xs: double** |비디오 샘플 가로 세로 비율의 분자입니다. |
| **SampleAspectRatioNumerator** |**xs: double** |비디오 샘플 가로 세로 비율의 분자입니다. |
| **SampleAspectRatioNumerator** |**xs:double** |비디오 샘플 가로 세로 비율의 분모입니다. |
| **FrameRate**<br /><br /> 필수 |**xs: decimal** |.3f 형식으로 측정된 비디오 프레임 속도입니다. |
| **Bitrate** |**xs:int** |자산 파일에서 계산되는 평균 비디오 비트 전송률(Kb/초)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다. |
| **MaxGOPBitrate** |**xs: int** |이 비디오 트랙의 최대 GOP 평균 비트 전송률(Kb/초)입니다. |
| **HasBFrames** |**xs:int** |B 프레임의 비디오 트랙 번호입니다. |

## <a name="MetadataType"></a> MetadataType
**MetadataType**은 자산 파일의 메타데이터를 key/value 문자열로 설명하는 전역 복합 형식입니다. 예제: key=”language” 및 value=”eng”  

이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **key**<br /><br /> 필수 |**xs:string** |key/value 쌍의 키입니다. |
| **값**<br /><br /> 필수 |**xs:string** |key/value 쌍의 값입니다. |

## <a name="ProgramType"></a> ProgramType
**ProgramType**은 프로그램을 설명하는 전역 복합 형식입니다.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **ProgramId**<br /><br /> 필수 |**xs:int** |Program ID입니다. |
| **NumberOfPrograms**<br /><br /> 필수 |**xs:int** |프로그램 수입니다. |
| **PmtPid**<br /><br /> 필수 |**xs:int** |PMT(프로그램 맵 테이블)에는 프로그램에 대한 정보가 포함됩니다.  자세한 내용은 [PMT](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT)를 참조하세요. |
| **PcrPid**<br /><br /> 필수 |**xs: int** |디코더에서 사용됩니다. 자세한 내용은 [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR)을 참조하세요. |
| **StartPTS** |**xs: long** |프레젠테이션 시작 타임스탬프입니다. |
| **EndPTS** |**xs: long** |프레젠테이션 끝 타임스탬프입니다. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType**은 스트림을 설명하는 전역 복합 형식입니다.  

이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="attributes"></a>특성
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **기본값**<br /><br /> 필수 |**xs: int** |기본 프레젠테이션임을 나타내려면 이 속성을 1로 설정합니다. |
| **Dub**<br /><br /> 필수 |**xs:int** |더빙된 프레젠테이션임을 나타내려면 이 속성을 1로 설정합니다. |
| **Original**<br /><br /> 필수 |**xs: int** |원본 프레젠테이션임을 나타내려면 이 속성을 1로 설정합니다. |
| **Comment**<br /><br /> 필수 |**xs:int** |이 트랙에 해설이 있음을 나타내려면 이 속성을 1로 설정합니다. |
| **Lyrics**<br /><br /> 필수 |**xs:int** |이 트랙에 가사가 있음을 나타내려면 이 속성을 1로 설정합니다. |
| **Karaoke**<br /><br /> 필수 |**xs:int** |가라오케 트랙(배경 음악, 보컬 없음)임을 나타내려면 이 성을 1로 설정합니다. |
| **Forced**<br /><br /> 필수 |**xs:int** |강제 프레젠테이션임을 나타내려면 이 속성을 1로 설정합니다. |
| **HearingImpaired**<br /><br /> 필수 |**xs:int** |1로 청각 사람들을 위해이 트랙 임을 나타내려면이 특성을 설정 합니다. |
| **VisualImpaired**<br /><br /> 필수 |**xs:int** |시각 장애자용 트랙임을 나타내려면 이 속성을 1로 설정합니다. |
| **CleanEffects**<br /><br /> 필수 |**xs: int** |이 트랙에 새 효과가 있음을 나타내려면 이 속성을 1로 설정합니다. |
| **AttachedPic**<br /><br /> 필수 |**xs: int** |이 트랙에 그림이 있음을 나타내려면 이 속성을 1로 설정합니다. |

## <a name="Programs"></a> Programs 요소
여러 **Program** 요소를 보유하는 래퍼 요소입니다.  

### <a name="child-elements"></a>자식 요소
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |MPEG-TS 형식의 자산 파일에는 자산 파일의 프로그램에 대한 정보가 포함됩니다. |

## <a name="VideoTracks"></a> VideoTracks 요소
 여러 **VideoTrack** 요소를 보유하는 래퍼 요소입니다.  

 이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="child-elements"></a>자식 요소
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType(TrackType에서 상속)](media-services-input-metadata-schema.md#VideoTrackType) |자산 파일의 비디오 트랙에 대한 정보가 포함됩니다. |

## <a name="AudioTracks"></a> AudioTracks 요소
 여러 **AudioTrack** 요소를 보유하는 래퍼 요소입니다.  

 이 문서의 마지막에 있는 [XML 예제](media-services-input-metadata-schema.md#xml)를 참조하세요.  

### <a name="elements"></a>요소
| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType(TrackType에서 상속)](media-services-input-metadata-schema.md#AudioTrackType) |자산 파일의 오디오 트랙에 대한 정보가 포함됩니다. |

## <a name="code"></a> 스키마 코드
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a> XML 예제
다음은 입력 메타데이터 파일의 예제입니다.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

