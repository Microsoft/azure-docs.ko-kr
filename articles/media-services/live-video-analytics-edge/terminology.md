---
title: IoT Edg 용어에 대 한 라이브 비디오 분석-Azure
description: 이 문서에서는 IoT Edge 용어에 대 한 라이브 비디오 분석의 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 05/30/2020
ms.openlocfilehash: e3a77b69adf2241a4af2652db4edb6673a63b4f0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690615"
---
# <a name="terminology"></a>용어

이 문서에서는 [IoT Edge의 라이브 비디오 분석과](overview.md)관련 된 용어의 개요를 제공 합니다.

## <a name="azure-media-services"></a>Azure Media Services

Azure Media Services은 미디어 솔루션을 빌드할 수 있도록 하는 클라우드 미디어 플랫폼입니다. 이에 대 한 자세한 내용은 [Azure Media Services](../latest/media-services-overview.md) 설명서에서 확인할 수 있습니다.

## <a name="asset"></a>자산

[자산은](../latest/assets-concept.md) Media Services 계정에 연결 된 Azure storage 계정의 blob 컨테이너에 매핑되는 Azure Media Services의 엔터티입니다. 자산과 연결 된 모든 파일은 해당 컨테이너에 blob으로 저장 되며, Media Services는 자산에 연결 된 메타 데이터 (예: 이름, 설명, 만든 시간)를 보관 합니다.

IoT Edge의 Live Video Analytics는 자산을 만들거나 기존 자산에 데이터를 추가할 수 있습니다. 이를 통해에 지 장치에서 비디오 캡처를 사용 하 여 Azure Media Services에 기록 하 고 기존 Azure Media Services 스트리밍 기능을 통해 재생할 수 있는 연속 및 이벤트 기반 비디오 녹화 및 재생 시나리오를 사용할 수 있습니다.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/)는 언어에 제약 받지 않는 고성능 RPC(원격 프로시저 호출) 프레임워크입니다. [프로토콜 버퍼 3](https://developers.google.com/protocol-buffers/docs/proto3) 을 통해 통신을 위한 기본 메시지 교환 형식으로 세션 기반 구조적 스키마를 사용 합니다.

## <a name="media-graph"></a>미디어 그래프

[미디어 그래프](media-graph-concept.md) 를 사용 하 여 미디어를 캡처할 위치, 처리 하는 방법 및 결과를 전달 해야 하는 위치를 정의할 수 있습니다. 이를 통해 원본, 프로세서 및 싱크 노드로 구성 된 그래프를 정의할 수 있으므로 라이브 비디오 분석 응용 프로그램을 빌드하는 기능을 제공 합니다. 미디어 그래프는 미디어 그래프 개념 페이지에서 자세히 설명 합니다.

## <a name="recording"></a>기록 중

보안 카메라에 대 한 비디오 관리 시스템의 컨텍스트에서 비디오 기록은 카메라에서 비디오를 캡처 하 고 모바일 및 브라우저 앱을 통해 후속 보기를 위해 파일 (또는 파일)에 저장 하는 프로세스를 나타냅니다. 비디오 기록은 [연속 비디오 녹화](continuous-video-recording-concept.md) 및 [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)로 분류할 수 있습니다. 이러한 내용은 [비디오 기록](video-recording-concept.md) 개념 페이지에 자세히 설명 되어 있습니다.

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) 는 실시간 스트리밍 프로토콜을 나타냅니다. 실시간 속성을 사용 하 여 데이터를 배달 하는 것을 제어 하기 위한 응용 프로그램 수준 프로토콜입니다. RTSP는 오디오 및 비디오와 같은 실시간 데이터의 제어 된 주문형 제공을 가능 하 게 하는 확장 가능한 프레임 워크를 제공 합니다. 

## <a name="streaming"></a>스트리밍

Netflix, YouTube 등의 서비스에서 모바일 장치에 대 한 비디오를 시청 했다면 스트리밍 비디오가 발생 했습니다. "재생" (충분 한 대역폭이 있는 경우)이 적중 된 후 재생이 시작 되 고 비디오의 타임 라인을 따라 앞뒤로 검색할 수 있습니다. 스트리밍을 사용 하는 경우 조사 중인 비디오 부분만 제공 하 고, 데이터가 서버에서 재생 클라이언트로 전송 되는 동안 뷰어가 비디오 재생을 시작할 수 있도록 하는 것이 좋습니다. Azure Media Services 컨텍스트에서 [스트리밍은](https://en.wikipedia.org/wiki/Streaming_media) 스트리밍 클라이언트 (예: Azure Media Player)에 [Azure Media Services](../azure-media-player/azure-media-player-overview.md) 미디어를 배달 하는 프로세스를 나타냅니다. Azure Media Services를 사용 하 여 [HLS (HTTP 라이브 스트리밍)](https://developer.apple.com/streaming/) 및 [MPEG](https://dashif.org/about/)와 같은 업계 표준의 HTTP 기반의 미디어 스트리밍 프로토콜을 사용 하 여 클라이언트에 비디오를 스트리밍할 수 있습니다. HLS는 [Jw 플레이어](https://www.jwplayer.com/), [hls.js](https://github.com/video-dev/hls.js/), [Videojs](https://videojs.com/), [Google Shaka 플레이어](https://github.com/google/shaka-player)와 같은 Azure Media Player 및 웹 플레이어에서 지원 되거나 Android의 [Exoplayer](https://github.com/google/ExoPlayer) 및 iOS의 [AV 파운데이션](https://developer.apple.com/av-foundation/)을 사용 하 여 모바일 앱에서 기본적으로 렌더링할 수 있습니다. MPEG 대시는 Azure Media Player에서 지원 되며, [이 페이지에서 클라이언트 목록을 찾을](https://dashif.org/clients/)수 있습니다. 

[미디어 그래프](#media-graph)s를 사용 하 여 Azure Media Services 자산에 비디오를 기록 하면 Media Services 스트리밍 기능을 사용 하 여 HLS 및 대시로 비디오 스트림을 제공할 수 있습니다. [비디오 재생](video-playback-concept.md) 문서에서이에 대해 자세히 알아볼 수 있습니다.

## <a name="vms"></a>VM

[Vm](https://en.wikipedia.org/wiki/Video_management_system) 은 비디오 관리 시스템을 나타냅니다. 이러한 시스템은 CCTV 카메라를 구성 및 제어 하 고, 해당 카메라에서 비디오를 캡처 및 기록 하는 데 사용 됩니다. 또한 이러한 시스템은 기록 된 비디오를 재생 하는 클라이언트 응용 프로그램을 제공 합니다.

## <a name="next-steps"></a>다음 단계

[미디어 그래프](media-graph-concept.md)
