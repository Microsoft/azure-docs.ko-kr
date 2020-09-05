---
title: Azure Media Services 릴리스 정보 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure Media Services v2 릴리스 정보에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 7764452d0e52a29204b276b4939c4a8a5c144ca4
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268658"
---
# <a name="azure-media-services-release-notes"></a>Azure Media Services 릴리스 정보

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services에 대한 이 릴리스 정보에는 이전 릴리스 이후의 변경 내용과 알려진 문제가 요약되어 있습니다.

> [!NOTE]
> Media Services v2에는 새로운 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Azure 팀은 고객의 의견을 수렴하여 고객에게 영향을 주는 문제를 해결하기 위해 노력하고 있습니다. 문제를 보고하거나 질문이 있는 경우 [Azure Media Services MSDN 포럼]에서 게시물을 제출하세요. 

## <a name="known-issues"></a><a name="issues"></a>알려진 문제
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Media Services 관련 일반 문제

| 문제 | Description |
| --- | --- |
| REST API에 다양한 일반 HTTP 헤더가 제공되지 않습니다. |REST API를 사용하여 Media Services 애플리케이션을 개발하는 경우 CLIENT-REQUEST-ID, REQUEST-ID, RETURN-CLIENT-REQUEST-ID를 비롯한 몇 가지 일반 HTTP 헤더 필드가 지원되지 않습니다. 이 헤더는 이후 업데이트에서 추가될 예정입니다. |
| 퍼센트 인코딩은 허용되지 않습니다. |Media Services는 스트리밍 콘텐츠의 URL을 작성할 때 속성의 값을 사용합니다(예: `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. 이름 속성 값에는 !* '();:@&=+$,/?%#[]"와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 "." 하나만 사용할 수 있습니다. |
| Azure Storage SDK 버전 3.x의 일부분인 ListBlobs 메서드에서 오류가 발생합니다. |Media Services에서는 [2012-02-12](/rest/api/storageservices/version-2012-02-12) 버전을 기반으로 SAS URL을 생성합니다. Storage SDK를 사용하여 Blob 컨테이너의 Blob을 나열하려는 경우 Storage SDK 버전 2.x에 포함된 [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) 메서드를 사용합니다. |
| Media Services 제한 메커니즘은 서비스에 과도한 요청을 보내는 애플리케이션의 리소스 사용을 제한합니다. 해당 서비스에서 "서비스를 사용할 수 없음" 503 HTTP 상태 코드를 반환할 수 있습니다. |자세한 내용은 [Media Services 오류 코드](media-services-encoding-error-codes.md)에서 503 HTTP 상태 코드 설명을 참조하세요. |
| 엔터티를 쿼리할 때 한 번에 반환되는 엔터티 수의 제한은 1,000개입니다. 공용 REST 버전 2에서는 쿼리 결과를 1,000개로 제한하기 때문입니다. |[이 .NET 예제](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) 및 [이 REST API 예제](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)에 설명된 대로 건너뛰기 및 포함(.NET)/top(REST)을 사용합니다. |
| 일부 클라이언트에 부드러운 스트리밍 매니페스트의 반복 태그 문제가 발생할 수 있습니다. |자세한 내용은 [이 섹션](media-services-deliver-content-overview.md#known-issues)을 참조하세요. |
| Media Services .NET SDK 개체는 직렬화할 수 없으며 따라서 Azure Redis Cache에서 작동하지 않습니다. |SDK AssetCollection 개체를 직렬화하여 Azure Redis Cache에 추가하려는 경우 예외가 throw됩니다. |
|REST API는 자산 또는 계정 수준 필터를 가져오려고 할 때 "이 버전의 REST API에서 필터에 액세스할 수 없습니다."라는 오류 메시지로 응답합니다.|필터를 가져오는 데 사용하려는 것보다 최신 API 버전으로 필터를 만들거나 수정했습니다. 이 문제는 고객이 사용하는 코드 또는 도구에서 두 API 버전을 사용하는 경우에 발생할 수 있습니다.  여기서 가장 좋은 해결 방법은 최신 또는 두 API 버전을 사용하도록 코드를 업그레이드하는 것입니다.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>REST API 버전 기록
Media Services REST API 버전 기록에 대한 자세한 내용은 [Azure Media Services REST API 참조]를 참조하세요.

## <a name="february-2020"></a>2020년 2월

일부 분석 미디어 프로세서는 더 이상 사용되지 않을 예정입니다. 사용 중지 날짜는 [레거시 구성 요소](legacy-components.md) 토픽을 참조하세요.

## <a name="september-2019"></a>2019년 9월

### <a name="deprecation-of-media-processors"></a>미디어 프로세서 사용 중단

*Azure Media Indexer* 및 *Azure Media Indexer 2 미리 보기*의 사용 중단을 발표할 예정입니다. [Azure Media Services Video Indexer](../video-indexer/index.yml)는 이러한 레거시 미디어 프로세서를 대체합니다.

사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 토픽을 참조하세요.

또한 [Azure Media Indexer 및 Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션](migrate-indexer-v1-v2.md)을 참조하세요.

## <a name="august-2019"></a>2019년 8월

### <a name="deprecation-of-media-processors"></a>미디어 프로세서 사용 중단

WAME(*Windows Azure Media Encoder*) 및 AME(*Azure Media Encoder*) 미디어 프로세서의 사용 중단을 발표할 예정입니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 토픽을 참조하세요.

자세한 내용은 [WAME에서 Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101334) 및 [AME에서 Media Encoder Standard로 마이그레이션](https://go.microsoft.com/fwlink/?LinkId=2101335)을 참조하세요.

## <a name="march-2019"></a>2019년 3월

Azure Media Services의 Media Hyperlapse 미리 보기 기능은 사용 중지되었습니다.

## <a name="december-2018"></a>2018년 12월

Azure Media Services의 Media Hyperlapse 미리 보기 기능은 곧 사용 중지됩니다. 2018년 12월 19일부터 Media Services는 Media Hyperlapse를 더 이상 변경하거나 개선하지 않습니다. 2019년 3월 29일에 사용 중지되며 더 이상 사용할 수 없습니다.

## <a name="october-2018"></a>2018년 10월

### <a name="cmaf-support"></a>CMAF 지원

CMAF를 지원하는 Apple HLS(iOS 11+) 및 MPEG-DASH 플레이어에 대해 CMAF 및 'cbcs' 암호화가 지원됩니다.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT 썸네일 스프라이트

이제 v2 API를 사용하면 Media Services를 사용하여 Web VTT 썸네일 스프라이트를 생성할 수 있습니다. 자세한 내용은 [썸네일 스프라이트 생성](generate-thumbnail-sprite.md)을 참조하세요.

## <a name="july-2018"></a>2018년 7월

최신 서비스 릴리스에서는 작업이 실패할 때 서비스가 반환하는 오류 메시지가 두 개 이상의 줄로 분리되는 방식과 관련하여 서식이 약간 변경되었습니다.

## <a name="may-2018"></a>2018년 5월 

2018년 5월 12일부터 라이브 채널은 RTP/MPEG-2 전송 스트림 수집 프로토콜을 더 이상 지원하지 않습니다. RTP/MPEG-2에서 RTMP 또는 조각난 MP4(부드러운 스트리밍) 수집 프로토콜로 마이그레이션하세요.

## <a name="october-2017-release"></a>2017년 10월 릴리스
> [!IMPORTANT] 
> Media Services는 Azure Access Control Service 인증 키에 대한 지원을 사용하지 않습니다. 2018년 6월 22일부터 더 이상 Access Control Service 키를 사용하여 코드를 통해 Media Services 백 엔드에서 인증할 수 없습니다. [Azure AD 기반 인증](media-services-use-aad-auth-to-access-ams-api.md)에 Azure AD(Azure Active Directory)를 사용하도록 코드를 업데이트해야 합니다. Azure Portal에서 이러한 변경에 대한 경고를 감시합니다.

### <a name="updates-for-october-2017"></a>2017년 10월 업데이트
#### <a name="sdks"></a>SDK
* .NET SDK는 Azure AD 인증을 지원하도록 업데이트되었습니다. Azure AD로의 빠른 마이그레이션을 장려하기 위해 Nuget.org의 최신 .NET SDK에서 Access Control Service 인증에 대한 지원을 제거했습니다. 
* JAVA SDK는 Azure AD 인증을 지원하도록 업데이트되었습니다. Java SDK에 Azure AD 인증에 대한 지원이 추가되었습니다. Media Services에서 Java SDK를 사용하는 방법에 대한 세부 정보는 [Java 클라이언트 SDK를 사용하여 Azure Media Services 시작](media-services-java-how-to-use.md)을 참조하세요.

#### <a name="file-based-encoding"></a>파일 기반 인코딩
* 이제 프리미엄 인코더를 사용하여 H.265 HEVC(고품질 비디오 코딩) 비디오 코덱으로 콘텐츠를 인코딩할 수 있습니다. H.264와 같은 다른 코덱이 아닌 H.265를 선택하는 경우 가격 책정에 영향을 주지 않습니다. HEVC 특허 라이선스에 대한 정보는 [온라인 서비스 약관](https://azure.microsoft.com/support/legal/)을 참조하세요.
* iOS11 또는 GoPro Hero 6을 사용하여 캡처된 비디오와 같이 H.265(HEVC) 비디오 코덱으로 인코딩된 원본 비디오의 경우 이제 프리미엄 인코더 또는 표준 인코더를 사용하여 해당 비디오를 인코딩할 수 있습니다. 특허 라이선스에 대한 정보는 [온라인 서비스 약관](https://azure.microsoft.com/support/legal/)을 참조하세요.
* 다중 언어 오디오 트랙을 포함하는 콘텐츠의 경우 언어 값은 해당 파일 형식 사양에 따라 올바르게 레이블이 지정되어야 합니다(예: ISO MP4). 표준 인코더를 사용하여 스트리밍하기 위해 콘텐츠를 인코딩할 수 있습니다. 결과 스트리밍 로케이터는 사용 가능한 오디오 언어를 나열합니다.
* 이제 표준 인코더는 "AAC 오디오" 및 "AAC 고급 오디오"라는 두 개의 새 오디오 전용 시스템 미리 설정을 지원합니다. 각각 128Kbps 및 192Kbps의 비트 전송률로 스테레오 AAC(고급 오디오 코딩) 출력을 생성합니다.
* 이제 프리미엄 인코더는 입력으로 QuickTime/MOV 파일 형식을 지원합니다. 비디오 코덱은 [이 GitHub 문서에 나열된 Apple ProRes 형식](./media-services-media-encoder-standard-formats.md) 중 하나여야 합니다. 오디오는 AAC 또는 PCM(펄스 부호 변조) 중 하나여야 합니다. 예를 들어 프리미엄 인코더는 입력으로 QuickTime/MOV 파일에 래핑된 DVC/DVCPro 비디오를 지원하지 않습니다. 표준 인코더는 이러한 비디오 코덱을 지원하지 않습니다.
* 인코더에서 다음 버그가 수정되었습니다.

    * 이제 입력 자산을 사용하여 작업을 제출할 수 있습니다. 이러한 작업이 완료된 후에 자산을 수정하고(예: 자산 내에서 파일 추가, 삭제 또는 이름 바꾸기) 추가 작업을 제출할 수 있습니다.
    * 표준 인코더에서 생성한 JPEG 썸네일의 품질이 향상되었습니다.
    * 표준 인코더는 매우 짧은 비디오에서 입력 메타데이터 및 썸네일 생성을 보다 효율적으로 처리합니다.
    * 표준 인코더에 사용된 H.264 디코더의 향상된 기능은 드물지만 특정 아티팩트를 제거합니다. 

#### <a name="media-analytics"></a>미디어 분석
Azure Media Redactor의 일반 공급: 이 미디어 프로세서는 선택한 개인의 얼굴을 흐리게 하여 비디오 익명화를 수행하며 공공 안전 및 뉴스 미디어 시나리오에서 사용하기 적합합니다. 

이 새 프로세서에 대한 개요는 [이 블로그 게시물](https://azure.microsoft.com/blog/azure-media-redactor/)을 참조하세요. 설명서 및 설정에 대한 정보는 [Azure 미디어 분석으로 얼굴 편집](media-services-face-redaction.md)을 참조하세요.



## <a name="june-2017-release"></a>2017년 6월 릴리스

이제 Media Services는 [Azure AD 기반 인증](media-services-use-aad-auth-to-access-ams-api.md)을 지원합니다.

> [!IMPORTANT]
> 현재 Media Services는 Access Control Service 인증 모델을 지원합니다. Access Control Service 인증은 2018년 6월 1일부터 사용되지 않을 예정입니다. 가능한 빨리 Azure AD 인증 모델로 마이그레이션하는 것이 좋습니다.

## <a name="march-2017-release"></a>2017년 3월 릴리스

이제 인코딩 작업을 만들 때 "적응 스트리밍" 사전 설정 문자열을 지정하여 [비트 전송률 단계를 자동으로 생성](media-services-autogen-bitrate-ladder-with-mes.md)하기 위해 표준 인코더를 사용할 수 있습니다. Media Services로 스트리밍할 비디오를 인코딩하려면 "적응 스트리밍" 사전 설정을 사용합니다. 특정 시나리오에 대한 인코딩 기본 설정을 사용자 지정하려면 [이러한 사전 설정](media-services-mes-presets-overview.md)으로 시작할 수 있습니다.

이제 Media Encoder Standard 또는 Media Encoder Premium Workflow를 사용하여 [fMP4 청크를 생성하는 인코딩 작업을 만들](media-services-generate-fmp4-chunks.md) 수 있습니다. 

## <a name="february-2017-release"></a>2017년 2월 릴리스

2017년 4월 1일부터 사용자 계정에 있는 90일이 지난 작업 레코드는 연결된 태스크 레코드와 함께 자동으로 삭제됩니다. 레코드의 총 수가 최고 할당량 미만인 경우에도 삭제됩니다. 작업 정보를 보관하려면 [Media Services .NET SDK와 관련된 엔터티 및 자산 관리](media-services-dotnet-manage-entities.md)에 설명된 코드를 사용할 수 있습니다.

## <a name="january-2017-release"></a>2017년 1월 릴리스

Media Services에서 스트리밍 엔드포인트는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 애플리케이션에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. Media Services는 매끄러운 Azure Content Delivery Network 통합도 제공합니다. StreamingEndpoint 서비스의 아웃바운드 스트림은 Media Services 계정에서 자산의 라이브 스트림, 주문형 비디오 또는 점진적 다운로드일 수 있습니다. 각 Media Services 계정에는 기본 스트리밍 엔드포인트가 포함됩니다. 계정에서 추가 스트리밍 엔드포인트를 만들 수 있습니다. 

스트리밍 엔드포인트 1.0 및 2.0이라는 두 가지 버전이 있습니다. 2017년 1월 10일부터 새로 만든 모든 Media Services 계정에는 버전 2.0 기본 스트리밍 엔드포인트가 포함됩니다. 이 계정에 추가하는 추가 스트리밍 엔드포인트도 버전 2.0입니다. 이 변경은 기존 계정에 영향을 주지 않습니다. 기존 스트리밍 엔드포인트인 버전 1.0을 2.0 버전으로 업그레이드할 수 있습니다. 이러한 변경으로 인한 동작, 청구 및 기능 변경 내용이 있습니다. 자세한 내용은 [스트리밍 엔드포인트 개요](media-services-streaming-endpoints-overview.md)를 참조하세요.

2\.15 버전부터 Media Services는 스트리밍 엔드포인트 엔터티에 다음과 같은 속성을 추가했습니다.

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

이러한 속성에 대한 자세한 정보는 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)를 참조하세요. 

## <a name="december-2016-release"></a>2016년 12월 릴리스

 이제 Media Services를 사용하여 해당 서비스에 대한 원격 분석/메트릭 데이터에 액세스할 수 있습니다. 현재 버전의 Media Services를 사용하여 라이브 채널, 스트리밍 엔드포인트 및 보관 엔터티에 대한 원격 분석 데이터를 수집할 수 있습니다. 자세한 내용은 [Media Services 원격 분석](media-services-telemetry-overview.md)을 참조하세요.

## <a name="july-2016-release"></a><a name="july_changes16"></a>2016년 7월 릴리스
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>인코딩 작업으로 생성된 매니페스트 파일(*.ISM)에 대한 업데이트
인코딩 작업이 Media Encoder Standard 또는 Media Encoder Premium에 제출된 경우 인코딩 작업은 출력 자산에 [스트리밍 매니페스트 파일](media-services-deliver-content-overview.md)(*.ism)을 생성합니다. 최신 서비스 릴리스와 함께 이 스트리밍 매니페스트 파일의 구문이 업데이트되었습니다.

> [!NOTE]
> 스트리밍 매니페스트(.ism) 파일의 구문은 내부 용도로 예약되며 후속 릴리스에서 변경될 수 있습니다. 이 파일의 내용을 수정하거나 조작하지 마세요.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>인코딩 작업이 하나 이상의 MP4 파일을 출력하는 경우 새 클라이언트 매니페스트(*. ISMC) 파일이 출력 자산에 생성됩니다.
최신 서비스 릴리스부터, 하나 이상의 MP4 파일을 생성하는 인코딩 작업이 완료되면 출력 자산에는 스트리밍 클라이언트 매니페스트(*.ismc) 파일도 포함됩니다. .ismc 파일을 통해 동적 스트리밍의 성능을 향상시킬 수 있습니다. 

> [!NOTE]
> 클라이언트 매니페스트(.ism) 파일의 구문은 내부 용도로 예약되며 후속 릴리스에서 변경될 수 있습니다. 이 파일의 내용을 수정하거나 조작하지 마세요.
> 
> 

자세한 내용은 [이 블로그](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file)를 참조하세요.

### <a name="known-issues"></a>알려진 문제
일부 클라이언트에 부드러운 스트리밍 매니페스트의 반복 태그 문제가 발생할 수 있습니다. 자세한 내용은 [이 섹션](media-services-deliver-content-overview.md#known-issues)을 참조하세요.

## <a name="april-2016-release"></a><a id="apr_changes16"></a>2016년 4월 릴리스
### <a name="media-analytics"></a>미디어 분석
 Media Services는 강력한 비디오 인텔리전스를 위한 미디어 분석을 출시했습니다. 자세한 내용은 [Media Services 분석 개요](media-services-analytics-overview.md)를 참조하세요.

### <a name="apple-fairplay-preview"></a>Apple FairPlay(미리 보기)
이제 Media Services를 사용하여 Apple FairPlay에서 HLS(HTTP 라이브 스트리밍) 콘텐츠를 동적으로 암호화할 수 있습니다. Media Services 라이선스 배달 서비스를 사용하여 클라이언트에 FairPlay 라이선스를 제공할 수도 있습니다. 자세한 내용은 "Azure Media Services를 사용하여 Apple FairPlay로 보호되는 HLS 콘텐츠 스트리밍"을 참조하세요.

## <a name="february-2016-release"></a><a id="feb_changes16"></a>2016년 2월 릴리스
최신 버전의 .NET용 Media Services SDK(3.5.3)에는 Google Widevine 관련 버그 수정이 포함되어 있습니다. Widevine으로 암호화된 여러 자산에 대해 AssetDeliveryPolicy를 다시 사용할 수 없습니다. 이 버그 수정의 일환으로 WidevineBaseLicenseAcquisitionUrl 속성이 SDK에 추가되었습니다.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>2016년 1월 릴리스
인코더 이름의 혼동을 줄이기 위해 인코딩 예약 단위 이름이 바뀌었습니다.

기본, 표준 및 프리미엄 인코딩 예약 단위의 이름이 S1, S2 및 S3 예약 단위로 각각 바뀌었습니다. 지금 기본 인코딩 예약 단위를 사용하는 고객은 Azure Portal(및 청구서)에서 레이블로 S1를 참조하세요. 표준 및 프리미엄을 사용하는 고객은 각각 S2 및 S3 레이블을 참조하세요. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>2015년 12월 릴리스

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder 사용 중단 알림

 Media Encoder는 Media Encoder Standard 릴리스의 약 12개월 지점부터 사용되지 않습니다.

### <a name="azure-sdk-for-php"></a>PHP용 Azure SDK
Azure SDK 팀은 Media Services에 대한 업데이트 및 새 기능을 포함하는 [PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php) 패키지의 새 릴리스를 게시합니다. 특히 PHP용 Media Services SDK는 이제 최신 [Content Protection](media-services-content-protection-overview.md) 기능을 지원합니다. 이러한 기능은 토큰 제한을 포함하거나 없는 AES 및 DRM(PlayReady 및 Widevine)을 사용하는 동적 암호화입니다. 또한 [인코딩 단위](media-services-dotnet-encoding-units.md)의 크기 조정을 지원합니다.

자세한 내용은 다음을 참조하세요.

* 다음 [코드 샘플](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)을 통해 신속하게 시작할 수 있습니다.
  * **vodworkflow_aes.php**: 이 PHP 파일은 AES-128 동적 암호화 및 키 배달 서비스를 사용하는 방법을 보여줍니다. [AES-128 동적 암호화 및 키 배달 서비스 사용](media-services-protect-with-aes128.md)에 설명된 .NET 샘플에 기반합니다.
  * **vodworkflow_aes.php**: 이 PHP 파일은 PlayReady 동적 암호화 및 라이선스 배달 서비스를 사용하는 방법을 보여줍니다. [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)에 설명된 .NET 샘플에 기반합니다.
  * **scale_encoding_units.php**: 이 PHP 파일은 인코딩 예약 단위 크기를 조정하는 방법을 보여줍니다.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>2015년 11월 릴리스
 이제 Media Services는 클라우드에서 Widevine 라이선스 배달 서비스를 제공합니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)를 참조하세요. 또한 [이 자습서](media-services-protect-with-playready-widevine.md) 및 [GitHub 리포지토리](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)도 참조하세요. 

Media Services에서 제공하는 Widevine 라이선스 배달 서비스는 미리 보기로 제공됩니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)를 참조하세요.

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>2015년 10월 릴리스
Media Services는 이제 다음과 같은 데이터 센터에서 사용할 수 있습니다. 브라질 남부, 인도 서부, 인도 남부 및 인도 중부. 이제 Azure Portal을 사용하여 [Media Service 계정을 만들고](media-services-portal-create-account.md)[Media Services 설명서 웹 페이지](https://azure.microsoft.com/documentation/services/media-services/)에서 설명한 다양한 작업을 수행할 수 있습니다. Live Encoding은 이러한 데이터 센터에서 활성화되지 않습니다. 또한 일부 유형의 인코딩 예약 단위를 이러한 데이터 센터에서 사용할 수 없습니다.

* 브라질 남부:                                          표준 및 기본 인코딩 예약 단위만 사용 가능합니다.
* 인도 서부, 인도 남부 및 인도 중부:             기본 인코딩 예약 단위만 사용 가능합니다.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>2015년 9월 릴리스
이제 Media Services에서는 Widevine 모듈식 DRM 기술을 사용하여 주문형 비디오와 라이브 스트림 둘 다를 보호하는 기능을 제공합니다. 다음과 같은 배달 서비스 파트너를 사용하여 Widevine 라이선스를 배달할 수 있습니다.
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)를 참조하세요.
  
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)(버전 3.5.1 이상) 또는 REST API를 통해 Widevine을 사용하도록 AssetDeliveryConfiguration을 구성할 수 있습니다. 
* Media Services에서는 Apple ProRes 동영상 지원을 추가했습니다. 이제 Apple ProRes 또는 기타 코덱을 사용하는 QuickTime 원본 동영상 파일을 업로드할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)를 참조하세요.
* 이제 Media Encoder Standard를 사용하여 하위 클리핑 및 라이브 보관 추출을 수행할 수 있습니다. 자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)를 참조하세요.
* 다음 필터링이 업데이트되었습니다. 
  
  * 이제 오디오 전용 필터로 Apple HLS 형식을 사용할 수 있습니다. 이 업데이트를 통해 오디오 전용 트랙을 URL에 지정하여(audio-only=false) 제거할 수 있습니다.
  * 이제 자산에 대한 필터를 정의할 때 단일 URL에 여러(최대 3개) 필터를 결합할 수 있습니다.
    
    자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)를 참조하세요.
* Media Services는 이제 HLS 버전 4에서 I-프레임을 지원합니다. I-프레임 지원은 빨리 감기와 되감기 작업을 최적화합니다. 기본적으로 모든 HLS 버전 4 출력은 I-프레임 재생 목록(EXT-X-I-FRAME-STREAM-INF)를 포함합니다.
자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)를 참조하세요.

## <a name="august-2015-release"></a><a id="august_changes_15"></a>2015년 8월 릴리스
* Java용 Media Services SDK 버전 0.8.0 릴리스 및 새로운 샘플이 제공됩니다. 자세한 내용은 다음을 참조하세요.
    
* Azure Media Player는 다중 오디오 스트림을 지원하도록 업데이트되었습니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)에 게시해 주세요.

## <a name="july-2015-release"></a><a id="july_changes_15"></a>2015년 7월 릴리스
* Media Encoder Standard의 일반 공급 기능이 도입되었습니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)에 게시해 주세요.
  
    Media Encoder Standard은 [이 섹션](https://go.microsoft.com/fwlink/?LinkId=618336)에 설명된 대로 기본 설정을 사용합니다. 4K 인코드에 대한 기본 설정을 사용하는 경우 프리미엄 예약 단위 형식을 가져와야 합니다. 자세한 내용은 [인코딩 크기 조정](media-services-scale-media-processing-overview.md)을 참조하세요.
* 라이브 실시간 캡션은 Media Services 및 Media Player에서 사용되었습니다. 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)에 게시해 주세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK의 현재 버전은 3.4.0.0입니다. 다음이 업데이트되었습니다. 

* 라이브 보관에 대한 지원이 구현되었습니다. 라이브 아카이브를 포함한 자산은 다운로드할 수 없습니다.
* 동적 필터에 대한 지원이 구현되었습니다.
* 사용자가 자산을 삭제하는 동안 스토리지 컨테이너를 유지할 수 있도록 기능이 구현되었습니다.
* 채널에서 재시도 정책 관련 버그가 수정되었습니다.
* Media Encoder Premium Workflow를 활성화했습니다.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>2015년 6월 릴리스
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK의 현재 버전은 3.3.0.0입니다. 다음이 업데이트되었습니다. 

* OpenId Connect 검색 사양에 대한 지원이 추가되었습니다.
* ID 공급자 측의 키 롤오버 처리에 대한 지원이 추가되었습니다.

OpenID Connect 검색 문서를 노출하는 ID 공급자(Azure AD, Google 및 Salesforce에서처럼)를 사용하는 경우 Media Services에 OpenID Connect 검색 사양의 JWT(JSON Web Tokens)에 대한 유효성을 검사하는 서명 키를 가져오도록 지시할 수 있습니다. 

자세한 내용은 [OpenID Connect 검색 사양에서 JSON 웹 키를 사용하여 Media Services에서 JWT 인증 사용](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)을 참조하세요.

## <a name="may-2015-release"></a><a id="may_changes_15"></a>2015년 5월 릴리스
다음과 같은 새로운 기능이 도입되었습니다.

* [Media Services로 Live Encoding 미리 보기](media-services-manage-live-encoder-enabled-channels.md)
* [동적 매니페스트](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>2015년 4월 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)가 도입되었습니다.
* Media Services REST 2.10부터 RTMP(실시간 메시지 프로토콜)을 수집하도록 구성된 채널이 기본 및 보조 수집 URL을 통해 생성됩니다. 자세한 내용은 [채널 수집 구성](media-services-live-streaming-with-onprem-encoders.md#channel_input)을 참조하세요.
* Azure Media Indexer가 업데이트되었습니다.
* 스페인어 지원이 추가되었습니다.
* XML 형식에 대한 새 구성이 추가되었습니다.

자세한 내용은 [이 블로그](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)를 참조하세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK의 현재 버전은 3.2.0.0입니다. 다음이 업데이트되었습니다.

* 호환성이 손상되는 변경 내용: TokenRestrictionTemplate.Issuer 및 TokenRestrictionTemplate.Audience가 문자열 형식으로 변경되었습니다.
* 사용자 지정 다시 시도 정책 만들기 관련 업데이트가 수행되었습니다.
* 파일 업로드 및 다운로드 관련 버그가 수정되었습니다.
* 이제 MediaServicesCredentials 클래스에서는 기본 및 보조 액세스 제어 엔드포인트에 대한 인증을 허용합니다.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>2015년 3월 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* 이제 Media Services는 Content Delivery Network 통합을 제공합니다. 통합을 지원하기 위해 CdnEnabled 속성이 StreamingEndpoint에 추가되었습니다. 버전 2.9부터 REST API에서 CdnEnabled를 사용할 수 있습니다. 자세한 내용은 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)를 참조하세요. 버전 3.1.0.2부터 .NET SDK에서 CdnEnabled를 사용할 수 있습니다. 자세한 내용은 [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file)를 참조하세요.
* Media Encoder Premium Workflow를 도입했습니다. 자세한 내용은 [Azure Media Services의 프리미엄 인코딩 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)를 참조하세요.

## <a name="february-2015-release"></a><a id="february_changes_15"></a>2015년 2월 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
Media Services REST API의 현재 버전은 2.9입니다. 이 버전부터 스트리밍 엔드포인트에서 Content Delivery Network 통합을 사용할 수 있습니다. 자세한 내용은 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)를 참조하세요.

## <a name="january-2015-release"></a><a id="january_changes_15"></a>2015년 1월 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
동적 암호화를 사용하는 Content Protection의 일반 공급이 도입되었습니다. 자세한 내용은 [Media Services에서 DRM 기술의 일반적인 가용성으로 스트리밍 보안 강화](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)를 참조하세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK의 현재 버전은 3.1.0.1입니다.

이 릴리즈에서 기본 Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate 생성자는 사용되지 않음으로 표시됩니다. TokenType를 인수로 사용하는 새로운 생성자입니다.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>2014년 12월 릴리스
### <a name="general-media-services-updates"></a>일반 Media Services 업데이트
* 일부 업데이트 및 새 기능이 Media Indexer에 추가되었습니다. 자세한 내용은 [Azure Media Indexer 버전 1.1.6.7 릴리스 정보](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)를 참조하세요.
* 인코딩 예약 단위를 업데이트하는 데 사용할 수 있는 새 REST API가 추가되었습니다. 자세한 내용은 [REST에서 EncodingReservedUnitType](/rest/api/media/operations/encodingreservedunittype)을 참조하세요.
* 키 배달 서비스에 대한 CORS 지원이 추가되었습니다.
* 권한 부여 정책 옵션을 쿼리하는 성능이 향상되었습니다.
* 중국 데이터 센터에서는 이제 다른 데이터 센터와 마찬가지로 [키 배달 URL](/rest/api/media/operations/contentkey#get_delivery_service_url) 이 고객별로 하나씩 할당됩니다.
* HLS 자동 대상 기간이 추가되었습니다. 라이브 스트리밍을 수행할 때 HLS는 항상 동적으로 패키지됩니다. Media Services는 기본적으로 키 프레임 간격(KeyFrameInterval)에 따라 자동으로 HLS 세그먼트 패키징 비율(FragmentsPerSegment)을 계산합니다. 이 메서드는 라이브 인코더에서 수신한 GOP(Group of Pictures)라고도 합니다. 자세한 내용은 [Media Services 라이브 스트리밍 사용](/previous-versions/azure/dn783466(v=azure.100))을 참조하세요.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK 업데이트
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)의 현재 버전은 3.1.0.0입니다. 다음이 업데이트되었습니다.

* .NET SDK 종속성이 .NET 4.5 Framework로 업그레이드되었습니다.
* 인코딩 예약 단위를 업데이트하는 데 사용할 수 있는 새 API가 추가되었습니다. 자세한 내용은 [.NET을 사용하여 예약 단위 형식 업데이트 및 인코딩 예약 단위 증가](media-services-dotnet-encoding-units.md)를 참조하세요.
* 토큰 인증을 위한 JWT 지원이 추가되었습니다. 자세한 내용은 [Media Services 및 동적 암호화의 JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조하세요.
* PlayReady 라이선스 템플릿에 BeginDate 및 ExpirationDate에 대한 상대적 오프셋이 추가되었습니다.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>2014년 11월 릴리스
* 이제 Media Services를 사용하여 라이브 부드러운 스트리밍(fMP4) 콘텐츠를 TLS 연결을 통해 수집할 수 있습니다. TLS를 통해 수집하려면 수집 URL을 HTTPS로 업데이트해야 합니다. 현재 Media Services는 사용자 지정 도메인을 사용하는 TLS를 지원하지 않습니다. 라이브 스트리밍에 대한 자세한 내용은 [Azure Media Services 라이브 스트리밍 사용](/previous-versions/azure/dn783466(v=azure.100))을 참조하세요.
* 현재 TLS 연결을 통해 RTMP 라이브 스트림을 수집할 수 없습니다.
* 콘텐츠를 배달하는 출발점이 될 스트리밍 엔드포인트가 2014년 9월 10일 이후에 만들어진 경우에만 TLS를 통해 스트리밍할 수 있습니다. 스트리밍 URL이 2014년 9월 10일 이후에 만들어진 스트리밍 엔드포인트를 기반으로 하는 경우 URL에는 "streaming.mediaservices.windows.net"(새 형식)이 포함됩니다. "origin.mediaservices.windows.net"(이전 형식)이 포함된 스트리밍 URL은 TLS를 지원하지 않습니다. URL이 이전 형식인 경우 TLS를 통해 스트리밍하려면 [새 스트리밍 엔드포인트를 만듭니다.](media-services-portal-manage-streaming-endpoints.md) TLS를 통해 콘텐츠를 스트리밍하려면 새 스트리밍 엔드포인트를 기준으로 하는 URL을 사용합니다.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
이제 .NET용 Media Services SDK 확장의 버전이 2.0.0.3입니다.

이제 .NET용 Media Services SDK의 버전이 3.0.0.8입니다. 다음이 업데이트되었습니다.

* 다시 시도 정책 클래스에서 리팩터링이 구현되었습니다.
* HTTP 요청 헤더에 사용자 에이전트 문자열이 추가되었습니다.
* NuGet 복원 빌드 단계가 추가되었습니다.
* 리포지토리에서 x509 인증서를 사용하도록 시나리오 테스트가 수정되었습니다.
* 채널 및 스트리밍 끝을 업데이트하는 경우 유효성 검사 설정이 추가되었습니다.

### <a name="new-github-repository-to-host-media-services-samples"></a>Media Services 샘플을 호스트하는 새 GitHub 리포지토리
샘플은 [Media Services 샘플 GitHub 리포지토리](https://github.com/Azure/Azure-Media-Services-Samples)에 있습니다.

## <a name="september-2014-release"></a><a id="september_changes_14"></a>2014년 9월 릴리스
Media Services REST 메타데이터의 현재 버전은 2.7입니다. 최신 REST 업데이트에 대한 자세한 내용은 [Media Services REST API 참조](/rest/api/media/operations/azure-media-services-rest-api-reference)를 참조하세요.

이제 .NET용 Media Services SDK의 버전이 3.0.0.7입니다.

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>주요 변경 내용
* 원본의 이름이 [StreamingEndpoint]로 바뀌었습니다.
* Azure Portal을 사용하여 MP4 파일을 인코딩한 다음 게시할 때 기본 동작이 변경되었습니다.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>일반 공급 릴리스에 포함된 새 기능/시나리오
* Media Indexer 미디어 프로세서가 도입되었습니다. 자세한 내용은 [Media Indexer를 사용하여 미디어 파일 인덱싱](/previous-versions/azure/dn783455(v=azure.100))을 참조하세요.
* 이제 [StreamingEndpoint] 엔터티를 사용하여 사용자 지정 도메인(호스트) 이름을 추가할 수 있습니다.
  
    사용자 지정 도메인 이름을 Media Services 스트리밍 엔드포인트 이름으로 사용하려면 스트리밍 엔드포인트에 사용자 지정 호스트 이름을 추가합니다. Media Services REST API 또는 .NET SDK를 통해 사용자 지정 호스트 이름을 추가합니다.
  
    고려 사항은 다음과 같습니다.
  
  * 사용자 지정 도메인 이름에 대한 소유권이 있어야 합니다.
  * Media Services에서 도메인 이름 소유권의 유효성을 검사해야 합니다. 도메인이 유효한지 확인하려면 MediaServicesAccountId 부모 도메인을 매핑하는 CName을 만들어 DNS mediaservices-dns-zone의 유효성을 검사합니다.
  * 사용자 지정 호스트 이름(예: sports.contoso.com)을 Media Services StreamingEndpoint 호스트 이름(예: amstest.streaming.mediaservices.windows.net)에 매핑되는 다른 CName을 만들어야 합니다.

    자세한 내용은 [StreamingEndpoint](/rest/api/media/operations/streamingendpoint) 문서의 CustomHostNames 속성을 참조하세요.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>공개 미리 보기 릴리스에 포함된 새 기능/시나리오
* 라이브 스트리밍 미리 보기 자세한 내용은 [Media Services 라이브 스트리밍 사용](/previous-versions/azure/dn783466(v=azure.100))을 참조하세요.
* 키 전달 서비스 자세한 내용은 [AES-128 동적 암호화 및 키 배달 서비스 사용](/previous-versions/azure/dn783457(v=azure.100))을 참조하세요.
* AES 동적 암호화 자세한 내용은 [AES-128 동적 암호화 및 키 배달 서비스 사용](/previous-versions/azure/dn783457(v=azure.100))을 참조하세요.
* PlayReady 라이선스 배달 서비스 
* PlayReady 동적 암호화 
* Media Services PlayReady 라이선스 템플릿 자세한 정보는 [Media Services PlayReady 라이선스 템플릿 개요]를 참조하세요.
* 스토리지에서 암호화된 자산 스트리밍 자세한 내용은 [스토리지에서 암호화된 콘텐츠 스트리밍](/previous-versions/azure/dn783451(v=azure.100))을 참조하세요.

## <a name="august-2014-release"></a><a id="august_changes_14"></a>2014년 8월 릴리스
자산을 인코딩할 때 인코딩 작업이 완료되면 출력 자산이 생성됩니다. 이번 릴리스까지 Media Services 인코더는 출력 자산에 대한 메타데이터를 생성했습니다. 이번 릴리스부터 이 인코더는 입력 자산에 대한 메타데이터도 생성합니다. 자세한 내용은 [입력 메타데이터] 및 [출력 메타데이터]를 참조하세요.

## <a name="july-2014-release"></a><a id="july_changes_14"></a>2014년 7월 릴리스
Azure Media Services 패키지 작성 도구 및 암호기에 대한 다음 버그가 수정되었습니다.

* 라이브 보관 자산을 HLS로 전송할 때 오디오만 다시 재생합니다. 이 문제가 수정되어 현재 오디오와 비디오를 모두 재생할 수 있습니다.
* 자산을 HLS 및 AES 128비트 봉투 암호화로 패키지하는 경우 Android 디바이스에서 패키지된 스트림이 다시 재생되지 않습니다. 이 버그가 수정되어 HLS를 지원하는 Android 디바이스에서 패키지된 스트림이 다시 재생됩니다.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>2014년 5월 릴리스
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>일반 Media Services 업데이트
이제 [동적 패키징]을 사용하여 HLS 버전 3을 스트리밍할 수 있습니다. HLS 버전 3를 스트리밍하려면 원래 로케이터 경로에 *.ism/manifest(format=m3u8-aapl-v3) 형식을 추가합니다. 자세한 내용은 [이 포럼](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)을 참조하세요.

이제 동적 패키징에서는 PlayReady를 통해 정적으로 암호화된 부드러운 스트리밍을 기반으로 하여 PlayReady로 암호화된 HLS(버전 3 및 버전 4)도 배달할 수 있습니다. PlayReady로 부드러운 스트리밍을 암호화하는 방법에 대한 자세한 내용은 [PlayReady로 부드러운 스트리밍 보호](/previous-versions/azure/dn189154(v=azure.100))를 참조하세요.

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK 업데이트
Media Services .NET SDK의 현재 버전은 3.0.0.5입니다. 다음이 업데이트되었습니다.

* 미디어 자산을 업로드하고 다운로드할 때 속도 및 복원력이 향상됩니다.
* 다시 시도 논리와 일시적 예외 처리 시 다음 항목이 향상되었습니다. 
  
  * 파일을 쿼리, 변경 내용 저장, 업로드 또는 다운로드할 때 발생한 예외의 경우 일시적인 오류 감지 및 다시 시도 논리가 향상되었습니다. 
  * 이제 웹 예외(예: Access Control Service 토큰 요청 중)가 발생하면 치명적 오류일수록 더 빠르게 실패합니다.

자세한 내용은 [.NET용 Media Services SDK의 다시 시도 논리]를 참조하세요.

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>2014년 1월/2월 릴리스
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 및 3.0.0.3
3\.0.0.1 및 3.0.0.2의 변경 내용은 다음과 같습니다.

* OrderBy 문에서 LINQ 쿼리 사용에 관련된 문제가 수정되었습니다.
* [GitHub]의 테스트 솔루션이 단위 기반 테스트와 시나리오 기반 테스트로 분할되었습니다.

변경 내용에 대한 자세한 내용은 [Media Services .NET SDK 3.0.0.1 및 3.0.0.2 릴리스](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)를 참조하세요.

버전 3.0.0.3에서 다음과 같은 사항이 변경되었습니다.

* 버전 3.0.3.0을 사용하도록 Azure Storage 종속성이 업그레이드되었습니다.
* 3\.0에서 이전 버전과 호환성 문제가 수정되었습니다. *.* 릴리스에 대한 이전 버전과의 호환성 문제가 해결되었습니다.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>2013년 12월 릴리스
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3\.0.x.x 릴리스는 이전 버전인 2.4.x.x 릴리스와 호환되지 않습니다.
> 
> 

이제 .NET용 Media Services의 최신 버전이 3.0.0.0입니다. NuGet에서 최신 패키지를 다운로드하거나 [GitHub]에서 비트를 받을 수 있습니다.

Media Services SDK 버전 3.0.0.0부터 [Azure AD Access Control Service](/previous-versions/azure/azure-services/hh147631(v=azure.100)) 토큰을 다시 사용할 수 있습니다. 자세한 내용은 [.NET용 Media Services SDK를 사용하여 Media Services에 연결](/previous-versions/azure/jj129571(v=azure.100))의 "Access Control Service 토큰 다시 사용" 섹션을 참조하세요.

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK 확장 2.0.0.0
 Media Services .NET SDK 확장은 코드를 단순화하고 Media Services를 사용하여 더욱 쉽게 개발할 수 있도록 지원하는 일련의 확장 메서드 및 도우미 함수입니다. [Media Services .NET SDK 확장](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)에서 최신 버전을 가져올 수 있습니다.

## <a name="november-2013-release"></a><a id="november_changes_13"></a>2013년 11월 릴리스
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services .NET SDK 변경 내용
이 버전부터 .NET용 Media Services SDK는 Media Services REST API 계층에 대한 호출을 실행할 때 발생할 수 있는 일시적인 오류를 처리합니다.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>2013년 8월 릴리스
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Azure SDK 도구에 포함된 Media Services PowerShell cmdlet
다음 Media Services PowerShell cmdlet이 [Azure SDK 도구](https://github.com/Azure/azure-sdk-tools)에 포함됩니다.

* Get-AzureMediaServices 

    예: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    예: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    예: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    예: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>2013년 6월 릴리스
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services 변경 내용
이 섹션에 언급된 다음과 같은 변경 내용은 2013년 6월 Media Services 릴리스에 포함된 업데이트입니다.

* 여러 스토리지 계정을 미디어 서비스 계정에 연결할 수 있는 기능 
    * StorageAccount
    * Asset.StorageAccountName 및 Asset.StorageAccount
* Job.Priority를 업데이트하는 기능 
* 알림 관련 엔터티 및 속성: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * 작업
* Asset.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services .NET SDK 변경 내용
2013년 6월 Media Services SDK 릴리스에 포함된 변경 내용은 다음과 같습니다. 최신 Media Services SDK는 GitHub에서 제공됩니다.

* 버전 2.3.0.0부터는 Media Services SDK에서 Media Services 계정에 여러 스토리지 계정을 연결하도록 지원합니다. 이 기능을 지원하는 API는 다음과 같습니다.
  
    * IStorageAccount 형식
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 속성
    * StorageAccount 속성
    * StorageAccountName 속성
  
      자세한 내용은 [여러 Storage 계정에서 Media Services 자산 관리](/previous-versions/azure/dn271889(v=azure.100))를 참조하세요.
* 알림 관련 API입니다. 버전 2.2.0.0부터는 Azure Queue Storage 알림을 수신 대기할 수 있습니다. 자세한 내용은 [Media Services 작업 알림 처리](/previous-versions/azure/dn261241(v=azure.100))를 참조하세요.
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 속성
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 형식
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 형식
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 형식
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 형식
* Storage 클라이언트 SDK 2.0(Microsoft.WindowsAzure.StorageClient.dll)에 대한 종속성
* OData 5.5(Microsoft.Data.OData.dll)에 대한 종속성

## <a name="december-2012-release"></a><a id="december_changes_12"></a>2012년 12월 릴리스
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services .NET SDK 변경 내용
* IntelliSense: 다양한 유형의 누락된 IntelliSense 설명서가 추가되었습니다.
* Microsoft.Practices.TransientFaultHandling.Core: SDK에서 이 어셈블리의 이전 버전에 종속되는 문제가 수정되었습니다. 현재 SDK는 이 어셈블리의 5.1.1209.1 버전을 참조합니다.

2012년 11월 SDK에서 확인되어 수정된 문제:

* IAsset.Locators.Count: 이제 이 수는 모든 로케이터가 삭제된 후 새로운 IAsset 인터페이스에서 올바르게 보고됩니다.
* IAssetFile.ContentFileSize: 이제 이 값은 IAssetFile.Upload(filepath)에서 업로드한 후 올바르게 설정됩니다.
* IAssetFile.ContentFileSize: 이제 이 속성은 자산 파일을 만들 때 설정할 수 있습니다. 이전에는 읽기 전용이었습니다.
* IAssetFile.Upload(filepath): 자산에 여러 파일을 업로드할 때 이 동기식 업로드 메서드가 "서버가 요청을 인증하지 못했습니다. 서명을 비롯한 권한 부여 헤더 값이 올바르게 구성되어 있는지 확인하세요." 오류를 Throw하는 문제를 해결합니다.
* IAssetFile.UploadAsync: 파일의 동시 업로드를 5개의 파일로 제한하는 문제가 수정되었습니다.
* IAssetFile.UploadProgressChanged: 이 이벤트는 이제 SDK에서 제공됩니다.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): 이제 이 메서드 오버로드가 제공됩니다.
* IAssetFile.DownloadAsync: 파일의 동시 다운로드를 5개의 파일로 제한하는 문제가 수정되었습니다.
* IAssetFile.Delete(): IAssetFile로 업로드된 파일이 없는 경우 delete를 호출하면 예외가 throw되는 문제가 수정되었습니다.
* 작업: 작업 템플릿을 사용하여 “MP4를 부드러운 스트림으로 변환 작업”과 “PlayReady Protection 작업”을 연결해도 작업이 전혀 만들어지지 않는 문제가 수정되었습니다.
* EncryptionUtils.GetCertificateFromStore(): 이 메서드는 인증서 구성 문제로 인해 인증서를 찾지 못하여 발생하는 Null 참조 예외를 더 이상 Throw하지 않습니다.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>2012년 11월 릴리스
이 섹션에 설명된 변경 내용은 2012년 11월(버전 2.0.0.0) SDK에 포함된 업데이트입니다. 이러한 변경 내용에서는 2012년 6월 SDK 미리 보기 릴리스용으로 작성된 코드를 수정하거나 다시 작성해야 할 수 있습니다.

* 자산
  
    * IAsset.Create(assetName)는 *유일한* 자산 생성 함수입니다. IAsset.Create는 더 이상 메서드 호출을 통해 파일을 업로드하지 않고, IAssetFile을 사용하여 업로드합니다.
    * 서비스 SDK에서 IAsset.Publish 메서드와 AssetState.Publish 열거 값이 제거되었습니다. 이 값을 기반으로 하는 모든 코드를 다시 작성해야 합니다.
* FileInfo
  
    * 이 클래스는 제거되었으며 IAssetFile로 바뀌었습니다.
  
* IAssetFiles
  
    * IAssetFile은 FileInfo를 대체하며 동작이 다릅니다. 이를 사용하려면 IAssetFiles 개체를 인스턴스화한 후 Media Services SDK 또는 Storage SDK를 사용하여 파일을 업로드합니다. 사용할 수 있는 IAssetFile.Upload 오버로드는 다음과 같습니다.
  
        * IAssetFile.Upload(filePath): 스레드를 차단하는 동기 메서드로, 단일 파일을 업로드할 때만 권장됩니다.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): 이 비동기 메서드는 기본 설정된 업로드 메커니즘입니다. 
    
            알려진 버그: 취소 토큰을 사용하는 경우 업로드가 취소됩니다. 작업에 취소 상태가 여러 번 발생할 수 있습니다. 예외를 올바르게 파악하여 처리해야 합니다.
* 로케이터
  
    * 원본 관련 버전이 제거되었습니다. SAS 특정 context.Locators.CreateSasLocator(asset, accessPolicy)가 더 이상 사용되지 않거나 일반 공급에 의해 제거된 것으로 표시됩니다. 업데이트된 동작에 대한 자세한 내용은 "새 기능" 아래의 "로케이터" 섹션을 참조하세요.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>2012년 6월 미리 보기 릴리스
11월 SDK 릴리스의 새로운 기능은 다음과 같습니다.

* 엔터티 삭제
  
    * 이제 cloudMediaContext.ObjCollection.Delete(objInstance) 등의 컬렉션에서 삭제할 필요 없이 IObject.Delete() 등의 개체 수준에서 IAsset, IAssetFile, ILocator, IAccessPolicy 및 IContentKey 개체가 삭제되었습니다.
* 로케이터
  
    * 이제 로케이터는 CreateLocator 메서드를 사용하여 만들어야 합니다. LocatorType.SAS 또는 LocatorType.OnDemandOrigin 열거 값을 만들려는 특정 로케이터 유형에 대한 인수로 사용해야 합니다.
    * 로케이터에 새로운 속성이 추가되었으므로 콘텐츠에 사용할 수 있는 URI를 더욱 쉽게 얻을 수 있습니다. 이렇게 로케이터를 새롭게 디자인함으로써 향후 타사 제품으로 보다 유연하게 확장할 수 있으며 미디어 클라이언트 애플리케이션의 사용 편의를 높일 수 있습니다.
* 비동기 메서드 지원
  
    * 모든 메서드에 비동기 지원이 추가되었습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Azure Media Services REST API 참조]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[입력 메타데이터]: ./media-services-input-metadata-schema.md
[출력 메타데이터]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Media Services PlayReady 라이선스 템플릿 개요]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[동적 패키징]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[.NET용 Media Services SDK의 다시 시도 논리]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)