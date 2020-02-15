---
title: 로그인 한 후 앱 페이지에 오류 메시지가 표시 됩니다. Microsoft Docs
description: 앱에서 오류 메시지를 반환 하는 경우 Azure AD 로그인 문제를 해결 하는 방법입니다.
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185487"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>사용자가 로그인 하면 앱 페이지에서 오류 메시지를 표시 합니다.

이 시나리오에서 Azure Active Directory (Azure AD)는에서 사용자에 게 서명 합니다. 그러나 응용 프로그램은 오류 메시지를 표시 하 고 사용자가 로그인 흐름을 완료 하지 않도록 합니다. 문제는 앱이 Azure AD에서 발급 한 응답을 수락 하지 않았기 때문입니다.

앱이 Azure AD의 응답을 수락 하지 못한 몇 가지 원인이 있습니다. 오류 메시지가 응답에서 누락 된 항목을 명확 하 게 식별 하지 못하는 경우 다음을 시도 합니다.

-   앱이 Azure AD 갤러리가 면 [AZURE ad의 응용 프로그램에 대 한 SAML 기반 Single Sign-On를 디버그 하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)의 단계를 수행 했는지 확인 합니다.

-   [Fiddler](https://www.telerik.com/fiddler) 와 같은 도구를 사용 하 여 SAML 요청, 응답 및 토큰을 캡처합니다.

-   앱 공급 업체에 SAML 응답을 보내고 누락 된 항목을 요청 합니다.

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 응답에 특성이 없습니다.

Azure ad 응답으로 보낼 Azure AD 구성의 특성을 추가 하려면 다음 단계를 수행 합니다.

1. [**Azure Portal**](https://portal.azure.com/) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. Single Sign-On에 대해 구성 하려는 응용 프로그램을 선택 합니다.

7. 앱이 로드 된 후 탐색 창에서 **Single sign-on** 을 선택 합니다.

8. **사용자 특성** 섹션에서 **다른 모든 사용자 특성 보기 및 편집**을 선택 합니다. 여기에서 사용자가 로그인 할 때 SAML 토큰에서 앱에 보낼 특성을 변경할 수 있습니다.

   특성을 추가하려면:

   1. **특성 추가**를 선택합니다. **이름을**입력 하 고 드롭다운 목록에서 **값** 을 선택 합니다.

   1.  **저장**을 선택합니다. 테이블에 새 특성이 표시 됩니다.

9. 구성을 저장합니다.

   다음에 사용자가 앱에 로그인 할 때 Azure AD는 SAML 응답에 새 특성을 보냅니다.

## <a name="the-app-doesnt-identify-the-user"></a>앱에서 사용자를 식별 하지 않음

SAML 응답에 역할 같은 특성이 없으므로 앱에 로그인 하지 못합니다. 또는 응용 프로그램에 **NameID** (사용자 식별자) 특성에 대해 다른 형식 또는 값이 필요 하기 때문에 실패 합니다.

앱에서 사용자를 만들고 유지 관리 하 고 제거 하기 위해 [AZURE AD 자동 사용자 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 을 사용 하는 경우 사용자가 SaaS 앱에 프로 비전 되었는지 확인 합니다. 자세한 내용은 [사용자가 AZURE AD 갤러리 응용 프로그램에 프로 비전 되지 않음](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)을 참조 하세요.

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Azure AD 앱 구성에 특성 추가

사용자 ID 값을 변경하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램 목록**의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. SSO에 대해 구성 하려는 앱을 선택 합니다.

7. 앱이 로드 된 후 탐색 창에서 **Single sign-on** 을 선택 합니다.

8. 사용자 **특성**의 사용자 **식별자** 드롭다운 목록에서 사용자의 고유 식별자를 선택 합니다.

## <a name="change-the-nameid-format"></a>NameID 형식 변경

응용 프로그램에서 **nameid** (사용자 식별자) 특성에 다른 형식을 사용 해야 하는 경우 Nameid를 [편집](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) 하 여 nameid 형식을 변경 하는 것을 참조 하세요.

Azure AD는 선택한 값 또는 SAML AuthRequest에서 앱이 요청한 형식을 기반으로 **NameID** 특성 (사용자 식별자)의 형식을 선택 합니다. 자세한 내용은 [Single SIGN-ON SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)의 "NameIDPolicy" 섹션을 참조 하세요.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>앱에서 SAML 응답에 다른 서명 방법이 필요 합니다.

Azure AD에서 디지털 서명 하는 SAML 토큰의 일부를 변경 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.

   > [!NOTE]
   > 원하는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록의**맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. Single Sign-On에 대해 구성 하려는 응용 프로그램을 선택 합니다.

7. 응용 프로그램이 로드 되 면 탐색 창에서 **Single sign-on** 을 선택 합니다.

8. **SAML 서명 인증서**아래에서 **고급 인증서 서명 설정 표시**를 선택 합니다.

9. 다음 옵션 중에서 앱에 필요한 **서명 옵션** 을 선택 합니다.

   * **SAML 응답 서명**
   * **SAML 응답 및 어설션 서명**
   * **SAML 어설션 서명**

   사용자가 다음에 앱에 로그인 할 때 Azure AD는 사용자가 선택한 SAML 응답의 일부에 서명 합니다.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>앱에 SHA-1 서명 알고리즘이 필요 합니다.

기본적으로 Azure AD는 가장 안전한 알고리즘을 사용 하 여 SAML 토큰에 서명 합니다. 응용 프로그램에 s h a-1이 필요한 경우를 제외 하 고 서명 알고리즘을 *s h a-1* 로 변경 하지 않는 것이 좋습니다.

서명 알고리즘을 변경하려면 아래 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/) 을 열고 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

2. 왼쪽의 탐색 창 맨 위에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.

3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.

4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.

5. 응용 프로그램 목록을 보려면 **모든 응용 프로그램** 을 선택 합니다.

   > [!NOTE]
   > 원하는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록의**맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.

6. Single Sign-On에 대해 구성 하려는 앱을 선택 합니다.

7. 앱이 로드 되 면 앱의 왼쪽에 있는 탐색 창에서 **Single sign-on** 을 선택 합니다.

8. **SAML 서명 인증서**아래에서 **고급 인증서 서명 설정 표시**를 선택 합니다.

9. **서명 알고리즘**으로 **SHA-1** 을 선택 합니다.

   다음에 사용자가 앱에 로그인 할 때 Azure AD는 SHA-1 알고리즘을 사용 하 여 SAML 토큰에 서명 합니다.

## <a name="next-steps"></a>다음 단계
[AZURE AD의 응용 프로그램에 대 한 SAML 기반 Single Sign-On를 디버그 하는 방법](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)입니다.
