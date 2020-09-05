---
title: Azure AD에서 로그 분석 마법사 구성 | Microsoft Docs
description: 로그 분석을 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93caf52d8b4a11f9843ad5f18ebf968d1d0730cd
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226210"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>자습서: 로그 분석 마법사 구성


이 자습서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 감사 및 로그인 로그에 대한 로그 분석 작업 영역 구성
> * KQL(Kusto 쿼리 언어)을 사용하여 쿼리 실행
> * 특정 계정이 사용될 때 경고를 보내는 경고 규칙 만들기
> * 빠른 시작 템플릿을 사용하여 사용자 지정 통합 문서 만들기
> * 기존 통합 문서 템플릿에 쿼리 추가

## <a name="prerequisites"></a>필수 구성 요소

- P1 라이선스 관리자가 한 명 이상 있는 Azure 구독. Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.

- Azure AD 테넌트.

- Azure AD 테넌트의 글로벌 관리자 또는 보안 관리자인 사용자.


다음 문서를 숙지하세요.

- [자습서: Azure 리소스에서 리소스 로그 수집 및 분석](../../azure-monitor/learn/tutorial-resource-logs.md)

- [활동 로그를 Log Analytics와 통합하는 방법](./howto-integrate-activity-logs-with-log-analytics.md)

- [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)

- [KQL 빠른 참조](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor 통합 문서](../../azure-monitor/platform/workbooks-overview.md)



## <a name="configure-a-workspace"></a>작업 영역 구성 

이 절차에서는 감사 및 로그인 로그에 대한 로그 분석 작업 영역을 구성하는 방법을 간략하게 설명합니다.
로그 분석 작업 영역 구성은 다음과 같은 두 가지 주요 단계로 이루어집니다.
 
1. 로그 분석 작업 영역 만들기
2. 진단 설정 지정

**작업 영역을 구성하는 방법:** 


1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **로그 분석 작업 영역**을 검색합니다.

    ![리소스 서비스 및 문서 검색](./media/tutorial-log-analytics-wizard/search-services.png)

3. 로그 분석 작업 영역 페이지에서 **추가**를 클릭합니다.

    ![추가](./media/tutorial-log-analytics-wizard/add.png)

4.  **Log Analytics 작업 영역 만들기** 페이지에서 다음 단계를 수행합니다.

    ![Log Analytics 작업 영역 만들기](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. 구독을 선택합니다.

    2. 리소스 그룹을 선택합니다.
 
    3. **이름** 텍스트 상자에 이름을 입력합니다(예: MytestWorkspace1).

    4. 지역을 선택합니다.

5. **리뷰 + 만들기**를 클릭합니다.

    ![검토 후 만들기](./media/tutorial-log-analytics-wizard/review-create.png)

6. **만들기**를 클릭하고 배포가 성공할 때까지 기다립니다. 새 작업 영역을 보려면 페이지를 새로 고쳐야 할 수도 있습니다.

    ![생성](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. **Azure Active Directory** 검색

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. **모니터링** 섹션에서 **진단 설정**을 클릭합니다.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. **진단 설정** 페이지에서 **진단 설정 추가**를 클릭합니다.

    ![진단 설정 추가](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. **진단 설정** 페이지에서 다음 단계를 수행합니다.

    ![진단 설정 선택](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. **범주 세부 정보**에서 **AuditLogs** 및 **SigninLogs**를 선택합니다.

    2. **대상 세부 정보**에서 **Log Analytics에 보내기**를 선택한 다음, 새 로그 분석 작업 영역을 선택합니다. 
   
    3. **저장**을 클릭합니다. 

## <a name="run-queries"></a>쿼리 실행  

이 절차에서는 **KQL(Kusto 쿼리 언어)** 을 사용하여 쿼리를 실행하는 방법을 보여줍니다.


**쿼리를 실행하는 방법:**


1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 검색

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **모니터링** 섹션에서 **로그**를 클릭합니다.

4. **로그** 페이지에서 **시작**을 클릭합니다.

5. **검색* 텍스트 상자에 쿼리를 입력합니다.

6. **실행**을 클릭합니다.  


### <a name="kql-query-examples"></a>KQL 쿼리 예제

입력 데이터에서 임의의 항목 10개 가져오기:

`SigninLogs | take 10`

조건부 액세스가 성공했던 로그인 살펴보기

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


성공한 횟수 계산

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


일별 사용자당 성공한 로그인 횟수 집계:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


다음과 같이 사용자가 특정 기간에 특정 작업을 수행하는 횟수를 확인합니다.

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


작업 이름에 대한 결과 피벗

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


내부 조인을 사용하여 감사 및 로그인 로그 병합:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


클라이언트 앱 유형별 로그인 횟수 보기:

`SigninLogs | summarize count() by ClientAppUsed`

일별 로그인 횟수 계산:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

임의의 항목 5개를 가져오고, 결과에 표시하려는 열 프로젝션:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


상위 5개 결과를 내림차순으로 가져오고, 보고 싶은 열 프로젝션

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

값을 다른 두 열로 결합하여 새 열 만들기:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>경고 규칙 만들기  

이 절차에서는 breakglass 계정이 사용될 때 경고를 보내는 방법을 보여줍니다.

**경고 규칙을 만드는 방법:**


1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 검색

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **모니터링** 섹션에서 **로그**를 클릭합니다.

4. **로그** 페이지에서 **시작**을 클릭합니다.

5. ***검색** 텍스트 상자에 `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`을 입력합니다.

6. **실행**을 클릭합니다.  

7. 도구 모음에서 **새 경고 규칙**을 클릭합니다.

    ![새 경고 규칙](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. **경고 규칙 만들기** 페이지에서 범위가 올바른지 확인합니다.

9. **조건**에서 다음 항목을 클릭합니다. **평균 사용자 지정 로그 검색이 <logic undefined>개보다 클 때마다**

    ![기본 조건](./media/tutorial-log-analytics-wizard/default-condition.png)

10. **신호 논리 구성** 페이지의 **경고 논리** 섹션에서 다음 단계를 수행합니다.

    ![경고 논리](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. **기준**으로 **결과 수**를 선택합니다.

    2. **연산자**로 **보다 큼**을 선택합니다.

    3. **임계값**으로 **0**을 선택합니다. 

11. **신호 논리 구성** 페이지의 **평가 기준** 섹션에서 다음 단계를 수행합니다.

    ![평가 기준](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. **기간(분)** 으로 **5**를 선택합니다.

    2. **빈도(분)** 로 **5**를 선택합니다.

    3. **완료**를 클릭합니다. 

12. **작업 그룹**에서 **작업 그룹 선택**을 클릭합니다. 

    ![작업 그룹](./media/tutorial-log-analytics-wizard/action-group.png)

13. **이 경고 규칙에 연결할 작업 그룹 선택**에서 **작업 그룹 만들기**를 클릭합니다. 

    ![작업 그룹 만들기](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. **작업 그룹 만들기** 페이지에서 다음 단계를 수행합니다.

    ![인스턴스 세부 정보](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. **작업 그룹 이름** 텍스트 상자에 **내 작업 그룹**을 입력합니다.

    2. **표시 이름** 텍스트 상자에 **내 작업**을 입력합니다.

    3. **검토 + 만들기**를 클릭합니다. 

    4. **만들기**를 클릭합니다.


15. **작업 사용자 지정**에서 다음 단계를 수행합니다.

    ![작업 사용자 지정](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. **이메일 제목**을 선택합니다.

    2. **제목 줄** 텍스트 상자에 `Breakglass account has been used`를 입력합니다.

16. **경고 규칙 세부 정보**에서 다음 단계를 수행합니다.

    ![경고 규칙 세부 정보](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. **경고 규칙 이름** 텍스트 상자에 `Breakglass account`를 입력합니다.

    2. **설명** 텍스트 상자에 `Your emergency access account has been used`를 입력합니다.

17. **경고 규칙 만들기**를 클릭합니다.   


## <a name="create-a-custom-workbook"></a>사용자 지정 통합 문서 만들기

이 절차에서는 빠른 시작 템플릿을 사용하여 새 통합 문서를 만드는 방법을 보여줍니다.




1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 검색

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **모니터링** 섹션에서 **통합 문서**를 클릭합니다.

    ![통합 문서](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **빠른 시작** 섹션에서 **비어 있음**을 클릭합니다.

    ![빠른 시작](./media/tutorial-log-analytics-wizard/quick-start.png)

5. **추가**를 클릭합니다.

    ![통합 문서 추가](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. **텍스트 추가**를 클릭합니다.

    ![텍스트 추가](./media/tutorial-log-analytics-wizard/add-text.png)


7. 텍스트 상자에 `# Client apps used in the past week`를 입력한 다음, **편집 완료**를 클릭합니다.

    ![통합 문서 텍스트](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. 새 통합 문서에서 **추가**를 클릭한 다음, **쿼리 추가**를 클릭합니다.

    ![쿼리 추가](./media/tutorial-log-analytics-wizard/add-query.png)

9. 쿼리 텍스트 상자에 `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`를 입력합니다.

10. **쿼리 실행** 클릭

    ![쿼리 실행](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. 도구 모음의 **시각화**에서 **원형 차트**를 클릭합니다.

    ![원형 차트](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. **편집 완료**를 클릭합니다.

    ![편집 완료](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>통합 문서 템플릿에 쿼리 추가  

이 절차에서는 기존 통합 문서 템플릿에 쿼리를 추가하는 방법을 보여줍니다. 예제는 조건부 액세스 성공 실패 분포를 보여주는 쿼리를 기반으로 합니다.


1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Azure Active Directory** 검색

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. **모니터링** 섹션에서 **통합 문서**를 클릭합니다.

    ![통합 문서](./media/tutorial-log-analytics-wizard/workbooks.png)

4. **조건부 액세스** 섹션에서 **조건부 액세스 인사이트 및 보고**를 클릭합니다.

    ![조건부 액세스 템플릿](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. 도구 모음에서 **편집**을 클릭합니다.

    ![조건부 액세스 템플릿](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. 도구 모음에서 줄임표(...)를 클릭한 다음, **추가**를 클릭하고 **쿼리 추가**를 클릭합니다.

    ![통합 문서 쿼리 추가](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. 쿼리 텍스트 상자에 `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`를 입력합니다.

8. **쿼리 실행** 클릭

    ![쿼리 실행](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. **시간 범위**를 클릭한 다음, **쿼리에서 설정**을 선택합니다.

10. **시각화**를 클릭한 다음, **가로 막대형 차트**를 선택합니다. 

11. **고급 설정**을 클릭하고 차트 제목으로 `Conditional Access status over the last 20 days`를 입력한 다음, **편집**을 클릭합니다. 

    ![차트 제목 설정](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>다음 단계

Azure Portal을 사용하여 디바이스 ID를 관리하는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [Monitoring](overview-monitoring.md)