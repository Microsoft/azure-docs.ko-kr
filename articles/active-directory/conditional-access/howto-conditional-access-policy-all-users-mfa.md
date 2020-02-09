---
title: 조건부 액세스-모든 사용자에 대해 MFA 필요-Azure Active Directory
description: 모든 사용자가 multi-factor authentication을 수행 하도록 요구 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52faa2b6167606a46bf189d514a1eb314b443783
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424930"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>조건부 액세스: 모든 사용자에 대해 MFA 필요

Alex Weinert, Microsoft의 Id 보안 디렉터리는 [Pa $ $word가 중요 하지 않습니다](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> 암호는 중요 하지 않지만 MFA는 Microsoft의 연구에 따라 MFA를 사용 하는 경우 계정이 99.9% 이상 손상 될 가능성이 적습니다.

이 문서의 지침을 참조 하 여 조직에서 사용자 환경에 맞게 조정 된 MFA 정책을 만들 수 있습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구 이므로 정책에서 다음 계정을 제외 하는 것이 좋습니다.

* 테넌트 전체 계정 잠금을 방지 하기 위한 **긴급 액세스** 또는 **투명** 계정 드문 경우 지만 모든 관리자는 테넌트에서 잠기므로 응급 액세스 관리 계정을 사용 하 여 테넌트에 로그인 할 수 있습니다 .이를 통해 액세스를 복구 하는 단계를 수행 합니다.
   * 자세한 내용은 [AZURE AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)문서에서 찾을 수 있습니다.
* **서비스 계정** 및 **서비스 원칙**(예: Azure AD Connect 동기화 계정). 서비스 계정은 특정 사용자에 게 연결 되지 않은 비 대화형 계정입니다. 일반적으로 백 엔드 서비스에서 사용 되며 응용 프로그램에 대 한 프로그래밍 방식 액세스를 허용 합니다. MFA를 프로그래밍 방식으로 완료할 수 없기 때문에 서비스 계정을 제외 해야 합니다.
   * 조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다. 임시 해결 방법으로, 이러한 특정 계정을 기준 정책에서 제외할 수 있습니다.

## <a name="application-exclusions"></a>응용 프로그램 제외

조직에는 사용 중인 많은 클라우드 응용 프로그램이 있을 수 있습니다. 이러한 응용 프로그램이 모두 동일한 보안을 요구 하는 것은 아닙니다. 예를 들어 급여 및 참석 응용 프로그램은 MFA를 요구할 수 있지만 낙서는 그렇지 않습니다. 관리자는 특정 응용 프로그램을 해당 정책에서 제외 하도록 선택할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 할당 된 관리 역할이 multi-factor authentication을 수행 하도록 요구 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **조건부 액세스** >  > **보안** **Azure Active Directory** 로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정 합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**아래에서 **사용자 및 그룹** 을 선택 합니다.
   1. **포함**아래에서 **모든 사용자** 를 선택 합니다.
   1. **제외**아래에서 **사용자 및 그룹** 을 선택 하 고 조직의 응급 액세스 또는 사용 중단 계정을 선택 합니다. 
   1. **완료** 를 선택합니다.
1. **포함** > **클라우드 앱 또는 작업** 아래에서 **모든 클라우드 앱**을 선택 합니다.
   1. **제외**에서 multi-factor authentication이 필요 하지 않은 응용 프로그램을 선택 합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 권한 부여**를 선택 하 고 **multi-factor authentication을 요구**한 다음 **선택**을 선택 합니다.
1. 설정을 확인 하 고 **정책 사용** 을 **켜기**로 설정 합니다.
1. 만들기 **를 선택 하** 여 정책을 사용 하도록 설정 합니다.

### <a name="named-locations"></a>명명된 위치

조직에서는 **명명 된 위치** 라고 하는 알려진 네트워크 위치를 조건부 액세스 정책에 통합 하도록 선택할 수 있습니다. 이러한 명명 된 위치에는 본사 위치에 대 한 것과 같은 신뢰할 수 있는 IPv4 네트워크가 포함 될 수 있습니다. 명명 된 위치를 구성 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](location-condition.md) 문서를 참조 하세요.

위의 예 정책에서 조직은 회사 네트워크에서 클라우드 앱에 액세스 하는 경우 multi-factor authentication을 요구 하지 않도록 선택할 수 있습니다. 이 경우 정책에 다음 구성을 추가할 수 있습니다.

1. **할당**에서 **조건** > **위치**를 선택 합니다.
   1. **예**를 구성 합니다.
   1. **모든 위치**를 포함 합니다.
   1. **모든 신뢰할 수 있는 위치**를 제외 합니다.
   1. **완료** 를 선택합니다.
1. **완료** 를 선택합니다.
1. 정책 변경 내용을 **저장** 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용 하 여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
