---
title: SSPR 및 Azure Multi-Factor Authentication에 대 한 결합 된 등록-Azure Active Directory
description: 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록할 수 있도록 하 Azure Active Directory에 대 한 결합 된 등록 환경에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4afc5e554e72fc2ab78173368930b2e5317bce7
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718920"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Azure Active Directory 개요에 대 한 결합 된 보안 정보 등록

결합된 등록 전에는 사용자가 Azure Multi-Factor Authentication 및 SSPR(셀프 서비스 암호 재설정)에 대한 인증 방법을 별도로 등록했습니다. 사용자는 비슷한 메서드가 Multi-Factor Authentication 및 SSPR에 사용 되었지만 두 기능 모두에 등록 해야 했습니다. 이제는 결합 된 등록을 통해 사용자가 한 번 등록 하 고 Multi-Factor Authentication 및 SSPR의 이점을 얻을 수 있습니다.

> [!NOTE]
> 2020 년 8 월 15 일부터 모든 새 Azure AD 테 넌 트가 결합 된 등록에 대해 자동으로 사용 하도록 설정 됩니다.

이 문서에서는 결합 된 보안 등록을 간략하게 설명 합니다. 결합 된 보안 등록을 시작 하려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [결합 된 보안 등록 사용](howto-registration-mfa-sspr-combined.md)

![사용자에 대 한 등록 된 보안 정보를 표시 하는 내 프로필](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

새 환경을 사용 하도록 설정 하기 전에이 관리자 중심 설명서 및 사용자 중심의 설명서를 검토 하 여이 기능의 기능과 영향을 이해 해야 합니다. [사용자 설명서](../user-help/security-info-setup-signin.md) 에 대 한 교육을 바탕으로 사용자에 게 새로운 환경을 준비 하 고 성공적으로 롤아웃 하도록 도움을 줍니다.

Azure AD 통합 보안 정보 등록은 현재 Azure 미국 정부, Azure 독일 또는 Azure 중국 21Vianet과 같은 국가별 클라우드에서 사용할 수 없습니다.

> [!IMPORTANT]
> 원본 미리 보기와 향상 된 결합 등록 환경 모두에 대해 사용 하도록 설정 된 사용자는 새로운 동작을 볼 수 있습니다. 두 환경 모두에 대해 사용 하도록 설정 된 사용자는 새로운 내 프로필 환경만 볼 수 있습니다. 새 *내 프로필* 은 결합 된 등록의 모양과 느낌을 맞추고 사용자에 게 원활한 환경을 제공 합니다. 사용자는로 이동 하 여 내 프로필을 볼 수 있습니다 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) .
>
> "죄송 하지만 로그인 할 수 없습니다."와 같은 보안 정보 옵션에 액세스 하려고 할 때 오류 메시지가 나타날 수 있습니다. 웹 브라우저에서 타사 쿠키를 차단 하는 구성 또는 그룹 정책 개체가 없는지 확인 합니다.

*내 프로필* 페이지는 페이지에 액세스 하는 컴퓨터의 언어 설정에 따라 지역화 됩니다. Microsoft는 브라우저 캐시에서 가장 최근에 사용 된 언어를 저장 하므로 이후 페이지에 대 한 액세스 시도는 마지막으로 사용 된 언어로 계속 렌더링 됩니다. 캐시를 지우면 페이지가 다시 렌더링 됩니다.

특정 언어를 적용 하려는 경우 `?lng=<language>` URL의 끝에를 추가할 수 있습니다 `<language>` . 여기서은 렌더링 하려는 언어의 코드입니다.

![SSPR 또는 기타 보안 확인 방법 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>결합 된 등록에서 사용할 수 있는 메서드

결합 된 등록에서는 다음과 같은 인증 방법과 작업을 지원 합니다.

| 방법 | 등록 | 변경 | DELETE |
| --- | --- | --- | --- |
| Microsoft Authenticator | 예 (최대 5 개) | 아니요 | 예 |
| 기타 authenticator 앱 | 예 (최대 5 개) | 아니요 | 예 |
| 하드웨어 토큰 | 아니요 | 아니요 | 예 |
| 전화 | 예 | 예 | 예 |
| 대체 전화 | 예 | 예 | 예 |
| 사무실 전화 | 아니요 | 아니요 | 아니요 |
| 메일 | 예 | 예 | 예 |
| 본인 확인 질문 | 예 | 아니요 | 예 |
| 앱 암호 | 예 | 아니요 | 예 |
| FIDO2 보안 키<br />*관리 모드는 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지 에서만*| 예 | 예 | 예 |

> [!NOTE]
> 앱 암호는 Multi-Factor Authentication에 대해 적용 된 사용자만 사용할 수 있습니다. 조건부 액세스 정책을 통해 Multi-Factor Authentication을 사용 하도록 설정 된 사용자는 앱 암호를 사용할 수 없습니다.

사용자는 다음 옵션 중 하나를 기본 Multi-Factor Authentication 방법으로 설정할 수 있습니다.

- Microsoft Authenticator – 알림.
- Authenticator 앱 또는 하드웨어 토큰-코드
- 전화 통화.
- 문자 메시지.

Azure AD에 더 많은 인증 방법을 계속 추가 하는 경우 이러한 메서드는 결합 된 등록에서 사용할 수 있습니다.

## <a name="combined-registration-modes"></a>결합 된 등록 모드

결합 된 등록에는 인터럽트와 관리의 두 가지 모드가 있습니다.

- **인터럽트 모드** 는 사용자가 로그인 시 보안 정보를 등록 하거나 새로 고칠 때 사용자에 게 표시 되는 마법사와 같은 환경입니다.
- **관리 모드** 는 사용자 프로필의 일부로 서 사용자가 보안 정보를 관리할 수 있도록 합니다.

두 모드 모두 Multi-Factor Authentication에 사용할 수 있는 메서드를 이전에 등록 한 사용자는 보안 정보에 액세스 하기 전에 Multi-Factor Authentication를 수행 해야 합니다. 사용자는 이전에 등록 된 메서드를 계속 사용 하기 전에 정보를 확인 해야 합니다. 

### <a name="interrupt-mode"></a>인터럽트 모드

둘 다 테 넌 트에 대해 사용 하도록 설정 된 경우 결합 된 등록은 Multi-Factor Authentication 및 SSPR 정책을 모두 사용 합니다. 이러한 정책은 로그인 하는 동안 사용자 등록이 중단 되었는지 여부와 등록에 사용할 수 있는 방법을 제어 합니다.

다음은 사용자에 게 보안 정보를 등록 하거나 새로 고치는 메시지가 표시 될 수 있는 예제 시나리오입니다.

- *Multi-Factor Authentication 등록은 Id 보호를 통해 적용 됩니다.* 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- *사용자 단위 Multi-Factor Authentication를 통해 Multi-Factor Authentication 등록을 적용 합니다.* 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- *Multi-Factor Authentication 등록은 조건부 액세스 또는 다른 정책을 통해 적용 됩니다.* 사용자가 Multi-Factor Authentication 필요한 리소스를 사용 하는 경우 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- *SSPR 등록이 적용 됨:* 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. SSPR 메서드만 등록 합니다.
- *SSPR 새로 고침 적용:* 사용자는 관리자가 설정한 간격에 따라 보안 정보를 검토 해야 합니다. 사용자에 게 정보가 표시 되며, 필요한 경우 현재 정보를 확인 하거나 변경할 수 있습니다.

등록이 적용 되 면 사용자는 대부분의 보안 수준에서 Multi-Factor Authentication 및 SSPR 정책을 준수 하는 데 필요한 최소 메서드 수를 표시 합니다.

다음 예제 시나리오를 살펴보십시오.

- 사용자가 SSPR에 대해 사용 하도록 설정 되어 있습니다. SSPR 정책에서는 모바일 앱 코드, 전자 메일 및 휴대폰을 다시 설정 하 고 사용 하도록 설정 하는 두 가지 방법이 필요 합니다.
- 이 사용자는 두 가지 메서드를 등록 해야 합니다.
   - 사용자는 기본적으로 인증자 앱 및 전화에 표시 됩니다.
   - 사용자는 authenticator 앱 또는 휴대폰 대신 전자 메일을 등록 하도록 선택할 수 있습니다.

다음 순서도에서는 로그인 중에 등록이 중단 될 때 사용자에 게 표시 되는 방법을 설명 합니다.

![결합 된 보안 정보 순서도](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Multi-Factor Authentication 및 SSPR를 모두 사용 하는 경우 Multi-Factor Authentication 등록을 적용 하는 것이 좋습니다.

SSPR 정책에서 사용자가 일정 한 간격으로 보안 정보를 검토 해야 하는 경우 로그인 중에 사용자가 중단 되 고 등록 된 모든 메서드를 표시 합니다. 최신 정보를 확인할 수 있습니다. 최신 정보를 확인 하거나 필요한 경우 변경할 수 있습니다. 사용자는이 페이지에 액세스할 때 multi-factor authentication을 수행 해야 합니다.

### <a name="manage-mode"></a>관리 모드

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 내 프로필에서 **보안 정보** 를 선택 하 여 또는로 이동 하 여 관리 모드에 액세스할 수 있습니다. 여기에서 사용자는 메서드를 추가 하 고, 기존 메서드를 삭제 하거나 변경 하 고, 기본 메서드를 변경 하는 등의 방법을 사용할 수 있습니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인하는 동안 보안 정보 설정

관리자가 등록을 적용 했습니다.

사용자가 필요한 보안 정보를 모두 설정 하지 않은 상태에서 Azure Portal로 이동 합니다. 사용자 이름 및 암호를 입력 하면 보안 정보를 설정 하 라는 메시지가 표시 됩니다. 그러면 사용자가 마법사에 표시 된 단계에 따라 필요한 보안 정보를 설정 합니다. 설정에서 허용 하는 경우 사용자는 기본적으로 표시 되는 방법이 아닌 다른 방법을 설정 하도록 선택할 수 있습니다. 마법사를 완료 한 후 사용자는 설정 된 방법과 Multi-Factor Authentication의 기본 방법을 검토 합니다. 사용자는 설치 프로세스를 완료 하기 위해 정보를 확인 하 고 Azure Portal을 계속 진행 합니다.

### <a name="set-up-security-info-from-my-profile"></a>내 프로필에서 보안 정보 설정

관리자가 등록을 적용 하지 않았습니다.

필요한 보안 정보를 아직 설정 하지 않은 사용자는로 이동 [https://myprofile.microsoft.com](https://myprofile.microsoft.com) 합니다. 사용자가 왼쪽 창에서 **보안 정보** 를 선택 합니다. 여기서 사용자는 메서드를 추가 하도록 선택 하 고, 사용 가능한 메서드를 선택 하 고, 해당 메서드를 설정 하는 단계를 따릅니다. 완료 되 면 사용자에 게 보안 정보 페이지에 설정 된 메서드가 표시 됩니다.

### <a name="delete-security-info-from-my-profile"></a>내 프로필에서 보안 정보 삭제

이전에 하나 이상의 메서드를 설정한 사용자는로 이동 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 합니다. 사용자가 이전에 등록 된 메서드 중 하나를 삭제 하도록 선택 합니다. 완료 되 면 사용자에 게 보안 정보 페이지에서 해당 메서드가 더 이상 표시 되지 않습니다.

### <a name="change-the-default-method-from-my-profile"></a>내 프로필에서 기본 방법 변경

이전에 Multi-Factor Authentication로 탐색 하는 데 사용할 수 있는 메서드를 하나 이상 설정한 사용자 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 입니다. 사용자가 현재 기본 메서드를 다른 기본 메서드로 변경 합니다. 완료 되 면 사용자는 보안 정보 페이지에 새 기본 메서드를 표시 합니다.

## <a name="next-steps"></a>다음 단계

시작 하려면 [셀프 서비스 암호 재설정을 사용](tutorial-enable-sspr.md) 하도록 설정 하 고 [Azure Multi-Factor Authentication를 사용](tutorial-enable-azure-mfa.md)하도록 설정 하는 자습서를 참조 하세요.

[테 넌 트에서 결합 된 등록을 사용 하도록 설정](howto-registration-mfa-sspr-combined.md) 하거나 [사용자가 인증 방법을 다시 등록](howto-mfa-userdevicesettings.md#manage-user-authentication-options)하도록 하는 방법에 대해 알아봅니다.

[Azure Multi-Factor Authentication 및 SSPR에 사용할 수 있는 방법](concept-authentication-methods.md)을 검토할 수도 있습니다.