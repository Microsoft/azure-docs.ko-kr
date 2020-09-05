---
title: 로그인 위험 기반 조건부 액세스-Azure Active Directory
description: Id 보호 로그인 위험을 사용 하 여 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c6e3316afc09992d16e17d9d2e2afe6b92dcd22
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049046"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>조건부 액세스: 로그인 위험 기반 조건부 액세스

대부분의 사용자는 추적 가능한 일반 동작을 갖고 있으며, 정상 범위를 벗어나면 사용자가 로그인하도록 허용하는 것이 위험할 수 있습니다. 해당 사용자를 차단 하거나 multi-factor authentication을 수행 하도록 요청 하는 것이 사실 사용자의 신원을 증명할 수 있습니다. 

로그인 위험은 지정 된 인증 요청이 id 소유자에 의해 권한이 부여 되지 않은 확률을 나타냅니다. Azure AD Premium P2 라이선스가 있는 조직은 [로그인 위험 검색 Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)통합 하는 조건부 액세스 정책을 만들 수 있습니다.

이 정책이 할당 될 수 있는 두 가지 위치가 있습니다. 조직에서는 보안 암호 변경을 요구 하는 로그인 위험 기반 조건부 액세스 정책을 사용 하도록 설정 하려면 다음 옵션 중 하나를 선택 해야 합니다.

## <a name="enable-with-conditional-access-policy"></a>조건부 액세스 정책을 사용 하 여 사용

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자**를 선택합니다.
   1. **제외**에서 **사용자 및 그룹**을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료**를 선택합니다.
1. **클라우드 앱 또는 작업**  >  **포함**아래에서 **모든 클라우드 앱**을 선택 합니다.
1. **조건**  >  **사용자 위험**에서 **구성** 을 **예**로 설정 합니다. **이 정책이 적용 될 로그인 위험 수준 선택에서 다음을 적용** 합니다. 
   1. **높음** 및 **보통**을 선택 합니다.
   1. **완료**를 선택합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 권한 부여**, **다단계 인증 요구**, **선택**을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들어 사용하도록 설정합니다.

## <a name="enable-through-identity-protection"></a>Id 보호를 통해 사용

1. **Azure Portal**에 로그인합니다.
1. **모든 서비스**를 선택한 다음 **Azure AD ID 보호**로 이동 합니다.
1. **로그인 위험 정책**을 선택 합니다.
1. **할당**에서 **사용자**를 선택 합니다.
   1. **포함**에서 **모든 사용자**를 선택합니다.
   1. **제외**아래에서 **제외 된 사용자 선택**을 선택 하 고 조직의 응급 액세스 또는 손상 없는 계정을 선택한 다음 **선택**을 선택 합니다.
   1. **완료**를 선택합니다.
1. **조건**에서 **로그인 위험**을 선택한 다음 **보통 이상**을 선택 합니다.
   1. **선택**, **완료**를 차례로 선택 합니다.
1. 액세스 **제어**에서  >  **Access** **액세스 허용**을 선택한 다음 **다단계 인증 필요**를 선택 합니다.
   1. **선택**을 선택합니다.
1. **정책 적용** 을 **켜기**로 설정 합니다.
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[사용자 위험 기반 조건부 액세스](howto-conditional-access-policy-risk-user.md)

[조건부 액세스 보고 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory Identity Protection이란?](../identity-protection/overview-identity-protection.md)