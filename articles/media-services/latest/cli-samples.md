---
title: Azure CLI 예제 – Azure Media Services | Microsoft Docs
description: Azure Media Services 서비스용 Azure CLI 예제를 제공합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733184"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure Media Services에 대한 Azure CLI 예제

다음 표에는 Azure Media Services용 Azure CLI 예제의 링크가 포함되어 있습니다.

## <a name="examples"></a>예

|  |  |
|---|---|
|**규모**||
| [미디어 예약 단위 규모](media-reserved-units-cli-how-to.md)|Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. <br/>이 스크립트는 CLI를 사용하여 MRU(미디어 예약 단위) 비율 크기를 조정하는 방법을 보여줍니다.|
|**계정**||
| [Media Services 계정 만들기](create-account-cli-how-to.md) | 스크립트는 Azure Media Services 계정을 만듭니다. |
| [계정 자격 증명 다시 설정](./scripts/cli-reset-account-credentials.md)|계정 자격 증명을 다시 설정하고 app.config 설정을 되돌립니다.|
|**Assets**||
| [자산 만들기](./scripts/cli-create-asset.md)|콘텐츠를 업로드 할 Media Services Asset을 만듭니다.|
| [파일 업로드](./scripts/cli-upload-file-asset.md)|저장소 컨테이너에 로컬 파일을 업로드합니다.|
| **Transforms** 및 **Jobs**||
| [변환 만들기](./scripts/cli-create-transform.md)|변환을 만드는 방법을 보여줍니다. Transform은 비디오 또는 오디오 파일(흔히 "레시피"라고도 함)을 처리하는 간단한 작업 워크플로를 설명합니다.<br/> 원하는 이름으로 변환 하는 경우 항상 확인 해야 하 고 "recipe" 이미 존재 합니다. 그렇지 않으면 다시 사용합니다. |
| [사용자 지정 변환을 사용 하 여 인코딩](custom-preset-cli-howto.md) | 특정 시나리오 또는 장치 요구 사항을 대상으로 사전 설정 사용자 지정을 빌드하는 방법을 보여 줍니다.|
| [작업 만들기](./scripts/cli-create-jobs.md)|HTTPs URL을 사용하여 작업을 간단한 인코딩 Transform에 제출합니다.|
| [EventGrid 만들기](./scripts/cli-create-event-grid.md)|Job State Changes에 대한 계정 수준 Event Grid 구독을 만듭니다.|
| **제공**||
| [자산 게시](./scripts/cli-publish-asset.md)| 스트리밍 로케이터를 만들고 스트리밍 URL을 다시 가져옵니다. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| 주문형 비디오 자산에 대한 필터 구성하고 CLI를 사용하여 [계정 필터](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 및 [자산 필터](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)를 만드는 방법을 보여줍니다. 

## <a name="see-also"></a>참고 항목

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [빠른 시작: 비디오 파일 스트리밍 - CLI](stream-files-cli-quickstart.md)
