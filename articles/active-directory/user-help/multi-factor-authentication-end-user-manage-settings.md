---
title: 2단계 인증 방법 및 설정 변경 - Azure Active Directory
description: 추가 보안 인증 페이지에서 회사 또는 학교 계정의 보안 인증 방법과 설정을 변경하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798383"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>2단계 인증 방법 및 설정 변경

회사 또는 학교 계정의 보안 인증 방법을 설정한 후에는 다음과 같은 관련 세부 정보를 업데이트할 수 있습니다.

- 기본 보안 인증 방법

- 보안 인증 방법 세부 정보(예: 전화 번호)

- Authenticator 앱 설정 또는 Authenticator 앱에서 디바이스 삭제

## <a name="using-the-additional-security-verification-page"></a>추가 보안 인증 페이지 사용하기

조직에서 2단계 인증을 설정하고 관리하는 방법을 상세하게 안내한 경우, 먼저 해당 지침을 따라야 합니다. 조직에서 안내하지 않은 경우에는 [추가 보안 인증](./multi-factor-authentication-end-user-first-time.md) 페이지에서 보안 인증 방법 설정을 확인할 수 있습니다.

>[!Note]
>화면에 표시되는 내용이 이 문서에서 안내하는 내용과 같지 않다면 관리자가 **보안 정보(미리 보기)** 환경을 설정한 것이거나 조직에서 사용자 지정 포털을 제공하는 것입니다. 새로운 보안 정보 환경에 대한 자세한 내용은 [보안 정보(미리 보기) 개요](./security-info-setup-signin.md)를 참조하세요. 조직의 사용자 지정 포털에 대한 자세한 내용은 조직의 기술 지원팀으로 문의하세요.

### <a name="to-get-to-the-additional-security-verification-page"></a>추가 보안 인증 페이지로 이동하려면

[추가 보안 인증 페이지 링크](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)를 클릭하면 됩니다.

![사용 가능한 보안 인증 방법 세부 정보가 표시된 추가 보안 인증 페이지](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

다음 단계에 따라 **추가 보안 인증** 페이지로 이동할 수도 있습니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

1. 오른쪽 맨 위에서 계정 이름을 선택한 후 **프로필**을 선택합니다.

1. **추가 보안 인증**을 선택합니다.  

    ![추가 보안 인증 페이지로 연결되는 내 앱 링크](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>**추가 보안 인증** 페이지의 **앱 암호** 섹션을 사용하는 방법에 대한 자세한 내용은 [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md)를 참조하세요. 앱 암호는 2단계 인증을 지원하지 않는 앱에서만 사용해야 합니다.

## <a name="change-your-default-security-verification-method"></a>기본 보안 인증 방법 변경

사용자 이름과 암호를 사용하여 회사 또는 학교 계정에 로그인하면 선택된 보안 인증 방법이 자동으로 표시됩니다. 조직의 요구 사항에 따라, 이 방법은 Authenticator 앱을 통한 알림 또는 확인 코드, 문자 메시지 또는 전화 통화일 수 있습니다.

사용 중인 기본 보안 인증 방법을 변경하려면 여기에서 그렇게 할 수 있습니다.

### <a name="to-change-your-default-security-verification-method"></a>기본 보안 인증 방법을 변경하려면

1. **추가 보안 인증** 페이지의 **기본 설정된 옵션은 무엇입니까?** 목록에서 사용할 방법을 선택합니다. 모든 옵션이 표시되지만, 조직에서 사용 가능하도록 설정한 옵션만 선택할 수 있습니다.

    - **앱을 통해 알림**: Authenticator 앱으로 대기 중인 인증 프롬프트가 있다는 알림을 받습니다.

    - **내 인증 전화로 전화 걸기**: 모바일 디바이스로 정보 인증을 요청하는 전화 통화가 걸려옵니다.

    - **인증 전화로 텍스트 코드 전송**: 모바일 디바이스로 확인 코드 문자 메시지가 전송됩니다. 회사 또는 학교 계정의 인증 프롬프트에 이 코드를 입력해야 합니다.

    - **내 사무실 전화로 전화 걸기**: 사무실 전화로 정보 인증을 요청하는 전화 통화가 걸려옵니다.

    - **앱의 확인 코드 사용**: Authenticator 앱을 사용하여 확인 코드를 받아서 회사 또는 학교 계정의 프롬프트에 입력합니다.

2. **저장**을 선택합니다.

## <a name="add-or-change-your-phone-number"></a>전화 번호 추가 또는 변경

**추가 보안 인증** 페이지에서 새 전화 번호를 추가하거나 기존 전화 번호를 업데이트할 수 있습니다.

>[!Important]
>기본 전화를 분실하거나 도난당했을 때 또는 새 전화로 바꾸어서 더 이상 원래의 기본 전화 번호를 사용할 수 없을 때 계정이 잠기는 일을 방지하도록 보조 전화 번호를 추가하는 것이 좋습니다.

### <a name="to-change-your-phone-numbers"></a>전화 번호를 변경하려면:

1. **추가 보안 인증** 페이지의 **어떤 방법으로 응답하시겠습니까?** 섹션에서 **인증 전화**(기본 모바일 디바이스) 및 **사무실 전화**의 전화 번호 정보를 업데이트합니다.

1. **대체 인증 전화** 옵션 옆의 상자를 선택 하 고 기본 장치에 액세스할 수 없는 경우 전화 통화를 받을 수 있는 보조 전화 번호를 입력 합니다.

1. **저장**을 선택합니다.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 새 계정 추가

[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)용 Microsoft Authenticator 앱에서 회사 또는 학교 계정을 설정할 수 있습니다.

Microsoft Authenticator 앱에서 이미 회사 또는 학교 계정을 설정했다면 다시 설정하지 않아도 됩니다.

1. **추가 보안 인증** 페이지의 **어떤 방법으로 응답하시겠습니까?** 섹션에서 **Authenticator 앱 설정**을 선택합니다.

    ![Microsoft Authenticator 앱에서 회사 또는 학교 계정 설정](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. 모바일 디바이스에서 QR 코드를 스캔하는 작업을 포함하여 화면상의 지침에 따라 진행한 후 **다음**을 선택합니다.

    정보 확인을 위해 Microsoft Authenticator 앱에서 알림을 승인하라는 메시지가 표시됩니다.

1. **저장**을 선택합니다.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에서 계정 또는 디바이스 삭제

Microsoft Authenticator 앱에서 계정을 삭제할 수 있고, 회사 또는 학교 계정에서 디바이스를 삭제할 수 있습니다. 일반적으로 분실 또는 도난되었거나 오래된 디바이스를 계정에서 영구 제거하려는 경우 디바이스를 삭제하고, 연결 문제를 해결하거나 새로운 사용자 이름과 같은 계정 변경 사항을 반영하려는 경우 계정을 삭제합니다.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>회사 또는 학교 계정에서 디바이스를 삭제하려면

1. **추가 보안 인증** 페이지의 **어떤 방법으로 응답하시겠습니까?** 섹션에서 **Authenticator 앱 설정** 단추를 선택합니다.

1. **저장**을 선택합니다.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에서 계정을 삭제하려면

Microsoft Authenticator 앱에서 삭제하려는 디바이스 옆에 있는 **삭제** 단추를 선택합니다.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>신뢰할 수 있는 디바이스에서 2단계 인증 프롬프트 설정

조직의 설정에 따라 브라우저에서 2단계 인증을 수행할 때 **X일 동안 다시 묻지 않음**이라는 확인란이 표시될 수 있습니다. 2단계 인증 프롬프트를 표시하지 않으려고 이 옵션을 선택한 상태에서 디바이스를 분실하거나 디바이스의 보안이 침해된 경우, 계정을 보호하기 위해 2단계 인증 프롬프트를 다시 설정해야 합니다. 모든 디바이스에 대해 동시에 프롬프트를 설정해야 합니다. 특정 디바이스에 대해서만 프롬프트를 설정할 수는 없습니다.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>디바이스에서 2단계 인증 프롬프트를 설정하려면

[**추가 보안 인증** 페이지](#to-get-to-the-additional-security-verification-page)에서 **신뢰할 수 있는 이전 디바이스에서 Multi-Factor Authentication 복원**을 선택합니다. 다음번에 디바이스에 로그인하면 2단계 인증을 수행하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

2단계 인증 설정을 추가 또는 업데이트한 후에는 앱 암호를 관리하거나, 로그인하거나, 몇 가지 일반적인 2단계 인증 관련 문제에 관한 도움말을 얻을 수 있습니다.

- [2단계 인증을 위한 앱 암호 관리](multi-factor-authentication-end-user-app-passwords.md): 2단계 인증을 지원하지 않는 앱의 경우

- [2단계 인증을 사용하여 로그인](multi-factor-authentication-end-user-signin.md)

- [2단계 인증에 대한 일반적인 문제](multi-factor-authentication-end-user-troubleshoot.md)