---
title: Azure Media Services v2에서 v3으로 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다. 또한 이 문서에서는 Media Services v2 to v3에서 이동하기 위한 마이그레이션 지침을 제공합니다.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/27/2019
ms.author: juliako
ms.openlocfilehash: 6ae094ddf1bfc55ce793995f60f1694be6eb19c1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103813"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침

이 문서에서는 Azure Media Services v3에서 변경된 내용을 설명하고, 두 버전 간의 차이점을 보여주고, 마이그레이션 가이드를 제공합니다.

현재 비디오 서비스가 [레거시 Media Services v2 API](../previous/media-services-overview.md) 위에 배포된 경우 v3 API로 마이그레이션하기 전에 다음 지침 및 고려 사항을 검토해야 합니다. v3 API에는 개발자 경험과 Media Services 기능을 향상하는 여러 이점과 새 기능이 있습니다. 그러나 이 문서의 [알려진 문제](#known-issues) 섹션에서 언급했듯이, API 버전 간의 변경 사항으로 인해 몇 가지 제한이 있습니다. Media Services 팀이 지속적으로 v3 API를 개선하고 버전 간 차이를 해결하고 있으므로 이 페이지는 계속 유지됩니다. 

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](developers-guide.md) 중 하나를 사용하세요.

## <a name="benefits-of-media-services-v3"></a>Media Services v3의 혜택
  
### <a name="api-is-more-approachable"></a>더 접근하기 쉬운 API

*  v3은 Azure Resource Manager에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. Azure Resource Manager 템플릿을 사용하여 변환, 스트리밍 엔드포인트, 라이브 이벤트 등을 만들고 배포할 수 있습니다.
* [공개 API(Swagger라고도 함) 사양](https://aka.ms/ams-v3-rest-sdk) 문서입니다.
    파일 기반 인코딩을 포함하여 모든 서비스 구성 요소의 스키마를 공개합니다.
* [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) 및 Ruby에 SDK를 사용할 수 있습니다.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) 통합을 통해 간단한 스크립팅을 지원합니다.

### <a name="new-features"></a>새로운 기능

* 파일 기반 작업 처리의 경우 HTTP(S) URL을 입력으로 사용할 수 있습니다.<br/>아직 콘텐츠를 Azure에 저장하거나 자산을 만들 필요가 없습니다.
* 파일 기반 작업 처리를 위한 [변환](transforms-jobs-concept.md) 개념을 도입합니다. 변환을 사용하여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테넌트 간에 처리 설정을 격리할 수 있습니다.
* [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화 설정이 각기 다른 [스트리밍 로케이터](streaming-locators-concept.md)가 자산에 여러 개 있을 수 있습니다.
* [콘텐츠 보호](content-key-policy-concept.md)에서 다중 키 기능을 지원합니다.
* 단일 비트 전송률 기여 피드를 다중 비트 전송률이 있는 출력 스트림으로 코드 변환하기 위해 Media Services를 사용할 때 최대 24시간 분량의 라이브 이벤트를 스트리밍할 수 있습니다.
* 대기 시간이 짧은 새 라이브 스트리밍이 라이브 이벤트에서 지원됩니다. 자세한 내용은 [대기 시간](live-event-latency.md)을 참조하세요.
* 라이브 이벤트 미리 보기는 [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화를 지원합니다. 이렇게 하면 DASH 및 HLS 패키징뿐만 아니라 미리 보기에서도 콘텐츠 보호가 가능합니다.
* 라이브 출력은 v2 API의 Program 엔터티보다 사용 방법이 간단합니다. 
* RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
* RTMPS 보안 수집<br/>라이브 이벤트를 만들면 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다.   
* 엔터티에 대한 RBAC(역할 기반 Access Control)를 갖습니다. 

## <a name="changes-from-v2"></a>v2에서 변경된 내용

* v3로 만든 자산의 경우 Media Services는 [Azure Storage 서버 쪽 스토리지 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)만 지원합니다.
    * Media Services에서 제공하는 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)(AES 256)를 사용하는 v2 API로 만든 자산에 v3 API를 사용할 수 있습니다.
    * v3 API를 사용하는 레거시 AES 256 [스토리지 암호화](../previous/media-services-rest-storage-encryption.md)로는 새 자산을 만들 수 없습니다.
* v3 SDK가 Storage SDK에서 분리되었으며, 따라서 사용하고 싶은 Storage SDK를 보다 철저하게 제어하고 버전 문제를 피할 수 있게 되었습니다. 
* v3 API에서 모든 인코딩 비트 전송률은 비트/초입니다. 이것은 v2 Media Encoder Standard 미리 설정과 다른 점입니다. 예를 들어 v2의 비트 전송률은 128(kbps)로 지정되지만 v3에서는 128000(비트/초)이 됩니다. 
* Entities AssetFiles, AccessPolicies 및 IngestManifests는 v3에 없습니다.
* v3에서 IAsset.ParentAssets 속성이 없습니다.
* ContentKeys는 더 이상 엔터티가 아닌 스트리밍 로케이터의 속성입니다.
* Event Grid 지원은 NotificationEndpoints를 대체합니다.
* 다음 엔터티의 이름이 바뀌었습니다.
    * Job Output은 Task를 대체하며, 이제 Job의 일부입니다.
    * Streaming Locator는 Locator를 대체합니다.
    * Live Event는 Channel을 대체합니다.<br/>라이브 이벤트 요금은 라이브 채널 미터를 기반으로 청구됩니다. 자세한 내용은 [대금 청구](live-event-states-billing.md) 및 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.
    * Live Output은 Program을 대체합니다.
* 라이브 출력은 명시적으로 시작할 필요가 없습니다. 생성 시 시작되고 삭제 시 중지됩니다. v2 API에서는 프로그램이 다르게 작동했습니다. 생성 후 시작해야 했습니다.
*  작업에 대 한 정보를 가져오려면 작업이 생성 된 변환 이름을 몰라도 지정 해야 합니다. 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>v2 API와 관련된 기능 격차

v3 API는 v2 API와 관련하여 다음과 같은 기능 격차가 있습니다. 격차를 해소하기 위한 작업이 진행 중입니다.

* v3를 통해 [프리미엄 인코더](../previous/media-services-premium-workflow-encoder-formats.md) 및 레거시 [미디어 분석 프로세서](../previous/media-services-analytics-overview.md)(Azure Media Services Indexer 2 미리 보기, Face Redactor 등)에 액세스할 수 없습니다.<br/>Media Indexer 1 또는 2 미리 보기에서 마이그레이션하려는 고객은 v3 API의 AudioAnalyzer 프리셋을 즉시 사용할 수 있습니다.  이 새 프리셋은 기존 Media Indexer 1 또는 2보다 많은 기능을 포함하고 있습니다. 
* 많은 합니다 [v2의 Media Encoder Standard의 고급 기능](../previous/media-services-advanced-encoding-with-mes.md) Api 현재 사용할 수 없는 v3에서는 같은:
    * 클리핑(주문형 및 라이브 시나리오용)
    * 자산 연결
    * 오버레이
    * 자르기
    * 썸네일 스프라이트
    * 에 오디오가 없을 때 입력 조용한 오디오 트랙 삽입
    * 에 비디오가 없을 경우 입력 비디오 트랙 삽입
* 코드 변환을 사용하는 라이브 이벤트는 현재 슬레이트 삽입 중간 스트림 및 API 호출을 통한 광고 표시기 삽입을 지원하지 않습니다. 

> [!NOTE]
> 이 문서에 책갈피를 지정하고 업데이트를 계속 확인하세요.
 
## <a name="code-differences"></a>코드 차이점

다음 표는 일반적인 시나리오에서 v2와 v3의 코드가 어떻게 다른지 보여줍니다.

|시나리오|V2 API|V3 API|
|---|---|---|
|자산 만들기 및 파일 업로드 |[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|작업 제출|[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>먼저 변환을 만든 후 작업을 제출하는 방법을 보여줍니다.|
|AES 암호화를 사용하여 자산 게시 |1. ContentKeyAuthorizationPolicyOption 만들기<br/>2. ContentKeyAuthorizationPolicy 만들기<br/>3. AssetDeliveryPolicy 만들기<br/>4. Asset 만들기/콘텐츠 업로드 또는 작업 제출/출력 자산 사용<br/>5. Asset에 AssetDeliveryPolicy 연결<br/>6. ContentKey 만들기<br/>7. Asset에 ContentKey 연결<br/>8. AccessPolicy 만들기<br/>9. Locator 만들기<br/><br/>[v2 .NET 예제](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. 콘텐츠 키 정책 만들기<br/>2. Asset 만들기<br/>3. 콘텐츠 업로드 또는 Asset을 JobOutput으로 사용<br/>4. 스트리밍 로케이터 만들기<br/><br/>[v3 .NET 예제](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|작업 세부 정보 가져오기 작업 및 관리 |[V2 사용 하 여 작업 관리](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3 사용 하 여 작업 관리](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>알려진 문제

* 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-sdk), CLI 또는 지원되는 SDK 중 하나를 사용하세요.
* 작업, 특히 비디오 또는 오디오 분석과 관련된 작업의 동시성 및 성능을 제어하려면 계정에서 MRU(미디어 예약 단위)를 프로비전해야 합니다. 자세한 내용은 [미디어 처리 크기 조정](../previous/media-services-scale-media-processing-overview.md)을 참조하세요. [CLI 2.0 for Media Services v3](media-reserved-units-cli-how-to.md), [Azure Portal](../previous/media-services-portal-scale-media-processing.md) 또는 [v2 API](../previous/media-services-dotnet-encoding-units.md)를 사용하여 MRU를 관리할 수 있습니다. Media Services v2를 사용하든 아니면 v3 API를 사용하든, MRU를 프로비전해야 합니다.
* v3 API로 만든 Media Services 엔터티는 v2 API를 사용하여 관리할 수 없습니다.  
* v2 API로 만든 엔터티를 v3 API를 통해 관리하는 것은 좋지 않습니다. 다음은 두 버전의 엔터티가 서로 호환되지 않는 차이점의 예입니다.   
    * v2에서 만든 작업 및 태스크는 변환과 연결되지 않으므로 v3에서 나타나지 않습니다. v3 변환 및 작업으로 전환하는 것이 좋습니다. 전환할 때 비교적 짧은 기간 동안 진행 중인 v2 작업을 모니터링해야 합니다.
    * v2로 만든 채널 및 프로그램(v3에서는 라이브 이벤트 및 라이브 출력으로 매핑)은 v3을 사용하여 계속 관리할 수 없습니다. 편리한 채널 중지점에서 v3 라이브 이벤트 및 라이브 출력으로 전환하는 것이 좋습니다.<br/>현재는 지속적으로 실행 중인 채널을 마이그레이션할 수 없습니다.  

> [!NOTE]
> Media Services 팀이 지속적으로 v3 API를 개선하고 버전 간 차이를 해결하고 있으므로 이 페이지는 계속 유지됩니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

비디오 파일 인코딩 및 스트리밍을 시작하는 것이 얼마나 쉬운 지 보려면 [파일 스트리밍](stream-files-dotnet-quickstart.md)을 확인합니다. 

