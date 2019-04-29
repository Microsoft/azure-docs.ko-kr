---
title: Microsoft Azure Media Services 시나리오 및 데이터 센터에서 기능의 사용 가능성 | Microsoft Docs
description: 이 항목은 Microsoft Azure Media Services 시나리오 및 데이터 센터에서 기능 및 서비스의 사용 가용성 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3576a50cd1510a4da562981ff9ae98e12a357fc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638232"
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>시나리오 및 데이터 센터에서 Media Services 기능의 사용 가용성

Microsoft AMS(Azure Media Services)는 다양한 클라이언트(예: TV, PC 및 모바일 디바이스)로의 주문형 및 라이브 스트리밍 배달을 위해 비디오 또는 오디오 콘텐츠를 안전하게 업로드, 저장, 인코딩 및 패키지할 수 있습니다.

AMS는 전 세계 여러 데이터 센터에서 작동합니다. 이러한 데이터 센터는 지리적 영역으로 그룹화되므로 애플리케이션을 빌드할 위치를 유연하게 선택할 수 있습니다. [지역 및 위치 목록](https://azure.microsoft.com/regions/)을 검토할 수 있습니다. 

이 토픽에서는 [라이브](#live_scenarios) 또는 주문형 콘텐츠를 제공하는 일반적인 시나리오를 보여줍니다. 이 항목에서는 데이터 센터에서 미디어 기능 및 서비스의 사용 가용성에 대한 세부 정보도 제공합니다.

## <a name="overview"></a>개요

### <a name="prerequisites"></a>필수 조건

Azure Media Services 사용을 시작하려면 다음이 있어야 합니다.

* Azure 계정. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com)을 참조하십시오.
* Azure Media Services 계정. 자세한 내용은 [계정 만들기](media-services-portal-create-account.md)를 참조하세요.
* 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

    AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 스트리밍 엔드포인트가 **실행** 상태에 있어야 합니다.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>AMS OData 모델에 대해 개발할 때 일반적으로 사용되는 개체

다음 이미지에서는 Media Services OData 모델에 대해 개발할 때 가장 일반적으로 사용되는 개체 중 일부를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

전체 모델은 [여기](https://media.windows.net/API/$metadata?api-version=2.15)서 볼 수 있습니다 .  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>저장소에서 콘텐츠 보호 및 암호화하지 않고 스트리밍 미디어 배달(암호화되지 않음)

![VoD 워크플로](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. 자산에 고품질 미디어 파일을 업로드합니다.

    업로드하는 동안 및 저장소에 있는 동안 콘텐츠를 보호하기 위해 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다.
2. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.

    그대로 있는 콘텐츠를 보호하기 위해 출력 자산에 저장소 암호화 옵션을 적용하는 것이 좋습니다.
3. 자산 배달 정책(동적 패키징에서 사용)을 구성합니다.

    자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 **합니다** .
4. 주문형 로케이터를 만들어 자산을 게시합니다.
5. 게시된 콘텐츠를 스트리밍합니다.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](#availability) 섹션을 참조하세요.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>저장소에서 콘텐츠를 보호하고 암호화된 스트리밍 미디어를 동적으로 배달합니다.

![PlayReady로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. 자산에 고품질 미디어 파일을 업로드합니다. 저장소 암호화 옵션을 자산에 적용합니다.
2. 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다. 저장소 암호화 옵션을 출력 자산에 적용합니다.
3. 재생하는 동안 동적으로 암호화하려는 경우 자산에 대한 암호화 콘텐츠 키를 만듭니다.
4. 콘텐츠 키 인증 정책을 구성합니다.
5. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
6. 주문형 로케이터를 만들어 자산을 게시합니다.
7. 게시된 콘텐츠를 스트리밍합니다.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](#availability) 섹션을 참조하세요.

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>미디어 분석을 사용하여 비디오에 대한 실질적인 통찰력 얻기

미디어 분석은 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음입니다. 자세한 내용은 [Azure Media Services 분석 개요](media-services-analytics-overview.md)를 참조하세요.

1. 자산에 고품질 미디어 파일을 업로드합니다.
2. [미디어 분석 개요](media-services-analytics-overview.md) 섹션에 설명된 미디어 분석 서비스 중 하나를 사용하여 비디오를 처리합니다.
3. 미디어 분석 미디어 프로세서는 MP4 파일 또는 JSON 파일을 생성합니다. 미디어 프로세서가 MP4 파일을 생성한 경우 파일을 점진적으로 다운로드할 수 있습니다. 미디어 프로세서가 JSON 파일을 생성한 경우 Azure Blob Storage에서 해당 파일을 다운로드할 수 있습니다.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](#availability) 섹션을 참조하세요.

## <a name="deliver-progressive-download"></a>점진적 다운로드 제공

1. 자산에 고품질 미디어 파일을 업로드합니다.
2. 하나의 MP4 파일로 인코딩합니다.
3. 주문형 또는 SAS 로케이터를 만들어 자산을 게시합니다.

    SAS 로케이터를 사용하는 경우 콘텐츠는 Azure Blob Storage에서 다운로드됩니다. 이 경우 스트리밍 엔드포인트가 시작된 상태에 있을 필요가 없습니다.
4. 콘텐츠를 점진적으로 다운로드합니다.

## <a id="live_scenarios"></a>라이브 스트리밍 이벤트 배달 

1. 다양한 라이브 스트리밍 프로토콜(예: RTMP 또는 부드러운 스트리밍)을 사용하여 라이브 콘텐츠를 수집합니다.
2. (선택 사항)스트림을 적응 비트 전송률 스트림으로 인코딩합니다.
3. 라이브 스트림을 미리 봅니다.
4. 일반적인 스트리밍 프로토콜(예: MPEG DASH, 부드러운, HLS)을 통해 고객에게 직접 또는 추가 배포를 위해 CDN(Content Delivery Network)에 콘텐츠를 배달합니다.

    또는

    나중에 스트리밍하기 위해 수집된 콘텐츠를 기록 및 저장합니다(주문형 비디오).

라이브 스트리밍을 수행할 때 다음 경로 중 하나를 선택할 수 있습니다.

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>온-프레미스 인코더(통과)에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업

다음 다이어그램에서는 **통과** 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로](./media/scenarios-and-availability/media-services-live-streaming-current.png)

자세한 내용은 [온-프레미스 인코더의 다중 비트 전송률 라이브 스트림을 수신하는 채널 사용](media-services-live-streaming-with-onprem-encoders.md)을 참조하세요.

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Azure Media Services를 사용하여 라이브 인코딩을 수행할 수 있는 채널 작업

다음 다이어그램에서는 채널이 Media Services를 통해 라이브 인코딩을 수행할 수 있는 라이브 스트리밍 워크플로에 관련된 AMS 플랫폼의 주요 부분을 보여 줍니다.

![라이브 워크플로](./media/scenarios-and-availability/media-services-live-streaming-new.png)

자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

데이터 센터에서 사용 가용성에 대한 정보는 [사용 가능성](#availability) 섹션을 참조하세요.

## <a name="consuming-content"></a>콘텐츠 사용

Azure Media Services는 iOS 장치, Android 장치, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 애플리케이션을 만드는 데 필요한 도구를 제공합니다. 다음 항목에서 제공하는 SDK 및 플레이어 프레임워크 링크를 사용하여 Media Services의 스트리밍 미디어를 사용할 수 있는 클라이언트 애플리케이션을 개발할 수 있습니다. 자세한 내용은 [비디오 플레이어 애플리케이션 개발](media-services-develop-video-players.md)을 참조하세요.

## <a name="enabling-azure-cdn"></a>Azure CDN 사용하기

Media Services는 Azure CDN과의 통합을 지원합니다. Azure CDN을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Media Services 계정에서 스트리밍 엔드포인트를 관리하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

## <a id="scaling"></a>Media Services 계정 크기 조정하기

AMS 고객은 해당 AMS 계정에서 스트리밍 엔드포인트, 미디어 처리 및 저장소의 크기를 조정할 수 있습니다.

* Media Services 고객은 **표준** 스트리밍 엔드포인트나 **프리미엄** 스트리밍 엔드포인트를 선택할 수 있습니다. **표준** 스트리밍 엔드포인트는 대부분의 스트리밍 워크로드에 적합합니다. **프리미엄** 스트리밍 단위와 동일한 기능을 포함하고 아웃바운드 대역폭을 자동으로 확장합니다. 

    **프리미엄** 스트리밍 엔드포인트는 고급 워크로드에 적합하며, 확장성 있는 전용 대역폭 용량을 제공합니다. **프리미엄** 스트리밍 엔드포인트가 있는 고객은 기본적으로 하나의 SU(스트리밍 단위)를 가져옵니다. SU를 추가하여 스트리밍 엔드포인트의 크기를 조정할 수 있습니다. 각 SU는 애플리케이션에 추가 대역폭 수용작업량을 제공합니다. **프리미엄** 스트리밍 엔드포인트의 크기를 조정하는 방법에 대한 자세한 내용은 [스트리밍 엔드포인트 크기 조정](media-services-portal-scale-streaming-endpoints.md) 항목을 참조하세요.

* Media Services 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 형식과 연결됩니다. 예약 단위 유형 **S1**, **S2** 또는 **S3** 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다.

    예약 단위 유형을 지정하는 것 외에도 계정에 **RU(예약 단위)** 를 프로비전하도록 지정할 수 있습니다. 프로비전되는 RU의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다.

    >[!NOTE]
    >RU는 Azure Media Indexer를 사용하는 인덱싱 작업을 비롯하여 모든 미디어 처리 병렬화에 대해 작동합니다. 그러나 인코딩과 달리 인덱싱 작업은 예약 단위가 더 빠르게 실행되어도 더 빨리 처리되지 않습니다.

    자세한 내용은 [미디어 처리 크기 조정](media-services-portal-scale-media-processing.md)을 참조하세요.
* 또한 저장소 계정을 추가하여 Media Services 계정을 확장할 수 있습니다. 각 저장소 계정은 500TB로 제한됩니다. 여러 저장소 계정을 단일 Media Services 계정에 연결하여 기본 제한 이상으로 저장소를 확장할 수 있습니다. 자세한 내용은 [저장소 계정 관리](meda-services-managing-multiple-storage-accounts.md)를 참조하세요.

## <a id="availability"></a>데이터 센터에서 Media Services 기능의 사용 가용성

이 섹션에서는 데이터 센터에서 Media Services 기능 의 사용 가용성에 대한 세부 정보를 제공합니다.

### <a name="ams-accounts"></a>AMS 계정

#### <a name="availability"></a>가용성

데이터 센터에서 Media Services를 사용할 수 있는지 확인하려면 https://azure.microsoft.com/status/로 이동하여 MEDIA 테이블로 스크롤합니다.

### <a name="streaming-endpoints"></a>스트리밍 엔드포인트 

Media Services 고객은 **표준** 스트리밍 엔드포인트나 **프리미엄** 스트리밍 엔드포인트를 선택할 수 있습니다. 자세한 내용은 [크기 조정](#scaling) 섹션을 참조하세요.

#### <a name="availability"></a>가용성

|이름|상태|데이터 센터
|---|---|---|
|Standard|GA|모두|
|Premium|GA|모두|

### <a name="live-encoding"></a>라이브 인코딩

#### <a name="availability"></a>가용성

다음을 제외한 모든 데이터 센터에서 사용할 수 있습니다. 독일, 브라질 남부, 인도 서부, 인도 남부 및 인도 중부. 

### <a name="encoding-media-processors"></a>미디어 프로세서 인코딩

AMS에서는 두 가지 주문형 인코더인 **Media Encoder Standard** 및 **Media Encoder Premium 워크플로**를 제공합니다. 자세한 내용은 [Azure 주문형 미디어 인코더의 개요 및 비교](media-services-encode-asset.md)를 참조하세요. 

#### <a name="availability"></a>가용성

|미디어 프로세서 이름|상태|데이터 센터
|---|---|---|
|미디어 인코더 표준|GA|모두|
|미디어 인코더 Premium 워크플로|GA|중국을 제외한 모든 지역|

### <a name="analytics-media-processors"></a>분석 미디어 프로세서

미디어 분석은 조직과 기업이 비디오 파일에서 실질적인 통찰력을 끌어내기 쉽도록 만드는 언어 및 시각 구성 요소 모음입니다. 자세한 내용은 [Azure Media Services 분석 개요](media-services-analytics-overview.md)를 참조하세요.

#### <a name="availability"></a>가용성

|미디어 프로세서 이름|상태|데이터 센터
|---|---|---|
|Azure 미디어 얼굴 탐지기|미리 보기|모두|
|Azure 미디어 Hyperlapse|미리 보기|모두|
|Azure Media Indexer|GA|모두|
|Azure 미디어 동작 탐지기|미리 보기|모두|
|Azure 미디어 OCR|미리 보기|모두|
|Azure Media Redactor|미리 보기|모두|
|Azure Media Stabilizer|미리 보기|모두|
|Azure 미디어 비디오 미리 보기|미리 보기|모두|
|Azure Media Indexer 2|미리 보기|중국 및 연방 정부 지역을 제외한 모든 지역|

### <a name="protection"></a>보호

Microsoft Azure Media Services를 사용하면 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. 자세한 내용은 [AMS 콘텐츠 보호](media-services-content-protection-overview.md)를 참조하세요.

#### <a name="availability"></a>가용성

|암호화|상태|데이터 센터|
|---|---|---| 
|Storage|GA|모두|
|AES-128 키|GA|모두|
|Fairplay|GA|모두|
|PlayReady|GA|모두|
|Widevine|GA|독일, 연방 정부 및 중국을 제외한 모든 지역

### <a name="reserved-units-rus"></a>RU(예약 단위)

프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다. 

자세한 내용은 [크기 조정](#scaling) 섹션을 참조하세요.

#### <a name="availability"></a>가용성

모든 데이터 센터에서 사용할 수 있습니다.

### <a name="reserved-unit-ru-type"></a>RU(예약 단위) 형식

Media Services 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 형식과 연결됩니다. 예약 단위 유형 S1, S2 또는 S3 중에서 선택할 수 있습니다.

자세한 내용은 [크기 조정](#scaling) 섹션을 참조하세요.

#### <a name="availability"></a>가용성

|RU 형식 이름|상태|데이터 센터
|---|---|---|
|S1|GA|모두|
|S2|GA|브라질 남부 및 인도 서부를 제외한 모든 지역|
|S3|GA|인도 서부를 제외한 모든 지역|

## <a name="next-steps"></a>다음 단계

Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

