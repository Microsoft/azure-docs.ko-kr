---
title: 고객에게 콘텐츠 배달 | Microsoft 문서
description: 이 항목에서는 Azure Media Services를 사용하여 콘텐츠를 배달하는데 필요한 항목을 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5db2cb983c0c3cd0e2194f7686964d9ec3828d6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232278"
---
# <a name="deliver-content-to-customers"></a>고객에게 콘텐츠 배달
고객에게 스트리밍 또는 주문형 비디오 콘텐츠를 전달할 때는 다양한 네트워크 조건의 다양한 디바이스에 고품질 비디오를 제공하는 것이 목표입니다.

이 목표를 위해 다음을 수행할 수 있습니다.

* 사용자의 스트림을 다중 비트 전송률(적응 비트 전송률) 비디오 스트림으로 인코딩합니다. 이렇게 하면 품질 및 네트워크 상태가 관리됩니다.
* Microsoft Azure Media Services [동적 패키징](media-services-dynamic-packaging-overview.md) 을 사용하여 스트림을 여러 프로토콜로 동적으로 다시 패키징합니다. 이렇게 하면 여러 디바이스의 스트리밍이 관리됩니다. Media Services서 다음과 같은 적응 비트 전송률 스트리밍 기술 제공을 지원합니다. <br/>
    * **HLS**(HTTP 라이브 스트리밍) - URL의 "/Manifest" 부분에 "(format=m3u8-aapl)" 경로를 추가하여 스트리밍 원본 서버에 **Apple iOS** 네이티브 디바이스에서 사용할 수 있게 HLS 콘텐츠를 반환하도록 지정합니다(자세한 내용은 [로케이터](#locators) 및 [URL](#URLs) 참조).
    * **MPEG-DASH** - URL의 "/Manifest" 부분에 "(format=mpd-time-csf)" 경로를 추가하여 스트리밍 원본 서버에 MPEG-DASH를 반환하도록 지시합니다(자세한 내용은 [로케이터](#locators) 및 [URL](#URLs) 참조).
    * **부드러운 스트리밍**.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 

이 문서에서는 중요한 콘텐츠 배달 개념에 대해 간략하게 설명합니다.

알려진 문제를 확인하려면 [알려진 문제](media-services-deliver-content-overview.md#known-issues)를 참조하세요.

## <a name="dynamic-packaging"></a>동적 패키징
Media Services가 제공하는 동적 패키징을 사용하여 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 Media Services에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 다시 패키지하지 않고도 배달할 수 있습니다. 동적 패키징을 사용하여 콘텐츠를 전달하는 것이 좋습니다.

동적 패키징을 활용하려면 mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩해야 합니다.

동적 패키징을 사용하여 단일 저장소 형식으로 파일을 저장하고 요금을 지불할 수 있습니다. Media Services는 사용자의 요청에 따라 적절한 응답을 빌드하고 제공합니다.

동적 패키징은 표준 및 프리미엄 스트리밍 엔드포인트에 사용할 수 있습니다. 

자세한 내용은 [동적 패키징](media-services-dynamic-packaging-overview.md)을 참조하세요.

## <a name="filters-and-dynamic-manifests"></a>필터 및 동적 매니페스트
Media Services를 사용하여 자산에 대한 필터를 정의할 수 있습니다. 이러한 필터는 고객이 비디오의 특정 부분만 재생하거나 자산과 연결된 모든 변환 대신 고객의 디바이스가 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정하는 등을 선택할 수 있도록 하는 서버 측 규칙입니다. 하나 이상의 지정한 필터에 따라 비디오를 스트림하는 고객의 요청에 따라 생성된 *동적 매니페스트* 를 통해 이러한 필터링이 이루어집니다.

자세한 내용은 [필터 및 동적 매니페스트](media-services-dynamic-manifest-overview.md)를 참조하세요.

## <a name="a-idlocatorslocators"></a><a id="locators"/>로케이터
콘텐츠 스트림 또는 다운로드에 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어 자산을 게시해야 합니다. 로케이터는 자산에 포함된 파일에 액세스할 수 있는 진입점을 제공합니다. Media Services는 두 가지 유형의 로케이터를 지원합니다.

* OnDemandOrigin 로케이터. 미디어를 스트리밍(예: MPEG-DASH, HLS 또는 부드러운 스트리밍)하거나 점진적으로 파일을 다운로드하는 데 사용합니다.
* SAS(공유 액세스 서명) URL 로케이터. 미디어 파일을 로컬 컴퓨터로 다운로드하는 데 사용합니다.

*액세스 정책* 은 사용 권한(예: 읽기, 쓰기 및 나열) 및 클라이언트가 특정 자산에 대한 액세스 권한을 갖는 기간을 정의하는 데 사용됩니다. OnDemandOrigin 로케이터를 만들 때는 목록 권한(AccessPermissions.List)을 사용할 수 없습니다.

로케이터에는 만료 날짜가 있습니다. Azure 포털은 로케이터의 만료 날짜를 100년 이후로 설정합니다.

> [!NOTE]
> Azure 포털을 사용하여 2015년 3월 이전에 로케이터를 만든 경우 이러한 로케이터는 2년 후에 만료되도록 설정되었을 것입니다.
> 
> 

로케이터의 만료 날짜를 업데이트하려면 [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) 또는 [.NET](https://go.microsoft.com/fwlink/?LinkID=533259) API를 사용합니다. SAS 로케이터의 만료 날짜를 업데이트할 때 해당 URL도 변경됩니다.

로케이터는 사용자별 액세스 제어를 관리하도록 설계되지 않았습니다. DRM(Digital Rights Management) 솔루션을 사용하여 개별 사용자에게 서로 다른 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [미디어 보안 설정](https://msdn.microsoft.com/library/azure/dn282272.aspx)을 참조하세요.

로케이터를 만들 때 Azure Storage의 필수 스토리지 및 전파 프로세스로 인해 30초간 지연될 수 있습니다.

## <a name="adaptive-streaming"></a>적응 스트리밍
적응 비트 전송률 기술에서는 비디오 플레이어 애플리케이션이 네트워크 상태를 확인하고 여러 가지 비트 전송률 중에서 선택할 수 있습니다. 네트워크 통신 성능이 저하되면 클라이언트에서 더 낮은 비트 전송률을 선택하여 더 낮은 비디오 품질로 비디오를 계속 재생하도록 할 수 있습니다. 네트워크 상태가 향상되면 클라이언트에서 더 높은 비트 전송률로 전환하여 향상된 비디오 품질을 제공할 수 있습니다. Azure Media Services는 적응 비트 전송률 기술 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍 및 MPEG-DASH를 지원합니다.

사용자에게 스트리밍 URL을 제공하려면 먼저 OnDemandOrigin 로케이터를 만들어야 합니다. 로케이터를 만들면 스트리밍할 콘텐츠를 포함하는 자산에 대한 기본 경로가 제공됩니다. 그러나 이 콘텐츠를 스트리밍하려면 나중에 이 경로를 수정해야 합니다. 스트리밍 매니페스트 파일에 대한 전체 URL을 생성하려면 로케이터의 경로 값과 매니페스트(filename.ism) 파일 이름을 연결해야 합니다. 그런 다음 로케이터 경로에 **/Manifest** 및 적절한 형식(필요한 경우)을 추가합니다.

> [!NOTE]
> SSL 연결을 통해 콘텐츠를 스트리밍할 수도 있습니다. 이렇게 하려면 스트리밍 URL이 HTTPS로 시작해야 합니다. 현재 AMS는 사용자 지정 도메인을 사용하는 SSL을 지원하지 않습니다.  
> 

콘텐츠를 배달하는 출발점이 될 스트리밍 엔드포인트가 2014년 9월 10일 이후에 만들어진 경우에만 SSL을 통해 스트리밍할 수 있습니다. 스트리밍 URL이 2014년 9월 10일 이후에 만들어진 스트리밍 엔드포인트를 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net"이 포함됩니다. "origin.mediaservices.windows.net"(이전 형식)이 포함된 스트리밍 URL은 SSL을 지원하지 않습니다. URL이 이전 형식인 경우 SSL을 통해 스트리밍할 수 있도록 하려면 새 스트리밍 엔드포인트를 만듭니다. 새 스트리밍 엔드포인트를 기준으로 하는 URL을 사용하여 SSL을 통해 콘텐츠를 스트리밍합니다.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>스트리밍 URL 형식

### <a name="mpeg-dash-format"></a>MPEG-DASH 형식
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HLS(HTTP 라이브 스트리밍) V4 형식
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HLS(HTTP 라이브 스트리밍) V3 형식
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>오디오 전용 필터로 Apple HTTP 라이브 스트리밍(HLS) 포맷
기본적으로 오디오 전용 트랙은 HLS 매니페스트에 포함되어 있습니다. 셀룰러 네트워크에 대한 Apple 스토어 인증이 필요합니다. 이 경우 클라이언트가 충분한 대역폭이 없거나 2G 이상으로 연결되지 않은 경우 재생이 오디오 전용으로 전환됩니다. 이를 통해 콘텐츠 스트리밍이 버퍼링 없이 제공되지만 화면은 표시되지 않습니다. 일부 시나리오에서 오디오 전용 화면보다는 어느 정도 버퍼링이 발생하는 것이 나을 수도 있습니다. 오디오 전용 트랙을 제거하려는 경우 URL에 **audio-only=false** 를 추가합니다.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

자세한 내용은 [동적 매니페스트 컴퍼지션 지원 및 HLS 출력 추가 기능](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)을 참조하세요.

### <a name="smooth-streaming-format"></a>부드러운 스트리밍 형식
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

예제:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>부드러운 스트리밍 2.0 매니페스트(레거시 매니페스트)
기본적으로 부드러운 스트리밍 매니페스트 형식에는 반복 태그(r 태그)가 포함됩니다. 그러나 일부 플레이어에서는 r 태그를 지원하지 않습니다. 이러한 플레이어를 사용하는 클라이언트는 r 태그를 사용하지 않도록 설정하는 형식을 사용할 수 있습니다.

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>점진적 다운로드
점진적 다운로드를 사용하면 전체 파일이 다운로드되기 전에 미디어 재생을 시작할 수 있습니다. .ism*(ismv, isma, ismt, ismc) 파일을 점진적으로 다운로드할 수 없습니다.

콘텐츠를 점진적으로 다운로드하려면 로케이터의 OnDemandOrigin 유형을 사용합니다. 다음 예제에서는 locator.r의 OnDemandOrigin 유형을 기반으로 하는 URL을 보여 줍니다.

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

점진적 다운로드를 위해 원본 서비스에서 스트리밍하려는 저장소 암호화된 자산을 암호 해독해야 합니다.

## <a name="download"></a>다운로드
콘텐츠를 클라이언트 디바이스에 다운로드하려면 SAS 로케이터를 만들어야 합니다. SAS 로케이터를 통해 파일에 있는 Azure Storage 컨테이너에 액세스할 수 있습니다. 다운로드 URL을 작성하려면 호스트와 SAS 서명 사이에 파일 이름을 포함해야 합니다.

다음 예제에서는 SAS 로케이터에 기반한 URL을 보여줍니다.

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

고려 사항은 다음과 같습니다.

* 점진적 다운로드를 위해 원본 서비스에서 스트리밍하려는 저장소 암호화된 자산을 암호 해독해야 합니다.
* 12시간 안에 완료되지 않은 다운로드는 실패합니다.

## <a name="streaming-endpoints"></a>스트리밍 엔드포인트

스트리밍 엔드포인트는 추가 배포를 위해 CDN(콘텐츠 배달 네트워크) 또는 클라이언트 플레이어 애플리케이션에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. 스트리밍 엔드포인트 서비스의 아웃 바운드 스트림은 라이브 스트림 또는 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. 스트리밍 엔드포인트 유형으로는 **표준** 및 **프리미엄** 두 가지가 있습니다. 자세한 내용은 [스트리밍 엔드포인트 개요](media-services-streaming-endpoints-overview.md)를 참조하세요.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 

## <a name="known-issues"></a>알려진 문제
### <a name="changes-to-smooth-streaming-manifest-version"></a>부드러운 스트리밍 매니페스트 버전에 대한 변경 내용
2016년 7월 이전 릴리스에서 미디어 인코더 표준으로 자산이 생성되었고 미디어 인코더 Premium 워크플로 또는 이전 Azure Media Encoder가 동적 패키징을 사용하여 스트리밍된 경우 반환된 부드러운 스트리밍 매니페스트는 버전 2.0을 준수합니다. 버전 2.0에서는 조각 기간 중에 소위 반복('r') 태그를 사용하지 않습니다. 예를 들면 다음과 같습니다.


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

2016년 7월 서비스 릴리스에서는 생성된 부드러운 스트리밍 매니페스트가 버전 2.2를 준수하며 조각 기간에서 반복 태그를 사용합니다. 예를 들면 다음과 같습니다.

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

일부 레거시 부드러운 스트리밍 클라이언트는 반복 태그를 지원하지 않을 수 있으며 매니페스트를 로드하지 못합니다. 이 문제를 완화하기 위해 레거시 매니페스트 형식 매개 변수 **(format=fmp4-v20)** 를 사용하거나 반복 태그를 지원하는 최신 버전으로 클라이언트를 업데이트할 수 있습니다. 자세한 내용은 [부드러운 스트리밍 2.0](media-services-deliver-content-overview.md#fmp4_v20)을 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>관련된 항목
[저장소 키를 롤링 후 Media Services 로케이터를 업데이트합니다.](media-services-roll-storage-access-keys.md)

