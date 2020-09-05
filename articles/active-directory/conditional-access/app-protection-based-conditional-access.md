---
title: 조건부 액세스를 사용하는 앱 보호 정책 - Azure Active Directory
description: Azure Active Directory에서 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 앱 보호 정책을 요청하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3626a29bb828c9fbc353b11858d42fecd4bb129d
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641027"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>방법: 조건부 액세스를 사용하는 클라우드 앱 액세스에 대한 앱 보호 정책 및 승인된 클라이언트 앱 필요

사용자는 정기적으로 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원들이 생산성을 높일 수 있도록 하는 동시에 잠재적으로 안전하지 않은 애플리케이션의 데이터 손실을 방지하려고 합니다. 조건부 액세스를 통해 조직에서는 승인된(최신 인증 지원) 클라이언트 앱에 대한 액세스를 적용된 Intune 앱 보호 정책으로 제한할 수 있습니다.

이 문서에서는 Office 365, Exchange Online 및 SharePoint Online과 같은 리소스에 대한 조건부 액세스 정책을 구성하는 세 가지 시나리오를 제공합니다.

- [시나리오 1: Office 365 앱에 앱 보호 정책을 사용하는 승인된 앱 필요](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [시나리오 2: 브라우저 앱에 앱 보호 정책을 사용하는 승인된 앱 필요](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [시나리오 3: Exchange Online 및 SharePoint Online에 승인된 클라이언트 앱 및 앱 보호 정책 필요](#scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

조건부 액세스에서 이러한 클라이언트 앱은 앱 보호 정책을 사용하여 보호되는 것으로 알려져 있습니다. 앱 보호 정책에 대한 자세한 내용은 [앱 보호 정책 개요](/intune/apps/app-protection-policy) 문서에서 찾을 수 있습니다.

> [!WARNING]
> 모든 응용 프로그램이 승인 된 응용 프로그램으로 지원 되거나 응용 프로그램 보호 정책을 지원 하지는 않습니다. 적격 클라이언트 앱 목록은 [앱 보호 정책 요구 사항](concept-conditional-access-grant.md#require-app-protection-policy)을 참조하세요.

> [!NOTE]
> "선택 된 컨트롤 중 하나 필요" 권한 부여 컨트롤은 또는 절과 유사 합니다. 이는 정책 내에서 사용 되어 사용자가 **앱 보호 정책 필요** 또는 **승인 된 클라이언트 앱** 부여 컨트롤 필요를 지 원하는 앱을 활용할 수 있도록 합니다. 앱이 두 정책 모두에서 지원 되는 경우 **앱 보호 정책이 적용 되어야** 합니다. **앱 보호 정책 필요** 권한 부여 컨트롤을 지원하는 앱에 대한 자세한 내용은 [앱 보호 정책 요구 사항](concept-conditional-access-grant.md#require-app-protection-policy)을 참조하세요.

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>시나리오 1: Office 365 앱에 앱 보호 정책을 사용하는 승인된 앱 필요

이 시나리오에서 Contoso는 Office 365 리소스에 대 한 모든 모바일 액세스에서 액세스를 받기 전에 앱 보호 정책에 의해 보호 되는 Outlook mobile 및 OneDrive와 같은 승인 된 클라이언트 앱을 사용 하도록 결정 했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직에서는 모바일 디바이스에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 단계를 완료해야 합니다.

**1단계: Office 365에 대한 Azure AD 조건부 액세스 정책 구성**

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹**을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365(미리 보기)** 를 선택합니다.
1. **조건**에서 **디바이스 플랫폼**을 선택합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **Android** 및 **iOS**를 포함합니다.
1. **조건**아래에서 **클라이언트 앱**을 선택 합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **모바일 앱 및 데스크톱 클라이언트** 를 선택 하 고 다른 모든 항목을 선택 취소 합니다.
1. **액세스 제어** > **권한 부여**에서 다음 옵션을 선택합니다.
   - **승인된 클라이언트 앱 필요**
   - **앱 보호 정책 필요(미리 보기)**
   - **선택된 컨트롤이 모두 필요**
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들고 사용하도록 설정합니다.

**2단계: EAS(Exchange Online with Active Sync)에 대한 Azure AD 조건부 액세스 정책 구성**

이 단계의 조건부 액세스 정책의 경우 다음 구성 요소를 구성합니다.

1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹**을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365 Exchange Online**을 선택합니다.
1. **조건**아래에서 **클라이언트 앱**을 선택 합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **Exchange ActiveSync 클라이언트** 를 선택 하 고 다른 모든 항목을 선택 취소 합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 권한 부여**, **앱 보호 정책 필요**를 선택하고 **선택**을 선택합니다.
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들고 사용하도록 설정합니다.

**3단계: iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies) 문서를 검토하세요. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>시나리오 2: 브라우저 앱에 앱 보호 정책을 사용하는 승인된 앱 필요

이 시나리오에서 Contoso는 Office 365 리소스에 대한 모든 모바일 웹 검색 액세스가 액세스를 받기 전에 앱 보호 정책을 통해 보호되는 iOS 및 Android용 Edge와 같은 승인된 클라이언트 앱을 사용하도록 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직에서는 모바일 디바이스에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 단계를 완료해야 합니다.

**1단계: Office 365에 대한 Azure AD 조건부 액세스 정책 구성**

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹**을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365(미리 보기)** 를 선택합니다.
1. **조건**에서 **디바이스 플랫폼**을 선택합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **Android** 및 **iOS**를 포함합니다.
1. **조건**아래에서 **클라이언트 앱**을 선택 합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **브라우저** 를 선택 하 고 다른 모든 항목을 선택 취소 합니다.
1. **액세스 제어** > **권한 부여**에서 다음 옵션을 선택합니다.
   - **승인된 클라이언트 앱 필요**
   - **앱 보호 정책 필요(미리 보기)**
   - **선택된 컨트롤이 모두 필요**
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들고 사용하도록 설정합니다.

**2단계: iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies) 문서를 검토하세요. 

## <a name="scenario-3-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>시나리오 3: Exchange Online 및 SharePoint Online에 승인된 클라이언트 앱 및 앱 보호 정책 필요

이 시나리오에서 Contoso는 액세스를 받기 전에 앱 보호 정책에 의해 보호되는 Outlook 모바일과 같은 승인된 클라이언트 앱을 사용하는 한, 사용자가 모바일 디바이스에서 이메일 및 SharePoint 데이터에만 액세스할 수 있도록 결정했습니다. 모든 사용자는 이미 Azure AD 자격 증명을 사용하여 로그인하고 Azure AD Premium P1 또는 P2 및 Microsoft Intune을 포함하는 라이선스가 할당되어 있습니다.

조직에서는 모바일 디바이스 및 Exchange ActiveSync 클라이언트에서 승인된 클라이언트 앱을 사용하도록 요구하기 위해 다음 세 단계를 완료해야 합니다.

**1단계: Exchange Online 및 SharePoint Online에 액세스할 때 승인된 클라이언트 앱 및 앱 보호 정책을 사용해야 하는 Android 및 iOS 기반 최신 인증 클라이언트에 대한 정책입니다.**

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹**을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365 Exchange Online** 및 **Office 365 SharePoint Online**을 선택합니다.
1. **조건**에서 **디바이스 플랫폼**을 선택합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **Android** 및 **iOS**를 포함합니다.
1. **조건**아래에서 **클라이언트 앱**을 선택 합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **모바일 앱 및 데스크톱 클라이언트** 를 선택 하 고 다른 모든 항목을 선택 취소 합니다.
1. **액세스 제어** > **권한 부여**에서 다음 옵션을 선택합니다.
   - **승인된 클라이언트 앱 필요**
   - **앱 보호 정책 필요(미리 보기)**
   - **선택된 컨트롤 중 하나가 필요**
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들고 사용하도록 설정합니다.

**2단계: 승인된 클라이언트 앱을 사용해야 하는 Exchange ActiveSync 클라이언트에 대한 정책입니다.**

1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자** 또는 이 정책을 적용할 특정 **사용자 및 그룹**을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함**에서 **Office 365 Exchange Online**을 선택합니다.
1. **조건**아래에서 **클라이언트 앱**을 선택 합니다.
   1. **구성**을 **예**로 설정합니다.
   1. **Exchange ActiveSync 클라이언트** 를 선택 하 고 다른 모든 항목을 선택 취소 합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 권한 부여**, **앱 보호 정책 필요**를 선택하고 **선택**을 선택합니다.
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들고 사용하도록 설정합니다.

**3단계: iOS 및 Android 클라이언트 애플리케이션에 대한 Intune 앱 보호 정책 구성**

Android 및 iOS용 앱 보호 정책을 만드는 단계는 [앱 보호 정책을 만들고 할당하는 방법](/intune/apps/app-protection-policies) 문서를 검토하세요. 

## <a name="next-steps"></a>다음 단계

[조건부 액세스란?](overview.md)

[조건부 액세스 구성 요소](concept-conditional-access-policies.md)

[일반 조건부 액세스 정책](concept-conditional-access-policy-common.md)

