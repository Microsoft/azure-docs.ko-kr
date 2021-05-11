---
title: CLI를 사용하여 Azure Media Services로 필터 만들기
description: 이 문서에서는 CLI를 사용하여 Azure Media Services v3로 필터를 만드는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 2e03205ca2bd896aa3a3f1cfca5959144237f88b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111175"
---
# <a name="creating-filters-with-cli"></a>CLI를 사용하여 필터 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

고객에게 콘텐츠를 제공(라이브 이벤트 또는 주문형 비디오를 스트리밍)하는 경우 클라이언트에게는 기본 자산의 매니페스트 파일에 설명된 내용보다 더 많은 유연성이 필요할 수 있습니다. Azure Media Services를 사용하면 콘텐츠에 사용할 계정 필터 및 자산 필터를 정의할 수 있습니다.

이 기능과 이 기능이 사용되는 시나리오에 대한 자세한 설명은 [동적 매니페스트](filters-dynamic-manifest-concept.md) 및 [필터](filters-concept.md)를 참조하세요.

이 항목에서는 주문형 비디오 자산에 대한 필터를 구성하는 방법 및 Media Services v3용 CLI를 사용하여 [계정 필터](/cli/azure/ams/account-filter) 및 [자산 필터](/cli/azure/ams/asset-filter)를 만드는 방법을 보여 줍니다.

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange)를 검토해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](./account-create-how-to.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다.

## <a name="define-a-filter"></a>필터 정의

다음 예제에서는 최종 매니페스트에 추가되는 트랙 선택 조건을 정의합니다. 이 필터는 EC-3인 오디오 트랙 및 비트 전송률이 0-1000000 범위인 비디오 트랙을 모두 포함합니다.

> [!TIP]
> REST에서 **필터** 를 정의할 계획이라면 “속성” 래퍼 JSON 개체를 포함해야 합니다.  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>계정 필터 만들기

다음 [az ams account-filter](/cli/azure/ams/account-filter) 명령은 [앞에서 정의한](#define-a-filter) 필터 트랙 선택을 사용하여 계정 필터를 만듭니다.

이 명령을 사용하여 트랙 선택 항목을 나타내는 JSON을 포함하는 선택적 `--tracks` 매개 변수를 전달할 수 있습니다.  @{file}을 사용하여 파일에서 JSON을 로드합니다. Azure CLI를 로컬로 사용하는 경우 다음과 같이 전체 파일 경로를 지정합니다.

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

[필터에 대한 JSON 예제](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)도 참조하세요.

## <a name="create-asset-filters"></a>자산 필터 만들기

다음 [az ams asset-filter](/cli/azure/ams/asset-filter) 명령은 [앞에서 정의한](#define-a-filter) 필터 트랙 선택을 사용하여 자산 필터를 만듭니다. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

[필터에 대한 JSON 예제](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)도 참조하세요.

## <a name="associate-filters-with-streaming-locator"></a>필터를 스트리밍 로케이터와 연결

이제 스트리밍 로케이터에 적용되는 자산 또는 계정 필터 목록을 지정할 수 있습니다. [동적 패키지 작성 도구(스트리밍 엔드포인트)](encode-dynamic-packaging-concept.md)는 클라이언트가 URL에 지정하는 필터 목록과 함께 이 필터 목록을 적용합니다. 이 조합은 URL + 스트리밍 로케이터에 지정하는 필터를 기반으로 하는 [동적 매니페스트](filters-dynamic-manifest-concept.md)를 생성합니다. 필터를 적용하지만 URL에 필터 이름을 표시하지 않으려는 경우 이 기능을 사용하는 것이 좋습니다.

다음 CLI 코드는 스트리밍 로케이터를 만들고 `filters`를 지정하는 방법을 보여 줍니다. 이 속성은 자산 필터 이름 및/또는 계정 필터 이름의 공백으로 구분된 목록을 사용하는 선택적 속성입니다.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>필터를 사용하여 스트림

필터를 정의하고 나면 클라이언트가 스트리밍 URL에 필터를 사용할 수 있습니다. Apple HLS(HTTP 라이브 스트리밍), MPEG-DASH, 부드러운 스트리밍 등의 적응 비트 전송률 스트리밍 프로토콜에 필터를 적용할 수 있습니다.

다음 표에서는 필터가 있는 URL의 몇 가지 예제를 보여 줍니다.

|프로토콜|예제|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|부드러운 스트리밍|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>다음 단계

[비디오 스트리밍](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>참고 항목

[Azure CLI](/cli/azure/ams)
