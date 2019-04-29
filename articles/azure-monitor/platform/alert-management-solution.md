---
title: Azure Log Analytics의 경고 관리 솔루션 | Microsoft Docs
description: Log Analytics의 경고 관리 솔루션을 사용하여 환경의 모든 경고를 분석할 수 있습니다.  Log Analytics 내에서 생성된 경고를 통합하는 것 외에도, 연결된 System Center Operations Manager 관리 그룹에서 Log Analytics로 경고를 가져옵니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 06532369efb802606eb13a4b38a8579a3528f999
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777035"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Azure Log Analytics의 경고 관리 솔루션

![경고 관리 아이콘](media/alert-management-solution/icon.png)

> [!NOTE]
>  이제 Azure Monitor는 [SCOM, Zabbix 또는 Nagios와 같은 모니터링 도구](https://aka.ms/managing-alerts-other-monitoring-services)에서 생성된 경고를 포함한 [경고를 대규모로 관리](https://aka.ms/azure-alerts-overview)하는 개선된 기능을 지원합니다.
>  


경고 관리 솔루션을 통해 Log Analytics 리포지토리에서 모든 경고를 분석할 수 있습니다.  [Log Analytics에서 만든](../../azure-monitor/platform/alerts-overview.md) 또는 [Nagios 또는 Zabbix에서 가져온](../../azure-monitor/learn/quick-collect-linux-computer.md) 원본을 포함하여 다양한 원본에서 이러한 경고가 발생할 수 있습니다. 또한 솔루션은 [연결된 System Center Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md)에서도 경고를 가져옵니다.

## <a name="prerequisites"></a>필수 조건
솔루션은 Log Analytics 리포지토리에서 **경고** 유형의 모든 레코드를 사용하므로 이러한 레코드를 수집하는 데 필요한 구성을 모두 수행해야 합니다.

- Log Analytics 경고의 경우 [경고 규칙을 만들어서](../../azure-monitor/platform/alerts-overview.md) 리포지토리에서 직접 경고 레코드를 만듭니다.
- Nagios 및 Zabbix 경고의 경우 [해당 서버를 구성하여](../../azure-monitor/learn/quick-collect-linux-computer.md) Log Analytics에 경고를 보냅니다.
- System Center Operations Manager 경고의 경우 [Operations Manager 관리 그룹을 Log Analytics 작업 영역에 연결](../../azure-monitor/platform/om-agents.md)합니다.  그러면 System Center Operations Manager에서 생성된 모든 경고를 Log Analytics로 가져옵니다.  

## <a name="configuration"></a>구성
[솔루션 추가](../../azure-monitor/insights/solutions.md)에서 설명하는 프로세스를 사용하여 경고 관리 솔루션을 Log Analytics 작업 영역에 추가합니다. 추가 구성은 필요 없습니다.

## <a name="management-packs"></a>관리 팩
System Center Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결된 경우, 이 솔루션을 추가하면 System Center Operations Manager에 다음 관리 팩이 설치됩니다.  관리 팩 구성 또는 유지 관리는 필요 없습니다.

* Microsoft System Center Advisor 경고 관리(Microsoft.IntelligencePacks.AlertManagement)

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../../azure-monitor/platform/om-agents.md)을 참조하세요.

## <a name="data-collection"></a>데이터 수집
### <a name="agents"></a>에이전트
다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원 | 설명 |
|:--- |:--- |:--- |
| [Windows 에이전트](agent-windows.md) | 아닙니다. |직접 Windows 에이전트는 경고를 생성하지 않습니다.  이벤트에서 Log Analytics 경고를 만들고 Windows 에이전트에서 성능 데이터를 수집할 수 있습니다. |
| [Linux 에이전트](../../azure-monitor/learn/quick-collect-linux-computer.md) | 아닙니다. |직접 Linux 에이전트는 경고를 생성하지 않습니다.  이벤트에서 Log Analytics 경고를 만들고 Linux 에이전트에서 성능 데이터를 수집할 수 있습니다.  Linux 에이전트가 필요한 해당 서버에서 Nagios 및 Zabbix 경고를 수집합니다. |
| [System Center Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md) |예 |Operations Manager 에이전트에 대해 생성된 경고는 관리 그룹에 전달된 다음 Log Analytics에 전달됩니다.<br><br>Operations Manager 에이전트에서 Log Analytics로 직접 연결은 필요하지 않습니다. 경고 데이터는 관리 그룹에서 Log Analytics 리포지토리로 전달됩니다. |


### <a name="collection-frequency"></a>수집 빈도
- 리포지토리에 저장되는 즉시 경고 레코드가 솔루션에 제공됩니다.
- 경고 데이터는 3분마다 Operations Manager 관리 그룹에서 Log Analytics로 전송됩니다.  

## <a name="using-the-solution"></a>솔루션 사용
경고 관리 솔루션을 Log Analytics 작업 영역에 추가하면 대시보드에 **경고 관리** 타일이 추가됩니다.  이 타일은 마지막 24 시간 이내에 생성된 현재 활성 경고 수에 대한 카운트와 그래픽 표현을 표시합니다.  이 시간 범위를 변경할 수 없습니다.

![경고 관리 타일](media/alert-management-solution/tile.png)

**경고 관리** 타일을 클릭하여 **경고 관리** 대시보드를 엽니다.  대시보드는 다음 표의 열을 포함하고 있습니다.  각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 10개의 경고를 나열합니다.  열 아래쪽의 **모두 보기** 를 클릭하거나 열 제목을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|:--- |:--- |
| 중요한 알림 |경고 이름별로 그룹화된 중요 심각도를 가진 모든 경고.  경고 이름을 클릭하여 해당 경고에 대한 모든 레코드를 반환하는 로그 검색을 실행합니다. |
| 경고 알림 |경고 이름별로 그룹화된 경고의 심각도를 가진 모든 경고입니다.  경고 이름을 클릭하여 해당 경고에 대한 모든 레코드를 반환하는 로그 검색을 실행합니다. |
| 활성 SCOM 경고 |경고를 생성한 원본별로 그룹화된 *닫힘* 이외의 상태인 Operations Manager에서 수집된 모든 경고입니다. |
| 모든 활성 경고 |경고 이름별로 그룹화된 심각도를 가진 모든 경고입니다. *닫힘*이외의 상태를 가진 Operations Manager 경고만 포함합니다. |

오른쪽으로 스크롤하면 대시보드는 일반 쿼리 여러 개를 나열하며, 이 쿼리를 클릭하면 경고 데이터에 대한 [로그 검색](../../azure-monitor/log-query/log-query-overview.md) 을 수행할 수 있습니다.

![경고 관리 대시보드](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics 레코드
경고 관리 솔루션은 **경고**유형을 가진 모든 레코드를 분석합니다.  Log Analytics에서 생성되거나 Nagios 또는 Zabbix에서 수집되는 경고를 솔루션에서 직접 수집하지 않습니다.

솔루션은 System Center Operations Manager에서 경고를 가져오며 유형이 **경고**이고 SourceSystem이 **OpsManager**인 각 경고에 대해 해당 레코드를 만듭니다.  이러한 레코드는 다음 표의 속성을 가집니다.  

| 자산 | 설명 |
|:--- |:--- |
| Type |*경고* |
| SourceSystem |*OpsManager* |
| AlertContext |경고가 XML 형식으로 생성되게 하는 데이터 항목에 대한 세부 정보입니다. |
| AlertDescription |경고에 대한 자세한 설명입니다. |
| AlertId |경고의 GUID입니다. |
| AlertName |경고의 이름입니다. |
| AlertPriority |경고의 우선 순위 수준입니다. |
| AlertSeverity |경고의 심각도 수준입니다. |
| AlertState |경고의 최신 해결 상태입니다. |
| LastModifiedBy |경고를 마지막으로 수정한 사용자의 이름입니다. |
| ManagementGroupName |경고를 생성한 관리 그룹의 이름입니다. |
| RepeatCount |확인된 후 같은 모니터링 대상 개체에 대해 같은 경고가 생성된 횟수입니다. |
| ResolvedBy |경고를 확인한 사용자의 이름입니다. 경고가 아직 확인되지 않은 경우 비어 있습니다. |
| SourceDisplayName |경고를 생성한 모니터링 개체의 표시 이름입니다. |
| SourceFullName |경고를 생성한 모니터링 개체의 전체 이름입니다. |
| TicketId |경고에 대해 티켓을 할당하기 위해 System Center Operations Manager 환경이 프로세스와 통합되는 경우 경고에 대한 티켓 ID입니다.  비어 있으면 티켓 ID가 할당되지 않습니다. |
| TimeGenerated |경고가 만들어진 날짜 및 시간입니다. |
| TimeLastModified |경고가 마지막 변경된 날짜 및 시간입니다. |
| TimeRaised |경고가 생성된 날짜 및 시간입니다. |
| TimeResolved |경고가 확인된 날짜 및 시간입니다. 경고가 아직 확인되지 않은 경우 비어 있습니다. |

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 표는 이 솔루션에 의해 수집된 경고 레코드에 대한 샘플 로그 검색을 제공합니다. 

| 쿼리 | 설명 |
|:---|:---|
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) |지난 24 시간 동안 발생한 중요한 알림 |
| Alert &#124; where AlertSeverity == "warning" and TimeRaised > ago(24h) |지난 24 시간 동안 발생한 중요한 경고 |
| Alert &#124; where SourceSystem == "OpsManager" and AlertState != "Closed" and TimeRaised > ago(24h) &#124; summarize Count = count() by SourceDisplayName |지난 24 시간 동안 발생한 활성 경고를 가진 소스 |
| Alert &#124; where SourceSystem == "OpsManager" and AlertSeverity == "error" and TimeRaised > ago(24h) and AlertState != "Closed" |지난 24시간 동안 발생했고 여전히 활성화되어 있는 중요 경고 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(24h) and AlertState == "Closed" |지난 24시간 동안 발생했고 지금은 해결된 경고 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; summarize Count = count() by AlertSeverity |지난 1 일 동안 발생했고 심각도별로 그룹화된 경고 |
| Alert &#124; where SourceSystem == "OpsManager" and TimeRaised > ago(1d) &#124; sort by RepeatCount desc |지난 1 일 동안 발생했고 반복 횟수 값별로 정렬된 경고 |



## <a name="next-steps"></a>다음 단계
* Log Analytics에서 경고 생성에 대한 자세한 내용은 [Log Analytics의 경고](../../azure-monitor/platform/alerts-overview.md) 에 관하여 알아보세요.
