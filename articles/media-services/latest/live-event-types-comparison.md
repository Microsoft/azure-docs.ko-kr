---
title: Azure Media Services LiveEvent 형식 | Microsoft Docs
description: Azure Media Services에서 라이브 이벤트를 *통과* 또는 *라이브 인코딩으로*설정할 수 있습니다. 이 문서에서는 라이브 이벤트 유형을 비교 하는 상세 테이블을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 41df31cde95ae7ed1d05dac572718622067194c9
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265255"
---
# <a name="live-event-types-comparison"></a>라이브 이벤트 유형 비교

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서  [라이브 이벤트](/rest/api/media/liveevents) 는 *통과* (온-프레미스 라이브 인코더는 다중 비트 전송률 스트림) 또는 *라이브 인코딩* (온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 보냄)으로 설정할 수 있습니다. 

이 문서에서는 라이브 이벤트 유형의 기능을 비교 합니다.

## <a name="types-comparison"></a>형식 비교 

다음 표에서는 라이브 이벤트 유형의 기능을 비교 합니다. 형식은 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)를 사용 하 여 만드는 동안 설정 됩니다.

* **LiveEventEncodingType** -온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 보냅니다. 수집 스트림은 추가 처리 없이 라이브 이벤트를 통과 합니다. 통과 라이브 이벤트 라고도 합니다.
* **LiveEventEncodingType** -온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상인 경우 **Default720p** 사전 설정은 6 개의 해상도/비트 전송률 쌍 집합을 인코딩합니다 (세부 정보는이 문서의 뒷부분 참조).
* **LiveEventEncodingType Premium1080p** -온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 전송률 쌍의 출력 집합을 지정 합니다 (자세한 내용은이 문서의 뒷부분 참조). 

| 기능 | 통과 라이브 이벤트 | Standard 또는 Premium1080p 라이브 이벤트 |
| --- | --- | --- |
| 단일 비트 전송률 입력은 클라우드에서 다중 비트 전송률로 인코딩됩니다. |예 |예 |
| 기여 피드에 대한 최대 비디오 해상도 |4K(60프레임/초에서 4096x2160) |1080p(30프레임/초에서 1920x1088)|
| 기여 피드에 권장되는 최대 계층|최대 12개|하나의 오디오|
| 출력의 최대 계층| 입력과 같음|최대 6 (아래 시스템 사전 설정 참조)|
| 기여 피드의 최대 집계 대역폭|60Mbps|해당 없음|
| 기여에서 단일 계층의 최대 전송률 |20Mbps|20Mbps|
| 여러 언어 오디오 트랙 지원|예|예|
| 지원되는 입력 비디오 코덱 |H.264/AVC 및 H.265/HEVC|H.264/AVC|
| 지원되는 출력 비디오 코덱|입력과 같음|H.264/AVC|
| 지원되는 비디오 비트 수준, 입력 및 출력|HDR 10/HLG를 포함하여 최대 10비트|8비트|
| 지원되는 입력 오디오 코덱|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| 지원되는 출력 오디오 코덱|입력과 같음|AAC-LC|
| 출력 비디오의 최대 비디오 해상도|입력과 같음|표준-720p, Premium1080p-1080p|
| 입력 비디오의 최대 프레임 전송률|60 프레임/초|Standard 또는 Premium1080p-30 프레임/초|
| 입력 프로토콜|RTMP, fragmented-MP4(부드러운 스트리밍)|RTMP, fragmented-MP4(부드러운 스트리밍)|
| 가격|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|[가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/) 를 참조하고 "라이브 비디오" 탭 클릭|
| 최대 실행 시간| 연중무휴 라이브 선형 | 24 시간 x 365 일, 라이브 선형 (미리 보기)|
| 포함된 CEA 608/708 캡션 데이터 전달 가능|예|예|
| 실시간 기록을 켜는 기능|예|예|
| 슬레이트 삽입 지원|예|예|
| API를 통한 광고 신호 지원| 예|예|
| SCTE-35 인밴드 메시지를 통한 광고 신호 지원|예|예|
| 기여 피드의 일시 정지에서 복구하는 기능|예|부분|
| 균일하지 않은 입력 GOP에 대한 지원|예|아니요 - 입력에 고정된 GOP 지속 기간이 있어야 함|
| 변수 프레임 속도 입력에 대한 지원|예|아니요 - 입력은 고정된 프레임 속도여야 함. 예를 들어 움직임이 많은 장면 중에는 사소한 차이가 허용됩니다. 그러나 기여 피드가 프레임 주기를 삭제할 수 없습니다 (예: 15 프레임/초).|
| 입력 피드가 손실될 경우 라이브 이벤트 자동 차단|예|12시간 동안 LiveOutput 실행이 없는 경우|

## <a name="system-presets"></a>시스템 사전 설정

라이브 인코더의 출력에 포함 된 해상도 및 비트 전송률은 [presetName](/rest/api/media/liveevents/create#liveeventencoding)에 의해 결정 됩니다. **표준** 라이브 인코더 (LiveEventEncodingType)를 사용 하는 경우 *Default720p* 사전 설정은 아래에 설명 된 6 개의 해상도/비트 전송률 쌍 집합을 지정 합니다. 그렇지 않고 **Premium1080p** 라이브 인코더 (LiveEventEncodingType)를 사용 하는 경우 *Default1080p* 사전 설정은 해상도/비트 전송률 쌍의 출력 집합을 지정 합니다.

> [!NOTE]
> 표준 라이브 인코딩에 대해 설정 된 경우 Default1080p 사전 설정을 라이브 이벤트에 적용할 수 없습니다. 오류가 발생 합니다. 또한 Premium1080p 라이브 인코더에 Default720p 사전 설정을 적용 하려고 하면 오류가 발생 합니다.

### <a name="output-video-streams-for-default720p"></a>Default720p에 대 한 출력 비디오 스트림

기여 피드가 720p 이상 해상도 인 경우 **Default720p** 사전 설정은 피드를 다음 6 개 계층으로 인코딩합니다. 아래 표에서는 비트 전송률을 kbps로, MaxFPS는 최대 허용 프레임 속도 (프레임/초)를 나타내며 프로필은 사용 된 H. s s e s 프로필을 나타냅니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |높음 |
| 2200 |960 |540 |30 |높음 |
| 1350 |704 |396 |30 |높음 |
| 850 |512 |288 |30 |높음 |
| 550 |384 |216 |30 |높음 |
| 200 |340 |192 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 720p에서 레이어가 하나만 있고, 6레이어 이하인지 확인합니다. 또한 표준 라이브 인코더에 대 한 사전 설정을 요청 하도록 지정 합니다.
> 비트 전송률 및 해상도의 특정 값은 시간이 지남에 따라 조정 될 수 있습니다.

### <a name="output-video-streams-for-default1080p"></a>Default1080p에 대 한 출력 비디오 스트림

기여 피드가 1080p 해상도 인 경우 **Default1080p** 사전 설정은 피드를 다음 6 개 계층으로 인코딩합니다.

| Bitrate | 너비 | 높이 | MaxFPS | 프로필 |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |높음 |
| 3000 |1280 |720 |30 |높음 |
| 1600 |960 |540 |30 |높음 |
| 800 |640 |360 |30 |높음 |
| 400 |480 |270 |30 |높음 |
| 200 |320 |180 |30 |높음 |

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal을 통해 지원 티켓을 여세요. 원하는 해상도 및 비트 전송률 표를 지정해야 합니다. 1080p에 레이어가 하나만 있고 최대 6 개 계층이 있는지 확인 합니다. 또한 Premium1080p 라이브 인코더에 대 한 사전 설정을 요청 하도록 지정 합니다.
> 비트 전송률 및 해상도의 특정 값은 시간이 지남에 따라 조정 될 수 있습니다.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Default720p 및 Default1080p에 대 한 출력 오디오 스트림

*Default720p* 및 *Default1080p* 사전 설정 모두에서 오디오는 스테레오 AAC-LC (128 kbps)로 인코딩됩니다. 샘플링 주기는 기여 피드의 오디오 트랙의 앞에 옵니다.

## <a name="implicit-properties-of-the-live-encoder"></a>라이브 인코더의 암시적 속성

이전 섹션에서는 레이어 수, 해상도, 비트 전송률 등의 미리 설정을 통해 명시적으로 제어할 수 있는 라이브 인코더의 속성에 대해 설명 합니다. 이 섹션에서는 암시적 속성을 명확 하 게 설명 합니다.

### <a name="group-of-pictures-gop-duration"></a>GOP (사진 그룹) 기간

라이브 인코더는 기여 피드의 [gop](https://en.wikipedia.org/wiki/Group_of_pictures) 구조를 따릅니다. 즉, 출력 계층이 동일한 gop 기간을 갖게 됩니다. 따라서 온-프레미스 인코더를 구성 하 여 GOP 기간 (일반적으로 2 초)이 고정 된 기여 피드를 생성 하는 것이 좋습니다. 이렇게 하면 서비스의 나가는 HLS 및 MPEG 파선 스트림에도 고정 GOP 기간이 있습니다. GOP 기간의 작은 변형은 대부분의 장치에서 허용 될 가능성이 높습니다.

### <a name="frame-rate"></a>프레임 율

또한 라이브 인코더는 기여 피드의 개별 비디오 프레임의 지속 시간을 따릅니다. 즉, 출력 계층에 동일한 기간의 프레임이 포함 됩니다. 따라서 고정 프레임 속도 (최대 30 개의 프레임/초)를 포함 하는 기여 피드가 생성 되도록 온-프레미스 인코더를 구성 하는 것이 좋습니다. 이렇게 하면 서비스의 나가는 HLS 및 MPEG 파선 스트림에 고정 프레임 속도 기간이 적용 됩니다. 프레임 속도의 작은 변형은 대부분의 장치에서 허용 될 수 있지만 라이브 인코더가 올바르게 재생 되는 출력을 생성 한다는 보장이 없습니다. 온-프레미스 라이브 인코더는 프레임을 삭제 하지 않아야 합니다 (예: 배터리 부족 상태) 또는 다른 방식으로 프레임 속도로 다양 합니다.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 계층의 해상도

라이브 인코더는 기여 피드의 upconverting를 방지 하도록 구성 됩니다. 따라서 출력 계층의 최대 해상도는 기여 피드의 크기를 초과 하지 않습니다.

예를 들어 Default1080p live encoding에 대해 구성 된 라이브 이벤트에 720p의 기여 피드를 보내는 경우 출력에는 5 개 계층 (3Mbps의 경우 5, 200 kbps까지)이 포함 됩니다. 또는 360p의 기여 피드를 표준 라이브 인코딩으로 구성 된 라이브 이벤트로 보내면 출력에 3 개의 계층 (288p, 216p 및 192p의 해상도)이 포함 됩니다. 중복 제거의 경우 160x90 표준 라이브 인코더에 픽셀의 기여 피드를 보내면 출력에 기여 피드와 동일한 비트 전송률의 160x90 해상도로 한 계층이 포함 됩니다.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>기여 피드 및 출력 계층의 비트 전송률

라이브 인코더는 기여 피드의 비트 전송률에 관계 없이 사전 설정의 비트 전송률 설정을 적용 하도록 구성 됩니다. 결과적으로 출력 계층의 비트 전송률이 기여 피드의 속도를 초과할 수 있습니다. 예를 들어 1 Mbps의 해상도 720p에서 기여 피드를 보내면 출력 계층은 위의 [테이블](live-event-types-comparison.md#output-video-streams-for-default720p) 에 있는 것과 동일 하 게 유지 됩니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 개요](live-streaming-overview.md)
