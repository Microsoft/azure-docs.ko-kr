---
title: Azure CLI 스크립트 예제 - 작업 만들기 및 제출 | Microsoft Docs
description: 이 항목의 Azure CLI 스크립트는 작업을 HTTPs URL을 사용하여 간단한 인코딩 Transform으로 제출하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0f2e1d901e674c3fecf957bd10314e51195f8b84
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295991"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI 예: 작업 만들기 및 제출

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 옵션 중 하나는 다음 문서와 같이 HTTPS URL을 작업 입력으로 지정하는 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항 

[Media Services 계정 만들기](./create-account-howto.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

`az ams job start`를 실행하면 레이블을 작업의 출력에 설정할 수 있습니다. 이 레이블은 나중에 이 출력 자산의 용도를 식별하는 데 사용할 수 있습니다. 

- 값을 레이블에 할당하는 경우 '--output-assets'를 "assetname=label"로 설정합니다.
- 값을 레이블에 할당하지 않는 경우 '--output-assets'를 "assetname="로 설정합니다.
  "="를 `output-assets`에 추가합니다. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

다음과 비슷한 응답을 받습니다.

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>다음 단계

[az ams job(CLI)](/cli/azure/ams/job?view=azure-cli-latest)
