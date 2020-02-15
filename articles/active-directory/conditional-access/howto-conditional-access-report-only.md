---
title: 보고서 전용 모드에서 조건부 액세스 정책 구성-Azure Active Directory
description: 조건부 액세스에서 보고서 전용 모드를 사용 하 여 도입 지원
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
ms.openlocfilehash: d63aaa0103715a928cdd5332de738a473b329f2e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186080"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>보고서 전용 모드에서 조건부 액세스 정책 구성 (미리 보기)

보고서 전용 모드에서 조건부 액세스 정책을 구성 하려면:

1. 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 필요에 따라 정책 조건을 구성 하 고 필요한 권한 부여 컨트롤을 구성 합니다.
1. **정책 사용** 에서 **보고서 전용** 모드로 전환을 설정 합니다.
1. **저장**을 선택합니다.

> [!TIP]
> 기존 정책의 정책 상태 **사용** 을 **On** 에서 **보고서 전용** 으로 편집할 수 있지만 이렇게 하면 정책 적용이 사용 되지 않습니다. 

Azure AD 로그인 로그에서 보고서 전용 결과를 봅니다.

특정 로그인에 대 한 보고서 전용 정책의 결과를 보려면 다음을 수행 합니다.

1. 보고서 판독기, 보안 읽기 권한자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **로그인**으로 이동 합니다.
1. 로그인을 선택 하거나 필터를 추가 하 여 결과를 좁힙니다.
1. **세부 정보** 서랍에서 **보고서 전용 (미리 보기)** 탭을 선택 하 여 로그인 중에 평가 된 정책을 확인 합니다.

> [!NOTE]
> 로그인 로그를 다운로드 하는 경우 조건부 액세스 보고서 전용 결과 데이터를 포함 하려면 JSON 형식을 선택 합니다.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure AD와 Azure Monitor 통합 설정

새 조건부 액세스 Insights 통합 문서를 사용 하 여 조건부 액세스 정책의 집계 영향을 확인 하려면 Azure Monitor를 Azure AD와 통합 하 고 로그인 로그를 내보내야 합니다. 이 통합을 설정 하는 두 가지 단계가 있습니다. 

1. [Azure Monitor 구독에 등록 하 고 작업 영역을 만듭니다](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).
1. [AZURE AD에서 Azure Monitor 로그인 로그를 내보냅니다](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

가격 책정에 대 한 자세한 내용은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에서 찾을 수 Azure Monitor. 비용을 예측 하 고, 일일 한도를 설정 하거나, 데이터 보존 기간을 사용자 지정 하는 리소스는 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)문서에서 찾을 수 있습니다.

## <a name="view-conditional-access-insights-workbook"></a>조건부 액세스 Insights 통합 문서 보기

Azure AD 로그를 Azure Monitor와 통합 하면 새 조건부 액세스 insights 통합 문서를 사용 하 여 조건부 액세스 정책의 영향을 모니터링할 수 있습니다.

1. 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **통합 문서**를 찾습니다.
1. **조건부 액세스 정보 (미리 보기)** 를 선택 합니다.
1. **조건부 액세스 정책** 드롭다운에서 하나 이상의 정책을 선택 합니다. 기본적으로 사용 하도록 설정 된 모든 정책이 선택 됩니다.
1. 시간 범위를 선택 합니다. 시간 범위가 사용 가능한 데이터 집합을 초과 하면 보고서에 사용 가능한 데이터가 모두 표시 됩니다. **조건부 액세스 정책** 및 **시간 범위** 매개 변수를 설정 하면 보고서가 로드 됩니다.
   1. 필요에 따라 개별 **사용자** 또는 **앱** 을 검색 하 여 보고서 범위를 좁힐 수 있습니다.
1. 시간 범위에서 데이터를 볼 때 사용자 수 또는 로그인 수를 선택 합니다.
1. **데이터 보기**에 따라 **영향 요약** 에는 선택한 매개 변수의 범위에 있는 사용자 또는 로그인 수가 총 수, **성공**, **실패**, **사용자 작업 필요**및 **적용 안**함 별로 그룹화 되어 표시 됩니다. 타일을 선택 하 여 특정 결과 유형의 로그인을 검사 합니다. 
   1. 통합 문서 매개 변수를 변경한 경우 나중에 사용 하기 위해 복사본을 저장 하도록 선택할 수 있습니다. 보고서 맨 위에 있는 저장 아이콘을 선택 하 고 저장할 이름 및 위치를 제공 합니다.
1. 아래로 스크롤하여 각 조건에 대 한 로그인의 분석 결과를 확인 합니다.
1. 위의 선택 항목으로 필터링 된 개별 로그인 이벤트를 조사 하려면 보고서 아래쪽의 **로그인 세부 정보** 를 확인 합니다.

> [!TIP] 
> 특정 쿼리를 드릴 다운 하거나 로그인 세부 정보를 내보내야 하나요? 쿼리 오른쪽의 단추를 선택 하 여 Log Analytics에서 쿼리를 엽니다. 내보내기를 선택 하 여 CSV 또는 Power BI로 내보냅니다.

## <a name="common-problems-and-solutions"></a>일반적인 문제 및 해결 방법

### <a name="why-are-the-queries-in-the-workbook-failing"></a>통합 문서의 쿼리가 실패 하는 이유는 무엇 인가요?

잘못 된 작업이 나 여러 작업 영역이 통합 문서와 연결 된 경우에는 때때로 쿼리가 실패할 수 있다는 사실을 알게 됩니다. 이 문제를 해결 하려면 통합 문서 위쪽에서 **편집** 을 클릭 한 다음 설정 기어를 클릭 합니다. 통합 문서와 연결 되지 않은 작업 영역을 선택 하 고 제거 합니다. 각 통합 문서와 연결 된 작업 영역은 하나만 있어야 합니다.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>조건부 액세스 정책 드롭다운 매개 변수가 내 정책을 포함 하지 않는 이유는 무엇 인가요?

조건부 액세스 정책 드롭다운은 4 시간 동안 가장 최근의 로그인을 쿼리 하 여 채워집니다. 이전 4 시간 동안 테 넌 트에 로그인이 없는 경우 드롭다운이 비어 있을 수 있습니다. 자주 로그인 하지 않는 작은 테 넌 트의 경우와 같이이 지연이 지속적으로 발생 하는 경우 관리자는 조건부 액세스 정책 드롭다운에서 쿼리를 편집 하 고 쿼리 시간을 4 시간 보다 오래 된 시간으로 확장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

Azure AD 통합 문서에 대 한 자세한 내용은 [Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)문서를 참조 하세요.
