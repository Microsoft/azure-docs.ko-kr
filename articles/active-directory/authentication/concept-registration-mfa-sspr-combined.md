---
title: SSPR 및 MFA-Azure Active Directory에 대 한 결합 된 등록
description: Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정 등록 (미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c882d286a73900f58ef06e7c51b05c7237e39a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425338"
---
# <a name="combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 (미리 보기)

결합 된 등록 전에 사용자는 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 인증 방법을 별도로 등록 했습니다. 사용자는 비슷한 메서드가 Multi-Factor Authentication 및 SSPR에 사용 되었지만 두 기능 모두에 등록 해야 했습니다. 이제는 결합 된 등록을 통해 사용자가 한 번 등록 하 고 Multi-Factor Authentication 및 SSPR의 이점을 얻을 수 있습니다.

![사용자에 대 한 등록 된 보안 정보를 표시 하는 내 프로필](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

새 환경을 사용 하도록 설정 하기 전에이 관리자 중심 설명서 및 사용자 중심의 설명서를 검토 하 여이 기능의 기능과 영향을 이해 해야 합니다. [사용자 설명서](../user-help/user-help-security-info-overview.md) 에 대 한 교육을 바탕으로 사용자에 게 새로운 환경을 준비 하 고 성공적으로 롤아웃 하도록 도움을 줍니다.

Azure AD 통합 보안 정보 등록은 현재 Azure 미국 정부, Azure 독일 또는 Azure 중국 21Vianet과 같은 국가별 클라우드에서 사용할 수 없습니다.

|     |
| --- |
| Multi-Factor Authentication 및 Azure Active Directory (Azure AD) 셀프 서비스 암호 재설정에 대 한 결합 된 보안 정보 등록은 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

> [!IMPORTANT]
> 원본 미리 보기와 향상 된 결합 등록 환경 모두에 대해 사용 하도록 설정 된 사용자는 새로운 동작을 볼 수 있습니다. 두 환경 모두에 대해 사용 하도록 설정 된 사용자에 게는 새 내 프로필 환경만 표시 됩니다. 새 내 프로필은 결합 된 등록의 모양과 느낌을 맞추고 사용자에 게 원활한 환경을 제공 합니다. 사용자는 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)로 이동 하 여 내 프로필을 볼 수 있습니다.

> [!NOTE] 
> 보안 정보 옵션에 액세스 하려고 하면 오류 메시지가 나타날 수 있습니다. 예를 들어 "죄송 합니다."에 로그인 할 수 없습니다. 이 경우 웹 브라우저에서 타사 쿠키를 차단 하는 구성 또는 그룹 정책 개체가 없는지 확인 합니다. 

내 프로필 페이지는 페이지에 액세스 하는 컴퓨터의 언어 설정에 따라 지역화 됩니다. Microsoft는 브라우저 캐시에서 가장 최근에 사용 된 언어를 저장 하므로 이후 페이지에 대 한 액세스 시도는 마지막으로 사용 된 언어로 계속 렌더링 됩니다. 캐시를 지우면 페이지가 다시 렌더링 됩니다. 특정 언어를 적용 하려는 경우 URL의 끝에 `?lng=<language>`를 추가할 수 있습니다. 여기서 `<language>`은 렌더링 하려는 언어의 코드입니다.

![SSPR 또는 기타 보안 확인 방법 설정](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>결합 된 등록에서 사용할 수 있는 메서드

결합 된 등록에서는 다음과 같은 인증 방법과 작업을 지원 합니다.

|   | 등록 | 변경 | 삭제 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 예 (최대 5 개) | 아닙니다. | 예 |
| 기타 authenticator 앱 | 예 (최대 5 개) | 아닙니다. | 예 |
| 하드웨어 토큰 | 아닙니다. | 아닙니다. | 예 |
| 전화 | 예 | 예 | 예 |
| 대체 전화 | 예 | 예 | 예 |
| 사무실 전화 | 아닙니다. | 아닙니다. | 아닙니다. |
| 이메일 | 예 | 예 | 예 |
| 보안 질문 | 예 | 아닙니다. | 예 |
| 앱 암호 | 예 | 아닙니다. | 예 |

> [!NOTE]
> 앱 암호는 Multi-Factor Authentication에 대해 적용 된 사용자만 사용할 수 있습니다. 조건부 액세스 정책을 통해 Multi-Factor Authentication을 사용 하도록 설정 된 사용자는 앱 암호를 사용할 수 없습니다.

사용자는 다음 옵션 중 하나를 기본 Multi-Factor Authentication 방법으로 설정할 수 있습니다.

- Microsoft Authenticator – 알림.
- Authenticator 앱 또는 하드웨어 토큰-코드
- 전화 통화.
- 텍스트 메시지입니다.

Azure AD에 더 많은 인증 방법을 추가 하기 때문에 이러한 메서드는 결합 된 등록에서 사용할 수 있습니다.

## <a name="combined-registration-modes"></a>결합 된 등록 모드

결합 된 등록에는 인터럽트와 관리의 두 가지 모드가 있습니다.

- **인터럽트 모드** 는 사용자가 로그인 시 보안 정보를 등록 하거나 새로 고칠 때 사용자에 게 표시 되는 마법사와 같은 환경입니다.

- **관리 모드** 는 사용자 프로필의 일부로 서 사용자가 보안 정보를 관리할 수 있도록 합니다.

두 모드 모두 Multi-Factor Authentication에 사용할 수 있는 메서드를 이전에 등록 한 사용자는 Multi-Factor Authentication를 수행 해야 보안 정보에 액세스할 수 있습니다.

### <a name="interrupt-mode"></a>인터럽트 모드

둘 다 테넌트에 대해 사용 하도록 설정 된 경우 결합 된 등록은 Multi-Factor Authentication 및 SSPR 정책을 모두 사용 합니다. 이러한 정책은 로그인 하는 동안 사용자 등록이 중단 되었는지 여부와 등록에 사용할 수 있는 방법을 제어 합니다.

사용자에 게 보안 정보를 등록 하거나 새로 고치는 메시지가 표시 될 수 있는 몇 가지 시나리오는 다음과 같습니다.

- Id 보호를 통해 Multi-Factor Authentication 등록이 적용 됨: 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- 사용자 단위 Multi-Factor Authentication를 통해 등록이 적용 Multi-Factor Authentication: 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- Multi-Factor Authentication 등록은 조건부 액세스 또는 다른 정책을 통해 적용 됩니다. Multi-Factor Authentication 필요한 리소스를 사용 하는 경우 사용자에 게 등록 하 라는 메시지가 표시 됩니다. 사용자가 SSPR를 사용 하도록 설정 된 경우 Multi-Factor Authentication 메서드와 SSPR 메서드를 등록 합니다.
- SSPR 등록이 적용 됨: 로그인 하는 동안 사용자에 게 등록 하 라는 메시지가 표시 됩니다. SSPR 메서드만 등록 합니다.
- SSPR refresh 적용: 사용자가 관리자가 설정한 간격에 따라 보안 정보를 검토 해야 합니다. 사용자에 게 정보가 표시 되며, 필요한 경우 현재 정보를 확인 하거나 변경할 수 있습니다.

등록이 적용 되 면 사용자는 대부분의 보안 수준에서 Multi-Factor Authentication 및 SSPR 정책을 준수 하는 데 필요한 최소 메서드 수를 표시 합니다.

예:

- 사용자가 SSPR에 대해 사용 하도록 설정 되어 있습니다. SSPR 정책에서는 모바일 앱 코드, 전자 메일 및 휴대폰을 다시 설정 하 고 사용 하도록 설정 하는 두 가지 방법이 필요 합니다.
   - 이 사용자는 두 가지 메서드를 등록 해야 합니다.
      - 사용자는 기본적으로 인증자 앱 및 전화에 표시 됩니다.
      - 사용자는 authenticator 앱 또는 휴대폰 대신 전자 메일을 등록 하도록 선택할 수 있습니다.

이 순서도는 로그인 중에 등록이 중단 될 때 사용자에 게 표시 되는 방법을 설명 합니다.

![결합 된 보안 정보 순서도](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Multi-Factor Authentication 및 SSPR를 모두 사용 하는 경우 Multi-Factor Authentication 등록을 적용 하는 것이 좋습니다.

SSPR 정책에서 사용자가 일정 한 간격으로 보안 정보를 검토 해야 하는 경우 로그인 중에 사용자가 중단 되 고 등록 된 모든 메서드를 표시 합니다. 최신 정보를 확인할 수 있습니다. 최신 정보를 확인 하거나 필요한 경우 변경할 수 있습니다. 사용자는이 페이지에 액세스할 때 multi-factor authentication을 수행 해야 합니다.

### <a name="manage-mode"></a>관리 모드

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 이동 하거나 내 프로필에서 **보안 정보** 를 선택 하 여 관리 모드에 액세스할 수 있습니다. 여기에서 사용자는 메서드를 추가 하 고, 기존 메서드를 삭제 하거나 변경 하 고, 기본 메서드를 변경 하는 등의 방법을 사용할 수 있습니다.

## <a name="key-usage-scenarios"></a>주요 사용 시나리오

### <a name="set-up-security-info-during-sign-in"></a>로그인 하는 동안 보안 정보 설정

관리자가 등록을 적용 했습니다.

사용자가 필요한 보안 정보를 모두 설정 하지 않은 상태에서 Azure Portal로 이동 합니다. 사용자 이름 및 암호를 입력 하면 보안 정보를 설정 하 라는 메시지가 표시 됩니다. 그러면 사용자가 마법사에 표시 된 단계에 따라 필요한 보안 정보를 설정 합니다. 설정에서 허용 하는 경우 사용자는 기본적으로 표시 되는 방법이 아닌 다른 방법을 설정 하도록 선택할 수 있습니다. 마법사를 완료 한 후 사용자는 설정 된 방법과 Multi-Factor Authentication의 기본 방법을 검토 합니다. 사용자는 설치 프로세스를 완료 하기 위해 정보를 확인 하 고 Azure Portal을 계속 진행 합니다.

### <a name="set-up-security-info-from-my-profile"></a>내 프로필에서 보안 정보 설정

관리자가 등록을 적용 하지 않았습니다.

필요한 보안 정보를 아직 설정 하지 않은 사용자는 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)로 이동 합니다. 사용자가 왼쪽 창에서 **보안 정보** 를 선택 합니다. 여기서 사용자는 메서드를 추가 하도록 선택 하 고, 사용 가능한 메서드를 선택 하 고, 해당 메서드를 설정 하는 단계를 따릅니다. 완료 되 면 사용자에 게 보안 정보 페이지에 방금 설정 된 메서드가 표시 됩니다.

### <a name="delete-security-info-from-my-profile"></a>내 프로필에서 보안 정보 삭제

이전에 하나 이상의 메서드를 설정한 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동 합니다. 사용자가 이전에 등록 된 메서드 중 하나를 삭제 하도록 선택 합니다. 완료 되 면 사용자에 게 보안 정보 페이지에서 해당 메서드가 더 이상 표시 되지 않습니다.

### <a name="change-the-default-method-from-my-profile"></a>내 프로필에서 기본 방법 변경

이전에 Multi-Factor Authentication 사용할 수 있는 메서드를 하나 이상 설정 하는 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)으로 이동할 수 있습니다. 사용자가 현재 기본 메서드를 다른 기본 메서드로 변경 합니다. 완료 되 면 사용자는 보안 정보 페이지에 새 기본 메서드를 표시 합니다.

## <a name="next-steps"></a>다음 단계

[사용자가 인증 방법을 다시 등록 하도록 강제](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[테넌트에서 결합 된 등록 사용](howto-registration-mfa-sspr-combined.md)

[SSPR 및 MFA 사용량 및 통찰력 보고](howto-authentication-methods-usage-insights.md)

[Multi-Factor Authentication 및 SSPR에 대해 사용 가능한 방법](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication 구성](howto-mfa-getstarted.md)
