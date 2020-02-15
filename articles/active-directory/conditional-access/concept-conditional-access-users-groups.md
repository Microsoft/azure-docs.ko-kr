---
title: 조건부 액세스 정책의 사용자 및 그룹-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 사용자 및 그룹
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
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192130"
---
# <a name="conditional-access-users-and-groups"></a>조건부 액세스: 사용자 및 그룹

조건부 액세스 정책에는 의사 결정 프로세스의 신호 중 하나로 사용자 할당을 포함 해야 합니다. 사용자는 조건부 액세스 정책에서 포함 하거나 제외할 수 있습니다. 

![조건부 액세스의 결정에 대 한 신호로 사용 되는 사용자](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>사용자 포함

일반적으로이 사용자 목록에는 조직이 조건부 액세스 정책에서 대상으로 하는 모든 사용자가 포함 됩니다. 

조건부 액세스 정책을 만들 때 사용할 수 있는 옵션은 다음과 같습니다.

- None
   - 사용자를 선택 하지 않음
- 모든 사용자가 액세스할 수 있습니다.
   - B2B 게스트를 비롯 하 여 디렉터리에 있는 모든 사용자입니다.
- 사용자 및 그룹 선택
   - 모든 게스트 및 외부 사용자 (미리 보기)
      - 이 선택 항목에는 `user type` 특성이 `guest`로 설정 된 모든 사용자를 포함 하는 B2B 게스트 및 외부 사용자가 포함 됩니다. 이 선택 항목은 CSP (클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인 한 모든 외부 사용자에도 적용 됩니다. 
   - 디렉터리 역할 (미리 보기)
      - 관리자가 할당을 확인 하는 데 사용 되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당 된 사용자에 게 더 제한적인 정책을 만들 수 있습니다.
   - 개요
      - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어, 조직에서는 HR 앱이 클라우드 앱으로 선택 된 경우 HR 부서의 모든 구성원을 포함 하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

## <a name="exclude-users"></a>사용자 제외

제외는 일반적으로 응급 액세스 또는 투명 계정에 사용 됩니다. 응급 액세스 계정에 대 한 자세한 내용 및 중요 한 이유는 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용 하 여 복원 력 있는 액세스 제어 관리 전략 만들기](../authentication/concept-resilient-controls.md)

조건부 액세스 정책을 만들 때 다음 옵션을 제외 하는 데 사용할 수 있습니다.

- 모든 게스트 및 외부 사용자 (미리 보기)
   - 이 선택 항목에는 `user type` 특성이 `guest`로 설정 된 모든 사용자를 포함 하는 B2B 게스트 및 외부 사용자가 포함 됩니다. 이 선택 항목은 CSP (클라우드 솔루션 공급자)와 같은 다른 조직에서 로그인 한 모든 외부 사용자에도 적용 됩니다. 
- 디렉터리 역할 (미리 보기)
   - 관리자가 할당을 확인 하는 데 사용 되는 특정 Azure AD 디렉터리 역할을 선택할 수 있습니다. 예를 들어 조직은 전역 관리자 역할이 할당 된 사용자에 게 더 제한적인 정책을 만들 수 있습니다.
- 개요
   - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어, 조직에서는 HR 앱이 클라우드 앱으로 선택 된 경우 HR 부서의 모든 구성원을 포함 하는 그룹을 선택할 수 있습니다. 그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
