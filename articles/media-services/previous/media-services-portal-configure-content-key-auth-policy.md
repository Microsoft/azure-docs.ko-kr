---
title: Azure Portal을 사용하여 콘텐츠 키 인증 정책 구성 | Microsoft Docs
description: 콘텐츠 키에 대한 인증 정책을 구성하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: b046ce5a8647abe601a6327667241d98445ce1e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130560"
---
# <a name="configure-a-content-key-authorization-policy"></a>콘텐츠 키 인증 정책 구성
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>개요
 Microsoft Azure Media Services를 사용하면 AES(Advanced Encryption Standard)(128비트 암호화 키 사용) 또는 [PlayReady DRM(디지털 권한 관리)](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HTTP-Live-Streaming(HLS) 스트림을 배달할 수 있습니다. Media Services를 사용하면 Widevine DRM으로 암호화된 DASH 스트림을 전달할 수도 있습니다. PlayReady와 Widevine 모두 일반적인 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다.

또한 Media Services는 키/라이선스 배달 서비스를 제공하여 클라이언트가 암호화된 콘텐츠를 재생할 수 있는 AES 키 또는 PlayReady/Widevine 라이선스를 받을 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 콘텐츠 키 인증 정책을 구성하는 방법을 보여 줍니다. 키는 나중에 동적으로 콘텐츠를 암호화하는데 사용할 수 있습니다. 현재 HLS, MPEG DASH 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 점진적 다운로드는 암호화할 수 없습니다.

플레이어가 동적으로 암호화되도록 설정된 스트림을 요청하는 경우, Media Services는 구성된 키를 사용하고 AES 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

여러 콘텐츠 키를 가지거나 Media Services 키 배달 서비스가 아닌 키/라이선스 배달 서비스 URL을 지정하려면 Media Services 키 배달 서비스 Media Services .NET SDK 또는 REST API를 사용하세요. 자세한 내용은 다음을 참조하세요.

* [Media Services .NET SDK를 사용하여 콘텐츠 키 권한 부여 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md)
* [Media Services REST API를 사용하여 콘텐츠 키 권한 부여 정책 구성](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>다음과 같은 몇 가지 고려 사항이 적용됩니다.
* Azure Media Services 계정이 만들어지면 기본 스트리밍 엔드포인트가 “중지됨” 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 스트리밍 엔드포인트가 “실행 중” 상태에 있어야 합니다. 
* 사용자의 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조하세요.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체(정책 옵션 및 제한 사항)를 15분 동안 캐시합니다. ContentKeyAuthorizationPolicy를 만들고 토큰 제한을 사용하도록 지정하고, 테스트한 다음, 정책을 개방형 제한으로 업데이트할 수 있습니다. 이 프로세스를 수행할 경우 대략 15분 후에 정책이 개방형 버전으로 전환됩니다.
* Media Services 스트리밍 엔드포인트는 실행 전 응답에서 CORS 'Access-Control-Allow-Origin' 헤더 값을 “\*” 와일드카드로 설정합니다. 이 값은 Azure Media Player, Roku 및 JWPlayer 등을 망라한 대부분의 플레이어에서 작동합니다. 그러나 자격 증명 모드가 “include”로 설정된 상태에서 dash.js의 XMLHttpRequest가 “\*” 와일드카드를 Access-Control-Allow-Origin 값으로 허용하지 않으므로 dash.js를 사용하는 일부 플레이어에서는 작동하지 않습니다. 이러한 dashjs 제한을 해결하기 위해, 단일 도메인에서 클라이언트를 호스트하는 경우 Media Services가 실행 전 응답 헤더에서 해당 도메인을 지정할 수 있습니다. 도움이 필요한 경우 Azure Portal을 통해 지원 티켓을 엽니다.

## <a name="configure-the-key-authorization-policy"></a>키 인증 정책 구성
키 인증 정책을 구성하려면 **콘텐츠 보호** 페이지를 선택하세요.

Media Services는 키를 요청하는 사용자를 인증하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책에는 개방, 토큰 또는 IP 인증 제한이 있을 수 있습니다 (IP는 REST 또는 .NET SDK로 구성 가능)

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>토큰 제한
토큰 제한 정책을 선택하려면 **TOKEN** 단추를 선택합니다.

토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [SWT(단순 웹 토큰)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) 형식 및 JWT(JSON 웹 토큰) 형식의 토큰을 지원합니다. 자세한 내용은 [JWT 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조하세요.

Media Services는 STS를 제공하지 않습니다. 토큰을 발급하도록 사용자 지정 STS를 만들 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 콘텐츠 키에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 암호화 키를 클라이언트에게 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

### <a name="playready"></a>PlayReady
PlayReady로 콘텐츠를 보호하려는 경우 권한 부여 정책에서 지정해야 하는 항목 중 하나는 PlayReady 라이선스 템플릿을 정의하는 XML 문자열입니다. 기본적으로 설정되는 정책:

    <PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
          <LicenseTemplates>
            <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
              <ContentKey i:type="ContentEncryptionKeyFromHeader" />
              <LicenseType>Nonpersistent</LicenseType>
              <PlayRight>
                <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
              </PlayRight>
            </PlayReadyLicenseTemplate>
          </LicenseTemplates>
        </PlayReadyLicenseResponseTemplate>

**정책 xml 가져오기** 단추를 선택하고 [Media Services PlayReady 라이선스 템플릿 개요](media-services-playready-license-template-overview.md)에 에 정의된 XML 스키마에 맞는 다른 XML을 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

