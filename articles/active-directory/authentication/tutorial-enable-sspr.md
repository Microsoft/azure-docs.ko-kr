---
title: Azure Active Directory 셀프 서비스 암호 재설정 사용
description: 이 자습서에서는 사용자 그룹에 대해 Azure Active Directory 셀프 서비스 암호 재설정을 사용하도록 설정하고 암호 재설정 프로세스를 테스트하는 방법을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026316"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>자습서: 사용자가 Azure Active Directory 셀프 서비스 암호 재설정을 사용하여 계정의 잠금을 해제하거나 암호를 다시 설정할 수 있도록 설정

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자 또는 지원 센터에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 사용자 계정이 잠겨 있거나 암호를 잊어버린 경우 프롬프트에 따라 자신을 차단 해제하여 작업을 다시 수행할 수 있습니다. 사용자가 디바이스 또는 애플리케이션에 로그인할 수 없는 경우 이 기능을 통해 지원 센터 호출 및 생산성 저하를 줄일 수 있습니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 관리자가 셀프 서비스 암호 재설정을 사용하도록 설정하는 방법을 보여줍니다. 셀프 서비스 암호 재설정에 이미 등록된 최종 사용자가 계정으로 다시 이동해야 하는 경우 https://aka.ms/sspr 로 이동합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD 사용자 그룹에 대해 셀프 서비스 암호 재설정을 사용하도록 설정
> * 인증 방법 및 등록 옵션 구성
> * 사용자 권한으로 SSPR 프로세스 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *글로벌 관리자* 권한이 있는 계정
* 알고 있는 암호가 있는 관리자가 아닌 사용자(예: *testuser*). 이 자습서에서는 이 계정을 사용하여 최종 사용자 SSPR 환경을 테스트합니다.
    * 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.
* 관리자가 아닌 사용자가 멤버인 그룹(예: *SSPR-Test-Group*). 이 자습서에서는 이 그룹에 대해 SSPR을 사용하도록 설정합니다.
    * 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../active-directory-groups-create-azure-portal.md)를 참조하세요.

## <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

Azure AD를 사용하면 SSPR을 *없음*, *선택된* 사용자 또는 *모든* 사용자에 사용하도록 설정할 수 있습니다. 이 세분화된 기능을 통해 사용자 하위 세트를 선택하여 SSPR 등록 프로세스 및 워크플로를 테스트할 수 있습니다. 프로세스에 익숙해지고 요구 사항을 더 광범위한 사용자 세트에 전달할 수 있는 경우 SSPR을 사용하도록 설정할 사용자 그룹을 추가로 선택할 수 있습니다. 또는 Azure AD 테넌트의 모든 사용자에 대해 SSPR을 사용하도록 설정할 수 있습니다.

이 자습서에서는 테스트 그룹의 사용자 세트에 대해 SSPR을 구성합니다. 다음 예제에서는 *SSPR-Test-Group* 그룹이 사용됩니다. 필요에 따라 사용자 고유의 Azure AD 그룹을 제공합니다.

1. *글로벌 관리자* 권한이 있는 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **암호 재설정**을 선택합니다.
1. **속성** 페이지의 *셀프 서비스 암호 재설정이 사용하도록 설정됨* 옵션 아래에서 **그룹 선택**을 선택합니다.
1. Azure AD 그룹(예: *SSPR-Test-Group*)을 찾아서 선택한 다음, *선택*을 선택합니다.

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    더 광범위한 SSPR 배포의 일부로 중첩된 그룹이 지원됩니다. 선택한 그룹의 사용자에게 적절한 라이선스가 할당되어 있는지 확인합니다. 현재 이러한 라이선스 요구 사항에 대한 유효성 검사 프로세스가 없습니다.

1. 선택한 사용자에 대해 SSPR을 사용하도록 설정하려면 **저장**을 선택합니다.

## <a name="select-authentication-methods-and-registration-options"></a>인증 방법 및 등록 옵션 선택

사용자가 계정의 잠금을 해제하거나 암호를 다시 설정해야 하는 경우 추가 확인 방법을 입력하라는 메시지가 표시됩니다. 이 추가 인증 요소는 승인된 SSPR 이벤트만 완료되도록 합니다. 사용자가 제공하는 등록 정보에 따라 허용할 인증 방법을 선택할 수 있습니다.

1. 왼쪽 메뉴의 **인증 방법** 페이지에서 **재설정에 필요한 방법 수**를 *1*로 설정합니다.

    보안을 향상시키기 위해 SSPR에 필요한 인증 방법 수를 늘릴 수 있습니다.

1. 조직에서 허용하려는 **사용자가 사용할 수 있는 방법**을 선택합니다. 이 자습서에서는 다음 방법을 사용하도록 설정하는 확인란을 선택합니다.

    * *모바일 앱 알림*
    * *모바일 앱 코드*
    * *Email*
    * *휴대폰*
    * *사무실 전화*

1. 인증 방법을 적용하려면 **저장**을 선택합니다.

사용자가 계정의 잠금을 해제하거나 암호를 다시 설정하려면 먼저 연락처 정보를 등록해야 합니다. 이 연락처 정보는 이전 단계에서 구성한 다양한 인증 방법에 사용됩니다.

관리자는 이 연락처 정보를 수동으로 제공하거나 사용자가 등록 포털로 이동하여 해당 정보를 직접 제공할 수 있습니다. 이 자습서에서는 다음에 로그인할 때 등록하라는 메시지가 사용자에게 표시되도록 구성합니다.

1. 왼쪽 메뉴의 **등록** 페이지에서 **사용자가 로그인 시 등록하도록 요구하시겠습니까?** 에 대해 *예*를 선택합니다.
1. 연락처 정보를 최신 상태로 유지하는 것이 중요합니다. SSPR 이벤트가 시작될 때 연락처 정보가 오래된 경우 사용자가 계정의 잠금을 해제하거나 암호를 다시 설정하지 못할 수 있습니다.

    **사용자가 인증 정보를 다시 확인해야 하기 전의 일 수**를 *180*으로 설정합니다.
1. 등록 설정을 적용하려면 **저장**을 선택합니다.

## <a name="configure-notifications-and-customizations"></a>알림 및 사용자 지정 구성

사용자에게 계정 활동에 대한 정보를 계속 제공하기 위해 SSPR 이벤트가 발생할 때 이메일 알림을 보내도록 구성할 수 있습니다. 이러한 알림에는 일반 사용자 계정과 관리자 계정이 모두 포함될 수 있습니다. 관리자 계정의 경우 이 알림은 SSPR을 사용하여 권한 있는 관리자 계정 암호가 다시 설정될 때 추가적인 인식 레이어를 제공합니다.

1. 왼쪽 메뉴의 **알림** 페이지에서 다음 옵션을 구성합니다.

   * **사용자에게 암호 재설정에 대해 알림**을 *예*로 설정합니다.
   * **다른 관리자가 암호를 재설정하면 모든 관리자에게 알림**을 *예*로 설정합니다.

1. 알림 기본 설정을 적용하려면 **저장**을 선택합니다.

SSPR 프로세스에 대한 추가 지원이 필요한 경우 "관리자에게 문의" 링크를 사용자 지정할 수 있습니다. 이 링크는 SSPR 등록 프로세스에서 사용되거나 사용자가 계정의 잠금을 해제하거나 암호를 다시 설정할 때 사용됩니다. 사용자가 필요한 지원을 받도록 하려면 사용자 지정 기술 지원팀 이메일 또는 URL을 제공하는 것이 좋습니다.

1. 왼쪽 메뉴의 **사용자 지정** 페이지에서 *기술 지원팀 링크 사용자 지정*을 **예**로 사용자 지정합니다.
1. **사용자 지정 기술 지원팀 이메일 또는 URL** 필드에서 사용자가 조직으로부터 추가 지원을 받을 수 있는 이메일 주소 또는 웹 페이지 URL(예: *https://support.contoso.com/* )을 제공합니다.
1. 사용자 지정 링크를 적용하려면 **저장**을 선택합니다.

## <a name="test-self-service-password-reset"></a>셀프 서비스 암호 재설정 테스트

SSPR을 사용하도록 설정 및 구성되면 이전 섹션에서 선택한 그룹(예: *Test-SSPR-Group*)에 속한 사용자를 사용하여 SSPR 프로세스를 테스트합니다. 다음 예제에서는 *testuser* 계정이 사용됩니다. 이 자습서의 첫 번째 섹션에서 SSPR을 사용하도록 설정한 그룹에 속한 사용자 계정을 제공합니다.

> [!NOTE]
> 셀프 서비스 암호 재설정을 테스트하는 경우 관리자가 아닌 계정을 사용합니다. 관리자는 항상 셀프 서비스 암호 재설정을 사용하도록 설정되며 두 가지 인증 방법을 사용하여 암호를 다시 설정해야 합니다.

1. 수동 등록 프로세스를 확인하려면 InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)으로 이동합니다. 다음 번에 로그인하면 이 등록 포털로 이동합니다.
1. 관리자가 아닌 테스트 사용자(예: *testuser*) 권한으로 로그인하고, 인증 방법 연락처 정보를 등록합니다.
1. 완료되면 **정상**으로 표시된 단추를 선택하고 브라우저 창을 닫습니다.
1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://aka.ms/sspr](https://aka.ms/sspr)으로 이동합니다.
1. 관리자가 아닌 테스트 사용자의 계정 정보(예: *testuser*)와 CAPTCHA의 문자를 입력하고, **다음**을 선택합니다.

    ![사용자 계정 정보를 입력하여 암호 재설정](media/tutorial-enable-sspr/password-reset-page.png)

1. 확인 단계를 수행하여 암호를 다시 설정합니다. 완료되면 암호가 다시 설정되었음을 알리는 이메일 알림을 받게 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 시리즈의 다음 자습서에서 비밀번호 쓰기 저장을 구성합니다. 이 기능은 Azure AD SSPR의 암호 변경 내용을 온-프레미스 AD 환경에 다시 씁니다. 이 자습서 시리즈를 계속 진행하여 비밀번호 쓰기 저장을 구성하려면 지금 SSPR을 사용하지 않도록 설정하지 마세요.

이 자습서의 일부로 구성한 SSPR 기능을 더 이상 사용하지 않으려면 다음 단계를 사용하여 SSPR 상태를 **없음**으로 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **암호 재설정**을 선택합니다.
1. **속성** 페이지의 *셀프 서비스 암호 재설정이 사용하도록 설정됨* 옵션에서 **없음**을 선택합니다.
1. SSPR 변경 내용을 적용하려면 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 선택한 사용자 그룹에 대해 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD 사용자 그룹에 대해 셀프 서비스 암호 재설정을 사용하도록 설정
> * 인증 방법 및 등록 옵션 구성
> * 사용자 권한으로 SSPR 프로세스 테스트

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication 사용](tutorial-mfa-applications.md)
