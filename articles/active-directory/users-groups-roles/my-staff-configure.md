---
title: 내 직원을 사용 하 여 사용자 관리 위임 (미리 보기)-Azure AD | Microsoft Docs
description: 내 직원과 관리 단위를 사용 하 여 사용자 관리 위임
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: da358ecb126dda21d18120bbe698fec234c85e4f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798961"
---
# <a name="manage-your-users-with-my-staff-preview"></a>내 직원 (미리 보기)을 사용 하 여 사용자 관리

직원 들이 자신의 직원 구성원이 자신의 Azure AD 계정에 액세스할 수 있도록 허용 하는 사용 권한 (예: 스토어 관리자 또는 팀 리더)에 게 직원을 위임할 수 있습니다. 조직에서는 중앙 기술 지원팀에 의존 하는 대신 암호 재설정 또는 팀 관리자에 게 전화 번호 변경과 같은 일반적인 작업을 위임할 수 있습니다. 직원 들이 자신의 계정에 액세스할 수 없는 사용자는 기술 지원팀 또는 IT 담당자가 필요 하지 않은 두 번의 클릭 만으로 액세스 권한을 다시 얻을 수 있습니다.

조직의 직원을 구성 하기 전에이 설명서 및 [사용자 설명서](../user-help/my-staff-team-manager.md) 를 검토 하 여 사용자에 게이 기능의 기능과 영향을 이해 하는 것이 좋습니다. 사용자 설명서를 활용 하 여 새로운 경험을 위해 사용자를 학습 하 고 준비 하 고 성공적으로 롤아웃 되도록 도울 수 있습니다.

사용자를 위한 SMS 기반 인증은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-my-staff-works"></a>내 직원의 작업 방식

직원은 역할 할당의 관리 제어 범위를 제한 하는 데 사용할 수 있는 리소스 컨테이너인 au (관리 단위)를 기반으로 합니다. 내 직원에서 au는 매장 또는 부서와 같은 조직 사용자의 하위 집합을 정의 하는 데 사용 됩니다. 그런 다음, 예를 들어 범위가 하나 이상의 au 인 역할에 팀 관리자를 할당할 수 있습니다. 아래 예제에서 사용자에 게는 인증 관리 역할이 부여 되 고, 3 개의 au는 역할의 범위입니다. 관리 단위에 대 한 자세한 내용은 [Azure Active Directory의 관리 단위 관리](directory-administrative-units.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.

  * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 구독과 연결된 Azure Active Directory 테넌트.

  * 필요한 경우 [Azure Active Directory 테넌트를 만들거나](../fundamentals/sign-up-organization.md)[Azure 구독을 계정에 연결합니다](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* SMS 기반 인증을 사용 하도록 설정 하려면 Azure AD 테 넌 트에서 *전역 관리자* 권한이 필요 합니다.
* 문자 메시지 인증 방법 정책에서 설정된 각 사용자는 이 방법을 사용하지 않더라도 라이선스가 있어야 합니다. 사용 하도록 설정 된 각 사용자에 게는 다음 Azure AD 또는 Microsoft 365 라이선스 중 하나가 있어야 합니다.

  * [Azure AD Premium P1 또는 P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [M365(Microsoft 365) F1 또는 F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [EMS(Enterprise Mobility + Security) E3 또는 E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) 또는 [M365(Microsoft 365) E3 또는 E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>내 직원을 사용 하도록 설정 하는 방법

Au를 구성한 후에는 내 직원에 게 액세스 하는 사용자에 게이 범위를 적용할 수 있습니다. 관리자 역할이 할당 된 사용자만 내 직원에 액세스할 수 있습니다. 내 직원을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 사용자 관리자 권한으로 Azure Portal에 로그인 합니다.
2. **Azure Active Directory**  >  **사용자 설정**사용자 기능 미리 보기  >  **User feature previews**  >  **사용자 기능 미리 보기 설정 관리**로 이동 합니다.
3. **관리자는 내 직원에 액세스할 수 있으며**모든 사용자, 선택한 사용자 또는 사용자 액세스 권한 없음을 선택할 수 있습니다.

> [!Note]
> 관리자 역할이 할당 된 사용자만 내 직원에 액세스할 수 있습니다. 관리자 역할이 할당 되지 않은 사용자에 대해 내 직원이를 사용 하도록 설정한 경우 내 직원에 게는 액세스할 수 없습니다.

## <a name="conditional-access"></a>조건부 액세스

Azure AD 조건부 액세스 정책을 사용 하 여 내 직원 포털을 보호할 수 있습니다. 직원에 게 액세스 하기 전에 multi-factor authentication 요구와 같은 작업에 사용 합니다.

[AZURE AD 조건부 액세스 정책을](../conditional-access/index.yml)사용 하 여 직원을 보호 하는 것이 좋습니다. 내 직원에 게 조건부 액세스 정책을 적용 하려면 PowerShell을 사용 하 여 내 직원 서비스 사용자를 수동으로 만들어야 합니다.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>내 직원에 게 조건부 액세스 정책 적용

1. [Microsoft Graph 베타 PowerShell cmdlet](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1)을 설치 합니다.
1. 다음 명령을 실행합니다.

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. 내 직원 클라우드 응용 프로그램에 적용 되는 조건부 액세스 정책을 만듭니다.

    ![내 직원 앱에 대 한 조건부 액세스 정책 만들기](media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>내 직원 사용

사용자가 내 직원으로 이동 하면 관리 권한이 있는 [관리 단위의](directory-administrative-units.md) 이름이 표시 됩니다. [내 직원 사용자 설명서](../user-help/my-staff-team-manager.md)에서 "위치" 라는 용어를 사용 하 여 관리 단위를 참조 합니다. 관리자의 권한에 AU 범위가 없으면 조직 전체에 사용 권한이 적용 됩니다. 직원을 사용 하도록 설정 하 고 관리 역할이 할당 된 사용자는를 통해 액세스할 수 있습니다 [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . AU를 선택 하 여 해당 AU의 사용자를 확인 하 고 사용자를 선택 하 여 자신의 프로필을 열 수 있습니다.

## <a name="reset-a-users-password"></a>사용자의 암호 다시 설정

다음 역할에는 사용자의 암호를 재설정할 수 있는 권한이 있습니다.

- [인증 관리자](directory-assign-admin-roles.md#authentication-administrator)
- [권한 있는 인증 관리자](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [전역 관리자](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [기술 지원팀 관리자](directory-assign-admin-roles.md#helpdesk-administrator)
- [사용자 관리자](directory-assign-admin-roles.md#user-administrator)
- [암호 관리자](directory-assign-admin-roles.md#password-administrator)

**내 직원**에서 사용자의 프로필을 엽니다. **암호 다시 설정**을 선택합니다.

- 사용자가 클라우드 전용 이면 사용자에 게 제공할 수 있는 임시 암호를 볼 수 있습니다.
- 사용자가 온-프레미스 Active Directory에서 동기화 되는 경우 온-프레미스 AD 정책을 만족 하는 암호를 입력할 수 있습니다. 그런 다음 사용자에 게 해당 암호를 지정할 수 있습니다.

    ![암호 재설정 진행률 표시기 및 성공 알림](media/my-staff-configure/reset-password.png)

사용자는 다음에 로그인 할 때 암호를 변경 해야 합니다.

## <a name="manage-a-phone-number"></a>전화 번호 관리

**내 직원**에서 사용자의 프로필을 엽니다.

- **전화 번호 추가** 섹션을 선택 하 여 사용자의 전화 번호를 추가 합니다.
- 전화 번호 **편집** 을 선택 하 여 전화 번호를 변경 합니다.
- **전화 번호 제거** 를 선택 하 여 사용자의 전화 번호를 제거 합니다.

사용자는 설정에 따라 SMS를 사용 하 여 로그인 하 고, 다단계 인증을 수행 하 고, 셀프 서비스 암호 재설정을 수행 하기 위해 설정한 전화 번호를 사용할 수 있습니다.

사용자의 전화 번호를 관리 하려면 다음 역할 중 하나를 할당 해야 합니다.

- [인증 관리자](directory-assign-admin-roles.md#authentication-administrator)
- [권한 있는 인증 관리자](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [전역 관리자](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>검색

내 직원의 검색 표시줄을 사용 하 여 조직에서 au와 사용자를 검색할 수 있습니다. 조직의 모든 AU와 사용자를 검색할 수 있지만 관리자 권한이 부여 된 AU에 있는 사용자만 변경할 수 있습니다.

AU 내에서 사용자를 검색할 수도 있습니다. 이렇게 하려면 사용자 목록 위쪽의 검색 표시줄을 사용 합니다.

## <a name="audit-logs"></a>감사 로그

Azure Active Directory 포털의 내 직원에서 수행 된 작업에 대 한 감사 로그를 볼 수 있습니다. 내 직원에서 수행 된 작업에 의해 감사 로그가 생성 된 경우 감사 이벤트의 추가 세부 정보 아래에 표시 되는 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[내 직원 사용자 설명서](../user-help/my-staff-team-manager.md) 
 [관리 단위 설명서](directory-administrative-units.md)