---
title: 조건부 액세스 - 결합된 보안 정보 - Azure Active Directory
description: 보안 정보 등록을 위한 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: da68e21aa279ea2503a21ce35eee52f8e49d1434
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049097"
---
# <a name="conditional-access-securing-security-info-registration"></a>조건부 액세스: 보안 정보 등록 보호

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록하는 시기 및 방법에 대한 보안을 설정할 수 있습니다. 이 미리 보기 기능은 [결합된 등록 미리 보기](../authentication/concept-registration-mfa-sspr-combined.md)를 사용하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 신뢰할 수 있는 네트워크 위치와 같은 조건을 사용하여 Azure Multi-Factor Authentication 및 SSPR(셀프 서비스 암호 재설정)에 등록하기 위한 액세스를 제한하려는 조직에서 사용하도록 설정할 수 있습니다. 사용 가능한 조건에 대한 자세한 내용은 [조건부 액세스: 조건](concept-conditional-access-conditions.md) 문서에서 찾을 수 있습니다.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구하는 정책 만들기

다음 정책은 결합된 등록 환경을 사용하여 등록하려고 하는 모든 선택된 사용자에게 적용되며, 신뢰할 수 있는 네트워크로 표시된 위치에서 연결하지 않는 한, 액세스를 차단합니다.

1. **Azure Portal**에서 **Azure Active Directory** > **보안** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 이름에 이 정책의 이름을 입력합니다. 예를 들어 **신뢰할 수 있는 네트워크의 결합된 보안 정보 등록**이라고 지정할 수 있습니다.
1. **할당**에서 **사용자 및 그룹**을 선택하고 이 정책을 적용할 사용자 및 그룹을 선택합니다.

   > [!WARNING]
   > 사용자에 대해 [결합된 등록](../authentication/howto-registration-mfa-sspr-combined.md)을 사용하도록 설정해야 합니다.

1. **클라우드 앱 또는 작업**에서 **사용자 작업**을 선택하고 **보안 정보 등록**을 선택합니다.
1. **조건** > **위치**에서 다음을 수행합니다.
   1. **예**를 구성합니다.
   1. **임의의 위치**를 포함합니다.
   1. **신뢰할 수 있는 모든 위치**를 제외합니다.
   1. 위치 블레이드에서 **완료**를 선택합니다.
   1. 조건 블레이드에서 **완료**를 선택합니다.
1. **조건  > ** **클라이언트 앱(미리 보기)** 에서 **구성**을 **예**로 설정하고 **완료**를 선택합니다.
1. **액세스 제어** > **권한 부여**에서 다음을 수행합니다.
   1. **액세스 차단**을 선택합니다.
   1. 그런 다음 **선택**을 클릭합니다.
1. **정책 사용**을 **켜기**로 설정합니다.
1. 그런 다음 **저장**을 선택합니다.

이 정책의 6단계에서 조직에서 선택할 수 있는 옵션이 제공됩니다. 이 정책에 따르면 위의 작업 진행 중에 신뢰할 수 있는 네트워크 위치에서 등록해야 합니다. 조직에서는 **위치**대신 사용 가능한 모든 조건을 활용하도록 선택할 수 있습니다. 이 정책은 포함된 항목이 차단되고 포함 조건과 일치하지 않는 모든 항목이 허용되도록 하는 차단 정책입니다. 

경우에 따라 위의 6단계에서 위치 대신 디바이스 상태를 사용하도록 선택할 수 있습니다.

6. **조건** > **디바이스 상태(미리 보기)** 에서 다음을 수행합니다.
   1. **예**를 구성합니다.
   1. **모든 디바이스 상태**를 포함합니다.
   1. **디바이스가 하이브리드 Azure AD에 조인됨** 및/또는 **디바이스가 규격으로 표시됨**을 제외합니다.
   1. 위치 블레이드에서 **완료**를 선택합니다.
   1. 조건 블레이드에서 **완료**를 선택합니다.

> [!WARNING]
> 정책에서 디바이스 상태를 조건으로 사용하는 경우 디렉터리의 게스트 사용자에게 영향을 줄 수 있습니다. [보고서 전용 모드](concept-conditional-access-report-only.md)를 사용하면 정책 결정의 영향을 확인하는 데 도움이 됩니다.
> "사용자 작업" 범위의 CA 정책에는 보고서 전용 모드를 적용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
