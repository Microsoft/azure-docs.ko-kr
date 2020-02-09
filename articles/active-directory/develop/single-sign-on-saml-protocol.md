---
title: Azure Single Sign On SAML 프로토콜 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory에서 Single Sign On SAML 프로토콜을 설명합니다.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701402"
---
# <a name="single-sign-on-saml-protocol"></a>Single Sign-On SAML 프로토콜

이 문서에서는 Azure AD(Azure Active Directory)에서 Single Sign-On에 대해 지원하는 SAML 2.0 인증 요청 및 응답에 대해 설명합니다.

아래 프로토콜 다이어그램은 Single Sign-On 시퀀스를 설명합니다. 클라우드 서비스(서비스 공급자)는 HTTP 리디렉션 바인딩을 사용하여 `AuthnRequest` (인증 요청) 요소를 Azure AD(ID 공급자)에 전달합니다. 그런 다음 Azure AD는 HTTP post 바인딩을 사용하여 `Response` 요소를 클라우드 서비스에 게시합니다.

![Single Sign On 워크플로](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

사용자 인증을 요청하기 위해 클라우드 서비스는 `AuthnRequest` 요소를 Azure AD에 보냅니다. `AuthnRequest` SAML 2.0 샘플은 다음 예제와 같습니다.

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| 매개 변수 |  | Description |
| --- | --- | --- |
| ID | 필수 | Azure AD는 이 특성을 사용하여 반환된 응답의 `InResponseTo` 특성을 채웁니다. ID는 숫자로 시작할 수 없으므로 GUID의 문자열 표현에 "id"와 같은 문자열을 앞에 추가합니다. 예를 들어 `id6c1c178c166d486687be4aaf5e482730` 은 유효한 ID입니다. |
| 버전 | 필수 | 이 매개 변수는 **2.0**으로 설정해야 합니다. |
| IssueInstant | 필수 | UTC 값과 [라운드 트립 형식("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)을 포함하는 DateTime 문자열입니다. Azure AD에는 이 형식의 DateTime 값이 필요하지만, 값을 평가하거나 사용하지 않습니다. |
| AssertionConsumerServiceUrl | 선택 사항 | 제공되는 경우 이 매개 변수는 Azure AD에서 클라우드 서비스의 `RedirectUri`와 일치해야 합니다. |
| ForceAuthn | 선택 사항 | 부울 값입니다. true이면 Azure AD에 유효한 세션이 있어도 사용자를 다시 인증해야 합니다. |
| IsPassive | 선택 사항 | 사용자 상호 작용 없이 세션 쿠키(있는 경우)를 사용하여 Azure AD가 사용자를 자동으로 인증할지를 지정하는 부울 값입니다. True이면 Azure AD는 세션 쿠키를 사용하여 사용자 인증을 시도합니다. |

다른 모든 `AuthnRequest` 특성(예: onsent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex 및 ProviderName)은 **무시**됩니다.

Azure AD도 `AuthnRequest`에서 `Conditions` 요소를 무시합니다.

### <a name="issuer"></a>발급자

`AuthnRequest`의 `Issuer` 요소는 Azure AD에서 클라우드 서비스의 **ServicePrincipalNames** 중 하나와 정확히 일치해야 합니다. 일반적으로 애플리케이션 등록 중에 지정된 **앱 ID URI** 로 설정됩니다.

`Issuer` 요소가 포함된 SAML 발췌 부분은 다음 샘플과 같습니다.

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

이 요소는 응답에서 특정 이름 ID 형식을 요청하고 Azure AD로 전송되는 `AuthnRequest` 요소에서는 선택 사항입니다.

`NameIdPolicy` 요소는 다음 샘플과 같습니다.

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

`NameIDPolicy`가 제공된 경우 선택 사항인 `Format` 특성을 포함할 수 있습니다. `Format` 특성은 다음 값 중 하나만 포함할 수 있으며 다른 값을 사용하면 오류가 발생합니다.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory는 쌍별 식별자로 NameID 클레임을 발급합니다.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory는 전자 메일 주소 형식으로 NameID 클레임을 발급합니다.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: 이 값은 클레임 형식을 선택하기 위한 Azure Active Directory를 허용합니다. Azure Active Directory는 쌍별 식별자로 NameID를 발급합니다.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory에서 NameID 클레임을 현재 SSO 작업에 고유하고 임의로 생성된 값으로 발급합니다. 따라서 임시 값이며 인증 사용자를 식별하는 데 사용할 수 없습니다.

Azure AD는 `AllowCreate` 특성을 무시합니다.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` 요소는 원하는 인증 방법을 지정합니다. Azure AD로 전송되는 `AuthnRequest` 요소에서는 선택 사항입니다. Azure AD는 `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`와 같은 `AuthnContextClassRef` 값을 지원 합니다.

### <a name="scoping"></a>범위 지정
ID 공급자 목록을 포함하는 `Scoping` 요소는 Azure AD로 전송되는 `AuthnRequest` 요소에서 선택 사항입니다.

제공되는 경우 `ProxyCount` 특성, `IDPListOption` 또는 `RequesterID` 요소는 지원되지 않으므로 포함하지 않습니다.

### <a name="signature"></a>서명
Azure AD에서 서명된 인증 요청을 지원하지 않으므로 `Signature` 요소는 `AuthnRequest` 요소에 포함하지 않습니다.

### <a name="subject"></a>제목
Azure AD는 `AuthnRequest` 요소의 `Subject` 요소를 무시합니다.

## <a name="response"></a>응답
요청한 로그온이 성공적으로 완료되면 Azure AD는 클라우드 서비스에 응답을 게시합니다. 성공적인 로그온 시도에 대한 응답은 다음 샘플과 같습니다.

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>응답

`Response` 요소는 권한 부여 요청의 결과를 포함합니다. Azure AD는 `Response` 요소에 `ID`, `Version` 및 `IssueInstant` 값을 설정합니다. 다음 특성도 설정합니다.

* `Destination`: 로그온이 성공적으로 완료되면 서비스 공급자(클라우드 서비스)의 `RedirectUri`로 설정됩니다.
* `InResponseTo`: 응답을 시작한 `AuthnRequest` 요소의 `ID` 특성으로 설정됩니다.

### <a name="issuer"></a>발급자

Azure AD는 Azure AD 테넌트의 테넌트 ID가 TenantIDGUID > \<`https://login.microsoftonline.com/<TenantIDGUID>/` `Issuer` 요소를 설정 합니다.

예를 들어 발급자 요소가 포함된 응답은 다음 샘플과 같습니다.

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>상태

`Status` 요소가 로그온의 성공 여부를 전달합니다. 여기에는 요청 상태를 나타내는 코드 또는 중첩된 코드 집합이 포함된 `StatusCode` 요소가 있습니다. 또한 로그온 프로세스 중에 생성된 사용자 지정 오류 메시지를 포함하는 `StatusMessage` 요소도 포함됩니다.

<!-- TODO: Add an authentication protocol error reference -->

실패한 로그온 시도에 대한 SAML 응답은 다음 샘플과 같습니다.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>어설션

`ID`, `IssueInstant` 및 `Version` 외에도 Azure AD는 응답의 `Assertion` 요소에서 다음 요소를 설정합니다.

#### <a name="issuer"></a>발급자

이는 `https://sts.windows.net/<TenantIDGUID>/`로 설정 됩니다. 여기서 \<TenantIDGUID >은 Azure AD 테넌트의 테넌트 ID입니다.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>서명

Azure AD는 성공적인 로그온에 대한 응답에서 어설션을 서명합니다. `Signature` 요소는 클라우드 서비스에서 소스를 인증하고 어설션의 무결성을 확인하는 데 사용할 수 있는 디지털 서명을 포함합니다.

이 디지털 서명을 생성하기 위해 Azure AD는 해당 메타데이터 문서의 `IDPSSODescriptor` 요소에 서명 키를 사용합니다.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>제목

이 어설션의 문 주체인 보안 주체를 지정합니다. 여기에는 인증된 사용자를 나타내는 `NameID` 요소가 포함됩니다. `NameID` 값은 토큰의 대상 그룹인 서비스 공급자에만 전달되는 대상 지정 식별자입니다. 영구적이며 해지할 수 있지만 다시 할당되지는 않습니다. 또한 불투명하며 사용자에 대한 어떠한 정보도 표시하지 않으며 특성 쿼리의 식별자로 사용할 수 없습니다.

`SubjectConfirmation` 요소의 `Method` 특성은 항상 `urn:oasis:names:tc:SAML:2.0:cm:bearer`로 설정됩니다.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>조건

이 요소는 SAML 어설션의 사용 제한을 정의하는 조건을 지정합니다.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` 및 `NotOnOrAfter` 특성은 어설션이 유효한 간격을 지정합니다.

* `NotBefore` 특성 값은 `Assertion` 요소의 `IssueInstant` 특성 값과 같거나 약간(1초 미만) 나중입니다. Azure AD에서는 Azure AD 자체와 클라우드 서비스(서비스 공급자) 간의 시차를 고려하지 않으며 이 시간에 어떠한 버퍼도 추가하지 않습니다.
* `NotOnOrAfter` 특성 값은 `NotBefore` 특성 값보다 70분 후입니다.

#### <a name="audience"></a>대상

대상 그룹을 식별하는 URI를 포함합니다. Azure AD는 이 요소의 값을 로그온이 시작된 `AuthnRequest`의 `Issuer` 요소 값으로 설정합니다. `Audience` 값을 평가하려면 애플리케이션 등록 중에 지정된 `App ID URI` 값을 사용합니다.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

`Issuer` 값과 마찬가지로 `Audience` 값은 Azure AD에서 클라우드 서비스를 나타내는 서비스 주체 이름 중 하나와 정확히 일치해야 합니다. 그러나 `Issuer` 요소의 값이 URI 값이 아닌 경우 응답에 있는 `Audience` 값은 `spn:` 접두사가 있는 `Issuer` 값입니다.

#### <a name="attributestatement"></a>AttributeStatement

주체 또는 사용자에 대한 클레임을 포함합니다. 다음 발췌문에는 샘플 `AttributeStatement` 요소가 포함되어 있습니다. 줄임표는 요소에 여러 특성 및 특성 값이 포함될 수 있음을 나타냅니다.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **이름 클레임** - `Name` 특성 값(`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`)은 인증된 사용자의 사용자 주체 이름입니다(예: `testuser@managedtenant.com`).
* **ObjectIdentifier 클레임** - `ObjectIdentifier` 특성 값(`http://schemas.microsoft.com/identity/claims/objectidentifier`)은 Azure AD에서 인증된 사용자를 나타내는 디렉터리 개체의 `ObjectId`입니다. `ObjectId`는 인증된 사용자에 대해 변경할 수 없고, 전역적으로 고유하며, 안전하게 다시 사용할 수 있는 식별자입니다.

#### <a name="authnstatement"></a>AuthnStatement

이 요소는 어설션 주체가 특정 시간에 특정 수단으로 인증되었음을 어설션합니다.

* `AuthnInstant` 특성은 사용자가 Azure AD를 인증한 시간을 지정합니다.
* `AuthnContext` 요소는 사용자를 인증하는 데 사용된 인증 컨텍스트를 지정합니다.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
