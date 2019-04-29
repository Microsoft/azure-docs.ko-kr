---
title: Axinom을 사용하여 Azure Media Services에 Widevine 라이선스 배달 | Microsoft 문서
description: 이 문서에서는 Azure Media Services(AMS)를 사용하여 PlayReady와 Widevine DRM이 모두 있는 AMS에서 동적으로 암호화된 스트림을 전달하는 방법을 설명합니다. PlayReady 라이선스는 Media Services PlayReady 라이선스 서버에서 제공되며 Widevine 라이선스는 Axinom 라이선스 서버에서 제공됩니다.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 6714beae690e23c686fc08b88e93044ae3901c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245042"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Axinom을 사용하여 Azure Media Services에 Widevine 라이선스 제공 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>개요
Azure Media Services(AMS)에 Google Widevine 동적 보호가 추가되었습니다(자세한 내용은 [Mingfei의 블로그](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) 참조). 또한 Azure Media Player(AMP)에도 Widevine 지원이 추가되었습니다(자세한 내용은 [AMP 문서](https://amp.azure.net/libs/amp/latest/docs/) 참조). 이는 MSE 및 EME가 포함된 현대식 브라우저에 대한 다중 원시 DRM(PlayReady 및 Widevine)를 가진 CENC로 보호되는 DASH 콘텐츠 합리화의 주요 성과입니다.

Media Services .NET SDK 버전 3.5.2부터는 Media Services를 사용하여 Widevine 라이선스 템플릿을 구성하고 Widevine 라이선스를 얻을 수 있습니다. 또한 다음 AMS 파트너를 사용하여 Widevine 라이선스를 배달할 수 있습니다. 파트너([Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/))를 사용하여 Widevine 라이선스를 배달할 수 있습니다.

이 문서에서는 Axinom에서 관리하는 Widevine 라이선스 서버를 통합하고 테스트하는 방법을 설명합니다. 구체적으로 다음 사항을 다룹니다.  

* 해당 라이선스 취득 URL이 포함된 다중-DRM(PlayReady 및 Widevine)을 사용하여 동적 일반 암호화 구성;
* 라이선스 서버 요구 사항을 충족하기 위해 JWT 토큰 생성;
* JWT 토큰 인증으로 라이선스 취득을 처리하는 Azure Media Player 앱 개발

전체 시스템 및 콘텐츠 키, 키 ID, 키 시드, JTW 토큰 및 해당 클레임의 흐름을 다음 다이어그램으로 가장 잘 설명할 수 있습니다.

![대시 및 CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
동적 보호 및 키 배달 정책을 구성하려면 Mingfei의 블로그 [Media Services를 사용하여 Widevine 패키징 구성](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)을 참조하세요.

다음 두 가지가 모두 있는 DASH 스트리밍용 다중 DRM으로 동적 CENC 보호를 구성할 수 있습니다.

1. 토큰 인증 제한이 포함될 수 있는 Microsoft Edge 및 IE11에 대한 PlayReady 보호. 토큰 제한 정책은 Azure Active Directory 등과 같은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다.
2. Chrome에 대한 Widevine 보호의 경우 다른 STS가 발급한 토큰을 이용한 토큰 인증이 필요할 수 있습니다. 

Azure Active Directory를 Axinom의 Widevine 라이선스 서버에 대한 STS로 사용할 수 없는 이유는 [JWT 토큰 생성](media-services-axinom-integration.md#jwt-token-generation)을 참조하세요.

### <a name="considerations"></a>고려 사항
1. Axinom 지정 키 시드(8888000000000000000000000000000000000000) 및 사용자가 생성하거나 선택한 키 ID를 사용하여 키 배달 서비스를 구성하기 위한 콘텐츠 키를 생성해야 합니다. Axinom 라이선스 서버는 테스트와 생산에 모두 유효한 동일한 키 시드를 기반으로 콘텐츠 키를 포함하고 있는 모든 라이선스를 발급합니다.
2. 테스트용 Widevine 라이선스 획득 URL: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). HTTP 및 HTTS 모두 허용됩니다.

## <a name="azure-media-player-preparation"></a>Azure Media Player 준비
AMP v1.4.0은 PlayReady와 Widevine DRM 둘 다를 사용하여 동적으로 패키징된 AMS 콘텐츠의 재생을 지원합니다.
Widevine 라이선스 서브에 토큰 인증이 필요하지 않은 경우 Widevine에서 보호하는 DASH 콘텐츠를 테스트하기 위해 추가로 수행해야 하는 일은 없습니다. 예를 들어 AMP 팀에는 PlayReady를 지원하는 Microsoft Edge 및 IE11과 Widevine을 지원하는 Chrome에서 작동 모습을 볼 수 있는 간단한 [샘플](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)을 제공합니다.
Axinom 제공한 Widevine 라이선스 서버에는 JWT 토큰 인증이 필요합니다. HTTP 헤더 “X-AxDRM-Message”를 통해 라이선스 요청과 함께 JWT 토큰을 전송해야 합니다. 이 목적을 위해 원본을 설정하기 전에 웹 페이지 호스팅 AMP에서 다음과 같은 javascript를 추가해야 합니다.

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

AMP 코드의 나머지 부분은 AMP 문서 [여기](https://amp.azure.net/libs/amp/latest/docs/)에서와 같이 표준 AMP API입니다.

사용자 지정 인증 헤더를 설정하기 위한 위의 javascript는 AMP의 공식적인 장기적 방식이 출시되기 전의 단기적인 방식입니다.

## <a name="jwt-token-generation"></a>JWT 토큰 생성
테스트용 Axinom Widevine 라이선스 서버에는 JWT 토큰 인증이 필요합니다. 또한 JWT 토큰의 클레임 중 하나는 기본 데이터 형식이 아닌 복잡한 개체 유형으로 되어 있습니다.

아쉽게도 Azure AD는 기본 형식의 JWT 토큰만 발급할 수 있습니다. 마찬가지로,.NET Framework API(System.IdentityModel.Tokens.SecurityTokenHandler 및 JwtPayload)를 사용하면 복잡한 개체 유형을 클레임으로 입력하는 것만 가능합니다. 그러나 클레임은 여전히 문자열로 직렬화됩니다. 그러므로 Widevine 라이선스 요청에 대한 JWT 토큰을 생성하는 경우 둘 중 하나를 사용할 수 없습니다.

John Sheehan의 [JWT NuGet 패키지](https://www.nuget.org/packages/JWT) 는 요구 사항에 부합하므로 이 NuGet 패키지를 사용하겠습니다.

다음은 테스트를 위해 Axinom Widevine 라이선스 서버에서 요구한 대로 필요한 클레임을 사용하여 JWT 토큰을 생성하기 위한 코드입니다.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine 라이선스 서버

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>고려 사항
1. AMS PlayReady 라이선스 배달 서비스의 경우 인증 토큰 앞에 “Bearer=”가 필요하지만, Axinom Widevine 라이선스 서버는 이를 사용하지 않습니다.
2. Axinom 통신 키는 서명 키로 사용됩니다. 키는 16진 문자열이지만 인코딩할 때에는 문자열이 아닌 일련의 바이트로 처리해야 합니다. ConvertHexStringToByteArray 메서드를 사용하면 이 목적이 달성됩니다.

## <a name="retrieving-key-id"></a>키 ID 검색
JWT 토큰을 생성하기 위한 코드에서 키 ID가 필요하다는 것을 알 수 있습니다. JWT 토큰은 AMP 플레이어를 로드하기 전에 준비되어 있어야 하므로 JWT 토큰을 생성하려면 키 ID를 검색해야 합니다.

물론 키 ID를 유지하는 여러 가지 방법이 있습니다. 예를 들어 키 ID를 콘텐츠 메타데이터와 함께 데이터베이스에 저장할 수 있습니다. 또는 DASH MPD(미디어 프레젠테이션 설명) 파일에서 키 ID를 검색할 수 있습니다. 아래 코드는 후자의 경우입니다.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>요약
최근 Azure Media Services Content Protection 및 Azure Media Player에 모두 Widevine 지원이 추가됨에 따라, AMS의 PlayReady 라이선스 서비스 및 다음과 같은 현대적인 브라우저에 대한 Axinom의 Widevine 라이선스 서버를 사용하여 DASH + 다중 원시 DRM(PlayReady + Widevine)의 스트리밍을 구현할 수 있습니다.

* Chrome
* Windows 10용 Microsoft Edge
* Windows 8.1 및 Windows 10용 IE 11
* Mac(iOS 제외)용 Firefox(데스크톱) 및 Safari도 Silverlight 및 Azure Media Player와 같은 URL을 통해 지원됩니다.

Axinom Widevine 라이선스 서버를 활용하는 미니 솔루션에는 다음과 같은 매개 변수가 필요합니다. 키 ID를 제외하고 매개 변수의 나머지 부분은 Widevine 서버 설정에 따라 Axinom에서 제공됩니다.

| 매개 변수 | 사용 방법 |
| --- | --- |
| 통신 키 ID |JWT 토큰의 클레임 "com_key_id"의 값으로 포함되어야 합니다([이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |
| 통신 키 |JWT 토큰의 서명 키로 사용해야 합니다( [이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |
| 키 시드 |지정된 콘텐츠 키 ID로 콘텐츠 키를 생성하는 데 사용해야 합니다([이](media-services-axinom-integration.md#content-protection) 섹션 참조). |
| Widevine 라이선스 획득 URL |DASH 스트리밍에 대한 자산 배달 정책 구성에 사용해야 합니다([이](media-services-axinom-integration.md#content-protection) 섹션 참조). |
| 콘텐츠 키 ID |JWT 토큰의 자격 부여 메시지 클레임 값의 일부로 포함되어야 합니다( [이](media-services-axinom-integration.md#jwt-token-generation) 섹션 참조). |

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>승인
이 문서를 작성하는 데 기여해 주신 Kristjan Jõgi of Axinom, Mingfei Yan, Amit Rajput에게 감사 드립니다.

