---
title: B2B에 대 한 AD FS를 사용 하 여 직접 페더레이션 설정-Azure AD
description: 게스트가 Azure AD 앱에 로그인 할 수 있도록 직접 페더레이션을 위한 id 공급자로 AD FS를 설정 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e350d6338b6ca589ab18d068ef6a314363fe205c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272838"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>예: Active Directory Federation Services을 사용 하 여 직접 페더레이션 (AD FS) (미리 보기)
|     |
| --- |
| 직접 페더레이션은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이 문서에서는 Active Directory Federation Services (AD FS)를 사용 하 여 SAML 2.0 또는 WS-급지 id 공급자로 [직접 페더레이션을](direct-federation.md) 설정 하는 방법을 설명 합니다. 직접 페더레이션을 지원 하려면 id 공급자에서 특정 특성과 클레임을 구성 해야 합니다. 직접 페더레이션을 위해 id 공급자를 구성 하는 방법을 설명 하기 위해 Active Directory Federation Services (AD FS)를 예로 사용 합니다. SAML id 공급자로 AD FS를 설정 하 고 WS-급지 id 공급자로를 설정 하는 방법을 보여 드리겠습니다.

> [!NOTE]
> 이 문서에서는 데모용으로 SAML 및 WS 공급 모두에 대 한 AD FS를 설정 하는 방법을 설명 합니다. Id 공급자가 AD FS 되는 직접 페더레이션 통합의 경우 WS-급지됨를 프로토콜로 사용 하는 것이 좋습니다. 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>SAML 2.0 직접 페더레이션을 위한 AD FS 구성
Azure AD B2B는 아래 나열 된 특정 요구 사항과 함께 SAML 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 구성할 수 있습니다. SAML 구성 단계를 설명 하기 위해이 섹션에서는 SAML 2.0에 대 한 AD FS를 설정 하는 방법을 보여 줍니다. 

직접 페더레이션을 설정 하려면 id 공급자의 SAML 2.0 응답에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 구성할 수 있습니다. [테스트 AD FS 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) 의 12 단계에서는 AD FS 엔드포인트을 찾는 방법이 나 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`와 같은 메타 데이터 URL을 생성 하는 방법을 설명 합니다. 

|특성  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |파트너 IdP의 발급자 URI (예: `http://www.example.com/exk10l6w90DHM0yi...`         |

Id 공급자가 발급 한 SAML 2.0 토큰에서 다음과 같은 클레임을 구성 해야 합니다.


|특성  |값  |
|---------|---------|
|NameID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


다음 섹션에서는 AD FS 사용 하 여 필요한 특성 및 클레임을 구성 하는 방법을 보여 줍니다. SAML 2.0 id 공급자의 예입니다.

### <a name="before-you-begin"></a>시작하기 전에

이 절차를 시작 하기 전에 AD FS 서버를 이미 설정 하 고 작동 해야 합니다. AD FS 서버 설정에 대 한 도움말은 [Azure 가상 머신에서 테스트 AD FS 3.0 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)를 참조 하세요.

### <a name="add-the-claim-description"></a>클레임 설명 추가

1. AD FS 서버에서 **도구** > **AD FS 관리**를 선택 합니다.
2. 탐색 창에서 **서비스** > **클레임 설명**을 선택 합니다.
3. **작업**아래에서 **클레임 설명 추가**를 선택 합니다.
4. **클레임 설명 추가** 창에서 다음 값을 지정 합니다.

   - **표시 이름**: 영구 식별자
   - **클레임 식별자**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - **페더레이션 메타 데이터의이 클레임 설명을이 페더레이션 서비스에서 수락할 수 있는 클레임 유형으로 게시**확인란을 선택 합니다.
   - **페더레이션 메타 데이터의이 클레임 설명을이 페더레이션 서비스에서 보낼 수 있는 클레임 유형으로 게시**확인란을 선택 합니다.

5. **확인**을 클릭합니다.

### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 트러스트 및 클레임 규칙 추가

1. AD FS 서버에서 **도구** > **AD FS 관리**로 이동 합니다.
2. 탐색 창에서 신뢰 당사자 **트러스트** > **신뢰 관계** 를 선택 합니다.
3. **작업**아래에서 **신뢰 당사자 트러스트 추가**를 선택 합니다. 
4. **Select Data Source**에 대 한 신뢰 당사자 트러스트 추가 마법사에서 **온라인 또는 로컬 네트워크에 게시 된 신뢰 당사자에 대 한 데이터 가져오기**옵션을 사용 합니다. 이 페더레이션 메타 데이터 URL https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml를 지정 합니다. 다른 기본 선택 항목을 유지 합니다. **닫기**를 선택합니다.
5. **클레임 규칙 편집** 마법사가 열립니다.
6. **클레임 규칙 편집** 마법사에서 **규칙 추가**를 선택 합니다. **규칙 유형 선택**에서 **LDAP 특성을 클레임으로 보내기**를 선택 합니다. **다음**을 선택합니다.
7. **클레임 규칙 구성**에서 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 클레임 규칙 
   - **특성 저장소**: Active Directory 
   - **LDAP 특성**: 전자 메일 주소 
   - **보내는 클레임 유형**: 전자 메일 주소

8. **마침**을 선택합니다.
9. **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다. 
10. **확인**을 클릭합니다.  

### <a name="create-an-email-transform-rule"></a>전자 메일 변환 규칙 만들기
1. **클레임 규칙 편집** 으로 이동 하 여 **규칙 추가**를 클릭 합니다. **규칙 유형 선택**에서 **들어오는 클레임 변환** 을 선택 하 고 **다음**을 클릭 합니다. 
2. **클레임 규칙 구성**에서 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 변환 규칙 
   - **들어오는 클레임 유형**: 전자 메일 주소 
   - **보내는 클레임 유형**: 이름 ID 
   - **보내는 이름 ID 형식**: 영구 식별자 
   - **모든 클레임 값 통과**를 선택합니다.

3. **Finish**를 클릭합니다. 
4. **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다. 
5. **확인**을 클릭합니다. 이제 AD FS 서버는 SAML 2.0 프로토콜을 사용 하 여 직접 페더레이션을 위해 구성 됩니다.

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>WS-급지됨 직접 페더레이션을 위한 AD FS 구성 
Azure AD B2B는 아래 나열 된 특정 요구 사항과 함께 WS-급지됨 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 구성할 수 있습니다. 현재 두 개의 WS (Shibboleth) 공급자는 Azure AD와의 호환성을 테스트 했습니다. 여기에는 AD FS 및 있습니다. 여기서는 WS-급지 id 공급자의 예로 Active Directory Federation Services (AD FS)를 사용 합니다. Azure AD를 사용 하 여 WS-급지 규격 공급자 간에 신뢰 당사자 트러스트를 설정 하는 방법에 대 한 자세한 내용은 Azure AD Id 공급자 호환성 문서를 다운로드 하세요.

직접 페더레이션을 설정 하려면 id 공급자의 WS-급지됨 메시지에서 다음 특성을 받아야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 구성할 수 있습니다. [테스트 AD FS 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed) 의 12 단계에서는 AD FS 엔드포인트을 찾는 방법이 나 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`와 같은 메타 데이터 URL을 생성 하는 방법을 설명 합니다.
 
|특성  |값  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |파트너 IdP의 발급자 URI (예: `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발급 한 WS-급지됨 토큰에 필요한 클레임:

|특성  |값  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

다음 섹션에서는 AD FS를 사용 하 여 필요한 특성 및 클레임을 구성 하는 방법을 설명 합니다.

### <a name="before-you-begin"></a>시작하기 전에
이 절차를 시작 하기 전에 AD FS 서버를 이미 설정 하 고 작동 해야 합니다. AD FS 서버 설정에 대 한 도움말은 [Azure 가상 머신에서 테스트 AD FS 3.0 인스턴스 만들기](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)를 참조 하세요.


### <a name="add-the-relying-party-trust-and-claim-rules"></a>신뢰 당사자 트러스트 및 클레임 규칙 추가 
1. AD FS 서버에서 **도구** > **AD FS 관리**로 이동 합니다. 
1. 탐색 창에서 신뢰 당사자 **트러스트** > **신뢰 관계** 를 선택 합니다. 
1. **작업**아래에서 **신뢰 당사자 트러스트 추가**를 선택 합니다.  
1. 신뢰 당사자 트러스트 추가 마법사에서 **데이터 원본 선택**에 대해 **온라인 또는 로컬 네트워크에 게시 된 신뢰 당사자에 대 한 데이터 가져오기**옵션을 사용 합니다. 페더레이션 메타 데이터 URL `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`를 지정 합니다.  다른 기본 선택 항목을 유지 합니다. **닫기**를 선택합니다.
1. **클레임 규칙 편집** 마법사가 열립니다. 
1. **클레임 규칙 편집** 마법사에서 **규칙 추가**를 선택 합니다. **규칙 유형 선택**에서 **사용자 지정 규칙을 사용 하 여 클레임 보내기**를 선택 합니다. *다음*을 선택합니다. 
1. **클레임 규칙 구성**에서 다음 값을 지정 합니다.

   - **클레임 규칙 이름**: 변경 불가능 Id 문제  
   - **사용자 지정 규칙**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. **마침**을 선택합니다. 
1. **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다.  
1. 동일한 **클레임 규칙 편집** 마법사에서 **규칙 추가**를 선택 합니다. **Cohose 규칙 유형**에서 **LDAP 특성을 클레임으로 보내기**를 선택 합니다. **다음**을 선택합니다.
1. **클레임 규칙 구성**에서 다음 값을 지정 합니다. 

   - **클레임 규칙 이름**: 전자 메일 클레임 규칙  
   - **특성 저장소**: Active Directory  
   - **LDAP 특성**: 전자 메일 주소  
   - **보내는 클레임 유형**: 전자 메일 주소 

1.  **마침**을 선택합니다. 
1.  **클레임 규칙 편집** 창에 새 규칙이 표시 됩니다. **적용**을 클릭합니다.  
1.  **확인**을 클릭합니다. 이제 WS-급지됨를 사용 하 여 직접 페더레이션을 위해 AD FS 서버가 구성 됩니다.

## <a name="next-steps"></a>다음 단계
다음으로 azure ad 포털에서 또는 PowerShell을 사용 하 여 [AZURE ad에서 직접 페더레이션을 구성](direct-federation.md#step-2-configure-direct-federation-in-azure-ad) 합니다. 
