---
title: Azure Media Services의 스트리밍 로케이터 | Microsoft Docs
description: 이 문서에서는 스트리밍 로케이터의 개념과 Azure Media Services에서 이러한 로케이터를 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466734"
---
# <a name="streaming-locators"></a>스트리밍 로케이터

출력 자산의 비디오를 재생할 클라이언트에 사용할 수 있도록 하려면 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만든 다음, 스트리밍 URL을 빌드해야 합니다. .NET 샘플은 [스트리밍 로케이터 가져오기](stream-files-tutorial-with-api.md#get-a-streaming-locator)를 참조하세요.

**스트리밍 로케이터**를 만드는 과정을 게시라고 합니다. 기본적으로 **스트리밍 로케이터**는 API 호출을 수행한 직후부터 유효하며, 선택적인 시작 및 종료 시간을 구성하지 않는 한 삭제될 때까지 지속됩니다. 

**스트리밍 로케이터**를 만드는 경우 [자산](https://docs.microsoft.com/rest/api/media/assets) 이름 및 [스트리밍 정책](https://docs.microsoft.com/rest/api/media/streamingpolicies) 이름을 지정해야 합니다. 미리 정의된 스트리밍 정책 중 하나를 사용하거나 사용자 지정 정책을 만들 수 있습니다. 현재 사용 가능한 미리 정의된 스트리밍 정책은 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' 및 'Predefined_MultiDrmStreaming'입니다. 사용자 지정 스트리밍 정책을 사용하는 경우 Media Services 계정에 대해 이러한 정책의 제한된 세트를 설계하고 동일한 옵션 및 프로토콜이 필요할 때마다 스트리밍 로케이터에 해당 정책을 다시 사용하는 것이 좋습니다. 

스트림에 대한 암호화 옵션을 지정하려는 경우 콘텐츠 키가 Media Services의 키 제공 구성 요소를 통해 최종 클라이언트에 제공되는 방법을 구성하는 [콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies)을 만듭니다. 스트리밍 로케이터를 **콘텐츠 키 정책** 및 콘텐츠 키와 연결합니다. Media Services에서 키를 자동 생성하게 할 수 있습니다. 다음.NET 예제는 Media Services v3의 토큰 제한으로 AES 암호화를 구성하는 방법을 보여 줍니다. [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **콘텐츠 키 정책**은 업데이트할 수 있으며, 키 순환을 수행해야 하는 경우 정책을 업데이트하는 것이 좋습니다. 키 배달 캐시를 업데이트하고 업데이트된 정책을 선택하는 데 최대 15분까지 걸릴 수 있습니다. 각 스트리밍 로케이터에 대해 새 콘텐츠 키 정책을 만드는 방법은 권장되지 않습니다. 동일한 옵션이 필요할 때마다 기존 정책의 다시 사용을 시도하는 것이 좋습니다.

> [!IMPORTANT]
> * 날짜/시간 형식의 **스트리밍 로케이터** 속성은 언제나 UTC 형식입니다.
> * Media Services 계정에 대한 제한된 정책 세트를 설계하고 동일한 옵션이 필요할 때마다 스트리밍 로케이터에 해당 세트를 다시 사용하는 것이 좋습니다. 

## <a name="associate-filters-with-streaming-locators"></a>스트리밍 로케이터를 사용 하 여 연결 필터

목록을 지정할 수 있습니다 [자산 또는 계정 필터](filters-concept.md)에 적용 되는 것에 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)합니다. 합니다 [동적 packager](dynamic-packaging-overview.md) 이 목록을 함께 클라이언트 URL에 지정 된 필터를 적용 합니다. 이 조합에서는 오류가 발생 하는 [dyanamic 매니페스트](filters-dynamic-manifest-overview.md), URL에 대 한 필터 + 스트리밍 로케이터에 지정 하는 필터를 기준으로 하는 합니다. 필터를 적용 하 고 싶지만 필터 이름을 URL에 노출 하지 않으려는 경우이 기능을 사용 하는 것이 좋습니다.

## <a name="filter-order-page-streaming-locator-entities"></a>필터, 순서 및 스트리밍 로케이터 엔터티 페이지

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [자습서: .NET](stream-files-tutorial-with-api.md)를 사용하여 비디오 업로드, 인코딩 및 스트림
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
