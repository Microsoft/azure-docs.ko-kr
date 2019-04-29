---
title: castLabs를 사용하여 Azure Media Services에 Widevine 라이선스 제공 | Microsoft 문서
description: 이 문서에서는 Azure Media Services(AMS)를 사용하여 PlayReady와 Widevine DRM이 모두 있는 AMS에서 동적으로 암호화된 스트림을 전달하는 방법을 설명합니다. PlayReady 라이선스는 Media Services PlayReady 라이선스 서버에서 제공되며 Widevine 라이선스는 castLabs 라이선스 서버에서 제공됩니다.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Mingfeiy;willzhan;Juliako
ms.openlocfilehash: dfb82e91b0f65b85d34b7e20d57ed9929469321f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232584"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>castLabs를 사용하여 Azure Media Services에 Widevine 라이선스 제공 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>개요

이 문서에서는 Azure Media Services(AMS)를 사용하여 PlayReady와 Widevine DRM이 모두 있는 AMS에서 동적으로 암호화된 스트림을 전달하는 방법을 설명합니다. PlayReady 라이선스는 Media Services PlayReady 라이선스 서버에서 제공되며 Widevine 라이선스는 **castLabs** 라이선스 서버에서 제공됩니다.

CENC(PlayReady 및/또는 Widevine)에 의해 보호되는 스트리밍 콘텐츠를 재생하려면 [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용할 수 있습니다. 자세한 내용은 [AMP 문서](https://amp.azure.net/libs/amp/latest/docs/) 를 참조하십시오.

다음 다이어그램은 고도의 Azure Media Services 및 castLabs 통합 아키텍처를 보여줍니다.

![통합](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>일반적인 시스템 설정

* 미디어 콘텐츠는 AMS에 저장됩니다.
* 콘텐츠 키의 키 ID는 castLabs 및 AMS에 저장됩니다.
* castLabs 및 AMS에는 모두 내장된 토큰 인증이 있습니다. 다음 섹션에서는 인증 토큰에 대해 설명합니다. 
* 클라이언트가 비디오 스트리밍을 요청하면 콘텐츠는 **일반 암호화** (CENC)를 통해 동적으로 암호화되며 AMS는 부드러운 스트리밍 및 DASH로 동적으로 패키징됩니다. HLS 스트리밍 프로토콜에 대한 PlayReady M2TS 기본 스트림 암호화를 전달할 수도 있습니다.
* PlayReady 라이선스는 AMS 라이선스 서버에서 검색되고 Widevine 라이선스는 castLabs 라이선스 서버에서 검색됩니다. 
* Media Player는 클라이언트 플랫폼 기능에 따라 가져올 라이선스를 자동으로 결정합니다. 

## <a name="authentication-token-generation-for-getting-a-license"></a>라이선스를 가져오기 위한 인증 토큰 생성

castLabs 및 AMS는 둘 다 라이선스를 인증하는 데 사용되는 JWT(JSON 웹 토큰) 토큰 형식을 지원합니다. 

### <a name="jwt-token-in-ams"></a>AMS의 JWT 토큰

다음 표에서는 AMS의 JWT 토큰을 설명합니다. 

| 발급자 | 선택한 STS(보안 토큰 서비스)의 발급자 문자열 |
| --- | --- |
| 대상 |사용된 STS의 대상 문자열 |
| 클레임 |클레임 집합 |
| NotBefore |토큰의 시작 유효성 |
| 만료 |토큰의 종료 유효성 |
| SigningCredentials |PlayReady 라이선스 서버, castLabs 라이선스 서버 및 STS 간에 공유되는 키로, 대칭 또는 비대칭 키일 수 있습니다. |

### <a name="jwt-token-in-castlabs"></a>castLabs의 JWT 토큰

다음 표에서는 castLabs의 JWT 토큰을 설명합니다. 

| 이름 | 설명 |
| --- | --- |
| optData |사용자에 대한 정보가 포함된 JSON 문자열. |
| crt |자산, 해당 자산의 라이선스 정보 및 재생 권한에 대한 정보가 포함된 JSON 문자열. |
| iat |Epoch의 현재 날짜/시간. |
| jti |이 토큰에 대한 고유 식별자(모든 토큰은 castLabs 시스템에서 한 번만 사용할 수 있음). |

## <a name="sample-solution-setup"></a>샘플 솔루션 설정

[샘플 솔루션](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) 은 다음 두 프로젝트로 구성되어 있습니다.

* PlayReady 및 Widevine에 대해 이미 수집된 자산에 DRM 제한을 설정하는 데 사용할 수 있는 콘솔 응용 프로그램.
* 토큰을 배포하는 웹 애플리케이션. STS의 간소화된 버전으로 볼 수 있습니다.

콘솔 애플리케이션을 사용하려면

1. app.config를 변경하여 AMS 자격 증명, castLabs 자격 증명, STS 구성 및 공유 키를 설정합니다.
2. AMS에 자산을 업로드합니다.
3. 업로드된 자산에서 UUID를 가져오고 Program.cs 파일의 32행을 다음과 같이 변경합니다.
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. castLabs 시스템에서 AssetId를 사용하여 자산의 이름을 지정합니다(Program.cs 파일의 44행).
   
   **castLabs**에 대해 AssetId를 설정해야 하며, 고유한 영숫자 문자열로 지정해야 합니다.
5. 프로그램을 실행합니다.

웹 애플리케이션(STS)을 사용하려면

1. web.config를 변경하여 castlabs 판매자 ID, STS 구성 및 공유 키를 설정합니다.
2. Azure Websites에 배포합니다.
3. 웹 사이트로 이동합니다.

## <a name="playing-back-a-video"></a>비디오 재생

일반 암호화(PlayReady 및/또는 Widevine)를 사용하여 암호화된 비디오를 재생하려면 [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용할 수 있습니다. 콘솔 앱을 실행하는 경우 콘텐츠 키 ID와 매니페스트 URL이 화면에 표시됩니다.

1. 새 탭을 열고 STS를 시작합니다(http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]).
2. [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html)로 이동합니다.
3. 스트리밍 URL에 붙여 넣습니다.
4. **고급 옵션** 확인란을 클릭합니다.
5. **보호** 드롭다운에서 PlayReady 및/또는 Widevine를 선택합니다.
6. STS에서 가져온 토큰을 토큰 텍스트 상자에 붙여 넣습니다. 
   
   castLab 라이선스 서버는 토큰 앞에 "Bearer =" 접두사가 필요하지 않습니다. 따라서 토큰을 제출하기 전에 제거하세요.
7. 플레이어를 업데이트합니다.
8. 비디오가 재생됩니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

