---
title: Id 보호 및 B2B 사용자-Azure Active Directory
description: B2B 사용자가 Id 보호를 사용 하 여 작동 및 알려진 제한 사항
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ec9e110c3a476c9096ae3e216c9780da0e0f49
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949199"
---
# <a name="identity-protection-and-b2b-users"></a>ID 보호 및 B2B 사용자

Azure AD B2B 공동 작업을 통해 조직에서는 Id 보호를 사용 하 여 B2B 사용자에 대 한 위험 기반 정책을 적용할 수 있습니다. 이러한 정책은 두 가지 방법으로 구성 됩니다.

- 관리자는 게스트 사용자를 포함 하는 모든 앱에 적용 되는 기본 제공 Id 보호 위험 기반 정책을 구성할 수 있습니다.
- 관리자는 로그인 위험을 조건으로 사용 하 여 게스트 사용자를 포함 하는 조건부 액세스 정책을 구성할 수 있습니다.

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대 한 위험 평가 방법

B2B 공동 작업 사용자에 대 한 사용자 위험은 자신의 홈 디렉터리에서 평가 됩니다. 이러한 사용자에 대 한 실시간 로그인 위험은 리소스 디렉터리에서 리소스에 액세스 하려고 할 때 평가 됩니다.

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 공동 작업 사용자에 대 한 Id 보호의 제한 사항

자신의 홈 디렉터리에 있는 id로 인해 리소스 디렉터리에서 B2B 공동 작업 사용자에 대 한 Id 보호 구현에는 제한 사항이 있습니다. 주요 제한 사항은 다음과 같습니다.

- 게스트 사용자가 암호 재설정을 강제 하는 Id 보호 사용자 위험 정책을 트리거하는 경우 **차단**됩니다. 이 블록은 리소스 디렉터리에서 암호를 다시 설정할 수 없기 때문에 발생 합니다.
- **게스트 사용자는 위험한 사용자 보고서에 표시 되지**않습니다. 이러한 표시의 손실은 B2B 사용자의 홈 디렉터리에서 발생 하는 위험 평가 때문에 발생 합니다.
- 관리자는 리소스 디렉터리에서 **위험한 B2B 공동 작업 사용자를 해제 하거나 수정할 수 없습니다** . 이러한 기능 손실은 리소스 디렉터리의 관리자가 B2B 사용자의 홈 디렉터리에 액세스할 수 없기 때문입니다.

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>내 디렉터리에서 위험한 B2B 공동 작업 사용자를 수정할 수 없는 이유는 무엇입니까?

B2B 사용자에 대 한 위험 평가 및 수정은 홈 디렉터리에서 발생 합니다. 이로 인해 게스트 사용자는 리소스 디렉터리의 위험한 사용자 보고서에 표시 되지 않으며 리소스 디렉터리의 관리자는 이러한 사용자에 대해 보안 암호 재설정을 강제할 수 없습니다.

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>조직의 위험 기반 정책으로 인해 B2B 공동 작업 사용자가 차단 된 경우 어떻게 해야 하나요?

디렉터리의 위험한 B2B 사용자가 위험 기반 정책에 의해 차단 되는 경우 사용자는 홈 디렉터리에서 해당 위험을 수정 해야 합니다. 사용자는 자신의 홈 디렉터리에서 보안 암호 재설정을 수행 하 여 위험을 수정할 수 있습니다. 자신의 홈 디렉터리에서 셀프 서비스 암호 재설정을 사용 하도록 설정 하지 않은 경우 관리자가 수동으로 위험을 해제 하거나 암호를 다시 설정 하도록 하려면 조직의 IT 담당자에 게 문의 해야 합니다.

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>B2B 공동 작업 사용자가 위험 기반 정책의 영향을 받지 않도록 어떻게 할까요?

조직의 위험 기반 조건부 액세스 정책에서 B2B 사용자를 제외 하면 B2B 사용자가 위험 평가에 의해 영향을 받거나 차단 되지 않습니다. 이러한 B2B 사용자를 제외 하려면 Azure AD에서 조직의 모든 게스트 사용자를 포함 하는 그룹을 만듭니다. 그런 다음이 그룹을 기본 제공 Id 보호 사용자 위험 및 로그인 위험 정책에 대 한 제외로 추가 하 고 로그인 위험을 조건으로 사용 하는 조건부 액세스 정책을 추가 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](../external-identities/what-is-b2b.md)