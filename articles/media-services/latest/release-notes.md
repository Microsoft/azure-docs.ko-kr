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
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 09f9731f78093354f324f263899519bc5d31fa5d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760771"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 릴리스 정보

최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="known-issues"></a>알려진 문제

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI 또는 지원되는 SDK 중 하나를 사용하세요.

자세한 내용은 [Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](migrate-from-v2-to-v3.md#known-issues)을 참조하세요.

## <a name="april-2019"></a>2019 년 4 월

[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) 기본 제공 분석기 미리 설정에 추가 되었습니다.

## <a name="march-2019"></a>2019 년 3 월

Dolby Atmos. 지원 이제 동적 패키징 자세한 내용은 [오디오 코덱 동적 패키징에서 지원](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)합니다.

이제 스트리밍 로케이터가 만들어집니다를 적용할 자산 또는 계정 필터 목록을 지정할 수 있습니다. 자세한 내용은 [스트리밍 로케이터를 사용 하 여 필터를 연결](filters-concept.md#associate-filters-with-streaming-locator)합니다.

## <a name="february-2019"></a>2019년 2월

Media Services v3 이제 국가별 클라우드 Azure에서 지원 됩니다. 아직은 일부 클라우드에서 일부 기능을 사용할 수 없습니다. 자세한 내용은 [Azure Media Services v3가 있는 클라우드 및 지역](azure-clouds-regions.md)을 참조하세요.

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
- ```az ams transform create``` 및 ```az ams transform output add``` 명령에 ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` 인수가 추가되었습니다.

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

- 라이브에 대한 짧은 대기 시간(종단간 10초)
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

.NET SDK의 다음 기능은 다음과 같습니다.

* 미디어 콘텐츠를 인코딩하거나 분석할 수 있는 **Transform** 및 **Job**. 예를 들어 [스트림 파일](stream-files-tutorial-with-api.md) 및 [분석](analyze-videos-tutorial-with-api.md)을 참조하세요.
* 최종 사용자 디바이스에 콘텐츠를 게시하고 스트리밍하는 **스트리밍 로케이터**.
* 콘텐츠를 전송할 때 키 전송 및 콘텐츠 보호(DRM)를 구성하는 **스트리밍 정책** 및 **콘텐츠 키 정책**.
* 라이브 스트리밍 콘텐츠의 수집 및 보관을 구성하는 **라이브 이벤트** 및 **라이브 스트리밍**.
* Azure Storage에 미디어 콘텐츠를 저장하고 게시하는 **Asset**. 
* 실시간 및 주문형 미디어 콘텐츠에 대한 동적 패키징, 암호화 및 스트리밍을 구성하고 확장하는 **스트리밍 엔드포인트**.

### <a name="known-issues"></a>알려진 문제

* 작업을 제출할 때는 HTTPS URL, SAS URL 또는 Azure Blob Storage에 있는 파일의 경로를 사용하여 원본 비디오를 수집하도록 지정할 수 있습니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[개요](media-services-overview.md)
