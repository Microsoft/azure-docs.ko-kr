---
title: Azure Media Services의 필터를 정의합니다.
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍은 Media Services가 동적 매니페스트를 생성하여 이루어집니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 61b877c322fcd58472990c328beea2e309502bce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734578"
---
# <a name="define-account-filters-and-asset-filters"></a>계정 필터 및 자산 필터 정의  

고객 (라이브 스트리밍 이벤트 또는 주문형 비디오) 콘텐츠를 제공 하는 경우 클라이언트의 기본 자산의 매니페스트 파일에 설명 된 것 보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다. 

필터는 고객이 다음과 같은 작업을 수행할 수 있도록 하는 서버 쪽 규칙입니다. 

- 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생합니다. 예를 들면 다음과 같습니다.
  - 라이브 이벤트의 하위 클립을 표시하는 매니페스트를 줄입니다("하위 클립 필터링").
  - 비디오의 시작 부분을 자릅니다("비디오 트리밍").
- 콘텐츠를 재생하는 데 사용되는 디바이스에서 지원하는 지정된 변환 및/또는 지정된 언어 트랙만 전달합니다(“변환 필터링”). 
- 플레이어의 DVR 창 길이를 제한하기 위해 프레젠테이션 창(DVR)을 조정합니다("프레젠테이션 창 조정").

Media Services는 미리 정의된 필터에 따라 [동적 매니페스트](filters-dynamic-manifest-overview.md)를 제공합니다. 필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH 및 부드러운 스트리밍

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`<br/>HLS v3 사용: `format=m3u8-aapl-v3`합니다.|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>필터 정의

자산 필터에는 두 가지 유형이 있습니다. 

* [계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters)(전역) - Azure Media Services 계정의 모든 자산에 적용 가능하며 계정의 수명 동안 보유합니다.
* [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters)(로컬) -필터를 만들 때 연결된 자산에만 적용 가능하며 자산의 수명 동안 보유합니다. 

[계정 필터](https://docs.microsoft.com/rest/api/media/accountfilters) 및 [자산 필터](https://docs.microsoft.com/rest/api/media/assetfilters) 유형은 필터를 정의/설명하기 위한 정확히 동일한 속성을 가집니다. **자산 필터**를 작성하는 경우 외에는 필터를 연결할 자산 이름을 지정해야 합니다.

시나리오에 따라 더 적합한 필터 유형이 다릅니다(자산 필터 또는 계정 필터). 계정 필터는 일반적으로 디바이스 프로필에 적합(변환 필터링)한 반면, 자산 필터는 특정 자산을 트리밍하는 데 사용할 수 있습니다.

다음 속성을 사용하여 필터를 설명합니다. 

|이름|설명|
|---|---|
|firstQuality|필터의 첫 번째 품질 비트 전송률입니다.|
|presentationTimeRange|프레젠테이션 시간 범위입니다. 이 속성은 매니페스트 시작/종료 지점, 프레젠테이션 창 길이 및 라이브 시작 위치를 필터링하는 데 사용됩니다. <br/>자세한 내용은 [PresentationTimeRange](#presentationtimerange)를 참조하세요.|
|tracks|트랙 선택 조건입니다. 자세한 내용은 [tracks](#tracks)를 참조하세요.|

### <a name="presentationtimerange"></a>presentationTimeRange

이 속성은 **자산 필터**와 함께 사용합니다. **계정 필터**를 사용하여 이 속성을 설정하지 않는 것이 좋습니다.

|이름|설명|
|---|---|
|**endTimestamp**|VoD(주문형 비디오)에 적용됩니다.<br/>라이브 스트리밍 프레젠테이션을 위해 자동으로 무시 되 고 적용 되며 프레젠테이션이 종료 스트림을 VoD 하 게 될 때입니다.<br/>가장 가까운 다음 GOP 시작 위치로 반올림 프레젠테이션의 절대 끝점을 나타내는 long 값입니다. 단위는 endTimestamp 1800000000의 15 일은 3 분에 있도록 날짜 표시줄입니다.<br/>재생 목록 (매니페스트)에 있는 조각 자르려면 startTimestamp 및 endTimestamp를 사용 합니다.<br/>예를 들어 startTimestamp 40000000 및 endTimestamp = 100000000 = 기본 시간 간격을 사용 하 여 조각 4 초 사이 있는 및 VoD 프레젠테이션의 10 초를 포함 하는 재생 목록이 생성 됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**forceEndTimestamp**|라이브 스트리밍과 적용 됩니다.<br/>EndTimestamp 속성을 표시 해야 하는지 여부를 나타냅니다. True 이면 endTimestamp를 지정 해야 합니다 또는 잘못 된 요청 코드를 반환 됩니다.<br/>허용되는 값: true, false|
|**liveBackoffDuration**|라이브 스트리밍과 적용 됩니다.<br/> 이 값 최신 라이브는 위치를 검색할 수 있는 클라이언트를 정의 합니다.<br/>이 속성을 사용 하면 라이브 재생 위치를 지연 하 고 플레이어를 위해 서버 쪽 버퍼를 만들 수 있습니다.<br/>이 속성에 대 한 단위 시간 간격 (아래 참조)입니다.<br/>최대 기간 백오프 라이브은 300 초 (3000000000)입니다.<br/>예를 들어, 2000000000 즉 최신 사용 가능한 콘텐츠는 20 초 지연 실제 라이브에 지에서의 값입니다.|
|**presentationWindowDuration**|라이브 스트리밍과 적용 됩니다.<br/>PresentationWindowDuration을 사용 하 여 재생 목록에 포함할 조각의 슬라이딩 윈도우를 적용 합니다.<br/>이 속성에 대 한 단위 시간 간격 (아래 참조)입니다.<br/>예를 들어presentationWindowDuration=1200000000을 설정하면 2분 슬라이딩 윈도우가 적용됩니다. 라이브 에지 2분 이내의 미디어가 재생 목록에 포함됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 재생 목록에 포함됩니다. 최소 프레젠테이션 기간은 60초입니다.|
|**startTimestamp**|라이브 스트리밍 또는 주문형 (VoD) 비디오에 적용 됩니다.<br/>이 스트림의 절대 시작점을 나타내는 long 값입니다. 값은 가장 가까운 다음 GOP 시작 지점으로 반올림됩니다. 단위 하므로 150000000의 startTimestamp은 15 초는 시간 간격입니다.<br/>재생 목록 (매니페스트)에 있는 조각 자르려면 startTimestamp 및 endTimestampp를 사용 합니다.<br/>예를 들어 startTimestamp 40000000 및 endTimestamp = 100000000 = 기본 시간 간격을 사용 하 여 조각 4 초 사이 있는 및 VoD 프레젠테이션의 10 초를 포함 하는 재생 목록이 생성 됩니다. 경계에 걸쳐 있는 조각인 경우 전체 조각이 매니페스트에 포함됩니다.|
|**timescale**|1 초에서 간격의 수로 지정 된 프레젠테이션 시간 범위를 모든 타임 스탬프 및 기간에 적용 됩니다.<br/>기본값인 늘어날 때마다 긴 100 나노초 되는 1 초에서 백만 10000000-10 씩 증가 합니다.<br/>예를 들어, startTimestamp을 30 초로 설정 하려는 경우 기본 시간 간격을 사용 하는 경우 300000000 값을 사용 하는 합니다.|

### <a name="tracks"></a>tracks

지정할 필터 추적 속성 조건 (FilterTrackPropertyConditions) 목록을 기반를 동적으로 생성된 된 매니페스트에 트랙을 스트림에 (라이브 스트리밍 또는 주문형 비디오)를 포함 합니다. 필터는 논리 **AND** 및 **OR** 연산을 사용하여 결합됩니다.

필터 트랙 속성 조건은 트랙 유형, 값(다음 표에 설명) 및 연산(Equal, NotEqual)을 설명합니다. 

|이름|설명|
|---|---|
|**Bitrate**|필터링을 위해 트랙의 비트 전송률을 사용합니다.<br/><br/>권장 값은 비트 전송률의 범위(초당 비트 수)입니다. 예를 들어 “0-2427000”입니다.<br/><br/>참고: 특정 비트 전송률 값을 예를 들어 250000(초당 비트 수)과 같이 사용할 수 있지만, 정확한 비트 전송률이 자산별로 변동될 수 있으므로 이 방법은 권장되지 않습니다.|
|**FourCC**|필터링을 위해 트랙의 FourCC 값을 사용합니다.<br/><br/>값은 [RFC 6381](https://tools.ietf.org/html/rfc6381)에 지정된 코덱 형식의 첫 번째 요소입니다. 현재 지원되는 코덱은 다음과 같습니다. <br/>비디오: “avc1”, “hev1”, “hvc1”<br/>오디오: “mp4a”, “ec-3”<br/><br/>에 자산에는 추적에 대 한 FourCC 값을 확인 하려면 가져오고 매니페스트 파일을 검사 합니다.|
|**언어**|필터링을 위해 트랙의 언어를 사용합니다.<br/><br/>값은 RFC 5646에 지정된, 포함할 언어의 태그입니다. 예: "en".|
|**Name**|필터링을 위해 트랙의 이름을 사용합니다.|
|**형식**|필터링을 위해 트랙의 유형을 사용합니다.<br/><br/>허용되는 값은 “video”, “audio” 또는 “text”입니다.|

## <a name="associate-filters-with-streaming-locator"></a>스트리밍 로케이터를 사용 하 여 연결 필터

스트리밍 로케이터를 사용 하 여 적용할 자산 또는 계정 필터 목록을 지정할 수 있습니다. 합니다 [동적 packager](dynamic-packaging-overview.md) 이 목록을 함께 클라이언트 URL에 지정 된 필터를 적용 합니다. 이 조합에서는 오류가 발생 하는 [dyanamic 매니페스트](filters-dynamic-manifest-overview.md), URL에 대 한 필터 + 스트리밍 로케이터에 지정 하는 필터를 기준으로 하는 합니다. 필터를 적용 하 고 싶지만 필터 이름을 URL에 노출 하지 않으려는 경우이 기능을 사용 하는 것이 좋습니다.

## <a name="definition-example"></a>정의 예제

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 프로그래밍 방식으로 필터를 만드는 방법을 설명합니다.  

- [REST API를 사용하여 필터 만들기](filters-dynamic-manifest-rest-howto.md)
- [.NET을 사용하여 필터 만들기](filters-dynamic-manifest-dotnet-howto.md)
- [CLI를 사용하여 필터 만들기](filters-dynamic-manifest-cli-howto.md)

