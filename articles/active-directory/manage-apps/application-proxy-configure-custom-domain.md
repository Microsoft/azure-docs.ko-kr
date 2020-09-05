---
title: Azure AD 응용 프로그램 프록시의 사용자 지정 도메인
description: Azure AD 애플리케이션 프록시에서 사용자 지정 도메인을 구성 및 관리합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 6688875385d34fcbece964d43827c6d62ae7ced4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587772"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 구성

Azure Active Directory 애플리케이션 프록시를 통해 애플리케이션을 게시할 때 사용자가 사용할 외부 URL을 만듭니다. 이 URL은 기본 도메인 *yourtenant.msappproxy.net*을 가져옵니다. 예를 들어 *Contoso*라는 이름의 테넌트에 *Expenses*라는 앱을 게시한 경우 외부 URL은 *https:\//expenses-contoso.msappproxy.net*이 됩니다. *msappproxy.net* 대신에 자신의 도메인 이름을 사용하려는 경우 애플리케이션에 대한 사용자 지정 도메인을 구성합니다. 

## <a name="benefits-of-custom-domains"></a>사용자 지정 도메인의 이점

가능하면 앱에 사용자 지정 도메인을 설정하는 것이 좋습니다. 사용자 지정 도메인을 사용하는 이유는 다음과 같습니다.

- 앱 간의 링크는 회사 네트워크 외부에서도 작동합니다. 사용자 지정 도메인이 없으면, 앱에 애플리케이션 프록시 외부의 대상에 대해 하드 코딩된 내부 링크가 있고 링크가 외부에서 확인되지 않는 경우 연결이 끊어집니다. 내부 및 외부 URL이 동일한 경우 이 문제를 방지합니다. 사용자 지정 도메인을 사용할 수 없는 경우 [Azure AD 애플리케이션 프록시로 게시된 앱에 대해 하드 코딩된 링크 리디렉션](../application-proxy-link-translation.md)에서 이 문제를 해결하는 다른 방법을 참조하세요. 
  
- 사용자는 네트워크 내부 또는 외부에서 동일한 URL을 사용하여 앱에 액세스할 수 있으므로 더 손쉽게 환경을 사용할 수 있습니다. 다른 내부 및 외부 URL에 대해 알아보거나 현재 위치를 추적할 필요가 없습니다. 

- 브랜딩을 제어하고 원하는 URL을 만들 수 있습니다. 사용자 지정 도메인은 사용자의 신뢰를 높이는 데 도움이 될 수 있습니다. 사용자가 *msappproxy.net* 대신 친숙한 이름을 보고 사용할 수 있기 때문입니다.

- 일부 구성은 사용자 지정 도메인에서만 작동합니다. 예를 들어 AD FS(Active Directory Federation Services)를 사용하지만 WS-Federation을 사용할 수 없는 경우와 같이 SAML(Security Assertion Markup Language)을 사용하는 앱에 사용자 지정 도메인이 필요합니다. 자세한 내용은 [애플리케이션 프록시에서 클레임 인식 앱으로 작업](application-proxy-configure-for-claims-aware-applications.md)을 참조하세요. 

내부 및 외부 URL을 일치시킬 수 없는 경우 사용자 지정 도메인을 사용하는 것은 중요하지 않지만 다른 혜택을 활용할 수 있습니다. 

## <a name="dns-configuration-options"></a>DNS 구성 옵션

요구 사항에 따라 DNS 구성을 설정하기 위한 몇 가지 옵션이 있습니다.

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>동일한 내부 및 외부 URL, 서로 다른 내부 및 외부 동작 

내부 사용자를 애플리케이션 프록시를 통해 이동시키지 않으려는 경우 *스플릿 브레인(Split-Brain) DNS*를 설정할 수 있습니다. 분할 DNS 인프라는 이름 확인을 위해 내부 호스트를 내부 도메인 이름 서버에, 외부 호스트를 외부 도메인 이름 서버에 전달합니다. 

![스플릿 브레인(Split-Brain) DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>다른 내부 및 외부 URL 

내부 및 외부 URL이 다른 경우 사용자 라우팅은 URL에 의해 결정되기 때문에 스플릿 브레인(Split-Brain) 동작을 구성할 필요가 없습니다. 이 경우 외부 DNS만 변경하고 외부 URL을 애플리케이션 프록시 엔드포인트로 라우팅합니다. 

외부 URL에 대해 사용자 지정 도메인을 선택하면 외부 DNS 공급자에 추가해야 하는 CNAME 항목이 알림 표시줄에 표시됩니다. 앱의 **애플리케이션 프록시** 페이지로 이동하여 언제든지 이 정보를 볼 수 있습니다.

## <a name="set-up-and-use-custom-domains"></a>사용자 지정 도메인 설정 및 사용

사용자 지정 도메인을 사용하도록 온-프레미스 앱을 구성하려면 확인된 Azure Active Directory 사용자 지정 도메인, 사용자 지정 도메인에 대한 PFX 인증서 및 구성할 온-프레미스 앱이 필요합니다. 

### <a name="create-and-verify-a-custom-domain"></a>사용자 지정 도메인 만들기 및 확인

사용자 지정 도메인을 만들고 확인하려면 다음을 수행합니다.

1. Azure Active Directory의 왼쪽 탐색 영역에서 **사용자 지정 도메인 이름**을 선택한 다음, **사용자 지정 도메인 추가**를 선택합니다. 
1. 사용자 지정 도메인 이름을 입력하고 **도메인 추가**를 선택합니다. 
1. 도메인 페이지에서 도메인에 대한 TXT 레코드 정보를 복사합니다. 
1. 도메인 등록자로 이동하여 복사한 DNS 정보에 따라 도메인에 대한 새 TXT 레코드를 만듭니다.
1. 도메인을 등록한 후 Azure Active Directory의 도메인 페이지에서 **확인**을 선택합니다. 도메인 상태가 **확인**된 후에는 애플리케이션 프록시를 비롯한 모든 Azure AD 구성에서 도메인을 사용할 수 있습니다. 

자세한 지침은 [Azure Active Directory 포털을 사용하여 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)를 참조하세요.

### <a name="configure-an-app-to-use-a-custom-domain"></a>사용자 지정 도메인을 사용하도록 앱 구성

사용자 지정 도메인을 사용하여 애플리케이션 프록시를 통해 앱을 게시하려면 다음을 수행합니다.

1. 새 앱의 경우 Azure Active Directory의 왼쪽 탐색 영역에서 **엔터프라이즈 애플리케이션**을 선택합니다. **새 애플리케이션**을 선택합니다. **온-프레미스 애플리케이션** 섹션에서 **온-프레미스 애플리케이션 추가**를 선택합니다. 
   
   **엔터프라이즈 애플리케이션**에 이미 있는 앱의 경우 목록에서 선택한 다음, 왼쪽 탐색 영역에서 **애플리케이션 프록시**를 선택합니다. 

2. 애플리케이션 프록시 설정 페이지에서 고유한 온-프레미스 애플리케이션을 추가하는 경우 **이름**을 입력합니다.

3.  **내부 URL** 필드에 앱에 대한 내부 URL을 입력합니다.
   
4. **외부 URL** 필드에서 목록을 드롭다운하고 사용하려는 사용자 지정 도메인을 선택합니다.
   
5. **추가**를 선택합니다.
   
   ![사용자 지정 도메인 선택](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 도메인에 인증서가 이미 있는 경우 **인증서** 필드에 인증서 정보가 표시됩니다. 그렇지 않은 경우 **인증서** 파일을 선택합니다.
   
   ![인증서를 업로드하려면 클릭](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. **SSL 인증서** 페이지에서 PFX 인증서 파일을 찾아 선택합니다. 인증서에 대한 암호를 입력하고 **인증서 업로드**를 선택합니다. 인증서에 대한 자세한 내용은 [사용자 지정 도메인에 대한 인증서](#certificates-for-custom-domains) 섹션을 참조하세요. 인증서가 유효 하지 않거나 암호에 문제가 있는 경우 오류 메시지가 표시 됩니다. [응용 프로그램 프록시 FAQ](application-proxy-faq.md#application-configuration) 에는 시도할 수 있는 몇 가지 문제 해결 단계가 포함 되어 있습니다.
   
   ![인증서 업로드](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 사용자 지정 도메인은 인증서를 한 번만 업로드하면 됩니다. 그 후에는 다른 앱에 사용자 지정 도메인을 사용할 때 업로드된 인증서가 자동으로 적용됩니다.
   
8. 인증서를 추가한 경우 **애플리케이션 프록시** 페이지에서 **저장**을 선택합니다. 
   
9. **애플리케이션 프록시** 페이지의 알림 표시줄에서 DNS 영역에 추가해야 하는 CNAME 항목을 확인합니다. 
   
   ![CNAME DNS 항목 추가](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. [Azure Portal을 사용하여 DNS 레코드 및 레코드 세트 관리](../../dns/dns-operations-recordsets-portal.md)의 지침에 따라 새 외부 URL을 *msappproxy.net* 도메인으로 리디렉션하는 DNS 레코드를 추가합니다.
   
11. DNS 레코드가 올바르게 구성되었는지 확인하려면 [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) 명령을 사용하여 외부 URL에 연결되는지와 *msapproxy.net* 도메인이 별칭으로 표시되는지를 확인합니다.

이제 애플리케이션이 사용자 지정 도메인을 사용하도록 설정됩니다. 애플리케이션을 테스트하거나 릴리스하기 전에 애플리케이션에 사용자를 할당해야 합니다. 

앱의 도메인을 변경하려면 앱의 **애플리케이션 프록시** 페이지에서 **외부 URL**의 드롭다운 목록에서 다른 도메인을 선택합니다. 필요한 경우 업데이트된 도메인에 대한 인증서를 업로드하고 DNS 레코드를 업데이트합니다. **외부 URL**의 드롭다운 목록에 원하는 사용자 지정 도메인이 표시되지 않는 경우 확인이 진행되지 않을 수 있습니다.

애플리케이션 프록시에 대한 자세한 내용은 [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)를 참조하세요.

## <a name="certificates-for-custom-domains"></a>사용자 지정 도메인에 대한 인증서

인증서는 사용자 지정 도메인에 대한 보안 TLS 연결을 만듭니다. 

### <a name="certificate-formats"></a>인증서 형식

PFX 인증서를 사용하여 필수 중간 인증서가 모두 포함되어 있는지 확인해야 합니다. 인증서에 프라이빗 키가 포함되어 있어야 합니다.

가장 일반적인 인증서 서명 방법은 SAN (주체 대체 이름)과 같이 지원 됩니다. 

와일드카드가 외부 URL과 일치하는 한, 와일드카드 인증서를 사용할 수 있습니다. [와일드카드 애플리케이션](application-proxy-wildcard.md)에 대한 와일드카드 인증서를 사용해야 합니다. 인증서를 사용하여 하위 도메인에도 액세스하려는 경우 하위 도메인 와일드카드를 동일한 인증서의 주체 대체 이름으로 추가해야 합니다. 예를 들어 *\*.apps.adventure-works.com*을 주체 대체 이름으로 추가하지 않으면 *\*.adventure-works.com*의 인증서는 *\*.apps.adventure-works.com*에 작동하지 않습니다. 

인증서 체인이 클라이언트 디바이스에 설치되어 있는 경우 고유한 PKI(공개 키 인프라)에서 발급한 인증서를 사용할 수 있습니다. Intune은 이러한 인증서를 관리 디바이스에 배포할 수 있습니다. 관리 디바이스가 아닌 경우 이러한 인증서를 수동으로 설치해야 합니다. 

프라이빗 루트 CA는 클라이언트 컴퓨터에 푸시해야 하고 많은 문제를 일으킬 수 있으므로 프라이빗 루트 CA 사용은 권장되지 않습니다.

### <a name="certificate-management"></a>인증서 관리

모든 인증서 관리는 개별 애플리케이션 페이지를 통해 관리됩니다. 애플리케이션의 **애플리케이션 프록시** 페이지로 이동하여 **인증서** 필드에 액세스합니다.

응용 프로그램에 대 한 인증서를 업로드 하면 동일한 인증서를 사용 하는 구성 된 **새** 앱에도 자동으로 적용 됩니다. 테 넌 트에서 기존 앱에 대 한 인증서를 다시 업로드 해야 합니다.

인증서가 만료되면 다른 인증서를 업로드하라는 경고를 받습니다. 인증서가 해지되면 앱에 액세스할 때 보안 경고가 사용자에게 표시될 수 있습니다. 앱에 대한 인증서를 업데이트하려면 앱에 대한 **애플리케이션 프록시** 페이지로 이동하고 **인증서**를 선택한 후, 새 인증서를 업로드합니다. 이전 인증서가 다른 앱에서 사용되고 있지 않으면 자동으로 삭제됩니다. 

## <a name="next-steps"></a>다음 단계

* Azure AD 인증을 사용하여 게시된 앱에 대해 [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* 게시된 클라우드 앱에 대한 [조건부 액세스](../conditional-access/concept-conditional-access-cloud-apps.md)
