---
title: Azure AD 앱에 대 한 암호 기반 Single Sign-On를 구성 하는 방법
description: Microsoft id 플랫폼 (Azure AD)에서 Azure AD 응용 프로그램에 대 한 SSO (암호 기반 Single Sign-On)를 구성 하는 방법
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: e04a3aab128bb8f0bdee01361bc0d09aad6ed2fb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049063"
---
# <a name="configure-password-based-single-sign-on"></a>암호 기반 Single Sign-On 구성

응용 프로그램 관리에 대 한 [빠른 시작 시리즈](view-applications-portal.md) 에서는 응용 프로그램에 대 한 IdP (id 공급자)로 Azure AD를 사용 하는 방법을 알아보았습니다. 빠른 시작 가이드에서는 SAML 기반 SSO를 설정 합니다. 또 다른 옵션은 암호 기반 SSO입니다. 이 문서에서는 암호 기반 SSO 옵션에 대해 자세히 설명 합니다. 

이 옵션은 HTML 로그인 페이지가 있는 모든 웹 사이트에 사용할 수 있습니다. 암호 기반 SSO는 암호 보관이 라고도 합니다. 암호 기반 SSO를 사용 하면 id 페더레이션을 지원 하지 않는 웹 응용 프로그램에 대 한 사용자 액세스 및 암호를 관리할 수 있습니다. 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유 해야 하는 경우에도 유용 합니다.

암호 기반 SSO는 응용 프로그램을 Azure AD에 신속 하 게 통합 하기 시작 하는 좋은 방법 이며 다음을 수행할 수 있습니다.

- 사용자 이름 및 암호를 안전 하 게 저장 하 고 재생 하 여 사용자에 대 한 Single Sign-On 사용

- 로그인하는 데 사용자 이름 및 암호 필드 이상을 필요로 하는 애플리케이션에 대해 여러 로그인 필드를 필요로 하는 애플리케이션 지원

- 사용자가 자격 증명을 입력할 때 [앱](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) 에서 사용자에 게 표시 되는 사용자 이름 및 암호 필드의 레이블 사용자 지정

- 사용자가 수동으로 입력 하는 기존 응용 프로그램 계정에 고유한 사용자 이름 및 암호를 제공할 수 있습니다.

- 비즈니스 그룹의 멤버가 [셀프 서비스 애플리케이션 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 기능을 사용하여 사용자에게 할당된 사용자 이름 및 암호를 지정하도록 허용

-   관리자가 자격 증명 업데이트 기능을 사용 하 여 응용 프로그램에 로그인 할 때 개인 또는 그룹에서 사용할 사용자 이름 및 암호를 지정할 수 있습니다. 

## <a name="before-you-begin"></a>시작하기 전에

IdP (Id 공급자)로 Azure AD를 사용 하 고 SSO (Single Sign-On)를 설정 하는 것은 사용 중인 응용 프로그램에 따라 단순 하거나 복잡할 수 있습니다. 일부 응용 프로그램은 몇 가지 작업 으로만 설정할 수 있습니다. 다른 경우에는 심층 구성이 필요 합니다. 신속 하 게 진행 하려면 응용 프로그램 관리에 대 한 [빠른 시작 시리즈](view-applications-portal.md) 를 살펴봅니다. 추가 중인 응용 프로그램이 간단한 경우에는이 문서를 읽을 필요가 없을 것입니다. 추가 중인 응용 프로그램에 사용자 지정 구성이 필요 하 고 암호 기반 SSO를 사용 해야 하는 경우이 문서를 참조 하세요.

> [!IMPORTANT] 
> **엔터프라이즈 응용**프로그램의 응용 프로그램을 탐색 하는 데에는 **Single sign-on** 옵션이 없는 몇 가지 시나리오가 있습니다. 
>
> **앱 등록** 를 사용 하 여 응용 프로그램을 등록 한 경우 Single Sign-On 기능은 기본적으로 Oidc OAuth를 사용 하도록 설정 됩니다. 이 경우 **Enterprise 응용 프로그램**에서 **Single sign-on** 옵션이 탐색에 표시 되지 않습니다. **앱 등록** 를 사용 하 여 사용자 지정 앱을 추가 하는 경우 매니페스트 파일의 옵션을 구성 합니다. 매니페스트 파일에 대 한 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)를 참조 하세요. SSO 표준에 대해 자세히 알아보려면 [Microsoft id 플랫폼을 사용한 인증 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)를 참조 하세요. 
>
> 응용 프로그램이 다른 테 넌 트에 호스트 되거나 사용자 계정에 필요한 권한 (전역 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 관리자 또는 서비스 주체의 소유자)이 없는 경우에는 탐색에서 **Single sign-on** 이 누락 되는 경우도 있습니다. 사용 권한은 **Single sign-on** 을 열 수는 있지만 저장할 수 없는 시나리오를 일으킬 수도 있습니다. Azure AD 관리 역할에 대 한 자세한 내용은 (를 참조 https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 하세요.


## <a name="basic-configuration"></a>기본 구성

[퀵 스타트 시리즈](view-applications-portal.md)에서는 Azure AD가 앱에 대 한 IdP (id 공급자)로 사용 되 고 있음을 알 수 있도록 테 넌 트에 앱을 추가 하는 방법을 알아보았습니다. 일부 앱은 이미 미리 구성 되어 있으며 Azure AD 갤러리에 표시 됩니다. 다른 앱은 갤러리에 없으며, 일반 앱을 만들어 수동으로 구성 해야 합니다. 앱에 따라 암호 기반 SSO 옵션을 사용 하지 못할 수도 있습니다. 앱에 대 한 Single Sign-On 페이지에 암호 기반 옵션 목록이 표시 되지 않는 경우 사용할 수 없습니다.

> [!IMPORTANT]
> 암호 기반 SSO에는 My Apps 브라우저 확장이 필요 합니다. 자세히 알아보려면 [내 앱 배포 계획](access-panel-deployment-plan.md)을 참조 하세요.

암호 기반 SSO에 대 한 구성 페이지는 단순 합니다. 앱에서 사용 하는 로그온 페이지의 URL만 포함 합니다. 이 문자열은 사용자 이름 입력 필드를 포함 하는 페이지 여야 합니다.

URL을 입력 한 후 **저장**을 선택 합니다. Azure AD는 사용자 이름 및 암호 입력 필드에 대 한 로그인 페이지의 HTML을 구문 분석 합니다. 시도가 성공 하면 완료 된 것입니다.
 
다음 단계는 [응용 프로그램에 사용자 또는 그룹을 할당](methods-for-assigning-users-and-groups.md)하는 것입니다. 사용자 및 그룹을 할당 한 후 사용자가 응용 프로그램에 로그인 할 때 사용할 자격 증명을 제공할 수 있습니다. **사용자 및 그룹**을 선택 하 고 사용자 또는 그룹의 행에 대 한 확인란을 선택한 다음 **자격 증명 업데이트**를 선택 합니다. 마지막으로 사용자 또는 그룹에 사용할 사용자 이름 및 암호를 입력 합니다. 이렇게 하지 않으면 시작할 때 자격 증명을 입력 하 라는 메시지가 사용자에 게 표시 됩니다.
 

## <a name="manual-configuration"></a>수동 구성

Azure AD의 구문 분석에 실패 하는 경우 로그인을 수동으로 구성할 수 있습니다.

1. ** \<application name> 구성**에서 ** \<application name> 암호 Single sign-on 설정 구성** 을 선택 하 여 로그온 **구성** 페이지를 표시 합니다. 

2. **로그인 필드 수동 검색**을 선택 합니다. 로그인 필드의 수동 검색에 대해 설명 하는 추가 지침이 표시 됩니다.

   ![암호 기반 Single Sign-On 수동 구성](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **캡처 로그인 필드**를 선택 합니다. 새 탭에서 캡처 상태 페이지가 열리고 **현재 진행 중인 메시지 메타 데이터 캡처가**표시 됩니다.

4. **내 앱 확장 필요** 상자가 새 탭에 표시 되 면 **지금 설치** 를 선택 하 여 **내 앱 보안 로그인 확장** 브라우저 확장을 설치 합니다. 브라우저 확장에는 Microsoft Edge, Chrome 또는 Firefox가 필요 합니다. 그런 다음, 확장을 설치, 시작 및 사용 하도록 설정 하 고 캡처 상태 페이지를 새로 고칩니다.

   그러면 브라우저 확장에서 입력 한 URL을 표시 하는 다른 탭이 열립니다.
5. 입력 한 URL이 있는 탭에서 로그인 프로세스를 진행 합니다. 사용자 이름 및 암호 필드를 입력 하 고 로그인을 시도 합니다. (올바른 암호를 제공할 필요가 없습니다.)

   캡처된 로그인 필드를 저장 하 라는 메시지가 표시 됩니다.
6. **확인**을 선택합니다. 브라우저 확장이 **응용 프로그램에 대해 메시지 메타 데이터가 업데이트**된 상태에서 캡처 상태 페이지를 업데이트 합니다. 브라우저 탭이 닫힙니다.

7. Azure AD **로그온 구성** 페이지에서 **확인을 선택 하면 앱에 성공적으로 로그인 할 수**있습니다.

8. **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
