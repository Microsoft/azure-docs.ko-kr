---
title: 조건부 액세스 보고서 전용 모드 - Azure Active Directory
description: 조건부 액세스 정책 배포를 사용 하 여 보고서 전용 모드를 어떻게 지원 하나요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75880f4c533a503852d62ff940e53d4bcc30d218
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186112"
---
# <a name="what-is-conditional-access-report-only-mode"></a>조건부 액세스 보고서 전용 모드

조건부 액세스는 적절 한 상황에서 올바른 액세스 제어를 적용 하 여 보안을 유지 하기 위해 고객에 게 널리 사용 되 고 있습니다. 그러나 조직에서 조건부 액세스 정책을 배포 하는 문제 중 하나는 최종 사용자에 게 미치는 영향을 확인 하는 것입니다. 레거시 인증 차단, 사용자 인구에 대 한 다단계 인증 요구, 로그인 위험 정책 구현 등과 같은 일반적인 배포 이니셔티브의 영향을 받는 사용자의 수와 이름을 예상 하기가 어려울 수 있습니다. 

보고서 전용 모드는 관리자가 환경에서 사용 하도록 설정 하기 전에 조건부 액세스 정책의 영향을 평가할 수 있도록 하는 새로운 조건부 액세스 정책 상태입니다.  보고서 전용 모드의 릴리스 사용:

- 조건부 액세스 정책은 보고서 전용 모드에서 사용 하도록 설정할 수 있습니다.
- 로그인 하는 동안 보고서 전용 모드의 정책은 평가 되지만 적용 되지 않습니다.
- 결과는 로그인 로그 정보에 대 한 **조건부 액세스** 및 **보고서 전용 (미리 보기)** 탭에 기록 됩니다.
- Azure Monitor 구독이 있는 고객은 조건부 액세스 insights 통합 문서를 사용 하 여 조건부 액세스 정책의 영향을 모니터링할 수 있습니다.

> [!WARNING]
> 규격 장치를 필요로 하는 보고서 전용 모드의 정책은 장치 호환성이 적용 되지 않는 경우에도 사용자에 게 Mac, iOS 및 Android에서 장치 인증서를 선택 하 라는 메시지를 표시할 수 있습니다. 이러한 메시지는 장치를 준수 하도록 설정할 때까지 반복 될 수 있습니다. 최종 사용자가 로그인 하는 동안 프롬프트를 받지 못하도록 하려면 장치 준수 검사를 수행 하는 보고서 전용 정책에서 장치 플랫폼 Mac, iOS 및 Android를 제외 합니다.

![Azure AD 로그인 로그의 보고서 전용 탭](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>정책 결과

지정 된 로그인에 대해 보고서 전용 모드의 정책을 평가 하는 경우 다음과 같은 네 가지 새로운 결과 값이 있습니다.

| 결과 | Description |
| --- | --- |
| 보고서 전용: 성공 | 구성 된 모든 정책 조건, 필요한 비 대화형 권한 부여 컨트롤 및 세션 컨트롤이 충족 되었습니다. 예를 들어 토큰에 이미 있는 MFA 클레임이 multi-factor authentication 요구 사항을 충족 하거나 규격 장치에서 장치 검사를 수행 하 여 준수 장치 정책을 충족 합니다. |
| 보고서 전용: 오류 | 구성 된 모든 정책 조건이 충족 되었지만 모든 필수 비 대화형 권한 부여 컨트롤 또는 세션 컨트롤이 충족 되지 않았습니다. 예를 들어, 블록 컨트롤이 구성 된 사용자에 게 정책이 적용 되거나 장치가 규격 장치 정책에 실패 하는 경우를 예로 들 수 있습니다. |
| 보고서 전용: 사용자 작업이 필요 합니다. | 구성 된 모든 정책 조건이 충족 되었지만 필요한 권한 부여 컨트롤 또는 세션 컨트롤을 충족 하려면 사용자 작업이 필요 합니다. 보고서 전용 모드를 사용 하는 경우 사용자에 게 필요한 컨트롤을 충족 하 라는 메시지가 표시 되지 않습니다. 예를 들어 사용자에 게 multi-factor authentication 챌린지 나 사용 약관에 대 한 메시지가 표시 되지 않습니다.   |
| 보고서 전용: 적용 되지 않음 | 구성 된 일부 정책 조건이 충족 되지 않았습니다. 예를 들어 사용자가 정책에서 제외 되거나 정책은 특정 한 트러스트 된 명명 된 위치에만 적용 됩니다. |

## <a name="conditional-access-insights-workbook"></a>조건부 액세스 Insights 통합 문서

관리자는 보고서 전용 모드에서 여러 정책을 만들 수 있으므로 각 정책의 개별 영향 및 여러 정책의 결합 된 영향을 모두 이해 해야 합니다. 새 조건부 액세스 정보 통합 문서를 통해 관리자는 조건부 액세스 쿼리를 시각화 하 고 지정 된 시간 범위, 응용 프로그램 집합 및 사용자에 대 한 정책의 영향을 모니터링할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계

[조건부 액세스 정책에 대 한 보고서 전용 모드 구성](howto-conditional-access-report-only.md)
