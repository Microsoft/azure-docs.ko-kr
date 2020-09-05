---
title: Azure Media Services REST를 사용 하 여 인코딩할 경우 비디오 하위 클립
description: 이 항목에서는 REST를 사용 하 여 Azure Media Services 인코딩할 때 비디오를 하위 클립 하는 방법에 대해 설명 합니다.
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
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: d60baf6b8ca3fd2371ab13128524ad407d07942e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257350"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Media Services를 사용 하 여 인코딩할 때 비디오를 하위 클립-REST

[작업](/rest/api/media/jobs)을 사용 하 여 인코딩할 때 비디오를 자르거나 하위 클립 할 수 있습니다. 이 기능은 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용하여 빌드된 모든 [변환](/rest/api/media/transforms)과 함께 작동합니다. 

이 항목의 REST 예제에서는 인코딩 작업을 전송할 때 비디오를 트리밍하는 작업을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [Azure Media Services 계정을 만듭니다](./create-account-howto.md).
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 
- 변환 및 출력 자산을 만듭니다. [URL을 기반으로 원격 파일 인코딩 및 비디오 스트리밍-REST](stream-files-tutorial-with-rest.md) 자습서에서 변환 및 출력 자산을 만드는 방법을 확인할 수 있습니다.
- [인코딩 개념](encoding-concept.md) 항목을 검토 합니다.

## <a name="create-a-subclipping-job"></a>서브 클립 작업 만들기

1. 다운로드 한 postman 컬렉션에서 **변환 및 작업**  ->  **하위 클리핑으로 작업 만들기**를 선택 합니다.
    
    **PUT** 요청은 다음과 같습니다.
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. "TransformName" 환경 변수의 값을 변환 이름으로 업데이트 합니다. 
1. **본문** 탭을 선택 하 고 "myOutputAsset"를 출력 자산 이름으로 업데이트 합니다.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. **보내기**를 누릅니다.

    만들고 제출한 작업 및 작업 상태에 대 한 정보가 포함 된 **응답이** 표시 됩니다. 

## <a name="next-steps"></a>다음 단계

[사용자 지정 변환을 사용 하 여 인코딩하는 방법](custom-preset-rest-howto.md) 
