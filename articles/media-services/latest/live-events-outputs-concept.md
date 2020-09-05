---
title: Azure Media Services v3의 라이브 이벤트 및 라이브 출력 개념
titleSuffix: Azure Media Services
description: 이 항목에서는 Azure Media Services v 3에서 라이브 이벤트 및 라이브 출력에 대 한 개요를 제공 합니다.
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
ms.openlocfilehash: da91e37c47db6d944aa6974123dd80ec982453e4
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265187"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Media Services에서 라이브 이벤트 및 라이브 출력

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용 하면 Azure 클라우드의 고객에 게 라이브 이벤트를 제공할 수 있습니다. Media Services v3에서 라이브 스트리밍 이벤트를 설정 하려면이 문서에서 설명 하는 개념을 이해 해야 합니다.

> [!TIP]
> Media Services v2 Api에서 마이그레이션하는 고객의 경우 **라이브 이벤트** 엔터티는 v 2의 **채널** 을 바꾸고 **라이브 출력** 은 **프로그램**을 대체 합니다.

## <a name="live-events"></a>라이브 이벤트

[라이브 이벤트](/rest/api/media/liveevents)는 라이브 비디오 피드 수집 및 처리를 담당합니다. 라이브 이벤트를 만들 때 원격 인코더에서 라이브 신호를 보내는 데 사용할 수 있는 기본 및 보조 입력 끝점이 생성 됩니다. 원격 라이브 인코더는 [RTMP](https://www.adobe.com/devnet/rtmp.html) 또는 [부드러운 스트리밍](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) (조각화 된 MP4) 입력 프로토콜을 사용 하 여 해당 입력 끝점에 기여 피드를 보냅니다. RTMP 수집 프로토콜의 경우 콘텐츠를 일반 ()으로 전송 `rtmp://` 하거나 통신 ()에서 안전 하 게 암호화할 수 있습니다 `rtmps://` . 부드러운 스트리밍 수집 프로토콜의 경우 지원되는 URL 체계는 `http://` 또는 `https://`입니다.  

## <a name="live-event-types"></a>라이브 이벤트 유형

[라이브 이벤트](/rest/api/media/liveevents) 는 *통과* (온-프레미스 라이브 인코더는 다중 비트 전송률 스트림) 또는 *라이브 인코딩* (온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 보냄)으로 설정할 수 있습니다. 형식은 [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype)를 사용 하 여 만드는 동안 설정 됩니다.

* **LiveEventEncodingType**: 온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 보냅니다. 수집 스트림은 추가 처리 없이 라이브 이벤트를 통과 합니다. 통과 모드 라고도 합니다.
* **LiveEventEncodingType**: 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. 기여 피드가 720p 이상 해상도 인 경우 **Default720p** 사전 설정은 6 가지 해상도/비트 전송률 쌍 집합을 인코딩합니다.
* **LiveEventEncodingType Premium1080p**: 온-프레미스 라이브 인코더는 단일 비트 전송률 스트림을 라이브 이벤트로 보내고 Media Services 여러 비트 전송률 스트림을 만듭니다. Default1080p 사전 설정은 해상도/비트 전송률 쌍의 출력 집합을 지정 합니다.

### <a name="pass-through"></a>통과

![Media Services 예제 다이어그램을 사용한 통과 라이브 이벤트](./media/live-streaming/pass-through.svg)

통과 **라이브 이벤트**를 사용하는 경우 온-프레미스 라이브 인코더에 의존하여 다중 비트 전송률이 있는 비디오 스트림을 생성하고 이 스트림을 기여 피드로 하여 LiveEvent에 전송(RTMP 또는 조각난 MP4 프로토콜 사용)할 수 있습니다. 그러면 라이브 이벤트는 들어오는 비디오 스트림을 추가 처리 없이 제공합니다. 이러한 통과 라이브 이벤트는 장기 실행 라이브 이벤트 또는 24x365 선형 라이브 스트리밍을 위해 최적화 되었습니다. 이 형식의 라이브 이벤트를 만들 경우 None(LiveEventEncodingType.None)을 지정합니다.

H.264/AVC 또는 H.265/HEVC 비디오 코덱 및 AAC(AAC-LC, HE-AACv1 또는 HE-AACv2) 오디오 코덱을 사용하여 최대 4K의 해상도 및 초당 60프레임의 프레임 속도로 기여 피드를 전송할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison.md)를 참조 하세요.

> [!NOTE]
> 통과 방법을 사용 하는 것은 오랜 시간 동안 여러 이벤트를 수행 하 고 온-프레미스 인코더에 이미 투자 한 경우 라이브 스트리밍을 수행 하는 가장 경제적인 방법입니다. [가격](https://azure.microsoft.com/pricing/details/media-services/) 정보를 참조 하세요.
>

[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)의 .NET 코드 예제를 참조하세요.

### <a name="live-encoding"></a>라이브 인코딩  

![Media Services 예제 다이어그램을 사용한 라이브 인코딩](./media/live-streaming/live-encoding.svg)

Media Services에서 라이브 인코딩을 사용 하는 경우 라이브 이벤트에 기여 피드로 단일 비트 전송률 비디오를 보내도록 온-프레미스 라이브 인코더를 구성 합니다 (RTMP 또는 조각화 된 Mp4 프로토콜 사용). 그런 다음 들어오는 단일 비트 전송률 스트림을 [다중 비트 전송률 비디오 스트림으로](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)인코드 하도록 라이브 이벤트를 설정 하 고, MPEG-2, HLS 및 부드러운 스트리밍 같은 프로토콜을 통해 장치를 재생 하기 위해 출력을 배달할 수 있도록 합니다.

Live encoding을 사용 하는 경우, AAC/AVC 비디오 코덱 및 (AAC, He-aacv1 또는 He-aacv2) 오디오 코덱을 사용 하 여 프레임 속도로 30 프레임/초 프레임 속도로 최대 1080p 해상도로만 기여 피드를 보낼 수 있습니다. 통과 라이브 이벤트는 60 프레임/초에 최대 4K의 해상도를 지원할 수 있습니다. 자세한 내용은 [라이브 이벤트 유형 비교](live-event-types-comparison.md)를 참조 하세요.

라이브 인코더의 출력에 포함 된 해상도 및 비트 전송률이 미리 설정에 따라 결정 됩니다. **표준** 라이브 인코더 (LiveEventEncodingType)를 사용 하는 경우 *Default720p* 기본 설정은 720p 3.5 Mbps에서 192p, 200 kbps로 이동 하 여 6 해상도/비트 전송률 쌍의 집합을 지정 합니다. 그렇지 않고 **Premium1080p** live Encoder (LiveEventEncodingType)를 사용 하는 경우 *Default1080p* 사전 설정은 3.5 Mbps에서 Mbps로 이동 하 여 200 kbps에서 180p로 이동 하는 6 개의 해상도/비트 전송률 쌍 집합을 지정 합니다. 자세한 내용은 [시스템 미리 설정](live-event-types-comparison.md#system-presets)을 참조하세요.

> [!NOTE]
> 라이브 인코딩 사전 설정을 사용자 지정 해야 하는 경우 Azure Portal를 통해 지원 티켓을 엽니다. 원하는 해상도 및 비트 전송률 표를 지정 합니다. 720p (표준 라이브 인코더에 대 한 사전 설정을 요청 하는 경우) 또는 1080p (Premium1080p live 인코더에 대 한 사전 설정을 요청 하는 경우)에 레이어가 하나만 있는지 확인 하 고 최대 6 개의 계층을 만듭니다.

## <a name="creating-live-events"></a>라이브 이벤트 만들기

### <a name="options"></a>옵션

라이브 이벤트를 만들 때 다음 옵션을 지정할 수 있습니다.

* 라이브 이벤트의 스트리밍 프로토콜(현재 RTMP 및 부드러운 스트리밍 프로토콜이 지원됨).<br/>라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대 한 별도의 라이브 이벤트를 만듭니다.  
* 이벤트를 만들 때 자동 시작을 지정할 수 있습니다. <br/>Autostart가 true로 설정되어 있는 경우 Live Event가 생성 후 시작됩니다. 라이브 이벤트가 실행되는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 Stop을 호출해야 합니다. 또는 스트리밍을 시작할 준비가 되 면 이벤트를 시작할 수 있습니다.

    자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing.md)를 참조하세요.

* 수집 및 미리 보기에서 IP 제한입니다. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 단일 IP 주소(예: '10.0.0.1'), IP 주소 및 CIDR 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1/22') 또는 IP 주소와 점으로 구분된 십진수 서브넷 마스크를 사용하는 IP 범위(예: '10.0.0.1(255.255.252.0)')로 지정할 수 있습니다.<br/>지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 0.0.0.0/0으로 설정합니다.<br/>IP 주소는 다음 형식 중 하나 여야 합니다. 4 개의 숫자 또는 CIDR 주소 범위의 IpV4 주소입니다.

    자신의 방화벽에서 특정 Ip를 사용 하도록 설정 하거나 라이브 이벤트의 입력을 Azure IP 주소로 제한 하려는 경우 [Azure 데이터 센터 IP 주소 범위](https://www.microsoft.com/download/details.aspx?id=41653)에서 JSON 파일을 다운로드 합니다. 이 파일에 대 한 자세한 내용을 보려면 페이지에서 **세부 정보** 섹션을 선택 하십시오.
    
* 이벤트를 만들 때 실시간 화상 기능을 사용 하도록 선택할 수 있습니다. <br/> 기본적으로 실시간 기록을 사용할 수 없습니다. 라이브 이벤트 또는 연결 된 라이브 출력이 실행 되는 동안에는이 속성을 변경할 수 없습니다. 

### <a name="naming-rules"></a>이름 지정 규칙

* 최대 라이브 이벤트 이름은 32 자입니다.
* 이름은이 [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 따라야 합니다. `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`

또한 [스트리밍 끝점 명명 규칙](streaming-endpoint-concept.md#naming-convention)을 참조 하세요.

> [!TIP]
> 라이브 이벤트 이름을 고유 하 게 유지 하기 위해 GUID를 생성 한 다음 모든 하이픈 및 중괄호 (있는 경우)를 제거할 수 있습니다. 문자열은 모든 라이브 이벤트에서 고유 하며 길이가 32이 되도록 보장 됩니다.

## <a name="live-event-ingest-urls"></a>라이브 이벤트 수집 URL

라이브 이벤트가 만들어지면 라이브 온-프레미스 인코더에 제공할 수집 Url을 가져올 수 있습니다. 라이브 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다. 자세한 내용은 [권장 온-프레미스 라이브 인코더](recommended-on-premises-live-encoders.md)를 참조하세요.

비베니티 URL 또는 베니티 URL을 사용할 수 있습니다.

> [!NOTE] 
> 수집 URL을 예측하려면 "베니티" 모드를 설정합니다.

* 비베니티 URL

    베 니 티 URL은 Media Services v3의 기본 모드입니다. 잠재적으로 라이브 이벤트를 빠르게 가져오지만 수집 URL은 라이브 이벤트를 시작할 때 알려집니다. URL은 라이브 이벤트를 중지/시작하는 경우 변경됩니다. <br/>베 니 티은 최종 사용자가 앱에서 라이브 이벤트를 최대한 활용 하 고 동적 수집 URL이 문제가 되지 않는 앱을 사용 하 여 스트리밍하는 시나리오에서 유용 합니다.

    라이브 이벤트를 만들기 전에 클라이언트 앱이 수집 URL을 미리 생성할 필요가 없는 경우 라이브 이벤트에 대 한 액세스 토큰을 자동으로 Media Services 수 있습니다.

* 베니티 URL

    베 니 티 모드는 하드웨어 브로드캐스트 인코더를 사용 하 고 라이브 이벤트를 시작할 때 인코더를 다시 구성 하지 않으려는 large media 방송사에서 선호 됩니다. 이러한 방송사는 시간이 지남에 따라 변경 되지 않는 예측 수집 URL을 원합니다.
    
    > [!NOTE]
    > Azure Portal 베 니 티 URL의 이름은 "*영구 입력 url*"입니다.

    API에서이 모드를 지정 하려면를 `vanityUrl` `true` 만들 때로 설정 합니다 (기본값은 `false` ). 또한 만들 때 사용자 고유의 액세스 토큰 ()을 전달 해야 `LiveEventInput.accessToken` 합니다. 토큰 값을 지정 하 여 URL에서 임의의 토큰을 방지 합니다. 액세스 토큰은 하이픈을 포함 하거나 포함 하지 않는 유효한 GUID 문자열 이어야 합니다. 모드가 설정 된 후에는 업데이트할 수 없습니다.

    액세스 토큰은 데이터 센터에서 고유 해야 합니다. 앱이 베 니 티 URL을 사용 해야 하는 경우 기존 GUID를 다시 사용 하는 대신 항상 액세스 토큰에 대 한 새 GUID 인스턴스를 만드는 것이 좋습니다.

    다음 Api를 사용 하 여 베 니 티 URL을 사용 하도록 설정 하 고 액세스 토큰을 유효한 GUID (예: `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` )로 설정 합니다.  

    |Language|베 니 티 URL 사용|액세스 토큰 설정|
    |---|---|---|
    |REST (영문)|[vanityUrl](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--베 니 티](/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--액세스 토큰](/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[라이브. VanityUrl](/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>라이브 수집 URL 명명 규칙

* 아래 *임의* 문자열은 128비트 16진수 숫자입니다(0-9 a-f의 32문자로 구성됨).
* *사용자의 액세스 토큰*: 베 니 티 모드를 사용할 때 설정 하는 유효한 GUID 문자열입니다. 예: `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`
* *스트림 이름*: 특정 연결의 스트림 이름을 나타냅니다. 스트림 이름 값은 일반적으로 사용 하는 라이브 인코더에 의해 추가 됩니다. 연결을 설명 하는 이름을 사용 하도록 라이브 인코더를 구성할 수 있습니다 (예: "video1_audio1", "video2_audio1", "stream").

#### <a name="non-vanity-url"></a>비베니티 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>베니티 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>부드러운 스트리밍

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>라이브 이벤트 미리 보기 URL

라이브 이벤트가 기여 피드를 수신 하기 시작 하면 미리 보기 끝점을 사용 하 여 추가 게시 전에 라이브 스트림을 수신 중인지 미리 보고 유효성을 검사할 수 있습니다. 미리 보기 스트림이 양호 하다 고 확인 한 후 라이브 이벤트를 사용 하 여 하나 이상의 (미리 만든) 스트리밍 끝점을 통해 라이브 스트림을 배달할 수 있도록 설정할 수 있습니다. 이를 수행 하려면 라이브 이벤트에 새 [라이브 출력](/rest/api/media/liveoutputs) 을 만듭니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

## <a name="live-event-long-running-operations"></a>라이브 이벤트 장기 실행 작업

자세한 내용은 [장기 실행 작업](media-services-apis-overview.md#long-running-operations)을 참조 하세요.

## <a name="live-outputs"></a>라이브 출력

라이브 이벤트로 들어오는 스트림이 있으면 [자산](/rest/api/media/assets), [라이브 출력](/rest/api/media/liveoutputs) 및 [스트리밍 로케이터](/rest/api/media/streaminglocators)를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다.  

라이브 출력에 대 한 자세한 내용은 [클라우드 DVR 사용](live-event-cloud-dvr.md)을 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

질문과 [대답](frequently-asked-questions.md#live-streaming) 문서를 참조 하세요.

## <a name="ask-questions-and-get-updates"></a>질문 하 고 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)
