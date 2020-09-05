---
title: Azure Active Directory에서 연결 된 로그온 구성
description: Azure AD에서 연결 된 로그온을 구성 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: bad4fe7f0cf090e5d61506e775fccf677df45fa5
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641979"
---
# <a name="configure-linked-sign-on"></a>연결된 로그온 구성

응용 프로그램 관리에 대 한 [빠른 시작 시리즈](view-applications-portal.md) 에서는 응용 프로그램에 대 한 IdP (id 공급자)로 Azure AD를 사용 하는 방법을 알아보았습니다. 빠른 시작 가이드에서는 SAML 기반 SSO를 설정 합니다. 다른 옵션을 **연결**합니다. 이 문서에서는 연결 된 옵션에 대해 자세히 설명 합니다.

**연결** 된 옵션을 사용 하면 사용자가 조직의 [내 앱](https://myapps.microsoft.com/) 또는 Office 365 포털에서 앱을 선택할 때 대상 위치를 구성할 수 있습니다.

링크 옵션이 중요 한 몇 가지 일반적인 시나리오는 다음과 같습니다.
- Active Directory Federation Services (AD FS)와 같이 현재 페더레이션을 사용 하는 사용자 지정 웹 응용 프로그램에 대 한 링크를 추가 합니다.
- 특정 SharePoint 페이지 또는 사용자의 액세스 패널에 표시 하려는 다른 웹 페이지에 딥 링크를 추가 합니다.
- 인증을 요구 하지 않는 앱에 대 한 링크를 추가 합니다. 
 
 **연결** 된 옵션은 Azure AD 자격 증명을 통해 로그온 기능을 제공 하지 않습니다. 그러나 **엔터프라이즈 응용 프로그램**의 다른 기능 중 일부를 계속 사용할 수 있습니다. 예를 들어 감사 로그를 사용 하 고 사용자 지정 로고 및 앱 이름을 추가할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

신속 하 게 진행 하려면 응용 프로그램 관리에 대 한 [빠른 시작 시리즈](view-applications-portal.md) 를 살펴봅니다. Single Sign-On를 구성 하는 빠른 시작에서 **연결 된** 옵션도 찾을 수 있습니다. 

**연결** 된 옵션은 Azure AD를 통해 로그인 기능을 제공 하지 않습니다. 옵션은 사용자가 앱에서 앱을 선택 [Microsoft 365 하거나 앱](https://myapps.microsoft.com/) 시작 관리자를 선택 하는 경우 사용자가 전송 되는 위치를 설정 합니다.

> [!IMPORTANT] 
> **엔터프라이즈 응용**프로그램의 응용 프로그램을 탐색 하는 데에는 **Single sign-on** 옵션이 없는 몇 가지 시나리오가 있습니다. 
>
> **앱 등록** 를 사용 하 여 응용 프로그램을 등록 한 경우 Single Sign-On 기능은 기본적으로 Oidc OAuth를 사용 하도록 설정 됩니다. 이 경우 **Enterprise 응용 프로그램**에서 **Single sign-on** 옵션이 탐색에 표시 되지 않습니다. **앱 등록** 를 사용 하 여 사용자 지정 앱을 추가 하는 경우 매니페스트 파일의 옵션을 구성 합니다. 매니페스트 파일에 대 한 자세한 내용은 [Azure Active Directory 응용 프로그램 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)를 참조 하세요. SSO 표준에 대해 자세히 알아보려면 [Microsoft id 플랫폼을 사용한 인증 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)를 참조 하세요. 
>
> 응용 프로그램이 다른 테 넌 트에 호스트 되거나 사용자 계정에 필요한 권한 (전역 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 관리자 또는 서비스 주체의 소유자)이 없는 경우에는 탐색에서 **Single sign-on** 이 누락 되는 경우도 있습니다. 사용 권한은 **Single sign-on** 을 열 수는 있지만 저장할 수 없는 시나리오를 일으킬 수도 있습니다. Azure AD 관리 역할에 대 한 자세한 내용은 (를 참조 https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 하세요.

### <a name="configure-link"></a>링크 구성

앱에 대 한 링크를 설정 하려면 **Single sign-on** 페이지에서 **연결** 됨을 선택 합니다. 그런 다음 링크를 입력 하 고 **저장**을 선택 합니다. 이러한 옵션을 찾을 수 있는 위치에 대 한 미리 알림이 필요 한가요? [퀵 스타트 시리즈](view-applications-portal.md)를 확인 합니다.
 
앱을 구성한 후에는 사용자 및 그룹을 할당 합니다. 사용자를 할당 하는 경우 응용 프로그램이 [내 앱](https://myapps.microsoft.com/) 또는 Microsoft 365 앱 시작 관리자에 표시 되는 시간을 제어할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
