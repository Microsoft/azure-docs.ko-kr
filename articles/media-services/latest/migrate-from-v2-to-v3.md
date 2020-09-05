---
title: Azure Media Services v2에서 v3으로 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다. 또한 이 문서에서는 Media Services v2 to v3에서 이동하기 위한 마이그레이션 지침을 제공합니다.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e692eef45f8a0b4a8918e7255fa1ac85d4ad064c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265833"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 Media Services v 2에서 v3로의 마이그레이션 지침을 제공 합니다.

현재 비디오 서비스가 [레거시 Media Services v2 API](../previous/media-services-overview.md) 위에 배포된 경우 v3 API로 마이그레이션하기 전에 다음 지침 및 고려 사항을 검토해야 합니다. v3 API에는 개발자 경험과 Media Services 기능을 향상하는 여러 이점과 새 기능이 있습니다. 그러나 이 문서의 [알려진 문제](#known-issues) 섹션에서 언급했듯이, API 버전 간의 변경 사항으로 인해 몇 가지 제한이 있습니다. Media Services 팀이 지속적으로 v3 API를 개선하고 버전 간 차이를 해결하고 있으므로 이 페이지는 계속 유지됩니다. 

## <a name="prerequisites"></a>필수 구성 요소

* [V2 및 v3 Media Services](media-services-v2-vs-v3.md) 검토
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Media Services v3의 혜택
  
### <a name="api-is-more-approachable"></a>더 접근하기 쉬운 API

*  v3은 Azure Resource Manager에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. Azure Resource Manager 템플릿을 사용하여 변환, 스트리밍 엔드포인트, 라이브 이벤트 등을 만들고 배포할 수 있습니다.
* [Openapi 사양 (이전의 Swagger)](https://aka.ms/ams-v3-rest-sdk) 문서입니다.
    파일 기반 인코딩을 포함하여 모든 서비스 구성 요소의 스키마를 공개합니다.
* [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) 및 Ruby에 SDK를 사용할 수 있습니다.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) 통합을 통해 간단한 스크립팅을 지원합니다.

### <a name="new-features"></a>새로운 기능

* 파일 기반 작업 처리의 경우 HTTP(S) URL을 입력으로 사용할 수 있습니다.<br/>아직 콘텐츠를 Azure에 저장하거나 자산을 만들 필요가 없습니다.
* 파일 기반 작업 처리를 위한 [변환](transforms-jobs-concept.md) 개념을 도입합니다. 변환을 사용하여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테넌트 간에 처리 설정을 격리할 수 있습니다.
* [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화 설정이 각기 다른 [스트리밍 로케이터](streaming-locators-concept.md)가 자산에 여러 개 있을 수 있습니다.
* [콘텐츠 보호](content-key-policy-concept.md) 는 다중 키 기능을 지원 합니다.
* 단일 비트 전송률 기여 피드를 다중 비트 전송률이 있는 출력 스트림으로 코드 변환하기 위해 Media Services를 사용할 때 최대 24시간 분량의 라이브 이벤트를 스트리밍할 수 있습니다.
* 대기 시간이 짧은 새 라이브 스트리밍이 라이브 이벤트에서 지원됩니다. 자세한 내용은 [대기 시간](live-event-latency.md)을 참조하세요.
* 라이브 이벤트 미리 보기는 [동적 패키징](dynamic-packaging-overview.md) 및 동적 암호화를 지원합니다. 이렇게 하면 DASH 및 HLS 패키징뿐만 아니라 미리 보기에서도 콘텐츠 보호가 가능합니다.
* 라이브 출력은 v2 API의 Program 엔터티보다 사용 방법이 간단합니다. 
* RTMP 지원 향상(향상된 안정성 및 더 많은 소스 인코더 지원)
* RTMPS 보안 수집<br/>라이브 이벤트를 만들면 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰(AppId)이 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다.   
* 엔터티에 대한 RBAC(역할 기반 Access Control)를 갖습니다. 

## <a name="known-issues"></a>알려진 문제

*  현재 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 다음을 수행할 수 있습니다.

    * Media Services v3 [라이브 이벤트](live-events-outputs-concept.md)관리 
    * v3 [자산](assets-concept.md)보기 (관리 안 함) 
    * [api에 액세스 하는 방법에 대 한 정보를 가져옵니다](./access-api-howto.md). 

    다른 모든 관리 작업 (예: [변환 및 작업](transforms-jobs-concept.md) 및 [콘텐츠 보호](content-protection-overview.md))의 경우 [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원 되는 [sdk](media-services-apis-overview.md#sdks)중 하나를 사용 합니다.
* 작업, 특히 비디오 또는 오디오 분석과 관련된 작업의 동시성 및 성능을 제어하려면 계정에서 MRU(미디어 예약 단위)를 프로비전해야 합니다. 자세한 내용은 [미디어 처리 크기 조정](../previous/media-services-scale-media-processing-overview.md)을 참조하세요. [Media Services v3에 대해 CLI 2.0](media-reserved-units-cli-how-to.md)을 사용 하거나 [Azure Portal](../previous/media-services-portal-scale-media-processing.md)를 사용 하거나 [v2 Api](../previous/media-services-dotnet-encoding-units.md)를 사용 하 여 mru를 관리할 수 있습니다. Media Services v2를 사용하든 아니면 v3 API를 사용하든, MRU를 프로비전해야 합니다.
* v3 API로 만든 Media Services 엔터티는 v2 API를 사용하여 관리할 수 없습니다.  
* V2 API의 모든 엔터티가 V3 API에 자동으로 표시 되는 것은 아닙니다.  다음은 호환 되지 않는 두 버전의 엔터티에 대 한 예입니다.  
    * v2에서 만든 작업 및 태스크는 변환과 연결되지 않으므로 v3에서 나타나지 않습니다. v3 변환 및 작업으로 전환하는 것이 좋습니다. 전환할 때 비교적 짧은 기간 동안 진행 중인 v2 작업을 모니터링해야 합니다.
    * v2로 만든 채널 및 프로그램(v3에서는 라이브 이벤트 및 라이브 출력으로 매핑)은 v3을 사용하여 계속 관리할 수 없습니다. 편리한 채널 중지점에서 v3 라이브 이벤트 및 라이브 출력으로 전환하는 것이 좋습니다.<br/>현재는 지속적으로 실행 중인 채널을 마이그레이션할 수 없습니다.  

> [!NOTE]
> Media Services 팀이 지속적으로 v3 API를 개선하고 버전 간 차이를 해결하고 있으므로 이 페이지는 계속 유지됩니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[자습서: URL을 기반으로 원격 파일 인코딩 및 비디오 스트리밍-.NET](stream-files-dotnet-quickstart.md)
