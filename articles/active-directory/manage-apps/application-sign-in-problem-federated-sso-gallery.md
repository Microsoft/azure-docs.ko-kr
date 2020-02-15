---
title: 페더레이션된 Single Sign-On 갤러리 앱에 로그인 하는 데 문제가 있습니다. | Microsoft Docs
description: Azure AD에서 SAML 기반 페더레이션된 Single Sign-On에 대해 구성된 애플리케이션에 로그인하는 경우 특정 문제에 대한 지침
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97954123b6fc31dce09282c08c702438cd64c476
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159253"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>페더레이션된 Single Sign-On에 대해 구성된 갤러리 애플리케이션에 로그인하는 문제

아래의 로그인 문제를 해결 하려면 다음 제안에 따라 진단을 개선 하 고 해결 단계를 자동화 하는 것이 좋습니다.

- Azure Portal에서 테스트 환경을 사용할 때 더 나은 진단 및 해결 방법을 제공 하기 위해 [My Apps 보안 브라우저 확장](access-panel-extension-problem-installing.md) 을 설치 하 여 Azure AD (Azure Active Directory)를 지원 합니다.
- Azure Portal의 앱 구성 페이지에서 테스트 환경을 사용 하 여 오류를 재현 합니다. [SAML 기반 Single Sign-On 응용 프로그램 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 에 대 한 자세한 정보


## <a name="application-not-found-in-directory"></a>애플리케이션을 디렉터리에서 찾을 수 없습니다

*오류 AADSTS70001: 디렉터리에서 식별자가 ' https:\//contoso.com ' 인 응용 프로그램을 찾을 수 없습니다*.

**가능한 원인**

응용 프로그램에서 SAML 요청의 Azure AD로 전송 되는 `Issuer` 특성은 Azure AD에서 응용 프로그램에 대해 구성 된 식별자 값과 일치 하지 않습니다.

**해결 방법**

SAML 요청의 `Issuer` 특성이 Azure AD에 구성 된 식별자 값과 일치 하는지 확인 합니다. 내 앱 보안 브라우저 확장을 사용 하 여 Azure Portal에서 [테스트 환경을](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 사용 하는 경우 다음 단계를 수동으로 수행할 필요가 없습니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

1.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

1.  필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

1.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

1.  **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

1.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

1.  애플리케이션이 로드되면 **기본 SAML 구성**을 엽니다. 식별자 텍스트 상자의 값이 오류에 표시 된 식별자 값의 값과 일치 하는지 확인 합니다.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>회신 주소가 응용 프로그램에 대해 구성 된 회신 주소와 일치 하지 않습니다.

*오류 AADSTS50011: 회신 주소 ' https:\//contoso.com '은 (는) 응용 프로그램에 대해 구성 된 회신 주소와 일치 하지 않습니다.*

**가능한 원인**

SAML 요청의 `AssertionConsumerServiceURL` 값이 Azure AD에 구성 된 회신 URL 값 또는 패턴과 일치 하지 않습니다. SAML 요청의 `AssertionConsumerServiceURL` 값은 오류에 표시 되는 URL입니다.

**해결 방법**

SAML 요청의 `AssertionConsumerServiceURL` 값이 Azure AD에 구성 된 회신 URL 값과 일치 하는지 확인 합니다. 내 앱 보안 브라우저 확장을 사용 하 여 Azure Portal에서 [테스트 환경을](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 사용 하는 경우 다음 단계를 수동으로 수행할 필요가 없습니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

1.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

1.  필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

1.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

1.  **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

1.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

1.  애플리케이션이 로드되면 **기본 SAML 구성**을 엽니다. [회신 URL] 텍스트 상자의 값을 확인 하거나 업데이트 하 여 SAML 요청의 `AssertionConsumerServiceURL` 값과 일치 시킵니다.    
    
Azure AD에서 회신 URL 값을 업데이트 하 고 SAML 요청에서 응용 프로그램에 의해 전송 된 값과 일치 하는 경우 응용 프로그램에 로그인 할 수 있어야 합니다.

## <a name="user-not-assigned-a-role"></a>역할이 지정되지 않은 사용자

*오류 AADSTS50105: 로그인 한 사용자 ' contoso.com\@'은 (는) 응용 프로그램의 역할에 할당 되지 않았습니다*.

**가능한 원인**

사용자는 Azure AD에서 애플리케이션에 대한 액세스 권한을 부여받지 않았습니다.

**해결 방법**

응용 프로그램에 하나 이상의 사용자를 직접 할당 하려면 다음 단계를 수행 합니다. 내 앱 보안 브라우저 확장을 사용 하 여 Azure Portal에서 [테스트 환경을](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 사용 하는 경우 다음 단계를 수동으로 수행할 필요가 없습니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

1.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

1.  필터 검색 상자에 **"Azure Active Directory**"를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

1.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

1.  **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

1.  응용 프로그램 목록에서 사용자를 할당 하려는 응용 프로그램을 선택 합니다.

1.  응용 프로그램이 로드 되 면 응용 프로그램의 왼쪽 탐색 메뉴에서 **사용자 및 그룹** 을 선택 합니다.

1.  **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭하여 **할당 추가** 창을 엽니다.

1.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 선택합니다.

1. **이름 또는 이메일 주소로 검색** 검색 상자에 추가하려는 사용자의 전체 이름 또는 이메일 주소를 입력합니다.

1. 목록의 **사용자** 위로 마우스를 이동하여 **확인란**을 표시합니다. 사용자의 프로필 사진 또는 로고 옆에 있는 확인란을 클릭 하 여 사용자를 **선택** 된 목록에 추가 합니다.

1. **선택 사항:** 사용자를 두 명 이상 **추가**하려면 **이름 또는 전자 메일 주소로 검색** 검색 상자에 다른 전체 이름 또는 전자 메일 주소를 입력 하 고 확인란을 클릭 하 여 **선택한** 목록에 사용자를 추가 합니다.

1. 사용자 선택이 완료 되 면 **선택** 단추를 클릭 하 여 응용 프로그램에 할당 될 사용자 및 그룹의 목록에 추가 합니다.

1. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭 하 여 선택한 사용자에 게 할당할 역할을 선택 합니다.

1. **할당** 단추를 클릭하여 선택한 사용자에게 애플리케이션을 할당합니다.

잠시 후 선택한 사용자가 솔루션 설명 섹션에 설명 된 방법을 사용 하 여 이러한 응용 프로그램을 시작할 수 있습니다.

## <a name="not-a-valid-saml-request"></a>유효한 SAML 요청이 아닙니다.

*오류 AADSTS75005: 요청이 유효한 Saml2 프로토콜 메시지가 아닙니다.*

**가능한 원인**

Azure AD에서는 Single Sign-On의 애플리케이션에서 보낸 SAML 요청을 지원하지 않습니다. 일반적인 문제는 다음과 같습니다.

-   SAML 요청에서 필수 필드 누락
-   SAML 요청 인코딩 방법

**해결 방법**

1. SAML 요청을 캡처합니다. Saml [기반 Single Sign-On를 AZURE AD의 응용 프로그램에 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 하는 방법 자습서에 따라 saml 요청을 캡처하는 방법을 알아봅니다.

1. 애플리케이션 공급 업체에 연락하여 다음 정보를 공유합니다.

   -   SAML 요청

   -   [Azure AD Single Sign-On SAML 프로토콜 요구 사항](../develop/single-sign-on-saml-protocol.md)

응용 프로그램 공급 업체는 Single Sign-On에 대 한 Azure AD SAML 구현을 지원 하는지 확인 해야 합니다.

## <a name="misconfigured-application"></a>잘못 구성 되는 응용 프로그램

*오류 AADSTS650056: 잘못 구성 되었습니다. 클라이언트의 응용 프로그램 등록에서 요청 된 사용 권한의 ' AAD Graph '에 대 한 사용 권한이 클라이언트에 나열 되지 않았기 때문일 수 있습니다. 또는 관리자가 테 넌 트에서 동의한 하지 않았습니다. 또는 요청에서 응용 프로그램 식별자가 구성 된 클라이언트 응용 프로그램 식별자와 일치 하는지 확인 합니다. 관리자에 게 문의 하 여 테 넌 트를 대신 하 여 구성을 수정 하거나 동의 하세요*.

**가능한 원인**

응용 프로그램에서 SAML 요청의 Azure AD로 전송 되는 `Issuer` 특성은 Azure AD의 응용 프로그램에 대해 구성 된 식별자 값과 일치 하지 않습니다.

**해결 방법**

SAML 요청의 `Issuer` 특성이 Azure AD에 구성 된 식별자 값과 일치 하는지 확인 합니다. 내 앱 보안 브라우저 확장을 사용 하 여 Azure Portal에서 [테스트 환경을](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 사용 하는 경우 다음 단계를 수동으로 수행할 필요가 없습니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

1.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

1.  필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

1.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

1.  **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

1.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

1.  애플리케이션이 로드되면 **기본 SAML 구성**을 엽니다. 식별자 텍스트 상자의 값이 오류에 표시 된 식별자 값의 값과 일치 하는지 확인 합니다.


## <a name="certificate-or-key-not-configured"></a>인증서 또는 키가 구성되지 않음

*오류 AADSTS50003: 서명 키가 구성되지 않았습니다.*

**가능한 원인**

애플리케이션 개체가 손상되어 Azure AD에서 애플리케이션에 대해 구성된 인증서를 인식하지 못합니다.

**해결 방법**

기존 인증서를 삭제하고 새 인증서를 만들려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

1. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

1. 필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

1. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

1. **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

1. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

1. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

1. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기** 를 선택 합니다.

1. 만료 날짜를 선택 하 고 **저장**을 클릭 합니다.

1. **새 인증서 활성화**를 선택하여 활성 인증서를 재정의합니다. 그런 다음, 창 맨 위에 있는 **저장**을 클릭하고 롤오버 인증서를 활성화하도록 수락합니다.

1. **SAML 서명 인증서** 섹션에서 **제거**를 클릭하여 **사용하지 않는** 인증서를 제거합니다.

## <a name="saml-request-not-present-in-the-request"></a>요청에 SAML 요청이 없습니다.

*오류 AADSTS750054: SAMLRequest 또는 Samlrequest는 SAML 리디렉션 바인딩에 대 한 HTTP 요청에서 쿼리 문자열 매개 변수로 제공 되어야 합니다.*

**가능한 원인**

Azure AD가 HTTP 요청의 URL 매개 변수 내에서 SAML 요청을 식별할 수 없었습니다. 이 문제는 응용 프로그램이 Azure AD에 SAML 요청을 보낼 때 HTTP 리디렉션 바인딩을 사용 하지 않는 경우에 발생할 수 있습니다.

**해결 방법**

응용 프로그램은 HTTP 리디렉션 바인딩을 사용 하 여 location 헤더로 인코드된 SAML 요청을 보내야 합니다. 구현 방법에 대한 자세한 내용은 [SAML 프로토콜 사양 문서](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)의 HTTP 리디렉션 바인딩 섹션을 참조하세요.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD가 토큰을 잘못 된 끝점으로 보내고 있습니다.

**가능한 원인**

Single Sign-On 하는 동안 로그인 요청에 명시적 회신 URL (Assertion Consumer Service URL)이 포함 되어 있지 않으면 Azure AD는 해당 응용 프로그램에 대해 구성 된 모든 사용 Url을 선택 합니다. 응용 프로그램에 명시적 회신 URL이 구성 되어 있는 경우에도 사용자는 https://127.0.0.1:444리디렉션되는 것일 수 있습니다. 

애플리케이션을 비-갤러리 앱으로 추가하면 Azure Active Directory에서는 이 회신 URL을 기본값으로 생성했습니다. 이 동작이 변경되어 Azure Active Directory는 기본적으로 이 URL을 더 이상 추가하지 않습니다. 

**해결 방법**

응용 프로그램에 대해 구성 된 사용 하지 않는 회신 Url을 삭제 합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.

3.  필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5.  **모든 애플리케이션**을 선택하여 모든 애플리케이션 목록을 봅니다.

    여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.

7.  애플리케이션이 로드되면 **기본 SAML 구성**을 엽니다. **회신 url (Assertion Consumer Service URL)** 에서 시스템에 의해 생성 된 사용 하지 않거나 기본 회신 url을 삭제 합니다. `https://127.0.0.1:444/applications/default.aspx`)을 입력합니다.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>애플리케이션에 전송된 SAML 클레임을 사용자 지정할 때 발생하는 문제

응용 프로그램에 전송 된 SAML 특성 클레임을 사용자 지정 하는 방법을 알아보려면 [Azure Active Directory에서 클레임 매핑](../develop/active-directory-claims-mapping.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure AD에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
