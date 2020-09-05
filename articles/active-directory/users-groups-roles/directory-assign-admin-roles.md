---
title: Azure AD 역할 설명 및 사용 권한-Azure Active Directory | Microsoft Docs
description: 관리자 역할은 사용자를 추가하고, 관리자 역할을 할당하며, 사용자 암호를 다시 설정하고, 사용자 라이선스 또는 도메인을 관리하는 데 사용할 수 있습니다.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09664de9ab2040ad04127ee1556c21244ac6bc2d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269678"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory에서 관리자 역할 사용 권한

Azure AD(Azure Active Directory)를 사용하면 제한된 관리자가 권한이 적은 역할로 ID 작업을 관리하도록 지정할 수 있습니다. 사용자 추가 또는 변경, 관리 역할 할당, 사용자 암호 재설정, 사용자 라이선스 관리, 도메인 이름 관리 등의 목적으로 관리자를 할당할 수 있습니다. [기본 사용자 권한](../fundamentals/users-default-permissions.md)은 Azure AD의 사용자 설정에서만 변경할 수 있습니다.

## <a name="limit-use-of-global-administrator"></a>전역 관리자 사용 제한

전역 관리자 역할에 할당된 사용자는 Azure AD 조직의 모든 관리 설정을 읽고 수정할 수 있습니다. 기본적으로 사용자가 Microsoft 클라우드 서비스에 등록할 때 Azure AD 테 넌 트가 만들어지고 사용자는 전역 관리자 역할의 구성원이 됩니다. 기존 테 넌 트에 구독을 추가 하면 전역 관리자 역할에 할당 되지 않습니다. 전역 관리자와 권한 있는 역할 관리자만이 관리자 역할을 위임할 수 있습니다. 비즈니스 위험을 줄이기 위해 조직 내 극소수의 사람에게만 이 역할을 할당하는 것이 좋습니다.

모범 사례에 따라 조직 내에서 5명 미만의 사람에게만 이 역할을 할당하는 것이 좋습니다. 조직에서 전역 관리자 역할에 할당된 관리자가 5명을 초과하는 경우 다음과 같은 방법으로 수를 줄일 수 있습니다.

### <a name="find-the-role-you-need"></a>필요한 역할 찾기

수많은 역할 목록에서 필요한 역할을 찾기가 어려우면 Azure AD에서 역할 범주에 따라 역할의 하위 집합을 표시할 수 있습니다. 선택한 유형의 역할만 표시하는 [Azure AD 역할 및 관리자](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)에 대한 새 **유형** 필터를 살펴보세요.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>지금은 있지만 전역 관리자 역할을 할당할 당시에는 없던 역할

사용자를 전역 관리자로 승격하던 당시에는 없던 세분화된 권한을 제공하는 Azure AD에 역할이 추가되었을 수 있습니다. 시간이 지나면서 이전에는 전역 관리자 역할만이 수행할 수 있었던 작업을 수행하는 역할이 점점 추가됩니다. 다음 [사용 가능한 역할](#available-roles)에서 이러한 내용이 반영된 것을 볼 수 있습니다.

## <a name="assign-or-remove-administrator-roles"></a>관리자 역할 할당 또는 제거

Azure Active Directory에서 사용자에게 관리 역할을 할당하는 방법을 알아보려면 [Azure Active Directory에서 관리자 역할 보기 및 할당](directory-manage-roles-portal.md)을 참조하세요.

> [!Note]
> Azure AD premium P2 라이선스가 있고 이미 PIM (Privileged Identity Management) 인 경우 모든 역할 관리 작업은 Azure AD가 아닌 권한 Id 관리에서 수행 됩니다.
>
> ![이미 PIM을 사용 하 고 프리미엄 P2 라이선스가 있는 사용자를 위해 PIM에서 관리 되는 Azure AD 역할](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>사용 가능한 역할

다음과 같은 관리자 역할을 사용할 수 있습니다.

### <a name="application-administrator"></a>[애플리케이션 관리자](#application-administrator-permissions)

이 역할의 사용자는 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

또한 이 역할은 Microsoft Graph API에 대한 권한을 제외하고 위임된 권한 및 애플리케이션 권한에 _동의_하는 기능을 부여합니다.

> [!IMPORTANT]
> 이 예외는 _다른_ 앱(예: 타사 앱 또는 사용자가 등록한 앱)에 대한 권한에는 동의할 수 있지만, Azure AD 자체에 대한 권한에는 동의할 수 없다는 뜻입니다. 여전히 앱을 등록할 때 이 권한을 _요청_할 수 있지만, 이 권한을 _부여_하려면 Azure AD 관리자여야 합니다. 즉, 악의적인 사용자가 자신의 권한을 쉽게 상승시킬 수 없습니다. 예를 들어 전체 디렉터리에 쓸 수 있는 앱을 만들고 동의한 다음, 해당 앱의 권한을 통해 자신을 전역 관리자로 승격할 수 없습니다.
>
>이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 응용 프로그램의 id에 사용자 또는 다른 개체를 만들거나 업데이트 하는 기능과 같은 리소스에 대 한 액세스 권한이 부여 된 경우이 역할에 할당 된 사용자는 응용 프로그램을 가장 하는 동안 이러한 작업을 수행할 수 있습니다. 응용 프로그램의 id를 가장 하는이 기능은 사용자가 해당 역할 할당을 통해 수행할 수 있는 작업에 대 한 권한 상승 일 수 있습니다. 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.

### <a name="application-developer"></a>[애플리케이션 개발자](#application-developer-permissions)

이 역할의 사용자는 "사용자가 애플리케이션을 등록할 수 있음" 설정이 아니오로 설정된 경우 애플리케이션 등록을 만들 수 있습니다. 또한 이 역할은 “사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음” 설정이 [아니요]로 설정된 경우 대신 동의할 수 있는 권한을 부여합니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가됩니다.

### <a name="authentication-administrator"></a>[인증 관리자](#authentication-administrator-permissions)

이 역할이 할당된 사용자는 일부 사용자의 암호가 아닌 자격 증명을 설정하거나 초기화하고 모든 사용자의 암호를 업데이트할 수 있습니다. 인증 관리자는 관리자가 아닌 사용자 또는 일부 역할이 할당된 사용자가 기존의 비암호 자격 증명(예: MFA 또는 FIDO)을 재등록하도록 요구할 수 있으며, 다음에 로그인할 때 MFA를 요청하도록 **디바이스에 MFA 저장**을 철회할 수도 있습니다. 이러한 작업은 관리자가 아닌 사용자 또는 다음 역할 중 하나 이상에 할당된 사용자에게만 적용됩니다.

* 인증 관리자
* 디렉터리 읽기 권한자
* 게스트 초대자
* 메시지 센터 읽기 권한자
* 보고서 구독자

[권한 있는 인증 관리자](#privileged-authentication-administrator) 역할은 모든 사용자에게 재등록 및 다단계 인증을 강제 적용할 수 있습니다.

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 자격 증명을 변경할 수 있습니다. 사용자의 자격 증명을 변경한다는 것은 사용자의 ID 및 사용 권한을 가정할 수 있음을 의미할 수 있습니다. 다음은 그 예입니다.
>
>* 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 사용 권한이 부여되었을 수 있으며, 다른 위치에서는 인증 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 인증 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>* Azure 구독 소유자: Azure에서 중요한 프라이빗 정보 또는 중요한 구성에 액세스할 수 있습니다.
>* 그룹 멤버 자격을 관리할 수 있는 보안 그룹 및 Microsoft 365 그룹 소유자입니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>* Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>* 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

### <a name="azure-devops-administrator"></a>[Azure DevOps 관리자](#azure-devops-administrator-permissions)

이 역할이 할당된 사용자는 Azure DevOps 정책을 관리하여 새 Azure DevOps 조직 만들기를 구성 가능한 사용자 또는 그룹 세트로 제한할 수 있습니다. 이 역할의 사용자는 회사의 Azure AD 조직에서 지원하는 모든 Azure DevOps 조직을 통해 이 정책을 관리할 수 있습니다.

모든 엔터프라이즈 Azure DevOps 정책은 이 역할의 사용자가 관리할 수 있습니다.

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection 관리자](#azure-information-protection-administrator-permissions)

이 역할을 가진 사용자는 Azure Information Protection 서비스에 대한 모든 사용 권한을 갖습니다. 이 역할은 Azure Information Protection 정책에 대한 레이블을 구성하고, 보호 템플릿을 관리하고, 보호를 활성화하는 권한을 갖습니다. 이 역할은 ID 보호 센터, Privileged Identity Management, Office 365 Service Health 또는 Office 365 보안 및 준수 센터에서 어느 권한도 부여하지 않습니다.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF 키 세트 관리자](#b2c-ief-keyset-administrator-permissions)

사용자는 토큰 암호화, 토큰 서명 및 클레임 암호화/암호 해독을 위한 정책 키와 비밀을 만들고 관리할 수 있습니다. 기존 키 컨테이너에 새 키를 추가하면 이 제한된 관리자는 기존 애플리케이션에 영향을 주지 않고 필요에 따라 비밀을 롤오버할 수 있습니다. 이 사용자는 이러한 비밀을 만든 후에도 비밀의 전체 콘텐츠와 만료 날짜를 볼 수 있습니다.

> [!IMPORTANT]
> 이 역할은 중요한 역할입니다. 사전 프로덕션 및 프로덕션 단계에서 키 세트 관리자 역할을 신중하게 감사하고 할당해야 합니다.

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF 정책 관리자](#b2c-ief-policy-administrator-permissions)

이 역할의 사용자는 Azure AD B2C에서 모든 사용자 지정 정책을 만들고, 읽고, 업데이트하고, 삭제할 수 있으므로 관련 Azure AD B2C 조직의 ID 환경 프레임워크를 완전히 제어할 수 있습니다. 이 사용자는 정책을 편집하여 외부 ID 공급자와의 직접 페더레이션을 설정하고, 디렉터리 스키마를 변경하고, 모든 사용자 관련 콘텐츠(HTML, CSS, JavaScript)를 변경하고, 인증을 완료하기 위한 요구 사항을 변경하고, 새 사용자를 만들고, 전체 마이그레이션을 포함하여 외부 시스템으로 사용자 데이터를 보내고, 암호 및 전화 번호와 같은 중요한 필드를 비롯한 모든 사용자 정보를 편집할 수 있습니다. 반면 이 역할은 암호화 키를 변경하거나 조직의 페더레이션에 사용되는 비밀을 편집할 수 없습니다.

> [!IMPORTANT]
> B2 IEF 정책 관리자는 매우 중요한 역할이므로 프로덕션 환경의 조직에 대해 매우 제한적으로 할당해야 합니다. 이러한 사용자의 활동을 면밀하게 감사해야 하며, 특히 프로덕션 조직이 그렇습니다.

### <a name="billing-administrator"></a>[대금 청구 관리자](#billing-administrator-permissions)

구매, 구독 관리, 지원 티켓 관리 및 서비스 상태 모니터링을 수행할 수 있습니다.

### <a name="cloud-application-administrator"></a>[클라우드 애플리케이션 관리자](#cloud-application-administrator-permissions)

이 역할의 사용자는 애플리케이션 관리자 역할과 동일한 권한을 가집니다. 다만 애플리케이션 프록시를 관리하는 권한은 없습니다. 이 역할은 엔터프라이즈 애플리케이션 및 애플리케이션 등록의 모든 측면을 만들고 관리하는 기능을 부여합니다. 또한 이 역할은 위임된 권한 및 Microsoft Graph와 Azure AD Graph를 제외한 애플리케이션 사용 권한에 동의하는 기능을 부여합니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

> [!IMPORTANT]
> 이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 응용 프로그램의 id에 사용자 또는 다른 개체를 만들거나 업데이트 하는 기능과 같은 리소스에 대 한 액세스 권한이 부여 된 경우이 역할에 할당 된 사용자는 응용 프로그램을 가장 하는 동안 이러한 작업을 수행할 수 있습니다. 응용 프로그램의 id를 가장 하는이 기능은 사용자가 해당 역할 할당을 통해 수행할 수 있는 작업에 대 한 권한 상승 일 수 있습니다. 클라우드 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.


### <a name="cloud-device-administrator"></a>[클라우드 디바이스 관리자](#cloud-device-administrator-permissions)

이 역할의 사용자는 Azure AD에서 디바이스를 사용하고 사용하지 않도록 설정하며, 삭제하고, Azure Portal에서 Windows 10 BitLocker 키(있는 경우)를 읽을 수 있습니다. 역할은 디바이스에서 다른 속성을 관리하는 사용 권한을 부여하지 않습니다.

### <a name="compliance-administrator"></a>[규정 준수 관리자](#compliance-administrator-permissions)

이 역할이 있는 사용자에게는 Microsoft 365 규정 준수 센터, Microsoft 365 관리 센터, Azure, Office 365 보안 및 준수 센터에서 규정 준수 관련 기능을 관리할 권한이 있습니다. 담당자는 Exchange 관리 센터와 Teams 및 비즈니스용 Skype 관리 센터 내의 모든 기능을 관리하고 Azure 및 Microsoft 365 관련 지원 티켓을 만들 수도 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

그런 다음 | 가능한 작업
----- | ----------
[Microsoft 365 규정 준수 센터](https://protection.office.com) | Microsoft 365 서비스 전반에 걸쳐 조직의 데이터 보호 및 관리<br>규정 준수 경고 관리
[준수 관리자](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 조직의 규정 준수 활동 추적, 할당, 확인
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 데이터 거버넌스 관리<br>법적 조사/데이터 조사 수행<br>데이터 주체 요청 관리<br><br>이 역할은 Office 365 보안 및 준수 센터 역할 기반 액세스 제어의 [규정 준수 관리자 RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)과 동일한 권한을 갖습니다.
[Intune](/intune/role-based-access-control) | 모든 Intune 감사 데이터 확인
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 전용 권한 소유/경고를 관리할 수 있음<br>파일 정책을 생성/수정하고 파일 거버넌스 작업을 허용할 수 있음<br>데이터 관리에서 모든 기본 제공 보고서를 확인할 수 있음

### <a name="compliance-data-administrator"></a>[규정 준수 데이터 관리자](#compliance-data-administrator-permissions)

이 역할이 있는 사용자는 Microsoft 365 규정 준수 센터, Microsoft 365 관리 센터 및 Azure에서 데이터를 추적할 수 있는 권한이 있습니다. 또한 사용자는 Exchange 관리 센터, Compliance Manager, Teams 및 비즈니스용 Skype 관리 센터 내에서 규정 준수 데이터를 추적하고 Azure 및 Microsoft 365의 지원 티켓을 만들 수 있습니다.

그런 다음 | 가능한 작업
----- | ----------
[Microsoft 365 규정 준수 센터](https://protection.office.com) | Microsoft 365 서비스 전반의 규정 준수 관련 정책 모니터링<br>규정 준수 경고 관리
[준수 관리자](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 조직의 규정 준수 활동 추적, 할당, 확인
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 데이터 거버넌스 관리<br>법적 조사/데이터 조사 수행<br>데이터 주체 요청 관리<br><br>이 역할은 Office 365 보안 및 준수 센터 역할 기반 액세스 제어의 [규정 준수 데이터 관리자 RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)과 동일한 권한을 갖습니다.
[Intune](/intune/role-based-access-control) | 모든 Intune 감사 데이터 확인
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 전용 권한 소유/경고를 관리할 수 있음<br>파일 정책을 생성/수정하고 파일 거버넌스 작업을 허용할 수 있음<br>데이터 관리에서 모든 기본 제공 보고서를 확인할 수 있음

### <a name="conditional-access-administrator"></a>[조건부 액세스 관리자](#conditional-access-administrator-permissions)

이 역할을 가진 사용자는 Azure Active Directory 조건부 액세스 설정을 관리할 수 있습니다.
> [!NOTE]
> Azure에서 Exchange ActiveSync 조건부 액세스 정책을 배포하려면 사용자도 전역 관리자여야 합니다.

### <a name="customer-lockbox-access-approver"></a>[고객 Lockbox 액세스 승인자](#customer-lockbox-access-approver-permissions)

조직에서 [고객 Lockbox 요청](/office365/admin/manage/customer-lockbox-requests)을 관리합니다. 이러한 고객 Lockbox 요청에 대한 이메일 알림을 수신하고 Microsoft 365 관리 센터에서 요청을 승인 및 거부할 수 있습니다. 고객 Lockbox 기능을 켜거나 끌 수도 있습니다. 글로벌 관리자만 이 역할에 할당된 사용자의 암호를 재설정할 수 있습니다.

### <a name="desktop-analytics-administrator"></a>[Desktop Analytics 관리자](#desktop-analytics-administrator-permissions)


이 역할의 사용자는 Desktop Analytics 및 Office Customization 및 Policy 서비스를 관리할 수 있습니다. Desktop Analytics의 경우 여기에는 자산 인벤토리를 확인하고, 배포 계획을 만들고, 배포 및 상태를 볼 수 있는 기능이 포함됩니다. Office Customization & Policy 서비스의 경우 이 역할의 사용자는 Office 정책을 관리할 수 있습니다.

### <a name="device-administrators"></a>[디바이스 관리자](#device-administrators-permissions)

이 역할은 [디바이스 설정](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)의 추가 로컬 관리자로서 할당을 위해서만 사용할 수 있습니다. 이 역할을 가진 사용자가 Azure Active Directory에 가입된 모든 Windows 10 디바이스에서 로컬 컴퓨터 관리자가 됩니다. Azure Active Directory의 디바이스 개체를 관리하는 기능이 없습니다.

### <a name="directory-readers"></a>[디렉터리 읽기 권한자](#directory-readers-permissions)

이 역할의 사용자는 기본 디렉터리 정보를 읽을 수 있습니다. 이 역할은 다음 용도로 사용해야 합니다.

* 모든 게스트 사용자가 아닌 특정 게스트 사용자에게만 읽기 권한을 부여합니다.
* "Azure AD 포털에 대한 액세스 권한을 관리자로만 제한"이 "예"로 설정된 경우 관리자가 아닌 특정 사용자에게 Azure Portal에 대한 액세스 권한을 부여합니다.
* Directory.Read.All을 사용할 수 없는 디렉터리에 대한 액세스 권한을 서비스 주체에게 부여합니다.

### <a name="directory-synchronization-accounts"></a>[디렉터리 동기화 계정](#directory-synchronization-accounts-permissions)

사용하지 마십시오. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.

### <a name="directory-writers"></a>[디렉터리 쓰기 권한자](#directory-writers-permissions)
이 역할의 사용자는 사용자, 그룹 및 서비스 사용자의 기본 정보를 읽고 업데이트할 수 있습니다. [승인 프레임 워크](../develop/quickstart-register-app.md)를 지원 하지 않는 응용 프로그램에만이 역할을 할당 합니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365 관리자/CRM 관리자](#crm-service-administrator-permissions)

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Dynamics 365 Online 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [서비스 관리자 역할을 사용하여 Azure AD 조직 관리](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Dynamics 365 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Dynamics 365 관리자"입니다.

### <a name="exchange-administrator"></a>[Exchange 관리자](#exchange-service-administrator-permissions)

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Exchange Online 내에서 글로벌 사용 권한을 갖습니다. 또한에는 모든 Microsoft 365 그룹을 생성 및 관리 하 고, 지원 티켓을 관리 하 고, 서비스 상태를 모니터링할 수 있는 기능이 있습니다. 자세한 내용은 [Office 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Exchange 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Exchange 관리자"입니다. [Exchange 관리 센터](https://go.microsoft.com/fwlink/p/?LinkID=529144)의 "Exchange Online 관리자"입니다.


### <a name="external-id-user-flow-administrator"></a>[외부 ID 사용자 흐름 관리자](#external-id-user-flow-administrator-permissions)

이 역할을 가진 사용자는 Azure Portal에서 사용자 흐름을 만들고 관리할 수 있습니다 ("기본 제공" 정책이 라고도 함). 이러한 사용자는 HTML/CSS/JavaScript 콘텐츠를 사용자 지정 하 고, MFA 요구 사항을 변경 하 고, 토큰에서 클레임을 선택 하 고, API 커넥터를 관리 하 고, Azure AD 조직의 모든 사용자 흐름에 대해 세션 설정을 구성할 수 있습니다. 반면에이 역할에는 사용자 데이터를 검토 하거나 조직 스키마에 포함 된 특성을 변경할 수 있는 기능이 포함 되지 않습니다. Id 경험 프레임 워크 정책 (사용자 지정 정책이 라고도 함)에 대 한 변경 내용은이 역할의 범위 밖에 있습니다.

### <a name="external-id-user-flow-attribute-administrator"></a>[외부 ID 사용자 흐름 특성 관리자](#external-id-user-flow-attribute-administrator-permissions)

이 역할의 사용자는 Azure AD 조직의 모든 사용자 흐름에서 사용할 수 있는 사용자 지정 특성을 추가 또는 삭제할 수 있습니다. 따라서 이 역할의 사용자는 새 요소를 변경하거나 최종 사용자 스키마에 추가하고 모든 사용자 흐름의 동작에 영향을 줄 수 있으며, 이로 인해 최종 사용자에게 요청하여 결국 애플리케이션에 대한 클레임으로 전송되는 데이터를 간접적으로 변경할 수 있습니다. 이 역할은 사용자 흐름을 편집할 수 없습니다.

### <a name="external-identity-provider-administrator"></a>[외부 ID 공급자 관리자](#external-identity-provider-administrator-permissions)

이 관리자는 Azure AD 조직과 외부 ID 공급자 간의 페더레이션을 관리합니다. 이 역할의 사용자는 새 ID 공급자를 추가하고 사용 가능한 모든 설정(예: 인증 경로, 서비스 ID, 할당된 키 컨테이너)을 구성할 수 있습니다. 이 사용자는 Azure AD 조직이 외부 ID 공급자의 인증을 신뢰할 수 있게 해줍니다. 최종 사용자 환경에 미치는 영향은 조직의 유형에 따라 달라집니다.

* 직원 및 파트너를 위한 Azure AD 조직: 페더레이션을 추가하면(예: Gmail을 사용하여) 아직 사용되지 않은 모든 게스트 초대에 즉시 영향을 줍니다. [Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가](../external-identities/google-federation.md)를 참조하세요.
* Azure Active Directory B2C 조직: 페더레이션을 추가해도(예: Facebook 또는 다른 Azure AD 조직을 사용하여) 사용자 흐름(기본 제공 정책이라고도 함)에서 ID 공급자를 옵션으로 추가하기 전에는 최종 사용자 흐름에 즉시 영향을 주지 않습니다. 관련 예제는 [Microsoft 계정을 ID 공급자로 구성](../../active-directory-b2c/identity-provider-microsoft-account.md)을 참조하세요. 사용자 흐름을 변경하려면 "B2C 사용자 흐름 관리자"의 제한된 역할이 필요합니다.

### <a name="global-administrator--company-administrator"></a>[전역 관리자/회사 관리자](#company-administrator-permissions)

이 역할의 사용자는 Azure Active Directory의 모든 관리 기능뿐 아니라 Microsoft 365 보안 센터, Microsoft 365 규정 준수 센터, Exchange Online, SharePoint Online 및 비즈니스용 Skype Online과 같이 Azure Active Directory ID를 사용하는 서비스에도 액세스할 수 있습니다. 또한 전역 관리자는 모든 Azure 구독 및 관리 그룹을 관리 하기 위해 [액세스 권한을 상승](../../role-based-access-control/elevate-access-global-admin.md) 시킬 수 있습니다. 이렇게 하면 전역 관리자가 각 Azure AD 테 넌 트를 사용 하 여 모든 Azure 리소스에 대 한 전체 액세스 권한을 얻을 수 있습니다. Azure AD 조직에 가입하는 사람은 전역 관리자가 됩니다. 회사에 여러 전역 관리자가 있을 수 있습니다. 전역 관리자는 모든 사용자 및 모든 다른 관리자의 암호를 다시 설정할 수 있습니다.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "회사 관리자"로 식별됩니다. [Azure portal](https://portal.azure.com)에서는 "전역 관리자"입니다.
>
>

### <a name="global-reader"></a>[글로벌 읽기 권한자](#global-reader-permissions)

이 역할의 사용자는 모든 Microsoft 365 서비스의 설정 및 관리 정보를 읽을 수 있지만 관리 작업을 수행할 수는 없습니다. 글로벌 읽기 권한자는 전역 관리자에 대응되는 읽기 전용 역할입니다. 계획, 감사 또는 조사에는 전역 관리자 대신 글로벌 읽기 권한자를 할당하세요. 전역 관리자 역할을 할당하지 말고 Exchange 관리자처럼 제한된 다른 관리자 역할과 함께 글로벌 읽기 권한자를 사용하면 작업을 쉽게 수행할 수 있습니다. 글로벌 읽기 권한자는 Microsoft 365 관리 센터, Exchange 관리 센터, SharePoint 관리 센터, Teams 관리 센터, 보안 센터, 규정 준수 센터, Azure AD 관리 센터 및 디바이스 관리 센터에서 작동합니다.

> [!NOTE]
> 글로벌 읽기 권한자 역할에는 현재 다음과 같은 제한이 있습니다.
>
>- [Onedrive 관리 센터](https://admin.onedrive.com/) -onedrive 관리 센터는 전역 읽기 권한자 역할을 지원 하지 않습니다.
>- [M365 관리 센터](https://admin.microsoft.com/Adminportal/Home#/homepage) - 글로벌 읽기 권한자는 고객 lockbox 요청을 읽을 수 없습니다. M365 관리 센터의 왼쪽 창에 있는 **지원**에서 **고객 lockbox 요청** 탭을 찾을 수 없습니다.
>- [Office 보안 및 준수 센터](https://sip.protection.office.com/homepage) - 글로벌 읽기 권한자는 SCC 감사 로그를 읽거나, 콘텐츠를 검색하거나, 보안 점수를 볼 수 없습니다.
>- [Teams 관리 센터](https://admin.teams.microsoft.com) - 글로벌 읽기 권한자는 **Teams 수명 주기**, **분석 및 보고서**, **IP 전화 디바이스 관리** 및 **앱 카탈로그**를 읽을 수 없습니다.
>- [PAM(Privileged Access Management)](/office365/securitycompliance/privileged-access-management-overview)은 글로벌 읽기 권한자 역할을 지원하지 않습니다.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - 글로벌 읽기 권한자는 [중앙 보고](/azure/information-protection/reports-aip)에만 지원되고, Azure AD 조직이 [통합 레이블 플랫폼](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)에 있지 않은 경우에만 지원됩니다.
>
> 이러한 기능은 현재 개발 중입니다.
>

### <a name="groups-administrator"></a>[그룹 관리자](#groups-administrator-permissions)

이 역할의 사용자는 그룹과 해당 설정(이름, 만료 정책 등)을 만들고 관리할 수 있습니다. 사용자를 이 역할에 할당하면 Outlook뿐 아니라 Teams, SharePoint, Yammer 등의 다양한 워크로드에서 조직의 모든 그룹을 관리하는 기능이 부여됩니다. 또한 사용자는 Microsoft 관리 센터, Azure Portal 등의 다양한 관리 포털에서 다양한 그룹 설정을 관리할 수 있을 뿐 아니라 Teams 및 SharePoint 관리 센터와 같은 워크로드 관련 그룹 설정을 관리할 수 있습니다.

### <a name="guest-inviter"></a>[게스트 초대자](#guest-inviter-permissions)

이 역할의 사용자는 **멤버가 초대할 수 있음** 사용자 설정을 아니요로 설정하는 경우 Azure Active Directory B2B 게스트 사용자 초대를 관리할 수 있습니다. B2B 협업에 대한 자세한 내용은 [Azure AD B2B 협업 정보](../external-identities/what-is-b2b.md)를 참조하세요. 다른 권한은 포함되지 않습니다.

### <a name="helpdesk-administrator"></a>[기술 지원팀 관리자](#helpdesk-administrator-permissions)

이 역할을 가진 사용자는 암호를 변경하고, 새로 고침 토큰을 무효화하고, 서비스 요청을 관리하며, 서비스 상태를 모니터링할 수 있습니다. 새로 고침 토큰을 무효화하면 사용자가 다시 로그인해야 합니다. 기술 지원팀 관리자는 관리자가 아니거나 다음 역할만 할당된 다른 사용자의 암호를 재설정하고 새로 고침 토큰을 무효화할 수 있습니다.

* 디렉터리 읽기 권한자
* 게스트 초대자
* 기술 지원팀 관리자
* 메시지 센터 읽기 권한자
* 암호 관리자
* 보고서 구독자

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 다음은 그 예입니다.
>
>- 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 기술 지원 팀 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 기술 지원 팀 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>- Azure 구독 소유자: Azure의 중요한 정보나 개인 정보 또는 중요한 구성에 액세스할 수 있습니다.
>- 그룹 멤버 자격을 관리할 수 있는 보안 그룹 및 Microsoft 365 그룹 소유자입니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>- Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>- 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

[관리 단위(현재 공개 미리 보기)](./directory-administrative-units.md)를 사용하여 일부 사용자에게 관리 권한을 위임하고 일부 사용자에게 정책을 적용할 수 있습니다.

이 역할을 이전에는 [Azure Portal](https://portal.azure.com/)에서 "암호 관리자"라고 했습니다. Azure AD의 "기술 지원팀 관리자" 이름이 이제는 Azure AD PowerShell 및 Microsoft Graph API의 이름과 일치합니다.

### <a name="hybrid-identity-administrator"></a>[하이브리드 ID 관리자](#hybrid-identity-administrator-permissions)

이 역할의 사용자는 Azure AD에서 하이브리드 ID 사용 설정과 관련된 서비스 및 설정을 사용하도록 설정하고, 구성하고, 관리할 수 있습니다. 이 역할은 지원되는 세 가지 인증 방법, 즉, PHS(암호 해시 동기화), PTA(통과 인증), 페더레이션(AD FS 또는 타사 페더레이션 공급자) 중 하나로 Azure AD를 구성하고, 관련 온-프레미스 인프라를 배포하여 이 방법을 사용하도록 설정할 수 있는 기능을 부여합니다. 온-프레미스 인프라에는 프로비저닝 및 PTA 에이전트가 포함되어 있습니다. 이 역할은 S-SSO(Seamless Single Sign-On)를 사용하도록 설정하여 Windows 10 이외의 디바이스 또는 Windows Server 2016 이외의 컴퓨터에서 원활한 인증이 가능하게 하는 기능을 부여합니다. 또한 이 역할은 모니터링 및 문제 해결을 위해 로그인 로그를 살펴보고 상태 및 분석에 액세스할 수 있는 기능을 부여합니다. 

### <a name="insights-administrator"></a>[Insights 관리자](#insights-administrator-permissions)
이 역할의 사용자는 [M365 Insights 응용 프로그램](https://go.microsoft.com/fwlink/?linkid=2129521)의 전체 관리 기능 집합에 액세스할 수 있습니다. 이 역할에는 디렉터리 정보를 읽고, 서비스 상태를 모니터링 하 고, 파일 지원 티켓을 확인 하 고, Insights 관리 설정 측면에 액세스할 수 있는 기능이 있습니다.

### <a name="insights-business-leader"></a>[Insights 비즈니스 리더](#insights-business-leader-permissions)
이 역할의 사용자는 [M365 insights 응용 프로그램](https://go.microsoft.com/fwlink/?linkid=2129521)을 통해 대시보드와 정보 집합에 액세스할 수 있습니다. 여기에는 모든 대시보드에 대 한 모든 액세스 권한 및 제공 되는 정보 및 데이터 탐색 기능이 포함 됩니다. 이 역할의 사용자는 정보 관리자 역할의 책임이 있는 제품 구성 설정에 액세스할 수 없습니다.

### <a name="intune-administrator"></a>[Intune 관리자](#intune-service-administrator-permissions)

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Intune Online 내에서 글로벌 사용 권한을 갖습니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 디바이스를 관리하는 기능을 포함합니다. 자세한 내용은 [Microsoft Intune에서 RBAC(역할 기반 관리 제어)](/intune/role-based-access-control)를 참조하세요

이 역할은 모든 보안 그룹을 만들고 관리할 수 있습니다. 그러나 Intune 관리자는 Office 그룹에 대한 관리자 권한이 없습니다. 즉, 관리자는 조직 내 모든 Office 그룹의 소유자 또는 멤버를 업데이트할 수 없습니다. 그러나 관리자는 자신이 만드는 Office 그룹을 관리할 수 있으며, 이 권한은 최종 사용자 권한의 일부로 제공됩니다. 따라서 관리자가 만드는 모든 Office 그룹(보안 그룹 아님)은 관리자의 250 할당량을 기준으로 계산되어야 합니다.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서이 역할은 "Intune 서비스 관리자"로 식별 됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Intune 관리자"입니다.

### <a name="kaizala-administrator"></a>[Kaizala 관리자](#kaizala-administrator-permissions)

Microsoft Kaizala 서비스가 있는 경우 이 역할의 사용자에게는 서비스 내에서 설정을 관리할 수 있을 뿐 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있는 글로벌 권한이 부여됩니다. 또한 사용자는 조직 구성원의 Kaizala 도입 및 사용과 관련된 정보, 그리고 Kaizala 작업을 사용하여 생성된 비즈니스 보고서에 액세스할 수 있습니다.

### <a name="license-administrator"></a>[라이선스 관리자](#license-administrator-permissions)

이 역할의 사용자는 사용자 및 그룹의(그룹 기반 라이선스 사용) 라이선스 할당을 추가, 제거 및 업데이트하고, 사용자의 사용 위치를 관리합니다. 이 역할은 사용 위치를 벗어나서 구독을 구매 또는 관리하고, 그룹을 만들거나 관리하고, 사용자를 만들거나 관리하는 기능을 부여하지 않습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

### <a name="message-center-privacy-reader"></a>[메시지 센터 개인 정보 읽기 권한자](#message-center-privacy-reader-permissions)

이 역할의 사용자는 데이터 개인 정보 메시지를 포함하여 메시지 센터의 모든 알림을 모니터링할 수 있습니다. 메시지 센터 개인 정보 읽기 권한자는 데이터 개인 정보와 관련된 알림을 비롯한 이메일 알림을 수신하며, 메시지 센터 기본 설정을 사용하여 구독을 취소할 수 있습니다. 전역 관리자와 메시지 센터 개인 정보 읽기 권한자만 데이터 개인 정보 메시지를 읽을 수 있습니다. 또한 이 역할은 그룹, 도메인 및 구독을 볼 수 있습니다. 이 역할에는 서비스 요청을 보고, 만들고, 관리할 수 있는 권한이 없습니다.

### <a name="message-center-reader"></a>[메시지 센터 읽기 권한자](#message-center-reader-permissions)

이 역할의 사용자는 Exchange, Intune 및 Microsoft Teams와 같은 구성된 서비스에서 조직에 대한 [Office 365 메시지 센터](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)의 알림 및 자문 상태 업데이트를 모니터링할 수 있습니다. 메시지 센터 읽기 권한자는 게시물 및 업데이트 이메일 다이제스트를 매주 수신하며, 메시지 센터 게시물을 Office 365에서 공유할 수 있습니다. Azure AD에서 이 역할에 할당된 사용자는 Azure AD 서비스에서 사용자 및 그룹처럼 읽기 전용 권한만 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

### <a name="modern-commerce-user"></a>[최신 상거래 사용자](#modern-commerce-user-permissions)

사용하지 마십시오. 이 역할은 상거래에서 자동으로 할당되며 다른 용도로 사용하기에 적합하거나 지원되지 않습니다. 아래에서 자세한 내용을 참조하세요.

최신 상거래 사용자 역할은 Microsoft 365 관리 센터에 액세스할 수 있는 권한을 특정 사용자에 게 제공 하 고 **홈**, **대금 청구**및 **지원**에 대 한 왼쪽 탐색 항목을 표시 합니다. 이러한 영역에 제공되는 콘텐츠는 사용자가 직접 구매했거나 조직을 대신해서 구매한 제품을 관리하기 위해 사용자에게 할당하는 [상거래 관련 역할](../../cost-management-billing/manage/understand-mca-roles.md)을 통해 제어됩니다. 여기에는 요금 결제 또는 청구 계정 및 청구 프로필 액세스와 같은 작업이 포함될 수 있습니다. 

최신 상거래 사용자 역할의 사용자에 게는 일반적으로 다른 Microsoft 구매 시스템에서 관리 권한이 있지만 관리 센터에 액세스 하는 데 사용 되는 전역 관리자 또는 청구 관리자 역할은 없습니다. 

**최신 상거래 사용자 역할은 언제 할당 되나요?**

* **Microsoft 365 관리 센터에서 셀프 서비스 구매** – 사용자는 셀프 서비스 구매를 통해 제품을 직접 구매하거나 가입하여 신제품을 사용해 볼 수 있습니다. 이러한 제품은 관리 센터에서 관리됩니다. 셀프 서비스 구매를 수행 하는 사용자에 게는 상거래 시스템의 역할과 최신 상거래 사용자 역할이 할당 되므로 관리 센터에서 해당 구매를 관리할 수 있습니다. 관리자는 [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide)을 통해(Power BI, Power Apps 및 Power Automate에 대한) 셀프 서비스 구매를 차단할 수 있습니다. 자세한 내용은 [셀프 서비스 구매 질문과 대답](/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide)을 참조하세요.  
* **Microsoft 상업적 marketplace에서 구매**  – 사용자가 Microsoft AppSource 또는 Azure Marketplace에서 제품이 나 서비스를 구입 하는 경우에는 전역 관리자 또는 청구 관리자 역할이 없는 경우 최신 상거래 사용자 역할이 할당 됩니다. 경우에 따라 사용자가 제품을 구매하지 못하게 차단될 수도 있습니다. 자세한 내용은 [Microsoft 상업용 마켓플레이스](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase)를 참조하세요.
* **Microsoft의 제안** – 제안은 조직에 Microsoft 제품 및 서비스를 구매할 것을 권유하는 Microsoft의 공식 제안입니다. 제안을 수락 하는 사용자에 게 Azure AD의 전역 관리자 또는 청구 관리자 역할이 없는 경우, 관리자 센터에 액세스 하려면 제안서를 완료 하 고 최신 상거래 사용자 역할을 모두 할당 합니다. 이 사람은 관리 센터에 액세스할 때 자신의 상거래 관련 역할에 의해 권한이 부여된 기능만 사용할 수 있습니다.
* **상거래 관련 역할** - 일부 사용자에게 상거래 관련 역할이 할당됩니다. 사용자가 전역 또는 대금 청구 관리자가 아니면 관리 센터에 액세스할 수 있도록 최신 상거래 사용자 역할을 받게 됩니다.  

최신 상거래 사용자 역할이 사용자에 게 할당 되지 않은 경우에는 Microsoft 365 관리 센터에 액세스할 수 없게 됩니다. 직접 또는 조직을 대신해서 제품을 관리하는 사용자는 제품을 관리할 수 없게 됩니다. 여기에는 라이선스 할당, 결제 방법 변경, 청구서 결제 또는 구독 관리에 대한 기타 작업이 포함될 수 있습니다.

### <a name="network-administrator"></a>[네트워크 관리자](#network-administrator-permissions)

이 역할의 사용자는 사용자 위치의 네트워크 원격 분석 데이터를 기반으로 작성되는 Microsoft의 네트워크 경계 아키텍처 권장 사항을 검토할 수 있습니다. Office 365의 네트워크 성능은 일반적으로 사용자 위치와 관련이 있는 신중한 엔터프라이즈 고객 네트워크 경계 아키텍처에 좌우됩니다. 이 역할을 통해 검색 된 사용자 위치를 편집 하 고 해당 위치에 대 한 네트워크 매개 변수를 구성 하 여 원격 분석 측정 및 디자인 권장 사항을 향상할 수 있습니다.
### <a name="office-apps-administrator"></a>[Office 앱 관리자](#office-apps-administrator-permissions)

이 역할의 사용자는 Office 365 앱의 클라우드 설정을 관리할 수 있습니다. 여기에는 클라우드 정책 관리, 셀프 서비스 다운로드 관리, Office 앱 관련 보고서를 살펴보는 기능이 포함됩니다. 이 역할은 주 관리 센터 내에서 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능도 부여합니다. 이 역할이 할당된 사용자는 Office 앱 최신 기능의 통신도 관리할 수도 있습니다. 

### <a name="partner-tier1-support"></a>[파트너 계층1 지원](#partner-tier1-support-permissions)

사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

### <a name="partner-tier2-support"></a>[파트너 계층2 지원](#partner-tier2-support-permissions)

사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

### <a name="password-administrator"></a>[암호 관리자](#password-administrator-permissions)

이 역할의 사용자는 제한적으로 암호를 관리할 수 있습니다. 이 역할은 서비스 요청을 관리하거나 서비스 상태를 모니터링하는 기능을 부여하지 않습니다. 암호 관리자는 관리자가 아니거나 다음 역할의 멤버인 다른 사용자의 암호만 재설정할 수 있습니다.

* 디렉터리 읽기 권한자
* 게스트 초대자
* 암호 관리자

### <a name="power-bi-administrator"></a>[Power BI 관리자](#power-bi-service-administrator-permissions)

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Power BI 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Power BI 관리자 역할 이해](/power-bi/service-admin-role)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Power BI 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Power BI 관리자"입니다.

### <a name="power-platform-administrator"></a>[Power Platform 관리자](#power-platform-administrator-permissions)

이 역할의 사용자는 환경, PowerApps, 흐름, 데이터 손실 방지 정책의 모든 것을 만들고 관리할 수 있습니다. 또한 이 역할이 할당된 사용자는 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다.

### <a name="printer-administrator"></a>[프린터 관리자](#printer-administrator-permissions)

이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록하고 유니버설 인쇄 커넥터 설정을 비롯한 모든 프린터 구성을 관리할 수 있습니다. 모든 위임된 인쇄 권한 요청에 동의할 수 있습니다. 또한 프린터 관리자는 보고서를 인쇄할 수 있습니다.

### <a name="printer-technician"></a>[프린터 기술자](#printer-technician-permissions)

이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록하고 프린터 상태를 관리할 수 있습니다. 또한 모든 커넥터 정보를 읽을 수 있습니다. 프린터 기술자가 할 수 없는 주요 작업은 프린터 및 공유 프린터에 대한 사용자 권한을 설정하는 것입니다.

### <a name="privileged-authentication-administrator"></a>[권한 있는 인증 관리자](#privileged-authentication-administrator-permissions)

이 역할의 사용자는 전역 관리자를 비롯한 모든 사용자의 암호가 아닌 자격 증명을 설정하거나 초기화하고, 모든 사용자의 암호를 업데이트할 수 있습니다. 권한 있는 인증 관리자는 사용자가 기존의 비암호 자격 증명(예: MFA 또는 FIDO)을 다시 등록하고 ‘디바이스에 MFA 저장’을 철회하도록 강제 적용한 다음, 모든 사용자가 다음에 로그인할 때 MFA를 요청할 수 있습니다. [인증 관리자](#authentication-administrator) 역할은 관리자가 아닌 사용자와 다음 Azure AD 역할이 할당된 사용자에게만 다시 등록하고 MFA를 수행하도록 강제할 수 있습니다.

* 인증 관리자
* 디렉터리 읽기 권한자
* 게스트 초대자
* 메시지 센터 읽기 권한자
* 보고서 구독자

### <a name="privileged-role-administrator"></a>[권한 있는 역할 관리자](#privileged-role-administrator-permissions)

이 역할을 가진 사용자는 Azure Active Directory 및 Azure AD Privileged Identity Management에서 역할 할당을 관리할 수 있습니다. Azure AD 역할에 할당 될 수 있는 그룹을 만들고 관리할 수 있습니다. 또한 이 역할을 통해 Privileged Identity Management 및 관리 단위의 모든 것을 관리할 수 있습니다.

> [!IMPORTANT]
> 이 역할은 전역 관리자 역할을 포함하여 모든 Azure AD 역할의 할당을 관리할 수 있습니다. 이 역할은 사용자 생성 또는 업데이트와 같은 Azure AD의 다른 모든 권한 있는 기능을 포함하지는 않습니다. 그러나 이 역할에 할당된 사용자는 추가 역할을 할당하여 본인 또는 다른 사용자에게 추가 권한을 부여할 수 있습니다.

### <a name="reports-reader"></a>[보고서 읽기 권한자](#reports-reader-permissions)

이 역할의 사용자는 Microsoft 365 관리 센터의 사용 현황 보고 데이터 및 보고서 대시보드와 Power BI의 채택 컨텍스트 팩을 볼 수 있습니다. 또한 역할은 Azure AD의 로그온 보고서 및 활동과 함께 Microsoft Graph Reporting API에서 반환되는 데이터에 대한 액세스를 제공합니다. 보고서 구독자 역할에 할당된 사용자는 관련된 사용량 및 채택 메트릭에만 액세스할 수 있습니다. 설정을 구성하거나 Exchange와 같은 제품 특정 관리 센터에 액세스할 수 있는 관리자 권한은 없습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

### <a name="search-administrator"></a>[Search 관리자](#search-administrator-permissions)

이 역할의 사용자는 Microsoft 365 관리 센터의 모든 Microsoft Search 관리 기능에 액세스할 수 있습니다. 또한 이 역할의 사용자는 메시지 센터를 살펴보고, 서비스 상태를 모니터링하고, 서비스 요청을 만들 수 있습니다.

### <a name="search-editor"></a>[Search 편집자](#search-editor-permissions)

이 역할의 사용자는 Microsoft 365 관리 센터에서 책갈피, 질문과 대답, 위치 등 Microsoft Search에 대한 콘텐츠를 만들고, 관리하고, 삭제할 수 있습니다.

### <a name="security-administrator"></a>[보안 관리자](#security-administrator-permissions)

이 역할의 사용자에게는 Microsoft 365 보안 센터, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection, Office 365 보안 및 준수 센터의 보안 관련 기능을 관리할 권한이 있습니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

그런 다음 | 가능한 작업
--- | ---
[Microsoft 365 보안 센터](https://protection.office.com) | Microsoft 365 서비스 전반의 보안 관련 정책 모니터링<br>보안 위협과 경고 관리<br>보고서 보기
ID 보호 센터 | 보안 읽기 권한자 역할의 모든 권한<br>암호 재설정을 제외한 모든 ID 보호 센터 작업을 수행하는 기능도 있음
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 보안 읽기 권한자 역할의 모든 권한<br>Azure AD 역할 할당 또는 설정을 관리할 수 **없음**
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 정책 관리<br>보안 위협 확인/조사/대응<br>보고서 보기
Azure Advanced Threat Protection | 의심스러운 보안 활동 모니터링/대응
Windows Defender ATP 및 EDR | 역할 할당<br>머신 그룹 관리<br>엔드포인트 위협 검색 및 자동 수정 구성<br>경고 확인/조사/대응
[Intune](/intune/role-based-access-control) | 사용자, 디바이스, 등록, 구성 및 애플리케이션 정보 확인<br>Intune을 변경할 수는 없음
[Cloud App Security](/cloud-app-security/manage-admins) | 관리자/정책/설정 추가, 로그 업로드, 거버넌스 작업 수행
[Azure Security Center](../../role-based-access-control/built-in-roles.md) | 보안 정책 보기, 보안 상태 보기, 보안 정책 편집, 경고 및 권장 사항 보기, 경고 및 권장 사항 해제를 수행합니다.
[Office 365 서비스 상태](/office365/enterprise/view-service-health) | Office 365 서비스의 상태 확인
[스마트 잠금](../authentication/howto-password-smart-lockout.md) | 실패한 로그인 이벤트가 발생할 때 잠금의 임계값 및 기간을 정의합니다.
[암호 보호](../authentication/concept-password-ban-bad.md) | 사용자 지정 금지 암호 목록 또는 온-프레미스 암호 보호를 구성합니다.

### <a name="security-operator"></a>[보안 운영자](#security-operator-permissions)

이 역할의 사용자는 경고를 관리할 수 있으며 Microsoft 365 보안 센터, Azure Active Directory, Identity Protection, Privileged Identity Management, Office 365 보안 및 준수 센터의 모든 정보를 포함하여 보안 관련 기능에 대한 글로벌 읽기 전용 액세스 권한이 있습니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)에서 제공됩니다.

그런 다음 | 가능한 작업
--- | ---
[Microsoft 365 보안 센터](https://protection.office.com) | 보안 읽기 권한자 역할의 모든 권한<br>보안 위협 경고 확인/조사/대응
ID 보호 센터 | 보안 읽기 권한자 역할의 모든 권한<br>암호 재설정을 제외한 모든 ID 보호 센터 작업을 수행하는 기능도 있음
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 보안 읽기 권한자 역할의 모든 권한
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 읽기 권한자 역할의 모든 권한<br>보안 경고 확인/조사/대응
Windows Defender ATP 및 EDR | 보안 읽기 권한자 역할의 모든 권한<br>보안 경고 확인/조사/대응
[Intune](/intune/role-based-access-control) | 보안 읽기 권한자 역할의 모든 권한
[Cloud App Security](/cloud-app-security/manage-admins) | 보안 읽기 권한자 역할의 모든 권한
[Office 365 서비스 상태](/office365/enterprise/view-service-health) | Office 365 서비스의 상태 확인

### <a name="security-reader"></a>[보안 판독기](#security-reader-permissions)

이 역할의 사용자에게는 Microsoft 365 보안 센터, Azure Active Directory, ID 보호, Privileged Identity Management의 모든 정보를 비롯한 보안 관련 기능에 대한 전역 읽기 전용 액세스 권한이 있습니다. 또한 Azure Active Directory 로그인 보고서와 감사 로그 읽기 권한 및 Office 365 보안 및 준수 센터의 읽기 권한도 있습니다. Office 365 사용 권한에 대한 자세한 정보는 [Office 365 보안 및 규정 준수 센터의 사용 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에서 제공됩니다.

그런 다음 | 가능한 작업
--- | ---
[Microsoft 365 보안 센터](https://protection.office.com) | Microsoft 365 서비스 전반에서 보안 관련 정책 확인<br>보안 위협 및 경고 확인<br>보고서 보기
ID 보호 센터 | 모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<br><ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Azure AD Privileged Identity Management에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한이 있습니다. Azure AD 역할 할당 및 보안 검토에 대한 정책 및 보고서.<br>Azure AD Privileged Identity Management에 가입하거나 변경할 수 **없습니다**. 이 역할에 속한 사람은 Privileged Identity Management 포털 또는 PowerShell에서 적격 사용자를 대상으로 전역 관리자, 권한 있는 역할 관리자 등의 추가 역할을 활성화할 수 있습니다.
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 정책 보기<br>보안 위협 확인/조사<br>보고서 보기
Windows Defender ATP 및 EDR | 경고 확인/조사 Windows Defender ATP에서 역할 기반 액세스 제어를 켜면 Azure AD 보안 읽기 권한자 역할처럼 읽기 전용 권한이 있는 사용자는 Windows Defender ATP 역할에 할당될 때까지 액세스 권한을 잃게 됩니다.
[Intune](/intune/role-based-access-control) | 사용자, 디바이스, 등록, 구성 및 애플리케이션 정보 확인. Intune을 변경할 수는 없음
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 전용 권한 소유/경고를 관리할 수 있음
[Azure Security Center](../../role-based-access-control/built-in-roles.md) | 권장 사항 및 경고를 보고, 보안 정책을 보고, 보안 상태를 볼 수 있지만 변경할 수는 없습니다.
[Office 365 서비스 상태](/office365/enterprise/view-service-health) | Office 365 서비스의 상태 확인

### <a name="service-support-administrator"></a>[서비스 지원 관리자](#service-support-administrator-permissions)

이 역할의 사용자는 Microsoft for Azure 및 Office 365 서비스에서 지원 요청을 열 수 있으며, [Azure Portal](https://portal.azure.com) 및 [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 서비스 대시보드 및 메시지 센터를 볼 수 있습니다. 자세한 내용은 [관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> 이전에는 [Azure Portal](https://portal.azure.com) 및 [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 이 역할을 "서비스 관리자"라고 했습니다. Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell의 기존 이름과 맞추기 위해 "서비스 지원 관리자"로 변경했습니다.

### <a name="sharepoint-administrator"></a>[SharePoint 관리자](#sharepoint-service-administrator-permissions)

이 역할을 가진 사용자는 서비스가 있는 경우 Microsoft SharePoint Online 내에서 전역 사용 권한을 가지 며, 모든 Microsoft 365 그룹을 생성 및 관리 하 고, 지원 티켓을 관리 하 고, 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "SharePoint 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "SharePoint 관리자"입니다.

> [!NOTE]
> 또한이 역할은 Microsoft Intune에 대 한 Microsoft Graph API에 범위 지정 된 사용 권한을 부여 하 여 SharePoint 및 OneDrive 리소스와 관련 된 정책을 관리 하 고 구성할 수 있도록 합니다.

### <a name="skype-for-business--lync-administrator"></a>[비즈니스용 Skype/Lync 관리자](#lync-service-administrator-permissions)

이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 글로벌 사용 권한을 가질 뿐만 아니라 Azure Active Directory에서 Skype 관련 사용자 특성을 관리할 수 있습니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하고, Teams 및 Business용 Skype 관리 센터에 액세스하는 기능을 부여합니다. 계정에는 Teams에 대한 라이선스가 있어야 합니다. 그렇지 않으면 Teams PowerShell cmdlet을 실행할 수 없습니다. [비즈니스용 Skype 관리자 역할 정보](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)의 자세한 내용 및 [비즈니스용 Skype 및 Microsoft Teams 추가 기능 라이선스](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)의 Teams 라이선스 정보

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Lync 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서는 “비즈니스용 Skype 관리자”입니다.

### <a name="teams-communications-administrator"></a>[Teams 통신 관리자](#teams-communications-administrator-permissions)

이 역할의 사용자는 음성 및 전화 통신과 관련된 Microsoft Teams 워크로드의 측면을 관리할 수 있습니다. 여기에는 전화 번호 할당, 음성 및 회의 정책 및 호출 분석 도구 집합에 대한 전체 액세스를 위한 관리 도구가 포함됩니다.

### <a name="teams-communications-support-engineer"></a>[Teams 통신 지원 엔지니어](#teams-communications-support-engineer-permissions)

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 관련된 모든 참가자에 대한 전체 호출 레코드 정보를 볼 수 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

### <a name="teams-communications-support-specialist"></a>[Teams 통신 지원 전문가](#teams-communications-support-specialist-permissions)

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 조회하는 특정 사용자에 대한 호출에서 사용자 세부 정보를 보기만 할 수 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

### <a name="teams-service-administrator"></a>[Teams 서비스 관리자](#teams-service-administrator-permissions)

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터와 해당하는 PowerShell 모듈을 통해 Microsoft Teams 워크로드의 모든 측면을 관리할 수 있습니다. 여기에는 다른 영역 중 전화 통신, 메시징, 회의 및 팀 자체와 관련된 모든 관리 도구가 포함됩니다. 이 역할은 모든 Microsoft 365 그룹을 생성 및 관리 하 고, 지원 티켓을 관리 하 고, 서비스 상태를 모니터링 하는 기능을 추가로 부여 합니다.

### <a name="user-administrator"></a>[사용자 관리자](#user-administrator-permissions)

이 역할을 가진 사용자는 사용자를 만들고 몇 가지 제한 사항이 있는 사용자의 모든 측면을 관리할 수 있습니다 (표 참조). 암호 만료 정책을 업데이트할 수 있습니다. 또한 이 역할의 사용자는 모든 그룹을 만들고 관리할 수 있습니다. 이 역할은 사용자 보기를 만들고 관리하며, 지원 티켓을 관리하고, 서비스 상태를 모니터링하는 기능도 포함합니다. 사용자 관리자는 대부분의 관리자 역할에 속한 사용자의 일부 사용자 속성을 관리할 권한이 없습니다. 이 역할의 사용자는 MFA를 관리할 권한이 없습니다. 다음 표에는 이 제한이 적용되지 않는 예외적인 역할이 나열되어 있습니다.

| **사용 권한** | **가능한 작업** |
| --- | --- |
|일반적인 사용 권한|<p>사용자 및 그룹 만들기</p><p>사용자 보기 만들기 및 관리</p><p>Office 지원 티켓 관리<p>암호 만료 정책 업데이트|
| <p>모든 관리자를 포함한 모든 사용자에게</p>|<p>라이선스 관리</p><p>사용자 계정 이름을 제외한 모든 사용자 속성 관리</p>
| 비관리자 또는 다음의 제한된 관리자 역할의 사용자에만 적용:<ul><li>디렉터리 읽기 권한자<li>그룹 관리자<li>게스트 초대자<li>기술 지원팀 관리자<li>메시지 센터 읽기 권한자<li>암호 관리자<li>보고서 구독자<li>사용자 관리자|<p>삭제 및 복원</p><p>사용 안 함 및 사용</p><p>새로 고침 토큰 무효화</p><p>사용자 계정 이름을 포함한 모든 사용자 속성 관리</p><p>암호 재설정</p><p>(FIDO) 디바이스 키 업데이트</p>|

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 다음은 그 예입니다.
>
>- 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 사용자 관리자에게 권한이 부여되지 않습니다. 이 경로를 통해 사용자 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>- Azure 구독 소유자: Azure에서 중요한 프라이빗 정보 또는 중요한 구성에 액세스할 수 있습니다.
>- 그룹 멤버 자격을 관리할 수 있는 보안 그룹 및 Microsoft 365 그룹 소유자입니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>- Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>- 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

## <a name="role-permissions"></a>역할 권한

다음 표에서는 각 역할에 부여되는 Azure Active Directory의 특정 권한에 대해 설명합니다. 일부 역할에는 Azure Active Directory 외부의 Microsoft 서비스에 대한 추가 사용 권한이 있을 수 있습니다.

### <a name="application-administrator-permissions"></a>애플리케이션 관리자 권한

앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Azure Active Directory에서 서비스 주체의 앱 프록시 인증 속성을 업데이트합니다. |
| microsoft.directory/Application/appProxyUrlSettings/update | Azure Active Directory에서 애플리케이션 프록시 내부 및 외부 URL을 업데이트합니다. |
| microsoft.directory/applications/applicationProxy/read | 모든 앱 프록시 속성을 읽습니다. |
| microsoft.directory/applications/applicationProxy/update | 모든 앱 프록시 속성을 업데이트합니다. |
| microsoft.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.directory/applications/create | Azure Active Directory에서 애플리케이션을 만듭니다. |
| microsoft.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.directory/appRoleAssignments/read | Azure Active Directory에서 appRoleAssignments를 읽습니다. |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft. directory/Congroups/allProperties/read | Azure Active Directory에서 애플리케이션 프록시 커넥터 그룹 속성을 읽습니다. |
| microsoft. directory/Congroups/allProperties/update | Azure Active Directory에서 모든 애플리케이션 프록시 커넥터 그룹 속성을 업데이트합니다. |
| microsoft.directory/connectorGroups/create | Azure Active Directory에서 애플리케이션 프록시 커넥터 그룹을 만듭니다. |
| microsoft.directory/connectorGroups/delete | Azure Active Directory에서 애플리케이션 프록시 커넥터 그룹을 삭제합니다. |
| microsoft. directory/커넥터/allProperties/read | Azure Active Directory에서 모든 애플리케이션 프록시 커넥터 속성을 읽습니다. |
| microsoft.directory/connectors/create | Azure Active Directory에서 애플리케이션 프록시 커넥터를 만듭니다. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.directory/policies/applicationConfiguration/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.directory/policies/applicationConfiguration/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="application-developer-permissions"></a>애플리케이션 개발자 권한

'사용자가 애플리케이션을 등록할 수 있음' 설정에 관계없이 애플리케이션 등록을 만들 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Azure Active Directory에서 애플리케이션을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/appRoleAssignments/createAsOwner | Azure Active Directory에서 appRoleAssignments를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory에서 oAuth2PermissionGrants를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/servicePrincipals/createAsOwner | Azure Active Directory에서 servicePrincipals를 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |

### <a name="authentication-administrator-permissions"></a>인증 관리자 권한

관리 사용자가 아닌 사용자의 인증 방법 정보를 보고, 설정하고, 다시 설정할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/strongAuthentication/update | MFA 자격 증명 정보와 같은 강력한 인증 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/users/password/update | Office 365 조직에 포함된 모든 사용자의 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |

### <a name="azure-devops-administrator-permissions"></a>Azure DevOps 개발자 권한

Azure DevOps 조직 정책 및 설정을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 [역할 설명](#azure-devops-administrator)을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Azure DevOps를 읽고 구성합니다. |

### <a name="azure-information-protection-administrator-permissions"></a>Azure Information Protection 관리자 권한

Azure Information Protection 서비스의 모든 것을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 [역할 설명](#)을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>B2C IEF 키 세트 관리자 권한

Identity Experience Framework에서 페더레이션 및 암호화용 비밀을 관리합니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Azure Active Directory B2C에서 키 세트를 읽고 구성합니다. |

### <a name="b2c-ief-policy-administrator-permissions"></a>B2C IEF 정책 관리자 권한

Identity Experience Framework에서 보안 프레임워크 정책을 만들고 관리합니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Azure Active Directory B2C에서 사용자 지정 정책을 읽고 구성합니다. |

### <a name="billing-administrator-permissions"></a>대금 청구 관리자 권한

결제 정보 업데이트와 같은 일반 결제 관련 작업을 수행할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/organization/basic/update | Azure Active Directory에서 조직의 기본 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.commerce.billing/allEntities/allTasks | 청구와 관련된 모든 것을 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="cloud-application-administrator-permissions"></a>클라우드 애플리케이션 관리자 권한

앱 프록시를 제외한 앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.directory/applications/create | Azure Active Directory에서 애플리케이션을 만듭니다. |
| microsoft.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/policies/applicationConfiguration/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.directory/policies/applicationConfiguration/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Azure Active Directory에서 policies.applicationConfiguration 속성을 업데이트합니다. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory에서 policies.applicationConfiguration 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="cloud-device-administrator-permissions"></a>클라우드 디바이스 관리자 권한

Azure AD에서 디바이스를 관리하기 위한 모든 권한입니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/devices/delete | Azure Active Directory에서 디바이스를 삭제합니다. |
| microsoft.directory/devices/disable | Azure Active Directory에서 디바이스를 사용하지 않도록 설정합니다. |
| microsoft.directory/devices/enable | Azure Active Directory에서 디바이스를 사용하도록 설정합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="company-administrator-permissions"></a>회사 관리자 권한

Azure AD 및 Azure AD ID를 사용하는 Microsoft 서비스의 모든 측면을 관리할 수 있습니다. 이 역할을 전역 관리자 역할이라고도 합니다. 

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | microsoft.aad.cloudAppSecurity에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Azure Active Directory에서 administrativeUnits를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/applications/allProperties/allTasks | Azure Active Directory에서 애플리케이션을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Azure Active Directory에서 appRoleAssignments를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/contacts/allProperties/allTasks | Azure Active Directory에서 연락처를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/contracts/allProperties/allTasks | Azure Active Directory에서 계약을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/devices/allProperties/allTasks | Azure Active Directory에서 디바이스를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Azure Active Directory에서 directoryRoles를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure Active Directory에서 directoryRoleTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/domains/allProperties/allTasks | Azure Active Directory에서 도메인을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/groups/allProperties/allTasks | Azure Active Directory에서 그룹을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft. directory/groupsAssignableToRoles/allProperties/update | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정한 그룹을 업데이트 합니다. |
| microsoft. directory/groupsAssignableToRoles/create | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정 하 여 그룹을 만듭니다. |
| microsoft. directory/groupsAssignableToRoles/delete | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정한 그룹을 삭제 합니다. |
| microsoft.directory/groupSettings/allProperties/allTasks | Azure Active Directory에서 groupSettings를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Azure Active Directory에서 groupSettingTemplates를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Azure Active Directory에서 loginTenantBranding을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Azure Active Directory에서 oAuth2PermissionGrants를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/organization/allProperties/allTasks | Azure Active Directory에서 조직을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/policies/allProperties/allTasks | Azure Active Directory에서 정책을 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Azure Active Directory에서 roleAssignments를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Azure Active Directory에서 roleDefinitions를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Azure Active Directory에서 scopedRoleMemberships를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/serviceAction/activateService | Azure Active Directory에서 Activateservice 서비스 작업을 수행할 수 있습니다. |
| microsoft.directory/serviceAction/disableDirectoryFeature | Azure Active Directory에서 Disabledirectoryfeature 서비스 작업을 수행할 수 있습니다. |
| microsoft.directory/serviceAction/enableDirectoryFeature | Azure Active Directory에서 Enabledirectoryfeature 서비스 작업을 수행할 수 있습니다. |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory에서 Getavailableextentionproperties 서비스 작업을 수행할 수 있습니다. |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory에서 servicePrincipals를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Azure Active Directory에서 subscribedSkus를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directory/users/allProperties/allTasks | Azure Active Directory에서 사용자를 만들고 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.directorySync/allEntities/allTasks | Azure AD Connect에서 모든 작업을 수행합니다. |
| microsoft.aad.identityProtection/allEntities/allTasks | microsoft.aad.identityProtection에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.azure.advancedThreatProtection/allEntities/read | microsoft.azure.advancedThreatProtection에서 모든 리소스를 읽습니다. |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.commerce.billing/allEntities/allTasks | 청구와 관련된 모든 것을 관리합니다. |
| microsoft.intune/allEntities/allTasks | Intune의 모든 측면을 관리합니다. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 데스크톱 분석의 모든 측면을 관리합니다. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 고객 Lockbox의 모든 측면을 관리합니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.messageCenter/securityMessages/read | microsoft.office365.messageCenter에서 securityMessages를 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Office 365 보호 센터의 모든 측면을 관리합니다. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | microsoft.office365.securityComplianceCenter에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면을 관리합니다. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면을 관리합니다. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | microsoft.windows.defenderAdvancedThreatProtection에서 모든 리소스를 읽습니다. |

### <a name="compliance-administrator-permissions"></a>규정 준수 관리자 권한

Azure AD 및 Office 365에서 준수 구성 및 보고서를 읽고 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="compliance-data-administrator-permissions"></a>규정 준수 데이터 관리자 권한

규정 준수 콘텐츠를 만들고 관리합니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft. directory. cloudAppSecurity/allEntities/Allentities | Microsoft Cloud App Security를 읽고 구성합니다. |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면을 관리합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="conditional-access-administrator-permissions"></a>조건부 액세스 관리자 권한

조건부 액세스 기능을 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.directory/policies/conditionalAccess/basic/update | Azure Active Directory에서 policies.conditionalAccess 속성을 업데이트합니다. |
| microsoft.directory/policies/conditionalAccess/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.directory/policies/conditionalAccess/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/conditionalAccess/owners/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.directory/policies/conditionalAccess/owners/update | Azure Active Directory에서 policies.conditionalAccess 속성을 업데이트합니다. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Azure Active Directory에서 policies.conditionalAccess 속성을 업데이트합니다. |

### <a name="crm-service-administrator-permissions"></a>CRM 서비스 관리자 권한

Dynamics 365 제품의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면을 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="customer-lockbox-access-approver-permissions"></a>고객 LockBox 액세스 승인자 권한

고객 조직 데이터에 액세스하려는 Microsoft 지원 요청을 승인할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 고객 Lockbox의 모든 측면을 관리합니다. |

### <a name="desktop-analytics-administrator-permissions"></a>Desktop Analytics 관리자 권한

Desktop Analytics 서비스와 Office Customization & Policy 서비스를 관리할 수 있습니다. Desktop Analytics의 경우 여기에는 자산 인벤토리를 확인하고, 배포 계획을 만들고, 배포 및 상태를 볼 수 있는 기능이 포함됩니다. Office Customization & Policy 서비스의 경우 이 역할의 사용자는 Office 정책을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 데스크톱 분석의 모든 측면을 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="device-administrators-permissions"></a>디바이스 관리자 권한

이 역할에 할당된 사용자는 Azure AD에 조인된 디바이스의 로컬 관리자 그룹에 추가됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Azure Active Directory에서 groupSettings의 기본 속성을 읽습니다. |
| microsoft.directory/groupSettingTemplates/basic/read | Azure Active Directory에서 groupSettingTemplates의 기본 속성을 읽습니다. |

### <a name="directory-readers-permissions"></a>디렉터리 읽기 권한자 권한
기본 디렉터리 정보를 읽을 수 있습니다. 애플리케이션에 대한 액세스 권한은 사용자를 위한 것이 아닙니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Azure Active Directory에서 administrativeUnits의 기본 속성을 읽습니다. |
| microsoft.directory/administrativeUnits/members/read | Azure Active Directory에서 administrativeUnits.members 속성을 읽습니다. |
| microsoft.directory/applications/basic/read | Azure Active Directory에서 애플리케이션의 기본 속성을 읽습니다. |
| microsoft.directory/applications/owners/read | Azure Active Directory에서 applications.owners 속성을 읽습니다. |
| microsoft.directory/applications/policies/read | Azure Active Directory에서 applications.policies 속성을 읽습니다. |
| microsoft.directory/contacts/basic/read | Azure Active Directory에서 연락처의 표준 속성을 읽습니다. |
| microsoft.directory/contacts/memberOf/read | Azure Active Directory에서 contacts.memberOf 속성을 읽습니다. |
| microsoft.directory/contracts/basic/read | Azure Active Directory에서 계약의 기본 속성을 읽습니다. |
| microsoft.directory/devices/basic/read | Azure Active Directory에서 디바이스의 기본 속성을 읽습니다. |
| microsoft.directory/devices/memberOf/read | Azure Active Directory에서 devices.memberOf 속성을 읽습니다. |
| microsoft.directory/devices/registeredOwners/read | Azure Active Directory에서 devices.registeredOwners 속성을 읽습니다. |
| microsoft.directory/devices/registeredUsers/read | Azure Active Directory에서 devices.registeredUsers 속성을 읽습니다. |
| microsoft.directory/directoryRoles/basic/read | Azure Active Directory에서 directoryRoles의 기본 속성을 읽습니다. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Azure Active Directory에서 directoryRoles.eligibleMembers 속성을 읽습니다. |
| microsoft.directory/directoryRoles/members/read | Azure Active Directory에서 directoryRoles.members 속성을 읽습니다. |
| microsoft.directory/domains/basic/read | Azure Active Directory에서 도메인의 기본 속성을 읽습니다. |
| microsoft.directory/groups/appRoleAssignments/read | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 읽습니다. |
| microsoft.directory/groups/basic/read | Azure Active Directory에서 그룹의 기본 속성을 읽습니다. |
| microsoft.directory/groups/memberOf/read | Azure Active Directory에서 groups.memberOf 속성을 읽습니다. |
| microsoft.directory/groups/members/read | Azure Active Directory에서 groups.members 속성을 읽습니다. |
| microsoft.directory/groups/owners/read | Azure Active Directory에서 groups.owners 속성을 읽습니다. |
| microsoft.directory/groups/settings/read | Azure Active Directory에서 groups.settings 속성을 읽습니다. |
| microsoft.directory/groupSettings/basic/read | Azure Active Directory에서 groupSettings의 기본 속성을 읽습니다. |
| microsoft.directory/groupSettingTemplates/basic/read | Azure Active Directory에서 groupSettingTemplates의 기본 속성을 읽습니다. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 oAuth2PermissionGrants의 기본 속성을 읽습니다. |
| microsoft.directory/organization/basic/read | Azure Active Directory에서 조직의 기본 속성을 읽습니다. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory에서 organization.trustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.directory/roleAssignments/basic/read | Azure Active Directory에서 roleAssignments의 기본 속성을 읽습니다. |
| microsoft.directory/roleDefinitions/basic/read | Azure Active Directory에서 roleDefinitions의 기본 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/basic/read | Azure Active Directory에서 servicePrincipals의 기본 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/memberOf/read | Azure Active Directory에서 servicePrincipals.memberOf 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Azure Active Directory에서 servicePrincipals.ownedObjects 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/owners/read | Azure Active Directory에서 servicePrincipals.owners 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/policies/read | Azure Active Directory에서 servicePrincipals.policies 속성을 읽습니다. |
| microsoft.directory/subscribedSkus/basic/read | Azure Active Directory에서 subscribedSkus의 기본 속성을 읽습니다. |
| microsoft.directory/users/appRoleAssignments/read | Azure Active Directory에서 users.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/users/basic/read | Azure Active Directory에서 사용자의 기본 속성을 읽습니다. |
| microsoft.directory/users/directReports/read | Azure Active Directory에서 users.directReports 속성을 읽습니다. |
| microsoft.directory/users/manager/read | Azure Active Directory에서 users.manager 속성을 읽습니다. |
| microsoft.directory/users/memberOf/read | Azure Active Directory에서 users.memberOf 속성을 읽습니다. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 users.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/users/ownedDevices/read | Azure Active Directory에서 users.ownedDevices 속성을 읽습니다. |
| microsoft.directory/users/ownedObjects/read | Azure Active Directory에서 users.ownedObjects 속성을 읽습니다. |
| microsoft.directory/users/registeredDevices/read | Azure Active Directory에서 users.registeredDevices 속성을 읽습니다. |

### <a name="directory-synchronization-accounts-permissions"></a>디렉터리 동기화 계정 권한

Azure AD Connect에서만 사용됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Azure Active Directory에서 organization.dirSync 속성을 업데이트합니다. |
| microsoft.directory/policies/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/basic/read | Azure Active Directory에서 정책의 기본 속성을 읽습니다. |
| microsoft.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.directory/policies/owners/read | Azure Active Directory에서 policies.owners 속성을 읽습니다. |
| microsoft.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.directory/policies/policiesAppliedTo/read | Azure Active Directory에서 policies.policiesAppliedTo 속성을 읽습니다. |
| microsoft.directory/policies/tenantDefault/update | Azure Active Directory에서 policies.tenantDefault 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/read | Azure Active Directory에서 servicePrincipals의 기본 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/memberOf/read | Azure Active Directory에서 servicePrincipals.memberOf 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/owners/read | Azure Active Directory에서 servicePrincipals.owners 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Azure Active Directory에서 servicePrincipals.ownedObjects 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/read | Azure Active Directory에서 servicePrincipals.policies 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directorySync/allEntities/allTasks | Azure AD Connect에서 모든 작업을 수행합니다. |

### <a name="directory-writers-permissions"></a>디렉터리 쓰기 권한자 권한

기본 디렉터리 정보를 읽고 쓸 수 있습니다. 애플리케이션에 대한 액세스 권한은 사용자를 위한 것이 아닙니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft. 디렉터리/그룹/할당 라이선스 | Azure Active Directory의 그룹에 대 한 라이선스를 관리 합니다. |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다.  |
| microsoft. 디렉터리/그룹/분류/업데이트 | Azure Active Directory 그룹의 분류 속성을 업데이트 합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft. directory/groups/groupType/update | Azure Active Directory 그룹의 groupType 속성을 업데이트 합니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft. directory/groups/reprocessLicenseAssignment | Azure Active Directory 그룹에 대 한 라이선스 할당을 다시 처리 합니다. |
| microsoft. directory/groups/securityEnabled/update | Azure Active Directory 그룹의 secutiryEnabled 속성을 업데이트 합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft. 디렉터리/그룹/표시/업데이트 | 그룹의 표시 유형 속성 업데이트 |
| microsoft.directory/groupSettings/basic/update | Azure Active Directory에서 groupSettings의 기본 속성을 업데이트합니다. |
| microsoft.directory/groupSettings/create | Azure Active Directory에서 groupSettings를 만듭니다. |
| microsoft.directory/groupSettings/delete | Azure Active Directory에서 groupSettings를 삭제합니다. |
| microsoft. directory/oAuth2PermissionGrants/basic/update | Azure Active Directory에서 oAuth2PermissionGrants의 기본 속성을 업데이트 합니다. |
| microsoft. directory/oAuth2PermissionGrants/create | Azure Active Directory에서 oAuth2PermissionGrants를 만듭니다. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 응용 프로그램 프로 비전 비밀 및 자격 증명을 관리 합니다. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | 응용 프로그램 프로 비전 동기화 작업을 시작, 다시 시작 및 일시 중지 합니다. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | 응용 프로그램 프로 비전 동기화 작업 및 스키마를 만들고 관리 합니다. |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft. 디렉터리/사용자/사용 안 함 | Azure Active Directory에서 사용자 계정을 사용 하지 않도록 설정 합니다. |
| microsoft. 디렉터리/사용자/사용 | Azure Active Directory에서 사용자 계정 사용 |
| microsoft.directory/users/invalidateAllRefreshTokens | 사용자가 다음 번에 로그인 할 때 다시 인증 하도록 요구 하는 Azure Active Directory의 모든 사용자 새로 고침 토큰을 무효화 합니다. |
| microsoft.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft. directory/users/reprocessLicenseAssignment | Azure Active Directory에서 사용자에 대 한 라이선스 할당을 다시 처리 합니다. |
| microsoft. directory/users/userPrincipalName/update | Azure Active Directory에서 userPrincipalName 속성을 업데이트 합니다. |

### <a name="exchange-service-administrator-permissions"></a>Exchange 서비스 관리자 권한

Exchange 제품의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 그룹의 기본 속성을 업데이트 합니다. |
| microsoft.directory/groups/unified/create | Microsoft 365 그룹을 만듭니다. |
| microsoft.directory/groups/unified/delete | Microsoft 365 그룹을 삭제 합니다. |
| microsoft.directory/groups/unified/members/update | Microsoft 365 그룹의 멤버 자격을 업데이트 합니다. |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 그룹의 소유권을 업데이트 합니다. |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online의 모든 측면을 관리합니다. |
| microsoft.office365.network/performance/allProperties/read | M365 관리 센터의 네트워크 성능 페이지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="external-id-user-flow-administrator-permissions"></a>외부 ID 사용자 흐름 관리자 권한

사용자 흐름과 관련된 모든 것을 만들고 관리합니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Azure Active Directory B2C에서 사용자 흐름을 읽고 구성합니다. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>외부 ID 사용자 흐름 특성 관리자 권한

모든 사용자 흐름에 사용할 수 있는 특성 스키마를 만들고 관리합니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Azure Active Directory B2C에서 사용자 특성을 읽고 구성합니다. |

### <a name="external-identity-provider-administrator-permissions"></a>외부 ID 공급자 관리자 권한

직접 페더레이션에서 사용할 ID 공급자를 구성합니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Azure Active Directory B2C에서 ID 공급자를 읽고 구성합니다. |

### <a name="global-reader-permissions"></a>글로벌 읽기 권한자 권한
전역 관리자가 읽을 수 있는 모든 것을 읽을 수 있지만, 아무 것도 업데이트할 수 없습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 [역할 설명](#global-reader)을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | 청구와 관련된 모든 것을 읽습니다. |
| microsoft.directory/administrativeUnits/basic/read    | Azure Active Directory에서 administrativeUnits의 기본 속성을 읽습니다. |
| microsoft.directory/administrativeUnits/members/read    | Azure Active Directory에서 administrativeUnits.members 속성을 읽습니다. |
| microsoft.directory/applications/basic/read    | Azure Active Directory에서 애플리케이션의 기본 속성을 읽습니다. |
| microsoft.directory/applications/owners/read    | Azure Active Directory에서 applications.owners 속성을 읽습니다. |
| microsoft.directory/applications/policies/read    | Azure Active Directory에서 applications.policies 속성을 읽습니다. |
| microsoft.directory/contacts/basic/read    | Azure Active Directory에서 연락처의 표준 속성을 읽습니다. |
| microsoft.directory/contacts/memberOf/read    | Azure Active Directory에서 contacts.memberOf 속성을 읽습니다. |
| microsoft.directory/contracts/basic/read    | Azure Active Directory에서 계약의 기본 속성을 읽습니다. |
| microsoft.directory/devices/basic/read    | Azure Active Directory에서 디바이스의 기본 속성을 읽습니다. |
| microsoft.directory/devices/memberOf/read    | Azure Active Directory에서 devices.memberOf 속성을 읽습니다. |
| microsoft.directory/devices/registeredOwners/read    | Azure Active Directory에서 devices.registeredOwners 속성을 읽습니다. |
| microsoft.directory/devices/registeredUsers/read    | Azure Active Directory에서 devices.registeredUsers 속성을 읽습니다. |
| microsoft.directory/directoryRoles/basic/read    | Azure Active Directory에서 directoryRoles의 기본 속성을 읽습니다. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Azure Active Directory에서 directoryRoles.eligibleMembers 속성을 읽습니다. |
| microsoft.directory/directoryRoles/members/read    | Azure Active Directory에서 directoryRoles.members 속성을 읽습니다. |
| microsoft.directory/domains/basic/read    | Azure Active Directory에서 도메인의 기본 속성을 읽습니다. |
| microsoft.directory/groups/appRoleAssignments/read    | Azure Active Directory에서 Groups.AppRoleAssignments 속성을 읽습니다. |
| microsoft.directory/groups/basic/read    | Azure Active Directory에서 그룹의 기본 속성을 읽습니다. |
| microsoft.directory/groups/hiddenMembers/read    | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.directory/groups/memberOf/read    | Azure Active Directory에서 groups.memberOf 속성을 읽습니다. |
| microsoft.directory/groups/members/read    | Azure Active Directory에서 groups.members 속성을 읽습니다. |
| microsoft.directory/groups/owners/read    | Azure Active Directory에서 groups.owners 속성을 읽습니다. |
| microsoft.directory/groups/settings/read    | Azure Active Directory에서 groups.settings 속성을 읽습니다. |
| microsoft.directory/groupSettings/basic/read    | Azure Active Directory에서 groupSettings의 기본 속성을 읽습니다. |
| microsoft.directory/groupSettingTemplates/basic/read    | Azure Active Directory에서 groupSettingTemplates의 기본 속성을 읽습니다. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Azure Active Directory에서 oAuth2PermissionGrants의 기본 속성을 읽습니다. |
| microsoft.directory/organization/basic/read    | Azure Active Directory에서 조직의 기본 속성을 읽습니다. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Azure Active Directory에서 organization.trustedCAsForPasswordlessAuth 속성을 읽습니다. |
| microsoft.directory/policies/standard/read    | Azure Active Directory에서 표준 정책을 읽습니다. |
| microsoft.directory/roleAssignments/basic/read    | Azure Active Directory에서 roleAssignments의 기본 속성을 읽습니다. |
| microsoft.directory/roleDefinitions/basic/read    | Azure Active Directory에서 roleDefinitions의 기본 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Azure Active Directory에서 servicePrincipals.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/basic/read    | Azure Active Directory에서 servicePrincipals의 기본 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/memberOf/read    | Azure Active Directory에서 servicePrincipals.memberOf 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Azure Active Directory에서 servicePrincipals.ownedObjects 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/owners/read    | Azure Active Directory에서 servicePrincipals.owners 속성을 읽습니다. |
| microsoft.directory/servicePrincipals/policies/read    | Azure Active Directory에서 servicePrincipals.policies 속성을 읽습니다. |
| microsoft.directory/signInReports/allProperties/read    | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/subscribedSkus/basic/read    | Azure Active Directory에서 subscribedSkus의 기본 속성을 읽습니다. |
| microsoft.directory/users/appRoleAssignments/read    | Azure Active Directory에서 users.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/users/basic/read    | Azure Active Directory에서 사용자의 기본 속성을 읽습니다. |
| microsoft.directory/users/directReports/read    | Azure Active Directory에서 users.directReports 속성을 읽습니다. |
| microsoft.directory/users/manager/read    | Azure Active Directory에서 users.manager 속성을 읽습니다. |
| microsoft.directory/users/memberOf/read    | Azure Active Directory에서 users.memberOf 속성을 읽습니다. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Azure Active Directory에서 users.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/users/ownedDevices/read    | Azure Active Directory에서 users.ownedDevices 속성을 읽습니다. |
| microsoft.directory/users/ownedObjects/read    | Azure Active Directory에서 users.ownedObjects 속성을 읽습니다. |
| microsoft.directory/users/registeredDevices/read    | Azure Active Directory에서 users.registeredDevices 속성을 읽습니다. |
| microsoft.directory/users/strongAuthentication/read    | MFA 자격 증명 정보와 같은 강력한 인증 속성을 읽습니다. |
| microsoft.office365.exchange/allEntities/read    | Exchange Online의 모든 것을 읽습니다. |
| microsoft.office365.messageCenter/messages/read    | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.messageCenter/securityMessages/read    | microsoft.office365.messageCenter에서 securityMessages를 읽습니다. |
| microsoft.office365.network/performance/allProperties/read | M365 관리 센터의 네트워크 성능 페이지를 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/read    | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | microsoft.office365.securityComplianceCenter에서 모든 표준 속성을 읽습니다. |
| microsoft.office365.usageReports/allEntities/read    | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/standard/read    | microsoft.office365.webPortal에서 모든 리소스에 대한 표준 속성을 읽습니다. |

### <a name="groups-administrator-permissions"></a>그룹 관리자 권한
명명 정책이나 만료 정책과 같은 그룹 및 그룹 설정과 관련된 모든 것을 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/groups/basic/read | Azure Active Directory에서 그룹의 표준 속성을 읽습니다.  |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="guest-inviter-permissions"></a>게스트 초대자 권한
'멤버가 게스트를 초대할 수 있음' 설정에 관계없이 게스트 사용자를 초대할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Azure Active Directory에서 users.appRoleAssignments 속성을 읽습니다. |
| microsoft.directory/users/basic/read | Azure Active Directory에서 사용자의 기본 속성을 읽습니다. |
| microsoft.directory/users/directReports/read | Azure Active Directory에서 users.directReports 속성을 읽습니다. |
| microsoft.directory/users/inviteGuest | Azure Active Directory에서 게스트 사용자를 초대합니다. |
| microsoft.directory/users/manager/read | Azure Active Directory에서 users.manager 속성을 읽습니다. |
| microsoft.directory/users/memberOf/read | Azure Active Directory에서 users.memberOf 속성을 읽습니다. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Azure Active Directory에서 users.oAuth2PermissionGrants 속성을 읽습니다. |
| microsoft.directory/users/ownedDevices/read | Azure Active Directory에서 users.ownedDevices 속성을 읽습니다. |
| microsoft.directory/users/ownedObjects/read | Azure Active Directory에서 users.ownedObjects 속성을 읽습니다. |
| microsoft.directory/users/registeredDevices/read | Azure Active Directory에서 users.registeredDevices 속성을 읽습니다. |

### <a name="helpdesk-administrator-permissions"></a>기술 지원팀 관리자 권한

관리자가 아닌 사용자 및 기술 지원팀 관리자의 암호를 재설정할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="hybrid-identity-administrator-permissions"></a>하이브리드 ID 관리자 권한

클라우드 프로비저닝 및 인증 서비스를 사용, 배포, 구성, 관리, 모니터링하고 문제를 해결합니다. 

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/applications/audience/update  | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다.  |
| microsoft.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.directory/applications/create | Azure Active Directory에서 애플리케이션을 만듭니다. |
| microsoft.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션을 인스턴스화합니다. |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Azure AD 클라우드 프로비저닝 서비스의 모든 속성을 읽고 구성합니다. |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | Azure AD에서 ADFS(Active Directory Federated Services) 또는 타사 페더레이션 공급자의 모든 것을 관리합니다. |
| microsoft.directory/organization/dirSync/update | Azure Active Directory에서 organization.dirSync 속성을 업데이트합니다. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Azure AD에서 PHS(암호 해시 동기화)의 모든 것을 관리합니다. |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | Azure AD에서 PTA(통과 인증)의 모든 것을 관리합니다. |
| microsoft.directory/seamlessSSO/allProperties/allTasks | Azure AD에서 Seamless SSO(Single Sign-On)의 모든 것을 관리합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/create | Azure Active Directory에서 servicePrincipals를 만듭니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Azure AD에서 동기화 작업의 모든 것을 관리합니다. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Azure AD에서 동기화 스키마의 모든 것을 관리합니다. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Azure AD에서 동기화 자격 증명의 모든 것을 관리합니다. |
| microsoft.directory/servicePrincipals/tag/update | Azure Active Directory에서 servicePrincipals.tag 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="insights-administrator-permissions"></a>Insights 관리자 권한

M365 Insights 앱에서 sdministrative 액세스를 가집니다. 

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft insights/allEntities/Allentities | 통찰력의 모든 측면을 관리 합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="insights-business-leader-permissions"></a>Insights 비즈니스 리더 권한

M365 Insights 앱을 통해 대시보드와 정보를 보고 공유할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft의 통찰력/보고서/읽기 | Insights 앱에서 보고서 및 대시보드 보기 |
| microsoft insights/프로그램/업데이트 | Insights 앱에서 프로그램을 배포 하 고 관리 합니다. |

### <a name="intune-service-administrator-permissions"></a>Intune 서비스 관리자 권한

Intune 제품의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.directory/devices/basic/update | Azure Active Directory에서 디바이스의 기본 속성을 업데이트합니다. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/devices/create | Azure Active Directory에서 디바이스를 만듭니다. |
| microsoft.directory/devices/delete | Azure Active Directory에서 디바이스를 삭제합니다. |
| microsoft.directory/devices/registeredOwners/update | Azure Active Directory에서 devices.registeredOwners 속성을 업데이트합니다. |
| microsoft.directory/devices/registeredUsers/update | Azure Active Directory에서 devices.registeredUsers 속성을 업데이트합니다. |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.intune/allEntities/allTasks | Intune의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="kaizala-administrator-permissions"></a>Kaizala 관리자 권한

Microsoft Kaizala의 설정을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | Office 365 관리 센터를 읽습니다. |

### <a name="license-administrator-permissions"></a>라이선스 관리자 권한

사용자 및 그룹의 제품 라이선스를 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.directory/users/usageLocation/update | Azure Active Directory에서 users.usageLocation 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="lync-service-administrator-permissions"></a>Lync 서비스 관리자 권한

비즈니스용 Skype 제품의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read    | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |


### <a name="message-center-privacy-reader-permissions"></a>메시지 센터 개인 정보 읽기 권한자 권한

메시지 센터 게시물, 데이터 개인 정보 메시지, 그룹, 도메인 및 구독을 읽을 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.messageCenter/securityMessages/read | microsoft.office365.messageCenter에서 securityMessages를 읽습니다. |

### <a name="message-center-reader-permissions"></a>메시지 센터 읽기 권한자 권한
Office 365 메시지 센터에서만 조직의 메시지 및 업데이트를 읽을 수 있습니다. 

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |

### <a name="modern-commerce-user-permissions"></a>최신 상거래 사용자 권한
회사, 부서 또는 팀의 상업적 구매를 관리할 수 있습니다. 

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.commerce.billing/partners/read | O365 청구의 파트너 속성을 읽습니다. |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | 볼륨 라이선스 서비스 센터의 모든 것을 관리합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 확인 합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |


### <a name="network-administrator-permissions"></a>네트워크 관리자 권한
네트워크 위치를 관리하고 Microsoft 365 Software as a Service 애플리케이션에 대한 엔터프라이즈 네트워크 디자인 인사이트를 검토할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | M365 관리 센터의 네트워크 성능 페이지를 읽습니다.  |
| microsoft.office365.network/locations/allProperties/allTasks | 각 위치의 네트워크 위치 속성을 읽고 구성합니다. |

### <a name="office-apps-administrator-permissions"></a>Office 앱 관리자 권한
정책 및 설정 관리를 포함하여 Office 앱의 클라우드 서비스를 관리할 수 있고, 최종 사용자의 디바이스에서 "새로운 기능" 기능 콘텐츠를 선택, 선택 취소 및 게시하는 기능을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.userCommunication/allEntities/allTasks | 새로운 기능 메시지를 읽고 표시 여부를 업데이트합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="partner-tier1-support-permissions"></a>파트너 계층1 지원 권한

사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="partner-tier2-support-permissions"></a>파트너 계층2 지원 권한

사용하지 마세요. 일반적인 용도로는 적합하지 않습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.directory/domains/allTasks | Azure Active Directory에서 도메인을 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/organization/basic/update | Azure Active Directory에서 조직의 기본 속성을 업데이트합니다. |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="password-administrator-permissions"></a>암호 관리자 권한

비관리자 및 암호 관리자의 암호를 다시 설정할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="power-bi-service-administrator-permissions"></a>Power BI 서비스 관리자 권한

Power BI 제품의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>
| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면을 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |


### <a name="power-platform-administrator-permissions"></a>Power Platform 관리자 권한

Microsoft Dynamics 365, PowerApps 및 Microsoft Flow의 모든 것을 만들고 관리할 수 있습니다. 

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>
| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면을 관리합니다. |
| microsoft.flow/allEntities/allTasks | Microsoft Flow의 모든 것을 관리합니다. |
| microsoft.powerApps/allEntities/allTasks | PowerApps의 모든 것을 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="printer-administrator-permissions"></a>프린터 관리자 권한

프린터 및 프린터 커넥터의 모든 것을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>
| **actions** | **설명** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | 프린터 및 커넥터를 만들고 삭제하고, Microsoft 인쇄의 모든 속성을 읽고 업데이트합니다. |

### <a name="printer-technician-permissions"></a>프린터 기술자 권한

프린터를 등록/등록 취소하고 프린터 상태를 업데이트할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>
| **actions** | **설명** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Microsoft 인쇄에서 모든 커넥터 속성을 읽습니다. |
| microsoft.azure.print/printers/allProperties/read | Microsoft 인쇄에서 프린터의 모든 속성을 읽습니다. |
| microsoft.azure.print/printers/basic/update | Microsoft 인쇄에서 프린터의 기본 속성을 업데이트합니다. |
| microsoft.azure.print/printers/register | Microsoft 인쇄에서 프린터를 등록합니다. |
| microsoft.azure.print/printers/unregister | Microsoft 인쇄에서 프린터 등록을 취소합니다. |

### <a name="privileged-authentication-administrator-permissions"></a>권한 있는 인증 관리자 권한

사용자(관리자 또는 비관리자)의 인증 메서드 정보를 보고, 설정하고, 다시 설정할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/strongAuthentication/update | MFA 자격 증명 정보와 같은 강력한 인증 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/users/password/update | Office 365 조직에 포함된 모든 사용자의 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |

### <a name="privileged-role-administrator-permissions"></a>권한 있는 역할 관리자 권한

Azure AD의 역할 할당 및 Privileged Identity Management의 모든 것을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft. directory/groupsAssignableToRoles/allProperties/update | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정한 그룹을 업데이트 합니다. |
| microsoft. directory/groupsAssignableToRoles/create | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정 하 여 그룹을 만듭니다. |
| microsoft. directory/groupsAssignableToRoles/delete | Azure Active Directory에서 isAssignableToRole 속성을 true로 설정한 그룹을 삭제 합니다. |
| microsoft. directory/privilegedIdentityManagement/allEntities/Allentities | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 읽고 구성합니다. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Azure Active Directory에서 servicePrincipals.oAuth2PermissionGrants 속성을 읽고 구성합니다. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | 관리 단위(구성원 포함)를 만들고 관리합니다. |
| microsoft.directory/roleAssignments/allProperties/allTasks | 역할 할당을 만들고 관리합니다. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | 역할 정의를 만들고 관리합니다. |

### <a name="reports-reader-permissions"></a>보고서 읽기 권한자 권한

로그인 및 감사 보고서를 읽을 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |

### <a name="search-administrator-permissions"></a>Search 관리자 부여

Microsoft Search 설정의 모든 것을 만들고 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.search/allEntities/allProperties/allTasks | microsoft.office365.search에서 모든 리소스를 만들거나 삭제하고, 모든 속성을 읽고 업데이트합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="search-editor-permissions"></a>Search 편집자 권한

책갈피, 질문과 대답, 위치, 평면도와 같은 편집 콘텐츠를 만들고 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | microsoft.office365.messageCenter에서 메시지를 읽습니다. |
| microsoft.office365.search/content/allProperties/allTasks | microsoft.office365.search에서 콘텐츠를 만들거나 삭제하고, 모든 속성을 읽고 업데이트합니다. |

### <a name="security-administrator-permissions"></a>보안 관리자 권한

Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽고 구성을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft. directory/identityProtection/allProperties/read | microsoft.aad.identityProtection에서 모든 리소스를 읽습니다. |
| microsoft. directory/identityProtection/allProperties/update | microsoft.aad.identityProtection에서 모든 리소스를 업데이트합니다. |
| microsoft.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.directory/policies/create | Azure Active Directory에서 정책을 만듭니다. |
| microsoft.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.directory/policies/tenantDefault/update | Azure Active Directory에서 policies.tenantDefault 속성을 업데이트합니다. |
| microsoft. directory/privilegedIdentityManagement/allProperties/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/update | microsoft.office365.protectionCenter에서 모든 리소스를 업데이트합니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="security-operator-permissions"></a>보안 운영자 권한

보안 이벤트를 만들고 관리합니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.advancedThreatProtection/allEntities/read | Azure AD Advanced Threat Protection을 읽고 구성합니다. |
| microsoft. directory/cloudAppSecurity/allProperties/Allproperties | Microsoft Cloud App Security를 읽고 구성합니다. |
| microsoft. directory/identityProtection/allProperties/read | microsoft.aad.identityProtection에서 모든 리소스를 읽습니다. |
| microsoft. directory/privilegedIdentityManagement/allProperties/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.intune/allEntities/allTasks | Intune의 모든 측면을 관리합니다. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 보안 및 준수 센터를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Windows Defender Advanced Threat Protection을 읽고 구성합니다. |

### <a name="security-reader-permissions"></a>보안 읽기 권한자 권한

Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽을 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/policies/conditionalAccess/basic/read | Azure Active Directory에서 policies.conditionalAccess 속성을 읽습니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection에서 모든 리소스를 읽습니다. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement에서 모든 리소스를 읽습니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 보호 센터의 모든 측면을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="service-support-administrator-permissions"></a>서비스 지원 관리자 권한

서비스 상태 정보를 읽고, 지원 티켓을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

### <a name="sharepoint-service-administrator-permissions"></a>SharePoint 서비스 관리자 권한

SharePoint 서비스의 모든 측면을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 그룹의 기본 속성을 업데이트 합니다. |
| microsoft.directory/groups/unified/create | Microsoft 365 그룹을 만듭니다. |
| microsoft.directory/groups/unified/delete | Microsoft 365 그룹을 삭제 합니다. |
| microsoft.directory/groups/unified/members/update | Microsoft 365 그룹의 멤버 자격을 업데이트 합니다. |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 그룹의 소유권을 업데이트 합니다. |
| microsoft.office365.network/performance/allProperties/read | M365 관리 센터의 네트워크 성능 페이지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint에서 모든 리소스를 만들고 삭제하고, 표준 속성을 읽고 업데이트합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read    | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="teams-communications-administrator-permissions"></a>Teams 통신 관리자 권한

Microsoft Teams 서비스 내에서 호출 및 회의 기능을 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |

### <a name="teams-communications-support-engineer-permissions"></a>Teams 통신 지원 엔지니어 권한

고급 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="teams-communications-support-specialist-permissions"></a>Teams 통신 지원 전문가 권한

기본 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |

### <a name="teams-service-administrator-permissions"></a>Teams 서비스 관리자 권한

Microsoft Teams 서비스를 관리할 수 있습니다.

> [!NOTE]
> 이 역할에는 Azure Active Directory 외부의 추가 권한이 있습니다. 자세한 내용은 위에 나온 역할 설명을 참조하세요.
>
>

| **actions** | **설명** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Azure Active Directory에서 groups.unified 속성을 업데이트합니다. |
| microsoft.directory/groups/unified/basic/update | Microsoft 365 그룹의 기본 속성을 업데이트 합니다. |
| microsoft.directory/groups/unified/create | Microsoft 365 그룹을 만듭니다. |
| microsoft.directory/groups/unified/delete | Microsoft 365 그룹을 삭제 합니다. |
| microsoft.directory/groups/unified/members/update | Microsoft 365 그룹의 멤버 자격을 업데이트 합니다. |
| microsoft.directory/groups/unified/owners/update | Microsoft 365 그룹의 소유권을 업데이트 합니다. |
| microsoft.office365.network/performance/allProperties/read | M365 관리 센터의 네트워크 성능 페이지를 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.usageReports/allEntities/read | Office 365 사용 보고서를 읽습니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |

### <a name="user-administrator-permissions"></a>사용자 관리자 권한
제한된 관리자의 암호 재설정을 비롯하여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Azure Active Directory에서 appRoleAssignments를 만듭니다. |
| microsoft.directory/appRoleAssignments/delete | Azure Active Directory에서 appRoleAssignments를 삭제합니다. |
| microsoft.directory/appRoleAssignments/update | Azure Active Directory에서 appRoleAssignments를 업데이트합니다. |
| microsoft.directory/contacts/basic/update | Azure Active Directory에서 연락처의 기본 속성을 읽습니다. |
| microsoft.directory/contacts/create | Azure Active Directory에서 연락처를 만듭니다. |
| microsoft.directory/contacts/delete | Azure Active Directory에서 연락처를 삭제합니다. |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.directory/groups/create | Azure Active Directory에서 그룹을 만듭니다. |
| microsoft.directory/groups/createAsOwner | Azure Active Directory에서 그룹을 만듭니다. 작성자는 첫 번째 소유자로 추가되고, 만들어진 개체는 작성자의 250개 개체 만들기 할당량과 대조하여 계산됩니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/hiddenMembers/read | Azure Active Directory에서 groups.hiddenMembers 속성을 읽습니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |
| microsoft.directory/users/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/users/assignLicense | Azure Active Directory에서 사용자의 라이선스를 관리합니다. |
| microsoft.directory/users/basic/update | Azure Active Directory에서 사용자의 기본 속성을 업데이트합니다. |
| microsoft.directory/users/create | Azure Active Directory에서 사용자를 만듭니다. |
| microsoft.directory/users/delete | Azure Active Directory에서 사용자를 삭제합니다. |
| microsoft.directory/users/invalidateAllRefreshTokens | Azure Active Directory에서 모든 사용자 새로 고침 토큰을 무효화합니다. |
| microsoft.directory/users/manager/update | Azure Active Directory에서 users.manager 속성을 업데이트합니다. |
| microsoft.directory/users/password/update | Azure Active Directory의 모든 사용자에 대한 암호를 업데이트합니다. 자세한 내용은 온라인 설명서를 참조하세요. |
| microsoft.directory/users/restore | Azure Active Directory에서 삭제된 사용자를 복원합니다. |
| microsoft.directory/users/userPrincipalName/update | Azure Active Directory에서 users.userPrincipalName 속성을 업데이트합니다. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health를 읽고 구성합니다. |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓을 만들고 관리합니다. |
| microsoft.office365.webPortal/allEntities/basic/read | microsoft.office365.webPortal에서 모든 리소스에 대한 기본 속성을 읽습니다. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health를 읽고 구성합니다. |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 지원 티켓을 만들고 관리합니다. |

## <a name="role-template-ids"></a>역할 템플릿 ID

역할 템플릿 ID는 Microsoft Graph API 또는 PowerShell 사용자가 주로 사용합니다.

Graph displayName | Azure Portal 표시 이름 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
애플리케이션 관리자 | 애플리케이션 관리자 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
애플리케이션 개발자 | 애플리케이션 개발자 | CF1C38E5-3621-4004-A7CB-879624DCED7C
인증 관리자 | 인증 관리자 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps 관리자 | Azure DevOps 관리자 | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection 관리자 | Azure Information Protection 관리자 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C IEF 키 세트 관리자 | B2C IEF 키 세트 관리자 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 정책 관리자 | B2C IEF 정책 관리자 | 3edaf663-341e-4475-9f94-5c398ef6c070
대금 청구 관리자 | 대금 청구 관리자 | b0f54661-2d74-4c50-afa3-1ec803f12efe
클라우드 애플리케이션 관리자 | 클라우드 애플리케이션 관리자 | 158c047a-c907-4556-b7ef-446551a6b5f7
클라우드 디바이스 관리자 | 클라우드 디바이스 관리자 | 7698a772-787b-4ac8-901f-60d6b08affd2
회사 관리자 | 전역 관리자 | 62e90394-69f5-4237-9190-012177145e10
규정 준수 관리자 | 규정 준수 관리자 | 17315797-102d-40b4-93e0-432062caca18
규정 준수 데이터 관리자 | 규정 준수 데이터 관리자 | e6d1a23a-da11-4be4-9570-befc86d067a7
조건부 액세스 관리자 | 조건부 액세스 관리자 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 서비스 관리자 | Dynamics 365 관리자 | 44367163-eba1-44c3-98af-f5787879f96a
고객 LockBox 액세스 승인자 | 고객 Lockbox 액세스 승인자 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
데스크톱 분석 관리자 | 데스크톱 분석 관리자 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
디바이스 관리자 | 디바이스 관리자 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
디바이스 연결 | 사용되지 않음 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
디바이스 관리 | 사용되지 않음 | 2b499bcd-da44-4968-8aec-78e1674fa64d
디바이스 사용자 | 사용되지 않음 | d405c6df-0af8-4e3b-95e4-4d06e542189e
디렉터리 읽기 권한자 | 디렉터리 읽기 권한자 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
디렉터리 동기화 계정 | 사용하면 안되므로 표시되지 않습니다. | d29b2b05-8046-44ba-8758-1e26182fcf32
디렉터리 작성자 | 디렉터리 작성자 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 서비스 관리자 | Exchange 관리자 | 29232cdf-9323-42fd-ade2-1d097af3e4de
외부 ID 사용자 흐름 관리자 | 외부 ID 사용자 흐름 관리자 | 6e591065-9bad-43ed-90f3-e9424366d2f0
외부 ID 사용자 흐름 특성 관리자 | 외부 ID 사용자 흐름 특성 관리자 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
외부 ID 공급자 관리자 | 외부 ID 공급자 관리자 | be2f45a1-457d-42af-a067-6ec1fa63bc45
글로벌 읽기 권한자 | 글로벌 읽기 권한자 | f2ef992c-3afb-46b9-b7cf-a126ee74c451
그룹 관리자 | 그룹 관리자 | fdd7a751-b60b-444a-984c-02652fe8fa1c 
게스트 초대자 | 게스트 초대자 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
기술 지원팀 관리자 | 기술 지원팀 관리자 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
하이브리드 ID 관리자 | 하이브리드 ID 관리자 | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Insights 관리자 | Insights 관리자 | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Insights 비즈니스 리더 | Insights 비즈니스 리더 | 31e939ad-9672-4796-9c2e-873181342d2d
Intune 서비스 관리자 | Intune 관리자 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 관리자 | Kaizala 관리자 | 74ef975b-6605-40af-a5d2-b9539d836353
라이선스 관리자 | 라이선스 관리자 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 서비스 관리자 | 비즈니스용 Skype 관리자 | 75941009-915a-4869-abe7-691bff18279e
메시지 센터 개인 정보 읽기 권한자 | 메시지 센터 개인 정보 읽기 권한자 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
메시지 센터 읽기 권한자 | 메시지 센터 읽기 권한자 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
최신 상거래 사용자 | 최신 상거래 사용자 | d24aef57-1500-4070-84db-2666f29cf966
네트워크 관리자 | 네트워크 관리자 | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Office 앱 관리자 | Office 앱 관리자 | 2b745bdf-0803-4d80-aa65-822c4493daac
파트너 계층1 지원 | 사용하면 안되므로 표시되지 않습니다. | 4ba39ca4-527c-499a-b93d-d9b492c50246
파트너 계층2 지원 | 사용하면 안되므로 표시되지 않습니다. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
암호 관리자 | 암호 관리자 | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI 서비스 관리자 | Power BI 관리자 | a9ea8996-122f-4c74-9520-8edcd192826c
Power Platform 관리자 | Power Platform 관리자 | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
프린터 관리자 | 프린터 관리자 | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
프린터 기술자 | 프린터 기술자 | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
권한 있는 인증 관리자 | 권한 있는 인증 관리자 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
권한 있는 역할 관리자 | 권한 있는 역할 관리자 | e8611ab8-c189-46e8-94e1-60213ab1f814
보고서 구독자 | 보고서 읽기 권한자 | 4a5d8f65-41da-4de4-8968-e035b65339cf
Search 관리자 | Search 관리자 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Search 편집자 | Search 편집자 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
보안 관리자 | 보안 관리자 | 194ae4cb-b126-40b2-bd5b-6091b380977d
보안 운영자 | 보안 운영자 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
보안 판독기 | 보안 판독기 | 5d6b6bb7-de71-4623-b4af-96380a352509
서비스 지원 관리자 | 서비스 지원 관리자 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 서비스 관리자 | SharePoint 관리자 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams 통신 관리자 | Teams 통신 관리자 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams 통신 지원 엔지니어 | Teams 통신 지원 엔지니어 | f70938a0-fc10-4177-9e90-2178f8765737
Teams 통신 지원 전문가 | Teams 통신 지원 전문가 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams 서비스 관리자 | Teams 서비스 관리자 | 69091246-20e8-4a56-aa4d-066075b2a7a8
사용자 | 사용하면 안되므로 표시되지 않습니다. | a0b1b346-4d3e-4e8b-98f8-753987be4970
사용자 계정 관리자 | 사용자 관리자 | fe930be7-5e62-47db-91af-98c3a49a38b1
작업 공간 디바이스 연결 | 사용되지 않음 | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 해당 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 디바이스 연결
* 디바이스 관리
* 디바이스 사용자
* 전자 메일 확인 사용자 생성자
* 사서함 관리자
* 작업 공간 디바이스 연결

## <a name="roles-not-shown-in-the-portal"></a>포털에 표시되지 않는 역할

PowerShell 또는 MS Graph API에서 반환된 모든 역할이 Azure Portal에 표시되는 것은 아닙니다. 다음 표에는 이러한 차이점이 정리되어 있습니다.

API 이름 | Azure Portal 이름 | 메모
-------- | ------------------- | -------------
회사 관리자 | 전역 관리자 | [명확성을 높이기 위해 이름이 변경됨](directory-assign-admin-roles.md#role-template-ids)
CRM 서비스 관리자 | Dynamics 365 관리자 | [현재 제품 브랜드를 반영](directory-assign-admin-roles.md#role-template-ids)
디바이스 연결 | 사용되지 않음 | [사용되지 않는 역할 설명서](directory-assign-admin-roles.md#deprecated-roles)
디바이스 관리 | 사용되지 않음 | [사용되지 않는 역할 설명서](directory-assign-admin-roles.md#deprecated-roles)
디바이스 사용자 | 사용되지 않음 | [사용되지 않는 역할 설명서](directory-assign-admin-roles.md#deprecated-roles)
디렉터리 동기화 계정 | 사용하면 안되므로 표시되지 않습니다. | [디렉터리 동기화 계정 설명서](directory-assign-admin-roles.md#directory-synchronization-accounts)
디렉터리 작성자 | 사용하면 안되므로 표시되지 않습니다. | [디렉터리 쓰기 권한자 설명서](directory-assign-admin-roles.md#directory-writers)
게스트 사용자 | 사용하면 안되므로 표시되지 않습니다.  | 해당 없음
Lync 서비스 관리자 | 비즈니스용 Skype 관리자 | [현재 제품 브랜드를 반영](directory-assign-admin-roles.md#role-template-ids)
파트너 계층 1 지원 | 사용하면 안되므로 표시되지 않습니다. | [파트너 계층1 지원 설명서](directory-assign-admin-roles.md#partner-tier1-support)
파트너 계층 2 지원 | 사용하면 안되므로 표시되지 않습니다. | [파트너 계층2 지원 설명서](directory-assign-admin-roles.md#partner-tier2-support)
제한된 게스트 사용자 | 사용하면 안되므로 표시되지 않습니다. | 해당 없음
사용자 | 사용하면 안되므로 표시되지 않습니다. | 해당 없음
작업 공간 디바이스 연결 | 사용되지 않음 | [사용되지 않는 역할 설명서](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>다음 단계

* Azure 구독의 관리자로 사용자를 할당 하는 방법에 대해 자세히 알아보려면 [azure 역할 할당 추가 또는 제거 (AZURE RBAC)](../../role-based-access-control/role-assignments-portal.md) 를 참조 하세요.
* Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대한 자세한 내용은 [여러 역할의 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)를 참조하세요.
* 구독과 Azure AD 테 넌 트 간의 관계 또는 구독을 연결 하거나 추가 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 테 넌 트에 Azure 구독 연결 또는 추가](../fundamentals/active-directory-how-subscriptions-associated-directory.md) 를 참조 하세요.
