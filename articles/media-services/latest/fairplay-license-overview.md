---
title: Media Services 및 Apple FairPlay 라이선스 지원 - Azure | Microsoft Docs
description: 이 항목에서는 Apple FairPlay 라이선스를 간략히 요구 사항 및 구성 합니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733575"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay 라이선스 요구 사항 및 구성 

Azure Media Services를 사용하면 **Apple FairPlay**(AES-128 CBC)로 HLS 콘텐츠를 암호화할 수 있습니다. 또한 Media Services는 FairPlay 라이선스를 배달하는 서비스를 제공합니다. 플레이어가 FairPlay로 보호된 콘텐츠를 재생하려고 하면 라이선스 배달 서비스로 요청이 전송되어 라이선스를 얻습니다. 라이선스 서비스에서 요청을 승인하면 클라이언트로 전송하여 지정된 콘텐츠의 암호를 해독하고 재생하는 데 사용되는 라이선스가 발급됩니다.

또한 Media Services는 FairPlay 라이선스를 구성할 수 있는 API를 제공합니다. 이 항목에서는 FairPlay 라이선스 요구 사항을 설명하고 Media Services API를 사용하여 **FairPlay** 라이선스를 구성하는 방법을 보여줍니다. 

## <a name="requirements"></a>요구 사항

Media Services를 사용하여 **Apple FairPlay**로 암호화된 HLS 콘텐츠를 암호화하고 FairPlay 라이선스를 배달할 때 다음이 필요합니다.

* [Apple Development Program](https://developer.apple.com/)에 등록합니다.
* Apple에서는 [배포 패키지](https://developer.apple.com/contact/fps/)를 얻으려면 콘텐츠 소유자를 요구합니다. 이미 Media Services로 KSM(키 보안 모듈)을 구현했고 최종 FPS 패키지를 요청하고 있음을 명시합니다. 최종 FPS 패키지에는 인증을 생성하고 ASK(애플리케이션 비밀 키)를 얻기 위한 지침이 있습니다. ASK를 사용하여 FairPlay를 구성합니다.
* Media Services 키/라이선스 전송 쪽에서 다음 항목을 설정해야 합니다.

    * **AC(앱 인증서)**: 개인 키가 포함된 .pfx 파일입니다. 이 파일을 만들고 암호로 암호화합니다. .pfx 파일은 Base64 형식이어야 합니다.

        다음 단계에서는 FairPlay에 대한 .pfx 인증서 파일을 생성하는 방법을 설명합니다.

        1. https://slproweb.com/products/Win32OpenSSL.html에서 OpenSSL을 설치합니다.

            FairPlay 인증서 및 Apple에서 전달하는 다른 파일이 있는 폴더로 이동합니다.
        2. 명령줄에서 다음 명령을 실행합니다. 이렇게 하면 .cer 파일이 .pem 파일로 변환됩니다.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. 명령줄에서 다음 명령을 실행합니다. 이렇게 하면 .pem 파일이 개인 키가 있는 .pfx 파일로 변환됩니다. OpenSSL에서 .pfx 파일에 대한 암호를 묻습니다.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **앱 인증서 암호**: .pfx 파일을 만들기 위한 암호입니다.
    * **ASK**: 이 키는 Apple 개발자 포털을 사용하여 인증을 생성할 때 받습니다. 각 개발 팀에 고유한 ASK가 제공됩니다. ASK 복사본을 저장하고 안전한 장소에 보관합니다. Media Services에 ASK를 FairPlayAsk로 구성해야 합니다.
    
* FPS 클라이언트 쪽에서 다음을 설정해야 합니다.

  * **AC(앱 인증서)**: 운영 체제에서 일부 페이로드를 암호화하는 데 사용하는 공개 키가 포함된 .cer/.der 파일입니다. 플레이어에 필요하기 때문에 Media Services에서 이에 대해 알고 있어야 합니다. 키 배달 서비스는 해당 개인 키를 사용하여 암호를 해독합니다.

* FairPlay 암호화된 스트림을 재생하려면 먼저 실제 ASK를 받은 다음 실제 인증서를 생성합니다. 이 프로세스에서는 다음 세 가지 요소를 모두 만듭니다.

  * .der 파일
  * .pfx 파일
  * .pfx에 대한 암호

## <a name="fairplay-and-player-apps"></a>FairPlay 및 플레이어 앱

콘텐츠가 **Apple FairPlay**로 암호화된 경우, 개별 비디오 및 오디오 샘플은 **AES-128 CBC** 모드를 사용하여 암호화됩니다. **FairPlay 스트리밍** (FPS)은 디바이스 운영 체제에 통합되며, iOS 및 Apple TV에서 고유하게 지원됩니다. OS X의 Safari는 EME(Encrypted Media Extensions) 인터페이스 지원을 사용하여 FPS를 지원합니다.

Azure Media Player는 FairPlay 재생도 지원합니다. 자세한 내용은 [Azure Media Player 설명서](https://amp.azure.net/libs/amp/latest/docs/index.html)를 참조하세요.

iOS SDK를 사용하여 고유한 플레이어 앱을 개발할 수 있습니다. FairPlay 콘텐츠를 재생하려면 라이선스 교환 프로토콜을 구현해야 합니다. Apple에서는 이 프로토콜을 지정하지 않습니다. 키 배달 요청을 전송하는 방법은 앱마다 다릅니다. Media Services FairPlay 키 배달 서비스에서는 SPC가 다음 형식의 www-form-url 인코딩된 게시 메시지로 도착해야 합니다.

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay 구성 .NET 예제

Media Services API를 사용하여 FairPlay 라이선스를 구성할 수 있습니다. 플레이어가 FairPlay로 보호된 콘텐츠를 재생하려고 하면 라이선스 배달 서비스로 요청이 전송되어 라이선스를 얻습니다. 라이선스 서비스가 요청을 승인하면 서비스에서 라이선스를 발급합니다. 클라이언트로 전송되며 지정된 콘텐츠를 암호 해독하고 재생하는 데 사용됩니다.

> [!NOTE]
> 일반적으로 인증과 ASK 집합 하나만 있기 때문에 FairPlay 정책 옵션은 한 번만 구성하면 됩니다.

다음 예제에서는 [Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet)를 사용하여 라이선스를 구성합니다.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>다음 단계

[DRM으로 보호](protect-with-drm.md)하는 방법 확인
