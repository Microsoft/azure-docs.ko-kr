---
title: Media Services 용어 및 개념
titleSuffix: Azure Media Services
description: Azure Media Services에 대 한 용어 및 개념에 대해 알아봅니다.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 895e29608a9f0d80b8e03c3bc95c3c74005f7c4c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269712"
---
# <a name="media-services-terminology-and-concepts"></a>Media Services 용어 및 개념

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 항목에서는 Azure Media Services 용어 및 개념에 대 한 간략 한 개요를 제공 합니다. 또한이 문서에서는 Media Services v3 개념 및 기능에 대해 자세히 설명 하는 문서에 대 한 링크도 제공 합니다.

이 항목에서 설명 하는 기본 개념은 개발을 시작 하기 전에 검토 해야 합니다.

> [!NOTE]
> 현재 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 Media Services V3 [라이브 이벤트](live-events-outputs-concept.md)를 관리 하 고, v3 [자산](assets-concept.md)을 보고, 관리 하지 않고, [api에 액세스 하는 방법에 대 한 정보를 가져올](./access-api-howto.md)수 있습니다.
> 다른 모든 관리 작업 (예: [변환 및 작업](transforms-jobs-concept.md) 및 [콘텐츠 보호](content-protection-overview.md))의 경우 [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원 되는 [sdk](media-services-apis-overview.md#sdks)중 하나를 사용 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Media Services v3 용어

|용어|설명|
|---|---|
|라이브 이벤트|**라이브 이벤트** 는 비디오, 오디오 및 실시간 메타 데이터의 라이브 스트림 수집, 코드 변환 (선택 사항) 및 패키징을 위한 파이프라인을 나타냅니다.<br/><br/>Media Services v2 Api에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 는 v 2의 **채널** 엔터티를 대체 합니다. 자세한 내용은 [v2에서 v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조 하세요.|
|스트리밍 끝점/패키징/원본|**스트리밍 끝점** 은 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 응용 프로그램에 직접 배달할 수 있는 동적 (just-in-time) 패키징 및 원본 서비스를 나타냅니다. 일반 스트리밍 미디어 프로토콜 (HLS 또는 대시) 중 하나를 사용 합니다. 또한 **스트리밍 끝점** 은 업계 최고의 drms (디지털 권한 관리 시스템)에 대 한 동적 (just-in-time) 암호화를 제공 합니다.<br/><br/>미디어 스트리밍 업계에서이 서비스를 일반적으로 패키지 작성 또는 **원본**이라고 **합니다.**  이 기능에 대 한 업계의 다른 일반적인 용어에는 JITP (just-in-time) 또는 JITE (just-in-time 암호화)가 포함 됩니다.

## <a name="media-services-v3-concepts"></a>Media Services v3 개념

|개념|Description|링크|
|---|---|---|
|자산 및 콘텐츠 업로드|Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작 하려면 Media Services 계정을 만들고 디지털 파일을 **자산**에 업로드 해야 합니다.|[클라우드 업로드 및 스토리지](storage-account-concept.md)<br/><br/>[자산 개념](assets-concept.md)|
|콘텐츠 인코딩|고품질 디지털 미디어 파일을 자산에 업로드 한 후에는 다양 한 브라우저 및 장치에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. <br/><br/>Media Services v3로 인코딩하려면 **변환** 및 **작업**을 만들어야 합니다.|[변환 및 작업](transforms-jobs-concept.md)<br/><br/>[Media Services를 사용하여 인코딩](encoding-concept.md)|
|콘텐츠 분석(Video Indexer)|Media Services v3를 사용 하면 Media Services v3 사전 설정을 사용 하 여 비디오 및 오디오 파일에서 정보를 추출할 수 있습니다. Media Services v3 사전 설정을 사용 하 여 콘텐츠를 분석 하려면 **변환** 및 **작업**을 만들어야 합니다.<br/><br/>더 자세한 인사이트가 필요하면 [Video Indexer](../video-indexer/index.yml)를 직접 사용해 보세요.|[비디오 및 오디오 파일 분석](analyzing-video-audio-files-concept.md)|
|패키징 및 제공|콘텐츠를 인코딩한 후에는 **동적 패키징을**활용할 수 있습니다. Media Services에서 **스트리밍 끝점** 은 클라이언트 플레이어에 미디어 콘텐츠를 배달 하는 데 사용 되는 동적 패키징 서비스입니다. 클라이언트에서 재생할 수 있도록 출력 자산의 비디오를 재생 하려면 **스트리밍 로케이터** 를 만든 다음 스트리밍 url을 빌드해야 합니다. <br/><br/>자산 이름 외에도 **스트리밍 로케이터**를 만들 때 **스트리밍 정책을**지정 해야 합니다. 스트리밍 **정책을** 사용 하면 스트리밍 **로케이터**에 대 한 스트리밍 프로토콜 및 암호화 옵션 (있는 경우)을 정의할 수 있습니다. 콘텐츠를 라이브 또는 주문형으로 스트리밍할 때 동적 패키징을 사용 합니다. <br/><br/>Media Services **동적 매니페스트** 를 사용 하 여 비디오의 특정 변환이 나 하위 클립을 스트리밍할 수 있습니다.|[동적 패키징](dynamic-packaging-overview.md)<br/><br/>[스트리밍 끝점](streaming-endpoint-concept.md)<br/><br/>[스트리밍 로케이터](streaming-locators-concept.md)<br/><br/>[스트리밍 정책](streaming-policy-concept.md)<br/><br/>[동적 매니페스트](filters-dynamic-manifest-overview.md)<br/><br/>[필터](filters-concept.md)|
|콘텐츠 보호|Media Services를 사용 하면 AES(Advanced Encryption Standard) (AES-128) 또는/와 세 가지 주요 DRM 시스템 (Microsoft PlayReady, Google Widevine Apple FairPlay)을 사용 하 여 동적으로 암호화 된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. <br/><br/>스트림에서 암호화 옵션을 지정 하는 경우 **콘텐츠 키 정책을** 만들고 **스트리밍 로케이터**와 연결 합니다. **콘텐츠 키 정책을** 사용 하 여 콘텐츠 키를 최종 클라이언트에 배달 하는 방법을 구성할 수 있습니다.<br/><br/> 동일한 옵션이 필요할 때마다 정책을 다시 사용 하십시오.| [콘텐츠 키 정책](content-key-policy-concept.md)<br/><br/>[콘텐츠 보호](content-protection-overview.md)|
|라이브 스트리밍|Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전송할 수 있습니다. **라이브 이벤트**는 라이브 비디오 피드 수집 및 처리를 담당합니다. **라이브 이벤트**를 만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력 끝점이 생성 됩니다. 스트림이 **라이브 이벤트**로 흐르는 경우 **자산**, **라이브 출력**및 **스트리밍 로케이터**를 만들어 스트리밍 이벤트를 시작할 수 있습니다. **라이브 출력** 은 **자산** 에 스트림을 보관 하 고 **스트리밍 끝점**을 통해 뷰어에 사용할 수 있도록 합니다. 라이브 이벤트는 *통과*(온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 전송) 또는 *라이브 인코딩*(온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 전송)으로 설정할 수 있습니다. |[라이브 스트리밍 개요](live-streaming-overview.md)<br/><br/>[라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)|
|Event Grid 모니터링|작업의 진행 상황을 확인 하려면 **Event Grid**을 사용 합니다. 또한 Media Services는 라이브 이벤트 유형도 내보냅니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. |[Event Grid 이벤트 처리](reacting-to-media-services-events.md)<br/><br/>[스키마](media-services-event-schemas.md)|
|Azure Monitor 모니터링|Azure Monitor를 사용 하 여 앱을 수행 하는 방법을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링 합니다.|[메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)<br/><br/>[진단 로그 스키마](media-services-diagnostic-logs-schema.md)|
|플레이어 클라이언트|Azure Media Player를 사용 하 여 다양 한 브라우저 및 장치에서 Media Services 여 스트리밍된 미디어 콘텐츠를 재생할 수 있습니다. Azure Media Player는 HTML5, MSE (미디어 원본 확장) 및 EME (암호화 된 미디어 확장)와 같은 업계 표준을 사용 하 여 보강 적응 스트리밍 환경을 제공 합니다. |[Azure Media Player 개요](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 동적 암호화 - .NET](protect-with-aes128.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md)
