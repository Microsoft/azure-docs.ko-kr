---
title: Azure Media Services를 사용하는 라이브 스트리밍 개요 | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용하는 라이브 스트리밍 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a9d0daaacb046df7943202775adc77bc912cce11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217576"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Media Services를 사용하는 라이브 스트리밍 개요

> [!NOTE]
> 2018년 5월 12일부터 라이브 채널은 RTP/MPEG-2 전송 스트림 수집 프로토콜을 더 이상 지원하지 않습니다. RTP/MPEG-2에서 RTMP 또는 조각난 MP4(부드러운 스트리밍) 수집 프로토콜로 마이그레이션하세요.

## <a name="overview"></a>개요

Azure Media Services를 사용하여 라이브 스트리밍 이벤트를 제공할 때 다음 구성 요소가 일반적으로 포함됩니다.

* 이벤트를 브로드캐스트하는 데 사용되는 카메라
* 카메라에서 라이브 스트리밍 서비스로 보내는 스트림까지 신호를 변환하는 라이브 비디오 인코더.

    필요에 따라 여러 라이브 시간에 동기화된 인코더. 뛰어난 고가용성과 체감 품질이 요구되는 특정 중요한 라이브 이벤트의 경우, 데이터 손실 없이 원활하게 장애 조치(failover)를 실현하기 위해 시간이 동기화된 활성-활성 중복 인코더를 사용하는 것이 바람직합니다.
* 다음을 수행할 수 있도록 하는 라이브 스트리밍 서비스:

  * 다양한 라이브 스트리밍 프로토콜(예: RTMP 또는 부드러운 스트리밍)을 사용하여 라이브 콘텐츠 수집
  * (선택 사항) 스트림을 적응 비트 전송률 스트림으로 인코딩
  * 라이브 스트림 미리 보기
  * 나중에 스트리밍하기 위해 수집된 콘텐츠 기록 및 저장(주문형 비디오)
  * 일반적인 스트리밍 프로토콜(예: MPEG DASH, 부드러운, HLS)을 통해 고객에게 직접 또는 추가 배포를 위해 CDN(Content Delivery Network)에 콘텐츠 배달.

**Microsoft Azure Media Services**(AMS)에서는 라이브 스트리밍 콘텐츠를 수집, 인코딩, 미리 보기, 저장 및 배달하는 기능을 제공합니다.

콘텐츠를 고객에게 배달할 때는 서로 다른 네트워크 조건에 따라 다양한 디바이스에 고화질 영상을 제공하는 것이 목표입니다. 이를 위해 라이브 인코더를 사용하여 사용자의 스트림을 다중 비트 전송률(적응 비트 전송률) 비디오 스트림으로 인코딩합니다.  여러 디바이스에서 스트리밍을 관리하려면 Media Services [동적 패키징](media-services-dynamic-packaging-overview.md) 을 사용하여 스트림을 여러 프로토콜에 동적으로 다시 패키징합니다. Media Services서 다음과 같은 적응 비트 전송률 스트리밍 기술 제공을 지원합니다. HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH

Azure Media Services에서 **채널**, **프로그램** 및 **스트리밍 끝점**은 수집, 형식 지정, DVR, 보안, 확장성 및 중복성을 포함한 라이브 스트리밍 기능 전반을 처리합니다.

**채널** 은 라이브 스트리밍 콘텐츠를 처리하기 위한 파이프라인을 나타냅니다. 채널은 다음 방식으로 라이브 입력 스트림을 수신할 수 있습니다.

* 온-프레미스 라이브 인코더가 다중 비트 전송률 **RTMP** 또는 **부드러운 스트리밍**(조각화된 MP4)을 **통과** 전달을 위해 구성된 채널에 보냅니다. 어떠한 추가적인 처리 없이 수집된 스트림이 **채널**을 통과하는 경우를 **통과** 전달이라고 합니다. 다중 비트 전송률 부드러운 스트리밍을 출력하는 라이브 인코더인 MediaExcel, Ateme, Imagine Communications, Envivio, Cisco 및 Elemental을 사용할 수 있습니다. RTMP를 출력하는 라이브 인코더는 Adobe FMLE(Flash Media Live Encoder), Telestream Wirecast, Haivision, Teradek 및 Tricaster 트랜스코더입니다.  또한 라이브 인코더는 라이브 인코딩이 사용되지 않는 채널에 단일 비트 전송률 스트림을 전송할 수 있지만 이 방법은 권장되지 않습니다. 요청된 경우 Media Services는 고객에게 스트림을 배달합니다.

  > [!NOTE]
  > 통과 방법을 사용하면 긴 기간 동안 여러 이벤트를 수행하고 온-프레미스 인코더에 이미 투자한 경우 라이브 스트리밍을 수행하는 가장 경제적인 방법입니다. [가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 세부 정보를 참조하세요.
  > 
  > 
* 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 RTMP 또는 부드러운 스트리밍(조각난 MP4) 형식의 하나로 Media Services를 통해 라이브 인코딩을 수행할 수 있는 LiveEvent에 전송합니다. RTMP 출력이 있는 다음 라이브 인코더는 Telestream Wirecast, FMLE 형식의 채널과 작동하는 것으로 알려져 있습니다. 그러면 채널은 들어오는 단일 비트 전송률 스트림을 다중 비트 전송률(적응) 비디오 스트림으로 라이브 인코딩합니다. 요청된 경우 Media Services는 고객에게 스트림을 배달합니다.

Media Services 2.10 릴리스부터, 채널을 만들 때 채널이 입력 스트림을 받는 방법과 채널이 스트림의 라이브 인코딩을 수행할지 여부를 지정할 수 있습니다. 다음 두 가지 옵션을 사용할 수 있습니다.

* **없음** (통과) – 다중 비트 전송률 스트림(통과 스트림)을 출력할 온-프레미스 라이브 인코더를 사용할 계획인 경우 이 값을 지정합니다. 이 경우 들어오는 스트림이 인코딩 없이 출력으로 전달됩니다. 이것이 2.10 릴리스 이전의 채널 동작입니다.  
* **표준** – Media Services를 사용하여 단일 비트 전송률 라이브 스트림을 다중 비트 전송률 스트림으로 인코딩할 계획인 경우 이 값을 선택합니다. 이 방법은 자주 수행하지 않는 이벤트를 신속하게 강화하는 데 더욱 경제적입니다. 라이브 인코딩의 청구 영향이 있고 라이브 인코딩 채널을 "실행 중" 상태로 두면 요금이 청구됨을 기억해야 합니다.  시간당 추가 요금 청구를 방지하려면 라이브 스트리밍 이벤트가 완료된 직후 실행 중인 채널을 중지하는 것이 좋습니다.

## <a name="comparison-of-channel-types"></a>채널 형식 비교

다음 표에서는 Media Services에서 지원되는 두 개의 채널 형식을 비교하는 지침을 제공합니다.

| 기능 | 통과 채널 | 표준 채널 |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |아닙니다. |예 |
| 최대 해상도, 계층 수 |1080p, 8계층, 60+fps |720p, 6계층, 30fps |
| 입력 프로토콜 |RTMP, 부드러운 스트리밍 |RTMP, 부드러운 스트리밍 |
| 가격 |[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭 | [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) |
| 최대 실행 시간 |연중 무휴 |8시간 |
| 슬레이트 삽입 지원 |아닙니다. |예 |
| 광고 신호 지원 |아닙니다. |예 |
| 통과 CEA 608/708 캡션 |예 |예 |
| 균일하지 않은 입력 GOP에 대한 지원 |예 |아니요 - 입력은 고정된 2초 GOP여야 함 |
| 변수 프레임 속도 입력에 대한 지원 |예 |아니요 - 입력은 고정된 프레임 속도여야 함.<br/>예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 하지만 인코더는 10프레임/초까지 떨어질 수 없습니다. |
| 입력 피드가 손실될 경우 채널 자동 차단 |아닙니다. |12시간 동안 프로그램 실행이 없는 경우 |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>온-프레미스 인코더(통과)에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업

다음 다이어그램에서는 **통과** 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

자세한 내용은 [온-프레미스 인코더의 다중 비트 전송률 라이브 스트림을 수신하는 채널 사용](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업

다음 다이어그램에서는 채널이 Media Services를 통해 라이브 인코딩을 수행할 수 있는 라이브 스트리밍 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

## <a name="description-of-a-channel-and-its-related-components"></a>채널 및 관련 구성 요소에 대한 설명

### <a name="channel"></a>채널

Media Services에서, [채널](https://docs.microsoft.com/rest/api/media/operations/channel)은 라이브 스트리밍 콘텐츠 처리를 담당합니다. 채널은 라이브 트랜스코더에 제공될 입력 엔드포인트(수집 URL)를 제공합니다. 채널은 라이브 트랜스코더에서 라이브 입력 스트림을 수신하여 하나 이상의 StreamingEndpoints를 통해 스트리밍하는 데 사용할 수 있도록 합니다. 또한 채널은 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다.

채널을 만들 때 수집 URL 및 미리 보기 URL을 얻을 수 있습니다. 이러한 URL을 얻으려면 채널이 시작됨 상태로 있을 필요가 없습니다. 라이브 트랜스코더에서 채널로 데이터를 푸시할 준비가 되면 채널을 시작해야 합니다. 라이브 트랜스코더에서 데이터 수집을 시작하면 스트림을 미리 볼 수 있습니다.

각 Media Services 계정에는 여러 채널, 여러 프로그램 및 여러 StreamingEndpoints가 포함될 수 있습니다. 대역폭 및 보안 요구 사항에 따라 하나 이상의 채널에 StreamingEndpoint 서비스를 전용으로 사용할 수 있습니다. 모든 StreamingEndpoint는 모든 채널에서 가져올 수 있습니다.

채널을 만들 때 4개의 숫자를 사용하는 IpV4 주소, CIDR 주소 범위 형식 중 하나로 허용된 IP 주소를 지정할 수 있습니다.

### <a name="program"></a>프로그램
[프로그램](https://docs.microsoft.com/rest/api/media/operations/program) 에서는 라이브 스트림의 세그먼트 게시 및 저장을 제어할 수 있습니다. 채널은 프로그램을 관리합니다. 채널 및 프로그램 관계는 기존 미디어와 매우 유사하여 채널에는 일정한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널에 있는 일부 시간 제한 이벤트로 지정됩니다.
**ArchiveWindowLength** 속성을 설정하여 프로그램에 대해 기록된 콘텐츠를 유지할 시간을 지정할 수 있습니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다.

또한 ArchiveWindowLength는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 나타냅니다. 프로그램은 지정된 시간 동안 실행되지만 기간 길이보다 늦는 콘텐츠는 계속 삭제됩니다. 또한 이 속성의 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

각 프로그램은 자산에 연결됩니다. 프로그램을 게시하려면 연결된 자산에 대한 로케이터를 만들어야 합니다. 이 로케이터가 있으면 클라이언트에 제공할 수 있는 스트리밍 URL을 작성할 수 있습니다.

채널은 동시 실행 프로그램을 최대 세 개까지 지원하므로 동일한 들어오는 스트림의 보관 파일을 여러 개 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 예를 들어 비즈니스 요구 사항에 따라 6시간의 프로그램을 보관하고 마지막 10분만 브로드캐스트해야 할 수 있습니다. 이렇게 하려면 두 개의 동시 실행 프로그램을 만들어야 합니다. 한 프로그램은 6시간의 이벤트를 보관하도록 설정하고 프로그램은 게시하지 않습니다. 다른 프로그램은 10분 동안을 보관하도록 설정하고 프로그램을 게시합니다.

## <a name="billing-implications"></a>요금 청구
채널은 상태가 API를 통한 "실행 중"으로 전환되면 그 즉시 청구되기 시작됩니다.  

다음 표에서는 채널 상태가 API 및 Azure Portal에서 청구 상태에 매핑되는 방식을 보여줍니다. API와 포털 UX의 상태는 서로 약간 다릅니다. 채널이 API를 통한 "실행 중" 상태 또는 Azure Portal의 "준비" 또는 "스트리밍" 상태에 있는 한, 요금이 청구됩니다.

채널이 요금을 청구하지 못하게 하려면 API를 통해 또는 Azure Portal에서 채널을 중지해야 합니다.
채널의 작업이 끝나면 채널을 중지할 책임이 있습니다. 채널을 중지하지 않으면 요금이 계속 청구됩니다.

> [!NOTE]
> 표준 채널에서 작업할 때 AMS는 입력 피드가 손실된 후 12시간 동안 실행 중인 프로그램이 없으면 "실행 중" 상태인 모든 채널을 자동 차단합니다. 그러나 채널이 "실행 중" 상태였던 시간에 대한 요금은 청구됩니다.
>
>

### <a id="states"></a>채널 상태 및 상태가 청구 모드에 매핑되는 방식
채널의 현재 상태입니다. 가능한 값은 다음과 같습니다.

* **중지됨**. 이는 채널을 만든 후 초기 상태입니다(포털에서 자동 시작을 선택하지 않은 경우). 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서 채널 속성을 업데이트할 수 있지만 스트리밍은 허용되지 않습니다.
* **시작 중**. 채널이 시작 중입니다. 이 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다. 오류가 발생하는 경우 채널이 중단된 상태를 반환합니다.
* **실행 중**. 라이브 스트림 처리에 채널을 사용할 수 있습니다. 이제 사용 요금이 청구됩니다. 추가 요금 청구를 방지하기 위해 채널을 중지해야 합니다.
* **중지 중**. 채널이 중지 중입니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다.
* **삭제 중**. 채널이 삭제 중입니다. 이 일시적인 상태에서는 요금이 청구되지 않습니다. 이 상태에서는 업데이트 또는 스트리밍이 허용되지 않습니다.

다음 표에서는 채널 상태가 청구 모드에 매핑되는 방식을 보여 줍니다.

| 채널 상태 | 포털 UI 표시기 | 요금이 청구됩니까? |
| --- | --- | --- |
| 시작 중 |시작 중 |없음(일시적인 상태) |
| 실행 중 |준비(실행 중인 프로그램이 없음)<br/>또는<br/>스트리밍(실행 중인 프로그램이 하나 이상임) |예 |
| 중지 중 |중지 중 |없음(일시적인 상태) |
| 중지됨 |중지됨 |아닙니다. |

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>관련된 항목
[Azure Media Services 조각화된 MP4 라이브 수집 사양](media-services-fmp4-live-ingest-overview.md)

[Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)

[온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업](media-services-live-streaming-with-onprem-encoders.md)

[할당량 및 제한 사항](media-services-quotas-and-limitations.md)  

[Media Services 개념](media-services-concepts.md)
