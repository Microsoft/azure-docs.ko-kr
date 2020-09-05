---
title: Azure Multi-Factor Authentication 사용
description: 이 자습서에서는 Azure Multi-Factor Authentication을 사용자 그룹에 사용하도록 설정하고, 로그인 이벤트 중에 보조 요소 프롬프트를 테스트하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9072c5611f5bd5a4b8cca082cb2bfd7a1e3f1b2
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718903"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>자습서: Azure Multi-Factor Authentication을 사용하여 사용자 로그인 이벤트 보호

MFA(Multi-Factor Authentication)는 로그인 이벤트 중에 사용자에게 추가 형태의 식별을 요구하는 메시지를 표시하는 프로세스입니다. 이 프롬프트는 휴대폰에서 코드를 입력하거나 지문 검사를 제공하는 것일 수 있습니다. 두 번째 인증 형식이 필요한 경우 이 추가 요소는 공격자가 쉽게 얻거나 복제할 수 있는 것이 아니므로 보안이 향상됩니다.

Azure Multi-Factor Authentication 및 조건부 액세스 정책은 특정 로그인 이벤트 중에 사용자에게 MFA를 사용하도록 설정할 수 있는 유연성을 제공합니다.

> [!IMPORTANT]
> 이 자습서에서는 관리자에게 Azure Multi-Factor Authentication을 사용하도록 설정하는 방법을 보여줍니다.
>
> IT 팀이 Azure Multi-Factor Authentication을 사용하는 기능을 사용하도록 설정하지 않았거나 로그인하는 동안 문제가 발생한 경우 추가 지원을 받으려면 기술 지원팀에 문의하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Multi-Factor Authentication을 사용자 그룹에 사용하도록 설정하는 조건부 액세스 정책 만들기
> * MFA를 요청하는 정책 조건 구성
> * 사용자 권한으로 MFA 프로세스 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* Azure AD Premium P1 또는 평가판 이상의 라이선스를 사용하도록 설정되어 작동하는 Azure AD 테넌트.
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *글로벌 관리자* 권한이 있는 계정
* 알고 있는 암호가 있는 관리자가 아닌 사용자(예: *testuser*). 이 자습서에서는 이 계정을 사용하여 최종 사용자 Azure Multi-Factor Authentication 환경을 테스트합니다.
    * 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../fundamentals/add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.
* 관리자가 아닌 사용자가 멤버인 그룹(예: *MFA-Test-Group*). 이 자습서에서는 이 그룹에 Azure Multi-Factor Authentication을 사용하도록 설정합니다.
    * 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

Azure Multi-Factor Authentication을 사용하도록 설정하고 이를 사용하는 데 추천되는 방법은 조건부 액세스 정책을 사용하는 것입니다. 조건부 액세스를 사용하면 이벤트 로그인에 반응하고 사용자에게 애플리케이션 또는 서비스에 대한 액세스 권한을 부여하기 전에 추가 작업을 요청하는 정책을 만들고 정의할 수 있습니다.

![조건부 액세스를 적용하여 로그인 프로세스를 보호하는 방법에 대한 개략적인 다이어그램](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

사용자가 언제 어디서나 생산성을 높이고 조직을 보호할 수 있도록 조건부 액세스 정책은 세부적이고 구체적일 수 있습니다. 이 자습서에서는 사용자가 Azure Portal에 로그인할 때 MFA를 요구하는 메시지를 표시하는 기본 조건부 액세스 정책을 만들어 보겠습니다. 이 시리즈의 이후 자습서에서는 위험 기반 조건부 액세스 정책을 사용하여 Azure Multi-Factor Authentication을 구성합니다.

먼저 다음과 같이 조건부 액세스 정책을 만들고 사용자의 테스트 그룹을 할당합니다.

1. *글로벌 관리자* 권한이 있는 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **보안**을 선택합니다.
1. **조건부 액세스**, **+ 새 정책**을 차례로 선택합니다.
1. 정책 이름(예: *MFA 파일럿*)을 입력합니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택한 다음, **사용자 및 그룹 선택** 라디오 단추를 선택합니다.
1. **사용자 및 그룹** 확인란, **선택**을 차례로 선택하여 사용 가능한 Azure AD 사용자 및 그룹을 찾습니다.
1. Azure AD 그룹(예: *MFA-Test-Group*)을 찾아서 선택한 다음, **선택**을 선택합니다.

    [ ![조건부 액세스 정책에 사용할 Azure AD 그룹 선택](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. 조건부 액세스 정책을 그룹에 적용하려면 **완료**를 선택합니다.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>다단계 인증 조건 구성

조건부 액세스 정책이 만들어지고 사용자의 테스트 그룹이 할당되면 이제 정책을 트리거하는 클라우드 앱 또는 작업을 정의합니다. 이러한 클라우드 앱 또는 작업은 MFA를 요구하는 것과 같은 추가 처리가 필요한 시나리오입니다. 예를 들어 재무 애플리케이션에 액세스하거나 관리 도구를 사용하는 경우 추가 확인 프롬프트를 요구하도록 결정할 수 있습니다.

이 자습서에서는 사용자가 Azure Portal에 로그인할 때 MFA를 요구하도록 조건부 액세스 정책을 구성합니다.

1. **클라우드 앱 또는 작업**을 선택합니다. 조건부 액세스 정책을 *모든 클라우드 앱* 또는 *앱 선택*에 적용하도록 선택할 수 있습니다. 유연성을 제공하기 위해 정책에서 특정 앱을 제외할 수도 있습니다.

    이 자습서에서는 *포함* 페이지에서 **앱 선택** 라디오 단추를 선택합니다.

1. **선택**을 선택한 다음, 사용할 수 있는 사용 가능한 로그인 이벤트 목록을 찾습니다.

    이 자습서에서는 정책이 Azure Portal의 로그인 이벤트에 적용되도록 **Microsoft Azure 관리**를 선택합니다.

1. 선택한 앱을 적용하려면 **선택**을 선택한 다음, **완료**를 선택합니다.

    ![조건부 액세스 정책에 포함할 Microsoft Azure 관리 앱 선택](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

액세스 제어를 사용하면 승인된 클라이언트 앱이 필요하거나 하이브리드 Azure AD에 조인된 디바이스를 사용하는 것과 같이 사용자에게 액세스 권한을 부여하는 요구 사항을 정의할 수 있습니다. 이 자습서에서는 Azure Portal에 로그인하는 동안 MFA를 요구하도록 액세스 제어를 구성합니다.

1. *액세스 제어* 아래에서 **권한 부여**를 선택한 다음, **액세스 권한 부여** 라디오 단추가 선택되어 있는지 확인합니다.
1. **다단계 인증 필요** 확인란을 선택한 다음, **선택**을 선택합니다.

구성이 사용자에게 미치는 영향을 확인하려면 조건부 액세스 정책을 *보고서 전용*으로 설정하고, 지금 정책을 사용하지 않으려면 *끄기*로 설정할 수 있습니다. 이 자습서에서 사용자 테스트 그룹을 대상으로 하므로 이를 통해 정책을 사용하도록 설정한 다음, Azure Multi-Factor Authentication을 테스트할 수 있습니다.

1. *정책 사용* 토글을 **켜기**로 설정합니다.
1. 조건부 액세스 정책을 적용하려면 **만들기**를 선택합니다.

## <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 테스트

조건부 액세스 정책 및 Azure Multi-Factor Authentication이 작동되는지 확인해 보겠습니다. 먼저 다음과 같이 MFA를 요구하지 않는 리소스에 로그인합니다.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)으로 이동합니다.
1. 관리자가 아닌 테스트 사용자(예: *testuser*) 권한으로 로그인합니다. MFA를 완료하라는 메시지가 표시되지 않습니다.
1. 브라우저 창을 닫습니다.

이제 Azure Portal에 로그인합니다. 조건부 액세스 정책이 Azure Portal에서 추가 확인을 요구하도록 구성되었으므로 Azure Multi-Factor Authentication 프롬프트가 표시됩니다.

1. InPrivate 또는 incognito 모드에서 새 브라우저 창을 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동합니다.
1. 관리자가 아닌 테스트 사용자(예: *testuser*) 권한으로 로그인합니다. Azure Multi-Factor Authentication을 등록하고 사용해야 합니다. 프롬프트에 따라 프로세스를 완료하고 Azure Portal에 성공적으로 로그인했는지 확인합니다.

    ![브라우저 프롬프트에 따라 등록한 다단계 인증 프롬프트에서 로그인합니다.](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. 브라우저 창을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 일부로 구성된 Azure Multi-Factor Authentication을 사용하도록 설정하는 조건부 액세스 정책을 더 이상 사용하지 않으려면 다음 단계에 따라 해당 정책을 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **보안**을 선택합니다.
1. **조건부 액세스**를 선택한 다음, 만든 정책(예: *MFA 파일럿*)을 선택합니다.
1. **삭제**를 선택한 다음, 정책을 삭제할지 여부를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 선택한 사용자 그룹에 조건부 액세스 정책을 사용하여 Azure Multi-Factor Authentication을 사용하도록 설정했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Multi-Factor Authentication을 Azure AD 사용자 그룹에 사용하도록 설정하는 조건부 액세스 정책 만들기
> * MFA를 요청하는 정책 조건 구성
> * 사용자 권한으로 MFA 프로세스 테스트

> [!div class="nextstepaction"]
> [SSPR(셀프 서비스 암호 재설정)에 비밀번호 쓰기 저장 사용](./tutorial-enable-sspr-writeback.md)