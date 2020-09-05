---
title: CDN 통합을 사용 하 여 콘텐츠 스트리밍
titleSuffix: Azure Media Services
description: CDN 통합을 사용 하 여 콘텐츠를 스트리밍하는 방법과 프리페치 및 원본 지원 CDN-프리페치에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: cd2b9848ef1358c1aeaaed43e55d9914ac701564
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267108"
---
# <a name="stream-content-with-cdn-integration"></a>CDN 통합을 사용 하 여 콘텐츠 스트리밍

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다.  

CDN은 코덱 당 Media Services [스트리밍 끝점 (원본)](streaming-endpoint-concept.md) , 스트리밍 프로토콜 당, 비트 전송률, 컨테이너 형식 및 암호화/a s/DRM 별로 스트리밍되는 콘텐츠를 캐시 합니다. 코덱-스트리밍 프로토콜-컨테이너 형식-비트 전송률 암호화의 각 조합에 대해 별도의 CDN 캐시가 있습니다.

자주 사용 하는 콘텐츠는 비디오 조각이 캐시 되는 동안 CDN 캐시에서 직접 제공 됩니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 주문형 콘텐츠는 널리 사용 되는 콘텐츠 중 일부를 포함할 수 있기 때문에 다소 까다롭습니다. 수많은 비디오 자산이 널리 사용 되 고 있지 않은 경우 (한 주에 한 번 또는 두 번만 표시), 수천 명의 사용자가 다른 비디오를 모두 감시 하는 경우 CDN이 훨씬 효율적입니다.

또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시 됩니다. 예를 들어 특정 비디오가 처음으로 시청 될 때를 가정 합니다. 뷰어에서 몇 초 동안만 감시 하는 것을 건너뛴 경우, 사용자가 감시 하는 것과 관련 된 비디오 조각만 표시 됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사용자가 하나의 비트 전송률을 감시 하 고 다른 사용자가 다른 비트 전송률을 감시 하는 경우 각 사용자는 CDN에서 별도로 캐시 됩니다. 두 명의 사용자가 동일한 비트 전송률을 시청 하는 경우에도 서로 다른 프로토콜을 통해 스트리밍할 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.

테스트 환경을 제외 하 고 표준 및 프리미엄 스트리밍 끝점 모두에 대해 CDN을 사용 하도록 설정 하는 것이 좋습니다. 각 스트리밍 끝점 유형에는 서로 다른 지원 되는 처리량 제한이 있습니다.
스트리밍 끝점에서 지원 되는 최대 동시 스트림 수에 대해 정확한 계산을 수행 하는 것은 어려울 수 있습니다. 여기에는 다음이 포함됩니다.

- 스트리밍에 사용 되는 최대 비트 전송률
- 플레이어 사전 버퍼 및 전환 동작 플레이어는 원본에서 세그먼트 버스트를 시도 하 고 로드 속도를 사용 하 여 적응 비트 전송률 전환을 계산 합니다. 스트리밍 끝점이 채도에 근접 하는 경우 응답 시간은 다를 수 있으며 플레이어는 더 낮은 품질로 전환 하기 시작 합니다. 스트리밍 끝점 플레이어에서 부하가 줄어들기 때문에 더 높은 품질로 확장 하 여 원치 않는 전환 트리거를 만듭니다.
전반적으로 최대 스트리밍 끝점 처리량을 계산 하 고이를 최대 비트 전송률 (모든 플레이어에서 가장 높은 비트 전송률을 사용 한다고 가정)에 따라 분할 하는 것이 안전 합니다. 예를 들어, 표준 스트리밍 끝점은 600 Mbps로 제한 되 고 3Mbp의 최고 비트 전송률이 있을 수 있습니다. 이 경우 약 200 개의 동시 스트림이 위쪽 비트 전송률에서 지원 됩니다. 오디오 대역폭 요구 사항도 고려해 야 합니다. 오디오 스트림은 128 kps 에서만 스트리밍할 수 있지만 동시 스트림 수에 곱할 경우 총 스트리밍이 빠르게 추가 됩니다.

이 항목에서는 [CDN 통합](#enable-azure-cdn-integration)을 사용 하도록 설정 하는 방법을 설명 합니다. 또한 프리페치 (활성 캐싱) 및 [원본 지원 CDN 프리페치](#origin-assist-cdn-prefetch) 개념을 설명 합니다.

## <a name="considerations"></a>고려 사항

- CDN을 사용 하도록 설정할지 여부에 관계 없이 [스트리밍 끝점](streaming-endpoint-concept.md) `hostname` 및 스트리밍 URL은 동일 하 게 유지 됩니다.
- CDN을 사용 하거나 사용 하지 않고 콘텐츠를 테스트 하는 기능이 필요한 경우 CDN이 사용 하도록 설정 되지 않은 또 다른 스트리밍 끝점을 만듭니다.

## <a name="enable-azure-cdn-integration"></a>Azure CDN 통합 사용

> [!IMPORTANT]
> 평가판 또는 학생용 Azure 계정에 대해 CDN을 사용 하도록 설정할 수 없습니다.
>
> CDN 통합은 연방 정부 및 중국 지역을 제외한 모든 Azure 데이터 센터에서 사용 하도록 설정 됩니다.

CDN을 사용 하 여 스트리밍 끝점을 프로 비전 한 후에는 스트리밍 끝점을 CDN 끝점에 매핑하기 위해 DNS 업데이트를 수행 하기 전에 Media Services에 정의 된 대기 시간이 있습니다.

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. 스트리밍 끝점이 시작 되 면 Azure CDN 통합을 사용 하도록 설정 하 고 변경 내용이 모든 CDN Pop에서 활성화 되는 데 최대 2 시간이 걸릴 수 있습니다. 그러나 스트리밍 끝점을 시작 하 고 스트리밍 끝점에서 중단 없이 스트리밍할 수 있습니다. 통합이 완료 되 면 CDN에서 스트림이 배달 됩니다. 프로 비전 기간 동안에는 스트리밍 끝점이 **시작** 상태가 되 고 성능이 저하 될 수 있습니다.

표준 스트리밍 끝점을 만들 때 기본적으로 표준 Verizon을 사용 하 여 구성 됩니다. REST Api를 사용 하 여 Premium Verizon 또는 표준 Akamai 공급자를 구성할 수 있습니다.

Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자**를 사용하여 구성할 수 있습니다.

> [!NOTE]
> Azure CDN에 대 한 자세한 내용은 [CDN 개요](../../cdn/cdn-overview.md)를 참조 하세요.

## <a name="determine-if-a-dns-change-was-made"></a>DNS 변경을 수행 했는지 확인 합니다.

을 사용 하 여 스트리밍 끝점에서 DNS 변경이 수행 되었는지 (트래픽이 Azure CDN로 전달 되 고 있는지) 확인할 수 있습니다 <https://www.digwebinterface.com> . Azureedge.net 도메인 이름이 결과에 표시 되는 경우에는 이제 트래픽을 CDN 이라고 합니다.

## <a name="origin-assist-cdn-prefetch"></a>원본-CDN 지원-프리페치

CDN 캐싱은 사후 프로세스입니다. CDN에서 다음 개체를 요청 하는 항목을 예측할 수 있는 경우 CDN은 사전에 다음 개체를 요청 하 고 캐시할 수 있습니다. 이 프로세스를 사용 하면 개체의 모든 (또는 대부분)에 대해 캐시 적중을 수행 하 여 성능을 향상 시킬 수 있습니다.

프리페치의 개념은 플레이어 imminently에서 해당 개체를 요청 하는 것을 예상 하 여 플레이어에 게 해당 개체를 배달 하는 시간을 단축 하기 위해 "인터넷에 지"에 개체를 배치 하는 것입니다.

이러한 목표를 달성 하기 위해 스트리밍 끝점 (원본)과 CDN은 몇 가지 방법으로 직접 작업 해야 합니다.

- CDN에서 프리페치 할 다음 개체를 CDN에 알리기 위해 Media Services 원본에는 "인텔리전스" (원본 지원)가 있어야 합니다.
- CDN은 프리페치 및 캐싱 (CDN 프리페치 파트)을 수행 합니다. CDN에는 프리페치 또는 일반 인출 인지 여부, 404 응답 처리, 무한 프리페치 루프를 방지 하는 방법 등을 원본에 알리기 위한 "인텔리전스"도 있어야 합니다.

### <a name="benefits"></a>혜택

*원본 지원 CDN 프리페치* 기능의 이점에는 다음이 포함 됩니다.

- 프리페치는 재생 중에 가장자리에 예상 되는 비디오 세그먼트를 미리 배치 하 여 비디오 재생 품질을 향상 시키고, 뷰어에 대 한 대기 시간을 줄이고, 비디오 세그먼트 다운로드 시간을 개선 합니다. 이로 인해 비디오 시작 시간이 단축 되 고 다시 버퍼링 횟수가 줄어듭니다.
- 이 개념은 일반적인 CDN 원본 시나리오에 적용 되며 미디어로 제한 되지 않습니다.
- Akamai에서이 기능을 [Akamai 클라우드 Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)에 추가 했습니다.

> [!NOTE]
> 이 기능은 Media Services 스트리밍 끝점과 통합 된 Akamai CDN에 아직 적용 되지 않습니다. 그러나 기존 Akamai 계약이 있고 Akamai CDN과 Media Services 원본 간에 사용자 지정 통합이 필요한 Media Services 고객에 게 제공 됩니다.

### <a name="how-it-works"></a>작동 방법

헤더에 대 한 CDN 지원 `Origin-Assist CDN-Prefetch` (라이브 및 비디오 주문형 스트리밍 모두)은 AKAMAI cdn을 사용 하 여 직접 계약을 체결 한 고객에 게 제공 됩니다. 이 기능에는 Akamai CDN과 Media Services 원본 간의 다음 HTTP 헤더 교환이 포함 됩니다.

|HTTP 헤더|값|보낸 사람|받는 사람|목적|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1(기본값) 또는 0 |CDN|원본|CDN에서 프리페치를 사용 하도록 지정 합니다.|
|`CDN-Origin-Assist-Prefetch-Path`| 예제: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|원본|CDN|CDN에 대 한 프리페치 경로를 제공 합니다.|
|`CDN-Origin-Assist-Prefetch-Request`|1(프리페치 요청) 또는 0(일반 요청)|CDN|원본|CDN의 요청이 프리페치 임을 나타내려면입니다.|

헤더 교환의 작동 방식을 확인하려면 다음 단계를 수행합니다.

1. Postman 또는 말아를 사용 하 여 오디오 또는 비디오 세그먼트 또는 조각에 대 한 Media Services 원본에 요청을 발급 합니다. 요청에 헤더를 추가 해야 `CDN-Origin-Assist-Prefetch-Enabled: 1` 합니다.
2. 응답에서 `CDN-Origin-Assist-Prefetch-Path` 상대 경로를 해당 값으로 사용 하는 헤더가 표시 되어야 합니다.

### <a name="supported-streaming-protocols"></a>지원 되는 스트리밍 프로토콜

이 `Origin-Assist CDN-Prefetch` 기능은 라이브 및 주문형 스트리밍에 대해 다음과 같은 스트리밍 프로토콜을 지원 합니다.

* HLS v3
* HLS v4
* HLS CMAF
* 대시 (CSF)
* 대시 (CMAF)
* 부드러운 스트리밍

### <a name="faqs"></a>FAQ

* CDN 프리페치에서 404을 가져오기 위해 프리페치 경로 URL이 잘못 된 경우는 어떻게 되나요?

    CDN은 10 초 또는 기타 구성 된 값에 대해 404 응답만 캐시 합니다.

* 주문형 비디오가 있다고 가정 합니다. CDN 프리페치를 사용 하는 경우 클라이언트가 첫 번째 비디오 세그먼트를 요청 하면 프리페치는 동일한 비트 전송률에서 모든 후속 비디오 세그먼트를 프리페치 하는 루프를 시작 한다는 것을 의미 하나요?

    아니요. CDN-프리페치는 클라이언트에서 시작한 요청/응답 후에만 수행 됩니다. CDN-프리페치는 프리페치 루프를 방지 하기 위해 프리페치에 의해 트리거되지 않습니다.

* 원본 지원 CDN-프리페치 기능을 항상 설정 하 시겠습니까? 설정/해제 하려면 어떻게 해야 하나요?

    이 기능은 기본적으로 해제되어 있습니다. 고객이 Akamai API를 통해 설정 해야 합니다.

* 라이브 스트리밍의 경우 출처는 다음 세그먼트 또는 조각을 아직 사용할 수 없는 경우에 도움이 될 것입니다.

    이 경우 Media Services 원본은 헤더를 제공 하지 않고 `CDN-Origin-Assist-Prefetch-Path` CDN 프리페치는 발생 하지 않습니다.

* 는 `Origin-Assist CDN-Prefetch` 동적 매니페스트 필터를 사용 하 여 어떻게 작동 하나요?

    이 기능은 매니페스트 필터와 독립적으로 작동 합니다. 다음 조각이 필터 창에서 벗어난 경우 원시 클라이언트 매니페스트를 찾고 CDN 프리페치 응답 헤더로 반환 하 여 해당 URL을 찾을 수 있습니다. 따라서 CDN은 대시/HLS/부드러운 매니페스트에서 필터링 된 조각의 URL을 가져옵니다. 그러나 플레이어는 해당 조각이 플레이어에서 보유 하는 대시/HLS/부드러운 매니페스트에 포함 되지 않기 때문에 CDN에 대 한 GET 요청을 수행 하지 않습니다.

* 대시 MPD/HLS 재생 목록/부드러운 매니페스트는 프리페치된 수 있나요?

    아니요, 대시 MPD, HLS 마스터 재생 목록, HLS 변형 재생 목록 또는 부드러운 매니페스트 URL이 프리페치 헤더에 추가 되지 않습니다.

* 프리페치 Url은 상대적 또는 절대값 입니까?

    Akamai CDN은 둘 다 허용 하지만 절대 Url 사용에는 명백한 이점을 제공 하지 않기 때문에 Media Services 원본은 프리페치 경로에 대 한 상대 Url만 제공 합니다.

* 이 기능은 DRM으로 보호 된 콘텐츠를 사용 하나요?

    예,이 기능은 HTTP 수준에서 작동 하므로 모든 세그먼트/조각을 디코드 하거나 구문 분석 하지 않습니다. 콘텐츠가 암호화 되는지 여부를 고려 하지 않습니다.

* 이 기능은 SSAI (서버 쪽 Ad 삽입)에서 작동 하나요?
    
    SSAI는 Media Services 원본에서 원본 콘텐츠의 타임 스탬프를 변경 하지 않으므로 원래/기본 콘텐츠 (광고 삽입 전의 원래 비디오 콘텐츠)가 작동 합니다. 이 기능이 ad 콘텐츠에서 작동 하는지 여부는 ad 원본이 원본 지원을 지원 하는지 여부에 따라 달라 집니다. 예를 들어 ad 콘텐츠가 Azure Media Services (동일 또는 별도의 원본)에도 호스트 되는 경우 ad 콘텐츠가 프리페치된 됩니다.

* 이 기능은 UHD/HEVC 콘텐츠에서 작동 하나요?

    예.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [스트리밍 끝점 (원본)](streaming-endpoint-concept.md) 문서를 검토 해야 합니다.
* [이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.
