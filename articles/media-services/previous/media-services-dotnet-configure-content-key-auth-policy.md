---
title: Media Services .NET SDK를 사용하여 콘텐츠 키 권한 부여 정책 구성 | Microsoft Docs
description: Media Services .NET SDK를 사용하여 콘텐츠 키에 대한 인증 정책을 구성하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;mingfeiy
ms.openlocfilehash: 744887a3b23518a5b970a3fda94bf990806bd2d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230273"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>동적 암호화: 콘텐츠 키 인증 정책 구성
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>개요
 Microsoft Azure Media Services를 사용하면 AES(Advanced Encryption Standard)(128비트 암호화 키 사용) 또는 [PlayReady DRM(디지털 권한 관리)](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HTTP-Live-Streaming(HLS) 스트림을 배달할 수 있습니다. Media Services를 사용하면 Widevine DRM으로 암호화된 DASH 스트림을 전달할 수도 있습니다. PlayReady와 Widevine은 모두 일반 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다.

또한 Media Services는 키/라이선스 배달 서비스를 제공하여 클라이언트가 암호화된 콘텐츠를 재생할 수 있는 AES 키 또는 PlayReady/Widevine 라이선스를 받을 수 있습니다.

Media Services에서 자산을 암호화하려는 경우 암호화 키(CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결해야 합니다. 자세한 내용은 [.NET을 사용하여 콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md)를 참조하세요. 또한 이 문서에 설명된 대로 키에 대한 권한 부여 정책을 구성해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하고 AES 또는 DRM 암호화를 사용하여 동적으로 사용자의 콘텐츠를 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

Media Services는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책에는 하나 이상의 인증 제한이 있을 수 있습니다 옵션은 열기 또는 토큰 제한입니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services 지원 토큰에는 [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(간단한 웹 토큰) 형식 및 [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JSON Web Token) 형식의 토큰을 지원합니다.

Media Services는 STS를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Azure Access Control Service를 사용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다(이 문서에서 설명). 토큰이 유효하고 해당 토큰의 클레임이 콘텐츠 키에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 암호화 키를 클라이언트에게 반환합니다.

자세한 내용은 다음 문서를 참조하세요.

- [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Active Directory와 Azure Media Services OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기반으로 하는 콘텐츠 키 배달을 제한합니다](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

### <a name="some-considerations-apply"></a>다음과 같은 몇 가지 고려 사항이 적용됩니다.
* Azure Media Services 계정이 만들어지면 기본 스트리밍 엔드포인트가 “중지됨” 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 스트리밍 엔드포인트가 “실행 중” 상태에 있어야 합니다. 
* 사용자의 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조하세요.
* AssetCreationOptions.StorageEncrypted 옵션을 사용하여 자산을 업로드하고 인코딩합니다.
* 동일한 정책 구성이 필요한 여러 콘텐츠 키를 사용하려는 경우 단일 권한 부여 정책을 만들고 여러 콘텐츠 키와 함께 다시 사용하는 것이 좋습니다.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체(정책 옵션 및 제한 사항)를 15분 동안 캐시합니다. ContentKeyAuthorizationPolicy를 만들고 토큰 제한을 사용하도록 지정하고, 테스트한 다음, 정책을 개방형 제한으로 업데이트할 수 있습니다. 이 프로세스를 수행할 경우 대략 15분 후에 정책이 개방형 버전의 정책으로 전환됩니다.
* 자산 배달 정책을 추가하거나 업데이트하는 경우, 기존 로케이터를 삭제하고 새 로케이터를 만들어야 합니다.
* 현재 점진적 다운로드는 암호화할 수 없습니다.
* Media Services 스트리밍 엔드포인트는 실행 전 응답에서 CORS 'Access-Control-Allow-Origin' 헤더 값을 ‘\*’ 와일드카드로 설정합니다. 이 값은 Azure Media Player, Roku 및 JWPlayer 등을 망라한 대부분의 플레이어에서 작동합니다. 그러나 자격 증명 모드가 “include”로 설정된 상태에서 dashjs의 XMLHttpRequest가 “\*” 와일드카드를 Access-Control-Allow-Origin 값으로 허용하지 않으므로 dash.js를 사용하는 일부 플레이어에서는 작동하지 않습니다. 이러한 dashjs 제한을 해결하기 위해 단일 도메인에서 클라이언트를 호스트하는 경우 Media Services가 실행 전 응답 헤더에서 해당 도메인을 지정할 수 있습니다. 도움이 필요한 경우 Azure Portal을 통해 지원 티켓을 엽니다.

## <a name="aes-128-dynamic-encryption"></a>AES-128 동적 암호화
### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 열기 권한 부여 정책을 만들고 콘텐츠 키에 추가합니다.
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>토큰 제한
이 섹션에서는 콘텐츠 키 인증 정책을 만들고 콘텐츠 키와 연결하는 방법을 설명합니다. 권한 부여 정책은 사용자가 키를 받을 권한이 있는지 여부를 확인하기 위해 충족해야 하는 권한 부여 요구 사항에 대해 설명합니다. 예를 들어 확인 키 목록에 토큰 서명에 사용된 키가 포함되어 있나요?

토큰 제한 옵션을 구성하려면 XML을 사용하여 토큰의 권한 부여 요구 사항을 설명해야 합니다. 토큰 제한 구성 XML은 다음 XML 스키마를 준수 해야 합니다.
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

.NET용 Media Services SDK를 사용할 때 TokenRestrictionTemplate 클래스를 사용하여 제한 토큰을 생성할 수 있습니다.
다음 예제에서는 토큰 제한으로 인증 정책을 만듭니다. 이 예제에서는 클라이언트가 서명 키(VerificationKey)가 포함된 토큰, 토큰 발급자 및 필요한 클레임을 제공해야 합니다.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>테스트 토큰
키 권한 부여 정책에 사용된 토큰 제한에 따라 테스트 토큰을 가져오려면 다음을 참조하세요.
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady 동적 암호화
Media Services를 사용하면 사용자가 보호된 콘텐츠를 재생하려고 할 때 PlayReady DRM 런타임에서 적용할 권한 및 제한 사항을 구성할 수 있습니다. 

PlayReady로 콘텐츠를 보호하려는 경우 권한 부여 정책에서 지정해야 하는 항목 중 하나는 [PlayReady 라이선스 템플릿](media-services-playready-license-template-overview.md)을 정의하는 XML 문자열입니다. .NET용 Media Services SDK에서 PlayReadyLicenseResponseTemplate 및 PlayReadyLicenseTemplate 클래스는 PlayReady 라이선스 템플릿을 정의하는 데 도움이 됩니다.

PlayReady 및 Widevine으로 콘텐츠를 암호화하는 방법을 알아보려면 [PlayReady 및/또는 Widevine 동적 일반 암호화 사용](media-services-protect-with-playready-widevine.md)을 참조하세요.

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 열기 권한 부여 정책을 만들고 콘텐츠 키에 추가합니다.

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>토큰 제한
토큰 제한 옵션을 구성하려면 XML을 사용하여 토큰의 권한 부여 요구 사항을 설명해야 합니다. 토큰 제한 구성 XML은 “토큰 제한 스키마” 섹션에 표시된 XML 스키마를 준수해야 합니다.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

키 권한 부여 정책에 사용된 토큰 제한에 따라 테스트 토큰을 가져오려면 “[테스트 토큰](#test-token)” 섹션을 참조하세요. 

## <a id="types"></a>ContentKeyAuthorizationPolicy를 정의할 때 사용되는 형식
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
콘텐츠 키의 권한 부여 정책을 구성했으므로 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조하세요.

