---
title: Azure Media Services v3를 사용할 수 있는 클라우드 및 지역
description: 이 문서에서는 Azure Media Services v3를 사용할 수 있는 Azure 클라우드 및 지역에 대해 설명합니다.
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
ms.custom: references_regions
ms.openlocfilehash: e9da87f3b5f7cb513b4b5d51c93a59d1703d401d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258659"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Azure Media Services v3가 있는 클라우드 및 지역

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3는 전 세계 Azure, Azure Government, Azure Germany, Azure China 21Vianet에서 Azure Resource Manager 매니페스트를 통해 사용할 수 있습니다. 그러나 모든 Media Services 기능을 모든 Azure 클라우드에서 사용할 수 있는 것은 아닙니다. 이 문서에서는 기본 Media Services v3 구성 요소의 가용성을 설명합니다.

## <a name="feature-availability-in-azure-clouds"></a>Azure 클라우드의 기능 가용성

| 기능|전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| [StandardEncoderPreset](encoding-concept.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| [LiveEvents](live-streaming-overview.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |

## <a name="feature-availability-in-preview"></a>Preview의 기능 가용성

[LiveTranscription](live-transcription.md) 은 다음 지역에서 제공 됩니다.

- 동남 아시아
- 서유럽
- 북유럽
- 미국 동부
- 미국 중부
- 미국 중남부
- 미국 서부 2
- 브라질 남부

## <a name="regionsgeographieslocations"></a>Azure 지역/지리적 위치/위치

[Azure Media Services 서비스가 배포 된 지역](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Azure 지역 코드 이름

**location** 매개 변수를 제공해야 하는 경우 지역 코드 이름을 **location** 값으로 제공해야 합니다. 계정이 속해 있고 호출을 라우팅해야 하는 Azure 지역의 코드 이름을 가져오려면 [Azure CLI](/cli/azure/?view=azure-cli-latest)에서 다음 줄을 실행합니다.

```azurecli-interactive
az account list-locations
```

일단 위에 표시된 줄을 실행하면 모든 Azure 지역 목록이 표시됩니다. 찾으려는 *displayName*이 있는 Azure 지역으로 이동한 후 **location** 매개 변수에 대해 해당 *name* 값을 사용합니다.

예를 들어 미국 서부 2의 Azure 지역에서(아래에 표시됨)는 **location** 매개 변수에 "westus2"를 사용합니다.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>엔드포인트  

다른 국가별 Azure 클라우드에서 Media Services 계정에 연결할 때 다음 엔드포인트를 알아야 합니다.

### <a name="global-azure"></a>글로벌 Azure

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure 리소스 관리자 |  `https://management.azure.com/` |
| 인증 | `https://login.microsoftonline.com/` |
| 토큰 대상 그룹 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure 리소스 관리자 |  `https://management.usgovcloudapi.net/` |
| 인증 | `https://login.microsoftonline.us/` |
| 토큰 대상 그룹 | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure 리소스 관리자 | `https://management.cloudapi.de/` |
| 인증 | `https://login.microsoftonline.de/` |
| 토큰 대상 그룹 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure 중국 21Vianet

| 서비스 | 엔드포인트 |
| ------- | -------- |
| Azure 리소스 관리자 | `https://management.chinacloudapi.cn/` |
| 인증 | `https://login.chinacloudapi.cn/` |
| 토큰 대상 그룹 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>참고 항목

* [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
