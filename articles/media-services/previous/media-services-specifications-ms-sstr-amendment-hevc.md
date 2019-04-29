---
title: Azure Media Services – HEVC용 부드러운 스트리밍 프로토콜(MS-SSTR) 수정 사항 | Microsoft Docs
description: 이 사양은 Azure Media Services에서 HEVC를 사용한 조각화된 MP4 기반 라이브 스트리밍에 대한 프로토콜 및 형식을 설명합니다. HEVC 수집 및 스트리밍 지원을 포함하도록 부드러운 스트리밍 프로토콜 설명서(MS-SSTR)를 수정하였습니다. 이 문서에서는 HEVC를 제공하는 데 필요한 변경 내용을 명시하며, 그 외 "(변경 없음)"으로 표시된 부분은 단순히 설명용으로 복사되었습니다.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: johndeu;
ms.openlocfilehash: b26252ddda7997bebd730bb4c1007f76b3e645a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650712"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>HEVC용 부드러운 스트리밍 프로토콜(MS-SSTR) 수정 사항 

## <a name="1-introduction"></a>1 소개 

이 문서에서는 HEVC 인코딩 비디오의 부드러운 스트리밍을 사용하도록 부드러운 스트리밍 프로토콜 사양 [MS SSTR]에 적용되는 구체적인 수정 사항을 설명합니다. 이 사양에서는 HEVC 비디오 코덱을 제공하는 데 필요한 변경 내용만 간략하게 설명합니다. 이 문서는 [MS-SSTR] 사양과 동일한 번호 매기기 체계를 따릅니다. 문서 전체에 표시된 빈 헤드라인은 [MS SSTR] 사양에서 독자의 위치를 알려주기 위해 제공됩니다.  "(변경 없음)"은 텍스트가 단순히 설명용으로 복사되었음을 나타냅니다.

이 문서에서는 부드러운 스트리밍 매니페스트에서 HEVC 비디오 코덱 신호를 보내기 위한 기술 구현 요구 사항을 제공하며 표준 참조는 HEVC, HEVC의 Common Encryption을 포함하는 현재 MPEG 표준을 참조하도록 업데이트되고, ISO 기본 미디어 파일 형식의 상자 이름은 최신 사양과 일치하도록 업데이트되었습니다. 

참조된 부드러운 스트리밍 프로토콜 사양 [MS-SSTR]은 오디오나 비디오 같은 실시간 및 주문형 디지털 미디어를 여러 방법으로(인코더에서 웹 서버로, 서버에서 다른 서버로, 서버에서 HTTP 클라이언트로) 제공하는 데 사용되는 통신 형식을 설명합니다.
HTTP를 통한 MPEG-4([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) 기반 데이터 구조를 사용하면 품질 수준이 다른 여러 압축 미디어 콘텐츠 간에 거의 실시간으로 원활하게 전환할 수 있습니다. 따라서 클라이언트 컴퓨터 또는 디바이스에 따라 네트워크 및 비디오 렌더링 조건이 변하더라도 HTTP 클라이언트 최종 사용자에게 일관적인 재생 환경이 제공됩니다.

## <a name="11-glossary"></a>1.1 용어 

다음 용어는 *[MS 용어]* 에 정의되어 있습니다.

>   **GUID(Globally Unique Identifier) UUID(Universally Unique Identifier)**

다음 용어는 이 문서에서만 사용됩니다.

>  **컴퍼지션 시간:** 클라이언트에서 샘플이 표시되는 시간으로, [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)에 정의되어 있습니다.
> 
>   **CENC**: Common Encryption으로, [ISO/IEC 23001-7] 제2판에 정의되어 있습니다.
> 
>   **디코딩 시간:** 클라이언트에서 샘플을 디코딩하는 데 필요한 시간으로,[[ISO/IEC https://go.microsoft.com/fwlink/?LinkId=18369514496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)에 정의되어 있습니다.

**조각(Fragment):** 하나 이상의 **샘플**로 구성되어 있고 독립적으로 다운로드 가능한 **미디어** 단위입니다.

>   **HEVC:** 고효율 비디오 코딩(High Efficiency Video Coding)으로, [ISO/IEC 23008-2]에 정의되어 있습니다.
> 
>   **매니페스트:** 클라이언트가 **미디어**에 대한 요청을 할 수 있도록 허용하는 **프레젠테이션**에 대한 메타데이터. **미디어:** 클라이언트에서 **프레젠테이션** 재생에 사용하는 압축된 오디오, 비디오 및 텍스트 데이터. **미디어 형식:** 오디오 또는 비디오를 압축된 **샘플**로 표시하는 데 사용되는 잘 정의된 형식.
> 
>   **프레젠테이션:** 단일 동영상을 재생하는 데 필요한 모든 **스트림** 및 관련 메타데이터 세트. **요청:** 클라이언트에서 서버로 전송된 HTTP 메시지로, [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)에 정의되어 있습니다. **응답:** 서버에서 클라이언트로 전송된 HTTP 메시지로, [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)에 정의되어 있습니다.
> 
>   **샘플:** **미디어**가 저장되고 처리되는 가장 작은 기본 단위(예: 프레임).
> 
>   **MAY(할 수 있다), SHOULD(하는 것이 좋다), MUST(반드시 해야 한다), SHOULD NOT(하지 않는 것이 좋다), MUST NOT(절대 하면 안 된다):** 이러한 용어(영문의 경우 모두 대문자)는 [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317)에 설명된 대로 사용됩니다. 선택적 동작을 서술하는 설명문은 MAY(할 수 있다), SHOULD(하는 것이 좋다) 또는 SHOULD NOT(하지 않는 것이 좋다) 중 하나를 사용합니다.

## <a name="12-references"></a>1.2 참조

>   Microsoft Open Specifications 설명서에 대한 참조에는 게시 연도가 포함되지 않습니다. 왜냐하면 링크는 최신 버전의 문서에 연결되며, 이 문서가 자주 업데이트되기 때문입니다. 다른 문서에 대한 참조에는 사용 가능한 게시 연도가 포함됩니다.

### <a name="121-normative-references"></a>1.2.1 표준 참조 

>  [MS-SSTR] 부드러운 스트리밍 프로토콜 *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] 국제 표준화 기구, "Information technology -- Coding of audio-visual objects -- Part 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, Amendments 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] 국제 표준화 기구, "Information technology -- Coding of audio-visual objects -- Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Information technology -- High efficiency coding and media delivery in heterogeneous environments -- Part 2: High efficiency video coding: 2013 or newest edition<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Information technology — MPEG systems technologies — Part 7: Common encryption in ISO base media file format files, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, “The 'Codecs' and 'Profiles' Parameters for "Bucket" Media Types”<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] MP4 등록 기관, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels"를 BCP 14, RFC 2119, 1997년 3월,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 정보 참조 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols Master Glossary*(Windows 프로토콜 마스터 용어집)."
> 
>   [RFC3548] Josefsson, S., ed., "는 Base16, Base32, and Base64 Data Encodings", RFC 3548, 2003 년 7 월 [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., and Overell, P., "Augmented BNF for Syntax   Specifications: ABNF", STD 68, RFC 5234, January 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 개요 

>   HEVC를 제공하는 데 필요한 부드러운 스트리밍 사양 변경 내용만 아래에 명시되어 있습니다. 변경되지 않은 섹션 헤더는 참조된 부드러운 스트리밍 사양 [MS-SSTR]에서 위치를 유지하기 위해 나열됩니다.

## <a name="14-relationship-to-other-protocols"></a>1.4 다른 프로토콜과의 관계 

## <a name="15-prerequisitespreconditions"></a>1.5 필수 구성 요소/사전 조건 

## <a name="16-applicability-statement"></a>1.6 적용 가능성 설명문 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 버전 관리 및 기능 협상 

## <a name="18-vendor-extensible-fields"></a>1.8 공급 업체가 확장 가능한 필드 

>   다음 방법은 HEVC 비디오 형식을 사용하는 스트림을 식별하는 데 사용해야 합니다.
> 
>   * **미디어 형식에 대한 사용자 지정 설명 포함 코드:** 이 기능은 *2.2.2.5* 섹션에 지정된 대로 **FourCC** 필드에서 제공합니다.
>   구현자 확장 확장 코드에 지정 된 대로 MPEG4-RA에 등록 하 여 충돌 하지 않는지 확인할 수 있습니다 [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 표준 할당 

## <a name="2-messages"></a>2 메시지 

## <a name="21-transport"></a>2.1 전송

## <a name="22-message-syntax"></a>2.2 메시지 구문 

### <a name="221-manifest-request"></a>2.2.1 매니페스트 요청 

### <a name="222-manifest-response"></a>2.2.2 매니페스트 응답 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion(변수):** 매니페스트 응답 메시지의 부 버전. 반드시 2로 설정해야 합니다. (변경 없음)
> 
>   **TimeScale(변수):** Duration 특성의 시간 단위로, 1초의 증분으로 지정됩니다. 기본값
> 1. (변경 없음)
> 
>    권장된 값은 비디오 프레임 및 소수 자릿수 프레임 속도 (예를 들어 30/1.001hz) 비디오를 포함 하는 조각의 정확한 기간을 나타내는 90000입니다.

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

CENC(일반 암호화)가 비디오 또는 오디오 스트림에 적용된 경우 ProtectionElement를 제공해야 합니다. HEVC 암호화 스트림은 일반 암호화 제2판 [ISO/IEC 23001-7]을 준수해야 합니다. VCL NAL 단위의 조각 데이터만 암호화해야 합니다.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale(변수):** 이 스트림의 기간 및 시간 값에 대한 시간 단위로, 1초의 증분으로 지정됩니다. HEVC 스트림에는 값 90000을 권장합니다. 오디오 스트림에는 파형 샘플링 빈도(예: 48000 또는 44100)와 일치하는 값을 사용하는 것이 좋습니다.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC(변수):** 각 샘플에 사용되는 미디어 형식을 식별하는 네 자리 문자 코드. 다음 범위의 값은 다음 의미 체계 의미에 예약됩니다.
> 
> * “hev1”: 이 트랙에 대한 비디오 샘플은 [ISO/IEC-14496-15]에 지정된 'hev1' 샘플 설명 형식을 사용하여 HEVC 비디오를 사용합니다.
> 
>   **CodecPrivateData(변수):** 미디어 형식에는 고유하고 트랙의 모든 샘플에는 공통적인 매개 변수를 지정하는 데이터로, 16진수 코드 바이트 문자열로 표시됩니다. 바이트 시퀀스의 형식과 의미 체계 의미는 다음과 같이 **FourCC** 필드 값에 따라 달라집니다.
> 
>   * TrackElement가 HEVC 비디오를 설명하는 경우 **FourCC** 필드는 **"hev1"** 과 같아야 하고
> 
>   합니다 **CodecPrivateData** 필드는 abnf 다음 바이트 시퀀스의 16 진수 코드 문자열 표현을 포함 되어야 [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (ms-sstr에서 변경 없음)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField는 SPS(시퀀스 매개 변수 집합)를 포함합니다.
> 
>   * PPSField는 PPS(조각 매개 변수 집합)를 포함합니다.
> 
>   참고: VPS(비디오 매개 변수 세트)는 CodecPrivateData에 포함되지 않지만, 'hvcC' 상자에 저장된 파일의 파일 헤더에는 포함되어야 합니다. 부드러운 스트리밍 프로토콜을 사용하는 시스템은 사용자 지정 특성 “코덱”을 사용하여 추가 디코딩 매개 변수(예: HEVC 계층)에 신호를 보내야 합니다.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **SmoothStreamingMedia의 MajorVersion** 필드는 반드시 2로 설정해야 하고, **MinorVersion** 필드는 반드시 2로 설정해야 합니다. (변경 없음)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 조각 요청 

>   **참고**: **MinorVersion** 2 및 ‘hev1’에 대해 요청되는 기본 미디어 형식은 [ISO/IEC 14496-12] ISO 기본 미디어 파일 형식 및 [ISO/IEC 23001-7] 공통 암호화 제2판에 지정된 ‘iso8’ 브랜드 ISO 기본 미디어 파일 형식입니다.

### <a name="224-fragment-response"></a>2.2.4 조각 응답 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox**는 사용되지 않으며, 그 함수는 [ISO/IEC 14496-12] 섹션 8.8.12에 명시된 트랙 조각 디코딩 시간 상자(‘tfdt’ - Track Fragment Decode Time Box)로 대체되었습니다.
> 
>   **참고**: 클라이언트가 트랙 실행 상자('trun' - Track Run Box)에 나열된 샘플 기간을 모두 더하거나 샘플 시간을 기본 샘플 기간과 곱하여 조각의 시간을 계산할 수 있습니다. ‘tfdt’의 baseMediaDecodeTime과 조각 기간을 더하면 그 다음 조각의 URL 시간 매개 변수와 같습니다.
> 
>   필요한 경우 [ISO/IEC 14496-12] 섹션 8.16.5에 지정된 대로 동영상 조각 상자에서 참조하는 첫 번째 샘플의 트랙 조각 디코딩 시간과 일치하는 UTC 시간을 나타내도록 동영상 조각 상자(‘moof’)보다 생산자 참조 시간 상자('prft')를 먼저 삽입하는 것이 좋습니다.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox**는 사용되지 않으며, 그 함수는 [ISO/IEC 14496-12] 섹션 8.8.12에 명시된 트랙 조각 디코딩 시간 상자(‘tfdt’ - Track Fragment Decode Time Box)로 대체되었습니다.
> 
>   **참고**: 클라이언트가 트랙 실행 상자('trun' - Track Run Box)에 나열된 샘플 기간을 모두 더하거나 샘플 시간을 기본 샘플 기간과 곱하여 조각의 시간을 계산할 수 있습니다. ‘tfdt’의 baseMediaDecodeTime과 조각 기간을 더하면 그 다음 조각의 URL 시간 매개 변수와 같습니다. 미리 보기 주소는 라이브 스트리밍을 지연하므로 사용되지 않습니다.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** 및 관련 필드는 조각의 샘플 메타데이터에 대한 기본값을 캡슐화합니다. **TfhdBox** 필드의 구문은 [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 섹션 8.8.7에 정의된 트랙 조각 헤더 상자의 엄격한 구문 하위 집합입니다.
> 
>   **BaseDataOffset(8바이트):** **MdatBox** 필드의 시작부터 **MdatBox** 필드의 샘플 필드까지 포함하는 오프셋(바이트)입니다. 이 제한을 알리려면 default-base-is-moof 플래그(0x020000)를 설정해야 합니다.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** 및 관린 필드는 요청된 조각에 대한 샘플 메타데이터를 캡슐화합니다. **TrunBox**의 구문은 [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 섹션 8.8.8에 정의된 버전 1 트랙 조각 실행 상자의 엄격한 하위 집합입니다.
> 
>   **SampleCompositionTimeOffset(4바이트):** 첫 번째로 표시된 샘플의 프레젠테이션 시간이 첫 번째로 디코딩된 샘플의 디코딩 시간과 일치하도록 각 샘플의 샘플 컴퍼지션 시간 오프셋이 조정되었습니다. 음수 비디오 샘플 컴퍼지션 오프셋은
> 
>   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)에 정의된 대로 사용해야 합니다.
> 
>   참고: 이렇게 하면 가장 큰 디코딩된 그림 버퍼 제거 지연 시간과 동일한 오디오 지연 비디오로 인한 비디오 동기화 오류를 방지하고, 제거 지연 시간이 다를 수 있는 대체 조각 간의 프레젠테이션 타이밍이 유지됩니다.
> 
>   ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) 섹션에 정의된 필드 구문은 다음을 제외하고 동일하게 유지됩니다.
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 조각 응답 공통 필드 

### <a name="225-sparse-stream-pointer"></a>2.2.5 스파스 스트림 포인터 

### <a name="226-fragment-not-yet-available"></a>2.2.6 아직 조각을 사용할 수 없음 

### <a name="227-live-ingest"></a>2.2.7 실시간 수집 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType(변수):** MPEG-4([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) 파일의 하위 형식 및 의도된 사용법과 상위 수준 특성을 지정합니다.
> 
>   **MajorBrand(변수):** 미디어 파일의 주요 브랜드입니다. 반드시 "isml"로 설정해야 합니다.
> 
>   **MinorVersion(변수):** 미디어 파일의 부 버전. 반드시 1로 설정해야 합니다.
> 
>   **CompatibleBrands(변수):** MPEG-4의 지원되는 브랜드를 지정합니다.
>   반드시 "ccff" 및 "iso8"을 포함해야 합니다.
> 
>   ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) 섹션에 정의된 필드 구문은 다음과 같습니다.

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**참고**: ‘ccff’ 및 ‘iso8’ 호환성 브랜드는 조각이 "공용 컨테이너 파일 형식"과 Common Encryption [ISO/IEC 23001-7] 및 ISO 기본 미디어 파일 형식 버전 4 [ISO/IEC 14496-12]를 준수한다는 것을 나타냅니다.

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 조각 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 트랙 조각 확장 헤더 

### <a name="228-server-to-server-ingest"></a>2.2.8 서버 간 수집 

## <a name="3-protocol-details"></a>3 프로토콜 세부 정보 


## <a name="31-client-details"></a>3.1 클라이언트 세부 정보 

### <a name="311-abstract-data-model"></a>3.1.1 추상 데이터 모델 

#### <a name="3111-presentation-description"></a>3.1.1.1 프레젠테이션 설명 

>   프레젠테이션 설명 데이터 요소는 프레젠테이션에 대한 모든 메타데이터를 캡슐화합니다.
> 
>   프레젠테이션 메타데이터: 프레젠테이션의 모든 스트림에 공통적인 메타데이터 세트. 프레젠테이션 메타데이터는 *2.2.2.1* 섹션에 지정된 다음 필드를 구성합니다.
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **Duration**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   HEVC 스트림을 포함하는 프레젠테이션은 다음 항목을 설정해야 합니다.

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0(참고: 사용되지 않는 상자)
> 
>   프레젠테이션이 다음 항목도 설정해야 합니다.

    TimeScale = 90000

>   스트림 컬렉션: 스트림 설명 데이터 요소의 컬렉션으로, *3.1.1.1.2* 섹션에 지정되어 있습니다.
> 
>   보호 설명: 보호 시스템 메타데이터 설명 데이터 요소의 컬렉션으로, *3.1.1.1.1*에 지정되어 있습니다.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 보호 시스템 메타데이터 설명 

>   보호 시스템 메타데이터 설명 데이터 요소는 단일 콘텐츠 보호 시스템과 관련된 메타데이터를 캡슐화합니다. (변경 없음)
> 
>   보호 헤더 설명: 단일 Content Protection 시스템에 관련된 Content Protection 메타데이터. 보호 헤더 설명은 *2.2.2.2* 섹션에 명시된 다음 필드로 구성됩니다.
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 스트림 설명 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 트랙 설명 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 사용자 지정 특성 설명 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 조각 참조 설명 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 트랙 관련 조각 참조 설명 

#### <a name="3112-fragment-description"></a>3.1.1.2 조각 설명 

##### <a name="31121-sample-description"></a>3.1.1.2.1 샘플 설명 

### <a name="312-timers"></a>3.1.2 타이머 

### <a name="313-initialization"></a>3.1.3 초기화 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 상위 계층에서 트리거하는 이벤트 

#### <a name="3141-open-presentation"></a>3.1.4.1 프레젠테이션 열기 

#### <a name="3142-get-fragment"></a>3.1.4.2 조각 가져오기 

#### <a name="3143-close-presentation"></a>3.1.4.3 프레젠테이션 닫기 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 이벤트 처리 및 규칙 시퀀싱 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 매니페스트 요청 및 매니페스트 응답 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 조각 요청 및 조각 응답

## <a name="32-server-details"></a>3.2 서버 세부 정보

## <a name="33-live-encoder-details"></a>3.3 실시간 인코더 세부 정보 

## <a name="4-protocol-examples"></a>4 프로토콜 예제 

## <a name="5-security"></a>5 보안 

## <a name="51-security-considerations-for-implementers"></a>5.1 구현에 대한 보안 고려 사항

>   이 프로토콜을 사용하여 전송되는 콘텐츠의 상업적 가치가 높은 경우 콘텐츠 보호 시스템을 사용하여 콘텐츠 무단 사용을 방지해야 합니다. **ProtectionElement**를 사용하여 콘텐츠 보호 시스템 사용과 관련된 메타데이터를 전달할 수 있습니다. 보호되는 오디오 및 비디오 콘텐츠는 MPEG Common Encryption 제2판: 2015 [ISO/IEC 23001-7]에 지정된 대로 암호화해야 합니다.
> 
>   **참고**: HEVC 비디오의 경우 VCL NAL의 조각 데이터만 암호화됩니다. 조각 헤더 및 다른 NAL은 암호화 전에도 프레젠테이션 애플리케이션에 액세스할 수 있습니다. 보안 비디오 경로에서 암호화된 정보는 프레젠테이션 애플리케이션에 사용할 수 없습니다.

## <a name="52-index-of-security-parameters"></a>5.2 보안 매개 변수의 인덱스 


| **보안 매개 변수**  | **섹션**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption 상자 | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Common Encryption 상자

다음 상자는 일반 암호화가 적용될 때 조각 응답에 나타날 수 있으며, [ISO/IEC 23001-7] 또는 [ISO/IEC 14496-12]에 지정되어 있습니다.

1.  보호 시스템 관련 헤더 상자('pssh')

2.  샘플 암호화 상자('senc')

3.  샘플 보조 정보 오프셋 상자('saio')

4.  샘플 보조 정보 크기 상자('saiz')

5.  샘플 그룹 설명 상자('sgpd')

6.  샘플-그룹 상자('sbgp')

-----------------------

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
