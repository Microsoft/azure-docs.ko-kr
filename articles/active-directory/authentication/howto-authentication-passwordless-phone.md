---
title: Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인-Azure Active Directory
description: Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 Azure AD에 대해 암호 없는 로그인을 사용 하도록 설정
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ed1c3628b33d3ed29c3af3b773f2b635e684a67
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717050"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 암호 없는 로그인 사용

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 기술과 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용하여 디바이스에 연결되고 생체 인식 또는 PIN을 사용하는 사용자 자격 증명을 사용하도록 설정합니다. 이 인증 방법은 모바일을 비롯 한 모든 장치 플랫폼과 Microsoft 인증 라이브러리와 통합 되는 모든 앱 또는 웹 사이트에서 사용할 수 있습니다. 

![사용자가 로그인을 승인 하도록 요청 하는 브라우저 로그인의 예](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

사용자 이름을 입력 한 후 암호를 입력 하 라는 메시지가 표시 되는 대신 Microsoft Authenticator 앱에서 휴대폰 로그인을 사용 하도록 설정한 사용자는 앱의 번호를 탭 하 라는 메시지를 표시 합니다. 앱에서 사용자는 번호가 일치해야 하고 [승인]을 선택한 다음, PIN 또는 생체 인식을 제공해야 인증이 완료됩니다.

> [!NOTE]
> 이 기능은 2017 년 3 월부터 Microsoft Authenticator 앱에 포함 되었으므로 디렉터리에 대해 정책을 사용 하도록 설정 하면 사용자가이 흐름을 즉시 발견할 수 있으며 정책에 의해 사용 하도록 설정 되지 않은 경우 오류 메시지가 표시 될 수 있습니다. 이러한 변화를 인지하고 사용자가 대비하도록 하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Multi-Factor Authentication, 푸시 알림이 확인 방법으로 허용 됨 
- iOS 8.0 이상 또는 Android 6.0 이상을 실행하는 디바이스에 설치된 최신 버전의 Microsoft Authenticator

> [!NOTE]
> Azure AD PowerShell을 사용 하 여 이전 Microsoft Authenticator app 암호 없는 로그인 미리 보기를 사용 하도록 설정한 경우 전체 디렉터리에 대해 사용 하도록 설정 되었습니다. 이 새 메서드를 사용 하도록 설정 하면 PowerShell 정책이 대체 됩니다. 새 인증 방법을 통해 테 넌 트의 모든 사용자에 대해을 사용 하도록 설정 하는 것이 좋습니다. 그렇지 않으면 새 정책에 없는 사용자가 더 이상 passwordlessly에 로그인 할 수 없습니다. 

## <a name="enable-passwordless-authentication-methods"></a>암호 없는 인증 방법 사용

### <a name="enable-the-combined-registration-experience"></a>결합 된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 기능을 사용 합니다. 결합 된 [보안 정보 등록 사용](howto-registration-mfa-sspr-combined.md)문서의 단계를 수행 하 여 결합 된 등록을 사용 하도록 설정 합니다.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>암호 없는 휴대폰 로그인 인증 방법 사용

1. **전역 관리자** 계정을 사용 하 여 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. *Azure Active Directory*를 검색하고 선택합니다. **보안**  >  **인증 방법**  >  **인증 방법 정책 (미리 보기)을** 선택 합니다.
1. **Passwordless 휴대폰 로그인**에서 다음 옵션을 선택 합니다.
   1. **사용** -예 또는 아니요
   1. **대상** -모든 사용자 또는 사용자 선택
1. **저장** 새 정책 설정

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft Authenticator 앱의 사용자 등록 및 관리

1. [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)으로 이동합니다.
1. 아직 로그인 하지 않은 경우 로그인
1. **방법 추가**를 클릭 하 고 **authenticator 앱**을 선택한 다음 **추가** 를 클릭 하 여 인증자 앱을 추가 합니다.
1. 지침에 따라 장치에 Microsoft Authenticator 앱을 설치 하 고 구성 합니다.
1. **완료** 를 클릭 하 여 인증자 MFA 앱 설정 흐름을 완료 합니다. 
1. **Microsoft Authenticator**의 계정 드롭다운 메뉴에서 **휴대폰 로그인 사용** 을 선택 합니다.
1. 앱의 지침에 따라 암호 없는 휴대폰 로그인에 대 한 등록을 완료 합니다. 

조직에서는 사용자에 게 암호를 지정 [하지 않고 휴대폰으로 로그인을](../user-help/user-help-auth-app-sign-in.md) 지정 하 여 Microsoft Authenticator 앱에서 추가 지원을 설정 하 고 휴대폰 로그인을 사용 하도록 설정할 수 있습니다. 이러한 설정을 적용 하려면 로그 아웃 한 후 다시 테 넌 트에 다시 로그인 해야 할 수 있습니다. 

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명을 사용 하 여 로그인

공개 미리 보기의 경우 사용자가 이 새 자격 증명을 만들거나 사용할 수 있도록 하는 방법은 없습니다. 관리자가 테 넌 트를 사용 하도록 설정 하 **고** 사용자가 휴대폰 로그인을 사용 하도록 Microsoft Authenticator 앱을 업데이트 한 후에만 사용자에 게 암호 없는 로그인이 발생 합니다.

웹에서 사용자 이름을 입력 하 고 **다음**을 선택 하면 사용자에 게 숫자가 표시 되 고, 암호를 사용 하는 대신 인증에 적합 한 수를 선택 하도록 Microsoft Authenticator 앱에 메시지가 표시 됩니다. 

![Microsoft Authenticator 앱을 사용한 브라우저 로그인 예](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>알려진 문제

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>사용자가 정책으로 사용 하도록 설정 되어 있지 않지만에서 암호를 사용 하지 않는 휴대폰 로그인 방법을 Microsoft Authenticator

사용자가 현재 Microsoft Authenticator 앱 또는 이전 장치에서 암호 없는 휴대폰 로그인 자격 증명을 만든 시점이 있을 수 있습니다. 관리자가 암호 없는 휴대폰 로그인에 대 한 인증 방법 정책을 사용 하도록 설정 하면 자격 증명을 등록 한 모든 사용자가 정책을 사용 하도록 설정 되었는지 여부에 관계 없이 새 로그인 프롬프트가 표시 되기 시작 합니다. 사용자가 정책으로 자격 증명을 사용할 수 없는 경우 인증 흐름을 완료 한 후에 오류가 표시 됩니다. 

관리자는 사용자가 암호 없는 휴대폰 로그인을 사용 하도록 선택할 수 있습니다. 또는 사용자가 메서드를 제거 해야 합니다. 사용자에 게 더 이상 등록 된 장치가 없는 경우로 이동 하 여 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 제거할 수 있습니다. 여전히 MFA에 대 한 인증자를 사용 하는 경우 Microsoft Authenticator 내에서 **휴대폰 로그인 사용 안 함** 을 선택할 수 있습니다.  

### <a name="ad-fs-integration"></a>AD FS 통합

사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 항상 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 이 논리를 통해 하이브리드 테 넌 트의 사용자가 "대신 암호 사용"을 클릭 하는 추가 단계를 수행 하지 않고 로그인 확인을 위해 AD FS로 전달 되지 않습니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시합니다. 

사용자가 응답 하지 않는 암호를 사용 하 여 로그인 인증을 보류 중이 고 다시 로그인을 시도 하는 경우 사용자는 암호를 입력 하 AD FS 하는 것이 좋습니다.  

### <a name="azure-mfa-server"></a>Azure MFA 서버

조직의 온-프레미스 Azure mfa 서버를 통해 mfa를 사용 하도록 설정한 최종 사용자는 여전히 단일 암호 없는 전화 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.  

### <a name="device-registration"></a>디바이스 등록

이 새로운 강력한 자격 증명을 만들기 위한 필수 구성 요소 중 하나는 Microsoft Authenticator 앱이 설치 된 장치가 Azure AD 테 넌 트 내에 개별 사용자에 등록 되어야 한다는 것입니다. 현재 장치 등록 제한으로 인해 장치는 단일 테 넌 트에만 등록할 수 있습니다. 이 제한은 Microsoft Authenticator 앱에서 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 설정할 수 있음을 의미합니다.

### <a name="intune-mobile-application-management"></a>Intune 모바일 애플리케이션 관리 

MAM (모바일 응용 프로그램 관리)이 필요한 정책에 적용 되는 최종 사용자는 Microsoft Authenticator 앱에서 암호 없는 자격 증명을 등록할 수 없습니다. 

> [!NOTE]
> 장치 등록은 장치 관리 또는 "MDM"과 동일 하지 않습니다. Azure AD 디렉터리에서 장치 ID와 사용자 ID를 함께 연결 합니다.  

## <a name="next-steps"></a>다음 단계

[암호 없는의 작동 원리 알아보기](concept-authentication-passwordless.md)

[디바이스 등록에 대한 자세한 정보](../devices/overview.md#getting-devices-in-azure-ad)

[Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)