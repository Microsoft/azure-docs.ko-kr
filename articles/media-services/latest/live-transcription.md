---
title: 라이브 전사
titleSuffix: Azure Media Services
description: Azure Media Services 라이브 기록에 대해 알아봅니다.
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
ms.openlocfilehash: 698aa849a82e7c9f9a630ca7040f9d449b3765b3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264966"
---
# <a name="live-transcription-preview"></a>라이브 기록 (미리 보기)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 미디어 서비스는 다양 한 프로토콜에서 비디오, 오디오 및 텍스트를 제공 합니다. MPEG-2 또는 HLS/CMAF를 사용 하 여 라이브 스트림을 게시 하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML에 transcribed 텍스트를 제공 합니다. 배달은 MPEG-4 30 (ISO/IEC 14496-30) 조각으로 패키지 됩니다. HLS/TS를 통해 배달을 사용 하는 경우 텍스트는 청크 분할 VTT로 배달 됩니다.

라이브 기록을 켜면 추가 요금이 적용 됩니다. [Media Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/)의 라이브 비디오 섹션에서 가격 정보를 검토 하세요.

이 문서에서는 Azure Media Services 사용 하 여 라이브 이벤트를 스트리밍할 때 실시간 기록을 사용 하도록 설정 하는 방법을 설명 합니다. 계속 하기 전에 Media Services v3 REST Api를 사용 하는 방법을 잘 알고 있어야 합니다. 자세한 내용은 [이 자습서](stream-files-tutorial-with-rest.md) 를 참조 하세요. 또한 [라이브 스트리밍](live-streaming-overview.md) 개념에 대해 잘 알고 있어야 합니다. Media Services 자습서를 [사용 하 여 스트림 라이브](stream-live-tutorial-with-api.md) 를 완료 하는 것이 좋습니다.

## <a name="live-transcription-preview-regions-and-languages"></a>라이브 기록 미리 보기 지역 및 언어

다음 지역에서 라이브 기록을 사용할 수 있습니다.

- 동남 아시아
- 서유럽
- 북유럽
- 미국 동부
- 미국 중부
- 미국 중남부
- 미국 서부 2
- 브라질 남부

Transcribed 수 있는 사용할 수 있는 언어 목록으로, API의 언어 코드를 사용 합니다.

| 언어 | 언어 코드 |
| -------- | ------------- |
| 카탈로니아어  | ca-ES |
| 덴마크어(덴마크) | da-DK |
| 독일어(독일) | de-DE |
| 영어(오스트레일리아) | en-AU |
| 영어(캐나다) | en-CA |
| 영어(영국) | en-GB |
| 영어(인도) | en-IN |
| 영어(뉴질랜드) | en-NZ |
| 영어(미국) | ko-KR |
| 스페인어(스페인) | es-ES |
| 스페인어(멕시코) | es-MX |
| 핀란드어(핀란드) | fi-FI |
| 프랑스어(캐나다) | fr-CA |
| 프랑스어(프랑스) | fr-FR |
| 이탈리아어(이탈리아) | it-IT |
| 네덜란드어(네덜란드) | nl-NL |
| 포르투갈어(브라질) | pt-BR |
| 포르투갈어(포르투갈) | pt-PT |
| 스웨덴어(스웨덴) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>라이브 기록을 사용 하 여 라이브 이벤트 만들기

기록을 켠 상태에서 라이브 이벤트를 만들려면 2019-05-01-preview API 버전을 사용 하 여 PUT 작업을 보냅니다. 예를 들면 다음과 같습니다.

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

작업에는 다음 본문이 있습니다. 여기서 통과 라이브 이벤트는 수집 프로토콜로 RTMP을 사용 하 여 생성 됩니다. 이러한 속성을 추가 합니다.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>라이브 이벤트가 시작 된 후 기록을 시작 하거나 중지 합니다.

라이브 이벤트는 실행 중 상태에 있는 동안 라이브 기록을 시작 하 고 중지할 수 있습니다. 라이브 이벤트를 시작 및 중지 하는 방법에 대 한 자세한 내용은 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md#long-running-operations)에서 장기 실행 작업 섹션을 참조 하세요.

실시간 이벤트를 켜거나 기록 언어를 업데이트 하려면 라이브 이벤트를 패치 하 여 "a" 속성을 포함 합니다. 실시간 이벤트를 해제 하려면 라이브 이벤트 개체에서 "a" 속성을 제거 합니다.  

> [!NOTE]
> 라이브 이벤트에서 기록을 **두 번** 이상 설정 하거나 해제 하는 것은 지원 되는 시나리오가 아닙니다.

라이브를 설정 하는 샘플 호출입니다.

패치나 ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>기록 배달 및 재생

서비스에서 동적 패키징을 사용 하 여 다양 한 프로토콜에서 비디오, 오디오 및 텍스트를 배달 하는 방법에 대 한 [동적 패키징 개요](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) 문서를 검토 합니다. MPEG-2 또는 HLS/CMAF를 사용 하 여 라이브 스트림을 게시 하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML에 transcribed 텍스트를 제공 합니다. 이 배달은 MPEG-4 30 (ISO/IEC 14496-30) 조각으로 패키지 됩니다. HLS/TS를 통해 배달을 사용 하는 경우 텍스트는 청크 분할 된 VTT로 배달 됩니다. [Azure Media Player](use-azure-media-player.md) 와 같은 웹 플레이어를 사용 하 여 스트림을 재생할 수 있습니다.  

> [!NOTE]
> Azure Media Player 사용 하는 경우 버전 2.3.3 이상을 사용 합니다.

## <a name="known-issues"></a>알려진 문제

미리 보기의 경우 라이브 기록의 알려진 문제는 다음과 같습니다.

- 앱은 [Media Services V3 OpenAPI 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)에 설명 된 미리 보기 api를 사용 해야 합니다.
- DRM (디지털 권한 관리) 보호는 텍스트 트랙에는 적용 되지 않으며 AES 봉투 (envelope) 암호화만 가능 합니다.

## <a name="next-steps"></a>다음 단계

* [Media Services 개요](media-services-overview.md)
