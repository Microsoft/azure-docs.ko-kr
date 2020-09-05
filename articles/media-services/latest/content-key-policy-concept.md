---
title: Media Services의 콘텐츠 키 정책 - Azure | Microsoft Docs
description: 이 문서에서는 콘텐츠 키 정책의 개념과 Azure Media Services에서 이러한 정책을 사용하는 방법을 설명합니다.
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
ms.custom: seodec18
ms.openlocfilehash: b6d907e987cb57836997cab43e9bfa9e919bf67d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268291"
---
# <a name="content-key-policies"></a>콘텐츠 키 정책

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services를 사용하면 Advanced Encryption Standard(AES-128) 또는 Microsoft PlayReady, Google Widevine 및 Apple FairPlay 등 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나로 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 AES 키 및DRM(PlayReady, Widevine 및 FairPlay) 라이선스를 배달하는 서비스를 제공합니다. 

스트림에서 암호화 옵션을 지정 하려면 [스트리밍 정책을](streaming-policy-concept.md) 만들고 [스트리밍 로케이터](streaming-locators-concept.md)와 연결 해야 합니다. 콘텐츠 키 [정책을](/rest/api/media/contentkeypolicies) 만들어 [자산](assets-concept.md)에 대 한 보안 액세스를 제공 하는 콘텐츠 키를 최종 클라이언트에 배달 하는 방법을 구성 합니다. 지정 된 구성을 사용 하는 키를 클라이언트에 배달 하기 위해 충족 해야 하는 콘텐츠 키 정책에 대 한 요구 사항 (제한 사항)을 설정 해야 합니다. 콘텐츠 키 정책은 투명한 스트리밍 또는 다운로드에 필요하지 않습니다. 

일반적으로 콘텐츠 키 정책을 [스트리밍 로케이터](streaming-locators-concept.md)와 연결 합니다. 또는 고급 시나리오에 대 한 사용자 지정 스트리밍 정책을 만들 때 [스트리밍 정책](streaming-policy-concept.md) 내에서 콘텐츠 키 정책을 지정할 수 있습니다. 

## <a name="best-practices-and-considerations"></a>모범 사례 및 고려 사항

> [!IMPORTANT]
> 다음 권장 사항을 검토 하세요.

* 미디어 서비스 계정에 대해 제한 된 정책 집합을 디자인 하 고 동일한 옵션이 필요할 때마다 스트리밍 로케이터를 다시 사용 해야 합니다. 자세한 내용은 [할당량 및 제한](limits-quotas-constraints.md)을 참조 하세요.
* 콘텐츠 키 정책을 업데이트할 수 있습니다. 키 배달 캐시를 업데이트 하 고 업데이트 된 정책을 선택 하는 데 최대 15 분이 걸릴 수 있습니다. 

   정책을 업데이트 하 여 캐시 된 콘텐츠를 사용 하는 고객에 게 재생 문제를 일으킬 수 있는 기존 CDN 캐시를 덮어씁니다.  
* 각 자산에 대 한 새 콘텐츠 키 정책을 만들지 않는 것이 좋습니다. 동일한 정책 옵션이 필요한 자산 간에 동일한 콘텐츠 키 정책을 공유 하는 주요 이점은 다음과 같습니다.
   
   * 적은 수의 정책을 관리 하는 것이 더 쉽습니다.
   * 콘텐츠 키 정책에 대 한 업데이트를 수행 해야 하는 경우 변경 내용은 거의 모든 새 라이선스 요청에 적용 됩니다.
* 새 정책을 만들어야 하는 경우 자산에 대 한 새 스트리밍 로케이터를 만들어야 합니다.
* 콘텐츠 키를 자동으로 생성 하도록 Media Services 하는 것이 좋습니다. 

   일반적으로 수명이 긴 키를 사용 하 고 [Get](/rest/api/media/contentkeypolicies/get)을 사용 하 여 콘텐츠 키 정책이 있는지 확인 합니다. 키를 가져오려면 별도의 동작 메서드를 호출하여 비밀 또는 자격 증명을 가져와야 합니다. 아래 예제를 참조하세요.

## <a name="example"></a>예

키를 가져오려면 `GetPolicyPropertiesWithSecretsAsync` [기존 정책에서 서명 키 가져오기](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) 예제에 표시 된 대로를 사용 합니다.

## <a name="filtering-ordering-paging"></a>필터링, 정렬, 페이징

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="additional-notes"></a>추가 참고 사항

* 형식에 해당 하는 콘텐츠 키 정책의 속성 `Datetime` 은 항상 UTC 형식입니다.
* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [AES-128 동적 암호화 및 키 전달 서비스 사용](protect-with-aes128.md)
* [DRM 동적 암호화 및 라이선스 배달 서비스 사용](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
