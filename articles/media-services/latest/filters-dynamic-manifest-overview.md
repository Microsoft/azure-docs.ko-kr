---
title: 동적 포장기를 사용 하 여 매니페스트 필터링
titleSuffix: Azure Media Services
description: 동적 패키지를 사용 하 여 필터를 만들고 선택적으로 매니페스트를 스트리밍하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 4a2464cbb47291f35cd4d7e20b555356e3e9aa8b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266462"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>동적 포장기를 사용 하 여 매니페스트 필터링

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

적응 비트 전송률 스트리밍 콘텐츠를 장치에 배달 하는 경우 특정 장치 기능 또는 사용 가능한 네트워크 대역폭을 대상으로 지정 하기 위해 여러 버전의 매니페스트를 게시 해야 하는 경우가 있습니다. [동적](dynamic-packaging-overview.md) 패키지 작성 도구를 사용 하면 특정 코덱, 해상도, 비트 전송률 및 오디오 트랙 조합을 즉석에서 필터링 할 수 있는 필터를 지정할 수 있습니다. 이 필터링을 통해 여러 복사본을 만들 필요가 없습니다. 대상 장치 (iOS, Android, SmartTV 또는 브라우저) 및 네트워크 기능 (고대역폭, 모바일 또는 저대역폭 시나리오)에 구성 된 특정 필터 집합을 사용 하 여 새 URL을 게시 하기만 하면 됩니다. 이 경우 클라이언트는 사용 가능한 [자산 필터 또는 계정 필터](filters-concept.md)를 지정 하 여 쿼리 문자열을 통해 콘텐츠의 스트리밍을 조작 하 고 필터를 사용 하 여 스트림의 특정 섹션을 스트리밍할 수 있습니다.

일부 배달 시나리오에서는 고객이 특정 트랙에 액세스할 수 없는지 확인 해야 합니다. 예를 들어 HD 트랙을 포함 하는 매니페스트를 특정 구독자 계층에 게시 하지 않을 수 있습니다. 또는 특정 장치에 대 한 배달 비용을 줄이고 추가 트랙의 이점을 활용 하지 않으려는 특정 ABR (적응 비트 전송률) 트랙을 제거 하려고 할 수 있습니다. 이 경우 미리 만든 필터 목록을 만들 때 [스트리밍 로케이터](streaming-locators-concept.md) 와 연결할 수 있습니다. 그러면 클라이언트는 **스트리밍 로케이터**에 의해 정의 되므로 콘텐츠가 스트리밍되는 방식을 조작할 수 없습니다.

클라이언트에서 URL에 지정 하는 추가 장치별 필터와 [스트리밍 로케이터에 대 한 필터](filters-concept.md#associating-filters-with-streaming-locator) 를 지정 하 여 필터링을 결합할 수 있습니다. 이 조합은 메타 데이터 또는 이벤트 스트림, 오디오 언어 또는 설명 오디오 트랙과 같은 추가 트랙을 제한 하는 데 유용 합니다.

스트림에 다른 필터를 지정 하는이 기능은 대상 장치에 대 한 여러 사용 사례 시나리오를 대상으로 하는 강력한 **동적 매니페스트** 조작 솔루션을 제공 합니다. 이 항목에서는 **동적 매니페스트와** 관련 된 개념을 설명 하 고이 기능을 사용할 수 있는 시나리오의 예를 제공 합니다.

> [!NOTE]
> 동적 매니페스트는 해당 자산의 기본 매니페스트와 자산 및 자산을 변경 하지 않습니다.

## <a name="overview-of-manifests"></a>매니페스트 개요

Azure Media Services HLS, MPEG 대시 및 부드러운 스트리밍 프로토콜을 지원 합니다. [동적 패키징을](dynamic-packaging-overview.md)사용 하는 경우 스트리밍 클라이언트 매니페스트 (HLS 마스터 재생 목록, 대시 미디어 프레젠테이션 설명 [MPD] 및 부드러운 스트리밍)는 URL의 형식 선택기에 따라 동적으로 생성 됩니다. 자세한 내용은 [일반적인 주문형 워크플로의](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)배달 프로토콜을 참조 하세요.

### <a name="get-and-examine-manifest-files"></a>매니페스트 파일 가져오기 및 검사

동적으로 생성 된 매니페스트에 포함 되어야 하는 스트림 트랙 (라이브 또는 주문형 비디오 [VOD])에 따라 필터 추적 속성 조건의 목록을 지정 합니다. 트랙의 속성을 가져오고 검사하려면 먼저 부드러운 스트리밍 매니페스트를 로드해야 합니다.

[.Net으로 파일 업로드, 인코딩 및](stream-files-tutorial-with-api.md#get-streaming-urls) 스트리밍 자습서에서는 .net을 사용 하 여 스트리밍 url을 빌드하는 방법을 보여 줍니다. 앱을 실행하는 경우 URL 중 하나가 부드러운 스트리밍 매니페스트를 가리킵니다. `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/> URL을 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 파일이 다운로드됩니다. 텍스트 편집기에서 열 수 있습니다.

REST 예제를 보려면 REST를 [사용 하 여 파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)을 참조 하세요.

### <a name="monitor-the-bitrate-of-a-video-stream"></a>비디오 스트림의 비트 전송률 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer)를 사용하여 비디오 스트림의 비트 전송률을 모니터링할 수 있습니다. 데모 페이지는 **진단 탭에 진단 정보를 표시** 합니다.

![Azure Media Player 진단][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>예: 쿼리 문자열의 필터를 사용 하는 Url

HLS, MPEG, 부드러운 스트리밍 등의 ABR 스트리밍 프로토콜에 필터를 적용할 수 있습니다. 다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>변환 필터링

자산을 여러 인코딩 프로필 (h.264 기준선, h.264 High, AACL, AACH, 돌비 Digital Plus) 및 여러 품질 비트 전송률로 인코딩할 수 있습니다. 그러나 모든 클라이언트 디바이스가 모든 자산 프로필 및 비트 전송률을 지원하는 것은 아닙니다. 예를 들어 이전 Android 장치는 H. 264 기준선 + AACL만 지원 합니다. 혜택을 받을 수 없는 장치에 더 높은 비트 전송률을 전송 하면 대역폭과 장치 계산이 낭비 됩니다. 이러한 장치는 제공 된 모든 정보를 디코딩하고 표시를 위해 규모를 축소 해야 합니다.

동적 매니페스트를 사용 하면 모바일, 콘솔 또는 HD/SD와 같은 장치 프로필을 만들고 각 프로필에 포함 하려는 트랙과 품질을 포함할 수 있습니다. 이를 변환 필터링 이라고 합니다. 다음 다이어그램에서는이에 대 한 예를 보여 줍니다.

![동적 매니페스트를 사용 하 여 변환 필터링의 예][renditions2]

다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 HLS, MPEG 대시 및 부드러운 스트리밍 프로토콜 중 하나로 [동적으로 패키지할](dynamic-packaging-overview.md) 수 있습니다.

다음 다이어그램의 맨 위에는 필터가 없는 자산의 HLS 매니페스트가 표시 됩니다. 여기에는 7 개의 변환이 모두 포함 됩니다.  왼쪽 아래에는 "이상" 이라는 필터가 적용 된 HLS 매니페스트가 나와 있습니다. "이상" 필터는 1 Mbps 미만의 모든 비트 전송률 제거를 지정 하 여 응답에서 하위 두 품질 수준이 제거 되었습니다. 오른쪽 아래 다이어그램에는 "mobile" 필터가 적용 된 HLS 매니페스트가 나와 있습니다. "Mobile" 필터는 해상도가 720p 보다 큰 변환의 제거를 지정 하므로 1080p 변환 두 개가 제거 되었습니다.

![동적 매니페스트를 사용 하 여 변환 필터링][renditions1]

## <a name="removing-language-tracks"></a>언어 트랙 제거
자산에는 영어, 스페인어, 프랑스어 등의 여러 오디오 언어가 포함 될 수 있습니다. 일반적으로 플레이어 SDK는 사용자 선택 당 기본 오디오 트랙 선택 및 사용 가능한 오디오 트랙을 관리 합니다.

이러한 플레이어 Sdk를 개발 하는 것은 장치별 플레이어 프레임 워크에서 다른 구현이 필요 하기 때문에 어렵습니다. 또한 일부 플랫폼에서 플레이어 Api는 제한적 이며, 사용자가 기본 오디오 트랙을 선택 하거나 변경할 수 없는 오디오 선택 기능을 포함 하지 않습니다. 자산 필터를 사용 하 여 원하는 오디오 언어만 포함 하는 필터를 만들어 동작을 제어할 수 있습니다.

![동적 매니페스트를 사용 하 여 언어 트랙 필터링][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>자산의 시작 트리밍

대부분의 라이브 스트리밍 이벤트에서 운영자는 실제 이벤트 전에 일부 테스트를 실행합니다. 예를 들어 이벤트를 시작 하기 전에 다음과 같은 슬레이트를 포함할 수 있습니다. "프로그램이 일시적으로 시작 됩니다."

프로그램을 보관하는 경우 테스트 및 슬레이트 데이터도 보관되며 프레젠테이션에도 포함됩니다. 그러나이 정보는 클라이언트에 표시 되지 않습니다. 동적 매니페스트를 사용하면 시작 시간 필터를 만들고 매니페스트에서 원치 않는 데이터를 제거할 수 있습니다.

![동적 매니페스트를 사용 하 여 자산의 시작 트리밍][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>라이브 보관에서 하위 클립(뷰) 만들기

많은 라이브 이벤트가 장시간 실행되며 라이브 보관에는 여러 이벤트가 포함될 수 있습니다. 라이브 이벤트가 끝난 후 브로드캐스터는 라이브 보관을 논리 프로그램 시작 및 중단 시퀀스로 구분하려고 할 수 있습니다.

별도의 자산을 만들지 않고 별도의 자산을 만들지 않고, 이러한 가상 프로그램을 개별적으로 게시할 수 있습니다 .이는 CDNs에서 캐시 된 기존 조각의 이점을 얻지 못합니다. 이러한 가상 프로그램의 예로는 축구 또는 농구 게임의 쿼터, 야구의 이닝 또는 스포츠 프로그램의 개별 행사 등이 있습니다.

동적 매니페스트를 사용 하면 시작/종료 시간을 사용 하 여 필터를 만들고 라이브 아카이브 위에 가상 뷰를 만들 수 있습니다.

![동적 매니페스트를 사용 하 여 하위 클립 필터][subclip_filter]

필터링 된 자산은 다음과 같습니다.

![동적 매니페스트를 사용 하 여 필터링 된 자산][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>프레젠테이션 창 (DVR) 조정

현재 Azure Media Services는 기간을 1 분에서 25 시간 사이로 구성할 수 있는 순환 보관을 제공 합니다. 매니페스트 필터링을 사용하면 미디어를 삭제하지 않고도 보관 위에 롤링 DVR 창을 만들 수 있습니다. 브로드캐스터가 라이브 에지와 함께 이동하는 제한된 DVR 창을 제공하는 동시에 더 큰 보관 창을 유지하는 등의 여러 시나리오가 있습니다. 브로드캐스터는 DVR 창의 데이터를 사용 하 여 클립을 강조 표시 하거나 다양 한 장치에 다른 DVR 창을 제공 하고자 할 수 있습니다. 예를 들어 대부분의 모바일 디바이스는 큰 DVR 창을 처리하지 못합니다. 모바일 디바이스의 경우 2분, 데스크톱 클라이언트의 경우 1시간 동안 DVR 창을 표시할 수 있습니다.

![동적 매니페스트를 사용 하는 DVR 창][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff(라이브 위치) 조정

매니페스트 필터링을 사용하면 라이브 프로그램의 라이브 에지에서 몇 초 정도 제거할 수 있습니다. 필터링을 통해 방송사는 미리 보기 게시 지점에서 프레젠테이션을 감시 하 고, 뷰어가 스트림을 받기 전에 광고 삽입 지점을 만들 수 있습니다 (30 초 동안 지원). 그러면 방송사는 보급 알림 기회 전에 정보를 수신 하 고 처리 하기 위해 이러한 보급 알림을 클라이언트 프레임 워크에 푸시할 수 있습니다.

광고 지원 외에도 라이브 백오프 설정을 사용 하 여 뷰어의 위치를 조정 하 여 클라이언트가 라이브에 지를 드리프트 하 고 적중 하는 경우 서버에서 조각을 가져올 수 있습니다. 이렇게 하면 클라이언트에서 HTTP 404 또는 412 오류가 발생 하지 않습니다.

![동적 매니페스트를 사용 하 여 라이브 백오프를 필터링 합니다.][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>단일 필터에 여러 규칙 결합

단일 필터에 여러 필터링 규칙을 결합할 수 있습니다. 예를 들어 "범위 규칙"을 정의 하 여 라이브 보관에서 슬레이트을 제거 하 고 사용 가능한 비트 전송률을 필터링 할 수도 있습니다. 여러 필터링 규칙을 적용 하는 경우 최종 결과는 모든 규칙의 교집합입니다.

![동적 매니페스트를 사용 하는 여러 필터링 규칙][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>여러 개의 필터 결합(필터 컴퍼지션)

단일 URL로 여러 개의 필터를 결합할 수도 있습니다. 다음 시나리오는 필터를 결합하는 이유를 보여 줍니다.

1. 비디오 품질을 제한 하기 위해 Android 또는 iPad와 같은 모바일 장치에 대 한 비디오 품질을 필터링 해야 합니다. 원치 않는 품질을 제거 하기 위해 장치 프로필에 적합 한 계정 필터를 만듭니다. 추가 연결 없이 동일한 Media Services 계정에서 모든 자산에 대 한 계정 필터를 사용할 수 있습니다.
1. 또한 자산의 시작 및 종료 시간을 트리밍하려고 합니다. 트리밍을 수행 하려면 자산 필터를 만들고 시작/종료 시간을 설정 합니다.
1. 두 필터를 결합 하려고 합니다. 조합이 없으면 트리밍 필터에 품질 필터링을 추가 해야 합니다. 그러면 필터 사용이 더 어려워집니다.

필터를 결합 하려면 필터 이름을 세미콜론으로 구분 된 형식으로 매니페스트/재생 목록 URL로 설정 합니다. 품질을 필터링 하는 *필터링* 라는 필터가 있고 특정 시작 시간을 설정 하는 *mystarttime* 이라는 다른를 사용 하 고 있다고 가정 합니다. 최대 3개의 필터를 결합할 수 있습니다.

자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)을 참조하세요.

## <a name="considerations-and-limitations"></a>고려 사항 및 제한 사항

- VOD 필터에 대해 **Forceendtimestamp**, **presentationWindowDuration**및 **liveBackoffDuration** 값을 설정 하면 안 됩니다. 라이브 필터 시나리오에만 사용 됩니다.
- 동적 매니페스트는 GOP 경계 (키 프레임)에서 작동 하므로 트리밍에는 GOP 정확도가 있습니다.
- 계정 및 자산 필터에 동일한 필터 이름을 사용할 수 있습니다. 자산 필터는 우선 순위가 높고 계정 필터를 재정의 합니다.
- 필터를 업데이트 하는 경우 스트리밍 끝점이 규칙을 새로 고치는 데 최대 2 분이 걸릴 수 있습니다. 필터를 사용 하 여 콘텐츠를 제공 하 고 프록시와 CDN 캐시에서 콘텐츠를 캐시 한 경우 이러한 필터를 업데이트 하면 플레이어 오류가 발생할 수 있습니다. 필터를 업데이트 한 후에는 캐시를 지우는 것이 좋습니다. 이 옵션을 사용할 수 없는 경우 다른 필터를 사용 하는 것이 좋습니다.
- 고객은 수동으로 매니페스트를 다운로드 하 고 정확한 시작 타임 스탬프 및 시간 단위를 구문 분석 해야 합니다.

    - 자산에서 트랙의 속성을 확인 하려면 [매니페스트 파일을 가져오고 검사](#get-and-examine-manifest-files)합니다.
    - 자산 필터 타임 스탬프 속성을 설정 하는 수식은 다음과 같습니다. <br/>starttimestamp = &lt; 매니페스트의 시작 시간 &gt;  +   &lt; (초) * 시간 간격으로 예상 된 필터 시작 시간 &gt;

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 보여 줍니다.  

- [REST API를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
