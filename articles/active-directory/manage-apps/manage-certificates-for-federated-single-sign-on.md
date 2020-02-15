---
title: Azure AD에서 페더레이션 인증서 관리 | Microsoft Docs
description: 페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다.
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
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159032"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리

이 문서에서는 Azure AD (software as a service) 응용 프로그램에 대 한 SSO (Single Sign-On)를 설정 하기 위해 만드는 Azure Active Directory 인증서와 관련 된 일반적인 질문과 정보를 다룹니다. Azure AD 앱 갤러리에서 또는 비갤러리 애플리케이션 템플릿을 사용하여 애플리케이션을 추가합니다. 페더레이션된 SSO 옵션을 사용하여 애플리케이션을 구성합니다.

이 문서는 SAML ( [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) ) 페더레이션을 통해 AZURE AD SSO를 사용 하도록 구성 된 앱에만 해당 됩니다.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 애플리케이션에 대해 자동 생성된 인증서

갤러리에서 새 응용 프로그램을 추가 하 고 SAML 기반 로그온을 구성 하는 경우 (응용 프로그램 개요 페이지에서 **saml** > **Single sign-on** 을 선택 하 여) Azure AD는 3 년 동안 유효한 응용 프로그램에 대 한 인증서를 생성 합니다. 활성 인증서를 보안 인증서 ( **.cer**) 파일로 다운로드 하려면 해당 페이지로 돌아가 (**saml 기반 로그온**) **saml 서명 인증서** 제목에서 다운로드 링크를 선택 합니다. Raw (이진) 인증서 또는 Base64 (base 64 인코딩 텍스트) 인증서 중에서 선택할 수 있습니다. 갤러리 응용 프로그램의 경우이 섹션에는 응용 프로그램의 요구 사항에 따라 페더레이션 메타 데이터 XML ( **.xml** 파일)로 인증서를 다운로드 하는 링크가 표시 될 수도 있습니다.

![SAML 활성 서명 인증서 다운로드 옵션](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Saml **서명 인증서 제목의** **편집** 아이콘 (연필)을 선택 하 여 활성 또는 비활성 인증서를 다운로드 하 여 **saml 서명 인증서** 페이지를 표시할 수도 있습니다. 다운로드 하려는 인증서 옆에 있는 줄임표 ( **...** )를 선택 하 고 원하는 인증서 형식을 선택 합니다. 인증서를 PEM (개인 정보-고급 메일) 형식으로 다운로드 하는 추가 옵션이 있습니다. 이 형식은 a s e 64와 동일 하지만 Windows에서 인증서 형식으로 인식 되지 않는 **. pem** 파일 이름 확장명을 사용 합니다.

![SAML 서명 인증서 다운로드 옵션 (활성 및 비활성)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버

기본적으로 Azure는 SAML Single Sign-On 구성 중에 자동으로 생성 되는 3 년 후에 만료 되도록 인증서를 구성 합니다. 인증서를 저장 한 후에는 인증서의 날짜를 변경할 수 없으므로 다음을 수행 해야 합니다.

1. 원하는 날짜로 새 인증서를 만듭니다.
1. 새 인증서를 저장 합니다.
1. 새 인증서를 올바른 형식으로 다운로드 합니다.
1. 응용 프로그램에 새 인증서를 업로드 합니다.
1. Azure Active Directory 포털에서 새 인증서를 활성으로 설정 합니다.

다음 두 섹션에서는 이러한 단계를 수행 하는 데 도움이 됩니다.

### <a name="create-a-new-certificate"></a>새 인증서 만들기

먼저 다른 만료 날짜를 사용 하 여 새 인증서를 만들고 저장 합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인 합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 계정의 엔터프라이즈 응용 프로그램 목록이 표시 됩니다.
1. 영향을 받는 응용 프로그램을 선택 합니다. 응용 프로그램에 대 한 개요 페이지가 표시 됩니다.
1. 응용 프로그램 개요 페이지의 왼쪽 창에서 **Single sign-on**을 선택 합니다.
1. **Single Sign-On 방법 선택** 페이지가 표시 되 면 **SAML**을 선택 합니다.
1. **Saml 미리 보기를 사용 하 여 Single Sign-on 설정** 페이지에서 **saml 서명 인증서** 제목을 찾고 **편집** 아이콘 (연필)을 선택 합니다. **SAML 서명 인증서** 페이지가 표시 됩니다. 여기에는 각 인증서의 상태 (**활성** 또는 **비활성**), 만료 날짜 및 지문 (해시 문자열)이 표시 됩니다.
1. **새 인증서**를 선택 합니다. 인증서 목록 아래에 새 행이 나타납니다. 여기에서 만료 날짜의 기본값은 현재 날짜에서 정확히 3 년입니다. 변경 내용이 아직 저장 되지 않았으므로 만료 날짜를 수정할 수 있습니다.
1. 새 인증서 행에서 만료 날짜 열 위로 마우스를 이동 하 고 **날짜 선택** 아이콘 (일정)을 선택 합니다. 새 행의 현재 만료 날짜의 월을 표시 하는 달력 컨트롤이 나타납니다.
1. 달력 컨트롤을 사용 하 여 새 날짜를 설정할 수 있습니다. 현재 날짜와 3 년 사이에 현재 날짜 사이의 날짜를 설정할 수 있습니다.
1. **저장**을 선택합니다. 이제 새 인증서에 **비활성**상태, 선택한 만료 날짜 및 지문이 표시 됩니다.
1. **SAML 미리 보기를 사용 하 여 Single Sign-on 설정** 페이지로 돌아가려면 **X** 를 선택 합니다.

### <a name="upload-and-activate-a-certificate"></a>인증서 업로드 및 활성화

다음으로 새 인증서를 올바른 형식으로 다운로드 하 고, 응용 프로그램에 업로드 하 고, Azure Active Directory에서 활성으로 만듭니다.

1. 다음 중 하나를 수행 하 여 응용 프로그램의 추가 SAML sign-on 구성 지침을 확인 합니다.

   - 별도의 브라우저 창이 나 탭에서 볼 수 있도록 **구성 가이드** 링크 선택 또는
   - **설정** 제목으로 이동 하 여 사이드바에 표시 되는 단계별 **지침 보기** 를 선택 합니다.

1. 지침에서 인증서 업로드에 필요한 인코딩 형식을 확인 합니다.
1. 앞에서 [갤러리 및 비 갤러리 응용 프로그램에 대해 자동 생성 된 인증서](#auto-generated-certificate-for-gallery-and-non-gallery-applications) 섹션의 지침을 따르세요. 이 단계에서는 응용 프로그램에서 업로드 하는 데 필요한 인코딩 형식으로 인증서를 다운로드 합니다.
1. 새 인증서로 롤오버 하려면 **SAML 서명 인증서** 페이지로 돌아가 새로 저장 된 인증서 행에서 줄임표 ( **...** )를 선택 하 고 **인증서를 활성화 하도록**선택 합니다. 새 인증서의 상태가 **활성**으로 변경 되 고 이전에 활성화 된 인증서가 **비활성**상태로 변경 됩니다.
1. 앞에서 표시 한 응용 프로그램의 SAML 로그온 구성 지침에 따라 계속 하 여 SAML 서명 인증서를 올바른 인코딩 형식으로 업로드할 수 있습니다.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>인증서 만료에 대 한 전자 메일 알림 주소 추가

Azure AD는 SAML 인증서가 만료 되기 전에 60, 30 및 7 일의 전자 메일 알림을 보냅니다. 알림을 받을 전자 메일 주소를 둘 이상 추가할 수 있습니다. 알림을 보낼 전자 메일 주소를 지정 하려면 다음을 수행 합니다.

1. **SAML 서명 인증서** 페이지에서 **알림 전자 메일 주소** 제목으로 이동 합니다. 기본적으로이 제목은 응용 프로그램을 추가한 관리자의 전자 메일 주소만 사용 합니다.
1. 최종 전자 메일 주소 아래에 인증서의 만료 알림을 받을 전자 메일 주소를 입력 한 다음 Enter 키를 누릅니다.
1. 추가 하려는 각 전자 메일 주소에 대해 이전 단계를 반복 합니다.
1. 삭제 하려는 각 전자 메일 주소에 대해 전자 메일 주소 옆에 있는 **삭제** 아이콘 (가비지를 사용할 수 있음)을 선택 합니다.
1. **저장**을 선택합니다.

aadnotification@microsoft.com으로부터 알림 이메일을 받습니다. 스팸 메일로 전송 되는 전자 메일을 방지 하려면이 전자 메일을 연락처에 추가 합니다.

## <a name="renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서 갱신

인증서가 곧 만료 되는 경우 사용자에 게 심각한 가동 중지 시간이 발생 하지 않는 절차를 사용 하 여 갱신할 수 있습니다. 만료 되는 인증서를 갱신 하려면:

1. 기존 인증서와 겹치는 날짜를 사용 하 여 이전에 [새 인증서 만들기](#create-a-new-certificate) 섹션의 지침을 따릅니다. 이 날짜는 인증서 만료로 인 한 가동 중지 시간을 제한 합니다.
1. 응용 프로그램이 인증서를 자동으로 롤포워드할 수 있는 경우 다음 단계를 수행 하 여 새 인증서를 활성으로 설정 합니다.
   1. **SAML 서명 인증서** 페이지로 돌아갑니다.
   1. 새로 저장 된 인증서 행에서 줄임표 ( **...** )를 선택한 다음 **인증서를 활성 상태로 만들기**를 선택 합니다.
   1. 다음 두 단계를 건너뜁니다.

1. 앱이 한 번에 하나의 인증서만 처리할 수 있는 경우에는 다음 단계를 수행 하는 데 가동 중지 시간 간격을 선택 합니다. 응용 프로그램이 새 인증서를 자동으로 선택 하지 않고 둘 이상의 서명 인증서를 처리할 수 있는 경우에는 다음 단계를 언제 든 지 수행할 수 있습니다.
1. 이전 인증서가 만료 되기 전에 먼저 [인증서 업로드 및 활성화](#upload-and-activate-a-certificate) 섹션의 지침을 따르세요.
1. 응용 프로그램에 로그인 하 여 인증서가 제대로 작동 하는지 확인 합니다.

## <a name="related-articles"></a>관련 문서

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](../saas-apps/tutorial-list.md)
- [Azure Active Directory를 사용한 응용 프로그램 관리](what-is-application-management.md)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [Azure Active Directory의 응용 프로그램에 대 한 SAML 기반 Single Sign-On 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
