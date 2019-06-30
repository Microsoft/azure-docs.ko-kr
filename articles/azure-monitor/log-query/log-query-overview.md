---
title: Azure Monitor에서 쿼리 로그 개요 | Microsoft Docs
description: 일반적인 질문에 답변할와 관련 된 쿼리를 기록 및 사용 하기 시작 합니다.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310333"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor에서 로그 쿼리 개요
로그 쿼리를 사용 하면 수집 된 데이터의 값을 완벽 하 게 활용 하 여 도움이 [Azure Monitor 로그](../platform/data-platform-logs.md)합니다. 강력한 쿼리 언어를 사용 하면, 여러 테이블의 데이터를 조인 하 고, 대량의 데이터를 집계 하 고, 최소한의 코드를 사용 하 여 복잡 한 작업을 수행할 수 있습니다. 거의 모든 질문에 대답할 수 있습니다 및 분석을 수행할 지원 데이터를 수집 하 고 올바른 쿼리를 작성 하는 방법을 이해 합니다.

와 같은 Azure Monitor의 몇 가지 기능 [insights](../insights/insights-overview.md) 하 고 [솔루션](../insights/solutions-inventory.md) 기본 쿼리를 노출 하지 않고 로그 데이터를 처리 합니다. Azure Monitor의 다른 기능을 완전히 활용 하려면 쿼리를 생성 하는 방법 및 대화형으로 Azure Monitor 로그에서 데이터를 분석 하 고를 사용 하는 방법을 이해 해야 합니다.

이 문서에서는 Azure Monitor에서 로그 쿼리 하는 방법에 대 한 학습을 시작 점으로 사용 합니다. 일반적인 질문에 답변 하 고 세부 정보 및 단원을 추가로 제공 하는 다른 설명서 링크를 제공 합니다.

## <a name="how-can-i-learn-how-to-write-queries"></a>쿼리를 작성 하는 방법을 어떻게 알 수 있습니까?
오른쪽 항목으로 이동 하려는 경우에 다음 자습서를 사용 하 여 시작할 수 있습니다.

- [Azure Monitor에서 Log Analytics를 사용 하 여 시작](get-started-portal.md)합니다.
- [Azure Monitor에서 로그 쿼리를 사용 하 여 시작](get-started-queries.md)합니다.

기본 사항을 아래로 만든 후 사용자 고유의 데이터 또는 데모 환경에서 데이터를 사용 하 여 여러 단원을 통해 안내 합니다. 

- [Azure Monitor 로그 쿼리 문자열을 사용 하 여 작동 합니다.](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>어떤 언어 수행할 쿼리에 사용 하 여 기록 하 시겠습니까?
Azure Monitor 로그 기반 [Azure Data Explorer](/azure/data-explorer), 로그 쿼리는 동일한 Kusto 쿼리 언어 (KQL)를 사용 하 여 작성 됩니다. 쉽게 읽을 수 있도록 설계 된 다양 한 언어 이며 저자인 하는 최소한의 지침을 사용 하 여 사용할 수 있게 해야 합니다.

참조 [Azure 데이터 탐색기 KQL 설명서](/azure/kusto/query) KQL 및 사용할 수 있는 다른 함수에 대 한 참조에 대 한 전체 설명서.<br>
참조 [Azure Monitor에서 로그 쿼리를 사용 하 여 시작](get-started-queries.md) 간단한 연습입니다. Azure Monitor 로그에서 데이터를 사용 하 여 언어에 대 한 합니다.
참조 [Azure Monitor 로그 쿼리 언어상 차이점](data-explorer-difference.md) KQL Azure Monitor에서 사용 되는 버전에서 약간의 차이점에 대 한 합니다.

## <a name="what-data-is-available-to-log-queries"></a>데이터 쿼리를 기록할 수 있는 기능
Azure Monitor 로그에서 수집 된 모든 데이터를 검색 하 고 로그 쿼리에서 분석 하는 작업을 수 있습니다. 다른 데이터 원본에서 다른 테이블에 해당 데이터를 기록 하지만 여러 원본에서 데이터를 분석 하는 단일 쿼리에서 여러 테이블을 포함할 수 있습니다. 쿼리를 작성 하는 경우 적어도 기본적으로 Azure Monitor 로그에서 데이터를 구조화 하는 방법을 이해 해야 하므로 어느 테이블에, 원하는 데이터를 확인 하 여 시작 합니다.

참조 [Azure Monitor 로그 원본](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)는 목록이 서로 다른 데이터 원본에 대 한 Azure Monitor 로그를 채웁니다.<br>
참조 [Azure Monitor 로그 구조](logs-structure.md) 데이터가 구조화 되는 방법에 대 한 설명은 합니다.

## <a name="what-does-a-log-query-look-like"></a>로그 쿼리의 같습니다
쿼리는 해당 테이블에서 모든 레코드를 검색 하는 것에 대 한 단일 테이블 이름 처럼 간단할 수 있습니다.

```Kusto
Syslog
```

또는 특정 레코드를 필터링, 요약,을 차트의 결과 시각화할 수 것:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

더 복잡 한 분석을 위해 조인을 사용 하 여 결과 함께 분석 하는 여러 테이블에서 데이터를 검색할 수 있습니다.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
KQL 잘 모르는 경우에 적어도 이러한 쿼리에서 사용 되는 기본 논리를 알아낼 수 해야 합니다. 이러한 테이블의 이름으로 시작 하 고 필터링 하 고 해당 데이터를 처리 하는 여러 명령을 추가 합니다. 쿼리 명령의 숫자를 사용할 수 및 사용할 수 있는 다른 KQL 명령에 잘 알고 있듯이 더 복잡 한 쿼리를 작성할 수 있습니다.

참조 [Azure Monitor에서 로그 쿼리를 사용 하 여 시작](get-started-queries.md) 로그 쿼리 언어 및 일반 함수를 소개 하는 자습서입니다.<br>


## <a name="what-is-log-analytics"></a>Log Analytics란?
Log Analytics는 로그 쿼리를 작성 하 고 그 결과 대화형으로 분석에 대 한 Azure portal에서 기본 도구입니다. Azure Monitor에서 로그 쿼리를 다른 곳에서 사용 된 경우에에서는 일반적으로 쓰기 및 쿼리를 테스트 먼저 Log Analytics를 사용 합니다.

Azure portal의 여러 위치에서 Log Analytics를 시작할 수 있습니다. Log Analytics에 사용 가능한 데이터의 범위에서 시작 하는 방법이 결정 됩니다. 참조 [쿼리 범위](scope.md) 대 한 자세한 내용은 합니다.

- 선택 **로그** 에서 합니다 **Azure Monitor** 메뉴 또는 **Log Analytics 작업 영역** 메뉴.
- 선택 **Analytics** 에서 합니다 **개요** Application Insights 응용 프로그램의 페이지입니다.
- 선택 **로그** Azure 리소스의 메뉴에서.

![Log Analytics](media/log-query-overview/log-analytics.png)

참조 [Azure Monitor에서 Log Analytics를 사용 하 여 시작](get-started-portal.md) 다양 한 기능을 소개 하는 Log Analytics의 자습서를 연습 합니다.

## <a name="where-else-are-log-queries-used"></a>로그 쿼리는 다른 위치에 사용 하나요?
로그 쿼리 및 Log Analytics에서 해당 결과 사용 하 여 대화형으로 작동 하는 것 외에도 영역 쿼리는 사용할 Azure Monitor에는 다음과 같습니다.

- **경고 규칙.** [경고 규칙](../platform/alerts-overview.md)은 작업 영역에서 데이터의 문제를 사전에 식별합니다.  각 경고 규칙은 일정한 간격으로 자동으로 실행되는 로그 검색을 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- **대시보드.** 쿼리 결과를 [Azure 대시보드](../learn/tutorial-logs-dashboards.md)에 고정하여 로그 및 메트릭 데이터를 함께 시각화하고 다른 Azure 사용자와 선택적으로 공유할 수 있습니다.
- **뷰.**  [뷰 디자이너](../platform/view-designer.md)를 통해 사용자 대시보드에 포함될 데이터의 시각화를 만들 수 있습니다.  로그 쿼리는 각 뷰의 [타일](../platform/view-designer-tiles.md) 및 [시각화 파트](../platform/view-designer-parts.md)에서 사용되는 데이터를 제공합니다.  
- **내보내기.**  Azure Monitor에서 Excel 또는 [Power BI](../platform/powerbi.md)로 데이터를 가져오는 경우 로그 쿼리를 만들어 내보낼 데이터를 정의합니다.
- **PowerShell.** 명령줄 또는 사용 하는 Azure Automation runbook에서 PowerShell 스크립트를 실행할 수 있습니다 [Get AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) Azure Monitor에서 로그 데이터를 검색 합니다.  이 cmdlet에는 검색할 데이터를 결정하는 쿼리가 필요합니다.
- **Azure Monitor Logs API.**  REST API 클라이언트는 [Azure Monitor Logs API](../platform/alerts-overview.md)를 통해 작업 영역에서 로그 데이터를 검색할 수 있습니다.  API 요청에는 검색할 데이터를 확인하기 위해 Azure Monitor에 대해 실행되는 쿼리가 포함됩니다.


## <a name="next-steps"></a>다음 단계
- 안내를 [Azure portal에서 Log Analytics를 사용 하 여 자습서](get-started-portal.md)합니다.
- 안내를 [쿼리 작성에 대 한 자습서](get-started-queries.md)합니다.
