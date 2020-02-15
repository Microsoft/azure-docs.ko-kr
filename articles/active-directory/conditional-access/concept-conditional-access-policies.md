---
title: 조건부 액세스 정책 빌드-Azure Active Directory
description: 조건부 액세스 정책을 작성 하는 데 사용할 수 있는 모든 옵션은 무엇 이며 무엇을 의미 하나요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87224c9e3e697b86aab51d1e922af8ab2130ac40
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186243"
---
# <a name="building-a-conditional-access-policy"></a>조건부 액세스 정책 빌드

[조건부 액세스 란](overview.md)문서에 설명 된 대로 조건부 액세스 정책은 대입문, **할당** 및 **액세스 제어**에 대 한 것입니다. 조건부 액세스 정책은 신호를 결합 하 고 결정을 내리고 조직 정책을 적용 합니다.

조직에서 이러한 정책을 어떻게 만들 까 요? 필요한 항목

![조건부 액세스 (신호 + 결정 + 적용 = 정책)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>할당

할당 부분은 조건부 액세스 정책의 사용자, 대상 및 위치를 제어 합니다.

### <a name="users-and-groups"></a>개요

[사용자 및 그룹](concept-conditional-access-users-groups.md) 은 정책에 포함 하거나 제외할 사용자를 할당 합니다. 이 할당에는 모든 사용자, 특정 사용자 그룹, 디렉터리 역할 또는 외부 게스트 사용자가 포함 될 수 있습니다. 

### <a name="cloud-apps-or-actions"></a>클라우드 앱 또는 작업

[클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md) 은 정책의 영향을 받는 클라우드 응용 프로그램 또는 사용자 작업을 포함 하거나 제외할 수 있습니다.

### <a name="conditions"></a>조건

정책에는 여러 [조건이](concept-conditional-access-conditions.md)포함 될 수 있습니다.

#### <a name="sign-in-risk"></a>로그인 위험

[Azure AD ID 보호](../identity-protection/overview.md)를 사용 하는 조직의 경우 생성 되는 위험 검색은 조건부 액세스 정책에 영향을 줄 수 있습니다.

#### <a name="device-platforms"></a>디바이스 플랫폼

여러 장치 운영 체제 플랫폼을 사용 하는 조직에서는 다양 한 플랫폼에 특정 정책을 적용 하려고 할 수 있습니다. 

장치 플랫폼을 계산 하는 데 사용 되는 정보는 변경할 수 있는 사용자 에이전트 문자열과 같은 확인 되지 않은 원본에서 제공 됩니다.

#### <a name="locations"></a>위치

위치 데이터는 IP 지리적 위치 데이터에서 제공 됩니다. 관리자는 위치를 정의 하 고 조직의 네트워크 위치와 같이 신뢰할 수 있는 것으로 표시 하도록 선택할 수 있습니다.

#### <a name="client-apps"></a>클라이언트 앱

기본적으로 조건부 액세스 정책은 최신 인증을 지 원하는 브라우저 앱, 모바일 앱 및 데스크톱 클라이언트에 적용 됩니다. 

이 할당 조건을 통해 조건부 액세스 정책은 최신 인증을 사용 하지 않는 특정 클라이언트 응용 프로그램을 대상으로 지정할 수 있습니다. 이러한 응용 프로그램에는 Exchange ActiveSync 클라이언트, 최신 인증을 사용 하지 않는 이전 Office 응용 프로그램, IMAP, MAPI, POP, SMTP 등의 메일 프로토콜이 포함 됩니다.

#### <a name="device-state"></a>디바이스 상태

이 컨트롤은 하이브리드 Azure AD에 가입 되어 있거나 Intune에서 규격으로 표시 된 장치를 제외 하는 데 사용 됩니다. 이 제외는 관리 되지 않는 장치를 차단 하기 위해 수행할 수 있습니다. 

## <a name="access-controls"></a>액세스 제어

조건부 액세스 정책의 액세스 제어 부분은 정책이 적용 되는 방식을 제어 합니다.

### <a name="grant"></a>허용

[Grant](concept-conditional-access-grant.md) 는 관리자에 게 액세스를 차단 하거나 허용할 수 있는 정책 적용 수단을 제공 합니다.

#### <a name="block-access"></a>액세스 차단

액세스를 차단 하면 지정 된 할당에 따라 액세스를 차단할 수 있습니다. Block 컨트롤은 강력 하며 적절 한 정보를 wielded 해야 합니다.

#### <a name="grant-access"></a>액세스 권한 부여

Grant 컨트롤은 하나 이상의 컨트롤에 대 한 적용을 트리거할 수 있습니다. 

- Multi-factor authentication 필요 (Azure Multi-Factor Authentication)
- 장치를 규격으로 표시 해야 함 (Intune)
- 하이브리드 Azure AD 조인된 디바이스 필요
- 승인된 클라이언트 앱 필요
- 앱 보호 정책 필요

관리자는 다음 옵션을 사용 하 여 이전 컨트롤 또는 모든 선택 된 컨트롤 중 하나를 요구 하도록 선택할 수 있습니다. 여러 컨트롤의 기본값은 모두 필요 합니다.

- 모든 선택 된 컨트롤 필요 (컨트롤 및 컨트롤)
- 선택한 컨트롤 (컨트롤 또는 컨트롤) 중 하나가 필요 합니다.

### <a name="session"></a>세션

[세션 컨트롤](concept-conditional-access-session.md) 을 통해 환경을 제한할 수 있습니다. 

- 앱에서 적용된 제한 사항 사용
   - 현재 Exchange Online 및 SharePoint Online 에서만 작동 합니다.
      - 에서는 장치 정보를 전달 하 여 전체 또는 제한 된 액세스 권한을 부여 하는 환경을 제어할 수 있습니다.
- 조건부 액세스 앱 제어 사용
   - Microsoft Cloud App Security의 신호를 사용 하 여 다음과 같은 작업을 수행 합니다. 
      - 중요 한 문서의 다운로드, 잘라내기, 복사 및 인쇄를 차단 합니다.
      - 위험한 세션 동작을 모니터링 합니다.
      - 중요 한 파일의 레이블을 지정 해야 합니다.
- 로그인 빈도
   - 최신 인증에 대 한 기본 로그인 빈도를 변경할 수 있습니다.
- 영구 브라우저 세션
   - 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다.

## <a name="simple-policies"></a>단순 정책

조건부 액세스 정책에 적용 하려면 최소한 다음을 포함 해야 합니다.

- 정책의 **이름** 입니다.
- **할당**
   - 정책을 적용할 **사용자 및/또는 그룹**
   - 정책을 적용할 **클라우드 앱 또는 작업** 입니다.
- **액세스 제어**
   - 컨트롤 **허용** 또는 **차단**

![빈 조건부 액세스 정책](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

[일반적인 조건부 액세스 정책](concept-conditional-access-policy-common.md) 문서에는 대부분의 조직에 유용한 몇 가지 정책이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[클라우드 기반 Azure Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)

[Intune을 사용 하 여 장치 준수 관리](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security 및 조건부 액세스](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
