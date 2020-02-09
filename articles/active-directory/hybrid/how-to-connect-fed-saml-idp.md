---
title: 'Azure AD Connect: Single Sign-on에 SAML 2.0 Id 공급자 사용-Azure'
description: 이 문서에서는 Single Sign-On에 SAML 2.0 호환 Idp를 사용하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305108"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Single Sign-On에 SAML 2.0 IdP(ID 공급자) 사용

이 문서에서는 SAML 2.0 호환 SP-Lite 프로필 기반 ID 공급자를 기본 STS(보안 토큰 서비스)/ID 공급자로 사용하는 방법에 대한 정보를 제공합니다. 이 시나리오는 사용자 디렉터리 및 암호 저장소가 SAML 2.0을 사용하여 액세스할 수 있는 온-프레미스에 이미 있는 경우에 유용합니다. 이 기존 사용자 디렉터리는 Office 365 및 다른 Azure AD 보안 리소스에 로그온하는 데 사용할 수 있습니다. SAML 2.0 SP-Lite 프로필은 널리 사용되는 SAML(Security Assertion Markup Language ) 페더레이션 ID 표준을 기준으로 하여 로그온 및 특성 교환 프레임워크를 제공합니다.

>[!NOTE]
>테스트를 통해 Azure AD에서 사용할 수 있는 것으로 확인된 타사 Idp 목록을 보려면 [Azure AD 페더레이션 호환성 목록](how-to-connect-fed-compatibility.md)을 참조하세요.

Microsoft는 Office 365와 같은 Microsoft 클라우드 서비스를 올바르게 구성된 SAML 2.0 프로필 기반 IdP에 통합하는 방식으로 이러한 로그온 환경을 지원합니다. SAML 2.0 ID 공급자는 타사 제품이므로 Microsoft는 이와 관련된 배포, 구성, 문제 해결 모범 사례를 지원하지 않습니다. SAML 2.0 ID 공급자와의 통합이 구성된 후에는 Microsoft 연결 분석기 도구를 사용하여 구성이 적절한지 테스트할 수 있습니다. 이 도구에 대해서는 아래에 자세히 설명되어 있습니다. SAML 2.0 SP-Lite 프로필 기반 ID 공급자에 대한 자세한 내용은 해당 공급자를 제공하는 조직에 문의하세요.

> [!IMPORTANT]
> SAML 2.0 ID 공급자를 사용하는 이러한 로그온 시나리오에서는 제한된 클라이언트 집합만 사용할 수 있습니다. 여기에는 다음이 포함됩니다.
> 
> - Outlook Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트
> - 기본 인증 및 IMAP, POP, Active Sync, MAPI 등의 지원되는 Exchange 액세스 방법을 사용하는 풍부한 전자 메일 기능을 제공하는 클라이언트(향상된 클라이언트 프로토콜 엔드포인트을 배포해야 함). 여기에는 다음이 포함됩니다.
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone(다양한 iOS 버전)
>     - 다양한 Google Android 디바이스
>     - Windows Phone 7, Windows Phone 7.8 및 Windows Phone 8.0
>     - Windows 8 메일 클라이언트 및 Windows 8.1 메일 클라이언트
>     - Windows 10 메일 클라이언트

다른 모든 클라이언트는 SAML 2.0 ID 공급자를 사용하는 이러한 로그온 시나리오에서 사용할 수 없습니다. 예를 들어 Lync 2010 데스크톱 클라이언트는 Single Sign-On에 대해 구성 된 SAML 2.0 Id 공급자를 사용 하 여 서비스에 로그인 할 수 없습니다.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2.0 프로토콜 요구 사항
이 문서에는 SAML 2.0 ID 공급자가 하나 이상의 Microsoft 클라우드 서비스(예: Office 365)에 대한 로그온을 위해 Azure AD와 페더레이션할 때 구현해야 하는 프로토콜 및 메시지 서식에 대한 자세한 요구 사항이 나와 있습니다. 이 시나리오에서 사용되는 Microsoft 클라우드 서비스용 SAML 2.0 신뢰 당사자(SP-STS)는 Azure AD입니다.

SAML 2.0 ID 공급자 출력 메시지를 제공된 샘플 추적과 가능한 한 유사하게 유지하는 것이 좋습니다. 또한 가능한 경우 제공된 Azure AD 메타데이터의 특정 특성 값을 사용하도록 합니다. 출력 메시지가 적절하다고 생각되면 아래 설명된 대로 Microsoft 연결 분석기를 사용해서 테스트할 수 있습니다.

Azure AD 메타데이터는 URL [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml)에서 다운로드할 수 있습니다.
Office 365의 중국 특정 인스턴스를 사용하는 중국 고객의 경우 다음 페더레이션 엔드포인트를 사용해야 합니다. [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml)

## <a name="saml-protocol-requirements"></a>SAML 프로토콜 요구 사항
이 섹션에서는 메시지 서식을 올바르게 지정하는 데 도움을 주기 위해 요청 및 응답 메시지 쌍을 함께 처리하는 방법을 자세히 설명합니다.

Azure AD는 아래와 같은 몇 가지 특정 요구 사항에 따라 SAML 2.0 SP Lite 프로필을 사용하는 ID 공급자에 작동하도록 구성할 수 있습니다. 자동 및 수동 테스트에서 샘플 SAML 요청 및 응답 메시지를 사용하면 Azure AD와의 상호 운용성을 달성하는 데 도움이 될 수 있습니다.

## <a name="signature-block-requirements"></a>서명 블록 요구 사항
SAML 응답 메시지 내에서 서명 노드에는 메시지 자체에 대한 디지털 서명 정보가 포함됩니다. 서명 블록에는 다음 요구 사항이 적용됩니다.

1. 어설션 노드 자체에 서명해야 합니다.
2.  RSA-sha1 알고리즘을 DigestMethod로 사용해야 합니다. 기타 디지털 서명 알고리즘은 허용되지 않습니다.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  XML 문서에 서명을 할 수도 있습니다. 
4.  Transform 알고리즘은 다음 샘플의 값과 일치해야 합니다. `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  SignatureMethod 알고리즘은 다음 샘플과 일치해야 합니다. `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>지원되는 바인딩
바인딩은 필요한 전송 관련 통신 매개 변수입니다. 바인딩에는 다음 요구 사항이 적용됩니다.

1. HTTPS는 필수 전송입니다.
2.  Azure AD는 로그인 중에 토큰을 제출 하기 위해 HTTP POST를 요구 합니다.
3.  Azure AD는 id 공급자에 대 한 인증 요청에 HTTP POST를 사용 하 여 id 공급자에 대 한 로그 아웃 메시지를 리디렉션합니다.

## <a name="required-attributes"></a>필수 특성
다음 표에는 SAML 2.0 메시지의 특정 특성에 대한 요구 사항을 표시됩니다.
 
|특성|설명|
| ----- | ----- |
|NameID|이 어설션 값은 Azure AD 사용자의 ImmutableID와 같아야 합니다. 최대 64자의 영숫자일 수 있습니다. html이 아닌 모든 보안 문자를 인코딩해야 합니다. 예를 들어 "+" 문자는 ".2B"로 표시됩니다.|
|IDPEmail|UPN(사용자 계정 이름)은 SAML 응답에 이름이 IDPEmail인 요소로 표시됩니다. 이 이름은 Azure AD/Office 365에서 UPN(사용자 계정 이름)입니다. UPN은 전자 메일 주소 형식입니다. Windows Office 365(Azure Active Directory)의 UPN 값입니다.|
|발급자|ID 공급자의 URI여야 합니다. 샘플 메시지의 발급자를 다시 사용하지 마십시오. Azure AD 테넌트에 여러 개의 최상위 도메인이 있는 경우 발급자는 도메인별로 구성된 지정한 URI 설정과 일치해야 합니다.|

>[!IMPORTANT]
>현재 Azure AD는 SAML 2.0에 대해 NameID 형식 URI urn:oasis:names:tc:SAML:2.0:nameid-format:persistent를 지원합니다.

## <a name="sample-saml-request-and-response-messages"></a>샘플 SAML 요청 및 응답 메시지
요청 및 응답 메시지 쌍은 로그온 메시지 교환에 대해 표시됩니다.
다음은 Azure AD에서 샘플 SAML 2.0 ID 공급자로 전송되는 샘플 요청 메시지입니다. 샘플 SAML 2.0 ID 공급자는 SAML-P 프로토콜을 사용하도록 구성된 AD FS(Active Directory Federation Services)입니다. 다른 SAML 2.0 ID 공급자와의 상호 운용성 테스트도 완료되었습니다.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

다음은 샘플 SAML 2.0 호환 ID 공급자로부터 Azure AD/Office 365로 전송되는 샘플 응답 메시지입니다.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>SAML 2.0 호환 ID 공급자 구성
이 섹션에는 SAML 2.0 프로토콜을 사용하여 하나 이상의 Microsoft 클라우드 서비스(예: Office 365)에 대한 Single Sign-On 액세스를 허용하기 위해 Azure AD와 페더레이션되도록 SAML 2.0 ID 공급자를 구성하는 방법에 대한 지침이 포함되어 있습니다. 이 시나리오에서 사용되는 Microsoft 클라우드 서비스용 SAML 2.0 신뢰 당사자는 Azure AD입니다.

## <a name="add-azure-ad-metadata"></a>Azure AD 메타데이터 추가
SAML 2.0 ID 공급자는 Azure AD 신뢰 당사자에 대한 정보를 준수해야 합니다. Azure AD는 https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 에 메타데이터를 게시합니다.

SAML 2.0 ID 공급자를 구성할 때 항상 최신 Azure AD 메타데이터를 가져오는 것이 좋습니다.

>[!NOTE]
>Azure AD는 ID 공급자에서 메타데이터를 읽지 않습니다.

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD를 신뢰 당사자로 추가
SAML 2.0 ID 공급자와 Azure AD 간의 통신을 사용하도록 설정해야 합니다. 이 구성은 특정 ID 공급자에 종속됩니다. 해당 설명서를 참조하세요. 일반적으로 신뢰 당사자 ID를 Azure AD 메타데이터의 entityID와 동일하게 설정합니다.

>[!NOTE]
>SAML 2.0 ID 공급자 서버의 시계가 정확한 시간 원본과 동기화되는지 확인합니다. 시계 시간이 부정확하면 페더레이션 로그인이 실패하게 됩니다.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>SAML 2.0 ID 공급자로 로그온하기 위해 Windows PowerShell 설치
Azure AD 로그온에 사용하기 위해 SAML 2.0 ID 공급자를 구성하고 다음에 수행할 단계는 Windows PowerShell용 Azure Active Directory 모듈을 다운로드하고 설치하는 것입니다. 일단 설치되면 이러한 cmdlet을 사용하여 Azure AD 도메인을 페더레이션된 도메인으로 구성합니다.

Windows PowerShell용 Azure Active Directory 모듈은 Azure AD에서 조직 데이터를 관리하기 위한 다운로드입니다. 이 모듈은 Windows PowerShell에 cmdlet 집합을 설치합니다. 이러한 cmdlet을 사용하여 Azure AD 및 구독된 모든 클라우드 서비스에 대한 Single Sign-On 액세스를 설정합니다. cmdlet을 다운로드하고 설치하는 방법에 대한 지침을 [https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)를 참조하세요.

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>SAML ID 공급자 및 Azure AD 간에 트러스트 설정
Azure AD 도메인에서 페더레이션을 구성하기 전에 사용자 지정 도메인을 먼저 구성해야 합니다. Microsoft에서 제공하는 기본 도메인은 페더레이션할 수 없습니다. Microsoft의 기본 도메인은 "onmicrosoft.com"으로 끝납니다.
Windows PowerShell 명령줄 인터페이스에서 일련의 cmdlet을 실행하여 Single Sign-On에 대한 도메인을 추가하거나 변환합니다.

SAML 2.0 ID 공급자를 사용하여 페더레이션하려는 각 Azure Active Directory 도메인을 Single Sign-On으로 추가하거나 표준 도메인에서 Single Sign-On 도메인으로 변환해야 합니다. 도메인을 추가하거나 변환하면 SAML 2.0 ID 공급자와 Azure AD 간에 트러스트가 설정됩니다.

다음 절차에서는 SAML 2.0 SP-Lite를 사용하여 기존 표준 도메인을 페더레이션된 도메인으로 변환하는 과정을 안내합니다. 

>[!NOTE]
>도메인에서는 이 단계를 수행한 후 최대 2시간 정도 작동이 중단되어 사용자에게 영향을 줄 수 있습니다.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Azure AD 디렉터리에서 페더레이션에 대한 도메인 구성


1. Azure AD 디렉터리에 테넌트 관리자 권한으로 연결: Connect-MsolService.
2.  원하는 Office 365 도메인이 SAML 2.0에서 페더레이션을 사용하도록 구성: `$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  IDP 메타데이터 파일에서 서명 인증서 base64 인코딩 문자열을 얻을 수 있습니다. 이 위치의 예가 제공되어 있으나 구현에 따라 약간 다를 수 있습니다.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

“Set-MsolDomainAuthentication”에 대한 자세한 내용은 [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx)를 참조하세요.

>[!NOTE]
>Id 공급자에 대해 `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` ECP 확장을 설정한 경우에만 사용을 실행 해야 합니다. OWA(Outlook Web Application)를 제외한 Exchange Online 클라이언트는 POST 기반 활성 엔드포인트에 의존합니다. SAML 2.0 STS가 Shibboleth의 ECP 활성 엔드포인트 구현과 비슷한 활성 엔드포인트을 구현하는 경우 이러한 리치 클라이언트가 Exchange Online 서비스와 상호 작용할 수 있습니다.

페더레이션이 구성되면 "비페더레이션"(또는 "관리")으로 다시 전환할 수 있지만 이 변경을 완료하려면 최대 2시간이 걸릴 수 있으며 클라우드 기반 로그인을 위한 새 임의 암호를 각 사용자에게 배포해야 합니다. 일부 시나리오에서는 설정의 오류를 복구하기 위해 "관리"로 다시 전환해야 할 수 있습니다. 도메인 변환에 대한 자세한 내용은 [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)를 참조하세요.

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Azure AD/Office 365로 사용자 계정 프로비전
Office 365에서 사용자를 인증하려면 먼저 SAML 2.0 클레임의 어설션에 해당하는 사용자 계정으로 Azure AD를 프로비전해야 합니다. 이러한 사용자 계정이 사전에 Azure AD에 알려져 있지 않으면 페더레이션 로그인에 사용할 수 없습니다. Azure AD Connect 또는 Windows PowerShell을 사용하여 사용자 계정을 프로비전할 수 있습니다.

Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure AD 디렉터리의 도메인으로 계정을 프로비전할 수 있습니다. 자세한 내용은 [Azure Active Directory에 온-프레미스 디렉터리 통합](whatis-hybrid-identity.md)을 참조하세요.

Azure AD에 새 사용자 추가를 자동화하고 온-프레미스 디렉터리의 변경 내용을 동기화하는 데 Windows PowerShell은 사용할 수도 있습니다. Windows PowerShell cmdlet을 사용하려면 [Azure Active Directory 모듈](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0)을 다운로드해야 합니다.

이 절차에서는 Azure AD에 단일 사용자를 추가하는 방법을 보여 줍니다.


1. Azure AD 디렉터리에 테넌트 관리자 권한으로 연결: Connect-MsolService.
2.  새 사용자 계정 만들기:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

“New-MsolUser” 체크 아웃에 대한 자세한 내용은 [https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)을 참조하세요.

>[!NOTE]
>"UserPrinciplName" 값은 SAML 2.0 클레임의 "IDPEmail"에 대해 전송하는 값과 일치해야 하고 "ImmutableID" 값은 "NameID" 어설션에 전송된 값과 일치해야 합니다.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>SAML 2.0 IDP를 사용하여 Single Sign-On 확인
관리자는 Single Sign-On(ID 페더레이션이라고도 함)을 확인 및 관리하기 전에 해당 정보를 검토하고 다음 문서의 단계를 수행하여 SAML 2.0 SP-Lite 기반 ID 공급자로 Single Sign-On을 설정해야 합니다.


1.  Azure AD SAML 2.0 프로토콜 요구 사항을 검토해야 합니다.
2.  SAML 2.0 ID 공급자를 구성해야 합니다.
3.  SAML 2.0 ID 공급자를 사용한 Single Sign-On을 위해 Windows PowerShell 설치해야 합니다.
4.  SAML 2.0 ID 공급자 및 Azure AD 간에 트러스트를 설정해야 합니다.
5.  Windows PowerShell 또는 Azure AD Connect를 통해 Azure Active Directory(Office 365)에 알려진 테스트 사용자 계정을 프로비전해야 합니다.
6.  [Azure AD Connect](whatis-hybrid-identity.md)를 사용하여 디렉터리 동기화를 구성해야 합니다.

SAML 2.0 SP-Lite 기반 ID 공급자를 사용하여 Single Sign-On을 설정한 후 올바르게 작동하는지 확인해야 합니다.

>[!NOTE]
>도메인을 추가하지 않고 변환한 경우 Single Sign-On을 설정하는 데 최대 24시간이 걸릴 수 있습니다.
Single Sign-On을 확인하기 전에 Active Directory 동기화 설정을 완료하고, 디렉터리를 동기화하고, 동기화된 사용자를 활성화해야 있습니다.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>도구를 사용하여 Single Sign-On이 올바르게 설정되어 있는지 확인
Single Sign-On이 올바르게 설정되어 있는지 확인하려면 다음 절차를 수행하여 회사 자격 증명으로 클라우드 서비스에 로그인할 수 있는지 확인할 수 있습니다.

Microsoft는 SAML 2.0 기반된 ID 공급자를 테스트하는 데 사용할 수 있는 도구를 제공합니다. 이 테스트 도구를 실행하기 전에 ID 공급자와 페더레이션하도록 Azure AD 테넌트를 구성했어야 합니다.

>[!NOTE]
>연결 분석기에는 Internet Explorer 10 이상이 필요합니다.



1. 연결 분석기를 [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)에서 다운로드합니다.
2.  지금 설치를 클릭하여 이 도구를 다운로드하여 설치하기 시작합니다.
3.  "Office 365, Azure 또는 Azure Active Directory를 사용하는 다른 서비스와 페더레이션을 설정할 수 없습니다."를 선택합니다.
4.  이 도구가 다운로드되고 실행되면 연결 진단 창이 표시됩니다. 이 도구는 페더레이션 연결을 테스트하는 과정을 안내합니다.
5.  연결 분석기가 로그인 할 수 있도록 SAML 2.0 IDP을 열고 테스트 중인 사용자 계정에 대 한 자격 증명을 입력 합니다. ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  페더레이션 테스트 로그인 창에서 SAML 2.0 ID 공급자와 페더레이션되도록 구성된 Azure AD 테넌트에 대한 계정 이름 및 암호를 입력해야 합니다. 이 도구는 해당 자격 증명을 사용하여 로그인을 시도하고 로그인 기도 중에 수행된 자세한 테스트 결과를 출력으로 제공합니다.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. 이 창에는 실패한 테스트 결과가 표시됩니다. 자세한 결과 검토를 클릭하면 수행한 각 테스트 결과에 대한 정보가 표시됩니다. 또한 결과를 공유하기 위해 디스크를 저장할 수도 있습니다.
 
>[!NOTE]
>또한 연결 분석기는 WS* 기반 및 ECP/PAOS 프로토콜을 사용하여 활성 페더레이션을 테스트합니다. 이러한 기능을 사용하지 않는 경우 다음 오류를 무시해도 됩니다. ID 공급자의 활성 페더레이션 엔드포인트를 사용하여 활성 로그인 흐름 테스트

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Single Sign-On이 올바르게 설정되어 있는지 수동으로 확인
수동 확인은 SAML 2.0 ID 공급자가 많은 시나리오에서 제대로 작동하고 있는지 확인하기 위해 수행할 수 있는 추가 단계를 제공합니다.
Single Sign-On이 올바르게 설정되어 있는지 확인하려면 다음 단계를 완료합니다.


1. 도메인에 가입 된 컴퓨터에서 회사 자격 증명에 사용 하는 것과 동일한 로그인 이름을 사용 하 여 클라우드 서비스에 로그인 합니다.
2.  암호 상자 내부를 클릭합니다. Single Sign-On이 설정되면 암호 상자가 음영 처리되며 "이제 &lt;회사&gt;에 로그인해야 합니다"라는 메시지가 표시됩니다.
3.  &lt;회사&gt; 링크에서 로그인을 클릭합니다. 로그인할 수 있으면 Single Sign-On이 설정된 것입니다.

## <a name="next-steps"></a>다음 단계


- [Azure AD Connect를 사용하여 Active Directory Federation Services 관리 및 사용자 지정](how-to-connect-fed-management.md)
- [Azure AD 페더레이션 호환성 목록](how-to-connect-fed-compatibility.md)
- [Azure AD Connect 사용자 지정 설치](how-to-connect-install-custom.md)
