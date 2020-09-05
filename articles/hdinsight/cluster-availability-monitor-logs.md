---
title: HDInsight에서 Azure Monitor 로그를 사용 하 여 클러스터 가용성을 모니터링 하는 방법
description: Azure Monitor 로그를 사용 하 여 클러스터 상태 및 가용성을 모니터링 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 19e3f1a157ee2c042dfebfc96c9b51c3c4698ebc
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163733"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>HDInsight에서 Azure Monitor 로그를 사용 하 여 클러스터 가용성을 모니터링 하는 방법

HDInsight 클러스터에는 쿼리 가능한 메트릭 및 로그 뿐만 아니라 구성 가능한 경고를 제공 하는 Azure Monitor 로그 통합이 포함 되어 있습니다. 이 문서에서는 Azure Monitor를 사용 하 여 클러스터를 모니터링 하는 방법을 보여 줍니다.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 로그 통합

Azure Monitor 로그를 사용 하면 HDInsight 클러스터와 같은 여러 리소스에 의해 생성 된 데이터를 한 곳에서 수집 하 고 집계 하 여 통합 모니터링 환경을 구현할 수 있습니다.

필수 구성 요소로, 수집 된 데이터를 저장 하려면 Log Analytics 작업 영역이 필요 합니다. 아직 만들지 않은 경우 다음 지침 [에 따라 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)를 수행할 수 있습니다.

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor 로그 통합 사용

포털의 HDInsight 클러스터 리소스 페이지에서 **Azure Monitor**를 선택 합니다. 그런 다음 **사용** 을 선택 하 고 드롭다운에서 Log Analytics 작업 영역을 선택 합니다.

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

기본적으로에 지 노드를 제외 하 고 모든 클러스터 노드에 OMS 에이전트를 설치 합니다. OMS 에이전트는 클러스터에 지 노드에 설치 되어 있지 않으므로 기본적으로 Log Analytics에 있는에 지 노드에 대 한 원격 분석은 없습니다.

## <a name="query-metrics-and-logs-tables"></a>메트릭 및 로그 테이블 쿼리

Azure Monitor 로그 통합이 사용 하도록 설정 되 면 (몇 분 정도 걸릴 수 있음) **Log Analytics 작업 영역** 리소스로 이동 하 여 **로그**를 선택 합니다.

![작업 영역 로그 Log Analytics](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

로그는 다음과 같은 다양 한 샘플 쿼리를 나열 합니다.

| 쿼리 이름                      | Description                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 현재 사용 가능한 컴퓨터    | 매 시간 마다 로그를 전송 하는 컴퓨터 수를 차트로 표시 합니다.                     |
| 하트 비트 나열                 | 지난 1 시간 동안 모든 컴퓨터 하트 비트 나열                           |
| 각 컴퓨터의 마지막 하트 비트 | 각 컴퓨터에서 보낸 마지막 하트 비트 표시                             |
| 사용할 수 없는 컴퓨터           | 지난 5 시간 동안 하트 비트를 보내지 않은 모든 알려진 컴퓨터를 나열 합니다. |
| 가용성 률               | 연결 된 각 컴퓨터의 가용성 률 계산                |

예를 들어 위의 스크린샷에 표시 된 것 처럼 해당 쿼리에서 **실행** 을 선택 하 여 **Availability rate** 샘플 쿼리를 실행 합니다. 이렇게 하면 클러스터의 각 노드에 대 한 가용성 비율이 백분율로 표시 됩니다. 동일한 Log Analytics 작업 영역에 메트릭을 전송 하기 위해 여러 HDInsight 클러스터를 사용 하도록 설정한 경우 표시 되는 클러스터에서 모든 노드 (에 지 노드 제외)의 가용성 요금이 표시 됩니다.

![작업 영역 로그의 ' availability rate ' 샘플 쿼리 Log Analytics](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> 가용성 속도는 24 시간 동안 측정 되므로 정확한 가용성 요금은 표시 되기까지 최소 24 시간 동안 클러스터를 실행 해야 합니다.

오른쪽 위 모서리에서 **고정** 을 클릭 하 여이 테이블을 공유 대시보드에 고정할 수 있습니다. 쓰기 가능한 공유 대시보드가 없으면 [Azure Portal에서 대시보드를 만들고 공유](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)하는 방법을 확인할 수 있습니다.

## <a name="azure-monitor-alerts"></a>Azure Monitor 경고

메트릭 값 또는 쿼리 결과가 특정 조건에 맞을 때 트리거되는 Azure Monitor 경고를 설정할 수도 있습니다. 예를 들어 하나 이상의 노드가 5 시간 안에 하트 비트를 보내지 않은 경우 (즉, 사용할 수 없는 것으로 간주 됨) 전자 메일을 보내는 경고를 만들어 보겠습니다.

**로그**에서 아래와 같이 해당 쿼리에서 **실행** 을 선택 하 여 **사용할 수 없는 computers** 샘플 쿼리를 실행 합니다.

![작업 영역 로그의 ' 사용할 수 없는 컴퓨터 ' 샘플 Log Analytics](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

모든 노드를 사용할 수 있는 경우이 쿼리는 지금은 0 개의 결과를 반환 해야 합니다. **새 경고 규칙** 을 클릭 하 여이 쿼리에 대 한 경고 구성을 시작 합니다.

![Log Analytics 작업 영역 새 경고 규칙](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

경고에는 규칙을 만들 *리소스* (이 경우 Log Analytics 작업 영역), 경고를 트리거하는 *조건* 및 경고가 트리거될 때 발생 하는 동작을 결정 하는 *동작 그룹* 의 세 가지 구성 요소가 있습니다.
아래와 같이 **조건 제목을**클릭 하 여 신호 논리 구성을 마칩니다.

![포털 경고 규칙 만들기 조건](media/cluster-availability-monitor-logs/portal-condition-title.png)

그러면 **신호 논리 구성**이 열립니다.

**경고 논리** 섹션을 다음과 같이 설정 합니다.

*기준: 결과 수, 조건: 보다 큼, 임계값: 0.*

이 쿼리는 사용할 수 없는 노드만 결과로 반환 하므로 결과 수가 0 보다 크면 경고가 발생 합니다.

**평가 기준** 섹션에서 사용할 수 없는 노드를 확인 하려는 빈도에 따라 **기간** 및 **빈도** 를 설정 합니다.

이 경고의 목적을 위해 **Period = Frequency** 를 확인 하는 것이 좋습니다. 기간, 빈도 및 기타 경고 매개 변수에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)에서 찾을 수 있습니다.

신호 논리 구성이 완료 되 면 **완료** 를 선택 합니다.

![경고 규칙이 신호 논리를 구성 합니다.](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

기존 작업 그룹이 아직 없는 경우 **작업 그룹** 섹션에서 **새로 만들기** 를 클릭 합니다.

![경고 규칙이 새 작업 그룹을 만듭니다.](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

그러면 **작업 그룹 추가**가 열립니다. **작업 그룹 이름**, **약식 이름**, **구독**및 **리소스 그룹을 선택 합니다.** **작업 섹션에서** 작업 **이름을** 선택 하 고 **전자 메일/SMS/푸시/음성** 을 **작업 유형으로 선택 합니다.**

> [!NOTE]
> Azure Function, LogicApp, Webhook, ITSM 및 Automation Runbook과 같은 전자 메일/a p i/푸시/음성 외에 경고에서 트리거할 수 있는 다른 작업은 여러 가지가 있습니다. [더 알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

그러면 **이메일/SMS/푸시/음성이**열립니다. 받는 사람에 대 한 **이름을** 선택 하 고 **전자 메일** 상자 **를 선택 하** 고 경고를 보낼 전자 메일 주소를 입력 합니다. **이메일/SMS/푸시/음성**에서 **확인** 을 선택한 다음 **작업 그룹 추가** 에서 작업 그룹 구성을 완료 합니다.

![경고 규칙은 추가 작업 그룹을 만듭니다.](media/cluster-availability-monitor-logs/portal-add-action-group.png)

이러한 블레이드가 닫힌 **후 작업 그룹 섹션에** 나열 된 작업 그룹을 확인 해야 합니다. 마지막으로 **경고 규칙 이름과** **설명을** 입력 하 고 **심각도**를 선택 하 여 **경고 세부 정보** 섹션을 완료 합니다. **경고 규칙 만들기** 를 클릭 하 여 완료 합니다.

![포털에서 경고 규칙 만들기 완료](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> **심각도** 를 지정 하는 기능은 여러 경고를 만들 때 사용할 수 있는 강력한 도구입니다. 예를 들어 단일 헤드 노드가 다운 되는 경우 경고를 생성 하는 경고 (심각도 1)를 생성 하 고, 두 헤드 노드가 모두 다운 될 가능성이 없는 이벤트에서 위험 (심각도 0)을 발생 시키는 다른 경고를 만들 수 있습니다.

이 경고에 대 한 조건이 충족 되 면 경고가 발생 하 고 다음과 같은 경고 세부 정보가 포함 된 전자 메일을 받게 됩니다.

![Azure Monitor 경고 전자 메일 예제](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

**Log Analytics 작업 영역**에서 **경고** 로 이동 하 여 발생 한 모든 경고를 심각도 별로 그룹화 하 여 볼 수도 있습니다.

![Log Analytics 작업 영역 경고](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

심각도 그룹화 (위에서 강조 표시 된 대로 **심각도 1** )를 선택 하면 아래와 같이 발생 한 심각도의 모든 경고에 대 한 레코드가 표시 됩니다.

![Log Analytics 작업 영역 심각도 1 개 경고](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>다음 단계

* [클러스터 가용성 - Apache Ambari](./hdinsight-cluster-availability.md)
* [Azure Monitor 로그 사용](hdinsight-hadoop-oms-log-analytics-tutorial.md)
