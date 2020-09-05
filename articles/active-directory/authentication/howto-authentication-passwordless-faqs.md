---
title: Hybrid FIDO2 보안 키 배포에 대 한 Faq-Azure Active Directory
description: Azure Active Directory (미리 보기)를 사용 하 여 암호 없는 하이브리드 FIDO2 보안 키 로그인에 대 한 몇 가지 자주 묻는 질문에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16e232cedb13dc246bf7a568adfad401c1fe3eb8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236656"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>Azure AD의 하이브리드 FIDO2 보안 키에 대 한 배포 Faq (질문과 대답) (미리 보기)

이 문서에서는 하이브리드 Azure AD 조인 장치에 대 한 배포 faq (질문과 대답) 및 온-프레미스 리소스에 대 한 암호 없는 로그인에 대해 설명 합니다. 이 암호 없는 기능을 사용 하면 FIDO2 보안 키를 사용 하 여 하이브리드 azure ad 조인 장치에 대해 Windows 10 장치에서 Azure ad 인증을 사용 하도록 설정할 수 있습니다. 사용자는 FIDO2 키와 같은 최신 자격 증명을 사용 하 여 장치에서 Windows에 로그인 하 고 온-프레미스 리소스에 대해 원활한 Single Sign-On (SSO) 환경을 사용 하 여 기존 Active Directory Domain Services (AD DS) 기반 리소스에 액세스할 수 있습니다.

하이브리드 환경의 사용자에 대 한 다음 시나리오가 지원 됩니다.

* FIDO2 보안 키를 사용 하 여 하이브리드 Azure AD 조인 장치에 로그인 하 고 온-프레미스 리소스에 대 한 SSO 액세스를 가져옵니다.
* FIDO2 보안 키를 사용 하 여 Azure AD 조인 장치에 로그인 하 고 온-프레미스 리소스에 대 한 SSO 액세스를 가져옵니다.

FIDO2 보안 키 및 온-프레미스 리소스에 대 한 하이브리드 액세스를 시작 하려면 다음 문서를 참조 하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="security-keys"></a>보안 키

* [조직에서 리소스에 액세스 하려면 2 단계 인증이 필요 합니다. 이 요구 사항을 지원 하기 위해 수행할 수 있는 작업은 무엇 인가요?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [호환 되는 FIDO2 보안 키를 어디에서 찾을 수 있나요?](#where-can-i-find-compliant-fido2-security-keys)
* [보안 키를 분실 한 경우 어떻게 해야 하나요?](#what-if-i-lose-my-security-key)
* [FIDO2 보안 키에서 데이터를 보호 하는 방법은 무엇입니까?](#how-is-the-data-protected-on-the-fido2-security-key)
* [FIDO2 보안 키 등록은 어떻게 작동 하나요?](#how-does-the-registering-of-fido2-security-keys-work)
* [관리자가 사용자에 대 한 키를 직접 프로 비전 할 방법이 있나요?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>조직에서 리소스에 액세스 하려면 다단계 인증이 필요 합니다. 이 요구 사항을 지원 하기 위해 수행할 수 있는 작업은 무엇 인가요?

FIDO2 보안 키는 다양 한 폼 팩터를 제공 합니다. 관심 있는 장치 제조업체에 문의 하 여 PIN 또는 생체 인식으로 두 번째 요소로 장치를 사용 하도록 설정 하는 방법을 설명 합니다. 지원 되는 공급자 목록은 [FIDO2 보안 키 공급자](concept-authentication-passwordless.md#fido2-security-key-providers)를 참조 하세요.

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>호환 되는 FIDO2 보안 키를 어디에서 찾을 수 있나요?

지원 되는 공급자 목록은 [FIDO2 보안 키 공급자](concept-authentication-passwordless.md#fido2-security-key-providers)를 참조 하세요.

### <a name="what-if-i-lose-my-security-key"></a>보안 키가 손실 되 면 어떻게 되나요?

**보안 정보** 페이지로 이동 하 고 FIDO2 보안 키를 제거 하 여 Azure Portal에서 키를 제거할 수 있습니다.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>FIDO2 보안 키에서 데이터를 보호 하는 방법은 무엇입니까?

FIDO2 보안 키에는 저장 된 개인 키를 보호 하는 보안 enclaves 있습니다. FIDO2 보안 키에는 개인 키를 추출할 수 없는 Windows Hello와 같이 기본 제공 되는 공세 속성도 있습니다.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>FIDO2 보안 키 등록은 어떻게 작동 하나요?

FIDO2 보안 키를 등록 하 고 사용 하는 방법에 대 한 자세한 내용은 [암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md)을 참조 하세요.

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>관리자가 사용자에 대 한 키를 직접 프로 비전 할 방법이 있나요?

아니요, 현재는 그렇지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [인터넷에 연결 되지 않은 경우이 기능이 작동 하나요?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Azure AD에서 열려 있어야 하는 특정 끝점은 무엇 인가요?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Windows 10 장치에 대 한 도메인 가입 유형 (Azure AD 조인 또는 하이브리드 Azure AD 조인)을 식별 어떻게 할까요??](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [패치를 적용 해야 하는 Dc 수에 대 한 권장 사항은 무엇 인가요?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [온-프레미스 전용 장치에 FIDO2 자격 증명 공급자를 배포할 수 있나요?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [도메인 관리자 또는 기타 높은 권한 계정에 대해 FIDO2 보안 키 로그인이 작동 하지 않습니다. 굳이?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>인터넷에 연결 되지 않은 경우이 기능이 작동 하나요?

인터넷 연결은이 기능을 사용 하기 위한 필수 구성 요소입니다. 사용자가 FIDO2 보안 키를 사용 하 여 처음 로그인 할 때 인터넷에 연결 되어 있어야 합니다. 후속 로그인 이벤트의 경우 캐시 된 로그인이 작동 하 고 사용자가 인터넷 연결 없이 인증할 수 있습니다.

일관 된 환경을 위해 장치가 인터넷에 연결 되어 있고 Dc에 대 한 시야를 사용 하는지 확인 합니다.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Azure AD에서 열려 있어야 하는 특정 끝점은 무엇 인가요?

등록 및 인증에는 다음 끝점이 필요 합니다.

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

Microsoft 온라인 제품을 사용 하는 데 필요한 전체 끝점 목록은 [Office 365 url 및 IP 주소 범위](/microsoft-365/enterprise/urls-and-ip-address-ranges)를 참조 하세요.

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Windows 10 장치에 대 한 도메인 가입 유형 (Azure AD 조인 또는 하이브리드 Azure AD 조인)을 식별 어떻게 할까요??

Windows 10 클라이언트 장치에 올바른 도메인 가입 유형이 있는지 확인 하려면 다음 명령을 사용 합니다.

```console
Dsregcmd/status
```

다음 샘플 출력은 *AzureADJoined* 가 *YES*로 설정 된 것 처럼 장치가 Azure AD에 가입 된 것을 보여 줍니다.

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

다음 샘플 출력은 *Domainedjoined* 도 *예*로 설정 되어 있으므로 장치가 하이브리드 Azure AD에 가입 되어 있음을 보여 줍니다. *DomainName* 도 표시 됩니다.

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Windows Server 2016 또는 2019 도메인 컨트롤러에서 다음 패치가 적용 되었는지 확인 합니다. 필요한 경우 Windows 업데이트를 실행 하 여 설치 합니다.

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

클라이언트 장치에서 다음 명령을 실행 하 여 패치가 설치 된 적절 한 도메인 컨트롤러에 대 한 연결을 확인 합니다.

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>패치를 적용 해야 하는 Dc 수에 대 한 권장 사항은 무엇 인가요?

조직의 인증 요청 부하를 처리할 수 있도록 패치를 사용 하 여 대부분의 Windows Server 2016 또는 2019 도메인 컨트롤러를 패치 하는 것이 좋습니다.

Windows Server 2016 또는 2019 도메인 컨트롤러에서 다음 패치가 적용 되었는지 확인 합니다. 필요한 경우 Windows 업데이트를 실행 하 여 설치 합니다.

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>온-프레미스 전용 장치에 FIDO2 자격 증명 공급자를 배포할 수 있나요?

아니요,이 기능은 온-프레미스 전용 장치에 대해서는 지원 되지 않습니다. FIDO2 자격 증명 공급자가 표시 되지 않습니다.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>도메인 관리자 또는 기타 높은 권한 계정에 대해 FIDO2 보안 키 로그인이 작동 하지 않습니다. 그 이유는 무엇일까요?

기본 보안 정책은 온-프레미스 리소스에 대 한 높은 권한 계정에 로그인 할 수 있는 권한을 Azure AD에 부여 하지 않습니다.

계정을 차단 해제 하려면 **Active Directory 사용자 및 컴퓨터** 를 사용 하 여 *Azure AD Kerberos 컴퓨터 개체 (CN = AzureADKerberos, OU = 도메인 컨트롤러 \<domain-DN> )* 의 *의 msds-primary-computer-NeverRevealGroup* 속성을 수정 합니다.

## <a name="under-the-hood"></a>기본적인 이해

* [Azure AD Kerberos를 온-프레미스 Active Directory Domain Services 환경에 연결 하는 방법](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [AD에서 만들어지고 Azure AD에 게시 된 이러한 Kerberos 서버 개체를 어디에서 볼 수 있나요?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [공개 키를 온-프레미스 AD DS에 등록 하지 않아 인터넷에 종속 되지 않는 이유는 무엇 인가요?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Kerberos 서버 개체에서 키를 회전 하는 방법은 무엇입니까?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Azure AD Connect 필요한 이유는 무엇 인가요? Azure AD에서 AD DS 정보를 다시 작성 하나요?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [PRT + 부분 TGT를 요청할 때 HTTP 요청/응답의 모양은 무엇입니까?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Azure AD Kerberos를 온-프레미스 Active Directory Domain Services 환경에 연결 하는 방법

온-프레미스 AD DS 환경과 Azure AD 테 넌 트의 두 부분으로 구성 됩니다.

**AD DS(Active Directory Domain Services)**

Azure AD Kerberos 서버는 온-프레미스 AD DS 환경에서 DC (도메인 컨트롤러) 개체로 표현 됩니다. 이 DC 개체는 여러 개체로 구성 됩니다.

* *CN = AzureADKerberos, OU = 도메인 컨트롤러\<domain-DN>*
    
    AD DS의 RODC (읽기 전용 도메인 컨트롤러)를 나타내는 *컴퓨터* 개체입니다. 이 개체와 연결 된 컴퓨터가 없습니다. 대신 DC의 논리적 표현입니다.

* *CN = krbtgt_AzureAD, CN = Users,\<domain-DN>*

    RODC Kerberos TGT (허용 티켓) 암호화 키를 나타내는 *사용자* 개체입니다.

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Azure AD Kerberos 서버 개체에 대 한 메타 데이터를 저장 하는 *ServiceConnectionPoint* 개체입니다. 관리 도구는이 개체를 사용 하 여 Azure AD Kerberos 서버 개체를 식별 하 고 찾습니다.

**Azure Active Directory**

Azure AD Kerberos 서버는 *KerberosDomain* 개체로 서 azure ad에 표시 됩니다. 각 온-프레미스 AD DS 환경은 Azure AD 테 넌 트에서 단일 *KerberosDomain* 개체로 표현 됩니다.

예를 들어 *contoso.com* 및 *fabrikam.com*과 같은 두 개의 도메인이 있는 AD DS 포리스트가 있을 수 있습니다. Azure AD에서 전체 포리스트에 대해 Kerberos Tgt (허용 티켓)를 발급 하도록 허용 하는 경우 Azure AD에는 두 개의 *KerberosDomain* 개체, *contoso.com* 에는 하나의 개체, *fabrikam.com*에는 1 개의 개체가 있습니다.

AD DS 포리스트가 여러 개인 경우 각 포리스트의 각 도메인에 대해 하나의 *KerberosDomain* 개체가 있습니다.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>AD DS에서 만들어지고 Azure AD에 게시 된 이러한 Kerberos 서버 개체를 어디에서 볼 수 있나요?

모든 개체를 보려면 최신 버전의 Azure AD Connect에 포함 된 Azure AD Kerberos 서버 PowerShell cmdlet을 사용 합니다.

개체를 보는 방법에 대 한 지침을 비롯 한 자세한 내용은 [Kerberos 서버 개체 만들기](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object)를 참조 하세요.

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>공개 키를 온-프레미스 AD DS에 등록 하지 않아 인터넷에 종속 되지 않는 이유는 무엇 인가요?

비즈니스용 Windows Hello에 대 한 배포 모델의 복잡성에 대 한 의견을 받았습니다. 따라서 인증서 및 PKI (FIDO2는 인증서를 사용 하지 않음)를 사용 하지 않고도 배포 모델을 단순화할 수 있습니다.

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Kerberos 서버 개체에서 키를 회전 하는 방법은 무엇입니까?

다른 DC와 마찬가지로 Azure AD Kerberos 서버 암호화 *krbtgt* 키를 정기적으로 회전 해야 합니다. 다른 모든 AD DS *krbtgt* 키를 회전 하는 데 사용 하는 것과 동일한 일정을 따르는 것이 좋습니다.

> [!NOTE]
> *Krbtgt* 키를 회전 하는 다른 도구가 있지만 PowerShell cmdlet을 사용 하 여 Azure AD Kerberos 서버의 [ *krbtgt* 키를 회전](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) 해야 합니다. 이 방법을 사용 하면 온-프레미스 AD DS 환경과 Azure AD 모두에서 키가 업데이트 됩니다.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Azure AD Connect 필요한 이유는 무엇 인가요? Azure AD에서 AD DS 정보를 다시 작성 하나요?

Azure AD Connect는 Azure AD에서 AD DS로 정보를 다시 쓰지 않습니다. 이 유틸리티에는 AD DS에서 Kerberos 서버 개체를 만들고 Azure AD에 게시 하기 위한 PowerShell 모듈이 포함 되어 있습니다.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>PRT + 부분 TGT를 요청할 때 HTTP 요청/응답의 모양은 무엇입니까?

HTTP 요청은 표준 주 새로 고침 토큰 (PRT) 요청입니다. 이 PRT 요청은 Kerberos TGT (허용 티켓)가 필요 함을 나타내는 클레임을 포함 합니다.

| 클레임 | 값 | Description                             |
|-------|-------|-----------------------------------------|
| tgt   | true  | 클레임은 클라이언트에 TGT가 필요 함을 나타냅니다. |

Azure AD는 암호화 된 클라이언트 키와 메시지 버퍼를 PRT 응답에 추가 속성으로 결합 합니다. 페이로드는 Azure AD 장치 세션 키를 사용 하 여 암호화 됩니다.

| 필드              | 형식   | Description  |
|--------------------|--------|--------------|
| tgt_client_key     | 문자열 | Base64 인코딩된 클라이언트 키 (암호)입니다. 이 키는 TGT를 보호 하는 데 사용 되는 클라이언트 암호입니다. 이 암호 없는 시나리오에서 클라이언트 암호는 각 TGT 요청의 일부로 서버에서 생성 된 다음 응답에서 클라이언트로 반환 됩니다. |
| tgt_key_type       | int    | 클라이언트 키와 KERB_MESSAGE_BUFFER에 포함 된 Kerberos 세션 키 모두에 사용 되는 온-프레미스 AD DS 키 유형입니다. |
| tgt_message_buffer | 문자열 | Base64 인코딩 KERB_MESSAGE_BUFFER입니다. |

## <a name="next-steps"></a>다음 단계

FIDO2 보안 키 및 온-프레미스 리소스에 대 한 하이브리드 액세스를 시작 하려면 다음 문서를 참조 하세요.

* [암호 없는 FIDO2 보안 키](howto-authentication-passwordless-security-key.md)
* [암호 없는 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [암호 없는 온-프레미스](howto-authentication-passwordless-security-key-on-premises.md)
