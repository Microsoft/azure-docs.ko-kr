---
title: 사용자 지정 정책에서 RESTful 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 RESTful 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edad748bc2192f98b9674b80dada5b03aa9ee2d1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77197989"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 RESTful 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 사용자 고유의 RESTful 서비스에 대 한 지원을 제공 합니다. Azure AD B2C는 입력 클레임 컬렉션의 RESTful 서비스로 데이터를 보내고 출력 클레임 컬렉션에 데이터를 다시 수신합니다. RESTful 서비스 통합을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- **사용자 입력 데이터의 유효성 검사** - 잘못된 형식의 데이터가 Azure AD B2C에 저장되지 않도록 방지합니다. 사용자의 값이 유효하지 않으면 RESTful 서비스는 사용자에게 항목을 제공하도록 지시하는 오류 메시지를 반환합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객 데이터베이스에서 종료되었는지 확인할 수 있습니다.
- **입력 클레임 덮어쓰기** - 입력 클레임의 값 서식을 다시 지정할 수 있습니다. 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다.
- **사용자 데이터 보강** - 회사 LOB(기간 업무) 애플리케이션과 추가로 통합할 수 있습니다. 예를 들어 Your RESTful 서비스는 사용자의 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환할 수 있습니다. 반환 클레임을 저장하거나, 다음 오케스트레이션 단계에서 계산하거나, 액세스 토큰에 포함할 수 있습니다.
- **사용자 지정 비즈니스 논리 실행** - 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 사용 권한을 관리하고, 데이터베이스를 감사하고, 다른 작업을 수행할 수 있습니다.

사용자 정책에서 입력 클레임을 REST API로 보낼 수 있습니다. REST API는 나중에 정책에서 사용할 수 있는 출력 클레임을 반환하거나, 오류 메시지를 throw할 수 있습니다. 다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

- **유효성 검사 기술 프로필** - 유효성 검사 기술 프로필은 RESTful 서비스를 호출합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 오류 메시지가 자체 어설션된 페이지에 표시되고 출력 클레임에 반환됩니다.
- **클레임 교환** - 오케스트레이션 단계를 통해 RESTful 서비스가 호출됩니다. 이 시나리오에서는 오류 메시지를 렌더링하는 사용자 인터페이스가 없습니다. REST API가 오류를 반환하는 경우 오류 메시지와 함께 사용자가 신뢰 당사자 애플리케이션으로 다시 리디렉션됩니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제는 RESTful 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>입력 클레임

**InputClaims** 요소는 REST API로 보낼 클레임 목록을 포함합니다. 클레임 이름을 REST API에서 정의된 이름에 매핑할 수도 있습니다. 다음 예제는 정책과 REST API 간의 매핑을 보여 줍니다. **givenName**은 REST API에 **firstName**으로 전송되는 반면, **surname**은 **lastName**으로 전송됩니다. **email** 클레임은 현재 상태대로 설정됩니다.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 요소는 REST API로 보내기 전에 입력 클레임을 수정하거나 새 입력 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

## <a name="send-a-json-payload"></a>JSON 페이로드 보내기

REST API 기술 프로필을 사용 하면 복잡 한 JSON 페이로드를 끝점으로 보낼 수 있습니다.

복잡 한 JSON 페이로드를 보내려면 다음을 수행 합니다.

1. [Generatejson](json-transformations.md) 클레임 변환을 사용 하 여 json 페이로드를 빌드합니다.
1. REST API 기술 프로필에서 다음을 수행 합니다.
    1. `GenerateJson` 클레임 변환에 대 한 참조를 사용 하 여 입력 클레임 변환을 추가 합니다.
    1. 메타 데이터 `SendClaimsIn` 옵션을 `body`로 설정 합니다.
    1. `ClaimUsedForRequestPayload` 메타 데이터 옵션을 JSON 페이로드가 포함 된 클레임의 이름으로 설정 합니다.
    1. 입력 클레임에서 JSON 페이로드를 포함 하는 입력 클레임에 대 한 참조를 추가 합니다.

다음 `TechnicalProfile` 예에서는 타사 전자 메일 서비스 (이 경우 SendGrid)를 사용 하 여 확인 전자 메일을 보냅니다.

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 REST API에서 반환된 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 REST API에서 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, REST API ID 공급자에서 반환되지 않은 클레임을 포함할 수도 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

다음 예제는 REST API에서 반환된 클레임을 보여 줍니다.

- **loyaltyNumber** 클레임 이름에 매핑된 **MembershipId** 클레임입니다.

기술 프로필은 ID 공급자에서 반환되지 않은 클레임도 반환합니다.

- 기본값이 **로 설정된** loyaltyNumberIsNew`true` 클레임입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ServiceUrl | yes | REST API 엔드포인트의 URL입니다. |
| AuthenticationType | yes | RESTful 클레임 공급자가 수행하는 인증 형식입니다. 가능한 값은 `None`, `Basic`, `Bearer` 또는 `ClientCertificate`입니다. `None` 값은 REST API가 익명이 아님을 나타냅니다. `Basic` 값은 REST API가 HTTP 기본 인증으로 보호됨을 나타냅니다. Azure AD B2C를 포함하여 확인된 사용자만 API에 액세스할 수 있습니다. `ClientCertificate` (권장) 값은 REST API 클라이언트 인증서 인증을 사용 하 여 액세스를 제한 함을 나타냅니다. Azure AD B2C와 같이 적절 한 인증서가 있는 서비스만 API에 액세스할 수 있습니다. `Bearer` 값은 REST API 클라이언트 OAuth2 전달자 토큰을 사용 하 여 액세스를 제한 함을 나타냅니다. |
| SendClaimsIn | 예 | 입력 클레임이 RESTful 클레임 공급자에게 전송되는 방법을 지정합니다. 가능한 값은 `Body`(기본값), `Form`, `Header` 또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문에 전송되는 입력 클레임입니다. `Form` 값은 앰퍼샌드 '&'로 구분된 키 값 형식의 요청 본문에 전송되는 입력 클레임입니다. `Header` 값은 요청 헤더에 전송되는 입력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에 전송되는 입력 클레임입니다. 각각에 의해 호출 되는 HTTP 동사는 다음과 같습니다.<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | 예 | 출력 클레임의 형식을 지정합니다. 가능한 값은 `Body`(기본값), `Form`, `Header` 또는 `QueryString`입니다. `Body` 값은 JSON 형식의 요청 본문에 전송되는 출력 클레임입니다. `Form` 값은 앰퍼샌드 '&'로 구분된 키 값 형식의 요청 본문에 전송되는 출력 클레임입니다. `Header` 값은 요청 헤더에 전송되는 출력 클레임입니다. `QueryString` 값은 요청 쿼리 문자열에 전송되는 출력 클레임입니다. |
| ClaimUsedForRequestPayload| 예 | REST API 전송 될 페이로드를 포함 하는 문자열 클레임의 이름입니다. |
| DebugMode | 예 | 디버그 모드에서 기술 프로필을 실행합니다. 가능한 값은 `true`또는 `false` (기본값)입니다. 디버그 모드에서 REST API는 자세한 정보를 반환할 수 있습니다. [오류 메시지 반환](#returning-error-message) 섹션을 참조 하세요. |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 [클레임 확인](claim-resolver-overview.md) 이 기술 프로필에 포함 되는지 여부를 지정 합니다. 가능한 값은 `true`또는 `false` (기본값)입니다. 기술 프로필에서 클레임 해결 프로그램을 사용 하려면 `true`으로 설정 합니다. |

## <a name="cryptographic-keys"></a>암호화 키

인증 형식이 `None`으로 설정된 경우 **CryptographicKeys** 요소가 사용되지 않습니다.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

인증 형식이 `Basic`으로 설정된 경우 **CryptographicKeys** 요소에 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | yes | 인증에 사용되는 사용자 이름입니다. |
| BasicAuthenticationPassword | yes | 인증에 사용되는 암호입니다. |

다음 예제는 기본 인증을 사용하는 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

인증 형식이 `ClientCertificate`으로 설정된 경우 **CryptographicKeys** 요소에 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClientCertificate | yes | 인증에 사용할 X509 인증서(RSA 키 집합)입니다. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

인증 형식이 `Bearer`으로 설정된 경우 **CryptographicKeys** 요소에 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | 예 | OAuth 2.0 전달자 토큰입니다. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>오류 메시지 반환

REST API가 'CRM 시스템에서 사용자를 찾을 수 없습니다.'와 같은 오류 메시지를 반환해야 할 수 있습니다. 오류가 발생 하면 REST API는 HTTP 409 오류 메시지 (충돌 응답 상태 코드)를 다음 특성으로 반환 해야 합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 버전 | yes | 1.0.0 |
| 상태 | yes | 409 |
| 코드 | 예 | `DebugMode`를 사용으로 설정한 경우에 표시되는 RESTful 엔드포인트 공급자의 오류 코드입니다. |
| requestId | 예 | `DebugMode`를 사용으로 설정한 경우에 표시되는 RESTful 엔드포인트 공급자의 요청 식별자입니다. |
| userMessage | yes | 사용자에게 표시되는 오류 메시지입니다. |
| developerMessage | 예 | `DebugMode`를 사용으로 설정한 경우에 표시되는, 문제점 및 해결 방법에 대한 자세한 설명입니다. |
| moreInfo | 예 | `DebugMode`를 사용으로 설정한 경우에 표시되는, 추가 정보를 가리키는 URI입니다. |

다음 예제는 JSON 형식의 오류 메시지를 반환하는 REST API를 보여 줍니다.

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

다음 예제는 오류 메시지를 반환하는 C# 클래스를 보여 줍니다.

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>다음 단계

RESTful 기술 프로필을 사용 하는 예제는 다음 문서를 참조 하세요.

- [Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합](rest-api-claims-exchange-dotnet.md)
- [HTTP 기본 인증을 사용하여 RESTful 서비스 보호](secure-rest-api-dotnet-basic-auth.md)
- [클라이언트 인증서를 사용하여 RESTful 서비스 보호](secure-rest-api-dotnet-certificate-auth.md)
- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력에 대한 유효성 검사로 통합](custom-policy-rest-api-claims-validation.md)
