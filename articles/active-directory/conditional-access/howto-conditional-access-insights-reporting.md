---
title: 조건부 액세스 인사이트 및 보고 통합 문서 - Azure Active Directory
description: Azure AD 조건부 액세스 인사이트 및 보고 통합 문서를 사용하여 정책 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c2364eae0d04da8f8e6fe38ae80db7adb8666ce
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049420"
---
# <a name="conditional-access-insights-and-reporting"></a>조건부 액세스 인사이트 및 보고

조건부 액세스 인사이트 및 보고 통합 문서를 사용하면 조건부 액세스 정책이 시간에 따라 조직에 미치는 영향을 이해할 수 있습니다. 로그인 중에 하나 이상의 조건부 액세스 정책이 적용될 수 있으며, 특정 권한 부여 제어가 충족된 경우 액세스를 부여하고 충족되지 않은 경우 액세스를 거부할 수 있습니다. 각 로그인 중에 여러 조건부 액세스 정책이 평가될 수 있으므로 인사이트 및 보고 통합 문서를 사용하면 개별 정책 또는 모든 정책 중에서 하위 집합의 영향을 검사할 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

인사이트 및 보고 통합 문서를 사용하려면 테넌트에 로그인 로그 데이터를 유지할 Log Analytics 작업 영역이 있어야 합니다. 조건부 액세스를 사용하려면 사용자에게 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다.

다음 역할은 인사이트 및 보고에 액세스할 수 있습니다.  

- 조건부 액세스 관리자 
- 보안 판독기 
- 보안 관리자 
- 전역 독자 
- 전역 관리자 

사용자에게는 다음과 같은 Log Analytics 작업 영역 역할 중 하나도 필요합니다.  

- 참가자  
- 소유자 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Azure AD에서 Azure Monitor 로그로 로그인 로그 스트리밍 

Azure AD 로그를 Azure Monitor 로그에 통합하지 않은 경우 다음 단계를 수행해야 통합 문서가 로드됩니다.  

1. [Azure Monitor에서 Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md).
1. [Azure Monitor 로그에 Azure AD 로그 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>작동 방법 

인사이트 및 보고 통합 문서에 액세스하려면 다음을 수행합니다.  

1. **Azure Portal**에 로그인합니다.
1. **Azure Active Directory** > **보안** > **조건부 액세스** > **인사이트 및 보고**로 이동합니다.

### <a name="get-started-select-parameters"></a>시작: 매개 변수 선택 

인사이트 및 보고 대시보드를 사용하면 지정된 기간 동안 하나 이상의 조건부 액세스 정책이 미치는 영향을 볼 수 있습니다. 통합 문서 맨 위에 있는 각 매개 변수를 설정하여 시작합니다. 

![Azure Portal의 조건부 액세스 인사이트 및 보고 대시보드](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**조건부 액세스 정책**: 하나 이상의 조건부 액세스 정책을 선택하여 복합적인 영향을 확인합니다. 정책은 사용하도록 설정된 정책과 보고 전용 정책으로 구분됩니다. 기본적으로 사용하도록 설정된 정책이 모두 선택됩니다. 사용하도록 설정된 정책은 현재 테넌트에 적용된 되는 정책입니다.  

**시간 범위**: 4시간에서 최대 90일까지의 시간 범위를 선택합니다. Azure AD 로그를 Azure Monitor에 통합한 시점보다 더 전의 시간 범위를 선택할 경우 통합 시점 이후의 로그인만 표시됩니다.  

**사용자**: 기본적으로 대시보드에는 선택한 정책이 모든 사용자에게 미치는 영향을 보여 줍니다. 개별 사용자를 기준으로 필터링하려면 텍스트 필드에 사용자 이름을 입력합니다. 모든 사용자를 기준으로 필터링하려면 텍스트 필드에 “모든 사용자”를 입력하거나 매개 변수를 비워 둡니다. 

**앱**: 기본적으로 대시보드에는 선택한 정책이 모든 앱에 미치는 영향을 보여 줍니다. 개별 앱을 기준으로 필터링하려면 텍스트 필드에 앱 이름을 입력합니다. 모든 앱을 기준으로 필터링하려면 텍스트 필드에 “모든 앱”을 입력하거나 매개 변수를 비워 둡니다. 

**데이터 뷰**: 대시보드에 사용자 수 또는 로그인 수를 기준으로 결과를 표시할지 여부를 선택합니다. 개별 사용자에게는 지정된 시간 범위 동안 여러 결과를 갖는 여러 앱에 대한 수백 개의 로그인이 있을 수 있습니다. 데이터 뷰를 사용자로 선택할 경우 하나의 사용자가 성공 횟수와 실패 횟수에 모두 포함될 수 있습니다. 예를 들어 10명의 사용자가 있는 경우 지난 30일 동안 성공 결과가 있는 사용자가 8명이고 지난 30일 동안 실패 결과가 있는 사용자가 9명일 수 있습니다.

## <a name="impact-summary"></a>영향 요약 

매개 변수를 설정하면 영향 요약이 로드됩니다. 요약은 선택한 정책을 평가할 때 시간 범위 동안 “성공”, “실패”, “사용자 작업 필요” 또는 “적용되지 않음”이 발생한 사용자 또는 로그인 수를 보여 줍니다.  

![조건부 액세스 통합 문서의 영향 요약](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: 해당 기간 동안 선택한 정책 중 하나 이상이 평가된 사용자 또는 로그인 수입니다.

**성공**: 해당 기간 동안 선택한 정책의 복합적인 결과가 “성공” 또는 “보고 전용: 성공”인 사용자 또는 로그인 수입니다.

**실패**: 해당 기간 동안 선택한 정책 중 하나 이상의 결과가 “실패” 또는 “보고 전용: 실패”인 사용자 또는 로그인 수입니다.

**사용자 작업 필요**: 해당 기간 동안 선택한 정책의 복합적인 결과가 “보고 전용: 사용자 작업 필요”인 사용자 또는 로그인 수입니다. 사용자 작업은 보고 전용 조건부 액세스 정책에서 다단계 인증과 같은 대화형 권한 부여 제어가 요구되는 경우에 필요합니다. 대화형 권한 부여 제어는 보고 전용 정책에 의해 적용되지 않으므로 성패 여부를 확인할 수 없습니다.  

**적용되지 않음**: 해당 기간 동안 선택한 정책 중 하나도 적용되지 않은 사용자 또는 로그인 수입니다.

### <a name="understanding-the-impact"></a>영향의 이해 

![조건 및 상태별 통합 문서 분석](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

각 조건에 대해 사용자 또는 로그인의 분석을 볼 수 있습니다. 통합 문서 맨 위의 요약 타일을 선택하여 특정 결과(예: 성공 또는 실패)의 로그인을 필터링할 수 있습니다. 각 조건부 액세스 조건(디바이스 상태, 디바이스 플랫폼, 클라이언트 앱, 위치, 애플리케이션 로그인 위험)에 대해 로그인 분석을 볼 수 있습니다.  

## <a name="sign-in-details"></a>로그인 세부 정보 

![통합 문서 로그인 세부 정보](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

대시보드 아래쪽에서 로그인을 검색하여 특정 사용자의 로그인을 조사할 수도 있습니다. 왼쪽 쿼리에는 가장 빈도가 높은 사용자가 표시됩니다. 사용자를 선택하면 오른쪽 쿼리가 필터링됩니다.  

> [!NOTE]
> 로그인 로그를 다운로드 하는 경우 조건부 액세스 보고서 전용 결과 데이터를 포함 하려면 JSON 형식을 선택 합니다.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>보고서 전용 모드에서 조건부 액세스 정책 구성

보고서 전용 모드에서 조건부 액세스 정책을 구성 하려면:

1. 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. 기존 정책을 선택 하거나 새 정책을 만듭니다.
1. **정책 사용** 에서 **보고서 전용** 모드로 전환을 설정 합니다.
1. **저장**을 선택합니다.

> [!TIP]
> 기존 정책의 **정책 상태 사용** 을 **On** 에서 **보고서 전용** 으로 편집 하면 기존 정책 적용만 사용 하지 않도록 설정 됩니다. 

## <a name="troubleshooting"></a>문제 해결

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>권한 오류로 인해 쿼리가 실패 하는 이유는 무엇 인가요?

통합 문서에 액세스 하려면 적절 한 Azure AD 권한 뿐만 아니라 작업 영역 권한 Log Analytics 필요 합니다. 샘플 log analytics 쿼리를 실행 하 여 적절 한 작업 영역 권한이 있는지 테스트 하려면 다음을 수행 합니다.

1. **Azure Portal**에 로그인합니다.
1. **Azure Active Directory**  >  **로그**로 이동 합니다.
1. `SigninLogs`쿼리 상자에를 입력 하 고 **실행**을 선택 합니다.
1. 쿼리에서 결과가 반환 되지 않으면 작업 영역이 올바르게 구성 되지 않았을 수 있습니다. 

![실패 한 쿼리 문제 해결](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Azure AD 로그인 로그를 Log Analytics 작업 영역으로 스트리밍하는 방법에 대 한 자세한 내용은 [AZURE ad 로그를 Azure Monitor 로그와 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)문서를 참조 하세요.

### <a name="why-are-the-queries-in-the-workbook-failing"></a>통합 문서의 쿼리가 실패 하는 이유는 무엇 인가요?

잘못 된 작업이 나 여러 작업 영역이 통합 문서와 연결 된 경우에는 때때로 쿼리가 실패할 수 있다는 사실을 알게 됩니다. 이 문제를 해결 하려면 통합 문서 위쪽에서 **편집** 을 클릭 한 다음 설정 기어를 클릭 합니다. 통합 문서와 연결 되지 않은 작업 영역을 선택 하 고 제거 합니다. 각 통합 문서와 연결 된 작업 영역은 하나만 있어야 합니다.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>조건부 액세스 정책 매개 변수가 비어 있는 이유는 무엇 인가요?

정책 목록은 가장 최근의 로그인 이벤트에 대해 평가 된 정책을 확인 하 여 생성 됩니다. 테 넌 트에 최신 로그인이 없는 경우 통합 문서가 조건부 액세스 정책 목록을 로드 하는 데 몇 분 정도 기다려야 할 수 있습니다. 이 문제는 Log Analytics를 구성 하는 즉시 발생 하거나 테 넌 트에서 최근 로그인 작업이 없는 경우 더 오래 걸릴 수 있습니다.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>통합 문서를 로드하는 데 시간이 오래 걸리는 이유는 무엇인가요?  

선택한 시간 범위 및 테넌트의 크기에 따라 통합 문서가 다량의 로그인 이벤트를 평가하고 있을 수 있습니다. 대규모 테넌트의 경우 로그인 볼륨이 Log Analytics의 쿼리 용량을 초과할 수 있습니다. 시간 범위를 4시간으로 줄여 보고 통합 문서가 로드되는지 확인하세요.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>몇 분 동안 로드한 후에 통합 문서가 0개의 결과를 반환하는 이유는 무엇인가요? 

로그인 볼륨이 Log Analytics의 쿼리 용량을 초과하면 통합 문서가 0개의 결과를 반환합니다. 시간 범위를 4시간으로 줄여 보고 통합 문서가 로드되는지 확인하세요.  

### <a name="can-i-save-my-parameter-selections"></a>매개 변수 선택 사항을 저장할 수 있나요?  

**Azure Active Directory** > **통합 문서** > **조건부 액세스 인사이트 및 보고**로 이동하여 통합 문서 맨 위에 매개 변수 선택 사항을 저장할 수 있습니다. 여기에서 통합 문서를 편집하고 **내 보고서** 또는 **공유 보고서**에 매개 변수 선택 사항을 포함하여 작업 영역의 복사본을 저장할 수 있는 통합 문서 템플릿을 찾을 수 있습니다. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>추가 쿼리를 사용하여 통합 문서를 편집하고 사용자 지정할 수 있나요? 

**Azure Active Directory** > **통합 문서** > **조건부 액세스 인사이트 및 보고**로 이동하여 통합 문서를 편집하고 사용자 지정할 수 있습니다. 여기에서 통합 문서를 편집하고 **내 보고서** 또는 **공유 보고서**에 매개 변수 선택 사항을 포함하여 작업 영역의 복사본을 저장할 수 있는 통합 문서 템플릿을 찾을 수 있습니다. 쿼리 편집을 시작하려면 통합 문서의 맨 위에서 **편집**을 클릭하세요.  
 
## <a name="next-steps"></a>다음 단계

- [조건부 액세스 보고 전용 모드](concept-conditional-access-report-only.md)

- Azure AD 통합 문서에 대 한 자세한 내용은 [Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)문서를 참조 하세요.

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
