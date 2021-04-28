---
title: 앱 암호를 관리하는 방법 - Azure Active Directory | Microsoft Docs
description: 2단계 인증과 관련하여 앱 암호 및 앱 암호의 용도에 대해 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 07303a0b0b3007ade9adb90af7397855a5014cc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98179425"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>2단계 인증을 위한 앱 암호 관리

> [!Important]
>관리자가 사용자의 앱 암호 사용을 허용하지 않을 수 있습니다. **앱 암호** 가 옵션으로 표시되지 않으면 조직에서 사용할 수 없는 것입니다.

앱 암호를 사용하는 경우 다음 사항을 고려해야 합니다.

- 앱 암호는 자동으로 생성되며, 앱당 하나씩 만들어 입력해야 합니다.

- 사용자당 40개의 암호로 제한되어 있습니다. 해당 제한을 초과한 후 암호를 만들려고 하면 새 암호를 만들기 전에 기존 암호를 삭제할 것인지를 묻는 메시지가 표시됩니다.

    >[!Note]
    >Outlook을 포함한 Office 2013 클라이언트는 새로운 인증 프로토콜을 지원하며 2단계 인증과 함께 사용할 수 있습니다. 이 지원으로 2단계 인증을 켜고 나면 Office 2013 클라이언트에는 더 이상 앱 암호가 필요하지 않습니다. 자세한 내용은 [Office 2013 및 Office 2016 클라이언트 앱에 대한 최신 인증 작동 방식](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) 문서를 참조하세요.

## <a name="create-new-app-passwords"></a>새 앱 암호 만들기

초기 2단계 인증을 등록하는 동안 단일 앱 암호가 제공됩니다. 두 개 이상의 암호가 필요한 경우 직접 만들어야 합니다. 조직에서 2단계 인증을 설정하는 방법에 따라 여러 영역에서 앱 암호를 만들 수 있습니다. 회사 또는 학교 계정으로 2단계 인증을 사용하도록 등록하는 방법에 대한 자세한 내용은 [2단계 인증 및 회사 또는 학교 계정에 대한 개요](multi-factor-authentication-end-user-first-time.md) 및 관련 문서를 참조하세요.

### <a name="where-to-create-and-delete-your-app-passwords"></a>앱 암호를 만들고 삭제하는 위치

2단계 인증을 사용하는 방법에 따라 앱 암호를 만들고 삭제할 수 있습니다.

- **조직이 2단계 인증 및 추가 보안 인증 페이지를 사용합니다.** 조직에서 2단계 인증으로 회사 또는 학교 계정(예: alain@contoso.com)을 사용하는 경우 [추가 보안 인증 페이지](https://account.activedirectory.windowsazure.com/Proofup.aspx)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은 이 문서에서 [추가 보안 인증 페이지를 사용하여 앱 암호 만들기 및 삭제](#create-and-delete-app-passwords-from-the-additional-security-verification-page)를 참조하세요.

- **조직이 2단계 인증 및 Office 365 포털을 사용합니다.** 조직에서 회사 또는 학교 계정(예: alain@contoso.com), 2단계 확인 및 Microsoft 365 앱을 사용하는 경우 [Office 365 포털 페이지](https://www.office.com)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은 이 문서에서 [Office 365 포털을 사용하여 앱 암호 만들기 및 삭제](#create-and-delete-app-passwords-using-the-office-365-portal)를 참조하세요.

- **개인 Microsoft 계정을 사용하여 2단계 인증을 사용하고 있습니다.** 2단계 인증으로 개인 Microsoft 계정(예: alain@outlook.com)을 사용하는 경우 [보안 기본 사항 페이지](https://account.microsoft.com/security/)에서 앱 암호를 관리할 수 있습니다. 자세한 지침은 [2단계 인증을 지원하지 않는 앱에서 앱 암호 사용](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)을 참조하세요.

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>추가 보안 인증 페이지에서 앱 암호 만들기 및 삭제

회사 또는 학교 계정에 대한 **추가 보안 인증** 페이지에서 앱 암호를 만들고 삭제할 수 있습니다.

1. [추가 보안 인증 페이지](https://account.activedirectory.windowsazure.com/Proofup.aspx)에 로그인한 다음 **앱 암호** 를 선택합니다.

    ![앱 암호 탭이 강조 표시된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. **만들기** 를 선택하여 앱 암호가 필요한 앱의 이름을 입력하고 **다음** 을 선택합니다.

    ![암호가 필요한 앱의 이름이 있는 앱 암호 만들기 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. **앱 암호 페이지** 에서 암호를 복사한 다음 **닫기** 를 선택합니다.

    ![지정한 앱에 대한 암호가 있는 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. **앱 암호** 페이지에서 앱이 나열되는지 확인합니다.

    ![목록에 새 앱이 표시된 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 앱 암호를 만든 앱(예: Outlook 2010)을 열고 암호 입력 요청 시 앱 암호를 붙여넣습니다. 이 작업은 앱당 한 번만 수행하면 됩니다.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>앱 암호 페이지를 사용하여 앱 암호를 삭제하려면

1. **앱 암호** 페이지에서 삭제할 앱 암호 옆에 있는 **삭제** 를 선택합니다.

   ![앱 암호 페이지에서 앱 암호를 삭제하는 것을 보여 주는 스크린샷](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. **예** 를 선택하여 암호를 삭제할 것인지 확인한 다음, **닫기** 를 선택합니다.

    앱 암호가 성공적으로 삭제되었습니다.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호 만들기 및 삭제

회사 또는 학교 계정 및 Microsoft 365 앱에서 2단계 인증을 사용하는 경우 Office 365 포털을 사용하여 앱 암호를 만들고 삭제할 수 있습니다.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>Office 365 포털을 사용하여 앱 암호를 삭제하려면

1. 회사 또는 학교 계정에 로그인하고 [내 계정 페이지](https://myaccount.microsoft.com)로 이동한 후 **보안 정보** 를 선택합니다.

    ![보안 정보 탭을 표시하는 Office 포털](media/multi-factor-authentication-end-user-app-passwords/mfa-security-info.png)

2. **방법 추가** 를 선택하고 드롭다운 목록에서 **앱 암호** 를 선택한 다음, **추가** 를 클릭합니다.

    ![방법 추가 드롭다운 목록이 표시된 보안 정보 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-add-method.png)

3. 앱 암호에 사용할 이름을 입력한 후 **다음** 을 선택합니다.

    ![앱 암호의 이름이 표시된 앱 암호 만들기 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-enter-app-password-name.png)

4. **앱 암호 페이지** 에서 암호를 복사한 다음, **완료** 를 선택합니다.

    ![생성된 새 앱 암호를 사용하는 앱 암호 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-copy-app-password.png)

5. **보안 정보** 페이지에서 앱 암호가 나열되는지 확인합니다.

    ![새 앱 암호가 목록에 표시된 보안 정보 페이지](media/multi-factor-authentication-end-user-app-passwords/mfa-verify-app-password.png)  

6. 앱 암호를 만든 앱(예: Outlook 2016)을 열고 암호 입력 요청 시 앱 암호를 붙여넣습니다. 이 작업은 앱당 한 번만 수행하면 됩니다.

### <a name="to-delete-app-passwords-using-the-security-info-page"></a>보안 정보 페이지를 사용하여 앱 암호 삭제

1. **보안 정보** 페이지에서 삭제할 앱 암호 옆에 있는 **삭제** 를 선택합니다.

   ![보안 정보 페이지에서 앱 암호를 삭제하는 것을 보여 주는 스크린샷](media/multi-factor-authentication-end-user-app-passwords/mfa-delete-app-password.png)

2. 확인 상자에서 **확인** 을 선택합니다.

    앱 암호가 성공적으로 삭제되었습니다.

## <a name="if-your-app-passwords-arent-working-properly"></a>앱 암호가 제대로 작동하지 않는 경우

암호를 올바르게 입력했는지 확인합니다. 암호를 올바르게 입력했다면 로그인을 다시 시도하고 새 앱 암호를 만들 수 있습니다. 그러한 옵션들로 문제가 해결되지 않는다면 기존 앱 암호를 삭제할 수 있도록 조직의 지원 센터에 문의합니다. 그러면 새로운 앱 암호를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [2단계 인증 설정 관리](multi-factor-authentication-end-user-manage-settings.md)

- [Microsoft Authenticator 앱](user-help-auth-app-download-install.md)을 사용하여 텍스트 또는 전화를 받는 대신 앱 알림으로 로그인을 확인합니다.
