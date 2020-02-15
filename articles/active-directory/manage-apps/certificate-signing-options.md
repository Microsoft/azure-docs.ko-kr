---
title: Azure AD 앱에 대 한 고급 SAML 토큰 인증서 서명 옵션
description: Azure Active Directory에서 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션을 사용하는 방법을 알아봅니다.
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
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159202"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory의 갤러리 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션

현재 Azure AD(Azure Active Directory)는 Azure Active Directory 앱 갤러리에서 수천 개의 사전 통합 애플리케이션을 지원합니다. 500 이상의 응용 프로그램은 [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) 응용 프로그램과 같은 SAML ( [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) ) 2.0 프로토콜을 사용 하 여 Single Sign-On 지원 합니다. 고객이 SAML을 사용 하 여 Azure AD를 통해 응용 프로그램에 인증 하는 경우 Azure AD는 HTTP POST를 통해 응용 프로그램에 토큰을 보냅니다. 그런 다음 응용 프로그램은 사용자 이름 및 암호를 묻는 메시지를 표시 하는 대신 토큰을 검사 하 고 토큰을 사용 하 여 고객에 로그인 합니다. 이러한 SAML 토큰은 Azure AD 및 특정 표준 알고리즘에서 생성된 고유한 인증서로 서명됩니다.

Azure AD는 갤러리 애플리케이션의 기본 설정 중 일부를 사용합니다. 애플리케이션 요구 사항에 따라 기본값이 설정됩니다.

Azure AD에서 인증서 서명 옵션 및 인증서 서명 알고리즘을 설정할 수 있습니다.

## <a name="certificate-signing-options"></a>인증서 서명 옵션

Azure AD는 다음과 같이 세 가지 인증서 서명 옵션을 지원합니다.

* **SAML 어설션 서명**. 대부분의 갤러리 애플리케이션에 이 기본 옵션이 설정됩니다. 이 옵션을 선택 하면 IdP (Id 공급자) 인 Azure AD가 응용 프로그램의 [x.509](https://wikipedia.org/wiki/X.509) 인증서를 사용 하 여 SAML 어설션 및 인증서에 서명 합니다.

* **SAML 응답 서명**. 이 옵션을 선택 하는 경우 Azure AD는 응용 프로그램의 x.509 인증서를 사용 하 여 SAML 응답에 IdP 서명 합니다.

* **SAML 응답 및 어설션 서명**. 이 옵션을 선택 하는 경우 IdP로 Azure AD는 응용 프로그램의 x.509 인증서를 사용 하 여 전체 SAML 토큰에 서명 합니다.

## <a name="certificate-signing-algorithms"></a>인증서 서명 알고리즘

Azure AD는 SAML 응답에 서명 하는 두 가지 서명 알고리즘 또는 Sha (보안 해시 알고리즘)를 지원 합니다.

* **SHA-256**. Azure AD가 SAML 응답에 서명하기 위해 이 기본 알고리즘을 사용합니다. 최신 알고리즘 이며 s h a-1 보다 더 안전 합니다. 대부분의 애플리케이션에서 SHA-256 알고리즘을 지원합니다. 애플리케이션이 서명 알고리즘으로 SHA-1만 지원하는 경우 이를 변경할 수 있습니다. 그렇지 않으면 SAML 응답에 서명하는 데 SHA-256 알고리즘을 사용하는 것이 좋습니다.

* **SHA-1**. 이 알고리즘은 이전 버전이 며 SHA-256 보다 낮은 보안으로 취급 됩니다. 애플리케이션에서 이 서명 알고리즘만 지원하는 경우 **서명 알고리즘** 드롭다운 목록에서 이 옵션을 선택할 수 있습니다. 그러면 Azure AD에서 SHA-1 알고리즘으로 SAML 응답에 서명합니다.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>인증서 서명 옵션 및 서명 알고리즘 변경

응용 프로그램의 SAML 인증서 서명 옵션과 인증서 서명 알고리즘을 변경 하려면 해당 응용 프로그램을 선택 합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에서 계정에 로그인 합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 계정의 엔터프라이즈 응용 프로그램 목록이 표시 됩니다.
1. 응용 프로그램을 선택합니다. 응용 프로그램에 대 한 개요 페이지가 표시 됩니다.

   ![예: 응용 프로그램 개요 페이지](./media/certificate-signing-options/application-overview-page.png)

그런 다음 해당 응용 프로그램에 대 한 SAML 토큰에서 인증서 서명 옵션을 변경 합니다.

1. 응용 프로그램 개요 페이지의 왼쪽 창에서 **Single sign-on**을 선택 합니다.
1. **SAML 미리 보기를 사용 하 여 Single Sign-on 설정** 페이지가 표시 되 면 5 단계로 이동 합니다.
1. **Single Sign-On 방법 선택** 페이지가 표시 되지 않으면 **Single Sign-On 모드 변경** 을 선택 하 여 해당 페이지를 표시 합니다.
1. **Single Sign-On 방법 선택** 페이지에서 사용 가능한 경우 **SAML** 을 선택 합니다. ( **Saml** 을 사용할 수 없는 경우 응용 프로그램은 saml을 지원 하지 않으며,이 절차와 문서의 나머지 부분을 무시할 수 있습니다.)
1. **Saml 미리 보기를 사용 하 여 Single Sign-on 설정** 페이지에서 **saml 서명 인증서** 제목을 찾고 **편집** 아이콘 (연필)을 선택 합니다. **SAML 서명 인증서** 페이지가 표시 됩니다.

   ![예: SAML 서명 인증서 페이지](./media/certificate-signing-options/saml-signing-page.png)

1. **서명 옵션** 드롭다운 목록에서 **SAML 응답 서명**, **SAML 어설션 서명** 또는 **SAML 응답 및 어설션 서명**을 선택합니다. 이러한 옵션에 대 한 설명은이 문서의 앞부분에 나오는 [인증서 서명 옵션](#certificate-signing-options)에 나와 있습니다.
1. **서명 알고리즘** 드롭다운 목록에서 **SHA-1** 또는 **SHA-256**을 선택합니다. 이러한 옵션에 대 한 설명은이 문서의 앞부분에 나오는 [인증서 서명 알고리즘](#certificate-signing-algorithms) 섹션에 나와 있습니다.
1. 선택 사항에 만족 하는 경우 **저장** 을 선택 하 여 새 SAML 서명 인증서 설정을 적용 합니다. 그렇지 않으면 **X** 를 선택 하 여 변경 내용을 취소 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 앱 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML 기반 Single Sign-On 문제 해결](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
