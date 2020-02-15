---
title: 인증 세션 관리 구성-Azure Active Directory
description: 사용자 로그인 빈도 및 브라우저 세션 지 속성을 포함 하 여 Azure AD 인증 세션 구성을 사용자 지정 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb094d04a7210d76a98f3e47af750e49b617e493
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77195065"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>조건부 액세스를 사용 하 여 인증 세션 관리 구성

복잡 한 배포에서는 조직에서 인증 세션을 제한 해야 할 수 있습니다. 일부 시나리오에는 다음이 포함 될 수 있습니다.

* 관리 되지 않는 장치 또는 공유 장치에서 리소스 액세스
* 외부 네트워크의 중요 한 정보에 대 한 액세스
* 높은 영향 사용자
* 중요 업무용 응용 프로그램

조건부 액세스 제어를 사용 하면 모든 사용자에 게 영향을 주지 않고 조직 내의 특정 사용 사례를 대상으로 하는 정책을 만들 수 있습니다.

정책을 구성 하는 방법에 대 한 세부 정보를 살펴보기 전에 기본 구성을 검토해 보겠습니다.

## <a name="user-sign-in-frequency"></a>사용자 로그인 빈도

로그인 빈도는 리소스에 액세스 하려고 할 때 사용자에 게 다시 로그인 하 라는 메시지가 표시 되는 기간을 정의 합니다.

사용자 로그인 빈도에 대 한 Azure Active Directory (Azure AD) 기본 구성은 90 일의 롤링 기간입니다. 사용자에 게 자격 증명을 요청 하는 것이 중요 한 것 처럼 보일 수 있지만,이 경우에는 사용자가 생각 하지 않고 자격 증명을 입력 하도록 학습 한 사용자가 실수로 악성 자격 증명 프롬프트에 해당 자격 증명을 제공할 수 있습니다.

사용자에 게 다시 로그인 하 라는 메시지가 표시 되는 것을 오류가 심각한 증가 수 있습니다. 실제로 IT 정책 위반으로 인해 세션이 해지 됩니다. 암호 변경, incompliant 장치 또는 계정 비활성화를 비롯 한 몇 가지 예가 있습니다. [PowerShell을 사용 하 여 사용자 세션](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)을 명시적으로 해지할 수도 있습니다. Azure AD 기본 구성은 "세션의 보안 상태가 변경 되지 않은 경우 사용자에 게 자격 증명을 제공 하지 않습니다." 라는 메시지가 표시 됩니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱과 함께 작동 합니다. 다음 웹 응용 프로그램을 포함 하 여 Windows, Mac 및 모바일 용 Microsoft 네이티브 앱 대부분은 설정을 준수 합니다.

- Word, Excel, PowerPoint Online
- OneNote 온라인
- Office.com
- O365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

### <a name="user-sign-in-frequency-and-device-identities"></a>사용자 로그인 빈도 및 장치 id

Azure AD 조인, 하이브리드 Azure AD 조인 또는 Azure AD 등록 장치를 사용 하는 경우 사용자가 장치를 잠금 해제 하거나 대화형으로 로그인 하는 경우이 이벤트는 로그인 빈도 정책도 충족 합니다. 다음 두 예제에서 사용자 로그인 빈도는 1 시간으로 설정 됩니다.

예제 1:

- 00:00에서 사용자는 Windows 10 Azure AD 조인 장치에 로그인 하 여 SharePoint Online에 저장 된 문서에서 작업을 시작 합니다.
- 사용자는 장치에서 한 시간 동안 동일한 문서에 대해 작업을 계속 합니다.
- 01:00에서 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 사용자에 게 다시 로그인 하 라는 메시지가 표시 됩니다.

예제 2:

- 00:00에서 사용자는 Windows 10 Azure AD 조인 장치에 로그인 하 여 SharePoint Online에 저장 된 문서에서 작업을 시작 합니다.
- 00:30에서는 사용자가 장치를 시작 하 고 잠금을 해제 합니다.
- 00:45에서 사용자는 자신의 중단에서 반환 하 고 장치의 잠금을 해제 합니다.
- 01:45에서 마지막 로그인이 00:45에 발생 한 이후 관리자가 구성한 조건부 액세스 정책의 로그인 빈도 요구 사항에 따라 다시 로그인 하 라는 메시지가 사용자에 게 표시 됩니다.

## <a name="persistence-of-browsing-sessions"></a>검색 세션의 지 속성

영구 브라우저 세션을 사용 하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다.

브라우저 세션 지 속성의 Azure AD 기본값은 개인 장치의 사용자가 "로그인 상태 유지"를 표시 하 여 세션을 유지할지 여부를 선택할 수 있도록 허용 합니다. 인증 성공 후 확인. [AD FS Single Sign-on 설정](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)문서에 있는 지침을 사용 하 여 AD FS에서 브라우저 지 속성을 구성 하는 경우 해당 정책을 준수 하 고 Azure AD 세션도 유지 합니다. 또한 테 넌 트의 사용자에 게 "로그인 상태 유지"가 표시 되는지 여부를 구성할 수 있습니다. [AZURE AD 로그인 페이지 사용자 지정](../fundamentals/customize-branding.md)문서에 있는 지침을 사용 하 Azure Portal의 회사 브랜딩 창에서 적절 한 설정을 변경 하 여 프롬프트를 표시 합니다.

## <a name="configuring-authentication-session-controls"></a>인증 세션 컨트롤 구성

조건부 액세스는 Azure AD Premium 기능이 며 프리미엄 라이선스가 필요 합니다. 조건부 액세스에 대해 자세히 알아보려면 [Azure Active Directory의 조건부 액세스 란?](overview.md#license-requirements) 을 참조 하세요.

> [!WARNING]
> 현재 공개 미리 보기로 제공 되는 [구성 가능한 토큰 수명](../develop/active-directory-configurable-token-lifetimes.md) 기능을 사용 하는 경우 동일한 사용자 또는 앱 조합에 대해 두 개의 다른 정책 (이 기능을 사용 하는 경우, 다른 하나는 구성 가능한 토큰 수명 기능 포함)을 만들 수 없습니다. Microsoft는 2020 년 5 월 1 일에 구성 가능한 토큰 수명 기능을 사용 중지 하 고 조건부 액세스 인증 세션 관리 기능으로 바꿀 계획입니다.  

### <a name="policy-1-sign-in-frequency-control"></a>정책 1: 로그인 빈도 제어

1. 새 정책 만들기
1. 대상 클라우드 앱을 포함 하 여 고객 환경에 필요한 모든 조건을 선택 합니다.

   > [!NOTE]
   > 최상의 사용자 환경을 위해 Exchange Online, SharePoint Online 등의 주요 Microsoft Office 앱에 대해 동일한 인증 프롬프트 빈도를 설정 하는 것이 좋습니다.

1. **액세스 제어** > **세션** 으로 이동 하 고 **로그인 빈도** 를 클릭 합니다.
1. 첫 번째 텍스트 상자에 날짜 및 시간에 대 한 필수 값을 입력 합니다.
1. 드롭다운에서 **시간** 또는 **일** 값을 선택 합니다.
1. 정책 저장

![로그인 빈도로 구성 된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Azure AD에서 등록 된 Windows 장치에는 장치에 로그인 하는 것으로 간주 됩니다. 예를 들어 Office 앱에 대 한 로그인 빈도를 24 시간으로 구성한 경우 Azure AD에 등록 된 Windows 장치의 사용자는 장치에 로그인 하 여 로그인 빈도 정책을 충족 하 고 Office 앱을 열 때 메시지가 다시 표시 되지 않습니다.

동일한 브라우저 세션에서 실행 되는 다른 웹 앱에 대해 서로 다른 로그인 빈도를 구성한 경우 동일한 브라우저 세션에서 실행 되는 모든 앱이 단일 세션 토큰을 공유 하므로 가장 엄격한 정책이 두 앱 모두에 적용 됩니다.

### <a name="policy-2-persistent-browser-session"></a>정책 2: 영구 브라우저 세션

1. 새 정책 만들기
1. 모든 필수 조건을 선택 합니다.

   > [!NOTE]
   > 이 컨트롤은 "모든 클라우드 앱"을 조건으로 선택 해야 합니다. 브라우저 세션 지 속성은 인증 세션 토큰에 의해 제어 됩니다. 브라우저 세션의 모든 탭은 단일 세션 토큰을 공유 하므로 모두 지 속성 상태를 공유 해야 합니다.

1. **액세스 제어** > **세션** 으로 이동 하 고 **영구 브라우저 세션** 을 클릭 합니다.
1. 드롭다운에서 값 선택
1. 정책 저장

![영구 브라우저에 대해 구성 된 조건부 액세스 정책](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 조건부 액세스의 영구 브라우저 세션 구성이 "로그인 상태 유지"를 덮어씁니다. 두 정책을 모두 구성한 경우 동일한 사용자에 대해 Azure Portal의 회사 브랜딩 창에서를 설정 합니다.

## <a name="validation"></a>유효성 검사

가상 도구를 사용 하 여 사용자의 로그인을 대상 응용 프로그램으로 시뮬레이션 하 고 정책을 구성한 방법에 따라 기타 조건을 사용 합니다. 인증 세션 관리 컨트롤이 도구의 결과에 표시 됩니다.

![조건부 액세스 What If 도구 결과](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>정책 배포

정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md)문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 조건부 액세스 정책을 구성 하는 방법을 알아보려면 [Azure Active Directory 조건부 액세스를 사용 하는 특정 앱에 대 한 MFA 필요](app-based-mfa.md)문서를 참조 하세요.
* 사용자 환경에 대 한 조건부 액세스 정책을 구성할 준비가 된 경우 [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](best-practices.md)문서를 참조 하세요.
