---
title: Azure AD 내 앱에서 응용 프로그램에 로그인 하는 문제 해결
description: Azure AD 내 앱에서 응용 프로그램에 로그인 하는 문제 해결
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 472ab82e6941be5c1485773b1115d91eda701666
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231183"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Azure AD 내 앱에서 응용 프로그램에 로그인 하는 문제 해결

내 앱은 azure ad (Azure Active Directory)에서 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자가 액세스 권한을 부여한 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 하는 웹 기반 포털입니다. 

앱에 대 한 id 공급자로 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [AZURE ad의 응용 프로그램 관리 란?](what-is-application-management.md)을 참조 하세요. 신속 하 게 속도를 높이려면 [응용 프로그램 관리에서 빠른 시작 시리즈](view-applications-portal.md)를 확인 하세요.

이러한 애플리케이션은 Azure AD 포털에서 사용자를 대신하여 구성됩니다. 응용 프로그램을 올바르게 구성 하 고 사용자 또는 사용자가 구성원으로 속한 그룹에 할당 하 여 내 앱에서 응용 프로그램을 확인 해야 합니다. 

사용자가 볼 수 있는 앱의 종류는 다음과 같은 범주로 분류됩니다.
-   Microsoft 365 및 Office 365 응용 프로그램
-   페더레이션 기반 SSO로 구성된 Microsoft 및 타사 애플리케이션
-   암호 기반 SSO 애플리케이션
-   기존 SSO 솔루션을 사용한 애플리케이션

앱이 나타나거나 표시 되지 않는 경우를 확인 하는 몇 가지 사항은 다음과 같습니다.
- 앱이 Azure AD에 추가 되었는지 확인 하 고 사용자가 할당 되었는지 확인 합니다. 자세히 알아보려면 [응용 프로그램 관리에 대 한 빠른 시작 시리즈](view-applications-portal.md)를 참조 하세요.
- 앱을 최근에 추가한 경우 사용자가 다시 로그 아웃 했다가 다시 로그인 합니다. 
- 앱이 Office와 같은 라이선스를 요구 하는 경우 사용자에 게 적절 한 라이선스가 할당 되었는지 확인 합니다.
- 라이선스 변경에 걸리는 시간은 그룹의 크기와 복잡도에 따라 달라질 수 있습니다.

## <a name="general-issues-to-check-first"></a>먼저 확인해야 할 일반적인 문제

-   웹 브라우저가 요구 사항을 충족 하는지 확인 하세요. [내 앱 지원 브라우저](../user-help/my-apps-portal-end-user-access.md)를 참조 하세요.
-   사용자의 브라우저에서 해당 **신뢰할 수 있는 사이트**에 애플리케이션의 URL을 추가했는지 확인합니다.
-   애플리케이션이 올바르게 **구성**되었는지 확인합니다.
-   사용자의 계정이 로그인에 대해 **활성화**되었는지 확인합니다.
-   사용자의 계정이 **잠겨 있지 않는지** 확인합니다.
-   사용자의 **암호가 만료되거나 기억하지 못하는지** 확인합니다.
-   **Multi-Factor Authentication**에서 사용자 액세스를 차단하지 않는지 확인합니다.
-   **조건부 액세스 정책** 또는 **ID 보호** 정책이 사용자 액세스를 차단하지 않는지 확인합니다.
-   사용자의 **인증 연락처 정보**를 최신으로 유지하여 Multi-Factor Authentication 또는 조건부 액세스 정책을 적용할 수 있도록 해야 합니다.
-   브라우저의 쿠키를 지우고 다시 로그인하려고 시도할 수도 있는지 확인합니다.

## <a name="problems-with-the-users-account"></a>사용자의 계정과 관련된 문제
사용자 계정 문제로 인해 내 앱에 대 한 액세스를 차단할 수 있습니다. 다음 몇 가지 방법으로 사용자 및 해당 계정 설정과 관련된 문제를 해결할 수 있습니다.
-   [Azure Active Directory에 사용자의 계정이 존재하는지 확인](#check-if-a-user-account-exists-in-azure-active-directory)
-   [사용자의 계정 상태 확인](#check-a-users-account-status)
-   [사용자 암호 다시 설정](#reset-a-users-password)
-   [셀프 서비스 암호 재설정 사용](#enable-self-service-password-reset)
-   [사용자의 Multi-Factor Authentication 상태 확인](#check-a-users-multi-factor-authentication-status)
-   [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)
-   [사용자의 그룹 구성원 자격 확인](#check-a-users-group-memberships)
-   [사용자의 할당된 라이선스 확인](#check-a-users-assigned-licenses)
-   [사용자에 게 라이선스 할당](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Azure Active Directory에 사용자의 계정이 존재하는지 확인
사용자의 계정이 있는지를 확인하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  사용자 개체의 속성이 예상한 대로 표시되고 데이터가 누락되지 않았는지 확인합니다.

### <a name="check-a-users-account-status"></a>사용자의 계정 상태 확인
사용자의 계정 상태를 확인하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  **프로필**을 선택 합니다.
8.  **설정** 아래에서 **로그인 차단**이 **아니오**로 설정되어야 합니다.

### <a name="reset-a-users-password"></a>사용자의 암호 다시 설정
사용자의 암호를 다시 설정하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  사용자 창 맨 위에 있는 **암호 재설정** 단추를 선택 합니다.
8.  표시 되는 **암호 다시 설정** 창에서 **암호 재설정** 단추를 선택 합니다.
9.  사용자를 위한 **임시 암호** 또는 **새 암호 입력**을 복사합니다.
10. 사용자에게 이 새 암호를 전달하고 다음 번에 Azure Active Directory에 로그인하는 동안 이 암호를 변경하도록 해야 합니다.

### <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용
셀프 서비스 암호 재설정을 사용하려면 아래 배포 단계를 따르세요.
-   [사용자가 Azure Active Directory 암호를 재설정할 수 있도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)
-   [사용자가 Active Directory 온-프레미스 암호를 재설정하거나 변경하도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>사용자의 Multi-Factor Authentication 상태 확인
사용자의 Multi-Factor Authentication 상태를 확인하려면 아래 단계를 수행합니다.
1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2. 왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. 탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5. **모든 사용자**를 선택합니다.
6. 창 맨 위에 있는 **Multi-Factor Authentication** 단추를 선택 합니다.
7. **Multi-Factor Authentication 관리 포털이** 로드 되 면 **사용자** 탭에 있는지 확인 합니다.
8. 검색, 필터링 또는 정렬을 사용하여 사용자 목록에서 사용자를 찾습니다.
9. 사용자 목록에서 사용자를 선택하고 원하는 대로 Multi-Factor Authentication을 **사용**, **사용 안 함** 또는 **적용**합니다.
   >[!NOTE]
   >사용자가 **강제된** 상태인 경우 일시적으로 **사용 안 함**으로 설정하여 해당 계정으로 다시 돌아올 수 있도록 할 수 있습니다. 다시 돌아오면 해당 상태를 **사용**으로 변경하여 다음 번에 로그인하는 도중 해당 연락처 정보를 다시 등록할 수 있습니다. 또는 [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)에 있는 단계를 수행하여 이 데이터를 확인하거나 설정할 수 있습니다.

### <a name="check-a-users-authentication-contact-info"></a>사용자의 인증 연락처 정보 확인
Multi-Factor Authentication, 조건부 액세스, ID 보호 및 암호 재설정에 사용되는 사용자의 인증 연락처 정보를 확인하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  **프로필**을 선택 합니다.
8.  **인증 연락처 정보**까지 스크롤합니다.
9.  필요에 따라 사용자 및 업데이트에 등록된 데이터를 **검토**합니다.

### <a name="check-a-users-group-memberships"></a>사용자의 그룹 구성원 자격 확인
사용자의 그룹 구성원 자격을 확인하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  **그룹** 을 선택 하 여 사용자가 구성원으로 속해 있는 그룹을 확인 합니다.

### <a name="check-a-users-assigned-licenses"></a>사용자의 할당된 라이선스 확인
사용자의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  **라이선스** 를 선택 하 여 현재 사용자에 게 할당 된 라이선스를 확인 합니다.

### <a name="assign-a-user-a-license"></a>사용자에게 라이선스 할당 
사용자에게 라이선스를 할당하려면 다음 단계를 수행합니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **모든 사용자**를 선택합니다.
6.  관심이 있는 사용자를 **검색** 하 고 선택할 **행을 선택** 합니다.
7.  **라이선스** 를 선택 하 여 현재 사용자에 게 할당 된 라이선스를 확인 합니다.
8.  **할당** 단추를 선택 합니다.
9.  사용 가능한 제품 목록에서 **하나 이상의 제품**을 선택합니다.
10. **선택 사항** 세부적으로 제품을 할당 하려면 **할당 옵션** 항목을 선택 합니다. **확인**을 선택합니다.
11. **할당** 단추를 선택 하 여이 사용자에 게 이러한 라이선스를 할당 합니다.

## <a name="troubleshooting-deep-links"></a>딥 링크 문제 해결
딥 링크 또는 사용자 액세스 URL은 사용자가 해당 브라우저 URL 막대에서 직접 암호 SSO 애플리케이션에 액세스하는 데 사용할 수 있는 링크입니다. 이 링크로 이동 하면 사용자는 먼저 내 앱으로 이동 하지 않고도 응용 프로그램에 자동으로 로그인 됩니다. 이 링크는 사용자가 Office 365 응용 프로그램 시작 관리자에서 이러한 응용 프로그램에 액세스 하는 데 사용 하는 링크와 동일 합니다.

### <a name="checking-the-deep-link"></a>딥 링크를 확인 하는 중

올바른 딥 링크가 있는지 확인 하려면 다음 단계를 수행 합니다.
1. [**Azure Portal**](https://portal.azure.com/) 을 열고 **전역 관리자** 또는 공동 관리자 권한으로 로그인 **합니다.**
2. 왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
5. 모든 **응용 프로그램을 선택 하** 여 모든 응용 프로그램의 목록을 봅니다.
   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.
6. [**Azure Portal**](https://portal.azure.com/) 을 열고 **전역 관리자** 또는 공동 관리자 권한으로 로그인 **합니다.**
7. 왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
8. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
9. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
10. 모든 **응용 프로그램을 선택 하** 여 모든 응용 프로그램의 목록을 봅니다.
    * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.
11. 딥 링크를 확인 하려는 응용 프로그램을 선택 합니다.
12. **사용자 액세스 URL** 레이블을 찾습니다. 딥 링크가이 URL과 일치 해야 합니다.

## <a name="contact-support"></a>지원에 문의
사용 가능한 경우 다음 정보를 사용 하 여 지원 티켓을 엽니다.
-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   오류가 발생하는 동안 표준 시간대 및 시간/기간
-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
- [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
