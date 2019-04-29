---
title: Azure Media Services v3를 사용할 수 있는 클라우드 및 지역 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3를 사용할 수 있는 Azure 클라우드 및 지역에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 4f8851248c395a1f03c46490c8eb5e71221dd133
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733303"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Azure Media Services v3가 있는 클라우드 및 지역

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

## <a name="regionsgeographieslocations"></a>Azure 지역/지리적 위치/위치

* [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure 지역 제품](https://azure.microsoft.com/global-infrastructure/services/)
* [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 위치](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Azure 지역 코드 이름 

**location** 매개 변수를 제공해야 하는 경우 지역 코드 이름을 **location** 값으로 제공해야 합니다. 계정이 속해 있고 호출을 라우팅해야 하는 Azure 지역의 코드 이름을 가져오려면 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에서 다음 줄을 실행합니다.

```bash
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

|엔드포인트 ||
| --- | --- | 
| Azure 리소스 관리자 |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| 토큰 대상 그룹 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|엔드포인트||
| --- | --- | 
| Azure 리소스 관리자 |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| 토큰 대상 그룹 | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| 엔드포인트 ||
| --- | --- |  
| Azure 리소스 관리자 | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| 토큰 대상 그룹 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|엔드포인트||
| --- | --- | 
| Azure 리소스 관리자 | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| 토큰 대상 그룹 |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>다음 단계

[Media Services v3 개요](media-services-overview.md)
