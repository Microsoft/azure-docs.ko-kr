---
title: Azure Media Services 질문과 대답 | Microsoft Docs
description: FAQ(질문과 대답)
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3164445dc28a2436d8492902d865414da8d75702
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463916"
---
# <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 AMS(Azure Media Services) 사용자 커뮤니티에 자주 올라오는 질문과 대답을 다룹니다.

## <a name="general-ams-faqs"></a>일반 AMS FAQ

Q: Apple iOS 디바이스에 스트리밍하려면 어떻게 하나요?

A: URL의 "/Manifest" 부분에 "(format=m3u8-aapl)" 경로를 추가하여 스트리밍 원본 서버에 Apple iOS 네이티브 디바이스에서 사용할 수 있게 HLS 콘텐츠를 다시 반환하도록 지시합니다(자세한 내용은 [콘텐츠 전달](media-services-deliver-content-overview.md) 참조).

Q: 인덱싱을 크기 조정하려면 어떻게 하나요?

A: 예약 단위는 인코딩 및 인덱싱 작업에서 동일합니다. [Encoding 예약 단위 크기를 조정하는 방법](media-services-scale-media-processing-overview.md)의 지침에 따르세요. **참고** : 인덱서 성능은 예약 단위 유형의 영향을 받지 않습니다.

Q: 비디오를 업로드, 인코딩 및 게시합니다. 스트리밍하려고 할 때 어떤 이유로 비디오가 재생되지 않는 걸까요?

A: 가장 일반적인 이유 중 하나는 재생하려고 하는 스트리밍 엔드포인트가 **실행 중** 상태가 아니기 때문입니다.  

Q: 라이브 스트림에서 합치기를 수행할 수 있나요?

A: 라이브 스트림 합치기는 현재 Azure Media Services에서 제공되지 않으므로, 컴퓨터에 미리 합치기를 수행해야 합니다.

Q: 라이브 스트리밍으로 Azure CDN을 사용할 수 있나요?

A: Media Services는 Azure CDN과의 통합을 지원합니다. 자세한 내용은 [Media Services 계정에서 스트리밍 엔드포인트를 관리하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.  CDN을 사용하여 라이브 스트리밍을 사용할 수 있습니다. Azure Media Services는 부드러운 스트리밍, HLS 및 MPEG-DASH 출력을 제공합니다. 이러한 형식에서는 HTTP를 데이터 전송에 사용하여 HTTP 캐싱의 이점을 얻을 수 있습니다. 라이브 스트리밍에서 실제 비디오/오디오 데이터가 조각으로 분할되고 이 개별 조각은 CDN에 캐시됩니다. 새로 고쳐야 하는 데이터 요구는 매니페스트 데이터입니다. CDN은 매니페스트 데이터를 주기적으로 새로 고칩니다.

Q: Azure Media Services는 저장된 이미지를 지원하나요?

A: JPEG 또는 PNG 이미지를 저장하기 위해 찾으려는 경우 Azure Blob Storage에 유지해야 합니다. 비디오 또는 오디오 자산와 연결된 상태를 유지하려는 경우가 아니면 Media Services 계정에 넣어도 이점은 없습니다. 또는 비디오 인코더에서 오버레이로 이미지를 사용해야 하는 경우입니다. 미디어 인코더 표준은 비디오 위에 이미지 겹치기를 지원하며 지원되는 입력 형식으로 JPEG 및 PNG를 나열합니다. 자세한 내용은 [오버레이 만들기](media-services-advanced-encoding-with-mes.md#overlay)를 참조하세요.

Q: Media Services 계정 간에 자산을 복사하려면 어떻게 해야 하나요?

A: .NET을 사용하여 Media Services 계정 간에 자산을 복사하려면 [Azure Media Services .NET SDK 확장](https://github.com/Azure/azure-sdk-for-media-services-extensions/) 리포지토리에서 제공하는 [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 확장 메서드를 사용합니다. 자세한 내용은 [이](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) 포럼 스레드를 참조하세요.

Q: AMS에서 작업할 때 파일 이름 지정에 지원되는 문자는 무엇인가요?

A: Media Services는 스트리밍 콘텐츠(예: http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.)를 위해 URL을 작성할 때 IAssetFile.Name 속성 값을 사용합니다. 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. **Name** 속성 값에는 !* '();:@&=+$,/?%#[]"와 같은 [퍼센트 인코딩 예약 문자](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 ‘.’ 하나만 사용할 수 있습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.

Q: REST를 사용하여 연결하려면 어떻게 해야 하나요?

A: AMS API에 연결하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

Q: 인코딩 프로세스 중에 비디오를 회전하려면 어떻게 해야 하나요?

A: [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)는 90/180/270도 회전을 지원합니다. 기본 동작은 들어오는 MP4/MOV 파일에서 회전 메타데이터를 검색하여 그에 맞게 보정하는 "Auto"입니다. 다음 **소스** 요소를 [여기](media-services-mes-presets-overview.md)에 정의된 json 사전 설정 중 하나에 포함합니다.

    "Version": 1.0,
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
