---
title: REST를 사용하여 콘텐츠 키 인증 정책 구성 - Azure | Microsoft Docs
description: Media Services REST API를 사용하여 콘텐츠 키에 대한 인증 정책을 구성하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: cb9b784e612b1dca6b5251cb5a20140e908158c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598440"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>동적 암호화: 콘텐츠 키 인증 정책 구성  
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>개요
 Azure Media Services를 사용하면 128비트 암호화 키와 PlayReady 또는 Widevine DRM(디지털 권한 관리)을 사용하여 AES(Advanced Encryption Standard)로 동적 암호화된 콘텐츠를 배달할 수 있습니다. 또한 Media Services는 인증된 클라이언트에게 키 및 PlayReady/Widevine 라이선스를 배달하는 서비스를 제공합니다.

Media Services에서 자산을 암호화하려는 경우 암호화 키(CommonEncryption 또는 EnvelopeEncryption)를 자산에 연결해야 합니다. 자세한 내용은 [REST를 사용하여 콘텐츠 키 만들기](media-services-rest-create-contentkey.md)를 참조하세요. 또한 이 문서에 설명된 대로 키에 대한 권한 부여 정책을 구성해야 합니다.

플레이어가 스트림을 요청하면 Media Services는 지정된 키를 사용하여 AES 또는 PlayReady 암호화를 통해 콘텐츠를 동적으로 암호화합니다. 스트림을 해독하기 위해 플레이어는 키 배달 서비스에서 키를 요청합니다. 사용자에게 키를 얻을 수 있는 권한이 있는지 여부를 결정하기 위해 서비스는 키에 지정된 권한 부여 정책을 평가합니다.

Media Services는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책에는 개방형 제한 또는 토큰 제한을 사용하여 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [SWT(단순 웹 토큰)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) 형식 및 JWT(JSON Web Token) 형식의 토큰을 지원합니다.

Media Services는 STS를 제공하지 않습니다. 사용자 지정 STS를 만들거나 Azure AD(Azure Active Directory)를 사용하여 토큰을 발급할 수 있습니다. 지정된 키로 서명된 토큰을 만들고 토큰 제한 구성에서 지정한 클레임을 발급하려면 반드시 STS를 구성해야 합니다(이 문서에서 설명). 토큰이 유효하고 해당 토큰의 클레임이 콘텐츠 키에 대해 구성된 클레임과 일치하는 경우 Media Services 키 배달 서비스는 암호화 키를 클라이언트에게 반환합니다.

자세한 내용은 다음 문서를 참조하세요.
- [JWT 토큰 인증](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services OWIN MVC 기반 앱을 Azure Active Directory와 통합하고 JWT 클레임을 기준으로 콘텐츠 키 배달 제한](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>다음과 같은 몇 가지 고려 사항이 적용됩니다.
* 동적 패키징 및 동적 암호화를 사용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 “실행” 상태인지 확인합니다.
* 사용자의 자산은 적응 비트 전송률 MP4 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합을 포함해야 합니다. 자세한 내용은 [자산 인코딩](media-services-encode-asset.md)을 참조하세요.
* AssetCreationOptions.StorageEncrypted 옵션을 사용하여 자산을 업로드하고 인코딩합니다.
* 동일한 정책 구성이 필요한 여러 콘텐츠 키를 사용하려는 경우 단일 권한 부여 정책을 만들고 여러 콘텐츠 키와 함께 다시 사용하는 것이 좋습니다.
* 키 배달 서비스는 ContentKeyAuthorizationPolicy 및 관련 개체(정책 옵션 및 제한 사항)를 15분 동안 캐시합니다. ContentKeyAuthorizationPolicy를 만들고 토큰 제한을 사용하도록 지정하고, 테스트한 다음, 정책을 개방형 제한으로 업데이트할 수 있습니다. 이 프로세스를 수행할 경우 대략 15분 후에 정책이 개방형 버전의 정책으로 전환됩니다.
* 자산 배달 정책을 추가하거나 업데이트하는 경우, 기존 로케이터를 삭제하고 새 로케이터를 만들어야 합니다.
* 현재 점진적 다운로드는 암호화할 수 없습니다.
* Media Services 스트리밍 엔드포인트는 실행 전 응답에서 CORS Access-Control-Allow-Origin 헤더 값을 “\*” 와일드카드로 설정합니다. 이 값은 Azure Media Player, Roku 및 JWPlayer 등을 망라한 대부분의 플레이어에서 작동합니다. 그러나 자격 증명 모드가 “include”로 설정된 상태에서 dash.js의 XMLHttpRequest가 “\*” 와일드카드를 Access-Control-Allow-Origin 값으로 허용하지 않으므로 dash.js를 사용하는 일부 플레이어에서는 작동하지 않습니다. 이러한 dash.js 제한을 해결하기 위해 단일 도메인에서 클라이언트를 호스트하는 경우 Media Services가 실행 전 응답 헤더에서 해당 도메인을 지정할 수 있습니다. 도움이 필요한 경우 Azure Portal을 통해 지원 티켓을 엽니다.

## <a name="aes-128-dynamic-encryption"></a>AES-128 동적 암호화
> [!NOTE]
> Media Services REST API를 사용할 때는 다음 사항을 고려해야 합니다.
> 
> Media Services에서 엔터티에 액세스하는 경우 HTTP 요청에 특정 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.
> 
> 
> 

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 열기 권한 부여 정책을 만들고 콘텐츠 키에 추가합니다.

#### <a id="ContentKeyAuthorizationPolicies"></a>ContentKeyAuthorizationPolicies 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 36

    {"Name":"Open Authorization Policy"}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 211
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
    request-id: aabfa731-e884-4bf3-8314-492b04747ac4
    x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 08:25:56 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}

#### <a id="ContentKeyAuthorizationPolicyOptions"></a>ContentKeyAuthorizationPolicyOptions 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 168

    {"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

응답:    

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 349
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
    request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
    x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 08:56:40 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}

#### <a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>ContentKeyAuthorizationPolicies를 옵션과 연결
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 154

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}

응답:

    HTTP/1.1 204 No Content

#### <a id="AddAuthorizationPolicyToKey"></a>콘텐츠 키에 권한 부여 정책 추가
요청:

    PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 78

    {"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}

응답:

    HTTP/1.1 204 No Content

### <a name="token-restriction"></a>토큰 제한
이 섹션에서는 콘텐츠 키 인증 정책을 만들고 콘텐츠 키와 연결하는 방법을 설명합니다. 권한 부여 정책은 사용자가 키를 받을 권한이 있는지 여부를 확인하기 위해 충족해야 하는 권한 부여 요구 사항에 대해 설명합니다. 예를 들어 확인 키 목록에 토큰 서명에 사용된 키가 포함되어 있나요?

토큰 제한 옵션을 구성하려면 XML을 사용하여 토큰의 권한 부여 요구 사항을 설명해야 합니다. 토큰 제한 구성 XML은 다음 XML 스키마를 준수 해야 합니다.


#### <a id="schema"></a>토큰 제한 스키마
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

토큰 제한 정책을 구성하는 경우 기본 확인 키, 발급자 및 대상 매개 변수를 지정해야 합니다. 기본 확인 키에는 토큰 서명에 사용된 키가 포함됩니다. 발급자는 토큰을 발급하는 STS입니다. 청중(범위) 라고도 함)은 토큰의 의도 또는 토큰이 접근을 인증하는 대상 리소스를 설명합니다. Media Services 키 배달 서비스는 이러한 토큰의 값이 템플릿 파일에 있는 값과 일치하는지 확인합니다.

다음 예제에서는 토큰 제한으로 인증 정책을 만듭니다. 이 예제에서는 클라이언트가 서명 키(VerificationKey)가 포함된 토큰, 토큰 발급자 및 필요한 클레임을 제공해야 합니다.

### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies 만들기
토큰 제한 정책을 만듭니다(“[ContentKeyAuthorizationPolicies 만들기](#ContentKeyAuthorizationPolicies)” 섹션 참조).

### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 1079

    {"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

응답:    

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1260
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
    request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
    x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:10:37 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies를 옵션과 연결
ContentKeyAuthorizationPolicies를 옵션과 연결합니다(“[ContentKeyAuthorizationPolicies 만들기](#ContentKeyAuthorizationPolicies)” 섹션 참조).

#### <a name="add-an-authorization-policy-to-the-content-key"></a>콘텐츠 키에 권한 부여 정책 추가
ContentKey에 AuthorizationPolicy를 추가합니다(“[콘텐츠 키에 권한 부여 정책 추가](#AddAuthorizationPolicyToKey)” 섹션 참조).

## <a name="playready-dynamic-encryption"></a>PlayReady 동적 암호화
Media Services를 사용하면 사용자가 보호된 콘텐츠를 재생하려고 할 때 PlayReady DRM 런타임에서 적용할 권한 및 제한 사항을 구성할 수 있습니다. 

PlayReady로 콘텐츠를 보호하려는 경우 권한 부여 정책에서 지정해야 하는 항목 중 하나는 [PlayReady 라이선스 템플릿](media-services-playready-license-template-overview.md)을 정의하는 XML 문자열입니다. 

### <a name="open-restriction"></a>열기 제한
열기 제한은 시스템이 키를 요청하는 사람에게 키를 제공하는 것을 의미합니다. 이 제한은 테스트 목적으로 유용할 수 있습니다.

다음 예제에서는 열기 권한 부여 정책을 만들고 콘텐츠 키에 추가합니다.

#### <a id="ContentKeyAuthorizationPolicies2"></a>ContentKeyAuthorizationPolicies 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 58

    {"Name":"Deliver Common Content Key"}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 233
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
    request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
    x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:26:00 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 593

    {"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 774
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
    request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
    x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:23:24 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies를 옵션과 연결
ContentKeyAuthorizationPolicies를 옵션과 연결합니다(“[ContentKeyAuthorizationPolicies 만들기](#ContentKeyAuthorizationPolicies)” 섹션 참조).

#### <a name="add-an-authorization-policy-to-the-content-key"></a>콘텐츠 키에 권한 부여 정책 추가
ContentKey에 AuthorizationPolicy를 추가합니다(“[콘텐츠 키에 권한 부여 정책 추가](#AddAuthorizationPolicyToKey)” 섹션 참조).

### <a name="token-restriction"></a>토큰 제한
토큰 제한 옵션을 구성하려면 XML을 사용하여 토큰의 권한 부여 요구 사항을 설명해야 합니다. 토큰 제한 구성 XML은 “[토큰 제한 스키마](#schema)” 섹션에 표시된 XML 스키마를 준수해야 합니다.

#### <a name="create-contentkeyauthorizationpolicies"></a>ContentKeyAuthorizationPolicies 만들기
ContentKeyAuthorizationPolicies를 만듭니다(“[ContentKeyAuthorizationPolicies 만들기](#ContentKeyAuthorizationPolicies2)” 섹션 참조).

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>ContentKeyAuthorizationPolicyOptions 만들기
요청:

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 3.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 1525

    {"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

응답:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1706
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
    request-id: ccf8a4ba-731e-4124-8192-079592c251cc
    x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Tue, 10 Feb 2015 09:58:47 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>ContentKeyAuthorizationPolicies를 옵션과 연결
ContentKeyAuthorizationPolicies를 옵션과 연결합니다(“[ContentKeyAuthorizationPolicies 만들기](#ContentKeyAuthorizationPolicies)” 섹션 참조).

#### <a name="add-an-authorization-policy-to-the-content-key"></a>콘텐츠 키에 권한 부여 정책 추가
ContentKey에 AuthorizationPolicy를 추가합니다(“[콘텐츠 키에 권한 부여 정책 추가](#AddAuthorizationPolicyToKey)” 섹션 참조).

## <a id="types"></a>ContentKeyAuthorizationPolicy를 정의할 때 사용되는 형식
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1, 
        IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
    }


> [!NOTE]
> 콘텐츠 키 인증 정책에 대한 IP 제한은 아직 서비스에서 사용할 수 없습니다.


### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
        Widevine = 3
    }


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
콘텐츠 키의 권한 부여 정책을 구성했으므로 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

