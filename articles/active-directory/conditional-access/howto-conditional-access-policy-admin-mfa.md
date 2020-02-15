---
title: 조건부 액세스-관리자에 대 한 MFA 필요-Azure Active Directory
description: 관리자가 multi-factor authentication을 수행 하도록 요구 하는 사용자 지정 조건부 액세스 정책 만들기
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
ms.openlocfilehash: fb396429c95dbed090283752c5a0d9ff5cc176af
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148201"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>조건부 액세스: 관리자에 대해 MFA 필요

관리 권한이 할당 된 계정은 공격자의 대상입니다. 이러한 계정에 대해 MFA (multi-factor authentication)를 요구 하는 것은 해당 계정이 손상 되는 위험을 줄이는 쉬운 방법입니다.

최소한 다음 역할에 대 한 MFA를 요구 하는 것이 좋습니다.

* 대금 청구 관리자
* 조건부 액세스 관리자
* Exchange 관리자
* 전역 관리자
* 기술 지원팀 (암호) 관리자
* 암호 관리자
* 보안 관리자
* SharePoint 관리자
* 사용자 관리자

조직에서는 적합 한 역할을 포함 하거나 제외 하도록 선택할 수 있습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구 이므로 정책에서 다음 계정을 제외 하는 것이 좋습니다.

* 테 넌 트 전체 계정 잠금을 방지 하기 위한 **긴급 액세스** 또는 **투명** 계정 드문 경우 지만 모든 관리자는 테 넌 트에서 잠기므로 응급 액세스 관리 계정을 사용 하 여 테 넌 트에 로그인 할 수 있습니다 .이를 통해 액세스를 복구 하는 단계를 수행 합니다.
   * 자세한 내용은 [AZURE AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)문서에서 찾을 수 있습니다.
* **서비스 계정** 및 **서비스 주체**(예: Azure AD Connect 동기화 계정). 서비스 계정은 특정 사용자에 게 연결 되지 않은 비 대화형 계정입니다. 일반적으로 백 엔드 서비스에서 사용 되며 응용 프로그램에 대 한 프로그래밍 방식 액세스를 허용 합니다. MFA를 프로그래밍 방식으로 완료할 수 없기 때문에 서비스 계정을 제외 해야 합니다.
   * 조직에서 스크립트나 코드에 이러한 계정을 사용 하는 경우 [관리 되는 id](../managed-identities-azure-resources/overview.md)로 대체 하는 것이 좋습니다. 임시 해결 방법으로, 이러한 특정 계정을 기준 정책에서 제외할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 할당 된 관리 역할이 multi-factor authentication을 수행 하도록 요구 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정 합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**아래에서 **사용자 및 그룹** 을 선택 합니다.
   1. **포함**아래에서 **디렉터리 역할 (미리 보기)** 을 선택 하 고 최소한 다음 역할을 선택 합니다.
      * 대금 청구 관리자
      * 조건부 액세스 관리자
      * Exchange 관리자
      * 전역 관리자
      * 기술 지원팀 관리자
      * 암호 관리자
      * 보안 관리자
      * SharePoint 관리자
      * 사용자 관리자
   1. **제외**아래에서 **사용자 및 그룹** 을 선택 하 고 조직의 응급 액세스 또는 사용 중단 계정을 선택 합니다. 
   1. **완료** 를 선택합니다.
1. **포함** > **클라우드 앱 또는 작업** 아래에서 **모든 클라우드 앱**을 선택 하 고 **완료**를 선택 합니다.
1. **액세스 제어** > **권한 부여**에서 **액세스 권한 부여**를 선택 하 고 **multi-factor authentication을 요구**한 다음 **선택**을 선택 합니다.
1. 설정을 확인 하 고 **정책 사용** 을 **켜기**로 설정 합니다.
1. 만들기 **를 선택 하** 여 정책을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용 하 여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
