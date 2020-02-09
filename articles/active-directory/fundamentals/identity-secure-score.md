---
title: Identity 보안 점수는 무엇입니까? - Azure Active Directory
description: Identity 보안 점수를 사용 하 여 디렉터리의 보안 태세 개선 하는 방법을
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988710"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Azure Active Directory의 ID 보안 점수란?

Azure AD 테넌트는 얼마나 안전하나요? 이 질문에 대답 하는 방법, 모르는 경우이 문서는 identity 보안 점수를 모니터링 하 고 identity 보안 상태를 개선할 수 있습니다 하는 방법을 설명 합니다.

## <a name="what-is-an-identity-secure-score"></a>ID 보안 점수란?

Identity 보안 점수는 1에서 Microsoft의 모범 사례 권장 사항이 포함 된 보안을 어떻게 정렬 표시기로 작동 하는 223 사이의 숫자입니다. Identity 보안 점수에 각 개선 작업은 특정 구성에 맞게 조정 됩니다.  

![보안 점수](./media/identity-secure-score/identity-secure-score-overview.png)

점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

ID 보안 점수 대시보드에서 점수 및 관련 정보에 액세스할 수 있습니다. 이 대시보드에서 다음을 찾을 있습니다.

- Identity 보안 점수
- 동일한 업계와 비슷한 크기의 다른 테넌트 Id 점수를 보호 하는 방법을 보여 주는 비교 그래프 비교
- Identity 보안 점수 시간이 지남에 따라 어떻게 변경 되었는지 보여 주는 추세 그래프
- 가능한 향상 된 기능 목록

개선 작업을 따라 다음을 수행할 수 있습니다.

- 점수 및 보안 태세 개선
- Identity 투자의 일환으로 조직에 사용할 수 있는 기능을 활용

## <a name="how-do-i-get-my-secure-score"></a>내 보안 점수는 어떻게 가져오나요?

Identity 보안 점수는 Azure AD의 모든 버전에서 사용할 수 있습니다. 점수에 액세스하려면 [Azure AD 개요 대시보드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)로 이동합니다.

## <a name="how-does-it-work"></a>어떻게 작동합니까?

Azure에서는 48시간마다 사용자 보안 구성을 살펴보고 사용자 설정과 권장 모범 사례를 비교합니다. 이 평가의 결과 따라 점수를 새 디렉터리에 대해 계산 됩니다. 보안 구성 최선의 방법 지침과 똑같은 되지 않습니다 하 고 개선 작업을 일부만 맞을 가능성이 있습니다. 이러한 시나리오에서는 있습니다만 획득할 수 일부 컨트롤에 사용할 수 있는 최대 점수입니다.

각 권장 사항은 Azure AD 구성에 따라 측정됩니다. 모범 사례 권장 사항을 사용 하도록 설정 하려면 타사 제품을 사용 중인 경우 개선 작업의 설정에서이 구성을 지정할 수 있습니다. 사용자 환경에 적용 되지 경우 무시할 권장 사항을 설정 하는 옵션이 있습니다. 무시된 권장 사항은 점수 계산에 포함되지 않습니다.

![무시 하거나 타사에 의해 설명 된 대로 작업을 표시](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>보안 점수가 어떻게 도움이 되나요?

보안 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="who-can-use-the-identity-secure-score"></a>ID 보안 점수는 누가 사용할 수 있나요?

ID 보안 점수는 다음과 같은 역할에 의해 사용될 수 있습니다.

- 글로벌 관리자
- 보안 관리자
- 보안 읽기 권한자

### <a name="how-are-controls-scored"></a>컨트롤을 평가 하는 방법

컨트롤 두 가지 방법으로 점수를 매길 수 있습니다. 이진 방식에서에 따라 점수가 일부-권장 사항에 따라 구성 된 설정 또는 기능이 있는 경우 100% 점수를 얻게 됩니다. 다른 점수는 총 구성의 비율로 계산 됩니다. 예를 들어, 개선 권장 사항에서 MFA 사용 하 여 모든 사용자를 보호 및 보호 된 총 100 명의 5에만 있는 경우 30 개 요소를 받게 알 경우 하는 점수를 얻을 수는 부분 점수 약 2 (보호 된 5 / 총 100 * 30 최대 pts = 2 pts 부분 점수) .

### <a name="what-does-not-scored-mean"></a>[점수가 매겨지지 않음]은 어떤 의미인가요?

[점수가 매겨지지 않음]으로 레이블이 지정된 작업은 조직에서 수행할 수 있지만 (아직!) 도구에 연결되지 않아 점수가 매겨지지 않는 작업입니다. 따라서 여전히 보안을 개선할 수는 있지만 해당 작업에 대한 크레딧을 지금 당장 받을 수는 없습니다.

### <a name="how-often-is-my-score-updated"></a>내 점수가 업데이트되는 빈도는?

점수는 하루에 한 번(약 오전 1시 PST) 계산됩니다. 측정된 작업을 변경하는 경우 점수는 다음날 자동으로 업데이트됩니다. 변경 내용이 점수에 반영되는 데 최대 48시간이 걸립니다.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>내 점수가 변경됐습니다. 변경 이유를 어떻게 알 수 있나요?

로 이동 합니다 [Microsoft 365 보안 센터](https://security.microsoft.com/)전체 Microsoft 보안 점수를 찾을 수 있습니다. 변경 기록 탭에서 자세한 내용을 검토 하 여 보안 점수에 모든 변경 내용을 쉽게 볼 수 있습니다.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>보안 점수 가져오기 위반의 내 위험을 측정은?

간단히 말해 아닙니다. 보안 점수 절대 측정값을 명시적 되지는 않습니다 가능성의 위반 가져올 하려는 합니다. 보안 점수는 보안을 위반할 위험을 상쇄할 수 있는 기능의 채택 정도를 표시합니다. 서비스가 없습니다 수를 초과할 수는, 하 고 보안 점수는 어떤 방식으로든에서 보장 하는 것으로 해석 되지 않아야 보장할 수 있습니다.

### <a name="how-should-i-interpret-my-score"></a>내 점수는 어떻게 해석해야 하나요?

권장된 보안 기능을 구성하거나 보안 관련 작업(예: 보고서 읽기)을 수행하면 점수를 받습니다. 일부 작업은 사용자에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정하는 것처럼 부분 완료에 대해 점수가 매겨집니다. 보안 점수는 직접 사용 하는 Microsoft 보안 서비스의 특징입니다. 유용성을 사용 하 여 보안 분산 되어야 합니다는 기억 합니다. 모든 보안 제어에는 사용자 영향 구성 요소가 있습니다. 사용자 영향이 낮은 제어는 사용자의 일상적인 작업에 거의 영향을 미치지 않습니다.

점수 기록을 확인 하려면로 이동 합니다 [Microsoft 365 보안 센터](https://security.microsoft.com/) 및 전체 Microsoft 보안 점수를 검토 합니다. 전체에 변경 내용을 검토할 수 있습니다 보안 점수 기록 보기를 클릭 합니다. 특정 날짜를 선택하여 해당 날짜에 사용하도록 설정된 제어 및 각 제어에 대해 획득한 점수를 확인합니다.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>ID 보안 점수는 Office 365 보안 점수와 어떤 관계가 있나요?

합니다 [Microsoft 보안 점수](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) 5 포함 고유 컨트롤 및 점수 매기기 범주:

- ID
- Data
- 디바이스
- 인프라
- 앱

Identity 보안 점수는 Microsoft 보안 점수 identity 부분을 나타냅니다. 이 처럼 중복 id에 대 한 권장 사항을 보안 점수 microsoft에서 identity 점수 동일 있음을 의미 합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft 보안 점수에 대 한 자세한 내용은](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
