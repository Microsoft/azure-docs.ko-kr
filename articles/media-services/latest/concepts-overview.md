---
title: Azure Media Services 개념-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services 개념의 간략 한 개요를 제공 하 고 세부 정보에 대 한 링크를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74a4ee03562963c8a50159f085e4b76b6d461ed9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103854"
---
# <a name="media-services-concepts"></a>Media Services 개념

이 항목에서는 Azure Media Services 개념의 간략 한 개요를 제공 하 고 심층적인 설명은 Media Services v3 개념 및 기능을 사용 하 여 문서에 대 한 링크를 제공 합니다. 이 항목에는 개발을 시작하기 전에 검토해야 하는 기본 개념이 설명되어 있습니다.

> [!NOTE]
> 현재는 Azure Portal을 사용하여 v3 리소스를 관리할 수 없습니다. [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](developers-guide.md) 중 하나를 사용하세요.

## <a name="cloud-upload-and-storage"></a>클라우드 업로드 및 저장

관리, 암호화, 인코딩, 분석 및 Azure에서 미디어 콘텐츠 스트리밍을 시작 하려면 Media Services 계정을 만들고에 디지털 파일을 업로드할 **자산**합니다.

- [클라우드 업로드 및 스토리지](storage-account-concept.md)
- [자산 개념](assets-concept.md)

## <a name="encoding"></a>Encoding

자산에 고품질 디지털 미디어 파일을 업로드 한 후에 다양 한 브라우저 및 장치에서 재생할 수 있는 형식으로 인코딩할 수 있습니다. 

Media Services v3을 사용 하 여 인코딩할 만들 필요가 **변형** 하 고 **작업**합니다.

![변환](./media/encoding/transforms-jobs.png)

- [Transforms 및 Jobs](transforms-jobs-concept.md)
- [Media Services로 인코딩](encoding-concept.md)

## <a name="media-analytics"></a>미디어 분석

비디오 및 오디오 파일을 분석 하려면도 만들려는 **변형** 하 고 **작업**합니다.

- [비디오 및 오디오 파일 분석](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>패키징, 배달, 보호

콘텐츠 인코딩 되 면 있습니다 활용할 수 있습니다 **동적 패키징**합니다. **스트리밍 끝점** 는 미디어 콘텐츠를 클라이언트 플레이어를 제공 하는 데 사용 하는 Media Services의 동적 패키징 서비스입니다. 비디오 출력 자산에 사용할 수 있도록 재생에 대 한 클라이언트를 만들 필요가 **스트리밍 로케이터** 후 스트리밍 Url을 작성 합니다. 

만들 때 합니다 **스트리밍 로케이터**, 자산의 이름 외에도 지정 해야 **스트리밍 정책**합니다. **정책 스트리밍** 사용 하면 스트리밍 프로토콜을 정의 하 고 암호화 옵션 (있는 경우)에 **스트리밍 로케이터**합니다.

라이브 또는 주문형 콘텐츠를 스트림할 수 있는지 여부를 동적 패키징을 사용 됩니다. 다음 다이어그램은 동적 패키징 워크플로 사용 하 여 주문형 스트리밍 보여줍니다.

![동적 패키징](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Media Services를 사용 하 여 Advanced Encryption Standard (AES-128)로 동적 암호화 된 라이브 및 주문형 콘텐츠를 제공할 수 있습니다 또는 / 및 세 가지 주요 디지털 rights management (DRM) 시스템 중 하나: 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다.

스트림 암호화 옵션을 지정 하는 경우 만들기를 **콘텐츠 키 정책** 와 연결 프로그램 **스트리밍 로케이터**합니다. 합니다 **콘텐츠 키 정책** 최종 클라이언트에 콘텐츠 키 배달 방법을 구성할 수 있습니다.

다음 이미지는 Media Services 콘텐츠 보호 워크플로를 보여 줍니다. 

![콘텐츠 보호](./media/content-protection/content-protection.svg)

&#42;동적 암호화는 AES-128 "암호화 되지 않은 키", CBCS, 및 CENC를 지원합니다. 

Media Services를 사용할 수 있습니다 **동적 매니페스트** 특정 변환 또는 하위 클립 비디오를 스트림 하 합니다. 다음 예제에서는 7개의 ISO MP4 비디오 변환(180p에서 1080p까지)으로 메자닌 자산을 인코드하는 데 인코더가 사용되었습니다. 인코딩된 자산은 다음 스트리밍 프로토콜 중 하나로 동적으로 패키징할 수 있습니다. HLS, MPEG DASH 및 부드러운 스트리밍  다이어그램의 맨 위에 필터가 없는 자산을 위한 HLS 매니페스트가 나와 있습니다(7개의 모든 변환 포함).  왼쪽 아래에는 "ott" 필터가 적용된 HLS 매니페스트가 표시되어 있습니다. “ott” 필터는 1Mbps 미만의 모든 비트 전송률을 제거하도록 지정하며, 이 때문에 응답에서 맨 아래의 두 품질 수준이 제거됩니다. 오른쪽 아래에는 "mobile" 필터가 적용된 HLS 매니페스트가 나와 있습니다. "mobile" 필터는 해상도가 720p보다 높은 변환을 제거하도록 지정하며, 이 때문에 두 개의 1080p 변환이 제거됩니다.

![변환 필터링](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [동적 패키징](dynamic-packaging-overview.md)
- [스트리밍 엔드포인트](streaming-endpoint-concept.md)
- [스트리밍 로케이터](streaming-locators-concept.md)
- [스트리밍 정책](streaming-policy-concept.md)
- [콘텐츠 키 정책](content-key-policy-concept.md)
- [콘텐츠 보호](content-protection-overview.md)
- [동적 매니페스트](filters-dynamic-manifest-overview.md)
- [필터](filters-concept.md)

## <a name="live-streaming"></a>라이브 스트리밍

Azure Media Services를 사용하면 Azure 클라우드에서 고객에게 라이브 이벤트를 전달할 수 있습니다. **라이브 이벤트**는 라이브 비디오 피드 수집 및 처리를 담당합니다. 만들 때를 **라이브 이벤트**, 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 입력된 끝점이 만들어집니다. 에 들어오는 스트림이 있으면 합니다 **라이브 이벤트**를 만들어 스트리밍 이벤트를 시작할 수 있습니다는 **자산**, **Live 출력**, 및 **스트리밍 로케이터** . **출력 live** 에 스트림을 보관 됩니다 합니다 **자산** 을 통해 시청자에 게 제공 하 고는 **스트리밍 끝점**합니다. A **라이브 이벤트** 두 가지 유형 중 하나일 수 있습니다: **통과** 하 고 **라이브 인코딩**.

다음 이미지에서는 통과 형식 워크플로를 보여 줍니다.

![통과](./media/live-streaming/pass-through.svg)

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 이벤트 및 라이브 출력](live-events-outputs-concept.md)

## <a name="monitoring"></a>모니터링

### <a name="event-grid"></a>Event Grid

작업의 진행률을 보려면 사용할지 **Event Grid**합니다. 또한 Media Services는 라이브 이벤트 유형을 내보냅니다. Event Grid를 사용하면 앱이 사용자 지정 원본뿐만 아니라 거의 모든 Azure 서비스의 이벤트에 대해 수신 대기하고 대응할 수 있습니다. 

- [Event Grid 이벤트를 처리합니다.](reacting-to-media-services-events.md)
- [스키마](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

모니터 메트릭 및 진단 로그 도움이 되는 응용 프로그램 Azure Monitor를 사용 하 여 수행 하는 방법을 이해 합니다.

- [메트릭 및 진단 로그](media-services-metrics-diagnostic-logs.md)
- [진단 로그 스키마](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>플레이어 클라이언트

다양 한 브라우저 및 장치에서 Media Services에서 스트리밍 미디어 콘텐츠를 재생 하려면 Azure Media Player를 사용할 수 있습니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, 미디어 원본 확장(MSE) 및 암호화된 미디어 확장(EME) 등의 업계 표준을 활용합니다. 

- [Azure Media Player 개요](use-azure-media-player.md)

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [원격 파일 및 스트림 비디오 인코딩 – REST](stream-files-tutorial-with-rest.md)
* [업로드된 파일 및 스트림 비디오 인코딩 – .NET](stream-files-tutorial-with-api.md)
* [라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
* [비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 동적 암호화 - .NET](protect-with-aes128.md)
* [다중 DRM으로 동적으로 암호화 - .NET](protect-with-drm.md) 
