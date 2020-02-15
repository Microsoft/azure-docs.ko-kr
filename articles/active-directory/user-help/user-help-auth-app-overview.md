---
title: Microsoft Authenticator 앱이란? - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator 앱의 정의, 작동 원리 및 이 콘텐츠 섹션에 포함된 정보를 비롯하여 Microsoft Authenticator 앱에 대해 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: c95ae2e48a7d5a3b9e9e28b1d4e14b670205c043
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062288"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱이란?

2단계 인증을 사용하는 경우 Microsoft Authenticator 앱으로 계정에 쉽게 로그인할 수 있습니다. 2단계 인증을 사용하면 특히 중요한 정보를 볼 때 보다 안전하게 계정에 액세스할 수 있습니다. 암호를 잊어버리거나 암호가 노출될 수 있으므로 2단계 인증은 다른 사람이 침입하기 어렵게 만들어서 계정을 보호하는 추가 보안 단계입니다.

Microsoft Authenticator 앱은 다음을 비롯한 여러 가지 방법으로 사용할 수 있습니다.

- 사용자 이름 및 암호로 로그인한 후 인증에 대한 프롬프트에 응답합니다.

- 암호를 입력하거나 사용자 이름, 인증 앱을 사용하지 않고 모바일 디바이스에서 지문, 얼굴 또는 PIN을 사용하여 로그인합니다.

- 인증자 앱을 지원하는 다른 계정에 대한 코드 생성기로서,

> [!Important]
> Microsoft Authenticator 앱은 2단계 인증을 사용하는 모든 계정에 사용할 수 있으며 TOTP(시간 제약이 있는 일회성 암호) 표준을 지원합니다.
>
>이 문서는 Microsoft Authenticator 앱을 다운로드하여 보안 확인 방법으로 사용하려는 사용자를 위해 작성되었습니다. 직원 및 기타 용도로 Authenticator 앱을 사용하여 암호 없는 로그인을 설정하는 방법에 대한 정보를 찾는 관리자는 [Microsoft Authenticator 앱으로 암호 없는 로그인 사용(미리 보기)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-phone)을 참조하세요.

## <a name="terminology"></a>용어

| 용어|Description|
| ----|-----------|
| 2단계 인증 | 암호 및 PIN 같은 두 가지 인증 정보만 사용해야 하는 인증 프로세스입니다. Microsoft Authenticator 앱은 표준 2단계 인증 및 암호 없는 로그인을 지원합니다. |
| MFA(Multi-Factor authentication) | 모든 2단계 인증은 다단계 인증이며, 조직의 요구 사항에 따라 *최소한* 두 가지 인증 정보를 사용해야 합니다. |
| Microsoft 계정(MSA라고도 함) | 사용자가 고유한 개인 계정을 만들어서 Outlook, OneDrive, Xbox LIVE 또는 Office 365 같은 소비자 지향 Microsoft 제품 및 클라우드 서비스에 액세스합니다. Microsoft 계정은 Microsoft에서 실행하는 Microsoft 소비자 ID 계정 시스템에 생성되고 저장됩니다. |
| 회사 또는 학교 계정 | 사용자가 Microsoft Azure, Windows Intune 및 Office 365 같은 내부의 제한된 리소스에 액세스할 수 있도록 조직에서 회사 또는 학교 계정(예: alain@contoso.com)을 만듭니다. |
| 확인 코드 | 추가된 각 계정 아래의 인증 앱에 표시되는 6자리 코드입니다. 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다. 이것을 OTP(일회성 암호)라고도 합니다. |

## <a name="how-two-factor-verification-works-with-the-app"></a>2단계 인증이 앱에서 작동하는 원리

2단계 인증은 Microsoft Authenticator 앱에서 다음과 같은 방식으로 작동합니다.

- **알림.** 회사/학교 계정 또는 개인 Microsoft 계정에 로그인하는 데 사용하는 디바이스에 사용자 이름 및 암호를 입력하면 Microsoft Authenticator 앱에서 **로그인 승인**을 요청하는 알림을 보냅니다. 로그인 시도를 인식한 경우 **승인**을 선택합니다. 그렇지 않은 경우 **거부**를 선택합니다. **거부**를 선택하면 요청을 사기로 표시할 수 있습니다.

- **확인 코드.** 회사/학교 계정 또는 개인 Microsoft 계정에 로그인하는 데 사용하는 디바이스에 사용자 이름 및 암호를 입력한 다음, Microsoft Authenticator 앱의 **계정** 화면에서 연결된 확인 코드를 복사합니다. 확인 코드를 OTP(일회성 암호) 인증이라고도 합니다.

- **암호 없는 로그인.** 회사/학교 계정 또는 개인 Microsoft 계정에 로그인하는 데 사용하는 디바이스에 사용자 이름을 입력한 다음, 모바일 디바이스에서 지문, 얼굴 또는 PIN을 사용하여 본인이라는 것을 확인합니다. 이 방법은 암호를 입력할 필요가 없습니다.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>디바이스의 생체 인식 기능 사용 여부

PIN을 사용하여 인증 프로세스를 완료하는 경우 디바이스의 지문 또는 안면 인식(생체 인식) 기능을 대신 사용하도록 Microsoft Authenticator 앱을 설정할 수 있습니다. 처음으로 인증자 앱을 사용하여 계정을 확인하는 경우 PIN 대신 사용자 디바이스 생체 인식 기능을 신원 확인으로 사용하는 옵션을 선택하여 이를 설정할 수 있습니다.

## <a name="who-decides-if-you-use-this-feature"></a>이 기능의 사용 여부는 누가 결정하나요?

계정 유형에 따라 사용자가 2단계 인증을 사용하도록 조직에서 결정할 수도 있고, 사용자가 직접 결정할 수도 있습니다.

- **회사 또는 학교 계정** 회사 또는 학교 계정(예: alain@contoso.com)을 사용하는 경우 특정 인증 방법과 함께 2단계 인증을 사용해야 하는지 여부는 조직에서 결정합니다. Microsoft Authenticator 앱에 회사 또는 학교 계정을 추가하는 방법에 대한 자세한 내용은 [회사 또는 학교 계정 추가](user-help-auth-app-add-work-school-account.md)를 참조하세요.

- **개인 Microsoft 계정.** 개인 Microsoft 계정에 2단계 인증을 설정하도록 선택할 수 있습니다(예: alain@outlook.com). 개인 Microsoft 계정 추가에 대한 자세한 내용은 [개인 계정 추가](user-help-auth-app-add-personal-ms-account.md)를 참조하세요.

- **비 Microsoft 계정**을 참조하세요. 비 Microsoft 계정에 2단계 인증을 설정하도록 선택할 수 있습니다(예: alain@gmail.com). 비 Microsoft 계정에서 2단계 인증이라는 용어를 사용하지 않을 수도 있지만, **보안** 또는 **로그인** 설정에서 해당 기능을 찾을 수 있을 것입니다. Microsoft Authenticator 앱은 TOTP 표준을 지원하는 계정과 호환됩니다. Microsoft 이외의 계정 추가에 대한 자세한 내용은 [비 Microsoft 계정 추가](user-help-auth-app-add-non-ms-account.md)를 참조하세요.

## <a name="in-this-section"></a>섹션 내용

| 아티클 | Description |
| ------ | ------------ |
| [앱 다운로드 및 설치](user-help-auth-app-download-install.md) | Android 및 iOS를 실행하는 디바이스용 Microsoft Authenticator 앱을 받아서 설치할 수 있는 위치 및 방법을 설명합니다. |
| [회사 또는 학교 계정 추가](user-help-auth-app-add-work-school-account.md) | Microsoft Authenticator 앱에 다양한 회사 또는 학교 계정과 개인 계정을 추가하는 방법을 설명합니다. |
| [개인 계정 추가](user-help-auth-app-add-personal-ms-account.md) | Microsoft Authenticator 앱에 개인 Microsoft 계정을 추가하는 방법을 설명합니다. |
| [비 Microsoft 계정 추가](user-help-auth-app-add-non-ms-account.md) | Microsoft Authenticator 앱에 Microsoft 이외의 계정을 추가하는 방법을 설명합니다. |
| [수동으로 계정 추가](user-help-auth-app-add-account-manual.md) | 제공된 QR 코드를 스캔할 수 없는 경우 Microsoft Authenticator 앱에 수동으로 계정을 추가하는 방법을 설명합니다. |
| [앱을 사용하여 로그인](user-help-auth-app-sign-in.md) | Microsoft Authenticator 앱을 사용하여 다양한 계정에 로그인하는 방법을 설명합니다.|
| [계정 자격 증명 백업 및 복구](user-help-auth-app-backup-recovery.md) | Microsoft Authenticator 앱을 사용하여 계정 자격 증명을 백업 및 복구하는 방법에 대한 정보를 제공합니다. |
| [Microsoft Authenticator 앱 FAQ](user-help-auth-app-faq.md) | 앱에 대한 질문과 대답입니다. |
