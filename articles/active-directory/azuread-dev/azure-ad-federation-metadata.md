---
title: Azure AD 페더레이션 메타데이터 | Microsoft Docs
description: 이 문서에서는 Azure Active Directory가 Azure Active Directory 토큰을 수락하는 서비스에 대해 게시하는 페더레이션 메타데이터 문서를 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: ff034da1f2f40ad0162e5b9fad477d066bc4c3e7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165099"
---
# <a name="federation-metadata"></a>페더레이션 메타데이터

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory(Azure AD)는 Azure AD가 발급하는 보안 토큰을 수락하도록 구성된 서비스에 대한 페더레이션 메타데이터 문서를 게시합니다. 페더레이션 메타데이터 문서 형식은 [Web Services Federation Language(WS-Federation) 버전 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)에서 설명하며, [OASIS SAML(Security Assertion Markup Language) v2.0의 메타데이터](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)를 확장합니다.

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>테넌트별 및 테넌트에 독립적인 메타데이터 엔드포인트
Azure AD 테넌트별 및 테넌트 독립적 엔드포인트를 게시합니다.

테넌트별 엔드포인트는 특정 테넌트에 대해 설계되었습니다. 테넌트별 페더레이션 메타데이터에는 테넌트별 발급자 및 엔드포인트 정보를 포함하는 테넌트에 관한 정보가 포함됩니다. 테넌트별 엔드포인트를 사용하는 단일 테넌트에 대한 액세스를 제한하는 애플리케이션입니다.

테넌트 독립적 엔드포인트는 모든 Azure AD 테넌트에 공통된 정보를 제공합니다. 이 정보는 *login.microsoftonline.com* 에서 호스트되는 테넌트에 적용되며 테넌트 간에 공유됩니다. 테넌트 독립적 엔드포인트는 특정 테넌트와 연결되어있지 않으므로 다중 테넌트 애플리케이션에 좋습니다.

## <a name="federation-metadata-endpoints"></a>페더레이션 메타데이터 엔드포인트
Azure AD는 페더레이션 메타데이터를 `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`에 게시합니다.

**테넌트별 엔드포인트**의 경우, `TenantDomainName`은(는) 다음 유형 중 하나가 될 수 있습니다.

* Azure AD 테넌트의 등록된 도메인 이름, 예: `contoso.onmicrosoft.com`.
* 도메인의 변경할 수 없는 ID, 예: `72f988bf-86f1-41af-91ab-2d7cd011db45`.

**테넌트에 독립적인 엔드포인트**의 경우 `TenantDomainName`은(는) `common`입니다. 이 문서는 login.microsoftonline.com에서 호스트되는 모든 Azure AD 테넌트에 공통된 페더레이션 메타데이터 요소만을 나열합니다.

예를 들어, 테넌트별 엔드포인트는 `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`일 수 있습니다. 테넌트 독립적 엔드포인트는 [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)입니다. 브라우저에서 이 URL을 입력하여 페더레이션 메타데이터 문서를 볼 수 있습니다.

## <a name="contents-of-federation-metadata"></a>페더레이션 메타데이터의 내용
다음 섹션에는 Azure AD에서 발급한 토큰을 사용하는 서비스에 필요한 정보를 제공합니다.

### <a name="entity-id"></a>엔터티 ID
`EntityDescriptor` 요소는 `EntityID` 특성을 포함하고 있습니다. `EntityID` 특성의 값은 발급자, 즉, 토큰을 발행한 보안 토큰 서비스(STS)를 나타냅니다. 토큰을 받을 때 발급자 유효성을 검사해야 합니다.

다음 메타데이터는 `EntityDescriptor` 요소가 있는 샘플 테넌트별 `EntityID` 요소를 보여줍니다.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
테넌트 독립적 엔드포인트의 테넌트 ID를 사용자의 테넌트 ID로 바꾸어 테넌트별 `EntityID` 값을 만들 수 있습니다. 결과 값은 토큰 발급자와 동일합니다. 이 전략을 통해 다중 테넌트 애플리케이션이 지정된 테넌트에 대한 발급자의 유효성을 검사할 수 있습니다.

다음 메타데이터는 샘플 테넌트 독립적 `EntityID` 요소를 보여줍니다. `{tenant}` 는 자리 표시자가 아니라 문자 그대로의 의미를 나타냅니다.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>토큰 서명 인증서
서비스에서 Azure AD 테 넌 트에서 발급 한 토큰을 받으면 페더레이션 메타 데이터 문서에 게시 된 서명 키를 사용 하 여 토큰의 서명 유효성을 검사 해야 합니다. 페더레이션 메타데이터는 테넌트를 토큰 서명에 사용하는 인증서의 공개 부분을 포함합니다. 인증서 원시 바이트는 `KeyDescriptor` 요소에 표시됩니다. 토큰 서명 인증서는 `use` 특성의 값이 `signing`인 경우의 서명에만 유효합니다.

Azure AD를 통해 게시된 페더레이션 메타데이터 문서에는 Azure AD가 서명 인증서 업데이트를 준비하는 경우와 같은 여러 서명 키가 있을 수 있습니다. 페더레이션 메타데이터 문서가 둘 이상의 인증서를 포함하는 경우, 토큰의 유효성을 검사하는 서비스는 문서에서 모든 인증서를 지원해야 합니다.

다음 메타데이터는 서명 키가 있는 샘플 `KeyDescriptor` 요소를 보여줍니다.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` 요소는 페더레이션 메타데이터 문서에서 WS-Federation 관련 섹션 및 SAML 관련 섹션의 두 곳에 표시됩니다. 두 섹션 모두에서 게시된 인증서는 동일해야 합니다.

WS-Federation 관련 섹션에서 WS-Federation 메타데이터 판독기는 `RoleDescriptor` 형식을 가진 `SecurityTokenServiceType` 요소에서 인증서를 읽습니다.

다음 메타데이터는 샘플 `RoleDescriptor` 요소를 보여줍니다.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

SAML 관련 섹션에서 WS-Federation 메타데이터 판독기는 `IDPSSODescriptor` 요소에서 인증서를 읽습니다.

다음 메타데이터는 샘플 `IDPSSODescriptor` 요소를 보여줍니다.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
테넌트 관련 및 테넌트 독립적 인증서의 형식에는 차이가 없습니다.

### <a name="ws-federation-endpoint-url"></a>WS-Federation 엔드포인트 URL
페더레이션 메타데이터는 WS-Federation 프로토콜에서 단일 로그인 및 단일 로그 아웃에 Azure AD가 사용하는 URL을 포함합니다. 이 엔드포인트가 `PassiveRequestorEndpoint` 요소에 나타납니다.

다음 메타데이터는 테넌트별 엔드포인트에 대한 샘플 `PassiveRequestorEndpoint` 요소를 보여줍니다.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
테넌트 독립적 엔드포인트의 경우, 다음 예제와 같이 WS-Federation URL은 WS-Federation 엔드포인트에 나타납니다.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML 프로토콜 엔드포인트 URL
페더레이션 메타데이터는 SAML 2.0 프로토콜에서 단일 로그인 및 단일 로그아웃에 Azure AD가 사용하는 URL을 포함합니다. 이 엔드포인트는 `IDPSSODescriptor` 요소에 나타납니다.

로그인 및 로그아웃 URL은 `SingleSignOnService` 및 `SingleLogoutService` 요소에 나타납니다.

다음 메타데이터는 테넌트별 엔드포인트에 대한 샘플 `PassiveResistorEndpoint` 를 보여줍니다.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

마찬가지로 다음 샘플과 같이 일반 SAML 2.0 프로토콜 엔드포인트에 대한 엔드포인트는 테넌트 독립적 페더레이션 메타데이터에 게시됩니다.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
