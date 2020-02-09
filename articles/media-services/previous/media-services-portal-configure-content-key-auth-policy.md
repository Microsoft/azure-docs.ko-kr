---
title: Azure Portal |를 사용 하 여 콘텐츠 키 권한 부여 정책 구성 Microsoft Docs
description: 이 문서에서는 콘텐츠 키에 대 한 권한 부여 정책을 구성 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 8580bafd4d68ef6567b09fefcaa01c682ae2cafe
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968797"
---
# <a name="configure-a-content-key-authorization-policy"></a>콘텐츠 키 인증 정책 구성
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Áttekintés
 Azure Media Services를 사용 하 여 128 비트 암호화 키 또는 [PLAYREADY DRM (디지털 권한 관리](https://www.microsoft.com/playready/overview/))을 사용 하 여 AES(ADVANCED ENCRYPTION STANDARD) (AES)로 보호 되는 HLS (MPEG 부드러운 스트리밍 및 HTTP 라이브 스트리밍) 스트림을 배달할 수 있습니다. Media Services를 사용 하 여 Widevine DRM으로 암호화 된 대시 스트림도 제공할 수도 있습니다. Mind a PlayReady, mind a Widevine titkosítása a Common Encryption (ISO/IEC 23001-7 CENC) szabvány specifikációi szerint történik.

또한 Media Services는 클라이언트가 AES 키 또는 PlayReady/Widevine 라이선스를 가져와 암호화 된 콘텐츠를 재생할 수 있는 키/라이선스 배달 서비스를 제공 합니다.

이 문서에서는 Azure Portal 사용 하 여 콘텐츠 키 인증 정책을 구성 하는 방법을 보여 줍니다. 나중에이 키를 사용 하 여 콘텐츠를 동적으로 암호화할 수 있습니다. 현재 HLS, MPEG 대시 및 부드러운 스트리밍 형식을 암호화할 수 있습니다. 점진적 다운로드는 암호화할 수 없습니다.

플레이어가 동적으로 암호화 되도록 설정 된 스트림을 요청 하는 경우 Media Services는 구성 된 키를 사용 하 여 AES 또는 DRM 암호화를 사용 하 여 동적으로 콘텐츠를 암호화 합니다. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. 사용자에 게 키를 가져올 수 있는 권한이 있는지 여부를 확인 하기 위해 서비스는 키에 대해 지정한 권한 부여 정책을 평가 합니다.

여러 콘텐츠 키를 포함 하거나 Media Services 키 배달 서비스 이외의 키/라이선스 배달 서비스 URL을 지정 하려는 경우 Media Services .NET SDK 또는 REST Api를 사용 합니다. További információ eléréséhez lásd:

* [Media Services .NET SDK를 사용 하 여 콘텐츠 키 권한 부여 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md)
* [Media Services REST API를 사용 하 여 콘텐츠 키 권한 부여 정책 구성](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>몇 가지 고려 사항이 적용 됩니다.
* A Media Services-fiók létrehozásakor a rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva állapotban. 콘텐츠 스트리밍을 시작 하 고 동적 패키징 및 동적 암호화를 활용 하려면 스트리밍 엔드포인트이 "실행 중" 상태 여야 합니다. 
* 자산은 적응 비트 전송률 Mp4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함 해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조 하세요.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체 (정책 옵션 및 제한)를 15 분 동안 캐시 합니다. ContentKeyAuthorizationPolicy를 만들고 토큰 제한을 사용 하도록 지정 하 고, 테스트 한 다음, 정책을 개방형 제한으로 업데이트할 수 있습니다. 이 프로세스는 정책이 오픈 버전으로 전환 될 때까지 약 15 분이 걸립니다.
* Media Services 스트리밍 엔드포인트은 실행 전 응답의 CORS 액세스 제어 허용-원본 헤더 값을 와일드 카드 "\*"로 설정 합니다. 이 값은 Azure Media Player, Roku 및 JWPlayer 등 대부분의 플레이어에서 잘 작동 합니다. 그러나 자격 증명 모드를 "포함"으로 설정 하 고, 해당 하는 경우에는 "\*" 와일드 카드를 액세스-제어-원본 값으로 허용 하지 않기 때문에 대시가를 사용 하는 일부 플레이어는 작동 하지 않습니다. 이 제한 사항에 대 한 해결 방법으로, 단일 도메인에서 클라이언트를 호스트 하는 경우 실행 전 응답 헤더에서 해당 도메인을 지정할 수 Media Services. 도움이 필요 하면 Azure Portal를 통해 지원 티켓을 여세요.

## <a name="configure-the-key-authorization-policy"></a>키 권한 부여 정책 구성
키 권한 부여 정책을 구성 하려면 **콘텐츠 보호** 페이지를 선택 합니다.

Media Services는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원 합니다. 콘텐츠 키 권한 부여 정책에는 열기, 토큰 또는 IP 권한 부여 제한이 있을 수 있습니다. (IP는 REST 또는 .NET SDK를 사용 하 여 구성할 수 있습니다.)

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청 하는 사람에 게 키를 제공 하는 것을 의미 합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>토큰 제한
토큰 제한 정책을 선택 하려면 **토큰** 단추를 선택 합니다.

토큰 제한 정책에는 STS (보안 토큰 서비스)에서 발급 한 토큰이 수반 되어야 합니다. Media Services는[SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(단순 웹 토큰) 및 JSON WEB TOKEN (JWT) 형식의 토큰을 지원 합니다. 자세한 내용은 [JWT 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)을 참조 하세요.

Media Services STS를 제공 하지 않습니다. 토큰을 발급 하는 사용자 지정 STS를 만들 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효 하 고 토큰의 클레임이 콘텐츠 키에 대해 구성 된 것과 일치 하면 Media Services 키 배달 서비스는 암호화 키를 클라이언트에 반환 합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 대상 (범위 라고도 함)은 토큰의 의도 또는 토큰에서 액세스 권한을 부여 하는 리소스에 대해 설명 합니다. Media Services 키 배달 서비스는 토큰의 이러한 값이 템플릿의 값과 일치 하는지 확인 합니다.

### <a name="playready"></a>PlayReady
PlayReady를 사용 하 여 콘텐츠를 보호 하는 경우 권한 부여 정책에서 지정 해야 하는 항목 중 하나는 PlayReady 라이선스 템플릿을 정의 하는 XML 문자열입니다. 기본적으로 다음 정책이 설정 됩니다.

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

**정책 xml 가져오기** 단추를 선택 하 고 [Media Services PlayReady 라이선스 템플릿 개요](media-services-playready-license-template-overview.md)에 정의 된 xml 스키마를 준수 하는 다른 xml을 제공할 수 있습니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

