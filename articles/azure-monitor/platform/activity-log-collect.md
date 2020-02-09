---
title: Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석 | Microsoft Docs
description: Azure Monitor 로그에서 Azure 활동 로그를 수집 하 고 모니터링 솔루션을 사용 하 여 모든 Azure 구독에서 Azure 활동 로그를 분석 하 고 검색 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 2bf2f012e553e08a1eb829f93d9af0f0e74f638b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977650"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 합니다.

> [!WARNING]
> 이제 리소스 로그를 수집 하는 방법과 유사한 진단 설정을 사용 하 여 활동 로그를 Log Analytics 작업 영역으로 수집할 수 있습니다. [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](diagnostic-settings-legacy.md)을 참조 하세요.

Azure [활동 로그](platform-logs-overview.md) 는 azure 구독에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 합니다. 이 문서에서는 Log Analytics 작업 영역에 활동 로그를 수집 하는 방법과이 데이터를 분석 하기 위한 로그 쿼리 및 보기를 제공 하는 활동 로그 분석 [모니터링 솔루션](../insights/solutions.md)을 사용 하는 방법을 설명 합니다. 

활동 로그를 Log Analytics 작업 영역에 연결 하면 다음과 같은 이점이 있습니다.

- 분석을 위해 여러 Azure 구독에서 하나의 위치로 활동 로그를 통합 합니다.
- 활동 로그 항목을 90 일 보다 오래 저장 합니다.
- 활동 로그 데이터를 Azure Monitor에 의해 수집 된 다른 모니터링 데이터와 상호 연결 합니다.
- [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 복잡 한 분석을 수행 하 고 활동 로그 항목에 대 한 심층 통찰력을 얻습니다.

## <a name="connect-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 연결
단일 작업 영역을 동일한 Azure 테넌트의 여러 구독에 대 한 활동 로그에 연결할 수 있습니다. 여러 테넌트에 걸친 컬렉션의 경우 [다른 Azure Active Directory 테넌트의 구독에서 Log Analytics 작업 영역으로 Azure 활동 로그 수집](activity-log-collect-tenants.md)을 참조 하세요.

> [!IMPORTANT]
> OperationalInsights 및 Microsoft.operationsmanagement 리소스 공급자가 구독에 등록 되지 않은 경우 다음 절차에 따라 오류가 발생할 수 있습니다. 이러한 공급자를 등록 하려면 [Azure 리소스 공급자 및 형식](../../azure-resource-manager/management/resource-providers-and-types.md) 을 참조 하세요.

활동 로그를 Log Analytics 작업 영역에 연결 하려면 다음 절차를 따르십시오.

1. Azure Portal **Log Analytics 작업 영역** 메뉴에서 작업 영역을 선택 하 여 활동 로그를 수집 합니다.
1. 작업 영역 메뉴의 **작업 영역 데이터 원본** 섹션에서 **Azure 활동 로그**를 선택 합니다.
1. 연결 하려는 구독을 클릭 합니다.

    ![작업 영역](media/activity-log-export/workspaces.png)

1. **연결** 을 클릭 하 여 구독의 활동 로그를 선택한 작업 영역에 연결 합니다. 구독이 다른 작업 영역에 이미 연결 되어 있는 경우 먼저 **연결 끊기** 를 클릭 하 여 연결을 끊습니다.

    ![작업 영역 연결](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Log Analytics 작업 영역에서 분석
활동 로그를 Log Analytics 작업 영역에 연결 하면 [로그 쿼리](../log-query/log-query-overview.md)를 사용 하 여 검색할 수 있는 **azureactivity** 라는 테이블에 항목이 작업 영역에 기록 됩니다. 이 테이블의 구조는 [로그 항목의 범주](activity-log-view.md#categories-in-the-activity-log)에 따라 달라 집니다. 각 범주에 대 한 설명은 [Azure 활동 로그 이벤트 스키마](activity-log-schema.md) 를 참조 하세요.

## <a name="activity-logs-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션에는 Log Analytics 작업 영역에서 활동 로그 레코드를 분석 하기 위한 여러 로그 쿼리 및 뷰가 포함 되어 있습니다.

### <a name="install-the-solution"></a>솔루션 설치
**활동 로그 분석** 솔루션을 설치 하려면 [모니터링 솔루션 설치](../insights/solutions.md#install-a-monitoring-solution) 의 절차를 따르십시오. 추가 구성은 필요 하지 않습니다.

### <a name="use-the-solution"></a>솔루션 사용
모니터링 솔루션은 Azure Portal의 **모니터** 메뉴에서 액세스할 수 있습니다. **Insights** 섹션에서 **자세히** 를 선택 하 여 솔루션 타일이 있는 **개요** 페이지를 엽니다. **Azure 활동 로그** 타일에는 작업 영역의 **azureactivity** 레코드 수 수가 표시 됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭 하 여 **azure 활동 로그** 보기를 엽니다. 뷰에는 다음 표의 시각화 파트가 포함 되어 있습니다. 각 파트에는 지정 된 시간 범위에 대 한 해당 부분의 기준과 일치 하는 항목이 최대 10 개까지 나열 됩니다. 파트 아래쪽의 **모두 보기** 를 클릭 하 여 일치 하는 모든 레코드를 반환 하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)

| 시각화 요소 | Description |
| --- | --- |
| Azure 활동 로그 항목 | 선택한 날짜 범위에 대 한 상위 Azure 활동 로그 항목 레코드 합계의 가로 막대형 차트를 표시 하 고 상위 10 개 활동 호출자의 목록을 표시 합니다. 가로 막대형 차트를 클릭하면 `AzureActivity`에 대한 로그 검색이 실행됩니다. 호출자 항목을 클릭 하면 해당 항목에 대 한 모든 활동 로그 항목을 반환 하는 로그 검색이 실행 됩니다. |
| 상태별 활동 로그 | 선택한 날짜 범위에 대 한 Azure 활동 로그 상태와 상위 10 개 상태 레코드의 목록에 대 한 도넛형 차트를 표시 합니다. 차트를 클릭 하 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`에 대 한 로그 쿼리를 실행 합니다. 상태 항목을 클릭 하면 해당 상태 레코드에 대 한 모든 활동 로그 항목을 반환 하는 로그 검색이 실행 됩니다. |
| 리소스별 활동 로그 | 활동 로그를 포함 하는 총 리소스 수를 표시 하 고 각 리소스에 대 한 레코드 수를 포함 하는 상위 10 개 리소스를 나열 합니다. 전체 영역을 클릭하여 `AzureActivity | summarize AggregatedValue = count() by Resource`에 대해 로그 검색을 실행하면 솔루션에 사용할 수 있는 모든 Azure 리소스가 표시됩니다. 리소스를 클릭 하 여 해당 리소스에 대 한 모든 활동 레코드를 반환 하는 로그 쿼리를 실행 합니다. |
| 리소스 공급자별 활동 로그 | 활동 로그를 생성 하는 리소스 공급자의 총 수를 표시 하 고 상위 10 개를 나열 합니다. 전체 영역을 클릭 하 여 모든 Azure 리소스 공급자를 표시 하는 `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`에 대 한 로그 쿼리를 실행 합니다. 리소스 공급자를 클릭 하 여 공급자에 대 한 모든 활동 레코드를 반환 하는 로그 쿼리를 실행 합니다. |

## <a name="next-steps"></a>다음 단계

- [활동 로그](platform-logs-overview.md)에 대해 자세히 알아보세요.
- [Azure Monitor data platform](data-platform.md)에 대해 자세히 알아보세요.
- [로그 쿼리](../log-query/log-query-overview.md) 를 사용 하 여 활동 로그에서 자세한 정보를 볼 수 있습니다.
