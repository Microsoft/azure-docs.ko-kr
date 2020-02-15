---
title: Azure AD 애플리케이션 프록시의 사용자 지정 도메인 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인을 구성 하 고 관리 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1656d730d55d4c5ab7fb963e49a8057ad88c9f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185531"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 여 사용자 지정 도메인 구성

Azure Active Directory 응용 프로그램 프록시를 통해 응용 프로그램을 게시 하는 경우 사용자에 대 한 외부 URL을 만듭니다. 이 URL은 기본 도메인 *yourtenant.msappproxy.net*을 가져옵니다. 예를 들어 *Contoso*라는 테 넌 트에 *지출* 이라는 앱을 게시 하는 경우 외부 URL은 *https:\//expenses-contoso.msappproxy.net*입니다. *Msappproxy.net*대신 고유한 도메인 이름을 사용 하려는 경우 응용 프로그램에 대 한 사용자 지정 도메인을 구성할 수 있습니다. 

## <a name="benefits-of-custom-domains"></a>사용자 지정 도메인의 이점

가능 하면 앱에 대 한 사용자 지정 도메인을 설정 하는 것이 좋습니다. 사용자 지정 도메인을 사용 하는 몇 가지 이유는 다음과 같습니다.

- 앱 간의 링크는 회사 네트워크 외부 에서도 작동 합니다. 사용자 지정 도메인이 없으면 앱이 응용 프로그램 프록시 외부의 대상에 대해 하드 코드 된 내부 링크를 포함 하 고 링크를 외부에서 확인할 수 없으면 중단 됩니다. 내부 및 외부 Url이 동일한 경우이 문제를 방지 합니다. 사용자 지정 도메인을 사용할 수 없는 경우이 문제를 해결 하는 다른 방법으로 [Azure AD 응용 프로그램 프록시로 게시 된 앱에 대해 하드 코딩 된 링크 리디렉션](../application-proxy-link-translation.md) 을 참조 하세요. 
  
- 사용자는 네트워크 내부 또는 외부에서 동일한 URL을 사용 하 여 앱에 액세스할 수 있으므로 더 쉽게 환경을 사용할 수 있습니다. 다른 내부 및 외부 Url을 배우고 현재 위치를 추적할 필요가 없습니다. 

- 브랜딩을 제어 하 고 원하는 Url을 만들 수 있습니다. 사용자 지정 도메인을 사용 하면 사용자가 *msappproxy.net*대신 친숙 한 이름을 보고 사용 하기 때문에 사용자의 확신을 쉽게 구축할 수 있습니다.

- 일부 구성은 사용자 지정 도메인에만 적용 됩니다. 예를 들어 Active Directory Federation Services (AD FS)를 사용 하지만 WS-FEDERATION을 사용할 수 없는 경우와 같이 SAML (Security Assertion Markup Language)를 사용 하는 앱에 대 한 사용자 지정 도메인이 필요 합니다. 자세한 내용은 [응용 프로그램 프록시에서 클레임 인식 앱 작업](application-proxy-configure-for-claims-aware-applications.md)을 참조 하세요. 

내부 및 외부 Url을 일치 시킬 수 없는 경우 사용자 지정 도메인을 사용 하는 것은 중요 하지 않지만 다른 혜택을 활용할 수 있습니다. 

## <a name="dns-configuration-options"></a>DNS 구성 옵션

요구 사항에 따라 DNS 구성을 설정 하기 위한 몇 가지 옵션이 있습니다.

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>동일한 내부 및 외부 URL, 서로 다른 내부 및 외부 동작 

응용 프로그램 프록시를 통해 내부 사용자를 전달 하지 않으려는 경우 *분할-두뇌 DNS*를 설정할 수 있습니다. 분할 DNS 인프라는 이름 확인을 위해 내부 호스트를 내부 도메인 이름 서버에, 외부 호스트를 외부 도메인 이름 서버로 전달 합니다. 

![스플릿 브레인(Split-Brain) DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>서로 다른 내부 및 외부 Url 

내부 및 외부 Url이 다른 경우 사용자 라우팅은 URL에 의해 결정 되기 때문에 분할 인 두뇌 동작을 구성할 필요가 없습니다. 이 경우 외부 DNS만 변경 하 고 외부 URL을 응용 프로그램 프록시 끝점으로 라우팅합니다. 

외부 URL에 대해 사용자 지정 도메인을 선택 하면 외부 DNS 공급자에 추가 해야 하는 CNAME 항목이 알림 표시줄에 표시 됩니다. 앱의 **응용 프로그램 프록시** 페이지로 이동 하 여 언제 든 지이 정보를 볼 수 있습니다.

## <a name="set-up-and-use-custom-domains"></a>사용자 지정 도메인 설정 및 사용

사용자 지정 도메인을 사용 하도록 온-프레미스 앱을 구성 하려면 확인 된 Azure Active Directory 사용자 지정 도메인, 사용자 지정 도메인에 대 한 PFX 인증서 및 구성할 온-프레미스 앱이 필요 합니다. 

### <a name="create-and-verify-a-custom-domain"></a>사용자 지정 도메인 만들기 및 확인

사용자 지정 도메인을 만들고 확인 하려면:

1. Azure Active Directory의 왼쪽 탐색 **영역에서 사용자 지정 도메인 이름** 을 선택 하 고 **사용자 지정 도메인 추가**를 선택 합니다. 
1. 사용자 지정 도메인 이름을 입력 하 고 **도메인 추가**를 선택 합니다. 
1. 도메인 페이지에서 도메인에 대 한 TXT 레코드 정보를 복사 합니다. 
1. 도메인 등록자로 이동 하 여 복사한 DNS 정보에 따라 도메인에 대 한 새 TXT 레코드를 만듭니다.
1. 도메인을 등록 한 후 Azure Active Directory의 도메인 페이지에서 **확인**을 선택 합니다. 도메인 상태가 **확인**되 면 응용 프로그램 프록시를 비롯 한 모든 Azure AD 구성에서 도메인을 사용할 수 있습니다. 

자세한 지침은 [Azure Active Directory 포털을 사용 하 여 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)를 참조 하세요.

### <a name="configure-an-app-to-use-a-custom-domain"></a>사용자 지정 도메인을 사용 하도록 앱 구성

사용자 지정 도메인을 사용 하 여 응용 프로그램 프록시를 통해 앱을 게시 하려면:

1. 새 앱의 경우 Azure Active Directory의 왼쪽 탐색 영역에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. **새 애플리케이션**을 선택합니다. **온-프레미스 애플리케이션** 섹션에서 **온-프레미스 애플리케이션 추가**를 선택합니다. 
   
   **엔터프라이즈 응용 프로그램**에 이미 있는 앱의 경우 목록에서 선택 하 고 왼쪽 탐색 영역에서 **응용 프로그램 프록시** 를 선택 합니다. 

2. 응용 프로그램 프록시 설정 페이지에서 고유한 온-프레미스 응용 프로그램을 추가 하는 경우 **이름을** 입력 합니다.

3.  **내부 url** 필드에 앱에 대 한 내부 url을 입력 합니다.
   
4. **외부 Url** 필드에서 목록을 드롭다운 하 고 사용 하려는 사용자 지정 도메인을 선택 합니다.
   
5. **추가**를 선택합니다.
   
   ![사용자 지정 도메인 선택](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 도메인에 이미 인증서가 있는 경우 **인증서 필드에 인증서 정보가** 표시 됩니다. 그렇지 않으면 **인증서** 필드를 선택 합니다. 
   
   ![인증서를 업로드하려면 클릭](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. **SSL 인증서** 페이지에서 PFX 인증서 파일을 찾아 선택 합니다. 인증서에 대 한 암호를 입력 하 고 **인증서 업로드**를 선택 합니다. 인증서에 대 한 자세한 내용은 [사용자 지정 도메인에 대 한 인증서](#certificates-for-custom-domains) 섹션을 참조 하세요.
   
   ![인증서 업로드](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 사용자 지정 도메인은 인증서를 한 번 업로드 하기만 하면 됩니다. 그 후에는 다른 앱에 사용자 지정 도메인을 사용 하는 경우 업로드 된 인증서가 자동으로 적용 됩니다.
   
8. 인증서를 추가한 경우 **응용 프로그램 프록시** 페이지에서 **저장**을 선택 합니다. 
   
9. **응용 프로그램 프록시** 페이지의 알림 표시줄에서 DNS 영역에 추가 해야 하는 CNAME 항목을 확인 합니다. 
   
   ![CNAME DNS 항목 추가](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. [Azure Portal를 사용 하 여 dns 레코드 및 레코드 집합 관리](../../dns/dns-operations-recordsets-portal.md) 의 지침에 따라 새 외부 URL을 *msappproxy.net* 도메인으로 리디렉션하는 dns 레코드를 추가 합니다.
   
11. DNS 레코드가 올바르게 구성 되었는지 확인 하려면 [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) 명령을 사용 하 여 외부 URL에 연결할 수 있고 *msapproxy.net* 도메인이 별칭으로 표시 되는지 확인 합니다.

이제 응용 프로그램이 사용자 지정 도메인을 사용 하도록 설정 됩니다. 응용 프로그램을 테스트 하거나 해제 하기 전에 응용 프로그램에 사용자를 할당 해야 합니다. 

앱의 도메인을 변경 하려면 앱의 **응용 프로그램 프록시** 페이지에서 **외부 URL** 의 드롭다운 목록에서 다른 도메인을 선택 합니다. 필요한 경우 업데이트 된 도메인에 대 한 인증서를 업로드 하 고 DNS 레코드를 업데이트 합니다. **외부 URL**의 드롭다운 목록에 원하는 사용자 지정 도메인이 표시 되지 않는 경우 확인 되지 않을 수 있습니다.

응용 프로그램 프록시에 대 한 자세한 지침은 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가 Azure Active Directory](application-proxy-add-on-premises-application.md)를 참조 하세요.

## <a name="certificates-for-custom-domains"></a>사용자 지정 도메인에 대 한 인증서

인증서는 사용자 지정 도메인에 대 한 보안 SSL 연결을 만듭니다. 

### <a name="certificate-formats"></a>인증서 형식

필요한 모든 중간 인증서가 포함 되어 있는지 확인 하려면 PFX 인증서를 사용 해야 합니다. 인증서에 개인 키가 포함 되어 있어야 합니다.

인증서 서명 방법에 대 한 제한은 없습니다. ECC (타원 Curve Cryptography), SAN (주체 대체 이름) 및 기타 일반 인증서 유형이 지원 됩니다. 

와일드 카드가 외부 URL과 일치 하는 한 와일드 카드 인증서를 사용할 수 있습니다. 와일드 카드 [응용 프로그램](application-proxy-wildcard.md)에는 와일드 카드 인증서를 사용 해야 합니다. 인증서를 사용 하 여 하위 도메인에도 액세스 하려는 경우 하위 도메인 와일드 카드를 동일한 인증서의 주체 대체 이름으로 추가 해야 합니다. 예를 들어 adventure-works.com에 대 *\** 한 인증서는 주체 대체 이름으로 *apps.adventure-works.com를\** 추가 하지 않는 한 *apps.adventure-works.com에\** 대해 작동 하지 않습니다. 

인증서 체인이 클라이언트 장치에 설치 되어 있는 경우 고유한 PKI (공개 키 인프라)에서 발급 한 인증서를 사용할 수 있습니다. Intune은 이러한 인증서를 관리 되는 장치에 배포할 수 있습니다. 관리 되지 않는 장치의 경우 이러한 인증서를 수동으로 설치 해야 합니다.

개인 루트 CA는 사용 하지 않는 것이 좋습니다. 또한 개인 루트 CA를 클라이언트 컴퓨터에 푸시 해야 하므로 많은 과제가 발생 합니다. 

### <a name="certificate-management"></a>인증서 관리

모든 인증서 관리는 개별 응용 프로그램 페이지를 통해 관리 됩니다. 응용 프로그램의 **응용 프로그램 프록시** 페이지로 이동 하 여 **인증서** 필드에 액세스 합니다.

여러 응용 프로그램에 동일한 인증서를 사용할 수 있습니다. 업로드 된 인증서가 다른 응용 프로그램에서 작동 하는 경우 자동으로 적용 됩니다. 앱을 추가 하거나 구성할 때 다시 업로드 하 라는 메시지가 표시 되지 않습니다. 

인증서가 만료 되 면 다른 인증서를 업로드 하 라는 경고가 표시 됩니다. 인증서가 해지 되 면 앱에 액세스할 때 사용자에 게 보안 경고가 표시 될 수 있습니다. 앱에 대 한 인증서를 업데이트 하려면 앱에 대 한 **응용 프로그램 프록시** 페이지로 이동 하 여 **인증서**를 선택 하 고 새 인증서를 업로드 합니다. 이전 인증서를 다른 앱에서 사용 하지 않는 경우 자동으로 삭제 됩니다. 

## <a name="next-steps"></a>다음 단계
* Azure AD 인증을 사용하여 게시된 앱에 대해 [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* 게시 된 앱에 대 한 [조건부 액세스를 사용 하도록 설정](../conditional-access/overview.md) 합니다.

