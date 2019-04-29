---
title: Azure Portal을 사용하여 콘텐츠 보호 정책 구성 | Microsoft Docs
description: 이 문서에서는 Azure Portal을 사용하여 콘텐츠 보호 정책을 구성하는 방법에 대해 설명합니다. 또한 자산에서 동적 암호화를 사용하도록 설정하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c0571e202448951de0994d34f68e1649eabd5519
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128140"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Azure Portal을 사용하여 콘텐츠 보호 정책 구성
 Azure Media Services를 사용하면 컴퓨터를 떠날 때부터 저장, 처리 및 배달에 이르는 과정 내내 미디어를 보호할 수 있습니다. Media Services를 사용하면 128비트 암호화 키를 사용하여 AES(Advanced Encryption Standard)로 동적 암호화된 콘텐츠를 배달할 수 있습니다. PlayReady 및/또는 Widevine DRM(디지털 권한 관리)과 Apple FairPlay를 사용하여 일반적인 암호화(CENC)와 함께 사용할 수도 있습니다. 

Media Services는 DRM 라이선스 및 AES 암호화되지 않은 키를 인증된 클라이언트에 배달하는 서비스를 제공합니다. Azure Portal을 사용하여 모든 암호화 형식에 대해 키/라이선스 권한 부여 정책을 만들 수 있습니다.

이 문서에서는 포털을 사용하여 콘텐츠 보호 정책을 구성하는 방법에 대해 설명합니다. 또한 자산에 동적 암호화를 적용하는 방법을 보여 줍니다.

## <a name="start-to-configure-content-protection"></a>콘텐츠 보호 구성 시작
포털에서 Media Services 계정을 사용하여 전역 콘텐츠 보호를 구성하려면 다음 단계를 따릅니다.

1. [포털](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.

1. **설정** > **Content Protection**을 선택합니다.

    ![콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>키/라이선스 권한 부여 정책
Media Services는 키 또는 라이선스를 요청하는 사용자를 인증하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책을 구성해야 합니다. 키/라이선스가 배달되려면 먼저 클라이언트에서 정책을 충족해야 합니다. 콘텐츠 키 인증 정책에는 하나 이상의 권한 부여 제한(열기 또는 토큰 제한)이 있을 수 있습니다.

포털을 사용하여 모든 암호화 형식에 대해 키/라이선스 권한 부여 정책을 만들 수 있습니다.

### <a name="open-authorization"></a>Open Authorization
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다. 

### <a name="token-authorization"></a>토큰 권한 부여
토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 SWT(단순 웹 토큰) 형식 및 JWT(JSON Web Token) 형식의 토큰을 지원합니다. Media Services는 STS를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Azure Access Control Service를 사용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다. 토큰이 유효하고 해당 토큰의 클레임이 키(또는 라이선스)에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 요청된 키(또는 라이선스)를 클라이언트에 반환합니다.

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 보안 토큰 서비스입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

![키/라이선스 권한 부여 정책](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady 라이선스 템플릿
PlayReady 라이선스 템플릿을 PlayReady 라이선스에서 사용할 수 있도록 하는 기능을 설정합니다. PlayReady 라이선스 템플릿에 대한 자세한 내용은 [Media Services PlayReady 라이선스 템플릿 개요](media-services-playready-license-template-overview.md)를 참조하세요.

### <a name="nonpersistent"></a>비영구적
라이선스를 비영구적으로 구성하는 경우 플레이어가 라이선스를 사용하는 동안에는 메모리에 보관만 됩니다.  

![비영구적 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>영구적
라이선스를 영구적으로 구성하는 경우 클라이언트의 영구 저장소에 저장됩니다.

![영구적 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine 라이선스 템플릿
Widevine 라이선스 템플릿을 Widevine 라이선스에서 사용할 수 있도록 하는 기능을 설정합니다.

### <a name="basic"></a>Basic
**기본**을 선택하면 템플릿이 모든 기본값으로 만들어집니다.

### <a name="advanced"></a>고급
Widevine 권한 템플릿에 대한 자세한 내용은 [Widevine 라이선스 템플릿 개요](media-services-widevine-license-template-overview.md)를 참조하세요.

![고급 콘텐츠 보호](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 구성
FairPlay 암호화를 사용하도록 설정하려면 **FairPlay 구성**을 선택합니다. 그런 후 **앱 인증서**를 선택하고 **애플리케이션 암호 키**를 입력합니다. FairPlay 구성 및 요구 사항에 대한 자세한 내용은 [Microsoft PlayReady 또는 Apple FairPlay로 HLS 콘텐츠 보호](media-services-protect-hls-with-FairPlay.md)를 참조하세요.

![FairPlay 구성](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>동적 암호화를 자산에 적용
동적 암호화를 활용하려면 소스 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩해야 합니다.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>암호화하려는 자산을 선택
모든 자산을 보려면 **설정** > **자산**을 선택합니다.

![자산 옵션](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>AES 또는 DRM으로 암호화
자산에 대해 **암호화**를 선택하는 경우 **AES** 또는 **DRM**의 두 옵션이 제공됩니다. 

#### <a name="aes"></a>AES
다음과 같은 모든 스트리밍 프로토콜에서 AES 암호화되지 않은 키 암호화를 사용합니다. 부드러운 스트리밍, HLS 및 MPEG-DASH.

![암호화 구성](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. **DRM**을 선택하면 다른 콘텐츠 보호 정책(이 시점에서 구성해야 함) 및 스트리밍 프로토콜 집합이 표시됩니다.

    a. **MPEG-DASH를 사용하는 PlayReady 및 Widevine** - PlayReady 및 Widevine DRM의 MPEG-DASH 스트림을 동적으로 암호화합니다.

    b. **MPEG-DASH를 사용하는 PlayReady 및 Widevine + HLS를 사용하는 FairPlay** - PlayReady 및 Widevine DRM의 MPEG-DASH 스트림을 동적으로 암호화합니다. 이 옵션은 FairPlay의 HLS 스트림도 암호화합니다.

    다. **부드러운 스트리밍, HLS 및 MPEG-DASH만 사용하는 PlayReady** - PlayReady DRM의 부드러운 스트리밍, HLS, MPEG-DASH 스트림을 동적으로 암호화합니다.

    d. **MPEG-DASH만 사용하는 Widevine** - Widevine DRM의 MPEG-DASH를 동적으로 암호화합니다.
    
    e. **HLS만 사용하는 FairPlay** - FairPlay의 HLS 스트림을 동적으로 암호화합니다.

1. FairPlay 암호화를 사용하도록 설정하려면 **콘텐츠 보호 전역 설정** 블레이드에서 **FairPlay 구성**을 선택합니다. 그런 후 **앱 인증서**를 선택하고 **애플리케이션 암호 키**를 입력합니다.

    ![암호화 형식](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. 암호화를 선택한 후 **적용**을 선택합니다.

>[!NOTE] 
>Safari에서 AES 암호화 HLS를 재생하려는 경우 [Encrypted HLS in Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)(Safari의 암호화된 HLS) 블로그 게시물을 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

