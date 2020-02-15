---
title: Azure Media Services v3 릴리스 정보 | Microsoft 문서
description: 최신 개발 정보를 확인할 수 있도록 이 문서에서는 Azure Media Services v3의 최신 업데이트에 대한 정보를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: e5bf99e2ea84f41054ff57d08882bfa8ab4d6be5
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114232"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 릴리스 정보

>이 URL을 복사 하 여 붙여 넣는 방법으로 업데이트에 대 한이 페이지를 다시 방문 하는 시기에 대 한 알림 받기: RSS 피드 판독기에 `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` 합니다.

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="known-issues"></a>알려진 문제

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI 또는 지원되는 SDK 중 하나를 사용하세요.

자세한 내용은 [Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md#known-issues)을 참조하세요.
 
## <a name="january-2020"></a>2020년 1월

### <a name="improvements-in-media-processors"></a>미디어 프로세서의 향상 된 기능

- 비디오 분석에서 인터레이스 소스에 대 한 향상 된 지원 – 이러한 콘텐츠는 이제 유추 엔진으로 전송 되기 전에 올바르게 인터레이스 되지 않습니다.
- "최상" 모드로 미리 보기를 생성할 때 인코더는 이제 30 초 이상 검색 하 여 단색이 아닌 프레임을 선택 합니다.

### <a name="azure-government-cloud-updates"></a>Azure Government 클라우드 업데이트

다음 Azure Government 지역에서 GA'ed을 Media Services. *미국 정부 애리조나* 및 *미국 정부 텍사스*.

## <a name="december-2019"></a>2019년 12월

라이브 및 비디오 주문형 스트리밍 모두에 대해 *원본 지원 프리페치* 헤더에 대 한 CDN 지원이 추가 되었습니다. Akamai CDN을 사용 하 여 직접 계약을 체결 한 고객에 게 제공 됩니다. 원본-지원 CDN-프리페치 기능은 Akamai CDN과 Azure Media Services 원본 간의 다음 HTTP 헤더 교환을 포함 합니다.

|HTTP 헤더|값|보낸 사람|받는 사람|목적|
| ---- | ---- | ---- | ---- | ----- |
|CDN-원본-지원-프리페치-사용 | 1 (기본값) 또는 0 |CDN|원본|CDN에서 프리페치를 사용 하도록 지정 하려면|
|CDN-지원-프리페치-경로| 예제: <br/>조각 (video = 1400000000, format = mpd-cmaf)|원본|CDN|CDN에 프리페치 경로를 제공 하려면|
|CDN-원본-지원-프리페치-요청|1 (프리페치 요청) 또는 0 (일반 요청)|CDN|원본|CDN의 요청이 프리페치 임을 나타내려면|

작업 중인 헤더 exchange의 일부를 확인 하려면 다음 단계를 수행 합니다.

1. Postman 또는 말아를 사용 하 여 오디오 또는 비디오 세그먼트 또는 조각에 대 한 Media Services 원본에 요청을 발급 합니다. 요청에 CDN-원본-지원-프리페치 사용: 1 헤더를 추가 해야 합니다.
2. 응답에는 상대 경로를 해당 값으로 사용 하는 헤더 (CDN-원본-지원-프리페치 경로)가 표시 됩니다.

## <a name="november-2019"></a>2019년 11월

### <a name="live-transcription-preview"></a>라이브 기록 미리 보기

실시간 기록은 현재 공개 미리 보기로 제공 되며 미국 서 부 2 지역에서 사용할 수 있습니다.

라이브 기록은 라이브 이벤트와 함께 추가 기능으로 작동 하도록 설계 되었습니다.  통과와 표준 또는 프리미엄 인코딩 라이브 이벤트 모두에서 지원 됩니다.  이 기능을 사용 하는 경우 서비스는 Cognitive Services의 [음성 텍스트](../../cognitive-services/speech-service/speech-to-text.md) 기능을 사용 하 여 들어오는 오디오의 음성 단어를 텍스트로 높여줄 합니다. 그런 다음이 텍스트는 MPEG-2 및 HLS 프로토콜의 비디오 및 오디오와 함께 배달할 수 있게 됩니다. 청구는 "실행 중" 상태인 라이브 이벤트의 추가 비용 인 새 추가 기능 측정기를 기준으로 합니다.  실시간 기록 및 대금 청구에 대 한 자세한 내용은 [라이브](live-transcription.md) 기록을 참조 하세요.

> [!NOTE]
> 현재 라이브 기록은 미국 서 부 2 지역 에서만 미리 보기 기능으로 사용할 수 있습니다. 이번에는 영어 (en-us)의 음성 단어 기록을 지원 합니다.

### <a name="content-protection"></a>콘텐츠 보호

9 월에 다시 제한 된 지역에서 릴리스된 *토큰 재생 방지* 기능을 이제 모든 지역에서 사용할 수 있습니다.
이제 고객이 동일한 토큰을 사용 하 여 키 또는 라이선스를 요청 하는 횟수에 대 한 제한을 설정할 수 Media Services. 자세한 내용은 [토큰 재생 방지](content-protection-overview.md#token-replay-prevention)를 참조 하세요.

### <a name="new-recommended-live-encoder-partners"></a>새 권장 라이브 인코더 파트너

RTMP 라이브 스트리밍에 대 한 다음과 같은 새로운 권장 파트너 인코더에 대 한 지원이 추가 되었습니다.

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 및 최대 작업 카메라](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>파일 인코딩 기능 향상

- 이제 새로운 콘텐츠 인식 인코딩 사전 설정을 사용할 수 있습니다. 콘텐츠 인식 인코딩을 사용 하 여 GOP 정렬 Mp4 집합을 생성 합니다. 입력 콘텐츠가 지정 된 경우 서비스는 입력 콘텐츠에 대 한 초기 경량 분석을 수행 합니다. 이러한 결과를 사용 하 여 적응 스트리밍이 배달할 최적의 계층 수, 적절 한 비트 전송률 및 해상도 설정을 결정 합니다. 이 사전 설정은 덜 복잡 하 고 복잡성이 높은 비디오에 특히 효과적입니다 .이 비디오에서는 출력 파일이 더 낮은 비트 전송률이 고 여전히 좋은 경험을 제공 하는 품질로 제공 됩니다. 출력에는 비디오 및 오디오가 인터리브 된 MP4 파일이 포함 됩니다. 자세한 내용은 [OPEN API 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)을 참조 하십시오.
- Media Encoder Standard의 sizer에 대 한 향상 된 성능 및 다중 스레딩 특정 조건에서 고객은 5-40% VOD 인코딩 사이의 성능 향상을 확인 해야 합니다. 여러 비트 전송률로 인코드된 복잡성이 낮은 콘텐츠가 있으면 성능이 가장 높습니다. 
- 이제 표준 인코딩은 시간 기반 GOP 설정을 사용 하는 경우 VOD 인코딩에 대해 VFR (가변 프레임 요금) 콘텐츠에 대 한 일반 GOP 주기를 유지 관리 합니다.  즉, 15-30 fps 마다 다른 혼합 프레임 속도 콘텐츠를 제출 하는 고객이 적응 비트 전송률 스트리밍 MP4 파일에 대 한 출력에서 계산 되는 일반적인 GOP 거리가 표시 됩니다. 이렇게 하면 HLS 또는 대시를 통해 배달할 때 트랙 간에 원활 하 게 전환 하는 기능이 향상 됩니다. 
-  VFR (가변 프레임 전송률) 원본 콘텐츠에 대해 향상 된 AV 동기화

### <a name="video-indexer-video-analytics"></a>Video Indexer, 비디오 분석

- 이제 VideoAnalyzer 사전 설정을 사용 하 여 추출 된 키프레임이 크기가 조정 되는 대신 비디오의 원래 해상도로 설정 됩니다. 고해상도 키 프레임 추출은 원본 품질 이미지를 제공 하 고 Microsoft Computer Vision 및 Custom Vision 서비스에서 제공 하는 이미지 기반 인공 지능 모델을 사용 하 여 비디오에서 더 많은 정보를 얻을 수 있도록 합니다.

## <a name="september-2019"></a>2019년 9월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>라이브 이벤트의 라이브 선형 인코딩

Media Services v3은 라이브 이벤트의 라이브 선형 인코딩의 24 시간 x 365 일 미리 보기를 발표 하 고 있습니다.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>미디어 프로세서의 사용 중단

*Azure Media Indexer* 및 *Azure Media Indexer 2 Preview*의 사용 중단을 발표 하 고 있습니다. 사용 중지 날짜는 [레거시 구성 요소](../previous/legacy-components.md) 항목을 참조 하세요. [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) 이러한 레거시 미디어 프로세서를 대체 합니다.

자세한 내용은 [Azure Media Indexer에서 마이그레이션 및 Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션](../previous/migrate-indexer-v1-v2.md)을 참조 하세요.

## <a name="august-2019"></a>2019년 8월

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Media Services에 대 한 동남 공화국 지역 쌍이 열려 있습니다. 

현재 남아프리카 북부와 남아프리카 공화국 지역에서 Media Services를 사용할 수 있습니다.

자세한 내용은 [Media Services v3이 존재 하는 클라우드 및 지역](azure-clouds-regions.md)을 참조 하세요.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>미디어 프로세서의 사용 중단

사용이 중지 된 wame ( *Windows Azure Media Encoder* ) 및 *Azure Media Encoder* (AME) 미디어 프로세서의 사용 중단을 발표 하 고 있습니다. 사용 중지 날짜는이 [레거시 구성 요소](../previous/legacy-components.md) 항목을 참조 하세요.

자세한 내용은 [WAME to Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101334) 및 [AME를 Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101335)을 참조 하세요.
 
## <a name="july-2019"></a>2019년 7월

### <a name="content-protection"></a>콘텐츠 보호

토큰 제한으로 보호 된 콘텐츠를 스트리밍하는 경우 최종 사용자는 키 배달 요청의 일부로 전송 되는 토큰을 가져와야 합니다. *토큰 재생 방지* 기능을 사용 하면 고객이 동일한 토큰을 사용 하 여 키 또는 라이선스를 요청 하는 횟수에 대 한 제한을 설정할 수 Media Services 있습니다. 자세한 내용은 [토큰 재생 방지](content-protection-overview.md#token-replay-prevention)를 참조 하세요.

7 월부터 미리 보기 기능은 미국 중부 및 미국 서 부 중부 에서만 사용할 수 있었습니다.

## <a name="june-2019"></a>2019년 6월

### <a name="video-subclipping"></a>비디오 하위 클리핑

이제 [작업](https://docs.microsoft.com/rest/api/media/jobs)을 사용 하 여 인코딩할 때 비디오를 자르거나 하위 클립 할 수 있습니다. 

이 기능은 [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용 하 여 작성 된 모든 [변환](https://docs.microsoft.com/rest/api/media/transforms) 에 적용 됩니다. 

예를 참조 하세요.

* [.NET을 사용 하 여 비디오 하위 클립](subclip-video-dotnet-howto.md)
* [REST를 사용 하 여 비디오 하위 클립](subclip-video-rest-howto.md)

## <a name="may-2019"></a>2019년 5월

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Media Services 진단 로그 및 메트릭에 대 한 Azure Monitor 지원

이제 Azure Monitor를 사용 하 여 Media Services에서 내보낸 원격 분석 데이터를 볼 수 있습니다.

* Azure Monitor 진단 로그를 사용 하 여 Media Services 키 배달 끝점에서 보낸 요청을 모니터링 합니다. 
* Media Services [스트리밍 끝점](streaming-endpoint-concept.md)에서 내보낸 메트릭을 모니터링 합니다.   

자세한 내용은 [Media Services 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md)을 참조 하세요.

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>동적 패키징에서 지 원하는 다중 오디오 트랙 

여러 코덱 및 언어를 사용 하는 여러 오디오 트랙이 있는 자산을 스트리밍하는 경우 [동적 패키징](dynamic-packaging-overview.md) 은 이제 HLS 출력 (버전 4 이상)에 대 한 다중 오디오 트랙을 지원 합니다.

### <a name="korea-regional-pair-is-open-for-media-services"></a>대한민국 지역 쌍이 Media Services에 대해 열려 있습니다. 

Media Services는 대한민국 중부 및 대한민국 남부 지역에서 사용할 수 있습니다. 

자세한 내용은 [Media Services v3이 존재 하는 클라우드 및 지역](azure-clouds-regions.md)을 참조 하세요.

### <a name="performance-improvements"></a>성능 향상

Media Services 성능 개선을 포함 하는 업데이트를 추가 했습니다.

* 처리를 위해 지원 되는 최대 파일 크기가 업데이트 되었습니다. , [할당량 및 제한 사항](limits-quotas-constraints.md)을 참조 하세요.
* [인코딩 속도 향상](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>2019년 4월

### <a name="new-presets"></a>새 사전 설정

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) 가 기본 제공 분석기 사전 설정에 추가 되었습니다.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) 가 기본 제공 인코더 사전 설정에 추가 되었습니다. 자세한 내용은 [콘텐츠 인식 인코딩](content-aware-encoding.md)을 참조 하세요. 

## <a name="march-2019"></a>2019년 3월

동적 패키징은 이제 돌비 Atmos을 지원 합니다. 자세한 내용은 [동적 패키징에서 지 원하는 오디오 코덱](dynamic-packaging-overview.md#audio-codecs)을 참조 하세요.

이제 스트리밍 로케이터에 적용 되는 자산 또는 계정 필터 목록을 지정할 수 있습니다. 자세한 내용은 [스트리밍 로케이터를 사용 하 여 필터 연결](filters-concept.md#associating-filters-with-streaming-locator)을 참조 하세요.

## <a name="february-2019"></a>2019년 2월

Media Services v3은 이제 Azure 국가별 클라우드에서 지원 됩니다. 아직은 일부 클라우드에서 일부 기능을 사용할 수 없습니다. 자세한 내용은 [Azure Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

Media Services에 대한 Azure Event Grid 스키마에 [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) 이벤트가 추가되었습니다.

## <a name="january-2019"></a>2019년 1월

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard 및 MPI 파일 

Media Encoder Standard로 인코딩하여 MP4 파일을 생성하는 경우 새 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](dynamic-packaging-overview.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

## <a name="december-2018"></a>2018년 12월

V3 API의 GA 릴리스업데이트에는 다음이 포함됩니다.
       
* **PresentationTimeRange** 속성은 **자산 필터** 및 **계정 필터**에 대해 더 이상 '필수'가 아닙니다. 
* **작업** 및 **변환**의 $top 및 $skip 쿼리 옵션이 제거되었으며 $orderby가 추가되었습니다. 새 순서 지정 기능을 추가되면서, $top 및 $skip 옵션을 이전에 구현하지 않았어도 실수로 노출되는 것으로 확인되었습니다.
* 열거형 확장성을 다시 사용하도록 설정했습니다. 이 기능은 미리 보기 버전의 SDK에서 사용하도록 설정되며 GA 버전에서는 실수로 사용하지 않도록 설정되었습니다.
* 두 개의 미리 정의된 스트리밍 정책 이름이 바뀌었습니다. **SecureStreaming**은 이제 **MultiDrmCencStreaming**입니다. **SecureStreamingWithFairPlay**는 이제 **Predefined_MultiDrmStreaming**입니다.

## <a name="november-2018"></a>2018년 11월

현재 CLI 2.0 모듈이 [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50에 제공됩니다.

### <a name="new-commands"></a>새 명령

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) - 미디어 예약 단위를 관리할 수 있습니다. 자세한 내용은 [미디어 예약 단위 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

### <a name="new-features-and-breaking-changes"></a>새 기능과 주요 변경 내용

#### <a name="asset-commands"></a>자산 명령

- ```--storage-account``` 및 ```--container``` 인수가 추가되었습니다.
- ```az ams asset get-sas-url``` 명령에서 만료 시간(현재+23시간) 및 사용 권한(읽기)에 대한 기본값이 추가되었습니다.

#### <a name="job-commands"></a>작업 명령

- ```--correlation-data``` 및 ```--label``` 인수가 추가되었습니다.
- ```--output-asset-names```의 이름이 ```--output-assets```으로 바뀌었습니다. 이제 'assetName = label' 형식으로 공백으로 구분된 자산 목록을 허용합니다. 레이블이 없는 자산은 ‘assetName=’과 같이 보낼 수 있습니다.

#### <a name="streaming-locator-commands"></a>스트리밍 로케이터 명령

- ```az ams streaming locator``` 기본 명령이 ```az ams streaming-locator```로 바뀌었습니다.
- ```--streaming-locator-id``` 및 ```--alternative-media-id support``` 인수가 추가되었습니다.
- ```--content-keys argument``` 인수가 업데이트되었습니다.
- ```--content-policy-name```의 이름이 ```--content-key-policy-name```으로 바뀌었습니다.

#### <a name="streaming-policy-commands"></a>스트리밍 정책 명령

- ```az ams streaming policy``` 기본 명령이 ```az ams streaming-policy```로 바뀌었습니다.
- ```az ams streaming-policy create```에 암호화 매개 변수 지원이 추가되었습니다.

#### <a name="transform-commands"></a>변환 명령

- ```--preset-names``` 인수가 ```--preset```으로 바뀌었습니다. 이제 한 번에 1개의 출력/사전 설정만 설정할 수 있습니다(더 추가하려면 ```az ams transform output add```를 실행해야 함). 또한 사용자 정의 JSON에 경로를 전달하여 사용자 정의 StandardEncoderPreset을 설정할 수 있습니다.
- 제거할 출력 인덱스를 전달하여 ```az ams transform output remove```를 수행할 수 있습니다.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 및 ```az ams transform create``` 명령에 ```az ams transform output add``` 인수가 추가되었습니다.

## <a name="october-2018---ga"></a>2018년 10월 - GA

이 섹션에서는 AMS(Azure Media Services) 10월 업데이트에 대해 설명합니다.

### <a name="rest-v3-ga-release"></a>REST v3 GA 릴리스

[REST v3 GA 릴리스](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에는 라이브, 계정/자산 수준 매니페스트 필터 및 DRM 지원을 위한 더 많은 API가 포함되어 있습니다.

#### <a name="azure-resource-management"></a>Azure Resource 관리 

Azure Resource 관리가 지원되어 통합 관리 및 API 운영(이제 한 곳에 모든 기능 제공)가 가능합니다.

이 릴리스부터 Resource Manager 템플릿을 사용하여 라이브 이벤트를 만들 수 있습니다.

#### <a name="improvement-of-asset-operations"></a>자산 작업 개선 

다음과 같은 개선 사항이 도입되었습니다.

- HTTP(s) URL 또는 Azure Blob Storage SAS URL에서 수집합니다.
- 자산에 대한 자체 컨테이너 이름을 지정합니다. 
- 보다 쉬운 출력이 지원되어 Azure Functions로 사용자 지정 워크플로를 만들 수 있습니다.

#### <a name="new-transform-object"></a>새로운 변환 개체

새로운 **변환** 개체로 인해 인코딩 모델이 간소화됩니다. 새로운 개체를 통해 Resource Manager 템플릿 및 사전 설정 인코딩을 쉽게 만들고 공유할 수 있습니다. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory 인증 및 RBAC

Azure AD 인증 및 RBAC(역할 기반 액세스 제어)를 통해 Azure AD에서 역할 또는 사용자별로 보안 변환, LiveEvent, 콘텐츠 키 정책 또는 자산을 사용할 수 있습니다.

#### <a name="client-sdks"></a>클라이언트 SDK  

Media Services v3에 지원되는 언어: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>라이브 인코딩 업데이트

다음과 같은 라이브 인코딩 업데이트가 도입되었습니다.

- 라이브에 대한 짧은 대기 시간(엔드투엔드 10초).
- RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
- RTMPS 보안 수집

    라이브 이벤트를 만들면 이제 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다. 
- 24시간 코드 변환 지원 
- SCTE35를 통한 RTMP의 광고 신호 지원 향상

#### <a name="improved-event-grid-support"></a>Event Grid 지원 향상

다음과 같은 Event Grid 지원 향상을 확인할 수 있습니다.

- Azure Event Grid 통합으로 Logic Apps 및 Azure Functions를 사용하여 보다 쉽게 개발할 수 있습니다. 
- 인코딩, 라이브 채널 등의 이벤트를 구독하세요.

### <a name="cmaf-support"></a>CMAF 지원

CMAF를 지원하는 Apple HLS(iOS 11+) 및 MPEG-DASH 플레이어에 대해 CMAF 및 'cbcs' 암호화가 지원됩니다.

### <a name="video-indexer"></a>비디오 인덱서

Video Indexer GA 릴리스가 8월에 발표되었습니다. 현재 지원되는 기능에 대한 자세한 내용은 [Video Indexer란?](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json)을 참조하세요. 

### <a name="plans-for-changes"></a>변경 계획

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
라이브, 콘텐츠 키 정책, 계정/자산 필터, 스트리밍 정책을 비롯한 모든 기능에 대한 작업을 포함하고 있는 Azure CLI 2.0 모듈이며, 곧 출시될 예정입니다. 

### <a name="known-issues"></a>알려진 문제

Asset 또는 AccountFilters에 대한 API 미리 보기를 사용하는 고객만 다음 문제의 영향을 받습니다.

Media Services v3 CLI 또는 API를 통해 9월 28일에서 10월 12일 사이에 자산 또는 계정 필터를 생성한 경우에는 버전 충돌로 인해 모든 자산 및 계정 필터를 제거하고 다시 생성해야 합니다. 

## <a name="may-2018---preview"></a>2018년 5월 - 미리 보기

### <a name="net-sdk"></a>.NET SDK

.NET SDK에는 다음과 같은 기능이 포함 되어 있습니다.

* 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial-with-api.md)을 참조하세요.
* 최종 사용자 디바이스에 콘텐츠를 게시하고 스트리밍하는 **스트리밍 로케이터**.
* 콘텐츠를 전송할 때 키 전송 및 콘텐츠 보호(DRM)를 구성하는 **스트리밍 정책** 및 **콘텐츠 키 정책**.
* 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **라이브 이벤트** 및 **라이브 스트리밍**.
* Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
* 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성하고 확장하는 **스트리밍 엔드포인트**.

### <a name="known-issues"></a>알려진 문제

* 작업을 제출할 때는 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일의 경로를 사용하여 원본 비디오를 수집하도록 지정할 수 있습니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [개요](media-services-overview.md)
- [Media Services v2 릴리스 정보](../previous/media-services-release-notes.md)
