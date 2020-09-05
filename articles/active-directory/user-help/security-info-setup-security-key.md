---
title: 보안 키를 확인 방법으로 설정 - Azure AD
description: FIDO2(Fast Identity Online) 보안 키를 확인 방법으로 사용하기 위해 ID를 확인하도록 보안 정보(미리 보기) 페이지를 설정하는 방법입니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797669"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>보안 키를 확인 방법으로 설정합니다.

조직 내에서 보안 키를 암호 없는 로그인 방법으로 사용할 수 있습니다. 보안 키는 회사 또는 학교 계정에 로그인하기 위해 고유 PIN과 함께 사용되는 물리적 디바이스입니다. 보안 키에는 물리적 디바이스와 사용자만 알고 있는 정보를 요구하기 때문에 사용자 이름과 암호보다 더 강력한 확인 방법으로 간주됩니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 보안 키 옵션이 보이지 않으면 조직에서 이 옵션을 확인하는 데 사용하도록 허용하지 않는 것일 수 있습니다. 이 경우 다른 방법을 선택하거나 조직의 지원 센터에 문의하여 도움을 요청해야 합니다.

## <a name="security-verification-versus-password-reset-authentication"></a>보안 확인 및 암호 재설정 인증

보안 정보 방법은 2단계 보안 확인 및 암호 재설정에 모두 사용됩니다. 그러나 모든 보안 정보 방법을 둘 모두에 사용할 수 있는 것은 아닙니다.

| 방법 | 사용 대상 |
| ------ | -------- |
| 인증자 앱 | 2단계 인증 및 암호 재설정 인증입니다. |
| 문자 메시지 | 2단계 인증 및 암호 재설정 인증입니다. |
| 전화 통화 | 2단계 인증 및 암호 재설정 인증입니다. |
| 보안 키 | 2단계 인증 및 암호 재설정 인증입니다. |
| 이메일 계정 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |
| 보안 질문 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |

## <a name="what-is-a-security-key"></a>보안 키란?

현재 [Fast Identity Online(FIDO)](https://fidoalliance.org/fido2/) (FIDO2) 암호 없는 인증 프로토콜을 사용하여 다양한 디자인과 보안 키 공급자를 지원하고 있습니다. 이러한 키를 통해 회사 또는 학교 계정에 로그인하여 지원되는 디바이스 및 웹 브라우저에서 조직의 클라우드 기반 리소스에 액세스할 수 있습니다.

관리자 또는 조직은 회사 또는 학교 계정에 필요한 경우 보안 키를 제공합니다. 디바이스에 연결하는 USB 키 또는 NFC 리더를 탭하는 NFC 키 등 사용할 수 있는 보안 키의 유형은 다양합니다. 보안 키의 유형 등 보안 키에 대한 자세한 내용은 제조업체의 설명서에서 확인할 수 있습니다.

> [!Note]
> FIDO2 보안 키를 사용할 수 없는 경우, Microsoft Authenticator 앱 또는 Windows Hello와 같이 사용할 수 있는 다른 암호 없는 인증 방법이 있습니다. Microsoft Authenticator 앱에 대한 자세한 내용은 [Microsoft Authenticator 앱이란?](user-help-auth-app-overview.md)을 참조하세요. Windows Hello에 대한 자세한 내용은 [Windows Hello 개요](https://www.microsoft.com/windows/windows-hello)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

보안 키를 등록하려면 다음 조건이 충족되어야 합니다.

- 관리자가 조직 내에서 이 기능을 사용하도록 설정했습니다.

- 디바이스에서 Windows 10 2019년 5월 업데이트를 실행하고 지원되는 브라우저를 사용하고 있습니다.

- 관리자 또는 조직에서 승인한 물리적 보안 키가 있습니다. 보안 키는 FIDO2 및 Microsoft 규격이어야 합니다. 보안 키와 규격 여부에 대한 질문이 있는 경우 조직의 지원 센터에 문의하세요.

## <a name="register-your-security-key"></a>보안 키 등록

키를 사용하여 회사 또는 학교 계정에 로그인하려면 보안 키를 만들고 고유 PIN을 지정해야 합니다. 계정에 최대 10개의 키가 등록되어 있을 수 있습니다. 

1. https://myaccount.microsoft.com 에서 **내 프로필** 페이지로 이동하여 아직 로그인하지 않았으면 로그인합니다.

2. **보안 정보**와 **메서드 추가**를 차례로 선택한 다음 **메서드 추가** 드롭다운 목록에서 **보안 키**를 선택합니다.

    ![보안 키가 선택된 메서드 추가 상자](media/security-info/security-info-security-key-add-method.png)

3. **추가**를 선택한 다음 **USB 디바이스** 또는 **NFC 디바이스** 중 보유하고 있는 보안 키 유형을 선택합니다.

    ![보유하고 있는 보안 키 유형이 USB인지, NFC인지 선택합니다.](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 보유하고 있는 보안 키 유형을 잘 모르는 경우 제조업체의 설명서를 참조하세요. 제조업체에 대해 잘 모르는 경우에는 조직의 지원 센터에 문의하여 도움을 요청하세요.

4. 보안 키를 물리적으로 사용할 수 있도록 한 다음 **보안 키** 상자에서 **다음**을 선택합니다.

    ![보안 키 시작 등록 상자](media/security-info/security-info-security-key-start-setup.png)

    새 로그인 방법의 설정 과정을 설명해 주는 새 상자가 표시됩니다.

5. **새 로그인 방법 설정** 상자에서 **다음**을 선택하고 아래 작업을 수행합니다.

    - 보안 키가 USB 디바이스인 경우 디바이스의 USB 포트에 보안 키를 삽입합니다.

    - 보안 키가 NFC 디바이스인 경우 사용 중인 리더에 보안 키를 탭합니다.

6. **Windows 보안** 상자에 고유한 보안 키 PIN을 입력한 다음 **확인**을 선택합니다.

    **새 로그인 방법 설정** 상자로 돌아갑니다.

7. **다음**을 선택합니다.

8. **보안 정보** 페이지로 돌아가 나중에 새 보안 키를 인식할 이름을 입력하고 **다음**을 선택합니다.

    ![보안 정보 페이지, 보안 키 이름 지정](media/security-info/security-info-security-key-name.png)

    보안 키가 등록되어 회사 또는 학교 계정에 로그인하는 데 사용할 수 있습니다.

9. **완료**를 선택하여 **보안 키** 상자를 닫습니다.

    **보안 정보** 페이지가 보안 키 정보로 업데이트됩니다.

    ![등록된 메서드가 모두 표시된 보안 정보 페이지](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>보안 정보에서 보안 키 삭제

보안 키를 잃어버리거나 더 이상 사용하지 않으려는 경우 보안 정보에서 키를 삭제할 수 있습니다. 이렇게 하면 보안 키가 회사 또는 학교 계정에서 사용되지 않고, 보안 키가 계속해서 데이터 및 자격 증명 정보를 저장합니다. 보안 키 자체에서 데이터 및 자격 증명 정보를 삭제하려면 이 문서의 [Microsoft 규격 보안 키 다시 설정](#reset-your-security-key) 섹션의 지침을 따라야 합니다.

1. 제거할 보안 키에서 **삭제** 링크를 선택합니다.

2. **보안 키 삭제** 상자에서 **확인**을 선택합니다.

    보안 키가 삭제되어 더 이상 회사 또는 학교 계정에 로그인하는 데 사용할 수 없습니다.

>[!Important]
>보안 키를 실수로 삭제하는 경우 이 문서의 [보안 키 등록 방법](#register-your-security-key) 섹션에 나와 있는 지침을 사용하여 다시 등록할 수 있습니다.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Windows 설정에서 보안 키 설정 관리

보안 키를 다시 설정하고 새 보안 키 PIN을 만드는 등 **Windows 설정** 앱에서 보안 키 설정을 관리할 수 있습니다.

### <a name="reset-your-security-key"></a>보안 키 다시 설정

물리적 보안 키에 저장된 모든 계정 정보를 삭제하려면 키를 공장 기본값으로 다시 반환해야 합니다. 보안 키를 다시 설정하면 키에서 모든 항목이 삭제되어 다시 시작할 수 있습니다.

>[!IMPORTANT]
>보안 키를 다시 설정하면 키에서 모든 항목이 삭제되며 공장 기본값으로 다시 설정됩니다.
>
> **모든 데이터와 자격 증명이 지워집니다.**

#### <a name="to-reset-your-security-key"></a>보안 키를 다시 설정하려면

1. Windows 설정 앱을 열고 **계정**, **로그인 옵션**을 차례로 선택한 다음 **보안 키**와 **관리**를 차례로 선택합니다.

2. 보안 키를 USB 포트에 삽입하거나 NFC 리더를 탭하여 ID를 확인합니다.

3. 특정 보안 키 제조업체에 따라 화면의 지침을 따릅니다. 키 제조업체가 화면의 지침에 나와 있지 않은 경우 자세한 내용은 제조업체 사이트를 참조하세요.

4. **닫기**를 선택하여 **관리** 화면을 닫습니다.

### <a name="create-a-new-security-key-pin"></a>새 보안 키 PIN 만들기

보안 키용으로 새 보안 키 PIN을 만들 수 있습니다.

#### <a name="to-create-a-new-security-key-pin"></a>새 보안 키 PIN을 만들려면

1. Windows 설정 앱을 열고 **계정**, **로그인 옵션**을 차례로 선택한 다음 **보안 키**와 **관리**를 차례로 선택합니다.

2. 보안 키를 USB 포트에 삽입하거나 NFC 리더를 탭하여 ID를 확인합니다.
3. **보안 키 PIN** 영역에서 **추가**를 선택하고 새 보안 키 PIN을 입력 및 확인한 다음 **확인**을 선택합니다.

     보안 키는 회사 또는 학교 계정에서 사용할 수 있도록 새 보안 키 PIN으로 업데이트됩니다. PIN을 다시 변경하려는 경우 **변경** 단추를 선택하면 됩니다.
4. **닫기**를 선택하여 **관리** 화면을 닫습니다.

## <a name="additional-security-info-methods"></a>추가 보안 정보 메서드

보안 키를 등록하려면 하나 이상의 추가 보안 확인 방법이 등록되어 있어야 합니다. 자세한 내용은 [개요 섹션](./security-info-setup-auth-app.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- 암호 없는 확인 방법에 대한 자세한 내용은 [Microsoft의 Azure AD에서 FIDO2 보안 키 공개 미리 보기를 시작하여 암호 없이 로그인 가능](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins) 블로그를 읽어 보거나, [Microsoft Authenticator 앱이란?](user-help-auth-app-overview.md)과 [Windows Hello 개요](https://www.microsoft.com/windows/windows-hello) 문서를 참조하세요.

- [Microsoft 규격 보안 키](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)에 대해 자세히 알아봅니다.

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.