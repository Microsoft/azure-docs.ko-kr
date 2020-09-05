---
title: Azure AD ID 보호 사용자 환경
description: Azure AD ID 보호의 사용자 환경
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
ms.openlocfilehash: 7d3315a8d6bca9406461d0082ed85bbefc9459f4
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950207"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD ID 보호 사용자 환경

Azure Active Directory ID 보호를 사용하여 다음을 수행할 수 있습니다.

* 사용자가 MFA (Azure Multi-Factor Authentication에 등록 해야 함)
* 위험한 로그인 및 손상 된 사용자의 재구성 자동화

모든 Id 보호 정책은 사용자의 로그인 환경에 영향을 줍니다. 사용자가 Azure MFA 및 셀프 서비스 암호 재설정과 같은 도구를 등록 하 고 사용할 수 있도록 하면 영향을 줄일 수 있습니다. 이러한 도구는 적절 한 정책 선택과 함께 사용자에 게 필요할 때 자체 수정 옵션을 제공 합니다.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 등록

Multi-factor authentication 등록을 요구 하 고 모든 사용자를 대상으로 하는 Id 보호 정책을 사용 하도록 설정 하면 나중에 Azure MFA를 사용 하 여 자동으로 수정할 수 있습니다. 이 정책을 구성 하면 사용자에 게 등록을 선택할 수 있는 14 일 기간을 제공 하 고 끝에는 등록이 강제로 적용 됩니다. 사용자 환경에 대 한 개요는 아래에 설명 되어 있습니다. 자세한 내용은 [2 단계 인증 및 회사 또는 학교 계정에 대 한 개요](../user-help/multi-factor-authentication-end-user-first-time.md)문서의 최종 사용자 설명서에서 찾을 수 있습니다.

### <a name="registration-interrupt"></a>등록 인터럽트

1. Azure AD 통합 응용 프로그램에 로그인 할 때 사용자는 multi-factor authentication에 대 한 계정을 설정 하기 위한 요구 사항에 대 한 알림을 가져옵니다. 이 정책은 새로운 장치를 사용 하는 새로운 사용자를 위한 Windows 10 기본 환경 에서도 트리거됩니다.
   
    ![추가 정보 필요](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 단계별 단계를 완료 하 여 Azure Multi-Factor Authentication에 등록 하 고 로그인을 완료 합니다.

## <a name="risky-sign-in-remediation"></a>위험한 로그인 수정

관리자가 로그인 위험에 대 한 정책을 구성 하면 영향을 받는 사용자가 로그인을 시도 하 고 정책 위험 수준을 트리거할 때 알림이 표시 됩니다. 

### <a name="risky-sign-in-self-remediation"></a>위험한 로그인 자체 수정

1. 사용자는 새 위치, 디바이스 또는 앱에서 로그인과 같은 해당 로그인에 대해 비정상적인 점이 검색되었음을 통보받습니다.
   
    ![비정상적인 프롬프트](./media/concept-identity-protection-user-experience/120.png)

1. 사용자는 이전에 등록 된 메서드 중 하나를 사용 하 여 Azure MFA를 완료 하 여 id를 증명 해야 합니다. 

### <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자의 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단 하도록 선택할 수 있습니다. 차단을 해제 하려면 최종 사용자가 IT 직원에 게 문의 하거나 익숙한 위치 또는 장치에서 로그인을 시도할 수 있습니다. 이 경우에는 multi-factor authentication을 수행 하 여 자체 수정이 불가능 합니다.

![로그인 위험 정책에 의해 차단 됨](./media/concept-identity-protection-user-experience/200.png)

IT 담당자는 사용자가 다시 로그인 할 수 있도록 [차단](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) 해제 섹션의 지침을 따를 수 있습니다.

## <a name="risky-user-remediation"></a>위험한 사용자 수정

사용자 위험 수준 정책을 구성한 경우 사용자 위험 수준 손상 가능성을 충족 하는 사용자가 로그인 하기 전에 사용자 손상 복구 흐름을 통과 해야 합니다. 

### <a name="risky-user-self-remediation"></a>위험한 사용자 자동 수정

1. 사용자는 해당 계정 보안이 의심스러운 작업 또는 유출된 자격 증명으로 인해 위험에 노출되었다는 알림을 받습니다.
   
    ![수정](./media/concept-identity-protection-user-experience/101.png)

1. 사용자는 이전에 등록 된 메서드 중 하나를 사용 하 여 Azure MFA를 완료 하 여 id를 증명 해야 합니다. 
1. 마지막으로 사용자는 자신의 계정에 대 한 액세스 권한이 있을 수 있으므로 셀프 서비스 암호 재설정을 사용 하 여 자신의 암호를 변경 해야 합니다.

## <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자의 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단 하도록 선택할 수 있습니다. 차단을 해제 하려면 최종 사용자가 IT 직원에 게 문의 해야 합니다. 이 경우 multi-factor authentication 및 셀프 서비스 암호 재설정을 수행 하 여 자동으로 수정할 수 없습니다.

![사용자 위험 정책에 의해 차단 됨](./media/concept-identity-protection-user-experience/104.png)

IT 담당자는 사용자가 다시 로그인 할 수 있도록 [차단](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) 해제 섹션의 지침을 따를 수 있습니다.

## <a name="see-also"></a>참조

- [위험 재구성 및 사용자 차단 해제](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory ID 보호](./overview-identity-protection.md)