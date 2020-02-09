---
title: Azure Log Analytics를 사용 하 여 WAF 로그 검사
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure Log Analytics를 사용 하 여 Application Gateway 웹 응용 프로그램 방화벽 로그를 검사 하는 방법을 보여 줍니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048119"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Log Analytics를 사용하여 Application Gateway 웹 애플리케이션 방화벽 로그 검사

Application Gateway WAF가 작동 하 고 나면 로그를 사용 하 여 각 요청에서 발생 하는 상황을 검사할 수 있습니다. 방화벽 로그는 WAF가 평가, 일치 및 차단 하는 항목에 대한 통찰력을 제공 합니다. Log Analytics를 사용 하면 방화벽 로그 내의 데이터를 검사 하 여 훨씬 더 많은 정보를 제공할 수 있습니다. Log Analytics 작업 영역을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요. 로그 쿼리에 대한 자세한 내용은 Azure Monitor의 [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

## <a name="import-waf-logs"></a>WAF 로그 가져오기

Log Analytics로 방화벽 로그를 가져오려면 [백 엔드 상태, 진단 로그 및 Application Gateway에 대한 메트릭](application-gateway-diagnostics.md#diagnostic-logging)을 참조 하세요. Log Analytics 작업 영역에 방화벽 로그가 있으면 데이터를 보고, 쿼리를 작성 하 고, 시각화를 만들어 포털 대시보드에 추가할 수 있습니다.

## <a name="explore-data-with-examples"></a>예제를 사용 하 여 데이터 탐색

방화벽 로그에서 원시 데이터를 보려면 다음 쿼리를 실행 하면 됩니다.

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

다음 쿼리와 유사 하 게 표시 됩니다.

![Log Analytics 쿼리](media/log-analytics/log-query.png)

데이터를 드릴 다운 하 고 그래프를 그리거나 여기에서 시각화를 만들 수 있습니다. 시작 지점으로 다음 쿼리를 참조 하세요.

### <a name="matchedblocked-requests-by-ip"></a>IP 별 일치/차단 된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI 별 일치/차단 된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>상위 일치 규칙

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>상위 5 개 일치 규칙 그룹

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>대시보드에 추가

쿼리를 만든 후 대시보드에 추가할 수 있습니다.  Log analytics 작업 영역의 오른쪽 위에서 **대시보드에 고정을** 선택 합니다. 예제 대시보드에 고정 된 이전 4 개의 쿼리를 사용 하 여이 데이터를 한눈에 볼 수 있습니다.

![대시보드](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>다음 단계

[Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)
