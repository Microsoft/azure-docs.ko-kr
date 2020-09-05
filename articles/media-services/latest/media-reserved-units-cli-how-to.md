---
title: CLI를 사용하여 미디어 예약 단위 크기 조정 - Azure | Microsoft Docs
description: 이 토픽에서는 CLI를 사용하여 Azure Media Services에서 미디어 처리 크기를 조정하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 8e681b1f82086480541d08ae9f8ef9e5cf07b4df
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268471"
---
# <a name="scaling-media-processing"></a>미디어 처리 크기 조정

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용하면 MRU(미디어 예약 단위)를 관리하여 계정에서 미디어 처리의 크기를 조정할 수 있습니다. Mru는 미디어 처리 작업이 처리 되는 속도를 결정 합니다. **S1**, **S2**또는 **S3**예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. 

예약 단위 유형을 지정하는 것 외에도 계정에 예약 단위를 프로비전하도록 지정할 수 있습니다. 프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되면 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

## <a name="choosing-between-different-reserved-unit-types"></a>여러 예약 단위 유형 중에서 선택

다음 표를 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다. 테스트를 직접 수행하기 위해 [다운로드할 수 있는 비디오](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)에서 몇 가지 벤치마크 사례도 제공됩니다.

|RU 유형|시나리오|[7분 1080p 비디오](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)에 대한 결과 예|
|---|---|---|
| **S1**|단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오.|"H264 단일 비트 전송률 SD 16x9"를 사용 하 여 단일 비트 전송률 SD 확인 MP4 파일로 인코딩하는 데 약 7 분이 걸립니다.|
| **S2**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨|"H264 단일 비트 전송률 720p" 사전 설정을 사용 하는 인코딩은 약 6 분 정도 걸립니다.<br/><br/>"H264 다중 비트 전송률 720p" 사전 설정을 사용 하는 인코딩은 약 12 분이 소요 됩니다.|
| **S3**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>Full HD 및 4K 해상도 비디오. 시간이 중요하며 소요 시간이 짧은 인코딩|"H264 단일 비트 전송률 1080p" 사전 설정을 사용 하는 인코딩은 3 분 정도 걸립니다.<br/><br/>"H264 다중 비트 전송률 1080p" 미리 설정을 사용하여 인코딩할 때 약 8분이 걸립니다.|

## <a name="considerations"></a>고려 사항

* Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 S3 단위 유형이 권장됩니다.
* 공유 풀을 사용 중이어서 예약 단위가 없는 경우에는 인코딩 작업의 성능이 S1 RU와 동일해집니다. 그러나 작업이 대기된 상태로 유지될 수 있는 시간의 상한은 없으며 언제든지 작업은 최대 하나만 실행됩니다.

문서의 나머지 부분에서는 [Media Services V3 CLI](https://aka.ms/ams-v3-cli-ref) 를 사용 하 여 mru 크기를 조정 하는 방법을 보여 줍니다.

> [!NOTE]
> Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

## <a name="prerequisites"></a>사전 요구 사항 

[Media Services 계정 만들기](./create-account-howto.md)

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>CLI를 사용하여 미디어 예약 단위 크기 조정

`mru` 명령을 실행합니다.

다음 [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) 명령은 **count** 및 **type** 매개 변수를 사용하여 "amsaccount" 계정에서 미디어 예약 단위를 설정합니다.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>결제

계정에 미디어 예약 단위를 프로 비전 한 시간 (분)을 기준으로 요금이 청구 됩니다. 이는 계정에서 실행 중인 작업이 있는지 여부와 관계 없이 발생 합니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.   

## <a name="next-step"></a>다음 단계

[비디오 분석](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>참고 항목

* [할당량 및 제한](limits-quotas-constraints.md)
* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
