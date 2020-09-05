---
title: 응용 프로그램 프록시 앱에 Single Sign-On를 구성 하는 방법
description: 애플리케이션 프록시 애플리케이션에 대해 Single Sign-On을 신속하게 구성하는 방법
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/12/2019
ms.author: kenwith
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54673e0018f83024d1c01599d0096c71b6b1d0ae
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642132"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션에 대해 Single Sign-On을 구성하는 방법

SSO(Single Sign-On)를 사용하면 사용자가 여러 번 인증하지 않고도 애플리케이션에 액세스할 수 있습니다. Azure Active Directory에 대해 클라우드에서 단일 인증을 허용하고 서비스 또는 커넥터가 사용자를 가장하여 애플리케이션에서 추가 인증 질문을 완료할 수 있습니다.

## <a name="how-to-configure-single-sign-on"></a>Single Sign-On을 구성하는 방법
SSO를 구성하려면 먼저 애플리케이션이 Azure Active Directory를 통해 사전 인증할 수 있도록 구성되어 있는지 확인합니다. 이 구성을 수행 하려면 **Azure Active Directory**  - &gt; **엔터프라이즈 응용** 프로그램  - &gt; **모든**  - &gt; ** - &gt; **응용 프로그램 응용 프로그램 프록시로 이동 합니다. 이 페이지에서 "사전 인증" 필드가 표시되고 "Azure Active Directory"로 설정되어 있는지 확인합니다. 

사전 인증 방법에 대한 자세한 내용은 [앱 게시 문서](application-proxy-add-on-premises-application.md)의 4단계를 참조하세요.

   ![Azure Portal의 사전 인증 방법](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>애플리케이션 프록시 애플리케이션에 대한 Single Sign-On 모드 구성
특정 유형의 Single Sign-On을 구성합니다. 로그온 방법은 백 엔드 애플리케이션에서 사용하는 인증 유형에 따라 분류됩니다. 앱 프록시 애플리케이션에서 지원하는 세 가지 유형의 로그온은 다음과 같습니다.

-   **암호 기반 로그온**: 암호 기반 로그온은 사용자 이름과 암호 필드를 사용하여 로그온하는 모든 애플리케이션에 사용할 수 있습니다. 구성 단계는 [Azure AD 갤러리 애플리케이션에 대해 암호 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)에 나와 있습니다.

-   **Windows 통합 인증**: IWA(Windows 통합 인증)를 사용하는 애플리케이션의 경우 KCD(Kerberos 제한 위임)를 통해 Single Sign-On을 사용할 수 있습니다. 이 방법은 Active Directory에서 애플리케이션 프록시 커넥터에 권한을 부여하여 사용자를 가장하고 토큰을 대신 보내고 받습니다. KCD 구성에 대한 자세한 내용은 [Single Sign-on과 KCD 설명서](application-proxy-configure-single-sign-on-with-kcd.md)를 참조하세요.

-   **헤더 기반 로그온**: 헤더 기반 로그온은 파트너 관계를 통해 활성화되며 몇 가지 구성이 추가로 필요합니다. 인증에 헤더를 사용하는 애플리케이션에 대해 Single Sign-On을 구성하기 위한 파트너 관계 및 단계별 지침은 [Azure AD용 PingAccess 설명서](application-proxy-configure-single-sign-on-with-ping-access.md)를 참조하세요.

-   **Saml Single Sign-On**: saml Single Sign-On를 사용 하 여 azure ad는 사용자의 azure ad 계정을 사용 하 여 응용 프로그램에 인증 합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. SAML 기반 Single Sign-On을 사용하면 SAML 클레임에서 정의하는 규칙에 따라 사용자를 특정 애플리케이션 역할에 매핑할 수 있습니다. SAML Single Sign-On 설정에 대 한 자세한 내용은 [응용 프로그램 프록시를 사용 하는 Single Sign-On saml](application-proxy-configure-single-sign-on-on-premises-apps.md)

이러한 각 옵션은 "엔터프라이즈 애플리케이션"에서 애플리케이션으로 이동하여 왼쪽 메뉴에서 **Single Sign-On** 페이지를 열면 찾을 수 있습니다. 이전 포털에서 응용 프로그램을 만든 경우 이러한 옵션이 모두 표시 되지 않을 수 있습니다.

이 페이지에는 추가 로그온 옵션인 연결된 로그온이 있습니다. 이 옵션은 애플리케이션 프록시에서도 지원됩니다. 그러나 이 옵션은 애플리케이션에 Single Sign-On을 추가하지 않습니다. 즉, 애플리케이션에 이미 Active Directory Federation Services와 같은 다른 서비스를 사용하여 Single Sign-On이 구현되어 있을 수 있습니다. 

이 옵션을 사용하면 관리자가 애플리케이션에 액세스할 때 사용자에게 제일 먼저 표시되는 애플리케이션에 대한 링크를 만들 수 있습니다. 예를 들어 Active Directory Federation Services 2.0를 사용 하 여 사용자를 인증 하도록 구성 된 응용 프로그램이 있는 경우 관리자는 "연결 된 로그온" 옵션을 사용 하 여 앱에 대 한 링크를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [애플리케이션 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정](application-proxy-configure-single-sign-on-password-vaulting.md)
- [애플리케이션 프록시를 사용하는 Single Sign-On에 대한 Kerberos 제한된 위임](application-proxy-configure-single-sign-on-with-kcd.md)
- [애플리케이션 프록시를 사용하는 Single Sign-On에 대한 헤더 기반 인증](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [응용 프로그램 프록시를 사용 하는 Single Sign-On에 대 한 SAML](application-proxy-configure-single-sign-on-on-premises-apps.md)
