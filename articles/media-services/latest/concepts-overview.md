---
title: Azure Media Services 용어 및 개념-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services 용어 및 개념에 대 한 간략 한 개요를 제공 하 고 자세한 정보를 제공 하는 링크를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970039"
---
# <a name="media-services-concepts"></a>Media Services 개념

이 항목에서는 Azure Media Services 용어 및 개념에 대 한 간략 한 개요를 제공 합니다. 또한이 문서에서는 Media Services v3 개념 및 기능에 대해 자세히 설명 하는 문서에 대 한 링크도 제공 합니다. 

이 항목에는 개발을 시작하기 전에 검토해야 하는 기본 개념이 설명되어 있습니다.

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용하세요.

## <a name="terminology"></a>용어

이 섹션에서는 몇 가지 일반적인 업계 용어를 Media Services v3 API에 매핑하는 방법을 보여 줍니다.

### <a name="live-event"></a>라이브 이벤트

**라이브 이벤트** 는 비디오, 오디오 및 실시간 메타 데이터의 라이브 스트림 수집, 코드 변환 (선택 사항) 및 패키징을 위한 파이프라인을 나타냅니다.

Media Services v2 Api에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 는 v 2의 **채널** 엔터티를 대체 합니다. 자세한 내용은 [v2에서 v3로 마이그레이션](migrate-from-v2-to-v3.md)을 참조 하세요.

### <a name="streaming-endpoint-packaging-and-origin"></a>스트리밍 엔드포인트 (패키징 및 원본)

**스트리밍 엔드포인트** 은 일반 스트리밍 미디어 프로토콜 (HLS 또는 대시) 중 하나를 사용 하 여 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 응용 프로그램에 직접 배달할 수 있는 동적 (just-in-time) 패키징 및 원본 서비스를 나타냅니다. 또한 **스트리밍 엔드포인트** 은 업계 최고의 drms에 동적 (just-in-time) 암호화를 제공 합니다.

미디어 스트리밍 업계에서이 서비스를 일반적으로 패키지 작성 또는 **원본**이라고 **합니다.**  이 기능에 대 한 업계의 다른 일반적인 용어에는 JITP (just-in-time) 또는 JITE (Just-in-time 암호화)가 포함 됩니다. 
 
## <a name="cloud-upload-and-storage"></a>클라우드 업로드 및 스토리지

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작 하려면 Media Services 계정을 만들고 디지털 파일을 **자산**에 업로드 해야 합니다.

- [클라우드 업로드 및 스토리지](storage-account-concept.md)
- [자산 개념](assets-concept.md)

## <a name="encoding"></a>Encoding

고품질 디지털 미디어 파일을 자산에 업로드 한 후에는 다양 한 브라우저 및 장치에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. 

Media Services v3로 인코딩하려면 **변환** 및 **작업**을 만들어야 합니다.

![변환](./media/encoding/transforms-jobs.png)

- [Transforms 및 Jobs](transforms-jobs-concept.md)
- [Media Services 인코딩](encoding-concept.md)

## <a name="media-analytics"></a>미디어 분석

비디오 및 오디오 파일을 분석 하려면 **변환** 및 **작업도**만들어야 합니다.

- [비디오 및 오디오 파일 분석](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>패키징, 배달, 보호

콘텐츠를 인코딩한 후에는 **동적 패키징을**활용할 수 있습니다. Media Services에서 **스트리밍 엔드포인트**/원본은 클라이언트 플레이어에 미디어 콘텐츠를 배달 하는 데 사용 되는 동적 패키징 서비스입니다. 클라이언트에서 재생할 수 있도록 출력 자산의 비디오를 재생 하려면 **스트리밍 로케이터** 를 만든 다음 스트리밍 url을 빌드해야 합니다. 

자산 이름 외에도 **스트리밍 로케이터**를 만들 때 **스트리밍 정책을**지정 해야 합니다. 스트리밍 **정책을** 사용 하면 스트리밍 **로케이터**에 대 한 스트리밍 프로토콜 및 암호화 옵션 (있는 경우)을 정의할 수 있습니다.

콘텐츠를 라이브 또는 주문형으로 스트리밍할 때 동적 패키징을 사용 합니다. 다음 다이어그램에서는 동적 패키징 워크플로를 사용 하는 주문형 스트리밍을 보여 줍니다.

![동적 패키징](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Media Services를 사용 하면 AES(Advanced Encryption Standard) (AES-128) 또는/와 세 가지 주요 DRM (디지털 권한 관리) 시스템 (Microsoft PlayReady, Google Widevine Apple FairPlay)을 사용 하 여 동적으로 암호화 된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다.

스트림에서 암호화 옵션을 지정 하는 경우 **콘텐츠 키 정책을** 만들고 **스트리밍 로케이터**와 연결 합니다. **콘텐츠 키 정책을** 사용 하 여 콘텐츠 키를 최종 클라이언트에 배달 하는 방법을 구성할 수 있습니다.

다음 이미지는 Media Services 콘텐츠 보호 워크플로를 보여 줍니다. 

![콘텐츠 보호](./media/content-protection/content-protection.svg)

&#42;동적 암호화는 AES-128 "clear key", CBCS 및 CBCS를 지원 합니다. 

Media Services **동적 매니페스트** 를 사용 하 여 비디오의 특정 변환이 나 하위 클립을 스트리밍할 수 있습니다. 다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 HLS, MPEG DASH, 부드러운 스트리밍 등의 스트리밍 프로토콜 중 하나로 동적으로 패키징할 수 있습니다.  다이어그램의 맨 위에 필터가 없는 자산을 위한 HLS 매니페스트가 나와 있습니다(7개의 모든 변환 포함).  왼쪽 아래에는 "ott" 필터가 적용된 HLS 매니페스트가 표시되어 있습니다. “ott” 필터는 1Mbps 미만의 모든 비트 전송률을 제거하도록 지정하며, 이 때문에 응답에서 맨 아래의 두 품질 수준이 제거됩니다. 오른쪽 아래에는 "mobile" 필터가 적용된 HLS 매니페스트가 나와 있습니다. "mobile" 필터는 해상도가 720p보다 높은 변환을 제거하도록 지정하며, 이 때문에 두 개의 1080p 변환이 제거됩니다.

![변환 필터링](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [동적 패키징](dynamic-packaging-overview.md)
- [스트리밍 엔드포인트](streaming-endpoint-concept.md)
- [스트리밍 로케이터](streaming-locators-concept.md)
- [스트리밍 정책](streaming-policy-concept.md)
- [콘텐츠 키 정책](content-key-policy-concept.md)
- [콘텐츠 보호](content-protection-overview.md)
- [동적 매니페스트](filters-dynamic-manifest-overview.md)
- [필터](filters-concept.md)

> [!NOTE]
> Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="live-streaming"></a>라이브 스트리밍

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. **라이브 이벤트**는 라이브 비디오 피드 수집 및 처리를 담당합니다. **라이브 이벤트**를 만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력 엔드포인트이 생성 됩니다. 스트림이 **라이브 이벤트**로 흐르는 경우 **자산**, **라이브 출력**및 **스트리밍 로케이터**를 만들어 스트리밍 이벤트를 시작할 수 있습니다. **라이브 출력** 은 **자산** 에 스트림을 보관 하 고 **스트리밍 엔드포인트**을 통해 뷰어에 사용할 수 있도록 합니다. **라이브 이벤트** 는 **통과** 와 **라이브 인코딩의**두 가지 유형 중 하나일 수 있습니다.

다음 이미지는 통과 형식 워크플로를 보여 줍니다.

![통과](./media/live-streaming/pass-through.svg)

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)

## <a name="monitoring"></a>모니터링

### <a name="event-grid"></a>Event Grid

작업의 진행 상황을 확인 하려면 **Event Grid**를 사용 해야 합니다. 또한 Media Services는 라이브 이벤트 유형도 내보냅니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 반응할 수 있습니다. 

- [Event Grid 이벤트 처리](reacting-to-media-services-events.md)
- [스키마](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Azure Monitor에서 응용 프로그램을 수행 하는 방법을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링 합니다.

- [메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)
- [진단 로그 스키마](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>플레이어 클라이언트

Azure Media Player를 사용 하 여 다양 한 브라우저 및 장치에서 Media Services 여 스트리밍된 미디어 콘텐츠를 재생할 수 있습니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, 미디어 원본 확장(MSE) 및 암호화된 미디어 확장(EME) 등의 업계 표준을 활용합니다. 

- [Azure Media Player 개요](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 동적 암호화 - .NET](protect-with-aes128.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md) 
