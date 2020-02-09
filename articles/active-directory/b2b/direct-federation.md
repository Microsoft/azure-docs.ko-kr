---
title: B2B에 대 한 id 공급자와 직접 페더레이션-Azure AD
description: 게스트가 Azure AD 앱에 로그인 할 수 있도록 SAML 또는 WS-급지됨 id 공급자와 직접 페더레이션
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 036c8361af3f6631b6151782fa18495542d2e3f6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888883"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>게스트 사용자를 위한 AD FS 및 타사 공급자와의 직접 페더레이션 (미리 보기)
|     |
| --- |
| 직접 페더레이션은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이 문서에서는 B2B 공동 작업을 위해 다른 조직과 직접 페더레이션을 설정 하는 방법을 설명 합니다. IdP (id 공급자)가 SAML 2.0 또는 WS-급지됨 프로토콜을 지 원하는 조직과 직접 페더레이션을 설정할 수 있습니다.
파트너의 IdP 직접 페더레이션을 설정 하면 해당 도메인의 새 게스트 사용자는 자신의 IdP 관리 조직 계정을 사용 하 여 Azure AD 테넌트에 로그인 하 고 사용자와 공동 작업을 시작할 수 있습니다. 게스트 사용자가 별도의 Azure AD 계정을 만들 필요가 없습니다.
> [!NOTE]
> 직접 페더레이션 게스트 사용자는 테넌트 컨텍스트를 포함 하는 링크 (예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`또는 확인 된 도메인의 경우 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`)를 사용 하 여 로그인 해야 합니다. 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 직접 페더레이션 사용자는 현재 테넌트 컨텍스트가 없는 일반 엔드포인트을 사용 하 여 로그인 할 수 없습니다. 예를 들어 `https://myapps.microsoft.com`, `https://portal.azure.com`또는 `https://teams.microsoft.com`를 사용 하면 오류가 발생 합니다.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>게스트 사용자가 직접 페더레이션을 사용 하 여 인증 된 경우는 언제 입니까?
조직과의 직접 페더레이션을 설정한 후에는 직접 페더레이션을 사용 하 여 초대 하는 모든 새 게스트 사용자가 인증 됩니다. 직접 페더레이션을 설정 해도 이미 초대를 받은 게스트 사용자에 대 한 인증 방법은 변경 되지 않습니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.
 - 게스트 사용자가 이미 사용자의 초대를 사용 하는 경우 이후에 조직과 직접 페더레이션을 설정 하면 해당 게스트 사용자는 직접 페더레이션을 설정 하기 전에 사용한 것과 동일한 인증 방법을 계속 사용 합니다.
 - 파트너 조직과의 직접 페더레이션을 설정 하 고 게스트 사용자를 초대 하는 경우 파트너 조직에서 나중에 Azure AD로 이동 하는 경우 초대를 이미 사용한 게스트 사용자는 직접 페더레이션을 계속 사용 합니다. 테넌트의 페더레이션 정책이 있습니다.
 - 파트너 조직과의 직접 페더레이션을 삭제 하는 경우 현재 직접 페더레이션을 사용 하는 모든 게스트 사용자는 로그인 할 수 없습니다.

이러한 시나리오에서는 디렉터리에서 게스트 사용자 계정을 삭제 하 고 다시 초대 하 여 게스트 사용자의 인증 방법을 업데이트할 수 있습니다.

직접 페더레이션은 도메인 네임 스페이스 (예: contoso.com 및 fabrikam.com)에 연결 됩니다. 조직에서는 AD FS 또는 타사 IdP를 사용 하 여 직접 페더레이션 구성을 설정할 때 하나 이상의 도메인 네임 스페이스를 이러한 IdPs에 연결 합니다. 

## <a name="end-user-experience"></a>최종 사용자 환경 
직접 페더레이션을 사용 하 여 게스트 사용자는 자신의 조직 계정을 사용 하 여 Azure AD 테넌트에 로그인 합니다. 공유 리소스에 액세스할 때 로그인 하 라는 메시지가 표시 되 면 직접 페더레이션 사용자는 자신의 IdP으로 리디렉션됩니다. 성공적으로 로그인 한 후에는 리소스에 액세스 하기 위해 Azure AD로 반환 됩니다. 직접 페더레이션 사용자의 새로 고침 토큰은 Azure AD의 [통과 새로 고침 토큰에 대 한 기본 길이인](../develop/active-directory-configurable-token-lifetimes.md#exceptions) 12 시간 동안 유효 합니다. 페더레이션된 IdP SSO를 사용 하도록 설정 된 경우 사용자는 SSO를 경험 하 고 초기 인증 후에 로그인 프롬프트를 표시 하지 않습니다.

## <a name="limitations"></a>제한 사항

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD의 DNS 확인 도메인
페더레이션 하려는 도메인은 Azure AD에서 DNS를 확인 ***하지*** 않아야 합니다. 관리 되지 않는 (전자 메일 확인 또는 "바 이럴") Azure AD 테넌트가 DNS에서 확인 되지 않으므로 직접 페더레이션을 설정할 수 있습니다.
### <a name="authentication-url"></a>인증 URL
직접 페더레이션은 인증 URL의 도메인이 대상 도메인과 일치 하거나 인증 URL이 허용 되는 이러한 id 공급자 중 하나인 정책에만 허용 됩니다 .이 목록은 변경 될 수 있습니다.
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

예를 들어 **fabrikam.com**에 대 한 직접 페더레이션을 설정할 때 인증 URL `https://fabrikam.com/adfs`는 유효성 검사를 통과 합니다. 예를 들어 동일한 도메인에 있는 호스트는 `https://sts.fabrikam.com/adfs`를 전달 합니다. 그러나 같은 도메인에 대 한 인증 URL `https://fabrikamconglomerate.com/adfs` 또는 `https://fabrikam.com.uk/adfs`는 전달 되지 않습니다.

### <a name="signing-certificate-renewal"></a>서명 인증서 갱신
Id 공급자 설정에서 메타 데이터 URL을 지정 하는 경우 Azure AD는 만료 될 때 서명 인증서를 자동으로 갱신 합니다. 그러나 만료 시간 이전에 어떤 이유로 든 인증서를 회전 하거나 메타 데이터 URL을 제공 하지 않으면 Azure AD에서 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트 해야 합니다.

### <a name="limit-on-federation-relationships"></a>페더레이션 관계에 대 한 제한
현재 최대 1000 페더레이션 관계가 지원 됩니다. 이 제한에는 [내부 페더레이션](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) 및 직접 페더레이션을 모두 포함 됩니다.
## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>관리 되지 않는 (전자 메일 확인) 테넌트가 존재 하는 도메인을 사용 하 여 직접 페더레이션을 설정할 수 있나요? 
예. 도메인을 확인 하지 않은 상태에서 테넌트가 [관리자 인수](../users-groups-roles/domains-admin-takeover.md)하지 않은 경우 해당 도메인을 사용 하 여 직접 페더레이션을 설정할 수 있습니다. 관리 되지 않거나 전자 메일 확인 됨은 사용자가 B2B 초대를 교환 현재 존재 하지 않는 도메인을 사용 하 여 Azure AD에 대 한 셀프 서비스 등록을 수행 하는 경우에 생성 됩니다. 이러한 도메인으로 직접 페더레이션을 설정할 수 있습니다. Azure Portal 또는 PowerShell을 통해 DNS 확인 도메인으로 직접 페더레이션을 설정 하려고 하면 오류가 표시 됩니다.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>직접 페더레이션 및 전자 메일 일회용 암호 인증을 둘 다 사용 하도록 설정 하는 경우 어떤 방법이 우선적으로 적용 되나요?
파트너 조직에 직접 페더레이션을 설정 하면 해당 조직의 새 게스트 사용자에 대 한 전자 메일 일회용 암호 인증 보다 우선 적용 됩니다. 게스트 사용자가 직접 페더레이션을 설정 하기 전에 일회용 암호 인증을 사용 하 여 초대를 사용 하는 경우에는 일회성 암호 인증을 계속 사용 합니다. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>부분적으로 동기화 된 테넌트로 인해 페더레이션 주소 로그인 문제가 발생 하나요?
아니요,이 시나리오에서는 [전자 메일 일회용 암호](one-time-passcode.md) 기능을 사용 해야 합니다. "부분적으로 동기화 된 테넌트"는 온-프레미스 사용자 id가 클라우드와 완전히 동기화 되지 않는 파트너 Azure AD 테넌트를 나타냅니다. 해당 id가 클라우드에 아직 없지만 B2B 초대를 교환 하려는 사용자는 로그인 할 수 없습니다. 일회용 암호 기능을 사용 하면이 게스트가 로그인 할 수 있습니다. 직접 페더레이션 기능은 게스트에 고유한 IdP 관리 조직 계정이 있지만 조직에 Azure AD가 전혀 존재 하지 않는 시나리오를 해결 합니다.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1 단계: 파트너 조직의 id 공급자 구성
먼저 파트너 조직에서 필요한 클레임 및 신뢰 당사자 트러스트를 사용 하 여 id 공급자를 구성 해야 합니다. 

> [!NOTE]
> 직접 페더레이션을 위해 id 공급자를 구성 하는 방법을 설명 하기 위해 Active Directory Federation Services (AD FS)를 예로 사용 합니다. 직접 페더레이션을 준비 하면서 AD FS를 SAML 2.0 또는 WS-급지됨 id 공급자로 구성 하는 방법에 대 한 예제를 제공 하는 [AD FS로 직접 페더레이션 구성](direct-federation-adfs.md)문서를 참조 하세요.

### <a name="saml-20-configuration"></a>SAML 2.0 구성

Azure AD B2B는 아래 나열 된 특정 요구 사항과 함께 SAML 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 구성할 수 있습니다. SAML id 공급자와 Azure AD 간의 트러스트를 설정 하는 방법에 대 한 자세한 내용은 [Single sign-on에 saml 2.0 Id 공급자 (IdP) 사용](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)을 참조 하세요.  

> [!NOTE]
> 직접 페더레이션을 위한 대상 도메인은 Azure AD에서 DNS를 확인 하지 않아야 합니다. 인증 URL 도메인은 대상 도메인과 일치 해야 합니다. 또는 허용 되는 id 공급자의 도메인 이어야 합니다. 자세한 내용은 [제한 사항](#limitations) 섹션을 참조 하세요. 

#### <a name="required-saml-20-attributes-and-claims"></a>필요한 SAML 2.0 특성 및 클레임
다음 표에서는 타사 id 공급자에서 구성 해야 하는 특정 특성 및 클레임에 대 한 요구 사항을 보여 줍니다. 직접 페더레이션을 설정 하려면 id 공급자의 SAML 2.0 응답에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 구성할 수 있습니다.

IdP의 SAML 2.0 응답에 필요한 특성:

|attribute  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |파트너 IdP의 발급자 URI (예: `http://www.example.com/exk10l6w90DHM0yi...`         |


IdP에서 발급 한 SAML 2.0 토큰에 필요한 클레임:

|attribute  |값  |
|---------|---------|
|NameID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-급지됨 구성 
Azure AD B2B는 아래 나열 된 몇 가지 특정 요구 사항과 함께 WS-급지됨 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 구성할 수 있습니다. 현재 두 개의 WS (Shibboleth) 공급자는 Azure AD와의 호환성을 테스트 했습니다. 여기에는 AD FS 및 있습니다. Azure AD를 사용 하 여 WS-급지 규격 공급자 간에 신뢰 당사자 트러스트를 설정 하는 방법에 대 한 자세한 내용은 [AZURE Ad Id 공급자 호환성 문서](https://www.microsoft.com/download/details.aspx?id=56843)에서 사용할 수 있는 "ws 프로토콜을 사용 하는 STS 통합 백서"를 참조 하세요.

> [!NOTE]
> 직접 페더레이션을 위한 대상 도메인은 Azure AD에서 DNS를 확인 하지 않아야 합니다. 인증 URL 도메인은 허용 된 id 공급자의 도메인 또는 대상 도메인 중 하 나와 일치 해야 합니다. 자세한 내용은 [제한 사항](#limitations) 섹션을 참조 하세요. 

#### <a name="required-ws-fed-attributes-and-claims"></a>필요한 WS-급지됨 특성 및 클레임

다음 표에서는 타사 WS 공급 id 공급자에서 구성 해야 하는 특정 특성 및 클레임에 대 한 요구 사항을 보여 줍니다. 직접 페더레이션을 설정 하려면 id 공급자의 WS-급지됨 메시지에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 구성할 수 있습니다.

IdP의 WS-급지됨 메시지에 필요한 특성:
 
|attribute  |값  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |파트너 IdP의 발급자 URI (예: `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발급 한 WS-급지됨 토큰에 필요한 클레임:

|attribute  |값  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2 단계: Azure AD에서 직접 페더레이션 구성 
다음으로, Azure AD에서 1 단계에서 구성 된 id 공급자를 사용 하 여 페더레이션을 구성 합니다. Azure AD 포털 또는 PowerShell을 사용할 수 있습니다. 직접 페더레이션 정책이 적용 되기까지 5-10 분이 걸릴 수 있습니다. 이 시간 동안 직접 페더레이션 도메인에 대 한 초대를 교환 하려고 시도 하지 마세요. 다음과 같은 특성이 필요합니다.
- 파트너 IdP의 발급자 URI
- 파트너 IdP의 수동 인증 엔드포인트 (https만 지원 됨)
- 인증서

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 직접 페더레이션을 구성 하려면

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **Id 공급자**를 선택한 다음 **새 SAML/WS-급지됨 IdP**를 선택 합니다.

    ![새 SAML 또는 WS 급지되지 IdP를 추가 하는 단추를 보여 주는 스크린샷](media/direct-federation/new-saml-wsfed-idp.png)

4. **새 saml/WS-급지됨 IdP** 페이지의 **id 공급자 프로토콜**에서 **SAML** 또는 **WS 급지됨**를 선택 합니다.

    ![SAML 또는 WS-급지됨 IdP 페이지의 구문 분석 단추를 보여 주는 스크린샷](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 파트너 조직의 도메인 이름을 입력 합니다 .이 이름은 직접 페더레이션을 위한 대상 도메인 이름일 수 있습니다.
6. 메타 데이터 파일을 업로드 하 여 메타 데이터 정보를 채울 수 있습니다. 메타 데이터를 수동으로 입력 하도록 선택 하는 경우 다음 정보를 입력 합니다.
   - 파트너 IdP의 도메인 이름
   - 파트너 IdP의 엔터티 ID
   - 파트너 IdP의 수동 요청자 엔드포인트
   - 인증서
   > [!NOTE]
   > 메타 데이터 URL은 선택 사항 이지만 권장 됩니다. 메타 데이터 URL을 제공 하는 경우 Azure AD는 만료 될 때 서명 인증서를 자동으로 갱신할 수 있습니다. 만료 시간 이전에 어떤 이유로 든 인증서를 회전 하거나 메타 데이터 URL을 제공 하지 않으면 Azure AD는 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트 해야 합니다.

7. **저장**을 선택합니다. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>PowerShell을 사용 하 여 Azure AD에서 직접 페더레이션을 구성 하려면

1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). 자세한 단계가 필요한 경우 게스트 사용자를 추가 하는 빠른 시작에는 [최신 AzureADPreview 모듈 설치](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)섹션이 포함 되어 있습니다. 
2. 다음 명령 실행: 
   ```powershell
   Connect-AzureAD
   ```
1. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다. 
2. 다음 명령을 실행 하 여 페더레이션 메타 데이터 파일에서 값을 바꿉니다. AD FS Server와 Okta의 경우 페더레이션 파일은 federationmetadata.xml입니다 (예: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`). 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3 단계: Azure AD에서 직접 페더레이션 테스트
이제 새 B2B 게스트 사용자를 초대 하 여 직접 페더레이션 설정을 테스트 합니다. 자세한 내용은 [Azure Portal에서 AZURE AD B2B 공동 작업 사용자 추가](add-users-administrator.md)를 참조 하세요.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>직접 페더레이션 관계를 편집 어떻게 할까요??

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **Id 공급자** 선택
4. **SAML/WS 공급 id 공급자**에서 공급자를 선택 합니다.
5. Id 공급자 세부 정보 창에서 값을 업데이트 합니다.
6. **저장**을 선택합니다.


## <a name="how-do-i-remove-direct-federation"></a>직접 페더레이션을 제거 어떻게 할까요??
직접 페더레이션 설치를 제거할 수 있습니다. 이렇게 하면 이미 초대를 받은 페더레이션 게스트 사용자는 로그인 할 수 없습니다. 하지만 디렉터리에서 리소스를 삭제 하 고 다시 초대 하 여 리소스에 대 한 액세스 권한을 다시 부여할 수 있습니다. Azure AD 포털에서 id 공급자를 사용 하 여 직접 페더레이션을 제거 하려면 다음을 수행 합니다.

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **Id 공급자**를 선택 합니다.
4. Id 공급자를 선택한 다음 **삭제**를 선택 합니다. 
5. **예**를 선택하여 삭제를 확인합니다. 

PowerShell을 사용 하 여 id 공급자와의 직접 페더레이션을 제거 하려면 다음을 수행 합니다.
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령 실행: 
   ```powershell
   Connect-AzureAD
   ```
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다. 
4. 다음 명령을 입력합니다.
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
