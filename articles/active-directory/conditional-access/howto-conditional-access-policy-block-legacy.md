---
title: 조건부 액세스 - 레거시 인증 차단 - Azure Active Directory
description: 레거시 인증 프로토콜을 차단하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c10f0ab4ff7fd8461ef81da67e50f7e5e174104
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068629"
---
# <a name="conditional-access-block-legacy-authentication"></a>조건부 액세스: 레거시 인증 차단

레거시 인증 프로토콜과 관련된 위험이 증가함에 따라 조직은 이러한 프로토콜을 사용하여 인증 요청을 차단하고 최신 인증을 요구하는 것이 좋습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 레거시 인증 요청을 차단하는 조건부 액세스 정책을 만드는 데 도움이 됩니다. 이 정책은 관리자가 기존 사용자에게 미칠 영향을 확인할 수 있도록 시작할 때 [읽기 전용 모드](howto-conditional-access-insights-reporting.md)로 설정됩니다. 관리자는 정책이 의도한 대로 적용되는 것이 편한 경우 **켜짐**으로 전환하거나, 특정 그룹을 추가하고 다른 그룹을 제외하여 배포를 스테이징할 수 있습니다.

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자**를 선택합니다.
   1. **제외**에서 **사용자 및 그룹**을 선택하고 레거시 인증 사용 기능을 유지해야 하는 계정을 선택합니다. 사용자 자신이 잠기지 않도록 하나 이상의 계정을 제외합니다. 계정을 제외하지 않으면이 정책을 만들 수 없습니다.
   1. **완료**를 선택합니다.
1. **클라우드 앱 또는 작업**에서 **모든 클라우드 앱**을 선택합니다.
   1. **완료**를 선택합니다.
1. **조건**  >  **클라이언트 앱**에서 **구성** 을 **예**로 설정 합니다.
   1. **Exchange ActiveSync 클라이언트** 및 **다른 클라이언트만**확인란을 선택 합니다. Azure에서 Exchange ActiveSync 조건부 액세스 정책을 배포하려면 사용자도 전역 관리자여야 합니다.
   1. **완료**를 선택합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 차단**을 선택합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인하고 **정책 사용**을 **보고 전용**으로 설정합니다.
1. **만들기**를 선택하여 정책을 사용하도록 만듭니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[Office 365 및 Microsoft 365를 사용하여 전자 메일을 보내도록 다기능 디바이스 또는 애플리케이션을 설정하는 방법](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
