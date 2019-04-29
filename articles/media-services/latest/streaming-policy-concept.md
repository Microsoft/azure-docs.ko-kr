---
title: Azure Media Services의 스트리밍 정책 | Microsoft Docs
description: 이 문서에서는 스트리밍 정책이란 무엇이며 Azure Media Services에서 스트리밍 정책을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230899"
---
# <a name="streaming-policies"></a>스트리밍 정책

Azure Media Services v3에서 [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies)을 사용하여 [스트리밍 로케이터](streaming-locators-concept.md)의 스트리밍 프로토콜 및 암호화 옵션을 정의할 수 있습니다. 미리 정의된 스트리밍 정책 중 하나를 사용하거나 사용자 지정 정책을 만들 수 있습니다. 현재 사용 가능한 미리 정의된 스트리밍 정책은 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' 및 'Predefined_MultiDrmStreaming'입니다.

> [!IMPORTANT]
> * 날짜/시간 형식의 **스트리밍 정책** 속성은 언제나 UTC 형식입니다.
> * Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 

## <a name="examples"></a>예

### <a name="not-encrypted"></a>암호화되지 않음

파일을 있는 그대로(암호화하지 않고) 스트리밍하려는 경우 미리 정의된 암호화되지 않은 스트리밍 정책을 'Predefined_ClearStreamingOnly'로 설정합니다(.NET에서는 PredefinedStreamingPolicy.ClearStreamingOnly를 사용할 수 있음).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>암호화 

봉투(Envelope) 및 cenc 암호화를 사용하여 콘텐츠를 암호화해야 하는 경우 정책을 'Predefined_MultiDrmCencStreaming'으로 설정합니다. 이 정책은 로케이터에서 생성되고 설정할 두 가지 콘텐츠 키(봉투 및 CENC)에 대한 것임을 나타냅니다. 따라서 봉투, PlayReady 및 Widevine 암호화가 적용됩니다(키가 구성된 DRM 라이선스에 따라 재생 클라이언트로 배달됨).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

또한 CBCS(FairPlay)를 사용하여 스트림을 암호화하려면 'Predefined_MultiDrmStreaming'을 사용합니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [파일 스트리밍](stream-files-dotnet-quickstart.md)
* [AES-128 동적 암호화 및 키 전달 서비스 사용](protect-with-aes128.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
